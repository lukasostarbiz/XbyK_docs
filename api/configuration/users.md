# Users
  * [ Copy page link ](api/configuration/users#) | [Get HelpService ID](api/configuration/users#)
Core MVC 5


[✖](api/configuration/users# "Close page link panel") [Copy to clipboard](api/configuration/users#)
  
List of examples:
  * [Dependency injection](api/configuration/users#dependency-injection)
    * [Initialize required services](api/configuration/users#initialize-required-services)
  * [Users](api/configuration/users#users)
    * [Create new users with administration access](api/configuration/users#create-new-users-with-administration-access)
    * [Update existing users](api/configuration/users#update-existing-users)
    * [Update multiple users](api/configuration/users#update-multiple-users)
    * [Work with custom user fields](api/configuration/users#work-with-custom-user-fields)
    * [Delete users](api/configuration/users#delete-users)
  * [User authentication and authorization](api/configuration/users#user-authentication-and-authorization)
    * [Check user administration access](api/configuration/users#check-user-administration-access)


## Dependency injection
### Initialize required services
C#
Copy
```
// Initializes all services and provider classes used within
// the API examples on this page using dependency injection.
private readonly IUserInfoProvider userInfoProvider;

public UsersServices(IUserInfoProvider userInfoProvider)
{
    this.userInfoProvider = userInfoProvider;
}
```

[> Back to list of examples](api/configuration/users#toc)
## Users
### Create new users with administration access
C#
Copy
```
// Creates a new user object
UserInfo newUser = new UserInfo();

// Sets the user properties
newUser.UserName = "NewUser";
newUser.Email = "new.user@domain.com";

// Enables the user
newUser.UserEnabled = true;

// Allows the user to access the administration interface
// Also controls whether the user is displayed in the Users application in the administration
newUser.UserAdministrationAccess = true;

// Saves the user to the database
userInfoProvider.Set(newUser);
```

[> Back to list of examples](api/configuration/users#toc)
### Update existing users
C#
Copy
```
// Gets the user
UserInfo updateUser = userInfoProvider.Get("NewUser");
if (updateUser != null)
{
    // Updates the user's properties
    updateUser.FirstName = updateUser.FirstName.ToLowerInvariant();
    updateUser.LastName = updateUser.LastName.ToLowerInvariant();

    // Saves the changes to the database
    userInfoProvider.Set(updateUser);
}
```

[> Back to list of examples](api/configuration/users#toc)
### Update multiple users
C#
Copy
```
// Gets all users whose username starts with 'NewUser'
var users = userInfoProvider.Get().WhereStartsWith("UserName", "NewUser");

// Loops through individual users
foreach (UserInfo modifyUser in users)
{
    // Updates the user properties
    modifyUser.FirstName = modifyUser.FirstName.ToUpper();
    modifyUser.LastName = modifyUser.LastName.ToUpper();

    // Saves the changes to the database
    userInfoProvider.Set(modifyUser);
}
```

[> Back to list of examples](api/configuration/users#toc)
### Work with custom user fields
C#
Copy
```
// Gets the user
UserInfo user = userInfoProvider.Get("NewUser");

if (user != null)
{
    // Attempts to retrieve a value from a custom text field named 'CustomField'
    string value = user.GetValue("CustomField", "");

    // Sets a modified value for the custom field
    user.SetValue("CustomField", value + "_customSuffix");

    // Saves the changes to the database
    userInfoProvider.Set(user);
}
```

[> Back to list of examples](api/configuration/users#toc)
### Delete users
C#
Copy
```
// Gets the user
UserInfo deleteUser = userInfoProvider.Get("NewUser");

if (deleteUser != null)
{
    // Deletes the user
    userInfoProvider.Delete(deleteUser);
}
```

[> Back to list of examples](api/configuration/users#toc)
## User authentication and authorization
### Check user administration access
C#
Copy
```
// Gets the user
UserInfo user = userInfoProvider.Get("NewUser");

if (user != null)
{
    // Checks if the user has access to the admin UI
    if (user.HasAdministrationAccess())
    {
        // Perform an action
    }
}
```

[> Back to list of examples](api/configuration/users#toc)