---
source: https://docs.kentico.com/guides/digital-marketing/xperience-essentials/manage-content-hub-essentials
scrape_date: 2026-01-26
---

  * [Home](/guides)
  * [Digital marketing](/guides/digital-marketing)
  * [Xperience essentials](/guides/digital-marketing/xperience-essentials)
  * Manage content in Content hub 


# Manage content in Content hub
Xperience **Content hub** is an application that comes with every Xperience application. It is a central repository for all your **reusable content**. It stores [structured content](/guides/architecture/content-modeling/content-modeling-guide/structured-and-unstructured-content) without presentation information, which significantly helps with content reuse and content governance over the content’s lifetime.
## Adding content into the Content hub
When you create a new content item, you’ll input your content in a form-like editing interface. You don’t need to pay attention to what the content will look like on the website and what you need to adjust and tweak to adhere to your brand design guidelines. You can focus on the quality of information your content conveys.
Your developers ensure that your application correctly presents content across different marketing channels. The following image shows how the structured _Featured content_ looks displayed on the website.
[![Featured content](/docsassets/guides/manage-content-hub-essentials/featured-content-display.png)](/docsassets/guides/manage-content-hub-essentials/featured-content-display.png)
## Filtering content in the Content hub
_Content hub_ also comes with filters that help you quickly find the item you are looking for.
[![Filters in Content hub](/docsassets/guides/manage-content-hub-essentials/cm-guide-content-hub-filtering.png)](/docsassets/guides/manage-content-hub-essentials/cm-guide-content-hub-filtering.png)
## Content publishing workflows
Each content created in Xperience _Content hub_ or other channels has a _content publishing workflow_.
[Workflows](/documentation/developers-and-admins/configuration/workflows) ensure that no content is published without proper review. Xperience contains a _default_ workflow that runs for every content item. Developers and admins of Xperience projects can use this default workflow or customize it as needed.
**What does the _default_ workflow look like?**
When you create a content item, Xperience puts the item into a **Draft** step. You can add your data, change its design, or reference other existing content. Once you save your changes, the data (and, in the case of _pages_ , also their design) is available for preview in the administration interface. Still, it’s not available to your audience. The content item becomes publicly available only once you **Publish** it.
You can _edit_ an existing content item to update its content. Editing the content item creates a new version but keeps the original content public. The content version is in the _Draft_ status and becomes publicly available only after you _Publish_ this new version.
The Kbank demo site contains examples of two custom workflows - _Article publishing workflow_ and _Article page publishing workflow_. You’ll work with [both workflows](/guides/digital-marketing/xperience-essentials/manage-website-content-essentials#exercise-1---adding-a-new-article) in this tutorial.
**Task**
### Exercise - Adding structured content
You’ll need the Kbank demo website to follow up on the exercises. If you already haven’t, visit the [Kentico website](https://www.kentico.com/services/training/training-website-requests) and request the _Xperience by Kentico – Business Tutorial Kbank demo site_. You’ll receive an automated confirmation email. Once you approve the request, we will automatically send you a link to the demo site.
**Download tutorial resources**
You can also download all the resources you’ll need for the tutorial: [Business Banking Tutorial materials](https://download.kentico.com/Training/Tutorial+materials/Adding+a+new+article.zip).
In this exercise, you’ll see how easy it is to work with items in the _Content hub_ by adjusting an existing page and creating a new segment.
**Task** :
Your copywriter asked you to improve the _Loans_ page in the _Personal Banking_ channel by adding a FAQ widget.
  1. Go to the **Personal Banking** application.
  2. Select the _Loans_ page in the content tree and switch to the **Page Builder**.
  3. Select the **Edit page** and create a new version of the page.
  4. Use the **plus** button on the left side to add a new section below the _Kbank loans overview_. In the _Sections_ dropdown, pick the **Multi column** section type.
  5. Use the **plus** button in the middle of the new section to add the **Faq** widget.
  6. On the widget banner, open the **Configure widget** properties.
  7. Use the **Select existing** button to add your FAQ items to the widget. 
    1. Switch to the **Personal Banking** workspace using the dropdown selector, select the following items, and confirm with the **Select** button: 
      1. _Personal loan DON’Ts_.
      2. _Consolidation loan DON’Ts_.
      3. _Consolidated loan applicants_.
    2. Fill in the **Title** : _FAQ_.
    3. **Apply** to save the changes.
  8. **Save** the changes on the _Loans_ page.
  9. **Publish** the page to see the _FAQ_ element on the live site.


## Reusing content doesn’t create content copies
In the exercise above, you’ve used reusable content. From a technical perspective, reusing FAQs doesn’t create a copy of each content item on the backend.
Xperience created a _reference_ to the original item in the _Content hub_ , and developers ensure the referenced data is displayed correctly. That’s why keeping your content in sync across multiple instances is easy when you store it in the _Content hub_.
You can go to the **Content hub** , switch to **Personal banking** workspace, and search for Personal loan DON’Ts content item. Select the Personal loan DON’Ts content item and switch to the **Usages** tab. When you switch to the **In channels** view on the right-hand side, you’ll see that the item is referenced from the _Loans_ and _Personal Loan_ pages.
## Reusing content makes updating easier
Since reusable content doesn’t contain presentation information, you can easily reuse it in other channels, such as email. When you update the content item in the _Content hub_ , your change is promoted everywhere the content is reused.
### Locate where your content is used
Before you edit or remove any content item, you can review impact of your changes with the _Content Reuse Locator_. You’ll find [this tool](https://www.kentico.com/discover/blog/content-reuse-locator) in the **Used in** [tab](/documentation/business-users/website-content#used-in) of any content item details within _Content hub_.
The _Used in_ tab shows every page or content item that references the item you are editing. This allows you to confidently change, update, or remove the edited content item without disrupting the content experience, for example, on a website page that displays this item.
[![See where your content is used before you make any changes](/docsassets/guides/manage-content-hub-essentials/content_reuse_locator_used_in_content_item.png)](/docsassets/guides/manage-content-hub-essentials/content_reuse_locator_used_in_content_item.png)
**Why use structured content?**
Modern marketing teams do not need to recreate or copy-paste the content on their websites.
They strategically design their content model, **allowing content reusability** and **resurfacing** based on its context or marketer’s needs. When you store your data in a structured format, you can easily **reassemble** it into a different content type, **present** it through a different design, or **repurpose** it in other [marketing channels](https://en.wikipedia.org/wiki/Marketing_channel).
## Next step
You’ve learned about the _Content hub_ and added reusable content to a website channel. Continue to learn about the website channel and [working with website pages](/guides/digital-marketing/xperience-essentials/manage-website-content-essentials).
![]()
[]()[]()
