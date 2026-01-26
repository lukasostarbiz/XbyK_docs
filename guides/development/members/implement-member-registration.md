---
source: https://docs.kentico.com/guides/development/members/implement-member-registration
scrape_date: 2026-01-26
---

  * [Home](/guides)
  * [Development](/guides/development)
  * [Members](/guides/development/members)
  * Implement a member registration widget 


# Implement a member registration widget
Many websites allow visitors to register and sign in for exclusive access to premium content. You can add this functionality to your website channels in Xperience by Kentico by implementing [Members](/documentation/business-users/members) in your site.
Let’s dive into the first step of this process, expanding upon the standard approach by creating a **reusable widget** that allows members to register via [Forms authentication](/documentation/developers-and-admins/development/registration-and-authentication/forms-authentication). It also includes an overview of how to apply the same approach to a [Sign-in widget](#apply-the-same-approach-to-authentication).
Integrating the registration form into a widget requires extra complexity compared to using a standard routed MVC view.
The registration widget will allow editors to do the following:
  * Easily link to a registration page from components like the _Page selector_ , _Combined content selector_ , and _Rich text editor_.
  * Seamlessly integrate the registration form into relevant pages, such as landing pages for campaigns driving member registration.
  * Configure the design of the components, for example, deciding which fields the form should include and defining custom field labels.


However, it requires extra development. The widget uses hidden form fields to make sure its configuration persists through server-side validation.
To see simpler examples using standard MVC routed views for membership functionality, check out the following resources:
  * The [Xperience by Kentico Community Portal](https://github.com/Kentico/community-portal/tree/e7c0d98cf533899ec79f7a1389bf280b54dbddd2/src/Kentico.Community.Portal.Web/Features/Registration) on GitHub.
  * The **Dancing Goat** sample project, available through the `Kentico.Xperience.Templates` _.NET Templates_ package.


## Before you start
This guide requires the following:
  * Familiarity with [C#](https://learn.microsoft.com/en-us/dotnet/csharp/), [.NET Core](https://learn.microsoft.com/en-us/dotnet/), [.NET Identity](https://learn.microsoft.com/en-us/aspnet/core/security/authentication/identity), [Dependency injection](https://learn.microsoft.com/en-us/dotnet/core/extensions/dependency-injection), and the [MVC pattern](https://learn.microsoft.com/en-us/aspnet/core/mvc/overview).
  * Experience with [Widgets](/guides/development/page-builder/build-simple-cta-widget).
  * A running instance of Xperience by Kentico, preferably [30.11.1](/documentation/changelog) or higher. 
Some features covered in the training guides may not work in older versions. 


**Code samples**
You can find a project with completed, working versions of code samples from this guide and others in the [finished branch](https://github.com/Kentico/xperience-by-kentico-training-guides/tree/finished) of the _Training guides_ repository.
The [main branch](https://github.com/Kentico/xperience-by-kentico-training-guides) of the repository provides a starting point to code along with the guides.
The code samples in this guide are for [.NET 8](https://learn.microsoft.com/en-us/dotnet/core/whats-new/dotnet-8/overview) only.
They come from a project that uses [implicit using directives](https://learn.microsoft.com/en-us/dotnet/core/project-sdk/overview#implicit-using-directives). You may need to add additional `using` directives to your code if your project does not use this feature.
## Examine the requirements
For this guide’s example, imagine you have the following requirements for member registration:
  1. A visitor can fill out a form to become a member and create an account.
  2. If a member shares their full name, they can choose the display order.
  3. Editors can include the registration form in campaign landing pages.
  4. Editors can specify their own labels for the fields in each language.
  5. Editors can choose to display the form with only required fields, or to include optional fields.


You can [extend the member class with custom fields](/documentation/developers-and-admins/development/registration-and-authentication/add-fields-to-member-objects) to handle the name order requirement.
All of the requirements regarding editors’ control of the registration forms can be met by making the registration form a [widget](/documentation/developers-and-admins/development/builders/page-builder/widgets-for-page-builder): editors can place the widget on Page Builder zones in landing pages and use widget properties to configure the labels and fields to display in the registration form.
## Extend the Member class
To start, let’s [add custom fields](/documentation/developers-and-admins/development/registration-and-authentication/add-fields-to-member-objects) that allow members to configure the order of their full names. While we’re at it, we’ll also add a field to mirror the “favorite coffee” example from our [training guide about customizing contacts](/guides/development/customizations-and-integrations/add-custom-contact-field).
### Define the module fields
Navigate to the **Modules** application in Xperience, and edit the **Membership** module. Choose **Classes → Member → Database columns**. Add text fields for the given name, family name, and favorite coffee of your members, along with a boolean field to indicate if the family name should go first.
  * Given name 
    * **Field name** : GuidesMemberGivenName
    * **Data type** : Text
    * **Size** : 100
    * **Required** : False (disabled)
  * Family name 
    * **Field name** : GuidesMemberFamilyName
    * **Data type** : Text
    * **Size** : 100
    * **Required** : False (disabled)
  * Family name first 
    * **Field name** : GuidesMemberFamilyNameFirst
    * **Data type** : Boolean (Yes/No)
    * **Required** : False (disabled)
    * **Default value** : False (disabled)
  * Favorite coffee 
    * **Field name** : GuidesMemberFavoriteCoffee
    * **Data type** : Text
    * **Size** : 100
    * **Required** : False (disabled)


Now, these custom columns exist in the database. You can read and manipulate them using the `GetValue` and `SetValue` methods of a `MemberInfo` object in code.
### Adjust the UI
While we can access our custom fields in code, they will not show up in the Xperience admin UI without further adjustment.
After defining the fields in the **Modules** application, switch to the **UI forms** tab of the _Member_ class and click on the **Edit** form. On its **Fields** tab, add a **New field** for each of the database columns you created previously, defining appropriate captions and form components.
[![Screenshot of the fields of the Member class’s ‘Edit’ UI form](/docsassets/guides/implement-member-registration/MemberUIForm.png)](/docsassets/guides/implement-member-registration/MemberUIForm.png)
### Extend the ApplicationUser class
Xperience comes with a class called `ApplicationUser` out of the box, which ties the Xperience `MemberInfo` object to [.NET Identity](https://learn.microsoft.com/en-us/aspnet/core/security/authentication/identity). However, this class is in an assembly and cannot be directly modified.
To add our custom fields and any other functionality, we need to extend this class.
Start by creating a folder called _Membership_ under the _Features_ folder of the _TrainingGuides.Web_ project. Here you can extend `ApplicationUser` with a new class called `GuidesMember`, featuring properties for our custom fields.
Then, define methods to map between the custom class and the `MemberInfo` object from Xperience by Kentico.
C#
**GuidesMember.cs**
Copy
```
using CMS.Membership;
using Kentico.Membership;

namespace TrainingGuides.Web.Features.Membership;

public class GuidesMember : ApplicationUser
{
    public string GivenName { get; set; } = string.Empty;
    public string FamilyName { get; set; } = string.Empty;
    public bool FamilyNameFirst { get; set; } = false;
    public string FullName =>
        (GivenName, FamilyName) switch
        {
            ("", "") => string.Empty,
            (string given, "") => given,
            ("", string family) => family,
            (string given, string family) =>
                FamilyNameFirst ? $"{family} {given}" : $"{given} {family}",
            (null, null) or _ => string.Empty,
        };

    public string FavoriteCoffee { get; set; } = string.Empty;
    public DateTime Created { get; set; }

    public override void MapToMemberInfo(MemberInfo target)
    {
        if (target is null)
        {
            throw new ArgumentNullException(nameof(target));
        }

        /*
         * base.MapToMemberInfo will set target.MemberPassword every time
         * however we do not want to set it if PasswordHash is null,
         * and this stores the original so we can revert it
         */
        string originalPasswordHash = target.MemberPassword;

        base.MapToMemberInfo(target);

        if (PasswordHash is null)
        {
            target.MemberPassword = originalPasswordHash;
        }

        _ = target.SetValue("GuidesMemberGivenName", GivenName);
        _ = target.SetValue("GuidesMemberFamilyName", FamilyName);
        _ = target.SetValue("GuidesMemberFamilyNameFirst", FamilyNameFirst);
        _ = target.SetValue("GuidesMemberFavoriteCoffee", FavoriteCoffee);
    }

    public override void MapFromMemberInfo(MemberInfo source)
    {
        base.MapFromMemberInfo(source);

        GivenName = source.GetValue("GuidesMemberGivenName", string.Empty);
        FamilyName = source.GetValue("GuidesMemberFamilyName", string.Empty);
        FamilyNameFirst = source.GetValue("GuidesMemberFamilyNameFirst", false);
        FavoriteCoffee = source.GetValue("GuidesMemberFavoriteCoffee", string.Empty);
        Created = source.MemberCreated;
    }

    public static GuidesMember FromMemberInfo(MemberInfo memberInfo)
    {
        var guidesMember = new GuidesMember();
        guidesMember.MapFromMemberInfo(memberInfo);

        return guidesMember;
    }
}

public static class MemberInfoExtensions
{
    public static GuidesMember AsGuidesMember(this MemberInfo member) =>
        GuidesMember.FromMemberInfo(member);
}
```

## Set up Identity
Now, to enable .NET Identity, we need to [Configure registration and authentication](/documentation/developers-and-admins/development/registration-and-authentication).
C#
**Program.cs**
Copy
```
using Kentico.Membership;
using Microsoft.AspNetCore.Identity;
...
var builder = WebApplication.CreateBuilder(args);
...
builder.Services
    .AddIdentity<GuidesMember, NoOpApplicationRole>(options =>
    {
        options.SignIn.RequireConfirmedEmail = false; // Change this to true once you implement email confirmation as a part of the registration process.
        options.User.RequireUniqueEmail = true;
    })
    .AddUserStore<ApplicationUserStore<GuidesMember>>()
    .AddRoleStore<NoOpApplicationRoleStore>()
    .AddUserManager<UserManager<GuidesMember>>()
    .AddSignInManager<SignInManager<GuidesMember>>();
...
builder.Services.AddAuthorization();
...
// After calling InitKentico and UseStaticFiles
// Make sure to call the middleware in this order
// Note that some of these lines are already present in a different order in the main branch.
app.UseCookiePolicy();
app.UseAuthentication();
app.UseKentico();
app.UseAuthorization();
...
```

**Differences in the finished branch code**
If you look at the code in the [finished branch of our repository](https://github.com/Kentico/xperience-by-kentico-training-guides/blob/finished/src/TrainingGuides.Web/Program.cs), you’ll notice additional calls, such as `AddDefaultTokenProviders` and `ConfigureApplicationCookie`.
These relate to other adjacent functionality like restricted content and email confirmation as a part of the registration process. You can find working examples of many identity features in the repository, and guides about some of them later in this series.
## Create a service
To contain functionality related to membership operations, such as creating and managing members, let’s make a service.
Create a _Services_ folder under the _Membership_ folder in your project and add an `IMembershipService` interface.
C#
**IMembershipService.cs**
Copy
```
namespace TrainingGuides.Web.Features.Membership.Services;

/// <summary>
/// Interface for membership services.
/// </summary>
public interface IMembershipService
{

}
```

Then, add a class to hold implementations of these members, and register the implementation.
C#
**MembershipService.cs**
Copy
```
namespace TrainingGuides.Web.Features.Membership.Services;

public class MembershipService : IMembershipService
{

}
```

C#
**~/ServiceCollectionExtensions.cs**
Copy
```
using TrainingGuides.Web.Features.Membership.Services;
...
public static void AddTrainingGuidesServices(this IServiceCollection services)
{
    ...
    services.AddScoped<IMembershipService, MembershipService>();
    ...

}
```

Make sure to register your service as **scoped** ; some crucial parts of .NET Identity are scoped services, and we’ll want to utilize them in our code.
We’ll expand this service as needed throughout the guide, but for now let’s move on to the widget.
## Consider your region’s data protection laws
Since Member objects in Xperience contain personally identifiable information about the humans they represent, real-life implementations of member registration may need to include a [consent](/documentation/developers-and-admins/data-protection/consent-management) to store that data if your company does business in a region with laws governing data protection.
It is likely that this consent will need to be separate and have distinct verbiage from consents related to contact tracking, such as the cookie consents from the [Data protection series](/guides/development/data-protection).
Managing consents alongside members is outside the scope of this example, but you can find information about working with consents in the Xperience by Kentico [documentation](/documentation/developers-and-admins/data-protection/consent-development).
## Craft the widget
Let’s start the process of building our registration widget.
In the _TrainingGuides.Web_ project, create a _~/Features/Membership/Widgets/Registration_ folder. This will contain the files relating to our registration widget.
### Define the widget properties
When creating a widget, it’s helpful to start by considering what editors should be able to configure.
According to our [requirements](#examine-the-requirements), editors need the ability to display a minimal registration form with only required fields, or a fully detailed form that includes optional fields. They also need to define the form’s labels.
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

## Handle the form data
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
[![Screenshot of the registration form filled out on a page](/docsassets/guides/implement-member-registration/RegistrationForm.png)](/docsassets/guides/implement-member-registration/RegistrationForm.png)
[![Screenshot of the resulting member appearing in the member listing](/docsassets/guides/implement-member-registration/MembersApp.png)](/docsassets/guides/implement-member-registration/MembersApp.png)
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


## What’s next?
The [next guide in this series](/guides/development/members/map-member-to-contact) will cover the process of creating and maintaining a relationship between members and contacts.
![]()
[]()[]()
