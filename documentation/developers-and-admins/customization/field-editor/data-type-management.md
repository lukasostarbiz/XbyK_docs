---
source: https://docs.kentico.com/documentation/developers-and-admins/customization/field-editor/data-type-management
scrape_date: 2026-01-26
---

  * [Home](/documentation)
  * [Developers and admins](/documentation/developers-and-admins)
  * [Customization](/documentation/developers-and-admins/customization)
  * [Field editor](/documentation/developers-and-admins/customization/field-editor)
  * Data type management 


# Data type management
All data types assignable via the field editor must be explicitly registered. The system uses the metadata provided during registration to:
  * generate the database columns storing the values of individual fields,
  * configure code generator and field editor behavior,
  * perform value conversions (and object mapping for complex types) when fetching data to and from the database.


The following table highlights important types registered by default:
Field editor name |  C# data type |  Database representation |  Notes  
---|---|---|---  
Boolean (Yes/No) |  bool |  [bit](https://docs.microsoft.com/en-us/sql/t-sql/data-types/bit-transact-sql) |   
Integer number |  int |  [int](https://docs.microsoft.com/en-us/sql/t-sql/data-types/int-bigint-smallint-and-tinyint-transact-sql) |   
Long integer number |  long |  [bigint](https://docs.microsoft.com/en-us/sql/t-sql/data-types/int-bigint-smallint-and-tinyint-transact-sql) |   
Decimal number |  decimal |  [decimal](https://docs.microsoft.com/en-us/sql/t-sql/data-types/decimal-and-numeric-transact-sql) |   
Date |  DateTime |  [date](https://docs.microsoft.com/en-us/sql/t-sql/data-types/date-transact-sql) |   
Date and time |  DateTime |  [datetime2](https://docs.microsoft.com/en-us/sql/t-sql/data-types/datetime2-transact-sql) |  Time values are stored in the time zone of the server where the application is running. However, the administration UI automatically converts and displays time in the local time zone of each user, based on browser and environment settings.  
Time interval |  TimeSpan |  [time](https://docs.microsoft.com/en-us/sql/t-sql/data-types/time-transact-sql) |   
Text |  string |  [nvarchar](https://docs.microsoft.com/en-us/sql/t-sql/data-types/nchar-and-nvarchar-transact-sql) |  This data type is not intended to store rich text content (formatted text that includes links, e.g., created using the [rich text editor](/documentation/business-users/rich-text-editor)) – use _Rich text (HTML)_ data type instead. Database field length is configurable when creating or editing fields (up to the database maximum).  
Long text |  string |  [nvarchar(max)](https://docs.microsoft.com/en-us/sql/t-sql/data-types/nchar-and-nvarchar-transact-sql) |  This data type is not intended to store rich text content (formatted text that includes links, e.g., created using the [rich text editor](/documentation/business-users/rich-text-editor)) – use _Rich text (HTML)_ data type instead.  
Rich text (HTML) |  string |  [nvarchar(max)](https://docs.microsoft.com/en-us/sql/t-sql/data-types/nchar-and-nvarchar-transact-sql) |  Represents rich text content created with the [rich text editor](/documentation/business-users/rich-text-editor). This data type also ensures that usage of content items is tracked correctly.  
Unique identifier (GUID) |  Guid |  [uniqueidentifier](https://docs.microsoft.com/en-us/sql/t-sql/data-types/uniqueidentifier-transact-sql) |   
Content item asset |  ContentItemAssetMetadata |  [nvarchar(max)](https://docs.microsoft.com/en-us/sql/t-sql/data-types/nchar-and-nvarchar-transact-sql) |  Binary data attached to a content item.   
Pages and reusable content |  IEnumerable<ContentItemReference> |  [nvarchar(max)](https://docs.microsoft.com/en-us/sql/t-sql/data-types/nchar-and-nvarchar-transact-sql) |  Represents a collection of [content items](/documentation/business-users/content-hub) and [pages](/documentation/business-users/website-content) referenced by a specific content type field.  
Media files |  IEnumerable<AssetRelatedItem> |  [nvarchar(max)](https://docs.microsoft.com/en-us/sql/t-sql/data-types/nchar-and-nvarchar-transact-sql) |  Represents a collection of media files. For example, chosen by the [media file selector component](/documentation/developers-and-admins/customization/extend-the-administration-interface/ui-form-components/reference-admin-ui-form-components).  
Emails |  IEnumerable<EmailRelatedItem> |  [nvarchar(max)](https://docs.microsoft.com/en-us/sql/t-sql/data-types/nchar-and-nvarchar-transact-sql) |  Represents a collection of emails from an email channel, referenced by a specific field. For example, chosen by the [email selector component](/documentation/developers-and-admins/customization/extend-the-administration-interface/ui-form-components/reference-admin-ui-form-components).  
Pages |  IEnumerable<WebPageRelatedItem> |  [nvarchar(max)](https://docs.microsoft.com/en-us/sql/t-sql/data-types/nchar-and-nvarchar-transact-sql) |  Represents a collection of [pages](/documentation/business-users/website-content) from the content tree of a selected website channel. For example, chosen by the [page selector component](/documentation/developers-and-admins/customization/extend-the-administration-interface/ui-form-components/reference-admin-ui-form-components).  
Headless items |  IEnumerable<HeadlessItemReference> |  [nvarchar(max)](https://docs.microsoft.com/en-us/sql/t-sql/data-types/nchar-and-nvarchar-transact-sql) |  Represents a collection of [headless items](/documentation/business-users/headless-content) from a headless channel. For example, chosen by the headless item selector component. This data type is only available in fields of headless [content types](/documentation/developers-and-admins/development/content-types).  
Smart folder |  SmartFolderReference |  [uniqueidentifier](https://learn.microsoft.com/en-us/sql/t-sql/data-types/uniqueidentifier-transact-sql) |  Represents a [smart folder](/documentation/business-users/content-hub/content-hub-folders#smart-folders) selected via the [Smart folder selector component](/documentation/developers-and-admins/customization/extend-the-administration-interface/ui-form-components/reference-admin-ui-form-components#smart-folder-selector).  
Taxonomy |  IEnumerable<TagReference> |  [nvarchar(max)](https://docs.microsoft.com/en-us/sql/t-sql/data-types/nchar-and-nvarchar-transact-sql) |  Represents a collection of tags selected via the [tag selector component](/documentation/developers-and-admins/customization/extend-the-administration-interface/ui-form-components/reference-admin-ui-form-components#tag-selector).  
Object code names |  IEnumerable<string> |  [nvarchar(max)](https://docs.microsoft.com/en-us/sql/t-sql/data-types/nchar-and-nvarchar-transact-sql) |  Represents a collection of object code names selected via the [object code name selector component](/documentation/developers-and-admins/customization/extend-the-administration-interface/ui-form-components/reference-admin-ui-form-components).  
Object global identifiers |  IEnumerable<Guid> |  [nvarchar(max)](https://docs.microsoft.com/en-us/sql/t-sql/data-types/nchar-and-nvarchar-transact-sql) |  Represents a collection of object guids selected via the [object guid selector component](/documentation/developers-and-admins/customization/extend-the-administration-interface/ui-form-components/reference-admin-ui-form-components#object-selector).  
Note that the system doesn’t maintain the referential integrity of objects linked via the Pages and reusable content, Media files, Emails, Pages, and Object code names data types. Custom code must account for the referenced objects no longer existing or being otherwise invalid.
## Register custom data types
The default set of data types can be extended, which allows you to register additional data types into the system. To ensure that your data types are available in all scenarios, registration must be done [during application startup](/documentation/developers-and-admins/customization/run-code-on-application-startup) in the `OnPreInit` method of a module class.
Custom data types registered in the system are not supported by the GraphQL API for [headless channels](/documentation/developers-and-admins/configuration/headless-channel-management). Only built-in scalar types can be used in content types available through the API. For more information, see [Retrieve headless content](/documentation/developers-and-admins/development/content-retrieval/retrieve-headless-content).
To register a custom data type, call the `RegisterDataTypes(params DataType[])` method of the `DataTypeManager` class.
The `DataType<T>` class provides multiple constructors suitable depending on the complexity of the type being registered.
For primitive types and simple objects that can be directly serialized to the database, use
C#
Copy
```
DataType<T>(sqlType: string sqlType,
            fieldType: string fieldType,
            schemaType: string schemaType,
            conversionFunc: Func<object, T, CultureInfo, T> conversionFunc)
```

where:
  * **T** – generic substituted with the C# type of the data type being registered.
  * **sqlType** – the [SQL type](https://docs.microsoft.com/en-us/sql/t-sql/data-types/data-types-transact-sql) of the database column storing the values of the `DataType`.
  * **fieldType** – the string identifier of the data type in the system. Must be **lowercase**.
  * **schemaType** – the XSD data type (xs:string, xs:integer, etc.) used for XML serialization into `IDataContainer`.
  * **conversionFunc** – the function used to convert the stored database value to the data type. Must be static.


For composite types, use 
C#
Copy
```
DataType<T>(sqlType: string sqlType,
            fieldType: string fieldType,
            schemaType: string schemaType,
            conversionFunc: Func<object, T, CultureInfo, T> conversionFunc,
            dbConversionFunc: Func<T, object, CultureInfo, object> dbConversionFunc,
            textSerializer: IDataTypeTextSerializer textSerializer)
```

where:
  * **T** – generic substituted with the C# type of the data type being registered.
  * **sqlType** – the [SQL type](https://docs.microsoft.com/en-us/sql/t-sql/data-types/data-types-transact-sql) of the database column storing the values of the DataType.
  * **fieldType** – the string identifier of the data type in the system. Must be **lowercase**.
  * **schemaType** – the XSD data type (`xs:string`, `xs:integer`, etc.) used for XML serialization into `IDataContainer`. For composite types, this is commonly set to **xs:string** , as you’re often storing a serialized object (e.g., as a JSON string).
  * **conversionFunc** – the function used to convert the stored database value to the data type. Must be static.
  * **dbConversionFunc** – the function used to convert the data type to its string representation for storage in the database (e.g., a JSON string). Must be static.


For example, the composite type `MyCompositeType`:
C#
Copy
```
public class MyCompositeType
{
    public int Identifier { get; set; }
    public string Name { get; set; }
}
```

would use the following registration:
C#
Copy
```
// Called during module pre-initialization on application startup
protected override void OnPreInit()
{
    DataTypeManager.RegisterDataTypes(
        new DataType<MyCompositeType>("nvarchar(max)", "mydatatype", "xs:string",
                                    MyCompositeTypeHelper.ConvertToSystemRepresentation,
                                    MyCompositeTypeHelper.ConvertToDbRepresentation,
                                    new DefaultDataTypeTextSerializer("mydatatype"))
            {
                    // Data type configuration
                    // See the 'Data type configuration' section for details
            }
    );
}
```

C#
Copy
```
public static class MyCompositeTypeHelper
{
    public static object ConvertToDbRepresentation(MyCompositeType value, object defaultValue, CultureInfo _)
    {
        if (value == null)
        {
            return defaultValue;
        }

        try
        {
            return JsonSerializer.Serialize(value);
        }
        catch (Exception e)
        {
            throw new InvalidOperationException("An error occurred when serializing the data type.", e);
        }
    }

    public static MyCompositeType ConvertToSystemRepresentation(object value, MyCompositeType defaultValue, CultureInfo _)
    {
        if (value == null)
        {
            return defaultValue;
        }

        // We know the value is stored in the database as a JSON string
        var stringValue = value as string;
        if (string.IsNullOrEmpty(stringValue))
        {
            return defaultValue;
        }

        try
        {
            return JsonSerializer.Deserialize<MyCompositeType>(stringValue);
        }
        catch (Exception e)
        {
            throw new InvalidOperationException("An error occurred when deserializing the data type.", e);
        }
    }
}
```

The data type is now registered and can be assigned to object fields via the field editor. Note that to edit fields based on this data type, you also need to develop corresponding form components.
The following blog post contains a step-by-step implementation of a custom data type: [Embedded structured content and the power of custom data types](https://community.kentico.com/blog/embedded-structured-content-and-the-power-of-custom-data-types).
### Data type configuration
During data type registration, you can also configure the following properties that control the behavior of the system when working with the data type in code generators, the field editor, and database queries. 
**Field editor**
  * **Hidden** – indicates whether the data type is hidden from the field editor interface.
  * **HasConfigurableDefaultValue** – indicates whether the data type needs a configurable default value. Controls the visibility of the default value editor in the field editor interface. See [Assign default value editors for custom data types](#assign-default-value-editors-for-custom-data-types).
  * **IsAvailableForDataClass** – used to explicitly select data classes (specific content types or module classes) for which the data type is available. The following example ensures that the data type is available only for fields of content types and not for module classes:
C#
Copy
```
new DataType<MyCompositeType>("nvarchar(max)", "mydatatype", "xs:string",
                          MyCompositeTypeHelper.ConvertToSystemRepresentation,
                          MyCompositeTypeHelper.ConvertToDbRepresentation,
                          new DefaultDataTypeTextSerializer("mydatatype"))
{
    // Makes the data type available only if the data class represents a content type
    // You can add additional conditions here to restrict availability further
    IsAvailableForDataClass = dataClassInfo => dataClassInfo.IsContentType()
}
```



**Code generators**
  * **TypeAlias** – C# type assigned to properties generated from fields based on the data type.
  * **DefaultValueCode** – C# code for the default value of the data type. Provided as a string literal. For example: `"String.Empty"`


**General**
  * **DbType** – System.Data.SqlDbType used when generating database queries. Set to the same type as the data type’s sqlType parameter.
  * **VariableSize** – indicates that the type can have variable size. For example, _nvarchar(value)_. If set to true, the field editor interface also offers a _Size_ field that allows users to set the size of the underlying database column. 
    * **DefaultSize** – used when size isn’t provided via the field editor.
  * **VariablePrecision** – indicates that the type can have variable precision (e.g., floats). If set to true, the field editor interface also offers a _Precision_ field that allows users to set the precision of the underlying database column. 
    * **DefaultPrecision** – used when precision isn’t provided via the field editor.


## Assign default value editors for custom data types
Most data types assignable via the field editor provide a way to specify a default value for fields of that data type. For custom data types, you can assign a fitting UI form component via `RegisterDefaultValueComponent`. The method is available in admin module classes (derived from the `AdminModule` base class) – see [Prepare your environment for admin development](/documentation/developers-and-admins/customization/extend-the-administration-interface/prepare-your-environment-for-admin-development) for more information about admin modules.
The method takes the following parameters:
  * **dataType** – the string identifier of the data type.
  * **componentIdentifier** – the string identifier of the form component used as default value editor.
  * **Func <object, string> serializationFunction** – handles serialization of the form component’s underlying type to it’s string representation.
  * **Func <string, object> deserializationFunction** – handles deserialization from string into the form component’s underlying type.


Call the method during admin module initialization:
C#
Copy
```
using CMS.DataEngine;
using CMS.Helpers;

using Kentico.Xperience.Admin.Base;
using Kentico.Xperience.Admin.Base.Forms;

public class MyAdminModule : AdminModule
{
    public AdminBaseModule()
            : base("My.Xperience.Admin.Module")
    {
    }

    protected override void OnInit()
    {
        base.OnInit();

        // Assigns text input as the default value editor for "mydatatype"
        RegisterDefaultValueComponent("mydatatype", TextInputComponent.IDENTIFIER,
                                        ValidationHelper.GetValue<string>, value => ValidationHelper.GetValue<string>(value))
    }
}
```

![]()
[]()[]()
