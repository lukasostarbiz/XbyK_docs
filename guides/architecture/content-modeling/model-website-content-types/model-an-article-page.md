---
source: https://docs.kentico.com/guides/architecture/content-modeling/model-website-content-types/model-an-article-page
scrape_date: 2026-01-26
---

  * [Home](/guides)
  * [Architecture](/guides/architecture)
  * [Content modeling](/guides/architecture/content-modeling)
  * [Model website content types](/guides/architecture/content-modeling/model-website-content-types)
  * Model an Article page 


# Model an Article page
In the dynamic landscape of digital content, even organizing and presenting content articles on your website can help capture and retain your audience’s attention.
In this guide, you’ll learn:
  * How to model an Article page that displays articles from reusable content.
  * Which fields to include to prepare article content type for SEO.
  * About modeling design components that allow your editors to display the article and related content.


Once finished, you’ll know how to model the Article website page similar to the page below. You’ll see how to keep the model flexible and scalable to meet your team’s business needs and improve your editors and customers’ experience.
[![Article page layout](/docsassets/guides/model-an-article-page/article-page-layout.png)](/docsassets/guides/model-an-article-page/article-page-layout.png)
## Prerequisites
You should have basic knowledge of [content modeling principles](/guides/architecture/content-modeling/content-modeling-guide).
## Start with information learned during your content audit of all your website channels
Let’s play out the following scenario:
Your marketing team manages websites in several languages. They publish weekly updates and press releases or share your customer studies and interviews with stakeholders on different websites. They sometimes reuse some of these general-purpose articles, like interviews with clients or relevant press releases, across multiple websites. Besides, they create campaign pages to increase conversions, grow the leads’ database, or support your current customers. They also want to display different listings of articles based on the person reading them or showing the latest articles from specific categories. 
Reusing the same content on different websites means that your team will need to have a way to handle search engines to prevent any SEO issues like duplicate content penalties or indicate that search engines should not index specific pages.
The Kbank site this guide mentions several times is an Xperience by Kentico demo application. You can find more about the application in the Business tutorial and even [request your instance Kbank hosted on our servers](https://www.kentico.com/get-started/hosted-trial).
Some examples discussed in this guide do not appear in the Kbank demo site, for example, the priority field for sitemap.xml files.
## Consider business scenarios for article pages
Looking at your team’s requirements, you’ve defined the following criteria for handling article pages. Editors on your team need to:
  * Use different page templates to show the same article on different websites.
  * Add the same article under another title and descriptions on other websites.
  * Inform search engines that some of the articles were published on different websites.
  * Create an article listing page where editors can personalize which articles are displayed.
  * Display collections of articles covering similar topics or targeting the same audience.
  * Help search engines better understand the website content with language-specific sitemaps.
  * Add social media-specific information when editors are creating the article.
  * Promote articles on similar topics or a product the article covers alongside the published article.


Learn more about what business requirements you might want to consider when you define your project’s content model in [Begin with business needs](/guides/architecture/content-modeling/content-modeling-guide/content-modeling-business-requirements) available in the Content modeling guide.
These requirements dictate which data you need to store and what components your editors will need to build the pages.
[![Quick outline of components to build the Article page type.](/docsassets/guides/model-an-article-page/sketch-outline-article-page-content-type.png)](/docsassets/guides/model-an-article-page/sketch-outline-article-page-content-type.png)
As some of the topics related to, for example, SEO or page design can get technical, you might want to consult your subject matter experts or specialists in respective fields.
## Consult required data with stakeholders and SEOs
Let’s say you have done your due diligence and have your facts straight. Here are the findings you’ve learned.
Info from **Senior editors**
  * The editor team should create articles using the [reusable article items](/guides/architecture/content-modeling/model-reusable-content/model-a-reusable-article) from the Content hub.
  * Any _reusable article_ content item contains a default set of related articles. However, the editors should be able to adjust which articles are displayed alongside the published article or personalize the articles for different audience segments.
  * Editors will need to personalize relevant Featured articles to the page and link to promoted products using widgets.


Info from **SEO Experts**
  * You will need to store the traditional field, such as: 
    * Page title (SEO title) for the search result page.
    * Page description.
    * Author information.
  * SEOs requested the following fields to handle traditional SEO issues: 
    * Remove page (URL) from sitemap.
    * Means to inject a “no-index” directive to prevent search engines from indexing the content.
    * A way to prevent showing content in rich text results (_nosnippet_ meta tag).
    * A field to define the page’s canonical URL. 
To inform search engines that editors republished the same article, editors need to canonicalize the URL. To learn more about canonicalization, find out more in Ahrefs’ article [Canonical Tags: A Simple Guide for Beginners](https://ahrefs.com/blog/canonical-tags/) and in [What is URL Canonicalization](https://developers.google.com/search/docs/crawling-indexing/canonicalization) in Google documentation. 
    * To help with SEO, the articles will need to include structured data. The article needs to contain an article [schema markup](https://developers.google.com/search/docs/appearance/structured-data/article#json-ld), including: 
      * The article’s published and modified date.
      * The article’s URL.
      * The thumbnail image URL.
This demo article doesn’t worry about two other optional sitemap.xml attributes for URL priority and change frequency, as the major search engines don’t care much about them. Use the [sitemaps.org](http://sitemaps.org) - Protocol to learn more about the sitemaps and their format. Also, consult with local SEOs in the markets you’ll be servicing to determine which sitemap data the local search engines prefer to include. 
  * Social media data 
    * OG: Title
    * OG: Description
    * OG: Image
Your editor must provide open-graph meta tags to improve your article data display across different social media channels. In Kentico, we have seen that customers often group OG tags with SEO fields covered above, even though they play a different role. Developers will ensure that some attribute metadata, such as article’s URL (`og:url`) or type (`og:type`) will populate automatically from system data. 


Info from **Developers/Solution Architects** :
  * Xperience exposes helpful website channel-specific metadata, and developers can pull this information dynamically into website page markup, sitemap.xml, or the document’s rich text data.
For simplicity, let’s call these attributes metadata. To learn more about content metadata, see [6 Types of Metadata and Their Uses](https://www.spiceworks.com/tech/devops/articles/what-is-metadata/). 
  * Publish date
    * Editors might want to adjust some of the information, for example, data when an article was published or modified. To override automatically stored values, editors will need a dedicated field.


Info from **graphic designers**
  * You know all the different types of layouts and combinations that designers have prepared for the Article page.


## Identify data types that compose the Article page
After completing the previous exercise, you and your team should know what data they need for the Article page content type and what components editors need to create an article in a website channel. Now, it’s time to see if your data model will also fit into the presentation model.
[![Article page content type](/docsassets/guides/model-an-article-page/article-page-content-type.png)](/docsassets/guides/model-an-article-page/article-page-content-type.png)
You might wonder why the first diagram held values for SEO and OpenGraph fields in separate content components while this new graph moves both into the _Article page_ content type.
The reasoning for making all fields part of one type is simple:
**SEO** and **OpenGraph** data are unique to a specific article; storing them in a separate content type (and linking them to the page) doesn’t make sense. Breaking them down into particular content types might lead the editors to the wrong path, and they will start reusing the existing content stored in SEO fields across multiple articles.
At the same time, you don’t want to force your developers to recreate the same set of fields for any future webpage content type. Xperience has a feature called [reusable field schemas](/documentation/developers-and-admins/development/content-types/reusable-field-schemas) which allows your system architects to create a collection of fields, reuse them across different content types and combine them with dedicated fields, unique to that specific content type.
The following image shows Article page content type. You can see it consists of two dedicated fields and reused two schemas, SEO and Open Graph.
[![Article page layout](/docsassets/guides/model-an-article-page/article-page-content-type-definition-kbank.png)](/docsassets/guides/model-an-article-page/article-page-content-type-definition-kbank.png)
You can also create a simplified relationship diagram to describe connections between individual types. Seeing content types in context can help resurface any repeating patterns that might be extracted into separate content types. 
## Identify page components to create the website page
Visitors will access the article on the website page through a specific URL. It doesn’t mean the website page must only contain data from a particular article. Website editors will likely want to display relevant content alongside the article to guide the visitors along their customer journeys and point them to other relevant content or promote a product or service by linking to a campaign page.
When modeling how Xperience will store content, **we recommend separating the data** you might need to reuse in a different channel (like email, social, app, etc.) or integrate in another format (that will depend on specific requirements instigated by a 3rd party solution) **from the presentation design**.
Look at the business requirements. On the Kbank website, you can see that the presentation layer of the _Article page_ will display data from both the main article and additional data from other content types.
[![Article page layout](/docsassets/guides/model-an-article-page/article-page-layout-additional-content.png)](/docsassets/guides/model-an-article-page/article-page-layout-additional-content.png)
This guide will not discuss individual components in detail to keep this guide focused on creating the Article page content type. The following guides will cover principles and tips for designing presentation components. 
### Define page templates and their properties
Editors use Xperience page templates to quickly create pages from predefined layouts without the developer’s help. Based on the website design, each website content type can have unlimited page templates. Templates can contain properties that allow editors to change the page’s layout, color, or style.
Find out more about the [page templates](/documentation/developers-and-admins/development/builders/page-builder/page-templates-for-page-builder) in Xperience documentation. 
Modeling properties for presentation components depends on the design and the variability defined through the project content audit and design phase.
For example, your Article page template can contain the following properties:
  * Page default configuration.
  * Position for the article text and additional content.
  * The layout of the article and components are divided into different proportions.
  * Page template styles.


The following image shows Article page template properties available on the Kbank demo site.
[![Shows proof-of-concept page template properties that you can see on the Kbank demo site.](/docsassets/guides/model-an-article-page/example-page-template-properties.png)](/docsassets/guides/model-an-article-page/example-page-template-properties.png)
### Define design components
To display additional content, editors of Kbank’s website will use the following components to add their content. 
#### Articles column section
Based on the graphics designs, the article section will not have any additional styling properties. It allows editors to add only a [limited set of widgets](/documentation/developers-and-admins/development/builders/page-builder) for example:
#### Featured news widget
Editors use a simple page selector field to reference the intended page.
[![Example of properties in the Kbank’s Featured news widget](/docsassets/guides/model-an-article-page/featured-news-widget-properties.png)](/docsassets/guides/model-an-article-page/featured-news-widget-properties.png)
#### Related articles widget
The widget provides editor options to display the default related articles or hand-pick individual articles.
[![Related articles widget on Kbank demo site](/docsassets/guides/model-an-article-page/related-news-widget-properties.png)](/docsassets/guides/model-an-article-page/related-news-widget-properties.png)
#### Call to action widget
The call to action widget is the most complex widget. Editors can link individual pages on the website, external sites, or target documents stored in the Content hub.
[![Call To Action \(CTA\) widget properties on Kbank demo site](/docsassets/guides/model-an-article-page/cta-widget-properties.png)](/docsassets/guides/model-an-article-page/cta-widget-properties.png)
#### Decide on personalization rules
Editors can also personalize the content that each widget displays to different audiences.
[![Editors select the audience that content in the widget targets](/docsassets/guides/model-an-article-page/widget-content-personalization-dropdown.png)](/docsassets/guides/model-an-article-page/widget-content-personalization-dropdown.png)
## Verify your Article page diagram with stakeholders
Talk to your team about your model before your developers build the Article page content type in Xperience. Make sure everyone knows what the output will look like. In this stage, you should collect input from the leading developers, business analysts, solution architects, senior content editors, or content strategists who are part of the project.
## Create the Article page content type in Xperience
To help editors understand their workflow with the type, create your Article page content type (and any missing content types) in Xperience’s Content types application. If you have done your work correctly and your Article page model has all the details required to create the content items from the Article page content type, defining it in Xperience will be easy for anyone on your dev team.
### Add guidance for website editors
Streamline the data input process for editors and guide each content type field. With Xperience, you can implement tooltips or include textual explanations below each field, clarifying the content and format expectations for editors. Tell editors what content to enter into the fields and the format it should be in.
[![Example of different tips for editors in the Kbank’s Article page](/docsassets/guides/model-an-article-page/article-page-properties.png)](/docsassets/guides/model-an-article-page/article-page-properties.png)
Your devs might create proof-of-concept variants of Articles page templates if you have time. Testing the content creation flow in administration will help editors get hands-on experience and might uncover some bumps in the path. But that’s not required.
## Have editors create one or two website pages with the Article page content type
Editors create website pages in a dedicated website channel. Ask your editors to make at least two articles to test how adding articles will feel on their website.
The following video shows what creating the new Article page might look like.
## Polish the content type and get a write-off from business owners
Ensure your content type contains the required fields for the necessary information and that there are instructions to help editors. Check for any inconsistencies or errors in the data types and field order (content type properties). When you are done, hand it over and get your stakeholders’ agreement.
If you’ve followed the example in this guide, your editors can add a new article to your website. You can also give your developers precise instructions on tailoring the Article page content type to support editors in reaching their goals.
![]()
[]()[]()
