# Checkout process
  * [ Copy page link ](documentation/developers-and-admins/digital-commerce-setup/checkout-process#) | [Get HelpService ID](documentation/developers-and-admins/digital-commerce-setup/checkout-process#)
Core MVC 5


[✖](documentation/developers-and-admins/digital-commerce-setup/checkout-process# "Close page link panel") [Copy to clipboard](documentation/developers-and-admins/digital-commerce-setup/checkout-process#)
**Advanced license required**   
  
Features described on this page require the Xperience by Kentico **Advanced** license tier. 
Most of the checkout process for [digital commerce](documentation/developers-and-admins/digital-commerce-setup) needs to be implemented by developers. This is because checkout requirements (e.g., payment methods, shipping options, tax handling, order confirmation) can vary greatly between projects. The commerce feature provides the core building blocks, but it is up to the development team to customize the checkout process to match the specific needs of the shop.
To implement a checkout process in your commerce solution, you need to handle the following steps:
  1. [Store products in the shopping cart](documentation/developers-and-admins/digital-commerce-setup/checkout-process#manage-shopping-cart-data)
  2. [Retrieve the current shopping cart](documentation/developers-and-admins/digital-commerce-setup/checkout-process#manage-the-current-shopping-cart)
  3. [Create a new order from the shopping cart](documentation/developers-and-admins/digital-commerce-setup/checkout-process#create-orders-from-shopping-carts)


**Commerce object types**
  * Certain objects (Shopping cart, Order, Order address, Order item, Customer, Customer address) are considered live-site data and are not supported by the [CI/CD](documentation/developers-and-admins/ci-cd) feature
  * Configuration objects (Order statuses, Shipping methods, Payment methods) support [CI/CD](documentation/developers-and-admins/ci-cd) and can be deployed across environments
  * All objects can be [extended](documentation/developers-and-admins/customization/object-types/extend-system-object-types) by adding custom fields


## Manage shopping cart data
Shopping carts are represented by `ShoppingCartInfo` objects in the API. The actual content of a shopping cart is stored in the `ShoppingCartData` string property. You are responsible for implementing the [data model](documentation/developers-and-admins/digital-commerce-setup/model-product-catalog) and serialization logic for the shopping cart content.
A typical implementation (as in the [Dancing Goat](documentation/developers-and-admins/installation#available-project-templates) project template) uses a `ShoppingCartDataModel` to represent the cart, containing a collection of `ShoppingCartDataItem` objects for each product in the cart. This example uses three properties to represent the item ID, quantity, and [variant ID](documentation/developers-and-admins/digital-commerce-setup/model-product-catalog#product-variants), but your actual implementation may need to reflect the [content model](documentation/developers-and-admins/digital-commerce-setup/model-product-catalog) of your product catalog:
C#
**Example - ShoppingCartDataItem.cs**
Copy
```
// Represents a single item in the shopping cart
public class ShoppingCartDataItem
{
    public int ContentItemId { get; set; }
    public int Quantity { get; set; }
    public int? VariantId { get; set; }
}
```

C#
**Example - ShoppingCartDataModel.cs**
Copy
```
// Represents the shopping cart data model containing all items
public class ShoppingCartDataModel
{
    public ICollection<ShoppingCartDataItem> Items { get; init; } = new List<ShoppingCartDataItem>();
}
```

To store and retrieve the shopping cart data model from the `ShoppingCartInfo` object, you can implement custom extension methods. You can use a data model and persistence method that fits your requirements; the following example uses JSON serialization for simplicity, but you can use any approach that suits your project.
C#
**Example - ShoppingCartDataModelExtensions.cs**
Copy
```
// Provides extension methods for serializing and deserializing the shopping cart data
public static class ShoppingCartDataModelExtensions
{
    public static ShoppingCartDataModel GetShoppingCartDataModel(this ShoppingCartInfo shoppingCart)
    {
        // Deserializes the ShoppingCartData string into a ShoppingCartDataModel instance
        return (string.IsNullOrEmpty(shoppingCart?.ShoppingCartData) ? null : JsonSerializer.Deserialize<ShoppingCartDataModel>(shoppingCart.ShoppingCartData))
            ?? new ShoppingCartDataModel();
    }

    public static void StoreShoppingCartDataModel(this ShoppingCartInfo shoppingCart, ShoppingCartDataModel shoppingCartData)
    {
        // Serializes the ShoppingCartDataModel instance into a JSON string and stores it in ShoppingCartData
        shoppingCart.ShoppingCartData = JsonSerializer.Serialize(shoppingCartData);
    }
}
```

To update the shopping cart (add, update, or remove items), you can use logic similar to the following, as seen in `DancingGoatShoppingCartController.cs`:
C#
**Example - DancingGoatShoppingCartController.cs (excerpt)**
Copy
```
// An instance of IInfoProvider<ShoppingCartInfo> (e.g., obtained via dependency injection)
private readonly IInfoProvider<ShoppingCartInfo> shoppingCartInfoProvider;

// Updates the quantity of a product in the shopping cart, or adds/removes the item as needed
private void UpdateQuantity(ShoppingCartInfo shoppingCart,
                            int contentItemId,
                            int quantity,
                            int? variantId,
                            bool setAbsoluteValue = false)
{
    // Retrieves the current shopping cart data model
    var shoppingCartData = shoppingCart.GetShoppingCartDataModel();

    // Finds the product item in the cart by content item ID and variant ID
    var productItem = shoppingCartData.Items.FirstOrDefault(
                                x => x.ContentItemId == contentItemId && x.VariantId == variantId
                                );
    if (productItem != null)
    {
        // Updates the quantity (absolute or relative)
        productItem.Quantity = setAbsoluteValue ? quantity : Math.Max(0, productItem.Quantity + quantity);
        // Removes the item if the quantity is zero
        if (productItem.Quantity == 0)
        {
            shoppingCartData.Items.Remove(productItem);
        }
    }
    else if (quantity > 0)
    {
        // Adds a new item to the cart if it does not exist and quantity is positive
        shoppingCartData.Items.Add(new ShoppingCartDataItem {
                                        ContentItemId = contentItemId,
                                        Quantity = quantity,
                                        VariantId = variantId
                                        });
    }

    // Serializes and stores the updated shopping cart data model
    shoppingCart.StoreShoppingCartDataModel(shoppingCartData);

    // Stores the updated shopping cart object to the database
    await shoppingCartInfoProvider.SetAsync(shoppingCart);
}
```

This approach allows you to flexibly manage the shopping cart’s contents and persist them as a serialized string in the database.
For a complete example, see the [Dancing Goat](documentation/developers-and-admins/installation#available-project-templates) project template’s shopping cart controller and related models.
### Manage the current shopping cart
The current shopping cart is managed using the `ICurrentShoppingCart*` services, available in the `Kentico.Commerce.Web.Mvc` namespace. These services provide methods to retrieve, create, and delete shopping carts for the current user session.
  * For anonymous users, the shopping cart identifier is stored in a browser cookie.
  * For signed-in [members](documentation/business-users/members), the shopping cart is associated with the member account, ensuring that the cart content is consistent across devices and sessions.


The actual shopping cart data is persisted in the database. This allows members to access their cart from any device after signing in. You can also [configure](documentation/developers-and-admins/digital-commerce-setup#configure-shopping-cart) the expiration period after which abandoned shopping carts are deleted.
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
// An instance of ICurrentShoppingCartService (e.g., obtained via dependency injection)
private readonly ICurrentShoppingCartService currentShoppingCartService;

// Retrieves the shopping cart for the current user
ShoppingCartInfo shoppingCart = await currentShoppingCartService.Get();

// If there is no shopping cart for the current user, creates a new shopping cart
shoppingCart ??= await currentShoppingCartService.Create(null);

// Accesses the shopping cart content (e.g., products in the cart) using
// the extensions methods from the previous section.
var cartData = shoppingCart.GetShoppingCartDataModel();

// Deletes the shopping cart for the current user if needed
await currentShoppingCartService.Delete();
```

## Create orders from shopping carts
Use the [order creation service](documentation/developers-and-admins/digital-commerce-setup/checkout-process/order-creation) (`IOrderCreationService`) and [price calculation service](documentation/developers-and-admins/digital-commerce-setup/price-calculation) (`IPriceCalculationService`) to create orders from shopping cart data. These services automate the complete order creation workflow:
  * **Customer and address management** – Creates or retrieves customer records and addresses
  * **Price calculation** – Calculates order totals, taxes, shipping costs, and [catalog discounts](documentation/developers-and-admins/digital-commerce-setup/promotions/catalog-discounts)
  * **Order persistence** – Creates order, order item, and order address records in a database transaction
  * **Notifications** – Sends order confirmation notifications


Before creating orders, ensure that [shipping methods, payment methods](documentation/developers-and-admins/digital-commerce-setup/shipping-and-payment-methods), and [order statuses](documentation/developers-and-admins/digital-commerce-setup/configure-order-statuses) are configured in the system.
For implementation details, customization options, and code examples, see:
  * [Implement order creation](documentation/developers-and-admins/digital-commerce-setup/checkout-process/order-creation) – Complete guide to using `IOrderCreationService`
  * [Implement price calculation](documentation/developers-and-admins/digital-commerce-setup/price-calculation) – Guide to configuring `IPriceCalculationService`
  * [Implement catalog discounts](documentation/developers-and-admins/digital-commerce-setup/promotions/catalog-discounts) – Guide to creating custom promotion rules


## Automatically change status of orders
If you have an external system that performs actions that can affect the status of your orders (e.g., shipping company, payment provider), you can automatically update the order status by changing the `OrderOrderStatusID` property of the `OrderInfo` object.
The following example showcases a sample code that receives a confirmation of payment from a payment provider and automatically updates the respective order to reflect this change. You would typically call this code in the callback handler of your third-party payment provider integration. When changing order statuses via the API, the [notifications](documentation/developers-and-admins/digital-commerce-setup/configure-order-statuses#configure-notifications-for-order-statuses) are sent as usual.
C#
**Change status of an order**
Copy
```
// Instance of provider services (e.g., obtained via dependency injection)
private readonly IInfoProvider<OrderInfo> orderInfoProvider;
private readonly IInfoProvider<OrderStatusInfo> orderStatusInfoProvider;

// Order ID received from a payment provider
int orderId;

// Retrieves the order data
OrderInfo order = await orderInfoProvider.GetAsync(orderId, cancellationToken);

// Retrieves data of the new order status
OrderStatusInfo newOrderStatus = await orderStatusInfoProvider.GetAsync("PaymentReceived", cancellationToken);

// Sets the new status to the order data
order.OrderOrderStatusID = newOrderStatus.OrderStatusID;

// Saves the changes of the order data
await orderInfoProvider.SetAsync(order, cancellationToken);
```