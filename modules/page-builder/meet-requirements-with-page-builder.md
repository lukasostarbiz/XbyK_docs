# Look over the requirements
  * [ Copy page link ](modules/page-builder/meet-requirements-with-page-builder#) | [Get HelpService ID](modules/page-builder/meet-requirements-with-page-builder#)
Core MVC 5


[✖](modules/page-builder/meet-requirements-with-page-builder# "Close page link panel") [Copy to clipboard](modules/page-builder/meet-requirements-with-page-builder#)
Let’s examine how and when to apply [_Page Builder_](documentation/developers-and-admins/development/builders/page-builder) in Xperience by Kentico projects.
## Consider when to apply Page Builder
Page Builder appeals to content editors because it provides flexibility. It empowers non-technical teams to design pages without pestering a developer whenever they need to tweak visual elements and audition visual changes to pages.
Page Builder also allows editors to set up content [personalization through page widgets](documentation/developers-and-admins/digital-marketing-setup/content-personalization) so that site visitors see different content based on their activities.
For these reasons, Page Builder is well suited to landing pages and other marketing-heavy applications. However, it can do more harm than good in cases that require strict uniformity among pages.
## Appraise the available components
Once you’ve decided that Page Builder suits your requirement, it helps to understand the three main types of component.
### Widgets
[Page Builder widgets](documentation/developers-and-admins/development/builders/page-builder/widgets-for-page-builder) are the most granular Page Builder components. They are pieces of content and/or functionality that editors can move between the available zones on a page.
Widgets often have properties which can be configured to influence their appearance and the data they display. They are able to display data from virtually any location accessible through a .NET application, including:
  * The widget’s own properties.
  * The page the widget is on, including any [linked content items](documentation/business-users/content-hub/content-items).
  * Any other page or content item in the site, specified through a [Page selector](documentation/developers-and-admins/customization/extend-the-administration-interface/ui-form-components/reference-admin-ui-form-components#page-selector) or [Content item selector](documentation/developers-and-admins/customization/extend-the-administration-interface/ui-form-components/reference-admin-ui-form-components#combined-content-selector).
  * Any Xperience object type, specified through an [Object selector](documentation/developers-and-admins/customization/extend-the-administration-interface/ui-form-components/reference-admin-ui-form-components#object-selector) or [General selector](documentation/developers-and-admins/customization/extend-the-administration-interface/ui-form-components/reference-admin-ui-form-components#general-selector).
  * Any external source, for example a third-party database, GraphQL endpoint, or REST API. 
The data retrieved from the external source can optionally be based on parameters configured through widget properties. 


Widgets can also [make POST requests](documentation/developers-and-admins/development/builders/page-builder/widgets-for-page-builder#handle-post-actions) to endpoints in order to handle data entry by site visitors.
### Sections
[Page Builder sections](documentation/developers-and-admins/development/builders/page-builder/sections-for-page-builder) are configurable views that determine where widgets can be added, and the visual design of these areas. They contain _Widget zones_ , which are like slots that widgets can fit into. Widget zones can restrict which widgets are allowed within them.
Page Builder sections can be based on view components, in order to implement complex display logic. For example, they can [hide their contents if the site visitor has not accepted a certain consent agreement](guides/development/activities-and-marketing/hide-builder-content-from-visitors-who-have-not-consented-to-tracking).
**Sections and data**
It is possible to include functionality similar to widgets in Page Builder sections. For example, you can use section properties to query and display data. However, we recommend using them only for the visual and structural design of the page, like providing a styled layout and including anchors in the page content.
There is no advantage to using sections instead of widgets for displaying or posting data, and blending the functionality of the two by using sections in an unintended manner might confuse business users.
### Page templates
[Page templates](documentation/developers-and-admins/development/builders/page-builder/page-templates-for-page-builder) are configurable views that can display content, determine where sections can be added, or both. Templates can be general-purpose, or associated with a specific page type, in which case they can display structured data from that page type.
Page templates can contain [_Editable areas_](documentation/developers-and-admins/development/builders/page-builder/create-pages-with-editable-areas), which are like slots that can fit Page Builder sections. Editable areas are analogous to widget zones in sections, but they have more detailed options:
  * A default section
  * Restrictions on which sections are allowed inside of them
  * Restrictions on which widgets are allowed inside _any_ contained sections
  * Configurations relating to caching of widget output.


Even without Page Builder sections and widgets, templates can allow your editors to swap out different page designs and features either by choosing different templates or adjusting the properties of a template. This makes them more powerful and flexible than standard page content type views with page editable areas. 
## Decide how to apply Page Builder
### Strive for flexibility
Let’s take a look at how these components work together in a business scenario. We’ll discuss how we might leverage Page Builder components if a client presented the following mockups, with the requirement that their editors can achieve both without the help of a developer.
[![Mockup of a promotional page with two products](docsassets/guides/meet-requirements-with-page-builder/WidgetMockups1.png)](https://docs.kentico.com/docsassets/guides/meet-requirements-with-page-builder/WidgetMockups1.png) [![Alternate mockup of a promotional page with two products](docsassets/guides/meet-requirements-with-page-builder/WidgetMockups2.png)](https://docs.kentico.com/docsassets/guides/meet-requirements-with-page-builder/WidgetMockups2.png)
In theory, both of these results could be achieved with a single component of any kind. With enough properties and display logic to react to them, even a single template, section, or widget could be twisted into a complex monstrosity that can render both of these pages alone. However, this flawed approach would lead to hyper-specific code, little reusability, and an awful editor experience trying to configure dozens of properties in an overcrowded menu. This scenario is better achieved through the combination of all three component types.
#### Examine the granular pieces
Looking over the mockups, each version of the page contains some amount of text that can be handled by the out-of-box _rich text_ widget.
Both versions of the page also have at least one _call to action (CTA)_ button, with multiple possibilities of text and destination, so this is a good candidate for a custom widget.
[![annotated mockup pointing out relevant components](docsassets/guides/meet-requirements-with-page-builder/WidgetMockups2annotation1.png)](https://docs.kentico.com/docsassets/guides/meet-requirements-with-page-builder/WidgetMockups2annotation1.png)
Both page versions also contain information about products, with different layouts and included data. This would also be well-suited to a custom widget. The product widget should have properties that determine its layout and which data it displays from the selected product.
[![annotated mockup pointing out relevant components](docsassets/guides/meet-requirements-with-page-builder/WidgetMockups1annotation2.png)](https://docs.kentico.com/docsassets/guides/meet-requirements-with-page-builder/WidgetMockups1annotation2.png) [![annotated mockup pointing out relevant components](docsassets/guides/meet-requirements-with-page-builder/WidgetMockups2annotation2.png)](https://docs.kentico.com/docsassets/guides/meet-requirements-with-page-builder/WidgetMockups2annotation2.png)
While some developers might prefer to create a listing widget that can display multiple products, rather than a widget that displays only one product per instance, a single-product widget is better for this scenario, where the products are not always in the same area of the page.
#### Consider the layout
If you look over the layout of the two mockups, you’ll notice that the first has two differently-colored columns, while the second contains a single column of a lighter color followed by a two-column area of a darker color.
Some would suggest having each of these configurations be a selectable option in the properties of a template or section, for example, _Two column layout_ and _Top column with two columns underneath_ , with properties for setting the color of each area. However, this could make the properties complicated. You would need to either figure out how many distinct parts exist in each layout to render the appropriate number of color and style selectors, or include unused color and style controls when a layout with fewer than the maximum number of parts is selected.
A better approach in this case would be to create a page template that can have a color, style, and one to three columns, and a section with the same.
[![annotated mockup pointing out relevant components](docsassets/guides/meet-requirements-with-page-builder/WidgetMockups1annotation3.png)](https://docs.kentico.com/docsassets/guides/meet-requirements-with-page-builder/WidgetMockups1annotation3.png) [![annotated mockup pointing out relevant components](docsassets/guides/meet-requirements-with-page-builder/WidgetMockups2annotation3.png)](https://docs.kentico.com/docsassets/guides/meet-requirements-with-page-builder/WidgetMockups2annotation3.png)
The sections can then nest within the template’s columns and create a wide variety of configurations as they stack above and below one another, each with only three properties.
[![annotated mockup pointing out relevant components](docsassets/guides/meet-requirements-with-page-builder/WidgetMockups1annotation4.png)](https://docs.kentico.com/docsassets/guides/meet-requirements-with-page-builder/WidgetMockups1annotation4.png) [![annotated mockup pointing out relevant components](docsassets/guides/meet-requirements-with-page-builder/WidgetMockups2annotation4.png)](https://docs.kentico.com/docsassets/guides/meet-requirements-with-page-builder/WidgetMockups2annotation4.png)
This approach misses out on some design combinations, such as a layout with four or more columns that are each a different color, but it will cover most reasonable goals.
### Use Page Builder conditionally
Next, let’s explore how Page Builder might be leveraged conditionally. Imagine that a client presented a mockup like this, explaining that a product page should look like this by default, but have an option where editors can customize it more in special cases.
[![Mockup of a product detail page](docsassets/guides/meet-requirements-with-page-builder/WidgetMockups3.png)](https://docs.kentico.com/docsassets/guides/meet-requirements-with-page-builder/WidgetMockups3.png)
One approach to solve this problem would be to include the data shown in the mockup directly into a template associated with product pages, and add an editable area so that editors can add additional content through widgets. However, this makes the presentation of the product specific data inflexible, and also displays an enabled Page Builder area by default.
A better approach would be to use a template property to decide whether Page Builder is available at all. By default, the template can display only the data from the mockup. If Page Builder is enabled, it can become a flexible template like the one from the previous example. There, the Product widget from earlier can display pieces of the structured product data in various layouts alongside any future widgets.
[ Previous page ](modules/page-builder)
2 of 16
[ Mark complete and continue ](modules/page-builder/map-enum-to-dropdown)
  * [Community Questions & Answers](https://community.kentico.com/q-and-a)
  * [Contact support](https://community.kentico.com/support)


### Cookie consent
We use necessary [cookies](https://www.kentico.com/cookies-policy) to run our website and improve your experience while browsing. Additional cookies are only used with your consent. You may revoke your consent on the [Cookies Policy](https://www.kentico.com/cookies-policy) page or in your browser at any time. 
ACCEPT ALL  [Configure](https://www.kentico.com/cookies-policy)
USE ONLY NECESSARY 
![](https://docs.kentico.com/modules/page-builder/meet-requirements-with-page-builder)
[](https://docs.kentico.com/modules/page-builder/meet-requirements-with-page-builder)[](https://docs.kentico.com/modules/page-builder/meet-requirements-with-page-builder)