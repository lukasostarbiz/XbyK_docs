# Model a product page template
  * [ Copy page link ](modules/page-builder/model-template-data#) | [Get HelpService ID](modules/page-builder/model-template-data#)
Core MVC 5


[✖](modules/page-builder/model-template-data# "Close page link panel") [Copy to clipboard](modules/page-builder/model-template-data#)
In this guide, we’ll walk through the process of creating a page template to match this product detail mockup:
[![Mockup of a product detail page](docsassets/guides/create-versatile-templates-part-1/WidgetMockups3.png)](https://docs.kentico.com/docsassets/guides/create-versatile-templates-part-1/WidgetMockups3.png)
See the [first guide in this series](modules/page-builder/meet-requirements-with-page-builder) for an overview of the mockup, and a breakdown of how to meet business requirements with [Page Builder](documentation/developers-and-admins/development/builders/page-builder).
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
`ProductFeature` is a generated class, created by the Xperience [code generation tool](documentation/developers-and-admins/api/generate-code-files-for-system-objects). 
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



[ Previous page ](modules/page-builder/map-enum-to-dropdown)
4 of 16
[ Mark complete and continue ](modules/page-builder/define-template-properties)
  * [Community Questions & Answers](https://community.kentico.com/q-and-a)
  * [Contact support](https://community.kentico.com/support)


### Cookie consent
We use necessary [cookies](https://www.kentico.com/cookies-policy) to run our website and improve your experience while browsing. Additional cookies are only used with your consent. You may revoke your consent on the [Cookies Policy](https://www.kentico.com/cookies-policy) page or in your browser at any time. 
ACCEPT ALL  [Configure](https://www.kentico.com/cookies-policy)
USE ONLY NECESSARY 
![](https://docs.kentico.com/modules/page-builder/model-template-data)
[](https://docs.kentico.com/modules/page-builder/model-template-data)[](https://docs.kentico.com/modules/page-builder/model-template-data)