---
source: https://docs.kentico.com/api/digital-marketing/contact-groups
scrape_date: 2026-01-26
---

  * [Home](/api)
  * [Digital marketing](/api/digital-marketing)
  * Contact groups 


# Contact groups
  
List of examples:
  * [Dependency injection](#dependency-injection)
    * [Initialize required services](#initialize-required-services)
    * [Create contact groups](#create-contact-groups)
    * [Update contact groups](#update-contact-groups)
    * [Update multiple contact groups](#update-multiple-contact-groups)
    * [Delete contact groups](#delete-contact-groups)


## Dependency injection
### Initialize required services
C#
Copy
```
// Initializes all services and provider classes used within
// the API examples on this page using dependency injection.
private readonly IInfoProvider<ContactInfo> contactInfoProvider;
private readonly IInfoProvider<ContactGroupInfo> contactGroupInfoProvider;
private readonly IInfoProvider<ContactGroupMemberInfo> contactGroupMemberInfoProvider;


public ContactGroupsServices(IInfoProvider<ContactInfo> contactInfoProvider,
                     IInfoProvider<ContactGroupInfo> contactGroupInfoProvider,
                     IInfoProvider<ContactGroupMemberInfo> contactGroupMemberInfoProvider)
{
    this.contactInfoProvider = contactInfoProvider;
    this.contactGroupInfoProvider = contactGroupInfoProvider;
    this.contactGroupMemberInfoProvider = contactGroupMemberInfoProvider;
}
```

[> Back to list of examples](#toc)
### Create contact groups
C#
Copy
```
// Creates a new contact group object
ContactGroupInfo newGroup = new ContactGroupInfo()
{
    ContactGroupDisplayName = "New group",
    ContactGroupName = "NewGroup",
    ContactGroupEnabled = true,
    ContactGroupDynamicCondition = "{%Rule(\"(Contact.AgreedWithConsent(\"form\"))\"," +
    " \"<rules><r pos=\"0\" par=\"\" op=\"and\" /><r pos=\"0\" par=\"0\" op=\"and\" n=\"CMSContactHasAgreedWithConsent\" >" +
    "<p n=\"_perfectum\"><t>has</t><v></v><r>0</r><d>select operation</d><vt>text</vt><tv>0</tv></p><p n=\"consent\">" +
    "<t>#select consent</t><v>form</v><r>0</r><d>select consent</d><vt>text</vt><tv>0</tv></p></r></rules>\") %}"
};

// Saves the contact group to the database
contactGroupInfoProvider.Set(newGroup);
```

[> Back to list of examples](#toc)
### Update contact groups
C#
Copy
```
// Gets the contact group
ContactGroupInfo updateGroup = contactGroupInfoProvider.Get("NewGroup");

if (updateGroup != null)
{
    // Updates the contact group's properties
    updateGroup.ContactGroupDisplayName = updateGroup.ContactGroupDisplayName.ToLowerInvariant();

    // Saves the modified contact group to the database
    contactGroupInfoProvider.Set(updateGroup);
}
```

[> Back to list of examples](#toc)
### Update multiple contact groups
C#
Copy
```
// Gets all contact groups whose name starts with 'New'
var groups = contactGroupInfoProvider.Get().WhereStartsWith("ContactGroupName", "New");

// Loops through individual contact groups
foreach (ContactGroupInfo group in groups)
{
    // Updates the contact group properties
    group.ContactGroupDisplayName = group.ContactGroupDisplayName.ToUpper();

    // Saves the modified contact group to the database
    contactGroupInfoProvider.Set(group);
}
```

[> Back to list of examples](#toc)
### Delete contact groups
C#
Copy
```
// Gets the contact group
ContactGroupInfo deleteGroup = contactGroupInfoProvider.Get("NewGroup");

if (deleteGroup != null)
{
    // Deletes the contact group
    contactGroupInfoProvider.Delete(deleteGroup);
}
```

[> Back to list of examples](#toc)
![]()
[]()[]()
