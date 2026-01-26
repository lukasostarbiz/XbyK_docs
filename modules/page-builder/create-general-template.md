---
source: https://docs.kentico.com/modules/page-builder/create-general-template
scrape_date: 2026-01-26
---

Module: Page Builder
10 of 16 Pages
# Create a generic page template
Looking over the page template we’ve just created, you may find it a bit of a waste to have all these options for Page Builder layouts and appearance available only for product pages.
Since most of what we’ve made is reusable, it will be straightforward to create a more general-purpose page template that has these Page Builder features, and can be used on any page type.
  1. Create a new _~/Features/Shared/Templates_ folder.
  2. Add a new page template properties file called _GeneralPageTemplateProperties.cs_.
  3. Include the same properties as _ProductPagePageTemplateProperties.cs_ , except for the checkbox. This template will always use Page Builder areas. 
C#
**GeneralPageTemplateProperties.cs**
Copy
```
using Kentico.PageBuilder.Web.Mvc.PageTemplates;
using Kentico.Xperience.Admin.Base.FormAnnotations;
using TrainingGuides.Web.Features.Shared.OptionProviders;
using TrainingGuides.Web.Features.Shared.OptionProviders.ColumnLayout;
using TrainingGuides.Web.Features.Shared.OptionProviders.CornerStyle;
using TrainingGuides.Web.Features.Shared.OptionProviders.ColorScheme;

namespace TrainingGuides.Web.Features.Shared;

public class GeneralPageTemplateProperties : IPageTemplateProperties
{
    [DropDownComponent(
        Label = "Color scheme",
        ExplanationText = "Select the color scheme of the template.",
        DataProviderType = typeof(DropdownEnumOptionProvider<ColorSchemeOption>),
        Order = 10)]
    public string ColorScheme { get; set; } = nameof(ColorSchemeOption.TransparentDark);

    [DropDownComponent(
        Label = "Corner type",
        ExplanationText = "Select the corner type of the template.",
        DataProviderType = typeof(DropdownEnumOptionProvider<CornerStyleOption>),
        Order = 20)]
    public string CornerStyle { get; set; } = nameof(CornerStyleOption.Round);

    [DropDownComponent(
        Label = "Column layout",
        ExplanationText = "Select the layout of the editable areas in the template.",
        DataProviderType = typeof(DropdownEnumOptionProvider<ColumnLayoutOption>),
        Order = 30)]
    public string ColumnLayout { get; set; } = nameof(ColumnLayoutOption.OneColumn);
}
```

  4. Add a view called _GeneralPageTemplate.cshtml_ , copying the parts from _ProductPagePageTemplate.cshtml_ that are not specifically related to products. 
cshtml
**GeneralPageTemplate.cshtml**
Copy
```
@using Microsoft.AspNetCore.Html
@using TrainingGuides.Web.Features.Shared
@using TrainingGuides.Web.Features.Shared.OptionProviders.ColumnLayout
@using TrainingGuides.Web.Features.Shared.ViewComponents

@model TemplateViewModel<GeneralPageTemplateProperties>

@{
        if (!Enum.TryParse(Model.Properties.ColumnLayout, out ColumnLayoutOption columnLayout))
        {
            columnLayout = ColumnLayoutOption.OneColumn;
        }
}
<tg-component-style color-scheme="@Model.Properties.ColorScheme" corner-style="@Model.Properties.CornerStyle">
    <vc:page-builder-columns column-layout-option="@columnLayout" editable-area-options="@new EditableAreaOptions()" />
</tg-component-style>
```

  5. Register the template in a file called _GeneralPageTemplate.cs_.
  6. Do not include the `ContentTypeNames` property. 
Omitting `ContentTypeNames` allows the template to be used for any page content type. 
C#
**GeneralPageTemplate.cs**
Copy
```
using Kentico.PageBuilder.Web.Mvc.PageTemplates;
using TrainingGuides.Web.Features.Shared;

[assembly: RegisterPageTemplate(
    identifier: GeneralPageTemplate.IDENTIFIER,
    name: "General page template",
    propertiesType: typeof(GeneralPageTemplateProperties),
    customViewName: "~/Features/Shared/Templates/GeneralPageTemplate.cshtml",
    IconClass = "xp-square")]

namespace TrainingGuides.Web.Features.Shared;
public static class GeneralPageTemplate
{
    public const string IDENTIFIER = "TrainingGuides.GeneralPageTemplate";
}
```



Now, if you sign in to the administration interface, you should have a general-use page template with properties to determine its color, corner styling, and editable area layout.
Your browser does not support the video tag. 
## Wrapping up
If you need a refresher, feel free to take another look at the [beginning of this series](/modules/page-builder/meet-requirements-with-page-builder) to review the mockups of the page functionality we’ve been working to implement so far.
**Apply your knowledge**
If you’d like to put your knowledge to the test, try swapping out the `UsePageBuilder` checkbox in `ProductPagePageTemplateProperties` for another enumeration-based dropdown.
Create a third option which keeps the structured product data, while rendering editable areas at the top and bottom of the page.
[ Previous page ](/modules/page-builder/expand-page-builder-functionality)
10 of 16
[ Mark complete and continue ](/modules/page-builder/implement-flexible-sections)
![]()
[]()[]()
