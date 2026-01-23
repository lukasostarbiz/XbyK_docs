---
source: https://docs.kentico.com/documentation/developers-and-admins/digital-commerce-setup/configure-order-statuses
scrape_date: 2026-01-22
---

  * [Home](/documentation)
  * [Developers and admins](/documentation/developers-and-admins)
  * [Digital commerce setup](/documentation/developers-and-admins/digital-commerce-setup)
  * Order statuses 


# Order statuses
**Advanced license required**   
  
Features described on this page require the Xperience by Kentico **Advanced** license tier. 
Order statuses represent stages in the lifecycle of [orders](/documentation/business-users/manage-commerce-stores) created by your store’s customers (_New_ , _Payment received_ , _Shipped_ , _Delivered_ , etc.).
## Create order statuses
  1. Navigate to the **Commerce configuration** application.
  2. Select **New order status**.
  3. Fill in the required fields. 
     * **Order status name** – the display name of the status.
     * **Notify users / the customer when the order reaches this status** – specifies whether the system sends email notifications when an order is moved to this status (or when the order is created for the first order status). Notifications can be sent internally to selected users, or externally to the customer who created the order. See [Configure notifications for order statuses](#configure-notifications-for-order-statuses).
  4. Select **Save**.
  5. Drag the order status to the appropriate position within your desired order lifecycle.


## Configure notifications for order statuses
You can set up two types of notifications about order status changes:
  * **Customer** – sent to the customer who created the order
  * **Internal** – sent to selected [users](/documentation/developers-and-admins/configuration/users/user-management) (staff)


### Create customer notification emails
Customer order notifications are based on [emails](/documentation/business-users/digital-marketing/emails) in [email channels](/documentation/developers-and-admins/digital-marketing-setup/email-channel-management).
**Dedicated commerce email channel**
If possible, we recommend setting up a separate dedicated email channel for your commerce-related emails. This allows you to:
  * Use a different sending domain than your other email communication. This protects your commerce email sender’s reputation from being affected by other emails (e.g., marketing newsletters).
  * Set up a separate [email client](/documentation/developers-and-admins/configuration/email-configuration) for sending commerce-related emails. This ensures that order notifications are not delayed by other email operations, for example when a newsletter is being sent to a very large number of recipients.


You can create any number of customer notification emails and use a different one for every order status.
  1. Create an [email](/documentation/business-users/digital-marketing/emails). 
     * Set the **Email purpose** to _Order status change_.
  2. Prepare the email’s content: 
     * You can fill in the email’s subject, preview text and other content fields in the **Content** view mode. Use [dynamic text placeholders](/documentation/business-users/digital-marketing/emails#personalize-emails-with-dynamic-text) to add information about the related order.  
[![Customer commerce order notification](/docsassets/documentation/configure-order-statuses/order_notification_customer.png)](/docsassets/documentation/configure-order-statuses/order_notification_customer.png)
     * If using an Email Builder template, you can compose the email’s content using configurable components in the **Email Builder** view mode. For detailed information, see [Create emails in Email Builder](/documentation/business-users/digital-marketing/emails/create-emails-in-email-builder).
  3. **Publish** the email. 
     * See [Email lifecycle](/documentation/business-users/digital-marketing/emails#email-lifecycle) to learn more.


### Set internal notification content
The system uses a single notification template for internal order notifications. To define the content:
**Prerequisite**
Make sure that the [service domain for notifications](/documentation/developers-and-admins/configuration/notifications#configure-domains-for-notifications) is set up in your project. For projects deployed in the Xperience by Kentico [SaaS environment](/documentation/developers-and-admins/saas), the [service domain](/documentation/developers-and-admins/digital-marketing-setup/email-channel-management#email-channels-in-the-saas-environment) is configured automatically.
  1. Navigate to the **Notifications** application.
  2. Select the **Commerce order** default notification and [edit the email content](/documentation/developers-and-admins/configuration/notifications#edit-default-notifications). 
     * You can use [content placeholders](/documentation/developers-and-admins/configuration/notifications#notification-content-placeholders) to include information about the order.
[![Internal commerce order notification](/docsassets/documentation/configure-order-statuses/order_notification.png)](/docsassets/documentation/configure-order-statuses/order_notification.png)
  3. **Save** the changes.


### Enable notifications for order statuses
Now you need to enable the notification for individual order statuses:
  1. Navigate to the **Commerce configuration** application.
  2. Select an order status for which you want the system to send notifications.
  3. If you wish to enable internal notifications: 
    1. Select the **Notify users when the order reaches this status** checkbox.
    2. Select **Recipients** – users who you want to receive the notification whenever an order is [moved](/documentation/business-users/manage-commerce-stores#change-order-status) to this status. 
       * Only users who have access to the **Orders** application can be selected.
       * If a user is selected and later loses the access to the **Orders** application, they will still receive order notifications, but will not be able to view the orders.
  4. If you wish to enable customer notifications: 
    1. Select the **Notify the customer when the order reaches this status** checkbox.
    2. Select an [email](/documentation/business-users/digital-marketing/emails) with the _Order status change_ purpose from an email channel.
  5. **Save** the changes.


### Send notifications for new orders
Order notifications (both for customers and internal) are automatically sent when [moving](/documentation/business-users/manage-commerce-stores#change-order-status) existing orders between order statuses. If you want the system to send notifications when a new order is created, you need to call the `SendNotification` method of the `IOrderNotificationService` service when [creating the order](/documentation/developers-and-admins/digital-commerce-setup/checkout-process#create-orders-from-shopping-carts).
C#
**Send notifications on order creation**
Copy
```
// An instance of IOrderNotificationService can be retrieved using dependency injection
private readonly IOrderNotificationService orderNotificationService;

// Creates a new order object
var order = new OrderInfo()
{
    // Create and populate the order object
    // ...
};

// Create and populate the order address (OrderAddressInfo) and order items (OrderItemInfo)
// ...

try
{
    // Sends notifications about the created order (both for customers and internal)
    await orderNotificationService.SendNotification(order.OrderID, cancellationToken);
}
catch (OrderNotificationSendException ex)
{
    // We strongly recommend using a logger service (e.g., ILogger) to detect errors that could occur when sending order notifications
    logger.LogError(ex, "Failed to send notification for order ID {OrderID}", order.OrderID);
}
 


```

![]()
[]()[]()
