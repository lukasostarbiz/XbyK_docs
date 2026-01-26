---
source: https://docs.kentico.com/documentation/developers-and-admins/api/objectquery-api
scrape_date: 2026-01-26
---

  * [Home](/documentation)
  * [Developers and admins](/documentation/developers-and-admins)
  * [API](/documentation/developers-and-admins/api)
  * ObjectQuery API 


# ObjectQuery API
The Xperience ObjectQuery API provides an abstraction layer over the SQL database. Developers use ObjectQuery to retrieve data from the Xperience database. The main advantages of using ObjectQuery are:
  * strongly typed and enumerable results
  * independence on specific versions of SQL syntax
  * security


ObjectQuery call example:
C#
Copy
```
var query = UserInfo.Provider.Get();
```

The example query retrieves all users stored within Xperience.
The resulting collection contains **Info** objects. One instance of an _Info_ object represents one row of data from the database. In this case, the result is a set of `UserInfo` objects holding the data of individual users. The result allows you to iterate through the records using a foreach statement.
Similarly to users, you can use ObjectQuery to retrieve any other type of object. The Xperience API offers `IInfoProvider` interfaces (services) for individual object types, with a `Get` method that performs an appropriate ObjectQuery call. The `IInfoProvider` services can be added to your code [using dependency injection](/documentation/developers-and-admins/development/website-development-basics/dependency-injection) or accessed through the `Provider` property of the corresponding `Info` class.
The following table provides an example of the naming conventions.
Object |  Info class name |  IInfoProvider service  
---|---|---  
User |  UserInfo |  IUserInfoProvider  
## Select specific columns
ObjectQuery allows you to select only those columns that you need to work with. Selecting only the required columns is a good practice that prevents issues with performance of your code by transferring only the necessary data from the database server to the application server.
C#
Copy
```
var columnsQuery = UserInfo.Provider.Get().Columns("FirstName", "LastName", "UserName");
```

This example query retrieves all users but limits the data to the users’ first names, last names, and usernames.
## Limit retrieved results (SQL WHERE)
ObjectQuery provides a way to narrow down the resulting data using SQL syntax independent where conditions.
Use predefined where condition methods that provide optimized performance and make your code easily readable. The predefined where condition methods include, but are not limited to:
  * `WhereEquals("ColumnName", value)` – checks the equality of the value in the specified column with the value specified in the second parameter.
  * `WhereGreaterThan("ColumnName", value)` – compares the value in the specified column with the second parameter.
  * `WhereNull("ColumnName")`– select only rows where the specified column has a NULL value.
  * `WhereNot(whereCondition)` – negates the specified where condition.
  * `WhereStartsWith("ColumnName", "Value")` – only works for text columns. Selects only rows whose value in the specified column starts with the given value.


C#
Copy
```
// Retrieves all users whose first name is "Joe"
var whereQuery = UserInfo.Provider.Get().WhereEquals("FirstName", "Joe");
```

**Tip** : Use Visual Studio’s IntelliSense to help you pick the right predefined where condition.
### Logical operators
ObjectQuery allows you to join together multiple where conditions.
  * By default, the conditions are combined using an `AND` parameter.
  * To place an `OR` operator between conditions, call the `Or()` method between the given `Where` methods.
  * You can also explicitly call the `And()` method if you wish to make the code of your conditions clearer and easier to read.


**Example 1** :
C#
Copy
```
// Retrieves users whose first name is "Joe" or last name is "Smith"
var whereQuery = UserInfo.Provider.Get()
    .Where("FirstName", QueryOperator.Equals, "Joe")
    .Or()
    .Where("LastName", QueryOperator.Equals, "Smith");
```

SQL
**Generated condition**
Copy
```
DECLARE @FirstName nvarchar(max) = N'Joe';
DECLARE @LastName nvarchar(max) = N'Smith';

...

WHERE [FirstName] = @FirstName OR [LastName] = @LastName
```

**Example 2** :
C#
Copy
```
// Retrieves all enabled users whose first name is "Joe" and email address ends with "localhost.local"
var whereQuery = UserInfo.Provider.Get()
    .WhereEquals("FirstName", "Joe")
    .WhereEquals("UserEnabled", 1)
    .WhereEndsWith("Email", "localhost.local");
```

SQL
**Generated condition**
Copy
```
DECLARE @FirstName nvarchar(max) = N'John';
DECLARE @UserEnabled int = 1;
DECLARE @Email nvarchar(max) = N'%localhost.local';

...

WHERE [FirstName] = @FirstName AND [UserEnabled] = @UserEnabled AND [Email] LIKE @Email
```

### Nested where conditions
If you need to construct complex where conditions with multiple `AND`/`OR` operators, use nested where conditions:
**Note:** You must use the `new`keyword to construct nested where conditions.
C#
Copy
```
// Retrieves users named "Smith", whose first name is "Joe" or "John"
var nestedWhereQuery = UserInfo.Provider.Get()
    .WhereEquals("LastName", "Smith")
    .Where(new WhereCondition()
        .WhereEquals("FirstName", "Joe")
        .Or()
        .WhereEquals("FirstName", "John")
    );
```

SQL
**Generated condition**
Copy
```
DECLARE @LastName nvarchar(max) = N'Smith';
DECLARE @FirstName nvarchar(max) = N'Joe';
DECLARE @FirstName1 nvarchar(max) = N'John';

...

WHERE [LastName] = @LastName AND ([FirstName] = @FirstName OR [FirstName] = @FirstName1)
```

## Order query results (SQL ORDER BY)
ObjectQuery can also sort data. Use the `OrderBy`, `OrderByAscending`, or `OrderByDescending` methods.
C#
Copy
```
var orderedQuery = UserInfo.Provider.Get()
    .WhereGreaterThan("UserCreated", DateTime.Now.AddDays(-7))
    .OrderByDescending("UserCreated");
```

This example retrieves users who registered or were created in the system during the past week. The results are in descending order.
## Limit the number of query results (SQL TOP N)
ObjectQuery can also retrieve only a specified number of results.
C#
Copy
```
var topNQuery = EventLogInfo.Provider.Get()
    .TopN(5)
    .OrderByDescending("EventTime");
```

This example retrieves the latest 5 event log records with the newest ordered first.
## Join multiple tables (SQL JOIN)
With ObjectQuery, you can combine data from two or more tables based on a common field.
C#
Copy
```
var joinQuery = UserInfo.Provider.Get()
    .Source(sourceItem => sourceItem.Join<MediaFileInfo>("UserID", "FileModifiedByUserID"))
    .WhereEmpty("Email");
```

This example retrieves users who have an empty email value, together with data about media files modified by the given users.
The code on the second line appends _Media_File_ table rows to the appropriate user records based on their ID. The code on the third line then selects only rows where the _Email_ column is empty.
By default, the `Join<TInfo>` method performs an Inner join. For other types of joins, you can call the `LeftJoin<TInfo>` or `RightJoin<TInfo>` methods. See [SQL Joins](http://www.w3schools.com/sql/sql_join.asp) for more information.
For advanced scenarios, you can use the non-generic version of the `Join` method. In this case, you need to identify the target table via a `QuerySourceTable` object (requires the exact database table name, optionally allows you to specify an alias and [SQL Hints](https://docs.microsoft.com/en-us/sql/t-sql/queries/hints-transact-sql)).
**Working with joined data**
If you need to work with data containing columns from multiple tables, convert the result of the query to a `DataSet`. The default ObjectQuery result is strongly typed, so only allows you to access the data of a single Xperience `Info` class. Use the `Result` property of ObjectQuery to get the results as a `DataSet`.
**Limitation** : ObjectQuery joins cannot be used for tables located in different databases. In these cases, use separate ObjectQuery calls and then combine the resulting data manually.
### Joining 3 or more tables
You can chain together multiple `Join` methods to combine rows from 3 or more database tables.
**Note** : When you call the `Join` method for a query source, the _left column_ parameter works with columns within the given query source table by default. When using multiple joins, we recommend that you explicitly specify both the table and column by adding a table name prefix to the left column name, for example: _“CMS_User.UserID”_
The following example joins together rows from 3 tables based on shared user IDs and returns the result as a DataSet.
C#
Copy
```
using System.Data;

using CMS.ContactManagement;
using CMS.MediaLibrary;
using CMS.Membership;

...

DataSet data = UserInfo.Provider.Get()
    .Source(sourceItem => sourceItem.Join<MediaFileInfo>("CMS_User.UserID", "FileModifiedByUserID")
                                    .Join<ContactInfo>("CMS_User.UserID", "ContactOwnerUserID")
            )
    .Columns("UserID, UserName, ContactEmail, FileTitle")
    .Result;
```

## Work with ObjectQuery results
ObjectQuery results are [lazy-loaded](http://en.wikipedia.org/wiki/Lazy_loading). Lazy loading increases performance by executing queries only when you need to work with their results. Most of the examples on this page only construct the proper queries. The SQL queries are executed when you either enumerate the results (using the foreach statement or by calling `GetEnumerableTypedResult`), or cast the query as a different type. When you work with the results after the query executed, the system does not perform any additional requests to the database.
The most common example of working with query results is iterating through them. Use a standard foreach loop.
C#
Copy
```
var userQuery = UserInfo.Provider.Get();

foreach (UserInfo user in userQuery)
{
    // Do something with the UserInfo object
}
```

**Tip** : You can access the results of ObjectQuery calls as a `DataSet` through the `Result` property. This is only recommended for data containing columns from multiple different object types or tables (joins and unions).
**LINQ support**
ObjectQuery provides limited support for [LINQ](https://docs.microsoft.com/en-us/dotnet/csharp/programming-guide/concepts/linq/).
Currently, we only recommend using LINQ for simple expressions, such as comparisons with constants. Any other expressions can lead to sub-optimal query execution. Use the ObjectQuery API directly to get optimal performance.
### Asynchronous execution of queries
The ObjectQuery API supports asynchronous loading of data from Xperience.
After you prepare a query with any required parameters, you can execute it asynchronously by enumerating the results via the `GetEnumerableTypedResultAsync()` method.
C#
Copy
```
// Prepares a query that loads all users
var userQuery = UserInfo.Provider.Get();

// Asynchronously executes the query and loads the results into an enumerable collection
IEnumerable<UserInfo> users = await userQuery.GetEnumerableTypedResultAsync();
```

To asynchronously load a single Xperience object, call the `GetAsync` method of the corresponding `IInfoProvider` service.
C#
Copy
```
// Asynchronously loads a UserInfo object representing a specified user (by ID or GUID)
Task<UserInfo> user = UserInfo.Provider.GetAsync(userId);
```

### Reuse queries (mutability)
ObjectQuery instances are mutable by default. This means that any changes (method calls) made to existing instances also modify the original query.
If you wish to reuse an ObjectQuery instance without modifying the original, use one of the following approaches:
  * Call the `Immutable()` method for the original query. This ensures automatic cloning of the ObjectQuery instance when it is modified.
  * If you want to leave a query mutable, you can call the `Clone()` method to create a new instance which can then be modified without affecting the original.


C#
**Example**
Copy
```
using System.Linq;
using CMS.Membership;

...

// Creates an immutable query for loading users ordered by their full name
var userQuery = UserInfo.Provider.Get()
    .OrderBy("FullName")
    .Immutable();

// Gets a subset of users based on the original query
var enabledUserList = userQuery
    .WhereEquals("UserEnabled", 1)
    .ToList();

// Gets a list of users using the original unmodified query
var userList = userQuery.ToList();
```

![]()
[]()[]()
