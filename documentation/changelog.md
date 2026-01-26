---
source: https://docs.kentico.com/documentation/changelog
scrape_date: 2026-01-26
---

  * [Home](/documentation)
  * Changelog 


# Changelog
## Refresh (January 22, 2026)
**version 31.1.0**
### New features
**Visibility conditions for form fields**  
When creating forms in the Form Builder, editors can now control when and how fields are displayed by [adding visibility conditions](/documentation/business-users/digital-marketing/forms/create-and-edit-forms#field-visibility). The conditions are based on the value of another field in the form. For example, a “Company name” field can be configured to only appear when users select “Business” in a preceding “Account type” field. This helps keep forms clean and relevant for individual users.
The system provides a set of visibility conditions for common scenarios. Development of custom conditions is currently not supported.
Fields can also be configured as hidden, which allows them to be removed from the form while preserving the field’s data in existing form submissions. Hidden fields can also be used by developers to [set form values programmatically](/documentation/developers-and-admins/customization/handle-global-events/handle-form-events).
**Content writing assistance: Improved AIRA rich text editor refinements**  
[AIRA rich text editor refinements](/documentation/business-users/aira#rich-text-editor-refinements) are now available not only for [fields on the Content tab](/documentation/developers-and-admins/configuration/aira-configuration#add-aira-features-to-your-content-types), but also in rich text editors [within Page Builder and Email Builder](/documentation/developers-and-admins/configuration/aira-configuration#rich-text-refinements-in-page-builder-and-email-builder). When you apply predefined text refinement prompts, the text is now edited directly in the editor instead of in the AIRA chat.
**Coupon codes for digital commerce promotions**  
Digital commerce [promotions](/documentation/developers-and-admins/digital-commerce-setup/promotions) now support coupon code redemption. Users can configure promotions that require customers to enter a specific code during checkout to receive discounts. See [Coupon codes](/documentation/developers-and-admins/digital-commerce-setup/promotions/coupon-codes) for more information.
**Activity value-based conditions for automation**  
A new condition type is now available in automation [condition steps](/documentation/business-users/digital-marketing/automation#conditions), which evaluates whether an activity was logged with a specific value. This allows marketers to build more sophisticated conditions, particularly when working with custom activities.
### Updates and changes
**New retrieve content by GUIDs content retriever methods**  
Developers can now retrieve pages and content items by their GUID identifiers when working with multiple content types or reusable schemas. Three new methods are available for the [content retriever API](/documentation/developers-and-admins/api/content-item-api/content-retriever-api):
  * `RetrieveAllPagesByGuids` – retrieves pages of any content type by their `WebPageItemGUID` values
  * `RetrieveContentOfContentTypesByGuids` – retrieves content items of specific content types by their `ContentItemGUID` values
  * `RetrieveContentOfReusableSchemasByGuids` – retrieves content items using reusable field schemas by their `ContentItemGUID` values


This is particularly useful when working with GUIDs obtained from selector components, such as page selectors or combined content selectors. See the [content retriever API reference](/documentation/developers-and-admins/api/content-item-api/reference-content-retriever-api) for more information.
**Froala editor update**  
The [Froala WYSIWYG editor](https://froala.com/wysiwyg-editor) that provides the [Rich text editor](/documentation/business-users/rich-text-editor) in Xperience was updated to version 5.0.0. See [Froala Editor 5.0.0](https://froala.com/blog/editor/new-releases/froala-v5-0-0-code-snippets-word-import/) for details.
**Rich text editor configuration update**  
All [rich text editor configurations](/documentation/developers-and-admins/configuration/rich-text-editor-configuration/rich-text-editor-customization#replace-the-default-editor-configuration) provided by the system were updated, so that the toolbar in these configurations now includes the _Undo_ and _Redo_ buttons.
**Empty visibility conditions for selector content type fields**  
The [field editor](/documentation/developers-and-admins/customization/field-editor) for [content types](/documentation/developers-and-admins/development/content-types) and [reusable field schemas](/documentation/developers-and-admins/development/content-types/reusable-field-schemas) now supports the _Empty_ visibility condition for selector fields. This includes fields with data types such as _Pages and reusable content_ or _Taxonomy_. Manual registration of `IsEmptyVisibilityCondition` for the given data types is no longer required.
**Database changes**  
The `CMS_AutomationTemplate` database table was removed. This table is not used by the system and was not intended to store custom data.
**Replacement of disabled mode on the Content tab**  
When a piece of content (content items, website channel pages, emails, and headless items) is not being edited, its fields on the _Content_ tab are now displayed in read-only mode instead of disabled mode. This update improves readability and allows editors to copy text without having to start editing the content.
### Removed object types for CI/CD
The update removes the `ma.automationtemplate` object type from the system. This is a legacy object type that was not used, but could potentially be present in the [repository.config](/documentation/developers-and-admins/ci-cd/configure-ci-cd-repositories) files of your CI/CD repositories. After upgrading, you need to remove any configuration related to this object type from your _repository.config_ files, otherwise you will encounter the following error when storing or restoring data: “System.InvalidOperationException: Object type ‘ma.automationtemplate’ not found”
Note: Check and update the Continuous Deployment repository.config used for [SaaS projects](/documentation/developers-and-admins/deployment/deploy-to-the-saas-environment#prepare-a-project-for-deployment-to-the-saas-environment). Projects created using installation templates prior to version 31.1.0 include this object type in their initial CD repository.config.
### New object types for CI/CD
The update introduces the following object types, which are supported by the [Continuous Integration](/documentation/developers-and-admins/ci-cd/continuous-integration) and [Continuous Deployment](/documentation/developers-and-admins/ci-cd/continuous-deployment) features. Consider updating the [repository.config](/documentation/developers-and-admins/ci-cd/configure-ci-cd-repositories) files of your CI/CD repositories, particularly when using the `<IncludedObjectTypes>` allowlist for object type filtering.
  * commerce.promotioncoupon – stores [coupon codes](/documentation/developers-and-admins/digital-commerce-setup/promotions/coupon-codes) associated with created promotions.


### Newly obsolete API
**Html.Kentico().PageBuilderScripts method obsolete**  
The `Html.Kentico().PageBuilderScripts()` method for [loading Page Builder scripts](/documentation/developers-and-admins/development/builders/page-builder/create-pages-with-editable-areas#load-page-builder-scripts-and-styles) was made obsolete. Use `Html.Kentico().PageBuilderScriptsAsync()`instead.
**PriceCalculationPromotionCandidate object obsolete**  
The `PriceCalculationPromotionCandidate` digital commerce object, storing promotion-related information, was made obsolete. The object was never intended to be exposed publicly. Instead, the information is surfaced via `IPriceCalculationPromotionCandidate`.
### Fixed issues
  * API – In versions 30.12.0 or newer, updating content items using the `IContentItemManager.TryUpdateDraft()` method shortly after creating a new draft or a new language variant of the item via the API resulted in an error (“Sequence contains no matching element”, “System.InvalidOperationException”).
  * Admin UI
    * After editing and then reverting a page or content item to its published version, certain field types displayed their value incorrectly. This was only a visual issue – values appeared correctly after refreshing the UI.
    * After enabling a discount in the **Promotions** application, its status in the breadcrumbs remained incorrectly set to “Deactivated” until the page was fully refreshed. 
  * Assets – When uploading [content item assets](/documentation/business-users/content-hub/content-item-assets) via the [API](/api/content-management/content-items#content-item-assets), the system now automatically ensures that the file extension value set in the asset metadata starts with a dot character (`.`). A missing dot in the metadata could lead to errors when [synchronizing content](/documentation/business-users/content-sync) that referenced the asset (“Error uploading asset” or “Unexpected error code (Forbidden)”). The change affects assets uploaded after the update is applied.
  * Events – The [event](/documentation/developers-and-admins/customization/handle-global-events) `WebPageEvents.Move.Execute`, which is triggered after the Move operation, provided incorrect values for the following event argument properties: `OriginalTreePath`, `OriginalOrder`, and `OriginalParentID`. The values reflected the page’s new location after the move instead of the original location.
  * Membership – [Members](/documentation/developers-and-admins/development/registration-and-authentication) who had an active [shopping cart](/documentation/developers-and-admins/digital-commerce-setup) could not be deleted. After applying the update, members with shopping carts can now be deleted, and their associated shopping cart records are removed automatically.
  * Page Builder – When a [Page Builder](/documentation/developers-and-admins/development/builders/page-builder) component was re-rendered after a request, it could display text in the default language instead of the appropriate translation. For example, this could occur in a custom widget with a form after submitting invalid data.
  * Pages – Copying [shareable preview URLs](/documentation/business-users/website-content) to the clipboard did not work in the Safari browser.
  * Performance
    * Improved performance when loading child pages in the content tree in web page applications.
    * the update improves the performance of system APIs used to resolve [resource strings](/documentation/developers-and-admins/customization/integrate-custom-code#store-application-resources-in-resource-files). 
  * Recycle bin – When an email was restored from the [recycle bin](/documentation/business-users/recycle_bin), the usage of [content items](/documentation/business-users/content-hub/content-items#track-usage-of-content-items) and [pages](/documentation/business-users/website-content/track-page-usages) referenced in [Email Builder components](/documentation/developers-and-admins/development/builders/email-builder/develop-email-builder-components) was not tracked correctly.
  * Rich text editor – The _Insert link_ → _Web URL_ [rich text editor](/documentation/business-users/rich-text-editor) option did not work correctly when used on more than one [asset](/documentation/business-users/content-hub/content-item-assets) in a single rich text editor field.


* * *
### SaaS update (January 22, 2026)
**Xperience by Kentico version update not required**
New features
  * Security events in Xperience Portal – The new [Security events](/documentation/developers-and-admins/deployment/deploy-to-the-saas-environment/manage-saas-deployments#security-events) application allows users to view security events detected by [Cloudflare CDN](/documentation/developers-and-admins/configuration/saas-configuration#cloudflare-cdn), such as DDoS attacks and blocked requests, directly in [Xperience Portal](/documentation/developers-and-admins/saas/xperience-portal). The application provides filtering options, a timeline view, and event details like the event’s Ray ID or HTTP request information.


Fixed issues
  * SaaS 
    * In rare cases, a failed [zero-downtime deployment](/documentation/developers-and-admins/deployment/deploy-to-the-saas-environment#zero-downtime-deployments) could leave the environment in an inconsistent state where the deployment slots were not properly synchronized. This prevented successful recovery through standard [restore](/documentation/developers-and-admins/deployment/deploy-to-the-saas-environment/manage-saas-deployments#restore-points-and-exports) operations and could cause the application to remain in an error state even after attempting to restore to a previous package.
    * In some cases, old versions of stored files were not deleted, leading to increased storage usage.
    * The application service could fail to start after a swap during the [deployment](/documentation/developers-and-admins/deployment/deploy-to-the-saas-environment#deploy-with-a-deployment-package).
    * When processing a large number of files, [exports](/documentation/developers-and-admins/deployment/deploy-to-the-saas-environment/manage-saas-deployments#restore-points-and-exports) could time out due to extensive data in temporary or cache folders. 


* * *
### Kentico migration tool (January 15, 2026)
**Xperience by Kentico version update not required**
**Kentico Migration Tool - Commerce entities support**  
The [Kentico Migration Tool](https://github.com/Kentico/xperience-by-kentico-kentico-migration-tool) now supports migrating commerce entities (customers, orders) from Kentico Xperience 13.
* * *
### MCP servers (January 15, 2026)
**Xperience by Kentico version update not required**
Fixed issues
  * Content Modeling MCP – The [Page Builder](/documentation/developers-and-admins/development/builders/page-builder) validation phase could fail with an internal server error when using the [Content Modeling MCP server](/guides/architecture/content-modeling/content-modeling-mcp).
  * Documentation MCP server – Changelog information available through [documentation MCP server](/documentation/developers-and-admins/installation/mcp-server) is now limited to releases from approximately the past year. Older release details may no longer be included.


* * *
### Hotfix (January 8, 2026)
**version 31.0.2**
Fixed issues
  * Performance – The hotfix improves the performance of the [ContentRetriever API](/documentation/developers-and-admins/api/content-item-api/content-retriever-api) when retrieving a larger number of content items or pages with [linked items](/documentation/business-users/content-hub/content-items#link-content-items).
  * Read-only deployments – When the application was running in [Read-only mode](/documentation/developers-and-admins/deployment/read-only-deployments), accessing assets in the blob storage that were not previously cached caused exceptions, and the assets failed to load.


* * *
### SaaS update (January 8, 2026)
**Xperience by Kentico version update not required**
Fixed issues
  * SaaS – The reliability of [restore](/documentation/developers-and-admins/deployment/deploy-to-the-saas-environment/manage-saas-deployments) operations in [zero-downtime deployments](/documentation/developers-and-admins/deployment/deploy-to-the-saas-environment) has been improved.


Updates and changes
  * SaaS – The minimum required Xperience by Kentico version for [zero-downtime deployments](/documentation/developers-and-admins/deployment/deploy-to-the-saas-environment) was increased to _31.0.2_. Zero-downtime deployment is now enabled only for projects running version _31.0.2_ or later and disabled for earlier versions to ensure deployment stability.


* * *
### KentiCopilot repository (January 8, 2025)
**Xperience by Kentico version update not required**
The [KentiCopilot repository](https://github.com/Kentico/xperience-by-kentico-kenticopilot) was updated with new prompts for migrating the _codebase_ of Kentico Xperience 13 projects to [Xperience by Kentico](/guides/upgrade-to-xbyk/upgrade-from-kx13).
These prompts are designed to help migrate the live site and page presentation logic as described in the [upgrade walkthrough](/guides/upgrade-to-xbyk/upgrade-walkthrough).
* * *
### Hotfix (December 18, 2025)
**version 31.0.1**
Updates and changes
  * Digital commerce – [Order notification emails](/documentation/developers-and-admins/digital-commerce-setup/configure-order-statuses#create-customer-notification-emails) now support two additional dynamic text placeholders: _OrderSubtotalPrice_ and _OrderDiscount_. These placeholders display the sum of all item prices before discounts and the total discount amount applied to an order, respectively. Additionally, the _OrderItems_ placeholder has been modified to display the item price after applying catalog discounts.


Fixed issues
  * Digital commerce – [Order promotions](/documentation/developers-and-admins/digital-commerce-setup/promotions/order-discounts) now accurately distribute the applied discount across line items. Previously, rounding during order discount distribution could cause small discrepancies where the sum of individual line item discounts did not exactly match the total order discount. For example, assume a shopping cart with three different items, each valued at $100, and an order discount of $100. In this scenario, the system distributed an even discount of $33.33 across the three line items, leaving the remaining $0.01 unapplied. The hotfix ensures that the full leftover discount ($33.34 in the example case) is always applied to the first line item.
  * Email Builder – When using MJML markup for [Email Builder components](/documentation/developers-and-admins/development/builders/email-builder/develop-email-builder-components), application-root-relative URLs (e.g., `~/getContentAsset/...`) in MJML attributes that generate CSS were not resolved correctly, causing missing images when viewing emails in _Preview mode_ and in sent emails. For example, the issue occurred for URLs in the `background-url` attribute of MJML components such as `<mj-hero>`.
  * Management API – The MCP server for the [management API](/documentation/developers-and-admins/development/content-types/management-api) failed to start when the related Xperience project targeted .NET 10.


* * *
## Refresh (December 11, 2025)
**version 31.0.0**
### .NET 10 support
Xperience by Kentico now supports application development using .NET 10, the latest major .NET framework release. For more information on .NET 10, see Microsoft’s [official announcement](https://devblogs.microsoft.com/dotnet/announcing-dotnet-10/).
The support also includes Kentico’s [SaaS](/documentation/developers-and-admins/saas/saas-overview) solution, which supports the deployment of projects based on .NET 10.
### New features
**Shareable preview URLs**  
Editors can now generate [shareable preview URLs](/documentation/developers-and-admins/configuration/shareable-preview-urls) for website channel pages, making it easy to collaborate with external contributors who don’t have access to the administration. Administrators can adjust the [automatic expiration period](/documentation/developers-and-admins/configuration/shareable-preview-urls#change-automatic-expiration) for these links in the _Settings_ application.
**Content modeling MCP server**  
The [Content Modeling MCP server](/guides/architecture/content-modeling/content-modeling-mcp) provides clear guidance and built-in validation to help you navigate the [content modeling](/guides/architecture/content-modeling) process, reducing the effort required to create content model prototypes and eliminating repetitive tasks, such as writing editor instructions. By making the early stages of design easier and allowing quick iteration, the server enables you to build better-structured, higher-quality Xperience prototypes faster and with greater confidence.
**Content type management API CRUD support**  
The [Content type management API](/documentation/developers-and-admins/development/content-types/management-api) and the related MCP server were expanded to support full CRUD (Create, Read, Update, Delete) operations for content types, reusable field schemas, and their fields. This enhancement enables automated content modeling workflows, accelerates migration scenarios, and simplifies integration with external systems that need to manage your project’s content model. For example, you can use the management API to quickly implement the designs created by the [Content Modeling MCP server](/guides/architecture/content-modeling/content-modeling-mcp).
**Catalog and order promotions for commerce stores**  
Developers can now implement custom promotion rules that automatically apply discounts to products during the price calculation. [Catalog discounts](/documentation/developers-and-admins/digital-commerce-setup/promotions/catalog-discounts) enable percentage-based discounts (e.g., 10% off), fixed amount discounts (e.g., $5 off), category-based promotions, and customer-targeted offers. [Order discounts](/documentation/developers-and-admins/digital-commerce-setup/promotions/order-discounts) enable a price reduction for the overall order (e.g., 15% off for all orders above $100).
Store managers [create and configure](/documentation/business-users/manage-commerce-stores#manage-promotions) promotions in the administration interface, including discount values, target products, and activation schedules.
**Clone headless items**  
Headless items can now be [cloned](/documentation/business-users/headless-content#clone-headless-items) to quickly create new items with the same data, including the option to clone all language variants at once.
### Updates and changes
**.NET 10 in Kentico.Xperience.Templates projects**  
.NET project templates installed from the [Kentico.Xperience.Templates](https://www.nuget.org/packages/Kentico.Xperience.Templates) NuGet package version 31.0.0 and newer now target .NET 10 instead of .NET 8.
**Database changes**  
The following columns were removed from the `OM_ActivityType` database table:
  * ActivityTypeManualCreationAllowed
  * ActivityTypeMainFormControl
  * ActivityTypeDetailFormControl
  * ActivityTypeColor
  * ActivityTypeItemObjectType
  * ActivityTypeItemDetailObjectType


These columns were not used by the system, but their removal may break custom database queries, scripts or procedures.
**IEquatable support**  
The `ContentItemReference`, `TagReference`, and `SmartFolderReference` classes now implement `IEquatable<T>`, enabling proper equality comparison based on their `Identifier` property. This allows developers to use standard collection methods like `Contains()`, `Intersect()`, and `Distinct()` when working with collections of content references. Two reference objects are considered equal if they have the same `Identifier` value.
**Digital commerce**
  * `IOrderCreationService` and `IPriceCalculationService` APIs for [digital commerce](/documentation/developers-and-admins/digital-commerce-setup) are no longer considered experimental and can be used in production projects.
  * The price calculation logic now supports calculation modes that optimize the calculation pipeline for different commerce scenarios. Developers can set the `Mode` property on `PriceCalculationRequest` to control which calculation steps execute.


### New object types for CI/CD
The update introduces the following object types, which are supported by the [Continuous Integration](/documentation/developers-and-admins/ci-cd/continuous-integration) and [Continuous Deployment](/documentation/developers-and-admins/ci-cd/continuous-deployment) features. Consider updating the [repository.config](/documentation/developers-and-admins/ci-cd/configure-ci-cd-repositories) files of your CI/CD repositories, particularly when using the `<IncludedObjectTypes>` allowlist for object type filtering.
  * `commerce.promotion` – Stores created [promotions](/documentation/developers-and-admins/digital-commerce-setup/promotions).


### Newly obsolete API
**Legacy Form Builder visibility condition API**  
All API members related to the legacy implementation of Form Builder visibility conditions are now obsolete. For example, this includes the following classes from the `Kentico.Forms.Web.Mvc` namespace:
  * `VisibilityCondition` and `AnotherFieldVisibilityCondition` classes
  * `RegisterFormVisibilityCondition` attribute
  * All default validation rule implementations, e.g., `EqualityVisibilityCondition`


This API is only used by the compatibility mode for forms from older versions and will be removed in the future.
**EventLogHelper.ClearEventLog method**  
The `EventLogHelper.ClearEventLog` method is now obsolete. If you wish to delete all event log records, use `IInfoProvider<EventLogInfo>.BulkDelete()` instead.
### Fixed issues
  * Content hub
    * On projects with a large number of [languages](/documentation/developers-and-admins/configuration/languages), the language flags displayed in content item listings weren’t collapsed correctly, which could block other columns in the list. The issue occurred in versions 30.11.0 or newer.
    * When the content item listing was displayed with a horizontal scrollbar, interaction with the row selection checkboxes in the list didn’t work correctly and list item action buttons couldn’t be selected in certain cases. 
  * Content item assets – An empty side panel was displayed when a user tried to insert or link a content item asset (using the _Insert image_ or _Insert link_ → _Asset_ toolbar options) into the [rich text editor](/documentation/business-users/rich-text-editor) without having the _View_ permission for the _Content hub_ application.
  * Content synchronization – An error occurred during the [content sync](/documentation/business-users/content-sync) restoration of pages that were in the _Draft (New version)_ status (or a [custom workflow step](/documentation/developers-and-admins/configuration/workflows)) and also had multiple [vanity URLs](/documentation/business-users/website-content/manage-page-urls#manage-vanity-urls-of-pages).
  * Form Builder – Form components with properties defined using legacy `EditingComponent` attributes could not be configured in the Form Builder interface when the `CMSBuilderScriptsIncludeJQuery` [configuration key](/documentation/developers-and-admins/configuration/reference-configuration-keys) was set to its default _false_ value.
  * Forms – When a form was [rendered](/documentation/developers-and-admins/development/builders/page-builder/render-widgets-in-code) directly in the code of a page that used [custom .NET routing](/documentation/developers-and-admins/development/routing/content-tree-based-routing/set-up-content-tree-based-routing#combine-content-tree-based-and-asp.net-routing), an error occurred and the form widget was not displayed. The issue occurred on version 30.12.0 or newer.
  * Headless channels – Items selected in the headless item selector side panel did not persist when switching pages in the side panel.
  * Media libraries – [Migration of media libraries](/guides/architecture/media-libraries-migration-guidance) to Content hub could in rare cases fail with a `TaskCanceledException` exception due to a combination of page reload and propagation of the `CancellationToken` associated with the HTTP request for the migration [scheduled task](/documentation/developers-and-admins/customization/scheduled-tasks).
  * Recycle bin – When the display name of a page in the [recycle bin](/documentation/business-users/recycle_bin) corresponded to the [URL slug](/documentation/business-users/website-content/manage-page-urls) of a page in the original location, the page in the recycle bin could not be restored even if there was no URL collision.
  * UI form components – Custom [admin UI form components](/documentation/developers-and-admins/customization/extend-the-administration-interface/ui-form-components) with a data type that was not [registered](/documentation/developers-and-admins/customization/field-editor/data-type-management#register-custom-data-types) in the system were not compatible with the Form Builder. If such a component was assigned to provide the UI of a [Form Builder component property](/documentation/developers-and-admins/development/builders/form-builder/form-components/form-component-properties), an error (`NotSupportedException`) occurred in the resulting form field configuration dialog.
  * Users – If an administration [user](/documentation/developers-and-admins/configuration/users/user-management) changed the _Email_ of their own account, the original email address remained cached until the application was restarted. This could cause issues, such as incorrectly sent [notification](/documentation/developers-and-admins/configuration/notifications) emails.


* * *
### KentiCopilot repository (December 4, 2025)
**Xperience by Kentico version update not required**
We published a new [KentiCopilot repository](https://github.com/Kentico/xperience-by-kentico-kenticopilot), which provides pre-configured prompts and instructions for everyday Xperience development tasks.
The repository includes:
  * Ready-to-use prompts for GitHub Copilot, Cursor, and Claude Code (transferable to other AI assistants)
  * Step-by-step setup instructions to integrate prompts into your Xperience projects


* * *
### Hotfix (December 4, 2025)
**version 30.12.3**
Fixed issues
  * Content item assets – In the Safari browser, drag and drop uploads to the [Asset uploader](/documentation/developers-and-admins/development/content-types#add-option-to-upload-files) form component and the [Combined content selector](/documentation/developers-and-admins/customization/extend-the-administration-interface/ui-form-components/reference-admin-ui-form-components#combined-content-selector) failed. The issue affected the selector if it was in single-selection mode (`MinimumItems = 1`, `MaximumItems = 1`).


* * *
### Hotfix (November 27, 2025)
**version 30.12.2**
Fixed issues
  * General – The hotfix updates several internal third-party dependencies of client admin UI scripts to newer versions that address vulnerabilities.


* * *
### SaaS update (November 27, 2025)
**Xperience by Kentico version update not required**
Fixed issues
  * SaaS – In projects that hadn’t yet undergone maintenance scheduled after the last [SaaS update](/documentation/changelog#saas-update-november-20-2025), the post-deployment cleanup of [deployments](/documentation/developers-and-admins/deployment/deploy-to-the-saas-environment) that caused application downtime could fail. The issue had no impact on the deployed projects.


* * *
### Hotfix (November 20, 2025)
**version 30.12.1**
Fixed issues
  * AIRA translations 
    * Opening the _Mass translate_ side panel during [AIRA translations](/documentation/business-users/aira#aira-translations) incorrectly triggered a notification about unsaved changes and prevented the translation-in-progress page from appearing.
    * The [translation](/documentation/business-users/aira#aira-translations) of content items and pages using AIRA could fail in case any of their content fields contained 5000-7000 characters. 
  * Admin UI – Users could select more than one item when using range selection _(Shift+Click)_ in listings with only a single item selection allowed. The multi-selection was purely visual, and more than one item couldn’t be selected. This issue occurred in version 30.12.0.


* * *
### SaaS update (November 20, 2025)
**Xperience by Kentico version update not required**
New features
  * Zero-downtime deployments – [Zero-downtime deployments](/documentation/developers-and-admins/deployment/deploy-to-the-saas-environment#zero-downtime-deployments) allow you to update your live site without service interruptions. [SaaS deployments](/documentation/developers-and-admins/deployment/deploy-to-the-saas-environment) use Kentico-managed infrastructure that automatically switches your application to [read-only mode](/documentation/developers-and-admins/deployment/read-only-deployments) to maintain data consistency. Your application code must be [configured](/documentation/developers-and-admins/deployment/read-only-deployments#required-project-changes) to support read-only mode before zero-downtime deployments can be used.


Updates and changes
  * Separate Restore points and Exports applications – [Xperience Portal](/documentation/developers-and-admins/saas/xperience-portal) now displays _Restore points_ and _Exports_ in separate applications under _Data Management_ for projects with version 30.1.0 or higher deployed across all environments. This change separates restore points (used to restore your application to a previous state) from exports (used to download data for inspection or long-term storage). For more information, see [Restore points and exports](/documentation/developers-and-admins/deployment/deploy-to-the-saas-environment/manage-saas-deployments#restore-points-and-exports).


* * *
## Refresh (November 13, 2025)
**version 30.12.0**
### New features
**Smart drag and drop asset uploads**  
Users can now drag and drop assets directly into the combined content selector and inline rich text editor to create content item assets of a configured [content type](/documentation/developers-and-admins/development/content-types#mass-asset-upload-configuration).
**Read-only deployment support**  
Xperience by Kentico applications can now run and be deployed in read-only mode. Switching traffic from a live application to a read-only deployment enables zero-downtime database and file system updates for production environments. When read-only mode is enabled, the system automatically blocks all write operations to the database and Azure Blob Storage, ensuring data consistency during deployment windows. See [Read-only deployments](/documentation/developers-and-admins/deployment/read-only-deployments) for more information.
Currently, read-only mode is available for [private cloud deployments](/documentation/developers-and-admins/deployment/deploy-to-private-cloud). Support for zero-downtime deployments of [SaaS](/documentation/developers-and-admins/saas/saas-overview) projects will be introduced soon.
**Restore deleted content**  
Deleting pages, content items, headless items, and emails is no longer permanent. Deleted items are retained in the system for a [configurable](/documentation/developers-and-admins/configuration/recycle-bin-management) period of time, during which they can be [recovered](/documentation/business-users/recycle_bin#restore-deleted-items). References to restored pages and content items in other content are restored along with these items.
**Validation rules for form fields**  
When creating forms in the Form Builder, users can now [add validation rules](/documentation/business-users/digital-marketing/forms/create-and-edit-forms#field-validation) to fields. These rules restrict which values can be submitted into fields. For example, validation rules can limit the maximum length of a field or enforce a specific format. The system provides a basic set of validation rules by default, and also allows developers to [define custom rules](/documentation/developers-and-admins/development/builders/form-builder/form-builder-validation-rules) suitable for project-specific scenarios.
**Content item cloning**  
New reusable content items can now be easily created by [cloning](/documentation/business-users/content-hub/content-items#clone-content-items) existing ones, with the option to clone all language variants of an item.
**Multi-select in listings**  
Users can now select a range of items in listings using _Shift+Click_ , making it much faster to perform bulk actions on multiple items simultaneously.
**Content type management API and MCP server preview**  
This update includes a preview version of a management API that allows retrieval and editing of objects within Xperience. The management API is intended to be used via AI tools together with a corresponding [Model Context Protocol (MCP)](https://modelcontextprotocol.io/docs/getting-started/intro) server.
At this time, the functionality is limited to retrieving information about [content types](/documentation/developers-and-admins/development/content-types) and [reusable field schemas](/documentation/developers-and-admins/development/content-types/reusable-field-schemas). Support for create, update and delete operations will be added in the future.
For detailed information and instructions, see [Content type management API](/documentation/developers-and-admins/development/content-types/management-api).
**Extended logging**  
The system now performs more detailed [logging](/documentation/developers-and-admins/development/logging) for the following functionality:
  * Content synchronization – structured logging and tracing during various steps of the [content sync](/documentation/business-users/content-sync) process, including both the source instance and content restoration on the target. This helps developers and administrators analyze potential issues that may occur when synchronizing content. See [Troubleshoot content sync](/documentation/developers-and-admins/configuration/content-sync-configuration#troubleshoot-content-sync) to learn how to configure your preferred logging provider to receive all available content sync logs.
  * Asynchronous tasks – logs that provide information about the start, completion and results of [scheduled tasks](/documentation/developers-and-admins/customization/scheduled-tasks) and background services. This gives developers and administrators more insight into actions that occur in the application’s background.


### Updates and changes
**Default event log size increased**  
The default size of the [event log](/documentation/developers-and-admins/configuration/event-log) (i.e., the value of the _Event log size_ setting) was increased to 10 000 items on newly installed projects. No changes are applied when updating existing projects.
**Logging of pre-initialization events**  
The system’s [logging](/documentation/developers-and-admins/development/logging) functionality was updated to provide buffering of errors and other log triggers that occur during startup before the Xperience by Kentico application is [initialized](/documentation/developers-and-admins/development/website-development-basics/configure-new-projects) (`InitKentico`). The buffer content is included in the log output after the application is initialized.
### Newly obsolete API
**Legacy Form Builder validation rule API**  
All API members related to the legacy implementation of Form Builder validation rules are now obsolete. For example, this includes the following classes from the `Kentico.Forms.Web.Mvc` namespace:
  * `ValidationRule` and `CompareToFieldValidationRule` classes
  * `RegisterFormValidationRule` attribute
  * All default validation rule implementations, e.g., `MaximumLengthValidationRule` and `EmailValidationRule`


This API is only used by the compatibility mode for forms from older versions, and will be removed in the future. Use the new [Form Builder validation rule framework](/documentation/developers-and-admins/development/builders/form-builder/form-builder-validation-rules) instead.
### Fixed issues – security
  * [Security advisory 2025-11-13](/documentation/security-advisories/security-advisory-2025-11-13) – information about security issues fixed within this release.


### Fixed issues
  * AIRA – [AIRA in-product guidance](/documentation/business-users/aira#aira-in-product-guidance) would sometimes fail to enable after logging into the Xperience by Kentico administration.
  * Admin UI – This update resolves issues with opening new tabs using Ctrl+Click/Cmd+Click or middle mouse click, and fixes drag-and-drop behavior for items in [website channel](/documentation/developers-and-admins/configuration/website-channel-management) navigation when using Firefox.
  * Azure storage – In certain cases, the [Azure Blob storage](/documentation/developers-and-admins/api/files-api-and-cms-io/file-system-providers/azure-blob-storage) provider attempted to use _Shared key authentication_ for connections even when configured to use [Managed identity authentication](/documentation/developers-and-admins/api/files-api-and-cms-io/file-system-providers/azure-blob-storage#managed-identity-authentication). This could prevent the application from accessing the file system.
  * Content synchronization – An error occurred during the restoration of [content sync](/documentation/business-users/content-sync) data for certain combinations of [page content](/documentation/business-users/website-content). For example, the issue occurred if the synchronized items contained a reference to a page within a folder, when both the page and parent folder didn’t exist on the target instance yet.
  * Digital commerce – In rare cases, a registered [member](/documentation/business-users/members) could have multiple related [shopping cart](/documentation/developers-and-admins/digital-commerce-setup/checkout-process) entries in the database. The update adds a uniqueness constraint to prevent this.
  * Emails – Usage of content items and pages in sent [regular emails](/documentation/business-users/digital-marketing/emails/send-regular-emails-to-subscribers) was incorrectly displayed in their respective _Used in_ tab.
  * Forms – After form validation failed for a [form](/documentation/business-users/digital-marketing/forms/create-and-edit-forms) containing a _Consent agreement_ field, the [consent short text](/documentation/developers-and-admins/data-protection/consent-management) displayed in the reloaded form was in the wrong [language](/documentation/developers-and-admins/configuration/languages). Instead of showing the consent text translation for the website’s selected language, it always displayed in the default language.
  * Performance – Adding and removing items from the [cache](/documentation/developers-and-admins/development/caching) could in some cases cause the application’s memory consumption to increase over time. Allocation of memory for cached data was improved to prevent the issue.
  * Project templates – Several issues related to [digital commerce](/documentation/developers-and-admins/digital-commerce-setup) were fixed in the _Dancing Goat_ sample project template.
  * Settings – Input validation in the [Settings](/documentation/developers-and-admins/configuration/settings) application didn’t work correctly, allowing invalid values to be saved.
  * Smart folders – Content item listings in the admin UI weren’t updated correctly in certain cases when switching between [smart folders](/documentation/business-users/content-hub/content-hub-folders#smart-folders).
  * System – If an error occurred when running the _Optimize database size_ action from the [System](/documentation/developers-and-admins/configuration/system-overview) application (e.g., due to temporary database unavailability), the task responsible for the action wasn’t able to recover and rerun at a later time.
  * UI form components
    * In some cases, the value saved by admin UI fields using the _Date input_ form component was one day earlier than the selected date. The issue occurred when the server’s date was one day behind the user’s local date due to time zone differences.
    * The _Text below the input_ option in the [field editor](/documentation/developers-and-admins/customization/field-editor) didn’t work for date and time form components (e.g., _Date and time input_ or _Date input_). The entered text wasn’t displayed for the resulting field.
    * When fields with a _Date and time input_ or _Date input_ form component were rendered in read-only mode, the _Now_ and _Today_ buttons remained functional and incorrectly allowed value changes. 


* * *
### Hotfix (November 6, 2025)
**version 30.11.3**
Fixed issues
  * Performance – This update improves the performance of the [content retriever API](/documentation/developers-and-admins/api/content-item-api/content-retriever-api). Performance issues could occur when retrieving a larger number of content items or pages with cached linked items.
  * System – The application failed to start if an error or other [logging](/documentation/developers-and-admins/development/logging) trigger occurred during startup before the Xperience by Kentico application was [initialized](/documentation/developers-and-admins/development/website-development-basics/configure-new-projects) (`InitKentico`). For example, this could happen when running on IIS with `MaxRequestBodySize` set to a value higher than IIS’s `maxAllowedContentLength` limit. The issue occurred for versions 30.11.0 or newer.


* * *
### Hotfix (October 30, 2025)
**version 30.11.2**
Fixed issues
  * SendGrid integration – If a project used [SendGrid to send emails](/documentation/developers-and-admins/configuration/email-configuration#sendgrid-integration) and stored a large number of sent regular emails (over 200), an error could occur during the recalculation of [email statistics](/documentation/business-users/digital-marketing/emails/track-email-statistics). This caused the email statistics to show incomplete data.


* * *
### SaaS update (October 30, 2025)
**Xperience by Kentico version update not required**
Fixed issues
  * Xperience Portal – [Restore points](/documentation/developers-and-admins/deployment/deploy-to-the-saas-environment/manage-saas-deployments#restore-points-and-exports) could be saved with an incorrect recovery timestamp, which could lead to failed restore attempts.


* * *
### Hotfix (October 23, 2025)
**version 30.11.1**
Fixed issues
  * Field editor – Deleting two content types in sequence could in rare cases cause the [field editor](/documentation/developers-and-admins/customization/field-editor) to become unresponsive when editing existing content or object types.
  * Forms – The HTML output of a [form](/documentation/business-users/digital-marketing/forms) was repeatedly wrapped into an additional `div` element whenever field validation failed while submitting the form. This could affect the layout of the page containing the form. The issue occurred while the `CMSBuilderScriptsIncludeJQuery` [configuration key](/documentation/developers-and-admins/configuration/reference-configuration-keys) was set to its default _false_ value.


* * *
### SaaS update (October 23, 2025)
**Xperience by Kentico version update not required**
Fixed issues
  * Xperience Portal – In some cases, when a package [deployment](/documentation/developers-and-admins/deployment/deploy-to-the-saas-environment) failed during the preprocessing stage (for example, when extracting the package or reading the _cloud-metadata.json_ file), the _Build Number_ in [Xperience Portal](/documentation/developers-and-admins/saas/xperience-portal) was displayed as `$(PackageMetadata.BuildNumber)` instead of being empty.


* * *
## Refresh (October 16, 2025)
**version 30.11.0**
### Important – Manual update steps
One of the features introduced by this release is [form usage tracking](/documentation/business-users/digital-marketing/forms/create-and-edit-forms#track-usage-of-forms), which uses a new type of reference to track which forms are displayed on pages. After updating your project, you need to manually run the _Track missing object usages_ [scheduled task](/documentation/developers-and-admins/customization/scheduled-tasks) to ensure the system accurately reflects form usage. While new references are tracked automatically after the update, existing references need to be tracked and generated using the scheduled task. This ensures that form usage tracking and all related features, such as content sync, function correctly.
### SaaS update
**Xperience by Kentico version update not required**
**App Service backups in SaaS environments**  
The [backup strategy](/documentation/developers-and-admins/deployment/deploy-to-the-saas-environment/manage-saas-deployments#restore-points-and-exports) for application service backups in SaaS environments has changed to improve the performance of backups and restores. As a result, it is no longer possible to download the app service files from a restore point. Instead, you can use [manual exports](/documentation/developers-and-admins/deployment/deploy-to-the-saas-environment/manage-saas-deployments#create-a-manual-export) to get access to the current state of the app service.
### Documentation MCP server
**Xperience by Kentico version update not required**
**Xperience by Kentico Documentation MCP server**  
You can now connect your IDE or AI assistant to the [Xperience by Kentico Documentation MCP server](/documentation/developers-and-admins/installation/mcp-server) to access up-to-date official documentation, including guides and API examples, directly within your development environment.
### New features
**Contact group filtering for regular email recipients**  
When [sending regular emails](/documentation/business-users/digital-marketing/emails/send-regular-emails-to-subscribers), the target recipient list can now be filtered by setting included or excluded [contact groups](/documentation/business-users/digital-marketing/contact-groups). This allows marketers to target specific audience segments without creating and maintaining separate recipient lists. The sending interface for regular emails now also displays the estimated number of recipients. See [Create contact groups for recipient filtering](/documentation/business-users/digital-marketing/emails/send-regular-emails-to-subscribers#create-contact-groups-for-recipient-filtering).
**Form usage tracking**  
The _Forms_ application now provides a new _Used in_ tab, which shows the pages that display the selected form in their Page Builder content. This helps users make informed decisions when editing a form or considering its deletion. Form usage tracking does not include pages that [render](/documentation/developers-and-admins/development/builders/page-builder/render-widgets-in-code) the form directly in their code. See [Track usage of forms](/documentation/business-users/digital-marketing/forms/create-and-edit-forms#track-usage-of-forms).
**Developer preview of price calculation and order creation services**  
The [digital commerce feature](/documentation/developers-and-admins/digital-commerce-setup) introduces two new services that enable you to streamline the development of your commerce solution. The [price calculation service](/documentation/developers-and-admins/digital-commerce-setup/price-calculation) introduces a flexible, customizable, and extensible way to automate price calculation of shopping carts and orders. The order creation service (`IOrderCreationService`) allows developers to automatically create orders from shopping carts, along with all related objects, such as customers and addresses. Both services are being released as a developer preview and are not intended to be used in production deployments.
**New logging functionality**  
Xperience by Kentico now uses the native .NET `ILogger` as its primary logging interface. This provides a better experience for developers and allows them to use existing .NET logging knowledge and tools. This improvement simplifies integration with third-party logging frameworks ([Serilog](https://serilog.net/), [NLog](https://nlog-project.org/), [Aspire dashboard](https://learn.microsoft.com/en-us/dotnet/aspire/fundamentals/dashboard/overview?tabs=bash), etc. ) and supports modern patterns such as dependency injection, configuration-based filtering, and structured logging. To learn more, see [Logging](/documentation/developers-and-admins/development/logging).
The data logged by specific Xperience libraries and features will be extended and improved over time to allow more accurate debugging, tracing and observability.
The system maintains full backward compatibility for the original logging approach. See [Event log changes and recommendations](#event-log-changes-and-recommendations) for detailed information.
**Navigation link UI improvements**  
The admin UI now supports standard browser tab-opening behavior for navigation links. Users can open links in new tabs using _Ctrl+Click_ / _Cmd+Click_ or _Middle mouse click_.
**Navigation link ‘href’ support**  
Developers can now implement proper navigation behavior in custom front-end components that use an `href` attribute, allowing these components to support _Ctrl+Click_ / _Cmd+Click_ and _Middle mouse click_ actions for opening links in new tabs.
### Updates and changes
**Channel code name validation**  
The code name configuration for channels (e.g., website, email) now has the same character restrictions for projects [installed](/documentation/developers-and-admins/installation#create-a-project) with the `--cloud` parameter as for [Xperience Portal](/documentation/developers-and-admins/saas/xperience-portal), and no longer allows special characters in the channel’s code name.
**Form code names**  
The _Code name_ and _Table name_ values generated for new [forms](/documentation/business-users/digital-marketing/forms) now contain additional numbers (representing milliseconds from the time when the form was created).
### Event log changes and recommendations
This refresh introduces a new [logging implementation](#New-logging-functionality-30110) based on the native .NET `ILogger`. The system maintains backward compatibility for logging via `IEventLogService` (now internally uses `ILogger`). However, we strongly recommend that you switch to `ILogger` when [writing new logging code](/documentation/developers-and-admins/development/logging#logging-in-custom-code) and update existing code when possible.
The update may also affects certain event log customization scenarios:
  * Custom implementations inheriting from `EventLogService` that modify the `IEnumerable<IEventLogWriter>` collection may result in logging issues. We recommend removing such customizations, since they block forwarding of events to the `ILogger` infrastructure.
  * Any [customizations](/documentation/developers-and-admins/customization/decorate-system-services) of `IEventLogService` will not apply to events that are logged via the new approach. Usage of `IEventLogService` will gradually be replaced by `ILogger` within the Xperience libraries.
  * Custom `IEventLogWriter` implementations continue to work without changes, but will become obsolete in the future. We recommend integrating [Custom logging providers](/documentation/developers-and-admins/development/logging#custom-logging-providers) instead.
  * The `KenticoEventLog` logging provider responsible for writing entries into the built-in [event log](/documentation/developers-and-admins/configuration/event-log) was removed, and replaced by a new `XperienceEventLog` provider. Any custom logging configuration for this provider in your project’s appsettings.json will no longer affect the system. You need to transfer such configuration to the new `XperienceEventLog` provider. To learn more, see [Configure logging](/documentation/developers-and-admins/development/logging#configure-logging).


Additionally, the new implementation brings the following general logging changes:
  * The new logging implementation includes logs within the command line output when running Xperience as a web application.
  * The descriptions of _UPDATEOBJ_ events in the [event log](/documentation/developers-and-admins/configuration/event-log) no longer contain details about the changed object fields if the legacy _CMSLogFieldChanges_ [configuration key](/documentation/developers-and-admins/configuration/reference-configuration-keys) is enabled. This key is now deprecated and has no effect.


### New object types for CI/CD
The update introduces the following object types, which are supported by the [Continuous Integration](/documentation/developers-and-admins/ci-cd/continuous-integration) and [Continuous Deployment](/documentation/developers-and-admins/ci-cd/continuous-deployment) features. Consider updating the [repository.config](/documentation/developers-and-admins/ci-cd/configure-ci-cd-repositories) files of your CI/CD repositories, particularly when using the `<IncludedObjectTypes>` allowlist for object type filtering.
  * `emaillibrary.sendconfigurationcontactgroup` – stores included and excluded [contact groups](/documentation/business-users/digital-marketing/contact-groups) in the sendout settings for [regular emails](/documentation/business-users/digital-marketing/emails/send-regular-emails-to-subscribers).


### Newly obsolete API
**EventLogService class obsolete**  
The `EventLogService` class (the default implementation of `IEventLogService`) was not intended for public use and is now obsolete. If you need to customize the event log, see [Configure logging](/documentation/developers-and-admins/development/logging#configure-logging) and [Customize event logging](/documentation/developers-and-admins/development/logging#customize-event-logging).
### Fixed issues – security
  * [Security advisory 2025-10-16](/documentation/security-advisories/security-advisory-2025-10-16) – information about security issues fixed within this release.


### Fixed issues
  * Admin UI – Several usability issues with date and time input components in the administration UI were fixed.
  * Assets – An error could occur when [translating a content item](/documentation/business-users/content-hub/content-items#translate-content-items) in Azure-hosted instances. The issue occurred when a translated content item contained an unoptimized image asset uploaded while [automatic image format conversion](/documentation/business-users/content-hub/content-item-assets#optimize-image-assets) was disabled, automatic image optimization was later enabled, and the application was running on an Azure App Service using the default local file system without a [blob storage](/documentation/developers-and-admins/api/files-api-and-cms-io/file-system-providers/azure-blob-storage) for assets.
  * Automation – If the _Set contact field_ value step in an [automation process](/documentation/business-users/digital-marketing/automation) was configured to set a contact field with a non-nullable data type to its default value, an error occurred when a contact entered the step, which blocked the process for the given contact. The error also appeared when attempting to edit the step in the Automation Builder. For example, this issue occurred if a step was configured to set a [custom contact field](/documentation/developers-and-admins/digital-marketing-setup/contact-configuration#add-custom-fields-to-contacts) with the _Boolean_ data type to a false value.
  * Azure storage
    * The The system failed to clean the local [Azure cache](/documentation/developers-and-admins/api/files-api-and-cms-io/file-system-providers/azure-blob-storage) if the paths provided via the _CMSAzureTempPath_ or _CMSAzureCachePath_ keys used the ‘/’ (forward slash) separator.
    * When cleaning [local Azure cache](/documentation/developers-and-admins/api/files-api-and-cms-io/file-system-providers/azure-blob-storage#optional-application-settings-for-azure-storage), the system was incorrectly deleting files that were newer than the configured age threshold instead of older ones. This could lead to the premature deletion of recently cached files. 
  * Channels – When deleting a headless item from a [headless channel](/documentation/developers-and-admins/configuration/headless-channel-management) (both micro and regular), the application was fully reloaded without displaying a confirmation message. After applying the hotfix, the application is no longer reloaded, and a confirmation message is displayed.
  * Content hub folders – An incorrect error message was displayed when creating a [content hub folder](/documentation/business-users/content-hub/content-hub-folders) without a specified folder name. After applying the hotfix, the validation in the creation dialog fails when a folder name is not specified and the correct message is displayed.
  * Content types – The _Include field’s content in AIRA translation_ checkbox was displayed for email and headless content types for which [AIRA translation](/documentation/business-users/aira#aira-translations) is not supported.
  * Localization – Error messages displayed in the admin UI if a network error occurred were not [localized](/documentation/developers-and-admins/customization/admin-ui-localization) into the user’s preferred language for the administration.
  * Xperience Portal – In certain cases, some [alert notifications](/documentation/developers-and-admins/deployment/deploy-to-the-saas-environment/manage-saas-deployments#alerts) were not delivered via email and were not displayed in the _Alerts_ application. Alert functionality has been restored.


* * *
### Hotfix (October 9, 2025)
**version 30.10.3**
Fixed issues
  * Content synchronization – [Synchronization](/documentation/business-users/content-sync) could in some cases fail for projects with data created using the [Kentico Migration Tool](/documentation/developers-and-admins/upgrade-to-xperience-by-kentico/migration-toolkit#kentico-migration-tool). This fix extends the partial solution of the issue introduced in Refresh 30.10.0.
  * Media libraries – Previously, [media files](/documentation/business-users/media-libraries/manage-media-files) whose `FilePath` values in the `Media_File` database table did not exactly match the expected paths (including case differences) were not [migrated](/guides/architecture/media-libraries-migration-guidance) to the Content hub application. The migration process is now case-insensitive, and media files with `FilePath` values that differ only by letter casing are now migrated correctly. Files with mismatched paths are skipped and logged in the [Event log](/documentation/developers-and-admins/configuration/event-log).


* * *
### Hotfix (October 2, 2025)
**version 30.10.2**
Fixed issues
  * Modules – The _Include field’s content in AIRA translation_ checkbox was displayed in module class UI form fields where it is not supported.
  * Pages – Items displayed in a website channel’s content tree were not updated asynchronously, which could lead to inconsistencies between the displayed items and their actual data. This could cause actions such as deleting or updating [language](/documentation/developers-and-admins/configuration/languages) variants to affect the wrong variant.
  * System – During the application startup, an error could occur for websites under heavy load. A `NullReferenceException` was thrown in some cases by the `WebPageUrlDataContextExtractor.ExtractFromLanguagePrefix` method, which could temporarily cause affected website requests to return a 500 Internal Server Error. The issue occurred for versions 30.6.3 or newer.
  * UI form components
    * For [fields](/documentation/developers-and-admins/customization/field-editor) not marked as required or fields within [reusable schemas](/documentation/developers-and-admins/development/content-types/reusable-field-schemas), [UI form components](/documentation/developers-and-admins/customization/extend-the-administration-interface/ui-form-components/reference-admin-ui-form-components) displayed their own configured [default value](/documentation/developers-and-admins/customization/field-editor#create-new-fields) when the underlying database value for the corresponding field was `NULL`. This was incorrect, as `NULL` can be a valid state for such fields. This occurred only for form components assigned to fields with a [non-nullable value type](/documentation/developers-and-admins/customization/field-editor/data-type-management) (such as [checkboxes](/documentation/developers-and-admins/customization/extend-the-administration-interface/ui-form-components/reference-admin-ui-form-components#checkbox)). After applying the hotfix, when the database value is `NULL`, these form components now display the [default of their underlying C# value type](https://learn.microsoft.com/en-us/dotnet/csharp/language-reference/builtin-types/default-values).
    * The name of the `NumberWithLabelComponent` UI form component was changed from _Text with label_ to _Number with label_. 


* * *
### Hotfix (September 25, 2025)
**version 30.10.1**
Fixed issues
  * AIRA translations – The _Translation queue_ application incorrectly allowed users to delete [AIRA translations](/documentation/business-users/aira#aira-translations) that were currently being processed. After applying the hotfix, translations that are being processed can no longer be manually deleted from the _Translation queue_ application and are only deleted automatically after they’re finished.
  * Content hub – If the last field with the _Content item_ asset data type was removed from a [content type](/documentation/developers-and-admins/development/content-types) with existing items (e.g., a content type representing images), an error occurred in the Content hub grid view and no content was displayed.
  * Pages
    * When [deleting a page with children](/documentation/business-users/website-content/delete-pages#deleting-a-page-with-child-pages) that shared the same tree path as a page with children in another website channel, the delete side panel incorrectly displayed the other channel’s page and its children. This issue did not impact the actual deletion.
    * [Scope configurations](/documentation/developers-and-admins/development/content-types/limit-the-pages-users-can-create#define-content-type-scopes) for page content types could be evaluated incorrectly during [page creation](/documentation/business-users/website-content/create-pages) if the project contained multiple website channels and the new page’s tree path was the same for more than one channel (e.g., multiple website channels with the _/articles_ tree path). 


* * *
### AIRA update (September 24, 2025)
**NuGet packages not released for this update**
Updates and changes
  * AIRA translations – Improved the reliability of [AIRA translations](/documentation/business-users/aira#aira-translations) for text that contains special characters.


* * *
## Refresh (September 22, 2025)
**version 30.10.0**
### New features
**Azure Blob Storage managed identity authentication support**  
[Azure Blob storage](/documentation/developers-and-admins/api/files-api-and-cms-io/file-system-providers/azure-blob-storage) providers now support [managed identity authentication](https://learn.microsoft.com/en-us/entra/identity/managed-identities-azure-resources/overview) as an alternative to traditional shared key authentication. The new authentication method enhances security by eliminating the need to store sensitive access keys in application configuration. Managed identity provides automatic credential rotation, fine-grained access control through Azure RBAC, and reduced risk of credential exposure. See [Managed identity authentication](/documentation/developers-and-admins/api/files-api-and-cms-io/file-system-providers/azure-blob-storage#managed-identity-authentication).
**Translation of linked content and mass translation using AIRA**  
[AIRA translations](/documentation/business-users/aira#aira-translations) now support automatic translation of linked items when translating a page or content item. The [Content hub](/documentation/business-users/content-hub) application now also supports [mass translation](/documentation/business-users/content-hub/content-items#mass-translating) of content items using AIRA.
**Scaled image variants**  
Administrators and developers can now [configure](/documentation/developers-and-admins/development/content-types#configure-image-variants) a new _Scaled_ [image variant](/documentation/business-users/content-hub/content-item-assets#image-variants) type. Scaled image variants resize image dimensions based on the specified width and height while maintaining the original aspect ratio, which can help with the [resolution switching](https://developer.mozilla.org/en-US/docs/Web/HTML/Guides/Responsive_images#how_do_you_create_responsive_images) problem.
### Updates and changes
**Date and time input updates**  
Date and time input components in the administration UI were updated to provide an improved and more consistent user experience, with support for keyboard navigation. The components were also enhanced with [ARIA](https://developer.mozilla.org/en-US/docs/Web/Accessibility/ARIA) attributes to improve accessibility and screen reader support.
**API for deleting pages and content items updated**  
The `Delete` methods in `IWebPageManager` and `IContentManager` were updated to accept parameter objects (`DeleteWebPageParameters` and `DeleteContentItemParameters`). The previous method variants requiring individual parameters were marked as obsolete.
**Smart crop image variants**  
Previously, the system supported only a single type of [image variant](/documentation/business-users/content-hub/content-item-assets#image-variants) for cropping and resizing. With the introduction of the new _Scaled_ image variant type, the original functionality is now available as the Smart crop variant type.
### Newly obsolete API
**Deprecated DateTimePicker front-end component**  
The `DateTimePicker` front-end input component from the `@kentico/xperience-admin-components` package is now deprecated. Instead, you can use the new `DateTimeInput` and `DateTimeRangeInput` components from the same package in your custom front-end code.
### Fixed issues
  * AIRA translations
    * The AI model currently used by [AIRA](/documentation/business-users/aira) experienced performance degradation, resulting in failed translations of content with extensive text fields.
    * When translating content items or pages using [AIRA](/documentation/business-users/aira#aira-translations), binary files (e.g., [content item assets](/documentation/business-users/content-hub/content-item-assets)) were not copied to the newly created [language](/documentation/developers-and-admins/configuration/languages) variant. This could cause the original binary file to be deleted when the new language variant is deleted. 
  * Admin UI – The confirmation snackbar was not displayed when deleting items from general listings.
  * CI/CD – When restoring data using the [Continuous Integration](/documentation/developers-and-admins/ci-cd/continuous-integration) or [Continuous Deployment](/documentation/developers-and-admins/ci-cd/continuous-deployment) features, the system didn’t validate code name uniqueness for [reusable field schemas](/documentation/developers-and-admins/development/content-types/reusable-field-schemas) and GUID uniqueness for [content type](/documentation/developers-and-admins/development/content-types) definitions, which could lead to data inconsistencies. After applying the update, an error occurs when attempting to restore a reusable field schema or content type definition with an existing identifier.
  * Content synchronization – [Synchronization](/documentation/business-users/content-sync) could in some cases fail for projects with data created using the [Kentico Migration Tool](/documentation/developers-and-admins/upgrade-to-xperience-by-kentico/migration-toolkit#kentico-migration-tool).
  * Digital commerce
    * Creating a new order status with [customer notifications](/documentation/developers-and-admins/digital-commerce-setup/configure-order-statuses) when there were no email channels available resulted in an error. The email channel selector now displays a corresponding message.
    * It was possible to [delete customer objects](/documentation/business-users/manage-commerce-stores#manage-customers) even if other objects were already linked to the customer object (e.g., orders, customer addresses). The customer object would not be deleted and an error would be logged.
    * The option to sort [customers](/documentation/business-users/manage-commerce-stores) in the _Customers_ application by the date of their last order was added. 
  * Localization
    * In certain cases, the metadata of [event log](/documentation/developers-and-admins/configuration/event-log) entries, such as event source, was incorrectly localized, which interfered with searching and filtering.
    * The names of days displayed in date selection dialogs were not [localized](/documentation/developers-and-admins/customization/admin-ui-localization) to the user’s preferred language for the administration interface. 
  * Notifications – An error when processing a single [notification](/documentation/developers-and-admins/configuration/notifications) email could have resulted in the loss of all other notification emails scheduled to be sent in the same batch.
  * Pages – The dialog notifying about unsaved changes was not displayed when users left the admin UI page for creating a new [language](/documentation/developers-and-admins/configuration/languages) variant of a page or when [creating a new page](/documentation/business-users/website-content/create-pages).
  * Performance – In some cases, the system used inefficient queries to load the data of [content item](/documentation/business-users/content-hub/content-items) listings in the _Content hub_ application and selectors, resulting in slow page loads, particularly for [workspaces](/documentation/developers-and-admins/configuration/users/role-management/workspaces) with a very large number of content items.
  * Project templates – Several issues related to [digital commerce](/documentation/developers-and-admins/digital-commerce-setup) were fixed in the _Dancing Goat_ sample project template.
  * Update procedure – An error occurred when [updating](/documentation/developers-and-admins/installation/update-xperience-by-kentico-projects) to version 30.7.0 or newer if the project contained multiple [customer journeys](/documentation/business-users/digital-marketing/customer-journeys) (4 or more).


* * *
### SaaS hotfix (September 18, 2025)
**NuGet packages not released for this update**
Fixed issues
  * Xperience Portal 
    * The listing of DNS records during the [pre-validation of website channel domains](/documentation/developers-and-admins/configuration/website-channel-management#add-custom-website-channel-domains) was improved.
    * The validation of [Azure Blob storage](/documentation/developers-and-admins/api/files-api-and-cms-io/file-system-providers/azure-blob-storage#azure-blob-storage-for-kenticos-saas) container names during [custom restore](/documentation/developers-and-admins/deployment/deploy-to-the-saas-environment#deploy-a-large-amount-of-data-during-initial-deployments) and logging of related errors were improved. 


* * *
### AIRA update (September 11, 2025)
**NuGet packages not released for this update**
Updates and changes
The maximum supported number of [image variant](/documentation/business-users/content-hub/content-item-assets#image-variants) definitions has been increased to 63. Exceeding this limit may result in unexpected behavior.
* * *
### Hotfix (September 4, 2025)
**version 30.9.2**
Fixed issues
  * Content items – If the same content item was [linked](/documentation/business-users/content-hub/content-items#link-content-items) multiple times from different fields of a single page or content item, only the first field was populated in some scenarios when [retrieving the linked content](/documentation/developers-and-admins/development/content-retrieval/retrieve-content-items#retrieve-linked-content-items).
  * Localization – Attempting to access localization resources stored in a _.resx_ file that didn’t match the name of the corresponding C# class used to [register the resources](/documentation/developers-and-admins/customization/integrate-custom-code#store-application-resources-in-resource-files) in the system resulted in a `MissingManifestResourceException`. For example, resources stored in `Resources.resx` but registered under `MyApplicationResources.cs`. This issue made the upgrade to version _30.9.0 and newer_ not possible, as it prevented the CI/CD restore after update from passing. After applying the hotfix, the system logs a warning to the event log after detecting a resource name mismatch instead of raising an exception.
  * UI form components – When the [object selector](/documentation/developers-and-admins/customization/extend-the-administration-interface/ui-form-components/reference-admin-ui-form-components#object-selector) was opened and overflowed its parent element, the overflowing items were hidden on the first open, and no scrollbar appeared. After applying the hotfix, the items are now properly displayed and accessible with a scrollbar.


* * *
### Hotfix (August 28, 2025)
**version 30.9.1**
Updates and changes
The behavior of the _Store edited URLs as former URLs_ [setting](/documentation/business-users/website-content/manage-page-urls#automatically-store-edited-urls-as-former-urls) was updated. The setting now only controls whether new former URLs are generated when a page’s URL is edited. Existing [former URLs](/documentation/business-users/website-content/manage-page-urls) redirecting from old page URLs keep working even when the setting is disabled. If you no longer wish to redirect from a former URL, you need to [remove](/documentation/business-users/website-content/manage-page-urls#remove-former-urls-created-by-editing-urls) it.
Fixed issues – security
  * [Security advisory 2025-08-28](/documentation/security-advisories/security-advisory-2025-08-28) – information about security issues fixed within this release.


Fixed issues
  * AIRA – Translation of a content item or page using AIRA could have failed when the translated display name was longer than the allowed limit (100). AIRA now respects this limit during translation, and the display name from the source language is retained if the translated version is too long.
  * API – The `AddLinkParameters` and `AddCommandParameters` objects, used by the corresponding methods for [item and page header actions](/documentation/developers-and-admins/customization/extend-the-administration-interface/ui-pages/reference-ui-page-templates/listing-ui-page-template#add-header-and-item-actions) in the listing UI page template, now correctly expose their contained `ConfirmationParameters`. Previously, the `ConfirmationParameters` property incorrectly had an `internal` setter instead of `init`.
  * Admin UI – Fields using the _Tag selector_ [form component](/documentation/developers-and-admins/development/content-types#add-option-to-tag-content) marked as _required_ were missing the corresponding visual identificator in the administration interface (red asterisk next to field name).
  * Digital commerce – Customers were not able to create orders when [customer notifications](/documentation/developers-and-admins/digital-commerce-setup/configure-order-statuses#create-customer-notification-emails) were configured for the initial order status, and the notification email was using [Email Builder](/documentation/business-users/digital-marketing/emails/create-emails-in-email-builder).
  * Email Builder – When a previously published email with the _Automation_ or _Order status change_ purpose was modified in [Email Builder](/documentation/business-users/digital-marketing/emails/create-emails-in-email-builder) view mode and published again, the changes were not reflected and the email’s old version was used until the application’s cache was cleared.
  * Event log – The system failed to log events to the [event log](/documentation/developers-and-admins/configuration/event-log) if the associated request contained an invalid or malformed URL or URL referrer.
  * Modules – The _Reference to_ option used when [modeling object relationships](/documentation/developers-and-admins/customization/object-types/object-type-configuration/model-object-type-relationships) via the _Modules_ application incorrectly listed two _Scheduled task configuration_ object types.
  * Routing – When the _Store edited URLs as former URLs_ [setting](/documentation/business-users/website-content/manage-page-urls#automatically-store-edited-urls-as-former-urls) was disabled, [redirects](/documentation/business-users/website-content/edit-and-publish-pages#redirects-from-unpublished-and-deleted-pages) from deleted pages did not work and visitors received 404 errors.


* * *
## Refresh (August 25, 2025)
**version 30.9.0**
### New features
**Content synchronization for forms**  
The [Content sync](/documentation/business-users/content-sync) feature was extended to support [forms](/documentation/business-users/digital-marketing/forms). Users can now synchronize forms and their field configuration from one instance of Xperience by Kentico to another, either directly from the Forms application or when synchronizing pages that include a form in their Page Builder content. Currently, forms are synchronized without their submission data, autoresponder settings, and related automation processes or submission notifications.
**Customer journey insights**  
AIRA now provides [in-product insights](/documentation/business-users/aira#customer-journey-insights) into [customer journeys](/documentation/business-users/digital-marketing/customer-journeys) through its chatbot interface. The insights show a breakdown of the [contact groups](/documentation/business-users/digital-marketing/contact-groups) represented in a journey, how the contacts in these groups progress through stages, and which contact segments perform best or worst. This helps marketers and content editors better understand audience behavior and optimize engagement strategies.
**Deletion of inactive contacts**  
[Contact management](/documentation/business-users/digital-marketing/contact-management) features generate a very large amount of data, which may be overwhelming for marketers and eventually lead to performance issues. To mitigate these issues, the system now provides a way to regularly delete inactive contacts. See [Delete inactive contacts](/documentation/developers-and-admins/digital-marketing-setup/contact-configuration/delete-inactive-contacts) to learn more.
**AIRA translations**  
Editors can now [translate their content using AIRA](/documentation/business-users/aira#aira-translations) when creating new language variants of pages or content items.
**Shipping and payment options in digital commerce**  
[Digital commerce](/documentation/developers-and-admins/digital-commerce-setup) now enables users to configure [shipping and payment methods](/documentation/developers-and-admins/digital-commerce-setup/shipping-and-payment-methods) in the _Commerce configuration_ application.
**Admin UI localization – date and time globalization**  
[Localized](/documentation/developers-and-admins/customization/admin-ui-localization) admin UI interfaces now format date and time values displayed in the UI according to the user’s selected locale. This is based on the culture code used to [register](/documentation/developers-and-admins/customization/admin-ui-localization#configure-available-languages-for-users) the corresponding localization.
**Workflow notifications overview**  
Administrators can now [see an overview](/documentation/developers-and-admins/configuration/workflows#see-all-custom-workflow-steps-with-notifications-enabled) of all custom workflow steps with notifications enabled, making it easier to monitor notification coverage across workflows.
### Updates and changes
**Froala editor update**  
The [Froala WYSIWYG editor](https://froala.com/wysiwyg-editor) that provides the [Rich text editor](/documentation/business-users/rich-text-editor) in Xperience was updated to version 4.6.0. See [Froala Editor 4.6.0](https://froala.com/blog/editor/new-releases/froala-4-6-0-table-editing-react-19-support-find-replace/) for details.
**New ActionStateEvaluator async API**  
A new asynchronous version of the `ActionStateEvaluator` [API](/documentation/developers-and-admins/customization/extend-the-administration-interface/ui-pages/reference-ui-page-templates/listing-ui-page-template#actionstateevaluator) used for modifying commands of items in [listing UI page templates](/documentation/developers-and-admins/customization/extend-the-administration-interface/ui-pages/reference-ui-page-templates/listing-ui-page-template) was added.
**UI listing page template actions API**  
New variants of the existing listing UI page template methods used for adding [header and listing item actions](/documentation/developers-and-admins/customization/extend-the-administration-interface/ui-pages/reference-ui-page-templates/listing-ui-page-template#add-header-and-item-actions) were introduced. The new method variants accept similarly named parameter objects, rather than multiple individual parameters, for example:
C#
Copy
```
.AddCommand
  (
    new AddCommandParameters(commandName: nameof(DoSomething), label: "Title") { }
  );
```

### New object types for CI/CD
The update introduces the following object types, which are supported by the [Continuous Integration](/documentation/developers-and-admins/ci-cd/continuous-integration) and [Continuous Deployment](/documentation/developers-and-admins/ci-cd/continuous-deployment) features. Consider updating the [repository.config](/documentation/developers-and-admins/ci-cd/configure-ci-cd-repositories) files of your CI/CD repositories, particularly when using the `<IncludedObjectTypes>` allowlist for object type filtering.
  * `commerce.shippingmethod` – stores configured [shipping methods](/documentation/developers-and-admins/digital-commerce-setup/shipping-and-payment-methods) for the checkout process.
  * `commerce.paymentmethod` – stores configured [payment methods](/documentation/developers-and-admins/digital-commerce-setup/shipping-and-payment-methods) for the checkout process.


### Newly obsolete API
**Legacy contact management API**  
The following API classes from the `CMS.ContactManagement` namespace were used by legacy features that are not implemented in Xperience by Kentico and were made obsolete without replacements:
  * `AccountInfo`
  * `AccountContactInfo`
  * `AccountStatusInfo`
  * `ContactRoleInfo`
  * `ContactStatusInfo`
  * `AccountHelper`


**Contact batch deletion API**  
The `IContactsDeleteService` interface from the `CMS.ContactManagement` namespace is now obsolete. If you need to delete batches of contacts, use the new `IContactsBulkDeletionService`, which provides async support. See [Delete inactive contacts](/documentation/developers-and-admins/digital-marketing-setup/contact-configuration/delete-inactive-contacts) for more information and examples.
### Fixed issues
  * AIRA – Deprecation warnings in the [feature settings](/documentation/developers-and-admins/configuration/aira-configuration#manage-aira-features) of AIRA were displayed incorrectly, prompting customers to [update](/documentation/developers-and-admins/installation/update-xperience-by-kentico-projects) their Xperience by Kentico version even when no new version had been released.
  * Content hub – An error occurred when moving [content folders](/documentation/business-users/content-hub/content-hub-folders#content-folders) if the folder was renamed in the past, and its original name matched a folder in the new location.
  * Content items – If a [content item](/documentation/business-users/content-hub/content-items) was scheduled to be published or unpublished in one [language](/documentation/developers-and-admins/configuration/languages), its _Status_ was displayed incorrectly when viewing content item lists in another language where the item didn’t exist. The wrong language version’s scheduled status was displayed instead of the _Not translated_ status.
  * Content versioning – The date and time values displayed in [version history](/documentation/business-users/content-versioning) records were based on the server’s location. As of this update, they now correspond to the user’s local time zone.
  * Custom admin UI – When an individual [UI form component](/documentation/developers-and-admins/customization/extend-the-administration-interface/ui-form-components) was configured to appear as read-only on a [UI page](/documentation/developers-and-admins/customization/extend-the-administration-interface/ui-pages/ui-pages-with-forms) where `PageConfiguration.EditMode` was set to `FormEditMode.Default`, the component was incorrectly rendered as editable.
  * Customer journeys – Inconsistencies occurred in the results displayed for a [customer journey](/documentation/business-users/digital-marketing/customer-journeys) when a contact that fulfilled the conditions of a stage in the journey was [merged](/documentation/business-users/digital-marketing/contact-management#contact-merging) or deleted.
  * Digital commerce
    * Certain UI strings in the _Orders_ application were not [localizable](/documentation/developers-and-admins/customization/admin-ui-localization).
    * It was not possible to attach more than one [address](/documentation/developers-and-admins/digital-commerce-setup/checkout-process) to one customer. 
  * Localization
    * Some of the text displayed in the [content sync](/documentation/business-users/content-sync) dialog was not [localized](/documentation/developers-and-admins/customization/admin-ui-localization) correctly to the user’s preferred language for the administration interface.
    * The names of [contact](/documentation/business-users/digital-marketing/contact-management) fields were not [localized](/documentation/developers-and-admins/customization/admin-ui-localization) to the user’s preferred language for the administration interface, for example in the configuration dialog for the _Set contact field value_ step in [automation processes](/documentation/business-users/digital-marketing/automation). 
  * Rich text editor – When inserting links into the [rich text editor](/documentation/business-users/rich-text-editor), URLs containing encoded characters (e.g., spaces encoded as `%20`) were encoded again, which could break the URL in some cases.


* * *
## SaaS update (August 21, 2025)
**NuGet packages not released for this update**
### New features
**SaaS custom restore**  
[Custom restore](/documentation/developers-and-admins/deployment/deploy-to-the-saas-environment#deploy-a-large-amount-of-data-during-initial-deployments) allows you to upload a large amount of data to your SaaS environments. This feature helps you bypass the 2 GB [deployment package](/documentation/developers-and-admins/deployment/deploy-to-the-saas-environment#create-a-deployment-package) limit and simplifies initial deployments, for example, when [upgrading](/documentation/developers-and-admins/upgrade-to-xperience-by-kentico) to Xperience by Kentico.
### Updates and changes
**SaaS Terms of Service**  
Terms of Service (ToS) for Xperience by Kentico SaaS no longer need to be accepted by the administrator in [Xperience Portal](/documentation/developers-and-admins/saas/xperience-portal). Instead, ToS agreement is now part of the contract signing process.
**SaaS Backups application update**  
Following the update in the [last Refresh](#refresh-july-24-2025), where the _Backups_ feature for SaaS was changed to a system of [restore points and exports](/documentation/developers-and-admins/deployment/deploy-to-the-saas-environment/manage-saas-deployments#restore-points-and-exports), the _Backups_ application has now been replaced by the _Data Management_ set of applications. This new set includes restore points and the [custom restore](/documentation/developers-and-admins/deployment/deploy-to-the-saas-environment#deploy-a-large-amount-of-data-during-initial-deployments) feature added in this update.
* * *
### Hotfix (August 14, 2025)
**version 30.8.3**
Fixed issues
  * General – The hotfix updates several internal third-party dependencies of client admin UI scripts to newer versions that address vulnerabilities.


* * *
### Hotfix (August 7, 2025)
**version 30.8.2**
Fixed issues
  * Digital commerce – When a project used an [extended](/documentation/developers-and-admins/development/registration-and-authentication/add-fields-to-member-objects#modify-the-applicationuser-class) `ApplicationUser` class to represent [members](/documentation/business-users/members), invoking [shopping cart-related](/documentation/developers-and-admins/digital-commerce-setup/checkout-process#manage-the-current-shopping-cart) services (`ICurrentShoppingCartCreator` and `ICurrentShoppingCartRetriever`) resulted in an error (“No service for type …UserManager[…ApplicationUser] has been registered” exception).
  * Rich text editor – Multiple links leading to the same [media library file](/documentation/business-users/media-libraries) within a single [rich text field](/documentation/business-users/rich-text-editor) could cause an error when saving the content in which the field is located (e.g., a page, content item, email, etc.).


* * *
### Hotfix (July 31, 2025)
**version 30.8.1**
Fixed issues
  * Content hub – Cache wasn’t handled properly for the workspace selector found in the _Content hub_ application. This could lead to issues when accessing _Content hub_ for roles that had their permissions changed (e.g., removed permissions for a [workspace](/documentation/developers-and-admins/configuration/users/role-management/workspaces)).
  * Emails – If the system contained a very large number of logged [email statistics](/documentation/business-users/digital-marketing/emails/track-email-statistics), a timeout could occur during the periodic recalculation of these statistics for emails. The email statistic recalculation procedure was modified and its performance was improved to prevent such issues. Applying the update temporarily clears all calculated statistics for emails, which means data will not be displayed in the _Statistics_ view mode of emails in email channels. To restore the statistics, you can wait until the first periodic recalculation (approximately 30 minutes after updating your project) or use the _Refresh_ button in the _Statistics_ view of individual emails.


* * *
## Refresh (July 24, 2025)
**version 30.8.0**
### Manual update steps
After updating your project, use the _Track missing content item usages_ [scheduled task](/documentation/developers-and-admins/customization/scheduled-tasks) to ensure the system accurately reflects content usage. This is necessary because the update introduces support for tracking usages in [Email Builder](/documentation/developers-and-admins/development/builders/email-builder) emails and page selector references. While new references are tracked correctly post-update, existing references need to be tracked using the scheduled task to maintain accurate content tracking and ensure all related features, such as [tracking of page usages](/documentation/business-users/website-content/track-page-usages) and [content sync](/documentation/business-users/content-sync) function correctly.
### Media libraries sunset
[Media libraries](/documentation/business-users/media-libraries) and all their related API members are now obsolete. They will remain available and supported until **July 24, 2026** , after which they will be removed. We recommend beginning migration to their modern and future-proof alternative (content item assets) as soon as possible. See [Media libraries migration guidance](/guides/architecture/media-libraries-migration-guidance) for instructions on migrating media libraries to Content hub.
### New features
**Digital commerce production release**  
The [digital commerce](/documentation/developers-and-admins/digital-commerce-setup) feature was extended to allow administrators to inform customers about the status of their order via [notifications](/documentation/developers-and-admins/digital-commerce-setup/configure-order-statuses).
The digital commerce feature is now out of preview mode and the related API is no longer marked as experimental.
**Workflow notifications**  
The system now offers built-in [workflow notifications](/documentation/developers-and-admins/configuration/workflows#workflow-notifications). Notifications help to ensure that items move smoothly through the workflow. When moving items, users can add comments that are included in the notification email.
**Content synchronization for pages**  
The [Content sync](/documentation/business-users/content-sync) feature was extended to support [website channel pages](/documentation/business-users/website-content). Users can now transfer pages from one instance of Xperience by Kentico to another, either directly from the page tree in website channel applications, or automatically for linked pages when synchronizing other items.
**SaaS restore points**  
The Backups feature for SaaS deployments was updated to a system of [restore points and exports](/documentation/developers-and-admins/deployment/deploy-to-the-saas-environment/manage-saas-deployments#restore-points-and-exports). The new system enables users to quickly create restore points which can now be used to [restore the application](/documentation/developers-and-admins/deployment/deploy-to-the-saas-environment/manage-saas-deployments#restore-from-a-restore-point) to a previous state. It is also possible to export the database and download any services (database, storage, application service) directly from Xperience Portal.
**Admin UI localization**  
Xperience’s admin UI now supports localization. Users can select their preferred language when working, with automatic fallback to configured default languages. See [Admin UI localization](/documentation/developers-and-admins/customization/admin-ui-localization#distribute-localization-files) for more information.
**Known issue:** The system does not currently localize widget selection dialogs in [Builder](/documentation/developers-and-admins/development/builders) UIs.
**Support for the slash character (‘/’) in vanity URLs**  
[Vanity URLs](/documentation/business-users/website-content/manage-page-urls) can now be [configured](/documentation/developers-and-admins/development/routing/configure-forbidden-url-characters#allow-the-slash-character-in-vanity-urls) to allow the slash character (‘/’) in their paths.
**New API for management of page URLs**  
The update introduces a [new API](/api/content-management/page-url-management) that allows developers to [manage page URLs](/documentation/business-users/website-content/manage-page-urls) by editing URL slugs and vanity URLs, adding or removing vanity URLs, and setting the canonical URLs of pages.
**Preview mode detection for Email Builder components**  
When implementing Email Builder components, developers can now check whether the component is being viewed in preview mode (_Email Builder_ , _Preview_ or draft emails). This can be useful when working with contexts that are not available in preview modes, or for populating components with sample data for email previews. See [Email preview mode](/documentation/developers-and-admins/development/builders/email-builder/develop-email-builder-components#email-preview-mode) for detailed information.
### Updates and changes
**Minimum Xperience by Kentico version for new SaaS projects**  
The minimum Xperience by Kentico version for new projects that can be deployed to the SaaS environment is **30.1.0**. If you are using an earlier version, you need to [update](/documentation/developers-and-admins/installation/update-xperience-by-kentico-projects) your project to the latest version before deploying it to the SaaS environment. Existing projects are not affected, but we recommend updating them to the latest version.
**Improved digital commerce API**  
The API for the [digital commerce](/documentation/developers-and-admins/digital-commerce-setup) feature received several minor improvements and fixes. Additionally, `ICurrentShoppingCartService` was split into separate services for each action to simplify customization.
**Content synchronization for draft items**  
In previous versions, [content synchronization](/documentation/business-users/content-sync) was only supported for items in the _Published_ or _Unpublished_ status. With this update, items can be selected for synchronization if they were published in the past, but are currently in the _Draft (New version)_ status or a [custom workflow step](/documentation/developers-and-admins/configuration/workflows). In such cases, the latest _Published_ version of the item is synchronized.
**Resource registration with localization targets**  
The `RegisterLocalizationResource` attribute now accepts an optional `LocalizationTarget` parameter to specify whether resources are used on the client-side, server-side, or in Builder components. If not specified, the registration defaults to `LocalizationTarget.Server`, maintaining backward compatibility with existing usage.
**Pages and content items in fields of extendable system object types**  
The _Pages and reusable content_ [data type](/documentation/developers-and-admins/customization/field-editor/data-type-management) is now supported in custom fields of [extendable system object types](/documentation/developers-and-admins/customization/object-types/extend-system-object-types). Note that the system doesn’t maintain the referential integrity of objects linked via this data type. Custom code must account for the referenced objects no longer existing or being otherwise invalid.
**Email dynamic text information**  
Information about [dynamic text placeholders](/documentation/business-users/digital-marketing/emails#personalize-emails-with-dynamic-text) that are available for an email’s content is now displayed within a collapsible section at the bottom of the _Content_ view mode.
**Admin UI edit icon changes**  
The edit icon (
**Retention policy for restore points and exports (backups)**
  * All [restore points](/documentation/developers-and-admins/deployment/deploy-to-the-saas-environment/manage-saas-deployments#restore-points-and-exports) expire in 30 days
  * Daily restore points expire based on your service plan: 
    * Level 1 – 3 days
    * Level 2 – 3 days
    * Custom – 7 days or more
  * Weekly full exports expire in 30 days


### Newly obsolete API
**Media libraries**  
All [media library](/documentation/business-users/media-libraries) and media library file API members have been marked as obsolete. For guidance on how to replace these API members, see [Media library migration](/guides/architecture/media-libraries-migration-guidance). The most important ones include:
  * `AssetSelectorComponent` – media file selector [admin UI form component](/documentation/developers-and-admins/customization/extend-the-administration-interface/ui-form-components)
  * `AssetRelatedItem` – represents media files, e.g., in the media file selector
  * `MediaFileInfo`
  * `MediaLibraryInfo`
  * `IInfoProvider<MediaFileInfo>`
  * `IInfoProvider<MediaLibraryInfo>`
  * `IMediaFileUrlRetriever`
  * `IMediaFileUrl`
  * `IMediaProtectionService`


**IWebPageQueryResultMapper is now obsolete**  
The `IWebPageQueryResultMapper` class is now obsolete, following mapping changes introduced in [hotfix 30.6.2](#hotfix-june-26-2025). Use `IContentQueryModelTypeMapper`, which serves as a full alternative with additional features.
**ApplicationBackgroundService is now obsolete**  
The `ApplicationBackgroundService` is now obsolete. Use the `ApplicationLifecycleBackgroundService` class instead.
### Fixed issues
  * AIRA – In some cases, documentation links in [AIRA](/documentation/business-users/aira)’s in-product guidance responses were not rendered as clickable links, appearing as plain text instead of using the correct Markdown format `[text](URL)`.
  * Admin UI – When an invalid form was submitted in the administration UI, the system incorrectly returned a 200 HTTP status code. After the update, the 400 status code is returned to indicate that the invalid values were not saved.
  * Assets – In version 30.6.3, the _/getContentAsset_ endpoint was unintentionally modified to treat the language parameter as case-sensitive.
  * Automation – When configuring [Condition steps](/documentation/business-users/digital-marketing/automation#conditions) in an automation process, the _Contact has value in field_ condition didn’t work when used for a [custom contact fields](/documentation/developers-and-admins/digital-marketing-setup/contact-configuration) with a non-text data type (e.g., _Boolean_ fields). After the update, the condition can be used by evaluating the text representation of the field’s value, for example by checking that a _Boolean_ field is equal to 1 (for true values) or 0 (for false values). To fix existing conditions in automation processes, you need to edit and re-save the condition in the Automation Builder after applying the update.
  * Content item assets – Fixed an error that occurred when [optimizing](/documentation/business-users/content-hub/content-item-assets#optimize-image-assets) certain pre-existing [image assets](/documentation/business-users/content-hub/content-item-assets) on the Dancing Goat sample project template using the Adjust image dialog.
  * Content items – The _Track missing content item usages_ [scheduled task](/documentation/developers-and-admins/customization/scheduled-tasks) did not track usages for items in emails created in [Email Builder](/documentation/developers-and-admins/development/builders/email-builder).
  * Content management – If a [content type](/documentation/developers-and-admins/development/content-types) had a non-required field with the _Date_ or _Date and time_ data type, certain interactions with items of the given type caused empty values in the field to be replaced with a minimum date value.
  * Customer journeys – When a [](/documentation/business-users/digital-marketing/customer-journeys) customer journey was restored (deployed) using [CI/CD](/documentation/developers-and-admins/ci-cd), indexes were not created for certain database tables used to store the journey’s data. This could result in reduced performance when displaying the journey’s results in the administration.
  * Digital commerce – Multiple issues were fixed in the [Digital commerce](/documentation/developers-and-admins/digital-commerce-setup) feature. The [experimental](https://learn.microsoft.com/en-us/dotnet/csharp/language-reference/attributes/general#experimental-attributes) attribute was removed from the related API. We recommend that you start your commerce development with this version.
  * Email Builder
    * If [dynamic text placeholders](/documentation/business-users/digital-marketing/emails#personalize-emails-with-dynamic-text) were added into the values of email fields, and these fields were [retrieved and displayed](/documentation/developers-and-admins/development/builders/email-builder/develop-email-builder-components#email-fields) within the code of an Email Builder component (via `EmailContext.GetEmail<T>`), the internal notation of the dynamic text placeholders was incorrectly displayed within content in the Email Builder view mode. The issue did not affect the resulting emails sent to recipients or the email content displayed in _Preview_ mode.
    * The Email Builder UI did not display sufficient information if an error occurred within an [Email Builder component](/documentation/developers-and-admins/development/builders/email-builder/develop-email-builder-components) (template, widget, etc.). After the update, an appropriate message is displayed and an error with more detailed information is logged in the [event log](/documentation/developers-and-admins/configuration/event-log). 
  * Emails – [Dynamic text](/documentation/business-users/digital-marketing/emails#personalize-emails-with-dynamic-text) or [double opt-in links](/documentation/business-users/digital-marketing/emails/send-regular-emails-to-subscribers#add-a-subscription-form) inserted into the content of a rich text email field couldn’t be saved in certain cases when the save action was used immediately without making any other changes.
  * Forms – Deleting individual [form submissions](/documentation/business-users/digital-marketing/forms/manage-form-submissions) didn’t remove files uploaded via the _Upload file_ [form component](/documentation/developers-and-admins/development/builders/form-builder/reference-form-builder-components) from the file system. Applying the update ensures that the files are deleted together with form submissions, but does not clean up existing form files that were related to previously deleted submissions.
  * General – Dependencies of client [builder](/documentation/developers-and-admins/development/builders) scripts were updated to address known vulnerabilities: _form-data_ was updated to version 4.0.4.
  * Licensing – Fixed an issue that could occasionally cause the [communication](/documentation/developers-and-admins/installation/licenses#license-server-communication) between an instance and the Kentico license server to fail, logging an error to the [event log](/documentation/developers-and-admins/configuration/event-log).
  * Page Builder
    * An incorrect [page template](/documentation/developers-and-admins/development/builders/page-builder/page-templates-for-page-builder) could be used for a newly created version of a page if a new version of the page was previously created, its template was changed, and this change was reverted.
    * When moving a [section](/documentation/developers-and-admins/development/builders/page-builder/sections-for-page-builder) while the _Change section type_ dialog was opened, an error was logged to the browser console. 
  * Pages – Page usage was not tracked after running the _Track missing content item usages_ [scheduled task](/documentation/developers-and-admins/customization/scheduled-tasks) for pages linked using the [Page selector](/documentation/developers-and-admins/customization/extend-the-administration-interface/ui-form-components/reference-admin-ui-form-components#page-selector) UI form component.
  * Performance – Retrieval of linked items using the [IContentRetriever](/documentation/developers-and-admins/api/content-item-api/content-retriever-api) or [Content item query](/documentation/developers-and-admins/api/content-item-api/content-item-query-api) APIs was inefficient and used an excessive number of database queries. Applying this update reduces the number of queries and overall improves the performance of linked item retrieval.
  * SaaS – [SaaS backups](/documentation/developers-and-admins/deployment/deploy-to-the-saas-environment/manage-saas-deployments) (exports) could fail in some cases when custom database schemas were used. To use exported database backups with a local SQL Server, you need to enable [contained users authentication](https://learn.microsoft.com/en-us/sql/relational-databases/security/contained-database-users-making-your-database-portable?view=sql-server-ver17) on the server.
  * Workspaces – Switching to a different [workspace](/documentation/developers-and-admins/configuration/users/role-management/workspaces) in the Content hub application when the current page was still loading could have resulted in an error.


* * *
### Hotfix (July 17, 2025)
**version 30.6.4**
Fixed issues
  * API – In certain cases, retrieving content using the `IContentRetriever` API could result in an [InvalidCastException](https://learn.microsoft.com/en-us/dotnet/api/system.invalidcastexception?view=net-9.0).
  * Page permissions – Duplicate records could be created in the _CMS_WebPageAcl_ table during a project’s [update](/documentation/developers-and-admins/installation/update-xperience-by-kentico-projects) if the updated project’s database was in an inconsistent state (e.g., caused by a previously failed update). Automatic removal of existing and future duplicate records was introduced in one of the previous versions, and removed duplicates were logged in the [event log](/documentation/developers-and-admins/configuration/event-log). However, in certain cases, such as when the duplicates did not reference any specific page (e.g., duplicate entries for a website channel’s root), the removed duplicates were not logged. This had no impact on the removal process itself or the update operation, only on the visibility of the removals in the event log.


* * *
### Hotfix (July 3, 2025)
**version 30.6.3**
Fixed issues
  * Azure storage – When using [Azure storage](/documentation/developers-and-admins/api/files-api-and-cms-io/file-system-providers/azure-blob-storage), cleanup of Azure temporary and cache folders was performed inefficiently. Applying the hotfix changes how the cleanup process works and improves its performance. For existing projects that use [Azure storage](/documentation/developers-and-admins/api/files-api-and-cms-io/file-system-providers/azure-blob-storage), we recommend manually deleting the content of the _App_Data/AzureCache_ and _App_Data/AzureTemp_ folders before you redeploy with the hotfix applied.
  * Form Builder – If a selector [UI form component](/documentation/developers-and-admins/customization/extend-the-administration-interface/ui-form-components/reference-admin-ui-form-components) (e.g., the _Tag selector_) was assigned to a [property](/documentation/developers-and-admins/development/builders/form-builder/form-components/form-component-properties) of a Form Builder form component, an error occurred when attempting to _Apply_ the properties of form fields using the given Form Builder component.
  * Performance
    * Using [content tree-based routing](/documentation/developers-and-admins/development/routing/content-tree-based-routing) generated unnecessary SQL queries. The database was only queried when a URL was accessed for the first time; all following requests for that same path were resolved using the cache.
    * When [vulnerability scanning tools](https://en.wikipedia.org/wiki/Vulnerability_scanner) attempted to set the language parameter of the _/getContentAsset_ endpoint to an invalid value, an unnecessary query to the database was made, and an error was logged in the [event log](/documentation/developers-and-admins/configuration/event-log). However, there are no known vulnerabilities related to this endpoint. 
  * SaaS – Deploying files with a path length (file name and directory structure) longer than 260 characters could, in certain cases, prevent [deployment to SaaS environments](/documentation/developers-and-admins/deployment/deploy-to-the-saas-environment#deploy-to-the-saas-environment).


* * *
### Hotfix (June 26, 2025)
**version 30.6.2**
New features
  * **Model mapping improvements**
The system’s automatic [model mapping API](/documentation/developers-and-admins/api/content-item-api/content-item-query-api#run-queries-and-map-the-result) now supports a more flexible approach with relaxed restrictions on model classes. You can now use any non-abstract classes, structs, or records with a parameterless constructor for content mapping, without a corresponding registration via [RegisterContentTypeMappingAttribute](/documentation/developers-and-admins/api/generate-code-files-for-system-objects#registercontenttypemapping-attribute). This allows you to create custom, simplified models with only the properties you need, and use registered and unregistered models together. The change applies broadly to all system APIs that use the mapping logic ([Content item query](/documentation/developers-and-admins/api/content-item-api/content-item-query-api), [IContentRetriever](/documentation/developers-and-admins/api/content-item-api/content-retriever-api)).
When the automatic mapping falls back to an unregistered model, it follows the mapping behavior described in [the mapping documentation](/documentation/developers-and-admins/api/content-item-api/content-item-query-api#using-getmappedresult-methods). The result of the automatic mapping attempt can be further modified using custom logic.
Additionally, starting with this update we now recommend using `IContentQueryModelTypeMapper` instead of `IContentQueryResultMapper` for all content mapping scenarios. Corresponding documentation sections previously working with `IContentQueryResultMapper` were updated.


Fixed issues
  * Page Builder – Using empty values in the `href` and `src` HTML attributes within [Page Builder](/documentation/developers-and-admins/development/builders/page-builder) components could lead to styling issues in Page Builder edit mode.
  * Pages – When [Continuous Integration](/documentation/developers-and-admins/ci-cd/continuous-integration) was enabled with the `cms.webpageurlpath` [object type](/documentation/developers-and-admins/ci-cd/reference-ci-cd-object-types) included, it was not possible to delete the last [language variant](/documentation/developers-and-admins/configuration/languages) of a [page](/documentation/business-users/website-content) in the _Draft_ step.


* * *
### Hotfix (June 19, 2025)
**version 30.6.1**
Fixed issues
  * Custom admin UI – After updating a project to version 30.6.0, errors occurred when using custom or extended admin UI [listing pages](/documentation/developers-and-admins/customization/extend-the-administration-interface/ui-pages/reference-ui-page-templates/listing-ui-page-template) provided by external libraries that reference Xperience NuGet packages 30.5.4 or older. The changes could cause runtime errors on listing pages in the admin UI (“Method not found” exceptions), when the external libraries called the `AddColumn()` or `AddComponentColumn()` methods. The issue affected listing pages from custom and community packages, as well as [Xperience integrations](/documentation/developers-and-admins/third-party-integrations#external-packages) delivered via external packages. Applying this hotfix restores compatibility with external libraries.
  * Email Builder – If an [Email Builder template](/documentation/developers-and-admins/development/builders/email-builder/develop-email-builder-components#templates) contained multiple editable areas, an error (“Sections rendering context is not in a valid state.” exception) could occur in certain scenarios when a widget that loaded content from the database was placed into a section within the first editable area. The error prevented the system from sending such emails, and users could not view the email in _Preview_ mode.
  * Admin UI – In version 30.6.0, the _URLs_ tab in the page editing UI sometimes did not show the correct domain name in the displayed [page URLs](/documentation/business-users/website-content/manage-page-urls). The _Website domain_ [value](/documentation/developers-and-admins/configuration/website-channel-management#create-website-channels) set in the admin UI was used even when the domain set [programmatically](/documentation/developers-and-admins/configuration/website-channel-management#domain-aliases-and-environment-specific-domains) or through [Xperience Portal](/documentation/developers-and-admins/configuration/website-channel-management#manage-channel-domains-in-xperience-portal) should have been used instead.


* * *
## Refresh (June 12, 2025)
**version 30.6.0**
### New features
**Multiple vanity URLs for pages**  
Each [page](/documentation/business-users/website-content) can now have multiple [vanity URLs](/documentation/business-users/website-content/manage-page-urls). Marketers can leverage this feature to create different URLs for different audiences. The _URLs_ tab in the website channel’s page editing UI has been updated to better support managing a larger number of page URLs.
**Emergency deployments to SaaS environments**  
[Emergency deployments](/documentation/developers-and-admins/deployment/deploy-to-the-saas-environment#deploy-emergency-fixes-to-the-saas-environment) allow users to bypass the standard deployment flow and push critical fixes or version updates directly to SaaS environments other than _QA_ (e.g., to _UAT_ or even to _PROD_). This process allows users to rapidly deploy fixes even if the _QA_ environment is “blocked“ by a currently developed feature.
**Email Builder widget personalization**  
Marketers can now create personalized variants of [Email Builder widgets](/documentation/business-users/digital-marketing/emails/create-emails-in-email-builder#add-content-using-widgets). This allows you to increase the effectiveness of your emails, and deliver targeted content to email recipients based on the [contact groups](/documentation/business-users/digital-marketing/contact-groups) to which they belong.
**Email Builder improvements**  
The following functionality was added to [Email Builder](/documentation/developers-and-admins/development/builders/email-builder):
  * Preset [email templates](/documentation/business-users/digital-marketing/emails/create-emails-in-email-builder/email-builder-templates) – You can now save default email templates and their Email Builder content as preset email templates. These preset email templates can then be used as a starting point for new emails.
  * Changing [email templates](/documentation/business-users/digital-marketing/emails/create-emails-in-email-builder/email-builder-templates) – Editors can now switch between different templates for emails created in Email Builder.
  * Component restrictions – The option to set widget or section restrictions was added to Email Builder components, ensuring better control and organization.
  * Copy functionality – You can now copy Email Builder components ([widgets](/documentation/developers-and-admins/development/builders/email-builder/develop-email-builder-components#widgets), [sections](/documentation/developers-and-admins/development/builders/email-builder/develop-email-builder-components#sections)), and paste them in any emails where Email Builder is available.
  * Link pages and images – You can now link pages and images using the built-in [inline rich text editor](/documentation/developers-and-admins/development/builders/email-builder/inline-editors-email-builder-widgets#rich-text-inline-editor).


**Email preview improvements**  
The option to switch between different views of an [email](/documentation/business-users/digital-marketing/emails) was added to the _Preview_ mode in email channels, enabling users to see how their email is going to look in the _desktop_ , _mobile_ , and _inbox_ views. Additionally, users can copy the full HTML code of the email from _Preview_ mode.
**In-product guidance via AIRA**  
[AIRA](/documentation/business-users/aira) now provides in-product guidance through a built-in chatbot interface, which uses [Kentico’s Learn Portal](/documentation/) as the main source of information. Users can ask questions directly within the Xperience by Kentico administration to get feature explanations or best practice recommendations without leaving the application.
This feature is currently in an early preview stage. We welcome your feedback regarding its performance and the quality of the responses. Please send any comments or suggestions to aira@kentico.com.
**Commerce order status changes and notifications**  
Digital commerce feature preview now enables users to set up [order statuses](/documentation/developers-and-admins/digital-commerce-setup/configure-order-statuses#create-order-statuses), set up [internal notifications on status transitions](/documentation/developers-and-admins/digital-commerce-setup/configure-order-statuses#configure-notifications-for-order-statuses), and [manage orders](/documentation/business-users/manage-commerce-stores).
### Updates and changes
**Preset page templates application replacement**  
The Refresh introduces a new _Preset templates_ application that replaces the old _Preset page templates application_. The new application allows you to manage both [page](/documentation/business-users/website-content/page-templates) and [email](/documentation/business-users/digital-marketing/emails/create-emails-in-email-builder/email-builder-templates) preset templates. Roles with permissions for the _Preset page templates_ application have the same permissions for the _Preset templates_ application. After applying the update, all [custom admin UI pages](/documentation/developers-and-admins/customization/extend-the-administration-interface/ui-pages) registered under `PageTemplatesApplication` will be registered under `PresetTemplatesApplication`, and the old application will be marked as obsolete.
**ContentRetriever API updates**
  * The API is no longer marked experimental and can be used freely across projects.
  * `RetrieveContentByGuids<TResult>` now correctly handles mixed data returned by the combined content selector. The method also automatically ensures web page data for retrieved pages.


**GetListResult method update**  
A new `GetListResultAsync<T>` method was added to the [object query API](/documentation/developers-and-admins/api/objectquery-api), which provides an async variant of the `GetListResult<T>` method. Additionally, the inner implementation of the existing method was also updated, resulting in improved performance.
**Email Builder in the Dancing Goat project template**  
The _Dancing Goat_ sample project template was extended to include an [Email Builder](/documentation/business-users/digital-marketing/emails/create-emails-in-email-builder) demo, with several basic [Email Builder](/documentation/developers-and-admins/development/builders/email-builder/develop-email-builder-components) components.
**Rich text editor plugin registration API**  
The registration API for [custom rich text editor plugins](/documentation/developers-and-admins/configuration/rich-text-editor-configuration/rich-text-editor-plugins#implement-custom-plugins) was extended to provide the `froalaEditorConfigurator.configureOptions` method, which allows dynamic adjustments of the [editor configuration](/documentation/developers-and-admins/configuration/rich-text-editor-configuration).
**All content GUIDs and IDs accessible through UI**  
The _Information_ section on the _Properties_ tab now also displays _Content item ID_ and _Content item GUID_ for [content items](/documentation/business-users/content-hub/content-items), _Content item GUID_ and _Page GUID_ for [pages](/documentation/business-users/website-content), and _Headless item ID_ and _Headless item GUID_ for [headless items](/documentation/business-users/headless-content).
**Listing pages support tooltips for columns**  
The method `PageConfiguration.ColumnConfigurations.AddColumn()` now supports rendering tooltips for columns on [listing pages](/documentation/developers-and-admins/customization/extend-the-administration-interface/ui-pages/reference-ui-page-templates/listing-ui-page-template).
### New object types for CI/CD
The update introduces the following object types, which are supported by the [Continuous Integration](/documentation/developers-and-admins/ci-cd/continuous-integration) and [Continuous Deployment](/documentation/developers-and-admins/ci-cd/continuous-deployment) features. Consider updating the [repository.config](/documentation/developers-and-admins/ci-cd/configure-ci-cd-repositories) files of your CI/CD repositories, particularly when using the `<IncludedObjectTypes>` allowlist for object type filtering.
  * `emaillibrary.emailpresettemplate` – Email Builder configuration of saved [preset email templates](/documentation/business-users/digital-marketing/emails/create-emails-in-email-builder/email-builder-templates).
  * `commerce.orderstatus` – stores stages in the lifecycle of [orders](/documentation/developers-and-admins/digital-commerce-setup/configure-order-statuses).
  * `commerce.orderstatusnotification` – stores binding records indicating which users are configured to receive [internal notifications](/documentation/developers-and-admins/digital-commerce-setup/configure-order-statuses#set-internal-notification-content) about order status changes.


### Fixed issues
  * API – In certain cases, the `ContextUtils.PropagateCurrent` method didn’t correctly [pass context values](/documentation/developers-and-admins/customization/integrate-custom-code#database-connection-issues-when-scheduling-parallel-or-asynchronous-work-from-external-libraries) to tasks managed in custom code, which could lead to exceptions when awaiting tasks using `Task.WhenAll`.
  * Admin UI
    * Navigating to a different page or menu item in the administration interface before the current page was finished loading could result in an error, particularly on slower connections.
    * Rendering of [Admin UI form components](/documentation/developers-and-admins/customization/extend-the-administration-interface/ui-form-components) in read-only mode was improved by fixing minor visual issues. 
  * Channels – A newly created channel (website, email, headless) could, in some cases, not be visible as an application on the administration dashboard due to being incorrectly cached.
  * Email Builder
    * If an [Email Builder section](/documentation/developers-and-admins/development/builders/email-builder/develop-email-builder-components#sections) contained multiple widget zones, an error (“The connection does not support MultipleActiveResultSets.” exception) could occur if a widget that loaded content from the database was placed into two or more of the zones. The error prevented the system from sending such emails, and users could not view the email in _Preview_ mode.
    * When developing [Email Builder sections](/documentation/developers-and-admins/development/builders/email-builder/develop-email-builder-components#sections) with [MJML components](/documentation/developers-and-admins/development/builders/email-builder/develop-email-builder-components#mjml-markup), widget zones placed within `<mj-column>` elements were not correctly placed within the resulting HTML table element in the Email Builder view mode. This could cause issues with component positioning or styling in certain cases. The issue did not affect content in _Preview_ mode or the actual emails sent to recipients.
    * When using MJML markup for Email Builder components, CSS classes assigned to [MJML components](/documentation/developers-and-admins/development/builders/email-builder/develop-email-builder-components#mjml-markup) (via the `css-class` attribute) weren’t rendered for the resulting HTML elements in the Email Builder view mode. This could cause components to appear without styling. The issue did not affect content in _Preview_ mode or the actual emails sent to recipients. 
  * Forms – If a [form](/documentation/business-users/digital-marketing/forms/create-and-edit-forms) contained fields with long _Label_ text, the listing header in the [Submissions](/documentation/business-users/digital-marketing/forms/manage-form-submissions) view mode overlapped the first row of the displayed form submissions. After applying the update, long label text values are truncated in the listing header.
  * Rich text editor – An error occurred when inserting an asset using the _Insert image_ and _Insert link_ dialogs in the [rich text editor](/documentation/business-users/rich-text-editor) if no asset was selected to be inserted.
  * SaaS – The daily cap for the [Microsoft Application Insights integration](/documentation/developers-and-admins/configuration/saas-configuration#enable-microsoft-application-insights-integration) was modified to prevent a situation where a persisting issue could prevent the project from logging any new entries after the cap is reached.
  * Xperience Portal – Attempting to [pre-validate](/documentation/developers-and-admins/configuration/website-channel-management#add-website-channel-domains-with-minimal-downtime) an invalid domain name caused minor inconsistencies in the system.


* * *
### Hotfix (June 5, 2025)
**version 30.5.4**
Updates and changes
**Allowed depth of field cycles in headless content**  
Xperience version 30.1.0 updated the _Hot Chocolate_ GraphQL server used for [headless channels](/documentation/developers-and-admins/configuration/headless-channel-management) to [version 14](https://chillicream.com/blog/2024/08/30/hot-chocolate-14), which introduced extra security features, such as _Maximum allowed field cycle depth_ validation. The default configuration had the cycle depth limit set to 3 and was enforced in non-development environments.
This hotfix (30.5.4) changes the default depth limit to 5, enforces the rule the same way on all environments, and provides the ability to customize the `MaxFieldCycleDepth` via `HeadlessOptions`. See [Configure the headless API](/documentation/developers-and-admins/configuration/headless-channel-management#configure-the-headless-api).
Since the validation for maximum allowed depth of field cycles is now enforced in development environments, deeply recursive GraphQL queries may now start failing with a _Maximum allowed coordinate cycle depth was exceeded_ error. Manually adjust the `MaxFieldCycleDepth` configuration if you encounter this error.
Fixed issues
  * API – Attempting to [generate models](/documentation/developers-and-admins/api/generate-code-files-for-system-objects) for objects containing fields of the _Pages and reusable content_ [data type](/documentation/developers-and-admins/customization/field-editor/data-type-management) that were hidden via the _Display in editing form_ checkbox resulted in an exception.
  * Content item API – If a [content type](/documentation/developers-and-admins/development/content-types) had a field of the _Pages and reusable content_ [data type](/documentation/developers-and-admins/customization/field-editor/data-type-management) that was hidden via the _Display in editing form_ checkbox, an exception occurred when using the [content item query](/documentation/developers-and-admins/api/content-item-api/content-item-query-api) API to retrieve items of the given type with the `WithLinkedItems()` parameterization. Using hidden fields with the Pages and reusable content data type is not supported, but the API was updated to avoid the exception when retrieving such items with existing data.
  * Email Builder – For projects running with an application base path (e.g., _domain.com/path_), an error occurred in the [Email Builder UI](/documentation/business-users/digital-marketing/emails/create-emails-in-email-builder) when attempting to add components.
  * UI form components – Using the [page selector](/documentation/developers-and-admins/customization/extend-the-administration-interface/ui-form-components/reference-admin-ui-form-components#page-selector) when creating a new content item from the [combined content selector](/documentation/developers-and-admins/customization/extend-the-administration-interface/ui-form-components/reference-admin-ui-form-components#combined-content-selector) in content hub caused the administration to freeze.


* * *
### Hotfix (June 2, 2025)
**version 30.5.3**
Fixed issues
  * Microsoft Azure – In certain cases, the application failed to start for projects deployed to Microsoft Azure that used [Azure storage](/documentation/developers-and-admins/api/files-api-and-cms-io/file-system-providers/azure-blob-storage) for their file system (including both [SaaS](/documentation/developers-and-admins/saas/saas-overview) and private cloud projects). The issue was caused by a timeout on application start that occurred if the application’s Azure temporary and cache folders contained a very large number of directories or files.


* * *
### Hotfix (May 29, 2025)
**version 30.5.2**
Fixed issues
  * Content items – An error occurred after running the _Track missing content item usages_ [scheduled task](/documentation/developers-and-admins/customization/scheduled-tasks) if it encountered a widget without properties.
  * Content synchronization – [Synchronization](/documentation/business-users/content-sync) failed if one of the selected content items had a field [linking](/documentation/business-users/content-hub/content-items#link-content-items) to another item, was in the _Unpublished_ status, and did not exist in the _Published_ status on the target instance. Synchronization of such items is not intended, and still doesn’t occur after applying the hotfix. However, the error no longer occurs and other items selected in the batch are synchronized correctly.
  * Digital commerce – The size of the _ShoppingCartData_ database column of the _Commerce_ShoppingCart_ table was increased to unlimited size for projects updated from previous versions.
  * Page permissions – Duplicate records could be created in the _CMS_WebPageAcl_ table during a project’s [update](/documentation/developers-and-admins/installation/update-xperience-by-kentico-projects) if the updated project’s database was in an inconsistent state (e.g., caused by a previously failed update) and the project’s was created with version 30.2.0 or newer. The hotfix prevents future duplicate records from occurring in new projects.
  * Xperience Portal – The _Application response_ [alert](/documentation/developers-and-admins/deployment/deploy-to-the-saas-environment/manage-saas-deployments#alerts) was not always triggered by the system. The hotfix improves the reliability of the alert.


* * *
### Hotfix (May 22, 2025)
**version 30.5.1**
Fixed issues
  * API – [Generated models](/documentation/developers-and-admins/api/generate-code-files-for-system-objects) for [reusable field schemas](/documentation/developers-and-admins/development/content-types/reusable-field-schemas) incorrectly removed periods and other special characters from schema code names. After applying the hotfix, generated `REUSABLE_FIELD_SCHEMA_NAME` variables in model classes now contain the full code name value, preventing errors when referencing these schemas in queries when using `OfReusableSchema`. Users need to regenerate their reusable field schema model classes after applying the hotfix for this change to be reflected.
  * Admin UI – Fixed an issue where an empty snackbar would occasionally and randomly appear in the Admin UI.
  * Admin UI authentication – When using the _Tab_ key to navigate through the administration sign-in page, the _Show_ button was incorrectly focused before the _Password_ field.
  * Emails – [Double opt-in links](/documentation/business-users/digital-marketing/emails/send-regular-emails-to-subscribers#add-a-subscription-form) in the content of emails with the _Form autoresponder_ purpose couldn’t be edited in some cases. When the link was selected immediately after the email content was initially loaded, the rich text editor incorrectly opened a default edit pop-up that blocked the editing panel used for this scenario.


* * *
## Refresh (May 19, 2025)
**version 30.5.0**
### Manual update steps
The update introduces an internal system [user](/documentation/developers-and-admins/configuration/users/user-management) with the _kentico-system-service_ username, which is used by the new [content synchronization](/documentation/business-users/content-sync) feature.
If your project already contains a custom user with this username, you need to remove this user before starting the [update](/documentation/developers-and-admins/installation/update-xperience-by-kentico-projects). Otherwise an error will occur, and you will need to re-run the update on an unmodified backup of your project and database.
If you have a [SaaS](/documentation/developers-and-admins/saas/saas-overview) project or use the [Continuous Deployment](/documentation/developers-and-admins/ci-cd/continuous-deployment) feature, you need to ensure that your [repository.config](/documentation/developers-and-admins/ci-cd/configure-ci-cd-repositories) files include the `cms.user` object type. Otherwise the new system user will not be deployed after you perform the update. The update also requires you to use the `CreateUpdate` [CD restore mode](/documentation/developers-and-admins/ci-cd/configure-ci-cd-repositories#configure-cd-restore-mode) (`Create` is not sufficient).
### New features
**Content synchronization**  
Content sync allows users to easily transfer content from one instance of Xperience by Kentico to another. The purpose of content sync is to help manage content when using multiple environments. For example, a project’s developers may have a local _Development_ instance, from which initial content is sent to a hosted _Staging_ instance. The staging instance is accessible only by editors and other staff, who use it to prepare content updates. The final content is then synchronized during regular releases to a public-facing _Production_ instance. For more information, see [Content sync](/documentation/business-users/content-sync) and [Content sync configuration](/documentation/developers-and-admins/configuration/content-sync-configuration).
**Staging environment for SaaS**  
[SaaS](/documentation/developers-and-admins/saas/saas-overview) projects now offer the option to add a _Staging_ [deployment environment](/documentation/developers-and-admins/deployment/deploy-to-the-saas-environment#deploy-the-package-to-another-deployment-environment), which provides a platform where editors and other staff can prepare content, and then transfer it to the production environment using the new content sync feature. Staging environments are designed to be nearly identical to production environments in most aspects, such as performance, availability, backup policies, and the option to set custom domains.
**Pre-validation of DNS records for website channel domains in SaaS**  
To help ensure a smoother transition when assigning a new main domain to your website channel, it is now possible to [pre-validate the required DNS records](/documentation/developers-and-admins/configuration/website-channel-management#add-custom-website-channel-domains). This enhancement minimizes downtime during the domain update process.
**Improved email field retrieval in Email Builder components**  
The update introduces a new API for [retrieving content from email fields](/documentation/developers-and-admins/development/builders/email-builder/develop-email-builder-components#email-fields) within Email Builder components, including support for all available field data types.
**Enhanced page URL retrieval**  
This update improves [URL retrieval](/documentation/developers-and-admins/development/content-retrieval/retrieve-page-content/retrieve-page-urls) functionality with several optimizations and new features:
  * A new `GetUrl()` extension method for models implementing `IWebPageFieldsSource` that provides a URL retrieval option that never queries the database for additional data. The interface is by default implemented by all [generated page classes](/documentation/developers-and-admins/api/generate-code-files-for-system-objects).
  * A specialized `UrlPathColumns()` method for [content item query](/documentation/developers-and-admins/api/content-item-api/content-item-query-api) that includes columns required for URL path generation. Useful when query data is restricted via [Columns()](/documentation/developers-and-admins/api/content-item-api/reference-content-item-query#columns).
  * Control over [URL language behavior](/documentation/developers-and-admins/development/content-retrieval/retrieve-page-content/retrieve-page-urls#using-the-geturl-extension-method) via the new `SetUrlLanguageBehavior` method, allowing developers to decide if URLs for fallback language pages should use the requested language path structure or the [fallback language](/documentation/developers-and-admins/configuration/languages#language-fallbacks) structure.


**Content retriever API**  
This update introduces multiple changes to the experimental [IContentRetriever](/documentation/developers-and-admins/api/content-item-api/content-retriever-api) API.
  * **Caching support** – The `IContentRetriever` API now supports result caching. Caching is enabled by default for all retrieval methods and can be configured using the `RetrievalCacheSettings` parameter. See [Implicit result caching](/documentation/developers-and-admins/api/content-item-api/content-retriever-api#implicit-result-caching).
  * **New retrieval methods using GUIDs** – The ContentRetriever API now contains two new methods that enable retrieving content directly using GUIDs: [RetrievePagesByGuids](/documentation/developers-and-admins/api/content-item-api/reference-content-retriever-api#retrieve-pages-by-guids) and [RetrieveContentByGuids](/documentation/developers-and-admins/api/content-item-api/reference-content-retriever-api#retrieve-content-items-by-guids). These methods are useful for situations when you have specific content item identifiers, such as from related items fields or selectors.
  * **Option to provide configuration defaults** – [Parameter classes](/documentation/developers-and-admins/api/content-item-api/content-retriever-api#configurable-retrieval-behavior) within the ContentRetriever API, such as `RetrieveAllPagesParameters` now include a static `Default` property. This property provides a default instance of the specific parameter type when no custom configuration is needed. A similar `Default` property was added to [query parameterization](/documentation/developers-and-admins/api/content-item-api/content-retriever-api#optional-query-configuration) classes. In this context, `Default` returns a null action, representing no additional configuration. Useful when a configuration action is syntactically required but no additional configuration is necessary (for instance, when using certain advanced overloads of the available methods).
  * **Obtain used cache dependencies** – The API now includes `CacheDependencyCollector` for [cache dependency](/documentation/developers-and-admins/development/caching/cache-dependencies) management. This class automatically collects dependencies for items retrieved via `IContentRetriever` calls within a scoped context. Access collected dependencies using `GetCacheDependency()` for broader caching strategies, like caching multiple retrieval calls under a single section. See [Implicit result caching](/documentation/developers-and-admins/api/content-item-api/content-retriever-api#implicit-result-caching).
  * **Web page data for linked items included by default in results** – The API now defaults to including full web page data, such as URL paths, for linked content items retrieved via content queries by internally using the [WithWebPageData](/documentation/developers-and-admins/api/content-item-api/reference-content-item-query#withwebpagedata) parameterization.
  * **URL retrieval behavior** – The `IContentRetriever` API now also provides the URL enhancements introduced by [Enhanced page URL retrieval](#EnhancedPageURLRetrieval). See [URL retrieval behavior](/documentation/developers-and-admins/api/content-item-api/content-retriever-api#url-retrieval-behavior).


**Page usages**  
You can now see all the [usages](/documentation/business-users/website-content/track-page-usages) of your website channel pages using the new page usage listing tab. This feature enables you to confidently manage, delete, and edit your pages, knowing exactly what other content you may influence.
**Scheduled recalculation of customer journey results**  
You can now [schedule recalculation](/documentation/business-users/digital-marketing/customer-journeys#CJScheduledRecalculation) of a customer journey to keep the journey results automatically up to date.
### Updates and changes
**Former URLs application replaced by a new URLs application**  
The Refresh introduces a new [URLs](/documentation/business-users/website-content/manage-page-urls) application that replaces the _Former URLs_ application. The new application includes a _Former URLs_ tab, preserving the original functionality, and adds a _Vanity URLs_ tab that provides an overview of [vanity URLs](/documentation/business-users/website-content/manage-page-urls#manage-vanity-urls-of-pages). Roles with permissions for the _Former URLs_ application have the same permissions for the _URLs_ application. After applying the update, all [custom pages](/documentation/developers-and-admins/customization/extend-the-administration-interface/ui-pages) registered under `FormerUrlsApp` will be registered under the new _URLs_ application. The URL slug for the new application is `urls`, with the _Former URLs_ tab available under `urls/former-urls`.
**Read-only mode**  
The Refresh introduces read-only mode for [Admin UI form components](/documentation/developers-and-admins/customization/extend-the-administration-interface/ui-form-components), allowing the components to be displayed as editable, disabled, or read-only. The system now uses read-only mode when users view previously published versions in the [version history](/documentation/business-users/content-versioning#version-history). [Custom UI form components](/documentation/developers-and-admins/customization/extend-the-administration-interface/ui-form-components#create-custom-form-components) should implement read-only mode support using [custom logic](/documentation/developers-and-admins/customization/extend-the-administration-interface/ui-form-components#form-component-front-end).
**LinkedItemsDependencyRetriever API**  
The [ILinkedItemsDependencyRetriever](https://api-reference.kentico.com/api/CMS.ContentEngine.ILinkedItemsDependencyAsyncRetriever.html) API newly builds and returns [dependencies](/documentation/developers-and-admins/development/caching/cache-dependencies) on [reusable field schemas](/documentation/developers-and-admins/development/content-types/reusable-field-schemas) if any items for which dependencies are requested use them.
**Content item usages**  
The [content item usages](/documentation/business-users/content-hub/content-items#track-usage-of-content-items) tab has been enhanced to display usages in a hierarchical structure, sharing the design with the new [page usages](/documentation/business-users/website-content/track-page-usages) feature.
**AIRA Companion app upgrade available**  
A new version of the AIRA Companion app integration is available. The app is still available only as a preview. Users can now select from pre-made prompts when using the chatbot. Mock messages can now be used when integrating and testing the app. See the [Xperience by Kentico Aira Unified](https://github.com/Kentico/xperience-by-kentico-aira-unified) GitHub repository for more details.
**Form context in validation rules**  
A `FormContext` property was added to the `ValidationRule` base class. This enables developers to evaluate [validation rules](/documentation/developers-and-admins/customization/extend-the-administration-interface/ui-form-components/ui-form-component-validation-rules) based on the context where the UI form is displayed. For example, you can limit a validation rule to be used only in the UI forms of content items.
### Feedback wanted: Content retriever API
In Refresh 30.4.0, we released the [experimental Content Retriever API](/documentation/developers-and-admins/api/content-item-api/content-retriever-api) and are looking for your input regarding its usage. This API will remain experimental until the next Refresh release to allow for further refinement based on your feedback.
Help us by sharing your experience:
  * What aspects are working well for you?
  * What issues or difficulties have you experienced?
  * What feature or retrieval functionality is missing?


Submit your feedback via our roadmap portal: <https://roadmap.kentico.com/>
### Newly obsolete API
**Command events in UI page extenders**  
The option to handle UI page command events in [UI page extenders](/documentation/developers-and-admins/customization/extend-the-administration-interface/ui-pages/ui-page-extenders) was deprecated. The related `PageExtender<TPage>.CommandExecute` property and `PageCommandEventArgs` class are now obsolete. This customization pattern was difficult to use without internal knowledge of [UI page commands](/documentation/developers-and-admins/customization/extend-the-administration-interface/ui-pages/ui-page-commands), and will be removed without a direct replacement. However, endpoints for relevant customization scenarios may be added to individual UI pages in the future.
**Deprecated properties in @kentico/xperience-admin-components package**  
The `disabled` property in [FormItemWrapper](/documentation/developers-and-admins/customization/extend-the-administration-interface/ui-form-components#add-support-for-form-field-metadata), which is used to wrap [React front-end input components](/documentation/developers-and-admins/customization/extend-the-administration-interface/ui-form-components/reference-react-input-components) when creating [custom UI form components](/documentation/developers-and-admins/customization/extend-the-administration-interface/ui-form-components) to ensure they work correctly on [UI pages with forms](/documentation/developers-and-admins/customization/extend-the-administration-interface/ui-pages/ui-pages-with-forms), was made deprecated and replaced by `editMode`. The `disabled` property is backward compatible but will be removed in future versions.
**EmailContext.EmailFields for Email Builder components**  
The `EmailContext.EmailFields` property, which allows retrieval of email field values within [Email Builder components](/documentation/developers-and-admins/development/builders/email-builder/develop-email-builder-components), is now obsolete. Instead, use the new `EmailContext.GetEmail<T>` method together with [generated classes](/documentation/developers-and-admins/api/generate-code-files-for-system-objects) for email content types. See [Email fields](/documentation/developers-and-admins/development/builders/email-builder/develop-email-builder-components#email-fields).
### Fixed issues
  * Content hub – An error occurred when deleting [content items](/documentation/business-users/content-hub/content-items) while using the Content hub grid view (gallery showing thumbnail images).
  * Content types – If a [content type](/documentation/developers-and-admins/development/content-types) or [reusable field schema](/documentation/developers-and-admins/development/content-types/reusable-field-schemas) field had the _Maximum number of items_ or _Minimum number of items_ visibility condition assigned, the field was not displayed on the initial load of the resulting editing form, even if the condition was fulfilled.
  * Customer journeys – The _Conditions_ field of a stage in a [customer journey](/documentation/business-users/digital-marketing/customer-journeys) was incorrectly set as optional.
  * Digital commerce
    * Certain properties of the `CustomerInfo` class incorrectly allowed _null_ values and are now set as required.
    * The size of the `ShoppingCartData` property of the [ShoppingCartInfo class](/documentation/developers-and-admins/digital-commerce-setup/checkout-process) was incorrectly limited in size. The size of the corresponding database column is now not unlimited. For projects updated from previous versions, this issue is addressed in the hotfix version 30.5.2. 
  * Notifications – When a [notification placeholder](/documentation/developers-and-admins/configuration/notifications#add-url-placeholders) was added into the URL of a link using the _Insert link > Web URL_ rich text editor option, the value was encoded incorrectly and the placeholder wasn’t resolved in the resulting notification emails. The issue occurred when adding a placeholder to a notification link URL after updating to version 30.4.0 or newer. To fix existing occurrences of the issue, you need to re-save the links in the affected notifications.
  * Project templates – Several issues related to [digital commerce](/documentation/developers-and-admins/digital-commerce-setup) were fixed in the _Dancing Goat_ sample project template.


* * *
### Hotfix (May 7, 2025)
**version 30.4.2**
New features
  * **Constant classes for file endpoints**
The hotfix introduces `MediaLibraryConstants` and `ContentItemAssetConstants` classes containing constants with URL path prefixes of endpoints that the system uses to serve files. These classes are intended to be used when implementing [custom middleware](/documentation/developers-and-admins/configuration/content-hub-configuration#override-csp-when-displaying-content-through-iframe) that sets custom [Content Security Policy](https://developer.mozilla.org/en-US/docs/Web/HTTP/Guides/CSP) for files, for example, when displaying videos using `iframe` tags.


Fixed issues
  * Email Builder – If the code of an [Email Builder widget](/documentation/developers-and-admins/development/builders/email-builder/develop-email-builder-components#widgets) contained an override of the `OnInitializedAsync` Razor component method, a “Context is not in a valid state” error could occur in certain cases when rendering an email with the widget. The error prevented the system from sending such emails, and users could not view the email in Preview mode.
  * Languages – When attempting to edit an untranslated page from a _Combined content selector_ UI form component, the user was incorrectly redirected to the [fallback version](/documentation/developers-and-admins/configuration/languages#language-fallbacks) of the page, instead of the admin UI page for creating a new language variant.


* * *
### Hotfix (April 30, 2025)
**version 30.4.1**
Fixed issues
  * General – The hotfix updates several internal third-party dependencies of client admin UI scripts to newer versions that address vulnerabilities.


* * *
### Email Builder update (April 24, 2025)
**NuGet packages not released for this update**
Email Builder Starter Kit release
The Email Builder Starter Kit provides a basic set of [components](/documentation/developers-and-admins/development/builders/email-builder/develop-email-builder-components), which speed up initial usage of [Email Builder](/documentation/developers-and-admins/development/builders/email-builder) functionality. The Starter Kit components allow users to easily create the most common types of email content. Developers can adjust or extend the components according to specific project requirements, or use them as examples when implementing custom components.
The Starter Kit is available in the [Xperience by Kentico Email Builder Starter Kit](https://github.com/Kentico/xperience-by-kentico-email-builder-starter-kit) GitHub repository. See the repository’s _README_ file for detailed information and instructions.
* * *
## Refresh (April 17, 2025)
**version 30.4.0**
### Manual update steps
When updating from versions older than [version 29.3.0](/documentation/changelog#refresh-july-25-2024), which introduced the [usage tracking feature](/documentation/business-users/content-hub/content-items#track-usage-of-content-items), use the new _Track missing content item usages_ [scheduled task](/documentation/developers-and-admins/customization/scheduled-tasks) to track missing content item usages. The tracking of existing references is no longer done automatically during the update.
### New features
**New content retrieval APIs**  
This update introduces a new, experimental `IContentRetriever` API designed to provide a unified approach for fetching content within Xperience web applications. This service abstracts common [content query](/documentation/developers-and-admins/api/content-item-api/content-item-query-api) patterns to simplify the retrieval of pages, reusable content items, and items using [reusable field schemas](/documentation/developers-and-admins/development/content-types/reusable-field-schemas). See [ContentRetriever API](/documentation/developers-and-admins/api/content-item-api/content-retriever-api) for a detailed overview of the API.
Key features:
  * _Simplified retrieval for common scenarios_ – The API handles general retrieval cases (current page, all pages/items of a specific type, etc.) with optimized defaults for query construction, language fallback handling, and linked item retrieval, making basic fetching straightforward.
  * _Configurable behavior_ – Optional parameter objects allow developers to easily override default behaviors for specific needs, such as forcing language variants, enabling preview mode, including linked items, or filtering by page path.
  * _Query customization_ – In more complex retrieval scenarios, you can directly access the underlying [content query builder](/documentation/developers-and-admins/api/content-item-api/content-item-query-api#build-queries), enabling custom filtering, sorting, projections, and paging.
  * _Flexible model mapping_ – You can override the [default model mapping process](/documentation/developers-and-admins/api/content-item-api/content-item-query-api#run-queries-and-map-the-result), transforming the raw retrieved data (`IContentQueryDataContainer`) into custom view models or applying specific logic beyond the default property mapping.


**Enhanced CI/CD repository configuration**  
This release introduces a new version (v2) for _repository.config_ files used by the [Continuous Integration & Continuous Deployment](/documentation/developers-and-admins/ci-cd) (CI/CD) feature. The new version brings enhanced control over repository content, particularly for content items. You can now precisely control which content items are included or excluded from the CI/CD repository using dedicated configuration elements:
  * _Type-based filtering_ – Use the new `<IncludedContentItemsOfType>` and `<ExcludedContentItemsOfType>` elements to manage content items based on their [content type](/documentation/developers-and-admins/development/content-types).
  * _Name-based filtering_ – Use the new `<ContentItemFilters>` element with `<IncludedContentItemNames>` and `<ExcludedContentItemNames>` to filter individual content items by their code name patterns, including wildcard (%) support. This filtering applies _after_ type-based filtering.


_repository.config_ files generated via [kxp-ci-store](/documentation/developers-and-admins/ci-cd/continuous-integration#store-object-data-to-the-repository) or [kxp-cd-config](/documentation/developers-and-admins/ci-cd/continuous-deployment#generate-cd-configuration-files) now use this new format by default.
The existing _repository.config_ format remains backward-compatible. However, migrating existing _repository.config_ files to the v2 format is _required_ if you wish to leverage the new advanced filtering capabilities described above, benefit from improved CD restore performance (particularly in the `CreateUpdate` mode), or ensure compatibility with potential future updates that rely on the v2 structure. If you choose to migrate, follow the steps outlined in [Migrate CI/CD repository.config to v2](/documentation/developers-and-admins/ci-cd/configure-ci-cd-repositories/config-v2-migration).
Additionally, the product documentation now contains sample repository configurations that address common CI/CD scenarios. These are suitable as a starter that can be adapted to project-specific workflows. See [Repository configuration templates](/documentation/developers-and-admins/ci-cd/configure-ci-cd-repositories/repository-configuration-templates).
**Cache dependency support for reusable field schemas**  
You can now configure cache dependencies that are tied directly to [reusable field schemas](/documentation/developers-and-admins/development/content-types/reusable-field-schemas) and their usage within content items or pages. When a schema or any item using a targeted schema is modified, created, or deleted, corresponding cache entries associated with this dependency are automatically invalidated. See the following pages for details:
  * [Cache dependencies – reusable field schemas](/documentation/developers-and-admins/development/caching/cache-dependencies#reusable-field-schemas)
  * [Cache dependencies – pages reusable field schema usage](/documentation/developers-and-admins/development/caching/cache-dependencies#pages-reusableFieldSchemaUsage)
  * [Cache dependencies – content items reusable field schema usage](/documentation/developers-and-admins/development/caching/cache-dependencies#contentItems-reusableFieldSchemaUsage)
  * [Reusable field schemas cache dependencies reference](/documentation/developers-and-admins/development/caching/cache-dependencies-reference#reusable-field-schemas)


**Request context-aware CacheDependencyBuilder methods**  
To streamline [cache dependency](/documentation/developers-and-admins/development/caching/cache-dependencies) configuration in web applications, especially in multichannel and multilingual environments, several new _context-aware extension methods_ have been added to the `CacheDependencyBuilder` API. These methods automatically use channel and/or language information derived from the current HTTP request, reducing the need for manual context retrieval and parameter passing.
These enhancements simplify development and help ensure cache invalidation keys accurately reflect the specific context (channel, language) of the data being cached. Primary benefits:
  * _Automatic context resolution_ – No need to manually fetch `IWebsiteChannelContext` or `IPreferredLanguageRetriever` in many common dependency scenarios.
  * _Improved accuracy_ – Ensures dependencies are correctly scoped to the relevant channel and/or language variant presented to the user.


These extension methods are available in the `Kentico.Content.Web.Mvc` namespace when building cache dependencies with `ForContentItems` or `ForWebPageItems`.
**Async object events**  
The update introduces a new event handling approach for the most common object events – _Insert_ , _Update_ and _Delete_ (triggered when an object in the system is created, updated or deleted). The new object event handlers allow developers to use both synchronous and asynchronous code, and support standard constructor dependency injection. See [Handle object events](/documentation/developers-and-admins/customization/handle-global-events/handle-object-events).
**Digital commerce developer preview**  
The [digital commerce](/documentation/developers-and-admins/digital-commerce-setup) feature is released as a preview for developers. In this release, you can explore the commerce implementation in the [Dancing Goat](/documentation/developers-and-admins/installation#available-project-templates) sample site, which currently contains a [product catalog](/documentation/developers-and-admins/digital-commerce-setup/model-product-catalog) and a sample handling of the [shopping cart](/documentation/developers-and-admins/digital-commerce-setup/checkout-process). After completing a purchase on the website, you can view the resulting _Customers_ and _Orders_ in their respective applications.
**Customizable system email sender and service domains in SaaS deployments**  
You can now [customize](/documentation/developers-and-admins/configuration/email-configuration#configurable-sender-email-addresses) the sender address for system emails and the email service domain in projects deployed to the SaaS environment in [Xperience Portal](/documentation/developers-and-admins/saas/xperience-portal).
**Image dimension limits**  
You can now set a limit to the maximum width or height of an image asset when [automatic image conversion](/documentation/developers-and-admins/development/content-types#automatically-optimize-image-assets) is enabled for its content type. Images exceeding the set limits are automatically resized to fit upon upload.
**Copy asset URLs**  
To use [content item assets](/documentation/business-users/content-hub/content-item-assets) stored in Xperience across different platforms (such as social media), you can now [copy the URL](/documentation/business-users/content-hub/content-item-assets#copy-an-asset-url) of a published asset or image variant.
**Improvements for customer journeys**  
Several visual improvements were made to [customer journeys](/documentation/business-users/digital-marketing/customer-journeys), which now also allow you to easily view which contacts [converted or dropped off](/documentation/business-users/digital-marketing/customer-journeys#view-converted-and-dropped-off-contacts) at a given stage.
**Expanded email personalization**  
The update improves the personalization options for [email](/documentation/business-users/digital-marketing/emails) content. In older versions, [dynamic text](/documentation/business-users/digital-marketing/emails#personalize-emails-with-dynamic-text) could only be added into rich text fields. Now users can insert personalization placeholders into all types of text fields. These placeholders represent attributes of the email’s recipient, such as their name or email address. When the email is sent, the placeholders are replaced by appropriate values for the given recipient.
### Updates and changes
**Customer journeys**  
When a [customer journey](/documentation/business-users/digital-marketing/customer-journeys) starts recalculating, the journey results are temporarily cleared until the recalculation finishes to prevent inconsistencies in the UI.
**Froala editor update**  
The [Froala WYSIWYG editor](https://froala.com/wysiwyg-editor) that provides the [Rich text editor](/documentation/business-users/rich-text-editor) in Xperience was updated to version 4.5.1. See Froala Editor [4.5.1](https://froala.com/blog/editor/new-releases/froala-editor-v4-5-1-enhanced-filestack-styling-non-editable-content-and-more/) for details.
**Improved image zooming in the Adjust the image dialog**  
You can now control the zoom level of an edited [image](/documentation/business-users/content-hub/content-item-assets) in the _Adjust the image_ dialog using the mouse wheel or touchpad pinch gesture.
**Page Builder data visible in version history**  
[Page Builder](/documentation/developers-and-admins/development/builders/page-builder) data is now visible when viewing a previously published version of a page in the [version history](/documentation/business-users/content-versioning#version-history).
**WorkspaceEvaluatePermission attribute made public**  
The `WorkspaceEvaluatePermission` attribute was made public. This attribute allows users to place [UI pages](/documentation/developers-and-admins/customization/extend-the-administration-interface/ui-pages) in workspace-scoped applications behind permissions.
### Newly obsolete API
**Application.PostStart event**  
The `Application.PostStart` [global event](/documentation/developers-and-admins/customization/handle-global-events/reference-global-system-events) is now obsolete. This approach is no longer recommended for running post application startup actions.
**Disabled property in form components replaced by EditMode property**  
The `Disabled` properties below were made obsolete and are now replaced by the `EditMode` property:
  * `IFormComponentClientPropertie.Disabled`
  * `IFormComponentProperties.Disabled`
  * `FormComponentProperties.Disabled`
  * `FormComponentClientProperties.Disabled`
  * `FormComponentAttribute.Disabled`
  * `EditTemplateClientProperties.Disabled`
  * `EditConfiguration.Disabled`


**Rich text editor configuration classes**  
The following classes representing [rich text editor configurations](/documentation/developers-and-admins/configuration/rich-text-editor-configuration) are not intended for public use and were made obsolete:
  * `RichTextEditorStructuredContentConfiguration`
  * `RichTextEditorHeadlessStructuredContentConfiguration`
  * `RichTextEditorWebsitesStructuredContentConfiguration`
  * `RichTextEditorContactNotesConfiguration`
  * `RichTextEditorConsentTextConfiguration`


To access the identifiers of the system’s rich text editor configurations (for example when implementing [custom replacement of the default editor configuration](/documentation/developers-and-admins/configuration/rich-text-editor-configuration/rich-text-editor-customization#replace-the-default-editor-configuration)), use the constants available in the new `RichTextEditorConfigurationsIdentifiers` class (`Kentico.Xperience.Admin.Base.Forms` namespace).
### Fixed issues
  * Admin UI – If the selection dialog was opened for a field using the _Combined content selector_ form component while one or more items were already selected, the dialog performed unnecessary loading, which could lower the performance of the admin UI in the browser.
  * Admin UI customization – Using the `UIPermission` attribute in [UI page extenders](/documentation/developers-and-admins/customization/extend-the-administration-interface/ui-pages/ui-page-extenders) to [add required permissions](/documentation/developers-and-admins/customization/extend-the-administration-interface/ui-pages/ui-page-permission-checks#ui-permission-model-and-page-extenders) didn’t work if the extended page was deeper than the first level of the given application’s UI page tree.
  * Assets – Upload of large files (hundreds of MB) to [content item asset](/documentation/business-users/content-hub/content-item-assets) fields could fail in certain cases (with a `SystemOutOfMemoryException`).
  * Azure storage – When using [Azure storage](/documentation/developers-and-admins/api/files-api-and-cms-io/file-system-providers/azure-blob-storage), the system inefficiently processed files when cleaning Azure temporary and cache folders. When a very large number of files were present, this process could lead to a timeout, which caused the application to become unresponsive.
  * CI/CD – Records in the _CMS_WebPageAclMapping_ table were not restored properly in the `Create` [CD restore mode](/documentation/developers-and-admins/ci-cd/configure-ci-cd-repositories#configure-cd-restore-mode), potentially resulting in missing [page permissions](/documentation/developers-and-admins/configuration/users/role-management/page-permission-management). After updating, any missing records are restored.
  * Content items – When a page containing [Page Builder](/documentation/developers-and-admins/development/builders/page-builder) data was cloned, references to [content items](/documentation/business-users/content-hub/content-items) from the Page Builder were not properly tracked until the page was saved or published from the _Page Builder_ tab. As a result, these content items did not appear on the [Usage tab](/documentation/business-users/content-hub/content-items#track-usage-of-content-items), and they were not available for [cascade publishing](/documentation/business-users/content-hub/content-items#cascade-publishing) if they were still in the _Draft_ step.
  * Customer journeys – The displayed time of the latest [customer journey](/documentation/business-users/digital-marketing/customer-journeys) calculation didn’t automatically update after a journey [recalculation](/documentation/business-users/digital-marketing/customer-journeys#recalculate-a-customer-journey) finished.
  * Email Builder – Relative URLs weren’t resolved correctly when working with emails in the [Email Builder UI](/documentation/business-users/digital-marketing/emails/create-emails-in-email-builder). For example, this prevented images from being displayed within the content of Email Builder widgets. The URLs were resolved correctly when the email was sent to recipients or opened in _Preview_ mode.
  * Pages – The content tree of [website channels](/documentation/business-users/website-content) didn’t correctly display:
    * the status of pages in a [custom workflow step](/documentation/developers-and-admins/configuration/workflows) or pages scheduled for [publishing](/documentation/business-users/website-content/edit-and-publish-pages#scheduled-publishing) or [unpublishing](/documentation/business-users/website-content/edit-and-publish-pages#unpublish-pages)
    * the _Not translated_ status of folders
  * SaaS deployment – When [deploying a package](/documentation/developers-and-admins/deployment/deploy-to-the-saas-environment#deploy-the-package-to-another-deployment-environment) to any new environment, the file system of the environment’s App Service is now cleared to improve the reliability of the deployment process.
  * Users
    * Unlock links contained in [account lockout](/documentation/developers-and-admins/configuration/users/user-management#account-lockout) notification emails were not correctly invalidated after they were used to unlock a user account.
    * When a [user](/documentation/developers-and-admins/configuration/users/user-management) signed in to the administration, the `LastLogon` value wasn’t updated for the user. This value can only be viewed in the _CMS_User_ database table or accessed via `UserInfo.LastLogon` in the API.
    * When [inviting](/documentation/developers-and-admins/configuration/users/user-management#invite-users) new administration users, a validation error occurred if the new user’s email address contained special characters, for example ‘+’. After applying the update, the invitation is sent correctly and any unsupported characters are replaced in the resulting user name. 


* * *
### Hotfix (April 10, 2025)
**version 30.3.3**
Fixed issues
  * Email Builder – Using methods of the `ILinkedItemsDependencyRetrieverin` interface in [Email Builder components](/documentation/developers-and-admins/development/builders/email-builder/develop-email-builder-components) led to components not rendering and the application becoming unresponsive.


* * *
### Hotfix (April 3, 2025)
**version 30.3.2**
Fixed issues
  * API – An error occurred for [content item queries](/documentation/developers-and-admins/api/content-item-api/content-item-query-api) using both the `InLanguage` and `InWorkpaces` filters when a language with more than one [fallback language](/documentation/developers-and-admins/configuration/languages#language-fallbacks) was specified as a parameter of the `InLanguage` filter.
  * Admin UI – The “secured” icon was not displayed correctly for [secured pages](/documentation/business-users/website-content/secure-pages) in a website channel’s content tree.
  * Pages – A dialog notifying about unsaved changes was incorrectly displayed when changing the workflow step of a page containing an image in a [rich text field](/documentation/business-users/rich-text-editor).
  * SaaS – Successful [backups](/documentation/developers-and-admins/deployment/deploy-to-the-saas-environment/manage-saas-deployments#restore-points-and-exports) created in [Xperience Portal](/documentation/developers-and-admins/saas/xperience-portal) were not displayed in the _Backups_ application in some cases.
  * Visual builders – Errors were logged in the browser console when viewing the [Email Builder](/documentation/developers-and-admins/development/builders/email-builder) or [Page Builder](/documentation/developers-and-admins/development/builders/page-builder) interface (`page-builder.css` not found).


* * *
### Hotfix (March 27, 2025)
**version 30.3.1**
Fixed issues – security
  * [Security advisory 2025-03-27](/documentation/security-advisories/security-advisory-2025-03-27) – information about security issues fixed within this release.


Fixed issues
  * Content versioning 
    * If [content versioning](/documentation/developers-and-admins/configuration/content-versioning-configuration) was enabled and a content item was scheduled for unpublish at the time, an error was incorrectly displayed when viewing the [version history](/documentation/business-users/content-versioning#version-history) of the content item.
    * When [restoring a version](/documentation/business-users/content-versioning) with an empty non-required field, the field was populated with the value from the current version in certain cases. 
  * Pages – An error occurred when updating a page with a _Combined content selector_ field referencing a deleted content item.


* * *
## Refresh (March 24, 2025)
**version 30.3.0**
### Kentico documentation chatbot
A new Kentico documentation chatbot is now available. This intelligent assistant helps you quickly find answers about Xperience by Kentico and easily navigate our documentation. Try it out by using the chat button on the bottom right of the screen in the Xperience documentation.
### New features
**Email Builder production release**  
The [Email Builder](/documentation/developers-and-admins/development/builders/email-builder) feature was extended to support the following:
  * The option to build, save and send [emails](/documentation/business-users/digital-marketing/emails) with the _Automation_ or _Regular_ purposes. 
    * Note: Any existing emails with an Email Builder template and another purpose (_Form autoresponder_ or _Confirmation_) are not supported. If you have created such emails in the previous developer preview version, we recommend deleting them.
  * Configurable properties for [section](/documentation/developers-and-admins/development/builders/email-builder/develop-email-builder-components#sections) and [template](/documentation/developers-and-admins/development/builders/email-builder/develop-email-builder-components#templates) components.
  * [Inline editors](/documentation/developers-and-admins/development/builders/email-builder/inline-editors-email-builder-widgets) for widget properties, including a rich text editor by default.
  * Improved [email context](/documentation/developers-and-admins/development/builders/email-builder/develop-email-builder-components#access-contextual-information-in-components), which allows developers to access the data of the current email in component code, including the fields defined by the email’s content type and the unsubscription link for [regular emails](/documentation/business-users/digital-marketing/emails/send-regular-emails-to-subscribers).
  * Basic personalization options for adding the email recipient’s attributes (_First name_ , _Last name_ or _Email_) into component content.


The Email Builder feature is now out of preview mode and the related API is no longer marked as experimental.
**Customer journeys**  
[Customer journeys](/documentation/business-users/digital-marketing/customer-journeys) allow you to track and analyze user interactions across multiple touchpoints. Marketers define customer journeys by setting up key stages with conditions for progression, and then gain insights into how contacts move through the journey.
**Content versioning**  
The system now stores previously published versions of pages, reusable content items, and headless content items when [content versioning](/documentation/business-users/content-versioning) is [enabled](/documentation/developers-and-admins/configuration/content-versioning-configuration). Users can browse the version history and restore previous versions. Version history also tracks when a page was unpublished, when it was scheduled to be published or unpublished, and when these scheduled actions were canceled.
**AIRA image processing**  
When [mass uploading](/documentation/business-users/content-hub/content-item-assets#mass-asset-upload) images, AIRA can now automatically [generate descriptions](/documentation/business-users/aira#automatically-generate-image-description) and [assign tags](/documentation/business-users/aira#automatically-assign-tags-to-images) based on the image content.
**CacheDependencyBuilder API**  
The system now provides the `CacheDependencyBuilder` API that allows you to generate [cache dependency keys](/documentation/developers-and-admins/development/caching/cache-dependencies) from retrieved data (pages, content items, media files, etc.) and use them when [caching](/documentation/developers-and-admins/development/caching/data-caching).
**Page search**  
Editors can now find their desired pages more easily in a [website channel’s content tree](/documentation/business-users/website-content) using the new search button. The resulting pages are listed in the same format as pages in the content tree.
**API for managing redirects**  
The system now provides new [API](/documentation/developers-and-admins/development/routing/custom-redirects) for managing redirects that consists of `IWebPageRedirectManager` interface with one method `CreateOrUpdateRedirect`. The API can be useful during upgrades or when adjusting redirects from deleted pages. For managing redirects from unpublished pages, use existing methods in `IWebPageManager`.
### Updates and changes
**Continuous Deployment improvements in version 30.1.0**  
The changelog for version 30.1.0 was retroactively updated to include information about an improvement made to the performance of the [Continuous Deployment](/documentation/developers-and-admins/ci-cd/continuous-deployment) restore mode. See [Continuous Deployment restore mode performance improvements](#30-1-0-cicd) for details.
**First published and Last published values accessible through generated models**  
`ContentItemCommonDataFirstPublishedWhen` and `ContentItemCommonDataLastPublishedWhen` values were added to generated models under `SystemFields` and also to the `IContentQueryDataContainer` interface.
**Temporary Azure Blob storage folders removed from SaaS backups**  
Temporary folders related to [Azure Blob storage](/documentation/developers-and-admins/api/files-api-and-cms-io/file-system-providers/azure-blob-storage) (_AzureTemp_ and _AzureCache_) are now removed from the [application backup](/documentation/developers-and-admins/deployment/deploy-to-the-saas-environment/manage-saas-deployments#restore-points-and-exports) to improve the performance of the backup process.
**Xperience Portal**  
Portal users can now view the system email service domain in the _Channel and domain_ → _System domains_ application and view DNS records for [website](/documentation/developers-and-admins/configuration/website-channel-management#view-dns-records-of-custom-domains) and [email](/documentation/developers-and-admins/digital-marketing-setup/email-channel-management) channel domains in the _Channels and Domains_ → _Channels_ application.
### New object types for CI/CD
The update introduces the following object types, which are supported by the [Continuous Integration](/documentation/developers-and-admins/ci-cd/continuous-integration) and [Continuous Deployment](/documentation/developers-and-admins/ci-cd/continuous-deployment) features. Consider updating the [repository.config](/documentation/developers-and-admins/ci-cd/configure-ci-cd-repositories) files of your CI/CD repositories, particularly when using the `<IncludedObjectTypes>` allowlist for object type filtering.
  * `om.customerjourney` – stores customer journeys.
  * `om.customerjourneystage` – stores stages of a customer journey.
  * `om.customerjourneyhistory` – stores the definitions of dynamic data classes used to calculate and persist statistics for individual customer journeys.


### Fixed issues – security
  * [Security advisory 2025-03-24](/documentation/security-advisories/security-advisory-2025-03-24) – information about security issues fixed within this release.


### Fixed issues
  * Admin UI
    * If a field with the _Integer number_ (`int`) data type used the _Maximum value_ or _Minimum value_ [validation rule](/documentation/developers-and-admins/customization/extend-the-administration-interface/ui-form-components/ui-form-component-validation-rules), a validation error was displayed when an empty value was submitted, even if the field wasn’t required and allowed empty (`null`) values.
    * If a [content folder](/documentation/business-users/content-hub/content-hub-folders#content-folders) with a very long name was selected in the Content hub listing or combined content selector, the information bar displayed when searching could be displayed with a broken layout.
    * The _Save_ button was incorrectly disabled after a validation error occurred in the [combined content selector](/documentation/developers-and-admins/customization/extend-the-administration-interface/ui-form-components/reference-admin-ui-form-components#combined-content-selector) UI form component.
    * The name of the _Log object and page actions_ [event log setting](/documentation/developers-and-admins/configuration/event-log#configure-the-event-log) and its description incorrectly stated that it enables logging of page actions.
    * When creating a new [smart folder](/documentation/business-users/content-hub/content-hub-folders#smart-folders) in the _Content hub_ application, an infinite reload occurred in the _Save as smart folder_ dialog (an error was also logged in the browser console).
    * When the [listing configuration](/documentation/business-users/content-hub#configure-content-hub-listing) panel was open in the _Content hub_ application, the left navigation menu couldn’t be used to open another application until the panel was closed. 
  * Admin UI authentication – The _Keep me signed in_ option on the sign-in page of the Xperience administration didn’t work if [Multi-factor authentication](/documentation/developers-and-admins/configuration/users/user-management#multi-factor-authentication) was enabled.
  * Admin UI customization – When creating custom admin [UI pages](/documentation/developers-and-admins/customization/extend-the-administration-interface/ui-pages/ui-pages-with-forms) that allowed users to create new objects, default values configured in [UI forms](/documentation/developers-and-admins/customization/object-types#define-editing-forms) for fields with a reference data type (e.g., _Pages_ or _Media files_) weren’t loaded correctly on the resulting UI page. As a result, default selected objects couldn’t be configured for fields with a selector form component.
  * Application startup – During startup, if the application failed to load a referenced assembly whose full file system path was longer than 100 characters, the application raised an unhandled event log-related exception `Value cannot be longer than 100 characters. (Parameter 'eventCode')` instead of logging the original issue.
  * Assets – The [image variant](/documentation/business-users/content-hub/content-item-assets#image-variants) listing incorrectly displayed the code name of variants instead of their display name.
  * CI/CD – The order of serialized [content item references](/documentation/developers-and-admins/ci-cd/reference-ci-cd-object-types#binding-object-types) was non-deterministic and could indicate changes in serialized files even if no changes were made.
  * Content hub – Mass actions in the _Content hub_ application were only applied to [content items](/documentation/business-users/content-hub/content-items) from the currently selected [content folder](/documentation/business-users/content-hub/content-hub-folders#content-folders), even if items from other folders were also selected.
  * Cookies – The cookies used for [admin UI authentication](/documentation/developers-and-admins/configuration/users/user-management) were registered with an incorrect [cookie level](/documentation/developers-and-admins/data-protection/cookies#cookie-levels), which could cause users to be signed out of the administration after setting or changing their cookie level on the live site.
  * Form Builder – If a [form’s](/documentation/business-users/digital-marketing/forms) output code included the [<fieldset>](https://developer.mozilla.org/en-US/docs/Web/HTML/Element/fieldset) HTML tag (for example added via a custom [Form Builder section](/documentation/developers-and-admins/development/builders/form-builder/form-sections)), an error occurred when attempting to select a file in a form field using the _Upload file_ [component](/documentation/developers-and-admins/development/builders/form-builder/reference-form-builder-components).
  * Headless content – After publishing previously unpublished content linked to a [headless item](/documentation/business-users/headless-content) (a [page](/documentation/business-users/website-content), [reusable content item](/documentation/business-users/content-hub/content-items), or another headless item), the cache wasn’t cleared correctly, causing the linked content to be unavailable during [headless item retrieval](/documentation/developers-and-admins/development/content-retrieval/retrieve-headless-content).
  * Pages – In certain cases, an incorrect dialog window was shown after choosing a page to redirect to when [deleting a page](/documentation/business-users/website-content/delete-pages).
  * Permissions – Users could see sections of a [website channel](/documentation/developers-and-admins/configuration/website-channel-management)’s content tree without having the _Display_ permission set for the [website’s root](/documentation/developers-and-admins/configuration/users/role-management/page-permission-management#allow-users-to-work-with-website-channel-content). Only sections [breaking inheritance](/documentation/developers-and-admins/configuration/users/role-management/page-permission-management#edit-page-permissions) and having at least the _Display_ permission were visible.
  * SaaS – The process of [creating backups](/documentation/developers-and-admins/deployment/deploy-to-the-saas-environment/manage-saas-deployments#restore-points-and-exports) could fail due to network issues, potentially affecting the deployment process if the failed backup was a _Predeployment backup_. After the fix, the process is retried if the connection is interrupted.
  * Taxonomies – In certain cases, selecting [tags](/documentation/developers-and-admins/configuration/taxonomies) for [reusable content items](/documentation/business-users/content-hub/content-items) led to a large number of requests that overloaded the server. This issue occurred in versions 30.2.0 or newer.


* * *
### Hotfix (March 6, 2025)
**version 30.2.2**
Updates and changes
This hotfix resolves the “The ALTER TABLE statement conflicted with the CHECK constraint CHECK_CMS_ContentItem_ContentItemIsReusable_ContentItemContentFolderID” issue that occurred when [updating a project](/documentation/developers-and-admins/installation/update-xperience-by-kentico-projects) to version [30.0.0](#refresh-december-12-2024) or newer if the project’s database contained reusable content items in an inconsistent state (without being assigned to a content folder or the _All content items_ root folder).
Fixed issues
  * Recipient lists – The following issues occurred on the _Overview_ page for [recipient lists](/documentation/business-users/digital-marketing/emails/send-regular-emails-to-subscribers):
    * The page load time was very long if the list contained a very large number of recipients. Applying the hotfix significantly improves the loading performance.
    * The number of _receiving_ contacts was calculated incorrectly if the list contained contacts that were _unsubscribed_ and also had the _bounced_ status.
  * Taxonomies – Filtering content items in the Content hub by [tags](/documentation/developers-and-admins/configuration/taxonomies) didn’t work in Xperience versions 30.2.0 and 30.2.1.
  * Xperience Portal – Users with service plans that allowed for additional domains could not add the correct number of domains to their [website channels](/documentation/developers-and-admins/configuration/website-channel-management#add-website-channels-in-xperience-portal).


* * *
### Hotfix (February 27, 2025)
**version 30.2.1**
Updates and changes
  * [ObjectEvents](/documentation/developers-and-admins/customization/handle-global-events/reference-global-system-events#objectevents) are now cancellable via `Cancel` in the `Before` handler.


Fixed issues
  * Assets – If a content item had multiple [asset fields](/documentation/developers-and-admins/development/content-types#add-option-to-upload-files), some containing uploaded images and some left empty, the following issues occurred:
    * Running the _Calculate dimensions of image assets_ scheduled task after updating to [version 30.0.0](#refresh-december-12-2024) failed. To fix the issue, rerun the task after applying this hotfix.
    * [Image variants](/documentation/business-users/content-hub/content-item-assets#image-variants) weren’t generated correctly after a [variant definition](/documentation/developers-and-admins/development/content-types#configure-image-variants) was added or modified. All image variants will now be generated correctly and automatically when you next add or edit an image variant definition.
  * Content types – [Creating a scope](/documentation/developers-and-admins/development/content-types/limit-the-pages-users-can-create#create-scopes-for-content-types) for a page for which a version in the default [language](/documentation/developers-and-admins/configuration/languages) did not exist resulted in an error when loading the scope’s edit page.
  * Events – [BizFormItemEvents](/documentation/developers-and-admins/customization/handle-global-events/reference-global-system-events#bizformitemevents) are now cancellable via `Cancel` in the `Before` handler without the cancellation resulting in live site errors being displayed to the users during form submission.


* * *
## Refresh (February 20, 2025)
**version 30.2.0**
### New features
**Image variants**  
Administrators can now [configure image variants](/documentation/developers-and-admins/development/content-types#configure-image-variants) with fixed dimensions, that marketers can further adjust to fit their needs. An [image variant](/documentation/business-users/content-hub/content-item-assets#image-variants) is a cropped version of the primary image, created around its focal point. If you have access to [AIRA features](/documentation/business-users/aira) in your project, AIRA can [automatically select the focal point](/documentation/business-users/aira#automatically-select-image-focal-point) in the image, automating the task for content editors.
To efficiently work with image variants in your website’s code, use the new [image Tag Helpers](/documentation/developers-and-admins/development/reference-tag-helpers).
If your project contains image assets created before updating to [version 30.0.0](#refresh-december-12-2024), ensure that you run the _Calculate dimensions of image assets_ [scheduled task](/documentation/developers-and-admins/customization/scheduled-tasks) if you haven’t already, as image variants depend on asset dimensions.
**Content type scoping for pages**  
Administrators can now control which [content types](/documentation/developers-and-admins/development/content-types) users can use to create pages in individual parts of a [website channel](/documentation/developers-and-admins/configuration/website-channel-management). The content types can be configured using a global parent/child relationship for a given content type or for specified paths (scopes) in the website channel’s content tree. For more information, see [Limit the pages users can create](/documentation/developers-and-admins/development/content-types/limit-the-pages-users-can-create).
**Notifications for form submissions**  
The system can now be configured to send [notifications](/documentation/developers-and-admins/configuration/notifications#form-submission-notifications) to administration users about [forms](/documentation/business-users/digital-marketing/forms) that visitors submit on the live site.
**Email Builder developer preview**  
Email Builder allows editors to create [emails](/documentation/business-users/digital-marketing/emails) in a user-friendly visual interface using predefined components. These components are implemented by developers as described in the [Email Builder](/documentation/developers-and-admins/development/builders/email-builder) documentation.
The Email Builder feature is released in _developer preview_ mode and is not fully functional. Currently, the feature’s main purpose is to allow technical users to familiarize themselves with the development process for Email Builder components. You can test components in the Email Builder UI, but cannot save the content and send the emails.
**Automatic DDoS detection for SaaS deployments**  
Projects deployed to the SaaS environment can now automatically detect and mitigate DDoS attacks via the [Cloudflare CDN](/documentation/developers-and-admins/configuration/saas-configuration#cloudflare-cdn). An [alert](/documentation/developers-and-admins/deployment/deploy-to-the-saas-environment/manage-saas-deployments#alerts) is also created to notify users about recent DDoS attacks.
**AIRA Companion app preview**  
The AIRA Companion app is a new integration that provides an alternative interface to Xperience by Kentico designed for mobile devices. The application includes AIRA chat, where users can ask questions about the selected non-personal data stored in the system and also pose general questions. This integration is currently available only as a preview. The complementary [blog post](https://community.kentico.com/blog/expanding-dxp-beyond-boundaries) provides more information and explains how to request access to the preview. The code is available in the [Xperience by Kentico Aira Unified](https://github.com/Kentico/xperience-by-kentico-aira-unified) GitHub repository.
**Xperience by Kentico API Reference**  
An API Reference for Xperience by Kentico is now available at <http://api-reference.kentico.com>.
### Updates and changes
**Improved DataProtection API for SaaS deployments**
In previous Xperience by Kentico versions (30.1.3 and older), users working on projects deployed in the [SaaS environment](/documentation/developers-and-admins/saas/saas-overview) encountered issues where they were signed out of the Xperience administration interface with an error after a new deployment. To resolve this issue, a new implementation of the [ASP.NET DataProtection API](https://learn.microsoft.com/en-us/aspnet/core/security/data-protection/introduction) was introduced and can be enabled by calling the `IServiceCollection.AddXperienceCloudDataProtection` method when building the web application.
The new implementation is automatically enabled in all new projects installed with version 30.2.0 or newer. When updating existing projects, you need to enable it manually. We recommend enabling this feature in all SaaS projects unless you have your own custom implementation of the ASP.NET DataProtecton API.
C#
**Enabling the DataProtection feature for SaaS projects**
Copy
```
using Kentico.Xperience.Cloud;

// ...

WebApplicationBuilder builder = WebApplication.CreateBuilder(args);

// ...

// Enables the DataProtection API in all SaaS environments
if (builder.Environment.IsQa() || builder.Environment.IsUat() || builder.Environment.IsProduction() || builder.Environment.IsEnvironment(CloudEnvironments.Custom))
{
    // ...

    builder.Services.AddXperienceCloudDataProtection(builder.Configuration);
}
```

**Cloning all language variants of a page**
When [cloning](/documentation/business-users/website-content/create-pages#clone-a-page) a page, it’s now possible to clone all [language variants](/documentation/business-users/website-content/translate-pages) of the page at once. This is now the default behavior. Cloning just one language variant is still possible, either by selecting this option in the UI or by setting the `CloneAllLanguageVariants` property of `ClonePageParameters` to false when cloning through the API.
**Assets**
  * When [manually optimizing](/documentation/business-users/content-hub/content-item-assets#optimize-image-assets) an image, the system now shows a more accurate approximate file size of the image after adjustments.
  * The image preview displayed while optimizing an image now better reflects the chosen image format and quality.


**Pages**
  * Pages scheduled to be [unpublished](/documentation/business-users/website-content/edit-and-publish-pages#unpublish-pages) can no longer be selected as a [redirect](/documentation/business-users/website-content/edit-and-publish-pages#redirects-from-unpublished-and-deleted-pages) target to prevent circular redirects.


**Xperience Portal**
  * Users can now [reset their Multi-Factor Authentication configuration](/documentation/developers-and-admins/saas/xperience-portal#reset-multi-factor-authentication) if they cannot access the device or account used for MFA.
  * The primary contact of a project is now highlighted in the [Users](/documentation/developers-and-admins/saas/xperience-portal#users) listing.


### New object types for CI/CD
The update introduces the following object types, which are supported by the [Continuous Integration](/documentation/developers-and-admins/ci-cd/continuous-integration) and [Continuous Deployment](/documentation/developers-and-admins/ci-cd/continuous-deployment) features. Consider updating the [repository.config](/documentation/developers-and-admins/ci-cd/configure-ci-cd-repositories) files of your CI/CD repositories, particularly when using the `<IncludedObjectTypes>` allowlist for object type filtering.
  * `cms.allowedchildcontenttype` – stores binding relationships representing allowed child types between content types used for pages.
  * `cms.webpagescope` – stores web page scope association between a website channel and a page.
  * `cms.webpagescopecontenttype` – stores binding relationships between web page scopes and content types.


### Newly obsolete API
With the introduction of [Email Builder](/documentation/developers-and-admins/development/builders/email-builder), the following interfaces that allow developers to work with [Page Builder](/documentation/developers-and-admins/development/builders/page-builder) component properties were made obsolete and replaced:
  * `Kentico.PageBuilder.Web.Mvc.IComponentProperties`
    * Use `IComponentProperties` from the `Kentico.Builder.Web.Mvc` namespace instead (shared for all visual builder component properties).
  * `IComponentPropertiesRetriever`
    * Use `IPageBuilderComponentPropertiesRetriever` instead (both interfaces are found in the `Kentico.PageBuilder.Web.Mvc` namespace).
  * `IPageTemplatePropertiesRetriever`
    * Use `IPageBuilderTemplatePropertiesRetriever` instead (both interfaces are found in the `Kentico.PageBuilder.Web.Mvc.PageTemplates` namespace).


### Fixed issues
  * API – Using the `IRowActionListingConfiguration.AddEditRowAction` extension method multiple times with the same `parameters` argument could lead to an unexpected exception.
  * Admin UI
    * The admin UI could become unresponsive when attempting to navigate to another application in the left navigation menu while multiple right side panel editing dialogs were open over each other.
    * The [workspace](/documentation/developers-and-admins/configuration/users/role-management/workspaces) and [language](/documentation/developers-and-admins/configuration/languages) selectors in the _Content hub_ application did not highlight the currently selected items in the drop-down. 
  * Contact management
    * If a very large number (over 100 000) was entered into the “in the last x days” parameter of a [contact group](/documentation/business-users/digital-marketing/contact-groups) or [automation](/documentation/business-users/digital-marketing/automation) condition, an error occurred. After applying the fix, the condition evaluates as if the parameter were empty in these cases.
    * The links displayed for [consents](/documentation/developers-and-admins/data-protection/consent-management) in a [contact’s](/documentation/business-users/digital-marketing/contact-management) profile within the _Contact management_ application didn’t work. After applying the fix, the links open the editing UI for the related consent.
    * The list of [consents](/documentation/developers-and-admins/data-protection/consent-management) in a contact’s profile within the _Contact management_ application incorrectly displayed consents even after they were revoked. 
  * Former URLs – [Unpublishing](/documentation/business-users/website-content/edit-and-publish-pages#unpublish-pages) or [deleting](/documentation/business-users/website-content/delete-pages) a page that was previously moved in the content tree and then moved back to the original location lead to circular redirects.
  * General – Unused vulnerable dependency packages were removed from the [Page Builder](/documentation/developers-and-admins/development/builders/page-builder) and [Form Builder](/documentation/developers-and-admins/development/builders/form-builder).
  * Page permissions – Duplicate records could be created in the _CMS_WebPageAcl_ table during a project’s [update](/documentation/developers-and-admins/installation/update-xperience-by-kentico-projects) if the updated project’s database was in an inconsistent state (e.g., caused by a previously failed update where the database wasn’t restored to its original state using a backup). This refresh prevents future duplicate records from occurring and removes existing ones. Pages that had their duplicate [page permissions](/documentation/developers-and-admins/configuration/users/role-management/page-permission-management) removed in this way can be found in the [event log](/documentation/developers-and-admins/configuration/event-log).
  * Performance – Queries executed during the initialization of the [Content hub](/documentation/business-users/content-hub) listing were optimized to improve performance.


* * *
### Hotfix (February 13, 2025)
**version 30.1.3**
Fixed issues
  * Page Builder – An error occurred when loading the properties of a [Page Builder component](/documentation/developers-and-admins/development/builders/page-builder#page-builder-components) (widget, section, etc.) with a _Combined content selector_ referencing a deleted content item.


* * *
### Hotfix (February 6, 2025)
**version 30.1.2**
Fixed issues
  * Admin UI – The design of the listing in the headless item selector was broken (column headers were displayed incorrectly).


* * *
### Hotfix (January 30, 2025)
**version 30.1.1**
Updates and changes
This hotfix resolves the “Detected package downgrade: Microsoft.Extensions.Caching.Memory from 9.0.0 to 8.0.1” build error that occurred when building .NET 9 projects with Xperience’s [30.1.0](#refresh-january-23-2025) NuGet packages installed.
Fixed issues
  * Former URLs 
    * After editing a [redirect](/documentation/business-users/website-content/edit-and-publish-pages#redirects-from-unpublished-and-deleted-pages), the cache was not cleared in specific cases, and the page’s [old URLs](/documentation/business-users/website-content/manage-page-urls#former-urls) continued redirecting to the previous target.
    * When a page’s language variant was deleted without a redirect, [former URLs](/documentation/business-users/website-content/manage-page-urls#former-urls) targeting the page that were created by editing a URL were deleted even if another language variant still existed. 
  * Forms – When a [form](/documentation/business-users/digital-marketing/forms) was added via the _Form_ Page Builder widget to a page in a right-to-left language (for example Hebrew), the form failed to load after the application was restarted.


* * *
## Refresh (January 23, 2025)
**version 30.1.0**
### Known issues
#### Detected package downgrade: Microsoft.Extensions.Caching.Memory from 9.0.0 to 8.0.1
.NET 9 projects encounter this error during build after updating to this version. A fix for the issue is on the way and will be delivered in an upcoming hotfix.
As a workaround, add the following `PackageReference` item to all _.csproj_ files that reference Xperience’s NuGet packages:
```
<PackageReference Include="Microsoft.Extensions.Caching.Memory" Version="9.0.1" />
```

**Update:** This issue was resolved in [hotfix 30.1.1](#hotfix-january-30-2025). We recommend removing the workaround if used.
### New features
**Redirects for deleted and unpublished pages**  
When [unpublishing](/documentation/business-users/website-content/edit-and-publish-pages#unpublish-pages) or [deleting](/documentation/business-users/website-content/delete-pages) a page, editors can create a [redirect](/documentation/business-users/website-content/edit-and-publish-pages#redirects-from-unpublished-and-deleted-pages) to another page. This can be used to ensure that visitors land on an alternative page and don’t get a 404 error. Redirects are listed and can be edited in the [Former URLs](/documentation/business-users/website-content/manage-page-urls) application.
Permissions for Former URLs now include the _Update_ permission, which allows changing redirect targets from unpublished and deleted pages.
**Rate limiting**  
All endpoints used as part of the Xperience administration [user management](/documentation/developers-and-admins/configuration/users/user-management) functionality, such as the sign-in or registration page, are now protected by rate limiting. This restricts the number of requests allowed within a given window of time, which provides additional security against brute-force attacks. Rate limiting is implemented using [ASP.NET Core Rate limiting middleware](https://learn.microsoft.com/en-us/aspnet/core/performance/rate-limit).
To learn about the default rate limiting policy and its configuration options, see [Rate limiting for user management endpoints](/documentation/developers-and-admins/configuration/users/administration-registration-and-authentication/administration-forms-authentication#rate-limiting-for-user-management-endpoints).
Additionally, developers can [set up rate limiting](/documentation/developers-and-admins/security-guidelines/rate-limiting) for other endpoints, based on your project’s requirements and traffic load.
**Retrieval of content from workspaces**  
Content item queries now support the [InWorkspaces](/documentation/developers-and-admins/api/content-item-api/reference-content-item-query#inworkspaces) method for parameterizing data retrieval. This method allows developers to retrieve content items from specific [workspaces](/documentation/developers-and-admins/configuration/users/role-management/workspaces), providing a consistent content experience across channels and workspaces.
**‘Set contact field value’ step for automation**  
A new step type is available for [automation](/documentation/business-users/digital-marketing/automation), which allows users to set a value for the selected contact field using an automation process.
**Refresh button for automation statistics**  
The [statistics of an automation process](/documentation/business-users/digital-marketing/automation#automation-process-analytics) can now be refreshed on demand using the Refresh button.
**Restart applications in the SaaS environment**  
If users cannot access a deployed Xperience application, they can [restart the application](/documentation/developers-and-admins/deployment/deploy-to-the-saas-environment/manage-saas-deployments#restart-deployed-applications) directly in Xperience Portal.
**Manual licensing synchronization**  
Users can now manually synchronize an Xperience instance with the Kentico license server via a new _Synchronize_ button located under _Settings_ -> _System_ -> _License_. For example, this can be useful if you want to quickly recover from an accidental license violation.
### Updates and changes
**Dynamic column width in object listing tables**  
All listing tables in the administration now scale the width of columns according to the space required for the displayed values. This allows the listings to use the available space more efficiently, avoids unnecessary scrolling, and makes it easier to read long values in most cases.
**Asynchronous Set and Delete methods for InfoProvider API**  
The system’s [InfoProvider API](/documentation/developers-and-admins/api/database-table-api) now supports asynchronously saving data to and deleting data from the database via the `SetAsync` and `DeleteAsync` methods.
**New analyzer to assist with Xperience project development**  
The _Kentico.Xperience.Core_ NuGet package now ships with an analyzer that provides a set of rules to advocate certain best practices when developing Xperience by Kentico projects. The initial version of the analyzer informs the developer about the need to override the asynchronous counterpart of a synchronous method (or vice versa) if their custom type overrides one of the `InfoProviderDecorator<TInfo>` or `AbstractInfoBase<TInfo>` methods related to data modification.
**Hot Chocolate update**  
The _Hot Chocolate_ GraphQL server used for [headless channels](/documentation/developers-and-admins/configuration/headless-channel-management) was updated to [version 14](https://chillicream.com/blog/2024/08/30/hot-chocolate-14). If you directly use `HotChocolate` packages in your project, make sure to [update your code](https://github.com/ChilliCream/graphql-platform/blob/720cb959b5fd19d7782c755e2799312b30479f96/website/src/docs/hotchocolate/v14/migrating/migrate-from-13-to-14.md) accordingly.
**Interfaces for form contexts**  
The following interfaces for [admin UI](/documentation/developers-and-admins/customization/extend-the-administration-interface/ui-form-components#form-component-back-end) form contexts were made public:
  * `IMultilingualFormContext`
  * `IWorkspaceFormContext`
  * `IContentItemFormContextBase`
  * `IContentItemFormContext`
  * `IWebPageFormContext`
  * `IEmailItemFormContext`
  * `IHeadlessItemFormContext`


It’s now possible to determine in which context the admin UI form component is used. Form context instances can be created using the following new factories:
  * `IContentItemFormContextFactory`
  * `IWebPageFormContextFactory`
  * `IEmailItemFormContextFactory`
  * `IHeadlessItemFormContextFactory`


**Continuous Deployment restore mode performance improvements**  
Continuous Deployment was updated to more quickly [restore](/documentation/developers-and-admins/ci-cd/configure-ci-cd-repositories#configure-cd-restore-mode) the serialized repository on the target machine. The most significant performance increase was recorded when using the `Create` mode. Larger speedup was also observed with the `CreateUpdate` and `Full` restore modes.
### Newly obsolete API
The following API members were marked obsolete, to be removed in one of the future releases:
  * The `FormDefinitionColumn` property of the `ObjectTypeInfo` class is no longer used by the system.
  * The following extension methods from the `ContentItemManagerSchedulingExtensions` class are now obsolete. Use identical methods of the `IContentItemManager` interface instead. 
    * SchedulePublish
    * CancelScheduledPublish
    * ScheduleUnpublish
    * CancelScheduledUnpublish
  * The following extension methods from the `WebPageManagerSchedulingExtensions` class are now obsolete. Use identical methods of the `IWebPageManager` interface instead. 
    * SchedulePublish
    * CancelScheduledPublish
    * ScheduleUnpublish
    * CancelScheduledUnpublish


### Fixed issues
  * Admin UI
    * After changing the display name of a workspace in the [Workspaces](/documentation/developers-and-admins/configuration/users/role-management/workspaces) application, the system displayed an incorrect message in the dialog notifying about the change of the code name.
    * Header action buttons on editing pages in the administration incorrectly remained active while another action was being processed after being selected. On slower connections, this allowed users to select multiple actions concurrently, for example _Save_ a new object while its creation was being cancelled.
    * If there were unsaved changes in a side panel editing dialog, selecting another action within the main page that opened another side panel dialog could cause the UI to become unresponsive. 
  * Assets – When [manually optimizing](/documentation/business-users/content-hub/content-item-assets#optimize-image-assets) an image for the first time, the system didn’t correctly display the image preview if you changed the image format multiple times in a row without saving the content item.
  * Automation – When a condition in the _Condition_ step of an [automation process](/documentation/business-users/digital-marketing/automation) was replaced, the new condition was applied but an error was logged by the system’s event log.
  * Content hub – The _Delete_ button in the confirmation dialog for the _Delete_ action for [content items](/documentation/business-users/content-hub/content-items) remained active after being selected, which could allow users on slow connections to start multiple concurrent delete actions.
  * Emails – Visiting the _Preview_ tab of an [email](/documentation/business-users/digital-marketing/emails) with links in its content resulted in an error being logged in the browser console.
  * Former URLs – When an [unpublished](/documentation/business-users/website-content/edit-and-publish-pages#unpublish-pages) page with a [fallback language variant](/documentation/developers-and-admins/configuration/languages) was moved in the content tree or its slug was changed, the corresponding [former URL](/documentation/business-users/website-content/manage-page-urls#former-urls) was not created.
  * Page Builder – Repeatedly calling the `IWebPageDataContextRetriever` used for retrieving page data of the current page could lead to an error. The issue occurred when working with pages in the [Page Builder](/documentation/developers-and-admins/development/builders/page-builder).
  * Performance
    * Improved performance when loading page data in the _Content_ view mode in website channel applications.
    * The initial data load of the [Content hub](/documentation/business-users/content-hub) listing was optimized to improve performance. 
  * Permissions – [Changing permissions](/documentation/developers-and-admins/configuration/users/role-management#assign-permissions-to-roles) for a given application and [workspace](/documentation/developers-and-admins/configuration/users/role-management/workspaces) caused the application’s permission GUIDs in the database to regenerate causing unnecessary changes in [CI/CD](/documentation/developers-and-admins/ci-cd/continuous-integration) data representing the permission configuration.
  * UI form components – When creating a new content item via the [Combined content selector](/documentation/developers-and-admins/customization/extend-the-administration-interface/ui-form-components/reference-admin-ui-form-components#combined-content-selector) UI form component, any visibility conditions assigned to the fields of the item’s [content type](/documentation/developers-and-admins/development/content-types) were not evaluated and applied.
  * Xperience Portal – Warnings and errors in the _Event log_ and _Exceptions_ applications in the [Xperience Portal](/documentation/developers-and-admins/saas/xperience-portal) were incorrectly filtered. The applications now contain all available entries.


* * *
### Hotfix (January 16, 2025)
**version 30.0.3**
Fixed issues
  * API – A `NullReferenceException` was thrown when accessing `WebsiteChannelContext` properties without `HttpContext` access.
  * CI/CD – The system incorrectly resolved page permission identifiers during the CD restore operation, resulting in an exception. The issue occurred if the tree path of a [page](/documentation/business-users/website-content) directly under the website root was changed on the source instance.
  * Smart folders – Creating a smart folder using the [smart folder selector](/documentation/developers-and-admins/customization/extend-the-administration-interface/ui-form-components/reference-admin-ui-form-components#smart-folder-selector) resulted in an error if the user didn’t have the _Create_ permission for the _Content hub_ application in the current [workspace](/documentation/developers-and-admins/configuration/users/role-management/workspaces).


* * *
### Hotfix (January 9, 2025)
**version 30.0.2**
Updates and changes
The new [MapStaticAssets](https://learn.microsoft.com/en-us/aspnet/core/release-notes/aspnetcore-9.0?view=aspnetcore-9.0#static-asset-delivery-optimization) feature introduced in .NET 9 is now supported. Adding the middleware previously prevented the [Page Builder](/documentation/developers-and-admins/development/builders/page-builder) and [Form Builder](/documentation/developers-and-admins/development/builders/form-builder) from working.
Fixed issues – security
  * [Security advisory 2025-01-09](/documentation/security-advisories/security-advisory-2025-01-09) – information about security issues fixed within this release.


Fixed issues
  * API – The XML documentation comment for the `CreateContentItemParameters()` constructor displayed the wrong text for the `name` and `displayName` parameters. The comment incorrectly stated that the `displayName` parameter can be `null`.
  * Content item API – In certain cases, the data retrieved by the [content item query](/documentation/developers-and-admins/api/content-item-api/content-item-query-api) model binding methods could contain null values in fields where a non-null value was expected. For example, this could occur when retrieving the root page of a website channel, which does not provide a value for the `WebPageItemParentID` property. After applying the hotfix, such fields return the default value for the given data type, for example 0 for numeric fields.
  * Former URLs – If a [page](/documentation/business-users/website-content) was previously opened and cached by the system and then later moved in the content tree, visiting its [former URL](/documentation/business-users/website-content/manage-page-urls#former-urls) with a specified language prefix (e.g., _~/es/products/example-product_) resulted in a redirect loop. The problem occurred if the page wasn’t translated in the language specified in the URL. After applying the hotfix, the [fallback variant](/documentation/developers-and-admins/configuration/languages#language-fallbacks) of the page is correctly displayed.
  * Forms – When a consent was selected for a _Consent agreement_ field in a [form](/documentation/business-users/digital-marketing/forms/create-and-edit-forms), an error occurred and the form’s configuration became invalid. This caused the Form Builder to display empty content and prevented further editing of the form’s fields.
  * Pages – An error occurred when creating [pages](/documentation/business-users/website-content) in a website channel if the page’s [content type](/documentation/developers-and-admins/development/content-types) contained a field with the _Pages_ data type that had a visibility condition depending on another field.
  * Workspaces – Updating the code name of the [default workspace](/documentation/developers-and-admins/configuration/users/role-management/workspaces#default-workspace) resulted in an error.


* * *
### Hotfix (December 19, 2024)
**version 30.0.1**
Fixed issues
  * Admin UI customization – The configurable content hub listing feature introduced in version 30.0.0 caused display issues for custom or extended [listing pages](/documentation/developers-and-admins/customization/extend-the-administration-interface/ui-pages/reference-ui-page-templates/listing-ui-page-template) that add multiple columns under an identical name (as specified via `AddColumn(name)`). After applying the hotfix, listings again support multiple columns registered under the same name. If the _Content hub_ listing page is extended using this approach, the columns are grouped together and cannot be reordered.
  * Languages – It was incorrectly possible to create a [language](/documentation/developers-and-admins/configuration/languages) with a code name matching an existing top-level page URL slug.
  * Role management – The _Content hub_ application could be incorrectly disabled in the page permissions tab if the page was reloaded while [assigning permissions to a role](/documentation/developers-and-admins/configuration/users/role-management#assign-permissions-to-roles) for the _Content hub_.
  * Website content – [Cloning](/documentation/business-users/website-content/create-pages#clone-a-page) of pages with names close to the maximum length (100) led to an unexpected error.
  * Workspaces
    * Creating a [workspace](/documentation/developers-and-admins/configuration/users/role-management/workspaces) with a code name identical to a code name previously used by a different workspace resulted in an error. This was caused by name collisions on [content folders](/documentation/business-users/content-hub/content-hub-folders) (including the root content folder) that didn’t properly reflect workspace code name changes.
    * [Workspaces](/documentation/developers-and-admins/configuration/users/role-management/workspaces) are now listed alphabetically in all workspace selectors. 


* * *
## Refresh (December 12, 2024)
**version 30.0.0**
### Known issues
**The ALTER TABLE statement conflicted with the CHECK constraint CHECK_CMS_ContentItem_ContentItemIsReusable_ContentItemContentFolderID**
This error could occur when updating to version 30.0.0 or newer if the project’s database contained reusable content items in an inconsistent state (without being assigned to a content folder or the _All content items_ root folder). A fix for the issue is on the way and will be delivered in an upcoming hotfix.
**Update** : This issue was resolved in [hotfix 30.2.2](#hotfix-march-6-2025).
### .NET 9 support
Xperience by Kentico supports application development using .NET 9, the latest major .NET framework release. For more information on .NET 9, see Microsoft’s [official announcement](https://devblogs.microsoft.com/dotnet/announcing-dotnet-9/).
The support also includes Kentico’s [SaaS](/documentation/developers-and-admins/api/files-api-and-cms-io/file-system-providers/azure-blob-storage) solution, which supports the deployment of projects based on .NET 9.
**Known issues**
The [MapStaticAssets](https://learn.microsoft.com/en-us/aspnet/core/release-notes/aspnetcore-9.0?view=aspnetcore-9.0#static-asset-delivery-optimization) feature is currently not supported. Adding the middleware to your application pipeline prevents [Page Builder](/documentation/developers-and-admins/development/builders/page-builder) and [Form Builder](/documentation/developers-and-admins/development/builders/form-builder) from working. As a workaround, use the existing `UseStaticFiles` middleware.
**Update** : Support for the [MapStaticAssets](https://learn.microsoft.com/en-us/aspnet/core/release-notes/aspnetcore-9.0?view=aspnetcore-9.0#static-asset-delivery-optimization) feature was introduced in [hotfix 30.0.2](#hotfix-january-9-2025).
### New features
**Workspaces**  
The [Content hub](/documentation/business-users/content-hub) application is now scoped under [workspaces](/documentation/developers-and-admins/configuration/users/role-management/workspaces). Workspaces allow you to organize your content into multiple distinct manageable units, making content easier to manage and maintain. You can [assign roles with permissions](/documentation/developers-and-admins/configuration/users/role-management#assign-permissions-to-roles) to these units to restrict or grant access to specific content based on user roles or responsibilities, enhancing collaboration by allowing teams to segregate their work and control who can view or edit certain content. Additionally, this approach mitigates security risks by ensuring that sensitive content is only accessible to authorized users, reducing the risk of data breaches or misuse.
Projects updated to version 30.0.0 and newer have all their Content hub content stored in a _Default workspace_. The roles with permissions for the Content hub application have the same permissions for Content hub in the _Default workspace_. For more information see [Default workspace](/documentation/developers-and-admins/configuration/users/role-management/workspaces#default-workspace).
The release of this feature introduces changes to related CI/CD objects. See [New object types for CI/CD](#CiCdObjects30) for possible manual steps depending on your project and repository configuration.
**Known issues** : When updating from an older version to version 30.0.0 or higher, default data, such as the [default workspace](/documentation/developers-and-admins/configuration/users/role-management/workspaces#default-workspace), is created. This happens separately for each application environment (QA, production, etc.). If you update a local instance, delete any of this default data, and then use [Continuous Deployment](/documentation/developers-and-admins/ci-cd/continuous-deployment) to transfer the object changes to another environment, the default data will not be removed (unless you use CD with `Full` [restore mode](/documentation/developers-and-admins/ci-cd/configure-ci-cd-repositories#configure-cd-restore-mode)). For example, the issue can occur when deploying to [SaaS](/documentation/developers-and-admins/saas/saas-overview) environments. If you wish to remove this default data, you need to do so manually in all your environments.
**Scheduled tasks**  
Users can now define and manage [scheduled tasks](/documentation/developers-and-admins/customization/scheduled-tasks) via the new Scheduled tasks application. Scheduled tasks automate recurring operations by defining tasks that execute at specified intervals. The feature supports task configuration, scheduling, and execution, enabling task-based automation with customizable intervals, logging, and error handling.
Developers can implement tasks by creating classes that implement the `IScheduledTask` interface and registering them under a unique identifier. In the Scheduled tasks application, they can then define task intervals, enable or disable tasks, and monitor results via the application’s _Last result_ column.
In previous versions, the system relied on scheduled tasks internally. The release of this feature introduces changes to related CI/CD objects. See [New object types for CI/CD](#CiCdObjects30) for possible manual steps depending on your project and repository configuration.
**Visibility conditions for content type fields**  
The [field editor](/documentation/developers-and-admins/customization/field-editor) for [content types](/documentation/developers-and-admins/development/content-types) and [reusable field schemas](/documentation/developers-and-admins/development/content-types/reusable-field-schemas) was extended to support visibility conditions. These conditions allow users to set up fields that are dynamically displayed or hidden, for example based on the value of another field. By default, the system provides a set of field evaluation conditions, and developers can create and register [custom visibility conditions](/documentation/developers-and-admins/customization/extend-the-administration-interface/ui-form-components/ui-form-component-visibility-conditions) to meet specific project requirements.
**Dimensions in asset metadata**  
`ContentItemAssetMetadata` was extended with new properties representing the `Width` and `Height` of the asset in pixels. The system currently supports calculating the properties only for image assets (the value is automatically `null` for all other asset types). You can access these properties when [retrieving assets](/documentation/developers-and-admins/development/content-retrieval/retrieve-content-items#retrieve-assets) or [querying the GraphQL schema](/documentation/developers-and-admins/development/content-retrieval/retrieve-headless-content#basic-querying) and use them, e.g., to prevent layout shifts when displaying content on the web.
The system automatically calculates the dimensions for new images. To calculate the dimensions for all images already in the system after applying the update, run the _Calculate dimensions_ of image assets [scheduled task](/documentation/developers-and-admins/customization/scheduled-tasks). After the task successfully completes, you can safely delete it.
**Image optimization**  
You can now [optimize image assets](/documentation/business-users/content-hub/content-item-assets#optimize-image-assets) for their use across different channels by converting the images to a more suitable format and adjusting their quality. To help ensure that editors use optimized images, administrators can [configure automatic optimization](/documentation/developers-and-admins/development/content-types#automatically-optimize-image-assets) upon image creation in the system.
To optimize any image already present in the system, [reupload](/documentation/business-users/content-hub/content-item-assets) it (if automatic optimization is configured) or optimize the image manually.
**Support for SVG and AVIF image formats**  
[Content item assets](/documentation/business-users/content-hub/content-item-assets) and [media libraries](/documentation/business-users/media-libraries/manage-media-files) now support the [SVG](https://en.wikipedia.org/wiki/SVG) and [AVIF](https://en.wikipedia.org/wiki/AVIF) formats for image files. Such images can be uploaded in the administration, selected in various image selectors, and displayed to your audience through channels.
After updating existing projects, the image extensions that you wish to use must be added manually in the _Settings → Content → Assets → Asset allowed extensions_ setting.
SVG images are not allowed by default, because they may host scripts or CSS that can pose a security risk. Carefully consider the [potential security impacts](/documentation/developers-and-admins/configuration/content-hub-configuration#support-for-svg-images) before allowing the SVG file type.
**Custom activity support for automation**  
[Automation](/documentation/business-users/digital-marketing/automation) now supports a new _Custom activity_ trigger type, which allows you to set up more flexible automation processes. Additionally, [custom activities](/documentation/developers-and-admins/digital-marketing-setup/set-up-activities/custom-activities) can be logged by a step within an automation process, which can then trigger another process.
**‘Contact field is empty’ condition for contact groups and automation**  
A new condition is available for [contact groups](/documentation/business-users/digital-marketing/contact-groups) and [automation processes](/documentation/business-users/digital-marketing/automation), which allows marketers to check if a contact field contains a value or not. Developers can set up the empty values for custom data types.
**‘Contact field has value’ condition for automation**  
The _Contact field is empty_ condition, previously only available in [contact groups](/documentation/business-users/digital-marketing/contact-groups), can now also be used in [automation processes](/documentation/business-users/digital-marketing/automation).
**Improved App Service plans of QA/UAT SaaS environments**  
The [App Service plans](https://azure.microsoft.com/en-us/pricing/details/app-service/windows/) for _QA_ and _UAT_ [deployment environments](/documentation/developers-and-admins/deployment/deploy-to-the-saas-environment#deploy-the-package-to-another-deployment-environment) were upgraded from _S1_ to _P0V3_ to improve performance.
**Xperience Portal improvements**  
The [Xperience Portal](/documentation/developers-and-admins/saas/xperience-portal) was improved in several locations:
  * Xperience Portal users can now [customize](/documentation/developers-and-admins/deployment/deploy-to-the-saas-environment/manage-saas-deployments#alerts) the types of alerts they want to receive for each environment.
  * A new [Email metrics](/documentation/developers-and-admins/deployment/deploy-to-the-saas-environment/manage-saas-deployments#email-metrics) application allows Xperience Portal users to monitor email statistics from the [SendGrid platform](/documentation/developers-and-admins/configuration/email-configuration#sendgrid-integration).


**Advanced rich text editor customization**  
New [rich text editor customization](/documentation/developers-and-admins/configuration/rich-text-editor-configuration/rich-text-editor-customization) options that allow developers to control how the system loads rich text editor configurations and to make dynamic adjustments of the configuration. For example, this approach can be used to insert custom CSS into the rich text editor.
**Language flags**  
[Languages](/documentation/developers-and-admins/configuration/languages) were extended with a flag icon selector, enabling you to set a flag icon visually representing a language. Languages with a flag icon set have their flags displayed in the _Language_ column in the [Content hub](/documentation/developers-and-admins/configuration/content-hub-configuration) , corresponding to the languages a content item is translated into.
**Clone pages**  
An option to [clone](/documentation/business-users/website-content/create-pages#clone-a-page) existing pages was added. This new option allows users to quickly create new pages with the same web page data.
**Configurable Content hub listing**  
Content Hub now supports [listing configuration](/documentation/business-users/content-hub#configure-content-hub-listing), allowing users to customize the order of columns and select which columns are displayed. This configuration is client-side only and will not affect other users.
**New object code name selector attribute**  
The [object code name selector](/documentation/developers-and-admins/customization/extend-the-administration-interface/ui-form-components/reference-admin-ui-form-components#object-selector) UI form component now has an `ObjectCodeNameSelectorComponent` attribute, which allows developers to use the selector as an [editing component](/documentation/developers-and-admins/customization/extend-the-administration-interface/ui-form-components/editing-components).
### Updates and changes
**.NET 8 is now the minimum supported .NET framework version**  
Xperience by Kentico now requires [.NET 8](https://dotnet.microsoft.com/en-us/download/dotnet/8.0) as the minimum framework version. Projects that update to version 30.0.0 or newer must target .NET 8 or [.NET 9](https://dotnet.microsoft.com/en-us/download/dotnet/9.0). The support for .NET 6 is being deprecated in alignment with Microsoft’s planned end-of-life for the version.
With this transition, support for binary serialization was removed from the system. This change primarily impacts all _Info_ classes. The removed API includes the `GetObjectData` method, the `ISerializable` interface, and the serialization constructor of each Info class: `protected (SerializationInfo info, StreamingContext context) : base(info, context)`. Additionally, assertions related to binary serialization testing were removed from the testing framework provided via the _Kentico.Xperience.Core.Tests_ NuGet package. These changes reflect Microsoft’s indirect [removal of BinaryFormatter](https://learn.microsoft.com/en-us/dotnet/core/compatibility/serialization/8.0/binaryformatter-disabled) from the framework.
**Code generator – Change to the default behavior of provider class generation**  
The [code generator](/documentation/developers-and-admins/api/generate-code-files-for-system-objects) no longer creates provider classes (_InfoProvider_) and interfaces (_IInfoProvider_) by default when generating object type classes (`--type Classes`). To include these, you must use the `--with-provider-class parameter`. This change aligns projects with our recommendation to use the `IInfoProvider<TInfo>` [API](/documentation/developers-and-admins/api/database-table-api#generic-provider-class-iinfoprovidertinfo) as a generic substitute for per-Info provider classes.
For additional details on this and other parameters, refer to the documentation or use the `--help` option in the code generator.
Recommendations:
  * **For existing projects** – Review and update any existing scripts or processes in your development pipeline that rely on the automatic generation of provider classes and interfaces. Add the `--with-provider-class True` parameter where needed.
  * **For new projects** – Use `IInfoProvider<TInfo>` where possible for cleaner and more maintainable code. Use explicit providers only when required.


**BizFormFiles moved under assets**  
The _BizFormFiles_ folder stores files uploaded via [form](/documentation/business-users/digital-marketing/forms) fields using the _Upload file_ [form component](/documentation/developers-and-admins/development/builders/form-builder/reference-form-builder-components). For new projects installed with version 30.0.0 or newer, this folder is placed under the _~/assets_ folder instead of the project root.
Applying the update to existing projects does not immediately move the folder, and the system maintains full backward compatibility for the original location. However, we strongly recommend moving the folder to the new location via the following steps after you update your project:
  1. Verify whether your project or any related infrastructure relies on the original _~/BizFormFiles_ path. Prepare any necessary adjustments to reflect the new _~/assets/BizFormFiles_ path.
  2. Manually execute the _Move BizFormFiles to the assets folder_ [scheduled task](/documentation/developers-and-admins/customization/scheduled-tasks). 
     * This task is added to your project as part of the update
     * When executed, the task moves all _BizFormFiles_ content under _~/assets/BizFormFiles_ , and then deletes the original folder.
  3. Apply any required changes from step 1.
  4. Remove any [file system mapping](/documentation/developers-and-admins/api/files-api-and-cms-io/file-system-providers) of the _~/BizFormFiles_ folder to shared storage, such as [Azure Blob storage](/documentation/developers-and-admins/api/files-api-and-cms-io/file-system-providers/azure-blob-storage) or [Amazon S3](/documentation/developers-and-admins/api/files-api-and-cms-io/file-system-providers/amazon-s3). 
     * Typically, your project will already contain mappings of the entire _~/assets_ folder, so you will not need to add new mappings.


**Database changes**
  * The following columns in the `CMS_ContentItemCommonData` database table were renamed: 
    * ContentItemCommonDataPageBuilderWidgets → ContentItemCommonDataVisualBuilderWidgets
    * ContentItemCommonDataPageTemplateConfiguration → ContentItemCommonDataVisualBuilderTemplateConfiguration
  * The `Temp_PageBuilderWidgets` table was renamed to `Temp_VisualBuilderWidgets`, including all columns.
  * The following columns were removed from the `CMS_WorkflowAction` table: 
    * ActionParameters
    * ActionResourceID
    * ActionIconClass
    * ActionThumbnailClass
    * ActionDataProviderClass
    * ActionDataProviderAssemblyName
    * ActionAllowedObjects


These changes may break any custom database queries, scripts, or procedures that use the original names.
**Change to the default behavior of CMSBuilderScriptsIncludeJQuery**  
The `CMSBuilderScriptsIncludeJQuery` setting introduced in version 29.6.0 now defaults to `false`. Users need to explicitly opt-in for jQuery usage by enabling the setting via application configuration (_appsettings.json_). See [Link jQuery as part of builder scripts](/documentation/developers-and-admins/development/builders/bundle-static-assets-of-builder-components#link-jquery-as-part-of-builder-scripts).
With this change, jQuery is considered deprecated and will be phased out completely in one of the future major releases. The library will not be updated from the current 3.5.1 version, even in the case of new vulnerability disclosures. Projects with custom components that rely on the library should consider referencing an external implementation to prevent possible issues in the future.
**Multi-factor authentication system page URLs**  
The following system page URLs used by the [multi-factor authentication](/documentation/developers-and-admins/configuration/users/user-management#multi-factor-authentication) feature were updated:
  * /admin/mfa-recovery-code → /admin/mfa/recovery-code
  * /admin/mfa-logon-recovery → /admin/mfa/logon-recovery


**CI/CD ObjectIdentificationFields settings key**  
`ObjectIdentificationFields` is a new [CI/CD settings key](/documentation/developers-and-admins/customization/object-types/object-type-configuration/enable-ci-cd-for-object-types#continuousintegrationsettings) used for object identification in a database when the `CodeNameColumn` is not available. The key was introduced to prevent duplicate records of objects without a code name column (e.g., permissions set in the [Role management](/documentation/developers-and-admins/configuration/users/role-management)).
**Generate URLs to Content hub pages**  
With the Content hub now being scoped under [workspaces](/documentation/developers-and-admins/configuration/users/role-management/workspaces), the URL structure in the _Content hub_ has been updated to include the `WorkspaceID`. When [generating URLs](/documentation/developers-and-admins/customization/extend-the-administration-interface/ui-pages#generate-urls-to-other-administration-pages) to _Content hub_ pages, make sure to specify the `WorkspaceID` in the `IPageLinkGenerator` URL parameters to avoid incorrect URLs. The `WorkspaceID` is retrieved from workspace-scoped items, for example, content items, content folders, or smart folders.
### New object types for CI/CD
The update introduces the following object types, which are supported by the [Continuous Integration](/documentation/developers-and-admins/ci-cd/continuous-integration) and [Continuous Deployment](/documentation/developers-and-admins/ci-cd/continuous-deployment) features. Consider updating the [repository.config](/documentation/developers-and-admins/ci-cd/configure-ci-cd-repositories) files of your CI/CD repositories, particularly when using the `<IncludedObjectTypes>` allowlist for object type filtering.
  * `cms.scheduledtaskconfiguration` – stores scheduled task configurations from the _Scheduled_ tasks application. 
    * This new object type automatically replaces the existing `cms.scheduledtask` object type (used internally by the system in previous versions) when serializing the repository for the first time after the update.
    * If you have `cms.scheduledtask` included in your _repository.config_ file, you **must** remove it before serializing the repository for the first time after completing the update. This primarily concerns SaaS projects as they are required to maintain an explicit allowlist of all object types in _repository.config_.
  * `cms.workspace` – stores workspaces 
    * In case you have Content hubs objects included in your _repository.config_ file, you **must** add this object type to your file’s allowlist for the serialization of the repository to not fail.
  * `cms.workspacedatapermission` – stores the workspace permission name, application identifier, workspace identifier, and role identifier.


### Breaking changes – API
**VersionStatus and ContentItemVersionStatus ‘Archived’ value now produces a compilation error**  
The obsolete `VersionStatus.Archived` and `ContentItemVersionStatus.Archived` enum values now produce a compilation _error_. This change ensures developers transition to the recommended `Unpublished` value. To restore project compatibility, replace all instances of `Archived` from the corresponding enum with `Unpublished`.
### Newly obsolete API
**Generic provider updates**  
The following Info providers were made obsolete. Use the generic [IInfoProvider](/documentation/developers-and-admins/api/database-table-api#generic-provider-class-iinfoprovidertinfo) provider instead.
_CMS.DataProtection_ namespace:
  * ConsentInfoProvider
  * ConsentAgreementInfoProvider
  * ConsentArchiveInfoProvider


_CMS.Modules_ namespace:
  * ResourceInfoProvider


_CMS.ContactManagement_ namespace:
  * ContactInfoProvider
  * ContactGroupInfoProvider
  * ContactGroupMemberInfoProvider
  * ContactRoleInfoProvider
  * ContactStatusInfoProvider
  * VisitorToContactInfoProvider
  * AccountContactInfoProvider
  * AccountInfoProvider
  * AccountStatusInfoProvider


_CMS.ContentEngine_ namespace:
  * ContentFolderInfoProvider


**ContactInfoProvider methods**  
The `ContactInfoProvider` class for managing [contacts](/documentation/business-users/digital-marketing/contact-management) is obsolete, and basic CRUD operations can instead be performed via the generic [IInfoProvider](/documentation/developers-and-admins/api/database-table-api#generic-provider-class-iinfoprovidertinfo) provider. Methods that provided functionality outside of this scope were refactored into separate supporting services, and have the following replacements:
  * _GetContactInfo(string email)_ → _IContactExternalDataUpdateService.GetByEmail_
  * _UpdateContactFromExternalData_ → _IContactExternalDataUpdateService.Update_
  * _DeleteContactInfos_ → _IContactsDeleteService.BulkDelete_
  * _GetContactFullName_ → _ContactInfo.ContactDescriptiveName_ property


**Content item parameter API**  
All the `CreateContentItemParametersconstructors` for creating content item parameters were made obsolete. Use the new `CreateContentItemParameters` constructor with 5 parameters instead, which additionally specifies a content item’s workspace.
C#
Copy
```
// Creates a content item metadata object
CreateContentItemParameters createParams =
          new CreateContentItemParameters(contentType,
                                          codeName,
                                          contentItemDisplayName,
                                          languageName,
                                          workspaceName);
```

**Content folder API**  
The `GetRoot` and `GetRootAsync` methods for retrieving the root content folder in Content hub were made obsolete. Use the new `GetRoot` and `GetRootAsync` methods with a `workspaceName` parameter for retrieving the root content folder from a specific workspace instead.
**Admin UI customization API**  
The `UINoPermissionRequired` attribute, which indicated permissionless access to and visibility of applications in the admin UI, was made obsolete and renamed `NoPermissionRequired`, which has identical functionality.
### Removed obsolete API
The refresh release removes API marked _Obsolete_ since version 27.0.0. Ensure your code no longer references these members to avoid compilation errors.
The following list highlights important types and areas of the API that were affected.
  * `CMSString` wrapper class over the framework `string` object – use conventional framework APIs to work with strings instead.
  * `TypeHelper` helper methods for manipulating object type code name strings – there is no alternative.
  * `SqlHelper` methods for query manipulation and comparison were made internal – there is no alternative.
  * `TableManager` methods for working with various SQL objects – there is no alternative.
  * `FileHelper` methods for various text file operations – there is no alternative.
  * `UserRoleInfoProvider.Add(int userId, int roleId, DateTime? validTo = null)` method for creating temporary role assignments – there is no alternative.
  * `GetConsentText()` method for ConsentInfo objects – use the `GetConsentTextAsync()` method.


### Fixed issues
  * Admin UI
    * Admin UI – Values entered into _Code name_ inputs weren’t validated correctly for the maximum allowed length in certain parts of the system (e.g., content folder code names).
    * Segments in the content folder breadcrumb navigation shown in the _Combined content selector_ dialog didn’t work correctly with all link operations provided by browsers.
    * The [Asset uploader](/documentation/developers-and-admins/development/content-types#add-option-to-upload-files) form component incorrectly displayed the previous version of an image after a new image was uploaded. The public content was not affected and the correct image was displayed. 
  * Admin UI customization – It wasn’t possible to disable the _Role_ selection field for the editing form in the _Users_ application via the _Edit_ [UI form](/documentation/developers-and-admins/customization/object-types/extend-system-object-types) of the customizable _User_ class.
  * Automation
    * Users without the _Update_ [permission](/documentation/developers-and-admins/configuration/users/role-management) for the [Automation](/documentation/business-users/digital-marketing/automation) application were not able to view the contents of the General panel, receiving an _Access denied_ notification.
    * [Automation process statistics](/documentation/business-users/digital-marketing/automation#automation-process-analytics) did not display time zone information in the _Statistics from_ tooltip. 
  * Content hub – Saving an update of the _Requires authentication_ property for a [content item](/documentation/business-users/content-hub/content-items) didn’t trigger an update of the item’s _Last modified_ date and time.
  * Emails
    * The [email](/documentation/business-users/digital-marketing/emails) preview displayed an error if more than 5 levels of [linked content items](/documentation/business-users/content-hub/content-items#link-content-items) were added to the email’s fields. After applying the update, the error no longer appears, but the [limit of 5 nested content item levels](/documentation/developers-and-admins/development/content-types#add-the-option-to-link-content-items) still applies, and further levels are not loaded into the email content.
    * Users without the _Send email_ [permission](/documentation/developers-and-admins/configuration/users/role-management) for [email channel](/documentation/developers-and-admins/digital-marketing-setup/email-channel-management) applications were incorrectly able to edit [regular emails](/documentation/business-users/digital-marketing/emails/send-regular-emails-to-subscribers) that were scheduled to be sent. This permission is now required. After applying the update, add the _Send email_ permission to all [roles](/documentation/developers-and-admins/configuration/users/role-management) that need to be able to edit scheduled regular emails. 
  * Object types – When [modeling object type relationships](/documentation/developers-and-admins/customization/object-types/object-type-configuration/model-object-type-relationships) via a database column in a custom module class, the _Reference to_ selector offered a large number of object types that were not relevant (such as binding object types or internal system objects).
  * Permissions – Users could see sections of a [website channel](/documentation/developers-and-admins/configuration/website-channel-management)’s content tree without having the _Display_ permission set for the [website’s root](/documentation/developers-and-admins/configuration/users/role-management/page-permission-management#allow-users-to-work-with-website-channel-content). Only sections [breaking inheritance](/documentation/developers-and-admins/configuration/users/role-management/page-permission-management#edit-page-permissions) and having at least the _Display_ permission were visible. The final fix for this issue is introduced in [Refresh 30.3.0](#refresh-december-12-2024).
  * Users – The _Users_ application incorrectly allowed the _User name_ value to be edited for inactive [users](/documentation/developers-and-admins/configuration/users/user-management) who hadn’t accepted the invitation yet.
  * Website content – It was not possible to create a new [language variant](/documentation/business-users/website-content/translate-pages) of a page using the _Copy content from another language_ option if the current language variant of the page contained an empty field with the [Asset uploader](/documentation/developers-and-admins/development/content-types#add-option-to-upload-files) form component.


* * *
### Hotfix (December 5, 2024)
**version 29.7.3**
Fixed issues – security
  * [Security advisory 2024-12-05](/documentation/security-advisories/security-advisory-2024-12-05) – information about security issues fixed within this release.


* * *
### Hotfix (November 28, 2024)
**version 29.7.2**
Updates and changes
  * General – The hotfix updates the following NuGet package dependencies:
    * System.Text.Json to version 8.0.5
    * System.Text.RegularExpressions to version 4.3.1


Fixed issues
  * Cookies – Signing in to the administration incorrectly increased the user’s accepted [cookie level](/documentation/developers-and-admins/data-protection/cookies#cookie-levels) to _Editor_ , which stopped the user from being tracked as a [contact](/documentation/business-users/digital-marketing/contact-management) when visiting the live site in the same browser. After applying the hotfix, signing in to the administration sets the cookie level to _All_ instead.
  * Emails – The `SmtpOptions.TransferEncoding` option, which can be configured when setting up [SMTP server email clients](/documentation/developers-and-admins/configuration/email-configuration#smtp-servers), didn’t work if set via the `AddXperienceSystemSmtp` or `AddXperienceChannelSmtp` method.
  * Rich text editor – Using the [rich text editor](/documentation/business-users/rich-text-editor) component as an editing component of a [Page Builder widget](/documentation/developers-and-admins/development/builders/page-builder/widgets-for-page-builder/widget-properties) property led to an error when saving text with an inserted link. The issue occurred in versions 29.7.0 and 29.7.1.
  * Website content – A user without the _Update_ [permission](/documentation/developers-and-admins/configuration/users/role-management/page-permission-management) for specific web pages was able to publish such pages via [cascade publishing](/documentation/business-users/content-hub/content-items#cascade-publishing) in certain scenarios.


* * *
### Hotfix (November 21, 2024)
**version 29.7.1**
New features
  * **Batch confirmation of updates for channels and domains in Xperience Portal**
When configuring channels and domains for projects, users of [Xperience Portal](/documentation/developers-and-admins/saas/xperience-portal) can now perform multiple queued changes and apply them all at once when the system finishes the configuration. As a result, the application does not need to be restarted after each change and is restarted only once when applying the changes.


Updates and changes
  * The user interface of [Xperience Portal](/documentation/developers-and-admins/saas/xperience-portal) has been improved in several locations: 
    * The _Deployments_ application now has a new _Deployment progress_ listing where you can observe the progress of any deployments currently in progress.
    * The _Deployment history_ listing in the _Deployments_ application now supports pagination and filtering.
    * The _Channels_ application in the _Channels and domains_ section now supports pagination and filtering.


Fixed issues – security
  * [Security advisory 2024-11-21](/documentation/security-advisories/security-advisory-2024-11-21) – information about security issues fixed within this release.


Fixed issues
  * Admin UI – The icon selector was, in some cases, incorrectly displayed under the content item creation dialog when used as a [UI form component](/documentation/developers-and-admins/customization/extend-the-administration-interface/ui-form-components) for a field of a content type.
  * Page Builder – [Page Builder component](/documentation/developers-and-admins/development/builders/page-builder#page-builder-components) (widget, section, page template, etc.) properties did not properly link content items if the property name started with multiple capitalized letters. As a result, [usage tracking](/documentation/business-users/content-hub/content-items#track-usage-of-content-items) and [cascade publishing](/documentation/business-users/content-hub/content-items#cascade-publishing) didn’t work for content items linked through these properties.
  * Rich text editor – URLs with the “mailto”, “data”, and “tel” URI schemes had their URLs changed after repeated saves in the [rich text editor](/documentation/business-users/rich-text-editor).


* * *
## Refresh (November 14, 2024)
**version 29.7.0**
### Security advisories
Beginning with this release (November 14, 2024 onwards, product version 29.7.0 and later), you can find all new information about security updates for Xperience by Kentico in the form of [security advisories](/documentation/security-advisories).
### Subscribe to Xperience by Kentico RSS feeds
Subscribe to our RSS feeds to get notified about new product features or security updates as soon as we release them.
  * Are you interested only in receiving security updates? Then subscribe to our [Security updates RSS feed](https://docs.kentico.com/feeds/xbyk-security.xml).
  * Do you want to receive all product updates, including all refresh and hotfix news? Then subsribe to our [Product releases RSS feed](https://docs.kentico.com/feeds/xbyk-releases.xml).


### New features
**Combined content selector**  
The content item selector [form component](/documentation/developers-and-admins/customization/extend-the-administration-interface/ui-form-components/reference-admin-ui-form-components) was improved to enable the selection of web pages from [content trees of website channels](/documentation/business-users/website-content) in addition to reusable content items from the [content hub](/documentation/business-users/content-hub). As a result, the selector was renamed to _Combined content selector_. This improvement enables developers to directly access fields of selected web pages in [emails](/documentation/developers-and-admins/digital-marketing-setup/email-templates#linked-pages-and-content-items) and [headless content](/documentation/developers-and-admins/development/content-retrieval/retrieve-headless-content#linked-pages).
**Administration authentication improvements**  
The authentication functionality of the Xperience administration now provides the following features, which help increase security against brute-force attacks:
  * Account lockout – temporarily blocks authentication for user accounts after a number of failed sign-in attempts. See [Account lockout](/documentation/developers-and-admins/configuration/users/user-management#account-lockout) to learn more.
  * Forbidden passwords – allows developers to [configure a list of passwords](/documentation/developers-and-admins/configuration/users/administration-registration-and-authentication/administration-forms-authentication#password-options) that cannot be submitted when users register new accounts or reset existing passwords (even if the password otherwise fulfills password policy requirements). This prevents users from choosing common or easy-to-guess passwords.


**Notifications for administration users**  
[Notifications](/documentation/developers-and-admins/configuration/notifications) inform users of the administration about various events. In the current release, notifications are supported for [user management](/documentation/developers-and-admins/configuration/users/user-management) events, such as [invitations](/documentation/developers-and-admins/configuration/users/user-management#reset-password) and [password resets](/documentation/developers-and-admins/configuration/users/user-management#invite-users). These notifications replace the original code-driven approach to customizing user management emails, which allows non-developers to adjust the notification content directly in the administration through the new _Notifications_ application.
**Asset download**  
The _Content hub_ application now allows editors to [download files](/documentation/business-users/content-hub/content-item-assets#download-files) uploaded as content item assets.
**Automation summary for contacts**  
When viewing contacts in the _Contact management_ application, the new _Automation processes_ tab displays a list of [automation processes](/documentation/business-users/digital-marketing/automation) the contact has triggered and the current step for each process. Additionally, the three latest automation processes of a contact are displayed on the _Overview_ tab.
**Run database migrations during CI/CD restore**  
The [CI/CD](/documentation/developers-and-admins/ci-cd) utility now supports running [database migrations](/documentation/developers-and-admins/ci-cd/ci-cd-database-migration-scripts) as part of the CI/CD database restore operation via the `--enable-migrations` parameter. When using this parameter, migrations don’t need to be applied using external scripts. The utility now covers all CI/CD scenarios supported by Xperience.
### Updates and changes
  * Automation – The _Backspace_ , _Shift_ and _Space_ keys no longer have any effect in the [Automation Builder](/documentation/business-users/digital-marketing/automation) interface. Previously, these keys could cause unintentional interactions while designing automation processes.
  * Form components – The _Content item selector_ form component was renamed to _Combined content selector_ , and the related _Content items_ data type was renamed to _Pages and reusable content_. These changes only affect the administration interface. The code representation of the affected object remains the same. The form component attribute for the Combined content selector is still `ContentItemSelectorComponent`.
  * Forms – The UI for configuring basic autoresponders and more advanced email automation for [forms](/documentation/business-users/digital-marketing/forms/create-and-edit-forms) was split into two separate tabs in the form options panel (_Autoresponder_ and _Automation_).


### New object types for CI/CD
The update introduces the following object types, which are supported by the [Continuous Integration](/documentation/developers-and-admins/ci-cd/continuous-integration) and [Continuous Deployment](/documentation/developers-and-admins/ci-cd/continuous-deployment) features. Consider updating the [repository.config](/documentation/developers-and-admins/ci-cd/configure-ci-cd-repositories) files of your CI/CD repositories, particularly when using the `<IncludedObjectTypes>` allowlist for object type filtering.
  * `cms.notificationemail` – stores the content and configuration of system notification emails (user invitations, password reset emails, etc.).
  * `cms.notificationemailtemplate` – stores notification email templates.


### Newly obsolete API
**Generic provider updates**
The `ApplicationPermissionInfoProvider` class from the _CMS.Membership_ namespaces was made obsolete. Use the generic [IInfoProvider](/documentation/developers-and-admins/api/database-table-api#generic-provider-class-iinfoprovidertinfo) provider instead.
**Options API for administration authentication emails**
The following options API related to [user management](/documentation/developers-and-admins/configuration/users/user-management) emails was made obsolete:
  * `AdminIdentityEmailOptions.SenderAddress`
  * `AdminIdentityEmailOptions.RegistrationEmailMessageProvider`
  * `AdminIdentityEmailOptions.ResetPasswordEmailMessageProvider`
  * `AdminIdentityEmailOptions.ResetMultifactorSecretKeyEmailMessageProvider`


Instead, customize user management emails via the corresponding [notifications](/documentation/developers-and-admins/configuration/notifications), which can be configured through the new _Notifications_ application in the administration UI.
If you have the obsolete `AdminIdentityEmailOptions.SenderAddress` property configured, it overrides sender addresses configured in the _Notifications_ application. We recommend that you set the sender address for each notification email through the _Notifications_ application, and then remove the usage of the obsolete API.
**ContactInfoProvider methods**
The following `ContactInfoProvider` methods are now obsolete:
  * `GetContactIDByEmail(string email)` – use the _GetContactInfo(string email)_ method instead.
  * `GetContactFullName(int id)` – use the overload with a _ContactInfo_ parameter instead.
  * `UpdateContactFromExternalData(BaseInfo source, bool allowOverwrite, int contactID)` – use the overload with a _ContactInfo_ parameter instead.


### Fixed issues – security
  * [Security advisory 2024-11-14](/documentation/security-advisories/security-advisory-2024-11-14) – information about security issues fixed within this release.


### Fixed issues
  * Admin UI
    * The UI of the _Content hub_ application didn’t work correctly if content items were deleted while being selected in the listing. The number of selected items wasn’t updated and the mass actions for selected remained visible even if all of the selected items were deleted.
    * When the _From/To_ option in a listing filter was filled in, the resulting time range was calculated incorrectly (depending on the [UTC offset](https://en.wikipedia.org/wiki/UTC_offset) of the server hosting the application). Applying the filter could then cause the listing to display the wrong results. 
  * Auto-scaling – The system’s [auto-scaling support](/documentation/developers-and-admins/configuration/auto-scaling-support) didn’t correctly synchronize [role-permission assignments](/documentation/developers-and-admins/configuration/users/role-management) to other instances of the application (for example when an application permission was removed for a role or a role was deleted).
  * Automation – _Wait_ steps within [automation processes](/documentation/business-users/digital-marketing/automation) incorrectly allowed users to enter a time interval value lower than 1.
  * CI/CD – Trying to create a different [language variant](/documentation/business-users/website-content/translate-pages) of a website channel page after a [CI/CD](/documentation/developers-and-admins/ci-cd) restore operation could lead to an error. This issue occurred if the [language](/documentation/developers-and-admins/configuration/languages) of the variant was newly added by the CI/CD restore, and only for pages that didn’t exist on the source instance of the CI/CD data.
  * Caching – Cached content of web pages was not cleared when the web page data of linked pages (e.g., URL slug) was modified.
  * Cookies – Signing in to the administration interface didn’t increase the accepted [cookie level](/documentation/developers-and-admins/data-protection/cookies#cookie-levels) to _Editor_ in the user’s browser. This could cause issues in the administration of projects that used custom cookies registered with the _Editor_ level.
  * Event log – In certain cases, database exceptions related to event logging could occur when running code making use of `CMS.DataEngine.ApplicationBackgroundService`. For example, background system tasks and custom code were affected. The issue was caused by logging dynamic-length values to database columns limited to 100 characters.
  * Headless content – The GraphQL schema of a headless channel couldn’t be [queried](/documentation/developers-and-admins/development/content-retrieval/retrieve-headless-content) when a [headless content type](/documentation/business-users/headless-content) referenced other headless types via a [reusable field schema](/documentation/developers-and-admins/development/content-types/reusable-field-schemas) that wasn’t used by any headless content types assigned to the channel.
  * Page Builder – The [Page Builder](/documentation/developers-and-admins/development/builders/page-builder)’s editing UI stopped working correctly if left open for an hour without a refresh, which could cause all unsaved changes to be lost. The issue occurred on version 29.5.0 or newer.
  * Rich text editor
    * Pasting formatted text into the [rich text editor](/documentation/business-users/rich-text-editor) could result in invalid _HTML_ caused by multiple identical _id_ attributes.
    * When editing the content of [website channel](/documentation/developers-and-admins/configuration/website-channel-management) pages, URLs containing the given website channel’s domain inserted into the [rich text editor](/documentation/business-users/rich-text-editor) were processed incorrectly. Such URLs could then point to the wrong domain in cases where the website channel’s domain was different than the domain used to access the administration application. 


* * *
### Hotfix (November 7, 2024)
**version 29.6.3**
Fixed issues
  * Admin UI authentication – An error occurred when signing in to the administration with [multi-factor authentication](/documentation/developers-and-admins/configuration/users/user-management#multi-factor-authentication) enabled. The issue occurred on projects that didn’t have [ASP.NET Identity](https://learn.microsoft.com/en-us/aspnet/core/security/authentication/identity) services configured for the application (e.g., for live site authentication).
  * Rich text editor – The _Insert link → Asset_ dialog in the [rich text editor](/documentation/business-users/rich-text-editor) only displayed content item assets that were translated in the currently selected language, without showing assets that were available in a [fallback language](/documentation/developers-and-admins/configuration/languages#language-fallbacks).


* * *
### Hotfix (October 31, 2024)
**version 29.6.2**
Fixed issues
  * API – The obsolete message of the `AuthenticateUrl` method pointed to the wrong replacement method.
  * Auto-scaling – In certain rare cases, the system’s [auto-scaling support](/documentation/developers-and-admins/configuration/auto-scaling-support) could stop synchronizing changes between active instances.


* * *
### Hotfix (October 24, 2024)
**version 29.6.1**
Fixed issues
  * Emails – Adding a _Pages_ field into an email [content type](/documentation/developers-and-admins/development/content-types) resulted in unexpected behavior, preventing emails using the content type from being sent.
  * Event log – Triggering the [event log](/documentation/developers-and-admins/configuration/event-log) cleaning procedure (by exceeding the event log size by a set percentage, for example) with a large number of long entries in the event log could lead to a database deadlock and the event log application becoming inaccessible. The issue was observed with 50 thousand entries and an average record size measuring a few kilobytes.


* * *
## Refresh (October 17, 2024)
**version 29.6.0**
### License key transition period ending
The release of version 29.6.0 marks the end of the three-month transition period to the new license key format introduced in the July Refresh – [Product instance licensing changes](#product-instance-licensing-changes).
All projects that update to version 29.6.0 and newer must use the new license keys exclusively.
### New features
**Multi-factor authentication for the administration**  
The Xperience administration now supports multi-factor authentication (MFA) for [users](/documentation/developers-and-admins/configuration/users/user-management). You can enable MFA to add an extra layer of security to the administration sign-in process – users need to provide a passcode generated by an authenticator app using the [Time-based One-time Password](https://en.wikipedia.org/wiki/Time-based_one-time_password) (TOTP) algorithm. See [Multi-factor authentication](/documentation/developers-and-admins/configuration/users/user-management#multi-factor-authentication).
**Email microchannels**  
The system now supports the _Microchannel_ size for [email channels](/documentation/developers-and-admins/digital-marketing-setup/email-channel-management). Email microchannels are available in all license tiers, but have a limit of **5** emails per microchannel. This limit restricts the maximum number of email items allowed in the channel, but not how many times these emails can be sent to recipients.
You can select the channel size when creating new email channels in the _Channel management_ application, or switch the size for existing channels that do not exceed the maximum number of allowed emails.
**Mass asset upload**  
The _Content hub_ application now allows users to [mass upload](/documentation/business-users/content-hub/content-item-assets#mass-asset-upload) new files and automatically convert them to content item assets of a configured [content type](/documentation/developers-and-admins/development/content-types#mass-asset-upload-configuration).
**Content item selector supports multiple content types**  
The content item selector form component now supports multiple allowed content types. You can specify multiple content types when using the selector in [content type fields](/documentation/developers-and-admins/customization/field-editor) or as an [editing component](/documentation/developers-and-admins/customization/extend-the-administration-interface/ui-form-components/reference-admin-ui-form-components#combined-content-selector).
**Retrieval of absolute URLs of pages**  
The `IWebPageUrlRetriever` interface now enables users to directly [retrieve absolute URLs](/documentation/developers-and-admins/development/content-retrieval/retrieve-page-content/retrieve-page-urls) of web pages. The retrieved object now contains an `AboluteUrl` property in addition to the `RelativePath` property.
**‘Clicked email link with URL’ condition for contact groups and automation**  
A new condition is available for [contact groups](/documentation/business-users/digital-marketing/contact-groups) and [automation processes](/documentation/business-users/digital-marketing/automation), which allows marketers to check if a contact has clicked a link with a specific URL within a selected email.
### Updates and changes
**.NET 8 in Kentico.Xperience.Templates projects**  
.NET project templates installed from the [Kentico.Xperience.Templates](https://www.nuget.org/packages/Kentico.Xperience.Templates) NuGet package version 29.6.0 and newer now target .NET 8 instead of .NET 6.
**End of preview mode for automation**  
[Automation](/documentation/business-users/digital-marketing/automation), which was introduced in preview mode in refresh 29.4.0, is now released as a fully supported feature.
**Excluding jQuery from Form Builder and Page Builder scripts**  
This release removes the dependency of [Page Builder](/documentation/developers-and-admins/development/builders/page-builder) and [Form Builder](/documentation/developers-and-admins/development/builders/form-builder) client-side [scripts](/documentation/developers-and-admins/development/reference-tag-helpers#page-builder-scripts) on the jQuery library. To maintain backward compatibility, users now control whether jQuery is linked on live site pages with Form Builder and Page Builder content. The behavior is configured via a new `CMSBuilderScriptsIncludeJQuery` setting (set using _appsettings.json_), which is `true` by default. Starting with the next major release (version 30.0.0), the setting will default to `false`, requiring explicit opt-in. All out-of-the-box builder features remain functional without jQuery. See [Exclude jQuery from builder scripts](/documentation/developers-and-admins/development/builders/bundle-static-assets-of-builder-components#link-jquery-as-part-of-builder-scripts).
jQuery will be phased out completely in one of the future major releases. For projects with custom components that rely on the library, consider disabling it in Xperience and referencing an external implementation to prevent possible issues in the future.
**Detecting Page Builder read-only mode**  
Users now have the option to check the [Page Builder](/documentation/developers-and-admins/development/builders/page-builder) mode from a retrieved `IPageBuilderDataContext` instance via the `GetMode` method. The method returns a value from the `PageBuilderMode` enumeration. See [Establish rendering context](/documentation/developers-and-admins/development/builders/page-builder/create-pages-with-editable-areas#establish-rendering-context).
**Binary serialization support removed from generated Info classes**  
The [code generator](/documentation/developers-and-admins/api/generate-code-files-for-system-objects#generate-code-files) for _Info_ classes no longer generates the following members:
  * `Serializable` attribute annotating the class
  * constructor: protected (SerializationInfo info, StreamingContext context) : base(info, context)


The support for this type of serialization is now obsolete. To remove these members from your custom classes, we recommend regenerating all _Info_ classes after updating your project.
CMD
**Generate Info classes**
Copy
```
dotnet run --no-build -- --kxp-codegen --type "Classes" --with-provider-class False
```

**Removed transitive dependency**  
The _Microsoft.Extensions.Configuration.Binder_ package (transitive dependency) was removed from the _Kentico.Xperience.Core_ package.
### Newly obsolete API
  * The `CMSEmailUrlDefaultScheme` configuration key is now obsolete. Use the `UrlResolveOptions.UseSSL` [option](/documentation/developers-and-admins/development/content-retrieval/retrieve-page-content/retrieve-page-urls#url-formatting-and-behavior) instead.
  * The `AuthenticateUrl` Razor extension method is now obsolete. Use the new `ApplyPreviewContext` method, which [adds preview context to the URLs](/documentation/developers-and-admins/development/content-retrieval/retrieve-page-content#add-preview-context-to-urls) instead.


### Fixed issues
  * Admin UI
    * Drop-down selector UI form components incorrectly changed their height when a value was selected from an empty selector state.
    * The caching of administration requests for content item assets was improved to enhance the performance. 
  * Admin UI authentication – The sign-in button for the administration incorrectly remained active even after being selected. On slower connections, this allowed users to select the button multiple times.
  * Admin UI customization – The content of UI page listings wasn’t reloaded after invoking the `Response().UseCommand('LoadData')` [UI page command](/documentation/developers-and-admins/customization/extend-the-administration-interface/ui-pages/ui-page-commands). The issue occurred on version 29.5.0 or newer.
  * Automation – Deleting a [form](/documentation/business-users/digital-marketing/forms/create-and-edit-forms) that was configured to trigger an [automation process](/documentation/business-users/digital-marketing/automation) also deleted the related process. After applying the update, the system prevents such forms from being deleted in the administration UI. Deleting such forms via the API only deletes the form and leaves the automation process.
  * CI/CD – The [continuous integration](/documentation/developers-and-admins/ci-cd/continuous-integration) repository didn’t correctly maintain the latest state of content item relationships (references to other content items via the _Content items_ [data type](/documentation/developers-and-admins/customization/field-editor/data-type-management)) that were edited via the admin UI. This could lead to errors when restoring the CI data, for example after deleting a linked content item. Projects using CI/CD should follow the steps outlined in the [update instructions](/documentation/developers-and-admins/installation/update-xperience-by-kentico-projects#update-development-projects-with-continuous-integration) to get their repository to the correct state.
  * Content hub
    * Segments in the content folder breadcrumb navigation didn’t work correctly with all link operations provided by browsers (open in new tab, copy link address, etc.).
    * The dialog for renaming [content folders](/documentation/business-users/content-hub/content-hub-folders#content-folders) didn’t display notifications about unsaved changes when canceling or otherwise leaving the dialog.
    * The status of content items wasn’t displayed correctly in the admin UI breadcrumbs when viewing an item on the _Properties_ or _Usage_ tab.
    * The system incorrectly displayed “Access denied” notifications when a user created a new language version of a content item with only _View_ or _Create_ [permissions](/documentation/developers-and-admins/configuration/users/role-management) for the _Content hub_ application. 
  * Headless tracking
    * When [logging](/documentation/developers-and-admins/digital-marketing-setup/headless-tracking#log-activities) _Data input_ activities using the headless tracking API, the values of contact fields weren’t validated to ensure correct maximum length.
    * When [logging](/documentation/developers-and-admins/digital-marketing-setup/headless-tracking#log-activities) _Data input_ activities using the headless tracking API, values of the _ContactEmail_ field weren’t validated to ensure correct email address format. 
  * Pages – Images with multiple source URLs within page content weren’t resolved correctly in certain cases when the page was displayed in _Preview_ mode or [Page Builder](/documentation/developers-and-admins/development/builders/page-builder). For example, the issue could occur for `<picture>` tags containing multiple `<source>` elements with `srcset` attributes.
  * UI form components – The `DisabledFolderIdsFilter` property of the [Content folder selector](/documentation/developers-and-admins/customization/extend-the-administration-interface/ui-form-components/reference-admin-ui-form-components#content-item-folder-selector) component’s `ContentFolderSelector` attribute didn’t work correctly, so it wasn’t possible to specify folders that cannot be selected.
  * Users – The system didn’t correctly refresh the admin UI authentication cookie when a [user](/documentation/developers-and-admins/configuration/users/user-management) changed the _User name_ of their own account, which could lead to various errors while using the administration.
  * Website content – When a [page URL](/documentation/business-users/website-content/manage-page-urls) (system or vanity) was accessed and was not the canonical URL of the page, the redirect to the canonical URL incorrectly returned a non-lowercased URL in certain cases.


* * *
### Hotfix (October 10, 2024)
**version 29.5.3**
Fixed issues
  * Update procedure – An error occurred when [updating a project](/documentation/developers-and-admins/installation/update-xperience-by-kentico-projects) to version 29.3.0 or newer if the project contained a very large number of [Page Builder](/documentation/developers-and-admins/development/builders/page-builder) widgets with properties referencing content items.


* * *
### Hotfix (September 30, 2024)
**version 29.5.2**
Fixed issues
  * Activities – On instances with more than one channel (of any type), _Email click_ [activities](/documentation/business-users/digital-marketing/emails/track-email-statistics) could in some cases be logged under the wrong channel or not at all. Applying the hotfix ensures that _Email click_ activities are logged under the correct email channel and fixes the channel data for activities that were logged incorrectly. However, activities that were not logged at all due to the issue cannot be recovered.


* * *
### Hotfix (September 26, 2024)
**version 29.5.1**
Updates and changes
  * Automation – Several visual improvements were made in the [Automation Builder](/documentation/business-users/digital-marketing/automation) interface.


Fixed issues
  * Automation – Buttons in confirmation dialogs within the [Automation Builder](/documentation/business-users/digital-marketing/automation) interface incorrectly remained active even after a button was selected (for example, a dialog appears when saving changes in an enabled process). On slower connections, this allowed users to select the buttons multiple times, which could result in unexpected behavior or break the automation process.
  * Content item API – [Content item query](/documentation/developers-and-admins/api/content-item-api/content-item-query-api) calls for retrieving items from a smart folder returned incorrect data when the `InSmartFolder` method was used together with the `InLanguage` [query parameterization](/documentation/developers-and-admins/api/content-item-api/reference-content-item-query).
  * Update procedure – An error occurred when [updating a project](/documentation/developers-and-admins/installation/update-xperience-by-kentico-projects) to version 29.3.0 or newer if the project used [widgets with personalization variants](/documentation/business-users/digital-marketing/widget-personalization) in the [Page Builder](/documentation/developers-and-admins/development/builders/page-builder).


* * *
## Refresh (September 19, 2024)
**version 29.5.0**
### New features
**Vanity URLs**  
Editors can now configure pages to use short and human-readable [vanity URLs](/documentation/business-users/website-content/manage-page-urls#edit-vanity-urls-of-pages) for marketing, branding, sharing, and SEO purposes without the need for configuration in the website code. Editors can also select which URL (default system or vanity) is the [canonical URL](/documentation/business-users/website-content/manage-page-urls#select-the-canonical-url-of-pages) of a page.
**Activity logging for email link clicks**  
When recipients click links in [email](/documentation/business-users/digital-marketing/emails) content, the system can now log the actions as [activities](/documentation/business-users/digital-marketing/contact-activities) for individual contacts. These activities are available in addition to the anonymous email statistics that are tracked in current versions of Xperience by Kentico. Email activities allow marketers to [segment contacts](/documentation/business-users/digital-marketing/contact-groups), personalize content, and make decisions based on the actions that your audience takes when viewing email channel content. See [Track email statistics](/documentation/business-users/digital-marketing/emails/track-email-statistics) to learn more.
To ensure compliance with data protection regulations, email activity logging is not active by default. Developers need to [implement logic](/documentation/developers-and-admins/digital-marketing-setup/set-up-email-tracking/log-email-activities#implement-an-email-tracking-activity-evaluator) that decides when it is possible to track email activities for specific contacts, for example based on [consents](/documentation/developers-and-admins/data-protection/consent-management).
**Member registration activity logging**  
The system now logs the new _Member registration_ [activity](/documentation/business-users/digital-marketing/contact-activities) when a visitor completes the [member](/documentation/business-users/members) registration process. Registration activities allow you to segment your contacts, or set up [automation processes](/documentation/business-users/digital-marketing/automation) for engaging with newly registered members.
For visitors who are tracked as [contacts](/documentation/business-users/digital-marketing/contact-management), the activity is logged for the current contact. Otherwise, the activity is logged for an existing contact matching the member’s email address. If a matching contact cannot be found, a new “untracked” contact is created for the activity (based on [legitimate interest](/documentation/developers-and-admins/digital-marketing-setup/set-up-activities#activity-logging-based-on-legitimate-interest)).
The system automatically maps the email address of newly registered members to the email of the corresponding contact (if the contact does not already have an email value). Developers can extend the mapping for [custom member fields](/documentation/developers-and-admins/development/registration-and-authentication/add-fields-to-member-objects) by implementing their own [mapping service](/documentation/developers-and-admins/digital-marketing-setup/contact-configuration/map-custom-member-fields-to-contacts#implement-the-custom-member-field-mapper).
**Automation improvements**  
[Automation](/documentation/business-users/digital-marketing/automation) now supports a new _Registration_ trigger type, which allows you to set up processes for engaging with contacts who register as a [member](/documentation/business-users/members).
Additionally, the Automation Builder now automatically pre-fills names for new steps based on their type, and the text displayed in steps is now fully customizable (only the exact step name is used).
**Content folder moving**  
The _Content hub_ application now allows editors to move [content folders](/documentation/business-users/content-hub/content-hub-folders#content-folders) under a different folder, together with all subfolders and contained items.
**Filtering in the content folder selector**  
The [content folder selector](/documentation/developers-and-admins/customization/extend-the-administration-interface/ui-form-components/reference-admin-ui-form-components#content-item-folder-selector) UI form component now allows developers to specify which content folders are disabled for selection.
**Admin UI development improvements**  
The **UI tree** in the [System](/documentation/developers-and-admins/configuration/system-overview) application was extended to display URL parameters of individual UI pages.
**New SaaS deployment regions**  
Support for Canada East, UK West, Japan West, Australia Southeast, North Central US, and Switzerland North regions was added for [SaaS deployments](/documentation/developers-and-admins/deployment/deploy-to-the-saas-environment/manage-saas-deployments). See [SaaS overview](/documentation/developers-and-admins/saas/saas-overview#where-is-the-saas-service-available) for a list of all available regions.
### Updates and changes
  * Website content – The _URL_ properties of web pages were moved from the _Content_ view mode to a new _URLs_ tab with the new [vanity URL](/documentation/business-users/website-content/manage-page-urls#edit-vanity-urls-of-pages) properties.
  * SaaS 
    * [Service plans](/documentation/developers-and-admins/saas/saas-service-plans) up to Level 3 now allow 2 [custom domains](/documentation/developers-and-admins/configuration/website-channel-management#manage-channel-domains-in-xperience-portal) by default. One for the apex domain (_example.com_) and one for the _www_ subdomain (_www.example.com_).
    * All projects deployed to the [SaaS environment](/documentation/developers-and-admins/saas) now support both the new and old [license key formats](/documentation/changelog#product-instance-licensing-changes).
  * [Xperience Portal](/documentation/developers-and-admins/saas/xperience-portal) – You can now see additional details about your license (Xperience license tier, number of extra [custom website channel domains](/documentation/developers-and-admins/configuration/website-channel-management#manage-channel-domains-in-xperience-portal), and the total number of available standard and microchannels) in the **Service plan and license details** section of the project Dashboard.


### Newly obsolete API
**Generic provider updates**
Provider classes from the _CMS.OnlineMarketing_ and _CMS.Activities_ namespaces were made obsolete. Use the generic [IInfoProvider](/documentation/developers-and-admins/api/database-table-api#generic-provider-class-iinfoprovidertinfo) provider instead. Affected classes:
  * `CMS.OnlineMarketing.TrackedWebsiteInfoProvider`
  * `CMS.Activities.ActivityInfoProvider`
  * `CMS.Activities.ActivityTypeInfoProvider`


**Admin UI page API**
  * The `IPageUrlGenerator` service and the `IPageUrlGenerator.GenerateUrl` method for getting the URLs of administration [UI pages](/documentation/developers-and-admins/customization/extend-the-administration-interface/ui-pages) were made obsolete. Use the new `IPageLinkGenerator` service and `IPageLinkGenerator.GetPath` method instead, which provide more robust URL generating in cases where new path segments are added to UI page URLs.
  * The `UIPage` attribute with the `icon` parameter was made obsolete. The new `UIPage` attribute uses an `Icon` property instead.
  * API members for setting URL parameter values for UI pages via a `string []` array were made obsolete (for example `PageConfiguration.AddEditRowAction`). Use the new API which replaces the array parameter with the `PageParameterValues` class.


C#
**Example**
Copy
```
PageConfiguration.AddEditRowAction<ChildObjectEdit>(parameters:
  new PageParameterValues
    {
        { typeof(ChildObjectEditSection), ParentId }
    });
```

**Smart folder selector attribute**
When assigning the [Smart folder selector](/documentation/developers-and-admins/customization/extend-the-administration-interface/ui-form-components/reference-admin-ui-form-components#smart-folder-selector) UI form component to properties, the `SmartFolderSelectorComponent` attribute constructor with the `contentTypeFilter` parameter is now obsolete.
Instead, use the attribute constructor without parameters and assign the filter type into the corresponding attribute property – either `AllowedContentTypeIdentifiersFilter` or `AllowedReusableFieldSchemaIdentifiersFilter`.
C#
**Example**
Copy
```
[SmartFolderSelectorComponent(AllowedContentTypeIdentifiersFilter = typeof(CoffeeContentTypeFilter), Label = "Coffee smart folder", Order = 1)]
public SmartFolderReference SmartFolderWithCoffee { get; set; }
```

### Fixed issues
  * Admin UI authentication – If an unauthenticated user attempted to access an administration page while the application was configured to use an [external authentication provider](/documentation/developers-and-admins/configuration/users/administration-registration-and-authentication/administration-external-authentication) for the admin UI, the _returnUrl_ query string parameter was lost during the redirection to the authentication provider. As a result, the administration dashboard was always displayed after successfully signing in instead of the originally request admin UI page.
  * Administration – If `IWebsiteChannelContext` was used to get the current [website channel](/documentation/developers-and-admins/configuration/website-channel-management) within the context of the administration interface, an incorrect channel could be returned based on the domain in the current request’s URL. After the update, `IWebsiteChannelContext` only works when called within the context of website channel pages, and does not return a value for administration interface requests.
  * Automation
    * If an existing contact submitted a form with an email value that didn’t match the contact’s current email address, any [automation processes](/documentation/business-users/digital-marketing/automation) (or autoresponders) configured for the form were incorrectly triggered for the current contact, instead of running for the contact matching the submitted email address. This could cause inconsistencies in autoresponder and automation emails sent as a result of the form submission.
    * Resizing the browser window while working in the [Automation Builder](/documentation/business-users/digital-marketing/automation#create-an-automation-process) could cause all unsaved changes to be lost if the window size was reduced enough to trigger a switch to the mobile layout. After the update, the mobile layout switch does not occur if unsaved changes are detected. 
  * Content hub
    * After switching from a [smart folder](/documentation/business-users/content-hub/content-hub-folders#smart-folders) to a [content folder](/documentation/business-users/content-hub/content-hub-folders#content-folders), the smart folder’s filtering condition remained applied to the list of items displayed for the selected content folder.
    * An error could occur in the [content item filter](/documentation/business-users/content-hub/content-items#find-content-items) dialog after switching the time frame of the _Last modified_ or _Last published_ option while having a date selected in the other option. 
  * Content item API – [Content item query](/documentation/developers-and-admins/api/content-item-api/content-item-query-api) calls for retrieving items from a smart folder resulted in an error if the [query parameterization](/documentation/developers-and-admins/api/content-item-api/reference-content-item-query) included both `InSmartFolder` and `WithContentTypeFields`, but did not select specific content types using either `OfContentType` or `OfReusableSchema`.
  * General – Connection-related errors originating from background system services
    * In rare cases, the system could generate connection-related errors in the [event log](/documentation/developers-and-admins/configuration/event-log), originating from background long-running services supporting the system. The only solution was to restart the application. Error examples:
      * Not allowed to change the `ConnectionString` property. The connection’s current state is closed.
      * There is already an open `DataReader` associated with this `Connection` which must be closed first.
      * Invalid operation. The connection is closed.
  * Page Builder
    * Users could submit a form using the _Form_ widget while editing website channel pages in the [Page Builder](/documentation/business-users/website-content/widgets-and-page-builder) view mode.
    * Users without the _Update_ page permission could access the editable mode of Page Builder for website channel pages under certain conditions. However, any changes made this way could not be saved. 
  * Permissions – Page preview URLs could be accessed by users who had their [page permissions](/documentation/developers-and-admins/configuration/users/role-management/page-permission-management) removed after the link was generated.


* * *
### Hotfix (September 12, 2024)
**version 29.4.3**
Fixed issues
  * SaaS – Certain errors were showing up in the [event log](/documentation/developers-and-admins/configuration/event-log) after deployment to the [SaaS environment](/documentation/developers-and-admins/saas/saas-overview).
  * Unix/Linux – File paths were not processed correctly due to case sensitivity. The paths were being checked with lowercase characters instead of their original casing. After applying the hotfix, the original casing is used during processing.


* * *
### Hotfix (September 5, 2024)
**version 29.4.2**
Updates and changes
  * **Submitted data can no longer modify the current contact’s email address**
When users submit data via a [form submission](/documentation/business-users/digital-marketing/forms/create-and-edit-forms#map-form-submission-to-contact-attributes), [headless tracking Data input activity](/documentation/developers-and-admins/digital-marketing-setup/headless-tracking#log-activities), or [cross-site tracking Data input activity](/documentation/developers-and-admins/digital-marketing-setup/cross-site-tracking#data-input), this data can be mapped to the fields of the associated [contact](/documentation/business-users/digital-marketing/contact-management). After this update, such actions can no longer be used to change the email address of the current contact.
If the contact already has an email address stored in Xperience that doesn’t match the new email value, all related activities and field updates are performed for a different contact. Either an existing contact that matches the submitted email value is used, or a new contact is created. In these cases, the user’s associated contact remains unchanged (stored in a browser cookie on websites), but any subsequent actions, such as [automation processes](/documentation/business-users/digital-marketing/automation), are performed for the “other” contact that matches the submitted email address.


Fixed issues
  * API – It was not possible to correctly call the system’s API via an [external application](/documentation/developers-and-admins/api/use-the-xperience-by-kentico-api-externally) with only the _Kentico.Xperience.Core_ NuGet package installed. Users could encounter issues with service resolution and database connection, for example. Applying the hotfix fixes the issues and changes the steps required to connect an external application to the system. See [Configure external web applications](/documentation/developers-and-admins/api/use-the-xperience-by-kentico-api-externally#web-applications) and [Configure external regular applications](/documentation/developers-and-admins/api/use-the-xperience-by-kentico-api-externally#non-web-applications-console-applications-etc.) for details.
  * Admin UI customization – When a custom [dropdown selector](/documentation/developers-and-admins/customization/extend-the-administration-interface/ui-form-components/reference-admin-ui-form-components#dropdown-selector) was displayed in a confirmation dialog, the dropdown options were covered by the dialog itself.
  * Automation – When a trigger of an [automation process](/documentation/business-users/digital-marketing/automation) was deleted, it remained stored in the database. Adding a new trigger would create an additional database object, potentially causing inconsistent results in the automation process. With the hotfix applied, only the most recent trigger associated with the process remains.
  * Content hub – The toolbar of the [rich text editor](/documentation/business-users/rich-text-editor) didn’t stick to the top of the edited field if the field’s content spanned more than the viewport height.
  * Deployment – When hosting an application in the [SaaS environment](/documentation/developers-and-admins/saas/saas-overview) or on IIS while having the [Application Initialization](https://learn.microsoft.com/en-us/iis/get-started/whats-new-in-iis-8/iis-80-application-initialization) feature enabled and configured to send preload requests (often enabled by default, for example on [Microsoft Azure](https://azure.microsoft.com/)), these requests could have ended with a server error, resulting in errors in the system’s [event log](/documentation/developers-and-admins/configuration/event-log). After applying the hotfix, SaaS deployments automatically register middleware for handling preload requests and return a valid system response. To add the same middleware for projects hosted in other IIS environments, configure the new `IISApplicationInitializationOptions` for your application and enable the `UseDefaultSystemResponseForPreload` property.
C#
**Program.cs**
Copy
```
using Kentico.Web.Mvc;
...

builder.Services.Configure<IISApplicationInitializationOptions>(options =>
{
    options.UseDefaultSystemResponseForPreload = true;
});
```

  * Emails – The system could get stuck during the mailout of a large number of scheduled emails, requiring a full application restart to recover.


* * *
### Hotfix (August 29, 2024)
**version 29.4.1**
Updates and changes
  * SaaS – Optimized the speed of creating [deployments](/documentation/developers-and-admins/deployment/deploy-to-the-saas-environment/manage-saas-deployments) and [backups](/documentation/developers-and-admins/deployment/deploy-to-the-saas-environment/manage-saas-deployments#restore-points-and-exports) for SaaS projects with a large amount of files deployed in regions located outside Europe.


Fixed issues – security
  * Page permissions – The preview URLs of [website channel pages](/documentation/business-users/website-content) were accessible to users without the _Read_ [page permission](/documentation/developers-and-admins/configuration/users/role-management/page-permission-management).
  * XSS – The [Rich text editor](/documentation/business-users/rich-text-editor) component used in the administration was vulnerable to cross-site scripting attacks (XSS). To eliminate this vulnerability, URIs are validated and can contain only [allowed schemes](/documentation/developers-and-admins/configuration/rich-text-editor-configuration#html-allowed-in-the-editor).


Fixed issues
  * Automation – The _Contact is in recipient list_ condition available for _Condition_ steps in [automation processes](/documentation/business-users/digital-marketing/automation) was incorrectly evaluated as true for contacts who belonged to the recipient list, but were unsubscribed or had the bounced status.
  * Channels – It was possible to create [website channels](/documentation/developers-and-admins/configuration/website-channel-management) with [domains](/documentation/developers-and-admins/configuration/website-channel-management#configure-a-domain-name-in-the-xperience-administration) that were already taken by other channels.


* * *
## Refresh (August 22, 2024)
**version 29.4.0**
### New features
**Automation**  
[Automation](/documentation/business-users/digital-marketing/automation) allows you to set up processes that dynamically interact with your audience. Marketers design the steps within each process using a visual Automation Builder interface. In the current version, you can use automation to set up advanced email follow-ups when a [form](/documentation/business-users/digital-marketing/forms) is submitted. With automation, you can send a series of multiple emails, add waiting intervals, or send different emails based on conditions.
**Smart folder dynamic content delivery**  
Dynamic content delivery can now be enabled for [smart folders](/documentation/business-users/content-hub/content-hub-folders#smart-folders). This allows developers to retrieve items matching a smart folder’s filter criteria, and gives content editors the power to control which items are delivered directly in the _Content hub_ UI, without needing to adjust code.
Such folders can be selected using the new [Smart folder selector](/documentation/developers-and-admins/customization/extend-the-administration-interface/ui-form-components/reference-admin-ui-form-components#smart-folder-selector) UI form component, for example in dedicated fields of [website channel pages](/documentation/business-users/website-content) and [reusable content items](/documentation/business-users/content-hub/content-items), or in [Page Builder widget](/documentation/developers-and-admins/development/builders/page-builder/widgets-for-page-builder/widget-properties) properties. Developers can then use such fields to [retrieve](/documentation/developers-and-admins/development/content-retrieval/retrieve-content-items#retrieve-content-items-from-smart-folders) content items from the selected smart folder.
**Smart folder cloning**  
New option to clone [smart folders](/documentation/business-users/content-hub/content-hub-folders#smart-folders), which allows users to quickly create new smart folders with the same filter criteria as an existing folder.
**Smart folder properties**  
Users can now view the properties of [smart folders](/documentation/business-users/content-hub/content-hub-folders#smart-folders) in the _Content hub_ application. The properties include the folder’s identifiers, created and last modified timestamps, as well as other metadata.
**Improved Page Builder read-only mode**  
[Page Builder](/documentation/developers-and-admins/development/builders/page-builder) now allows you to copy widgets and sections, view widget/section properties, and browse available widget personalization variants on pages in a non-editable status (e.g., _Published_ and _Unpublished_).
**Last published filtering in Content hub**  
The Content hub listing was extended to allow editors to filter items based on their Last published date and time. This new option can also be used in the filter criteria of smart folders.
**Listing filter UI improvements**  
When a filter is applied to a listing in the administration (and when viewing a smart folder), hovering over the filter tiles above the listing now displays information about the value set for the matching filter condition.
**Public API for page permission management**  
The new public `WebPageAclManager` [API](/api/content-management/page-permissions) allows developers to manage [page permissions](/documentation/developers-and-admins/configuration/users/role-management/page-permission-management). The API can be useful when managing website content programmatically.
**Multiple allowed email purposes for the email selector**  
The [email selector](/documentation/developers-and-admins/customization/extend-the-administration-interface/ui-form-components/reference-admin-ui-form-components#email-selector) UI form component was extended with the `AllowedEmailPurposes` property, which allows developers to set multiple allowed email purposes.
### Newly obsolete API
**Generic provider updates**  
The following Info providers in the _CMS.EmailMarketing_ and _CMS.EmailEngine_ namespace were made obsolete. Use the generic [IInfoProvider](/documentation/developers-and-admins/api/database-table-api#generic-provider-class-iinfoprovidertinfo) provider instead. This is a continuation of the effort to replace dedicated providers with the generic approach. The interfaces for these providers were made obsolete in the [March 2024 Refresh](#refresh-march-21-2024).
_CMS.EmailMarketing_ namespace:
  * `EmailBounceInfoProvider`
  * `EmailTemplateContentTypeInfoProvider`
  * `EmailConfigurationInfoProvider`
  * `EmailChannelInfoProvider`
  * `EmailChannelSenderInfoProvider`
  * `EmailLinkInfoProvider`
  * `EmailStatisticsInfoProvider`
  * `EmailStatisticsHitsInfoProvider`
  * `EmailTemplateInfoProvider`
  * `EmailSubscriptionConfirmationInfoProvider`
  * `EmailMarketingRecipientInfoProvider`
  * `SendConfigurationInfoProvider`
  * `RecipientListSettingsInfoProvider`


_CMS.EmailEngine_ namespace:
  * `AttachmentForEmailInfoProvider`
  * `EmailAttachmentInfoProvider`
  * `EmailInfoProvider`


### Fixed issues – security
**Stored XSS in Checkbox form component**  
The [Checkbox](/documentation/developers-and-admins/development/builders/form-builder/reference-form-builder-components) component in Form Builder was vulnerable to Cross-Site-Scripting attack (XSS). To eliminate this vulnerability, support for HTML in Checkbox component was removed.
### Fixed issues
  * AIRA – When an [email subject suggestion](/documentation/business-users/aira#email-subject-fields) was clicked in the _Generate with AI_ dialog and inserted into the email subject field, the _Save_ button didn’t become active unless another change was made in the email’s editing form.
  * Admin UI
    * The _More actions_ menu for listing page mass actions wasn’t displayed correctly in the _Content hub_ application. The more actions menu appears when the mass actions available for a listing page cannot fit into the width of the viewing device.
    * The date string of the [Date](/documentation/developers-and-admins/customization/extend-the-administration-interface/ui-form-components/reference-admin-ui-form-components#date-input) and [DateTime](/documentation/developers-and-admins/customization/extend-the-administration-interface/ui-form-components/reference-admin-ui-form-components#date-and-time-input) selector component could overflow the designated input field when displayed on some browsers. 
  * Channels – It is now possible to set an additional website domain via the admin UI (_Channel management_ → edit a channel → _General_ tab) for channels with [domains and domain aliases](/documentation/developers-and-admins/configuration/website-channel-management#domain-aliases-and-environment-specific-domains) set via application configuration. Domains set via the admin UI were previously disregarded for such channels. Domains configured this way behave as additional domain aliases.
  * Content hub
    * If multiple [taxonomy tags](/documentation/developers-and-admins/configuration/taxonomies) were selected in a [smart folder’s](/documentation/business-users/content-hub/content-hub-folders) filter condition, but one of the tags was later deleted, the entire _Taxonomy_ condition was cleared for the folder, and the folder’s items weren’t displayed correctly.
    * When a user switched between folders in the content item selector, any selection of items was cleared, which prevented the selection of items from different [folders](/documentation/business-users/content-hub/content-hub-folders). After the update, item selection persists when switching between folders. 
  * General – Changes made to channel configuration in one [auto-scaling](/documentation/developers-and-admins/configuration/auto-scaling-support) instance were not properly reflected in other auto-scaling instances.
  * Page Builder
    * Improved security of certain [Page Builder](/documentation/developers-and-admins/development/builders/page-builder) endpoints.
    * When more than 12 [widgets](/documentation/business-users/website-content/widgets-and-page-builder) were available in the Page Builder widget selection dialog, widgets without a defined [icon class](/documentation/developers-and-admins/development/builders/page-builder/widgets-for-page-builder#register-widgets) had their names incorrectly trimmed.
    * Widget [personalization condition types](/documentation/developers-and-admins/digital-marketing-setup/content-personalization/develop-personalization-condition-types) without specified properties could not be created. 
  * Performance – The process of [moving pages](/documentation/business-users/website-content/edit-and-publish-pages#move-pages) in a website channel application was optimized to reduce the time required for the action. The improvement mainly affects the movement of a large number of pages at once.
  * Rich text editor – After using the _Refine text with AI_ option in a [rich text field](/documentation/business-users/rich-text-editor) with enabled [AI features](/documentation/developers-and-admins/configuration/aira-configuration), the content of selection dialogs for the _Insert image_ and _Insert link_ editor options wasn’t loaded.
  * Website content – Changing the primary language of a [website channel](/documentation/developers-and-admins/configuration/website-channel-management) caused an error in certain edge cases.


* * *
### Hotfix (August 15, 2024)
**version 29.3.3**
Fixed issues
  * Media libraries – Synchronization tasks for auto-scaling were incorrectly created when storing media library files in a shared [file system provider](/documentation/developers-and-admins/api/files-api-and-cms-io/file-system-providers).


* * *
### Hotfix (August 8, 2024)
**version 29.3.2**
Fixed issues
  * Event log – Errors were logged into the system’s [event log](/documentation/developers-and-admins/configuration/event-log) when requests were aborted by a client (i.e., when the application canceled a task). This could result in a large number of unnecessary errors in certain environments. After applying the hotfix, such errors are no longer logged when using the default `Error` log level configuration for `KenticoEventLog` (in the project’s _appsettings.json_ file). If required, you can return logging of such errors as information events by setting the `Debug` log level.


* * *
### Hotfix (August 1, 2024)
**version 29.3.1**
New features
  * Migration Toolkit – The Sitefinity [Migration Tool](/documentation/developers-and-admins/upgrade-to-xperience-by-kentico/migration-toolkit) enables you to automatically export data from Sitefinity and import it to your Xperience by Kentico instance. For more information and detailed instructions, see the [Xperience by Kentico: Sitefinity Migration Tool](https://github.com/Kentico/xperience-by-kentico-sitefinity-migration-tool) GitHub repository.


Fixed issues
  * AIRA – [Artificial intelligence features](/documentation/developers-and-admins/configuration/aira-configuration) could not be enabled for fields using the _Rich text (HTML)_ [data type](/documentation/developers-and-admins/customization/field-editor/data-type-management).
  * Admin UI
    * After expanding the [Properties -> Channel permissions](/documentation/developers-and-admins/configuration/users/role-management/page-permission-management) tab on the root of a website channel’s content tree, the system displayed an incorrect message in the dialog notifying about unsaved changes for any page in the channel.
    * An incorrect error message was displayed whenever users performed an action they didn’t have [permission](/documentation/developers-and-admins/configuration/users/role-management) for.
    * When users without the _Display_ [permission](/documentation/developers-and-admins/configuration/users/role-management/page-permission-management) for a page tried to access it via its URL, an incorrect error message was displayed 
  * Headless channels – Fields using the Rich text (HTML) [data type](/documentation/developers-and-admins/customization/field-editor/data-type-management) could not be accessed and retrieved via [GraphQL queries](/documentation/developers-and-admins/development/content-retrieval/retrieve-headless-content) in [headless content](/documentation/business-users/headless-content).
  * Licensing – Registering new license keys introduced in version 29.3.0 to the system resulted in every feature and customization leveraging [artificial intelligence](/documentation/developers-and-admins/configuration/aira-configuration) features returning license-related errors. For example, users could encounter errors when editing emails in email channels or when editing objects with rich text fields using the _Rich text editor refinements_ or _Generate email content_ features. The issue only affected projects with access to AI features.
  * Page permissions
    * Roles with the _Manage permissions_ application-level permission were incorrectly offered when selecting roles for individual page permissions (these roles automatically have all page permissions).
    * Users with the _Create_ [page permission](/documentation/developers-and-admins/configuration/users/role-management/page-permission-management) could only create new pages if they also had the _Display_ permission for all the page’s children. 
  * Reusable field schemas – Reusable schema fields hidden via the _Display in editing form_ checkbox offered when editing [content types](/documentation/developers-and-admins/development/content-types) on the _Fields_ tab were not modifiable via [global content item events](/documentation/developers-and-admins/customization/handle-global-events/reference-global-system-events) (for [reusable content items](/documentation/developers-and-admins/customization/handle-global-events/reference-global-system-events#contentitemevents), [pages](/documentation/developers-and-admins/customization/handle-global-events/reference-global-system-events#webpageevents), and [headless items](/documentation/developers-and-admins/customization/handle-global-events/reference-global-system-events#headlessitemevents)).
  * Rich text editor – When the [Rich text editor](/documentation/developers-and-admins/customization/extend-the-administration-interface/ui-form-components/reference-admin-ui-form-components#rich-text-editor) UI form component was assigned to a property of a Page Builder component (widget, section, page template, etc.), it wasn’t possible to paste content from MS Word into the editor in the component’s configuration dialog. The entire dialog automatically closed together with the _Word Paste Detected_ dialog.
  * UI form components – When running Xperience in some Linux environments (e.g., as an Azure App Service), it was not possible to enter a date or time via the DateTime selector component due to improperly triggered validation.


* * *
## Refresh (July 25, 2024)
**version 29.3.0**
### Important – Manual update steps
One of the features introduced by this release is [usage tracking](/documentation/business-users/content-hub/content-items#track-usage-of-content-items), which monitors references between content items in the system. For usage tracking to function correctly, the system needs to find and store all existing references during the update.
#### Before the update
The automatic update procedure is capable of handling references created using the default [rich text editor](/documentation/developers-and-admins/customization/extend-the-administration-interface/ui-form-components/reference-admin-ui-form-components#rich-text-editor) and [content item selector](/documentation/developers-and-admins/customization/extend-the-administration-interface/ui-form-components/reference-admin-ui-form-components#combined-content-selector) components. However, if your project uses any custom solutions for storing content item references, such as a custom URL selector, or a custom rich text inline editor, you need to follow a specific update procedure for this refresh:
  1. Update the project’s [NuGet packages](/documentation/developers-and-admins/installation/update-xperience-by-kentico-projects#update-nuget-packages).
  2. **Before updating the database** , you need to implement [reference extractors](/documentation/developers-and-admins/customization/extend-the-administration-interface/ui-form-components/ui-form-component-reference-extractors) for any content type fields or Page Builder component properties containing references to reusable content items added via custom [UI form components](/documentation/developers-and-admins/customization/extend-the-administration-interface/ui-form-components) or [inline editors](/documentation/developers-and-admins/development/builders/page-builder/widgets-for-page-builder/inline-editors-for-widget-properties).
  3. Update the project’s [database and file system](/documentation/developers-and-admins/installation/update-xperience-by-kentico-projects#update-data-and-files).


If you update your project’s database before implementing the necessary reference extractors, usage tracking will report results inconsistent with the true state of your project. Contact [Kentico support](https://www.kentico.com/services/support) for assistance if you encounter any issues or perform the database update before implementing reference extractors.
#### After the update
After performing the update, you need to change the [data type](/documentation/developers-and-admins/customization/field-editor/data-type-management) of all fields that use the _Rich text editor_ form component to a new data type: _Rich text (HTML)_. Using _Text_ and _Long text_ data types together with the _Rich text editor_ form component is now obsolete. The _Rich text (HTML)_ data type uses the same database representation as _Text_ and _Long text_ , so you can switch the data types in the _Content types_ application. Content types with the obsolete combination of data types and _Rich text editor_ form components display a warning on their _Fields_ tab. Content item usage is tracked in the affected fields but this is likely to change in the future.
You can use the following code snippet to run an automatic one-time conversion of all affected fields to the new data type. Call the `ConvertTextAndLongTextTypesToRichText` method, for example, on application startup after initializing Kentico services.
[Download data type conversion code snippets](/docsassets/documentation/changelog/Content_item_usage_text_data_type_conversions.cs)
### Product instance licensing changes
Beginning with this release (July 25, 2024 onwards, product version 29.3.0 and later), Xperience by Kentico is introducing support for a new license key format. This change allows the system to track and inform clients about contractual violations related to product usage.
Note that changes introduced by this version primarily concern projects that plan to maintain version parity with the latest Xperience by Kentico releases. Projects running Xperience by Kentico versions lower than 29.3.0 **are not** retroactively affected. Such projects can continue using the current license keys until otherwise indicated or until you wish to update to a newer version.
Please carefully read the following sections to familiarize yourselves with the possible impact on your projects.
#### Transition period
The product is entering a _three-month_ transition period lasting until the **October 2024 Refresh** release. During this period, both the current license key format (managed via the _License keys_ application) and the new one will be active and available in the product side by side. You can seamlessly switch your projects to the new format without any downtime.
The _October 2024 Refresh_ release will remove support for the current license key format. **All projects updated to the October 2024 Refresh release or newer** will need to switch to the new licenses.
#### Changes to license key management
The _License keys_ application is becoming obsolete and will be removed in the October 2024 Refresh release. License keys in the new format are managed via the _Settings_ application under _System - > Licenses_.
Clients can generate the new keys via [Client Portal](https://client.kentico.com/). The NEW label on the key generator buttons indicates key generators for the new license keys.
To replace your current license keys with new ones:
  1. Sign in to [Client Portal](https://client.kentico.com/).
  2. Under license management, generate license keys in the new format.
  3. In your Xperience instance, open _Settings - > System -> Licenses_.
  4. Insert the license key into the _License key_ setting and save the changes.
  5. Remove all obsolete license keys from the _License keys_ application.


The new licensing model requires a single license key for each Xperience instance. You no longer need to generate a key per channel domain. Contact sales@kentico.com if you have any questions or require assistance.
#### License keys are no longer bound to domains
Unlike the current license key model, which requires a separate license key for each domain used by the product (e.g., for website and email channels, or domain aliases), the new license keys are not bound to individual domains. Each Xperience by Kentico instance requires only a single license key.
#### Product documentation
Product documentation related to [licensing](/documentation/developers-and-admins/installation/licenses) was updated to reflect the newly introduced approach. The documentation will contain information about both licensing models until the end of the transition period, marked by the release of the _October 2024 Refresh_. All information related to the old licensing process will be removed from that point onward.
### New features
**Page permissions**  
[Page permissions](/documentation/developers-and-admins/configuration/users/role-management/page-permission-management) allow you to manage and scope permissions to website channels, specific content tree sections, and individual pages. To enable the management of permissions on a page level, the usual _View_ , _Create_ , _Update_ , and _Delete_ [permissions](/documentation/developers-and-admins/configuration/users/role-management) set for a whole website channel in the **Role management** application were replaced by:
  * _Access channel_ and _Manage permissions_ permissions for individual website channel applications managed in the **Role management** application
  * _Display_ , _Read_ , _Create_ , _Update_ , and _Delete_ page permissions managed directly from within the website channel application


To support a seamless transition to page permissions, certain roles automatically gain the following permissions after applying the update:
  * Roles with the _Update_ permission for the **Role management** application gain the _Manage permissions_ permission.
  * Roles with any permissions for a particular website channel application gain the _Access channel_ permission for the application and, within it, all corresponding page permissions.


Page permissions are not included in [CI/CD](/documentation/developers-and-admins/ci-cd) data. After performing a CI/CD restore operation, any moved and newly created pages inherit permissions from their new parent page.
**Usage tracking**  
Content editors can now in content throughout the system to help them make informed decisions when editing or deleting already published content. The _Usage_ tab is now available for all content items in the _Content hub_. It contains a listing of reusable items and channel items that link to the currently viewed content item using the content item selector or the rich text editor.
**Channel type conversion**  
Headless and website channels can now be converted between microchannels and regular channels using the _General_ tab when editing a channel in the _Channel management_ application.
**Workflow roles with control over all steps**  
[Workflows](/documentation/developers-and-admins/configuration/workflows) can now be configured to have a set of roles with “full control” (in addition to roles assigned to individual workflow steps). Users in these roles can work with all steps in the workflow, and also directly publish items from any step.
**Scheduled maintenance of Xperience Portal projects**  
To minimize the possible negative effects of on your Xperience Portal projects, you can now choose specific weekdays and timeslots during which your projects can be updated in the _System → Update schedule_ Xperience Portal application.
**SaaS US West deployment regions**  
Support for the West US and West US 2 regions was added for [SaaS deployments](/documentation/developers-and-admins/deployment/deploy-to-the-saas-environment/manage-saas-deployments).
**E-commerce integration – Shopify**  
Allows you to connect your Shopify store with an Xperience by Kentico application using [Shopify Storefront](https://shopify.dev/docs/api/storefront) and [Shopify Admin](https://shopify.dev/docs/api/admin) APIs. The integration provides synchronization of products and e-commerce actions between the two platforms. For more information and detailed instructions, see the [Xperience by Kentico Shopify integration](https://github.com/Kentico/xperience-by-kentico-shopify) GitHub repository.
**E-commerce integration – Kentico Xperience 13**  
Allows you to connect a [Kentico Xperience 13 store](/13/e-commerce-features) with an Xperience by Kentico application. The integration is primarily intended to enable migration of existing Kentico Xperience 13 E-Commerce projects to Xperience by Kentico, but can also be used for other scenarios. For more information and detailed instructions, see the [Xperience by Kentico - Kentico Xperience 13 E-commerce](https://github.com/Kentico/xperience-by-kentico-k13ecommerce) GitHub repository.
### New object types for CI/CD
The update introduces the following object types, which are supported by the [Continuous Integration](/documentation/developers-and-admins/ci-cd/continuous-integration) and [Continuous Deployment](/documentation/developers-and-admins/ci-cd/continuous-deployment) features. Consider updating the [repository.config](/documentation/developers-and-admins/ci-cd/configure-ci-cd-repositories) files of your CI/CD repositories, particularly when using the `<IncludedObjectTypes>` allowlist for object type filtering.
  * cms.contentworkflowrole – stores binding relationships between workflows and roles, indicating that the role has full control for all of the workflow’s steps.


### Obsolete data types for Rich text fields
The combination of using the _Rich text editor_ form component with _Text_ or _Long text_ [data types](/documentation/developers-and-admins/customization/field-editor/data-type-management) in the [field editor](/documentation/developers-and-admins/customization/field-editor) is now obsolete. If you want to use the _Rich text editor_ form component, use the new _Rich text (HTML)_ data type. Using _Text_ and _Long text_ for non-rich text fields is still valid, and the data types themselves are not obsolete.
### Newly obsolete API
  * `CMS.Base.ClassHelper` – there is no alternative. The type was not intended for public use. You can use the application’s service container to dynamically load all required dependencies.
  * `CMS.RegisterCustomManager` attribute – deprecated customization pattern. Use the [decorator pattern](/documentation/developers-and-admins/customization/decorate-system-services) to customize the behavior of system classes.


### Fixed issues
  * CI/CD – Under certain circumstances, working with pages under one website channel could also influence the [CI/CD](/documentation/developers-and-admins/ci-cd) repository structure of pages in other website channels.
  * Former URLs – When filtering [former URLs](/documentation/business-users/website-content/manage-page-urls#former-urls) based on dates, the UI displayed an incorrectly named label in the list of applied filters.
  * General – Removing variable-length fields (_nvarchar_ [data types](/documentation/developers-and-admins/customization/field-editor/data-type-management)) from system objects (content types, object types) could end in a database timeout for objects with a large number of records (~hundreds of thousands). The timeout could be caused by a database size optimization that was run every time a variable-length database column (backing the corresponding removed field) was dropped. The fix for the issue disables this automatic optimization and only runs it during CI/CD database restore. If users need to optimize the database size outside of CI/CD restore operations, they can use the new _Optimize database size_ action added to the _Overview_ tab of the _System overview_ application. The action runs the equivalent of [DBCC CLEANTABLE](https://learn.microsoft.com/en-us/sql/t-sql/database-console-commands/dbcc-cleantable-transact-sql) over the connected database.
  * Installation – When creating a new database via `kentico-xperience-dbmanager`, default system user accounts were incorrectly created with the [security stamp](https://learn.microsoft.com/en-us/dotnet/api/microsoft.aspnetcore.identity.identityuser-1.securitystamp) column set to NULL.
  * Page Builder – An error occurred when using a [widget](/documentation/developers-and-admins/development/builders/page-builder/widgets-for-page-builder#register-widgets), [section](/documentation/developers-and-admins/development/builders/page-builder/sections-for-page-builder#register-sections), [page template](/documentation/developers-and-admins/development/builders/page-builder/page-templates-for-page-builder#register-page-templates), or [personalization condition](/documentation/developers-and-admins/digital-marketing-setup/content-personalization/develop-personalization-condition-types#register-condition-types) that was registered with an identifier starting with a lowercase character.
  * Performance – An excessive number of database queries was used when loading pages in the content tree of [website channel applications](/documentation/business-users/website-content). The performance of the content tree was optimized to improve load times for website channels containing a very large number of pages.
  * Website content
    * Changing the order of [website pages](/documentation/business-users/website-content/edit-and-publish-pages#move-pages) in one [website channel](/documentation/developers-and-admins/configuration/website-channel-management) could affect the order in other website channels.
    * UI components that allowed selection of pages ([page selector](/documentation/developers-and-admins/customization/extend-the-administration-interface/ui-form-components/reference-admin-ui-form-components#page-selector), [URL selector](/documentation/developers-and-admins/customization/extend-the-administration-interface/ui-form-components/reference-admin-ui-form-components#url-selector), _Insert link_ dialog in the [rich text editor](/documentation/business-users/rich-text-editor)) with a limited tree path incorrectly allowed users to select pages under the website root in some circumstances. 


* * *
### Hotfix (July 11, 2024)
**version 29.2.2**
Fixed issues
  * Website content – If [pages](/documentation/business-users/website-content) under different website channels had an identical [tree path](/documentation/developers-and-admins/development/routing/content-tree-based-routing/set-up-content-tree-based-routing#page-routing-overview), and one of these pages was [moved](/documentation/business-users/website-content/edit-and-publish-pages#move-pages), the resulting confirmation message displayed the number of moved pages incorrectly.


* * *
### Hotfix (July 4, 2024)
**version 29.2.1**
Fixed issues
  * Rich text editor – When inserting an image into the [inline rich text editor](/documentation/developers-and-admins/development/builders/page-builder/widgets-for-page-builder/rich-text-inline-editors) within a Page Builder widget, the image’s [alternative text](https://www.w3schools.com/tags/att_img_alt.asp) was not set. The alternative text is now automatically prefilled with the image’s description (or name, if the description is not available).


* * *
## Refresh (June 27, 2024)
**version 29.2.0**
### New features
**Smart folders in Content hub**  
The _Content hub_ application was extended to support [smart folders](/documentation/business-users/content-hub/content-hub-folders#smart-folders), which provide a new dynamic way to organize and filter content items. Each smart folder is based on a filtering condition, such as “items modified in the last 7 days”, “items with the _Draft status_ ”, “items with the _Acme_ tag”, etc. The condition is evaluated dynamically, so items move in and out of smart folders as their fields and metadata change. A single content item can belong into any number of smart folders. Smart folders are also available in all content item selectors throughout the system.
**Scheduled unpublishing of pages and content items**  
”Archive” functionality for content items, website channel pages and headless items was renamed to “Unpublish”, which better reflects the feature’s intended purpose. When unpublishing [content items](/documentation/business-users/content-hub/content-items#unpublish-content-items) or [pages](/documentation/business-users/website-content/edit-and-publish-pages#unpublish-pages), editors can now choose whether to unpublish immediately or schedule the unpublish to a future date and time.
**Scheduling support for cascade publishing**  
When scheduling a [content item](/documentation/business-users/content-hub/content-items#scheduled-publishing) or [website channel page](/documentation/business-users/website-content/edit-and-publish-pages#scheduled-publishing) to be published, the [cascade publishing](/documentation/business-users/content-hub/content-items#cascade-publishing) feature now schedules linked items to be published at the same time, rather than publishing them immediately.
**Microchannels**  
The system now supports the creation of _microchannels_. Microchannels can contain at most 20 content items of the specific channel type – pages for website channels and headless items for headless channels. You can select the channel size when creating new channels in the _Channel management_ application. Microchannels are not supported for [Email channels](/documentation/developers-and-admins/digital-marketing-setup/email-channel-management).
**User interface improvements**  
The [Email selector](/documentation/developers-and-admins/customization/extend-the-administration-interface/ui-form-components/reference-admin-ui-form-components#email-selector) was extended to display the _Status_ column in the list of emails available for selection. Additionally, any disabled emails in the selector now have tooltips explaining why the email cannot be selected.
**Extended support for admin UI listing page filters**  
[Filter models](/documentation/developers-and-admins/customization/extend-the-administration-interface/ui-pages/reference-ui-page-templates/listing-ui-page-template#add-a-listing-filter) for admin UI listing pages were extended to support [validation rules](/documentation/developers-and-admins/customization/extend-the-administration-interface/ui-form-components/ui-form-component-validation-rules), [visibility conditions](/documentation/developers-and-admins/customization/extend-the-administration-interface/ui-form-components/ui-form-component-visibility-conditions) and [component state configurators](/documentation/developers-and-admins/customization/extend-the-administration-interface/ui-form-components/editing-components/configure-editing-component-state). Additionally, new attributes provide the option to set the filter summary text that appears above the listing for individual filter properties, and add properties that appear in the filter UI, but do not affect the resulting filtering condition. This allows developers to create more advanced filters for UI listing pages.
**SaaS East US 2 deployment region**  
Support for the East US 2 region was added for [SaaS deployments](/documentation/developers-and-admins/deployment/deploy-to-the-saas-environment/manage-saas-deployments).
### Updates and changes
  * API – The `WhereIn` and `WhereNotIn` WHERE condition parameterization methods used by the [content item](/documentation/developers-and-admins/api/content-item-api/reference-content-item-query) and [object query](/documentation/developers-and-admins/api/objectquery-api) API were extended with overloads accepting `IEnumerable` in addition to `ICollection`.
  * Content management – All “Archive” terminology in the administration interface and API was renamed to “Unpublish”.
  * Content hub – The _Authentication_ column was removed in the _Content hub_ listing. Instead, a “secured” icon is displayed in the _Status_ column for items that require authentication.
  * General – The names of certain objects ([taxonomies](/documentation/developers-and-admins/configuration/taxonomies), tags, [content hub folders](/documentation/business-users/content-hub/content-hub-folders)) no longer need to be unique. This applies to “display names” that appear in the user interface. The code names of objects still must be globally unique.
  * Rich text editor 
    * The code view of the [Rich text editor](/documentation/business-users/rich-text-editor) no longer highlights code syntax.
    * The [inline rich text editor](/documentation/developers-and-admins/development/builders/page-builder/widgets-for-page-builder/rich-text-inline-editors) used in Page Builder widgets no longer displays the word counter.
  * User interface – Switching between pages in the pagination UI for the listing in the _Content hub_ application now automatically scrolls to the top of the items displayed for the page.


### New object types for CI/CD
The update introduces the following object types, which are supported by the [Continuous Integration](/documentation/developers-and-admins/ci-cd/continuous-integration) and [Continuous Deployment](/documentation/developers-and-admins/ci-cd/continuous-deployment) features. Consider updating the [repository.config](/documentation/developers-and-admins/ci-cd/configure-ci-cd-repositories) files of your CI/CD repositories, particularly when using the `<IncludedObjectTypes>` allowlist for object type filtering.
  * cms.smartfolder – stores [smart folders](/documentation/business-users/content-hub/content-hub-folders#smart-folders), which are used to dynamically categorize and filter items in the _Content hub_ application.


### Newly obsolete API
**“Archive” API**  
All content management API with the “Archive” keyword (e.g., `IContentItemManager.TryArchive`, `WebPageEvents.Archive`, `VersionStatus.Archived`) was made obsolete. Use the new replacement API with the “Unpublish” keyword.
### Fixed issues – security
Requests to endpoints under the `/admin` path returned incorrect HTTP status codes in certain cases. To improve security, the following changes were made:
  * Requests to endpoints under the `/admin` path now return a 400 HTTP status code if anti-forgery token validation fails.
  * Requests to non-existing endpoints under the `/admin` path now return a 404 HTTP status code.


### Fixed issues
  * Admin UI
    * A horizontal scrollbar was incorrectly displayed in tree listings (e.g., taxonomy tree, website channel content tree) whenever a vertical scrollbar was required. The horizontal scrollbar is not necessary anymore, as all tree listings are resizable.
    * Attempting to change the data type of a field via an object’s _Fields_ tab to an incompatible data type (for example, _Date_ to _Long text_) caused a crash in the editing interface that required a full page reload to recover. After applying the refresh, an error message is shown instead.
    * Buttons that were disabled (e.g., due to insufficient user permissions) didn’t show tooltips when hovered.
    * The _Change workflow step_ button in content management applications was missing a tooltip in scenarios where the button was inactive for the current user.
    * The pagination UI for the listing in the _Content hub_ application didn’t display page numbers correctly if there were many pages of items.
    * The website channel content tree displayed an incorrect icon for [pages](/documentation/business-users/website-content) that weren’t translated in the currently selected [language](/documentation/developers-and-admins/configuration/languages), but had an existing language variant that was scheduled to be published.
    * Trees in the administration UI (e.g., the _Content hub_ folder tree) didn’t scroll to the currently selected item when this was required to show the item after reloading the page.
    * [Filtering](/documentation/business-users/content-hub/content-items#find-content-items) configuration was incorrectly shared among all channel applications in an instance. After applying the refresh, filters can be configured independently for each channel application. However, the fix also invalidates all existing filter configurations, which need to be set up again. 
  * Admin UI customization
    * Actions handled by custom components added via `Page.PageConfiguration.HeaderActions.AddActionWithCustomComponent` to [listing page](/documentation/developers-and-admins/customization/extend-the-administration-interface/ui-pages/reference-ui-page-templates/listing-ui-page-template) header actions didn’t work correctly.
    * Links in a [listing UI page](/documentation/developers-and-admins/customization/extend-the-administration-interface/ui-pages/reference-ui-page-templates/listing-ui-page-template) didn’t open in a new tab even though the `Target` property of `ActionConfiguration.LinkParameters` of `TableActions` was set to `"_blank"`. 
  * Cascade publishing – If an item was selected in the [cascade publishing](/documentation/business-users/content-hub/content-items#cascade-publishing) dialog and another action in the system prevented the item from being published (for example if the user lost permissions, or the item was meanwhile published by another user), the item incorrectly remained selected after the dialog was reloaded via the _Refresh_ button. The issue only occurred in the cascade publishing dialog that appeared when [mass publishing](/documentation/business-users/content-hub/content-items#mass-publishing) items in the _Content hub_ application.
  * Content hub folders – The [content hub folder](/documentation/business-users/content-hub/content-hub-folders) tree wasn’t refreshed after renaming a folder, which could cause the folders to be displayed out of alphabetical order until the page was reloaded.
  * General – System routes were not registered correctly when using [ASP.NET Core pipeline branching](https://learn.microsoft.com/en-us/aspnet/core/fundamentals/middleware/?view=aspnetcore-8.0#branch-the-middleware-pipeline) for [middleware registration](/documentation/developers-and-admins/development/website-development-basics/configure-new-projects#configure-application-startup).
  * Headless channels
    * After applying the 29.1.4 update, the [headless item](/documentation/business-users/headless-content) selector component didn’t work correctly.
    * After selecting the [Preview button](/documentation/business-users/headless-content#preview-headless-items), the [Preview URL](/documentation/developers-and-admins/configuration/headless-channel-management#create-headless-channels) didn’t open in a new tab but was instead appended to the current URL, and a general error page was shown. 
  * Media libraries – Attempting to upload a file with a name that already exists in the media library resulted in an error if the original file had a thumbnail attached. This issue occurred only in media libraries mapped to Azure storage.
  * Website content – Deleting the ‘Draft’ [version](/documentation/business-users/website-content#page-lifecycle) of a page could result in an error on the live site due to incorrectly cleared cache.


* * *
### Hotfix (June 20, 2024)
**version 29.1.5**
Fixed issues
  * API – Working with SQL queries containing DateTime strings (e.g., `2/22/2024 1:50:11 PM`) generated by the system could result in SQL exceptions related to malformed DateTime format. Only some Linux environments are currently affected. The issue was caused by an update in the underlying operating system libraries (specifically, [libicu](https://icu.unicode.org/)), which changed the structure of the output DateTime format string. Applying the hotfix prevents a possible occurrence of the issue for Windows and MacOS as well, should a similar library update occur on those platforms in the future.
  * Content items – Pages referencing reusable content items via fields belonging to [reusable field schemas](/documentation/developers-and-admins/development/content-types/reusable-field-schemas) could lose such references when the page used [Page Builder](/documentation/developers-and-admins/development/builders/page-builder) and contained a widget that could also reference reusable content items via its fields. Modifying the reusable content item reference of the widget then caused the system to also remove all reusable content item references from the fields belonging to reusable field schemas.


* * *
### Hotfix (June 13, 2024)
**version 29.1.4**
Fixed issues
  * Content management – After applying the 29.0.0 update, the [content item selector](/documentation/developers-and-admins/customization/extend-the-administration-interface/ui-form-components/reference-admin-ui-form-components#combined-content-selector) displayed content items in a different order than the one in which the items were selected.
  * Membership – Performing any updates to member objects (via `UserManager<ApplicationUser>`) after they were created during the initial registration process caused the system to reset the member’s password. This issue could occur when, for example, [email confirmation](/documentation/developers-and-admins/development/registration-and-authentication/forms-authentication#email-confirmation) was enabled for member accounts.
  * Rich text editor – When using the [Asset](/documentation/developers-and-admins/configuration/rich-text-editor-configuration/rich-text-editor-plugins#asset-plugin) and [Link](/documentation/developers-and-admins/configuration/rich-text-editor-configuration/rich-text-editor-plugins#link-plugin) plugins with a custom [rich text editor configuration](/documentation/developers-and-admins/configuration/rich-text-editor-configuration#customize-the-editor), the buttons for the plugins were not displayed in the editor’s toolbar if the `toolbarButtons` [property](https://froala.com/wysiwyg-editor/docs/options/#toolbarButtons) was defined using a JSON object instead of an array.


* * *
### Hotfix (June 6, 2024)
**version 29.1.3**
Fixed issues
  * API – If the `ContentItemLanguageMetadata.ScheduledPublishWhen` property was set while creating a new content item or page using the [IContentItemManager](/api/content-management/content-items) or [IWebPageManager](/api/content-management/pages) API, and the item’s content type was under workflow, the new item was incorrectly created in the first workflow step (`ContentWorkflowStepType.SystemDraft`), even though it was already scheduled to be published. After applying the hotfix, such items are created in the last workflow step (`ContentWorkflowStepType.SystemFinal`). The update automatically moves any inconsistent items with a scheduled publish date into the last workflow step.
  * SaaS – _Partial_ [project backups](/documentation/developers-and-admins/deployment/deploy-to-the-saas-environment/manage-saas-deployments#restore-points-and-exports) were retained for 60 days instead of the intended expiration time of 60 hours.


* * *
### Hotfix (May 30, 2024)
**version 29.1.2**
Updates and changes
[Xperience Portal](/documentation/developers-and-admins/saas/xperience-portal) – You can now see the details of your service plan (formerly called “project tier”) in the Service overview section of the project Dashboard.
Fixed issues
  * CI/CD – The bindings that assign workflows to content types (_cms.contentworkflowcontenttype_) and workflow steps to roles (_cms.contentworkflowsteprole_) weren’t stored correctly by [CI/CD](/documentation/developers-and-admins/ci-cd). Consequently, only the last assigned content type for workflows and the last assigned role for each workflow step was present after restoring the data to another instance via CI/CD.
  * Cascade publishing – The [cascade publishing](/documentation/business-users/content-hub/content-items#cascade-publishing) dialog displayed linked content items as disabled if they were under workflow and in the _Scheduled_ status. As a result, such items couldn’t be selected for immediate publishing.
  * Rich text editor – The HTML sanitizer used in the [Rich text editor](/documentation/business-users/rich-text-editor) incorrectly processed certain sequences of characters in already encoded URLs.


* * *
### Hotfix (May 23, 2024)
**version 29.1.1**
Fixed issues
  * CI/CD
    * The [CI/CD data](/documentation/developers-and-admins/ci-cd/ci-cd-repository-structure) of pages, content items, emails and headless items could include certain values related to new versions of previously published items (`ContentItemLanguageMetadataContentWorkflowStepID` and `ContentItemLanguageMetadataScheduledPublishWhen` values in _cms.contentitemlanguagemetadata_ files). When the CI/CD data was restored to another instance, these did not cause any issues, but represented minor data inconsistencies, since new draft versions of items are not synchronized by CI/CD.
    * When restoring CI/CD data containing a new [workflow](/documentation/developers-and-admins/configuration/workflows) step together with an item in the given step, an error occurred and the restoration of the item failed. 
  * Content hub – If a user performed an action that changed which items were shown in content item listings (e.g., entering a search phrase, applying a filter, selecting a folder), the current selection of items in the listings was cleared. After applying the hotfix, the item selection persists when the listing is modified.
  * Forms – Using [output caching](/documentation/developers-and-admins/development/caching/output-caching) with a page containing a _Form_ widget prevented all subsequent submissions of the form. After applying the update, the system automatically detects the presence of the _Form_ widget on a page and disables its caching.
  * Taxonomies – While creating a new [tag](/documentation/developers-and-admins/configuration/taxonomies#create-tags-in-a-taxonomy), deleting any of its parent tags redirected the user to a general error page instead of switching to a different tag in the taxonomy tree.


* * *
## Refresh (May 16, 2024)
**version 29.1.0**
### New features
**Scheduled publishing of pages and content items**  
When publishing [content items](/documentation/business-users/content-hub/content-items) and [website channel pages](/documentation/business-users/website-content), editors can now choose whether to publish immediately or select a date and time when the publish will occur.
**Time values in the administration displayed in the user’s local time zone**  
All time values in the administration are now automatically converted and displayed in the local time zone of each user, based on browser and environment settings. For example: 01:30 PM (GMT+02:00). Internally, time values are stored in the time zone of the server where the application is running (exactly like in previous versions of Xperience by Kentico).
**‘Contact has value in field’ condition for contact groups**  
A new _Contact has value in field_ condition is available for [contact groups](/documentation/business-users/digital-marketing/contact-groups). This condition is fulfilled for contacts whose value in a selected field meets the specified requirements.
**Taxonomy support for headless and email channels**  
Fields with the _Taxonomy_ data type can now also be added to [content types](/documentation/developers-and-admins/development/content-types) for [emails](/documentation/business-users/digital-marketing/emails) and [headless items](/documentation/business-users/headless-content).
**Improved filtering in content hub, headless and email channel applications**  
The listing in content hub, headless and email channel applications now allows editors to filter items based on assigned tags. Additionally, the listing in email channel applications also now allows editors to filter emails based on their _Status_.
**Content hub folder properties**  
Users can now view the properties of [folders](/documentation/business-users/content-hub/content-hub-folders) in the Content hub application. The properties include the folder’s ID, code name, created and last modified timestamps, as well as other metadata.
**API for managing content hub folders**  
New [public API](/api/content-management/content-hub-folders) that allows developers to manage content hub folders, and move items between folders. The API can be useful when implementing integrations or migrating content.
**Improved reusable field schemas visualization on the content type ‘Fields’ tab**  
When editing content types on the _Fields_ tab, [reusable field schema](/documentation/developers-and-admins/development/content-types/reusable-field-schemas) placeholder elements now display all fields that constitute the assigned schema. Selecting the edit icon (
**New content item query methods for reusable field schemas**  
Developers can now use the [LinkingSchemaField](/documentation/developers-and-admins/api/content-item-api/reference-content-item-query#FCT-LinkingSchemaField) and [LinkedFromSchemaField](/documentation/developers-and-admins/api/content-item-api/reference-content-item-query#FCT-LinkedFromSchemaField) methods when parameterizing data retrieval using content item queries. `LinkingSchemaField` retrieves all content items that link to at least one item from the provided input collection via a reusable schema field. `LinkedFromSchemaField` retrieves all content items linked from a reusable schema field belonging to items from the provided input collection.
**User interface improvements**  
The editing experience was improved with two new improvements to the administration interface:
  * Editors can open a [preview of pages](/documentation/business-users/website-content#preview) in a new tab from the page actions menu.
  * Editors can resize tree UI components throughout the system to improve navigation with multiple expanded levels (e.g., in website channel applications, the page selector, _Settings_ application, _Taxonomies_ application).


### Updates and changes
  * Content management – The names and icons of _Draft_ item statuses were unified across all content-related applications (_Content hub_ as well as website, email, and headless channel applications). The status names are now _Draft (Initial)_ for new items that haven’t been published yet, and _Draft (New version)_ for published items with a new version that is being edited.
  * Email templates – The _Email templates_ application was moved in the administration’s application list from the _Digital marketing_ category to _Configuration_.
  * Forms – [Form submissions](/documentation/business-users/digital-marketing/forms/manage-form-submissions) are now ordered according to their submission date and time in _descending_ order (the latest form submissions are displayed first).


### Newly obsolete API
**Generic provider updates**
Info provider interfaces in the following namespaces were replaced by the generic `IInfoProvider<TInfo>` [provider](/documentation/developers-and-admins/api/database-table-api#generic-provider-class-iinfoprovidertinfo) and made obsolete:
_CMS.MediaLibrary_
  * `IMediaFileInfoProvider`
  * `IMediaLibraryInfoProvider`


_CMS.DataEngine_
  * `IQueryInfoProvider`
  * `ISettingsCategoryInfoProvider`
  * `ISettingsKeyInfoProvider`
  * `ITempFileInfoProvider`


_CMS.EventLog_
  * `IEventLogInfoProvider`


_CMS.Globalization_
  * `ICountryInfoProvider`
  * `IStateInfoProvider`


**Obsolete localization API**
The following API members from the `CMS.Localization` namespace are not intended for public use, a were made obsolete:
  * `IResourceStringInfoProvider`, `ResourceStringInfoProvider`
  * `ResourceStringInfo`, `ResourceStringInfoBase<TInfo, TInfoProviderInterface>`
  * `IResourceTranslationInfoProvider`, `ResourceTranslationInfoProvider`
  * `ResourceTranslationInfo`, `ResourceTranslationInfoBase<TInfo, TInfoProviderInterface>`
  * `ISQLLocalizationSource`


**Obsolete publish workflow step type**
The `SystemPublish` value in the `ContentWorkflowStepType` enum is now obsolete. Use the `SystemFinal` value instead, which represents items that are scheduled to be published.
### Fixed issues – security
  * Admin UI – Admin UI API endpoints related to Page Builder and Form Builder were accessible to member accounts under specified conditions. The vulnerability didn’t enable attackers to extract sensitive data or perform unauthorized actions in the administration interface.


### Fixed issues
  * API – Resetting thread context using the `ContextUtils` class added in version 29.0.1 caused concurrency issues in certain cases.
  * Admin UI
    * Date and time input fields were styled incorrectly if validation failed when attempting to enter a value.
    * The buttons in the confirmation dialog that appeared when deleting workflow steps were styled incorrectly.
    * Various issues related to asynchronous tasks were fixed for the administration UI. These issues could potentially cause data to be saved or displayed incorrectly, or even UI crashes in rare cases.
    * Various issues related to asynchronous tasks were fixed for the administration UI. These issues could potentially cause data to be saved or displayed incorrectly, or even UI crashes in rare cases. 
  * Azure storage
    * Media library files mapped to [Azure Blob storage](/documentation/developers-and-admins/api/files-api-and-cms-io/file-system-providers/azure-blob-storage) could show incorrect file timestamps (creation and last modified dates) under certain circumstances.
    * Media library files mapped to [Azure Blob storage](/documentation/developers-and-admins/api/files-api-and-cms-io/file-system-providers/azure-blob-storage) could show incorrect file timestamps (creation and last modified dates) under certain circumstances. 
  * Cascade publishing – The [cascade publishing](/documentation/business-users/content-hub/content-items#cascade-publishing) dialog didn’t work correctly in cases where another action caused the publish operation to be invalid (for example if the user lost permissions, or the item was meanwhile published by another user). Error states weren’t displayed correctly for items that could not be published for such reasons, and in certain cases the dialog became stuck in a loading state after clicking the _Publish_ button.
  * Channels – When using [auto-scaling](/documentation/developers-and-admins/configuration/auto-scaling-support) in a project with multiple channels, a newly created channel application in one Xperience instance didn’t properly show up in the UI of the other instances.
  * Content hub folders – If the name of a [content hub folder](/documentation/business-users/content-hub/content-hub-folders) contained multiple whitespace characters in a sequence, they were displayed only as a single space in the folder tree.
  * Content item API – The content item query `TopN` method, when used together with `Where` in a `ForContentType.Parameters()` statement, could sometimes return an empty result due to an incorrect application of the TOP N clause in the resulting SQL query.
  * Forms – [Forms](/documentation/business-users/digital-marketing/forms) with the _reCAPTCHA_ form component couldn’t be submitted in certain cases. The issue occurred if the form was placed on a website channel configured to use _reCAPTCHA version 3_ and the form’s code name contained a `.` period character.
  * Project templates – The `_LandingPageLayout.cshtml` layout in the _Dancing Goat_ project template didn’t include [activity logging scripts](/documentation/developers-and-admins/digital-marketing-setup/set-up-activities). As a result, activity logging didn’t work on the resulting site’s _Coffee samples_ landing page. The issue is only resolved for new projects created after [updating project templates](/documentation/developers-and-admins/installation/update-xperience-by-kentico-projects).
  * Rich text editor – When using the _Insert link → Asset_ option of the rich text editor, the _Open in new tab_ option didn’t work correctly if the content item asset’s file name contained the `@` character.
  * Workflow – Deleting, creating and reordering a [workflow’s](/documentation/developers-and-admins/configuration/workflows) steps could in certain cases cause the steps to be displayed in the wrong order in the _Change workflow_ step dialog (when moving pages, emails or items between steps). The update prevents the issue from occurring, but any affected workflows need to be fixed manually after the update – drag the workflow’s last step to the first position and then back again. This ensures correct recalculation of the order for all steps.


* * *
### Hotfix (May 9, 2024)
**version 29.0.3**
Fixed issues
  * Content management – Items selected in the [content item selector](/documentation/developers-and-admins/customization/extend-the-administration-interface/ui-form-components/reference-admin-ui-form-components#combined-content-selector) weren’t saved if they were filtered out by a search in the selection dialog.
  * Pages – Custom [forbidden URL characters](/documentation/developers-and-admins/development/routing/configure-forbidden-url-characters) were not replaced and remained in the [URL slugs](/documentation/business-users/website-content/manage-page-urls) of pages. The update fixes the issue for new URLs and introduces validation on the _URL slug_ field that checks for the forbidden characters. For existing pages, the forbidden characters need to be replaced manually.


* * *
### Hotfix (May 2, 2024)
**version 29.0.2**
Updates and changes
  * Integrations – The [Xperience by Kentico Zapier](https://github.com/Kentico/xperience-by-kentico-zapier) integration that was introduced in preview mode with refresh 28.4.0 is now fully supported.


Fixed issues
  * Field editor – Toggling the _Display in editing form_ checkbox offered when editing [content types](/documentation/developers-and-admins/development/content-types) on the _Fields_ tab in the _Content hub_ application and then attempting to save the changes triggered validation errors for other required fields in the configuration, even when their values were set correctly. This made it impossible to save any changes.
  * Headless items – Deleting a [headless item](/documentation/business-users/headless-content) from its detailed view incorrectly required the _Update_ [permission](/documentation/developers-and-admins/configuration/users/role-management) instead of _Delete_.


* * *
### Hotfix (April 25, 2024)
**version 29.0.1**
New features
The `CMS.Base.ContextUtils` class was introduced into the public API. The class’s `PropagateCurrent` and `ResetCurrent` methods allow developers to propagate or clear the system’s thread context, such as the database connection, when executing asynchronous or parallel code (for example using `Task.Run`). See [Integrate custom code](/documentation/developers-and-admins/customization/integrate-custom-code#database-connection-issues-when-scheduling-parallel-or-asynchronous-work-from-external-libraries).
Fixed issues
  * Admin UI – The [Form submissions tab](/documentation/business-users/digital-marketing/forms/manage-form-submissions) in the _Forms_ application didn’t display a scrollbar if the form contained a large number of submissions. This prevented users from viewing all submissions.
  * Admin UI customization – Actions handled by custom components added via `Page.PageConfiguration.TableActions.AddActionWithCustomComponent` to [listing page](/documentation/developers-and-admins/customization/extend-the-administration-interface/ui-pages/reference-ui-page-templates/listing-ui-page-template) table rows did not work correctly and logged errors in the event log.
  * Content management
    * Publishing a content item, page, email or headless item with fields [linking](/documentation/business-users/content-hub/content-items#link-content-items) to other content items could result in high CPU utilization on the client and lead to a frozen UI in some cases (“Maximum update depth” warnings were logged in the browser console).
    * While reordering items within a [content item selector](/documentation/developers-and-admins/customization/extend-the-administration-interface/ui-form-components/reference-admin-ui-form-components#combined-content-selector), the order of content items could accidentally be changed in another selector for the same content type. This was possible by dragging an item from “selector A” over items in “selector B”. 
  * Form Builder – The _Radio buttons_ , _Drop-down list_ , and _Checkboxes_ [Form Builder form components](/documentation/developers-and-admins/development/builders/form-builder/reference-form-builder-components) displayed incorrect UI for their properties. The issue was present only after applying hotfix 28.3.1 or later.
  * Pages – When a page contained both an image and a special character (e.g., `'`, `"`, `&`, `<` or `>`) in a [rich text field](/documentation/business-users/rich-text-editor), a dialog notifying about unsaved changes was incorrectly displayed when leaving the page after saving the changes or when publishing the page.
  * Rich text editor
    * After applying the 29.0.0 update, the [rich text editor’s toolbar](/documentation/business-users/rich-text-editor#format-text) was missing if the `toolbarButtons` option was not included in the [Customize the editor](/documentation/developers-and-admins/configuration/rich-text-editor-configuration).
    * If multiple content types in the system had a _Content item asset_ field with the same name, an error occurred when displaying the rich text editor, and the editor’s _Insert image_ dialog didn’t work. 
  * SaaS deployment – Content editors were not able to save text content with code snippets in projects hosted in the SaaS environment. WAF rules of the Cloudflare CDN were modified for POST requests originating from the administration interface to allow content with code snippets. Any POST requests originating from outside the administration interface (e.g., form submissions) must still adhere to a strict set of rules.


* * *
## Refresh (April 18, 2024)
**version 29.0.0**
### New features
**Content hub folders**  
The _Content hub_ application now allows users to create a hierarchy of [folders](/documentation/business-users/content-hub/content-hub-folders) for organizing content items. The purpose of folders is to help efficiently navigate through content items in the administration interface. However, these folders cannot be used to control content delivery or filter content on the live website.
**Mass publishing in Content hub**  
The _Content hub_ application now supports [mass publishing](/documentation/business-users/content-hub/content-items#mass-publishing) of content items.
**Scope content items offered by content item selector by reusable field schemas**  
The [content item selector](/documentation/developers-and-admins/customization/extend-the-administration-interface/ui-form-components/reference-admin-ui-form-components#combined-content-selector) UI form component can now filter content items according to assigned reusable field schemas. Content item selectors configured via the [field editor](/documentation/developers-and-admins/customization/field-editor) can now select the _Allowed reusable field schema_ option. For model-driven forms such as widget property dialogs, implement the `IReusableFieldSchemasFilter` interface and assign it using the `ContentItemSelectorComponent` attribute. See the [content item selector documentation](/documentation/developers-and-admins/customization/extend-the-administration-interface/ui-form-components/reference-admin-ui-form-components#combined-content-selector) for an example.
When retrieving fields scoped by reusable field schemas via content item query, use the `OfReusableSchema` parameterization.
When retrieving fields scoped by reusable field schemas via GraphQL, use _[inline fragments](https://graphql.org/learn/queries/#inline-fragments)_ to access the properties of the underlying type. See, [Retrieve headless items](/documentation/developers-and-admins/development/content-retrieval/retrieve-headless-content#basic-querying).
**Ordering in the content item and headless item selectors**  
The selectors now support a drag-and-drop ordering mechanism for easily reordering linked items, preserving the selected order. Both the minimum and maximum number of selectable items can now also be set. This new feature enables a substantial change in the approach to content modeling, as you can now use [reusable content items](/documentation/business-users/content-hub) instead of pages without URLs to model strictly-ordered hierarchies, including [website navigation](/guides/development/developer-kickstart/model-navigation). This new approach helps declutter the website channel content tree and promotes better reusability of your content model.
**Content item code name generation API**  
Developers can now use the `CMS.ContentEngine.IContentItemCodeNameProvider` API to generate globally unique code names for content items programmatically.
**Updated automapping support for reusable field schema**  
This update introduces the option to specify which model class a given content type referenced under a reusable field schema maps to when using `IContentQueryExecutor.GetMappedResult<TModel>` or `IContentQueryResultMapper.Map`.
```
// Maps content types of type 'Laptop.CONTENT_TYPE_NAME' from the collection of items
// referenced by the 'IComputer' schema to the 'Laptop' model class.
[MapContentTypeReferenceTo(Laptop.LAPTOP_CLASS_NAME, typeof(Laptop))]

// Maps content types of type 'Desktop.CONTENT_TYPE_NAME' from the collection of items
// referenced by the 'IComputer' schema to the 'Desktop' model class.
[MapContentTypeReferenceTo(Desktop.DESKTOP_CLASS_NAME, typeof(Desktop))]

// Other content types map to the 'Computer' model class
[MapReferenceTo(typeof(Computer))]

// Property representing reusable field schema references
public IEnumerable<IComputer> Computers { get; set; }
```

When no mapping preferences are specified, the system defaults first to the type of the property representing the reusable schema and finally to the type registered via `RegisterContentTypeMappingAttribute`.
**Expanded reusable field schema data type support**  
Fields defined for [reusable field schemas](/documentation/developers-and-admins/development/content-types/reusable-field-schemas) can now use the following [data types](/documentation/developers-and-admins/customization/field-editor/data-type-management):
  * Content items – content item references (content item [selector](/documentation/developers-and-admins/customization/extend-the-administration-interface/ui-form-components/reference-admin-ui-form-components#combined-content-selector))
  * Media files – media library files (media file selector [form component](/documentation/developers-and-admins/customization/extend-the-administration-interface/ui-form-components/reference-admin-ui-form-components))
  * Object code names and global identifiers (GUIDs) – references to objects managed by the system (object [selector](/documentation/developers-and-admins/customization/extend-the-administration-interface/ui-form-components/reference-admin-ui-form-components#object-selector))
  * Pages – web page references (page [selector](/documentation/developers-and-admins/customization/extend-the-administration-interface/ui-form-components/reference-admin-ui-form-components#page-selector))
  * Taxonomy – a collection of [tags](/documentation/developers-and-admins/configuration/taxonomies) that belong to taxonomies (tag [selector](/documentation/developers-and-admins/customization/extend-the-administration-interface/ui-form-components/reference-admin-ui-form-components#tag-selector))
  * Time interval
  * Unique identifier (GUID)


**Expanded email data type support**  
Fields defined in [content types](/documentation/developers-and-admins/development/content-types) for [emails](/documentation/business-users/digital-marketing/emails) can now use the following [data types](/documentation/developers-and-admins/customization/field-editor/data-type-management). The data types are also supported by macros in [email templates](/documentation/developers-and-admins/digital-marketing-setup/email-templates).
  * Media files
  * Object code names
  * Object global identifiers
  * Pages
  * Time interval
  * Unique identifier (GUID)


**Create pages and folders directly in the content tree**  
Users can now [create new pages](/documentation/business-users/website-content/create-pages) and folders directly in the content tree of website channel applications.
### Updates and changes
  * Code generators – Interfaces [generated for reusable field schemas](/documentation/developers-and-admins/api/generate-code-files-for-system-objects#reusable-field-schema-interfaces) now contain the code name of the schema under the `REUSABLE_FIELD_SCHEMA_NAME` constant.
  * Content item API 
    * Web page content items retrieved via [content item query](/documentation/developers-and-admins/api/content-item-api/content-item-query-api) now contain the identifier of their corresponding [website channel](/documentation/developers-and-admins/configuration/website-channel-management) in the `WebPageItemWebsiteChannelId` property.
    * Parts of the content item creation API (`CreateContentItemParameters`, `CreateWebPageParameters`, and `CreateFolderParameters`) were extended with additional overloads that no longer require the code name parameters explicitly and instead use `IContentItemCodeNameProvider` internally to generate one. See the reusable content item page and [API examples](/api/) for usage examples.


### New object types for CI/CD
The update introduces the following object types, which are supported by the [Continuous Integration](/documentation/developers-and-admins/ci-cd/continuous-integration) and [Continuous Deployment](/documentation/developers-and-admins/ci-cd/continuous-deployment) features. Consider updating the [repository.config](/documentation/developers-and-admins/ci-cd/configure-ci-cd-repositories) files of your CI/CD repositories, particularly when using the `<IncludedObjectTypes>` allowlist for object type filtering.
  * cms.contentfolder – stores [folders](/documentation/business-users/content-hub/content-hub-folders) used to organize items in the _Content hub_ application. Required for all projects to ensure that the content hub works correctly.


### Breaking changes
**Regular email rescheduling permissions**  
In order to reschedule the sendout of [regular emails](/documentation/business-users/digital-marketing/emails/send-regular-emails-to-subscribers) in [email channel](/documentation/developers-and-admins/digital-marketing-setup/email-channel-management) applications, users must now belong to a [role](/documentation/developers-and-admins/configuration/users/role-management) with the _Send email_ permission for the given application.
Resolution: Add the _Send email_ permission to all [roles](/documentation/developers-and-admins/configuration/users/role-management) that need to be able to configure when regular emails are sent.
**Content hub URLs**  
The URLs of administration UI pages for managing individual items in the _Content hub_ application newly include a segment representing the location of the given item. The location value can either be the ID of the folder containing the given item (highlights the folder in the content hub tree) or the all constant (displays the item under “All content items”, is valid for all items).
URL path format: _/admin/content-hub/ <language>/**< location>**/list/<item ID>/<UI page>_ Example: _/admin/content-hub/en/**all** /list/19/content_
Resolution: Update any stored or hardcoded URLs targeting content item admin UI pages to include the location segment. Use the _all_ location to preserve full backward compatibility.
If you have any [admin UI customizations](/documentation/developers-and-admins/customization/extend-the-administration-interface) that [generate links](/documentation/developers-and-admins/customization/extend-the-administration-interface/ui-pages#generate-urls-to-other-administration-pages) to content item UI pages, add the location parameter to your `IPageUrlGenerator.GenerateUrl` calls (the order of the URL parameters is significant). Use the `ContentHubSlugs.ALL_CONTENT_ITEMS` constant to generate “All content items” links, which are valid for any content item.
```
// Service obtained using dependency injection
private readonly IPageUrlGenerator pageUrlGenerator;

//...

pageUrlGenerator.GenerateUrl<ContentItemEdit>(languageName, ContentHubSlugs.ALL_CONTENT_ITEMS, contentItemId.ToString());
```

If you need to generate links that preserve the currently selected folder when linking within the _Content hub_ application, use the `PageParameter` attribute to retrieve and bind the folder parameter from the URL.
```
[PageParameter(typeof(StringPageModelBinder), typeof(ContentHubFolder))]
public string FolderId { get; set; }
```

Then add the retrieved value to the `GenerateUrl` parameters in place of the `ContentHubSlugs.ALL_CONTENT_ITEMS` constant.
### .Internal namespace API changes
Certain parts of the public API could not safely be used by developers, because they contained members from `.Internal namespaces`, which are not allowed for public use. These inconsistencies were fixed by moving API members between `.Internal` and public namespaces, or in certain cases by making members obsolete.
The following list covers impactful changes for public API scenarios – members that were moved from an `.Internal` namespace to a public one. To resolve potential issues in your code, remove the _.Internal_ suffix from the given namespaces in `using` statements.
  * From `CMS.Automation.Internal` to `CMS.Automation`
    * `AutomationEmail` – type representing [custom form autoresponder emails](/documentation/developers-and-admins/digital-marketing-setup/custom-form-autoresponder-emails).
  * From `CMS.Base.Internal` to `CMS.Base`
    * `HttpCacheability`, `HttpCacheRevalidation` – used by properties of _CMS.Base.Routing.CMSActionResult_ , the base class of the system’s HTTP handler return type.
  * From `CMS.DataEngine.Internal` to `CMS.DataEngine`
    * `GuidItem` – used as an argument of _CMS.DataEngine.SqlHelper.BuildGuidTable_.
    * `StringItem` – used as an argument of _CMS.DataEngine.SqlHelper.BuildStringTable_.
  * From `CMS.Headless.Internal` to `CMS.Headless`
    * `HeadlessTokenInfo` – _Info_ class representing [headless channel API keys](/documentation/developers-and-admins/configuration/headless-channel-management#manage-api-keys).
  * From `Kentico.Content.Web.Mvc.Internal` to `Kentico.Content.Web.Mvc`
    * `ComponentFilterRegistrar` – type used in the [page template](/documentation/developers-and-admins/development/builders/page-builder/page-templates-for-page-builder/page-template-filtering) and [form component filtering](/documentation/developers-and-admins/development/builders/form-builder/form-components/form-component-filtering) API.
    * `FormAfterSubmitModeEnum` – enum type representing possible actions after a user submits a form using the _Form_ [Page Builder widget](/documentation/developers-and-admins/development/builders/page-builder/reference-default-page-builder-widgets). Not used in common scenarios, but could be required for certain _Form_ widget customizations.


### Newly obsolete API
Info provider interfaces in the `CMS.ContactManagement` namespace were replaced by the generic `IInfoProvider<TInfo>` [provider](/documentation/developers-and-admins/api/database-table-api#generic-provider-class-iinfoprovidertinfo) and made obsolete.
  * IAccountContactInfoProvider
  * IAccountInfoProvider
  * IAccountStatusInfoProvider
  * IContactRoleInfoProvider
  * IContactStatusInfoProvider
  * IContactInfoProvider
  * IVisitorToContactInfoProvider
  * IContactGroupInfoProvider
  * IContactGroupMemberInfoProvider


### Fixed issues
  * AIRA – Content items with custom fields of type _Text_ assigned exclusively via reusable field schemas were unavailable when selecting items as the content source in the _Generate with AI_ dialog for a field using the [generate email content](/documentation/business-users/aira#generate-email-content) AI feature.
  * Admin UI – The text of some action confirmation messages in the administration was adjusted to be more accurate.
  * CI/CD – The Continuous Integration repository didn’t correctly maintain the latest state of content item relationships (references to other content items via the _Content items_ [data type](/documentation/developers-and-admins/customization/field-editor/data-type-management)) that were edited via the admin UI. Instead, for all operations done through the admin UI that modify content item relationships (adding and removing content items via the [content item selector](/documentation/developers-and-admins/customization/extend-the-administration-interface/ui-form-components/reference-admin-ui-form-components#combined-content-selector)), the repository maintained the state immediately preceding the latest operation. Relationships assigned programmatically via the API were unaffected. After applying this refresh update, projects using CI/CD should follow the steps outlined in the [update instructions](/documentation/developers-and-admins/installation/update-xperience-by-kentico-projects#update-development-projects-with-continuous-integration) to get their repository to the correct state.
  * Content hub – Attempting to edit a nonexistent content item via the content item edit page (e.g., by directly accessing the edit page URL with invalid parameters) returned an HTTP 500 error instead of the system error page.
  * Content item API – The `LinkedFrom` and `Linking` [content item query parameterization](/documentation/developers-and-admins/api/content-item-api/content-item-query-api) methods returned a `NullReferenceException` when the provided content type didn’t exist. The exception type was changed to `ArgumentException`, which is more suitable for the reported error.
  * Content management – The [cascade publishing](/documentation/business-users/content-hub/content-items#cascade-publishing) dialog didn’t prevent selection of linked content items that would then fail to publish due to workflow restrictions. The issue occurred for items under workflow if the current user didn’t belong to a role that was allowed to work with the last step in the workflow.
  * Languages
    * Headless channel applications always opened in their [primary language](/documentation/developers-and-admins/configuration/headless-channel-management#create-headless-channels) instead of the language most recently selected in their [language selector](/documentation/business-users/headless-content#translate-headless-items).
    * When a user opened a website channel application for the first time, the application loaded in the system’s [default language](/documentation/developers-and-admins/configuration/languages#set-up-a-new-language) instead of the channel’s [primary language](/documentation/developers-and-admins/configuration/website-channel-management#create-website-channels). 
  * Pages
    * The value of the `CacheVaryByConstants.ROUTE_URL_SLUG` constant was incorrect. The constant stores the name of the route parameter that holds the URL slug value for page routes, and can be used to vary the [output cache of pages](/documentation/developers-and-admins/development/caching/output-caching#page-template-content) based on their URLs.
    * When directly publishing new pages with one or more _Content item selector_ fields, a `TaskCanceledException` error occurred and was displayed in the system’s [event log](/documentation/developers-and-admins/configuration/event-log). However, the error did not prevent the page from being published or cause any functional issues. 
  * Rich text editor – When using the _Insert link → Web URL_ option of the [rich text editor](/documentation/business-users/rich-text-editor) with content spanning the entire height of the content editing area, the pop-up for entering the URL was anchored to the bottom of the page and could not be filled out.


* * *
### Hotfix (April 11, 2024)
**version 28.4.4**
Fixed issues
  * Taxonomies – The placeholder that appears in the taxonomy tree when creating tags in the _Taxonomies_ application was displayed incorrectly in certain circumstances.


* * *
### Hotfix (April 4, 2024)
**version 28.4.3**
Fixed issues – security
  * Users – Regular users with the _Create_ permission for the _Users_ application were allowed to assign the _Administrator_ [role](/documentation/developers-and-admins/configuration/users/role-management) to newly invited users.


Fixed issues
  * Page Builder – Custom HTML elements surrounding Page Builder [widget zones](/documentation/developers-and-admins/development/builders/page-builder/create-pages-with-editable-areas) in the administration were incorrectly removed in certain cases.


* * *
### Hotfix (March 28, 2024)
**version 28.4.2**
Fixed issues
  * Admin UI
    * A validation message in the _Rename content item_ dialog was not displayed correctly.
    * Exceedingly long content item names could overflow on the **Content** tab.
    * Exceedingly long language names could overflow in the language selector.
    * The tag selector dialog incorrectly expanded certain tags in the tag tree. 
  * CI/CD – After separately [updating](/documentation/developers-and-admins/installation/update-xperience-by-kentico-projects) the databases of multiple instances synchronized via [CI/CD](/documentation/developers-and-admins/ci-cd) from versions 22.0-26.6 to version 27.0 or later, the system could no longer match existing Former URLs during CD restore. This led to duplicate Former URL entries in the target database after running the restore command. The hotfix also deletes any already existing Former URL duplicates in the project database.
  * UI form components – Properties for the minimum and maximum number of selected tags for the `TagSelectorComponent` [editing component](/documentation/developers-and-admins/customization/extend-the-administration-interface/ui-form-components/reference-admin-ui-form-components#tag-selector) were introduced.
  * Xperience Portal – The status of custom channel domains with invalid DNS records was incorrectly displayed as _Error_ instead of _Validation failed_.


**Recommended manual steps for self-hosted environments**
Some deployment environments, like [Azure Web Apps](https://azure.microsoft.com/en-us/products/app-service/web/), don’t provide a persistent file system. This can potentially lead to a loss of binary files like [media libraries](/documentation/business-users/media-libraries), [content item assets](/documentation/business-users/content-hub/content-item-assets), or files uploaded via the _Upload file_ [form component](/documentation/developers-and-admins/development/builders/form-builder/reference-form-builder-components) when redeploying the application, swapping slots, switching servers, etc. The possible loss can be prevented by mapping and storing the files in persistent shared file storage like [Azure Blob](/documentation/developers-and-admins/api/files-api-and-cms-io/file-system-providers/azure-blob-storage) or [Amazon S3](/documentation/developers-and-admins/api/files-api-and-cms-io/file-system-providers/amazon-s3).
Therefore, if you:
  * Use [self-managed deployments](/documentation/developers-and-admins/deployment/deploy-to-private-cloud) in environments without a persistent file system (e.g., [Azure Web Apps](https://azure.microsoft.com/en-us/products/app-service/web/) )
  * Don’t have the following folders mapped to shared storage: 
    * **~/assets** folder containing content item assets and media library files
    * **~/BizFormFiles** folder containing files uploaded from users using the _Upload file_ [form component](/documentation/developers-and-admins/development/builders/form-builder/reference-form-builder-components) (if you use [Forms](/documentation/developers-and-admins/development/builders/form-builder) with the given component)


We recommend taking the following actions:
  1. Add [mappings](/documentation/developers-and-admins/api/files-api-and-cms-io/file-system-providers) for the unmapped folders.
  2. Obtain the files from the unmapped folders in your deployed project and manually upload them to the shared storage. Depending on your deployment environment you might be able to access the files directly on the file system. If this isn’t possible, you can download the files manually (e.g., the files uploaded through forms are stored in each form’s submissions).


* * *
### Hotfix (March 22, 2024)
**version 28.4.1**
Fixed issues
  * General – An error occurred when building version 28.4.0 projects using the .NET 8.0 Target framework. This also prevented NuGet package updates to 28.4.0 for projects targeting .NET 8.0.


* * *
## Refresh (March 21, 2024)
**version 28.4.0**
### New features
**Taxonomies support**
[Taxonomies](/documentation/developers-and-admins/configuration/taxonomies) provide users with the option to tag content. Tags can be assigned to individual pieces of content for multiple purposes including but not limited to modeling relationships, categorization, and organization and classification of data. Example uses include:
  * Administrators can [create taxonomies](/documentation/developers-and-admins/configuration/taxonomies#configure-taxonomies) and fill them with hierarchically structured tags.
  * Content editors can add tags to [pages](/documentation/business-users/website-content/edit-and-publish-pages#add-tags-to-pages) and [reusable content items](/documentation/business-users/content-hub/content-items#add-tags-to-content-items).
  * Developers can [retrieve and display](/documentation/developers-and-admins/configuration/taxonomies#retrieve-taxonomies-and-tagged-content) content based on tags.


**Query content items without specifying content type names**
In previous versions of Xperience, retrieving content items via content item query required explicitly stating the content item type as a parameter of the `ForContentType` [method](/documentation/developers-and-admins/api/content-item-api/reference-content-item-query#Builder-ForContentType). Any implementation that relied on dynamic item selection (e.g., via [selector components](/documentation/developers-and-admins/customization/extend-the-administration-interface/ui-form-components/reference-admin-ui-form-components#selectors)) often had to restrict its functionality to a predefined set of content page or resort to various workarounds due to this limitation.
This release extends content item query with options to query content items without prior knowledge of their type. The available parameterization alternatives include:
  * Assigned [reusable field schemas](/documentation/developers-and-admins/development/content-types/reusable-field-schemas) – selects items from content types with the specified reusable schema assigned. Selection can be further limited using conventional _WHERE_ conditions.
  * Content item identifiers, GUIDs, or code names (for website pages) – selects items that match the provided collection of identifiers (e.g., via the [Page selector](/documentation/developers-and-admins/customization/extend-the-administration-interface/ui-form-components/reference-admin-ui-form-components) component).
  * A list of content types – selects items that match the provided content type names. Doesn’t require repeated `ForContentType` calls.


The new querying API is available via the `ForContentTypes` [method](/documentation/developers-and-admins/api/content-item-api/reference-content-item-query#Builder-ForContentTypes) on `ContentItemQueryBuilder`. See [Reference - Content item query](/documentation/developers-and-admins/api/content-item-api/reference-content-item-query) for details.
**Enhancements to content item query result mapping**
The process of mapping database data returned by content item queries to objects used by the live site was updated to address the requirements introduced by the querying improvements [outlined above](#QueryImprovements-2840).
The existing implementation using `IContentQueryExecutor.GetResult<TModel>` (`GetWebPageResult` for pages) requires developers to pattern-match based on content type names and assign appropriate models matching the data. This is especially noticeable when working with result sets comprised of multiple content types:
```
// Mapping a result set containing multiple content types
await contentQueryExecutor.GetResult<IContentItemFieldsSource>(builder, dataContainer =>
{
    // Pattern-matching to suitable model classes
    switch(dataContainer.ContentTypeName)
    {
      case Coffee.CONTENT_TYPE_NAME:
          return contentQueryResultMapper.Map<Coffee>(dataContainer);
      case Event.CONTENT_TYPE_NAME:
          return contentQueryResultMapper.Map<Event>(dataContainer);
      default:
          throw new Exception("Attempting to map an unknown type.");
    }
});
```

While useful for advanced use cases, this approach leads to unnecessary boilerplate code in the majority of common scenarios. It also relies on prior knowledge of content types comprising the result set.
This version update introduces new query execution API – `IContentQueryExecutor.GetMappedResult<TModel>` (`GetMappedWebPageResult` for pages). This API abstracts the mapping function present in `GetResult<TModel>` and directly returns saturated model classes.
For result sets comprised of a single content type, you now omit the mapping function entirely and call:
```
var builder = new ContentItemQueryBuilder();
builder.ForContentType(Article.CONTENT_TYPE_NAME);

// 'GetMappedWebPageResult' directly returns an enumeration of types specified by the generic parameter
IEnumerable<Article> articles =
            await contentQueryExecutor.GetMappedWebPageResult<Article>(builder);
```

Result sets that are comprised of two or more content types or that retrieve objects based on [reusable field schemas](#QueryImprovements-2840) can also omit all pattern-matching within the mapping logic and work directly with instances of the corresponding model classes:
```
var builder = new ContentItemQueryBuilder();
// Uses the newly introduced API to retrieve multiple content types
builder.ForContentTypes(query =>
{
    query.OfContentType(Article.CONTENT_TYPE_NAME, Blog.CONTENT_TYPE_NAME);
    query.WithContentTypeFields();
});

// Gets a mixed collection of articles and blogs.
// The 'TModel' generic in GetMappedWebPageResult must cast the result set to a type shared by all models.
// This example uses 'IContentItemFieldsSource' - implemented by all generated model classes - as the shared type.
IEnumerable<IContentItemFieldsSource> result =
        await executor.GetMappedWebPageResult<IContentItemFieldsSource>(builder);

// Gets all articles
List<Article> articles = result.OfType<Article>().ToList();
// Gets all blogs
List<Blog> blogs = result.OfType<Blog>().ToList();
```

The system determines which model class to instantiate for each content type based on the `RegisterContentTypeMapping` attribute used to annotate model classes. The attribute is, by default, present in all [generated classes](/documentation/developers-and-admins/api/generate-code-files-for-system-objects). Each content type can be associated with exactly one model class via the attribute. To use the `GetMappedResult` methods in existing projects, **regenerate all model classes** to ensure the attribute is present in all models.
Additionally, as part of these changes, `IContentQueryResultMapper` and `IWebPageQueryResultMapper`, used by the mapping logic within `GetResult<TModel>`, were unified to offer identical functionality for all content item types (reusable, pages). Going forward, we recommend using `IContentQueryResultMapper` for all custom mapping operations. Related scenarios covered in the documentation were updated to reflect the new recommendation.
**UI form components**
  * You can now provide multiple dependency fields to a [form component configurator](/documentation/developers-and-admins/customization/extend-the-administration-interface/ui-form-components/editing-components/configure-editing-component-state), enabling you to create more complex rules and conditions for your editing components. For example:
```
[FormComponentConfiguration(typeof(MyConfigurator),
                            new[] { nameof(FirstDependencyField),
                                    nameof(SecondDependencyField) })]
```

  * Developers can now access the `ContentTypeID` and `IsSecured` properties in `IWebPagePanelItemModifier` implementations, making it possible to disable pages in the [Page selector](/documentation/developers-and-admins/customization/extend-the-administration-interface/ui-form-components/reference-admin-ui-form-components#page-selector) form component based on their [content type](/documentation/developers-and-admins/development/content-types) and [access privileges](/documentation/business-users/website-content/secure-pages). For example:
```
public class SecuredArticlePagesPanelItemModifier : IWebPagePanelItemModifier
{
    public WebPagePanelItem Modify(WebPagePanelItem pagePanelItem, WebPagePanelItemModifierParameters itemModifierParams)
    {
        string contentTypeCodeName = DataClassInfoProvider.GetClassName(itemModifierParams.WebPageMetadata.ContentTypeID);

        // Disables selection of secured pages of the 'acme.article' type
        if (string.Equals(contentTypeCodeName, "acme.article", System.StringComparison.OrdinalIgnoreCase) && itemModifierParams.WebPageMetadata.IsSecured)
        {
            pagePanelItem.SelectableOption.Selectable = false;
            pagePanelItem.SelectableOption.UnselectableReason = "Cannot select secured 'acme.article' pages";
        }

        return pagePanelItem;
    }
}
```



**Integrations**
  * CRM – The [Xperience by Kentico CRM](https://github.com/Kentico/xperience-by-kentico-crm) integration was updated to support bi-directional synchronization between [contacts](/documentation/business-users/digital-marketing/contact-management) in Xperience and Leads or Contacts in the connected CRM.
  * MiniProfiler – New external module that integrates Xperience by Kentico applications with [MiniProfiler](https://miniprofiler.com/). This integration allows developers to optimize database queries and reduce load times. For more information, see the [Xperience by Kentico MiniProfiler](https://github.com/Kentico/xperience-by-kentico-miniprofiler) GitHub repository.
  * Tag Manager – The [Xperience by Kentico Tag Manager](https://github.com/Kentico/xperience-by-kentico-tag-manager) integration was updated to provide support for the following additional code snippets:
    * [Microsoft Clarity](https://clarity.microsoft.com) Session recordings and Heatmaps
    * [VWO A/B testing](https://vwo.com/v2)
    * [Intercom chatbot](https://www.intercom.com/ai-chatbot)
    * [Google Analytics 4](https://marketingplatform.google.com)
  * Zapier – New external module that enables you to create [automated workflows](https://zapier.com/workflowsauto), or “Zaps,” that can trigger actions across different platforms based on events that occur in Xperience by Kentico. For example, you can trigger actions in Zapier when a new [contact](/documentation/business-users/digital-marketing/contact-management) is created or a content item’s [workflow](/documentation/developers-and-admins/configuration/workflows) step changes. In the other direction, you can set up actions in Xperience that are triggered by “Zaps” from other applications – store external data as a [form submission](/documentation/business-users/digital-marketing/forms/manage-form-submissions) or change the workflow step of a content item. This integration empowers users to streamline repetitive tasks, synchronize data, and improve productivity without requiring any coding knowledge. The integration is currently in preview, with full support coming soon. For more information, see the [Xperience by Kentico Zapier](https://github.com/Kentico/xperience-by-kentico-zapier) GitHub repository.


### Updates and changes
  * Content types and reusable field schemas – Improved duplicate column name detection was added to content type and reusable field schema fields. Content type field names must be unique within the content type and across all reusable field schemas. Reusable schema field names must be unique across all reusable schema and content type fields.
  * [Database table API](/documentation/developers-and-admins/api/database-table-api) – New `CMS.DataEngine.Provider<TInfo>` API for directly accessing `IInfoProvider<TInfo>` instances in code where dependency injection is not possible.
  * Website development – The `RoutedWebPage` type was extended by a new property containing the GUID of the currently accessed page. Developers can use this data to [retrieve the page](/documentation/developers-and-admins/development/content-retrieval/retrieve-page-content) and access its content fields. For example, when developing [widgets](/documentation/developers-and-admins/development/builders/page-builder/widgets-for-page-builder) or other [Page Builder](/documentation/developers-and-admins/development/builders/page-builder) components, `RoutedWebPage` data for the current page is accessible via `ComponentViewModel.Page`.


### New object types for CI/CD
The update introduces the following object types, which are supported by the [Continuous Integration](/documentation/developers-and-admins/ci-cd/continuous-integration) and [Continuous Deployment](/documentation/developers-and-admins/ci-cd/continuous-deployment) features. Consider updating the [repository.config](/documentation/developers-and-admins/ci-cd/configure-ci-cd-repositories) files of your CI/CD repositories, particularly when using the `<IncludedObjectTypes>` allowlist for object type filtering.
  * cms.taxonomy – stores [taxonomies](/documentation/developers-and-admins/configuration/taxonomies), i.e., collections of related tags.
  * cms.tag – stores individual taxonomy tags that can be used to organize content.


### Newly obsolete API
Info provider interfaces in the following namespaces were replaced by the generic `IInfoProvider<TInfo>` [provider](/documentation/developers-and-admins/api/database-table-api#generic-provider-class-iinfoprovidertinfo) and made obsolete.
_CMS.Activities_ :
  * IActivityInfoProvider
  * IActivityTypeInfoProvider


_CMS.EmailEngine_ :
  * IAttachmentForEmailInfoProvider
  * IEmailAttachmentInfoProvider
  * IEmailInfoProvider


_CMS.EmailMarketing_ :
  * IEmailBounceInfoProvider
  * IEmailConfigurationInfoProvider
  * IEmailLinkInfoProvider
  * IEmailStatisticsInfoProvider
  * IEmailStatisticsHitsInfoProvider
  * IEmailTemplateInfoProvider
  * IEmailSubscriptionConfirmationInfoProvider
  * IEmailMarketingRecipientInfoProvider
  * ISendConfigurationInfoProvider
  * IRecipientListSettingsInfoProvider


_CMS.Modules_ :
  * IResourceInfoProvider


_CMS.OnlineForms_ :
  * IBizFormInfoProvider
  * IFormFeaturedFieldInfoProvider
  * IBizFormRoleInfoProvider


_CMS.OnlineMarketing_ :
  * ITrackedWebsiteInfoProvider


### Fixed issues – security
  * Admin UI authentication – Antiforgery cookies used by the admin UI are now generated with the `secure` [attribute](https://owasp.org/www-community/controls/SecureCookieAttribute). We recommend clearing the cookies for your administration project domain after applying the refresh update to ensure the new cookie configuration is used.


### Fixed issues
  * Admin UI – Very long workflow or workflow step names were displayed incorrectly in various parts of the administration UI.
  * Content hub – A selected content item’s Properties panel didn’t show the content item’s type under Information.
  * SaaS environment
    * The Cloudflare CDN cache was not purged after deployment for projects with more than 30 domains in the environment.
    * The channel domain dropdown was not fully visible in the _Deployments_ application when a project contained a large amount of channels.
    * [Status monitoring](https://status.xperience-portal.com/) was added for the following regions:
      * Canada Central
      * East Asia (Hong Kong)
      * Germany West Central
      * Japan East
      * North Europe (Ireland)
      * UAE North 
  * Selector UI component
    * The _Data source value separator_ option (added in version 28.3.1) displayed an unresolved resource string value.
    * The semicolon character was not set as the default value of the _Data source value separator_ option in the UI for the _Dropdown selector_ and the _Radio group_ [Admin UI form components](/documentation/developers-and-admins/customization/extend-the-administration-interface/ui-form-components/reference-admin-ui-form-components), as well as for the _Radio buttons_ , _Drop-down list_ , and _Checkboxes_ [Form Builder form components](/documentation/developers-and-admins/development/builders/form-builder/reference-form-builder-components). 


* * *
### Hotfix (March 14, 2024)
**version 28.3.3**
Updates and changes
  * Website content – Special characters in [page URLs](/documentation/business-users/website-content/manage-page-urls) are now displayed in their decoded, user-friendly versions in the URL section on the Content tab.


Fixed issues
  * Rich text editor – The HTML sanitizer used in the [Rich text editor](/documentation/business-users/rich-text-editor) removed all entered CSS _@media_ rules.


* * *
### Hotfix (March 7, 2024)
**version 28.3.2**
Fixed issues
  * Code generators – When [generating code](/documentation/developers-and-admins/api/generate-code-files-for-system-objects) for a [binding class](/documentation/developers-and-admins/customization/object-types/object-type-configuration/model-object-type-relationships) that relates objects of the same object type, the code generator produced uncompilable code containing two identically named variables. After applying the hotfix, column names are used as variable names to ensure uniqueness for bindings that target the same object types.


* * *
### Hotfix (February 29, 2024)
**version 28.3.1**
New features
  * Form components – The _Dropdown selector_ and the _Radio group component_ [Admin UI form components](/documentation/developers-and-admins/customization/extend-the-administration-interface/ui-form-components/reference-admin-ui-form-components) as well as the _Radio buttons_ , _Drop-down list_ and _Checkboxes_ [Form Builder form components](/documentation/developers-and-admins/development/builders/form-builder/reference-form-builder-components) didn’t display their options correctly if the option text contained a semicolon. If you want to use semicolons in the value or text of the options, configure a different separator using the new _Data source value separator_ setting in the UI (or using the new `DataSourceValueSeparator` property when adding the components in code).


Fixed issues – security
  * Security – Fixed a vulnerability in the Page and Form Builder dependencies.


Fixed issues
  * Workflow 
    * If the API was used to directly create a published or archived content item, and the item’s content type had a workflow assigned, some of the new item’s metadata was set incorrectly (the `ContentItemLanguageMetadataContentWorkflowStepID` column in the `ContentItemLanguageMetadata` table).
    * When creating a new content item via the Content item selector component, the Create new item dialog incorrectly allowed users to directly Publish the new item even if the given content type was under page [workflow](/documentation/developers-and-admins/configuration/workflows). 


* * *
## Refresh (February 22, 2024)
**version 28.3.0**
### New features
Workflow
  * New workflow feature that allows you to define the lifecycle for [pages](/documentation/business-users/website-content), [reusable content items](/documentation/business-users/content-hub/content-items), [headless items](/documentation/business-users/headless-content) and [emails](/documentation/business-users/digital-marketing/emails). Each workflow consists of one or more custom steps between the default _Draft_ and _Published_ steps. This helps ensure the quality of content by setting up a reviewing and approval process tailored specifically for your organization. See [Workflows](/documentation/developers-and-admins/configuration/workflows).


Artificial intelligence
  * New [AI feature](/documentation/developers-and-admins/configuration/aira-configuration) for fields using the _Rich text editor_ form component, which allows users to refine selected pieces of text (make text shorter, improve spelling and grammar, etc.).


Image file endpoint protection
  * New feature that allows improved protection of the media library image file endpoint (`getmedia`) for requests with resize parameters. When this feature is enabled, a validation hash is required for any image file requests with resize query string parameters. This feature is enabled for new installations by default. See [Image file request protection](/documentation/developers-and-admins/configuration/media-library-configuration#image-file-request-protection) for more information.
  * **Note** : If image endpoint protection is enabled for an existing project updated from a previous version, you need to [update certain media file URLs in your content](/documentation/developers-and-admins/configuration/media-library-configuration#enable-image-file-request-protection-on-existing-projects) to ensure that the content is displayed correctly.


Integrations
  * Azure AI Search – An external module that enables you to create [Azure AI Search](https://azure.microsoft.com/en-us/products/ai-services/ai-search) indexes for the content of [pages](/documentation/business-users/website-content) from a website channel’s content tree using a code-first approach. The module is distributed as a NuGet package. For more information, see the [Xperience by Kentico Azure Search](https://github.com/Kentico/xperience-by-kentico-azure-ai-search) GitHub repository.


### Updates and changes
  * Added a unique index on the _ContentItemLanguageMetadata_ database table that consists of:
    * ContentItemLanguageMetadataContentItemID
    * ContentItemLanguageMetadataContentLanguageID
    * ContentItemLanguageMetadataLatestVersionStatus
  * SaaS environment
    * Automatic [predeployment backups](/documentation/developers-and-admins/deployment/deploy-to-the-saas-environment/manage-saas-deployments#automatic-restore-points-and-exports) are now available not only for the PROD environment, but also for QA and UAT.
    * The _Memory utilization_ alert is now triggered when the total memory usage of your [deployed projects](/documentation/developers-and-admins/deployment/deploy-to-the-saas-environment/manage-saas-deployments) reaches 90% instead of 80%.


### Newly obsolete API
  * Provider interfaces in the `CMS.DataProtection` namespace were replaced by the generic `IInfoProvider<TInfo>` provider and made obsolete. Affected interfaces: 
    * IConsentInfoProvider
    * IConsentArchiveInfoProvider
    * IConsentAgreementInfoProvider
  * Provider interfaces in the `CMS.MacroEngine` namespace were replaced by the generic `IInfoProvider<TInfo>` provider and made obsolete. Affected interfaces: 
    * IMacroIdentityInfoProvider
    * IUserMacroIdentityInfoProvider
    * IMacroRuleCategoryInfoProvider
    * IMacroRuleMacroRuleCategoryInfoProvider
    * IMacroRuleInfoProvider


### Fixed issues – security
The [System.IdentityModel.Tokens.Jwt](https://www.nuget.org/packages/System.IdentityModel.Tokens.Jwt/6.31.0) package (transitive dependency) was updated to the latest version.
### Fixed issues
  * Admin UI – The tile view of the applications list in the administration displayed an incorrect number of applications per row.
  * Contact management – The _Email bounces_ column in contact listings incorrectly displayed “OK” for contacts without a known email address. After the updated, a dash is displayed instead for such contacts.
  * Content items – All public methods of `IContentItemManager`, except for `GetContentItemMetadata` and `GetContentItemLanguageMetadata`, now check that the content item identifier provided as input refers to a reusable content item. The purpose of `IContentItemManager` is to facilitate manipulation with reusable content items. The fact that it allowed users to work with other types of content items was an oversight that could lead to data inconsistencies. To work with web page items, use `IWebPageManager`.
  * Headless tracking – If a new [custom activity type](/documentation/developers-and-admins/digital-marketing-setup/set-up-activities/custom-activities) was created or an existing type’s code name was changed, the [headless tracking API](/documentation/developers-and-admins/digital-marketing-setup/headless-tracking) didn’t reflect the change until the application’s cache was cleared.
  * Modules – Attempting to create new fields without database columns in UI forms without first specifying their data type resulted in an unhandled exception instead of failed form validation.
  * Page Builder – The scroll bar was not available in the Page selector editing component when used in widget properties if the page selected as root had a large number of subpages.
  * SaaS environment – In certain rare cases, the Cloudflare CDN firewall blocked the upload of media library files.
  * UI form components – The _Object code names selector_ component incorrectly allowed users to specify object types without a dedicated code name column.


* * *
### Hotfix (February 15, 2024)
**version 28.2.3**
Fixed issues – security
The hotfix updates the ‘Microsoft.Data.SqlClient’ library to version 5.1.5.
Fixed issues
  * Admin UI authentication – The `ExpireTimeSpan` property of `AdminIdentityOptions.AuthenticationOptions` was not being reflected when set via the `AdminIdentityOptions` object. For example:
```
builder.Services.Configure<AdminIdentityOptions>(builder.Configuration.GetSection(nameof(AdminIdentityOptions)));
```

With the corresponding _appsettings.json_ section:
```
"AdminIdentityOptions": {
    "Authenticationoptions": {
        "ExpireTimeSpan": "06:00:00"
    }
}
```

Instead, the property was only configurable via its encapsulating `AdminAuthenticationOptions` type. The hotfix ensures correct behavior and also logs a warning to the event log if it detects that the `ExpireTimeSpan` value is being set via `AdminAuthenticationOptions`.
  * CI/CD – An error occurred when restoring a CI/CD repository of a project with a different default language than English into a database different than the one from which the repository was stored.
  * Code generators – Since version 28.2.0, generating provider classes and interfaces for object types via the code generator utility was not possible. The problem was caused by the `--with-provider-class parameter`, added in 28.2.0, which was not being correctly reflected.


* * *
### Hotfix (February 8, 2024)
**version 28.2.2**
Fixed issues
  * SaaS deployment – The [Continuous Deployment](/documentation/developers-and-admins/ci-cd/continuous-deployment) configuration file of new projects for SaaS deployment (installed with the `--cloud` parameter) was missing the necessary configuration to ensure reusable field schema deployment to the SaaS environment. To fix this issue for existing projects, download the _Kentico.Xperience.Templates_ package from this hotfix, create a new boilerplate project and copy the generated Continuous Delivery configuration file from the `$CDRepository` folder to your project.


* * *
### Hotfix (February 1, 2024)
**version 28.2.1**
New features
  * UI form components – You can now deploy your projects [without the Xperience by Kentico administration](/documentation/developers-and-admins/deployment/deploy-to-private-cloud/deploy-without-the-administration) when you make use of [form component configurators](/documentation/developers-and-admins/customization/extend-the-administration-interface/ui-form-components/editing-components/configure-editing-component-state). This is now possible after registering each form component configurator under an identifier via the `RegisterFormComponentConfigurator` assembly attribute, and then using the `FormComponentConfiguration` attribute with its new `Identifier` property.


Fixed issues
  * API – When [generating content](/documentation/developers-and-admins/configuration/aira-configuration) for an email field, the AI panel displayed incorrect text for the quick refinement options below the generated text.
  * Admin UI – The _Save_ button displayed when editing headless items had an incorrect color schema.
  * CI/CD – Separately [updating](/documentation/developers-and-admins/installation/update-xperience-by-kentico-projects) the databases of multiple instances synchronized via [CI/CD](/documentation/developers-and-admins/ci-cd) from versions 22.0-26.6 to version 27.0 or later resulted in an inconsistent state of page URL _GUIDs_. This led to duplicate key errors during subsequent CI/CD restore operations, as the synchronized URLs had non-matching GUIDs. The hotfix only prevents the GUID inconsistencies when updating project databases to version 28.2.1 or newer (the issue is not resolved for projects where the inconsistencies are already present).
  * Content types – When adding reusable field schemas in the _Content types_ application, the checkbox in the side panel did not toggle to display a checkmark - indicating successful selection of a schema for addition - unless the checkbox element was selected directly. The schema field was still added after confirmation. After applying the hotfix, the checkbox toggles correctly even if a different area of a schema is selected in the listing.


* * *
## Refresh (January 25, 2024)
**version 28.2.0**
### New features
Reusable field schemas
  * Reusable field schemas enable the reuse of a collection of content type fields across multiple content types. A common use case is grouping a set of functionally related fields that often need to be duplicated across content types. For example, for search engine optimization purposes or social networking. See [Reusable field schemas](/documentation/developers-and-admins/development/content-types/reusable-field-schemas).
  * Reusable field schemas also introduce a new type of code file primitive – reusable schema interfaces. Each interface encapsulates fields defined by its corresponding schema. The code generator now supports generating these files via the `--type` `ReusableFieldSchemas` parameter. Generated content type classes implement these interfaces as needed based on the set of assigned reusable field schemas. See [Generate code files for system objects](/documentation/developers-and-admins/api/generate-code-files-for-system-objects#reusable-field-schema-interfaces).


Headless content preview
  * It’s now possible to retrieve headless items in their latest available version when using an [API key](/documentation/developers-and-admins/configuration/headless-channel-management) with the appropriate Access type. 
    * Your existing API keys will be automatically converted to the [Headless content - Published](/documentation/developers-and-admins/configuration/headless-channel-management#access-types-of-api-keys) Access type upon applying the update.
  * After setting up the [Preview URL](/documentation/developers-and-admins/configuration/headless-channel-management), you can now easily navigate to your preview environment straight from a headless channel application using the [Preview button](/documentation/business-users/headless-content).


Headless tracking
  * Headless tracking enables developers to [track contacts](/documentation/developers-and-admins/digital-marketing-setup/contact-configuration) and [log activities](/documentation/developers-and-admins/digital-marketing-setup/set-up-activities) for the audience of external content channels, such as mobile apps, single-page applications, etc. Xperience then serves as a central hub where you can monitor contacts and analyze activities for all connected websites and channels. See [Headless tracking](/documentation/developers-and-admins/digital-marketing-setup/headless-tracking).


Website development
  * The `RoutedWebPage` type was extended by new properties containing further information about the language, content type and website channel of the currently accessed page. Developers can use this data to [retrieve the page](/documentation/developers-and-admins/development/content-retrieval/retrieve-page-content) and access its content fields. For example, when developing [widgets](/documentation/developers-and-admins/development/builders/page-builder/widgets-for-page-builder) or other [Page Builder](/documentation/developers-and-admins/development/builders/page-builder) components, the `RoutedWebPage` `ComponentViewModel.Page`.


Content management
  * The _Content hub_ application now supports bulk deletion of content items. Select the set of content items to delete via selectors on the left-hand side of the listing.
  * Image thumbnails displayed in the user interface (e.g., in asset selector, content hub) now have a chessboard pattern as a background to improve the visibility of white images with a transparent background.


Emails
  * [Regular emails](/documentation/business-users/digital-marketing/emails/send-regular-emails-to-subscribers) sent to subscribed recipients now support one-click unsubscribe. The system automatically includes the `List-Unsubscribe-Post` and `List-Unsubscribe` headers in regular emails. If a recipient uses one-click unsubscribe, the resulting request is handled in the same way as a standard unsubscribe link in email content.


Admin UI customization
  * UI listing pages – [Listing UI pages](/documentation/developers-and-admins/customization/extend-the-administration-interface/ui-pages/reference-ui-page-templates/listing-ui-page-template) can now be extended with commands that apply to multiple items from the listing via the `PageConfiguration.MassActions` property.


File system providers
  * Amazon S3 – New configuration option that controls whether [Amazon S3](/documentation/developers-and-admins/api/files-api-and-cms-io/file-system-providers/amazon-s3) file system mappings fall back to the original local folder if a requested file cannot be found in Amazon S3. This fallback can cause issues when deploying files using the [Continuous Deployment](/documentation/developers-and-admins/ci-cd/continuous-deployment) restore process, for example during [deployment to the SaaS environment](/documentation/developers-and-admins/deployment/deploy-to-the-saas-environment). Developers can configure the option using the introduced `EnableFallbackToLocalFileSystem` property of `AmazonStorageOptions`. The default values are _true_ for self-managed projects and _false_ for SaaS deployments.
C#
**Program.cs**
Copy
```
using Kentico.Xperience.AmazonStorage;

var builder = WebApplication.CreateBuilder(args);

builder.Services.AddKentico( ... );

// Disables the fallback to the local file system
builder.Services.Configure<AmazonStorageOptions>(options => options.EnableFallbackToLocalFileSystem = false);
```



Integrations
  * CRM – An external module (a starter kit) that simplifies the integration of Xperience by Kentico with CRM software. The module also contains two packages that directly integrate Xperience by Kentico with [Microsoft Dynamics 365 Sales](https://www.microsoft.com/en-us/dynamics-365/products/sales) and [Salesforce Sales Cloud](https://www.salesforce.com/eu/products/sales-cloud/) CRM. The integration enables sending form submissions from Xperience to the CRM as leads and checking synchronization status in the Xperience administration. The starter kit and two integrations are distributed as NuGet packages. See the [Xperience by Kentico CRM](https://github.com/Kentico/xperience-by-kentico-crm) GitHub repository for details.
  * Tag Manager – An external module that allows marketers and editors to author and embed custom tags or code snippets into website channels. The module supports Google Tag Manager code snippets out of the box. The module is distributed as a NuGet package. See the [Xperience by Kentico Tag Manager](https://github.com/Kentico/xperience-by-kentico-tag-manager) GitHub repository for details.


### Updates and changes
Code generators
  * The `--type All` parameter used to generate code files for all supported objects in the system is now marked obsolete. Running the code generator utility with this parameter produces a warning in the output. The parameter’s behavior remains unchanged. However, it doesn’t support generation for reusable fields schemas introduced in this release and will output invalid code in projects that make use of this new feature. Use a combination of the existing parameters to generate equivalent output.
  * Code generators now provide the option to generate object type (module class) code files without the corresponding _I*Provider_ and _*Provider_ [classes](/documentation/developers-and-admins/api/database-table-api) via the `--with-provider-class` class parameter. The parameter defaults to _true_ – if not provided, object type classes are generated with their corresponding providers. If set to _false_ , only the _*Info_ class is generated. This option was added to support the use of the generic `IInfoProvider<TInfo>` provider class instead of dedicated providers per object type. See [Info class managers](/documentation/developers-and-admins/api/database-table-api#info-class-managers).


Data types
  * The _Date_ and _Long integer number_ data types are now available for the fields of [content types](/documentation/developers-and-admins/development/content-types) for [emails](/documentation/business-users/digital-marketing/emails).


Headless channels
  * Domains that are allowed for Cross-Origin Resource Sharing (CORS) by headless channel GraphQL endpoints can now be configured in the administration using the new _Settings > Content > Headless > Allowed origins_ setting. This setting overrides allowed domains set through the _CMSHeadless.CorsAllowedOrigins_ key in your application’s configuration file (_appsettings.json_ by default) or the _HeadlessOptions.CorsAllowedOrigins_ option in your project’s startup file (_Program.cs_). See [Retrieve headless content](/documentation/developers-and-admins/development/content-retrieval/retrieve-headless-content).


Page Builder
  * The _Vue.js_ framework used in the Page and Form Builders was updated to [version 3](https://v2.vuejs.org/lts/).


Xperience Portal
  * The  _DNS record configuration_ of custom channel domains in Xperience Portal now supports IPv6 AAAA records for apex domains.


### Fixed issues
  * Admin UI
    * In certain rare cases, the system could incorrectly display a confirmation dialog when selecting actions in listing pages that did not have a confirmation dialog defined.
    * The dialog for a new language variant added in the content hub was missing a caption and description.
    * The header of action columns in listing pages was missing the background. As a result, listing headers overlapped with the _Actions_ text.
    * When using the [binding UI page template](/documentation/developers-and-admins/customization/extend-the-administration-interface/ui-pages/reference-ui-page-templates/binding-ui-page-template), the confirmation dialog for removing items from the binding was hidden behind the editing side panel in some cases. 
  * Content hub – The _Properties → Information → Published_ status was displayed incorrectly for published content items. The issue occurred on versions 28.1.0 and newer.
  * Content item API – The behavior of column data retrieval via the `GetValue` and `TryGetValue` methods was unified for nullable and non-nullable types. If the methods do not find the requested column, they return the specified default value instead. Prior to this version, the methods returned `null` for nullable types and raised a `NullReferenceException` for non-nullable types. Developers could encounter this behavior when manually mapping the result of content item queries from `IContentQueryDataContainer` objects.
  * Forms – Calling `BizFormItemProvider.GetItem` terminated with a database exception for any combination of parameters. As a result, it was not possible to, for example, preview submitted form data via the _Forms_ application. This issue was introduced in version 28.1.1.
  * Headless API
    * Improved the error messages returned for various invalid states in GraphQL queries for [retrieving headless channel content](https://docs.xperience.io/xp/developers-and-admins/development/content-retrieval/retrieve-headless-content).
    * Null values were not accepted as valid for optional arguments of GraphQL query fields in headless content retrieval requests.
    * When retrieving linked headless items via GraphQL, the items were retrieved in a different order than they were displayed in the _Headless item selector_. 
  * Headless items – Improved UI texts and tooltips in the Headless item selector.
  * Media libraries – The error message displayed when attempting to upload files with non-allowed extensions to [media libraries](/documentation/business-users/media-libraries) was updated to contain accurate information.
  * Modules – An error occurred when attempting to delete a category in the field editor of a module class UI form. For example, the problem could occur when editing a UI form of a [customizable system class](/documentation/developers-and-admins/customization/object-types/extend-system-object-types) in the _Modules_ application.
  * Page Builder
    * The system did not serve the minified ‘systemFormComponents.min.js’ file correctly in the Page Builder scripts for the Form widget.
    * The widget and section selection dialog in the [Page Builder](/documentation/business-users/website-content/widgets-and-page-builder) interface was displayed incorrectly in certain cases. After applying the update, the dialog dimensions and layout are better modified to match the screen dimensions and the number of displayed items. 
  * Page templates
    * The Microsoft Application Insights client-side telemetry script was re-added to the Dancing Goat project template (_kentico-xperience-sample-mvc_) after being accidentally removed in version 27.0.0.
    * When implementing [page templates](/documentation/developers-and-admins/development/builders/page-builder/page-templates-for-page-builder) with a custom model, it was not possible to pass data to the template view using ViewBag, ViewData or TempData. 
  * Pages
    * After collapsing and subsequently expanding the URL section on the Content tab with an empty URL slug field, the UI did not display the validation error message.
    * After discarding changes made on the Content tab, the headers of form categories would disappear.
    * The URL section on the Content tab was incorrectly expanded by default on existing pages. 
  * Reusable content – When a content item was linked from two different fields of another content item and the user attempted to remove the reference from one of the fields, the system did not correctly remove the reference, leaving the item linked in both fields.
  * SaaS environment – An error occurred when accessing the root of a [SaaS environment](/documentation/developers-and-admins/deployment/deploy-to-the-saas-environment/manage-saas-deployments) domain that didn’t have a valid website channel running (e.g., a website channel domain before package deployment or a custom email channel service domain). Now the system displays an appropriate message without any errors.
  * UI form components – Expendable form categories containing fields with dependencies unexpectedly collapsed after such fields were changed.


* * *
### Hotfix (January 11, 2024)
**version 28.1.2**
Fixed issues
  * Content types – It was incorrectly possible to remove a content type from a channel’s _Allowed content types_ using the selector side panel, even if items of the given type already existed under the channel.
  * Rich text editor – On Linux environments (e.g., Azure App Service), projects using the .NET 8 framework could encounter an error when the system attempted to display a [rich text editor](/documentation/developers-and-admins/configuration/rich-text-editor-configuration) form component.
  * Update procedure – When [updating projects](/documentation/developers-and-admins/installation/update-xperience-by-kentico-projects) from version 27.0 (any hotfix version) to version 28.0.0 or later, the database update could fail unexpectedly if it encountered certain Page Builder configurations with slight inconsistencies.


* * *
### Hotfix (January 4, 2024)
**version 28.1.1**
New features
  * Microsoft Azure Storage – New configuration option that controls whether [Azure Storage](/documentation/developers-and-admins/api/files-api-and-cms-io/file-system-providers/azure-blob-storage) file system mappings fall back to the original local folder if a requested file cannot be found in Azure Storage. This fallback can cause issues when deploying files using the [Continuous Deployment](/documentation/developers-and-admins/ci-cd/continuous-deployment) restore process, for example during [deployment to the SaaS environment](/documentation/developers-and-admins/deployment/deploy-to-the-saas-environment). Developers can configure the option using the introduced `EnableFallbackToLocalFileSystem` property of `AzureStorageOptions`. The default values are _true_ for self-managed projects and _false_ for SaaS deployments.
C#
**Program.cs**
Copy
```
using Kentico.Xperience.AzureStorage;

var builder = WebApplication.CreateBuilder(args);

builder.Services.AddKentico( ... );

// Disables the fallback to the local file system
builder.Services.Configure<AzureStorageOptions>(options => options.EnableFallbackToLocalFileSystem = false);
```

  * Headless channels – New `CorsAllowedHeaders` configuration option, which can be used to restrict which HTTP headers are allowed for content retrieval requests against headless channel GraphQL endpoints. If not set, all headers are allowed by default. See [Retrieve headless content](/documentation/developers-and-admins/development/content-retrieval/retrieve-headless-content) to learn more.


Updates and changes
  * The [Froala Editor](https://froala.com/) that provides the [Rich text editor](/documentation/developers-and-admins/configuration/rich-text-editor-configuration) in Xperience was updated to version 4.1.4. This fixes a potential XSS vulnerability in the editor. See the [Froala Changelog](https://froala.com/wysiwyg-editor/changelog/#4.1.4) for details.
  * Data types – The  _Content items_ data type is now available when modeling object types in custom module classes. Note that the system doesn’t maintain the referential integrity of objects linked via this data type. Custom code must account for the referenced objects no longer existing or being otherwise invalid.


Fixed issues
  * Admin UI external authentication – The ‘@’ character in the usernames of administration users synchronized via external authentication providers (Auth0, Okta, Active Directory) was incorrectly replaced by underscore ’_’ characters.
  * Former URLs – If a page was moved to a different position under the same parent, any [former URLs](/documentation/business-users/website-content/manage-page-urls) of the page that contained the current parent’s URL slug were deleted.
  * Headless channels – The API endpoint of [headless channels](/documentation/developers-and-admins/configuration/headless-channel-management) didn’t support [CORS preflight requests](https://developer.mozilla.org/en-US/docs/Glossary/Preflight_request), which could cause requests to be blocked by CORS policy.
  * Pages
    * After publishing a [page](/documentation/business-users/website-content) in the  _Page Builder_ view mode of a website channel application, the page’s status icon in the content tree wasn’t updated correctly.
    * If a page field was set as required and used the _Rich text editor_ form component, the following issues could occur when editing the page in the _Content_ view mode of a website channel application: 
      * The rich text editor component disappeared if the field was saved and validated with an empty value.
      * A validation error was not displayed under the field when attempting to directly publish the page with an empty value in the rich text field. 
  * Settings – Changes to settings made via the _Settings_ application were not saved under certain circumstances.


* * *
## Refresh (December 14, 2023)
**version 28.1.0**
You can read a [blog post by Sean Wright](https://community.kentico.com/blog/xperience-by-kentico-refresh-december-14-2023) for a brief introduction of this refresh.
### New features
Headless channels
  * This release introduces a production-ready version of the headless API feature. With _headless channels_ , you can now securely retrieve content using HTTP requests and GraphQL, and consolidate your applications, services or external websites into the Xperience ecosystem.
  * [Headless channel management](/documentation/developers-and-admins/configuration/headless-channel-management) describes how to set up and configure headless channels.
  * [Headless content](/documentation/business-users/headless-content) explains how to prepare and edit the content available for headless channels.
  * [Retrieve headless content](/documentation/developers-and-admins/development/content-retrieval/retrieve-headless-content) is a developer resource dedicated to retrieving Xperience content using GraphQL.


Content management
  * Cascade publishing – When publishing a page, content item, email or headless item that links to other _reusable_ content items, the system now displays a list of all unpublished linked items. Users can select individual linked items and publish them together with the main item. See [Content hub](/documentation/business-users/content-hub/content-items) for more information.


Code generators
  * Code files generated for reusable content types now implement the `IContentItemFieldsSource` interface. The interface can be used as the unifying type to access system fields from a collection of multiple content types, or register extension methods that are available for all generated classes. See [Generate code files for system objects](/documentation/developers-and-admins/api/generate-code-files-for-system-objects).


Emails
  * Email templates – Added support for displaying links to pages referenced via _Pages_ content item fields within email templates. See [Email templates](/documentation/developers-and-admins/digital-marketing-setup/email-templates) for more information.


SaaS environment
  * A new Level 1 project tier is now available for [Xperience Portal projects](/documentation/developers-and-admins/deployment/deploy-to-the-saas-environment/manage-saas-deployments). See [SaaS service plans](/documentation/developers-and-admins/saas/saas-service-plans) for more information about the available project tiers.
  * When [downloading a backup](/documentation/developers-and-admins/deployment/deploy-to-the-saas-environment/manage-saas-deployments) of an application’s storage and files, you can now access and download the backup via the [AzCopy](https://learn.microsoft.com/en-us/azure/storage/common/storage-use-azcopy-v10) or [Azure Storage Explorer](https://azure.microsoft.com/en-us/products/storage/storage-explorer) tools instead of downloading a possibly very large zip file.
  * Support for the following SaaS deployment regions was added: Germany West Central, Japan East, UAE North, Canada Central, North Europe (Ireland) and East Asia (Hong Kong).
  * Xperience Portal now displays the version of the Xperience application for all deployments.


API
  * Content item query – A new `IncludeTotalCount` parametrization method for content item query was added. The method ensures that every retrieved item stores the total number of items, regardless of pagination applied by the `Offset` method. The total count can be accessed by calling `GetTotalCount()` on any item retrieved by the query. See [Reference - Content item query](/documentation/developers-and-admins/api/content-item-api/reference-content-item-query).
  * A new interface `IWebPageFolderRetriever` was added to allow developers to retrieve folders from the content tree of website channels.


### Newly obsolete API
  * `CMS.IO.Directory.PrepareFilesForImport` and `CMS.IO.AbstractDirectory.PrepareFilesForImport` – the methods were not intended for public use.


### Updates and changes
  * Content types – A content type that is allowed in a channel can no longer be removed from the channel if any content items of that type already exist under the channel. Also, when updating to this version, all missing relationships between content types and channels are automatically added.


### Updated best practises
  * When [registering custom data types](/documentation/developers-and-admins/customization/field-editor/data-type-management) into the system, call the registration code [during application startup](/documentation/developers-and-admins/customization/run-code-on-application-startup) in the `OnPreInit` method of a module class. This ensures that your data types are available in all situations.


### Fixed issues
  * Admin UI
    * The _Select all_ checkbox could behave incorrectly under certain circumstances.
    * The media library and page selector side panels used incorrect scrolling behavior in certain cases. 
  * Admin UI customization – The support for admin JS module development over HTTPS that was introduced in version 28.0.0 did not work with the default ASP.NET Core development certificate due to a misconfiguration.
  * Page Builder – Pages in Page Builder and Preview mode whose language was retrieved using the `IContentLanguageRetriever` interface were in the fallback language instead of the preferred language if fallbacks were applied.
  * Project templates – In the _Dancing Goat_ project template the Page builder displayed wrong language variant for linked items when a language fallback was applied.


* * *
### Hotfix (December 7, 2023)
**version 28.0.3**
Fixed issues
  * Code generators – The class form definitions for content types were incorrectly changed during the update process. As a result, the [code generators](/documentation/developers-and-admins/api/generate-code-files-for-system-objects) did not work correctly after the update in certain cases.


* * *
### Hotfix (November 30, 2023)
**version 28.0.2**
Fixed issues
  * Data types
    * It was not possible to work with the _Object code names_ , _Object global identifiers_ , and _Object IDs_ data types in projects without the _Kentico.Xperience.WebApp_ package (e.g., console applications).
    * The _Object global identifiers_ data type, available for custom object types (in custom modules) and system classes since version 27.0.0, is now also available when modeling web page and reusable content types. Note that the system doesn’t maintain the referential integrity of objects linked via this data type. Custom code must account for the referenced objects no longer existing or being otherwise invalid. 
  * Forms – The _~/BizFormFiles_ folder storing files uploaded via form fields using the _Upload file_ [form component](/documentation/developers-and-admins/development/builders/form-builder/reference-form-builder-components) wasn’t mapped to Azure Blob storage in projects installed for [deployment to the SaaS environment](/documentation/developers-and-admins/deployment/deploy-to-the-saas-environment). The hotfix only resolves the issue for new projects created after [updating project templates](/documentation/developers-and-admins/installation/update-xperience-by-kentico-projects). To add the mapping to existing projects, call `MapAzureStoragePath($"~/BizFormFiles");` in the default _StorageInitializationModule.cs_ file.
C#
Copy
```
protected override void OnInit()
{
    base.OnInit();
    if (Environment.IsQa() || Environment.IsUat() || Environment.IsProduction())
    {
        MapAzureStoragePath($"~/assets/");
        MapAzureStoragePath($"~/BizFormFiles");
    }
    else
    {
        MapLocalStoragePath($"~/assets/media");
    }
}
```

  * Xperience Portal
    * An error occurred in certain cases when revalidating channel domains in Xperience Portal.
    * If the channel limit was exceeded for a project in Xperience Portal, the alert message shown in the _Channels_ application didn’t correctly display the number of allowed channels.
    * The channel domain management page under _Channels and Domains_ in Xperience Portal displayed a misleading alert message if the user had insufficient permissions to add or edit domains (i.e., users with the _Developer_ [role](/documentation/developers-and-admins/saas/xperience-portal/reference-xperience-portal-user-roles)). 


* * *
### Hotfix (November 23, 2023)
**version 28.0.1**
Fixed issues
  * Admin UI customization – If the `ModuleInitParameters` parameter was passed to the `base.OnInit()` call when writing the code of an [admin UI customization module](/documentation/developers-and-admins/customization/extend-the-administration-interface/prepare-your-environment-for-admin-development) (inheriting from `AdminModule`), an exception occurred on application startup.
  * Pages – The _Discard_ action was incorrectly available for [pages](/documentation/business-users/website-content) in the initial _Draft_ state (after creating a new page or a new version of an archived page).


* * *
### SaaS environment update (November 21, 2023)
**version 28.0.0 (NuGet packages not released for this update)**
Updates and changes
  * The project tiers available for [Xperience Portal projects](/documentation/developers-and-admins/deployment/deploy-to-the-saas-environment/manage-saas-deployments) were renamed: 
    * Small → Level 2
    * Medium → Level 3
  * Due to the [retirement of StackPath CDN](https://www.stackpath.com/products/cdn/), SaaS deployments now support integration of [Cloudflare CDN](https://www.cloudflare.com/learning/cdn/what-is-a-cdn/) instead. The CDN option is available and automatically enabled for all Xperience Portal project tiers.


* * *
## Refresh (November 16, 2023)
**version 28.0.0**
### New features
General
  * .NET 8 support – Xperience by Kentico version 28.0.0 fully supports project development on [.NET 8](https://dotnet.microsoft.com/en-us/download/dotnet/8.0).


Languages
  * When creating a new language variant of a [page](/documentation/business-users/website-content/translate-pages) or [content item](/documentation/business-users/content-hub/content-items), it is now possible to copy content from an existing language variant.


Page templates
  * The _Preset_ [page template](/documentation/business-users/website-content/page-templates) feature was restored. Preset templates allow content editors to save the Page Builder configuration and content of existing pages that are based on a template. The resulting templates can then be used as a starting point for new pages.
  * `TemplateViewModel` and `TemplateViewModel<TPropertiesType>` classes were introduced to allow developers to pass a custom model to the [page template’s view](/documentation/developers-and-admins/development/builders/page-builder/page-templates-for-page-builder).


Emails
  * Email templates – The content types for which an [email template](/documentation/developers-and-admins/digital-marketing-setup/email-templates) is allowed can now be configured directly when editing individual templates in the _Email templates_ application.


Website development
  * When accessing the current page in various website development scenarios, the API now provides `RoutedWebPage` objects, which contain the code name of the page’s [content type](/documentation/developers-and-admins/development/content-types) in addition to the page ID and language. Developers can use this data to [retrieve the page](/documentation/developers-and-admins/development/content-retrieval/retrieve-page-content) and access its content fields. For example, when developing [widgets](/documentation/developers-and-admins/development/builders/page-builder/widgets-for-page-builder) or other [Page Builder](/documentation/developers-and-admins/development/builders/page-builder) components, the `RoutedWebPage` data for the current page is accessible in `ComponentViewModel.Page`.


Rich text editor
  * It is now possible to manually extend the set of [allowed HTML attributes and tags](/documentation/developers-and-admins/configuration/rich-text-editor-configuration) in the [Rich text editor](/documentation/business-users/rich-text-editor).


Customization
  * Module classes now support service resolution via [IServiceProvider](https://learn.microsoft.com/en-us/dotnet/api/system.iserviceprovider) in initialization code, using the new `OnInit(ModuleInitParameters)` override. Additionally, the `OnPreInit(ModuleInitParameters)` override allows access to the application’s [IServiceCollection](https://learn.microsoft.com/en-us/dotnet/api/microsoft.extensions.dependencyinjection.iservicecollection), e.g., to configure the application’s startup options. See [Run code on application startup](/documentation/developers-and-admins/customization/run-code-on-application-startup).


Content modeling
  * This release changes the accessibility of the _Pages_ and _Object code names_ data types. 
    * Pages – this data type is now available when modeling web page and reusable [content types](/documentation/developers-and-admins/development/content-types), and [object types](/documentation/developers-and-admins/customization/object-types) (in custom modules). Fields of this data type store references to web pages.
    * Object code names – this data type is now available when modeling web page and reusable [content types](/documentation/developers-and-admins/development/content-types) , custom [object types](/documentation/developers-and-admins/customization/object-types) (in custom modules) and when [extending system object types](/documentation/developers-and-admins/customization/object-types/extend-system-object-types). Fields of this data type store references to other objects in system via object code names.Note that the system doesn’t maintain the referential integrity of objects linked via these data types. Custom code must account for the referenced objects no longer existing or being otherwise invalid.


Admin UI authentication
  * New `ExpireTimespan` property for `AdminAuthenticationOptions` that allows you to set the expiration period for authenticated admin UI sessions. See [Administration - Forms authentication](/documentation/developers-and-admins/configuration/users/administration-registration-and-authentication/administration-forms-authentication).


Admin UI customization
  * Custom JavaScript modules can now be developed in SSL-enabled environments when using the _Proxy_ mode. To enable SSL, add the `UseSSL` property to the module configuration in your Xperience instance. See [Prepare your environment for admin development](/documentation/developers-and-admins/customization/extend-the-administration-interface/prepare-your-environment-for-admin-development).


### Breaking changes – API
  * The `ITreeNode` interface was removed. The interface was not functionally connected to any system logic. It was an accidental remainder from the switch to the new content modeling approach introduced by version 27.0.0.
  * The `IPageBuilderConfigurationSourceLoader` and `PageBuilderConfigurationSource` types were moved to the `Kentico.PageBuilder.Web.Mvc.Internal` namespace and are not intended for public use.


### Newly obsolete API
The following API members were marked obsolete, to be removed in one of the future releases:
  * The `IRoutedWebPage` and `IWebPageDataContext` types used when getting information about the current page are now obsolete. Use `RoutedWebPage` and `WebPageDataContext` instead.
  * The `ActivityLoggingAPI` and `ActivityLoggingScript` extension methods for registering [activity logging scripts](/documentation/developers-and-admins/digital-marketing-setup/set-up-activities) in page views are now obsolete. Use the new `ActivityLoggingScriptV2` method instead. The method has optional parameters that allow you to enable or disable logging scripts for page-related activities and custom activities.
  * The following properties of the `ObjectTypeInfo` class are no longer used by the system: 
    * ModuleName
    * AssemblyNameColumn
    * SizeColumn
    * MimeTypeColumn
    * ResourceIDColumn
    * DependsOnIndirectlyAfter upgrading, your custom object type classes may output warnings related to these properties. Manually remove the offending code or regenerate the affected classes using the [code generator](/documentation/developers-and-admins/api/generate-code-files-for-system-objects).
  * The update obsoletes the following API members related to the content model used prior to version 27.0.0 and which are no longer actively used by the system: 
    * FieldBase.External
    * FieldBase.IsInherited
    * IField.External
    * IField.IsInherited
    * FormCategoryInfo.IsInherited
  * The following properties were made obsolete with no replacement: 
    * FormFieldInfo.GetResolvedDefaultValue
    * FormFieldInfo.SetResolvedDefaultValue
    * FormInfo.AddFormCategory
    * FormInfo.CombineWithForm
    * FormInfo.EnsureDefaultValues
    * FormHelper.EnsureDefaultValues
    * FormHelper.GetOriginalFileName
    * FormHelper.GetGuidFileName


### Fixed issues
  * AIRA – [Email subject suggestions](/documentation/developers-and-admins/configuration/aira-configuration) generated by the AI feature were in some cases displayed in a different language than the one set for the email channel after requesting adjustments via the quick refinement buttons or the manual text prompt.
  * Activities – If a [custom activity](/documentation/developers-and-admins/digital-marketing-setup/set-up-activities/custom-activities) was logged from client-side code directly on page load, two separate anonymous contacts were created for new visitors (one with the custom activity, the other with the system’s default page visit activities). To fix the issue, developers need to register updated activity logging scripts using the new `ActivityLoggingScriptV2` extension method.
  * Admin UI
    * Some object creation pages in the administration UI had missing page titles.
    * The “select all” checkbox above selectable lists of items in the administration UI behaved incorrectly in cases where the list contained both selected and not selected items. After the fix, the checkbox either selects all items if none are currently selected, or clears the selection of all items.
    * The text of object “name” field labels was unified throughout the administration to match the “Object name” and “Code name” format. 
  * CI/CD – Content item references were not restored properly in the CD _Create_ mode, leaving the database inconsistent.
  * Channels – Validation for the maximum number of allowed characters was missing for the domain inputs offered when creating or editing channels.
  * Contact management – The _Birthday_ and _Country_ /_State_ fields are now displayed when editing a contact’s profile in the _Contact management_ application.
  * Content item API – When fields of the _Content item asset_ data type did not have _Required_ set and were left unpopulated, retrieving content items with such fields resulted in an exception. After the update, the retrieval API correctly returns null.
  * Content types – In certain rare cases when creating content types, the system could return an error if the code name of the newly created content type matched the database table name of an already existing, previously renamed content type.
  * Email templates – Leading and trailing whitespace characters in the names of email templates weren’t trimmed correctly in certain parts of the administration UI.
  * Emails
    * If a contact belonged to a [recipient list](/documentation/business-users/digital-marketing/emails/send-regular-emails-to-subscribers) and their email address was later deleted, an error occurred for each such contact when sending a _Regular_ email to the recipient list. After the fix, contacts without an email address are skipped when sending emails, without causing any errors.
    * [Subscription](/documentation/business-users/digital-marketing/emails/send-regular-emails-to-subscribers) confirmation and unsubscription links didn’t work if the recipient’s email address contained certain special characters (for example ’’). 
  * Forms
    * The default _From_ address of [custom form autoresponder emails](/documentation/developers-and-admins/digital-marketing-setup/custom-form-autoresponder-emails) didn’t reflect the [global sending domain for system emails](/documentation/developers-and-admins/configuration/email-configuration) set via `SystemEmailOptions.SendingDomain`.
    * When a file was uploaded through a form’s _Upload file_ field, a 404 error was returned when attempting to view or download the file in the _Submissions_ view of the _Forms_ application. 
  * General – The application could reach the preset limit on the number of database connections under heavy traffic (e.g., during a load test).
  * Media libraries – An error occurred in certain cases when renaming a folder in a media library in the _Media libraries_ application.
  * Pages – The _Current URL_ or _New URL_ fields displayed an incorrect URL value when creating a new language variant of a page that already existed in the fallback language.
  * Pages
    * Sample sites based on the _Dancing Goat_ project template didn’t preserve the currently selected language when redirecting users after sign-in, registration or sign-out. The English variant of the site’s home page was always opened. The issue is only resolved for new projects created after [updating project templates](/documentation/developers-and-admins/installation/update-xperience-by-kentico-projects).
    * The _Current URL_ or _New URL_ fields displayed an incorrect URL value when creating a new language variant of a page that already existed in the fallback language. 
  * Project templates – Certain sample widgets included in the _Dancing Goat_ [project template](/documentation/developers-and-admins/installation) contained cross-site scripting vulnerabilities.
  * Rich text editor – Images or links added in the [rich text editor](/documentation/business-users/rich-text-editor) weren’t created correctly if the selected content item asset contained an apostrophe ( ’ ) in its file name. This could result in missing images or broken links on the live site or in sent emails.


* * *
### Hotfix (November 9, 2023)
**version 27.0.4**
Fixed issues
  * Recipient lists – When editing the approval or unsubscribe settings of a [recipient list](/documentation/business-users/digital-marketing/emails/send-regular-emails-to-subscribers), an error occurred when saving the dialog after a different _Thank you page_ or _Goodbye page_ was selected while the _Send (un)subscription confirmation_ option was disabled. Additionally, the selected confirmation email wasn’t cleared correctly if the _Send (un)subscription confirmation_ option was disabled.


* * *
### Hotfix (November 2, 2023)
**version 27.0.3**
Fixed issues – security
  * Unauthorized access – Fixed an issue that, under extremely specific conditions, allowed unauthenticated actors access to the emails of users registered in the administration interface.
  * XSS – Fixed a cross-site scripting vulnerability caused by improper sanitization of content item asset file names.


Fixed issues
  * Cross-site tracking – [Cross-site tracking](/documentation/developers-and-admins/digital-marketing-setup/cross-site-tracking) didn’t work correctly if the website channel linked to the tracked site had its _Default cookie level_ set lower than _Visitor_.
  * Field editor – [Fields](/documentation/developers-and-admins/customization/field-editor) with a data type that cannot be set as _Required_ (e.g., _Binary_ , _Content items_ , _Content item asset_) couldn’t be saved and created if the _Display in editing form_ option was disabled.
  * Pages – The _Current URL_ and _New URL_ fields of pages in website channel applications were not displayed and updated correctly in certain scenarios (e.g., when creating a new language variant of a page).
  * Project templates – Projects [installed](/documentation/developers-and-admins/installation) with the `--cloud` parameter had incorrect Azure Blob storage folder mappings for media library files in the _StorageInitializationModule_ class. The issue is only resolved for new projects created after [updating project templates](/documentation/developers-and-admins/installation/update-xperience-by-kentico-projects).
  * Routing – [Routes](/documentation/developers-and-admins/development/routing/content-tree-based-routing/set-up-content-tree-based-routing) registered for specific website channels didn’t work correctly in cases where multiple `RegisterWebPageRoute` attributes were added for a single content type, but different website channels.
  * UI form components – If a _Page selector_ was limited to a section of the content tree, the selection dialog didn’t work correctly in environments with multiple website channels.


* * *
### Hotfix (October 26, 2023)
**version 27.0.2**
Fixed issues
  * Emails – If fields with the _Date and time_ , _Decimal number_ or _Floating-point number_ data types were placed into an [email template](/documentation/developers-and-admins/digital-marketing-setup/email-templates), the value was not resolved according to the _Formatting culture_ of the [language](/documentation/developers-and-admins/configuration/languages) set for the [email channel](/documentation/developers-and-admins/digital-marketing-setup/email-channel-management).
  * Routing – Validation of URL collisions between the [URL slugs](/documentation/business-users/website-content/manage-page-urls) of pages and the code names of [language](/documentation/developers-and-admins/configuration/languages) didn’t work correctly in certain cases.
  * UI form components
    * Some form components didn’t correctly display the value saved for the associated field under specific conditions. For example, the icon selector used when editing a content type always showed _No icon selected_ after the _General_ tab was reloaded.
    * The _URL selector_ [UI form component](/documentation/developers-and-admins/customization/extend-the-administration-interface/ui-form-components/reference-admin-ui-form-components) allowed users to select a page even when the field was placed outside of a website channel. After applying the hotfix, the selection functionality is disabled in this scenario, and the component only provides a text field for inserting URLs manually. 


* * *
### Hotfix (October 19, 2023)
**version 27.0.1**
New features
  * API – Asynchronous versions of services used to retrieve [cache dependencies](/documentation/developers-and-admins/development/caching/cache-dependencies) for linked content items were introduced: `IWebPageLinkedItemsDependencyRetriever` and `ILinkedItemsDependencyRetriever`. The new interfaces are `IWebPageLinkedItemsDependencyAsyncRetriever` and `ILinkedItemsDependencyAsyncRetriever`.
  * Emails – Added a new `ForPreview` property to the Email selector [UI form component](/documentation/developers-and-admins/customization/extend-the-administration-interface/ui-form-components/reference-admin-ui-form-components), which controls whether the selector allows emails without a published version (false by default).


Updates and changes
  * The [HTMLSanitizer](https://www.nuget.org/packages/HtmlSanitizer) NuGet package used in the [Rich text editor](/documentation/business-users/rich-text-editor) was updated to version _8.0.723_.


Fixed issues
  * AIRA – Selecting _Cancel_ in the AI panel while [generating content](/documentation/developers-and-admins/configuration/aira-configuration) for an email field could in some cases result in an error.
  * Admin UI
    * Buttons for saving, publishing and editing pages were not visible in website channel applications when viewed on a smartphone-sized display.
    * Improved UI texts and error messages in multiple locations across the administration interface, e.g., _Content hub_ , _Media libraries_ , and website channel applications.
    * Validation errors were sometimes incorrectly displayed on administration pages with visibility conditions immediately after a previously hidden required field was made visible. 
  * CI/CD – [Continuous Integration](/documentation/developers-and-admins/ci-cd/continuous-integration) generated unnecessary changes when the primary language of any [website channel](/documentation/developers-and-admins/configuration/website-channel-management) was updated. After applying the hotfix, only changes to the edited website channel object are generated.
  * Content hub – It was not possible to upload [assets](/documentation/business-users/content-hub/content-item-assets) with uppercase characters in file extensions, e.g., _image.PNG_.
  * Content items
    * If a _Content item selector_ field was used to create and directly publish a linked content item, using the _Publish_ action for the original content item didn’t save the added link (if changes were not previously saved using the _Save_ action).
    * When using content item query to [retrieve items](/documentation/developers-and-admins/development/content-retrieval/retrieve-content-items) with a _Content item asset_ field, an exception was thrown if the field was empty (no file uploaded). This could cause an error on the live site. After applying the hotfix, empty content item asset fields return a `null` value – the retrieval still needs to be handled correctly by developers to avoid null reference exceptions. 
  * Emails
    * Content types for emails incorrectly had the _ClassWebPageHasUrl_ column set to true in the _CMS_Class_ database table.
    * The _Last updated_ time of emails wasn’t set correctly when the email’s content or settings were updated.
    * [Emails](/documentation/business-users/digital-marketing/emails) that did not have a published version yet (i.e., emails with the initial _Draft_ status) could be selected in the administration’s email selectors, for example when configuring an autoresponder for a [form](/documentation/business-users/digital-marketing/forms/create-and-edit-forms). Attempting to send an unpublished email results in an error. 
  * Languages – The _Data protection_ application didn’t preserve the [language](/documentation/developers-and-admins/configuration/languages) selected previously in another application (e.g., _Content hub_).
  * Modules – It was possible to edit the _General_ configuration of fields without database representation in the UI forms of system modules. Such changes could could cause errors in the administration.
  * Page Builder – Editing component state configuration for [Page Builder legacy selectors](/documentation/developers-and-admins/upgrade-to-xperience-by-kentico/editing-components-in-xperience-by-kentico) did not work correctly after applying the October refresh (version 27.0.0).
  * Performance
    * Certain requests made by the [Page Builder](/documentation/developers-and-admins/development/builders/page-builder) were not cached properly, which resulted in unnecessary database queries.
    * Projects with large numbers of content items and linked items (multiple thousands and more) generated excessively large queries when retrieving content linked item data, which could cause SQL errors on the live site. 
  * Project templates – The _Program.cs_ file in the _Boilerplate (kentico-xperience-mvc)_ [project template](/documentation/developers-and-admins/installation) contained commented code that was invalid. The hotfix fixes the issue only for new boilerplate projects created after [updating project templates](/documentation/developers-and-admins/installation/update-xperience-by-kentico-projects).
  * Rich text editor – It was possible to select images without a fallback language variant for the current language in the _Insert image_ dialog in the [rich text editor](/documentation/business-users/rich-text-editor). After applying the hotfix, such images are not displayed in the dialog.
  * Routing
    * Creating a page with the URL slug identical to a code name of a [language](/documentation/developers-and-admins/configuration/languages) led to incorrectly generated former URLs for the page. The hotfix adds validation to ensure that URL slugs of pages are not identical to the code names of any language.
    * [Former URLs](/documentation/business-users/website-content/manage-page-urls) were not created for untranslated variants of pages when the URL slug of their ancestor changed. 
  * Xperience Portal – When adding a new sending domain for an email channel in the Xperience Portal _Channels_ application, the DNS records screen displayed incomplete domain name values.


* * *
## Refresh (October 12, 2023)
**version 27.0.0**
This Refresh release introduces major changes to content management and general content workflow in the system.
[Updating](/documentation/developers-and-admins/installation/update-xperience-by-kentico-projects) is only supported for projects on version 26.6 (any hotfix version). If you are transitioning from an older version, you need to perform the update twice – first to version 26.6.0 and then to 27.0.0 or newer.
### Content management changes
This release replaces the primary method of content composition and delivery – driven by pages (`TreeNode` objects), the _Pages_ application, and the `DocumentQuery` content delivery API – with the concept of **reusable** and **non-reusable** content items and **channels**. The concept of first creating a content type template and defining custom fields via the field editor remains unchanged.
**Reusable content items** encapsulate pieces of _structured data_ – pure content not burdened by any information specific to a content delivery method, such as additional formatting metadata. Reusable items represent content intended for use across multiple delivery methods. Individual reusable items can also reference other reusable items, up to an arbitrary depth, to create complex, multi-level structures.
**Non-reusable content items** consume reusable content items via reference and serve as the medium for delivery. Each non-reusable item is responsible for transforming the referenced reusable content by providing delivery-specific formatting and metadata. Non-reusable items are tightly coupled to _channels._
**Channels** represent a medium through which information is delivered from the system to end-users. This release introduces two types of channels: _Email_ and _Website._
**Email** channels are a dedicated medium of communication to a specific audience. Each project may contain multiple email channels, focusing on different content strategies, languages and audience engagement techniques.
**Website** channels encapsulate websites managed by Xperience. Each channel is an independent entity with its own domain name configuration and content management. Channels store content in pages and linked content items.
With this release, there is no longer a single _Pages_ application to encapsulate the majority of content management functionality. Instead, each existing channel has a dedicated application, which can be found under the _Channels_ category in the Xperience administration.
See the following pages for a detailed introduction to the new functionality:
#### Business
  * [Content hub](/documentation/business-users/content-hub) – introduces working with _reusable content items_.
  * [Content item workflow](/documentation/business-users/content-hub/content-items#content-items-lifecycle) – introduces the workflow of content items.
  * [Pages](/documentation/business-users/website-content) – introduces working with _website channels_.
  * [Emails](/documentation/business-users/digital-marketing/emails) – introduces working with _email channels_.


#### Administration
  * [Content types](/documentation/developers-and-admins/development/content-types) – content types define fields for storing content, and determine whether content items are _reusable_ or _non-reusable_.
  * [Website channel management](/documentation/developers-and-admins/configuration/website-channel-management) – overview of _website channel_ configuration.
  * [Email channel management](/documentation/developers-and-admins/digital-marketing-setup/email-channel-management) – overview of _email channel_ configuration.


#### Development
  * [Content item API](/documentation/developers-and-admins/api/content-item-api/content-item-query-api) – provides a general overview of reusable content item management and content delivery APIs.
  * [Content retrieval](/documentation/developers-and-admins/development/content-retrieval) – introduces working with content delivery APIs in the context of web application development.
  * [Content item database structure](/documentation/developers-and-admins/api/content-item-api/content-item-database-structure) – an overview of the database entities composing the new content model.
  * [Reference - Global system events](/documentation/developers-and-admins/customization/handle-global-events/reference-global-system-events) – new events for _reusable content items_ and _web pages_ (_non-reusable content items_ belonging to a _website channel_).
  * [Email templates](/documentation/developers-and-admins/digital-marketing-setup/email-templates) – explains how to create templates for emails, which are now based on content types.


Note that this list is not exhaustive. Many existing features had to undergo small changes to accommodate the new approach to content modeling.
### Channels instead of Sites
With the introduction of channels, the concept of grouping project-related data under a site (`SiteInfo` object) and the existence of site-specific objects was also removed. This should not pose a problem for existing projects, since previous Xperience by Kentico versions only supported a single site.
### New features
#### Content management
  * Languages – Multilingual support for pages and content items was introduced. See [Languages](/documentation/developers-and-admins/configuration/languages).
  * Support for running multiple websites managed by a single Xperience back-end. Documentation will be available soon.
  * Page templates – When registering [Page templates for Page Builder](/documentation/developers-and-admins/development/builders/page-builder/page-templates-for-page-builder), developers can now specify the content types for which the template is available without needing to implement template filtering (new `ContentTypeNames` property of the `RegisterPageTemplate` attribute).


#### Emails
  * Email channels – Emails in Xperience are now created and managed within [email channels](/documentation/developers-and-admins/digital-marketing-setup/email-channel-management). Channels allow for clean separation of an organization’s emails, each focusing on a different content strategy or language. Every email channel has a unique _sending domain_ , and its own application in the Xperience administration where users [create and send emails](/documentation/business-users/digital-marketing/emails).
  * Email content – The system now uses [content types](/documentation/developers-and-admins/development/content-types) to define the fields available to marketers when creating emails. Email content types are tied with [email templates](/documentation/developers-and-admins/digital-marketing-setup/email-templates), which contain macro placeholders that set the position of individual fields within the email content.
  * Email workflow – Emails with the _Form autoresponder_ or _Confirmation_ purpose now support basic Draft/Published workflow. This allows users to edit a new version of an email while the system still uses the previous published version. See [Emails](/documentation/business-users/digital-marketing/emails).
  * AI integration – Added an integration with [Azure OpenAI Service](https://learn.microsoft.com/azure/ai-services/openai/overview), which allows users to generate content suggestions for email subjects and content fields. See [Artificial intelligence features](/documentation/developers-and-admins/configuration/aira-configuration).
  * Email client configuration – The default SMTP and SendGrid email clients can now be configured separately for individual email channels, as well as system emails that are not related to a specific channel. See [Email configuration](/documentation/developers-and-admins/configuration/email-configuration).
  * Bounced email configuration – The bounced email tracking configuration for SMTP email clients can now be configured separately for individual email channels, as well as system emails. See [Set up email tracking](/documentation/developers-and-admins/digital-marketing-setup/set-up-email-tracking).
  * You can now set a shared sending domain for all system emails that are not created under a specific email channel (e.g., user registration, password reset, system notification emails). Use the `SystemEmailOptions` options class. See [Email configuration](/documentation/developers-and-admins/configuration/email-configuration).


#### Data protection
  * Consents – The _Data protection_ application now allows users to create language variants of consent texts. See [Consent management](/documentation/developers-and-admins/data-protection/consent-management) and [Languages](/documentation/developers-and-admins/configuration/languages).


#### UI improvements
  * Users can now directly edit the names of [pages](/documentation/business-users/website-content), [emails](/documentation/business-users/digital-marketing/emails) and [content items](/documentation/business-users/content-hub/content-items) by selecting an icon next to the item’s name in the editing form.


#### Administration UI users
  * The user registration and password reset email [customization pattern](/documentation/developers-and-admins/configuration/users/administration-registration-and-authentication/administration-forms-authentication), available via `AdminIdentityOptions.EmailOptions` was extended and simplified. The `RegistrationEmailMessageProvider` and `ResetPasswordEmailMessageProvider` options now contain a new `OriginalMessage` property that contains the default `EmailMessage` object sent by the system. You can now directly modify parts of this object instead of always having to construct the full `EmailMessage` when customizing these types of emails.


#### Administration UI development
  * Rich text editor – When creating custom [Rich text editor configurations](/documentation/developers-and-admins/configuration/rich-text-editor-configuration) with the default _Link_ plugin, developers can now configure which types of links are offered when the _Insert link_ button is selected in the editor. If the `pluginOptions` configuration is omitted, all options shown below are used.
JS
Copy
```
"customPlugins": [
    {
        "pluginName": "@kentico/xperience-admin-base/Link",
        "pluginOptions": {
            "dropdownOptions": [
            {
                "linkOption": "asset",
                "componentName": "@kentico/xperience-admin-base/AssetPanel"
            },
            {
                "linkOption": "external"
            },
            {
                "linkOption": "webpage",
                "componentName": "@kentico/xperience-admin-websites/PageLink"
            }
            ]
        }
    }
]
```

  * UI form components
    * New _Email selector_ [form component](/documentation/developers-and-admins/customization/extend-the-administration-interface/ui-form-components/reference-admin-ui-form-components) that allows users to select [emails](/documentation/business-users/digital-marketing/emails) from an email channel. Use this new selector instead of the _Object selector_ with the email configuration object type.
    * The _Url format_ [validation rule](/documentation/developers-and-admins/customization/extend-the-administration-interface/ui-form-components/ui-form-component-validation-rules) for UI form components provides new properties that can be enabled to also allow URL fragments starting with ‘#’ and query string values starting with ‘?’.
  * UI page templates – New [binding UI page template](/documentation/developers-and-admins/customization/extend-the-administration-interface/ui-pages/reference-ui-page-templates/binding-ui-page-template) that allows users to manage bindings, which represent many-to-many [relationships](/documentation/developers-and-admins/customization/object-types/object-type-configuration/model-object-type-relationships) between [object types](/documentation/developers-and-admins/customization/object-types) (_*Info_ classes).


### Breaking changes
  * Assets
    * Subfolders under the _/assets_ folder for storing [content item assets](/documentation/business-users/content-hub) and media files are now always created with lower case names.
    * The file system folder structure for [media library files](/documentation/business-users/media-libraries/manage-media-files) no longer includes a folder matching the site code name (media libraries are now global). 
  * Content types – Content type features are not available and were replaced with the _Use for_ selector on the _General_ tab.
    * Select _Pages_ for content types meant for website channels.
    * Select _Reusable content_ for content types in the _Content hub_.
    * Select _Emails_ for content types meant for email channels.
  * Cookies – [Default cookie level](/documentation/developers-and-admins/data-protection/cookies) configuration is now tied to [website channels](/documentation/developers-and-admins/configuration/website-channel-management) and must be configured on each channel’s _Channel settings_ tab.
  * Event log – The “Error notification email address” (_CMSSendErrorNotificationTo_) and “Send email notifications from” (_CMSSendEmailNotificationsFrom_) settings – used to configure the sending of notification emails about events logged into the [event log](/documentation/developers-and-admins/configuration/event-log) – have been removed. The system by default no longer sends any notification emails related to the event log. If you wish to implement a similar functionality, use [custom logging providers](/documentation/developers-and-admins/development/logging#custom-logging-providers).
  * Forms
    * The _After form submission_ [settings](/documentation/business-users/digital-marketing/forms/create-and-edit-forms) of forms (_Display a message_ , _Redirect to URL_ , _Redirect to page_) were moved from the form editing UI to properties of individual _Form_ widgets. The _Autoresponder_ settings remain in the form UI.
    * The reCAPTCHA form component no longer controls which reCAPTCHA version is displayed to the user. Instead, it uses the reCAPTCHA version configured by the website channel under which it is displayed.
    * [Forms](/documentation/business-users/digital-marketing/forms) are now global objects (not related to a specific [website channel](/documentation/developers-and-admins/configuration/website-channel-management)). 
  * Modules – It was possible to edit certain properties of fields in the UI forms of system modules and then delete the fields. Such changes could cause errors and inconsistencies in the administration.
  * Page Builder – Page Builder is no longer enabled via the content type _Features_ tab. Instead, you must specify a collection of content type code names when enabling Page Builder for the project in the startup pipeline.
C#
**Program.cs**
Copy
```
features.UsePageBuilder(new PageBuilderOptions
{
    ContentTypeNames = new[]
    {
        // Enables Page Builder for the 'LandingPage' and 'ContactsPage' content types
        LandingPage.CONTENT_TYPE_NAME,
        ContactsPage.CONTENT_TYPE_NAME
    }
});
```

  * Page templates – Preset [page templates](/documentation/developers-and-admins/development/builders/page-builder/page-templates-for-page-builder) were temporarily removed and will be added to the product soon.
  * Rich text editor – The system’s _Asset_ and _Link_ [plugins](/documentation/developers-and-admins/configuration/rich-text-editor-configuration/rich-text-editor-plugins) used in rich text editor configurations now use the _xperience-admin-base_ namespace in their `pluginName` value. If you use custom configurations with these plugins, you need to update the values:
    * @kentico/xperience-admin-base/Asset
    * @kentico/xperience-admin-base/Link
  * Routing – The default location of Razor view files used by the [basic mode](/documentation/developers-and-admins/development/routing/content-tree-based-routing/set-up-content-tree-based-routing) of the content tree-based routing feature was changed from _~/Views/Shared/PageTypes/ <viewfile>_ to _~/Views/Shared/ContentTypes/ <viewfile>_.
  * Site domain aliases – Website _domain alias_ configuration is now done via a code-driven approach utilizing the [ASP.NET Core options pattern](https://learn.microsoft.com/en-us/aspnet/core/fundamentals/configuration/options). See [Website channel management](/documentation/developers-and-admins/configuration/website-channel-management).


### Breaking changes – API
The following list covers breaking changes in frequently used API scenarios, but is not a comprehensive list of all API changes.
  * [Data caching](/documentation/developers-and-admins/development/caching/data-caching)
  * [Content item API](/documentation/developers-and-admins/api/content-item-api/content-item-query-api)
  * `DocumentEngine` replaced with `ContentEngine` and `Websites` projects. 
    * Use [generated content type classes](/documentation/developers-and-admins/api/generate-code-files-for-system-objects) to access page data instead of `TreeNode`.
    * Use `IWebPageManager` to manipulate pages instead of `TreeNode` methods.
    * The `IPageDataContextRetriever` service was replaced with the `IWebPageDataContextRetriever` and the returned object has changed.
  * [Content retrieval](/documentation/developers-and-admins/development/content-retrieval)
    * `DocumentQuery` and the `IPageRetriever` interface were removed, see [Retrieve page content](/documentation/developers-and-admins/development/content-retrieval/retrieve-page-content).
  * [Activities](/documentation/developers-and-admins/digital-marketing-setup/set-up-activities)
    * The `IPagesActivityLogger` service used to log page activities was replaced by `IWebPagesActivityLogger`. The methods of the service are now asynchronous and accept different parameters.
    * The `CustomActivityData` type used when [logging custom activities](/documentation/developers-and-admins/digital-marketing-setup/set-up-activities/custom-activities) has renamed properties to match the the new data structure of pages and languages.
  * Cross-site tracking 
    * When configuring the [cross-site tracking](/documentation/developers-and-admins/digital-marketing-setup/cross-site-tracking) feature for the application, the `ConsentSettings` property of `CrossSiteTrackingOptions` now accepts an `IEnumerable` collection of `CrossSiteTrackingConsentOptions` objects instead of a single object. Add one object for every website channel where you wish to use the feature.
    * The `kxt('consentdata');` function in [cross-site tracking](/documentation/developers-and-admins/digital-marketing-setup/cross-site-tracking) scripts now requires a `languageName` parameter instead of `cultureCode`. Set the parameter’s value to the code name of the [language](/documentation/developers-and-admins/configuration/languages) in which you want to load the consent texts.
  * Bounced email configuration – When [configuring bounced email tracking](/documentation/developers-and-admins/digital-marketing-setup/set-up-email-tracking) for an SMTP email client, the `SoftBounceLimit` property is now available in a new `BouncedEmailsGlobalOptions` options class instead of `BouncedEmailsOptions`.
  * Content tree-based routing – The method for enabling the [content-tree based routing](/documentation/developers-and-admins/development/routing/content-tree-based-routing) feature for the application was renamed to `UseWebPageRouting`.
  * Cookies 
    * The `CookieHelper` class is now obsolete. To work with cookies, use the newly added, non-static `ICookieAccessor`. Custom cookie registration is changing as well. Custom cookies now must be registered via the `CookieLevelOptions` options class on application startup. See [Cookies](/documentation/developers-and-admins/data-protection/cookies) for more information.
  * Forms – [Forms](/documentation/business-users/digital-marketing/forms) are now global objects, so the `BizFormInfo` class no longer contains the `FormSiteID` property.
  * [Legacy MVC selector API](/documentation/developers-and-admins/upgrade-to-xperience-by-kentico/editing-components-in-xperience-by-kentico)
    * The following properties used by the legacy Content selector JavaScript API were renamed:
      * _Page.nodeId → Page.identifier_
      * _Page.nodeAliasPath → Page.treePath_
      * _Page.nodeGuid → Page.guid_
    * The following properties used by the legacy Page selector JavaScript API were renamed:
      * _pageSelectorItem.nodeGuid → pageSelectorItem.webPageGuid_
    * The following properties used by the legacy Page path selector JavaScript API were renamed:
      * _pathSelectorItem.nodeAliasPath → pathSelectorItem.treePath_
  * [Page templates](/documentation/developers-and-admins/development/builders/page-builder/page-templates-for-page-builder) – Properties were renamed for `PageTemplateDefinition`, `PageTemplateFilterContext` and the `RegisterPageTemplate` attribute.


### Newly obsolete API
  * `AdminIdentityEmailOptions.DefaultFromAddress` – use the `SenderAddress` property on the same object instead.
  * `ConsentInfo.GetConsentText` – use the new asynchronous `ConsentInfo.GetConsentTextAsync` method instead.
  * Cookies 
    * `CMS.Helpers.CookieHelper` – use `Kentico.Web.Mvc.ICookieAccessor` instead. See [Cookies](/documentation/developers-and-admins/data-protection/cookies) for more information.
    * `CMS.Helpers.CookieLevel` – use the static properties from `Kentico.Web.Mvc.CookieLevel` instead. For example: `Kentico.Web.Mvc.CookieLevel.Essential.Level`
  * With the removal of `DocumentQuery` from the system, many supporting APIs were made obsolete with no replacement.


### Fixed issues – security
  * XSS – Fixed a potential cross-site scripting vulnerability in the [Rich text editor configuration](/documentation/developers-and-admins/configuration/rich-text-editor-configuration) used for emails.


### Fixed issues
  * Admin UI authentication
    * An error could occur in special cases when adding a new user in the _Users_ application.
    * An error occurred if a user attempted to sign in to the administration while being signed in as a [member](/documentation/business-users/members) on the live site.
    * The authentication cookie used for the administration was unnecessarily set in every response to admin UI requests. After the fix, the authentication cookie is valid for 30 minutes (unless the user signs out or the account is disabled).
    * The hashing algorithm used for admin UI user passwords was enhanced for improved security. Existing user passwords remain compatible, but we recommend that you encourage all users to set new passwords (which will automatically use the new hashing algorithm). 
  * CI/CD
    * An error occurred when restoring [CI/CD](/documentation/developers-and-admins/ci-cd) data if the changes included a modified module class or content type that had both added and removed fields, with the total number of fields remaining the same.
    * Changes of the _Enable Continuous Integration_ setting were not applied immediately due to incorrect caching. 
  * Code generators – When [generating](/documentation/developers-and-admins/api/generate-code-files-for-system-objects) code for fields with the _Media files_ data type, the corresponding properties in the resulting class incorrectly had the `string` type instead of `IEnumerable<AssetRelatedItem>`.
  * Contact groups – The _Recalculate contact group_ button was displayed as active for users without the _Update_ [permission](/documentation/developers-and-admins/configuration/users/role-management) for the _Contact groups_ application (an error occurred if the button was clicked).
  * Custom admin UI – The `ActionConfigurationExtensions.AddCommandWithConfirmation` method’s `confirmationDetail` parameter was not reflected when configuring custom action buttons for the admin UI.
  * Emails – The _Send test email_ button in the _Email queue_ application was incorrectly available for users without the _Update_ [permission](/documentation/developers-and-admins/configuration/users/role-management) for the application.
  * Field editor
    * Changing the _Data type_ of an existing field could lead to inconsistent behavior or errors while selecting and configuring a new _Form component_ for the field.
    * Creating a field with leading or trailing whitespace characters in the _Field name_ caused errors and prevented editing of the field. After the fix, leading or trailing whitespace is automatically trimmed from the _Field name_.
    * The [field editor](/documentation/developers-and-admins/customization/field-editor) incorrectly allowed creation of fields with the _Required_ flag enabled, but _Display in editing form_ disabled. This combination of field settings resulted in an error when saving the resulting editing form. Validation was added to prevent this configuration. 
  * Form Builder – It was not possible to add [form components](/documentation/developers-and-admins/development/builders/form-builder/form-components) with identifiers containing certain special characters (for example, _acme.date-input_) to forms.
  * Installation – If the `kentico-xperience-dbmanager` utility was installed and used without one of the [Xperience project templates](/documentation/developers-and-admins/installation), errors occurred while attempting to set the database connection string. After the fix, the utlity can handle such scenarios, and create the `ConnectionStrings` section is the _appsettings.json_ file if it is missing.
  * Rich text editor – The _Replace image_ functionality in the [rich text editor](/documentation/business-users/rich-text-editor) didn’t work correctly.
  * UI form components
    * If a [form component](/documentation/developers-and-admins/customization/extend-the-administration-interface/ui-form-components) had a [custom data type](/documentation/developers-and-admins/customization/field-editor/data-type-management) (registered using `DataTypeManager`), fields using the component were not displayed in editing forms.
    * If the _Radio button group_ [UI form component](/documentation/developers-and-admins/customization/extend-the-administration-interface/ui-form-components/reference-admin-ui-form-components) was used for a required field, and the form was saved without changing the selected option, the component returned a _null_ value instead of the preset default value.
    * The _Media file selector_ UI form component could cause performance issues due to loading all files within a folder every time it was opened. After the fix, media files displayed in the selector are cached to improve performance. 
  * Update procedure – Under certain circumstances, the Xperience database [update](/documentation/developers-and-admins/installation/update-xperience-by-kentico-projects) could end with the following error: “ _There is already an open DataReader associated with this Connection which must be closed first._ ”


* * *
### Hotfix (September 14, 2023)
**version 26.6.1**
Fixed issues
  * Media libraries – Updating existing [media files](/documentation/business-users/media-libraries/manage-media-files) incorrectly required users to have the Update [permission](/documentation/developers-and-admins/configuration/users/role-management) for the Media libraries application. After applying the hotfix, media file updates require the Manage media library permission.


* * *
## Refresh (September 7, 2023)
**version 26.6.0**
### New features
#### Administration
  * Listing page filters 
    * The state of listing filters in the administration now persists for the duration of each user’s browsing session.
    * The listing in the [Event log](/documentation/developers-and-admins/configuration/event-log) application now provides a filter, which makes it easier for users to find relevant events or errors.
    * The listing filter in the _Content hub_ application was extended to allow filtering based on the last update time of items.
  * [UI form components](/documentation/developers-and-admins/customization/extend-the-administration-interface/ui-form-components/reference-admin-ui-form-components) – When assigning the _Content item selector_ component to properties via the `ContentItemSelectorComponent` attribute, developers can now define which content types are selectable using an `IContentTypesFilter` implementation.


### Fixed issues
  * Membership – The Xperience-specific implementation of ASP.NET Core Identity’s [password reset functionality](/documentation/developers-and-admins/development/registration-and-authentication/forms-authentication) did not update member passwords when implemented.


* * *
### Hotfix (August 31, 2023)
**version 26.5.2**
Fixed issues
  * Rich text editor – An error was displayed when interacting with the _Rich text editor_ toolbar if the component was located in an editing form containing another field with the _Date_ or _Date and time_ data type.


* * *
### Hotfix (August 17, 2023)
**version 26.5.1**
Fixed issues
  * Admin UI customization – When initializing client admin UI modules, the system could incorrectly load files not related to the bootstrapping process, resulting in errors during application startup.


* * *
## Refresh (August 10, 2023)
**version 26.5.0**
### New features
#### Content management
  * The list of items in the [Content hub](/documentation/business-users/content-hub) application now provides a filter, which allows editors to easily view only selected content types, or items with specific workflow statuses or authentication requirements.


#### Administration
  * Administration UI development – Developers can now create filters for UI listing page, which allow users to limit which objects are displayed according to specified criteria. Filters can be added to both custom listing pages and the default listing pages in the Xperience by Kentico administration UI. See [Add a listing filter](/documentation/developers-and-admins/customization/extend-the-administration-interface/ui-pages/reference-ui-page-templates/listing-ui-page-template#add-a-listing-filter) to learn more.
  * UI form components – New _General selector_ UI [form component](/documentation/developers-and-admins/customization/extend-the-administration-interface/ui-form-components/reference-admin-ui-form-components), which allows users to choose items from any set of data defined by developers. The items offered by the selector can be of any type, including external data outside of Xperience.


#### Integrations
  * Lucene search – An external module that integrates Xperience with the latest 4.8 beta version of [Lucene.NET](https://github.com/apache/lucenenet), enabling auto-indexing of content in Xperience based on application-local, code-defined search indexes and search results retrieval. See the project’s GitHub repository for details: [xperience-by-kentico-lucene](https://github.com/Kentico/xperience-by-kentico-lucene)


* * *
### Hotfix (August 3, 2024)
**version 26.4.0 (NuGet packages not released for this update)**
Fixed issues
  * Xperience Portal – Users with [roles](/documentation/developers-and-admins/saas/xperience-portal/reference-xperience-portal-user-roles) other than _Tenant Administrator_ could not access project _Settings_ within Xperience Portal.


* * *
### Hotfix (July 20, 2024)
**version 26.4.0 (NuGet packages not released for this update)**
Fixed issues – security
  * XSS – Certain parts of the [Xperience Portal](/documentation/developers-and-admins/saas/xperience-portal) interface were potentially vulnerable to cross-site scripting.


* * *
## Refresh (July 13, 2023)
**version 26.4.0**
### New features
#### Xperience Portal
  * Backups – Users can now restore full backups of projects directly from Xperience Portal. See [Manage SaaS deployments](/documentation/developers-and-admins/deployment/deploy-to-the-saas-environment/manage-saas-deployments).


#### Content management
  * Users can now create media library folders directly in the selection dialogs provided by the [rich text editor](/documentation/business-users/rich-text-editor) (Insert image, Insert link) and the _Media file selector_ [form component](/documentation/developers-and-admins/customization/extend-the-administration-interface/ui-form-components/reference-admin-ui-form-components).


### Fixed issues
  * Users – An error occurred when adding a new [user](/documentation/developers-and-admins/configuration/users/user-management) in the _Users_ application if the site configured in the _Sites_ application was not running on the same domain as the administration.
  * Xperience Portal
    * If a new user was invited to join multiple [Xperience Portal](/documentation/developers-and-admins/saas/xperience-portal) projects, the system sent a corresponding number of invitation emails with an account activation link. The link was only valid in the latest email. After the fix, the account activation link is only present in the first “Your account has been created” email.
    * Reports in the _Metrics_ application were not loaded correctly during deployments to production environments (and for some time after the deployment finished).
    * Users with the _Developer_ [role](/documentation/developers-and-admins/saas/xperience-portal/reference-xperience-portal-user-roles) were incorrectly allowed to cancel scheduled deployments to the Production environment. 


* * *
### Hotfix (July 7, 2023)
**version 26.3.3**
Fixed issues
  * Emails – The _Send test email_ feature in the _Email queue_ application used a fixed sender (from) address, which caused issues under certain configurations. The hotfix enables users to set the _From_ address for test emails.


* * *
### Hotfix (June 29, 2023)
**version 26.3.2**
Fixed issues
  * CI/CD – The system performed unnecessary optimization of the file system repository after completing the [Continuous Deployment](/documentation/developers-and-admins/ci-cd/continuous-deployment) store operation.


* * *
### Hotfix (June 22, 2023)
**version 26.3.1**
Fixed issues – security
  * Permissions – Administration interface access [permissions](/documentation/developers-and-admins/configuration/users/role-management) were not correctly checked for form component actions.


Fixed issues
  * Data protection – The anti-forgery cookie for live site visitors (_.AspNetCore.Antiforgery. <hash>_) was incorrectly removed when tracking consent was revoked. This caused certain interactive elements on the site to be unusable (consent agreements, form submissions, etc.).
  * SaaS environment
    * Database usage was not monitored correctly when the active database was swapped for a production environment of a deployed project. This could prevent the _DTU utilization_ alert from being fired.
    * The _Memory utilization_ alert was not fired for [projects deployed in the SaaS environment](/documentation/developers-and-admins/deployment/deploy-to-the-saas-environment/manage-saas-deployments). 


* * *
## Refresh (June 15, 2023)
**version 26.3.0**
### New features
#### Content management
  * Media libraries 
    * Content editors are now able to [rename folders](/documentation/business-users/media-libraries/manage-media-files) in media libraries and edit the metadata of media library files, such as title or description.


#### Administration
  * UI form components – A new UI [form component](/documentation/developers-and-admins/customization/extend-the-administration-interface/ui-form-components/reference-admin-ui-form-components) was introduced to enable safe displaying of links within forms in the administration interface.


### Fixed issues
  * Emails – Tracking of bounced emails, as well as delivered email and spam report [statistics](/documentation/business-users/digital-marketing/emails/track-email-statistics) didn’t work for projects [deployed to the SaaS environment](/documentation/developers-and-admins/deployment/deploy-to-the-saas-environment). The issue also caused errors in the system’s Event log.
  * Page Builder – When attempting to [copy any widget](/documentation/business-users/website-content/widgets-and-page-builder) in the Page Builder interface containing invalid or deprecated HTML (e.g., a _Rich text_ widget with HTML inserted using the _Code View_ feature), errors were logged in the JavaScript console and the widget was not copied. After applying the refresh, the widget is copied, but a preview thumbnail is not available when inserting the widget.


* * *
### Hotfix (June 8, 2023)
**version 26.2.2 (NuGet packages not released for this update)**
New features
  * [Xperience Portal](/documentation/developers-and-admins/saas/xperience-portal) – New _Alerts_ application in Xperience Portal, which notifies about potential problems with [projects deployed in the SaaS environment](/documentation/developers-and-admins/deployment/deploy-to-the-saas-environment/manage-saas-deployments). For example, an alert is fired if a deployed project is unresponsive for over 15 minutes. The system also sends notification emails to Xperience Portal users when an alert is fired. Users can enable or disable the email notifications for specific alert severity levels.


Fixed issues
  * SaaS environment – The _Event log_ in the _Monitoring_ application of [Xperience Portal](/documentation/developers-and-admins/saas/xperience-portal) was flooded with scheduler-related errors (“An attempt was made to access a socket in a way forbidden by its access permissions.“).
  * Xperience Portal
    * Reports in the _Metrics_ application were not loaded when the deployed project was under heavy load. After the update, the _DTU_ report in the _Metrics_ application no longer detects and identifies which database is active or inactive (two databases are used for production deployment environments). Instead, the databases are always named _xperience_blue_ and _xperience_green_. The active/inactive status of the databases can be interpreted from the values in the DTU report.
    * The Terms of Service (ToS) acceptance screen was displayed incorrectly if a ToS update occurred while a user had the Deployments application open in [Xperience Portal](/documentation/developers-and-admins/saas/xperience-portal).
    * Time values in the _Deployment history_ of projects in Xperience Portal were set incorrectly in certain cases. This could prevent the deployments from being displayed in chronological order.
    * When hovering over charts in the _Monitoring → Metrics_ application, values of 0 were incorrectly displayed as “(no data)”. After the update, values equal to 0 are displayed as “0.00”, and “(no data)” is only displayed if the underlying value is _null_. 


* * *
### Hotfix (June 1, 2023)
**version 26.2.2**
Fixed issues
  * Field editor – The _Field comparison_ [validation rule](/documentation/developers-and-admins/customization/extend-the-administration-interface/ui-form-components/ui-form-component-validation-rules) couldn’t be added to content type fields in the Content types application.


* * *
### Hotfix (May 25, 2023)
**version 26.2.1**
Fixed issues
  * Contact management – When deployed to Azure Web Apps, the application generated unnecessary anonymous [contacts](/documentation/business-users/digital-marketing/contact-management) when processing requests from the environment’s bot services (Application Insights, Always On). For example, the issue occurred on projects deployed to the SaaS environment.
  * Media libraries
    * The system incorrectly checked the _Delete_ [permission](/documentation/developers-and-admins/configuration/users/role-management) for the Media libraries application when a user deleted a media library folder. After applying the hotfix, the  _Manage media library_ permission is required instead.
    * When a media library folder was deleted, the change wasn’t synchronized to other instance of the application when using [Auto-scaling support](/documentation/developers-and-admins/configuration/auto-scaling-support). 
  * SaaS environment
    * The _Event log_ in the _Monitoring_ application of Xperience Portal was flooded with scheduler-related errors (“An attempt was made to access a socket in a way forbidden by its access permissions.“).
    * The hotfix introduces new `UseKenticoCloud` middleware that must be added to all projects intended for [SaaS deployment](/documentation/developers-and-admins/deployment/deploy-to-the-saas-environment). All new projects installed with the `--cloud` parameter contain the middleware by default. For existing SaaS projects under development, the middleware must be added manually (to the _Program.cs_ file by default). See [Configure new projects](/documentation/developers-and-admins/development/website-development-basics/configure-new-projects) for the required middleware order. 
  * Settings – When a setting was updated in the _Settings_ applications, the system did not immediately reflect the changed value.


* * *
## Refresh (May 18, 2023)
**version 26.2.0**
### New features
#### Digital marketing
  * Emails 
    * The system can now track bounces and delivery rates for emails of the _Regular_ type, as well as bounces for individual contacts in recipient lists. This allows you to identify addresses that do not correctly receive emails, which helps keep your recipient lists healthy and protects your sender reputation. For more information, see [Set up email tracking](/documentation/developers-and-admins/digital-marketing-setup/set-up-email-tracking) and [Send regular emails to subscribers](/documentation/business-users/digital-marketing/emails/send-regular-emails-to-subscribers).
    * Marketers can now manually trigger a _Refresh_ in the _Statistics_ view of emails, which immediately recalculates and displays [statistics](/documentation/business-users/digital-marketing/emails/track-email-statistics) for the given email.
    * New _Unsubscribe rate_ statistic for _Regular_ emails. Shows the percentage and exact number of recipients who used the email’s unsubscribe link.
    * New _Spam reports_ statistics for _Regular_ emails, available when using [SendGrid](/documentation/developers-and-admins/configuration/email-configuration) to send emails. Shows how many recipients marked the email as spam in their email client.
    * New _Send email_ permission for the _Emails_ application, which can be assigned to administration user [roles](/documentation/developers-and-admins/configuration/users/role-management). This permission is required to send or schedule [emails](/documentation/business-users/digital-marketing/emails) of the _Regular_ type. To preserve functionality for existing roles, the update automatically grants the _Send email_ permission to all roles with the _Update_ permission for the _Emails_ application.


### Updates and changes
  * Emails – Recipient lists for [regular emails](/documentation/business-users/digital-marketing/emails/send-regular-emails-to-subscribers) are no longer managed in the _Contact groups_ application. Instead, the new _Recipient lists_ application provides a separate management UI for this purpose. 
    * To preserve functionality, the update automatically copies all permissions that existing [administration user roles](/documentation/developers-and-admins/configuration/users/role-management) have for the _Contact groups_ application, and grants them for the new _Recipient lists_ application.
  * Contact management – The _Contact groups_ and _Recipient lists_ applications no longer allow contacts to be manually removed from the group or list. The option to remove contacts was misleading – the contact list is recalculated automatically based on the contact group’s condition or managed by subscriptions and unsubscriptions for recipient lists.


### Fixed issues
  * Database – The system persisted incorrect _Info_ object state in its cache in cases where a database transaction failed and was rolled back. For example, if a database transaction updating a _MediaFileInfo_ object failed, the database state was not updated, but the updated state persisted in the system cache. This lead to data inconsistency between the application and the database, and could cause a subsequent _Get_ operation to obtain incorrect data (when retrieved from the system cache).
  * Page Builder – Scrolling did not work correctly in the _Page selector_ editing component dialog when using the [compatibility mode](/documentation/developers-and-admins/upgrade-to-xperience-by-kentico/editing-components-in-xperience-by-kentico) of Page Builder.
  * Rich text editor – On administration pages containing multiple [Rich text editor](/documentation/business-users/rich-text-editor) components, certain toolbar options (_Insert image_ , _Insert link_ , _Insert Dynamic Text_ , _Insert Double Opt-In Link_) didn’t work correctly and could interact with the wrong instance of the editor. The issue could also affect custom plugins registered for the rich text editor. If you have custom plugins, review the [updated documentation](/documentation/developers-and-admins/configuration/rich-text-editor-configuration/rich-text-editor-plugins) and make sure your plugins are registered correctly.
  * UI form components – Selecting content items via the _Content item selector_ component resulted in duplicate selection if the checkbox was used to select individual items.


* * *
### Hotfix (May 11, 2023)
**version 26.1.3**
Fixed issues – security
  * XSS – Page preview URLs were vulnerable to reflected XSS attacks due to improper processing. The vulnerability was exploitable only by authenticated users.


Fixed issues
  * Object types – The hotfix removes the ability to define [object type fields](/documentation/developers-and-admins/customization/object-types) of the _Pages_ and _Content items_ data types. Fields of these data types were incorrectly available to object type classes for a brief period, but their support was never intended. Note that already created object type classes making use of these data types remain unaffected. However, the system is not prepared to handle them correctly, and certain features, such as code generators, produce incorrect results. This change does not impact [content types](/documentation/developers-and-admins/development/content-types) in any way.
  * UI form components – It wasn’t possible to enter values consisting only of zeros (e.g., ‘00.0’) into numeric inputs, such as the Number input [form component](/documentation/developers-and-admins/customization/extend-the-administration-interface/ui-form-components/reference-admin-ui-form-components).


* * *
### Hotfix (May 4, 2023)
**version 26.1.2**
Fixed issues
  * Admin UI – Replaced usage of the deprecated onKeyPress event with the onKeyDown event in the client code of administration components. This change does not impact the existing public API.


* * *
### Hotfix (April 27, 2023)
**version 26.1.1**
Fixed issues
  * UI form components – Validation errors weren’t displayed correctly in certain cases when using the _Field comparison_ [validation rules](/documentation/developers-and-admins/customization/extend-the-administration-interface/ui-form-components/ui-form-component-validation-rules) for UI form components in the administration.
  * Xperience Portal – The _Application health_ report in the _Metrics_ application of [Xperience Portal](/documentation/developers-and-admins/saas/xperience-portal) wasn’t loaded and displayed correctly in certain cases, particularly after selecting the _Last 30 days_ time period.


* * *
## Refresh (April 20, 2023)
**version 26.1.0**
### New features
#### Digital marketing
  * Activities – Added support for custom activity logging from client-side code. This allows tracking of basic interactions with important page elements, for example clicks of “call to action” buttons or links. See [Custom activities](/documentation/developers-and-admins/digital-marketing-setup/set-up-activities/custom-activities).
  * Forms – Individual _featured fields_ can now be hidden from the dialog that appears when [adding fields](/documentation/business-users/digital-marketing/forms/create-and-edit-forms) in the Form Builder. This allows users to filter out featured fields that are not relevant for their forms.


#### Content management
  * Media libraries – Content editors are now able to [move files](/documentation/business-users/media-libraries/manage-media-files) between folders of a media library.


#### Xperience Portal
  * New _Metrics_ application in Xperience Portal, which displays various performance metrics for [deployments in the SaaS environment](/documentation/developers-and-admins/deployment/deploy-to-the-saas-environment/manage-saas-deployments).


#### Administration
  * New _Field comparison_ [validation rules](/documentation/developers-and-admins/customization/extend-the-administration-interface/ui-form-components/ui-form-component-validation-rules) for UI form components. The rules are available for integer, decimal, floating-point number and string type fields, and allow comparisons with other fields of the same data type.


### Fixed issues
  * Admin UI – Fixed inconsistent spacing between elements in the administration UI.
  * Emails – An error occurred when attempting to delete a [recipient list](/documentation/business-users/digital-marketing/emails/send-regular-emails-to-subscribers) containing one or more contacts.
  * Forms – The system performed unnecessary queries when loading and displaying the featured field options for new [form fields](/documentation/business-users/digital-marketing/forms/create-and-edit-forms).
  * SaaS deployment – The _cms.role_ object type was missing in the default [Continuous Deployment](/documentation/developers-and-admins/ci-cd/continuous-deployment) configuration file of new projects for SaaS deployment (installed with the `--cloud` parameter). As a result, role data wasn’t included in the deployment.
  * Xperience Portal
    * Storage account outages were not tracked correctly, and were not displayed correctly in the _Monitoring > Outages_ application of Xperience Portal.
    * The date and time of the latest deployment for environments in [Xperience Portal](/documentation/developers-and-admins/saas/xperience-portal) was displayed incorrectly in certain cases. 


* * *
### Hotfix (April 13, 2023)
**version 26.0.3**
Fixed issues – security
  * Roles – The [roles](/documentation/developers-and-admins/configuration/users/role-management) of administration users could be modified without sufficient permissions in certain cases.


Fixed issues
  * Cross-site tracking – The tracking snippet generated for [tracked websites](/documentation/developers-and-admins/digital-marketing-setup/cross-site-tracking) in the _Cross-site tracking_ application did not contain the tracked site’s URL. This issue occurred after updating a project from an older version, and also for projects deployed to the SaaS environment.


* * *
### Hotfix (April 6, 2023)
**version 26.0.2**
Fixed issues
  * Admin UI – If an error notification bar appeared within a modal dialog and the error text was very long, the dialog was incorrectly resized to fit the text.
  * Rich text editor – Adding image links to [rich text editor](/documentation/business-users/rich-text-editor) content did not work correctly when using the _Insert link > Asset_ toolbar option.


* * *
### Hotfix (March 31, 2023)
**version 26.0.1**
Fixed issues
  * Admin UI – If a selector component was used in a modal dialog, the selection side panel was displayed under the dialog and couldn’t be used. For example, the problem occurred when selecting a page in the Approval or Unsubscribe settings of a recipient list in the _Contact groups_ application.
  * Forms
    * It was possible to submit [forms](/documentation/business-users/digital-marketing/forms) without selecting a value in a  _Drop-down_ list field, even if the field was set as required.
    * The Form Builder interface was not correctly displayed as read-only for users without the  _Update_ [permission](/documentation/developers-and-admins/configuration/users/role-management) for the  _Forms_ application. 
  * Installation – The [user-defined table type](https://learn.microsoft.com/en-us/sql/relational-databases/server-management-objects-smo/tasks/using-user-defined-tables) _Type_OM_OrderedIntegerTable_DuplicatesAllowed_ used by the Xperience database installation scripts was not defined correctly. In rare cases, this could have caused issues when recalculating digital marketing [activities](/documentation/developers-and-admins/digital-marketing-setup/set-up-activities).
  * Pages
    * The `PageUrl.AbsoluteUrl` property [returned](/documentation/developers-and-admins/development/content-retrieval/retrieve-page-content) by the `IPageUrlRetrieverService` incorrectly cached the URL scheme (protocol) of the request under which the service was first called. For example, if the service was first called within a request with the HTTP scheme, all subsequest URLs in the `AbsoluteUrl` property were also returned with HTTP.
    * When pages contained images in their rich text fields, a dialog notifying about unsaved changes was displayed when leaving the page, even if no changes were made. 
  * UI form components – The [TextArea component](/documentation/developers-and-admins/customization/extend-the-administration-interface/ui-form-components/reference-admin-ui-form-components) did not correctly reflect the `MinRowsNumber` and `MaxRowsNumber` properties when only one of them was explicitly provided. That is, the text area did not display the specified minimum number of rows when only `MinRowsNumber` was provided and did not grow to the specified size when only `MaxRowsNumber` was provided.


* * *
## Refresh (March 27, 2023)
**version 26.0.0**
### New features
#### General
  * Xperience web applications now support [registration and authentication](/documentation/developers-and-admins/development/registration-and-authentication) using ASP.NET Identity.
  * New [Members](/documentation/business-users/members) application for management of registered accounts.
  * Users can check which version of Xperience they are using via the newly added _Product information_ icon displayed above the user menu in the administration interface.
  * The Xperience by Kentico source code is now available on the [client portal](https://client.kentico.com/) for those who purchase it as part of their subscription.


#### Content management
  * Page Builder – Content editors can now [copy entire sections](/documentation/business-users/website-content/widgets-and-page-builder) and paste them on any page where page builder is available.
  * Media libraries – Content editors are now able to [create and delete folders](/documentation/business-users/media-libraries/manage-media-files) in the _Media libraries_ application.
  * Sections of the website, pages, and individual content items can now require visitors to be authenticated before they can access the given content. To implement authentication, see [Registration and authentication](/documentation/developers-and-admins/development/registration-and-authentication). See [Secure pages](/documentation/business-users/website-content/secure-pages) and [Content hub](/documentation/business-users/content-hub/content-items#secure-content-items) for information about securing pages and individual content items. To learn how to implement authentication checks in your code, see [Retrieve page content](/documentation/developers-and-admins/development/content-retrieval/retrieve-page-content#page-security-configuration) and [Retrieve content items](/documentation/developers-and-admins/development/content-retrieval/retrieve-content-items#content-item-security).


#### Digital marketing
  * Contact management 
    * Added support for defining custom activity types, which allows marketers to track any required action performed by contacts. Custom activities can by logged using the API or via cross-site tracking. See [Custom activities](/documentation/developers-and-admins/digital-marketing-setup/set-up-activities/custom-activities).
    * Marketers can now enable or disable individual activity types in the _Contact management_ application (applies to both default and custom activity types).
    * New _Contact has performed activity with value_ condition type for [contact groups](/documentation/business-users/digital-marketing/contact-groups).
  * Forms – The _reCAPTCHA_ [form component](/documentation/developers-and-admins/development/builders/form-builder/reference-form-builder-components) was updated to support reCAPTCHA v3. This version of reCAPTCHA provides frictionless validation without interrupting users. A score is calculated for each request, indicating whether an interaction is likely to be a bot or a valid human user.


#### Xperience Portal
  * New _Event log_ and _Exceptions_ applications in Xperience Portal, which display Application Insights data for [deployments in the SaaS environment](/documentation/developers-and-admins/deployment/deploy-to-the-saas-environment/manage-saas-deployments).


#### Administration
  * The refresh introduces changes to confirmation dialogs that can be raised by [listing and edit UI pages](/documentation/developers-and-admins/customization/extend-the-administration-interface/ui-pages/reference-ui-page-templates). Confirmation dialogs shown by these pages can now optionally contain forms (typically with a multi-choice or checkbox option), that can be used to control the behavior of the corresponding [page command handlers](/documentation/developers-and-admins/customization/extend-the-administration-interface/ui-pages/ui-page-commands).
  * New _MaximumDoubleValue_ and _MinimumDoubleValue_ [validation rules](/documentation/developers-and-admins/customization/extend-the-administration-interface/ui-form-components/ui-form-component-validation-rules) for UI form components.


### Updates and changes
  * Settings – The _System → Files_ category and its _Generate thumbnails_ setting are no longer displayed in the _Settings_ application. The setting is always enabled by default.
  * Emails
    * The _Sender name_ and _Sender email_ properties can no longer be set immediately when creating new emails. These properties remain available in the _Properties_ panel when editing existing emails.
    * The _Properties_ panel of emails is now organized into two collapsible categories – _General_ and _Sender and recipients_.
  * Contact management – The _Status_ column is no longer displayed in the [contact](/documentation/business-users/digital-marketing/contact-management) listing. This column is currently not used in Xperience by Kentico.
  * SendGrid integration – The system now uses a direct dependency on the [SendGrid](https://www.nuget.org/packages/SendGrid) NuGet package (updated to version 9.28.1).


### Breaking changes – API
  * `PageFormSubmissionPageBase.GetFormComponents` was made asynchronous and now returns `Task<ICollection<IFormComponent>>`. To recover from the breaking change: 
    * await the method call and change the signature of the overridden method
    * pass a `CancellationToken` to the method call
  * `IContentTypeFieldsProvider.GetFields` was made asynchronous and now returns `Task<ICollection<IFormFieldInfo>>` . To recover from the breaking change: 
    * await the method call and change the signature of the overridden method
    * pass a `CancellationToken` to the method call
  * The `GetAssetPanelInitialPropertiesResult` command result type used by `RichTextEditorComponent` was renamed to `GetMultiSourceAssetPanelPropertiesResult` . 
    * The type’s `Enabled` property was renamed to `AssetPanelEnabled`.
  * The `ReviewAndSendDialog` UI page was moved from the `Kentico.Xperience.Admin.Base.UIPages` namespace to `Kentico.Xperience.Admin.DigitalMarketing.UIPages`.
  * The `IPageManager` interface, used to manage page hierarchy, was extended with an additional `CheckSecurityMismatch` method.


### Newly obsolete API
  * `IPageManager.Move(TreeNode, TreeNode, PageDropPlacement)` - use `IPageManager.Move(PageMoveParameters)` instead.
  * The refresh introduces changes to confirmation dialogs that can be raised by [Listing](/documentation/developers-and-admins/customization/extend-the-administration-interface/ui-pages/reference-ui-page-templates/listing-ui-page-template) and [Edit](/documentation/developers-and-admins/customization/extend-the-administration-interface/ui-pages/reference-ui-page-templates/edit-ui-page-template) [UI pages](/documentation/developers-and-admins/customization/extend-the-administration-interface/ui-pages) (e.g., when invoking actions or saving changes). 
    * The following properties from `EditConfiguration` were consolidated into `EditConfiguration.SubmitConfiguration`.
      * SubmitVisible
      * SubmitLabel
      * SubmitTooltipText
    * The following properties from `EditTemplateClientProperties` were consolidated into `EditTemplateClientProperties.SubmitButton`.
      * SubmitVisible
      * SubmitLabel
      * SubmitTooltipText
    * The following properties from `Action` (descriptor of interactive elements on listing UI pages) were consolidated into `Action.ConfirmationDialog` .
      * Confirmation
      * ConfirmationContent
      * ConfirmationDetail
      * ConfirmationButton
    * The following properties from `ActionConfiguration` were consolidated into `ActionConfiguration.ConfirmationConfiguration`.
      * Confirmation
      * ConfirmationContent
      * ConfirmationDetail
      * ConfirmationButton
  * `ListingPage.GetRowsFromData` was extended with a cancellation token parameter. The original method signature is now obsolete.


### Removed obsolete API
The refresh releases removes all API marked _Obsolete_ since version 22.0.0.
### Fixed issues – security
  * Xperience Portal – Under certain circumstances, there was a risk an unauthorized third party could access and download backups of projects deployed in the SaaS environment.


### Fixed issues
  * Administration – In certain cases, it was possible to submit values using disabled fields in the administration’s editing forms.
  * Content types – If the _Display in editing form_ option was enabled in the [field editor](/documentation/developers-and-admins/customization/field-editor) for a content type field with the _Content items_ data type, an error occurred when configuring further properties for the field’s form component.
  * Deployment – Sites [deployed without the administration](/documentation/developers-and-admins/deployment/deploy-to-private-cloud/deploy-without-the-administration) crashed during the startup process (“Unable to resolve service for type `Kentico.PageBuilder.Web.Mvc.Internal.IComponentPropertiesStorageProcessor`“ exception).
  * Digital marketing – Various usability improvements were made in the UI of the _Emails_ , _Email templates_ and _Contact groups_ applications.
  * Emails
    * Added a new friendly warning to inform users when attempting to send a [regular email](/documentation/business-users/digital-marketing/emails/send-regular-emails-to-subscribers) to an empty recipient list.
    * Improved UI and explanation texts for emails that were sent, but do not have any logged statistics.
    * Pressing CTRLF while focused in the _Source code_ editor of email templates triggered the editor’s built-in search functionality, which was not intended. After applying the refresh, the browser’s standard “Find in page” functionality is prioritized.
    * Some parts of the email UI remained active even if the overall editing interface was disabled, e.g., due to missing update permissions or when viewing a regular emails that was already sent out. For example, the problem affected the editing dialog for double opt-in links in form autoresponder emails, the email _Plain text_ content editor, and certain header action buttons.
    * The content editor of form autoresponder emails displayed the placeholder for double opt-in links incorrectly if the link’s text contained special characters.
    * The editing dialog for double opt-in links in form autoresponder [emails](/documentation/business-users/digital-marketing/emails) didn’t check for unsaved changes. After applying the refresh, a confirmation prompt is displayed when attempting to close the dialog with a modified link text or recipient list. 
  * Modules – Field settings related to data type integrity were not validated correctly in the editor for the UI forms of [module classes](/documentation/developers-and-admins/customization/object-types). For example, the problem allowed invalid values for the number of digits and decimal places of _Decimal number_ type fields in UI forms.
  * Page Builder – Images added using the `url()` CSS function in the _Code View_ mode of the _Rich text_ widget’s editor were not displayed on the live site or in the Page Builder.
  * Project templates – When creating a SaaS environment [deployment package](/documentation/developers-and-admins/deployment/deploy-to-the-saas-environment) for a project based on the Dancing Goat project template, [content item](/documentation/business-users/content-hub/content-item-assets) asset files were incorrectly duplicated in the package (in both the standard asset folder and the Continuous Deployment repository). After the refresh, SaaS deployment packages created for new Dancing Goat project no longer include the _~/assets_ folder.
  * Rich text editor
    * If the rich text editor had a custom [toolbar configuration](/documentation/developers-and-admins/configuration/rich-text-editor-configuration) with the `imageReplace` button, the button displayed a default Froala dialog instead of the system’s dialog for selecting images.
    * Inserting of links in the [rich text editor](/documentation/business-users/rich-text-editor) didn’t work correctly in certain cases in the _Content_ view mode of the _Pages_ and _Emails_ applications.
    * On administration UI pages with a right-side panel, the panel didn’t close correctly if the user interacted with the rich text editor toolbar on the main editing page. For example, the problem could occur when editing emails in the _Emails_ application.
    * The _Insert image_ and _Insert link_ dialogs in the rich text editor displayed the _Media library_ selection tab even if there were no media libraries present in the system.
    * The rich text editor _Insert image_ and _Insert link_ dialogs did not work correctly when the rich text component was used to edit the property of a [form component](/documentation/developers-and-admins/development/builders/form-builder/form-components) in the Form Builder.
    * When selecting a link to a page or content item in the rich text editor, the full toolbar was incorrectly displayed instead of just the link-related toolbar options. 


* * *
### Hotfix (March 9, 2023)
**version 25.0.2**
Fixed issues
  * Content hub – When updating a  _Content item asset_ field in an existing [content item](/documentation/business-users/content-hub) and not publishing the item after, the original asset was displayed instead of the updated asset.


* * *
### Hotfix (March 2, 2023)
**version 25.0.1**
Fixed issues
  * Admin UI – In certain cases, it was possible to submit values using disabled fields of admin UI forms.
  * MacOS – It was not possible to [install the Xperience database](/documentation/developers-and-admins/installation) on Apple devices equipped with the Apple silicon family of CPUs.
  * Rich text editor – The _Insert image_ and _Insert link_ dialogs in the rich text editor incorrectly displayed content items with a _Content item asset_ field but no file uploaded. This could occur when a content type was created with an optional asset field.


* * *
## Refresh (February 23, 2023)
**version 25.0.0**
### New features
#### Content management
  * Content item assets – [Content item assets](/documentation/business-users/content-hub/content-item-assets) are now also available for selection in the [rich text editor](/documentation/business-users/rich-text-editor) and the _Rich text_ Page Builder widget.


#### Digital marketing
  * [Emails](/documentation/business-users/digital-marketing/emails)
    * Users can now preview and test the content of emails by sending a draft version.
    * New option to clone emails, which allows users to quickly create new emails based on the content and settings of existing emails.
    * The content editor now allows editing of [double opt-in links](/documentation/business-users/digital-marketing/emails/send-regular-emails-to-subscribers) placed into form autoresponder emails. This allows users to quickly update the text and recipient list set for existing links.
    * The _Emails_ application now displays a status for emails of the _Regular_ type (possible options are _Draft_ , _Scheduled_ , _Sending_ and _Sent_).


#### Xperience Portal
  * [Backups](/documentation/developers-and-admins/deployment/deploy-to-the-saas-environment/manage-saas-deployments#restore-points-and-exports) – Users can now manually create and download backups of applications deployed in the [SaaS environment](/documentation/developers-and-admins/deployment/deploy-to-the-saas-environment). Backups can be used to locally inspect or debug the application.


### Updates and changes
  * The [Froala WYSIWYG editor](https://froala.com/wysiwyg-editor) that provides the [Rich text editor](/documentation/business-users/rich-text-editor) in Xperience was updated to version 4.0.17. See [Froala Editor 4.0.17](https://froala.com/blog/editor/new-releases/froala-editor-4-0-17-release/) for details.
  * Emails – The editing interface of [Regular emails](/documentation/business-users/digital-marketing/emails/send-regular-emails-to-subscribers) now becomes read-only after the sending starts.


### Breaking changes
  * [Global events](/documentation/developers-and-admins/customization/handle-global-events/reference-global-system-events) – When deleting objects, the system now checks for depending objects before triggering the `Delete` event (`ObjectEvents` or `*Info.TYPEINFO.Events`). If any depending objects exist, the event is not triggered at all.


### Breaking changes – API
  * Application startup – `IFeaturesBuilder` interface members `BeforeConfiguration` and `AfterConfiguration` were not intended to be used in custom code and were removed from the public API. [Extension methods for this interface](/documentation/developers-and-admins/development/website-development-basics/configure-new-projects/enable-application-features) (e.g., `UsePageBuilder` ) are used to add types required by various Xperience features into the application’s [IoC container](/documentation/developers-and-admins/development/website-development-basics/dependency-injection).
  * The `IDataQuery` interface (`CMS.DataEngine`) contains two new methods: 
    * `GetAsyncEnumerableResult`
    * `GetScalarResultAsync`


### Newly obsolete API
  * Emails – The `CMS.EmailEngine.EmailSender` class is now obsolete. Use `IEmailService` to send emails (see the [Email queue](/api/configuration/email-queue) API example).


### Fixed issues
  * Admin UI
    * Added missing spacing to certain selection dialogs.
    * Disabled editing forms in the administration (e.g. due to missing [update permissions](/documentation/developers-and-admins/configuration/users/role-management)) behaved inconsistently in certain cases. After applying the refresh, disabled editing forms always contain a warning message and have a disabled _Save_ button with a tooltip.
    * The administration UI breadcrumbs didn’t correctly shorten text for objects with very long names. 
  * CI/CD
    * If an object was deleted, but the operation was stopped due to the existence of depending objects, the files representing the object and its dependencies in the [Continuous Integration](/documentation/developers-and-admins/ci-cd/continuous-integration) repository were removed even though the object was not actually deleted.
    * The XML files representing contact groups of the _Recipient list_ type in the CI/CD repository incorrectly used 0 or 1 values for the _ContactGroupIsRecipientList_ boolean property, which could cause inconsistencies. After applying the refresh, the property stores _True_ or _False_ values. 
  * Contact groups – When viewing the details of _Recipient list_ contact groups, text in the _Approval_ and _Unsubscribe settings_ areas could overflow when viewed with a small display width.
  * Content types – When adding a new content type field with the _Content item_ selector form component, the _Allowed content type_ option could not be configured until the field was saved.
  * Cross-site tracking – Certain requests returned during [cross-site tracking](/documentation/developers-and-admins/digital-marketing-setup/cross-site-tracking) used an incorrect `X-Frames-Options` header. This combination of the header and its value is now deprecated in modern browsers. After applying the refresh, the `content-security-policy` header is used for this purpose.
  * Emails
    * If the application crashed or was stopped while sending a [regular email](/documentation/business-users/digital-marketing/emails/send-regular-emails-to-subscribers) to recipients, the mailout remained stuck and didn’t recover after the application restarted.
    * If the send date or time of a [regular email](/documentation/business-users/digital-marketing/emails/send-regular-emails-to-subscribers) was changed in the _Reschedule_ dialog and the sendout was then cancelled, the dialog was not refreshed correctly and the _Cancel scheduled sendout_ button remained visible.
    * The _Preview_ mode of emails incorrectly allowed links in the email content to be clicked, which could lead to inconsistent behavior. After applying the refresh, links are no longer active when clicked in the email _Preview_.
    * The system created redundant anonymous [contacts](/documentation/business-users/digital-marketing/contact-management) in certain cases when a user confirmed their [email subscription](/documentation/business-users/digital-marketing/emails/send-regular-emails-to-subscribers) by clicking a double opt-in link. For example, this could occur if the recipient opened the double opt-in link in a different browser than the one where the original subscription form was submitted. After applying the refresh, such anonymous contacts are automatically merged into the recipient’s main contact. 
  * Performance – The number of database queries called when loading the content of [Page Builder widgets](/documentation/developers-and-admins/development/builders/page-builder/widgets-for-page-builder) was optimized. The original performance issue occurred after updating to version 24.0.0.
  * Project templates – The sample site created by the _Dancing Goat_ [project template](/documentation/developers-and-admins/installation) didn’t correctly handle situations where certain content items were deleted, which could result in errors on the live site.
  * Role management
    * If a user with the _Administrator_ [role](/documentation/developers-and-admins/configuration/users/role-management) unassigned this role from their own account, an _Access denied_ notification was displayed even though the operation was valid and the role was unassigned.
    * When using [external authentication for the administration](/documentation/developers-and-admins/configuration/users/administration-registration-and-authentication/administration-external-authentication) with the user synchronization frequency set to `UserSynchronizationFrequency.Always`, users with the _Administrator_ role were incorrectly allowed to edit the role assignments of users managed by the external authentication provider. 
  * UI form components
    * Fields based on certain form components did not display their tooltip (e.g. the _Page selector_ or _Content item selector_).
    * Fields based on the _Number input_ form component incorrectly displayed _null_ as their value when the value was empty.
    * If a field based on the _Object selector_ had a large number of items available and a value was already selected, the list of items loaded and displayed the first batch of items twice.
    * If a field based on the _Radio group component_ did not have a label assigned, the resulting UI page generated warnings in the browser console. For example, the problem occurred in the _Review and send_ dialog for emails in the administration. 
  * Xperience Portal – The _DevOps Engineer_ role did not have access to the _Outages_ application in [Xperience Portal](/documentation/developers-and-admins/saas/xperience-portal).


* * *
### Hotfix (February 16, 2023)
**version 24.0.3**
New features
  * Admin UI authentication – The hotfix introduces new `OnSigningIn` and `OnSigningOut` events, which are invoked when users sign in or out of the Xperience administration. Both events are available under `AuthenticationOptions.CookieEventsOptions` when configuring `AdminIdentityOptions`. See [Administration - Forms authentication](/documentation/developers-and-admins/configuration/users/administration-registration-and-authentication/administration-forms-authentication) for details.


Fixed issues
  * General – The application could reach the preset limit on the number of database connections under heavy traffic, e.g., during a load test. In the worst case, this could result in HTTP 502 Gateway errors.
  * Page Builder – [Widgets](/documentation/developers-and-admins/development/builders/page-builder/widgets-for-page-builder) that had [output caching](/documentation/developers-and-admins/development/caching/output-caching) disabled consumed unnecessary memory and the application did not clear this memory correctly.


* * *
### Hotfix (February 9, 2023)
**version 24.0.2**
Fixed issues
  * Content items 
    * Files stored as content item assets were served with an incorrect file name when downloaded on the live site.
    * On pages based on a [page template](/documentation/business-users/website-content/page-templates), linked content items that were selected in a particular order were displayed in a random order when viewed on the live site. 
  * Page Builder – After applying hotfix 24.0.1, it was not possible to publish pages that used [Page Builder](/documentation/business-users/website-content/widgets-and-page-builder) and contained sections without properties.


* * *
### Hotfix (February 2, 2023)
**version 24.0.1**
Fixed issues
  * Performance – An unnecessary number of database queries was performed when loading the content of [Page Builder widgets](/documentation/business-users/website-content/widgets-and-page-builder). This issue occurred after updating to version 24.0.0.
  * Roles – Modification of [user-role assignments](/documentation/developers-and-admins/configuration/users/role-management) via the administration did not work correctly in certain cases. This issue occurred only after updating to version 24.0.0.


* * *
## Refresh (January 26, 2023)
**version 24.0.0**
### New features
#### Users
  * [Microsoft Azure Active Directory](https://azure.microsoft.com/en-us/products/active-directory), [Auth0](https://auth0.com/), and [Okta](https://www.okta.com/) external identity providers can now be used to manage Xperience administration users. See [Administration - External authentication](/documentation/developers-and-admins/configuration/users/administration-registration-and-authentication/administration-external-authentication).


#### Content management
  * Content item assets – [Content item assets](/documentation/business-users/content-hub/content-item-assets) are a new type of content items that allow content editors to upload and store various types of files, for example, photos, pictures, sound files, videos, package files, presentations, or documents. You can reuse assets stored in the _Content hub_ throughout the system. To create a new content item asset, create a [content type](/documentation/developers-and-admins/development/content-types) with an _Content item asset_ field.
  * Content item selector – Users are now able to select content items from the [content hub](/documentation/business-users/content-hub) in [component properties](/documentation/developers-and-admins/customization/extend-the-administration-interface/ui-form-components/reference-admin-ui-form-components).
  * Media libraries 
    * Content editors are now able to upload a new version of [media library files](/documentation/business-users/media-libraries/manage-media-files).
    * Content editors are now able to view media file information such as the GUID, Media URL, size, or image resolution.


#### Digital marketing
  * Emails 
    * New functionality that allows marketers to send regular emails to groups of recipients. Visitors subscribe by submitting a form on the website. The form sends an autoresponder email with a double opt-in link, through which recipients finish the subscription process. See [Send regular emails to subscribers](/documentation/business-users/digital-marketing/emails/send-regular-emails-to-subscribers).
    * The system now tracks and displays statistics for [emails](/documentation/business-users/digital-marketing/emails) created in the _Email templates_ and _Emails_ applications, including the number of sent emails, email opens and clicked links. See [Track email statistics](/documentation/business-users/digital-marketing/emails/track-email-statistics).
  * Forms – Users can now edit the _Code name_ of [forms](/documentation/business-users/digital-marketing/forms/create-and-edit-forms), which allows developers to work with more practical identifiers, e.g., when rendering forms in code as [stand-alone widgets](/documentation/developers-and-admins/development/builders/page-builder/render-widgets-in-code). 
    * Changes of the form code name break existing forms placed onto pages via the _Form_ widget. You need to reselect the form in these widgets after making such changes.
    * Changing the code name also automatically updates the form’s _Class name_. Such changes break existing code files [generated](/documentation/developers-and-admins/api/generate-code-files-for-system-objects) for the form, and developers need to update or regenerate the code.


#### Xperience Portal
  * When [deploying to the SaaS environment](/documentation/developers-and-admins/deployment/deploy-to-the-saas-environment), the deployment package can now be uploaded using the **Deployment** application in Xperience Portal.


### Updates and changes
#### API
  * New _CMS.EmailEngine.ISmtpClientFactory_ API that enables developers to modify the configuration of the system’s SMTP client (if using SMPT servers for mailout). This API is primarily intended for advanced environments with specific requirements.


#### General
  * Rich text editor – The ability to drag-and-drop content into the rich text editor UI form component was disabled. Use your operating system’s clipboard functionality instead.
  * Emails – The “Preheader” property of [emails](/documentation/business-users/digital-marketing/emails) was renamed to “Preview text”. The corresponding placeholder in the source code of email templates is now _$$previewtext$$_. Applying the refresh automatically updates the placeholder in the source code of existing email templates.
  * Admin UI customization – [UI pages](/documentation/developers-and-admins/customization/extend-the-administration-interface/ui-pages) for creating new objects (inheriting from the base class) no longer validate the editing form when the _Change_ [UI page command](/documentation/developers-and-admins/customization/extend-the-administration-interface/ui-pages/ui-page-commands) is executed. This prevents unnecessary validation errors while filling in parts of the create form.


#### Object types
This release changes the way [hash table caching](/documentation/developers-and-admins/customization/object-types#configure-code-generation-for-data-classes) for [object types](/documentation/developers-and-admins/customization/object-types) is configured. In previous versions, the caching was configured by passing the `HashtableSettings` object via an optional constructor parameter to the object type’s _I*InfoProvider_ implementation:
C#
**Hash table caching configuration using the provider class**
Copy
```
[ProviderInterface(typeof(IMyObjectTypeInfoProvider))]
public partial class MyObjectTypeInfoProvider : AbstractInfoProvider<MyObjectTypeInfo, MyObjectTypeInfoProvider>, IMyObjectTypeInfoProvider
{
    public MyObjectTypeInfoProvider()
            : base(MyObjectTypeInfo.TYPEINFO,
                   new HashtableSettings
                        {
                            // Enables hash table caching over the identifier and code name
                            ID = true,
                            Name = true
                        }
                   )
    {
    }
}
```

From this version onward, this approach and the corresponding _*InfoProvider_ constructor are obsolete. Instead, the caching is configured directly in the _*Info_ data class via the `InfoCache` attribute:
C#
**Hash table caching configuration via the Info data class**
Copy
```
[InfoCache(InfoCacheBy.ID | InfoCacheBy.Name)]
public partial class MyObjectTypeInfo : AbstractInfo<MyObjectTypeInfo, IMyObjectTypeInfoProvider>
{
}
```

If you are using hash table caching for custom object types, there are two ways to migrate to the new approach:
  1. Regenerate all custom object type classes using the [code generator](/documentation/developers-and-admins/api/generate-code-files-for-system-objects), which automatically ensures the new format. However, note that this will also require you to manually transfer all customizations made to the object type’s classes.
  2. Manually convert custom object type classes to the new approach: 
    1. Remove the `HashtableSettings` parameter from the _*InfoProvider_ constructor.
    2. Annotate the corresponding _*Info_ class with the `InfoCache` attribute. 
      1. Use the `InfoCacheBy` enum to determine the properties to cache by.
      2. (Optional) Use the `InfoCachePriority` enum to configure whether the cached items should expire.


#### Content hashes in admin UI script filenames
The file names of script files consumed by the Xperience admin UI now include a content hash (e.g., _kentico.xperience.admin.app.entry.kxh.**adf398f7ffd6e16a4961**.js_) This change ensures that script files cached on the client are correctly invalidated when updating to a new version. In previous versions, the client browser usually defaulted to cached scripts even if the file contents were different, requiring users to refresh the browser cache (CtrlF5) to get the latest version (or wait for cache expiration).
All newly created custom admin UI modules (see [Prepare your environment for admin development](/documentation/developers-and-admins/customization/extend-the-administration-interface/prepare-your-environment-for-admin-development)) automatically include content hashing. However, if your admin UI consumes any existing custom modules, and you wish to make use of the content hashing feature, you need to make the following changes:
  1. Update all _@kentico_ packages to 24.0.0.
  2. Open the _webpack.config.js_ of your module and follow the comments in the following snippet:
JS
**webpack.config.js**
Copy
```
const webpackMerge = require("webpack-merge");

const baseWebpackConfig = require("@kentico/xperience-webpack-config");

module.exports = (opts, argv) => {
  // Add the 'argv' parameter to the arrow function signature
  const baseConfig = (webpackConfigEnv, argv) => {
    return baseWebpackConfig({
      orgName: "acme",
      projectName: "web-admin",
      webpackConfigEnv: webpackConfigEnv,
      argv: argv,
    });
  };

  const projectConfig = {
    module: {
      rules: [
        {
          test: /\.(js|ts)x?$/,
          exclude: [/node_modules/],
          loader: "babel-loader",
        },
      ],
    },
    // Add the output-clean:true setting
    output: {
      clean: true
    },
    devServer: {
      port: 3009,
    },
  };

  // Pass the added 'argv' parameter to 'baseConfig'
  return webpackMerge.merge(projectConfig, baseConfig(opts, argv));
};
```

  3. Rebuild the module.


The output file name now contains a content hash.
#### Xperience admin UI customizations boilerplate project converted to a .NET template
The Xperience admin UI customization boilerplate project (previously available for download on [Prepare your environment for admin development](/documentation/developers-and-admins/customization/extend-the-administration-interface/prepare-your-environment-for-admin-development)) was converted to a .NET template. You can now install the project using `dotnet new kentico-xperience-admin-sample` . Afterwards, reference the created project from your Xperience application and work with it like before.
### Breaking changes
#### Changes to assembly placement in NuGet packages
The _CMS.AspNetCore.Platform_ assembly was moved from _Kentico.Xperience.Core_ to the _Kentico.Xperience.WebApp_ NuGet package and renamed to _Kentico.AspNetCore.Platform_.
For web applications, this change in not breaking – _Kentico.Xperience.Web.App_ depends on the _Kentico.Xperience.Core_ package.
However, for [other types of applications](/documentation/developers-and-admins/api/use-the-xperience-by-kentico-api-externally) (e.g., console or desktop applications), there is a possibility that your custom code called some code from the moved assembly. If you encounter breaking changes (compilation errors) in your projects after upgrading, you need to add the _Kentico.Xperience.WebApp_ NuGet package to the affected projects.
### Breaking changes – API
  * The `UserInfo.Enabled` property was fully removed, use `UserInfo.UserEnabled` instead.
  * The following members were removed from the content management API: 
    * `IPageTypeFieldsProvider` – use `IContentTypeFieldsProvider` instead.
    * `IPageTypeFieldsProviderFactory` – if this interface was used as a dependency, replace usages with `IContentTypeFieldsProvider` directly. The additional layer of abstraction introduced by the factory was removed completely.
    * `PageType` – there is no alternative. Implement a custom class to replace.
    * `LoadAvailablePageTypesResult` – there is no alternative. Implement a custom class to replace.
    * `LoadAvailablePageTypesCommandArguments` – there is no alternative. Implement a custom class to replace.
  * `PageFormSubmissionPageBase` – the constructor now depends on `IContentTypeFieldsProvider` directly.
  * The `CMS.AspNetCore.Platform` namespace was renamed to `Kentico.AspNetCore.Platform` (also includes all subnamespaces).


### Newly obsolete API
  * Data types – The `AllowedObjectTypes` property of `DataType` objects is now obsolete. Use the `IsAvailableForDataClass` predicate instead.


### Fixed issues – security
  * Permissions – Modified the behavior of various permissions in relation to digital marketing applications. 
    * Contact group edit (condition builder) can no longer be opened by users without _Update_ permissions for the _Contact groups_ application.
    * Deleting contacts from contact groups is now possible with _Update_ permissions for the _Contact groups_ application.
    * The Save button when editing emails in the _Emails_ application is now disabled for users without _Update_ permissions.
    * Deleting collected form submissions in the _Forms_ application is now possible with _Update_ permissions.


### Fixed issues
  * API
    * Calling the `IPageDataContextRetriever.TryRetrieve` method resulted in an unhandled exception if the page data context could not be initialized, instead of returning a false value.
    * Conventional MVC mechanism such as `AuthorizeAttribute` now work with the admin UI [role-based access control model](/documentation/developers-and-admins/configuration/users/role-management) for routes registered behind `/admin`.
    * Exceptions caused by cancelling asynchronous operations via cancellation tokens – as a response to a cancelled client request or application shutdown, for example – were incorrectly logged to the event log as errors (e.g., as `System.OperationCancelledException`).
    * The API documentation for the `WithPageUrlPaths` DocumentQuery extension method was improved with additional remarks regarding the method usage. 
  * Admin UI
    * Drop-down menus now close during a click-away action that targets an iframe with either the Page Builder, Form Builder, or page preview window.
    * Fixed the vertical alignment of button labels in the Safari browser.
    * For certain elements the ‘element is now focused’ blue border indicator was partially obscured or was rendered incorrectly.
    * Implemented responsive drop-down menus that automatically adjust based on viewport and parent element width.
    * Opening a drop-down list in a component properties dialog sometimes caused the drop-down menu to overflow the dialog window.
    * The application menu now overlays menus and panels that open from the right side of the interface (e.g., selector dialogs in the _Pages_ application, configuration options in the _Forms_ application) on displays where smaller viewport width causes overlaps.
    * The confirmation dialog displayed when changing page templates now uses the same look and feel as other dialog windows in the admin UI.
    * The listing pages context menu, available via (…), was obscured by the listing container in special cases.
    * The primary action button was not by default focused in interactive dialogs. Now, the primary action is always performed on enter (save, delete, confirm, etc.). 
  * Content hub – Content items listed in the content hub are now by default ordered according to the _Last modified_ column.
  * Content items – It was incorrectly possible to delete linked content items from “locked” pages that were in the published or archived workflow state.
  * Form Builder – Fixed various text overflow issues that could occur when entering long words in form component properties.
  * General
    * Optimized the number of database queries required when checking [user permissions](/documentation/developers-and-admins/configuration/users/role-management) in the admin UI.
    * The Readme.txt file in the _Dancing Goat_ and _Boilerplate_ [project templates](/documentation/developers-and-admins/installation) contained an invalid link to the documentation. 
  * Headless API – Fixed issues caused by the tilda ‘ _~_ ’ character in asset filenames.
  * Modules – When creating fields for module classes via the [field editor](/documentation/developers-and-admins/customization/field-editor), the _Field name_ length wasn’t validated correctly by the UI, and very long values could result in an error.
  * Page Builder – If Cross-Site Request Forgery validation failed when submitting a form generated by the _Form_ Page Builder widget, a “Cannot read property of null” error was logged in the browser console in addition to the expected HTTP 400 error code.
  * Pages
    * Page URL slugs and other text longer than the width of the various page property dialogs (_Information_ , _URL_) now break into multiple lines instead of disappearing past the browser window when longer than the available viewport space.
    * The content tree in the Pages application now better indicates possible placement when dragging and dropping pages.
    * The design and appearance of certain elements in the content tree was updated to better match the admin UI look and feel.
    * When changing page URL slugs on the _URL_ tab in the _Pages_ application, entering slugs longer than the width of the dialog window caused the input to stretch past the browser window.
    * When drag and dropping pages, the dragged pages are now hidden from their original position in the content tree instead of showing both at the original position and on the mouse cursor, possibly confusing users.
    * When moving items with children via the content tree, the confirmation prompt for the move operation did not appear in certain cases.
    * When saving page URL slugs on the _URL_ tab in the _Pages_ application, the Save button now transitions to a disabled state to prevent multiple concurrent requests from being submitted. 
  * UI form components
    * It was not possible to view more than the first 150 files in a selected media library via the _Media file selector_ [UI form component](/documentation/developers-and-admins/customization/extend-the-administration-interface/ui-form-components/reference-admin-ui-form-components).
    * It was possible to clear a selection made using the _Object selector_ even if the field was marked as required.
    * Pages selected within the _Page selector_ form component cast a shadow incorrectly when dragged.
    * Selecting a month or year in the _Date and time input_ form component incorrectly saved the whole form.
    * The `MinimumDecimalValueValidationRule` and `MaximumDecimalValueValidationRule` [validation rule attributes](/documentation/developers-and-admins/customization/extend-the-administration-interface/ui-form-components/ui-form-component-validation-rules#assign-validation-rules-using-attributes) could not be used because the attribute constructor did not allow the _decimal_ type as a valid attribute parameter type. The attribute constructors now accept _double_ instead. Conversion to decimal is done by rounding to 15 significant digits using rounding to nearest (a limitation of the _double_ input type).
    * When the _Object selector_ was placed in a side panel, selecting any action inside the selector caused the side panel to collapse. 
  * Unix/Linux – Instances hosted in Linux environments could encounter exceptions when accessing resources from [Amazon S3](/documentation/developers-and-admins/api/files-api-and-cms-io/file-system-providers/amazon-s3). This would occur, for example, when accessing media library files stored in Amazon S3.
  * Xperience Portal
    * A JavaScript error was logged in the browser console when viewing the _hash string salt_ value (_Dashboard_ → _Project_ _info_ section) on displays with certain viewport widths.
    * A wrong type of error page was displayed for Not Found (HTTP 404) and other HTTP errors.
    * The _Outages_ application in _Xperience Portal_ only displayed project outage reports starting from the beginning of the month following the project deployment. For example, projects deployed 8/15/2022 could only view reports starting from 9/1/2022.
    * The deployments page may not have displayed correctly during an ongoing maintenance.
    * The format of the _Account created_ and _User added to project_ email notifications was not correct in case the first name of the user was unknown.
    * The project expiration date displayed on the _Dashboard_ in _Xperience Portal_ incorrectly included the 30 day grace period for license renewals. 


* * *
### Hotfix (January 19, 2023)
**version 23.0.9**
Fixed issues
  * Admin UI customization – Searching using the [listing template](/documentation/developers-and-admins/customization/extend-the-administration-interface/ui-pages/reference-ui-page-templates) resulted in an error if a column of the listed object type was named after an [SQL reserved keyword](https://learn.microsoft.com/en-us/sql/t-sql/language-elements/reserved-keywords-transact-sql) (e.g., _Key_). After applying the hotfix, the system escapes all column names in such queries, allowing search on listing templates to work as expected.


* * *
### Hotfix (January 12, 2023)
**version 23.0.8**
Updates and changes
  * Project templates – The initial permission configuration for the sample Digital Channel Manager [role](/documentation/developers-and-admins/configuration/users/role-management) was modified to reflect security best practices. This change only applies to the _Dancing Goat_ and _Boilerplate_ project templates installed from [Kentico.Xperience.Templates](https://www.nuget.org/packages/Kentico.Xperience.Templates) version 23.0.8 and newer. In existing installations, we strongly recommend making the following change to the _Digital Channel Manager_ role (if present in your project): remove the _View_ permission for the _Email queue_ application.


* * *
### Hotfix (January 5, 2023)
**version 23.0.7**
New features
  * Email customization – New `EmailMessage.Validate` extension method that validates whether properties (`From`, `Recipients`, `CcRecipients`, etc.) of the `CMS.EmailEngine.EmailMessage` object are set correctly. The method is intended primarily for use when implementing [custom email clients](/documentation/developers-and-admins/customization/email-customization).


Updates and changes
  * Permissions – Only users with the _Administrator_ role can now change assigned roles via the _Users_ → edit a user → _General_ tab.


Fixed issues
  * Event log – An error occurred when viewing the details of event log records without a description.
  * SendGrid integration – The SendGrid integration failed to send emails with email addresses specified using advanced formats such as `"display name" <user@host>`. After applying the hotfix, the integration supports all email address formats allowed by the [System.Net.mail.MailAddress](https://learn.microsoft.com/en-us/dotnet/api/system.net.mail.mailaddress) class.


* * *
### Hotfix (December 15, 2022)
**version 23.0.6**
Fixed issues
  * Rich text editor – Hotfix 22.3.1 introduced HTML sanitization of content in the [Rich text editor](/documentation/business-users/rich-text-editor). This sanitization can result in modified or broken HTML code, for example, when adding content via the editor’s _Code View_ option. After applying this hotfix, the sanitization additionally allows `ID` and `data-*` attributes, as well as `href` attributes containing _mailto_ links in `<a>` tags.


* * *
### Hotfix (December 8, 2022)
**version 23.0.5**
Fixed issues
  * CI/CD – The _Administrator_ system role could not be unassigned from users when restoring [Continuous Integration](/documentation/developers-and-admins/ci-cd/continuous-integration) or [Deployment](/documentation/developers-and-admins/ci-cd/continuous-deployment) data.


* * *
### Hotfix (December 1, 2022)
**version 23.0.4**
Fixed issues
  * Permissions – It was possible to modify existing forms via the [Form Builder interface](/documentation/business-users/digital-marketing/forms/create-and-edit-forms) (_Forms_ → edit a form → _Form Builder_ tab) without possessing the _Update_ [permission](/documentation/developers-and-admins/configuration/users/role-management) for the _Forms_ application. After applying the hotfix, the _Update_ permission is required when making modifications to all forms.


* * *
### Hotfix (November 24, 2022)
**version 23.0.3**
Fixed issues
  * Admin UI customization – API documentation was missing for the client API that enables developers to work with pages in in modal dialogs (`useTemplateDialog()`) , which was introduced in hotfix 23.0.1.
  * Permissions – When adding images from a [media library](/documentation/business-users/media-libraries) into the content of a _Rich text_ widget in the Page Builder, the _Insert image_ selection dialog didn’t work for users without the _Administrator_ [role](/documentation/developers-and-admins/configuration/users/role-management). After applying the hotfix, media files in the dialog can be viewed, selected and uploaded by users with a role that has sufficient permissions for the _Pages_ application.


* * *
### Hotfix (November 16, 2022)
**version 23.0.2**
Fixed issues
  * Field editor – Minor visual issues occurred within the [field editor](/documentation/developers-and-admins/customization/field-editor) user interface in rare cases.
  * Permissions
    * Certain action buttons in the _Pages_ and _Content hub_ applications remained active, even if the user’s [role](/documentation/developers-and-admins/configuration/users/role-management) did not have the required _Create_ or _Update_ permissions assigned for the given applications.
    * Delete buttons for files in the _Media libraries_ application remained active, even if the user’s role did not have the required _Manage media library_ permission assigned for the application. 


* * *
### Hotfix (November 10, 2022)
**version 23.0.1**
New features
  * Admin UI customization – The hotfix introduces a new `useTemplateDialog()` hook for the client customization API that enables developers to set the properties of pages displayed within modal dialogs via `UIPageLocation(PageLocationEnum.Dialog)`.


Fixed issues
  * Continuous Deployment – [Continuous Deployment](/documentation/developers-and-admins/ci-cd/continuous-deployment) didn’t include the data of binding object types when running the restore operation.
  * UI form components – If the _Object selector_ [UI form component](/documentation/developers-and-admins/customization/extend-the-administration-interface/ui-form-components/reference-admin-ui-form-components) was configured to select exactly one item, it was not possible to clear the selection once an object was selected.


* * *
## Refresh (November 7, 2022)
**version 23.0.0**
### New features
#### Users
  * Role-based access control for the Xperience administration – The refresh update introduces a permission model for the Xperience administration. The model handles only permissions for the user interface of the administration application – the visibility of applications, application elements, tabs, and pages. The added functionality consists of the following: 
    * New [Role management application](/documentation/developers-and-admins/configuration/users/role-management) that enables the management of user roles.
    * New [permission evaluation API](/documentation/developers-and-admins/customization/extend-the-administration-interface/ui-pages/ui-page-permission-checks) for UI pages and applications.
    * Support for defining custom permissions and assigning and evaluating them in both existing and custom applications.


#### Digital marketing
  * Email management – Editors can now personalize [emails](/documentation/business-users/digital-marketing/emails) created in the _Emails_ applications by adding dynamic text to the content. When sending emails to specific recipients, the system replaces dynamic text with information known about the given recipient (_First name_ , _Last name_ or _Email_).


#### Content management
  * Headless API – A [headless API](/documentation/developers-and-admins/development/content-retrieval/retrieve-headless-content) for retrieving content items was released as a preview feature. Developers can now retrieve content items from Xperience using HTTP requests and the JSON data format. Check the [related documentation](/documentation/developers-and-admins/development/content-retrieval/retrieve-headless-content) to see the limitations related to the preview status of the feature.
  * Media libraries – Developers can now set the encoding quality used when resizing [images retrieved from media libraries](/documentation/developers-and-admins/development/content-retrieval/retrieve-content-from-media-libraries) via a configurable options object. The configuration is provided as part of the implementation within the _Kentico.Xperience.ImageProcessing_ [NuGet package](/documentation/developers-and-admins/development/website-development-basics/configure-new-projects/xperience-by-kentico-nuget-packages).
  * Algolia integration – An external module that allows you to create [Algolia search indexes](https://www.algolia.com/products/search-and-discovery/hosted-search-api/) and index [content types with the ‘Page’ feature](/documentation/developers-and-admins/development/content-types) in the Xperience content tree using a code-first approach. The integration also enables you to provide a search interface on the live site using .NET API, JavaScript API, or the [InstantSearch.js](https://www.algolia.com/doc/guides/building-search-ui/what-is-instantsearch/js/) JavaScript library. For more information, see the [Algolia Search Integration](https://github.com/Kentico/xperience-by-kentico-algolia) GitHub repository.


#### Xperience Portal
  * Projects in [Xperience Portal](/documentation/developers-and-admins/saas/xperience-portal) can now undergo planned scheduled maintenance. Certain features are unavailable for the duration of the maintenance. The maintenance intervals are planned by Kentico.
  * Uptime statistics for Xperience Portal projects, Deployment API, and deployment regions alongside downtime incidents are now available at [status.xperience-portal.com](https://status.xperience-portal.com/).


### Updates and changes
  * Forms – [Emails](/documentation/business-users/digital-marketing/emails) that are assigned to the autoresponder of one or more forms can no longer be deleted.
  * The [Froala WYSIWYG editor](https://froala.com/wysiwyg-editor) that provides the [Rich text editor](/documentation/business-users/rich-text-editor) in Xperience was updated to version 4.0.15. See [Froala Editor 4.0.15](https://froala.com/blog/editor/new-releases/froala-editor-4-0-15-released-xss-vulnerability-resolved-and-more/) for details.
  * User interface – The _Properties → Metadata_ section in the UI of the _Pages_ application was renamed to _Information_.


### Breaking changes
  * During [application startup](/documentation/developers-and-admins/development/website-development-basics/configure-new-projects), the system no longer automatically adds services related to session state and cross-origin resource sharing (CORS) to the service collection. Specifically, the `IServiceCollection.AddKentico` call no longer includes the following: 
    * [AddDistributedMemoryCache](https://learn.microsoft.com/en-us/dotnet/api/microsoft.extensions.dependencyinjection.memorycacheservicecollectionextensions.adddistributedmemorycache)
    * [AddSession](https://learn.microsoft.com/en-us/dotnet/api/microsoft.extensions.dependencyinjection.sessionservicecollectionextensions.addsession) – if you wish to utilize session state in your code, you need to manually [Configure session state](https://learn.microsoft.com/en-us/aspnet/core/fundamentals/app-state#configure-session-state) (add the required services and middleware components).
    * [AddCors](https://learn.microsoft.com/en-us/aspnet/core/security/cors) – to avoid potential errors, remove the `UseCors` call from your application’s middleware components. If you wish to use CORS, you need to manually [Enable Cross-Origin Requests](https://learn.microsoft.com/en-us/aspnet/core/security/cors).
  * The following dependency of the _Kentico.Xperience.WebApp_ [package](/documentation/developers-and-admins/development/website-development-basics/configure-new-projects/xperience-by-kentico-nuget-packages) was updated: 
    * [HtmlSanitizer](https://www.nuget.org/packages/HtmlSanitizer) from version 7 . 1 . 542 to 8.0.601. The update includes several breaking changes: [#365](https://github.com/mganss/HtmlSanitizer/pull/365), [#370](https://github.com/mganss/HtmlSanitizer/pull/370)
  * The following dependency of the _Kentico.Xperience.Core_ package was updated: 
    * [Microsoft.Data.SqlClient](https://www.nuget.org/packages/Microsoft.Data.SqlClient/) from version 4.1.0 to 5.0.1. The update includes potential breaking changes: [Microsoft.Data.SqlClient 5.0](https://techcommunity.microsoft.com/t5/sql-server-blog/released-general-availability-of-microsoft-data-sqlclient-5-0/ba-p/3592710)


### Breaking changes – API
  * The following members were removed from the membership API. There is no provided alternative. 
    * Removed properties:
      * `RoleInfo.RoleIsDomain`
      * `UserRoleInfo.ValidTo`
    * Removed constants:
      * `RoleName.EVERYONE`
      * `RoleName.AUTHENTICATED`
      * `RoleName.NOTAUTHENTICATED`


### Breaking changes – Database
All changes in the database structure are automatically handled by Xperience during the update procedure. This information is provided to help you prepare in case you use custom code or scripts that rely on the original database structure.
  * The following database views were changed or removed: 
    * `View_CMS_UserRole_Joined` was removed.
    * `View_CMS_UserRole_MembershipRole_ValidOnly_Joined` was removed.
    * The `ValidTo` column was removed from `View_CMS_UserRoleMembershipRole`.


### Fixed issues
  * Admin UI – Several minor issues with alignment, font size, spacing and shadows were fixed in the administration interface.
  * Contact groups – Validation error messages in the [contact group](/documentation/business-users/digital-marketing/contact-groups) condition builder were duplicated when an invalid condition was submitted multiple times.
  * Emails – The _Preheader_ field in the _Properties_ of [emails](/documentation/business-users/digital-marketing/emails) was missing a tooltip.
  * Event log – The system’s [event logging API](/api/development/event-log) was not thread-safe, causing, e.g., `Parallel.ForEach` calls that logged information into the event log to incorrectly terminate with an exception.
  * Forms
    * Deleting a form with an enabled autoresponder didn’t remove the internal automation process used to send the autoresponder emails.
    * Fields with very long label text were displayed incorrectly in the Form Builder interface.
    * Horizontal scrollbars were displayed for fields in the Form Builder interface in certain cases on devices with a small display width.
    * If the form selected in a _Form_ widget was later deleted, the widget’s configuration dialog displayed errors.
    * Multiple clicks of a [form’s](/documentation/business-users/digital-marketing/forms) submit button in quick succession could cause the system to send multiple autoresponder emails. After the update, clicked submit buttons are disabled until the request is processed.
    * Multiple clicks of action buttons in the options panel of the form administration UI could trigger multiple requests. After the update, a loading button is displayed after a click until the request is processed.
    * The _Country_ and _State_ contact attributes were not available for mapping when configuring form fields. After the update, mapping to these attributes is supported for _Text_ and _Number_ fields. Text fields attempt to map country or state code name values, number fields work with country or state IDs.
    * The Form Builder interface displayed checkboxes with incorrect alignment in certain cases for _Checkboxes_ fields.
    * The autoresponder options in the _After form submission_ panel of the _Forms_ application were not disabled correctly if the form was modified so that it no longer contained a field mapped to the _Email_ contact attribute. The autoresponder options now need to be manually reconfigured if a properly mapped Email field is returned to the form. 
  * Media libraries
    * When [resizing images](/documentation/developers-and-admins/development/content-retrieval/retrieve-content-from-media-libraries) retrieved from media libraries, the image encoding quality was set to 100%, which could cause resized images to be larger in file size than the original. After the update, the default encoding quality is set to 80%.
    * [WebP images](https://developers.google.com/speed/webp) uploaded to media libraries were stored and served with the wrong MIME type, and were not displayed correctly on the website.
    * [Getting the URL of a media file](/documentation/developers-and-admins/development/content-retrieval/retrieve-content-from-media-libraries) using the `IMediaFileUrlRetriever.Retrieve` API always generated the file’s `DirectPath` URL, even when it was not required or used. When storing media files on Azure storage, this resulted in unnecessary requests to Azure. 
  * Page Builder – Page, Path, Media and Attachment selectors for the [Page Builder legacy compatibility mode](/documentation/developers-and-admins/upgrade-to-xperience-by-kentico/editing-components-in-xperience-by-kentico) did not preserve any order of the selected items. After applying the update, the items are stored in the order in which they were selected.
  * Pages
    * After attempting to save a conflicting [URL slug](/documentation/business-users/website-content/manage-page-urls) for a page in the Pages application, the resulting error message disappeared immediately.
    * Fixed minor design issues in the _Change template_ dialog in the _Pages_ application, and added a Friendly warning with additional information. 
  * UI form components
    * Entering a very long number into the _Number input_ [form component](/documentation/developers-and-admins/customization/extend-the-administration-interface/ui-form-components/reference-admin-ui-form-components) caused the value to be converted to scientific notation. After the update, only numbers between -2147483647 and 2147483647 can be entered.
    * Fields in the administration with a selector form component (e.g., _Dropdown selector_) did not save their value in special scenarios. The problem occurred if the field used a [visibility condition](/documentation/developers-and-admins/customization/extend-the-administration-interface/ui-form-components/ui-form-component-visibility-conditions), and also had an assigned [configurator](/documentation/developers-and-admins/customization/extend-the-administration-interface/ui-form-components/editing-components/configure-editing-component-state) that dynamically populated the selection options.
    * If the _Rich text editor_ had a custom [toolbar configuration](/documentation/developers-and-admins/configuration/rich-text-editor-configuration) with the `toolbarInline` option disabled, enabling the `toolbarSticky` option didn’t work. The toolbar didn’t remain displayed at the top of the editing area when scrolling down in the content. 
  * Xperience Portal – An error was logged to the browser console when selecting or clearing the checkbox for confirming DNS settings in the _Site domains_ or _SendGrid domains_ applications within Xperience Portal.


* * *
### Hotfix (October 20, 2022)
**version 22.3.2**
Fixed issues
  * API – Creating a new media library (`MediaLibraryInfo` object) in code without `HttpContext` access resulted in an error. For example, the problem could occur when [using the Xperience API](/documentation/developers-and-admins/api/use-the-xperience-by-kentico-api-externally) in a console application.
  * Code generators – The system’s [code generator](/documentation/developers-and-admins/api/generate-code-files-for-system-objects) created invalid code for objects (e.g., content types) with fields of the _Pages_ and _Media files_ data types.
  * Object types – An error occurred when using the object selector component with an [object type](/documentation/developers-and-admins/customization/object-types) that did not have the `displayNameColumn` configured in its [Type info properties](/documentation/developers-and-admins/customization/object-types/object-type-configuration). The error affected fields created in the [Field editor](/documentation/developers-and-admins/customization/field-editor) with the _Object code names_ data type and form component, as well as code-driven properties decorated by the _Object selector_ [UI form component](/documentation/developers-and-admins/customization/extend-the-administration-interface/ui-form-components/reference-admin-ui-form-components).


* * *
### Hotfix (October 13, 2022)
**version 22.3.1**
New features
  * Xperience Portal – The page displayed after new users finish setting up their [Xperience Portal](/documentation/developers-and-admins/saas/xperience-portal) account and password now contains a button redirecting to the portal’s sign-in screen.


Fixed issues – security
  * XSS – Administration input fields using the [Rich text editor](/documentation/business-users/rich-text-editor) component were vulnerable to reflected XSS attacks. The hotfix ensures proper sanitization.


Fixed issues
  * Admin UI – Improved input validation on the _Features_ tab in the [Content types](/documentation/developers-and-admins/development/content-types) application.
  * Admin UI customization – The [client customization framework](/documentation/developers-and-admins/customization/extend-the-administration-interface/admin-ui-customization-model-overview) didn’t correctly load files from JavaScript modules built on non-Windows operating systems.
  * Content hub – After updating the name of a content item in the [Content hub](/documentation/business-users/content-hub) application, the administration’s breadcrumbs and navigation menu didn’t reflect the new name.
  * Infrastructure – Export of the Xperience database to a backup file failed due to changes related to content items (introduced in Refresh 22.3.0). As a result, it was also not possible to deploy applications to the [SaaS environment](/documentation/developers-and-admins/deployment/deploy-to-the-saas-environment).


* * *
## Refresh (October 6, 2022)
**version 22.3.0**
### New features
  * Content hub – A new way of working with content was added. It is now possible to create [content types](/documentation/developers-and-admins/development/content-types) (formerly _page types_) with a field configured to select content items. These linked content items can then be [retrieved](/documentation/developers-and-admins/development/content-retrieval/retrieve-content-items) using the API. You can also manage existing content items in the [Content hub](/documentation/business-users/content-hub) application. 
    * Create [content types](/documentation/developers-and-admins/development/content-types) with field which allow editors to link content items.
    * Model content with [content items](/documentation/business-users/content-hub/content-items).
    * [Use the API](/documentation/developers-and-admins/development/content-retrieval/retrieve-content-items) to work with the linked content items in your code.


  * Email management – When creating [emails](/documentation/business-users/digital-marketing/emails) in the _Email templates_ and _Emails_ applications, editors can specify the following new options:
    * Plain text content – Improves deliverability of emails. Some recipients may prefer plain text emails, and certain email clients only accept plain text.
    * Email preheader – The brief text that recipients see in their inbox after the email sender information and the subject line.


  * Xperience Portal – SaaS deployment uptime monitoring is now available in Xperience Portal. See [Uptime monitoring](/documentation/developers-and-admins/saas/xperience-portal).


  * Administration UI development 
    * Properties within configuration dialogs of admin UI, [Page Builder](/documentation/developers-and-admins/development/builders/page-builder), and [Form Builder](/documentation/developers-and-admins/development/builders/form-builder) components now support [multiple visibility conditions](/documentation/developers-and-admins/customization/extend-the-administration-interface/ui-form-components/ui-form-component-visibility-conditions).
    * Added the option to dynamically configure the state of [editing components](/documentation/developers-and-admins/customization/extend-the-administration-interface/ui-form-components/editing-components) assigned to properties in admin UI, [Page Builder](/documentation/developers-and-admins/development/builders/page-builder), and [Form Builder](/documentation/developers-and-admins/development/builders/form-builder) configuration dialogs. The configuration can run on the initial load of the properties dialog or when the value of another field within the dialog changes. A typical use case are selector components (drop-down, radio buttons), whose selection changes based on the value of a different property in the dialog. See [Configure editing component state](/documentation/developers-and-admins/customization/extend-the-administration-interface/ui-form-components/editing-components/configure-editing-component-state).


  * Forms – The listing in the _Forms_ application contains a new column indicating whether the autoresponder is enabled for each form.


  * Contact groups – When viewing the contacts belonging to a [contact group](/documentation/business-users/digital-marketing/contact-groups) in the _Contact groups_ application, users can now select contacts to open the given profile in the _Contact management_ application.


  * The _Text area_ [UI form component](/documentation/developers-and-admins/customization/extend-the-administration-interface/ui-form-components/reference-admin-ui-form-components) for the administration provides new properties that allow configuration of the area’s minimum and maximum displayed height (number of rows).


### Updates and changes
  * Rich text editor – When registering [Rich text editor configurations](/documentation/developers-and-admins/configuration/rich-text-editor-configuration), a display name needs to be specified for the configuration.
  * [Field editor](/documentation/developers-and-admins/customization/field-editor) – Updated the names of certain field configuration options to better describe their purpose (_Tooltip text_ and _Text below the input_).


### Fixed issues
  * Admin UI
    * An accessibility warning was logged in the browser console when viewing administration pages containing the _Password_ [UI form component](/documentation/developers-and-admins/customization/extend-the-administration-interface/ui-form-components/reference-admin-ui-form-components) (e.g., on the sign-in or change password page).
    * Dialogs and side panels (e.g., the options panel in the _Forms_ application) incorrectly closed after the user performed certain actions outside of the dialog area, for example mouse wheel clicks or right-clicks. After applying the update, dialogs are only closed by primary interactions, i.e., left mouse button clicks.
    * Minor improvements of the administration interface were made, for example increased font size for certain text.
    * The _Icon selector_ form component was not disabled correctly, e.g., when viewing the editing form of a published or archived page.
    * The layout of the [Form Builder](/documentation/business-users/digital-marketing/forms/create-and-edit-forms) designer area was broken on devices with a small display width.
    * When creating or editing fields in the [Field editor](/documentation/developers-and-admins/customization/field-editor), multiple scrollbars appeared in certain cases.
    * When displaying selected pages in an administration UI form, the _Page selector_ [component](/documentation/developers-and-admins/customization/extend-the-administration-interface/ui-form-components/reference-admin-ui-form-components) incorrectly showed the published name of [pages](/documentation/business-users/website-content), even when a newer version (draft) of the page had a different name. 
  * Emails – Selecting the [email](/documentation/business-users/digital-marketing/emails) name in the administration UI’s breadcrumbs incorrectly opened the email preview instead of the content editing view.
  * Forms
    * The _After form submission_ panel in the _Forms_ application became broken and displayed an error if an email selected for the Autoresponder was later deleted.
    * The email selector in the _After form submission_ panel incorrectly remained enabled even if the form didn’t contain a field mapped to the _Email_ contact attribute. 
  * General
    * Running on a time zone with a large [UTC offset](https://en.wikipedia.org/wiki/UTC_offset) caused unhandled errors in certain scenarios. For example, such errors could occur when logging event log records or when executing unit tests.
    * The system had a dependency on the deprecated _Microsoft.jQuery.Unobtrusive.Ajax_ package. The Page and Form Builder scripts no longer use the _jquery.unobtrusive-ajax.js_ bundle, and the dependency was removed. The related `FormBuilderBundlesOptions.JQueryUnobtrusiveAjaxCustomBundleWebRootPath` property in the API is now obsolete.
    * [Domain aliases](/documentation/developers-and-admins/configuration/website-channel-management) of sites were not validated correctly and allowed duplicate domain name values for the same site.
    * [Field editor](/documentation/developers-and-admins/customization/field-editor) – Values entered into the _Default value_ of fields were not validated in certain cases, and validation messages were displayed incorrectly. 
  * Project templates – The sample site created by the Dancing Goat project template contained several broken links to non existing pages.
  * Routing
    * If the page specified in _Settings → URLs and SEO → Home page_ was deleted, the _Pages_ application didn’t work and an error occurred.
    * The routing engine was vulnerable to CRLF Injection when performing redirects due to improper encoding of the URL query string. 


* * *
### Hotfix (September 29, 2022)
**version 22.2.3**
Fixed issues
  * API – The `AbstractTableManager.TableExists` method returned `false` when the call terminated with an exception. After applying the hotfix, the method propagates the exception and correctly terminates.
  * Field editor – It was not possible the configure the _Default value_ and _Required_ status for [fields](/documentation/developers-and-admins/customization/field-editor) of the following data types: _Object code names, Object Guids, Pages, Media files_


* * *
### Hotfix (September 22, 2022)
**version 22.2.2**
Fixed issues
  * Emails – When viewing the details of emails via the  _Email detail_ dialog in the  _Email queue_ application, labels identifying individual email properties displayed unresolved resource strings instead of the corresponding property names.
  * Site domain aliases – It was not possible to register a domain name or alias starting with the  _www._ prefix. This made it impossible to generate absolute URLs with the  _www._ prefix to content managed by the system, as the URL generation API always prepended the URL with the site’s domain name, which resulted in URLs such as  _https://mydomain.com/landing_. After applying the hotfix, domain names starting with  _www._ are allowed. Moreover, the hotfix fixes an issue that allowed users to register multiple identical domain aliases for a single domain.


* * *
### Hotfix (September 15, 2022)
**version 22.2.1**
Updates and changes
  * The [Froala WYSIWYG editor](https://froala.com/wysiwyg-editor) that provides the [Rich text editor](/documentation/business-users/rich-text-editor) in Xperience was updated to version 4.0.14. See [Froala Editor 4.0.14](https://froala.com/blog/editor/new-releases/froala-wysiwyg-editor-v4-0-14) for details.


Fixed issues
  * Emails – The _Preview_ mode for [emails](/documentation/business-users/digital-marketing/emails) in the _Emails_ application was modified to be more resilient against cross-site scripting attacks.
  * Field editor – When defining new fields via the [field editor](/documentation/developers-and-admins/customization/field-editor) interface, the configuration of the field’s assigned [UI form component](/documentation/developers-and-admins/customization/extend-the-administration-interface/ui-form-components) was not persisted correctly in special cases. The problem occurred if the UI form component’s configuration options used components with UI page commands. Such options are disabled by default during initial field creation due to certain system limitations. However, the initial save of the field didn’t persist other configuration options that were available.


* * *
## Refresh (September 8, 2022)
**version 22.2.0**
### New features
**Object type management and customization**  
Introduced a new _Modules_ application with the following functionality:
  * Support for creating and registering [object types](/documentation/developers-and-admins/customization/object-types) into the system. Object types contain metadata that describe the properties and behavior of database entities integrated into and leveraging certain Xperience features.
  * Support for extending system object types.
  * Support for entering [macro expressions](/documentation/developers-and-admins/configuration/macro-expressions). Until now, macro expressions were used by the system in the background, but were not available to users. Currently, macros are usable when configuring the default values of object type fields via the _Modules_ application.


**New email management applications**  
New _Email templates_ and _Emails_ applications that allow users to prepare and edit the content of emails directly in the Xperience administration. See [Emails](/documentation/business-users/digital-marketing/emails) for more information. Currently, such emails can only be used with [form autoresponders](/documentation/business-users/digital-marketing/forms/create-and-edit-forms).
**Xperience Portal**  
Custom site domains and the SendGrid sender domain can now be assigned through Xperience Portal. See [Domain names for SaaS deployment](/documentation/developers-and-admins/configuration/website-channel-management#website-channels-in-the-saas-environment) and [SendGrid integration](/documentation/developers-and-admins/configuration/email-configuration#sendgrid-integration).
**Disqus integration**  
Xperience offers an external module that integrates with the [Disqus](https://disqus.com/) comment platform. The module contains a _Disqus comments_ widget that provides the option to add a comment section to any page on your website. Disqus also offers advanced moderation tools, analytics and monetization options. The module is distributed as a NuGet package. For more information and detailed instructions, see the [Xperience by Kentico Disqus Widget](https://github.com/Kentico/xperience-disqus/tree/xbk) GitHub repository.
**Code editor UI form component**  
New _Code editor_ [UI form component](/documentation/developers-and-admins/customization/extend-the-administration-interface/ui-form-components/reference-admin-ui-form-components) for the administration. Provides a text editing area suitable for code, with support for syntax highlighting and line numbers.
### Updates and changes
The original _Emails_ application in the Xperience administration was renamed to _Email queue_. The new _Emails_ application is now used to manage the content of emails. See [Emails](/documentation/business-users/digital-marketing/emails).
### Fixed issues
  * Admin UI
    * Certain inputs and selectors displayed incorrectly when they contained a very long text value.
    * Certain locations in the administration displayed unresolved resource string keys instead of the actual text (for example the descriptions of event log records related to page workflow status changes).
    * Error messages displayed when attempting to delete an object with existing dependencies did not accurately describe the cause of the problem. For example, such errors occurred when deleting a page type with existing pages in the content tree.
    * The explanation text for the Password component was not displayed correctly in some locations.
    * When navigating between pages in listings within the administration, the screen didn’t scroll to the top of the page content.
    * When working in a dialog within the Xperience administration, notifications and error messages were incorrectly displayed outside of the dialog in certain cases. 
  * Contact groups – The object selectors in [contact group](/documentation/business-users/digital-marketing/contact-groups) conditions were misaligned in certain cases.
  * Forms – The _General_ or _After form submission_ options panel in the [form editing interface](/documentation/business-users/digital-marketing/forms/create-and-edit-forms) was not hidden correctly after clicking into the Form Builder editing area.
  * General – After performing a [project update](/documentation/developers-and-admins/installation/update-xperience-by-kentico-projects), the system didn’t correctly detect differences in the minor and hotfix version number of the database and project packages. The application now fails to start and returns an error on startup when such a version difference is detected. You always need to update both project packages and the database when performing an update.
  * Installation – When running the `dotnet kentico-xperience-dbmanager` CLI command with the `--recreate-existing-database` parameter, the database configuration was not preserved in certain scenarios (for example for Azure SQL databases).
  * Page Builder – Fixed minor vulnerabilities in the dependencies of Page Builder scripts.
  * Page types
    * After deleting a field in the _Field editor_ within the _Page types_ application, the configuration of the deleted field was incorrectly displayed instead of the values of the next field, which the field editor automatically expands.
    * The label text of the save button in the _Field editor_ within the _Page types_ application was unified to “Save”. 
  * Pages
    * Deleting a page while editing another one prevented notifications about unsaved changes from being displayed.
    * Notifications about unsaved changes were not displayed correctly in the _Pages_ application when attempting to move the page or its parent. 
  * Rich text editor – When using the _Code View_ of the [Rich text editor](/documentation/business-users/rich-text-editor) to edit page content in the _Pages_ application, changes were lost after saving the page. After applying the refresh, the editor automatically switches to the default WYSIWYG view after clicking anywhere outside of the editor area, including the page’s _Save_ button, and changes are saved correctly.


* * *
### Hotfix (September 1, 2022)
**version 22.1.3**
Fixed issues
  * Licensing – A licensing error prevented access to the administration if the [cross-site tracking](/documentation/developers-and-admins/digital-marketing-setup/cross-site-tracking) feature was enabled for the application with `CrossSiteTrackingOptions` configured, and the license key was missing or expired.
  * Rich text editor – For applications running on the domain root (without an application path), URLs of images and links placed into [rich text editor](/documentation/business-users/rich-text-editor) content in the Page Builder interface became invalid after saving and publishing the page. Applying the hotfix does not fix existing broken URLs, but allows you to create correct links by re-saving and publishing the affected pages again.


* * *
### Hotfix (August 26, 2022)
**version 22.1.2**
Fixed issues
  * Licensing – The administration dashboard didn’t work correctly when the license key had expired. As a result, users could not enter a new valid license.


* * *
### Hotfix (August 19, 2022)
**version 22.1.1**
Fixed issues
  * Cross-site tracking – Calling the `kxt('pagevisit');` function in [cross-site tracking](/documentation/developers-and-admins/digital-marketing-setup/cross-site-tracking) scripts generated an error in the browser console if the function’s optional `onerror` callback was not handled.
  * Infrastructure – The _Kentico.Xperience.DbManager.dll_ library distributed as part of the  _Kentico.Xperience.DbManager_ NuGet package was missing a Microsoft Authenticode digital signature.
  * SaaS deployment – The _Export-DeploymentPackage_ PowerShell script (provided as part of cloud project templates) created a malformed _$StorageAssets_ directory within the resulting deployment package. The problem occurred for projects where an item in the directory had _Copy to Output Directory_ set to a different value than _Do not copy_.


To avoid the described issue for cloud projects created using an older version of the [Kentico.Xperience.Templates package](/documentation/developers-and-admins/development/website-development-basics/configure-new-projects/xperience-by-kentico-nuget-packages), update the package and recreate the project to obtain the newest version of the _Export-DeploymentPackage_ script.
* * *
## Refresh (August 12, 2022)
**version 22.1.0**
### New features
**Minimal APIs support**  
The system now supports application configuration using [minimal APIs](https://docs.microsoft.com/en-us/aspnet/core/fundamentals/minimal-apis) introduced in .NET 6.
  * [Project templates](/documentation/developers-and-admins/installation) from the _Kentico.Xperience.Templates_ NuGet package were updated – newly created projects leverage the minimal API configuration model by default.
  * The legacy configuration model with separate program entry (_Program.cs_) and startup files (_Startup.cs_ by default) remains fully supported, but its use is no longer recommended. All documentation and training materials now work with minimal APIs exclusively. To migrate your codebase to the new model, follow [Migrate to the new minimal hosting model in ASP.NET Core 6.0](https://docs.microsoft.com/en-us/aspnet/core/migration/50-to-60-samples) for framework code, and [Configure new projects](/documentation/developers-and-admins/development/website-development-basics/configure-new-projects) for Xperience-related code.


**Page templates**
  * Page templates can now be configured using [custom properties](/documentation/developers-and-admins/development/builders/page-builder/page-templates-for-page-builder/page-template-properties).
  * Pages created using page templates that contain Page Builder content (widgets, sections) can now be saved as _Preset templates_ and reused when creating other pages. Templates prepared by the developers (added via `RegisterPageTemplate`) are now referred to as _Default templates_. See [Page templates](/documentation/business-users/website-content/page-templates).
  * When changing the _Default_ page template of a page, users now have the option to transfer existing Page Builder content over from the current page, assuming editable areas in both the source and target template use matching identifiers. See the _Implement page templates section_ on [Page templates for Page Builder](/documentation/developers-and-admins/development/builders/page-builder/page-templates-for-page-builder) for developer documentation, and the _Change templates of existing pages_ section on [Page templates](/documentation/business-users/website-content/page-templates) for business documentation.


**Xperience Portal**  
The hash string salt value assigned to Xperience Portal projects is now visible in [Xperience Portal](/documentation/developers-and-admins/saas/xperience-portal#hash-string-salt-for-the-xperience-portal-project), under the  _Project info_ section of the project _Dashboard_. Previously, hash string salts were provided by Kentico alongside Xperience Portal projects using other channels.
**Administration interface**  
The Xperience administration now uses an appropriately-themed dialog window when notifying users about interactive events (e.g., notifications about unsaved changes), instead of each browser’s default notification system.
### Fixed issues
  * Admin UI customization – The `BarItemGroup` component ([@kentico/xperience-admin-components](https://www.npmjs.com/package/@kentico/xperience-admin-components)) generated the “Each child in a list should have a unique ‘key’ prop” warning in the browser console, for example when using the field editor in the _Page types_ application.
  * CI/CD
    * CI/CD commands returned a non-zero exit code in special cases even if the result was successful.
    * If the [Continuous Integration](/documentation/developers-and-admins/ci-cd/continuous-integration) or [Continuous Deployment](/documentation/developers-and-admins/ci-cd/continuous-deployment) command-line tools were run targeting a directory without a _repository.config_ file, the processes got stuck and could only be terminated using a hard exit (CtrlC). 
  * Contact groups – Attempting to close the contact group condition dialog with unsaved changes now displays a warning prompt.
  * Cross-site tracking – The _Website name_ column in the listing of tracked websites under _Cross-site tracking_ _→ Tracked websites_ incorrectly displayed the tracked site’s code name instead of its display name.
  * Former URLs – Moving a page via drag-and-drop using the content tree in the [Pages](/documentation/business-users/website-content) application incorrectly created a [former URL](/documentation/business-users/website-content/manage-page-urls) for the page even when the page’s URL was not affected by the move operation (e.g., a reorder within the same section of the tree).
  * Forms – The Form Builder interface could be displayed on different domains via an iframe (assuming certain conditions were met).
  * General – Minor visual issues that could in certain cases appear throughout the administration interface across various browsers (listings and search inputs overflowing on smaller resolutions, incorrect shadows on certain elements, minor layout issues on specific pages, etc.).
  * Licensing – License expiration notifications were displayed incorrectly in certain cases.
  * Pages
    * After a page was moved in the content tree in the _Pages_ application, the right-side workspace was not updated and could incorrectly display outdated information (e.g., a page’s URL still reflected the previous position).
    * After discarding changes to a page in the _Pages_ application, certain fields on the _Content_ tab were not reverted to their previous values (not displayed correctly from the last published or archived version of the page).
    * When editing a page’s URL slug via _Properties_ in the _Pages_ application, the _Publish change_ button could disappear in rare cases.
    * When editing a page’s URL slug via _Properties_ in the _Pages_ application, the caption of the save button now reflects the workflow state of the page – _Publish change_ for published pages, and _Save change_ for unpublished or archived pages. 
  * Project templates – When running the Dancing Goat project in Kestrel on Linux environments, accessing certain malformed images caused a complete shut down of the Kestrel hosting process, requiring a full application restart. The affected images were replaced. This change only applies to new projects created after updating the _Kentico.Xperience.Templates_ NuGet package to version 22.1.0 or newer.
  * UI form components
    * Text inside disabled Text area and Text input [UI form components](/documentation/developers-and-admins/customization/extend-the-administration-interface/ui-form-components/reference-admin-ui-form-components) was not visible when using the Safari browser.
    * The Form Builder interface could be displayed on different domains via an iframe (assuming certain conditions were met).
    * The URL selector UI form component could under certain circumstances lose focus unexpectedly when manually editing its value.
    * The clickable area of checkbox components in the administration was increased.
    * The object selector UI form component didn’t reflect the `Tooltip` and `InactiveMessage` properties.
    * The system incorrectly evaluated [UI form component visibility conditions](/documentation/developers-and-admins/customization/extend-the-administration-interface/ui-form-components/ui-form-component-visibility-conditions) that made use of transitive dependencies. In these cases, the system failed to correctly reflect the values of certain fields based on their (in)visibility when evaluating the condition, which could result in incorrect visibility states. For example, assume field dependencies A → B → C, which implies that field C also depends on field A. Setting A to a value that hides B must also hide C (due to transitivity), which was not the case. After applying the fix, complex visibility conditions that depend on hidden fields use either the hidden field’s default value (if set) or an empty value. 
  * Xperience Portal
    * In the [Xperience Portal](/documentation/developers-and-admins/saas/xperience-portal) _Deployments_ application, the _Deploy to_ drop-down for selecting the target environment was incorrectly enabled even where there was no existing deployment in the source environment.
    * The expiration date of the license key generated via the _License key generator_ application in [Xperience Portal](/documentation/developers-and-admins/saas/xperience-portal) can no longer be manually specified. License key expiration is now automatically managed by the portal – all generated keys are set to expire together with the validity of your Xperience subscription.
    * The link to the License Key Generator on the Xperience Portal Dashboard didn’t work.
    * Xperience Portal password reset emails did not contain a password reset link if the user’s email address included uppercase characters. 


### Database changes
The following database columns were removed. This was only a cleanup on the database level – the columns were no longer used by the system. 
  * _CMS_Class_ table – _ClassIsNavigationItem_
  * _CMS_Class table – ClassIsMenuItemType_
  * _CMS_Document table – DocumentShowInMenu_


* * *
### Hotfix (August 5, 2022)
**version 22.0.4**
New features
  * Cross-site tracking – Functions in cross-site tracking scripts now provide an optional `onerror` callback, which allows custom handling for scenarios where cookies are blocked, as well as other error states. See [Cross-site tracking](/documentation/developers-and-admins/digital-marketing-setup/cross-site-tracking).


Fixed issues
  * Cross-site tracking – Adding or revoking consent agreements using the `kxt('consentagree', ...);` and `kxt('consentrevoke', ...);` functions in [cross-site tracking](/documentation/developers-and-admins/digital-marketing-setup/cross-site-tracking) scripts incorrectly created an anonymous contact when the client’s browser blocked third-party cookies. In these cases, the contact was unnecessary and never contained any data, since tracking is not possible even if the visitor gives consent.


* * *
### Hotfix (July 29, 2022)
**version 22.0.3**
Fixed issues
  * Cross-site tracking 
    * Checking the consent status of the current contact using the `kxt('consentcontactstatus', ...);` call in [cross-site tracking](/documentation/developers-and-admins/digital-marketing-setup/cross-site-tracking) scripts incorrectly created a new anonymous contact in cases where the visitor had not given consent to be tracked.
    * When a visitor accepted tracking consent on an external website, and then arrived on the main Xperience site, the system failed to detect the consent and didn’t automatically set an appropriate cookie level for the main site. After applying the hotfix, the cookie level specified during application startup via `CrossSiteTrackingOptions` is set automatically for tracked visitors from external sites, and the cross-site contact is merged with the contact representing the visitor on the main site. 


* * *
### Hotfix (July 22, 2022)
**version 22.0.2**
Fixed issues
  * General – Minor fixed issues without direct customer impact (e.g., improved confirmation message text for the database update CLI command).


* * *
### Hotfix (July 15, 2022)
**version 22.0.1**
Fixed issues
  * Contact groups – The recalculation warning displayed after editing a [contact group’s condition](/documentation/business-users/digital-marketing/contact-groups) behaved incorrectly. In certain cases, clicking the button didn’t immediately display the “loading” status, and the warning remained visible even after recalculation was triggered and successfully finished.
  * Cross-site tracking – When using the default configuration, the [cross-site tracking](/documentation/developers-and-admins/digital-marketing-setup/cross-site-tracking) scripts attempted to reach a non-existing `Kentico.CrossSiteTracking/Logger/LogCrossSiteAnalytics` endpoint. This caused failed requests on the tracked site’s pages.
  * Forms – If validation failed for the _Email_ or _U.S. phone number_ fields when submitting a [form](/documentation/business-users/digital-marketing/forms), the validation error messages were displayed incorrectly (as unresolved resource string keys).
  * Licensing – The administration incorrectly displayed license expiration notifications when using an evaluation license. After applying the hotfix, expiration notifications only appear for full licenses.
  * Project templates – The _Privacy_ page on the Dancing Goat sample site (`kentico-xperience-sample-mvc` [project template](/documentation/developers-and-admins/installation)) displayed an error if the data protection demo was not enabled in the _Sample data generator_ application. The hotfix does not update existing sites, only new projects created based on the Dancing Goat template.


* * *
## Refresh (July 1, 2022)
**version 22.0.0**
The initial release of the Xperience by Kentico adopters program.
* * *
![]()
[]()[]()
