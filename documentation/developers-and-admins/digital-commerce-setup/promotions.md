# Promotions
  * [ Copy page link ](documentation/developers-and-admins/digital-commerce-setup/promotions#) | [Get HelpService ID](documentation/developers-and-admins/digital-commerce-setup/promotions#)
Core MVC 5


[✖](documentation/developers-and-admins/digital-commerce-setup/promotions# "Close page link panel") [Copy to clipboard](documentation/developers-and-admins/digital-commerce-setup/promotions#)
**Advanced license required**   
  
Features described on this page require the Xperience by Kentico **Advanced** license tier. 
Promotions allow you to offer discounts and special offers to your customers. The system evaluates active promotions during checkout and automatically applies the best available discount.
To learn how to create and manage promotions in the administration, see [Manage promotions](documentation/business-users/manage-commerce-stores#manage-promotions).
Xperience by Kentico provides a promotion framework that allows developers to implement custom discount rules. The framework integrates with the [price calculation API](documentation/developers-and-admins/digital-commerce-setup/price-calculation) to automatically apply promotions during checkout.
## Promotion types
The system supports different types of promotions based on when and how discounts are applied:
Promotion type |  Description |  Use cases  
---|---|---  
[Catalog discounts](documentation/developers-and-admins/digital-commerce-setup/promotions/catalog-discounts) |  Discounts applied to individual products during price calculation. The system evaluates all applicable promotions and automatically selects the best discount for each product. | 
  * Percentage discounts (10% off coffees)
  * Fixed amount discounts ($5 off)
  * Category-based promotions
  * Member-only pricing

  
[Order discounts](documentation/developers-and-admins/digital-commerce-setup/promotions/order-discounts) |  Discounts applied to the entire order total. Order discounts are evaluated after catalog discounts and can be combined with product-level promotions. |  Order total discounts based on shopping cart contents, total price, etc.  
## Discount applicability and priority
Discounts in Xperience can be combined and stacked in various ways. Promotions are evaluated during the price calculation pipeline:
  1. **Catalog discounts** are evaluated first, applying product-level discounts based on configured promotion rules. The system evaluates all applicable promotions and automatically selects the best discount for each product.
  2. **Order discounts** are evaluated after product prices are calculated, applying order-level discounts to the total. The system evaluates all applicable order-level promotions and selects the highest eligible discount, applying it to the order total.


The [order creation service](documentation/developers-and-admins/digital-commerce-setup/checkout-process/order-creation) persists promotion data with orders for reporting and order history. Viewing order details in the **Orders** application provides a breakdown of each applied discount and its impact on the overall unit and order prices.
[![Discount summary](docsassets/documentation/promotions/applied_discounts.png)](https://docs.kentico.com/docsassets/documentation/promotions/applied_discounts.png)
## Implement promotions
To implement custom promotions in your commerce solution:
  * **Catalog discounts** – See [Implement catalog discounts](documentation/developers-and-admins/digital-commerce-setup/promotions/catalog-discounts) for creating product-level promotion rules.
  * **Order discounts** – See [Implement order discounts](documentation/developers-and-admins/digital-commerce-setup/promotions/order-discounts) for creating order-level promotion rules.


## Manage promotions
Marketers [create and manage promotions](documentation/business-users/manage-commerce-stores) in the administration interface. After you register a promotion rule in code, it becomes available for selection when creating new promotions.
Promotions support:
  * **Activation scheduling** – Set start and end dates for time-limited offers.
  * **Configurable properties** – Define promotion parameters (discount percentage, target categories, eligibility rules) through the admin UI.
  * **Multiple active promotions** – Run several promotions simultaneously with automatic best-discount selection.