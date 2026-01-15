# Email channel management
  * [ Copy page link ](documentation/developers-and-admins/digital-marketing-setup/email-channel-management#) | [Get HelpService ID](documentation/developers-and-admins/digital-marketing-setup/email-channel-management#)
Core MVC 5


[✖](documentation/developers-and-admins/digital-marketing-setup/email-channel-management# "Close page link panel") [Copy to clipboard](documentation/developers-and-admins/digital-marketing-setup/email-channel-management#)
**Email license requirements**
The license tier required for email features depends on the size of the email channels that you use:
  * Email microchannels – available for all license tiers
  * Standard size email channels – require the **Advanced** license tier


To learn more, see the description of the [Channel size](documentation/developers-and-admins/digital-marketing-setup/email-channel-management#create-and-configure-email-channels) property.
Email channels are a dedicated medium of communication between you and your audience. An Xperience project may contain multiple email channels, focusing on different content strategies, languages and audience engagement techniques. For example, you can have separate email channels dedicated to marketing and supporting different products or services offered by your company, each with a different different tone and style.
Channels allow for a clean separation of your organization’s emails. Every email channel has **its own application** in the Xperience administration, where marketers [create and send emails](documentation/business-users/digital-marketing/emails).
The defining property of every email channel is a _sending domain_ , which represents your company or brand, and is used in all _sender addresses_ for the channel’s emails.
## Create and configure email channels
  1. Open the **Channel management** application in Xperience.
  2. Select **New channel**.
  3. Fill in the channel properties: 
     * **Channel name** – the name of the channel displayed in Xperience.
     * _(Optional)_ **Identifiers** – specify the code name if you wish to use a code name different than the pre-filled value.
       * Channel code names must be unique across all channels (of any type).
       * In projects intended for [SaaS deployment](documentation/developers-and-admins/deployment/deploy-to-the-saas-environment) (created with the `--cloud` parameter), only code names with alphanumeric characters are allowed.
     * **Channel type** – select _Email_ as the channel type.
     * **Channel size** – channels are available in two sizes:
       * _Standard channel_ – may contain an unlimited number of email items. Only available in the _Advanced_ license tier.
       * _Microchannel_ – available in all license tiers, but limited to **5** emails per microchannel. The limit restricts the maximum number of email content items allowed in the microchannel, but not how many times these emails can be sent to recipients. Each email can link an unlimited number of [reusable content items](documentation/business-users/content-hub).
     * **Content language** – the [language](documentation/developers-and-admins/configuration/languages) of all emails in the channel.
     * **Sending domain** – the domain associated with the channel. This domain is used for the channel’s [sender addresses](documentation/developers-and-admins/digital-marketing-setup/email-channel-management#add-sender-addresses), and must be unique across all email channels.
     * **Email service domain** – the domain used in various system links in the channel’s emails (click tracking links, the email open tracking pixel, asset URLs, subscription and unsubscription URLs). The specified domain must host an Xperience by Kentico application connected to the database where this email channel is configured. The most common setup is to use the domain of a related [website channel](documentation/developers-and-admins/configuration/website-channel-management). Alternatively, you can use the domain hosting the Xperience application where you manage your emails (if the application is publicly accessible).
**Email service domain format**
If the target application runs under a port different than  _80_ or with a specific URL path, both must be specified in the domain name. Do not include the URL scheme (_http_ or _https_). 
  4. Select **Save**.


The email channel is now ready. If you need to configure an existing channel, open the channel again via the **Channel management** application and edit the values on the **General** tab.
After you add a channel, the system automatically creates a new application matching the channel’s name in the **Channels** category of the administration’s application list. This is the application where users can create and view the channel’s emails.
For applications [deployed in the SaaS environment](documentation/developers-and-admins/deployment/deploy-to-the-saas-environment), you also need to add the channel in Xperience Portal. See [Email channels in the SaaS environment](documentation/developers-and-admins/digital-marketing-setup/email-channel-management#email-channels-in-the-saas-environment).
### Change channel size of existing channels
You can freely change between the _microchannel_ and _standard_ channel size of existing channels via the **General** tab. Additionally, changing _standard_ channels to _microchannels_ does not cause any data loss in case the converted channel exceeds microchannel limits. However, you will receive a license violation warning if the resulting channel or item count oversteps the limits of your license for that particular channel type.
### Set up emails in multiple languages
Each email channel only supports one [language](documentation/developers-and-admins/configuration/languages) (unlike pages in website channels or content items). Therefore, if you wish to send emails in a different language, you need to create a new email channel with a unique **Sending domain** for each language variant.
## Add allowed content types
Every email in Xperience uses a specific [content type](documentation/developers-and-admins/development/content-types). The content type defines the fields that users can edit when writing emails.
To create emails under a channel, you need to prepare suitable [content types](documentation/developers-and-admins/development/content-types) and allow them for the channel:
  1. Open the **Channel management** application in Xperience.
  2. Select your email channel.
  3. On the **Allowed content types** tab, **Select content types**.
  4. Select or clear the checkboxes to choose which content types you want to make available in the channel.
  5. Select **Save**.


## Add sender addresses
Every email has a sender name and address that recipients see in their inbox. In Xperience, you prepare sender addresses in advance for each email channel. When creating [emails](documentation/business-users/digital-marketing/emails), users can choose from a list of the given channel’s sender addresses.
To add a sender address for an email channel:
  1. Open the **Channel management** application in Xperience.
  2. Select your email channel.
  3. On the **Sender addresses** tab, select **Add sender address**.
  4. Fill in the sender address properties:
     * **Sender name** – the name of the email sender.
     * **Sender email address** – the “From” address that recipients see in their inbox. The domain part of all sender addresses must match the sending domain of the email channel. The address must be unique across all senders under the email channel.
  5. Select **Save**.


## Email domains in private cloud environments
### Configure sending and service domains programmatically
Programmatic configuration of email channel domains is only supported in [private cloud environments](documentation/developers-and-admins/deployment/deploy-to-private-cloud).
For applications [deployed in the SaaS environment](documentation/developers-and-admins/deployment/deploy-to-the-saas-environment), this configuration is done automatically by the infrastructure after you add an email channel and set its domains in Xperience Portal (see [Email channels in the SaaS environment](documentation/developers-and-admins/digital-marketing-setup/email-channel-management#email-channels-in-the-saas-environment)).
The sending and service domains of your email channels can be configured programmatically via the `EmailChannelDomainOptions` class using ASP.NET [configuration providers](https://learn.microsoft.com/en-us/aspnet/core/fundamentals/configuration/). This configuration **is prioritized** over the channel domain values set via the Xperience administration interface.
For example, when configured via `appsettings.json`:
JSON
**appsettings.json**
Copy
```
{
  "EmailChannelDomains": {
    "DomainOverrides": {
      "AcmeEmails": {
        "SendingDomain": "mysendingdomain.com",
        "ServiceDomain": "myservicedomain.com"
      }
    }
  },
  ...
}
```

Where `AcmeEmails` is the code name of the channel to configure.
C#
**Program.cs**
Copy
```
using CMS.Websites;

var builder = WebApplication.CreateBuilder(args);

// Apply the configuration from appsettings.json
builder.Services.Configure<EmailChannelDomainOptions>(builder.Configuration.GetSection("EmailChannelDomains"));
```

For channels not configured via application settings, the values specified via the administration interface are used.
### Domains for different application environments
You can use the [options pattern](https://learn.microsoft.com/en-us/aspnet/core/fundamentals/configuration/options) to set different domains per [environment](https://learn.microsoft.com/en-us/aspnet/core/fundamentals/environments) by introducing corresponding **appsettings.environment.json** files. The following example uses different sending and service domains when running the project under the _Development_ environment.
JSON
**appsettings.Development.json**
Copy
```
{
  "EmailChannelDomains": {
    "DomainOverrides": {
      "AcmeEmails": {
        "SendingDomain": "localtest.com",
        "ServiceDomain": "localhost"
      }
    }
  }
}
```

## Email channels in the SaaS environment
### Add email channels in Xperience Portal
For applications [deployed in the SaaS environment](documentation/developers-and-admins/deployment/deploy-to-the-saas-environment), you need to add your channels in [Xperience Portal](documentation/developers-and-admins/saas/xperience-portal) after you create them in the [Xperience administration](documentation/developers-and-admins/digital-marketing-setup/email-channel-management#create-and-configure-email-channels):
  1. Open the **Channels and Domains → Channels** application in Xperience Portal.
  2. Select **Add channel**.
  3. Set the channel properties: 
     * **Display name** – enter a name for the channel that will be displayed in Xperience Portal.
     * **Code name** – copy the exact code name of the [email channel](documentation/developers-and-admins/digital-marketing-setup/email-channel-management#create-and-configure-email-channels) from the _Channel management_ application in the Xperience administration.
     * **Channel type** – _Email_
     * **Channel size** – select the same [channel size](documentation/developers-and-admins/digital-marketing-setup/email-channel-management#create-and-configure-email-channels) as in the _Channel management_ application in the Xperience administration.
  4. Select **Add channel**.


After you add an email channel, the system automatically starts configuring the sending and service domains for the deployed applications. This process may take up to several minutes. During this time you can continue configuring other channels and [domains](documentation/developers-and-admins/digital-marketing-setup/email-channel-management#add-custom-email-channel-domains).
Once you perform all required changes to channels and domains and after the configuration is completed by the system, select the **Apply changes** button in the information ribbon at the top of the interface and perform a final review of the changes you are about to apply. Applying these changes triggers a restart of the affected applications.
You may need to manually refresh the current page for the **Apply changes** button to appear in the information ribbon.
When the configuration is finished, the email channel uses automatically generated **Default** domains unless you [add suitable custom domains](documentation/developers-and-admins/digital-marketing-setup/email-channel-management#manage-email-channel-domains-in-xperience-portal) for the channel in production or staging environments.
### Manage email channel domains in Xperience Portal
Email channels in [Xperience Portal](documentation/developers-and-admins/saas/xperience-portal) use the following basic domain types:
  * **Default** – a default sending and service domain is generated for every email channel and environment. Default domains are replaced when you add a custom domain.
  * **Custom** – custom domains added for the PROD or STG environment.


and two different kinds of domains:
  * **Sending domain** ([sender addresses](documentation/developers-and-admins/digital-marketing-setup/email-channel-management#add-sender-addresses). 
    * Must be unique across all email channels.
    * Emails are always sent via [Twilio SendGrid](https://sendgrid.com/). See [SendGrid integration](documentation/developers-and-admins/configuration/email-configuration#sendgrid-integration) for more information.
  * **Service domain** (
    * Do not include a URL path, port number or URL scheme in the service domain.
    * If your emails are related to a specific website, we recommend setting the email service domain to the main domain of the given [website channel](documentation/developers-and-admins/configuration/website-channel-management).


### Add custom email channel domains
To set a custom domain for an email channel you need to add the domain in Xperience Portal and set the corresponding DNS records in the DNS registry of your domain provider:
  1. As a user with the _Tenant administrator_ or _DevOps Engineer_ [role](documentation/developers-and-admins/saas/xperience-portal/reference-xperience-portal-user-roles), open the **Channels and Domains → Channels** application in [Xperience Portal](documentation/developers-and-admins/saas/xperience-portal).
  2. Select the **Domains** (
  3. Select **Add domain**.
  4. Choose the **Environment** (custom domains are only supported for production or staging environments).
  5. Choose the **Domain kind** (_Email sending domain_ or _Service domain_).
  6. Enter the custom **Domain name** and select **Next**.
  7. Copy the displayed DNS records from Xperience Portal to your DNS registrar.
  8. After the settings are successfully registered with your DNS registrar, confirm the DNS configuration and select **Create**. This triggers a DNS validation process that takes a few minutes to finish. 
     * If the DNS validation fails, double-check your DNS settings, wait a few minutes, and select **Validate** ([Revalidate custom domain DNS settings](documentation/developers-and-admins/digital-marketing-setup/email-channel-management#revalidate-custom-domain-dns-settings).


When the custom domain’s DNS settings are successfully validated, the domain switches to the **Active** status and replaces the default domain. Each email channel can only have one _Active_ sending domain and one _Active_ service domain.
[![Email channel domains in Xperience Portal](docsassets/documentation/email-channel-management/email_channel_domains_portal.png)](https://docs.kentico.com/docsassets/documentation/email-channel-management/email_channel_domains_portal.png)
The system then automatically configures the new domain settings for the deployed application. This process may take up to several minutes. During this time you can continue configuring other [channels](documentation/developers-and-admins/digital-marketing-setup/email-channel-management#add-email-channels-in-xperience-portal) and domains. You do not need to (and cannot) configure the channel’s domain in the Xperience administration.
Once you perform all required changes to channels and domains and after the configuration is completed by the system, select the **Apply changes** button in the information ribbon at the top of the interface and perform a final review of the changes you are about to apply. Applying these changes triggers a restart of the affected applications.
You may need to manually refresh the current page for the **Apply changes** button to appear in the information ribbon.
### Domain status
You can check the status of a channel’s domains in the **Channels and Domains → Channels** application in [Xperience Portal](documentation/developers-and-admins/saas/xperience-portal). Select the **Domains** (
  * **Active** – The domain is active and working. Each email channel can only have one _Active_ sending domain and one _Active_ service domain. Adding a new custom domain that reaches the _Active_ status replaces the previous _Active_ domain of the given kind.
  * **DNS validation failed** – The DNS records are configured incorrectly, or the configuration has not yet been propagated through the DNS servers. See [Revalidate custom domain DNS settings](documentation/developers-and-admins/digital-marketing-setup/email-channel-management#revalidate-custom-domain-dns-settings).
  * **Error** – A system configuration issue with the domain. Contact [Kentico support](https://xperience.io/services/support).
  * **Transient error** – There was a temporary error while creating, validating, or deleting the domain. To recover from this status, use the _Revalidate_ (
  * **Creating** – The domain is being created.
  * **Pending** – The domain is created, but the application settings have not been applied yet.
  * **Deleting** – The domain is being deleted.


### Revalidate custom domain DNS settings
If you add a custom domain for a channel and its DNS validation fails, you need to revalidate the DNS settings in Xperience Portal.
  1. Open the **Channels and Domains → Channels** application in [Xperience Portal](documentation/developers-and-admins/saas/xperience-portal) with a **Tenant administrator** or **DevOps Engineer** role.
  2. Select the **Domains** (
  3. Select **Revalidate** (
  4. Xperience Portal displays DNS settings. Apply these settings with your DNS registrar.
  5. After the settings are successfully registered with your DNS registrar, select the checkbox confirming the DNS configuration and select **Validate**. This triggers a DNS validation process.
  6. Allow a few minutes for the DNS validation to finish.
  7. Check the [domain status](documentation/developers-and-admins/digital-marketing-setup/email-channel-management#domain-status).


Your custom domains are now revalidated. If the issues persist, contact [Kentico support](https://xperience.io/services/support).
### View DNS records of custom domains
You can view DNS records for any custom domain in the Xperience Portal:
  1. Open the **Channels and Domains → Channels** application in [Xperience Portal](documentation/developers-and-admins/saas/xperience-portal) with a **Tenant administrator** or **DevOps Engineer** role.
  2. Select the **Domains** (
  3. Select the **DNS records** (  
[![Action to view DNS records](docsassets/documentation/email-channel-management/DNS_action.png)](https://docs.kentico.com/docsassets/documentation/email-channel-management/DNS_action.png)


You can now see the DNS records for the domain. You can copy the values and use them, for example, with a new registrar.
[![DNS records](docsassets/documentation/email-channel-management/DNS_records_view.png)](https://docs.kentico.com/docsassets/documentation/email-channel-management/DNS_records_view.png)