# Installation troubleshooting
  * [ Copy page link ](documentation/developers-and-admins/installation/installation-troubleshooting#) | [Get HelpService ID](documentation/developers-and-admins/installation/installation-troubleshooting#)
Core MVC 5


[✖](documentation/developers-and-admins/installation/installation-troubleshooting# "Close page link panel") [Copy to clipboard](documentation/developers-and-admins/installation/installation-troubleshooting#)
If you encounter issues during or after the installation, the following topics may help you solve them:
  * [Disk permission problems when hosting under IIS](documentation/developers-and-admins/installation/installation-troubleshooting#disk-permission-problems-when-hosting-under-iis)
  * [Database connection problems](documentation/developers-and-admins/installation/installation-troubleshooting#database-connection-problems)


## Disk permission problems when hosting under IIS
Xperience performs most operations without writing to the disk. However, there are situations when the application needs to access the disk, such as:
  * Storing uploaded files on the file system
  * File operations, such as resizing images
  * [Web farm](documentation/developers-and-admins/configuration/auto-scaling-support) synchronization


If you receive an error message stating that the application cannot write to the disk, you need to:
  1. Identify the user account under which your application is running.
  2. Grant the **Modify** and **Write** permissions for the project folder to the account.


For applications hosted in IIS, the user account is based on the used application pool. You can find the user account name in one of the following locations:
  * The encountered error/information message.
  * In **IIS Manager** , under the **Application Pools** section. Determine the user account according to the value in the **Identity** column:
Application pool identity  |  User account name   
---|---  
Local Service  |  Service   
LocalSystem  |  System   
NetworkService  |  Network Service   
ApplicationPoolIdentity  |  IIS AppPool\<app pool name>  
If you are using **Visual Studio’s** built-in web server, the application runs under your Windows user account.


See also: [Set, View, Change, or Remove Permissions on Files and Folders (Microsoft documentation)](https://docs.microsoft.com/en-us/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/cc754344\(v=ws.11\))
## Database connection problems
If you encounter problems when connecting to the SQL server database during the installation or at a later point, make sure that:
  * The target server has a [supported database server](documentation/developers-and-admins/installation/system-requirements) installed and running.
  * You are using the correct instance of the SQL server if the server has multiple instances. The instance name must be entered in format _server\instance_.
  * The TCP/IP protocol is enabled on the SQL server. You can check using the _SQL Server Configuration Manager_ utility, under _SQL Server Network Configuration/SQL Native Client Configuration - > Client Protocols_.
  * Access to the database server is not blocked by a firewall (the default port number for the TCP/IP protocol is 1433).


If the connection fails with the **_“Login failed for user ‘username’.”_** error message, check the following troubleshooting sections, depending on the used authentication type.
### SQL Server accounts
If you are using an SQL Server account with a password to authenticate against the database, make sure that:
  * You are using a valid username and password.
  * The login exists on the server, is enabled, and has permissions to connect to the server (check the logins on your server using SQL Server Management Studio in the **Server - > Security -> Logins** section of the _Object Explorer_).
  * Your server supports SQL Server authentication (check that the **_SQL Server and Windows Authentication mode_** option is enabled in the **Server Properties - > Security** dialog in SQL Server Management Studio).


### Windows authentication mode
To resolve problems with database connections that use Windows authentication, you may need to contact your network administrator. The Xperience application runs under a specific local or domain account, depending on your environment (for example _NetworkService_).
When running the application on IIS, the account depends on the **_Identity_** of the assigned application pool. If your SQL server is located on a different machine than your web server, you may need to configure the application pool to run under a domain account rather than a local account.
The account must have its own login created on the SQL server, configured for Windows authentication, and with the appropriate permissions. You can manage the logins using SQL Server Management Studio, in the **Server - > Security -> Logins** section of the _Object Explorer._