# Model navigation
  * [ Copy page link ](modules/content-modeling-guide/model-navigation#) | [Get HelpService ID](modules/content-modeling-guide/model-navigation#)
Core MVC 5


[✖](modules/content-modeling-guide/model-navigation# "Close page link panel") [Copy to clipboard](modules/content-modeling-guide/model-navigation#)
Website navigation helps users find the content they are looking for. Well-ordered navigation menus provide an overview of how the website’s content or its segments are structured. It can also keep users engaged and lead them to explore more of the site.
There are different approaches to modeling navigation that you can consider. The following text highlights the recommended approach. At the same time, your project requirement might differ and you’ll need to reach for a different approach for modeling navigation. That’s why we have collected examples suitable for Xperience projects into a dedicated [model website navigation](guides/architecture/content-modeling/model-website-content-types/model-website-navigation) guide.
We recommend modeling website navigation via reusable [content items](documentation/business-users/content-hub). Developers prepare dedicated content types for individual _Navigation items_ as well as for the _Website navigation_ as a whole. Editors then manually build the website navigation in the content hub. Based on the site’s designs, they create the hierarchy and desired order of individual menu items that developers then display in the relevant locations. The editors can also organize the content items used for website navigation into [folders](documentation/business-users/content-hub/content-hub-folders#content-folders) in the content hub. This way they can make the menu hierarchy more visual and easily see in which channel application the navigation items are used.
What about modeling navigation using dedicated [website pages](documentation/business-users/website-content) without URLs within the content tree which was common in Kentico 13 and older versions?
In Xperience, this approach is less optimal, as it clutters the content tree with items that do not represent actual pages on the live site, which can be confusing to your content editors. See the [guide about navigation](guides/architecture/content-modeling/model-website-content-types/model-website-navigation) to learn about additional options and practices to follow if you decide to place navigation into the content tree.
You can also ask developers to build a custom _Menu widget_. Editors can place the widget into appropriate section and select which content items the widget processes into a submenu. Similarly, you can request a custom _Breadcrumbs menu_ widget. When an editor places the widget onto the page, the widget automatically displays the breadcrumb path to the page’s location.
Depending on your project’s requirements, the _Navigation item_ content type may contain some or all of the following properties:
  * Page to link to – a page from the content tree that is selectable using the [Page selector](documentation/developers-and-admins/customization/extend-the-administration-interface/ui-form-components/reference-admin-ui-form-components). The selector populates its assigned field with the GUID of the selected page, which developers can use to [retrieve the URL](documentation/developers-and-admins/development/content-retrieval/retrieve-page-content/retrieve-page-urls) of the linked page.
  * Depending on the project’s requirements, you may want to add some of the following fields: 
    * External link 
      * Developers can use the [URL validation rule](documentation/developers-and-admins/customization/extend-the-administration-interface/ui-form-components/ui-form-component-validation-rules) to ensure the URLs passed by editors are valid.
    * Link title
    * Link caption
    * Link icon
    * Additional properties (e.g., open in a new tab)


The _Website navigation_ content type then contains all the _Navigation items_ that are displayed in the navigation menu.
For more information and a sample implementation, see [this page of our Developer kickstart](guides/development/developer-kickstart/model-navigation). For complex overview of approaches to modeling navigation, see a dedicated guide [Model website navigation](guides/architecture/content-modeling/model-website-content-types/model-website-navigation).
[ Previous page ](modules/content-modeling-guide/content-modeling-digital-marketing-features)
24 of 28
[ Mark complete and continue ](modules/content-modeling-guide/website-system-pages)
  * [Community Questions & Answers](https://community.kentico.com/q-and-a)
  * [Contact support](https://community.kentico.com/support)


### Cookie consent
We use necessary [cookies](https://www.kentico.com/cookies-policy) to run our website and improve your experience while browsing. Additional cookies are only used with your consent. You may revoke your consent on the [Cookies Policy](https://www.kentico.com/cookies-policy) page or in your browser at any time. 
ACCEPT ALL  [Configure](https://www.kentico.com/cookies-policy)
USE ONLY NECESSARY 
![](https://docs.kentico.com/modules/content-modeling-guide/model-navigation)
[](https://docs.kentico.com/modules/content-modeling-guide/model-navigation)[](https://docs.kentico.com/modules/content-modeling-guide/model-navigation)