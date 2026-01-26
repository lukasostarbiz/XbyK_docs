---
source: https://docs.kentico.com/guides/development/advanced-content/work-with-reusable-field-schemas
scrape_date: 2026-01-26
---

  * [Home](/guides)
  * [Development](/guides/development)
  * [Advanced content](/guides/development/advanced-content)
  * Work with reusable field schemas 


# Work with reusable field schemas
[Reusable field schemas](/documentation/developers-and-admins/development/content-types/reusable-field-schemas) are collections of fields that multiple content types can share. When a schema is updated with a new field, for example, any content types that use that snippet will also have the new field.
Beyond this, you can restrict references between content items based on reusable field schemas, or even filter [content item queries](/documentation/developers-and-admins/api/content-item-api/reference-content-item-query), retrieving items of multiple types that share a schema.
Let’s explore this functionality through the lens of a common scenario: content model evolution.
## Before you start
This guide requires the following:
  * Familiarity with [C#](https://learn.microsoft.com/en-us/dotnet/csharp/), [.NET Core](https://learn.microsoft.com/en-us/dotnet/), [Dependency injection](https://learn.microsoft.com/en-us/dotnet/core/extensions/dependency-injection), and the [MVC pattern](https://learn.microsoft.com/en-us/aspnet/core/mvc/overview).
  * A running instance of Xperience by Kentico, preferably [30.11.1](/documentation/changelog) or higher. 
Some features covered in the training guides may not work in older versions. 


**Code samples**
You can find a project with completed, working versions of code samples from this guide and others in the [finished branch](https://github.com/Kentico/xperience-by-kentico-training-guides/tree/finished) of the _Training guides_ repository.
The [main branch](https://github.com/Kentico/xperience-by-kentico-training-guides) of the repository provides a starting point to code along with the guides.
The code samples in this guide are for [.NET 8](https://learn.microsoft.com/en-us/dotnet/core/whats-new/dotnet-8/overview) only.
They come from a project that uses [implicit using directives](https://learn.microsoft.com/en-us/dotnet/core/project-sdk/overview#implicit-using-directives). You may need to add additional `using` directives to your code if your project does not use this feature.
## Consider the scenario
As businesses mature over time, they may need more advanced content models to meet their needs. As a developer on such projects, you’ll have to adapt the code to handle these changes.
In this example, the project is moving from a flat _Article_ content type to a Reusable field schema called _Article schema_ , which is shared between two content types: _General article_ and _Interview_.
[![diagram of article content types](/docsassets/guides/work-with-reusable-field-schemas/articleschema.png)](/docsassets/guides/work-with-reusable-field-schemas/articleschema.png)
To save you time, the main branch already includes the two new schema-based article types, alongside the old article type. You can see them in the **Content types** application. [Code files](https://github.com/Kentico/xperience-by-kentico-training-guides/tree/main/src/TrainingGuides.Entities) for the new types and the schemas have already been [generated](/documentation/developers-and-admins/api/generate-code-files-for-system-objects).
However, the code of the site does not yet handle the data from these new types. _Interviews_ and _General articles_ do not display properly on article pages, while the old type does.
Let’s dive into the code, and fix our article service to handle this new content type. Then, at the end, we can add personalization to further enhance the widgets that use it.
## Examine the current implementation
In the content tree of the _Training guides pages_ channel, you’ll find eight children under _News and articles_ :
Four pages that display articles of the deprecated type:
  * About reptiles
  * About birds
  * About dogs
  * About cats


And four pages referencing schema-based articles that do not display:
  * About frogs
  * About conifers
  * Bean plant cultivation: an interview with Jack Trott
  * Sheep care: an interview with Little Bo-Peep


[![Deprecated article displaying its content](/docsassets/guides/work-with-reusable-field-schemas/article-old-display.jpg)](/docsassets/guides/work-with-reusable-field-schemas/article-old-display.jpg)
[![Schema-based article with no display](/docsassets/guides/work-with-reusable-field-schemas/article-new-no-display.png)](/docsassets/guides/work-with-reusable-field-schemas/article-new-no-display.png)
You’ll also notice that these new articles do not display if you use the _Article list_ or _Featured article_ widgets on a page with page builder.
Let’s take a look under the hood to see how article pages are rendered.
### Check the controller
Visit _ArticlePageController.cs_ in the _TrainingGuides.Web/Features/Articles_ folder.
You can see that the controller retrieves the current `ArticlePage` using the `RetrieveCurrentPage` method. Under the hood, this method gets the page based on the current _web page data context_. To put this into perspective, open the **Content types** app in the Xperience administration. You’ll notice that the new content types, _Article (general)_ and _Article (interview)_ are both reusable types.
[![Reusable content types](/docsassets/guides/work-with-reusable-field-schemas/content-types-reusable.png)](/docsassets/guides/work-with-reusable-field-schemas/content-types-reusable.png)
The _Article page_ content type contains two fields for selecting content items:
  * `ArticlePageContent`, labelled _Article page content (DEPRECATED, DO NOT USE)_ in the admin UI, for selecting items of the deprecated _Article_ content type.
  * `ArticlePageArticleContent`, labelled _Article page content_ in the admin UI, for selecting items that use the new article schema.


Since the _Article page controller_ is working with the _Article page_ content type, rather than the reusable types it references, you can leave it alone.
However, notice that it uses an `IArticlePageService` instance to extract article data into an `ArticlePageViewModel` object. Let’s inspect this method, `ArticlePageService.GetArticlePageViewModel`, in the next section.
### Dig into the service
In the _ArticlePageService.cs_ (from the _Services_ folder that neighbors the controller), you’ll see code that converts the linked `Article` item from an `ArticlePage` object and uses it to construct an `ArticlePageViewModel`.
If we make sure this method accounts for the schema-based articles as well, the template and widgets will be able to display them alongside the deprecated articles.
C#
**ArticlePageService.cs**
Copy
```
...
/// Creates a new instance of <see cref="ArticlePageViewModel"/>, setting the properties using ArticlePage given as a parameter.
/// </summary>
/// <param name="articlePage">Corresponding Article page object.</param>
/// <returns>New instance of ArticlePageViewModel.</returns>
public ArticlePageViewModel GetArticlePageViewModel(ArticlePage? articlePage)
{
    if (articlePage == null)
    {
        return new ArticlePageViewModel();
    }

    var article = articlePage.ArticlePageContent.FirstOrDefault();
    var articleTeaserImage = article?.ArticleTeaser.FirstOrDefault();

    string articleUrl = articlePage.GetUrl().RelativePath;

    return new ArticlePageViewModel
    {
        Title = article?.ArticleTitle ?? string.Empty,
        SummaryHtml = new HtmlString(article?.ArticleSummary),
        TextHtml = new HtmlString(article?.ArticleText),
        CreatedOn = articlePage.ArticlePagePublishDate,
        TeaserImage = AssetViewModel.GetViewModel(articleTeaserImage),
        Url = articleUrl
    };
}
...
```

The code samples here rely on a [decorated version](https://github.com/Kentico/xperience-by-kentico-training-guides/blob/main/src/TrainingGuides.Web/Features/Shared/Services/TrainingGuidesWebPageUrlRetriever.cs) of `IWebPageUrlRetriever` that includes exception handling.
If you do not plan to use a similar customization, make sure to handle errors that the `Retrieve` method may throw when it cannot find a page.
In it’s current state, the `GetArticlePageViewModel` method does the following:
  1. Gets an `Article` from the provided `ArticlePage`’s `ArticlePageContent` property.
  2. Extracts the first `Asset` object from its `ArticleTeaser` property.
  3. Retrieves the URL of the provided `ArticlePage`
  4. Assembles and returns an `ArticlePageViewModel` from all of these pieces.


Our goal is to have the project display new articles alongside old ones, prioritizing the new ones, so let’s try to get an `IArticleSchema` from the provided `ArticlePage` and fall back to the `Article` only if none is found.
## Update the code
Since we’ll need the URL of the page either way, move the line of code that retrieves it to the beginning.
Then, if the page has an `IArticleSchema` in its `ArticlePageArticleContent` collection, assemble and return an `ArticlePageViewModel` using values from the `IArticleSchema` instead of the `Article`.
Outside of the `if` statement, leave the previous steps to return an `ArticlePageViewModel` based on the `Article` type.
C#
**ArticlePageService.cs**
Copy
```
...
/// <summary>
/// Creates a new instance of <see cref="ArticlePageViewModel"/>, setting the properties using ArticlePage given as a parameter.
/// </summary>
/// <param name="articlePage">Corresponding Article page object.</param>
/// <returns>New instance of ArticlePageViewModel.</returns>
public ArticlePageViewModel GetArticlePageViewModel(ArticlePage? articlePage)
{
    if (articlePage == null)
    {
        return new ArticlePageViewModel();
    }

    string articleUrl = articlePage.GetUrl().RelativePath;
    var articleSchema = articlePage.ArticlePageArticleContent.FirstOrDefault();

    if (articleSchema != null)
    {
        var articleSchemaTeaserImage = articleSchema.ArticleSchemaTeaser.FirstOrDefault();

        return new ArticlePageViewModel
        {
            Title = articleSchema.ArticleSchemaTitle,
            SummaryHtml = new HtmlString(articleSchema?.ArticleSchemaSummary),
            TextHtml = new HtmlString(articleSchema?.ArticleSchemaText),
            CreatedOn = articlePage.ArticlePagePublishDate,
            TeaserImage = AssetViewModel.GetViewModel(articleSchemaTeaserImage),
            Url = articleUrl
        };
    }

    var article = articlePage.ArticlePageContent.FirstOrDefault();
    var articleTeaserImage = article?.ArticleTeaser.FirstOrDefault();

    return new ArticlePageViewModel
    {
        Title = article?.ArticleTitle ?? string.Empty,
        SummaryHtml = new HtmlString(article?.ArticleSummary),
        TextHtml = new HtmlString(article?.ArticleText),
        CreatedOn = articlePage.ArticlePagePublishDate,
        TeaserImage = AssetViewModel.GetViewModel(articleTeaserImage),
        Url = articleUrl
    };
}
...
 
 
 
 
 
 
 
 
 
 
 
 
 
 


```

## See the results
Run the project and sign in to the Xperience administration. Now if you visit any of the new articles listed earlier, you should see the article content.
[![Schema-based article displaying its content](/docsassets/guides/work-with-reusable-field-schemas/article-new-display.jpg)](/docsassets/guides/work-with-reusable-field-schemas/article-new-display.jpg)
Go to the **Page Builder** tab of the _Home_ page, and click **Edit page**
Add an instance of the _Featured article_ widget. Configure the widget to reference one of the new articles.
Then, add an instance of the Article List widget. Set its content tree section to the _News and articles_ page, and save your configuration.
Now you should see both widgets displaying new article content.
[![Widgets displaying the content of schema-based articles](/docsassets/guides/work-with-reusable-field-schemas/widgets-new-display.png)](/docsassets/guides/work-with-reusable-field-schemas/widgets-new-display.png)
## Add content personalization
After our changes the widgets are working again. However, we have the opportunity to make them even better.
If we implement [content personalization](/documentation/developers-and-admins/digital-marketing-setup/content-personalization), we can allow editors to show different featured articles to visitors who are members of certain contact groups, based on what is more relevant to them.
To give you an idea of what we are implementing, here is what the process of personalizing widgets looks like for editors:
Navigate to the **Contact groups** application in the Xperience admin, and define a new contact group with placeholder values for its name and description.
Now, [define a new personalization condition type](/documentation/developers-and-admins/digital-marketing-setup/content-personalization/develop-personalization-condition-types) in the _TrainingGuides.Web/Features/Personalization_ folder that allows editors to select a contact group and evaluates whether the current contact is in that group.
C#
**IsInContactGroupConditionType.cs**
Copy
```
using CMS.ContactManagement;
using CMS.DataEngine;
using TrainingGuides.Web.Features.Personalization;
using Kentico.PageBuilder.Web.Mvc.Personalization;
using Kentico.Xperience.Admin.Base.FormAnnotations;
using Kentico.Xperience.Admin.Base.Forms;

[assembly: RegisterPersonalizationConditionType(
    "TrainingGuides.Web.Features.Personalization",
    typeof(IsInContactGroupConditionType),
    "Is in contact group",
    Description = "Evaluates if the current contact is in one of the contact groups.", IconClass = "icon-app-contact-groups", Hint = "Display to visitors who match at least one of the selected contact groups:")]

namespace TrainingGuides.Web.Features.Personalization;

/// <summary>
/// Personalization condition type based on contact group.
/// </summary>
public class IsInContactGroupConditionType : ConditionType
{
    /// <summary>
    /// Selected contact group code names.
    /// </summary>
    [ObjectSelectorComponent(PredefinedObjectType.CONTACTGROUP,
    Label = "Contact groups",
    Order = 0,
    MaximumItems = 0)]
    public IEnumerable<ObjectRelatedItem> SelectedContactGroups { get; set; } = [];

    /// <summary>
    /// Evaluate condition type.
    /// </summary>
    /// <returns>Returns <c>true</c> if implemented condition is met.</returns>
    public override bool Evaluate()
    {
        var contact = ContactManagementContext.GetCurrentContact();

        if (contact == null)
        {
            return false;
        }

        if (SelectedContactGroups == null || !SelectedContactGroups.Any())
        {
            return contact.ContactGroups.Count == 0;
        }

        return contact.IsInAnyContactGroup(SelectedContactGroups.Select(c => c.ObjectCodeName).ToArray());
    }
}
```

Rebuild the solution and access the page builder tab. Now you can define a different featured article that displays for members of your contact group.
Your browser does not support the video tag. 
## What’s next?
The [next guide](/guides/development/advanced-content/filter-content-based-on-taxonomies) in this series will cover the process of querying content based on reusable field schemas and taxonomies in order to filter the article list widget by taxonomy term. After that, the [following guide](/guides/development/advanced-content/filter-content-based-on-taxonomies) goes over the process of converting articles from our deprecated content type to the new schema, so you can delete the old content type.
![]()
[]()[]()
