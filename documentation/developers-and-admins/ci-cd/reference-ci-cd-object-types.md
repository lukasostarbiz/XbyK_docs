# Reference - CI/CD object types
  * [ Copy page link ](documentation/developers-and-admins/ci-cd/reference-ci-cd-object-types#) | [Get HelpService ID](documentation/developers-and-admins/ci-cd/reference-ci-cd-object-types#)
Core MVC 5


[✖](documentation/developers-and-admins/ci-cd/reference-ci-cd-object-types# "Close page link panel") [Copy to clipboard](documentation/developers-and-admins/ci-cd/reference-ci-cd-object-types#)
The [Continuous Integration](documentation/developers-and-admins/ci-cd/continuous-integration) and [Continuous Deployment](documentation/developers-and-admins/ci-cd/continuous-deployment) features support the following types of Xperience objects:
  * [General](documentation/developers-and-admins/ci-cd/reference-ci-cd-object-types#general)
  * [Content management](documentation/developers-and-admins/ci-cd/reference-ci-cd-object-types#content-management)
  * [Digital marketing](documentation/developers-and-admins/ci-cd/reference-ci-cd-object-types#digital-marketing)
  * [Digital commerce](documentation/developers-and-admins/ci-cd/reference-ci-cd-object-types#digital-commerce)
  * [Binding object types](documentation/developers-and-admins/ci-cd/reference-ci-cd-object-types#binding-object-types)


Related pages
  * [Continuous Integration](documentation/developers-and-admins/ci-cd/continuous-integration)
  * [Continuous Deployment](documentation/developers-and-admins/ci-cd/continuous-deployment)
  * [CI/CD repository structure](documentation/developers-and-admins/ci-cd/ci-cd-repository-structure)
  * [Exclude objects from CI/CD](documentation/developers-and-admins/ci-cd/configure-ci-cd-repositories)


## General
Object type |  CI/CD folder name |  Notes  
---|---|---  
Alternative forms |  cms.alternativeform |  Used by the Xperience administration. Contains definitions of UI forms used to provide editing interfaces for [object types](documentation/developers-and-admins/customization/object-types). Child object type of **Module class** objects.  
[Channels](documentation/developers-and-admins/configuration/website-channel-management) |  cms.channel |  A medium for delivering content via a specific method (website, email, social networks).  
[Consents](documentation/developers-and-admins/data-protection/consent-management) |  cms.consent |  Includes consent definitions, with all consent texts. CI/CD does not include archived consents or consent agreements given by contacts.  
[Languages](documentation/developers-and-admins/configuration/languages) |  cms.contentlanguage |   
Modules  |  cms.resource |  Contains definitions for system and [custom modules](documentation/developers-and-admins/customization/object-types#define-the-data-class). Direct parent of module class (cms.class) objects. Supported child objects:
  * Module classes (cms.class)

  
Module classes |  cms.class |  Includes the definitions and settings of module classes (object types). Used internally by the Xperience administration and data structure. Supported child objects:
  * Alternative forms (cms.alternativeform)

  
Module classes - Customizable |  cms.systemtable |  Includes the definitions and settings of certain module classes (object types). Used internally by the Xperience administration and data structure. Supported child objects:
  * Alternative forms (cms.alternativeform)
  * Queries (cms.query)

  
[Notification emails](documentation/developers-and-admins/configuration/notifications) |  cms.notificationemail |   
[Notification email templates](documentation/developers-and-admins/configuration/notifications#notification-email-templates) |  cms.notificationemailtemplate |   
Queries |  cms.query |  Used internally by the system. Child object type of **Module class** objects.  
[Roles](documentation/developers-and-admins/configuration/users/role-management) |  cms.role |   
[Scheduled task](documentation/developers-and-admins/customization/scheduled-tasks) configurations |  cms.scheduledtaskconfiguration |  Information about scheduled task execution intervals and other metadata.  
Setting categories and groups |  cms.settingscategory |  The _cms.settingscategory_ object type includes both setting categories and setting groups.  
[Settings](documentation/developers-and-admins/configuration/settings) |  cms.settingskey |  Settings are supported, but **_excluded by default_** in the [repository.config file](documentation/developers-and-admins/ci-cd/configure-ci-cd-repositories). Settings may contain sensitive data – only remove the exclusion if you agree to make setting values available within the file system used by your application and any connected source control systems. You can configure the _repository.config_ file to include settings in general, but exclude individual setting keys that you consider sensitive. **Important:** The **License key** (_Settings_ -> _System_ -> _Licenses_) setting that stores the [license key](documentation/developers-and-admins/installation/licenses#license-keys) used to activate the instance is excluded permanently regardless of your _repository.config_ configuration.  
[Users](documentation/developers-and-admins/configuration/users/user-management) |  cms.user |  User accounts for the administration interface.  
[Workspaces](documentation/developers-and-admins/configuration/users/role-management/workspaces) |  cms.workspace |  When using CI/CD to add a new workspace, your [repository configuration](documentation/developers-and-admins/ci-cd/configure-ci-cd-repositories) must also include the _cms.contentfolder_ object type. Each workspace requires a root content folder to ensure that [Content hub](documentation/business-users/content-hub) works correctly.  
## Content management
Object type |  CI/CD folder name |  Notes  
---|---|---  
[Content types](documentation/developers-and-admins/development/content-types) |  cms.contenttype |  Stores content type definitions.  
[Content items](documentation/business-users/content-hub) |  contentitemdata.*  
cms.contentitem  
cms.contentitemcommondata  
cms.contentitemlanguagemetadata |  The system uses multiple files to store content item data. See the **Content items** section of [CI/CD repository structure](documentation/developers-and-admins/ci-cd/ci-cd-repository-structure#content-items) for more information. **Important:** To include or exclude content items from the repository, use the `IncludedContentItemsOfType` or `ExcludedContentItemsOfType` [filters](documentation/developers-and-admins/ci-cd/configure-ci-cd-repositories#includeexclude-content-items). ([Available from Xperience 30.4.0](documentation/developers-and-admins/ci-cd/configure-ci-cd-repositories/config-v2-migration))