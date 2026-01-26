---
source: https://docs.kentico.com/documentation/developers-and-admins/digital-marketing-setup/set-up-email-tracking
scrape_date: 2026-01-26
---

  * [Home](/documentation)
  * [Developers and admins](/documentation/developers-and-admins)
  * [Digital marketing setup](/documentation/developers-and-admins/digital-marketing-setup)
  * Set up email tracking 


# Set up email tracking
**Email license requirements**
The license tier required for email features depends on the size of the [email channels](/documentation/developers-and-admins/digital-marketing-setup/email-channel-management) that you use:
  * Email microchannels – available for all license tiers
  * Standard size email channels – require the **Advanced** license tier


To learn more, see the description of the [Channel size](/documentation/developers-and-admins/digital-marketing-setup/email-channel-management#create-and-configure-email-channels) property for email channels.
To get the most out of your [email communication](/documentation/business-users/digital-marketing/emails), it is important to determine the effectiveness of individual emails and optimize them according to the results. You can achieve this by tracking sent emails, and monitoring the reactions of recipients. The system collects data by processing hidden image requests and link redirects, and by tracking email bounces.
The following analytics are available for emails:
  * **Sent emails** – logged when an email successfully leaves the Xperience platform, e.g., to an SMTP server or SendGrid for further mailing. This statistic does not always guarantee that the email was delivered to the recipient.
  * **Email opens** – logged when a recipient opens an email (in a client that allows loading of images).
  * **Clicked links** – logged when recipients click links within the content of an email.
  * **Link click activities** – link clicks in emails can also be logged as [activities](/documentation/business-users/digital-marketing/contact-activities), which provide information about individual recipients and links. To ensure compliance with data protection regulations, email activities are not logged by default. Developers need to [implement logic](/documentation/developers-and-admins/digital-marketing-setup/set-up-email-tracking/log-email-activities) that decides when it is possible to track email activities for specific contacts, for example based on [consents](/documentation/developers-and-admins/data-protection/consent-management).


Additional statistics are provided for [regular emails](/documentation/business-users/digital-marketing/emails/send-regular-emails-to-subscribers) sent to subscribed recipients:
  * **Delivered emails** – calculated by tracking [email bounces](https://en.wikipedia.org/wiki/Bounce_message) (undelivered emails) and subtracting the bounce count from the total number of sent emails. Bounced email tracking requires [additional configuration](#configure-bounced-email-tracking).
  * **Unsubscriptions** – logged when a recipient uses the email’s unsubscribe link to stop receiving emails for the given recipient list.
  * **Spam reports** – only available when using [SendGrid](/documentation/developers-and-admins/configuration/email-configuration) to send emails. Logged when a recipient marks the message as spam in their email client (see [Spam Reports](https://docs.sendgrid.com/glossary/spam-reports)).


The statistics and activities can be viewed for emails in email channel applications. For more information, see [Track email statistics](/documentation/business-users/digital-marketing/emails/track-email-statistics).
## Limitations
Keep the following limitations in mind when starting with email tracking:
  * Tracking is only available for [emails](/documentation/business-users/digital-marketing/emails) created using an email channel application in Xperience. For example, you cannot track custom emails created or sent using the API.
  * Statistics are not tracked for draft emails.
  * You cannot retroactively track emails that have already been sent before [enabling the tracking feature](#enable-the-email-tracking-feature).
  * Logged statistics are not displayed immediately in the administration interface for emails. The system calculates logged email statistics periodically every 30 minutes, or users can trigger the recalculation manually in email channel applications. This delay does not apply to _email activities_ , which are displayed almost immediately when they occur.


## Set the service domain for your email channels
To use email tracking, your [email channels](/documentation/developers-and-admins/digital-marketing-setup/email-channel-management) must have a correctly set **Email service domain**. The service domain name is used as the base URL of tracking endpoints within email content (used within the URLs of click tracking links and the email open tracking pixel image).
The service domain needs to host an Xperience by Kentico application, connected to the database where your email channels are configured.
  * The email service domain must **exactly** match the target application’s URL, including the port number and application path. Misconfiguration may break links in email content and prevent tracking from working, particularly in development or testing environments.
  * The target application must support the URL scheme (protocol) specified by the application’s `UrlResolveOptions.UseSSL` [option](/documentation/developers-and-admins/development/content-retrieval/retrieve-page-content/retrieve-page-urls#url-formatting-and-behavior) and the [.NET options pattern](https://learn.microsoft.com/en-us/aspnet/core/fundamentals/configuration/options?view=aspnetcore-8.0) – **https** by default.


The most common setup is to use the domain of a related [website channel](/documentation/developers-and-admins/configuration/website-channel-management) as the service domain of your email channels. If you do not wish to associate URLs in your email content with a specific website, you can use the domain hosting the Xperience application where you manage your emails (if the application is publicly accessible).
## Enable the email tracking feature
To track statistics and activities for emails, the corresponding feature must be enabled for your Xperience application:
  1. Open your Xperience solution in Visual Studio.
  2. Edit the **Program.cs** file.
  3. [Enable](/documentation/developers-and-admins/development/website-development-basics/configure-new-projects/enable-application-features) the email tracking feature by calling the `UseEmailStatisticsLogging` method of the `IFeaturesBuilder` delegate. Pass the delegate as an argument to `AddKentico`.
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

    // Enables the email tracking feature for statistics and activities, registers the following endpoints:
    // "/Kentico.Emails/OpenEmail.gif" for opened email tracking
    // "/Kentico.Emails/EmailClickedLink" for clicked link tracking
    features.UseEmailStatisticsLogging();
});
```



When a recipient opens an email or clicks a link in its content, a request is sent to the Xperience application. The endpoints registered by the `UseEmailStatisticsLogging` feature process the tracking requests and log the appropriate email statistics and activities. The feature enables tracking for all [email channels](/documentation/developers-and-admins/digital-marketing-setup/email-channel-management).
## Implement email activity logging
Unlike the overall email statistics, email activities are not anonymous, and provide information about the actions of individual email recipients. To ensure compliance with data protection regulations, email activities are **not logged by default**.
Developers need to [implement logic](/documentation/developers-and-admins/digital-marketing-setup/set-up-email-tracking/log-email-activities) that decides when it is possible to track email activities for specific contacts, for example based on [consents](/documentation/developers-and-admins/data-protection/consent-management).
## Configure bounced email tracking
When an email cannot be delivered successfully, an automatic reply informing about the problem is returned ([bounced](https://en.wikipedia.org/wiki/Bounce_message)) back to the sender or another designated address. You can configure the system to match bounces with emails sent from Xperience and track delivery statistics. Bounced email tracking also allows the system to identify addresses that do not correctly receive emails, which helps keep recipient lists healthy and protects your sender reputation.
To set up bounced email tracking, start by [enabling the email tracking feature](#enable-the-email-tracking-feature). Additional steps depend on the [email clients](/documentation/developers-and-admins/configuration/email-configuration) configured for your application or specific email channels – [SendGrid integration](#sendgrid) or an [SMTP server](#smtp-server).
### SendGrid
When [sending email via SendGrid](/documentation/developers-and-admins/configuration/email-configuration) (either managed or self-managed SendGrid integration), bounced email tracking **works automatically by default**. You only need to enable the SendGrid integration for your application or specific email channels by calling the appropriate _Add*SendGrid_ extension methods when building your application.
Evaluation and tracking of bounces is done on the side of SendGrid. If an email cannot be delivered and is identified as bounced, the information is retrieved by Xperience and logged as a bounce in the given email’s statistics. The [contact](/documentation/business-users/digital-marketing/contact-management) associated with the bounced address switches to the **Hard bounce** status and is excluded when sending further emails. If necessary, marketers can **Reset bounces** for individual contacts from the **Email marketing** section of the contact’s profile in the **Contact management** application.
**SendGrid email categories**
The system tracks bounce statistics for emails using [Categories](https://docs.sendgrid.com/for-developers/sending-email/categories) in SendGrid. Every email created in an Xperienceemail channel application is represented by a separate SendGrid category. The category names are in format: _Xperience- <email GUID identifier>_
To preserve your email statistics, make sure these categories **are not deleted** from your SendGrid account.
The maximum number of categories is [limited](https://docs.sendgrid.com/for-developers/sending-email/categories) (1000 for paid SendGrid plans, 100 for free plans). If the limit is reached, previously logged bounced email statistics may be lost when the system synchronizes with SendGrid.
### SMTP server
When [sending email using an SMTP server](/documentation/developers-and-admins/configuration/email-configuration), you need to set up a mailbox that will receive bounced emails. To ensure that bounced emails are returned to this mailbox, you can either:
  * Configure your SMTP server to set the address into the `Return-Path` header for outgoing emails. This approach is strongly recommended if possible, because it allows marketers to freely set the sender address for your emails.  
– OR –
  * Force the address as the sender for your emails. See `ForcedEmailSenderAddress` in the configuration options described below.


**Email sending and bounce tracking with Return-Path header support**
[![Email sending and bounced tracking with Return-Path header support](/docsassets/documentation/set-up-email-tracking/Bounced_Emails_SMTP_Return-Path.png)](/docsassets/documentation/set-up-email-tracking/Bounced_Emails_SMTP_Return-Path.png)
**Email sending and bounce tracking with a forced sender address**
**[![Email sending and bounce tracking with a forced sender address](/docsassets/documentation/set-up-email-tracking/Bounced_Emails_SMTP_No_Return-Path.png)](/docsassets/documentation/set-up-email-tracking/Bounced_Emails_SMTP_No_Return-Path.png)**
Continue by configuring the Xperience application to connect to your bounce mailbox using [POP3](https://en.wikipedia.org/wiki/Post_Office_Protocol):
  1. Open your Xperience project in Visual Studio and edit the **Program.cs** file.
  2. Call the one or more of the following methods for `IServiceCollection` when building the web application:
     * `AddXperienceChannelSmtpBouncedEmails` – POP3 connection to a bounce mailbox for a specific [email channel](/documentation/developers-and-admins/digital-marketing-setup/email-channel-management). Specify the **code name** of the email channel as the method’s parameter.
     * `AddXperienceSmtpBouncedEmails` – POP3 connection to a bounce mailbox as a fallback for all email channels without their own dedicated configuration. This method is primarily intended for backward compatibility, and we recommend using channel-specific configuration.
  3. Use ASP.NET Core [configuration providers](https://docs.microsoft.com/en-us/aspnet/core/fundamentals/configuration/) (such as the default _appSettings.json_ file or [Azure Key Vault](https://docs.microsoft.com/en-us/aspnet/core/security/key-vault-configuration)) to load configuration options for the following types:
     * `BouncedEmailsOptions` – configure separately for every email channel. The **name** of the options instance must match the code name of the email channel. See the code example below.
       * `Server` – configures the POP3 connection to your bounce mailbox. Set the properties of the underlying `Pop3Server` type:
         * `Host` – the hostname of the mail server (domain name or IP address).
         * `Port` – the port number used for the connection (typically 110 for non-encrypted connections or 995 for secured connections).
         * `UserName` – the user name for the connection to the mail server (typically, the user name will match the email address of the bounce mailbox).
         * `Password` – the password for the user name.
       * `ForcedEmailSenderAddress` – overrides the sender address for all emails. Set the value to the email address of your bounce mailbox.
Only use the `ForcedEmailSenderAddress` property if you cannot configure your SMTP server to set the `Return-Path` header for outgoing emails.
Setting `ForcedEmailSenderAddress` prevents overrides the email channel’s sending domain and prevents configuration sender addresses in the administration interface.
     * `BouncedEmailsGlobalOptions`
       * `SoftBounceLimit` – the number of soft bounces allowed before an email address is excluded when sending emails to any recipient list. Set to 5 by default.


C#
**Program.cs**
Copy
```
using Kentico.Web.Mvc;
using CMS.EmailEngine;
using CMS.EmailMarketing;

var builder = WebApplication.CreateBuilder(args);

...

// Configures email sending using an SMTP server for 'EmailChannel1' and 'EmailChannel2'
builder.Services.AddXperienceChannelSmtp("EmailChannel1", ...);
builder.Services.AddXperienceChannelSmtp("EmailChannel2", ...);

// Enables bounce tracking for emails sent via an SMTP server for 'EmailChannel1' and 'EmailChannel2'
builder.Services.AddXperienceChannelSmtpBouncedEmails("EmailChannel1");
builder.Services.AddXperienceChannelSmtpBouncedEmails("EmailChannel2");

// Loads the bounced email configuration from configuration file sections
builder.Services.Configure<BouncedEmailsOptions>("EmailChannel1", builder.Configuration.GetSection("Channel1BounceOptions"));
builder.Services.Configure<BouncedEmailsOptions>("EmailChannel2", builder.Configuration.GetSection("Channel2BounceOptions"));
builder.Services.Configure<BouncedEmailsGlobalOptions>(builder.Configuration.GetSection("BouncedEmailsGlobalOptions"));
```

JSON
**Example - appsettings.json**
Copy
```
{
  "Channel1BounceOptions": {
    "Server" : {
      "Host" : "server1.com",
      "Port" : 995,
      "UserName" : "bounces@localhost.test",
      "Password" : "password"
    }
  },
  "Channel2BounceOptions": {
    "Server" : {
      "Host" : "server2.com",
      "Port" : 995,
      "UserName" : "bounces@localhost.test",
      "Password" : "password"
    }
  },
  "BouncedEmailsGlobalOptions": {
    "SoftBounceLimit" : 7
  }
  ...
}
```

The system now regularly checks for bounced emails.
When a bounce is detected, it is logged in the given email’s statistics. Additionally, the bounce is added for the [contact](/documentation/business-users/digital-marketing/contact-management) associated with the given address. Two types of bounces are possible:
  * **Soft bounce** – emails that could not be delivered due to a temporary problem, for example, if the mailbox was full, the email message was too large, or the email was blocked due to a spam filter. The system allows soft bounces until the count reaches the number set by the `SoftBounceLimit` configuration option. If the limit is reach, the contact is excluded when sending further emails.
  * **Hard bounce** – emails that could not be delivered due to a permanent problem, for example, if the email address doesn’t exist, or the target email server has completely blocked delivery. If a hard bounce is encountered, the contact switches to the _Hard bounce_ status, and is excluded when sending further emails.


If necessary, marketers can **Reset bounces** for individual contacts from the **Email marketing** section of the contact’s profile in the **Contact management** application.
[![Information about email bounces in the profile of contacts](/docsassets/documentation/set-up-email-tracking/Contact_Profile_Email_Bounces.png)](/docsassets/documentation/set-up-email-tracking/Contact_Profile_Email_Bounces.png)
![]()
[]()[]()
