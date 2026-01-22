---
source: https://docs.kentico.com/modules/data-protection/define-cookie-consent-module
scrape_date: 2026-01-22
---

Module: Data protection
2 of 13 Pages
# Define a module that maps cookie levels to consent
Laws relating to personal data protection have come into effect in many places worldwide. In many cases, an essential step to comply with such regulations is to attain a [consent agreement](/documentation/developers-and-admins/data-protection/consent-management) from an individual to store and process their personal data.
In some cases, when properly explained, consent for storing browser cookies in the visitor’s browser files can be sufficient for collecting and processing the data associated with those cookies’ functionality.
This example shows the process of creating a custom page in the Xperience administration interface that maps custom cookie levels to [consents](/documentation/developers-and-admins/data-protection/consent-management). With this page, editors can define which consents in the Data protection application are associated with specific cookie levels. This is a key step in creating a cookie-level configuration page, e.g. the [cookie policy page](https://www.kentico.com/cookies-policy) on [Kentico.com](http://Kentico.com).
## Create the consents
First, open the **Data protection** application in the **Configuration** category of the administration interface for Xperience by Kentico, and create consents with the following properties:
  1. Preference cookies consent 
    1. **New consent**
       * **Display name:** Preference cookies
       * **Code name:** preference.cookies.consent
[![Screenshot of consent create page](/docsassets/guides/map-consents-to-cookie-levels/ConsentCreate.png)](/docsassets/guides/map-consents-to-cookie-levels/ConsentCreate.png)
    2. **Consent texts**
       * **Short text:** Preference cookies help us remember changes and configurations you make on the site.
       * **Full text:** Preference cookies and the data you provide us make your work easier by, for example, remembering the language in which you want to display the pages, etc.
[![Screenshot of consent text editing page](/docsassets/guides/map-consents-to-cookie-levels/ConsentText.png)](/docsassets/guides/map-consents-to-cookie-levels/ConsentText.png)
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
[![Screenshot of the module creation page](/docsassets/guides/map-consents-to-cookie-levels/DataProtectionCustomizationsModule.png)](/docsassets/guides/map-consents-to-cookie-levels/DataProtectionCustomizationsModule.png)
  3. On the **Classes** tab of this new module, create a new class. 
    1. **Display name:** Cookie level consent mapping
    2. **Namespace:** TrainingGuides
    3. **Name:** CookieLevelConsentMapping
[![Screenshot of the class creation page](/docsassets/guides/map-consents-to-cookie-levels/CookieLevelConsentMappingClass.png)](/docsassets/guides/map-consents-to-cookie-levels/CookieLevelConsentMappingClass.png)
  4. On the **Database columns** tab in this new class, add new fields with the following properties. These fields will determine which consent corresponds to each cookie level. 
    1. Preference cookie consent field
       * **Field name:** PreferenceConsentCodeName
       * **Data type:** Object code names
       * **Required:** False (Disabled)
[![Screenshot of database fields editing page](/docsassets/guides/map-consents-to-cookie-levels/CookieLevelConsentMappingDBFields.png)](/docsassets/guides/map-consents-to-cookie-levels/CookieLevelConsentMappingDBFields.png)
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
[![Screenshot of UI form creation page](/docsassets/guides/map-consents-to-cookie-levels/CookieLevelConsentMappingUIFormCreation.png)](/docsassets/guides/map-consents-to-cookie-levels/CookieLevelConsentMappingUIFormCreation.png)
  2. Save the form, and switch to the **Fields** tab.
  3. Create a field for each [previously specified consent columns](#create-the-class) with the following properties: 
     * **Form component:** Object code names
     * **Initial value:** Select
     * **Object type:** CMS.Consent
     * **Maximum items:** 1
  4. The fields should have the following **Field caption** values, respectively: 
     * Preference cookies consent (level 2)
     * Analytical cookies consent (level 3)
     * Marketing cookies consent (level 4)


The fields defined within UI forms represent how the database columns will be represented in the admin UI
[![Screenshot of UI form editor](/docsassets/guides/map-consents-to-cookie-levels/CookieLevelConsentMappingUIForm.png)](/docsassets/guides/map-consents-to-cookie-levels/CookieLevelConsentMappingUIForm.png)
### Generate the code
Next, designate code settings for the class to make it more compatible with other features.
  1. Switch to the **Code** tab of the class.
  2. Set the **Object type** to _TrainingGuides.CookieLevelConsentMapping_.
  3. Set the **Display name column** to _CookieLevelConsentMappingID_ , and set **Code name column** and **GUID column** to _CookieLevelConsentMappingGuid._
  4. Leave all the other properties un-set. 
**Readable names**
Typically, columns that hold human-readable data are used for Display names and code names of custom objects. This helps the people managing the objects find them in object listings and query them easily.
However, in this case, there is only going to be a single entry for this class, so there will be no listing page. There is no need for the class to have a readable display name because it is never used.
[![screenshot of the code tab of the CookieLevelConsentMapping class](/docsassets/guides/map-consents-to-cookie-levels/CookieLevelConsentMappingClassCode.png)](/docsassets/guides/map-consents-to-cookie-levels/CookieLevelConsentMappingClassCode.png)


This completes the administration interface setup for the class, so now the code can be generated.
Now, you can generate a code file for the custom class as described on [this page](/documentation/developers-and-admins/api/generate-code-files-for-system-objects).
  1. Switch the command line to the _TrainingGuides.Web_ directory.
  2. Run the `--kxp-codegen` tool, setting the location to the _TrainingGuides.Entities_ project and include only the _CookieLevelConsentMapping_ class.
CMD
Copy
```
dotnet run -- --kxp-codegen --type "Classes" --location "../TrainingGuides.Entities/{type}/{name}" --with-provider-class "false" --include "TrainingGuides.CookieLevelConsentMapping"
```

This will generate a _CookieLevelConsentMappingInfo.generated.cs_ code file in the _TrainingGuides.Entities/Classes/CookieLevelConsentMapping_ folder.
**Info providers**
We recommend using the `--with-provider-class "false"` parameter to **avoid** generating a [dedicated provider](/documentation/developers-and-admins/customization/customize-system-providers) for your class.
You should use a [generic provider](/documentation/developers-and-admins/customization/customize-system-providers), based on `IInfoProvider<TInfo>` to access the objects of your class.
This is the best practice **unless** you specifically need to retrieve your objects in a context where **dependency injection is not yet available**. In such cases, use [`Provider<T>.Instance`](/documentation/developers-and-admins/api/database-table-api#generic-provider-class-iinfoprovidertinfo) to access the generic provider.
Only generate dedicated providers if dependency injection is not possible **and** using `Provider<T>.Instance` doesn’t work for your specific scenario (for example, if you need custom functionality or overrides in the provider class).
If you’ve previously generated classes that **do** require dedicated providers, use the [`--include` or `--exclude` parameters](/documentation/developers-and-admins/customization/object-types#generate-code-files) to ensure that the new command does not overwrite them.
  3. Add a subfolder named _Overrides_ to the _~/Classes_ folder of the _TrainingGuides.Entities_ project.
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


[ Previous page ](/modules/data-protection)
2 of 13
[ Mark complete and continue ](/modules/data-protection/implement-cookie-consent-module)
![]()
[]()[]()
