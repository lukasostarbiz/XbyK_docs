# Set up restricted pages
  * [ Copy page link ](modules/members/set-up-restricted-pages#) | [Get HelpService ID](modules/members/set-up-restricted-pages#)
Core MVC 5


[✖](modules/members/set-up-restricted-pages# "Close page link panel") [Copy to clipboard](modules/members/set-up-restricted-pages#)
When a business decides to include members in their project, they typically have some kind of exclusive content that only members should be able to see.
As a result, developers must ensure that only signed-in members can see certain content.
Luckily, Xperience and .NET Identity both include functionality to facilitate this process.
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
[ Previous page ](modules/members/create-profile-page)
9 of 12
[ Mark complete and continue ](modules/members/expand-restricted-content-and-sign-in-functionality)
  * [Community Questions & Answers](https://community.kentico.com/q-and-a)
  * [Contact support](https://community.kentico.com/support)


### Cookie consent
We use necessary [cookies](https://www.kentico.com/cookies-policy) to run our website and improve your experience while browsing. Additional cookies are only used with your consent. You may revoke your consent on the [Cookies Policy](https://www.kentico.com/cookies-policy) page or in your browser at any time. 
ACCEPT ALL  [Configure](https://www.kentico.com/cookies-policy)
USE ONLY NECESSARY 
![](https://docs.kentico.com/modules/members/set-up-restricted-pages)
[](https://docs.kentico.com/modules/members/set-up-restricted-pages)[](https://docs.kentico.com/modules/members/set-up-restricted-pages)