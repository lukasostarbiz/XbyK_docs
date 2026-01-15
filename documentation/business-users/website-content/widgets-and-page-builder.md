# Widgets and Page Builder
  * [ Copy page link ](documentation/business-users/website-content/widgets-and-page-builder#) | [Get HelpService ID](documentation/business-users/website-content/widgets-and-page-builder#)
Core MVC 5


[✖](documentation/business-users/website-content/widgets-and-page-builder# "Close page link panel") [Copy to clipboard](documentation/business-users/website-content/widgets-and-page-builder#)
**Page Builder** is a user-friendly UI feature that allows users to build pages using configurable widgets prepared by developers. Page Builder lets you easily display various types of content using widgets directly from within the Xperience administration.
[![Adding content through widgets using the page builder](docsassets/documentation/widgets-and-page-builder/page_builder.png)](https://docs.kentico.com/docsassets/documentation/widgets-and-page-builder/page_builder.png)
**Page Builder** is available in website channel applications in the page menu of pages that have the [Page Builder enabled](documentation/developers-and-admins/development/builders/page-builder).
**Prerequisite**
A developer needs to set up the [Page Builder feature](documentation/developers-and-admins/development/builders/page-builder) and [prepare pages with editable areas](documentation/developers-and-admins/development/builders/page-builder/create-pages-with-editable-areas), before you can create and manage content using this approach.
## Page Builder workflow
When working with Page Builder, you’ll often use the following workflow:
  1. Open a website channel application.
  2. In the content tree, select the page that you want to edit.
  3. Select **Page Builder** in the page menu on the right side of the interface.  
[![Page’s Page Builder tab](docsassets/documentation/widgets-and-page-builder/pb_tab.png)](https://docs.kentico.com/docsassets/documentation/widgets-and-page-builder/pb_tab.png)
  4. Make sure the page is in the _Draft_ status or another [custom workflow step](documentation/developers-and-admins/configuration/workflows). 
     * If a page is already _Published_ select **Edit page**. This transitions the page to the _Draft_ step and enables editing and re-publishing.  
[![Editing a published page](docsassets/documentation/widgets-and-page-builder/create_new_version_button.png)](https://docs.kentico.com/docsassets/documentation/widgets-and-page-builder/create_new_version_button.png)
     * When a page is _Unpublished_ , you first need to select **Create new version**. This transitions the page to the _Draft_ step and enables editing and publishing.
  5. [Adjust sections](documentation/business-users/website-content/widgets-and-page-builder#adjust-sections) to achieve your desired visual layout of the page.
  6. [Add widgets](documentation/business-users/website-content/widgets-and-page-builder#edit-widgets) to the page to define the content.


## Adjust sections
On a page where Page Builder is set up, you can do the following actions with sections. You can watch a video or read about the actions below.
UI elements that allow you to modify sections are gray:
  * **Add sections** – Click a gray  _plus_ button located on the left side of an editable area to insert a new section. The list of available sections depends on the implementation of your website.
  * **Move sections** – Hover over a section and drag it by the drag handle.
  * **Change section type** – You can change the type of a section to adjust the arrangement of a page. To do so, hover over the section you want to modify, click the section type button, and select the section type you want to use.
  * **Configure sections** (only displayed for configurable sections) – Click the configure button, edit the section properties in the configuration dialog, and click  _Apply_. Check with your site’s administrators or developers for more information about the section types on your website and their properties.
  * **Copy sections** – Select a section you want to copy and click the copy button in the upper right corner of the section to copy a section with all widgets in the section. 
    * You can paste the copied sections onto any page that uses the Page Builder using the _Add section_ button.
    * A section can only be pasted into an editable area if all widgets in the copied section and the section itself are allowed in the area.
    * The copy feature allows you to store up to 10 sections for 12 hours.
  * **Delete sections** – Hover over the section you want to remove and click the delete button in the upper right corner of the section.


[![Section management UI elements](docsassets/documentation/widgets-and-page-builder/sections_UI.png)](https://docs.kentico.com/docsassets/documentation/widgets-and-page-builder/sections_UI.png)
Each section defines one or more zones, where you can add widgets.
After making any changes on the page, do not forget to click the **Save** button. Changes made to the sections can be previewed in the **Preview** mode.
## Edit widgets
On a page where Page Builder is set up, you can do the following actions with widgets. UI elements that allow you to modify widgets are purple:
  * **Add widgets** – Click a purple  _plus_ button to insert a widget to the desired location. The list of available widgets depends on the implementation of your website.
  * **Move widgets** – Click on a widget and drag it by its handle or full header to a different position.
  * **Personalize widgets** – See [Personalize Page Builder widgets](documentation/business-users/digital-marketing/widget-personalization).
  * **Configure widgets** (only displayed for configurable widgets) – Click the configure button, edit the widget properties in the configuration dialog, and click  _Apply_. 
    * **Note** : Widget configuration options and property editors are implemented individually with each widget. For more information, visit your project’s documentation or contact your developer.
  * **Copy widgets** – Click on a widget you want to copy and click the copy button in the upper right corner of the widget. You can then paste copied widgets on pages built using Page Builder via the _Add widget_ functionality. This feature allows you to store up to 10 widgets for 12 hours.
  * **Delete widgets** – Click on a widget you want to remove and click the delete button in the upper right corner of the widget.


[![Widget management UI elements](docsassets/documentation/widgets-and-page-builder/widgets_UI.png)](https://docs.kentico.com/docsassets/documentation/widgets-and-page-builder/widgets_UI.png)
Watch a short video about how you can work with Page Builder widgets.