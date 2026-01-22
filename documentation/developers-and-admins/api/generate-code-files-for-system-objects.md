---
source: https://docs.kentico.com/documentation/developers-and-admins/api/generate-code-files-for-system-objects
scrape_date: 2026-01-22
---

  * [Home](/documentation)
  * [Developers and admins](/documentation/developers-and-admins)
  * [API](/documentation/developers-and-admins/api)
  * Generate code files for system objects 


# Generate code files for system objects
Certain types of objects in the system, such as content types, can contain custom fields used to model and structure the content stored within them (e.g., a content type object with fields for a title, article summary, and article text). These custom fields are typically assigned via the [field editor](/documentation/developers-and-admins/customization/field-editor) or the [Form Builder](/documentation/developers-and-admins/development/builders/form-builder) editing interfaces and correspond to individual database columns.
Since each object type can have a different set of custom fields, providing strongly-typed access to these fields through a shared, general class is difficult. Consequently, accessing custom field data directly often requires additional code and can be unwieldy. Developers might need to:
  * Specify database column names explicitly when retrieving or manipulating data for particular fields.
  * Manually perform value type conversions (e.g., from `object` or `string`) and validation on the data as it comes from the database.


To simplify working with such objects, especially when retrieving data using the [ContentRetriever API](/documentation/developers-and-admins/api/content-item-api/content-retriever-api) or the [content item query API](/documentation/developers-and-admins/api/content-item-api/content-item-query-api), the system allows you to generate object-specific code files, often referred to as model classes.
A generated code file contains C# properties that directly correspond to the custom fields defined for the source object (e.g., **Title** , **ArticleSummary** , **ArticleText** properties for an article [content type](/documentation/developers-and-admins/development/content-types)). Each property is generated with an appropriate C# data type and getter/setter logic suitable for manipulating the data of the underlying field. This provides strongly-typed access, IntelliSense support, and eliminates the need for manual type casting or validation in many cases.
Additionally, using these generated model classes greatly simplifies content retrieval. When the result type specified in a retrieval method matches the generated class for the content being retrieved, the system’s default mapping logic can often automatically populate the properties of the generated class instance directly from the query results. This reduces the need to write custom mapping logic or manually extract values from the raw database data (`IContentQueryDataContainer` objects).
Code generation is available for the following objects:
  * [Reusable content types](/documentation/developers-and-admins/development/content-types) – generates a class with the reusable content type’s fields as properties. See [ContentRetriever API](/documentation/developers-and-admins/api/content-item-api/content-retriever-api) and [Content item API](/documentation/developers-and-admins/api/content-item-api/content-item-query-api) to learn about available API that can be used with generated content type classes.
  * [Page content types](/documentation/developers-and-admins/development/content-types) – generates a class with the page content type’s fields as properties. See [ContentRetriever API](/documentation/developers-and-admins/api/content-item-api/content-retriever-api) [Content item API](/documentation/developers-and-admins/api/content-item-api/content-item-query-api) to learn about available API that can be used with generated page classes.
  * [Email content types](/documentation/developers-and-admins/development/content-types) – generates a class with the email content type’s fields as properties. Xperience currently does not provide a general API for retrieving and working with emails. Classes generated for email content types are used when [retrieving the values](/documentation/developers-and-admins/development/builders/email-builder/develop-email-builder-components#email-fields) of an email’s fields within [Email Builder components](/documentation/developers-and-admins/development/builders/email-builder/develop-email-builder-components).
  * [Reusable field schemas](/documentation/developers-and-admins/development/content-types/reusable-field-schemas) – generates an interface that defines the corresponding schema’s fields as its properties.
  * [Forms](/documentation/developers-and-admins/development/builders/form-builder) – generates a class with the form’s fields as properties. See [Content retrieval](/documentation/developers-and-admins/development/content-retrieval) to learn about available API that can be used with generated form classes.
  * [Object types](/documentation/developers-and-admins/customization/object-types) – generates classes that facilitate working with object types. See [Database table API](/documentation/developers-and-admins/api/database-table-api) to learn about the architecture and available API of such objects. 
    * Code generation for object types can be further configured in the administration, see [Configure code generation for data classes](/documentation/developers-and-admins/customization/object-types#Generatecodefilesforsystemobjects-otcodegenconfig).


## Generate code files
The code generator is command line-based. Code files are generated via the `dotnet run --kxp-codegen` [.NET CLI](https://docs.microsoft.com/en-us/dotnet/core/tools/) command. You can run the generator from a command line interface of your choice (cmd, PowerShell, Bash).
Run the command from the root directory of your Xperience project:
CMD
Copy
```
cd my/Xperience/app/root
dotnet run --no-build -- --kxp-codegen --type "ReusableContentTypes"
```

**Command parameters**
Add the `--no-build` parameter to the [dotnet run](https://docs.microsoft.com/en-us/dotnet/core/tools/dotnet-run) command if you don’t wish to rebuild the project before the classes are generated.
Add the `--` syntax separator before any command parameters that you wish to pass directly to the Xperience application (`--kxp-codegen`). This prevents conflicts with general .NET CLI parameters, for example when using the `--help` option.
The code generator supports the following parameters. You can also view the supported parameters by running the generator with the `--help` option:
Parameter  |  Required |  Description  
---|---|---  
--type |  Yes |  Sets the type of objects for which the code files are generated. Available types: 
  * **Forms** – generates code files for forms.
  * **ReusableContentTypes** – generates code files for reusable content.
  * **PageContentTypes** – generates code files for pages.
  * **EmailContentTypes** – generates code files for emails.
  * **ReusableFieldSchemas** – generates code files for reusable field schemas. 
    * You must set the `--namespace` parameter when using this option.
    * Use together with the _ReusableContentTypes_ or _PageContentTypes_ option which generate classes that already implement all assigned reusable schema interfaces.
    * Make sure to generate schema interfaces into the same namespace as your content type classes. The generator outputs uncompilable code otherwise.
    * For more information, see [Reusable field schema interfaces](#reusable-field-schema-interfaces).
  * **Classes** – generates code files for object types. Code generation for object types can be further configured in the administration, see [Configure code generation for data classes](/documentation/developers-and-admins/customization/object-types#Generatecodefilesforsystemobjects-otcodegenconfig). 
    * Use together with the [–with-provider-class](#withProviderClass) parameter to generate the corresponding _*Provider_ class and interface.

The switches are case-sensitive. CMD **Example** Copy ```
cd my/Xperience/app/root
dotnet run -- --kxp-codegen --type "ReusableContentTypes"
```
  
--skip-confirmation |  No |  Skips the confirmation message displayed before the code files are generated.  
--include |  No |  Pattern to limit the included objects. Use ‘;’ to separate multiple object names. Use the ‘*’ wildcard character to match all objects with the same prefix. Reusable field schemas don’t use a namespace prefix, but you can use a shared code name prefix to allow bulk include.  For example: CMD Copy ```
dotnet run -- --kxp-codegen --type "ReusableContentTypes" --include "MyProject.Article;Acme.*"
```
  
--exclude |  No |  Pattern to define the excluded objects. Use ‘;’ to separate multiple object names. Use the ‘*’ wildcard character to match all objects with the same prefix. Reusable field schemas don’t use a namespace prefix, but you can use a shared code name prefix to allow bulk exclude. For example: CMD Copy ```
dotnet run -- --kxp-codegen --type "ReusableContentTypes" --exclude "MyProject.Article;Acme.*"
```
  
--location |  No |  The absolute or relative path of the target folder where the code files are generated. Path values support the following macros: 
  * **{type}** – replaced by a string representing the object type (e.g.  _ReusableContentTypes_).
  * **{dataClassNamespace}** – replaced by the namespace from the object name (e.g.  _Acme_ for _Acme.Article_). Resolves to an empty string when generating  _ReusableFieldSchemas_.
  * **{name}** – replaced by the object name without the namespace.

If the location is not provided, the code file target is the _/{type}/{dataClassNamespace}/{name}/_ folder hierarchy in the application directory.  
--namespace |  No |  The custom namespace added to the code of the generated classes. If not provided, a default system namespace is used based on the generated object type. The default values for the namespace of each type are the following:
  * **Forms** – _namespace CMS.OnlineForms.Types_
  * **ReusableContentTypes** – _namespace {dataClassNamespace}_
  * **PageContentTypes** – _namespace {dataClassNamespace}_
  * **EmailContentTypes** – _namespace {dataClassNamespace}_
  * **ReusableFieldSchemas** – no default namespace. Must be specified using the `--namespace` parameter.
  * **Classes** – _namespace {objectTypeName}_

When setting a custom namespace, you can use the following macros in the parameter value:
  * **{type}** – replaced by a string representing the object type (e.g.  _ReusableContentTypes_).
  * **{dataClassNamespace}** – replaced by the namespace from the object name (e.g.  _Acme_ for _Acme.Article_). Resolves to an empty string when generating  _ReusableFieldSchemas_.
  * **{name}** – replaced by the object name without the namespace.

  
--with-provider-class  |  No Supported values:  
`True`/`False` |  Applies only when generating object type classes (`--type Classes`). Controls whether a corresponding provider class `*Provider` and interface `I*Provider` is generated for the selected object types. Defaults to **False**. You must explicitly call `--with-provider-class True` to enable this behavior. We don’t recommend generating dedicated provider classes per object type. Instead, use the generic `IInfoProvider<TInfo>` API to inject Info managers in your code. See [Database table API](/documentation/developers-and-admins/api/database-table-api#info-class-managers) for more information. CMD **Example** Copy ```
# # Generates *Info classes only by default
dotnet run -- --kxp-codegen --type "Classes"
# Generates *Info classes along with provider classes and interfaces
dotnet run -- --kxp-codegen --type "Classes" --with-provider-class True
```
  
The system generates the code files in the specified location and folder structure.
### Store generated model classes
For smaller projects, you can store generated model classes directly within the main Xperience project.
If your project consists of multiple projects or [RCLs](https://learn.microsoft.com/en-us/aspnet/core/razor-pages/ui-class), we recommend that you create a separate _Class library_ project for storing all generated model classes, and then reference this project from your other projects. This allows you to keep the models together (model classes often reference each other via linking properties), and ensures that the models are accessible in your entire code base.
When using the generated code files in a separate library or external application, you need to allow the system to detect the classes. Make sure class discovery is enabled for the project using the `AssemblyDiscoverable` attribute – see [Integrate custom code](/documentation/developers-and-admins/customization/integrate-custom-code).
Don’t directly add generated code files into external projects that compile into a different output type than a DLL assembly (for example console applications). The system cannot discover the code in these cases.
## Reusable field schema interfaces
Code files generated via **ReusableContentTypes** or **PageContentTypes** for content types that use at least one [reusable field schema](/documentation/developers-and-admins/development/content-types/reusable-field-schemas) implement `I<SchemaName>` interfaces. Each such interface corresponds to a schema assigned to the given content type. You must generate these interfaces separately using the **ReusableFieldSchemas** `--type` parameter. Make sure to place them under the same namespace as the content type classes that use them. The generator outputs uncompilable code otherwise.
For example, assume an _Article_ page content type that uses the _PageMetadata_ and _SEO_ reusable field schemas.
To generate the page content type, run:
CMD
**Generate page content types**
Copy
```
dotnet run -- --kxp-codegen --type "PageContentTypes" --namespace "My.Project"
```

Which outputs:
C#
**Example generated class and its contents**
Copy
```
namespace My.Project;

public partial class Article : IPageMetadata, ISEO, IWebPageFieldsSource
{
    // Properties that represent the 'PageMetadata' and 'SEO' reusable schema fields

    // The SystemFields property

    // Properties that represent 'Article' content type fields
}
```

To generate the corresponding reusable schema interfaces, run:
CMD
Copy
```
dotnet run -- --kxp-codegen --type "ReusableFieldSchemas" --namespace "My.Project"
```

Which outputs:
C#
**IPageMetadata reusable schema interface**
Copy
```
namespace My.Project
{
    public interface IPageMetadata
    {
        public const string REUSABLE_FIELD_SCHEMA_NAME = "PageMetadata";

        // Properties that represent 'PageMetadata' reusable schema fields
    }
}
```

C#
**ISEO reusable schema interface**
Copy
```
namespace My.Project
{
    public interface ISEO
    {
        public const string REUSABLE_FIELD_SCHEMA_NAME = "SEO";

        // Properties that represent 'SEO' reusable schema fields
    }
}
```

### Configure model mapping for properties storing content item references to reusable field schemas
Content types can store references to other content items via fields of the _Pages and reusable content_ [data type](/documentation/developers-and-admins/customization/field-editor/data-type-management) and the [combined content selector](/documentation/developers-and-admins/customization/extend-the-administration-interface/ui-form-components/reference-admin-ui-form-components#combined-content-selector) component.
In content type model classes output by the code generator, these fields are represented as an `IEnumerable` collection of model classes that correspond to the content type which the field references. For example, assume the following content types:
  * Book – contains information about various books (ISIN, title, etc.).
  * Author – contains information about authors (name, bio, etc.) and a _Books_ field that references content items of the _Book_ content type (the books a given author has written).


Then the **Books** field, a reference from the _Author_ content type to the _Book_ content type, is in the generated model class represented as a property of the `IEnumerable<Book>` type.
If the target of the reference is **not** a specific content type but a [reusable field schema](/documentation/developers-and-admins/development/content-types/reusable-field-schemas) that can be used by multiple content types, the property representing the reference is a collection of [reusable field schema interface](#reusable-field-schema-interfaces) objects.
As an example, imagine a `PageMetadata` reusable field schema that declares fields shared by all page content types in the system. References to this schema are in generated model classes defined using the `IEnumerable<IPageMetadata>` type.
When retrieving content either via the [ContentRetriever](/documentation/developers-and-admins/api/content-item-api/reference-content-retriever-api) or [content item query](/documentation/developers-and-admins/api/content-item-api/content-item-query-api#run-queries-and-map-the-result) API and mapping the results to model classes that contain properties representing references to reusable field schemas, the system by default instantiates the referenced content items using the target reusable field schema interface type (`IPageMetadata`, for example).
You can override this default behavior and directly set which model class the system should use via the `MapContentTypeReferenceTo` and `MapReferenceTo` property-level attributes.
C#
**Specify model classes for automatic mapping**
Copy
```
// Maps content items of the 'Blog.BLOG_CLASS_NAME' content type from the collection
// of items referenced by the 'IPageMetadata' schema to the 'Blog' model class.
[MapContentTypeReferenceTo(Blog.BLOG_CLASS_NAME, typeof(Blog))]

// Maps content items of the 'Article.ARTICLE_CLASS_NAME' content type from the
// collection of items referenced by the 'IPageMetadata' schema to the 'Article' model class.
[MapContentTypeReferenceTo(Article.ARTICLE_CLASS_NAME, typeof(Article))]

// Map all other content items to the 'PageMetadata' model class
[MapReferenceTo(typeof(PageMetadata))]

// Property representing reusable field schema references
public IEnumerable<IPageMetadata> Pages { get; set; }

// A custom implementation of the 'IPageMetadata' interface containing
// a subset of the schema's properties.
public class PageMetadata : IPageMetadata
{
    public string PageMetadataTitle { get; set; }
}
```

If neither of these attributes is used, the system defaults first to the type of the property representing the reusable schema reference (`IPageMetadata` in the example above) and finally to the type registered via the [RegisterContentTypeMapping attribute](#registercontenttypemapping-attribute). This fallback chain is identical for both the ContentRetriever and content item query APIs.
## RegisterContentTypeMapping attribute
All generated model classes come annotated with the `RegisterContentTypeMapping` class-level attribute. The attribute controls which model classes get prioritized when the system maps content item database data to models.
C#
**RegisterContentTypeMapping example**
Copy
```
// Directs the system to use the annotated model class when model binding
// content items of the 'DancingGoat.Coffee' content type
[RegisterContentTypeMapping("DancingGoat.Coffee")]
```

## IContentItemFieldsSource interfaces
Classes generated for content types implement the following interfaces:
  * reusable content types – `IContentItemFieldsSource`
  * page content types – `IWebPageFieldsSource` (inherits from `IContentItemFieldsSource`)
  * email content types – `IEmailFieldsSource` (inherits from `IContentItemFieldsSource`)


These interfaces are useful when accessing system properties across multiple content types, or when defining extension methods across all generated classes.
C#
**Example - access system fields across multiple content types**
Copy
```
// Instance of the IContentRetriever service obtained via dependency injection
private readonly IContentRetriever contentRetriever;

public async Task<IEnumerable<IContentItemFieldsSource>> FetchMultipleContentTypes()
{
    // Gets multiple content types in a single query using IContentRetriever
    var contentTypes = new[] { Coffee.CONTENT_TYPE_NAME, Event.CONTENT_TYPE_NAME };

    // Uses 'IContentItemFieldsSource' as the shared type
    return await contentRetriever.RetrieveContentOfContentTypes<IContentItemFieldsSource>(contentTypes);
}
```

C#
**Example - extension method on IWebPageFieldsSource**
Copy
```
public static class WebPageFieldsSourceExtensionMethods
{
    public static object GetSystemProperties(this IWebPageFieldsSource source)
    {
        return new {
                       Guid = source.SystemFields.ContentItemGUID,
                       Id   = source.SystemFields.ContentItemID
                   };
    }
}
```

In most scenarios, we do **not recommend directly modifying the generated classes**.
The classes as generated as [partial classes](https://learn.microsoft.com/en-us/dotnet/csharp/programming-guide/classes-and-structs/partial-classes-and-methods). This means that you can extend them in a separate code file. Using this approach, you avoid the need to merge custom changes made to the generated code every time the class is generated again after the object’s properties are changed.
### Example
C#
**Store.generated.cs - generated class**
Copy
```
namespace DancingGoat
{
    // Partial class generated by the code generator for content type 'DancingGoat.Store'
    public partial class Store : IWebPageFieldsSource
    {
        // ...

        /// <summary>
        /// Store name.
        /// </summary>
        public string StoreName { get; set; }

         /// <summary>
        /// Store location.
        /// </summary>
        public string StoreLocation { get; set; }

        // ...
    }
}
```

C#
**Store.cs - extending the generated class**
Copy
```
namespace DancingGoat
{
    // Partial class extending the generated 'Store' class
    public partial class Store
    {
        public string StoreFullName { get => $"{StoreName}-{StoreLocation}" }
    }
}
```

![]()
[]()[]()
