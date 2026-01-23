---
source: https://docs.kentico.com/guides/configuration/saas/saas-comparison
scrape_date: 2026-01-22
---

  * [Home](/guides)
  * [Configuration](/guides/configuration)
  * [SaaS](/guides/configuration/saas)
  * Private cloud vs. Kentico SaaS - The deployment journey 


# Private cloud vs. Kentico SaaS - The deployment journey
Choosing the right deployment model for Xperience by Kentico is an important decision. This page offers a side-by-side comparison of [Kentico SaaS](/documentation/developers-and-admins/saas) and [private cloud solutions](/documentation/developers-and-admins/deployment/deploy-to-private-cloud). It focuses on real deployment tasks and troubleshooting, building on top of our [feature comparison page](/documentation/developers-and-admins/saas/differences-saas-private-cloud).
You’ll find actionable insights to help you select the best hosting approach, understand the benefits of Kentico SaaS, and streamline your deployment process.
We structured the material as a table comparison of deployment tasks, where each task is clickable. You can find more detailed explanations and guidance for each task below the table, to dive deeper into any area of interest.
## Resources
This comparison was created in collaboration with our Kentico MVPs, drawing on their real-world experience and insights. We reference the following MVP blog posts throughout this material—feel free to explore them for additional context and practical tips:
  * [Say “yes” to Xperience by Kentico SaaS and “no” to nightmares](https://community.kentico.com/blog/say-yes-to-xperience-saas), _by Mike Wills_ (May 2025)
  * [The Kentico SaaS Migration: A Step-by-Step Guide](https://www.goldfinch.me/blog/the-kentico-saas-migration-a-step-by-step-guide), _by Liam Goldfinch_ (April 2025)
  * [The Kentico SaaS Migration: Automated Deployments](https://www.goldfinch.me/blog/the-kentico-saas-migration-automated-deployments), _by Liam Goldfinch_ (April 2025)
  * [The Kentico SaaS Migration: Lessons Learned and Troubleshooting Tips](https://www.goldfinch.me/blog/the-kentico-saas-migration-lessons-learned-and-troubleshooting-tips), _by Liam Goldfinch_ (April 2025)
  * [Kentico SAAS: Pros and Cons vs Self Hosting](https://www.silvertech.com/website-portal-development/hosting-managed-services/kentico-saas--pros-and-cons-vs-self-hosting), _by Derek Barka_ (September 2025)


Some of these blog posts mention “upcoming” and “roadmap” features that Kentico has since released.
## Table overview
Deployment area |  Private cloud (e.g., Azure) |  Kentico SaaS  
---|---|---  
[Infrastructure setup](#infrastructure-setup) |  You design and deploy infra (Azure, AWS, etc.). Must handle networks, certs, access, scaling, and monitoring. |  Fully hosted by Kentico. [Preconfigured environments](/documentation/developers-and-admins/deployment/deploy-to-the-saas-environment) (QA, UAT, STG, PROD). Scalable, monitored, and secure.  
[Initial deployment](#initial-deployment) |  Manual work to push code, configure CMS, run migrations. |  Easy to upload deployment package via Xperience Portal. CMS and DB come preconfigured.  
[CI/CD pipeline setup](#cicd-pipeline-setup) |  Create GitHub Actions or Azure DevOps pipelines for each environment. Must integrate testing, secrets, infra provisioning. |  Kentico .NET cloud templates come with sample default configuration for CD and Xperience Portal, reducing custom setup.  
[UAT and Staging environments](#uat-and-staging-environments) |  You create and maintain your own UAT and Staging environments. You manage coordination between editors, developers, and testers in terms of content edits. |  Preview (QA, UAT) and Staging (STG) are built-in (depending on your license). The Portal supports seamless [deployment flow](/documentation/developers-and-admins/deployment/deploy-to-the-saas-environment#deploy-the-package-to-another-deployment-environment) from one environment to another.  
[Content synchronization](#content-synchronization) |  Additional configuration required to [set up content sync](/documentation/developers-and-admins/configuration/content-sync-configuration#set-up-content-sync). |  Xperience Portal automatically handles [content sync for Kentico SaaS](/documentation/developers-and-admins/configuration/content-sync-configuration#content-sync-for-saas-environments).  
[Automated testing](#automated-testing) |  You manually integrate tests and manage environment resets. |  Include GitHub workflows and automated testing steps in your deployment pipeline using [Xperience Portal API](/guides/configuration/saas/saas-walkthrough/saas-update-with-xperience-portal-api).  
[Go live](#go-live) |  Manual deployment, DNS, final checks, and backups. |  Promote to PROD from Portal. DNS and infra managed by Kentico.  
[Post-go-live operations](#post-go-live-operations) |  You monitor uptime, apply patches, handle scaling and logs. |  Kentico handles observability, scaling, platform patches, and backups.  
[Troubleshooting](#troubleshooting) |  You debug across cloud logs, services, and app monitoring. |  Xperience Portal gives access to logs, deployment results, and rollback.  
[Configuration](#configuration) |  You store secrets in Key Vault or config files and handle configuration for each environment. |  Managed via GitHub and the Xperience Portal.  
[Media & image handling](#media-image-handling) |  You configure blob storage, CDN, image rules manually. |  Default Azure blob storage configuration included in Kentico .NET template. Media files deployment happens automatically.  
## Deployment area details
### Infrastructure setup
#### Private cloud
  * You are responsible for designing and deploying all infrastructure (Azure, AWS, etc.).
  * You must handle network configuration, SSL certificates, access control, scaling, and monitoring.
  * For each cloud service you use, you must learn the best practices for security and cost management.
  * Each environment (e.g., dev, staging, prod) requires manual setup and ongoing maintenance.


_“Our typical self-managed Xperience projects run in Azure tenants. Production uses the most robust infrastructure, QA mirrors production to validate every layer, and Dev stays intentionally simple.”_
— [Mike Wills, Say Yes to SaaS](https://community.kentico.com/blog/say-yes-to-xperience-saas#private-cloud-environments)
_“My site currently lives on a Rackspace shared server with other internal IDHL projects, while the database is already hosted in Microsoft Azure.”_
— [Mike Wills, Say Yes to SaaS](https://community.kentico.com/blog/say-yes-to-xperience-saas#private-cloud-environments)
#### Kentico SaaS
  * Kentico fully hosts and manages the whole infrastructure.
  * Preconfigured environments are provided (QA, UAT, STG, PROD, Custom - based on your license).
  * Kentico handles scalability, monitoring, and security.
  * You focus on application development and configuration, not infrastructure.


  * If you need a custom environment or a plan, coordinate with Kentico early.
  * Assign team roles in the Xperience Portal for secure access.
  * Set up the Xperience Portal’s built-in notifications.


_“Kentico has kindly provided a Level 1 SaaS instance which has two environments, a non-production (QA) and production (PROD) environment.”_
— [Liam Goldfinch, A Step-by-Step Guide](https://www.goldfinch.me/blog/the-kentico-saas-migration-a-step-by-step-guide)
### Initial deployment
#### Private cloud
  * You have to manage the process to push code, configure the CMS, and run database migrations, whether manual or automated.
  * Deployment scripts or pipelines must be created and maintained.
  * Each environment may require separate configuration and validation.
  * There’s a higher risk of human error during any manual steps.


_“We rely on Azure DevOps multi-stage pipelines together with Xperience Continuous Integration (CI) and Continuous Deployment (CD).”_
— [Mike Wills, Say Yes to SaaS](https://community.kentico.com/blog/say-yes-to-xperience-saas#private-cloud-deployments)
#### Kentico SaaS
  * Deployment packages can be easily uploaded via the Xperience Portal.
  * The CMS and database come preconfigured for you.
  * The Xperience Portal provides deployment logs and status for easy verification.


  * Watch your deployment progress in the Xperience Portal.
  * If a deployment fails, review the Portal logs for error details.


_“The main change is to ensure that the artifact created after a Dev deployment matches the SaaS deployment-package format, ready for upload through the Xperience Portal, and deployment with a click.”_
— [Mike Wills, Say Yes to SaaS](https://community.kentico.com/blog/say-yes-to-xperience-saas#saas-deployments)
### CI/CD pipeline setup
#### Private cloud
  * You must create and configure CI/CD pipelines (e.g., GitHub Actions, Azure DevOps) for each environment.
  * Pipelines must integrate testing, secrets management, and infrastructure provisioning.
  * Your scripts require ongoing maintenance and troubleshooting.
  * Security and permissions must be managed for each pipeline.


_“Before migrating to SaaS, my existing CI/CD flow was built around deploying to a self-hosted environment.”_
— [Liam Goldfinch, Automated Deployments](https://www.goldfinch.me/blog/the-kentico-saas-migration-automated-deployments)
#### Kentico SaaS
  * By default, Kentico’s .NET templates for cloud include configuration for CD and Xperience Portal.
  * Our SaaS reduces the need for custom setup and accelerates pipeline creation.
  * Xperience Portal and Xperience Portal API simplify deployment and environment management.


  * Take advantage of the default CD configuration that comes with a [Kentico .NET cloud template](/guides/configuration/saas/saas-walkthrough/saas-install-configure-project#install-saas-ready-project).
  * Utilize the [Xperience Portal API](/guides/configuration/saas/saas-walkthrough/saas-update-with-xperience-portal-api) to include any automated tasks in your pipeline.
  * Watch your deployment progress in the Xperience Portal.


_“…Xperience SaaS takes over with a robust process, deploying application files, applying Xperience refreshes, synchronizing the CD package, and creating automatic backups of SQL, storage, and app files.”_
— [Mike Wills, Say Yes to SaaS](https://community.kentico.com/blog/say-yes-to-xperience-saas#saas-deployments)
### UAT and Staging environments
#### Private cloud
  * You must create, maintain and configure UAT and Staging environments, duplicating infrastructure.
  * Provisioning and deprovisioning resources is time-consuming.


#### Kentico SaaS
  * Preview (QA, UAT) and [Staging](/documentation/developers-and-admins/configuration/content-sync-configuration#content-staging-best-practices) environments are built-in dedicated spaces in the Xperience Portal.
  * The Portal supports seamless deployment flow from one environment to another.
  * You can assign appropriate access to UAT testers and content editors through the Xperience Portal.


Developers can take advantage of the [environment identification extension methods](/documentation/developers-and-admins/configuration/saas-configuration#environment-identification-extension-methods) to run certain code only in a specific environment.
### Content synchronization
#### Private cloud
  * You must configure content sync and blob storage.
  * You get full control but also greater responsibility and complexity.


_“At present, customers [can] edit content directly in Production using Xperience publishing workflow and preview features. Kentico’s … Content Synchronization feature [allows] a separate authoring tier”_
— [Mike Wills, Say Yes to SaaS](https://community.kentico.com/blog/say-yes-to-xperience-saas#private-cloud-environments)
#### Kentico SaaS
  * You do not need to configure content sync manually (except for local development environments).
  * Content synchronization connections are defined and preconfigured in the Xperience Portal.
  * You still have control and an option to adjust the default configuration.


See the [Content sync documentation](/documentation/developers-and-admins/configuration/content-sync-configuration).
### Automated testing
#### Private cloud
  * Integrate automated tests into your pipeline.
  * Environment resets and test data management are your responsibility.


#### Kentico SaaS
  * Like with private cloud, you can include GitHub workflows and automated testing steps in your deployment pipeline using the Xperience Portal API.
  * The Xperience Portal provides deployment checks and logs for test results.


_“The core CI/CD principles hadn’t changed, I was still building, testing, packaging, but instead of deploying to a self-hosted server, I was using Xperience Portal’s deployment API endpoint.”_
— [Liam Goldfinch, Automated Deployments](https://www.goldfinch.me/blog/the-kentico-saas-migration-automated-deployments)
### Go live
#### Private cloud
  * You are responsible for coordinating all go-live activities and communication.
  * Rollback and recovery plans must be in place.
  * [Zero-downtime deployment](/documentation/developers-and-admins/deployment/read-only-deployments#private-cloud) requires multiple databases, deployment slots, blob storage, and read-only support in code.


#### Kentico SaaS
  * Promoting to PROD is managed through the Xperience Portal.
  * DNS and infrastructure are handled by Kentico.
  * Xperience Portal provides status and rollback options with [automatic restore points](/documentation/developers-and-admins/deployment/deploy-to-the-saas-environment/manage-saas-deployments#automatic-restore-points-and-exports).
  * The SaaS environment orchestrates [Zero-downtime deployment](/documentation/developers-and-admins/deployment/deploy-to-the-saas-environment#zero-downtime-deployments) when you generate deployment packages with `-ZeroDowntimeSupportEnabled` parameter and make sure your code supports read-only mode.


_“You can now use the Deploy to button to promote the same package to the Production environment.”_
— [Liam Goldfinch, A Step-by-Step Guide](https://www.goldfinch.me/blog/the-kentico-saas-migration-a-step-by-step-guide)
### Post-go-live operations
#### Private cloud
  * You apply patches, handle scaling, and manage logs.
  * Configuring monitoring tools is your responsibility for each environment.
  * Depending on your provider, you might have to manually set up some email notifications and alerts.


#### Kentico SaaS
  * The Xperience Portal conveniently consolidates monitoring tools under “one roof.”
  * Kentico handles observability, scaling, platform patches, and backups.
  * You can set up automated notifications through the Portal.


**Optimize and automate/Automate monitoring and backups**
  * Set up [automated notifications](/guides/configuration/saas/saas-walkthrough/saas-setup#set-up-notifications).
  * Utilize automatic and manual [restore points and exports](/documentation/developers-and-admins/deployment/deploy-to-the-saas-environment/manage-saas-deployments#restore-points-and-exports) for frequent backups.


_“The platform automatically backs up databases and files before deployments, supports manual backups, and can download backups for refreshing local dev environments or debugging issues. You can also restore the QA and Production environments right from the portal.”_
— [Derek Barka, Pros and Cons](https://www.silvertech.com/website-portal-development/hosting-managed-services/kentico-saas--pros-and-cons-vs-self-hosting)
### Troubleshooting
#### Private cloud
  * You debug issues across cloud logs, services, and application monitoring tools.
  * Root cause analysis and resolution are your responsibility.
  * You need to maintain documentation of common issues and fixes.


_“Full infrastructure access delivers control but also significant burden: DNS records, certificates, VNET routes, WAF rules, storage keys, bindings, SQL restrictions, and upgrades. All can fail and demand deep expertise to fix in an emergency.”_
— [Mike Wills, Say Yes to SaaS](https://community.kentico.com/blog/say-yes-to-xperience-saas#private-cloud-troubleshooting)
#### Kentico SaaS
  * Xperience Portal provides access to logs, deployment results, and backup and rollback features.
  * Deployment history helps with root cause analysis and recovery.


_“Teams sometimes fear losing low-level access, but we retain what we need while facing far fewer failure points. … We have all the access we need, have much less to worry about, and avoid risky, ad-hoc fixes.”_
— [Mike Wills, Say Yes to SaaS](https://community.kentico.com/blog/say-yes-to-xperience-saas#saas-troubleshooting)
  * Check out the [troubleshooting tips](https://www.goldfinch.me/blog/the-kentico-saas-migration-lessons-learned-and-troubleshooting-tips) and lessons learned from Liam Goldfinch’s experiments.
  * Visit our [FAQ documentation page](/documentation/developers-and-admins/saas/saas-frequently-asked-questions).


### Configuration
#### Private cloud
  * You are responsible for managing each environment to ensure consistency.


_“The release pipeline picks up the artifact, extracts it onto the shared server, ensures IIS is configured with the correct bindings, and applies environment specific configuration transformations. As this part of the pipeline will be completely replaced, I won’t be spending time covering it here.”_
— [Liam Goldfinch, Automated Deployments](https://www.goldfinch.me/blog/the-kentico-saas-migration-automated-deployments)
#### Kentico SaaS
  * The Portal ensures secure and consistent configuration in many aspects, while the provided extensions enable seamless integration of environment-specific code.


### Media & image handling
#### Private cloud
  * You must configure blob storage, CDN, and image rules manually.
  * Ongoing maintenance and optimization are required.
  * Media delivery and performance depend on your setup.


#### Kentico SaaS
  * Default Azure blob storage configuration is included in the Kentico .NET template.
  * Deployment and media delivery are handled automatically.
  * Built-in CDN and image optimization improve performance out of the box.


If images fail to load, verify your [`StorageInitializationModule` configuration](/guides/configuration/saas/saas-walkthrough/saas-install-configure-project#azure-blob-storage-folder-mapping).
_“Next, I came across the StorageInitializationModule.cs file, which contains sample code for initializing Azure Blob Storage. I made sure to include this in my project!”_
— [Liam Goldfinch, Step by step](https://www.goldfinch.me/blog/the-kentico-saas-migration-a-step-by-step-guide)
## Let us know what you think
We are continuously working on improving our materials. Are you missing any information or need further explanation on any aspect of our SaaS? Would you like to contribute to this guide by sharing your own SaaS deployment experience? Use the **Send us feedback** button at the end of this page to let us know.
![]()
[]()[]()
