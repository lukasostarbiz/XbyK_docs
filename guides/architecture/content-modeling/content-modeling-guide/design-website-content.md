---
source: https://docs.kentico.com/guides/architecture/content-modeling/content-modeling-guide/design-website-content
scrape_date: 2026-01-22
---

  * [Home](/guides)
  * [Architecture](/guides/architecture)
  * [Content modeling](/guides/architecture/content-modeling)
  * [Content modeling guide](/guides/architecture/content-modeling/content-modeling-guide)
  * Design website content 


# Design website content
## About this guide
This page is a part of the **Content modeling guide** which you should follow sequentially from [beginning to end](/guides/architecture/content-modeling/content-modeling-guide). You can also follow this series in [Content modeling guide](/modules/content-modeling-guide), which helps you keep track of your progress as you move through this sequential material.
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


We will discuss different options how you can display content in the website channel. For further details you can structure your digital content, see the dedicated materials on the [atomic content model](/guides/architecture/content-modeling/content-modeling-guide/design-atomic-content-model) and the [page-based model](/guides/architecture/content-modeling/content-modeling-guide/design-page-based-content-model).
## Design content for a website flow
You can design and present content using the following Xperience features (and their combinations):
  * **Predefined content templates**
    * Displays only structured content (that is stored through the [page](/guides/architecture/content-modeling/content-modeling-guide/design-page-based-content-model) or [](/guides/architecture/content-modeling/content-modeling-guide/design-atomic-content-model) )
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
  * Editors [modify the page content and its layout using Page Builder](#design-content-using-page-builder) and provided components – widgets, sections, or Page Builder templates. This approach allows you to combine structured and unstructured content using dedicated Page Builder components. 
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


Both template types may optionally include [Page Builder sections](#design-content-using-page-builder) to provide editors with further customization options using available Page Builder components. This type of content composition is widespread in many Xperience projects.
**Recommendations – templates**
We recommend basing every [web page content type](/documentation/developers-and-admins/development/content-types#create-content-types) on _page templates_ with [configurable page template properties](/documentation/developers-and-admins/development/builders/page-builder/page-templates-for-page-builder/page-template-properties) and enable them for most of the pages. This gives editors additional capabilities, like [save and reuse page template configuration](/guides/digital-marketing/work-with-websites/create-a-reusable-landing-page-template). Pages that are not based on page templates **cannot be switched to use page templates later** in the development cycle.
**Recommendations – page presets**
To speed up content production and publishing, we recommend creating _custom page presets for pages that work with Page Builder_.
For example, a senior editor can create an empty page using the default page template, configure its layout with required sections and (empty) widgets, and save the configuration as a page _Preset_. When editors create a new page of this type, they’ll start working on a preconfigured page. This helps with content production and helps keep design consistency between pages with the same underlying content type.
## Design content using Page Builder
[Page Builder](/documentation/developers-and-admins/development/builders/page-builder) is a user-friendly interface where non-technical users manage the content and layout of pages using configurable components.
Editors work with _Page Builder_ in [website channels](/documentation/developers-and-admins/configuration/website-channel-management). They can build entire pages (or their sections), experiment with different layouts and page designs, and immediately see the results. Equipped with widgets and sections, editors can lay out content without asking developers for help whenever they want to build a new page or adjust the design of an existing page.
[Page Builder](/guides/development/page-builder) allows content editors to design parts or whole pages wherever developers enable it (by defining [editable areas](/documentation/developers-and-admins/development/builders/page-builder/create-pages-with-editable-areas)). Editors can use two types of components to format content using _Page Builder_ : [sections](/documentation/developers-and-admins/development/builders/page-builder/sections-for-page-builder) and [widgets](/documentation/developers-and-admins/development/builders/page-builder/widgets-for-page-builder).
**Sections** specify the visual layout for widgets. They represent reusable pieces of markup that can store an arbitrary number of **widget zones** – areas where content editors place _widgets_.
**Widgets** are reusable components that content editors and non-technical users can quickly work with. Widgets give users more power and flexibility when adjusting page content and basic editing of text and images. By working with widgets, users decide which components are placed where on the page.
### Behind the scenes: How Page Builder works
Developers prepare _Page Builder_ components based on project requirements.
_Page Builder_ was not designed to create content of entire websites. The content that editors add to a widget is stored per widget instance. This makes the content difficult to work with for developers if editors want to reuse content on a different page. Instead of reusing, editors will copy data into a new item, such as a widget, which leads to content duplication and content governance nightmares. If you need to frequently reuse a piece of content, such as an FAQ question, consider storing this content in the _Content hub_ and give editors a widget to display it.
Keep the following points in mind when considering the use of _Page Builder_ :
  * Most website pages do not require frequent design or layout changes. For such pages, defining only the content in Xperience and handling the content presentation in code is more convenient.
  * _Page Builder_ has been designed for content editors and marketers as they need to be able to alter the layout of their pages. However, they don’t need to adjust the layout of all content across the entire site.
  * The risk of human error causing unintended formatting or layout issues (due to incorrect widget configuration, misclicks, etc.) increases with the number of _Page Builder-enabled_ pages.


### Understand how Page Builder stores content
All content added directly to a Page Builder widget is stored in a single database field (_CMS_ContentItemCommonData_ → _ContentItemCommonDataVisualBuilderWidgets_), making it difficult to reuse. On the other hand, widgets that add design to structured content store only a reference to the linked content (using the GUID value).
Sometimes, projects require WYSIWYG editing through widgets that also store content. While specifying the project requirements, we recommend considering the implications of this design decision. Storing content directly in widgets significantly impacts **content reusability** or the **project’s upgradeability**.
### See the different types of Page Builder widgets
Developers can prepare widgets to handle content in three ways:
  * [Widgets that present structured content](#widgets-that-present-structured-content)
  * [Widgets that store and design the content at the same time](#widgets-that-store-and-design-the-content-at-the-same-time)
  * [Widgets that combine both inline editing and structured content](#widgets-that-combine-both-inline-editing-and-structured-content)


### Widgets that present structured content
Editors add these widgets into appropriate _Page Builder_ sections and select the content to display. Xperience can store the content itself in one of the following locations:
  * **As content items in the Content hub**. The widget serves only as a design container for the content stored in the _Content hub_. For example, a _Promotion_ widget displays data stored in the _Promotion_ content type:
[![Selecting content items from Page Builder widgets](/docsassets/guides/design-website-content/image-2023-8-16_14-44-13.png)](/docsassets/guides/design-website-content/image-2023-8-16_14-44-13.png)
  * **Elsewhere in Xperience website channel application.** The widgets serve only as design containers for the content stored (and presented) elsewhere. For example, a widget that displays only several properties of the “Article” content type:
Your browser does not support the video tag.


#### What does working with the reusable content look like?
For example, imagine editors are building a campaign landing page.
The page will contain a _Hero_ element. From the design perspective, editors should select only the _Hero banner_ content type from the _Content hub_ to populate a **Banner widget**. From the editor’s perspective, adding reusable content to the page and displaying the structured data might look like the following:
Your browser does not support the video tag. 
When modeling the content storage, always consider the editor’s workflow. Help them understand what type of content they’re supposed to select. For example, you can use instructions next to the selectors in the widget [properties](/documentation/developers-and-admins/development/builders/page-builder/widgets-for-page-builder) and your project documentation to ensure editors receive enough guidance.
[![Widget with a selector and an explanation text](/docsassets/guides/design-website-content/xperience-by-kentico-cafe-widget-properties-explanation-text.png)](/docsassets/guides/design-website-content/xperience-by-kentico-cafe-widget-properties-explanation-text.png)
Similarly, ensure that editors will not unintentionally break the page experience by selecting content that doesn’t belong.
  * Developers can restrict the selection to pages of specific _webpage types_ that fulfill required conditions (e.g., have the URL feature enabled). See samples of the Xperience selectors in [Reference - Admin UI form components](/documentation/developers-and-admins/customization/extend-the-administration-interface/ui-form-components/reference-admin-ui-form-components).  
[![Page selector component with filtered selection](/docsassets/guides/design-website-content/xperience-by-kentico-selector-widget-properties.png)](/docsassets/guides/design-website-content/xperience-by-kentico-selector-widget-properties.png)
  * Similarly, developers define which type editors select into the widget properties, such as the _Benefits_ widget in the Kbank demo site.  
[![Selector content type selection](/docsassets/guides/design-website-content/cm-guide-selecting-one-type-of-content-type.png)](/docsassets/guides/design-website-content/cm-guide-selecting-one-type-of-content-type.png)


### Widgets that store and design the content at the same time
With these _single-purpose content widgets_ , editors can manage the content in a WYSIWYG manner using [inline editors](/documentation/developers-and-admins/development/builders/page-builder/widgets-for-page-builder/inline-editors-for-widget-properties), use the widget’s properties and add content through the widget’s configuration dialog, or combine both. The out-of-the-box _Rich text_ widget is an example of a widget with advanced text styling capabilities.
The _Banner_ widget on the Dancing Goat [sample site](https://devnet.kentico.com/articles/kentico-xperience-sample-sites-and-their-differences) shows both _inline editors_ and _widget configuration properties_ where all the input data is stored within one database field.
[![Widgets that store and design the content at the same time](/docsassets/guides/design-website-content/xperience-by-kentico-banner-widget-editing-properties.png)](/docsassets/guides/design-website-content/xperience-by-kentico-banner-widget-editing-properties.png)
Similarly, the _Page heading_ widget on [Kbank demo site](/guides/architecture/content-modeling/content-modeling-guide/what-is-a-content-type#best-practices-in-kbank-demo-site) allows editors to add content and style it using only widget configuration properties.
[![Add formatting metadata to content using a widget](/docsassets/guides/design-website-content/cm-guide-page-heading-properties.png)](/docsassets/guides/design-website-content/cm-guide-page-heading-properties.png)
### Widgets that combine both inline editing and structured content
_Mixed-content widgets_ allow editors to reuse structured content, e.g., by providing a selector to pick content from the content tree. At the same time, editors can use the widget’s editable properties to overwrite some of the content pulled from the selected page or create brand-new content. Editors can also provide additional information or behavior to their content, such as ensuring that hyperlinks to external websites open in a new tab.
[![Mixed-content widget example](/docsassets/guides/design-website-content/xperience-by-kentico-card-widget-properties.png)](/docsassets/guides/design-website-content/xperience-by-kentico-card-widget-properties.png)
We recommend limiting the use of mixed-content widgets to specific pages (sections), as content stored in these widgets can be more difficult to reuse in other channels.
### Drive displaying data using taxonomy
Design dedicated widgets to consume [taxonomy terms](/documentation/developers-and-admins/configuration/taxonomies) as input and dynamically display content based on those terms to support taxonomy-driven content. For example, a _content listing widget_ should allow editors to select a [taxonomy tag](/guides/digital-marketing/work-with-taxonomies/taxonomies) or [smart folder](/documentation/business-users/content-hub/content-hub-folders#smart-folders) as a filter. The widget then queries and displays only the matching items. It should also offer options to exclude certain content, such as by listing available tags or applying an “exclude from display” tag in article itself, to give editors more control over the final output. Ideally, the widget will allow editors to wrap the listed content with additional content (e.g., text or visuals) to ensure that content remains reusable and manageable directly from the _Page Builder_.
[![A listing widget with taxonomy filtering](/docsassets/guides/design-website-content/cm-news-listing-article-with-filtering-via-taxonomy-tags.png)](/docsassets/guides/design-website-content/cm-news-listing-article-with-filtering-via-taxonomy-tags.png)
### Recommendations – Page Builder
**Combine structured content and widgets**
To make the website’s content both reusable and future-proof, we recommend storing the content of the widgets using the structured content format. Developers should prepare widget properties that allow editors to format content using WYSIWYG editing capabilities.
Based on the project requirements, the structured content of widgets can be stored in a dedicated section in the content tree or directly in a hierarchy under the page as child items.
**Limit using widgets for single-purpose content**
Xperience stores the content input directly into widgets within one database field for the whole page. We recommend storing content in widgets only when the content is not going (or doesn’t have the potential) to be reused on the website. This content can work on one-off pages, such as campaign landing pages.
**Create sections with designing capabilities**
We recommend creating sections with properties to let editors define how the content they add via widgets displays on the website. Some examples of section properties are a field for a URL parameter (Anchor tag) for deep linking, a property to define the number of columns within the section, a property to adjust the margin or padding of widgets, and changing the color of the section, and similar properties.
**Set restrictions on editable areas**
Use editable area restrictions to ensure editors can add only desired widgets or sections to specific areas. This ensures page designs do not break (e.g., in different browsers) and creates boundaries for data predictability (when considering future upgrades). Find out more in [Create pages with editable areas](/documentation/developers-and-admins/development/builders/page-builder/create-pages-with-editable-areas).
**Create custom widgets to add markup to pages**
Typically, in the early stages of a live project, editors might need to add custom HTML markup or include scripts to embed 3rd party applications. Project owners sometimes need to remember this or similar scenarios when detailing project requirements, so ensure your project gets this low-hanging fruit to make editors happy.
We recommend creating custom widgets that allow editors to add scripts or HTML markup to the page’s _head_ or _body_. For example, editors might use an _Anchor widget_ to enable linking to a section of the page or a _Script widget_ to add JavaScript that runs a custom-built mortgage calculator.
If editors will reuse these scripts or markup on different pages, developers can prepare a custom _Script_ content type or widget.
**Add instructions to selectors in Page Builder components**
When building widgets that display structured content, provide editors with clear instructions describing what content items they should select. You can use the **ExplanationText** [widget property](/documentation/developers-and-admins/customization/extend-the-administration-interface/ui-form-components/editing-components) and provide contextual information specific to the selected widget. Providing additional guidance ensures that editors don’t break the visual experience of the website by mistake. Also, include clear instructions about how editors should use every widget on the website in your project documentation.
**Make designing content generic**
Though you might want to store design-specific information, like exact color codes or sizes, inside the channel-specific or reusable content types in the _Content hub_ , we do not recommend doing this.
From a content longevity perspective, you want to have your design information as agnostic to the visual design as possible. When you decide to, for example, redesign your brand, it’s easier to remap the generic class name to new colors than change the specific _#F05A22_ brand color code in hundreds of different places. The following screenshot shows styling options in the Kbank demo site.
[![Styling options](/docsassets/guides/design-website-content/cm-guide-section-properties.png)](/docsassets/guides/design-website-content/cm-guide-section-properties.png)
In typical Xperience projects, **these three approaches are combined together** to create an effective content management experience that supports reusing the content as necessary. Stakeholders must decide how they’ll work with the content when defining the project requirements. However, to make your content future-friendly, we recommend storing it in a structured format.
## What’s next?
You’ve learned about the different components editors can use to design Xperience pages. On the [next page](/guides/architecture/content-modeling/content-modeling-guide/design-atomic-content-model), you’ll learn about different approaches to storing the data using two different content models - the [atomic content model](/guides/architecture/content-modeling/content-modeling-guide/design-atomic-content-model) that supports high-content reusability. Then, we’ll discuss the [page-based content model](/guides/architecture/content-modeling/content-modeling-guide/design-page-based-content-model).
![]()
[]()[]()
