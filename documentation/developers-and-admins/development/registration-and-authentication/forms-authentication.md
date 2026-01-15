# Forms authentication
  * [ Copy page link ](documentation/developers-and-admins/development/registration-and-authentication/forms-authentication#) | [Get HelpService ID](documentation/developers-and-admins/development/registration-and-authentication/forms-authentication#)
Core MVC 5


[✖](documentation/developers-and-admins/development/registration-and-authentication/forms-authentication# "Close page link panel") [Copy to clipboard](documentation/developers-and-admins/development/registration-and-authentication/forms-authentication#)
Forms authentication is a method of authenticating users in web applications where users are required to enter their credentials (such as a username and password) on a login page, which is then validated against a database or other data source to confirm their identity. The user’s credentials reach the server via a form submitted from the login page (hence the naming).
Once the user is authenticated, a session is created for them and they can access protected pages and features of the application. The user’s identity is typically stored in an encrypted cookie for the duration of the session.
Xperience by Kentico uses [ASP.NET Identity](https://learn.microsoft.com/en-us/aspnet/identity/overview/getting-started/introduction-to-aspnet-identity) to manage membership in web applications. The types and API to set up forms authentication is located in the **Kentico.Membership** namespace (provided as part of the _Kentico.Xperience.WebApp_ [NuGet package](documentation/developers-and-admins/development/website-development-basics/configure-new-projects/xperience-by-kentico-nuget-packages)).
## Prerequisites
Before implementing forms authentication, you must [enable and configure ASP.NET Identity](documentation/developers-and-admins/development/registration-and-authentication) in your web application.
## Implement forms authentication
Use the following approach to develop actions that allow visitors to register on your website:
  * [Registration](documentation/developers-and-admins/development/registration-and-authentication/forms-authentication#registration)
  * [Sign in and sign out](documentation/developers-and-admins/development/registration-and-authentication/forms-authentication#sign-in-and-sign-out)
  * [Password policy](documentation/developers-and-admins/development/registration-and-authentication/forms-authentication#password-policy)
  * [Password reset](documentation/developers-and-admins/development/registration-and-authentication/forms-authentication#password-reset)


### Registration
Create a new controller class in your project or edit an existing one. Implement two registration actions – one basic GET action to display the registration form and a second POST action to handle creating new users when the form is submitted. Use conventional Identity APIs to implement the registration flow. For more information, see the comments in the following code snippet:
C#
**Registration actions**
Copy
```
public class AccountController : Controller
{
    private readonly ILogger<AccountController> logger;
    private readonly UserManager<ApplicationUser> userManager;
    private readonly SignInManager<ApplicationUser> signInManager;

    // Provides instances of required services using dependency injection
    public AccountController(UserManager<ApplicationUser> userManager,
                             SignInManager<ApplicationUser> signInManager,
                             ILogger<AccountController> logger)
    {
        this.userManager = userManager;
        this.signInManager = signInManager;
        this.logger = logger;
    }

    ...

    // GET: Account/Register
    // Returns a basic view with the registration form
    public ActionResult Register()
    {
        return View();
    }

    // POST: Account/Register
    // Creates a member account
    [HttpPost]
    [ValidateAntiForgeryToken]
    public async Task<IActionResult> Register(RegisterViewModel model)
    {

        if (!ModelState.IsValid)
        {
            return View(model);
        }

        // Holds user registration data. Map the properties from
        // the registration form to the desired fields.
        var member = new ApplicationUser
        {
            UserName = model.UserName,
            Email = model.Email,
            // Enables the member account. In simple registration flows,
            // always set to true, otherwise the account will not be able to sign in.
            // When implementing a multi-step registration process (e.g., with email confirmation),
            // more robust logic is required. See the 'Email confirmation' section for details.
            Enabled = true
        };

        var registerResult = new IdentityResult();
        try
        {
            // Creates the member account
            registerResult = await userManager.CreateAsync(member, model.Password);
        }
        catch (Exception ex)
        {
            logger.LogError(new EventId(0, "REGISTRATION_ERROR"), ex, $"Registration failed for user {model.UserName}");
            ModelState.AddModelError(string.Empty, "Registration failed.");
        }

        if (registerResult.Succeeded)
        {
            // Signs the registered account in to the site
            var signInResult = await signInManager.PasswordSignInAsync(member, model.Password, true, false);

            if (signInResult.Succeeded)
            {
                // Redirects to the site root
                return Redirect("/");
            }
        }

        foreach (var error in registerResult.Errors)
        {
            ModelState.AddModelError(string.Empty, error.Description);
        }

        return View(model);
    }
}
```

In Xperience, registered users are stored as members in the **CMS_Member** database table and displayed in the administration’s **Members** application. In the example above, `ApplicationUser` represents the Xperience member object that is being created. The default implementation lets you collect only basic data (username, email, password). To collect a broader set of visitor data, the `ApplicationUser` class can be extended with additional fields (first name, title, etc.). See [Add fields to member objects](documentation/developers-and-admins/development/registration-and-authentication/add-fields-to-member-objects). 
Next, create a view model for the Register action (`RegisterViewModel` in the example above). The view model:
  * Passes parameters from the registration form (name, email address, password and confirmation field).
The name is required for every account. See the [IUserValidator](https://learn.microsoft.com/en-us/dotnet/api/microsoft.aspnetcore.identity.iuservalidator-1) implementation in the default Identity implementation.
  * Uses data annotations to define validation and formatting rules for the data. See [System.ComponentModel.DataAnnotations](https://learn.microsoft.com/en-us/dotnet/api/system.componentmodel.dataannotations) for more information about the available annotation attributes.


C#
**Registration view model**
Copy
```
using System.ComponentModel;
using System.ComponentModel.DataAnnotations;

public class RegisterViewModel
{
    [DataType(DataType.Text)]
    [Required(ErrorMessage = "Enter your username")]
    [DisplayName("User name")]
    public string UserName { get; set; }

    [DataType(DataType.EmailAddress)]
    [Required(ErrorMessage = "Enter your email")]
    [DisplayName("Email")]
    [EmailAddress(ErrorMessage = "Enter a valid email address")]
    public string Email { get; set; }

    [DataType(DataType.Password)]
    [DisplayName("Password")]
    [Required(ErrorMessage = "Enter a password")]
    public string Password { get; set; }

    [DataType(DataType.Password)]
    [DisplayName("Confirm your password")]
    [Required(ErrorMessage = "Enter the password again")]
    [Compare("Password", ErrorMessage = "The entered passwords do not match")]
    public string PasswordConfirmation { get; set; }
}
```

As the last step, design the user interface required for registration on your website:
  * Create a view for the `Register` action and display an appropriate registration form. Use a strongly typed view based on your registration view model. _Note:_ The following example uses [Bootstrap](https://getbootstrap.com/) to provide basic formatting.


cshtml
**Example - registration form**
Copy
```
<form asp-action="Register" method="post">
    <div class="form-group">
        <span class="text-dark"><label asp-for="UserName"></label></span>
        <input asp-for="UserName" class="form-control" />
        <span asp-validation-for="UserName" class="text-danger"></span>
    </div>

    <div class="form-group">
        <span class="text-dark"><label asp-for="Email"></label></span>
        <input asp-for="Email" class="form-control" />
        <span asp-validation-for="Email" class="text-danger"></span>
    </div>

    <div class="form-group">
        <span class="text-dark"><label asp-for="Password"></label></span>
        <input asp-for="Password" class="form-control" />
        <span asp-validation-for="Password" class="text-danger"></span>
    </div>

    <div class="form-group">
        <span class="text-dark"><label asp-for="PasswordConfirmation"></label></span>
        <input asp-for="PasswordConfirmation" class="form-control" />
        <span asp-validation-for="PasswordConfirmation" class="text-danger"></span>
    </div>

    <input type="submit" value="Register" class="btn btn-primary" />
</form>
```

Visitors can now register new accounts on your site. Upon successful registration, the system creates the account in the connected Xperience database, **CMS_Member** table.
### Sign in and sign out
The next part of the authentication flow enables registered accounts to sign in to the site. With forms authentication, this is done through another form that validates submitted credentials against the database of existing accounts. If the submitted information matches an existing account, the visitor is signed in. Otherwise, an error occurs. 
To implement the sign in flow on your website:
  * Create a sign-in form that allows registered users to enter their credentials.
  * Implement two authentication actions: 
    * A basic GET action to display the authentication form.
    * A POST action to handle the authentication.


Use conventional Identity APIs to implement the authentication flow. For more information, see the comments in the following code snippet:
C#
**Sign in controller actions**
Copy
```
public class AccountController : Controller
{
    private readonly ILogger<AccountController> logger;
    private readonly UserManager<ApplicationUser> userManager;
    private readonly SignInManager<ApplicationUser> signInManager;

    // Provides instances of required services using dependency injection
    public AccountController(UserManager<ApplicationUser> userManager,
                             SignInManager<ApplicationUser> signInManager,
                             ILogger<AccountController> logger)
    {
        this.userManager = userManager;
        this.signInManager = signInManager;
        this.logger = logger;
    }

    ...

    // GET: Account/SignIn
    [HttpGet]
    [AllowAnonymous]
    public ActionResult SignIn()
    {
        return View();
    }

    // POST: Account/SignIn
    [HttpPost]
    [AllowAnonymous]
    [ValidateAntiForgeryToken]
    public async Task<IActionResult> SignIn(SignInViewModel model, string returnUrl)
    {
        if (!ModelState.IsValid)
        {
            return View(model);
        }

        var signInResult = SignInResult.Failed;
        try
        {
            // Signs the visitor in using Identity APIs
            signInResult = await signInManager.PasswordSignInAsync(model.UserName, model.Password, model.RememberMe, false);
        }
        catch (Exception ex)
        {
            logger.LogError(new EventId(0, "SIGNIN_ERROR"), ex, $"Sign in failed for user {model.UserName}");
        }

        if (signInResult.Succeeded)
        {
            // Redirects successfully signed-in members to the home page
            return RedirectToAction(nameof(HomeController.Index), "Home");
        }

        // Returns an error in case the sign-in fails
        ModelState.AddModelError(string.Empty, "We couldn't sign you in using the provided credentials.");
        return View(model);
    }

    // POST: Account/Logout
    // Signs the account out of the system
    [Authorize]
    [HttpPost]
    [ValidateAntiForgeryToken]
    public ActionResult Logout()
    {
        signInManager.SignOutAsync();
        return Redirect("/");
    }
}
```

The controller now contains actions required to handle user authentication. The view model used by the `SignIn` action:
C#
**SignInViewModel.cs**
Copy
```
using System.ComponentModel;
using System.ComponentModel.DataAnnotations;

public class SignInViewModel
{
    [Required(ErrorMessage = "Enter your user name")]
    [DisplayName("User name")]
    public string UserName { get; set; }

    [DataType(DataType.Password)]
    [DisplayName("Password")]
    public string Password { get; set; }

    [DisplayName("Stay signed in")]
    public bool RememberMe { get; set; }
}
```

As the last step, design the user interface:
  * Create a view for the `SignIn` action and display an appropriate form. Use a strongly typed view based on your registration view model. _Note:_ The following example uses [Bootstrap](https://getbootstrap.com/) to provide basic formatting.


cshtml
**Example - sign-in form**
Copy
```
<form asp-controller="Account" asp-action="SignIn" method="post">
    <div class="form-group">
        <span><label asp-for="UserName"></label></span>
        <input asp-for="UserName" class="form-control" />
        <span asp-validation-for="UserName" class="text-danger"></span>
    </div>

    <div class="form-group">
        <span class="text-dark"><label asp-for="Password"></label></span>
        <input asp-for="Password" class="form-control" />
        <span asp-validation-for="Password" class="text-danger"></span>
    </div>

    <div class="form-group">
        <span class="text-dark"><label asp-for="RememberMe"></label></span>
        <input asp-for="RememberMe" class="form-control" />
        <span asp-validation-for="RememberMe" class="text-danger"></span>
    </div>

    <input type="submit" value="Sign in" class="btn btn-primary" />
</form>
```

### Password policy
By default, ASP.NET Identity uses a relatively strong password policy that requires passwords to be at least six characters long and contain at least one non-alphanumeric character, one digit, and one lowercase and uppercase character. However, developers can modify these settings to meet their specific security requirements.
Here are some of the common settings that you can configure:
  1. Minimum password length (`RequiredLength`) – this setting specifies the minimum number of characters required for a user’s password.
  2. Require non-alphanumeric characters (`RequireNonAlphanumeric`) – this setting specifies whether the password should contain at least one non-alphanumeric character, such as a symbol or punctuation mark.
  3. Require digit (`RequireDigit`) – this setting specifies whether the password should contain at least one digit.
  4. Require lowercase and uppercase characters (`RequireUppercase, RequireLowercase`) – this setting specifies whether the password should contain both lowercase and uppercase characters.


See [Microsoft’s ASP.NET Identity documentation](https://learn.microsoft.com/en-us/aspnet/core/security/authentication/identity-configuration#password) for all password options.
Configure these settings in the application’s Identity configuration in **Program.cs** : 
C#
**Program.cs**
Copy
```
builder.Services.AddIdentity<ApplicationUser, NoOpRoleStore>(options =>
{
    options.Password.RequireDigit = false;
    options.Password.RequireNonAlphanumeric = true;
    options.Password.RequiredLength = 8;
    options.Password.RequireUppercase = false;
    options.Password.RequireLowercase = false;
})
```

### Password reset
The ability to reset passwords is an important part of any website that allows visitors to register accounts and sign in. It is expected and commonly used as a recovery mechanism by users who forget their password.
Before starting with the implementation, add the `AddDefaultTokenProviders` method to your `AddIdentity` call in **Program.cs**. The call ensures the default Identity implemetnation of password reset generators for the integration.
C#
**Program.cs**
Copy
```
services.AddIdentity<ApplicationUser, NoOpApplicationRole>(options =>
{
     // ...
})
    .AddUserStore<ApplicationUserStore<ApplicationUser>>()
    .AddRoleStore<NoOpApplicationRoleStore>()
    .AddUserManager<UserManager<ApplicationUser>>()
    .AddSignInManager<SignInManager<ApplicationUser>>()
    .AddDefaultTokenProviders();
```

Now, to implement password reset on your site, add the following controller actions:
  * A GET action that displays an email address entry form.
  * A POST action that handles sending of password reset emails to the specified address.
  * An action that handles the password reset requests – validates the reset token and displays a password reset form.
  * A POST action that accepts the input of the password reset form.


Use conventional Identity APIs to implement the authentication flow. For more information, see the comments in the following code snippet:
C#
Copy
```
using System;
using System.Web;
using System.Threading.Tasks;

using Microsoft.AspNetCore.Mvc;
using Microsoft.AspNetCore.Identity;

using CMS.EmailEngine;

using Kentico.Membership;

public class PasswordResetController : Controller
{
    private readonly UserManager<ApplicationUser> userManager;
    private readonly IEmailService messageService;

    public PasswordResetController(UserManager<ApplicationUser> userManager,
                             IEmailService messageService)
    {
        this.userManager = userManager;
        this.messageService = messageService;
    }

    // Allows visitors to submit their email address and request a password reset
    public IActionResult PasswordResetRequest()
    {
        return View();
    }

    // Generates a password reset request for the specified email address.
    [HttpPost]
    [ValidateAntiForgeryToken]
    public async Task<IActionResult> RequestPasswordReset(PasswordResetRequestViewModel model)
    {
        // Validates the received email address based on the view model
        if (!ModelState.IsValid)
        {
            return View(model);
        }

        // Gets the user entity for the specified email address
        ApplicationUser user = await userManager.FindByEmailAsync(model.Email);

        if (user != null)
        {
            // Generates a password reset token for the user
            string token = await userManager.GeneratePasswordResetTokenAsync(user);

            // URL-encodes the token
            string encodedToken = HttpUtility.UrlEncode(token);

            // Prepares the URL of the password reset link (targets the "PasswordReset" action)
            // Fill in the name of your controller
            string resetUrl = Url.Action(nameof(PasswordResetController.PasswordReset),
                                         "PasswordReset",
                                         new { userId = user.Id, token = encodedToken },
                                         Request.Scheme);

            // Creates and sends the password reset email to the user's address
            await messageService
                .SendEmail(new EmailMessage()
                {
                    From = "admin@localhost.local",
                    Recipients = user.Email,
                    Subject = "Password reset request",
                    Body = $"To reset your account's password, click <a href=\"{resetUrl}\">here</a>."
                });
        }

        // Displays a view asking the visitor to check their email and click the password reset link.
        // General security practices recommend never confirming whether the reset email was sent successfully.
        // For this reason, the method always terminates by displaying a generic message.
        return RedirectToAction(nameof(CheckYourEmail));
    }

    public IActionResult CheckYourEmail()
    {
        return View();
    }

    // Handles the links that users click in password reset emails.
    // If the request parameters are valid, displays a form where users can reset their password.
    public async Task<IActionResult> PasswordReset(int? userId, string token)
    {
        // Handles the case when the token is missing from the URL
        if (String.IsNullOrEmpty(token))
        {
            return NotFound();
        }

        // Decodes the token from the URL
        token = HttpUtility.UrlDecode(token);

        // Gets the member that requested the password reset
        ApplicationUser user = await userManager.FindByIdAsync(userId.ToString());

        try
        {
            // Verifies the parameters of the password reset request
            // True if the token is valid for the specified user, false if the token is invalid or has expired
            // By default, the generated tokens are single-use and expire in 1 day
            if (await userManager.VerifyUserTokenAsync(
                                    user: user,
                                    tokenProvider: userManager.Options.Tokens.PasswordResetTokenProvider,
                                    purpose: UserManager<ApplicationUser>.ResetPasswordTokenPurpose,
                                    token: token)
               )
            {
                // If the password request is valid, displays the password reset form
                var model = new ResetPasswordViewModel
                {
                    UserId = userId.Value,
                    Token = token
                };

                return View(model);
            }

            // If the password request is invalid, returns a view informing the user
            return View("PasswordResetResult", ViewBag.Success = false);
        }
        catch (InvalidOperationException)
        {
            // An InvalidOperationException occurs if a user with the given ID is not found
            // Returns a view informing the user that the password reset request is not valid
            return View("PasswordResetResult", ViewBag.Success = false);
        }
    }

    // Resets the user's password based on the posted data.
    // Accepts the user ID, password reset token and the new password via the ResetPasswordViewModel.
    [HttpPost]
    [ValidateAntiForgeryToken]
    public async Task<IActionResult> ResetPasswordResult(ResetPasswordViewModel model)
    {
        // Validates the received password data based on the view model
        if (!ModelState.IsValid)
        {
            return View(model);
        }

        bool result = false;

        ApplicationUser user = await userManager.FindByIdAsync(model.UserId.ToString());

        // Changes the user's password if the provided reset token is valid
        if (user != null && (await userManager.ResetPasswordAsync(user, model.Token, model.Password)).Succeeded)
        {
            // If the password change was successful, displays a message informing the user
            result = true;
        }

        // Displays the result of the password reset operation
        return View("PasswordResetResult", ViewBag.Success = result);
    }
}
```

Create view models for your password reset actions and input forms:
  * For the reset request form (`PasswordResetRequestViewModel`), the view model must validate and transfer the email address value.
  * For the password reset form (`ResetPasswordViewModel`), the view model must contain the user ID, reset token and the new password.


C#
**View models used by the password reset actions**
Copy
```
public class PasswordResetRequestViewModel
{
    [DataType(DataType.EmailAddress)]
    [Required(ErrorMessage = "The email address cannot be empty.")]
    [Display(Name = "Email address")]
    [EmailAddress(ErrorMessage = "Invalid email address.")]
    [MaxLength(254, ErrorMessage = "The Email address cannot be longer than 254 characters.")]
    public string Email
    {
        get;
        set;
    }
}

public class ResetPasswordViewModel
{
    public int UserId
    {
        get;
        set;
    }

    public string Token
    {
        get;
        set;
    }

    [DataType(DataType.Password)]
    [Required(ErrorMessage = "The password cannot be empty.")]
    [DisplayName("Password")]
    [MaxLength(100, ErrorMessage = "The password cannot be longer than 100 characters.")]
    public string Password
    {
        get;
        set;
    }

    [DataType(DataType.Password)]
    [DisplayName("Password confirmation")]
    [MaxLength(100, ErrorMessage = "The password cannot be longer than 100 characters.")]
    [Compare("Password", ErrorMessage = "The entered passwords do not match.")]
    public string PasswordConfirmation
    {
        get;
        set;
    }
}
```

As the last step, design the user interface for the password reset functionality on your website:
  * Create a view for the  _PasswordResetRequest_ action that displays an email submission form.
  * Create a view that instructs users to check their email and click a link to reset their password (_CheckYourEmail_ view in the example).
  * Create a view for the  _PasswordReset_ action that displays a password reset form.
  * Create a view for the results of the  _ResetPasswordResult_ action (_PasswordResetResult_ in the example).


Password reset is now available for the application. When a user initiates password reset and submits their email address, the system sends them an email. The email contains a link (single-use with a 1-day expiration by default) that sends the user to a password reset form, where they can set a new password. The password reset form only works for users who access the URL with a valid token parameter.
## Email confirmation
ASP.NET Identity also allows you to set up a more advanced registration process that requires email confirmation (double opt-in). Email confirmation is useful when you wish to add an additional layer of legitimacy to the accounts registered in your application. This approach can help mitigate fake or spam user accounts by requiring an email address that the visitor can provably access as part of the registration process.
The account registration flow with email confirmation enabled looks as follows:
  1. The visitor submits a registration form that must contain their email address.
  2. The system sends an email with a confirmation link to the provided address.
  3. The user clicks the link in the email, proving ownership of the address.
  4. The system enables their account, allowing them to sign in.


### Implement email confirmation
In your project’s ASP.NET Identity configuration, enable email confirmation and add the `AddDefaultTokenProviders` method to your `AddIdentity` call.
C#
**Program.cs**
Copy
```
builder.Services.AddIdentity<ApplicationUser, NoOpRoleStore>(options =>
{
    ...
    options.SignIn.RequireConfirmedEmail = true;
})
    .AddDefaultTokenProviders();
```

Create a new controller class in your project or modify an existing registration flow.
C#
**Authentication flow with email confirmation**
Copy
```
private readonly ILogger<AccountController> logger;
private readonly IEmailService emailService;
private readonly UserManager<ApplicationUser> userManager;
private readonly SignInManager<ApplicationUser> signInManager;

// Obtains instances of required dependencies using constructor dependency injection
public AccountController(UserManager<ApplicationUser> userManager,
                         SignInManager<ApplicationUser> signInManager,
                         ILogger<AccountController> logger,
                         IEmailService emailService)
{
    this.userManager = userManager;
    this.signInManager = signInManager;
    this.logger = logger;
    this.emailService = emailService;
}

// Displays the registration form
// For the purposes of email confirmation, the form must collect
// the visitor's email and password at minimum.
// GET: //Account/Register
[HttpGet]
public IActionResult Register()
{
    return View();
}

// Handles user registration
// POST: Account/Register
[HttpPost]
[ValidateAntiForgeryToken]
public async Task<IActionResult> Register(RegisterViewModel model)
{

    if (!ModelState.IsValid)
    {
        return View(model);
    }

    var member = new ApplicationUser
    {
        UserName = model.UserName,
        Email = model.Email,
        // Newly registered accounts must be created as disabled to prevent them from being able to sign in.
        // Disabled accounts cannot sign in until they have confirmed their account.
        // See the remarks section on the parent page for details about 'ApplicationUser.Enabled'.
        Enabled = userManager.Options.SignIn.RequireConfirmedEmail ? false : true
    };

    var registerResult = new IdentityResult();

    try
    {
        registerResult = await userManager.CreateAsync(member, model.Password);
    }
    catch (Exception ex)
    {
        logger.LogError(new EventId(0, "REGISTRATION_ERROR"), ex, $"Registration failed for user {model.UserName}");
        ModelState.AddModelError(string.Empty, "Registration failed.");
    }

    if (registerResult.Succeeded)
    {

        if (userManager.Options.SignIn.RequireConfirmedEmail)
        {
            // Generates the confirmation token and link URL
            string confirmToken = await userManager.GenerateEmailConfirmationTokenAsync(member);
            var confirmationLink = Url.Action(nameof(ConfirmEmail), "Account",
                new { memberEmail = member.Email, confirmToken }, Request.Scheme);

            // Sends the cofirmation message using the configured email provider
            await emailService.SendEmail(new EmailMessage()
            {
                From = "admin@localhost.local",
                Recipients = member.Email,
                Subject = "Email confirmation",
                Body = $"Confirm your new account by clicking <a href=\"{confirmationLink}\">here</a>."
            });

            return RedirectToAction(nameof(VerifyEmail));
        }
        else
        {
            var signInResult = await signInManager.PasswordSignInAsync(member, model.Password, true, false);

            if (signInResult.Succeeded)
            {
                // Redirects to the Home action
                RedirectToAction(nameof(HomeController.Index), "Home");
            }
        }
    }

    foreach (var error in registerResult.Errors)
    {
        ModelState.AddModelError(string.Empty, error.Description);
    }

    return View(model);
}

// Processes email confirmation links
[HttpGet]
public async Task<ActionResult> ConfirmEmail([FromQuery] string memberEmail, [FromQuery] string confirmToken)
{
    IdentityResult confirmResult;

    ApplicationUser user = await userManager.FindByEmailAsync(memberEmail);
    try
    {
        // Verifies the confirmation parameters and enables the user account if successful
        confirmResult = await userManager.ConfirmEmailAsync(user, confirmToken);
    }
    catch (InvalidOperationException)
    {
        // An InvalidOperationException occurs if a user with the given ID is not found
        confirmResult = IdentityResult.Failed(new IdentityError() { Description = "User not found." });
    }

    if (confirmResult.Succeeded)
    {
        // If the verification was successful, displays a view informing the user that their account was activated
        return RedirectToAction(nameof(EmailConfirmed));
     }

    // Returns a view informing the user that the email confirmation failed
    return RedirectToAction(nameof(EmailConfirmationFailed));
}

// Returns a basic view informing users about the sent confirmation email
public IActionResult VerifyEmail()
{
    return View();
}

// Informs that email confirmation was successful
public IActionResult EmailConfirmed()
{
    return View();
}

// Returned in case the email confirmation fails (e.g., due to an expired token or invalid link)
public IActionResult EmailConfirmationFailed()
{
    return View();
}
```

The view model used by `Register`:
C#
**Register action view model**
Copy
```
using System.ComponentModel;
using System.ComponentModel.DataAnnotations;

public class RegisterViewModel
{
    [DataType(DataType.Text)]
    [Required(ErrorMessage = "Enter your username")]
    [DisplayName("User name")]
    [MaxLength(100, ErrorMessage = "Username cannot be longer than {1}")]
    public string UserName { get; set; }

    [DataType(DataType.EmailAddress)]
    [Required(ErrorMessage = "Enter your email")]
    [DisplayName("Email")]
    [EmailAddress(ErrorMessage = "Enter a valid email address")]
    [MaxLength(100, ErrorMessage = "Address cannot be longer than {1}")]
    public string Email { get; set; }

    [DataType(DataType.Password)]
    [DisplayName("Password")]
    [Required(ErrorMessage = "Enter a password")]
    public string Password { get; set; }

    [DataType(DataType.Password)]
    [DisplayName("Confirm your password")]
    [Required(ErrorMessage = "Enter the password again")]
    [Compare("Password", ErrorMessage = "The entered passwords do not match")]
    public string PasswordConfirmation { get; set; }
}
```

Design the user interface required for registration on your website:
  * Create a view for the  _Register_ action and display an appropriate registration form.
  * Create a view with content that informs users about the need to confirm their newly registered account  _(_ the _VerifyEmail_ view in the example).
  * Create views for the  _EmailConfirmed_ and  _EmailConfirmationFailed_ actions. Display information for users who click the confirmation link (for both successful and unsuccessful confirmation).


Visitors can now register accounts on your site. Upon registration, the system creates a disabled member in the connected Xperience database – **CMS_Member** table – and sends a confirmation email to the submitted address. After clicking the confirmation link, the visitor’s account becomes enabled and they can sign in.