---
source: https://docs.kentico.com/modules/content-modeling-guide/avoid-creating-channel-specific-content-from-linked-content-fragments
scrape_date: 2026-01-26
---

Module: Content modeling guide
16 of 28 Pages
# Avoid creating channel-specific content from linked content fragments
Customers who are used to displaying their content (stored in a CMS) on the website love using visual builders to create website content. Xperience offers **Page Builder** , which allows editors to assemble web pages using components like hero banners, text and image blocks, reviews, testimonials, and FAQs. For simplicity, we’ll call these components “content fragments.” These fragments aren’t semantic; they hold specific pieces of data that represent only a part of the overall information needed to convey the complete meaning of the content.
While this approach makes it easy for editors to craft entire web pages quickly in Xperience, it has limitations. The most significant advantage is speed and simplicity, but only when you’re only concerned with presenting content on a single website channel. However, this method ties the content exclusively to the specific web page, meaning the fragments can’t be easily queried or reused across different channels in Xperience. By composing their content type using widgets, editors lock their content only within the context of that specific web page.
To build a more flexible, multi-channel solution, we recommend creating semantically defined reusable content types that are made up (composed or linked) from other reusable components. This approach ensures that the content isn’t just relevant to one specific channel but can be used and adapted across different platforms. It also makes the content queryable, structured, and easier to manage in the long term, giving developers more control over its use. We’ll discuss the _Page Builder_ in detail in a [dedicated material](/modules/content-modeling-guide/design-website-content).
### Storing reusable content in Content hub vs. custom module classes
Both options offer unique advantages when storing [reusable content](/documentation/business-users/content-hub) in _Content hub_ or [custom module](/guides/development/customizations-and-integrations/create-basic-module) classes. From a user perspective, we recommend considering the _Content hub_ before choosing custom module classes.
The **Content hub** provides default Kentico functionality, including editing workflows, multilingual support, usage reports for improving content curation, and a built-in user interface. Additionally, the Content hub allows editors to easily manage and reuse content across various channels, making it highly flexible for developers and editors. Using Content hub often means developers don’t have to create custom UI components, saving time and effort.
A key benefit of the Content hub is its ability to integrate with APIs, enabling content creation from third-party sources. Upcoming features are expected to enhance this functionality further, making it an even more powerful tool. Using the Content hub, developers can focus on supporting content management through out-of-the-box components rather than building custom solutions, leading to quicker deployment and easier maintenance.
### Make designing content generic
Though you might want to store design-specific information, like exact color codes or sizes, inside the channel-specific content types, we do not recommend doing this. From a content longevity perspective, you want to have your design information as agnostic to the visual design as possible. When you decide to, for example, redesign your brand, it’s easier to remap the generic class name to new colors than to change the specific _#F05A22_ brand color code in hundreds of different places. The following screenshot shows styling options in the Kbank demo site.
[![Styling options](/docsassets/guides/store-content/cm-guide-section-properties.png)](/docsassets/guides/store-content/cm-guide-section-properties.png)
[ Previous page ](/modules/content-modeling-guide/use-the-built-in-admin-ui-form-components)
16 of 28
[ Mark complete and continue ](/modules/content-modeling-guide/design-website-content)
![]()
[]()[]()
