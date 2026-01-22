---
source: https://docs.kentico.com/modules/members/create-member-contact-relationship
scrape_date: 2026-01-22
---

Module: Members
5 of 12 Pages
# Create a member-contact relationship
A [Contact](/documentation/business-users/digital-marketing/contact-management) object in Xperience by Kentico represents a website visitor. As you saw [earlier in this series](/modules/members/groundwork-registration-widget), you can enable **registration** and implement sign in logic for your solution that creates [members](/documentation/business-users/members).
For many reasons it is useful to keep the the **member synchronized with the corresponding contact**. On successful member registration, the system logs the event for the contact and maps the member’s email to the contact if the contact’s email is empty. This synchronization aligns email data but doesn’t take care of any custom fields, such as name and other information relevant for your domain. Nor does it establish a direct link between the two objects.
This guide expands on an [example from our documentation](/documentation/developers-and-admins/digital-marketing-setup/contact-configuration/map-custom-member-fields-to-contacts), showing how to map custom member and contact fields, and use them to create link between the two objects.
The examples here rely in part on examples from other training materials:
  * The [Add a custom field to the Contact profile](/guides/development/customizations-and-integrations/add-custom-contact-field) guide. Our example uses a custom contact field created here.
  * The [Data protection](/guides/development/data-protection) series. Our example utilizes cookie logic and the consent banner from this series.


If you have not completed these requirements, you can consider the parts of this example relating to _custom contact fields_ , _consent_ , and _cookie management_ optional.
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
Now, in your website, use the registration page we created [earlier in this series](/modules/members/groundwork-registration-widget) to register a new member.
When you go back to your administration dashboard and reload the **Contact management** , you will see that your new contact has its fields set according to what you filled out for the member. Including the custom fields. Success!
Your browser does not support the video tag. 
[![Shows the custom contact fields filled out according to the member data](/docsassets/guides/map-member-to-contact/custom-contact-fields-filled-out.png)](/docsassets/guides/map-member-to-contact/custom-contact-fields-filled-out.png)
[ Previous page ](/modules/members/register-the-member)
5 of 12
[ Mark complete and continue ](/modules/members/actualize-member-contact-relationship)
![]()
[]()[]()
