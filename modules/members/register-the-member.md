# Complete the registration functionality
  * [ Copy page link ](modules/members/register-the-member#) | [Get HelpService ID](modules/members/register-the-member#)
Core MVC 5


[✖](modules/members/register-the-member# "Close page link panel") [Copy to clipboard](modules/members/register-the-member#)
With the registration widget in place, we can move on to the code that actually creates the member.
Let’s add some new methods to the `MembershipService`:
  * Create a new member in the Xperience database. 
    * Make sure that a new member’s username does not collide with another’s email, and vice versa. The registration form should prevent members from submitting usernames containing the ‘@’ character, but we should double check here, in case any future members are created in alternative ways.
  * Find a member based on email address. 
    * Wrap the `FindByNameAsync` method from the user manager class.
  * Find a member based on username. 
    * Wrap the `FindByEmailAsync` method from the user manager class.


C#
**IMembershipService.cs**
Copy
```
public interface IMembershipService
{
    ...
    /// <summary>
    /// Creates a new member.
    /// </summary>
    /// <param name="guidesMember">The member to create.</param>
    /// <param name="password">The password for the member.</param>
    /// <returns>The result of the creation operation.</returns>
    Task<IdentityResult> CreateMember(GuidesMember guidesMember, string password);

    /// <summary>
    /// Finds a member by email.
    /// </summary>
    /// <param name="email">The email of the member to find.</param>
    /// <returns>The <see cref="GuidesMember"/> if found; otherwise, null.</returns>
    Task<GuidesMember?> FindMemberByEmail(string email);

    /// <summary>
    /// Finds a member by username.
    /// </summary>
    /// <param name="userName">The username of the member to find.</param>
    /// <returns>The <see cref="GuidesMember"/> if found; otherwise, null.</returns>
    Task<GuidesMember?> FindMemberByName(string userName);
}
```

C#
**MembershipService.cs**
Copy
```
using Microsoft.AspNetCore.Identity;
using Microsoft.Extensions.Localization;
...
public class MembershipService : IMembershipService
{
    ...
    // Use dependency injection to populate stringLocalizer.
    private readonly IStringLocalizer<SharedResources> stringLocalizer;
    ...
    /// <inheritdoc />
    public async Task<IdentityResult> CreateMember(GuidesMember guidesMember, string password)
    {
        if (guidesMember is null)
        {
            return IdentityResult.Failed(new IdentityError
            {
                Code = "InvalidMember",
                Description = stringLocalizer["Invalid data."]
            });
        }

        // Uniqueness of username and email are checked automatically given correct configuration, but we need to make sure that one user's username cannot be set to another user's email, or vice versa.
        if (await UsernameEmailCollision(guidesMember))
        {
            return IdentityResult.Failed(new IdentityError
            {
                Code = "EmailOrUsernameInUse",
                Description = stringLocalizer["Email or username already in use."]
            });
        }

        return await userManager.CreateAsync(guidesMember, password);
    }

    private async Task<bool> UsernameEmailCollision(GuidesMember guidesMember)
    {
        bool userNameIsExistingEmail = !string.IsNullOrWhiteSpace(guidesMember.UserName)
            && guidesMember.UserName.Contains('@')
            && (await FindMemberByEmail(guidesMember.UserName)) is not null;

        bool emailIsExistingUserName = !string.IsNullOrWhiteSpace(guidesMember.Email)
            && (await FindMemberByName(guidesMember.Email)) is not null;

        return userNameIsExistingEmail || emailIsExistingUserName;
    }
    ...
    /// <inheritdoc />
    public async Task<GuidesMember?> FindMemberByName(string userName) =>
        await userManager.FindByNameAsync(userName);

    /// <inheritdoc />
    public async Task<GuidesMember?> FindMemberByEmail(string email) =>
        await userManager.FindByEmailAsync(email);
}
```

Now we can implement a controller action that handles the POST request from the registration form.
Start by creating a _Controllers_ folder in the _Membership_ directory and adding a _Registration controller_.
Create a `Register` action using the same path constant from earlier. If the model is not valid, return the form and model so it will display validation messages.
Then try to create a member in the database with the new method. If it succeeds, return a success message, and otherwise, log an error and return the form again.
C#
**RegistrationController.cs**
Copy
```
using Microsoft.AspNetCore.Identity;
using Microsoft.AspNetCore.Mvc;
using Microsoft.Extensions.Localization;
using CMS.Core;
using TrainingGuides.Web.Features.Membership.Services;
using TrainingGuides.Web.Features.Shared.Helpers;
using TrainingGuides.Web.Features.Membership.Widgets.Registration;

namespace TrainingGuides.Web.Features.Membership.Controllers;

public class RegistrationController : Controller
{
    // Use dependency injection to populate these.
    private readonly IMembershipService membershipService;
    private readonly ILogger<RegistrationController> logger;
    private readonly IStringLocalizer<SharedResources> stringLocalizer;

    private const string REGISTRATION_FORM_VIEW_PATH = "~/Features/Membership/Widgets/Registration/RegistrationForm.cshtml";

    ...

    [HttpPost($"{{{ApplicationConstants.LANGUAGE_KEY}}}{ApplicationConstants.REGISTER_ACTION_PATH}")]
    [ValidateAntiForgeryToken]
    public async Task<IActionResult> Register(RegistrationWidgetViewModel model)
    {
        var result = IdentityResult.Failed();

        if (!ModelState.IsValid)
        {
            ModelState.AddModelError(string.Empty, stringLocalizer["Please fill in all required fields."]);
            return PartialView(REGISTRATION_FORM_VIEW_PATH, model);
        }

        // NOTE: This example does not include consent, but in a real-world scenario, you may need to get a member's consent before saving this data.
        var guidesMember = new GuidesMember
        {
            UserName = model.UserName,
            Email = model.EmailAddress,
            GivenName = model.GivenName,
            FamilyName = model.FamilyName,
            FamilyNameFirst = model.FamilyNameFirst,
            FavoriteCoffee = model.FavoriteCoffee,
            Enabled = false
        };
        try
        {
            result = await membershipService.CreateMember(guidesMember, model.Password);
        }
        catch (Exception ex)
        {
            logger.LogError(EventIds.MemberRegistration, ex, "An error occurred while registering a new member with username {UserName} in {Controller}.{Action}", model.UserName, nameof(RegistrationController), nameof(Register));
            result = IdentityResult.Failed([new() { Code = "Failure", Description = stringLocalizer["Registration failed."] }]);
        }

        if (result.Succeeded)
        {
            return Content(stringLocalizer["Success!"]);
        }
        else
        {
            foreach (string error in result.Errors.Select(e => e.Description))
            {
                ModelState.AddModelError(string.Empty, error);
            }

            return PartialView(REGISTRATION_FORM_VIEW_PATH, model);
        }
    }
}
```

C#
**~/Features/Shared/Logging/EventIds**
Copy
```
namespace TrainingGuides.Web.Features.Shared.Logging;

/// <summary>
/// Contains event IDs for logging.
/// </summary>
internal static class EventIds
{
    public static readonly EventId RetrieveUrl = new(1001, nameof(RetrieveUrl));
    public static readonly EventId MemberRegistration = new(1002, nameof(MemberRegistration));
    public static readonly EventId MemberSignIn = new(1003, nameof(MemberSignIn));
}
 


```

## Check your progress
Now you should be able to add the Registration widget to a page with a widget zone and configure its properties.
Your browser does not support the video tag. 
Once you save the registration widget on a page, you can navigate to the page as a visitor and submit the form. You’ll be able to see the member you created in the **Members** application of the Xperience administration interface.
[![Screenshot of the registration form filled out on a page](docsassets/guides/implement-member-registration/RegistrationForm.png)](https://docs.kentico.com/docsassets/guides/implement-member-registration/RegistrationForm.png)
[![Screenshot of the resulting member appearing in the member listing](docsassets/guides/implement-member-registration/MembersApp.png)](https://docs.kentico.com/docsassets/guides/implement-member-registration/MembersApp.png)
Although this guide does not describe how to set up email confirmation as part of the registration process, you can find a working example in the [Training guides repository](https://github.com/Kentico/xperience-by-kentico-training-guides/tree/finished)
Specifically check out:
  * [The additional files in the Registration widget folder](https://github.com/Kentico/xperience-by-kentico-training-guides/tree/finished/src/TrainingGuides.Web/Features/Membership/Widgets/Registration)
  * [The new actions in the Registration controller](https://github.com/Kentico/xperience-by-kentico-training-guides/blob/finished/src/TrainingGuides.Web/Features/Membership/Controllers/RegistrationController.cs)
  * [The change to RequireConfirmedEmail in Program.cs](https://github.com/Kentico/xperience-by-kentico-training-guides/blob/finished/src/TrainingGuides.Web/Program.cs)


## Apply the same approach to authentication
Now that members can register on your website, you need to implement a way for them to sign in. In our Training guides repository, we demonstrate the sign in utilizing the same approach as for the registration: a widget.
The process for creating a Sign-in widget is almost identical to the registration functionality we’ve just built.
You can use an AJAX form and a partial view, so that the context surrounding the widget is not lost when the controller returns the model with validation errors. The key difference is that sign-in forms typically redirect to a new page after successful authentication.
However, **these two factors introduce a quirk** ; if we try to return a redirect, the resulting page will render nested within the element specified as the form’s update target.
One approach to work around this behavior is to add a flag to the model that indicates if authentication was successful, and return the form with the model.
Then, in the Sign-in form’s view, you can render a redirect when the successful authentication flag is `true`.
C#
**AuthenticationController.cs**
Copy
```
using CMS.ContentEngine;
using CMS.DataEngine;
using Kentico.Content.Web.Mvc.Routing;
using Microsoft.AspNetCore.Authorization;
using Microsoft.AspNetCore.Mvc;
using Microsoft.Extensions.Localization;
using TrainingGuides.Web.Features.Membership.Services;
using TrainingGuides.Web.Features.Membership.Widgets.LinkOrSignOut;
using TrainingGuides.Web.Features.Membership.Widgets.SignIn;
using TrainingGuides.Web.Features.Shared.Helpers;
using TrainingGuides.Web.Features.Shared.Services;

namespace TrainingGuides.Web.Features.Membership.Controllers;

public class AuthenticationController : Controller
{
    // Use dependency injection to populate these
    private readonly IMembershipService membershipService;
    private readonly IStringLocalizer<SharedResources> stringLocalizer;
    private readonly IPreferredLanguageRetriever preferredLanguageRetriever;
    private readonly IInfoProvider<ContentLanguageInfo> contentLanguageInfoProvider;
    private readonly IHttpRequestService httpRequestService;

    private const string SIGN_IN_FAILED = "Your sign-in attempt was not successful. Please try again.";

    ...

    private IActionResult RenderError(SignInWidgetViewModel model)
    {
        ModelState.AddModelError(string.Empty, stringLocalizer[SIGN_IN_FAILED]);
        return PartialView("~/Features/Membership/Widgets/SignIn/SignInForm.cshtml", model);
    }

    private IActionResult RenderSuccess(string redirectUrl)
    {
        var model = new SignInWidgetViewModel
        {
            DisplayForm = true,
            AuthenticationSuccessful = true,
            RedirectUrl = redirectUrl

        };
        return PartialView("~/Features/Membership/Widgets/SignIn/SignInForm.cshtml", model);
    }

    [HttpPost($"{{{ApplicationConstants.LANGUAGE_KEY}}}{ApplicationConstants.AUTHENTICATE_ACTION_PATH}")]
    [ValidateAntiForgeryToken]
    public async Task<IActionResult> Authenticate(SignInWidgetViewModel model, [FromQuery(Name = ApplicationConstants.RETURN_URL_PARAMETER)] string returnUrl)
    {
        if (!ModelState.IsValid)
        {
            return RenderError(model);
        }

        var signInResult = await membershipService.SignIn(model.UserNameOrEmail, model.Password, model.StaySignedIn);

        string returnPath = string.IsNullOrWhiteSpace(returnUrl)
            ? (model.DefaultRedirectPageGuid == Guid.Empty
                ? "/"
                : (await httpRequestService.GetPageRelativeUrl(model.DefaultRedirectPageGuid, preferredLanguageRetriever.Get())).TrimStart('~'))
            : EnsureRelativeReturnUrl(returnUrl);

        string absoluteReturnUrl = httpRequestService.GetAbsoluteUrlForPath(returnPath, false);

        return signInResult.Succeeded
            ? RenderSuccess(absoluteReturnUrl)
            : RenderError(model);
    }

    private string EnsureRelativeReturnUrl(string returnUrl) =>
        returnUrl.StartsWith('/') || returnUrl.StartsWith("~/")
            ? returnUrl
            : httpRequestService.ExtractRelativePath(returnUrl);

    ...
}
```

In the above code, the `AuthenticationSuccessful` flag in `SignInWidgetViewModel` indicates that the form should redirect the user when it receives the model.
cshtml
**SignInForm.cshtml**
Copy
```
@using TrainingGuides.Web.Features.Membership.Widgets.SignIn

@model SignInWidgetViewModel

@if(Model.AuthenticationSuccessful)
{
    <script>
        window.location = "@Model.RedirectUrl";
    </script>
}

<div class="form-horizontal">

    <div asp-validation-summary="ModelOnly" class="text-danger validation-summary"></div>

    @* Include hidden inputs for widget display and links.
        If server-side validation fails, these values will allow the form to be properly re-rendered. *@
    <input asp-for="DefaultRedirectPageGuid">
    <input asp-for="DisplayForm">
    <input asp-for="SubmitButtonText">
    <input asp-for="UserNameOrEmailLabel">
    <input asp-for="PasswordLabel">
    <input asp-for="StaySignedInLabel">

    <div class="form-group">
        <div class="editing-form-label-cell">
            <label class="control-label form-label mt-3">@Model.UserNameOrEmailLabel</label>
        </div>
        <div class="editing-form-value-cell">
            <input asp-for="UserNameOrEmail" class="form-control">
            <span asp-validation-for="UserNameOrEmail" class="text-danger field-validation-error"></span>
        </div>
    </div>

    <div class="form-group">
        <div class="editing-form-label-cell">
            <label class="control-label form-label mt-3">@Model.PasswordLabel</label>
        </div>
        <div class="editing-form-value-cell">
            <input asp-for="Password" class="form-control">
            <span asp-validation-for="Password" class="text-danger field-validation-error"></span>
        </div>
    </div>
    <div class="mt-3 form-check">
        <input asp-for="StaySignedIn" class="form-check-input">
        <label class="form-check-label">@Model.StaySignedInLabel</label>
    </div>
    <div class="text-center">
        <button id="signIn" type="submit" class="btn tg-btn-secondary text-uppercase my-4">@Model.SubmitButtonText</button>
    </div>
</div>
```

C#
**SignInWidgetViewComponent.cs**
Copy
```
using Kentico.PageBuilder.Web.Mvc;
using Microsoft.AspNetCore.Mvc;
using TrainingGuides.Web.Features.Membership.Services;
using TrainingGuides.Web.Features.Membership.Widgets.SignIn;
using TrainingGuides.Web.Features.Shared.Helpers;
using TrainingGuides.Web.Features.Shared.Services;

[assembly: RegisterWidget(
    identifier: SignInWidgetViewComponent.IDENTIFIER,
    viewComponentType: typeof(SignInWidgetViewComponent),
    name: "SignIn",
    propertiesType: typeof(SignInWidgetProperties),
    Description = "Displays a sign in form for members.",
    IconClass = "icon-user")]

namespace TrainingGuides.Web.Features.Membership.Widgets.SignIn;
public class SignInWidgetViewComponent : ViewComponent
{
    private readonly IHttpRequestService httpRequestService;
    private readonly IMembershipService membershipService;

    public const string IDENTIFIER = "TrainingGuides.SignInWidget";

    public SignInWidgetViewComponent(
        IHttpRequestService httpRequestService,
        IMembershipService membershipService)
    {
        this.httpRequestService = httpRequestService;
        this.membershipService = membershipService;
    }

    public async Task<IViewComponentResult> InvokeAsync(SignInWidgetProperties properties) =>
        View("~/Features/Membership/Widgets/SignIn/SignInWidget.cshtml", await BuildWidgetViewModel(properties));

    public async Task<SignInWidgetViewModel> BuildWidgetViewModel(SignInWidgetProperties properties) => new SignInWidgetViewModel
    {
        ActionUrl = GetActionUrl(),
        DefaultRedirectPageGuid = properties.DefaultRedirectPage.FirstOrDefault()?.Identifier ?? Guid.Empty,
        DisplayForm = !await membershipService.IsMemberAuthenticated(),
        FormTitle = properties.FormTitle,
        SubmitButtonText = properties.SubmitButtonText,
        UserNameOrEmailLabel = properties.UserNameLabel,
        PasswordLabel = properties.PasswordLabel,
        StaySignedInLabel = properties.StaySignedInLabel
    };

    private string GetActionUrl()
    {
        return httpRequestService.GetAbsoluteUrlForPath(ApplicationConstants.AUTHENTICATE_ACTION_PATH, true);
    }
}
```

With this context, try to follow the same process we went through for the registration widget to create a sign-in widget.
If you run into any trouble, you can reference the completed implementation in the [finished branch](https://github.com/Kentico/xperience-by-kentico-training-guides/tree/finished) of the Training guides repository.
You may find the following resources particularly helpful:
  * [Http request service](https://github.com/Kentico/xperience-by-kentico-training-guides/blob/finished/src/TrainingGuides.Web/Features/Shared/Services/HttpRequestService.cs)
  * [Membership service](https://github.com/Kentico/xperience-by-kentico-training-guides/blob/finished/src/TrainingGuides.Web/Features/Membership/Services/MembershipService.cs)
  * [Sign-in widget](https://github.com/Kentico/xperience-by-kentico-training-guides/tree/finished/src/TrainingGuides.Web/Features/Membership/Widgets/SignIn)
  * [Link or sign-out widget](https://github.com/Kentico/xperience-by-kentico-training-guides/tree/finished/src/TrainingGuides.Web/Features/Membership/Widgets/LinkOrSignOut)
  * [Authentication controller](https://github.com/Kentico/xperience-by-kentico-training-guides/blob/finished/src/TrainingGuides.Web/Features/Membership/Controllers/AuthenticationController.cs)


[ Previous page ](modules/members/build-registration-widget)
4 of 12
[ Mark complete and continue ](modules/members/create-member-contact-relationship)
  * [Community Questions & Answers](https://community.kentico.com/q-and-a)
  * [Contact support](https://community.kentico.com/support)


### Cookie consent
We use necessary [cookies](https://www.kentico.com/cookies-policy) to run our website and improve your experience while browsing. Additional cookies are only used with your consent. You may revoke your consent on the [Cookies Policy](https://www.kentico.com/cookies-policy) page or in your browser at any time. 
ACCEPT ALL  [Configure](https://www.kentico.com/cookies-policy)
USE ONLY NECESSARY 
![](https://docs.kentico.com/modules/members/register-the-member)
[](https://docs.kentico.com/modules/members/register-the-member)[](https://docs.kentico.com/modules/members/register-the-member)