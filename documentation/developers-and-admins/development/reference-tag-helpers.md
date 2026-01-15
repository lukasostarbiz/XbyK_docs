# Reference - Tag Helpers
  * [ Copy page link ](documentation/developers-and-admins/development/reference-tag-helpers#) | [Get HelpService ID](documentation/developers-and-admins/development/reference-tag-helpers#)
Core MVC 5


[✖](documentation/developers-and-admins/development/reference-tag-helpers# "Close page link panel") [Copy to clipboard](documentation/developers-and-admins/development/reference-tag-helpers#)
This page serves as a reference of all [ASP.NET Core Tag Helpers](https://docs.microsoft.com/en-US/aspnet/core/mvc/views/tag-helpers) provided by the Xperience API.
## Add Tag Helpers to views
You can add individual Tag Helpers to your views via the `@addTagHelper` directive. Specify the fully qualified name of the Tag Helper class and the name of the assembly where it is located (both provided in the corresponding sections below). Alternatively, you can use the ‘*’ wildcard to include all Tag Helpers matching a specified pattern.
cshtml
Copy
```
@* Adds all Tag Helpers from the Kentico.Content.Web.Mvc assembly to the view *@
@addTagHelper *, Kentico.Content.Web.Mvc

@* Adds the form zone Tag Helper to the view *@
@addTagHelper Kentico.Content.Web.Mvc.FormZoneTagHelper, Kentico.Content.Web.Mvc
```

Add the Tag Helpers in relevant [_ViewImports.cshtml](https://docs.microsoft.com/en-us/aspnet/core/mvc/views/layout#importing-shared-directives) files to make them available in all related views automatically. 
## Attribute Tag Helpers – Images
To simplify the work with content item images when developing your website (e.g., when implementing [responsive images](https://developer.mozilla.org/en-US/docs/Web/HTML/Responsive_images)), you can use the following attribute Tag Helpers that target attributes of the `<img>` tag and the `<source>` tag inside a `<picture>` element.
**Fully qualified name:**
  * Kentico.Content.Web.Mvc.ContentItemAssetImageTagHelper
  * Kentico.Content.Web.Mvc.ContentItemAssetPictureSourceTagHelper


**Assembly name:** Kentico.Content.Web.Mvc
The Tag Helpers provide the following attributes:
  * `kxp-img`
    * Renders markup of the `src` (inside of `<img>`) or `srcset` (inside of `<source>`) attributes with the image URL as their value.
    * Accepts a [ContentItemAsset](documentation/developers-and-admins/development/content-retrieval/retrieve-content-items#retrieve-assets) object. 
cshtml
Copy
```
@addTagHelper Kentico.Content.Web.Mvc.ContentItemAssetImageTagHelper, Kentico.Content.Web.Mvc

<img kxp-img="@Model.ContentItemAsset" ... />
```

  * `kxp-img-dimensions`
    * Renders markup of the `width` and `height` attributes with the image dimensions as their values. If used in conjunction with `kxp-img-variant`, the variant’s dimensions are used instead.
    * Requires concurrent usage of `kxp-img`. 
cshtml
Copy
```
@addTagHelper Kentico.Content.Web.Mvc.ContentItemAssetImageTagHelper, Kentico.Content.Web.Mvc

<img kxp-img="@Model.ContentItemAsset" kxp-img-dimensions ... />
```

  * `kxp-img-variant`
    * Specifies which [image variant](documentation/developers-and-admins/development/content-types#configure-image-variants) is rendered in `kxp-img`.
    * Accepts the code name identifier of the image variant.
    * Requires concurrent usage of `kxp-img`. 
cshtml
Copy
```
@addTagHelper Kentico.Content.Web.Mvc.ContentItemAssetImageTagHelper, Kentico.Content.Web.Mvc.ContentItemAssetPictureSourceTagHelper, Kentico.Content.Web.Mvc

@* Works well with both scaled and smart crop image variants *@
<img kxp-img="@Model.ContentItemAsset" kxp-img-dimensions kxp-img-variant="variantCodeName" ... />

@* Best used with smart-crop variants to address art direction scenarios *@
<picture>
    <source media="(min-width: 1024px)" kxp-img="@Model.ContentItemAsset" kxp-img-dimensions kxp-img-variant="variantCodeName1" ... />
    <source media="(min-width: 600px)" kxp-img="@Model.ContentItemAsset" kxp-img-dimensions kxp-img-variant="variantCodeName2" ... />
    <img kxp-img="@Model.ContentItemAsset" kxp-img-dimensions ... />
</picture>
```



Currently, there is no attribute Tag Helper in the Xperience API that supports rendering of `srcset` inside of the `<img>` tag. To achieve this, you must either construct the `srcset` attribute manually or implement a custom attribute Tag Helper in your project.
## Tag Helpers – Page Builder
### Page Builder scripts
**Fully qualified name:** Kentico.Content.Web.Mvc.PageBuilderScriptsTagHelper
**Assembly name:** Kentico.Content.Web.Mvc
Renders markup that includes scripts required by the [Page Builder](documentation/developers-and-admins/development/builders/page-builder/create-pages-with-editable-areas) feature. Serves as an alternative to the `HtmlHelper.Kentico().PageBuilderScripts` extension method.
The Tag Helper includes scripts used in the system’s editing interface, as well as those of individual Page Builder components ([widgets](documentation/developers-and-admins/development/builders/page-builder/widgets-for-page-builder), [sections](documentation/developers-and-admins/development/builders/page-builder/sections-for-page-builder), [inline property editors](documentation/developers-and-admins/development/builders/page-builder/widgets-for-page-builder/inline-editors-for-widget-properties)). See the linked documentation to learn how to correctly add custom scripts for your own components.
cshtml
Copy
```
@addTagHelper Kentico.Content.Web.Mvc.PageBuilderScriptsTagHelper, Kentico.Content.Web.Mvc

<page-builder-scripts />
```

### Page Builder styles
**Fully qualified name:** Kentico.Content.Web.Mvc.PageBuilderStylesTagHelper
**Assembly name:** Kentico.Content.Web.Mvc
Renders markup that includes style sheets required by the [Page Builder](documentation/developers-and-admins/development/builders/page-builder/create-pages-with-editable-areas) feature. Serves as an alternative to the `HtmlHelper.Kentico().PageBuilderStyles` extension method.
The Tag Helper includes style sheets used in the system’s editing interface, as well as those of individual Page Builder components ([widgets](documentation/developers-and-admins/development/builders/page-builder/widgets-for-page-builder), [sections](documentation/developers-and-admins/development/builders/page-builder/sections-for-page-builder), [inline property editors](documentation/developers-and-admins/development/builders/page-builder/widgets-for-page-builder/inline-editors-for-widget-properties)). See the linked documentation to learn how to correctly add custom style sheets for your own components.
cshtml
Copy
```
@addTagHelper Kentico.Content.Web.Mvc.PageBuilderStylesTagHelper, Kentico.Content.Web.Mvc

<page-builder-styles />
```

### Editable area
**Fully qualified name:** Kentico.Content.Web.Mvc.EditableAreaTagHelper
**Assembly name:** Kentico.Content.Web.Mvc
Marks a location where a Page Builder [editable area](documentation/developers-and-admins/development/builders/page-builder/create-pages-with-editable-areas) is to be rendered when editing the page via the administration interface. Can be used as an alternative to the `HtmlHelper.Kentico().EditableAreaAsync` extension method.
The helper has the following required attributes:
  * `area-identifier` – identifier of the area unique within the context of the given page.


cshtml
Copy
```
@addTagHelper Kentico.Content.Web.Mvc.EditableAreaTagHelper, Kentico.Content.Web.Mvc

<editable-area area-identifier="areaSimple" />
```

Optionally, you can pass an `EditableAreaOptions` object via the `area-options` attribute to further configure the area. See [Create pages with editable areas](documentation/developers-and-admins/development/builders/page-builder/create-pages-with-editable-areas) for details.
cshtml
Copy
```
@addTagHelper Kentico.Content.Web.Mvc.EditableAreaTagHelper, Kentico.Content.Web.Mvc
@using Kentico.PageBuilder.Web.Mvc

@{
    var options = new EditableAreaOptions
    {
        AllowedWidgets = new[] { "LearningKit.Widgets.NumberWidget",
                                 "LearningKit.Widgets.SomeOtherWidget" },
        AllowedSections = AllowedComponents.ALL
    };
}

<editable-area area-identifier="areaConfigured" area-options="options" />
```

Alternatively, you can pass each property of the `EditableAreaOptions` object as an individual attribute:
  * `area-options-allowed-sections` – a list of [section](documentation/developers-and-admins/development/builders/page-builder/sections-for-page-builder) identifiers that can be added to the area. If left empty, all sections are allowed.
  * `area-options-allowed-widgets` – a list of [widget](documentation/developers-and-admins/development/builders/page-builder/widgets-for-page-builder) identifiers that can be added to the area. If left empty, all widgets are allowed.
  * `area-options-default-section-identifier` – identifier of the default section for the area.
  * `allow-widget-output-cache` – indicates whether the output of individual widgets placed into the area can be [cached](documentation/developers-and-admins/development/caching/output-caching). This value is combined with the _AllowCache_ property of widgets (specified during [widget registration](documentation/developers-and-admins/development/builders/page-builder/widgets-for-page-builder)) to determine whether output caching is enabled for each widget instance contained within the area. The default value is _false_.
  * `widget-output-cache-expires-on` – a _DateTimeOffset_ value that sets the absolute expiration date for cached content.
  * `widget-output-cache-expires-after` – a _TimeSpan_ value that sets the length of time from the first request time to cache the contents.
  * `widget-output-cache-expires-sliding` – a _TimeSpan_ value that defines a sliding window of expiration for the cached content. Content not accessed within the specified time frame gets evicted.


Note that if you both pass the options object and specify some parameters directly via attributes, the parameters specified via attributes take precedence (i.e., override the corresponding properties of the options object).
#### Cache configuration examples
The caching configuration options are modeled after and behave identically to the [Cache tag helper](https://docs.microsoft.com/en-us/aspnet/core/mvc/views/tag-helpers/built-in/cache-tag-helper) provided by ASP.NET Core:
cshtml
Copy
```
@* widget-output-cache-expires-on *@
@* Sets the cached entry to expire on 5:02 PM on January 29, 2025. If the specified date is in the past, nothing gets cached. *@
<editable-area area-identifier="editableArea" area-widget-output-cache="true" widget-output-cache-expires-on="@new DateTime(2025,1,29,17,02,0)" />

@* widget-output-cache-expires-after *@
@* Contents are evicted after five minutes and cached again on subsequent requests. *@
<editable-area area-identifier="editableArea" area-widget-output-cache="true" widget-output-cache-expires-after="@TimeSpan.FromSeconds(300)" />

@* widget-output-cache-expires-sliding *@
@* Cached content is evicted if not accessed again within 120 seconds of the initial request. *@
<editable-area area-identifier="editableArea" area-widget-output-cache="true" widget-output-cache-expires-sliding="@TimeSpan.FromSeconds(120)" />
```

### Widget zone
**Fully qualified name:** Kentico.Content.Web.Mvc.WidgetZoneTagHelper
**Assembly name:** Kentico.Content.Web.Mvc
Marks a location where widgets can be placed within a [Page Builder section](documentation/developers-and-admins/development/builders/page-builder/sections-for-page-builder). Serves as an alternative to the `HtmlHelper.Kentico().WidgetZoneAsync` extension method.
Every section must contain at least one widget zone – sections without widget zones are not supported.
cshtml
Copy
```
@addTagHelper Kentico.Content.Web.Mvc.WidgetZoneTagHelper, Kentico.Content.Web.Mvc

<widget-zone />
```

The helper also has the following optional attributes:
  * `zone-name` – identifier of the zone used when changing the type of the zone in the Page Builder.
  * `allowed-widgets` – a list of [widget](documentation/developers-and-admins/development/builders/page-builder/widgets-for-page-builder) identifiers that can be added to the zone. If left empty, all widgets are allowed.


cshtml
Copy
```
@{
    var widgetsWhitelist = new[] { Kentico.Content.Web.Mvc.SystemComponentIdentifiers.FORM_WIDGET_IDENTIFIER,
                                   "LearningKit.Widgets.NumberWidget",
                                   "LearningKit.Widgets.SomeOtherWidget" };
}

<widget-zone zone-name="main-zone" allowed-widgets="widgetsWhitelist" />
```

### Page data
**Fully qualified name:** Kentico.Content.Web.Mvc.WidgetZoneTagHelper
**Assembly name:** Kentico.Content.Web.Mvc
Renders an invisible form field that includes contextual information about the page. Necessary when developing components that use [POST actions](documentation/developers-and-admins/development/builders/page-builder/widgets-for-page-builder). Serves as an alternative to the `HtmlHelper.Kentico().PageData` extension method.
cshtml
Copy
```
@addTagHelper Kentico.Content.Web.Mvc.PageDataTagHelper, Kentico.Content.Web.Mvc

<form asp-controller="Controller" asp-action="HandlePost" id="form">

    ...

    <page-data />

    <input type="submit" value="Submit" />
</form>
```

### Component properties data
**Fully qualified name:** Kentico.Content.Web.Mvc.ComponentPropertiesDataTagHelper
**Assembly name:** Kentico.Content.Web.Mvc
Used to persist properties of the currently rendered component for POST actions. Server as an alternative to the `HtmlHelper.Kentico().ComponentPropertiesData` extension method. 
cshtml
Copy
```
@addTagHelper Kentico.Content.Web.Mvc.ComponentPropertiesDataTagHelper, Kentico.Content.Web.Mvc

<form asp-controller="Controller" asp-action="HandlePost" id="form">

    ...

    <component-properties-data />

    <input type="submit" value="Submit" />
</form>
```

### Stand-alone widget
**Fully qualified name:** Kentico.Content.Web.Mvc.StandaloneWidgetTagHelper
**Assembly name:** Kentico.Content.Web.Mvc
Renders a specified widget directly. Serves as an alternative to the `HtmlHelper.Kentico().RenderStandaloneWidgetAsync` extension method. See [Render widgets in code](documentation/developers-and-admins/development/builders/page-builder/render-widgets-in-code).
The helper has the following attributes:
  * `widget-type-identifier` – the `string` identifier under which the widget was registered.
  * (Optional) `widget-properties` – `IWidgetProperties` object representing the widget’s [properties](documentation/developers-and-admins/development/builders/page-builder/widgets-for-page-builder/widget-properties). Create an instance of the appropriate property model class and set any required values.


cshtml
Copy
```
@using System.Collections.Generic

@addTagHelper Kentico.Content.Web.Mvc.StandaloneWidgetTagHelper, Kentico.Content.Web.Mvc
@using Kentico.Forms.Web.Mvc.Widgets
@using Kentico.Content.Web.Mvc
@using Kentico.Xperience.Admin.Base.Forms

@{
    // Prepares properties for the Form widget
    var widgetProperties = new FormWidgetProperties()
    {
        SelectedForm = new List<ObjectRelatedItem>() { new ObjectRelatedItem { ObjectCodeName = "FormCodeName" } }
    };
}

@* Renders the system's default Form widget *@
<standalone-widget widget-type-identifier="@SystemComponentIdentifiers.FORM_WIDGET_IDENTIFIER" widget-properties="widgetProperties" />
```

### Nested widget
**Fully qualified name:** Kentico.Content.Web.Mvc.NestedWidgetTagHelper
**Assembly name:** Kentico.Content.Web.Mvc
Renders a widget nested inside another widget. Serves as an alternative to the `HtmlHelper.Kentico().RenderNestedWidgetAsync` extension method. See [Extend widgets](documentation/developers-and-admins/development/builders/page-builder/widgets-for-page-builder/extend-widgets).
cshtml
Copy
```
@addTagHelper Kentico.Content.Web.Mvc.NestedWidgetTagHelper, Kentico.Content.Web.Mvc

@* In this example, the extended widget uses an extended FormWidgetProperties class *@
@model ComponentViewModel<ExtendedFormWidgetProperties>
@* Renders the system's default Form widget as the nested widget *@
<nested-widget widget-type-identifier="@SystemComponentIdentifiers.FORM_WIDGET_IDENTIFIER" widget-properties="Model.Properties" />
```

## Tag Helpers – Form Builder
### Form zone
**Fully qualified name:** Kentico.Content.Web.Mvc.FormZoneTagHelper
**Assembly name:** Kentico.Content.Web.Mvc
Marks a location where form components can be placed within a form section. Serves as an alternative to the `HtmlHelper.Kentico().FormZoneAsync` extension method.
cshtml
Copy
```
@addTagHelper Kentico.Content.Web.Mvc.FormZoneTagHelper, Kentico.Content.Web.Mvc

<form-zone />
```

The helper also has the following optional attributes:
  * `zone-name` – identifier of the zone used when changing the type of the zone in the Page Builder.


cshtml
Copy
```
<form-zone zone-name="main-zone" />
```

## Tag Helpers – Caching
### Cache dependency
**Fully qualified name:** Kentico.Web.Mvc.Caching.CacheDependencyTagHelper
**Assembly name:** Kentico.Web.Mvc
Allows for specification of dependency cache keys within the [Cache tag helper](https://docs.microsoft.com/en-us/aspnet/core/mvc/views/tag-helpers/built-in/cache-tag-helper) element. See [Output caching](documentation/developers-and-admins/development/caching/output-caching) and [Cache dependencies](documentation/developers-and-admins/development/caching/cache-dependencies).
The helper has the following attributes:
  * `cache-keys` – an array of [cache dependency dummy keys](documentation/developers-and-admins/development/caching/cache-dependencies).
  * `enabled` – indicates whether the dependencies get injected. Enabled by default.


cshtml
Copy
```
@addTagHelper Kentico.Web.Mvc.Caching.CacheDependencyTagHelper, Kentico.Web.Mvc

@{
    var userCacheKeys = new[] { "cms.user|all" };
    var enabled = false;
}
<cache enabled="@enabled">
    @* Clears the cached data whenever a user object is modified *@
    <cache-dependency cache-keys="@userCacheKeys" enabled="@enabled" />

    Time: @DateTime.Now
</cache>
```