---
source: https://docs.kentico.com/documentation/developers-and-admins/api/database-table-api
scrape_date: 2026-01-26
---

  * [Home](/documentation)
  * [Developers and admins](/documentation/developers-and-admins)
  * [API](/documentation/developers-and-admins/api)
  * Database table API 


# Database table API
Xperience stores most data in database tables. The API provides classes for managing the data of each table – an `Info` class, `IInfoProvider` interface (service), and an `InfoProvider` class.
[![Info object database table structure in Xperience](/docsassets/documentation/database-table-api/DB_InfoProvider_API.png)](/docsassets/documentation/database-table-api/DB_InfoProvider_API.png)
## Info classes
Every `Info` class is related to a specific database table. Instances of Info classes represent entries (rows) in the given table – the class serves as a container for the data of the entry. The properties of an info class correspond to the columns of the related table.
### Info class validation
When saving info objects to the database, the system validates object code name format and uniqueness. You can extend this validation by implementing info-specific rules via `IInfoValidator<TInfo>`. See [Customize system providers](/documentation/developers-and-admins/customization/customize-system-providers#info-object-validation).
## Info class managers
The system offers the following options for CRUD operations over managed database objects.
### Generic provider class – IInfoProvider<TInfo>
The `IInfoProvider<TInfo>` and its associated extension methods provide conventional CRUD API in the form of `Get`, `Set`, and `Delete` methods and their asynchronous counterparts. The `Get` and `GetAsync` methods expose multiple overloads for retrieval via ID, GUID, or code name depending on the configuration of the corresponding object type.
You can manage objects via `IInfoProvider<TInfo>` instances. Substitute `TInfo` for the `*Info` class you want to work with. We recommend this approach for all custom objects added to the system. For example, to obtain an instance of the content language manager, use:
C#
**Initialize a generic info object provider via dependency injection**
Copy
```
using CMS.DataEngine;

private readonly IInfoProvider<ContentLanguageInfo> contentLanguageProvider;

public MyClass(IInfoProvider<ContentLanguageInfo> contentLanguageProvider)
{
    this.contentLanguageProvider = contentLanguageProvider;
}
```

Note that not all system classes currently fully support this approach. 
For bulk operations, use the `BulkInsert`, `BulkUpdate`, and `BulkDelete` methods.
If you cannot use dependency injection in your codebase, you can instantiate the generic provider via the static `CMS.DataEngine.Provider<TInfo>.Instance`.
### Dedicated provider classes
Each managed database object can have a dedicated provider class that ensures CRUD operations.
#### IInfoProvider interfaces
Most `Info` classes have a matching `IInfoProvider` interface (service), which allows basic management of the given objects – getting, creating, updating, and deleting – using the system’s [ObjectQuery](/documentation/developers-and-admins/api/objectquery-api) query syntax.
You can resolve instances of `IInfoProvider` services using [dependency injection](/documentation/developers-and-admins/development/website-development-basics/dependency-injection) or via the `Provider` property of the corresponding `Info` class.
C#
**Code example**
Copy
```
using CMS.Membership;

// ...

// Gets a UserInfo object representing the user with the "Andy" username
UserInfo user = UserInfo.Provider.Get("Andy");

// Saves the user's email address to a local variable
string userEmail = user.Email;
```

#### InfoProvider classes
`InfoProvider` classes are the actual implementations of the `IInfoProvider` interfaces. Every provider manages the data of a specific table using the related `Info` objects.
We do not recommend using `InfoProvider` classes directly for basic operations, such as getting, creating, or deleting objects. Use the corresponding `IInfoProvider` service instead.
![]()
[]()[]()
