---
source: https://docs.kentico.com/guides/architecture/media-libraries-migration-guidance
scrape_date: 2026-01-26
---

  * [Home](/guides)
  * [Architecture](/guides/architecture)
  * Media library migration 


# Media library migration
Media libraries have been officially sunset, and their related API members have been marked as obsolete. Support will continue for a year (until July 24, 2026), after which the feature and all associated APIs will be removed. [Content item assets](/documentation/business-users/content-hub/content-item-assets) are the long-term replacement, offering a more structured and future-ready way to manage files in Xperience by Kentico.
This guide walks you through migrating media library files to content item assets in Xperience by Kentico projects. It covers how assets are transformed, which metadata is preserved (such as GUIDs, custom fields, and system fields), and how legacy URLs are handled during the transition.
**Migrate media libraries from Kentico Xperience 13**
For information about migrating media libraries from Kentico Xperience 13 to Xperience by Kentico, see [Upgrade from Kentico Xperience 13](/guides/upgrade-to-xbyk/upgrade-from-kx13) or the [upgrade walkthrough guide](/guides/upgrade-to-xbyk/upgrade-walkthrough).
## Migration overview
When you migrate your media library files, each one is transformed into a content item asset within a dedicated [workspace](/documentation/developers-and-admins/configuration/users/role-management/workspaces) in the Content hub application.
The full migration involves the following high-level steps:
  1. [Migrate media library files to Content hub](#migrate-media-library-files-to-content-hub)
  2. [Create a database backup](#create-a-database-backup)
  3. [Set up your development environment](#set-up-your-development-environment)
  4. [Migrate fields referencing media files](#migrate-fields-referencing-media-library-files)
  5. [Migrate codebase](#migrate-codebase)
  6. [Deploy](#deploy)
  7. [Delete media libraries](#delete-media-libraries)


[![Migration overview](/docsassets/guides/media-libraries-migration-guidance/migration_overview.png)](/docsassets/guides/media-libraries-migration-guidance/migration_overview.png)
### How media library files are transformed
Each media library file becomes a content item of type `MediaLibrariesMigration.MigratedMediaFile`.
The newly created content items:
  * Retain their original unique identifier (GUID).
  * Preserve all [custom fields](/documentation/developers-and-admins/customization/object-types/extend-system-object-types).
  * Include all system fields.


### Folder structure and workspaces
Migration places the files in the _Migration from media libraries_ [workspace](/documentation/developers-and-admins/configuration/users/role-management/workspaces), which is created when the migration is first run in the **Media libraries** application.
The folder hierarchy in the migration workspace mirrors the original structure in the [media libraries](/documentation/business-users/media-libraries). For example, a file in `~/AcmeMediaLibrary/Icons/` will be located under `~/AcmeMediaLibrary/Icons/` in the _Migration from media libraries_ workspace.
### Linking and redirect behavior
All existing `~/getmedia/...` links are automatically redirected to their `~/getcontentasset/...` counterparts. This ensures that legacy links continue to function without modification.
Legacy `~/getmedia/...` links leading to migrated content item assets do not support [URL-based image resizing](/documentation/developers-and-admins/development/content-retrieval/retrieve-content-from-media-libraries#resize-images).
[Usage](/documentation/business-users/content-hub/content-items#track-usage-of-content-items) of media library files (now content items) linked through `~/getmedia/...` links is tracked only for the default [language](/documentation/developers-and-admins/configuration/languages) variant of the content items.
_Direct path_ media library URLs (e.g., `~/AcmeMediaLibrary/coffee_cup.png`) are not supported for redirections.
## Migration process
The migration from media library files to content item assets involves a combination of tool-assisted and manual steps. Follow these steps in order to ensure a complete and consistent migration.
### Migrate media library files to Content hub
**Important** : To avoid the need to include the newly created content type and its items in your deployment package, run the migration directly in your target environment (e.g., Production). This is especially important for enviroments where the size of your deployment package has a limit, for example the [SaaS](/documentation/developers-and-admins/saas) environment.
This migration step is done in the administration interface of your application and can only be performed by users with the _Administrator_ [role](/documentation/developers-and-admins/configuration/users/role-management) or a role with the _Migrate media library_ permission for the _Media libraries_ application.
Each media library file is copied into a content item of type `MediaLibrariesMigration.MigratedMediaFile` in the project’s default [language](/documentation/developers-and-admins/configuration/languages), and retains its unique identifier (GUID). The items are created in a _Migration from media libraries_ [workspace](/documentation/developers-and-admins/configuration/users/role-management/workspaces), which is created when running the migration for the first time. The folder structure in the workspace is identical to the folder structure in the original media libraries.
This is a non-destructive migration – it does not remove or alter the original media library files. Instead, it creates a copy in the Content hub application (in the _Migration from media libraries_ workspace). As a result, any existing functionality or features in your application that rely on media libraries will continue to work without modification.
This migration step migrates only media libraries that have not been migrated yet, ensuring no duplicate content is created even if the step is rerun. Users who had permissions for the _Media libraries_ application keep the same level of access to files in the _Migration from media libraries_ workspace. This means that:
  * Users with the _Manage media library_ permission for the _Media libraries_ application get full permissions in the migration workspace.
  * Users with other permissions (_View_ , _Create_ , _Update_ , _Delete_) for working with media libraries get _View_ permission in the migration workspace.


To migrate media libraries along with their files:
  1. Open the **Media libraries** application.
  2. Migrate your media libraries.  
[![Migrate media libraries to Content hub](/docsassets/guides/media-libraries-migration-guidance/migrate_media_libraries.png)](/docsassets/guides/media-libraries-migration-guidance/migrate_media_libraries.png)
After the migration finishes you can check its result in the **Event log** application.
  3. You have successfully migrated your media libraries including their files.


[![Example migrated media libraries to Content hub](/docsassets/guides/media-libraries-migration-guidance/migrated_media_libraries.png)](/docsassets/guides/media-libraries-migration-guidance/migrated_media_libraries.png)
### Create a database backup
Before proceeding with any further modifications, create a **full database backup** of your target environment where you performed the migration of media library files. This allows you to recreate the state of your target’s database in your local environment and you can roll back safely if needed during the fields or codebase migration.
**Content freeze**
After your backup is created, make sure no changes are made to your existing media library files, links to media library files are not changed, and no changes are made to content types with a _Media files_ field. This ensures no media library files or their references are lost during the following migration steps.
### Set up your development environment
Restore your local (or other development) environment’s database using the [database backup](#create-a-database-backup) created in the previous step to replicate the target environment’s database in your local environment. You’ll use this local environment to carry out the upcoming migration steps safely before [deploying](#deploy) your changes back to the target environment.
### Migrate fields referencing media library files
Before you migrate the fields that reference your media files, ensure that **all** media files in your media library have been successfully migrated and that your Xperience application is not running.
The field reference migration is handled entirely through the [.NET Command Line Interface (CLI)](https://learn.microsoft.com/en-us/dotnet/core/tools/).
The migration CLI script migrates database definitions of fields, including their data, which reference media files. This means that fields that referenced media files are changed to reference content items on the database level. The [codebase migration](#migrate-codebase) is done separately.
The fields of the _Media files_ [data type](/documentation/developers-and-admins/customization/field-editor/data-type-management) are migrated to the _Pages and reusable content_ data type. The original _Media file selector_ [admin UI form component](/documentation/developers-and-admins/customization/extend-the-administration-interface/ui-form-components) for the field is also changed to the _Combined content selector_. After migration, the allowed content type for the _Combined content selector_ for the migrated fields is automatically set to the new replacement _Migrated media file_ content type. The value set for the maximum number of files is also migrated, which corresponds to the `MaximumAssets` configuration property in media file selector and `MaximumItems`in combined content selector.
The migrated fields include:
  * Content type fields
  * Custom class fields
  * Custom fields in [extendable system object types](/documentation/developers-and-admins/customization/object-types/extend-system-object-types)
  * Widget properties with the `AssetSelectorComponent` as the editing component
  * Module class UI forms


References to media files in [Page Builder](/documentation/developers-and-admins/development/builders/page-builder) and [Email Builder](/documentation/developers-and-admins/development/builders/email-builder) are not migrated and need to be recalculated using the _Track missing content item usages_ [scheduled task](/documentation/developers-and-admins/customization/scheduled-tasks).
The migration script is safely rerunnable without the risk of creating duplicate data. If a field fails to migrate (e.g., because of a collision in field names), an error is logged in the command line, and the process continues.
To begin the migration, open the command line and run the following command:
CMD
Copy
```
dotnet run --kxp-migrate-media-file-fields
```

### Migrate codebase
Review your project’s codebase and manually replace references to now obsolete media library API members.
We’ve suggested a replacement for each outdated API to make your migration easier. These are just recommendations – feel free to take a different approach if it better suits your project.
Original |  Target |  Description  
---|---|---  
MediaFileInfo |  Generated model class of a given [content type](/documentation/developers-and-admins/development/content-types) |  The content type needs to have a content item asset field.  
MediaLibraryInfo |  ContentFolderInfo |   
IInfoProvider<MediaFileInfo> |  IContentItemManager |  You can use the `IContentItemManager` API to manage content items programmatically, offering similar functionality as the obsolete [info provider](/documentation/developers-and-admins/api/database-table-api#generic-provider-class-iinfoprovidertinfo). See [Content items](/api/content-management/content-items) for examples of usage.  
IInfoProvider<MediaLibraryInfo> |  IContentFolderManager |  You can use the `IContentFolderManager` API to manage content folders programmatically, offering similar functionality as the obsolete [info provider](/documentation/developers-and-admins/api/database-table-api#generic-provider-class-iinfoprovidertinfo). See [Content hub folders](/api/content-management/content-hub-folders) for examples of usage.  
AssetRelatedItem |  ContentItemReference |   
AssetSelectorComponent |  ContentItemSelectorComponent |  Use the [combined content selector](/documentation/developers-and-admins/customization/extend-the-administration-interface/ui-form-components/reference-admin-ui-form-components#combined-content-selector) admin UI form component instead of the obsolete media file selector.  
IMediaFileUrlRetriever |  No direct replacement |  There’s no built-in way to resolve URLs of retrieved assets or to resize images via URL parameters during migration. But with a bit of custom development, you can create a similar solution that fits your needs.  
IMediaFileUrl |  No direct replacement |  There is no direct replacement. However, URLs of assets can be obtained from properties of the `ContentItemAsset` object. See [Retrieve content items](/documentation/developers-and-admins/development/content-retrieval/retrieve-content-items) for more information.  
IMediaProtectionService |  No direct replacement |  There is no direct replacement. Content items do not support URL-based image resizing.  
ObjectQuery().ForAssets() |  [Content item query](/documentation/developers-and-admins/api/content-item-api/reference-content-item-query) |  The same functionality as the `ForAssets` object query extension method can be achieved using content item query, for example by using the `Where` parameterization method.  
### Deploy
Once all fields and their data have been successfully migrated and corresponding fixes in the codebase were made, deploy your changes to the desired environment. See [Deployment](/documentation/developers-and-admins/deployment) for information about deploying to different environments.
When using the [continuous deployment](/documentation/developers-and-admins/ci-cd/continuous-deployment) feature or deploying to the [SaaS](/documentation/developers-and-admins/saas) environment, ensure the newly created content type (`MediaLibrariesMigration.MigratedMediaFile`) is excluded when serializing your data to a deployment package. Additionally, set the [CD restore mode](/documentation/developers-and-admins/ci-cd/configure-ci-cd-repositories#configure-cd-restore-mode) to at least `CreateUpdate` during serialization.
### Delete media libraries
After you deploy your application and confirm that all assets are working as intended, you can delete the original media libraries from your environment.
You can delete a media library using the bin icon (_Media libraries_ application.
When you delete all your media libraries, make sure to update [roles](/documentation/developers-and-admins/configuration/users/role-management) which have permissions to them to prevent others from accidentaly creating new media libraries or files.
To remove the permissions for the _Media libraries_ application from a given role:
  1. Open the **Role management** application.
  2. Select the role you want to remove the permissions for.
  3. Switch to the **Permissions** tab of the selected role.
  4. **Delete** permissions for the _Media libraries_ application.


You have successfully removed permissions for the _Media libraries_ application for a given role.
![]()
[]()[]()
