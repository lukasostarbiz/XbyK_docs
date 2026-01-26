---
source: https://docs.kentico.com/api/content-management/content-hub-folders
scrape_date: 2026-01-26
---

  * [Home](/api)
  * [Content management](/api/content-management)
  * Content hub folders 


# Content hub folders
  
List of examples:
  * [Dependency injection](#dependency-injection)
    * [Initialize required services](#initialize-required-services)
  * [Content hub folder management](#content-hub-folder-management)
    * [Create content hub folders](#create-content-hub-folders)
    * [Rename content hub folders](#rename-content-hub-folders)
    * [Move content hub folders](#move-content-hub-folders)
    * [Move items into a content hub folder](#move-items-into-a-content-hub-folder)
    * [Delete content hub folders](#delete-content-hub-folders)


## Dependency injection
### Initialize required services
C#
Copy
```
// Initializes all services used within the API examples on this page using dependency injection
private readonly IContentFolderManager contentFolderManager;
private readonly IContentQueryExecutor contentQueryExecutor;

public ContentHubFoldersServices(IContentFolderManagerFactory contentFolderManagerFactory,
                                 IContentQueryExecutor contentQueryExecutor,
                                 IUserInfoProvider userInfoProvider)
{
    // Gets the user responsible for management operations
    // e.g., shown as the creator in 'Created by' fields
    UserInfo user = userInfoProvider.Get("JimmieWonka");
    // Creates an instance of the manager class facilitating content hub folder operations
    contentFolderManager = contentFolderManagerFactory.Create(user.UserID);

    this.contentQueryExecutor = contentQueryExecutor;
}
```

[> Back to list of examples](#toc)
## Content hub folder management
### Create content hub folders
C#
Copy
```
// Gets the "root folder", representing the parent for the first level of the folder tree in the selected workspace
ContentFolderInfo rootFolder = await contentFolderManager.GetRoot(workspaceName: "MyWorkspace");

// Prepares an instance of 'CreateContentFolderParameters', containing the folder's names
// You can leave the folder's code name ('Name' property) unspecified, and it will be generated automatically based on the display name
CreateContentFolderParameters createFolderParams = new CreateContentFolderParameters(displayName: "My folder", name: "MyFolder");

// Creates the new folder under the specified parent folder (or root)
await contentFolderManager.Create(rootFolder.ContentFolderID, createFolderParams);
```

[> Back to list of examples](#toc)
### Rename content hub folders
C#
Copy
```
// Gets a folder with the 'MyFolder' code name
ContentFolderInfo folder = await contentFolderManager.Get("MyFolder");

// Prepares ContentFolderMetadata with an updated display name
ContentFolderMetadata renamedFolderMetadata = new ContentFolderMetadata("My folder renamed", folder.ContentFolderName);

// Renames the folder by updating its metadata
await contentFolderManager.Update(folder.ContentFolderID, renamedFolderMetadata);
```

[> Back to list of examples](#toc)
### Move content hub folders
C#
Copy
```
// Gets a folder with the 'MyFolder' code name
ContentFolderInfo folder = await contentFolderManager.Get("MyFolder");

// Gets a parent folder where 'MyFolder' will be moved
ContentFolderInfo parentFolder = await contentFolderManager.Get("ParentFolder");

// Moves the folder under the new parent folder
await contentFolderManager.Move(folder.ContentFolderID, parentFolder.ContentFolderID);
```

[> Back to list of examples](#toc)
### Move items into a content hub folder
C#
Copy
```
// Gets the ids of all content items of the 'Wonka.CandyStore' type
string STORE_CONTENT_TYPE = "Wonka.CandyStore";

ContentItemQueryBuilder builder = new ContentItemQueryBuilder()
                                        .ForContentType(STORE_CONTENT_TYPE);

var itemIds = await contentQueryExecutor
                .GetResult<int>(builder, rowData =>
                {
                    var contentItemId = rowData.ContentItemID;
                    return contentItemId;
                },
                // Retrieves the latest version of items
                new ContentQueryExecutionOptions() { ForPreview = true });

// Gets a folder with the 'CandyStores' code name
ContentFolderInfo targetFolder = await contentFolderManager.Get("CandyStores");

// Moves the retrieved content items under the new parent folder
await contentFolderManager.MoveItems(targetFolder.ContentFolderID, itemIds);
```

[> Back to list of examples](#toc)
### Delete content hub folders
C#
Copy
```
// Gets a folder with the 'MyFolder' code name
ContentFolderInfo folder = await contentFolderManager.Get("MyFolder");

// Deletes the folder
// All content items stored within remain in the system and can be found directly under 'All content items'
await contentFolderManager.Delete(folder.ContentFolderID);
```

[> Back to list of examples](#toc)
![]()
[]()[]()
