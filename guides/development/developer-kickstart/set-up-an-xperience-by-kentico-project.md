# Set up an Xperience by Kentico project
  * [ Copy page link ](guides/development/developer-kickstart/set-up-an-xperience-by-kentico-project#) | [Get HelpService ID](guides/development/developer-kickstart/set-up-an-xperience-by-kentico-project#) | This page is part of a module: [ Xperience by Kentico Developer kickstart ](modules/developer-kickstart)
Core MVC 5


[✖](guides/development/developer-kickstart/set-up-an-xperience-by-kentico-project# "Close page link panel") [Copy to clipboard](guides/development/developer-kickstart/set-up-an-xperience-by-kentico-project#)
Before you begin building the site, you need to set up an Xperience project.
Xperience provides [.NET project templates](https://docs.microsoft.com/en-us/dotnet/core/tools/custom-templates) you can install via the [.NET command-line tool](https://docs.microsoft.com/en-us/dotnet/core/tools/) (included with the .NET SDK).
A template is a great place to start because it comes with:
  * all required Xperience NuGet package dependencies
  * the startup class, configured according to Xperience requirements (registered services and middleware)
  * automatic database connection configuration


## Install the Xperience project templates package
We will work with the **Xperience by Kentico version 30.6.1** in this series.
If you have previously installed other versions of Xperience on your machine, we recommend [clearing any unwanted templates](guides/development/get-started/install-a-specific-version-of-xperience-by-kentico#check-your-existing-template-versions) first.
Run the following command in your command line prompt:
CMD
**Install project templates**
Copy
```
dotnet new install kentico.xperience.templates::30.6.1 --force
```

If you have other Xperience templates on your machine (for example after playing around with the latest version), the `--force` parameter will ensure the 30.6.1 version gets installed correctly.
You should see an output showing you have successfully installed three project templates on your computer:
[![Command line output showing three Xperience templates have been installed successfully: Boilerplate, Admin sample and Sample](docsassets/guides/set-up-an-xperience-by-kentico-project/templates-installed.png)](https://docs.kentico.com/docsassets/guides/set-up-an-xperience-by-kentico-project/templates-installed.png)
## Create a project
Let’s use the **Boilerplate** template (`kentico-xperience-mvc`) to create an empty project called _Kickstart.Web_.
Navigate to your desired project directory and add the following folder structure: _~/src/Kickstart.Web_.
The _src_ folder will hold the .NET solution.
The _Kickstart.Web_ will contain the top-level MVC project responsible for the site presentation.
Navigate to the _Kickstart.Web_ directory in your command line tool and create your new project:
CMD
**Create a new project**
Copy
```
dotnet new kentico-xperience-mvc -n Kickstart.Web
```

You will encounter the following prompt during the project creation:
```
Template is configured to run the following action:
Description: Restore tools required for this project configuration.
Manual instructions: Run 'dotnet tool restore'
Actual command: dotnet tool restore
Do you want to run this action [Y(yes)|N(no)]?
```

Agree by typing `Y`.
After the successful run, you should see a new .NET project generated in your Kickstart.Web directory.
[![New project structure in File explorer](docsassets/guides/set-up-an-xperience-by-kentico-project/project-created.png)](https://docs.kentico.com/docsassets/guides/set-up-an-xperience-by-kentico-project/project-created.png)
### Add a solution
Adding a solution file will allow other projects to work in tandem with _Kickstart.Web_ and keep things organized.
You can add a solution [via the .NET CLI](https://learn.microsoft.com/en-us/dotnet/core/tools/dotnet-sln) or using an IDE of your choice.
Some IDEs, like Visual Studio or Visual Studio Code with the [C# Dev Kit extension](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csdevkit), will generate the solution file automatically after you open the project. We recommend saving it in the _src_ folder, one level up from _Kickstart.Web_.
For example, if you are using Visual Studio:
  1. Open _Kickstart.Web.csproj_ in Visual Studio, and highlight the auto-generated solution in the solution explorer.
  2. Click on _File_ , and choose **Save Kickstart.Web.sln as** from the dropdown.
  3. Navigate to the _src_ folder, and rename the file to _Kickstart.sln_ , then click **Save**.


## Assign a license
In the _src_ directory, create a new folder called _License_.
Add a file called _license.txt_ with the content of a valid localhost license key.
**Getting your license key**
You can obtain the license key from your agency, supervisor, or team lead.
Alternatively, you can create an account at the [Client Portal](https://client.kentico.com/), and generate a [temporary key](https://client.kentico.com/evaluation-keys) valid for 30 days.
Your browser does not support the video tag. 
Learn more about licensing in our [documentation](documentation/developers-and-admins/installation/licenses).
## Connect the database
The last thing we need is to set up the database.
Run this command from the _~/src/Kickstart.Web_ directory:
CMD
**Create project database**
Copy
```
dotnet kentico-xperience-dbmanager -- -s "<sql_server_name>" -a "kickstart" -d "Xperience.Kickstart" --license-file "..\License\license.txt"
```

Remember to substitute the `<sql_server_name>` placeholder for your SQL server name.
In this example, we are using the following parameters:
  * `-s` specifies the SQL server for the new database.
  * `-a` sets the password for the  _administrator_ account.
  * `-d` names the database in your SQL Server.
  * `--license-file` specifies the path to a text file containing your license key.


Run `dotnet kentico-xperience-dbmanager -- --help` to see the complete list of available commands.
For more details about parameters and configuration options, visit our [documentation](documentation/developers-and-admins/installation#create-the-project-database).
That’s it for the installation process. You now have a basic project with a database on your machine.
## Set up local hosting
When developing websites, you often need to build and test code changes quickly. For this reason, it’s helpful to have the site hosted locally on your machine. In this aspect, ASP.NET Core is very flexible, as it supports multiple hosting options.
We’ll use the [Kestrel web server](https://learn.microsoft.com/en-us/aspnet/core/fundamentals/servers/kestrel?view=aspnetcore-8.0) to host our application. Kestrel is cross-platform and comes included by default with .NET installations. To launch the installed website, execute the following command in the _~/src/Kickstart.Web_ directory:
CMD
Copy
```
dotnet run
```

Alternatively, you may be able to run the `dotnet run` command directly from your IDE.
You should see output similar to the following in the terminal:
```
info: Microsoft.Hosting.Lifetime[14]
      Now listening on: http://localhost:33328
info: Microsoft.Hosting.Lifetime[0]
      Application started. Press Ctrl+C to shut down.
info: Microsoft.Hosting.Lifetime[0]
      Hosting environment: Development
info: Microsoft.Hosting.Lifetime[0]
      Content root path: <path_to_project_directory>
```

Enter the URL from the second line into your browser (in our case, _http://localhost:33328_) to view your site.
When the site loads, you should see a message that says something like this:
`The Kickstart.Web site has not been configured yet.`
There’s not much to see yet, but we’ll get to that in the following steps. For now, you can take a look at the Xperience administration:
Add _/admin_ to your site’s URL and sign in using the default _administrator_ account and the password _‘kickstart’_ , as you specified when installing the project database.
Your browser does not support the video tag. 
**Useful resources for your reference**
Take a look at [Administration interface basics](guides/administration-interface-basics) – a short overview of the Xperience administration and its common navigation and interaction patterns.
Watch [this video](https://www.youtube.com/watch?v=Uyr0J6Ws4R8&ab_channel=Kentico) for a quick walkthrough on installing Xperience by Kentico.
Check out our [Install a specific version of Xperience by Kentico](guides/development/get-started/install-a-specific-version-of-xperience-by-kentico) guide to learn how to manage different Xperience template versions on your machine.
Explore the [Xperience Manager](https://github.com/Kentico/xperience-by-kentico-manager) tool, which can help you simplify installing and managing Xperience by Kentico instances in future projects.
Now, let’s continue with the next step, where you will learn how to create a reusable content type, an essential building block in developing multichannel experiences with Xperience by Kentico.
## Continue learning
When you’re ready, move on to the next page: [Create a reusable content type](guides/development/developer-kickstart/create-a-reusable-content-type)