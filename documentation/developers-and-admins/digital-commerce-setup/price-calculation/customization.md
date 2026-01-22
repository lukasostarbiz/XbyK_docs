---
source: https://docs.kentico.com/documentation/developers-and-admins/digital-commerce-setup/price-calculation/customization
scrape_date: 2026-01-22
---

  * [Home](/documentation)
  * [Developers and admins](/documentation/developers-and-admins)
  * [Digital commerce setup](/documentation/developers-and-admins/digital-commerce-setup)
  * [Price calculation](/documentation/developers-and-admins/digital-commerce-setup/price-calculation)
  * Price calculation customization 


# Price calculation customization
**Advanced license required**   
  
Features described on this page require the Xperience by Kentico **Advanced** license tier. 
The [price calculation service](/documentation/developers-and-admins/digital-commerce-setup/price-calculation) provides several customization options to fit your specific business requirements. You can extend the core data transfer objects, modify existing calculation steps, or add entirely new steps to the calculation pipeline.
## Extend data transfer objects
To customize a data transfer object, create a new class that inherits from the respective object, add your custom fields, and use the custom class in your calculation steps and services. When you extend these classes, you also need to update any related classes or services that utilize them to ensure compatibility with your custom fields.
The following objects are available for extension:
  * `PriceCalculationRequest`
  * `PriceCalculationResult`
  * `PriceCalculationRequestItem`
  * `PriceCalculationResultItem`
  * `ProductIdentifier`
  * `ProductData`


The following example demonstrates extending `ProductData` with custom fields and creating corresponding result types that enforce type safety through generics.
C#
**Define custom data transfer objects**
Copy
```
// Custom ProductData with additional fields
public record CodesamplesProductData : ProductData
{
    public TaxCategoryEnum TaxCategory { get; init; }
    public bool IsTaxExempt { get; init; }
    public IEnumerable<TagReference> Categories { get; init; } = [];
}

/// <summary>
/// Price calculation request item for code samples.
/// </summary>
public record CodeSamplesPriceCalculationRequestItem
    : PriceCalculationRequestItemBase<ProductIdentifier>;

/// <summary>
/// Price calculation request for code samples.
/// </summary>
public record CodesamplesPriceCalculationRequest
    : PriceCalculationRequestBase<CodeSamplesPriceCalculationRequestItem, AddressDto>;

/// <summary>
/// Price calculation result item for code samples.
/// </summary>
public sealed class CodesamplesPriceCalculationResultItem
    : PriceCalculationResultItemBase<ProductIdentifier, CodesamplesProductData>
{
}

/// <summary>
/// Price calculation result for code samples.
/// </summary>
public class CodesamplesPriceCalculationResult
    : PriceCalculationResultBase<CodesamplesPriceCalculationResultItem>;
```

These custom types can be used when [modifying calculation steps](#modify-existing-calculation-steps) or [implementing custom steps](#implement-custom-calculation-steps).
## Modify existing calculation steps
You can modify the behavior of existing [calculation steps](/documentation/developers-and-admins/digital-commerce-setup/price-calculation#calculation-steps) to implement custom pricing logic without creating entirely new steps. When you register a custom step implementation, it automatically replaces the default step implementation in the calculation pipeline.
The following calculation step interfaces can be overridden:
Interface |  Description  
---|---  
`IProductDataLoaderPriceCalculationStep` |  Loads product information and pricing data using the given [IProductDataRetriever implementation](/documentation/developers-and-admins/digital-commerce-setup/price-calculation/implementation#implement-product-data-retrieval).  
`ICatalogPromotionPriceCalculationStep` |  Evaluates and applies catalog promotions to individual products.  
`ILineSubtotalsPriceCalculationStep` |  Calculates line subtotals after catalog discounts.  
`IShippingPriceCalculationStep` |  Calculates shipping costs.  
`IOrderPromotionPriceCalculationStep` |  Evaluates and applies order-level promotions.  
`ITaxPriceCalculationStep` |  Calculates applicable taxes.  
`ILineTotalPriceCalculationStep` |  Calculates final line totals including taxes.  
`IOrderTotalPriceCalculationStep` |  Calculates the order total price.  
`IOrderGrandTotalPriceCalculationStep` |  Calculates the final grand total.  
### Override a calculation step
  1. Create a custom class that implements the interface for the calculation step you want to modify. Add the `Execute` method with your custom logic.
  2. Register your implementation in the [dependency injection](/documentation/developers-and-admins/development/website-development-basics/dependency-injection) container. When you register a custom step implementation, it automatically replaces the default step in the calculation pipeline:
C#
**Program.cs**
Copy
```
// Register with open generics to replace the default implementation
builder.Services.AddTransient(typeof(IModifiedCalculationStep<,>), typeof(ModifiedCalculationStep<,>));
```



For a complete example of overriding a calculation step, see [Implement tax calculation](/documentation/developers-and-admins/digital-commerce-setup/price-calculation/implementation#implement-tax-calculation), which demonstrates replacing the default tax step with custom tax logic.
## Implement custom calculation steps
Custom calculation steps allow you to add specialized pricing logic such as volume-based discounts, loyalty program benefits, handling fees, gift wrapping charges, or complex promotional rules. Custom steps can be inserted anywhere in the calculation pipeline by creating a custom steps provider.
### Create a custom step
  1. Add a custom class that implements `IPriceCalculationStep<TPriceCalculationRequest, TPriceCalculationResult>`. In the `Execute` method, define your custom logic to modify the calculation result based on the calculation request.
C#
**Custom calculation step implementation**
Copy
```
public sealed class CustomCalculationStep<TRequest, TResult> : IPriceCalculationStep<TRequest, TResult>
 where TRequest : PriceCalculationRequest
 where TResult : PriceCalculationResult
{
 public Task Execute(IPriceCalculationData<TRequest, TResult> calculationData, CancellationToken cancellationToken)
 {
     // Custom calculation logic
     return Task.CompletedTask;
 }
}
```

  2. Register your implementation in the [dependency injection](/documentation/developers-and-admins/development/website-development-basics/dependency-injection) container to make it available to the price calculation service.
C#
**Program.cs**
Copy
```
builder.Services.AddTransient(typeof(IPriceCalculationStep<,>), typeof(CustomCalculationStep<,>));
```

  3. Create a [custom calculation steps provider](#create-a-custom-steps-provider) to include your new step in the calculation pipeline.


## Create a custom steps provider
A custom steps provider allows you to control which steps execute and in what order. You can reorganize the default pipeline, add custom steps, or conditionally include steps based on request properties such as the [calculation mode](/documentation/developers-and-admins/digital-commerce-setup/price-calculation#calculation-modes).
When you register a custom implementation of `IPriceCalculationStepsProvider`, you need to ensure that all required calculation steps are included, including the default calculation steps. If any required step is omitted, the price calculation service may not function correctly, leading to incomplete or incorrect price calculations.
  1. Create a class that implements `IPriceCalculationStepsProvider<TPriceCalculationRequest, TPriceCalculationResult>`. Inject all default and custom calculation steps through the constructor.
C#
**Custom calculation steps provider**
Copy
```
public class CustomCalculationStepsProvider<TRequest, TResult> : IPriceCalculationStepsProvider<TRequest, TResult>
 where TRequest : PriceCalculationRequest
 where TResult : PriceCalculationResult
{
 private readonly IProductDataLoaderPriceCalculationStep<TRequest, TResult> productDataLoaderStep;
 private readonly ICatalogPromotionPriceCalculationStep<TRequest, TResult> catalogPromotionStep;
 private readonly ILineSubtotalsPriceCalculationStep<TRequest, TResult> lineSubtotalsStep;
 private readonly IShippingPriceCalculationStep<TRequest, TResult> shippingStep;
 private readonly IOrderPromotionPriceCalculationStep<TRequest, TResult> orderPromotionStep;
 private readonly IPriceCalculationStep<TRequest, TResult> customStep;
 private readonly ITaxPriceCalculationStep<TRequest, TResult> taxStep;
 private readonly ILineTotalPriceCalculationStep<TRequest, TResult> lineTotalStep;
 private readonly IOrderTotalPriceCalculationStep<TRequest, TResult> orderTotalStep;
 private readonly IOrderGrandTotalPriceCalculationStep<TRequest, TResult> orderGrandTotalStep;

 public CustomCalculationStepsProvider(
     IProductDataLoaderPriceCalculationStep<TRequest, TResult> productDataLoaderStep,
     ICatalogPromotionPriceCalculationStep<TRequest, TResult> catalogPromotionStep,
     ILineSubtotalsPriceCalculationStep<TRequest, TResult> lineSubtotalsStep,
     IShippingPriceCalculationStep<TRequest, TResult> shippingStep,
     IOrderPromotionPriceCalculationStep<TRequest, TResult> orderPromotionStep,
     IPriceCalculationStep<TRequest, TResult> customStep,
     ITaxPriceCalculationStep<TRequest, TResult> taxStep,
     ILineTotalPriceCalculationStep<TRequest, TResult> lineTotalStep,
     IOrderTotalPriceCalculationStep<TRequest, TResult> orderTotalStep,
     IOrderGrandTotalPriceCalculationStep<TRequest, TResult> orderGrandTotalStep)
 {
     this.productDataLoaderStep = productDataLoaderStep;
     this.catalogPromotionStep = catalogPromotionStep;
     this.lineSubtotalsStep = lineSubtotalsStep;
     this.shippingStep = shippingStep;
     this.orderPromotionStep = orderPromotionStep;
     // Injects the custom step
     this.customStep = customStep;
     this.taxStep = taxStep;
     this.lineTotalStep = lineTotalStep;
     this.orderTotalStep = orderTotalStep;
     this.orderGrandTotalStep = orderGrandTotalStep;
 }
}
```

  2. Implement the `Get` method to return the calculation steps in the desired order. The method receives the calculation request, enabling conditional step selection based on properties like the calculation mode.
C#
**Get method implementation**
Copy
```
// The Get method receives the request to enable conditional step selection based on calculation mode
public IEnumerable<IPriceCalculationStep<TRequest, TResult>> Get(
    TRequest request)
{
    // Runs for all modes (Catalog, ShoppingCart, Checkout)
    yield return productDataLoaderStep;
    yield return catalogPromotionStep;
    yield return lineSubtotalsStep;

    // Determines steps for ShoppingCart and Checkout modes only
    if (request.Mode == PriceCalculationMode.ShoppingCart
        || request.Mode == PriceCalculationMode.Checkout)
    {
        // Adds shipping only in Checkout mode
        if (request.Mode == PriceCalculationMode.Checkout)
        {
            yield return shippingStep;
        }

        yield return orderPromotionStep;
        // Inserts custom step after order promotions
        yield return customStep;
        yield return taxStep;
        yield return lineTotalStep;
        yield return orderTotalStep;
        yield return orderGrandTotalStep;
    }
}
```

  3. Register the custom steps provider to replace the default calculation pipeline. When you register your custom steps provider, the calculation service automatically detects and uses it, executing your custom step during calculations.
C#
**Program.cs**
Copy
```
builder.Services.AddTransient(typeof(IPriceCalculationStepsProvider<,>), typeof(CustomCalculationStepsProvider<,>));
```



**Custom calculation modes**
`PriceCalculationMode` can be extended with custom values. You can create custom modes by instantiating new `PriceCalculationMode` objects with custom names. Handle custom modes in your custom steps provider’s `Get` method to control which calculation steps execute for each mode.
![]()
[]()[]()
