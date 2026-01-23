---
source: https://docs.kentico.com/documentation/developers-and-admins/configuration/taxonomies
scrape_date: 2026-01-22
---

  * [Home](/documentation)
  * [Developers and admins](/documentation/developers-and-admins)
  * [Configuration](/documentation/developers-and-admins/configuration)
  * Taxonomies 


# Taxonomies
Taxonomies are an important tool for structuring, organizing and categorizing content. Taxonomies allow administrators to create and organize tags in a hierarchical manner. Content editors benefit from these structures by tagging pages, emails, headless and reusable items, improving navigation and connections among related information. For developers, this page explains how to leverage tags to fetch and display related content.
Within the Xperience by Kentico environment, users can work with taxonomies and tags:
  * **Taxonomies** are structured collections of tags that organize and categorize data into hierarchical relationships. Each taxonomy contains a hierarchical structure of related tags. Examples of taxonomies are: 
    * Clothing colors (white, black, blue…)
    * Car fuel types (petrol, diesel, hybrid…)
    * PC features (external GPU, disc drive, tower size…).
  * **Tags** are individual keywords assigned to individual pieces of data. Tags can be assigned to individual pieces of content for multiple purposes including but not limited to modeling relationships, categorization, and organization and classification of data.


To start using taxonomies in your project:
  1. Create and configure [taxonomies and tags](#configure-taxonomies).
  2. Enable content editors to [add tags to content](#enable-editors-to-tag-content).
  3. Retrieve [tagged content](#retrieve-taxonomies-and-tagged-content). 
     * You can enable editors to select what content they want to display. Add option to [select tags in form components](#add-option-to-select-tags-in-form-components).


## Configure taxonomies
Before you enable content editors to assign tags to content, you need to create taxonomies and tags.
  1. [Create a taxonomy](#create-a-taxonomy)
  2. [Create tags in a taxonomy](#create-tags-in-a-taxonomy)


### Create a taxonomy
To create a new taxonomy:
  1. Navigate to the **Taxonomies** application.
  2. Select **New taxonomy**.
  3. Enter details about the taxonomy. 
     * **Taxonomy name** – the name that is displayed to users in the administration interface.
     * _(Optional)_ **Identifiers** – specify the code name if you wish to use a code name different than the pre-filled value.
     * _(Optional)_ **Description** – an explanation of the taxonomy and its intended use. This field is intended as an internal guide for other content editors to understand where the given taxonomy is used, and is not intended to be displayed publicly.
  4. **Save** the taxonomy.


The taxonomy is now created and you can add tags.
**Tip** : When you edit an already created taxonomy, you can also localize the _Taxonomy name_ field.
### Create tags in a taxonomy
To create new tags:
  1. Navigate to the **Taxonomies** application.
  2. Select a taxonomy under which you want to create tags.
  3. In the taxonomy tree on the left, select a node under which you want to create the new tag.
  4. Select **New tag**.
  5. Enter details about the tag. 
     * **Title** – the name that is displayed to users and visitors.
     * _(Optional)_ **Identifiers** – specify code name if you wish to use code name different than the default.
     * _(Optional)_ **Description** – an explanation of the tag that can be displayed publicly depending on the implementation.
     * _(Optional)_ In the **Translations** section, you can localize the _Title_ and _Description_ fields of the tag for any [languages](/documentation/developers-and-admins/configuration/languages) that are configured in the system.
  6. **Save** the tag. 
     * If you with to save the tag and continue creating other tags in the same location, select **Save and create another**.


## Enable editors to tag content
You can enable content editors to add tags to items.
[![Taxonomy selector in a content type field](/docsassets/documentation/taxonomies/taxonomy_field.png)](/docsassets/documentation/taxonomies/taxonomy_field.png)
See [Add option to tag content](/documentation/developers-and-admins/development/content-types#add-option-to-tag-content) for more information.
## Retrieve taxonomies and tagged content
### Retrieve content items with specified tags
You can limit the retrieval query to only retrieve content items with the specified tags using the `WhereContainsTags` content item query parametrization method.
See [Retrieve content items](/documentation/developers-and-admins/development/content-retrieval/retrieve-content-items#filter-content-items-based-on-tags) for more information.
### Add option to select tags in form components
You can enable content editors to select tags in builder component and administration UI component properties. Content editors can select tags in form component properties and these tags can be used to [retrieve specific content with the selected tags](#retrieve-content-items-with-specified-tags).
[![Taxonomy selector in widget properties](/docsassets/documentation/taxonomies/taxonomy_widget_properties.png)](/docsassets/documentation/taxonomies/taxonomy_widget_properties.png)
Add the `TagSelectorComponent` editing component to your component’s properties. See [Tag selector](/documentation/developers-and-admins/customization/extend-the-administration-interface/ui-form-components/reference-admin-ui-form-components#tag-selector) and [Editing components](/documentation/developers-and-admins/customization/extend-the-administration-interface/ui-form-components/editing-components) for more information.
### Retrieve information about tags and taxonomies
If you want to display information about a particular taxonomy (e.g., a component on the live site that allows you to filter articles by their tags) you can use the `RetrieveTaxonomy` method of the `ITaxonomyRetriever` interface.
C#
**Retrieve taxonomy and tag details**
Copy
```
// An instance of  ITaxonomyRetriever (e.g., obtained via dependency injection)
private readonly ITaxonomyRetriever taxonomyRetriever;

// Retrieves the general TaxonomyData object for a selected taxonomy
TaxonomyData taxonomyData = await taxonomyRetriever.RetrieveTaxonomy("ArticleCategory", "en");

// Retrieves data about the taxonomy from the TaxonomyData object
var taxonomy = taxonomyData.Taxonomy;
string taxonomyDisplayName = taxonomy.Title;
string taxonomyCodeName = taxonomy.Name;

// Retrieves the collection of all tags within the taxonomy
IEnumerable<Tag> tags = taxonomyData.Tags;

// Retrieves data about a tag within the taxonomy
Tag someTag = tags.FirstOrDefault();
string tagDisplayName = someTag.Title;
string tagCodeName = someTag.Name;
```

![]()
[]()[]()
