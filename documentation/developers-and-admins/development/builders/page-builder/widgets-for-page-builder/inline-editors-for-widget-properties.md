---
source: https://docs.kentico.com/documentation/developers-and-admins/development/builders/page-builder/widgets-for-page-builder/inline-editors-for-widget-properties
scrape_date: 2026-01-22
---

  * [Home](/documentation)
  * [Developers and admins](/documentation/developers-and-admins)
  * [Development](/documentation/developers-and-admins/development)
  * [Builders](/documentation/developers-and-admins/development/builders)
  * [Page Builder](/documentation/developers-and-admins/development/builders/page-builder)
  * [Widgets for Page Builder](/documentation/developers-and-admins/development/builders/page-builder/widgets-for-page-builder)
  * Inline editors for widget properties 


# Inline editors for widget properties
Inline editors allow content editors to configure the [properties of Page Builder widgets](/documentation/developers-and-admins/development/builders/page-builder/widgets-for-page-builder/widget-properties). They can be used as an alternative to the property configuration dialog, or in combination with it.
Unlike the configuration dialog, inline editors are interactive elements directly within the output of the related widget. For example, inline editors can range from basic value inputs to more advanced options, such as file uploaders or text areas with rich formatting options.
Each inline editor manages only a single widget property. For widgets that have multiple properties, you can add multiple inline editors into the widget’s output.
The editors are visible only in the Xperience administration interface when [working with pages that contain editable areas](/documentation/business-users/website-content/widgets-and-page-builder). However, the configured properties then affect the appearance or functionality of the given widget on the live site.
By default, the system contains an implementation of an inline editor that allows users to insert rich text content. See [Rich text inline editors](/documentation/developers-and-admins/development/builders/page-builder/widgets-for-page-builder/rich-text-inline-editors) to learn how to integrate the editor into your widgets. 
**Inline editors for Email Builder widget properties**
This page describes how to work with inline editors for [Page Builder widget properties](/documentation/developers-and-admins/development/builders/page-builder/widgets-for-page-builder/widget-properties). Inline editors are also available for [Email Builder widget properties](/documentation/developers-and-admins/development/builders/email-builder/inline-editors-email-builder-widgets).
**Custom inline editors with links to content items**
If you decorate any widget property with a [custom form component](/documentation/developers-and-admins/customization/extend-the-administration-interface/ui-form-components) or use an inline editor that allows users to add links to content items (other than the default [combined content selector](/documentation/developers-and-admins/customization/extend-the-administration-interface/ui-form-components/reference-admin-ui-form-components#combined-content-selector) and [rich text editor](/documentation/business-users/rich-text-editor)), you need to create a [custom reference extractor](/documentation/developers-and-admins/customization/extend-the-administration-interface/ui-form-components/ui-form-component-reference-extractors) in order to enable [usage tracking](/documentation/business-users/content-hub/content-items#track-usage-of-content-items) in the widget property.
**Areas**
Widgets are designed as global components. Adding related files, such as inline editor files, into [Areas](https://docs.microsoft.com/en-us/aspnet/core/mvc/controllers/areas) is not supported and may lead to unexpected behavior.
## Implement inline editors
To create an inline editor for a property and integrate it into your Page Builder widgets:
For a full scenario with code samples which will guide you through the process of developing a widget with a property and inline editor, see [Example - Widget development](/documentation/developers-and-admins/development/builders/page-builder/widgets-for-page-builder/example-widget-development).
  1. Add an editor view model class to your project. 
     * The model class should contain at least two properties – the name and value of the widget property managed by the editor.
     * We recommend storing the view models of inline editors in a designated folder, for example: _~/Components/InlineEditors/ <EditorName>/_
C#
Copy
```
public class CustomEditorModel
{
    public string PropertyName { get; set; }
    ...
}
```

  2. Create a partial view that defines the inline editor’s interface. 
     * Make the partial view use the view model that you created for the editor.
     * The view code must encapsulate inner HTML elements using the `Kentico().BeginInlineEditor()` helper method. Specify the following parameters for the method:
       * A unique identifier of the inline editor.
       * The name of the widget property managed by the editor.
cshtml
Copy
```
@using Kentico.PageBuilder.Web.Mvc
@using Kentico.Web.Mvc

@model CustomEditorModel

@using (Html.Kentico().BeginInlineEditor("custom-editor", Model.PropertyName))
{
    ...
}
```

       * We recommend storing the partial views of inline editors in a designated folder, for example:  _~/Components/InlineEditors/ <EditorName>_
  3. Create a JavaScript file containing a self-invoking function that registers the property editor. Every widget property editor must be registered using client-side code. 
     * To register the property editor, call the `window.kentico.pageBuilder.registerInlineEditor` function.
JS
Copy
```
(function () {
  // Registers the 'custom-editor' inline property editor
  window.kentico.pageBuilder.registerInlineEditor("custom-editor", {
    init: function (options) {
        var editor = options.editor;
        ...
    }
  });
})();
```

     * Specify the following parameters when calling the `registerInlineEditor` function:
       * The identifier of the property editor, i.e. the first parameter set for the _BeginInlineEditor_ helper method in the HTML code of the editor’s partial view.
       * An anonymous object with an `init` property function that is executed whenever the given property editor is loaded. Use this function to run custom JavaScript that modifies the widget’s content or behavior based on the property value. The `options` parameter is an object that wraps the following properties: 
         * `editor` – the DOM element encapsulating the editor
         * `propertyName` – the name of the related widget property
         * `propertyValue` – the value of the related widget property
         * `localizationService` – the localization service
**Executing scripts on inline editor events**
If you need to execute custom scripts on certain events, you can use additional property functions of the anonymous object defined when registering the inline editor.
JS
Copy
```
// Mandatory:
init: function (options) { ... },
// Optional:
destroy: function (options) { ... },   // Executed when a user deletes a widget containing the inline editor
dragStart: function (options) { ... }, // Executed when a user starts to drag a widget with the inline editor
drop: function (options) { ... }       // Executed when a user successfully drops a widget with the inline editor
```

Only the `editor` property is available via the `options` parameter in the destroy, `dragStart`, and `drop` property functions.
     * `init` function that you registered for the editor).
JS
Copy
```
// Creates a custom event that notifies the widget about a change in the value of a property
var event = new CustomEvent("updateProperty", {
    detail: {
        value: "newValue",
        name: propertyName
    }
});
editor.dispatchEvent(event);
```

The `updateProperty` event’s data must contain the detail property, with the following values:
       * `value` – the new value of the widget property.
       * `name` – the name of the related widget property.
       * (Optional) `refreshMarkup` – bool value that determines whether the event automatically triggers a refresh of the widget’s entire markup from the server. True by default.
  4. Prepare any additional assets required by the editor, such as further JavaScript files, CSS stylesheets, images, etc. 
     * For detailed information and recommendations, see [Add scripts and styles for inline editors](#add-scripts-and-styles-for-inline-editors).
  5. Edit the partial views of the widgets where you want to integrate the editor: 
    1. Determine whether the widget is being displayed in edit mode (in the administration interface). To access the context where the inline editor is rendered, use the `Retrieve` method of the `IPageBuilderDataContextRetriever` service. Evaluate the context’s Page Builder mode by using the `GetMode` method. The method returns values from the `PageBuilderMode` enumeration.
    2. Render the editor’s partial view in the appropriate location. Use the editor’s view model to pass the name and current value of the related widget property.
cshtml
Copy
```
@addTagHelper *, Microsoft.AspNetCore.Mvc.TagHelpers

@using Kentico.PageBuilder.Web.Mvc

@inject IPageBuilderDataContextRetriever pageBuilderContext

@* Shows an inline editor when rendered in the edit mode of the Pages application in Kentico *@
@if (pageBuilderContext.Retrieve().GetMode() == PageBuilderMode.Edit)
{
    var inlineEditorModel = new CustomEditorModel
    {
        @* Use the nameof() operator to get the name of the widget property from the widget properties model *@
        PropertyName = nameof(CustomWidgetProperties.Number),
        PropertyValue = Model.Number
    };

    <partial name="~/Components/InlineEditors/CustomEditor/_CustomEditor.cshtml" model="inlineEditorModel" />
}
```



The modified widgets now display your inline editor when [working with page content in the administration interface](/documentation/business-users/website-content/widgets-and-page-builder). The appearance or functionality of widgets on the live site is affected by the property value configured through the inline editor.
## Add scripts and styles for inline editors
To add JavaScript and CSS styles required by your inline editors (including the script file containing the editor’s registration code), we recommend placing script and stylesheet files into sub-folders under:
  * **_~/wwwroot/PageBuilder/Public/InlineEditors/ <EditorName>_** – scripts and styles intended for the live site
  * **_~/wwwroot/PageBuilder/Admin/InlineEditors/ <EditorName>_** – scripts and styles intended for the administration interface (when working with the widget in the Page Builder editing interface). For example, inline editor registration scripts.


You can use sub-folders that match the identifiers of individual editors, or a _Shared_ sub-folder for assets used by multiple editors. Note that this recommendation only applies when using the default configuration of the bundling support provided by Xperience. The configuration of your project may be different. See [Bundle static assets of builder components](/documentation/developers-and-admins/development/builders/bundle-static-assets-of-builder-components).
Inline editor scripts and styles are only included in the administration interface when pages are displayed in **Edit** mode within the **Pages** application.
**Notes – Scripts**
  * Avoid linking or executing scripts directly within the views of inline editors. This could lead to duplicated scripts on pages containing multiple widgets that use the editor.


**Notes – Styles**
  * To prevent your editor styles from overriding the site’s styles, add a prefix to all CSS selectors used for the editor (for example `custom-editor-class`).
  * To prevent the editor’s styles from being overridden by the site’s styles, make all CSS selectors used for the editor as specific as possible. If necessary, use the `!important` rule for critical selectors.


![]()
[]()[]()
