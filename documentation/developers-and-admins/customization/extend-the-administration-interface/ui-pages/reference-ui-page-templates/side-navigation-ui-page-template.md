---
source: https://docs.kentico.com/documentation/developers-and-admins/customization/extend-the-administration-interface/ui-pages/reference-ui-page-templates/side-navigation-ui-page-template
scrape_date: 2026-01-26
---

  * [Home](/documentation)
  * [Developers and admins](/documentation/developers-and-admins)
  * [Customization](/documentation/developers-and-admins/customization)
  * [Extend the administration interface](/documentation/developers-and-admins/customization/extend-the-administration-interface)
  * [UI pages](/documentation/developers-and-admins/customization/extend-the-administration-interface/ui-pages)
  * [Reference - UI page templates](/documentation/developers-and-admins/customization/extend-the-administration-interface/ui-pages/reference-ui-page-templates)
  * Side navigation UI page template 


# Side navigation UI page template
This template enables navigation within a single application via a navigation menu.
[![Navigation menu](/docsassets/documentation/side-navigation-ui-page-template/SideNavigation.png)](/docsassets/documentation/side-navigation-ui-page-template/SideNavigation.png)
The navigation menu:
  * is included by specifying the `SECTION_LAYOUT` client template for a page.
  * lists only the **direct descendants** of the page that uses the `SECTION_LAYOUT` client template.
  * can nest pages further down the hierarchy via pages that also use the `SECTION_LAYOUT` template on each level.
  * orders pages according to the `order` property of the `UIPage` [registration attribute](/documentation/developers-and-admins/customization/extend-the-administration-interface/ui-pages).
  * is not displayed for a specific level if there is only one page on that level.


When a page that uses the `SECTION_LAYOUT` template is accessed, it automatically displays its first child page (according to the order specified during registration).
You can prevent pages from being displayed in the navigation via the `UINavigation` attribute. See [UI pages](/documentation/developers-and-admins/customization/extend-the-administration-interface/ui-pages) for details. 
The system provides two base classes that can be used to introduce the navigation element into an application:
  1. [ApplicationPage](#admin-ui-application)
  2. [SecondaryMenuSectionPage](#custom-page)


### Admin UI application
A common use case for this template is as an entry point into an [admin UI application](/documentation/developers-and-admins/customization/extend-the-administration-interface/ui-pages/ui-application-pages).
  1. Create a [new UI page](/documentation/developers-and-admins/customization/extend-the-administration-interface/ui-pages) that inherits from the `ApplicationPage` base class.
  2. Register the new application using the `UIApplication` assembly attribute.
  3. Set the client-side page template to `TemplateNames.SECTION_LAYOUT`.


C#
**Add navigation to a new admin UI application**
Copy
```
using Kentico.Xperience.Admin.Base;

[assembly: UIApplication(
    identifier: IDENTIFIER,
    type: typeof(CustomApplication),
    slug: "customapp",
    name: "Custom application",
    category: "MyCategory",
    icon: Icons.Apple,
    templateName: TemplateNames.SECTION_LAYOUT)]

// The root page of a custom admin UI application
public class CustomApplication : ApplicationPage
{
    // Unique identifier of the application
    public const string IDENTIFIER = "Acme.CustomApplication";
}
```

All UI pages registered as **immediate** descendants of this page are now listed within a navigation menu on the left-hand side. If the parent page only has a single child (meaning the menu would only contain one item), the navigation is not displayed.
If you wish to include pages deeper within the application in the navigation menu (i.e., children of children) as well or include navigation for only a specific subsection of pages, see [Custom page](#custom-page).
### Custom page
If you need to provide navigation for pages deeper within the hierarchy, create a UI page that inherits from `SecondaryMenuSectionPage` and uses the `TemplateNames.SECTION_LAYOUT` template.
You can use such pages in one of two ways:
  1. Set the page’s parent as one of the pages already listed in the application’s navigation menu (included either by an `ApplicationPage` or another `SecondaryMenuSectionPage` higher in the hierarchy).  
The template adds another level to the side navigation menu. You can repeat this approach to nest the navigation to an arbitrary depth.
For example:
C#
**Add navigation to a specific page**
Copy
```
using Kentico.Xperience.Admin.Base;

// Registers CustomPage as a child of UserList and assigns the SECTION_LAYOUT client-side template to the page.
// All pages registered as children of CustomPage now appear in the navigation menu as 'children' of UserList
[assembly: UIPage(
    parentType: typeof(UserList),
    slug: "custompage",
    uiPageType: typeof(CustomPage),
    name: "Custom menu",
    templateName: TemplateNames.SECTION_LAYOUT,
    order: UIPageOrder.NoOrder)]

// The root page of a custom admin UI section
public class CustomPage : SecondaryMenuSectionPage
{
}
```

Note that if the parent page only has a single child (meaning the menu would only contain one item), the navigation is not displayed.
Using this approach, you achieve a hierarchical menu similar to the one that can be found in, for example, the _Contact management_ application.
[![Side navigation](/docsassets/documentation/side-navigation-ui-page-template/SideNavigation.png)](/docsassets/documentation/side-navigation-ui-page-template/SideNavigation.png)
  2. If the application doesn’t have top-level navigation and you wish to include navigation for specific subpages, set the page’s parent to the desired application page. Direct children of the navigation page then show up in a new navigation menu displayed only for the chosen section of the application.


![]()
[]()[]()
