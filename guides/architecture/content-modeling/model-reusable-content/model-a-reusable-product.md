# Model a reusable Product
  * How-to| [ Copy page link ](guides/architecture/content-modeling/model-reusable-content/model-a-reusable-product#) | [Get HelpService ID](guides/architecture/content-modeling/model-reusable-content/model-a-reusable-product#)
Core MVC 5


[✖](guides/architecture/content-modeling/model-reusable-content/model-a-reusable-product# "Close page link panel") [Copy to clipboard](guides/architecture/content-modeling/model-reusable-content/model-a-reusable-product#)
When managing content on a website, we rely on specific content types, which serve as templates for creating various content items. This article will explore how to design content types specifically for financial products.
This article will guide you through designing content types for financial products in a way that promotes reusability and adaptability.
You’ll learn how to:
  * Create content types that your team can use across various channels.
  * Make your content modeling decisions based on standards.
  * Break down a complex financial product into a reusable content type.
  * Strategically use taxonomies to help editors categorize their content.


By the end of this article, you’ll have the tools to create well-thought content type that will allow your editor team efficiently create and manager their content.
## Build for multichannel Xperience by Kentico
Xperience by Kentico provides a [multichannel experience](guides/digital-marketing/work-with-channels/digital-marketing-channels), which means you can reuse the same content stored in Xperience on websites, emails, applications, or other marketing channels. When your team needs to add a new channel to promote your services, you don’t need to start the content modeling process from scratch just to display your content in this new channel.
[![Multichannel Xperience by Kentico](docsassets/guides/model-a-reusable-product/example-multichannel-xperience.png)](https://docs.kentico.com/docsassets/guides/model-a-reusable-product/example-multichannel-xperience.png)
## Define content types from reusable pieces
We recommend that you design your content model using semantic content types. Semantic content types consistently carry their meaning regardless of the context in which they appear.
The **Product** is a typical representation of a semantic content type. It represents an important content entity that stays the same whether you inform about it on your main website or campaign microsite, send a promotion via email, display its details in a mobile application or export the product data for a third-party product comparison website. The core data remains the same; what changes is how you talk about the product in different contexts.
[![Context-specific messages that talk about the same product](docsassets/guides/model-a-reusable-product/cm-reusable-product-content-different-messages.png)](https://docs.kentico.com/docsassets/guides/model-a-reusable-product/cm-reusable-product-content-different-messages.png)
Building a _Product_ content type that is channel-independent is just one piece of the puzzle. The content type structure itself is another piece.
Xperience allows for content type composability, which means that you can compose even semantic content types from smaller, independent pieces of content. These smaller content pieces themselves can represent different content types. They become building blocks that quickly adapt to changing requirements.
For example, several products or services will likely share the same benefit. It doesn’t make sense to copy-paste the same benefit to every product that provides it. It’s better to create one instance of the benefit and refer to it from every relevant product. When editors need to change something in the benefit, for example, its icon, editors will update the benefit in one place – and Xperience itself will promote the change to every relevant product.
[![The same content item displayed across different channels](docsassets/guides/model-a-reusable-product/cm-product-content-in-different-channels.png)](https://docs.kentico.com/docsassets/guides/model-a-reusable-product/cm-product-content-in-different-channels.png)
At the same time, editors will not need to recreate the same information across different content items. By adopting the mindset of “ _build once, reuse everywhere,_ ” you design a model where editors can quickly adjust existing content, reuse it in a new channel, and save time and resources.
[![Simple graph of Product content type](docsassets/guides/model-a-reusable-product/cm-product-content-type-sketch.png)](https://docs.kentico.com/docsassets/guides/model-a-reusable-product/cm-product-content-type-sketch.png)
## Follow data modeling standards
When it comes to content modeling, it’s always better if you make your decisions based on standards or conventions. Using standards and conventions makes content easier to manage and scale. Especially with e-commerce-specific content types, organizing the data into clear categories and using standard attributes that describe and explain the product help marketers create compelling messages that attract customers. Structured content, like this reusable product type, works well with tools like search engines and marketplaces. Following standards will save you time, help you avoid mistakes, and make systems more accessible for everyone to understand.
For example, you can find inspiration for content types at [Schema.org](https://schema.org).
## Start with typical content-type fields
Let’s use the Kbank demo site as an example. Kbank provides financial products or services.[schema.org)](https://schema.org/FinancialProduct) contains detailed information on the recommended fields for financial products.
Each product content type includes essential fields, such as:
  * Title
  * Slogan or short description
  * Associated product image
  * Product description.


At this point, you will likely realize that every semantic content type will contain this field combination. You have two options:
  * Create these fields in every content type.
  * Leverage [reusable field schemas](documentation/developers-and-admins/development/content-types/reusable-field-schemas), define a collection of core fields, and reuse this core collection in every relevant content type.


### Use reusable field schemas for core content across content types
[Reusable field schemas](documentation/developers-and-admins/development/content-types/reusable-field-schemas) allow you to avoid repetitive work.
Instead of defining similar fields repeatedly for each content type, you create a schema of fields. Each schema defines a set of fields dedicated to a specific use case or scenario. You then add this schema to relevant content types as needed. Xperience reflects your changes to a schema, such as adding or removing, in all linked content types.
[![Editing product content item in Xperience](docsassets/guides/model-a-reusable-product/editing-product-content-item-in-xperience.png)](https://docs.kentico.com/docsassets/guides/model-a-reusable-product/editing-product-content-item-in-xperience.png)
Creating the **Core Content** collection in the **Product** content type simplifies work for the Kbank team. For editors, it ensures consistency by using the same fields in order across all product and service content types. For developers, it standardizes properties, making it easier to build the presentation layer and saving time in the process. Additionally, the reusable field schema allows developers to create components more efficiently by reducing repetitive tasks.
## Make the image reusable
Good-looking images give your marketing communication proper. Placed strategically, pictures or other visuals can spice up ongoing conversations in your customer’s head.
Your marketers rarely use images once; in many cases, they’ll want to reuse the visuals and frame the conversation into one visual context.
**Call to action in content**
[![Kbank call to action element with reusable image content type](docsassets/guides/model-a-reusable-product/kbank-call-to-action-button-with-image.png)](https://docs.kentico.com/docsassets/guides/model-a-reusable-product/kbank-call-to-action-button-with-image.png)
**Hero banner of a landing page**
[![Kbank banner with reusable image content type](docsassets/guides/model-a-reusable-product/kbank-banner-widget-with-the-same-image.png)](https://docs.kentico.com/docsassets/guides/model-a-reusable-product/kbank-banner-widget-with-the-same-image.png)
**Promotional email**
[![Promotional email with reusable image](docsassets/guides/model-a-reusable-product/kbank-email-card-with-the-same-image.png)](https://docs.kentico.com/docsassets/guides/model-a-reusable-product/kbank-email-card-with-the-same-image.png)
Use a dedicated **Asset** content type to store images and visuals. Your team can upload the file once and reuse it anywhere they need. And if they need to update the image, they’ll just do it once - and their update propagates into all channels and places that reference it.
[![Product content type with reusable Asset content type](docsassets/guides/model-a-reusable-product/graph-product-fields-with-reusable-asset-content-type.png)](https://docs.kentico.com/docsassets/guides/model-a-reusable-product/graph-product-fields-with-reusable-asset-content-type.png)
See other benefits you can get when you [store your assets in a reusable content type](guides/architecture/content-modeling/model-reusable-content/model-a-reusable-article).
## Identify specific fields for financial services
Given the specificity of financial services, your product content type will include additional fields to store specific financial information, such as:
  * Annual % Rate
  * Interest Rate
  * Taxonomy (Currencies)
  * Taxonomy (Regional availability).


These fields are consistent across all financial services on the website and provide a good case for using reusable field schemas.
[![Product content type graph showing financial product fields](docsassets/guides/model-a-reusable-product/graph-product-fields-with-financial-fields.png)](https://docs.kentico.com/docsassets/guides/model-a-reusable-product/graph-product-fields-with-financial-fields.png)
Since every product content type will share these financial fields, it makes sense to define their values through another collection of reusable field schemas.
[![Financial fields built with reusable field schema in Kbank’s product content types](docsassets/guides/model-a-reusable-product/reusable-field-schemas-financial-fields.png)](https://docs.kentico.com/docsassets/guides/model-a-reusable-product/reusable-field-schemas-financial-fields.png)
Find out more about [reusable field schemas](documentation/developers-and-admins/development/content-types/reusable-field-schemas) in the documentation.
### Add content-type specific financial fields
In real-world scenarios, you’ll define several different reusable Product content types. You’ve already seen that you can define reusable field schemas that editors will use to store the same kind of data for each product. At the same time, your solution will contain fields that will be specific per product type. You can use standard content type fields to store the product-specific data.
For example, the Kbank demo project contains four products - _Product - Loan_ , _Product - Account_ , _Product - Insurance_ , and _Product - Card_. They each contain dedicated fields that relate only to the specific type, such as the following:
**Product - Card** :
  * Maximum borrow amount
  * Card floor limit
  * Minimum repayment amount (monthly)


**Product - Loan** :
  * Maximum loan amount
  * Loan duration
  * Is the loan renegotiable?
  * Loan grace period
  * Loan required collateral


The following image overviews dedicated product-related fields of different reusable products in the Kbank demo site.
[![Specific fields in different reusable product content types](docsassets/guides/model-a-reusable-product/cm-product-content-type-specific-fields.png)](https://docs.kentico.com/docsassets/guides/model-a-reusable-product/cm-product-content-type-specific-fields.png)
## Make benefits a separate content type
In any product promotion, it always helps when you can pinpoint the benefits the product brings to the customer.
[![Product benefits displayed in hero banner widget](docsassets/guides/model-a-reusable-product/kbank-product-benefits-in-banner-widget.png)](https://docs.kentico.com/docsassets/guides/model-a-reusable-product/kbank-product-benefits-in-banner-widget.png)
From a content modeling perspective, the concept of benefits extends beyond the product. In general, your team might want to promote the company by listing what benefits customers get when they start working with you.
[![Company benefits displayed in a banner widget](docsassets/guides/model-a-reusable-product/kbank-company-benefits-in-banner-widget.png)](https://docs.kentico.com/docsassets/guides/model-a-reusable-product/kbank-company-benefits-in-banner-widget.png)
You can create a Benefit content type to help you communicate product or company benefits.
[![Product content type with linked Benefits content type](docsassets/guides/model-a-reusable-product/graph-benefits-content-type-as-part-of-product.png)](https://docs.kentico.com/docsassets/guides/model-a-reusable-product/graph-benefits-content-type-as-part-of-product.png)
Making the benefits a set of data, among other product data, opens new horizons. You will store specific benefits as an individual item, and then you can reuse them in different products. You can highlight particular benefits when promoting the product or your company across various channels, including the website, headless apps, and emails.
[![Product benefits displayed in a mobile app](docsassets/guides/model-a-reusable-product/kbank-benefits-in-app.png)](https://docs.kentico.com/docsassets/guides/model-a-reusable-product/kbank-benefits-in-app.png)
## Make product features reusable
Product features are little gems of information that, like keys, can unlock a product’s essential aspects in customers’ eyes. If you introduce product features in a way that customers easily understand, they become the secret sauce that makes the product tick.
Storing features into separate content types has two main benefits. First, you’re **making your content more modular** , thus reusable whenever needed.
[![Product with linked product features in a separate content type](docsassets/guides/model-a-reusable-product/product-with-linked-features.png)](https://docs.kentico.com/docsassets/guides/model-a-reusable-product/product-with-linked-features.png)
Secondly, since the feature content type stores data without any presentation information, you can easily show it in any way – for example, as tabular data
[![Product features displayed in a table](docsassets/guides/model-a-reusable-product/product-features-in-tabular-data.png)](https://docs.kentico.com/docsassets/guides/model-a-reusable-product/product-features-in-tabular-data.png)
Well-modeled features will help you describe the product to your audience, whether by comparing features across a range of products, assessing their availability, or even weaving them into price calculators.
[![Product features in product comparator widget](docsassets/guides/model-a-reusable-product/features-in-product-comparator.png)](https://docs.kentico.com/docsassets/guides/model-a-reusable-product/features-in-product-comparator.png)
## Taxonomy
In Xperience, taxonomies allow editors to organize and categorize content efficiently. Using tags, which can be structured hierarchically, editors can apply multiple taxonomy groups to a single content type, creating a flexible way to manage information.
For example, the Kbank demo site contains different taxonomy groups. The goal of Products in Kbank is to show how you can use the taxonomy feature and support various scenarios.
  * **Core** taxonomy: Provides semantic tags to further refine different content types into various topics and categories.
  * **Currencies** taxonomy: Identifies the currency in which a financial service is available and helps global users understand their options.
  * **Regional Availability** taxonomy: Specifies the countries where products or services are offered and supports the website’s localization.
  * **Financial Information** taxonomy: Defines attributes for financial product features, such as eligibility criteria or availability.


These examples demonstrate how you can approach modeling product-specific taxonomies in Xperience to help editors structure content while maintaining consistency and scalability.
For example, you can create dedicated content types to model relationships between different product (and other content type) categories and then group them accordingly.
[![Taxonomy created with a dedicated content type item](docsassets/guides/model-a-reusable-product/taxonomy-as-content-type-category-item.png)](https://docs.kentico.com/docsassets/guides/model-a-reusable-product/taxonomy-as-content-type-category-item.png)
[![Product with linked content item](docsassets/guides/model-a-reusable-product/product-with-linked-content-item-as-taxonomy.png)](https://docs.kentico.com/docsassets/guides/model-a-reusable-product/product-with-linked-content-item-as-taxonomy.png)
You’ve seen how you can break a complex financial product into a reusable content type, a composite of other content types.
[![Product content type in simple graph](docsassets/guides/model-a-reusable-product/graph-product-content-type-complete.png)](https://docs.kentico.com/docsassets/guides/model-a-reusable-product/graph-product-content-type-complete.png)
Interested in a different approach? Watch a short video about modeling a slightly different reusable Product content type.
## To sum up
Making your content model modular and composable makes your content future-friendly. You’ve learned about how to design reusable and adaptable content types for financial products. You’ve seen different options for breaking product content into manageable components, using taxonomies strategically, and applying standards to ensure consistency and scalability.
Now, you’re ready to create compelling content by combining existing reusable content types! Happy content modeling.