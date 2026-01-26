---
source: https://docs.kentico.com/documentation/developers-and-admins/configuration/administration-domain-configuration
scrape_date: 2026-01-26
---

  * [Home](/documentation)
  * [Developers and admins](/documentation/developers-and-admins)
  * [Configuration](/documentation/developers-and-admins/configuration)
  * Administration domain configuration 


# Administration domain configuration
The Xperience by Kentico [administration interface](/documentation/administration-interface-basics) is a web application that can be accessed in a browser by adding the **/admin** path after the domain where the application is deployed. When you add one or more [website channels](/documentation/developers-and-admins/configuration/website-channel-management), the administration is also available on all of the configured website channel domains.
However, even though delivering content through websites is one of the main purposes of Xperience by Kentico, it is not a website-centered product and does not require website channels to function. All channel types are optional. Xperience by Kentico can run as a content management and digital marketing platform even before you configure any channels, and it is possible to serve content through [email](/documentation/developers-and-admins/digital-marketing-setup/email-channel-management) or [headless channels](/documentation/developers-and-admins/configuration/headless-channel-management) without a website.
We recommend separating the administration from your website channels by using a **dedicated domain to access the administration**. For example, you can have website channels running on the _domain1.com_ and _domain2.com_ domains, and use a shared **admin.domain.com** domain to manage both websites, as well as features that are not website-related.
[Set up a domain redirect](#set-up-an-administration-domain-redirect) to ensure that all users consistently access the administration on the dedicated domain.
To further secure access to your project’s administration, you can consider deploying one Xperience instance for the administration and another separate instance [without the administration](/documentation/developers-and-admins/deployment/deploy-to-private-cloud/deploy-without-the-administration) to serve your live websites to visitors.
**Administration domains for SaaS deployments**
When using [Xperience by Kentico SaaS](/documentation/developers-and-admins/saas/saas-overview), you currently cannot set a custom administration domain.
However, every [deployment environment](/documentation/developers-and-admins/deployment/deploy-to-the-saas-environment#deploy-the-package-to-another-deployment-environment) has a default system domain that you can use to access the administration in format: _< environment>-<project_code_name>.xperience-sites.com_
To access the project’s administration UI, append _/admin_ to the default domain URL.
**Administration domains in local environments**
When setting up [local hosting](/documentation/developers-and-admins/development/website-development-basics/set-up-local-hosting), you can use _localhost_ with different port numbers to set up multiple domains for your website channels and administration (for example using the [launchSettings.json file](https://learn.microsoft.com/aspnet/core/fundamentals/environments#development-and-launchsettingsjson) for the [Kestrel](https://learn.microsoft.com/en-us/aspnet/core/fundamentals/servers/kestrel) web server).
## Benefits of a dedicated administration domain
Using a dedicated administration domain provides the following advantages:
  * The administration is always consistently accessed through a single domain. This reduces confusion, and prevents administrators, content editors and marketers from assuming that the administration only affects their “current” website.
  * Easier setup of single sign-on (SSO) for the administration when using [external authentication](/documentation/developers-and-admins/configuration/users/administration-registration-and-authentication/administration-external-authentication) options.
  * Simpler configuration of [Cross-origin resource sharing (CORS)](https://developer.mozilla.org/en-US/docs/Web/HTTP/CORS) for [headless channel](/documentation/developers-and-admins/configuration/headless-channel-management) features or for integrations with third-party applications or services.
  * Readiness for future expansion of your project. Any configuration or integrations that rely on the domain of the administration do not need to be adjusted to support newly added channels with other domains.
  * You have a clearly defined domain that is suitable for all administration-related purposes, such as the [service domain](/documentation/developers-and-admins/configuration/notifications#configure-domains-for-notifications) used for notifications.


## Set up an administration domain redirect
  1. Open your Xperience solution in Visual Studio.
  2. Choose a way to configure the dedicated administration domain for the application, for example, using [ASP.NET Core configuration providers](https://learn.microsoft.com/aspnet/core/fundamentals/configuration/). The following code snippets show how to set up the configuration via the _appsettings.json_ file, together with a matching options class.
JSON
**appsettings.json**
Copy
```
{
    ...

    "CMSHashStringSalt": "93c8cba7-0837-4358-9862-0483f046bcc9",
    "AdminRedirectOptions": {
        "RedirectDomain": "admin.domain.com"
    }
}
```

C#
**Options class**
Copy
```
public class AdminRedirectOptions
{
    public string RedirectDomain { get; set; }
}
```

  3. Perform a redirect to the configured administration domain for all requests with the **/admin** URL path. For example, use the [ASP.NET URL rewriting Middleware](https://learn.microsoft.com/aspnet/core/fundamentals/url-rewriting).
C#
**Program.cs**
Copy
```
var builder = WebApplication.CreateBuilder(args);

// ...

// Loads the administration redirect domain (AdminRedirectOptions) from a configuration file section
builder.Services.Configure<AdminRedirectOptions>(builder.Configuration.GetSection("AdminRedirectOptions"));

// ...

var app = builder.Build();

app.InitKentico();

app.UseStaticFiles();
app.UseCookiePolicy();
app.UseAuthentication();

// Configures the URL rewriting options
var rewriteOptions = new RewriteOptions()
    .Add(context =>
    {
        var httpContext = context.HttpContext;
        var request = httpContext.Request;

        // Checks if the URL path starts with '/admin'
        if (request.Path.StartsWithSegments("/admin", StringComparison.OrdinalIgnoreCase))
        {
            // Gets the redirect domain for the administration
            string adminDomain = httpContext.RequestServices
                .GetRequiredService<IOptions<AdminRedirectOptions>>().Value.RedirectDomain;

            // Checks if the current host is not already the administration domain
            if (!adminDomain.Equals(request.Host.ToString(), StringComparison.OrdinalIgnoreCase))
            {
                // Redirects the request to the new domain
                httpContext.Response.Redirect($"//{adminDomain}{request.Path}{request.QueryString}", permanent: false);
                context.Result = RuleResult.EndResponse;
            }
        }
    });

// Adds the URL rewriting middleware to the request pipeline using the configured RewriteOptions
app.UseRewriter(rewriteOptions);

app.UseKentico();

// ...
```

  4. Save the changes and rebuild your solution.


When a user now accesses the application on any domain with a URL path starting with **/admin** , the system automatically redirects them to the correct administration domain.
![]()
[]()[]()
