# Guidelines for stable customization
  * [ Copy page link ](documentation/developers-and-admins/customization/stable-customization-guidelines#) | [Get HelpService ID](documentation/developers-and-admins/customization/stable-customization-guidelines#)
Core MVC 5


[✖](documentation/developers-and-admins/customization/stable-customization-guidelines# "Close page link panel") [Copy to clipboard](documentation/developers-and-admins/customization/stable-customization-guidelines#)
This page contains guidelines for working with the [Xperience by Kentico API](documentation/developers-and-admins/api) and other parts of the application in customization or integration scenarios. The goal of these recommendations is to help developers create stable code and minimize problems when updating to newer versions.
## API usage
Xperience by Kentico is built with a strong focus on stability and long-term reliability. The platform’s [API](documentation/developers-and-admins/api) is designed to provide developers with a consistent and predictable experience, ensuring that customizations and integrations remain robust across updates. Xperience helps you build solutions with confidence, minimizing the risk of unexpected breaking changes.
Despite this commitment to long-term stability, the Xperience development lifecycle requires parts of the API to change over time. The impact of such changes is mitigated by compatibility policies and clear deprecation practices. Backward compatibility is maintained for at least 12 months after a feature is deprecated or an API member is marked as obsolete.
In most cases, the API provides a replacement for obsolete members, which is clearly described in the obsolete workaround message. The replacement will exist side-by-side with the obsolete API member for the duration of the compatibility period. This will give you time to make any necessary changes.
**API compatibility exceptions**
There may be rare exceptions of breaking changes that occur either without prior notice or within the 12-month compatibility period. For example, if Kentico needs to address a security vulnerability, or make unavoidable architectural changes required for the product’s future evolution. However, Kentico will always provide guidance or automated solutions to keep updates as smooth as possible.
### API not intended for public use
The following Xperience API is technically publicly available, but is not intended for public use. Such API members may be changed or removed without prior notice, and without providing a replacement.
  * **Obsolete API** – API marked as [obsolete](https://learn.microsoft.com/en-us/dotnet/api/system.obsoleteattribute). In most cases has a preferred alternative, and will most likely be removed in the future, causing your code to break.
  * **Internal namespaces** – API contained in namespaces with the `.Internal` suffix. Such API should be considered as unstable, and may be subject to breaking changes during any update.
  * **API documented as internal** – in rare cases, an API member needs to be public, but is only intended for use by the framework infrastructure and not intended for public use. Such members are explicitly documented in their `<remarks>` or `<summary>` as internal. For example:
C#
**Internal API remark**
Copy
```
/// <remarks>
/// This API supports the framework infrastructure and is not intended to be used directly from public code.
/// </remarks>
```

  * **Constructors of infrastructure classes** – certain publicly accessible classes are designed to be invoked by the framework infrastructure, and are not intended to be directly instantiated in user code. The constructors of such classes may be subject to breaking changes during any update. The following are examples of infrastructure classes:
    * [UI page classes](documentation/developers-and-admins/customization/extend-the-administration-interface/ui-pages) and [UI form component classes](documentation/developers-and-admins/customization/extend-the-administration-interface/ui-form-components)
      * You can interact with admin UI classes via the appropriate API, such as [UI page extenders](documentation/developers-and-admins/customization/extend-the-administration-interface/ui-pages/ui-page-extenders) and [form component attributes](documentation/developers-and-admins/customization/extend-the-administration-interface/ui-form-components/editing-components).
    * Controllers handling various system endpoints (with the `Controller` suffix)


### Experimental API
In some cases, new features in Xperience are released in **preview mode**. Such features may be incomplete, have known limitations, and are subject to changes. Preview mode is primarily intended to allow developers and other users to familiarize themselves with the feature and give feedback.
API related to preview mode features is typically [marked as experimental](https://learn.microsoft.com/en-us/dotnet/csharp/language-reference/attributes/general#experimental-attributes) and usage will result in warnings when compiling your project. The warnings are treated as errors for reporting purposes. To use the code, you need to [suppress](https://learn.microsoft.com/en-us/dotnet/fundamentals/syslib-diagnostics/experimental-overview#suppress-warnings) the warnings.
Experimental API is not intended for use in production code. By suppressing the warnings and using the API, you acknowledge the fact that the API may change during any update, until the feature is fully released and the experimental attributes are removed.
### System user context
Xperience contains an internal system [user](documentation/developers-and-admins/configuration/users/user-management) with the **kentico-system-service** user name. This user account serves as the “author” of changes performed by automated tasks in the system, such as the restoration of [content sync](documentation/developers-and-admins/configuration/content-sync-configuration) operations.
You can use this account in custom code that requires user context, but is not actually performed by a person interacting with the UI. For example, a suitable scenario is management of [content items](api/content-management/content-items) or [pages](api/content-management/pages) via the `IContentItemManager` or `IWebPageManager` classes in a custom [scheduled tasks](documentation/developers-and-admins/customization/scheduled-tasks).
## Admin UI
The Xperience administration interface is designed to be highly [extensible and customizable](documentation/developers-and-admins/customization/extend-the-administration-interface). However, the following aspects of the admin UI are **not guaranteed to be stable** and may change without prior notice during any update:
  * **URLs** – The URLs of applications and individual pages in the admin UI are not changed frequently, but we strongly recommend that you avoid creating dependencies on the exact URL structure. To [generate stable links or references](documentation/developers-and-admins/customization/extend-the-administration-interface/ui-pages#generate-urls-to-other-administration-pages) to UI pages, use the `IPageLinkGenerator` service.
  * **HTML output** – The design, rendered HTML code, and logic of admin UI pages may be updated to accommodate design improvements and functional changes. Such modifications can impact custom implementations of [admin UI tests](documentation/developers-and-admins/customization/extend-the-administration-interface/administration-interface-ui-tests) that depend on underlying components of the system, such as the `data-testid` attribute. Avoid coupling your code to the exact HTML output of the admin UI unless absolutely necessary.


## Database
Kentico aims to keep the Xperience by Kentico database schema as stable as possible. Tables are often added or expanded with new columns during updates, but breaking changes, such as table and column renaming or removal, only occur in rare cases. Such database changes are planned as part of major version updates and will be clearly communicated in the product’s [Changelog](documentation/changelog).
### Data access and management
Avoid direct access to the Xperience database structures (custom SQL scripts, procedures, etc.). The [API](documentation/developers-and-admins/api) is the primary way to work with data in Xperience.
You may encounter advanced scenarios where direct database access is required, typically for performance optimization of operations that handle very large amounts of data. In these cases, use caution and provide test coverage for your functionality to ensure that your custom queries remain compatible with any database schema or data changes.
### Extend the default database tables
**Never modify Xperience tables manually** in the database – adding or removing columns, changing column data types, etc.
If you wish to add a custom column, you can do so in the administration UI for customizable data classes. See [Extend system object types](documentation/developers-and-admins/customization/object-types/extend-system-object-types) for more information and detailed instructions.
**Unique names for custom fields**
When adding custom fields to [customizable classes](documentation/developers-and-admins/customization/object-types/extend-system-object-types) or their UI forms, include a unique prefix in the **Field name** (for example your company name). The prefix ensures uniqueness for the field name, and prevents conflicts with system fields that may be added in the future.
### Add custom database tables
If you need to store data in a custom table within the Xperience database, use the administration UI to create one of the following objects:
  * [Content type](documentation/developers-and-admins/development/content-types) – for content, files, and other data that you wish to deliver through your application’s channels (websites, emails, etc.).
  * [Data class under a custom module](documentation/developers-and-admins/customization/object-types) – a powerful option for fully integrating custom data into Xperience. Requires more extensive custom development.
  * [Form](documentation/business-users/digital-marketing/forms/create-and-edit-forms) – suitable for data that you wish to collect from website visitors.


**Content modeling**
See the [Content modeling guide](modules/content-modeling-guide) for in-depth information and recommendations on structuring content in Xperience.
In all cases, the system automatically creates a database table based on the options that you configure. Any fields (columns) that you define for the table remain unchanged during updates. In rare cases, an update may add new general fields required by the system. Such additions should not break or affect the table’s functionality.
When creating objects representing custom database tables, always use a unique prefix (namespace) in the code name of the object and the matching table name. This convention prevents conflicts with system objects and tables that may be added in the future. Never use the following code name prefixes for custom objects or database tables: **aira** , **cd** , **ci** , **cj** , **cms** , **commerce** , **emaillibrary** , **form** , **om**
### Database table indexes
Customization of [database table indexes](https://learn.microsoft.com/en-us/sql/relational-databases/indexes/indexes) is supported, but may require additional maintenance during updates:
  * You can drop the default indexes and create custom indexes for tables. Xperience does not provide index management features, and such changes need to be performed manually (via scripts or Database management systems, such as SQL Server Management Studio).
  * Use a **unique prefix** or other naming convention for your custom indexes and maintain documentation to keep track of the changes.
  * Xperience updates may fail if the system attempts to create an index that is not compatible with your custom indexes. For example, if you drop a table’s default clustered index and create your own, the update may fail if it attempts to update or recreate the default clustered index.
  * After applying an update, review each table with custom indexes and make sure the indexes are still relevant and fulfill your requirements.


### Other database objects
Other than database tables, you can also add your own objects, such as stored procedures, views, functions, etc.
  * Do not directly modify the default Xperience database objects. Updates can overwrite any database object and the customizations will be lost.
  * Always use a **unique prefix** or other naming convention for custom database objects. The prefix prevents conflicts with system objects that may be added in the future.
  * If you need to use a modified version of an Xperience database object, create your own custom object and copy the code from the original (use a unique naming convention). Then make any required modifications and ensure that your custom functionality uses the changed version of the database object.


## Default data - UI forms
All installations of Xperience automatically include a set of default objects, and some of these objects are critical components of the system. In most cases, such default objects are protected and cannot be deleted or modified in the administration UI.
[UI forms](documentation/developers-and-admins/customization/object-types/extend-system-object-types#Extendsystemobjecttypes-UIForms) of [customizable data classes](documentation/developers-and-admins/customization/object-types/extend-system-object-types#customizable-data-classes) are one exception of critical default objects which you can interact with.
  * Whenever possible, avoid using the default UI forms in your custom admin UI. Instead, create your own UI forms under the given customizable class and add any required fields. You can replace the UI form on an existing UI page using a [UI page extender](documentation/developers-and-admins/customization/extend-the-administration-interface/ui-pages/ui-page-extenders).
  * You can safely add your own custom fields to the default UI forms.
  * The default UI forms and their fields are protected against deletion and non-compatible changes (_Field name_ , _Data type_ , etc.).
  * Any changes to the optional settings (_Default value_ , _Field appearance_ settings, etc.) of one of the default UI form fields may be **overwritten during any update**. Carefully consider if such changes are truly necessary, and if so, maintain documentation of the changes so that you can easily recover if a custom change is lost during an update.


## Custom templating expressions
Templating can be a powerful tool that allows users to create dynamic content by combining static text or templates with variable data. Expressions or placeholders used by a templating feature are typically distinguished from regular text using special syntax delimiters.
If you need to introduce a custom templating language or expression syntax into your Xperience implementation, **avoid using the following delimiter patterns** , which are reserved by native functionality in Xperience:
  * `{%...%}` – used for [macro syntax](documentation/developers-and-admins/configuration/macro-expressions/macro-syntax)
  * `{$...$}` – used for [admin UI localization](documentation/developers-and-admins/customization/admin-ui-localization) functionality
  * `{{...}}` – used for placeholders in [notifications](documentation/developers-and-admins/configuration/notifications) and [dynamic text in emails](documentation/business-users/digital-marketing/emails#personalize-emails-with-dynamic-text)


Using these reserved delimiters in your custom templating implementation can lead to parsing conflicts, unexpected behavior, or interference with existing Xperience functionality.
**Future compatibility considerations**
Xperience by Kentico may expand its templating and macro functionality to additional locations in future updates. Areas that currently do not process these expressions may gain such capabilities, potentially affecting custom templating implementations that use conflicting delimiter syntax.
To avoid the issues described above, we recommend using a delimiter pattern that is unique and clearly identifiable as part of your custom implementation. For example, `@acme{...}` or `@custom[...]`.