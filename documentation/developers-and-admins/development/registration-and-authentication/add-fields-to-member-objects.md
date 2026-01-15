# Add fields to member objects
  * [ Copy page link ](documentation/developers-and-admins/development/registration-and-authentication/add-fields-to-member-objects#) | [Get HelpService ID](documentation/developers-and-admins/development/registration-and-authentication/add-fields-to-member-objects#)
Core MVC 5


[✖](documentation/developers-and-admins/development/registration-and-authentication/add-fields-to-member-objects# "Close page link panel") [Copy to clipboard](documentation/developers-and-admins/development/registration-and-authentication/add-fields-to-member-objects#)
Xperience by Kentico provides the option to extend the objects representing members (visitors who register an account in the system) with additional fields.
The default member object is composed of a set of basic fields required to fulfill simple authentication scenarios. The object can store names, emails, passwords, and supports external logins. However, this may not be sufficient for more advanced scenarios that need to capture more specific user data – personal information, data from social identity providers, etc.
For instance, your application may want to store additional information – first and last names, a profile picture URL – for users that sign in using an [external authentication provider](documentation/developers-and-admins/development/registration-and-authentication/external-authentication) (Google, Twitter, Facebook, Auth0, etc.). This can be achieved by extending member objects with additional fields that capture the desired data.
When adding new fields:
  * [Add the new fields to the MemberInfo object](documentation/developers-and-admins/development/registration-and-authentication/add-fields-to-member-objects#add-the-new-fields-to-the-memberinfo-object)
  * [Modify ApplicationUser to account for the added fields](documentation/developers-and-admins/development/registration-and-authentication/add-fields-to-member-objects#modify-the-applicationuser-class)
  * [Configure ASP.NET Identity to work with the modified ApplicationUser](documentation/developers-and-admins/development/registration-and-authentication/add-fields-to-member-objects#configure-asp.net-identity-to-work-with-the-modified-applicationuser)
  * [Display the added fields in the Members application](documentation/developers-and-admins/development/registration-and-authentication/add-fields-to-member-objects#display-added-fields-in-the-members-application)


### Add the new fields to the MemberInfo object
The first step is to define new fields for `CMS.Membership.MemberInfo`. The class is connected to Xperience’s ORM framework and its API is used when saving members to the database. Extending the object adds new columns to the **CMS_Member** database table, where the additional data will be stored (for more information about the architecture, see [Remarks - ApplicationUser and MemberInfo](documentation/developers-and-admins/development/registration-and-authentication/add-fields-to-member-objects#remarks-applicationuser-and-memberinfo)).
  1. In the admin UI, open the **Modules** application.
  2. Select the **Membership** module.
  3. Switch to the **Classes** tab.
  4. Select the **Member** class.
  5. Switch to the **Database columns** tab.
  6. Create new fields based on your requirements using the [field editor](documentation/developers-and-admins/customization/field-editor).


You have added custom fields to the member object. For more information about the ORM framework in Xperience, see: [Database table API](documentation/developers-and-admins/api/database-table-api), [Object types](documentation/developers-and-admins/customization/object-types), [Extend system object types](documentation/developers-and-admins/customization/object-types/extend-system-object-types)
### Modify the ApplicationUser class
The added fields now need to be reflected in the `ApplicationUser` class to make them available for use within ASP.NET Identity APIs.
  1. In your project, create a new class that inherits from `Kentico.Membership.ApplicationUser` .
  2. In the class, declare properties corresponding to the object type fields added via the **Modules** application.
  3. Override the `MapFromMemberInfo` and `MapToMemberInfo` methods and: 
    1. Call the base implementation of each method. This ensures the default mapping _._
    2. Get and set values of the custom properties you wish to have available using `MemberInfo.GetValue` and `MemberInfo.SetValue`


C#
**ExtendedApplicationUser class**
Copy
```
using CMS.Membership;

using Kentico.Membership;

namespace MemberCustomization
{
    // Extends the default Kentico.Membership.ApplicationUser object
    public class ExtendedApplicationUser : ApplicationUser
    {
        // Exposes the existing 'MemberId' property of the 'MemberInfo' object
        public int MemberId
        {
            get;
            set;
        }

        // Property that corresponds to a custom field specified in the Modules application in the admin UI
        public string FirstName
        {
            get;
            set;
        }

        // Ensures field mapping between Xperience member objects and the Kentico.Membership ASP.NET Identity implementation
        // Called when retrieving member from Xperience via Kentico.Membership.ApplicationUserManager<TUser>
        public override void MapFromMemberInfo(MemberInfo source)
        {
            // Calls the base class implementation of the MapFromMemberInfo method
            base.MapFromMemberInfo(source);

            // Maps the 'MemberId' property to the extended member object
            MemberId = source.MemberID;

            // Sets the value of the 'FirstName' property
            FirstName = source.GetValue<string>("FirstName", null);
        }

        // Ensures field mapping between Xperience member objects and the Kentico.Membership ASP.NET Identity implementation
        // Called when creating or updating members using Kentico.Membership.ApplicationUserManager<TUser>
        public override void MapToMemberInfo(MemberInfo target)
        {
            // Calls the base class implementation of the MapToMemberInfo method
            base.MapToMemberInfo(target);

            // Maps the 'MemberId' property to the extended member object
            target.MemberID = MemberId;

            // Sets the value of the 'FirstName' MemberInfo field
            target.SetValue("FirstName", FirstName);
        }
    }
}
```

The extended class is now ready. The main benefit of this approach is that it enables you to work with the added custom fields using strongly-typed properties.
### Configure ASP.NET Identity to work with the modified ApplicationUser
In your application’s startup file (**Program.cs** by default), edit the Identity configuration. Substitute the `ApplicationUser` class with the extendedclass(`ExtendedApplicationUser` in this example).
C#
**Program.cs - ASP.NET Identity configuration**
Copy
```
builder.Services.AddIdentity<ExtendedApplicationUser, NoOpApplicationRole>(options =>
{
    options.SignIn.RequireConfirmedAccount = true;
})
    .AddUserStore<ApplicationUserStore<ExtendedApplicationUser>>()
    .AddRoleStore<NoOpApplicationRoleStore>()
    .AddUserManager<UserManager<ExtendedApplicationUser>>()
    .AddSignInManager<SignInManager<ExtendedApplicationUser>>();
```

When working with Identity services in code, use the extended user class, for example:
C#
Copy
```
public AccountController(UserManager<ExtendedApplicationUser> userManager, SignInManager<ExtendedApplicationUser> signInManager)
```

The Identity implementation now works with the extended member objects. You can use the additional fields to capture more information from users during [forms authentication](documentation/developers-and-admins/development/registration-and-authentication/forms-authentication), or store additional claims from an [external identity provider](documentation/developers-and-admins/development/registration-and-authentication/external-authentication).
### Display added fields in the Members application
To display the fields added to the _Member_ object type in the **Membership** application (e.g., when inspecting details of selected members), add the fields to the object type’s _Edit_ UI form:
  1. Open the **Modules** application and navigate to **Membership** → **Classes** → **Member** → **UI forms**.
  2. Select the **Edit** UI form.
  3. Select **New field**. The **Database column** selector opens. 
     * Use the selector to choose from among the columns added to the Member class. Data submitted via the created form field is persisted in the selected column.
  4. Add the new columns to the form using the [field editor](documentation/developers-and-admins/customization/field-editor).


The added columns now show in the modified UI form when viewing member details in the **Members** application.
Additionally, to display the added fields in the member listing (on the root page of the application), write an [extender](documentation/developers-and-admins/customization/extend-the-administration-interface/ui-pages/ui-page-extenders) for the `MemberList` [listing UI page](documentation/developers-and-admins/customization/extend-the-administration-interface/ui-pages/reference-ui-page-templates/listing-ui-page-template):
C#
**Display a 'FirstName' column in the listing**
Copy
```
using System.Threading.Tasks;

using Kentico.Xperience.Admin.Base;
using Kentico.Xperience.Admin.Base.UIPages;

public class MemberListExtender : PageExtender<MemberList>
{
    public override Task ConfigurePage()
    {
        base.ConfigurePage();

        // Displays a 'FirstName' column in the listing
        Page.PageConfiguration.ColumnConfigurations
                .AddColumn("FirstName");

        return Task.CompletedTask;
    }
}
```

### Remarks – ApplicationUser and MemberInfo
Xperience applications implement authentication using [ASP.NET Identity](https://learn.microsoft.com/en-us/aspnet/identity/overview/getting-started/introduction-to-aspnet-identity). The implementation uses the `Kentico.Membership.ApplicationUser` type derived from `IdentityUser` to represent members. When saving member data to the database (`CreateAsync` or `UpdateAsync` methods on `UserManager`), Xperience maps data from `ApplicationUser` to `CMS.Membership.MemberInfo` objects. `MemberInfo` objects are connected to the system’s [ORM framework](documentation/developers-and-admins/api/database-table-api), which is then used to persist the data to the database.
Conversely, when retrieving member data from the database (`UserManager.FindBy*` methods), the member is first retrieved as `MemberInfo` and then converted to `ApplicationUser`. The transfer of data between objects from both sides of the flow is handled by the `MapFromMemberInfo` and `MapToMemberInfo` methods on `ApplicationUser`.
The main benefit of the additional layer introduced by `ApplicationUser` is the ability to work with added fields using strongly-typed properties. This would not be possible if Identity worked directly with `MemberInfo`, because system ***Info** class definitions cannot be easily extended with new fields (they are not declared as `partial` classes). The only way to access custom fields is via the **GetValue** and **SetValue** methods, which do not allow for direct typed access (see [Extend system object types](documentation/developers-and-admins/customization/object-types/extend-system-object-types)). For a more detailed overview of Xperience’s Identity architecture, see [Registration and authentication](documentation/developers-and-admins/development/registration-and-authentication).