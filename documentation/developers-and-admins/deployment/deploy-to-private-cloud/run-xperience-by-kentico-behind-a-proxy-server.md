---
source: https://docs.kentico.com/documentation/developers-and-admins/deployment/deploy-to-private-cloud/run-xperience-by-kentico-behind-a-proxy-server
scrape_date: 2026-01-22
---

  * [Home](/documentation)
  * [Developers and admins](/documentation/developers-and-admins)
  * [Deployment](/documentation/developers-and-admins/deployment)
  * [Deploy to private cloud](/documentation/developers-and-admins/deployment/deploy-to-private-cloud)
  * Run Xperience by Kentico behind a proxy server 


# Run Xperience by Kentico behind a proxy server
If you wish to host Xperience behind a proxy server or another service that takes over the application’s public domain (e.g., [Azure Application Gateway](https://azure.microsoft.com/en-in/services/application-gateway/)), you need to ensure request URLs from all parts of the application are generated correctly (targeting the proxy domain). Otherwise, you may encounter issues with [Cross-Origin Resource Sharing (CORS)](https://developer.mozilla.org/en-US/docs/Web/HTTP/CORS) policies when performing certain actions.
[![Acesssing an Xperience application through a reverse proxy server](/docsassets/documentation/run-xperience-by-kentico-behind-a-proxy-server/Proxy_Server.png)](/docsassets/documentation/run-xperience-by-kentico-behind-a-proxy-server/Proxy_Server.png)
To correctly configure Xperience running behind a proxy service, you need to:
  1. [Set up Forwarded Headers Middleware](#setting-up-forwarded-headers-middleware) for your Xperience application.
  2. [Consider domain alias requirements](#domain-aliases-in-proxy-scenarios).


## Setting up Forwarded Headers Middleware
See the [Configure ASP.NET Core to work with proxy servers and load balancers](https://docs.microsoft.com/en-us/aspnet/core/host-and-deploy/proxy-load-balancer) article to learn about Forwarded Headers Middleware.
The following example shows basic forwarded headers configuration in a .NET application’s [startup class](https://learn.microsoft.com/en-us/aspnet/core/fundamentals/startup) (_Program.cs_).
C#
Copy
```
...

// Configures the Forwarded Headers Middleware options
services.Configure<ForwardedHeadersOptions>(options =>
{
    // Enables processing of all 'X-Forwarded' headers (-For, -Host, -Proto and -Prefix)
    // The 'X-Forwarded-Host' header is required, other headers can be enabled or disabled as required by your proxy environment
    options.ForwardedHeaders = ForwardedHeaders.All;
});

...

// Enables the Forwarded Headers Middleware
app.UseForwardedHeaders();

...

app.UseStaticFiles();
app.UseKentico();

...
```

Adjust the configuration based on the requirements of your proxy environment.
## Domain aliases in proxy scenarios
If you need to access the Xperience application through a different domain than the proxy server’s public domain (for example a back end server domain in a local development or testing environment), you also need to add [Domain aliases](/documentation/developers-and-admins/configuration/website-channel-management#domain-aliases-and-environment-specific-domains) for your website channel.
![]()
[]()[]()
