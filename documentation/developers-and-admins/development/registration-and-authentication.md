---
source: https://docs.kentico.com/documentation/developers-and-admins/development/registration-and-authentication
scrape_date: 2026-01-26
---

  * [Home](/documentation)
  * [Developers and admins](/documentation/developers-and-admins)
  * [Development](/documentation/developers-and-admins/development)
  * Registration and authentication 


# Registration and authentication
Registration and authentication are two critical components of web application security.
Registration is the process by which a user creates an account on a web application. This process typically involves collecting information from the user, such as their name, email address, and a chosen password. The purpose of registration is to allow the web application to keep track of each user’s account and provide them with personalized services and content.
Authentication, on the other hand, is the process of verifying a user’s identity when they sign in to the web application. This involves checking the user’s credentials, such as their username and password, against the information stored in the application’s database. The purpose of authentication is to ensure that only authorized users can access designated parts of the web application and its resources (for example, [secured pages](/documentation/business-users/website-content/secure-pages) or [content items](/documentation/business-users/content-hub/content-items#secure-content-items)).
Together, registration and authentication provide several benefits for web application users and administrators. By registering, users can access personalized services and content, save preferences and settings, and track their activity in the application.
## Configure registration and authentication
Xperience uses a customized implementation of [ASP.NET Identity](https://learn.microsoft.com/en-us/aspnet/core/security/authentication/identity) (Identity) to manage registration and authentication. Identity is included as part of the .NET framework and can be added to the application and configured as part of the startup pipeline in **Program.cs**.
C#
**Program.cs - add Identity to the application**
Copy
```
var builder = WebApplication.CreateBuilder(args);

...

// Adds and configures ASP.NET Identity for the application
builder.Services.AddIdentity<ApplicationUser, NoOpApplicationRole>(options =>
{
    // Ensures that disabled member accounts cannot sign in
    options.SignIn.RequireConfirmedAccount = true;
    // Ensures unique emails for registered accounts
    options.User.RequireUniqueEmail = true;
})
    .AddUserStore<ApplicationUserStore<ApplicationUser>>()
    .AddRoleStore<NoOpApplicationRoleStore>()
    .AddUserManager<UserManager<ApplicationUser>>()
    .AddSignInManager<SignInManager<ApplicationUser>>();

// Adds authorization support to the app
builder.Services.AddAuthorization();
```

In the code snippet above, `ApplicationUser` is Xperience’s implementation of the Identity [user object](https://learn.microsoft.com/en-us/dotnet/api/microsoft.aspnetcore.identity.identityuser). This object is then mapped to `MemberInfo` which is persisted in the Xperience database – registered visitors are referred to as members in the system. For more information about the data flow and behavior, see [Xperience ASP.NET Identity architecture](#xperience-asp.net-identity-architecture).
If we break down the registration:
  * `NoOpApplicationRole` and `NoOpApplicationRoleStore` – Xperience **does not support** roles and role management as part of the Identity integration. The objects are empty implementations required by the `AddIdentity` method that exist only to simplify the configuration process.
  * `ApplicationUserStore` is the Xperience-specific implementation of the Identity `UserStore`. It persists data in the Xperience database and ensures conversion between `ApplicationUser` and `MemberInfo`.
  * The `RequireConfirmedAccount` option works together with the `ApplicationUser.Enabled` property to ensure that only enabled accounts can sign in to the system. See [Remarks - ApplicationUser.Enabled](#applicationuser.enabled) and [Remarks - Disabling user accounts](#disabling-user-accounts) for more information.
  * The `RequireUniqueEmail` option ensures members cannot register an additional account using an email already in the system, which is a requirement of Xperience’s Identity implementation.


With Identity configured, add the required `UseAuthentication` and `UseAuthorization` middleware. **Make sure to call the middleware in the provided order.**
C#
**Program.cs - add required middleware**
Copy
```
var app = builder.Build();

app.InitKentico();
app.UseStaticFiles();

// Make sure to call the middleware in the provided order
app.UseCookiePolicy();
app.UseAuthentication();
app.UseKentico();
app.UseAuthorization();
```

Identity is now configured for the application. Continue by implementing your desired registration and authentication flows.
## Registration and authentication flows
**[Forms authentication](/documentation/developers-and-admins/development/registration-and-authentication/forms-authentication)**
Forms authentication is a type of registration and authentication mechanism that uses HTML forms to collect user credentials (such as a username and password). When a visitor attempts to sign in, the collected data is matched againsted the database. This registration method enables a highly customized experience, as it allows for great flexibility when designing the authentication flow.
**[External authentication](/documentation/developers-and-admins/development/registration-and-authentication/external-authentication)**
External authentication is a process of authenticating visitors to a web application using an external identity provider, such as Google, Facebook, or Twitter. Its purpose is to provide a more convenient and secure way for visitors to access the application, as it allows them to use their existing social media accounts to sign in. This flow also reduces the burden of managing user authentication and security for application developers, as they can rely on the security measures implemented by the external identity provider.
## Management and customization
**[Add fields to member objects](/documentation/developers-and-admins/development/registration-and-authentication/add-fields-to-member-objects)**
Xperience provides the option to extend `MemberInfo` objects (visitors who register an account in the system) with additional fields. The default object that represents members in Xperience is by default equipped with only the most essential fields required for authentication using ASP.NET Identity. Most projects will likely want to collect a broader set of member data, which is enabled by this extension mechanism.
**[Manage members in the system](/documentation/business-users/members)**
The system provides a management interface for member objects via the **Members** application. Alternatively, to work with members using the API, use Xperience’s ORM framework as described on [Database table API](/documentation/developers-and-admins/api/database-table-api). Members are represented by the `MemberInfo` class.
## Retrieve the currently authenticated member
When implementing restricted sections of the application, you might sometimes need to access the details of the currently authenticated member, such as their email. This data is stored in the `ApplicationUser` object and retrieved via the `UserManager` class.
Identity by default requires the user’s name to be populated (ensured by [IUserValidator<TUser>](https://learn.microsoft.com/en-us/dotnet/api/microsoft.aspnetcore.identity.iuservalidator-1) called as part of the data validation process). To retrieve the current user, you can use `userManager.FindByNameAsync`. The name of the user associated with the current request is stored in `HttpContext.User.Identity.Name`.
Alternatively, if you ensure that all registration flows in your application populate the member’s email, you can use `userManager.FindByEmailAsync`.
As a second alternative, you can also use the member’s ID, which is guaranteed to exist as it gets assigned by the system when the member account is created.
C#
**Retrieve the current authenticated member using their ID**
Copy
```
// Instances of required services obtained using, e.g., dependency injection
private readonly IHttpContextAccessor httpContextAccessor;
private readonly UserManager<ApplicationUser> userManager;

public async Task MyMethod()
{
    // Gets the currently authenticated member account using their ID
    var currentMember = await userManager.
                    FindByIdAsync(httpContextAccessor.HttpContext.User.
                        FindFirstValue(userManager.Options.ClaimsIdentity.UserIdClaimType));

    // Custom logic...
}
```

## Xperience ASP.NET Identity architecture
Xperience applications implement authentication using [ASP.NET Identity](https://learn.microsoft.com/en-us/aspnet/identity/overview/getting-started/introduction-to-aspnet-identity). The implementation uses the `Kentico.Membership.ApplicationUser` type derived from `IdentityUser` to represent members – accounts registered in the system by site visitors. When saving member data to the database (`CreateAsync` or `UpdateAsync` methods on `UserManager`), Xperience maps data from `ApplicationUser` to `CMS.Membership.MemberInfo` objects. `MemberInfo` objects are connected to the system’s [ORM framework](/documentation/developers-and-admins/api/database-table-api), which is used to persist the data to the database.
Conversely, when retrieving member data from the database (`UserManager.FindBy*` methods), the member is first retrieved as `MemberInfo` and then converted to `ApplicationUser`. The transfer of data between objects from both sides of the flow is handled by the `MapFromMemberInfo` and `MapToMemberInfo` methods on `ApplicationUser`. This mapping can be customized – see [Add fields to member objects](/documentation/developers-and-admins/development/registration-and-authentication/add-fields-to-member-objects).
Accounts from [external authentication providers](/documentation/developers-and-admins/development/registration-and-authentication/external-authentication) such as Google, Facebook, etc. are stored using `MemberExternalLoginInfo` objects in the `CMS_MemberExternalLogin` database table. Each member account can have up to `N` associated external credentials, where `N` corresponds to the number of external providers supported by your implementation. Accounts that rely exclusively on authentication via an external provider have their `MemberInfo.MemberIsExternal` property set to `1`.
The following diagram summarizes the described behavior and data flow.
[![Xperience ASP.NET Identity architecture and data flow](/docsassets/documentation/registration-and-authentication/image-2023-2-22_16-47-13.png)](/docsassets/documentation/registration-and-authentication/image-2023-2-22_16-47-13.png)
## Remarks
### ApplicationUser.Enabled
When creating member accounts in the system, you **must** set their `ApplicationUser.Enabled` property. The enabled status is also controlled via the **Members** application –> **Disable/Enable** action, which toggles the state for the corresponding account.
Based on this property, the system determines whether the account can sign in. To avoid introducing additional Xperience-specific implementations to the Identity logic, the check that prevents disabled accounts from signing in is combined with the `IdentityOptions.SignInOptions.RequireConfirmedAccount` Identity setting (which is typically used with [account email confirmation](/documentation/developers-and-admins/development/registration-and-authentication/forms-authentication) flows).
C#
**Program.cs - Identity configuration**
Copy
```
builder.Services.AddIdentity<ApplicationUser, NoOpApplicationRole>(options =>
{
    ...
    options.SignIn.RequireConfirmedAccount = true;
})
```

For this reason, enabling this option is **required** for the _Enabled_ status to work correctly.
When using [forms authentication](/documentation/developers-and-admins/development/registration-and-authentication/forms-authentication) together with email confirmation, `ApplicationUserStore` (the Xperience-specific implementation of the [UserStore](https://learn.microsoft.com/en-us/dotnet/api/microsoft.aspnetcore.identity.entityframeworkcore.userstore) class) sets the `Enabled` property to `true` when the email verification step is successful (`ApplicationUserStore.SetEmailConfirmedAsync` called as part of `UserManager.ConfirmEmailAsync`). The property is not handled automatically at any other point.
### Disabling user accounts
Every time the `ApplicationUser.Enabled` property changes, the system generates a new value for the account’s [SecurityStamp](https://learn.microsoft.com/en-us/dotnet/api/microsoft.aspnetcore.identity.identityuser-1.securitystamp). The security stamp value is also stored in the client’s authentication cookie and compared against the value on the server. If a mismatch is detected (the `Enabled` status changed, a password change occurred, etc.), the client is forced to re-authenticate.
The security stamp comparison is done by [ISecurityStampValidator](https://learn.microsoft.com/en-us/dotnet/api/microsoft.aspnetcore.identity.isecuritystampvalidator) at a set interval, 30 minutes by default. This means that accounts that were disabled can still sign in to the system until the next revalidation event. If you wish to immediately block access for disabled accounts, you can change the revalidation interval via `SecurityStampValidatorOptions`.
C#
**Program.cs**
Copy
```
// Sets the validation interval to zero - the authentication cookie stored on the client is checked on every request
// If the validation fails - the security stamp is different than the one stored on the client -
// the client's authentication cookie (e.g., AspNetCore.Identity.Application) is cleared, forcing reauthentication
builder.Services.Configure<SecurityStampValidatorOptions>(options => options.ValidationInterval = TimeSpan.Zero);
```

### Registration activity logging
The system automatically logs the _Member registration_ [activity](/documentation/business-users/digital-marketing/contact-activities) whenever a member becomes active (enabled). This occurs when saving member data to the database in the following scenarios:
  * When a new member is added via `UserManager.CreateAsync` with `ApplicationUser.Enabled` set to true.
  * Whenever a member is updated to become active. For example, via `UserManager.ConfirmEmailAsync` when using [email confirmation](/documentation/developers-and-admins/development/registration-and-authentication/forms-authentication#email-confirmation) for new members, or manually via `UserManager.UpdateAsync`.


Keep this in mind if you plan to set up [automation processes](/documentation/business-users/digital-marketing/automation) with the _Registration_ trigger. The process will only start once the member account is active, not necessarily when the registration form is submitted by the user. Additionally, such processes may also start when reactivating existing member accounts that were previously disabled. However, this only occurs if the reactivation is performed using the ASP.NET Identity API (`UserManager`), not if the member is enabled in the [administration UI](/documentation/business-users/members).
### Page preview mode
[Preview mode](/documentation/business-users/website-content#preview) in Xperience enables editors to view the latest version of pages before they are published. Preview mode works automatically for all [content types](/documentation/developers-and-admins/development/content-types) for pages that are included in routing.
Preview URLs for pages are used in the following scenarios in website channel applications:
  * When viewing pages in **Preview** mode in the administration.
  * When editing pages via [Page Builder](/documentation/developers-and-admins/development/builders/page-builder).


The preview URLs the system generates for pages consist of virtual context, which is additional information, such as a hash for validating the URL against the client’s authentication cookie, context about the current [website channel](/documentation/developers-and-admins/configuration/website-channel-management), view mode (e.g., Read-only), etc. The live site application validates and processes the preview URL and displays the page using the conventional [routing process](/documentation/developers-and-admins/development/routing).
To share a preview of a page externally, users can create a [shareable preview URL](/documentation/developers-and-admins/configuration/shareable-preview-urls). This URL address is different from the one used for the internal preview. Shareable preview URLs are also handled through the regular [routing process](/documentation/developers-and-admins/development/routing).
**IVirtualContextDecorationArbiter**
You can implement the `IVirtualContextDecorationArbiter` interface to control whether links in previewed content should consist of virtual context. Note that you can’t change the actual preview URLs or use the interface to disable authentication of previewed content.
C#
**Example**
Copy
```
[assembly: RegisterImplementation(typeof(IVirtualContextDecorationArbiter), typeof(CustomVirtualContextDecorationArbiter))]

public class CustomVirtualContextDecorationArbiter : IVirtualContextDecorationArbiter
{
    private const string ARTICLES_PREFIX = "~/articles/";

    public virtual bool PathRequiresDecoration(string path)
    {

        // Do not decorate scripts or article pages
        return !(path.StartsWith(ARTICLES_PREFIX, StringComparison.OrdinalIgnoreCase));
    }
}
```

#### MVC authorization flows and preview mode
The preview mode functionality runs on a dedicated internal authentication scheme. As a result, pages [secured behind authorization](https://learn.microsoft.com/en-us/aspnet/core/security/authorization/simple) that checks for a specific authentication scheme are not previewable. This also applies to [shareable preview](/documentation/developers-and-admins/configuration/shareable-preview-urls).
C#
**Not previewable authorization configuration**
Copy
```
// Pages served by this controller are not previewable due to the explicit authentication scheme requirement
[Authorize("SomeAuthenticationScheme")]
public class MyController : Controller
```

![]()
[]()[]()
