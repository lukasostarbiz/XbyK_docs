---
source: https://docs.kentico.com/modules/content-modeling-guide/store-content-in-reusable-schemas
scrape_date: 2026-01-22
---

Module: Content modeling guide
13 of 28 Pages
# Store core content into reusable field schemas
While creating a content model or defining the data structure for a content type, you’ll often identify that some fields or sets of fields repeat across different content types. For example, all product-related content types will have the same fields to hold _title_ , _product description_ , _product image_ , and core _taxonomy_. Some product-specific fields are similar. Other content types used to promote these products might contain the same structure.
To manage these repetitions efficiently, we recommend using **reusable field schemas** that help you handle such [repeated fields](/documentation/developers-and-admins/development/content-types/reusable-field-schemas) across different content types.
The following image shows the Kbank demo site’s content types for storing products: **Account** , **Card** , and **Loan**.
[![Example of reusable field schema](/docsassets/guides/store-content/cm-reusable-schema-fields-product-content-type.png)](/docsassets/guides/store-content/cm-reusable-schema-fields-product-content-type.png)
All three product content types share two groups of schemas – the _Core content_ schema and _Financial data_ schema. While the _Financial data_ schema is relevant only to the products, the _Core content schema_ stores the same content as other content types in the Kbank demo site. For example, we show two more content types that share the same schema: **Featured content** and **Promotion**.
Note that each content type has dedicated fields relevant only to that specific content type, such as the Minimal inflow or account type in the _Product_ – _Account type_ field or the _External URL_ field in the _Featured content_. See [how to approach modeling taxonomies](/modules/content-modeling-guide/model-taxonomies) to learn more.
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
[ Previous page ](/modules/content-modeling-guide/select-content-modeling-approach)
13 of 28
[ Mark complete and continue ](/modules/content-modeling-guide/build-flexible-model-with-content-blocks)
![]()
[]()[]()
