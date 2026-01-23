---
source: https://docs.kentico.com/documentation/developers-and-admins/configuration/website-channel-management/manage-multiple-websites
scrape_date: 2026-01-22
---

  * [Home](/documentation)
  * [Developers and admins](/documentation/developers-and-admins)
  * [Configuration](/documentation/developers-and-admins/configuration)
  * [Website channel management](/documentation/developers-and-admins/configuration/website-channel-management)
  * Manage multiple websites 


# Manage multiple websites
Having multiple website channels within a single Xperience by Kentico application offers several benefits. Firstly, it allows you to target diverse audience segments with tailored content and designs, improving user engagement and conversion rates. Secondly, it facilitates efficient content management by centralizing all web properties in one system, streamlining updates and reducing maintenance overhead.
This page provides best practices for **organizing the code of multiple channels within one Xperience project**.
The required level of code separation depends on the [content model](/guides/architecture/content-modeling/content-modeling-guide) used by the website channels. Most of the recommendations assume that each website channel uses an (almost) completely separate content model, i.e. a different set of [content types](/documentation/developers-and-admins/development/content-types) for each website channel. However, if you have multiple website channels with identical content models, code separation may not be needed at all, and you can develop a standard single project without any issues.
To implement multiple website channels:
  1. [Create and configure your website channels](/documentation/developers-and-admins/configuration/website-channel-management) in the administration interface.
  2. Set up separation of code files in your projects for the channels according to the recommendations below.
  3. Configure a [dedicated domain for the administration](/documentation/developers-and-admins/configuration/administration-domain-configuration) (separate from your website channel domains).


## Content types
Use a unique namespace in the code name of [content types](/documentation/developers-and-admins/development/content-types) related to a specific website channel. This prevents conflicts when implementing various features related to content types.
## Razor class libraries
Use [Razor class libraries](https://learn.microsoft.com/en-us/aspnet/core/razor-pages/ui-class) (RCLs) to separate code for individual website channels. Also consider implementing a separate RCL to store code files that are shared between projects.
For example, the Visual Studio solution could contain the following projects:
  * AcmeWebsite – RCL for the “Acme” website channel
  * DancingGoatWebsite – RCL for the “Dancing Goat” website channel
  * Shared – optional RCL for resources shared between the website channels (e.g., Page Builder components)
  * Xperience – the main web application project containing startup code, general system data, and configuration. For example: 
    * Startup and general system configuration (could be based on the Xperience _Boilerplate_ [project template](/documentation/developers-and-admins/installation))
    * Page Builder scripts and styles and their bundling configuration
    * Binary data of [content item assets](/documentation/business-users/content-hub/content-item-assets)


It is recommended to store related code files into [feature folders](https://medium.com/c2-group/simplifying-projects-with-a-folder-by-feature-structure-3a13cff2d28c) within the corresponding class libraries.
## Controllers
  * Store controllers in a separate RCL for each website channel.
  * For controllers used within [content tree-based routing](/documentation/developers-and-admins/development/routing/content-tree-based-routing), register the controllers for a specific content type and website channel in the `RegisterWebPageRoute` attribute. This prevents route conflicts between different website channels.
  * For controllers served by the default MVC routing (i.e., `{controller}/{action}`), ensure separation by adding website channel prefixes to the controller names when needed.


## Views
  * Depending how and where your views are used, we recommend storing them in: 
    * Website channel RCL – views used for the pages or components on a specific website channel.
    * Shared RCL – common views used and referenced across multiple website channels (e.g., error handling pages).
  * To avoid conflicts, always include a “channel name” or “common” folder in the view path. For example: _< Feature>/Views/AcmeWebsite/View.cshtml_
  * If a view is found in both an RCL and the main web application, the view in the web application takes precedence.
  * We recommend always using the full path to views (e.g., in controller action results).


## View components
  * Separate view components by using unique namespaces for each website channel.


## Static files
Separate static assets for each website channel:
  * Store static files (stylesheets, fonts, logos, favicon) for each website channel in the corresponding RCL, e.g., _< AcmeWebsite project>/assets_
  * You can add a [static web asset base path](https://learn.microsoft.com/en-us/aspnet/core/blazor/fundamentals/static-files?view=aspnetcore-7.0#static-web-asset-base-path) to the RCL to simplify asset paths: `<StaticWebAssetBasePath>/AcmeWebsite</StaticWebAssetBasePath>`


## Page Builder
[Page Builder](/documentation/developers-and-admins/development/builders/page-builder) components can be shared, depending on the implementation.
### Widgets and sections
  * We recommend storing the code of general components in a shared location. Components specific to a website channel can be stored in the corresponding RCL.
  * Restrict widgets and sections using the `AllowedWidgets` and `AllowedSections` parameters of the `EditableAreaOptions` object when [defining editable areas](/documentation/developers-and-admins/development/builders/page-builder/create-pages-with-editable-areas) on pages.


### Page templates
  * Store [page templates](/documentation/developers-and-admins/development/builders/page-builder/page-templates-for-page-builder) for each website in the corresponding RCL, e.g., _< AcmeWebsite project>/PageTemplates_.
  * Scope page templates to certain content types using the `ContentTypeNames` parameter of the [registration attribute](/documentation/developers-and-admins/development/builders/page-builder/page-templates-for-page-builder). If you need more granular scoping, you can use [page template filtering](/documentation/developers-and-admins/development/builders/page-builder/page-templates-for-page-builder/page-template-filtering).


### Static files of Page Builder components
  * Scripts and styles of Page Builder components must be stored in the main web application to satisfy the _/wwwroot/PageBuilder/Admin_ and _/wwwroot/PageBuilder/Public_ location conventions for [bundling and minification](/documentation/developers-and-admins/development/builders/bundle-static-assets-of-builder-components).
  * Avoid self-invoking functions when writing component scripts. The scripts are bundled and added together for all pages with Page Builder, so self-invoking may trigger functions from unrelated components or website channels. Instead, call your JavaScript API directly from the markup of individual Page Builder components.


![]()
[]()[]()
