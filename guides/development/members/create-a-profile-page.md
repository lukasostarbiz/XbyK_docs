---
source: https://docs.kentico.com/guides/development/members/create-a-profile-page
scrape_date: 2026-01-22
---

  * [Home](/guides)
  * [Development](/guides/development)
  * [Members](/guides/development/members)
  * Create a profile page 


# Create a profile page
If you allow members to register and sign in to your site, they will expect the ability to update their information.
[An earlier part of this series](/guides/development/members/implement-member-registration) covered how to use configurable widgets for the registration and sign-in forms. Let’s explore an alternative approach now, utilizing a pre-defined [page template](/documentation/developers-and-admins/development/builders/page-builder/page-templates-for-page-builder) on a page.
In some ways, our approach will be similar to using a dedicated MVC route that displays a view. However, using a _page template_ on a page allows editors to see the page in the content tree. That way, editors can easily link the page with Xperience’s built-in form components and control the page’s URL slug.
## Before you start
This guide requires the following:
  * Familiarity with [C#](https://learn.microsoft.com/en-us/dotnet/csharp/), [.NET Core](https://learn.microsoft.com/en-us/dotnet/), [.NET Identity](https://learn.microsoft.com/en-us/aspnet/core/security/authentication/identity), [Dependency injection](https://learn.microsoft.com/en-us/dotnet/core/extensions/dependency-injection), and the [MVC pattern](https://learn.microsoft.com/en-us/aspnet/core/mvc/overview).
  * A running instance of Xperience by Kentico, preferably [30.11.1](/documentation/changelog) or higher. 
Some features covered in the training guides may not work in older versions. 


The examples in this guide require that you:
  * Have installed the The [main branch](https://github.com/Kentico/xperience-by-kentico-training-guides) of the _Training guides_ repository.
  * Have completed the previous guides in the [Members series](/guides/development/members).


**Code samples**
You can find a project with completed, working versions of code samples from this guide and others in the [finished branch](https://github.com/Kentico/xperience-by-kentico-training-guides/tree/finished) of the _Training guides_ repository.
The [main branch](https://github.com/Kentico/xperience-by-kentico-training-guides) of the repository provides a starting point to code along with the guides.
The code samples in this guide are for [.NET 8](https://learn.microsoft.com/en-us/dotnet/core/whats-new/dotnet-8/overview) only.
They come from a project that uses [implicit using directives](https://learn.microsoft.com/en-us/dotnet/core/project-sdk/overview#implicit-using-directives). You may need to add additional `using` directives to your code if your project does not use this feature.
## Examine the requirements
Let’s take a moment to consider what should be possible to update on a standard profile page.
Since members can trigger the password reset process without signing in, we don’t need to include passwords in the profile form.
Email and username should be a bit more closely guarded, with processes similar to the email confirmation and password reset processes, where a special token is sent to verify the identity of the member, so we won’t include them as editable fields, though we can display them.
We can also display the date the member’s account was created, but this should not be editable for factual reasons.
We can, however, allow members to edit their name and name order, and their favorite coffee.
The form should display a success message when it’s able to update a member’s information, and validation errors when the member submits improperly formatted data.
While it is possible to add page template properties that allow editors to configure the form in a similar manner to the [registration widget](/guides/development/members/implement-member-registration), this example will not go over the process.
## Build the _Update profile_ form
Let’s make a view component for managing the update profile form. This way, in case we ever decide to include it somewhere aside from this template, it’s easier to reuse.
Under the _~/Features/Membership/Profile_ folder in the _TrainingGuides.Web_ project, create a _ViewComponents_ folder to house the files.
Overall, the view component will be somewhat similar to the registration form widget, using an AJAX form with a partial view to manage validation and results.
### Define the view model
The `GuidesMemberProfileViewModel` from [earlier in the series](/guides/development/members/implement-member-registration#set-up-a-view-model) contains the custom fields that we used to expand the built-in _Member_ type, so we can inherit from this class for our update form, adding the extra fields that we need.
Let’s take another peek at that class as a refresher.
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

Back in the _~/Features/Membership/Profile/ViewComponents_ folder, create a new class called `UpdateProfileViewModel` that inherits from `GuidesMemberProfileViewModel`.
  * For form fields that should be displayed but not editable (e.g., `EmailAddress`), add properties decorated with the `Display` attribute.
  * For values that aren’t part of the form but need to persist through a post return (e.g., `SubmitButtonText`), use the `HiddenInput` attribute like you did in the registration form,.
  * For properties only used in the initial setup (e.g., `Title`), or which only apply after a result is returned (e.g., `SuccessMessage`), no attributes are necessary.


C#
**UpdateProfileViewModel.cs**
Copy
```
using System.ComponentModel.DataAnnotations;
using Microsoft.AspNetCore.Mvc;

namespace TrainingGuides.Web.Features.Membership.Profile;

public class UpdateProfileViewModel : GuidesMemberProfileViewModel
{
    private string fullName = string.Empty;

    [Display(Name = "Full name")]
    public string FullName
    {
        get => !string.IsNullOrWhiteSpace(fullName)
            ? fullName
            : FamilyNameFirst
                ? $"{FamilyName} {GivenName}"
                : $"{GivenName} {FamilyName}";
        set => fullName = value ?? string.Empty;
    }

    [Display(Name = "Email address")]
    public string EmailAddress { get; set; } = string.Empty;

    [Display(Name = "User name")]
    public string UserName { get; set; } = string.Empty;

    [Display(Name = "Member since")]
    public DateTime Created { get; set; }

    [HiddenInput]
    public string SubmitButtonText { get; set; } = string.Empty;

    public string Title { get; set; } = string.Empty;

    public string ActionUrl { get; set; } = string.Empty;

    public string SuccessMessage { get; set; } = string.Empty;
}
```

### Add a service
Since our view model needs data from multiple sources, such as information about the current member and about the base URL of the site, let’s make a separate service to populate it, rather than a static method within a view model.
Add a _Services_ folder in the _Profile_ directory and define a service that assembles a view model for a given member.
C#
**IUpdateProfileService.cs**
Copy
```
namespace TrainingGuides.Web.Features.Membership.Profile;

public interface IUpdateProfileService
{
    /// <summary>
    /// Get the view model for the update profile view component.
    /// </summary>
    /// <param name="guidesMember">The member to base the view model on.</param>
    /// <returns>An <see cref="UpdateProfileViewModel"/> based on the values of the <see cref="GuidesMember"/>'s properties.</returns>
    UpdateProfileViewModel GetViewModel(GuidesMember guidesMember);
}
```

C#
**UpdateProfileService.cs**
Copy
```
using Kentico.Content.Web.Mvc.Routing;
using Microsoft.Extensions.Localization;
using TrainingGuides.Web.Features.Shared.Helpers;
using TrainingGuides.Web.Features.Shared.Services;

namespace TrainingGuides.Web.Features.Membership.Profile;

public class UpdateProfileService : IUpdateProfileService
{
    private readonly IHttpRequestService httpRequestService;
    private readonly IPreferredLanguageRetriever preferredLanguageRetriever;
    private readonly IStringLocalizer<SharedResources> stringLocalizer;

    public UpdateProfileService(IHttpRequestService httpRequestService,
        IPreferredLanguageRetriever preferredLanguageRetriever,
        IStringLocalizer<SharedResources> stringLocalizer)
    {
        this.httpRequestService = httpRequestService;
        this.preferredLanguageRetriever = preferredLanguageRetriever;
        this.stringLocalizer = stringLocalizer;
    }

    /// <inheritdoc/>
    public UpdateProfileViewModel GetViewModel(GuidesMember guidesMember) =>
        new()
        {
            GivenName = guidesMember?.GivenName ?? string.Empty,
            FamilyName = guidesMember?.FamilyName ?? string.Empty,
            FamilyNameFirst = guidesMember?.FamilyNameFirst ?? false,
            FavoriteCoffee = guidesMember?.FavoriteCoffee ?? string.Empty,
            UserName = guidesMember?.UserName ?? string.Empty,
            EmailAddress = guidesMember?.Email ?? string.Empty,
            FullName = guidesMember?.FullName ?? string.Empty,
            Created = guidesMember?.Created ?? DateTime.MinValue,
            ActionUrl = httpRequestService.GetAbsoluteUrlForPath(ApplicationConstants.UPDATE_PROFILE_ACTION_PATH, true),
            SubmitButtonText = stringLocalizer["Submit"],
            Title = stringLocalizer["Update profile"]
        };
}
```

Remember to register the service with the DI container.
C#
**~/ServiceCollectionExtensions.cs**
Copy
```
...
public static void AddTrainingGuidesServices(this IServiceCollection services)
{
    ...
    services.AddSingleton<IUpdateProfileService, UpdateProfileService>();
    ...
}
...
```

### Create the view component class
Now we can define the main file of our view component for updating the profile of the current member. **Make sure to pre-populate the model with data about the current member** , so the member does not need to manually re-fill all of their information every time.
To improve the editor experience, add functionality to display a dummy member in **Preview** or **Page Builder** view of the profile page. This way, editors who have not signed in as members won’t see a broken, empty profile when they visit the page in the Xperience admin UI.
You can prevent unauthenticated members from seeing the profile form by setting its page to [require authentication.](/documentation/business-users/website-content/secure-pages)
We’ll explore this process in more detail [later in the membership series](/guides/development/members/require-authentication-for-certain-content).
C#
**UpdateProfileViewComponent.cs**
Copy
```
using Kentico.Content.Web.Mvc;
using Kentico.PageBuilder.Web.Mvc;
using Kentico.Web.Mvc;
using Microsoft.AspNetCore.Mvc;
using TrainingGuides.Web.Features.Membership.Services;

namespace TrainingGuides.Web.Features.Membership.Profile;

public class UpdateProfileViewComponent : ViewComponent
{
    private readonly IMembershipService membershipService;
    private readonly IHttpContextAccessor httpContextAccessor;
    private readonly IUpdateProfileService updateProfileService;

    public UpdateProfileViewComponent(IMembershipService membershipService,
        IHttpContextAccessor httpContextAccessor,
        IUpdateProfileService updateProfileService)
    {
        this.membershipService = membershipService;
        this.httpContextAccessor = httpContextAccessor;
        this.updateProfileService = updateProfileService;

    }

    public async Task<IViewComponentResult> InvokeAsync()
    {
        var httpContext = httpContextAccessor.HttpContext;
        GuidesMember currentMember;

        bool useDummyMember = httpContext.Kentico().PageBuilder().GetMode() != PageBuilderMode.Off || httpContext.Kentico().Preview().Enabled;

        currentMember = useDummyMember
            ? membershipService.DummyMember
            : await membershipService.GetCurrentMember() ?? membershipService.DummyMember;

        var model = updateProfileService.GetViewModel(currentMember);

        return View("~/Features/Membership/Profile/ViewComponents/UpdateProfile.cshtml", model);
    }
}
```

C#
**~/Features/Membership/Services/IMembershipService.cs**
Copy
```
...
/// <summary>
/// Generates a dummy member for display in page builder and preview modes.
/// </summary>
/// <returns> A dummy member</returns>
GuidesMember DummyMember { get; }
...
```

C#
**~/Features/Membership/Services/MembershipService.cs**
Copy
```
...
/// <inheritdoc/>
public GuidesMember DummyMember => new()
{
    UserName = "JohnDoe",
    Email = "JohnDoe@localhost.local",
    GivenName = "John",
    FamilyName = "Doe",
    FamilyNameFirst = false,
    FavoriteCoffee = "Latte",
    Enabled = true,
    Created = DateTime.Now,
    Id = 0
};
...
```

### Create views for the view component
Let’s start by creating the view returned by the component’s `InvokeAsync` method.
Much like with the [registration widget](/guides/development/members/implement-member-registration), create an AJAX form that posts to a special action, and use a separate partial view for the form fields within the update target element.
cshtml
**UpdateProfile.cshtml**
Copy
```
@using TrainingGuides.Web.Features.Membership.Profile;
@using TrainingGuides.Web.Features.Shared.Helpers;

@model UpdateProfileViewModel

@{
    // Using a new guid ensures no conflict if, for some reason, multiple instances of the view component are on the same page.
    string formDivId = $"updateProfileForm{Guid.NewGuid()}";
}
<h3>@Model.Title</h3>

@using (Html.AjaxBeginForm(new AjaxOptions
{
    HttpMethod = "POST",
    InsertionMode = InsertionMode.Replace,
    UpdateTargetId = formDivId
}, new { action = Model.ActionUrl }))
{
    <div id="@formDivId" class="px-2">
        <partial name="~/Features/Membership/Profile/ViewComponents/UpdateProfileForm.cshtml" model="Model" />
    </div>
}
```

C#
**~/Features/Shared/Helpers/ApplicationConstants.cs**
Copy
```
...
public const string UPDATE_PROFILE_ACTION_PATH = "/MemberManagement/UpdateProfile";
...
```

Now let’s add the partial view that the above code expects.
**Displaying non-editable fields**
For fields that members are not supposed to edit, do not use the input tag helper with the readonly attribute. Render them directly instead.
This way, values such as the full name, which can change based on a form submission, will update when a response comes back from the controller.
Directly rendered values also clearly indicate to members that the fields are not editable.
If there is a success message, meaning the controller is returning the current instance of the view after a successful update, render it to the page.
cshtml
**UpdateProfileForm.cshtml**
Copy
```
@using TrainingGuides.Web.Features.Membership.Profile;

@model UpdateProfileViewModel

<div class="form-horizontal">
    <div asp-validation-summary="ModelOnly" class="text-danger field-validation-error"></div>

    <input asp-for="SubmitButtonText">

    <div class="form-group">
        <div class="editing-form-label-cell">
            <label asp-for="UserName" class="control-label form-label mt-3"></label>
        </div>
        <div class="ms-2">
            <strong>@Model.UserName</strong>
        </div>
    </div>
    <div class="form-group">
        <div class="editing-form-label-cell">
            <label asp-for="FullName" class="control-label form-label mt-3"></label>
        </div>
        <div class="ms-2">
            <strong>@Model.FullName</strong>
        </div>
    </div>
    <div class="form-group">
        <div class="editing-form-label-cell">
            <label asp-for="EmailAddress" class="control-label form-label mt-3"></label>
        </div>
        <div class="ms-2">
            <strong>@Model.EmailAddress</strong>
        </div>
    </div>
    <div class="form-group">
        <div class="editing-form-label-cell">
            <label asp-for="Created" class="control-label form-label mt-3"></label>
        </div>
        <div class="ms-2">
            <strong>@Model.Created</strong>
        </div>
    </div>
    <div class="form-group">
        <div class="editing-form-label-cell">
            <label asp-for="GivenName" class="control-label form-label mt-3"></label>
        </div>
        <div class="editing-form-value-cell">
            <input asp-for="GivenName" class="form-control" test-id="givenName">
            <span asp-validation-for="GivenName" class="text-danger field-validation-error"></span>
        </div>
    </div>
    <div class="form-group">
        <div class="editing-form-label-cell">
            <label asp-for="FamilyName" class="control-label form-label mt-3"></label>
        </div>
        <div class="editing-form-value-cell">
            <input asp-for="FamilyName" class="form-control" test-id="familyName">
            <span asp-validation-for="FamilyName" class="text-danger field-validation-error"></span>
        </div>
    </div>
    <div class="form-group">
        <div class="editing-form-label-cell">
            <label asp-for="FamilyNameFirst" class="control-label form-label mt-3"></label>
        </div>
        <div class="editing-form-value-cell">
            <input asp-for="FamilyNameFirst" class="form-check" test-id="familyNameFirst">
            <span asp-validation-for="FamilyNameFirst" class="text-danger field-validation-error"></span>
        </div>
    </div>
    <div class="form-group">
        <div class="editing-form-label-cell">
            <label asp-for="FavoriteCoffee" class="control-label form-label mt-3"></label>
        </div>
        <div class="editing-form-value-cell">
            <input asp-for="FavoriteCoffee" class="form-control" test-id="favoriteCoffee">
            <span asp-validation-for="FavoriteCoffee" class="text-danger field-validation-error"></span>
        </div>
    </div>

    <div class="text-center">
        <button id="register" type="submit" class="btn tg-btn-secondary text-uppercase my-4">@Model.SubmitButtonText</button>
    </div>
    @if (!string.IsNullOrEmpty(Model.SuccessMessage))
    {
        <div class="text-center">
            <strong>@Model.SuccessMessage</strong>
        </div>
    }
</div>
```

## Handle the form with a controller action
In the _Controllers_ directory of the _Membership_ folder, add a new controller called `MemberManagementController`.
You may already have this file if you’ve taken the time to replicate the reset password functionality from the [finished branch](https://github.com/Kentico/xperience-by-kentico-training-guides/tree/finished/src/TrainingGuides.Web/Features/Membership) of the _Training guides_ repository.
Create a POST action using the same path constant as the _UpdateProfile_ view, adding any new functionality that you need to the membership service.
C#
**MemberManagementController.cs**
Copy
```
using Microsoft.AspNetCore.Mvc;
using Microsoft.Extensions.Localization;
using TrainingGuides.Web.Features.Membership.Profile;
using TrainingGuides.Web.Features.Membership.Services;
using TrainingGuides.Web.Features.Shared.Helpers;

namespace TrainingGuides.Web.Features.Membership.Controllers;

public class MemberManagementController : Controller
{
    private readonly IMembershipService membershipService;
    private readonly IStringLocalizer<SharedResources> stringLocalizer;

    private const string UPDATE_PROFILE_FORM_VIEW_PATH = "~/Features/Membership/Profile/ViewComponents/UpdateProfileForm.cshtml";

    public MemberManagementController(IMembershipService membershipService,
        IStringLocalizer<SharedResources> stringLocalizer)
    {
        this.membershipService = membershipService;
        this.stringLocalizer = stringLocalizer;
    }

    /// <summary>
    /// Updates a user profile.
    /// </summary>
    /// <param name="model">View model with profile fields to update.</param>
    /// <returns></returns>
    [HttpPost($"{{{ApplicationConstants.LANGUAGE_KEY}}}{ApplicationConstants.UPDATE_PROFILE_ACTION_PATH}")]
    [ValidateAntiForgeryToken]
    public async Task<IActionResult> UpdateProfile(UpdateProfileViewModel model)
    {
        if (!ModelState.IsValid)
        {
            return PartialView(UPDATE_PROFILE_FORM_VIEW_PATH, model);
        }

        // Get the current member instead of pulling from the model, so that members cannot attempt to change each others information.
        var guidesMember = await membershipService.GetCurrentMember();

        if (guidesMember is not null)
        {
            var result = await membershipService.UpdateMemberProfile(guidesMember, model);

            if (result.Succeeded)
            {
                var newModel = GetNewUpdateProfileViewModel(model,
                    guidesMember,
                    stringLocalizer["Profile updated successfully."]);

                return PartialView(UPDATE_PROFILE_FORM_VIEW_PATH, newModel);
            }
            else
            {
                foreach (var error in result.Errors)
                {
                    ModelState.AddModelError(string.Empty, error.Description);
                }
            }
        }
        return PartialView(UPDATE_PROFILE_FORM_VIEW_PATH, model);
    }

    private UpdateProfileViewModel GetNewUpdateProfileViewModel(UpdateProfileViewModel model, GuidesMember guidesMember, string successMessage) =>
        new()
        {
            Title = model.Title,
            EmailAddress = guidesMember.Email ?? string.Empty,
            UserName = guidesMember.UserName ?? string.Empty,
            Created = guidesMember.Created,
            FullName = guidesMember.FullName,
            GivenName = guidesMember.GivenName,
            FamilyName = guidesMember.FamilyName,
            FamilyNameFirst = guidesMember.FamilyNameFirst,
            FavoriteCoffee = guidesMember.FavoriteCoffee,
            SubmitButtonText = model.SubmitButtonText,
            SuccessMessage = successMessage,
        };
}
```

Make sure to retrieve the currently signed-in member, rather than retrieving the member based on a submitted field. This way, nobody can update a different member’s details by forging a request to submit a username or email that is not their own.
C#
**~/Features/Membership/Services/IMembershipService.cs**
Copy
```
...
/// <summary>
/// Updates the profile of a member.
/// </summary>
/// <param name="member">Member to update.</param>
/// <param name="updateProfileViewModel">ViewModel with updated fields.</param>
/// <returns></returns>
Task<IdentityResult> UpdateMemberProfile(GuidesMember member, UpdateProfileViewModel updateProfileViewModel);
...
```

C#
**~/Features/Membership/Services/MembershipService.cs**
Copy
```
...
/// <inheritdoc />
public async Task<IdentityResult> UpdateMemberProfile(GuidesMember guidesMember, UpdateProfileViewModel updateProfileViewModel)
{
    guidesMember.GivenName = updateProfileViewModel.GivenName;
    guidesMember.FamilyName = updateProfileViewModel.FamilyName;
    guidesMember.FamilyNameFirst = updateProfileViewModel.FamilyNameFirst;
    guidesMember.FavoriteCoffee = updateProfileViewModel.FavoriteCoffee;

    SynchronizeContact(guidesMember);

    return await userManager.UpdateAsync(guidesMember);
}
...
```

## Create the _Profile page_ page template
Now that we have the view component and the controller action it posts to, we need to use the view component somewhere to test this functionality.
Let’s create a bare-bones page template so that editors can see the profile page in the content tree.
### Start with a content type
While we could make a template that applies to an existing content type, let’s create a dedicated _Profile page_ content type and make sure the template only works for this type. This way, we can avoid cluttering the template options when editors are creating landing pages.
Create a new content type with the following properties:
  * **Content type name:** Profile page
  * **Namespace:** TrainingGuides
  * **Name:** ProfilePage
  * **Icon:** xp-personalisation
  * **Use for:** Pages
  * **Include in routing:** True (enabled)
  * **Short code name:** TrainingGuidesProfilePage


You don’t need to add anything on the **Fields** tab, since we haven’t defined ways for editors to configure the profile page.
Now run the [Code generation tool](/documentation/developers-and-admins/api/generate-code-files-for-system-objects) to generate a C# class for this content type in your project:
CMD
**.NET CLI**
Copy
```
dotnet run -- --kxp-codegen --type "PageContentTypes" --include "TrainingGuides.ProfilePage"
```

### Define and register the template
In your project, add a new _ProfilePage_ folder within the _Profile_ directory, for files related to our new content type and its template.
Since we already created a reusable view component for the profile functionality, defining the template’s display will be simple:
cshtml
**ProfilePagePageTemplate.cshtml**
Copy
```
@using TrainingGuides.Web.Features.Membership.Profile

<vc:update-profile />
```

Then, you can use the `RegisterPageTemplate` attribute in a new _.cs_ file to tell Xperience about the template:
C#
**ProfilePagePageTemplate.cs**
Copy
```
using Kentico.PageBuilder.Web.Mvc.PageTemplates;
using TrainingGuides;
using TrainingGuides.Web.Features.Membership.Profile;

[assembly: RegisterPageTemplate(
    identifier: ProfilePagePageTemplate.IDENTIFIER,
    name: "Profile page content type template",
    customViewName: "~/Features/Membership/Profile/ProfilePage/ProfilePagePageTemplate.cshtml",
    ContentTypeNames = [ProfilePage.CONTENT_TYPE_NAME],
    IconClass = "xp-personalisation")]

namespace TrainingGuides.Web.Features.Membership.Profile;
public static class ProfilePagePageTemplate
{
    public const string IDENTIFIER = "TrainingGuides.ProfilePagePageTemplate";
}
```

Use the `ContentTypeNames` parameter to make sure only pages of the _Profile page_ type can use the template.
### Serve the template from a controller
Now we need to tell Xperience’s [Content tree-based router](/documentation/developers-and-admins/development/routing/content-tree-based-routing) to use a template for the _Profile page_ content type.
Create a controller that returns a `TemplateResult` from its `Index` action and register it with the router.
C#
**ProfilePageController.cs**
Copy
```
using Kentico.Content.Web.Mvc.Routing;
using Kentico.PageBuilder.Web.Mvc.PageTemplates;
using Microsoft.AspNetCore.Mvc;
using TrainingGuides;

[assembly: RegisterWebPageRoute(
    contentTypeName: ProfilePage.CONTENT_TYPE_NAME,
    controllerType: typeof(TrainingGuides.Web.Features.Membership.Profile.ProfilePageController))]

namespace TrainingGuides.Web.Features.Membership.Profile;
public class ProfilePageController : Controller
{
    public IActionResult Index() => new TemplateResult();
}
```

### Add a page that uses the template
With the template now bound to the content type, you can create a new _Profile page_ in Xperience and assign the new template.
Your browser does not support the video tag. 
Make sure to secure the page by enabling the **Requires authentication** checkbox in the **Membership** section of its **Properties** tab.
[![Screenshot of the page’s properties](/docsassets/guides/create-a-profile-page/SecuredProfilePage.png)](/docsassets/guides/create-a-profile-page/SecuredProfilePage.png)
## Consider the possibilities
If you want to expand on the functionality here, you can create allows members to change their email address, and tie it to a button on the profile page.
You can create new methods in your membership service that use the `UserManager<T>`’s `GenerateChangeEmailTokenAsync` and `ChangeEmailAsync` methods in a flow similar to the existing [password reset process](https://github.com/Kentico/xperience-by-kentico-training-guides/tree/finished/src/TrainingGuides.Web/Features/Membership/Widgets/ResetPassword) in the [finished branch](https://github.com/Kentico/xperience-by-kentico-training-guides/tree/finished) of the _Training guides_ repository. Send an email with a link that contains the token.
You can further expand the functionality by allowing members to define a recovery email address, or a phone number to use in case their primary email is no longer accessible.
## What’s next?
The [next guide in this series](/guides/development/members/require-authentication-for-certain-content) will cover the process of hiding certain content from unauthenticated visitors.
![]()
[]()[]()
