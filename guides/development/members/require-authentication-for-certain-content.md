# Require authentication for certain content
  * How-to| [ Copy page link ](guides/development/members/require-authentication-for-certain-content#) | [Get HelpService ID](guides/development/members/require-authentication-for-certain-content#)
Core MVC 5


[✖](guides/development/members/require-authentication-for-certain-content# "Close page link panel") [Copy to clipboard](guides/development/members/require-authentication-for-certain-content#)
When a business decides to include members in their project, they typically have some kind of exclusive content that only members should be able to see.
As a result, developers must ensure that only signed-in members can see certain content.
Luckily, Xperience and .NET Identity both include functionality to facilitate this process.
## Before you start
This guide requires the following:
  * Familiarity with [C#](https://learn.microsoft.com/en-us/dotnet/csharp/), [.NET Core](https://learn.microsoft.com/en-us/dotnet/), [.NET Identity](https://learn.microsoft.com/en-us/aspnet/core/security/authentication/identity), [Dependency injection](https://learn.microsoft.com/en-us/dotnet/core/extensions/dependency-injection), and the [MVC pattern](https://learn.microsoft.com/en-us/aspnet/core/mvc/overview).
  * A running instance of Xperience by Kentico, preferably [30.11.1](documentation/changelog) or higher. 
Some features covered in the training guides may not work in older versions. 


**Code samples**
The code samples in this guide rely on code from previous guides in the [Members series](guides/development/members).
You can find a project with completed, working versions of code samples from this guide and others in the [finished branch](https://github.com/Kentico/xperience-by-kentico-training-guides/tree/finished) of the _Training guides_ repository.
The [main branch](https://github.com/Kentico/xperience-by-kentico-training-guides) of the repository provides a starting point to code along with the guides.
The code samples in this guide are for [.NET 8](https://learn.microsoft.com/en-us/dotnet/core/whats-new/dotnet-8/overview) only.
They come from a project that uses [implicit using directives](https://learn.microsoft.com/en-us/dotnet/core/project-sdk/overview#implicit-using-directives). You may need to add additional `using` directives to your code if your project does not use this feature.
## Mark content to require authentication in the Xperience administration
In the admin UI of Xperience by Kentico, you can find configurable properties to specify whether a given content item is secured.
### Restrict a website page
In the Xperience admin UI, open a _website channel_ where you want to secure a page.
Then, select the page you want to secure in the content tree, and switch to the **Properties** tab.
Expand the **Membership** section and tick the box to require authentication, before clicking to publish the change.
For this example, choose one of the article pages under the **News and articles** page of the **Training guides pages** channel.
Depending on whether you’ve completed the [advanced content guides](guides/development/advanced-content), some articles might not work. Make sure you choose one that renders properly.
[![Screenshot of the membership properties of a page](docsassets/guides/require-authentication-for-certain-content/WebPageRequireAuthentication.png)](https://docs.kentico.com/docsassets/guides/require-authentication-for-certain-content/WebPageRequireAuthentication.png)
### Restrict a reusable item in the Content hub
In the **Content hub** , edit the item you want to secure.
Select the **Properties** tab and tick the box to require authentication under the **Security** heading.
For this example, choose one of the reusable articles referenced by a page under the **News and articles** page of the **Training guides pages** channel.
Make sure it’s from a page that renders properly, and is **NOT** associated with the page you chose in the previous section. **DO NOT** secure the page that references this reusable item, only secure the reusable item itself.
[![Screenshot of the security properties of a reusable content item](docsassets/guides/require-authentication-for-certain-content/ReusableItemRequireAuthentication.png)](https://docs.kentico.com/docsassets/guides/require-authentication-for-certain-content/ReusableItemRequireAuthentication.png)
## Handle the 403 HTTP status code
The _403 Forbidden_ error code is used in web development to indicate that someone does not have access to a resource.
Both Xperience and .NET Identity follow this convention in ways that are relevant to our scenario:
  * Xperience’s [Content tree-based router](documentation/developers-and-admins/development/routing/content-tree-based-routing) returns this status code when a secured page is requested by an unauthenticated visitor.
  * .NET Identity offers [configuration](https://learn.microsoft.com/en-us/aspnet/core/security/authentication/identity-configuration?view=aspnetcore-9.0#cookie-settings) to redirect requests that return this status code to a certain path, such as a sign-in page.


### Configure Identity
If you examine the Identity configuration options mentioned above, you’ll notice that the path must be set on startup, and therefore does not have the context of the request for which access was denied. There is no way to determine, for example, which language variant of the sign-in page the application should redirect to.
Therefore, let’s redirect visitors to a handler that can dynamically figure out which language version they should see.
Start by creating three constants:
  * One should hold the path to a controller action that will serve as this handler.
  * Another should determine the name of the query string parameter to which Identity assigns the _return URL_ (the path that initially returned the _403 Forbidden_ status).
  * The final constant should hold a name for the authentication cookie.


C#
**~/Features/Shared/Helpers/ApplicationConstants.cs**
Copy
```
...
public const string ACCESS_DENIED_ACTION_PATH = "/Authentication/AccessDenied";
public const string RETURN_URL_PARAMETER = "returnUrl";
...
```

C#
**~/Features/DataProtection/Shared.CookieNames.cs**
Copy
```
...
// Essential cookies
public const string COOKIE_AUTHENTICATION = "trainingguides.authentication";
...
```

Then, use these constants to set the corresponding `CookieAuthenticationOptions` properties in the `ConfigureApplicationCookie` call during startup.
C#
**~/Program.cs**
Copy
```
...
builder.Services.ConfigureApplicationCookie(options =>
{
    options.AccessDeniedPath = new PathString(ApplicationConstants.ACCESS_DENIED_ACTION_PATH);
    options.ReturnUrlParameter = ApplicationConstants.RETURN_URL_PARAMETER;
    options.Cookie.IsEssential = true;
    options.Cookie.Name = CookieNames.COOKIE_AUTHENTICATION;
});
...
```

Make sure that you call `ConfigureApplicationCookie` _after_ `AddIdentity`.
### Define the AccessDenied controller action
With identity configured, let’s create the handler to which it will redirect.
Add a new action called `AccessDenied` to the authentication controller, utilizing the path constant from earlier.
Include logic that extracts a language codename from the provided _return URL_ if it exists, and returns the default language code otherwise.
Then, use it to get a language-specific sign-in URL with a new `IMembershipService` method.
Finally, redirect to the sign-in URL, passing along the query string parameter so that we can redirect to the secured content after successful authentication.
C#
**AuthenticationController.cs**
Copy
```
...
using Kentico.Content.Web.Mvc.Routing;
...
namespace TrainingGuides.Web.Features.Membership.Controllers;

public class AuthenticationController : Controller
{
    // Use dependency injection to populate these
    private readonly IMembershipService membershipService;
    private readonly IStringLocalizer<SharedResources> stringLocalizer;
    private readonly IHttpRequestService httpRequestService;
    // NEW DEPENDENCIES
    private readonly IPreferredLanguageRetriever preferredLanguageRetriever;
    private readonly IInfoProvider<ContentLanguageInfo> contentLanguageInfoProvider;
    // END NEW DEPENDENCIES

    private const string SIGN_IN_FAILED = "Your sign-in attempt was not successful. Please try again.";

    ...

    [HttpGet(ApplicationConstants.ACCESS_DENIED_ACTION_PATH)]
    public async Task<IActionResult> AccessDenied([FromQuery(Name = ApplicationConstants.RETURN_URL_PARAMETER)] string returnUrl)
    {
        string language = GetLanguageFromReturnUrl(returnUrl);

        string signInUrl = await membershipService.GetSignInUrl(language, true);

        var query = QueryString.Create(ApplicationConstants.RETURN_URL_PARAMETER, returnUrl);

        var redirectUrl = new UriBuilder(signInUrl)
        {
            Query = query.ToString()
        };

        return Redirect(redirectUrl.ToString());
    }

    private string GetLanguageFromReturnUrl(string returnUrl)
    {
        // Cache this in real-world scenarios
        var languages = contentLanguageInfoProvider.Get()
            .Column(nameof(ContentLanguageInfo.ContentLanguageName))
            .GetListResult<string>();

        foreach (string language in languages)
        {
            if (returnUrl.StartsWith($"/{language}/", StringComparison.OrdinalIgnoreCase) || returnUrl.StartsWith($"~/{language}/", StringComparison.OrdinalIgnoreCase))
            {
                return language;
            }
        }
        // Since this controller action has no language in its path, this will return the channel default.
        return preferredLanguageRetriever.Get();
    }
}
```

Here is the referenced addition to the membership service:
The code samples in this example rely on a [decorated version](https://github.com/Kentico/xperience-by-kentico-training-guides/blob/main/src/TrainingGuides.Web/Features/Shared/Services/TrainingGuidesWebPageUrlRetriever.cs) of `IWebPageUrlRetriever` that includes exception handling.
If you do not plan to use a similar customization, make sure to handle errors that the `Retrieve` method may throw.
C#
**IMembershipService.cs**
Copy
```
/// <summary>
/// Gets the URL of the expected sign in page in the provided language.
/// </summary>
/// <param name="language">The required language to retrieve.</param>
/// <param name="absoluteURL">Whether to return an absolute URL.</param>
/// <returns>The relative path of the sign in page.</returns>
Task<string> GetSignInUrl(string language, bool absoluteURL = false);
```

C#
**MembershipService.cs**
Copy
```
/// <inheritdoc />
public async Task<string> GetSignInUrl(string language, bool absoluteURL = false)
    => await GetPageUrl(ApplicationConstants.EXPECTED_SIGN_IN_PATH, language, absoluteURL);
...
private async Task<string> GetPageUrl(string expectedPagePath, string language, bool absoluteURL = false)
{
    var signInUrl = await webPageUrlRetriever.Retrieve(
        webPageTreePath: expectedPagePath,
        websiteChannelName: websiteChannelContext.WebsiteChannelName,
        languageName: language
    );

    return absoluteURL ?
        httpRequestService.GetAbsoluteUrlForPath(signInUrl.RelativePath.TrimStart('~'), false)
        : signInUrl.RelativePath;
}
...
```

And here is the constant it utilizes for the tree path of the sign-in page:
C#
**~/Features/Shared/Helpers/ApplicationConstants.cs**
Copy
```
...
public const string EXPECTED_SIGN_IN_PATH = "/Membership/Sign_in";
...
```

**Referencing specific items in the database from code**
This code depends on a sign-in page existing at a specific path in the content tree. (It doesn’t need to be the only one, but it must exist at the specified tree path.)
When you need to store a reference to some kind of identifier for data in the database, we recommend using something that editors have control over, such as a tree path or codename, rather than something like a numerical ID or GUID.
That way, if an editor accidentally moves or deletes an object, it is possible for them to fix it with no code changes necessary.
## Check your progress
Now, if you set the _About conifers_ page to require authentication earlier, you can test out the functionality.
Your browser does not support the video tag. 
You’ll notice that the secured page correctly redirects to the _Sign in_ page, but once the member authenticates, the form **DOES NOT** send them to the _return URL_.
We’ll have to make some changes for that to work.
## Add redirect logic to the Sign-in widget
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

See [this section of the language selector guide](guides/development/multilingual/implement-language-selector-for-your-website#handle-urls-and-routing) or [the Training guides repository](https://github.com/Kentico/xperience-by-kentico-training-guides/blob/finished/src/TrainingGuides.Web/Features/Shared/Services/HttpRequestService.cs) for details about how to implement the `GetBaseUrlWithLanguage` method.
If you ensure the return URL only works with relative paths, you can prevent attackers from exploiting the query string to redirect members to phishing sites after successful authentication on your site.
## See the redirect in action
Now the sign in form should correctly redirect you to the _return URL_ after you authenticate.
Your browser does not support the video tag. 
However, you may notice that even if you followed along [earlier](guides/development/members/require-authentication-for-certain-content#restrict-a-reusable-item-in-the-content-hub) and set a reusable item to require authentication, it is still visible to signed out users on the page.
[![Screenshot of the ‘About frogs’ article displaying on the site even though the underlying reusable item is secured](docsassets/guides/require-authentication-for-certain-content/AboutFrogsUnsecured.png)](https://docs.kentico.com/docsassets/guides/require-authentication-for-certain-content/AboutFrogsUnsecured.png)
This happens because the web page that references this reusable article is not secured. The _content tree-based router_ only knows to check security of the pages it is retrieving, and not any reusable content they might reference.
## Handle secured reusable content
Ideally, content editors should mark all pages that reference secured items to require authentication, but we shouldn’t assume that they will never make mistakes.
When pages display reusable content, you need one of the following approaches to for security:
  1. Set the query that retrieves the content to [filter out secured items](documentation/developers-and-admins/development/content-retrieval/retrieve-page-content#page-security-configuration).
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

Depending on whether you’ve followed along with the [Advanced content](guides/development/advanced-content) series, the `ArticlePageArticleContent` field may or may not exist in the `ArticlePage` type.
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