---
source: https://docs.kentico.com/documentation/developers-and-admins/digital-marketing-setup/email-channel-management/manage-multiple-email-channels
scrape_date: 2026-01-26
---

  * [Home](/documentation)
  * [Developers and admins](/documentation/developers-and-admins)
  * [Digital marketing setup](/documentation/developers-and-admins/digital-marketing-setup)
  * [Email channel management](/documentation/developers-and-admins/digital-marketing-setup/email-channel-management)
  * Manage multiple email channels 


# Manage multiple email channels
Having multiple email channels within a single Xperience by Kentico application provides flexibility in how you communicate with your audiences. You can use different sending domains for each channel, which makes it easier to tailor communication to specific regions, brands, or customer segments. Multiple channels also let you distribute different language variants of emails, which a single channel doesn’t support.
This page provides best practices for **organizing the code of multiple email channels within one Xperience project**.
The required level of code separation depends on the [content model](/guides/architecture/content-modeling/content-modeling-guide) each channel uses. In most cases, you’ll want to treat them as independent, with separate [content types](/documentation/developers-and-admins/development/content-types) and [Email Builder components](/documentation/developers-and-admins/development/builders/email-builder/develop-email-builder-components) for each channel. However, if your email channels share an identical content model, you can often treat them as a single channel and reuse your code without any issues.
To implement multiple email channels:
  1. [Create and configure your email channels](/documentation/developers-and-admins/digital-marketing-setup/email-channel-management) in the administration interface.
  2. Organize the project structure and content types according to the recommendations below.


## Content types
Use a unique namespace in the code name of [content types](/documentation/developers-and-admins/development/content-types) related to a specific email channel. This prevents conflicts when implementing various features related to content types.
### Generated email model classes
For smaller projects, you may store your [generated email model classes](/documentation/developers-and-admins/api/generate-code-files-for-system-objects) directly in the main Xperience project.
For larger solutions, where Email Builder components may be placed in dedicated [Razor class libraries](https://learn.microsoft.com/en-us/aspnet/core/razor-pages/ui-class) (RCLs), the model classes must also be available to those RCLs. In this case, we recommend storing your generated email classes in a dedicated _Class library_ project for all generated model classes and reference it from the main Xperience project as well as from any Email Builder RCLs.
## Email Builder
Use [Razor class libraries](https://learn.microsoft.com/en-us/aspnet/core/razor-pages/ui-class) (RCLs) to organize the code of your [Email Builder](/documentation/developers-and-admins/development/builders/email-builder) components if you plan to create a large number of Email Builder components. For smaller projects you can store your components directly within the main Xperience project. Your components can be shared depending on your implementation.
We recommend structuring your solution with separate projects for each channel, as well as an optional shared library for components shared across your channels:
  * **AcmeEmail** – RCL for the _Acme_ email channel
  * **DancingGoatEmail** – RCL for the _Dancing Goat_ email channel
  * **Shared** – optional RCL for [Email Builder components](/documentation/developers-and-admins/development/builders/email-builder/develop-email-builder-components) common to multiple channels
  * **Xperience** – the main web application project containing startup code, system data, and configuration. For example: 
    * Startup and general system configuration (could be based on the Xperience _Boilerplate_ [project template](/documentation/developers-and-admins/installation))
    * Binary data of [content item assets](/documentation/business-users/content-hub/content-item-assets)


### Widgets and sections
  * Store the code of general components in a shared location. Components specific to an email channel can be stored in a corresponding RCL.
  * Restrict widgets and sections using the `AllowedWidgets` and `AllowedSections` properties of the `EditableArea` razor component used to [define editable areas](/documentation/developers-and-admins/development/builders/email-builder/develop-email-builder-components#editable-areas) in email templates.


### Email templates
  * Store [email templates](/documentation/developers-and-admins/development/builders/email-builder/develop-email-builder-components#templates) for each email channel in their corresponding RCL, e.g., _< AcmeEmail project>/EmailTemplates_.
  * Scope email templates to certain content types using the `ContentTypeNames` parameter of the [registration attribute](/documentation/developers-and-admins/development/builders/email-builder/develop-email-builder-components#register-templates).


![]()
[]()[]()
