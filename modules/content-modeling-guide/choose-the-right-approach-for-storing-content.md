---
source: https://docs.kentico.com/modules/content-modeling-guide/choose-the-right-approach-for-storing-content
scrape_date: 2026-01-22
---

Module: Content modeling guide
10 of 28 Pages
# Choose the right approach for storing content
The method you use to store your content in Xperience affects how easily editors can reuse, update, adapt, and maintain their digital content. We’ll explore and explain the details and impacts of choosing different storage approaches, and provide recommendations for when to use each.
### Remember how Page Builder content is stored
All content added directly to a Page Builder widget is stored in a single database field (_CMS_ContentItemCommonData_ → _ContentItemCommonDataVisualBuilderWidgets_), making it difficult to reuse. On the other hand, widgets that add design to structured content store only a reference to the linked content item (using the GUID value).
Sometimes, projects require WYSIWYG editing through widgets that also store content. While specifying the project requirements, we recommend considering the implications of this design decision. Storing content directly in widgets significantly impacts content reusability and the project’s maintenance costs and upgradability.
### Combine Page Builder and structured content
**Page Builder** and structured content are not mutually exclusive. You can combine _Page Builder_ with structured content to leverage the full potential of both approaches.
When you define your content model, consider situations in which you can store the data in a _structured format_ and use _Page Builder_ widgets or sections to present or even overwrite this content in the website page. Your widgets and sections can contain properties that allow editors to add design information (size, colors, and other content behavior). Widgets can contain properties that editors can add to overwrite the structured, reusable data and tailor the data with a per-instance-specific message. Furthermore, some content types, e.g., Articles listing pages, bring editors more possibilities if you combine _Page Builder_ and structured content.
In general, your editors will benefit when you combine both approaches:
  * Use _Page Builder_ for formatting structured content
  * Add _Page Builder editable areas_ to _page templates_ that display structured data to help with content personalization.


The following diagram shows the benefit of storing data separately from its presentation. From a user perspective, editors can create their data once and reuse it when needed.
  * News (_content type_).
  * The editor creates a new page using the News _page type_ and selects the _page template_ they want. The _News_ page type has the URL feature enabled to generate the page’s URL and make it accessible to website visitors.
  * Data input via the _Content tab_ is stored in the “News” _page type_ , and data presentation (layout and styling) is handled via the _page template_.
  * The _Teaser image_ field in the _News_ page type references a _file_ stored in the _Content hub_ ; it doesn’t keep the file itself.
  * Landing page (_content type_)
  * The editor creates a new page using the Landing page _page type_ and selects a _page template_.
  * The Landing page _page type_ has Page Builder enabled.
  * Using the _Featured article_ widget, the editor displays some data stored in the _Article_ : the _Title_ , _Teaser image_ , and _Publication date_.
  * The widget references the selected _News_ using its GUID. If the widget has any properties that set design information for the displayed data, their configuration applies separately for each widget.
  * The editor also adds a _Speaker_ widget that displays some of the data from the _Author_ content type stored in the Content hub.
  * The widget references the selected _Author_ using its GUID. Data about the widgets’ configuration on the _Landing page_ is stored in the _DocumentPageBuilderWidgets_ database field in JSON format.


[![Structured content reuse example](/docsassets/guides/store-content/cm-guide-using-structured-content-in-content-types.png)](/docsassets/guides/store-content/cm-guide-using-structured-content-in-content-types.png)
### Use Content hub for reusable content
Xperience comes with the **Content hub** application for storing reusable content. Think of the _Content hub_ as a **centralized repository** where you **create and keep your marketing content** , and potentially **reuse** it across multiple communication channels.
In the _Content hub_ , editors create items using content types with fields where they input data. The editing forms create a unified editing experience, and adding content in the _Content hub_ feels like filling out an online form.
[![Content type editing interface](/docsassets/guides/store-content/cm-guide-hero-banner-content-type.png)](/docsassets/guides/store-content/cm-guide-hero-banner-content-type.png)
Because the _Content hub_ is channel agnostic, editors do not see what the content will look like once it’s displayed, for example, on the company website. They can focus **on the quality of their content** rather than getting distracted by how the content will look.
[ Previous page ](/modules/content-modeling-guide/understand-different-storing-options)
10 of 28
[ Mark complete and continue ](/modules/content-modeling-guide/work-with-semantic-content-types)
![]()
[]()[]()
