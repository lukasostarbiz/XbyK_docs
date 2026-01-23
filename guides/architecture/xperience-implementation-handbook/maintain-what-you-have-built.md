---
source: https://docs.kentico.com/guides/architecture/xperience-implementation-handbook/maintain-what-you-have-built
scrape_date: 2026-01-22
---

  * [Home](/guides)
  * [Architecture](/guides/architecture)
  * [Xperience implementation handbook](/guides/architecture/xperience-implementation-handbook)
  * Maintain what you have built 


# Maintain what you have built
## Maintaining
Analyze the data you gather from your project to determine what actions to take. Focus on optimizations of content and performance.
### Environment maintenance
  * Define a backup strategy (e.g., database file system backups and a disaster restore / recovery plan).
  * Perform SQL maintenance (e.g., shrink the transaction log, rebuild indexes).
  * Plan outages and take necessary steps to notify the appropriate people.


### Application maintenance
  * Review and address performance issues [using tools like MiniProfiler](https://dev.to/michael419/get-to-know-xperience-by-kentico-unlocking-performance-insights-with-miniprofiler-2b2c).
  * Keep software patched and up-to-date, [including Xperience](/documentation/developers-and-admins/installation/update-xperience-by-kentico-projects), the operating system, SQL server, etc. 
    * if you [prepare your code for read-only mode](/documentation/developers-and-admins/deployment/read-only-deployments#required-project-changes), you can use [zero-downtime deployments](/documentation/developers-and-admins/deployment/read-only-deployments) when you update Xperience by Kentico.


### Data maintenance
  * Archive old data that is no longer needed.
  * Remove unnecessary objects (e.g., unused media assets, unused reusable content items, inactive contacts, unsent emails, old contact activities, etc.).
  * Update and optimize content (e.g., update outdated content, fix broken links, etc.).
  * Deploy new content and changes from a staging environment to keep the production environment clear of unfinished drafts.


### Scenario
#### Large database causing performance issues
A client was utilizing nearly all available features.
Since they did not have any data monitoring or a maintenance strategy, the size of the database was growing rapidly (100+GB) and started slowing down the system. Eventually, the added load exceeded the capabilities of their environment and the site went down.
In this case, the project was a large website with hundreds of thousands of pages, and over 100 million contacts, activities, and analytics entries.
With the data cleaned up, they implemented a plan to monitor the database size and manage data growth, for example, deleting inactive contacts and activities.
## Monitoring
Monitoring your project provides insight into what you can optimize and improve, and informs you of the solution’s overall health.
### Environment
  * Monitor resource utilization on the server (e.g., CPU, RAM, HDD). 
    * Consider tools offered by your hosting environment, like the Xperience SaaS environment’s [Monitoring tools](/documentation/developers-and-admins/deployment/deploy-to-the-saas-environment/manage-saas-deployments#application-monitoring), Azure’s [Application insights](https://learn.microsoft.com/en-us/azure/azure-monitor/app/app-insights-overview), etc.
  * Monitor website availability with uptime services.
  * Monitor website responsiveness / performance. 
    * Consider checking the performance from different geographic locations.


### Application
  * Monitor application errors, warnings, 404 errors, etc. 
    * You can use a reporting service such as [SQL Server Reporting Services](https://learn.microsoft.com/en-us/sql/reporting-services/create-deploy-and-manage-mobile-and-paginated-reports?) to monitor entries in the _CMS_EventLog_ table and send alerts when too many errors appear.


### Data
  * Keep an eye on online marketing data and any custom analytics data that you may store in your database. 
    * Monitor all data, like visitors and their activities, page views, etc., as they can grow rapidly when traffic is high.
    * Create automatic processes to delete inactive contacts.
    * Keep online marketing data for 6 to 12 months to provide the necessary data for contact groups, marketing automation, and other marketing features.
  * Monitor the size of all database tables.
  * Define rules to handle outdated content.
  * Monitor content for broken links.


## What’s next?
We hope this handbook has given you an idea of some important things to consider and look out for over the course of your Xperience project.
Let us know if you come across anything that needs additional explanation, and we will do our best to cover it in updates and future materials.
For now, check out the [community blog](https://community.kentico.com/blog) for more useful information relating to Xperience by Kentico.
As your team prepares for and begins development, the [training guides](/guides/) and [documentation](/documentation/) will also be important resources.
![]()
[]()[]()
