---
source: https://docs.kentico.com/modules/private-cloud-deployment/deploy-xperience-with-cli
scrape_date: 2026-01-26
---

Module: Private cloud deployment
2 of 7 Pages
# Deploy Xperience with a CLI
This guide goes over the process of using _Command-line interfaces_ to deploy an Xperience by Kentico application, focusing specifically on hosting in either Azure Web Apps or IIS in your [private cloud](/modules/private-cloud-deployment/deploy-to-private-cloud).
Headings that are specific to either platform will be labelled accordingly.
If you want guidance on how to deploy to the Xperience SaaS environment, where you can manage your projects through the [Xperience Portal](/documentation/developers-and-admins/saas/xperience-portal), see the [documentation’s instructions](/documentation/developers-and-admins/deployment/deploy-to-the-saas-environment) for how to do so.
## Before you start
This guide requires the following:
  * Familiarity with [.NET Core](https://learn.microsoft.com/en-us/dotnet/), general development operations concepts, and working with [Command-line interfaces (CLIs)](https://en.wikipedia.org/wiki/Command-line_interface)
  * An instance of Xperience by Kentico to deploy. The samples in this guide use an instance running on version [29.3.0](/documentation/changelog).


## Start with a working project
Let’s examine the initial deployment process, using the [Kickstart project](https://github.com/Kentico/xperience-by-kentico-kickstart) as an example.
You can still follow along with your own project, you’ll just need to account for the names of files and folders being different.
Start by following the Kickstart repository’s instructions to set up the project and database locally. Don’t forget to run the `kxp-ci-restore` command and double check that the site’s two pages render properly.
## Configure external storage
We recommend hosting assets, form files, and media files in some kind of external storage in all cases, but especially when your application is hosted in _Azure Web Apps_ or other similar environments that do not guarantee persistent storage.
Aside from preserving your files in the event of unexpected restarts, using external storage will prevent future code deployments from destroying or overwriting them.
Xperience offers two solutions out-of-the-box. The documentation contains instructions for [setting up Azure Blob Storage](/documentation/developers-and-admins/api/files-api-and-cms-io/file-system-providers/azure-blob-storage) and [Amazon S3](/documentation/developers-and-admins/api/files-api-and-cms-io/file-system-providers/amazon-s3), along with a way to [customize file system providers](/documentation/developers-and-admins/api/files-api-and-cms-io/file-system-providers/custom-file-system-providers) to integrate with alternative services.
If you plan to have more than one published environment, or use external storage in your development environment as well, make sure to use a separate container or bucket per environment to prevent them from overwriting one another’s files.
## Set up the database
Whether you plan to host your deployed application files in Azure or on your own server with IIS, you can theoretically host your database anywhere the application can access. You can have on-premise servers accessing Azure SQL databases, Azure Web Apps that talk to on-premise SQL servers, or any number of combinations involving other hosting providers. This guide will focus on two options: hosting your database in Azure, or in your own SQL server.
### Azure - Host your database
#### Set up a server to host the database.
If you don’t already have one, sign in to the Azure portal and create a new database server resource. For example, _SQL server (logical server)_.
Configure the server according to your organizations needs and policies. Once it is up and running, go to **Security → Networking** and make sure that your IP is allowed to access the server in the firewall rules.
#### Export the database locally
In SQL Server Management Studio, connect the object explorer to your local database server.
Find the database you want to copy to Azure, in this case the _Xperience.Kickstart_ database that you restored from the Kickstart repository.
Right-click and choose **Tasks → Export Data-tier Application** , and go through the wizard to save a **.bacpac** file.
#### Import the database to the live server
Connect the object explorer to your new Azure SQL server in SQL Server Management Studio.
Then, right-click the **Databases** folder and choose **Import Data-tier Application**.
Follow the wizard to import the database, specifying the details of the service plan and database name.
This database will serve as the live database for the deployed application.
### Host your database in your own SQL Server
If you want to host your database on a standard SQL server, connect to the local version with SQL Server Management Studio (SSMS), and create a full backup of your local _Xperience.Kickstart_ database, taking note of the folder where you save the _.bak_ file.
Then connect SSMS to the database server that will host the published site’s database, right-click on the **Databases** folder, and choose _Restore_. Choose the _.bak_ file from the previous backup, and restore it as a new database on the target server.
## Separate the app settings
In your Xperience by Kentico solution (_Kickstart.sln_), make a copy of **appsettings.json** named **appsettings.development.json**.
Leave the new _appsettings.development.json_ file as-is, but change the connection string of **_appsettings.json_** to connect to the live database.
Alternatively, you can employ things like [user secrets and environment variables](https://learn.microsoft.com/en-us/aspnet/core/security/app-secrets?view=aspnetcore-8.0) to make sure different environments have the appropriate connection strings.
This way, you can test with your local kickstart database while developing, but the published version of the application will connect to the live database.
You can also use this to store settings to determine which container or bucket each environment uses if you have set up external storage.
## Create a publishable package
Navigate to the _Kickstart.Web_ folder where the main project’s _.csproj_ file lives, and open a command line.
Use the `dotnet publish` command, setting the `configuration` option to `Release`.
CMD
**Publish with the .NET CLI**
Copy
```
dotnet publish --configuration Release
```

You can use the [available options](https://learn.microsoft.com/en-us/dotnet/core/tools/dotnet-publish#options) to configure aspects of the resulting application.
You can also configure the published app by adding elements to the _.csproj_ file before you publish. For example, you can set a [Runtime Identifier](https://learn.microsoft.com/en-us/dotnet/core/rid-catalog) in the project file if you want to always build the project for a specific target platform without specifying the `runtime` option of the `dotnet publish` command.
## Azure - Deploy the application to your Web App
This guide uses Azure CLI to deploy your application to a Web App in Azure.
If you don’t already have this CLI installed, use `winget` to download and install it.
CMD
**Install Azure CLI**
Copy
```
winget install -e --id Microsoft.AzureCLI
```

After running this command, close any reopen any active terminal windows so that you can use the new tool.
Run the `az login` command to initiate an authentication flow in your browser, allowing you to access your Azure subscriptions.
### Set up a web app
If you don’t already have an empty web app where you want to deploy your code, you’ll need to create one.
You can do this through the [Azure portal](https://portal.azure.com/) or with the `az webapp` command group of the Azure CLI.
Make sure that the web app can access your SQL server. For example, if your database is also hosted in Azure, you can create a firewall exception at the [database level](https://learn.microsoft.com/en-us/azure/azure-sql/database/firewall-configure?view=azuresql) [server level](https://learn.microsoft.com/en-us/azure/azure-sql/database/firewall-create-server-level-portal-quickstart?view=azuresql), or you can [set up Microsoft Entra ID](https://learn.microsoft.com/en-us/azure/azure-sql/database/authentication-aad-configure?view=azuresql&tabs=azure-portal) for your applications.
### Zip and deploy the application.
Open a command window and navigate to the **.\bin\Release\net8.0\publish** directory within the _Kickstart.Web_ folder where you previously ran `dotnet publish`.
We’re going to [deploy this folder as a zip file](https://learn.microsoft.com/en-us/azure/app-service/deploy-zip?tabs=cli), so start by putting its contents into a zip folder. You may be able to do this in the command line, depending on your environment, for example, with `Compress-Archive` in _PowerShell_ or with `zip` in _Bash_.
Now, run the `az webapp deploy` command, specifying the name of your resource group, web app, and zip.
CMD
**Deploy with the Azure CLI**
Copy
```
az webapp deploy --resource-group Kickstart --name KickstartWebApp --src-path .\KickstartDeployment.zip
```

## IIS - Deploy the application to your web server
### Install the .NET Core Hosting Bundle
In order to host .NET core apps, you’ll need to make sure to install the [.NET Core Hosting Bundle](https://dotnet.microsoft.com/permalink/dotnetcore-current-windows-runtime-bundle-installer) for IIS on the web server where you want your published application to live.
### Set up the web site
On the web server where you will host your application, add a new [site](https://learn.microsoft.com/en-us/iis/get-started/getting-started-with-iis/create-a-web-site) or [application](https://learn.microsoft.com/en-us/iis/configuration/system.applicationhost/sites/site/application/) in IIS, depending on your requirements. Specify a **Physical path** where it should exist in the filesystem, and take note of this location for later.
Configure any domains and certificates your application requires.
### Deploy the application
On your development machine, navigate to the **.\bin\Release\net8.0\publish** directory within the _Kickstart.Web_ folder where you previously ran `dotnet publish`.
Copy the contents of this folder to the **Physical path** location that the IIS site or application points to on the web server.
Depending on your network and security settings, this may be possible to do with the command line, for example, with `robocopy` in _cmd.exe_ , `Copy-Item` in _PowerShell_ , or `cp` in _Bash_. We recommend setting up a reusable script to run these commands along with other checks and tasks that you want to perform during deployment.
## Configure the site
Now, when you access your site, wherever it is hosted, you’ll likely see an [HTTP 500 error](https://developer.mozilla.org/en-US/docs/Web/HTTP/Status/500).
This error appears because the Kickstart repo does not contain a license key for the domain that hosts your published application.
Access the **/admin** path of your application to sign in to the Xperience admin UI. Add a license key for all necessary domains in the **License keys** application.
[ Previous page ](/modules/private-cloud-deployment)
2 of 7
[ Mark complete and continue ](/modules/private-cloud-deployment/deploy-code-and-data-changes)
![]()
[]()[]()
