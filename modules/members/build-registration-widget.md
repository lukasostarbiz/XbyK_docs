---
source: https://docs.kentico.com/modules/members/build-registration-widget
scrape_date: 2026-01-22
---

Module: Members
3 of 12 Pages
# Build the registration widget
Let’s start the process of building our registration widget.
In the _TrainingGuides.Web_ project, create a _~/Features/Membership/Widgets/Registration_ folder. This will contain the files relating to our registration widget.
### Define the widget properties
When creating a widget, it’s helpful to start by considering what editors should be able to configure.
According to our [requirements](/modules/members/groundwork-registration-widget), editors need the ability to display a minimal registration form with only required fields, or a fully detailed form that includes optional fields. They also need to define the form’s labels.
Let’s say that a _minimal_ registration form contains only **username** , **email** , **password** , and **password confirmation** fields. Add boolean properties to indicate whether to show optional form fields, like those related to the member’s name and favorite coffee.
C#
**RegistrationWidgetProperties.cs**
Copy
```
using Kentico.PageBuilder.Web.Mvc;
using Kentico.Xperience.Admin.Base.FormAnnotations;

namespace TrainingGuides.Web.Features.Membership.Widgets.Registration;

public class RegistrationWidgetProperties : IWidgetProperties
{
    /// <summary>
    /// Determines whether the widget should display the Given name and Family name fields.
    /// </summary>
    [CheckBoxComponent(
        Label = "Show name",
        ExplanationText = "Checkbox that determines if the registration form should display given name and family name fields.",
        Order = 10)]
    public bool ShowName { get; set; } = true;

    /// <summary>
    /// Determines whether the widget should display extra fields.
    /// </summary>
    [CheckBoxComponent(
        Label = "Show extra fields",
        ExplanationText = "Checkbox that determines if the registration form should display extra fields.",
        Order = 20)]
    public bool ShowExtraFields { get; set; } = true;
}
```

Then, add properties where editors can specify the labels for all of the registration form’s fields, as well as the title of the form and the text of the submit button.
C#
**RegistrationWidgetProperties.cs**
Copy
```
...
/// <summary>
/// Form title
/// </summary>
[TextInputComponent(
    Label = "Form title",
    ExplanationText = "Title to display above the registration form.",
    Order = 30)]
public string FormTitle { get; set; } = "Sign up";

/// <summary>
/// Submit button text
/// </summary>
[TextInputComponent(
    Label = "Submit button text",
    ExplanationText = "Text for the button that submits the registration form.",
    Order = 40)]
public string SubmitButtonText { get; set; } = "Submit";

/// <summary>
/// User name label.
/// </summary>
[TextInputComponent(
    Label = "User name label",
    ExplanationText = "Label for the text box where registrants can input their UserName.",
    Order = 50)]
public string UserNameLabel { get; set; } = "User name";

/// <summary>
/// Email address label.
/// </summary>
[TextInputComponent(
    Label = "Email address label",
    ExplanationText = "Label for the text box where registrants can input their email address.",
    Order = 60)]
public string EmailAddressLabel { get; set; } = "Email address";

/// <summary>
/// Password label.
/// </summary>
[TextInputComponent(
    Label = "Password label",
    ExplanationText = "Label for the text box where registrants can input their password.",
    Order = 70)]
public string PasswordLabel { get; set; } = "Password";

/// <summary>
/// Password label.
/// </summary>
[TextInputComponent(
    Label = "Confirm password label",
    ExplanationText = "Label for the text box where registrants can confirm their password.",
    Order = 80)]
public string ConfirmPasswordLabel { get; set; } = "Confirm your password";

/// <summary>
/// Given name label.
/// </summary>
[TextInputComponent(
    Label = "Given name label",
    ExplanationText = "Label for the text box where registrants can input their given name.",
    Order = 90)]
public string GivenNameLabel { get; set; } = "Given name";

/// <summary>
/// Family name label.
/// </summary>
[TextInputComponent(
    Label = "Family name label",
    ExplanationText = "Label for the text box where registrants can input their family name.",
    Order = 100)]
public string FamilyNameLabel { get; set; } = "Family name";

/// <summary>
/// Label for checkbox that indicates that the family name should display first.
/// </summary>
[TextInputComponent(
    Label = "'Family name first' checkbox label",
    ExplanationText = "Label for the checkbox that indicates whether the family name comes before the given name.",
    Order = 110)]
public string FamilyNameFirstLabel { get; set; } = "Family name goes first";

/// <summary>
/// Favorite coffee label.
/// </summary>
[TextInputComponent(
    Label = "Favorite coffee label",
    ExplanationText = "Label for the text box where registrants can input their favorite coffee.",
    Order = 120)]
public string FavoriteCoffeeLabel { get; set; } = "Favorite coffee";
...
```

Setting default values for the labels in the default language will save your editors some time.
### Set up a view model
Now let’s move on to creating a view model for the widget.
We’re going to use .NET [Model validation](https://learn.microsoft.com/en-us/aspnet/core/mvc/models/validation) to handle validation of this form, so we’ll need to include properties for the data that visitors will fill in alongside those for managing the widget’s configuration and display.
It will be useful in other related features (e.g., [member profile page](/guides/development/members/create-a-profile-page)) to have a view model class containing all of the custom fields we added to the _Member_ object type. Let’s create a dedicated class for that and have our widget view model inherit from it. Add a _Profile_ folder under _Membership_ and add analogs of the four custom member fields with data annotations.
C#
**GuidesMemberProfileViewModel.cs**
Copy
```
using System.ComponentModel.DataAnnotations;

namespace TrainingGuides.Web.Features.Membership.Profile;

public class GuidesMemberProfileViewModel
{
    [DataType(DataType.Text)]
    [MaxLength(50)]
    [Display(Name = "Given name")]
    public string GivenName { get; set; } = string.Empty;

    [DataType(DataType.Text)]
    [MaxLength(50)]
    [Display(Name = "Family name")]
    public string FamilyName { get; set; } = string.Empty;

    [Display(Name = "Family name goes first")]
    public bool FamilyNameFirst { get; set; } = false;

    [DataType(DataType.Text)]
    [MaxLength(100)]
    [Display(Name = "Favorite coffee")]
    public string FavoriteCoffee { get; set; } = string.Empty;
}
```

Now, returning to your registration widget folder, add a widget view model that inherits from `GuidesMemberProfileViewModel` and also implements the `IWidgetViewModel` interface.
Create a view model that meets the following requirements:
  1. Include an `ActionUrl` property, which we can use to specify the URL that the registration form should post its data to.
  2. Include a flag to determine whether or not to display the form.
  3. Include properties that correspond to those in _RegistrationWidgetProperties.cs_.
  4. Define and annotate properties for the member’s username, email, password, and password confirmation.
  5. Implement the `IsMisconfigured` property from `IWidgetViewModel`.


C#
**RegistrationWidgetViewModel.cs**
Copy
```
using System.ComponentModel.DataAnnotations;
using Microsoft.AspNetCore.Mvc;
using TrainingGuides.Web.Features.Membership.Profile;
using TrainingGuides.Web.Features.Shared.Models;

namespace TrainingGuides.Web.Features.Membership.Widgets.Registration;

public class RegistrationWidgetViewModel : GuidesMemberProfileViewModel, IWidgetViewModel
{
    //WIDGET DISPLAY PROPERTIES

    /// <summary>
    /// Determines whether the widget is misconfigured.
    /// </summary>
    public bool IsMisconfigured =>
        string.IsNullOrWhiteSpace(ActionUrl)
        || string.IsNullOrWhiteSpace(SubmitButtonText)
        || string.IsNullOrWhiteSpace(UserNameLabel)
        || string.IsNullOrWhiteSpace(EmailAddressLabel)
        || string.IsNullOrWhiteSpace(PasswordLabel)
        || string.IsNullOrWhiteSpace(ConfirmPasswordLabel)
        || (ShowName && (string.IsNullOrWhiteSpace(GivenNameLabel) || string.IsNullOrWhiteSpace(FamilyNameLabel) || string.IsNullOrWhiteSpace(FamilyNameFirstLabel)))
        || (ShowExtraFields && string.IsNullOrWhiteSpace(FavoriteCoffeeLabel));

    /// <summary>
    /// The Action URL of the form.
    /// </summary>
    public string ActionUrl { get; set; } = string.Empty;

    /// <summary>
    /// Form title
    /// </summary>
    public string FormTitle { get; set; } = string.Empty;

    /// <summary>
    /// Determines whether the widget should display the form.
    /// </summary>
    [HiddenInput]
    public bool DisplayForm { get; set; }

    /// <summary>
    /// Determines whether the widget should display name-related fields.
    /// </summary>
    [HiddenInput]
    public bool ShowName { get; set; }

    /// <summary>
    /// Determines whether the widget should display extra fields.
    /// </summary>
    [HiddenInput]
    public bool ShowExtraFields { get; set; }

    /// <summary>
    /// Submit button text
    /// </summary>
    [HiddenInput]
    public string SubmitButtonText { get; set; } = string.Empty;

    /// <summary>
    /// User name label.
    /// </summary>
    [HiddenInput]
    public string UserNameLabel { get; set; } = string.Empty;

    /// <summary>
    /// Email address label.
    /// </summary>
    [HiddenInput]
    public string EmailAddressLabel { get; set; } = string.Empty;

    /// <summary>
    /// Password label.
    /// </summary>
    [HiddenInput]
    public string PasswordLabel { get; set; } = string.Empty;

    /// <summary>
    /// Password label.
    /// </summary>
    [HiddenInput]
    public string ConfirmPasswordLabel { get; set; } = string.Empty;

    /// <summary>
    /// Given name label.
    /// </summary>
    [HiddenInput]
    public string GivenNameLabel { get; set; } = string.Empty;

    /// <summary>
    /// Family name label.
    /// </summary>
    [HiddenInput]
    public string FamilyNameLabel { get; set; } = string.Empty;

    /// <summary>
    /// Label for checkbox that indicates that the family name should display first.
    /// </summary>
    [HiddenInput]
    public string FamilyNameFirstLabel { get; set; } = string.Empty;

    /// <summary>
    /// Favorite coffee label.
    /// </summary>
    [HiddenInput]
    public string FavoriteCoffeeLabel { get; set; } = string.Empty;

    //FORM PROPERTIES

    [DataType(DataType.Text)]
    [Required()]
    [RegularExpression("^[a-zA-Z0-9_\\-\\.]+$", ErrorMessage = "Please enter a valid username.")]
    [MaxLength(100)]
    public string UserName { get; set; } = string.Empty;

    [DataType(DataType.EmailAddress)]
    [Required()]
    [EmailAddress()]
    [MaxLength(254)]
    public string EmailAddress { get; set; } = string.Empty;

    [DataType(DataType.Password)]
    [Required()]
    [MaxLength(100)]
    public string Password { get; set; } = string.Empty;

    [DataType(DataType.Password)]
    [Required()]
    [MaxLength(100)]
    [Compare(nameof(Password))]
    public string ConfirmPassword { get; set; } = string.Empty;

}
```

If you have any model fields which are not displayed in the form, but which must persist through validation, mark them as **hidden inputs**.
The form will post to a controller action that validates and returns the model. Any model values which are not included in the POST request will be null after the partial view with the form fields returns from the controller action.
### Create the view component
The widget’s view component is responsible for initially populating the view model.
You can populate most of the view model’s properties from the widget’s properties, but we need new logic for `ActionUrl`.
#### Assemble a URL for the form
Start with a constant to hold the relative path to the controller action the form will post to. We can use it here, and again in the controller, to centralize the location of the string.
For example, in our Training guides repo, we store such constants in [~/Features/Shared/Helpers/ApplicationConstants.cs](https://github.com/Kentico/xperience-by-kentico-training-guides/blob/finished/src/TrainingGuides.Web/Features/Shared/Helpers/ApplicationConstants.cs).
C#
**~/Features/Shared/Helpers/ApplicationConstants.cs**
Copy
```
namespace TrainingGuides.Web.Features.Shared.Helpers;

internal static class ApplicationConstants
{
    //Multilingual
    public const string LANGUAGE_KEY = "language";

    //Membership
    ...
    public const string REGISTER_ACTION_PATH = "/Registration/Register";
    ...
}
```

Now we need a method to get a full URL based on this relative path. This is something that can be useful in many places, so let’s add a new method called `GetAbsoluteUrlForPath` to the shared `IHttpRequestService` implementation.
For the sake of our controller action, which does not have the same context as pages, we’ll also need to include the language name in the URL even if it is the default language. You can add another method called `GetBaseUrlWithLanguage`. Use boolean parameters to decide how the method determines the default language, and whether or not to include the language name in the URL for the default language
C#
**IHttpRequestService.cs**
Copy
```
namespace TrainingGuides.Web.Features.Shared.Services;

public interface IHttpRequestService
{
    ...

    /// <summary>
    /// Retrieves Base URL from the current request context. If current site is in a language variant, returns language with the base URL as well
    /// </summary>
    /// <param name="checkDatabaseForDefaultLanguage">Determines whether to query the database for the default language when it cannot be determined from route data.</param>
    /// <param name="alwaysIncludeLanguage">Determines whether to always include the language in the URL, even if it is the default language.</param>
    /// <returns>The base URL in current language variant. (e.g. website.com or website.com/es)</returns>
    /// <remarksThis overload exists to avoid adjusting the logic of the original method, which would affect an existing training guide</remarks>
    string GetBaseUrlWithLanguage(bool checkDatabaseForDefaultLanguage, bool alwaysIncludeLanguage = false);

    /// <summary>
    /// Retrieves a full URL for the specified relative path
    /// </summary>
    /// <param name="path">the relative path.</param>
    /// <param name="alwaysIncludeLanguage">Determines whether to always include the language in the URL, even if it is the default language.</param>
    /// <param name="queryString">Query string to add to the URL.</param>
    /// <returns></returns>
    string GetAbsoluteUrlForPath(string path, bool alwaysIncludeLanguage, QueryString? queryString = null);

    /// <summary>
    /// Combines URL paths
    /// </summary>
    /// <param name="paths">String paths to combine.</param>
    /// <returns>Combined paths</returns>
    /// <remarks>Works with or without leading and trailing slashes</remarks>
    string CombineUrlPaths(params string[] paths);
}
```

If you’ve already completed the [multilingual guides](/guides/development/multilingual), you might already have a different overload of `GetBaseUrlWithLanguage`.
Optionally, you can modify the logic of the existing method with optional parameters, rather than creating an overload.
C#
**HttpRequestService.cs**
Copy
```
using CMS.ContentEngine;
using CMS.DataEngine;
using TrainingGuides.Web.Features.Shared.Helpers;

namespace TrainingGuides.Web.Features.Shared.Services;

public class HttpRequestService : IHttpRequestService
{
    // Use dependency injection to populate these services
    private readonly IHttpContextAccessor httpContextAccessor;
    private readonly IInfoProvider<ContentLanguageInfo> contentLanguageInfoProvider;

    private const string WEB_PAGE_URL_PATHS = "Kentico.WebPageUrlPaths";

    ...

    private HttpRequest RetrieveCurrentRequest() => httpContextAccessor?.HttpContext?.Request
            ?? throw new NullReferenceException("Unable to retrieve current request context.");

    private bool IsLanguageDefault(string language)
    {
        if (string.IsNullOrWhiteSpace(language))
            return true;

        // Cache this query in real-world scenarios.
        string defaultLanguage = contentLanguageInfoProvider.Get()
            .WhereEquals(nameof(ContentLanguageInfo.ContentLanguageIsDefault), true)
            .Column(nameof(ContentLanguageInfo.ContentLanguageName))
            .TopN(1)
            .GetScalarResult<string>();

        if (string.IsNullOrEmpty(defaultLanguage))
            return true;

        return defaultLanguage == language;
    }

    /// <inheritdoc/>
    public string GetBaseUrlWithLanguage(bool checkDatabaseForDefaultLanguage, bool alwaysIncludeLanguage = false)
    {
        var currentRequest = RetrieveCurrentRequest();
        string language = (string?)currentRequest.RouteValues[ApplicationConstants.LANGUAGE_KEY] ?? string.Empty;
        var webPageUrlPathList = ((string?)currentRequest.RouteValues[WEB_PAGE_URL_PATHS])?.Split('/').ToList();

        bool notPrimaryLanguage;

        if (checkDatabaseForDefaultLanguage)
        {
            notPrimaryLanguage = webPageUrlPathList?.Contains(language) ?? !IsLanguageDefault(language);
        }
        else
        {
            var newWebPageUrlPathList = webPageUrlPathList ?? [];
            notPrimaryLanguage = newWebPageUrlPathList.Contains(language);
        }
        var url = new UriBuilder(GetBaseUrl(currentRequest))
        {
            Path = notPrimaryLanguage || alwaysIncludeLanguage
                ? $"/{language}"
                : string.Empty
        };

        return url.ToString();
    }

    /// <inheritdoc/>
    public string GetAbsoluteUrlForPath(string path, bool alwaysIncludeLanguage, QueryString? queryString = null)
    {
        // Handle ~ character that represents the root
        string trimmedPath = path.TrimStart('~');

        // If the path already contains a language name, don't include language name in base url.
        string baseUrl = StartsWithLanguage(trimmedPath)
            ? GetBaseUrl()
            : GetBaseUrlWithLanguage(true, alwaysIncludeLanguage);
        var fullUrl = new UriBuilder(baseUrl);

        //In case the base URL contains a language, it will count as the path, so we want to concatenate instead of replacing.
        string newPath = CombineUrlPaths(fullUrl.Path, trimmedPath);
        fullUrl.Path = newPath;

        if (queryString is not null)
        {
            fullUrl.Query = queryString.ToString();
        }

        return fullUrl.ToString();
    }

    private bool StartsWithLanguage(string relativePath)
    {
        // Cache this query in real-world scenarios.
        var languageCodes = contentLanguageInfoProvider.Get()
            .Column(nameof(ContentLanguageInfo.ContentLanguageName))
            .GetListResult<string>();

        string firstPathSegment = relativePath.TrimStart('/').Split('/')[0];

        return languageCodes.Any(code => code.Equals(firstPathSegment, StringComparison.OrdinalIgnoreCase));
    }

    /// <inheritdoc/>
    public string CombineUrlPaths(params string[] paths)
    {
        if (paths.Count() == 0)
        {
            return string.Empty;
        }

        var fixedPaths = paths.Select(p => p.Trim('/'));

        return string.Join("/", fixedPaths.Where(p => !string.IsNullOrWhiteSpace(p)));
    }

}
```

#### Hide the form from signed-in users
The new `DisplayForm` property also needs new logic. We should hide the registration form from members who are already signed in, because they already have accounts, so we need a method in the membership service to determine if the current member is authenticated.
Add a new method to the membership service that [retrieves the current member](/documentation/developers-and-admins/development/registration-and-authentication#retrieve-the-currently-authenticated-member), then utilize it in a separate boolean method that returns `false` if no such member is found.
In some cases, it is useful for the editor to see restricted content in the administration even if no member is signed in. This is why our `IsMemberAuthenticated` method also returns `true` if running in Page Builder or Preview mode.
C#
**IMembershipService.cs**
Copy
```
/// <summary>
/// Interface for membership services.
/// </summary>
public interface IMembershipService
{
    /// <summary>
    /// Gets the current member.
    /// </summary>
    /// <returns>The current <see cref="GuidesMember"/> if found; otherwise, null.</returns>
    Task<GuidesMember?> GetCurrentMember();

    /// <summary>
    /// Checks if the member is authenticated or if the application is running in Page Builder or Preview mode.
    /// </summary>
    /// <returns>True if the member is authenticated, or if running in Page Builder or Preview mode; otherwise, false.</returns>
    Task<bool> IsMemberAuthenticated();
}
```

C#
**MembershipService.cs**
Copy
```
...
using Kentico.Content.Web.Mvc;
using Kentico.PageBuilder.Web.Mvc;
using Kentico.Web.Mvc;
using Microsoft.AspNetCore.Identity;
...
public class MembershipService : IMembershipService
{
    // User dependency injection to populate these
    private readonly UserManager<GuidesMember> userManager;
    private readonly IHttpContextAccessor contextAccessor;

    ...

    /// <inheritdoc />
    public async Task<GuidesMember?> GetCurrentMember()
    {
        var context = contextAccessor.HttpContext;
        if (context is null)
        {
            return null;
        }

        return await userManager.GetUserAsync(context.User);
    }

    /// <inheritdoc />
    public async Task<bool> IsMemberAuthenticated()
    {
        bool memberFound = await GetCurrentMember() is not null;
        if (!memberFound)
        {
            var context = contextAccessor.HttpContext;
            var mode = context?.Kentico().PageBuilder().GetMode() ?? PageBuilderMode.Off;

            bool isPageBuilder = mode != PageBuilderMode.Off;
            bool isPreview = context?.Kentico().Preview().Enabled ?? false;

            return isPageBuilder || isPreview;
        }

        return memberFound;
    }
}
```

The `UserManager<T>` class is a scoped service, so it’s important to register `MembershipService` as a scoped service with the DI container.
#### Populate the view model
Now you can use these services in your view component to retrieve the action URL and hide the registration form from signed-in members. Add the file in the _~/Features/Membership/Widgets/Registration_ folder.
Remember to register the widget.
C#
**RegistrationWidgetViewComponent.cs**
Copy
```
using Kentico.PageBuilder.Web.Mvc;
using Microsoft.AspNetCore.Mvc;
using TrainingGuides.Web.Features.Membership.Services;
using TrainingGuides.Web.Features.Membership.Widgets.Registration;
using TrainingGuides.Web.Features.Shared.Helpers;
using TrainingGuides.Web.Features.Shared.Services;

[assembly: RegisterWidget(
    identifier: RegistrationWidgetViewComponent.IDENTIFIER,
    viewComponentType: typeof(RegistrationWidgetViewComponent),
    name: "Registration",
    propertiesType: typeof(RegistrationWidgetProperties),
    Description = "Displays a registration form for members.",
    IconClass = "icon-lines-rectangle-o")]

namespace TrainingGuides.Web.Features.Membership.Widgets.Registration;
public class RegistrationWidgetViewComponent : ViewComponent
{
    private readonly IHttpRequestService httpRequestService;
    private readonly IMembershipService membershipService;
    public const string IDENTIFIER = "TrainingGuides.RegistrationWidget";

    public RegistrationWidgetViewComponent(IHttpRequestService httpRequestService,
        IMembershipService membershipService)
    {
        this.httpRequestService = httpRequestService;
        this.membershipService = membershipService;
    }

    public async Task<RegistrationWidgetViewModel> BuildWidgetViewModel(RegistrationWidgetProperties properties) =>
        new()
        {
            ActionUrl = GetActionUrl(),
            DisplayForm = !await membershipService.IsMemberAuthenticated(),
            ShowName = properties.ShowName,
            ShowExtraFields = properties.ShowExtraFields,
            FormTitle = properties.FormTitle,
            SubmitButtonText = properties.SubmitButtonText,
            UserNameLabel = properties.UserNameLabel,
            EmailAddressLabel = properties.EmailAddressLabel,
            PasswordLabel = properties.PasswordLabel,
            ConfirmPasswordLabel = properties.ConfirmPasswordLabel,
            GivenNameLabel = properties.GivenNameLabel,
            FamilyNameLabel = properties.FamilyNameLabel,
            FamilyNameFirstLabel = properties.FamilyNameFirstLabel,
            FavoriteCoffeeLabel = properties.FavoriteCoffeeLabel
        };

    public async Task<IViewComponentResult> InvokeAsync(RegistrationWidgetProperties properties)
    {
        var registerModel = await BuildWidgetViewModel(properties);

        return View("~/Features/Membership/Widgets/Registration/RegistrationWidget.cshtml", registerModel);
    }

    private string GetActionUrl() => httpRequestService.GetAbsoluteUrlForPath(ApplicationConstants.REGISTER_ACTION_PATH, true);

}
```

### Add the widget’s view
Now we need to create widget view in the location specified by the view component’s `InvokeAsync` method.
Since the widget will use model validation for the form, let’s use a partial view to hold the form, which we can reference from the widget view.
The widget should only render the registration form if the `DisplayForm` property is true. In this case we chose to include the title regardless of whether the form displays.
Use an AJAX form so that when the endpoint returns a result, it does not replace the entire page on which the form resides.
cshtml
**RegistrationWidget.cshtml**
Copy
```
@using TrainingGuides.Web.Features.Membership.Widgets.Registration
@using TrainingGuides.Web.Features.Shared.Helpers

@model RegistrationWidgetViewModel

@{
    // Using a new guid ensures no conflict if, for some reason, multiple widgets are on the same page.
    var formDivId = $"registerForm{Guid.NewGuid()}";
}

@if (Model is null || Model.IsMisconfigured)
{
    <tg-page-builder-content>
        <tg-configure-widget-instructions />
    </tg-page-builder-content>

    return;
}

<h3 class="text-center px-2">@Model.FormTitle</h3>

@if (Model.DisplayForm)
{
    @using (Html.AjaxBeginForm(new AjaxOptions
    {
        HttpMethod = "POST",
        InsertionMode = InsertionMode.Replace,
        UpdateTargetId = formDivId
    }, new { action = Model.ActionUrl }))
    {
        <div id="@formDivId" class="px-2">
            <partial name="~/Features/Membership/Widgets/Registration/RegistrationForm.cshtml" model="Model" />
        </div>
    }
}
```

Now let’s implement the partial view specified above, containing the form itself.
cshtml
**RegistrationForm.cshtml**
Copy
```
@using TrainingGuides.Web.Features.Membership.Widgets.Registration

@model RegistrationWidgetViewModel

<div class="form-horizontal">
    <div asp-validation-summary="ModelOnly" class="text-danger field-validation-error"></div>

    @* Include hidden inputs for widget display.
        If server-side validation fails, these values will allow the form to be properly re-rendered. *@
    <input asp-for="ShowName">
    <input asp-for="ShowExtraFields">
    <input asp-for="SubmitButtonText">
    <input asp-for="UserNameLabel">
    <input asp-for="EmailAddressLabel">
    <input asp-for="PasswordLabel">
    <input asp-for="ConfirmPasswordLabel">
    <input asp-for="GivenNameLabel">
    <input asp-for="FamilyNameLabel">
    <input asp-for="FamilyNameFirstLabel">
    <input asp-for="FavoriteCoffeeLabel">

    <div class="form-group">
        <div class="editing-form-label-cell">
            <label for="@nameof(Model.UserName)" class="control-label form-label mt-3">@Model.UserNameLabel</label>
        </div>
        <div class="editing-form-value-cell">
            <input asp-for="UserName" class="form-control" test-id="userName">
            <span asp-validation-for="UserName" class="text-danger field-validation-error"></span>
        </div>
    </div>
    <div class="form-group">
        <div class="editing-form-label-cell">
            <label for="@nameof(Model.EmailAddress)" class="control-label form-label mt-3">@Model.EmailAddressLabel</label>
        </div>
        <div class="editing-form-value-cell">
            <input asp-for="EmailAddress" class="form-control" test-id="emailAddress">
            <span asp-validation-for="EmailAddress" class="text-danger field-validation-error"></span>
        </div>
    </div>
    <div class="form-group">
        <div class="editing-form-label-cell">
            <label for="@nameof(Model.Password)" class="control-label form-label mt-3">@Model.PasswordLabel</label>
        </div>
        <div class="editing-form-value-cell">
            <input asp-for="Password" class="form-control" test-id="password">
            <span asp-validation-for="Password" class="text-danger field-validation-error"></span>
        </div>
    </div>

    <div class="form-group">
        <div class="editing-form-label-cell">
            <label for="@nameof(Model.ConfirmPassword)" class="control-label form-label mt-3">@Model.ConfirmPasswordLabel</label>
        </div>
        <div class="editing-form-value-cell">
            <input asp-for="ConfirmPassword" class="form-control" test-id="confirmPassword">
            <span asp-validation-for="ConfirmPassword" class="text-danger field-validation-error"></span>
        </div>
    </div>
    @if (Model.ShowName)
    {
        <div class="form-group">
            <div class="editing-form-label-cell">
                <label for="@nameof(Model.GivenName)" class="control-label form-label mt-3">@Model.GivenNameLabel</label>
            </div>
            <div class="editing-form-value-cell">
                <input asp-for="GivenName" class="form-control" test-id="givenName">
                <span asp-validation-for="GivenName" class="text-danger field-validation-error"></span>
            </div>
        </div>

        <div class="form-group">
            <div class="editing-form-label-cell">
                <label for="@nameof(Model.FamilyName)" class="control-label form-label mt-3">@Model.FamilyNameLabel</label>
            </div>
            <div class="editing-form-value-cell">
                <input asp-for="FamilyName" class="form-control" test-id="familyName">
                <span asp-validation-for="FamilyName" class="text-danger field-validation-error"></span>
            </div>
        </div>

        <div class="form-group">
            <div class="editing-form-label-cell">
                <label for="@nameof(Model.FamilyNameFirst)" class="control-label form-label mt-3">@Model.FamilyNameFirstLabel</label>
            </div>
            <div class="editing-form-value-cell">
                <input asp-for="FamilyNameFirst" class="form-check" test-id="familyNameFirst">
                <span asp-validation-for="FamilyNameFirst" class="text-danger field-validation-error"></span>
            </div>
        </div>
    }
    @if (Model.ShowExtraFields)
    {
        <div class="form-group">
            <div class="editing-form-label-cell">
                <label for="@nameof(Model.FavoriteCoffee)" class="control-label form-label mt-3">@Model.FavoriteCoffeeLabel</label>
            </div>
            <div class="editing-form-value-cell">
                <input asp-for="FavoriteCoffee" class="form-control" test-id="favoriteCoffee">
                <span asp-validation-for="FavoriteCoffee" class="text-danger field-validation-error"></span>
            </div>
        </div>
    }

    <div class="text-center">
        <button id="register" type="submit" class="btn tg-btn-secondary text-uppercase my-4">@Model.SubmitButtonText</button>
    </div>
</div>
```

[ Previous page ](/modules/members/groundwork-registration-widget)
3 of 12
[ Mark complete and continue ](/modules/members/register-the-member)
![]()
[]()[]()
