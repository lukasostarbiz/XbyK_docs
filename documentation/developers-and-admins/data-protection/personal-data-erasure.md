---
source: https://docs.kentico.com/documentation/developers-and-admins/data-protection/personal-data-erasure
scrape_date: 2026-01-22
---

  * [Home](/documentation)
  * [Developers and admins](/documentation/developers-and-admins)
  * [Data protection](/documentation/developers-and-admins/data-protection)
  * Personal data erasure 


# Personal data erasure
To comply with the requirements of personal data regulations, such as the [GDPR](/documentation/developers-and-admins/data-protection/gdpr-compliance), you need to provide a way for administrators (data protection officers) to erase personal data stored within the system. This is necessary to fulfill the “right to be forgotten” of your project’s visitors and members, i.e. when resolving requests to delete personal data for specific data subjects.
By default, Xperience does not provide any functionality for erasing personal data. You need to implement the data erasure based on exact knowledge of your specific project, including the following factors:
  * How the project gathers, processes, and stores personal data
  * The nature of the legal requirements that you wish to fulfill
  * Other legal considerations that allow or require the project’s owner to hold personal data (for example commerce records for accounting purposes, etc.)


Depending on these requirements, your personal data erasure functionality may need to completely delete objects, clear values of individual object fields (columns), or perform anonymization of certain types of data while retaining others.
Use the following process to develop the personal data erasure:
**Prerequisite** : Before you start implementing personal data erasure, you need to prepare functionality that identifies and collects the given data. For detailed information, see [Personal data collection](/documentation/developers-and-admins/data-protection/personal-data-collection).
  1. Open your Xperience solution in Visual Studio.
  2. [Add a custom assembly](/documentation/developers-and-admins/customization/integrate-custom-code) (_Class Library_ project) with class discovery enabled to your solution or re-use an existing one.
     * The project will include code that extends the Xperience administration, so you need to install the **Kentico.Xperience.Admin** NuGet package.
     * To keep your custom assemblies clean, you can create a separate project for all code that requires the _Kentico.Xperience.Admin_ package (the erasure dialog model and UI page extender in this scenario).
  3. Create custom classes that perform the required delete actions – _Data erasers_. See the [Create data erasers](#create-data-erasers) section for details.
  4. Register your data eraser implementations [on application startup](/documentation/developers-and-admins/customization/run-code-on-application-startup). Place the registrations within a module’s `OnInit` method:
     * You can register any number of data erasers.
     * To register data erasers, call the `PersonalDataEraserRegister.Instance.Add` method (available in the `CMS.DataProtection` namespace). The system runs the delete actions defined within individual erasers according to the registration order (starting from the first data eraser to the last).
  5. Create and register an erasure dialog model to define the options that users can configure when deleting personal data. See [Define the eraser configuration](#define-the-eraser-configuration).


Users can now delete personal data on the **Right to be forgotten** tab of the **Data protection** application. The registered erasure dialog model defines the content of the configuration dialog where users choose which types of data are erased, and the data eraser implementations then perform the corresponding delete actions.
## Create data erasers
When deleting personal data in the **Data protection** application, users first submit real-world identifiers to find the appropriate data within the system. The registered _Identity collectors_ (see [Personal data collection](/documentation/developers-and-admins/data-protection/personal-data-collection)) convert these identifiers into Xperience objects, and _Data collectors_ return the related personal data. To allow users to delete the collected data, you need to create _Data erasers_.
Data erasers are classes that implement the `IPersonalDataEraser` interface (available in the `CMS.DataProtection` namespace). Every implementation must contain the `Erase` method, which processes the following parameters:
  * `IEnumerable<BaseInfo>` – provides the identity objects that were added by your `IIdentityCollector` implementations. You can convert the `BaseInfo` objects to specific types, such as `UserInfo` _,_ `ContactInfo`_,_ etc.
  * `IDictionary<string, object>` – a dictionary containing keys and values serialized from the erasure configuration dialog. See [Define the eraser configuration](#define-the-eraser-configuration).


The code of the `Erase` method depends on your requirements. Based on the method’s parameters, you can completely delete objects, assign empty values to individual object fields (columns), or perform more complex anonymization of data.
Use the [Provider API](/documentation/developers-and-admins/api/database-table-api) to delete objects.
Consider the parent-child relationships and other dependencies between objects. When you delete an object, all child objects and other objects that have a required dependency on the given object are also deleted. This may have an effect on the order in which you need to delete different types of objects within the Erase method, or even on the registration order of your `IPersonalDataEraser` implementations.
## Define the eraser configuration
The **Data protection** application provides an interface where users can choose which types of personal data they want to erase, or set other configuration options.
By default, the erasure dialog provides options with the following keys:
  * DeleteContacts
  * DeleteActivities
  * DeleteSubmittedFormsActivities
  * DeleteSubmittedFormsData


The keys can be used to access values from the erasure dialog via the dictionary parameter of the `Erase` method in [data erasers](#create-data-erasers). All of the default options have values of the `bool` type.
If you wish to change or extend the available configuration options, you need to create an erasure dialog model:
  1. Create a class that implements the `IDataErasureDialogModel` interface:
     * Add properties for all configuration options that you wish to have in the erasure dialog.
     * Define the configuration interface by assigning [editing components](/documentation/developers-and-admins/customization/extend-the-administration-interface/ui-form-components/editing-components) to the properties.
     * Implement the `Validate` method which indicates whether the dialog’s input is valid.
  2. Create and register a [page extender](/documentation/developers-and-admins/customization/extend-the-administration-interface/ui-pages/ui-page-extenders) class that extends the `RightToBeforgotten` UI page.
  3. Within the extender, modify the page’s configuration and assign the erasure dialog model to the `Page.PageConfiguration.DataErasureDialogModel` configuration property.


For an example, see the [Add the eraser configuration](#add-the-eraser-configuration) section below.
When deleting personal data on the **Right to be forgotten** tab in the _Data protection_ application, users can now configure options based on the assigned erasure dialog model.
## Example – Create a contact data eraser
The following example demonstrates how to implement a personal data eraser for [contacts](/documentation/business-users/digital-marketing/contact-management) and their logged [activities](/documentation/business-users/digital-marketing/contact-activities). The sample eraser extends the example from the [Personal data collection](/documentation/developers-and-admins/data-protection/personal-data-collection) page – the collectors find contact data based on an email address identifier, and the eraser allows users to delete the given contacts and/or their activities (on the **Right to be forgotten** tab of the **Data protection** application).
This example only shows the basic implementation concepts and deletes a very limited set of personal data. You can find more extensive code examples in the Dancing Goat sample site. [Install](/documentation/developers-and-admins/installation) the Dancing Goat project and view the files in the **DataProtectionSamples** folder.
Keep in mind that you always need to adjust your own implementation based on the personal data processing used by your project and the legal requirements that you wish to fulfill.
  * [Set up the custom project and data collectors](#set-up-the-custom-project-and-data-collectors)
  * [Create the data eraser](#create-the-data-eraser)
  * [Register the eraser](#register-the-eraser)
  * [Add the eraser configuration](#add-the-eraser-configuration)


### Set up the custom project and data collectors
Before you can start creating the eraser, you need to implement data collection functionality.
Recreate or reuse the entire custom project described in [Example - Contact data collectors](/documentation/developers-and-admins/data-protection/personal-data-collection#example-contact-data-collectors), including the _IIdentityCollector_ and _IPersonalDataCollector_ implementations, and the custom module class used for registration.
Install the **Kentico.Xperience.Admin** NuGet package into this project (it will include code that extends the Xperience administration).
### Create the data eraser
Add a new class implementing the `IPersonalDataEraser` interface under the custom project:
C#
Copy
```
using System.Collections.Generic;
using System.Linq;

using CMS.Activities;
using CMS.ContactManagement;
using CMS.DataEngine;
using CMS.DataProtection;
using CMS.Helpers;

public class ContactDataEraser : IPersonalDataEraser
{
    // Stores instances of services for managing activities and contacts
    private readonly IInfoProvider<ActivityInfo> activityInfoProvider;
    private readonly IInfoProvider<ContactInfo> contactInfoProvider;

    public ContactDataEraser(IInfoProvider<ActivityInfo> activityInfoProvider, IInfoProvider<ContactInfo> contactInfoProvider)
    {
        this.activityInfoProvider = activityInfoProvider;
        this.contactInfoProvider = contactInfoProvider;
    }

    public void Erase(IEnumerable<BaseInfo> identities, IDictionary<string, object> configuration)
    {
        // Gets all contact objects added by registered IIdentityCollector implementations
        var contacts = identities.OfType<ContactInfo>();

        // Does nothing if no contacts were collected
        if (!contacts.Any())
        {
            return;
        }

        // Gets a list of identifiers for the contacts
        List<int> contactIds = contacts.Select(c => c.ContactID).ToList();

        // Deletes the activities of the given contacts (if enabled in the configuration)
        DeleteActivities(contactIds, configuration);

        // Deletes the given contacts (if enabled in the configuration)
        // Also automatically deletes activities of the given contacts (contacts are parent objects of activities)
        DeleteContacts(contacts, configuration);
    }

    private void DeleteActivities(List<int> contactIds, IDictionary<string, object> configuration)
    {
        // Checks whether deletion of activities is enabled in the configuration options
        object deleteActivities;
        if (configuration.TryGetValue("DeleteActivities", out deleteActivities)
            && ValidationHelper.GetBoolean(deleteActivities, false))
        {
            // Deletes the activities of the specified contacts
            // The system may contain a very large number of activity records, so the example uses the BulkDelete API
            activityInfoProvider.BulkDelete(new WhereCondition().WhereIn("ActivityContactID", contactIds));
        }
    }

    private void DeleteContacts(IEnumerable<ContactInfo> contacts, IDictionary<string, object> configuration)
    {
        // Checks whether deletion of contacts is enabled in the configuration options
        object deleteContacts;
        if (configuration.TryGetValue("DeleteContacts", out deleteContacts)
            && ValidationHelper.GetBoolean(deleteContacts, false))
        {
            // Deletes the specified contacts
            foreach (ContactInfo contact in contacts)
            {
                contactInfoProvider.Delete(contact);
            }
        }
    }
}
```

The sample data eraser processes the contact objects provided by the registered identity collector (see [Example - Contact data collectors](/documentation/developers-and-admins/data-protection/personal-data-collection#example-contact-data-collectors) for details). The eraser then deletes the contacts and/or their logged activities according to configuration options, which are supplied by users through the erasure configuration dialog.
### Register the eraser
To register the personal data eraser, edit the [module class](/documentation/developers-and-admins/customization/run-code-on-application-startup) under the custom project and expand the initialization code in the `OnInit` method:
C#
Copy
```
using CMS;
using CMS.DataEngine;
using CMS.DataProtection;

// Registers the custom module into the system
[assembly: RegisterModule(typeof(CustomDataProtectionModule))]

internal class CustomDataProtectionModule : Module
{
    // Module class constructor, the system registers the module under the name "CustomDataProtection"
    public CustomDataProtectionModule()
        : base("CustomDataProtection")
    {
    }

    // Contains initialization code that is executed when the application starts
    protected override void OnInit()
    {
        base.OnInit();

        // Adds the ContactIdentityCollector to the collection of registered identity collectors
        IdentityCollectorRegister.Instance.Add(new ContactIdentityCollector());

        // Adds the ContactDataCollector to the collection of registered personal data collectors
        PersonalDataCollectorRegister.Instance.Add(new ContactDataCollector());

        // Adds the ContactDataEraser to the collection of registered personal data erasers
        PersonalDataEraserRegister.Instance.Add(new ContactDataEraser());
    }
}
```

### Add the eraser configuration
Add a new class implementing the `IDataErasureDialogModel` interface. The erasure dialog model must contain properties that represent options available in the erasure configuration dialog.
C#
Copy
```
using System.Threading.Tasks;

using Kentico.Xperience.Admin.Base.Forms;
using Kentico.Xperience.Admin.Base.FormAnnotations;
using Kentico.Xperience.Admin.DigitalMarketing;

public class CustomDataErasureDialogModel : IDataErasureDialogModel
{
    // Erasure dialog onfiguration properties and their editing components
    [CheckBoxComponent(Label = "Delete contacts", Order = 1)]
    public bool DeleteContacts { get; set; }

    [CheckBoxComponent(Label = "Delete activities", Order = 2)]
    public bool DeleteActivities { get; set; }

    // Validates the output of the dialog
    public virtual async Task<ValidationResult> Validate()
    {
        if (DeleteContacts || DeleteActivities)
        {
            return await ValidationResult.SuccessResult();
        }

        return new ValidationResult(isValid: false, errorMessage: "At least one item needs to be selected!");
    }
}
```

Create and register a [page extender](/documentation/developers-and-admins/customization/extend-the-administration-interface/ui-pages/ui-page-extenders) that assigns your custom data erasure dialog model to the `RightToBeforgotten` UI page.
C#
Copy
```
using Kentico.Xperience.Admin.Base;
using Kentico.Xperience.Admin.DigitalMarketing.UIPages;

[assembly: PageExtender(typeof(RightToBeForgottenExtender))]

public class RightToBeForgottenExtender : PageExtender<RightToBeForgotten>
{
    public override Task ConfigurePage()
    {
        // Assigns a custom erasure dialog model to the page configuration
        Page.PageConfiguration.DataErasureDialogModel = new CustomDataErasureDialogModel();

        return base.ConfigurePage();
    }
}
```

Save all changes and **Rebuild** the custom project.
You can now delete personal data for contacts and their activities in the **Data protection** application. On the application’s **Right to be forgotten** tab, you can search for data based on an email addresses. If matching contacts exist in the system, the sample collectors display their data in plain text format, and you can click **Select data to delete** to open the eraser configuration dialog.
[![Deleting contact personal data in the Data protection application](/docsassets/documentation/personal-data-erasure/personal_data_erasing.png)](/docsassets/documentation/personal-data-erasure/personal_data_erasing.png)
Submitting the dialog sends the collected data and selected configuration options to the registered eraser implementation, which performs the corresponding delete actions.
Contacts have a parent-child relationship with activities. If you delete a contact object, all of the activities logged for the given contact are also deleted automatically. However, the configuration dialog allows you to delete activities while retaining contacts.
![]()
[]()[]()
