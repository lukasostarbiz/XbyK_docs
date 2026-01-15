# Differences between SaaS and private cloud solutions
  * [ Copy page link ](documentation/developers-and-admins/saas/differences-saas-private-cloud#) | [Get HelpService ID](documentation/developers-and-admins/saas/differences-saas-private-cloud#)
Core MVC 5


[✖](documentation/developers-and-admins/saas/differences-saas-private-cloud# "Close page link panel") [Copy to clipboard](documentation/developers-and-admins/saas/differences-saas-private-cloud#)
Xperience offers two approaches to development and deployment of your projects – [private cloud solutions](documentation/developers-and-admins/deployment/deploy-to-private-cloud) and [SaaS](documentation/developers-and-admins/saas), which allows you to deploy, host, and manage Xperience by Kentico projects in a cloud environment managed by Kentico. While the features of the product remain the same, there are minor differences between the two approaches, especially in the deployment process.
|  SaaS |  Private cloud  
---|---|---  
[Installation](documentation/developers-and-admins/installation) | 
  * [Create a project](documentation/developers-and-admins/installation#create-a-project) – use the `--cloud` parameter. This will ensure you have the necessary additional configurations for the SaaS service:
    * The **Kentico.Integrations.Cloud** NuGet package
    * AppInsights and SendGrid startup configurations
    * A PowerShell script and CD configuration for generating a deployment package
    * **StorageInitializationModule.cs** file with a sample storage initialization module ([for mapping of files to Azure Blob storage](documentation/developers-and-admins/api/files-api-and-cms-io/file-system-providers/azure-blob-storage#default-kentico-managed-azure-blob-storage-configuration))
  * [Create the project database](documentation/developers-and-admins/installation#create-the-project-database)
    * `--hash-string-salt` – set the parameter to the hash salt value [found in Xperience Portal](documentation/developers-and-admins/saas/xperience-portal#hash-string-salt-for-the-xperience-portal-project)

| 
  * [Create the project database](documentation/developers-and-admins/installation#create-the-project-database)
    * `--hash-string-salt`– skip the parameter

  
[Licenses](documentation/developers-and-admins/installation/licenses) | 
  * [Tiered licenses](https://www.kentico.com/pricing) are offered for the product and various [service plans](documentation/developers-and-admins/saas/saas-service-plans) are offered for the SaaS environment. Access to various deployment environments, capacities, and performance based on the purchased plan.
  * License keys 
    * For applications deployed in all SaaS environments – automatically handled by Xperience Portal
    * [For local development](documentation/developers-and-admins/installation/licenses#license-keys-for-saas-local-development) – generated in Xperience Portal

| 
  * [Tiered license](https://www.kentico.com/pricing) offered for the product
  * License keys – Generated via <https://client.kentico.com/licenses>

  
[Configure new projects](documentation/developers-and-admins/development/website-development-basics/configure-new-projects) | 
  * Necessary to include the `UseKenticoCloud` component in the project’s middleware pipeline
  * Use [environment identification extension methods](documentation/developers-and-admins/configuration/saas-configuration#environment-identification-extension-methods) to include environment-specific configuration or code
  * Call the [required Xperience SaaS services](documentation/developers-and-admins/development/website-development-basics/configure-new-projects#configure-application-startup) when building the web application
  * Configure cache headers for [Cloudflare CDN](documentation/developers-and-admins/configuration/saas-configuration#cloudflare-cdn)

|   
[Deployment](documentation/developers-and-admins/deployment) | 
  * [Deployment in SaaS](documentation/developers-and-admins/deployment/deploy-to-the-saas-environment)
    * Cannot be deployed without the administration
    * [Create a deployment package](documentation/developers-and-admins/deployment/deploy-to-the-saas-environment#create-a-deployment-package)
    * [Upload a deployment package](documentation/developers-and-admins/deployment/deploy-to-the-saas-environment#upload-a-deployment-package-to-your-xperience-portal-project) to Xperience Portal (manually or via API)

| 
  * [Private cloud deployment](documentation/developers-and-admins/deployment/deploy-to-private-cloud)
    * Can be deployed [without the administration](documentation/developers-and-admins/deployment/deploy-to-private-cloud/deploy-without-the-administration)
    * Platform-specific process
    * For cloud-hosted deployments, important to manually [map certain project folders](documentation/developers-and-admins/deployment/deploy-to-private-cloud#map-parts-of-the-file-system-to-a-shared-storage) to shared cloud-based storage

  
[Continuous availability](documentation/developers-and-admins/deployment/read-only-deployments) | 
  * [Zero-downtime deployments](documentation/developers-and-admins/deployment/deploy-to-the-saas-environment#zero-downtime-deployments) fully managed by Kentico
  * Infrastructure (deployment slots, database replication, blob versioning) automatically configured
  * You only need to: 
    * Configure your application code to support [read-only mode](documentation/developers-and-admins/deployment/read-only-deployments)
    * Use the `-ZeroDowntimeSupportEnabled` parameter when creating deployment packages

| 
  * [Read-only deployments](documentation/developers-and-admins/deployment/read-only-deployments) require manual infrastructure configuration
  * You must configure: 
    * Two databases (production and clone for updates)
    * Three deployment slots (production, staging, and swap operations)
    * [Azure Blob storage](documentation/developers-and-admins/api/files-api-and-cms-io/file-system-providers/azure-blob-storage) with blob versioning enabled
  * Requires infrastructure expertise and [manual deployment process](documentation/developers-and-admins/deployment/read-only-deployments#deployment-process-overview)

  
[Azure Blob storage](documentation/developers-and-admins/api/files-api-and-cms-io/file-system-providers/azure-blob-storage) | 
  * Necessary project folders are [mapped by default](documentation/developers-and-admins/api/files-api-and-cms-io/file-system-providers/azure-blob-storage#default-kentico-managed-azure-blob-storage-configuration)

| 
  * Need to [add mapping for certain project folders manually](documentation/developers-and-admins/api/files-api-and-cms-io/file-system-providers/azure-blob-storage#azure-blob-storage-for-private-cloud-deployments)

  
[Website channel management](documentation/developers-and-admins/configuration/website-channel-management) | 
  * Website channels must be [added and managed](documentation/developers-and-admins/configuration/website-channel-management#website-channels-in-the-saas-environment) in [Xperience Portal](documentation/developers-and-admins/saas/xperience-portal) (in addition to the administration UI of the Xperience application itself)

| 
  * Website channels must have unique [channel domains](documentation/developers-and-admins/configuration/website-channel-management#website-channels-in-private-cloud-environments)

  
[Headless channel management](documentation/developers-and-admins/configuration/headless-channel-management) | 
  * Headless channels are NOT managed in Xperience Portal
  * The [GraphQL API endpoint](documentation/developers-and-admins/configuration/headless-channel-management#graphql-api-endpoints) is available on the system and default channel domains of any SaaS deployment that is publicly available. Keep this in mind when designing your project’s security.

|   
[Email configuration](documentation/developers-and-admins/configuration/email-configuration) | 
  * SendGrid integration is the only email sending option for SaaS – [Managed SendGrid integration](documentation/developers-and-admins/configuration/email-configuration#managed-sendgrid-integration)

| 
  * SendGrid integration – [Self-managed SendGrid integration](documentation/developers-and-admins/configuration/email-configuration#self-managed-sendgrid-integration)
  * Other available options – [External SMTP servers](documentation/developers-and-admins/configuration/email-configuration#smtp-servers) or [custom email clients](documentation/developers-and-admins/customization/email-customization#custom-email-client)

  
[Email channel management](documentation/developers-and-admins/digital-marketing-setup/email-channel-management) | 
  * Email channels must be [added and managed](documentation/developers-and-admins/digital-marketing-setup/email-channel-management#email-channels-in-the-saas-environment) in [Xperience Portal](documentation/developers-and-admins/saas/xperience-portal) (in addition to the administration UI of the Xperience application itself)
  * You can view [email sending statistics](documentation/developers-and-admins/deployment/deploy-to-the-saas-environment/manage-saas-deployments#email-metrics) in the [Xperience Portal](documentation/developers-and-admins/saas/xperience-portal) to help you identify deliverability issues

| 
  * Sending and service domains of email channels can be [configured programmatically](documentation/developers-and-admins/digital-marketing-setup/email-channel-management#configure-sending-and-service-domains-programmatically)

  
[Auto-scaling](documentation/developers-and-admins/configuration/auto-scaling-support) | 
  * No configuration necessary
  * When the load increases to peak levels in the production environment, the system temporarily adds one additional instance to ensure load balancing.
  * One auto-scaling instance is available for all license tiers. Additional instances can be purchased as a part of a custom service plan.

| 
  * [Private cloud hosting](documentation/developers-and-admins/configuration/auto-scaling-support#private-cloud-hosting) using third-party providers
  * [Private cloud on-premise hosting](documentation/developers-and-admins/configuration/auto-scaling-support#private-on-premise-hosting) using multiple application instances and a shared database

  
[Update Xperience by Kentico projects](documentation/developers-and-admins/installation/update-xperience-by-kentico-projects) | 
  * After updating locally you need to create a new deployment package

|   
[Use Xperience by Kentico API externally](documentation/developers-and-admins/api/use-the-xperience-by-kentico-api-externally) | 
  * Not available for SaaS
  * Supported for locally used external tools, such as tools used for data import or migration

|