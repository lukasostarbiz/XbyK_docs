---
source: https://docs.kentico.com/modules/page-builder/define-template-properties
scrape_date: 2026-01-22
---

Module: Page Builder
5 of 16 Pages
# Define the template's properties
Now that we have view models to determine the product template’s data, let’s add properties, so that editors can configure its appearance.
The properties will utilize dropdowns fed by the dropdown provider we created [earlier in this series](/modules/page-builder/map-enum-to-dropdown), so start by defining enumerations for different color scheme and corner style options.
Decorate options with the `Description` attribute to provide user-friendly names.
  * In the _~/Features/Shared/OptionProviders/ColorScheme_ directory:
C#
**ColorSchemeOption.cs**
Copy
```
using System.ComponentModel;

namespace TrainingGuides.Web.Features.Shared.OptionProviders.ColorScheme;

public enum ColorSchemeOption
{
    [Description("Light background, dark text")]
    Light1 = 1,

    [Description("Light background, dark text 2")]
    Light2 = 2,

    [Description("Light background, dark text 3")]
    Light3 = 3,

    [Description("Transparent background, dark text")]
    TransparentDark = 4,

    [Description("Transparent background, medium text")]
    TransparentMedium = 5,

    [Description("Transparent background, light text")]
    TransparentLight = 6,

    [Description("Dark background, light text")]
    Dark1 = 7,

    [Description("Dark background, light text 2")]
    Dark2 = 8
}
```

  * In the _~/Features/Shared/OptionProviders/CornerStyle_ directory:
C#
**CornerStyleOption.cs**
Copy
```
using System.ComponentModel;

namespace TrainingGuides.Web.Features.Shared.OptionProviders.CornerStyle;

public enum CornerStyleOption
{
    [Description("Sharp corners")]
    Sharp = 0,
    [Description("Round corners")]
    Round = 1,
    [Description("Very round corners")]
    VeryRound = 2,
}
```



Next, create a properties class for the _ProductPage_ content type that utilizes these dropdowns in the _~/Features/Products_ folder.
C#
**ProductPagePageTemplateProperties.cs**
Copy
```
using Kentico.PageBuilder.Web.Mvc.PageTemplates;
using Kentico.Xperience.Admin.Base.FormAnnotations;
using TrainingGuides.Web.Features.Shared.OptionProviders;
using TrainingGuides.Web.Features.Shared.OptionProviders.CornerStyle;
using TrainingGuides.Web.Features.Shared.OptionProviders.ColorScheme;

namespace TrainingGuides.Web.Features.Products;

public class ProductPagePageTemplateProperties : IPageTemplateProperties
{
    [DropDownComponent(
        Label = "Color scheme",
        ExplanationText = "Select the color scheme of the template.",
        DataProviderType = typeof(DropdownEnumOptionProvider<ColorSchemeOption>),
        Order = 20)]
    public string ColorScheme { get; set; } = nameof(ColorSchemeOption.TransparentDark);

    [DropDownComponent(
        Label = "Corner style",
        ExplanationText = "Select the corner type of the template.",
        DataProviderType = typeof(DropdownEnumOptionProvider<CornerStyleOption>),
        Order = 30)]
    public string CornerStyle { get; set; } = nameof(CornerStyleOption.Round);
}
```

### Set up the template
With the view models and service in place, we can create the page template, register it, and serve it from a controller.
**Find more details here**
This section briefly goes over processes covered in [Build a page template](/guides/development/developer-kickstart/build-a-page-template) step of our Kickstart series. Please refer to it if you’d like more detailed steps and information in the context of a different example.
Note these sections in particular:
  * [Add the page template view](/guides/development/developer-kickstart/build-a-page-template#add-the-page-template-view)
  * [Register the page template](/guides/development/developer-kickstart/build-a-page-template#register-the-page-template)
  * [Define a page template controller](/guides/development/developer-kickstart/build-a-page-template#define-a-page-template-controller)


#### Define the view
With the view model and properties in place, you can create a view under _~/Features/Products_.
Use the `GetTemplateModel` method to retrieve structured data and display it in a view. We’ll expand on this in the future. For now, let’s keep it simple.
cshtml
**ProductPagePageTemplate.cshtml**
Copy
```
@using TrainingGuides.Web.Features.Products.Models
@using TrainingGuides.Web.Features.Products

@model TemplateViewModel<ProductPagePageTemplateProperties>

@{
    var templateModel = Model.GetTemplateModel<ProductPageViewModel>();
}

<div>
    <div>
        <h3>@templateModel.NameHtml</h3>
        <p>@templateModel.ShortDescriptionHtml</p>
    </div>

    <div>
        <img src="@templateModel.Media.FirstOrDefault()?.FilePath" alt="@templateModel.Media.FirstOrDefault()?.Description"/>
    </div>
</div>
```

#### Register the template
Create a new file called _ProductPagePageTemplate.cs_ in the _~/Features/Products_ folder, and use it to house the registration attribute for the page template.
C#
**ProductPagePageTemplate.cs**
Copy
```
using Kentico.PageBuilder.Web.Mvc.PageTemplates;
using TrainingGuides;
using TrainingGuides.Web.Features.Products;

[assembly: RegisterPageTemplate(
    identifier: ProductPagePageTemplate.IDENTIFIER,
    name: "Product page template",
    propertiesType: typeof(ProductPagePageTemplateProperties),
    customViewName: "~/Features/Products/ProductPagePageTemplate.cshtml",
    ContentTypeNames = [ProductPage.CONTENT_TYPE_NAME],
    IconClass = "xp-box")]

namespace TrainingGuides.Web.Features.Products;
public static class ProductPagePageTemplate
{
    public const string IDENTIFIER = "TrainingGuides.ProductPageTemplate";
}
```

#### Create the controller
  1. In the _~/Features/Products_ folder, add a file called _ProductPageController_.
  2. Use an `IContentItemRetrieverService` to get the product data from Xperience.
  3. Use an instance of the `IProductPageService` we defined [earlier](/modules/page-builder/model-template-data) to convert the `ProductPage` to a `ProductPageViewModel`.
  4. Add a new `ProductFeatureViewModel` containing the product’s price, so that it appears in the table.


C#
**ProductPageController.cs**
Copy
```
using Kentico.Content.Web.Mvc.Routing;
using Kentico.PageBuilder.Web.Mvc.PageTemplates;
using Microsoft.AspNetCore.Mvc;
using TrainingGuides;
using TrainingGuides.Web.Features.Products.Models;
using TrainingGuides.Web.Features.Products.Services;
using TrainingGuides.Web.Features.Shared.Services;

[assembly: RegisterWebPageRoute(
    contentTypeName: ProductPage.CONTENT_TYPE_NAME,
    controllerType: typeof(TrainingGuides.Web.Features.Products.ProductPageController))]

namespace TrainingGuides.Web.Features.Products;

public class ProductPageController : Controller
{
    private readonly IContentItemRetrieverService contentItemRetriever;
    private readonly IProductPageService productPageService;

    public ProductPageController(
        IContentItemRetrieverService contentItemRetriever,
        IProductPageService productPageService)
    {
        this.contentItemRetriever = contentItemRetriever;
        this.productPageService = productPageService;
    }

    public async Task<IActionResult> Index()
    {
        var productPage = await contentItemRetriever.RetrieveCurrentPage<ProductPage>(3);

        var model = await productPageService.GetProductPageViewModel(productPage);
        model.Features.Add(
                new ProductFeatureViewModel
                {
                    Key = "price-from-product-content-item",
                    Name = "Price",
                    LabelHtml = new("Price"),
                    Price = model.Price,
                    ValueHtml = new(string.Empty),
                    FeatureIncluded = false,
                    ValueType = ProductFeatureValueType.Number,
                    ShowInComparator = true,
                });

        return new TemplateResult(model);
    }
}
```

#### Check your progress
We’ve been focused on code for a while, so let’s double-check that everything is working on the site.
  1. Sign in to the Xperience admin and open the _Training guides pages_ channel.
  2. Choose one of the _Product page_ pages under the _Products_ page in the tree, and edit the page on the _Page Builder_ tab.
  3. Click the page icon in the bottom left corner of the Page Builder frame to configure the Page Builder properties.
  4. Choose the _Product page template_ , and see how the data from the product page is now displayed according to your templates.

Your browser does not support the video tag. 
If you’re not seeing any output, make sure the product page type is included in the `UsePageBuilder` call during the application’s startup sequence, try putting breakpoints in the controller and view. If one or both of them is not being hit, it could indicate an issue in one of the registration attributes for the controller or page template.
[ Previous page ](/modules/page-builder/model-template-data)
5 of 16
[ Mark complete and continue ](/modules/page-builder/style-the-template)
![]()
[]()[]()
