---
source: https://docs.kentico.com/modules/email-builder/build-reusable-columns-component
scrape_date: 2026-01-26
---

Module: Email Builder
11 of 13 Pages
# Build a reusable columns component
Now, let’s create a Razor component analogous to the [Page Builder columns view component](https://github.com/Kentico/xperience-by-kentico-training-guides/blob/finished/src/TrainingGuides.Web/Features/Shared/ViewComponents/PageBuilderColumnsViewComponent.cs) from the [page builder module](/modules/page-builder).
In this case, we’ll render only widget zones. Allowing the component to conditionally render both editable areas and widget zones, like we did in the Page Builder component, would cause problems with the hierarchy of [MJML components](https://documentation.mjml.io/#standard-body-components).
Alternately, you can modify the component to render editable areas instead. This will require you to take a different approach regarding which level of the Email Builder hierarchy you can use certain MJML elements (templates vs sections vs widgets).
### Define the models
Add a new folder, _~/Features/Shared/EmailBuilder/RazorComponents/_ , and create models to represent email layout columns.
Start with a model for the individual columns. Each widget zone will need an identifier, and the option to control which widgets are allowed.
Then, the `mj-column` component that wraps each widget zone will need a width and CSS classes.
C#
**EmailBuilderColumnModel.cs**
Copy
```
namespace TrainingGuides.Web.Features.Shared.EmailBuilder.RazorComponents;

public class EmailBuilderColumnModel
{
    public int Width { get; set; } = 0;
    public string Identifier { get; set; } = string.Empty;
    public IEnumerable<string> CssClasses { get; set; } = [];
    public IEnumerable<string> AllowedWidgets { get; set; } = [];
    // If you want to apply this approach to Editable Areas rather than Widget Zones, you can also include this:
    // public IEnumerable<string> AllowedSections { get; set; } = [];
}
```

Moving on to the collection of columns, for now let’s just include one property to hold multiple column models.
You can expand this with additional properties in the future, or simply eliminate this class and include the collection of columns directly in the Razor component, depending on your project’s conventions.
C#
**EmailBuilderColumnsModel.cs**
Copy
```
namespace TrainingGuides.Web.Features.Shared.EmailBuilder.RazorComponents;

public class EmailBuilderColumnsModel
{
    public IEnumerable<EmailBuilderColumnModel> Columns { get; set; } = [];
}
```

### Build the component
The component’s code-behind needs the following elements:
  1. Constants that it can use for its widget zone identifiers. 
     * We’ll follow the zone naming conventions from the [Email Builder Starter Kit](https://github.com/Kentico/xperience-by-kentico-email-builder-starter-kit).
  2. A private `EmailBuilderColumnsModel` property, to hold information about the columns it needs to render
  3. _(Optional)_ A dictionary of attributes and properties, to dynamically configure certain properties of its Widget zone
  4. Properties to configure the component’s corner style, column layout, column colors, and allowed widgets
  5. Methods to assemble the columns and populate their properties based on the selected options 
     * Let’s specify the width of each column as a percentage, leaving some extra space to compensate for padding.
  6. Calls to the `ComponentStyleEnumService` to retrieve CSS classes corresponding to each selected color scheme


C#
**EmailBuilderColumns.razor.cs**
Copy
```
using Microsoft.AspNetCore.Components;
using TrainingGuides.Web.Features.Shared.OptionProviders.ColorScheme;
using TrainingGuides.Web.Features.Shared.OptionProviders.ColumnLayout;
using TrainingGuides.Web.Features.Shared.OptionProviders.CornerStyle;
using TrainingGuides.Web.Features.Shared.Services;

namespace TrainingGuides.Web.Features.Shared.EmailBuilder.RazorComponents;

public partial class EmailBuilderColumns : ComponentBase
{
    // If you want to apply this approach to Editable Areas rather than Widget Zones, we recommend changing these constants with more accurate names.
    private const string ZONE_MAIN = "Widget_Zone_0";
    private const string ZONE_SECONDARY = "Widget_Zone_1";
    private const string ZONE_TERTIARY = "Widget_Zone_2";

    private EmailBuilderColumnsModel Model { get; set; } = new();

    private Dictionary<string, object> AreaAttributes { get; set; } = [];

    private int NumberOfColumns => ColumnLayout switch
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

    [Inject]
    private IComponentStyleEnumService ComponentStyleEnumService { get; set; } = default!;

    [Parameter]
    public ColumnLayoutOption ColumnLayout { get; set; } = ColumnLayoutOption.OneColumn;

    [Parameter]
    public CornerStyleOption CornerStyle { get; set; } = CornerStyleOption.Round;

    [Parameter]
    public ColorSchemeOption Column1ColorScheme { get; set; } = ColorSchemeOption.Light1;

    [Parameter]
    public ColorSchemeOption Column2ColorScheme { get; set; } = ColorSchemeOption.Light1;

    [Parameter]
    public ColorSchemeOption Column3ColorScheme { get; set; } = ColorSchemeOption.Light1;

    [Parameter]
    public IEnumerable<string> AllowedWidgets { get; set; } = [];

    // If you want to apply this approach to Editable Areas rather than Widget Zones, you can also include this:
    // [Parameter]
    // public IEnumerable<string> AllowedSections { get; set; } = [];

    protected override void OnInitialized()
    {
        if (AllowedWidgets.Where(str => !string.IsNullOrWhiteSpace(str)).Any())
            AreaAttributes.Add(nameof(AllowedWidgets), AllowedWidgets);
        // If you want to apply this approach to Editable Areas rather than Widget Zones, you can also include this:
        // if (AllowedSections.Where(str => !string.IsNullOrWhiteSpace(str)).Any())
        //     AreaAttributes.Add(nameof(AllowedSections), AllowedSections);

        Model = new EmailBuilderColumnsModel
        {
            Columns = GetColumns(),
        };
    }

    private IEnumerable<EmailBuilderColumnModel> GetColumns()
    {
        var columns = new List<EmailBuilderColumnModel>();

        int numberOfColumns = NumberOfColumns;
        for (int i = 0; i < numberOfColumns; i++)
        {
            columns.Add(GetColumn(i));
        }

        return columns;
    }

    private EmailBuilderColumnModel GetColumn(int columnIndex)
    {
        string columnIdentifier;
        int width;
        var cssClasses = GetColumnCssClasses(columnIndex).ToList();
        cssClasses.AddRange(ComponentStyleEnumService.GetCornerStyleClasses(CornerStyle));
        cssClasses.Add("tg-tiny-margin");

        switch (ColumnLayout)
        {
            case ColumnLayoutOption.TwoColumnEven:
                //first column is main
                width = 45;
                columnIdentifier = columnIndex == 0
                    ? ZONE_MAIN
                    : ZONE_SECONDARY;
                break;
            case ColumnLayoutOption.TwoColumnLgSm:
                //first column is main
                if (columnIndex == 0)
                {
                    width = 60;
                    columnIdentifier = ZONE_MAIN;
                }
                else
                {
                    width = 30;
                    columnIdentifier = ZONE_SECONDARY;
                }
                break;
            case ColumnLayoutOption.TwoColumnSmLg:
                //second column is main
                if (columnIndex == 0)
                {
                    width = 30;
                    columnIdentifier = ZONE_SECONDARY;
                }
                else
                {
                    width = 60;
                    columnIdentifier = ZONE_MAIN;
                }
                break;
            case ColumnLayoutOption.ThreeColumnEven:
                //middle column is main
                width = 30;
                columnIdentifier = columnIndex == 1
                    ? ZONE_MAIN
                    : columnIndex == 0
                        ? ZONE_SECONDARY
                        : ZONE_TERTIARY;
                break;
            case ColumnLayoutOption.ThreeColumnSmLgSm:
                if (columnIndex == 1)
                {
                    width = 40;
                    columnIdentifier = ZONE_MAIN;
                }
                else
                {
                    width = 25;
                    columnIdentifier = columnIndex == 0
                        ? ZONE_SECONDARY
                        : ZONE_TERTIARY;
                }
                break;
            case ColumnLayoutOption.OneColumn:
            default:
                //sole column is main
                width = 90;
                columnIdentifier = ZONE_MAIN;
                break;
        }

        return new EmailBuilderColumnModel
        {
            Width = width,
            CssClasses = cssClasses,
            Identifier = columnIdentifier,
            AllowedWidgets = AllowedWidgets,
            // If you want to apply this approach to Editable Areas rather than Widget Zones, you can also include this:
            // AllowedSections = AllowedSections
        };
    }

    private IEnumerable<string> GetColumnCssClasses(int columnIndex) => columnIndex switch
    {
        0 => ComponentStyleEnumService.GetColorSchemeClasses(Column1ColorScheme),
        1 => ComponentStyleEnumService.GetColorSchemeClasses(Column2ColorScheme),
        2 => ComponentStyleEnumService.GetColorSchemeClasses(Column3ColorScheme),
        _ => []
    };
}
```

Moving on to the component itself, it can be fairly simple thanks to the work we did in the code-behind.
Display the columns using a foreach loop.
**EmailBuilderColumns.razor**
cshtml
**EmailBuilderColumns.razor**
Copy
```
@using Kentico.VisualBuilderComponents.Rcl.Components

@foreach(var column in Model.Columns)
{
    <mj-column width="@($"{column.Width}%")" css-class="@(string.Join(" ", column.CssClasses))" padding="8px">
        <WidgetZone ZoneName="@(column.Identifier)" @attributes="AreaAttributes" />
    </mj-column>
}
```

[ Previous page ](/modules/email-builder/prepare-template-styling-options)
11 of 13
[ Mark complete and continue ](/modules/email-builder/complete-flexible-template)
![]()
[]()[]()
