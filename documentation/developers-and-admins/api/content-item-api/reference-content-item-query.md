# Reference - Content item query
  * [ Copy page link ](documentation/developers-and-admins/api/content-item-api/reference-content-item-query#) | [Get HelpService ID](documentation/developers-and-admins/api/content-item-api/reference-content-item-query#)
Core MVC 5


[✖](documentation/developers-and-admins/api/content-item-api/reference-content-item-query# "Close page link panel") [Copy to clipboard](documentation/developers-and-admins/api/content-item-api/reference-content-item-query#)
This page provides information about the parameterization methods available for the [Content item API](documentation/developers-and-admins/api/content-item-api/content-item-query-api). The methods allow you to adjust queries and limit which items are retrieved or specify which columns are loaded to improve performance, for example.
## ContentItemQueryBuilder methods
### ForContentType 
Retrieves all items of the specified content type. Generates a subquery that can be further configured. See [Content query parameterization](documentation/developers-and-admins/api/content-item-api/reference-content-item-query#content-query-parameterization) and [ForContentType parameterization](documentation/developers-and-admins/api/content-item-api/reference-content-item-query#forcontenttype-parameterization).
C#
Copy
```
var builder = new ContentItemQueryBuilder();

// Retrieves all content items of the 'Sample.Type' type
builder.ForContentType("Sample.Type");
```

### ForContentTypes 
Retrieves all content items across all content types. Use the method’s [inner parameterization](documentation/developers-and-admins/api/content-item-api/reference-content-item-query#forcontenttypes-parameterization) to limit the selection to a subset of items.
Does not include content type field data and web page data by default. Use [WithContentTypeFields](documentation/developers-and-admins/api/content-item-api/reference-content-item-query#FCTs-WCTF) or [WithWebPageData](documentation/developers-and-admins/api/content-item-api/reference-content-item-query#FCTs-WWPD) to include it.
C#
Copy
```
var builder = new ContentItemQueryBuilder();

builder.ForContentTypes(parameters =>
{
    // Retrieves all items with the given reusable schema
    parameters.OfReusableSchema("PageMetadata");
});
```

### Parameters 
Specifies a set of parameters that apply to all items selected by individual subqueries. See [Content query parameterization](documentation/developers-and-admins/api/content-item-api/reference-content-item-query#content-query-parameterization).
C#
Copy
```
var builder = new ContentItemQueryBuilder();

builder.ForContentType("Sample.Type")
       .ForContentType("Sample.NewsRelease")
       // Sorts all records according to the 'ContentItemName' column
       .Parameters(globalParams => globalParams.OrderBy("ContentItemName"))
```

### InLanguage
Selects items from the specified language. Use a language code name as specified in the **Languages** application. The `InLanguage` method is applied to the result of all its preceding subqueries in a query.
C#
Copy
```
var builder = new ContentItemQueryBuilder();

builder.ForContentType("Sample.Type")
       .ForContentType("Sample.NewsRelease")
       // Selects only items from the English language
       .InLanguage("en");
```

More advanced scenario including linked items:
C#
Copy
```
var builder = new ContentItemQueryBuilder();

builder.ForContentType("project.store", subqueryParameters =>
        {
            // Retrieves all linked items up to the maximum depth of 1
            subqueryParameters.WithLinkedItems(1);
        })
        // Selects only items from the English language
        .InLanguage("en");
```

You can see the order in which the query methods are evaluated in the following diagram:
[![InLanguage method used on ForContentType query with linked items](docsassets/documentation/reference-content-item-query/InLanguageWithLinkedItems.png)](https://docs.kentico.com/docsassets/documentation/reference-content-item-query/InLanguageWithLinkedItems.png)
### InWorkspaces
Selects items from the specified [workspaces](documentation/developers-and-admins/configuration/users/role-management/workspaces). Use workspace code names as specified in the **Workspaces** application. The `InLanguage` method is applied to the result of all its preceding subqueries in a query.
C#
Copy
```
var builder = new ContentItemQueryBuilder();

builder.ForContentType("Sample.Type")
       .ForContentType("Sample.NewsRelease")
       // Selects only items from the WonkaFactory and Acme workspaces
       .InWorkspaces("WonkaFactory", "Acme");
```

Website channel pages are not scoped under workspaces. Combining this method with [ForWebsite](documentation/developers-and-admins/api/content-item-api/reference-content-item-query#ReferenceContentitemquery-ForWebsite) in a single query leads to an empty query result.
More advanced scenario including linked items:
C#
Copy
```
var builder = new ContentItemQueryBuilder();

builder.ForContentType("project.store", subqueryParameters =>
        {
            // Retrieves all linked items up to the maximum depth of 1
            subqueryParameters.WithLinkedItems(1);
        })
        // Selects only items from the WonkaFactory and Acme workspaces
        .InWorkspaces("WonkaFactory", "Acme");
```

You can see the order in which the query methods are evaluated in the following diagram:
[![InWorkspaces method used on ForContentType query with linked items](docsassets/documentation/reference-content-item-query/InWorkspacesWithLinkedItems.png)](https://docs.kentico.com/docsassets/documentation/reference-content-item-query/InWorkspacesWithLinkedItems.png)
## Content query parameterization
### Columns
Limits the columns that are retrieved by the query. See [Content item database structure](documentation/developers-and-admins/api/content-item-api/content-item-database-structure).
If not specified, the query by default includes all columns from all selected content types.
C#
Copy
```
var builder = new ContentItemQueryBuilder();

builder.ForContentType("Sample.Type", subqueryParameters =>
{
    // Retrieves only the 'Title' and 'Content' columns from 'Sample.Type'
    subqueryParameters.Columns("Title", "Content");
});
```

If _Columns_ is called multiple times for a content type, columns from all method calls are included.
The method also supports column aliasing:
C#
Copy
```
builder.ForContentType("Sample.Type1", subqueryParameters =>
{
    // Aliases 'Type1Title' as 'Title'
    subqueryParameters.Columns(QueryColumn.Alias("Type1Title", "Title"));
})
ForContentType("Sample.Type2", subqueryParameters =>
{
    // Aliases 'Type2Title' as 'Title'
    subqueryParameters.Columns(QueryColumn.Alias("Type2Title", "Title"));
})
// Orders both 'Type1' and 'Type2'
.Parameters(globalParameters => globalParameters.OrderBy("Title"));
```

#### UrlPathColumns
A specialized extension method that adds only the columns required for [web page URL retrieval](documentation/developers-and-admins/development/content-retrieval/retrieve-page-content/retrieve-page-urls). This method helps optimize performance when you need only URL-related data.
When used with [ForContentType](documentation/developers-and-admins/api/content-item-api/reference-content-item-query#forcontenttype-parameterization), call the method inside the subquery parameterization action:
C#
**UrlPathColumns usage**
Copy
```
var builder = new ContentItemQueryBuilder();

builder.ForContentType("Sample.Page", subqueryParameters =>
{
    // Retrieves only columns required for URL path generation
    subqueryParameters.ForWebsite("SampleChannel")
                      .UrlPathColumns();
});
 


```

When used with [ForContentTypes](documentation/developers-and-admins/api/content-item-api/reference-content-item-query#forcontenttypes-parameterization), call the method on the global `Parameters` object:
C#
**UrlPathColumns usage**
Copy
```
var builder = new ContentItemQueryBuilder();

builder.ForContentTypes( /* query parameters... */)
       .Parameters(p =>
          // Retrieves only columns required for URL path generation
          p.UrlPathColumns()
       );
 


```

`UrlPathColumns` must be used in conjunction with `ForWebsite` or `WithWebPageData`. It automatically includes all columns required by the system to correctly resolve page URLs.
You can combine `UrlPathColumns` with additional `Columns` calls if you need URL columns in addition to other fields:
C#
**UrlPathColumns usage**
Copy
```
var builder = new ContentItemQueryBuilder();

builder.ForContentType("Sample.Page", subqueryParameters =>
{
    // Retrieves URL path columns plus the Title field
    subqueryParameters.ForWebsite("SampleChannel")
                     .UrlPathColumns()
                     .Columns("Title");
});
 
 


```

### Offset
Offsets the records by the specified `index` (zero-based) and takes the next `X` items specified by `fetch`.
Must be used together with [OrderBy](documentation/developers-and-admins/api/content-item-api/reference-content-item-query#ReferenceContentitemquery-OrderByParam), otherwise the pagination is not applied (as the system cannot guarantee a deterministic ordering of the returned results).
C#
Copy
```
var builder = new ContentItemQueryBuilder();

builder.ForContentType("Sample.Type", subqueryParameters =>
{
    // Takes the next 5 items starting from the 11th
    subqueryParameters.Offset(10, 5);
    subqueryParameters.OrderBy("ContentItemName");
});
```

### IncludeTotalCount
Ensures that every retrieved item stores the total number of items, regardless of pagination applied by _Offset_.
C#
Copy
```
var builder = new ContentItemQueryBuilder();

builder.ForContentType("Sample.Type", subqueryParameters =>
{
    // Includes the total number of items
    subqueryParameters.IncludeTotalCount()
    // Takes the next 5 items starting from the 11th
    subqueryParameters.Offset(10, 5);
    subqueryParameters.OrderBy("ContentItemName");
});
```

After executing the query, use `GetTotalCount` on an item in the result to get the total number of items.
C#
Copy
```
var items = await queryExecutor.GetResult(builder, item => item);
int? totalItemCount = items.First().GetTotalCount();
```

### TopN
Limits the number of records fetched from the database. 
C#
Copy
```
var builder = new ContentItemQueryBuilder();

builder.ForContentType("Sample.Type", subqueryParameters =>
{
    // Takes the first 5 results from the selection
    subqueryParameters.TopN(5);
});
```

### OrderBy
Allows ordering of the results based on the value of a specified column.
C#
Copy
```
var builder = new ContentItemQueryBuilder();

builder.ForContentType("Sample.Type", subqueryParameters =>
{
    // By default, items in the specified columns are sorted in ascending order
    subqueryParameters.OrderBy("ContentItemName");

    // You can parameterize the behavior by providing an instance of 'CMS.DataEngine.OrderByColumn'
    subqueryParameters.OrderBy(new OrderByColumn("ContentItemName", OrderDirection.Descending));
});
```

### Where
Inserts an [SQL WHERE](https://learn.microsoft.com/en-us/sql/t-sql/queries/where-transact-sql) clause into the query.
C#
**WhereTrue**
Copy
```
var builder = new ContentItemQueryBuilder();

builder.ForContentType("Sample.Type", subqueryParameters =>
{
    // Retrieves items that have the 'ShowInBanner' property set to true
    subqueryParameters.Where(where => where.WhereTrue("ShowInBanner"));
});
```

C#
**WhereLike**
Copy
```
var builder = new ContentItemQueryBuilder();

builder.ForContentType("Sample.Type", subqueryParameters =>
{
    // Multiple where operations are implicitly joined by AND
    subqueryParameters.Where(where => where.WhereLike("ColumnA", "value")
                                        .WhereLike("ColumnB", "value"));

    // OR has to be specified explicitly
    subqueryParameters.Where(where => where.WhereLike("ColumnA", "value")
                                        .Or()
                                        .WhereLike("ColumnB", "value"));
});
```

C#
**WhereStartsWith**
Copy
```
var builder = new ContentItemQueryBuilder();

builder.ForContentType("Sample.Type", subqueryParameters =>
{
    // Retrieves items whose name starts with with 'Apple'
    subqueryParameters.Where(where => where.WhereStartsWith("ContentItemName", "Apple"));
});
```

The set of available `Where`extensions matches the expressivity of the SQL WHERE syntax. This page provides examples of only a few of the available methods.
### WhereContainsTags
Limits the query to content items that contain the [specified tags](documentation/developers-and-admins/configuration/taxonomies).
C#
**WhereContainsTags**
Copy
```
// A collection of tags, e.g., obtained from a Tag selector
IEnumerable<Guid> tagIdentifiers;

var builder = new ContentItemQueryBuilder()
    .ForContentType(
        "Some.Type",
        subqueryParameters =>
            // Retrieves items with the specified tags
            subqueryParameters.Where(where =>
                where.WhereContainsTags("SomeTaxonomy", tagIdentifiers))
    ).InLanguage("en");
```

C#
**WhereContainsTags**
Copy
```
// A collection of tags, e.g., obtained from a Tag selector
IEnumerable<Guid> tagIdentifiers;
var tagCollection = await TagCollection.Create(tagIdentifiers);

var builder = new ContentItemQueryBuilder()
    .ForContentType(
        ArticlePage.CONTENT_TYPE_NAME,
        subqueryParameters =>
            // Retrieves items with the specified tags and any child tags
            subqueryParameters.Where(where =>
                where.WhereContainsTags("SomeTaxonomy", tagCollection))
    ).InLanguage("en");
```

## ForContentType parameterization
Methods described in this section can only be called from within subqueries generated by a `ContentItemBuilder.ForContentType` call. 
### ForWebsite 
Configures the query to [retrieve pages](documentation/developers-and-admins/development/content-retrieval/retrieve-page-content) from a specified [website channel](documentation/developers-and-admins/configuration/website-channel-management). Specify the following parameters:
  * `websiteChannelName` – code name of the website channel from which the pages are retrieved.
  * `pathMatch` – a parameter of type `PathMatch` used to limit the retrieved pages only to a certain section of the website’s content tree.
  * `includeUrlPath` – indicates if the URL path should be included in the retrieved data.


C#
**ForWebsite**
Copy
```
var builder = new ContentItemQueryBuilder();

builder.ForContentType("Sample.Type", subqueryParameters =>
{
    // Retrieves pages from the specified website channel
    subqueryParameters.ForWebsite(
                            websiteChannelName: "DancingGoatPages",
                            pathMatch: PathMatch.Children("/Articles"),
                            includeUrlPath: true);
});
```

See [Retrieve page content](documentation/developers-and-admins/development/content-retrieval/retrieve-page-content) for more information.
### WithLinkedItems 
Configures query to include [linked content items](documentation/business-users/content-hub/content-items#link-content-items) recursively up to the specified depth.
C#
**WithLinkedItems**
Copy
```
var builder = new ContentItemQueryBuilder();

builder.ForContentType("Sample.Type", subqueryParameters =>
{
    // Retrieves all linked items up to the maximum depth of 2
    subqueryParameters.WithLinkedItems(2);
});
```

If the collection includes web page items and you want to use web page specific data (such as URL and tree path), specify the `IncludeWebPageData` option.
C#
**IncludeWebPageData usage**
Copy
```
var builder = new ContentItemQueryBuilder();

builder.ForContentType("Sample.Type", subqueryParameters =>
{
    // Retrieves all linked items up to the maximum depth of 2
    // including web page data of linked web page items
    subqueryParameters
        .WithLinkedItems(2, options => options.IncludeWebPageData());
});
```

To see how the query result is bound to a model class, check out [WithLinkedItems mapping](documentation/developers-and-admins/api/content-item-api/reference-content-item-query#ReferenceContentitemquery-WithLinkedItemsMap).
### Linking 
Retrieves all content items of the type specified in `ForContentType` that reference any of the content items from the provided collection in the given field. Enables loading data on-demand (lazily).
C#
**Linking**
Copy
```
var builder = new ContentItemQueryBuilder();

// Retrieves items of the 'project.staff' content type
builder.ForContentType("project.staff", subqueryParameters =>
{
    // Retrieves all content items of 'project.staff' type that reference any
    // of the items in 'managersCollection' from their 'ManagerField' field
    subqueryParameters.Linking("ManagerField", managersCollection);
});
```

For more information see [Linking details](documentation/developers-and-admins/api/content-item-api/reference-content-item-query#ReferenceContentitemquery-Linking).
### LinkedFrom 
Retrieves content items of the type specified in `ForContentType` that are linked from the given field of items in the provided collection with the given content type. Enables loading data on-demand (lazily).
C#
**LinkedFrom**
Copy
```
var builder = new ContentItemQueryBuilder();

// Retrieves items of the 'project.staff' content type
builder.ForContentType("project.staff", subqueryParameters =>
{
    // Items are retrieved for a collection of
    // 'project.store' items - 'storesCollection' - and the field 'StaffField'
    subqueryParameters.LinkedFrom("project.store", "StaffField", storesCollection);
});
```

For more information see [LinkedFrom details](documentation/developers-and-admins/api/content-item-api/reference-content-item-query#ReferenceContentitemquery-LinkedFrom).
### LinkedFromSchemaField
Retrieves all content items of the type specified in `ForContentType` that are linked from a collection of items via a [reusable schema field](documentation/developers-and-admins/development/content-types/reusable-field-schemas).
C#
**LinkedFromSchemaField usage example**
Copy
```
var builder = new ContentItemQueryBuilder();

builder.ForContentType("Sample.Type", subqueryParameters =>
{
    subqueryParameters.LinkedFromSchemaField("SchemaFieldCodeName", contentItems);
});
```

For more information see [LinkedFromSchemaField details](documentation/developers-and-admins/api/content-item-api/reference-content-item-query#FCT-LinkedFromSchemaField).
### SetUrlLanguageBehavior
Defines the behavior for the language slug of URL paths when web pages are retrieved in a fallback language. This method is relevant when requesting multilingual content ([InLanguage](documentation/developers-and-admins/api/content-item-api/reference-content-item-query#inlanguage)) and [retrieving page URL](documentation/developers-and-admins/development/content-retrieval/retrieve-page-content/retrieve-page-urls) information (e.g., through methods like [GetUrl()](documentation/developers-and-admins/development/content-retrieval/retrieve-page-content/retrieve-page-urls), often with [ForWebsite](documentation/developers-and-admins/api/content-item-api/reference-content-item-query#ReferenceContentitemquery-ForWebsite) or [WithWebPageData](documentation/developers-and-admins/api/content-item-api/reference-content-item-query#FCTs-WWPD)).
It accepts a `UrlLanguageBehavior` enum value:
  * `UrlLanguageBehavior.UseRequestedLanguage` – When a page is served in a [fallback language](documentation/developers-and-admins/configuration/languages#language-fallbacks), its URL path is generated based on the language originally requested by the `InLanguage` method. For instance, if Spanish is requested and an English page is returned as a fallback, the URL path will still be structured as if it were a Spanish page (e.g., using URL prefixes as configured in the [Languages](documentation/developers-and-admins/configuration/languages#set-up-a-new-language) application).
  * `UrlLanguageBehavior.UseFallbackLanguage` (Default) – When a page is served in a fallback language, its URL path is generated based on the language of the actual fallback content. In the example below, the URL path would be structured as an English page.


C#
**Using SetUrlLanguageBehavior**
Copy
```
// Assume 'builder' is an initialized ContentItemQueryBuilder

// Within ForContentType configuration:
builder.ForContentType("Sample.PageType", config =>
{
    config.ForWebsite("MyWebsiteChannel")
          .SetUrlLanguageBehavior(UrlLanguageBehavior.UseRequestedLanguage)
})
// Requesting multilingual content
.InLanguage("spanish");

// After executing the query:
IEnumerable<SamplePageType> pages = await executor.GetMappedWebPageResult<SamplePageType>(builder);
foreach (var page in pages)
{
    // If 'page' is an English fallback for a Spanish request,
    // its url will be in the format:
    //
    // '/<spanishLanguageName>/my-page-slug'
    //
    // due to 'UseRequestedLanguage'.
    WebPageUrl url = page.GetUrl();
}
 


```

## ForContentTypes parameterization
Methods described in this section can only be called from within subqueries generated by a `ContentItemBuilder.ForContentTypes` call.
### OfContentType
Selects items with the specified content types.
C#
**Retrieve based on content type**
Copy
```
var builder = new ContentItemQueryBuilder();

builder.ForContentTypes(parameters =>
{
    // Retrieves items of the 'Acme.Article', 'Acme.Blog' content types
    parameters.OfContentType("Acme.Article", "Acme.Blog");
});
```

### OfReusableSchema
Selects items with the specified [reusable field schema](documentation/developers-and-admins/development/content-types/reusable-field-schemas).
C#
**Retrieve based on reusable field schema**
Copy
```
var builder = new ContentItemQueryBuilder();

builder.ForContentTypes(parameters =>
{
    // Retrieves items with the 'PageMetadata' reusable field schema
    parameters.OfReusableSchema("PageMetadata");
});
```

### ForWebsite
Provides multiple overloads that can:
  * select all pages in the system
  * select pages based on their ID, GUID, or code names
  * select pages from specific [website channels](documentation/developers-and-admins/configuration/website-channel-management) and paths


C#
**Retrieve web page items**
Copy
```
var builder = new ContentItemQueryBuilder();

builder.ForContentTypes(parameters =>
{
    // Calls are mutually exclusive
    // Shown to demonstrate available overloads

    // Retrieves all pages in the system
    parameters.ForWebsite();

    // Retrieves pages with the provided GUIDs
    // (e.g., from the 'Page selector' component)
    parameters.ForWebsite(webPageGuids);

    // Retrieves all pages under the 'Acme' channel and 'Articles' page path
    parameters.ForWebsite(
            websiteChannelName: "Acme",
            pathMatch: PathMatch.Children("/Articles"));
});
```

Each overload also provides the optional `includeUrlPath` path parameter. `true` by default, it indicates whether web page URL data should be included in the query.
If you want to retrieve pages and reusable content items in a single query, see the [WithWebPageData](documentation/developers-and-admins/api/content-item-api/reference-content-item-query#FCTs-WWPD) method.
### WithContentTypeFields 
By default, the result returned by `ForContentTypes` contains content item metadata (_CMS_ContentItem_ table [Content item database structure](documentation/developers-and-admins/api/content-item-api/content-item-database-structure#content-items)) and [reusable field schema](documentation/developers-and-admins/development/content-types/reusable-field-schemas) data. `WithContentTypeFields` also adds [content type-specific fields](documentation/developers-and-admins/development/content-types#add-fields) to the result.
C#
**WithContentTypeFields example**
Copy
```
var builder = new ContentItemQueryBuilder();

builder.ForContentTypes(parameters =>
{
    // Retrieves items with the 'PageMetadata' reusable field schema
    // and includes content-type specific fields in the result
    parameters.OfReusableSchema("PageMetadata")
                .WithContentTypeFields();
});
```

### WithWebPageData 
By default, the result returned by `ForContentTypes` contains content item metadata (_CMS_ContentItem_ table [Content item database structure](documentation/developers-and-admins/api/content-item-api/content-item-database-structure#content-items)) and [reusable field schema](documentation/developers-and-admins/development/content-types/reusable-field-schemas) data. `WithWebPageData` also adds website content-specific fields (such as URL and tree path) to the result.
C#
**WithWebPageData example**
Copy
```
var builder = new ContentItemQueryBuilder();

builder.ForContentTypes(parameters =>
{
    // Retrieves items with the 'PageMetadata' reusable field schema
    // and includes web page specific fields in the result
    parameters.OfReusableSchema("PageMetadata")
                .WithWebPageData();
});
```

### WithLinkedItems
Configures query to include [linked content items](documentation/business-users/content-hub/content-items#link-content-items) recursively up to the specified depth. The parameterization behaves identically to its alternative in [ForContentType](documentation/developers-and-admins/api/content-item-api/reference-content-item-query#ReferenceContentitemquery-WithLinkedItems).
C#
**WithLinkedItems usage**
Copy
```
var builder = new ContentItemQueryBuilder();

builder.ForContentTypes(subqueryParameters =>
{
    // Retrieves all linked items up to the maximum depth of 2
    subqueryParameters
        .OfContentType(Article.CONTENT_TYPE_NAME)
        .WithLinkedItems(2);
});
```

If the collection of linked items contains web page items, you need to specify the `IncludeWebPageData` option to include web page specific data (such as URL and tree path) of the web page items:
C#
**IncludeWebPageData usage**
Copy
```
var builder = new ContentItemQueryBuilder();

builder.ForContentTypes(subqueryParameters =>
{
    // Retrieves all linked items up to the maximum depth of 2
    // including web page data of linked web page items
    subqueryParameters
        .OfContentType(Article.CONTENT_TYPE_NAME)
        .WithLinkedItems(2, options => options.IncludeWebPageData());
});
```

To see how the query result is bound to a model class, check out [WithLinkedItems mapping](documentation/developers-and-admins/api/content-item-api/reference-content-item-query#ReferenceContentitemquery-WithLinkedItemsMap).
### Linking
The method behaves identically to [ForContentType.Linking](documentation/developers-and-admins/api/content-item-api/reference-content-item-query#ReferenceContentitemquery-ForContentTypeLinking), with the following exceptions:
  * You need to specify the name of the content type whose items you want to retrieve, in addition to the field used to link the items. This is necessary to prevent ambiguities in case the searched content types contain identical field names.
  * The usage is limited to one `Linking` call for each `ForContentTypes` subquery.


C#
**ForContentTypes.Linking usage**
Copy
```
var builder = new ContentItemQueryBuilder();
builder.ForContentTypes(subqueryParameters =>
{
    // Retrieves items of the 'project.staff' type that link to
    // any of the content items in 'managersCollection' via their 'ManagerField' field
    subqueryParameters.Linking("project.staff", "ManagerField", managersCollection);
});
```

For more information see [Linking details](documentation/developers-and-admins/api/content-item-api/reference-content-item-query#ReferenceContentitemquery-Linking).
### LinkedFrom
The behavior is identical to [ForContentType.LinkedFrom](documentation/developers-and-admins/api/content-item-api/reference-content-item-query#ReferenceContentitemquery-ForContentTypeLinkedFrom), with the only difference being that you cannot chain multiple calls within a single subquery.
For more information see [LinkedFrom details](documentation/developers-and-admins/api/content-item-api/reference-content-item-query#ReferenceContentitemquery-LinkedFrom).
### LinkingSchemaField
Retrieves all content items that link to a collection of items via the specified [reusable schema field](documentation/developers-and-admins/development/content-types/reusable-field-schemas). [LinkedFromSchemaField](documentation/developers-and-admins/api/content-item-api/reference-content-item-query#FCT-LinkedFromSchemaField) complements this method by retrieving links from the opposite direction.
C#
**LinkingSchemaField method usage**
Copy
```
var imageIdentifiers = new List<int>() { 1, 2, 3 };

var builder = new ContentItemQueryBuilder().ForContentTypes(q =>
{
    q.LinkingSchemaField("ProductImage", imageIdentifiers);
});
```

For more information see [LinkingSchemaField details](documentation/developers-and-admins/api/content-item-api/reference-content-item-query#FCT-LinkingSchemaField).
### LinkedFromSchemaField
Retrieves all content items that are linked from a collection of items via a [reusable schema field](documentation/developers-and-admins/development/content-types/reusable-field-schemas). [LinkingSchemaField](documentation/developers-and-admins/api/content-item-api/reference-content-item-query#FCT-LinkingSchemaField) complements this method by retrieving links from the opposite direction.
C#
**LinkedFromSchemaField method usage**
Copy
```
var builder = new ContentItemQueryBuilder().ForContentTypes(q =>
{
    q.LinkedFromSchemaField("ProductImage", productIdentifiers);
});
```

For more information see [LinkedFromSchemaField details](documentation/developers-and-admins/api/content-item-api/reference-content-item-query#FCT-LinkedFromSchemaField).
### InSmartFolder
Retrieves content items that fulfill the filter conditions of the specified [smart folder](documentation/business-users/content-hub/content-hub-folders#smart-folders). This allows content editors to control which items are retrieved directly in the _Content hub_ UI, without needing to adjust the code.
The smart folder can be specified by its ID, GUID, or code name. You can get the smart folder GUID from a field using the [Smart folder selector](documentation/developers-and-admins/customization/extend-the-administration-interface/ui-form-components/reference-admin-ui-form-components#smart-folder-selector) UI form component. You can also find the identifiers of smart folders in the _Content hub_ application – expand the menu actions of a folder and select _Properties_.
C#
**InSmartFolder method usage**
Copy
```
var builder = new ContentItemQueryBuilder();

// Retrieves content items from the specified smart folder
builder.ForContentTypes(parameters => parameters.InSmartFolder(smartFolderGuid));
```

The `InSmartFolder` parameterization causes the query to return an **empty result** if the specified smart folder:
  * Doesn’t exist
  * Doesn’t have dynamic content delivery enabled
  * Has invalid filter conditions (for example if a tag saved in the _Taxonomy_ filter option was later deleted)


The following scenarios are unsupported and result in an exception:
  * Using multiple `inSmartFolder` calls for a single query
  * Combining `InSmartFolder` with `ForWebsite`parameterization (smart folders are only supported for reusable content items, not pages)


If you need to ensure that only items of one specific content type are retrieved (regardless of the smart folder’s filter condition), add `OfContentType` to the parameterization.
C#
**InSmartFolder usage for a specific content type**
Copy
```
var builder = new ContentItemQueryBuilder();

// Retrieves items of one specific content type from a smart folder
builder.ForContentTypes(parameters =>
{
    parameters.InSmartFolder(smartFolderGuid)
                .OfContentType("Sample.Type");
});
```

### SetUrlLanguageBehavior
Defines the behavior for the language of URL paths when web pages are retrieved in a fallback language. This method is particularly relevant when using [InLanguage](documentation/developers-and-admins/api/content-item-api/reference-content-item-query#inlanguage) and retrieving page URL information (e.g., through methods like [GetUrl()](documentation/developers-and-admins/development/content-retrieval/retrieve-page-content/retrieve-page-urls) after ensuring necessary columns are loaded, often with [ForWebsite](documentation/developers-and-admins/api/content-item-api/reference-content-item-query#ReferenceContentitemquery-ForWebsite) or [WithWebPageData](documentation/developers-and-admins/api/content-item-api/reference-content-item-query#FCTs-WWPD)).
It accepts a `UrlLanguageBehavior` enum value:
  * `UrlLanguageBehavior.UseRequestedLanguage` – When a page is served in a [fallback language](documentation/developers-and-admins/configuration/languages#language-fallbacks), its URL path is generated based on the language originally requested by the `InLanguage` method. For instance, if Spanish is requested and an English page is returned as a fallback, the URL path will still be structured as if it were a Spanish page (e.g., using URL prefixes as configured in the [Languages](documentation/developers-and-admins/configuration/languages#set-up-a-new-language) application).
  * `UrlLanguageBehavior.UseFallbackLanguage` (Default) – When a page is served in a fallback language, its URL path is generated based on the language of the actual fallback content. In the example below, the URL path would be structured as an English page.


C#
**Using SetUrlLanguageBehavior**
Copy
```
var builder = new ContentItemQueryBuilder();

builder.ForContentTypes(parameters =>
{
    parameters.ForWebsite("MyWebsiteChannel")
              .WithWebPageData() // Ensure web page data is loaded
              .SetUrlLanguageBehavior(UrlLanguageBehavior.UseRequestedLanguage)
})
.InLanguage("spanish"); // Requesting content in Spanish

// After executing the query:
IEnumerable<SamplePageType> pages = await executor.GetMappedWebPageResult<SamplePageType>(builder);
foreach (var page in pages)
{
    // If 'page' is an English fallback for a Spanish request,
    // its url will be in the format:
    //
    // '/<spanishLanguageName>/my-page-slug'
    //
    // due to 'UseRequestedLanguage'.
    WebPageUrl url = page.GetUrl();

}
 


```

### Where conditions
Where parameterization can be added to `ForContentTypes` queries using [Parameters](documentation/developers-and-admins/api/content-item-api/reference-content-item-query#Builder-Parameters).
C#
**Adding Where conditions**
Copy
```
var builder = new ContentItemQueryBuilder();
builder.ForContentTypes(parameters =>
{
    // ...
}).Parameters(parameters =>
{
    parameters.Where(where => where) //...
});
```

## Method details
Methods described in this section can only be called from within subqueries generated by a `ContentItemBuilder.ForContentTypes` call or a `ContentItemBuilder.ForContentType` call.
### WithLinkedItems 
`IContentQueryExecutor.GetMappedResult`, `IContentQueryExecutor.GetMappedWebPageResult`, or using the provided `IContentQueryModelTypeMapper` automatically binds the linked content item hierarchy to the specified model.
C#
Copy
```
IEnumerable<ModelClass> data =
            queryExecutor.GetMappedResult<ModelClass>(builder);
```

When mapping the data manually, use `GetLinkedItems` on the result to get the next level of references. This can be repeated up until the specified recursion level.
C#
Copy
```
var data = queryExecutor.GetResult<ContentItemDto>(builder, resultSelector);

private ContentItemDto resultSelector(IContentQueryDataContainer itemData)
{
    var item = new ContentItemDto
    {
        Title = itemData.GetValue<string>("Title"),
        Text = itemData.GetValue<string>("Text"),

        // Retrieves first-level linked items from the 'Author' field
        AuthorName = itemData.GetLinkedItems("Author").First()
                                .GetValue<string>("Name");

        // Retrieves second-level linked items from the 'Author' field
        AuthorProfileBlurb =
            itemData.GetLinkedItems("Author").First()
                    .GetLinkedItems("Profile").First()
                    .GetValue<string>("ProfileBlurb");
    };

    return item;
}
```

### Linking 
Retrieves all content items which reference any of the content items from the provided collection in the specified field. Enables loading data on-demand (lazily).
The following diagram illustrates the behavior on a simple content model:
[![Linking usage visualization](docsassets/documentation/reference-content-item-query/LinkingBasic.jpg)](https://docs.kentico.com/docsassets/documentation/reference-content-item-query/LinkingBasic.jpg)
Combining this method with [LinkedFrom](documentation/developers-and-admins/api/content-item-api/reference-content-item-query#ReferenceContentitemquery-LinkedFrom) in a single subquery is not supported.
The method can also be used together with `WithLinkedItems`. For example:
C#
**Linking and WithLinkedItems**
Copy
```
var builder = new ContentItemQueryBuilder();

// Retrieves items of the 'project.staff' content type
builder.ForContentType("project.staff", subqueryParameters =>
{
    // Retrieves all items of 'project.staff' type that reference any of the
    // items in 'managersCollection' from their 'ManagerField' together with
    // all second-level references for the selected 'project.staff' items
    subqueryParameters.Linking("ManagerField", managersCollection)
                    .WithLinkedItems(2);
});
```

[![Linking usage visualization](docsassets/documentation/reference-content-item-query/LinkingCombined.jpg)](https://docs.kentico.com/docsassets/documentation/reference-content-item-query/LinkingCombined.jpg)
The collection of items for which to retrieve references must implement the `IContentItemIdentifier` interface. The interface ensures fields required by each data model that wants to leverage this method. The fields must be bound to the model during model binding within `ContentQueryExecutor.GetResult`.
[Generated content type classes](documentation/developers-and-admins/api/generate-code-files-for-system-objects) already implement `IContentItemIdentifier` via the `SystemFields` property. The following approach applies for custom model classes.
C#
**IContentItemIdentifier fields in custom model classes**
Copy
```
var data = queryExecutor.GetResult<ContentItemDto>(builder, resultSelector);

ManagerDto resultSelector(IContentQueryDataContainer itemData)
{
    var item = new ManagerDto
    {
        // Binds fields required by 'Linking'
        ContentItemID = itemData.ContentItemID,
        ContentItemLanguageID = itemData.ContentItemDataContentLanguageID,
        // other fields...
    };

    return item;
}

// Example custom model class binding content items of the 'Manager' content type
class ManagerDto : IContentItemIdentifier
{
    public int ContentItemID { get; }
    public int ContentLanguageID { get; }
    public IEnumerable<ContentItemReference> PictureField { get; }
    public IEnumerable<ContentItemReference> AwardsField { get; }
}
```

### LinkedFrom 
Retrieves content items of a specific type linked from the given field in the provided collection. Enables loading data on-demand (lazily).
The following diagram illustrates the behavior on a simple content model. Red arrows trace query evaluation:
[![LinkedFrom usage visualization](docsassets/documentation/reference-content-item-query/LinkedFromBasic.jpg)](https://docs.kentico.com/docsassets/documentation/reference-content-item-query/LinkedFromBasic.jpg)
Combining this method with [Linking](documentation/developers-and-admins/api/content-item-api/reference-content-item-query#ReferenceContentitemquery-Linking) in a single subquery is not supported.
The method can also be used together with `WithLinkedItems`. For example:
C#
**Linked from and WithLinkedItems**
Copy
```
var builder = new ContentItemQueryBuilder();

// Retrieves items of the 'project.staff' content type
builder.ForContentType("project.staff", subqueryParameters =>
{
    // Items are retrieved for a collection of 'project.store' items and the field 'StaffField'
    // together with all first-level references for the selected 'project.staff' items
    subqueryParameters.LinkedFrom("project.store", "StaffField", storesCollection)
                        .WithLinkedItems(1);
});
```

[![LinkedFrom usage visualization](docsassets/documentation/reference-content-item-query/LinkedFromCombined.jpg)](https://docs.kentico.com/docsassets/documentation/reference-content-item-query/LinkedFromCombined.jpg)
The collection of items for which to retrieve references must implement the `IContentItemIdentifier` interface. The interface ensures fields required by each data model that wants to leverage this method. The fields must be bound to the model during model binding within `ContentQueryExecutor.GetResult`.
[Generated content type classes](documentation/developers-and-admins/api/generate-code-files-for-system-objects) by default implement `IContentItemIdentifier` via the `SystemFields` property. The following approach applies for custom model classes.
C#
**IContentItemIdentifier fields in custom model classes**
Copy
```
var data = queryExecutor.GetResult<ContentItemDto>(builder, resultSelector);

StoreDto resultSelector(IContentQueryDataContainer itemData)
{
    var item = new StoreDto
    {
        // Binds fields required by `LinkedFrom`
        ContentItemID = itemData.ContentItemID,
        ContentItemLanguageID = itemData.ContentItemDataContentLanguageID,
        // other fields...
    };

    return item;
}

// Example custom model class binding content items of the 'Store' content type
class StoreDto : IContentItemIdentifier
{
    public int ContentItemID { get; }
    public int ContentLanguageID { get; }
    public IEnumerable<ContentItemReference> StaffField { get; }
    public IEnumerable<ContentItemReference> ReferenceField { get; }
    public IEnumerable<ContentItemReference> FaqField { get; }
}
```

### LinkingSchemaField 
This method can only be called from within subqueries generated by a `ContentItemBuilder.ForContentTypes` call.
Retrieves all content items that link to a collection of items via the specified [reusable schema field](documentation/developers-and-admins/development/content-types/reusable-field-schemas). [LinkedFromSchemaField](documentation/developers-and-admins/api/content-item-api/reference-content-item-query#FCT-LinkedFromSchemaField) complements this method by retrieving links from the opposite direction.
See the following diagram for an illustration of the method’s behavior on a simplified content model. The query retrieves all content items that link to the images from `imageIdentifiers` via the _ProductImage_ reusable schema field. Red arrows trace the query evaluation:
[![LinkinkSchemaField method demonstration](docsassets/documentation/reference-content-item-query/LinkingSchemaFields.png)](https://docs.kentico.com/docsassets/documentation/reference-content-item-query/LinkingSchemaFields.png)
In the diagram above, the method is called with the following parameters:
  * `"ProductImage"` – the code name of a field that belongs to a reusable field schema. Reusable schema field code names must be unique across the system, it’s therefore sufficient to target fields directly.
  * `imageIdentifiers` – IDs of content items with the _Image_ content type.


### LinkedFromSchemaField 
Retrieves all content items that are linked from a collection of items via a [reusable schema field](documentation/developers-and-admins/development/content-types/reusable-field-schemas). [LinkingSchemaField](documentation/developers-and-admins/api/content-item-api/reference-content-item-query#FCT-LinkingSchemaField) complements this method by retrieving links from the opposite direction.
See the following diagram for an illustration of the method’s behavior on a simplified content model. The query retrieves all _Image_ content items that are linked from `productIdentifiers` via the _ProductImage_ reusable schema field. Red arrows trace the query evaluation:
[![LinkedFromSchemaField method demonstration](docsassets/documentation/reference-content-item-query/LinkedFromSchemaFields.png)](https://docs.kentico.com/docsassets/documentation/reference-content-item-query/LinkedFromSchemaFields.png)
In the diagram above, the method is called with the following parameters:
  * `"ProductImage"` – the code name of a field that belongs to a reusable field schema. Reusable schema field code names are unique and can be targeted directly without specifying the corresponding schema.
  * `productIdentifiers` – a collection of content items from content types using the _ProductFields_ reusable field schema.


The collection of items for which to retrieve references must implement the `IContentItemIdentifier` interface. The interface ensures fields required by each data model that wants to leverage this method.
[Generated content type classes](documentation/developers-and-admins/api/generate-code-files-for-system-objects) by default implement `IContentItemIdentifier` via the `SystemFields` property.
## IContentQueryExecutor configuration
Apart from configuring the query itself, you can also fine tune its execution. You can do so by setting the properties of the `ContentQueryExecutionOptions` attribute and providing it to the `IContentQueryExecutor` interface.
Property |  Description  
---|---  
`ForPreview` |  If set to _true_ , the query executor retrieves the queried items in their latest available version, regardless of their workflow state. Otherwise, the latest published version is retrieved. Default value: _false_.  
`IncludeSecuredItems` |  If set to _true_ , the query executor retrieves all items according to the query, including secured items. Otherwise, only items that are not secured are included in the query. Default value: _false_.  
To see how the configuration affects the data retrieval, check out the examples at [Retrieve page content](documentation/developers-and-admins/development/content-retrieval/retrieve-page-content).