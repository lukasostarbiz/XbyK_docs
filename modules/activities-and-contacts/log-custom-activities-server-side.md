---
source: https://docs.kentico.com/modules/activities-and-contacts/log-custom-activities-server-side
scrape_date: 2026-01-22
---

Module: Activities and contacts
5 of 9 Pages
# Log custom activities from the server side
The next custom activity scenario we will explore is a _Page like_ widget demonstrating server-side logging with C# code. You will implement a controller action that logs a custom activity, and a widget that posts to the controller.
### Define a request model
Create a strongly-typed class to represent the data that the widget will post to the controller, to make working with the data easier. In this case, you only need information to identify which page was liked, and to retrieve the correct item, so the Id of the web page content item, and the name of the content type should suffice.
  1. Add a new folder named  _PageLike_ under _TrainingGuides.Web/Features/Activities/Widgets._
  2. Create a class named _PageLikeRequestModel.cs_ with a property to identify the web page item.


C#
**PageLikeRequestModel.cs**
Copy
```
namespace TrainingGuides.Web.Features.Activities.Widgets.PageLike;

public class PageLikeRequestModel
{
    public string WebPageItemID { get; set; } = string.Empty;
}
```

### Examine the content item retriever service
You may have noticed that the [content item query api](/documentation/developers-and-admins/development/content-retrieval/retrieve-page-content) requires a strongly typed mapping function in order to select the results of the query.
However, since you’re building a widget, you may not know the content type of the web page item that your widget is placed on.
In order to account for this, you need a way to retrieve a web page item by ID when you don’t necessarily know its content type. For this, you can use the `IWebPageFieldsSource` interface that all generated web page content types implement.
  1. Go to the `ContentItemRetrieverService` class in _ContentItemRetrieverService.cs_.
  2. Notice the method called `RetrieveWebPageById`, taking the Id of a web page and optional language name as parameters.
  3. The method uses the ID to create a parameter action for another existing `RetrieveWebPages` method. 
C#
**ContentItemRetrieverService.cs**
Copy
```
...
/// <inheritdoc />
public async Task<IWebPageFieldsSource?> RetrieveWebPageById(
    int webPageItemId,
    int depth = 2,
    string? languageName = null)
{
    var pages = await RetrieveWebPages(parameters =>
        {
            parameters.Where(where => where.WhereEquals(nameof(WebPageFields.WebPageItemID), webPageItemId));
        },
        depth,
        languageName ?? preferredLanguageRetriever.Get());

    return pages.FirstOrDefault();
}
...
```



We are using the Content item retrieval API instead of ContentRetriever API.
As an alternative approach you could use the ContentRetriever API, but because of its limitations, this would require listing all possible page content type code names, as the page types only share an interface, not a base class. See comment at the `RetrieveWebPageByContentItemGuid` method in the [finished branch of our Training guides repo](https://github.com/Kentico/xperience-by-kentico-training-guides/blob/finished/src/TrainingGuides.Web/Features/Shared/Services/ContentItemRetrieverService.cs).
### Create the logging controller
The controller is central to the functionality of the _Page like_ widget. It is where you must log the custom activity and account for any errors.
  1. Add a new controller named _PageLikeController.cs_ to the _~/Features/Activities/Widgets/PageLike_ folder.
  2. Acquire an `ICustomActivityLogger`, an `IContentItemRetrieverService`, and an `ICookieConsentService` through constructor injection.
  3. Define a controller action with the `HttpPost` attribute to register it to the route _~/pagelike_ for POST requests.
  4. Use the `CurrentContactCanBeTracked` method from [earlier in this series](/modules/activities-and-contacts/enable-activity-tracking) to return a message if the visitor has not consented to tracking.
  5. Validate the `WebPageItemID` from the provided `PageLikeRequestModel`.
  6. Use your `IContentItemRetrieverService` to retrieve the web page item specified by the supplied Id.
  7. Use the page to construct a `CustomActivityData` object, including relevant information about the liked page in the `ActivityTitle` and `ActivityValue`, before logging the activity [as described in the documentation](/documentation/developers-and-admins/digital-marketing-setup/set-up-activities/custom-activities#server-side-code).
  8. Include an activity identifier constant, which will be stored in the view component created in a [future step](#add-the-widget-view-model).


C#
**PageLikeController.cs**
Copy
```
using CMS.Activities;
using Microsoft.AspNetCore.Mvc;
using TrainingGuides.Web.Features.DataProtection.Services;
using TrainingGuides.Web.Features.Shared.Services;

namespace TrainingGuides.Web.Features.Activities.Widgets.PageLike;


public class PageLikeController : Controller
{
    private const string NO_TRACKING_MESSAGE = "<span>You have not consented to tracking, so we cannot save this page like.</span>";
    private const string BAD_PAGE_DATA_MESSAGE = "<span>Error in page like data. Please try again later.</span>";
    private const string THANK_YOU_MESSAGE = "<span>Thank you!</span>";

    private readonly ICustomActivityLogger customActivityLogger;
    private readonly IContentItemRetrieverService contentItemRetrieverService;
    private readonly ICookieConsentService cookieConsentService;

    public PageLikeController(
        ICustomActivityLogger customActivityLogger,
        IContentItemRetrieverService contentItemRetrieverService,
        ICookieConsentService cookieConsentService)
    {
        this.customActivityLogger = customActivityLogger;
        this.contentItemRetrieverService = contentItemRetrieverService;
        this.cookieConsentService = cookieConsentService;
    }

    [HttpPost("/pagelike")]
    public async Task<IActionResult> PageLike(PageLikeRequestModel requestModel)
    {
        if (!cookieConsentService.CurrentContactCanBeTracked())
            return Content(NO_TRACKING_MESSAGE);

        if (!int.TryParse(requestModel.WebPageItemID, out int webPageItemID))
            return Content(BAD_PAGE_DATA_MESSAGE);

        var webPage = await contentItemRetrieverService.RetrieveWebPageById(
            webPageItemID);

        if (webPage is null)
            return Content(BAD_PAGE_DATA_MESSAGE);

        string likedPageName = webPage.SystemFields.WebPageItemName;
        string likedPageTreePath = webPage.SystemFields.WebPageItemTreePath;
        string likedPageGuid = webPage.SystemFields.WebPageItemGUID.ToString();

        var pageLikeActicityData = new CustomActivityData()
        {
            ActivityTitle = $"Page like - {likedPageTreePath} ({likedPageName})",
            ActivityValue = likedPageGuid,
        };

        customActivityLogger.Log(PageLikeWidgetViewComponent.ACTIVITY_IDENTIFIER, pageLikeActicityData);
        return Content(THANK_YOU_MESSAGE);
    }
}
```

### Add the widget view model
The _Page like_ widget will have a relatively simple view model, considering its basic requirements.
  * The widget needs to post the _WebPageItemID_ to the controller action from the previous step.
  * The widget should hide its button if the current visitor already liked the page in the past.


To meet both of these requirements, create a model with the Id and a boolean property to indicate whether or not the button should be displayed, as well as the base URL of the site, so the widget can construct the URL of the controller action.
C#
**PageLikeWidgetViewModel.cs**
Copy
```
namespace TrainingGuides.Web.Features.Activities.Widgets.PageLike;

public class PageLikeWidgetViewModel
{
    public bool ShowLikeButton { get; set; }
    public int WebPageItemID { get; set; }
    public string BaseUrl { get; set; } = string.Empty;
}
```

### Define the view component
The widget’s view component needs to populate the view model. It must retrieve the ID of the current web page item, and also determine whether or not the current visitor has already liked the page, in order to pass that information along to the widget.
  1. Create a file called _PageLikeWidgetViewComponent.cs_ in the _TrainingGuides.Web/Features/Activities/Widgets/PageLike_ folder.
  2. Use the `RegisterWidget` assembly attribute to register the widget, using a newly defined constant that holds the widget identifier.
  3. Add another constant to hold the identifier of the page like activity.
  4. Acquire `IInfoProvider<ActivityInfo>` and `IContentItemRetrieverService` objects with constructor injection.
  5. Define the `InvokeAsync` method, using `IInfoProvider<ActivityInfo>` to query for existing page-like activities of the current web page item by the current contact.
Remember that [in the controller](#create-the-logging-controller), you stored the Guid of the current web page to the custom activity’s `ActivityValue`. You can use this field to look up likes of the current page.
  6. If no existing likes are found, set `ShowLikeButton` to `true` in a new `PageLikeWidgetViewModel` instance.
  7. Use the `Page` property of the provided `ComponentViewModel` parameter to populate the remaining properties of the `PageLikeWidgetViewModel`.
  8. Return the path to a view in the same folder, which will be added in the next section.


C#
**PageLikeWidgetViewComponent.cs**
Copy
```
using CMS.Activities;
using CMS.ContactManagement;
using TrainingGuides.Web.Features.Activities.Widgets.PageLike;
using Kentico.PageBuilder.Web.Mvc;
using Microsoft.AspNetCore.Mvc;
using Microsoft.AspNetCore.Mvc.ViewComponents;
using TrainingGuides.Web.Features.Shared.Services;
using CMS.DataEngine;

[assembly: RegisterWidget(
    identifier: PageLikeWidgetViewComponent.IDENTIFIER,
    viewComponentType: typeof(PageLikeWidgetViewComponent),
    name: "Page like button",
    Description = "Displays a page like button.",
    IconClass = "icon-check-circle")]

namespace TrainingGuides.Web.Features.Activities.Widgets.PageLike;

public class PageLikeWidgetViewComponent : ViewComponent
{
    private readonly IInfoProvider<ActivityInfo> activityInfoProvider;
    private readonly IContentItemRetrieverService contentItemRetrieverService;
    private readonly IHttpRequestService httpRequestService;

    public const string IDENTIFIER = "TrainingGuides.PageLikeWidget";
    public const string ACTIVITY_IDENTIFIER = "pagelike";

    public PageLikeWidgetViewComponent(IInfoProvider<ActivityInfo> activityInfoProvider,
        IContentItemRetrieverService contentItemRetrieverService,
        IHttpRequestService httpRequestService)
    {
        this.activityInfoProvider = activityInfoProvider;
        this.contentItemRetrieverService = contentItemRetrieverService;
        this.httpRequestService = httpRequestService;
    }

    public async Task<ViewViewComponentResult> InvokeAsync(ComponentViewModel properties)
    {
        var currentContact = ContactManagementContext.GetCurrentContact(false);

        var webPage = await contentItemRetrieverService.RetrieveWebPageById(
            properties.Page.WebPageItemID);

        var likesOfThisPage = currentContact != null
            ? await activityInfoProvider.Get()
                .WhereEquals("ActivityContactID", currentContact.ContactID)
                .And().WhereEquals("ActivityType", ACTIVITY_IDENTIFIER)
                .And().WhereEquals("ActivityValue", webPage?.SystemFields.WebPageItemGUID.ToString())
                .GetEnumerableTypedResultAsync()
            : [];

        bool showLikeButton = likesOfThisPage.Count() == 0;

        var model = new PageLikeWidgetViewModel()
        {
            ShowLikeButton = showLikeButton,
            WebPageItemID = properties.Page.WebPageItemID,
            BaseUrl = httpRequestService.GetBaseUrl()
        };

        return View("~/Features/Activities/Widgets/PageLike/PageLikeWidget.cshtml", model);
    }
}
```

### Make the identifier available
In the future, you may need to limit which widgets are available in different Page Builder sections and zones. To make this easier, add the page like widget’s identifier to the `ComponentIdentifiers` class.
  1. Navigate to _TrainingGuides.Web/ComponentIdentifiers.cs._
  2. Add a new static class called `Widgets` inside the `ComponentIdentifiers` class if it does not already exist.
  3. Add a constant string that is equal to that of the page like widget view component.


C#
**ComponentIdentifiers.cs**
Copy
```
public static class ComponentIdentifiers
{
    ...
    public static class Widgets
    {
        ...
        public const string PAGE_LIKE = PageLikeWidgetViewComponent.IDENTIFIER;
        ...
    }
    ...
}
```

### Add the widget view
The last piece you need to add is the view file referenced by the view component.
  1. If you didn’t follow along with the [Data protection series](/guides/development/data-protection) yet, add a dependency on the _AspNetCore.Unobtrusive.Ajax_ NuGet Package, and add the following line to the part of _Program.cs_ where services are added to the application builder.
C#
**Program.cs**
Copy
```
...
builder.Services.AddUnobtrusiveAjax();
...
```

  2. Add a view file called  _PageLikeWidget.cshtml_ to the _TrainingGuides.Web/Features/Activities/Widgets/PageLike_ folder.
  3. Create an AJAX form that posts to the path of the controller action from earlier.
    1. Pass the Id of a div for the AJAX form to write its response.
    2. Use a hidden input to pass the web page item’s Id to the controller action when the widget submits the form.


cshtml
**PageLikeWidget.cshtml**
Copy
```
@using TrainingGuides.Web.Features.Activities.Widgets.PageLike;

@model PageLikeWidgetViewModel

@{
    var messageId = "pageLikeMessage";
}

@if(Model.ShowLikeButton || Context.Kentico().Preview().Enabled)
{
    @using (Html.AjaxBeginForm("PageLike", "PageLike", new AjaxOptions
     {
         HttpMethod = "POST",
         InsertionMode = InsertionMode.Replace,
         UpdateTargetId = messageId
     }, new { action = $"{Model.BaseUrl}/pagelike" }))
    {
        <div class="container">
            <div class="cookie-preferences js-cookie-preferences">
                <input id="WebPageItemID" name="WebPageItemID" type="hidden" value="@Model.WebPageItemID" />
                <button class="btn btn-secondary text-uppercase mt-4 cookie-preferences__button" type="submit" name="button">
                    Like this page
                </button>
                <div id="@messageId" class="cookie-preferences__message"></div>
            </div>
        </div>
    }
}
```

The coding is done, so you can test your new widget.
  1. Run the _TrainingGuides.Web_ project locally.
  2. Sign in to the administration interface, and open the **Training guides pages** channel.
  3. Choose **Edit → Create new version** the _Cookie policy_ page, and add an instance of the _Page like_ widget to the widget zone.
  4. Save and publish the page, then do the same for the _Contact us_ page.
  5. Visit the _/cookie-policy_ path on the live site.
  6. Make sure your cookie level is set to _Marketing_ , and click the like button.
  7. Return to the administration interface, opening the **Activities** tab of the **Contact management** application to see that Xperience has logged the _Page like_ activity.

Your browser does not support the video tag. 
[ Previous page ](/modules/activities-and-contacts/log-custom-activities-client-side)
5 of 9
[ Mark complete and continue ](/modules/activities-and-contacts/hide-builder-content-from-visitors-who-have-not-consented-to-tracking)
![]()
[]()[]()
