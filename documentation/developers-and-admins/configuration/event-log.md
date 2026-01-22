---
source: https://docs.kentico.com/documentation/developers-and-admins/configuration/event-log
scrape_date: 2026-01-22
---

  * [Home](/documentation)
  * [Developers and admins](/documentation/developers-and-admins)
  * [Configuration](/documentation/developers-and-admins/configuration)
  * Event log 


# Event log
The event log stores information about significant events that occur in the system. It is useful to view logged information if unwanted behavior occurs in the system and you want to find out where the problem originates or get additional details.
To view the event log, open the **Event log** application.
[![Event log overview](/docsassets/documentation/event-log/event_log.png)](/docsassets/documentation/event-log/event_log.png)
**Find relevant events**
When investigating issues or errors in the system, try to examine the **Source** and **Event code** of logged events. These two properties uniquely describe the nature of the event in most cases. For more information, select specific events in the log and view their full **Description** or other relevant properties.
The search bar allows you to find items with a source or event code matching the entered text. You can also **Filter** the list of events.
## Configure the event log
The verbosity and severity of the entries in the event log can be adjusted via application settings. For detailed instructions and technical information about logging, see [Logging](/documentation/developers-and-admins/development/logging).
Additionally, you can configure the event log functionality in the **Settings** application within the Xperience administration. The following settings are available in the **System → Event log** category.
Setting |  Description  
---|---  
Event log size |  Limits the maximum number of events stored in the Event log.
  * When exceeded by 10%, this percentage of the oldest events is deleted from the log in a batch. You can adjust the percentage using the **CMSLogKeepPercent** configuration key (via the project’s _appsettings.json_ file).
  * Old events that exceed the limit are not deleted immediately but only after a new event is logged.
  * Set the value to `0` if you do not want the system to log any events.

  
Log object actions |  If enabled, basic create, edit, and delete actions for Xperience objects are logged in the Event log.  
Log to database |  Indicates if events are logged to the database, i.e., the default log displayed in the _Event log_ application. Does not apply if the **Event log size** setting is 0.  
Log to file system |  Indicates if events are logged to an **App_Data\logEvents.log** file in the project directory. Does not apply if the **Event log size** setting is 0.  
![]()
[]()[]()
