---
source: https://docs.kentico.com/modules/members/create-profile-page
scrape_date: 2026-01-22
---

Module: Members
8 of 12 Pages
# Create the profile page
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
[ Previous page ](/modules/members/profile-form)
8 of 12
[ Mark complete and continue ](/modules/members/set-up-restricted-pages)
![]()
[]()[]()
