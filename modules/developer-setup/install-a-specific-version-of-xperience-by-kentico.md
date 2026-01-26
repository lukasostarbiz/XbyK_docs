---
source: https://docs.kentico.com/modules/developer-setup/install-a-specific-version-of-xperience-by-kentico
scrape_date: 2026-01-26
---

Module: Developer setup
3 of 7 Pages
# Install a specific version of Xperience by Kentico
During the creation of an Xperience by Kentico instance, when you install **Kentico.Xperience.Templates** without any further specification, the .NET CLI automatically installs the latest stable version.
While this is typically the desired behavior, you may encounter situations where you need to install an older version. For example, your company already uses a given version or has a lengthy approval process. 
This guide demonstrates installation of an exact version of Xperience by Kentico, using the boilerplate project. We’ll work with version **30.11.1** as an example, though you can substitute any valid version number and simply use this guide as a reference.
See the list of all valid version numbers in [the documentation’s _Changelog_ page](/documentation/changelog).
## Before you start
To follow along with this guide, we recommend your machine has the following installed:
  * [.NET 8.0](https://dotnet.microsoft.com/en-us/download/dotnet/8.0)
  * Microsoft SQL Server 2022 (or compatible version, such as the free [SQL Server Express Edition](https://www.microsoft.com/en-us/download/details.aspx?id=104781))
  * an **IDE** that supports ASP.NET Core development, such as [Visual Studio](https://visualstudio.microsoft.com/) or [Visual Studio Code](https://code.visualstudio.com/)


## Check your existing template versions
In the .NET CLI, the `dotnet new` command is used to install NuGet template packages, such as _Kentico.Xperience.Templates_.
Running this command’s uninstall argument without specifying what to uninstall brings up a list of the currently installed template packages.
CMD
**List installed templates**
Copy
```
dotnet new uninstall
```

If you have previously installed multiple versions of _Kentico.Xperience.Templates_ , you’ll notice that all of them appear in the list, but only the most recently installed one includes a _Templates_ subsection with a list of specific template names. **This is the version the system is currently actively using.** For example, this image shows a case where version 29.6.1 was most recently installed:
[![Screenshot showing multiple installed versions of Xperience templates, with only the latest installed \(version 29.6.1\) showing the Templates section](/docsassets/guides/install-a-specific-version-of-xperience-by-kentico/installed-versions.png)](/docsassets/guides/install-a-specific-version-of-xperience-by-kentico/installed-versions.png)
To clean up and uninstall _all templates_ from your machine you can run:
CMD
**Uninstall project templates**
Copy
```
dotnet new uninstall kentico.xperience.templates
```

## Install the required version of templates
Run the following command in your terminal to install the 30.11.1 version of Kentico.Xperience.Templates:
CMD
**Install project templates**
Copy
```
dotnet new install kentico.xperience.templates@30.11.1 --force
```

**Examine the command**
  * The `@` character allows you to specify the version you want to install. 
    * In some older versions of the .NET SDK, you may need to use `::` instead.
  * The `--force` parameter ensures the target version is installed correctly when a newer version of the Xperience templates package already exists in your environment.


If you already have the desired version of Kentico.Xperience.Templates installed (in this case 30.11.1), feel free to skip this step.
Now, with the proper template package installed, you can create projects using the Xperience templates.
## Create a project
Creating the project also relies on the .NET CLI, which will fill the provided folder with project files for the presentation layer of your site.
Navigate to the folder that you want to hold this project’s repository.
Add a _src_ folder within it, and a _TrainingGuides.Web_ folder within the _src_ directory.
Open a command window in the _TrainingGuides.Web_ directory and create a new project called _TrainingGuides.Web._ :
CMD
**Create a new project**
Copy
```
dotnet new kentico-xperience-mvc -n TrainingGuides.Web
```

This project will be the top-level MVC project, responsible for the site’s presentation, but it will not be the only project in the solution.
Agree when prompted to run a restore command.
After the run is successful, you will see a new .NET project generated in your _TrainingGuides.Web_ folder. Navigate one level up and create a new folder called _License_ in the _src_ directory.
Add a file called _license.txt_ with the content of a valid **license key**.
**Getting your license key**
You can obtain the license key from your agency, supervisor, or team lead.
Alternatively, you can create an account at the [Client Portal](https://client.kentico.com/), and generate a [temporary key](https://client.kentico.com/evaluation-keys) valid for 30 days.
Your browser does not support the video tag. 
Learn more about licensing in our [documentation](/documentation/developers-and-admins/installation/licenses).
Add a solution to the project. This will allow other projects to be created to work in tandem and keep things organized.
Some IDE’s, like Visual Studio Code with the [C# Dev Kit extension](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csdevkit), will generate the solution file automatically after you open the project. We recommend saving the solution directly in the _src_ folder, one level up from _TrainingGuides.Web_.
If you are using Visual Studio: 
  1. Open _TrainingGuides.Web.csproj_ in Visual Studio, and highlight the auto-generated solution in the solution explorer.
  2. Click on _File_ , and choose _Save TrainingGuides.Web.sln as_ from the dropdown.
  3. Navigate to the _src_ folder, and rename the file to _TrainingGuides.sln_ , then click _Save_.


## Connect the database
For the next step, let’s install the database.
Remember to replace the `<sql_server_name>` placeholder to match your SQL server before you run the following command.
CMD
**Create project database**
Copy
```
dotnet kentico-xperience-dbmanager -- -s "<sql_server_name>" -a "TrainingGuides123*" -d "Xperience.TrainingGuides" --hash-string-salt "59642433-67b2-4230-9c5b-ad98d02b0c72" --license-file "..\License\license.txt"
```

Note the information specified with each parameter, which you can change if you are using this as a reference rather than following along. 
  * `-s`
    * The SQL server where the database should be installed
  * `-a`
    * The password for the  _administrator_ account when accessing the administration interface
  * `-d`
    * The name given to the database in the SQL Server
  * `--hash-string-salt`
    * The _CMSHashStringSalt_ value is used for signing macros stored in the database
  * `--license-file`
    * The path to a text file containing your license key


For more details about parameters and configuration options, visit our [documentation](/documentation/developers-and-admins/installation#create-the-project-database).
Once the database finishes installing, run the site and visit the _/admin_ path. Sign in to your Xperience by Kentico instance with the username _administrator_ and the password specified in the command above.
**Site not configured message**
When the site loads, you should see a message that says something like this:
```
The TrainingGuides.Web site has not been configured yet.
```

This is okay; you can still access the administration interface. You can remove this message later when you add content and develop page templates.
After a successful sign-in, the app will navigate you to the [Xperience administration dashboard](/documentation/administration-interface-basics), the interface content editors use to perform their daily tasks. As a developer, you will use the administration dashboard to configure and customize Xperience by Kentico according to your company’s business needs.
[![Xperience administration dashboard after signing in as an ‘administrator’ user](/docsassets/guides/install-a-specific-version-of-xperience-by-kentico/xperience-admin.png)](/docsassets/guides/install-a-specific-version-of-xperience-by-kentico/xperience-admin.png)
## What’s next?
Now you have a fresh boilerplate project running on your specified version, so you can get started with development!
Check out also our [Xperience Manager (xman)](https://github.com/Kentico/xperience-by-kentico-manager) tool, which can help you simplify installing and managing Xperience by Kentico instances in future projects.
[ Previous page ](/modules/developer-setup/install-xperience-by-kentico)
3 of 7
[ Mark complete and continue ](/modules/developer-setup/generate-code-for-custom-content-and-data-classes)
![]()
[]()[]()
