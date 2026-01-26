---
source: https://docs.kentico.com/guides/development/page-builder/create-versatile-templates-part-1
scrape_date: 2026-01-26
---

  * [Home](/guides)
  * [Development](/guides/development)
  * [Page Builder](/guides/development/page-builder)
  * Create versatile page templates, part 1 


# Create versatile page templates, part 1
In this guide, we’ll walk through the process of creating a page template to match this product detail mockup:
[![Mockup of a product detail page](/docsassets/guides/create-versatile-templates-part-1/WidgetMockups3.png)](/docsassets/guides/create-versatile-templates-part-1/WidgetMockups3.png)
See the [first guide in this series](/guides/development/page-builder/meet-requirements-with-page-builder) for an overview of the mockup, and a breakdown of how to meet business requirements with [Page Builder](/documentation/developers-and-admins/development/builders/page-builder).
## Prerequisites
This guide uses the [main branch of the Training guides repository](https://github.com/Kentico/xperience-by-kentico-training-guides) as a starting point.
It relies on the dropdown provider from the [previous guide](/guides/development/page-builder/map-enum-to-dropdown).
If you’re following along while working in a different environment, make sure:
  * Your instance of Xperience by Kentico is version [30.11.1](/documentation/changelog) or higher. 
Some features covered in the training guides may not work in older versions. 
  * Your site has _Product_ , _ProductPage_ , _ProductFeature_ , and _Benefit_ content types that match the structure of those in the repository.
  * [Page Builder is enabled](/guides/development/developer-kickstart/configure-the-project#enable-content-tree-based-routing-and-page-builder) for the _Product page_ content type during the application’s startup sequence.


This guide will take place in the _TrainingGuides.Web_ project of the repository, so assume any folder paths start from the root of this project.
## Show structured data
The data in the mockup comes from the _Product_ content type, which is served in the web channel through the _Product page_ content type.
In order to implement a page that matches the mockup without any extra steps for editors, we need to access that data and display it directly in the template.
Let’s start by setting up view models to work with the data.
### Set up the view models
#### Create supporting view models
First, let’s set up the view model for the product page.
If you look at the _Product_ content type in Xperience, you’ll notice that products have collections of _Benefit_ and _Product feature_ content items. Start by making view models for these content types, so we can use them in the product.
  1. In the _~/Features/Products/Models_ folder, add a file called _ProductFeatureViewModel.cs_.
  2. Create properties corresponding to the content type’s fields. 
C#
**ProductFeatureViewModel.cs**
Copy
```
using Microsoft.AspNetCore.Html;

namespace TrainingGuides.Web.Features.Products.Models;

public class ProductFeatureViewModel
{
    public string Key { get; set; } = string.Empty;
    public string Name { get; set; } = string.Empty;
    public HtmlString LabelHtml { get; set; } = HtmlString.Empty;
    public decimal Price { get; set; }
    public HtmlString ValueHtml { get; set; } = HtmlString.Empty;
    public bool FeatureIncluded { get; set; }
    public ProductFeatureValueType ValueType { get; set; }
    public bool ShowInComparator { get; set; }
}
```

  3. Include a `ProductFeatureValueType` enumeration to represent the value of the `ValueType` field at the bottom of the file. 
C#
**ProductFeatureViewModel.cs**
Copy
```
...
//(After the end of the ProductFeatureViewModel class's scope)

public enum ProductFeatureValueType
{
    //These integers correspond to the data source in the Product feature content type, defined in the Xperience admin interface.
    Text = 0,
    Number = 1,
    Boolean = 2
}
```

  4. Add a method to the `ProductFeatureViewModel` class to get the corresponding enum value from an integer. 
C#
**ProductFeatureViewModel.cs**
Copy
```
...
private static ProductFeatureValueType GetValueType(string value)
{
    if (string.IsNullOrEmpty(value))
        return ProductFeatureValueType.Text;

    if (int.TryParse(value, out int id))
        return (ProductFeatureValueType)id;

    return ProductFeatureValueType.Text;
}
...
```

  5. Add a static `GetViewModel` method to create and populate a `ProductFeatureViewModel` based on a `ProductFeature` object. 
`ProductFeature` is a generated class, created by the Xperience [code generation tool](/documentation/developers-and-admins/api/generate-code-files-for-system-objects). 
C#
**ProductFeatureViewModel.cs**
Copy
```
...
public static ProductFeatureViewModel GetViewModel(ProductFeature feature) => new()
{
    Key = feature.ProductFeatureKey,
    Name = feature.SystemFields.ContentItemName,
    LabelHtml = new(feature.ProductFeatureLabel),
    Price = feature.ProductFeaturePrice,
    ValueHtml = new(feature.ProductFeatureValue),
    FeatureIncluded = feature.ProductFeatureIncluded,
    ValueType = GetValueType(feature.ProductFeatureValueType),
    ShowInComparator = feature.ProductFeatureShowInComparator == "1"
};
...
```



That takes care of _Product feature_ , so let’s move on to the _Benefit_ content type.
  1. Create a new class called `BenefitViewModel` in the _~/Features/Shared/Models_ folder.
  2. Add properties that correspond to the _Benefit_ content type’s fields, utilizing the existing `AssetViewModel` class for the asset. 
C#
**BenefitViewModel.cs**
Copy
```
using Microsoft.AspNetCore.Html;

namespace TrainingGuides.Web.Features.Shared.Models;

public class BenefitViewModel
{
    public HtmlString DescriptionHtml { get; set; } = HtmlString.Empty;
    public AssetViewModel Icon { get; set; } = new();
}
```

  3. Add a static `GetViewModel` method to retrieve a `BenefitViewModel` from an object of the generated `Benefit` class. 
C#
**BenefitViewModel.cs**
Copy
```
...
public static BenefitViewModel GetViewModel(Benefit benefit) => new()
{
    DescriptionHtml = new(benefit.BenefitDescription),
    Icon = benefit.BenefitIcon?.FirstOrDefault() != null
        ? AssetViewModel.GetViewModel(benefit.BenefitIcon.FirstOrDefault())
        : new(),
};
```


At this point, your view models should look something like this: 
C#
**ProductFeatureViewModel.cs**
Copy
```
using Microsoft.AspNetCore.Html;

namespace TrainingGuides.Web.Features.Products.Models;

public class ProductFeatureViewModel
{
    public string Key { get; set; } = string.Empty;
    public string Name { get; set; } = string.Empty;
    public HtmlString LabelHtml { get; set; } = HtmlString.Empty;
    public decimal Price { get; set; }
    public HtmlString ValueHtml { get; set; } = HtmlString.Empty;
    public bool FeatureIncluded { get; set; }
    public ProductFeatureValueType ValueType { get; set; }
    public bool ShowInComparator { get; set; }

    public static ProductFeatureViewModel GetViewModel(ProductFeature feature) => new()
    {
        Key = feature.ProductFeatureKey,
        Name = feature.SystemFields.ContentItemName,
        LabelHtml = new(feature.ProductFeatureLabel),
        Price = feature.ProductFeaturePrice,
        ValueHtml = new(feature.ProductFeatureValue),
        FeatureIncluded = feature.ProductFeatureIncluded,
        ValueType = GetValueType(feature.ProductFeatureValueType),
        ShowInComparator = feature.ProductFeatureShowInComparator == "1"
    };

    private static ProductFeatureValueType GetValueType(string value)
    {
        if (string.IsNullOrEmpty(value))
            return ProductFeatureValueType.Text;

        if (int.TryParse(value, out int id))
            return (ProductFeatureValueType)id;

        return ProductFeatureValueType.Text;
    }
}

public enum ProductFeatureValueType
{
    Text = 0,
    Number = 1,
    Boolean = 2
}
```

C#
**BenefitViewModel.cs**
Copy
```
using Microsoft.AspNetCore.Html;

namespace TrainingGuides.Web.Features.Shared.Models;

public class BenefitViewModel
{
    public HtmlString DescriptionHtml { get; set; } = HtmlString.Empty;
    public AssetViewModel Icon { get; set; } = new();

    public static BenefitViewModel GetViewModel(Benefit benefit) => new()
    {
        DescriptionHtml = new(benefit.BenefitDescription),
        Icon = benefit.BenefitIcon?.FirstOrDefault() != null
            ? AssetViewModel.GetViewModel(benefit.BenefitIcon.FirstOrDefault())
            : new(),
    };
}
```

#### Implement the Product page view model
With these in place, let’s move on to the `ProductPageViewModel`.
  1. Create a `ProductPageViewModel` class in the _~/Features/Products/Models_ folder.
  2. Inherit from the [`PageViewModel` class](https://github.com/Kentico/xperience-by-kentico-training-guides/blob/finished/src/TrainingGuides.Web/Features/Shared/Models/PageViewModel.cs). 
C#
**ProductPageViewModel.cs**
Copy
```
namespace TrainingGuides.Web.Features.Products.Models;

public class ProductPageViewModel : PageViewModel { }
```

Notice the `Link` property of the `PageViewModel` class, which can be re-used by other page types in the future. 
  3. Add properties that mirror the fields of the _Product_ content type, with collections of `ProductFeatureViewModel` and `BenefitViewModel` objects where appropriate. 
C#
**ProductPageViewModel.cs**
Copy
```
using Microsoft.AspNetCore.Html;
using TrainingGuides.Web.Features.Shared.Models;
...
public class ProductPageViewModel : PageViewModel
{
    public HtmlString NameHtml { get; set; } = HtmlString.Empty;
    public HtmlString ShortDescriptionHtml { get; set; } = HtmlString.Empty;
    public HtmlString DescriptionHtml { get; set; } = HtmlString.Empty;
    public List<AssetViewModel> Media { get; set; } = [];
    public decimal Price { get; set; }
    public List<ProductFeatureViewModel> Features { get; set; } = [];
    public List<BenefitViewModel> Benefits { get; set; } = [];
}
```



#### Add a supporting service
You might notice that `ProductViewModel` doesn’t have a `GetViewModel` method like `ProductFeatureViewModel` and `BenefitViewModel`. This is because it needs _Dependency injection_ to get the page’s URL, so we’re going to move it to a service instead. 
Make sure that any methods you include in your view model classes are simple, with only basic data transformation at most.
  1. Add an interface called `IProductPageService` to _~/Features/Products/Services_.
  2. Include a method signature `GetProductPageViewModel` with the following parameters:
    1. The `ProductPage` object to base the view model on.
    2. Optional boolean values to indicate which values to include.
    3. Optional settings for the call to action link 
C#
**IProductPageService.cs**
Copy
```
using TrainingGuides.Web.Features.Products.Models;

namespace TrainingGuides.Web.Features.Products.Services;

public interface IProductPageService
{
    public Task<ProductPageViewModel> GetProductPageViewModel(
        ProductPage? productPage,
        bool getMedia = true,
        bool getFeatures = true,
        bool getBenefits = true,
        string callToAction = "",
        string callToActionLink = "",
        bool openInNewTab = true,
        bool getPrice = true);
}
```

  3. Add a `ProductPageService` class that implements this interface.
C#
**ProductPageService.cs**
Copy
```
...
public class ProductPageService : IProductPageService
...
```

  4. Use _dependency injection_ to acquire an `IWebPageUrlRetriever` object.
C#
**ProductPageService.cs**
Copy
```
...
private readonly IWebPageUrlRetriever webPageUrlRetriever;
public ProductPageService(IWebPageUrlRetriever webPageUrlRetriever)
{
    this.webPageUrlRetriever = webPageUrlRetriever;
}
...
```

The code samples in this guide rely on a [decorated version](https://github.com/Kentico/xperience-by-kentico-training-guides/blob/main/src/TrainingGuides.Web/Features/Shared/Services/TrainingGuidesWebPageUrlRetriever.cs) of `IWebPageUrlRetriever` that includes exception handling.
If you do not plan to use a similar customization, make sure to handle errors that the `Retrieve` method may throw when it cannot find a page. 
  5. Implement the `GetProductPageViewModel` method.
C#
**ProductPageService.cs**
Copy
```
using Microsoft.IdentityModel.Tokens;
using TrainingGuides.Web.Features.Products.Models;
using TrainingGuides.Web.Features.Shared.Models;

namespace TrainingGuides.Web.Features.Products.Services;

public class ProductPageService : IProductPageService
{
    private readonly IWebPageUrlRetriever webPageUrlRetriever;
    public ProductPageService(IWebPageUrlRetriever webPageUrlRetriever)
    {
        this.webPageUrlRetriever = webPageUrlRetriever;
    }

    /// <summary>
    /// Creates a new instance of <see cref="ProductPageViewModel"/>, setting the properties using ProductPage given as a parameter.
    /// </summary>
    /// <param name="productPage">Corresponding Product page object.</param>
    /// <returns>New instance of ProductPageViewModel.</returns>
    public async Task<ProductPageViewModel> GetProductPageViewModel(
        ProductPage? productPage,
        bool getMedia = true,
        bool getFeatures = true,
        bool getBenefits = true,
        string callToActionText = "",
        string callToActionLink = "",
        bool openInNewTab = true,
        bool getPrice = true)
    {
        //Return an empty view model if the provided ProductPage is null.
        if (productPage == null)
        {
            return new ProductPageViewModel();
        }

        //Use the IWebPageUrlRetriever to get the URL of the product page.
        string url = callToActionLink.IsNullOrEmpty()
            ? (await webPageUrlRetriever.Retrieve(productPage)).RelativePath
            : callToActionLink;

        //Make sure to account for the boolean parameters as you construct the view model.
        return new ProductPageViewModel
        {
            NameHtml = new(productPage.ProductPageProduct.FirstOrDefault()?.ProductName),
            ShortDescriptionHtml = new(productPage.ProductPageProduct.FirstOrDefault()?.ProductShortDescription),
            DescriptionHtml = new(productPage.ProductPageProduct.FirstOrDefault()?.ProductDescription),
            Media = getMedia
                ? productPage.ProductPageProduct.FirstOrDefault()?
                    .ProductMedia.Select(AssetViewModel.GetViewModel)?
                    .ToList() ?? []
                : [],
            Link = new LinkViewModel()
            {
                Name = productPage.ProductPageProduct.FirstOrDefault()?.ProductName ?? string.Empty,
                LinkUrl = url,
                CallToAction = callToActionText.IsNullOrEmpty()
                    ? string.Empty
                    : callToActionText,
                OpenInNewTab = openInNewTab
            },
            Features = getFeatures
                ? productPage.ProductPageProduct.FirstOrDefault()?
                    .ProductFeatures
                    .Select(ProductFeatureViewModel.GetViewModel)
                    .ToList() ?? []
                : [],
            Benefits = getBenefits
                ? productPage.ProductPageProduct.FirstOrDefault()?
                    .ProductBenefits
                    .Select(BenefitViewModel.GetViewModel)
                    .ToList() ?? []
                : [],
            Price = getPrice ? productPage.ProductPageProduct
                .FirstOrDefault()?.ProductPrice ?? 0 : 0,
        };
    }
}
```

  6. Register the `ProductPageService` with the dependency injection container in _TrainingGuides.Web/ServiceCollectionExtensions.cs_ :
C#
**ServiceCollectionExtensions.cs**
Copy
```
...
public static void AddTrainingGuidesServices(this IServiceCollection services)
{
    ...
    services.AddSingleton<IProductPageService, ProductPageService>();
    ...
}
...
```



### Define page template properties
Now that we have view models to determine the product template’s data, let’s add properties, so that editors can configure its appearance.
The properties will utilize dropdowns fed by the dropdown provider we created [earlier in this series](/guides/development/page-builder/map-enum-to-dropdown), so start by defining enumerations for different color scheme and corner style options.
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
  3. Use an instance of the `IProductPageService` we defined [earlier](#add-a-supporting-service) to convert the `ProductPage` to a `ProductPageViewModel`.
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
## Style the template based on properties
You probably noticed in the last section that the template is not very pretty yet. You’ll also find that the dropdowns for color scheme and corner style in the template properties don’t change anything.
Let’s create a service that maps the options from the dropdown to CSS classes, and a tag helper that uses it to style a `div` element.
### Create a service to retrieve styles
  1. Define an `IComponentStyleEnumService` interface in the _~/Features/Shared/Services_ folder.
  2. Include method signatures to retrieve CSS classes based on a `ColorSchemeOption` and `CornerStyleOption` respectively. 
C#
**IComponentStyleEnumService.cs**
Copy
```
...
namespace TrainingGuides.Web.Features.Shared.Services;

public interface IComponentStyleEnumService
{
    IEnumerable<string> GetColorSchemeClasses(ColorSchemeOption colorScheme);

    IEnumerable<string> GetCornerStyleClasses(CornerStyleOption cornerStyle);
...
```

  3. Add method signatures to convert the string representation of a selected option to its corresponding enum value. 
C#
**IComponentStyleEnumService.cs**
Copy
```
...
CornerStyleOption GetCornerStyle(string cornerStyleString);

ColorSchemeOption GetColorScheme(string colorSchemeString);
}
```

  4. Implement the interface, mapping the selected options to sets of CSS classes. Use the `Parse` method from the `EnumStringService` we defined [earlier in the series](/guides/development/page-builder/map-enum-to-dropdown) for the string conversion methods. 
C#
**ComponentStyleEnumService.cs**
Copy
```
using TrainingGuides.Web.Features.Shared.OptionProviders;
using TrainingGuides.Web.Features.Shared.OptionProviders.CornerStyle;
using TrainingGuides.Web.Features.Shared.OptionProviders.ColorScheme;

namespace TrainingGuides.Web.Features.Shared.Services;

public class ComponentStyleEnumService : IComponentStyleEnumService
{
    public IEnumerable<string> GetColorSchemeClasses(ColorSchemeOption colorScheme) => colorScheme switch
    {
        ColorSchemeOption.Light1 => ["tg-bg-light-1", "tg-txt-dark"],
        ColorSchemeOption.Light2 => ["tg-bg-light-2", "tg-txt-dark"],
        ColorSchemeOption.Light3 => ["tg-bg-light-3", "tg-txt-dark"],
        ColorSchemeOption.Dark1 => ["tg-bg-primary", "tg-txt-light"],
        ColorSchemeOption.Dark2 => ["tg-bg-secondary", "tg-txt-light"],
        ColorSchemeOption.TransparentLight => ["tg-bg-none", "tg-txt-light"],
        ColorSchemeOption.TransparentMedium => ["tg-bg-none", "tg-txt-medium"],
        ColorSchemeOption.TransparentDark => ["tg-bg-none", "tg-txt-dark"],
        _ => [string.Empty],
    };

    public IEnumerable<string> GetCornerStyleClasses(CornerStyleOption cornerStyle) => cornerStyle switch
    {
        CornerStyleOption.Round => ["tg-corner-rnd"],
        CornerStyleOption.VeryRound => ["tg-corner-v-rnd"],
        CornerStyleOption.Sharp => ["tg-corner-shrp"],
        _ => [string.Empty],
    };

    public CornerStyleOption GetCornerStyle(string cornerStyleString) =>
        enumStringService.Parse(cornerStyleString, CornerStyleOption.Round);

    public ColorSchemeOption GetColorScheme(string colorSchemeString) =>
        enumStringService.Parse(colorSchemeString, ColorSchemeOption.TransparentDark);
}
```

Each of the classes in this example are small, usually only setting one css property to minimize collisions across classes.
Defining the styles themselves is outside the scope of this series, but you can find their definitions in the _~/scss_ directory, where .scss styles are defined and later compiled to css.
If you are using the [main branch of the Training guides repository](https://github.com/Kentico/xperience-by-kentico-training-guides) to follow along, the classes should already exist. 
  5. Remember to register the service with the dependency injection container in the _TrainingGuides.Web/ServiceCollectionExtensions.cs_ file: 
C#
**ServiceCollectionExtensions.cs**
Copy
```
...
public static void AddTrainingGuidesServices(this IServiceCollection services)
{
    ...
    services.AddSingleton<IComponentStyleEnumService, ComponentStyleEnumService>();
    ...
}
...
```



### Apply the styles with a tag helper
  1. Add a new class called `ComponentStyleTagHelper`to the _~/Features/Shared/Helpers/TagHelpers_ folder.
  2. Inherit from the .NET `TagHelper` class, and decorate it with the `HtmlTargetElement` attribute to set its element name to `tg-component-style`. 
C#
**ComponentStyleTagHelper.cs**
Copy
```
...
[HtmlTargetElement("tg-component-style")]
public class ComponentStyleTagHelper : TagHelper
...
```

  3. Give the tag helper string properties called `ColorScheme` and `CornerStyle`, so that objects that store selections as strings, such as page template properties, can use them directly. 
C#
**ComponentStyleTagHelper.cs**
Copy
```
...
public string ColorScheme { get; set; } = string.Empty;
public string CornerStyle { get; set; } = string.Empty;
...
```

  4. Use a constant to hold the type of tag, in case we need to add more complex logic for determining the tag name in the future. 
C#
**ComponentStyleTagHelper.cs**
Copy
```
...
private const string DIV_TAG = "div";
...
```

  5. Acquire an `IComponentStyleEnumService` object through dependency injection. 
C#
**ComponentStyleTagHelper.cs**
Copy
```
...
private readonly IComponentStyleEnumService componentStyleEnumService;

public ComponentStyleTagHelper(IComponentStyleEnumService componentStyleEnumService) : base()
{
    this.componentStyleEnumService = componentStyleEnumService;
}
...
```

  6. In the `Process` method, use the service to retrieve styles for the output tag. 
C#
**ComponentStyleTagHelper.cs**
Copy
```
...
public override void Process(TagHelperContext context, TagHelperOutput output)
{
    output.TagName = DIV_TAG;

    List<string> cssClasses = [];

    var colorScheme = componentStyleEnumService.GetColorScheme(ColorScheme);
    cssClasses.AddRange(componentStyleEnumService.GetColorSchemeClasses(colorScheme));

    var cornerStyle = componentStyleEnumService.GetCornerStyle(CornerStyle);
    cssClasses.AddRange(componentStyleEnumService.GetCornerStyleClasses(cornerStyle));

    if (cssClasses.Count > 0)
    {
        foreach (string cssClass in cssClasses)
        {
            output.AddClass(cssClass, HtmlEncoder.Default);
        }
    }
}
...
```


In the end, your tag helper should look like this: 
C#
**ComponentStyleTagHelper.cs**
Copy
```
using System.Text.Encodings.Web;
using Microsoft.AspNetCore.Mvc.TagHelpers;
using Microsoft.AspNetCore.Razor.TagHelpers;
using TrainingGuides.Web.Features.Shared.Services;


namespace TrainingGuides.Web.Features.Shared.Helpers.TagHelpers;

[HtmlTargetElement("tg-component-style")]
public class ComponentStyleTagHelper : TagHelper
{
    public string ColorScheme { get; set; } = string.Empty;
    public string CornerStyle { get; set; } = string.Empty;

    private const string DIV_TAG = "div";

    private readonly IComponentStyleEnumService componentStyleEnumService;

    public ComponentStyleTagHelper(IComponentStyleEnumService componentStyleEnumService) : base()
    {
        this.componentStyleEnumService = componentStyleEnumService;
    }
    public override void Process(TagHelperContext context, TagHelperOutput output)
    {
        output.TagName = DIV_TAG;

        List<string> cssClasses = [];

        var colorScheme = componentStyleEnumService.GetColorScheme(ColorScheme);
        cssClasses.AddRange(componentStyleEnumService.GetColorSchemeClasses(colorScheme));

        var cornerStyle = componentStyleEnumService.GetCornerStyle(CornerStyle);
        cssClasses.AddRange(componentStyleEnumService.GetCornerStyleClasses(cornerStyle));

        if (cssClasses.Count > 0)
        {
            foreach (string cssClass in cssClasses)
            {
                output.AddClass(cssClass, HtmlEncoder.Default);
            }
        }
    }
}
```

### Put the tag helper to use
The tag helper is now ready to be added to the view. If you return to _ProductPagePageTemplate.cshtml_ you can wrap all of the existing markup with the new tag helper tag, and pass along the color scheme and corner style properties from the page template’s configuration. 
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

<tg-component-style color-scheme="@Model.Properties.ColorScheme" corner-style="@Model.Properties.CornerStyle">
    <div>
        <div>
            <h3>@templateModel.NameHtml</h3>
            <p>@templateModel.ShortDescriptionHtml</p>
        </div>

        <div>
            <img src="@templateModel.Media.FirstOrDefault()?.FilePath" alt="@templateModel.Media.FirstOrDefault()?.Description"/>
        </div>
    </div>
</tg-component-style>
```

If you sign in to the Xperience administration interface, you’ll notice that you can now adjust the appearance of the box containing the product’s data by clicking the gear icon in the bottom left of the _Page Builder_ pane.
However, if you set the template to use rounded corners and a colored background, you’ll see that the corners of the image do not change to match the style of the parent div. The image may also be an unreasonable size, depending on the file.
[![Screenshot of incomplete template with rounded corners set. Also showing the Configure template button.](/docsassets/guides/create-versatile-templates-part-1/ScreenshotTemplateProgress.png)](/docsassets/guides/create-versatile-templates-part-1/ScreenshotTemplateProgress.png)
### Create an image tag helper
  1. In the _~/Features/Shared/Helpers/TagHelpers_ folder, add a new self-closing TagHelper called `StyledImageTagHelper` with the tag name `tg-styled-image`. 
C#
**StyledImageTagHelper.cs**
Copy
```
...
namespace TrainingGuides.Web.Features.Shared.Helpers.TagHelpers;

[HtmlTargetElement("tg-styled-image", TagStructure = TagStructure.WithoutEndTag)]
public class StyledImageTagHelper : TagHelper
...
```

  2. Follow the same procedure as last time to set up properties and an `IComponentStyleEnumService`, leaving out the `ColorScheme` property. 
C#
**StyledImageTagHelper.cs**
Copy
```
...
namespace TrainingGuides.Web.Features.Shared.Helpers.TagHelpers;

[HtmlTargetElement("tg-styled-image", TagStructure = TagStructure.WithoutEndTag)]
public class StyledImageTagHelper : TagHelper
{
    public string CornerStyle { get; set; } = string.Empty;

    private const string IMG_TAG = "img";

    private readonly IComponentStyleEnumService componentStyleEnumService;
    public StyledImageTagHelper(IComponentStyleEnumService componentStyleEnumService) : base()
    {
        this.componentStyleEnumService = componentStyleEnumService;
    }
...
```

  3. In the `Process` method, set the `TagMode` of the rendered HTML tag to `TagMode.SelfClosing` and style the image according to the classes returned by the service. 
C#
**StyledImageTagHelper.cs**
Copy
```
...
public override void Process(TagHelperContext context, TagHelperOutput output)
{
    output.TagName = IMG_TAG;
    output.TagMode = TagMode.SelfClosing;

    List<string> cssClasses = [];

    var cornerStyle = componentStyleEnumService
        .GetCornerStyle(CornerStyle ?? string.Empty);

    cssClasses.AddRange(componentStyleEnumService
        .GetCornerStyleClasses(cornerStyle));

    if (cssClasses.Count > 0)
    {
        foreach (string cssClass in cssClasses)
        {
            output.AddClass(cssClass, HtmlEncoder.Default);
        }
    }

}
...
```


The resulting tag helper should look like this: 
C#
**StyledImageTagHelper.cs**
Copy
```
using Microsoft.AspNetCore.Mvc.TagHelpers;
using System.Text.Encodings.Web;
using Microsoft.AspNetCore.Razor.TagHelpers;
using TrainingGuides.Web.Features.Shared.Services;

namespace TrainingGuides.Web.Features.Shared.Helpers.TagHelpers;

[HtmlTargetElement("tg-styled-image", TagStructure = TagStructure.WithoutEndTag)]
public class StyledImageTagHelper : TagHelper
{
    public string CornerStyle { get; set; } = string.Empty;

    private const string IMG_TAG = "img";

    private readonly IComponentStyleEnumService componentStyleEnumService;
    public StyledImageTagHelper(IComponentStyleEnumService componentStyleEnumService) : base()
    {
        this.componentStyleEnumService = componentStyleEnumService;
    }

    public override void Process(TagHelperContext context, TagHelperOutput output)
    {
        output.TagName = IMG_TAG;
        output.TagMode = TagMode.SelfClosing;

        List<string> cssClasses = [];

        var cornerStyle = componentStyleEnumService.GetCornerStyle(CornerStyle ?? string.Empty);
        cssClasses.AddRange(componentStyleEnumService.GetCornerStyleClasses(cornerStyle));

        if (cssClasses.Count > 0)
        {
            foreach (string cssClass in cssClasses)
            {
                output.AddClass(cssClass, HtmlEncoder.Default);
            }
        }

    }
}
```

The tag helper can be used in the view like this:
cshtml
**Tag helper usage (e.g., in ProductPagePageTemplate.cshtml)**
Copy
```
<tg-styled-image src="@templateModel.Media.FirstOrDefault()?.FilePath"
    alt="@templateModel.Media.FirstOrDefault()?.Description"
    corner-style="@Model.Properties.CornerStyle"
    class="c-product-img object-fit-cover" />
```

## Complete the template
Comparing the current template to the mockup from the start of this series, you’ll notice that the Product features table is missing.
[![Mockup of a product detail page](/docsassets/guides/create-versatile-templates-part-1/WidgetMockups3.png)](/docsassets/guides/create-versatile-templates-part-1/WidgetMockups3.png)
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
[![Screenshot of unfinished template](/docsassets/guides/create-versatile-templates-part-1/ScreenshotTemplateProgress2.jpg)](/docsassets/guides/create-versatile-templates-part-1/ScreenshotTemplateProgress2.jpg)
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
[![Screenshot of unfinished, but nicely formatted template](/docsassets/guides/create-versatile-templates-part-1/ScreenshotTemplateProgress3.jpg)](/docsassets/guides/create-versatile-templates-part-1/ScreenshotTemplateProgress3.jpg)
## What’s next?
[Part 2 of the _Create versatile templates_ guide](/guides/development/page-builder/create-versatile-templates-part-2) covers the process of adding toggleable _Page Builder_ functionality to this template, so that your editors can customize its content in special cases.
If you started in this guide, we recommend circling back to check out these guides form earlier in the series:
  * [Meet business requirements with Page Builder](/guides/development/page-builder/meet-requirements-with-page-builder), a guide that analyzes how and when to apply Page Builder to business scenarios
  * [Add a custom dropdown provider for administration components](/guides/development/page-builder/map-enum-to-dropdown), a guide that demonstrates the process of creating custom providers that provide options to dropdown menus in Page Builder component properties.


![]()
[]()[]()
