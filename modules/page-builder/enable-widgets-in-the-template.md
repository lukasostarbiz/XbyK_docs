---
source: https://docs.kentico.com/modules/page-builder/enable-widgets-in-the-template
scrape_date: 2026-01-26
---

Module: Page Builder
8 of 16 Pages
# Enable widgets in the template
So far, we have [walked through](/modules/page-builder/model-template-data) the steps of creating a page template that displays structured product data with different styling options.
Next, let’s make the template truly flexible by adding the option to dynamically add [Editable areas](/documentation/developers-and-admins/development/builders/page-builder/create-pages-with-editable-areas) to the template based on configurable properties.
## Enable widgets on the template
Let’s start by adding a single conditional _editable area_ to the template. This will allow editors to put widgets on the page if a certain checkbox is enabled. Afterward, we can expand the functionality for more flexibility.
### Add a new checkbox to the template properties
  1. Add a new boolean property called `UsePageBuilder` to the `ProductPagePageTemplateProperties` class found in the _~/Features/Products_ folder.
  2. Make it default to `false` so that the template automatically uses the standard product layout.
  3. Apply the `CheckBoxComponent` attribute to assign a check box control to the field in the template properties dialog, and set its `Order` to 10 so it appears before the other properties. 
C#
**ProductPagePageTemplateProperties.cs**
Copy
```
...
namespace TrainingGuides.Web.Features.Products;

public class ProductPagePageTemplateProperties : IPageTemplateProperties
{
    [CheckBoxComponent(
    Label = "Use Page Builder",
    ExplanationText = "Check to configure an advanced Page Builder template. Un-check to use the standard product layout.",
    Order = 10)]
    public bool UsePageBuilder { get; set; } = false;
...
```



### Include an editable area in the template view
  1. Open the _ProductPagePageTemplate.cs_ file in the same folder.
  2. Cut all of the code within the scope of the first `tg-component-style` tag, and paste it in a text file for later copying.
  3. After the first `tg-component-style` tag, add an `if` statement.
  4. Check the value of the new `UsePageBuilder` property. If it is true, render an editable area with the identifier `areaMain`. 
The `areaMain` identifier matches other templates used on this site. Using consistent identifiers makes switching templates on a page and transferring Page Builder data easier. 
cshtml
**ProductPagePageTemplate.cshtml**
Copy
```
...
<tg-component-style color-scheme="@Model.Properties.ColorScheme" corner-style="@Model.Properties.CornerStyle">
    @if(Model.Properties.UsePageBuilder)
    {
        <editable-area area-identifier="areaMain" />
    }
</tg-component-style>
...
```

  5. Add an `else` statement, and paste the code from the text file within its scope. 
cshtml
**ProductPagePageTemplate.cshtml**
Copy
```
...
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
...
```


The resulting view should look like this: 
cshtml
**ProductPagePageTemplate.cshtml**
Copy
```
@using Microsoft.AspNetCore.Html
@using System.Globalization
@using TrainingGuides.Web.Features.Products
@using TrainingGuides.Web.Features.Products.Models
@using TrainingGuides.Web.Features.Shared.OptionProviders.ColorScheme

@model TemplateViewModel<ProductPagePageTemplateProperties>

@{
    var templateModel = Model.GetTemplateModel<ProductPageViewModel>();
}
<tg-component-style color-scheme="@Model.Properties.ColorScheme" corner-style="@Model.Properties.CornerStyle">
    @if(Model.Properties.UsePageBuilder)
    {
        <editable-area area-identifier="areaMain" />
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

### Check your progress
Now, if you sign in to the Xperience admin and apply this template to one of the pages under _Store_ in the _Training guides pages_ channel, you’ll see a new checkbox in the properties.
[![Screenshot of page template properties](/docsassets/guides/create-versatile-templates-part-2/ScreenshotTemplatePageBuilderCheckbox.png)](/docsassets/guides/create-versatile-templates-part-2/ScreenshotTemplatePageBuilderCheckbox.png)
If you tick the box and click apply, the standard product content should disappear. It will be replaced with an editable area, where you can add and drag widgets.
[![Screenshot of page template with editable area](/docsassets/guides/create-versatile-templates-part-2/ScreenshotTemplatePageBuilderArea.png)](/docsassets/guides/create-versatile-templates-part-2/ScreenshotTemplatePageBuilderArea.png)
If you un-tick the property, you may see a warning telling you that any content in the removed editable area will be lost upon saving.
[ Previous page ](/modules/page-builder/finish-the-structured-data-template)
8 of 16
[ Mark complete and continue ](/modules/page-builder/expand-page-builder-functionality)
![]()
[]()[]()
