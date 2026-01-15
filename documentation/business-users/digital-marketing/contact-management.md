# Contact management
  * [ Copy page link ](documentation/business-users/digital-marketing/contact-management#) | [Get HelpService ID](documentation/business-users/digital-marketing/contact-management#) | This page is part of a module: [ Contact management ](modules/contact-management)
Core MVC 5


[✖](documentation/business-users/digital-marketing/contact-management# "Close page link panel") [Copy to clipboard](documentation/business-users/digital-marketing/contact-management#)
Contact management is a digital marketing tool that provides a centralized overview of all people who access content through your channels. Within Xperience by Kentico, visitors are represented by **Contacts**.
The system gathers information about all visitors including anonymous ones, and [tracks the activities](documentation/business-users/digital-marketing/contact-activities) that contacts perform through your channels. **On websites** , contacts are identified by a cookie, and the system automatically gathers data based on the actions and input of the associated visitors.
You can use the data collected for contacts to analyze client behavior, optimize content, identify potential buyers and perform other marketing‑related actions. In Xperience, [customer journeys](documentation/business-users/digital-marketing/customer-journeys) allow you to define key stages, representing the sequence of steps a contact must complete to achieve a successful conversion, and set conditions for their progression through these steps.
**Contacts and channels**
Contacts are global and shared across all content [channels](documentation/developers-and-admins/configuration/website-channel-management). For example, if you use your Xperience application to run two websites, send messages through an email channel, and deliver content to an external application through a headless channel, visitors and email recipients from all channels will belong to the same pool of contacts.
**Default cookie level or consent requirements**
The contact tracking functionality in Xperience by Kentico only works for website channels with the [Default cookie level](documentation/developers-and-admins/data-protection/cookies) set to _Visitor_ or _All_ , or for visitors who give tracking consent and increase their cookie level. For more information, see [Consent development](documentation/developers-and-admins/data-protection/consent-development).
## Manage contacts
Use the **Contact management** application in the Xperience administration to view and manage your contacts. A list of contacts is available on the **Contacts** tab.
[![Viewing the list of contacts in the Contact management application](docsassets/documentation/contact-management/Contact_List.png)](https://docs.kentico.com/docsassets/documentation/contact-management/Contact_List.png)
Information about contacts is stored in attributes, such as the name, email address, contact details, physical address, etc. To see an overview of all available information about a contact, select the contact in the list. This opens the contact’s profile.
[![Viewing a contact’s profile](docsassets/documentation/contact-management/ContactProfileWithAutomation.png)](https://docs.kentico.com/docsassets/documentation/contact-management/ContactProfileWithAutomation.png)
Marketers do not need to manually enter the attribute values for contacts. The system automatically collects and updates the data of each contact based on the information provided by the corresponding visitor. However, you can edit the attributes of contacts if required:
  1. Select the contact in the list.
  2. In the **Personal details** section of the contact profile, select **Edit**.
  3. Enter the required values.
  4. Select **Save**.


### Delete contacts
You can manually remove contacts by selecting the **Delete** (
The system can also be configured to [automatically delete contacts](documentation/developers-and-admins/digital-marketing-setup/contact-configuration/delete-inactive-contacts) who have not performed any activities over a specified number of days.
**Consequences of deleting contacts**
Deleting contacts also removes any associated data, notably [consent agreements](documentation/developers-and-admins/data-protection/consent-management), logged [activities](documentation/business-users/digital-marketing/contact-activities) and [customer journey](documentation/business-users/digital-marketing/customer-journeys) conversions.
Only delete contacts if you are sure this data will not be needed in the future. Consent agreements in particular may be required to maintain compliance with data protection laws.
## Contact merging
In some cases, multiple contacts in your system may actually represent a single real‑world person. Automatic merging gets rid of duplicates by combining several contacts into a single one.
The system automatically merges contacts that clearly represent the same person. This means that the system always **merges contacts with the same email address**.
Automatic merging can occur when new contacts are created or when the data of existing contacts is updated. The merging is only triggered by live site actions such as a form submissions. Creating contacts or editing them via the administration or the API **does not trigger** automatic merging.
After the system merges contacts, the result is a single contact referred to as a _parent_.
### Automatic merge collisions
When contact attribute collisions occur during automatic merges, the system uses the values of the child contact. If a collision is detected between the attributes of child contacts, and the parent contact does not contain a value in the given field, the priority of the values is undetermined – the system uses the values of the first contact loaded from the database.
### Contact email collisions
Because a contact’s email address is its main identifier in Xperience, the system does not allow the email to be changed based on data submitted by users. This can happen in the following ways:
  * [Form submissions](documentation/business-users/digital-marketing/forms/create-and-edit-forms#map-form-submission-to-contact-attributes)
  * [Headless tracking Data input activities](documentation/developers-and-admins/digital-marketing-setup/headless-tracking#log-activities)
  * [Cross-site tracking Data input activities](documentation/developers-and-admins/digital-marketing-setup/cross-site-tracking#data-input)


If the contact associated with a user already has an email address stored in Xperience, and it doesn’t match the newly submitted email value, all related activities and field updates are performed for a different contact. Either an existing contact that matches the submitted email value is used, or a new contact is created. In these cases, the user’s associated contact remains unchanged, but any subsequent actions, such as [automation processes](documentation/business-users/digital-marketing/automation), are performed for the “other” contact that matches the submitted email address.