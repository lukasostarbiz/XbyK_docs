# Remodel page types as reusable field schemas
  * How-to| [ Copy page link ](guides/development/upgrade-deep-dives/remodel-page-types-as-reusable-field-schemas#) | [Get HelpService ID](guides/development/upgrade-deep-dives/remodel-page-types-as-reusable-field-schemas#)
Core MVC 5


[✖](guides/development/upgrade-deep-dives/remodel-page-types-as-reusable-field-schemas# "Close page link panel") [Copy to clipboard](guides/development/upgrade-deep-dives/remodel-page-types-as-reusable-field-schemas#)
The [Xperience by Kentico: Kentico migration tool](https://github.com/Kentico/xperience-by-kentico-kentico-migration-tool) allows you to remodel your content on the fly during the upgrade process.
Using a [custom class mapping](https://github.com/Kentico/xperience-by-kentico-kentico-migration-tool/tree/master/Migration.Tool.Extensions#custom-class-mappings), you can discard fields, rename them, transform their values, and create [reusable field schemas](documentation/developers-and-admins/development/content-types/reusable-field-schemas) that you can share among multiple classes.
This guide will extract common fields from two page types from **Kentico Xperience 13 (KX13)** and move them to a _Reusable field schema_ shared by both web page content types in **Xperience by Kentico (XbyK)**.
## Set up the project
If you haven’t, we recommend following along with the [upgrade walkthrough](guides/architecture/upgrade-from-kx13/upgrade-walkthrough) to see the process of getting a migrated page working in Xperience by Kentico.
In preparation to follow along with this guide, you need:
  * A running instance of Kentico Xperience 13 with the Dancing Goat template
  * An instance of the [migration tool](https://github.com/Kentico/xperience-by-kentico-kentico-migration-tool)
  * A compatible install of Xperience by Kentico


If you haven’t followed along with the walkthrough, complete the steps from its [environment setup page](guides/architecture/upgrade-from-kx13/upgrade-walkthrough/setup-your-environment) in preparation for this guide.
## Determine the new model
As discussed in the [migration strategy planning guide](guides/architecture/upgrade-from-kx13/plan-your-strategy-for-migrating-features#structured-content-page-types), **page types** in KX13 map to **content types** in XbyK.
The migration tool automatically converts page types to content types with the same fields, and converts their pages _web page items_ in the content tree of a [website channel](documentation/developers-and-admins/configuration/website-channel-management). However, you can extend the process for specific page types, making structural adjustments as the tool converts them into content types, or changing where and how they are stored.
### Examine the source classes
In the KX13 Dancing Goat site, there are two [page types](13/developing-websites/defining-website-content-structure/managing-page-types/creating-page-types) corresponding to coffee grinders: _Manual grinder_ and _Electric grinder_.
The _Manual grinder_ page type has the following fields:
  * ManualGrinderID
  * ManualGrinderPromotionTitle
  * ManualGrinderPromotionDescription
  * ManualGrinderBannerText


These are the _Electric grinder_ fields:
  * ElectricGrinderID
  * ElectricGrinderPower
  * ElectricGrinderPromotionTitle
  * ElectricGrinderPromotionDescription
  * ElectricGrinderBannerText


### Consolidate the common fields
As both page types contain fields for a promotion title, promotion description, and banner text, we can move these fields to a shared schema.
Each content type in XbyK will need its own ID, and the electric grinder content type will need a power field that is not part of the schema.
[![Diagram of grinder reusable field schema](docsassets/guides/remodel-page-types-as-reusable-field-schemas/RFSUpgrade.jpg)](https://docs.kentico.com/docsassets/guides/remodel-page-types-as-reusable-field-schemas/RFSUpgrade.jpg)
## Implement the class mapping
### Add a mapping class
In your local version of the migration tool repository, add a new file to the _ClassMappings_ folder of the _Migration.Tool.Extensions_ project.
C#
**GrinderClassMapping.cs**
Copy
```
using CMS.DataEngine;
using Microsoft.Extensions.DependencyInjection;
using Migration.Tool.Common.Builders;

namespace Migration.Tool.Extensions.ClassMappings;

public static class GrinderClassMapping
{

}
...
```

### Define string constants for names
Looking over the [class mapping sample](https://github.com/Kentico/xperience-by-kentico-kentico-migration-tool/blob/master/Migration.Tool.Extensions/ClassMappings/ClassMappingSample.cs) from the migration tool, you can see that the names of classes and fields are often used multiple times. For example, a schema’s name is used when defining the schema and when assigning it to a content type.
For the sake of keeping everything in one place, let’s use constants for the string values we’ll need in this class, even if they are only used once.
This example uses unusual casing in the constant names for the sake of legibility, but you can adjust it to your preferences.
C#
**GrinderClassMapping.cs**
Copy
```
...
public static class GrinderClassMapping
{
    // Source class names
    private const string Source_ClassName_Electric = "DancingGoatCore.ElectricGrinder";
    private const string Source_ClassName_Manual = "DancingGoatCore.ManualGrinder";

    // Source class field names
    private const string Source_FieldName_Electric_PromotionTitle = "ElectricGrinderPromotionTitle";
    private const string Source_FieldName_Electric_PromotionDescription = "ElectricGrinderPromotionDescription";
    private const string Source_FieldName_Electric_BannerText = "ElectricGrinderBannerText";
    private const string Source_FieldName_Electric_Power = "ElectricGrinderPower";
    private const string Source_FieldName_Manual_PromotionTitle = "ManualGrinderPromotionTitle";
    private const string Source_FieldName_Manual_PromotionDescription = "ManualGrinderPromotionDescription";
    private const string Source_FieldName_Manual_BannerText = "ManualGrinderBannerText";

    // Target class names
    private const string Target_ClassName_Schema = "DancingGoat.Grinder";
    private const string Target_ClassName_Electric = "DancingGoat.ElectricGrinder";
    private const string Target_ClassName_Manual = "DancingGoat.ManualGrinder";

    // Target class GUIDs
    public const string Target_ClassGuid_Electric = "5A97ED6D-8A5A-415C-A464-A257298E6724";
    public const string Target_ClassGuid_Manual = "3C47B7BB-E86C-40A9-8B61-6A78A840D6AD";

    // Target class display names
    private const string Target_DisplayName_Schema = "Common Grinder Fields";
    private const string Target_DisplayName_Electric = "Electric Grinder";
    private const string Target_DisplayName_Manual = "Manual Grinder";

    // Target class schema descriptions
    private const string Target_Description_Schema = "Reusable schema that defines common grinder fields";

    // Target class table names
    private const string Target_TableName_Electric = "DancingGoat_ElectricGrinder";
    private const string Target_TableName_Manual = "DancingGoat_ManualGrinder";

    // Target class field names
    private const string Target_FieldName_Schema_PromotionTitle = "GrinderPromotionTitle";
    private const string Target_FieldName_Schema_PromotionDescription = "GrinderPromotionDescription";
    private const string Target_FieldName_Schema_BannerText = "GrinderBannerText";
    private const string Target_FieldName_Electric_Power = "ElectricGrinderPower";
    private const string Target_FieldName_Electric_ID = "ElectricGrinderID";
    private const string Target_FieldName_Manual_ID = "ManualGrinderID";

    ...
```

With these constants in place, we can begin to define the classes and mappings.
### Create the schema
Let’s start with the reusable field schema that will hold data common to both electric and manual grinders.
We can use the `IReusableSchemaBuilder.ConvertFrom` method to take the fields from the _Manual grinder_ page type and rename them with the `transformFieldName` parameter.
C#
**GrinderClassMapping.cs**
Copy
```
// Build schema for shared grinder fields
private static ReusableSchemaBuilder BuildSchema()
{
    // Build schema for shared grinder fields
    var schemaBuilder = new ReusableSchemaBuilder(Target_ClassName_Schema, Target_DisplayName_Schema, Target_Description_Schema);
    schemaBuilder.ConvertFrom(Source_ClassName_Manual, x => x switch
        {
            Source_FieldName_Manual_PromotionTitle => Target_FieldName_Schema_PromotionTitle,
            Source_FieldName_Manual_PromotionDescription => Target_FieldName_Schema_PromotionDescription,
            Source_FieldName_Manual_BannerText => Target_FieldName_Schema_BannerText,
            _ => null
        }
    );

    return schemaBuilder;
}
```

If you don’t want to copy an entire class as a schema, individual fields defined during the migration can copy their details from fields in the source page type using the `CreateFrom` method.
C#
**Schema field defined with CreateFrom**
Copy
```
schemaBuilder
    .BuildField(Target_FieldName_Schema_PromotionTitle)
    .CreateFrom(Source_ClassName_Manual, Source_FieldName_Manual_PromotionTitle);
```

If you need more control over each field’s attributes, you can define them manually.
C#
**Manually defined schema field**
Copy
```
schemaBuilder
    .BuildField(Target_FieldName_Schema_PromotionTitle)
    .WithFactory(() => new FormFieldInfo
    {
        Name = Target_FieldName_Schema_PromotionTitle,
        Caption = Target_FieldDisplayName_Schema_PromotionTitle,
        Guid = new Guid(Target_FieldGuid_Schema_PromotionTitle),
        DataType = FieldDataType.Text,
        Size = 200,
        Settings =
        {
            [Target_Setting_Schema_ControlName] = FormComponents.AdminTextInputComponent
        }
    });
```

### Map the electric grinder content type
Next, let’s move on to the _Electric grinder_ content type.
Start by defining the essential class attributes corresponding to fields in the `CMS_Class` table, like the name, display name, and table name.
Add a primary key field and assign the reusable field schema to the content type.
Next, assign values to the corresponding schema fields.
Finally, create a new field for the grinder’s _power_ using the KX13 field as a template.
**Use KX13 fields as templates**
The Kentico migration tool allows you to define content type fields on the fly as you map them.
For the schema fields used by the _Electric grinder_ content type, we defined their attributes manually in the _schema builder_ , then simply assigned them to the _Electric grinder_ content type as we mapped data from KX13. However, we still need to define attributes of the _power_ field.
Some migration tool methods, such as `SetFrom`, include an optional `isTemplate` parameter. The parameter lets you specify if the tool should replicate the KX13 field definition for the XbyK content type.
If `isTemplate` is set to `true`, the tool will transfer KX13 field information like the caption, data type, and default value to the XbyK content type. Otherwise, it will only transfer values.
See the [class mapping sample file](https://github.com/Kentico/xperience-by-kentico-kentico-migration-tool/blob/master/Migration.Tool.Extensions/ClassMappings/ClassMappingSample.cs) for more examples.
C#
**GrinderClassMapping.cs**
Copy
```
...
// Migrate electric grinders
private static MultiClassMapping BuildElectricGrinderMapping()
{
    var mappingElectric = new MultiClassMapping(Target_ClassName_Electric, target =>
    {
        target.ClassName = Target_ClassName_Electric;
        target.ClassTableName = Target_TableName_Electric;
        target.ClassDisplayName = Target_DisplayName_Electric;
        target.ClassType = ClassType.CONTENT_TYPE;
        target.ClassContentTypeType = ClassContentTypeType.WEBSITE;
    });

    mappingElectric.BuildField(Target_FieldName_Electric_ID).AsPrimaryKey();

    mappingElectric.UseResusableSchema(Target_ClassName_Schema);

    mappingElectric.BuildField(Target_FieldName_Schema_PromotionTitle)
        .SetFrom(Source_ClassName_Electric, Source_FieldName_Electric_PromotionTitle);

    mappingElectric.BuildField(Target_FieldName_Schema_PromotionDescription)
        .SetFrom(Source_ClassName_Electric, Source_FieldName_Electric_PromotionDescription);

    mappingElectric.BuildField(Target_FieldName_Schema_BannerText)
        .SetFrom(Source_ClassName_Electric, Source_FieldName_Electric_BannerText);

    mappingElectric.BuildField(Target_FieldName_Electric_Power)
        .SetFrom(Source_ClassName_Electric, Source_FieldName_Electric_Power, true);

    return mappingElectric;

}
...
```

### Map the manual grinder content type
The process for the _Manual grinder_ type is very similar, except it has no additional fields outside the schema.
Due to a bug, certain older versions of the migration tool may require you to include a non-schema field in your content type, or have at least one field that uses the KX13 counterpart as a template.
If you experience errors related the data class of your XbyK content types during data migration, you may need to include something like the commented-out `testField` example in the following code. You can delete the field from XbyK after your successful data migration.
C#
**GrinderClassMapping.cs**
Copy
```
...
// Migrate manual grinders
private static MultiClassMapping BuildManualGrinderMapping()
{
    var mappingManual = new MultiClassMapping(Target_ClassName_Manual, target =>
    {
        target.ClassName = Target_ClassName_Manual;
        target.ClassTableName = Target_TableName_Manual;
        target.ClassDisplayName = Target_DisplayName_Manual;
        target.ClassType = ClassType.CONTENT_TYPE;
        target.ClassContentTypeType = ClassContentTypeType.WEBSITE;
    });

    mappingManual.BuildField(Target_FieldName_Manual_ID).AsPrimaryKey();

    mappingManual.UseResusableSchema(Target_ClassName_Schema);

    mappingManual.BuildField(Target_FieldName_Schema_PromotionTitle)
        .SetFrom(Source_ClassName_Manual, Source_FieldName_Manual_PromotionTitle);

    mappingManual.BuildField(Target_FieldName_Schema_PromotionDescription)
        .SetFrom(Source_ClassName_Manual, Source_FieldName_Manual_PromotionDescription);

    mappingManual.BuildField(Target_FieldName_Schema_BannerText)
        .SetFrom(Source_ClassName_Manual, Source_FieldName_Manual_BannerText);

    // Setting a non-schema field to the content type may be necessary in some older versions of the migration tool.
    // You can delete this field from XbyK after data migration is complete
    // var testField = mappingManual.BuildField("TestField");
    // testField.ConvertFrom(Source_ClassName_Manual, Source_FieldName_Manual_PromotionTitle, true,
    //     (value, context) => "TestValue - Delete this field in XbyK");
    // testField.WithFieldPatch(field => field.Caption = "Test - Delete this field in XbyK");

    return mappingManual;
}
...
```

### Register the mappings
Now that the classes are mapped, you need to register the mappings and the schema builder with the [dependency injection](https://learn.microsoft.com/en-us/dotnet/core/extensions/dependency-injection) container.
Create a `MigrateGrinders` extension method for `IServiceCollection` that calls the private methods we’ve built so far and registers the resulting objects as singletons.
Then, call this method from the `ServiceCollectionExtensions` class of the _Migration.Tools.Extensions_ project, which executes at startup.
C#
**GrinderClassMapping.cs**
Copy
```
...
public static IServiceCollection MigrateGrinders(this IServiceCollection serviceCollection)
{
    var schemaBuilder = BuildSchema();

    var mappingElectric = BuildElectricGrinderMapping();

    var mappingManual = BuildManualGrinderMapping();

    // Register with the DI container
    serviceCollection.AddSingleton<IClassMapping>(mappingElectric);
    serviceCollection.AddSingleton<IClassMapping>(mappingManual);

    serviceCollection.AddSingleton<IReusableSchemaBuilder>(schemaBuilder);

    return serviceCollection;
}
...
```

C#
**ServiceCollectionExtensions.cs**
Copy
```
...
using Migration.Tool.Extensions.ClassMappings;
...
public static class ServiceCollectionExtensions
{
    public static IServiceCollection UseCustomizations(this IServiceCollection services)
    {
        ...
        services.MigrateGrinders();
        ...
```

## See the results
Now, run the data migration and you will see that both grinder content types use the reusable `Grinder` schema.
Electric grinder:
[![Screenshot of migrated Electric grinder type](docsassets/guides/remodel-page-types-as-reusable-field-schemas/ElectricGrinder_Migrated.png)](https://docs.kentico.com/docsassets/guides/remodel-page-types-as-reusable-field-schemas/ElectricGrinder_Migrated.png)
Manual grinder:
[![Screenshot of migrated Manual grinder type](docsassets/guides/remodel-page-types-as-reusable-field-schemas/ManualGrinder_Migrated.png)](https://docs.kentico.com/docsassets/guides/remodel-page-types-as-reusable-field-schemas/ManualGrinder_Migrated.png)
**Page name fields**
You can see that the new content types include a _DocumentName_ field, corresponding to the field of the same name in KX13’s _CMS.Document_ class. The migration tool automatically adds this field to content types created from pages, to hold the un-altered display name from the KX13 page.
If you have no use for this field, you can delete it from your content types in Xperience by Kentico.
Certain versions of the migration tool may create multiple page name fields (_DocumentName_ and _DocumentName1_) when you use a KX13 page type for a schema, then convert it to a content type utilizing that schema. For example, this may happen to the _Manual grinder_ content type in this example.
You can simply delete the duplicate field in these cases.
## What’s next?
In [the next upgrade deep dive](guides/development/upgrade-deep-dives/speed-up-remodeling-with-ai), we’ll explore how agentic AI coding assistants can speed up remodeling scenarios like the example we just covered. [after that](guides/development/upgrade-deep-dives/optimize-images-during-upgrade), we’ll set up XbyK’s [automatic asset optimization](documentation/developers-and-admins/development/content-types#automatically-optimize-image-assets) for images stored as media library files and page attachments in KX13.
If you encounter any roadblocks during your own migration, or if you have ideas for a subject we haven’t covered, don’t hesitate to reach out to us through the **Send us feedback** button at the bottom of this page.