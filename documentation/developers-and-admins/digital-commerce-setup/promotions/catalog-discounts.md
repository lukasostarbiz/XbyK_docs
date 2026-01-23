---
source: https://docs.kentico.com/documentation/developers-and-admins/digital-commerce-setup/promotions/catalog-discounts
scrape_date: 2026-01-22
---

  * [Home](/documentation)
  * [Developers and admins](/documentation/developers-and-admins)
  * [Digital commerce setup](/documentation/developers-and-admins/digital-commerce-setup)
  * [Promotions](/documentation/developers-and-admins/digital-commerce-setup/promotions)
  * Catalog discounts 


# Catalog discounts
**Advanced license required**   
  
Features described on this page require the Xperience by Kentico **Advanced** license tier. 
Catalog discounts allow you to create custom promotion rules that apply discounts to products during [price calculation](/documentation/developers-and-admins/digital-commerce-setup/price-calculation). Catalog discounts are evaluated for each product in a shopping cart or order and automatically apply the best available discount.
Use catalog discounts to implement:
  * Percentage-based discounts (e.g., 10% off all coffees)
  * Fixed amount discounts (e.g., $5 off selected products)
  * Category-based promotions (e.g., discounts on all products in a category)


**Catalog promotion rules are for unit price discounts only**
Catalog promotion rules are exclusively intended for calculating unit price discounts. Performing other modifications inside custom promotion rules, such as adding extra items to the order or modifying the cart contents, may disrupt the calculation pipeline and produce invalid results.
## Promotion types
The system supports two types of promotions:
  * **Catalog discounts** – Apply discounts to individual products. Each product can have at most one catalog promotion applied. The system automatically selects the promotion that provides the highest discount.
  * **Order discounts** – Apply discounts to the entire order based on its contents (for example, percentage discounts for orders above given total price). See [Order discounts](/documentation/developers-and-admins/digital-commerce-setup/promotions/order-discounts).


## Catalog promotion rule overview
A **promotion rule** defines the logic for determining whether a catalog discount applies to a product and how to calculate the discount amount. You implement promotion rules as classes that inherit from `CatalogPromotionRule`.
**Terminology – Discounts vs. Promotions**
In the administration interface, catalog discounts are managed under **Promotions → Catalog discounts**. However, in code, the related classes use “Promotion” in their names (e.g., `CatalogPromotionRule`, `CatalogPromotionCandidate`). This is because the underlying system uses a unified promotion framework.
### Promotion candidate
A **promotion candidate** represents a potential discount that could be applied to a product. When a promotion rule evaluates a product, it returns a `CatalogPromotionCandidate` containing the calculated discount amount. The [price calculation pipeline](/documentation/developers-and-admins/digital-commerce-setup/price-calculation) collects all candidates and selects the best one.
### Promotion rule properties
Promotion rules can define configurable properties that store managers set when creating promotions in the administration interface. Properties are defined in a separate class implementing `IPromotionRuleProperties` and are decorated with [editing components](/documentation/developers-and-admins/customization/extend-the-administration-interface/ui-form-components/editing-components) to generate the UI.
### Promotion selection logic
When multiple catalog promotions could apply to a product, the system automatically selects the best promotion based on the following criteria:
  1. The promotion with the highest `UnitPriceDiscountAmount` is selected.
  2. If two promotions have equal discount amounts, the most recently activated promotion takes precedence. This may in rare cases skew redemption statistics tracked in the admin UI.


Catalog promotions are **not stackable** , only one catalog promotion can be applied per product.
## Create catalog promotion rules
Implementing a catalog promotion rule involves creating two components:
  1. **Properties class** – Defines configurable settings that store managers can adjust in the administration interface (e.g., discount percentage, eligible categories). The properties class implements `IPromotionRuleProperties` or inherits from `CatalogPromotionRuleProperties`.
  2. **Logic class** – Contains the rule’s evaluation logic to determine if a product is eligible for the discount and calculates the discount amount. The logic class inherits from `CatalogPromotionRuleBase` or `CatalogPromotionRule`.


These two components work together: the properties class stores the configuration, and the logic class uses those configured values to evaluate products during price calculation.
[![Catalog promotion rule class hierarchy showing the relationship between properties and logic components](/docsassets/documentation/catalog-discounts/catalog-promotion-rule-hierarchy.drawio.svg)](/docsassets/documentation/catalog-discounts/catalog-promotion-rule-hierarchy.drawio.svg)
**Sample implementation**
See **DancingGoatCatalogPromotionRule.cs** in the Dancing Goat [project template](/documentation/developers-and-admins/installation#available-project-templates) for a sample implementation of a catalog discount rule.
### Define promotion rule properties
The system provides `CatalogPromotionRuleProperties` as a base class with common properties already defined:
  * **DiscountValueType** – Dropdown to select percentage or fixed discount
  * **DiscountValue** – Decimal input for the discount amount


You can inherit from this base class to include these standard fields and add your own custom properties. Use [editing component](/documentation/developers-and-admins/customization/extend-the-administration-interface/ui-form-components/editing-components) annotations to generate the input fields for each property.
The following example assumes a [product catalog](/documentation/developers-and-admins/digital-commerce-setup/model-product-catalog) modeled using [taxonomies](/documentation/developers-and-admins/configuration/taxonomies). It allows store admins to apply the promotion broadly per category:
C#
**Promotion rule properties extending the base class**
Copy
```
/// <summary>
/// Properties for configuring a catalog discount based on product category.
/// Extends <see cref="CatalogPromotionRuleProperties"/> to include standard discount fields.
/// </summary>
public class CatalogDiscountBasedOnProductCategoryProperties
    : CatalogPromotionRuleProperties
{
    // DiscountValue and DiscountValueType properties
    // are inherited from CatalogPromotionRuleProperties

    /// <summary>
    /// Product categories eligible for the discount.
    /// </summary>
    [TagSelectorComponent(
        CategoryService.PRODUCT_CATEGORY_TAXONOMY,
        Label = "Product categories",
        Order = 1)]
    public IEnumerable<TagReference> ProductCategories { get; set; } = Enumerable.Empty<TagReference>();
}
```

The base class properties use negative `Order` values (e.g., -90, -80) to ensure they appear at the top of the form. Keep this in mind when positioning custom properties.
Alternatively, you can directly implement `IPromotionRuleProperties` if you need full control over all configuration properties used by the rule.
### Implement the promotion rule
The framework provides two base classes for catalog promotion rules:
  * **CatalogPromotionRule** – Use when your properties class inherits from `CatalogPromotionRuleProperties`. Provides built-in helper methods that work with the base properties.
  * **CatalogPromotionRuleBase** – Use when you need fully custom properties implementing `IPromotionRuleProperties` directly. You must implement all discount calculation logic yourself.


Both base classes use the same generic type parameters:
  1. `TPromotionRuleProperties` – The rule’s [properties class](#define-promotion-rule-properties). Must inherit from `CatalogPromotionRuleProperties` for `CatalogPromotionRule`, or implement `IPromotionRuleProperties` for `CatalogPromotionRuleBase`.
  2. `TProductIdentifier` – The product identifier type – `ProductIdentifier` or a [derived type](/documentation/developers-and-admins/digital-commerce-setup/price-calculation/customization#extend-data-transfer-objects).
  3. `TPriceCalculationRequest` – The price calculation request type.
  4. `TPriceCalculationResult` – The price calculation result type.


[Register](#register-the-promotion-rule) the rule using `RegisterPromotionRuleAttribute` with the following parameters:
  1. A unique string identifier for the rule.
  2. `PromotionType.Catalog` to indicate this is a catalog promotion.
  3. A display name shown in the administration interface.


When your properties class inherits from `CatalogPromotionRuleProperties`, use `CatalogPromotionRule` to access built-in helper methods:
  * `GetDiscountAmount(unitPrice)` – Calculates the discount based on `DiscountValueType` and `DiscountValue` from the base properties. Handles both percentage and fixed discounts, ensuring the discount never exceeds the unit price.
  * `GetDiscountValueLabel()` – Returns a formatted label for display (e.g., “10%” or “$5.00”).


C#
**Catalog promotion rule with built-in helpers**
Copy
```
using System.Linq;

using CMS.Commerce;

using Kentico.Xperience.Admin.DigitalCommerce;

using Codesamples.Commerce;

/// <summary>
/// Catalog promotion rule that applies discounts to products in selected categories.
/// Uses custom <see cref="ProductDataWithCategory"/> through custom result type.
/// </summary>
public class CatalogDiscountBasedOnProductCategoryPromotionRule
    : CatalogPromotionRule<CatalogDiscountBasedOnProductCategoryProperties,
                           ProductIdentifier,
                           CodesamplesPriceCalculationRequest,
                           CodesamplesPriceCalculationResult>
{
    public override CatalogPromotionCandidate? GetPromotionCandidate(
        ProductIdentifier productIdentifier,
        IPriceCalculationData<CodesamplesPriceCalculationRequest, CodesamplesPriceCalculationResult> calculationData)
    {
        // Gets the result item for the current product
        var resultItem = calculationData.Result.Items
            .FirstOrDefault(i => i.ProductIdentifier == productIdentifier);

        if (resultItem?.ProductData is not CodesamplesProductData codesamplesProduct)
        {
            return null;
        }

        // Checks if the product belongs to any of the
        // categories selected when creating the rule
        bool isInEligibleCategory = codesamplesProduct.Categories
            .Intersect(Properties.ProductCategories)
            .Any();

        if (!isInEligibleCategory)
        {
            return null;
        }

        // Calculates the discount using the built-in helper method
        // Handles both percentage and fixed discounts based on DiscountValueType
        decimal discountAmount = GetDiscountAmount(codesamplesProduct.UnitPrice);

        return new CatalogPromotionCandidate
        {
            UnitPriceDiscountAmount = discountAmount
        };
    }
}
```

Note that the rule implementation uses `CodeSamplesPriceCalculationResult` to expose the product category data via a [customized](/documentation/developers-and-admins/digital-commerce-setup/price-calculation/customization#extend-data-transfer-objects) `ProductData` object:
C#
**Customized data objects**
Copy
```
// Extended product data that includes category information for promotion evaluation
public record CodesamplesProductData : ProductData
{
    public IEnumerable<TagReference> Categories { get; init; } = [];
}

// Price calculation result item enforcing usage of the modified CodesamplesProductData
public sealed class CodeSamplesPriceCalculationResultItem
    : PriceCalculationResultItemBase<ProductIdentifier, CodesamplesProductData>
{
}

/// Price calculation result for code samples.
public class CodeSamplesPriceCalculationResult
    : PriceCalculationResultBase<CodeSamplesPriceCalculationResultItem>
{
}
 


```

After implementing and registering a catalog promotion rule:
  1. The promotion rule appears in the administration interface when [creating new catalog discounts](/documentation/business-users/manage-commerce-stores).
  2. Store managers can configure the promotion and set activation dates.
  3. Active promotions are automatically evaluated during price calculation.
  4. The best promotion is applied to each eligible product.


### Register the promotion rule
Use the `RegisterPromotionRuleAttribute` assembly attribute to register your promotion rule in the system. Without registration, the rule will not appear in the administration interface.
C#
**Register a catalog promotion rule**
Copy
```
[assembly: RegisterPromotionRule<CatalogDiscountBasedOnProductCategoryPromotionRule>(
    identifier: "CatalogDiscountBasedOnProductCategory",
    promotionType: PromotionType.Catalog,
    name: "Discount based on product category")]
```

### Filter promotion applicability
Override the `IsApplicable` method to add high-level conditions that determine whether the promotion should be evaluated at all. This method runs **once per promotion** before iterating through products, making it useful for performance optimization when you can skip the entire promotion early.
The evaluation pipeline works as follows:
  1. **Get active promotions** – The system retrieves all active catalog promotions.
  2. **Check IsApplicable** – For each promotion, `IsApplicable` is called once. If it returns `false`, the promotion is skipped entirely.
  3. **Evaluate products** – For promotions that pass, `GetPromotionCandidate` is called for each product in the cart.
  4. **Select best promotion** – The system selects the promotion with the highest discount for each product.


Use `IsApplicable` for checks that apply to the entire promotion context (not individual products). For example, to check if the customer is registerd as a [member](/documentation/developers-and-admins/development/registration-and-authentication):
C#
**Check if a customer is a registered member**
Copy
```
private readonly IInfoProvider<CustomerInfo> customerInfoProvider;

public CatalogDiscountBasedOnProductCategoryPromotionRule(
    IInfoProvider<CustomerInfo> customerInfoProvider)
{
    this.customerInfoProvider = customerInfoProvider;
}

public override async Task<bool> IsApplicable(
    IPriceCalculationData<PriceCalculationRequest, PriceCalculationResult> calculationData,
    CancellationToken cancellationToken)
{
    // Gets the customer associated with the order
    CustomerInfo customer = await customerInfoProvider.GetAsync(
        calculationData.Request.CustomerId, cancellationToken);

    // Checks if a mapping between the customer and a member exists
    bool customerRegistered = customer?.CustomerMemberID > 0;

    return customerRegistered;
}
```

## Access promotion data in calculation results
After price calculation, you can access promotion information from the result to display discount details to customers or for reporting purposes.
Promotion candidates are stored in the `PromotionData.PromotionCandidates` collection on each result item. The collection contains all evaluated promotions, with the `Applied` property indicating which promotion was selected:
C#
**Access promotion data from calculation results**
Copy
```
public async Task DisplayPromotionInfo(
    PriceCalculationRequest request,
    CancellationToken cancellationToken)
{
    // Calculates the price
    PriceCalculationResult calculationResult =
        await priceCalculationService.Calculate(request, cancellationToken);

    // Gets the promotion applied per item
    foreach (var item in calculationResult.Items)
    {
        // Finds the applied catalog promotion (if any)
        var appliedPromotion = item.PromotionData?.CatalogPromotionCandidates
            .FirstOrDefault(p => p.Applied);

        if (appliedPromotion != null)
        {
            // Access promotion details via the PromotionCandidate property
            var candidate = appliedPromotion.PromotionCandidate;
            decimal discountAmount = candidate.UnitPriceDiscountAmount;

            // Work with the data...
        }
    }
}
```

## Extend promotion candidate objects
You can extend `CatalogPromotionCandidate` to include additional data needed for display or business logic. The [Dancing Goat](/documentation/developers-and-admins/installation#available-project-templates) sample project uses this approach with `DancingGoatCatalogPromotionCandidate` to store a display label for showing discount information to customers.
To access the custom promotion candidate in calculation results, cast the `PromotionCandidate` property:
C#
**Access custom promotion candidate**
Copy
```
public async Task ProcessCustomPromotionCandidate(
    PriceCalculationRequest request,
    CancellationToken cancellationToken)
{
    // Calculates the price
    PriceCalculationResult calculationResult
        = await priceCalculationService.Calculate(request, cancellationToken);

    foreach (PriceCalculationResultItem item in calculationResult.Items)
    {
        IPriceCalculationPromotionCandidate<CatalogPromotionCandidate>? appliedPromotion = item.PromotionData?.CatalogPromotionCandidates
            .FirstOrDefault(p => p.Applied);

        // Checks if the candidate is a custom 'DisplayableCatalogPromotionCandidate'
        // and works with the results
        if (appliedPromotion?.PromotionCandidate
                is DisplayableCatalogPromotionCandidate displayable)
        {
            string label = displayable.DisplayLabel;
            // Process the label...
        }
    }
}
```

![]()
[]()[]()
