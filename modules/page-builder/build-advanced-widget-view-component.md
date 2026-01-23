---
source: https://docs.kentico.com/modules/page-builder/build-advanced-widget-view-component
scrape_date: 2026-01-22
---

Module: Page Builder
14 of 16 Pages
# Build the advanced widget's view component
A widget’s view component is often the core of its functionality.
The view component contains the business logic that supplies all the values for the view model, usually by interacting with the [Xperience API](/documentation/developers-and-admins/api), or some external endpoint. It often uses widget properties to parameterize API interactions based on input from the editors.
  1. Use an identifier constant to **register the widget**.
  2. If you have any properties that are meant to change the data source of the widget, add conditional code that reacts to them accordingly.
  3. If you have properties meant to affect how the widget interacts with APIs, make sure to take them into account.
For example, you may have a property to narrow the results of a query. 
  4. Appraise your existing tools, such as services and extensions, to share code across your project and avoid redundancy.
If your application does not have any reusable resources that apply, and the widget shares functionality with other parts of your application, consider making new shared code. 
  5. Pass along display-related properties that are handled in the view, while processing display-related properties that are not handled by the view.


C#
**ProductWidgetViewComponent.cs**
Copy
```
using CMS.Helpers;
using Kentico.PageBuilder.Web.Mvc;
using Microsoft.AspNetCore.Mvc;
using Microsoft.AspNetCore.Mvc.ViewComponents;
using Microsoft.IdentityModel.Tokens;
using TrainingGuides.Web.Features.Products.Models;
using TrainingGuides.Web.Features.Products.Services;
using TrainingGuides.Web.Features.Products.Widgets.Product;
using TrainingGuides.Web.Features.Shared.Models;
using TrainingGuides.Web.Features.Shared.OptionProviders.CornerStyle;
using TrainingGuides.Web.Features.Shared.Services;

// Register the widget
[assembly: RegisterWidget(
    identifier: ProductWidgetViewComponent.IDENTIFIER,
    viewComponentType: typeof(ProductWidgetViewComponent),
    name: "Product",
    propertiesType: typeof(ProductWidgetProperties),
    Description = "Displays selected product.",
    IconClass = "icon-ribbon")]

namespace TrainingGuides.Web.Features.Products.Widgets.Product;

public class ProductWidgetViewComponent : ViewComponent
{
    // Identifier constant
    public const string IDENTIFIER = "TrainingGuides.ProductWidget";
    private const string BS_DROP_SHADOW_CLASS = "shadow";
    private const string BS_MARGIN_CLASS = "m-3";
    private const string BS_PADDING_CLASS_3 = "p-3";
    private const string BS_PADDING_CLASS_5 = "p-5";

    private readonly IContentItemRetrieverService contentItemRetrieverService;
    private readonly IComponentStyleEnumService componentStyleEnumService;
    private readonly IProductPageService productPageService;
    public ProductWidgetViewComponent(
        IContentItemRetrieverService contentItemRetrieverService,
        IComponentStyleEnumService componentStyleEnumService,
        IProductPageService productPageService)    {
        this.contentItemRetrieverService = contentItemRetrieverService;
        this.componentStyleEnumService = componentStyleEnumService;
        this.productPageService = productPageService;
    }

    public async Task<ViewViewComponentResult> InvokeAsync(ProductWidgetProperties properties)
    {
        var model = await GetProductWidgetViewModel(properties);
        return View("~/Features/Products/Widgets/Product/ProductWidget.cshtml", model);
    }

    private async Task<ProductWidgetViewModel> GetProductWidgetViewModel(ProductWidgetProperties properties)
    {
        if (properties == null)
            return new ProductWidgetViewModel();

        var productPageViewModel = await GetProductPageViewModel(properties);

        return new ProductWidgetViewModel()
        {
            Product = productPageViewModel,
            ShowProductFeatures = properties.ShowProductFeatures,
            ProductImage = properties.ShowProductImage
                ? productPageViewModel?.Media.FirstOrDefault() ?? new AssetViewModel()
                : new AssetViewModel(),
            ShowAdvanced = properties.ShowAdvanced,
            ColorScheme = properties.ColorScheme,
            CornerStyle = properties.CornerStyle,
            ParentElementCssClasses = GetParentElementCssClasses(properties).Join(" "),
            MainContentElementCssClasses = GetMainContentElementCssClasses(properties).Join(" "),
            ImageElementCssClasses = properties.ShowProductImage ? GetImageElementCssClasses(properties).Join(" ") : string.Empty,
            IsImagePositionSide = IsImagePositionSide(properties.ImagePosition),
            CallToActionCssClasses = componentStyleEnumService
                .GetColorSchemeClasses(componentStyleEnumService.GetLinkStyle(properties.CallToActionStyle ?? string.Empty))
                .Join(" ")
        };
    }

    // Conditionally retrieve the widget's data source (current page or selected page)
    private async Task<ProductPage?> GetProductPage(ProductWidgetProperties properties)
    {
        ProductPage? productPage;

        if (properties.Mode.Equals(ProductWidgetMode.CURRENT_PAGE))
        {
            productPage = await contentItemRetrieverService.RetrieveCurrentPage<ProductPage>(3);
        }
        else
        {
            // Use properties that affect how the widget interacts with the API
            var guid = properties.SelectedProductPage?.Select(webPage => webPage.Identifier).FirstOrDefault();

            productPage = guid.HasValue
                ? await contentItemRetrieverService.RetrieveWebPageByContentItemGuid<ProductPage>(
                    (Guid)guid,
                    4)
                : null;
        }

        int? productContentTypeId = contentTypeService.GetContentTypeId(ProductPage.CONTENT_TYPE_NAME);

        return productPage?.SystemFields.ContentItemContentTypeID == productContentTypeId
            ? productPage
            : null;
    }

    private async Task<ProductPageViewModel?> GetProductPageViewModel(ProductWidgetProperties properties)
    {
        var productPage = await GetProductPage(properties);

        if (!properties.PageAnchor.IsNullOrEmpty())
            properties.PageAnchor = properties.PageAnchor!.StartsWith('#') ? properties.PageAnchor : $"#{properties.PageAnchor}";

        return productPage != null
            ? await productPageService.GetProductPageViewModel(
                productPage: productPage,
                // Pass along display-related properties to the view
                getMedia: properties.ShowProductImage,
                getFeatures: properties.ShowProductFeatures,
                getBenefits: properties.ShowProductBenefits,
                callToAction: properties.CallToAction,
                callToActionLink: properties.PageAnchor,
                openInNewTab: properties.OpenInNewTab,
                getPrice: properties.ShowProductFeatures)
            : null;
    }

    //Process display-related properties that are not handled by the view
    private List<string> GetParentElementCssClasses(ProductWidgetProperties properties)
    {
        const string PARENT_ELEMENT_BASE_CLASS = "tg-product";
        const string LAYOUT_FULL_WIDTH_CLASS = "tg-layout-full-width";
        const string LAYOUT_IMAGE_LEFT_CLASS = "tg-layout-image-left";
        const string LAYOUT_IMAGE_RIGHT_CLASS = "tg-layout-image-right";
        const string LAYOUT_ASCENDING_CLASS = "tg-layout-ascending";
        const string LAYOUT_DESCENDING_CLASS = "tg-layout-descending";

        List<string> cssClasses = [PARENT_ELEMENT_BASE_CLASS];

        if (properties.ShowProductImage)
        {
            string imagePositionCssClass = properties.ImagePosition switch
            {
                nameof(ImagePositionOption.Left) => LAYOUT_IMAGE_LEFT_CLASS,
                nameof(ImagePositionOption.Right) => LAYOUT_IMAGE_RIGHT_CLASS,
                nameof(ImagePositionOption.Ascending) => LAYOUT_ASCENDING_CLASS,
                nameof(ImagePositionOption.Descending) => LAYOUT_DESCENDING_CLASS,
                nameof(ImagePositionOption.FullWidth) => LAYOUT_FULL_WIDTH_CLASS,
                _ => LAYOUT_FULL_WIDTH_CLASS
            };
            cssClasses.Add(imagePositionCssClass);

            if (IsImagePositionFullSize(properties.ImagePosition))
            {
                cssClasses.Add(BS_MARGIN_CLASS);

                cssClasses.AddRange(
                    componentStyleEnumService.GetCornerStyleClasses(
                        componentStyleEnumService.GetCornerStyle(properties.CornerStyle!)));

                if (properties.DropShadow)
                    cssClasses.Add(BS_DROP_SHADOW_CLASS);
            }
        }

        return cssClasses;
    }

    private List<string> GetMainContentElementCssClasses(ProductWidgetProperties properties)
    {
        const string MAIN_CONTENT_BASE_CSS_CLASS = "tg-product_main";
        const string ROUND_CORNERS_BOTTOM_ONLY_CLASS = "bottom-only";
        const string TEXT_ALIGN_LEFT_CLASS = "align-left";
        const string TEXT_ALIGN_CENTER_CLASS = "align-center";
        const string TEXT_ALIGN_RIGHT_CLASS = "align-right";

        List<string> cssClasses = [MAIN_CONTENT_BASE_CSS_CLASS];

        cssClasses.AddRange(GetChildElementCssClasses(properties));

        if (properties.ShowProductImage)
        {
            if (IsImagePositionSide(properties.ImagePosition))
                cssClasses.Add(BS_PADDING_CLASS_5);
            else
                cssClasses.Add(BS_PADDING_CLASS_3);

            if (IsImagePositionFullSize(properties.ImagePosition)
                && HasRoundCorners(properties.CornerStyle))
            {
                cssClasses.Add(ROUND_CORNERS_BOTTOM_ONLY_CLASS);
            }
        }

        string textAlignmentClass = properties.TextAlignment switch
        {
            nameof(ContentAlignmentOption.Left) => TEXT_ALIGN_LEFT_CLASS,
            nameof(ContentAlignmentOption.Center) => TEXT_ALIGN_CENTER_CLASS,
            nameof(ContentAlignmentOption.Right) => TEXT_ALIGN_RIGHT_CLASS,
            _ => TEXT_ALIGN_LEFT_CLASS
        };

        cssClasses.Add(textAlignmentClass);
        return cssClasses;
    }

    private List<string> GetImageElementCssClasses(ProductWidgetProperties properties)
    {
        const string IMAGE_BASE_CSS_CLASS = "tg-product_img";
        const string ROUND_CORNERS_TOP_ONLY_CLASS = "top-only";
        List<string> imageLeftRightClasses = ["tg-col", "c-product-img", "object-fit-cover"];

        List<string> cssClasses = [IMAGE_BASE_CSS_CLASS];

        cssClasses.AddRange(GetChildElementCssClasses(properties));

        if (IsImagePositionSide(properties.ImagePosition))
        {
            cssClasses.AddRange(imageLeftRightClasses);
        }

        if (IsImagePositionFullSize(properties.ImagePosition)
        && HasRoundCorners(properties.CornerStyle))
        {
            cssClasses.Add(ROUND_CORNERS_TOP_ONLY_CLASS);
        }

        return cssClasses;
    }

    private List<string> GetChildElementCssClasses(ProductWidgetProperties properties)
    {
        List<string> cssClasses = [];

        if (!IsImagePositionFullSize(properties.ImagePosition))
        {
            if (properties.DropShadow)
                cssClasses.Add(BS_DROP_SHADOW_CLASS);

            cssClasses.Add(BS_MARGIN_CLASS);
        }
        return cssClasses;
    }

    private bool IsImagePositionFullSize(string imagePosition) =>
        Equals(imagePosition, nameof(ImagePositionOption.FullWidth));

    private bool IsImagePositionSide(string imagePosition) =>
        Equals(imagePosition, nameof(ImagePositionOption.Left)) || Equals(imagePosition, nameof(ImagePositionOption.Right));

    private bool HasRoundCorners(string cornerStyle) =>
        Equals(cornerStyle, nameof(CornerStyleOption.Round)) || Equals(cornerStyle, nameof(CornerStyleOption.VeryRound));
}
```

If your project has a dedicated class for component identifiers, add the identifier for your widget.
C#
**ComponentIdentifiers.cs**
Copy
```
...
public static class ComponentIdentifiers
{
    public static class Sections
    {
        ...
    }

    public static class Widgets
    {
        ...
        //Include the widget in the component identifiers list
        public const string PRODUCT = ProductWidgetViewComponent.IDENTIFIER;
        ...
    }
}
```

## Document your widget
When you develop a new widget, make sure you train your editors about how to use it, and create documentation for it. Things that seem self-explanatory to development teams may not be so clear to non-technical users, so it’s important to give editors and other business users instructions on how to use what you make for them.
## See the results
If you’ve followed along with this series about Page Builder, you can now use the [General template](/modules/page-builder/model-template-data), [General section](/modules/page-builder/implement-flexible-sections), [Simple CTA widget](/modules/page-builder/build-simple-cta-widget), and Product widget to recreate the pages depicted in the mockups.
[![Mockup of a promotional page](/docsassets/guides/define-advanced-widget/WidgetMockups1.png)](/docsassets/guides/define-advanced-widget/WidgetMockups1.png)
[![Screenshot of a promotional page](/docsassets/guides/define-advanced-widget/ScreenshotPromotionalPage1.png)](/docsassets/guides/define-advanced-widget/ScreenshotPromotionalPage1.png)
[![Mockup of a promotional page](/docsassets/guides/define-advanced-widget/WidgetMockups2.png)](/docsassets/guides/define-advanced-widget/WidgetMockups2.png)
[![Screenshot of a promotional page](/docsassets/guides/define-advanced-widget/ScreenshotPromotionalPage2.png)](/docsassets/guides/define-advanced-widget/ScreenshotPromotionalPage2.png)
[![Mockup of a product detail page](/docsassets/guides/define-advanced-widget/WidgetMockups3.png)](/docsassets/guides/define-advanced-widget/WidgetMockups3.png)
[![Screenshot of a product page](/docsassets/guides/define-advanced-widget/ScreenshotProductPage.png)](/docsassets/guides/define-advanced-widget/ScreenshotProductPage.png)
You’re welcome to tweak the styling to get the pages to appear even closer to the mockups. You can also apply your knowledge to add additional configurations to the template, section, and widgets.
Additionally, the [finished branch of our Training guides repository](https://github.com/Kentico/xperience-by-kentico-training-guides/tree/finished) has several additional widgets that you can look over and modify for your own purposes.
[ Previous page ](/modules/page-builder/set-up-advanced-widget)
14 of 16
[ Mark complete and continue ](/modules/page-builder/localize-advanced-widget)
![]()
[]()[]()
