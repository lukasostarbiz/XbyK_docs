# Page permissions
  * [ Copy page link ](api/content-management/page-permissions#) | [Get HelpService ID](api/content-management/page-permissions#)
Core MVC 5


[✖](api/content-management/page-permissions# "Close page link panel") [Copy to clipboard](api/content-management/page-permissions#)
  
List of examples:
  * [Dependency injection](api/content-management/page-permissions#dependency-injection)
    * [Initialize required services](api/content-management/page-permissions#initialize-required-services)
  * [Page permissions management](api/content-management/page-permissions#page-permissions-management)
    * [Set page permissions](api/content-management/page-permissions#set-page-permissions)
    * [Retrieve page permissions](api/content-management/page-permissions#retrieve-page-permissions)
    * [Break page permission inheritance](api/content-management/page-permissions#break-page-permission-inheritance)
    * [Restore page permission inheritance](api/content-management/page-permissions#restore-page-permission-inheritance)


## Dependency injection
### Initialize required services
C#
Copy
```
// Initializes all services and provider classes used within
// the API examples on this page using dependency injection
private readonly IWebPageAclManager webPageAclManager;
private readonly IContentQueryExecutor contentQueryExecutor;
private readonly IRoleInfoProvider roleInfoProvider;


public PagePermissionsServices(IContentQueryExecutor contentQueryExecutor,
                               IWebPageAclManagerFactory webPageAclManagerFactory,
                               IWebsiteChannelContext websiteChannelContext,
                               IRoleInfoProvider roleInfoProvider)
{

    // Creates an instance of the manager class facilitating page permission operations
    this.webPageAclManager = webPageAclManagerFactory.Create(websiteChannelContext.WebsiteChannelID);
    this.contentQueryExecutor = contentQueryExecutor;
    this.roleInfoProvider = roleInfoProvider;
}
```

[> Back to list of examples](api/content-management/page-permissions#toc)
## Page permissions management
### Set page permissions
C#
Copy
```
/* Page permissions are always set for specific roles for a particular page.
   When setting page permissions, you need to provide an instance
   of 'WebPageAclConfiguration' (metadata) that contains instances
   of 'WebPageAclRole', representing the assignment of page permissions
   to a particular role.
*/

// Gets the id of the page for which to set page permissions
string REVIEW_CONTENT_TYPE = "Wonka.ChocolateReview";
var builder = new ContentItemQueryBuilder()
                   .ForContentType(REVIEW_CONTENT_TYPE, q =>
                   {
                       q.ForWebsite("WonkaPages")
                        .TopN(1)
                        .Where(w => w.WhereEquals("ReviewTitle", "Wonka Chocolate Excellence"));
                   });

var pageId = await contentQueryExecutor
                    .GetWebPageResult<int>(builder: builder, resultSelector: rowData =>
                    {
                        var pageRowId = rowData.WebPageItemID;
                        return pageRowId;
                    },
                    // Retrieves the latest version of the page
                    new ContentQueryExecutionOptions() { ForPreview = true });


// Retrieves a `RoleInfo` object for the "DigitalChannelManager" role. Use the role's code name
RoleInfo selectedRole = roleInfoProvider.Get("DancingGoat.DigitalChannelManager");

// Specifies the list of page permissions to be set for the selected role
var permissionsToSet = new List<string> { WebPageAclPermissions.READ, WebPageAclPermissions.DISPLAY };

// Prepares the metadata needed for the permission assignment
var webPageAclRole = new WebPageAclRole();
webPageAclRole.RoleID = selectedRole.RoleID;
webPageAclRole.Permissions = permissionsToSet;
var webPageAclConfiguration = new WebPageAclConfiguration();
webPageAclConfiguration.Roles.Add(webPageAclRole);

// Sets the specified page permissions for the given role on the particular page
await webPageAclManager.SetPermissions(pageId.First(), webPageAclConfiguration);
```

[> Back to list of examples](api/content-management/page-permissions#toc)
### Retrieve page permissions
C#
Copy
```
// Demonstrates how to retrieve the roles and their assigned page permissions for a specific page

// Gets the id of the page for which to retrieve page permissions
string REVIEW_CONTENT_TYPE = "Wonka.ChocolateReview";
var builder = new ContentItemQueryBuilder()
                   .ForContentType(REVIEW_CONTENT_TYPE, q =>
                   {
                       q.ForWebsite("WonkaPages")
                        .TopN(1)
                        .Where(w => w.WhereEquals("ReviewTitle", "Wonka Chocolate Excellence"));
                   });

var pageId = await contentQueryExecutor
                    .GetWebPageResult<int>(builder: builder, resultSelector: rowData =>
                    {
                        var pageRowId = rowData.WebPageItemID;
                        return pageRowId;
                    },
                    // Retrieves the latest version of the page
                    new ContentQueryExecutionOptions() { ForPreview = true });

// Retrieves a 'WebPageAclConfigurationDescriptor' instance containing:
//   - The roles and their assigned permissions for the page
//   - The id of the page that defines the retrieved page permissions
await webPageAclManager.GetPermissions(pageId.First());
```

[> Back to list of examples](api/content-management/page-permissions#toc)
### Break page permission inheritance
C#
Copy
```
// Demonstrates how to break inheritance page permissions

// Gets the id of the page for which to break page permission inheritance
string REVIEW_CONTENT_TYPE = "Wonka.ChocolateReview";
var builder = new ContentItemQueryBuilder()
                   .ForContentType(REVIEW_CONTENT_TYPE, q =>
                   {
                       q.ForWebsite("WonkaPages")
                        .TopN(1)
                        .Where(w => w.WhereEquals("ReviewTitle", "Wonka Chocolate Excellence"));
                   });

var pageId = await contentQueryExecutor
                    .GetWebPageResult<int>(builder: builder, resultSelector: rowData =>
                    {
                        var pageRowId = rowData.WebPageItemID;
                        return pageRowId;
                    },
                    // Retrieves the latest version of the page
                    new ContentQueryExecutionOptions() { ForPreview = true });

// Breaks the inheritance of page permissions for the specified page
await webPageAclManager.BreakInheritance(pageId.First());
```

[> Back to list of examples](api/content-management/page-permissions#toc)
### Restore page permission inheritance
C#
Copy
```
// Demonstrates how to restore the inheritance of page permissions

// Gets the id of the page for which to break page permission inheritance
string REVIEW_CONTENT_TYPE = "Wonka.ChocolateReview";
var builder = new ContentItemQueryBuilder()
                   .ForContentType(REVIEW_CONTENT_TYPE, q =>
                   {
                       q.ForWebsite("WonkaPages")
                        .TopN(1)
                        .Where(w => w.WhereEquals("ReviewTitle", "Wonka Chocolate Excellence"));
                   });

var pageId = await contentQueryExecutor
                    .GetWebPageResult<int>(builder: builder, resultSelector: rowData =>
                    {
                        var pageRowId = rowData.WebPageItemID;
                        return pageRowId;
                    },
                    // Retrieves the latest version of the page
                    new ContentQueryExecutionOptions() { ForPreview = true });

// Restores the inheritance of page permissions for the specified page
await webPageAclManager.RestoreInheritance(pageId.First());
```

[> Back to list of examples](api/content-management/page-permissions#toc)