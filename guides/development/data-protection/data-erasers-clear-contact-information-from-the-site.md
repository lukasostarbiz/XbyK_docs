---
source: https://docs.kentico.com/guides/development/data-protection/data-erasers-clear-contact-information-from-the-site
scrape_date: 2026-01-22
---

  * [Home](/guides)
  * [Development](/guides/development)
  * [Data protection](/guides/development/data-protection)
  * Data erasers - Clear contact information from the site 


# Data erasers - Clear contact information from the site
Some data protection laws require site owners to comply when visitors request that their personal data be removed from the application. Data erasers are used to delete or anonymize a visitor’s data. They are called from the **Right to be forgotten** tab in the **Data protection** app of the Xperience by Kentico administration. Let’s examine the process of writing a data eraser.
## Before you start
This guide requires the following:
  * Familiarity with [C#](https://learn.microsoft.com/en-us/dotnet/csharp/), [.NET Core](https://learn.microsoft.com/en-us/dotnet/), [Dependency injection](https://learn.microsoft.com/en-us/dotnet/core/extensions/dependency-injection), and the [MVC pattern](https://learn.microsoft.com/en-us/aspnet/core/mvc/overview).
  * A running instance of Xperience by Kentico, preferably [30.11.1](/documentation/changelog) or higher. 
Some features covered in the training guides may not work in older versions. 


The examples in this guide require that you:
  * Have followed along with the samples from the [earlier guides in the series](/guides/development/data-protection).


The example presented in this [Data protection guide series](/guides/development/data-protection) is a **valid implementation of data protection for[Contacts](/documentation/business-users/digital-marketing/contact-management)** in Xperience by Kentico. (Note that it does not cover the collection and erasure of [Members](/documentation/business-users/members) and their associated data.)
You can copy-paste the code samples into your own solution.
However, if you choose to do so, **make sure to consult your legal team** to determine whether the implementation, texts, and consent levels **meet the requirements of your region and market**.
**Code samples**
You can find a project with completed, working versions of code samples from this guide and others in the [finished branch](https://github.com/Kentico/xperience-by-kentico-training-guides/tree/finished) of the _Training guides_ repository.
The [main branch](https://github.com/Kentico/xperience-by-kentico-training-guides) of the repository provides a starting point to code along with the guides.
The code samples in this guide are for [.NET 8](https://learn.microsoft.com/en-us/dotnet/core/whats-new/dotnet-8/overview) only.
They come from a project that uses [implicit using directives](https://learn.microsoft.com/en-us/dotnet/core/project-sdk/overview#implicit-using-directives). You may need to add additional `using` directives to your code if your project does not use this feature.
## Add a data eraser
This example’s data eraser is similar in some ways to the data collector from [earlier](/guides/development/data-protection/data-collectors-find-contact-personal-data#collect-data), but it differs in a few key ways. Firstly, you don’t need to choose between different types of data writers in a data eraser because you are deleting it rather than compiling it for display. This means you don’t need to move the erasure logic into a separate file, as with the data collector.
This data eraser will follow the same principle as the [documentation example](/documentation/developers-and-admins/data-protection/personal-data-erasure#create-the-data-eraser), but it will include additional options for deleting form data and form submission activities.
Unlike the data collector, this class does not need collections of `CollectedColumn` objects. It will simply delete the objects rather than display specific fields.
Alternatively, you can make a data eraser that only anonymizes objects (overwrites columns with personal identifying information with anonymous values) instead of deleting them. If you decide to anonymize certain object types, you can take a similar approach and specify which columns you want to anonymize.
  1. Create a new _Erasers_ folder in _TrainingGuides.Web/Features/DataProtection_ and add a new file called `ContactDataEraser.cs`.
  2. Use the `FormCollectionService` from [earlier](/guides/development/data-protection/data-collectors-find-contact-personal-data#collect-data) to retrieve forms and form submissions.
  3. Include methods to delete form submission activities, all activities, contacts, and submitted form data.
These methods correspond to the configuration options that users can choose on the **Right to be forgotten** tab of the **Data protection** application. You must check this configuration in each method to decide whether the object type should actually be deleted.


C#
**ContactDataEraser.cs**
Copy
```
using CMS.Activities;
using CMS.Base;
using CMS.ContactManagement;
using CMS.DataEngine;
using CMS.DataProtection;
using CMS.Helpers;
using CMS.OnlineForms;
using TrainingGuides.Web.Features.DataProtection.Collectors;
using TrainingGuides.Web.Features.DataProtection.Services;

namespace TrainingGuides.Web.Features.DataProtection.Erasers;

public class ContactDataEraser : IPersonalDataEraser
{
    private readonly IFormCollectionService formCollectionService;
    private readonly Dictionary<Guid, FormDefinition> forms;

    private readonly IInfoProvider<ContactInfo> contactInfoProvider;
    private readonly IInfoProvider<ActivityInfo> activityInfoProvider;
    private readonly IInfoProvider<ConsentAgreementInfo> consentAgreementInfoProvider;
    private readonly IInfoProvider<BizFormInfo> bizFormInfoProvider;

    public ContactDataEraser(IFormCollectionService formCollectionService,
        IInfoProvider<ContactInfo> contactInfoProvider,
        IInfoProvider<ActivityInfo> activityInfoProvider,
        IInfoProvider<ConsentAgreementInfo> consentAgreementInfoProvider,
        IInfoProvider<BizFormInfo> bizFormInfoProvider)
    {
        this.formCollectionService = formCollectionService;

        this.contactInfoProvider = contactInfoProvider;
        this.activityInfoProvider = activityInfoProvider;
        this.consentAgreementInfoProvider = consentAgreementInfoProvider;
        this.bizFormInfoProvider = bizFormInfoProvider;

        forms = this.formCollectionService.GetForms();
    }

    public void Erase(IEnumerable<BaseInfo> identities, IDictionary<string, object> configuration)
    {
        var contacts = identities.OfType<ContactInfo>().ToList();

        if (!contacts.Any())
        {
            return;
        }

        var contactIds = contacts.Select(c => c.ContactID).ToList();
        var contactEmails = contacts.Select(c => c.ContactEmail).ToList();

        using (new CMSActionContext())
        {
            DeleteSubmittedFormsActivities(contactIds, configuration);
            DeleteActivities(contactIds, configuration);
            DeleteContacts(contacts, configuration);
            DeleteSiteSubmittedFormsData(contactEmails, contactIds, configuration);
        }
    }

    private void DeleteSubmittedFormsActivities(ICollection<int> contactIds,
        IDictionary<string, object> configuration)
    {
        if (configuration.TryGetValue("DeleteSubmittedFormsActivities", out object? deleteSubmittedFormsActivities)
            && ValidationHelper.GetBoolean(deleteSubmittedFormsActivities, false))
        {
            activityInfoProvider.BulkDelete(new WhereCondition()
                .WhereEquals("ActivityType", PredefinedActivityType.BIZFORM_SUBMIT)
                .WhereIn("ActivityContactID", contactIds));
        }
    }

    private void DeleteSiteSubmittedFormsData(ICollection<string> emails, ICollection<int> contactIDs,
        IDictionary<string, object> configuration)
    {
        if (configuration.TryGetValue("DeleteSubmittedFormsData", out object? deleteSubmittedForms)
            && ValidationHelper.GetBoolean(deleteSubmittedForms, false))
        {
            var consentAgreementGuids = consentAgreementInfoProvider.Get()
                .Columns("ConsentAgreementGuid")
                .WhereIn("ConsentAgreementContactID", contactIDs);

            var formClasses = bizFormInfoProvider.Get()
                .Source(s => s.LeftJoin<DataClassInfo>("CMS_Form.FormClassID", "ClassID"))
                .WhereIn("FormGUID", forms.Select(pair => pair.Key).ToList());

            formClasses.ForEachRow(row =>
            {
                var bizForm = new BizFormInfo(row);
                var formDefinition = forms[bizForm.FormGUID];

                var bizFormItems = formCollectionService.GetBizFormItems(emails, consentAgreementGuids, row, formDefinition);

                foreach (var bizFormItem in bizFormItems)
                {
                    bizFormItem.Delete();
                }
            });
        }
    }

    private void DeleteActivities(List<int> contactIds, IDictionary<string, object> configuration)
    {
        if (configuration.TryGetValue("DeleteActivities", out object? deleteActivities)
            && ValidationHelper.GetBoolean(deleteActivities, false))
        {
            activityInfoProvider.BulkDelete(
                new WhereCondition().WhereIn("ActivityContactID", contactIds));
        }
    }

    private void DeleteContacts(IEnumerable<ContactInfo> contacts, IDictionary<string, object> configuration)
    {
        if (configuration.TryGetValue("DeleteContacts", out object? deleteContacts) &&
            ValidationHelper.GetBoolean(deleteContacts, false))
        {
            foreach (var contactInfo in contacts.Where(contact => contact.ContactID > 0))
            {
                contactInfoProvider.Delete(contactInfo);
            }
        }
    }
}
```

Keep in mind that your eraser needs to clear or anonymize **all object types** that store or reference visitor data in any way to comply with data protection laws. This example does not cover any custom logic specific to your solution.
## Register the eraser
To complete this example, register the data eraser so that it is invoked by the system on the **Right to be forgotten** tab of the **Data protection** application in Xperience.
Open the `DataProtectionRegistrationModule.cs` file, and add the following line to the `OnInit` method.
C#
**DataProtectionRegistrationModule.cs**
Copy
```
...
// Adds the ContactDataEraser to the collection of registered personal data erasers
PersonalDataEraserRegister.Instance.Add(ActivatorUtilities.CreateInstance<ContactDataEraser>(serviceProvider));
...
```

This code will fit anywhere within the scope of the method, though it may be best to put it after the existing code from earlier in this series in order to mirror the ordering of the data protection UI and the order in which the data eraser is called in relation to the identity collector.
Now, you can open the **Data protection** application from the **Configuration** category of the administration interface. On the **Right to be forgotten** tab, you can enter the email of a known contact and choose which data to delete, and Xperience will use the `ContactDataEraser` to remove their information from the system, as shown in the video below:
Your browser does not support the video tag. 
![]()
[]()[]()
