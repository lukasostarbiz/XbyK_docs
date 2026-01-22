---
source: https://docs.kentico.com/guides/architecture/content-modeling/model-website-presentation-components/model-page-components
scrape_date: 2026-01-22
---

  * [Home](/guides)
  * [Architecture](/guides/architecture)
  * [Content modeling](/guides/architecture/content-modeling)
  * [Model website presentation components](/guides/architecture/content-modeling/model-website-presentation-components)
  * Model page components 


# Model page components
When defining the content model, teams often forget to pause to identify content types that play crucial roles in their planned customer journeys across different channels.
In this article, you’ll learn to:
  * Align your website’s presentation layer with your content strategy to support marketing goals.
  * Define different presentation components to help you display data effectively on your website, drive conversions for specific products, or generate new leads with articles.
  * Use page templates, sections, and widgets to create pages and tailor content based on visitors’ previous activities.


## Prerequisites
You should have basic knowledge of [content modeling principles](/guides/architecture/content-modeling/content-modeling-guide).
## Align your presentation layer with your content strategy
Imagine your website as a stage and the presentation layer as the spotlight. Here’s where you can tailor the customer experience. If you have correctly defined the components, you can guide visitors toward their intended actions by identifying specific areas in this layer.
The content model defines a conceptual framework that describes how data elements interact within the system. The content model outlines how editors will capture the data, store it in the system, what are the relationships between the data and retrieval rules to display it to customers. This model ensures clarity and consistency, guiding the organization’s data infrastructure. If you are not familiar with Xperience by Kentico, please find out more about [content modeling](/guides/architecture/content-modeling/content-modeling-guide) in our documentation.
Know your goals and desired outcomes before defining what components you need to display your content on the website. Without understanding your content strategy, the presentation layer you’ll define for your data will be incomplete.
Since you have done your content modeling sessions, you already know which types help you build your pages in your website channel.
We recommend designing website pages as a wrapper over a reusable content type stored in the content hub. The page wrapper or page envelope stores only the data that make sense for website page-specific content, such as SEO data and search engine instructions, like no-index directives.
**Wrapper page** |  **Article reusable content type** |  **Product content type**  
---|---|---  
[![Article page content type](/docsassets/guides/model-page-components/sketch-outline-article-page-content-types.png)](/docsassets/guides/model-page-components/sketch-outline-article-page-content-types.png) |  [![Reusable Article content type](/docsassets/guides/model-page-components/article-content-type-diagram.png)](/docsassets/guides/model-page-components/article-content-type-diagram.png) |  [![Reusable Product content type](/docsassets/guides/model-page-components/product-content-type-diagram.png)](/docsassets/guides/model-page-components/product-content-type-diagram.png)  
Talking to our customers, we’ve seen a familiar pattern: teams not using or thinking of their content in the omnichannel mode often start with the presentation layer and define typical “pages” you’ll see in your website sitemap. [Information Architecture vs. Sitemaps: What’s the Difference?](https://www.nngroup.com/articles/information-architecture-sitemaps/) Missing the data modeling exercise that helps make the content modular, reusable, and future-friendly locks them into old patterns, leading to content duplicity, complex maintenance, and subpar content curation experience.
## Think of customer journey and conversions
Let’s look at two content types on our Kbank demo sites and see their target goals.
**Article page** |  **Product page**  
---|---  
  * Drive conversions when promoting a specific product, like a card, or a banking service, like insurance.
  * Generate new leads or nurture interest so visitors become leads with articles about small business successes.
  * Educate or inspire existing clients with articles that help them pick the right product for their needs.
  * Increase brand visibility and credibility.

| 
  * Appropriately communicate the product’s value by displaying the correct information about the product.
  * Deliver information about the product that resonates with the target audience by using many visual elements leveraging reusable content.
  * Target your product persona better with personalized messaging.

  
With a little bit of prototyping (and later, by experience), you’ll soon identify website components that will help your team achieve their goals, such as:
  * Adding calls to action to guide people through their journey.
  * Showing and revealing relevant information depending on the page and circumstance.
  * Personalize content.


## Define presentation components
Kentico comes with a user-friendly interface called Page Builder that allows non-technical users to build pages from configurable page templates, sections, and widgets prepared by the developers. Editors can create pages, each with a different layout, without the help of developers. Using content personalization, they can also tailor the page’s content based on visitors’ previous activities.
**Page templates** define the overall look of the page. Developers build the templates to display content, and editors assign them when they create new or change the layout of existing pages. Editors can also save template configurations into page presets and speed up content creation.
  * **General purpose page templates** display data stored in [a structured format](/guides/architecture/content-modeling/content-modeling-guide/structured-and-unstructured-content). Editors input content through [unified form fields](/guides/digital-marketing/xperience-essentials/manage-content-hub-essentials) in the admin UI, and the template shows the content dynamically. The website can have multiple templates to display the same content differently, or each template can have specific properties to adjust the page layout. Find out more about [page templates](/documentation/business-users/website-content/page-templates) in Kentico documentation.
  * **Page Builder templates** display data through the Page Builder widgets and provide editors with great layout and design options. Find out more about [page templates with Page Builder](/documentation/developers-and-admins/development/builders/page-builder/page-templates-for-page-builder) in the documentation.


Even if the page templates display structured content dynamically, we recommend that developers enable Page Builder.
**Sections** define the page’s **vertical and horizontal layout** with Page Builder. They determine areas where editors place the content in widget zones. Sections can have properties editors use to define what the sections with widget zones look like, for example, their background and the number of columns where editors input widgets or adjust whitespace around individual content pieces.
**Widgets** are the most granular Page Builder components. Editors use them to add or display content on the page or to add specific functionality. Widgets are reusable, and different pages or website channels can share the same set of widgets. This dramatically helps the editing experience. Your team gets used to how to create pages in one channel, and they can quickly transfer this knowledge when your company decides to introduce other new website channels, for example, campaign microsites.
Similarly to other Page Builder components, developers prepare widgets to **display reusable content from the Content hub** or **allow editors to create one-off content** for individual pages. Based on the project requirements, each widget can have its properties that editors configure which data to display and what it will look like on the page. Editors can also create personalized variants of the widget’s content to tailor the page experience to specific visitor segments.
We recommend that developers build page templates with editable properties editors will use to create their pages. To make their content future-friendly, editors should store it in the structured format and use widgets to display it on the website.
## Identify individual page components
Let’s take a look at two pages on the Kbank website. 
**Article reusable content type** |  **Product content type**  
---|---  
**Article page template** |  **Product page template**  
[![Reusable Article content type](/docsassets/guides/model-page-components/article-page-components.png)](/docsassets/guides/model-page-components/article-page-components.png) |  [![Reusable Product content type](/docsassets/guides/model-page-components/product-page-components.png)](/docsassets/guides/model-page-components/product-page-components.png)  
## Define editable properties for individual components
Let’s take a look at individual component and their properties. The following list describes individual widgets and their properties, which you can find on the Kbank demo site. It represents one of the options, and you can approach your content creation differently.
The following section shows different Page Builder components and their configuration options. You can see them in action on the Kbank demo site.
## Page templates
The page templates in Kbank are tied to a specific content type.
The **Article page template** displays article data stored as [reusable content](/guides/architecture/content-modeling/model-reusable-content/model-a-reusable-article) and contains the Page Builder, where editors can further promote their marketing content.
[![Article page template](/docsassets/guides/model-page-components/article-page-template.png)](/docsassets/guides/model-page-components/article-page-template.png)
**Product page template** displays data stored in a [reusable Product content type](/guides/architecture/content-modeling/model-reusable-content/model-a-reusable-product). Editors create the page using the template and add widgets to the page. Product-specific widgets contain a special property, and product display data is displayed directly on the page; editors don’t need to make any selections. Editors can speed up their work and create a custom product page preset with the default widget configuration. Preconfigured widgets automatically display the product’s data when they select a reusable product from the Content hub.
[![Product page template](/docsassets/guides/model-page-components/product-page-template.png)](/docsassets/guides/model-page-components/product-page-template.png)
## Sections
**Multicolumn section** component provides editors with the most configuration options and is the most commonly used section type across the Kbank ecosystem.
[![Multicolumn section](/docsassets/guides/model-page-components/multicolumn-section.png)](/docsassets/guides/model-page-components/multicolumn-section.png)
**Articles column section** is optimized to work properly in the Page Builder area in the Article page template. It contains hand-selected widgets tailored to fit the size of the editable area and helps editors effectively continue marketing conversations with website visitors.
[![Article column section](/docsassets/guides/model-page-components/article-column-section.png)](/docsassets/guides/model-page-components/article-column-section.png)
**Form section** and its configuration options ensure that editors add forms the same way across the whole website.
[![Form section](/docsassets/guides/model-page-components/form-section.png)](/docsassets/guides/model-page-components/form-section.png)
**Single (or 1-) column section** contains a single property. On the Kbank demo site, you can see that the 1-column section exhibits two behaviors - when placed into the body of the page, editors can pick any widgets. When they place the section at the top of the Product page, [restrictions](https://community.kentico.com/blog/defining-editable-area-restrictions) applied to the template (not on the section level) to ensure editors can use only the _Banner_ widget. We’ll discuss recommended practices for sections in a following guide.
[![Single column section](/docsassets/guides/model-page-components/one-column-section.png)](/docsassets/guides/model-page-components/one-column-section.png)
The restrictions on the top zone allows editors to add only the _Hero banner widget_.
[![Section restrictions allow only Hero banner widget](/docsassets/guides/model-page-components/banner-widget.png)](/docsassets/guides/model-page-components/banner-widget.png)
## Widgets
The following section shows different configuration options for widgets that you can find on the Kbank demo site. Using these widgets, editors can create website pages using both [structured content or one-off content](/guides/architecture/content-modeling/xperience-content-modeling-basics/differences-between-structured-content-and-page-builder-content). We’ll cover defining widgets and recommended practices in one of the following guides.
### Hero banner widget
[![Hero banner widget in Kbank demo site](/docsassets/guides/model-page-components/hero-banner-widget.png)](/docsassets/guides/model-page-components/hero-banner-widget.png)
### FAQ widget
[![FAQ widget in Kbank demo site](/docsassets/guides/model-page-components/FAQ-widgets.png)](/docsassets/guides/model-page-components/FAQ-widgets.png)
### Image widget
[![Image widget in the Kbank demo site](/docsassets/guides/model-page-components/image-widget.png)](/docsassets/guides/model-page-components/image-widget.png)
### Product widget
[![Product widget in Kbank demo site](/docsassets/guides/model-page-components/product-widget.png)](/docsassets/guides/model-page-components/product-widget.png)
### Page heading widget
[![Page heading widget in Kbank demo site](/docsassets/guides/model-page-components/page-heading-widget.png)](/docsassets/guides/model-page-components/page-heading-widget.png)
Unlike the previous widgets that allowed also for reusing content, the **Page heading widget** creates one-off content as it stores all the data editors input in the page.
## Ensure page components support content personalization
Xperience comes with a powerful segmentation tool called contact groups.
You can break down your audience into segments based on their shared characteristics, such as the same demographic information, like age or location, or interests, for example, consuming the same content on your website. You can then accurately target different contact groups with tailored content on the same page to improve your marketing efforts. Find out more about [contact groups](/documentation/business-users/digital-marketing/contact-groups) in the documentation.
Not all widgets have to support content personalization. Save time and implementation costs, look into your customer journey and the touch-points you want to define on your website and define only the necessary content components that support content personalization in the project requirements.
## Simplify content creation to help your marketing team
The way you break down editing experience page components needs to fit your team’s experience.
  1. **Leverage Your Expertise** : Draw upon your team’s experience and help developers and business analysts define the components. Your team insights are invaluable in shaping practical page components - and representatives (or stakeholders) from content editing must be part of the content modeling sessions.
  2. **Validate Continuously** : Regularly assess and validate your chosen components and approach. Every developer working on the presentation layer should try creating several pages before inviting editors. Gather the feedback from test sessions and implement changes to optimize every component.
  3. **Prioritize Best Practices** : Every page should be built with a page template so that editors can save their preferred configuration. Follow this principle. At the same time, apply your knowledge of responsive design to ensure page templates work correctly and set up restrictions on components to ensure the page’s accessibility. Add explanations and instructions to help editors quickly understand what each property serves and what data is expected of them. (See the green explanations in screenshots above.)
  4. **Iterate Thoughtfully** : Defining the presentation model (as well as the whole content modeling) is an iterative process. Refine and enhance templates and other Page Builder components based on real-world usage and user needs.


## Page components in a nutshell
Align the presentation layer with your content strategy to help editors tailor the customer experience and guide them toward their intended actions. When you understand your marketing goals and know what common data types you’ll be working with, you can define the components your team will use to effectively display the content on the website channels.
The Kentico’s Page Builder will help your team create dynamic, personalized pages that resonate with your target audience. At the same time, your content will be stored in a way that makes reuse and content curation easier.
Ensure your page components support content personalization, and regularly validate if all the page components and how editors use them follow Xperience by Kentico best practices. Ultimately, your editors aim to create an engaging user experience that drives conversions and nurtures leads.
## What’s next?
In the following guide in this series, you’ll dive deeper into thinking about [sections](/guides/architecture/content-modeling/model-website-presentation-components/model-page-builder-sections). Besides diving into different options for section properties, you’ll learn some recommended practices and things to consider when modeling sections.
[Continue to the next guide](/guides/architecture/content-modeling/model-website-presentation-components/model-page-builder-sections)
![]()
[]()[]()
