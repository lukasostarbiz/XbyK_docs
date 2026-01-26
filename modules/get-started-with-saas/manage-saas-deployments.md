---
source: https://docs.kentico.com/modules/get-started-with-saas/manage-saas-deployments
scrape_date: 2026-01-26
---

Module: Get started with SaaS
3 of 4 Pages
# Monitor your project
Use Xperience Portal (available at <https://xperience-portal.com>) to manage your Xperience by Kentico applications [deployed in the SaaS environment](/documentation/developers-and-admins/deployment/deploy-to-the-saas-environment).
## Application monitoring
The **Monitoring** set of applications in the Xperience Portal are designed to keep your application running smoothly and provide full visibility into your application’s performance, enabling you to quickly detect and resolve issues, optimize performance, and ensure the best possible user experience.
  * [Uptime monitoring](#uptime-monitoring)
  * [Event log and exceptions](#event-log-and-exceptions)
  * [Performance metrics](#performance-metrics)
  * [Security events](#security-events)
  * [Alerts and email notifications](#alerts)


### Uptime monitoring
Use the **Outages** application under **Monitoring** in Xperience Portal to see a list of outages for your Xperience Portal project and deployed applications. Records include the time when the outage occurred and the cause of the outage. You can filter the outages by the month when they occurred.
Go to [status.xperience-portal.com](https://status.xperience-portal.com/) to see the uptime statistics for Xperience Portal. See [Xperience Portal uptime statistics](#xperience-portal-uptime-statistics).
[![Outages](/docsassets/documentation/manage-saas-deployments/Cloud_Outages.png)](/docsassets/documentation/manage-saas-deployments/Cloud_Outages.png)
Your Xperience Portal project occasionally undergoes [scheduled maintenance](/documentation/developers-and-admins/saas/xperience-portal#schedule-maintenance-of-xperience-portal-projects), during which certain Xperience Portal features may become unavailable.
#### Xperience Portal uptime statistics
Go to [status.xperience-portal.com](https://status.xperience-portal.com/) to see the statistics about the availability of:
  * Xperience Portal
  * Deployment API
  * Production sites by region


You can also see the list of **Past incidents** below the statistics.
You can subscribe to the incident reporting to receive a message whenever Kentico creates, updates, or resolves an incident. Select **Subscribe to** **updates** at the top of the Xperience by Kentico status page.
### Event log and exceptions
Xperience by Kentico integrates [Application Insights for ASP.NET Core applications](https://learn.microsoft.com/en-us/azure/azure-monitor/app/asp-net-core) to enable monitoring and aggregation of metrics for your website. For detailed information about enabling the Application Insights integration for your projects, see [Microsoft Application Insights integration](/documentation/developers-and-admins/configuration/saas-configuration#microsoft-application-insights-integration).
To view the Application Insights logs for your deployments, use the following applications under **Monitoring** in Xperience Portal:
  * **Event log** – displays entries from the [Xperience event log](/documentation/developers-and-admins/configuration/event-log) and any custom messages sent via the Application Insights SDK.
  * **Exceptions** – displays all .NET exceptions (errors) from Application Insights. Includes exceptions that can occur before the Xperience application is fully running, in addition to errors in the Xperience event log.


You can filter the logs for individual deployment environments, and based on the time when the event occurred.
[![Exception data from Application Insights](/docsassets/documentation/manage-saas-deployments/XP_Monitoring_Exceptions.png)](/docsassets/documentation/manage-saas-deployments/XP_Monitoring_Exceptions.png)
**Log size limits**
There is a **daily limit** on the size of the Application Insights logs. After the daily limit is reached, further logs are discarded.
The following table lists the daily limits for all deployment environment types and service plans.
**Environment** |  **Service plan** |  **GB/Day**  
---|---|---  
QA |  All |  1  
UAT |  Custom |  2  
PROD and STG |  Level 1 & Level 2 |  3  
PROD and STG |  Custom |  6  
1 GB can store approximately 125 000 average-sized exceptions or event log records.
### Performance metrics
Use the **Project metrics** application under **Monitoring** to see various performance metrics related to applications deployed to the SaaS environment. The available metrics include server errors, response time, CPU usage and memory usage. By tracking these metrics, potential issues can be detected and addressed before they impact users, bottlenecks and areas for optimization can be identified, resource utilization can be better understood, and insights can be gained to make data-driven decisions to improve and optimize the application.
To view performance metrics for various SaaS environments:
  1. In [Xperience Portal](https://xperience-portal.com), navigate to **Monitoring → Project metrics**.
  2. Select an **Environment** and a **Time period** , for which you want to display metrics.
  3. **Apply** the changes.


[![Metrics in Xperience Portal](/docsassets/documentation/manage-saas-deployments/XP_Monitoring_Metrics.png)](/docsassets/documentation/manage-saas-deployments/XP_Monitoring_Metrics.png)
### Email metrics
Use the **Email metrics** application under **Monitoring** to see email sending statistics from the [SendGrid platform](/documentation/developers-and-admins/configuration/email-configuration#sendgrid-integration). The available statistics include total numbers of delivered, bounced, and deferred emails. You can also track the number of [unique clicks](https://www.twilio.com/docs/sendgrid/glossary/clicks) in emails. These metrics help identify deliverability issues, optimize campaigns, and improve user engagement to drive better marketing performance.
To view email metrics for various email channels and [deployment environment](/documentation/developers-and-admins/deployment/deploy-to-the-saas-environment#deploy-the-package-to-another-deployment-environment):
  1. In [Xperience Portal](https://xperience-portal.com), navigate to **Monitoring → Email metrics**.
  2. Select a **Channel** , **Environment** and a **Time period** , for which you want to display metrics.
  3. **Apply** the changes.


[![Email metrics in Xperience Portal](/docsassets/documentation/manage-saas-deployments/XP_Monitoring_Emails.png)](/docsassets/documentation/manage-saas-deployments/XP_Monitoring_Emails.png)
### Security events
Use the **Security events** application under **Monitoring** to view security events detected and handled by [Cloudflare CDN](/documentation/developers-and-admins/configuration/saas-configuration#cloudflare-cdn). The application provides an overview of security threats, including [DDoS attacks](https://en.wikipedia.org/wiki/Denial-of-service_attack), malicious requests, and other suspicious activities that Cloudflare automatically detects and mitigates.
Security event data is available for the **last 28 days** only. Events older than 28 days are automatically removed and cannot be retrieved.
Security events are categorized into the following action types:
  * **Block** – Requests blocked by Cloudflare’s security rules
  * **Skip** – Requests that bypassed certain security checks
  * **Log** – Security events that were logged for monitoring purposes
  * **Managed challenge** – Requests that received an automated challenge to verify legitimacy


To view security events for your project:
  1. In [Xperience Portal](https://xperience-portal.com), navigate to **Monitoring → Security events**.
  2. Use the available filters to narrow down the security events: 
     * **Channel** – Filter by website channel
     * **Environment** – Select a [deployment environment](/documentation/developers-and-admins/deployment/deploy-to-the-saas-environment#deploy-the-package-to-another-deployment-environment) (QA, UAT, PROD, STG)
     * **Actions** – Filter by action type (Block, Skip, Log, Managed challenge)
     * **From (UTC)** and **To (UTC)** – Specify the time range for events
     * **Limit** – Set the number of most recent events to retrieve from Cloudflare
  3. **Apply filters** to view the filtered results.


The **Security events overview** section displays the total count of security events in the selected time period, a breakdown by action type with color-coded indicators showing the number of events for each category, and a timeline chart visualizing security event trends over time, helping you identify patterns and potential attack periods.
The **Security events** table displays filtered events based on your selected parameters. To view detailed information about a specific event, including Cloudflare’s [Ray ID](https://developers.cloudflare.com/fundamentals/reference/cloudflare-ray-id/), description, HTTP request details, and response information, select the Information (**Details** column.
[![Security events in Xperience Portal](/docsassets/documentation/manage-saas-deployments/security-events.png)](/docsassets/documentation/manage-saas-deployments/security-events.png)
### Alerts
Alerts notify about potential problems with applications deployed to the SaaS environment. Most of the available alerts are based on [performance metrics,](#performance-metrics) such as application responses, server errors or CPU/memory/DTU utilization.
The system currently provides the following types of alerts:
Alert type |  Severity |  Alert conditions  
---|---|---  
Application response |  Critical |  The application was not responding to web requests for over **15 minutes**. “Not responding” means that the value of the _Application response_ [metric](#performance-metrics) is 0%.  
CPU utilization greater than 80% |  Error |  The application’s average CPU utilization over the past **30 minutes** was above 80%.  
Memory utilization greater than 90% |  Error  |  The application’s average memory utilization over the past **30 minutes** was above 90%.  
DTU utilization greater than 90% |  Error |  The application’s average Database Transaction Unit (DTU) utilization over the past **30 minutes** was above 90%. The DTU utilization metric is calculated as the ratio of used DTUs to the total DTUs available for your project’s plan.  
DTU utilization greater than 70% |  Warning |  The application’s average memory utilization over the past **30 minutes** was above 70%. The DTU utilization metric is calculated as the ratio of used DTUs to the total DTUs available for your project’s plan.  
Failed requests (Server errors) |  Error |  More than 5% of web requests sent to the application over a **30 minute** interval resulted in a failed status, i.e. returned a 5xx [HTTP status code](https://en.wikipedia.org/wiki/List_of_HTTP_status_codes).  
Failed deployment |  Error |  [Deployment](/documentation/developers-and-admins/deployment/deploy-to-the-saas-environment#deploy-with-a-deployment-package) of a package failed.  
Service scaling |  Warning |  A production environment was scaled out to the maximum number of allowed instances over the past **360 minutes**. Typically occurs due to auto-scaling under heavy load. Only applies to projects with a custom service plan, which can allow scaling to 3 or more service instances.  
Cloudflare DDoS attack |  Warning |  An HTTP [DDoS](https://en.wikipedia.org/wiki/Denial-of-service_attack) attack has been detected on one of your domains. These attacks are automatically detected and mitigated by the [Cloudflare CDN](/documentation/developers-and-admins/configuration/saas-configuration#cloudflare-cdn). View detailed security event data in the [Security events](#security-events) application.  
To view alerts for your project’s deployments, navigate to **Monitoring → Alerts** in [Xperience Portal](https://xperience-portal.com). You can filter the alerts for individual deployment environments, based on the time when the alert was fired, and the alert severity.
Alerts are displayed with the **Resolved** _CPU utilization_ alert will be resolved after the average utilization drops under the 80% threshold over 30 minutes. Select **Show active alerts only** in the filter if you wish to see only unresolved alerts.
[![Alerts in Xperience Portal](/docsassets/documentation/manage-saas-deployments/XP_Monitoring_Alerts.png)](/docsassets/documentation/manage-saas-deployments/XP_Monitoring_Alerts.png)
#### Alert email notifications
Whenever an [alert](#alerts) is fired, the system sends a notification email to all Xperience Portal users assigned to the related project.
Individual users can adjust their preferences for receiving alert email notifications:
  1. In [Xperience Portal](https://xperience-portal.com), expand the top-right user menu.
  2. Select **Notifications**.
  3. Customize for which alert types and severities you want to receive email notifications from each [deployment environment](/documentation/developers-and-admins/deployment/deploy-to-the-saas-environment#deploy-the-package-to-another-deployment-environment). 
     * **Note** : The selected alert severity and alert type conditions are combined using an _AND_ statement. If you select _Critical_ alert severity and _CPU_ alert type, you will not receive any alerts of alert types other than _CPU_. For **Production** and **Staging** environments, it is recommended to enable all alert types and at least _Critical_ and _Error_ alert severity.
  4. Select **Save changes**.


[![Alert notification settings in Xperience Portal](/docsassets/documentation/manage-saas-deployments/XP_Alert_Notifications.png)](/docsassets/documentation/manage-saas-deployments/XP_Alert_Notifications.png)
[ Previous page ](/modules/get-started-with-saas/get-started-with-saas)
3 of 4
[ Mark complete and continue ](/modules/get-started-with-saas/next-steps)
![]()
[]()[]()
