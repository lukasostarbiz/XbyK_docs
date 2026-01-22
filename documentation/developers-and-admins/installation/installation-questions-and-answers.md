---
source: https://docs.kentico.com/documentation/developers-and-admins/installation/installation-questions-and-answers
scrape_date: 2026-01-22
---

  * [Home](/documentation)
  * [Developers and admins](/documentation/developers-and-admins)
  * [Installation](/documentation/developers-and-admins/installation)
  * Installation questions and answers 


# Installation questions and answers
This page answers common questions and issues encountered when installing Xperience projects.
  * [What .NET version do the created project templates target?](#what-.net-version-do-the-created-project-templates-target)
  * [Can I change the target framework version of installed projects?](#can-i-change-the-target-framework-version-of-installed-projects)
  * [What if I do not install the database with my web project?](#what-if-i-do-not-install-the-database-with-my-web-project)
  * [How can I access my LocalDB database through Microsoft SQL Server Management Studio?](#how-can-i-access-my-localdb-database-through-microsoft-sql-server-management-studio)
  * [Which starter project should I install?](#which-starter-project-should-i-install)
  * [Where can I find the installation log?](#where-can-i-find-the-installation-log)
  * [Where can I get more information?](#where-can-i-get-more-information)


### What .NET version do the created project templates target?
Projects created by the [installation process](/documentation/developers-and-admins/installation) target [.NET 10](https://dotnet.microsoft.com/en-us/download/dotnet/10.0). Your machine must have the required version installed. 
### Can I change the target framework version of installed projects?
Yes. You can increase the Target framework of installed Xperience projects to a higher .NET version.
  1. Open the project in Visual Studio.
  2. Manually [change the Target framework version](https://docs.microsoft.com/en-us/visualstudio/ide/visual-studio-multi-targeting-overview).


If you update the Target framework version for a project, we also recommend consolidating all installed NuGet packages to target the same version:
  1. In Visual Studio, open the **Package Manager Console** (under **Tools > NuGet Package Manager**).
  2. Run the following command:
CMD
Copy
```
Update-Package -reinstall;
```



The [Update-Package](https://docs.microsoft.com/en-us/nuget/tools/ps-ref-update-package) command reinstalls all NuGet packages with the appropriate target framework version for your project.
### What if I do not install the database with my web project?
Without a database, your web project cannot function at all. You need to have an SQL server available before you begin the installation process. See the [System requirements](/documentation/developers-and-admins/installation/system-requirements) for a list of supported servers.
### How can I access my LocalDB database through Microsoft SQL Server Management Studio?
Use the **(localdb)\Kentico** server name. The database files (_xperience.mdf_ , _xperience_log.ldf_) are physically stored in your user’s profile (e.g., C:\Users\Andy\\).
### Which starter project should I install?
If you wish to develop a new website, use the **Boilerplate** ( _kentico-xperience-mvc_ ) project template. If you wish to evaluate the system or examine a sample implementation of an Xperience site, install the **Dancing Goat** ( _kentico-xperience-sample-mvc_ ) sample site.
### Where can I find the installation log?
Xperience sites are installed as new project templates using the [.NET command-line utility](https://docs.microsoft.com/en-us/dotnet/core/tools/dotnet). The tool doesn’t provide logging support. However, any irrecoverable errors that occur during installation terminate the installation process and the terminal displays a message detailing the issue.
For logging, e.g., inside deployment scripts or similar places where manual supervision is unlikely, you can pipe command output to a physical file. For example, using PowerShell’s [Out-File](https://docs.microsoft.com/en-us/powershell/module/microsoft.powershell.utility/out-file) cmdlet. 
### Where can I get more information?
You can contact our support department, who will be happy to assist, at support@kentico.com.
![]()
[]()[]()
