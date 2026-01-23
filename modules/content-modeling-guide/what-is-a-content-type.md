---
source: https://docs.kentico.com/modules/content-modeling-guide/what-is-a-content-type
scrape_date: 2026-01-22
---

Module: Content modeling guide
2 of 28 Pages
# What is a content type
## Best practices in Kbank demo site
Starting with this page, we frequently refer to the [Kbank demo site](https://www.kentico.com/services/training/training-website-requests) to explain key concepts and demonstrate the best practices for implementing Xperience effectively. We built Kbank to show practical examples of different content modeling approaches and business scenarios that we’ve seen applied in Kentico applications. To explore the website yourself, you can start the hosted instance of Xperience by Kentico’s [Business Tutorial Kbank demo site](https://www.kentico.com/services/training/training-website-requests).
## What are content types?
We’ve mentioned the term _content type_ several times in the [previous page](/guides/architecture/content-modeling/content-modeling-guide). What is a content type, and why do we talk about them?
Content types (and their relationships) are crucial elements of any content model. They are _blueprints_ for individual items. When an editor creates an item using this blueprint, the content type determines what data and in what format is stored.
[Content type](https://enterprise-knowledge.com/what-is-a-content-type/) is not an Xperience-specific term. Rather, it’s a general term used in information architecture to define a standardized data structure.
From a business perspective, content types need to represent **meaningful structures that both humans and machines understand**. The following images show an article with additional related content and how this data is translated into a _content type_.
The first image shows how different pieces of an article are stored in a meaningful structure.
[![Breaking down the article structure into a content type](/docsassets/guides/what-is-a-content-type/content_type_basics_1.png)](/docsassets/guides/what-is-a-content-type/content_type_basics_1.png)
The second image shows the power of a content type. The _Article_ **content type** serves as a **blueprint** for all items built on it. Editors use the content type to create individual article items and input data into a predefined structure.
[![Articles from Kbank demo site created with the same Article content type](/docsassets/guides/what-is-a-content-type/article_items_from_a_content_type.png)](/docsassets/guides/what-is-a-content-type/article_items_from_a_content_type.png)
Content type’s _structure_ ensures that editors _input all the required data_ relevant to the article into objects of the same structure. Depending on their marketing goals, editors can then use [Page Builder](/documentation/developers-and-admins/development/builders/page-builder) to adjust what each article looks like or what other data the page contains when they display the articles on the website. You can find more about [modeling presentation components](/guides/architecture/content-modeling/model-website-presentation-components) in a dedicated series of guides.
## Summary
You’ve learned about the role of content types in your digital marketing content. You know that content types serve as blueprints that define the structure and format of data. When editors work with these formally defined objects, they ensure their content is consistent and allows for customization through tools like the [Page Builder](/documentation/developers-and-admins/development/builders/page-builder). You also learned about the Kbank demo site we use to explain the best practices and showcase different approaches to modeling.
[ Previous page ](/modules/content-modeling-guide)
2 of 28
[ Mark complete and continue ](/modules/content-modeling-guide/structured-and-unstructured-content)
![]()
[]()[]()
