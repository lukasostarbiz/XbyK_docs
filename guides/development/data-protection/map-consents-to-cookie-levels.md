# Map consents to cookie levels
  * [ Copy page link ](guides/development/data-protection/map-consents-to-cookie-levels#) | [Get HelpService ID](guides/development/data-protection/map-consents-to-cookie-levels#)
Core MVC 5


[✖](guides/development/data-protection/map-consents-to-cookie-levels# "Close page link panel") [Copy to clipboard](guides/development/data-protection/map-consents-to-cookie-levels#)
Laws relating to personal data protection have come into effect in many places worldwide. In many cases, an essential step to comply with such regulations is to attain a [consent agreement](documentation/developers-and-admins/data-protection/consent-management) from an individual to store and process their personal data.
In some cases, when properly explained, consent for storing browser cookies in the visitor’s browser files can be sufficient for collecting and processing the data associated with those cookies’ functionality.
This example shows the process of creating a custom page in the Xperience administration interface that maps custom cookie levels to [consents](documentation/developers-and-admins/data-protection/consent-management). With this page, editors can define which consents in the Data protection application are associated with specific cookie levels. This is a key step in creating a cookie-level configuration page, e.g. the [cookie policy page](https://www.kentico.com/cookies-policy) on [Kentico.com](http://Kentico.com).
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
## Create the consents
First, open the **Data protection** application in the **Configuration** category of the administration interface for Xperience by Kentico, and create consents with the following properties:
  1. Preference cookies consent 
    1. **New consent**
       * **Display name:** Preference cookies
       * **Code name:** preference.cookies.consent
[![Screenshot of consent create page](docsassets/guides/map-consents-to-cookie-levels/ConsentCreate.png)](https://docs.kentico.com/docsassets/guides/map-consents-to-cookie-levels/ConsentCreate.png)
    2. **Consent texts**
       * **Short text:** Preference cookies help us remember changes and configurations you make on the site.
       * **Full text:** Preference cookies and the data you provide us make your work easier by, for example, remembering the language in which you want to display the pages, etc.
[![Screenshot of consent text editing page](docsassets/guides/map-consents-to-cookie-levels/ConsentText.png)](https://docs.kentico.com/docsassets/guides/map-consents-to-cookie-levels/ConsentText.png)
  2. Analytical cookies consent 
    1. **New consent**
       * **Display name:** Analytical cookies
       * **Code name:** analytical.cookies.consent
    2. **Consent texts**
       * **Short text:** Analytical cookies are used to gather usage data to measure and improve performance.
       * **Long text:** Analytical cookies and the data you provide us allow us to perform site usage analytics to measure and improve the site’s performance. For example, we know which pages are most frequently visited, which buttons users click, etc.
  3. Marketing cookies consent 
    1. **New consent**
       * **Display name:** Marketing cookies
       * **Code name:** marketing.cookies.consent
    2. **Consent texts**
       * **Short text:** Marketing cookies are used to provide relevant ads based on your activity.
       * **Long text:** Marketing cookies and the data you provide us allow us to link our website to third-party social media and advertising networks, such as Facebook or Google Ads. With this connection, we can present relevant ads outside our website.


In real-world applications, your legal team should review the short and long texts of your consents to ensure that they meet the requirements of your region and market.
## Define the module
### Create the class
Now that these consents exist, we can create a UI page for mapping them to cookie levels.
  1. Open the **Modules** application in the administration interface for Xperience by Kentico, under the **Development** category.
  2. Create a new module for adding custom pages to the _Data protection_ application. 
    1. **Display name:** Data protection customizations
    2. **Pre-fill code name automatically:** False (Disabled)
    3. **Code name:** TrainingGuides.DataProtectionCustomizations
[![Screenshot of the module creation page](docsassets/guides/map-consents-to-cookie-levels/DataProtectionCustomizationsModule.png)](https://docs.kentico.com/docsassets/guides/map-consents-to-cookie-levels/DataProtectionCustomizationsModule.png)
  3. On the **Classes** tab of this new module, create a new class. 
    1. **Display name:** Cookie level consent mapping
    2. **Namespace:** TrainingGuides
    3. **Name:** CookieLevelConsentMapping
[![Screenshot of the class creation page](docsassets/guides/map-consents-to-cookie-levels/CookieLevelConsentMappingClass.png)](https://docs.kentico.com/docsassets/guides/map-consents-to-cookie-levels/CookieLevelConsentMappingClass.png)
  4. On the **Database columns** tab in this new class, add new fields with the following properties. These fields will determine which consent corresponds to each cookie level. 
    1. Preference cookie consent field
       * **Field name:** PreferenceConsentCodeName
       * **Data type:** Object code names
       * **Required:** False (Disabled)
[![Screenshot of database fields editing page](docsassets/guides/map-consents-to-cookie-levels/CookieLevelConsentMappingDBFields.png)](https://docs.kentico.com/docsassets/guides/map-consents-to-cookie-levels/CookieLevelConsentMappingDBFields.png)
    2. Analytical cookie consent field
       * **Field name:** AnalyticalConsentCodeName
       * **Data type:** Object code names
       * **Required:** False (Disabled)
    3. Marketing cookie consent field
       * **Field name:** MarketingConsentCodeName
       * **Data type:** Object code names
       * **Required:** False (Disabled)
    4. Guid field
       * **Field name:** CookieLevelConsentMappingGuid
       * **Data type:** Unique identifier (GUID)
       * **Required:** True (Enabled)


### Add the UI form
Switch to the **UI forms** tab of the class.
  1. Populate the following properties accordingly: 
     * **Form display name:** Cookie level consent mapping
     * **Pre-fill code name automatically:** False (Disabled)
     * **Form name:** CookieLevelConsentMapping
[![Screenshot of UI form creation page](docsassets/guides/map-consents-to-cookie-levels/CookieLevelConsentMappingUIFormCreation.png)](https://docs.kentico.com/docsassets/guides/map-consents-to-cookie-levels/CookieLevelConsentMappingUIFormCreation.png)
  2. Save the form, and switch to the **Fields** tab.
  3. Create a field for each [previously specified consent columns](guides/development/data-protection/map-consents-to-cookie-levels#create-the-class) with the following properties: 
     * **Form component:** Object code names
     * **Initial value:** Select
     * **Object type:** CMS.Consent
     * **Maximum items:** 1
  4. The fields should have the following **Field caption** values, respectively: 
     * Preference cookies consent (level 2)
     * Analytical cookies consent (level 3)
     * Marketing cookies consent (level 4)


The fields defined within UI forms represent how the database columns will be represented in the admin UI
[![Screenshot of UI form editor](docsassets/guides/map-consents-to-cookie-levels/CookieLevelConsentMappingUIForm.png)](https://docs.kentico.com/docsassets/guides/map-consents-to-cookie-levels/CookieLevelConsentMappingUIForm.png)
### Generate the code
Next, designate code settings for the class to make it more compatible with other features.
  1. Switch to the **Code** tab of the class.
  2. Set the **Object type** to _TrainingGuides.CookieLevelConsentMapping_.
  3. Set the **Display name column** to _CookieLevelConsentMappingID_ , and set **Code name column** and **GUID column** to _CookieLevelConsentMappingGuid._
  4. Leave all the other properties un-set. 
**Readable names**
Typically, columns that hold human-readable data are used for Display names and code names of custom objects. This helps the people managing the objects find them in object listings and query them easily.
However, in this case, there is only going to be a single entry for this class, so there will be no listing page. There is no need for the class to have a readable display name because it is never used.
[![screenshot of the code tab of the CookieLevelConsentMapping class](docsassets/guides/map-consents-to-cookie-levels/CookieLevelConsentMappingClassCode.png)](https://docs.kentico.com/docsassets/guides/map-consents-to-cookie-levels/CookieLevelConsentMappingClassCode.png)


This completes the administration interface setup for the class, so now the code can be generated.
Now, you can generate a code file for the custom class as described on [this page](documentation/developers-and-admins/api/generate-code-files-for-system-objects).
  1. Switch the command line to the _TrainingGuides.Web_ directory.
  2. Run the `--kxp-codegen` tool, setting the location to the _TrainingGuides.Entities_ project and include only the _CookieLevelConsentMapping_ class.
CMD
Copy
```
dotnet run -- --kxp-codegen --type "Classes" --location "../TrainingGuides.Entities/{type}/{name}" --with-provider-class True --include "TrainingGuides.CookieLevelConsentMapping"
```

This will generate three code files in the _TrainingGuides.Entities/Classes/CookieLevelConsentMapping_ folder. 
If you use a script to automate code generation as described in [this guide](guides/development/get-started/automate-regular-tasks-with-powershell-scripts#generate-code-files), use the `--include` and `--exclude` parameters on multiple commands to generate classes that require dedicated provider classes separately from those that do not. 
    1. _CookieLevelConsentMappingInfo.generated.cs_ is the info class containing the definition for objects of this type.
    2. _CookieLevelConsentMappingInfoProvider.generated.cs_ is the provider class, which has methods to interface with the database and perform CRUD operations on data.
    3. _ICookieLevelConsentMappingInfoProvider.generated.cs_ is an interface that allows the provider to be injected with dependency injection.
  3. Add a subfolder named _Overrides_ to the  _~/Classes_ folder of the _TrainingGuides.Entities_ project.
  4. Create a partial class for `CookieLevelConsentMapping` and enable continuous integration.
**Partial class**
Continuous Integration could be enabled by modifying the TYPEINFO declaration in the generated class. However, this separate partial class ensures that the change will not be overwritten next time the code is generated.
C#
**CookieLevelConsentMappingInfoOverride.cs**
Copy
```
namespace TrainingGuides.DataProtectionCustomizations;

public partial class CookieLevelConsentMappingInfo
{
    static CookieLevelConsentMappingInfo()
    {
        TYPEINFO.ContinuousIntegrationSettings.Enabled = true;
    }
}
```

This configuration allows the cookie-level consent mapping to be shared among developers by including serialized data in source control.


## Add a page to the UI
### Set up the project
Now that the class, form, and files are in place, we can integrate them into the UI.
  1. Create a new Class Library project targeting your version of .NET under the _TrainingGuides_ solution. Name it _TrainingGuides.Admin._
  2. Install the _Kentico.Xperience.Admin_ and _Kentico.Xperience.Core_ NuGet packages for the project. 
Make sure to choose package versions that match the project’s version, indicated by the version of the _Kentico.Xperience.WebApp_ package in the primary web project, and the value of the _CMSDBVersion_ settings key in the _CMS_SettingsKey_ table of the database.
  3. Add a reference from the _TrainingGuides.Admin_ project to the _TrainingGuides.Entities_ project, and a reference from the _TrainingGuides.Web_ project to the _TrainingGuides.Admin_ project.
  4. Add the assembly attribute to the _TrainingGuides.Admin.csproj_ file:


XML
**TrainingGuides.Admin.csproj**
Copy
```
...
<ItemGroup>
    <AssemblyAttribute Include="CMS.AssemblyDiscoverableAttribute">
    </AssemblyAttribute>
</ItemGroup>
...
```

This attribute will allow the files in the _TrainingGuides.Admin_ project to be discovered by the Xperience API during app initialization.
### Add the page
  1. Add a folder called _Pages_ to the _TrainingGuides.Admin_ project, and create a new class file called `CookieLevelConsentMappingPage.cs` within. This page will be used to edit objects of the `CookieLevelConsentMapping` class. 
     * Make the class inherit from `InfoEditPage<CookieLevelConsentMappingInfo>`. (You can find out more information about editing UI pages in the [documentation](documentation/developers-and-admins/customization/extend-the-administration-interface/ui-pages/reference-ui-page-templates/edit-ui-page-template).)
  2. Register the page as a child of the `DataProtectionContentLanguage` page in the administration interface. 
    1. Set the slug property to  _cookielevelconsentmapping_ , so that the URL of the page in the admin UI clearly indicates its purpose.
    2. Assign the _Edit_ template, so that Xperience knows this page is used to edit objects of the _CookieLevelConsentMapping_ type.
    3. Set the page order of `UIPageOrder.First + 1`, so that it is the second page after the **Consents** listing page.
  3. Override the `ObjectId` property of the `InfoEditPage` class, making sure that its `get` accessor will either: 
     * Return the ID of the first available `CookieLevelConsentMappingInfo` object,  
or
     * Create and save a new `CookieLevelConsentMappingInfo` object if none exists.
Overriding the `ObjectId` property ensures that only one object of the `CookieLevelConsentMapping` type will ever exist, assuming no additional code to create them is added.
  4. Override the `ConfigurePage` method and set `PageConfiguration.UIFormName` to the _CookieLevelConsentMapping_ form codename before calling base.


C#
**CookieLevelConsentMappingPage.cs**
Copy
```
using TrainingGuides.Admin.Pages;
using TrainingGuides.DataProtectionCustomizations;
using Kentico.Xperience.Admin.Base;
using Kentico.Xperience.Admin.Base.Forms;
using Kentico.Xperience.Admin.DigitalMarketing.UIPages;

[assembly: UIPage(
    parentType: typeof(DataProtectionContentLanguage),
    slug: "cookie-level-consent-mapping",
    uiPageType: typeof(CookieLevelConsentMappingPage),
    name: "Cookie level consent mapping",
    templateName: TemplateNames.EDIT,
    order: UIPageOrder.First + 1)]

namespace TrainingGuides.Admin.Pages;

internal class CookieLevelConsentMappingPage : InfoEditPage<CookieLevelConsentMappingInfo>
{
    private readonly ICookieLevelConsentMappingInfoProvider cookieLevelConsentMappingInfoProvider;

    public CookieLevelConsentMappingPage(IFormComponentMapper formComponentMapper, IFormDataBinder formDataBinder,
    ICookieLevelConsentMappingInfoProvider generalSettingsInfoProvider) : base(formComponentMapper, formDataBinder)
    {
        cookieLevelConsentMappingInfoProvider = generalSettingsInfoProvider;
    }


    public override int ObjectId
    {
        get
        {
            var mappings = GetOrCreateMappings();
            return mappings.CookieLevelConsentMappingID;
        }
        set => throw new InvalidOperationException("The $ObjectId value cannot be set.");
    }


    public override Task ConfigurePage()
    {
        PageConfiguration.UIFormName = "cookielevelconsentmapping";

        return base.ConfigurePage();
    }


    private CookieLevelConsentMappingInfo GetOrCreateMappings()
    {
        var item = cookieLevelConsentMappingInfoProvider.Get()?.FirstOrDefault();

        if (item == null)
        {
            item = new CookieLevelConsentMappingInfo
            {
                CookieLevelConsentMappingGuid = Guid.NewGuid()
            };
            cookieLevelConsentMappingInfoProvider.Set(item);
        }

        return item;
    }
}
```

When you rebuild the solution, you will see a new UI page in the **Data protection** application. Editors can use this page to map consents to the Preference, Analytical, and Marketing levels.
## Map the consents
The new page can be used to save cookie consent mappings.
  1. Navigate to the new page under the **Cookie level consent mapping** tab of the **Data protection** application in the Xperience administration interface.
  2. Select the corresponding consent, created in previous steps, for each cookie level.


[![Screenshot of newly-created UI page in the Data protection app](docsassets/guides/map-consents-to-cookie-levels/ConsentMappingResult.png)](https://docs.kentico.com/docsassets/guides/map-consents-to-cookie-levels/ConsentMappingResult.png)
Now, you can retrieve the mapping using the provider classes saved earlier. 
**Protect the mappings**
Some businesses may find it pertinent to remove the risk of editors accidentally deleting a consent that is used in the mapping page.
This can be achieved using [object events](documentation/developers-and-admins/customization/handle-global-events/reference-global-system-events). You can assign handlers to the `ConsentInfo.TYPEINFO.Events.Delete.Before` and `ConsentInfo.TYPEINFO.Events.BulkDelete.Before` events, to throw an exception if any of the consents involved are referenced by the cookie-level mapping.
You can closely follow [this example](documentation/developers-and-admins/customization/handle-global-events/handle-object-events) to register the handlers and compare the codenames of the provided consents to those in the properties of the singular `CookieLevelConsentMappingInfo` object.
## What’s next?
The [next guide in this series](guides/development/data-protection/create-a-cookie-preferences-widget) will show you how to create a cookie-level configuration widget using the mapping created here. With this widget, editors can create a configuration page that allows visitors to choose the level of cookies they agree to.