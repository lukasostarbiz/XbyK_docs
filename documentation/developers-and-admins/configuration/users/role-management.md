# Role management
  * [ Copy page link ](documentation/developers-and-admins/configuration/users/role-management#) | [Get HelpService ID](documentation/developers-and-admins/configuration/users/role-management#)
Core MVC 5


[✖](documentation/developers-and-admins/configuration/users/role-management# "Close page link panel") [Copy to clipboard](documentation/developers-and-admins/configuration/users/role-management#)
Xperience uses a role-based system to manage the permissions of [user accounts](documentation/developers-and-admins/configuration/users/user-management). Roles define permissions that determine what users can and cannot do within individual administration UI applications. The number of roles assignable to each user account is not limited; permissions of a given account are determined by the union of permissions granted by individual roles. 
The system by default provides the **Administrator** role that grants full permissions to all features and applications in the product. Initially, the administrator account created during the installation process is the only user with this role. As one of the requirements when inviting new users to the system is assigning a role to their account, we strongly recommend setting up additional roles with restricted access (such as _Editor_ or _Contributor_) to prevent any misuse of granted privilege.
You can grant four main types of permissions for most applications in the system:
Permission |  Description  
---|---  
View |  The most basic permission required to interact with an application in Xperience. Without the **View** permission, users cannot see the corresponding application in the administration, access it via direct URLs, or use it in any other way.  
Create |  Grants permission to create objects in the corresponding application.   
Update |  Grants permission to modify existing objects in the corresponding application.  
Delete |  Grants permission to delete existing objects in the corresponding application.  
Not every application needs all permissions. For example, _Create_ , _Update_ , and _Delete_ are not relevant for the **Event Log** application, which only displays events that occurred in the system.
In addition to the permissions summarized in the table above, applications may also have permissions related to their specific functionality.
Application |  Permission |  Description  
---|---|---  
Website channel applications |  Access channel |  Allows users to see and access the website channel application in the administration.  
Manage permissions |  Allows users to grant [page permissions](documentation/developers-and-admins/configuration/users/role-management/page-permission-management) to other roles. Roles with this permission always have full privileges for all pages in the website channel.  
Content hub |  Synchronize |  Allows users to [synchronize](documentation/business-users/content-sync) content items from one instance of Xperience to another.  
Email channel applications |  Send email |  Allows users to send or schedule [emails](documentation/business-users/digital-marketing/emails) of the _Regular_ type.  
Forms |  Synchronize |  Allows users to [synchronize](documentation/business-users/content-sync) forms from one instance of Xperience to another.  
Media libraries |  Manage media library |  Allows users to perform file operations within media libraries, e.g., upload and delete media files.  
Migrate media library |  Allows users to [migrate media libraries](guides/architecture/media-libraries-migration-guidance) to Content hub.  
Channel management |  Manage headless channel API keys |  Allows users to create, enable or disable API keys for [headless channels](documentation/developers-and-admins/configuration/headless-channel-management).  
Scheduled tasks |  Run tasks |  Allows users to run non-system [scheduled tasks](documentation/developers-and-admins/customization/scheduled-tasks).  
Developers can also define suitable feature-specific permissions when creating custom applications and UI pages for the [administration](documentation/developers-and-admins/customization/extend-the-administration-interface).
## Manage roles
You manage roles in the **Role management** application.
### Create roles
  1. In the **Role management** application, select **New role**.
  2. Enter the role: 
    1. **Role name** – used when working with the role in the administration interface.
    2. _(Optional)_ **Identifiers** – specify the code name if you wish to use a code name different than the pre-filled value.
    3. _(Optional)_ **Description** – information about the role.
  3. **Save** the changes.


The role is created in the system. You can now:
  * [Assign permissions to roles](documentation/developers-and-admins/configuration/users/role-management#assign-permissions-to-roles)
  * [Assign users to roles](documentation/developers-and-admins/configuration/users/role-management#assign-users-to-roles)


### Assign permissions to roles
You assign permissions via a role’s **Permissions** tab. Open the **Role management** application and select a role. Then, switch to the role’s **Permissions** tab.
Each application groups all permissions that can be granted under a **Permission set**. Use the assignment interface to define permissions for the selected role.
[![Permissions assignment overview](docsassets/documentation/role-management/PermissionsTab.png)](https://docs.kentico.com/docsassets/documentation/role-management/PermissionsTab.png)
Selecting **Add permission set** creates a new item for the role. Use the application selector to choose the application for which to define permissions. For workspace-scoped applications, you define the permission set for the application in a specific workspace. For example, you can grant all permissions for Content hub in workspace A and only the _View_ permission for Content hub in workspace B.
The selector on the right lists the permissions available for the application. **View** is always preselected and non-removable – other permissions are redundant if the role cannot access the application in the first place. Applications without explicitly defined permission sets are inaccessible and hidden from the administration interface for all users in the given role.
### Assign users to roles
Users are assigned to roles on the **Assigned users** tab.
  1. Open the **Role management** application and select a role.
  2. Switch to the **Assigned users** tab.
  3. Add users to the role via **Add user**.


The users are now assigned to the role.
### Assign roles to users
You can also assign roles to individual users via the **Users** application.
  1. Open the **Users** application.
  2. Select a user.
  3. Assign roles using the **Role** dropdown on the **General** tab.


### Assign roles when inviting new users
Assigning a role is also necessary when inviting new users to the system via **New user** in the **Users** application.
[![Assigning roles when inviting new users](docsassets/documentation/role-management/NewUserRoles.png)](https://docs.kentico.com/docsassets/documentation/role-management/NewUserRoles.png)