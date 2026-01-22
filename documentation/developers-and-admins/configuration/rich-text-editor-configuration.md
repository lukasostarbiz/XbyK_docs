---
source: https://docs.kentico.com/documentation/developers-and-admins/configuration/rich-text-editor-configuration
scrape_date: 2026-01-22
---

  * [Home](/documentation)
  * [Developers and admins](/documentation/developers-and-admins)
  * [Configuration](/documentation/developers-and-admins/configuration)
  * Rich text editor configuration 


# Rich text editor configuration
The [rich text editor](/documentation/business-users/rich-text-editor) available in the admin UI allows users to input content using conventional word processor features. The editor is based on the [Froala](https://froala.com/wysiwyg-editor/) WYSIWYG editor.
The editor provides various customization options:
  * [Prepare editor configurations](#customize-the-editor), for example to adjust the set of available formatting options
  * [Add custom plugins](/documentation/developers-and-admins/configuration/rich-text-editor-configuration/rich-text-editor-plugins) that extend the editor’s available feature set
  * [Customize how the system loads editor configurations](/documentation/developers-and-admins/configuration/rich-text-editor-configuration/rich-text-editor-customization)


## Use the rich text editor
The editor can be assigned to:
  * [fields via the field editor](#field-editor)
  * [component properties as an editing component](#editing-component)


### Field editor
You can assign the [rich text editor form component](/documentation/developers-and-admins/customization/extend-the-administration-interface/ui-form-components/reference-admin-ui-form-components) to [fields](/documentation/developers-and-admins/customization/field-editor) of the **Rich text (HTML)** type. To use [editors with custom configurations](#customize-the-editor), select the configuration in the component’s **Configuration name** property.
### Editing component
The rich text [editing component](/documentation/developers-and-admins/customization/extend-the-administration-interface/ui-form-components/editing-components) allows users to input rich text content in:
  * the configuration dialogs or panels of [page builder](/documentation/developers-and-admins/development/builders/page-builder) components (for example widgets)
  * the configuration dialogs of [Form Builder](/documentation/developers-and-admins/development/builders/form-builder) components ([form components](/documentation/developers-and-admins/development/builders/form-builder/form-components) or [sections with properties](/documentation/developers-and-admins/development/builders/form-builder/form-sections))
  * configuration dialogs of other admin UI components ([form components](/documentation/developers-and-admins/customization/extend-the-administration-interface/ui-form-components), [visibility](/documentation/developers-and-admins/customization/extend-the-administration-interface/ui-form-components/ui-form-component-visibility-conditions) and [validation](/documentation/developers-and-admins/customization/extend-the-administration-interface/ui-form-components/ui-form-component-validation-rules) rules)
  * custom [admin UI pages](/documentation/developers-and-admins/customization/extend-the-administration-interface/ui-pages/ui-pages-with-forms)


To use the editor as an editing component, annotate required properties with the `RichTextEditorComponent` attribute. Assign [custom configurations](#customize-the-editor) via the attribute’s `ConfigurationName` property.
C#
**Example - assign the rich text editor to a widget property**
Copy
```
using Kentico.PageBuilder.Web.Mvc;
using Kentico.Xperience.Admin.Base.FormAnnotations;

public class CustomWidgetProperties : IWidgetProperties
{
    // Assigns the 'RichTextComponent' as the editing component of the 'RichText' property
    [RichTextEditorComponent]
    public string RichText { get; set; }
}
```

When rendering the output of the rich text editor property for the live site, use the `Html.Kentico().ResolveUrls` extension method to correctly resolve links within the rich text content.
cshtml
**Example - resolve rich text content in views**
Copy
```
@using Kentico.PageBuilder.Web.Mvc
@using Kentico.Web.Mvc

// A view model with the rich text content
@model CustomWidgetViewModel

...

// Displays the HTML content
<div class="fr-view">
    @Html.Kentico().ResolveUrls(Model.RichTextContent)
</div>
```

## HTML allowed in the editor
To improve security of the system, the editor sanitizes all user input and discards any HTML tags, attributes and URI values that are not allowed. By default, only the following items are allowed:
**Default allowed HTML attributes**
“accept”, “accept-charset”, “accesskey”, “action”, “align”, “allowfullscreen”, “allowtransparency”, “alt”, “aria-.”, “async”, “autocomplete”, “autofocus”, “autoplay”, “autosave”, “background”, “bgcolor”, “border”, “charset”, “cellpadding”, “cellspacing”, “checked”, “cite”, “class”, “color”, “cols”, “colspan”, “content”, “contenteditable”, “contextmenu”, “controls”, “coords”, “data”, “data-.”, “datetime”, “default”, “defer”, “dir”, “dirname”, “disabled”, “download”, “draggable”, “dropzone”, “enctype”, “for”, “form”, “formaction”, “frameborder”, “headers”, “height”, “hidden”, “high”, “href”, “hreflang”, “http-equiv”, “icon”, “id”, “ismap”, “itemprop”, “keytype”, “kind”, “label”, “lang”, “language”, “list”, “loop”, “low”, “max”, “maxlength”, “media”, “method”, “min”, “mozallowfullscreen”, “multiple”, “muted”, “name”, “novalidate”, “open”, “optimum”, “pattern”, “ping”, “placeholder”, “playsinline”, “poster”, “preload”, “pubdate”, “radiogroup”, “readonly”, “rel”, “required”, “reversed”, “rows”, “rowspan”, “sandbox”, “scope”, “scoped”, “scrolling”, “seamless”, “selected”, “shape”, “size”, “sizes”, “span”, “src”, “srcdoc”, “srclang”, “srcset”, “start”, “step”, “summary”, “spellcheck”, “style”, “tabindex”, “target”, “title”, “type”, “translate”, “usemap”, “value”, “valign”, “webkitallowfullscreen”, “width”, “wrap”
**Default allowed HTML tags**
“a”, “abbr”, “address”, “area”, “article”, “aside”, “audio”, “b”, “base”, “bdi”, “bdo”, “blockquote”, “br”, “button”, “canvas”, “caption”, “cite”, “code”, “col”, “colgroup”, “datalist”, “dd”, “del”, “details”, “dfn”, “dialog”, “div”, “dl”, “dt”, “em”, “embed”, “fieldset”, “figcaption”, “figure”, “footer”, “form”, “h1”, “h2”, “h3”, “h4”, “h5”, “h6”, “header”, “hgroup”, “hr”, “i”, “iframe”, “img”, “input”, “ins”, “kbd”, “keygen”, “label”, “legend”, “li”, “link”, “main”, “map”, “mark”, “menu”, “menuitem”, “meter”, “nav”, “noscript”, “object”, “ol”, “optgroup”, “option”, “output”, “p”, “param”, “picture”, “pre”, “progress”, “queue”, “rp”, “rt”, “ruby”, “s”, “samp”, “section”, “select”, “small”, “source”, “span”, “strike”, “strong”, “sub”, “summary”, “sup”, “table”, “tbody”, “td”, “textarea”, “tfoot”, “th”, “thead”, “time”, “title”, “tr”, “track”, “u”, “ul”, “var”, “video”, “wbr”
**Allowed URIs**
URI values are only allowed if they start with the following schemes:
“http”, “https”, “mailto”, “tel”, “data”
Only the following MIME types are allowed for URIs with the _data_ scheme:
“image/avif”, “image/bmp”, “image/gif”, “image/jpeg”, “image/png”, “image/webp”
The default set of allowed HTML attributes and tags can be manually [extended](#allow-custom-attributes-and-tags) if required.
### Allow custom attributes and tags
If you wish to use tags and attributes other than the [default allowed HTML](#html-allowed-in-the-editor) in the rich text editor, you need to manually allow them as exceptions in the administration:
  1. Open the **Settings** application.
  2. Under **Content** navigate to **Rich Text Editor → Custom HTML attributes and tags**.
  3. Enter the desired tags/attributes into the respective fields. 
     * Enter the tags/attributes without double quotes and separate them by commas.
  4. **Save** the settings.


The specified attributes and tags are now allowed in the rich text editor on top of the default set.
**Notes** :
  * When adding tags that require attributes, you need to add said tag and all of the required attributes. E.g., if you want to use `<a href=""></a>` you need to add the “ _a_ ” tag as well as the “ _href_ ” attribute.
  * When adding tags that can be empty (e.g., `<hr />`), you need to also add the tag in the [htmlAllowedEmptyTagsoption](https://froala.com/wysiwyg-editor/docs/options/#htmlAllowedEmptyTags) of the editor [configuration](#define-editor-configurations).


## Customize the editor
For better user experience or to suit specific scenarios, you can customize the set of features offered by individual instances of the editor.
For example, you can limit the set of buttons and offered formatting options, or change the editor’s behavior. For the full list of available configuration options, see [Options](https://froala.com/wysiwyg-editor/docs/options/) in the official Froala documentation. You can also use the [Default Xperience plugins](/documentation/developers-and-admins/configuration/rich-text-editor-configuration/rich-text-editor-plugins) in your configurations.
If you configure custom handlers for the rich text editor [drop event](https://froala.com/wysiwyg-editor/docs/events/#drop-dropevent) or disable the upload functionality using Froala options (e.g., [imageUpload](https://froala.com/wysiwyg-editor/docs/options/#imageupload)), [mass asset upload](/documentation/business-users/content-hub/content-item-assets#mass-asset-upload) may not work correctly.
**Default editor configuration**
When added in its default state to custom fields or properties, the editor uses the following configuration:
JSON
Copy
```
{
    attribution: false,
    listAdvancedTypes: false,
    linkEditButtons: ['linkOpen', 'linkEdit', 'linkRemove'],
    linkInsertButtons: ['linkBack'],
    linkConvertEmailAddress: false,
    quickInsertEnabled: false,
    pasteDeniedAttrs: ['id', 'style'],
    toolbarButtons: [
        'bold',
        'italic',
        'underline',
        'paragraphFormat',
        'formatOL',
        'formatUL',
        'outdent',
        'indent',
        'alignLeft',
        'alignCenter',
        'alignRight',
        'html'
    ],
    paragraphFormat: {
        N: 'Paragraph',
        H1: 'Heading 1',
        H2: 'Heading 2',
        H3: 'Heading 3',
        H4: 'Heading 4',
        H5: 'Heading 5',
        H6: 'Heading 6',
    },
    customPlugins: []
}
```

The rich text editors in Xperience extend or override the default options with additional configuration tailored to different contexts. See [Rich text editor customization](/documentation/developers-and-admins/configuration/rich-text-editor-configuration/rich-text-editor-customization#replace-the-default-editor-configuration) for a list of available configurations.
For example, the following configuration is applied when editing structured content of pages on the _Content_ view under a website channel.
**Configuration example for a page with structured content**
JSON
Copy
```
{
  "imageDefaultWidth": 0,
  "imageResize": true,
  "imageUpload": false,
  "imagePaste": false,
  "imageDefaultMargin": null,
  "imageMove": true,
  "imageAddNewLine": false,
  "toolbarButtons": [
    "bold",
    "italic",
    "underline",
    "paragraphFormat",
    "formatOL",
    "formatUL",
    "outdent",
    "indent",
    "alignLeft",
    "alignCenter",
    "alignRight",
    "html",
    "insertLinkDropDown"
  ],
  "imageEditButtons": [ "imageReplace", "imageAlt", "imageAlign", "imageDisplay", "imageSize", "imageRemove" ],
  "customPlugins": [
    {
      "pluginName": "@kentico/xperience-admin-base/Link",
      "pluginOptions": {
        "dropdownOptions": [
          {
            "linkOption": "asset",
            "componentName": "@kentico/xperience-admin-base/AssetPanel"
          },
          {
            "linkOption": "external"
          },
          {
            "linkOption": "webpage",
            "componentName": "@kentico/xperience-admin-websites/PageLink"
          }
        ]
      }
    }
  ]
}
```

Use the following process to customize the editor:
  1. [Define editor configurations](#define-editor-configurations)
  2. [Register the configurations in the system](#register-editor-configurations)
  3. Assign the configurations to: 
    1. [rich text editing components](#assign-editor-configurations-to-rich-text-editing-components)
    2. [fields that use the rich text form component](#assign-editor-configurations-to-rich-text-fields)


### Define editor configurations
Some configurations may need additional custom CSS to work correctly. For more information on how to add custom styles to the rich text editor, see [Rich text editor customization](/documentation/developers-and-admins/configuration/rich-text-editor-configuration/rich-text-editor-customization#add-custom-css-to-the-rich-text-editor).
Editor configurations are defined using [JSON options objects](https://froala.com/wysiwyg-editor/docs/options) that the system uses when instantiating a rich text editor.
  1. Prepare a [custom assembly](/documentation/developers-and-admins/customization/integrate-custom-code) (_Class Library_ project) in your Xperience solution.
     * We recommend placing code and resources used only by the Xperience administration in [dedicated assemblies](/documentation/developers-and-admins/customization/extend-the-administration-interface/admin-ui-customization-model-overview).
  2. Create a new JSON file in the assembly (place the file into a suitable directory for storing editor configurations).
  3. In this file, define an editor configuration using the available configuration options.
The following example includes only basic text formatting options in the editor toolbar, along with the default Xperience plugins for adding media files and links.
JSON
**CustomEditorConfiguration.json**
Copy
```
{
  "imageDefaultWidth": 0,
  "imageResize": true,
  "imageUpload": false,
  "imagePaste": false,
  "imageDefaultMargin": null,
  "imageMove": true,
  "imageAddNewLine": false,
  "toolbarButtons": [
    "paragraphFormat",
    "|",
    "bold",
    "italic",
    "underline",
    "|",
    "align",
    "formatOL",
    "formatUL",
    "|",
    "insertAsset",
    "insertLinkDropDown"
  ],
  "imageEditButtons": [ "imageReplace", "imageAlt", "imageAlign", "imageDisplay", "imageSize", "imageRemove" ],
  "customPlugins": [
    {
      "pluginName": "@kentico/xperience-admin-base/Asset"
    },
    {
      "pluginName": "@kentico/xperience-admin-base/Link",
      "pluginOptions": {
        "dropdownOptions": [
          {
            "linkOption": "asset",
            "componentName": "@kentico/xperience-admin-base/AssetPanel"
          },
          {
            "linkOption": "external"
          }
        ]
      }
    }
  ]
}
```



**Froala plugins**
The Froala editor uses a modular architecture that in its default configuration contains only essential features. You can [add existing community plugins or implement custom plugins](/documentation/developers-and-admins/configuration/rich-text-editor-configuration/rich-text-editor-plugins) to expand the available feature set.
### Register editor configurations
Editor configurations must fulfill both of the following:
  * Must be stored as **embedded resources** within application assemblies.
  * Must be registered in the system under a unique identifier (the registration code must be placed in a [custom assembly](/documentation/developers-and-admins/customization/integrate-custom-code) with class discovery enabled).


Use the following process to register a configuration:
  1. Embed the configuration JSON in an assembly via the `EmbeddedResource` element.
XML
**Embedding the configuration via an assembly .csproj file**
Copy
```
<Project Sdk="Microsoft.NET.Sdk">

...

    <ItemGroup>
        <EmbeddedResource Include="path\to\custom\configuration.json" />
    </ItemGroup>

</Project>
```

  2. Create a new class that inherits from `RichTextEditorConfiguration`. Within the class:
    1. Assign a unique string identifier to the configuration.
       * Use a constant named `IDENTIFIER`. This allows you to easily reference the configuration in your code.
       * We recommend using a unique prefix in your identifiers to prevent conflicts when deploying components to other projects. For example, use prefixes matching your company name.
    2. Call the base class constructor with the following parameters:
       * `relativePath` – the relative path to the configuration file within the assembly.
       * (Optional) `baseNamespace` – allows you to provide a different assembly name in case the configuration file is stored in a different assembly. If set, the system searches the specified assembly (using the given `relativePath`) instead.
C#
**Example - registering a configuration to the system**
Copy
```
using Kentico.Xperience.Admin.Base.Forms;

public class CustomRichTextEditorConfiguration : RichTextEditorConfiguration
{
    // Relative path to the configuration file within the specified assembly
    // This example configuration is located under: <AssemblyName>\Misc\RichTextEditorConfiguration\
    private const string CONFIGURATION_PATH = "Misc\\RichTextEditorConfiguration\\RichTextEditorConfiguration.json";

    // String identifier assigned to the configuration
    public const string IDENTIFIER = "Acme.Web.EditorConfiguration";

    // User-friendly name of the configuration
    public const string DISPLAY_NAME = "Custom ACME editor configuration";

    public CustomRichTextEditorConfiguration()
        : base(CONFIGURATION_PATH)
    {
    }
}
```



  * Register the class using the `RegisterRichTextEditorConfiguration` assembly attribute. Provide the following arguments for the registration attribute:
    * the configuration’s identifier
    * the type of the class derived from `RichTextEditorConfiguration` (using the `typeof` operator)
    * a user-friendly display name, which appears when selecting the configuration for the _Rich text editor_ form component in the [field editor](/documentation/developers-and-admins/customization/field-editor).
C#
**Example - registering a plugin to the system**
Copy
```
using Kentico.Xperience.Admin.Base.Forms;

[assembly: RegisterRichTextEditorConfiguration(CustomRichTextEditorConfiguration.IDENTIFIER, typeof(CustomRichTextEditorConfiguration), CustomRichTextEditorConfiguration.DISPLAY_NAME)]
public class CustomRichTextEditorConfiguration : RichTextEditorConfiguration
```



The configuration is now registered in the system and can be assigned to:
  * [rich text editing components](#assign-editor-configurations-to-rich-text-editing-components)
  * [fields that use the rich text form component](#assign-editor-configurations-to-rich-text-fields)


### Assign editor configurations to rich text editing components
To assign custom configurations to rich text editors within component configuration dialogs:
  1. [Register the configuration](#register-editor-configurations).
  2. Decorate the component property intended to store the rich text content with the `RichTextEditorComponent` attribute. The attribute assigns the [Rich text editor form component](/documentation/developers-and-admins/customization/extend-the-administration-interface/ui-form-components/reference-admin-ui-form-components) as the property’s [editing component](/documentation/developers-and-admins/customization/extend-the-administration-interface/ui-form-components/editing-components).
  3. Provide the configuration to use via the attribute’s `ConfigurationName` property. You can use the declared `IDENTIFIER` constant to reference the desired configuration.


C#
**Example - assigning a custom toolbar to a configuration property**
Copy
```
using Kentico.Forms.Web.Mvc;
using Kentico.Xperience.Admin.Base.FormAnnotations;

public class TitledSectionProperties : IFormSectionProperties
{
    // Uses the 'RichTextComponent' as the editing component of the 'SectionText' property
    // and assigns a custom configuration for the Froala editor
    [RichTextEditorComponent(ConfigurationName = CustomRichTextEditorConfiguration.IDENTIFIER)]
    public string SectionText { get; set; }
}
```

### Assign editor configurations to rich text fields
For [fields](/documentation/developers-and-admins/customization/field-editor) that use the **Rich text editor** form component, you can set custom configurations via the **Configuration name** property.
  1. [Register the configuration](#register-editor-configurations) in the system.
  2. Open the object you wish to modify in the field editor.
  3. Select a field using the _Rich text editor_ form component (available for fields of the **Rich text (HTML)** [data type](/documentation/developers-and-admins/customization/field-editor/data-type-management)).
  4. Select the configuration via the **Configuration name** property.
  5. Save your changes.


The modified field now uses the specified configuration.
![]()
[]()[]()
