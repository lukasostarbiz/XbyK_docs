---
source: https://docs.kentico.com/documentation/developers-and-admins/saas/saas-overview
scrape_date: 2026-01-22
---

  * [Home](/documentation)
  * [Developers and admins](/documentation/developers-and-admins)
  * [SaaS](/documentation/developers-and-admins/saas)
  * SaaS overview 


# SaaS overview
Kentico’s [Software as a Service (SaaS)](/documentation/developers-and-admins/saas) provides infrastructure and supporting services that allow you to deploy, host and manage Xperience by Kentico projects in the cloud. On this page you can find answers to some questions you may ask when considering the SaaS service. If you have a question that is not covered on this page, reach out to your sales representative or [contact our Sales team](https://www.kentico.com/how-to-buy/contact-sales) if you are a new client.
## What is Xperience by Kentico SaaS?
Xperience by Kentico SaaS offers hosting, infrastructure and other services that allow digital agencies and system integrators to offload the burden of managing these services, and instead, focus their attention, time and effort on developing digital experiences that attract revenue to their business.
Xperience by Kentico SaaS leverages Azure components, selected third-party services, and our own management and automation capabilities to provide you with operational control of your cloud environments and your DXP. For example, you can easily set up and manage multiple digital content delivery channels ([website](/documentation/developers-and-admins/configuration/website-channel-management), [email](/documentation/developers-and-admins/digital-marketing-setup/email-channel-management) and [headless](/documentation/developers-and-admins/configuration/headless-channel-management)).
Support for headless scenarios combined with channel management capabilities within the DXP ensures marketers can leverage their DXP as a content service when and where it is required, and access the administration interface of the DXP directly from their browser, no matter where the marketers are working from.
## What does Kentico handle and what are your responsibilities?
Kentico manages and maintains the [SaaS environment](/documentation/developers-and-admins/deployment/deploy-to-the-saas-environment/manage-saas-deployments), [Xperience Portal](/documentation/developers-and-admins/saas/xperience-portal), the [deployment APIs](/documentation/developers-and-admins/deployment/deploy-to-the-saas-environment), monitors and ensures 24/7/365 availability of the service, and applies updates automatically to all SaaS infrastructure and related services. However, Kentico is not responsible for developing and deploying your Xperience by Kentico project to the SaaS environment on your behalf, nor for applying updates (hotfixes, refreshes) to your project.
[![SaaS responsibilities](/docsassets/documentation/saas-overview/saas-responsibilities.png)](/docsassets/documentation/saas-overview/saas-responsibilities.png)
You may choose to apply [updates](/documentation/changelog) (refresh or hotfix) at any time suitable for you, but we recommend regularly updating your project to the latest version, which ensures the platform is secure and all new features are available. All refreshes are included in the price of the [subscription](/documentation/developers-and-admins/installation/licenses) for the duration of the subscription period.
If, for some compelling reason, you are unable to regularly update to the latest version, at the minimum you need to keep your project within the [range of supported releases](/documentation/developers-and-admins/installation/support-policy). We cannot guarantee that deployment to the SaaS environment will be possible for older projects with an unsupported version.
## What security measures are taken by Kentico?
Kentico’s SaaS adheres to our company policy of enterprise-grade security. As an [ISO27001](https://www.iso.org/standard/27001) certified company, security is as important to us as it is to you.
All customer data is stored within the customer’s designated single tenant account in the region they specified prior to service provisioning. Within our European data center, we store data in the [Xperience Portal](/documentation/developers-and-admins/saas/xperience-portal) database such as username/email (for Xperience Portal [users](/documentation/developers-and-admins/saas/xperience-portal#users), not [users](/documentation/developers-and-admins/configuration/users/user-management) in your Xperience by Kentico project), and project name. We also store data in [Auth0](%60https://auth0.com/) (EU), including Multi-Factor Authentication (MFA). Logs from pipeline runs and Azure functions are also stored in Europe (including but not limited to custom domain names).
Other security measures include [Web Application Firewall](https://en.wikipedia.org/wiki/Web_application_firewall) (WAF), key management using the [Azure Key Vault](https://azure.microsoft.com/en-us/products/key-vault), encrypting all data in transit and when stored, DDoS protection, and secure access to the Xperience Portal using Multi-Factor Authentication (MFA).
For a list of all compliance certifications and an overall security overview, visit the [Kentico Trust Center](https://trust.kentico.com/).
## Where is the SaaS service available?
Xperience by Kentico SaaS service supports the following [Azure regions](https://azure.microsoft.com/en-au/explore/global-infrastructure/geographies/):
  * East US
  * East US 2
  * West US
  * West US 2
  * North Central US
  * Canada Central
  * Canada East
  * West Europe
  * North Europe
  * Germany West Central
  * Switzerland North
  * UK South
  * UK West
  * East Asia
  * Southeast Asia
  * Japan East
  * Japan West
  * Australia East
  * Australia Southeast
  * UAE North


New [Azure regions](https://azure.microsoft.com/en-au/explore/global-infrastructure/geographies/) are tested and evaluated regularly. If you want to inquire about a region that is not on the list, you can get in touch with your sales representative or [contact our Sales team](https://www.kentico.com/how-to-buy/contact-sales) if you are a new client.
## What happens in case of a disaster?
Kentico’s SaaS offering is designed for 99.9% uptime. Kentico’s SaaS service supports [redundancy](https://learn.microsoft.com/en-us/azure/azure-sql/database/high-availability-sla-local-zone-redundancy?view=azuresql&tabs=azure-powershell#general-purpose-service-tier-zone-redundant-availability) for database backups and storage, where [backups](/documentation/developers-and-admins/deployment/deploy-to-the-saas-environment/manage-saas-deployments#restore-points-and-exports) are stored on local disks within the data center and copied to a remote storage location. In the event of a major data center disaster, Kentico will use the backups from the remote storage location to return your environment to service as quickly as possible. The simplicity of our single tenant service means that our return to service times are frequently faster than those of a multi-tenanted service.
Kentico’s SaaS service leverages the high availability and reliability of [Azure SQL Database](https://learn.microsoft.com/en-us/azure/azure-sql/database/sql-database-paas-overview?view=azuresql). A set of spare nodes is allocated to the service if a failover of the primary node happens. This is done to minimize failover time and does not affect the data in the Azure storage layer.
Database and Blob Storage services for the production and staging environments are backed up daily, as are any on-demand [backups](/documentation/developers-and-admins/deployment/deploy-to-the-saas-environment/manage-saas-deployments#restore-points-and-exports) or [backups](/documentation/developers-and-admins/deployment/deploy-to-the-saas-environment/manage-saas-deployments#restore-points-and-exports) taken as part of the deployment process to a production or staging environment. Daily backups are kept for 3 to 7 days, depending on the [service plan](/documentation/developers-and-admins/saas/saas-service-plans) you have selected. Weekly backups are taken on Sundays and retained for 30 days. Xperience Portal users are also able to create full or partial backups on demand for all environments via the portal’s user interface.
You can monitor current state of the SaaS service and view recent outages on the [Xperience by Kentico Status](https://status.xperience-portal.com/) page.
## How does the SaaS service ensure performance?
Proper performance of your Xperience by Kentico platform is ensured by **auto-scaling** and a **CDN**.
Our **auto-scaling** infrastructure implements up/down horizontal auto-scaling of additional Xperience instances to address intermittent scaling needs. Load and resource utilization is measured in order to optimize load balancing based on traffic and activity. The service automatically scales back down when the workload reduces. The scaling is intended to ensure sufficient performance if the demand exceeds expected and allocated resources. Customers who utilize the scaling regularly are encouraged to upgrade to a higher service plan level that provides multiple instances.
A **content delivery network (CDN)** is a geographically distributed network of proxy servers and data centres. It provides high availability and performance by distributing the content service spatially relative to your website visitors. It also deploys smart dynamic routing to select the most optimal route for delivering content to your customers in order to reduce rendering times. All service levels of the SaaS service include CDN as a standard feature.
## How is the SaaS service supported by Kentico?
Our standard [Technical Support](https://www.kentico.com/services/support) offers unlimited 24/7 support to all clients with a valid Xperience by Kentico subscription. SaaS infrastructure alerts are triggered before the customer notices any disruption to their service in the majority of cases. The support team’s internal target response time to any infrastructure incident alert is 1 hour, within which the [Xperience by Kentico Status](https://status.xperience-portal.com/) page will also be updated, and our development team has a target to start fixing any identified issues within two hours after the alert is triggered or reported by the Technical Support team. The time it takes to completely resolve the issue depends on many factors and cannot be guaranteed.
## How can I monitor my project?
See [Manage SaaS deployments – Application monitoring](/documentation/developers-and-admins/deployment/deploy-to-the-saas-environment/manage-saas-deployments#application-monitoring) for more information about monitoring your project.
![]()
[]()[]()
