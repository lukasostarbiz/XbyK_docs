# Uninstall Xperience by Kentico components
  * [ Copy page link ](documentation/developers-and-admins/installation/uninstall-xperience-by-kentico-components#) | [Get HelpService ID](documentation/developers-and-admins/installation/uninstall-xperience-by-kentico-components#)
Core MVC 5


[✖](documentation/developers-and-admins/installation/uninstall-xperience-by-kentico-components# "Close page link panel") [Copy to clipboard](documentation/developers-and-admins/installation/uninstall-xperience-by-kentico-components#)
The Xperience [installation process](documentation/developers-and-admins/installation) installs several NuGet packages and [.NET CLI](https://docs.microsoft.com/en-us/dotnet/core/tools/) tools on your machine. See the following sections to learn how to uninstall each component:
  * [Xperience project templates NuGet package](documentation/developers-and-admins/installation/uninstall-xperience-by-kentico-components#xperience-project-templates) (Kentico.Xperience.Templates)
  * [.NET CLI tooling for project database management](documentation/developers-and-admins/installation/uninstall-xperience-by-kentico-components#xperience-project-database-manager) (Kentico.Xperience.DbManager)
  * [ASP.NET Core Web App projects created from the templates](documentation/developers-and-admins/installation/uninstall-xperience-by-kentico-components#projects-created-from-templates)


## Xperience project templates
The NuGet package with Xperience project templates can be uninstalled by running:
CMD
**Uninstall project templates**
Copy
```
dotnet new uninstall Kentico.Xperience.Templates
```

This removes the Xperience templates from the system, and they no longer appear in the list offered by the _dotnet new_ command.
## Xperience project database manager
Uninstall the database manager tool by calling the following command from your project’s directory:
CMD
Copy
```
dotnet tool uninstall Kentico.Xperience.DbManager
```

The database manager is installed [locally](https://docs.microsoft.com/en-us/dotnet/core/tools/global-tools) within each project created via the provided templates. If you’re removing the entire project, there’s no need to uninstall this component separately. 
## Projects created from templates
The provided project templates create standard ASP.NET Core Web App project files. To remove individual web applications, simply delete the folder containing the project. The installation process doesn’t modify any configuration on the hosting machine.