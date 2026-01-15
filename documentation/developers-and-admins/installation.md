# Installation
  * [ Copy page link ](documentation/developers-and-admins/installation#) | [Get HelpService ID](documentation/developers-and-admins/installation#)
Core MVC 5


[✖](documentation/developers-and-admins/installation# "Close page link panel") [Copy to clipboard](documentation/developers-and-admins/installation#)
Xperience by Kentico is a content and digital experience management system built on the [.NET platform](https://dotnet.microsoft.com/learn/dotnet/what-is-dotnet). For an overview of the system and its features, read our [product introduction](https://www.kentico.com/platforms/xperience-by-kentico).
Evaluate Xperience by Kentico in a local development environment for 30 days for free. See [Evaluate Xperience by Kentico](documentation/developers-and-admins/installation/licenses#evaluate-xperience-by-kentico).
Xperience provides boilerplate .NET projects that are distributed as [.NET project templates](https://docs.microsoft.com/en-us/dotnet/core/tools/custom-templates) and installed via the [.NET command-line tool](https://docs.microsoft.com/en-us/dotnet/core/tools/) (included with the .NET SDK).
Install an Xperience project:
  1. Familiarize yourself with the [System requirements](documentation/developers-and-admins/installation/system-requirements) – make sure the target machine meets the minimum hardware requirements, runs a supported operating system, and contains the software that Xperience needs for the installation.
  2. Install the [project templates package](documentation/developers-and-admins/installation#Installation-InstallTemplatesPackage) that provides project templates you can use to build your website.
  3. Select a project to create from the [available project templates](documentation/developers-and-admins/installation#available-project-templates).
  4. [Create the project](documentation/developers-and-admins/installation#Installation-CreateProject) and the [project database](documentation/developers-and-admins/installation#Installation-CreateProjectDatabase).


**Install Xperience on macOS**
For detailed steps for installing Xperience by Kentico on macOS, see [Developing with Xperience by Kentico on macOS](https://community.kentico.com/blog/developing-with-xperience-by-kentico-on-macos).
## Install project templates package 
Before creating new projects, you must install the Xperience by Kentico project templates package using the .NET CLI.
Open the command line and run the following command:
CMD
**Install project templates**
Copy
```
dotnet new install kentico.xperience.templates
```

This installs the **latest version** of the project templates NuGet package. The package location depends on the operating system.
You can now [create Xperience projects](documentation/developers-and-admins/installation#Installation-CreateProject) based on the [project templates](documentation/developers-and-admins/installation#available-project-templates).
You can also install a specific version of the project templates. This can be useful if you need to work with an older version of Xperience by Kentico.
For more information, see [Install a specific version of Xperience by Kentico](guides/development/get-started/install-a-specific-version-of-xperience-by-kentico).
### Available project templates
The following project types are available, each with a different use case:
Project type |  Template short name |  Description  
---|---|---  
Boilerplate |  kentico-xperience-mvc |  Creates an empty ASP.NET Core application that you can use as a base for your project. The application comes with a basic configuration required to run and develop Xperience projects.  Recommended as a starting point for the development of new Xperience ASP.NET Core projects.  
Dancing Goat |  kentico-xperience-sample-mvc |  A sample project of a fictional coffee shop franchise. Demonstrates the content management and digital marketing features of the Xperience solution.  
Admin UI customization boilerplate |  kentico-xperience-admin-sample |  A project for developers who wish to [extend the Xperience by Kentico administration interface](documentation/developers-and-admins/customization/extend-the-administration-interface). Intended to be used together with another Xperience project (does not work as a standalone application). Helps bootstrap your environment when developing custom client components for the admin UI. For more information, see [Admin UI customization model overview](documentation/developers-and-admins/customization/extend-the-administration-interface/admin-ui-customization-model-overview) and [Prepare your environment for admin development](documentation/developers-and-admins/customization/extend-the-administration-interface/prepare-your-environment-for-admin-development).  
The template short name of each project type needs to be specified when [creating projects](documentation/developers-and-admins/installation#Installation-CreateProject) using the `dotnet new` command.
## Create a project
Once you have installed the project templates package, you can install projects using the .NET command-line interface. You need to know [the template short name for the corresponding project type](documentation/developers-and-admins/installation#available-project-templates).
  1. Open the command line prompt.
  2. Navigate to the location where you want to create the project.
  3. Run the following command:
     * Replace _< short_name>_ with the template short name of the project type you want to install. See [Available project templates](documentation/developers-and-admins/installation#available-project-templates).
     * The `-n` parameter sets the name of the installed project. See the [dotnet new](https://docs.microsoft.com/en-us/dotnet/core/tools/dotnet-new) reference to learn more about the command options. 
       * It is not recommended to use non-alphanumeric characters in the project name, except for dot ‘.’ or underscore ’_’ **after the first character**.
     * The `--cloud` parameter installs a project suitable for [deployment to the SaaS environment](documentation/developers-and-admins/deployment/deploy-to-the-saas-environment). Skip this parameter if you plan to host your project in [private cloud](documentation/developers-and-admins/deployment/deploy-to-private-cloud). Projects installed with the parameter include: 
       * [Kentico.Xperience.Cloud](https://www.nuget.org/packages/Kentico.Xperience.Cloud) NuGet package that enables deployment to the [SaaS environment](documentation/developers-and-admins/deployment/deploy-to-the-saas-environment)
       * Configurations for [SendGrid](documentation/developers-and-admins/configuration/email-configuration#managed-sendgrid-integration) and [Microsoft Application Insights](documentation/developers-and-admins/configuration/saas-configuration#DeploytotheSaaSenvironment-EnableAppInsights)
       * Default [CD configuration file](documentation/developers-and-admins/ci-cd/continuous-deployment#generate-cd-configuration-files)
       * PowerShell script used for [generating the deployment package](documentation/developers-and-admins/deployment/deploy-to-the-saas-environment#create-a-deployment-package)
       * **StorageInitializationModule.cs** file with a sample storage initialization module used for [mapping files to Azure Blob storage](documentation/developers-and-admins/api/files-api-and-cms-io/file-system-providers/azure-blob-storage#azure-blob-storage-for-kenticos-saas)
CMD
**Create a project from a template**
Copy
```
dotnet new <short_name> --cloud -n AcmeWeb
```

  4. Install database .NET tools (`kentico-xperience-dbmanager`) for the project:
    1. You will be asked if you want to install the database tools during the installation of the .NET project (with the `dotnet tool restore` command).
    2. To install the database tools manually, run the `dotnet tool restore` command from the project’s root directory.


The command creates the project files and sets up the database tools for the project.
Continue by [creating the project database](documentation/developers-and-admins/installation#Installation-CreateProjectDatabase).
## Create the project database 
Before you can run an Xperience project, create a database and register a license key for your application:
  1. Obtain a license key for your application and save it in a text file (e.g., _license.txt_):
     * For [deployment to the SaaS environment](documentation/developers-and-admins/deployment/deploy-to-the-saas-environment), you only need a license key for the local development environment. [Generate a license key for local development in your Xperience Portal project](documentation/developers-and-admins/installation/licenses#Licenses-SaaSLicenseGenerator).
     * For [private cloud environments](documentation/developers-and-admins/deployment/deploy-to-private-cloud), see [Licensing private cloud deployments](documentation/developers-and-admins/installation/licenses#licensing-private-cloud-deployments).
     * See [Evaluate Xperience by Kentico](documentation/developers-and-admins/installation/licenses#evaluate-xperience-by-kentico) to learn how to get a 30 days free evaluation license key for local testing.
  2. Run the `dotnet kentico-xperience-dbmanager` command from the root directory of the project:
The `kentico-xperience-dbmanager` command is installed when [creating a project](documentation/developers-and-admins/installation#Installation-CreateProject). Run the `dotnet tool restore` command from the project’s root directory if the command is unavailable.
CMD
**Create project database**
Copy
```
dotnet kentico-xperience-dbmanager -- -s "<sql_server_name>" -d "<database_name>" -a "<admin_password>" --hash-string-salt "<hash_string_salt>" --license-file "<license_file_path>"
```



  * Add parameters after the `--` syntax separator to pass the parameters directly to the `kentico-xperience-dbmanager` command and to avoid conflicts with general `dotnet` parameters.
  * Set the `-s` parameter to your database server name.
  * Set the `-d` parameter to the name of the database.
  * Set the `-u` parameter to the username for the database authentication.
  * Set the `-p` parameter to the password for the database authentication.
  * Set the `-a` parameter to the password you want to use for the default _administrator_ account.
  * Set the `--hash-string-salt` parameter to the [hash salt](documentation/developers-and-admins/configuration/macro-expressions/macro-signatures) value for the Xperience instance: 
    * For [deployment to the SaaS environment](documentation/developers-and-admins/deployment/deploy-to-the-saas-environment), [get the hash salt value from the Xperience Portal Dashboard](documentation/developers-and-admins/saas/xperience-portal#hash-string-salt-for-the-xperience-portal-project).
    * Skip this parameter if you plan to host your project in [private cloud](documentation/developers-and-admins/deployment/deploy-to-private-cloud).
  * Set the `--license-file` parameter to the path to a text file with the license key. This registers the license key in the Xperience administration.
  * Use the `--help` parameter to list all available options
  * If you encounter an error when creating the database, add the `--verbose` parameter to get more detailed information, including the error’s stack trace..


The command installs and connects a database to your project via the `CMSConnectionString` application setting (located in the project’s **appsettings.json** file).
This completes the installation process. Your Xperience project is now ready for [Development](documentation/developers-and-admins/development) and [Deployment](documentation/developers-and-admins/deployment)
**Next steps**
  * Read [Website development basics](documentation/developers-and-admins/development/website-development-basics) for an introduction to the general development process of Xperience websites. The document guides you through basic development and hosting environment setups and introduces important Xperience concepts and features.
  * [Set up local hosting](documentation/developers-and-admins/development/website-development-basics/set-up-local-hosting) for your site so that you can get started with development.
  * Access the Xperience administration by appending **/admin** to your site’s URL. Sign in using the default **administrator** account and the password you specified when installing the project database.


**Get more information**
  * Take a look at our [Developer kickstart](modules/developer-kickstart) and learn how to develop a basic website using Xperience.
  * You can also visit our [Community Portal](https://community.kentico.com/), where you can join the Kentico community, find blog posts, Q&A discussions, and other resources for Xperience by Kentico.
  * If you’re stuck with a particular problem, [contact our support](https://www.kentico.com/services/support). Our team operates non-stop and will be happy to assist you.