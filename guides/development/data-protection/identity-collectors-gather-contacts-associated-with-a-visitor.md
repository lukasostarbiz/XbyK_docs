---
source: https://docs.kentico.com/guides/development/data-protection/identity-collectors-gather-contacts-associated-with-a-visitor
scrape_date: 2026-01-22
---

  * [Home](/guides)
  * [Development](/guides/development)
  * [Data protection](/guides/development/data-protection)
  * Identity collectors - Gather contacts associated with a visitor 


# Identity collectors - Gather contacts associated with a visitor
Data protection laws such as the European Union’s GDPR often specify that visitors to a site must be able to see their personal data that has been collected, and they can request that it be removed. Identity collection is a key part of this process.
Let’s create an identity collector for gathering [Contacts](/documentation/business-users/digital-marketing/contact-management). Data protection laws such as the European Union’s GDPR often specify that visitors to a site must be able to see their personal data that has been collected, and they can request that it be removed. Identity collection is a key part of this process.
Let’s create an identity collector for gathering [Contacts](/documentation/business-users/digital-marketing/contact-management).
You can read more about GDPR compliance in the [Xperience by Kentico Documentation](/documentation/developers-and-admins/data-protection/gdpr-compliance).
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
The example presented in this [Data protection guide series](/guides/development/data-protection) is a **valid implementation of data protection for[Contacts](/documentation/business-users/digital-marketing/contact-management)** in Xperience by Kentico. (Note that it does not cover the collection and erasure of [Members](/documentation/business-users/members) and their associated data.)
You can copy-paste the code samples into your own solution.
However, if you choose to do so, **make sure to consult your legal team** to determine whether the implementation, texts, and consent levels **meet the requirements of your region and market**.
**Code samples**
You can find a project with completed, working versions of code samples from this guide and others in the [finished branch](https://github.com/Kentico/xperience-by-kentico-training-guides/tree/finished) of the _Training guides_ repository.
The [main branch](https://github.com/Kentico/xperience-by-kentico-training-guides) of the repository provides a starting point to code along with the guides.
The code samples in this guide are for [.NET 8](https://learn.microsoft.com/en-us/dotnet/core/whats-new/dotnet-8/overview) only.
They come from a project that uses [implicit using directives](https://learn.microsoft.com/en-us/dotnet/core/project-sdk/overview#implicit-using-directives). You may need to add additional `using` directives to your code if your project does not use this feature.
## Understand the components
In the **Configuration → Data Protection** application in _Xperience by Kentico_ , you’ll notice tabs for **Data portability** , **Right to access** , and **Right to be forgotten**.
These correspond to aspects of the GDPR but are applicable to other regulations as well, and each is used for dealing with visitors’ personal data.
The **Data portability** tab, once implemented, allows administrators to gather a visitor’s data in a machine-readable format. In contrast, the **Right to access** tab collects data in a human-readable format. Finally, the **Right to be forgotten** tab allows a visitor’s data to be wiped from the site.
However, the code that carries out these actions needs to be implemented, as there is no one-size-fits-all solution to the virtually limitless ways a site could collect and store personal information. The code samples from this series cover just one example site with its own specific requirements. However, the code that carries out these actions needs to be implemented, as there is no one-size-fits-all solution to the virtually limitless ways a site could collect and store personal information. The code samples from this series cover just one example site with its own specific requirements.
## Identify the first step
Data collection and erasure require an _Identity collector_ as the first step.
_Identity collectors_ are used to gather a collection of Xperience objects representing the visitor, called Identities. For example, real-world people could be represented by a _User_ object, one or more _Contact_ objects, or any custom objects, such as _Customer_ , _Author_ , _Contractor_ , etc.
Once the identity collector gathers these identities, it passes them to other components. These components can use these identities to find objects that reference them, e.g. activities performed by a contact.
We will cover the creation of an identity collector, the first step of this process. We will cover the creation of an identity collector, the first step of this process.
Note: The system uses identifiers, such as an email address, to find Identity objects. It is critical that any data you collect and store always contains one of the following, so that it can be found.
  * a reference to one of the identity objects
  * the identifier itself


Consider an example where you use an email address as the identifier and create a form that collects the first and last name of a visitor but no email address. In such a case, there would be no way to find that information using a supplied email address, and GDPR compliance would be impossible when a visitor exercises their _right to be forgotten_.
## Implement the identity collector
This code sample will assume the site collects only contacts as identities — it does not account for visitors saved as [users](/documentation/developers-and-admins/configuration/users/user-management), [members](/documentation/business-users/members), or [custom objects](/documentation/developers-and-admins/customization/object-types).
  1. Create a _Collectors_ folder in _~/Features/DataProtection_ of the _TrainingGuides.Web_ project.
  2. Add a _ContactIdentityCollector.cs_ file to the folder, and add a namespace matching the folder structure (`TrainingGuides.Web.Features.DataProtection.Collectors`).
  3. Copy the [example identity collector from the documentation](/documentation/developers-and-admins/data-protection/personal-data-collection#create-the-identity-collector), and add it to the same namespace.
  4. Add a constant to replace the hard-coded `“email”` string, as it occurs more than once.
  5. Define additional logic that creates a new contact with the supplied email address if the query finds none.
It is possible for form data containing an email address to exist even if there is no contact in the database with that email. Creating this dummy contact allows the _Data collector_ , which will be covered in the next part of this series, to find form data associated with the provided email address in such a case.


C#
**ContactIdentityCollector.cs**
Copy
```
using CMS.ContactManagement;
using CMS.DataEngine;
using CMS.DataProtection;

namespace TrainingGuides.Web.Features.DataProtection.Collectors;

public class ContactIdentityCollector : IIdentityCollector
{
    private const string EMAIL_KEY = "email";

    private readonly IInfoProvider<ContactInfo> contactInfoProvider;

    public ContactIdentityCollector(IInfoProvider<ContactInfo> contactInfoProvider)
    {
        this.contactInfoProvider = contactInfoProvider;
    }

    public void Collect(IDictionary<string, object> dataSubjectFilter, List<BaseInfo> identities)
    {
        string? email = dataSubjectFilter.ContainsKey(EMAIL_KEY)
            ? dataSubjectFilter[EMAIL_KEY] as string
            : string.Empty;

        if (string.IsNullOrWhiteSpace(email))
        {
            return;
        }

        var contacts = contactInfoProvider
            .Get()
            .WhereEquals(nameof(ContactInfo.ContactEmail), email)
            .ToList();

        if (contacts.Count() == 0)
        {
            contacts.Add(new ContactInfo() { ContactEmail = email });
        }

        identities.AddRange(contacts);
    }
}
```

## Register the Identity collector
Now that the Identity collector is in place, it can be registered.
  1. Create a new `DataProtectionRegistrationModule` class in _~/Features/DataProtection/Shared_ folder of the _TrainingGuides.Web_ project.
  2. Inherit from Xperience’s `Module` class, and override `OnInit` to add `ContactIdentityCollector` to the `IdentityCollectorRegister`.
  3. Resolve an info provider for contacts, and pass it to the collector to fill the parameter we set earllier
  4. Register the module using assembly attribute.


C#
**DataProtectionRegistrationModule.cs**
Copy
```
using CMS;
using CMS.ContactManagement;
using CMS.Core;
using CMS.DataEngine;
using CMS.DataProtection;
using TrainingGuides.Web.Features.DataProtection.Collectors;
using TrainingGuides.Web.Features.DataProtection.Shared;

[assembly: RegisterModule(
    type: typeof(DataProtectionRegistrationModule))]

namespace TrainingGuides.Web.Features.DataProtection.Shared;

public class DataProtectionRegistrationModule : Module
{
    public DataProtectionRegistrationModule()
        : base("DataProtectionRegistration")
    {
    }

    // Contains initialization code that is executed when the application starts
    protected override void OnInit(ModuleInitParameters parameters)
    {
        var contactInfoProvider = parameters.Services.GetRequiredService<IInfoProvider<ContactInfo>>();

        base.OnInit(parameters);

        // Adds the ContactIdentityCollector to the collection of registered identity collectors
        IdentityCollectorRegister.Instance.Add(new ContactIdentityCollector(contactInfoProvider));
    }
}
```

Now, Xperience will know how to utilize the `ContactIdentityCollector` class for data portability, collection, and erasure requests. When you implement a _Data collector_ , covered [later on in this series](/guides/development/data-protection/data-collectors-find-contact-personal-data#collect-data), Xperience will use the identity collector class to gather a list of contacts and pass them to the data collector to retrieve personal data associated with them. Now, Xperience will know how to utilize the `ContactIdentityCollector` class for data portability, collection, and erasure requests. When you implement a _Data collector_ , covered [later on in this series](/guides/development/data-protection/data-collectors-find-contact-personal-data#collect-data), Xperience will use the identity collector class to gather a list of contacts and pass them to the data collector to retrieve personal data associated with them.
The `IdentityCollectorRegister`, like the other data protection registers, is a queue that can contain multiple collectors. If the same class is registered to this queue more than once, it will be called multiple times.
## What’s next?
## What’s next?
The [following guide](/guides/development/data-protection/data-collectors-find-contact-personal-data) in this series will cover the process of creating a _Data collector_ and some utility classes to help it run smoothly.
![]()
[]()[]()
