---
source: https://docs.kentico.com/documentation/developers-and-admins/development/builders/page-builder/render-widgets-in-code
scrape_date: 2026-01-26
---

  * [Home](/documentation)
  * [Developers and admins](/documentation/developers-and-admins)
  * [Development](/documentation/developers-and-admins/development)
  * [Builders](/documentation/developers-and-admins/development/builders)
  * [Page Builder](/documentation/developers-and-admins/development/builders/page-builder)
  * Render widgets in code 


# Render widgets in code
Developers can display [Page Builder widgets](/documentation/developers-and-admins/development/builders/page-builder/widgets-for-page-builder) manually, without the need to set up [editable regions](/documentation/developers-and-admins/development/builders/page-builder/create-pages-with-editable-areas) and insert widgets in the Page Builder interface. The API allows widgets to be rendered directly in the code of views. These can be views that define the output of standard pages and layouts, or even other Page Builder components, such as [sections](/documentation/developers-and-admins/development/builders/page-builder/sections-for-page-builder).
For example, this rendering approach can be used to include a widget into a site’s main layout (the website header or footer). The widget appears as a fixed part of the website, and cannot be removed or adjusted by content editors.
## Requirements
  * Page Builder must be enabled for your project, as described in [Page Builder](/documentation/developers-and-admins/development/builders/page-builder) (even though the widgets are rendered without the Page Builder interface).
  * To allow direct rendering, widgets must be registered using the `RegisterWidget` assembly attribute (as described in [Widgets for Page Builder](/documentation/developers-and-admins/development/builders/page-builder/widgets-for-page-builder)).
  * The page where the widget is rendered must include links to [Page Builder scripts and styles](/documentation/developers-and-admins/development/builders/page-builder/create-pages-with-editable-areas#load-page-builder-scripts-and-styles), if these are required by the given widget. The [system’s default widgets](/documentation/developers-and-admins/development/builders/page-builder/reference-default-page-builder-widgets) require the Page Builder scripts and styles.


## Render widgets
Edit the code of the view where you want to display the widget and call the `Html.Kentico().RenderStandaloneWidgetAsync` extension method (or its [Tag Helper](/documentation/developers-and-admins/development/reference-tag-helpers) equivalent).
Specify the following parameters for the method:
  * `identifier` – the `string` identifier under which the widget was registered. For identifiers of the default system widgets, check [the system widget reference](/documentation/developers-and-admins/development/builders/page-builder/reference-default-page-builder-widgets).
  * `properties` – `IWidgetProperties` object representing the widget’s [properties](/documentation/developers-and-admins/development/builders/page-builder/widgets-for-page-builder/widget-properties). Create an instance of the appropriate property model class and set any required values. Currently, the properties parameter is required and you cannot render widgets without properties. As a workaround, you can create and register an empty property model for the widget.


cshtml
Copy
```
@using System.Collections.Generic

@using Kentico.PageBuilder.Web.Mvc
@using Kentico.Content.Web.Mvc
@using Kentico.Web.Mvc
@using Kentico.Forms.Web.Mvc.Widgets
@using Kentico.Xperience.Admin.Base.Forms

...

@{
    // Prepares properties for the Form widget
    var widgetProperties = new FormWidgetProperties()
    {
        SelectedForm = new List<ObjectRelatedItem>() { new ObjectRelatedItem { ObjectCodeName = "FormCodeName" } },
        AfterSubmitMode = FormAfterSubmitModeConstants.DISPLAY_MESSAGE,
        AfterSubmitDisplayText = "Thank you for your input!"
    };
}

@* Renders the Form widget *@
@await Html.Kentico().RenderStandaloneWidgetAsync(SystemComponentIdentifiers.FORM_WIDGET_IDENTIFIER, widgetProperties);
```

**Note** : Do not use the `RenderStandaloneWidgetAsync` method in the views of other widgets. If you are developing a widget that extends the HTML output or functionality of an existing widget, call the `RenderNestedWidgetAsync` extension method instead. For more information, see [Extend widgets](/documentation/developers-and-admins/development/builders/page-builder/widgets-for-page-builder/extend-widgets).
![]()
[]()[]()
