---
source: https://docs.kentico.com/modules/data-protection/implement-cookie-consent-module
scrape_date: 2026-01-22
---

Module: Data protection
3 of 13 Pages
# Implement the cookie consent module
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
     * Make the class inherit from `InfoEditPage<CookieLevelConsentMappingInfo>`. (You can find out more information about editing UI pages in the [documentation](/documentation/developers-and-admins/customization/extend-the-administration-interface/ui-pages/reference-ui-page-templates/edit-ui-page-template).)
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
using CMS.DataEngine;
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
    private readonly IInfoProvider<CookieLevelConsentMappingInfo> cookieLevelConsentMappingInfoProvider;

    public CookieLevelConsentMappingPage(IFormComponentMapper formComponentMapper, IFormDataBinder formDataBinder,
    IInfoProvider<CookieLevelConsentMappingInfo> generalSettingsInfoProvider) : base(formComponentMapper, formDataBinder)
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


[![Screenshot of newly-created UI page in the Data protection app](/docsassets/guides/map-consents-to-cookie-levels/ConsentMappingResult.png)](/docsassets/guides/map-consents-to-cookie-levels/ConsentMappingResult.png)
Now, you can retrieve the mapping using the provider classes saved earlier. 
**Protect the mappings**
Some businesses may find it pertinent to remove the risk of editors accidentally deleting a consent that is used in the mapping page.
This can be achieved using [object events](/documentation/developers-and-admins/customization/handle-global-events/reference-global-system-events). You can assign handlers to the `ConsentInfo.TYPEINFO.Events.Delete.Before` and `ConsentInfo.TYPEINFO.Events.BulkDelete.Before` events, to throw an exception if any of the consents involved are referenced by the cookie-level mapping.
You can closely follow [this example](/documentation/developers-and-admins/customization/handle-global-events/handle-object-events) to register the handlers and compare the codenames of the provided consents to those in the properties of the singular `CookieLevelConsentMappingInfo` object.
[ Previous page ](/modules/data-protection/define-cookie-consent-module)
3 of 13
[ Mark complete and continue ](/modules/data-protection/create-cookie-consent-widget)
![]()
[]()[]()
