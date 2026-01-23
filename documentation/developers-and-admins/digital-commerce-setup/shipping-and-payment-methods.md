---
source: https://docs.kentico.com/documentation/developers-and-admins/digital-commerce-setup/shipping-and-payment-methods
scrape_date: 2026-01-22
---

  * [Home](/documentation)
  * [Developers and admins](/documentation/developers-and-admins)
  * [Digital commerce setup](/documentation/developers-and-admins/digital-commerce-setup)
  * Shipping and payment methods 


# Shipping and payment methods
**Advanced license required**   
  
Features described on this page require the Xperience by Kentico **Advanced** license tier. 
Managing shipping and payment methods is a key part of setting up [digital commerce](/documentation/developers-and-admins/digital-commerce-setup) in Xperience by Kentico. This page explains how to configure shipping and payment methods, extend system objects for business logic, and use content types to present information to customers.
To configure shipping and payment methods, you need to:
  1. [Configure shipping methods](#configure-shipping-methods).
  2. [Configure payment methods](#configure-payment-methods).
  3. [Display shipping and payment methods in the checkout process](#display-shipping-and-payment-methods).
  4. [Implement business logic for shipping and payment methods](#implement-shipping-and-payment-methods).


## Configure shipping methods
To create a new shipping method:
  1. Navigate to the **Commerce configuration** application.
  2. Select the **Shipping methods** tab.
  3. Select **New shipping method**.
  4. Set the properties of the new shipping method: 
     * **Shipping method name** – the display name of shipping method used in the admin interface.
     * **Description** – a brief description of the shipping method for internal users. We do not recommend you to use this description in the presentation layer.
     * **Enabled** – whether the shipping method is enabled for use.
     * **Shipping price** – the cost of using this shipping method. The exact way in which this price is included in the order total price depends on your implementation.


You can view all shipping methods in the **Shipping methods** listing and [display](#display-shipping-and-payment-methods) them on your storefront.
### Retrieve shipping methods in code
To retrieve shipping methods in your application, create a repository service that uses the `IInfoProvider<ShippingMethodInfo>` to query enabled shipping methods:
C#
**Retrieve a collection of enabled shipping methods**
Copy
```
// An instance of the shipping method info provider, obtained via dependency injection
private readonly IInfoProvider<ShippingMethodInfo> shippingMethodInfoProvider;

var shippingMethods = await shippingMethodInfoProvider.Get()
        .WhereTrue(nameof(ShippingMethodInfo.ShippingMethodEnabled))
        .GetEnumerableTypedResultAsync(cancellationToken: cancellationToken);

// Access properties of each shipping method
foreach (ShippingMethodInfo shippingMethod in shippingMethods)
{
    // You can access all properties of the ShippingMethodInfo object
    string name = shippingMethod.ShippingMethodName;
    string description = shippingMethod.ShippingMethodDescription;
    decimal price = shippingMethod.ShippingMethodPrice;
    bool enabled = shippingMethod.ShippingMethodEnabled;
}
```

## Configure payment methods
To create a new payment method:
  1. Navigate to the **Commerce configuration** application.
  2. Select the **Payment methods** tab.
  3. Select **New payment method**.
  4. Set the properties of the new payment method: 
     * **Payment method name** – the display name of the payment method used in the admin interface.
     * **Description** – a brief description of the payment method for internal users. We do not recommend you to use this description in the presentation layer.
     * **Enabled** – whether the payment method is enabled for use.


You can view all payment methods in the **Payment methods** listing and [display](#display-shipping-and-payment-methods) them on your storefront.
### Retrieve payment methods in code
To retrieve payment methods in your application, create a repository service that uses the `IInfoProvider<PaymentMethodInfo>` to query enabled payment methods:
C#
**Retrieve a collection of enabled payment methods**
Copy
```
// An instance of the payment method info provider, obtained via dependency injection
private readonly IInfoProvider<PaymentMethodInfo> paymentMethodInfoProvider;

var paymentMethods = await paymentMethodInfoProvider.Get()
        .WhereTrue(nameof(PaymentMethodInfo.PaymentMethodEnabled))
        .GetEnumerableTypedResultAsync(cancellationToken: cancellationToken);

// Access properties of each payment method
foreach (PaymentMethodInfo paymentMethod in paymentMethods)
{
    // You can access all properties of the PaymentMethodInfo object
    string name = paymentMethod.PaymentMethodName;
    string description = paymentMethod.PaymentMethodDescription;
    bool enabled = paymentMethod.PaymentMethodEnabled;
}
```

## Display shipping and payment methods
The system objects for [shipping](#configure-shipping-methods) and [payment](#configure-payment-methods) methods can be [extended](/documentation/developers-and-admins/customization/object-types/extend-system-object-types) to store internal business logic and configuration data, such as countries where a certain shipping method is available, processing timeframes, or integration settings with third-party providers.
However, to display shipping and payment methods on your storefront, you need to create dedicated [content types](/documentation/developers-and-admins/development/content-types), one for shipping methods and one for payment methods. This separation ensures that you can localize the presentation data for different markets and languages, while keeping the internal business logic separate from the customer-facing content.
The content types can have the following fields:
  * **Display name** – the name of the shipping or payment method as it appears to customers.
  * **Description** – a brief description of the shipping or payment method for customers.
  * **Icon** – an optional visual representation of the shipping or payment method.
  * **Shipping/Payment method** – an [object selector](/documentation/developers-and-admins/customization/extend-the-administration-interface/ui-form-components/reference-admin-ui-form-components#object-selector) field for selecting the associated shipping or payment method.


When displaying shipping and payment methods, you can use the [ContentRetriever API](/documentation/developers-and-admins/development/content-retrieval/retrieve-content-items) to retrieve and display the relevant content items.
## Implement shipping and payment methods
After you create the objects representing [shipping](#configure-shipping-methods) and [payment](#configure-payment-methods) methods, you need to implement the business logic. This may include:
  * Validating the selected shipping and payment methods during the checkout process. Availability of shipping methods may differ based on the selected shipping address.
  * Calculating shipping costs based on the selected shipping method and the customer’s location. You need to handle different pricing rules and conditions, for example, free shipping for orders over a certain amount.
  * Integrating with third-party payment gateways to process payments securely. Due to the customizability and variability of payment method providers, you need to account for different APIs and various authentication mechanisms.
  * Handling any specific business rules or requirements related to shipping and payment methods. For example, you may need to implement age verification for certain payment methods or restrict shipping methods based on the customer’s location.


You can see a simple example that showcases the basic functionality in the `DancingGoatCheckoutController.cs` file in the [Dancing Goat](/documentation/developers-and-admins/installation#available-project-templates) sample site.
![]()
[]()[]()
