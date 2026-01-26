---
source: https://docs.kentico.com/documentation/business-users/manage-commerce-stores
scrape_date: 2026-01-26
---

  * [Home](/documentation)
  * [Business users](/documentation/business-users)
  * Manage commerce stores 


# Manage commerce stores
**Advanced license required**   
  
Features described on this page require the Xperience by Kentico **Advanced** license tier. 
The digital commerce feature provides you with simple online stores where you can sell physical or digital products. It offers the essential tools, data structure, and actions needed to manage your shop and process orders.
## Manage products
Due to the customizability of the Xperience by Kentico commerce feature, how you manage products depends on the implementation of your [product catalog](/documentation/developers-and-admins/digital-commerce-setup/model-product-catalog).
The following image shows a **sample** listing of products hosted in the content hub from the Dancing Goat sample site:
[![Listing of products on the Dancing Goat](/docsassets/documentation/manage-commerce-stores/DG_products_listing.png)](/docsassets/documentation/manage-commerce-stores/DG_products_listing.png)
## Manage customers
The **Customers** application allows you to view customer details, and track orders made by customers.
  1. Navigate to the **Customers** application.
  2. Select a customer to view their details. 
     * On the **Addresses** tab, you can view and manage customer addresses.
     * On the **Orders** tab, you can view a list of orders made by the customer.


## Manage orders
The **Orders** application allows users to list orders, view order details, change order statuses, and finalize orders.
### View details of orders
  1. Navigate to the **Orders** application.
  2. Find and select the order you want to modify. 
     * You can use the search interface or sort orders by status.
     * On the **Overview** tab, you can view basic order info and customer details.
  3. Navigate to the **Ordered items** tab to view a list of products in the order.


[![List of ordered items](/docsassets/documentation/manage-commerce-stores/ordered_items.png)](/docsassets/documentation/manage-commerce-stores/ordered_items.png)
### View orders from specific customers
  1. Navigate to the **Customers** application.
  2. Select a customer whose orders you want to view.
  3. Navigate to the **Orders** tab to view a list of orders created by the selected customer.


[![View orders of a customer](/docsassets/documentation/manage-commerce-stores/customer_orders.png)](/docsassets/documentation/manage-commerce-stores/customer_orders.png)
### Change order status
  1. Navigate to the **Orders** application.
  2. Find and select the order you want to modify. 
     * You can use the search interface or sort orders by status.
  3. Select **Update order status**.
  4. Select the new status to which you want to move the order and **Confirm** the changes. 
     * Depending on your [commerce configuration](/documentation/developers-and-admins/digital-commerce-setup/configure-order-statuses#configure-notifications-for-order-statuses), selected users may receive a notification that a new order was moved to the new status. Note that if you skip statuses (e.g., move an order from the initial to the final status while skipping all other statuses), the notification is only sent for the status to which you move the order, not for the intermediate statuses.


[![Order status interface with the Update order status button](/docsassets/documentation/manage-commerce-stores/update_order_status.png)](/docsassets/documentation/manage-commerce-stores/update_order_status.png)
You can now see the order in the listing with the new status.
## Manage promotions
The **Promotions** application allows you to create and manage discounts for your store. Promotions can apply discounts to individual products or entire orders.
### Promotion types
Your development team configures the available promotion types for your store. Common types include:
  * **Catalog discounts** – Apply discounts to individual products (e.g., 10% off all coffees, $5 off selected items)
  * **Order discounts** – Apply discounts to the entire order (e.g., 15% off orders above $100)


When multiple promotions could apply to the same product or order, the system automatically selects the promotion that provides the best discount for the customer. For more details, see [Discount applicability and priority](/documentation/developers-and-admins/digital-commerce-setup/promotions#discount-applicability-and-priority).
### Create a promotion
  1. Open the **Promotions** application.
  2. Select the promotion type in the left tab.
  3. Select **Create promotion**.
  4. Fill in the promotion details: 
     * Enter a **Display name** for the promotion.
     * Select a **Promotion type** – choose from available promotion rules configured by your development team.
     * Configure the **promotion rule settings** based on the selected type (e.g., discount percentage, target products, eligibility rules).
  5. **Save** the promotion.
  6. Select **Schedule activation**.
  7. Set the **Active from** and **Active to** dates to define when the promotion is available. Promotions are automatically activated and deactivated based on these dates.


[![Create a promotion](/docsassets/documentation/manage-commerce-stores/promotion_create.png)](/docsassets/documentation/manage-commerce-stores/promotion_create.png)
The promotion is automatically applied to eligible products or orders during checkout based on the configured rules.
### Edit a promotion
  1. Open the **Promotions** application.
  2. Select the promotion you want to edit.
  3. Update the promotion settings as needed.
  4. **Save** your changes.


Changes to promotions take effect immediately for new price calculations (for products, shopping carts, and orders). Orders that have already been placed are not affected by promotion changes.
### Promotion statuses
Promotions display a status indicator to help you track their state:
  * **Scheduled** – The promotion is configured but not yet active. It will automatically activate when the **Active from** date arrives.
  * **Active** – The promotion is currently running and being applied to eligible products or orders.
  * **Deactivated** – The promotion is not active. You can reactivate it by setting new activation dates.


[![Promotion status info](/docsassets/documentation/manage-commerce-stores/promotion_status.png)](/docsassets/documentation/manage-commerce-stores/promotion_status.png)
The status automatically updates based on the **Active from** and **Active to** dates you configure via **Change duration** when editing the promotion.
### Use coupon codes with promotions
Coupon codes (also called discount codes or promo codes) allow you to control which customers receive a discount. Instead of automatically applying a promotion to all eligible customers, you can require customers to enter a specific code during checkout.
#### Add a coupon code to a promotion
When creating or editing a promotion:
  1. In the **Redemption method** field, select **Generic discount code**.
  2. Enter your desired code in the **Discount code** field (e.g., _WELCOME20_ , _SUMMER50_).
  3. Configure the rest of the promotion as usual (discount amount, activation dates, rules).
  4. **Save** the promotion.


The promotion now requires customers to enter the matching discount code to receive the discount.
Discount codes are **case-insensitive**. Customers can enter _welcome20_ , _WELCOME20_ , or _Welcome20_ and all variations will work.
### Delete a promotion
  1. Open the **Promotions** application.
  2. Select the promotion you want to delete.
  3. Select **Delete** and confirm the action.


![]()
[]()[]()
