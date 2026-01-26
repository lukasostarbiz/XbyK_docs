---
source: https://docs.kentico.com/guides/development/members/map-member-to-contact
scrape_date: 2026-01-26
---

  * [Home](/guides)
  * [Development](/guides/development)
  * [Members](/guides/development/members)
  * Synchronize member data with the corresponding contact 


# Synchronize member data with the corresponding contact
A [Contact](/documentation/business-users/digital-marketing/contact-management) object in Xperience by Kentico represents a website visitor. As you saw [earlier in this series](/guides/development/members/implement-member-registration), you can enable **registration** and implement sign in logic for your solution that creates [members](/documentation/business-users/members).
For many reasons it is useful to keep the the **member synchronized with the corresponding contact**. On successful member registration, the system logs the event for the contact and maps the member’s email to the contact if the contact’s email is empty. This synchronization aligns email data but doesn’t take care of any custom fields, such as name and other information relevant for your domain. Nor does it establish a direct link between the two objects.
This guide expands on an [example from our documentation](/documentation/developers-and-admins/digital-marketing-setup/contact-configuration/map-custom-member-fields-to-contacts), showing how to map custom member and contact fields, and use them to create link between the two objects.
The examples here rely in part on examples from other training materials:
  * The [Add a custom field to the Contact profile](/guides/development/customizations-and-integrations/add-custom-contact-field) guide. Our example uses a custom contact field created here.
  * The [Data protection](/guides/development/data-protection) series. Our example utilizes cookie logic and the consent banner from this series.


If you have not completed these requirements, you can consider the parts of this example relating to _custom contact fields_ , _consent_ , and _cookie management_ optional.
## Before you start
This guide requires the following:
  * Familiarity with [C#](https://learn.microsoft.com/en-us/dotnet/csharp/), [.NET Core](https://learn.microsoft.com/en-us/dotnet/), [Dependency injection](https://learn.microsoft.com/en-us/dotnet/core/extensions/dependency-injection), and the [MVC pattern](https://learn.microsoft.com/en-us/aspnet/core/mvc/overview).
  * A running instance of Xperience by Kentico, preferably [30.11.1](/documentation/changelog) or higher. 
Some features covered in the training guides may not work in older versions. 
  * Basic understanding of [Contacts](/documentation/business-users/digital-marketing/contact-management) and [Members](/documentation/business-users/members) in Xperience by Kentico, both form the administration and coding perspective.
  * Completion of the [previous guide](/guides/development/members/implement-member-registration).


**Code samples**
You can find a project with completed, working versions of code samples from this guide and others in the [finished branch](https://github.com/Kentico/xperience-by-kentico-training-guides/tree/finished) of the _Training guides_ repository.
The [main branch](https://github.com/Kentico/xperience-by-kentico-training-guides) of the repository provides a starting point to code along with the guides.
The code samples in this guide are for [.NET 8](https://learn.microsoft.com/en-us/dotnet/core/whats-new/dotnet-8/overview) only.
They come from a project that uses [implicit using directives](https://learn.microsoft.com/en-us/dotnet/core/project-sdk/overview#implicit-using-directives). You may need to add additional `using` directives to your code if your project does not use this feature.
## Build a member-to-contact relationship
The [documentation contact-member mapping example](/documentation/developers-and-admins/digital-marketing-setup/contact-configuration/map-custom-member-fields-to-contacts#example) is fairly straightforward. You implement the `IMemberToContactMapper` as a custom class and in the `Map` method you set all the custom values from the member object to the contact object. Then you call the default `Map` implementation, which ensures the mapping of the email and saving the `ContactInfo` object.
In that example the contact data gets updated, but the contact has no information about which member it corresponds to.
Let’s create a little more complex solution. We’ll look at how to map **custom member fields** to **custom contact fields** and how to utilize [custom contact field](/guides/development/customizations-and-integrations/add-custom-contact-field) to create relationship with the corresponding member.
Extending contact with custom field is out the scope of this example. Here we will use the custom fields created in [a customization guide](/guides/development/customizations-and-integrations/add-custom-contact-field). Feel free to skip this part if you have not completed that guide.
## Create the mapping
### Prepare the service
Let’s start by creating a service in which we will place methods with all the mapping logic. This way, we will be able to call the logic outside of the limited default scenarios where Xperience triggers the `IMemberToContactMapper`.
In _TrainingGuides.Web/Features/Membership/Services_ create a `MemberContactService` and it’s interface.
Implement a `TransferMemberFieldsToContact` method that accepts member (`MemberInfo`) and contact (`ContactInfo`) objects as parameters.
Cast the MemberInfo object into our custom `GuidesMember` type.
Then, create another overload of the `TransferMemberFieldsToContact`, that takes our custom member and the contact object as parameters and performs the following logic:
Make a copy of the contact and map the values of member properties to the new contact. Then, set the value of the member ID to the custom field on contact, identifying it by its code name - `TrainingGuidesContactMemberId`.
To work with custom field code names, we recommend using constants, especially if you need to reference the field repetitively.
C#
**IMemberContactService.cs**
Copy
```
namespace TrainingGuides.Web.Features.Membership.Services;

public interface IMemberContactService
{
namespace TrainingGuides.Web.Features.Membership.Services;

public interface IMemberContactService
{
    /// <summary>
    /// Transfers values from MemberInfo to ContactInfo
    /// </summary>
    /// <param name="member">The member whose data should be transferred</param>
    /// <param name="contact">The contact to transfer the data to</param>
    /// <returns>The updated ContactInfo object, but DOES NOT save the contact data</returns>
    ContactInfo TransferMemberFieldsToContact(MemberInfo member, ContactInfo contact);

    /// <summary>
    /// Transfers values from GuidesMember to ContactInfo
    /// </summary>
    /// <param name="guidesMember">The member whose data should be transferred</param>
    /// <param name="contact">The contact to transfer the data to</param>
    /// <returns>The updated ContactInfo object, but DOES NOT save the contact data</returns>
    ContactInfo TransferMemberFieldsToContact(GuidesMember guidesMember, ContactInfo contact);
}
```

C#
**MemberContactService.cs**
Copy
```
using CMS.ContactManagement;
using CMS.DataEngine;
using CMS.Membership;
using Kentico.Web.Mvc;
using TrainingGuides.Web.Features.DataProtection.Shared;

namespace TrainingGuides.Web.Features.Membership.Services;

public class MemberContactService : IMemberContactService
{
    // Use dependency injection to populate these
    private readonly IInfoProvider<ContactInfo> contactInfoProvider;
    private readonly ICookieAccessor cookieAccessor;
    private readonly ICurrentContactProvider currentContactProvider;
    private readonly IContactMergeService contactMergeService;

    private const string FAVORITE_COFFEE_FIELD_NAME = "TrainingGuidesContactFavoriteCoffee";
    private const string MEMBER_ID_FIELD_NAME = "TrainingGuidesContactMemberId";

    ...

    /// <inheritdoc />
    public ContactInfo TransferMemberFieldsToContact(MemberInfo member, ContactInfo contact)
    {
        var guidesMember = member.AsGuidesMember();

        return TransferMemberFieldsToContact(guidesMember, contact);
    }

    /// <inheritdoc />
    public ContactInfo TransferMemberFieldsToContact(GuidesMember guidesMember, ContactInfo contact)
    {
        var newContact = contact.Clone();

        if (!string.IsNullOrWhiteSpace(guidesMember.GivenName))
        {
            newContact.ContactFirstName = guidesMember.GivenName;
        }
        if (!string.IsNullOrWhiteSpace(guidesMember.FamilyName))
        {
            newContact.ContactLastName = guidesMember.FamilyName;
        }
        if (!string.IsNullOrWhiteSpace(guidesMember.FavoriteCoffee))
        {
            _ = newContact.SetValue(FAVORITE_COFFEE_FIELD_NAME, guidesMember.FavoriteCoffee);
        }

        // Sets the Member ID of the current contact
        _ = newContact.SetValue(MEMBER_ID_FIELD_NAME, guidesMember.Id);

        // For data security, do not overwrite contact email address if it is already set
        if (string.IsNullOrWhiteSpace(contact.ContactEmail) && !string.IsNullOrWhiteSpace(guidesMember.Email))
        {
            newContact.ContactEmail = guidesMember.Email;
        }

        return newContact;
    }
}
```

The methods we prepared will return a new `ContactInfo` object, that you can now use to update the original one.
You probably noticed the code sample above is injecting several dependencies that are currently unused. We will utilize them all later on in the example.
Next, let’s create a method that will update the existing contact. Using the `ContactInfo.HasChanged` property and the Xperience `IInfoProvider`, you can determine whether there have been any updates to the contact and assign the new contact object as the current contact.
C#
**IMemberContactService.cs**
Copy
```
...
/// <summary>
/// Saves the contact data if it has changed
/// </summary>
/// <param name="contact">The contact to save</param>
void UpdateContactIfChanged(ContactInfo contact);
...
```

C#
**MemberContactService.cs**
Copy
```
...
public void UpdateContactIfChanged(ContactInfo contact)
{
    if (contact.HasChanged)
    {
        contactInfoProvider.Set(contact);
    }
}
...
```

Before moving on, remember to register your new service:
C#
**ServiceCollectionExtensions.cd**
Copy
```
...
services.AddSingleton<IMemberContactService, MemberContactService>();
...
```

### Implement the mapper class
Create a `TrainingGuidesMemberToContactMapper.cs` class file in the _TrainingGuides.Web/Features/Membership/Services_ folder.
Implement the mapper class, similarly, to the documentation example we talked about earlier. Call the `TransferMemberFieldsToContact` and `UpdateContactIfChanged` to initiate the member-contact data synchronization when the `Map` method gets fired.
C#
**TrainingGuidesMemberToContactMapper.cs**
Copy
```
using CMS;
using CMS.ContactManagement;
using CMS.Membership;

using Kentico.OnlineMarketing.Web.Mvc;

using TrainingGuides.Web.Features.Membership.Services;

[assembly: RegisterImplementation(typeof(IMemberToContactMapper), typeof(TrainingGuidesMemberToContactMapper))]

namespace TrainingGuides.Web.Features.Membership.Services;
public class TrainingGuidesMemberToContactMapper : IMemberToContactMapper
{
    private readonly IMemberContactService memberContactService;

    public TrainingGuidesMemberToContactMapper(IMemberContactService memberContactService)
    {
        this.memberContactService = memberContactService;
    }

    /// <summary>
    /// Maps a member to a contact and updates the contact if it has changed
    /// </summary>
    /// <param name="member">The member whose data should be transferred</param>
    /// <param name="contact">The contact to transfer the data to</param>
    public void Map(MemberInfo member, ContactInfo contact)
    {
        if (member is null || contact is null)
            return;

        contact = memberContactService.TransferMemberFieldsToContact(member, contact);

        memberContactService.UpdateContactIfChanged(contact);
    }
}
```

## Check your progress
At this point you should be able to test whether your member-contact data synchronization works.
Run your website in an incognito browser window as a visitor and accept cookies. Open the Xperience administration dashboard in a new window and visit the **Contact management** application. You will see your newly created anonymous contact.
Now, in your website, use the registration page we created [earlier in this series](/guides/development/members/implement-member-registration) to register a new member.
When you go back to your administration dashboard and reload the **Contact management** , you will see that your new contact has its fields set according to what you filled out for the member. Including the custom fields. Success!
Your browser does not support the video tag. 
[![Shows the custom contact fields filled out according to the member data](/docsassets/guides/map-member-to-contact/custom-contact-fields-filled-out.png)](/docsassets/guides/map-member-to-contact/custom-contact-fields-filled-out.png)
## Synchronize contact with member on sign-in
The basic data synchronization is in place, but there are still a few things we need to handle. The first one is handling the event of member signing in.
### Merge duplicate contacts
Because with each cookie consent the system creates a new contact, you can end up with several for one visitor. It is a good practice to clean up the redundant contacts periodically.
For this, we will take advantage of the `IContactMergeService` which is a part of Xperience by Kentico out-of-the-box, and which we already injected into our `MemberContactService`.
Create a `MergeContactByEmail` method inside the `MemberContactService` that calls the out-of-the-box method to merge contacts.
C#
**IMemberContactService.cs**
Copy
```
...
/// <summary>
/// Merges the provided contact based on the provided email address
/// </summary>
/// <param name="contact">The contact to merge</param>
public void MergeContactByEmail(ContactInfo contact);
...
```

C#
**MemberContactService.cs**
Copy
```
...
public void MergeContactByEmail(ContactInfo contact) => contactMergeService.MergeContactByEmail(contact);
...
```

Now, create a new method in the `MembershipService` that will:
  1. Synchronize the contact-member.
  2. Call the `MergeContactByEmail` to trigger the cleanup.
  3. Set the contact synchronized with the member as the current contact.


C#
**MembershipService.cs**
Copy
```
...
private void SynchronizeContact(GuidesMember member, bool createNewContactIfNoneFound = false)
{
    //In a real-world scenario, make sure you check applicable data protection laws and handle consent accordingly.
    var contact = ContactManagementContext.GetCurrentContact()
        ?? (createNewContactIfNoneFound ? new ContactInfo() : null);

    if (contact is null)
        return;

    var newContact = memberContactService.TransferMemberFieldsToContact(member, contact);

    memberContactService.UpdateContactIfChanged(newContact);

    memberContactService.MergeContactByEmail(newContact);

    memberContactService.SetCurrentContactForMember(member);
}
...
```

C#
**IMemberContactService.cs**
Copy
```
...
/// <summary>
/// Sets the CurrentContact to the most recent one with a matching email that is associated with the given member
/// </summary>
/// <param name="member">The member to find an associated contact for</param>
void SetCurrentContactForMember(GuidesMember member);
...
```

C#
**MemberContactService.cs**
Copy
```
...
/// <summary>
/// Gets the most recent contact associated with the provided member whose email matches
/// </summary>
/// <param name="member">The GuidesMember to find an associated contact</param>
/// <returns>The most recent contact associated with the provided member whose email matches</returns>
/// <remarks>Ideally, this method should be called after the content has been merged, so there will only be one contact with the email address, but we'll choose the most recent just in case.</remarks>
private ContactInfo? GetMemberContactWithMatchingEmail(GuidesMember member)
{
    var contact = contactInfoProvider.Get()
        .WhereEquals(MEMBER_ID_FIELD_NAME, member.Id)
        .WhereEquals(nameof(ContactInfo.ContactEmail), member.Email)
        .OrderByDescending(nameof(ContactInfo.ContactCreated))
        .TopN(1)
        .FirstOrDefault();

    return contact;
}

/// <inheritdoc />
public void SetCurrentContactForMember(GuidesMember member)
{
    var contact = GetMemberContactWithMatchingEmail(member);
    if (contact is not null)
    {
        EnsureContactCookieLevel();
        currentContactProvider.SetCurrentContact(contact);
    }
}
...
/// <summary>
/// Ensures that the CurrentContact cookie can be created by setting the CMS cookie level to 200
/// </summary>
/// <remarks>
/// NOTE: In this project, the <see cref="DataProtection.ViewComponents.TrackingConsent.TrackingConsentViewComponent"/> will return the cookie level to 0 if the contact has not agreed to any consents.
/// Level 200 is necessary to check for consent agreements, before adjusting cookie levels accordingly.
/// </remarks>
private void EnsureContactCookieLevel()
{
    string cmsCookieLevel = cookieAccessor.Get(CookieNames.CMS_COOKIE_LEVEL);
    if (string.IsNullOrWhiteSpace(cmsCookieLevel) || !int.TryParse(cmsCookieLevel, out int cookieLevel) || cookieLevel < 200)
    {
        cookieAccessor.Set(CookieNames.CMS_COOKIE_LEVEL, "200");
    }
}
...
```

If you use code like this in a real-world project, make sure you are properly handling consent according to your region’s laws.
### Trigger the contact synchronization on sign-in
Call the `SynchronizeContact` method on member sign-in. In our Training guides example repository this means:
C#
**MembershipService.cs**
Copy
```
...
 public async Task<SignInResult> SignIn(string userNameOrEmail, string password, bool staySignedIn)
{
    try
    {
        var member = await FindMemberByUserNameOrEmail(userNameOrEmail);
        if (member is null)
        {
            return SignInResult.Failed;
        }

        var signInResult = await signInManager.PasswordSignInAsync(member.UserName!, password, staySignedIn, false);

        if (signInResult.Succeeded)
        {
            SynchronizeContact(member, true);
        }

        return signInResult;
    }
    catch (Exception ex)
    {
        logger.LogError(EventIds.MemberSignIn, ex, "An error occurred while signing in member with username or email {UserNameOrEmail} in {Service}.{Method}.", userNameOrEmail, nameof(MembershipService), nameof(SignIn));
        return SignInResult.Failed;
    }
}
...
 
 
 
 


```

## Handle member sign-out
The last piece of the puzzle for this example is handling the event of a member signing out.
You need to remove cookies when this happens, to prevent the same contact getting associated with a different member if someone else signs in in the same browser session.
On member sign out you need to clear the **CurrentContact** , **CMSCookieLevel** , and any consent-related cookies in your solution. In the Training guides solution, these are `trainingguides.cookieconsentlevel` and `trainingguides.cookielevelselection`.
See the [cookie names in our Training guides repository.](https://github.com/Kentico/xperience-by-kentico-training-guides/blob/finished/src/TrainingGuides.Web/Features/DataProtection/Shared/CookieNames.cs).
Let’s create a `RemoveContactCookies` method in our `MemberContactService` that clears the relevant cookies using the Xperience `ICookieAccessor`.
C#
**IMemberContactService.cs**
Copy
```
...
/// <summary>
/// Removes contact related cookies
/// </summary>
void RemoveContactCookies();
...
```

C#
**MemberContactService.cs**
Copy
```
...
public void RemoveContactCookies()
{
    cookieAccessor.Remove(CookieNames.CURRENT_CONTACT);
    cookieAccessor.Remove(CookieNames.CMS_COOKIE_LEVEL);
    cookieAccessor.Remove(CookieNames.COOKIE_ACCEPTANCE);
    cookieAccessor.Remove(CookieNames.COOKIE_CONSENT_LEVEL);
}
...
```

Then, call this method on member sign out. In our case, this means calling `RemoveContactCookies` inside the `SignOut` method in the `MembershipService` class.
C#
**MembershipService.cs**
Copy
```
...
public async Task SignOut()
{
    await signInManager.SignOutAsync();
    // clear cookies after sign out
    memberContactService.RemoveContactCookies();
}
...
```

## What’s next?
The [next guide on the membership topic](/guides/development/members/create-a-profile-page) will demonstrate how you can implement a secured profile page, where members can manage their personal information.
![]()
[]()[]()
