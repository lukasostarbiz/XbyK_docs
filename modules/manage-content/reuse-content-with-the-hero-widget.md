# Reuse content with the Hero widget
  * [ Copy page link ](modules/manage-content/reuse-content-with-the-hero-widget#) | [Get HelpService ID](modules/manage-content/reuse-content-with-the-hero-widget#)
Core MVC 5


[✖](modules/manage-content/reuse-content-with-the-hero-widget# "Close page link panel") [Copy to clipboard](modules/manage-content/reuse-content-with-the-hero-widget#)
The Hero element is often the first thing visitors see on your website, so it’s an excellent place to present content you wish to showcase.
The visual style and concise messaging set the tone of your visitor’s experience and invite them to explore your site further. Hero is a prominent element, and editors must have ways to custom-tailor Hero element’s content to different website audiences. In this guide, you’ll see how a custom-built _Hero banner_ widget helps editors keep the Hero area fresh and appealing.
To work with widgets, make sure you have the following ready:
  * Your app designers and developers discussed your business requirements, editors team, and your content needs - and they custom-tailored how you can configure your widget accordingly.
  * You already have content items prepared in a Content hub. (For the following examples with a Hero banner, you’d need a _Product item_ , _Product page_ and/or _Hero content item_.)


## Display reusable content in widgets
You can use widgets to show existing content when creating a page. You don’t have to create or copy content repeatedly when you populate different widgets with reusable content items. Just tell the widget to display for example an _article_ , _image_ , or list of _benefits_ , and the widget saves the reference to the content item stored in the **Content hub**. Based on the widget configuration options, you can also design the content displayed in the widget and create a visual message that serves your needs.
Let’s see an example from Kbank: the **Hero banner widget**.
We’ll use the demo Kbank site to show examples of the _Hero banner widget_ capabilities to reuse and design content. The possibilities for displaying data on a channel depend on how your developers or partner implemented your project.
[![](docsassets/guides/reuse-content-with-the-hero-widget/hero-banner.png)](https://docs.kentico.com/docsassets/guides/reuse-content-with-the-hero-widget/hero-banner.png)
Xperience allows you to reuse any content stored within one content item across different widgets. For example, the _Personal loan page_ uses the _Personal loan_ content item in various widgets.
The _Product content type_ used to store the _Personal loan_ data includes the product’s name, image, description, benefits, and other information. Now, depending on the widget, you can show different product information on your website. For example:
  * The _Hero banner_ shows the basic product information: name, description, or image.
  * The _Benefits widget_ will show the product benefits.
  * The _Product comparator widget_ will show the product features, and so on.


[![](docsassets/guides/reuse-content-with-the-hero-widget/reusable-content-in-widgets.png)](https://docs.kentico.com/docsassets/guides/reuse-content-with-the-hero-widget/reusable-content-in-widgets.png)
**Hero banner** can reuse information from the _Product content type_. Let’s look at how you can display data through the _Hero banner widget_ on the Kbank website.
## Use the Hero banner widget to display data from the current product page
The goal of every product page is to sell products or services online. 
Besides typical information like a name, product image, or a short description, the page also needs to show the benefits customers gain from the product or promote all the different features that make this specific product stand out from its competition.
### Select the content type you want to add to the website channel
When you add a _Product page_ , select a type that defines the data the page will hold (the _content type_).
[![Create new page from template](docsassets/guides/reuse-content-with-the-hero-widget/create-new-page.png)](https://docs.kentico.com/docsassets/guides/reuse-content-with-the-hero-widget/create-new-page.png)
### Choose the page template to display your product data
**Page template** helps you keep a consistent look across the pages editors create thanks to a predefined layout. You can create the template from scratch or save an existing page as a template.
[![Example of page template on Kbank website](docsassets/guides/reuse-content-with-the-hero-widget/page-template.png)](https://docs.kentico.com/docsassets/guides/reuse-content-with-the-hero-widget/page-template.png)
It’s common for developers and designers to prepare multiple default _templates_ that they associate with a product content type. To speed up creating pages, editors also often save their common custom page template configuration into _page presets_. If the type has only a one-page template and no presets, you’ll jump into step three after selecting the content type.
### Add product from the Content hub to your new page
In the next step, choose the product you want to display. Xperience creates a reference to this [reusable item in the **Content hub**](guides/digital-marketing/xperience-essentials/manage-content-hub-essentials), and developers have ensured that the page pulls the right data from the content item to display.
[![Product page on Kbank demo website](docsassets/guides/reuse-content-with-the-hero-widget/product-page.png)](https://docs.kentico.com/docsassets/guides/reuse-content-with-the-hero-widget/product-page.png)
### Confirm that the Hero banner widget displays data from the current page
Open the **Hero banner** on your page, and in the _Selected hero widget mode_ , ensure that the widget configuration displays data from the **Current product page**. 
Kbank’s website contains instructions for the _Hero banner_ configuration and saves you time with searching and copying the information. And you know that the content displayed on the page will respect your design guidelines.
[![Example of hero banner properties](docsassets/guides/reuse-content-with-the-hero-widget/hero-banner-properties.png)](https://docs.kentico.com/docsassets/guides/reuse-content-with-the-hero-widget/hero-banner-properties.png)
You can switch to the preview mode to check if the _Hero banner_ shows all the intended information about the product.
[![Hero banner on the live site](docsassets/guides/reuse-content-with-the-hero-widget/hero-banner-on-live-site.png)](https://docs.kentico.com/docsassets/guides/reuse-content-with-the-hero-widget/hero-banner-on-live-site.png)
## Use the Hero banner to display data from any product page
You may want to promote a product in the _Hero banner_ on a page that’s not built on the _Product page_ content type. The best example may be the _Home page_ or any campaign landing page.
  * _Hero banner_ is a widget, which means it’s a Page Builder component and can be used on any page type,
  * It’s not restricted to live only on the product information or product promotion page,
  * You can use it on the home page and campaign landing pages where you want to promote the product,
  * It doesn’t make sense to re-create the product information in the **Content hub**. Developers have prepared the _Hero banner widget_ to display data from any product page.


### Add a new page for product promotion
To create a campaign landing page, choose the **Content page** type. The _Content page_ is like a free-form page. 
What does it mean? It doesn’t directly reference any specific product from the **Content hub**. However, you can use its widgets to display items from the **Content hub** or some content from website pages.
[![Create a new page dialog](docsassets/guides/reuse-content-with-the-hero-widget/create-new-campaign-page.png)](https://docs.kentico.com/docsassets/guides/reuse-content-with-the-hero-widget/create-new-campaign-page.png)
### Configure the Hero banner to display data from a specific product page
Open the _Hero banner’s_ configuration and choose the **Select product page** for the _hero widget mode_. Then, pick the product page you want to display through the _Hero banner_. Developers ensured that Xperience retrieves the data from the linked product page and put it into the _Hero banner_ widget on your campaign page.
[![Hero banner on product page in Kbank demo website](docsassets/guides/reuse-content-with-the-hero-widget/hero-banner-properties-product-page.png)](https://docs.kentico.com/docsassets/guides/reuse-content-with-the-hero-widget/hero-banner-properties-product-page.png)
## Personalize the Hero banner with a dedicated Hero content type
This option allows you to add a custom-created content. If you don’t want to add a _Hero banner_ that already exists on some page, you can **Select hero content item**. It will show the content from an independent _Hero content item_ that is not part of a product.
[![Hero banner on product page in Kbank demo site](docsassets/guides/reuse-content-with-the-hero-widget/hero-banner-properties-content-item.png)](https://docs.kentico.com/docsassets/guides/reuse-content-with-the-hero-widget/hero-banner-properties-content-item.png)
## Adjust the styling of Hero banner
The _Hero banner_ has a predefined configuration. However, based on your developers’ implementation, you can adjust its design to make the banner stand out from other pages. The following _Hero banner_ examples come from the demo Kbank site and show what you can achieve if you configure the same _Hero banner_ differently.
The configuration options differ for each project, depending on the company’s implementation. Your app designers and developers custom-tailored how you can configure your widget accordingly
  * This is the default setup for the _Hero banner_ - dark text color and image in a circle.


[![Example of properties in Hero banner configuration](docsassets/guides/reuse-content-with-the-hero-widget/hero-banner-setup-1.png)](https://docs.kentico.com/docsassets/guides/reuse-content-with-the-hero-widget/hero-banner-setup-1.png)
[![Example of Hero banner configuration](docsassets/guides/reuse-content-with-the-hero-widget/hero-banner-1.png)](https://docs.kentico.com/docsassets/guides/reuse-content-with-the-hero-widget/hero-banner-1.png)
  * You can switch to light text color and image on the banner’s background.


[![Example of properties in Hero banner configuration](docsassets/guides/reuse-content-with-the-hero-widget/hero-banner-setup-2.png)](https://docs.kentico.com/docsassets/guides/reuse-content-with-the-hero-widget/hero-banner-setup-2.png)
[![Example of Hero banner configuration](docsassets/guides/reuse-content-with-the-hero-widget/hero-banner-2.png)](https://docs.kentico.com/docsassets/guides/reuse-content-with-the-hero-widget/hero-banner-2.png)
  * You can even remove the image and display the product’s benefits instead.


[![Example of properties in Hero banner configuration](docsassets/guides/reuse-content-with-the-hero-widget/hero-banner-setup-3.png)](https://docs.kentico.com/docsassets/guides/reuse-content-with-the-hero-widget/hero-banner-setup-3.png)
[![Example of Hero banner configuration](docsassets/guides/reuse-content-with-the-hero-widget/hero-banner-3.png)](https://docs.kentico.com/docsassets/guides/reuse-content-with-the-hero-widget/hero-banner-3.png)
[![Example of Hero banner configuration](docsassets/guides/reuse-content-with-the-hero-widget/seho-banner-styling.gif)](https://docs.kentico.com/docsassets/guides/reuse-content-with-the-hero-widget/seho-banner-styling.gif)
## Make the most out of Xperience widgets
Widgets in Xperience by Kentico, such as the _Hero banner_ widget, are here to help you work with reusable content. 
Once your developers prepare the widgets based on your project requirements, you can link the content that’s been already prepared and don’t need to worry about styling. And you can enjoy a cup of coffee instead of checking if every piece of information is correct.
[ Previous page ](modules/manage-content/create-new-article-on-a-website)
7 of 19
[ Mark complete and continue ](modules/manage-content/manage-assets-in-hub)
  * [Community Questions & Answers](https://community.kentico.com/q-and-a)
  * [Contact support](https://community.kentico.com/support)


### Cookie consent
We use necessary [cookies](https://www.kentico.com/cookies-policy) to run our website and improve your experience while browsing. Additional cookies are only used with your consent. You may revoke your consent on the [Cookies Policy](https://www.kentico.com/cookies-policy) page or in your browser at any time. 
ACCEPT ALL  [Configure](https://www.kentico.com/cookies-policy)
USE ONLY NECESSARY 
![](https://docs.kentico.com/modules/manage-content/reuse-content-with-the-hero-widget)
[](https://docs.kentico.com/modules/manage-content/reuse-content-with-the-hero-widget)[](https://docs.kentico.com/modules/manage-content/reuse-content-with-the-hero-widget)