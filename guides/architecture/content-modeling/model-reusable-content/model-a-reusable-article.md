---
source: https://docs.kentico.com/guides/architecture/content-modeling/model-reusable-content/model-a-reusable-article
scrape_date: 2026-01-22
---

  * [Home](/guides)
  * [Architecture](/guides/architecture)
  * [Content modeling](/guides/architecture/content-modeling)
  * [Model reusable content](/guides/architecture/content-modeling/model-reusable-content)
  * Model a reusable Article 


# Model a reusable Article
Company updates, blog articles, case studies, interviews, and similar content are some of the most common content types companies share regularly with their users. This guide will show you how to model a universal _article_ content type as a reusable component that your editors can display on the website or feature in an email.
## Prerequisites
  * You should have basic knowledge of [content modeling principles](/guides/architecture/content-modeling/content-modeling-guide).


## Start from your content audit
Auditing your existing content and its lifecycle or content editing workflows should be the core for any changes of updates to your content model.
Let’s play this scenario:
Your marketing team publishes weekly updates, shares customer studies, and interviews with stakeholders on different company sites. They also promote this content in their email blasts and monthly updates.
During your **content audit** , you realized that you have to **copy and paste** the **same article** several times when you want to publish it on your **company website** and **promote it** to your users **on the homepage**. Your editors must also copy the same content when sending monthly **email updates** to your newsletter subscribers.
[![Example of reusing content](/docsassets/guides/model-a-reusable-article/cm-examples-reusing-content.png)](/docsassets/guides/model-a-reusable-article/cm-examples-reusing-content.png)
You’ve also realized that all of these content artifacts share the same properties, such as:
  * Title
  * Summary
  * Teaser image
  * And the body of the text.


Besides the article, your team has identified that they will need an **Author** content type to indicate who wrote their articles and a taxonomy defining article types – **news** , **interview** , **case** **study** , and **press release**.
The **Kbank site** this guide mentions several times is an Xperience by Kentico demo application. You can find more about the application in the Business tutorial and even [request your instance Kbank hosted on our servers](https://www.kentico.com/get-started/hosted-trial).
Some examples discussed in this guide, such as the Author content type, do not appear on the Kbank demo site.
If you haven’t run a content audit, see [this SEMRush article](https://www.semrush.com/blog/content-audit-for-content-marketing-strategy/) and find out why and how to run a content audit.
## Consider your business scenarios
Looking at the different content strategies your team wants to implement, you’ve defined the following examples:
  * Feature articles on the home page.
  * Show various featured articles to users who already read the feature.
  * Display related articles.
  * Display articles of the same type.
  * Personalize the articles based on business-related criteria. (For example, point business users to business-specific content).


See the [Begin with business needs](/guides/architecture/content-modeling/content-modeling-guide/content-modeling-business-requirements) in the [Content modeling guide](/guides/architecture/content-modeling/content-modeling-guide).
These requirements determine which data you should **store in a structured format** so that it is **reusable across your website and other channels** , such as emails and additional websites.
You can also create a simplified relationship diagram to describe connections between individual types. Seeing content types in context can help resurface any repeating patterns that you can extract into separate content types.
[![Identifying relationships between different content types helps editors understand the benefits of composing Articles as reusable content.](/docsassets/guides/model-a-reusable-article/cm-simplified-relationship-diagram.png)](/docsassets/guides/model-a-reusable-article/cm-simplified-relationship-diagram.png)
## Define content types editors need to create an article
Now that you know what content types the marketing team needs (article, author, different article types, etc.) and the conceptual approach you need to take (making articles reusable), you can start looking into how to store the data.
Start thinking of your content model as a **data model**. You can organize how your data relate to one another rather than thinking about where (i.e., in what display channel, like a webpage) this data belongs. In short, you start **separating presentation-related data** and the **data that contains the information**.
Your team has identified they will need the following properties:
  * Article 
    * Title
    * Summary
  * Image thumbnail 
    * Description
    * Alt text caption
  * Article body
  * Author 
    * First name
    * Last name
    * Portrait image 
      * Description
      * Alt text caption
    * Job title
    * Company/organization
    * Bio
    * Email
    * Social media addresses
  * Article types (as taxonomy category) 
    * Interview
    * Case study
    * News
    * Press release


### Identify fields reused across different content types
Some fields like title, image, or description are often needed across different content types. You have two options: define these fields individually for each content type (e.g., ArticleTitle for articles and ProductTitle for products), or take advantage of their similarities by using an Xperience feature called [reusable field schemas](/documentation/developers-and-admins/development/content-types/reusable-field-schemas).
With reusable field schemas, developers can define a collection of fields once and apply them across multiple content types. This allows you to store essential information consistently and display data from different content types using the same content presentation components, such as widgets. By reusing fields in this way, you gain flexibility in presenting content and reduce the need for multiple presentation components simultaneously.
### Define article taxonomies
Taxonomies help your editors organize and categorize their content. The taxonomy example comes from the Kbank demo site example.
In real life, you will want to create the **case study** as a separate content type to accommodate shared fields that all case studies will need: a customer who submitted the case study, what improved after completing their project, etc. We’re staying with this example for some time. We will describe how we iterate over the content model (in this case, modeling taxonomies) when we update the demo site using features from upcoming releases.
The [list above](#ContentTypeDefinition) shows what properties your editor team expects to work with. That’s the _human part_ of the business working with the _Article_ content type.
You want to **make your content discoverable** , so you must embrace the idea that you are also building your content model for machines like search engines. Machines expect to see the data in a particular format. How can you ensure that other systems understand your Article content type correctly?
## Consult the [schema.org](http://schema.org) schema to identify missing properties or overlooked opportunities
As the next step, we recommend consulting [schema.org](http://schema.org) to see whether you have captured all the properties typical for the content types your team has identified.
[Schema.org](http://schema.org) is a reference website that provides guidelines about structured data and their standardization. Find out more about [schema.org](http://schema.org) on [Wikipedia](https://en.wikipedia.org/wiki/schema.org).
### Article
The Article content type corresponds with [schema.org](http://schema.org)’s [article](https://schema.org/Article) or [Blog Posting](https://schema.org/BlogPosting). Both types have mostly identical structured data properties.
[![Shows basic reusable article content type with its properties](/docsassets/guides/model-a-reusable-article/cm-simplified-data-model-article.png)](/docsassets/guides/model-a-reusable-article/cm-simplified-data-model-article.png)
### Author
[Schema.org](http://Schema.org) looks at [article authorship](https://schema.org/author) from two perspectives. Either you have the [Organization](https://schema.org/Organization) identified as a publisher, or one or more person(s) are creating the article. 
With the Kbank demo site, we have decided to assign all posts to the organization. For this example, let’s define the **author** -type properties using the **Person** type. You will see that you can break the _Author_ type down even further into three more suitable, reusable content types: _Company_ , _Address_ , and _Social media_.
  * **Author**
    * First name
    * Last name
    * Job title
    * **Portrait image**
      * Description
      * Alt text caption
    * Bio
    * **Company/organization**
      * Name
      * **Address**
        * Street
        * Business Park
        * City/Town
        * County
        * Postcode
        * Country
        * Longitude
        * Latitude
    * Email
    * **Social media addresses**
      * Label
      * Icon
      * Link


Aiming to support content reusability, you’ve defined several other content types and their properties.
**Aim for good enough.**
Do not fall into the perfectionist trap. Content modeling is an iterative process you’ll never get right the first time. Focus on defining the content types you know your team will work with and reuse, gathering feedback from your team, and improving. Rinse and repeat.
## Use Image content type to store the article’s thumbnail and author’s portrait
When you define the _article_ and the _author_ , you can easily spot that both types share the **image** data type. What are the implications?
Marketers and editors rarely consider an _image_ or an _asset_ as a content type that can stand on its own. They are used to adding an image through a WYSIWYG editor and calling it a day. They often realize that assets are, in fact, separate types that need their specific properties when it’s too late, for example, when they want to display a particular image only to logged-in users.
Given your team’s requirements, you **want to make the content reusable**. Why? It will be easier to attach an image to the article. But that would make the image part of the article type, making using the same image elsewhere almost impossible. Don’t write yourself into a corner with single-channel content; create the image as a separate type. [Schema.org](http://schema.org) defines the [ImageObject](https://schema.org/ImageObject).
Looking at the commonly shared properties, your asset will contain these:
  * **Asset**
    * Description
    * Alt text caption
    * The file (itself)


Your developers have complete control over the _Asset_ content type. They can set up the necessary fields according to your project’s needs and implement the desired functionality. Uploading Assets into the Content hub allows your team to treat the asset like any other content for reuse. You gain access to extra features, such as creating language versions, applying content management workflows, or restricting access to specific files, for instance, to only signed-in users.
TIP: Use [schema.org](http://schema.org) built-in tools and validate that you’ll be able to dynamically pull data to construct individual types using the [examples of schema markup](https://schema.org/ImageObject#examples) for [JSON-LD](https://en.wikipedia.org/wiki/JSON-LD) or _Structure_.
[![Simplified Asset content type](/docsassets/guides/model-a-reusable-article/cm-simplified-asset-content-type.png)](/docsassets/guides/model-a-reusable-article/cm-simplified-asset-content-type.png)
## Define Article category
[Taxonomies](https://en.wikipedia.org/wiki/Taxonomy) use defined terms (or _shared vocabularies_) to describe relationships between different entities, in this case, content types, and help organize data sets into hierarchies.
Since every business has its specifics, let’s stay with the Kbank demo site example. The fictional Kbank’s marketing team uses several taxonomies to categorize their content. The most significant taxonomy group for articles is the Core Taxonomy.
Editors use Xperience tag selector to mark articles as of a specific type. The system then allows for using the article type, e.g., in the Article listing widget where editors select which articles will become part of the list page.
Until this point, this whole exercise has proven one crucial thing:
**A good content model is CMS agnostic.**
The content model reflects how your business uses digital content. It defines the objects your team works with, the lifecycle your content follows, or the business scenarios your content fulfills. The model or content types should not reflect or arise from options that your website management system, like WordPress, provides for storing your content.
## Consult your designs to verify that your properties collect all the necessary data
After completing the previous exercise, you and your team should know what content types they need to store information for reusable articles. It’s time to see if your _data model will also fit into the presentation model_.
Look into the designs to ensure **your content types contain** the correct **properties** your designers wanted **to display**. Look also into your business cases and separate other data into separate structured data fields you have missed.
For example, since Kbank wants to display the publish date article on the website but not in the email, the _Publish date_ can be separate from the reusable article. At the same time, since Kbank’s team wants to share the same collection of related articles with one article on different websites, mobile apps, or other headless channels, you should define a property to capture the _relationship_ on a reusable rather than a channel-specific level.
[![A graphic design for the article on the Kbank demo project.](/docsassets/guides/model-a-reusable-article/cm-article-graphic-design.png)](/docsassets/guides/model-a-reusable-article/cm-article-graphic-design.png)
## Identify individual data types that compose the content types in the reusable article
### Start with data types
Until now, your developers could implement the content model in any content management system. From this point on, your content modeling steps will focus on the Xperience by Kentico and its capabilities.
Xperience by Kentico allows developers to define any type in the Content types application. It also provides a set of built-in UI components that editors can use to input data. Break down every property of your types to define the data model for reusable **Article** in Xperience by Kentico.
Xperience by Kentico is powered by an SQL database. The system stores content type data in dedicated tables with columns capturing the content type’s properties. You can learn more about the [content item database structure](/documentation/developers-and-admins/api/content-item-api/content-item-database-structure) in our documentation.
### Identify UI components and their configuration
We also recommend that you identify which UI components configuration editors will use to input data into Xperience. For example, the Article summary is of Text data type. For the Text, Xperience contains several UI components, such as Text input, area, URL, and others. Since the graphic designs don’t have any rich text-styled text, editors can use the Text area component.
Your browser does not support the video tag. 
[Schema.org](https://schema.org/) will also help you decide what data type you should use to store the value if you are uncertain which data types systems expect for the specific property.
The image shows fields of the Article from Kbank with their respective data type and UI component. For example, “Article text” is listed as type Rich text (HTML) that will be displayed with the Rich text editor.
[![A reusable Article from Kbank, with information about data type and UI component for each field.](/docsassets/guides/model-a-reusable-article/cm-article-model-properties.png)](/docsassets/guides/model-a-reusable-article/cm-article-model-properties.png)
### Add essential guidance for editors
To help editors with data input, you can guide them through each content type field. Xperience allows you to create tooltips or add a textual explanation text below each field. Tell editors what content to enter into the fields and the format it should be in.
We often hear in customers’ projects that developers will add the editor guidance in phase two or three of the project implementation.
We think any Xperience implementation and accompanying project documentation that the developers provide need to contain editor instructions from the beginning. We recommend including and specifying the guidance into project requirements to ensure your editors know what to do in a new system.
## Verify the content type diagram with the stakeholders
Before your developers start creating the types in Xperience, discuss your model with the team. Ensure everyone on the team understands what they will be working with. In this phase, we expect you’ll gather feedback from the lead developers, business analysts, solution architects, senior content editors, or content strategists involved in the project.
[![Model of reusable article](/docsassets/guides/model-a-reusable-article/cm-article-final-content-type.png)](/docsassets/guides/model-a-reusable-article/cm-article-final-content-type.png)
## Create the content type in the Xperience by Kentico application
Now, the actual hands-on (or fun) part of content modeling begins. [Create all content types](/documentation/developers-and-admins/development/content-types) necessary to construct a reusable Article in the Xperience’s content types application. If you’ve done your work correctly and your content model contains all the required information about the types, creating content types in Xperience will be a straightforward exercise that any junior on the dev team can work on.
[![An example of content types in the Content types application](/docsassets/guides/model-a-reusable-article/cm-list-of-content-types.png)](/docsassets/guides/model-a-reusable-article/cm-list-of-content-types.png)
## Have editors create one or two items in the Content hub using the Article content type
In Xperience, editors work with reusable content types from the Content hub. Data input into the Content hub is not tied to any presentation layer. It is helpful to ask editors to create article items in the Content hub. This will help you (and your editor team) ensure that the content-creating flow is natural and that editors don’t have to jump around the system between different applications just to create and publish a content item.
If you have time, your developers might create proof-of-concept components, such as widgets, that will display the data from the content type on the live site. But that’s not required.
## Finalize your Article content type and get business owners write-off
Finalize the look and feel of your type, add any guidance editors missed during their testing, and resolve potential conflicts in data types and order of fields (content types properties). Once finished, hand over the definition of the content type to your team.
[![Definition of content types in Excel, including detailed guidance to help less experienced editors](/docsassets/guides/model-a-reusable-article/cm-article-content-type-excel.png)](/docsassets/guides/model-a-reusable-article/cm-article-content-type-excel.png)
While you may try to build a highly adaptable content model that accommodates a variety of options, it’s nearly impossible to anticipate every future change. Marketing strategies shift, technology stacks evolve, and new communication channels emerge—meaning the content model will likely need updates over time.
When it’s time to adjust your content model, you have a few options. Before diving in, check out this article on [Safely evolving a content model with expand and contract](https://community.kentico.com/blog/safely-evolving-a-content-model-with-expand-and-contract) on the [Kentico Community portal](https://community.kentico.com).
Let’s look at an example from the Kbank project. When we first built the Kbank demo site, Xperience didn’t support reusable field schemas or taxonomies. So, we created specific fields like _ArticleTitle_ , _ArticleTeaserImage_ , and _ArticleCategory_ for the reusable _Article_ content type.
As Xperience introduced new functionality, we updated the model to demonstrate evolution using the reusable _Article_ content type. In this revised model, the original fields for the title and description remain, but we added a reusable field schema called Core Content. Editors now use this schema when creating new articles. This approach allows us to improve the content model over time without causing significant disruptions for editors which benefits large projects. You can also learn about how developers can approach updating content model in [guides about advanced content](/guides/development/advanced-content).
[![Revised Article model definition](/docsassets/guides/model-a-reusable-article/cm-revised-article-content-type-definition.png)](/docsassets/guides/model-a-reusable-article/cm-revised-article-content-type-definition.png)
## Have editors test creating the content type
If you’ve followed the example in this guide, your editors can create a new reusable article in the Content hub. Your developers will receive clear instructions about what they are making and how the content types need to look to help editors achieve their business goals.
Your browser does not support the video tag. 
See the short video where editors test how to create reusable articles in their application.
![]()
[]()[]()
