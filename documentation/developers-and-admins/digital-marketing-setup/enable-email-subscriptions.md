---
source: https://docs.kentico.com/documentation/developers-and-admins/digital-marketing-setup/enable-email-subscriptions
scrape_date: 2026-01-22
---

  * [Home](/documentation)
  * [Developers and admins](/documentation/developers-and-admins)
  * [Digital marketing setup](/documentation/developers-and-admins/digital-marketing-setup)
  * Enable email subscriptions 


# Enable email subscriptions
**Email license requirements**
The license tier required for email features depends on the size of the [email channels](/documentation/developers-and-admins/digital-marketing-setup/email-channel-management) that you use:
  * Email microchannels – available for all license tiers
  * Standard size email channels – require the **Advanced** license tier


To learn more, see the description of the [Channel size](/documentation/developers-and-admins/digital-marketing-setup/email-channel-management#create-and-configure-email-channels) property for email channels.
To allow your marketing team to send [regular emails to subscribers](/documentation/business-users/digital-marketing/emails/send-regular-emails-to-subscribers), you need to configure your application to correctly handle subscription and unsubscription links:
  1. [Enable the email marketing feature](#enable-the-email-marketing-feature)
  2. [Set an appropriate service domain for your email channels](#set-the-service-domain-for-your-email-channels)


## Enable the email marketing feature
To allow the system to handle subscription confirmations and unsubscriptions for email recipient lists, the email marketing feature must be enabled for your Xperience application:
  1. Open your Xperience solution in Visual Studio.
  2. Edit the **Program.cs** file.
  3. [Enable](/documentation/developers-and-admins/development/website-development-basics/configure-new-projects/enable-application-features) the email marketing feature by calling the `UseEmailMarketing` method of the `IFeaturesBuilder` delegate. Pass the delegate as an argument to `AddKentico`.
C#
**Program.cs**
Copy
```
using Kentico.Web.Mvc;
using Kentico.OnlineMarketing.Web.Mvc;

var builder = WebApplication.CreateBuilder(args);

...

builder.Services.AddKentico(features =>
{
    ...

    // Enables the email marketing feature, registers the following endpoints:
    // "/Kentico.Emails/SubscriptionConfirmation"
    // "/Kentico.Emails/Unsubscribe"
    features.UseEmailMarketing();
});
```



The subscription confirmation and unsubscription endpoints are now available for all [email channels](/documentation/developers-and-admins/digital-marketing-setup/email-channel-management).
## Set the service domain for your email channels
To correctly resolve subscription confirmation and unsubscription links in emails, your [email channels](/documentation/developers-and-admins/digital-marketing-setup/email-channel-management) must have a correctly set **Email service domain**. The service domain name is used as the base URL for these links.
The service domain needs to host an Xperience by Kentico application, connected to the database where your email channels are configured.
  * The email service domain must **exactly** match the target application’s URL, including the port number and application path. Misconfiguration may cause broken links in email content, particularly in development or testing environments.
  * The target application must support the URL scheme (protocol) specified by the application’s `UrlResolveOptions.UseSSL` [option](/documentation/developers-and-admins/development/content-retrieval/retrieve-page-content/retrieve-page-urls#url-formatting-and-behavior) and the [.NET options pattern](https://learn.microsoft.com/en-us/aspnet/core/fundamentals/configuration/options?view=aspnetcore-8.0) – **https** by default.


The most common setup is to use the domain of a related [website channel](/documentation/developers-and-admins/configuration/website-channel-management) as the service domain of your email channels. If you do not wish to associate URLs in your email content with a specific website, you can use the domain hosting the Xperience application where you manage your emails (if the application is publicly accessible).
![]()
[]()[]()
