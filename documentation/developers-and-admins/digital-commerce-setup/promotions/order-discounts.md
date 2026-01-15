# Order discounts
  * [ Copy page link ](documentation/developers-and-admins/digital-commerce-setup/promotions/order-discounts#) | [Get HelpService ID](documentation/developers-and-admins/digital-commerce-setup/promotions/order-discounts#)
Core MVC 5


[✖](documentation/developers-and-admins/digital-commerce-setup/promotions/order-discounts# "Close page link panel") [Copy to clipboard](documentation/developers-and-admins/digital-commerce-setup/promotions/order-discounts#)
**Advanced license required**   
  
Features described on this page require the Xperience by Kentico **Advanced** license tier. 
The order discount framework allows you to create custom promotion rules that apply discounts to entire orders during price calculation. Order discounts are evaluated based on the order’s total value or item count and automatically apply when conditions are met.
Use order discounts to implement:
  * Percentage-based discounts (e.g., 10% off orders over $100)
  * Fixed amount discounts (e.g., $15 off your order)
  * Quantity-based promotions (e.g., discounts on orders with 5+ items)


## Promotion types
The system supports two types of promotions:
  * **Catalog promotions** – Apply discounts to individual products. Each product can have at most one catalog promotion applied. See [Catalog discounts](documentation/developers-and-admins/digital-commerce-setup/promotions/catalog-discounts).
  * **Order promotions** – Apply discounts to the entire order based on its contents (for example, percentage discounts for orders above a given total price).


## Order promotion rule overview
A **promotion rule** defines the logic for determining whether a promotion applies to an order and how to calculate the discount amount. You implement promotion rules as classes that inherit from `OrderPromotionRule`.
**Order promotion rules are for price discounts only**
Order promotion rules are exclusively intended for calculating price discounts. Making other changes to the order inside custom promotion rules, such as adding extra items to the order or modifying the cart contents, may disrupt the resulting [price calculation](documentation/developers-and-admins/digital-commerce-setup/price-calculation) and produce invalid results.
### Promotion candidate
A **promotion candidate** represents a potential discount that could be applied to an order. When a promotion rule evaluates an order, it returns an `OrderPromotionCandidate` containing:
  * `OrderDiscountAmount` – The calculated discount amount


The [price calculation pipeline](documentation/developers-and-admins/digital-commerce-setup/price-calculation) collects all candidates and applies the discount to the order total.
### Promotion rule properties
Promotion rules can define configurable properties that store managers set when creating promotions in the administration interface. Properties are defined in a separate class implementing `IPromotionRuleProperties` and are decorated with [editing components](documentation/developers-and-admins/customization/extend-the-administration-interface/ui-form-components/editing-components) to generate the UI.
## Create order promotion rules
### Define promotion rule properties
The system provides `OrderPromotionRuleProperties` as a base class with common properties already defined:
  * **DiscountValueType** – Dropdown to select percentage or fixed discount
  * **DiscountValue** – Decimal input for the discount amount
  * **MinimumRequirementValueType** – Radio group to select no minimum, minimum purchase amount, or minimum quantity of items
  * **MinimumRequirementValue** – Decimal input for minimum value (visible when minimum purchase amount or minimum quantity is selected)


You can inherit from this base class to include these standard fields and add your own custom properties. Use [editing component](documentation/developers-and-admins/customization/extend-the-administration-interface/ui-form-components/editing-components) annotations to generate the input fields for each property:
C#
**Promotion rule properties extending the base class**
Copy
```
using Kentico.Xperience.Admin.Base.FormAnnotations;
using Kentico.Xperience.Admin.DigitalCommerce.Shared;

public class MemberOrderDiscountProperties : OrderPromotionRuleProperties
{
    // Base class properties include: DiscountValueType, DiscountValue,
    // MinimumRequirementValueType, MinimumRequirementValue

    // Add custom properties for your business logic
    [CheckBoxComponent(
        Label = "Members only",
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
  1. `TPromotionRuleProperties` – The rule’s [properties class](documentation/developers-and-admins/digital-commerce-setup/promotions/order-discounts#define-promotion-rule-properties). Must inherit from `OrderPromotionRuleProperties` for `OrderPromotionRule`, or implement `IPromotionRuleProperties` for `OrderPromotionRuleBase`.
  2. `TPriceCalculationRequest` – The price calculation request type.
  3. `TPriceCalculationResult` – The price calculation result type.


Register the rule using `RegisterPromotionRuleAttribute` with the following parameters:
  1. A unique string identifier for the rule.
  2. `PromotionType.Order` to indicate this is an order promotion.
  3. A display name shown in the administration interface.


When your properties class inherits from `OrderPromotionRuleProperties`, use `OrderPromotionRule` to access built-in helper methods:
  * `GetDiscountAmount(linesSubtotalAfterLineDiscounts)` – Calculates the discount based on `DiscountValueType` and `DiscountValue` from the base properties. Handles both percentage and fixed discounts, ensuring the discount never exceeds the order subtotal.
  * `GetDiscountValueLabel()` – Returns a formatted label for display (e.g., “10%” or “$5.00”).


C#
**Order promotion rule with built-in helpers**
Copy
```
using CMS.Commerce;

using Kentico.Xperience.Admin.DigitalCommerce.Shared;

[assembly: RegisterPromotionRule<OrderPercentageDiscountRule>(
    "OrderPercentageDiscount",
    PromotionType.Order,
    "Order Percentage Discount")]

public class OrderPercentageDiscountRule
    : OrderPromotionRule<OrderPromotionRuleProperties,
        PriceCalculationRequest, PriceCalculationResult>
{
    public override OrderPromotionCandidate GetPromotionCandidate(IPriceCalculationData<DancingGoatPriceCalculationRequest, DancingGoatPriceCalculationResult> calculationData)
    {
        // Gets the total price after catalog discounts
        var totalPrice = calculationData.Result.Items.Select(i => i.LineSubtotalAfterLineDiscount).Sum();

        return new OrderPromotionCandidate()
        {
            OrderDiscountAmount = GetDiscountAmount(totalPrice)
        };
    }
}
```

After implementing and registering an order promotion rule:
  1. The promotion rule appears in the administration interface when creating new order promotions.
  2. Store managers can configure the promotion properties, set minimum requirements, and set activation dates.
  3. Active promotions are automatically evaluated during price calculation.
  4. The discount is applied to eligible orders that meet the configured minimum requirements.


**Sample implementation**
See **DancingGoatOrderPromotionRule.cs** in the Dancing Goat [project template](documentation/developers-and-admins/installation#available-project-templates) for a sample implementation of a catalog discount rule.
### Register the promotion rule
Use the `RegisterPromotionRuleAttribute` assembly attribute to register your promotion rule with the system. Without registration, the rule will not appear in the administration interface.
C#
**Register an order promotion rule**
Copy
```
[assembly: RegisterPromotionRule<OrderPercentageDiscountRule>(
    "OrderPercentageDiscount",    // Unique identifier
    PromotionType.Order,          // Promotion type
    "Order Percentage Discount")] // Display name in admin UI
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


Use `IsApplicable` for checks that apply to the entire promotion context. For example, to restrict a promotion to registered [members](documentation/developers-and-admins/development/registration-and-authentication) while keeping the built-in minimum requirement validation:
C#
**Check if a customer is a registered member**
Copy
```
private readonly IInfoProvider<CustomerInfo> customerInfoProvider;

public override async Task<bool> IsApplicable(
    IPriceCalculationData<PriceCalculationRequest, PriceCalculationResult> calculationData,
    CancellationToken cancellationToken)
{
    // Gets the customer associated with the order
    CustomerInfo customer = await customerInfoProvider.GetAsync(
        calculationData.Request.CustomerId, cancellationToken);

    // Checks if a mapping between the customer and a member exists
    bool customerRegistered = customer?.CustomerMemberID > 0;
    if (!customerRegistered)
    {
        return false;
    }

    // Calls the rest of the logic
    return await base.IsApplicable(calculationData, cancellationToken);
}
```

Another common scenario is restricting a promotion to first-time customers who haven’t placed any orders yet:
C#
**Check if a customer is a first-time buyer**
Copy
```
public class FirstTimeBuyerDiscountRule
    : OrderPromotionRule<OrderPromotionRuleProperties,
        PriceCalculationRequest, PriceCalculationResult>
{
    private readonly IInfoProvider<OrderInfo> orderInfoProvider;
    private readonly IHttpContextAccessor httpContextAccessor;

    public FirstTimeBuyerDiscountRule(
        IInfoProvider<CustomerInfo> customerInfoProvider,
        IHttpContextAccessor httpContextAccessor)
    {
        this.httpContextAccessor = httpContextAccessor;
        this.customerInfoProvider = customerInfoProvider;
    }

    public override async Task<bool> IsApplicable(
    IPriceCalculationData<PriceCalculationRequest, PriceCalculationResult> calculationData,
    CancellationToken cancellationToken)
    {

        bool isAuthenticated = httpContextAccessor.HttpContext.User?.Identity?.IsAuthenticated ?? false;
        int customerId = calculationData.Request.CustomerId;
        if (isAuthenticated && (customerId == 0))
        {
            return false;
        }

        // Checks if the customer has any existing orders
        ObjectQuery<OrderInfo> existingOrders = await orderInfoProvider.Get()
            .WhereEquals(nameof(OrderInfo.OrderCustomerID), customerId.Value)
            .TopN(1)
            .GetEnumerableTypedResultAsync(cancellationToken: cancellationToken);

        // Only applies to customers with no previous orders
        return !existingOrders.Any();

        // Checks the base class conditions
        return !await base.IsApplicable(calculationData, cancellationToken)
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
using System.Linq;

using CMS.Commerce;

// Calculates the price
PriceCalculationResult calculationResult = await priceCalculationService.Calculate(request, cancellationToken);

// Gets the applied order promotion
IPriceCalculationPromotionCandidate<OrderPromotionCandidate> appliedOrderPromotion =
    calculationResult.PromotionData.OrderPromotionCandidates
        .FirstOrDefault(p => p.Applied);

if (appliedOrderPromotion != null)
{
    decimal discountAmount = appliedOrderPromotion.PromotionCandidate.OrderDiscountAmount;
    // Use the discount information...
}
```

## Extend promotion candidate objects
You can extend `OrderPromotionCandidate` to include additional data needed for display or business logic.
To access the custom promotion candidate in calculation results, cast the candidate from the `OrderPromotionCandidates` collection:
C#
**Access custom promotion candidate**
Copy
```
// Calculates the price
PriceCalculationResult calculationResult
    = await priceCalculationService.Calculate(request, cancellationToken);

// Gets the applied order promotion and casts to custom type
IPriceCalculationPromotionCandidate<OrderPromotionCandidate> appliedPromotion =
    calculationResult.PromotionData.OrderPromotionCandidates
        .FirstOrDefault(p => p.Applied);

// Checks if the candidate is a custom type and works with the results
if (appliedPromotion?.PromotionCandidate is DisplayableOrderPromotionCandidate displayable)
{
    string label = displayable.DisplayLabel;
    // Process the label...
}
```