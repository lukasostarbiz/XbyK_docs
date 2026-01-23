---
source: https://docs.kentico.com/documentation/developers-and-admins/digital-commerce-setup/promotions/order-discounts
scrape_date: 2026-01-22
---

  * [Home](/documentation)
  * [Developers and admins](/documentation/developers-and-admins)
  * [Digital commerce setup](/documentation/developers-and-admins/digital-commerce-setup)
  * [Promotions](/documentation/developers-and-admins/digital-commerce-setup/promotions)
  * Order discounts 


# Order discounts
**Advanced license required**   
  
Features described on this page require the Xperience by Kentico **Advanced** license tier. 
The order discount framework allows you to create custom promotion rules that apply discounts to entire orders during [price calculation](/documentation/developers-and-admins/digital-commerce-setup/price-calculation). Order discounts are evaluated based on the order’s total value or item count and automatically apply when conditions are met.
Use order discounts to implement:
  * Percentage-based discounts (e.g., 10% off orders over $100)
  * Fixed amount discounts (e.g., $15 off your order)
  * Quantity-based promotions (e.g., discounts on orders with 5+ items)


**Order discounts are for price discounts only**
Order discounts are exclusively intended for calculating price discounts. Making other changes to the order via order discounts, such as adding extra items to the order or modifying the cart contents, may disrupt the resulting [price calculation](/documentation/developers-and-admins/digital-commerce-setup/price-calculation) and produce invalid results.
## Promotion types
The system supports two types of promotions:
  * **Catalog discounts** – Apply discounts to individual products. Each product can have at most one catalog promotion applied. See [Catalog discounts](/documentation/developers-and-admins/digital-commerce-setup/promotions/catalog-discounts).
  * **Order discounts** – Apply discounts to the entire order based on its contents (for example, percentage discounts for orders above a given total price).


## Order promotion rule overview
A **promotion rule** defines the logic for determining whether a promotion applies to an order and how to calculate the discount amount. You implement promotion rules as classes that inherit from `OrderPromotionRule`.
**Terminology – Discounts vs. Promotions**
In the administration interface, order discounts are managed under **Promotions → Order discounts**. However, in code, the related classes use “Promotion” in their names (e.g., `OrderPromotionRule`, `OrderPromotionCandidate`). This is because the underlying system uses a unified promotion framework.
### Promotion candidate
A **promotion candidate** represents a potential discount that could be applied to an order. When a promotion rule evaluates an order, it returns an `OrderPromotionCandidate` containing:
  * `OrderDiscountAmount` – The calculated discount amount


The [price calculation pipeline](/documentation/developers-and-admins/digital-commerce-setup/price-calculation) collects all candidates and applies the discount to the order total.
When multiple order promotions could apply to an order, the system automatically selects the best promotion based on the following criteria:
  1. The promotion with the highest `OrderDiscountAmount` is selected.
  2. If two promotions have equal discount amounts, the most recently created promotion takes precedence. As a result, this may skew redemption statistics tracked in the admin UI in rare cases.


Order promotions are not cumulative. Only one order promotion can be applied per order.
### Promotion rule properties
Promotion rules can define configurable properties that store managers set when creating promotions in the administration interface. Properties are defined in a separate class implementing `IPromotionRuleProperties` and are decorated with [editing components](/documentation/developers-and-admins/customization/extend-the-administration-interface/ui-form-components/editing-components) to generate the UI.
## Create order promotion rules
Implementing an order promotion rule involves creating two components:
  1. **Properties class** – Defines configurable settings that store managers can adjust in the administration interface (e.g., discount percentage, minimum requirements). The properties class implements `IPromotionRuleProperties` or inherits from `OrderPromotionRuleProperties`.
  2. **Logic class** – Contains the rule’s evaluation logic to determine if the order is eligible for the discount and calculates the discount amount. The logic class inherits from `OrderPromotionRuleBase` or `OrderPromotionRule`.


These two components work together – the properties class stores the configuration, and the logic class uses those configured values to evaluate orders during price calculation.
[![Order promotion rule class hierarchy showing the relationship between properties and logic components](/docsassets/documentation/order-discounts/order-promotion-rule-hierarchy.drawio.svg)](/docsassets/documentation/order-discounts/order-promotion-rule-hierarchy.drawio.svg)
**Sample implementation**
See **DancingGoatOrderPromotionRule.cs** in the Dancing Goat [project template](/documentation/developers-and-admins/installation#available-project-templates) for a sample implementation of an order discount rule.
### Define promotion rule properties
The system provides `OrderPromotionRuleProperties` as a base class with common properties already defined and configurable via the administration interface:
  * **DiscountValueType** – Dropdown to select percentage or fixed discount
  * **DiscountValue** – Decimal input for the discount amount
  * **MinimumRequirementValueType** – Radio group to select no minimum, minimum purchase amount, or minimum quantity of items
  * **MinimumRequirementValue** – Decimal input for minimum value (visible when minimum purchase amount or minimum quantity is selected)


You can inherit from this base class to include these standard fields and add your own custom properties. Use [editing component](/documentation/developers-and-admins/customization/extend-the-administration-interface/ui-form-components/editing-components) annotations to generate the input fields for each property. The following sample demonstrates an extended properties class that inherits from `OrderPromotionRuleProperties` and
C#
**Promotion rule properties extending the base class**
Copy
```
/// <summary>
/// Sample order promotion proerties class extending the default configrable prop
/// set with a custom fields. Uses editing component annotations.
/// </summary>
public class SampleOrderPromotionRuleProperties : OrderPromotionRuleProperties
{
    // Base class properties include: DiscountValueType, DiscountValue,
    // MinimumRequirementValueType, MinimumRequirementValue

    // Add custom properties for your business logic
    [CheckBoxComponent(
        Label = "Additional checkbox field",
        Order = 1)]
    public bool MembersOnly { get; set; }
}
```

The base class properties use negative `Order` values (e.g., -190, -180) to ensure they appear at the top of the form. Keep this in mind when positioning custom properties.
Alternatively, you can directly implement `IPromotionRuleProperties`, bypassing the inheritance entirely, if you need full control over all configuration properties used by the rule.
### Implement the promotion rule
The framework provides two base classes for order promotion rules:
  * **OrderPromotionRule** – Use when your properties class inherits from `OrderPromotionRuleProperties`. Provides built-in helper methods that work with the base properties, including automatic minimum requirement validation (purchase amount or item quantity).
  * **OrderPromotionRuleBase** – Use when you need fully custom properties implementing `IPromotionRuleProperties` directly. You must implement all discount calculation and validation logic yourself.


Both base classes use the same generic type parameters:
  1. `TPromotionRuleProperties` – The rule’s [properties class](#define-promotion-rule-properties). Must inherit from `OrderPromotionRuleProperties` for `OrderPromotionRule`, or implement `IPromotionRuleProperties` for `OrderPromotionRuleBase`.
  2. `TPriceCalculationRequest` – The price calculation request type.
  3. `TPriceCalculationResult` – The price calculation result type.


Register the rule using `RegisterPromotionRuleAttribute` with the following parameters:
  1. A unique string identifier for the rule.
  2. `PromotionType.Order` to indicate this is an order promotion.
  3. A display name shown in the administration interface.


When your properties class inherits from `OrderPromotionRuleProperties`, use `OrderPromotionRule` to access built-in helper methods:
  * `GetDiscountAmount(linesSubtotalAfterLineDiscounts)` – Calculates the discount based on `DiscountValueType` and `DiscountValue` from the base properties. Handles both percentage and fixed discounts, ensuring the discount never exceeds the order subtotal.
  * `GetDiscountValueLabel()` – Returns a formatted label for display (e.g., “10%” or “$5.00”).


The following sample demonstrates a discount rule that enables only the default configuration. Since no additional configuration options are necessary, it uses the default properties class.
C#
**Order promotion rule with built-in helpers**
Copy
```
public class OrderPercentageDiscountRule
    : OrderPromotionRule<OrderPromotionRuleProperties,
        PriceCalculationRequest, PriceCalculationResult>
{
    public override OrderPromotionCandidate GetPromotionCandidate(
        IPriceCalculationData<PriceCalculationRequest, PriceCalculationResult> calculationData)
    {
        // Gets the total price after catalog discounts
        var totalPrice = calculationData.Result.Items
            .Sum(i => i.LineSubtotalAfterLineDiscount);

        return new OrderPromotionCandidate
        {
            // Calculates the discount using the built-in helper method
            // Handles both percentage and fixed discounts based
            // on DiscountValueType (as configured via the admin UI)
            OrderDiscountAmount = GetDiscountAmount(totalPrice)
        };
    }
}
```

After implementing and registering an order promotion rule:
  1. The promotion rule appears in the administration interface when [creating new order promotions](/documentation/business-users/manage-commerce-stores#create-a-promotion).
  2. Store managers can configure the promotion properties, set minimum requirements, and set activation dates.
  3. Active promotions are automatically evaluated during price calculation.
  4. The discount is applied to eligible orders that meet the configured minimum requirements.


### Register the promotion rule
Use the `RegisterPromotionRuleAttribute` assembly attribute to register your promotion rule with the system. Without registration, the rule will not appear in the administration interface.
C#
**Register an order promotion rule**
Copy
```
[assembly: RegisterPromotionRule<OrderPercentageDiscountRule>(
    "Acme.OrderPercentageDiscount",
    PromotionType.Order,
    "Order Percentage Discount")]
```

### Filter promotion applicability
Override the `IsApplicable` method to add high-level conditions that determine whether the promotion should be evaluated at all. This method runs **once per promotion** before calculating the discount, making it useful for performance optimization when you can skip the entire promotion early.
**Built-in minimum requirement validation**
When using `OrderPromotionRule`, the base class `IsApplicable` implementation automatically validates minimum requirements (purchase amount or item quantity) based on `MinimumRequirementValueType` and `MinimumRequirementValue` properties. You only need to override `IsApplicable` if you have additional custom conditions.
The evaluation pipeline works as follows:
  1. **Get active promotions** – The system retrieves all active order promotions.
  2. **Check IsApplicable** – For each promotion, `IsApplicable` is called once. If it returns `false`, the promotion is skipped entirely.
  3. **Calculate discount** – For promotions that pass, `GetPromotionCandidate` is called to calculate the order discount.
  4. **Apply promotion** – The system applies the discount to the order total.


Use `IsApplicable` for checks that apply to the entire promotion context. For example, to restrict a promotion to registered [members](/documentation/developers-and-admins/development/registration-and-authentication) while keeping the built-in minimum requirement validation:
C#
**Check if a customer is a registered member**
Copy
```
private readonly IInfoProvider<CustomerInfo> customerInfoProvider;

public MemberOrderDiscountRule(IInfoProvider<CustomerInfo> customerInfoProvider)
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
    if (customer == null || customer.CustomerMemberID <= 0)
    {
        return false;
    }

    // Calls the rest of the logic
    return await base.IsApplicable(calculationData, cancellationToken);
}
```

Another common scenario is restricting a promotion to first-time customers who haven’t placed any orders yet.
This rule assumes that:
  * the customer is a registered site [member](/documentation/developers-and-admins/development/registration-and-authentication)
  * and has not made any orders in the store yet.


C#
**Check if a customer is a first-time buyer**
Copy
```
using System;
using System.Linq;
using System.Threading;
using System.Threading.Tasks;

using CMS.Commerce;
using CMS.DataEngine;
using Codesamples.Commerce;
using Kentico.Xperience.Admin.DigitalCommerce;

using Microsoft.AspNetCore.Http;

[assembly: RegisterPromotionRule<FirstTimeBuyerOrderPromotionRule>(
    identifier: FirstTimeBuyerOrderPromotionRule.IDENTIFIER,
    promotionType: PromotionType.Order,
    name: "First time buyer discount"
)]

public class FirstTimeBuyerOrderPromotionRule
 : OrderPromotionRule<OrderPromotionRuleProperties,
                      CodesamplesPriceCalculationRequest,
                      CodesamplesPriceCalculationResult>
{
    public const string IDENTIFIER = "Acme.DigitalCommerce.FirstTimeBuyerOrderRule";

    private readonly IInfoProvider<OrderInfo> orderProvider;
    private readonly IHttpContextAccessor httpContextAccessor;

    public FirstTimeBuyerOrderPromotionRule(
        IInfoProvider<OrderInfo> orderProvider,
        IHttpContextAccessor httpContextAccessor)
    {
        this.orderProvider = orderProvider;
        this.httpContextAccessor = httpContextAccessor;
    }

    public override async Task<bool> IsApplicable(
        IPriceCalculationData<CodesamplesPriceCalculationRequest,
                              CodesamplesPriceCalculationResult> calculationData,
        CancellationToken cancellationToken)
    {
        bool isFirstTimeCustomer =
            await IsFirstTimeCustomer(
                calculationData.Request.CustomerId, cancellationToken);

        // Checks the base class conditions (minimum purchase requirements)
        return isFirstTimeCustomer
            && await base.IsApplicable(calculationData, cancellationToken);
    }

    /// <summary>
    /// Determines whether the customer is making their first purchase.
    /// Returns true for authenticated users with no previous orders.
    /// </summary>
    private async Task<bool> IsFirstTimeCustomer(int customerId, CancellationToken cancellationToken)
    {
        Console.WriteLine(customerId);
        // Applies only to authenticated live site members
        bool isAuthenticated =
            httpContextAccessor.HttpContext?.User?.Identity?.IsAuthenticated
            ?? false;
        if (!isAuthenticated)
        {
            return false;
        }

        // Applies to members that aren't tracked
        // as customers by the system
        if (customerId == 0)
        {
            return true;
        }

        // If the customer exists, checks if
        // they have any existing orders
        var orders = await orderProvider.Get()
            .WhereEquals(nameof(OrderInfo.OrderCustomerID), customerId)
            .TopN(1)
            .GetEnumerableTypedResultAsync(cancellationToken: cancellationToken);

        // Only applies to customers with no previous orders
        return !orders.Any();
    }

    public override OrderPromotionCandidate GetPromotionCandidate(
        IPriceCalculationData<CodesamplesPriceCalculationRequest,
                              CodesamplesPriceCalculationResult> calculationData)
    {
        // Gets the total price after catalog discounts
        var totalLinePriceAfterLineDiscount = calculationData.Result.Items
            .Sum(i => i.LineSubtotalAfterLineDiscount);

        // Apply discount for first-time buyers
        return new OrderPromotionCandidate
        {
            OrderDiscountAmount = GetDiscountAmount(totalLinePriceAfterLineDiscount)
        };
    }
}
```

## Access promotion data in calculation results
After price calculation, you can access promotion information from the result to display discount details to customers or for reporting purposes.
Order promotion candidates are stored in the `PromotionData.OrderPromotionCandidates` collection on the calculation result. Each candidate is wrapped in `IPriceCalculationPromotionCandidate<OrderPromotionCandidate>` which includes an `Applied` flag indicating whether the promotion was applied to the order:
C#
**Access promotion data from calculation results**
Copy
```
public static async Task AccessPromotionDataExample(
    IPriceCalculationService<PriceCalculationRequest, PriceCalculationResult> priceCalculationService,
    PriceCalculationRequest request)
{
    // Calculates the price
    PriceCalculationResult calculationResult =
        await priceCalculationService.Calculate(request);

    // Gets the applied order promotion
    IPriceCalculationPromotionCandidate<OrderPromotionCandidate>? appliedOrderPromotion =
        calculationResult.PromotionData.OrderPromotionCandidates
            .FirstOrDefault(p => p.Applied);

    if (appliedOrderPromotion is not null)
    {
        decimal discountAmount =
            appliedOrderPromotion.PromotionCandidate.OrderDiscountAmount;
        // Use the discount information...
    }
}
```

## Extend promotion candidate objects
You can extend `OrderPromotionCandidate` to include additional data needed for display or business logic.
To access the custom promotion candidate in calculation results, cast the candidate from the `OrderPromotionCandidates` collection:
C#
**Access custom promotion candidate**
Copy
```
public static async Task AccessCustomCandidateExample(
    IPriceCalculationService<PriceCalculationRequest, PriceCalculationResult> priceCalculationService,
    PriceCalculationRequest request)
{
    // Calculates the price
    PriceCalculationResult calculationResult
        = await priceCalculationService.Calculate(request);

    // Gets the applied order promotion and casts to custom type
    IPriceCalculationPromotionCandidate<OrderPromotionCandidate>? appliedPromotion =
        calculationResult.PromotionData.OrderPromotionCandidates
            .FirstOrDefault(p => p.Applied);

    if (appliedPromotion is not null) {
        // Checks if the candidate is a custom type and works with the results
        if (appliedPromotion?.PromotionCandidate is DisplayableOrderPromotionCandidate displayable)
        {
            string label = displayable.DisplayLabel;
            // Process the label...
        }
    }
}
```

![]()
[]()[]()
