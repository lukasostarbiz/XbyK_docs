---
source: https://docs.kentico.com/documentation/developers-and-admins/deployment/deploy-to-private-cloud/deploy-without-the-administration
scrape_date: 2026-01-22
---

  * [Home](/documentation)
  * [Developers and admins](/documentation/developers-and-admins)
  * [Deployment](/documentation/developers-and-admins/deployment)
  * [Deploy to private cloud](/documentation/developers-and-admins/deployment/deploy-to-private-cloud)
  * Deploy without the administration 


# Deploy without the administration
Deployment without the Xperience by Kentico administration is currently not supported when [deploying to SaaS](/documentation/developers-and-admins/deployment/deploy-to-the-saas-environment). The scenario described on this page applies only to [private cloud deployments](/documentation/developers-and-admins/deployment/deploy-to-private-cloud).
Xperience and all related dependencies are installed via [NuGet packages](/documentation/developers-and-admins/development/website-development-basics/configure-new-projects/xperience-by-kentico-nuget-packages). The installed packages dictate the features available to each project. However, not all packages are required for Xperience sites to run in production environments.
The Xperience administration interface (installed via the **Kentico.Xperience.Admin** NuGet package) and all related customizations can be removed when publishing to production. In these types of environments, you can have:
  * One Xperience instance **with** the administration interface. Access to this instance is restricted only to authorized users (i.e., placed behind a firewall or on a private network). This is where content editors and other users work with the system. 
    * **Note** : Do not remove any code that is part of your project’s live site implementation or presentation logic (controllers, views, etc.) from the administration instance. Even though the instance is not intended for live visitors, such logic is required by certain administration features, such as the [preview mode](/documentation/business-users/website-content#preview) for website channel pages.
  * Another Xperience instance **without** the administration interface. This is the instance with which regular live site visitors interact.
  * Both instances [connect to the same database](/documentation/developers-and-admins/configuration/auto-scaling-support).


Advantages | 
  * **Increased security** – removing the administration from public-facing sites reduces the attack surface available to malicious actors.
  * **Improved performance** – removing the administration reduces application overhead, which is beneficial for sites with significant traffic. For example, the administration registers a separate middleware pipeline for user authentication, which is unnecessary for the live site.

  
---|---  
Disadvantages | 
  * **Deployment complexity** – preparing a separate instance without the administration and its dependencies increases the complexity of your environment and adds extra steps to your deployment process.

  
In the most basic scenario, you can set up and deploy Xperience without the administration manually:
  1. Create a separate copy of your Xperience application.
  2. Uninstall the **Kentico.Xperience.Admin** NuGet package and remove any custom code intended only for the administration.
  3. Deploy both applications – the original to an environment with restricted access and the new copy without the administration to production.


However, in most cases, we recommend that you integrate the separation into an automated publishing script or pipeline.
## Sample automated separation process
This section outlines an approach that modifies the application’s build process to remove the **Kentico.Xperience.Admin** NuGet package and all related dependencies, including custom code intended only for the administration. The demonstrated approach is suitable for use in publishing scripts or automation pipelines.
  1. Edit your Xperience application’s .csproj file and add a new `AdminAttached` [MSBuild property](https://docs.microsoft.com/en-us/visualstudio/msbuild/propertygroup-element-msbuild):
XML
**Xperience application’s .csproj file**
Copy
```
<PropertyGroup>
    <TargetFramework> <!-- ... --> </TargetFramework>
    <RootNamespace> <!-- ... --> </RootNamespace>
    <!-- Defines an MSBuild property that defaults to true
         if not explicitly passed to MSBuild as a parameter -->
    <AdminAttached Condition="'$(AdminAttached)' == ''">true</AdminAttached>
</PropertyGroup>
```

  2. Add the following condition to the `Kentico.Xperience.AspNetCore.Admin` package reference using the `Condition` attribute:
XML
**Xperience application’s .csproj file**
Copy
```
<ItemGroup>
    ...
    <!-- If the specified condition evaluates to true (true by default), the project maintains this package reference -->
    <PackageReference Include="kentico.xperience.admin" Version="..." Condition="'$(AdminAttached)' == 'true'" />
</ItemGroup>
```

  3. Follow a similar process to remove any custom code when [customizing the administration interface](/documentation/developers-and-admins/customization/extend-the-administration-interface) or some of its [components](/documentation/developers-and-admins/configuration/rich-text-editor-configuration). Assuming your code follows best practices for admin UI customization outlined in [Admin UI customization model overview](/documentation/developers-and-admins/customization/extend-the-administration-interface/admin-ui-customization-model-overview), your administration-related code is located in isolated assemblies/NuGet packages, making the process straightforward. Repeatedly add the `Condition` attribute to all relevant references:
XML
**Xperience application’s .csproj file**
Copy
```
<!-- Project assembly references -->
<ItemGroup>
    ...
    <ProjectReference Include="..\Acme.Web.AdminCustomCode.csproj" Condition="'$(AdminAttached)' == 'true'" />
</ItemGroup>

<!-- Project package references -->
<ItemGroup>
    ...
    <PackageReference Include="Emca.Web.AdminCustomCode" Version="..." Condition="'$(AdminAttached)' == 'true'" />
</ItemGroup>
```

If you also need to make specific code outside dedicated assemblies run only when the administration is attached, declare a conditional compilation constant and use it as required:
XML
**Xperience application's .csproj file**
Copy
```
<PropertyGroup Condition="'$(AdminAttached)' == 'true'">
    <DefineConstants>$(DefineConstants);ADMIN_ATTACHED</DefineConstants>
</PropertyGroup>
```

C#
**ADMIN_ATTACHED usage**
Copy
```
#if ADMIN_ATTACHED
    // Surrounded code only compiles when administration dependencies are present
#endif
```

You can also invert this condition to run code only when the administration is detached. However, **do not** use such conditions to remove your project’s live site implementation or presentation logic from the administration instance. Such logic is required by certain administration features, such as the [preview mode](/documentation/business-users/website-content#preview) for website channel pages. 
  4. When you need to publish the project without admin UI packages and dependencies, pass `AdminAttached=false` as a parameter to MSBuild:
CMD
Copy
```
dotnet publish -c Release -p:AdminAttached=false --output <OUTPUT_DIRECTORY>
```



The output directory now contains the published application minus the dependencies marked by the `AdminAttached` condition.
![]()
[]()[]()
