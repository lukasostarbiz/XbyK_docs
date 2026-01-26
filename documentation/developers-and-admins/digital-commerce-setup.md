---
source: https://docs.kentico.com/documentation/developers-and-admins/digital-commerce-setup
scrape_date: 2026-01-26
---

  * [Home](/documentation)
  * [Developers and admins](/documentation/developers-and-admins)
  * Digital commerce setup 


# Digital commerce setup
**Advanced license required**   
  
Features described on this page require the Xperience by Kentico **Advanced** license tier. 
The digital commerce feature helps you build simple online stores where you can sell physical or digital products. It gives you the basic tools, data structure, and actions you need to manage your shop and process orders. You can use it to set up product listings, handle purchases, and store customer data. Custom development is needed to fully set up and customize your shop, but this feature takes care of the main building blocks so you don’t have to start from scratch.
To use the commerce features of Xperience by Kentico:
  1. [Enable](#enable-digital-commerce) the commerce feature.
  2. [Prepare a product catalog](/documentation/developers-and-admins/digital-commerce-setup/model-product-catalog) and fill it with products. 
     * You need to implement stock management to track product availability. You can do this by [modeling the product stock](/documentation/developers-and-admins/digital-commerce-setup/model-product-catalog/model-product-stock) as a custom module.
  3. Implement [shipping and payment methods](/documentation/developers-and-admins/digital-commerce-setup/shipping-and-payment-methods).
  4. Implement the [shopping cart and checkout process](/documentation/developers-and-admins/digital-commerce-setup/checkout-process). 
     * The shopping cart and checkout interfaces need to be implemented as custom pages or widgets.
  5. Configure [order statuses](/documentation/developers-and-admins/digital-commerce-setup/configure-order-statuses) and set up notifications for order status changes.
  6. Display the product catalog on your storefront (your public-facing interface where customers browse and purchase products). 
     * Depending on your product catalog model, you can either directly display product pages (if using pages to store products) or create pages for each product using custom code (if using the content hub to store products).


## Enable digital commerce
To enable the commerce feature:
  1. Open your Xperience project in Visual Studio and edit the _Program.cs_ file.
  2. [Enable](/documentation/developers-and-admins/development/website-development-basics/configure-new-projects/enable-application-features) the commerce feature in the application startup:
C#
**Program.cs**
Copy
```
using Kentico.Xperience.Commerce;

// ...

WebApplicationBuilder builder = WebApplication.CreateBuilder(args);

// ...

builder.Services.AddKentico(features =>
{
    // Enables digital commerce
    features.UseCommerce();
});
```



## Reference implementation
The sample [Dancing Goat project](/documentation/developers-and-admins/installation#available-project-templates) provides a production-ready implementation of the digital commerce solution that demonstrates how to integrate all commerce features into a working online store. The sample site includes complete implementations of:
  * Product catalog modeling and display
  * Price calculation with tax logic
  * Shipping and payment method handling
  * Shopping cart functionality
  * Checkout process with multiple steps
  * Order management and status tracking
  * Promotions and discounts implementation
  * Customer account integration with [membership](/documentation/developers-and-admins/development/registration-and-authentication)


Documentation pages in this section often reference specific parts of the Dancing Goat implementation when describing features and providing code examples. We recommend examining the sample site’s source code alongside this documentation to see how individual commerce components work together in a complete solution.
The Dancing Goat implementation serves as a practical reference for:
  * **Architecture patterns** – See how to structure your commerce-related services, controllers, and view models.
  * **Best practices** – Learn recommended approaches for error handling, validation, and data flow.
  * **Integration examples** – Understand how commerce features integrate with Xperience’s content management, membership, and other capabilities.
  * **Customization techniques** – Explore examples of extending default behaviors to meet specific business requirements.


You can use the Dancing Goat implementation as a starting point for your own commerce solution, adapting its patterns and code to your specific needs.
![]()
[]()[]()
