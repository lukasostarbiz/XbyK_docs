---
source: https://docs.kentico.com/documentation/developers-and-admins/development/builders/page-builder/reference-default-page-builder-widgets
scrape_date: 2026-01-26
---

  * [Home](/documentation)
  * [Developers and admins](/documentation/developers-and-admins)
  * [Development](/documentation/developers-and-admins/development)
  * [Builders](/documentation/developers-and-admins/development/builders)
  * [Page Builder](/documentation/developers-and-admins/development/builders/page-builder)
  * Reference - Default Page Builder widgets 


# Reference - Default Page Builder widgets
The following page lists all [widgets](/documentation/developers-and-admins/development/builders/page-builder/widgets-for-page-builder) available for the [Page Builder](/documentation/developers-and-admins/development/builders/page-builder) out-of-the-box. You may not see all of the default system widgets for some pages, depending on the [editable area limitations](/documentation/developers-and-admins/development/builders/page-builder/create-pages-with-editable-areas) in your project.
**Component identifiers**
The identifiers of system components are available in the `SystemComponentIdentifiers` class (in the `Kentico.Content.Web.Mvc` namespace). For example, use `SystemComponentIdentifiers.RICH_TEXT_WIDGET_IDENTIFIER` for the identifier of the _Rich text_ widget.
These identifiers are required when decorating existing widgets with custom functionality. See [Extend widgets](/documentation/developers-and-admins/development/builders/page-builder/widgets-for-page-builder/extend-widgets).
Widget name |  Properties |  Description  
---|---|---  
Form |  **FormWidgetProperties**
  * **SelectedForm** – an `IEnumerable<ObjectRelatedItem>` collection containing the code name of the selected form.
  * **AfterSubmitMode** – the action performed after a user submits the form. Set one of the options available in `FormAfterSubmitModeConstants`.
  * **AfterSubmitDisplayText**
  * **AfterSubmitRedirectToUrl**
  * **AfterSubmitRedirectToWebPage**

|  Allows users to [display a form](/documentation/business-users/digital-marketing/forms/display-forms-on-pages) on the website.  
Rich text |  **RichTextWidgetProperties**
  * **Content** – a string property that contains the formatted rich text.

|  Allows users to input [rich text content](/documentation/business-users/rich-text-editor) via a toolbar with standard word processor features. You can change the default set of toolbar options available to content editors via custom toolbar configurations.  
![]()
[]()[]()
