---
source: https://docs.kentico.com/documentation/business-users/website-content/page-templates
scrape_date: 2026-01-26
---

  * [Home](/documentation)
  * [Business users](/documentation/business-users)
  * [Website content](/documentation/business-users/website-content)
  * Page templates 


# Page templates
Page templates provide content editors with greater control over a page’s layout. Templates are prepared by the site’s developers and assigned by editors when [creating new pages](/documentation/business-users/website-content/create-pages) or changing the layout of existing pages. Depending on the implementation, page templates can combine means to display [structured content](/guides/architecture/content-modeling/content-modeling-guide) (edited on a page’s _Content_ tab) with predefined editable areas for [Page Builder](/documentation/business-users/website-content/widgets-and-page-builder).
## Page template types
Xperience supports the following types of page templates:
  * **Default page templates** – determine a page’s layout. Templates of this type first need to be created by the site’s developers.
  * **Preset page templates** – can be saved from pages that were created using  _default page templates_ . A _preset page template_ inherits the original template’s layout, along with all [Page Builder content](/documentation/business-users/website-content/widgets-and-page-builder) used on the page. Preset templates can then be used as a starting point for new pages. The Page Builder content on pages created using preset templates can be freely adjusted without impacting other pages – all modifications only affect the current page (there is no logical connection between the content of pages created with the same template).
    * Page content edited via the  _Content_ [view mode](/documentation/business-users/website-content) **will not be** saved in the preset template.


## Create pages based on page templates
To use page templates when creating pages:
  1. Ask a developer to define a page template in the code of your site.
See [Page templates for Page Builder](/documentation/developers-and-admins/development/builders/page-builder/page-templates-for-page-builder) for developer instructions.
  2. As an editor, you can now [create](/documentation/business-users/website-content/create-pages) a page based on a page template in the Xperience administration.
Page templates may only be available for certain types of pages. The available template options depend on your site’s implementation.


Editors can save this page as a _preset page template_ with the Page Builder content created on the page. See [Save pages as preset page templates](#save-pages-as-preset-page-templates).
### Configure page templates
You can configure a page template to further control the overall appearance of the page. The available configuration options depend on the implementation prepared by the site’s developers.
  1. Open a page based on a template and select the **Page Builder** view mode.
  2. Click the orange configure button (  
[![Template configuration dialog location](/docsassets/documentation/page-templates/templateconfiguration.png)](/docsassets/documentation/page-templates/templateconfiguration.png)
  3. Configure any available properties in the dialog.  
[![Template configuration dialog example](/docsassets/documentation/page-templates/templateconfigurationdialog.png)](/docsassets/documentation/page-templates/templateconfigurationdialog.png)
  4. **Apply** the changes.


The configured properties now apply to the template for the given page. Any other pages that use the same template have their own separate property configuration. Depending on the available properties, the changes are reflected in the page’s appearance or behavior on the website.
## Change templates of existing pages
You can change the page template of an existing page, provided there is more than one page template available for the given page.
  1. Open a page based on a template and select the **Page Builder** view mode.
  2. Click the orange change template button (  
[![Change template button location](/docsassets/documentation/page-templates/opentemplateselection.png)](/docsassets/documentation/page-templates/opentemplateselection.png)
  3. Select one of the available page templates.  
[![Template selection dialog](/docsassets/documentation/page-templates/templateselection.png)](/docsassets/documentation/page-templates/templateselection.png)
**Preserving Page Builder content when changing _default_ page templates**
When changing the _default_ page template for a page, you have the option to _Keep current widgets and sections if possible_. This option allows you to preserve widgets and sections from the current template if the new template contains editable areas with matching identifiers. Otherwise, Page Builder content from the current page is discarded. If you’re unsure about the expected behavior on your site, consult your site’s administrators.
  4. **Apply** the changes.


The page now uses the selected template. 
## Save pages as preset page templates
If you want to reuse Page Builder content, such as the layout of sections or content of widgets, you can save template-based pages as preset templates. Structured page data (edited in the _Content_ view mode) **is not** saved within preset templates.
  1. [Create or edit](/documentation/business-users/website-content/create-pages) a page based on a template.
  2. [Edit the page](/documentation/business-users/website-content/edit-and-publish-pages) and add desired content in the **Page Builder** view mode.
  3. **Save** your changes and select **Save as preset template**. A new dialog window opens.  
[![Save as preset template](/docsassets/documentation/page-templates/SaveAsTemplate.png)](/docsassets/documentation/page-templates/SaveAsTemplate.png)
  4. Enter the template’s fields: **Name** and **Icon** to identify the template. 
     * **Name** – display name of the template.
     * **Description** – detailed description of the template.
     * **Icon** – icon under which the template is displayed in the administration.  
[![Save as preset template - dialog](/docsassets/documentation/page-templates/SaveAsPresetTemplate-dialog.png)](/docsassets/documentation/page-templates/SaveAsPresetTemplate-dialog.png)
  5. **Save** the preset template.


You now have a preset template that you can select when creating new pages that support the original default template. Such pages automatically start with the Page Builder content stored within the preset template and can be further adjusted. Changes made in the content of one page **do not** affect the content of other pages that use the same preset page template.
## Manage preset page templates
You can see a list of your saved templates in the **Preset page templates** tab in the **Preset templates** application.
[![List of preset page templates](/docsassets/documentation/page-templates/PresetPageTemplates.png)](/docsassets/documentation/page-templates/PresetPageTemplates.png)
The tab displays a listing with the names and descriptions of individual preset page templates. It also displays the base default templates from which the preset templates are derived.
### Edit preset page templates
Direct editing of preset page templates is not currently supported. In order to change a template, you need to [delete](#delete-preset-page-templates) it and [save](#save-pages-as-preset-page-templates) it again.
### Delete preset page templates
To delete a saved preset template:
  1. Open the **Preset templates** application.
  2. Open the **Preset page templates** tab.
  3. **Delete** (


You have now successfully deleted the page template. Note, that this has no impact on the pages created from the template.
![]()
[]()[]()
