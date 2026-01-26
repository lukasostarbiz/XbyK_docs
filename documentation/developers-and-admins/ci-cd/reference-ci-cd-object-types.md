---
source: https://docs.kentico.com/documentation/developers-and-admins/ci-cd/reference-ci-cd-object-types
scrape_date: 2026-01-26
---

  * [Home](/documentation)
  * [Developers and admins](/documentation/developers-and-admins)
  * [CI/CD](/documentation/developers-and-admins/ci-cd)
  * Reference - CI/CD object types 


# Reference - CI/CD object types
The [Continuous Integration](/documentation/developers-and-admins/ci-cd/continuous-integration) and [Continuous Deployment](/documentation/developers-and-admins/ci-cd/continuous-deployment) features support the following types of Xperience objects:
  * [General](#general)
  * [Content management](#content-management)
  * [Digital marketing](#digital-marketing)
  * [Digital commerce](#digital-commerce)
  * [Binding object types](#binding-object-types)


Related pages
  * [Continuous Integration](/documentation/developers-and-admins/ci-cd/continuous-integration)
  * [Continuous Deployment](/documentation/developers-and-admins/ci-cd/continuous-deployment)
  * [CI/CD repository structure](/documentation/developers-and-admins/ci-cd/ci-cd-repository-structure)
  * [Exclude objects from CI/CD](/documentation/developers-and-admins/ci-cd/configure-ci-cd-repositories)


## General
Object type |  CI/CD folder name |  Notes  
---|---|---  
Alternative forms |  cms.alternativeform |  Used by the Xperience administration. Contains definitions of UI forms used to provide editing interfaces for [object types](/documentation/developers-and-admins/customization/object-types). Child object type of **Module class** objects.  
[Channels](/documentation/developers-and-admins/configuration/website-channel-management) |  cms.channel |  A medium for delivering content via a specific method (website, email, social networks).  
[Consents](/documentation/developers-and-admins/data-protection/consent-management) |  cms.consent |  Includes consent definitions, with all consent texts. CI/CD does not include archived consents or consent agreements given by contacts.  
[Languages](/documentation/developers-and-admins/configuration/languages) |  cms.contentlanguage |   
Modules  |  cms.resource |  Contains definitions for system and [custom modules](/documentation/developers-and-admins/customization/object-types#define-the-data-class). Direct parent of module class (cms.class) objects. Supported child objects:
  * Module classes (cms.class)

  
Module classes |  cms.class |  Includes the definitions and settings of module classes (object types). Used internally by the Xperience administration and data structure. Supported child objects:
  * Alternative forms (cms.alternativeform)

  
Module classes - Customizable |  cms.systemtable |  Includes the definitions and settings of certain module classes (object types). Used internally by the Xperience administration and data structure. Supported child objects:
  * Alternative forms (cms.alternativeform)
  * Queries (cms.query)

  
[Notification emails](/documentation/developers-and-admins/configuration/notifications) |  cms.notificationemail |   
[Notification email templates](/documentation/developers-and-admins/configuration/notifications#notification-email-templates) |  cms.notificationemailtemplate |   
Queries |  cms.query |  Used internally by the system. Child object type of **Module class** objects.  
[Roles](/documentation/developers-and-admins/configuration/users/role-management) |  cms.role |   
[Scheduled task](/documentation/developers-and-admins/customization/scheduled-tasks) configurations |  cms.scheduledtaskconfiguration |  Information about scheduled task execution intervals and other metadata.  
Setting categories and groups |  cms.settingscategory |  The _cms.settingscategory_ object type includes both setting categories and setting groups.  
[Settings](/documentation/developers-and-admins/configuration/settings) |  cms.settingskey |  Settings are supported, but **_excluded by default_** in the [repository.config file](/documentation/developers-and-admins/ci-cd/configure-ci-cd-repositories). Settings may contain sensitive data – only remove the exclusion if you agree to make setting values available within the file system used by your application and any connected source control systems. You can configure the _repository.config_ file to include settings in general, but exclude individual setting keys that you consider sensitive. **Important:** The **License key** (_Settings_ -> _System_ -> _Licenses_) setting that stores the [license key](/documentation/developers-and-admins/installation/licenses#license-keys) used to activate the instance is excluded permanently regardless of your _repository.config_ configuration.  
[Users](/documentation/developers-and-admins/configuration/users/user-management) |  cms.user |  User accounts for the administration interface.  
[Workspaces](/documentation/developers-and-admins/configuration/users/role-management/workspaces) |  cms.workspace |  When using CI/CD to add a new workspace, your [repository configuration](/documentation/developers-and-admins/ci-cd/configure-ci-cd-repositories) must also include the _cms.contentfolder_ object type. Each workspace requires a root content folder to ensure that [Content hub](/documentation/business-users/content-hub) works correctly.  
## Content management
Object type |  CI/CD folder name |  Notes  
---|---|---  
[Content types](/documentation/developers-and-admins/development/content-types) |  cms.contenttype |  Stores content type definitions.  
[Content items](/documentation/business-users/content-hub) |  contentitemdata.*  
cms.contentitem  
cms.contentitemcommondata  
cms.contentitemlanguagemetadata |  The system uses multiple files to store content item data. See the **Content items** section of [CI/CD repository structure](/documentation/developers-and-admins/ci-cd/ci-cd-repository-structure#content-items) for more information. **Important:** To include or exclude content items from the repository, use the `IncludedContentItemsOfType` or `ExcludedContentItemsOfType` [filters](/documentation/developers-and-admins/ci-cd/configure-ci-cd-repositories#includeexclude-content-items). ([Available from Xperience 30.4.0](/documentation/developers-and-admins/ci-cd/configure-ci-cd-repositories/config-v2-migration))
* * *
CI/CD data does **not include** changes made to new versions of previously published content items. While an item is in the _Draft (New version)_ status (or a custom [workflow step](/documentation/developers-and-admins/configuration/workflows)), CI/CD stores the original published version, until the new changes are published. Consequently, the **published** version of content items is always restored by CI/CD – any changes made in _Draft (New version)_ or custom workflow steps are **lost** when the item is restored.
* * *
**Content item asset fields** Binary data managed by [fields](/documentation/developers-and-admins/customization/field-editor) of the _Content item asset_ data type is serialized into the repository together with the source content item objects – on instances using CI/CD, the binary data exists in two copies: one in the _~/assets_ folder and the second in the repository. **Note** : Changing the data type of _Content item asset_ fields or removing these fields entirely will result in abandoned binary files in the repository. To remove these unwanted files, serialize all objects again, which recreates the repository without the orphaned files.  
[Content folders](/documentation/business-users/content-hub/content-hub-folders#content-folders) |  cms.contentfolder |  Stores folders used to organize items in the _Content hub_ application. Required for all projects to ensure that the content hub works correctly. The CI/CD files storing content hub folder data are organized into file system subfolders named according to each content hub folder’s parent. The hierarchy starts from a “root” system folder. [Smart folders](/documentation/business-users/content-hub/content-hub-folders#smart-folders) are stored separately as _cms.smartfolder_ objects.  
[Headless channel configuration](/documentation/developers-and-admins/configuration/headless-channel-management) |  cms.headlesschannel |  Child object type of **Channel** objects.  
[Headless items](/documentation/business-users/headless-content) |  contentitemdata.*  
cms.contentitem  
cms.contentitemcommondata  
cms.contentitemlanguagemetadata  
cms.headlessitem |  The system uses multiple files to store headless item data. See the **Headless items** section of [CI/CD repository structure](/documentation/developers-and-admins/ci-cd/ci-cd-repository-structure#headless-items) for more information. **Important:** To include or exclude headless items from the repository, use the `IncludedContentItemsOfType` or `ExcludedContentItemsOfType` [filters](/documentation/developers-and-admins/ci-cd/configure-ci-cd-repositories#includeexclude-content-items). ([Available from Xperience 30.4.0](/documentation/developers-and-admins/ci-cd/configure-ci-cd-repositories/config-v2-migration))
* * *
CI/CD data does **not include** changes made to new versions of previously published headless items. While an item is in the _Draft (New version)_ status (or a custom [workflow step](/documentation/developers-and-admins/configuration/workflows)), CI/CD stores the original published version, until the new changes are published. Consequently, the **published** version of headless items is always restored by CI/CD – any changes made in _Draft (New version)_ or custom workflow steps are **lost** when the item is restored.  
[Media library files](/documentation/business-users/media-libraries/manage-media-files) |  media.file |  Child object type of **Media library** objects. Only includes media file definitions, not the binary data of individual media files. Binary data is stored on the file system in the media library folders.  
[Pages](/documentation/business-users/website-content) |  contentitemdata.*  
cms.contentitem  
cms.contentitemcommondata  
cms.contentitemlanguagemetadata  
cms.webpageitem |  The system uses multiple files to store page data. See the **Pages** section of [CI/CD repository structure](/documentation/developers-and-admins/ci-cd/ci-cd-repository-structure#pages) for more information. **Important:** To include or exclude pages from the repository, use the `IncludedContentItemsOfType` or `ExcludedContentItemsOfType` [filters](/documentation/developers-and-admins/ci-cd/configure-ci-cd-repositories#includeexclude-content-items). ([Available from Xperience 30.4.0](/documentation/developers-and-admins/ci-cd/configure-ci-cd-repositories/config-v2-migration))
* * *
CI/CD data does **not include** changes made to new versions of previously published pages. While a page is in the _Draft (New version)_ status (or a custom [workflow step](/documentation/developers-and-admins/configuration/workflows)), CI/CD stores the original published version, until the new changes are published. Consequently, the **published** version of pages is always restored by CI/CD – any changes made in _Draft (New version)_ or custom workflow steps are **lost** when the page is restored. CI/CD data also does **not include** changes made to [page permissions](/documentation/developers-and-admins/configuration/users/role-management/page-permission-management). After performing the CD restore operation, the moved and newly created pages inherit permissions from their new parent page.
* * *
Supported child objects:
  * [Page former URL paths](/documentation/business-users/website-content/manage-page-urls#former-urls) (cms.webpageformerurlpath)
  * [Page URL paths](/documentation/business-users/website-content/manage-page-urls) (cms.webpageurlpath)

  
[Page former URL paths](/documentation/business-users/website-content/manage-page-urls#former-urls) |  cms.webpageformerurlpath |  Child object type of **Page** objects.  
[Page URL paths](/documentation/business-users/website-content/manage-page-urls) |  cms.webpageurlpath |  Child object type of **Page** objects.  
Web page folders |  ##WebPageFolders## |  **Important:** Dedicated mechanism for including/excluding website channel folders was introduced starting with [v2 configuration files](/documentation/developers-and-admins/ci-cd/configure-ci-cd-repositories/config-v2-migration) available from version 30.4.0. Website channel folders that are used to group pages in the content tree. They are represented as regular content items but have no backing content type structure. To track these folders in CI/CD repositories, use the _##WebPageFolders##_ placeholder instead of a regular content type inside the `<IncludedContentItemsOfType>` or `<ExcludedContentItemsOfType>` elements. XML **Track website channel folders** Copy ```
<IncludedContentItemsOfType>
  <ContentType>
    ##WebPageFolders##
  </ContentType>
</IncludedContentItemsOfType>
```
  
[Preset page templates](/documentation/developers-and-admins/development/builders/page-builder/page-templates-for-page-builder) |  cms.pagetemplateconfiguration |  The Page Builder configuration of a saved preset page template created using a default page template.  
[Reusable field schemas](/documentation/developers-and-admins/development/content-types/reusable-field-schemas) |  cms.class |  Reusable field schema definitions and data are stored as part of the _cms.contentitemcommondata_ object type. If your [content types](#cicd_contenttypes) use reusable field schemas and you want to track changes to their structure and data, you must serialize _cms.contentitemcommondata_ into your repository. This is particularly relevant for [SaaS](/documentation/developers-and-admins/saas) projects that need to explicitly opt-in for each CI/CD object. Tracking reusable field schemas in CI/CD requires the following includes in _repository.config_. XML **Add reusable field schema definitions to CI/CD** Copy ```
<IncludedObjectTypes>
    <ObjectType>cms.class</ObjectType>
</IncludedObjectTypes>

<ObjectFilters>
    <!-- Contains reusable field schema definitions -->
    <IncludedCodeNames ObjectType="cms.class">
        CMS.ContentItemCommonData
    </IncludedCodeNames>
</ObjectFilters>
```
**cms.resource filtering** The [cms.resource](#cms-resource) object type is a direct parent of all _cms.class_ object types. If your _repository.config_ [includes or excludes](/documentation/developers-and-admins/ci-cd/configure-ci-cd-repositories) a subset of _cms.resource_ objects, you need to explicitly include the **CMS.ContentEngine** resource to enable _cms.contentitemcommondata_ serialization. XML **cms.resource allowlist** Copy ```
<IncludedCodeNames ObjectType="cms.resource">
        CMS.ContentEngine
</IncludedCodeNames>
```
  
[Smart folders](/documentation/business-users/content-hub/content-hub-folders#smart-folders) |  cms.smartfolder |  Stores smart folders, which are used to dynamically categorize and filter items in the _Content hub_ application. The [content folder](/documentation/business-users/content-hub/content-hub-folders#content-folders) hierarchy in the _Content hub_ application is stored separately using _cms.contentfolder_ objects.  
[Taxonomies](/documentation/developers-and-admins/configuration/taxonomies) |  cms.taxonomy |   
[Tags](/documentation/developers-and-admins/configuration/taxonomies) |  cms.tag |  Tags that belong under another tag in the taxonomy tree are stored as child objects in the CI/CD repository. CI/CD files representing child tags are placed under subfolders named according to the parent tag.  
[Web page scope](/documentation/developers-and-admins/development/content-types/limit-the-pages-users-can-create) |  cms.webpagescope |  Child object type of **Website channel configuration** objects.  
[Website channel configuration](/documentation/developers-and-admins/configuration/website-channel-management) |  cms.websitechannel |  Child object type of **Channel** objects.  
[Workflows](/documentation/developers-and-admins/configuration/workflows) |  cms.contentworkflow |  Supported child objects:
  * Workflow step (cms.contentworkflowstep)

  
Workflow step |  cms.contentworkflowstep |  Child object type of **Workflow** (cms.contentworkflow) objects.  
## Digital marketing
Object type |  CI/CD folder name |  Notes  
---|---|---  
[Activity types](/documentation/developers-and-admins/digital-marketing-setup/set-up-activities) |  om.activitytype |   
[Automation processes](/documentation/business-users/digital-marketing/automation) |  ma.automationprocess |  Stores automation processes. CI/CD data does **not include** information about contacts passing through automation processes, or the contact history and other statistics of automation steps. Also required to ensure the functionality of all types of [form autoresponders](/documentation/business-users/digital-marketing/forms/create-and-edit-forms#actions-after-form-submission), which internally use automation. Note: If you delete or significantly restructure the steps in a process, and then CI/CD restore these changes on an instance where the process is already enabled or has contact history, you may break the process or cause inconsistencies. This scenario is **not supported**. Supported child objects:
  * Automation steps (ma.automationstep)
  * Automation triggers (cms.objectworkflowtrigger)

  
Automation actions |  ma.automationaction |  Stores data representing the step types that perform specific actions within automation processes (e.g., “Send email”). Also required to ensure the functionality of all types of [form autoresponders](/documentation/business-users/digital-marketing/forms/create-and-edit-forms#actions-after-form-submission), which internally use automation. Required for all projects to ensure system functionality that internally uses automation processes.  
Automation steps |  ma.automationstep |  Stores steps within automation processes (Automation Builder). CI/CD data does **not include** information about contacts passing through automation processes, or the contact history and other statistics of automation steps. Also required to ensure the functionality of all types of [form autoresponders](/documentation/business-users/digital-marketing/forms/create-and-edit-forms#actions-after-form-submission), which internally use automation. Note: If you delete or significantly restructure the steps in a process, and then CI/CD restore these changes on an instance where the process is already enabled or has contact history, you may break the process or cause inconsistencies. This scenario is **not supported**. Child object type of **Automation process** objects.  
Automation triggers |  cms.objectworkflowtrigger |  Stores the triggers of automation processes. Also required to ensure the functionality of all types of [form autoresponders](/documentation/business-users/digital-marketing/forms/create-and-edit-forms#actions-after-form-submission), which internally use automation. Child object type of **Automation process** objects.  
[Contact groups](/documentation/business-users/digital-marketing/contact-groups) |  om.contactgroup |  Only includes the definitions of contact groups, without information about the contacts assigned to the groups. Additionally, the CI/CD data does not contain information about scheduled rebuilds for contact groups. After restoring a new condition-based contact group from the repository folder, you need to manually rebuild the group in the _Contact groups_ application.  
Countries |  cms.country |  Supported child objects:
  * States (cms.state)

  
[Customer journeys](/documentation/business-users/digital-marketing/customer-journeys) |  om.customerjourney om.customerjourneyhistory |  Only includes the definitions of customer journeys, without statistics calculated for contacts. When restoring a customer journey from the repository folder, make sure to include module classes (_cms.class_). After the restoration, you need to manually [recalculate](/documentation/business-users/digital-marketing/customer-journeys#customer-journey-results) the journey in the Customer journeys application. Supported child objects:
  * Customer journey stages (om.customerjourneystage)

  
Customer journey stages |  om.customerjourneystage |  Child object type of **Customer journey** objects.  
[Email channel configuration](/documentation/developers-and-admins/digital-marketing-setup/email-channel-management) |  emaillibrary.emailchannel |  Supported child objects:
  * Email channel sender address (emaillibrary.emailchannelsender)

  
Email channel sender addresses |  emaillibrary.emailchannelsender |  Child object type of **Email channel configuration** objects.  
[Emails](/documentation/business-users/digital-marketing/emails) |  contentitemdata.*  
cms.contentitem  
cms.contentitemcommondata  
cms.contentitemlanguagemetadata  
emaillibrary.emailconfiguration |  The system uses multiple files to store email data. See the **Emails** section of [CI/CD repository structure](/documentation/developers-and-admins/ci-cd/ci-cd-repository-structure#emails) for more information. Supported child objects:
  * Regular email sendout configuration (emaillibrary.sendconfiguration)

**Important:** To include or exclude emails from the repository, use the `IncludedContentItemsOfType` or `ExcludedContentItemsOfType` [filters](/documentation/developers-and-admins/ci-cd/configure-ci-cd-repositories#includeexclude-content-items). ([Available from Xperience 30.4.0](/documentation/developers-and-admins/ci-cd/configure-ci-cd-repositories/config-v2-migration))
* * *
CI/CD data does **not include** changes made to new versions of previously published emails. While an email is in the _Draft (New version)_ status (or a custom [workflow step](/documentation/developers-and-admins/configuration/workflows)), CI/CD stores the original published version, until the new changes are published. Consequently, the **published** version of emails is always restored by CI/CD – any changes made in _Draft (New version)_ or custom workflow steps are **lost** when the email is restored. Does not include [statistics](/documentation/business-users/digital-marketing/emails/track-email-statistics) tracked for emails.  
[Email templates](/documentation/business-users/digital-marketing/emails) |  emaillibrary.emailtemplate |   
[Forms](/documentation/business-users/digital-marketing/forms) |  cms.form  
cms.formclass |  Only includes form definitions and settings, not the data records stored by individual forms. The system uses multiple object types to store the definitions and settings of forms. See the **Forms** section in [CI/CD repository structure](/documentation/developers-and-admins/ci-cd/ci-cd-repository-structure) for more information.  
Form featured fields |  cms.formfeaturedfield |  Stores the definitions of fields displayed in the **Featured** category when [adding fields to forms](/documentation/business-users/digital-marketing/forms/create-and-edit-forms).  
Macro rules |  cms.macrorule |  Used internally for the condition builder in [contact group](/documentation/business-users/digital-marketing/contact-groups) and [automation processes](/documentation/business-users/digital-marketing/automation).  
Macro rule categories |  cms.macrorulecategory |  Used internally for the condition builder in [contact group](/documentation/business-users/digital-marketing/contact-groups) and [automation processes](/documentation/business-users/digital-marketing/automation).  
[Preset email templates](/documentation/business-users/digital-marketing/emails/create-emails-in-email-builder/email-builder-templates) |  emaillibrary.emailpresettemplate |  The Email Builder configuration of a saved preset email template created using a default email template.  
[Recipient lists](/documentation/business-users/digital-marketing/emails/send-regular-emails-to-subscribers) |  om.recipientlist |  Stores recipients lists, which contain contacts who opt in to receive regular emails. Only includes the recipient list definition, without information about the contained recipients. Supported child objects:
  * Recipient list settings (emaillibrary.recipientlistsettings)

  
Recipient list settings |  emaillibrary.recipientlistsettings |  Stores the approval and unsubscribe settings of recipient lists. Child object type of **Recipient list** objects.  
[Regular email sendout configuration](/documentation/business-users/digital-marketing/emails/send-regular-emails-to-subscribers) |  emaillibrary.sendconfiguration |  Child object type of **Email** objects (emaillibrary.emailconfiguration).  
States |  cms.state |  Child object type of **Country** objects.  
[Tracked websites](/documentation/developers-and-admins/digital-marketing-setup/cross-site-tracking) |  om.trackedwebsite |  Websites registered for the [Cross-site tracking](/documentation/developers-and-admins/digital-marketing-setup/cross-site-tracking) feature.  
## Digital commerce
Object type |  CI/CD folder name |  Notes  
---|---|---  
Order status |  commerce.orderstatus |  Represents [statuses](/documentation/developers-and-admins/digital-commerce-setup/configure-order-statuses) in the lifecycle of orders created by customers (_New_ , _Payment received_ , _Shipped_ , _Delivered_ , etc.).  
Shipping method |  commerce.shippingmethod |  Defines [shipping options](/documentation/developers-and-admins/digital-commerce-setup/shipping-and-payment-methods) available to customers during checkout.  
Payment method |  commerce.paymentmethod |  Defines [payment options](/documentation/developers-and-admins/digital-commerce-setup/shipping-and-payment-methods) available to customers during checkout.  
Promotion |  commerce.promotion |  Stores created [promotions](/documentation/developers-and-admins/digital-commerce-setup/promotions). |  commerce.promotioncoupon |  Stores [coupon codes](/documentation/developers-and-admins/digital-commerce-setup/promotions/coupon-codes) associated with created promotions.  
## Binding object types
In addition to standard object types, CI/CD also supports most related bindings (i.e. objects representing relationships between the supported objects and other objects).
Binding |  CI folder name |  Main object type (parent) |  Related object type  
---|---|---|---  
Allowed child content types |  cms.allowedchildcontenttype |  Content type |  Content type  
Application permissions |  cms.applicationpermission |  Role |  No regular object type. The binding connects roles to application identifiers (defined in code when registering an admin UI application) and permission identifiers (defined in code when specifying the set of permissions available for an application).  
Automation process step transitions |  cms.workflowtransition |  Automation step |  Automation step  
Allowed content types for channels |  cms.contenttypechannel |  Channel |  Content type  
Allowed email templates for content types |  emaillibrary.emailtemplatecontenttype |  Content type |  Email template  
Email sendout contact group configuration |  emaillibrary.sendconfigurationcontactgroup |  Regular email sendout configuration |  Contact group  
Macro rules assigned to categories |  cms.macrorulemacrorulecategory |  Macro rule category |  Macro rule  
Order status notification users |  commerce.orderstatusnotification |  Order status |  User  
Users in roles |  cms.userrole |  Role |  User  
Web page scope content type |  cms.webpagescopecontenttype |  Web page scope |  Content type  
Workflow content type |  cms.contentworkflowcontenttype |  Content workflow |  Content type  
Workflow roles with full control |  cms.contentworkflowrole |  Content workflow |  Role  
Workflow step roles |  cms.contentworkflowsteprole |  Content workflow step |  Role  
Workspace data permissions |  cms.workspacedatapermission |  Role |  No regular object type. This object type, for a given workspace, connects a role to application identifiers (which support being scoped under **Workspaces**) and workspace permission identifiers (defined in code when specifying the set of workspace permissions available for an application).  
![]()
[]()[]()
