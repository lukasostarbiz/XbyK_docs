# Display a product on a product page
  * [ Copy page link ](modules/work-with-website-content/Create-a-new-product-page#) | [Get HelpService ID](modules/work-with-website-content/Create-a-new-product-page#)
Core MVC 5


[✖](modules/work-with-website-content/Create-a-new-product-page# "Close page link panel") [Copy to clipboard](modules/work-with-website-content/Create-a-new-product-page#)
Product pages can be an essential part of your company’s website. They inform the visitors about the advantages of doing business with your company and present your goods or services to potential customers in a captivating way that supports their purchase. A well-placed call to action button can help lead customers to conversion.
A product page, or website channel page, is a wrapper around a reusable product content type that displays data from individual product items. We’re following this recommended practice.
## Use page preset
When you create uniformly looking product pages, you can speed up your work using a **preset page**. A preset is a template with widgets set up for a specific purpose, such as a product page that you can use for quick creation. Add the content and some additional styling if you need it, and the page is set.
Let’s see how to create a step-by-step product page in Xperience by Kentico on our demo Kbank site. We’ll create a _Personal loan_ page.
Templates in real-life scenarios will also have other properties, for example, changing the position of an article and Page Builder area on an article page type.
## Create a new product page
In the following example, a senior editor prepared a [page template](documentation/business-users/website-content/page-templates), and a store manager created a product in the _Content hub_. For your website, you or someone else from your team needs to create the page preset first, based on your company’s strategy and guidelines.
**Why is the product kept separate from the product page?**
Curious, isn’t it?
  * The product content item in the _Content hub_ is reusable. It means you can link the product to more pages or channels. Imagine you created the product page and now want to advertise the product via email. So, you prepare the email and then simply link the existing product item instead of creating it from scratch again.
  * If anything changes in the content item, the widgets linked to this item change immediately as well.
  * The widget linking the content item adds the proper visual layer, regardless of the channel. Which means you don’t have to worry about different styles. It’s implemented and based on the company’s guidelines.


**How the related data work**
When creating a product, you usually link other content items to it. The linked items work the same way as the product content item. These are independent pieces of content and you can reuse them whenever you need. For the product, you group content items that fit the product.
In our  _Personal loan_ example, we added the following content items: an image, product benefits, product features, and a loan type.
[![Product with linked reusable content](docsassets/guides/Create-a-new-product-page/product-benefits-features.png)](https://docs.kentico.com/docsassets/guides/Create-a-new-product-page/product-benefits-features.png)
  1. First, create a new page as a subpage to the _Loans page_.


[![Adding a new page to the content tree](docsassets/guides/Create-a-new-product-page/creating-new-page.png)](https://docs.kentico.com/docsassets/guides/Create-a-new-product-page/creating-new-page.png)
  1. Name the page and choose the _Product page_ content type. This tells Xperience there will be a product linked to the page’s content, as you’ll see in the following steps.


[![Selecting the Product page type](docsassets/guides/Create-a-new-product-page/page-type.png)](https://docs.kentico.com/docsassets/guides/Create-a-new-product-page/page-type.png)
  1. Choose the Page template. The one we’re looking for is in the _Preset_ section. Someone from your team and the development agency used an existing page to create the template, and you can now use it to maintain consistent design or speed things up.


[![Selecting pre-configured page preset](docsassets/guides/Create-a-new-product-page/page-templates.png)](https://docs.kentico.com/docsassets/guides/Create-a-new-product-page/page-templates.png)
  1. Now set up the basic information about the page. This tells the page what product it should link to. In other words, the page references reusable content saved as a separate content item in the _Content hub_.


[![Selecting a reusable product from the Content hub](docsassets/guides/Create-a-new-product-page/selecting-product.png)](https://docs.kentico.com/docsassets/guides/Create-a-new-product-page/selecting-product.png)
  1. Beside the product, we’ll add SEO and other information based on the page implementation.


These fields are channel-specific, meaning the data applies only to the specific site, and you won’t be able to reuse it on other pages or channels.
  1. Now, it’s time to add the content to the widgets.


Let’s start with the _Hero banner_ and choose the Current product page in the widget properties. If the widget implementation allows it, you can, for example, change the design or add a _Call to action_ button. Do the same with the _Benefit_ and _Product widgets_.
[![Configuring the widget’s to display data from the linked product](docsassets/guides/Create-a-new-product-page/hero-banner-properties.png)](https://docs.kentico.com/docsassets/guides/Create-a-new-product-page/hero-banner-properties.png)
  1. Add a Call to action button to the Product widget to increase the chance of visitors interacting with your website. Add the _CTA_ text into the _Product widget_ properties to see it on the widget.


[![Adjusting visual design of the Hero banner widget](docsassets/guides/Create-a-new-product-page/product-properties.png)](https://docs.kentico.com/docsassets/guides/Create-a-new-product-page/product-properties.png)
[![Product widget](docsassets/guides/Create-a-new-product-page/product-widget.png)](https://docs.kentico.com/docsassets/guides/Create-a-new-product-page/product-widget.png)
  1. Finally, fill in the _Page heading_ widget and choose the _Form_.


[![Section that contains Page heading and Form widgets](docsassets/guides/Create-a-new-product-page/page-heading-and-form.png)](https://docs.kentico.com/docsassets/guides/Create-a-new-product-page/page-heading-and-form.png)
## Link the Product from another page
You don’t need to create the product page to be able to link the product.
Sometimes, you want to link a product from a page type that is different from the product page. Let’s see the _Home_ page as an example:
  1. You want to promote the Personal loan in the _Hero banner_ of the Home page. In the banner properties, choose the _Select product page_ option and select the _Personal loan_ page.


[![Selecting data from another page in the Hero banner properties](docsassets/guides/Create-a-new-product-page/hero-banner-properties-add-page.png)](https://docs.kentico.com/docsassets/guides/Create-a-new-product-page/hero-banner-properties-add-page.png)
  1. Once you confirm this choice, the _Hero banner_ displays the Personal loan product.


[![Configured Hero banner widget](docsassets/guides/Create-a-new-product-page/herro-banner-personal-loan.png)](https://docs.kentico.com/docsassets/guides/Create-a-new-product-page/herro-banner-personal-loan.png)
## In conclusion
Effective product pages play an important role in engaging potential customers, highlighting business advantages, and driving conversions. By strategically placing the right content completed with a call-to-action button, companies can guide visitors toward purchasing decisions.
[ Previous page ](modules/work-with-website-content/create-new-article-on-a-website)
12 of 20
[ Mark complete and continue ](modules/work-with-website-content/manage-page-urls-introduction)
  * [Community Questions & Answers](https://community.kentico.com/q-and-a)
  * [Contact support](https://community.kentico.com/support)


### Cookie consent
We use necessary [cookies](https://www.kentico.com/cookies-policy) to run our website and improve your experience while browsing. Additional cookies are only used with your consent. You may revoke your consent on the [Cookies Policy](https://www.kentico.com/cookies-policy) page or in your browser at any time. 
ACCEPT ALL  [Configure](https://www.kentico.com/cookies-policy)
USE ONLY NECESSARY 
![](https://docs.kentico.com/modules/work-with-website-content/Create-a-new-product-page)
[](https://docs.kentico.com/modules/work-with-website-content/Create-a-new-product-page)[](https://docs.kentico.com/modules/work-with-website-content/Create-a-new-product-page)