---
source: https://docs.kentico.com/documentation/developers-and-admins/development/builders/page-builder/widgets-for-page-builder/example-widget-development
scrape_date: 2026-01-22
---

  * [Home](/documentation)
  * [Developers and admins](/documentation/developers-and-admins)
  * [Development](/documentation/developers-and-admins/development)
  * [Builders](/documentation/developers-and-admins/development/builders)
  * [Page Builder](/documentation/developers-and-admins/development/builders/page-builder)
  * [Widgets for Page Builder](/documentation/developers-and-admins/development/builders/page-builder/widgets-for-page-builder)
  * Example - Widget development 


# Example - Widget development
The following scenario will guide you through the process of developing a simple Page Builder widget step-by-step with full code samples. For more detailed information about the underlying development principles, see our general pages about developing [widgets](/documentation/developers-and-admins/development/builders/page-builder/widgets-for-page-builder), [widget properties](/documentation/developers-and-admins/development/builders/page-builder/widgets-for-page-builder/widget-properties) and [inline editors](/documentation/developers-and-admins/development/builders/page-builder/widgets-for-page-builder/inline-editors-for-widget-properties).
When finished, the widget displays a simple message with a number of your choice. The number is set via a widget property and can be modified through an inline property editor or the properties dialog. The widget can be added to an editable area and displayed on the live site.
**Prerequisite**
To be able to use the widget in Page Builder, you need to [enable the Page Builder feature](/documentation/developers-and-admins/development/builders/page-builder) and [set up an editable area](/documentation/developers-and-admins/development/builders/page-builder/create-pages-with-editable-areas).
**Advanced widget example**
For a detailed walkthrough showing how to create a more complex widget, see the [Define an advanced widgets](/guides/development/page-builder/define-advanced-widget) guide.
## Widget
Create a [widget](/documentation/developers-and-admins/development/builders/page-builder/widgets-for-page-builder) with one modifiable integer [property](/documentation/developers-and-admins/development/builders/page-builder/widgets-for-page-builder/widget-properties).
### Property model
Create a property model **NumberWidgetProperties.cs** in the  _~/Component/Widgets/NumberWidget_ folder:
C#
Copy
```
using Kentico.Forms.Web.Mvc;
using Kentico.PageBuilder.Web.Mvc;

namespace LearningKitCore.Components.PageBuilder.Widgets.NumberWidget
{
    public class NumberWidgetProperties : IWidgetProperties
    {
        // Defines a property and sets its default value
        // Assigns the default Xperience number input component, which allows users to enter
        // a numeric value for the property in the widget's configuration dialog
        [NumberInputComponent(Order = 0, Label = "Number")]
        public int Number { get; set; } = 22;
    }
}
```

### Partial view
Create a partial view **_NumberWidget.cshtml** in the _~/Components/Widgets/NumberWidget_ folder:
cshtml
Copy
```
@addTagHelper *, Microsoft.AspNetCore.Mvc.TagHelpers

@using Kentico.PageBuilder.Web.Mvc

@using LearningKitCore.Components.PageBuilder.InlineEditors.NumberEditor
@using LearningKitCore.Components.PageBuilder.Widgets.NumberWidget

@inject IPageBuilderDataContextRetriever pageBuilderContext

@model ComponentViewModel<NumberWidgetProperties>

<h3 style="background-color: #dddddd;">The number you chose for today is: @Model.Properties.Number</h3>

@* Shows an inline editor when rendered in the edit mode of the Pages application in Xperience *@
@if (pageBuilderContext.Retrieve().GetMode() == PageBuilderMode.Edit)
{
    var inlineEditorModel = new NumberEditorModel
    {
        @* Use the nameof() operator to get the name of the edited property from the widget property model *@
        PropertyName = nameof(NumberWidgetProperties.Number),
        Number = @Model.Properties.Number
    };

    <partial name="~/Components/InlineEditors/NumberEditor/_NumberEditor.cshtml" model="inlineEditorModel" />
}
```

### Widget registration
Register the widget into the system using the **RegisterWidget** assembly attribute:
C#
Copy
```
[assembly: RegisterWidget("LearningKit.Widgets.NumberWidget",
                         "Number selector",
                         typeof(NumberWidgetProperties),
                         customViewName: "~/Components/Widgets/NumberWidget/_NumberWidget.cshtml")]
```

## Inline editor
Implement an [inline editor](/documentation/developers-and-admins/development/builders/page-builder/widgets-for-page-builder/inline-editors-for-widget-properties) able to modify the _Number_ integer property.
### Model
Create an editor model **NumberEditorModel.cs** in the _~/Components/InlineEditors/NumberEditor_ folder:
C#
Copy
```
namespace LearningKitCore.Components.PageBuilder.InlineEditors.NumberEditor
{
    public class NumberEditorModel
    {
        public string PropertyName { get; set; }

        public int Number { get; set; }
    }
}
```

### Partial view
Create a partial view **_NumberEditor.cshtml** in the _~/Components/InlineEditors/NumberEditor_ folder:
cshtml
Copy
```
@using Kentico.PageBuilder.Web.Mvc
@using Kentico.Web.Mvc

@model LearningKitCore.Components.PageBuilder.InlineEditors.NumberEditor.NumberEditorModel

@using (Html.Kentico().BeginInlineEditor("number-editor", Model.PropertyName))
{
    <div style="position: absolute; top: 0px; right: 0px;">
        <button id="plus-btn" type="button">+</button>
        <button id="minus-btn" type="button">-</button>
    </div>
}
```

### JavaScript
Create a JavaScript file **number-editor.js** in the _~/wwwroot/PageBuilder/Admin/InlineEditors/NumberEditor_ folder (this example assumes the default configuration of the system’s bundling support, see [Bundle static assets of builder components](/documentation/developers-and-admins/development/builders/bundle-static-assets-of-builder-components)):
JS
Copy
```
(function () {
    // Registers the 'number-editor' inline property editor within the page builder scripts
    window.kentico.pageBuilder.registerInlineEditor("number-editor", {
        init: function (options) {
            var editor = options.editor;

            // Click action for the 'Plus' button
            editor.querySelector("#plus-btn").addEventListener("click", function () {
                // Creates a custom event that notifies the widget about a change in the value of a property
                var event = new CustomEvent("updateProperty", {
                    detail: {
                        value: options.propertyValue + 1,
                        name: options.propertyName
                    }
                });
                editor.dispatchEvent(event);
            });

            // Click action for the 'Minus' button
            editor.querySelector("#minus-btn").addEventListener("click", function () {
                var event = new CustomEvent("updateProperty", {
                    detail: {
                        value: options.propertyValue - 1,
                        name: options.propertyName
                    }
                });
                editor.dispatchEvent(event);
            });
        }
    });
})();
```

The location of the script file under _~/wwwroot/PageBuilder/Admin/InlineEditors/NumberEditor_ ensures that the script is automatically linked in the administration interface on [pages containing editable areas](/documentation/developers-and-admins/development/builders/page-builder/create-pages-with-editable-areas) (within a bundle of inline editor scripts).
**Dancing Goat sample site**
If you implement this example on the _Dancing Goat_ sample site, you need to manually trigger the [bundling process](/documentation/developers-and-admins/development/builders/bundle-static-assets-of-builder-components#sample-bundling-and-minification-process) to include component scripts in the administration interface. You need to do the following:
  1. Run the `pageBuilder` [Grunt task](https://learn.microsoft.com/en-us/aspnet/core/client-side/using-grunt) available in the sample site to manually trigger the bundling process.
  2. Clear cached web content in your browser.


### Result
[![The number editor widget in the Page Builder UI](/docsassets/documentation/example-widget-development/number-editor.png)](/docsassets/documentation/example-widget-development/number-editor.png)
![]()
[]()[]()
