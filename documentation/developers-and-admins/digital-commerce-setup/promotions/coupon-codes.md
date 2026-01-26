---
source: https://docs.kentico.com/documentation/developers-and-admins/digital-commerce-setup/promotions/coupon-codes
scrape_date: 2026-01-26
---

  * [Home](/documentation)
  * [Developers and admins](/documentation/developers-and-admins)
  * [Digital commerce setup](/documentation/developers-and-admins/digital-commerce-setup)
  * [Promotions](/documentation/developers-and-admins/digital-commerce-setup/promotions)
  * Coupon codes 


# Coupon codes
**Advanced license required**   
  
Features described on this page require the Xperience by Kentico **Advanced** license tier. 
Coupon codes (also called discount codes or promo codes) allow you to require customers to enter a specific code to receive a promotion discount. This gives marketers control over when and how discounts are applied, enabling targeted campaigns and limited-time offers.
Coupon codes are not a separate type of discount. They are an **additional redemption requirement** that can be configured for any [catalog discount](/documentation/developers-and-admins/digital-commerce-setup/promotions/catalog-discounts) or [order discount](/documentation/developers-and-admins/digital-commerce-setup/promotions/order-discounts). When a promotion requires a coupon code, it is only applied if the customer provides the matching code during checkout. To learn how to create promotions, see [Add a coupon code to a promotion](/documentation/business-users/manage-commerce-stores#add-a-coupon-code-to-a-promotion).
Discount codes are **case-insensitive**. Customers can enter _winter20_ , _WINTER20_ , or _Winter20_ and all will match a promotion configured with the code _WINTER20_.
## Pass coupon codes to price calculation
For coupon-based promotions to work, you must pass the customer’s entered coupon codes to [price calculation](/documentation/developers-and-admins/digital-commerce-setup/price-calculation/implementation). This is done through the `CouponCodes` property on the price calculation request.
If you do not set the `CouponCodes` property on your `PriceCalculationRequest`, promotions that require coupon codes are **never evaluated** and will not apply to the cart, even if the customer has entered valid codes. Storing coupon codes in your cart data model is not sufficient on its own.
### Add coupon codes to the calculation request
When calling the price calculation service, include any coupon codes the customer has entered:
C#
**Pass coupon codes to price calculation**
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

The price calculation pipeline automatically checks each promotion’s coupon requirements. Promotions requiring a coupon code are only evaluated if a matching code is present in the `CouponCodes` collection.
### Access applied coupon codes from calculation results
You can access the actually applied coupon codes from the price calculation result through the `PromotionData` property:
C#
**Access coupon codes from calculation result**
Copy
```
/// <summary>
/// Demonstrates how to access applied coupon codes from the price calculation result.
/// Coupon codes are available in the PromotionData of the calculation result.
/// </summary>
public void AccessAppliedCouponCodes(PriceCalculationResult calculationResult)
{
    // Access coupon codes from catalog promotions (per-item discounts)
    foreach (var item in calculationResult.Items)
    {
        var appliedCatalogPromotion = item.PromotionData.CatalogPromotionCandidates
            .FirstOrDefault(candidate => candidate.Applied);

        if (appliedCatalogPromotion != null)
        {
            // CouponCode property contains the code that triggered the promotion,
            // or null if the promotion was applied automatically
            string? couponCode = appliedCatalogPromotion.CouponCode;
        }
    }

    // Access coupon codes from order promotions
    var appliedOrderPromotion = calculationResult.PromotionData.OrderPromotionCandidates
        .FirstOrDefault(candidate => candidate.Applied);

    if (appliedOrderPromotion != null)
    {
        // CouponCode property contains the code that triggered the promotion,
        // or null if the promotion was applied automatically
        string? couponCode = appliedOrderPromotion.CouponCode;
    }
}
 



 


```

The `CouponCode` property on applied promotion candidates contains the coupon code that triggered the promotion, or `null` if the promotion was applied automatically.
### Pass coupon codes to order creation
When creating an order, include the coupon codes so they are passed to price calculation during order creation:
C#
**Pass coupon codes to order creation**
Copy
```
/// <summary>
/// Creates order data with coupon codes for order creation.
/// </summary>
public OrderData CreateOrderDataWithCoupons(
    ShoppingCartDataModel cart,
    int memberId,
    string orderNumber,
    AddressDto billingAddress,
    int paymentMethodId,
    int shippingMethodId)
{
    OrderData orderData = new OrderData
    {
        MemberId = memberId,
        OrderNumber = orderNumber,
        LanguageName = "en",
        BillingAddress = billingAddress,
        PaymentMethodId = paymentMethodId,
        ShippingMethodId = shippingMethodId,
        // Include the coupon codes so they are passed to price calculation
        // during order creation and persisted with the order
        CouponCodes = cart.CouponCodes,
        OrderItems = cart.Items.Select(item => new OrderItem
        {
            ProductIdentifier = item.ProductIdentifier,
            Quantity = item.Quantity
        }).ToList()
    };

    return orderData;
}
 
 
 


```

## Implement coupon code entry in the shopping cart
To allow customers to enter coupon codes on your live site, you need to:
  1. Store entered coupon codes in your shopping cart data model.
  2. Provide a live site UI for entering and removing codes in your store.
  3. Pass the codes to price calculation.


### Store coupon codes in the cart
Add a collection to store coupon codes in your [shopping cart](/documentation/developers-and-admins/digital-commerce-setup/checkout-process#manage-the-current-shopping-cart) data model:
C#
**Shopping cart data model with coupon codes**
Copy
```
/// <summary>
/// Coupon codes entered by the customer.
/// </summary>
public ICollection<string> CouponCodes { get; set; } = new List<string>();
 


```

### Handle coupon code actions
Create controller actions to add coupon codes:
C#
**Controller actions for coupon codes**
Copy
```
/// <summary>
/// Adds a coupon code to the shopping cart.
/// </summary>
[HttpPost]
public async Task<IActionResult> AddCoupon(string couponCode, CancellationToken cancellationToken = default)
{
    Console.WriteLine($"Applying {couponCode}");

    if (string.IsNullOrWhiteSpace(couponCode))
    {
        return RedirectToAction(nameof(Index));
    }

    // Calls a helper service to handle coupon operations
    await _shoppingCartService.AddCouponCode(couponCode.Trim(), cancellationToken);

    return RedirectToAction(nameof(Index));
}
 


```

C#
**ShoppingCartService.AddCouponCode**
Copy
```
private readonly ICurrentShoppingCartRetriever _shoppingCartRetriever;
private readonly ICurrentShoppingCartCreator _shoppingCartCreator;
private readonly ICurrentShoppingCartDiscardHandler _shoppingCartDiscardHandler;
private readonly ICurrentShoppingCartMemberSignInHandler _shoppingCartMemberSignInHandler;

public ShoppingCartService(
    ICurrentShoppingCartRetriever shoppingCartRetriever,
    ICurrentShoppingCartCreator shoppingCartCreator,
    ICurrentShoppingCartDiscardHandler shoppingCartDiscardHandler,
    ICurrentShoppingCartMemberSignInHandler shoppingCartMemberSignInHandler)
{
    _shoppingCartRetriever = shoppingCartRetriever;
    _shoppingCartCreator = shoppingCartCreator;
    _shoppingCartDiscardHandler = shoppingCartDiscardHandler;
    _shoppingCartMemberSignInHandler = shoppingCartMemberSignInHandler;
}

/// <summary>
/// Adds a coupon code to the shopping cart.
/// </summary>
public async Task AddCouponCode(string couponCode, CancellationToken cancellationToken = default)
{
    var cart = await GetOrCreateCart(cancellationToken);
    var cartData = cart.GetShoppingCartDataModel();

    // Check if the code is already applied (case-insensitive)
    if (!cartData.CouponCodes.Contains(couponCode, StringComparer.OrdinalIgnoreCase))
    {
        cartData.CouponCodes.Add(couponCode);
        cart.StoreShoppingCartDataModel(cartData);
        cart.Update();
    }
}

/// <summary>
/// Gets the current shopping cart or creates a new one if it doesn't exist
/// using <see cref="ICurrentShoppingCartRetriever"/> and <see cref="ICurrentShoppingCartCreator"/>.
/// </summary>
public async Task<ShoppingCartInfo> GetOrCreateCart(CancellationToken cancellationToken = default)
{
    var cart = await _shoppingCartRetriever.Get(cancellationToken);
    cart ??= await _shoppingCartCreator.Create(cancellationToken);
    return cart;
}
 
 
 
 
 
 


```

And to remove coupon codes:
C#
**Controller actions for coupon codes**
Copy
```
/// <summary>
/// Removes a coupon code from the shopping cart.
/// </summary>
[HttpPost]
public async Task<IActionResult> RemoveCoupon(string couponCode, CancellationToken cancellationToken = default)
{
    if (string.IsNullOrWhiteSpace(couponCode))
    {
        return RedirectToAction(nameof(Index));
    }

    // Calls a helper service to handle coupon operations
    await _shoppingCartService.RemoveCouponCode(couponCode.Trim(), cancellationToken);

    return RedirectToAction(nameof(Index));
}
 


```

C#
**ShoppingCartService.RemoveCouponCode**
Copy
```
private readonly ICurrentShoppingCartRetriever _shoppingCartRetriever;
private readonly ICurrentShoppingCartCreator _shoppingCartCreator;
private readonly ICurrentShoppingCartDiscardHandler _shoppingCartDiscardHandler;
private readonly ICurrentShoppingCartMemberSignInHandler _shoppingCartMemberSignInHandler;

public ShoppingCartService(
    ICurrentShoppingCartRetriever shoppingCartRetriever,
    ICurrentShoppingCartCreator shoppingCartCreator,
    ICurrentShoppingCartDiscardHandler shoppingCartDiscardHandler,
    ICurrentShoppingCartMemberSignInHandler shoppingCartMemberSignInHandler)
{
    _shoppingCartRetriever = shoppingCartRetriever;
    _shoppingCartCreator = shoppingCartCreator;
    _shoppingCartDiscardHandler = shoppingCartDiscardHandler;
    _shoppingCartMemberSignInHandler = shoppingCartMemberSignInHandler;
}

/// <summary>
/// Removes a coupon code from the shopping cart.
/// Implementation-dependent based on the site's shopping cart model.
/// </summary>
public async Task RemoveCouponCode(string couponCode, CancellationToken cancellationToken = default)
{
    var cart = await _shoppingCartRetriever.Get(cancellationToken);
    if (cart == null)
    {
        return;
    }

    var cartData = cart.GetShoppingCartDataModel();

    // Find and remove the coupon code
    var codeToRemove = cartData.CouponCodes
        .FirstOrDefault(c => string.Equals(c, couponCode, StringComparison.OrdinalIgnoreCase));

    // Updates the cart in the database
    if (codeToRemove != null)
    {
        cartData.CouponCodes.Remove(codeToRemove);
        cart.StoreShoppingCartDataModel(cartData);
        cart.Update();
    }
}
 
 
 
 
 
 


```

### Display coupon code UI
Add a form for entering coupon codes and display applied codes. A basic interface handling coupon actions should include:
  * A form with a text input field and a submit button where customers can enter a discount code.
  * A list displaying all currently applied coupon codes from the shopping cart.
  * An option to remove applied coupons.


![]()
[]()[]()
