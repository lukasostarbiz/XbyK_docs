---
source: https://docs.kentico.com/documentation/developers-and-admins/customization/object-types/object-type-configuration/model-object-type-relationships
scrape_date: 2026-01-22
---

  * [Home](/documentation)
  * [Developers and admins](/documentation/developers-and-admins)
  * [Customization](/documentation/developers-and-admins/customization)
  * [Object types](/documentation/developers-and-admins/customization/object-types)
  * [Object type configuration](/documentation/developers-and-admins/customization/object-types/object-type-configuration)
  * Model object type relationships 


# Model object type relationships
You can model the following types of relationships between object types:
  1. [Parent-child](#parent-child-relationships) – A parent-child relationship is a special case of a [one-to-many relationship](https://en.wikipedia.org/wiki/One-to-many_\(data_model\)). It models dependent relationships where the existence of some objects (children) does not make sense without another object (parent). In this type of relationship, deleting the parent object also causes the deletion of all its children. An example is an employer and their employees – if the employer ceases to exist, so do all their employees.
  2. [One-to-many (many-to-one)](#one-to-many-relationships) – A [one-to-many relationship](https://en.wikipedia.org/wiki/One-to-many_\(data_model\)) exists when a single instance of object `A` may be linked with many instances of object `B`, but one instance of object `B` is linked to only one instance of `A`. A typical example is the set of orders a customer has made on a given site.
  3. [Many-to-many](#many-to-many-relationships) – A [many-to-many relationship](https://en.wikipedia.org/wiki/Many-to-many_\(data_model\)) defines an associative structure between two or more object types using a special _binding object type_ that stores the identifiers of all objects involved in the relationship as foreign keys. Modeling the relationship using a supplementary database table reduces the problem to modeling multiple one-to-many relationships. A typical example of a many-to many relationship is one between roles and users – a user can be assigned to many roles, and a role can include many users. The binding table associates the identifiers of user and role objects.


## Parent-child relationships
A parent-child relationship is a type of hierarchy where distinct, connected objects have a logical relationship. A _parent_ is something that comes before its _child_ or _children_. At the time a child is created, its parent must already exist.
You can use the [type information](/documentation/developers-and-admins/customization/object-types/object-type-configuration) to define a parent-child hierarchy for the object types representing your data model. Every object (record in the database) of a child object type contains a reference to the specified parent object. Setting parent-child relationships ensures that the system can provide automation for the connected objects, such as the automatic deletion of child objects together with their parents.
To model a parent-child relationship between two object types:
  1. Open the **Modules** application and edit the data class that will be the **child** in the relationship.
  2. Switch to the **Database columns** tab and add a new database column with the _Integer number_ data type. This column will store the IDs of the parent objects (a _foreign key_). 
     * **Tip** : A common convention is to suffix columns storing the identifiers of objects with the **ID** suffix.
  3. [Generate](/documentation/developers-and-admins/api/generate-code-files-for-system-objects) the code for the object type again.
  4. Edit the code of the generated _Info_ class.
  5. Set the `parentIDColumn` and `parentObjectType` parameters in the constructor of the `TYPEINFO` member (last two parameters): 
     * **parentIDColumn** – the name of the column that stores the IDs of parent objects.
     * **parentObjectType** – the name of the parent object type as defined in the type information of the parent class. Generated _Info_ classes store the names in the static `OBJECT_TYPE` constant.
C#
**Sample child object type info constructor**
Copy
```
public static readonly ObjectTypeInfo TYPEINFO = new ObjectTypeInfo(
    providerType: typeof(ChildObjectInfoProvider),
    objectType: OBJECT_TYPE,
    objectClassName: "Acme.ChildObject",
    idColumn: "ChildObjectID",
    timeStampColumn: null,
    guidColumn: null,
    codeNameColumn: null,
    displayNameColumn: "ChildObjectDisplayName",
    binaryColumn: null,
    parentIDColumn: "ParentObjectID",
    parentObjectType: ParentObjectInfo.OBJECT_TYPE)
{
    TouchCacheDependencies = true,
};
```

  6. Save the changes and rebuild the solution.


The parent-child relationship is now established. When creating new child objects via the API, ensure the added foreign key column is populated with the identifier of the parent object. Otherwise, the system cannot properly handle the referential relationship automatically.
To reflect this relationship in the administration interface, you need to ensure your custom interface elements create and display child objects correctly. See the following sections for details:
  * [Child object listing pages](#child-object-listing-pages)
  * [Child object create pages](#child-object-create-pages)


Parent-child relationships also come with certain inherent limitations, see the [Parent-child limitations](#parent-child-limitations) section for more information.
### Child object listing pages
To show only objects that are the children of a specific parent, listing pages must filter the objects by the identifier of a specific parent. When adding listing pages, use UI page structures such as the following:
  * Parent object listing 
    * Parent object create
    * Parent object edit section (adds a URL slug with the identifier of the **parent** to the URL) 
      * Parent object edit
      * **Child object listing** (consumes the URL slug with the **parent** object’s identifier) 
        * [Child object create](#child-object-create-pages) (consumes the identifier of the **parent**)
        * Child object edit section (adds a URL slug with the identifier of the **child** to the URL) 
          * [Child object edit](#child-object-edit-pages) (consumes the identifiers of the selected **parent** and **child**)


And parse the parent identifier using [page model binders](/documentation/developers-and-admins/customization/extend-the-administration-interface/ui-pages#retrieve-parameters-from-the-url). To filter the set of objects according to the currently selected parent, use a [query modifier](/documentation/developers-and-admins/customization/extend-the-administration-interface/ui-pages/reference-ui-page-templates/listing-ui-page-template#modify-loaded-data).
C#
**Child object listing page**
Copy
```
[assembly: UIPage(
    parentType: typeof(ParentObjectEditSection),
    slug: "children",
    uiPageType: typeof(ChildObjectList),
    name: "Child object listing",
    templateName: TemplateNames.LISTING,
    order: 200)]

public class ChildObjectList : ListingPage
{
    protected override string ObjectType => ChildObjectInfo.OBJECT_TYPE;

    // Parses the parent object's identifier from the URL
    [PageParameter(typeof(IntPageModelBinder))]
    public int ParentId { get; set; }

    public override Task ConfigurePage()
    {
        ...

        // Ensures links to child object edit pages
        // Child edit pages are parameterized by the identifier of the parent
        // and the identifier of the child (PageParameterConstants.PARAMETERIZED_SLUG)
        // For this reason, you need to specify the identifier of the current parent
        // (the method automatically appends the identifier of each child - row item - to the URL)
        PageConfiguration.AddEditRowAction<ChildObjectEdit>(parameters:
            new PageParameterValues
                {
                    { typeof(ParentObjectEditSection), ParentId }
                });

        // Displays only the children of the parent identified by 'ParentId'
        PageConfiguration.QueryModifiers
            // Displays only children of the currently selected parent object
            .AddModifier((query, _) =>
            {
                return query.WhereEquals(nameof(ChildObjectInfo.ParentID), ParentId);
            });

        return base.ConfigurePage();
    }
}
```

### Child object create pages
When creating the child object via the administration interface, [create pages](/documentation/developers-and-admins/customization/extend-the-administration-interface/ui-pages/reference-ui-page-templates/edit-ui-page-template#use-edit-pages-to-create-new-objects) must populate the foreign key column with the identifier of the parent object. To finalize an object after it was created via the UI, the create page template provides the `FinalizeInfoObject` method. The create page must also obtain the identifier of the parent object. You can leverage UI page hierarchies such as the following to parse the parent identifier using [page model binders](/documentation/developers-and-admins/customization/extend-the-administration-interface/ui-pages#retrieve-parameters-from-the-url):
  * Parent object listing 
    * Parent object create
    * Parent object edit section (adds a URL slug with the identifier of the **parent** to the URL) 
      * Parent object edit
      * [Child object listing](#child-object-listing-pages) (consumes the identifier of the **parent**) 
        * **Child object create** (consumes the identifier of the **parent**)
        * Child object edit section (adds a URL slug with the identifier of the **child** to the URL) 
          * [Child object edit](#child-object-edit-pages) (consumes the identifiers of the selected **parent** and **child**)


C#
**Child object create page**
Copy
```
[assembly: UIPage(
    parentType: typeof(ChildObjectList),
    slug: "create",
    uiPageType: typeof(ChildObjectCreate),
    name: "Child object create",
    templateName: TemplateNames.EDIT,
    order: UIPageOrder.First)]

// Child object create page that redirects to 'ChildObjectEdit' after the object is created
public class ChildObjectCreate : CreatePage<ChildObjectInfo, ChildObjectEdit>
{
    // Gets the identifier of the parent from the URL (dictated by the UI page hierarchy)
    [PageParameter(typeof(IntPageModelBinder), typeof(ParentObjectEditSection))]
    public int ParentId { get; set; }

    public ChildObjectCreate(IFormComponentMapper formComponentMapper, IFormDataBinder formDataBinder, IPageLinkGenerator pageLinkGenerator)
        : base(formComponentMapper, formDataBinder, pageLinkGenerator)
    {
    }

    public override Task ConfigurePage()
    {
        // Ensures correct URL for child object edit pages when redirected after object creation
        AdditionalLinkParameters.Add(
            new PageParameterValues
            {
                { typeof(ChildObjectEditSection), ParentId }
            });

        return base.ConfigurePage();
    }

    protected override Task FinalizeInfoObject(ChildObjectInfo infoObject, IFormFieldValueProvider fieldValueProvider, CancellationToken cancellationToken)
    {
        // Sets the parent identifier to the foreign key column 'ParentID'
        infoObject.ParentID = ParentId;

        return base.FinalizeInfoObject(infoObject, fieldValueProvider, cancellationToken);
    }
}
```

### Child object edit pages
Child object edit pages behave as regular edit page. The only difference is that, depending on the UI page hierarchy of your implementation, their URLs may be parameterized multiple times. For example, assuming the following page hierarchy:
  * Parent object listing 
    * Parent object create
    * Parent object edit section (adds a URL slug with the identifier of the **parent** to the URL) 
      * Parent object edit
      * [Child object listing](#child-object-listing-pages) (consumes the identifier of the **parent**) 
        * [Child object create](#child-object-create-pages) (consumes the identifier of the **parent**)
        * Child object edit section (adds a URL slug with the identifier of the **child** to the URL) 
          * **Child object edit** (consumes the identifiers of the selected **parent** and **child**)


The edit page URL is parameterized by:
  * the identifier of the parent (parent object edit section)
  * the identifier of the child (child object edit section)


When [generating links](/documentation/developers-and-admins/customization/extend-the-administration-interface/ui-pages#page-urls-and-routing) to these pages, you need to keep this in mind and pass the appropriate parameters. For example, the `AddEditRowAction` [ListingPage extension method](/documentation/developers-and-admins/customization/extend-the-administration-interface/ui-pages/reference-ui-page-templates/listing-ui-page-template#add-row-actions) must be supplied with the identifier of the parent (the method ensures the identifier of the child – a row in the listing – automatically).
See the following snippets for a sample implementation:
C#
**Child object edit section**
Copy
```
using Kentico.Xperience.Admin.Base;

[assembly: UIPage(
    parentType: typeof(ChildObjectListing),
    slug: PageParameterConstants.PARAMETERIZED_SLUG,
    uiPageType: typeof(ChildObjectEditSection),
    name: "Edit section for child objects",
    templateName: TemplateNames.SECTION_LAYOUT,
    order: UIPageOrder.NoOrder)]

public class ChildObjectEditSection : EditSectionPage<ChildObjectInfo>
{
}
```

The following example assumes the object type has a corresponding [“edit” UI form](/documentation/developers-and-admins/customization/object-types) defined.
C#
**Child object edit page**
Copy
```
using Kentico.Xperience.Admin.Base;
using Kentico.Xperience.Admin.Base.Forms;

[assembly: UIPage(
    parentType: typeof(ChildObjectEditSection),
    slug: "edit",
    uiPageType: typeof(ChildObjectEdit),
    name: "Edit child",
    templateName: TemplateNames.EDIT,
    order: UIPageOrder.First)]

public class ChildObjectEdit : InfoEditPage<ChildObjectInfo>
{
    public ChildObjectEdit(IFormComponentMapper formComponentMapper, IFormDataBinder formDataBinder) : base(formComponentMapper, formDataBinder)
    {
    }

    [PageParameter(typeof(IntPageModelBinder), typeof(ChildObjectEditSection))]
    public override int ObjectId { get; set; }
}
```

### Parent-child limitations
#### Content item or page objects as parents
Custom child objects under content item or page classes as the parent are **NOT** supported. Content items and pages in Xperience are complex data structures that consist of multiple classes and have special logic for handling other factors, such as workflows and multilingual support.
Parent-child relationships that include content items or pages do not provide the automatic functionality that works for other classes, such as deleting of child objects along with the parent page.
#### Self-referencing parent-child relationships
The system does **NOT** support self-referential parent child relationships. Such cases would lead to various problems, for example when creating the first object of the given object type (an existing parent must be assigned to each object).
The recommended approach is to create a separate object type for each level of the hierarchy, with a root that does not have a parent at the top. If you need to model a tree hierarchy with variable depth, you can create [database columns that store references](#one-to-many-relationships) to the same object type, and use those to handle the parent-child hierarchy.
## One-to-many relationships
One-to-many relationships are based on database columns that store references to objects of other types (_foreign keys_). Such references allow each object of the given object type to have a relationship with an object of another type. For example, you can assign users, members, or any other object in the system to custom objects.
To ensure that the system automatically handles dependencies for your references, you need to register the reference fields in the [type information](/documentation/developers-and-admins/customization/object-types/object-type-configuration). With correct configuration, the system maintains referential integrity of relationships for [CI/CD](/documentation/developers-and-admins/ci-cd), or when removing references to deleted objects.
You can define references in two possible directions:
  * From a custom object type to another object type
  * From any [extendable system class](/documentation/developers-and-admins/customization/object-types/extend-system-object-types) to custom object types


**References to content item or page objects are not supported**
The system does **NOT** support creating references from custom classes to content item or page objects. Content items and pages are complex data structures that consist of multiple classes and have special logic for handling other factors, such as workflows and multilingual support.
### Add references from custom object types
To create a reference from a custom object type to another object type:
  1. Open the **Modules** application.
  2. Edit the custom module and data class where you wish to add the reference.
  3. On the **Database columns** tab, create a **New field**. 
     * A common convention is to suffix identifier columns with the **ID** suffix.
     * Set the field’s **Data type** to _Integer number_.
     * Select the target object type in the **Reference to** selector.
     * Choose a **Reference type** to determine how the system handles referential integrity and automatic removal of objects when a referenced object is deleted. For more information about the available options, see the table describing the `ObjectDependencyEnum` values below.
The **Required** flag in the field settings controls whether the reference field must have a target object set (not to be confused with the _Required_ option of the **Reference type** setting).
  4. Select **Save**. You can create any number of reference fields using the same approach.
  5. [Regenerate the code](/documentation/developers-and-admins/api/generate-code-files-for-system-objects) for the modified object type. If you have already customized the _Info_ , _IInfoProvider_ or _InfoProvider_ classes, manually compare and copy the differences caused by the reference fields to avoid overwriting your changes.
  6. (Optional) Set the ID columns as foreign keys in your database. See [Configure foreign key columns in the database](#configure-foreign-key-columns-in-the-database) for more information.


**Important** : The _Reference_ settings that you configure for fields on the **Database fields** tab only serve as input for the default code generated for the _Info_ class. Changing the field settings after you generate your code does NOT change the behavior of the reference fields. To make changes, you need to adjust the code of the given _Info_ class or regenerate the code.
The type information definition in the generated _Info_ code automatically includes the required object dependencies based on the _Reference_ settings of your fields. If you view the code, you can see that the `DependsOn` property in the [initializer](https://docs.microsoft.com/en-us/dotnet/csharp/programming-guide/classes-and-structs/object-and-collection-initializers) of the `TYPEINFO` member contains a `List` collection of `ObjectDependency` objects.
C#
**Object dependencies**
Copy
```
// Defines references from a custom object type to User and Culture
DependsOn = new List<ObjectDependency>
{
    // CustomTypeUserID and CustomTypeCultureID identify the foreign key columns defined for the custom object type
    new ObjectDependency("CustomTypeUserID", UserInfo.OBJECT_TYPE, ObjectDependencyEnum.Required),
    new ObjectDependency("CustomTypeCultureID", CultureInfo.OBJECT_TYPE, ObjectDependencyEnum.NotRequired)
},
```

Each `ObjectDependency` object defines one reference from the custom object type to another type in the system. `ObjectDependency` objects have the following parameters:
  * **column (string)** – the name of the foreign key column that stores the IDs of the referenced objects.
  * **object type (string)** – the name of the object type that is the target of the reference (accessible via the `OBJECT_TYPE` constant exposed by _Info_ classes).
  * **reference type (ObjectDependencyEnum)** – determines how the system handles referential integrity and automatic removal of objects when a referenced object is deleted. The value must be one of the options from the `ObjectDependencyEnum`:
Value  |  Description   
---|---  
NotRequired  |  Represents an optional (_weak_) reference. If the referenced object is deleted, the system sets a `null` value to the foreign key column.  The column that stores the IDs of the referenced objects must support empty/null values (the **Required** flag cannot be enabled).   
RequiredHasDefault  |  If the referenced object is deleted, the system automatically assigns a default object instead of setting `null` to the foreign key column.  To set the default object for your custom object types, you need to override the `GetDefaultObject` method in the code of the given _Info_ class.  C# **Assign a default object** Copy ```
protected override BaseInfo GetDefaultObject()
{
	// Gets the object of the custom type whose code name is "default"
	CustomTypeInfo defaultObject = CustomInfo.Provider.Get("default");
	return defaultObject;
}

```
  
Required  |  The system always maintains the integrity and consistency of the reference as long as a value is set (_strong reference_). The value can still be `null` if the **Required** flag is disabled for the given field.  If the referenced object is deleted, the system automatically deletes all objects connected as references.   
Binding  |  Only intended for the reference fields of dedicated binding object types used to model many-to-many relationships (via [associative tables](https://en.wikipedia.org/wiki/Associative_entity)). Works the same way as the `Required` dependency type.  See: [Many-to-many relationships](#many-to-many-relationships)  


### Add references from extendable system objects
You can extend existing objects in the system to store references to custom object types.
  1. Open the **Modules** application.
  2. Edit the module and the data class that you wish to extend.
  3. On the **Database columns** tab, create a **New field** using the [field editor](/documentation/developers-and-admins/customization/field-editor). 
     * A common convention is to suffix identifier columns with the **ID** suffix.
     * Set the field’s **Data type** to _Integer number_.
     * Either keep the field hidden and handle the values in code, adjust an existing UI form, or create a completely new UI form that manages the dependency.
     * There are no _Reference_ settings to configure for the field with this type of reference.
  4. **Save** the field.
  5. (Optional) Set the ID columns as foreign keys in your database. See [Configure foreign key columns in the database](#configure-foreign-key-columns-in-the-database) for more information.


You can repeat the process above for any number of customizable system object types. Once the reference database columns (_foreign keys_) are created, you need to register them as dependencies in the type information of your custom class:
  1. In an IDE, edit the _Info_ code of the custom object that is the **target of the reference**.
  2. In the [initializer](https://docs.microsoft.com/en-us/dotnet/csharp/programming-guide/classes-and-structs/object-and-collection-initializers) of the TYPEINFO object, assign a _List_ collection of`ExtraColumn` objects into the `Extends` property.
     * Create an `ExtraColumn` object for each custom reference that targets the given object type.
  3. Save the changes and rebuild the project.


`ExtraColumn` objects accept the following parameters:
  * **extended object type (string)** – the name of the object type where you added the reference field (the extendable system object).
  * **column (string)** – the name of the foreign key column in the extended class that stores the IDs of the referenced objects.
  * **reference type (ObjectDependencyEnum)** – determines how the system handles referential integrity and automatic removal of objects when a referenced object is deleted. The value must be one of the options from the `ObjectDependencyEnum`. See the table in the [Add references from custom object types](#add-references-from-custom-object-types) section for details.


C#
**Example**
Copy
```
// Defines references from the Contact and User classes to a custom class
Extends = new List<ExtraColumn>()
{
    new ExtraColumn(ContactInfo.OBJECT_TYPE, "ContactCustomClassID", ObjectDependencyEnum.Required),
    new ExtraColumn(UserInfo.OBJECT_TYPE, "UserCustomClassID", ObjectDependencyEnum.NotRequired)
}
```

The code informs the system that the specified field of the extended class stores references to another class. During the initialization of the application, the system automatically inserts an object dependency into the type information definition of the extended class.
### Edit references from the UI
To make the added reference (foreign key) columns editable from the create or edit pages of the corresponding object, follow this general approach:
  1. Open the **Modules** application and edit the extended data class.
  2. Switch to the **UI Forms** tab and edit the UI form used to create or edit the object (the process is analogous for both).
  3. Add a new to the UI Form with the following properties: 
    1. **Database column:** New field without database column
    2. **Field name:** choose a suitable name, you will be accessing and configuring this field via the API
    3. **Data type:** Text
    4. **Required:** Yes (checkbox selected)
    5. **Form component:** Dropdown selector
    6. **Data source:** leave empty (will be populated later using the API)
  4. **Save** the changes.


The modified UI form now displays an empty Dropdown selector. The next step is to populate it with the available reference targets.
  1. In an IDE, edit the UI page used to create or edit the extended object (the process is analogous for both).
  2. Override the `GetFormItems` method and populate the dropdown selector field with the identifiers and display names of the target objects.
C#
**Dropdown data source using GetFormItems()**
Copy
```
// Populates the dropdown with custom Office objects
protected override async Task<ICollection<IFormItem>> GetFormItems()
{
    // Gets the collection of form components for the UI form displayed by this page
    var items = await base.GetFormItems();

    // Gets all items that can be the target of the relationship
    // Formats the data according to the requirements of the 'Dropdown selector' component: <identifier>;<displayName>
    // Use constructor dependency injection to obtain required services
    var selectorOptions = officeInfoProvider.Get().GetEnumerableTypedResult()
                                            .OrderBy(s => s.OfficeName)
                                            .Select(x => $"{x.OfficeID};{x.OfficeName}");

    // Gets the Dropdown form component by its UI form field name
    var officeSelector = items.OfType<IFormComponent>()
                              .FirstOrDefault(i => i.Name == "OfficeSelect") as DropDownComponent;

    // Sets the Dropdown component's data source property
    officeSelector.Properties.Options = string.Join("\r\n", selectorOptions);

    return items;
}
```

  3. Override the `FinalizeInfoObject` method and ensure the identifier from the selector is saved into the foreign key column.
C#
**Set foreign key references using FinalizeInfoObject()**
Copy
```
protected override Task FinalizeInfoObject(OfficeJobInfo infoObject, IFormFieldValueProvider fieldValueProvider, CancellationToken cancellationToken)
{
    base.FinalizeInfoObject(infoObject, fieldValueProvider, cancellationToken);

    // Gets the submitted value from the UI form field dropdown
    fieldValueProvider.TryGet<string>("OfficeSelect", out var value);

    // Sets the value to the foreign key column
    // The field is required, so no validation is needed, we know the value is always there and in the format we need
    infoObject.OfficeJobParentID = Int32.Parse(value);

    // Sets the object to the database
    return Task.CompletedTask;
}
```

  4. Rebuild the project.


If you navigate to the page with the edited UI form, you can use the added dropdown selector to set the target of the dependency. Note that if the edited UI form is present on multiple pages, you need to repeat the process for each affect page or separate the logic to a shared base class.
[![Sample editing interface for the foreign key column](/docsassets/documentation/model-object-type-relationships/ForeignKeyEdit.png)](/docsassets/documentation/model-object-type-relationships/ForeignKeyEdit.png)
## Many-to-many relationships
A  _many-to-many relationship_ occurs when multiple records in a table are associated with multiple records in another table. A typical example of a many-to many relationship is one between roles and users. A user can be assigned to many roles, and a role can include many users.
MSSQL doesn’t allow you to implement a direct many-to-many relationship between tables. However, you can break the many-to-many relationship into multiple one-to-many relationships by using a **binding object type** with its own table (an [associative table](https://en.wikipedia.org/wiki/Associative_entity)). Each record in the binding type’s table contains the value of the primary keys of the object types it associates (_foreign keys_).
**References to content item or page objects are not supported**
The system does **NOT** support creating binding object types between custom objects and content item or page objects. Content items and pages are complex data structures that consist of multiple classes and database tables, and have special logic for handling other factors, such as workflows and multilingual support.
### Add binding classes
Use the following steps to create a binding between objects:
  1. Open the **Modules** application and edit the custom module where you want to create the binding.
  2. Select the **Classes** tab and click **New class**.
  3. Fill in the **Class name** and **Code name**.
  4. Select **Save**.


Now define the database columns storing the foreign keys of the related objects:
  1. Switch to the **Database columns** tab.
  2. Select **New field** and fill in the following values using the [field editor](/documentation/developers-and-admins/customization/field-editor). 
    1. **Data type** : Integer number
    2. **Required** : yes (checkbox selected)
    3. **Reference to** : select the appropriate object type
    4. **Reference type** : Binding
  3. Repeat for all foreign keys in the relationship (two at minimum).


The system creates the corresponding table in the database. Perform the following steps to configure the binding and finalize the database table:
  1. [Generate the binding’s code](/documentation/developers-and-admins/api/generate-code-files-for-system-objects).
  2. Include the files into your project. 
     * See [Integrate custom code](/documentation/developers-and-admins/customization/integrate-custom-code) for best practices.
  3. Edit the generated _Info_ class and find the `TYPEINFO` member.
  4. Add the `IsBinding` property to the `ObjectTypeInfo` initializer and set the value to `true`. See [Object type configuration](#Modelobjecttyperelationships-bindinginfoconfig) for details.
  5. Save the changes and rebuild the solution.
  6. (Optional) Set the ID columns as foreign keys in your database. See [Configure foreign key columns in the database](#configure-foreign-key-columns-in-the-database) for more information.


The binding object type is now ready. You can create relationships between objects using the API or prepare a user interface for this purpose.
### Object type configuration 
The [type information](/documentation/developers-and-admins/customization/object-types/object-type-configuration) is code that defines the general behavior and basic properties of object types. You can configure the type information inside the _Info_ class of a given binding using the `TYPEINFO` member.
Generated binding  _Info_ classes have **most** of the type information set automatically by the code generator. You need to configure the remaining properties and verify the rest is set correctly.
**Parameters of the ObjectTypeInfo constructor** :
  * **idColumn (4)** – the name of the identity column of the binding.
  * **parentID (11)** – the name of the column that stores the ID of the **first** object type in the relationship.
  * **parentObjectType (12)** – the object type name of the **first** object type in the relationship. Get the value from the `OBJECT_TYPE` constant in the given type’s _Info_ class.


**Properties in the ObjectTypeInfo initializer** :
  * **IsBinding** – must always be set to true for bindings _._ This property **is not automatically set by the code generator** and must be added manually!
  * **DependsOn** – List of `ObjectDependency` objects representing the object types in the relationship. Add an `ObjectDependency` for each related object **except** for the one specified via the _parent_ parameters in the constructor.


C#
**Binding class configuration example**
Copy
```
// Sample type information definition for a general binding
// The first object in the binding ALWAYS needs to be defined in the ObjectTypeInfo constructor
public static ObjectTypeInfo TYPEINFO = new ObjectTypeInfo(
    providerType: typeof(ObjectBindingInfoProvider),
    objectType: OBJECT_TYPE,
    objectClassName: "Acme.ObjectBinding",
    idColumn: "ObjectBindingID",
    timeStampColumn: null,
    guidColumn: null,
    codeNameColumn: null,
    displayNameColumn: null,
    binaryColumn: null,
    parentIDColumn: "ForeignKeyOne",
    parentObjectType: "ObjectType.One")
{
    // Must be added manually, not handled by code generators
    IsBinding = true,
    // Adds other object types to the binding relationship
    DependsOn = new List<ObjectDependency>()
    {
        new ObjectDependency("ForeignKeyTwo", "ObjectType.Two", ObjectDependencyEnum.Binding),
    },
};
```

### Add objects to a many-to-many relationships from the UI
You typically add objects to many-to-many relationships from the editing interface of one of the objects in the association. That is, you can leverage the following [UI page](/documentation/developers-and-admins/customization/extend-the-administration-interface/ui-pages) structure to obtain the identifier of the object using [page model binders](/documentation/developers-and-admins/customization/extend-the-administration-interface/ui-pages#parameterized-url-slugs):
  * Object listing page 
    * Object edit section (adds a URL slug with the identifier of the **source object** to the URL) 
      * Source object edit
      * Source object many-to-many relationship management (typically a [binding page](/documentation/developers-and-admins/customization/extend-the-administration-interface/ui-pages/reference-ui-page-templates/binding-ui-page-template))


For example, you add roles to users either when editing the role object or the user object. Therefore, you can always obtain the identifier of one of the objects in the association from the UI page hierarchy.
#### Implementation
To create the page that manages the many-to-many relationship (this process is analogous for all object types associated via a given binding relation), create a [binding UI page](/documentation/developers-and-admins/customization/extend-the-administration-interface/ui-pages/reference-ui-page-templates/binding-ui-page-template) that lists objects associated to the object being edited.
  * Use a page model binder to get the identifier of the edited object from the URL.


C#
**Many-to-many management binding page**
Copy
```
// Demonstrates how to manage a many-to-many relationship for a custom Office object that is associated
// to UserInfo objects (system users). This association is constructed from the Offices side of the relationship
// (i.e., the page URL structure contains the identifier of an office object)

public class OfficeWorkers : InfoBindingPage<OfficeUserInfo, UserInfo>
{
    // Configures the ID of the edited binding object
    [PageParameter(typeof(IntPageModelBinder))]
    public override int EditedObjectId { get; set; }

    // Returns the name of the property that is used as the source in the binding object type
    protected override string SourceBindingColumn => nameof(OfficeUserInfo.OfficeID);

    // Returns the name of the property that is used as the target in the binding object type
    protected override string TargetBindingColumn => nameof(OfficeUserInfo.AssignedUserID);

    // Configures the listing of workers assigned to the office
    public override async Task ConfigurePage()
    {
        // Configures captions of buttons and listings
        PageConfiguration.AddBindingButtonText = "Add workers";
        PageConfiguration.ExistingBindingsListing.Caption = "Users assigned as workers";
        PageConfiguration.SaveBindingsButtonText = "Confirm";
        PageConfiguration.BindingSidePanelListing.Caption = "Select users as workers";

        // Configures columns displayed in the listing of existing bindings
        PageConfiguration.ExistingBindingsListing.ColumnConfigurations
            .AddColumn(nameof(UserInfo.UserName), "User name", searchable: true, defaultSortDirection: SortTypeEnum.Asc);

        // Configures columns displayed in the listing of available object
        PageConfiguration.BindingSidePanelListing.ColumnConfigurations
            .AddColumn(nameof(UserInfo.UserName), "User name", searchable: true, defaultSortDirection: SortTypeEnum.Asc);

        await base.ConfigurePage();
    }
}
```

The basic management UI is now ready. Users can create and remove associations using the added interface elements.
[![Many-to-many relationship editing interface](/docsassets/documentation/model-object-type-relationships/ManyToManyManagement.png)](/docsassets/documentation/model-object-type-relationships/ManyToManyManagement.png)
## Configure foreign key columns in the database
The system does not provide automatic management of foreign keys on the database level. You need to manually set the added foreign key columns as foreign keys in the corresponding database tables. This is not required to ensure correct functionality of foreign key columns, but is recommended to leverage the performance and data consistency advantages of foreign key constraints.
You can either use SQL Server Management Studio or execute an SQL script. See [Create Foreign Key Relationships](https://learn.microsoft.com/en-us/sql/relational-databases/tables/create-foreign-key-relationships) for more information.
![]()
[]()[]()
