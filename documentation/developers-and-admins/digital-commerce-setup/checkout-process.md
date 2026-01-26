---
source: https://docs.kentico.com/documentation/developers-and-admins/digital-commerce-setup/checkout-process
scrape_date: 2026-01-26
---

  * [Home](/documentation)
  * [Developers and admins](/documentation/developers-and-admins)
  * [Digital commerce setup](/documentation/developers-and-admins/digital-commerce-setup)
  * Checkout process 


# Checkout process
**Advanced license required**   
  
Features described on this page require the Xperience by Kentico **Advanced** license tier. 
Most of the checkout process for [digital commerce](/documentation/developers-and-admins/digital-commerce-setup) needs to be implemented by developers. This is because checkout requirements (e.g., payment methods, shipping options, tax handling, order confirmation) can vary greatly between projects. The commerce feature provides the core building blocks, but it is up to the development team to customize the checkout process to match the specific needs of the shop.
To implement a checkout process in your commerce solution, you need to handle the following steps:
  1. [Store products in the shopping cart](#manage-shopping-cart-data)
  2. [Retrieve the current shopping cart](#manage-the-current-shopping-cart)
  3. [Create a new order from the shopping cart](#create-orders-from-shopping-carts)


**Commerce object types**
  * Certain objects (Shopping cart, Order, Order address, Order item, Customer, Customer address) are considered live-site data and are not supported by the [CI/CD](/documentation/developers-and-admins/ci-cd) feature
  * Configuration objects (Order statuses, Shipping methods, Payment methods) support [CI/CD](/documentation/developers-and-admins/ci-cd) and can be deployed across environments
  * All objects can be [extended](/documentation/developers-and-admins/customization/object-types/extend-system-object-types) by adding custom fields


## Manage shopping cart data
Shopping carts are represented by `ShoppingCartInfo` objects in the API. The actual content of a shopping cart is stored in the `ShoppingCartData` string property. You are responsible for implementing the [data model](/documentation/developers-and-admins/digital-commerce-setup/model-product-catalog) and serialization logic for the shopping cart content.
A typical implementation (as in the [Dancing Goat](/documentation/developers-and-admins/installation#available-project-templates) project template) uses a `ShoppingCartDataModel` to represent the cart, containing a collection of `ShoppingCartDataItem` objects for each product in the cart. This example uses a `ProductIdentifier` to represent the item and a `Quantity` property for the amount, but your actual implementation may need to reflect the [content model](/documentation/developers-and-admins/digital-commerce-setup/model-product-catalog) of your product catalog:
C#
**ShoppingCartDataItem.cs**
Copy
```
/// <summary>
/// Represents an item in the shopping cart data model.
/// </summary>
public class ShoppingCartDataItem
{
    /// <summary>
    /// Identifier holding content item identifier and variant identifier if applicable.
    /// </summary>
    public ProductIdentifier ProductIdentifier { get; set; } = new();

    /// <summary>
    /// Quantity of the item in shopping cart.
    /// </summary>
    public int Quantity { get; set; }
}
```

C#
**ShoppingCartDataModel.cs**
Copy
```
/// <summary>
/// Model for shopping cart data stored in ShoppingCartInfo.ShoppingCartData.
/// </summary>
public class ShoppingCartDataModel
{
    /// <summary>
    /// Items inside the shopping cart.
    /// </summary>
    public ICollection<ShoppingCartDataItem> Items { get; set; } = new List<ShoppingCartDataItem>();

    /// <summary>
    /// Coupon codes entered by the customer.
    /// </summary>
    public ICollection<string> CouponCodes { get; set; } = new List<string>();
}
```

To store and retrieve the shopping cart data model from the `ShoppingCartInfo` object, you can implement custom extension methods. You can use a data model and persistence method that fits your requirements; the following example uses JSON serialization for simplicity, but you can use any approach that suits your project.
C#
**ShoppingCartDataModelExtensions.cs**
Copy
```
/// <summary>
/// Extension methods for serializing/deserializing shopping cart data.
/// </summary>
public static class ShoppingCartDataModelExtensions
{
    /// <summary>
    /// Deserializes the shopping cart data model from the shopping cart object.
    /// </summary>
    public static ShoppingCartDataModel GetShoppingCartDataModel(this ShoppingCartInfo shoppingCart)
    {
        if (string.IsNullOrEmpty(shoppingCart?.ShoppingCartData))
        {
            return new ShoppingCartDataModel();
        }

        return JsonSerializer.Deserialize<ShoppingCartDataModel>(shoppingCart.ShoppingCartData)
            ?? new ShoppingCartDataModel();
    }

    /// <summary>
    /// Serializes the shopping cart data model and stores it in the shopping cart object.
    /// </summary>
    public static void StoreShoppingCartDataModel(this ShoppingCartInfo shoppingCart, ShoppingCartDataModel shoppingCartData)
    {
        shoppingCart.ShoppingCartData = JsonSerializer.Serialize(shoppingCartData);
    }
}
```

To update the shopping cart (add, update, or remove items), you can use logic similar to the following. Note that the logic assumes [products stored in the content hub](/documentation/developers-and-admins/digital-commerce-setup/model-product-catalog):
C#
**Update item quantity in shopping cart**
Copy
```
/// <summary>
/// Updates the quantity of an item in the shopping cart.
/// </summary>
public async Task UpdateItemQuantity(int contentItemId, int quantity, bool setAbsolute = false, CancellationToken cancellationToken = default)
{
    var cart = await _shoppingCartRetriever.Get(cancellationToken);
    if (cart == null)
    {
        return;
    }

    var cartData = cart.GetShoppingCartDataModel();

    var item = cartData.Items.FirstOrDefault(x => x.ProductIdentifier.Identifier == contentItemId);

    if (item != null)
    {
        item.Quantity = setAbsolute ? quantity : Math.Max(0, item.Quantity + quantity);

        if (item.Quantity == 0)
        {
            cartData.Items.Remove(item);
        }
    }
    else if (quantity > 0)
    {
        cartData.Items.Add(new ShoppingCartDataItem
        {
            ProductIdentifier = new ProductIdentifier { Identifier = contentItemId },
            Quantity = quantity
        });
    }

    cart.StoreShoppingCartDataModel(cartData);
    cart.Update();
}
```

This approach allows you to flexibly manage the shopping cart’s contents and persist them as a serialized string in the database.
### Configure shopping cart behavior
You can configure the expiration time of shopping carts. The value is used as the expiration time of the shopping cart cookie as well as the time after which any shopping cart is deleted. The default value is 1 day.
**Note** : The [scheduled task](/documentation/developers-and-admins/customization/scheduled-tasks) that deletes shopping carts older than the specified value is executed once per day. Using values of less than 1 day has no effect.
To configure the shopping cart expiration set the `Expiration` property on `ShoppingCartOptions`:
C#
**Program.cs**
Copy
```
using Kentico.Xperience.Commerce;

// ...

WebApplicationBuilder builder = WebApplication.CreateBuilder(args);

// ...

builder.Services.Configure<ShoppingCartOptions>(
    // Sets the expiration time of shopping carts to 30 days
    options => options.Expiration = TimeSpan.FromDays(30)
);
```

### Manage the current shopping cart
The current shopping cart is managed using the `ICurrentShoppingCart*` services, available in the `Kentico.Commerce.Web.Mvc` namespace. These services provide methods to retrieve, create, and delete shopping carts for the current user session.
  * For anonymous users, the shopping cart identifier is stored in a browser cookie.
  * For signed-in [members](/documentation/business-users/members), the shopping cart is associated with the member account, ensuring that the cart content is consistent across devices and sessions.


The actual shopping cart data is persisted in the database. This allows members to access their cart from any device after signing in. You can also [configure](#configure-shopping-cart-behavior) the expiration period after which abandoned shopping carts are deleted.
To work with the current user’s shopping cart, use the following services:
  * `ICurrentShoppingCartRetriever` – use the `Get` method to retrieve the `ShoppingCartInfo` object for the current user. Returns `null` if no cart exists.
  * `ICurrentShoppingCartCreator` – use the `Create` method to create a new `ShoppingCartInfo` object and store its identifier (in a cookie for anonymous users or linked to the member for signed-in users).
  * `ICurrentShoppingCartDiscardHandler` – use the `Discard` method to delete the current user’s shopping cart from the database and remove the identifier.
  * `ICurrentShoppingCartMemberSignInHandler` – use the `MemberSignIn` method to handle the transition from anonymous to member cart during sign-in. If both an anonymous and a member cart exist, the member cart is deleted and the anonymous cart is assigned to the member.


The default behavior of these services can be customized by overriding the respective service implementations in your project.
C#
**Managing the current shopping cart**
Copy
```
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

## Create orders from shopping carts
Use the [order creation service](/documentation/developers-and-admins/digital-commerce-setup/checkout-process/order-creation) (`IOrderCreationService`) and [price calculation service](/documentation/developers-and-admins/digital-commerce-setup/price-calculation) (`IPriceCalculationService`) to create orders from shopping cart data. These services automate the complete order creation workflow:
  * **Customer and address management** – Creates or retrieves customer records and addresses
  * **Price calculation** – Calculates order totals, taxes, shipping costs, and [catalog discounts](/documentation/developers-and-admins/digital-commerce-setup/promotions/catalog-discounts)
  * **Order persistence** – Creates order, order item, and order address records in a database transaction
  * **Notifications** – Sends order confirmation notifications


Before creating orders, ensure that [shipping methods, payment methods](/documentation/developers-and-admins/digital-commerce-setup/shipping-and-payment-methods), and [order statuses](/documentation/developers-and-admins/digital-commerce-setup/configure-order-statuses) are configured in the system.
For implementation details, customization options, and code examples, see:
  * [Implement order creation](/documentation/developers-and-admins/digital-commerce-setup/checkout-process/order-creation) – Complete guide to using `IOrderCreationService`
  * [Implement price calculation](/documentation/developers-and-admins/digital-commerce-setup/price-calculation) – Guide to configuring `IPriceCalculationService`


## Automatically change status of orders
If you have an external system that performs actions that can affect the status of your orders (e.g., shipping company, payment provider), you can automatically update the order status by changing the `OrderOrderStatusID` property of the `OrderInfo` object.
The following example showcases a sample code that receives a confirmation of payment from a payment provider and automatically updates the respective order to reflect this change. You would typically call this code in the callback handler of your third-party payment provider integration. When changing order statuses via the API, the [notifications](/documentation/developers-and-admins/digital-commerce-setup/configure-order-statuses#configure-notifications-for-order-statuses) are sent as usual.
C#
**Change status of an order**
Copy
```
private readonly IInfoProvider<OrderInfo> _orderInfoProvider;
private readonly IInfoProvider<OrderStatusInfo> _orderStatusInfoProvider;

public OrderStatusExamples(
    IInfoProvider<OrderInfo> orderInfoProvider,
    IInfoProvider<OrderStatusInfo> orderStatusInfoProvider)
{
    _orderInfoProvider = orderInfoProvider;
    _orderStatusInfoProvider = orderStatusInfoProvider;
}

/// <summary>
/// Changes the status of an order. Typically called from a payment provider callback.
/// </summary>
public async Task ChangeOrderStatus(int orderId, string newStatusCodeName, CancellationToken cancellationToken = default)
{
    // Retrieves the order data
    var order = await _orderInfoProvider.GetAsync(orderId, cancellationToken);

    // Retrieves data of the new order status
    var newOrderStatus = await _orderStatusInfoProvider.GetAsync(newStatusCodeName, cancellationToken);

    // Sets the new status to the order data
    order.OrderOrderStatusID = newOrderStatus.OrderStatusID;

    // Saves the changes of the order data
    await _orderInfoProvider.SetAsync(order, cancellationToken);
}
```

![]()
[]()[]()
