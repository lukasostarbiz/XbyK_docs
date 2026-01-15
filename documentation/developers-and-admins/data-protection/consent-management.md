# Consent management
  * [ Copy page link ](documentation/developers-and-admins/data-protection/consent-management#) | [Get HelpService ID](documentation/developers-and-admins/data-protection/consent-management#)
Core MVC 5


[✖](documentation/developers-and-admins/data-protection/consent-management# "Close page link panel") [Copy to clipboard](documentation/developers-and-admins/data-protection/consent-management#)
In Xperience, consents are records used to inform website visitors about the means of collecting and manipulating their personal data by the system, site administrators, marketers and anyone else who has access to said data, including third parties to whom this data is forwarded.
You can use consents to comply with the requirements of the [GDPR](documentation/developers-and-admins/data-protection/gdpr-compliance) and other personal data regulations.
Whenever any personal data of a visitor is obtained, it is necessary to have a consent agreement from the visitor to legally process this data. This includes [tracking of contacts](documentation/business-users/digital-marketing/contact-management) and their [activities](documentation/business-users/digital-marketing/contact-activities) on the live site. Every consent agreement of a visitor is directly connected to a corresponding [contact](documentation/business-users/digital-marketing/contact-management). Deleting contacts from the system therefore also causes deletion of the contact’s consent agreements.
**Definition of consent**
“‘Consent’ of the data subject means any freely given, specific, informed and unambiguous indication of the data subject’s wishes by which he or she, by a statement or by a clear affirmative action, signifies agreement to the processing of personal data relating to him or her.”
(Source: [GDPR Article 4](https://eur-lex.europa.eu/legal-content/EN/TXT/HTML/?uri=CELEX:32016R0679#d1e1489-1-1), Paragraph 11)
This page describes how to [create consents](documentation/developers-and-admins/data-protection/consent-management#create-consents), view [consent agreements](documentation/developers-and-admins/data-protection/consent-management#view-consent-agreements), and [view the older, archived versions of consent texts](documentation/developers-and-admins/data-protection/consent-management#view-archived-consents). 
## Create consents
  1. Open the **Data protection** application and switch to the **Consents** tab.
  2. Select **New consent**.
  3. Type a **Consent name** and a optionally a code name in the **Identifiers** section.
  4. Select **Save**.
  5. On the **Consent text** tab, compose the content of the consent in the text areas:
     * **Short text** – a brief explanation of the consent’s content for public users. The short text is shown to visitors when presenting an option to give consent agreements.
     * **Full text** – contains the full content of the consent, including any required legal terminology. The full text can be made available upon request, for example through a dedicated privacy policy page.
  6. Select **Save** to save the text.


The system saves the consent. You can modify the consent at any time by selecting it on the **Consents** tab of the **Data protection** application.
Learn how to include consents on your website in [Consent development](documentation/developers-and-admins/data-protection/consent-development).
### Translate consents to other languages
If you have multiple [languages](documentation/developers-and-admins/configuration/languages) configured for your project, you can create different language variants of consent texts. 
Select the consent in the **Data protection** application and open the **Consent text** tab. Switch between language variants using the selector in the top left.
[![Switching between language variants of a consent](docsassets/documentation/consent-management/Consents_language_variants.png)](https://docs.kentico.com/docsassets/documentation/consent-management/Consents_language_variants.png)
When switching to a language where the consent text is not yet translated, you can:
  * **Create empty consent text** – write the text from scratch in the given language.
  * **Copy consent text from another language** – select a language in which the consent text already exists, and use it as a starting point for the new language variant.


Select **Continue** , add the translated texts, and **Save** the consent.
Once the language variants are prepared, the texts are displayed according to the language in which visitors view the content. If a consent variant is not available in a language, the system attempts to display other variants of the consent text based the **fallback settings** of languages in your project (see [Languages](documentation/developers-and-admins/configuration/languages) to learn more).
**Language variants and consent agreements**
The purpose of consent language variants is to provide different short and full text content in individual languages. However, the system does not separate consent agreements based on language – a visitor has either given an agreement with the consent (in any language) or has not.
Therefore, the text of **all language variants must be legally equal** and accurately cover the functionality and personal data processing associated with the consent.
## View consent agreements
After you create consents and make it possible for visitors to give consent agreements on your website, you can view a list of agreements for individual consents:
  1. Open the **Data protection** application and switch to the **Consents** tab.
  2. Select the consent from the listing of available consents.
  3. Switch to the **Consent agreements** tab.


The tab displays a list of all visitors who have given an agreement for the selected consent. The list includes visitors who have given an agreement for an [older, archived version](documentation/developers-and-admins/data-protection/consent-management#view-archived-consents) of the consent, but not those who have revoked the consent at a later point.
The visitor information is based on the [contact](documentation/business-users/digital-marketing/contact-management) data available in the system.
## View archived consents
When a visitor gives a consent agreement, it is based on the exact text of the consent at the given time. However, consent texts may change at a later time.
To keep track of the exact consent versions for which visitors have given agreements, automatic consent archiving is performed. When a consent’s text is modified and saved, the system checks whether the site’s contacts have given at least one agreement since the last update. If this is the case, an archived version of the previous consent text is created. Every archived consent is represented by a unique hash string, and contains all language variants created for the given consent.
**Modifying consents**
When making major changes to consent texts, the best practice is to create new consents rather than modifying existing consents.
To see the archived versions of a specific consent and all of its language variants:
  1. Open the **Data protection** application and switch to the **Consents** tab.
  2. Select the consent from the listing of available consents.
  3. Switch to the **Consent archive** tab.


The tab displays all archived versions of the selected consent for which at least one visitor has given an agreement. You can also view the short and full text of the consent language variants.