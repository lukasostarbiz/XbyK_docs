---
source: https://docs.kentico.com/documentation/developers-and-admins/customization/secure-custom-endpoints
scrape_date: 2026-01-22
---

  * [Home](/documentation)
  * [Developers and admins](/documentation/developers-and-admins)
  * [Customization](/documentation/developers-and-admins/customization)
  * Secure custom endpoints 


# Secure custom endpoints
Your custom code may include endpoints to handle various logic (e.g., image uploads). For example, custom endpoints can be used in the implementations of [Page Builder](/documentation/developers-and-admins/development/builders/page-builder) components.
The framework treats these endpoints as any other API – they are, by default, publicly accessible under the handler URL. If your custom endpoints process any potentially confidential data, you may want to consider implementing protection against unauthorized access.
The `Authorize` attribute in .NET specifies that only authorized users can access a particular controller or action method. You can use the attribute with specific authentication schemes by specifying the scheme name as a parameter.
Generally, you would want to use the `Authorize` attribute when:
  1. Sensitive data is being accessed or modified – If your endpoint is dealing with sensitive data, such as personal information or confidential documents.
  2. Privileged actions are being performed – If your endpoint is performing privileged actions, such as creating or modifying records in the database.
  3. You need to track user activity – If you need to keep track of which users are accessing certain parts of your application, you can use the attribute to enforce authentication and log user activity.


To allow requests only from users signed in to the Xperience admin UI (a sufficient constraint to identify non-malicious actors), specify a scheme from `AdminIdentityConstants`.
  * `APPLICATION_SCHEME` – for users authenticated using [forms authentication](/documentation/developers-and-admins/configuration/users/administration-registration-and-authentication/administration-forms-authentication).
  * `EXTERNAL_SCHEME` – for users authenticated using [external providers](/documentation/developers-and-admins/configuration/users/administration-registration-and-authentication/administration-external-authentication).


C#
**Restrict endpoints to administration users**
Copy
```
using Kentico.Membership;

using Microsoft.AspNetCore.Authorization;
using Microsoft.AspNetCore.Mvc;

[Authorize(AuthenticationSchemes = $"{AdminIdentityConstants.APPLICATION_SCHEME}, {AdminIdentityConstants.EXTERNAL_SCHEME}")]
public class WidgetPostController : Controller
{
    // Returns 403 for requests without the '.AspNetCore.Xperience.Application' or '.AspNetCore.Xperience.External' cookies
    public ActionResult HandlePost()
    {
        ...

        return Ok();
    }
}
```

![]()
[]()[]()
