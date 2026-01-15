# Deploy Xperience without the administration interface
  * [ Copy page link ](modules/private-cloud-deployment/deploy-without-the-administration#) | [Get HelpService ID](modules/private-cloud-deployment/deploy-without-the-administration#)
Core MVC 5


[✖](modules/private-cloud-deployment/deploy-without-the-administration# "Close page link panel") [Copy to clipboard](modules/private-cloud-deployment/deploy-without-the-administration#)
Deployment without the Xperience by Kentico administration is currently not supported when [deploying to SaaS](documentation/developers-and-admins/deployment/deploy-to-the-saas-environment). The scenario described on this page applies only to [private cloud deployments](modules/private-cloud-deployment/deploy-to-private-cloud).
Xperience and all related dependencies are installed via [NuGet packages](documentation/developers-and-admins/development/website-development-basics/configure-new-projects/xperience-by-kentico-nuget-packages). The installed packages dictate the features available to each project. However, not all packages are required for Xperience sites to run in production environments.
The Xperience administration interface (installed via the **Kentico.Xperience.Admin** NuGet package) and all related customizations can be removed when publishing to production. In these types of environments, you can have:
  * One Xperience instance **with** the administration interface. Access to this instance is restricted only to authorized users (i.e., placed behind a firewall or on a private network). This is where content editors and other users work with the system.
  * Another Xperience instance **without** the administration interface. This is the instance with which regular live site visitors interact.
  * Both instances [connect to the same database](documentation/developers-and-admins/configuration/auto-scaling-support).


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
**Xperience application's .csproj file**
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
**Xperience application's .csproj file**
Copy
```
<ItemGroup>
    ...
    <!-- If the specified condition evaluates to true (true by default), the project maintains this package reference -->
    <PackageReference Include="kentico.xperience.admin" Version="..." Condition="'$(AdminAttached)' == 'true'" />
</ItemGroup>
```

  3. Follow a similar process to remove any custom code when [customizing the administration interface](documentation/developers-and-admins/customization/extend-the-administration-interface) or some of its [components](documentation/developers-and-admins/configuration/rich-text-editor-configuration). Assuming your code follows best practices for admin UI customization outlined in [Admin UI customization model overview](documentation/developers-and-admins/customization/extend-the-administration-interface/admin-ui-customization-model-overview), your administration-related code is located in isolated assemblies/NuGet packages, making the process straightforward. Repeatedly add the `Condition` attribute to all relevant references:
XML
**Xperience application's .csproj file**
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

You can also invert this condition to run code only when the administration is detached.
  4. When you need to publish the project without admin UI packages and dependencies, pass `AdminAttached=false` as a parameter to MSBuild:
CMD
Copy
```
dotnet publish -c Release -p:AdminAttached=false --output <OUTPUT_DIRECTORY>
```



The output directory now contains the published application minus the dependencies marked by the `AdminAttached` condition.
[ Previous page ](modules/private-cloud-deployment/update-xperience-version)
5 of 7
[ Mark complete and continue ](modules/private-cloud-deployment/deploy-to-private-cloud)
  * [Community Questions & Answers](https://community.kentico.com/q-and-a)
  * [Contact support](https://community.kentico.com/support)


### Cookie consent
We use necessary [cookies](https://www.kentico.com/cookies-policy) to run our website and improve your experience while browsing. Additional cookies are only used with your consent. You may revoke your consent on the [Cookies Policy](https://www.kentico.com/cookies-policy) page or in your browser at any time. 
ACCEPT ALL  [Configure](https://www.kentico.com/cookies-policy)
USE ONLY NECESSARY 
![](https://docs.kentico.com/modules/private-cloud-deployment/deploy-without-the-administration)
[](https://docs.kentico.com/modules/private-cloud-deployment/deploy-without-the-administration)[](https://docs.kentico.com/modules/private-cloud-deployment/deploy-without-the-administration)