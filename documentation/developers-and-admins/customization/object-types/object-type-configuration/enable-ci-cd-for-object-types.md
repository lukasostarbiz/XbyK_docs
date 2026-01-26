---
source: https://docs.kentico.com/documentation/developers-and-admins/customization/object-types/object-type-configuration/enable-ci-cd-for-object-types
scrape_date: 2026-01-26
---

  * [Home](/documentation)
  * [Developers and admins](/documentation/developers-and-admins)
  * [Customization](/documentation/developers-and-admins/customization)
  * [Object types](/documentation/developers-and-admins/customization/object-types)
  * [Object type configuration](/documentation/developers-and-admins/customization/object-types/object-type-configuration)
  * Enable CI/CD for object types 


# Enable CI/CD for object types
The Xperience [CI/CD](/documentation/developers-and-admins/ci-cd) functionality allows you to serialize the data of objects from the database into XML files and then use a source control system to synchronize the files between different instances. Though the configuration options and API members covered on this page strongly imply relation to only Continuous Integration, the setup ensures support for **BOTH** Continuous Integration and Continuous Deployment. 
To configure CI/CD support for an object type:
  1. Edit the generated _Info_ class representing the object type.
**Important** : To use CI/CD, the code of your custom object type must be located in a separate assembly. See [Integrate custom code](/documentation/developers-and-admins/customization/integrate-custom-code) for more information.
  2. Set the `ContinuousIntegrationSettings` property in the initializer of the `TYPEINFO` member.
     * Set the `Enabled` property to `true`_._
     * (Optional) Set other [ContinuousIntegrationSettings properties](#continuousintegrationsettings) according to the requirements of your class’s data.
C#
Copy
```
// Enables Continuous Integration for the object type
ContinuousIntegrationSettings =
{
    Enabled = true
},
```

  3. (Optional) Set the `SerializationSettings` property of the `TYPEINFO` member. Use a nested initializer to configure the [SerializationSettings properties](#serializationsettings).
  4. Save the changes.


If Continuous Integration is enabled, the system now tracks all create, update and delete operations for objects of the given custom class and serializes the data into XML files in the repository folder. To transfer all existing data of the class into your repository, run [complete serialization for all objects](/documentation/developers-and-admins/ci-cd/continuous-integration#ContinuousIntegration-ciserialize).
You can also use your custom object type’s name (the value of the `OBJECT_TYPE` constant) when [filtering objects](/documentation/developers-and-admins/ci-cd/configure-ci-cd-repositories) within the repository configuration file.
## Deleting object types that have CI/CD enabled
Use the following process if you ever need to delete an object type with existing data and CI/CD enabled:
  1. First, delete the given type’s code files and rebuild the containing assembly (or remove the entire assembly if it is no longer needed).
  2. Delete the object type in the **Modules** application in the Xperience administration.
  3. Manually delete the folder representing the type’s data from the repository folder (the system automatically removes the data from the database but cannot clear the Continuous Integration data).


## References
The reference lists the `ContinuousIntegrationSettings`and `SerializationSettings` properties that are intended for public use. The classes also contain other members that are used internally or handled automatically by the system. We do not recommend working with any of the undocumented members.
### ContinuousIntegrationSettings
Property |  Type |  Description  
---|---|---  
Enabled |  bool |  Indicates whether the class is included in CI/CD (when serializing all objects to the file system and tracking object changes). [Child classes](/documentation/developers-and-admins/customization/object-types/object-type-configuration/model-object-type-relationships) are included only if the property is also enabled for the parent class.  
DependencyColumns |  ICollection<string> |  Defined as an _ICollection_ of column names. When the values of the specified columns are changed for an object, Continuous Integration reserializes the given object and all of its child or otherwise dependent objects. Only needs to be set for classes that use non-standard columns to determine the identity of objects. The object type’s **code name** , **GUID** , **parent ID** , and **group ID** columns are included by default.  
ObjectFileNameFields |  ICollection<string> |  Overrides the names of the XML files containing the serialized data of the class’s objects within the repository folder. By default, the XML files are named according to the value stored in `CodeNameColumn`. For object types without a code name column, the value stored in `GuidColumn` is used instead. The property is defined as an `ICollection` of column names that the system combines into the names of the serialized XML files. Underscores ( _ ) are used as separators between the values of the specified columns. **Note** : For CI/CD to work correctly, the combination of columns must be unique within the appropriate scope (within the objects under a specific parent, etc.).  
ObjectIdentificationFields |  ICollection<string>  
|  Defined as an `ICollection` of field names used for object identification in a database when the `CodeNameColumn` is not available. The specified fields are used in a where condition which searches for an existing record in the target database during the CI/CD restore process. Set this property for objects that don’t have a code name column (e.g., permissions set in the [Role management](/documentation/developers-and-admins/configuration/users/role-management)) that could be used to match objects between the source and target instances during the CD restore process.  
ObjectTypeFolderName |  string |  Sets the name of the folder that stores the XML files containing the serialized data of the class’s objects within the repository folder. If not specified, the object type name (`ObjectTypeInfo.ObjectType`) is used.  
SeparatedFields |  IEnumerable<SeparatedField> |  Identifies columns that CI/CD stores into separate files when serializing objects to the file system. The separated file is created within the same folder as the main XML file holding the remaining serialized data of the object. Define separated columns within an `IEnumerable` collection of `SeparatedField` objects. For each `SeparatedField` object, specify the name of the column as the constructor parameter. You can configure additional settings for the separated file through the properties of the object. **Note** : If the class has a column that stores binary data (specified in `ObjectTypeInfo.BinaryColumn`), the system includes the column in `SeparatedFields` by default. Manually setting the `SeparatedFields` property overrides the default separation settings for `BinaryColumn`, but you can define a custom `SeparatedField` object for the given column.  
C#
**Example**
Copy
```
ContinuousIntegrationSettings =
{
    Enabled = true,

    // Adds a field that CI/CD serializes into a separate file (in addition to the BinaryColumn field)
    SeparatedFields = new List<SeparatedField>
    {
        new SeparatedField("SeparatedField")
        {
            FileName = "CustomFileName",
            FileExtension = ".txt"
        }
    }
}
```

### SerializationSettings
The `SerializationSettings` property of `ObjectTypeInfo` determines how the system serializes class objects from the database into XML data.
Property |  Type |  Description  
---|---|---  
ExcludedFieldNames |  ICollection<string> |  Allows you to limit which fields are included when serializing objects of the given class to XML. Defined as an `ICollection` of field names that you wish to exclude from the serialization (blacklist). By default, the system automatically excludes the database columns identified by the following properties:
  * `IDColumn`
  * `TimeStampColumn` (“last modified” column)
  * `VersionGUIDColumn`
  * `BinaryColumn`

  
StructuredFields |  IEnumerable<IStructuredField> |  Identifies fields that contain structured data that cannot directly be serialized as inner text values of an XML element. The default implementation handles columns that store structured data as XML with simple values and a fixed order of elements. To work with custom formats or advanced XML schemas, you need to create an `IStructuredData` implementation that defines how the serialization occurs. To register custom data formats, use the generic variant of the `IStructuredField` class.   
C#
**Example**
Copy
```
SerializationSettings =
{
    // Adds two fields to the default collection of fields that are excluded from the serialized data
    ExcludedFieldNames = { "ClassField", "ClassSecondField" },

    // Ensures that the system processes "ClassXmlField" as nested XML in the serialized data
    StructuredFields = new IStructuredField[]
    {
        new StructuredField("ClassXmlField")
    }
}
```

![]()
[]()[]()
