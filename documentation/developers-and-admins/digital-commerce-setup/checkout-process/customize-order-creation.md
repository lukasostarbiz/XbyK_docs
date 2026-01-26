---
source: https://docs.kentico.com/documentation/developers-and-admins/digital-commerce-setup/checkout-process/customize-order-creation
scrape_date: 2026-01-26
---

  * [Home](/documentation)
  * [Developers and admins](/documentation/developers-and-admins)
  * [Digital commerce setup](/documentation/developers-and-admins/digital-commerce-setup)
  * [Checkout process](/documentation/developers-and-admins/digital-commerce-setup/checkout-process)
  * Customize order creation 


# Customize order creation
**Advanced license required**   
  
Features described on this page require the Xperience by Kentico **Advanced** license tier. 
The [order creation service](/documentation/developers-and-admins/digital-commerce-setup/checkout-process/order-creation) provides several mapper interfaces that allow you to customize the order creation process without modifying core functionality. Use these interfaces to add [custom fields](/documentation/developers-and-admins/customization/object-types/extend-system-object-types) to orders, customers, addresses, and order items, or to modify data before it’s persisted to the database.
## When to customize
Consider implementing custom mappers when you need to:
  * Store additional business-specific data on orders, customers, or addresses
  * Calculate or derive values during order creation (loyalty points, customer segments, etc.)
  * Integrate with external systems and store integration metadata
  * Apply custom business rules before persisting order data
  * Maintain audit trails or custom tracking information


## Customer information mapping
Implement `ICustomerInfoMapper<TOrderData>` to add custom fields to customer records or modify customer data during order creation.
C#
**Example - Custom customer mapper**
Copy
```
/// <summary>
/// Maps custom fields to customer records during order creation.
/// Only called when creating new customers, not for existing ones.
/// </summary>
public class CustomCustomerInfoMapper : ICustomerInfoMapper<CustomOrderData>
{
    public CustomerInfo PopulateInfo(CustomerInfo customerInfo, CustomOrderData orderData)
    {
        // Add custom logic to populate additional customer fields
        // Example: customerInfo.SetValue("CustomerLoyaltyPoints", orderData.LoyaltyPoints);

        return customerInfo;
    }
}
```

**Important** : Custom mappers are called only when creating new customer records. If an existing customer is found (by member ID or email), the mapper is not invoked. To update existing customer data, you need to implement separate customer update logic outside the order creation process.
### Registration
C#
**Program.cs**
Copy
```
// Register in the application IoC container
builder.Services.AddTransient<ICustomerInfoMapper<CustomOrderData>, CustomCustomerInfoMapper>();
```

## Order information mapping
Implement `IOrderInfoMapper<TOrderData, TPriceCalculationResult>` to add custom fields to order records. This mapper has access to both the order data and the price calculation result, allowing you to store calculated values or metadata with the order.
C#
**Example - Custom order mapper**
Copy
```
/// <summary>
/// Maps custom fields to order records.
/// Has access to both order data and price calculation results.
/// </summary>
public class CustomOrderInfoMapper : IOrderInfoMapper<CustomOrderData, PriceCalculationResult>
{
    public OrderInfo PopulateInfo(
        OrderInfo orderInfo,
        CustomOrderData orderData,
        PriceCalculationResult calculationResult)
    {
        // Add custom logic to populate additional order fields

        // Examples
        orderInfo.SetValue("OrderAdditionalNotes", orderData.AdditionaNotes);
        orderInfo.SetValue("OrderGiftMessage", orderData.GiftMessage);

        return orderInfo;
    }
}
```

### Registration
C#
**Program.cs**
Copy
```
// Register in the application IoC container
builder.Services.AddTransient(typeof(IOrderInfoMapper<,>),
    typeof(CustomOrderInfoMapper<,>));
```

## Address mapping
Implement `ICustomerAddressInfoMapper<TAddressDto>` or `IOrderAddressInfoMapper<TAddressDto>` to add custom fields to address records. Both customer addresses and order addresses can be extended independently.
C#
**Example - Custom address mapper**
Copy
```
/// <summary>
/// Maps custom fields to order address records.
/// </summary>
public class CustomAddressInfoMapper : IOrderAddressInfoMapper<CustomAddressDto>
{
    public OrderAddressInfo PopulateInfo(
        OrderAddressInfo orderAddressInfo,
        CustomAddressDto addressDto)
    {
        // Add custom logic to populate additional address fields

        // Examples
        orderAddressInfo.SetValue("AddressDeliveryNotes", addressDto.DeliveryNotes);

        return orderAddressInfo;
    }

    public CustomAddressDto PopulateDto(CustomAddressDto addressDto, OrderAddressInfo orderAddressInfo)
    {
        // Reverse mapping if needed for reading order addresses
        return addressDto;
    }
}
```

**Address comparison** : When checking for duplicate customer addresses, the service uses all fields including custom fields in the comparison. Ensure your custom fields are appropriate for duplicate detection, or implement custom comparison logic if needed.
### Registration
C#
**Program.cs**
Copy
```
// Register in the application IoC container
builder.Services.AddTransient<IOrderAddressInfoMapper<CustomAddressDto>, CustomAddressInfoMapper>();
```

## Order item mapping
Implement `IOrderItemInfoMapper<TOrderData, TPriceCalculationResult>` to add custom fields to order items. This is useful for storing product-specific metadata or configuration data with each line item.
C#
**Example - Custom order item mapper**
Copy
```
/// <summary>
/// Maps custom fields to order item records.
/// Useful for product configuration, warranty selections, etc.
/// </summary>
public class CustomOrderItemInfoMapper : IOrderItemInfoMapper<CustomOrderData, PriceCalculationResult>
{
    public OrderItemInfo PopulateInfo(
        OrderItemInfo orderItemInfo,
        CustomOrderData orderData,
        PriceCalculationResult calculationResult,
        ProductIdentifier productIdentifier)
    {
        // Add custom logic to populate additional order item fields

        // Examples
        orderItemInfo.SetValue("ItemWarrantyMonths", 24);
        orderItemInfo.SetValue("ItemConfiguration", "CustomConfig");

        return orderItemInfo;
    }
}
```

### Registration
C#
**Program.cs**
Copy
```
// Register in the application IoC container
builder.Services.AddTransient(typeof(IOrderItemInfoMapper<,>), typeof(CustomOrderItemInfoMapper<,>));
```

## Price calculation customization
Implement `IPriceCalculationRequestMapper<TPriceCalculationRequest, TOrderData>` to add custom data to [price calculation requests](/documentation/developers-and-admins/digital-commerce-setup/price-calculation/customization#extend-data-transfer-objects) before prices are calculated. This allows you to influence the pricing calculation with order-specific context.
C#
**Example - Custom calculation request mapper**
Copy
```
/// <summary>
/// Maps custom data to price calculation requests.
/// Allows passing order-specific context to pricing calculations.
/// </summary>
public class CustomCalculationRequestMapper : IPriceCalculationRequestMapper<PriceCalculationRequest, CustomOrderData>
{
    public PriceCalculationRequest PopulateCalculationRequest(
        PriceCalculationRequest calculationRequest,
        CustomOrderData orderData)
    {
        // Add custom logic to modify the calculation request

        // Example: Add customer tier for tiered pricing
        // Example: Add promotional context or B2B contract information

        return calculationRequest;
    }
}
```

For more information on customizing the price calculation pipeline itself, see [Customize price calculation](/documentation/developers-and-admins/digital-commerce-setup/price-calculation/customization).
### Registration
C#
**Program.cs**
Copy
```
// Register in the dependency injection container
builder.Services
    .AddTransient(typeof(IPriceCalculationRequestMapper<,>), typeof(CustomCalculationRequestMapper<,>));
```

## Extending order data
To use custom fields in your mappers, extend the `OrderData` class and use your custom class throughout your order creation implementation:
C#
**Example - Custom order data**
Copy
```
/// <summary>
/// Extended order data with custom fields.
/// </summary>
public class CustomOrderData : OrderData
{
    public int LoyaltyPoints { get; set; }
    public string AdditionaNotes { get; set; } = string.Empty;
    public string GiftMessage { get; set; } = string.Empty;
}
```

You can also extend address DTOs to add custom address fields:
C#
**Example - Custom address DTO**
Copy
```
/// <summary>
/// Extended address DTO with delivery notes field.
/// </summary>
public record CustomAddressDto : AddressDto
{
    public string DeliveryNotes { get; set; } = string.Empty;
}
```

Update your controller to use the custom order data type:
C#
**Example - Using custom order data**
Copy
```
/// <summary>
/// Example controller using custom order data type.
/// Shows how to create orders with custom fields.
/// </summary>
public class CustomCheckoutController : Controller
{
    private readonly IOrderCreationService<CustomOrderData, PriceCalculationRequest,
        PriceCalculationResult, AddressDto> orderCreationService;

    public CustomCheckoutController(
        IOrderCreationService<CustomOrderData, PriceCalculationRequest,
            PriceCalculationResult, AddressDto> orderCreationService)
    {
        this.orderCreationService = orderCreationService;
    }

    public async Task<IActionResult> CreateOrder(CancellationToken cancellationToken)
    {
        var orderData = new CustomOrderData
        {
            // Populates standard fields
            MemberId = 1,
            OrderNumber = "ORD-2024-0001",
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
            OrderItems =
            [
                new OrderItem
                {
                    ProductIdentifier = new ProductIdentifier { Identifier = 123 },
                    Quantity = 2
                }
            ],

            // Populates custom fields
            LoyaltyPoints = 150,
            GiftMessage = "Happy Birthday!"
        };

        int orderId = await orderCreationService.CreateOrder(orderData, cancellationToken);
        return RedirectToAction("Confirmation", new { orderId });
    }
}
```

![]()
[]()[]()
