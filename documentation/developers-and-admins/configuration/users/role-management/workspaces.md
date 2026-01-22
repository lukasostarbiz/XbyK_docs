---
source: https://docs.kentico.com/documentation/developers-and-admins/configuration/users/role-management/workspaces
scrape_date: 2026-01-22
---

  * [Home](/documentation)
  * [Developers and admins](/documentation/developers-and-admins)
  * [Configuration](/documentation/developers-and-admins/configuration)
  * [Users](/documentation/developers-and-admins/configuration/users)
  * [Role management](/documentation/developers-and-admins/configuration/users/role-management)
  * Workspaces 


# Workspaces
Workspaces allow you to segregate content into distinct, manageable units, improving content management and access control. This separation ensures that users can efficiently locate and manage content. Additionally, you can [assign permissions to roles](/documentation/developers-and-admins/configuration/users/role-management#assign-permissions-to-roles) for each application scoped under a given workspace.
Workspaces prevent editors from accidentally affecting the work of others by enabling teams to create dedicated areas for different projects. This structure ensures that only authorized users can access sensitive content, reducing the risk of data breaches.
## Default workspace
The system uses the _Default workspace_ with the _KenticoDefault_ code name. This workspace ensures the existence of at least one workspace during the creation of new projects and support for the migration of content and permissions for applications newly scoped under workspaces.
In case you don’t need to split up your content into multiple workspaces (e.g., in small projects), you can keep using the _Default workspace_. Otherwise we recommend either renaming or replacing the workspace with new ones that better reflect your project’s needs.
If future updates introduce workspace support for additional applications, all the application content is assigned to the workspace with the _KenticoDefault_ identifier. If such a workspace doesn’t exist (was renamed or deleted), it’s created with the _Default workspace_ display name as part of the update.
Roles with permissions assigned to an application that is newly workspace-scoped have the same permissions assigned for the application in the _Default workspace_ after the update (e.g., a role with the _View_ permission for the [Content hub](/documentation/business-users/content-hub) has the _View_ permission for Content hub in the _Default workspace_ after the update).
Only a workspace with the _KenticoDefault_ code name can work with obsolete API where a workspace parameter is not specified. Deleting the workspace or changing its code name will invalidate your code that creates objects scoped under workspaces without a specified workspace. Before modifying the workspace, update your code that creates workspace-scoped objects (e.g., the code for [creating content items](/api/content-management/content-items#create-content-items)).
## Manage workspaces
You can manage workspaces in the **Workspaces** application.
### Create workspaces
To create a workspace:
  1. Open the **Workspaces** application.
  2. Select **New workspace**.  
[![Create a workspace](/docsassets/documentation/workspaces/workspaces_create_workspace.png)](/docsassets/documentation/workspaces/workspaces_create_workspace.png)
  3. Enter a **Workspace name** which identifies the workspace in the Admin UI.
  4. (Optional) **Identifiers** – specify the code name if you wish to use a code name different than the pre-filled value.
  5. **Save** the new workspace.


The workspace is now created. To allow users to work with workspaces, [grant permissions to roles](/documentation/developers-and-admins/configuration/users/role-management#assign-permissions-to-roles) for applications scoped under workspaces. These permissions can be set for each workspace and application separately.
To populate the workspace with content, [create new content items](/documentation/business-users/content-hub/content-items#create-content-items) or [move existing content items](/documentation/business-users/content-hub/content-items#move-content-items) from other workspaces.
### Delete workspaces
Deleting a workspace breaks any API calls that rely on its existence. Before proceeding, make sure your project’s developers no longer use the selected workspace as a parameter of the API calls in their code.
The last workspace in the system, or a workspace containing any content (e.g., content items or smart folders), cannot be deleted.
Keep in mind that if you [delete](/documentation/business-users/content-hub/content-items#delete-content-items) content items from a workspace that is later deleted, these content items cannot be [restored](/documentation/business-users/recycle_bin) from the recycle bin.
To delete a workspace:
  1. Open the **Workspaces** application.
  2. Locate an empty workspace you want to delete.
  3. **Delete** (  
[![Delete a workspace](/docsassets/documentation/workspaces/workspaces_delete_workspace.png)](/docsassets/documentation/workspaces/workspaces_delete_workspace.png)


The workspace is now permanently deleted.
![]()
[]()[]()
