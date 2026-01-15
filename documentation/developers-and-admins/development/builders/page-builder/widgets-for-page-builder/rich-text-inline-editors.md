# Rich text inline editors
  * [ Copy page link ](documentation/developers-and-admins/development/builders/page-builder/widgets-for-page-builder/rich-text-inline-editors#) | [Get HelpService ID](documentation/developers-and-admins/development/builders/page-builder/widgets-for-page-builder/rich-text-inline-editors#)
Core MVC 5


[✖](documentation/developers-and-admins/development/builders/page-builder/widgets-for-page-builder/rich-text-inline-editors# "Close page link panel") [Copy to clipboard](documentation/developers-and-admins/development/builders/page-builder/widgets-for-page-builder/rich-text-inline-editors#)
Xperience provides a rich text [inline editor](documentation/developers-and-admins/development/builders/page-builder/widgets-for-page-builder/inline-editors-for-widget-properties) that allows users to input content using conventional word processor features. The editor is based on the [Froala](https://froala.com/wysiwyg-editor/) WYSIWYG editor.
This page describes how to work with the rich text editor as an [inline editor](documentation/developers-and-admins/development/builders/page-builder/widgets-for-page-builder/inline-editors-for-widget-properties) for [Page Builder widget properties](documentation/developers-and-admins/development/builders/page-builder/widgets-for-page-builder/widget-properties).
To learn about other rich text editor scenarios, see:
  * [Inline editors for Email Builder widgets](documentation/developers-and-admins/development/builders/email-builder/inline-editors-email-builder-widgets#rich-text-inline-editor) –using the rich text inline editor for Email Builder widget properties.
  * [Rich text editor configuration](documentation/developers-and-admins/configuration/rich-text-editor-configuration) – modifying the editor used by the Xperience administration in general.


## Add rich text inline editors to widgets
To add the rich text inline editor to a Page Builder widget, edit the view of the desired widget and call the `Html.Kentico().RichTextEditor()` extension method. Pass the name of the edited property (use the `nameof` expression) as the method’s parameter. When rendering the output of the rich text editor for the live site, use the `Html.Kentico().ResolveRichText` extension method to correctly resolve links within the rich text content.
cshtml
**Example - View of a widget**
Copy
```
@using Kentico.PageBuilder.Web.Mvc
@using Kentico.Web.Mvc

// A model that contains the properties of a custom rich text editing widget
@model ComponentViewModel<CustomWidgetProperties>

...

// Only show the editor in the edit mode (Pages application)
@if (Context.Kentico().PageBuilder().GetMode() == PageBuilderMode.Edit)
{
    // Displays the inline editor
    Html.Kentico().RichTextEditor(nameof(Model.Properties.RichTextContent));
}
else
{
    // Displays the HTML content of the property
    <div class="fr-view">
        @Html.Raw(Html.Kentico().ResolveRichText(Model.Properties.RichTextContent))
    </div>
}
```

The inline editor can now be used to edit the selected property of the desired widget.
To ensure the inline rich text editor’s formatting and CSS styles are applied correctly to the editor’s HTML output, use the `fr-view` class in the HTML wrapper element of the editor’s content.
## HTML allowed in the editor
To improve the security of the system, the editor sanitizes all user input and discards any HTML tags, attributes and URI values that are not allowed. See the [list of allowed HTML](documentation/developers-and-admins/configuration/rich-text-editor-configuration#html-allowed-in-the-editor), which is shared with the editor used by the Xperience administration.
## Customize rich text editor toolbars
To provide a better experience for content editors, you can customize the toolbar of the rich text editor to display a specific set of buttons and formatting options.
To customize the editor toolbar, you need to:
  1. [Define toolbar configurations](documentation/developers-and-admins/development/builders/page-builder/widgets-for-page-builder/rich-text-inline-editors#define-toolbar-configurations)
  2. [Assign configurations to inline editors](documentation/developers-and-admins/development/builders/page-builder/widgets-for-page-builder/rich-text-inline-editors#assign-configurations-to-inline-editors)


### Define toolbar configurations
Within your project, create a JavaScript file for toolbar configurations. We recommended storing toolbar configuration files under a suitable directory in the _~/wwwroot/Scripts_ folder.
In this file, define the [toolbar options](https://froala.com/wysiwyg-editor/docs/options) for each configuration as seen in the following code example.
JS
Copy
```
(function (pageBuilder) {
    var richTextEditor = pageBuilder.richTextEditor = pageBuilder.richTextEditor || {};
    var configurations = richTextEditor.configurations = richTextEditor.configurations || {};
    // Overrides the "default" configuration of the Rich text widget, assuming you have not specified a different configuration
    // The below configuration adds the h5 and h6 values to the paragraphFormat and removes the code value. Also sets the toolbar to be visible without selecting any text.
    configurations["default"] = {
        toolbarVisibleWithoutSelection: true,
        paragraphFormat: {
            N: "Normal",
            H1: "Headline 1",
            H2: "Headline 2",
            H3: "Headline 3",
            H4: "Headline 4",
            H5: "Headline 5",
            H6: "Headline 6",
        },
    };

    // Defines a new configuration for a simple toolbar with only formatting
    // options and disables the inline design of the toolbar
    configurations["simple"] = {
        toolbarButtons: ['paragraphFormat', '|', 'bold', 'italic', 'underline', '|', 'align', 'formatOL', 'formatUL'],
        paragraphFormatSelection: true,
        toolbarInline: false
    };
})(window.kentico.pageBuilder);
```

Certain plugins that can be included within toolbars were adjusted for the Xperience environment (e.g., `insertImage` and `insertLink`)_,_ but these plugins override the corresponding Froala plugins and you can use the default code names to include them in your custom toolbar configurations.
**Custom editor plugins**
In addition to the [listed options](https://froala.com/wysiwyg-editor/docs/options), you can use the Xperience `insertDynamicText` option in your toolbar configurations (to allow editors to include personalized text).
You can also add further options by [implementing plugins](documentation/developers-and-admins/development/builders/page-builder/widgets-for-page-builder/rich-text-inline-editors#implement-custom-plugins-for-the-editor) for the Froala editor.
### Assign configurations to inline editors
There are two ways to assign different toolbar configurations to widget inline editors:
  * Link different configurations to specific pages
  * Specify multiple configurations and reference them from custom rich text widgets


You can combine both approaches. For example, you can create a script to customize the default **Rich text** widget and render it on specific pages. Then you can create an additional script where you define configurations for custom widgets and render this script globally through a shared layout.
#### Customize the toolbar based on location
Any rich text widget, including the default **Rich text** widget, can be customized to use a different toolbar depending on its location. For example, you can configure it to display one toolbar on the home page and a different toolbar on landing pages.
Edit the view of the page where you want to apply the customization and register the desired configuration script. The toolbar configuration script needs to be included **after** Page Builder scripts.
To customize the toolbar of the default **Rich text** widget, override the _default_ configuration `pageBuilder.richTextEditor.configurations['default']` in the script.
cshtml
Copy
```
@using Kentico.PageBuilder.Web.Mvc

...

@* Registers Page Builder scripts *@
@Html.Kentico().PageBuilderScripts()

@* Registers the toolbar configuration script *@
@if (Context.Kentico().PageBuilder().GetMode() == PageBuilderMode.Edit)
{
    <script src="~/Scripts/MyToolbarConfigurations.js"></script>
}
```

#### Customize the toolbar of custom widgets
Custom widgets containing the rich text inline editor can be configured to display a specific toolbar configuration. When rendering the rich text inline editor in the view of a widget, specify the configuration name as an additional parameter of the `RichTextEditor` method. If you do not specify a configuration, the `default` configuration is used.
cshtml
Copy
```
@using Kentico.PageBuilder.Web.Mvc
@using Kentico.Components.Web.Mvc.InlineEditors

@* Adds the rich text inline editor, using the 'custom' toolbar configuration *@
@Html.Kentico().RichTextEditor(editablePropertyName, "custom");
```

To make your toolbar configuration available for all pages, include the corresponding script in your site’s main layout (e.g., _~/Views/Shared/_Layout.cshtml_ ). The toolbar configuration script must be included **after** Page Builder scripts.
cshtml
Copy
```
@using Kentico.PageBuilder.Web.Mvc

...

@* Registers Page Builder scripts *@
@Html.Kentico().PageBuilderScripts()

@* Registers the toolbar configuration script *@
@if (Context.Kentico().PageBuilder().GetMode() == PageBuilderMode.Edit)
{
    <script src="~/Scripts/MyToolbarConfigurations.js"></script>
}
```

## Implement custom plugins for the editor
You can provide any missing features your editors require via plugins. 
Froala plugins are defined in JavaScript files. Within your project, create a JavaScript file for your plugin. We recommended storing plugin files under a suitable directory in the _~/wwwroot/Scripts_ folder. To implement your required functionality, follow the [Froala plugin development documentation](https://froala.com/wysiwyg-editor/docs/concepts/custom/plugin).
**Community plugins not supported**
[Plugins available for download directly](documentation/developers-and-admins/configuration/rich-text-editor-configuration/rich-text-editor-plugins) on Froala’s website and other community sourced plugins are not supported in the inline editor version of the rich text editor. Such plugins can only be added to the editor [used in the admin UI](documentation/developers-and-admins/configuration/rich-text-editor-configuration/rich-text-editor-plugins).
JS
Copy
```
(function (pageBuilder) {
    var richTextEditor = pageBuilder.richTextEditor = pageBuilder.richTextEditor || {};

    // Retrieves the collection of plugins
    var plugins = richTextEditor.plugins = richTextEditor.plugins || [];
    // Creates a plugin
    var myFirstPlugin = function (FroalaEditor) {
            // Registers the plugin under 'myFirstPlugin'
            FroalaEditor.RegisterCommand("myFirstPlugin", { ... });
            ...
        };
    // Adds the plugin to the collection
    plugins.push(myFirstPlugin);
})(window.kentico.pageBuilder);
```

To make the plugins available for rich text inline editors across the entire site, include the scripts into the website’s main layout (e.g.  _~/Views/Shared/_Layout.cshtml_). The scripts must be included **after** [Page Builder](documentation/developers-and-admins/development/builders/page-builder) scripts.
cshtml
Copy
```
@using Kentico.PageBuilder.Web.Mvc

@* Renders Page Builder scripts *@
@Html.Kentico().PageBuilderScripts()

@* Renders plugin scripts *@
@if (Context.Kentico().PageBuilder().GetMode() == PageBuilderMode.Edit)
{
    <script src="~/Scripts/MyPlugin.js"></script>
}
```

After you create a new plugin, reference it by its assigned name in a [toolbar configuration](documentation/developers-and-admins/development/builders/page-builder/widgets-for-page-builder/rich-text-inline-editors#define-toolbar-configurations) to make it available in the rich text editor.
### Example – Creating a custom toolbar button
The following example shows how to create a simple plugin that replaces occurrences of the word ‘you’ with ‘Thou’ and makes it available for use within inline editors.
Create a new JavaScript file: _~/wwwroot/Scripts/sampleButtonPlugin.js_
JS
**sampleButtonPlugin.js**
Copy
```
(function (pageBuilder) {
    var richTextEditor = pageBuilder.richTextEditor = pageBuilder.richTextEditor || {};

    // Retrieves the collection of plugins
    var plugins = richTextEditor.plugins = richTextEditor.plugins || [];
    // Creates a new Sample Button
    var sampleButtonPlugin = function (FroalaEditor) {
            FroalaEditor.DefineIcon("sampleButtonIcon", { SVG_KEY: "help" });
            FroalaEditor.RegisterCommand("sampleButton", {
                title: "Sample Button",
                icon: "sampleButtonIcon",
                callback: function () {
                      var htmlContent = this.html.get();
                      htmlContent=htmlContent.replace(/you/gi, 'Thou');
                      this.html.set(htmlContent);
                }
            });
        };
    // Adds the plugin to the collection
    plugins.push(sampleButtonPlugin);

    // Retrieves the collection of toolbar configurations
    var configurations = richTextEditor.configurations = richTextEditor.configurations || {};
    // Overrides the default toolbar configuration to display the plugin button
    configurations["default"] = {
        toolbarButtons: ['paragraphFormat', '|', 'sampleButton', 'bold', 'italic', 'underline', '|', 'align', 'formatOL', 'formatUL']
    };
})(window.kentico.pageBuilder);
```

Register the JavaScript file in the site’s global layout (_~/Views/Shared/_Layout.cshtml_).
cshtml
**_Layout.cshtml**
Copy
```
@if (Context.Kentico().PageBuilder().GetMode() == PageBuilderMode.Edit)
{
    <script src="~/Scripts/sampleButtonPlugin.js"></script>
}
```

The functionality is now available under the ‘?’ button in the editor of the default **Rich text** widget.