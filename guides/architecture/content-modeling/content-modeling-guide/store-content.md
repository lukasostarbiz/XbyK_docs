---
source: https://docs.kentico.com/guides/architecture/content-modeling/content-modeling-guide/store-content
scrape_date: 2026-01-26
---

  * [Home](/guides)
  * [Architecture](/guides/architecture)
  * [Content modeling](/guides/architecture/content-modeling)
  * [Content modeling guide](/guides/architecture/content-modeling/content-modeling-guide)
  * Store content 


# Store content
## About this guide
This page is a part of the **Content modeling guide** which you should follow sequentially from [beginning to end](/guides/architecture/content-modeling/content-modeling-guide). You can also follow this series in [Content modeling guide](/modules/content-modeling-guide), which helps you keep track of your progress as you move through this sequential material.
## Where to store your content
This material explains the options for creating, storing, categorizing, and managing reusable content available in Xperience by Kentico.
Up to this point, we’ve focused on general principles of content modeling that apply to many platforms. Now, we’ll look specifically at how Xperience stores content so you can make informed decisions for your project.
Before building your Xperience project, you should evaluate how the system stores content. The business use cases you identified during your content modeling sessions will help you choose the most suitable approach for storing and organizing the data.
Xperience delivers content through channels and doesn’t allow for tapping directly into reusable content storage and retrieving it. You need to consider your distribution strategy before you decide where and how to store your project’s content.
## Digital content and marketing channels
Marketing channels refer to platforms, tools, and strategies businesses use to communicate with their end consumers, suppliers, partners, and other business entities. In Xperience, a channel represents the platform where you deliver your content to customers, such as your main website, microsites, email channel, or a headless channel.
Marketers often copy content separately to each channel, which creates extra work and potentially leads to content and message inconsistency. With Xperience, you can store your marketing content in one place and distribute it to your digital channels from the same interface. This helps editor teams keep the campaigns and other marketing communications consistent and efficient.
The current version of Xperience allows you to distribute content across websites, email, and headless channels. When preparing the content model, you need to respect each channel’s content requirements. As you continue through the guide, you’ll learn more about these differences and how to model content for each channel.
Find more details about [digital marketing channels](/guides/digital-marketing/work-with-channels/digital-marketing-channels) in a separate guide.
## Keep all digital content in Xperience
From a high-level perspective, you can look at creating and managing content in Xperience in two different ways:
Content is created |  Content is managed |  Description  
---|---|---  
**Inside** Xperience |  **Inside** Xperience |  The Xperience database serves as the single source of truth for all content (e.g., various marketing materials, company-specific information, or product-related documents). Before editors start producing content, developers must prepare relevant data structures (_content types_) in the administration interface.  
**Inside or outside** Xperience |  **Inside or outside** Xperience |  Depending on the project requirements, customers often combine storage options. They keep part of their content inside Xperience and part outside. The content they manage outside of Xperience usually comes from business-specific external systems, e.g., various external systems, such as global statistics databases, [ERP](https://en.wikipedia.org/wiki/Enterprise_resource_planning) or [PIM](https://en.wikipedia.org/wiki/Product_information_management) solutions for managing product catalogs. They synchronize this external content to Xperience using custom-built functionality that polls the source’s API and brings the data to Xperience. We’ve commonly seen many variants of these options across different Xperience projects.
  * The system uses a custom process that creates corresponding content objects in Xperience. At the same time, Kentico editors can manage (_read_ , _update_ , and _delete_) this data in Xperience and synchronize their changes with the origin.
  * If editors need to display some content in their applications, they reference the content stored in the third-party solution. For example, a reference to statistics objects (a codename) or products (SKUs) makes this data available for display across different Xperience-driven applications. Developers then ensure Xperience styles updates the data correctly.
  * Third-party content is automatically displayed in predefined application areas, such as in specific sections of websites. Third-party data is part of the website implementation, and editors cannot opt out of displaying this content.

Note that the last two options introduce multiple content management systems within one setup, which can cause complications.  
**Recommendations - Creating and managing content in Xperience**
  * **Storing and managing content directly in Xperience is recommended** for most projects.
    * It ensures a single source of truth for content distribution across different channels. Moreover, editors know where the content for which they are responsible resides.
    * It prevents synchronization errors with third-party integrations and ensures that editors always have the latest version of the content available in one place.
    * We recommend storing content in Xperience if editors want to leverage digital marketing features, such as [content personalization](/documentation/developers-and-admins/digital-marketing-setup/content-personalization) or segmenting your audience into [contact groups](/documentation/business-users/digital-marketing/contact-groups) for various marketing activities.
  * If you **need to display content from a third-party solution** in an Xperience project:
    * We recommend **building a custom bidirectional content synchronization tool** that automatically updates the data across different systems.
    * Implementation partners should **provide sufficient project documentation** to help editors and marketers locate and efficiently manage their content.
    * Your editors should **manage their content in the production environment** using the default Xperience publishing workflow.


## Understand how Xperience stores digital content
In Xperience, all content is based on **content types**. You already know that a [content type](/documentation/developers-and-admins/development/content-types) is a _blueprint_ that defines _data structure, functionality, and behavior_ for all content items editors create from this type. Based on this definition, reusing content requires storing it in a presentation-independent manner, i.e., in a structured format.
Xperience uses different content types depending on the purpose. Each has its own storage options and ways of connecting to other stored content.
  * **Reusable content type** – Stores data in the [Content hub](/documentation/business-users/content-hub). This content isn’t tied to any specific channel. Editors can reuse it across multiple channels, such as websites, emails, or headless applications.
  * **Webpage content type** – Used in the [website channel](/documentation/business-users/website-content). _Web pages_ can store data directly or act as a container displaying reusable content from the _Content hub_ (or a combination of these approaches).
  * **Email content type** – Used in the [email channel](/documentation/developers-and-admins/digital-marketing-setup/email-channel-management). Emails can store data directly or display data from _webpages_ or _reusable items_ stored in the _Content hub_.
  * **Headless content type** – Used in the [headless channel](/documentation/developers-and-admins/configuration/headless-channel-management). Headless items can store data directly in the headless channel or display _reusable content_ from the _Content hub_.


We’ll discuss each of the options further in this and other dedicated materials. The following diagram overviews the storage options in Xperience.
[![Levels of abstraction in data storage and data presentation](/docsassets/guides/store-content/cm-guide-store-content-objects.png)](/docsassets/guides/store-content/cm-guide-store-content-objects.png)
### Content types from the data perspective
[Content types](/documentation/developers-and-admins/development/content-types) that store data in a _structured format_ allow editors to create items in a form-like interface where they input data. The editing forms create a unified editing experience, and adding content in the _Content hub_ feels like filling out an online form. From a [data perspective](/documentation/developers-and-admins/api/content-item-api/content-item-database-structure), these fields correspond to database columns. Developers define content types in the **Content types** application.
When developers create content types, they configure:
  * _Fields_ that define the data structure of the content type.
  * _Editing form layout_ that creates the interface editors use to create and modify the content items.
  * _Usage_ of the content type: 
    * Reusable content types are building blocks of reusable content meant to be linked together to form further content.
    * Content types for pages are used in website channels to form pages.
    * Content types for email are used in email channels to form emails.


Developers use the **Content types** application to define the [data model](/guides/architecture/content-modeling/content-modeling-guide/structured-and-unstructured-content) of the Xperience application. They create _content types_ based on the project’s business requirements using an easy-to-use admin UI consisting of form fields or ready-made form components.
[![Managing content type fields](/docsassets/guides/store-content/cm-guide-content-types-application-ui.png)](/docsassets/guides/store-content/cm-guide-content-types-application-ui.png)
The _content type_ is stored in a coupled database table; its fields represent database columns. For example, Kbank’s _News_ content type can have a coupled table named _Kbank_News_ and columns defined as _NewsTitle_ , _NewsSummary_ , or _NewsText_.
## Understand how each content type stores and uses data
The diagram [above](#understand-how-xperience-stores-digital-content) shows where different storage layers and content relationships are in Xperience. In the following sections, we’ll look at each channel and the _Content hub_ in detail and explore how editors can store their data.
### Website channel content
In a website channel, editors create content using the [Page Builder content](#page-builder-content) that allows flexible editing of the layout and content of pages, as [structured pages](#structured-pages) that store content into fields without including presentation information and support consistent reuse and delivery. They can also display [reusable content](#reusable-content-in-content-hub) from the _Content hub_. Pages in the website channel are displayed in a visual hierarchy called the [content tree](/guides/digital-marketing/xperience-essentials/manage-website-content-essentials#content-tree-represents-the-hierarchy-of-website-pages).
#### Page Builder content
[Page Builder](/documentation/developers-and-admins/development/builders/page-builder) allows non-technical users to manage the content and layout of their pages with templates, sections, and widgets. _Page Builder_ isn’t intended to store all website content.
  * _Page Builder_ allows editors to manage the content and layout of select pages using a WYSIWYG editor.
  * Editors control page layout using [widgets](/documentation/business-users/website-content/widgets-and-page-builder) and [sections](/documentation/developers-and-admins/development/builders/page-builder/sections-for-page-builder) – reusable and configurable content prepared by the developers. Editors can dynamically alter the page’s design without developer assistance.
  * We recommend using _Page Builder_ in all website pages where editors need to [personalize content](/documentation/business-users/digital-marketing/widget-personalization) to help website visitors on their customer journey.
  * _Page Builder_ works well for **one-off or quickly degrading content** that needs adjusting. It helps when editors need **WYSIWYG-style editing**.
  * Use it when you **don’t need to reuse the same piece of content in multiple places** (except for widgets that allow for presenting structured content; see below).
  * _Page Builder_ content is stored in a [dedicated database column](/documentation/developers-and-admins/api/content-item-api/content-item-database-structure#content-items) as a [JSON string](#remember-how-page-builder-content-is-stored).


**Recommendations – Page Builder**
Use _Page Builder_ to:
  * **layout and design reusable, structured content through page templates, sections, and widgets**. This approach helps editors focus on creating quality content and reusing it where needed.
  * **alter pages to allow for content personalization** using [personalized](/documentation/business-users/digital-marketing/widget-personalization) content variants. For example, above-the-fold content on high-traffic pages (home page), prominent areas on listing pages (articles page), project-specific category pages, or user profile pages.
  * **store one-off content** that you know will not be reused anywhere, such as data relevant only to the information displayed on the page, call to actions, or entire pages when creating one-off marketing campaign pages. Enable [personalization](/documentation/business-users/digital-marketing/widget-personalization) for these experiences too.


You can use the  _Rich text_ and  _Form_ widgets [provided out-of-the-box](/documentation/developers-and-admins/development/builders/page-builder/reference-default-page-builder-widgets) when working with _Page Builder_.
#### Structured pages
Editors create _structured pages_ without the need to focus on their layout. From a developer’s perspective, _structured pages_ are strongly typed and stored in dedicated database tables per _content type_. Developers can easily [retrieve this data via the API](/documentation/developers-and-admins/development/content-retrieval/retrieve-page-content) and display it on the live site application according to project requirements. Alternatively, developers can retrieve and display the content in various channels, such as mobile applications or third-party services.
_Structured pages_ can be formatted using the following approaches:
  * Programmatically per page type by assigning a dedicated view file – see [Set up content tree-based routing](/documentation/developers-and-admins/development/routing/content-tree-based-routing/set-up-content-tree-based-routing).
  * By editors using page templates assignable per page – see [Page templates for Page Builder](/documentation/developers-and-admins/development/builders/page-builder/page-templates-for-page-builder).
  * By editors using widgets that contain a selector functionality that editors use to pick the intended content item from the content tree. Existing [selectors](/documentation/developers-and-admins/upgrade-to-xperience-by-kentico/editing-components-in-xperience-by-kentico) allow editors to select any item from the content tree, so when creating the content storage section for reusable content, make the content items easy to find and navigate. You can also consider customization to the **Page selector** and restrict its use to [a specific content type](/documentation/developers-and-admins/customization/extend-the-administration-interface/ui-form-components/reference-admin-ui-form-components).


This approach helps maintain consistent content design and feel across the website channels, but provides editors with less flexibility in changing the layout or format.
**Recommendations – Structured pages**
We recommend using _structured pages_ for:
  * Content that doesn’t change structure or requires relatively static layouts, such as full (or parts of) articles, blogs, news pages, biographies, or company offices, where you want to reuse this content across multiple channels.
  * Pages that are not accessible under a specific URL, and you want to keep the data tied to the website channel.


### Reusable content in Content hub
Editors can create and manage [reusable content](/guides/architecture/content-modeling/content-modeling-guide/store-content#where-to-store-your-content) directly from within the website channel, without leaving the page editing workflow.
  * Editors can create _reusable content_ from website channel applications by selecting the _Create new_ button when editing any page that has a field containing linked content items.
  * Selecting the _Create new_ button automatically creates a new content item of a predefined content type. When an editor saves the changes, the new content item is linked to the edited page. If editors update the linked item, the content is automatically updated in the website page as well.


This option helps editors keep content consistent across multiple [digital marketing channels](/guides/digital-marketing/work-with-channels/digital-marketing-channels), minimizes content duplication, and improves content curation experience.
The following diagram shows the options for adding content in the website channel application.
[![Content management in website channels](/docsassets/guides/store-content/page-creation-workflow.png)](/docsassets/guides/store-content/page-creation-workflow.png)
When deciding how page content gets stored in Xperience, you have two options: [structured pages](/documentation/developers-and-admins/development/content-types) and [Page Builder](/documentation/business-users/website-content/widgets-and-page-builder). The following table gives an overview of both approaches.
|  Page Builder |  Structured content (both non-page items and reusable content)  
---|---|---  
**Advantages** | 
  * Focus on design
  * Greater control over the layout and styling
  * Makes it easier to fine-tune individual pages
  * More flexibility for [personalizing](/documentation/business-users/digital-marketing/widget-personalization) pages content

| 
  * Focus on content
  * Easier to work with data in code
  * Better for storing reusable content presented across multiple channels

  
**Recommended usage** | 
  * Campaign pages
  * Landing pages
  * Home pages

| 
  * Articles, news, blog posts
  * Offices, subsidiaries
  * Case studies
  * Reusable content types, such as:
    * Author bios, Employee profiles
    * CTAs, banners, FAQs, videos

  
### Email content
Any email editors create or adjust from within the Admin UI needs to be part of an email channel and based on a content type. You can have as many email content types as you want. Depending on the purpose of the email and how it’s implemented, editors can create _structured emails_ in the _Content_ view mode, _Email Builder_ , or a combination of the two.
  * _Structured emails_ use form-based content types with email templates predefined by developers 
    * Their structure and design are consistent, as each field renders exactly as defined and in order as defined by developers.
    * Editors can use AIRA to generate or refine email content in their email workflow.
    * Ideal for repeatable, fixed-layout communications such as newsletters, event reminders, or product updates, as they are easy and fast to create.
  * _Email Builder_ offers a visual drag-and-drop interface with customizable sections and widgets. 
    * Editors can change the email design depending on their needs. They have control over the layout and design of their emails, but thanks to the prepared components, their designs stay within brand limits.
    * Email Builder supports personalization against [contact groups](/guides/digital-marketing/work-with-contacts/contact-groups-overview).
    * Ideal for audience-targeted emails or campaigns with varied layouts, such as promotions or product showcases.


Use structured emails when editors need to create emails fast, and Email Builder when they need a flexible interface to support their message through email layouts. You’ll learn more about [designing email content](/guides/architecture/content-modeling/content-modeling-guide/design-email-content).
### Headless content
Editors create [content](/documentation/business-users/headless-content) in [headless channels](/documentation/developers-and-admins/configuration/headless-channel-management) when they want to deliver their digital content to external channels through a headless API (GraphQL). They can manage content for mobile apps, kiosks, web apps, and other connected devices within the Xperience ecosystem.
  * Headless content stores data in a presentation-agnostic format, ready for use across multiple platforms.
  * Data is served in JSON format for consumption by headless applications and services through [Xperience headless API](/documentation/developers-and-admins/development/content-retrieval/retrieve-headless-content).
  * Editors typically create items for presentation units, such as app screens, display windows, or navigation elements.
  * Headless content is ideal for scenarios such as mobile screens, information kiosks, TV displays, or AR experiences.


Headless content isn’t meant to be the primary method for working with content. It fits into the content system as part of Xperience’s [hybrid-headless approach](https://www.kentico.com/platform/development), combining headless delivery with traditional CMS features.
## Choose the right approach for storing content
The method you use to store your content in Xperience affects how easily editors can reuse, update, adapt, and maintain their digital content. We’ll explore and explain the details and impacts of choosing different storage approaches, and provide recommendations for when to use each.
### Remember how Page Builder content is stored
All content added directly to a Page Builder widget is stored in a single database field (_CMS_ContentItemCommonData_ → _ContentItemCommonDataVisualBuilderWidgets_), making it difficult to reuse. On the other hand, widgets that add design to structured content store only a reference to the linked content item (using the GUID value).
Sometimes, projects require WYSIWYG editing through widgets that also store content. While specifying the project requirements, we recommend considering the implications of this design decision. Storing content directly in widgets significantly impacts content reusability and the project’s maintenance costs and upgradability.
### Combine Page Builder and structured content
**Page Builder** and structured content are not mutually exclusive. You can combine _Page Builder_ with structured content to leverage the full potential of both approaches.
When you define your content model, consider situations in which you can store the data in a _structured format_ and use _Page Builder_ widgets or sections to present or even overwrite this content in the website page. Your widgets and sections can contain properties that allow editors to add design information (size, colors, and other content behavior). Widgets can contain properties that editors can add to overwrite the structured, reusable data and tailor the data with a per-instance-specific message. Furthermore, some content types, e.g., Articles listing pages, bring editors more possibilities if you combine _Page Builder_ and structured content.
In general, your editors will benefit when you combine both approaches:
  * Use _Page Builder_ for formatting structured content
  * Add _Page Builder editable areas_ to _page templates_ that display structured data to help with content personalization.


The following diagram shows the benefit of storing data separately from its presentation. From a user perspective, editors can create their data once and reuse it when needed.
  * News (_content type_).
  * The editor creates a new page using the News _page type_ and selects the _page template_ they want. The _News_ page type has the URL feature enabled to generate the page’s URL and make it accessible to website visitors.
  * Data input via the _Content tab_ is stored in the “News” _page type_ , and data presentation (layout and styling) is handled via the _page template_.
  * The _Teaser image_ field in the _News_ page type references a _file_ stored in the _Content hub_ ; it doesn’t keep the file itself.
  * Landing page (_content type_)
  * The editor creates a new page using the Landing page _page type_ and selects a _page template_.
  * The Landing page _page type_ has Page Builder enabled.
  * Using the _Featured article_ widget, the editor displays some data stored in the _Article_ : the _Title_ , _Teaser image_ , and _Publication date_.
  * The widget references the selected _News_ using its GUID. If the widget has any properties that set design information for the displayed data, their configuration applies separately for each widget.
  * The editor also adds a _Speaker_ widget that displays some of the data from the _Author_ content type stored in the Content hub.
  * The widget references the selected _Author_ using its GUID. Data about the widgets’ configuration on the _Landing page_ is stored in the _DocumentPageBuilderWidgets_ database field in JSON format.


[![Structured content reuse example](/docsassets/guides/store-content/cm-guide-using-structured-content-in-content-types.png)](/docsassets/guides/store-content/cm-guide-using-structured-content-in-content-types.png)
### Use Content hub for reusable content
Xperience comes with the **Content hub** application for storing reusable content. Think of the _Content hub_ as a **centralized repository** where you **create and keep your marketing content** , and potentially **reuse** it across multiple communication channels.
In the _Content hub_ , editors create items using content types with fields where they input data. The editing forms create a unified editing experience, and adding content in the _Content hub_ feels like filling out an online form.
[![Content type editing interface](/docsassets/guides/store-content/cm-guide-hero-banner-content-type.png)](/docsassets/guides/store-content/cm-guide-hero-banner-content-type.png)
Because the _Content hub_ is channel agnostic, editors do not see what the content will look like once it’s displayed, for example, on the company website. They can focus **on the quality of their content** rather than getting distracted by how the content will look.
### Plan your content modeling approach in Xperience
Different CMS solutions allow editors to approach their content in different ways. Although using a single _content type_ for _free-form content_ in Xperience may seem easier, we recommend creating a _structured content model_ with clearly defined content types and presentation layers to help editors maintain a [consistent content experience](/guides/architecture/content-modeling/content-modeling-guide/content-modeling-business-requirements) across teams and channels.
#### Aim for semantic content types
_Semantic content types_ organize information into [conventional data types](https://schema.org/) and make the data easier for both humans and machines to understand and interact with. _Semantic content types_ go beyond just presenting a few images or texts; they provide a clear semantic context and give the data meaning. This meaning remains the same when editors adapt this content for different channels, like a website, mobile applications, or emails. At the same time, editors can enrich this content with relevant information, such as date, time, or location, or personalize the message to tailor the experience further.
_Semantic content types_ are particularly valuable when customers integrate Xperience with third-party systems like ERPs or external product catalogs. Making the data shaped and scoped into a consistent structure in Xperience and other systems allows for efficient data synchronization. As a result, maintaining meaningful content and ensuring a seamless flow of information between systems is much easier.
Here are a few practical use cases:
  * **Articles** - store [articles](https://schema.org/Article), or [blog posts](https://schema.org/BlogPosting) as structured content items with titles, summaries, body content, author references, tags, and related assets. You can dynamically display articles on listing pages, author profiles, and topic landing pages without duplicating content. This ensures consistent presentation, supports discoverability, and makes it easy to update metadata or related links across all listings when editors update the articles.
  * **Product** – reuse [product’s](https://schema.org/Product) structure with common features and benefits across similar products. For example, if several products include fast approval or secure online banking, you store these benefits once and link them to each product. This allows product teams to update a benefit’s description or icon in one place, automatically updating all linked products without manual edits.
  * **Services** – manage your [services](https://schema.org/Service) as reusable content items containing structured details such as the service name, description, benefits, related case studies, and CTAs. You can link these services across product pages, industry landing pages, and campaign microsites. This allows your marketing and sales teams to maintain consistent messaging about each service. The system automatically updates the service information instantly across all channels where the service appears.
  * **Author profiles** – reuse [authors](https://schema.org/author) or bios across blogs, webinars, and landing pages. When a team member’s role or bio changes, your content team only needs to update it once, and the change reflects across all blog posts, speaker pages, and author blocks, saving time and ensuring consistency.
  * **Events** – share [event details](https://schema.org/Organization) across event listings, landing pages, promotional emails, and a dedicated event companion mobile app. Event teams can manage the event date, time, location, and description in one place, ensuring that any updates automatically appear everywhere the event is promoted.
  * **Organization** – store [organizational profiles](https://schema.org/Organization), such as partner companies or internal departments, as reusable content types with names, logos, descriptions, and contact details. These can be linked dynamically to press releases, case studies, or event listings, ensuring consistency and simplifying updates when contact information or branding changes.
  * **Place/Location** – manage [places](https://schema.org/Place) or [locations](https://schema.org/location) as structured content items with address, map coordinates, contact details, and opening hours. Editors can reference the same location across events, service area listings, or store finders. When they change the details, Xperience automatically promotes the updates across all digital channels.
  * **Reviews** – manage customer or partner [reviews](https://schema.org/Review) as reusable content items with the reviewer’s name, role, rating, testimonial text, and related product or service references. Editors can refer to these reviews from across product pages, campaign landing pages, and industry sections to build trust and social proof. Updating a review or adding translated variants automatically updates every location where the review is displayed, maintaining consistency while reducing repetitive content management.
  * **Testimonials** – similarly to reviews, you can use the same customer testimonial in multiple campaigns. Marketing teams can centrally store testimonials in a predictable [review snippet](https://developers.google.com/search/docs/appearance/structured-data/review-snippet) structure and reuse them on landing pages, newsletters, and product pages while tracking where each testimonial appears, supporting consistent messaging across channels.


Similarly, your project can store data in content types that aren’t described in the [schema.org](https://schema.org). Even though their data structure isn’t rooted in conventions, you can still define a predictable one. For example, you can derive their properties from existing types and validate their structure with Google’s [structured metadata markup](https://developers.google.com/search/docs/appearance/structured-data/intro-structured-data). We have seen the following types in Kentico customers’ projects.
  * **Hero banners** – store hero banners as [reusable content items](https://developers.google.com/search/docs/appearance/structured-data/carousel) with images, headlines, and supporting text. Editors can reuse _banners_ across landing pages, campaign pages, or microsites. This allows your design and marketing teams to update seasonal or promotional banners in one place, ensuring a unified look across channels while enabling fast swaps when campaigns change.
  * **FAQs** – link [common questions](https://schema.org/FAQPage) to products, services, or help articles. Support teams can update an FAQ answer once, and the new information will automatically display anywhere the FAQ is referenced, reducing customer confusion and repetitive edits.
  * **Case studies** – manage case studies as atomic content types that contain the client name, summary, testimonial quotes, and outcomes. These can be linked dynamically to industry pages, service pages, or campaign landing pages, allowing your sales and marketing teams to showcase relevant case studies without duplicating content across your site.
  * **Delivered projects** – store completed [project](https://schema.org/Project) descriptions, client details, project images, and outcomes as reusable content items. These can be linked to service pages, industry landing pages, or even specific blog posts highlighting your expertise, making it easier for your teams to display proof of delivery consistently while managing updates in a single location.
  * **Global CTAs (Calls to Action)** – manage CTAs as reusable items across pages and campaigns. If your marketing team needs to update a link or button text for a seasonal campaign, they can do it once in the CTA item, instantly updating all linked locations.


Depending on your project, look into [schema.org](https://schema.org/), Google’s [structured data](https://developers.google.com/search/docs/appearance/structured-data/search-gallery), or other similar metadata catalogs to give your content types a predictable structure.
This composable approach supports large teams by separating ownership and maintaining a single source of truth for reusable content. It simplifies multi-channel delivery because editors can reuse and display the same structured content in emails, websites, and apps without duplicating it. It also helps maintain consistent customer experiences, ensuring your brand speaks with one voice wherever your content appears.
#### Help agentic search, LLMs, and SEO
_Semantic content types_ also help provide a better [AI agentic web experience](https://blogs.microsoft.com/blog/2025/05/19/microsoft-build-2025-the-age-of-ai-agents-and-building-the-open-agentic-web/) and support traditional, now [almost legacy, SEO practices](https://searchengineland.com/legacy-seo-457541). It is easier for LLMs and search AI agents to extract and repurpose concise and structured content in clearly defined types. Different content elements represent conventional semantic types.
Developers can add semantic metadata to website-specific content and define a clear and concise structure that [LLMs understand](https://searchengineland.com/how-ai-mode-ai-overviews-work-patents-456346). This helps [search engines and AI agents](https://searchengineland.com/how-search-generative-experience-works-and-why-retrieval-augmented-generation-is-our-future-433393), including [AI Overviews](https://developers.google.com/search/docs/appearance/ai-features) and [NLWeb](https://news.microsoft.com/source/features/company-news/introducing-nlweb-bringing-conversational-interfaces-directly-to-the-web/), understand, interpret, and present your content more accurately.
Preparing the content for AI-driven tools will support your client’s long-term content strategy and discoverability across search and other emerging AI-driven platforms.
#### Help editors with content creation and curation
Providing structured, semantically relevant editing experiences helps editors deliver high-quality content efficiently. Editors can submit their content using elaborate editing [workflows](/documentation/developers-and-admins/configuration/workflows) to reduce mistakes during content creation, publishing, and curation throughout the content lifecycle.
### Select your content modeling approach
You’ve learned about different ways to store _structured content_ in Xperience. From a global perspective, the approach you choose depends on the project needs, editor teams, and content delivery strategy.
You can store your data in a **reusable content** format in the Content hub for universal delivery, including a headless or hybrid-headless experience, and follow the pattern that we call an **atomic content model** , or create a **page-based model** that works great for web-centric, visually driven projects.
The **atomic content model** focuses on creating a [highly composable, omnichannel experience](/guides/architecture/content-modeling/content-modeling-guide/design-atomic-content-model). Editors store **reusable content** in the _Content hub_ and keep this content channel-agnostic. To display this content in the presentation channel, such as a website, app, or campaign microsite, editors will use channel-specific “wrappers” around the _reusable content_ and add _channel-specific content_ , for example, SEO metadata in website channels. This approach works best for solutions that require scalable content reuse across many channels while maintaining a single source of truth.
The **page-based model** allows you to manage [structured and unstructured content](/guides/architecture/content-modeling/content-modeling-guide/structured-and-unstructured-content) within _webpage content types_ for _web-centric_ , [page-driven content flows](/guides/architecture/content-modeling/content-modeling-guide/design-page-based-content-model). The editing UI for structured content in the [website page](/guides/development/developer-kickstart/define-a-page-content-type) is similar to _Content hub_ , and editors can reuse content input through this interface in other website pages (on the same channel or cross website channel), for example, for listing pages, or in emails. The unstructured data is managed through the [Page Builder](/documentation/developers-and-admins/development/builders/page-builder). In _Page Builder_ components, editors can refer to other reusable, structured content and display it. Or, they can store the content inside the _Page Builder_ page or area. This makes the content “owned by the page” and very hard to reuse.
Using _Page Builder_ as a primary way to store data is effective for:
  * Projects where content is used primarily on one website and in one place on this website.
  * Teams that require a user-friendly, WYSIWYG editing interface while maintaining semantic structure for SEO and AI readiness.


We’ll discuss both approaches later in dedicated material.
#### Compare the content modeling options
The following high-level comparison helps you decide which approach aligns with your project goals and highlights the most important trade-offs.
The _Content hub_ fits best for content reuse, omnichannel consistency, and headless delivery. At the same time, _page-based models_ align with visual-first workflows.
_Page-based structured content_ aligns with design-driven editorial workflows, enabling structured content while maintaining flexibility for visual layout. In contrast, the _Content hub_ enables consistent content reuse across websites, apps, and campaigns with centralized management.
The editing experience differs. _Page-based model_ supports primarily visual, page-driven editing while the _Content hub_ focuses on structured, presentation-agnostic, reusable data management.
Xperience also supports **a combination of these approaches** , allowing you to use _Page Builder_ widgets to display data stored in a _structured format_ from the _Content hub_ within page layouts. However, Xperience does not support _inline editing_ of the _structured data_ within the _Page Builder_ interface.
Both approaches can support strong SEO and AI search readiness if you correctly structure the individual content types.
Let’s make a short overview of both approaches:
Aspect |  Atomic content model |  Page-based content model  
---|---|---  
Best for |  Multi-channel, headless, and scalable reuse |  Web-centric projects  
Structure |  Channel-agnostic, reusable |  Structured within the page hierarchy  
Editing experience |  Content-centric |  Visual, page-driven  
SEO and AI |  Strong with consistent reusable data, displayed in semantic HTML structures |  Editors need to focus on crafting semantic HTML structures  
### Store core content into reusable field schemas
While creating a content model or defining the data structure for a content type, you’ll often identify that some fields or sets of fields repeat across different content types. For example, all product-related content types will have the same fields to hold _title_ , _product description_ , _product image_ , and core _taxonomy_. Some product-specific fields are similar. Other content types used to promote these products might contain the same structure.
To manage these repetitions efficiently, we recommend using **reusable field schemas** that help you handle such [repeated fields](/documentation/developers-and-admins/development/content-types/reusable-field-schemas) across different content types.
The following image shows the Kbank demo site’s content types for storing products: **Account** , **Card** , and **Loan**.
[![Example of reusable field schema](/docsassets/guides/store-content/cm-reusable-schema-fields-product-content-type.png)](/docsassets/guides/store-content/cm-reusable-schema-fields-product-content-type.png)
All three product content types share two groups of schemas – the _Core content_ schema and _Financial data_ schema. While the _Financial data_ schema is relevant only to the products, the _Core content schema_ stores the same content as other content types in the Kbank demo site. For example, we show two more content types that share the same schema: **Featured content** and **Promotion**.
Note that each content type has dedicated fields relevant only to that specific content type, such as the Minimal inflow or account type in the _Product_ – _Account type_ field or the _External URL_ field in the _Featured content_. See [how to approach modeling taxonomies](/guides/architecture/content-modeling/content-modeling-guide/model-taxonomies) to learn more.
### Reusable field schemas from a data perspective
Unlike traditional fields, _reusable field schemas_ give your team flexibility. Both support the same data types, but the [reusable field schemas](/documentation/developers-and-admins/development/content-types/reusable-field-schemas) are stored in the [ContentItemCommonData](/documentation/developers-and-admins/api/content-item-api/content-item-database-structure) table, making them available to every content type. Any changes to a schema also immediately propagate across every content type that references it. However, developers need to ensure that these additional fields display content as intended in the code.
### Compose semantic content types with reusable field schemas and dedicated fields
_Reusable field schemas_ rarely constitute complete semantic content types independently, though they can be part of one. Rather than forcing them into semantic content types, use them to define the smallest minor reusable data structure you want to share across multiple content types and have editors input data intended for use across different channels. This will allow your team to maintain consistency in their data while avoiding redundant work.
### Functional reusable field schemas
_Reusable field schemas_ can store some core content of semantic content types and define functional data structures that different content types reuse. We’ve already mentioned the _financial data schema_ , which editors work in any **Product** content type on the Kbank demo site.
[![Financial data reusable field schema](/docsassets/guides/store-content/cm-guide-financial-data-schema.png)](/docsassets/guides/store-content/cm-guide-financial-data-schema.png)
Similarly, you can define and use an **SEO schema** across every page content type.
[![SEO fields in Kbank demo site](/docsassets/guides/store-content/cm-seo-reusable-field-schema.png)](/docsassets/guides/store-content/cm-seo-reusable-field-schema.png)
To allow editors to add reusable parameters to URLs, developers can define a [UTM parameters](https://en.wikipedia.org/wiki/UTM_parameters) schema and assign it to different _call-to-action buttons_ , _links_ , or _email_ content types to store data they want to track into analytics tools.
Find out which data types Xperience comes with in [Data type management](/documentation/developers-and-admins/customization/field-editor/data-type-management). You will also learn about customizing the out-of-the-box set to support different data types.
### Semantic data in reusable field schemas
You’ll identify data that repeats across different content types during a content audit. This might be a _heading_ , some _summary text_ , a _reference_ to an [asset](/guides/architecture/content-modeling/content-modeling-guide/store-files), or _tags_ from a specific [taxonomy collection](/documentation/developers-and-admins/configuration/taxonomies).
These schemas represent the **Core content** that many of your content types contain. Using a reusable field schema avoids the redundancy of defining similar fields across multiple content types. You can define the **Core content schema** and integrate this schema into other content types. Semantically, they don’t specify a content type that you can find in the [schema.org](https://schema.org/). At the same time, they gather crucial information about the content the company produces, meaning they might be considered semantic.
See the _Core content schema_ shared across the products and articles on the Kbank demo site.
[![Core content schema in Kbank demo site](/docsassets/guides/store-content/cm-guide-content-types-core-content-schema.png)](/docsassets/guides/store-content/cm-guide-content-types-core-content-schema.png)
When creating _field names_ , we recommend combining the content type’s _name_ with the _field name_ , for example, _BenefitDescription_ , _ArticleName_ , or _ProductTitle_. Developers can access the field’s values through _< contentType>.Fields.<Name>_ property, for example, _page.Fields.NewsTitle_. Determining the name of the type within the property name will ease some development situations. For instance, in SQL JOINs, you won’t need aliases to disambiguate between columns from two or more tables, such as News _Title_ and Product _Title_.
### Build a flexible model with content blocks
A **content block** is a piece of content that is not a full page but still carries semantic or functional meaning within your digital content. _Content blocks_ are typically reusable across multiple pages or app screens; they can repeatedly appear in emails or can be shared externally through a headless API. Storing this content in a structured format in _Content hub_ helps your content stay consistent and accurate and reduces unnecessary duplication if editors need to share this data across channels.
_Content blocks_ :
  * Have semantic meaning (they represent a specific type of information, like an FAQ section or a call to action).
  * Can be composed of multiple other structured content items (e.g., an FAQ block contains individual FAQ items).
  * Are usually reused across your website or other channels (like email or apps).
  * Live in the _Content hub_ (in dedicated folders), in a structured format to support reuse, filtering, and consistent presentation.
  * Allow editors to insert dynamic, reusable content without duplicating work.


You can see some examples of _content blocks_ that we’ve seen in Kentico projects.
  * Collection of **FAQs** for **product** or **support** pages.
  * **Hero banners** with global **links** and **promotional messages**.
  * **Accordion sections** for structured expandable content on landing pages.
  * **Speaker** or **author** **profiles** (when you don’t need to build full author pages).
  * **Office addresses** with **office hours** for multiple location listings.
  * **Testimonial sliders** used across product and marketing pages.
  * **Feature highlights** that display key benefits in a reusable component.
  * **Call-to-action sections** encouraging sign-ups, demos, or contact requests.
  * **Event highlights** showcasing upcoming events on various landing pages.
  * **Teaser** or **Featured content** cards for articles, products, or services that help editors overwrite the default content in product or service content items.
  * **Image galleries** reused across different campaign pages.
  * **Pricing lists** displayed consistently on product or service pages.
  * **Contact information** blocks that maintain consistent formatting across regions.


### Use the built-in admin UI form components
Xperience provides different form components for content types that editors will use to input their data. Other form components may also have additional configuration options.
For example, the _Long text_ data type comes with different text-based form components, such as the _Text input_ , _Password_ , _URL_ , or _Text area_. You can use the _Rich text (HTML)_ data type with the _Rich text editor_ form component for rich text.
[![Configuring an editing component for a text field](/docsassets/guides/store-content/cm-guide-long-text-config.png)](/docsassets/guides/store-content/cm-guide-long-text-config.png)
Form components also come with various configuration options that provide different editing experiences. Developers can, for example, configure the _Rich text_ form component as _Structured content_ , _Email content_ , or _Contact notes_. Developers can also customize their configuration. The following image shows Kbank’s _Rich text editor_ configuration options; the last _News page editor configuration_ is custom.
[![Additional field configuration options](/docsassets/guides/store-content/cm-guide-content-types-configuration-rich-text-editor.png)](/docsassets/guides/store-content/cm-guide-content-types-configuration-rich-text-editor.png)
Find out more about available [Admin UI form components](/documentation/developers-and-admins/customization/extend-the-administration-interface/ui-form-components/reference-admin-ui-form-components) and the [React input type components](/documentation/developers-and-admins/customization/extend-the-administration-interface/ui-form-components/reference-react-input-components) in the documentation.
### Simplify structured content with custom data types
The out-of-the-box [data types](/documentation/developers-and-admins/customization/field-editor/data-type-management) and [UI form components](/documentation/developers-and-admins/customization/extend-the-administration-interface/ui-form-components) that you use to define editing experience represent the most common types that fit most projects. During content modeling sessions, you may realize you might need to standardize some complex content object, like multiple addresses or office hours, into a predictable structure to improve the editing experience and help with data format predictability to match the unique project’s needs.
Instead of forcing every piece of structured content to live as its own content item (or even a granular content item composed of other, smaller content items) in the _Content hub_ , you can use _custom data types_ to model content that should belong to a parent item without cluttering your reusable content library. This is especially useful for structured content critical for presentation and governance, but it doesn’t make sense to manage it as standalone, reusable content items.
_Custom data types_ mean your content can remain structured, semantically meaningful, and repeatable while avoiding the limitations of hard-coded field sets in your content type. Editors can add, remove, and reorder structured entries (like multiple addresses for a business) without leaving empty fields or copy-pasting data they need to rearrange the content. This provides a smoother, frustration-free editing experience and enables developers to control presentation and validation for each piece of structured content.
See an example of an [address custom data type](https://community.kentico.com/blog/embedded-structured-content-and-the-power-of-custom-data-types) or [UTM parameters](https://github.com/Kentico/community-portal/blob/main/src/Kentico.Community.Portal.Admin/Client/src/components/UTMParametersDataTypeFormComponent.tsx) in the [Kentico Community Portal](https://community.kentico.com/).
### Leverage linked content items
Xperience allows editors to compose their content items by combining data stored in several other content items.
Let’s examine the _Product_ content type discussed on the previous page. Highlighted fields store the actual data inputs, while the light violet fields reference other content types that hold the values.
[![Content model diagram](/docsassets/guides/store-content/cm-guide-product-content-model-diagram.png)](/docsassets/guides/store-content/cm-guide-product-content-model-diagram.png)
You can see that the _Product_ type contains fields like _Product features_ , _Product benefits_ , or _Product image_. Sure, you can create two more fields and store the values of product benefits and their icons within the _Product_. At the same time, why would you?
In real life, if we take the Kbank demo site example, more than one product will offer the same benefits, such as _included internet banking_ , _getting a quick decision_ , or _fast access to funds_.
[![Kbank demo site example](/docsassets/guides/store-content/kbank-demo-banner-example.png)](/docsassets/guides/store-content/kbank-demo-banner-example.png)
Storing the information about benefits within the individual _product_ content item restricts access to this data only within the context of this one (and only one) product item itself. If another product allows for _fast access to funds_ , editors need to duplicate the same wording and decorate it with the same icon in another product item. Duplicating the same content often leads to content management nightmares.
The benefits of creating composable content types that store only references:
  * Help with content curation.
  * Prevent editors from duplicating the same content.
  * Improve content management and long-term content curation.
  * Lead to quick content composition.


When defining the content model, discuss which types can be reused and create them accordingly. Editors will use the **Content item** data type with the **Combined content selector** form component to refer to the relevant data. At the same time, developers will retrieve the [linked items](/documentation/developers-and-admins/development/content-retrieval) and display them accordingly.
Developers also [limit](/documentation/developers-and-admins/development/content-types#add-the-option-to-link-content-items) which content types editors can select when creating their content. The _Combined content selector_ allows developers to specify which content types editors select.
When an editor selects content items they want to use, rather than copying the values from the original item to the new item, Xperience creates a reference between these records.
Reusing content items improves content maintenance. When an editor updates the referenced item, the content changes are automatically promoted to every other item that uses this content, making content management a breeze. From the developer’s perspective, developers query the linked items in their custom code and return [a collection](/documentation/developers-and-admins/development/content-retrieval/retrieve-content-items) of [references to content items](/documentation/developers-and-admins/api/content-item-api/content-item-database-structure) they can further process. For example, they can retrieve URLs of web pages to which editors want to guide their audience.
One of the most typical examples of reusable content types is a content item [assets](/guides/architecture/content-modeling/content-modeling-guide/store-files) that users store in the _Content hub_. See further examples mentioned in this material on the Kbank demo site.
### Avoid creating channel-specific content from linked content fragments
Customers who are used to displaying their content (stored in a CMS) on the website love using visual builders to create website content. Xperience offers **Page Builder** , which allows editors to assemble web pages using components like hero banners, text and image blocks, reviews, testimonials, and FAQs. For simplicity, we’ll call these components “content fragments.” These fragments aren’t semantic; they hold specific pieces of data that represent only a part of the overall information needed to convey the complete meaning of the content.
While this approach makes it easy for editors to craft entire web pages quickly in Xperience, it has limitations. The most significant advantage is speed and simplicity, but only when you’re only concerned with presenting content on a single website channel. However, this method ties the content exclusively to the specific web page, meaning the fragments can’t be easily queried or reused across different channels in Xperience. By composing their content type using widgets, editors lock their content only within the context of that specific web page.
To build a more flexible, multi-channel solution, we recommend creating semantically defined reusable content types that are made up (composed or linked) from other reusable components. This approach ensures that the content isn’t just relevant to one specific channel but can be used and adapted across different platforms. It also makes the content queryable, structured, and easier to manage in the long term, giving developers more control over its use. We’ll discuss the _Page Builder_ in detail in a [dedicated material](/guides/architecture/content-modeling/content-modeling-guide/design-website-content).
### Storing reusable content in Content hub vs. custom module classes
Both options offer unique advantages when storing [reusable content](/documentation/business-users/content-hub) in _Content hub_ or [custom module](/guides/development/customizations-and-integrations/create-basic-module) classes. From a user perspective, we recommend considering the _Content hub_ before choosing custom module classes.
The **Content hub** provides default Kentico functionality, including editing workflows, multilingual support, usage reports for improving content curation, and a built-in user interface. Additionally, the Content hub allows editors to easily manage and reuse content across various channels, making it highly flexible for developers and editors. Using Content hub often means developers don’t have to create custom UI components, saving time and effort.
A key benefit of the Content hub is its ability to integrate with APIs, enabling content creation from third-party sources. Upcoming features are expected to enhance this functionality further, making it an even more powerful tool. Using the Content hub, developers can focus on supporting content management through out-of-the-box components rather than building custom solutions, leading to quicker deployment and easier maintenance.
### Make designing content generic
Though you might want to store design-specific information, like exact color codes or sizes, inside the channel-specific content types, we do not recommend doing this. From a content longevity perspective, you want to have your design information as agnostic to the visual design as possible. When you decide to, for example, redesign your brand, it’s easier to remap the generic class name to new colors than to change the specific _#F05A22_ brand color code in hundreds of different places. The following screenshot shows styling options in the Kbank demo site.
[![Styling options](/docsassets/guides/store-content/cm-guide-section-properties.png)](/docsassets/guides/store-content/cm-guide-section-properties.png)
## What’s next?
You have learned about the options for storing non-file data in Xperience. In the [Store files](/guides/architecture/content-modeling/content-modeling-guide/store-files) section, you’ll learn the recommended practices for storing different types of files in Xperience.
![]()
[]()[]()
