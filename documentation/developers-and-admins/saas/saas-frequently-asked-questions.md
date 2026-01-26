---
source: https://docs.kentico.com/documentation/developers-and-admins/saas/saas-frequently-asked-questions
scrape_date: 2026-01-26
---

  * [Home](/documentation)
  * [Developers and admins](/documentation/developers-and-admins)
  * [SaaS](/documentation/developers-and-admins/saas)
  * SaaS - Frequently asked questions 


# SaaS - Frequently asked questions
This page provides answers to frequently asked questions (FAQ) and highlights common issues developers may encounter when developing projects for SaaS deployments.
## What to do when starting with Xperience by Kentico SaaS?
You have just received an invitation to an Xperience by Kentico SaaS project. What steps should you take to get started with SaaS development? What materials about SaaS are available?
The following materials provide guidance for users who first encounter the Xperience by Kentico SaaS:
  * [SaaS](/guides/configuration/saas) guides will help you navigate the necessary steps to access your Xperience Portal project for the first time and enable your team to work on it.
  * [SaaS](/documentation/developers-and-admins/saas) page in the documentation provides links to all documentation materials relating to SaaS.
  * The following materials contain useful information. However, they are not part of the official documentation and therefore may contain information that is not up-to-date with the latest Xperience releases: 
    * [Xperience by Kentico CI/CD developer scenarios](https://community.kentico.com/blog/xperience-by-kentico-ci-cd-developer-scenarios) blog post by Sean Wright
    * [Xperience by Kentico SaaS best practices](https://community.kentico.com/blog/xperience-by-kentico-saas-best-practices) blog by Sean Wright
    * [Kentico’s Xperience Portal and SaaS: From Setup to Production](https://www.milanlund.com/knowledge-base/kentico-xperience-portal-and-saas-from-setup-to-production) article by Milan Lund
    * [Handling Search Index Rebuilds in Xperience by Kentico’s SaaS Environment](https://www.milanlund.com/knowledge-base/handling-search-index-rebuilds-in-xperience-by-kentico-s-saas-environment) article by Milan Lund
    * [Mastering serialization at Xperience by Kentico](https://konabos.com/blog/mastering-serialization-at-xperience-by-kentico) blog post by Lukasz Skowroński
    * [How to migrate content from PROD to QA within your SaaS Xperience by Kentico?](https://konabos.com/blog/how-to-migrate-content-from-prod-to-qa-within-your-saas-xperience-by-kentico) blog post by Lukasz Skowroński
    * [Set Up Xperience by Kentico on Docker in Minutes](https://konabos.com/blog/set-up-xperience-by-kentico-on-docker-in-minutes) blog post by Lukasz Skowroński


## How does Xperience by Kentico SaaS manage certificates and CDN for my project?
All SSL/TLS certificates for all domains are provided and automatically renewed by the platform. Certificates are automatically generated when validating a domain and are ready a few minutes after the validation is completed. CloudFlare CDN is provided for all projects, and it is not possible to use your own CDN.
Customers cannot use custom digital certificates or custom CDNs with Xperience by Kentico SaaS. The SaaS environment manages certificates and CDN configuration for all tenants.
## What can you do to troubleshoot your project?
Your application has encountered an unexpected issue. Where can you look to troubleshoot the issue and find the cause of the problem?
There are multiple tools to help you troubleshoot issues with the SaaS solution:
  * Check the [Event log](/documentation/developers-and-admins/configuration/event-log) in the Xperience administration interface.
  * In the [Xperience Portal](/documentation/developers-and-admins/saas/xperience-portal): 
    * Check the [Deployment history](/documentation/developers-and-admins/deployment/deploy-to-the-saas-environment#upload-a-deployment-package-to-your-xperience-portal-project) section in the **Deployments** application and download the deployment logs. 
      * Note that deployments take a moment to be listed in the deployment history from when they are started.
    * Check the [event log and exceptions](/documentation/developers-and-admins/deployment/deploy-to-the-saas-environment/manage-saas-deployments#event-log-and-exceptions) for any unexpected issues.
    * Check the [performance metrics](/documentation/developers-and-admins/deployment/deploy-to-the-saas-environment/manage-saas-deployments#performance-metrics) for any recent spikes in resource usage.
    * You can [restart the deployed application](/documentation/developers-and-admins/deployment/deploy-to-the-saas-environment/manage-saas-deployments#restart-deployed-applications) to see if this resolves the issue.


Finally, you can check the current status of the Xperience SaaS infrastructure on the [Xperience by Kentico Status](https://status.xperience-portal.com/) page.
## How can you troubleshoot performance issues of the live site?
Your live site has performance issues and you are trying to find out what is the bottleneck causing the issues.
In [Xperience Portal](/documentation/developers-and-admins/deployment/deploy-to-the-saas-environment/manage-saas-deployments#performance-metrics), navigate to **Monitoring → Project metrics** , where you can see various information about the performance of your application in a specified time frame. You can identify which part of the application is causing the bottleneck and proceed accordingly. You can either optimize your project or, if the issue is the amount of traffic, upgrade your [service plan](/documentation/developers-and-admins/saas/saas-service-plans).
Additionally, you can [enable email notifications](/documentation/developers-and-admins/deployment/deploy-to-the-saas-environment/manage-saas-deployments#alert-email-notifications) to alert you when performance metrics of your project are on the limit of your [service plan](/documentation/developers-and-admins/saas/saas-service-plans). See [Alerts](/documentation/developers-and-admins/deployment/deploy-to-the-saas-environment/manage-saas-deployments#alerts) for more information.
Finally, you can check the current status of the Xperience SaaS infrastructure on the [Xperience by Kentico Status](https://status.xperience-portal.com/) page.
## How to resolve database inconsistencies on SaaS environments?
Database inconsistencies may be introduced for various reasons (custom development, integration and migration scenarios) to databases of SaaS projects, but developers do not have direct access to run SQL scripts on databases of applications deployed in SaaS environments.
You can use [CD migration scripts](/documentation/developers-and-admins/ci-cd/ci-cd-database-migration-scripts) to perform SQL queries on the database of applications deployed in [SaaS environments](/documentation/developers-and-admins/deployment/deploy-to-the-saas-environment#deploy-the-package-to-another-deployment-environment). However, remember to always [create a restore point](/documentation/developers-and-admins/deployment/deploy-to-the-saas-environment/manage-saas-deployments#create-a-manual-restore-point) before modifying the database.
If the previous solution does not work for you, you can also leverage [custom restore](/documentation/developers-and-admins/deployment/deploy-to-the-saas-environment#deploy-a-large-amount-of-data-during-initial-deployments) to resolve database inconsistencies. Custom restore requires uploading a whole exported database that will be swapped with the one currently in use. You can obtain the deployed database through an [export](/documentation/developers-and-admins/deployment/deploy-to-the-saas-environment/manage-saas-deployments#create-a-manual-export). Review whether this option suits your needs, as it causes downtime in the target environment and the changes cannot be propagated to other environments without using custom restore for each environment.
## How to resolve broken deployment package in the Production environment?
You may encounter an issue where you deploy a package to the SaaS environment and promote it to the _Production_ environment before realizing that the package is faulty and causes issues on your live site.
There are two options how to handle this situation:
  * You can [restore a previous restore point](/documentation/developers-and-admins/deployment/deploy-to-the-saas-environment/manage-saas-deployments#restore-from-a-restore-point) of your application directly from the Xperience Portal interface. The system automatically creates backups before each deployment. Note that you may lose content created between the faulty deployment and the time when you choose to restore the backup.
  * You can create a new deployment package that fixes the previous issue if possible. The advantage is that all recently created content is preserved. This approach works best when using smaller and more frequent deployments which enable you to identify the cause of the issue.


## What is the user limit in Xperience Portal?
A common misunderstanding is that when using the Xperience SaaS there is a limit of 20 users for the entire solution, including the Xperience administration interface. The user limit only applies to the users in the [Xperience Portal](/documentation/developers-and-admins/saas/xperience-portal). The number of users in the administration interface of your Xperience application is not limited.
## How to obtain license keys for local development?
Why can you not create license keys in the client portal and where should you create your license keys?
License keys for SaaS development are generated in the **License key generator** application in the [Xperience Portal](/documentation/developers-and-admins/saas/xperience-portal).
See [Licenses](/documentation/developers-and-admins/installation/licenses#license-keys-for-saas-local-development) for more information.
## What Xperience by Kentico versions are supported in SaaS?
The minimum Xperience by Kentico version for new projects that can be deployed to the SaaS environment is **30.1.0**. If you are using an earlier version, you need to [update](/documentation/developers-and-admins/installation/update-xperience-by-kentico-projects) your project to the latest version before deploying it to the SaaS environment. Existing projects are not affected, but we recommend updating them to the latest version.
![]()
[]()[]()
