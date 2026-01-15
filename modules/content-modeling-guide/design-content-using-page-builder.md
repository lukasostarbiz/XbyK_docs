# Design content using Page Builder
  * [ Copy page link ](modules/content-modeling-guide/design-content-using-page-builder#) | [Get HelpService ID](modules/content-modeling-guide/design-content-using-page-builder#)
Core MVC 5


[✖](modules/content-modeling-guide/design-content-using-page-builder# "Close page link panel") [Copy to clipboard](modules/content-modeling-guide/design-content-using-page-builder#)
[Page Builder](documentation/developers-and-admins/development/builders/page-builder) is a user-friendly interface where non-technical users manage the content and layout of pages using configurable components.
Editors work with _Page Builder_ in [website channels](documentation/developers-and-admins/configuration/website-channel-management). They can build entire pages (or their sections), experiment with different layouts and page designs, and immediately see the results. Equipped with widgets and sections, editors can lay out content without asking developers for help whenever they want to build a new page or adjust the design of an existing page.
[Page Builder](guides/development/page-builder) allows content editors to design parts or whole pages wherever developers enable it (by defining [editable areas](documentation/developers-and-admins/development/builders/page-builder/create-pages-with-editable-areas)). Editors can use two types of components to format content using _Page Builder_ : [sections](documentation/developers-and-admins/development/builders/page-builder/sections-for-page-builder) and [widgets](documentation/developers-and-admins/development/builders/page-builder/widgets-for-page-builder).
**Sections** specify the visual layout for widgets. They represent reusable pieces of markup that can store an arbitrary number of **widget zones** – areas where content editors place _widgets_.
**Widgets** are reusable components that content editors and non-technical users can quickly work with. Widgets give users more power and flexibility when adjusting page content and basic editing of text and images. By working with widgets, users decide which components are placed where on the page.
### Behind the scenes: How Page Builder works
Developers prepare _Page Builder_ components based on project requirements.
_Page Builder_ was not designed to create content of entire websites. The content that editors add to a widget is stored per widget instance. This makes the content difficult to work with for developers if editors want to reuse content on a different page. Instead of reusing, editors will copy data into a new item, such as a widget, which leads to content duplication and content governance nightmares. If you need to frequently reuse a piece of content, such as an FAQ question, consider storing this content in the _Content hub_ and give editors a widget to display it.
Keep the following points in mind when considering the use of _Page Builder_ :
  * Most website pages do not require frequent design or layout changes. For such pages, defining only the content in Xperience and handling the content presentation in code is more convenient.
  * _Page Builder_ has been designed for content editors and marketers as they need to be able to alter the layout of their pages. However, they don’t need to adjust the layout of all content across the entire site.
  * The risk of human error causing unintended formatting or layout issues (due to incorrect widget configuration, misclicks, etc.) increases with the number of _Page Builder-enabled_ pages.


### Understand how Page Builder stores content
All content added directly to a Page Builder widget is stored in a single database field (_CMS_ContentItemCommonData_ → _ContentItemCommonDataVisualBuilderWidgets_), making it difficult to reuse. On the other hand, widgets that add design to structured content store only a reference to the linked content (using the GUID value).
Sometimes, projects require WYSIWYG editing through widgets that also store content. While specifying the project requirements, we recommend considering the implications of this design decision. Storing content directly in widgets significantly impacts **content reusability** or the **project’s upgradeability**.
### See the different types of Page Builder widgets
Developers can prepare widgets to handle content in three ways:
  * [Widgets that present structured content](modules/content-modeling-guide/design-content-using-page-builder#widgets-that-present-structured-content)
  * [Widgets that store and design the content at the same time](modules/content-modeling-guide/design-content-using-page-builder#widgets-that-store-and-design-the-content-at-the-same-time)
  * [Widgets that combine both inline editing and structured content](modules/content-modeling-guide/design-content-using-page-builder#widgets-that-combine-both-inline-editing-and-structured-content)


### Widgets that present structured content
Editors add these widgets into appropriate _Page Builder_ sections and select the content to display. Xperience can store the content itself in one of the following locations:
  * **As content items in the Content hub**. The widget serves only as a design container for the content stored in the _Content hub_. For example, a _Promotion_ widget displays data stored in the _Promotion_ content type:
[![Selecting content items from Page Builder widgets](docsassets/guides/design-website-content/image-2023-8-16_14-44-13.png)](https://docs.kentico.com/docsassets/guides/design-website-content/image-2023-8-16_14-44-13.png)
  * **Elsewhere in Xperience website channel application.** The widgets serve only as design containers for the content stored (and presented) elsewhere. For example, a widget that displays only several properties of the “Article” content type:
Your browser does not support the video tag.


#### What does working with the reusable content look like?
For example, imagine editors are building a campaign landing page.
The page will contain a _Hero_ element. From the design perspective, editors should select only the _Hero banner_ content type from the _Content hub_ to populate a **Banner widget**. From the editor’s perspective, adding reusable content to the page and displaying the structured data might look like the following:
Your browser does not support the video tag. 
When modeling the content storage, always consider the editor’s workflow. Help them understand what type of content they’re supposed to select. For example, you can use instructions next to the selectors in the widget [properties](documentation/developers-and-admins/development/builders/page-builder/widgets-for-page-builder) and your project documentation to ensure editors receive enough guidance.
[![Widget with a selector and an explanation text](docsassets/guides/design-website-content/xperience-by-kentico-cafe-widget-properties-explanation-text.png)](https://docs.kentico.com/docsassets/guides/design-website-content/xperience-by-kentico-cafe-widget-properties-explanation-text.png)
Similarly, ensure that editors will not unintentionally break the page experience by selecting content that doesn’t belong.
  * Developers can restrict the selection to pages of specific _webpage types_ that fulfill required conditions (e.g., have the URL feature enabled). See samples of the Xperience selectors in [Reference - Admin UI form components](documentation/developers-and-admins/customization/extend-the-administration-interface/ui-form-components/reference-admin-ui-form-components).  
[![Page selector component with filtered selection](docsassets/guides/design-website-content/xperience-by-kentico-selector-widget-properties.png)](https://docs.kentico.com/docsassets/guides/design-website-content/xperience-by-kentico-selector-widget-properties.png)
  * Similarly, developers define which type editors select into the widget properties, such as the _Benefits_ widget in the Kbank demo site.  
[![Selector content type selection](docsassets/guides/design-website-content/cm-guide-selecting-one-type-of-content-type.png)](https://docs.kentico.com/docsassets/guides/design-website-content/cm-guide-selecting-one-type-of-content-type.png)


### Widgets that store and design the content at the same time
With these _single-purpose content widgets_ , editors can manage the content in a WYSIWYG manner using [inline editors](documentation/developers-and-admins/development/builders/page-builder/widgets-for-page-builder/inline-editors-for-widget-properties), use the widget’s properties and add content through the widget’s configuration dialog, or combine both. The out-of-the-box _Rich text_ widget is an example of a widget with advanced text styling capabilities.
The _Banner_ widget on the Dancing Goat [sample site](https://devnet.kentico.com/articles/kentico-xperience-sample-sites-and-their-differences) shows both _inline editors_ and _widget configuration properties_ where all the input data is stored within one database field.
[![Widgets that store and design the content at the same time](docsassets/guides/design-website-content/xperience-by-kentico-banner-widget-editing-properties.png)](https://docs.kentico.com/docsassets/guides/design-website-content/xperience-by-kentico-banner-widget-editing-properties.png)
Similarly, the _Page heading_ widget on [Kbank demo site](modules/content-modeling-guide/what-is-a-content-type#best-practices-in-kbank-demo-site) allows editors to add content and style it using only widget configuration properties.
[![Add formatting metadata to content using a widget](docsassets/guides/design-website-content/cm-guide-page-heading-properties.png)](https://docs.kentico.com/docsassets/guides/design-website-content/cm-guide-page-heading-properties.png)
### Widgets that combine both inline editing and structured content
_Mixed-content widgets_ allow editors to reuse structured content, e.g., by providing a selector to pick content from the content tree. At the same time, editors can use the widget’s editable properties to overwrite some of the content pulled from the selected page or create brand-new content. Editors can also provide additional information or behavior to their content, such as ensuring that hyperlinks to external websites open in a new tab.
[![Mixed-content widget example](docsassets/guides/design-website-content/xperience-by-kentico-card-widget-properties.png)](https://docs.kentico.com/docsassets/guides/design-website-content/xperience-by-kentico-card-widget-properties.png)
We recommend limiting the use of mixed-content widgets to specific pages (sections), as content stored in these widgets can be more difficult to reuse in other channels.
### Drive displaying data using taxonomy
Design dedicated widgets to consume [taxonomy terms](documentation/developers-and-admins/configuration/taxonomies) as input and dynamically display content based on those terms to support taxonomy-driven content. For example, a _content listing widget_ should allow editors to select a [taxonomy tag](guides/digital-marketing/work-with-taxonomies/taxonomies) or [smart folder](documentation/business-users/content-hub/content-hub-folders#smart-folders) as a filter. The widget then queries and displays only the matching items. It should also offer options to exclude certain content, such as by listing available tags or applying an “exclude from display” tag in article itself, to give editors more control over the final output. Ideally, the widget will allow editors to wrap the listed content with additional content (e.g., text or visuals) to ensure that content remains reusable and manageable directly from the _Page Builder_.
[![A listing widget with taxonomy filtering](docsassets/guides/design-website-content/cm-news-listing-article-with-filtering-via-taxonomy-tags.png)](https://docs.kentico.com/docsassets/guides/design-website-content/cm-news-listing-article-with-filtering-via-taxonomy-tags.png)
### Recommendations – Page Builder
**Combine structured content and widgets**
To make the website’s content both reusable and future-proof, we recommend storing the content of the widgets using the structured content format. Developers should prepare widget properties that allow editors to format content using WYSIWYG editing capabilities.
Based on the project requirements, the structured content of widgets can be stored in a dedicated section in the content tree or directly in a hierarchy under the page as child items.
**Limit using widgets for single-purpose content**
Xperience stores the content input directly into widgets within one database field for the whole page. We recommend storing content in widgets only when the content is not going (or doesn’t have the potential) to be reused on the website. This content can work on one-off pages, such as campaign landing pages.
**Create sections with designing capabilities**
We recommend creating sections with properties to let editors define how the content they add via widgets displays on the website. Some examples of section properties are a field for a URL parameter (Anchor tag) for deep linking, a property to define the number of columns within the section, a property to adjust the margin or padding of widgets, and changing the color of the section, and similar properties.
**Set restrictions on editable areas**
Use editable area restrictions to ensure editors can add only desired widgets or sections to specific areas. This ensures page designs do not break (e.g., in different browsers) and creates boundaries for data predictability (when considering future upgrades). Find out more in [Create pages with editable areas](documentation/developers-and-admins/development/builders/page-builder/create-pages-with-editable-areas).
**Create custom widgets to add markup to pages**
Typically, in the early stages of a live project, editors might need to add custom HTML markup or include scripts to embed 3rd party applications. Project owners sometimes need to remember this or similar scenarios when detailing project requirements, so ensure your project gets this low-hanging fruit to make editors happy.
We recommend creating custom widgets that allow editors to add scripts or HTML markup to the page’s _head_ or _body_. For example, editors might use an _Anchor widget_ to enable linking to a section of the page or a _Script widget_ to add JavaScript that runs a custom-built mortgage calculator.
If editors will reuse these scripts or markup on different pages, developers can prepare a custom _Script_ content type or widget.
**Add instructions to selectors in Page Builder components**
When building widgets that display structured content, provide editors with clear instructions describing what content items they should select. You can use the **ExplanationText** [widget property](documentation/developers-and-admins/customization/extend-the-administration-interface/ui-form-components/editing-components) and provide contextual information specific to the selected widget. Providing additional guidance ensures that editors don’t break the visual experience of the website by mistake. Also, include clear instructions about how editors should use every widget on the website in your project documentation.
**Make designing content generic**
Though you might want to store design-specific information, like exact color codes or sizes, inside the channel-specific or reusable content types in the _Content hub_ , we do not recommend doing this.
From a content longevity perspective, you want to have your design information as agnostic to the visual design as possible. When you decide to, for example, redesign your brand, it’s easier to remap the generic class name to new colors than change the specific _#F05A22_ brand color code in hundreds of different places. The following screenshot shows styling options in the Kbank demo site.
[![Styling options](docsassets/guides/design-website-content/cm-guide-section-properties.png)](https://docs.kentico.com/docsassets/guides/design-website-content/cm-guide-section-properties.png)
In typical Xperience projects, **these three approaches are combined together** to create an effective content management experience that supports reusing the content as necessary. Stakeholders must decide how they’ll work with the content when defining the project requirements. However, to make your content future-friendly, we recommend storing it in a structured format.
[ Previous page ](modules/content-modeling-guide/design-website-content)
18 of 28
[ Mark complete and continue ](modules/content-modeling-guide/design-page-based-content-model)
  * [Community Questions & Answers](https://community.kentico.com/q-and-a)
  * [Contact support](https://community.kentico.com/support)


### Cookie consent
We use necessary [cookies](https://www.kentico.com/cookies-policy) to run our website and improve your experience while browsing. Additional cookies are only used with your consent. You may revoke your consent on the [Cookies Policy](https://www.kentico.com/cookies-policy) page or in your browser at any time. 
ACCEPT ALL  [Configure](https://www.kentico.com/cookies-policy)
USE ONLY NECESSARY 
![](https://docs.kentico.com/modules/content-modeling-guide/design-content-using-page-builder)
[](https://docs.kentico.com/modules/content-modeling-guide/design-content-using-page-builder)[](https://docs.kentico.com/modules/content-modeling-guide/design-content-using-page-builder)