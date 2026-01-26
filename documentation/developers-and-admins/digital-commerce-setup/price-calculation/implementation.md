---
source: https://docs.kentico.com/documentation/developers-and-admins/digital-commerce-setup/price-calculation/implementation
scrape_date: 2026-01-26
---

  * [Home](/documentation)
  * [Developers and admins](/documentation/developers-and-admins)
  * [Digital commerce setup](/documentation/developers-and-admins/digital-commerce-setup)
  * [Price calculation](/documentation/developers-and-admins/digital-commerce-setup/price-calculation)
  * Set up price calculation 


# Set up price calculation
**Advanced license required**   
  
Features described on this page require the Xperience by Kentico **Advanced** license tier. 
This page covers the practical implementation aspects of the [price calculation service](/documentation/developers-and-admins/digital-commerce-setup/price-calculation), including how to retrieve product data for pricing and how to use the service across different commerce scenarios.
## Implement product data retrieval
The price calculation service requires a custom implementation of `IProductDataRetriever<TProductIdentifier, TProductData>` to provide product information for pricing calculations. The product data retriever is data store agnostic – you can retrieve product data from any source, including Xperience’s [content hub](/documentation/developers-and-admins/digital-commerce-setup/model-product-catalog), external databases, third-party APIs, or custom data stores, as long as you return the data in the expected format.
The `IProductDataRetriever` interface defines a single method that you must implement:
C#
**IProductDataRetriever interface method**
Copy
```
public async Task<IReadOnlyDictionary<TProductIdentifier, TProductData>> Get(
    IEnumerable<TProductIdentifier> productIdentifiers,
    string languageName,
    CancellationToken cancellationToken = default)
```

The `Get` method accepts the following parameters:
  * `productIdentifiers` – collection of product identifiers from the calculation request items.
  * `languageName` – the language for localized product data (from `PriceCalculationRequest.LanguageName`). Use this to retrieve language-specific product information such as names or region-specific pricing.


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

If you need additional identification fields (such as variant identifiers, SKU codes, or multi-part keys), [extend the ProductIdentifier record](/documentation/developers-and-admins/digital-commerce-setup/price-calculation/customization#extend-data-transfer-objects).
**ProductData**
A record containing the product information needed for price calculations. The default `ProductData` contains a `UnitPrice` property (required by the default calculation logic):
C#
**Default ProductData**
Copy
```
public record ProductData
{
    public decimal UnitPrice { get; init; }
}
```

You can [extend ProductData](/documentation/developers-and-admins/digital-commerce-setup/price-calculation/customization#extend-data-transfer-objects) to include additional fields required by your pricing logic, such as product weight for shipping calculations, tax categories, or discount eligibility.
### Create a product data retriever
Follow these steps to implement the product data retriever:
  1. Decide whether to use the default `ProductIdentifier` and `ProductData` types or [extend them](/documentation/developers-and-admins/digital-commerce-setup/price-calculation/customization#extend-data-transfer-objects) with additional fields required by your pricing logic.
  2. Implement the `IProductDataRetriever<TProductIdentifier, TProductData>` interface using your chosen types:
C#
**Product data retriever class**
Copy
```
/// <summary>
/// Retrieves product data from the Content Hub for price calculation.
/// Implements the required IProductDataRetriever interface for the price calculation service.
/// </summary>
public class ProductDataRetriever<TProductIdentifier, TProductData> : IProductDataRetriever<TProductIdentifier, TProductData>
 where TProductIdentifier : ProductIdentifier
 where TProductData : CodesamplesProductData
{
 private readonly IContentRetriever contentRetriever;

 public ProductDataRetriever(IContentRetriever contentRetriever)
 {
     this.contentRetriever = contentRetriever;
 }

 /// <summary>
 /// Retrieves product data for the specified product identifiers.
 /// </summary>
 public async Task<IReadOnlyDictionary<TProductIdentifier, TProductData>> Get(
     IEnumerable<TProductIdentifier> productIdentifiers,
     string languageName,
     CancellationToken cancellationToken = default)
 {
     // Product retrieval implementation is catalog data source-dependent...
 }
}
```

  3. Use the [content retriever API](/documentation/developers-and-admins/api/content-item-api) to query Xperience’s [content hub](/documentation/developers-and-admins/digital-commerce-setup/model-product-catalog), or retrieve data from your external data source, and transform the results into `ProductData` objects:
C#
**Mapping to ProductData**
Copy
```
return products.ToDictionary(
    productSKU => new ProductIdentifier {
            Identifier = productSKU.SystemFields.ContentItemID
        },
    productSKU => new ProductData {
                UnitPrice = productSKU.ProductFieldsPrice,
        }
);
```

  4. Register your implementation in the [dependency injection](/documentation/developers-and-admins/development/website-development-basics/dependency-injection) container:
C#
**Program.cs**
Copy
```
builder.Services.AddTransient(
    typeof(IProductDataRetriever<,>),
    typeof(ProductDataRetriever<,>)
);
```

You must register the implementation using **open generic types** (`<,>` without specific type arguments) to enable Xperience to recognize and correctly resolve the type. This requires your `ProductDataRetriever` class to be generic with `where` constraints. See the implementation example in this section. 


### Pipeline integration
The `ProductDataLoaderCalculationStep` automatically calls your retriever implementation during the calculation process:
  1. The step extracts product identifiers from all `PriceCalculationRequestItem` objects in the request.
  2. It calls your retriever’s `Get` method with these identifiers and the request’s language name.
  3. The retrieved `ProductData` is attached to each corresponding `PriceCalculationResultItem`.
  4. Subsequent calculation steps (such as unit price, tax, or custom) can access this product data when evaluating [promotions](/documentation/developers-and-admins/digital-commerce-setup/promotions), for example.


This means you only need to implement the retriever – the price calculation service handles calling it at the appropriate time.
You now have a functioning product data retriever that loads pricing information from your catalog. The price calculation service retrieves product data during the calculation process.
## Implement tax calculation
The default `ITaxPriceCalculationStep` implementation does not calculate any taxes. You must provide a custom implementation for prices to include taxes in `ShoppingCart` and `Checkout` [calculation modes](#calculate-prices).
### Create a tax calculation step
  1. Create a class that implements `ITaxPriceCalculationStep<TRequest, TResult>`. Add the `Execute` method with your tax calculation logic.
C#
**Custom tax calculation step**
Copy
```
public class CustomTaxCalculationStep<TRequest, TResult> :
 ITaxPriceCalculationStep<TRequest, TResult>
     where TRequest : PriceCalculationRequest
     where TResult : PriceCalculationResult
{
 private readonly TaxOptions taxOptions;
 private readonly IPriceCalculationRoundingService roundingService;

 public CustomTaxCalculationStep(
     IOptions<TaxOptions> taxOptions,
     IPriceCalculationRoundingService roundingService)
 {
     this.taxOptions = taxOptions.Value;
     this.roundingService = roundingService;
 }

 public Task Execute(
     IPriceCalculationData<TRequest, TResult> calculationData,
     CancellationToken cancellationToken)
 {
     calculationData.Result.TotalTax = 0;

     foreach (var resultItem in calculationData.Result.Items)
     {
         // Apply your tax rate (example: 7.5%)
         decimal taxRate = 0.075m;
         decimal calculatedTax;

         if (taxOptions.PricesIncludeTax)
         {
             // Tax-inclusive: Extract tax from the price
             // Tax = Price - (Price / (1 + TaxRate))
             decimal priceWithTax = resultItem.LineSubtotalAfterAllDiscounts;
             calculatedTax = priceWithTax - (priceWithTax / (1 + taxRate));
         }
         else
         {
             // Tax-exclusive: Calculate tax on top of the price
             calculatedTax = resultItem.LineSubtotalAfterAllDiscounts * taxRate;
         }

         // Round the tax amount to 2 decimal places
         resultItem.LineTaxAmount = roundingService.Round(
             calculatedTax,
             new PriceCalculationRoundingContext());
         calculationData.Result.TotalTax += resultItem.LineTaxAmount;
     }

     return Task.CompletedTask;
 }
}
```

  2. Register your implementation in the [dependency injection](/documentation/developers-and-admins/development/website-development-basics/dependency-injection) container. This automatically replaces the default (no-op) tax step:
C#
**Program.cs**
Copy
```
builder.Services.AddTransient(
    typeof(ITaxPriceCalculationStep<,>),
    typeof(CustomTaxCalculationStep<,>)
);
```

You must register the implementation using **open generic types** (`<,>` without specific type arguments) to enable Xperience to recognize and correctly resolve the type. This requires your `TaxCalculationStep` class to be generic with `where` constraints. 


### Use custom product data for tax categories
For more sophisticated tax calculation (such as different rates by product category or tax-exempt products), [extend the ProductData object](/documentation/developers-and-admins/digital-commerce-setup/price-calculation/customization#extend-data-transfer-objects) with tax-related fields:
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
For a complete implementation example with rounding and category-based tax rates, examine the _DancingGoatTaxPriceCalculationStep.cs_ file in the [Dancing Goat sample site](/documentation/developers-and-admins/installation#available-project-templates).
## Calculate prices
The following sections demonstrate how to use the price calculation service in different commerce scenarios. Each scenario uses a different [calculation mode](/documentation/developers-and-admins/digital-commerce-setup/price-calculation#calculation-modes) optimized for that stage of the customer journey.
### Catalog price calculation
Use `PriceCalculationMode.Catalog` to calculate product prices for display in product listings, category pages, and product detail pages. This mode runs only the [essential steps](/documentation/developers-and-admins/digital-commerce-setup/price-calculation#calculation-steps): product data loading, catalog promotions, and line subtotals. It does not calculate taxes, order promotions, shipping, or order totals.
C#
**Calculate catalog prices**
Copy
```
// An instance of the price calculation service, obtained via dependency injection
private readonly IPriceCalculationService<PriceCalculationRequest, PriceCalculationResult> priceCalculationService;

public PriceCalculationExamples(
    IPriceCalculationService<PriceCalculationRequest, PriceCalculationResult> priceCalculationService)
{
    this.priceCalculationService = priceCalculationService;
}

public async Task<PriceCalculationResult> CalculateCatalogPrice(
    int productId,
    CancellationToken cancellationToken)
{
    // Creates a minimal calculation request for catalog display
    var calculationRequest = new PriceCalculationRequest
    {
        Items = [new PriceCalculationRequestItem
        {
            ProductIdentifier = new ProductIdentifier { Identifier = productId },
            Quantity = 1,
        }],
        LanguageName = "en",
        // Use Catalog mode for product listing/detail pages
        Mode = PriceCalculationMode.Catalog
    };

    // Calculates the prices based on the request
    PriceCalculationResult calculationResult =
        await priceCalculationService.Calculate(calculationRequest, cancellationToken);

    // Access pricing for the product
    var item = calculationResult.Items.First();
    // Original unit price before any discounts
    decimal unitPrice = item.ProductData.UnitPrice;
    // Price after catalog promotion (if any was applied)
    decimal discountedPrice = item.LineSubtotalAfterLineDiscount;

    return calculationResult;
}
 



 


```

`PriceCalculationMode.Catalog` is the default mode. If you don’t explicitly set the `Mode` property, the service calculates catalog prices automatically.
### Shopping cart price calculation
Use `PriceCalculationMode.ShoppingCart` when calculating prices for the shopping cart view. This mode includes catalog promotions, order promotions, taxes, and line/order totals, but excludes shipping calculation. Use this mode when displaying cart totals before the customer selects a shipping method.
C#
**Calculate shopping cart prices**
Copy
```
public async Task<PriceCalculationResult> CalculateShoppingCartPrice(
    PriceCalculationRequestItem[] cartItems,
    int customerId,
    CancellationToken cancellationToken)
{
    // Creates the calculation request
    var calculationRequest = new PriceCalculationRequest
    {
        Items = cartItems,
        // Use ShoppingCart mode - includes promotions and taxes but excludes shipping
        Mode = PriceCalculationMode.ShoppingCart,
        CustomerId = customerId,
        LanguageName = "en"
    };

    // Calculates the prices based on the request
    PriceCalculationResult calculationResult =
        await priceCalculationService.Calculate(calculationRequest, cancellationToken);

    // Cart subtotal after all discounts
    decimal subtotal = calculationResult.TotalPrice;
    // Total tax amount
    decimal tax = calculationResult.TotalTax;

    return calculationResult;
}
 



 


```

In a real implementation, you would retrieve cart items from your [shopping cart storage](/documentation/developers-and-admins/digital-commerce-setup/checkout-process#manage-shopping-cart-data) and transform them into `PriceCalculationRequestItem` objects. See the **CalculationService.cs** file in the [Dancing Goat sample site](/documentation/developers-and-admins/installation#available-project-templates) for a complete implementation example.
### Pass coupon codes
If your promotions use [coupon codes](/documentation/developers-and-admins/digital-commerce-setup/promotions/coupon-codes), include any customer-entered codes in the calculation request via the `CouponCodes` property:
C#
**Include coupon codes in price calculation**
Copy
```
/// <summary>
/// Calculates prices for a shopping cart with coupon codes applied.
/// </summary>
public async Task<PriceCalculationResult> CalculateWithCoupons(
    ShoppingCartDataModel cart,
    int customerId,
    CancellationToken cancellationToken)
{
    // Build the price calculation request
    PriceCalculationRequest calculationRequest = new PriceCalculationRequest
    {
        Items = cart.Items.Select(item => new PriceCalculationRequestItem
        {
            ProductIdentifier = item.ProductIdentifier,
            Quantity = item.Quantity
        }).ToList(),
        CustomerId = customerId,
        LanguageName = "en",
        Mode = PriceCalculationMode.ShoppingCart,
        // Pass any coupon codes entered by the customer
        CouponCodes = cart.CouponCodes
    };

    // Calculate prices - promotions requiring matching coupon codes are applied
    PriceCalculationResult result =
        await priceCalculationService.Calculate(calculationRequest, cancellationToken);

    return result;
}
 
 


```

Coupon codes are **case-insensitive**. The price calculation pipeline checks each promotion’s coupon requirements and only applies promotions configured for automatic redemption, or promotions where the customer has provided a matching code.
### Checkout price calculation
Use `PriceCalculationMode.Checkout` for the final price calculation during checkout. This mode runs all calculation steps including shipping and taxes. Use this mode when the customer has selected a shipping method and is ready to complete the purchase.
C#
**Calculate checkout prices**
Copy
```
public async Task<PriceCalculationResult> CalculateCheckoutPrice(
    PriceCalculationRequestItem[] cartItems,
    int shippingMethodId,
    int paymentMethodId,
    AddressDto billingAddress,
    int customerId,
    CancellationToken cancellationToken)
{
    // Creates the calculation request with full checkout data
    var calculationRequest = new PriceCalculationRequest
    {
        Items = cartItems,
        ShippingMethodId = shippingMethodId,
        PaymentMethodId = paymentMethodId,
        BillingAddress = billingAddress,
        CustomerId = customerId,
        LanguageName = "en",
        // Use Checkout mode for final order calculation with shipping
        Mode = PriceCalculationMode.Checkout
    };

    // Calculates the prices based on the request
    PriceCalculationResult calculationResult =
        await priceCalculationService.Calculate(calculationRequest, cancellationToken);

    // Total price of all items after applying all discounts
    decimal totalPrice = calculationResult.TotalPrice;
    // Total tax amount
    decimal totalTax = calculationResult.TotalTax;
    // The price for shipping
    decimal shippingPrice = calculationResult.ShippingPrice;
    // Grand total to be paid by the customer
    decimal grandTotal = calculationResult.GrandTotal;

    return calculationResult;
}
 



 


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
  * Product catalog – Use `PriceCalculationMode.Catalog` for product pricing with catalog promotions applied.
  * Shopping cart view – Use `PriceCalculationMode.ShoppingCart` for cart totals with order promotions and taxes, but without shipping.
  * Final checkout – Use `PriceCalculationMode.Checkout` for complete calculation including shipping.


The _Dancing Goat_ [sample site](/documentation/developers-and-admins/installation#available-project-templates) demonstrates this approach in its `CalculationService` class, which provides separate methods for calculations with and without shipping information. The service gathers customer data from the currently authenticated [member](/documentation/business-users/members) and retrieves customer records from the database.
![]()
[]()[]()
