---
source: https://docs.kentico.com/modules/members/expand-restricted-content-and-sign-in-functionality
scrape_date: 2026-01-26
---

Module: Members
10 of 12 Pages
# Expand restricted content and sign-in functionality
Let’s expand the _Sign in_ widget view component to redirect a member back to the provided _return URL_ after successful authentication.
It needs to find the `returnUrl` value in the query string of the request, so we should start with a new method in the `HttpRequestService`.
C#
**~/Features/Shared/Services/IHttpRequestService.cs**
Copy
```
/// <summary>
/// Retrieves the value of the specified query string parameter
/// </summary>
/// <param name="parameter">The name of the query string parameter to retrieve</param>
/// <returns>The value of the specified query string parameter</returns>
string GetQueryStringValue(string parameter);
```

C#
**~/Features/Shared/Services/HttpRequestService.cs**
Copy
```
/// <inheritdoc/>
public string GetQueryStringValue(string parameter) => httpContextAccessor.HttpContext?.Request.Query[parameter].ToString() ?? string.Empty;
```

Then, expand the `BuildWidgetViewModel` method of the sign-in widget view component to check the query string for a _returnUrl_ value, falling back to the previous logic if it finds none.
C#
**SignInWidgetViewComponent.cs**
Copy
```
using TrainingGuides.Web.Features.Shared.Helpers;
...
public class SignInWidgetViewComponent : ViewComponent
{
...
    public async Task<SignInWidgetViewModel> BuildWidgetViewModel(SignInWidgetProperties properties) => new SignInWidgetViewModel
    {
        ActionUrl = GetActionUrl(),
        DefaultRedirectPageGuid = properties.DefaultRedirectPage.FirstOrDefault()?.Identifier ?? Guid.Empty,
        DisplayForm = !await membershipService.IsMemberAuthenticated(),
        FormTitle = properties.FormTitle,
        SubmitButtonText = properties.SubmitButtonText,
        UserNameOrEmailLabel = properties.UserNameLabel,
        PasswordLabel = properties.PasswordLabel,
        StaySignedInLabel = properties.StaySignedInLabel
    };

    private string GetActionUrl()
    {
        // New code: retrieve and use the return URL if it exists
        string? returnUrl = GetReturnUrlFromQueryString();
        QueryString? queryString = string.IsNullOrWhiteSpace(returnUrl) ? null : QueryString.Create(ApplicationConstants.RETURN_URL_PARAMETER, returnUrl);

        return httpRequestService.GetAbsoluteUrlForPath(ApplicationConstants.AUTHENTICATE_ACTION_PATH, true, queryString);
    }

    // New code: use the new HttpRequestService method to retrieve the query string
    private string? GetReturnUrlFromQueryString()
    {
        string returnUrl = httpRequestService.GetQueryStringValue(ApplicationConstants.RETURN_URL_PARAMETER);

        // If there is no return URL or it is not a relative URL, return null
        if (string.IsNullOrWhiteSpace(returnUrl) || !returnUrl.StartsWith("/"))
        {
            return null;
        }

        return returnUrl;
    }
}
```

See [this section of the language selector guide](/guides/development/multilingual/implement-language-selector-for-your-website#handle-urls-and-routing) or [the Training guides repository](https://github.com/Kentico/xperience-by-kentico-training-guides/blob/finished/src/TrainingGuides.Web/Features/Shared/Services/HttpRequestService.cs) for details about how to implement the `GetBaseUrlWithLanguage` method.
If you ensure the return URL only works with relative paths, you can prevent attackers from exploiting the query string to redirect members to phishing sites after successful authentication on your site.
## See the redirect in action
Now the sign in form should correctly redirect you to the _return URL_ after you authenticate.
Your browser does not support the video tag. 
However, you may notice that even if you followed along [earlier](/modules/members/set-up-restricted-pages) and set a reusable item to require authentication, it is still visible to signed out users on the page.
[![Screenshot of the ‘About frogs’ article displaying on the site even though the underlying reusable item is secured](/docsassets/guides/require-authentication-for-certain-content/AboutFrogsUnsecured.png)](/docsassets/guides/require-authentication-for-certain-content/AboutFrogsUnsecured.png)
This happens because the web page that references this reusable article is not secured. The _content tree-based router_ only knows to check security of the pages it is retrieving, and not any reusable content they might reference.
## Handle secured reusable content
Ideally, content editors should mark all pages that reference secured items to require authentication, but we shouldn’t assume that they will never make mistakes.
When pages display reusable content, you need one of the following approaches to for security:
  1. Set the query that retrieves the content to [filter out secured items](/documentation/developers-and-admins/development/content-retrieval/retrieve-page-content#page-security-configuration).
  2. Check the `ContentItemIsSecured` value of the linked items you want to secure, and react accordingly. For example, you can redirect or display an error message.


In this case, let’s try the latter.
Go to the _~/Features/Articles/Services_ folder and add a new method to the article page service. It should check if the primary reusable content item referenced by an article page is secured.
C#
**IArticlePageService.cs**
Copy
```
...
/// <summary>
/// Determines whether the reusable article item referenced by the article page is secured.
/// </summary>
/// <param name="articlePage">The article page.</param>
/// <returns>True if the reusable item that the page references is secured.</returns>
bool IsReusableArticleSecured(ArticlePage articlePage);
...
```

C#
**ArticlePageService.cs**
Copy
```
...
/// <inheritdoc/>
public bool IsReusableArticleSecured(ArticlePage articlePage)
{
    var oldArticle = articlePage.ArticlePageContent.FirstOrDefault();
    var newArticle = (IContentItemFieldsSource?)articlePage.ArticlePageArticleContent.FirstOrDefault();

    return (oldArticle?.SystemFields.ContentItemIsSecured ?? false)
        || (newArticle?.SystemFields.ContentItemIsSecured ?? false);
}
...
```

Depending on whether you’ve followed along with the [Advanced content](/guides/development/advanced-content) series, the `ArticlePageArticleContent` field may or may not exist in the `ArticlePage` type.
Then, use your new method in the article page controller, returning the _403 Forbidden_ status code when the current visitor is unauthenticated and the item is secured.
C#
**ArticlePageController.cs**
Copy
```
using TrainingGuides.Web.Features.Membership.Services;
...
public class ArticlePageController : Controller
{
    // Use dependency injection to populate these

    private readonly IContentItemRetrieverService contentItemRetrieverService;
    private readonly IArticlePageService articlePageService;
    private readonly IMembershipService membershipService;

    ...

    public async Task<IActionResult> Index()
    {
        var articlePage = await contentItemRetrieverService.RetrieveCurrentPage<ArticlePage>(2);

        if (articlePage is not null
            && articlePageService.IsReusableArticleSecured(articlePage)
            && !await membershipService.IsMemberAuthenticated())
        {
            return Forbid();
        }

        var model = articlePageService.GetArticlePageViewModel(articlePage);
        return new TemplateResult(model);
    }
}
 



 
 
 
 
 
 


```

## See the results
Now, Identity should handle the 403 status the same way it does for pages if you try to access a secured article.
Your browser does not support the video tag. 
## What’s next?
The [finished branch](https://github.com/Kentico/xperience-by-kentico-training-guides/tree/finished) of the _Training guides_ repository contains a demonstration of two approaches to handle secured content in listings:
  * Display a message explaining that the item is locked and linking to the sign-in page.
  * Hide secured items completely from the listing by filtering them out of the query.


If you’re looking to expand upon the lesson we’ve just covered, try to implement this functionality in your own project.
Specifically, check out:
  * The [_Article list_ widget](https://github.com/Kentico/xperience-by-kentico-training-guides/blob/finished/src/TrainingGuides.Web/Features/Articles/Widgets/ArticleList).
  * The [Article page service](https://github.com/Kentico/xperience-by-kentico-training-guides/blob/finished/src/TrainingGuides.Web/Features/Articles/Services/ArticlePageService.cs).
  * The [Content item retriever service](https://github.com/Kentico/xperience-by-kentico-training-guides/blob/finished/src/TrainingGuides.Web/Features/Shared/Services/ContentItemRetrieverService.cs).


The branch also contains other useful features for your reference, like [password reset functionality](https://github.com/Kentico/xperience-by-kentico-training-guides/blob/finished/src/TrainingGuides.Web/Features/Membership/Widgets/ResetPassword) and a [dynamic widget](https://github.com/Kentico/xperience-by-kentico-training-guides/blob/finished/src/TrainingGuides.Web/Features/Membership/Widgets/LinkOrSignOut) that shows a sign-out button or a link depending on whether or not the current visitor is authenticated.
[ Previous page ](/modules/members/set-up-restricted-pages)
10 of 12
[ Mark complete and continue ](/modules/members/apply-roles-to-your-members-community-package)
![]()
[]()[]()
