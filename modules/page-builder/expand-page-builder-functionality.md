---
source: https://docs.kentico.com/modules/page-builder/expand-page-builder-functionality
scrape_date: 2026-01-26
---

Module: Page Builder
9 of 16 Pages
# Expand the template's Page Builder functionality
An editable area is fairly flexible on its own, especially if you have multiple options for sections and lots of widgets, but let’s try to make it even more versatile.
We’ll create a view component that uses template properties to determine the number of columns the template has, and how they are laid out.
### Expand the template properties
  1. Create a new folder, _~/Features/Shared/OptionProviders/ColumnLayout_.
  2. Add an enumeration called `ColumnLayoutOption`, with options for different layouts with one, two, and three columns. 
C#
**ColumnLayoutOption.cs**
Copy
```
using System.ComponentModel;

namespace TrainingGuides.Web.Features.Shared.OptionProviders.ColumnLayout;

public enum ColumnLayoutOption
{
    [Description("One column")]
    OneColumn = 0,
    [Description("Two columns")]
    TwoColumnEven = 1,
    [Description("Two columns - Lg/Sm")]
    TwoColumnLgSm = 2,
    [Description("Two columns - Sm/Lg")]
    TwoColumnSmLg = 3,
    [Description("Three columns")]
    ThreeColumnEven = 4,
    [Description("Three columns - Sm/Lg/Sm")]
    ThreeColumnSmLgSm = 5,
}
```

  3. Return to the _~/Features/Products/ProductPagePageTemplateProperties_ file and add a new string property to represent the column layout. 
    1. Set the property to use a dropdown component, supplied by our new enumeration.
    2. Add a [visibility condition](/documentation/developers-and-admins/customization/extend-the-administration-interface/ui-form-components/ui-form-component-visibility-conditions) to ensure that it only appears if the `UsePageBuilder` property’s checkbox is true (enabled). 
C#
**ProductPagePageTemplateProperties.cs**
Copy
```
...
[DropDownComponent(
    Label = "Column layout",
    ExplanationText = "Select the layout of the editable areas in the template.",
    DataProviderType = typeof(DropdownEnumOptionProvider<ColumnLayoutOption>),
    Order = 40)]
[VisibleIfTrue(nameof(UsePageBuilder))]
public string ColumnLayout { get; set; } = nameof(ColumnLayoutOption.OneColumn);
...
```



Now, if you sign in to the system, you should see the new checkbox appear in the template properties of any page using this template, but only if the checkbox property to use Page Builder is enabled. At this point, the new property won’t do anything, so let’s change that.
Your browser does not support the video tag. 
### Add supporting entities for the view component
  1. If the _~/Features/Shared_ folder does not contain a _ViewComponents_ folder, create it.
  2. Add a new class called `PageBuilderColumnViewModel`, with properties for the CSS class of the column, its identifier, and an `EditableAreaOptions` object. 
C#
**PageBuilderColumnViewModel.cs**
Copy
```
using Kentico.PageBuilder.Web.Mvc;

namespace TrainingGuides.Web.Features.Shared.ViewComponents;

public class PageBuilderColumnViewModel
{
    public string CssClass { get; set; } = string.Empty;
    public string Identifier { get; set; } = string.Empty;
    public EditableAreaOptions? EditableAreaOptions { get; set; }
}
```

  3. Add a new ViewComponent called `PageBuilderColumnsViewComponent`.
  4. Add a `PageBuilderColumnsViewModel` class, containing a collection of `PageBuilderColumnViewModel` objects. 
C#
**PageBuilderColumnsViewModel.cs**
Copy
```
namespace TrainingGuides.Web.Features.Shared.ViewComponents;

public class PageBuilderColumnsViewModel
{
    public IEnumerable<PageBuilderColumnViewModel> PageBuilderColumns = [];
}
```



### Implement the view component logic
Now it’s time to implement the view component. Let’s give the template a limit of three columns. It’s rare to see web pages with more than three columns, and if editors need more columns than that, they can use multi-column Page Builder sections within the template columns.
  1. Open _PageBuilderColumnsViewComponent.cs_ and add constants to the view component, representing the identifiers used when rendering _editable areas_ , along with bootstrap classes for managing the sizes of the columns.
Bootstrap is already included in the [Training guides repository](https://github.com/Kentico/xperience-by-kentico-training-guides). 
C#
**PageBuilderColumnsViewComponent.cs**
Copy
```
...
private const string AREA_MAIN = "areaMain";
private const string AREA_SECONDARY = "areaSecondary";
private const string AREA_TERTIARY = "areaTertiary";

private const string COL_XS = "col-md-3";
private const string COL_S = "col-md-4";
private const string COL_M = "col-md-6";
private const string COL_L = "col-md-8";
private const string COL = "col-md";
...
```

  2. Use properties to access the identifier constants.
This will allow for more complicated logic in the future. [Later on in the series](/modules/page-builder/implement-flexible-sections), we will modify this view component to work with [widget zones](/documentation/developers-and-admins/development/builders/page-builder#widget-zones) for [Page Builder sections](/documentation/developers-and-admins/development/builders/page-builder/sections-for-page-builder). 
C#
**PageBuilderColumnsViewComponent.cs**
Copy
```
...

private string MainIdentifier
{
    get => AREA_MAIN;
    set { }
}

private string SecondaryIdentifier
{
    get => AREA_SECONDARY;
    set { }
}

private string TertiaryIdentifier
{
    get => AREA_TERTIARY;
    set { }
}
...
```

  3. Add a method that takes a `ColumnLayoutOption` parameter and uses it to determine the number of columns associated with the option.
C#
**PageBuilderColumnsViewComponent.cs**
Copy
```
...
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
...
```

  4. Define a method that returns a `PageBuilderColumnViewModel` based on the index of the column, the selected `ColumnLayoutOption`, and an `EditableAreaOptions` object.
    1. Assign bootstrap column classes according to the sizes dictated in the option’s name.
    2. For layout options where the columns are not the same width, apply the main identifier if it is the largest one. 
C#
**PageBuilderColumnsViewComponent.cs**
Copy
```
...
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
...
```

  5. Implement the `Invoke` method with `ColumnLayoutOption` and `EditableAreaOptions` parameters.
    1. Use the `GetNumberOfColumns` method to construct a `for` loop. In the loop, assemble a list of `PageBuilderColumn` view models using the `GetColumn` method and the current index within the loop.
    2. Use the list to create a `PageBuilderColumnsViewModel` and pass it to a view at the path _~/Features/Shared/ViewComponents/PageBuilderColumns.cshtml_. 
The `PageBuilderColumns` view doesn’t exist yet, but don’t worry. We’ll add it in the next section. 
C#
**PageBuilderColumnsViewComponent.cs**
Copy
```
...
public IViewComponentResult Invoke(ColumnLayoutOption columnLayoutOption,
    EditableAreaOptions editableAreaOptions)
{
    int numberOfColumns = GetNumberOfColumns(columnLayoutOption);
    var columns = new List<PageBuilderColumnViewModel>();
    for (int index = 0; index < numberOfColumns; index++)
    {
        columns.Add(GetColumn(index, columnLayoutOption, editableAreaOptions));
    }

    var model = new PageBuilderColumnsViewModel
    {
        PageBuilderColumns = columns,
    };

    return View("~/Features/Shared/ViewComponents/PageBuilderColumns.cshtml", model);
}
...
```


The ViewComponent should look like this: 
C#
**PageBuilderColumnsViewComponent.cs**
Copy
```
using Kentico.PageBuilder.Web.Mvc;
using Microsoft.AspNetCore.Mvc;
using TrainingGuides.Web.Features.Shared.OptionProviders.ColumnLayout;

namespace TrainingGuides.Web.Features.Shared.ViewComponents;

public class PageBuilderColumnsViewComponent : ViewComponent
{
    private const string AREA_MAIN = "areaMain";
    private const string AREA_SECONDARY = "areaSecondary";
    private const string AREA_TERTIARY = "areaTertiary";

    private const string COL_XS = "col-md-3";
    private const string COL_S = "col-md-4";
    private const string COL_M = "col-md-6";
    private const string COL_L = "col-md-8";
    private const string COL = "col-md";

    private string MainIdentifier
    {
        get => AREA_MAIN;
        set { }
    }

    private string SecondaryIdentifier
    {
        get => AREA_SECONDARY;
        set { }
    }

    private string TertiaryIdentifier
    {
        get => AREA_TERTIARY;
        set { }
    }

    public IViewComponentResult Invoke(ColumnLayoutOption columnLayoutOption,
        EditableAreaOptions editableAreaOptions)
    {
        int numberOfColumns = GetNumberOfColumns(columnLayoutOption);
        var columns = new List<PageBuilderColumnViewModel>();
        for (int index = 0; index < numberOfColumns; index++)
        {
            columns.Add(GetColumn(index, columnLayoutOption, editableAreaOptions));
        }

        var model = new PageBuilderColumnsViewModel
        {
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
```

### Create the component view
  1. Add a new file named _PageBuilderColumns.cshtml_ to the _~/Features/Shared/ViewComponents_ folder.
  2. Set the `model` to `PageBuilderColumnsViewModel`, and nest `div` elements with the `container` and `row` classes. 
cshtml
**PageBuilderColumns.cshtml**
Copy
```
@using TrainingGuides.Web.Features.Shared.ViewComponents

@model PageBuilderColumnsViewModel

<div class="container tg-pb-col">
    <div class="row">

    </div>
</div>
```

  3. Use a `foreach` loop to iterate through the model’s `PageBuilderColumns` collection.
  4. Within the loop, add a `div` styled with the column’s `cssClass` property. 
cshtml
**PageBuilderColumns.cshtml**
Copy
```
...
@foreach (var column in Model.PageBuilderColumns)
{
    <div class="@column.CssClass">

    </div>
}
...
```

  5. Render an _editable area_ with the `editable-area` tag helper from Xperience. 
    1. Set the `area-options` attribute to the column’s `EditableAreaOptions` property. 
cshtml
**PageBuilderColumns.cshtml**
Copy
```
...
    <editable-area area-identifier="@column.Identifier" area-options="column.EditableAreaOptions" />
...
```



Now, the view should look like this:
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
                    <editable-area area-identifier="@column.Identifier" area-options="column.EditableAreaOptions" />
            </div>
        }
    </div>
</div>
```

### Use the view component in the template
Now the view component is finished. Let’s add it to the template view so we can test our progress.
  1. Return to the _~/Features/Shared/Products/ProductPagePageTemplate.cshtml_ file.
  2. Remove the `editable-area` tag from the view, and replace it with the new view component. 
cshtml
**ProductPagePageTemplate.cshtml**
Copy
```
...
@if(Model.Properties.UsePageBuilder)
{
    <vc:page-builder-columns column-layout-option="@columnLayout" editable-area-options="@new EditableAreaOptions()" />
}
...
```

  3. In the razor code block that sets the `templateModel` variable, default the column layout to one column if the model’s `ColumnLayout` property can’t be parsed. 
cshtml
**ProductPagePageTemplate.cshtml**
Copy
```
@{
    var templateModel = Model.GetTemplateModel<ProductPageViewModel>();
    if (!Enum.TryParse(Model.Properties.ColumnLayout, out ColumnLayoutOption columnLayout))
    {
        columnLayout = ColumnLayoutOption.OneColumn;
    }
}
```


In the end, your view should look like this: 
cshtml
**ProductPagePageTemplate.cshtml**
Copy
```
@using Microsoft.AspNetCore.Html
@using System.Globalization
@using TrainingGuides.Web.Features.Products
@using TrainingGuides.Web.Features.Products.Models
@using TrainingGuides.Web.Features.Shared.OptionProviders.ColumnLayout
@using TrainingGuides.Web.Features.Shared.OptionProviders.ColorScheme
@using TrainingGuides.Web.Features.Shared.ViewComponents

@model TemplateViewModel<ProductPagePageTemplateProperties>

@{
    var templateModel = Model.GetTemplateModel<ProductPageViewModel>();
    if (!Enum.TryParse(Model.Properties.ColumnLayout, out ColumnLayoutOption columnLayout))
    {
        columnLayout = ColumnLayoutOption.OneColumn;
    }
}
<tg-component-style color-scheme="@Model.Properties.ColorScheme" corner-style="@Model.Properties.CornerStyle">
    @if(Model.Properties.UsePageBuilder)
    {
        <vc:page-builder-columns column-layout-option="@columnLayout" editable-area-options="@new EditableAreaOptions()" />
    }
    else
    {
        <div class="tg-padding-big">
            <div class="row">
                <div class="col align-self-center">
                    <h3>@templateModel.NameHtml</h3>
                    <p>@templateModel.ShortDescriptionHtml</p>
                </div>

                <tg-styled-image src="@templateModel.Media.FirstOrDefault()?.FilePath" alt="@templateModel.Media.FirstOrDefault()?.Description" corner-style="@Model.Properties.CornerStyle" class="col c-product-img object-fit-cover" />
            </div>
            <div class="row">
                <div class="c-pricelist">
                    <div class="col">
                        <tg-component-style color-scheme="@ColorSchemeOption.Light1" corner-style="@Model.Properties.CornerStyle"  class="c-table">
                            @foreach (var feature in templateModel.Features)
                            {
                                <div class="c-table_row">
                                    <div class="c-table_cell"><div class="c-table_cell">@feature.LabelHtml</div></div>
                                    <div class="c-table_cell text-end">
                                        <tg-product-feature-value feature="@feature"/>
                                    </div>
                                </div>
                            }
                        </tg-component-style>
                    </div>
                </div>
            </div>
        </div>
    }
</tg-component-style>
```

Your browser does not support the video tag. 
[ Previous page ](/modules/page-builder/enable-widgets-in-the-template)
9 of 16
[ Mark complete and continue ](/modules/page-builder/create-general-template)
![]()
[]()[]()
