# Reference - ObjectTypeInfo
  * [ Copy page link ](documentation/developers-and-admins/customization/object-types/object-type-configuration/reference-objecttypeinfo#) | [Get HelpService ID](documentation/developers-and-admins/customization/object-types/object-type-configuration/reference-objecttypeinfo#)
Core MVC 5


[✖](documentation/developers-and-admins/customization/object-types/object-type-configuration/reference-objecttypeinfo# "Close page link panel") [Copy to clipboard](documentation/developers-and-admins/customization/object-types/object-type-configuration/reference-objecttypeinfo#)
`ObjectTypeInfo` properties that you can use to [set the type information for object types](documentation/developers-and-admins/customization/object-types/object-type-configuration).
The reference lists the `ObjectTypeInfo` members that are intended for public use. The class also contains other members, which are used internally or handled automatically by the system. **We don’t recommend** working with any of the undocumented members.
The `ObjectTypeInfo` class has the following types of properties:
  * [Constructor](documentation/developers-and-admins/customization/object-types/object-type-configuration/reference-objecttypeinfo#constructor)
  * [Database columns](documentation/developers-and-admins/customization/object-types/object-type-configuration/reference-objecttypeinfo#database-columns)
  * [General](documentation/developers-and-admins/customization/object-types/object-type-configuration/reference-objecttypeinfo#general)
  * [Object type relationships and bindings](documentation/developers-and-admins/customization/object-types/object-type-configuration/reference-objecttypeinfo#object-type-relationships-and-bindings)
  * [Continuous integration](documentation/developers-and-admins/customization/object-types/object-type-configuration/reference-objecttypeinfo#continuous-integration)
  * [Macros](documentation/developers-and-admins/customization/object-types/object-type-configuration/reference-objecttypeinfo#macros)


## Constructor
The following properties are always set through the constructor parameters when creating new `ObjectTypeInfo` instances:
Property |  Type |  Description  
---|---|---  
BinaryColumn |  string |  The name of the column that stores binary data associated with the object type. Can be `null`.  
CodeNameColumn |  string |  The name of the column that stores the unique text identifiers of objects. Can be `null` for object types without a dedicated code name column.  
DisplayNameColumn |  string |  The name of the column that stores the visible names of objects (names used in the administration interface or on the live site). Can be `null`.  
GuidColumn |  string |  The name of the column that stores the [GUID identifiers](http://en.wikipedia.org/wiki/Globally_unique_identifier) of objects. Can be `null`.  
IDColumn |  string |  The name of the primary key column. Created together with the object type’s data class and non-removable.  
ObjectClassName |  string |  **Required** for all object types. The code name that is assigned to the data class in the Xperience administration interface. Identifies the class’s definition in the database.  
ObjectType |  string |  **Required** for all object types. Serves as the primary identifier string for the object type. Use the identifier to select objects of a particular type via the API or when [building user interfaces](documentation/developers-and-admins/customization/extend-the-administration-interface). Code generators automatically create the `OBJECT_TYPE` constant within the code of the _Info_ class.  
ParentIDColumn |  string |  The name of the column that stores the IDs of parent objects. Set to `null` for object types without a parent. See the parent-child relationships section of [Model object type relationships](documentation/developers-and-admins/customization/object-types/object-type-configuration/model-object-type-relationships) for more information.  
ParentObjectType |  string |  The _name_ of the object type’s parent (defined in the type information of the parent class within the `OBJECT_TYPE` constant). Set to `null` for object types without a parent.  
ProviderType |  [Type](https://docs.microsoft.com/en-us/dotnet/api/system.type) |  **Required** for all object types. The type of the type’s _InfoProvider_ class. For example: `typeof(UserInfoProvider)`  
TimeStampColumn |  string |  The name of the column that stores the last modification date for objects. Can be `null`.  
[> Back to list of ObjectTypeInfo property types](documentation/developers-and-admins/customization/object-types/object-type-configuration/reference-objecttypeinfo#ReferenceObjectTypeInfo-TypeInfoTop)
## Database columns
In addition to the `*Column` properties used in the `ObjectTypeInfo` constructor, you can set the following optional properties to set database columns that perform a specific function:
Property |  Type |  Description  
---|---|---  
ExtensionColumn |  string |  Intended for objects that store associated binary data. Sets the name of the column that stores the extension type of the binary data. Used by [CI/CD](documentation/developers-and-admins/ci-cd) when serializing the associated binary data.  
ObjectLevelColumn |  string |  Intended for object types that form a tree hierarchy. Sets the name of the column that stores the level of objects in the hierarchy structure.  
ObjectPathSettings |  PathSettings |  Intended for object types that form a tree hierarchy. Sets the name of the column that stores the path of objects in the hierarchy structure. You can also set the path column separately for paths built out of objects names or object IDs through the `ObjectNamePathSettings` and `ObjectIDPathSettings` properties respectively. The system uses the stored paths to more efficiently delete child objects when a parent is being deleted and when serializing the objects via CI/CD.  
[> Back to list of ObjectTypeInfo property types](documentation/developers-and-admins/customization/object-types/object-type-configuration/reference-objecttypeinfo#ReferenceObjectTypeInfo-TypeInfoTop)
## General
Property |  Type |  Description  
---|---|---  
IsCategory |  bool |  Indicates whether the object type represents a category for organizing other objects in a tree hierarchy. The default value is `false` _._  
LogEvents |  bool |  Indicates whether the system logs events into the [Event log](documentation/developers-and-admins/configuration/event-log) when objects of the object type are modified. `False` by default.  
ProviderObject |  IInfoProvider |  Read-only property. Gets an instance of the `IInfoProvider` provider class specified by the `ProviderType` property. See: [Database table API](documentation/developers-and-admins/api/database-table-api)  
TouchCacheDependencies |  bool |  Indicates whether the system _touches_ the corresponding dummy cache keys when an object is modified. This causes the cache to delete all items that depend on the given dummy keys. Set to _true_ if you wish to use objects of the object type as cache dependencies. Populated by the code generator for [binding object types](documentation/developers-and-admins/customization/object-types/object-type-configuration/model-object-type-relationships). See also: [Cache dependencies](documentation/developers-and-admins/development/caching/cache-dependencies)  
[> Back to list of ObjectTypeInfo property types](documentation/developers-and-admins/customization/object-types/object-type-configuration/reference-objecttypeinfo#ReferenceObjectTypeInfo-TypeInfoTop)
## Object type relationships and bindings
Property |  Type |  Description  
---|---|---  
CheckDependenciesOnDelete |  bool |  Applies to object types that are the target of a _Required_ type reference. See the one-to-many relationships section on [Model object type relationships](documentation/developers-and-admins/customization/object-types/object-type-configuration/model-object-type-relationships) for more information. If you set `CheckDependenciesOnDelete` to `true` _,_ the system prevents objects of the type from being deleted if they are the target of a required reference from at least one other object in the system. Defaults to `false`.  
DependsOn |  List<ObjectDependency> |  Registers fields that store references from the type to other types (i.e., foreign key columns). Allows the system to maintain the referential integrity of relationships when a referenced object is deleted. Assign a `List` collection of `ObjectDependency` objects, each defining one reference. **Note** : Do not include the parent reference fields specified in the `ParentIDColumn` properties. See the one-to-many relationships section on [Model object type relationships](documentation/developers-and-admins/customization/object-types/object-type-configuration/model-object-type-relationships) for more information.  
Extends |  List<ExtraColumn> |  Registers external fields that store references from other classes in the system to the given class. Allows the system to maintain the [referential integrity](https://en.wikipedia.org/wiki/Referential_integrity) of relationships when objects are removed from the system. Assign a _List_ collection of `ExtraColumn` objects, each defining one reference that targets the given class. See the one-to-many relationships section on [Model object type relationships](documentation/developers-and-admins/customization/object-types/object-type-configuration/model-object-type-relationships) for more information.  
IsBinding |  bool |  Indicates whether the object type represents a relationship between two or more object types. See the many-to-many relationships section on [Model object type relationships](documentation/developers-and-admins/customization/object-types/object-type-configuration/model-object-type-relationships) for more information.  
ParentTypeInfo |  ObjectTypeInfo |  Read-only property. Returns the `ObjectTypeInfo` instance of the parent object type.  
[> Back to list of ObjectTypeInfo property types](documentation/developers-and-admins/customization/object-types/object-type-configuration/reference-objecttypeinfo#ReferenceObjectTypeInfo-TypeInfoTop)
## Continuous integration
Property |  Type |  Description  
---|---|---  
ContinuousIntegrationSettings |  ContinuousIntegrationSettings |  Determines how the class works with the system’s [CI/CD solution](documentation/developers-and-admins/ci-cd). Set through the properties of the `ContinuousIntegrationSettings` class. See [Enable CI/CD for object types](documentation/developers-and-admins/customization/object-types/object-type-configuration/enable-ci-cd-for-object-types) for details.  
SerializationSettings |  SerializationSettings |  Determines how the system serializes the class’s objects from the database into XML data. Serialization is used by the system’s CI/CD solution. Set through the properties of the `SerializationSettings` class. See [Enable CI/CD for object types](documentation/developers-and-admins/customization/object-types/object-type-configuration/enable-ci-cd-for-object-types) for details.  
[> Back to list of ObjectTypeInfo property types](documentation/developers-and-admins/customization/object-types/object-type-configuration/reference-objecttypeinfo#ReferenceObjectTypeInfo-TypeInfoTop)
## Macros
Property |  Type |  Description  
---|---|---  
MacroSettings |  MacroProcessingSettings |  Configures the behavior of the class within the system’s [macro engine](documentation/developers-and-admins/configuration/macro-expressions). Set through the properties of the `MacroProcessingSettings` class:
  * `ContainsMacros (bool)` – indicates whether the class has fields that contain macro expressions in their values. The property determines if objects of the class are processed when [resigning macros](documentation/developers-and-admins/configuration/macro-expressions/macro-signatures) in the system. `True` by default. You can set the value to `false` to optimize the performance of the resigning process (during manual resigning and upgrades). Does NOT affect the resolving of macros.
  * `MacroCollectionName (string)` – overrides the name of object collections used for the class’s data in macros. If not set, a default name is automatically derived from the class code name.

  
SensitiveColumns |  List<string> |  Specifies class fields that the system excludes from [macro expressions](documentation/developers-and-admins/configuration/macro-expressions). Allows you to protect fields with sensitive values (e.g., passwords). Set the sensitive columns by assigning a `List` collection of column names.  
[> Back to list of ObjectTypeInfo property types](documentation/developers-and-admins/customization/object-types/object-type-configuration/reference-objecttypeinfo#ReferenceObjectTypeInfo-TypeInfoTop)