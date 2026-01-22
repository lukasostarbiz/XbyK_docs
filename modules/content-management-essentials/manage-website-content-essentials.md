---
source: https://docs.kentico.com/modules/content-management-essentials/manage-website-content-essentials
scrape_date: 2026-01-22
---

Module: Content management essentials
3 of 5 Pages
# Manage website content
Xperience by Kentico considers a website as one of the [communication channels](/guides/digital-marketing/work-with-channels/digital-marketing-channels) marketing teams use today to reach their target audience.
_Channels_ include various platforms, such as the main website, regional sites, brand-specific sites, campaign microsites or landing pages, social media platforms, emails, and distributed content like content snippets on third-party partner websites.
In Xperience, you will manage your website content through dedicated [website channels](/documentation/developers-and-admins/configuration/website-channel-management). The website channels vary from a single channel for smaller businesses to multiple websites and campaign-specific microsites for companies with more extensive website presence.
The Kbank demo site contains three website channels:
  * **Personal Banking** for consumers.
  * **Business Banking** website for businesses.
  * **Mortgages Microsite**.


Kbank demo site contains other digital marketing channels that we’ll discuss later in the tutorial.
## Content tree represents the hierarchy of website pages
Most items in the content tree represent pages visitors access on a live site. They navigate to the page’s URL addresses, which Xperience has created. Xperience uses the page’s position of the page in the tree and dynamically generates URLs depending on the hierarchy. You can customize **URL slugs** or create **vanity URLs** through the **Content** view mode.
[![News listing on Kbank demo site](/docsassets/guides/manage-website-content-essentials/kbank_news.png)](/docsassets/guides/manage-website-content-essentials/kbank_news.png)
## Content authoring in the website channel
You can **create the pages** , **edit their content** , or **manage their properties** in your website channel. Xperience organizes the pages in a visual hierarchy called the **content tree** , which you can see in the following screenshot below.
## Structured content in the website channel
Structured content stores data without any presentation information. The website’s _structured content_ is in the **Content** view mode. It is still possible to reuse this data. However, it’s not fully reusable across the system as the **reusable content** stored in the [Content hub](/modules/content-management-essentials/manage-content-hub-essentials).
For example, the data on the _Content_ view is specifically tied to a website page in the Kbank demo site.
[![Content view shows the website’s page structured content, such as page publishing date or its SEO-related fields.](/docsassets/guides/manage-website-content-essentials/structured-data-website-content-tab-view.png)](/docsassets/guides/manage-website-content-essentials/structured-data-website-content-tab-view.png)
Similarly, you can preview a webpage’s URL using the URLs view.
[![Page URL slug](/docsassets/guides/manage-website-content-essentials/cm-guide-url-slug.png)](/docsassets/guides/manage-website-content-essentials/cm-guide-url-slug.png)
## Page Builder in the website channel
Xperience **Page Builder** provides a user-friendly interface for creating website pages using components that you can drag and drop.
The components include [page templates](/documentation/developers-and-admins/development/builders/page-builder/page-templates-for-page-builder), [page sections](/documentation/developers-and-admins/development/builders/page-builder/sections-for-page-builder), and [widgets](/documentation/developers-and-admins/development/builders/page-builder/widgets-for-page-builder).
The following screenshot shows custom widgets that you can see on the Kbank demo site.
[![Dialog containing available widgets](/docsassets/guides/manage-website-content-essentials/cm-guide-page-builder-widgets.png)](/docsassets/guides/manage-website-content-essentials/cm-guide-page-builder-widgets.png)
Out-of-the-box, Xperience comes with two widgets - **Rich text** and **Form**. Based on your project requirements, developers prepare other widgets and define areas on the page where they are available.
### Build custom pages with Page Builder
Pages in Xperience website channels are based on [page templates](/documentation/developers-and-admins/development/builders/page-builder/page-templates-for-page-builder). Some page templates display only structured data and don’t allow editors to adjust their layout. More commonly, page templates contain Xperience **Page Builder** and enable editors to design pages to fit their needs.
**Page Builder** allows editors to create good-looking pages and experiment with your page’s layout and design. You edit pages with Page Builder using one or a combination of these components:
  * **Page templates** - Layout of individual website pages.
  * **Sections** - Layout what different page sections will look like.
  * **Widgets** - Display website content.


In this video, you can see how you can work with page sections.
In this video, you can see how you can work with widgets.
## Exercise 1 - Adding a new article
In this exercise, you’ll work with structured content in the _Content_ mode. You’ll create a new article and use the **Related articles** widget using the **Page Builder** mode.
**Task** :
A Content editor prepared a new article called _Travel insurance for family with kids_ and sent you the resources. You need to publish this article in the News section of the _Personal Banking_ channel. (You can find the article-related materials in the _Adding a new article_ folder in the [manage Content hub content](/modules/content-management-essentials/manage-content-hub-essentials#exercise---adding-structured-content).)
**Create a new page**
  1. Open the **Personal Banking** application.
  2. Select the _News_ section in the content tree.
  3. Use the **New page** button.
  4. Fill in the **Page name** : _Travel insurance for family with kids_.
  5. Select the _Article page_ content item.
  6. Confirm your choice with the **Continue** button.


**Add content to your new page**
In the following steps, use the _Travel-insurance-for-family-with-kids.docx_ (or _TravelInsuranceForFamilyWithKids.txt_) from the _Adding a new article_ folder.
  1. **Select article to display** : _Create new_. 
    1. Enter the **Content item name** : _Travel insurance family - article_.
    2. **Location** selector: Select a location for the content item. You can select _Uncategorized (no folder)_ or a specific location within the [Content hub folders](/documentation/business-users/content-hub/content-hub-folders).
    3. Select **Content type** : _Article_.
    4. Confirm by selecting the **Continue** button.
  2. Add the **Title** : _Travel insurance for family with kids_.
  3. **Add teaser image** : 
    1. Select **Create new** , and the _Create new item_ dialog opens.
    2. Fill in the **Content item name** : _Family on vacation_.
    3. Keep the _Asset_ content type and select **Continue**.
    4. Add the **Alt text** : _A family with two kids is standing on the beach before a sunset_.
    5. Upload the **File** named _family-on-vacation.jpg_.
    6. **Publish** the image to see it on the live site.
  4. Paste the **Summary** from the _Travel-insurance-for-family-with-kids.docx_ or _TravelInsuranceForFamilyWithKids.txt_ file.
  5. Paste the main **News Text** of the article, use the **Rich text** field editor, and style your text.
  6. Add an image to the article: 
    1. Find the place in the **News Text** where you should add the image (in our case, before the insurance benefits).
    2. **Insert image** via the button that you find in the **Rich text editor**. 
      1. In the **Content hub** , search for the _Family premium account_ image.
      2. **Insert** the image.
    3. Use the built-in **image editor** in the Rich text and **Change size** to _100%_. The image will adjust to 100% of the width of the article column.
  7. In Select article type, use **Select tags** and add _News_ tag to categorize the article.
  8. **Select related articles** : 
    1. Use **Select existing**.
    2. Pick _Platinum Debit Card_ , _Travel insurance: Young adventurers_ , and _Travel insurance: Retirees_ articles.
    3. Confirm with the **Select** button.
  9. Add **SEO title** : _Travel safely as a family with kids with Kbank family travel insurance_.
  10. **SEO description** : _Learn how the travel insurance for family with kids can help you in the time of need. Whether you need to buy new clothes because of lost luggage, or need to cover unexpected health expenses_.
  11. Use the **Save** button after you complete your updates.
  12. Use **Change workflow step** to process the article through the workflow steps: 
    1. Select **Ready for approval** step and **Confirm**.
    2. Select **Ready for publishing** step and **Confirm**.
    3. Select **Schedule/Publish** the article and **Publish now**.


**Reload the News page to show the newest articles**
If you visit the _News_ page on the live website, you’ll see that your article is at the bottom of the widget. If you don’t see the **Teaser image** , go to the **Content hub** and publish the image.
## Preset page templates
In the previous exercise, you’ve used an existing a **page template**. What are [page templates](/documentation/developers-and-admins/development/builders/page-builder/page-templates-for-page-builder)?
**Page templates** are a game-changer when you anticipate creating multiple pages with identical layouts. They become invaluable when editors collaborate on various pages. When editors create their pages, they can build them using existing templates containing a preset configuration of where and how the data is displayed. And your editors can focus on adding engaging content.
Xperience supports two types of page templates:
  * **Default page templates** are created by developers for every Xperience website. You’ve used it in the [previous exercise](#exercise-1---adding-a-new-article) previous exercise to create the article about travel insurance.
  * **Preset page templates** are saved by editors using pages created from default page templates.


Let’s see how you can create custom _preset page templates_.
The following examples use Kentico’s _Kbank_ demo website. The available widgets, page types, and visuals will depend on your company’s implementation of Xperience by Kentico.
### Save an existing page as a preset
Editors can save an existing page configuration as a template, whether it is already published on the live site or in a draft stage. Saving a page as a template doesn’t influence the original page configuration.
If you want to make adjustments to the template layout, first save the original page as a template and then work with the new template itself:
  * Create a new page using the template.
  * Update the page’s configuration (add or remove widgets, change the number of columns in sections, or change the section’s background color).
  * Save this new configuration into a new template.


Xperience will not change the pages you created using the old template.
## Exercise 2 - Save page as preset page template
In this exercise, you’ll see what it’s like to save existing pages as page templates that you’ll use to create new pages.
**Task** :
Let’s pretend you created a successful campaign page and want to save it as a template for similar future campaigns.
  1. Select the page in the content tree, switch to the **Page Builder** mode if necessary, open the additional page actions, and save your page as a _preset template_.  
[![Save configuration as a new preset template](/docsassets/guides/manage-website-content-essentials/save-as-template.png)](/docsassets/guides/manage-website-content-essentials/save-as-template.png)
  2. Fill in the preset **Name** , add **Description** , and adjust the **Icon**.  
[![Add description to preset template](/docsassets/guides/manage-website-content-essentials/new-template-name-description.png)](/docsassets/guides/manage-website-content-essentials/new-template-name-description.png)


Depending on the widget configuration, the page template will contain different data. For example, the _Hero banner_ on the _Kbank_ website:
  * **Current product page** : the widget will be empty. Why? When you save the configuration as a page template, the reference to the reusable product item that contains the data is removed.
  * **Select product page** : links content from another product page.
  * **Select hero content item** : uses the custom hero content item and shows it in the widget.


This previous list only sums up how complex widgets, such as the _hero banner_ , influence what data is saved into the page template. You can run into different options depending on the widget’s properties you use. For example, if the _hero banner_ displays data from the **Current product page** and contains a **CTA** label with a link, Xperience will save both the **CTA** label and the link to the template.
Once you learn how to create page templates in Xperience, experiment with the Kbank demo site to test different configuration options. And you can also find out more about [reusing content through the Hero widget](/guides/digital-marketing/work-with-reusable-content/reuse-content-with-the-hero-widget).
### Preset page templates application
The template is of the same webpage content type that you created the template from. In this example, the _Car loan_ page is a _Content page type_ , so the template shows under the _Content page_.
[![Create a new page from a content type](/docsassets/guides/manage-website-content-essentials/page-content-type.png)](/docsassets/guides/manage-website-content-essentials/page-content-type.png)
## Build a page template from scratch
The second option for creating preset page templates is to start with a blank canvas and save the template for future use with an empty configuration. This means the template will keep all the widgets you added, but the widgets will be empty content-wise.
### Exercise 3 - Create a new preset page template
In this exercise, you’ll see what working with structured content in the _Content_ mode is like. You’ll create a new article and add widgets to the page using the _Page Builder_ mode.
**Task** :
The following example will teach you how to create a template for product pages on _Personal Banking_ website.
  1. Create a **New page** , fill in a name, and select the page type you want to use for this template, in this case, a _Product page_.  
[![Create a new page](/docsassets/guides/manage-website-content-essentials/new-page-content-type.png)](/docsassets/guides/manage-website-content-essentials/new-page-content-type.png)
  2. Select the **Default page** template. You will see only the default options if this is your first product page template. In _Kbank_ , developers prepared the _Product page_ template.  
[![Add a default page template to a new page](/docsassets/guides/manage-website-content-essentials/product-page-template.png)](/docsassets/guides/manage-website-content-essentials/product-page-template.png)
  3. Save your new page and go to the **Page Builder** tab, where you’ll prepare the layout using the widgets you see fit for the page.  
[![Save new page](/docsassets/guides/manage-website-content-essentials/new-page-save.png)](/docsassets/guides/manage-website-content-essentials/new-page-save.png)
  4. Let’s add some widgets to our _Kbank_ example page, like a _hero banner_ , _product_ , or _benefits_ widget. To add a widget, select the **plus button** in the middle of the section and choose the desired widget.  
[![Add widget dialog](/docsassets/guides/manage-website-content-essentials/page-builder-add-widget.png)](/docsassets/guides/manage-website-content-essentials/page-builder-add-widget.png)
  5. Once the widgets are set up, save the changes made to the layout and save it as a **preset template**.  
[![Saving widget configuration into preset page template](/docsassets/guides/manage-website-content-essentials/new-page-save-template.png)](/docsassets/guides/manage-website-content-essentials/new-page-save-template.png)
  6. **Name** the template, add a **Description** to help content creators pick the right template, and select an **Icon** to distinguish between templates visually.  
[![Preset page template properties](/docsassets/guides/manage-website-content-essentials/new-template-name-description-v2.png)](/docsassets/guides/manage-website-content-essentials/new-template-name-description-v2.png)
  7. The new template will be available immediately.  
[![Product page template](/docsassets/guides/manage-website-content-essentials/template-product-page.png)](/docsassets/guides/manage-website-content-essentials/template-product-page.png)


You can now use your new page template to create a new page. To test what’s creating new pages using templates is like, you can follow steps in the [Create a new product page](/guides/digital-marketing/work-with-websites/Create-a-new-product-page) guide.
[ Previous page ](/modules/content-management-essentials/manage-content-hub-essentials)
3 of 5
[ Mark complete and continue ](/modules/content-management-essentials/manage-files-essentials)
![]()
[]()[]()
