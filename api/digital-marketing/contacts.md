# Contacts
  * [ Copy page link ](api/digital-marketing/contacts#) | [Get HelpService ID](api/digital-marketing/contacts#)
Core MVC 5


[✖](api/digital-marketing/contacts# "Close page link panel") [Copy to clipboard](api/digital-marketing/contacts#)
  
List of examples:
  * [Dependency injection](api/digital-marketing/contacts#dependency-injection)
    * [Initialize required services](api/digital-marketing/contacts#initialize-required-services)
  * [Contacts](api/digital-marketing/contacts#contacts)
    * [Get the current contact](api/digital-marketing/contacts#get-the-current-contact)
    * [Create contacts](api/digital-marketing/contacts#create-contacts)
    * [Update contacts](api/digital-marketing/contacts#update-contacts)
    * [Update multiple contacts](api/digital-marketing/contacts#update-multiple-contacts)
    * [Delete contacts](api/digital-marketing/contacts#delete-contacts)
    * [Delete contacts in bulk](api/digital-marketing/contacts#delete-contacts-in-bulk)


## Dependency injection
### Initialize required services
C#
Copy
```
// Initializes all services and provider classes used within
// the API examples on this page using dependency injection.
private readonly IInfoProvider<ContactInfo> contactInfoProvider;
private readonly IContactsBulkDeletionService contactDeletionService;

public ContactsServices(IInfoProvider<ContactInfo> contactInfoProvider, IContactsBulkDeletionService contactDeletionService)
{
    this.contactInfoProvider = contactInfoProvider;
    this.contactDeletionService = contactDeletionService;
}
```

[> Back to list of examples](api/digital-marketing/contacts#toc)
## Contacts
### Get the current contact
C#
Copy
```
// Gets the contact related to the currently processed request
ContactInfo currentContact = ContactManagementContext.CurrentContact;
```

[> Back to list of examples](api/digital-marketing/contacts#toc)
### Create contacts
C#
Copy
```
// Creates a new contact object
ContactInfo newContact = new ContactInfo()
{
    ContactLastName = "Smith",
    ContactFirstName = "John",

    // Enables activity tracking for the new contact
    ContactMonitored = true
};

// Saves the contact to the database
contactInfoProvider.Set(newContact);
```

[> Back to list of examples](api/digital-marketing/contacts#toc)
### Update contacts
C#
Copy
```
// Gets the first contact whose last name is 'Smith'
ContactInfo updateContact = contactInfoProvider.Get()
                                    .WhereEquals("ContactLastName", "Smith")
                                    .TopN(1)
                                    .FirstOrDefault();

if (updateContact != null)
{
    // Updates the contact's properties
    updateContact.ContactCompanyName = "Company Inc.";

    // Saves the updated contact to the database
    contactInfoProvider.Set(updateContact);
}
```

[> Back to list of examples](api/digital-marketing/contacts#toc)
### Update multiple contacts
C#
Copy
```
// Gets all contacts whose last name is 'Smith'
var contacts = contactInfoProvider.Get()
                                  .WhereEquals("ContactLastName", "Smith");

// Loops through individual contacts
foreach (ContactInfo contact in contacts)
{
    // Updates the properties of the contact
    contact.ContactCompanyName = "Company Inc.";

    // Saves the updated contact to the database
    contactInfoProvider.Set(contact);
}
```

[> Back to list of examples](api/digital-marketing/contacts#toc)
### Delete contacts
C#
Copy
```
// Gets the first contact whose last name is 'Smith'
ContactInfo deleteContact = contactInfoProvider.Get()
                                    .WhereEquals("ContactLastName", "Smith")
                                    .TopN(1)
                                    .FirstOrDefault();

if (deleteContact != null)
{
    // Deletes the contact
    contactInfoProvider.Delete(deleteContact);
}
```

[> Back to list of examples](api/digital-marketing/contacts#toc)
### Delete contacts in bulk
C#
Copy
```
var dateOneWeekAgo = DateTime.Now.AddDays(-7);

// WhereCondition that selects contacts that are at least 7 days old and have an empty email address
var condition = new WhereCondition()
                .WhereLessThan(nameof(ContactInfo.ContactCreated), dateOneWeekAgo)
                .And()
                .WhereEmpty(nameof(ContactInfo.ContactEmail));

// Deletes contacts matching the specified condition
// Doesn't specify a batch size, which means that contacts are deleted in batches of 1000 (automatically repeats)
await contactDeletionService.BulkDelete(condition);
```

[> Back to list of examples](api/digital-marketing/contacts#toc)