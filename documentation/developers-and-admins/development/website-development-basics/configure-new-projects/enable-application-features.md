---
source: https://docs.kentico.com/documentation/developers-and-admins/development/website-development-basics/configure-new-projects/enable-application-features
scrape_date: 2026-01-26
---

  * [Home](/documentation)
  * [Developers and admins](/documentation/developers-and-admins)
  * [Development](/documentation/developers-and-admins/development)
  * [Website development basics](/documentation/developers-and-admins/development/website-development-basics)
  * [Configure new projects](/documentation/developers-and-admins/development/website-development-basics/configure-new-projects)
  * Enable application features 


# Enable application features
Some features provided by the system need to be explicitly enabled in the ASP.NET Core application’s startup class.
Features are enabled within the `IServiceCollection.AddKentico` call when building the application using `WebApplicationBuilder`. Each feature is enabled with a corresponding _Use*FeatureName*_ extension method:
C#
**Program.cs**
Copy
```
WebApplicationBuilder builder = WebApplication.CreateBuilder(args);

builder.Services.AddKentico(features =>
{
    features.Use*FeatureName*();
});
```

The following table provides an overview of all features that can be enabled: 
Feature |  Extension method |  Description and additional notes  
---|---|---  
[Commerce](/documentation/developers-and-admins/digital-commerce-setup) |  UseCommerce  
(Kentico.Xperience.Commerce) |  Enables the commerce feature, which allows you to build simple online stores.  
[Contact activity tracking](/documentation/business-users/digital-marketing/contact-activities) |  UseActivityTracking  
(Kentico.Activities.Web.Mvc) |  Required for page-related activities that need to be logged manually in your code. See [Set up activities](/documentation/developers-and-admins/digital-marketing-setup/set-up-activities) for more information.  
[Content tree-based routing](/documentation/developers-and-admins/development/routing/content-tree-based-routing) |  UseWebPageRouting  
(Kentico.Content.Web.Mvc.Routing) |  Enables [Content tree-based routing](/documentation/developers-and-admins/development/routing/content-tree-based-routing) and registers the resulting routes.  
[Cross-site tracking](/documentation/developers-and-admins/digital-marketing-setup/cross-site-tracking) |  UseCrossSiteTracking  
(Kentico.OnlineMarketing.Web.Mvc) |  Registers endpoints that process contact and activity tracking requests from external sites.  
[Email Builder](/documentation/developers-and-admins/development/builders/email-builder) |  UseEmailBuilder  
(Kentico.EmailBuilder.Web.Mvc) |  Initializes the Email Builder feature and registers required routes.  
[Email subscriptions](/documentation/business-users/digital-marketing/emails/send-regular-emails-to-subscribers) |  UseEmailMarketing  
(Kentico.OnlineMarketing.Web.Mvc) |  Registers endpoints that process subscription confirmation and unsubscription requests for email recipient lists. See [Enable email subscriptions](/documentation/developers-and-admins/digital-marketing-setup/enable-email-subscriptions) for additional details.  
[Email statistic logging](/documentation/business-users/digital-marketing/emails/track-email-statistics) |  UseEmailStatisticsLogging  
(Kentico.OnlineMarketing.Web.Mvc) |  Enables tracking of statistics for [emails](/documentation/business-users/digital-marketing/emails) sent from Xperience, including the number of sent emails, email opens and clicked links. Only applies to emails created in the Xperience administration within an email channel application. When a recipient opens an email or clicks a link in its content, a hidden request is sent to the Xperience application. Enabling the _UseEmailStatisticsLogging_ feature registers endpoints that process the tracking requests and log the appropriate email statistics. See [Set up email tracking](/documentation/developers-and-admins/digital-marketing-setup/set-up-email-tracking) for additional details.  
[Page Builder](/documentation/developers-and-admins/development/builders/page-builder) |  UsePageBuilder  
(Kentico.PageBuilder.Web.Mvc) |  Initializes the Page Builder feature and registers required routes. See [Enable the Page Builder feature](/documentation/developers-and-admins/development/builders/page-builder#enable-the-page-builder-feature) for more details and additional configuration options.  
![]()
[]()[]()
