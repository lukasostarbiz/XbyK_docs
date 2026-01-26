---
source: https://docs.kentico.com/documentation/developers-and-admins/third-party-integrations
scrape_date: 2026-01-26
---

  * [Home](/documentation)
  * [Developers and admins](/documentation/developers-and-admins)
  * Third-party integrations 


# Third-party integrations
**SendGrid**
Xperience provides a client for sending mail using the [Twilio SendGrid](https://sendgrid.com/) email platform.
See [Email configuration](/documentation/developers-and-admins/configuration/email-configuration) to learn more.
**Microsoft Application Insights for SaaS deployments**
For projects [deployed to the SaaS environment](/documentation/developers-and-admins/deployment/deploy-to-the-saas-environment), Xperience integrates [Application Insights for ASP.NET Core applications](https://docs.microsoft.com/en-us/azure/azure-monitor/app/asp-net-core) to enable monitoring and aggregation of metrics for your website:
  * [Xperience Event logs](/documentation/developers-and-admins/configuration/event-log) (Errors and warnings only)
  * .NET exceptions and errors
  * (_Optional_) [Client-side telemetry](https://docs.microsoft.com/en-us/azure/azure-monitor/app/usage-overview)


See [Microsoft Application Insights integration](/documentation/developers-and-admins/configuration/saas-configuration#DeploytotheSaaSenvironment-EnableAppInsights).
**Cloudflare CDN for SaaS deployments**
For projects [deployed to the SaaS environment](/documentation/developers-and-admins/deployment/deploy-to-the-saas-environment), Xperience integrates [Cloudflare CDN](https://www.cloudflare.com/learning/cdn/what-is-a-cdn/). The CDN integration ensures better application performance and availability by distributing the network load across data centers and by caching website resources.
See [Cloudflare CDN](/documentation/developers-and-admins/configuration/saas-configuration#cloudflare-cdn).
**Third-party authentication providers**
Xperience by Kentico supports authentication via external identity managers. The support differs slightly between the administration interface and the live site.
**Admin UI**
The Xperience admin UI supports any OAuth/OIDC-compliant identity provider. See [Administration - External authentication](/documentation/developers-and-admins/configuration/users/administration-registration-and-authentication/administration-external-authentication) for more details.
**Live site**
Xperience by Kentico live sites use [ASP.NET Identity](https://learn.microsoft.com/en-us/aspnet/core/security/authentication/identity) to manage user accounts. When implementing external authentication for Xperience projects, you can choose from the providers supported by Identity, such as Facebook, Twitter, Google, and other OAuth/OIDC-compliant platforms. See [External authentication](/documentation/developers-and-admins/development/registration-and-authentication/external-authentication) for more details.
## External packages
**Package compatibility** : External packages may not be immediately updated to support the latest version of Xperience by Kentico. Refer to the information in the GitHub readme files of individual packages.
**Support and feedback** : To report problems or provide feedback on an external package, create a GitHub issue in the package’s repository. If you want to report a vulnerability, consult the [Kentico open source security policy](https://github.com/Kentico/.github/blob/main/SECURITY.md).
**Disqus**
Xperience provides a module that integrates with the [Disqus](https://disqus.com/) comment platform. The module contains a _Disqus comments_ widget that provides the option to add a comment section to any page on your website. Disqus also offers advanced moderation tools, analytics, and monetization options. 
The integration is provided through the following NuGet package:
  * [Kentico.Xperience.Disqus.Widget](https://www.nuget.org/packages/Kentico.Xperience.Disqus.Widget)


For more information and detailed instructions, see the [Xperience by Kentico Disqus](https://github.com/Kentico/xperience-by-kentico-disqus) GitHub repository.
**Algolia**
An external module that allows you to create [Algolia search indexes](https://www.algolia.com/products/search-and-discovery/hosted-search-api/) and index Page content types in the Xperience content tree using a code-first approach. The integration also enables you to provide a search interface on the live site using .NET API, JavaScript API, or the [InstantSearch.js](https://www.algolia.com/doc/guides/building-search-ui/what-is-instantsearch/js/) JavaScript library.
The integration is provided through the following NuGet package:
  * [Kentico.Xperience.Algolia](https://www.nuget.org/packages/Kentico.Xperience.Algolia)


For more information, see the [Algolia Search Integration](https://github.com/Kentico/xperience-by-kentico-algolia) GitHub repository.
**Lucene**
An external module that integrates Xperience with the latest 4.8 beta version of [Lucene.NET](https://github.com/apache/lucenenet), enabling auto-indexing of content in Xperience based on application-local, code-defined search indexes, and search results retrieval.
For more information, see the [Xperience by Kentico Lucene](https://github.com/Kentico/xperience-by-kentico-lucene) GitHub repository.
**CRM**
An external module (a starter kit) that simplifies the integration of Xperience by Kentico with CRM software. The module also contains two packages that directly integrate Xperience by Kentico with [Microsoft Dynamics 365 Sales](https://www.microsoft.com/en-us/dynamics-365/products/sales) and [Salesforce Sales Cloud](https://www.salesforce.com/eu/products/sales-cloud/) CRM.
The integration enables:
  * Sending [form submissions](/documentation/business-users/digital-marketing/forms/manage-form-submissions) from Xperience to the connected CRM as Leads.
  * Bi-directional synchronization between [contacts](/documentation/business-users/digital-marketing/contact-management) in Xperience and Leads or Contacts in the connected CRM.
  * Checking synchronization status in the Xperience administration.


The starter kit and two integrations are distributed as NuGet packages.
For more information, see the [Xperience by Kentico CRM](https://github.com/Kentico/xperience-by-kentico-crm) GitHub repository.
**Tag Manager**
An external module that allows marketers and editors to author and embed custom tags or code snippets into website channels. The module supports several types of code snippets out-of-the-box including:
  * [Google Tag Manager](https://tagmanager.google.com/) – lets you implement front-end services, marketing and tracking tags on a website.
  * [Google Analytics 4](https://analytics.google.com/) – lets you track users on your website.
  * [VWO A/B Testing](https://vwo.com/) – lets you optimize digital experiences and maximize conversions using a specialized suite of tools.
  * [Intercom Messenger](https://www.intercom.com/) – lets you communicate with customers live through your website.
  * [Microsoft Clarity](https://clarity.microsoft.com/) – lets you record user sessions, generate heatmaps, scrollmaps and further analyze your website users.


The module is distributed as a NuGet package.
For more information, see the [Xperience by Kentico Tag Manager](https://github.com/Kentico/xperience-by-kentico-tag-manager) GitHub repository.
**Azure AI Search**
An external module that enables you to create [Azure AI Search](https://azure.microsoft.com/en-us/products/ai-services/ai-search) indexes for the content of [pages](/documentation/business-users/website-content) from a website channel’s content tree using a code-first approach. The module is distributed as a NuGet package.
For more information, see the [Xperience by Kentico Azure Search](https://github.com/Kentico/xperience-by-kentico-azure-ai-search) GitHub repository.
**Zapier**
An external module that enables you to create [automated workflows](https://zapier.com/workflows), or “Zaps”, that can trigger actions across different platforms based on events that occur in Xperience by Kentico. For example, you can trigger actions in Zapier when a new [contact](/documentation/business-users/digital-marketing/contact-management) is created or a content item’s [workflow](/documentation/developers-and-admins/configuration/workflows) step changes. In the other direction, you can set up actions in Xperience that are triggered by “Zaps” from other applications – store external data as a [form submission](/documentation/business-users/digital-marketing/forms/manage-form-submissions) or change the workflow step of a content item.
This integration empowers users to streamline repetitive tasks, synchronize data, and improve productivity without requiring any coding knowledge.
For more information, see the [Xperience by Kentico Zapier](https://github.com/Kentico/xperience-by-kentico-zapier) GitHub repository.
**Shopify**
Allows you to connect your Shopify store with an Xperience by Kentico application using [Shopify Storefront](https://shopify.dev/docs/api/storefront) and [Shopify Admin](https://shopify.dev/docs/api/admin) APIs. The integration provides synchronization of products and e-commerce actions between the two platforms. Implemented features provide users with the options to:
  * Add, update and remove products in the shopping cart
  * Manage discount coupons
  * Proceed to checkout directly on the Shopify store page


The integration is provided through the following NuGet packages:
  * [Kentico.Xperience.Shopify](https://www.nuget.org/packages/Kentico.Xperience.Shopify)
  * [Kentico.Xperience.Shopify.Rcl](https://www.nuget.org/packages/Kentico.Xperience.Shopify.Rcl)


For more information and detailed instructions, see the [Xperience by Kentico Shopify integration](https://github.com/Kentico/xperience-by-kentico-shopify) GitHub repository.
**Kentico Xperience 13 E-commerce**
Allows you to connect a [Kentico Xperience 13 store](/13/e-commerce-features) with an Xperience by Kentico application. The integration is primarily intended to enable migration of existing Kentico Xperience 13 E-Commerce projects to Xperience by Kentico. The following e-commerce scenarios are supported:
  * Synchronization of products from Kentico Xperience 13 to content items and website channel pages in Xperience by Kentico
  * Listing products on website channel pages based on parameters, product categories, prices and inventory
  * Currency selection
  * Implementation of a shopping cart, including order creation
  * Listing of orders in the administration
  * Listing of orders on the live site for the current customer
  * Updates of existing orders


The integration is provided through the following NuGet packages:
  * [Kentico.Xperience.StoreApi](https://www.nuget.org/packages/Kentico.Xperience.StoreApi) – installed into the _Kentico Xperience 13 live site_ application.
  * [Kentico.Xperience.K13Ecommerce](https://www.nuget.org/packages/Kentico.Xperience.K13Ecommerce) – installed into the _Xperience by Kentico_ application.
  * [Kentico.Xperience.Store.Rcl](https://www.nuget.org/packages/Kentico.Xperience.Store.Rcl) – installed into the _Xperience by Kentico_ application.


For more information and detailed instructions, see the [Xperience by Kentico - Kentico Xperience 13 E-commerce](https://github.com/Kentico/xperience-by-kentico-k13ecommerce) GitHub repository.
**MiniProfiler**
An external module that integrates Xperience by Kentico applications with [MiniProfiler](https://miniprofiler.com/). This integration allows developers to optimize database queries and reduce load times.
For more information, see the [Xperience by Kentico MiniProfiler](https://github.com/Kentico/xperience-by-kentico-miniprofiler) GitHub repository.
![]()
[]()[]()
