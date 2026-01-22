---
source: https://docs.kentico.com/documentation/developers-and-admins/customization/extend-the-administration-interface/prepare-your-environment-for-admin-development
scrape_date: 2026-01-22
---

  * [Home](/documentation)
  * [Developers and admins](/documentation/developers-and-admins)
  * [Customization](/documentation/developers-and-admins/customization)
  * [Extend the administration interface](/documentation/developers-and-admins/customization/extend-the-administration-interface)
  * Prepare your environment for admin development 


# Prepare your environment for admin development
This page provides information about the requirements and configuration necessary to get started customizing the Xperience administration interface. Find information about:
  * [Requirements](#requirements)
  * [Client development boilerplate](#client-development-boilerplate)
    * [Naming conventions](#naming-conventions)
    * [Boilerplate project overview](#boilerplate-project-overview)
    * [Add the boilerplate to an Xperience project](#add-the-boilerplate-to-an-xperience-project)
    * [Choose how to serve client scripts](#choose-how-to-serve-client-scripts)
    * [Export custom React components](#export-custom-react-components)
    * [Keep client packages up-to-date](#keep-client-packages-up-to-date)
    * [Rename the boilerplate project](#rename-the-boilerplate-project)


## Requirements
Depending on the scope of your planned customizations, you need some or all of the following components installed on your machine.
To make changes to existing applications and pages (via [page extenders](/documentation/developers-and-admins/customization/extend-the-administration-interface/ui-pages/ui-page-extenders)), or add new [pages](/documentation/developers-and-admins/customization/extend-the-administration-interface/ui-pages) and [applications](/documentation/developers-and-admins/customization/extend-the-administration-interface/ui-pages/ui-application-pages) based on [existing templates](/documentation/developers-and-admins/customization/extend-the-administration-interface/ui-pages/reference-ui-page-templates), you will only be making changes to the administration back end. For this, you need:
  * A development environment with support for .NET (such as [Visual Studio](https://visualstudio.microsoft.com/))


If the default set of components is insufficient and you need custom [UI page templates](/documentation/developers-and-admins/customization/extend-the-administration-interface/ui-pages), [form components](/documentation/developers-and-admins/customization/extend-the-administration-interface/ui-form-components), [validation rules](/documentation/developers-and-admins/customization/extend-the-administration-interface/ui-form-components/ui-form-component-validation-rules), and other components for your planned customizations, you will be developing JavaScript modules that plug into the front-end React application. For this, you also need: 
  * A supported [Node.js](/documentation/developers-and-admins/installation/system-requirements#task-specific-requirements) version
  * A compatible package manager of your choice
  * An editor for JavaScript development with TypeScript support (such as [Visual Studio Code](https://code.visualstudio.com/))
  * [Boilerplate project](#client-development-boilerplate) for the development of client JavaScript modules


See [Admin UI customization model overview](/documentation/developers-and-admins/customization/extend-the-administration-interface/admin-ui-customization-model-overview) for details about the structure of the admin UI customization framework and an overview of its extension points.
## Client development boilerplate
Xperience provides a boilerplate project to help you get started writing custom client components for the admin UI. We recommend using the boilerplate to bootstrap your environment. However, using the project is not required. If you are familiar with the way the admin UI consumes custom modules, you can set up a custom process.
The boilerplate project is available as a .NET template as part of the [Kentico.Xperience.Templates package](/documentation/developers-and-admins/development/website-development-basics/configure-new-projects/xperience-by-kentico-nuget-packages). To install it, run the following command in a suitable directory:
CMD
Copy
```
dotnet new kentico-xperience-admin-sample -n <project_name>
```

The project includes:
  * a template for developing JavaScript modules for the admin UI with minimum required dependencies and configuration
  * a .NET class library project to store back end code and embed the client module
  * examples demonstrating customization options


For more information about the contents of the boilerplate project and its default configuration, see [Boilerplate project overview](#boilerplate-project-overview).
### Naming conventions
JavaScript client modules are registered into the system based on naming conventions that use the **organization name** and **project name**. These values can be seen referenced in multiple places throughout the project.
  * In _webpack.config.js_ in the client JS module template
  * In the _.csproj_ file of the class library containing back-end customizations
  * In the _Module.cs_ class file where the client module is registered via `RegisterClientModule`


The current version of the boilerplate package comes with hardcoded organization and project names (using a fictional **Acme** organization and the **Web.Admin** project name). See [Rename the boilerplate project](#rename-the-boilerplate-project) to learn what needs to be changed when renaming the project.
These conventions ensure uniqueness when sharing client modules with other Xperience projects.
### Boilerplate project overview
The boilerplate project is made up of two main parts:
  1. [a Node.js project for developing JavaScript modules](#node.js-project-overview)
  2. [a .NET class library project](#net-class-library-overview)


#### Node.js project overview
The project comes with all required dependencies and recommended configuration, allowing you to immediately start development. The following table highlights important files:
File |  Description  
---|---  
package.json |  Contains module metadata and dependencies. By default, the package depends on:
  * _@kentico/xperience-admin-base_ – contains templates, hooks, and low level components
  * _@kentico/xperience-admin-components_ – UI components used to build the administration. Use components from this package if you want to maintain the look and feel of the system in your custom code.
  * _@kentico/xperience-webpack-config_ – basic webpack configuration required to build admin UI modules
  * [React](https://reactjs.org/) – library for building client UIs
  * [Babel](https://babeljs.io/) – transpiles JavaScript for compatibility with older browsers
  * [TypeScript](https://www.typescriptlang.org/) – typing support and compile-time type checking for JavaScript
  * [webpack](https://webpack.js.org/) – module bundler

Additionally, the file contains a [browserlist](https://www.npmjs.com/package/browserslist) configuration for Babel. The configuration mimicks the one used when building _@kentico/xperience_ packages. It was chosen with relatively robust backwards compatibility in mind, given that Xperience packages and their dependencies make use of some of the newest features from the ECMAScript specification.  
webpack.config.js |  A configuration for the webpack module bundler.  Depends on _@kentico/xperience-webpack-config_ which provides basic webpack configuration required to correctly build modules for the admin UI. **Webpack dynamic imports not supported** Dynamic imports (`import()`), often used with Webpack for [code splitting](https://webpack.js.org/guides/code-splitting/#dynamic-imports), are **not supported** within custom client modules. The way Xperience discovers and loads these custom modules at runtime is incompatible with code split via dynamic imports. Using dynamic imports in your custom module’s code may result in parts of your customization failing to load or other unexpected runtime errors within the administration interface. Ensure that your Webpack configuration and custom JavaScript/TypeScript code **do not** use the `import()` syntax for splitting parts of your module. All necessary code for your module should be included in the main bundle produced by Webpack during the build process.  
tsconfig.json |  Provides baseline [TypeScript configuration](https://www.typescriptlang.org/docs/handbook/tsconfig-json.html) for the module.  
babel.config.json |  Babel configuration file. Takes the pool of target browsers from package.json.  
#### .NET class library overview
The class library primarily serves as an entry point for client customizations. Client modules need to be registered before they can be used, which occurs in the following files:
File |  Description  
---|---  
project’s csproj file |  Contains directives that set
  * the `organizationName` and `projectName` of the client module
  * path to the output of the compiled javascript files

XML Copy ```
<PropertyGroup>
    <AdminOrgName>organizationName</AdminOrgName>
</PropertyGroup>

<ItemGroup>
    <!-- Output folder of the client build process -->
    <AdminClientPath Include="Client\dist\**">
        <ProjectName>projectName</ProjectName>
    </AdminClientPath>
</ItemGroup>
```
  
Module.cs |  Contains [application startup code](/documentation/developers-and-admins/customization/run-code-on-application-startup) used to register the client module via  C# Copy ```
RegisterClientModule("organizationName", "projectName");
```
available from the base `AdminModule` class.  
Other files included in the class library demonstrate customization options available for the admin UI. See [Admin UI customization model overview](/documentation/developers-and-admins/customization/extend-the-administration-interface/admin-ui-customization-model-overview).
### Add the boilerplate to an Xperience project
Before you can start using the boilerplate to develop client code, you need to add it to your Xperience project:
  1. Install the boilerplate project next to your Xperience project’s folder. Requires the _Kentico.Xperience.Templates_ package to be [installed](/documentation/developers-and-admins/installation).
CMD
Copy
```
dotnet new kentico-xperience-admin-sample -n <project_name>
```

[![Folder structure of the custom admin project](/docsassets/documentation/prepare-your-environment-for-admin-development/FolderStructure.png)](/docsassets/documentation/prepare-your-environment-for-admin-development/FolderStructure.png)
  2. Extract the contents next to your Xperience project’s folder.
  3. Reference the boilerplate project from your Xperience project. 
CMD
Copy
```
cd xperience\project\location
dotnet add reference ..\Acme.Web.Admin\Acme.Web.Admin.csproj
```

  4. Install all dependencies for client-side development using your package manager and build the package.
CMD
Copy
```
# Switches to the directory containing boilerplate client module code
cd ..\Acme.Web.Admin\Client
npm install
npm run build
```

  5. Configure the client module in the Xperience web application.
    1. Open _appsettings.json_ of your Xperience project.
    2. Add the module and configure your preferred way of [handling client scripts](#choose-how-to-serve-client-scripts) under `CMSAdminClientModuleSettings.`
JSON
**appsettings.json**
Copy
```
"CMSAdminClientModuleSettings": {
    // Add the module name in the <orgName>-<projectName> format separated by hyphens
    "acme-web-admin": {
        "Mode": ""
    }
}
```

  6. Build the web application project.


Your environment is now ready for the development of admin UI JavaScript modules.
### Choose how to serve client scripts
The admin UI customization framework supports multiple methods of accessing client scripts. You set which method to use when configuring the module:
Method |  Description  
---|---  
Proxy |  The Xperience web application requests client modules from a webpack dev server that runs parallel to the Xperience application. Changes to client code are immediately integrated and don’t require a restart or rebuild of the web application.  Before you start developing, the webpack server needs to be manually started by running C# Copy ```
npm run start
```
from the root of the module folder (where _package.json_ is located). [![Proxy mode behavior diagram](/docsassets/documentation/prepare-your-environment-for-admin-development/ProxyMode.png)](/docsassets/documentation/prepare-your-environment-for-admin-development/ProxyMode.png)  
When configuring the module in your Xperience application, supply the following parameters:
  * Mode: `Proxy`
  * Port: the port number matching the one set for the webpack server in _webpack.config.js_ (provided with the boilerplate project). JS **webpack.config.js** Copy ```
devServer: {
    port: 3009,
}
```

  * UseSSL: set to true if your local development environment uses HTTPS. _False_ by default. Note that the webpack instance serving your client scripts needs to be [configured to use SSL](https://webpack.js.org/configuration/dev-server/#devserverhttps) as well.

**Example appsettings.json module configuration** JSON **appsettings.json - Proxy mode configuration** Copy ```
"CMSAdminClientModuleSettings": {
    "orgName-projectName": {
        // In Proxy mode, client scripts are built separately and requests for them are proxied to the webpack server
        "Mode": "Proxy",
        // The port number needs to match the port set for the webpack server in webpack.config.js
        "Port": 3009
    }
}
```
  
Embedded |  Client scripts are stored in an assembly as embedded resources. This is the default method when no mode is explicitly configured for the module. The system looks for embedded modules in the directory specified by the `AdminClientPath`  _.csproj_ element.  XML **.csproj of the class library holding customizations** Copy ```
<PropertyGroup>
<TargetFramework><!-- framework_version --></TargetFramework>
<AdminOrgName>acme</AdminOrgName>
</PropertyGroup>

<ItemGroup>
<AdminClientPath Include="Client\dist\**">
    <ProjectName>web-admin</ProjectName>
</AdminClientPath>
</ItemGroup>
```
For changes made to client code to be reflected, you need to:
  1. Build the client code using ```
npm run build
```

  2. Build the Xperience web application (to update the embedded resources).

The following diagram summarizes the behavior of _Embedded_ mode: [![Embedded mode diagram](/docsassets/documentation/prepare-your-environment-for-admin-development/EmbeddedMode.png)](/docsassets/documentation/prepare-your-environment-for-admin-development/EmbeddedMode.png) When configuring the module in your Xperience application, supply the following parameters:
  * Mode: `Embedded`

**Example appsettings.json module configuration** JSON **appsettings.json - Embedded mode configuration** Copy ```
"CMSAdminClientModuleSettings": {
    "orgName-projectName": {
        // In Embedded mode, client scripts are stored in an assembly as embedded resources.
        // Mainly intended for deployment scenarios.
        "Mode": "Embedded"
    }
}
```
**Tips**
  * Use the `Proxy` mode when developing customizations and then switch to the `Embedded` mode for final, deployment-ready builds.
  * You can use [environment-specific](https://docs.microsoft.com/en-us/aspnet/core/fundamentals/environments?view=aspnetcore-5.0) configuration files to configure the modes for different environments.

  
### Export custom React components
You must export all components that you want to make available from the main Xperience admin UI application via the custom module’s **entry.tsx** file. This primarily includes all components covered in the customization section, such as [UI pages](/documentation/developers-and-admins/customization/extend-the-administration-interface/ui-pages), [UI form components](/documentation/developers-and-admins/customization/extend-the-administration-interface/ui-form-components), [validation](/documentation/developers-and-admins/customization/extend-the-administration-interface/ui-form-components/ui-form-component-validation-rules) and [visibility](/documentation/developers-and-admins/customization/extend-the-administration-interface/ui-form-components/ui-form-component-visibility-conditions) rules.
JS
**Exporting custom components**
Copy
```
// Exposes components from the module.
// The export must declare the full path to the given component file.
export * from './custom-layout/CustomLayoutTemplate';
export * from './form-components/ColorSelectorFormComponent';
```

### Keep client packages up-to-date
When [updating your Xperience project](/documentation/developers-and-admins/installation/update-xperience-by-kentico-projects), remember to also update the dependencies of your admin UI client project – _@kentico/xperience-admin-base_ , _@kentico/xperience-admin-components_ , _@kentico/xperience-webpack-config_ , etc.
Use the [npm-update](https://docs.npmjs.com/cli/v9/commands/npm-update) command:
CMD
Copy
```
# Switches to the directory containing boilerplate client module code
cd ..\Acme.Web.Admin\Client
npm update
```

**Custom dependencies**
In admin UI client projects, you may use any dependencies required for your functionality, except those managed by the application itself and shared across system and custom modules. The most important of these is the **React** dependency, which must match the version used by the application. For the required React version, see [System requirements](/documentation/developers-and-admins/installation/system-requirements#task-specific-requirements).
### Rename the boilerplate project
The admin UI customization framework relies on naming conventions to register JavaScript modules. All modules follow the **organizationName.projectName** naming pattern.
You can set these parameters when creating the boilerplate project. This way the project is created and configured with the correct values.
However, you can always rename the project at a later point by changing specific configuration files and folder names. This guide assumes the following values used during boilerplate creation:
  * orgName: Acme
  * projectName: Web.Admin


#### Client code
All files referenced in this section are located under _~/Acme.Web.Admin/Client_.
  1. Change the `orgName` and `projectName` settings in Xperience’s webpack configuration under _~/webpack.config.js_. This changes the name of the resulting module built by webpack:
JS
**webpack.config.js**
Copy
```
const baseConfig = (webpackConfigEnvc, argv) => {
    return baseWebpackConfig({
        // Change the orgName to your desired value
        orgName: "acme",
        // Change the projectName to your desired value
        projectName: "web-admin",
        webpackConfigEnv: webpackConfigEnv,
        argv: argv
    });
};
```

  2. (Optional) Change the name of the JavaScript module in _package.json_ :
JSON
**package.json**
Copy
```
{
    name: "acme-web-admin",
    ...
}
```



#### Back end code
  1. Edit the _.csproj_ file of your class library project under _~/Acme.Web.Admin_ and change the following configuration:
XML
**Acme.Web.Admin.csproj**
Copy
```
<PropertyGroup>
  <TargetFramework><!-- framework_version --></TargetFramework>
  <!-- Change the organization name -->
  <AdminOrgName>acme</AdminOrgName>
</PropertyGroup>

<ItemGroup>
  <AdminClientPath Include="Client\dist\**">
    <!-- Change the project name -->
    <ProjectName>web-admin</ProjectName>
  </AdminClientPath>
</ItemGroup>
```

  2. Edit _~/Acme.Web.Admin/Module.cs_ and change:
C#
**Module.cs**
Copy
```
public class Module : AdminModule
{
    // (Optional) Change the name of the custom module
    public Module()
        : base("Acme.Web.Admin")
    {
    }

    protected override void OnInit()
    {
        base.OnInit();

        // Change the organization name and project name in the client scripts registration
        RegisterClientModule("acme", "web-admin");
    }
}
```



![]()
[]()[]()
