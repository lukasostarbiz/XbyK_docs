# Price calculation
  * [ Copy page link ](documentation/developers-and-admins/digital-commerce-setup/price-calculation#) | [Get HelpService ID](documentation/developers-and-admins/digital-commerce-setup/price-calculation#)
Core MVC 5


[✖](documentation/developers-and-admins/digital-commerce-setup/price-calculation# "Close page link panel") [Copy to clipboard](documentation/developers-and-admins/digital-commerce-setup/price-calculation#)
**Advanced license required**   
  
Features described on this page require the Xperience by Kentico **Advanced** license tier. 
The price calculation service `IPriceCalculationService` calculates prices, promotions, taxes, shipping costs, and totals across the entire commerce experience – from [product catalog](documentation/developers-and-admins/digital-commerce-setup/model-product-catalog) displays through shopping cart summaries to final [checkout](documentation/developers-and-admins/digital-commerce-setup/checkout-process) totals.
Different calculation modes optimize the service for each scenario, running only the steps required for that context. This page explains the calculation pipeline and how to choose the right mode for your use case.
**Key points**
  * The price calculation service uses a **pipeline architecture** with sequential calculation steps.
  * Three **calculation modes** (`Catalog`, `ShoppingCart`, `Checkout`) optimize performance for different scenarios.
  * You must implement a **product data retriever** to connect the service to your catalog. See [Price calculation implementation](documentation/developers-and-admins/digital-commerce-setup/price-calculation/implementation#create-a-product-data-retriever).
  * **Tax calculation** requires a custom implementation – the default step does not calculate taxes. See [Price calculation implementation](documentation/developers-and-admins/digital-commerce-setup/price-calculation/implementation#create-a-tax-calculation-step).
  * All calculation steps can be [customized or extended](documentation/developers-and-admins/digital-commerce-setup/price-calculation/customization) to fit your business logic.


## Minimum required implementation
To make the price calculation API usable in your project, you need to implement two components:
Component |  Required for |  Description  
---|---|---  
**Product data retriever** |  All modes |  Implements `IProductDataRetriever` to load product information and pricing data from your [catalog](documentation/developers-and-admins/digital-commerce-setup/model-product-catalog). See [Implement product data retrieval](documentation/developers-and-admins/digital-commerce-setup/price-calculation/implementation#implement-product-data-retrieval).  
**Tax calculation step** |  ShoppingCart, Checkout modes |  Overrides the default `ITaxPriceCalculationStep` to calculate taxes. The default implementation does not add taxes. See [Price calculation implementation](documentation/developers-and-admins/digital-commerce-setup/price-calculation/implementation#create-a-tax-calculation-step).  
Once you’ve implemented and registered these components, the price calculation service automatically handles the rest of the calculation pipeline, including unit price calculations, catalog promotions, subtotals, order promotions, shipping costs, and final totals.
For complete implementation examples, examine the _ProductDataRetriever.cs_ and _DancingGoatTaxPriceCalculationStep.cs_ files in the [Dancing Goat](documentation/developers-and-admins/installation#available-project-templates) sample project.
## Calculation modes
The price calculation service supports three calculation modes that determine which steps execute. Set the `Mode` property on the `PriceCalculationRequest` to control the calculation scope.
Mode |  Use case |  Steps executed  
---|---|---  
**Catalog** |  Product listings, category pages, product detail pages |  Product data loading, catalog promotions, line subtotals  
**ShoppingCart** |  Shopping cart display before shipping selection |  All Catalog steps plus order promotions, taxes, line totals, order totals  
**Checkout** |  Final checkout with shipping |  All steps including shipping calculation  
The default mode is `PriceCalculationMode.Catalog`. Always set the appropriate mode based on your context to ensure correct calculations and optimal performance.
C#
**Set calculation mode based on context**
Copy
```
var catalogRequest = new PriceCalculationRequest
{
    Items = productItems,
    LanguageName = "en",
    // Defaults to 'Catalog' if not set
    Mode = PriceCalculationMode.Catalog
};
```

For detailed usage examples of each mode, see [Calculate prices](documentation/developers-and-admins/digital-commerce-setup/price-calculation/implementation#calculate-prices).
## Price calculation flow
The price calculation service transforms a `PriceCalculationRequest` into a `PriceCalculationResult` through a series of sequential steps:
  1. **Input preparation** – You provide a `PriceCalculationRequest` containing items to be priced with their product identifiers and quantities. Depending on the calculation mode, you may also include customer details, selected shipping and payment methods, and delivery addresses.
  2. **Price calculation steps** – The `IPriceCalculationService` executes a series of calculation steps in sequence. Each step performs a specific pricing task, modifying the `PriceCalculationResult` as it progresses through the pipeline.
  3. **Result compilation** – The service returns a `PriceCalculationResult` containing itemized pricing details, subtotals, shipping costs, taxes, and the grand total.


[![Price calculation flow in Xperience](docsassets/documentation/price-calculation/calculation-overview.drawio.svg)](https://docs.kentico.com/docsassets/documentation/price-calculation/calculation-overview.drawio.svg)
### Calculation steps
The calculation pipeline consists of the following steps. Each step only runs if included in the selected [calculation mode](documentation/developers-and-admins/digital-commerce-setup/price-calculation#calculation-modes).
Step |  Modes |  Description  
---|---|---  
Load product data |  All |  Loads product information and pricing data using your `IProductDataRetriever` implementation  
Catalog promotions |  All |  Applies [catalog promotions](documentation/developers-and-admins/digital-commerce-setup/promotions/catalog-discounts) to individual products (best discount wins)  
Line subtotals |  All |  Calculates line subtotals after catalog discounts  
Shipping |  Checkout |  Determines shipping cost based on the selected shipping method  
Order promotions |  ShoppingCart, Checkout |  Applies [order promotions](documentation/developers-and-admins/digital-commerce-setup/promotions/order-discounts) that discount the entire order  
Taxes |  ShoppingCart, Checkout |  Calculates taxes (requires custom implementation)  
Line totals |  ShoppingCart, Checkout |  Calculates final line totals including taxes  
Order total |  ShoppingCart, Checkout |  Sums line subtotals, shipping, and taxes  
Grand total |  ShoppingCart, Checkout |  Sets the final grand total to be paid  
You can customize the default flow by [modifying existing steps](documentation/developers-and-admins/digital-commerce-setup/price-calculation/customization#modify-existing-calculation-steps) or [implementing custom steps](documentation/developers-and-admins/digital-commerce-setup/price-calculation/customization#implement-custom-calculation-steps) and inserting them into the pipeline.
## Basic usage
Inject `IPriceCalculationService` and call `Calculate` with a `PriceCalculationRequest`. The service returns a `PriceCalculationResult` containing itemized prices, totals, and applied promotions.
C#
**Calculate prices for cart items**
Copy
```
public class ShoppingCartController : Controller
{
    private readonly IPriceCalculationService priceCalculationService;

    public ShoppingCartController(IPriceCalculationService priceCalculationService)
    {
        this.priceCalculationService = priceCalculationService;
    }

    public async Task<IActionResult> DisplayCart(IEnumerable<ShoppingCartItem> cartItems)
    {
        // Build the request with items to price
        var request = new PriceCalculationRequest
        {
            Items = cartItems.Select(item => new PriceCalculationRequestItem
            {
                ProductIdentifier = item.ProductSKU,
                Quantity = item.Quantity
            }).ToList(),
            LanguageName = "en",
            Mode = PriceCalculationMode.ShoppingCart
        };

        // Calculate prices
        PriceCalculationResult result = await priceCalculationService.Calculate(request);

        // Use the results
        var viewModel = new CartViewModel
        {
            Items = result.Items,
            Subtotal = result.TotalPrice,
            Tax = result.TotalTax,
            Total = result.GrandTotal
        };

        return View(viewModel);
    }
}
```

For complete examples of each calculation mode, see [Calculate prices](documentation/developers-and-admins/digital-commerce-setup/price-calculation/implementation#calculate-prices).
## Next steps
  * [Price calculation implementation](documentation/developers-and-admins/digital-commerce-setup/price-calculation/implementation) – Implement product data retrieval and use the calculation service
  * [Price calculation customization](documentation/developers-and-admins/digital-commerce-setup/price-calculation/customization) – Extend data objects, modify calculation steps, or add custom steps
  * [Checkout process](documentation/developers-and-admins/digital-commerce-setup/checkout-process) – Integrate price calculation into your checkout process