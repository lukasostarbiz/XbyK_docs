---
source: https://docs.kentico.com/documentation/developers-and-admins/configuration/website-channel-management
scrape_date: 2026-01-22
---

  * [Home](/documentation)
  * [Developers and admins](/documentation/developers-and-admins)
  * [Configuration](/documentation/developers-and-admins/configuration)
  * Website channel management 


# Website channel management
Website channels encapsulate websites managed by Xperience. Each channel is an independent entity with its own domain name configuration and content management.
Channels store content in [pages](/documentation/business-users/website-content) and [linked content items](/documentation/business-users/content-hub/content-items#link-content-items). See the [content modeling guide](/guides/architecture/content-modeling/content-modeling-guide) to learn more about the recommended ways to store and display content in Xperience. 
## Create website channels
To create a new website channel:
  1. Open the **Channel management** application in the Xperience administration.
  2. Select **New channel**.
  3. Fill in: 
     * **Channel name** – the name of the channel displayed in the administration. Also sets the name of the channel application where users manage the website’s pages and edit content.
     * _(Optional)_ **Identifiers** – specify the code name if you wish to use a code name different than the pre-filled value. 
       * Channel code names must be unique across all channels (of any type).
       * In projects intended for [SaaS deployment](/documentation/developers-and-admins/deployment/deploy-to-the-saas-environment) (created with the `--cloud` parameter), only code names with alphanumeric characters are allowed.
     * **Channel type** – select _Website_ as the channel type.
     * **Channel size** – channels are available in two sizes: _Standard_ and _Micro_. **Standard** channels may contain an unlimited number of content items. **Microchannels** are limited to 20 items in the channel’s page tree (including both standard pages and other items like folders or navigation menu structure). Each page can link an unlimited number of [reusable content items](/documentation/business-users/content-hub).
     * **Website domain** – the domain name on which the website will be accessible. See [Website channels in private cloud environments](#website-channels-in-private-cloud-environments) to learn about the expected format and limitations when setting website domains.
     * **Primary language** – a language configured in the **Languages** application. In multilingual content, the primary language is not displayed in URLs, as opposed to other languages. For example: If the primary language is set to English, and content is also in French, the URLs are in the format: 
       * `~/page` for English
       * `~/fr-fr/page` for French
  4. **Save** the changes.


The website channel is now ready. After you add a channel, the system automatically creates a new application matching the channel’s name in the **Channels** category of the administration’s application list. This is the application where users can create and edit the channel’s [pages](/documentation/business-users/website-content).
For applications [deployed in the SaaS environment](/documentation/developers-and-admins/deployment/deploy-to-the-saas-environment), you also need to add the channel in Xperience Portal. See [Website channels in the SaaS environment](#website-channels-in-the-saas-environment).
### Configure website channels
If you need to configure an existing channel, open the channel again via the **Channel management** application and edit the values on the **General** and **Channel settings** tabs.
Changing the **Primary language** affects the live URLs of the website’s pages in the given languages, and breaks page links added in the [Rich text editor](/documentation/business-users/rich-text-editor).
### Change channel size of existing channels
You can freely change between the _microchannel_ and _standard_ channel size of existing channels via the **General** tab. Additionally, changing _standard_ channels to _microchannels_ does not cause any data loss in case the converted channel exceeds microchannel limits. However, you will receive a license violation warning if the resulting channel or item count oversteps the limits of your license for that particular channel type.
## Add allowed content types
Every page in Xperience uses a specific [content type](/documentation/developers-and-admins/development/content-types). The content type defines the fields that users can edit when creating pages.
To create pages under a channel, you need to prepare suitable [content types](/documentation/developers-and-admins/development/content-types) and allow them for the channel:
  1. Open the **Channel management** application in Xperience.
  2. Select your website channel.
  3. On the **Allowed content types** tab, **Select content types**.
  4. Select or clear the checkboxes to choose which content types you want to make available in the channel.
  5. Select **Save**.


### Configure content types users can use
You can granularly limit the content types users can use to create pages for each page, folder, and content type within a given website channel. See [Limit the pages users can create](/documentation/developers-and-admins/development/content-types/limit-the-pages-users-can-create) for more information.
## Website channels in private cloud environments
Each website channel must have a unique domain assigned. The domain determines where the live website is accessible. The system also uses the domain when displaying previews of live content, resolving hyperlinks in text, and other cases.
### Domain name format
Use the following format for the domain name:
  * include the port number if the application is running under a different port than _80_
  * do not include the URL scheme (protocol) – both _http_ and _https_ are included automatically
  * include the path in the URL if your site is running under a specific path, for example, _mysite.com/path_


**Incorrect domain name** :
  * https://mysite.com


**Correct domain names** :
  * mysite.com
  * partners.mysite.com
  * mysite.com:8080
  * mysite.com/path


### Configure a domain name in the Xperience administration
  1. Use a domain name conforming to the [domain name format](#domain-name-format).
To run Xperience in your local development environment, for example on the _localhost_ domain, set **Website domain** to `localhost`.
You can configure the system to use a different set of domains according to the project environment. See [Domain aliases and environment-specific domains](#domain-aliases-and-environment-specific-domains). 
  2. Open the **Channel management** application in the **Xperience administration**.
  3. Select the website channel.
  4. Switch to the **General** tab.
  5. Edit the **Website domain** field.
  6. Select **Save**.


The domain name is now registered in the Xperience administration.
### Domain aliases and environment-specific domains
Domain aliases are alternative domain names that point to the same website. You can add any number of domain aliases to the main domain name.
Channel domain aliases can be configured programmatically using the `WebsiteChannelDomainOptions` options class and ASP.NET Core [configuration providers](https://learn.microsoft.com/en-us/aspnet/core/fundamentals/configuration/).
For example, when configured via `appsettings.json`:
JSON
**appsettings.json**
Copy
```
{
  "WebsiteChannelDomains": {
    "DomainOverrides": {
      "DancingGoatPages": {
        "Domains": [ "dancinggoat.com", "dancingllama.com" ]
      }
    }
  },
  ...
}
```

Where `DancingGoatPages` is the code name of the website channel to configure.
The system uses the **first** domain specified in the `Domains` array as the **main domain** (_dancinggoat.com_ in the example above). This configuration **takes priority** over the channel options set via the **General** tab in the Xperience user interface. The domain gets used, for example, when resolving relative links (in rich text content, email body). All additional domains are treated as **domain aliases** – they can be used to access the website, but are not reflected in URLs.
C#
**Program.cs**
Copy
```
using CMS.Websites;

var builder = WebApplication.CreateBuilder(args);

// Apply the configuration from appsettings.json
builder.Services.Configure<WebsiteChannelDomainOptions>(builder.Configuration.GetSection("WebsiteChannelDomains"));
```

For channels not configured via application settings, the values specified via the administration interface are used.
You can also make use of the [options pattern](https://learn.microsoft.com/en-us/aspnet/core/fundamentals/configuration/options) to set different domains per [environment](https://learn.microsoft.com/en-us/aspnet/core/fundamentals/environments) by introducing corresponding **appsettings.environment.json** files. The following example sets the primary domain to _localhost_ when the project is run under the _Development_ environment.
JSON
**appsettings.Development.json**
Copy
```
{
  "WebsiteChannelDomains": {
    "DomainOverrides": {
      "DancingGoatPages": {
        "Domains": [ "localhost", "localdev.com" ]
      }
    }
  }
}
```

## Website channels in the SaaS environment
### Add website channels in Xperience Portal
For applications [deployed in the SaaS environment](/documentation/developers-and-admins/deployment/deploy-to-the-saas-environment), you need to add your channels in [Xperience Portal](/documentation/developers-and-admins/saas/xperience-portal) after you create them in the [Xperience administration](#create-website-channels):
  1. Open the **Channels and Domains → Channels** application in Xperience Portal.
  2. Select **Add channel**.
  3. Set the channel properties: 
     * **Display name** – enter a name for the channel that will be displayed in Xperience Portal.
     * **Code name** – copy the exact code name of the [website channel](#create-website-channels) from the _Channel management_ application in the Xperience administration.
     * **Channel type** – _Website_
     * **Channel size** - select the same [channel size](#create-website-channels) as in the _Channel management_ application in the Xperience administration.
  4. Select **Add channel**.


After you add a channel, the system automatically starts configuring channel domain settings for the deployed applications. This process may take up to several minutes. During this time you can continue configuring other channels and [domains](#add-custom-website-channel-domains).
Once you perform all required changes to channels and domains and after the configuration is completed by the system, select the **Apply changes** button in the information ribbon at the top of the interface and perform a final review of the changes you are about to apply. Applying these changes triggers a restart of the affected applications.
You may need to manually refresh the current page for the **Apply changes** button to appear in the information ribbon.
When the domain and channel changes are applied, the content of the website channel becomes available under an automatically generated **Default domain** unless you [add suitable custom domains](#manage-channel-domains-in-xperience-portal) for the channel in production or staging environments.
### Manage channel domains in Xperience Portal
Website channels in [Xperience Portal](/documentation/developers-and-admins/saas/xperience-portal) use the following types of domain names:
  * **Default** – a default domain is generated for every website channel and environment. Default domains cannot be removed or altered.
  * **Custom** – a custom domain added for the PROD or STG environment. This can be either an apex domain (_example.com_) or a subdomain (_www.example.com_ , _sub.example.com_ , …).
Each apex domain and subdomain is treated as an individual custom domain and need to be added separately.
The number of available custom domains for a website channel depends on your [service plan](/documentation/developers-and-admins/saas/saas-service-plans). By default, all service plans allow two custom domains per channel (one for the apex domain and one for the _www_ subdomain). Should you need additional custom domains for a single website channel, contact Kentico sales.
  * **Main domain** – the primary domain set for the website channel, for example used in links to the site’s pages. Each channel can only have one main domain, typically a custom domain name suitable for the production or staging environment.


### Add custom website channel domains
To set a custom domain for a website channel you need to add the domain in Xperience Portal and set the corresponding DNS records in the DNS registry of your domain provider. This scenario describes a simpler process to add new domain to non-public projects and may cause downtime to your application. If you want to add a new domain to a public project and minimize downtime, see [Add website channel domains with minimal downtime](#add-website-channel-domains-with-minimal-downtime).
  1. Choose a domain name conforming to the following rules: 
     * The website cannot run under a specific path, for example: _mysite.com/path_
     * The domain name cannot include a **port number** or **URL scheme**.
  2. As a user with the _Tenant administrator_ or _DevOps Engineer_ [role](/documentation/developers-and-admins/saas/xperience-portal/reference-xperience-portal-user-roles), open the **Channels and Domains → Channels** application in [Xperience Portal](/documentation/developers-and-admins/saas/xperience-portal).
  3. Select the **Domains** (
  4. Select **Add domain**.
  5. Choose the **Environment** (custom domains are only supported for production or staging environments).
  6. Enter the custom **Domain name** , select **Add domain** , and select **Next**. 
     * If you want to minimize the downtime of your application, select the **Add domain with pre-validation** option to be able to validate the DNS records before you trigger the domain switch operation, which results in significantly reduced downtime. Continue according to steps in the following section [Add website channel domains with minimal downtime](#add-website-channel-domains-with-minimal-downtime).
  7. Copy the displayed DNS records from Xperience Portal to your DNS registrar.
  8. After the records are successfully registered with your DNS registrar, confirm the DNS configuration and select **Validate**. This triggers a DNS validation process that takes a few minutes to finish. 
     * If the DNS validation fails, double-check your DNS settings, wait a few minutes, and select the validate **Validate** action to restart the validation. See [Revalidate custom domain DNS settings](#revalidate-custom-domain-dns-settings).
     * An auto-renewing SSL/TLS digital certificate is automatically created for the new domain when the validation finishes successfully.
  9. Use the **Home** (


[![Website channel domains in Xperience Portal](/docsassets/documentation/website-channel-management/website_channel_domains_portal.png)](/docsassets/documentation/website-channel-management/website_channel_domains_portal.png)
After you add a channel, the system automatically starts configuring channel domain settings for the deployed applications. This process may take up to several minutes. During this time you can continue configuring other [channels](#add-website-channels-in-xperience-portal) and domains. You do not need to (and cannot) configure the channel’s domain in the Xperience administration.
Once you perform all required changes to channels and domains and after the configuration is completed by the system, select the **Apply changes** button in the information ribbon at the top of the interface and perform a final review of the changes you are about to apply. Applying these changes triggers a restart of the affected applications.
You may need to manually refresh the current page for the **Apply changes** button to appear in the information ribbon.
**License keys** for domain names of SaaS environment deployments are handled automatically, except for the _localhost_ domain. See [License keys for SaaS local development](/documentation/developers-and-admins/installation/licenses#Licenses-SaaSLicenseGenerator).
Your application is now available under the configured domain name.
You can see the **main domain** configured for each environment and website channel in the **Deployments** application in Xperience Portal.
[![Viewing channel domains in the Deployments application](/docsassets/documentation/website-channel-management/deployment_channel_domains.png)](/docsassets/documentation/website-channel-management/deployment_channel_domains.png)
#### Add website channel domains with minimal downtime
To set a custom domain for a website channel with minimal downtime to your application, you need to add the domain in Xperience Portal and set the corresponding DNS records in the DNS registry of your domain provider. This scenario describes a more complicated process to add a new domain to public projects while minimizing downtime. If you want to add a new domain to a non-public project and downtime is not a concern, see [Add custom website channel domains](#add-custom-website-channel-domains).
  1. Choose a domain name conforming to the following rules: 
     * The website cannot run under a specific path, for example: _mysite.com/path_
     * The domain name cannot include a **port number** or **URL scheme**.
  2. As a user with the _Tenant administrator_ or _DevOps Engineer_ [role](/documentation/developers-and-admins/saas/xperience-portal/reference-xperience-portal-user-roles), open the **Channels and Domains → Channels** application in [Xperience Portal](/documentation/developers-and-admins/saas/xperience-portal).
  3. Select the **Domains** (
  4. Select **Add domain**.
  5. Choose the **Environment** (custom domains are only supported for production or staging environments).
  6. Enter the custom **Domain name** , select **Add domain with pre-validation** , and select **Next**.
  7. Copy the displayed DNS records from Xperience Portal to your DNS registrar.
  8. After the records are successfully registered with your DNS registrar, confirm the DNS configuration and select **Validate**. This triggers a DNS validation process that takes a few minutes to finish. 
     * If the DNS validation fails, double-check your DNS settings, wait a few minutes, and select the **Pre-validate** action to restart the validation. See [Revalidate custom domain DNS settings](#revalidate-custom-domain-dns-settings).
     * A temporary SSL/TLS digital certificate is created when the pre-validation finishes successfully.
  9. Wait for the system to finish the pre-validation process and select the **Apply changes** button in the information ribbon at the top of the interface. 
     * You may need to manually refresh the current page for the **Apply changes** button to appear in the information ribbon.
     * Applying changes from the pre-validation operation does not cause downtime of your application.
  10. Select the **Validate** action and copy the _CNAME_ DNS record from Xperience Portal to your DNS registrar and wait for the validation operation to finish. 
     * If the DNS validation fails, double-check your DNS settings, wait a few minutes, and select the **Validate** action to restart the validation. See [Revalidate custom domain DNS settings](#revalidate-custom-domain-dns-settings).
     * An auto-renewing SSL/TLS digital certificate is automatically created for the new domain when the validation finishes successfully.
  11. Finalize the domain switch process. The actions in this step should be performed with minimal delay between them. 
    1. Use the **Home** (
    2. Select the **Apply changes** button in the information ribbon at the top of the interface. 
       * This action causes a brief downtime of your application.


Your application is now available under the configured domain name.
**License keys** for domain names of SaaS environment deployments are handled automatically, except for the _localhost_ domain. See [License keys for SaaS local development](/documentation/developers-and-admins/installation/licenses#Licenses-SaaSLicenseGenerator).
You can see the **main domain** configured for each environment and website channel in the **Deployments** application in Xperience Portal.
[![Viewing channel domains in the Deployments application](/docsassets/documentation/website-channel-management/deployment_channel_domains.png)](/docsassets/documentation/website-channel-management/deployment_channel_domains.png)
### Domain status
You can check the status of a channel’s domains in the **Channels and Domains → Channels** application in [Xperience Portal](/documentation/developers-and-admins/saas/xperience-portal). Select the **Domains** (
  * **Active** – The domain is active and working.
  * **DNS validation failed** / **DNS pre-validation failed** – The DNS records are configured incorrectly, or the configuration has not yet been propagated through the DNS servers. See [Revalidate custom domain DNS settings](#revalidate-custom-domain-dns-settings).
  * **Error** – A system configuration issue with the domain. Contact [Kentico support](https://xperience.io/services/support).
  * **Transient error** – There was a temporary error while creating, validating, or deleting the domain. To recover from this status, use the _Revalidate_ (
  * **Pre-validating** – The domain is being pre-validated.
  * **Pre-validation pending** – A pre-validation is completed and awaits confirmation.
  * **Pre-validated** – The domain was successfully pre-validated.
  * **Creating** – The domain is being created.
  * **Pending** – The domain is created, but the application settings have not been applied yet.
  * **Deleting** – The domain is being deleted.


### Revalidate custom domain DNS settings
If you add a custom domain for a channel and its DNS validation fails, you need to revalidate the DNS settings in Xperience Portal.
[![Revalidating a domain with the DNS validation failed status](/docsassets/documentation/website-channel-management/dns_validation_failed.png)](/docsassets/documentation/website-channel-management/dns_validation_failed.png)
  1. Open the **Channels and Domains → Channels** application in [Xperience Portal](/documentation/developers-and-admins/saas/xperience-portal) with a **Tenant administrator** or **DevOps Engineer** role.
  2. Select the **Domains** (
  3. Select **Validate** (
  4. Xperience Portal displays DNS settings. Apply these settings with your DNS registrar.
  5. After the settings are successfully registered with your DNS registrar, select the checkbox confirming the DNS configuration and select **Validate**. This triggers a DNS validation process.
  6. Allow a few minutes for the DNS validation to finish.
  7. Check the [domain status](#domain-status).


Your custom domains are now revalidated. If the issues persist, contact [Kentico support](https://xperience.io/services/support).
### View DNS records of custom domains
You can view DNS records for any custom domain in the Xperience Portal:
  1. Open the **Channels and Domains → Channels** application in [Xperience Portal](/documentation/developers-and-admins/saas/xperience-portal) with a **Tenant administrator** or **DevOps Engineer** role.
  2. Select the **Domains** (
  3. Select the **DNS records** (  
[![Action to view DNS records](/docsassets/documentation/website-channel-management/DNS_action.png)](/docsassets/documentation/website-channel-management/DNS_action.png)


You can now see the DNS records for the domain. You can copy the values and use them, for example, with a new registrar.
[![DNS records](/docsassets/documentation/website-channel-management/DNS_records_view.png)](/docsassets/documentation/website-channel-management/DNS_records_view.png)
![]()
[]()[]()
