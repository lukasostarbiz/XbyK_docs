---
source: https://docs.kentico.com/modules/developer-kickstart/build-a-page-template
scrape_date: 2026-01-26
---

Module: Xperience by Kentico Developer kickstart
7 of 13 Pages
# Build a page template
We recommend using [Page templates](/documentation/developers-and-admins/development/builders/page-builder/page-templates-for-page-builder) to display content in website projects. They enable flexibility in the presentation of a page without developer intervention – you can set up multiple templates for editors to choose from, and include properties that allow them to configure the design.
Now that we have created a _Landing page_ content type and configured the project, let’s create a simple template to display landing pages. The template will access fields from the reusable [_Slogan_](/modules/developer-kickstart/create-a-reusable-content-type) item linked by the Landing page. 
In the _Kickstart.Web_ project, create a directory _Features_ , with a subfolder _LandingPages_ for code files relating to landing pages.
## Add a view model class
Let’s start by defining a view model class to hold the data the template should display.
Add a new class named _LandingPageViewModel_ to the _LandingPages_ folder, with a `Message` property to represent some text that should be displayed on the page.
Then add a `GetViewModel` method that takes a `LandingPage` as the parameter. It should use the `SloganText` of the linked `Slogan` item for the `Message` property.
C#
**LandingPageViewModel.cs**
Copy
```
using System.Linq;

namespace Kickstart.Web.Features.LandingPages;

public class LandingPageViewModel
{
    public string Message { get; set; } = string.Empty;

    public static LandingPageViewModel GetViewModel(LandingPage landingPage) =>
        new()
        {
            Message = landingPage?.LandingPageSlogan.FirstOrDefault()?.SloganText ?? string.Empty
        };
}
```

## Define a page template controller
Page templates use a `ComponentViewModel` object as a model by default. This is useful for accessing any page template properties you may have set up, but adds an extra step to accessing content item data.
Let’s create a controller that allows you to substitute your own `LandingPageViewModel` instead, so that you can use it directly in the view.
### Add the class
Create a new class called `LandingPageController` in the _Features/LandingPages_ folder of _Kickstart.Web_.
Then add an asynchronous `Index` action for the controller, which returns a `TemplateResult` object.
C#
**LandingPageController.cs**
Copy
```
using System.Threading.Tasks;
using Kentico.PageBuilder.Web.Mvc.PageTemplates;

using Microsoft.AspNetCore.Mvc;

namespace Kickstart.Web.Features.LandingPages;

public class LandingPageController : Controller
{
    public async Task<IActionResult> Index()
    {
        return new TemplateResult();
    }
}
```

### Query the landing page
To retrieve the data of a given landing page, we’ll use the [ContentRetriever API](/documentation/developers-and-admins/api/content-item-api/content-retriever-api).
Inject the content retriever service into your class.
Utilize its `RetrieveCurrentPage` method to fetch the data of your current _Landing page_. This method will automatically find the page associated with the request route, retrieve the data in the current language, and map it to a `LandingPage` object in code.
Once you retrieve the landing page data, you can use the `GetViewModel` method from earlier in this step to create a `LandingPageViewModel`.
C#
**LandingPageController.cs**
Copy
```
using System.Threading.Tasks;

using Kentico.Content.Web.Mvc;
using Kentico.PageBuilder.Web.Mvc.PageTemplates;

using Microsoft.AspNetCore.Mvc;

namespace Kickstart.Web.Features.LandingPages;

public class LandingPageController : Controller
{
    private readonly IContentRetriever contentRetriever;

    public LandingPageController(
        IContentRetriever contentRetriever) => this.contentRetriever = contentRetriever;

    public async Task<IActionResult> Index()
    {
        var parameters = new RetrieveCurrentPageParameters
        {
            LinkedItemsMaxLevel = 1
        };
        var page = await contentRetriever.RetrieveCurrentPage<LandingPage>(parameters);
        var model = LandingPageViewModel.GetViewModel(page);
        return new TemplateResult(model);
    }
}
```

### Register the controller
Now we need to tell Xperience to use this template whenever someone requests a page of the `LandingPage` type.
Use the `RegisterWebPageRoute` assembly attribute to create this association.
Thanks to this attribute, the [content tree-based router](/documentation/developers-and-admins/development/routing/content-tree-based-routing) (which you configured in the previous step) knows to use the controller.
You don’t need to set up any kind of routing table that references the controller.
C#
**LandingPageController.cs**
Copy
```
using Kentico.Content.Web.Mvc.Routing;
using Kickstart;

[assembly: RegisterWebPageRoute(
    contentTypeName: LandingPage.CONTENT_TYPE_NAME,
    controllerType: typeof(Kickstart.Web.Features.LandingPages.LandingPageController))]

namespace Kickstart.Web.Features.LandingPages;
...
```

The completed file should look [like this code](https://github.com/Kentico/xperience-by-kentico-kickstart/blob/main/src/Kickstart.Web/Features/LandingPages/LandingPageController.cs) in the Kickstart repository.
**Refactoring**
The code samples here are meant to demonstrate important principles, but they are not production-ready.
In real-world scenarios, you will need to retrieve various content items frequently in your project. We recommend extracting content querying functionality into a separate service to make your code more modular and readable, as in [in this example](https://github.com/Kentico/xperience-by-kentico-training-guides/blob/main/src/TrainingGuides.Web/Features/Shared/Services/ContentItemRetrieverService.cs).
Depending on the size and lifetime of projects, you may also want to consider more complex [enterprise architecture](/guides/development/get-started/use-abstraction-and-enterprise-level-architecture-in-xperience-projects) with a higher degree of abstraction.
## Add the page template view
With the view model and controller in place, let’s move on to the view.
In the _Features/LandingPages_ folder, create an empty Razor view called _LandingPagePageTemplate.cshtml_.
Designate the view’s model as `TemplateViewModel`. Then retrieve the `LandingPageViewModel` object from the controller by calling `Model.GetTemplateModel`.
Finally, display the model’s message and add a Page Builder editable area.
cshtml
**LandingPagePageTemplate.cshtml**
Copy
```
@using Kickstart.Web.Features.LandingPages

@model TemplateViewModel

@{
    var templateModel = Model.GetTemplateModel<LandingPageViewModel>();
}

<h3>@templateModel.Message</h3>

<editable-area area-identifier="areaMain" />
```

The `TemplateViewModel` lives in the `Kentico.Content.Web.Mvc.PageBuilder` namespace. Notice that we don’t need a `using` directive here because we added it to __ViewImports.cshtml_ in the [previous step](/modules/developer-kickstart/configure-the-project#create-a-viewimports-file).
## Register the page template
Now the landing page template exists in code, but Xperience cannot use it unless you register it.
In the current state, when execution hits your controller, Xperience will not know which view to use when the `Index` action returns `TemplateResult`.
To fix this, let’s create a new file to represent the template itself.
### Set up the template file
Add a new file to the _Kickstart.Web/Features/LandingPages_ folder named _LandingPagePageTemplate.cs_.
Define a class of the same name and add a string constant to represent a unique identifier for the template.
C#
**LandingPagePageTemplate.cs**
Copy
```
namespace Kickstart.Web.Features.LandingPages;
public static class LandingPagePageTemplate
{
    public const string IDENTIFIER = "Kickstart.LandingPagePageTemplate";
}
```

### Add the registration attribute
Now we can use the `IDENTIFIER` constant to register the page template with the Xperience API.
Use the `RegisterPageTemplate` attribute, which takes the following parameters:
  * The identifier of the template, which Xperience will use when serializing template data in the database.
  * The display name for the template in the admin UI.
  * The path to the template’s view file.
  * A list of content types that the template supports.
  * An icon to represent the template in the admin UI.


If you don’t provide a list of allowed types, the template is allowed for all web page content types by default.
C#
**LandingPagePageTemplate.cs**
Copy
```
using Kentico.PageBuilder.Web.Mvc.PageTemplates;

using Kickstart;
using Kickstart.Web.Features.LandingPages;

[assembly: RegisterPageTemplate(
    identifier: LandingPagePageTemplate.IDENTIFIER,
    name: "Landing page content type template",
    customViewName: "~/Features/LandingPages/LandingPagePageTemplate.cshtml",
    ContentTypeNames = [LandingPage.CONTENT_TYPE_NAME],
    IconClass = "xp-market")]

namespace Kickstart.Web.Features.LandingPages;
public static class LandingPagePageTemplate
{
    public const string IDENTIFIER = "Kickstart.LandingPagePageTemplate";
}
```

Now the system knows that the view from the previous section is a page template. It will be available to any _LandingPage_ pages that are allowed to use page templates.
Learn more about the `RegisterPageTemplate` attribute on [this page of our documentation](/documentation/developers-and-admins/development/builders/page-builder/page-templates-for-page-builder#register-page-templates). 
In the next step, let’s check your progress by creating a homepage that uses this template.
[ Previous page ](/modules/developer-kickstart/configure-the-project)
7 of 13
[ Mark complete and continue ](/modules/developer-kickstart/apply-a-page-template)
![]()
[]()[]()
