# Upgrade widgets from Kentico Xperience 13
  * Concept| [ Copy page link ](guides/development/upgrade-deep-dives/upgrade-widgets-introduction#) | [Get HelpService ID](guides/development/upgrade-deep-dives/upgrade-widgets-introduction#)
Core MVC 5


[✖](guides/development/upgrade-deep-dives/upgrade-widgets-introduction# "Close page link panel") [Copy to clipboard](guides/development/upgrade-deep-dives/upgrade-widgets-introduction#)
When you upgrade from Kentico Xperience 13 (KX13) to Xperience by Kentico (XbyK), migrating widgets is one of the key elements you need to evaluate. The [Kentico Migration Tool](https://github.com/Kentico/xperience-by-kentico-kentico-migration-tool) helps you with [migrating the data](guides/architecture/upgrade-from-kx13/prep-for-upgrade-and-transfer-data#migrate-your-data). After transferring the data, you’ll still need to [update the widget code files](guides/architecture/upgrade-from-kx13/adjust-your-code-and-adapt#adjust-page-builder-components).
Both platforms are built on .NET, but the underlying architecture and APIs in XbyK have evolved to take advantage of modern .NET and .NET 8 features. How you migrate your widgets depends on your existing setup and what you want to achieve in the new environment.
If your KX13 site is already built on .NET Core, the process is more straightforward because XbyK runs natively on .NET 8. Migrating from an MVC-based project is more complex, since you need to adapt MVC artifacts to their .NET Core equivalents.
his material provides a conceptual overview of migrating widgets and explains the available options, including the requirements and limitations of each approach. It then covers how you can use the upgrade process to modernize your widgets and improve the editing experience in the new XbyK application.
## Understand data in widgets
[Page Builder widgets](documentation/developers-and-admins/development/builders/page-builder) are reusable components that let content editors manage content and layout without writing code.
Each widget stores its content and settings through widget properties, which define what data the widget holds and how it’s used. This data can come from multiple sources: content stored directly in the widget, references to items in the content tree or in custom tables, or even data provided through APIs from third-party systems. This flexibility allows editors to build and adjust pages quickly. However, it also means the structure of stored data varies from widget to widget.
Widget implementation differs between two KX13 applications: the **Page Builder** within [Pages](13/managing-website-content/working-with-pages) and the [Email Builder](13/on-line-marketing-features/managing-your-on-line-marketing-features/email-marketing/composing-marketing-emails).
While XbyK’s [Page Builder](guides/development/page-builder) leverages a similar technology stack as KX13’s and it is possible to migrate KX13 Page Builder content to XbyK, the XbyK [Email Builder](guides/development/email-marketing/email-builder-overview) employs a different architecture and widget development model.
[XbyK email widgets](guides/development/email-marketing/create-an-email-widget-for-articles) are implemented as custom _Razor components_ that allow developers to use MJML markup or standard HTML for email design. The migration tool does not migrate KX13 emails or their content, nor does it support migrating form autoresponders from KX13. You will also need to **rebuild the Email Builder widgets from scratch** within XbyK.
Let’s focus on how you can upgrade Page Builder content to XbyK.
Behind the scenes, Page Builder data, including widget properties, is stored in the [database fields](13/custom-development/working-with-pages-in-the-api/page-database-structure) as JSON objects (_CMS_Document_ table in KX13, and _CMS_ContentItemCommonData_ in XbyK). Widgets that reference other content store only the link (GUID) to that item, while widgets that hold their own content save the complete data in a serialized format. It’s common for widgets to combine these approaches, for example, storing a GUID for referenced content and in-UI adjustments, such as button labels in personalization, hyperlinks, color options, or deep-link anchors.
The data in widget properties can rely on built-in KX13 UI form components, such as text inputs, checkboxes, or page or media file [selectors](13/developing-websites/page-builder-development/selectors-for-page-builder-components). However, many projects also include custom properties with custom editing components to meet unique requirements. Understanding both the data structure and the components used to manage it is essential when migrating or refactoring widgets, as it affects how you can migrate or adapt content during an upgrade to XbyK.
## Explore the options for migrating widgets
Depending on your project’s complexity, available resources, and constraints, you can migrate widgets in three different ways.
The _Kentico Migration Tool_ supports multiple migration paths for widgets. You can migrate widgets in _legacy mode_ or _map_ KX13 _widget properties_ to new [XbyK UI controls](documentation/developers-and-admins/customization/extend-the-administration-interface/ui-form-components/reference-admin-ui-form-components). You can also use the upgrade as an opportunity to update your content model, which requires _remodeling and adjusting your widgets_.
### “Lift-and-shift” migration
You can choose to migrate your widgets in **legacy mode**. This is a straightforward migration of your widgets from KX13 to Xperience by Kentico, keeping the data, widget properties, and their configuration intact, i.e., as they are in KX13.
#### When to migrate in legacy mode
Transfer widgets and their content from KX13 to XbyK when:
  * You need to bring the site online in XbyK as fast as possible.
  * You plan to modernize the widgets incrementally soon after the upgrade.
  * You have mostly presentation widgets that contain the data (and design).


#### Legacy mode limitations
  * Widgets remain in a legacy state until you update them. We plan to discontinue the legacy mode in XbyK.
  * Editors won’t be able to use XbyK features, such as Content hub, in legacy widgets.
  * Editors will need to manually update references to pages after migration.


### Map widget properties to new XbyK controls
[Source instance API discovery](guides/architecture/upgrade-from-kx13/upgrade-walkthrough/migrate-data-and-binary-files#configuration) inspects your source instance during migration and maps built-in KX13 widget properties to the new platform. This approach produces native-like XbyK widgets and unifies the editing experience, especially if your XbyK project contains both migrated and newly built widgets. However, the underlying content model will remain the same, so editors will still not be able to fully take advantage of modern editing capabilities.
#### When to use source instance API discovery
Source instance API discovery automatically maps KX13 widgets to XbyK during migration to unify the editing experience. Use it when:
  * Your widgets use only built-in editing components.
  * You want a clean migration that aligns with XbyK best practices.
  * You want to minimize post-migration cleanup of widgets.


#### Source instance API considerations
  * Since the migration tool doesn’t modify code files, you cannot automatically migrate custom widget properties (i.e., custom UI controls built for the KX13 project) into the XbyK and you need to update these manually to make use of the new UI components.
  * Only built-in editing components are supported.


### Adjusting widgets during migration
For many projects, upgrading to XbyK is also a chance to modernize widgets or restructure the content model instead of just moving the data as-is. These adjustments can range from simple property cleanups to significant redesigns.
#### When to adjust widgets
XbyK introduces new patterns for working with content that makes it reusable across different marketing channels. Adjust widgets during migration when:
  * You want to align your widgets with a new content model, such as using Content hub for reusable content.
  * Your widgets contain legacy patterns that no longer fit your architecture.
  * You want to simplify maintenance or improve the editor experience.


Typical widget modifications to consider during migration include:
  * Moving content references from the content tree to the Content hub.
  * Converting inline widget content into reusable content items.
  * Updating widget properties to use new APIs or structured data.


## Migrating built-in widgets
Built-in [system widgets](13/developing-websites/page-builder-development/reference-system-widgets), i.e., the **Form** and **Rich text** widgets, migrate with minimal effort. They can be good test cases to validate your migration process before tackling custom widgets.
## Choosing the right approach to widget migration
For most projects, at least API discovery is the recommended path. It migrates widget properties in native XbyK UI and makes the experience fresh from day one. **Combining API discovery with targeted adjustments can deliver the cleanest, most future-proof result for teams looking to modernize.**
However, if you need a quick migration or your widgets include complex customizations, you can start in legacy mode and plan an incremental refactoring process.
## Customize widget migrations
Not every widget has to or can be migrated to XbyK as-is from KX13. In many projects, you might want to customize the migration process to address adjustments to new features or handle unique widget types, non-standard properties, or data that doesn’t [map cleanly to XbyK](guides/architecture/upgrade-from-kx13/plan-your-strategy-for-migrating-features).
The migration tool provides two key extension points for this:
  * **Custom widget migrations** give you control over how entire widgets are migrated.
  * **Custom widget property migrations** handle individual widget properties.


These customizations let you refine how data moves into XbyK, whether you want to align migrated widgets with a new content model, restructure widget data to improve editing experience, or address compatibility issues.
### Custom widget migrations
A custom widget migration tells the migration tool to handle a specific widget type. You configure how the migration tool should handle the widget content and its properties during migration, and specify the new structure, including the new properties of the migrated widget. This is useful when:
  * A KX13 widget (or its properties) doesn’t have a direct equivalent in XbyK.
  * You want to map a widget to a newer, more structured design.
  * You want to change the way data is stored in the migrated widget.


By customizing the migration, you can maintain a similar editing behavior and make the underlying widget code and data structure more maintainable.
#### Example: Upgrading a Banner widget
Imagine your KX13 site uses a custom Banner widget that stores text, images, and links directly in the widget data. In XbyK, you’ve created a cleaner, structured Banner content type in the Content hub. With a custom widget migration, you can:
  * Move the text and images into new content items in the Content hub.
  * Update the widget to store only references to those items.
  * Preserve the same look and feel on the live site while improving content reusability.


#### Custom widget property migrations
Sometimes, you don’t need to change the whole widget, but you need to address the handling of one or two properties. For example, a property that stores a page path in KX13 might need to store a content item reference in XbyK.
Property-level migrations let you handle these cases without rewriting the whole widget. They can help clean up or standardize property values, so it is easier to maintain and extend widgets in XbyK. You’ll still need to define the new widget properties, but you’ll not need to deal, for example, with a complex remodeling of the migrated widget.
#### Example: Updating a page selector property
Suppose your widget includes a property that lets editors pick a page from the content tree. In KX13, the widget stores the page’s path. In XbyK, widgets store a content item’s GUID instead. You can automatically translate the old path to the new GUID reference during data migration, so editors don’t have to fix the data manually after the upgrade.
#### Example: Cleaning up unused settings
KX13 projects can be as old as five years. It is common that some widgets have properties that aren’t relevant anymore, like an old toggle for a feature that’s no longer supported. With a custom property migration, you can remove or ignore that data, which makes the migrated widget simpler and easier to maintain.
### Migrating pages as widgets
Sometimes, content that exists as pages in KX13, but editors realized that it would work better as widgets in XbyK. The migration tool supports this scenario, letting you convert pages into widget instances during migration.
This approach is useful when:
  * A page mainly acts as a content container, such as a landing page with static text and images stored in nested child pages in the KX13 content tree.
  * You want to provide a flexible editing experience by moving static presentation pages into Page Builder pages with templates and widgets.
  * You want to display the content in Page Builder with reusable content rather than maintain separate page types in the content tree.


When you migrate pages as widgets, the tool can map page fields into widget properties and even handle child pages by converting their content into linked content items in the Content hub. This enables editors to compose their pages in a user-friendly UI and introduces new digital marketing options, such as [content personalization](documentation/developers-and-admins/digital-marketing-setup/content-personalization). It also fits into XbyK’s cleaner, more modern editing philosophy.
## Additional considerations for widget migration
Beyond the basic migration paths and customizations, there are several areas you’ll need to review to make sure your migrated widgets work correctly in XbyK.
### Widget content personalization
If your widgets display personalized content in KX13, you can continue personalizing them in XbyK. The platform supports personalization against contact groups, so migrated widgets can keep showing different content variations to various audiences. Consult the documentation to see if and how you need to adjust the code files. docs.kentico.com/documentation/developers-and-admins/digital-marketing-setup/content-personalization/develop-personalization-condition-types
### Business logic and data retrieval
All KX13 widgets rely on custom business logic to fetch and display data. Because APIs and data access patterns in XbyK are different, you’ll need to review and update this logic during migration. For example:
  * Replace DocumentQuery calls with the Content Retriever API.
  * Update data mappings to work with content items in the Content hub.


### Adjusting Page Builder components
In combination with the Content hub, XbyK’s Page Builder brings a more flexible content management experience than KX13. However, there are some differences to be aware of:
  * Custom or customized [editing components](documentation/developers-and-admins/upgrade-to-xperience-by-kentico/editing-components-in-xperience-by-kentico) based on KX13 frameworks need to be rewritten to fit the [technology stack](documentation/developers-and-admins/upgrade-to-xperience-by-kentico#technical-comparison).
  * Customizing the page structure and widget behavior might require adjusting zone and other restrictions you’ve had in the KX13 project to maintain consistent behavior.
  * Personalization conditions in KX13 that used controllers to render widget properties need to be updated in XbyK to work with the widget’s property model.


Test your migrated widgets in real Page Builder scenarios to catch these differences early.
### Handling migrated assets
Widgets that display images, files, or other media in KX13 need review. During the migration process, [attachments](https://github.com/Kentico/xperience-by-kentico-kentico-migration-tool/blob/master/Migration.Tool.CLI/README.md#attachments) and [media files](https://github.com/Kentico/xperience-by-kentico-kentico-migration-tool/blob/master/Migration.Tool.CLI/README.md#media-libraries) are converted to [content item assets](documentation/business-users/content-hub/content-item-assets). Ensure your widgets are updated to reference these new asset items rather than the legacy file paths.
## Summary and recommended practices
Migrating widgets from Kentico Xperience 13 to Xperience by Kentico can range from a simple lift-and-shift to a full redesign of their functionality. Investigate the complexity of the KX13 project and stakeholder requirements to determine whether to move the widgets just to upgrade, or invest in modernizing your solution.
Start investigating migration options with API discovery to get you the cleanest foundation. It creates native widgets in XbyK and lets your team immediately take advantage of the new UI. From there, you can decide which widgets need refining over time, which parts of the codebase need to be modernized, and where you can improve the editor experience with adjustments.
  * Plan ahead – Review your current widgets, their data sources, and any custom properties before migrating.
  * Investigate Kentico Migration Tool for relevant features for widget migration.
  * Start with built-in widgets – Test the process using widgets like the Form or Rich text widget to validate your setup.
  * Document customizations – Track any custom widget or property migrations so your team understands how the data maps in XbyK.
  * Test early and often – Deploy the migrated project to a test environment and invite stakeholders to validate migrated widgets.
  * Modernize gradually – Even if you migrate in legacy mode first, plan an incremental refactoring process to take advantage of native XbyK features.


Migrating Page Builder content with widgets, page templates, and other components, and adjusting these components to leverage new XbyK features makes a big difference for editors. With careful planning and testing, you can make the process smooth and prepare your solution to take full advantage of XbyK’s modern platform.
If you have any questions or upgrade scenarios you’d like us to cover in the future, please let us know with the **Send us feedback** button at the bottom of this page.