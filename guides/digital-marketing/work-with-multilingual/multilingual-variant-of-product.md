---
source: https://docs.kentico.com/guides/digital-marketing/work-with-multilingual/multilingual-variant-of-product
scrape_date: 2026-01-26
---

  * [Home](/guides)
  * [Digital marketing](/guides/digital-marketing)
  * [Work with multilingual](/guides/digital-marketing/work-with-multilingual)
  * Multilingual variant of a product 


# Multilingual variant of a product
Once you’ve created a language variant of a page ([you can check how here](/guides/digital-marketing/work-with-multilingual/multilingual-variant-of-page)), it’s time to convert its content items into the target language. By translating the content, you make sure the user experience is flawless and they don’t see a mixture of languages on one page. When you invest to create proper translations of your content items into languages where you have a market presence, you ensure the pages will perform well on the surface level (what the visitor sees) and the backend (for example, SEO and user accessibility).
This article helps everyone who works with content in the _Content hub_ and displays the data in the channels (pages or emails, for example).
#### Prerequisites:
Before you start working on this guide, make you sure you:
  * Have the Kbank demo site set up and running to test the scenario.
  * Know what multilingual in Xperience by Kentico means and how it works (you can read about it [in this article](/guides/digital-marketing/work-with-multilingual/multilingual-variant-of-content)).
  * Know how to create a language version of a page (check out the [previous article here](/guides/digital-marketing/work-with-multilingual/multilingual-variant-of-page))


## Translate the content
Let’s see where you can find the content you need to translate. There are two main groups of content you could be translating on your site. **Reusable** (or structured) and **unstructured**.
Xperience stores the **Reusable content** in the _Content hub_ , and you can link it to as many places as you want (if the chosen widget supports the content type). **Unstructured content** is used in a specific place, and your team can reuse it elsewhere only if you tediously copy it.
You’ll see both cases in our example Kbank _Insurance_ page.
### 1. Localize linked items
How can you tell which content has not been translated yet? You don’t have to search the _Content hub_ ; the page you’re translating will tell you.
As you can see in the example below, the untranslated content is in the frame, and there is a note informing you that the item has not been translated. You can also see a small _Not translated_ label. If you publish the page now, it will show the content in the fallback language.
[![Not translated content item](/docsassets/guides/multilingual-variant-of-product/not-translated-product.png)](/docsassets/guides/multilingual-variant-of-product/not-translated-product.png)
If you link content from a _Media library_ (not from the _Content hub_), Xperience will not alert you that the content item is not translated. The _Media library_ is a simple feature for storing media and other files and it is not sophisticated enough to know that you want to create a language variant of the item.
### 2. Translate the product
In our example page, the page (not one of the _Page Builder_ widgets) references a Product content item that is stored in the _Content hub_. You can click the **Edit** button to open the content item directly.
[![Opening the translation dialogue window](/docsassets/guides/multilingual-variant-of-product/edit-product.png)](/docsassets/guides/multilingual-variant-of-product/edit-product.png)
The **Content item name** of the new language variant serves for internal purposes, so use your company naming convention.
[![Creating an internal name for a language variant of a content item](/docsassets/guides/multilingual-variant-of-product/name-content-language-variant.png)](/docsassets/guides/multilingual-variant-of-product/name-content-language-variant.png)
Translate or adjust every text field in the content item.
[![Translating the content item](/docsassets/guides/multilingual-variant-of-product/create-content-language-variant.png)](/docsassets/guides/multilingual-variant-of-product/create-content-language-variant.png)
### 3. Translate further linked items
Often, you’ll see that the content item you’re translating has been linked to other content items. It’s important to translate those as well. As you’ll see with the Product content item example, the other linked items are also displayed in different places on the product page.
When you scroll down in the product, you’ll see several other items that need translation. These are highlighted by the frame and notification icons, as well as the _Not translated_ label. For example, the **Product benefits** and the **Product features**.
[![Other content items linked to a content item](/docsassets/guides/multilingual-variant-of-product/list-of-not-translated-content-items.png)](/docsassets/guides/multilingual-variant-of-product/list-of-not-translated-content-items.png)
Use the same steps to translate these items. Open the translation dialogue with the **Edit** button and translate all text fields and other linked items.
Don’t forget to publish every translated content item. When you do, the _Not translated_ label will change to _Published_.
[![Content item that was translated and published](/docsassets/guides/multilingual-variant-of-product/published-content-item.png)](/docsassets/guides/multilingual-variant-of-product/published-content-item.png)
If you don’t translate a part or some parts of of the Product content item, your page will display content in different languages. The translated parts will be in the target language. The omitted, untranslated content will be displayed in the fallback language. In the case of Kbank site, you will have a combination of, for example, the target Czech and fallback English language variants.
### 4. Translate content in the unstructured format
Unstructured content is content that lives on a specific page or on other channels and cannot be reused elsewhere. There are widgets for this type of content, such as a _Rich text widget_ or a _Page heading widget_.
To translate the content in these widgets, either translate the text directly in the widget (for the _Rich text widget_) or open the **Configure widget** dialogue and adjust the text there (for the _Page heading widget_).
Let’s see the _Page heading widget_ :
[![Translating unstructured content](/docsassets/guides/multilingual-variant-of-product/translate-page-heading.png)](/docsassets/guides/multilingual-variant-of-product/translate-page-heading.png)
In the image below, you can see a completely translated section—the _Page heading widget_ with the _Benefits widget_. You translated the benefits as part of the Product content item at the beginning. Because the _Benefit widget_ is linked to the Product content item, it updates the content automatically.
[![Final result of translated unstructured and reusable content](/docsassets/guides/multilingual-variant-of-product/translated-content-items.png)](/docsassets/guides/multilingual-variant-of-product/translated-content-items.png)
Keep in mind that the unstructured content has no fallback language variant.
### 5. Think about additional setting
Based on your project implementation, there may be additional settings you’d need to pay attention to. For example, the Kbank demo site shows editors can use the taxonomy feature and specify a language region where visitors see the language variant of the content. It is an example of a product content item.
The channel language setting is a custom-implemented for the Kbank project and it is not an out-of-the-box Xperience feature.
[![Select a region where the content in specific language is accessible](/docsassets/guides/multilingual-variant-of-product/content-regional-availability.png)](/docsassets/guides/multilingual-variant-of-product/content-regional-availability.png)
## To sum up
When you translate content items, remember that even what is not visible to visitors is important, such as SEO properties and alt texts for images. Translate every content item layer to ensure the page will show properly and share the message in a language that the audience understands.
Remember that the parts of the reusable content items that you haven’t translated will show in the fallback language variant. The unstructured content has no fallback language variant.
## Practice exercise
Let’s apply the previously gained knowledge in the following exercise. You’ll translate a simple product page and its content.
To perform the exercise, you need the Kbank demo site and the [translated materials](https://download.kentico.com/Training/Multilingual.zip).
### Task
Choose the German language and create the language variant of the _Electric car_ loan page in the _Personal Banking_ channel.
### Hint
If you need a little help, here are the steps you should take:
  1. **Select the target language** (of your choice) in the drop-down menu above the content tree.
  2. Find the **Electric car loan page** in the content tree.
  3. Create an **internal name** for the translated page.
  4. Create a **language variant of each content item** (on the Content tab), and remember to do it for every other linked content item. 
    1. For example, the Product content item includes an image, benefit items, and feature items. You need to create a language variant of those items as well.
    2. Don’t forget to check any other field in the content item. For example, the product content item includes a field called Regional availability, where you can set up the regions where the product is visible. You need to check if your target country is in the tags and add it if not. Another example is fields with rate and amount. Adjust the numbers based on the target country’s rules and currency.
  5. Translate the **unstructured content items** in the Page Builder (Rich text widgets, Page header widgets).
  6. Translate any other content item on the page still in the fallback language.
  7. Save and **Publish** the page.


Here is the final page compared to the one in the fallback language:
[![German language variant of a page](/docsassets/guides/multilingual-variant-of-product/multilingual-final-page.png)](/docsassets/guides/multilingual-variant-of-product/multilingual-final-page.png)
![]()
[]()[]()
