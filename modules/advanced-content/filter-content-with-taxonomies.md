# Filter content with taxonomies
  * [ Copy page link ](modules/advanced-content/filter-content-with-taxonomies#) | [Get HelpService ID](modules/advanced-content/filter-content-with-taxonomies#)
Core MVC 5


[✖](modules/advanced-content/filter-content-with-taxonomies# "Close page link panel") [Copy to clipboard](modules/advanced-content/filter-content-with-taxonomies#)
Now, we can implement filtering based on the _Article category_ taxonomy.
### Add new widget property
First, let’s add a [tag selector](documentation/developers-and-admins/customization/extend-the-administration-interface/ui-form-components/reference-admin-ui-form-components#tag-selector) to the widget UI to so that editors can select the desired tags.
Find the definition of the [Article list widget properties](https://github.com/Kentico/xperience-by-kentico-training-guides/blob/main/src/TrainingGuides.Web/Features/Articles/Widgets/ArticleList/ArticleListWidgetProperties.cs) in the Training guides repository.
Add a new `IEnumerable<TagReference>` property called `Tags` between the existing `ContentTreeSection` and `TopN`. Decorate the property with `TagSelectorComponent` attribute.
Specify allowed taxonomy passing our taxonomy code name, `ArticleCategory`, as the first parameter.
To refresh your memory on defining widget properties revisit our [Build a simple call-to-action widget](guides/development/page-builder/build-simple-cta-widget#define-widget-properties) guide.
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
Because of the current limitations of the [ContentRetriever API](documentation/developers-and-admins/api/content-item-api/content-retriever-api), we will do this in two steps:
  1. Retrieve the IDs of all reusable content items that implement **Article schema** and contain the specified tags.
  2. Retrieve **Article page** content items on the specified path that link these reusable items.


### Implement the service methods
Let’s go implement these service methods in our [ContentItemRetrieverService.cs file](https://github.com/Kentico/xperience-by-kentico-training-guides/blob/main/src/TrainingGuides.Web/Features/Shared/Services/ContentItemRetrieverService.cs) .
#### Retrieve reusable content items by schema and tags
The ContentRetriever API has a [`RetrieveContentOfReusableSchemas` extension method](documentation/developers-and-admins/api/content-item-api/reference-content-retriever-api#retrieve-content-items-sharing-reusable-field-schema) that is exactly suited for our scenario.
We will utilize it to retrieve the reusable content items that contain:
  * Our _Article schema_ , passing in the schema codename as a parameter (`schemaName`)
  * GUIDs of specific tags, using the [WhereContainsTags](documentation/developers-and-admins/api/content-item-api/reference-content-item-query#wherecontainstags) extension method.


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
Call our new **private** `RetrieveWebPageChildrenByPath` method and pass in custom query configuration - an anonymous function calling the [Linking](documentation/developers-and-admins/api/content-item-api/reference-content-item-query#linking) extension method.
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
[ Previous page ](modules/advanced-content/think-about-filtering-with-taxonomies)
5 of 11
[ Mark complete and continue ](modules/advanced-content/prepare-to-convert-items)
  * [Community Questions & Answers](https://community.kentico.com/q-and-a)
  * [Contact support](https://community.kentico.com/support)


### Cookie consent
We use necessary [cookies](https://www.kentico.com/cookies-policy) to run our website and improve your experience while browsing. Additional cookies are only used with your consent. You may revoke your consent on the [Cookies Policy](https://www.kentico.com/cookies-policy) page or in your browser at any time. 
ACCEPT ALL  [Configure](https://www.kentico.com/cookies-policy)
USE ONLY NECESSARY 
![](https://docs.kentico.com/modules/advanced-content/filter-content-with-taxonomies)
[](https://docs.kentico.com/modules/advanced-content/filter-content-with-taxonomies)[](https://docs.kentico.com/modules/advanced-content/filter-content-with-taxonomies)