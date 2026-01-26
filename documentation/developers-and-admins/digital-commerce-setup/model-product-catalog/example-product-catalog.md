---
source: https://docs.kentico.com/documentation/developers-and-admins/digital-commerce-setup/model-product-catalog/example-product-catalog
scrape_date: 2026-01-26
---

  * [Home](/documentation)
  * [Developers and admins](/documentation/developers-and-admins)
  * [Digital commerce setup](/documentation/developers-and-admins/digital-commerce-setup)
  * [Product catalog](/documentation/developers-and-admins/digital-commerce-setup/model-product-catalog)
  * Example - product catalog 


# Example - product catalog
**Advanced license required**   
  
Features described on this page require the Xperience by Kentico **Advanced** license tier. 
This page demonstrates a practical implementation of a product catalog using the [content hub](/documentation/business-users/content-hub) to store products as [reusable content items](/documentation/business-users/content-hub/content-items). The example follows the [product catalog modeling recommendations](/documentation/developers-and-admins/digital-commerce-setup/model-product-catalog) and showcases how to:
  * Define a [reusable field schema](/documentation/developers-and-admins/development/content-types/reusable-field-schemas) for common product fields
  * Create a product [content type](/documentation/developers-and-admins/development/content-types) that implements the schema
  * Use [taxonomies](/documentation/developers-and-admins/configuration/taxonomies) for product categorization
  * Retrieve and display products using the [content retriever API](/documentation/developers-and-admins/api/content-item-api)


## Content model overview
The example catalog uses the following content model structure:
  * **ProductFields** – a [reusable field schema](/documentation/developers-and-admins/development/content-types/reusable-field-schemas) containing common product properties (name, description, price, category).
  * **ProductSKU** – a [content type](/documentation/developers-and-admins/development/content-types) that implements the ProductFields schema and represents individual products.
  * **ProductCategory** – a [taxonomy](/documentation/developers-and-admins/configuration/taxonomies) for organizing products into categories.


[![Product catalog content model structure](/docsassets/documentation/example-product-catalog/content-model-structure.drawio.svg)](/docsassets/documentation/example-product-catalog/content-model-structure.drawio.svg)
This structure allows you to:
  * Reuse the ProductFields schema across multiple product types if needed.
  * Query products by their shared fields using the schema interface.
  * Filter products by category using taxonomy tags.


## Reusable field schema
The `ProductFields` reusable field schema defines the common fields shared by all products. When you [generate code for the schema](/documentation/developers-and-admins/api/generate-code-files-for-system-objects), you get an interface that content types can implement:
C#
**Generated ProductFields interface**
Copy
```
/// <summary>
/// Defines a contract for content types with the <see cref="ICodesamplesProductFields"/> reusable schema assigned.
/// </summary>
public interface ICodesamplesProductFields
{
    /// <summary>
    /// Code name of the reusable field schema.
    /// </summary>
    public const string REUSABLE_FIELD_SCHEMA_NAME = "Codesamples.ProductFields";


    /// <summary>
    /// ProductFieldsName.
    /// </summary>
    public string ProductFieldsName { get; set; }


    /// <summary>
    /// ProductFieldsDescription.
    /// </summary>
    public string ProductFieldsDescription { get; set; }


    /// <summary>
    /// ProductFieldsPrice.
    /// </summary>
    public decimal ProductFieldsPrice { get; set; }


    /// <summary>
    /// ProductFieldCategory.
    /// </summary>
    public IEnumerable<TagReference> ProductFieldCategory { get; set; }
}
```

The schema includes:
  * `ProductFieldsName` – the display name of the product
  * `ProductFieldsDescription` – a detailed product description
  * `ProductFieldsPrice` – the base price of the product
  * `ProductFieldCategory` – taxonomy tags for categorization


## Product content type
The `ProductSKU` content type implements the `ICodesamplesProductFields` interface, inheriting all fields from the reusable schema. The generated code provides a strongly-typed class for working with product data:
C#
**Generated ProductSKU class**
Copy
```
/// <summary>
 /// Represents a content item of type <see cref="ProductSKU"/>.
 /// </summary>
 [RegisterContentTypeMapping(CONTENT_TYPE_NAME)]
 public partial class ProductSKU : IContentItemFieldsSource, ICodesamplesProductFields
 {
     /// <summary>
     /// Code name of the content type.
     /// </summary>
     public const string CONTENT_TYPE_NAME = "Codesamples.ProductSKU";


     /// <summary>
     /// Represents system properties for a content item.
     /// </summary>
     [SystemField]
     public ContentItemFields SystemFields { get; set; }


     /// <summary>
     /// ProductFieldsName.
     /// </summary>
     public string ProductFieldsName { get; set; }


     /// <summary>
     /// ProductFieldsDescription.
     /// </summary>
     public string ProductFieldsDescription { get; set; }


     /// <summary>
     /// ProductFieldsPrice.
     /// </summary>
     public decimal ProductFieldsPrice { get; set; }


     /// <summary>
     /// ProductFieldCategory.
     /// </summary>
     public IEnumerable<TagReference> ProductFieldCategory { get; set; }
 }
```

The `[RegisterContentTypeMapping]` attribute automatically registers the class with the [content retriever](/documentation/developers-and-admins/api/content-item-api/content-retriever-api), enabling you to query products using the strongly-typed `ProductSKU` class.
## Retrieve products
Use the `IContentRetriever` service to query products from the content hub. The following examples demonstrate common retrieval patterns.
### Retrieve all products
C#
**Retrieve all products**
Copy
```
/// <summary>
/// Retrieves all products from the content hub.
/// </summary>
public async Task<List<ProductModel>> GetAllProductsAsync()
{
    var products = await contentRetriever.RetrieveContent<ProductSKU, ProductModel>(
        GetContentParameters(),
        additionalQueryConfiguration: null,
        new RetrievalCacheSettings(cacheItemNameSuffix: "AllProducts"),
        configureModel: async (container, productSKU) => await MapToProductModelAsync(productSKU)
    );

    return products.ToList();
}
```

The `RetrieveContent` method:
  * Accepts the content type (`ProductSKU`) and a target model type (`ProductModel`) as generic parameters
  * Uses `RetrieveContentParameters` to specify the language and preview mode
  * Supports caching through `RetrievalCacheSettings`
  * Transforms content items to view models using the `configureModel` delegate


### Retrieve a single product
C#
**Retrieve product by GUID**
Copy
```
/// <summary>
/// Retrieves a product by its content item GUID.
/// </summary>
public async Task<ProductModel?> GetProductByGuidAsync(Guid contentItemGuid)
{
    var products = await contentRetriever.RetrieveContentByGuids<ProductSKU, ProductModel>(
        [contentItemGuid],
        GetContentParameters(),
        additionalQueryConfiguration: null,
        new RetrievalCacheSettings(cacheItemNameSuffix: $"ByGuid|{contentItemGuid}"),
        configureModel: async (container, productSKU) => await MapToProductModelAsync(productSKU)
    );

    return products.FirstOrDefault();
}
```

Use `RetrieveContentByGuids` when you have the content item’s GUID, which is useful for product detail pages or when working with URL-based routing.
### Filter products by category
C#
**Retrieve products by category**
Copy
```
/// <summary>
/// Retrieves products by category using taxonomy tag GUID.
/// </summary>
public async Task<List<ProductModel>> GetProductsByCategoryAsync(Guid categoryTagGuid)
{
    var products = await contentRetriever.RetrieveContent<ProductSKU, ProductModel>(
        GetContentParameters(),
        query => query.Where(where => where
            .WhereContainsTags(
                nameof(ICodesamplesProductFields.ProductFieldCategory),
                [categoryTagGuid])),
        new RetrievalCacheSettings(cacheItemNameSuffix: $"ByCategory|{categoryTagGuid}"),
        configureModel: async (container, productSKU) => await MapToProductModelAsync(productSKU)
    );

    return products.ToList();
}
```

The `WhereContainsTags` method filters products that have a specific taxonomy tag assigned. Pass the field name and an array of tag GUIDs to match.
## Retrieve categories
Use the `ITaxonomyRetriever` service to retrieve category tags for navigation and filtering:
C#
**Retrieve product categories**
Copy
```
/// <summary>
/// Service for retrieving product categories from the ProductCategory taxonomy.
/// </summary>
public class CategoryService : CachedServiceBase
{
    public const string PRODUCT_CATEGORY_TAXONOMY = "Codesamples.ProductCategory";

    private readonly ITaxonomyRetriever taxonomyRetriever;
    private readonly ICacheDependencyBuilderFactory cacheDependencyBuilderFactory;

    public CategoryService(
        ITaxonomyRetriever taxonomyRetriever,
        IProgressiveCache progressiveCache,
        ICacheDependencyBuilderFactory cacheDependencyBuilderFactory)
        : base(progressiveCache)
    {
        this.taxonomyRetriever = taxonomyRetriever;
        this.cacheDependencyBuilderFactory = cacheDependencyBuilderFactory;
    }

    /// <summary>
    /// Retrieves all product categories from the ProductCategory taxonomy with caching.
    /// </summary>
    public async Task<List<CategoryModel>> GetCategoriesAsync()
    {
        return await LoadWithCacheAndDependency(async (cacheSettings) =>
        {
            // Loads taxonomy data
            var taxonomyData = await taxonomyRetriever.RetrieveTaxonomy(PRODUCT_CATEGORY_TAXONOMY, "en");

            var result = taxonomyData.Tags
                .Select(tag => new CategoryModel
                {
                    TagGuid = tag.Identifier,
                    Name = tag.Title
                })
                .OrderBy(n => n.Name)
                .ToList();

            // Set cache dependency - clear cache when the taxonomy changes
            var dependencyBuilder = cacheDependencyBuilderFactory.Create();
            cacheSettings.CacheDependency = dependencyBuilder
                .ForInfoObjects<TaxonomyInfo>()
                    .ByCodeName(PRODUCT_CATEGORY_TAXONOMY)
                    .Builder()
                .Build();

            return result;
        },
        cacheMinutes: CacheConstants.LongCacheDurationMinutes,
        cacheItemNameParts: ["commerce", "categories", PRODUCT_CATEGORY_TAXONOMY]);
    }
}
```

The taxonomy retriever returns all tags within the specified taxonomy, which you can use to build category menus or filter controls.
## Map products to view models
When displaying products, map the content item data to a view model that includes calculated fields such as discounted prices. The following example integrates with the [price calculation service](/documentation/developers-and-admins/digital-commerce-setup/price-calculation) to apply [catalog promotions](/documentation/developers-and-admins/digital-commerce-setup/promotions/catalog-discounts):
C#
**Map product to view model**
Copy
```
/// <summary>
/// Maps a ProductSKU content item to a ProductModel using price calculation service to determine discounts.
/// </summary>
private async Task<ProductModel> MapToProductModelAsync(ProductSKU productSKU)
{
    var basePrice = productSKU.ProductFieldsPrice;

    // Use price calculation service in Catalog mode to check for catalog promotions
    var calculationRequest = new CodesamplesPriceCalculationRequest
    {
        Items = [new CodeSamplesPriceCalculationRequestItem
        {
            ProductIdentifier = new ProductIdentifier { Identifier = productSKU.SystemFields.ContentItemID },
            Quantity = 1
        }],
        LanguageName = "en",
        Mode = PriceCalculationMode.Catalog
    };

    var calculationResult = await priceCalculationService.Calculate(calculationRequest);
    var resultItem = calculationResult.Items.FirstOrDefault();

    // Check if a catalog promotion was applied by looking at PromotionData
    var appliedPromotion = resultItem?.PromotionData?.CatalogPromotionCandidates
        ?.FirstOrDefault(c => c.Applied);
    bool isDiscounted = appliedPromotion != null;

    // Get the final price after any catalog discounts
    decimal finalPrice = resultItem?.LineSubtotalAfterLineDiscount ?? basePrice;

    return new ProductModel
    {
        ContentItemGuid = productSKU.SystemFields.ContentItemGUID,
        Id = productSKU.SystemFields.ContentItemID,
        Name = productSKU.ProductFieldsName,
        Description = productSKU.ProductFieldsDescription,
        Price = finalPrice,
        ListPrice = isDiscounted ? basePrice : null,
        Category = productSKU.ProductFieldCategory?.FirstOrDefault()?.Identifier.ToString() ?? string.Empty
    };
}
```

This approach:
  * Calculates the [final catalog price](/documentation/developers-and-admins/digital-commerce-setup/price-calculation/implementation#catalog-price-calculation) after applying any active [catalog discounts](/documentation/developers-and-admins/digital-commerce-setup/promotions/catalog-discounts).
  * Preserves the original list price for display (e.g., for showing strikethrough pricing).
  * Extracts category information from taxonomy tags.


For better performance in product listings, consider [caching](/documentation/developers-and-admins/development/caching/data-caching) the price calculation results or performing bulk calculations for multiple products at once.
![]()
[]()[]()
