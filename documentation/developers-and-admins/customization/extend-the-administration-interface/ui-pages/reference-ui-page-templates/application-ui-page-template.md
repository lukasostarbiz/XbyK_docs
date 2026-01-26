---
source: https://docs.kentico.com/documentation/developers-and-admins/customization/extend-the-administration-interface/ui-pages/reference-ui-page-templates/application-ui-page-template
scrape_date: 2026-01-26
---

  * [Home](/documentation)
  * [Developers and admins](/documentation/developers-and-admins)
  * [Customization](/documentation/developers-and-admins/customization)
  * [Extend the administration interface](/documentation/developers-and-admins/customization/extend-the-administration-interface)
  * [UI pages](/documentation/developers-and-admins/customization/extend-the-administration-interface/ui-pages)
  * [Reference - UI page templates](/documentation/developers-and-admins/customization/extend-the-administration-interface/ui-pages/reference-ui-page-templates)
  * Application UI page template 


# Application UI page template
The application page serves as an entry point for [admin UI applications](/documentation/developers-and-admins/customization/extend-the-administration-interface/ui-pages/ui-application-pages). All root pages must inherit from the `ApplicationPage` base class. The page acts as a regular [UI page](/documentation/developers-and-admins/customization/extend-the-administration-interface/ui-pages), with one exception. When accessed, it automatically redirects to its first child page (as specified by the `order` property in child page registrations).
C#
**Creating an application page**
Copy
```
using Kentico.Xperience.Admin.Base;

public class UsersApplication : ApplicationPage
{
}
```

### Register application pages
Application pages serve as root pages for admin UI applications. For this reason, they are intended for use together with the `SECTION_LAYOUT` [client template](/documentation/developers-and-admins/customization/extend-the-administration-interface/ui-pages/reference-ui-page-templates/side-navigation-ui-page-template) to provide a navigation menu for application pages.
Register application pages using the `UIApplication` assembly attribute.
C#
**Example application registration**
Copy
```
using Kentico.Xperience.Admin.Base;
using Kentico.Xperience.Admin.Base.UIPages;

[assembly: UIApplication(
    identifier: UsersApplication.IDENTIFIER,
    type: typeof(UsersApplication),
    slug: "users",
    name: "Users",
    category: BaseApplicationCategories.DEVELOPMENT,
    icon: Icons.RectangleParagraph,
    templateName: TemplateNames.SECTION_LAYOUT)]
```

![]()
[]()[]()
