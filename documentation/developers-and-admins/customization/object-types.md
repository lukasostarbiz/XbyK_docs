# Object types
  * [ Copy page link ](documentation/developers-and-admins/customization/object-types#) | [Get HelpService ID](documentation/developers-and-admins/customization/object-types#)
Core MVC 5


[✖](documentation/developers-and-admins/customization/object-types# "Close page link panel") [Copy to clipboard](documentation/developers-and-admins/customization/object-types#)
Xperience relies on various entities persisted in the database for its functionality – users represented by `UserInfo` objects stored in the `CMS_User` table, digital marketing contacts represented by `ContactInfo` objects stored in the `OM_Contact` table, to name a few. The properties and behavior of such entities within the system is managed via **object type** **registrations**. An object type stores metadata that control how the system works with a given entity.
Entities registered as object types can benefit from:
  * Object-relational mapping (ORM) via [entity-provider classes](documentation/developers-and-admins/api/database-table-api) with [fluent API](documentation/developers-and-admins/api/objectquery-api)
  * Configurable referential and parent-child relationships between objects with ensured referential integrity
  * Configurable handling of object caching and cache dependencies
  * [Continuous Integration](documentation/developers-and-admins/ci-cd/continuous-integration) and [Continuous Deployment](documentation/developers-and-admins/ci-cd/continuous-deployment)
  * Automation and support for other actions and behavior according to the object type’s configuration


The system enables you to model custom entities that leverage the aforementioned features. The process consists of the following steps:
  1. [Define a data class](documentation/developers-and-admins/customization/object-types#define-the-data-class) used by the ORM framework to map the database entity to an object.
  2. [Generate code files](documentation/developers-and-admins/customization/object-types#generate-code-files) for the data class. The process generates an [_Info_ class](documentation/developers-and-admins/api/database-table-api) that mirrors the database entity (used by the ORM framework) and contains the **object type registration**.
  3. [Configure the object type definition](documentation/developers-and-admins/customization/object-types/object-type-configuration) stored within the _Info_ class.


## Define the data class
Data classes are managed from the **Modules** application in the Xperience administration. Each class must belong to a **Module** – a container that groups related data classes.
### Create a module
Begin by creating a module:
  1. Open the **Modules** application. 
     * The default listing shows only system modules that contain one or more [extendable system object types](documentation/developers-and-admins/customization/object-types/extend-system-object-types). You cannot create new data classes under system modules.
  2. Select **New module**.
  3. Fill in the **Module name** and **Code name** fields. Optionally, you can give the module a **Description**. 
     * These fields mainly serve as metadata accessible from within object types to determine module association.
  4. Select **Save**.


### Define and model the data class
With the module created, you can now create new data class:
  1. Select your module from the listing.
  2. Switch to the **Classes** tab and select **New class**. 
     * The class models the object’s data structure for ORM and stores the object type definition.
  3. Enter the class’s **Class name** and **Code name**. 
     * The code name is also used to identify the object type registration in the system.
  4. Select **Save**.


The system creates the data class and its corresponding database table. The database table name follows the _Namespace_Name_ pattern. For example: _Acme_Office_
Continue by modeling the database entity:
  1. Switch to the **Database columns** tab.
  2. Using the [field editor](documentation/developers-and-admins/customization/field-editor), model the object as required. 
     * Each data class (database entity) is created with an [identity column](https://docs.microsoft.com/en-us/sql/t-sql/statements/create-table-transact-sql-identity-property) that cannot be removed.
     * Each added field creates a column in the corresponding database table.
     * At this stage, you are only modeling the database entity. You cannot assign value editors ([UI form components](documentation/developers-and-admins/customization/extend-the-administration-interface/ui-form-components)) to fields (in contrast with [content types](documentation/developers-and-admins/development/content-types)). This is done at a later stage when defining the object type’s editing interface.


## Generate code files
With the entity modeled, the last step is to generate code files for the object type.
  1. (Optional) Switch to the **Code** tab and assign columns as required. See [Configure code generation for object types](documentation/developers-and-admins/customization/object-types#Generatecodefilesforsystemobjects-otcodegenconfig) for a description of each setting. 
     * All the available settings only affect code generation and can be changed later in the generated code files (or by regenerating the class).
  2. Generate the code files by running the [code generator](documentation/developers-and-admins/api/generate-code-files-for-system-objects) utility. 
     * See [Integrate custom code](documentation/developers-and-admins/customization/integrate-custom-code) for recommendations related to organizing custom code files.
CMD
**Generate object type code files**
Copy
```
dotnet run --no-build -- --kxp-codegen --type "Classes"
```



The generated **< ObjectTypeName>Info** class contains the object type definition and system registration which consists of the following components:
  * The unique identifier under which the object type is registered in the system is stored in the `OBJECT_TYPE` constant.
  * The `RegisterObjectType` assembly attribute. Registers the object type and its implementing class into the system.
  * An object of the `ObjectTypeInfo` type under the `TYPEINFO` member. 
    * Defines the object type and its behavior in the system.


This concludes the necessary steps for object type creation. The type is now registered in the system and available to work with using the API.
The created object type only contains the most basic type definition required to work with the object in the system. The object type can be further configured according to your requirements. See [Object type configuration](documentation/developers-and-admins/customization/object-types/object-type-configuration).
For a general overview of the API responsible for ORM and general database interaction, see [Database table API](documentation/developers-and-admins/api/database-table-api) and [ObjectQuery API](documentation/developers-and-admins/api/objectquery-api).
See the next section to learn how to define editing forms for the object type and display them on pages in the administration interface.
You can further configure code generation for object types under **Modules** → _select a module_ → **Classes** → _edit a class_ → **Code**.
Setting |  Description  
---|---  
Object type |  The identifier of the object type in the system. Must be unique within the Xperience instance. We recommend prefixing object types with a shared identifier. For example, system object types begin with the `CMS.` prefix (this prefix is reserved).   
Display name column |  Stores a human-readable name of the object. Reflected in UIs that work with objects (e.g., in breadcrumbs, navigation, object listings). Depends mainly on the type of the used [UI page](documentation/developers-and-admins/customization/extend-the-administration-interface/ui-pages).  
Code name column |  Stores the code name of each object. This column is used to identify the object when retrieving objects via their code name. If set, generated provider interfaces by default contain a `Get(string codename)` method.  
GUID column |  Stores the [GUID](https://en.wikipedia.org/wiki/Universally_unique_identifier) of each object. This column is used to identify the object when retrieving objects via GUIDs. If set, generated provider interfaces by default contain a `Get(Guid guid)` method. Only columns with the _GUID_ [data type](documentation/developers-and-admins/customization/field-editor/data-type-management) can be selected.  
“Last modified” column |  If set, can be automatically populated by the system when performing certain supported actions with objects of the given type, such as updates (calling `Set` on the corresponding provider). Only columns with the _DateTime_ [data type](documentation/developers-and-admins/customization/field-editor/data-type-management) can be selected.  
Binary column |  Stores associated binary data. Only columns with the _Binary_ [data type](documentation/developers-and-admins/customization/field-editor/data-type-management) can be selected.  
Additionally, columns that can be used to uniquely identify individual records in the database (ID, Code name, GUID) have the _Use hashtable_ option available. If the option is enabled, retrieved objects are cached under the corresponding identifier. For subsequent queries targeting previously retrieved records, the cached values are used, reducing database usage. However, note that enabling hashtable caching for data classes with a significant number of records that are often loaded by the system may result in a large increase in application memory usage. See [Object type configuration](documentation/developers-and-admins/customization/object-types/object-type-configuration).
## Define editing forms 
Object types can have multiple editing forms, each shown in a different context as required. For example, creating new objects of a particular type can show a different set of form fields than the edit action, depending on user requirements.
You can manage all editing forms for a given object type on its **UI forms** tab:
  1. In the **Modules** application, edit a module and select a data class.
  2. Switch to the **UI forms** tab and select **New UI form**.
  3. Enter the **UI form name** and **Code name**. 
     * UI forms with the _create_ or _edit_ code names get used by default on object create and edit pages respectively. See [Display editing forms on UI pages](documentation/developers-and-admins/customization/object-types#display-editing-forms-on-ui-pages).
  4. Select **Save**.


With the UI form created, you now need to define its fields. Depending on the target use case, the form’s fields can contain all or a subset of the type’s database columns. Columns marked as **Required** , if not added to a form, use the default value configured on the _Database columns_ tab.
  1. Edit a created UI form and switch to the **Fields** tab.
  2. Select **New field** , the **Database column** selector opens. You now have two choices: 
    1. Create a field that corresponds to one of the database columns defined for the object type. Submitted data is persisted in the selected column.
    2. Select **New field without database column** to create a field whose value is not persisted in the database. For example, fields of this kind can be used to modify an object’s create/update logic, see [Process submitted UI form data](documentation/developers-and-admins/customization/object-types#process-submitted-ui-form-data).
  3. Using the [field editor](documentation/developers-and-admins/customization/field-editor), define all form fields according to your requirements. 
     * You can [use macro expressions](documentation/developers-and-admins/configuration/macro-expressions/macro-syntax) to set the **Default value** of fields.
  4. (Optional) Group fields into categories via **New category**. Categories contain all fields listed underneath them.


The UI form is now created and can be displayed.
## Create a user interface
To create a user interface for your objects, leverage the [user interface customization framework](documentation/developers-and-admins/customization/extend-the-administration-interface) exposed by the administration.
The following sections only briefly cover the necessary steps and provide links to relevant pages.
### Create object type listings
To create a page that lists objects of a specific type, use the [Listing UI page template](documentation/developers-and-admins/customization/extend-the-administration-interface/ui-pages/reference-ui-page-templates/listing-ui-page-template).
### Display editing forms on UI pages
To display created UI forms in the administration interface, use the [edit](documentation/developers-and-admins/customization/extend-the-administration-interface/ui-pages/reference-ui-page-templates/edit-ui-page-template) or [create](documentation/developers-and-admins/customization/extend-the-administration-interface/ui-pages/reference-ui-page-templates/edit-ui-page-template) UI page templates and assign the UI form’s name to the `UIFormName` property within the `ConfigurePage` method override.
These pages ensure that submitted values get propagated into the corresponding database columns mapped to each UI form field. 
Values from fields without database columns can be acted upon when processing submitted data on the back end.
### Process submitted UI form data
You can modify the logic that creates and updates the corresponding object in the database based on the data submitted from [create and edit pages](documentation/developers-and-admins/customization/object-types#Objecttypes-EditingFormsDisplay).
Within the UI pages that handle a particular object type, override the `FinalizeInfoObject` method.
To get field values, use:
  * `IFormFieldValueProvider.TryGet<TType>(string fieldName, out var fieldValue)` – returns the value of the given field as `TType`


where `fieldName` is the name of the field.
Access the object being created or edited via the `infoObject` parameter. You can also use `IFormFieldValueProvider` to read values from fields without database columns and use it, for example, for some conditional logic.
C#
**Object create and edit pages - FinalizeInfoObject example**
Copy
```
protected override Task FinalizeInfoObject(OfficeInfo infoObject, IFormFieldValueProvider fieldValueProvider, CancellationToken cancellationToken)
{
    // Updates the object based on form fields mapped to specific database columns
    base.FinalizeInfoObject(infoObject, fieldValueProvider, cancellationToken);

    // Reads the value from a field
    fieldValueProvider.TryGet<string>("FormFieldWithoutDatabaseColumn", out var fieldValue);

    // Further modifies a given value
    infoObject.OfficeDisplayName += $"_{fieldValue}";

    // The system validates and sets the object to the database
    return Task.CompletedTask;
}
```