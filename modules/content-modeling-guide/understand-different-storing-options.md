---
source: https://docs.kentico.com/modules/content-modeling-guide/understand-different-storing-options
scrape_date: 2026-01-22
---

Module: Content modeling guide
9 of 28 Pages
# Understand how each content type stores and uses data
The diagram [above](/modules/content-modeling-guide/understand-storing-data-in-content-type) shows where different storage layers and content relationships are in Xperience. In the following sections, we’ll look at each channel and the _Content hub_ in detail and explore how editors can store their data.
### Website channel content
In a website channel, editors create content using the [Page Builder content](#page-builder-content) that allows flexible editing of the layout and content of pages, as [structured pages](#structured-pages) that store content into fields without including presentation information and support consistent reuse and delivery. They can also display [reusable content](#reusable-content-in-content-hub) from the _Content hub_. Pages in the website channel are displayed in a visual hierarchy called the [content tree](/guides/digital-marketing/xperience-essentials/manage-website-content-essentials#content-tree-represents-the-hierarchy-of-website-pages).
#### Page Builder content
[Page Builder](/documentation/developers-and-admins/development/builders/page-builder) allows non-technical users to manage the content and layout of their pages with templates, sections, and widgets. _Page Builder_ isn’t intended to store all website content.
  * _Page Builder_ allows editors to manage the content and layout of select pages using a WYSIWYG editor.
  * Editors control page layout using [widgets](/documentation/business-users/website-content/widgets-and-page-builder) and [sections](/documentation/developers-and-admins/development/builders/page-builder/sections-for-page-builder) – reusable and configurable content prepared by the developers. Editors can dynamically alter the page’s design without developer assistance.
  * We recommend using _Page Builder_ in all website pages where editors need to [personalize content](/documentation/business-users/digital-marketing/widget-personalization) to help website visitors on their customer journey.
  * _Page Builder_ works well for **one-off or quickly degrading content** that needs adjusting. It helps when editors need **WYSIWYG-style editing**.
  * Use it when you **don’t need to reuse the same piece of content in multiple places** (except for widgets that allow for presenting structured content; see below).
  * _Page Builder_ content is stored in a [dedicated database column](/documentation/developers-and-admins/api/content-item-api/content-item-database-structure#content-items) as a [JSON string](/modules/content-modeling-guide/choose-the-right-approach-for-storing-content).


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


Use structured emails when editors need to create emails fast, and Email Builder when they need a flexible interface to support their message through email layouts. You’ll learn more about [designing email content](/modules/content-modeling-guide/design-email-content).
### Headless content
Editors create [content](/documentation/business-users/headless-content) in [headless channels](/documentation/developers-and-admins/configuration/headless-channel-management) when they want to deliver their digital content to external channels through a headless API (GraphQL). They can manage content for mobile apps, kiosks, web apps, and other connected devices within the Xperience ecosystem.
  * Headless content stores data in a presentation-agnostic format, ready for use across multiple platforms.
  * Data is served in JSON format for consumption by headless applications and services through [Xperience headless API](/documentation/developers-and-admins/development/content-retrieval/retrieve-headless-content).
  * Editors typically create items for presentation units, such as app screens, display windows, or navigation elements.
  * Headless content is ideal for scenarios such as mobile screens, information kiosks, TV displays, or AR experiences.


Headless content isn’t meant to be the primary method for working with content. It fits into the content system as part of Xperience’s [hybrid-headless approach](https://www.kentico.com/platform/development), combining headless delivery with traditional CMS features.
[ Previous page ](/modules/content-modeling-guide/understand-storing-data-in-content-type)
9 of 28
[ Mark complete and continue ](/modules/content-modeling-guide/choose-the-right-approach-for-storing-content)
![]()
[]()[]()
