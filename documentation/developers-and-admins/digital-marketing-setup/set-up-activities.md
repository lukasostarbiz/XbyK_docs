---
source: https://docs.kentico.com/documentation/developers-and-admins/digital-marketing-setup/set-up-activities
scrape_date: 2026-01-26
---

  * [Home](/documentation)
  * [Developers and admins](/documentation/developers-and-admins)
  * [Digital marketing setup](/documentation/developers-and-admins/digital-marketing-setup)
  * Set up activities 


# Set up activities
Activities represent actions that your [contacts](/documentation/business-users/digital-marketing/contact-management) perform. For example, the system logs activities when visitors view [pages](/documentation/business-users/website-content) or submit [forms](/documentation/business-users/digital-marketing/forms). The gathered [activity log](/documentation/business-users/digital-marketing/contact-activities) allows you to evaluate the behavior of contacts and visitors across your content channels.
## Activity logging requirements
Activity logging for contacts in Xperience is **enabled by default**.
Due to [personal data protection](/documentation/developers-and-admins/data-protection) policies, most activities are only logged for visitors who give [consent to be tracked as contacts](/documentation/developers-and-admins/data-protection/consent-development) and have the required [cookie level](/documentation/developers-and-admins/data-protection/cookies) set (_Visitor_ or _All_).
Some activities are logged automatically without any setup. However, certain activities require additional adjustments of your application.
### Activity logging based on legitimate interest
The system logs **Form submission** and **Member registration** activities even for visitors who have not given tracking consent.
Data added into the system as a result of such activities only includes information explicitly entered into the related [form](/documentation/business-users/digital-marketing/forms) (or [member registration](/documentation/developers-and-admins/development/registration-and-authentication) form).
For example, when an untracked visitor submits a form, the system creates a new contact with data based on the form’s [mapping of fields to contact attributes](/documentation/business-users/digital-marketing/forms/create-and-edit-forms). If the form contains an email address field and the submitted email matches an existing contact, the data is instead added or updated for the given contact. This type of activity logging does not enable any other type of tracking for the visitor.
Additionally, the system does not include sensitive context-specific information when logging such activities (for example URLs, channel identifiers, etc.).
**Important**
You need to ensure that usage of personal data collected through such activities falls under legitimate interest purposes, based on your project’s legal requirements. For example, you may be able to legally use the data to send [form autoresponder emails](/documentation/developers-and-admins/digital-marketing-setup/custom-form-autoresponder-emails). However, using the data to send unsolicited marketing messages may be a breach of [data protection regulations](/documentation/developers-and-admins/data-protection).
## Activity type overview
Xperience by Kentico tracks the following activities:
Activity type |  Code name |  Description  
---|---|---  
Click |  click |  The visitor performed a click on a website. Logged via [headless tracking API](/documentation/developers-and-admins/digital-marketing-setup/headless-tracking) or [cross-site tracking](/documentation/developers-and-admins/digital-marketing-setup/cross-site-tracking).  
Data input |  datainput |  The visitor entered data through a specific input. For example, such activities can be logged in headless tracking scenarios when a visitor submits information through a form in an external mobile app. Logged via [headless tracking API](/documentation/developers-and-admins/digital-marketing-setup/headless-tracking) or [cross-site tracking](/documentation/developers-and-admins/digital-marketing-setup/cross-site-tracking).  
Email click |  emailclick |  The recipient of an [email](/documentation/business-users/digital-marketing/emails) clicked a link within the content. **Logging requirements** [Email statistic tracking](/documentation/developers-and-admins/digital-marketing-setup/set-up-email-tracking) must be enabled and set up for your application. To ensure compliance with data protection regulations, email activity logging is not active by default. Developers need to implement logic that decides when it is possible to track email activities for specific contacts, for example based on [consents](/documentation/developers-and-admins/data-protection/consent-management). See [Log email activities](/documentation/developers-and-admins/digital-marketing-setup/set-up-email-tracking/log-email-activities) for detailed information.  
Form submission |  bizformsubmit |  The visitor submitted a [form](/documentation/business-users/digital-marketing/forms). Logged automatically.  
Landing page |  landingpage |  The visitor opened a page when first viewing a website channel. **Logging requirements** To log _Landing page_ activities, you need to [enable the activity tracking feature](#enable-the-activity-tracking-feature) for your application. The _Landing page_ activity logging is affected by the  _CMSLandingPageLoaded_ cookie, which expires after 20 minutes. The expiration period of the key is renewed every time the website channel is accessed again.  
Member registration |  memberregistration |  The visitor registers as a [member](/documentation/business-users/members). Logged when the member becomes active (enabled), for example after confirming the registration when using double opt-in via [email confirmation](/documentation/developers-and-admins/development/registration-and-authentication/forms-authentication#email-confirmation). Logged automatically.  
Page visit |  pagevisit |  The visitor viewed a web page. The page can be presented by an Xperience [website channel](/documentation/developers-and-admins/configuration/website-channel-management), a site using the [headless tracking API](/documentation/developers-and-admins/digital-marketing-setup/headless-tracking), or a site covered by [cross-site tracking](/documentation/developers-and-admins/digital-marketing-setup/cross-site-tracking). **Logging requirements** To log _Landing page_ and _Page visit_ activities, you need to [enable the activity tracking feature](#enable-the-activity-tracking-feature) for your application.  
**Custom activity types**
In addition to the default activity types, you can define your own custom types. Logging of custom activities always needs to be ensure by the project’s developers, according to the intended purpose of the activities.
See [Custom activities](/documentation/developers-and-admins/digital-marketing-setup/set-up-activities/custom-activities) for more information.
### Default activity type internals
Some of the default activity types are logged with references to other entities in the system that are related to the given activity. These references are stored as identifier values in the `ActivityItemID` and `ActivityItemDetailID` columns of individual activity records.
Understanding these values can be useful for developers in advanced scenarios, for example when implementing custom reports for activity data.
Activity type |  Logged references  
---|---  
Email click |  **ActivityItemID** – the ID of the related email configuration entity (record stored in the `EmailLibrary_EmailConfiguration` table and represented by `EmailConfigurationInfo` in the API). **ActivityItemDetailID** – the ID of the related email link entity (record stored in the `EmailLibrary_EmailLink` table and represented by `EmailLinkInfo` in the API).  
Form submission |  **ActivityItemID** – the ID of the submitted [form](/documentation/business-users/digital-marketing/forms/create-and-edit-forms) entity (record stored in the `CMS_Form` table, represented by `BizFormInfo` in the API). **ActivityItemDetailID** – the ID of the [form submission](/documentation/business-users/digital-marketing/forms/manage-form-submissions) entity (record stored in the form’s dedicated database table, represented by `BizFormItem` in the API).  
Member registration |  **ActivityItemID** – the ID of the created [member](/documentation/business-users/members) entity (record stored in the `CMS_Member` table and represented by `MemberInfo` in the API).  
## Enable the activity tracking feature
To log the _Landing page_ and _Page visit_ activities, you need to explicitly enable the activity tracking feature for your application. The feature registers routes that receive and process activity logging requests.
  1. Open your project in Visual Studio.
  2. Edit the **Program.cs** file.
  3. [Enable](/documentation/developers-and-admins/development/website-development-basics/configure-new-projects/enable-application-features) activity tracking by calling the `UseActivityTracking` method of the `IFeaturesBuilder` delegate. Pass the delegate as an argument to `AddKentico`.
C#
Copy
```
using Kentico.Activities.Web.Mvc;
using Kentico.Web.Mvc;

...

WebApplicationBuilder builder = WebApplication.CreateBuilder(args);

...

builder.Services.AddKentico(features =>
{
    features.UseActivityTracking();
});
```

  4. To log page visits for [website channels](/documentation/developers-and-admins/configuration/website-channel-management), register the activity logging script onto all pages by calling the `ActivityLoggingScriptV2` extension method in views.
     * Call the method in your website’s main layout (and any other used layouts) to ensure that performed activities are logged for every page.
     * The logging script runs asynchronously, so we recommend adding it at the end of the _< head>_ tag in the page code.
     * The method’s `logPageVisit` parameter must be `true`. This is the default state if you call the method without any parameters.
cshtml
Copy
```
@using Kentico.Activities.Web.Mvc

@* Registers scripts that ensure logging of page-related activities *@
@Html.Kentico().ActivityLoggingScriptV2()
```



Enabling the `UseActivityTracking` feature ensures that the system registers routes that receive activity logging requests.
After adding the code mentioned above, the _Page visit_ activity is logged every time a page is visited, and the _Landing page_ activity is logged every time a visitor opens a page when first viewing the website.
## Enable or disable specific activity types
By default, logging is enabled for all [activity types](#activity-type-overview). You can disable or re-enable individual activity types at any time:
  1. Open the **Contact management** application in the Xperience administration.
  2. Switch to the **Activity** **types** tab.
  3. Select the activity type that you wish to configure.
  4. Select or clear the **Enabled** checkbox.
  5. **Save** the activity type.


## Activity and contact update processing
When activities or contact detail updates occur, the system initially saves the data into the application memory. This data is then batch processed from the memory into the database every 10 seconds by default.
If your system processes a large number of activities and contact updates, you can consider increasing the default interval:
  1. Edit your project’s configuration file (_appsettings.json_ by default).
  2. Add the `CMSProcessContactActionsInterval` key. Increase the value to reflect how often you want the activities and contact updates to be processed.
JSON
**Processing activities and contact updates every 30 seconds**
Copy
```
"CMSProcessContactActionsInterval": 30,
```

  3. Save the file.


The system now processes activities and contact detail updates according to the configured interval. The interval affects the following:
  * How often the application saves logged activities and contact detail updates from the memory into the database.
  * How often the system recalculates data that could be affected by activities and contact updates, such as contact groups conditions, automation process triggers, scores, etc.


![]()
[]()[]()
