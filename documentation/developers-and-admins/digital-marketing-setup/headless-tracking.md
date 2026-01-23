---
source: https://docs.kentico.com/documentation/developers-and-admins/digital-marketing-setup/headless-tracking
scrape_date: 2026-01-22
---

  * [Home](/documentation)
  * [Developers and admins](/documentation/developers-and-admins)
  * [Digital marketing setup](/documentation/developers-and-admins/digital-marketing-setup)
  * Headless tracking 


# Headless tracking
**Advanced license required**   
  
Features described on this page require the Xperience by Kentico **Advanced** license tier. 
Websites running in Xperience by Kentico can [track visitors as contacts](/documentation/developers-and-admins/digital-marketing-setup/contact-configuration) and [log their activities](/documentation/developers-and-admins/digital-marketing-setup/set-up-activities). Headless tracking allows you to extend these features to the audience of other content channels, such as mobile apps, external services, single-page applications, etc. Xperience then serves as a central hub where you can monitor contacts and analyze activities for all connected websites and channels.
For example, headless tracking is suitable for applications that retrieve and display content from Xperience through [headless channels](/documentation/developers-and-admins/configuration/headless-channel-management).
**Relationship of headless channels and tracking**
Applications where you wish to use headless tracking must be represented in Xperience by a [headless channel](/documentation/developers-and-admins/configuration/headless-channel-management).
The channel allows you to [manage headless content](/documentation/business-users/headless-content) and [retrieve it via the GraphQL API](/documentation/developers-and-admins/development/content-retrieval/retrieve-headless-content). However, content retrieval is not a prerequisite, and you can use headless tracking in external applications that do not retrieve content from Xperience. For example, if you have an Xperience website channel and a mobile app that loads content from a different source, you can use headless tracking to synchronize the app’s user activity with your contacts in Xperience.
Headless tracking works as a REST API. The Xperience application provides endpoints to which you send HTTP requests from your external applications.
**Prerequisite**
Make sure the `Enable` and `EnableTracking` configuration keys or options are enabled for your Xperience application. See [Configure the headless API](/documentation/developers-and-admins/configuration/headless-channel-management#configure-the-headless-api) for details.
The following steps outline the typical process for setting up headless tracking in an external application:
  * [Ensure authentication and CORS for your requests](#authentication-and-cors)
  * [Display one or more tracking consents from Xperience to your application’s users](#get-the-text-of-consents-from-xperience)
  * [Allow users to accept or revoke the consents](#add-or-revoke-a-consent-agreement)
  * For users who have given a consent agreement to be tracked: 
    * [Create contacts in Xperience to represent individual users](#create-contacts)
    * [Log activities for the contacts](#log-activities)
      * Use the _Data input_ [activity](#log-activities) to send or update information about contacts
      * You can set up [custom activity types](/documentation/developers-and-admins/digital-marketing-setup/set-up-activities/custom-activities) for any actions that you wish to track in your application.


## Authentication and CORS
To authenticate headless tracking requests, you need to:
  1. Set up a [headless channel](/documentation/developers-and-admins/configuration/headless-channel-management) in Xperience.
  2. Create at least one [API key](/documentation/developers-and-admins/configuration/headless-channel-management#manage-api-keys) with the **[Access type](/documentation/developers-and-admins/configuration/headless-channel-management#access-types-of-api-keys)** set to _Tracking API_.


For each tracking API request, add the [Authorization](https://developer.mozilla.org/en-US/docs/Web/HTTP/Headers/Authorization) header containing an **enabled tracking API key**.
Text
**HTTP Authorization header**
Copy
```
Authorization: Bearer <ApiKey>
```

**Authorizaton header security recommendations**
If you set the _Authorization_ header directly when sending requests from a client API on a public web page, it can be seen by anyone. To ensure the security of your API keys, we recommend that you use a backend proxy server to add the _Authorization_ header and forward the requests to a headless tracking API endpoint.
If sending tracking requests directly from the client-side code of a web application (without additional header processing on a proxy server), you also need to ensure [Cross-Origin Resource Sharing (CORS)](https://developer.mozilla.org/en-US/docs/Web/HTTP/CORS).
Follow the instructions in [Cross-origin resource sharing](/documentation/developers-and-admins/development/content-retrieval/retrieve-headless-content#cross-origin-resource-sharing) — the CORS configuration options are shared for headless tracking endpoints and headless channel GraphQL endpoints.
## Get the text of consents from Xperience
To comply with [data protection regulations](/documentation/developers-and-admins/data-protection), most applications can only collect personal data and track activities for users who explicitly give consent.
Set up your [consents](/documentation/developers-and-admins/data-protection/consent-management) in Xperience, and retrieve their text by sending requests:
  * **HTTP method** : GET
  * **Endpoint** : /Kentico.Tracking/consents/{consent_code_name}?languageName={language_code_name}
  * **URI example** : https://domain.com/Kentico.Tracking/consents/TrackingConsent?languageName=en
  * **Response body** : Consent text strings in the `shortText` and `fullText` fields
JSON
Copy
```
{
    "shortText": "...",
    "fullText": "..."
}
```

**Consent text format**
The returned consent text strings contain HTML tags – text formatting elements, `<p>` tags for paragraphs, etc.


Create an appropriate UI in your application that displays the retrieved texts and allows users to give consent agreements.
## Create contacts
Your application’s users are represented as [contacts](/documentation/business-users/digital-marketing/contact-management) in Xperience. When a user gives consent to be tracked, you can create a new contact in Xperience by sending the following request:
  * **HTTP method** : POST
  * **Endpoint** : /Kentico.Tracking/contacts
  * **Response body** : GUID of the new contact in the `contact` field
JSON
Copy
```
{
    "contact": "06b035b8-6658-4a1b-b1de-5994e4975fb1"
}
```



The returned contact GUID is an identifier that you need to **store in your application**. Link the identifier with any other data that you have about the user. This allows you to keep track of the user in your application, identify them whenever they return, and pair them with the same contact.
The GUID is a required parameter whenever you need to [manage the contact’s consent agreements](#add-or-revoke-a-consent-agreement) or [log activities](#log-activities).
## Add or revoke a consent agreement
To add or revoke a [consent agreement](#get-the-text-of-consents-from-xperience) for a [contact](#create-contacts), send the following request:
  * **HTTP method** : POST
  * **Endpoint** : /Kentico.Tracking/contacts/{contact_guid}/consents/{consent_name}
  * **URI example** : https://domain.com/Kentico.Tracking/contacts/06b035b8-6658-4a1b-b1de-5994e4975fb1/consents/TrackingConsent
  * **Request headers** : The [Content-Type](https://developer.mozilla.org/en-US/docs/Web/HTTP/Headers/Content-Type) header must be set to _application/json_.
  * **Request body** : Set the `agree` field to `true` (agree) or `false` (revoke)
JSON
Copy
```
{
    "agree": true
}
```



## Check whether a contact has given a consent agreement
To check whether a [contact](#create-contacts) currently has a valid agreement for a [consent](#get-the-text-of-consents-from-xperience), send the following request:
  * **HTTP method** : GET
  * **Endpoint** : /Kentico.Tracking/contacts/{contact_guid}/consents/{consent_name}
  * **URI example** : https://domain.com/Kentico.Tracking/contacts/06b035b8-6658-4a1b-b1de-5994e4975fb1/consents/TrackingConsent
  * **Response body** : `true` or `false` value in the `isAgreed` field
JSON
Copy
```
{
    "isAgreed": true
}
```



## Log activities
The headless tracking API allows you to log the following types of [activities](/documentation/developers-and-admins/digital-marketing-setup/set-up-activities):
  * **Page visit** – represents a single view of a “page” in your application.
  * **Click** – represents a “click” action performed by a user.
  * **Data input** – allows you to update the data of [contacts](/documentation/business-users/digital-marketing/contact-management) using information submitted by the user.
  * [Custom activity types](/documentation/developers-and-admins/digital-marketing-setup/set-up-activities/custom-activities) – can represent any action that you wish to track.


The headless endpoint for activity logging does not automatically evaluate whether the specified contact has given consent to be tracked. Such conditions need to be implemented on the side of your external application before you log activities (see [Check whether a contact has given a consent agreement](#check-whether-a-contact-has-given-a-consent-agreement)).
Log activities by sending the following requests:
  * **HTTP method** : POST
  * **Endpoint** : /Kentico.Tracking/activities
  * **Request headers** : The [Content-Type](https://developer.mozilla.org/en-US/docs/Web/HTTP/Headers/Content-Type) header must be set to _application/json_.
  * **Request body** : 
    * `channel` – the GUID identifier of the [headless channel](/documentation/developers-and-admins/configuration/headless-channel-management) under which the activity will be logged.
      * Find the identifier in the Xperience administration: **Channel management** → edit channel → **General** → **Channel identifier for Tracking API**
      * The identifier must match the parent channel of the API key used to [authenticate](#authentication-and-cors) the logging request.
    * `contact` – the GUID of the contact who performed the activity (see [Create contacts](#create-contacts)).
    * `activities` – an array of activity objects. You can log any number of activities within a single request. See below to learn about the data structure of activity objects.


For each activity object in the `activities` array, set the following fields:
  * `type` – the _code name_ of the activity type that you wish to log. Use the following values:
    * pagevisit
    * click
    * datainput
    * _< activity type code name>_ for custom activities
  * `value` – a value logged with the activity. Required for _Click_ and _Data input_ activities.
  * `url` – optional for all activity types (except _Page visit_). Currently not displayed in the activity log within the Xperience administration.
  * `referrer` – an optional [URL referrer](https://developer.mozilla.org/en-US/docs/Web/HTTP/Headers/Referer) value. Currently not displayed in the activity log within the Xperience administration.


Additionally, individual activity types have additional requirements:
Activity type |  Instructions  
---|---  
Page visit |  The `url` field is required for page visit activities (for potential use in [Contact group](/documentation/business-users/digital-marketing/contact-groups) conditions). Set the `value` field to the title/name of the visited page. The title of the resulting activity is in format: _Page visit ‘ <value>’_  
Click |  The `Value` field is required for _Click_ activites. We recommend setting the value to the name, label or identifier of the clicked UI element. The title of the resulting activity is in format: _Clicked ‘ <value>’_  
Data input |  Populate `contactFields` with a JSON object containing fields matching the names of contact fields that you wish to set. The following contact fields are available:
  * **ContactEmail**
    * Note: For security reasons, the _Data input_ activity cannot be used to change a contact’s existing email value. If the contact already has an email address stored in Xperience that doesn’t match the new email value, the _Data input_ activity and all related field updates are performed for a different contact. Either an existing contact that matches the submitted email value is used, or a new contact is created.
  * **ContactFirstName**
  * **ContactMiddleName**
  * **ContactLastName**
  * **ContactBirthday**
  * **ContactMobilePhone** (_Private phone_ in the Xperience _Contact management_ application)
  * **ContactBusinessPhone**
  * **ContactCompanyName**
  * **ContactJobTitle**
  * **ContactAddress1**
  * **ContactCity**
  * **ContactZIP**
  * **ContactCountry** (accepts [3 letter ISO country codes](https://en.wikipedia.org/wiki/ISO_3166-1_alpha-3))
  * **ContactState** (accepts 2 letter state codes, currently only [USA state codes (ANSI)](https://en.wikipedia.org/wiki/List_of_U.S._state_and_territory_abbreviations) are supported)
  * **ContactNotes**

Only `string` values are supported for the contact fields. The `value` field is required for _Data input_ activities. For example, you can set the value to describe the source of the contact data. The title of the resulting activity is in format: _Data input ‘ <value>’_ You can view the collected contact data in the Xperience **Contact management** application and use it to build [contact group](/documentation/business-users/digital-marketing/contact-groups) conditions.  
Custom activities |  The `type` field must match the **Code name** of an enabled [custom activity type](/documentation/developers-and-admins/digital-marketing-setup/set-up-activities/custom-activities). The title of the resulting activity is the custom activity type’s name, followed by _‘ <value>’_ if the `value` field was specified.  
JSON
**Request body example**
Copy
```
{
    "channel": "a3f4bc8b-6d84-454f-8215-cbec70bfaadd",
    "contact": "06b035b8-6658-4a1b-b1de-5994e4975fb1",
    "activities": [
        {
            "type": "pagevisit",
            "url": "https://example.com/mypage"
        },
        {
            "type": "click",
            "value": "MyClickableElement"
        },
        {
            "type": "datainput",
            "value": "Registration form",
            "contactFields":
            {
                "ContactFirstName": "Luke",
                "ContactLastName" : "Smith",
                "ContactEmail" : "lsmith@domain.com"
            }
        },
        {
            "type": "custom_loan_calculator",
            "value": "5000"
        }
    ]
}
```

Activity logging requests return an [HTTP 207 Multi-Status](https://developer.mozilla.org/en-US/docs/Web/HTTP/Status/207) response. The response body contains a `results` array where you can find the HTTP status code for individual submitted activities.
JSON
**Response example**
Copy
```
{
    "results": [
        {
            "status": 202,
        },
        {
            "status": 202
        }
    ]
}
```

![]()
[]()[]()
