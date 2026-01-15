# Style the template
  * [ Copy page link ](modules/page-builder/style-the-template#) | [Get HelpService ID](modules/page-builder/style-the-template#)
Core MVC 5


[✖](modules/page-builder/style-the-template# "Close page link panel") [Copy to clipboard](modules/page-builder/style-the-template#)
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

  4. Implement the interface, mapping the selected options to sets of CSS classes. Use the `Parse` method from the `EnumStringService` we defined [earlier in the series](modules/page-builder/map-enum-to-dropdown) for the string conversion methods. 
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
[![Screenshot of incomplete template with rounded corners set. Also showing the Configure template button.](docsassets/guides/create-versatile-templates-part-1/ScreenshotTemplateProgress.png)](https://docs.kentico.com/docsassets/guides/create-versatile-templates-part-1/ScreenshotTemplateProgress.png)
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

[ Previous page ](modules/page-builder/define-template-properties)
6 of 16
[ Mark complete and continue ](modules/page-builder/finish-the-structured-data-template)
  * [Community Questions & Answers](https://community.kentico.com/q-and-a)
  * [Contact support](https://community.kentico.com/support)


### Cookie consent
We use necessary [cookies](https://www.kentico.com/cookies-policy) to run our website and improve your experience while browsing. Additional cookies are only used with your consent. You may revoke your consent on the [Cookies Policy](https://www.kentico.com/cookies-policy) page or in your browser at any time. 
ACCEPT ALL  [Configure](https://www.kentico.com/cookies-policy)
USE ONLY NECESSARY 
![](https://docs.kentico.com/modules/page-builder/style-the-template)
[](https://docs.kentico.com/modules/page-builder/style-the-template)[](https://docs.kentico.com/modules/page-builder/style-the-template)