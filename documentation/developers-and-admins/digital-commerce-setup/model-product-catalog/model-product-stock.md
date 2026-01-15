# Model product stock
  * [ Copy page link ](documentation/developers-and-admins/digital-commerce-setup/model-product-catalog/model-product-stock#) | [Get HelpService ID](documentation/developers-and-admins/digital-commerce-setup/model-product-catalog/model-product-stock#)
Core MVC 5


[✖](documentation/developers-and-admins/digital-commerce-setup/model-product-catalog/model-product-stock# "Close page link panel") [Copy to clipboard](documentation/developers-and-admins/digital-commerce-setup/model-product-catalog/model-product-stock#)
**Advanced license required**   
  
Features described on this page require the Xperience by Kentico **Advanced** license tier. 
Product stock management is a critical component of any commerce system. This page covers key considerations for implementing a product stock model in Xperience by Kentico.
The product stock model manages the relationship between products and their available quantities. It provides functionality to track, update, and display stock levels while handling various business scenarios such as purchases, returns, and restocking. We recommend that you implement the product stock model as a custom module to ensure flexibility and maintainability.
To create a custom product stock management module, follow these main steps:
  1. Create the core [custom module](documentation/developers-and-admins/digital-commerce-setup/model-product-catalog/model-product-stock#product-stock-module) with database structure and object dependencies.
  2. Build [administration interfaces](documentation/developers-and-admins/digital-commerce-setup/model-product-catalog/model-product-stock#management-interfaces) for listing and editing product stock records.
  3. Implement automatic or manual [stock record creation processes](documentation/developers-and-admins/digital-commerce-setup/model-product-catalog/model-product-stock#product-stock-creation).
  4. Handle [business logic](documentation/developers-and-admins/digital-commerce-setup/model-product-catalog/model-product-stock#business-logic-implementation) of stock updates for purchases, returns, and reservations.
  5. Implement [validation and business rules](documentation/developers-and-admins/digital-commerce-setup/model-product-catalog/model-product-stock#validation-and-business-rules) to ensure data integrity and implement business constraints.
  6. [Display product stock](documentation/developers-and-admins/digital-commerce-setup/model-product-catalog/model-product-stock#display-product-stock) information to customers on your storefront.


## Product stock module
Create a [custom module](documentation/developers-and-admins/customization/object-types) to handle product stock management. Within the module, create a class with the necessary fields. Each record of this class represents the stock level for a specific product. The fields typically include:
  * Content item ID – unique identifier linking to the product’s content item. 
    * You need to manually set the reference to the content item object in the initializer of the `TYPEINFO` member of the generated `*Info` class. This ensures that the stock information is always associated with a product and that the when the product is deleted, the stock information is also removed.
C#
**Example - ProductStockInfo**
Copy
```
public class ProductStockInfo
{
    public static readonly ObjectTypeInfo TYPEINFO = new ObjectTypeInfo(...)
    {
        DependsOn =
        [
            new ObjectDependency(nameof(ProductStockContentItemID), "cms.contentitem", ObjectDependencyEnum.Required)
        ],
    };
}
 
 
 
 


```

  * Product stock value – current quantity available for purchase.


Depending on your requirements, you may use a simple implementation with just content item ID and product stock value, or a more complex model that also includes fields such as reserved stock for pending orders and minimum threshold value.
## Management interfaces
The management interface consists of several interconnected components that work together to provide a complete administration experience for product stock records. [Create the following UI pages](documentation/developers-and-admins/customization/extend-the-administration-interface) to manage product stock.
  * [Create the application entry point](documentation/developers-and-admins/digital-commerce-setup/model-product-catalog/model-product-stock#create-the-application-entry-point) – Register the stock management app in the admin interface
  * [Create the listing page](documentation/developers-and-admins/digital-commerce-setup/model-product-catalog/model-product-stock#create-the-listing-page) – Display all stock records with product information
  * [Create the edit section](documentation/developers-and-admins/digital-commerce-setup/model-product-catalog/model-product-stock#create-the-edit-section) – Handle URL routing for individual record editing
  * [Create the edit page](documentation/developers-and-admins/digital-commerce-setup/model-product-catalog/model-product-stock#create-the-edit-page) – Provide the actual editing form interface
  * [Create the product metadata retriever](documentation/developers-and-admins/digital-commerce-setup/model-product-catalog/model-product-stock#create-the-product-metadata-retriever) – Service to retrieve product display names
  * [Register dependencies](documentation/developers-and-admins/digital-commerce-setup/model-product-catalog/model-product-stock#register-dependencies) – Configure dependency injection for all services


### Create the application entry point
First, create an application class to register your product stock management interface in the administration:
C#
**ProductStockApplication.cs**
Copy
```
using CMS.Membership;
using Kentico.Xperience.Admin.Base;
using Kentico.Xperience.Admin.DigitalCommerce.UIPages;

// Registers the application in the admin interface
[assembly: UIApplication(ProductStockApplication.IDENTIFIER, typeof(ProductStockApplication), "productstock", "Product stock", DigitalCommerceApplicationCategories.DIGITAL_COMMERCE, Icons.MoneyBill, TemplateNames.SECTION_LAYOUT)]

namespace YourProject.AdminComponents
{
    // Sets permissions required to access the application
    // Create and delete permissions are not needed if stock records are created and deleted automatically with products
    [UIPermission(SystemPermissions.VIEW, "{$base.roles.permissions.view$}")]
    [UIPermission(SystemPermissions.UPDATE, "{$base.roles.permissions.update}")]
    public sealed class ProductStockApplication : ApplicationPage
    {
        public const string IDENTIFIER = "YourProject.Application.ProductStock";
    }
}
```

### Create the listing page
The listing page displays all product stock records with product names, SKUs, and stock levels. **Important** : To display product information, you must join the `CMS_ContentItemLanguageMetadata` and `CMS_ContentItemCommonData` tables to obtain the product display name and product SKU.
C#
**ProductStockList.cs**
Copy
```
using CMS.Base;
using CMS.Commerce;
using CMS.DataEngine;
using Kentico.Xperience.Admin.Base;

// Registers this page as the main listing page for the ProductStock application
[assembly: UIPage(typeof(ProductStockApplication), "list", typeof(ProductStockList), "List of product stock", TemplateNames.LISTING, UIPageOrder.First)]

namespace YourProject.AdminComponents
{
    // Product stock listing page that displays all stock records with product information
    public sealed class ProductStockList : ListingPage
    {
        private readonly DefaultContentLanguageRetriever defaultContentLanguageRetriever;
        private readonly IProductQuantityFormatter productQuantityFormatter;

        public ProductStockList(DefaultContentLanguageRetriever defaultContentLanguageRetriever, IProductQuantityFormatter productQuantityFormatter)
        {
            this.defaultContentLanguageRetriever = defaultContentLanguageRetriever;
            this.productQuantityFormatter = productQuantityFormatter;
        }

        // Specifies which object type this listing page manages
        protected override string ObjectType => ProductStockInfo.OBJECT_TYPE;

        public override async Task ConfigurePage()
        {
            // Gets the default language to ensure consistent data retrieval across multilingual content
            var defaultContentLanguage = await defaultContentLanguageRetriever.Get();

            // Adds edit action for each row in the listing
            PageConfiguration.AddEditRowAction<ProductStockEditSection>();

            // Configures the columns that will be displayed in the listing grid
            PageConfiguration.ColumnConfigurations
                            // Product name comes from ContentItemLanguageMetadata table
                            .AddColumn("ContentItemLanguageMetadataDisplayName", "Product name", searchable: true)
                            // SKU comes from reusable field schema stored in CMS_ContentItemCommonData
                            .AddColumn("ProductSKUCode", "SKU", searchable: true)
                            // Stock value from the ProductStockInfo table with custom formatting
                            .AddColumn(nameof(ProductStockInfo.ProductStockValue), "Stock", formatter: StockFormatter);

            // Joins necessary tables to retrieve product information
            // This is required because ProductStockInfo only contains ContentItemID, not product details
            PageConfiguration.QueryModifiers.AddModifier(query =>
                query.Source(
                    s => s
                        // Joins with ContentItemLanguageMetadata to get product display names
                        .Join(
                            "CMS_ContentItemLanguageMetadata",
                            new WhereCondition($"[CMS_ContentItemLanguageMetadata].[ContentItemLanguageMetadataContentItemID] = [{ProductStockInfo.TYPEINFO.ClassStructureInfo.TableName}].[{nameof(ProductStockInfo.ProductStockContentItemID)}]")
                                // Filters by default language to avoid duplicate rows in multilingual setups
                                .And(new WhereCondition().WhereEquals("ContentItemLanguageMetadataContentLanguageID", defaultContentLanguage.ContentLanguageID))
                        )
                        // Joins with ContentItemCommonData to get reusable field schema data (like SKU)
                        .Join(
                            "CMS_ContentItemCommonData",
                            new WhereCondition($"[CMS_ContentItemCommonData].[ContentItemCommonDataContentItemID] = [{ProductStockInfo.TYPEINFO.ClassStructureInfo.TableName}].[{nameof(ProductStockInfo.ProductStockContentItemID)}]")
                                // Filters by default language for consistency
                                .And(new WhereCondition().WhereEquals("ContentItemCommonDataContentLanguageID", defaultContentLanguage.ContentLanguageID))
                        )
                )
            );

            await base.ConfigurePage();
        }

        // Formats the stock value for display using the commerce quantity formatter
        private string StockFormatter(object value, IDataContainer dataContainer)
        {
            return productQuantityFormatter.Format((decimal)value, new ProductQuantityFormatContext());
        }
    }
}
```

### Create the edit section
The edit section defines URL parameterization and routing for editing specific product stock records. It acts as a container or entry point for the edit operation:
C#
**ProductStockEditSection.cs**
Copy
```
using CMS.DataEngine;
using Kentico.Xperience.Admin.Base;

// Registers this as a parameterized edit section that handles routing for individual stock records
[assembly: UIPage(typeof(ProductStockList), PageParameterConstants.PARAMETERIZED_SLUG, typeof(ProductStockEditSection), "Product stock section caption", TemplateNames.SECTION_LAYOUT, UIPageOrder.NoOrder)]

namespace YourProject.AdminComponents
{
    // Edit section that handles URL parameterization and routing for editing specific product stock records.
    // Acts as a container/entry point for the edit operation.
    public sealed class ProductStockEditSection : EditSectionPage<ProductStockInfo>
    {
        private readonly ProductMetadataRetriever productMetadataRetriever;

        public ProductStockEditSection(ProductMetadataRetriever productMetadataRetriever)
        {
            this.productMetadataRetriever = productMetadataRetriever;
        }

        // Gets the display name for the object being edited (used in breadcrumbs and page titles)
        protected override async Task<string> GetObjectDisplayName(BaseInfo infoObject)
        {
            // Retrieves the product name for display in the edit section header
            // This ensures the page shows "Edit Product Stock - [Product Name]" instead of just an ID
            var product = await productMetadataRetriever.GetProductMetadata(infoObject as ProductStockInfo);
            return product.DisplayName;
        }
    }
}
```

### Create the edit page
Create a [UI form](documentation/developers-and-admins/customization/object-types/extend-system-object-types#Extendsystemobjecttypes-UIForms) for editing product stock records:
  1. Navigate to the **Modules** application → select your stock management module → **Classes** tab → select your product stock class → **UI forms** tab.
  2. Create a **New UI form** with the name _ProductStockEdit_ (code name).
  3. Switch to the **Fields** tab and add the fields you want to display on the form:
     * Product stock value field 
       * **Database column** : _The field of the product stock class that stores the stock value_
       * **Field caption** : Available stock value
       * **Enabled** : _Yes, selected_
       * **Form component** : Decimal number input
     * _(Optional)_ Product name field (for context, typically read-only) 
       * **Database column** : New field without database column
       * **Field name** : ProductStockProductName
       * **Data type** : Text
       * **Field caption** : Product name
       * **Enabled** : _No, not selected (this makes the field read-only)_
       * **Form component** : Text input
       * You need to ensure that the product name field is populated programmatically in the edit page code since it does not have a database column.


The edit page then renders the actual editing form where users interact with the product stock data:
C#
**ProductStockEdit.cs**
Copy
```
using System;
using System.Collections.Generic;
using System.Linq;
using System.Threading.Tasks;
using CMS.ContentEngine;
using Kentico.Xperience.Admin.Base;
using Kentico.Xperience.Admin.Base.Forms;

// Registers this as the main edit page within the ProductStockEditSection
[assembly: UIPage(typeof(ProductStockEditSection), "general", typeof(ProductStockEdit), "General", TemplateNames.EDIT, UIPageOrder.First)]

namespace YourProject.AdminComponents
{
    // The actual edit page where the editing form is rendered and user interaction happens.
    // Inherits from InfoEditPage&lt;ProductStockInfo&gt; to provide standard CRUD functionality.
    public sealed class ProductStockEdit : InfoEditPage<ProductStockInfo>
    {
        // Name of the UI form definition created in the admin interface
        private const string UI_FORM_COMPONENT_NAME = "ProductStockEdit";
        // Name of the field used to display the product name
        private const string PRODUCT_NAME_COMPONENT_NAME = "ProductStockProductName";

        private readonly ProductMetadataRetriever productMetadataRetriever;

        // Object ID parameter from the URL route (e.g., /edit/123)
        [PageParameter(typeof(IntPageModelBinder))]
        public override int ObjectId { get; set; }

        public ProductStockEdit(IFormComponentMapper formComponentMapper, IFormDataBinder formDataBinder,
            ProductMetadataRetriever productMetadataRetriever)
            : base(formComponentMapper, formDataBinder)
        {
            this.productMetadataRetriever = productMetadataRetriever;
        }

        public override async Task ConfigurePage()
        {
            // Specifies which UI form definition to use
            PageConfiguration.UIFormName = UI_FORM_COMPONENT_NAME;
            PageConfiguration.Headline = "Edit product stock";

            await base.ConfigurePage();
        }

        // Retrieves and modifies form items before they are displayed to the user
        // </summary>
        protected override async Task<ICollection<IFormItem>> GetFormItems()
        {
            var formItems = await base.GetFormItems();

            // Sets up the product name component to show which product this stock record belongs to
            await SetupProductNameComponent(formItems);

            return formItems;
        }

        // Sets up the product name component to display the associated product's name
        // This provides context to administrators about which product they're editing stock for
        private async Task SetupProductNameComponent(ICollection<IFormItem> formItems)
        {
            // Gets the current product stock record being edited
            var productStockInfo = await GetInfoObject();

            // Retrieves the product metadata to get the display name
            ContentItemLanguageMetadata productMetadata = await productMetadataRetriever.GetProductMetadata(productStockInfo);

            // Finds the product name form component and sets its value
            var productNameComponent = formItems.OfType<IFormComponent>()
                .FirstOrDefault(f => f.Name.Equals(PRODUCT_NAME_COMPONENT_NAME, StringComparison.OrdinalIgnoreCase));

            // Sets the product name (this is typically a read-only field for context)
            productNameComponent?.SetObjectValue(productMetadata.DisplayName);
        }
    }
}
```

### Create the product metadata retriever
The `ProductMetadataRetriever` must be implemented to retrieve product display names and other metadata. This service is essential for displaying correct product information in the management interface:
C#
**ProductMetadataRetriever.cs**
Copy
```
using System.Threading.Tasks;
using CMS.ContentEngine;
using Kentico.Xperience.Admin.Base.Authentication;
using Kentico.Xperience.Admin.Base.Forms;
using Microsoft.AspNetCore.Http;
using Microsoft.Extensions.DependencyInjection;

namespace YourProject.AdminComponents
{
    // Service responsible for retrieving product metadata (display names, etc.) from content items.
    // This is essential for displaying meaningful product information in the admin interface.
    public sealed class ProductMetadataRetriever
    {
        private readonly IContentItemManagerFactory contentItemManagerFactory;
        private readonly IHttpContextAccessor httpContextAccessor;
        private readonly DefaultContentLanguageRetriever defaultContentLanguageRetriever;

        public ProductMetadataRetriever(IContentItemManagerFactory contentItemManagerFactory,
            IHttpContextAccessor httpContextAccessor, DefaultContentLanguageRetriever defaultContentLanguageRetriever)
        {
            this.contentItemManagerFactory = contentItemManagerFactory;
            this.httpContextAccessor = httpContextAccessor;
            this.defaultContentLanguageRetriever = defaultContentLanguageRetriever;
        }

        /// <summary>
        /// Retrieves product metadata for a given product stock record
        /// </summary>
        /// <param name="productStockInfo">The product stock record</param>
        /// <returns>Content item language metadata containing the product's display name and other properties</returns>
        public async Task<ContentItemLanguageMetadata> GetProductMetadata(ProductStockInfo productStockInfo)
        {
            // Uses the default language to ensure consistent metadata retrieval
            var defaultContentLanguage = await defaultContentLanguageRetriever.Get();

            // Gets the current authenticated user for content manager context
            var currentUser = await httpContextAccessor.HttpContext.RequestServices
                .GetRequiredService<IAuthenticatedUserAccessor>().Get();

            // Creates content manager with proper user context for security
            var contentItemManager = contentItemManagerFactory.Create(currentUser.UserID);

            // Retrieves the product metadata using the content item ID stored in the stock record
            // This gets the product name and other language-specific metadata
            var productMetadata = await contentItemManager.GetContentItemLanguageMetadata(
                productStockInfo.ProductStockContentItemID,
                defaultContentLanguage.ContentLanguageName);

            return productMetadata;
        }
    }
}
```

### Create the default content language retriever
Create a service to retrieve the default content language, which is needed for consistent data retrieval:
C#
**DefaultContentLanguageRetriever.cs**
Copy
```
using System.Linq;
using System.Threading;
using System.Threading.Tasks;
using CMS.ContentEngine;
using CMS.DataEngine;
using CMS.Helpers;

namespace YourProject.AdminComponents
{
    // Service for retrieving the default content language.
    // This ensures consistent data retrieval across multilingual content scenarios.
    public sealed class DefaultContentLanguageRetriever
    {
        // Cache duration in minutes (24 hours)
        private const int ONE_DAY = 24 * 60;

        private readonly IInfoProvider<ContentLanguageInfo> contentLanguageInfoProvider;
        private readonly IProgressiveCache progressiveCache;
        private readonly ICacheDependencyBuilderFactory cacheDependencyBuilderFactory;

        public DefaultContentLanguageRetriever(IInfoProvider<ContentLanguageInfo> contentLanguageInfoProvider, IProgressiveCache progressiveCache, ICacheDependencyBuilderFactory cacheDependencyBuilderFactory)
        {
            this.contentLanguageInfoProvider = contentLanguageInfoProvider;
            this.progressiveCache = progressiveCache;
            this.cacheDependencyBuilderFactory = cacheDependencyBuilderFactory;
        }

        /// <summary>
        /// Retrieves the default content language with caching for performance
        /// </summary>
        /// <param name="cancellationToken">Cancellation token</param>
        /// <returns>The default ContentLanguageInfo</returns>
        public async Task<ContentLanguageInfo> Get(CancellationToken cancellationToken = default)
        {
            // Uses progressive cache to avoid repeated database queries
            return await progressiveCache.LoadAsync(async (cacheSettings, token) =>
            {
                // Sets up cache dependency to invalidate when languages change
                var cacheDependencyBuilder = cacheDependencyBuilderFactory.Create();
                cacheSettings.CacheDependency = cacheDependencyBuilder
                    .ForInfoObjects<ContentLanguageInfo>()
                        .All()
                        .Builder()
                    .Build();

                // Queries for the default language (marked as default in the system)
                var result = await contentLanguageInfoProvider.Get()
                   .WhereTrue(nameof(ContentLanguageInfo.ContentLanguageIsDefault))
                   .TopN(1)
                   .GetEnumerableTypedResultAsync(cancellationToken: cancellationToken);

                return result.FirstOrDefault();
            },
            // Caches for one day with automatic invalidation when content languages change
            new CacheSettings(ONE_DAY, true, nameof(DefaultContentLanguageRetriever), nameof(Get)), cancellationToken);
        }
    }
}
```

### Register dependencies
Finally, you need to register the dependencies in your dependency injection container:
C#
Copy
```
// Add these services to your Program.cs or service registration
// These are required for the product stock management interface to function properly
builder.Services.AddTransient<ProductMetadataRetriever>();
builder.Services.AddTransient<DefaultContentLanguageRetriever>();
```

## Product stock creation
### Automatic stock creation
Consider implementing automatic stock creation when new products are added:
  * Set up [event handlers](documentation/developers-and-admins/customization/handle-global-events) for product creation
  * Initialize default stock values based on business rules
  * Ensure proper data consistency between products and stock


The following code snippet demonstrates a simple event handler for automatic stock creation using the modern object event handling pattern.
C#
**Example - Automatic stock creation**
Copy
```
using System.Linq;
using System.Threading;
using System.Threading.Tasks;

using CMS.ContentEngine;
using CMS.DataEngine;

namespace Acme.Commerce;

// Event handler triggered after a new content item is created
// Automatically creates a stock record for product content items
public class ProductStockCreationHandler : IInfoObjectEventHandler<InfoObjectAfterInsertEvent<ContentItemInfo>>
{
    // An instance of the product stock info provider, obtained via dependency injection
    private readonly IInfoProvider<ProductStockInfo> productStockInfoProvider;

    public ProductStockCreationHandler(IInfoProvider<ProductStockInfo> productStockInfoProvider)
    {
        this.productStockInfoProvider = productStockInfoProvider;
    }

    // Invoked by the synchronous Set operation
    public void Handle(InfoObjectAfterInsertEvent<ContentItemInfo> infoObjectEvent)
    {
        ContentItemInfo contentItem = infoObjectEvent.InfoObject;

        // Only handle product content items
        if (IsProductContentItem(contentItem))
        {
            CreateProductStockRecord(contentItem.ContentItemID);
        }
    }

    // Invoked by the asynchronous SetAsync operation
    public async Task HandleAsync(InfoObjectAfterInsertEvent<ContentItemInfo> infoObjectEvent, CancellationToken cancellationToken)
    {
        ContentItemInfo contentItem = infoObjectEvent.InfoObject;

        // Only handle product content items
        if (IsProductContentItem(contentItem))
        {
            await CreateProductStockRecordAsync(contentItem.ContentItemID, cancellationToken);
        }
    }

    // Checks if the content item is a product
    private static bool IsProductContentItem(ContentItemInfo contentItem)
    {
        // Replace "YourProject.Product" with your actual product content type name
        return contentItem.ContentTypeName.Equals("YourProject.Product", System.StringComparison.OrdinalIgnoreCase);
    }

    // Creates a product stock record synchronously
    private void CreateProductStockRecord(int contentItemId)
    {
        // Checks if stock record already exists
        var existingProductStock = productStockInfoProvider.Get()
                                                           .WhereEquals(nameof(ProductStockInfo.ProductStockContentItemID), contentItemId)
                                                           .GetEnumerableTypedResult()
                                                           .FirstOrDefault();

        if (existingProductStock == null)
        {
            // Creates a new product stock record
            productStockInfoProvider.Set(new ProductStockInfo
            {
                ProductStockContentItemID = contentItemId,
                ProductStockValue = 0, // Sets the initial stock value to 0
            });
        }
    }

    // Creates a product stock record asynchronously
    private async Task CreateProductStockRecordAsync(int contentItemId, CancellationToken cancellationToken)
    {
        // Checks if stock record already exists
        var existingProductStock = await productStockInfoProvider.Get()
                                                                  .WhereEquals(nameof(ProductStockInfo.ProductStockContentItemID), contentItemId)
                                                                  .GetEnumerableTypedResultAsync(cancellationToken: cancellationToken);

        if (!existingProductStock.Any())
        {
            // Creates a new product stock record
            await productStockInfoProvider.SetAsync(new ProductStockInfo
            {
                ProductStockContentItemID = contentItemId,
                ProductStockValue = 0, // Sets the initial stock value to 0
            }, cancellationToken);
        }
    }
}
```

To use this event handler, you must register it in your application’s service collection. Create a [custom module](documentation/developers-and-admins/customization/run-code-on-application-startup) and override the `OnPreInit` method:
C#
**Register the event handler**
Copy
```
using CMS;
using CMS.Core;
using CMS.ContentEngine;
using CMS.DataEngine;

[assembly: RegisterModule(typeof(ProductStockModule))]

public class ProductStockModule : Module
{
    public ProductStockModule() : base(nameof(ProductStockModule))
    {
    }

    // OnPreInit allows you to access IServiceCollection via ModulePreInitParameters
    protected override void OnPreInit(ModulePreInitParameters parameters)
    {
        base.OnPreInit(parameters);

        // Registers the product stock creation event handler
        parameters.Services.AddInfoObjectEventHandler<InfoObjectAfterInsertEvent<ContentItemInfo>, ProductStockCreationHandler>();
    }
}
```

### Manual stock creation
Provide administration interface for manual stock creation and management:
  * Create an [editing interface](documentation/developers-and-admins/customization/extend-the-administration-interface/ui-pages/reference-ui-page-templates/edit-ui-page-template) where administrators can create new stock records
  * Consider adding bulk operations for mass stock updates


## Business logic implementation
### Purchase transactions
Handle stock reduction when products are purchased. The following example shows very simple stock management logic. In your project, you need handle any [edge cases and business rules](documentation/developers-and-admins/digital-commerce-setup/model-product-catalog/model-product-stock#validation-and-business-rules) specific to your requirements.
C#
**Example - Order service**
Copy
```
// Contains an instance of the product stock info provider, obtained via dependency injection
private readonly IInfoProvider<ProductStockInfo> productStockInfoProvider;

public async Task<string> CreateOrder(ShoppingCartDataModel shoppingCartData)
{
    // Contains IDs of products and their ordered quantities
    Dictionary<int /* ContentItemID */, decimal /* ItemQuantity */> orderedItems = [];

    foreach (var item in shoppingCartData.Items)
    {
        // ...

        // Collects ordered items and their quantities
        orderedItems.Add((product as IContentItemFieldsSource).SystemFields.ContentItemID, orderItem.OrderItemQuantity);
    }

    try
    {
        foreach (var orderedItem in orderedItems)
        {
            // Retrieves the product stock information for the ordered item
            var productStockInfo = (await productStockInfoProvider
                .Get()
                .WhereEquals(nameof(ProductStockInfo.ProductStockContentItemID), orderedItem.Key)
                .GetEnumerableTypedResultAsync())
                .FirstOrDefault();

            // Digital products may not have any associated stock
            if (productStockInfo != null)
            {
                // Decreases the stock value by the ordered quantity
                productStockInfo.ProductStockValue -= orderedItem.Value;

                // Updates the product stock record
                await productStockInfoProvider.SetAsync(productStockInfo);
            }
        }
    }
    catch (Exception ex)
    {
        logger.LogError(ex, $"Failed to decrease product stock for order {order.OrderNumber}");
    }
}
```

### Returns and restocking
Implement logic for increasing stock levels when products are returned or restocked:
  * Handle product returns by adding quantities back to available stock
  * Process restocking operations with proper validation


### Stock reservations
Consider managing temporary stock allocations for pending orders:
  * Reserve stock during the checkout process
  * Release reservations for abandoned carts
  * Convert reservations to actual purchases upon order completion


## Validation and business rules
Implement product stock validation and business rules to ensure data integrity:
  * Negative stock prevention – ensure stock levels cannot go below zero
  * Threshold alerts – consider generating notifications when stock falls below minimum levels
  * Concurrent access handling – prevent race conditions during simultaneous stock updates
  * Data integrity checks – validate relationships between products and stock records


## Display product stock
Display product stock information to customers on your storefront:
  * In-stock indicators – display indication whether a product is available for purchase
  * Quantity selectors – prevent customers from selecting more items than are in stock
  * Low stock warnings – consider alerting customers when quantities are limited and encourage them to purchase soon