# Consents
  * [ Copy page link ](api/configuration/consents#) | [Get HelpService ID](api/configuration/consents#)
Core MVC 5


[✖](api/configuration/consents# "Close page link panel") [Copy to clipboard](api/configuration/consents#)
  
List of examples:
  * [Dependency injection](api/configuration/consents#dependency-injection)
    * [Initialize required services](api/configuration/consents#initialize-required-services)
  * [Consents](api/configuration/consents#consents)
    * [Create consents](api/configuration/consents#create-consents)
    * [Update consents](api/configuration/consents#update-consents)
    * [Create consent language versions](api/configuration/consents#create-consent-language-versions)
    * [Delete consents](api/configuration/consents#delete-consents)
  * [Consent agreements](api/configuration/consents#consent-agreements)
    * [Create a consent agreement](api/configuration/consents#create-a-consent-agreement)
    * [Check a consent agreement](api/configuration/consents#check-a-consent-agreement)
    * [Revoke a consent agreement](api/configuration/consents#revoke-a-consent-agreement)
    * [Get the consent agreements of a contact](api/configuration/consents#get-the-consent-agreements-of-a-contact)


## Dependency injection
### Initialize required services
C#
Copy
```
// Initializes all services and provider classes used within
// the API examples on this page using dependency injection.
private readonly IInfoProvider<ConsentInfo> consentInfoProvider;
private readonly IConsentAgreementService consentAgreementService;
private readonly IInfoProvider<ContactInfo> contactInfoProvider;

public ConsentsServices(IInfoProvider<ConsentInfo> consentInfoProvider,
                         IConsentAgreementService consentAgreementService,
                         IInfoProvider<ContactInfo> contactInfoProvider)
{
    this.consentInfoProvider = consentInfoProvider;
    this.consentAgreementService = consentAgreementService;
    this.contactInfoProvider = contactInfoProvider;
}
```

[> Back to list of examples](api/configuration/consents#toc)
## Consents
### Create consents
C#
Copy
```
// Creates a new consent object
ConsentInfo newConsent = new ConsentInfo()
{
    ConsentName = "NewConsent",
    ConsentDisplayName = "New Consent",
};

// Defines and sets content of the consent
string shortText = "This is a sample consent.";
string fullText = "Lorem ipsum dolor sit amet, consectetur adipiscing elit.";
newConsent.UpsertConsentText("english", shortText, fullText);

// Saves the created consent to the database
newConsent.Update();
```

[> Back to list of examples](api/configuration/consents#toc)
### Update consents
C#
Copy
```
// Gets the consent
ConsentInfo existingConsent = consentInfoProvider.Get("NewConsent");

// Defines and updates content of the consent
ConsentText consentText = await existingConsent.GetConsentTextAsync("english");
string shortText = consentText.ShortText.Replace("consent", "consent declaration");
string fullText = consentText.FullText;
existingConsent.UpsertConsentText("english", shortText, fullText);

// Saves the created consent object to the database.
// If at least one contact gave an agreement with this consent,
// the content of the consent is archived before updating.
existingConsent.Update();
```

[> Back to list of examples](api/configuration/consents#toc)
### Create consent language versions
C#
Copy
```
// Gets the consent
ConsentInfo existingConsent = consentInfoProvider.Get("NewConsent");

// Defines and updates content of the Spanish language variant
string shortText = "Esta es una muestra de consentimiento.";
string fullText = "ESP Lorem ipsum dolor sit amet, consectetur adipiscing elit.";
existingConsent.UpsertConsentText("spanish", shortText, fullText);

// Saves the created consent object to the database
existingConsent.Update();
```

[> Back to list of examples](api/configuration/consents#toc)
### Delete consents
C#
Copy
```
// Gets the consent
ConsentInfo deleteConsent = consentInfoProvider.Get("NewConsent");

if (deleteConsent != null)
{
    // Deletes the consent
    consentInfoProvider.Delete(deleteConsent);
}
```

[> Back to list of examples](api/configuration/consents#toc)
## Consent agreements
### Create a consent agreement
C#
Copy
```
// Gets the consent
ConsentInfo consent = consentInfoProvider.Get("NewConsent");

// Gets the first contact whose name is John Doe
ContactInfo contact = contactInfoProvider.Get()
                                         .WhereEquals("ContactLastname", "Doe")
                                         .WhereEquals("ContactFirstName", "John")
                                         .TopN(1)
                                         .FirstOrDefault();

// Creates a consent agreement signifying that the contact has given an agreement with the consent
consentAgreementService.Agree(contact, consent);
```

[> Back to list of examples](api/configuration/consents#toc)
### Check a consent agreement
C#
Copy
```
// Gets the consent
ConsentInfo consent = consentInfoProvider.Get("NewConsent");

// Gets the first contact whose name is John Doe
ContactInfo contact = contactInfoProvider.Get()
                                         .WhereEquals("ContactLastname", "Doe")
                                         .WhereEquals("ContactFirstName", "John")
                                         .TopN(1)
                                         .FirstOrDefault();

// Checks if the contact has given an agreement with the consent
bool agreed = consentAgreementService.IsAgreed(contact, consent);
```

[> Back to list of examples](api/configuration/consents#toc)
### Revoke a consent agreement
C#
Copy
```
// Gets the consent
ConsentInfo consent = consentInfoProvider.Get("NewConsent");

// Gets the first contact whose name is John Doe
ContactInfo contact = contactInfoProvider.Get()
                                         .WhereEquals("ContactLastname", "Doe")
                                         .WhereEquals("ContactFirstName", "John")
                                         .TopN(1)
                                         .FirstOrDefault();

// Revokes the previously created consent agreement
consentAgreementService.Revoke(contact, consent);
```

[> Back to list of examples](api/configuration/consents#toc)
### Get the consent agreements of a contact
C#
Copy
```
// Gets the first contact whose name is John Doe
ContactInfo contact = contactInfoProvider.Get()
                                         .WhereEquals("ContactLastname", "Doe")
                                         .WhereEquals("ContactFirstName", "John")
                                         .TopN(1)
                                         .FirstOrDefault();

// Gets the list of consents with which the contact gave a consent agreement.
// The 'Consent' object contains information about a consent declaration. The 'GetConsentText'
// method gets the content of the consent from either the current consent declaration or from
// the consent archive, depending on the version of the consent with which the contact gave
// the agreement.
IEnumerable<Consent> consents = consentAgreementService.GetAgreedConsents(contact);

// Iterates through the retrieved consents
foreach (Consent consent in consents)
{
    // Gets the properties of the individual consents
    string displayName = consent.DisplayName;
    int id = consent.Id;

    // Gets the content of the consent
    string shortText = consent.GetConsentText("english").ShortText;
    string fullText = consent.GetConsentText("english").FullText;
}
```

[> Back to list of examples](api/configuration/consents#toc)