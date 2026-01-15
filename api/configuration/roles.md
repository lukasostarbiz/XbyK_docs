# Roles
  * [ Copy page link ](api/configuration/roles#) | [Get HelpService ID](api/configuration/roles#)
Core MVC 5


[✖](api/configuration/roles# "Close page link panel") [Copy to clipboard](api/configuration/roles#)
  
List of examples:
  * [Dependency injection](api/configuration/roles#dependency-injection)
    * [Initialize required services](api/configuration/roles#initialize-required-services)
  * [Roles](api/configuration/roles#roles)
    * [Create a role](api/configuration/roles#create-a-role)
    * [Assign a user to a role](api/configuration/roles#assign-a-user-to-a-role)
    * [Define application permissions for a specific role](api/configuration/roles#define-application-permissions-for-a-specific-role)


## Dependency injection
### Initialize required services
C#
Copy
```
// Initializes all services and provider classes used within
// the API examples on this page using dependency injection.
private readonly IUserInfoProvider userInfoProvider;
private readonly IRoleInfoProvider roleInfoProvider;
private readonly IUserRoleInfoProvider userRoleProvider;
private readonly IInfoProvider<ApplicationPermissionInfo> applicationPermissionInfoProvider;

public RolesServices(
    IUserInfoProvider userInfoProvider,
    IRoleInfoProvider roleInfoProvider,
    IUserRoleInfoProvider userRoleProvider,
    IInfoProvider<ApplicationPermissionInfo> applicationPermissionInfoProvider)
{
    this.userInfoProvider = userInfoProvider;
    this.roleInfoProvider = roleInfoProvider;
    this.userRoleProvider = userRoleProvider;
    this.applicationPermissionInfoProvider = applicationPermissionInfoProvider;
}
```

[> Back to list of examples](api/configuration/roles#toc)
## Roles
### Create a role
C#
Copy
```
// Creates a new `RoleInfo` object
RoleInfo newRole = new()
{
    RoleDisplayName = "New role's display name",

    //  Role's code name. Can contain only alphanumeric characters,
    //  some special characters (_, -, .) and cannot start or end with '.'.
    //  Cannot be "Administrator".
    RoleName = "New-role-code-name",

    // Role's optional description
    RoleDescription = "New role's description."
};

// Writes a role in the database
roleInfoProvider.Set(newRole);
```

[> Back to list of examples](api/configuration/roles#toc)
### Assign a user to a role
C#
Copy
```
// Example: Assign the "NewUser" user to a "DigitalChannelManager" role

// Retrieves a `RoleInfo` object for the "DigitalChannelManager" role. Use the role's code name.
RoleInfo selectedRoleInfo = roleInfoProvider.Get("DancingGoat.DigitalChannelManager");

// Retrieves a `UserInfo` object for the "NewUser" user. Use the user's code name
UserInfo selectedUserInfo = userInfoProvider.Get("NewUser");

// Creates a `UserRoleInfo` object that represents the assignment
UserRoleInfo newRoleAssignment = new()
{
    RoleID = selectedRoleInfo.RoleID,
    UserID = selectedUserInfo.UserID
};

// Writes the user-role relationship to the database
userRoleInfoProvider.Set(newRoleAssignment);
```

[> Back to list of examples](api/configuration/roles#toc)
### Define application permissions for a specific role
C#
Copy
```
// Example: Add "Update" permission for the "Content hub"
//        application to users with the "DigitalChannelManager" role.

// Prerequisites:
//  1.  Add the following namespace: `using Kentico.Xperience.Admin.Base.UIPages;`.
//  2.  Check the `UI tree` under the `System` application in the Xperience administration
//    for the class that represents the application under "Back-end page definition".
//  3.  Check "Role management" application to see the available permissions for the given application.


// Gets a `RoleInfo` object representing the "DigitalChannelManager" role
RoleInfo selectedRoleInfo = roleInfoProvider.Get("DancingGoat.DigitalChannelManager");

// Gets the `IDENTIFIER` constant for the selected application
string applicationName = Kentico.Xperience.Admin.Base.UIPages.ContentHubApplication.IDENTIFIER;

// Creates an `ApplicationPermissionInfo` object that represents the assignment
ApplicationPermissionInfo newApplicationPermissionRoleAssignment = new()
{
    RoleID = selectedRoleInfo.RoleID,
    // Use `System.Permissions`.<permission> to set the PermissionName
    PermissionName = SystemPermissions.UPDATE,
    ApplicationName = applicationName
};

// Writes the application permissions for a specific role to the database
applicationPermissionInfoProvider.Set(newApplicationPermissionRoleAssignment);
```

[> Back to list of examples](api/configuration/roles#toc)