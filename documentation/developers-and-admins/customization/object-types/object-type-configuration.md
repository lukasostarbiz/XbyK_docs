---
source: https://docs.kentico.com/documentation/developers-and-admins/customization/object-types/object-type-configuration
scrape_date: 2026-01-26
---

  * [Home](/documentation)
  * [Developers and admins](/documentation/developers-and-admins)
  * [Customization](/documentation/developers-and-admins/customization)
  * [Object types](/documentation/developers-and-admins/customization/object-types)
  * Object type configuration 


# Object type configuration
**Object type information** refers to the metadata of Xperience [object types](/documentation/developers-and-admins/customization/object-types). The information determines the basic properties and behavior of an object type when: 
  * Performing basic CRUD operations over objects of a particular type
  * Evaluating relationships with other classes (parent-child, object references and bindings)
  * Evaluating object dependencies (e.g,, to perform automatic deletion of related objects)


The configuration also determines how other system features that support working with object types behave:
  * [CI/CD](/documentation/developers-and-admins/ci-cd) – controls how objects are serialized
  * [Caching](/documentation/developers-and-admins/development/caching) – controls the caching behavior


Object types store their configuration in a separate type – `ObjectTypeInfo` – which is a mandatory component of each generated _Info_ class, together with the following:
  * A unique _object type name_ as an identifier (generated _Info_ classes store the value in a constant named `OBJECT_TYPE`).
  * The `RegisterObjectType` assembly attribute. The attribute assigns the type information definition to the _Info_ class via the unique _object type name_.


## Type info properties
### ObjectTypeInfo constructor
Navigate to the `TYPEINFO` field in the _Info_ class’s code and configure the following parameters in the `ObjectTypeInfo` constructor according to your requirements:
Parameter |  Type |  Description  
---|---|---  
Object type metadata  
providerType |  string |  The [Type](https://learn.microsoft.com/en-us/dotnet/api/system.type) of the class’s _InfoProvider_ class. For example: `typeof(UserInfoProvider)`  
objectType |  string |  Defined in the code of the _Info_ class (typically in an `OBJECT_TYPE`constant). Serves as the primary identifier for the class. Use the object type name to select classes in the API and when [building user interfaces](/documentation/developers-and-admins/customization/extend-the-administration-interface).  
objectClassName |  string |  The code name assigned to the [class](/documentation/developers-and-admins/customization/object-types) in the Xperience administration. Identifies the class’s definition in the database. For example, use the class code name when writing SQL where conditions for loading and filtering class records. **objectType and objectClassName identifiers** For custom object types, **always** use the same identifier for both the class and the object type (the default for generated _Info_ classes). Two distinct identifiers are in rare cased used internally by the system for purposes beyond the scope of this document.  
Database-related configuration  
idColumn  |  string |  Column that stores the IDs of objects of the given type (a _primary key_ column). Populated by the [code generator](/documentation/developers-and-admins/api/generate-code-files-for-system-objects) if set on the object’s **Code** tab in the administration.  
timeStampColumn  |  string |  Column that stores the last modification date for objects. Can be `null`. Populated by the code generator if set on the object’s **Code** tab in the administration.  
guidColumn  |  string |  Column that stores the [GUID identifiers](http://en.wikipedia.org/wiki/Globally_unique_identifier) of objects. Can be `null`. Populated by the code generator if set on the object’s **Code** tab in the administration.  
codeNameColumn  |  string |  Column that stores the unique text identifiers of objects. Can be `null` for classes without a dedicated code name column. Populated by the code generator if set on the object’s **Code** tab in the administration.  
displayNameColumn  |  string |  Column that stores the visible names of objects (names used in the administration interface or on the live site). Can be `null`. Populated by the code generator if set on the object’s **Code** tab in the administration.  
binaryColumn |  string |  Column that stores any binary data related this type of objects. Can be `null`. Populated by the code generator if set on the object’s **Code** tab in the administration.  
Object type relationships  
parentIDColumn |  string |  the name of the class field that stores the IDs of parent objects. Set to `null` for classes without a parent class. See [Model object type relationships](/documentation/developers-and-admins/customization/object-types/object-type-configuration/model-object-type-relationships).  
parentObjectType  |  string |  the _object type name_ of the class’s parent (as defined in the type information of the parent class). Set to `null` for classes without a parent class. See [Model object type relationships](/documentation/developers-and-admins/customization/object-types/object-type-configuration/model-object-type-relationships).  
### ObjectTypeInfo properties
Use the [initializer](https://docs.microsoft.com/en-us/dotnet/csharp/programming-guide/classes-and-structs/object-and-collection-initializers) to set other properties for the object. See [Reference - ObjectTypeInfo](/documentation/developers-and-admins/customization/object-types/object-type-configuration/reference-objecttypeinfo) for more information about the available properties.
### Hashtable caching
Info classes of object types support caching over columns that can be used to uniquely identify individual records (ID, Code name, GUID). Items get cached the first time they are fetched from the database. For subsequent queries targeting previously retrieved records, the cached values are used, reducing database usage. However, note that enabling hashtable caching for object types with a significant number of records may result in a large increase in application memory usage.
The caching is enabled by placing the `InfoCache` attribute over the generated info class.
C#
Copy
```
using CMS.DataEngine;

// Enables caching over the object ID and code name
// Note that the code name and guid columns must also be set using the ObjectTypeInfo constructor
[InfoCache(InfoCacheBy.ID | InfoCacheBy.Name | InfoCacheBy.Guid)]
public partial class MyObjectTypeInfo : AbstractInfo<MyObjectTypeInfo, IMyObjectTypeInfoProvider>
{
}
```

**Code generator support**
The caching is also configurable via each object type’s **Code** tab in the administration. Columns usable as unique identifiers have the _Use hashtable_ option available. The code generator reflects this setting when generating the code file by decorating the _Info_ class with the `InfoCache` attribute using corresponding parameters. See [Configure code generation for data classes](/documentation/developers-and-admins/customization/object-types#configure-code-generation-for-data-classes).
The attribute also allows you to configure the caching behavior via the following optional properties:
  * Cache priority (`Priority`) – Determines whether cached items can be released to relieve memory pressure. Set using the `InfoCachePriorty` enum. Allowed by default.
  * Eager loading (`LoadEagerly`) – Loads all objects of the given type into the cache on application start. Note that eagerly loading objects with a lot of records consumes large amounts of application memory. Disabled by default.


## Additional options
You can use the type information to configure how object types behave in the system. See the following topics:
  * [Enable CI/CD for object types](/documentation/developers-and-admins/customization/object-types/object-type-configuration/enable-ci-cd-for-object-types) – configure the type information of your custom classes to support synchronization of data using [CI/CD](/documentation/developers-and-admins/ci-cd).
  * [Model object type relationships](/documentation/developers-and-admins/customization/object-types/object-type-configuration/model-object-type-relationships) – define relationships among object types that are enforced by the system’s ORM framework: 
    1. Parent-child – use the type information to define a hierarchy for the classes representing your module’s data. Setting parent-child relationships for classes ensures that the system can provide many types of automatic behavior.
    2. One-to-many and many-to-many relationships – define fields for your custom classes that store references (foreign keys) to objects of other classes and vice versa.
  * [Reference - ObjectTypeInfo](/documentation/developers-and-admins/customization/object-types/object-type-configuration/reference-objecttypeinfo) – provides a reference of the `ObjectTypeInfo` properties that you can use to define object type behavior in the system.


![]()
[]()[]()
