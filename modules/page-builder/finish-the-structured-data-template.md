# Finish the structured data template
  * [ Copy page link ](modules/page-builder/finish-the-structured-data-template#) | [Get HelpService ID](modules/page-builder/finish-the-structured-data-template#)
Core MVC 5


[✖](modules/page-builder/finish-the-structured-data-template# "Close page link panel") [Copy to clipboard](modules/page-builder/finish-the-structured-data-template#)
Comparing the current template to the mockup from the start of this series, you’ll notice that the Product features table is missing.
[![Mockup of a product detail page](docsassets/guides/create-versatile-templates-part-1/WidgetMockups3.png)](https://docs.kentico.com/docsassets/guides/create-versatile-templates-part-1/WidgetMockups3.png)
Its data is already in the template model, so it should be fairly straightforward to render to the page.
However, if you inspect the _ProductFeature_ content type, you may notice that its value can come from multiple different places depending on the selected _ProductFeatureValueType_.
Let’s make one last tag helper, so that we don’t need to clutter the view with conditionals.
### Add a product feature value tag helper
Since this one is specifically related to Product features, and not general styling, we can put it in the _~/Features/Products/TagHelpers_ folder.
  1. Create a new self-closing tag helper called `ProductFeatureValueTagHelper`, with the tag name `tg-product-feature-value`.
  2. Add a `ProductFeatureViewModel` property and a constant for the `span` tag.
  3. In the `Process` method, determine the formated value depending on the provided `ProductFeatureValueType` from the view model and render it.


C#
**ProductFeatureValueTagHelper.cs**
Copy
```
using System.Globalization;
using Microsoft.AspNetCore.Html;
using Microsoft.AspNetCore.Razor.TagHelpers;
using TrainingGuides.Web.Features.Products.Models;

namespace TrainingGuides.Web.Features.Products.TagHelpers;

/// <summary>
/// Formats Product feature value based on its type.
/// </summary>
[HtmlTargetElement("tg-product-feature-value", TagStructure = TagStructure.WithoutEndTag)] //This TagStructure allows the helper to be called with a single self-closing tag in razor views.
public class ProductFeatureValueTagHelper : TagHelper
{
    public ProductFeatureViewModel? Feature { get; set; }

    private const string SPAN_TAG = "span";

    public override void Process(TagHelperContext context, TagHelperOutput output)
    {
        output.TagName = SPAN_TAG;

        // Make sure to set the output's  TagMode to StartTagAndEndTag.
        // This ensures that even though the tag helper is called with a single tag, the rendered output closes the `span` tag that wraps the value.
        output.TagMode = TagMode.StartTagAndEndTag;

        string? formattedValue = Feature?.ValueType switch
        {
            ProductFeatureValueType.Text => Feature.ValueHtml.Value,
            ProductFeatureValueType.Number => string.Format(CultureInfo.CurrentUICulture, "{0:0.00}", Feature.Price),
            ProductFeatureValueType.Boolean => Feature.FeatureIncluded ? "✔" : "-",
            _ => string.Empty
        };

        output.Content.SetHtmlContent(new HtmlString(formattedValue));
    }
}
```

### Add the product features list to the template view
  1. In the _~/Features/Products/ProductPagePageTemplate.cshtml_ folder, add a `foreach` loop to cycle through the product features.
  2. Retrieve each feature’s `LabelHtml` directly, and use the new tag helper to display its value
  3. Wrap the whole thing in the `tg-component-style` tag helper, setting `color-scheme` directly and `corner-style` based on the template properties.


cshtml
**ProductPagePageTemplate.cshtml**
Copy
```
...
<tg-component-style color-scheme="@ColorSchemeOption.Light1" corner-style="@Model.Properties.CornerStyle"  class="c-table">
    @foreach (var feature in templateModel.Features)
    {
        <div>
            <div>@feature.LabelHtml</div>
            <div>
                <tg-product-feature-value feature="@feature"/>
            </div>
        </div>
    }
</tg-component-style>
...
```

### Finish styling the template.
Now if you run the site, you’ll see all the data, but it’s still not formatted in a way that matches the mockup.
[![Screenshot of unfinished template](docsassets/guides/create-versatile-templates-part-1/ScreenshotTemplateProgress2.jpg)](https://docs.kentico.com/docsassets/guides/create-versatile-templates-part-1/ScreenshotTemplateProgress2.jpg)
Add CSS styles and any necessary elements to make the template more visually appealing.
cshtml
**ProductPagePageTemplate.cshtml**
Copy
```
@using TrainingGuides.Web.Features.Products.Models
@using TrainingGuides.Web.Features.Products
@using TrainingGuides.Web.Features.Shared.OptionProviders.ColorScheme

@model TemplateViewModel<ProductPagePageTemplateProperties>

@{
    var templateModel = Model.GetTemplateModel<ProductPageViewModel>();
}

<tg-component-style color-scheme="@Model.Properties.ColorScheme" corner-style="@Model.Properties.CornerStyle">
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
</tg-component-style>
```

At the end of it all, you should be able to achieve this by configuring your template.
[![Screenshot of unfinished, but nicely formatted template](docsassets/guides/create-versatile-templates-part-1/ScreenshotTemplateProgress3.jpg)](https://docs.kentico.com/docsassets/guides/create-versatile-templates-part-1/ScreenshotTemplateProgress3.jpg)
[ Previous page ](modules/page-builder/style-the-template)
7 of 16
[ Mark complete and continue ](modules/page-builder/enable-widgets-in-the-template)
  * [Community Questions & Answers](https://community.kentico.com/q-and-a)
  * [Contact support](https://community.kentico.com/support)


### Cookie consent
We use necessary [cookies](https://www.kentico.com/cookies-policy) to run our website and improve your experience while browsing. Additional cookies are only used with your consent. You may revoke your consent on the [Cookies Policy](https://www.kentico.com/cookies-policy) page or in your browser at any time. 
ACCEPT ALL  [Configure](https://www.kentico.com/cookies-policy)
USE ONLY NECESSARY 
![](https://docs.kentico.com/modules/page-builder/finish-the-structured-data-template)
[](https://docs.kentico.com/modules/page-builder/finish-the-structured-data-template)[](https://docs.kentico.com/modules/page-builder/finish-the-structured-data-template)