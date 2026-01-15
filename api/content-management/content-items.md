# Content items
  * [ Copy page link ](api/content-management/content-items#) | [Get HelpService ID](api/content-management/content-items#)
Core MVC 5


[✖](api/content-management/content-items# "Close page link panel") [Copy to clipboard](api/content-management/content-items#)
  
List of examples:
  * [Dependency injection](api/content-management/content-items#dependency-injection)
    * [Initialize required services](api/content-management/content-items#initialize-required-services)
  * [Content item retrieval](api/content-management/content-items#content-item-retrieval)
    * [Retrieve content items in web applications](api/content-management/content-items#retrieve-content-items-in-web-applications)
    * [Retrieve content items of a single content type](api/content-management/content-items#retrieve-content-items-of-a-single-content-type)
    * [Retrieve content items of multiple content types](api/content-management/content-items#retrieve-content-items-of-multiple-content-types)
  * [Content item management](api/content-management/content-items#content-item-management)
    * [Create content items](api/content-management/content-items#create-content-items)
    * [Clone content items](api/content-management/content-items#clone-content-items)
    * [Generate content item code names](api/content-management/content-items#generate-content-item-code-names)
    * [Create content item language variants](api/content-management/content-items#create-content-item-language-variants)
    * [Publish content items](api/content-management/content-items#publish-content-items)
    * [Schedule publishing for content items](api/content-management/content-items#schedule-publishing-for-content-items)
    * [Cancel scheduled publishing for content items](api/content-management/content-items#cancel-scheduled-publishing-for-content-items)
    * [Unpublish content items](api/content-management/content-items#unpublish-content-items)
    * [Schedule unpublishing for content items](api/content-management/content-items#schedule-unpublishing-for-content-items)
    * [Cancel scheduled unpublishing for content items](api/content-management/content-items#cancel-scheduled-unpublishing-for-content-items)
    * [Retrieve usages of a content item](api/content-management/content-items#retrieve-usages-of-a-content-item)
    * [Delete content items](api/content-management/content-items#delete-content-items)
    * [Create content item drafts](api/content-management/content-items#create-content-item-drafts)
    * [Update content item drafts](api/content-management/content-items#update-content-item-drafts)
  * [Content item assets](api/content-management/content-items#content-item-assets)
    * [Upload content item assets from files](api/content-management/content-items#upload-content-item-assets-from-files)
    * [Upload content item assets from data streams](api/content-management/content-items#upload-content-item-assets-from-data-streams)


## Dependency injection
### Initialize required services
C#
Copy
```
// Initializes all services and provider classes used within
// the API examples on this page using dependency injection.
private readonly IContentItemManager contentItemManager;
private readonly IContentQueryExecutor contentQueryExecutor;
private readonly IContentItemCodeNameProvider contentItemCodeNameProvider;
private readonly IContentQueryModelTypeMapper contentQueryModelTypeMapper;
private readonly IContentItemUsageRetriever contentItemUsageRetriever;

private readonly IContentRetriever contentRetriever;

public ContentItemsServices(IContentItemManagerFactory contentItemManagerFactory,
                            IContentQueryExecutor contentQueryExecutor,
                            IUserInfoProvider userInfoProvider,
                            IContentItemCodeNameProvider contentItemCodeNameProvider,
                            IContentQueryModelTypeMapper contentQueryModelTypeMapper,
                            IContentItemUsageRetriever contentItemUsageRetriever,
                            IContentRetriever contentRetriever)
{
    // Gets the user responsible for management operations.
    // The user is used only for auditing purposes,
    // e.g., to be shown as the creator in 'Created by' fields.
    // The user's permissions are not checked for any of the operations.
    UserInfo user = userInfoProvider.Get("JimmieWonka");

    // Creates an instance of the manager class facilitating content item operations
    this.contentItemManager = contentItemManagerFactory.Create(user.UserID);
    this.contentQueryExecutor = contentQueryExecutor;
    this.contentItemCodeNameProvider = contentItemCodeNameProvider;
    this.contentQueryModelTypeMapper = contentQueryModelTypeMapper;
    this.contentItemUsageRetriever = contentItemUsageRetriever;
    this.contentRetriever = contentRetriever;
}
```

[> Back to list of examples](api/content-management/content-items#toc)
## Content item retrieval
### Retrieve content items in web applications
C#
Copy
```
/* IContentRetriever is a service class that provides a high-level abstraction over the Content item query API for retrieving content.
 * The service is intended for content retrieval in the context of Xperience web applications.
 *
 * The service ensures the following default behavior for each query:
 *  -- Determines whether to retrieve the latest or published versions based on the current request context
 *  -- Retrieves content in the language matching the current request context
 *  -- Takes into account language fallback settings for content that does not exist in the specific language
 *  -- Provides built-in caching with automatic cache dependency collection
 *
 * You can further parameterize the queries using parameter objects and query configuration delegates.
 *
 * See the 'Reference - ContentRetriever API' documentation for a comprehensive overview of the API.
 * For a quick reference, see the following code examples:
 */

// Retrieves content items of a single content type
IEnumerable<CandyStore> candyStores = await contentRetriever.RetrieveContent<CandyStore>();

// Retrieves content items from a specified workspace in a specific language
IEnumerable<CandyStore> workspaceStores = await contentRetriever.RetrieveContent<CandyStore>(
    new RetrieveContentParameters
    {
        WorkspaceNames = new[] { "MainContent" },
        LanguageName = "en"
    });

// Retrieves content items of multiple content types using a shared interface
var contentTypes = new[] { CandyStore.CONTENT_TYPE_NAME, Author.CONTENT_TYPE_NAME };
IEnumerable<IContentItemFieldsSource> mixedContent = await contentRetriever.RetrieveContentOfContentTypes<IContentItemFieldsSource>(contentTypes);

// Retrieves specific content items by their GUIDs
var contentItemGuids = new[] { Guid.Parse("c6f453db-dd39-46ac-bdb1-78bdb5b20a9e") };
IEnumerable<CandyStore> specificContent = await contentRetriever.RetrieveContentByGuids<CandyStore>(contentItemGuids);

// Retrieves content items that implement specific reusable field schemas
var schemaNames = new[] { "ProductFields", "MetadataSchema" };
IEnumerable<IContentItemFieldsSource> contentWithSchemas = await contentRetriever.RetrieveContentOfReusableSchemas<IContentItemFieldsSource>(schemaNames);
```

[> Back to list of examples](api/content-management/content-items#toc)
### Retrieve content items of a single content type
C#
Copy
```
// This example demonstrates how to retrieve content items of a single content type
string STORE_CONTENT_TYPE = "Wonka.CandyStore";
string languageName = "en";
string workspaceName = "MyWorkspace";

/* Gets 5 content items of 'Wonka.CandyStore' content type from the 'MyWorkspace' workspace.
   The second parameter of the ForContentType method is an optional function to configure the given
   content type's query.
*/
ContentItemQueryBuilder builder =
        new ContentItemQueryBuilder()
                .ForContentType(STORE_CONTENT_TYPE, subqueryParameters =>
                {
                    subqueryParameters.TopN(5);
                    // Optional methods for global parametrization of their preceding queries
                }).InLanguage(languageName)
                  .InWorkspaces(workspaceName)
                  .Parameters(subqueryParameters =>
                   subqueryParameters.OrderBy(new OrderByColumn("StoreRating", OrderDirection.Descending)));

// Executes the query specified in 'builder' and binds it to the CandyStore class generated for the CandyStore content type
IEnumerable<CandyStore> candyStores =
        await contentQueryExecutor.GetMappedResult<CandyStore>(builder);
```

[> Back to list of examples](api/content-management/content-items#toc)
### Retrieve content items of multiple content types
C#
Copy
```
private async void RetrieveContentItemsOfContentTypes()
{
    // This example demonstrates how to retrieve content items of multiple content types
    string languageName = "en";
    int maxLevel = 1;
    string workspaceName = "MyWorkspace";

    /* Gets content items that use the 'ProductFields' reusable field schema.
       The items are retrieved from the 'MyWorkspace' workspace in the 'en' language.
       The first parameter of the ForContentTypes method is an optional function to configure the query.
    */
    ContentItemQueryBuilder builder =
            new ContentItemQueryBuilder()
                    .ForContentTypes(subqueryParameters =>
                    {
                        subqueryParameters.OfReusableSchema("ProductFields")
                        .WithContentTypeFields()
                        .WithLinkedItems(maxLevel);
                        // Optional methods for global parametrization of their preceding queries
                    }).InLanguage(languageName)
                      .InWorkspaces(workspaceName)
                      .Parameters(subqueryParameters =>
                       subqueryParameters.OrderBy(new OrderByColumn("ProductRating", OrderDirection.Descending)));

    // Executes the query specified in 'builder' and binds it using the logic in 'ModelBinder'.
    // The second argument of the 'GetResult' method is a delegate function used to specify the mapping behavior.
    IEnumerable<Dto> result =
            await contentQueryExecutor
                    .GetResult(builder, ModelBinder);

}


private Dto ModelBinder(IContentQueryDataContainer container)
{
    // Maps column data to corresponding properties of the custom model class based on matching names. For the example 'Dto' object,
    // only columns named 'Name', 'ProductRating' and 'ShortDescription' get mapped in addition to
    // 'SystemFields'. All other fields of content types are ignored.
    return contentQueryModelTypeMapper.Map<Dto>(container);
}

public class Dto
{
    // Maps Xperience-specific fields
    public ContentItemFields SystemFields { get; set; }
    // Maps the 'Name' column from the database
    public string Name { get; set; }
    // Maps the 'Content' column from the database
    public string ProductRating { get; set; }
    // Maps the 'ShortDescription' column from the database
    public string ShortDescription { get; set; }
}
```

[> Back to list of examples](api/content-management/content-items#toc)
## Content item management
### Create content items
C#
Copy
```
/* Content items consist of presentation data and metadata that drive system behavior.
    When creating new items, you must provide instances of 'CreateContentItemParameters' (metadata),
    and 'ContentItemData' (data).
    'ContentItemData' is a dictionary that stores item values in a <fieldName>:<fieldValue> format.
    The dictionary must contain records that correspond to the fields of the content item's content type as
    defined in the field editor.
*/

// The code name of the content type on which the item is based
string STORE_CONTENT_TYPE = "Wonka.CandyStore";

// The content item display name as shown in the admin UI
// Used to generate the item code name
string contentItemDisplayName = "Prague-Hradcany";

// The initial language in which the content item gets created.
// Must correspond to a language defined in the 'Languages' application.
string languageName = "en";

// The name of the workspace in which the content item gets created.
string workspaceName = "MyWorkspace";

// Creates a content item metadata object
CreateContentItemParameters createParams = new CreateContentItemParameters(
                                                                STORE_CONTENT_TYPE,
                                                                null,
                                                                contentItemDisplayName,
                                                                languageName,
                                                                workspaceName);

/* Populates content item data. This example assumes a content type with the following fields:
        -- Data type: Text; Code name: StoreName
        -- Data type: Long text; Code name: StoreDescription
        -- Data type: Content items; Code name: StoreReferences
    To find the underlying C# type of other data types, see 'Data type management' in the Kentico documentation.
*/
ContentItemData itemData = new ContentItemData(new Dictionary<string, object>{
    { "StoreName", "Prague-Hradcany" },
    { "StoreDescription", "The first Wonka candy shop in Czech Republic." },
    // A reference to an existing content item
    { "StoreReferences", new List<ContentItemReference>()
                { new ContentItemReference()
                        { Identifier = Guid.Parse("a82ad562-c411-47c9-8fee-ee55428a706f") } } }
});

/* Creates the content item in the database
   IContentItemManager always creates new content items with the 'All content items' location (not under a folder)
*/
await contentItemManager.Create(createParams, itemData);
```

[> Back to list of examples](api/content-management/content-items#toc)
### Clone content items
C#
Copy
```
/* Cloning a reusable content item creates a new content item with the same data.
   Files uploaded to the 'Content item asset' field are copied and a new independent
   instance of the file is created for the cloned item. The new item is created in the
   same workspace, at the root level of the folder tree. By default, all language
   variants are cloned. Set the 'cloneAllLanguageVariants' parameter to false to clone
   just the selected language variant.
*/

// Gets the ID of the item to clone
string STORE_CONTENT_TYPE = "Wonka.CandyStore";
ContentItemQueryBuilder builder =
        new ContentItemQueryBuilder()
                .ForContentType(STORE_CONTENT_TYPE, q =>
                {
                    q.TopN(1)
                     .Where(w => w.WhereEquals("StoreName", "Prague-Hradcany"));
                });

IEnumerable<int> itemIds = await contentQueryExecutor.GetResult<int>(builder, rowData =>
{
    var contentItemId = rowData.ContentItemID;
    return contentItemId;
},
        // Retrieves the latest version of the item
        new ContentQueryExecutionOptions() { ForPreview = true });

// Language variant to be cloned. This parameter has to be set to an existing language
// variant even when you want to clone all language variants of the item.
string languageName = "en";

// Display name of the new item shown in the admin UI
string displayName = "Berlin";

// Initializes parameters for cloning
var cloneParameters = new CloneContentItemParameters
(
    contentItemId: itemIds.FirstOrDefault(),
    languageName,
    displayName,
    // Set to false to clone just the selected language variant
    cloneAllLanguageVariants: true
);

// Creates the new cloned item in the database
// The new item is created in the same workspace as the original item,
// at the root level of the folder tree.
await contentItemManager.Clone(cloneParameters);
```

[> Back to list of examples](api/content-management/content-items#toc)
### Generate content item code names
C#
Copy
```
// The 'CreateContentItemParameters' constructor in the 'create a content item'
// example above uses API that automatically generates a unique code name for
// the content item being created.
// You can also transparently generate code names using 'IContentItemCodeNameProvider'.

// An example content item display name
string displayName = "Prague-Hradcany";

// 'IContentItemCodeNameProvider.Get' ensures a unique code name from the provided
// string by appending a random suffix in case an identical code name already exists
string codeName = await contentItemCodeNameProvider.Get(displayName);

// Use the 'codeName' value in content item APIs...
```

[> Back to list of examples](api/content-management/content-items#toc)
### Create content item language variants
C#
Copy
```
/* Content item language variants must be based on existing content items.
    When creating new items, provide instances of 'CreateLanguageVariantParameters' (metadata),
    and 'ContentItemData' (data).
*/

// Gets the id of the item on which to base the new language variants
string STORE_CONTENT_TYPE = "Wonka.CandyStore";
ContentItemQueryBuilder builder =
        new ContentItemQueryBuilder()
                .ForContentType(STORE_CONTENT_TYPE, q =>
                {
                    q.TopN(1)
                        .Where(w => w.WhereEquals("StoreName", "Prague-Hradcany"));
                });

var itemId = await contentQueryExecutor
                .GetResult<int>(builder, rowData =>
                {
                    var contentItemId = rowData.ContentItemID;
                    return contentItemId;
                },
                // Retrieves the latest version of items
                new ContentQueryExecutionOptions() { ForPreview = true });

// Prepares the language variant metadata
string languageVariantDisplayName = "Prague-Hradcany-de";
// Must correspond to a language defined in the 'Languages' application.
string languageName = "de";
var languageVariantParams =
                new CMS.ContentEngine.CreateLanguageVariantParameters(itemId.First(),
                                                    languageVariantDisplayName,
                                                    languageName);

/* Prepares the item data for the target language variant
    This example assumes a content type with the following fields:
        -- Data type: Text; Code name: StoreName
        -- Data type: Long text; Code name: StoreDescription
        -- Data type: Content items; Code name: StoreReferences
    To find the underlying C# type of other data types, see 'Data type management' in the documentation. */
ContentItemData itemData = new ContentItemData(new Dictionary<string, object>{
    { "StoreName", "Prag-Hradcany" },
    { "StoreDescription", "Der erste Wonka-Süßwarenladen in der Tschechischen Republik." },
    // Creates a reference to an existing content item
    { "StoreReferences", new List<ContentItemReference>()
                { new ContentItemReference()
                        { Identifier = Guid.Parse("a82af562-c411-47c9-8fee-ee55428a706f") }
                }
    }
});

if (await contentItemManager.TryCreateLanguageVariant(languageVariantParams, itemData))
{
    Console.WriteLine("Language variant successfully created.");
}
else
    throw new Exception("Something went wrong.");
```

[> Back to list of examples](api/content-management/content-items#toc)
### Publish content items
C#
Copy
```
// This example demonstrates how to publish content items
// That is, move an item from 'Draft' (or the final step in a workflow) to 'Published'

// Gets the id of the item to publish
string STORE_CONTENT_TYPE = "Wonka.CandyStore";
string languageName = "en";

ContentItemQueryBuilder builder =
        new ContentItemQueryBuilder()
                .ForContentType(STORE_CONTENT_TYPE, q =>
                {
                    q.TopN(1)
                        .Where(w => w.WhereEquals("StoreName", "Prague-Hradcany"));
                });

var itemId = await contentQueryExecutor
                .GetResult<int>(builder, rowData =>
                {
                    var contentItemId = rowData.ContentItemID;
                    return contentItemId;
                },
                // Retrieves the latest version of items
                new ContentQueryExecutionOptions() { ForPreview = true });

// Publishes the item
// For items under workflow, 'TryPublish' bypasses all remaining workflow steps and moves the item to the 'Published' status
if (await contentItemManager.TryPublish(itemId.First(), languageName))
{
    Console.WriteLine("Item successfully published");
}
else
    throw new Exception("Something went wrong.");
```

[> Back to list of examples](api/content-management/content-items#toc)
### Schedule publishing for content items
C#
Copy
```
// This example demonstrates how to schedule content items to be published at a future date and time

// Prepares a publish date 7 days from the current date and time
var publishDateTime = DateTime.Now.AddDays(7);

// Gets the id of the item to schedule
string STORE_CONTENT_TYPE = "Wonka.CandyStore";
string languageName = "en";

ContentItemQueryBuilder builder =
        new ContentItemQueryBuilder()
                .ForContentType(STORE_CONTENT_TYPE, q =>
                {
                    q.TopN(1)
                        .Where(w => w.WhereEquals("StoreName", "Prague-Hradcany"));
                });

var itemId = await contentQueryExecutor
                .GetResult<int>(builder, rowData =>
                {
                    var contentItemId = rowData.ContentItemID;
                    return contentItemId;
                },
                // Retrieves the latest version of items
                new ContentQueryExecutionOptions() { ForPreview = true });

/* Schedules the item to be published
   - Throws an exception if the item's status is not 'VersionStatus.Draft' or 'VersionStatus.InitialDraft' (this includes custom workflow steps).
   - If the specified date and time is not in the future, the item gets published immediately.
   - For items under workflow, 'SchedulePublish' bypasses all remaining workflow steps
     and moves the item to a system step representing the 'Scheduled' status in the UI.
*/
await contentItemManager.SchedulePublish(itemId.First(), languageName, publishDateTime);
```

[> Back to list of examples](api/content-management/content-items#toc)
### Cancel scheduled publishing for content items
C#
Copy
```
// This example demonstrates how to cancel a content item's scheduled publishing

// Gets the id of the item for which publishing will be canceled
string STORE_CONTENT_TYPE = "Wonka.CandyStore";
string languageName = "en";

ContentItemQueryBuilder builder =
        new ContentItemQueryBuilder()
                .ForContentType(STORE_CONTENT_TYPE, q =>
                {
                    q.TopN(1)
                        .Where(w => w.WhereEquals("StoreName", "Prague-Hradcany"));
                });

var itemId = await contentQueryExecutor
                .GetResult<int>(builder, rowData =>
                {
                    var contentItemId = rowData.ContentItemID;
                    return contentItemId;
                },
                // Retrieves the latest version of items
                new ContentQueryExecutionOptions() { ForPreview = true });


// Checks that the item is scheduled to be published
if (await contentItemManager.IsPublishScheduled(itemId.First(), languageName))
{
    /* Cancels the scheduled publishing for the item
       - For items under workflow, 'CancelScheduledPublish' returns the item to the final custom-defined workflow step.
       - If the item is also scheduled to be unpublished, the unpublish is canceled as well.
    */
    await contentItemManager.CancelScheduledPublish(itemId.First(), languageName);
}
```

[> Back to list of examples](api/content-management/content-items#toc)
### Unpublish content items
C#
Copy
```
// This example demonstrates how to unpublish content items --
// move the items from the 'Published' status to 'Unpublished'.

// Gets the id of the item to unpublish
string STORE_CONTENT_TYPE = "Wonka.CandyStore";
string languageName = "en";

ContentItemQueryBuilder builder =
        new ContentItemQueryBuilder()
                .ForContentType(STORE_CONTENT_TYPE, q =>
                {
                    q.TopN(1)
                        .Where(w => w.WhereEquals("StoreName", "Prague-Hradcany"));
                });

var itemId = await contentQueryExecutor
                .GetResult<int>(builder, rowData =>
                {
                    var contentItemId = rowData.ContentItemID;
                    return contentItemId;
                });

// Unpublishes the content item
if (await contentItemManager.TryUnpublish(itemId.First(), languageName))
{
    Console.WriteLine("Item successfully unpublished.");
}
else
    throw new Exception("Something went wrong.");
```

[> Back to list of examples](api/content-management/content-items#toc)
### Schedule unpublishing for content items
C#
Copy
```
// This example demonstrates how to schedule content items to be unpublished at a future date and time

// Prepares an unpublish date 7 days from the current date and time
var unpublishDateTime = DateTime.Now.AddDays(7);

// Gets the id of the item to schedule
string STORE_CONTENT_TYPE = "Wonka.CandyStore";
string languageName = "en";

ContentItemQueryBuilder builder =
        new ContentItemQueryBuilder()
                .ForContentType(STORE_CONTENT_TYPE, q =>
                {
                    q.TopN(1)
                        .Where(w => w.WhereEquals("StoreName", "Prague-Hradcany"));
                });

var itemId = await contentQueryExecutor
                .GetResult<int>(builder, rowData =>
                {
                    var contentItemId = rowData.ContentItemID;
                    return contentItemId;
                },
                // Retrieves the latest version of items
                new ContentQueryExecutionOptions() { ForPreview = true });

/* Schedules the item to be unpublished
   - The item must either be published or scheduled to be published. If the item is scheduled to be published,
     the publish date must be before the specified unpublish date. Otherwise the method throws an exception.
   - If the specified unpublish date and time is not in the future, the item gets unpublished immediately.
*/
await contentItemManager.ScheduleUnpublish(itemId.First(), languageName, unpublishDateTime);
```

[> Back to list of examples](api/content-management/content-items#toc)
### Cancel scheduled unpublishing for content items
C#
Copy
```
// This example demonstrates how to cancel a content item's scheduled unpublishing

// Gets the id of the item for which unpublishing will be canceled
string STORE_CONTENT_TYPE = "Wonka.CandyStore";
string languageName = "en";

ContentItemQueryBuilder builder =
        new ContentItemQueryBuilder()
                .ForContentType(STORE_CONTENT_TYPE, q =>
                {
                    q.TopN(1)
                        .Where(w => w.WhereEquals("StoreName", "Prague-Hradcany"));
                });

var itemId = await contentQueryExecutor
                .GetResult<int>(builder, rowData =>
                {
                    var contentItemId = rowData.ContentItemID;
                    return contentItemId;
                },
                // Retrieves the latest version of items
                new ContentQueryExecutionOptions() { ForPreview = true });


// Checks that the item is scheduled to be unpublished
if (await contentItemManager.IsUnpublishScheduled(itemId.First(), languageName))
{
    // Cancels the scheduled unpublishing for the item
    await contentItemManager.CancelScheduledUnpublish(itemId.First(), languageName);
}
```

[> Back to list of examples](api/content-management/content-items#toc)
### Retrieve usages of a content item
C#
Copy
```
// Demonstrates how to retrieve direct usages of a given content item

// Gets the id of the item for which to retrieve usages
string STORE_CONTENT_TYPE = "Wonka.CandyStore";
string languageName = "en";

ContentItemQueryBuilder builder =
    new ContentItemQueryBuilder()
            .ForContentType(STORE_CONTENT_TYPE, q =>
            {
                q.TopN(1)
                    .Where(w => w.WhereEquals("StoreName", "Prague-Hradcany"));
            });

var itemId = await contentQueryExecutor
            .GetResult<int>(builder, rowData =>
            {
                var contentItemId = rowData.ContentItemID;
                return contentItemId;
            });

// Retrieves direct usages of a content item specified by its
// contentItemId and its languageName.
await contentItemUsageRetriever.Retrieve(itemId.First(), languageName);
```

[> Back to list of examples](api/content-management/content-items#toc)
### Delete content items
C#
Copy
```
// Demonstrates how to permanently delete content items

// Gets the id of the item to delete
string STORE_CONTENT_TYPE = "Wonka.CandyStore";
string languageName = "en";

ContentItemQueryBuilder builder =
    new ContentItemQueryBuilder()
            .ForContentType(STORE_CONTENT_TYPE, q =>
            {
                q.TopN(1)
                    .Where(w => w.WhereEquals("StoreName", "Prague-Hradcany"));
            });

var itemId = await contentQueryExecutor
            .GetResult<int>(builder, rowData =>
            {
                var contentItemId = rowData.ContentItemID;
                return contentItemId;
            });

// Permanently deletes the item language variant from the database.
// Set the 'Permanently' parameter to false to retain the item in the recycle bin.
// If this was the last language variant remaining,
// deletes all associated metadata as well.
await contentItemManager.Delete(
            new DeleteContentItemParameters(itemId.First(), languageName)
            {
                Permanently = true,
            });
```

[> Back to list of examples](api/content-management/content-items#toc)
### Create content item drafts
C#
Copy
```
// Demonstrates how to create a new draft version for a content item with the 'Published' or 'Unpublished' status

// Gets the id of the item
string STORE_CONTENT_TYPE = "Wonka.CandyStore";
string languageName = "en";
ContentItemQueryBuilder builder =
        new ContentItemQueryBuilder()
                .ForContentType(STORE_CONTENT_TYPE, q =>
                {
                    q.TopN(1)
                        .Where(w => w.WhereEquals("StoreName", "Prague-Hradcany"));
                });

var itemId = await contentQueryExecutor
                .GetResult<int>(builder, rowData =>
                {
                    var contentItemId = rowData.ContentItemID;
                    return contentItemId;
                });

// Creates a new draft version of the item
if (await contentItemManager.TryCreateDraft(itemId.First(), languageName))
{
    Console.WriteLine($"New draft version successfully created.");
}
else
    throw new Exception("Something went wrong.");
```

[> Back to list of examples](api/content-management/content-items#toc)
### Update content item drafts
C#
Copy
```
// This example demonstrates how to update content items

// Gets the id of the item to update
string STORE_CONTENT_TYPE = "Wonka.CandyStore";
string languageName = "en";
ContentItemQueryBuilder builder =
        new ContentItemQueryBuilder()
                .ForContentType(STORE_CONTENT_TYPE, q =>
                {
                    q.TopN(1)
                        .Where(w => w.WhereEquals("StoreName", "Prague-Hradcany"));
                });

var itemId = await contentQueryExecutor
                .GetResult<int>(builder, rowData =>
                {
                    var contentItemId = rowData.ContentItemID;
                    return contentItemId;
                },
                new ContentQueryExecutionOptions() { ForPreview = true });

ContentItemData updatedItemData = new ContentItemData(new Dictionary<string, object> {
        { "StoreName", "Prague-Hradcany" },
        { "StoreDescription", "The first Wonka candy shop in Czech Republic." },
        // A reference to an existing content item
        { "StoreReferences", new List<ContentItemReference>()
                    { new ContentItemReference()
                            { Identifier = Guid.Parse("a82ad562-c411-47c9-8fee-ee55428a706f") } } }
    });

await contentItemManager.TryUpdateDraft(itemId.First(),
                                        languageName,
                                        updatedItemData);
```

[> Back to list of examples](api/content-management/content-items#toc)
## Content item assets
### Upload content item assets from files
C#
Copy
```
// This example shows how to upload content item assets from a file on the filesystem
// using files API found in the CMS.IO namespace. The process can be used both when creating or updating assets.


// Gets the metadata for content item creation.
// Identical to regular content item operations.
string LOGO_CONTENT_TYPE = "Wonka.StoreLogo";
string contentItemDisplayName = "Prague-Hradcany-Logo";
string languageName = "en";
string workspaceName = "MyWorkspace";
CreateContentItemParameters createParams = new CreateContentItemParameters(
                                                                LOGO_CONTENT_TYPE,
                                                                null,
                                                                contentItemDisplayName,
                                                                languageName,
                                                                workspaceName);

// Gets the file information from a file on the filesystem
var assetPath = "C:/data/images/hradcany-logo.jpg";
var file = CMS.IO.FileInfo.New(assetPath);

// Creates a metadata object that describes the uploaded asset
var assetMetadata = new ContentItemAssetMetadata()
{
    Extension = file.Extension,
    Identifier = Guid.NewGuid(),
    LastModified = DateTime.Now,
    Name = file.Name,
    Size = file.Length
};

// Creates a source object used to locate the asset and merges it with the metadata object
var fileSource = new ContentItemAssetFileSource(file.FullName, false);
var assetMetadataWithSource = new ContentItemAssetMetadataWithSource(fileSource, assetMetadata);

/* Populates content item data. This example assumes a content type with the following fields:
        -- Data type: Content item asset; Code name: StoreLogo
    To find the underlying C# type of other data types, see 'Data type management' in the Kentico documentation.
*/
ContentItemData itemData = new ContentItemData(new Dictionary<string, object>{
    { "StoreLogo", assetMetadataWithSource }
});

// Creates the content item in the database
await contentItemManager.Create(createParams, itemData);
```

[> Back to list of examples](api/content-management/content-items#toc)
### Upload content item assets from data streams
C#
Copy
```
// This example shows how to upload content item assets from 'CMS.IO.FileStream'
// The process can be used both when creating or updating assets.


// Gets the metadata for content item creation.
// Identical to regular content item operations.
string LOGO_CONTENT_TYPE = "Wonka.StoreLogo";
string contentItemDisplayName = "Prague-Hradcany-Logo";
string contentItemCodeName = "Prague-Hradcany-CandyStore-Logo";
string languageName = "en";
string workspaceName = "MyWorkspace";
CreateContentItemParameters createParams = new CreateContentItemParameters(
                                                                LOGO_CONTENT_TYPE,
                                                                contentItemCodeName,
                                                                contentItemDisplayName,
                                                                languageName,
                                                                workspaceName);

// Opens a 'CMS.IO.FileStream' with the asset binary
var assetPath = "C:/data/images/hradcany-logo.jpg";
var fileStream = File.Open(assetPath, FileMode.Open, FileAccess.ReadWrite);

// Creates a metadata object that describes the uploaded asset
var assetMetadata = new ContentItemAssetMetadata()
{
    Extension = Path.GetExtension(assetPath),
    Identifier = Guid.NewGuid(),
    LastModified = DateTime.Now,
    Name = Path.GetFileName(assetPath),
    Size = fileStream.Length
};

// Creates a source object used to locate the asset and merges it with the metadata object
var fileSource = new ContentItemAssetStreamSource((CancellationToken cancellationToken) => Task.FromResult<System.IO.Stream>(fileStream));
var assetMetadataWithSource = new ContentItemAssetMetadataWithSource(fileSource, assetMetadata);

/* Populates content item data. This example assumes a content type with the following fields:
        -- Data type: Content item asset; Code name: StoreLogo
    To find the underlying C# type of other data types, see 'Data type management' in the Kentico documentation.
*/
ContentItemData itemData = new ContentItemData(new Dictionary<string, object>{
    { "StoreLogo", assetMetadataWithSource }
});

// Creates the content item in the database
await contentItemManager.Create(createParams, itemData);
```

[> Back to list of examples](api/content-management/content-items#toc)