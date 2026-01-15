# Install, configure and develop a SaaS-ready project
  * [ Copy page link ](guides/configuration/saas/saas-walkthrough/saas-install-configure-project#) | [Get HelpService ID](guides/configuration/saas/saas-walkthrough/saas-install-configure-project#) | This page is part of a module: [ Deploy to SaaS step-by-step ](modules/deploy-to-saas-step-by-step)
Core MVC 5


[✖](guides/configuration/saas/saas-walkthrough/saas-install-configure-project# "Close page link panel") [Copy to clipboard](guides/configuration/saas/saas-walkthrough/saas-install-configure-project#)
Now that you’ve set up your Xperience Portal, let’s prepare your project for Kentico SaaS deployment.
## Before you start
To follow along, we recommend your machine has the following installed:
  * [.NET 8.0](https://dotnet.microsoft.com/en-us/download/dotnet/8.0)
  * Microsoft SQL Server 2022 (or compatible version, such as the free [SQL Server Express Edition](https://www.microsoft.com/en-us/download/details.aspx?id=104781))
  * an **IDE** that supports ASP.NET Core development, such as [Visual Studio](https://visualstudio.microsoft.com/) or [Visual Studio Code](https://code.visualstudio.com/)


## Install SaaS-ready project
Kentico provides an easy way to ensure all the default configuration is in place with our .NET templates and installation parameters.
First, [check your existing Kentico template versions](guides/development/get-started/install-a-specific-version-of-xperience-by-kentico#check-your-existing-template-versions) and [install the desired one](guides/development/get-started/install-a-specific-version-of-xperience-by-kentico#install-the-required-version-of-templates).
Then, create a project using the _kentico-xperience-sample-mvc_ template (the Dancing Goat sample site).
The [`--cloud` parameter](documentation/developers-and-admins/installation#create-a-project) will automatically add SaaS-specific sample configuration to your newly generated project.
CMD
**Create a new cloud-ready project**
Copy
```
dotnet new kentico-xperience-sample-mvc --cloud -n DancingGoat.Web
```

You can use the `--cloud` parameter with any of the Kentico templates. For example, for your own project, you may want to use the empty boilerplate (_kentico-xperience-mvc_) and either develop your project from scratch or migrate your existing solution into it.
## Create and connect the database
Obtain a license key from your Xperience Portal (you only need this for local development):
  1. Navigate to the **License key generator** page.
  2. Select the Xperience by Kentico version compatible with your project.
  3. Click **Generate license key**.
  4. Copy-paste the key and store it in a text file (e.g., _license.txt_) on your local machine.

Your browser does not support the video tag. 
Make sure to **store the key before you click _OK_**. The generated key is NOT stored in the Portal so you will not be able to access it later.
Next, obtain the hash string salt for your project from the Xperience Portal dashboard: [![Hash string salt in Xperience Portal](docsassets/guides/saas-install-configure-project/hash-string-salt.png)](https://docs.kentico.com/docsassets/guides/saas-install-configure-project/hash-string-salt.png)
Your browser does not support the video tag. 
Run the following command to create and connect a database to your project.
CMD
**Connect a database**
Copy
```
dotnet kentico-xperience-dbmanager -- -s "<sql_server_name>" -a "dg123*" -d "Xperience.DancingGoat" --hash-string-salt "<your_Xperience_Portal_hash_string_salt>" --license-file "<path_to_your_license_txt_file>"
```

Remember to replace the `<sql_server_name>` and `<path_to_your_license_txt_file>` placeholders with values relevant to your local environment.
The **hash string salt value has to match** the value from your Xperience Portal dashboard.
For more information about database creation and the command’s parameters, visit [our documentation](documentation/developers-and-admins/installation#create-the-project-database).
After successful database installation, run the project. You should see the Dancing Goat sample site Home page. Visit the _/admin_ path and sign in to the administration to ensure everything installed correctly.
[![Dancing Goat project administration showing the Home screen of the website channel](docsassets/guides/saas-install-configure-project/dancing-goat-admin.jpg)](https://docs.kentico.com/docsassets/guides/saas-install-configure-project/dancing-goat-admin.jpg)
## Enable Continuous Integration (CI) - optional
Enabling [Continuous Integration](documentation/developers-and-admins/ci-cd/continuous-integration) is not a must from the perspective of Kentico SaaS; it’s more of the choice of your project’s development team.
If you wish to enable CI for your project, sign in to your administration interface, navigate to the **Settings** application (under **Configuration** in the dashboard). Under **Synchronization - > Continuous Integration** check the **Enable Continuous Integration** checkbox.
Save.
[![How to enable CI in the administration interface](docsassets/guides/saas-install-configure-project/enable-ci.png)](https://docs.kentico.com/docsassets/guides/saas-install-configure-project/enable-ci.png)
Now you can use the `--kxp-ci-store` and `--kxp-ci-restore` commands as explained in [our documentation](documentation/developers-and-admins/ci-cd/continuous-integration).
## Examine and adjust the SaaS-specific configuration
Thanks to the `--cloud` parameter, your new project now includes the necessary NuGet packages, including the [Kentico.Xperience.Cloud](https://www.nuget.org/packages/Kentico.Xperience.Cloud) and all the default SaaS-specific configuration:
  * _repository.config_ - a file holding the default Continuous Deployment (CD) configuration
  * _Program.cs_ - includes cloud-specific app configurations
  * _Export-DeploymentPackage.ps1_ - a script to generate the future deployment package
  * _StorageInitializationModule.cs_ - a file holding the Azure Blob storage mapping


[![Screenshot from an IDE \(Visual Studio\) showing the project structure, pointing out cloud-specific default configuration](docsassets/guides/saas-install-configure-project/default-config.png)](https://docs.kentico.com/docsassets/guides/saas-install-configure-project/default-config.png)
Let’s examine the default configuration one-by-one.
### Continuous Deployment
[Continuous deployment (CD)](documentation/developers-and-admins/ci-cd/continuous-deployment) in Xperience converts content and objects from the database into XML files. These files are deployed with your code and restored in target environments through automated pipelines. This is a mechanism that Kentico SaaS uses for project data deployment.
**Content sync**
After your initial deployment, you don’t necessarily need CD every time you want to move content changes between SaaS environments. For [supported content](documentation/business-users/content-sync#supported-content), you can use [content sync](documentation/business-users/content-sync) to deploy updates (_Advanced_ license required).
See [Content sync configuration](documentation/developers-and-admins/configuration/content-sync-configuration) for configuration details. If the updated content is not supported by content sync or if the feature is not available, deploy the changes via a new [deployment package](documentation/developers-and-admins/deployment/deploy-to-the-saas-environment#deploy-with-a-deployment-package).
Take a look at the _$CDRepository_ folder and its _repository.config_ file.
The _repository.config_ holds the default CD configuration in an XML format. It [includes and excludes objects](documentation/developers-and-admins/ci-cd/configure-ci-cd-repositories), determining which ones will be a part of the deployment in the Xperience portal.
The object types are identified by their **CI/CD folder names**. Visit [our documentation](documentation/developers-and-admins/ci-cd/reference-ci-cd-object-types) for a list of objects supported by CD, their [CI/CD folder names](documentation/developers-and-admins/ci-cd/reference-ci-cd-object-types#general), and additional notes.
Your file right now is set up correctly for the Dancing Goat sample site project and should look like this:
XML
**repository.config**
Copy
```
<?xml version="1.0" encoding="utf-8"?>
<RepositoryConfiguration xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance">

  <!-- Specifies which types of object operations are performed in the target database when restoring the repository:
     Create - only creates new objects. Never deletes or modifies existing objects.
     CreateUpdate - creates new objects and updates existing objects. Never deletes objects.
       Full - creates new objects, updates existing objects, deletes objects that do not exist in the repository.
     -->
  <RestoreMode>CreateUpdate</RestoreMode>

  <!-- After changing the included or excluded object types, you need to run serialization for all objects to bring
       the repository folder into the required state. Use the CLI and execute the store command for Continuous Integration or Deployment.
     Always synchronize the repository.config along with the other content of the repository folder. -->

  <!-- To learn which object types can be used in the IncludedObjectTypes and ExcludedObjectTypes definitions,
       visit the documentation: https://devnet.kentico.com/CMSPages/DocLinkMapper.ashx?version=xp&link=ci_object_types -->

  <!-- If one or more main object types are specified, only objects of the given type and their child types are processed.
       All supported object types are included if empty. -->
  <IncludedObjectTypes>
    <ObjectType>cms.class</ObjectType>
    <ObjectType>cms.consent</ObjectType>
    <ObjectType>cms.contenttype</ObjectType>
    <ObjectType>cms.contentlanguage</ObjectType>
    <ObjectType>cms.scheduledtaskconfiguration</ObjectType>
    <ObjectType>cms.settingscategory</ObjectType>
    <ObjectType>cms.settingskey</ObjectType>
    <ObjectType>cms.user</ObjectType>
    <ObjectType>cms.role</ObjectType>
    <ObjectType>cms.channel</ObjectType>
    <ObjectType>cms.websitechannel</ObjectType>
    <ObjectType>cms.websitecaptchasettings</ObjectType>
    <ObjectType>cms.contentitem</ObjectType>
    <ObjectType>cms.pagetemplateconfiguration</ObjectType>
    <ObjectType>cms.contentfolder</ObjectType>
    <ObjectType>cms.smartfolder</ObjectType>
    <ObjectType>cms.standardmediadimensions</ObjectType>
    <ObjectType>cms.workspace</ObjectType>
    <ObjectType>cms.workspacedatapermission</ObjectType>
    <ObjectType>cms.allowedchildcontenttype</ObjectType>

    <ObjectType>cms.headlesschannel</ObjectType>
    <ObjectType>cms.headlessitem</ObjectType>

    <ObjectType>media.library</ObjectType>
    <ObjectType>media.file</ObjectType>

    <ObjectType>om.activitytype</ObjectType>
    <ObjectType>om.recipientlist</ObjectType>
    <ObjectType>om.contactgroup</ObjectType>
    <ObjectType>om.customerjourneyhistory</ObjectType>
    <ObjectType>om.customerjourney</ObjectType>
    <ObjectType>om.customerjourneystage</ObjectType>
    <ObjectType>cms.formclass</ObjectType>
    <ObjectType>cms.formfeaturedfield</ObjectType>

    <ObjectType>emaillibrary.emailchannel</ObjectType>
    <ObjectType>emaillibrary.emailchannelsender</ObjectType>
    <ObjectType>emaillibrary.emailconfiguration</ObjectType>
    <ObjectType>emaillibrary.emailtemplatecontenttype</ObjectType>
    <ObjectType>emaillibrary.recipientlistsettings</ObjectType>
    <ObjectType>emaillibrary.sendconfiguration</ObjectType>
    <ObjectType>emaillibrary.emailtemplate</ObjectType>

    <ObjectType>ma.automationprocess</ObjectType>
    <ObjectType>ma.automationstep</ObjectType>
    <ObjectType>ma.automationaction</ObjectType>
    <ObjectType>ma.automationtemplate</ObjectType>
    <ObjectType>cms.objectworkflowtrigger</ObjectType>
    <ObjectType>cms.workflowtransition</ObjectType>

    <ObjectType>cms.webpageitem</ObjectType>
    <ObjectType>cms.webpageurlpath</ObjectType>
    <ObjectType>cms.webpageformerurlpath</ObjectType>
    <ObjectType>om.trackedwebsite</ObjectType>
    <ObjectType>cms.form</ObjectType>

    <ObjectType>cms.contentworkflow</ObjectType>
    <ObjectType>cms.contentworkflowcontenttype</ObjectType>
    <ObjectType>cms.contentworkflowrole</ObjectType>
    <ObjectType>cms.contentworkflowstep</ObjectType>
    <ObjectType>cms.contentworkflowsteprole</ObjectType>
    <ObjectType>cms.taxonomy</ObjectType>
    <ObjectType>cms.tag</ObjectType>
    <ObjectType>cms.notificationemailtemplate</ObjectType>
    <ObjectType>cms.notificationemail</ObjectType>
    <ObjectType>cms.notificationemailform</ObjectType>
    <ObjectType>cms.notificationemailuser</ObjectType>

    <ObjectType>cms.webpagescope</ObjectType>
    <ObjectType>cms.webpagescopecontenttype</ObjectType>
  </IncludedObjectTypes>

  <!-- Excluded object types filter the set of included object types. The specified object types are excluded from processing.
     May include both main and child object types. -->
  <ExcludedObjectTypes>
  </ExcludedObjectTypes>

  <!-- You can exclude objects from processing by adding included or excluded code names for any object type.
       Use the % wildcard at the start or end of the values to include or exclude multiple objects based on code name prefixes or suffixes.
       You can add multiple code name values separated by semicolons for each object type. -->
  <ObjectFilters>
    <!-- Ensures serialization for reusable field schemas. -->
    <IncludedCodeNames ObjectType="cms.class">cms.contentitemcommondata</IncludedCodeNames>
    <!-- If one or more code names are included, only objects matching the specified code names are included. Other objects are not processed.
         All objects are included if empty. -->
    <!-- <IncludedCodeNames>IncludedCodeNameA;IncludedCodeNameB</IncludedCodeNames> -->
    <!-- <IncludedCodeNames ObjectType="ObjectTypeB">IncludedCodeNamePrefix%;%IncludedCodeNameSuffix</IncludedCodeNames> -->

    <!-- Excludes license key settings -->
    <ExcludedCodeNames ObjectType="cms.settingskey">CMSInstanceFriendlyName;CMSInstanceID</ExcludedCodeNames>

    <!-- Excluded code names add further filtering for the set of included objects. Objects that match the specified code names are excluded from processing. -->
  </ObjectFilters>

</RepositoryConfiguration>
```

For this series, we don’t need to adjust the CD configuration. If you decide to modify the _repository.config_ file in your own project, be careful about including all the necessary objects.
If you realize your deployment is missing anything (e.g., can’t see pages), check your _repository.config_. It may be excluding or including some objects incorrectly.
### Identification of your working environment
Sometimes, you may need to run certain code only in a specific environment. You can do this using the [environment identification extension methods](documentation/developers-and-admins/configuration/saas-configuration#environment-identification-extension-methods).
Take a look at the _Program.cs_ file in the root of your project to see an example usage.
C#
**Program.cs**
Copy
```
...

// the following code will only execute in cloud (QA, UAT, PROD or custom) environment
// but never when you run the solution locally
if (builder.Environment.IsQa()
    || builder.Environment.IsUat()
    || builder.Environment.IsProduction()
    || builder.Environment.IsEnvironment(CloudEnvironments.Custom))
{
    builder.Services.AddKenticoCloud(builder.Configuration);
    builder.Services.AddXperienceCloudSendGrid(builder.Configuration);
    builder.Services.AddXperienceCloudDataProtection(builder.Configuration);
}

builder.Services.AddKentico(features =>
{
...
```

If you run the project locally and place a breakpoint inside the `if` statement, you can observe that it will never get hit.
For this series, we don’t need to change this configuration. However, if your Xperience Portal does not have a UAT environment (e.g., subscription Level 1), you can remove the `builder.Environment.IsUat()` condition.
If you do make changes, remember to rebuild the solution for them to take effect.
### Microsoft Application Insights
This integration allows you to see errors and warnings from Xperience event log and .NET exceptions (including ones that occur before the Xperience app is fully running) in the Xperience Portal.
Our generated project already has the [Microsoft Application Insights enabled](documentation/developers-and-admins/configuration/saas-configuration#microsoft-application-insights-integration) - you can see the line of code in the _Program.cs_ file, when building the web application:
C#
**Program.cs**
Copy
```
...
builder.Services.AddXperienceCloudApplicationInsights(builder.Configuration);
...
```

You can turn off the insights entirely by simply removing the line.
If your project uses client-side telemetry, you can add the logs by following the [Microsoft documentation](https://learn.microsoft.com/en-us/azure/azure-monitor/app/asp-net-core#enable-client-side-telemetry-for-web-applications).
If you make changes to this setting, rebuild the solution for them to take effect.
### SendGrid integration
The [SendGrid integration](documentation/developers-and-admins/configuration/email-configuration#managed-sendgrid-integration) to send emails is also a part of your generated _Program.cs_ file:
C#
**Program.cs**
Copy
```
if (builder.Environment.IsQa()
    || builder.Environment.IsUat()
    || builder.Environment.IsProduction()
    || builder.Environment.IsEnvironment(CloudEnvironments.Custom))
{
    ...
    builder.Services.AddXperienceCloudSendGrid(builder.Configuration);
    ...
}
```

We are using the [environment identification extension](guides/configuration/saas/saas-walkthrough/saas-install-configure-project#identification-of-your-working-environment) methods mentioned above to enable SendGrid only in the SaaS environment, as it will not work locally.
Read more about the [SendGrid integration in our documentation](documentation/developers-and-admins/configuration/email-configuration#managed-sendgrid-integration).
### Cloudflare CDN configuration
Kentico SaaS integrates with the [Cloudflare CDN](documentation/developers-and-admins/configuration/saas-configuration#cloudflare-cdn) to perform caching.
For this series, you can stick to the default caching configuration, which you can read more about in [our documentation](documentation/developers-and-admins/configuration/saas-configuration#cloudflare-cdn).
If you’d like to override the default caching, you can, for example, set the TTL to a specific number by setting the _Cache-Control_ HTTP header for each HTTP response. See an example in the documentation.
### Azure Blob storage folder mapping
By default, Kentico SaaS uses Azure Blob storage to store media or binary files.
Any project installed with the `--cloud` parameter, like ours, contains a _StorageInitializationModule.cs_ file that defines the mapping of the folder where these files are stored.
Take a look at the `OnInit` method of your _StorageInitializationModule.cs_ that creates the mapping on application start:
C#
**StorageInitializationModule.cs**
Copy
```
...
protected override void OnInit()
{
    base.OnInit();

    if (Environment.IsQa() || Environment.IsUat() || Environment.IsProduction())
    {
        // Maps the assets directory (e.g. media files) to the Azure storage provider
        MapAzureStoragePath($"~/assets/");
    }
    else
    {
        // Maps contents of the assets directory which are not handled by the CD tool (media files)
        // to the dedicated local folder.
        MapLocalStoragePath($"~/assets/media");
    }
}
...
```

Unless you use a different folder structure for media files than Xperience by Kentico’s default, you don’t need to change this configuration at all.
### Custom static page to display when app is unavailable
In case your app is temporarily offline, it is a good practice to define a [custom static page](documentation/developers-and-admins/configuration/saas-configuration#define-a-custom-app-offline-file) that the visitor will see instead of a default generic page.
Add this file to your project’s root (including the ’_’ suffix). Feel free to modify the structure or style to your linking or branding needs:
HTML
**app_offline.htm_**
Copy
```
<!DOCTYPE html>
<html>
<head>
  <meta charset="UTF-8">
  <title>Offline</title>
</head>
<body style="margin:3em;font-family:sans-serif">
  <h2>Offline</h2>
  <p>This site is offline for maintenance.</p>
</body>
</html>
```

## Develop your solution
Your project is now ready from the configuration standpoint.
If you are following along using the Dancing Goat sample site, there’s no need for any code adjustments at this time. In your own solution, you can now perform any development, adjustments, or data/code migration, before you move to the next step - the initial project deployment.
## Continue learning
When you’re ready, move on to the next page: [Deploy your solution to Xperience Portal](guides/configuration/saas/saas-walkthrough/saas-initial-deployment).