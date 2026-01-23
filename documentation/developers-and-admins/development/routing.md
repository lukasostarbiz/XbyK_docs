---
source: https://docs.kentico.com/documentation/developers-and-admins/development/routing
scrape_date: 2026-01-22
---

  * [Home](/documentation)
  * [Developers and admins](/documentation/developers-and-admins)
  * [Development](/documentation/developers-and-admins/development)
  * Routing 


# Routing
Xperience sites respond to incoming requests using conventional ASP.NET routing. ASP.NET routing is a pattern matching system that is responsible for mapping incoming browser requests to specified controller actions. When the application launches, it registers one or more patterns within the framework’s route table to tell the routing engine what to do with any requests that match those patterns.
Xperience uses a routing approach that leverages the site’s [content tree](/documentation/business-users/website-content). With content tree-based routing, the system automatically generates and matches page URLs based on their position in the site’s content tree. You don’t need to manually map any MVC route templates. All routing logic is handled for you by the system.
In addition to the default system URLs generated from the content tree, marketers can also specify custom [vanity URLs](/documentation/business-users/website-content/manage-page-urls#edit-vanity-urls-of-pages) to create short and memorable URLs for pages for branding and easy sharing.
For more advanced routing scenarios, you can implement custom controllers and actions that take over the handling for specific requests, allowing you to customize the routing process. See [Content tree-based routing](/documentation/developers-and-admins/development/routing/content-tree-based-routing) for more information.
![]()
[]()[]()
