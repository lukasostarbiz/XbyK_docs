---
source: https://docs.kentico.com/documentation/developers-and-admins/customization/extend-the-administration-interface/ui-pages/ui-application-pages
scrape_date: 2026-01-22
---

  * [Home](/documentation)
  * [Developers and admins](/documentation/developers-and-admins)
  * [Customization](/documentation/developers-and-admins/customization)
  * [Extend the administration interface](/documentation/developers-and-admins/customization/extend-the-administration-interface)
  * [UI pages](/documentation/developers-and-admins/customization/extend-the-administration-interface/ui-pages)
  * UI application pages 


# UI application pages
UI application pages are [UI pages](/documentation/developers-and-admins/customization/extend-the-administration-interface/ui-pages) that encapsulate a UI page hierarchy with specific functionality under an **application**.
Every application in the admin UI is contained within an application page that serves as the app’s entry point and the root for its UI page subtree.
Application pages:
  * must inherit from the [ApplicationPage base class](#create-ui-application-pages)
  * be assigned to a [category](#ui-application-categories)
  * be [registered](#register-ui-application-pages) using the `UIApplication` attribute
  * have their [permissions defined](/documentation/developers-and-admins/customization/extend-the-administration-interface/ui-pages/ui-page-permission-checks)


## Create UI application pages
UI application pages must inherit from the `ApplicationPage` base class.
C#
**Create new UI application pages**
Copy
```
using Kentico.Xperience.Admin.Base;

public class CustomApplication : ApplicationPage
{
    // Unique identifier of the application
    public const string IDENTIFIER = "Acme.CustomApplication";
}
```

Application pages don’t need to display any content or visuals. The `ApplicationPage` base class ensures all requests targeting the page get redirected to their first registered child page. 
## UI application categories
Each application page must be assigned to an application category during registration. Moreover, the categories themselves need to be registered.
Register new categories via the `UIApplication` assembly attribute:
C#
**Registering a custom application category**
Copy
```
using Kentico.Xperience.Admin.Base;

[assembly: UICategory(
    codeName: "category.code.name",
    name: "Category name",
    icon: Icons.Cogwheel,
    order: 100)]
```

When registering new application pages, you can assign them to one of the predefined system categories – visible in the left-hand vertical menu in the admin UI – or create a custom category. Application pages are assigned to categories via the **category code name**.
### Predefined categories
The code names of default system categories are stored as constants in:
  * Kentico.Xperience.Admin.Base.UIPages.BaseApplicationCategories
  * Kentico.Xperience.Admin.DigitalMarketing.UIPages.DigitalMarketingApplicationCategories
  * Kentico.Xperience.Admin.Content.UIPages.ContentApplicationCategories


## Register UI application pages
To register application pages into the system, use the `UIApplication` assembly attribute. Supply the following parameters:
  * `identifier` – a unique identifier string for the application. Used in the data stored when assigning permission sets for the application to [roles](/documentation/developers-and-admins/configuration/users/role-management).
  * `type` – the type implementing the page (inheriting from `ApplicationPage`).
  * `slug` – the URL segment under which the application is available. Applications are always accessible directly under the admin UI’s root: _< domain>/admin/<UIApplicationSlug>_
  * `name` – the name of the application page.
  * `category` – the category to which the application is assigned.
  * `icon` – the icon displayed for the application in the administration application menu and dashboard.
  * `templateName` – the name of the client template used by the page. Application pages **should** use the `SECTION_LAYOUT` [client template](/documentation/developers-and-admins/customization/extend-the-administration-interface/ui-pages/reference-ui-page-templates/side-navigation-ui-page-template) to display a navigation menu for their child pages.


C#
**UI application page registration**
Copy
```
using Kentico.Xperience.Admin.Base;

[assembly: UIApplication(
    identifier: CustomApplication.IDENTIFIER,
    type: typeof(CustomApplication),
    slug: "my-application",
    name: "Custom admin UI app",
    category: BaseApplicationCategories.DEVELOPMENT,
    icon: Icons.Cogwheel,
    templateName: TemplateNames.SECTION_LAYOUT)]
```

## Assign permissions to UI application pages
To define the set of [UI permissions](/documentation/developers-and-admins/configuration/users/role-management) available to and evaluated by the given application, see [UI page permission checks](/documentation/developers-and-admins/customization/extend-the-administration-interface/ui-pages/ui-page-permission-checks).
![]()
[]()[]()
