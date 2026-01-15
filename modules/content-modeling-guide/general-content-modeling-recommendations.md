# General content modeling recommendations
  * [ Copy page link ](modules/content-modeling-guide/general-content-modeling-recommendations#) | [Get HelpService ID](modules/content-modeling-guide/general-content-modeling-recommendations#)
Core MVC 5


[✖](modules/content-modeling-guide/general-content-modeling-recommendations# "Close page link panel") [Copy to clipboard](modules/content-modeling-guide/general-content-modeling-recommendations#)
**Routing and URLs**
We recommend using [content tree-based routing](documentation/developers-and-admins/development/routing/content-tree-based-routing) for most scenarios. With content tree-based routing, the system generates ULRs for pages based on their position in the content tree. The content tree-based routing also allows editors to easily [change the URL slugs of their pages](documentation/business-users/website-content/manage-page-urls) and store the URL history in the **Former URLs** application. 
Sometimes, editors use characters considered suboptimal (or even invalid) for URLs. For example, some accented characters from extended Latin alphabets, such as _ö_ , can be encoded into SEO-suboptimal URL slugs. We recommend building [custom field validation](documentation/developers-and-admins/customization/extend-the-administration-interface/ui-form-components/ui-form-component-validation-rules) on any URL-related page type fields and constraining forbidden (or suboptimal) URL characters. To validate custom URL fields, your code can reference the _Settings_ for URLs in the administration. You can use the `ISettingsService` and [retrieve specified URL restrictions](documentation/developers-and-admins/configuration/settings).
**Aim for content reusability**
Follow a simple rule of thumb: if there is a chance that specific content will appear on the same website in multiple places or various channels of marketing communication, use structured content and **store your reusable content items in the Content hub**.
**Ensure a seamless editing experience**
When you prepare the structured content hierarchy, we recommend that every developer participating in the project steps into the content editor’s shoes and tests their workflow by creating several pages in the content tree. Developers should try to understand how editors will work with the website content. Knowing the inner workings of their implementation, they can suggest changes to improve the editing experience significantly. Talking from experience, you can often:
  * Slightly flatten the content tree hierarchy.
  * Convert large, monolithic content types that aggregate various content units storing different types of data into smaller, separate content types. Using these smaller content types, editors will compose content, such as campaign pages or app screens.
  * Use content tree predominantly only for navigable items with URLs.
  * Guide editors on how they should work with specific content types or what kind and format of data they should input in the content items. 
    * We recommend **specifying editor guidance** in the project requirements. To build a flexible and scalable content model, use editor guidelines to strengthen the content reusability and independence. It helps strengthen its reusability when you concentrate on what the content is rather than on its presentation. Your guides need to help editors focus on the type of content they should input rather than describing what their content will look like on the website.
    * Use **different icons** to provide visual guidance and distinguish between different content types.
    * Use the **Tooltip text** or **Text below the input** settings for content type fields to provide editor guidance.
    * As Page Builder is preferably used to present the information rather than store it, use **Label** and **Explanation text** widget and section properties to help editors understand the data their content needs and what will happen once displayed in the dedicated channel.
  * If your editors use widgets to display structured content, **set restrictions against a specific content type** within the widget properties.
  * Help editors quickly compose their pages. Request that developers set restrictions on editable areas, sections, and widget zones to limit confusion about which widget to choose. Use appropriate components to ensure editors cannot stray and input widgets in places where these widgets don’t belong. Setting up these fences helps with the content editing experience and ensures brand consistency.


**Ensure seamless content workflow**
Make sure that editors have complete control over their content. **Preview** tab in website channel applications allows editors to see what their content looks like before publishing.
We recommend that
  * Editors create and manage their content on the production version of the application,
  * Developers use a pre-production UAT environment to create, test, and prove their code changes before synchronizing it with the production site.


[ Previous page ](modules/content-modeling-guide/website-system-pages)
26 of 28
[ Mark complete and continue ](modules/content-modeling-guide/additional-resources-about-content-modeling)
  * [Community Questions & Answers](https://community.kentico.com/q-and-a)
  * [Contact support](https://community.kentico.com/support)


### Cookie consent
We use necessary [cookies](https://www.kentico.com/cookies-policy) to run our website and improve your experience while browsing. Additional cookies are only used with your consent. You may revoke your consent on the [Cookies Policy](https://www.kentico.com/cookies-policy) page or in your browser at any time. 
ACCEPT ALL  [Configure](https://www.kentico.com/cookies-policy)
USE ONLY NECESSARY 
![](https://docs.kentico.com/modules/content-modeling-guide/general-content-modeling-recommendations)
[](https://docs.kentico.com/modules/content-modeling-guide/general-content-modeling-recommendations)[](https://docs.kentico.com/modules/content-modeling-guide/general-content-modeling-recommendations)