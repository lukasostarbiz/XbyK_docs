---
source: https://docs.kentico.com/guides/development/customizations-and-integrations/import-objects-with-the-api
scrape_date: 2026-01-26
---

  * [Home](/guides)
  * [Development](/guides/development)
  * [Customizations and integrations](/guides/development/customizations-and-integrations)
  * Import objects in bulk using the API 


# Import objects in bulk using the API
In Xperience by Kentico, many of the entities you interact with are represented by _info objects_ in code, such as `UserInfo`, `ChannelInfo`, and `ActivityInfo`.
Xperience provides APIs for working with these objects, in most cases by resolving an `IInfoProvider<TInfo>`. Using these APIs, you can retrieve and manipulate info objects in code.
This comes in handy when integrating Xperience with external systems, for example, when initially importing a large number of objects from an external system, or keeping Xperience in sync with changes to a [decoupled system](/documentation/developers-and-admins/integrate-with-decoupled-systems).
This example will focus on a bulk-import scenario, which can apply both to “lift and shift” migrations of external functionality into Xperience, and to the start of ongoing integrations. We’ll import contacts from an imagined third-party marketing system called _Contactish_.
The code samples in this sample will mostly exist in the **~/Features/ContactImport** folder of the **TrainingGuides.Web** project and its subdirectories. Files located elsewhere will start with the **~** character, representing the root of the **TrainingGuides.Web** project.
## Before you start
This guide requires the following:
  * Familiarity with [C#](https://learn.microsoft.com/en-us/dotnet/csharp/), [.NET Core](https://learn.microsoft.com/en-us/dotnet/), [Dependency injection](https://learn.microsoft.com/en-us/dotnet/core/extensions/dependency-injection), and the [MVC pattern](https://learn.microsoft.com/en-us/aspnet/core/mvc/overview).
  * A running instance of Xperience by Kentico, preferably [30.11.1](/documentation/changelog) or higher. 
Some features covered in the training guides may not work in older versions. 


**Code samples**
You can find a project with completed, working versions of code samples from this guide and others in the [finished branch](https://github.com/Kentico/xperience-by-kentico-training-guides/tree/finished) of the _Training guides_ repository.
The [main branch](https://github.com/Kentico/xperience-by-kentico-training-guides) of the repository provides a starting point to code along with the guides.
The code samples in this guide are for [.NET 8](https://learn.microsoft.com/en-us/dotnet/core/whats-new/dotnet-8/overview) only.
They come from a project that uses [implicit using directives](https://learn.microsoft.com/en-us/dotnet/core/project-sdk/overview#implicit-using-directives). You may need to add additional `using` directives to your code if your project does not use this feature.
## Prepare the data you want to import
To begin the import scenario, access your external system and find some way to export the data you want to import into info objects in Xperience. Depending on the system and the manner in which you package the data, you may be able to control which fields to include, or even aggregate data.
For our example, let’s say that Contactish allows you to export batches of contacts in XML format, with the ability to define aggregate fields during export, such as `SegmentIdentifiers`, which lists the IDs of segments to which a contact relates.
**File format**
This same approach can work for any file format, such as CSV or JSON.
We chose XML for this example because we are able to work with it in .NET without installing additional NuGet packages.
Create a dedicated folder for files you want to import, such as **~/App_Data/TrainingGuidesContactImport/Contactish**.
For the sake of example, we’ll use [just one file](https://github.com/Kentico/xperience-by-kentico-training-guides/blob/finished/src/TrainingGuides.Web/App_Data/TrainingGuidesContactImport/Contactish/ContactishContactsExport.xml), though in some high-volume cases, you may find data spread across multiple files.
## Extend the contact class
In cases where you import objects from external systems, you’ll likely want to store some attributes from those systems in the Xperience objects. In these cases, you can [extend the system object type](/documentation/developers-and-admins/customization/object-types/extend-system-object-types) if applicable, or create a [custom object type](/documentation/developers-and-admins/customization/object-types/object-type-configuration/model-object-type-relationships#one-to-many-relationships) that relates to the built-in type and contains additional fields.
For our example, the Contact class is extensible, so we can add custom fields from our Contactish system. Add the following fields to the Contact class under **Modules → Contact management → Classes → Contact management - Contact → Database columns** :
  * _Contactish identifier_ - the identifier of the contact in Contactish 
    * **Field name:** TrainingGuidesContactishIdentifier
    * **Data type:** Text
    * **Size:** 50
    * **Required:** False (disabled)
  * _Contactish segment identifiers_ - the identifiers of segments applied to the contact in Contactish 
    * **Field name:** TrainingGuidesContactishSegmentIdentifiers
    * **Data type:** Text
    * **Size:** 500
    * **Required:** False (disabled)
  * _Contactish last synced_ - the most recent date and time when the contact’s data was updated from Contactish 
    * **Field name:** TrainingGuidesContactishLastSynced
    * **Data type:** Date and time
    * **Precision:** 7
    * **Required:** False (disabled)
  * _Contactish last updated_ - the date and time the contact was last updated in Contactish at the time of synchronizing 
    * **Field name:** TrainingGuidesContactishLastUpdated
    * **Data type:** Date and time
    * **Precision:** 7
    * **Required:** False (disabled)


You can find a more detailed exploration of extending the **Contact** class in our [custom contact field materials](/guides/development/customizations-and-integrations/add-custom-contact-field).
## Create an import service
Now that we’ve extended our object type to include additional fields, we can move on to code.
For the sake of simplicity, we’ll work with the Xperience API directly in the service. In real-world scenarios, we recommend using some form of abstraction, such as the repository pattern, for testability.
Let’s create a service for importing our objects. Start by creating a service interface with an `ImportContactsFromXml` method, then implement it using the `System.Xml` library to parse values from the source file. Apply these values to new `ContactInfo` objects, including the new custom fields we defined earlier.
Use the `BulkInsert` method from `IInfoProvider<ContactInfo>` to add the collection of contacts to the database in a single operation:
C#
**./Services/IContactImportService.cs**
Copy
```
using System.Xml;

namespace TrainingGuides.Web.Features.ContactImport;

public interface IContactImportService
{
    /// <summary>
    /// Imports contacts from the provided XML document.
    /// </summary>
    /// <param name="document">Xml document containing contacts</param>
    /// <returns>The number of contacts inserted</returns>
    int ImportContactsFromXml(XmlDocument document);
}
```

C#
**./Services/ContactImportService.cs**
Copy
```
using System.Xml;
using CMS.ContactManagement;
using CMS.DataEngine;

namespace TrainingGuides.Web.Features.ContactImport;

public class ContactishContactImportService(IInfoProvider<ContactInfo> contactInfoProvider) : IContactImportService
{
    /// <inheritdoc/>
    public int ImportContactsFromXml(XmlDocument document)
    {
        var contactInfos = GetContactsFromXml(document);

        // Note that object event handlers, contact group recalculation, etc. will not run for individual contacts added with BulkInsert
        // We need to manually invoke recalculation later in the process
        contactInfoProvider.BulkInsert(contactInfos);

        return contactInfos.Count();
    }

    /// <summary>
    /// Parses the provided XML document and returns a list of <see cref="ContactInfo"/> objects based on the data in the document.
    /// </summary>
    /// <param name="document">XML document containing serialized contact data from Contactish</param>
    /// <returns>Collection of ContactInfo objects parsed from the provided XML</returns>
    public IEnumerable<ContactInfo> GetContactsFromXml(XmlDocument document)
    {
        List<ContactInfo> contactInfos = [];

        var contacts = document[ContactishValues.CONTACTS_ELEMENT];

        contacts?.ChildNodes
            .OfType<XmlElement>()
            .Where(x => x.Name == ContactishValues.CONTACT_ELEMENT)
            .ToList()
            .ForEach(contactElement =>
            {
                var contact = new ContactInfo()
                {
                    ContactFirstName = contactElement[ContactishValues.FIRST_NAME_ELEMENT]?.InnerText ?? string.Empty,
                    ContactLastName = contactElement[ContactishValues.LAST_NAME_ELEMENT]?.InnerText ?? string.Empty,
                    ContactEmail = contactElement[ContactishValues.EMAIL_ELEMENT]?.InnerText ?? string.Empty,
                    ContactCreated = DateTime.Now,
                    ContactGUID = Guid.NewGuid(),
                    ContactLastModified = DateTime.Now,
                };

                var lastUpdated = DateTime.TryParse(contactElement[ContactishValues.LAST_UPDATED_ELEMENT]?.InnerText, out var parsedDate)
                    ? parsedDate
                    : DateTime.MinValue;

                contact.SetValue(ContactishValues.IDENTIFIER_FIELD, contactElement[ContactishValues.IDENTIFIER_ELEMENT]?.InnerText ?? string.Empty);
                contact.SetValue(ContactishValues.SEGMENT_IDENTIFIERS_FIELD, contactElement[ContactishValues.SEGMENT_IDENTIFIERS_ELEMENT]?.InnerText ?? string.Empty);
                contact.SetValue(ContactishValues.LAST_UPDATED_FIELD, lastUpdated);
                contact.SetValue(ContactishValues.LAST_SYNCED_FIELD, DateTime.Now);

                if (!string.IsNullOrWhiteSpace(contact.ContactEmail) && !string.IsNullOrWhiteSpace(contact.GetValue(ContactishValues.IDENTIFIER_FIELD)?.ToString()))
                    contactInfos.Add(contact);
            });

        return contactInfos;
    }
}
```

C#
**./ContactishValues.cs**
Copy
```
namespace TrainingGuides.Web.Features.ContactImport;

internal static class ContactishValues
{
    // Names of elements found in XML file
    internal const string CONTACTS_ELEMENT = "Contacts";
    internal const string CONTACT_ELEMENT = "Contact";
    internal const string FIRST_NAME_ELEMENT = "FirstName";
    internal const string LAST_NAME_ELEMENT = "LastName";
    internal const string EMAIL_ELEMENT = "Email";
    internal const string IDENTIFIER_ELEMENT = "Identifier";
    internal const string SEGMENT_IDENTIFIERS_ELEMENT = "SegmentIdentifiers";
    internal const string LAST_UPDATED_ELEMENT = "LastUpdated";

    // Names of custom fields in ContactInfo class
    internal const string IDENTIFIER_FIELD = "TrainingGuidesContactishIdentifier";
    internal const string SEGMENT_IDENTIFIERS_FIELD = "TrainingGuidesContactishSegmentIdentifiers";
    internal const string LAST_SYNCED_FIELD = "TrainingGuidesContactishLastSynced";
    internal const string LAST_UPDATED_FIELD = "TrainingGuidesContactishLastUpdated";
}
```

**BulkInsert performance considerations**
`BulkInsert` uses a single query to insert many records, avoiding dozens of separate queries from calling `Insert` in a loop. This improves performance significantly, but does not trigger the same automatic functions as standard `Insert` functionality for individual contacts, such as [contact group evaluation](/documentation/business-users/digital-marketing/contact-groups#recalculate-contact-groups) and [object events](/documentation/developers-and-admins/customization/handle-global-events/handle-object-events).
Additionally, `BulkInsert` doesn’t give you a chance to check if an object already exists before deciding whether or not to insert it. [Contact merging](/documentation/business-users/digital-marketing/contact-management#contact-merging) should take care of duplicates in the case of `ContactInfo` objects, but we recommend avoiding this where possible. Only use `BulkInsert` for initial imports from an external system, or when your integration ensures only new entries are in the exported file.
## Define a scheduled task
To execute the import process, we’ll create a scheduled task.
While developers often associate scheduled tasks with recurring operations, they execute code on demand when you manually trigger them through the admin UI.
Our task should retrieve the file from the filesystem, create an XML document, and use our new service to insert contacts into the database:
C#
**./ScheduledTasks/ContactishContactImportScheduledTask.cs**
Copy
```
using System.Xml;
using CMS.Scheduler;
using TrainingGuides.Web.Features.Shared.Logging;

namespace TrainingGuides.Web.Features.ContactImport;

public class ContactishContactImportScheduledTask(
    ILogger<ContactishContactImportScheduledTask> logger,
    IContactImportService contactImportService,
    IWebHostEnvironment webHostEnvironment) : IScheduledTask
{
    public const string IDENTIFIER = "TrainingGuides.ContactishContactImportScheduledTask";
    private const string IMPORT_FOLDER_PATH = "App_Data\\TrainingGuidesContactImport\\Contactish";

    public async Task<ScheduledTaskExecutionResult> Execute(ScheduledTaskConfigurationInfo taskConfiguration, CancellationToken cancellationToken)
    {
        string directoryPath = System.IO.Path.Combine(webHostEnvironment.ContentRootPath, IMPORT_FOLDER_PATH);

        if (!Directory.Exists(directoryPath))
        {
            logger.LogError(EventIds.ImportPathNotFound,
                "Contact import directory does not exist: {DirectoryPath}",
                directoryPath);
            return await Task.FromResult(new ScheduledTaskExecutionResult("Failed - Directory does not exist"));
        }

        // Find all XML files in the directory
        var xmlFiles = Directory.EnumerateFiles(directoryPath, "*.xml", SearchOption.TopDirectoryOnly)
            .ToList();

        foreach (string xmlFile in xmlFiles)
        {
            var doc = new XmlDocument();

            using var fileStream = new FileStream(System.IO.Path.Combine(directoryPath, xmlFile), FileMode.Open);
            try
            {
                // Load the XML document from the file stream
                doc.Load(fileStream);
            }
            catch (XmlException ex)
            {
                logger.LogError(EventIds.ImportContactsFromFileError,
                    ex,
                    "Failed to load XML document from file: {XmlFile}",
                    xmlFile);
                return await Task.FromResult(new ScheduledTaskExecutionResult(ex.Message));
            }

            // Import contacts from the XML document
            try
            {
                int contactsImported = contactImportService.ImportContactsFromXml(doc);

                logger.LogInformation(EventIds.ImportContactsFromFileInfo,
                    "Successfully imported {ContactsImported} contacts from file: {XmlFile}",
                    contactsImported,
                    xmlFile);
            }
            catch (Exception ex)
            {
                logger.LogError(EventIds.ImportContactsFromFileError,
                    ex,
                    "An error occurred while importing contacts from file: {XmlFile}",
                    xmlFile);

                return await Task.FromResult(new ScheduledTaskExecutionResult(ex.Message));
            }
        }
        return await Task.FromResult(ScheduledTaskExecutionResult.Success);
    }
}
```

C#
**~/Features/Shared/Logging/EventIds.cs**
Copy
```
namespace TrainingGuides.Web.Features.Shared.Logging;

/// <summary>
/// Contains event IDs for logging.
/// </summary>
internal static class EventIds
{
    ...
    public static readonly EventId ImportPathNotFound = new(1004, nameof(ImportPathNotFound));
    public static readonly EventId ImportContactsFromFileInfo = new(1005, nameof(ImportContactsFromFileInfo));
    public static readonly EventId ImportContactsFromFileError = new(1006, nameof(ImportContactsFromFileError));
    ...
}
```

## Register and configure the functionality
### Register the service
We need to register our `IContactImportService` implementation for the dependency injection container to resolve it properly. Add the service as a singleton in **ServiceCollectionExtensions.cs** :
C#
**~/ServiceCollectionExtensions.cs**
Copy
```
...
using TrainingGuides.Web.Features.ContactImport;

namespace TrainingGuides.Web;

public static class ServiceCollectionExtensions
{
    public static void AddTrainingGuidesServices(this IServiceCollection services)
    {
        ...
        services.AddSingleton<IContactImportService, ContactishContactImportService>();
        ...
    }
    ...
}
 



 


```

### Register the scheduled task
To make sure Xperience can see our scheduled task, we need to use the `RegisterScheduledTask` assembly attribute. You can put it anywhere in a discoverable project, but for this example we’ll put it in the same file as the task:
C#
**./ScheduledTasks/ContactishContactImportScheduledTask.cs**
Copy
```
using System.Xml;
using CMS.Scheduler;
using TrainingGuides.Web.Features.ContactImport;
using TrainingGuides.Web.Features.Shared.Logging;

[assembly: RegisterScheduledTask(identifier: ContactishContactImportScheduledTask.IDENTIFIER, typeof(ContactishContactImportScheduledTask))]

namespace TrainingGuides.Web.Features.ContactImport;

public class ContactishContactImportScheduledTask(
...
 



 


```

### Configure the scheduled task
Navigate to the **Scheduled tasks** application in Xperience by Kentico and click **New scheduled task configuration**. Create the following configuration:
  * **Scheduled task configuration name:** Contactish contact import
  * **Task implementation:** TrainingGuides.ContactishContactImportScheduledTask
  * **Enabled:** True (enabled)
  * **Start time:** (Any time in the past)
  * **Period:** Once


Make sure to set the **Start time** to a day that has already passed, so the task will only execute when you manually trigger it from the list.
## Execute the contact import
In the **Scheduled tasks** application in Xperience by Kentico, find your configured task and click the **Run** button under **Actions** to import the contacts. (You may have to refresh the page to see the updated result.)
You can check for any errors in the **Event log** application and the task’s execution log. Once completed successfully, navigate to the **Contacts** application to verify that your contacts are present. You can check their values in the database, or the UI if you added them to an accessible UI form.
[![Screenshot of resulting imported contacts in the database](/docsassets/guides/import-objects-with-the-api/results-db.png)](/docsassets/guides/import-objects-with-the-api/results-db.png) [![Screenshot of resulting imported contacts in the UI](/docsassets/guides/import-objects-with-the-api/results-ui.png)](/docsassets/guides/import-objects-with-the-api/results-ui.png)
## Expand the scenario
Congratulations! You’ve successfully implemented a bulk import system for external contact data. This approach can be adapted for other info object types in Xperience by Kentico, such as users, custom objects, or other extensible system objects.
Consider the following enhancements to make this proof-of-concept production-ready:
  * **Error handling and logging** - Add comprehensive error handling and logging to track import failures and other issues.
  * **Data validation** - Implement validation rules to ensure data integrity before importing.
  * **Incremental imports** - Modify the logic to support incremental updates, importing only new or changed records.
  * **Performance monitoring** - Track import performance and optimize for larger datasets.
  * **Marking or deleting imported files** - Delete successfully imported files, or rename them with some kind of tag to keep from importing the same contacts multiple times.


## What’s next?
The [next guide in this series](/guides/development/customizations-and-integrations/add-imported-contacts-groups-recipients) expands upon the contact import scenario, adding the contacts to a dynamic contact group and transferring them to a recipient list.
If you have ideas you’d like us to cover in future guides, or if you’ve encountered any problems or issues in our existing materials, please click the **Send us feedback** button below to let us know.
![]()
[]()[]()
