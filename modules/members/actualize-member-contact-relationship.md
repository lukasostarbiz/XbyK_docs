---
source: https://docs.kentico.com/modules/members/actualize-member-contact-relationship
scrape_date: 2026-01-22
---

Module: Members
6 of 12 Pages
# Actualize the member-contact relationship
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

[ Previous page ](/modules/members/create-member-contact-relationship)
6 of 12
[ Mark complete and continue ](/modules/members/profile-form)
![]()
[]()[]()
