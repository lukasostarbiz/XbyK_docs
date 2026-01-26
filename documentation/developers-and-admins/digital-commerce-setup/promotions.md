---
source: https://docs.kentico.com/documentation/developers-and-admins/digital-commerce-setup/promotions
scrape_date: 2026-01-26
---

  * [Home](/documentation)
  * [Developers and admins](/documentation/developers-and-admins)
  * [Digital commerce setup](/documentation/developers-and-admins/digital-commerce-setup)
  * Promotions 


# Promotions
**Advanced license required**   
  
Features described on this page require the Xperience by Kentico **Advanced** license tier. 
Promotions allow you to offer discounts and special offers to your customers. The system evaluates active promotions during checkout and automatically applies the best available discount.
To learn how to create and manage promotions in the administration, see [Manage promotions](/documentation/business-users/manage-commerce-stores#manage-promotions).
Xperience by Kentico provides a promotion framework that allows developers to implement custom discount rules. The framework integrates with the [price calculation API](/documentation/developers-and-admins/digital-commerce-setup/price-calculation) to automatically apply promotions during checkout.
## Promotion types
The system supports different types of promotions based on when and how discounts are applied:
Promotion type |  Description |  Use cases  
---|---|---  
[Catalog discounts](/documentation/developers-and-admins/digital-commerce-setup/promotions/catalog-discounts) |  Discounts applied to individual products during price calculation. The system evaluates all applicable promotions and automatically selects the best discount for each product. Only one promotion can apply per product. | 
  * Percentage discounts (10% off coffees)
  * Fixed amount discounts ($5 off)
  * Category-based promotions
  * Member-only pricing

  
[Order discounts](/documentation/developers-and-admins/digital-commerce-setup/promotions/order-discounts) |  Discounts applied to the entire order total. Order discounts are evaluated after catalog discounts and can be combined with product-level promotions. Only one order discount can apply per order. |  Order total discounts based on shopping cart contents, total price, etc.  
## Discount applicability and priority
Discounts in Xperience can be combined and stacked in various ways. Promotions are evaluated during the price calculation pipeline:
  1. **Catalog discounts** are evaluated first, applying product-level discounts based on configured promotion rules. The system evaluates all applicable promotions and automatically selects the best discount for each product.
  2. **Order discounts** are evaluated after product prices are calculated, applying order-level discounts to the total. The system evaluates all applicable order-level promotions and selects the highest eligible discount, applying it to the order total.


The selected [price calculation mode](/documentation/developers-and-admins/digital-commerce-setup/price-calculation/implementation#calculate-prices) also determines what types of promotion are evaluated. For example, [catalog price calculation](/documentation/developers-and-admins/digital-commerce-setup/price-calculation/implementation#catalog-price-calculation) applies catalog discounts only. Additionally, the entire calculation pipeline can be [customized](/documentation/developers-and-admins/digital-commerce-setup/price-calculation/customization#create-a-custom-steps-provider) to your preference.
The [order creation service](/documentation/developers-and-admins/digital-commerce-setup/checkout-process/order-creation) persists promotion data with orders for reporting and order history. Viewing order details in the **Orders** application provides a breakdown of each applied discount and its impact on the overall unit and order prices.
[![Discount summary](/docsassets/documentation/promotions/applied_discounts.png)](/docsassets/documentation/promotions/applied_discounts.png)
## Redemption methods
Promotions support two redemption methods that control how customers receive discounts:
Redemption method |  Description  
---|---  
**Automatic** |  The default behavior. Promotions are automatically evaluated and applied during price calculation when customers meet the configured conditions.  
**Generic discount code** |  Requires customers to enter a specific code (e.g., _WINTER20_) to receive the discount. The promotion is only applied if the customer provides the matching code.  
Coupon codes can be configured for both catalog discounts and order discounts. For implementation details, see [Coupon codes](/documentation/developers-and-admins/digital-commerce-setup/promotions/coupon-codes).
Use coupon codes for targeted marketing campaigns, newsletter subscriber discounts, influencer partnerships, or limited-time promotional offers.
## Implement promotions
  * **Catalog discounts** – See [Implement catalog discounts](/documentation/developers-and-admins/digital-commerce-setup/promotions/catalog-discounts) for creating product-level promotion rules.
  * **Order discounts** – See [Implement order discounts](/documentation/developers-and-admins/digital-commerce-setup/promotions/order-discounts) for creating order-level promotion rules.
  * **Coupon codes** – See [Coupon codes](/documentation/developers-and-admins/digital-commerce-setup/promotions/coupon-codes) for requiring discount codes for redemption.


## Manage promotions
Marketers [create and manage promotions](/documentation/business-users/manage-commerce-stores) in the administration interface. After you register a promotion rule in code, it becomes available for selection when creating new promotions.
Promotions support:
  * **Activation scheduling** – Set start and end dates for time-limited offers.
  * **Configurable properties** – Define promotion parameters (discount percentage, target categories, eligibility rules) through the admin UI.
  * **Redemption methods** – Choose between automatic application or require customers to enter a discount code.
  * **Multiple active promotions** – Run several promotions simultaneously with automatic best-discount selection.


![]()
[]()[]()
