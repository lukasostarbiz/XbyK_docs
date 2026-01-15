# Migrate data and binary files
  * How-to| [ Copy page link ](guides/architecture/upgrade-from-kx13/upgrade-walkthrough/migrate-data-and-binary-files#) | [Get HelpService ID](guides/architecture/upgrade-from-kx13/upgrade-walkthrough/migrate-data-and-binary-files#) | This page is part of a module: [ Upgrading to Xperience by Kentico - Walkthrough ](modules/upgrade-walkthrough)
Core MVC 5


[✖](guides/architecture/upgrade-from-kx13/upgrade-walkthrough/migrate-data-and-binary-files# "Close page link panel") [Copy to clipboard](guides/architecture/upgrade-from-kx13/upgrade-walkthrough/migrate-data-and-binary-files#)
Now, we will migrate the data from the KX13 (source) instance to the XbyK (target) instance. The video below goes over the migration tool configuration and shows how to run the data migration.
In the video we run the data migration in **two iterations** :
  * first to migrate _sites_ only.
  * then to migrate the rest of the data necessary to display the **Contacts** page: _settings keys_ , _page types_ , _pages_ and _forms_.


In your own project, you can run the data migration in **as many iterations as you wish**. To avoid repetitive migration of the same type of object, use the [`--bypass-dependency-check` parameter](https://github.com/Kentico/xperience-by-kentico-kentico-migration-tool/blob/master/Migration.Tool.CLI/README.md#migrate-command-parameters).
We highly recommend creating a **database backup** before each iteration.
Under the video, see the sample configuration files, links, and important remarks mentioned in the video.
Your browser does not support the video tag. 
## Configuration
These are sample configuration files used in the video above.
See the explanation of all the configuration options and more examples in the [Kentico Migration Tool GitHub documentation](https://github.com/Kentico/xperience-by-kentico-kentico-migration-tool/blob/master/Migration.Tool.CLI/README.md#configuration).
JS
**Migration.Tool.CLI/appsettings.json**
Copy
```
...
  "Settings": {
    "MigrationProtocolPath": "<ABSOLUTE_PATH_TO_STORE_THE_-_LOG>\\Migration.Toolkit.Protocol.log",
    "KxConnectionString": "Data Source=<YOUR_DB_SERVER_NAME>;Initial Catalog=<SOURCE_KX13_DB_NAME>;Integrated Security=True;Persist Security Info=False;Connect Timeout=60;Encrypt=False;Current Language=English;",
    "KxCmsDirPath": "<ABSOLUTE_PATH_TO_YOUR_SOURCE_KX13_INSTANCE>\\CMS",
    "XbKDirPath": "<ABSOLUTE_PATH_TO_YOUR_XbyK_REPO_FOLDER>\\DancingGoat.Web",
    "XbyKApiSettings": {
      "ConnectionStrings": {
          "CMSConnectionString": "Data Source=<YOUR_DB_SERVER_NAME>;Initial Catalog=<TARGET_XBYK_DB_NAME>;Integrated Security=True;Persist Security Info=False;Connect Timeout=60;Encrypt=False;Current Language=English;"
      }
    },
    "MigrateOnlyMediaFileInfo": false,
    "MigrateMediaToMediaLibrary": false,
    "LegacyFlatAssetTree": false,
    "AssetRootFolders": {},
    "UseDeprecatedFolderPageType": false,
    "ConvertClassesToContentHub": "",
    "CreateReusableFieldSchemaForClasses": "",
    "OptInFeatures": {
      "QuerySourceInstanceApi": {
        "Enabled": true,
        "Connections": [
          {
            "SourceInstanceUri": "http://localhost:<SOURCE_INSTANCE_PORT>/",
            "Secret": "my_secret"
          }
        ]
      }
    },
...
```

`ToolApiController` copied from the Kentico Migration Tool, _KX13.Extensions.cs_ :
C#
**/DancingGoatCore/Controllers/ToolApiController.cs**
Copy
```
using System.Collections.Generic;
...

public class ToolApiController : Controller
{
    private const string Secret = "my_secret";
    ...
    // the rest of the file stays the same
}
```

Registering the actions of the `ToolApi` controller:
C#
**/DancingGoatCore/Startup.cs**
Copy
```
app.UseEndpoints(endpoints =>
{
    endpoints.Kentico().MapRoutes();

    // added code
    endpoints.MapControllerRoute(
        name: "ToolExtendedFeatures",
        pattern: "{controller}/{action}",
        constraints: new
        {
            controller = "ToolApi"
        }
    );
...
}
...
```

## Migration commands
The migration commands we use in the video:
CMD
**Sites only migration command**
Copy
```
.\Migration.Tool.CLI.exe  migrate --sites
```

CMD
**Remaining data migration command**
Copy
```
.\Migration.Tool.CLI.exe  migrate --custom-modules --users --settings-keys --page-types --pages --forms --bypass-dependency-check
```

## Links and remarks
See the list of [data migration steps in our documentation](https://github.com/Kentico/xperience-by-kentico-kentico-migration-tool?tab=readme-ov-file#quick-start).
For successful data migration, make sure that:
  * Your _source_ instance **is running** during migration.
  * Your _target_ instance **is NOT running** during migration.


Create a **database backup** each time before you run the `migrate` command.
If you run into issues during the data migration process, check out the [Migration Protocol Reference](https://github.com/Kentico/xperience-by-kentico-kentico-migration-tool/blob/master/Migration.Tool.CLI/MIGRATION_PROTOCOL_REFERENCE.md#migration-protocol-reference) to troubleshoot common errors.
In the video, we utilize [Source instance API discovery](https://github.com/Kentico/xperience-by-kentico-kentico-migration-tool/blob/master/Migration.Tool.CLI/README.md#api-discovery-setup) to ensure the migrated Page Builder data can work with the new [Editing components in Xperience by Kentico](documentation/developers-and-admins/upgrade-to-xperience-by-kentico/editing-components-in-xperience-by-kentico) rather than in the legacy compatibility mode. While it is not a mandatory step, **we highly recommend this approach.**
### Walkthrough progress
Follow to the next article where you'll learn about [global code that you’ll need to adjust](guides/architecture/upgrade-from-kx13/upgrade-walkthrough/adjust-global-code).