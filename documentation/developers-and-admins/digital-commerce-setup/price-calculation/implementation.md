# Price calculation implementation
  * [ Copy page link ](documentation/developers-and-admins/digital-commerce-setup/price-calculation/implementation#) | [Get HelpService ID](documentation/developers-and-admins/digital-commerce-setup/price-calculation/implementation#)
Core MVC 5


[✖](documentation/developers-and-admins/digital-commerce-setup/price-calculation/implementation# "Close page link panel") [Copy to clipboard](documentation/developers-and-admins/digital-commerce-setup/price-calculation/implementation#)
**Advanced license required**   
  
Features described on this page require the Xperience by Kentico **Advanced** license tier. 
This page covers the practical implementation aspects of the [price calculation service](documentation/developers-and-admins/digital-commerce-setup/price-calculation), including how to retrieve product data for pricing and how to use the service across different commerce scenarios.
## Implement product data retrieval
The price calculation service requires a custom implementation of `IProductDataRetriever<TProductIdentifier, TProductData>` to provide product information for pricing calculations. The product data retriever is data store agnostic – you can retrieve product data from any source, including Xperience’s [content hub](documentation/developers-and-admins/digital-commerce-setup/model-product-catalog), external databases, third-party APIs, or custom data stores, as long as you return the data in the expected format.
The `IProductDataRetriever` interface defines a single method that you must implement:
C#
**IProductDataRetriever interface**
Copy
```
public interface IProductDataRetriever<TProductIdentifier, TProductData>
{
    Task<IDictionary<TProductIdentifier, TProductData>> Get(
        IEnumerable<TProductIdentifier> productIdentifiers,
        string languageName,
        CancellationToken cancellationToken = default
    );
}
```

The `Get` method accepts the following parameters:
  * `productIdentifiers` – collection of product identifiers from the calculation request items. The method receives all identifiers at once, allowing efficient bulk retrieval.
  * `languageName` – the language for localized product data (from `PriceCalculationRequest.LanguageName`). Use this to retrieve language-specific product information such as names or region-specific pricing.
  * `cancellationToken` – pass this token to async operations to support request cancellation.


The method must return a dictionary mapping each product identifier to its corresponding product data. The dictionary keys must match the identifiers from the input parameter. If a product cannot be found, you can either omit it from the dictionary or include it with null values, depending on your error handling strategy.
### ProductData and ProductIdentifier objects
**ProductIdentifier**
A record that identifies a product in your catalog. The default `ProductIdentifier` contains a single `ProductId` property:
C#
**Default ProductIdentifier**
Copy
```
public record ProductIdentifier
{
    public int ProductId { get; init; }
}
```

If you need additional identification fields (such as variant identifiers, SKU codes, or multi-part keys), [extend the ProductIdentifier record](documentation/developers-and-admins/digital-commerce-setup/price-calculation/customization#extend-data-transfer-objects):
C#
**Extended ProductIdentifier for variants**
Copy
```
public record ProductWithVariantIdentifier : ProductIdentifier
{
    public int? VariantId { get; init; }
}
```

**ProductData**
A record containing the product information needed for price calculations. The default `ProductData` requires a `UnitPrice` property (used by the unit price calculation step):
C#
**Default ProductData**
Copy
```
public record ProductData
{
    public decimal UnitPrice { get; init; }
}
```

You can [extend ProductData](documentation/developers-and-admins/digital-commerce-setup/price-calculation/customization#extend-data-transfer-objects) to include additional fields required by your pricing logic, such as product weight for shipping calculations, tax categories, or discount eligibility:
C#
**Extended ProductData with custom fields**
Copy
```
public record CustomProductData : ProductData
{
    public decimal Weight { get; init; }
    public string TaxCategory { get; init; }
    public string ProductCategory { get; init; }
}
```

### Create a product data retriever
Follow these steps to implement the product data retriever:
  1. Decide whether to use the default `ProductIdentifier` and `ProductData` types or [extend them](documentation/developers-and-admins/digital-commerce-setup/price-calculation/customization#extend-data-transfer-objects) with additional fields required by your pricing logic.
  2. Implement the `IProductDataRetriever<TProductIdentifier, TProductData>` interface using your chosen types:
C#
**Product data retriever class**
Copy
```
public class ProductDataRetriever : IProductDataRetriever<ProductIdentifier, ProductData>
{
    public async Task<IDictionary<ProductIdentifier, ProductData>> Get(
        IEnumerable<ProductIdentifier> productIdentifiers,
        string languageName,
        CancellationToken cancellationToken)
    {
        // Implementation goes here
    }
}
```

  3. Use the [content retriever API](documentation/developers-and-admins/api/content-item-api) to query Xperience’s [content hub](documentation/developers-and-admins/digital-commerce-setup/model-product-catalog), or retrieve data from your external data source:
C#
**Bulk retrieval example using content retriever API**
Copy
```
var productIds = productIdentifiers.Select(p => p.ProductId).ToList();

var products = await contentRetriever.RetrieveContent<ProductItem>(
    RetrieveContentParameters.Default,
    queryParameters => queryParameters
        .WhereIn(nameof(IContentItemFieldsSource.SystemFields.ContentItemID), productIds),
    new RetrievalCacheSettings(cacheItemNameSuffix: $"{nameof(ContentTypesQueryParameters.WhereIn)}|ByContentItemIDs"),
    cancellationToken: cancellationToken);
```

  4. Transform the retrieved data into `ProductData` objects with the required `UnitPrice` property and any custom fields:
C#
**Mapping to ProductData**
Copy
```
return products.ToDictionary(
    p => new ProductIdentifier { ProductId = p.SystemFields.ContentItemID },
    p => new ProductData { UnitPrice = p.GetValue<decimal>("ProductPrice") }
);
```

  5. Register your implementation in the [dependency injection](documentation/developers-and-admins/development/website-development-basics/dependency-injection) container:
C#
**Program.cs**
Copy
```
builder.Services.AddTransient(typeof(IProductDataRetriever<,>), typeof(ProductDataRetriever<,>));
```



### Pipeline integration
The `ProductDataLoaderCalculationStep` automatically calls your retriever implementation during the calculation process:
  1. The step extracts product identifiers from all `PriceCalculationRequestItem` objects in the request
  2. It calls your retriever’s `Get` method with these identifiers and the request’s language name
  3. The retrieved `ProductData` is attached to each corresponding `PriceCalculationResultItem`
  4. Subsequent calculation steps (unit price, tax, etc.) can access this product data


This automatic integration means you only need to implement the retriever – the price calculation service handles calling it at the appropriate time.
You now have a functioning product data retriever that loads pricing information from your catalog. The price calculation service retrieves product data during the calculation pipeline.
## Implement tax calculation
The default `ITaxPriceCalculationStep` implementation does not calculate any taxes. You must provide a custom implementation for prices to include taxes in `ShoppingCart` and `Checkout` modes.
### Create a tax calculation step
  1. Create a class that implements `ITaxPriceCalculationStep<TRequest, TResult>`. Add the `Execute` method with your tax calculation logic.
C#
**Custom tax calculation step**
Copy
```
public sealed class CustomTaxCalculationStep<TRequest, TResult> : ITaxPriceCalculationStep<TRequest, TResult>
    where TRequest : PriceCalculationRequest
    where TResult : PriceCalculationResult
{
    public Task Execute(
        IPriceCalculationData<TRequest, TResult> calculationData,
        CancellationToken cancellationToken)
    {
        calculationData.Result.TotalTax = 0;

        foreach (var resultItem in calculationData.Result.Items)
        {
            // Apply your tax rate (example: 7.5%)
            decimal taxRate = 0.075m;

            resultItem.LineTaxAmount = resultItem.LineSubtotalAfterAllDiscounts * taxRate;
            calculationData.Result.TotalTax += resultItem.LineTaxAmount;
        }

        return Task.CompletedTask;
    }
}
```

  2. Register your implementation in the [dependency injection](documentation/developers-and-admins/development/website-development-basics/dependency-injection) container. This automatically replaces the default (no-op) tax step:
C#
**Program.cs**
Copy
```
builder.Services.AddTransient(typeof(ITaxPriceCalculationStep<,>), typeof(CustomTaxCalculationStep<,>));
```



### Use custom product data for tax categories
For more sophisticated tax calculation (such as different rates by product category or tax-exempt products), [extend the ProductData object](documentation/developers-and-admins/digital-commerce-setup/price-calculation/customization#extend-data-transfer-objects) with tax-related fields:
C#
**Extended ProductData with tax fields**
Copy
```
public record CustomProductData : ProductData
{
    public string TaxCategory { get; init; }
    public bool IsTaxExempt { get; init; }
}
```

Then access these fields in your tax calculation step to apply the appropriate rates.
### Configure tax-inclusive pricing
The `TaxOptions` class controls whether product prices in your catalog already include tax or whether tax should be added on top. This setting affects how the calculation steps compute line totals and order totals.
C#
**Program.cs**
Copy
```
builder.Services.Configure<TaxOptions>(options =>
{
    // Set to true if your catalog prices already include tax
    // Set to false if tax should be added to the price
    options.PricesIncludeTax = false;
});
```

When `PricesIncludeTax` is `false` (default), the service adds `LineTaxAmount` to the subtotal to calculate `LineTotalPrice`. When `true`, the service assumes tax is already included in the price and your tax calculation step should extract the tax amount from the existing price rather than adding it.
Your custom tax calculation step should check `TaxOptions.PricesIncludeTax` to determine whether to extract tax from prices (tax-inclusive) or calculate tax on top of prices (tax-exclusive).
You now have a functioning tax calculation step. The price calculation service applies your tax logic during `ShoppingCart` and `Checkout` mode calculations, populating the `LineTaxAmount` and `TotalTax` properties in the result.
**Tips**
Use `IPriceCalculationRoundingService` to round calculated tax amounts consistently. The default implementation rounds to 2 decimal places and away from zero at the [midpoint](https://learn.microsoft.com/en-us/dotnet/api/system.midpointrounding).
For a complete implementation example with rounding and category-based tax rates, examine the _DancingGoatTaxPriceCalculationStep.cs_ file in the [Dancing Goat sample site](documentation/developers-and-admins/installation#available-project-templates).
## Calculate prices
The following sections demonstrate how to use the price calculation service in different commerce scenarios. Each scenario uses a different [calculation mode](documentation/developers-and-admins/digital-commerce-setup/price-calculation#calculation-modes) optimized for that stage of the customer journey.
### Catalog price calculation
Use `PriceCalculationMode.Catalog` to calculate product prices for display in product listings, category pages, and product detail pages. This mode runs only the essential steps: product data loading, catalog promotions, and line subtotals. It does not calculate taxes, order promotions, shipping, or order totals.
C#
**Calculate catalog prices**
Copy
```
// An instance of the price calculation service, obtained via dependency injection
private readonly IPriceCalculationService<PriceCalculationRequest, PriceCalculationResult> priceCalculationService;

// Creates a minimal calculation request for catalog display
var calculationRequest = new PriceCalculationRequest
{
    Items = [new PriceCalculationRequestItem
    {
        ProductIdentifier = new ProductIdentifier { ProductId = productId },
        Quantity = 1,
    }],
    LanguageName = "en",
    // Use Catalog mode for product listing/detail pages
    Mode = PriceCalculationMode.Catalog
};

// Calculates the prices based on the request
PriceCalculationResult calculationResult = await priceCalculationService.Calculate(calculationRequest, cancellationToken);

// Access pricing for the product
PriceCalculationResultItem item = calculationResult.Items.First();
// Original unit price before any discounts
decimal unitPrice = item.UnitPrice;
// Price after catalog promotion (if any was applied)
decimal discountedPrice = item.LineSubtotalAfterLineDiscount;
// Name of the applied catalog promotion (null if none)
string promotionName = item.CatalogPromotionName;
 



 


```

`PriceCalculationMode.Catalog` is the default mode. If you don’t explicitly set the `Mode` property, the service uses Catalog mode automatically.
### Shopping cart price calculation
Use `PriceCalculationMode.ShoppingCart` when calculating prices for the shopping cart view. This mode includes catalog promotions, order promotions, taxes, and line/order totals, but excludes shipping calculation. Use this mode when displaying cart totals before the customer selects a shipping method.
C#
**Calculate shopping cart prices**
Copy
```
// An instance of the price calculation service, obtained via dependency injection
private readonly IPriceCalculationService<PriceCalculationRequest, PriceCalculationResult> priceCalculationService;

// Shopping cart content retrieved from the current shopping cart
ShoppingCartDataModel shoppingCartData;
// The customer ID of the current user
int customerId;

// Creates the calculation request
var calculationRequest = new PriceCalculationRequest
{
    Items = [.. shoppingCartData.Items.Select(item => new PriceCalculationRequestItem
    {
        ProductIdentifier = item.ProductIdentifier,
        Quantity = item.Quantity,
    })],
    // Use ShoppingCart mode - includes promotions and taxes but excludes shipping
    Mode = PriceCalculationMode.ShoppingCart,
    CustomerId = customerId,
    LanguageName = "en"
};

// Calculates the prices based on the request
PriceCalculationResult calculationResult = await priceCalculationService.Calculate(calculationRequest, cancellationToken);

// Cart subtotal after all discounts
decimal subtotal = calculationResult.TotalPrice;
// Total tax amount
decimal tax = calculationResult.TotalTax;
// Note: ShippingPrice is not calculated in ShoppingCart mode
 



 


```

This example uses `ShoppingCartData`, `ShoppingCartDataItem`, and `ShoppingCartDataModel` classes from the [Dancing Goat sample site](documentation/developers-and-admins/installation#available-project-templates) to demonstrate a typical implementation.
### Checkout price calculation
Use `PriceCalculationMode.Checkout` for the final price calculation during checkout. This mode runs all calculation steps including shipping. Use this mode when the customer has selected a shipping method and is ready to complete the purchase.
C#
**Calculate checkout prices**
Copy
```
// An instance of the price calculation service, obtained via dependency injection
private readonly IPriceCalculationService<PriceCalculationRequest, PriceCalculationResult> priceCalculationService;

// Creates the calculation request with full checkout data
var calculationRequest = new PriceCalculationRequest
{
    Items = [.. shoppingCartData.Items.Select(item => new PriceCalculationRequestItem
    {
        ProductIdentifier = item.ProductIdentifier,
        Quantity = item.Quantity,
    })],
    ShippingMethodId = shippingMethodId,
    PaymentMethodId = paymentMethodId,
    BillingAddress = customerAddress,
    CustomerId = customerId,
    LanguageName = "en",
    // Use Checkout mode for final order calculation with shipping
    Mode = PriceCalculationMode.Checkout
};

// Calculates the prices based on the request
PriceCalculationResult calculationResult = await priceCalculationService.Calculate(calculationRequest, cancellationToken);

// Total price of all items after applying all discounts
decimal totalPrice = calculationResult.TotalPrice;
// Total tax amount
decimal totalTax = calculationResult.TotalTax;
// The price for shipping
decimal shippingPrice = calculationResult.ShippingPrice;
// Grand total to be paid by the customer
decimal grandTotal = calculationResult.GrandTotal;
// Items with detailed pricing information
ICollection<PriceCalculationResultItem> items = calculationResult.Items;
 



 


```

  * Always perform a final price calculation immediately before creating the order to ensure prices are current, even if you displayed preliminary calculations earlier in the checkout flow.
  * Implement proper error handling for scenarios where products are unavailable, shipping methods are invalid, or calculation steps fail.


### Prepare calculation requests
How you create and populate `PriceCalculationRequest` objects depends entirely on your implementation. The price calculation service does not prescribe a specific approach – you control how data flows from your catalog, shopping cart, checkout forms, and user sessions into the calculation request. This section highlights common implementation patterns you can use.
**Create a dedicated service layer**  
Create a dedicated service (such as `CalculationService` in the Dancing Goat sample) that handles the complexity of gathering data from multiple sources and constructing the calculation request. This approach centralizes request creation logic and makes it easier to maintain and test.
**Dynamic data gathering**  
Retrieve customer information, shipping selections, and cart contents from various sources at the time of calculation:
  * Shopping cart data from session storage or a database. Use the `ICurrentShoppingCartRetriever` API to retrieve the appropriate shopping cart for customers based on the current request.
  * Customer ID from the authenticated user’s identity.
  * Shipping and payment method selections from checkout form state.
  * Billing/shipping addresses from user profiles or form submissions.
  * Language and currency preferences from the current request context.


**Incremental calculations**  
Call the price calculation service at different points in your commerce flow using the appropriate calculation mode:
  * Product catalog – Use `PriceCalculationMode.Catalog` for product pricing with catalog promotions applied
  * Shopping cart view – Use `PriceCalculationMode.ShoppingCart` for cart totals with order promotions and taxes, but without shipping
  * Final checkout – Use `PriceCalculationMode.Checkout` for complete calculation including shipping


The Dancing Goat sample site demonstrates this approach in its `CalculationService` class, which provides separate methods for calculations with and without shipping information. The service gathers customer data from ASP.NET Core Identity, retrieves customer records from the database, and transforms address view models into `PriceCalculationRequestAddress` objects.
For a complete working example that shows how to integrate request creation with user authentication, form data, and session state, examine the _CalculationService.cs_ file in the [Dancing Goat sample site](documentation/developers-and-admins/installation#available-project-templates).