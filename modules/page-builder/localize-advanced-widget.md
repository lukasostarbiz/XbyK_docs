---
source: https://docs.kentico.com/modules/page-builder/localize-advanced-widget
scrape_date: 2026-01-26
---

Module: Page Builder
15 of 16 Pages
# Localize the widget (optional)
If you are [localizing your Admin UI](/documentation/developers-and-admins/customization/admin-ui-localization) into other languages, you can localize the widget’s name, description, and property labels so editors see them in their preferred language.
The localization process has two parts: first, you’ll replace hardcoded strings in your code with localization expressions. Then, you’ll define the actual translations in resource (`.resx`) files and register them with Xperience, as described in the [Admin UI localization documentation](/documentation/developers-and-admins/customization/admin-ui-localization#register-localized-resources).
For a complete example of a localized widget, see the [CallToAction sample widget in the finished branch](https://github.com/Kentico/xperience-by-kentico-training-guides/tree/finished/src/TrainingGuides.Web/Features/LandingPages/Widgets/CallToAction) of the Training guides repository.
### Localize widget properties
To localize the widget’s configuration interface, update widget property annotations to use [localization expressions](/documentation/developers-and-admins/customization/admin-ui-localization#admin-ui-fields) in the format `{$ resource.key $}`.
Here’s an example showing how to localize the `Label` and `ExplanationText` for one property in the Product widget:
C#
**ProductWidgetProperties.cs (localized example)**
Copy
```
using ...

namespace TrainingGuides.Web.Features.Products.Widgets.Product;

public class ProductWidgetProperties : IWidgetProperties
{
    ...
    [ContentItemSelectorComponent(
        ProductPage.CONTENT_TYPE_NAME,
        Label = "{$TrainingGuides.ProductWidget.SelectedProductPage.Label$}",
        ExplanationText = "{$TrainingGuides.ProductWidget.SelectedProductPage.ExplanationText$}",
        MaximumItems = 1,
        Order = 30)]
    public IEnumerable<ContentItemReference> SelectedProductPage { get; set; } = [];
    ...
}
 
 


```

You’ll need to apply this pattern to all properties in the class that have user-facing strings.
### Localize widget registration
Next, localize how the widget appears in the Page Builder widget selector. Use localization expressions in the widget registration attribute for the widget’s name and description:
C#
**ProductWidgetViewComponent.cs (localized registration)**
Copy
```
using ...

[assembly: RegisterWidget(
    identifier: ProductWidgetViewComponent.IDENTIFIER,
    viewComponentType: typeof(ProductWidgetViewComponent),
    name: "{$TrainingGuides.ProductWidget.Name$}",
    propertiesType: typeof(ProductWidgetProperties),
    Description = "{$TrainingGuides.ProductWidget.Description$}",
    IconClass = "icon-ribbon")]

namespace TrainingGuides.Web.Features.Products.Widgets.Product;

public class ProductWidgetViewComponent : ViewComponent
{
    ...
 



 


```

### Define resource strings
After adding localization expressions to your code, create the corresponding resource strings in `.resx` files. For example:
XML
**LocalizationCustom.en-US.resx**
Copy
```
...
<data name="TrainingGuides.ProductWidget.Name">
  <value>Product</value>
</data>
<data name="TrainingGuides.ProductWidget.SelectedProductPage.Label">
  <value>Select product page</value>
</data>
...
```

**Use AI agents to accelerate localization**
AI assistants like GitHub Copilot can significantly speed up the localization process. For example:
  * Manually write the localization expression for the first property, then let Copilot infer the pattern for the remaining properties.
  * Use a prompt like the following to generate both the localized code and resource file entries:


Text
Copy
```
For this widget properties class, perform two tasks:

1) Replace all hardcoded Label, ExplanationText, and Options strings with localization expressions in the format {$TrainingGuides.ProductWidget.{PropertyName}.{ParameterType}$} (e.g., {$TrainingGuides.ProductWidget.SelectedProductPage.Label$}).

2) Generate a .resx file with XML entries for each replaced string, using the original hardcoded values. Include entries from [RadioGroupComponent], [ContentItemSelectorComponent], [CheckBoxComponent], [DropDownComponent], and [TextInputComponent] attributes.

Provide both the updated C# code and the complete .resx XML content.
```

This approach reduces repetitive work while maintaining consistency across your localized content.
Note that this is just an example prompt. Adjust it to match your project’s naming conventions and specific needs, and provide the AI agent with the relevant code context.
**We recommend using the[Xperience by Kentico Documentation MCP server](/documentation/developers-and-admins/installation/mcp-server) to help AI agents better understand Xperience widgets and localization patterns.**
[ Previous page ](/modules/page-builder/build-advanced-widget-view-component)
15 of 16
[ Mark complete and continue ](/modules/page-builder/page-builder-learn-more)
![]()
[]()[]()
