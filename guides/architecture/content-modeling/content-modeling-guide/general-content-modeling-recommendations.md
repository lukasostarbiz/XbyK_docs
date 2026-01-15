# General content modeling recommendations
  * Concept| [ Copy page link ](guides/architecture/content-modeling/content-modeling-guide/general-content-modeling-recommendations#) | [Get HelpService ID](guides/architecture/content-modeling/content-modeling-guide/general-content-modeling-recommendations#)
Core MVC 5


[✖](guides/architecture/content-modeling/content-modeling-guide/general-content-modeling-recommendations# "Close page link panel") [Copy to clipboard](guides/architecture/content-modeling/content-modeling-guide/general-content-modeling-recommendations#)
## About this guide
This page is a part of the **Content modeling guide** which you should follow sequentially from [beginning to end](guides/architecture/content-modeling/content-modeling-guide). You can also follow this series in [Content modeling guide](modules/content-modeling-guide), which helps you keep track of your progress as you move through this sequential material.
## Plan your content model around business needs
A good content model that matches your business’s unique requirements and challenges is vital to a successful online presence. When deciding how to model content in Xperience, always respect the project’s business requirements and the preferences of website editors and marketers.
On this page, you can learn about general recommendations that you or your developers should consider when planning the content model for your project.
## Digital marketing features
Xperience comes with a set of digital marketing features. Marketers can use these features to attract new customers, promote content, or automate workflows to keep in touch with existing customers. Using digital marketing can directly impact your content model and how content editors work with the content.
Find out more about the digital marketing features in the [documentation](documentation/business-users/digital-marketing).
**Recommendations – Digital marketing**
  * If you want to leverage the built-in digital marketing features, **store your content in the Content hub** and **use Page Builder widgets and page templates to display it**.
  * We recommend using Page Builder on pages where your editors benefit from [content personalization](documentation/business-users/digital-marketing/widget-personalization). Marketers can personalize their content based on contact groups across the website. 
    * Typical examples of content personalization include **frequently changed pages** , such as the website’s home page, campaign landing pages, or thank-you pages. Marketers create variants of content that is dynamically displayed to different visitors based on visitors’ previous interactions with the website and business.
    * Less common scenarios include personalization on **pages that display aggregated content**. For example, an _Articles_ listing page can contain an editable area with a _Featured articles_ widget. Editors can personalize the widget and offer different featured articles to visitors based on their assigned contact groups or based on the phase of the visitors’ contact journey.
    * Developers need to **prepare custom Page Builder widgets** that allow editors to personalize content based on contact groups.
  * Use custom content assets type and **store your images, PDFs, videos, and other similar media in the Content hub**. If you need to upload large amounts of data in bulk, use [mass asset upload](documentation/business-users/content-hub/content-item-assets#mass-asset-upload).
  * For collecting data from users, we recommend using **built-in[forms](documentation/business-users/digital-marketing/forms)**. Ensure that your editors have rich options for customization of their forms via custom [Form Builder components](documentation/developers-and-admins/development/builders/form-builder). Include requirements for custom form components, their styling properties, or custom form section properties in your project requirements. Once developers prepare your project, editors can adjust their forms’ layout to match the rest of the pages.
  * We recommend using the [Data protection](documentation/developers-and-admins/data-protection) and [cookies](documentation/developers-and-admins/data-protection/cookies) functionality to ensure your website complies with relevant legislation.


## General content modeling recommendations
**Routing and URLs**
We recommend using [content tree-based routing](documentation/developers-and-admins/development/routing/content-tree-based-routing) for most scenarios. With content tree-based routing, the system generates ULRs for pages based on their position in the content tree. The content tree-based routing also allows editors to easily [change the URL slugs of their pages](documentation/business-users/website-content/manage-page-urls) and store the URL history in the **Former URLs** application. 
Sometimes, editors use characters considered suboptimal (or even invalid) for URLs. For example, some accented characters from extended Latin alphabets, such as _ö_ , can be encoded into SEO-suboptimal URL slugs. We recommend building [custom field validation](documentation/developers-and-admins/customization/extend-the-administration-interface/ui-form-components/ui-form-component-validation-rules) on any URL-related page type fields and constraining forbidden (or suboptimal) URL characters. To validate custom URL fields, your code can reference the _Settings_ for URLs in the administration. You can use the `ISettingsService` and [retrieve specified URL restrictions](documentation/developers-and-admins/configuration/settings).
**Aim for content reusability**
Follow a simple rule of thumb: if there is a chance that specific content will appear on the same website in multiple places or various channels of marketing communication, use structured content and **store your reusable content items in the Content hub**.
**Ensure a seamless editing experience**
When you prepare the structured content hierarchy, we recommend that every developer participating in the project steps into the content editor’s shoes and tests their workflow by creating several pages in the content tree. Developers should try to understand how editors will work with the website content. Knowing the inner workings of their implementation, they can suggest changes to improve the editing experience significantly. Talking from experience, you can often:
  * Slightly flatten the content tree hierarchy.
  * Convert large, monolithic content types that aggregate various content units storing different types of data into smaller, separate content types. Using these smaller content types, editors will compose content, such as campaign pages or app screens.
  * Use content tree predominantly only for navigable items with URLs.
  * Guide editors on how they should work with specific content types or what kind and format of data they should input in the content items. 
    * We recommend **specifying editor guidance** in the project requirements. To build a flexible and scalable content model, use editor guidelines to strengthen the content reusability and independence. It helps strengthen its reusability when you concentrate on what the content is rather than on its presentation. Your guides need to help editors focus on the type of content they should input rather than describing what their content will look like on the website.
    * Use **different icons** to provide visual guidance and distinguish between different content types.
    * Use the **Tooltip text** or **Text below the input** settings for content type fields to provide editor guidance.
    * As Page Builder is preferably used to present the information rather than store it, use **Label** and **Explanation text** widget and section properties to help editors understand the data their content needs and what will happen once displayed in the dedicated channel.
  * If your editors use widgets to display structured content, **set restrictions against a specific content type** within the widget properties.
  * Help editors quickly compose their pages. Request that developers set restrictions on editable areas, sections, and widget zones to limit confusion about which widget to choose. Use appropriate components to ensure editors cannot stray and input widgets in places where these widgets don’t belong. Setting up these fences helps with the content editing experience and ensures brand consistency.


**Ensure seamless content workflow**
Make sure that editors have complete control over their content. **Preview** tab in website channel applications allows editors to see what their content looks like before publishing.
We recommend that
  * Editors create and manage their content on the production version of the application,
  * Developers use a pre-production UAT environment to create, test, and prove their code changes before synchronizing it with the production site.


## Model navigation
Website navigation helps users find the content they are looking for. Well-ordered navigation menus provide an overview of how the website’s content or its segments are structured. It can also keep users engaged and lead them to explore more of the site.
There are different approaches to modeling navigation that you can consider. The following text highlights the recommended approach. At the same time, your project requirement might differ and you’ll need to reach for a different approach for modeling navigation. That’s why we have collected examples suitable for Xperience projects into a dedicated [model website navigation](guides/architecture/content-modeling/model-website-content-types/model-website-navigation) guide.
We recommend modeling website navigation via reusable [content items](documentation/business-users/content-hub). Developers prepare dedicated content types for individual _Navigation items_ as well as for the _Website navigation_ as a whole. Editors then manually build the website navigation in the content hub. Based on the site’s designs, they create the hierarchy and desired order of individual menu items that developers then display in the relevant locations. The editors can also organize the content items used for website navigation into [folders](documentation/business-users/content-hub/content-hub-folders#content-folders) in the content hub. This way they can make the menu hierarchy more visual and easily see in which channel application the navigation items are used.
What about modeling navigation using dedicated [website pages](documentation/business-users/website-content) without URLs within the content tree which was common in Kentico 13 and older versions?
In Xperience, this approach is less optimal, as it clutters the content tree with items that do not represent actual pages on the live site, which can be confusing to your content editors. See the [guide about navigation](guides/architecture/content-modeling/model-website-content-types/model-website-navigation) to learn about additional options and practices to follow if you decide to place navigation into the content tree.
You can also ask developers to build a custom _Menu widget_. Editors can place the widget into appropriate section and select which content items the widget processes into a submenu. Similarly, you can request a custom _Breadcrumbs menu_ widget. When an editor places the widget onto the page, the widget automatically displays the breadcrumb path to the page’s location.
Depending on your project’s requirements, the _Navigation item_ content type may contain some or all of the following properties:
  * Page to link to – a page from the content tree that is selectable using the [Page selector](documentation/developers-and-admins/customization/extend-the-administration-interface/ui-form-components/reference-admin-ui-form-components). The selector populates its assigned field with the GUID of the selected page, which developers can use to [retrieve the URL](documentation/developers-and-admins/development/content-retrieval/retrieve-page-content/retrieve-page-urls) of the linked page.
  * Depending on the project’s requirements, you may want to add some of the following fields: 
    * External link 
      * Developers can use the [URL validation rule](documentation/developers-and-admins/customization/extend-the-administration-interface/ui-form-components/ui-form-component-validation-rules) to ensure the URLs passed by editors are valid.
    * Link title
    * Link caption
    * Link icon
    * Additional properties (e.g., open in a new tab)


The _Website navigation_ content type then contains all the _Navigation items_ that are displayed in the navigation menu.
For more information and a sample implementation, see [this page of our Developer kickstart](guides/development/developer-kickstart/model-navigation). For complex overview of approaches to modeling navigation, see a dedicated guide [Model website navigation](guides/architecture/content-modeling/model-website-content-types/model-website-navigation).
## Website system pages
A good website administration experience doesn’t only mean that editors can easily create and manage website content. A well-structured website offers editors ways to adjust system-specific features without asking developers for help whenever they need to change the website’s SEO performance. For example, you can create a ”Website settings” section in the content tree and create specific page types that content administrators can use to change system settings. We recommend including the following system pages in your project:
  * [Search result pages](guides/architecture/content-modeling/content-modeling-guide/general-content-modeling-recommendations#search-result-pages)
  * [Consent settings page](guides/architecture/content-modeling/content-modeling-guide/general-content-modeling-recommendations#consent-settings-page)
  * [SEO-specific pages](guides/architecture/content-modeling/content-modeling-guide/general-content-modeling-recommendations#seo-specific-pages)
    * [Sitemap](guides/architecture/content-modeling/content-modeling-guide/general-content-modeling-recommendations#sitemap)
    * [Error pages](guides/architecture/content-modeling/content-modeling-guide/general-content-modeling-recommendations#error-pages)
    * [Robots.txt and no-indexing content](guides/architecture/content-modeling/content-modeling-guide/general-content-modeling-recommendations#robots.txt-and-no-indexing-content)


### Search result pages
Xperience doesn’t currently provide any solution out of the box that will search through website data and display results on an internal search results page. To implement search functionality for the website, use a custom solution.
**Recommendations – Search**
Considering your project requirements, we recommend using one of the available integrations with [Algolia Search](https://github.com/Kentico/xperience-by-kentico-algolia), [Lucene Search](https://github.com/Kentico/xperience-by-kentico-lucene), or [Azure AI Search](https://github.com/Kentico/xperience-by-kentico-azure-ai-search).
### Consent settings page
Xperience comes with extensive [Data protection](documentation/developers-and-admins/data-protection) functionality that allows for building websites compliant with many different e-privacy laws and legislation.
Administrators can use the data protection features to create and manage tracking consents or easily remove personal data from the administration UI. Once the personal data is in the system, marketers can easily use the built-in Xperience online marketing features, e.g., ensure that marketing emails are sent only to visitors who provided appropriate consent.
To comply with data privacy regulations, developers must implement data collection and erasure features to match project requirements and accommodate how the website gathers, processes, and stores personal data.
Find out more about customer data protection in Xperience:
  * [Customer data management](documentation/business-users/digital-marketing/contact-management)
  * [Data protection](documentation/developers-and-admins/data-protection)


**Recommendations – Consents**
To ensure marketers stay within legal boundaries with their marketing activities, we recommend leveraging the Xperience data protection functionality for collecting and managing every piece of visitor data in the customer life-cycle, from cookie tracking up to data submitted through online forms. We recommend building a consent settings page to allow users to manage their consent.
### SEO-specific pages
#### Sitemap
Sitemaps help search engines correctly index website content and significantly affect search rankings. You need to create a custom generator to build a website sitemap.
**Recommendations – SEO**
We recommend doing a custom implementation of the sitemap generator, including a mechanism that enables editors to decide which page URLs to include in the sitemap and a way to canonicalize URLs. For example, add a custom URL canonical field to the page’s **Content** tab.
#### Error pages
Errors can appear on different occasions. Visitors can request content that’s no longer available on the page, or they can make a typo in the URL. A user-friendly error page and simple website navigation can improve user experience and guide them to relevant content. Xperience uses MVC conventions for handling error pages.
**Recommendations – Errors**
Give editors the power to personalize the content of error pages by enabling Page Builder. It can help if your website contains a lot of content and the menu needs to provide quick navigation. The most frequently used error pages are:
  * 404 - Page Not Found.
  * 403 Forbidden - You do not have permission to access this page.
  * 400 Bad request - Your browser performed an invalid operation.
  * 500 Internal server error - The server encountered an error while processing your request.


#### Robots.txt and no-indexing content
Website administrators sometimes want to prevent search engine agents from indexing specific pages or parts of the website. The _Robots.txt_ page can help in such situations.
**Recommendations - Indexing**
We recommend providing editors or administrators with means to disallow parts of the website from search engines:
  * Add dedicated fields on the _Content_ tab to disable indexing of individual website pages. Selecting the fields should include the no-index directive in your output markup.
  * To disable indexing parts of the website, create a dedicated page (or a section in a custom module) for _Robots.txt_ file with, e.g., a _Text area_ form control and use custom code to process the directives administrators want to input into the Robots.txt. The implementation could also restrict the website’s indexing by specific search bots.


## Additional resources about content modeling
If you are new to content modeling, we have collected the following list of third-party resources that cover this important topic.
  * HeadlessCreator weekly 
    * <https://www.headlesscreator.com/course/content-modeling-weekly>
  * Content modeling free e-book 
    * <https://contentmodeling.com/index.php/free-e-book/>
  * Kontent: What is content modeling 
    * <https://kontent.ai/learn/model/intro-to-content-modeling>
  * Sanity’s Content Modeling Guide: 
    * <https://www.sanity.io/content-modeling>
    * <https://www.sanity.io/content-modeling/content-modeling-foundations>
    * <https://www.sanity.io/content-modeling/how-to-create-a-content-model>
    * <https://www.sanity.io/structured-content>
  * Contentful’s content modeling basics: 
    * <https://www.contentful.com/help/content-modelling-basics/>


## Conclusion
Even though content models show similarities across different organizations, every model is unique. Projects where editors and other website stakeholders collaborate on defining, are more successful in results and user satisfaction.
The key points for building content models are:
  * Prefer structured content and use widgets for styling rather than storing the content in Pages.
  * Depending on the skillset of the editor team, consider breaking up complex content types into several smaller types to improve content reusability and overall content composing experience.
  * Regularly validate your model with stakeholders, both internal and external.
  * Website developers should use the created content model and create content on the website themselves.
  * Feel free to experiment or deviate from the recommendations above to satisfy the project requirements.


Happy content modeling!