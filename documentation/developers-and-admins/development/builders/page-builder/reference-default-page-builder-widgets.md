# Reference - Default Page Builder widgets
  * [ Copy page link ](documentation/developers-and-admins/development/builders/page-builder/reference-default-page-builder-widgets#) | [Get HelpService ID](documentation/developers-and-admins/development/builders/page-builder/reference-default-page-builder-widgets#)
Core MVC 5


[✖](documentation/developers-and-admins/development/builders/page-builder/reference-default-page-builder-widgets# "Close page link panel") [Copy to clipboard](documentation/developers-and-admins/development/builders/page-builder/reference-default-page-builder-widgets#)
The following page lists all [widgets](documentation/developers-and-admins/development/builders/page-builder/widgets-for-page-builder) available for the [Page Builder](documentation/developers-and-admins/development/builders/page-builder) out-of-the-box. You may not see all of the default system widgets for some pages, depending on the [editable area limitations](documentation/developers-and-admins/development/builders/page-builder/create-pages-with-editable-areas) in your project.
**Component identifiers**
The identifiers of system components are available in the `SystemComponentIdentifiers` class (in the `Kentico.Content.Web.Mvc` namespace). For example, use `SystemComponentIdentifiers.RICH_TEXT_WIDGET_IDENTIFIER` for the identifier of the _Rich text_ widget.
These identifiers are required when decorating existing widgets with custom functionality. See [Extend widgets](documentation/developers-and-admins/development/builders/page-builder/widgets-for-page-builder/extend-widgets).
Widget name |  Properties |  Description  
---|---|---  
Form |  **FormWidgetProperties**
  * **SelectedForm** – an `IEnumerable<ObjectRelatedItem>` collection containing the code name of the selected form.
  * **AfterSubmitMode** – the action performed after a user submits the form. Set one of the options available in `FormAfterSubmitModeConstants`.
  * **AfterSubmitDisplayText**
  * **AfterSubmitRedirectToUrl**
  * **AfterSubmitRedirectToWebPage**

|  Allows users to [display a form](documentation/business-users/digital-marketing/forms/display-forms-on-pages) on the website.  
Rich text |  **RichTextWidgetProperties**
  * **Content** – a string property that contains the formatted rich text.

|  Allows users to input [rich text content](documentation/business-users/rich-text-editor) via a toolbar with standard word processor features. You can change the default set of toolbar options available to content editors via custom toolbar configurations.