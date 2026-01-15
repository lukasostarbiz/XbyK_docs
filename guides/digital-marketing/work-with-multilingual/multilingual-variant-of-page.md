# Multilingual variant of a page
  * How-to| [ Copy page link ](guides/digital-marketing/work-with-multilingual/multilingual-variant-of-page#) | [Get HelpService ID](guides/digital-marketing/work-with-multilingual/multilingual-variant-of-page#) | This page is part of a module: [ Manage content ](modules/manage-content)
Core MVC 5


[✖](guides/digital-marketing/work-with-multilingual/multilingual-variant-of-page# "Close page link panel") [Copy to clipboard](guides/digital-marketing/work-with-multilingual/multilingual-variant-of-page#)
In the previous article, you’ve read about [setting up multilingual on Kbank](guides/digital-marketing/work-with-multilingual/setting-up-multilingual-on-kbank). Now, you can start translating the pages as needed.
Translating your company website helps you reach a broader audience and provide a better user experience, which leads to higher conversion rates.
Let’s see how to translate a page into your targeted language.
## See the AIRA translation workflow
Watch a short video to see the whole translation workflow. 
Want to test how translations work? Follow the rest of the guide to get a hands-on experience.
#### Prerequisites:
  * Have the Kbank demo site set up and running to test the scenario.
  * Know what [multilingual in Xperience by Kentico](guides/digital-marketing/work-with-multilingual/multilingual-variant-of-content) means and how it works.
  * Know how to [set up multilingual on Kbank](guides/digital-marketing/work-with-multilingual/setting-up-multilingual-on-kbank).


## Create a new language variant of a page
### Localize untranslated pages
When translating a page, you need to know which one to pick so you don’t work on something already translated. Xperience by Kentico shows you the translation status at first glance. Just switch to the target language using the drop-down selector in the top left corner and see the icons next to the pages in the content tree:
  * The alphabet sign means the page is **not translated**.
  * The pencil says the translated page is in draft but **has not been published** yet.
  * The green tick sign tells you the translated page **is published**.


[![Status of translated pages in the content tree](docsassets/guides/multilingual-variant-of-page/content-tree-language-variant.png)](https://docs.kentico.com/docsassets/guides/multilingual-variant-of-page/content-tree-language-variant.png)
### Create the language variant
When creating a language variant of a page, you’re preparing a base for the content you’ll translate later. You need to take care of things in the background before you prepare the content visitors see when they visit your website.
In this article, we’ll create a language variant of the _Travel Guard Insurance_ page in the demo Kbank site.
#### Think about SEO
SEO properties are an essential part of any web page. Translating **SEO** properties to the targeted language supports the page in local search engines, so the target audience can find it easier. On the other hand, not translating the SEO properties could promote the translated page to the audience of the [fallback language](guides/digital-marketing/work-with-multilingual/multilingual-variant-of-content#what-is-a-fallback-language).
For example, if your company’s website is in English, and you decide to add a Spanish version but keep the SEO properties in English, the English audience can find the pages in Spanish. At the same time, the Spanish audience may miss the translated pages, and the number of visitors and potential conversions will be lower.
#### Add SEO-related content
Let’s first add some English SEO metadata to the **TravelGuard Insurance** product page.
You can use the following values:
  * SEO Title: _TravelGuard Insurance by KBank – Secure Your Trip_
  * SEO Page description: _Protect your travels with TravelGuard Insurance from KBank. Covers cancellations, emergencies, baggage, and delays. Peace of mind for every journey._


[![Adding the SEO fields](docsassets/guides/multilingual-variant-of-page/seo-language-variant.png)](https://docs.kentico.com/docsassets/guides/multilingual-variant-of-page/seo-language-variant.png)
#### Add Open Graph content
If your company is active on social media to promote content, translate the **Open Graph** fields as well. If your developers implemented a custom connector app to share your content on your social media profiles, filling data in the OG properties creates a social media post, both the headline and the text, in the desired language.
Let’s add _OpenGraph content_ to the _TravelGuard Insurance_ page to see how translation works. You can use the following values:
  * OpenGraph Title: _TravelGuard Insurance – Comprehensive Travel Protection_
  * OpenGraph Description: _Travel worry-free with KBank’s TravelGuard Insurance. Get coverage for emergencies, cancellations, and baggage wherever you go._
  * Facebook Title: _TravelGuard Insurance by KBank – Secure Your Trip_
  * Facebook Description: _Enjoy secure travel with KBank’s TravelGuard Insurance. Covers cancellations, emergencies, baggage, delays, and more for every traveler._


[![Open Graph fields](docsassets/guides/multilingual-variant-of-page/open-graph-fields.png)](https://docs.kentico.com/docsassets/guides/multilingual-variant-of-page/open-graph-fields.png)
### Translate page using AIRA
**AIRA translations** let editors quickly generate language variants of existing content without manual translation or external tools. You can translate pages (including their Page Builder content) and reusable content items in the Content hub.
When you start translating the page, AIRA creates a draft you can _review_ , _publish_ , or _discard_. The translation process is customizable within your Xperience application. You can define brand-specific terminology, exclude certain words, and set formatting rules in the [AIRA application](documentation/developers-and-admins/configuration/aira-configuration#configure-aira-translations). Note that bulk translation and automatic translation of linked items aren’t yet supported, and you have to trigger the translation of referenced pages and content items manually.
Let’s translate the _TravelGuard Insurance_ page.
  1. Make sure you have selected the _TravelGuard Insurance_ page.
  2. Use the **language selector** in the top menu and select the Spanish version, i.e., _Español_.
  3. Select **Translate using AIRA**.
  4. Use **Continue** to confirm.
  5. Wait for AIRA to complete translating.


### Adjust the page’s URL slug
URL is the next important thing you need to consider. If you leave the **URL** as is, which you can see in the example below, the URL includes the language prefix. Still, the URL slug is the same as in the original language. You can adjust the URL slug to match the page’s targeted language.
[![Adjusting the URL slug](docsassets/guides/multilingual-variant-of-page/url-slug-language-variant.png)](https://docs.kentico.com/docsassets/guides/multilingual-variant-of-page/url-slug-language-variant.png)
### Validate content translation
After AIRA completes the translation, validate the output. Check for errors introduced by the language model, such as mistranslations, inconsistencies, or terminology that conflicts with your brand guidelines or technical standards. Pay particular attention to inappropriate naming conventions or phrasing that misrepresent your product or service.
[![Validate the translation of different content fields.](docsassets/guides/multilingual-variant-of-page/seo-fields-translated-using-aira-translation.png)](https://docs.kentico.com/docsassets/guides/multilingual-variant-of-page/seo-fields-translated-using-aira-translation.png)
### Check other fields that don’t hold content
You may have other fields to think about based on your project implementation in your Xperience application. Some fields are not text fields, so they don’t need to be translated, but it’s good to think about them.
For example, on our Kbank demo site, we have the _Page publish date_. You may want to decide whether to keep it the same as the original page or use a different date.
[![Not all fields have to be translated](docsassets/guides/multilingual-variant-of-page/publish-date.png)](https://docs.kentico.com/docsassets/guides/multilingual-variant-of-page/publish-date.png)
### Preview the translated page
Now that you have translated or adjusted all the fields, **save** the page and review the translated content.
In our example, the page is a product page, so a product content type is linked directly to the page. However, not every page has a content item linked to the page itself; instead, content items are linked to the widgets.
[![Product content item linked to the newly created page](docsassets/guides/multilingual-variant-of-page/not-translated-content-item.png)](https://docs.kentico.com/docsassets/guides/multilingual-variant-of-page/not-translated-content-item.png)
## Next step
As we mentioned above, the Kbank website we use in this example has a product page with the product linked directly to it. This means we’ll translate the product into the _Content view_. For other content items, we’ll switch to the _Page Builder_.
In the following guide, prepare the [multilingual variant of a content item](guides/digital-marketing/work-with-multilingual/multilingual-variant-of-product).