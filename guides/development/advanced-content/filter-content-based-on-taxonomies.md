---
source: https://docs.kentico.com/guides/development/advanced-content/filter-content-based-on-taxonomies
scrape_date: 2026-01-26
---

  * [Home](/guides)
  * [Development](/guides/development)
  * [Advanced content](/guides/development/advanced-content)
  * Filter content with taxonomies and reusable field schemas 


# Filter content with taxonomies and reusable field schemas
[Taxonomies](/documentation/developers-and-admins/configuration/taxonomies) in Xperience by Kentico provide a powerful tool for your customers to categorize and organize content.
Each taxonomy is a _group of related tags_ (e.g., PC features: external GPU, disc drive, tower size…). Content editors assign these tags to content items with a field allowing the given taxonomy. Then they can, for example, configure a widget to display a list of content items with a specific set of tags.
Let’s empower your content editors to do just that - filter content items by taxonomy tags in a widget listing.
In the process, we’ll cover how you can easily query items of different content types as long as they share a reusable field schema. Let’s dive in!
## Before you start
This guide requires the following:
  * Familiarity with [C#](https://learn.microsoft.com/en-us/dotnet/csharp/), [.NET Core](https://learn.microsoft.com/en-us/dotnet/), [Dependency injection](https://learn.microsoft.com/en-us/dotnet/core/extensions/dependency-injection), and the [MVC pattern](https://learn.microsoft.com/en-us/aspnet/core/mvc/overview).
  * A running instance of Xperience by Kentico, preferably [30.11.1](/documentation/changelog) or higher. 
Some features covered in the training guides may not work in older versions. 
  * Basic experience with [Page Builder widgets](/documentation/developers-and-admins/development/builders/page-builder/widgets-for-page-builder) and [reusable field schemas](/documentation/developers-and-admins/development/content-types/reusable-field-schemas).


**Code samples**
You can find a project with completed, working versions of code samples from this guide and others in the [finished branch](https://github.com/Kentico/xperience-by-kentico-training-guides/tree/finished) of the _Training guides_ repository.
The [main branch](https://github.com/Kentico/xperience-by-kentico-training-guides) of the repository provides a starting point to code along with the guides.
The code samples in this guide are for [.NET 8](https://learn.microsoft.com/en-us/dotnet/core/whats-new/dotnet-8/overview) only.
They come from a project that uses [implicit using directives](https://learn.microsoft.com/en-us/dotnet/core/project-sdk/overview#implicit-using-directives). You may need to add additional `using` directives to your code if your project does not use this feature.
## Consider the scenario
Earlier in this series, we explored reusable field schemas through the lens of content model evolution. You have adjusted your project to work with [two new Article content types](/guides/development/advanced-content/work-with-reusable-field-schemas#consider-the-scenario) that share an _Article schema_ , alongside with a deprecated one.
Perhaps you recall that when you added the _Article list_ widget to your _Home_ page to check your progress and selected a parent page from the content tree, the widget rendered articles of all content types, both old and new.
Now imagine that your customer’s content editors want more control. They’re asking for the ability to filter articles in the widget by specific tags, so they can display only the articles that match the categories they’re interested in.
Your browser does not support the video tag. 
## Examine the current state
Take a look at the _Article schema_ in Xperience administration. Notice the _ArticleSchemaCategory_ field with the **Taxonomy** data type. This field allows editors to assign one or more tags from the _Article category_ taxonomy to any content item implementing this schema.
[![Taxonomy field in the Article schema in Xperience administration](/docsassets/guides/filter-content-based-on-taxonomies/taxonomy-field.png)](/docsassets/guides/filter-content-based-on-taxonomies/taxonomy-field.png)
The _Article category_ taxonomy already exists in our project and includes two tags: **Animals** and **Plants**. You can find it in the **Taxonomies** application under the **Configuration** category.
[![Article category taxonomy in the administration interface](/docsassets/guides/filter-content-based-on-taxonomies/article-category-taxonomy.png)](/docsassets/guides/filter-content-based-on-taxonomies/article-category-taxonomy.png)
When you examine any of the _Article (general)_ or _Article (interview)_ items in the **Content hub** , you’ll see they all have tags or an option to add tags in the **Category** field.
## Implement the filtering
Now, we can implement filtering based on the _Article category_ taxonomy.
### Add new widget property
First, let’s add a [tag selector](/documentation/developers-and-admins/customization/extend-the-administration-interface/ui-form-components/reference-admin-ui-form-components#tag-selector) to the widget UI to so that editors can select the desired tags.
Find the definition of the [Article list widget properties](https://github.com/Kentico/xperience-by-kentico-training-guides/blob/main/src/TrainingGuides.Web/Features/Articles/Widgets/ArticleList/ArticleListWidgetProperties.cs) in the Training guides repository.
Add a new `IEnumerable<TagReference>` property called `Tags` between the existing `ContentTreeSection` and `TopN`. Decorate the property with `TagSelectorComponent` attribute.
Specify allowed taxonomy passing our taxonomy code name, `ArticleCategory`, as the first parameter.
To refresh your memory on defining widget properties revisit our [Build a simple call-to-action widget](/guides/development/page-builder/build-simple-cta-widget#define-widget-properties) guide.
C#
**ArticleListWidgetProperties.cs**
Copy
```
...
using CMS.ContentEngine;

namespace TrainingGuides.Web.Features.Articles.Widgets.ArticleList;

public class ArticleListWidgetProperties : IWidgetProperties
{
    [ContentItemSelectorComponent(
        [
            ArticlePage.CONTENT_TYPE_NAME,
            DownloadsPage.CONTENT_TYPE_NAME,
            EmptyPage.CONTENT_TYPE_NAME,
            LandingPage.CONTENT_TYPE_NAME,
            ProductPage.CONTENT_TYPE_NAME
        ],
        Label = "Select a parent page to pull articles from",
        MaximumItems = 1,
        Order = 10)]
    public IEnumerable<ContentItemReference> ContentTreeSection { get; set; } = [];

    // new property definition
    [TagSelectorComponent(
        "ArticleCategory",
        Label = "Filter to categories",
        ExplanationText = "Select 0, 1 or more Article Type tags. Shows all if none are selected",
        Order = 15)]
    public IEnumerable<TagReference> Tags { get; set; } = [];

    ...
}
...
```

Run the project and navigate to Page Builder mode of any page that contains Article list widget. In the widget configuration you should see the new tag selector control allowing you to pick **Animals** or **Plants** tags.
Your browser does not support the video tag. 
### Adjust widget view component
Now, let’s add some logic to the widget view component. It needs to account for the tags the editor has chosen when retrieving content.
Take a look at the [ArticleListWidgetViewComponent.cs](https://github.com/Kentico/xperience-by-kentico-training-guides/blob/main/src/TrainingGuides.Web/Features/Articles/Widgets/ArticleList/ArticleListWidgetViewComponent.cs) file.
The `InvokeAsync` method calls `RetrieveArticlePages` to fetch the relevant article pages from the Content hub, passing in the editor’s content tree selection.
Then, it orders and further filters the articles before constructing a view model and returning the widget view.
C#
**ArticleListWidgetViewComponent.cs - old implementation**
Copy
```
...
public async Task<ViewViewComponentResult> InvokeAsync(ArticleListWidgetProperties properties)
{
    var model = new ArticleListWidgetViewModel();

    if (!properties.ContentTreeSection.IsNullOrEmpty())
    {
        var articlePages = await RetrieveArticlePages(properties.ContentTreeSection.First());

        model.Articles = (properties.OrderBy.Equals("OldestFirst", StringComparison.OrdinalIgnoreCase)
            ? (await GetArticlePageViewModels(articlePages)).OrderBy(article => article.CreatedOn)
            : (await GetArticlePageViewModels(articlePages)).OrderByDescending(article => article.CreatedOn))
            .Take(properties.TopN)
            .ToList();

        model.CtaText = properties.CtaText;
    }

    return View("~/Features/Articles/Widgets/ArticleList/ArticleListWidget.cshtml", model);
}
...
```

We’ll adjust the private method `RetrieveArticlePages` to consider the new `Tags` property in the following way:
C#
**ArticleListWidgetViewComponent.cs**
Copy
```
...
// add IEnumerable of tag references as a new parameter
private async Task<IEnumerable<ArticlePage>> RetrieveArticlePages(ContentItemReference parentPageSelection, IEnumerable<TagReference> tags)
{
    var selectedPageGuid = parentPageSelection.Identifier;

    var selectedPage = await contentItemRetrieverService.RetrieveWebPageByContentItemGuid(selectedPageGuid);
    var selectedPageWebPageGuid = selectedPage?.SystemFields.WebPageItemGUID;
    string selectedPageContentTypeName = await GetWebPageContentTypeName(selectedPageWebPageGuid);
    string selectedPagePath = selectedPage?.SystemFields.WebPageItemTreePath ?? string.Empty;

    if (string.IsNullOrEmpty(selectedPagePath))
    {
        return [];
    }

    // if no tags are specified, retrieve the Article pages in the same way as before
    if (tags.IsNullOrEmpty())
    {
        return await contentItemRetrieverService.RetrieveWebPageChildrenByPath<ArticlePage>(
            selectedPagePath,
            3);
    }
    // otherwise process the tags and retrieve the appropriate pages accordingly
    else
    {
        // code to retrieve article pages based on tags will go here
    }
}
...
```

Add the using directive for the compiler to recognize the `TagReference` class. 
C#
**ArticleListWidgetViewComponent.cs**
Copy
```
...
using CMS.ContentEngine;
...
```

Now there is one issue we need to solve:
Our widget expects **Article page** content items to construct its view model. However, the property holding our taxonomy tags is on the reusable content item linked by the page.
Because of the current limitations of the [ContentRetriever API](/documentation/developers-and-admins/api/content-item-api/content-retriever-api), we will do this in two steps:
  1. Retrieve the IDs of all reusable content items that implement **Article schema** and contain the specified tags.
  2. Retrieve **Article page** content items on the specified path that link these reusable items.


### Implement the service methods
Let’s go implement these service methods in our [ContentItemRetrieverService.cs file](https://github.com/Kentico/xperience-by-kentico-training-guides/blob/main/src/TrainingGuides.Web/Features/Shared/Services/ContentItemRetrieverService.cs) .
#### Retrieve reusable content items by schema and tags
The ContentRetriever API has a [`RetrieveContentOfReusableSchemas` extension method](/documentation/developers-and-admins/api/content-item-api/reference-content-retriever-api#retrieve-content-items-sharing-reusable-field-schema) that is exactly suited for our scenario.
We will utilize it to retrieve the reusable content items that contain:
  * Our _Article schema_ , passing in the schema codename as a parameter (`schemaName`)
  * GUIDs of specific tags, using the [WhereContainsTags](/documentation/developers-and-admins/api/content-item-api/reference-content-item-query#wherecontainstags) extension method.


The data we need to perform this query will become additional method parameters:
  * the name of the database column where we expect to find the tags (`taxonomyColumnName`)
  * the GUIDs of the required tags (`tagGuids`)
  * the content’s language (`languageName`)


Declare the method in the `IContentItemRetrieverService` interface and implement it in the `ContentItemRetrieverService` class.
C#
**IContentItemRetrieverService.cs**
Copy
```
public interface IContentItemRetrieverService
{
    ...
    Task<IEnumerable<IContentItemFieldsSource>> RetrieveContentItemsBySchemaAndTags(
        string schemaName,
        string taxonomyColumnName,
        IEnumerable<Guid> tagGuids,
        string? languageName = null);
}
```

C#
**ContentItemRetrieverService.cs**
Copy
```
...
public class ContentItemRetrieverService : IContentItemRetrieverService
{
    ...
    /// <inheritdoc />
    public async Task<IEnumerable<IContentItemFieldsSource>> RetrieveContentItemsBySchemaAndTags(
        string schemaName,
        string taxonomyColumnName,
        IEnumerable<Guid> tagGuids,
        string? languageName = null)
    {
        var parameters = new RetrieveContentOfReusableSchemasParameters
        {
            LanguageName = languageName ?? preferredLanguageRetriever.Get(),
            IsForPreview = webSiteChannelContext.IsPreview
        };

        return await contentRetriever.RetrieveContentOfReusableSchemas<IContentItemFieldsSource>(
            [schemaName],
            parameters,
            query => query.Where(where => where.WhereContainsTags(taxonomyColumnName, tagGuids)),
            RetrievalCacheSettings.CacheDisabled,
            configureModel: null);
    }
    ...
}
```

#### Retrieve Article pages by path and reference
Next, we need a method that will fetch all child Article pages on a specific path **which also link any of specified reusable content items**.
Our `ContentItemRetrieverService` already has the `RetrieveWebPageChildrenByPath` method (which our widget currently uses). 
C#
**ContentItemRetrieverService.cs - old implementation**
Copy
```
...
public class ContentItemRetrieverService : IContentItemRetrieverService
{
    ...
    public async Task<IEnumerable<T>> RetrieveWebPageChildrenByPath<T>(
        string path,
        int depth = 1,
        string? languageName = null)
        where T : IWebPageFieldsSource, new()
    {
        var parameters = new RetrievePagesParameters
        {
            LinkedItemsMaxLevel = depth,
            LanguageName = languageName ?? preferredLanguageRetriever.Get(),
            IsForPreview = webSiteChannelContext.IsPreview,
            PathMatch = PathMatch.Children(path)
        };

        return await contentRetriever.RetrievePages<T>(parameters);
    }
    ...
}
...
```

We can reuse the functionality – all we need is an additional query configuration in the ContentRetriever’s extension method parameters to pass in the references.
Add a new **private** overload of the `RetrieveWebPageChildrenByPath`, and a new nullable `additionalQueryConfiguration` parameter to the method to pass in an anonymous method.
C#
**ContentItemRetrieverService.cs**
Copy
```
...
private async Task<IEnumerable<T>> RetrieveWebPageChildrenByPath<T>(
        string path,
        int depth = 1,
        Action<RetrievePagesQueryParameters>? additionalQueryConfiguration = null,
        string? languageName = null)
        where T : IWebPageFieldsSource, new()
    { }

...
```

Extract all the code from the **public** `RetrieveWebPageChildrenByPath` method. Replace the the `RetrievePages` method call with an overload that takes `additionalQueryConfiguration` as a parameter.
C#
**ContentItemRetrieverService.cs**
Copy
```
...
public class ContentItemRetrieverService : IContentItemRetrieverService
{
    ...
    private async Task<IEnumerable<T>> RetrieveWebPageChildrenByPath<T>(
        string path,
        int depth = 1,
        Action<RetrievePagesQueryParameters>? additionalQueryConfiguration = null,
        string? languageName = null)
        where T : IWebPageFieldsSource, new()
    {
        var parameters = new RetrievePagesParameters
        {
            LinkedItemsMaxLevel = depth,
            LanguageName = languageName ?? preferredLanguageRetriever.Get(),
            IsForPreview = webSiteChannelContext.IsPreview,
            PathMatch = PathMatch.Children(path)
        };

        return await contentRetriever.RetrievePages<T>(
            parameters,
            additionalQueryConfiguration: additionalQueryConfiguration,
            cacheSettings: RetrievalCacheSettings.CacheDisabled);
    }
    ...
}
```

Now change the public `RetrieveWebPageChildrenByPath` method code to simply call this new private method:
C#
**ContentItemRetrieverService.cs**
Copy
```
...
public class ContentItemRetrieverService : IContentItemRetrieverService
{
    ...
    public async Task<IEnumerable<T>> RetrieveWebPageChildrenByPath<T>(
        string path,
        int depth = 1,
        string? languageName = null)
        where T : IWebPageFieldsSource, new()
        => await RetrieveWebPageChildrenByPath<T>(
            path,
            depth,
            null,
            languageName);
    ...
}
...
 
 
 
 
 


```

Last but not least, implement a new public method that retrieves pages from a given path which link to the specified content items. This method needs two new parameters beyond the existing path and depth: the **code name of the reference field** and the **IEnumerable of content item IDs**.
Call our new **private** `RetrieveWebPageChildrenByPath` method and pass in custom query configuration - an anonymous function calling the [Linking](/documentation/developers-and-admins/api/content-item-api/reference-content-item-query#linking) extension method.
C#
**ContentItemRetrieverService.cs**
Copy
```
public class ContentItemRetrieverService : IContentItemRetrieverService
{
    ...
    public async Task<IEnumerable<T>> RetrieveWebPageChildrenByPathAndReference<T>(
        string parentPagePath,
        string referenceFieldName,
        IEnumerable<int> referenceIds,
        int depth = 1,
        string? languageName = null)
        where T : IWebPageFieldsSource, new()
     => await RetrieveWebPageChildrenByPath<T>(
            path: parentPagePath,
            depth: depth,
            additionalQueryConfiguration: config => config.Linking(referenceFieldName, referenceIds),
            languageName: languageName);
    ...
}
...
```

Remember to also declare the new method in the `IContentItemRetrieverService` interface.
C#
**IContentItemRetrieverService.cs**
Copy
```
...
public interface IContentItemRetrieverService
{
    ...
    Task<IEnumerable<T>> RetrieveWebPageChildrenByPathAndReference<T>(
        string parentPagePath,
        string referenceFieldName,
        IEnumerable<int> referenceIds,
        int depth = 1,
        string? languageName = null)
        where T : IWebPageFieldsSource, new();
    ...
}
...
```

See the full [ContentItemRetrieverService file](https://github.com/Kentico/xperience-by-kentico-training-guides/blob/finished/src/TrainingGuides.Web/Features/Shared/Services/ContentItemRetrieverService.cs) in the finished branch of our Training guides repo.
### Put it all together
Our backend work is done. Let’s utilize the new methods in the _ArticleListWidgetViewComponent_.
Find the empty `else` statement inside the `RetrieveArticlePages` method. Add logic for the case when the editor **did** specify the tags to filter by:
C#
**ArticleListWidgetViewComponent.cs**
Copy
```
...
private async Task<IEnumerable<ArticlePage>> RetrieveArticlePages(ContentItemReference parentPageSelection, IEnumerable<TagReference> tags, string securedItemsDisplayMode)
{
    ...
    else
    {
        // first extract the list of tag GUIDs
        var tagGuids = tags.Select(tag => tag.Identifier).ToList();

        // retrieve the IDs of Article schema reusable content items
        // to specify the schema, use the REUSABLE_FIELD_SCHEMA_NAME constant from the generated IArticleSchema interface
        // pass in the name of the ArticleSchemaCategory field
        var taggedArticleIds = (
            await contentItemRetrieverService.RetrieveContentItemsBySchemaAndTags(
                IArticleSchema.REUSABLE_FIELD_SCHEMA_NAME,
                nameof(IArticleSchema.ArticleSchemaCategory),
                tagGuids)
            ).Select(article => article.SystemFields.ContentItemID);

        // retrieve and return Article page content items on specified path that reference the reusable content items
        // pass in the name of the ArticlePageArticleContent field, which we use to link reusable article item to Article page
        return await contentItemRetrieverService.RetrieveWebPageChildrenByPathAndReference<ArticlePage>(
            selectedPagePath,
            nameof(ArticlePage.ArticlePageArticleContent),
            taggedArticleIds,
            3);
    }
}
...
```

Finally, call the updated `RetrieveArticlePages` method from `InvokeAsync` to retrieve the article pages. 
C#
**ArticleListWidgetViewComponent.cs**
Copy
```
...
public async Task<ViewViewComponentResult> InvokeAsync(ArticleListWidgetProperties properties)
{
    var model = new ArticleListWidgetViewModel();

    if (!properties.ContentTreeSection.IsNullOrEmpty())
    {
        // call the updated method passing in the Tags property
        var articlePages = await RetrieveArticlePages(properties.ContentTreeSection.First(), properties.Tags);

        model.Articles = (properties.OrderBy.Equals("OldestFirst", StringComparison.OrdinalIgnoreCase)
            ? (await GetArticlePageViewModels(articlePages)).OrderBy(article => article.CreatedOn)
            : (await GetArticlePageViewModels(articlePages)).OrderByDescending(article => article.CreatedOn))
            .Take(properties.TopN)
            .ToList();

        model.CtaText = properties.CtaText;
    }

    return View("~/Features/Articles/Widgets/ArticleList/ArticleListWidget.cshtml", model);
}
...
```

## See the results
Rebuild and run your project. Navigate to a page (e.g., _Home_) where you added the Article list widget. Configure the widget to show articles with different tags, and see the filtered results when you **Apply** the changes.
Your browser does not support the video tag. 
Find the complete and working implementation of this example in the [finished branch of the Training guides repository](https://github.com/Kentico/xperience-by-kentico-training-guides/tree/finished) . If you are experiencing any issues or errors, double-check that your references, methods, parameters and `using` directives match the ones in the repo.
## Additional materials
So far in this series, we have explored reusable field schemas and taxonomies in the context of content model evolution using the **expand and contract approach**.
We have talked mostly about the _‘expand’_ part, adjusting your application to work with both the new and old content types. The next step is to _‘contract’_ your solution, converting deprecated items to the new content types so we can delete the old content type. The [next guide in this series](/guides/development/advanced-content/convert-content-to-reusable-schemas) covers this scenario.
If you are interested in the expand and contract approach, we recommend this [Community portal blog on the topic](https://community.kentico.com/blog/safely-evolving-a-content-model-with-expand-and-contract).
To learn more about [taxonomies](/documentation/developers-and-admins/configuration/taxonomies) from the content modeling perspective, check out our [Model a reusable article](/guides/architecture/content-modeling/model-reusable-content/model-a-reusable-article) guide. If you’d like to get a better perspective on how business users and editors work with taxonomies in Xperience, we recommend you take a look at our [Work with taxonomies](/guides/digital-marketing/work-with-taxonomies) business guides.
![]()
[]()[]()
