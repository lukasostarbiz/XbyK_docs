---
source: https://docs.kentico.com/documentation/developers-and-admins/configuration/users/administration-registration-and-authentication/administration-forms-authentication
scrape_date: 2026-01-22
---

  * [Home](/documentation)
  * [Developers and admins](/documentation/developers-and-admins)
  * [Configuration](/documentation/developers-and-admins/configuration)
  * [Users](/documentation/developers-and-admins/configuration/users)
  * [Administration - Registration and authentication](/documentation/developers-and-admins/configuration/users/administration-registration-and-authentication)
  * Administration - Forms authentication 


# Administration - Forms authentication
Xperience admin UI uses [ASP.NET Core Identity](https://docs.microsoft.com/en-us/aspnet/core/security/authentication/identity) to manage administration user accounts. The authentication pipeline for the admin UI is not shared with the [live site](/documentation/developers-and-admins/development/registration-and-authentication) and is configured independently via `Kentico.Membership.AdminIdentityOptions`.
The system provides the following configuration options. The default values are described in each section.
  * [Authentication options](#authentication-options)
    * [Mode](#mode)
    * [ExpireTimespan](#expiretimespan)
    * [MultiFactorAuthenticationOptions](#multifactorauthenticationoptions)
    * [CookieEventsOptions](#cookieeventsoptions)
  * [Password options](#password-options)
    * [Forbidden passwords](#forbidden-passwords)
    * [Configuration example](#configuration-example)
  * [Lockout options](#lockout-options)
  * [Email options](#email-options)
    * [LinkExpiration](#linkexpiration)
  * [Customize user management notifications](#customize-user-management-notifications)
  * [Rate limiting for user management endpoints](#rate-limiting-for-user-management-endpoints)


## Authentication options
Authentication options configure the behavior of the system for admin UI authentication.
### Mode
**Default value** : `AdminAuthenticationMode.PrioritizeExternal`
Allows you to configure if the default forms authentication should be maintained when an [external authentication](/documentation/developers-and-admins/configuration/users/administration-registration-and-authentication/administration-external-authentication) provider is registered for authentication into the Xperience admin UI. By default, when an external authentication provider is registered, this provider is the only authentication option. See [Administration external authentication – Sign-in page behavior](/documentation/developers-and-admins/configuration/users/administration-registration-and-authentication/administration-external-authentication#sign-in-page-behavior) for more information.
### ExpireTimespan
**DefaultValue** : 30 minutes
Sets the expiration time for admin UI authentication cookies. Uses sliding expiration by default.
### MultiFactorAuthenticationOptions
Allows you to set up [multi-factor authentication](/documentation/developers-and-admins/configuration/users/user-management#multi-factor-authentication) (MFA) for the administration. The underlying `AdminMultiFactorAuthenticationOptions` type provides the following properties:
  * **Enabled** – set a `true` value to enable MFA for all administration users (there is no option for users to individually opt-in or out). This MFA feature only applies to the administration’s built-in forms authentication ([external authentication providers](/documentation/developers-and-admins/configuration/users/administration-registration-and-authentication/administration-external-authentication) have their own MFA features and configuration).
  * **ApplicationName** – an identifier of your Xperience application displayed in MFA authenticator apps. The application name is passed to authenticator apps when users set up MFA using the displayed QR code. The default value is “Xperience by Kentico”.


C#
**Program.cs - Enable multi-factor authentication**
Copy
```
builder.Services.Configure<AdminIdentityOptions>(options =>
{
    options.AuthenticationOptions.MultiFactorAuthenticationOptions.Enabled = true;
});
```

You can also adjust the email message sent when a user’s multi-factor authentication secret key is reset. See the [Customize user management notifications](#customize-user-management-notifications) section.
### CookieEventsOptions
Contains events invoked when users sign in or out of the admin UI. Events fire for both the default forms authentication and external authentication.
The following events are available:
  * `OnSigningIn` – called when users successfully sign in to the admin UI.
  * `OnSigningOut` – called when users sign out of the admin UI.


Both events expose a context that allows you to modify the server response. For example:
C#
**Example - clear browsing data on sign-out**
Copy
```
builder.Services.Configure<AdminIdentityOptions>(options =>
{
    options.AuthenticationOptions.CookieEventsOptions.OnSigningOut = ctx =>
    {
        ctx.Response.Headers.Add("Clear-Site-Data", "*");
        return Task.CompletedTask;
    };
});
```

## Password options
Password options configure the application’s password policy.
Option |  Default value |  Description  
---|---|---  
RequiredLength |  8 |  The minimum length of the password string.  
RequiredUniqueChars |  1 |  The number of unique characters the password must contain  
RequireDigit |  true |  Indicates if a digit is required as part of the password string.  
RequireLowercase |  true |  Indicates if the password string must contain at least one lowercase letter.  
RequireNonAlphanumeric |  true |  Indicates if the password string must contain at least one [special character](https://owasp.org/www-community/password-special-characters).  
RequireUppercase |  true |  Indicates if the password string must contain at least one uppercase letter.  
ForbiddenPasswords |  |  See [Forbidden passwords](#forbidden-passwords).  
### Forbidden passwords
Additionally, you can configure a list of **forbidden passwords** via the `ForbiddenPasswords` option. This allows you to stop users from choosing common or easy-to-guess passwords. By default, the list of forbidden passwords is empty.
The system first validates that new passwords fulfill the password policy requirements, and then checks against the forbidden password list. Forbidden passwords are **case-insensitive** , but otherwise must exactly match the entered text. Password variants with special characters or numbers must be added as separate forbidden passwords.
For example, “password” blocks passwords like “password”, “Password” or “passWord”, but not “p4ssword”, “passw0rd” or “password!”.
Listing forbidden passwords manually may not be practical in most cases. We recommend implementing a generator class that puts together combinations of relevant keywords, special characters and numbers. The [configuration example](#configuration-example) below shows a basic forbidden password generator.
### Configuration example
Password options for the admin UI are stored in the `AdminIdentityOptions` class, and can be accessed via the `PasswordOptions` property. You can change the default configuration by overriding desired options using the [options pattern](https://docs.microsoft.com/en-us/aspnet/core/fundamentals/configuration/options). For example:
C#
**Program.cs**
Copy
```
using Kentico.Membership;

// ...

WebApplicationBuilder builder = WebApplication.CreateBuilder(args);

// ...

builder.Services.Configure<AdminIdentityOptions>(options =>
{
    options.PasswordOptions.RequireDigit = true;
    options.PasswordOptions.RequireLowercase = true;
    // ...

    // Adds forbidden passwords based on specified keywords
    var forbiddenKeywords = new List<string> { "password", "football", "qwerty" };
    options.PasswordOptions.ForbiddenPasswords = CustomGenerator.Generate(forbiddenKeywords);
});
```

C#
**Forbidden password generator example**
Copy
```
public static class CustomGenerator
{
    private static readonly List<char> SpecialChars = ['!', '@', '#', '$', '%', '^', '&', '*', '(', ')', '.', '?', '-', '_', '=', '+', '[', ']', '{', '}', '\\', '|', ';', ':', '\'', '"', ',', '<', '>', '/', '~', '`'];
    private static readonly List<string> Numbers = ["0", "1", "2", "3", "4", "5", "6", "7", "8", "9"];

    // Generates combinations of specified password keywords, special characters and numbers
    // This basic example adds a single special character and number to each keyword (in various orders)
    public static HashSet<string> Generate(List<string> keywords)
    {

        var forbiddenPasswords =
            from keyword in keywords
            from specialChar in SpecialChars
            from number in Numbers
            from forbiddenPassword in new[]
            {
                    keyword + specialChar + number,
                    keyword + number + specialChar,
                    specialChar + keyword + number,
                    number + keyword + specialChar
            }
            select forbiddenPassword;

        return new HashSet<string>(forbiddenPasswords);
    }
}
```

## Lockout options
Allows you to configure if and how the system [locks](/documentation/developers-and-admins/configuration/users/user-management#account-lockout) administration user accounts after consecutive failed sign-ins. The underlying `AdminLockoutOptions` type provides the following properties:
  * **Enabled** – `true` by default. Set a `false` value if you wish to fully disable the lockout functionality for the administration (not recommended).
  * **MaxFailedAccessAttempts** – sets the number of consecutive failed sign-in attempts after which user accounts are locked. The default values is 5. The failed sign-in count is reset for a user whenever they sign in successfully.
  * **DefaultLockoutTimeSpan** – a [TimeSpan](https://learn.microsoft.com/dotnet/fundamentals/runtime-libraries/system-timespan) value that sets the lockout duration. The default value is 5 minutes. Users can also unlock their account immediately by clicking a link in a notification email, which is automatically sent when the account is locked.


C#
**Program.cs - Adjust account lockout configuration**
Copy
```
using Kentico.Membership;

// ...

WebApplicationBuilder builder = WebApplication.CreateBuilder(args);

// ...
builder.Services.Configure<AdminIdentityOptions>(options =>
{
    // Configures account lockout for administration users

    // Allows 2 consecutive failed authentication attempts. Locks accounts after 3 failed authentications.
    options.LockoutOptions.MaxFailedAccessAttempts = 3;
    // Sets the lockout duration to 30 minutes
    options.LockoutOptions.DefaultLockoutTimeSpan = TimeSpan.FromMinutes(30.0);
});
```

## Email options
Email options configure emails related to user management sent by the Xperience application. You can customize these user management emails by editing the corresponding notifications in the [Notifications](/documentation/developers-and-admins/configuration/notifications) application.
### LinkExpiration
Currently, `LinkExpiration` is the only property configurable via the email options. This sets the validity interval of account management-related URLs generated by the system (registration, password reset, MFA secret key reset).
The **default value** is 1 day (`TimeSpan.FromDays(1)`).
C#
**Program.cs**
Copy
```
builder.Services.Configure<AdminIdentityOptions>(options =>
{
    // Sets the validity of user management URLs to 2 days
    options.EmailOptions.LinkExpiration = TimeSpan.FromDays(2);
    ...
});
```

## Customize user management notifications
The [Notifications](/documentation/developers-and-admins/configuration/notifications) application within the Xperience administration contains a set of preconfigured notifications that inform users about events related to their account:
  * User invitation
  * User password reset
  * Multi-factor authentication reset
  * User account unlock


You can adjust the subject, sender address and content of these email to better fit your needs.
## Rate limiting for user management endpoints
All endpoints used as part of the Xperience administration user management functionality, such as the sign-in or password reset page, are protected by rate limiting. This restricts the number of requests allowed within a given window of time, which provides additional security against denial-of-service and other brute-force attacks.
Rate limiting for user management endpoints is implemented using [ASP.NET Core Rate limiting middleware](https://learn.microsoft.com/en-us/aspnet/core/performance/rate-limit), specifically the [Token bucket limiter](https://learn.microsoft.com/en-us/aspnet/core/performance/rate-limit#token-bucket-limiter) algorithm.
By default, the rate limiter works in the following way:
  * User management endpoints are allowed to serve 100 requests (`TokenLimit`).
  * Every request uses up one token from the total limit. Tokens are replenished by 10 (`TokensPerPeriod`) every 6 seconds (`ReplenishmentPeriod`).
  * If the tokens are depleted, further requests are placed into a queue and need to wait until the tokens are replenished. The queue has a maximum length of 50 (`QueueLimit`).
  * When the tokens are depleted and the queue is full, any further requests are denied and the application responds with a 429 HTTP status code.


You can adjust tha rate limiter’s parameters by configuring `AdminAuthRateLimitOptions` (`Kentico.Xperience.Admin.Base` namespace) for your application. The following options are available:
Option |  Default value |  Description  
---|---|---  
Enabled |  true |  Indicates if rate limiting is enabled for user management endpoints. We do not recommend fully disabling rate limiting unless your project is secured by other custom measures against brute-force attacks.  
TokenLimit |  100 |  The maximum number of tokens available. A token is used up whenever the application serves a request to a user management endpoint.  
ReplenishmentPeriod |  6 |  Determines how often the rate limiter replenishes tokens. The value is in seconds.  
TokensPerPeriod |  10 |  The number of tokens that are added every replenishment period.  
QueueLimit |  50 |  When a request hits a user management endpoint while the token limit is depleted, the request waits in a queue until the tokens are replenished. This option sets the maximum size of the queue. If the maximum queue size is reached, any further requests are denied and the application responds with a 429 HTTP status code.  
RequestQueueProcessingOrder |  QueueProcessingOrder.OldestFirst |  The order in which requests are processed from the queue. Use values from the [QueueProcessingOrder](https://learn.microsoft.com/en-us/dotnet/api/system.threading.ratelimiting.queueprocessingorder) enum.  
C#
**Program.cs - Adjust rate limiting for user management endpoints**
Copy
```
using Kentico.Xperience.Admin.Base;

// ...

WebApplicationBuilder builder = WebApplication.CreateBuilder(args);

// ...

// Configures rate limiting for administration authentication endpoints
builder.Services.Configure<AdminAuthRateLimitOptions>(options =>
{
    options.TokenLimit = 300;
    options.ReplenishmentPeriod = TimeSpan.FromSeconds(30);
    options.TokensPerPeriod = 20;
    options.QueueLimit = 20;
    options.RequestQueueProcessingOrder = System.Threading.RateLimiting.QueueProcessingOrder.OldestFirst;
});
```

![]()
[]()[]()
