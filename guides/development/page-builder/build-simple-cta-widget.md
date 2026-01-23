---
source: https://docs.kentico.com/guides/development/page-builder/build-simple-cta-widget
scrape_date: 2026-01-22
---

  * [Home](/guides)
  * [Development](/guides/development)
  * [Page Builder](/guides/development/page-builder)
  * Build a simple call-to-action widget 


# Build a simple call-to-action widget
[Widgets](/documentation/developers-and-admins/development/builders/page-builder/widgets-for-page-builder) are Page Builder components of the smallest granularity. While [sections, discussed previously](/guides/development/page-builder/implement-flexible-sections), serve primarily to change the page layout and appearance, widgets are a tool for your editors to display and style structured data or take input from the live site visitor.
Next, let’s create a simple [widget based on a view component](/documentation/developers-and-admins/development/builders/page-builder/widgets-for-page-builder#widgets-based-on-a-view-component) - a **Simple call to action (CTA) widget**.
Editors can use the widget anywhere they need a stand-alone CTA button. For example, the “Contact us” or the “View downloads” button from the mockups we talked about in the [beginning of this Page Builder series](/guides/development/page-builder/meet-requirements-with-page-builder):
[![Mockup of a promotional page with Simple CTA widgets highlighted](/docsassets/guides/build-simple-cta-widget/WidgetMockups2-simple-CTA.png)](/docsassets/guides/build-simple-cta-widget/WidgetMockups2-simple-CTA.png)
## Prerequisites
This guide assumes you have:
  * basic knowledge of C# and .NET framework concepts
  * familiarity with the basics of [_Page Builder_](/documentation/developers-and-admins/development/builders/page-builder) in Xperience by Kentico


To follow along with the example in this guide, the main branch of our [Training guides repository](https://github.com/Kentico/xperience-by-kentico-training-guides) is a great starting point. If you have already been using the repository to follow along with earlier guides in this series, you are at the perfect spot.
Our .NET solution contains two projects. In this guide we will be working solely in the _TrainingGuides.Web_ , so you can assume that being the root at all times.
If you are working on your own project, make sure your instance of Xperience by Kentico is [version 30.11.1 or higher](/guides/development/get-started/install-a-specific-version-of-xperience-by-kentico).
The code samples in this guide are for .NET 8 only.
To see finished implementation of Simple CTA widget, check out [this folder in the finished branch of our repository](https://github.com/Kentico/xperience-by-kentico-training-guides/tree/finished/src/TrainingGuides.Web/Features/LandingPages/Widgets/SimpleCallToAction).
If you browse the repository, you may notice another Call to action widget, similar to this Simple CTA. It is different example of the CTA button, as seen in the Kbank demo site, unrelated to this guide.
## Understand the requirements
Editor working with the Simple CTA widget should be able to:
  * set the call to action text (e.g., **Contact us**)
  * configure whether the button click navigates to a page or an external link
  * select an existing page, or paste in an external link to navigate to
  * choose whether or not the target opens in a new browser tab

Your browser does not support the video tag. 
## Implement the widget
From an editor’s perspective, the widget has two parts:
  * **UI component** - the button that renders on the page.
  * **configurable properties** - a form the editor can access by clicking on the gear icon of the widget


From a developer’s perspective, the widget will consist of four files:
  * **properties** - defines the configurable properties form, for the editor to interact with
  * **view component** - defines how the property values affect the widget behavior and how they map to the widget view model
  * **view model** - the data model for the widget view to render
  * **view** - razor view of the widget. It renders in the result webpage for visitors. Editors can also see it in the Page Builder view and page preview in the Xperience administration.


Start by preparing your folder structure:
  1. Create a new _Widgets_ folder inside _~/Features/LandingPages_. Here you can place any future widgets related to the Landing pages feature.
  2. Inside the _Widgets_ folder, create a _SimpleCallToAction_ folder to hold your new widget’s files.


### Define widget properties
Based on our [specification](/guides/development/page-builder/build-simple-cta-widget#understand-the-requirements), the Simple CTA button needs five configurable properties for the editor:
  1. **Call to action text** - a text field to enter the button text
  2. **Target content** - a selector for the editor to chose type of target (page or an external link)
  3. **Target page** - a selector, shown only if the editor choses to target page
  4. **Absolute URL** - a text field to paste in target URL, shown only if the editor targets external link
  5. **Open in new tab** - a checkbox to open the target in the new tab


To define the configurable properties, we will use the set of existing [Xperience admin UI form components](/documentation/developers-and-admins/customization/extend-the-administration-interface/ui-form-components/reference-admin-ui-form-components).
  1. First, create a _SimpleCallToActionWidgetProperties.cs_ file in your _SimpleCallToAction_ folder, to hold your property definitions. 
Based on this file, Xperience will know how to render the UI controls in the administration interface.
  2. Define a `SimpleCallToActionWidgetProperties` class that extends `IWidgetProperties`. 
The `IWidgetProperties` interface comes with the Xperience by Kentico out-of-box.
  3. Inside the class, define your properties. They will hold the values of your future UI controls: 
C#
**SimpleCallToActionWidgetProperties.cs**
Copy
```
using System.ComponentModel;
using CMS.ContentEngine;
using Kentico.PageBuilder.Web.Mvc;
using Kentico.Xperience.Admin.Base.FormAnnotations;

using TrainingGuides.Web.Features.Shared.OptionProviders;

namespace TrainingGuides.Web.Features.LandingPages.Widgets.SimpleCallToAction;

public class SimpleCallToActionWidgetProperties : IWidgetProperties
{
    public string Text { get; set; } = string.Empty;

    public string TargetContent { get; set; } = nameof(TargetContentOption.Page);
    public IEnumerable<ContentItemReference> TargetContentPage { get; set; } = [];
    public string TargetContentAbsoluteUrl { get; set; } = string.Empty;
    public bool OpenInNewTab { get; set; } = false;
}
```



Next, we will add the UI form components. To do this, you need to decorate each property with an attribute, based on what [component](/documentation/developers-and-admins/customization/extend-the-administration-interface/ui-form-components/reference-admin-ui-form-components) you wish to render. Let’s look at the properties one by one.
#### Call to action text
Use `TextInputComponent` attribute, to render a [text input](/documentation/developers-and-admins/customization/extend-the-administration-interface/ui-form-components/reference-admin-ui-form-components#text-input).
C#
Copy
```
[TextInputComponent(
    Label = "Call to action text",
    ExplanationText = "Add your call to action. Keep it under 30 characters.",
    Order = 10)]
public string Text { get; set; } = string.Empty;
```

Take a closer look at the parameters:
  * `Label` displays before the input field.
  * `ExplanationText` displays underneath as an additional description for the editor.
  * `Order` determines the placing of the UI control on the form.


Always aim to provide a meaningful label for your UI controls. Based on your use case, consider adding a concise and relevant explanation text as well.
#### Target content
Editors will use this property to decide the type of target: **page** or **absolute URL**.
At the moment, you only need two types, but it is likely you will have to add more options over time (e.g., asset). Choosing a [dropdown selector component](/documentation/developers-and-admins/customization/extend-the-administration-interface/ui-form-components/reference-admin-ui-form-components#dropdown-selector) for this property ensures you can expand the options later, without changing the UI.
Another advantage of a dropdown component is that it supports dynamic options mapping, as described [earlier in this series](/guides/development/page-builder/map-enum-to-dropdown).
  1. Decorate the **TargetContent** property with the `DropDownComponent` attribute.
  2. Set `Label`, `ExplanationText` and `Order`.
  3. Fill the dropdown with data.
In this guide, we are using the `DropdownEnumOptionProvider` from [earlier in this series](/guides/development/page-builder/map-enum-to-dropdown), which is the recommended way.
Alternatively, you can skip sub-steps _a._ through _c._ and assign dropdown options as a string instead: `Options = "Page;Page\r\nAbsoluteUrl;Absolute URL"`
    1. Define a new enumeration in the _SimpleCallToActionWidgetProperties.cs_ file, underneath the `SimpleCallToActionWidgetProperties` class:
C#
**SimpleCallToActionWidgetProperties.cs**
Copy
```
...
public class SimpleCallToActionWidgetProperties : IWidgetProperties
{
    ...
}

public enum TargetContentOption
{
    [Description("Page")]
    Page,
    [Description("Absolute URL")]
    AbsoluteUrl
}
```

    2. Assign `DataProviderType`, using the `DropdownEnumOptionProvider` and your new `TargetContentOption` enumeration.
    3. Make `TargetContentOption.Page` the default value.
C#
Copy
```
[DropDownComponent(
    Label = "Target content",
    ExplanationText = "Select what happens when a visitor clicks your button.",
    DataProviderType = typeof(DropdownEnumOptionProvider<TargetContentOption>),
    Order = 20)]
public string TargetContent { get; set; } = nameof(TargetContentOption.Page);
```



#### Target page
  1. Use a [combined content selector](/documentation/developers-and-admins/customization/extend-the-administration-interface/ui-form-components/reference-admin-ui-form-components#combined-content-selector) for **Target page**.
  2. Specify the content type names of page types you want to allow.
  3. Set `MaximumItems = 1` to allow editor only pick **one page at the time**.
  4. Add a [visibility condition](/documentation/developers-and-admins/customization/extend-the-administration-interface/ui-form-components/ui-form-component-visibility-conditions) (`VisibleIfEqualTo`) to ensure, that **Target page** property only renders if the editor set the **Target content** to `TargetContentOption.Page`.


C#
Copy
```
[ContentItemSelectorComponent(
    [
        ArticlePage.CONTENT_TYPE_NAME,
        DownloadsPage.CONTENT_TYPE_NAME,
        EmptyPage.CONTENT_TYPE_NAME,
        LandingPage.CONTENT_TYPE_NAME,
        ProductPage.CONTENT_TYPE_NAME,
        ProfilePage.CONTENT_TYPE_NAME
    ],
    Label = "Target page",
    ExplanationText = "Select the page in the tree.",
    MaximumItems = 1,
    Order = 30)]
[VisibleIfEqualTo(nameof(TargetContent), nameof(TargetContentOption.Page), StringComparison.OrdinalIgnoreCase)]
public IEnumerable<ContentItemReference> TargetContentPage { get; set; } = [];
```

#### Absolute URL
  1. Like the **Call to action text** above, make the **Absolute URL** property a [text input](/documentation/developers-and-admins/customization/extend-the-administration-interface/ui-form-components/reference-admin-ui-form-components#text-input).
  2. Add a `VisibleIfEqualTo` [visibility condition](/documentation/developers-and-admins/customization/extend-the-administration-interface/ui-form-components/ui-form-component-visibility-conditions) to only show the field if the editor sets the **Target content** to `TargetContentOption.AbsoluteUrl`.


C#
Copy
```
[TextInputComponent(
    Label = "Absolute URL",
    ExplanationText = "Add a hyperlink to an external site, or use the product's URL + anchor tag # for referencing an anchor on the page, for example, \"https://your-doma.in/contact-us#form\"",
    Order = 40)]
[VisibleIfEqualTo(nameof(TargetContent), nameof(TargetContentOption.AbsoluteUrl), StringComparison.OrdinalIgnoreCase)]
public string TargetContentAbsoluteUrl { get; set; } = string.Empty;
```

#### Open in new tab
Our last property is a [checkbox component](/documentation/developers-and-admins/customization/extend-the-administration-interface/ui-form-components/reference-admin-ui-form-components) with a `bool` value. Set its default value to unchecked (`false`). This way, unless the editor changes it, the target will open in the same browser tab.
C#
Copy
```
[CheckBoxComponent(
    Label = "Open in new tab",
    Order = 50)]
public bool OpenInNewTab { get; set; } = false;
```

Here is the complete _SimpleCallToActionWidgetProperties.cs_ file for your reference:
C#
**SimpleCallToActionWidgetProperties.cs**
Copy
```
using System.ComponentModel;
using CMS.ContentEngine;
using Kentico.PageBuilder.Web.Mvc;
using Kentico.Xperience.Admin.Base.FormAnnotations;
using TrainingGuides.Web.Features.Shared.OptionProviders;

namespace TrainingGuides.Web.Features.LandingPages.Widgets.SimpleCallToAction;

public class SimpleCallToActionWidgetProperties : IWidgetProperties
{
    [TextInputComponent(
        Label = "Call to action text",
        ExplanationText = "Add your call to action. Keep it under 30 characters.",
        Order = 10)]
    public string Text { get; set; } = string.Empty;

    // See the same property implemented using RadioGroupComponent instead in CallToAction widget.
    [DropDownComponent(
        Label = "Target content",
        ExplanationText = "Select what happens when a visitor clicks your button.",
        DataProviderType = typeof(DropdownEnumOptionProvider<TargetContentOption>),
        Order = 20)]
    public string TargetContent { get; set; } = nameof(TargetContentOption.Page);

    [ContentItemSelectorComponent(
        [
            ArticlePage.CONTENT_TYPE_NAME,
            DownloadsPage.CONTENT_TYPE_NAME,
            EmptyPage.CONTENT_TYPE_NAME,
            LandingPage.CONTENT_TYPE_NAME,
            ProductPage.CONTENT_TYPE_NAME,
            ProfilePage.CONTENT_TYPE_NAME
        ],
        Label = "Target page",
        ExplanationText = "Select the page in the tree.",
        MaximumItems = 1,
        Order = 30)]
    [VisibleIfEqualTo(nameof(TargetContent), nameof(TargetContentOption.Page), StringComparison.OrdinalIgnoreCase)]
    public IEnumerable<ContentItemReference> TargetContentPage { get; set; } = [];

    [TextInputComponent(
        Label = "Absolute URL",
        ExplanationText = "Add a hyperlink to an external site, or use the product's URL + anchor tag # for referencing an anchor on the page, for example, \"https://your-doma.in/contact-us#form\"",
        Order = 40)]
    [VisibleIfEqualTo(nameof(TargetContent), nameof(TargetContentOption.AbsoluteUrl), StringComparison.OrdinalIgnoreCase)]
    public string TargetContentAbsoluteUrl { get; set; } = string.Empty;

    [CheckBoxComponent(
        Label = "Open in new tab",
        Order = 50)]
    public bool OpenInNewTab { get; set; } = false;
}

public enum TargetContentOption
{
    [Description("Page")]
    Page,
    [Description("Absolute URL")]
    AbsoluteUrl
}
```

### Implement widget component
The next step is to define the behavior and visual design of the widget. Let’s implement widget view model, view and view component.
[![Simple CTA button component](/docsassets/guides/build-simple-cta-widget/simple-cta-component.png)](/docsassets/guides/build-simple-cta-widget/simple-cta-component.png)
#### Create view model
  1. Create a new `SimpleCallToActionWidgetViewModel` class in your _SimpleCallToAction_ folder.
  2. From the UI perspective, the widget needs only three properties: 
     * `Text`: call to action text to render (e.g., Contact us)
     * `Url`: where navigate after button click
     * `OpenInNewTab`: how to open the target Url


C#
**SimpleCallToActionWidgetViewModel.cs**
Copy
```
namespace TrainingGuides.Web.Features.LandingPages.Widgets.SimpleCallToAction;

public class SimpleCallToActionWidgetViewModel
{
    public string Text { get; set; } = string.Empty;
    public string Url { get; set; } = string.Empty;
    public bool OpenInNewTab { get; set; } = false;
}
```

Notice that the view model properties don’t exactly mirror the widget properties. The purpose of the widget properties is to gather data from editors. These go through processing in the view component and the view model should include only data that is going to be displayed in the resulting view.
For example, in our case, there is no need for two separate properties for a page and an external link in the view model. The button only needs a target URL to navigate to.
#### Create view
  1. Add new view file, called _SimpleCallToActionWidget.cshtml_ , in your _SimpleCallToAction_ folder.
  2. Set your [`SimpleCallToActionWidgetViewModel`](/guides/development/page-builder/build-simple-cta-widget#create-view-model) as the `model`.
cshtml
**SimpleCallToActionWidget.cshtml**
Copy
```
@using TrainingGuides.Web.Features.LandingPages.Widgets.SimpleCallToAction
@model SimpleCallToActionWidgetViewModel
```

  3. Handle the error state of the widget. When either the `Text`, `Url` or the whole `Model` are undefined, let’s show a message to tell the editor something is wrong. Remember, you don’t want to show the message to the website visitor:
cshtml
**SimpleCallToActionWidget.cshtml**
Copy
```
...
<!-- render this part only if the widget is misconfigured -->
@if (Model == null || string.IsNullOrWhiteSpace(Model.Text)
    || string.IsNullOrWhiteSpace(Model.Url))
{
    <!-- display the message **only** if the page is in edit, readonly or preview mode in the Xperience administration-->
    @if (Context.Kentico().PageBuilder().GetMode() == PageBuilderMode.Edit
        || Context.Kentico().PageBuilder().GetMode() == PageBuilderMode.ReadOnly
        || Context.Kentico().Preview().Enabled)
    {
        <p>
            This widget needs some setup. Click the <strong>Configure widget</strong> gear icon in the top right to configure content and design for this widget.
        </p>
    }

    return;
}
```

As you create more widgets in your project, you’ll notice the error handling in widget views is more or less the same. We recommend extracting the functionality, using for example tag helpers (see [the SimpleCallToActionWidget view in the finished branch of our repository](https://github.com/Kentico/xperience-by-kentico-training-guides/blob/finished/src/TrainingGuides.Web/Features/LandingPages/Widgets/SimpleCallToAction/SimpleCallToActionWidget.cshtml)).
  4. Handle the valid state of the widget and render the button using the values from the model:
cshtml
**SimpleCallToActionWidget.cshtml**
Copy
```
...
@if (Model != null)
{
    <div class="text-center">
        <a href="@Model.Url" class="btn tg-btn-secondary text-uppercase my-4" target="@(Model.OpenInNewTab ? "_blank" : "")">
            @Model.Text
        </a>
    </div>
}
```



This code sample takes advantage of classes that exist in our Training guides repository, to style the link as a button. Feel free to reuse them or play around with your own styling.
If you are following along, your _SimpleCallToActionWidget.cshtml_ should look like this:
cshtml
**SimpleCallToActionWidget.cshtml**
Copy
```
@using TrainingGuides.Web.Features.LandingPages.Widgets.SimpleCallToAction
@model SimpleCallToActionWidgetViewModel

@if (Model == null || string.IsNullOrWhiteSpace(Model.Text)
    || string.IsNullOrWhiteSpace(Model.Url))
{
    @if (Context.Kentico().PageBuilder().GetMode() == PageBuilderMode.Edit
        || Context.Kentico().PageBuilder().GetMode() == PageBuilderMode.ReadOnly
        || Context.Kentico().Preview().Enabled)
    {
        <p>
            This widget needs some setup. Click the <strong>Configure widget</strong> gear icon in the top right to configure content and design for this widget.
        </p>
    }

    return;
}
@if (Model != null)
{
    <div class="text-center">
        <a href="@Model.Url"
        class="btn tg-btn-secondary text-uppercase my-4"
        target="@(Model.OpenInNewTab ? "_blank" : "")">
            @Model.Text
        </a>
    </div>
}
```

#### Create view component
  1. Add a new _SimpleCallToActionWidgetViewComponent.cs_ file in your _SimpleCallToAction_ folder.
  2. Create a class with the same name that extends the .NET `ViewComponent` class.
  3. Create an `InvokeAsync` method that 
     * takes `SimpleCallToActionWidgetProperties` as a parameter
     * creates an instance of [your view model](/guides/development/page-builder/build-simple-cta-widget#create-view-model) and fills it with data, based on the values in `properties`
     * returns [your widget view](/guides/development/page-builder/build-simple-cta-widget#create-view) and passes in the model
C#
**SimpleCallToActionWidgetViewComponent.cs**
Copy
```
using CMS.ContentEngine;
using Kentico.PageBuilder.Web.Mvc;
using Microsoft.AspNetCore.Mvc;
using Microsoft.AspNetCore.Mvc.ViewComponents;
using TrainingGuides.Web.Features.LandingPages.Widgets.CallToAction;
using TrainingGuides.Web.Features.LandingPages.Widgets.SimpleCallToAction;
using TrainingGuides.Web.Features.Shared.Services;

namespace TrainingGuides.Web.Features.LandingPages.Widgets.SimpleCallToAction;

public class SimpleCallToActionWidgetViewComponent : ViewComponent
{
    private readonly IContentItemRetrieverService contentItemRetrieverService;

    // use dependency injection to access content retrieval service
    public SimpleCallToActionWidgetViewComponent(IContentItemRetrieverService contentItemRetrieverService)
    {
        this.contentItemRetrieverService = contentItemRetrieverService;
    }

    public async Task<ViewViewComponentResult> InvokeAsync(SimpleCallToActionWidgetProperties properties)
    {

        // figure out what the target URL should be
        // if the editor sets TargetContent to *Page*, retrieve the page URL, using the GetWebPageUrl function (defined below)
        // if the editor sets TargetContent to *AbsoluteUrl*, simply read it from the properties parameter
        string targetUrl = properties.TargetContent switch
        {
            nameof(TargetContentOption.Page) => await GetWebPageUrl(properties.TargetContentPage?.FirstOrDefault()) ?? string.Empty,
            nameof(TargetContentOption.AbsoluteUrl) => properties.TargetContentAbsoluteUrl,
            _ => string.Empty
        };

        // fill the model with data:
        // pass along the call to action Text from *properties*
        // set the *targetUrl* retrieved above
        // pass along the *OpenInNewTab* from *properties*
        var model = new SimpleCallToActionWidgetViewModel()
        {
            Text = properties.Text,
            Url = targetUrl,
            OpenInNewTab = properties?.OpenInNewTab ?? false,
        };

        return View("~/Features/LandingPages/Widgets/SimpleCallToAction/SimpleCallToACtionWidget.cshtml", model);
    }

    // get the URL of a web page retrieved from a combined content selector control
    private async Task<string?> GetWebPageUrl(ContentItemReference? webPage)
    {
        if (webPage is not null)
        {
            var page = await contentItemRetrieverService.RetrieveWebPageByContentItemGuid(webPage.Identifier);
            return page?.GetUrl()?.RelativePath ?? string.Empty;
        }
        return string.Empty;
    }
}
```

Notice the `GetWebPageUrl` method above.
When an editor selects a page using the [combined content selector](/documentation/developers-and-admins/customization/extend-the-administration-interface/ui-form-components/reference-admin-ui-form-components#combined-content-selector), the value comes to the view component as a `ContentItemReference` object. Because the object doesn’t contain the page URL, we retrieve the page content item using the `RetrieveWebPageByContentItemGuid` method of our existing [ContentItemRetrieverService](https://github.com/Kentico/xperience-by-kentico-training-guides/blob/main/src/TrainingGuides.Web/Features/Shared/Services/ContentItemRetrieverService.cs).
The method’s implementation uses the [`IPreferredLanguageRetriever`](/documentation/developers-and-admins/development/content-retrieval/retrieve-page-content#access-current-preferred-language) to ensure the content item data is accurate for the [current site language](/guides/development/multilingual/display-your-website-content-in-multiple-languages).
Once we have the page, we can simply call its `GetUrl` extension method.


## Register the widget
The last piece of the puzzle is to [register your new widget](/documentation/developers-and-admins/development/builders/page-builder/widgets-for-page-builder#register-widgets) so the Xperience recognizes it.
  1. First, your widget needs an identifier. Add is as a constant in your `SimpleCallToActionWidgetViewComponent` class:
C#
**SimpleCallToActionWidgetViewComponent.cs**
Copy
```
...
public class SimpleCallToActionWidgetViewComponent : ViewComponent
{
    public const string IDENTIFIER = "TrainingGuides.SimpleCallToActionWidget";
    private readonly IContentItemRetrieverService contentItemRetrieverService;
    ...
}
 


```

  2. Use the `RegisterWidget` assembly attribute to register the widget, using a newly defined `IDENTIFIER`.
C#
**SimpleCallToActionWidgetViewComponent.cs**
Copy
```
...
using Kentico.PageBuilder.Web.Mvc;
using TrainingGuides.Web.Features.LandingPages.Widgets.CallToAction;
using TrainingGuides.Web.Features.LandingPages.Widgets.SimpleCallToAction;

[assembly:
RegisterWidget(
    identifier: SimpleCallToActionWidgetViewComponent.IDENTIFIER,
    viewComponentType: typeof(SimpleCallToActionWidgetViewComponent),
    name: "Simple call to action",
    propertiesType: typeof(SimpleCallToActionWidgetProperties),
    Description = "Displays a call to action button.",
    IconClass = "icon-bubble")]
...
 
 
 
 
 
 
 
 


```

See more information and individual parameters explanation in [our documentation page about widget registration](/documentation/developers-and-admins/development/builders/page-builder/widgets-for-page-builder#register-widgets).
Place your registration at the top of your _SimpleCallToActionWidgetViewComponent.cs_ file, after the `using` statements.
Your full _SimpleCallToActionWidgetViewComponent.cs_ file should look like this:
C#
**SimpleCallToActionWidgetViewComponent.cs**
Copy
```
using CMS.ContentEngine;
using Kentico.PageBuilder.Web.Mvc;
using Microsoft.AspNetCore.Mvc;
using Microsoft.AspNetCore.Mvc.ViewComponents;
using TrainingGuides.Web.Features.LandingPages.Widgets.CallToAction;
using TrainingGuides.Web.Features.LandingPages.Widgets.SimpleCallToAction;
using TrainingGuides.Web.Features.Shared.Services;

// register Simple call to action widget
[assembly:
    RegisterWidget(
        identifier: SimpleCallToActionWidgetViewComponent.IDENTIFIER,
        viewComponentType: typeof(SimpleCallToActionWidgetViewComponent),
        name: "Simple call to action",
        propertiesType: typeof(SimpleCallToActionWidgetProperties),
        Description = $"Displays a call to action button. Simpler configuration options than {CallToActionWidgetViewComponent.NAME} widget.",
        IconClass = "icon-bubble")]

namespace TrainingGuides.Web.Features.LandingPages.Widgets.SimpleCallToAction;

public class SimpleCallToActionWidgetViewComponent : ViewComponent
{
    public const string IDENTIFIER = "TrainingGuides.SimpleCallToActionWidget";

    private readonly IContentItemRetrieverService contentItemRetrieverService;

    public SimpleCallToActionWidgetViewComponent(IContentItemRetrieverService contentItemRetrieverService)
    {
        this.contentItemRetrieverService = contentItemRetrieverService;
    }

    public async Task<ViewViewComponentResult> InvokeAsync(SimpleCallToActionWidgetProperties properties)
    {

        string targetUrl = properties.TargetContent switch
        {
            nameof(TargetContentOption.Page) => await GetWebPageUrl(properties.TargetContentPage?.FirstOrDefault()) ?? string.Empty,
            nameof(TargetContentOption.AbsoluteUrl) => properties.TargetContentAbsoluteUrl,
            _ => string.Empty
        };

        var model = new SimpleCallToActionWidgetViewModel()
        {
            Text = properties.Text,
            Url = targetUrl,
            OpenInNewTab = properties?.OpenInNewTab ?? false,
        };

        return View("~/Features/LandingPages/Widgets/SimpleCallToAction/SimpleCallToACtionWidget.cshtml", model);
    }

    private async Task<string?> GetWebPageUrl(ContentItemReference? webPage)
    {
        if (webPage is not null)
        {
            var page = await contentItemRetrieverService.RetrieveWebPageByContentItemGuid(webPage.Identifier);
            return page?.GetUrl()?.RelativePath ?? string.Empty;
        }
        return string.Empty;
    }
}
```



### Expose the widget identifier for future reference
There will be cases in your project when you need to reference the widget by its identifier. For example, when restricting widgets allowed in a particular [widget zone](/documentation/developers-and-admins/development/reference-tag-helpers#widget-zone).
For this purpose we recommend storing references to all your widgets in one place. In our _TrainingGuides.Web_ project, we keep all Widget identifiers as constants inside a static class called `ComponentIdentifiers`.
  1. Navigate to _ComponentIdentifiers.cs_ file in the root of the _TrainingGuides.Web_ project.
  2. Locate the `Widgets` class inside the `ComponentIdentifiers` class.
  3. Add a constant referencing `SimpleCallToActionWidgetViewComponent.IDENTIFIER`.
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
        public const string SIMPLE_CALL_TO_ACTION = SimpleCallToActionWidgetViewComponent.IDENTIFIER;
    }
}
```



Now you can reference Simple call to action widget anywhere in your project as `ComponentIdentifiers.Widgets.SIMPLE_CALL_TO_ACTION`.
For example, if you followed [the flexible section example from earlier in this series](/guides/development/page-builder/implement-flexible-sections#create-a-section-view), you can pass something like this into the `editable-area-options` in the view:
cshtml
Copy
```
@new EditableAreaOptions()
{
    AllowedWidgets = [ComponentIdentifiers.Widgets.SIMPLE_CALL_TO_ACTION]
}
```

## See the button in action
Now that the work is done, it’s time to build your project and enjoy the fruits of your labor. If your implementation is correct and complete, you should be able to sign in to the administration interface and add a Simple CTA button to the Home page, as you can see in this video.
Your browser does not support the video tag. 
If you are not seeing your widget as an option, double-check [your widget registration](/guides/development/page-builder/build-simple-cta-widget#register-the-widget) is correct.
If you are getting an error after adding the widget, see the **Event log** under **Development** in menu on the left for more information.
## What’s next?
[The next guide in this series](/guides/development/page-builder/define-advanced-widget) will explain how to create a more advanced widget, demonstrating their true power.
![]()
[]()[]()
