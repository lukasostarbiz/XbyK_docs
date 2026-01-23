---
source: https://docs.kentico.com/modules/content-modeling-guide/design-website-content
scrape_date: 2026-01-22
---

Module: Content modeling guide
17 of 28 Pages
# Design website content
## Content tree hierarchy and page structure in website channels
Website channel applications allow marketers to create and manage the page content they want to display on the website. In a website channel application, a hierarchical structure called the _content tree_ represents the website content structure.
[![Content tree in website channel applications](/docsassets/guides/design-website-content/xperience-by-kentico-content-tree.png)](/docsassets/guides/design-website-content/xperience-by-kentico-content-tree.png)
Not every item in the content tree necessarily needs to **represent an actual page** on the website. From a presentation perspective, we recognize **three types of content** stored **in the content tree hierarchy** :
Type of content |  Content from the presentation perspective  
---|---  
**Content types for pages accessible under a URL** | 
  * Website pages accessible under specific URLs (e.g., _mysite.com/Home_).
  * Pages with URLs can have properties to define the URL slugs, categorize the content, and contain custom fields to adjust the content’s SEO properties. 
    * Media library file URLs are accessible through custom code.

  
**Content types for pages without a URL** | 
  * Not accessible directly under a specific URL.
  * Serve as building blocks editors can use to create strictly-ordered hierarchies.
  * Typical (but rare) examples include modeling different company offices or team organizations.

  
**Folders** | 
  * Folders don’t store any content. They are used to create taxonomies within the content tree to make navigation easier for editors.

  
**Content tree-based routing**
Depending on the website implementation, the [content tree-based routing](/documentation/developers-and-admins/development/routing/content-tree-based-routing) feature can generate page URLs based on their position within the content tree hierarchy. Learn out more about features related to pages with URLs:
  * [Routing](/documentation/developers-and-admins/development/routing)
  * [Manage page URLs](/documentation/business-users/website-content/manage-page-urls)


## Create and organize website channel content
Xperience allows you to treat _website channels_ as first-class citizens within your digital ecosystem. This means editors can create, manage, and design website content directly within the website channel application and they don’t need to switch between different contexts during their daily work. Editors can **add or reuse content** from the [Content hub](/documentation/business-users/content-hub), **craft channel-specific structured content** , or create and adjust content **directly within the webpage** using [Page Builder](/documentation/developers-and-admins/development/builders/page-builder). Or, they can approach the [website channels](/documentation/developers-and-admins/configuration/website-channel-management) as one of the presentation layers that is on the same level as other [digital marketing channels](/guides/digital-marketing/work-with-channels/digital-marketing-channels), and manage all their reusable content from the _Content hub_.
Content within a website channel can **be navigable** (forming part of the site’s structure and URL hierarchy) or **non-navigable**.
Developers can create **content templates** to display structured data dynamically. This ensures that the same content looks consistent across the website, but limits editorial control over design and layout. Alternatively, developers can design **page templates** that include _Page Builder_ zones. _Page templates_ provide editors with a flexible editing experience to manage page layouts, expose custom-built [page template properties](/documentation/developers-and-admins/development/builders/page-builder/page-templates-for-page-builder/page-template-properties), and contain _Page Builder_ widgets and sections. The _Page Builder_ is a user-friendly interface, well-suited for frequently updated or campaign-focused pages.
Before we dive into details you’ll use in designing your content strategy, remember:
  * Data in the _Content hub_ is always reusable and can support multi-channel delivery.
  * Data stored in a _structured format_ within the website channel can be reused across the website and email channels where needed.
  * Content added directly within widgets in _Page Builder_ is page-specific and not reusable across other pages or channels.


We will discuss different options how you can display content in the website channel. For further details you can structure your digital content, see the dedicated materials on the [atomic content model](/modules/content-modeling-guide/design-atomic-content-model) and the [page-based model](/modules/content-modeling-guide/design-page-based-content-model).
## Design content for a website flow
You can design and present content using the following Xperience features (and their combinations):
  * **Predefined content templates**
    * Displays only structured content (that is stored through the [page](/modules/content-modeling-guide/design-page-based-content-model) or [](/modules/content-modeling-guide/design-atomic-content-model) )
    * Define a layout for content based on the content types.
    * Can be bound only to specific content types (e.g., layouts specifically for articles or blog posts, testimonials, etc.).
    * Either assigned automatically or selectable by editors when creating pages.
    * Created and managed by the developers.
    * We recommend using content templates for content that needs to be tightly controlled.
  * **Page Templates**
    * Need to have _Page Builder_ enabled, but don’t need to contain widgets or sections.
    * Enables editors to adjust the layout of entire pages or specific sections dynamically.
    * Often combined with predefined templates to allow editors to further customize options for displayed content.
    * Created and enabled by developers but maintained by editors.
    * This approach is suitable for most website pages.


Depending on your project’s implementation, content editors can have different options to create pages and manage their content. The most common workflows are:
  * Editors [use predefined templates](#design-content-using-templates) to present _structured content_. _Page template properties_ give editors limited control over the final appearance of the content they produce. Instead, the page’s overall layout is determined using MVC view files assigned to each content type. This approach ensures that all pages of a specific type (e.g., articles, event, or product pages) use a layout from a predefined set of designs.
  * Editors [modify the page content and its layout using Page Builder](/modules/content-modeling-guide/design-content-using-page-builder) and provided components – widgets, sections, or Page Builder templates. This approach allows you to combine structured and unstructured content using dedicated Page Builder components. 
    * For example, using widgets that select a collection of _Article_ pages (structured content) and enable additional formatting options such as a custom _Call to action_ widget, the number of articles displayed per line, or personalized content, such as featuring different articles based on the visitor type (unstructured, Page Builder-specific content).


## Design content using templates
Templates can combine means to display structured content with areas editable by Page Builder.
Xperience recognizes two types of templates:
  1. **Page type templates**
     * A _page content type_ can define a static display template for each page with or without options for editors to adjust it. We recommend using this type of template if you need to enforce a unified look and feel for all pages of a particular type (e.g., blog posts), without giving the editors additional freedom of customization.
     * These templates do not require the Page Builder, which can simplify its production workflow – editors only fill in the necessary fields with their content, and the page is ready to go live. The type of the created page predetermines its design and layout.
     * To learn more about how to set up these types of templates see [Set up content tree-based routing](/documentation/developers-and-admins/development/routing/content-tree-based-routing/set-up-content-tree-based-routing#implement-routing). 
Static content layout doesn’t need to span over the whole page. We recommend that even for pages, like listing, product, or article pages, developers enable editors to work with _Page Builder_. This will allow editors, for example, to tailor the “unified content piece” with [personalized](/guides/digital-marketing/xperience-essentials/personalize-website-content-essentials) messaging. See article pages in the Kbank demo site for reference. 
  2. **Page Builder templates**
     * _Page Builder templates_ allow content editors to choose from _predefined page layouts_ when creating new pages.
     * Editors can use page templates to present _structured content_ , _Page Builder_ content, or a combination of both, as described below.
     * Developers prepare the templates and assign them to _webpage content types_.
     * Each content type can have multiple _default page templates_ to display content. When editors create a new page, they pick the most suitable template configuration. They can also save their _custom template configurations_ into page _Presets_ and [use these presets for creating new pages](/documentation/business-users/website-content/page-templates#save-pages-as-preset-page-templates).


Both template types may optionally include [Page Builder sections](/modules/content-modeling-guide/design-content-using-page-builder) to provide editors with further customization options using available Page Builder components. This type of content composition is widespread in many Xperience projects.
**Recommendations – templates**
We recommend basing every [web page content type](/documentation/developers-and-admins/development/content-types#create-content-types) on _page templates_ with [configurable page template properties](/documentation/developers-and-admins/development/builders/page-builder/page-templates-for-page-builder/page-template-properties) and enable them for most of the pages. This gives editors additional capabilities, like [save and reuse page template configuration](/guides/digital-marketing/work-with-websites/create-a-reusable-landing-page-template). Pages that are not based on page templates **cannot be switched to use page templates later** in the development cycle.
**Recommendations – page presets**
To speed up content production and publishing, we recommend creating _custom page presets for pages that work with Page Builder_.
For example, a senior editor can create an empty page using the default page template, configure its layout with required sections and (empty) widgets, and save the configuration as a page _Preset_. When editors create a new page of this type, they’ll start working on a preconfigured page. This helps with content production and helps keep design consistency between pages with the same underlying content type.
[ Previous page ](/modules/content-modeling-guide/avoid-creating-channel-specific-content-from-linked-content-fragments)
17 of 28
[ Mark complete and continue ](/modules/content-modeling-guide/design-content-using-page-builder)
![]()
[]()[]()
