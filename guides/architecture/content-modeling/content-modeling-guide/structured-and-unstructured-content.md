# Structured and unstructured content
  * Concept| [ Copy page link ](guides/architecture/content-modeling/content-modeling-guide/structured-and-unstructured-content#) | [Get HelpService ID](guides/architecture/content-modeling/content-modeling-guide/structured-and-unstructured-content#) | This page is part of a module: [ Content modeling guide ](modules/content-modeling-guide)
Core MVC 5


[✖](guides/architecture/content-modeling/content-modeling-guide/structured-and-unstructured-content# "Close page link panel") [Copy to clipboard](guides/architecture/content-modeling/content-modeling-guide/structured-and-unstructured-content#)
## About this guide
This page is a part of the **Content modeling guide** which you should follow sequentially from [beginning to end](guides/architecture/content-modeling/content-modeling-guide). You can also follow this series in [Content modeling guide](modules/content-modeling-guide), which helps you keep track of your progress as you move through this sequential material.
## Unstructured and structured data in content types
Data stored in any content management system can be divided into two main types: structured and unstructured. Let’s examine the differences between these data types and their implications for content management. Understanding the main differences is crucial for solution architects and developers as they are about to design a content model for content marketers to use in their digital strategies.
Generally, your marketing data stored in the content type can be _unstructured_ and _structured_.
  * **Unstructured data** format stores the data and its design, i.e., how the data is presented on the live site. For example, apart from the words in the product title, the title field also stored HTML tags, such as: _< h1>Advanced account</h1>_. In Xperience, we refer to data stored this way as **unstructured content**.
[![Examples of unstructured content](docsassets/guides/structured-and-unstructured-content/cm-guide-h1-html.png)](https://docs.kentico.com/docsassets/guides/structured-and-unstructured-content/cm-guide-h1-html.png)
  * **Structured data** is a way of storing data that contains only the data, such as a _product name_ in the _Product title_ field, for example, _Advanced account_. In Xperience, we refer to content that consists primarily of structured data with no or limited information about how this content is presented as **structured content**. _Structured content_ can be stored in the [Content hub](documentation/business-users/content-hub), or any [Xperience channel](guides/digital-marketing/work-with-channels/digital-marketing-channels) application, such as [website](documentation/developers-and-admins/configuration/website-channel-management) or [email](documentation/developers-and-admins/digital-marketing-setup/email-channel-management) channel. In Xperience, we call _structured content_ stored in the [Content Hub](documentation/business-users/content-hub) as **Reusable content** because it’s marketing-channel agnostic, and it is easy to reuse, maintain, and upgrade.
[![Examples of structured content](docsassets/guides/structured-and-unstructured-content/cm-guide-product+title-content-tab.png)](https://docs.kentico.com/docsassets/guides/structured-and-unstructured-content/cm-guide-product+title-content-tab.png)


### Why the decision between structured vs. unstructured content matters
Xperience gives you complete control over what your content types look like, as well as what data your editors store and in what format. During content modeling, your team defines what data you need to store, in this case, into the _Article_ content type and in what format, and developers implement Xperience to support your requirements.
The decision on which type of content is stored in the content type dictates how editors create and maintain content and how developers query and display the content in different marketing channels.
Although it’s relatively cheap to create data and store it together with what it looks like when displayed to the customer, **unstructured content usually becomes very expensive to maintain** and, in some cases, **almost impossible to reuse** in different marketing channels and **extremely difficult to upgrade**.
It’s common for content types to reuse data stored within other content types. For example, fields for an _Article_ content type used for creating blog posts include a _Title_ , _Lead_ , _Body_ , _Teaser image_ , _Author_ , _Publication date_ , and _Category_.
The _Author_ content type includes fields such as _Name_ , _Bio_ , _Profile picture_ , _Contact info_ , or _Social media links_. The _Teaser image_ field may refer to the _Media file_ content type with fields like _Title_ , _Description_ , _Internal description_ , or _category_. The _Category_ fields help you describe how this piece of content fits within your whole marketing content.
[![Content composition in content types](docsassets/guides/structured-and-unstructured-content/article_related_schemas.png)](https://docs.kentico.com/docsassets/guides/structured-and-unstructured-content/article_related_schemas.png)
Before you define which content types you’ll use, take a broader view on your content.
## Consider your content from different perspectives
Depending on your perspective, there are various ways to approach your content. Each approach has advantages and challenges, and every perspective has subtle differences that affect how you structure and manage your content. Use the following viewpoints and terminology to ease up thinking about the content model and discussing how to approach the content in Xperience with your team.
**Structured vs. unstructured content**
  * _Unstructured_ content contains data and instructions on how the system should display it. In Xperience, for example, editors author unstructured content in email channel applications or the Page Builder in website channels.
  * _Structured_ content keeps the content data separated from the design information in the database. In Xperience, this content is authored in the [Content hub](documentation/business-users/content-hub) and on the [Content](documentation/business-users/website-content#view-modes) view mode in website channel applications.


**Content from the presentation perspective**
  * _Navigable_ content items allow visitors to view the content item as a whole, such as a page under a URL on a website or an app screen.
  * _Reusable_ content usually serves as a building block for navigable items; visitors will never see the _reusable content_ alone.


**Marketing potential of the content**
  * _Reusable_ content items are presentation-agnostic and usually highly structured. Marketers can reuse the same content item across multiple channels, such as various places within one website, another microsite, and email. Making the content independent from a single channel makes it easier to maintain, modify, and reuse.
  * _Single-channel_ content items are usually strongly aligned with their presentation on a specific channel. Editors create this content ad hoc as they need. As a result, they often tie the content to its design. For example, by using rich text WYSIWYG (what-you-see-is-what-you-get) editors.


## Making the choice: Unstructured vs structured content
As the project’s content model designer, you have complete control over the appearance, data, and your content types’ structure. You decide in what format content editors will define their content types.
Let’s take a look at different aspects of structured and unstructured content.
### Benefits of unstructured content
Unstructured content has been the most common format for building websites, dating back to the early days of the Internet. Marketers like it because it offers a fast and straightforward editing experience. Many popular tools end up storing content in the unstructured format, including various page builders, such as [WordPress blocks](https://wordpress.org/gutenberg/) or other similar WYSIWYG webpage or website builder platforms.
### Rich text authoring and unstructured content
The rich text authoring is a part of the WYSIWYG editing experience that editors use to style the text. They customize its looks if they need to divert from brand-specific designs, insert media into the content, or link to other content. Rich text editing components often allow editors to edit the HTML source and give editors complete control over the design of the content.
Xperience by Kentico comes with a built-in [Rich text](documentation/business-users/rich-text-editor) widget editors can use to add content to a website.
[![Rich text widget in Xperience by Kentico](docsassets/guides/structured-and-unstructured-content/content-modeling-rich-text-widget.png)](https://docs.kentico.com/docsassets/guides/structured-and-unstructured-content/content-modeling-rich-text-widget.png)
### What are the disadvantages of unstructured content?
While editors can quickly create unstructured content, it is often hard to maintain or scale. From our experience, unstructured content excels in less complex scenarios. For example, it works well for a small team that manages content for a small single channel or produces one-off, short-lasting content for assets like campaign landing pages.
As your content, team, and workflows grow and become more complex, your creation tools need to adapt, particularly for multi-channel scenarios.
To support different channels, formats, and structures for which marketers create their content, WYSIWYG’s capabilities must be very advanced to safeguard the content marketers save into their applications. This includes creating valid HTML markup within the page design and ensuring the output HTML markup is fully accessible. Rich text components were primarily built to author website content. When you create content using WYSIWYG interface that allow free-form content creation, the editor only rarely produces content that anyone can reuse across different channels.
There are other points to consider, and you can learn more about them in a great post on the [Kentico Community Portal](https://community.kentico.com/blog/how-important-are-rich-text-authoring-experiences-in-2024).
The solution is to store content separately from its layout. In the case of _Rich text_ format, you can store the data in the **structured format** , following HTML standards, such as creating a bullet point list without additional styling, and then use other WYSIWYG tools to layout content. In Xperience’s website channels, the WYSIWYG tool is the Page Builder.
### Benefits of structured content
Modern marketing teams do not need to recreate or copy-paste the content on their websites. Underlying technologies evolved enough to support complex models that allow for **content reusability**. Developers can build applications that **display** or **resurface** content automatically based on its context or are driven by current marketing goals and campaigns. Content models based on structured data tend to be more flexible as they allow editors to leverage the content further. They can **reassemble** data into different content types, **present** it via a different design, or **repurpose** it in other marketing channels.
Structured content is future-friendly. Compared to unstructured content, structured content is more straightforward to adapt when the content strategy changes or different marketing teams need to tailor the content to specific usages.
Let’s have a look at the _product title_ example. The [Kbank demo site](guides/architecture/content-modeling/content-modeling-guide/what-is-a-content-type#best-practices-in-kbank-demo-site) uses several content types to store Kbank’s products, such as the _Product - Loan_ or _Product - Account_. The following diagram shows fields where editors input data or link other related content items based on different content types.
[![Content model break-down](docsassets/guides/structured-and-unstructured-content/cm-guide-product-content-model-diagram-product-title-higlighted.png)](https://docs.kentico.com/docsassets/guides/structured-and-unstructured-content/cm-guide-product-content-model-diagram-product-title-higlighted.png)
Let’s examine the different forms and formats this product title can take in various situations.
  * On a _product page_ , this title receives an `<h1>` HTML tag as it serves as the page’s title.  
[![Page title field transformations](docsassets/guides/structured-and-unstructured-content/cm-guide-h1-product+title.png)](https://docs.kentico.com/docsassets/guides/structured-and-unstructured-content/cm-guide-h1-product+title.png)
  * On a _product listing page_ , this title receives an `<h2>` HTML tag to conform with the [HTML DOM structure](https://www.w3schools.com/whatis/whatis_htmldom.asp).  
[![Page title field transformations](docsassets/guides/structured-and-unstructured-content/cm-guide-h2-title-hierarchy-product-listing-widget.png)](https://docs.kentico.com/docsassets/guides/structured-and-unstructured-content/cm-guide-h2-title-hierarchy-product-listing-widget.png)
  * On a _product comparator element_ , this title receives an `<h3>` HTML tag to conform with the [HTML DOM structure](https://www.w3schools.com/whatis/whatis_htmldom.asp).  
[![Page title field transformations](docsassets/guides/structured-and-unstructured-content/xbyk-premium-account-h2-h3.png)](https://docs.kentico.com/docsassets/guides/structured-and-unstructured-content/xbyk-premium-account-h2-h3.png)
  * On a _product campaign page_ , this title receives an `<h3>` HTML tag to conform with the [HTML DOM structure](https://www.w3schools.com/whatis/whatis_htmldom.asp).  
[![Page title field transformations](docsassets/guides/structured-and-unstructured-content/cm-guide-h3-title-product-overview-widget.png)](https://docs.kentico.com/docsassets/guides/structured-and-unstructured-content/cm-guide-h3-title-product-overview-widget.png)
  * On a website’s _Home_ page, this title becomes part of a personalized Hero banner image that promotes this product to a specific audience.  
[![Page title field transformations](docsassets/guides/structured-and-unstructured-content/cm-guide-h1-title-personalized-widget.png)](https://docs.kentico.com/docsassets/guides/structured-and-unstructured-content/cm-guide-h1-title-personalized-widget.png)
  * In a _mobile application_ , this title becomes part of the product details screen.  
[![Page title field transformations](docsassets/guides/structured-and-unstructured-content/cm-guide-title-mobile-app.png)](https://docs.kentico.com/docsassets/guides/structured-and-unstructured-content/cm-guide-title-mobile-app.png)


### Structured content helps with maintenance
Once you have stored your data in a structured format, updating the content from one place is also straightforward.
Let’s continue with our _product title_ example. Say there’s a new brand typography guideline requiring all titles to use the title case _Advanced Account_. With the content stored in a structured way, your editor needs to update just one place where they store the title’s default, root value – and all the other places that refer to the this original source _Product_ content type inherit the change.
Making content **reusable** is one of the primary goals of content modeling. Your content model should give your content a **structure**. Your content types need to represent the **meaning** of your content and allow marketers to provide this content with a **purpose**.
When you model your content correctly, your editors can reuse the same piece of data in multiple places using available Xperience features rather than duplicate it wherever needed. A well-defined content model also directly impacts [content governance](https://www.dotcms.com/blog/post/what-is-content-governance) (making sure what you produce is on-brand, coordinated, and of good quality) and associated review processes.
### Structured content simplifies development
Storing data into predefined structures doesn’t only make marketers more efficient. When content is clearly organized into structures, developers can easily understand the type of data, its format, and its purpose. This reduces guesswork and allows developers to build robust functionality instead of managing inconsistencies, ultimately speeding up the development process.
Structured content types create predictable, strongly typed content structures that developers can use to build reusable components and APIs.
Aiming for data clarity makes your content types scalable and reusable. For example, storing your data without saving how it’s going to be present alongside the default data makes it easier to add new features, support additional channels, or expand the system without disrupting existing workflows.
Structured content types create consistency that makes it easier to connect Xperience by Kentico with external tools, such as CRMs or analytics platforms, ensuring smooth data flow across systems. When you need to update or change your integrated system, the structured approach minimizes the risk of breaking existing functionality and makes the system easier to maintain.
## Structured vs unstructured content summary
Let’s break down the main advantages and challenges of structured versus unstructured content:
|  Structured content |  Unstructured content  
---|---|---  
Benefits | 
  * Easy to maintain, reuse, and upgrade
  * Scalable and reusable
  * Simplifies development and integration with other systems

| 
  * Fast and straightforward editing
  * Ideal for less complex, single-channel scenarios

  
Challenges | 
  * Requires initial setup
  * Editors need to understand the data structure

| 
  * Difficult to maintain and scale
  * Expensive to upgrade
  * Hard to reuse across multiple channels

  
## What’s next?
In short, content types serve as blueprints for structuring and organizing content. You have learned that making the content structured enables both humans and machines to understand and reuse the data and about the importance of separating the content data from design that will help you and your team maintain your content, reuse, and adapt it across multiple channels.