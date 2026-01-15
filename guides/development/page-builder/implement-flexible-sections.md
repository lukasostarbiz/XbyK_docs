# Implement flexible sections
  * How-to| [ Copy page link ](guides/development/page-builder/implement-flexible-sections#) | [Get HelpService ID](guides/development/page-builder/implement-flexible-sections#) | This page is part of a module: [ Page Builder ](modules/page-builder)
Core MVC 5


[✖](guides/development/page-builder/implement-flexible-sections# "Close page link panel") [Copy to clipboard](guides/development/page-builder/implement-flexible-sections#)
[Page Builder sections](documentation/developers-and-admins/development/builders/page-builder/sections-for-page-builder) are the primary way for your editors to change the layout and appearance of a page with a set template configuration. They control where widgets can be added.
Let’s go over the steps of creating a flexible Page Builder section.
The code samples here are for a section that allows editors to change its color scheme, corner style, and number of columns. If you use these samples specifically, you’ll be able to replicate the functionality in the following screenshot:
[![screenshot of Page Builder section demonstrating multiple layouts](docsassets/guides/implement-flexible-sections/PageBuilderSectionSample.png)](https://docs.kentico.com/docsassets/guides/implement-flexible-sections/PageBuilderSectionSample.png)
This image depicts four instances of the same section, each configured differently.
## Before you start
This guide requires the following:
  * Familiarity with [C#](https://learn.microsoft.com/en-us/dotnet/csharp/), [.NET Core](https://learn.microsoft.com/en-us/dotnet/), [Dependency injection](https://learn.microsoft.com/en-us/dotnet/core/extensions/dependency-injection), and the [MVC pattern](https://learn.microsoft.com/en-us/aspnet/core/mvc/overview).
  * A running instance of Xperience by Kentico, preferably [30.11.1](documentation/changelog) or higher. 
Some features covered in the training guides may not work in older versions. 
  * Familiarity with the basics of [Page Builder](documentation/developers-and-admins/development/builders/page-builder) in Xperience by Kentico.


If you’d like to follow along with this specific example, we recommend starting from [the first guide in the Page Builder series](guides/development/page-builder/meet-requirements-with-page-builder).
**Code samples**
You can find a project with completed, working versions of code samples from this guide and others in the [finished branch](https://github.com/Kentico/xperience-by-kentico-training-guides/tree/finished) of the _Training guides_ repository. The code samples in this guide revolve around creating a section called _General section_ to be located in the _~/Features/Shared/Sections/General_ folder.
The [main branch](https://github.com/Kentico/xperience-by-kentico-training-guides) of the repository provides a starting point to code along with the guides.
The code samples in this guide are for [.NET 8](https://learn.microsoft.com/en-us/dotnet/core/whats-new/dotnet-8/overview) only.
They come from a project that uses [implicit using directives](https://learn.microsoft.com/en-us/dotnet/core/project-sdk/overview#implicit-using-directives). You may need to add additional `using` directives to your code if your project does not use this feature.
## Define the section properties
Flexible Page Builder sections provide configurable options not only for the layout of [widget zones](documentation/developers-and-admins/development/builders/page-builder#widget-zones), but other aspects of their appearance as well.
  1. Create a file that inherits from `ISectionProperties`.
  2. Define string properties for any text data that will be rendered in the section’s output, such as attributes for HTML elements or simple text that you plan to display on the page.
  3. Use [dropdown components](documentation/developers-and-admins/customization/extend-the-administration-interface/ui-form-components/reference-admin-ui-form-components#dropdown-selector) on string properties for lists of options relating to the appearance and structure of the section. 
Use a custom `IDropDownOptionsProvider` implementation to feed your dropdowns with more manageable or dynamically-supplied options.
You can find more information here: [Add a custom dropdown provider for administration components](guides/development/page-builder/map-enum-to-dropdown)
If you need to display complicated data or take user input from the live site, use a widget and its properties rather than a section. 


C#
**GeneralSectionProperties.cs**
Copy
```
using Kentico.PageBuilder.Web.Mvc;
using Kentico.Xperience.Admin.Base.FormAnnotations;
using TrainingGuides.Web.Features.Shared.OptionProviders;
using TrainingGuides.Web.Features.Shared.OptionProviders.ColumnLayout;
using TrainingGuides.Web.Features.Shared.OptionProviders.CornerStyle;
using TrainingGuides.Web.Features.Shared.OptionProviders.ColorScheme;

namespace TrainingGuides.Web.Features.Shared.Sections.General;
public class GeneralSectionProperties : ISectionProperties
{
    [TextInputComponent(
        Label = "Section anchor",
        Order = 10)]
    public string SectionAnchor { get; set; } = string.Empty;

    [DropDownComponent(
        Label = "Color scheme",
        ExplanationText = "Select the color scheme of the section.",
        DataProviderType = typeof(DropdownEnumOptionProvider<ColorSchemeOption>),
        Order = 20)]
    public string ColorScheme { get; set; } = nameof(ColorSchemeOption.TransparentDark);

    [DropDownComponent(
        Label = "Corner type",
        ExplanationText = "Select the corner type of the section.",
        DataProviderType = typeof(DropdownEnumOptionProvider<CornerStyleOption>),
        Order = 30)]
    public string CornerStyle { get; set; } = nameof(CornerStyleOption.Round);

    [DropDownComponent(
        Label = "Column layout",
        ExplanationText = "Select the layout of the widget zones in the section.",
        DataProviderType = typeof(DropdownEnumOptionProvider<ColumnLayoutOption>),
        Order = 40)]
    public string ColumnLayout { get; set; } = nameof(ColumnLayoutOption.OneColumn);
}
```

If you’ve happened upon this part of the series, or you need review, you can see steps outlining the enumerations above in our previous guides about page templates:
  * [_ColorSchemeOption_ and _CornerStyleOption_](guides/development/page-builder/create-versatile-templates-part-1#define-page-template-properties)
  * [_ColumnLayoutOption_](guides/development/page-builder/create-versatile-templates-part-2#expand-the-template-properties)


You can also find working examples in the [finished branch of the Training guides repository](https://github.com/Kentico/xperience-by-kentico-training-guides/tree/finished).
If you’re using a view component-based section, you can add a view model class that contains the data from the properties, along with any other data we want to include in the view.
C#
**GeneralSectionViewModel.cs**
Copy
```
using TrainingGuides.Web.Features.Shared.OptionProviders.ColumnLayout;

namespace TrainingGuides.Web.Features.Shared.Sections.General;

public class GeneralSectionViewModel
{
    public string SectionAnchor { get; set; } = string.Empty;
    public string ColorScheme { get; set; } = string.Empty;
    public string CornerStyle { get; set; } = string.Empty;
    public ColumnLayoutOption ColumnLayout { get; set; }
}
```

## Create a section view
### Build helpful components
Consider the functionality you want to achieve with your section, and whether tools like tag helpers and view components could help achieve your goal.
If you’re following along with the examples in this series, start by expanding [the view component from earlier in the series](guides/development/page-builder/create-versatile-templates-part-2#add-supporting-entities-for-the-view-component). Change it so that it works with both [editable areas](documentation/developers-and-admins/development/builders/page-builder/create-pages-with-editable-areas) and [widget zones](documentation/developers-and-admins/development/builders/page-builder#widget-zones).
  1. Add an enumeration to represent the two types of areas that the component should render.
C#
**PageBuilderColumnsViewComponent.cs**
Copy
```
...
public class PageBuilderColumnsViewComponent : ViewComponent
{
    ...
}
...
public enum PageBuilderAreaType
{
    EditableAreas = 0,
    WidgetZones = 1,
}
```

  2. Add a property of the enum type to the view model, and use it to determine which tag helper to render in the view.
C#
**PageBuilderColumnsViewModel.cs**
Copy
```
namespace TrainingGuides.Web.Features.Shared.ViewComponents;

public class PageBuilderColumnsViewModel
{
    // New property PageBuilderAreaType
    public PageBuilderAreaType PageBuilderAreaType { get; set; }
    public IEnumerable<PageBuilderColumnViewModel> PageBuilderColumns = [];
}
```

cshtml
**PageBuilderColumns.cshtml**
Copy
```
@using TrainingGuides.Web.Features.Shared.ViewComponents

@model PageBuilderColumnsViewModel
<div class="container tg-pb-col">
    <div class="row">
        @foreach (var column in Model.PageBuilderColumns)
        {
            <div class="@column.CssClass">
                // New conditional logic for rendering editable areas or widget zones
                @if(Model.PageBuilderAreaType == PageBuilderAreaType.EditableAreas)
                {
                    // Existing tag that uses the entire EditableAreaOptions object
                    <editable-area area-identifier="@column.Identifier" area-options="column.EditableAreaOptions" />
                }
                else
                {
                    // New tag that uses the AllowedWidgetProperties of the EditableAreaOptions object
                    <widget-zone zone-name="@column.Identifier" allowed-widgets="column.EditableAreaOptions?.AllowedWidgets" />
                }
            </div>
        }
    </div>
</div>
```

  3. Adjust the view component to account for area type when constructing the view model and deciding identifiers for the areas.


C#
**PageBuilderColumnsViewComponent.cs**
Copy
```
...
public class PageBuilderColumnsViewComponent : ViewComponent
{
    private const string AREA_MAIN = "areaMain";
    private const string AREA_SECONDARY = "areaSecondary";
    private const string AREA_TERTIARY = "areaTertiary";

    // New widget zone constants
    private const string ZONE_MAIN = "zoneMain";
    private const string ZONE_SECONDARY = "zoneSecondary";
    private const string ZONE_TERTIARY = "zoneTertiary";

    private const string COL_XS = "col-md-3";
    private const string COL_S = "col-md-4";
    private const string COL_M = "col-md-6";
    private const string COL_L = "col-md-8";
    private const string COL = "col-md";

    private PageBuilderAreaType pageBuilderAreaType;

    // New identifier logic that returns a zone or area identifier depending on the selected area type
    private string MainIdentifier
    {
        get => pageBuilderAreaType == PageBuilderAreaType.EditableAreas ? AREA_MAIN : ZONE_MAIN;
        set { }
    }

    private string SecondaryIdentifier
    {
        get => pageBuilderAreaType == PageBuilderAreaType.EditableAreas ? AREA_SECONDARY : ZONE_SECONDARY;
        set { }
    }

    private string TertiaryIdentifier
    {
        get => pageBuilderAreaType == PageBuilderAreaType.EditableAreas ? AREA_TERTIARY : ZONE_TERTIARY;
        set { }
    }

    public IViewComponentResult Invoke(ColumnLayoutOption columnLayoutOption,
        PageBuilderAreaType pageBuilderAreaType,
        EditableAreaOptions editableAreaOptions)
    {
        // New code that sets the area type variable for the private identifier properties.
        this.pageBuilderAreaType = pageBuilderAreaType;

        int numberOfColumns = GetNumberOfColumns(columnLayoutOption);
        var columns = new List<PageBuilderColumnViewModel>();
        for (int index = 0; index < numberOfColumns; index++)
        {
            columns.Add(GetColumn(index, columnLayoutOption, editableAreaOptions));
        }

        var model = new PageBuilderColumnsViewModel
        {
            PageBuilderAreaType = pageBuilderAreaType,
            PageBuilderColumns = columns,
        };

        return View("~/Features/Shared/ViewComponents/PageBuilderColumns.cshtml", model);
    }

    private int GetNumberOfColumns(ColumnLayoutOption columnLayoutOption) => columnLayoutOption switch
    {

        ColumnLayoutOption.TwoColumnEven or
        ColumnLayoutOption.TwoColumnLgSm or
        ColumnLayoutOption.TwoColumnSmLg
        => 2,
        ColumnLayoutOption.ThreeColumnEven or
        ColumnLayoutOption.ThreeColumnSmLgSm
        => 3,
        ColumnLayoutOption.OneColumn or
        _
        => 1
    };

    private PageBuilderColumnViewModel GetColumn(int columnIndex, ColumnLayoutOption columnLayoutOption, EditableAreaOptions editableAreaOptions)
    {
        string cssClass = string.Empty;
        string columnIdentifier;

        switch (columnLayoutOption)
        {
            case ColumnLayoutOption.TwoColumnEven:
                //first column is main
                cssClass = COL_M;
                columnIdentifier = columnIndex == 0 ? MainIdentifier : SecondaryIdentifier;
                break;
            case ColumnLayoutOption.TwoColumnLgSm:
                //first column is main
                if (columnIndex == 0)
                {
                    cssClass += COL_L;
                    columnIdentifier = MainIdentifier;
                }
                else
                {
                    cssClass += COL_S;
                    columnIdentifier = SecondaryIdentifier;
                }
                break;
            case ColumnLayoutOption.TwoColumnSmLg:
                //second column is main
                if (columnIndex == 0)
                {
                    cssClass += COL_S;
                    columnIdentifier = SecondaryIdentifier;
                }
                else
                {
                    cssClass += COL_L;
                    columnIdentifier = MainIdentifier;
                }
                break;
            case ColumnLayoutOption.ThreeColumnEven:
                //middle column is main
                cssClass += COL_S;
                columnIdentifier = columnIndex == 1
                    ? MainIdentifier
                    : columnIndex == 0 ?
                        SecondaryIdentifier : TertiaryIdentifier;
                break;
            case ColumnLayoutOption.ThreeColumnSmLgSm:
                //middle column is main
                if (columnIndex == 1)
                {
                    cssClass += COL_M;
                    columnIdentifier = MainIdentifier;
                }
                else
                {
                    cssClass += COL_XS;
                    columnIdentifier = columnIndex == 0 ?
                        SecondaryIdentifier : TertiaryIdentifier;
                }
                break;
            case ColumnLayoutOption.OneColumn:
            default:
                //sole column is main
                columnIdentifier = MainIdentifier;
                cssClass += COL;
                break;
        }

        return new PageBuilderColumnViewModel
        {
            CssClass = cssClass,
            Identifier = columnIdentifier,
            EditableAreaOptions = editableAreaOptions
        };
    }
}
...
```

### Implement the view
Next, let’s add a view that handles the display of the data from the previous step’s view model.
  1. Create a .cshtml file for the section.
  2. Ensure that any properties being rendered directly to the page are filled in.
  3. Use tag helpers and view components to extract complex display logic into modular, reusable components.
  4. Include [widget zones](documentation/developers-and-admins/development/builders/page-builder#widget-zones) in the section, so editors can add widgets. 
You can include widget zones in the output of a view component used in the view, or directly in the view itself.
If you have restrictions for which widgets should be allowed, configure the `allowed-widgets` of the widget zone accordingly.


cshtml
**GeneralSection.cshtml**
Copy
```
@using TrainingGuides.Web.Features.Shared.Sections.General
@using TrainingGuides.Web.Features.Shared.ViewComponents

@model GeneralSectionViewModel

@{
    var sectionAnchor = !string.IsNullOrWhiteSpace(Model.SectionAnchor) ? $"id={Model.SectionAnchor}" : "";
}

<section @sectionAnchor>
    <tg-component-style
        color-scheme="@Model.ColorScheme"
        corner-style="@Model.CornerStyle"
        class="c-section default">

        <vc:page-builder-columns
            column-layout-option="@Model.ColumnLayout"
            page-builder-area-type="@PageBuilderAreaType.WidgetZones"
            editable-area-options="@new EditableAreaOptions()"/>
    </tg-component-style>
</section>
```

Find instructions for building the tag helper from this sample here: [Component style tag helper and supporting service](guides/development/page-builder/create-versatile-templates-part-1#create-a-service-to-retrieve-styles)
## Implement and register the section.
Finally, you need to register the section, and if you’re creating a complex section, you may also need to create a view component.
  1. Create a class with an `IDENTIFIER` constant and use it to register the Page Builder section, via the `RegisterSection` assembly attribute. 
    1. If you’re creating a simple section, you can just create a new class named after the section.
    2. If you’re creating a view componnet-based section, the view component is a good candidate for where to locate this registration. 
      1. Perform any processing logic that you may need on the values from the properties, such as setting fallback values when parsing enum options fails.
      2. Populate a new view model object and pass it to the view form the previous step.
  2. If your project has a class that aggregates identifiers from components across the project, add the view component’s `IDENTIFIER` constant.


C#
**GeneralSectionViewComponent.cs**
Copy
```
using Kentico.PageBuilder.Web.Mvc;
using Microsoft.AspNetCore.Mvc;
using TrainingGuides.Web.Features.Shared.OptionProviders.ColumnLayout;
using TrainingGuides.Web.Features.Shared.Sections.General;

[assembly: RegisterSection(
    identifier: GeneralSectionViewComponent.IDENTIFIER,
    viewComponentType: typeof(GeneralSectionViewComponent),
    name: "General section",
    propertiesType: typeof(GeneralSectionProperties),
    Description = "Highly customizable general section.",
    IconClass = "icon-square")]

namespace TrainingGuides.Web.Features.Shared.Sections.General;

public class GeneralSectionViewComponent : ViewComponent
{
    public const string IDENTIFIER = "TrainingGuides.GeneralSection";

    public IViewComponentResult Invoke(ComponentViewModel<GeneralSectionProperties> sectionProperties)
    {
        var properties = sectionProperties.Properties;

        if (!Enum.TryParse(properties.ColumnLayout, out ColumnLayoutOption columnLayout))
        {
            columnLayout = ColumnLayoutOption.OneColumn;
        }

        var model = new GeneralSectionViewModel()
        {
            SectionAnchor = properties.SectionAnchor,
            ColumnLayout = columnLayout,
            ColorScheme = properties.ColorScheme,
            CornerStyle = properties.CornerStyle,
        };

        return View("~/Features/Shared/Sections/General/GeneralSection.cshtml", model);
    }
}
```

C#
**ComponentIdentifiers.cs**
Copy
```
...
@using TrainingGuides.Web.Features.Shared.Sections.General
...
public static class ComponentIdentifiers
{
    ...
    public static class Sections
    {
        ...
        public const string GENERAL = GeneralSectionViewComponent.IDENTIFIER;
        ...
    }
    ...
}
...
```

Reference the new section using it’s identifier. For example:
cshtml
**GeneralPageTemplate.cshtml**
Copy
```
...
<tg-component-style color-scheme="@Model.Properties.ColorScheme" corner-style="@Model.Properties.CornerStyle">
    <vc:page-builder-columns column-layout-option="@columnLayout" page-builder-area-type="@PageBuilderAreaType.EditableAreas" editable-area-options="@new EditableAreaOptions { DefaultSectionIdentifier = ComponentIdentifiers.Sections.GENERAL }" />
</tg-component-style>
...
```

## What’s next?
In the [next guide of this series](guides/development/page-builder/build-simple-cta-widget), we’ll walk though the process of creating a widget that displays a simple _call to action_ (CTA) button to demonstrate widget development concepts, before moving on to [a more advanced widget](guides/development/page-builder/define-advanced-widget) for displaying products in various configurations.