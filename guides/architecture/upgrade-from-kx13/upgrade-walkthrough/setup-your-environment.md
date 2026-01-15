# Set up your environment
  * [ Copy page link ](guides/architecture/upgrade-from-kx13/upgrade-walkthrough/setup-your-environment#) | [Get HelpService ID](guides/architecture/upgrade-from-kx13/upgrade-walkthrough/setup-your-environment#) | This page is part of a module: [ Upgrading to Xperience by Kentico - Walkthrough ](modules/upgrade-walkthrough)
Core MVC 5


[✖](guides/architecture/upgrade-from-kx13/upgrade-walkthrough/setup-your-environment# "Close page link panel") [Copy to clipboard](guides/architecture/upgrade-from-kx13/upgrade-walkthrough/setup-your-environment#)
Let’s dive into the upgrade of the Dancing Goat sample site. If you follow along, at the end of this walk-through you will be able to display the **Contacts** page from **Kentico Xperience 13 (KX13)** in a new target **Xperience by Kentico (XbyK)** instance.
The first step of upgrading is to prepare your environment. You need to have your source solution running, download the migration tool, and set up the target instance.
## Prepare the source instance
  * Install the KX13 **Dancing Goat sample site** , [Refresh 13](https://download.kentico.com/Xperience_13_Refresh13.exe). You can also find the installer download in the [Client portal](https://client.kentico.com/download). 
If this is your first time installing the KX13 Dancing Goat sample site, follow [these steps](13tutorial/developer-tutorial/asp-net-core-development-tutorial/setting-up-an-xperience-core-project) through the installation wizard, but make sure to pick **Dancing Goat sample site** under **Installation type → Site**.
[![Select ‘Dancing Goat sample site’ in the installation wizard](docsassets/guides/setup-your-environment/dg-installation.png)](https://docs.kentico.com/docsassets/guides/setup-your-environment/dg-installation.png)  

To run the KX13 Dancing Goat locally, you will need to [add a localhost license](13/configuring-xperience/managing-sites/managing-site-license-keys).
If you currently don’t have access to a valid one, you can download it from our [our Client portal](https://client.kentico.com/migration-walkthrough-key). 
  * Run the source instance.
  * Double-check that you can view the live site and the administration. Make sure that you can view the **Contacts** page, especially.


## Get the migration tool
  * Make sure to have [ASP.NET Core 8.0](https://dotnet.microsoft.com/en-us/download) installed on the machine where the migration will run.
  * Pull the _master_ branch of the [Xperience by Kentico: Kentico Migration Tool](https://github.com/Kentico/xperience-by-kentico-kentico-migration-tool/tree/master) to your environment.
  * Review the [Library Version Matrix](https://github.com/Kentico/xperience-by-kentico-kentico-migration-tool/tree/master?tab=readme-ov-file#library-version-matrix) to determine the **compatible version of XbyK**.
E.g., Kentico Migration Tool version 3.0.0 is compatible with Xperience by Kentico version 30.2.0. See the [changelog](https://github.com/Kentico/xperience-by-kentico-kentico-migration-tool/releases/) for more compatibility info. 


## Install the target instance
  * [Install](guides/development/get-started/install-a-specific-version-of-xperience-by-kentico) a **migration tool-compatible version** of Xperience by Kentico, using the [_kentico-xperience-mvc_ template](guides/development/get-started/install-a-specific-version-of-xperience-by-kentico). 
    * Install your main project in a folder named **DancingGoat.Web** , which has at least one parent folder specific to this migration. We recommend a structure like _YOUR_REPOSITORIES/DancingGoatMigration/src/DancingGoat.Web_.
    * Name the project _DancingGoat.Web_ : 
CMD
**installation**
Copy
```
dotnet new kentico-xperience-mvc -n DancingGoat.Web
```

    * Remember to [install the database](documentation/developers-and-admins/installation#create-the-project-database) using the `kentico-xperience-dbmanager` tool from the .NET CLI.
  * Run your new target instance.
  * Make sure you can view the live site and sign in to the administration.


## Watch a video overview
Check out this video overview of the initial setup to verify your environment is correctly configured:
Your browser does not support the video tag. 
### Walkthrough progress
Follow to the next article and learn about [migrating data and binary files](guides/architecture/upgrade-from-kx13/upgrade-walkthrough/migrate-data-and-binary-files).