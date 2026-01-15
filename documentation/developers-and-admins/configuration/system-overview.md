# System overview
  * [ Copy page link ](documentation/developers-and-admins/configuration/system-overview#) | [Get HelpService ID](documentation/developers-and-admins/configuration/system-overview#)
Core MVC 5


[✖](documentation/developers-and-admins/configuration/system-overview# "Close page link panel") [Copy to clipboard](documentation/developers-and-admins/configuration/system-overview#)
Xperience provides information about the application and environment running the website and its administration.
To view system information, open the **System** application in Xperience and select **Overview**.
[![System overview in the Xperience administration](docsassets/documentation/system-overview/System_Overview.png)](https://docs.kentico.com/docsassets/documentation/system-overview/System_Overview.png)
System information  
---  
Machine name |  The name of the server machine on which the system is running.  
Uptime |  The time for which the application has been running and available, since the last restart.  
Server time |  The current time on the server machine.  
Database information  
Database name |  The name of the system database.  
Server version |  The SQL Server version installed on the database server.  
Database size |  The current size of the database, including the current data (the .mdf file) and the database log (the .ldf file).  
Server name |  The name of the database server on which the system’s database is running.  
Memory statistics  
Allocated memory |  The size of the memory allocated for the system.  
Process physical memory |  Physical memory used by the application’s process.  
Process virtual memory |  Memory allocated by the process in the virtual memory space.  
## Restart the application
You can restart the application by selecting the buttons in the **System information** section:
  * **Restart application** – sends a request to restart the Xperience application. The application shuts down and starts up again _if the hosting environment supports automatic application start_. If not, the application remains stopped and inaccessible until it is manually launched.
  * **Restart all web farm servers** – sends a restart request to all available [web farm servers](documentation/developers-and-admins/configuration/auto-scaling-support).


**Restart applications in the SaaS environment**
You can also [restart applications](documentation/developers-and-admins/deployment/deploy-to-the-saas-environment/manage-saas-deployments#restart-deployed-applications) deployed to [Xperience by Kentico SaaS](documentation/developers-and-admins/saas) from the Xperience Portal.
## Clear the application cache
Select **Clear cache** in the **Memory statistics** section to remove all [cached content](documentation/developers-and-admins/development/caching) from the application’s memory (server-side cache).
## Optimize database size
Select **Optimize database size** to reclaim space from dropped variable-length (e.g., varchar(max)) database columns. Equivalent to running [DBCC CLEANTABLE](https://learn.microsoft.com/en-us/sql/t-sql/database-console-commands/dbcc-cleantable-transact-sql) over the Xperience database. This can be a long-running operation depending on database size. You will be notified upon completion.
By default, Xperience by Kentico optimizes the database only during [CI/CD restore operations](documentation/developers-and-admins/ci-cd). Use this action to run the operation when required. For example, after dropping variable-length columns with a larger number of records. Useful primarily in development enviroments.