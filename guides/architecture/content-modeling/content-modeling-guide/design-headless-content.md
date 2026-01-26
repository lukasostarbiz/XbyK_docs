---
source: https://docs.kentico.com/guides/architecture/content-modeling/content-modeling-guide/design-headless-content
scrape_date: 2026-01-26
---

  * [Home](/guides)
  * [Architecture](/guides/architecture)
  * [Content modeling](/guides/architecture/content-modeling)
  * [Content modeling guide](/guides/architecture/content-modeling/content-modeling-guide)
  * Design headless content 


# Design headless content
**Advanced license required**   
  
Features described on this page require the Xperience by Kentico **Advanced** license tier. 
## About this guide
This page is a part of the **Content modeling guide** which you should follow sequentially from [beginning to end](/guides/architecture/content-modeling/content-modeling-guide). You can also follow this series in [Content modeling guide](/modules/content-modeling-guide), which helps you keep track of your progress as you move through this sequential material.
## Model headless content for multichannel delivery
Headless content items store content intended for distribution to channels outside of the Xperience application through a headless API ([GraphQL](https://graphql.org/)). This way, you can consolidate your various channels into the Xperience ecosystem, like mobile apps, web apps, kiosks, etc. into the Xperience ecosystem.
When modeling headless content, one headless item usually represents one unit of the presentation layer (page, screen, navigation, etc.).
[Headless channels](/documentation/developers-and-admins/configuration/headless-channel-management) serve headless content item data in JSON data format primarily to headless applications, services, or websites. Headless content can be retrieved by most smart devices (e.g., mobiles, information kiosks, TV screens, programmable billboards, augmented reality, etc.). Their use case is almost endless.
Xperience is a powerful hybrid-headless content management system focusing primarily on website content. Some of the key differences between Xperience and pure headless CMSs are:
  * Headless CMS delivers headless content to the website and other presentation layers and makes the presentation layers exchangeable.
  * Xperience is not a headless CMS but a hybrid-headless CMS. Headless is just a feature, and Xperience supports read-only headless content scenarios.
  * If the customer decides to use the headless feature to deliver website content, they will miss some other features that come with Xperience out of the box, namely routing, handling former URLs, building online forms for collecting customer data, and content personalization that allows editors to tailor content to customer needs.


## Headless terminology
Headless content has no universally accepted terminology that describes the various steps a user moves through as they navigate the application on their device. The project stakeholders should decide on the naming patterns they’ll follow during the product planning stages.
Developers need to adhere to naming conventions they set for their projects and keep their terminology consistent. For example, when they call a page in the presentation layer the _screen_ , they should not randomly alternate this name with _step_ throughout the project’s development.
The naming you decide upon will largely depend on the environment in which you want to use the headless content. For example:
Environment |  Headless terminology  
---|---  
Mobile applications |  Screen, view, page, or component  
Emails |  Email, template, configuration, or email settings  
Chatbots |  Dialogue, response  
Games |  Level, scene  
IoT Devices |  Data stream, Sensor reading, or command  
Virtual reality |  Scene, interaction, or environment  
The following example shows the terminology used for headless items in the [Kbank](/guides/architecture/content-modeling/content-modeling-guide/what-is-a-content-type#best-practices-in-kbank-demo-site) application.
[![Naming used in the Kbank headless application](/docsassets/guides/design-headless-content/kbank_app_naming.png)](/docsassets/guides/design-headless-content/kbank_app_naming.png)
We used _screen_ in the demo as it showcases a [progressive web application](https://developer.mozilla.org/en-US/docs/Web/Progressive_web_apps) in a mobile responsive view and we recommend that our clients use the same terminology if they deliver Xperience headless content into mobile apps.
## Define the customer journey
Mapping a user’s journey through your application enables you to model content based on user scenarios that should occur and plan how your content model is going to support them.
Using Kbank’s headless application as an example:
The first screen the user sees is the main home screen and navigation to other application screens. Home screen itself contains a product listing section that links to each separate _product_ and a shortened articles listing section.
[![Kbank home screen](/docsassets/guides/design-headless-content/kbank_home_screen.png)](/docsassets/guides/design-headless-content/kbank_home_screen.png)
The second screen from the navigation bar at the bottom is the product listing screen. The products listing screen links to all available products in the application.
[![Kbank products listing screen](/docsassets/guides/design-headless-content/kbank_products_listing_screen.png)](/docsassets/guides/design-headless-content/kbank_products_listing_screen.png)
Each product is then listed on an individual product screen. Editors can use dedicated fields to override the product’s default values and decide which product information displays in the mobile application.
[![Kbank product screen](/docsassets/guides/design-headless-content/kbank_product_screen.png)](/docsassets/guides/design-headless-content/kbank_product_screen.png)
The third screen is the articles listing screen. The articles listing screen lists all available articles in the application, which you can expand.
[![Kbank articles listing screen](/docsassets/guides/design-headless-content/kbank_articles_listing_screen.png)](/docsassets/guides/design-headless-content/kbank_articles_listing_screen.png)
Simplified diagram of Kbank’s headless application. The diagram does not show links to all the other articles and products.
[![Possible Kbank diagram](/docsassets/guides/design-headless-content/kbank_simplified_diagram.png)](/docsassets/guides/design-headless-content/kbank_simplified_diagram.png)
## Model headless content
### Store reusable content in Content hub
We recommend that you store all content as reusable in the [Content hub](/documentation/business-users/content-hub). This approach makes your content future-proof. If you extend your marketing reach with a headless channel, you should consider the way you want to model your content. Xperience makes delivering content across your different channels easy by using Content hub as a base.
One of the best approaches to model content is having a single “root content item” represent each content unit. This “root content item” should aggregate other content items via references as required by the project’s content model. To use such “root content items”, wrap them into headless items. If required you can also use [Field value overrides](#override-default-field-values) to modify default field values to something more suitable for headless.
[![Root content item with field overrides](/docsassets/guides/design-headless-content/root_content_item.png)](/docsassets/guides/design-headless-content/root_content_item.png)
In this case, the “root content item” is an _article_ and the _alternate title_ and _alternate image_ are field value overrides to make the content better suited for headless.
As headless content is a highly versatile media of information that can be served to numerous different markets and users, it is not possible to introduce one step-by-step guide to the content modeling process that will fit all. The way you model your content should best fit your needs. This page provides a recommendation that will work best for most scenarios, but not all of them.
### Override default field values
Editors might need to override the original content item field’s values with headless-specific values. The process is channel-agnostic, identical to how you can approach content for websites and emails.
[![Field value override logic](/docsassets/guides/design-headless-content/field_value_overrides.png)](/docsassets/guides/design-headless-content/field_value_overrides.png)
  1. Product is a content item stored in [Content hub](/documentation/business-users/content-hub) that acts as a “root content item” for products.
  2. HeadlessProduct is a wrapper for the Product _content type_ for delivery over headless channels. In case editors need to change the default values (e.g., shorter titles, product descriptions due to limited screen space on the target device or if you want to track the content performance - add a field where editors can input tracking IDs), HeadlessProduct can define the same set of fields as Product.
  3. Application logic is responsible for “overriding“ the values from Product fields with the values from _HeadlessProduct_ when the content item is served over headless.


## Populate headless channels with headless-specific content
We recommend using “root content items” to fill headless channels. This method simplifies displaying data, as one query shows one app screen. However, there are some disadvantages to this approach, one of which is almost no personalization of your content.
Marketers often want to personalize their content to best suit their consumers. Editors can create personalized content in a headless channel by making headless items for each piece of content that will be part of the app screen. The general recommendations for populating headless channels are:
  1. Headless items functioning as a wrapper for “root content items”. “Root content items” aggregate all the necessary reusable content items into one entity that represents one presentation layer.
  2. Headless items for separate content items with additional [fields](#override-default-field-values) for customizing their content.


Using Kbank as an example, their headless application uses the _App article listing screen_ content type to list all articles. The application also uses the _App article screen_ content type (marked with red arrows) to personalize the content.
[![Kbank personalized headless items](/docsassets/guides/design-headless-content/kbank_personalized_content.png)](/docsassets/guides/design-headless-content/kbank_personalized_content.png)
### Set up a preview for headless applications
Xperience allows editors to preview their content before they publish it. However, enabling preview for headless requires some [setup](/documentation/developers-and-admins/development/content-retrieval/retrieve-headless-content) beforehand.
After completing all the necessary configuration, you can use the **Preview button** to quickly get to your preview environment.
## What’s next
You’ve learned about the different options for designing headless content and how to approach creating your content model. On the [General content modeling recommendations](/guides/architecture/content-modeling/content-modeling-guide/general-content-modeling-recommendations) page, you’ll learn about overall content considerations and universal tips on website functionality that you might consider when planning your next project.
![]()
[]()[]()
