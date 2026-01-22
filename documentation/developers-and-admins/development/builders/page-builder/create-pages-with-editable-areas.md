---
source: https://docs.kentico.com/documentation/developers-and-admins/development/builders/page-builder/create-pages-with-editable-areas
scrape_date: 2026-01-22
---

  * [Home](/documentation)
  * [Developers and admins](/documentation/developers-and-admins)
  * [Development](/documentation/developers-and-admins/development)
  * [Builders](/documentation/developers-and-admins/development/builders)
  * [Page Builder](/documentation/developers-and-admins/development/builders/page-builder)
  * Create pages with editable areas 


# Create pages with editable areas
After you enable the [Page Builder feature](/documentation/developers-and-admins/development/builders/page-builder) for your Xperience application, you can start creating pages with editable areas where content editors can add content using [Page Builder widgets](/documentation/developers-and-admins/development/builders/page-builder/widgets-for-page-builder). Content editors can arrange widgets using predefined layouts to achieve their desired result without having to resort to coding. You can adjust the visual arrangement of widgets on a page via the following components:
  * **Editable areas** – defined directly in the views of your site’s pages. They are the top-level layout component and contain one or more sections.
  * **Sections** – customizable components that specify the visual layout of widget zones. Content editors can choose which sections to add within editable areas, and thus adjust the structure of the page. A section may contain multiple widget zones. See [Sections for Page Builder](/documentation/developers-and-admins/development/builders/page-builder/sections-for-page-builder).
  * **Widget zones** – components that allow content editors to insert widgets. Widget zones are defined in the views of sections.


[![Page builder structure](/docsassets/documentation/create-pages-with-editable-areas/EditableAreas.png)](/docsassets/documentation/create-pages-with-editable-areas/EditableAreas.png)
## Create a page
  1. Open the Xperience administration.
  2. In the **Content types** application, set up a [content type](/documentation/developers-and-admins/development/content-types) for which the Page Builder is enabled.
  3. In the website channel application, create a page of the appropriate content type in the content tree.


## Add editable areas to views
Identify the locations on your website where you want editors to place widgets. Then define these locations by adding editable areas to the code of the corresponding views. The identifier of each editable area must be a string without white spaces that is unique within the context of the given page:
cshtml
Copy
```
@using Kentico.PageBuilder.Web.Mvc
@using Kentico.Web.Mvc

<div>
    @await Html.Kentico().EditableAreaAsync("area1")
</div>
```

You can optionally specify a default [section](/documentation/developers-and-admins/development/builders/page-builder/sections-for-page-builder) for each area.
  1. Create an `EditableAreaOptions` object.
  2. Set the object’s `DefaultSectionsIdentifier` property to match the identifier of the section which you want to use as default.
  3. Assign the object as a parameter of the `EditableAreaAsync` extension method (or its [Tag Helper alternative](/documentation/developers-and-admins/development/reference-tag-helpers)) used to render the given area.


The default section is automatically added into new areas and in cases where an editor removes the last section from an area.
cshtml
Copy
```
<div>
    @{
        var optionsDefaultSection = new EditableAreaOptions
        {
            DefaultSectionIdentifier = "LearningKit.Sections.Col5050"
        };
    }
    @await Html.Kentico().EditableAreaAsync("areaWithSection", optionsDefaultSection)
</div>
```

If you do not specify the default section for an area, it uses the default section configured for all Page Builder instances (either the system’s _Default_ section with a single widget zone, or a custom section assigned when enabling Page Builder).
### Load Page Builder scripts and styles
All pages with editable areas must contain scripts and CSS styles required by Page Builder and its components. To add these resources to pages, call the following extension methods (or their [Tag Helper alternatives](/documentation/developers-and-admins/development/reference-tag-helpers)) in your views:
  * `Html.Kentico().PageBuilderScripts` – renders required script tags and script file links. Call before the closing `</body>` tag of the page code.
  * `Html.Kentico().PageBuilderStyles` – renders links for the required stylesheet files. Call within the `<head>` tag of the page code.
cshtml
Copy
```
@using Kentico.Web.Mvc
@using Kentico.PageBuilder.Web.Mvc

<html>
<head>
    ...
    @Html.Kentico().PageBuilderStyles()
</head>
<body>
    ...
    @Html.Kentico().PageBuilderScripts()
</body>
</html>
```

For views with an assigned **layout** , we recommend using Razor **sections**. Sections allow you to call the methods in the appropriate part of the overall page code, but only for pages that contain editable areas.


The helpers include scripts and styles used in the system’s editing interface, as well as those of individual Page Builder components ([widgets](/documentation/developers-and-admins/development/builders/page-builder/widgets-for-page-builder), [sections](/documentation/developers-and-admins/development/builders/page-builder/sections-for-page-builder), [inline property editors](/documentation/developers-and-admins/development/builders/page-builder/widgets-for-page-builder/inline-editors-for-widget-properties)). See the linked documentation to learn how to correctly add custom scripts and styles for your own components.
After adding editable areas to views and ensuring that the pages contain the required scripts and styles, you can see Page Builder with the editable areas in the website channel applications.
### Configure allowed components for editable areas
You can define widget restrictions for certain areas. To limit which components (widgets or sections) can be added to a specific area:
  1. Create an `EditableAreaOptions` object.
  2. Assign a list of strings to the object’s `AllowedWidgets` and/or `AllowedSections` property. 
     * The strings must contain the identifiers of components you want to allow in the area (see [Widgets for Page Builder](/documentation/developers-and-admins/development/builders/page-builder/widgets-for-page-builder) and [Sections for Page Builder](/documentation/developers-and-admins/development/builders/page-builder/sections-for-page-builder) to learn more about component identifiers). For identifiers of the default system widgets, check [the system widget reference](/documentation/developers-and-admins/development/builders/page-builder/reference-default-page-builder-widgets).
  3. Assign the `EditableAreaOptions` object as a parameter of the `EditableAreaAsync` extension method used to render the given area.


The allowed components parameters work as an allowlist – you list the components that you want to allow in the editable area, and all others are automatically excluded. When allowed components are not specified, all registered components can be added to the area. You can also set the properties to the following special values:
  * `AllowedComponents.ALL` – all components are allowed within the editable area.
  * `AllowedComponents.NONE` – no components are allowed within the editable area (equivalent to an empty list of identifiers). 
    * Use the `NONE` option when limiting sections – this “locks in” the default section for the given area and completely prevents editors from adding further sections of any type.


**Tip** : Prepare the identifiers of allowed components as a string array, and then assign the array to the appropriate property of the `EditableAreaOptions` object.
cshtml
**Example**
Copy
```
<div>
    @{
        var optionsLimited = new EditableAreaOptions
        {
            AllowedWidgets = new[] { "LearningKit.Widgets.NumberWidget",
                                     "LearningKit.Widgets.SomeOtherWidget" },
            AllowedSections = AllowedComponents.ALL
        };
    }
    @await Html.Kentico().EditableAreaAsync("limitedArea", optionsLimited)
</div>
```

When adding a new widget or section to the limited editable areas, editors can now only choose from the set of allowed components. When dragging widgets or sections between areas, the interface visually distinguishes between areas where you can and cannot drop the components.
The component limiting feature is intended as a way to set guidelines for content editors, not as a security measure. When you limit an area, existing instances of widgets and sections placed in the area are not validated or removed.
**Widget zone restrictions**
Editable area restrictions of widgets are combined with any widget restrictions set for individual [widget zones](/documentation/developers-and-admins/development/builders/page-builder/sections-for-page-builder). Both sets of restrictions apply to create the final list of allowed widgets for the zone.
## Retrieve the Page Builder configuration for the current page
You can access the Page Builder configuration of the current page via the `IPageBuilderDataContextRetriever` service and its `Retrieve` method. The method returns an `IPageBuilderDataContext` object that contains the configuration in its `Configuration` property.
The configuration is stored in the following nested structure:
  * Page – The root of the configuration 
    * EditableAreas – contains a list of all editable areas on the page 
      * Sections – contains section properties and all widget zones held within 
        * Zones – contains the identifier of the zone and a list of all widgets held within 
          * Widgets – contains widget metadata (the widget’s identifier, type, [personalization variants](/documentation/business-users/digital-marketing/widget-personalization), etc.)
  * PageTemplate – contains page template metadata (the template’s identifier and properties)


You can use the configuration if you wish to develop components that react to the presence of other components on the page.
### Establish rendering context
To check where and in what mode the currently processed component is being rendered, use `IPageBuilderDataContext` to retrieve the Page Builder mode using the `GetMode` method.
The method returns a value from the `PageBuilderMode` enumeration. Possible states:
  * `Off` – Page Builder is not used on the page.
  * `Edit` – Page Builder is initialized in the editing mode.
  * `ReadOnly` – Page Builder is initialized in the [read-only mode](/documentation/changelog#pbreadonly-29-4-0).


C#
Copy
```
using Kentico.PageBuilder.Web.Mvc;

// Stores an instance of the IPageBuilderDataContextRetriever service (e.g., obtained using dependency injection)
private readonly IPageBuilderDataContextRetriever pageBuilderDataContext;

// Execute the following block only in edit mode (when editing pages in website channels)
if (pageBuilderDataContext.Retrieve().GetMode() == PageBuilderMode.Edit)
{
    // Targeted custom logic
}
```

![]()
[]()[]()
