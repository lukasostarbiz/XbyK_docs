---
source: https://docs.kentico.com/documentation/developers-and-admins/digital-commerce-setup/checkout-process/order-creation
scrape_date: 2026-01-22
---

  * [Home](/documentation)
  * [Developers and admins](/documentation/developers-and-admins)
  * [Digital commerce setup](/documentation/developers-and-admins/digital-commerce-setup)
  * [Checkout process](/documentation/developers-and-admins/digital-commerce-setup/checkout-process)
  * Order creation 


# Order creation
**Advanced license required**   
  
Features described on this page require the Xperience by Kentico **Advanced** license tier. 
The system provides a streamlined way to create orders from shopping cart data using `IOrderCreationService`. The API automates the entire order creation workflow, including customer management, price calculation, and order persistence.
`IOrderCreationService` handles the complete order creation process. The API:
  1. **Manages customers and addresses** – Creates or updates customer records and their addresses based on the order data.
  2. **Calculates prices** – Integrates with the [price calculation API](/documentation/developers-and-admins/digital-commerce-setup/price-calculation) to calculate order totals, taxes, and shipping costs.
  3. **Creates order records** – Generates and persists the order, order items, and order addresses in the database.
  4. **Sends notifications** – Triggers external and internal order notifications for corresponding order states.


## Prerequisites
The order creation API uses the [price calculation API](/documentation/developers-and-admins/digital-commerce-setup/price-calculation) internally to calculate order totals, taxes, and shipping costs. Before implementing order creation, you must implement the mandatory components for price calculation:
  * **Product data retriever** (`IProductDataRetriever<ProductIdentifier, ProductData>`) – provides product information and pricing data from your product catalog.
  * **Tax calculation step** (`ITaxPriceCalculationStep`) – calculates applicable taxes (the default implementation does not add any taxes).


See [Implement price calculation](/documentation/developers-and-admins/digital-commerce-setup/price-calculation) for complete implementation details and examples.
Without a properly configured price calculation service, the order creation process cannot calculate order totals and will fail when you attempt to create an order.
## Quick start
This example shows the minimal required setup to use the API:
C#
**Checkout controller**
Copy
```
public class CheckoutController : Controller
{
    private readonly IOrderCreationService<OrderData, PriceCalculationRequest, PriceCalculationResult, AddressDto> orderCreationService;
    private readonly UserManager<ApplicationUser> userManager;


    // Inject the order creation service
    public CheckoutController(
        IOrderCreationService<OrderData, PriceCalculationRequest, PriceCalculationResult, AddressDto> orderCreationService,
        UserManager<ApplicationUser> userManager)
    {
        this.orderCreationService = orderCreationService;
    }

    public async Task<IActionResult> CreateOrder(CancellationToken cancellationToken)
    {
        // Prepare order data from your checkout form
        var orderData = new OrderData
        {
            // Gets the authenticated user, if one exists for the current order
            MemberId = (await userManager.GetUserAsync(User))?.Id,
            // Ensure a unique order ID
            OrderNumber = 1,
            // Gets the rest of the order data from the request
            LanguageName = "en",
            BillingAddress = new AddressDto
            {
                FirstName = "John",
                LastName = "Doe",
                Email = "john@example.com",
                Line1 = "123 Main St",
                City = "New York",
                Zip = "10001",
                CountryID = 1,
                StateID = 1
            },
            // Uses the billing address for shipping
            ShippingAddress = null,
            // Passed to IPriceCalculationService to calculate order totals
            OrderItems = new[]
            {
                new OrderItem
                {
                    ProductIdentifier = new ProductIdentifier { Identifier = 123 },
                    Quantity = 2
                }
            }
        };

        // Creates the order
        int orderId = await orderCreationService.CreateOrder(orderData, cancellationToken);

        // Redirects users to a confirmation page
        return RedirectToAction("Confirmation", new { orderId });
    }
}
```

The service automatically:
  * Creates or retrieves the appropriate customer object.
  * Calculates final order prices using `IPriceCalculationService`.
  * Creates the order and all related entities.
  * Sends order confirmation [notifications](/documentation/developers-and-admins/digital-commerce-setup/configure-order-statuses#configure-notifications-for-order-statuses).


## Key concepts
### Order creation flow
When you call `CreateOrder`, the service executes the following steps in order:
  1. **Validation** – validates that order data contains required fields (billing address, at least one order item).
  2. **Customer creation** – creates or retrieves customer entities based on member ID or email address.
  3. **Customer address creation** – creates missing customer addresses (with duplicate detection).
  4. **Price calculation** – calculates order totals using the [price calculation API](/documentation/developers-and-admins/digital-commerce-setup/price-calculation).
  5. **Order entity creation** – creates the order, order addresses, and order items in a database transaction.
  6. **Notification** – sends order confirmation [notifications](/documentation/developers-and-admins/digital-commerce-setup/configure-order-statuses#configure-notifications-for-order-statuses).


**Transaction scope**
Only order entities (`OrderInfo`, `OrderAddress`, `OrderItem`) are created within a database transaction. If order creation fails, these entities are rolled back.
Customer entities (`CustomerInfo`, `CustomerAddressInfo`) are persisted outside the transaction. If order creation fails after customer entities are created, those customer records remain in the database even though no order is created.
[![Order creation flow](/docsassets/documentation/order-creation/order-creation-flow.drawio.svg)](/docsassets/documentation/order-creation/order-creation-flow.drawio.svg)
### Order data model
Order data is represented by the `IOrderData` interface and its implementation `OrderData`. It contains:
  * **Customer information** – Member ID (for authenticated users).
  * **Order items** – Products and quantities using `IOrderItem<ProductIdentifier>`.
  * **Addresses** – Billing and shipping addresses as `AddressDto` objects.
  * **Payment and shipping** – Selected payment and shipping method IDs.
  * **Language** – Language name for product data localization.
  * **Order number** – Unique identifier for the order.


C#
**Basic order data structure**
Copy
```
var orderData = new OrderData
{
    MemberId = currentMemberId,
    OrderNumber = "ORD-2024-0001",
    LanguageName = "en",
    BillingAddress = new AddressDto { /* address fields */ },
    ShippingAddress = new AddressDto { /* address fields */ },
    PaymentMethodId = 1,
    ShippingMethodId = 2,
    OrderItems = new[]
    {
        new OrderItem
        {
            ProductIdentifier = new ProductIdentifier { Identifier = 123 },
            Quantity = 2
        }
    }
};
```

### Address handling
Addresses are represented by the `AddressDto` record, which includes:
  * Contact information – First name, last name, company, email, phone
  * Location details – Address lines, city, postal code, country, and state


The service automatically:
  * Creates customer addresses if they don’t already exist
  * Compares addresses to avoid duplicates (case-insensitive comparison, including [custom fields](#address-mapping))
  * Uses shipping address data as primary source for customer information when available


**Notes**
The shipping address is optional. If `ShippingAddress` is `null`, the service assumes the billing address should be used for both billing and shipping purposes. Only the billing address will be created as an order address in this case.
**Address comparison logic** : The service uses case-insensitive, trimmed comparison when checking for duplicate customer addresses. For example, “123 Main St” and “123 MAIN ST” are considered the same address.
### Customer management
The service handles both [authenticated](/documentation/business-users/members) and anonymous users:
  * **Authenticated users (members)** – Retrieves existing customer records by [member](/documentation/business-users/members) ID or creates new ones if the member doesn’t have a customer record yet.
  * **Anonymous users (guests)** – Looks up existing customers by email address from the order data, or creates a new customer record if no match is found.


**Customer data updates**
When an existing customer is found (by member ID or email), the service uses the existing customer record without updating customer information fields (such as first name, last name, email, or phone). Customer data is only populated when creating a new customer record.
If you need to update customer information during order creation, implement a custom `ICustomerInfoMapper<TOrderData>` to modify the customer record before it’s used in the order. See [Customer information mapping](#customer-information-mapping).
[Customer addresses](#address-handling) are stored separately and reused across orders when they match existing addresses.
### Price calculation integration
The order creation API uses `IPriceCalculationService` to compute order totals. You can customize the price calculation by:
  * Implementing `IPriceCalculationRequestMapper<TPriceCalculationRequest, TOrderData>` to add custom data to calculation requests.
  * Using custom calculation steps to modify pricing logic.


For more information on price calculation, see [Price calculation](/documentation/developers-and-admins/digital-commerce-setup/price-calculation).
### Promotion handling
The order creation service automatically persists promotion information calculated during price calculation:
  * **Catalog promotions** – Product-level discounts calculated during price calculation are saved as `OrderPromotionInfo` records linked to specific order items.
  * **Promotion candidates** – The selected promotion candidate (with `Applied = true`) is extracted from the price calculation result and stored with the order.
  * **Discount amounts** – The `UnitPriceDiscountAmount` from the promotion candidate is multiplied by item quantity and stored in `OrderPromotionDiscountAmount`.


You don’t need to manually handle promotion data during order creation – the service extracts it from the `IPriceCalculationResult` and creates appropriate order promotion records.
For information on implementing catalog discount rules, see [Implement catalog discounts](/documentation/developers-and-admins/digital-commerce-setup/promotions/catalog-discounts).
## Create orders
To use the service in your checkout process:
  1. Inject `IOrderCreationService<TOrderData, TPriceCalculationRequest, TPriceCalculationResult, TAddressDto>` into your controller or service.
  2. Prepare the `OrderData` object with cart items, addresses, and selected methods.
  3. Call `CreateOrder` to process the order.


C#
**Example - Basic order creation**
Copy
```
public class CheckoutController : Controller
{
    private readonly IOrderCreationService<OrderData, PriceCalculationRequest,
        PriceCalculationResult, AddressDto> orderCreationService;

    public CheckoutController(
        IOrderCreationService<OrderData, PriceCalculationRequest,
            PriceCalculationResult, AddressDto> orderCreationService)
    {
        this.orderCreationService = orderCreationService;
    }

    public async Task<IActionResult> ConfirmOrder(
        CustomerViewModel customer,
        CustomerAddressViewModel billingAddress,
        ShippingAddressViewModel shippingAddress,
        int paymentMethodId,
        int shippingMethodId,
        CancellationToken cancellationToken)
    {
        // Retrieve shopping cart items (implementation depends on your cart model)
        var cartItems = await GetShoppingCartItems(cancellationToken);

        // Prepare order data
        var orderData = new OrderData
        {
            MemberId = User.GetMemberId(), // null for anonymous users
            OrderNumber = await GenerateOrderNumber(cancellationToken),
            LanguageName = currentLanguage,
            BillingAddress = MapToAddressDto(billingAddress, customer),
            ShippingAddress = shippingAddress.IsSameAsBilling
                ? null
                : MapToAddressDto(shippingAddress, customer),
            PaymentMethodId = paymentMethodId,
            ShippingMethodId = shippingMethodId,
            OrderItems = cartItems.Select(item => new OrderItem
            {
                ProductIdentifier = item.ProductIdentifier,
                Quantity = item.Quantity
            })
        };

        // Create the order
        int orderId = await orderCreationService.CreateOrder(orderData, cancellationToken);

        // Clear the shopping cart and redirect to confirmation page
        await ClearShoppingCart(cancellationToken);

        return RedirectToAction("OrderConfirmation", new { orderId });
    }

    private AddressDto MapToAddressDto(
        CustomerAddressViewModel address,
        CustomerViewModel customer)
    {
        return new AddressDto
        {
            FirstName = customer.FirstName,
            LastName = customer.LastName,
            Company = customer.Company,
            Email = customer.Email,
            Phone = customer.PhoneNumber,
            Line1 = address.Line1,
            Line2 = address.Line2,
            City = address.City,
            Zip = address.PostalCode,
            CountryID = address.CountryId,
            StateID = address.StateId
        };
    }
}
```

You have successfully created orders from shopping cart data. Customer records, addresses, and order items are persisted in the database, and order confirmation notifications are sent.
## Customization points
The API provides several interfaces that allow you to customize the order creation process without modifying core functionality.
### Customer information mapping
Implement `ICustomerInfoMapper<TOrderData>` to add [custom fields](/documentation/developers-and-admins/customization/object-types/extend-system-object-types) to customer records:
C#
**Example - Custom customer mapper**
Copy
```
public class CustomCustomerInfoMapper : ICustomerInfoMapper<CustomOrderData>
{
    public CustomerInfo PopulateInfo(CustomerInfo customerInfo, CustomOrderData orderData)
    {
        // Add custom logic to populate additional customer fields.
        // Note that the example uses a customized
        // CustomOrderData object extended with additional fields.
        // Example: customerInfo.SetValue("CustomerLoyaltyPoints", orderData.LoyaltyPoints);

        return customerInfo;
    }
}

// Register in the application IoC container
builder.Services.AddTransient<ICustomerInfoMapper<CustomOrderData>, CustomCustomerInfoMapper>();
```

### Order information mapping
Implement `IOrderInfoMapper<TOrderData, TPriceCalculationResult>` to add [custom fields](/documentation/developers-and-admins/customization/object-types/extend-system-object-types) to order records:
C#
**Example - Custom order mapper**
Copy
```
public class CustomOrderInfoMapper : IOrderInfoMapper<CustomOrderData, PriceCalculationResult>
{
    public OrderInfo PopulateInfo(
        OrderInfo orderInfo,
        CustomOrderData orderData,
        PriceCalculationResult calculationResult)
    {
        // Add custom logic to populate additional order fields
        // Note that the example uses a customized
        // CustomOrderData object extended with additional fields.
        // Example: orderInfo.SetValue("OrderDiscountCode", orderData.DiscountCode);

        return orderInfo;
    }
}

// Register in the application IoC container
builder.Services.AddTransient<IOrderInfoMapper<CustomOrderData, PriceCalculationResult>,
    CustomOrderInfoMapper>();
```

### Address mapping
Implement `ICustomerAddressInfoMapper<TAddressDto>` or `IOrderAddressInfoMapper<TAddressDto>` to add [custom fields](/documentation/developers-and-admins/customization/object-types/extend-system-object-types) to address records:
C#
**Example - Custom address mapper**
Copy
```
public class CustomAddressInfoMapper : IOrderAddressInfoMapper<CustomAddressDto>
{
    public OrderAddressInfo PopulateInfo(
        OrderAddressInfo orderAddressInfo,
        CustomAddressDto addressDto)
    {
        // Add custom logic to populate additional address fields
        // Note that the example uses a customized
        // CustomAddressDto object extended with additional fields.
        // Example: orderAddressInfo.SetValue("AddressNotes", addressDto.DeliveryNotes);

        return orderAddressInfo;
    }

    public AddressDto PopulateDto(AddressDto addressDto, OrderAddressInfo orderAddressInfo)
    {
        // Reverse mapping if needed for reading order addresses
        return addressDto;
    }
}

// Register in the application IoC container
builder.Services.AddTransient<IOrderAddressInfoMapper<CustomAddressDto>, CustomAddressInfoMapper>();
```

### Order item mapping
Implement `IOrderItemInfoMapper<TOrderData, TPriceCalculationResult>` to add [custom fields](/documentation/developers-and-admins/customization/object-types/extend-system-object-types) to order items:
C#
**Example - Custom order item mapper**
Copy
```
public class CustomOrderItemInfoMapper :
    IOrderItemInfoMapper<CustomOrderData, PriceCalculationResult>
{
    public OrderItemInfo PopulateInfo(
        OrderItemInfo orderItemInfo,
        CustomOrderData orderData,
        PriceCalculationResult calculationResult,
        ProductIdentifier productIdentifier)
    {
        // Add custom logic to populate additional order item fields
        // Note that the example uses a customized
        // CustomOrderData object extended with additional fields.
        // Example: orderItemInfo.SetValue("ItemWarrantyMonths", 24);

        return orderItemInfo;
    }
}

// Register in the application IoC container
builder.Services.AddTransient<IOrderItemInfoMapper<CustomOrderData, PriceCalculationResult>,
    CustomOrderItemInfoMapper>();
```

### Price calculation customization
Implement `IPriceCalculationRequestMapper<TPriceCalculationRequest, TOrderData>` to add custom data to [price calculation requests](/documentation/developers-and-admins/digital-commerce-setup/price-calculation/customization#extend-data-transfer-objects):
C#
**Example - Custom calculation request mapper**
Copy
```
public class CustomCalculationRequestMapper :
    IPriceCalculationRequestMapper<PriceCalculationRequest, OrderData>
{
    public PriceCalculationRequest PopulateCalculationRequest(
        PriceCalculationRequest calculationRequest,
        OrderData orderData)
    {
        // Add custom logic to modify the calculation request
        // Example: Add customer tier information, etc.

        return calculationRequest;
    }
}

// Register in the dependency injection container
builder.Services
    .AddTransient<IPriceCalculationRequestMapper<PriceCalculationRequest, rderData>,
        CustomCalculationRequestMapper>();
```

![]()
[]()[]()
