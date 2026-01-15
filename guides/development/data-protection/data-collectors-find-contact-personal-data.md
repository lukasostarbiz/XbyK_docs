# Data collectors - Find contact personal data
  * [ Copy page link ](guides/development/data-protection/data-collectors-find-contact-personal-data#) | [Get HelpService ID](guides/development/data-protection/data-collectors-find-contact-personal-data#)
Core MVC 5


[✖](guides/development/data-protection/data-collectors-find-contact-personal-data# "Close page link panel") [Copy to clipboard](guides/development/data-protection/data-collectors-find-contact-personal-data#)
Some data protection laws require all of a visitor’s tracked personal data to be gathered and delivered to them upon request. A data collector is necessary to implement such functionality in Xperience by Kentico.
Data collectors find other objects associated with a provided set of identities from the Identity collector. In this example, the identities are contacts, and the data collector will find accounts, consent agreements, form data, activities, and other auxiliary data associated with those contacts.
This example includes _accounts_ and other related objects which are considered obsolete as of version **30.9.0**.
More details are available in the [changelog](documentation/changelog#refresh-august-25-2025).
If you are using a newer version, you can remove the affected code from these samples.
## Before you start
This guide requires the following:
  * Familiarity with [C#](https://learn.microsoft.com/en-us/dotnet/csharp/), [.NET Core](https://learn.microsoft.com/en-us/dotnet/), [Dependency injection](https://learn.microsoft.com/en-us/dotnet/core/extensions/dependency-injection), and the [MVC pattern](https://learn.microsoft.com/en-us/aspnet/core/mvc/overview).
  * A running instance of Xperience by Kentico, preferably [30.11.1](documentation/changelog) or higher. 
Some features covered in the training guides may not work in older versions. 


The examples in this guide require that you:
  * Have followed along with the samples from the [earlier guides in the series](guides/development/data-protection).


The example presented in this [Data protection guide series](guides/development/data-protection) is a **valid implementation of data protection for[Contacts](documentation/business-users/digital-marketing/contact-management)** in Xperience by Kentico. (Note that it does not cover the collection and erasure of [Members](documentation/business-users/members) and their associated data.)
You can copy-paste the code samples into your own solution.
However, if you choose to do so, **make sure to consult your legal team** to determine whether the implementation, texts, and consent levels **meet the requirements of your region and market**.
**Code samples**
You can find a project with completed, working versions of code samples from this guide and others in the [finished branch](https://github.com/Kentico/xperience-by-kentico-training-guides/tree/finished) of the _Training guides_ repository.
The [main branch](https://github.com/Kentico/xperience-by-kentico-training-guides) of the repository provides a starting point to code along with the guides.
The code samples in this guide are for [.NET 8](https://learn.microsoft.com/en-us/dotnet/core/whats-new/dotnet-8/overview) only.
They come from a project that uses [implicit using directives](https://learn.microsoft.com/en-us/dotnet/core/project-sdk/overview#implicit-using-directives). You may need to add additional `using` directives to your code if your project does not use this feature.
## Create data writers
Based on whether data collection is requested through the **Data portability** or **Right to access** tab in the Xperience UI, it will invoke registered personal data collectors with the `outputFormat` parameter set to a constant string value, either `PersonalDataFormat.HUMAN_READABLE` or `PersonalDataFormat.MACHINE_READABLE`.
Data collectors are meant to output a string result.
While it is possible to include logic to print in different formats within the code that collects the data, separating it out into dedicated writer classes keeps the code clearer and less redundant.
### Lay the groundwork
Before you define the first writer, create a class that represents a column that should be collected and displayed, as well as an interface that both writers will implement.
  1. In the _TrainingGuides.Web_ /Features/_DataProtection/Collectors_ folder, create a `CollectedColumn` class.
  2. Add properties to hold the display name and a code name.
  3. Define a constructor that takes both properties as parameters.
C#
**CollectedColumn.cs**
Copy
```
namespace TrainingGuides.Web.Features.DataProtection.Collectors;

public class CollectedColumn
{
    public string Name { get; }

    public string DisplayName { get; }

    public CollectedColumn(string name, string displayName)
    {
        Name = name;
        DisplayName = displayName;
    }
}
```

  4. Create a _Writers_ subfolder in _~/Features/DataProtection_ and add an interface that both the human and machine-readable writer classes can implement.
  5. Similar to the [example in the documentation](documentation/developers-and-admins/data-protection/personal-data-collection#implement-writer-classes), add signatures for void methods that create beginning and ending sections and info objects.
  6. Create an additional method signature for writing values.
  7. Add a method signature for a string method that outputs the result of the writer’s operations.
C#
**IPersonalDataWriter.cs**
Copy
```
using CMS.DataEngine;
using TrainingGuides.Web.Features.DataProtection.Collectors;

namespace TrainingGuides.Web.Features.DataProtection.Writers;

public interface IPersonalDataWriter : IDisposable
{
    void WriteStartSection(string sectionName, string sectionDisplayName);
    void WriteBaseInfo(BaseInfo baseInfo, List<CollectedColumn> columns, Func<string, object, object>? valueTransformationFunction = null);
    void WriteSectionValue(string sectionName, string sectionDisplayName, string value);
    void WriteEndSection();
    string GetResult();
}
```



### Add a human-readable writer
  1. In the same folder, define a `HumanReadablePersonalDataWriter` that implements the `IPersonalDataWriter` interface.
  2. Define it similarly to the [documentation example](documentation/developers-and-admins/data-protection/personal-data-collection#implement-writer-classes), utilizing a private string builder property.
  3. Add a method called `WriteSectionValue` that prints a key and value, ignoring the section name, which the machine-readable counterpart can utilize.
  4. Leave the `Dispose` method empty.
You don’t need the method here, but it is important for the machine-readable data writer covered in the [next step](guides/development/data-protection/data-collectors-find-contact-personal-data#create-a-machine-readable-writer).


C#
**HumanReadablePersonalDataWriter.cs**
Copy
```
using CMS.Base;
using CMS.DataEngine;
using System.Globalization;
using System.Text;
using TrainingGuides.Web.Features.DataProtection.Collectors;

namespace TrainingGuides.Web.Features.DataProtection.Writers;

public class HumanReadablePersonalDataWriter : IPersonalDataWriter
{
    private readonly StringBuilder stringBuilder;
    private int indentationLevel;
    private bool ignoreNewLine;

    private static readonly string decimalPrecision = new('#', 26);
    private static readonly string decimalFormat = "{0:0.00" + decimalPrecision + "}";

    public CultureInfo Culture { get; set; } = new CultureInfo(SystemContext.SYSTEM_CULTURE_NAME);

    public HumanReadablePersonalDataWriter()
    {
        stringBuilder = new StringBuilder();
        indentationLevel = 0;
        ignoreNewLine = false;
    }

    public void WriteStartSection(string sectionName, string sectionDisplayName)
    {
        ignoreNewLine = false;
        Indent();

        stringBuilder.AppendLine(sectionDisplayName + ": ");
        indentationLevel++;
    }

    private void Indent() => stringBuilder.Append('\t', indentationLevel);

    public void WriteBaseInfo(BaseInfo baseInfo, List<CollectedColumn> columns,
        Func<string, object, object>? valueTransformationFunction = null)
    {
        if (baseInfo == null)
        {
            throw new ArgumentNullException(nameof(baseInfo));
        }

        foreach (var column in columns)
        {
            string columnName = column.Name;
            string columnDisplayName = column.DisplayName;

            if (string.IsNullOrWhiteSpace(columnDisplayName) ||
                columnName.Equals(baseInfo.TypeInfo.IDColumn, StringComparison.Ordinal) ||
                columnName.Equals(baseInfo.TypeInfo.GUIDColumn, StringComparison.Ordinal))
            {
                continue;
            }

            object value = baseInfo.GetValue(columnName);

            if (value == null)
            {
                continue;
            }

            if (valueTransformationFunction != null)
            {
                value = valueTransformationFunction(columnName, value);
            }

            WriteKeyValue(columnDisplayName, value);
        }
    }

    public void WriteSectionValue(string sectionName, string sectionDisplayName, string value)
    {
        Indent();

        stringBuilder.Append($"{sectionDisplayName}: {value}");
        stringBuilder.AppendLine();
    }

    private void WriteKeyValue(string keyName, object value)
    {
        Indent();
        stringBuilder.Append($"{keyName}: ");

        string format = "{0}";

        if (value is decimal)
        {
            format = decimalFormat;
        }

        stringBuilder.AppendFormat(Culture, format, value);
        stringBuilder.AppendLine();

        ignoreNewLine = true;
    }

    public void WriteEndSection()
    {
        indentationLevel--;

        if (ignoreNewLine)
            return;

        Indent();
        stringBuilder.AppendLine();
        ignoreNewLine = true;
    }

    public string GetResult() => stringBuilder.ToString();

    public void Dispose()
    {
    }
}
```

### Create a machine-readable writer
Add an XML writer that closely mirrors the [example in the documentation](documentation/developers-and-admins/data-protection/personal-data-collection#implement-writer-classes).
In this case, `WriteSectionValue` needs to use the `sectionName` property for the XML tags rather than the display name.
C#
**XmlPersonalDataWriter.cs**
Copy
```
using CMS.DataEngine;
using CMS.Helpers;
using System.Text;
using System.Xml;
using TrainingGuides.Web.Features.DataProtection.Collectors;

namespace TrainingGuides.Web.Features.DataProtection.Writers;

public class XmlPersonalDataWriter : IPersonalDataWriter
{
    private readonly StringBuilder stringBuilder;
    private readonly XmlWriter xmlWriter;

    public XmlPersonalDataWriter()
    {
        stringBuilder = new StringBuilder();
        xmlWriter = XmlWriter.Create(stringBuilder, new XmlWriterSettings { Indent = true, OmitXmlDeclaration = true });
    }

    public void WriteStartSection(string sectionName, string sectionDisplayName) =>
        xmlWriter.WriteStartElement(TransformElementName(sectionName));

    private string TransformElementName(string originalName) => originalName.Replace('.', '_');

    public void WriteBaseInfo(BaseInfo baseInfo, List<CollectedColumn> columns,
        Func<string, object, object>? valueTransformationFunction = null)
    {
        if (baseInfo == null)
        {
            throw new ArgumentNullException(nameof(baseInfo));
        }

        foreach (var columnTuple in columns)
        {
            string columnName = columnTuple.Name;

            if (string.IsNullOrWhiteSpace(columnTuple.DisplayName))
            {
                continue;
            }

            object value = baseInfo.GetValue(columnName);
            if (value == null)
            {
                continue;
            }

            if (valueTransformationFunction != null)
            {
                value = valueTransformationFunction(columnName, value);
            }

            xmlWriter.WriteStartElement(columnName);
            xmlWriter.WriteValue(XmlHelper.ConvertToString(value, value.GetType()));
            xmlWriter.WriteEndElement();
        }
    }

    public void WriteSectionValue(string sectionName, string sectionDisplayName, string value)
    {
        xmlWriter.WriteStartElement(sectionName);
        xmlWriter.WriteString(value);
        xmlWriter.WriteEndElement();
    }

    public void WriteEndSection() => xmlWriter.WriteEndElement();

    public string GetResult()
    {
        xmlWriter.Flush();

        return stringBuilder.ToString();
    }

    public void Dispose() => xmlWriter.Dispose();
}
```

## Collect data
### Prepare to collect forms
While many of the objects that need to be collected are relatively straightforward, containing a set of known columns, including a reference to the _contact_ type, form submissions are more complicated.
The columns of forms are defined by editors in the Xperience administration interface. For this reason, there is no way to anticipate which columns the forms will consist of, and they cannot be explicitly referenced in code in advance.
You need logic to iterate through the forms on the website and determine if any of them have one or more email columns.
Create a class called `FormDefinition` in the _DataProtection/Collectors_ folder, which contains a list of email column names and a list of other columns to display.
C#
**FormDefinition.cs**
Copy
```
namespace TrainingGuides.Web.Features.DataProtection.Collectors;

public class FormDefinition
{
    public const string FORM_CONSENT_COLUMN_NAME = "Consent";
    public IEnumerable<string> EmailColumns { get; }
    public List<CollectedColumn> FormColumns { get; }

    public FormDefinition(List<string> emailColumns, List<CollectedColumn> formColumns)
    {
        EmailColumns = emailColumns;
        FormColumns = formColumns;
    }
}
```

When it comes to populating `FormDefinition` objects, another service class will be useful.
  1. In the _~/Features/DataProtection/Services_ folder of the _TrainingGuides.Web_ project, add an `IFormCollectionService` interface and a `FormCollectionService` class that implements it.
Remember to **register the service with the dependency injection container** in `AddTrainingGuidesServices` method inside _TrainingGuides.Web/ServiceCollectionExtensions.cs_.
C#
**ServiceCollectionExtensions.cs**
Copy
```
...
services.AddSingleton<IFormCollectionService, FormCollectionService>();
...
```

  2. Add a public method called `GetForms`, which retrieves all of the forms in the database and converts them to a `Dictionary`, with `Guid` objects as keys and of `FormDefinition` objects as values. Within this method, do the following:
    1. Find the `DataClassInfo` object associated with each form, and parse through the XML of the `ClassFormDefinition` and `ClassContactMapping` columns.
    2. Use this data to assemble a collection of email fields. For this example, target fields that are either mapped to the `ContactEmail` field or use the `Kentico.EmailInput` form component.
In your projects, you can define other sets of conditions to broaden or narrow the search for matching email addresses.
    3. Make a separate collection of fields that are not email fields or ID fields.
    4. Use these two collections to assemble `FormDefinition` for the form and add it to the dictionary.
  3. Create another public method called `GetBizFormItems` to retrieve all form submissions associated with a contact. The method needs to do the following:
    1. Construct an `ObjectQuery<BizFormItem> bizFormItems` to retrieve all form submissions that meet one of the following conditions: 
      1. The submission has an email field with one of the provided email addresses.  
or
      2. The submission references one of the supplied `ConsentAgreementInfo` objects.
    2. Use a `FormDefinition` parameter to determine which email fields to check for the supplied addresses.
    3. Use a `DataClassInfo` object to determine items of which form to retrieve.


C#
**IFormCollectionService.cs**
Copy
```
using System.Data;
using CMS.DataEngine;
using CMS.DataProtection;
using CMS.OnlineForms;
using TrainingGuides.Web.Features.DataProtection.Collectors;

namespace TrainingGuides.Web.Features.DataProtection.Services;

public interface IFormCollectionService
{
    public Dictionary<Guid, FormDefinition> GetForms();
    public ObjectQuery<BizFormItem> GetBizFormItems(ICollection<string> emails, ObjectQuery<ConsentAgreementInfo> consentAgreementGuids, DataRow row, FormDefinition formDefinition);
}
```

C#
**FormCollectionService.cs**
Copy
```
using CMS.DataEngine;
using CMS.DataProtection;
using CMS.OnlineForms;
using System.Data;
using System.Xml.Linq;
using TrainingGuides.Web.Features.DataProtection.Collectors;

namespace TrainingGuides.Web.Features.DataProtection.Services;

public class FormCollectionService : IFormCollectionService
{
    private readonly IInfoProvider<BizFormInfo> bizFormInfoProvider;

    public FormCollectionService(IInfoProvider<BizFormInfo> bizFormInfoProvider)
    {
        this.bizFormInfoProvider = bizFormInfoProvider;
    }

    private bool IsField(XElement element) => element.Name.ToString() == "field";

    private bool UsesComponent(XElement fieldElement, string componentIdentifier)
    {
        var matchingComponents = fieldElement
            .Descendants()
            .Where(desc => desc.Name == "componentidentifier" && desc.Value == componentIdentifier);
        return matchingComponents.Count() > 0;
    }

    private bool IsInList(XElement fieldElement, IEnumerable<string> list) =>
        list.Contains(fieldElement.Attribute("column")?.Value, StringComparer.OrdinalIgnoreCase);

    private bool IsNotInCollection(XElement fieldElement, IEnumerable<XElement> excludedFieldElements) =>
        !excludedFieldElements.Contains(fieldElement);

    private bool IsNotID(XElement fieldElement)
    {
        string? isPK = fieldElement.Attribute("isPK")?.Value;
        return !bool.Parse(isPK ?? "false");
    }

    private string GetCaption(XElement fieldElement) =>
        fieldElement
            .Descendants()
            .Where(desc => desc.Name == "fieldcaption")
            .FirstOrDefault()?
            .Value
        ?? string.Empty;

    /// <summary>
    /// Gets information about all forms in the database, including which of the form's fields are email fields
    /// </summary>
    /// <returns>A <see cref="Dictionary{TKey, TValue}"/> mapping a form's <see cref="Guid"/> to a <see cref="FormDefinition"/>, which specifies the form's email fields and non-email fields.</returns>
    public Dictionary<Guid, FormDefinition> GetForms()
    {
        var result = new Dictionary<Guid, FormDefinition>();
        var bizForms = bizFormInfoProvider.Get();
        foreach (var bizForm in bizForms)
        {
            var dataClassInfo = DataClassInfoProvider.GetDataClassInfo(bizForm.FormClassID);
            if (dataClassInfo == null || string.IsNullOrEmpty(dataClassInfo.ClassFormDefinition))
            {
                continue;
            }

            IEnumerable<string> mappedEmailFields = [];

            if (!string.IsNullOrEmpty(dataClassInfo.ClassContactMapping))
            {
                var contactMapping = XElement.Parse(dataClassInfo.ClassContactMapping);

                //gets lowercase names of fields mapped to contact email (there should be only one unless they mess with the database directly)
                mappedEmailFields = contactMapping?.Elements()
                    .Where(field => field.Attribute("column")?.Value == "ContactEmail")
                    .Select(field => field.Attribute("mappedtofield")?.Value ?? string.Empty)
                ?? [];
            }

            var formDefinition = XElement.Parse(dataClassInfo.ClassFormDefinition);

            //gets all form fields which are either mapped to the ContactEmail column, or which use the Kentico.EmailInput form control
            var emailFields = formDefinition?.Elements()
                .Where(element => IsField(element) && (IsInList(element, mappedEmailFields) || UsesComponent(element, "Kentico.EmailInput"))) ?? [];

            //gets the remaining fields
            var otherFields = formDefinition?.Elements()
                .Where(element => IsField(element) && IsNotInCollection(element, emailFields) && IsNotID(element)) ?? [];

            result.Add(
                bizForm.FormGUID,
                new FormDefinition(
                    emailFields
                        .Select(element => element.Attribute("column")?.Value ?? string.Empty)
                        .ToList(),
                    otherFields
                        .Select(element => new CollectedColumn(element.Attribute("column")?.Value ?? string.Empty, GetCaption(element)))
                        .ToList()
                    )
                );
        }
        return result;
    }

    /// <summary>
    /// Gets an object query for all bizform items with any of the supplied emails in any of its email columns, or with any of the supplied consent agreements
    /// </summary>
    /// <param name="emails">The email addresses to look for</param>
    /// <param name="consentAgreementGuids">The guids of consents agreed to by the contact</param>
    /// <param name="row">a DataRow representing the DataClassInfo associated with the given form</param>
    /// <param name="formDefinition">The form definition of the supplied bizform</param>
    /// <returns>An <see cref="ObjectQuery"/>for all <see cref="BizFormItem"/> objects related to the provided contact through a consent agreement or email address</returns>
    public ObjectQuery<BizFormItem> GetBizFormItems(ICollection<string> emails, ObjectQuery<ConsentAgreementInfo> consentAgreementGuids, DataRow row, FormDefinition formDefinition)
    {
        var formClass = new DataClassInfo(row);

        var bizFormItems = BizFormItemProvider.GetItems(formClass.ClassName);
        foreach (string column in formDefinition.EmailColumns)
        {
            bizFormItems.Or().WhereIn(column, emails);
        }

        var consentColumns = formDefinition.FormColumns
            .Where(column => column.Name
            .Contains(FormDefinition.FORM_CONSENT_COLUMN_NAME)).Select(column => column.Name);

        if (formClass.ClassFormDefinition.Contains(FormDefinition.FORM_CONSENT_COLUMN_NAME))
        {
            foreach (string columnName in consentColumns)
            {
                bizFormItems.Or().WhereIn(columnName, consentAgreementGuids);
            }
        }
        return bizFormItems;
    }
}
```

### Gather the data
You have prepared what you need to gather form submissions. In the next steps, you will add the main collection logic to your project.
  1. In the _TrainingGuides.Web_ /Features/_DataProtection/Collectors_ folder, create a class called `ContactDataCollectorCore`.
This class will hold all of the data collection logic, relying on the `IPersonalDataWriter` interface and separating the data collection code from the code that determines which writer to use.
  2. Define private variables to hold the data writer, lists of the relevant columns of all the classes to collect, and a dictionary of forms populated by the `FormCollectionService` from earlier.
  3. Add transformation methods to turn certain types of raw data references into a human-readable format.
  4. Populate the writer field from a parameter in the constructor.
  5. Define the `CollectData` method with a `string` return type, using the data writer to print details about all the object types.
Consent objects may be more complicated than they appear at a glance. Aside from writing details about the consents themselves, you must find consent agreements (`ConsentAgreementInfo`) and consent archives (`ConsentArchiveInfo`) associated with each consent.


C#
**ContactDataCollectorCore.cs**
Copy
```
using System.Data;
using System.Xml;
using CMS.Activities;
using CMS.ContactManagement;
using CMS.DataEngine;
using CMS.DataProtection;
using CMS.Globalization;
using CMS.Helpers;
using CMS.OnlineForms;
using TrainingGuides.Web.Features.DataProtection.Services;
using TrainingGuides.Web.Features.DataProtection.Writers;

namespace TrainingGuides.Web.Features.DataProtection.Collectors;

public class ContactDataCollectorCore
{
    private readonly List<CollectedColumn> contactInfoColumns =
    [
        new CollectedColumn("ContactFirstName", "First name"),
        new CollectedColumn("ContactMiddleName", "Middle name"),
        new CollectedColumn("ContactLastName", "Last name"),
        new CollectedColumn("ContactJobTitle", "Job title"),
        new CollectedColumn("ContactAddress1", "Address"),
        new CollectedColumn("ContactCity", "City"),
        new CollectedColumn("ContactZIP", "ZIP"),
        new CollectedColumn("ContactStateID", ""),
        new CollectedColumn("ContactCountryID", ""),
        new CollectedColumn("ContactMobilePhone", "Mobile phone"),
        new CollectedColumn("ContactBusinessPhone", "Business phone"),
        new CollectedColumn("ContactEmail", "Email"),
        new CollectedColumn("ContactBirthday", "Birthday"),
        new CollectedColumn("ContactGender", "Gender"),
        new CollectedColumn("ContactNotes", "Notes"),
        new CollectedColumn("ContactGUID", "GUID"),
        new CollectedColumn("ContactLastModified", "Last modified"),
        new CollectedColumn("ContactCreated", "Created"),
        new CollectedColumn("ContactCampaign", "Campaign"),
        new CollectedColumn("ContactCompanyName", "Company name")
    ];

    private readonly List<CollectedColumn> consentAgreementInfoColumns =
    [
        new CollectedColumn("ConsentAgreementGuid", "GUID"),
        new CollectedColumn("ConsentAgreementRevoked", "Consent action"),
        new CollectedColumn("ConsentAgreementTime", "Performed on")
    ];

    private readonly List<CollectedColumn> consentInfoColumns =
    [
        new CollectedColumn("ConsentGUID", "GUID"),
        new CollectedColumn("ConsentDisplayName", "Consent name"),
        new CollectedColumn("ConsentContent", "Full text")
    ];

    private readonly List<CollectedColumn> consentArchiveInfoColumns =
    [
        new CollectedColumn("ConsentArchiveGUID", "GUID"),
        new CollectedColumn("ConsentArchiveContent", "Full text")
    ];

    private readonly List<CollectedColumn> activityInfoColumns =
    [
        new CollectedColumn("ActivityId", "ID"),
        new CollectedColumn("ActivityCreated", "Created"),
        new CollectedColumn("ActivityType", "Type"),
        new CollectedColumn("ActivityUrl", "URL"),
        new CollectedColumn("ActivityTitle", "Title"),
        new CollectedColumn("ActivityItemId", "")
    ];

    private readonly List<CollectedColumn> accountInfoColumns =
    [
        new CollectedColumn("AccountName", "Name"),
        new CollectedColumn("AccountAddress1", "Address"),
        new CollectedColumn("AccountAddress2", "Address 2"),
        new CollectedColumn("AccountCity", "City"),
        new CollectedColumn("AccountZip", "ZIP"),
        new CollectedColumn("AccountWebSite", "Web site"),
        new CollectedColumn("AccountEmail", "Email"),
        new CollectedColumn("AccountPhone", "Phone"),
        new CollectedColumn("AccountFax", "Fax"),
        new CollectedColumn("AccountNotes", "Notes"),
        new CollectedColumn("AccountGUID", "GUID")
    ];

    private readonly List<CollectedColumn> countryInfoColumns =
    [
        new CollectedColumn("CountryDisplayName", "Country")
    ];

    private readonly List<CollectedColumn> stateInfoColumns =
    [
        new CollectedColumn("StateDisplayName", "State")
    ];

    private readonly List<CollectedColumn> contactGroupInfoColumns =
    [
        new CollectedColumn("ContactGroupGUID", "GUID"),
        new CollectedColumn("ContactGroupName", "Contact group name"),
        new CollectedColumn("ContactGroupDescription", "Contact group description")
    ];

    private readonly IPersonalDataWriter personalDataWriter;
    private readonly IFormCollectionService formCollectionService;
    private readonly Dictionary<Guid, FormDefinition> forms;

    private readonly IInfoProvider<ConsentAgreementInfo> consentAgreementInfoProvider;
    private readonly IInfoProvider<ActivityInfo> activityInfoProvider;
    private readonly IInfoProvider<BizFormInfo> bizFormInfoProvider;
    private readonly IInfoProvider<AccountInfo> accountInfoProvider;
    private readonly IInfoProvider<AccountContactInfo> accountContactInfoProvider;
    private readonly IInfoProvider<CountryInfo> countryInfoProvider;
    private readonly IInfoProvider<StateInfo> stateInfoProvider;


    public ContactDataCollectorCore(IPersonalDataWriter personalDataWriter,
        IFormCollectionService formCollectionService,
        IInfoProvider<ConsentAgreementInfo> consentAgreementInfoProvider,
        IInfoProvider<ActivityInfo> activityInfoProvider,
        IInfoProvider<BizFormInfo> bizFormInfoProvider,
        IInfoProvider<AccountInfo> accountInfoProvider,
        IInfoProvider<AccountContactInfo> accountContactInfoProvider,
        IInfoProvider<CountryInfo> countryInfoProvider,
        IInfoProvider<StateInfo> stateInfoProvider)
    {
        this.personalDataWriter = personalDataWriter;
        this.formCollectionService = formCollectionService;
        this.consentAgreementInfoProvider = consentAgreementInfoProvider;
        this.activityInfoProvider = activityInfoProvider;
        this.bizFormInfoProvider = bizFormInfoProvider;
        this.accountInfoProvider = accountInfoProvider;
        this.accountContactInfoProvider = accountContactInfoProvider;
        this.countryInfoProvider = countryInfoProvider;
        this.stateInfoProvider = stateInfoProvider;

        forms = this.formCollectionService.GetForms();
    }

    public string? CollectData(IEnumerable<BaseInfo> identities)
    {
        var contacts = identities.OfType<ContactInfo>().ToList();
        if (!contacts.Any())
        {
            return null;
        }

        var contactIDs = contacts.Select(c => c.ContactID).ToList();
        var contactEmails = contacts.Select(c => c.ContactEmail).ToList();

        var contactActivities = activityInfoProvider.Get()
            .Columns(activityInfoColumns.Select(t => t.Name))
            .WhereIn("ActivityContactID", contactIDs).ToList();

        var contactContactGroups = contacts
            .SelectMany(c => c.ContactGroups)
            .GroupBy(c => c.ContactGroupID)
            .Select(group => group.First());

        personalDataWriter.WriteStartSection("OnlineMarketingData", "Online marketing data");

        string before = personalDataWriter.GetResult();

        WriteContacts(contacts);
        WriteConsents(contactIDs);
        WriteContactActivities(contactActivities);
        WriteContactAccounts(contactIDs);
        WriteContactGroups(contactContactGroups);
        WriteSubmittedFormsData(contactEmails, contactIDs);

        string after = personalDataWriter.GetResult();
        //return nothing if the previous methods did not find any data to write
        if (string.IsNullOrWhiteSpace(after) || after.Equals(before))
        {
            return null;
        }

        personalDataWriter.WriteEndSection();

        return personalDataWriter.GetResult();
    }

    private object TransformGenderValue(string columnName, object columnValue)
    {
        if (columnName.Equals("ContactGender", StringComparison.InvariantCultureIgnoreCase))
        {
            int? gender = columnValue as int?;
            return gender switch
            {
                1 => "male",
                2 => "female",
                _ => "undefined",
            };
        }

        return columnValue;
    }

    private object TransformConsentText(string columnName, object columnValue)
    {
        if (columnName.Equals("ConsentContent", StringComparison.InvariantCultureIgnoreCase) ||
            columnName.Equals("ConsentArchiveContent", StringComparison.InvariantCultureIgnoreCase))
        {
            var consentXml = new XmlDocument();
            consentXml.LoadXml((columnValue as string) ?? string.Empty);

            var xmlNode =
                consentXml.SelectSingleNode("/ConsentContent/ConsentLanguageVersions/ConsentLanguageVersion/FullText");

            string result = HTMLHelper.StripTags(xmlNode?.InnerText);

            return result;
        }

        return columnValue;
    }

    private object TransformConsentAction(string columnName, object columnValue)
    {
        if (columnName.Equals("ConsentAgreementRevoked", StringComparison.InvariantCultureIgnoreCase))
        {
            bool revoked = (bool)columnValue;

            return revoked ? "Revoked" : "Agreed";
        }

        return columnValue;
    }

    private void WriteContacts(IEnumerable<ContactInfo> contacts)
    {
        foreach (var contactInfo in contacts.Where(contact => contact.ContactID > 0))
        {
            personalDataWriter.WriteStartSection(ContactInfo.OBJECT_TYPE, "Contact");
            personalDataWriter.WriteBaseInfo(contactInfo, contactInfoColumns, TransformGenderValue);

            int countryId = contactInfo.ContactCountryID;
            int stateId = contactInfo.ContactStateID;

            if (countryId != 0)
            {
                personalDataWriter.WriteBaseInfo(countryInfoProvider.Get(countryId), countryInfoColumns);
            }

            if (stateId != 0)
            {
                personalDataWriter.WriteBaseInfo(stateInfoProvider.Get(stateId), stateInfoColumns);
            }

            personalDataWriter.WriteEndSection();
        }
    }

    private void WriteConsents(ICollection<int> contactIDs)
    {
        var consentsDataSet = consentAgreementInfoProvider.Get()
            .Source(s => s.Join<ConsentInfo>("CMS_ConsentAgreement.ConsentAgreementConsentID", "ConsentID"))
            .Source(s =>
                s.LeftJoin<ConsentArchiveInfo>("CMS_ConsentAgreement.ConsentAgreementConsentHash",
                    "ConsentArchiveHash"))
            .WhereIn("ConsentAgreementContactID", contactIDs)
            .OrderBy("ConsentID")
            .OrderByDescending("ConsentAgreementTime")
            .Result;

        if (DataHelper.DataSourceIsEmpty(consentsDataSet))
        {
            return;
        }
        var consents = new Dictionary<int, ConsentInfo>();
        var consentRevocations = new Dictionary<int, List<ConsentAgreementInfo>>();

        var consentContentArchives = new Dictionary<string, ConsentArchiveInfo>(StringComparer.OrdinalIgnoreCase);
        var consentContentAgreements =
            new Dictionary<string, List<ConsentAgreementInfo>>(StringComparer.OrdinalIgnoreCase);

        foreach (var row in consentsDataSet.Tables[0].AsEnumerable())
        {
            var consentAgreementInfo = new ConsentAgreementInfo(row);

            if (!consents.TryGetValue(consentAgreementInfo.ConsentAgreementConsentID, out var consentInfo))
            {
                consentInfo = new ConsentInfo(row);
                consents.Add(consentAgreementInfo.ConsentAgreementConsentID, consentInfo);
            }

            if (consentAgreementInfo.ConsentAgreementRevoked)
            {
                var revocationsOfSameConsent = GetRevocationsOfSameConsent(consentRevocations,
                    consentAgreementInfo.ConsentAgreementConsentID);
                revocationsOfSameConsent.Add(consentAgreementInfo);
            }
            else
            {
                var agreementsOfSameConsentContent = GetAgreementsOfSameConsentContent(consentContentAgreements,
                    consentAgreementInfo.ConsentAgreementConsentHash);
                agreementsOfSameConsentContent.Add(consentAgreementInfo);

                if (IsAgreementOfDifferentConsentContent(consentAgreementInfo, consentInfo) &&
                    !consentContentArchives.ContainsKey(consentAgreementInfo.ConsentAgreementConsentHash))
                {
                    consentContentArchives.Add(consentAgreementInfo.ConsentAgreementConsentHash,
                        new ConsentArchiveInfo(row));
                }
            }
        }

        WriteConsents(consents, consentContentArchives, consentContentAgreements, consentRevocations);
    }

    private static List<ConsentAgreementInfo> GetRevocationsOfSameConsent(
        Dictionary<int, List<ConsentAgreementInfo>> consentRevocations, int consentId)
    {
        if (!consentRevocations.TryGetValue(consentId, out var revocationsOfSameConsent))
        {
            revocationsOfSameConsent = [];
            consentRevocations.Add(consentId, revocationsOfSameConsent);
        }

        return revocationsOfSameConsent;
    }

    private static List<ConsentAgreementInfo> GetAgreementsOfSameConsentContent(
        Dictionary<string, List<ConsentAgreementInfo>> consentContentAgreements, string consentHash)
    {
        if (!consentContentAgreements.TryGetValue(consentHash, out var agreementsOfSameConsent))
        {
            agreementsOfSameConsent = [];
            consentContentAgreements.Add(consentHash, agreementsOfSameConsent);
        }

        return agreementsOfSameConsent;
    }

    private static bool IsAgreementOfDifferentConsentContent(ConsentAgreementInfo consentAgreementInfo,
        ConsentInfo consentInfo) => consentAgreementInfo.ConsentAgreementConsentHash != consentInfo.ConsentHash;

    private void WriteConsents(Dictionary<int, ConsentInfo> consents,
        Dictionary<string, ConsentArchiveInfo> consentContentArchives,
        Dictionary<string, List<ConsentAgreementInfo>> consentContentAgreements,
        Dictionary<int, List<ConsentAgreementInfo>> consentRevocations)
    {
        foreach (var agreementsOfSameConsentContent in consentContentAgreements.Values)
        {
            var consentAgreement = agreementsOfSameConsentContent.First();
            var consentInfo = consents[consentAgreement.ConsentAgreementConsentID];

            consentContentArchives.TryGetValue(consentAgreement.ConsentAgreementConsentHash, out var consentArchiveInfo);
            consentRevocations.TryGetValue(consentAgreement.ConsentAgreementConsentID, out var revocationsOfSameConsent);

            WriteConsent(consentInfo, consentArchiveInfo, agreementsOfSameConsentContent, revocationsOfSameConsent);
        }
    }

    private void WriteConsent(ConsentInfo consentInfo, ConsentArchiveInfo? consentArchiveInfo,
        IEnumerable<ConsentAgreementInfo> consentAgreements,
        IEnumerable<ConsentAgreementInfo>? consentRevocations)
    {
        personalDataWriter.WriteStartSection(ConsentInfo.OBJECT_TYPE, "Consent");

        var agreedConsentLastModified =
            consentArchiveInfo?.ConsentArchiveLastModified ?? consentInfo.ConsentLastModified;

        WriteConsentContent(consentInfo, consentArchiveInfo);
        WriteConsentAgreements(consentAgreements);
        WriteConsentRevocations(consentRevocations?.Where(cr => cr.ConsentAgreementTime > agreedConsentLastModified));

        personalDataWriter.WriteEndSection();
    }

    private void WriteConsentContent(ConsentInfo consentInfo, ConsentArchiveInfo? consentArchiveInfo)
    {
        if (consentArchiveInfo == null)
        {
            personalDataWriter.WriteBaseInfo(consentInfo, consentInfoColumns, TransformConsentText);
        }
        else
        {
            personalDataWriter.WriteSectionValue("ConsentDisplayName", "Consent name", consentInfo.ConsentDisplayName);
            personalDataWriter.WriteBaseInfo(consentArchiveInfo, consentArchiveInfoColumns, TransformConsentText);
        }
    }

    private void WriteConsentAgreements(IEnumerable<ConsentAgreementInfo> consentAgreements)
    {
        foreach (var consentAgreement in consentAgreements)
        {
            personalDataWriter.WriteBaseInfo(consentAgreement, consentAgreementInfoColumns, TransformConsentAction);
        }
    }

    private void WriteConsentRevocations(IEnumerable<ConsentAgreementInfo>? consentRevocations)
    {
        if (consentRevocations == null)
        {
            return;
        }

        foreach (var consentRevocation in consentRevocations)
        {
            personalDataWriter.WriteBaseInfo(consentRevocation, consentAgreementInfoColumns, TransformConsentAction);
        }
    }

    private void WriteContactActivities(IEnumerable<ActivityInfo> contactActivities)
    {
        foreach (var contactActivityInfo in contactActivities)
        {
            personalDataWriter.WriteStartSection(ActivityInfo.OBJECT_TYPE, "Activity");
            personalDataWriter.WriteBaseInfo(contactActivityInfo, activityInfoColumns);
            personalDataWriter.WriteEndSection();
        }
    }

    private void WriteContactAccounts(ICollection<int> contactIDs)
    {
        var accountIDs = accountContactInfoProvider.Get()
            .WhereIn("ContactID", contactIDs)
            .Column("AccountID")
            .Distinct();
        var accountInfos = accountInfoProvider.Get()
            .Columns(accountInfoColumns.Select(t => t.Name))
            .WhereIn("AccountID", accountIDs)
            .ToList();

        var countryInfos = countryInfoProvider.Get()
            .WhereIn("CountryID", accountInfos.Select(r => r.AccountCountryID).ToList())
            .ToDictionary(ci => ci.CountryID);
        var stateInfos = stateInfoProvider.Get()
            .WhereIn("StateID", accountInfos.Select(r => r.AccountStateID).ToList())
            .ToDictionary(si => si.StateID);

        foreach (var accountInfo in accountInfos)
        {
            countryInfos.TryGetValue(accountInfo.AccountCountryID, out var countryInfo);
            stateInfos.TryGetValue(accountInfo.AccountStateID, out var stateInfo);

            personalDataWriter.WriteStartSection(AccountInfo.OBJECT_TYPE, "Account");

            personalDataWriter.WriteBaseInfo(accountInfo, accountInfoColumns);

            if (countryInfo != null)
            {
                personalDataWriter.WriteBaseInfo(countryInfo, countryInfoColumns);
            }

            if (stateInfo != null)
            {
                personalDataWriter.WriteBaseInfo(stateInfo, stateInfoColumns);
            }

            personalDataWriter.WriteEndSection();
        }
    }

    private void WriteContactGroups(IEnumerable<ContactGroupInfo> contactContactGroups)
    {
        foreach (var contactGroupInfo in contactContactGroups)
        {
            personalDataWriter.WriteStartSection(ContactGroupInfo.OBJECT_TYPE, "Contact group");
            personalDataWriter.WriteBaseInfo(contactGroupInfo, contactGroupInfoColumns);
            personalDataWriter.WriteEndSection();
        }
    }

    private void WriteSubmittedFormsData(ICollection<string> emails, ICollection<int> contactIDs)
    {
        var consentAgreementGuids = consentAgreementInfoProvider.Get()
            .Columns("ConsentAgreementGuid")
            .WhereIn("ConsentAgreementContactID", contactIDs);

        var formClasses = bizFormInfoProvider.Get()
            .Source(s => s.InnerJoin<DataClassInfo>("CMS_Form.FormClassID", "ClassID"))
            .WhereIn("FormGUID", forms.Keys);

        formClasses.ForEachRow(row =>
        {
            var bizForm = new BizFormInfo(row);
            var formDefinition = forms[bizForm.FormGUID];

            var bizFormItems = formCollectionService.GetBizFormItems(emails, consentAgreementGuids, row, formDefinition);

            foreach (var bizFormItem in bizFormItems)
            {
                personalDataWriter.WriteStartSection("SubmittedForm", "Submitted form");

                personalDataWriter.WriteSectionValue("FormDisplayName", "Form display name", bizForm.FormDisplayName);
                personalDataWriter.WriteSectionValue("FormGUID", "Form GUID", bizForm.FormGUID.ToString());
                personalDataWriter.WriteBaseInfo(bizFormItem, formDefinition.FormColumns);

                personalDataWriter.WriteEndSection();
            }
        });
    }
}
```

### Implement the collector
The `ContactDataCollectorCore` handles all the heavy lifting, which means that the `IPersonalDataCollector` implementation can be relatively small.
  1. Define the `ContactDataCollector` class in the _DataProtection_ folder.
  2. Determine the correct `DataWriter` to use based on the `outputFomat` parameter provided to the `Collect` method by Xperience, and use it to instantiate a `DataCollectorCore` object.


C#
**ContactDataCollector.cs**
Copy
```
using CMS.DataEngine;
using CMS.DataProtection;
using TrainingGuides.Web.Features.DataProtection.Writers;

namespace TrainingGuides.Web.Features.DataProtection.Collectors;

public class ContactDataCollector : IPersonalDataCollector
{
    private readonly IServiceProvider serviceProvider;

    public ContactDataCollector(IServiceProvider serviceProvider)
    {
        this.serviceProvider = serviceProvider;
    }

    public PersonalDataCollectorResult Collect(IEnumerable<BaseInfo> identities, string outputFormat)
    {
        using var personalDataWriter = CreateWriter(outputFormat);

        var dataCollector = ActivatorUtilities.CreateInstance<ContactDataCollectorCore>(serviceProvider, personalDataWriter);

        return new PersonalDataCollectorResult
        {
            Text = dataCollector.CollectData(identities)
        };
    }

    private IPersonalDataWriter CreateWriter(string outputFormat) =>
        outputFormat.ToLowerInvariant() switch
        {
            PersonalDataFormat.MACHINE_READABLE => new XmlPersonalDataWriter(),
            _ => new HumanReadablePersonalDataWriter()
        };
}
```

### Register the Data collector
Register the data collector so that it is called by the system on the **Right to access** and **Data portability** tabs of the **Data protection** application in Xperience.
  1. Add the following line to the `OnInit` method of the `DataProtectionRegistrationModule` class.
C#
**DataProtectionRegistrationModule.cs**
Copy
```
...
// Adds the ContactDataCollector to the collection of registered personal data collectors
PersonalDataCollectorRegister.Instance.Add(ActivatorUtilities.CreateInstance<ContactDataCollector>(serviceProvider));
...
```

This code can be placed anywhere within the scope of the method, though it may be best to put it after the existing code from the previous guide in order to mirror the order in which the collectors are called.
  2. Notice that the CreateInstance method requires an `IServiceProvider` instance that you have not defined yet. Resolve this service at the start of your OnInit method.
C#
**DataProtectionRegistrationModule.cs**
Copy
```
...
var serviceProvider = parameters.Services.GetRequiredService<IServiceProvider>();
...
```



Now, the system will use this data collector on the **Data portability** and **Right to access** tabs of the **Data protection** application in the **Configuration** section of the Xperience administration interface.
If you search the email address of an existing contact on one of these tabs, Xperience will use this data collector, in conjunction with the identity collector from [earlier in this series](guides/development/data-protection/identity-collectors-gather-contacts-associated-with-a-visitor), to retrieve data associated with that contact. See an example in the following video:
Your browser does not support the video tag. 
## What’s next?
The [next guide in this series](guides/development/data-protection/data-erasers-clear-contact-information-from-the-site) will cover the process of creating a _Data eraser_ for removing visitors’ personal data from the site.