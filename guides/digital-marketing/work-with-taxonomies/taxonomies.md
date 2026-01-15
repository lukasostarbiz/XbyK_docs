# Taxonomies
  * Concept| [ Copy page link ](guides/digital-marketing/work-with-taxonomies/taxonomies#) | [Get HelpService ID](guides/digital-marketing/work-with-taxonomies/taxonomies#) | This page is part of a module: [ Work with taxonomies ](modules/work-with-taxonomies)
Core MVC 5


[✖](guides/digital-marketing/work-with-taxonomies/taxonomies# "Close page link panel") [Copy to clipboard](guides/digital-marketing/work-with-taxonomies/taxonomies#)
Taxonomy is a way to organize and categorize content. Taxonomizing content is like sorting different things into groups based on some shared characteristics so they are easier to find.
Taxonomy in Xperience by Kentico is an out-of-the-box feature. You can tag your content to structure the content items, pages, or digital assets into categories and subcategories or parent-child hierarchies based on what they are about or who they are for.
Implemented taxonomy allows you to, for example, filter goods in an e-shop, restrict some content from certain types of users or categorize a live site or content across different channels.
There are more [types of taxonomy structures](https://blog.hubspot.com/marketing/website-taxonomy); Xperience uses a hierarchical one. Let’s see what it means.
## Hierarchical taxonomies in Xperience by Kentico
Xperience uses a **hierarchical structure** , creating categories and subcategories of tags. The tags in each taxonomy are related. As you go deeper into the taxonomy tree, the tags in the structure go from the most general to the most specific.
Imagine you run a fashion shop with various types of products. The main taxonomy category can be _fashion_ , with subcategories divided into _clothes_ , _shoes_ , _accessories_ , and others. Each subcategory has its own subcategories, and the hierarchy drills down to smaller and smaller groups.
Such taxonomy could look like this:
[![Example of hierarchical taxonomy](docsassets/guides/taxonomies/taxonomy-tree.png)](https://docs.kentico.com/docsassets/guides/taxonomies/taxonomy-tree.png)
## What are taxonomy trees, tags, or parent-child relationships
Before we dive deeper into the topic, let’s define the terminology used in Xperience and see some examples.
  * **Tags** are keywords describing an attribute, characteristic, or quality of content. A piece of content can have one or more tags. A tag can be, for example, the color or material of a product, a type of transportation, or part of a body to filter medical examinations. A piece of content can have more tags used for different purposes, for example, to categorize the content or show a relationship with other content items.
  * Tags in taxonomy are organized into **parent-child relationships** ; in other words, it’s a hierarchy of category-subcategory.
  * **Taxonomies** are tags in a hierarchical structure. A taxonomy is like a tree, with categories and subcategories or a parent-child relationship between tags. The tags in one taxonomy are related. There can be more taxonomies in Xperience, each grouping different tags based on different properties or purposes. For example, _Clothes’ color_ —white, black, red; _Medical examinations_ —head, spine, internal organs; and _Types of transport_ —bus, train, boat.*
  * A **Taxonomy group** is a group of tags based on their relationship. For example, as mentioned in the Taxonomies bullet point above, one group is _Clothes’ color_ , the other is _Medical examinations_ and the last is the _Types of transport_.
  * **Taxonomy tree** is a structured taxonomy group in a hierarchical structure.


## Types of taxonomy groups
Developers and business analysts define the taxonomy groups editors can work with.
You can use taxonomy groups for different purposes. One group can, for example, define semantic relationships. In this case, editors can tag content to show that certain content has a relationship based on the tag meaning, like showing a couple of articles on the same topic or specific services. Use these relationships to allow website visitors to filter content for products they are looking for. Other groups can affect how Xperience works with the content, for example, by tagging products based on their availability in a specific region or country. The website can then display products relevant to that specific country or region.
[![Example of tags used to display content in specific countries](docsassets/guides/taxonomies/regional-availability-tags.png)](https://docs.kentico.com/docsassets/guides/taxonomies/regional-availability-tags.png)
At the moment, it is not possible to extend standard taxonomy fields. If you need to store more contextual information about your tags, e.g., images or different descriptions, use a combination of dedicated content types, e.g., Category items.
## Use taxonomy to deliver persona-specific content
Taxonomies can distinguish between various content available to different groups of visitors. Let’s see in more detail how to use taxonomy to deliver content to specific personas.
Imagine our fashion company that wants to distinguish between different membership levels and offer content based on the group they’re in. Xperience allows you to grant secure access to specific content items (PDFs, whitepapers, case studies) - you can use this feature and combine it with taxonomies. By [securing access](documentation/business-users/content-hub/content-items#secure-content-items), you make sure that the material is not visible to visitors who are not logged in to your website. Then, you create a taxonomy for different types of personas, for example, members and VIP members, and label the desired content accordingly. To link the content to the corresponding persona, create a contact group for each persona and connect the taxonomy.
This way, you can personalize the content. Developers just need to ensure more than one taxonomy group is assigned to the content type. Following our example, one taxonomy group defines which group of members has access to the tagged content, and another describes the content itself.
[![Different taxonomies in one content type](docsassets/guides/taxonomies/content-model-taxonomies.png)](https://docs.kentico.com/docsassets/guides/taxonomies/content-model-taxonomies.png)
## Multilingual taxonomies
Imagine our fashion company runs an e-shop in different countries. Taxonomies can help with selecting products to be visible in specific countries or regions by tagging them with the respective tags.
You can also use taxonomies to allow your visitors to filter the content. To make things easy for your visitors, you need to translate the tags into the local language. This is similar to creating [language variants of pages and content](guides/digital-marketing/work-with-multilingual). However, editors can easily translate taxonomies in the Taxonomies application, where you add just the translated version for selected (or all) languages and don’t have to interfere with or recreate the taxonomy functionality.
[![Tag title translated to other languages](docsassets/guides/taxonomies/tag-translation.png)](https://docs.kentico.com/docsassets/guides/taxonomies/tag-translation.png)
You can translate the tags directly in the **Taxonomies** application, where you add the translation for selected (or all) languages. However, taxonomies don’t have the same capabilities as multilingual content, so they don’t have fallback languages. If a tag is translated and used for content in a target language, it automatically shows in the target language. If you don’t translate the tag and want to use it in the target language environment, Xperience shows it in the default language.
For example, our default language is English. We created a Czech version of a product page and want to add a tag. The desired tag shows on the Czech page if the content has a Czech language translation. If the tag doesn’t have the Czech translation (regardless of whether it’s translated into other languages), it shows on the Czech page in English, as English is the default language.
## Creating and updating taxonomies
When you think about or work with taxonomy structure, you can consider these two basic approaches to hierarchical taxonomy. Let’s see their characteristics:
**Single taxonomy** |  **Multiple taxonomies**  
---|---  
  * Easier for editors to work with.
  * The tags are available everywhere.
  * Rich tags group for filtering or searching.
  * Adding new tags doesn’t affect the code.
  * Shuffling tags around can break the implementation.

| 
  * Editors have to understand how to work with different taxonomy groups.
  * The tags are limited in their usage.
  * A number of taxonomy groups allow for more complex filtering and search.
  * New taxonomy group potentially requires a code change.
  * New types of taxonomies can vary in their function.

  
Xperience by Kentico doesn’t automatically check who can change taxonomies. So, if the developers granted you permission to change or update tags in taxonomy trees, be careful. Unprepared changes can lead to a functionality break, and your websites may not work as intended.
## To sum up
This guide gave you a basic introduction to taxonomies. You’ve learned about what taxonomies are in Xperience by Kentico and how they can be helpful for your company or project.
## Next steps
The following guide will show you the [practical use of taxonomies](guides/digital-marketing/work-with-taxonomies/work-with-taxonomies-in-kbank), how to work with taxonomy trees and tags, or how to translate the tags to different languages.