---
source: https://docs.kentico.com/documentation/developers-and-admins/security-guidelines/rate-limiting
scrape_date: 2026-01-22
---

  * [Home](/documentation)
  * [Developers and admins](/documentation/developers-and-admins)
  * [Security guidelines](/documentation/developers-and-admins/security-guidelines)
  * Rate limiting 


# Rate limiting
Rate limiting restricts the number of requests that can be served by the application or its specific endpoints within a given window of time. This protects against denial-of-service and other brute-force attacks.
By default, Xperience provides a rate limiter for all endpoints used as part of the administration user management functionality, such as the sign-in or password reset page. For more information and related configuration options, see [Rate limiting for user management endpoints](/documentation/developers-and-admins/configuration/users/administration-registration-and-authentication/administration-forms-authentication#rate-limiting-for-user-management-endpoints).
Individual projects have different security requirements and traffic loads, so Xperience does **not** use a global rate limiter or provide rate limiting for any other endpoints by default. However, developers can use the [ASP.NET Core Rate limiting middleware](https://learn.microsoft.com/en-us/aspnet/core/performance/rate-limit) to set up their own rate limiting policies based on specific project requirements. For example, you can consider rate limiting for:
  * Custom [live site member registration and authentication](/documentation/developers-and-admins/development/registration-and-authentication) endpoints
  * [Email subscription and unsubscription](/documentation/business-users/digital-marketing/emails/send-regular-emails-to-subscribers) endpoints (_/Kentico.Emails_)
  * [Content item asset retrieval](/documentation/developers-and-admins/development/content-retrieval/retrieve-content-items#retrieve-assets) endpoint (_/getContentAsset_)
  * [GraphQL API](/documentation/developers-and-admins/development/content-retrieval/retrieve-headless-content) endpoints for headless channels (_/graphql_)


## Set up rate limiting
Use the [ASP.NET Core Rate limiting middleware](https://learn.microsoft.com/en-us/aspnet/core/performance/rate-limit) to set up and enable rate limiting. You can select any required rate limiter algorithm for each endpoint.
For existing **Xperience endpoints** , you need to create a global rate limiter with partitioned limiters for the endpoints that you wish to cover. Use conditions based on the URL path of the given endpoints to return an appropriate rate limiter policy. Return `RateLimitPartition.GetNoLimiter("")` as the global limiter’s default policy unless you wish to apply rate limiting to your entire application.
For **custom endpoints** where you have full control over the endpoint’s route registration, you can use any preferred approach to enable rate limiting (for example the [RequireRateLimiting](https://learn.microsoft.com/en-us/dotnet/api/microsoft.aspnetcore.builder.ratelimiterendpointconventionbuilderextensions.requireratelimiting) method).
**Rate limiter policy conflicts**
Carefully consider which endpoints are affected when adding rate limiters. For example, rate limiting for the _/admin_ URL path would apply to all endpoints within the Xperience administration, including the system’s administration user management endpoints, which have a [rate limiting policy applied by default](/documentation/developers-and-admins/configuration/users/administration-registration-and-authentication/administration-forms-authentication#rate-limiting-for-user-management-endpoints). If an endpoint has multiple rate limiting policies, the more restrictive policy is applied.
The following code demonstrates how to set up rate limiting for several endpoints:
C#
**Program.cs - Configure rate limiting for endpoints**
Copy
```
WebApplicationBuilder builder = WebApplication.CreateBuilder(args);

// ...

builder.Services.AddRateLimiter(options =>
{
    options.GlobalLimiter = PartitionedRateLimiter.Create<HttpContext, string>(context =>
    {
        // The HTTP context is used to create partitioned rate limiters
        // The requests's URL path is checked to determine rate limiter policies
        var path = context.Request.Path.Value;

        // Sets rate limiting for custom '/account' endpoints
        if (path.StartsWith("/account", StringComparison.OrdinalIgnoreCase))
        {
            // Always specify a unique partition key ("membership_account_endpoint" in this example)
            return RateLimitPartition.GetTokenBucketLimiter("membership_account_endpoint", partition =>
            new TokenBucketRateLimiterOptions
            {
                TokenLimit = 100,
                ReplenishmentPeriod = TimeSpan.FromSeconds(6),
                TokensPerPeriod = 10,
                QueueProcessingOrder = QueueProcessingOrder.OldestFirst,
                QueueLimit = 50
            });
        }

        // Sets rate limiting for headless channel GraphQL API endpoints
        if (path.StartsWith("/graphql", StringComparison.OrdinalIgnoreCase))
        {
            // Always specify a unique partition key ("headless_endpoint" in this example)
            return RateLimitPartition.GetTokenBucketLimiter("headless_endpoint", partition =>
            new TokenBucketRateLimiterOptions
            {
                TokenLimit = 1000,
                ReplenishmentPeriod = TimeSpan.FromSeconds(6),
                TokensPerPeriod = 50,
                QueueProcessingOrder = QueueProcessingOrder.OldestFirst,
                QueueLimit = 0
            });
        }

        // Sets the global rate limiting policy for all endpoints - in this case no rate limiter.
        // The global policy must be less restrictive than partitioned rate limiters for specific endpoints,
        // otherwise the global restrictions overwrite the policy of individual endpoints.
        return RateLimitPartition.GetNoLimiter("");
    });
});
```

To enable your custom rate limiting policies, call [UseRateLimiter](https://learn.microsoft.com/en-us/dotnet/api/microsoft.aspnetcore.builder.ratelimiterapplicationbuilderextensions.useratelimiter) in the application’s middleware pipeline. The rate limiting middleware must be enabled **before** you call `UseKentico()`.
C#
**Program.cs - Enable rate limiting middleware**
Copy
```
// ...

var app = builder.Build();

app.InitKentico();

// ...

// Enables rate limiting middleware for custom policies
// Must be called before UseKentico()
app.UseRateLimiter();

// ...

app.UseKentico();
```

![]()
[]()[]()
