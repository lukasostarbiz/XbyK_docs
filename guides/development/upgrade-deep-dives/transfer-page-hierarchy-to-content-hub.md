---
source: https://docs.kentico.com/guides/development/upgrade-deep-dives/transfer-page-hierarchy-to-content-hub
scrape_date: 2026-01-22
---

  * [Home](/guides)
  * [Development](/guides/development)
  * [Upgrade deep dives](/guides/development/upgrade-deep-dives)
  * Transfer parent-child page hierarchy to the Content hub 


# Transfer parent-child page hierarchy to the Content hub
When you upgrade from Kentico Xperience 13 (KX13) to Xperience by Kentico (XbyK), you’ll likely want to take advantage of the new features it offers, such as the [Content hub](/documentation/business-users/content-hub): a central repository in XbyK for managing reusable content across multiple channels.
However, the parent-child relationships of your pages in the content tree might be important for your use case.
Let’s configure the [Kentico migration tool](https://github.com/Kentico/xperience-by-kentico-kentico-migration-tool) to automatically preserve parent-child relationships when moving certain pages to the Content hub.
For this example, we’ll use the KX13 _Dancing Goat_ sample site, transferring the _Store_ section of the content tree to the Content hub in XbyK.
## Before you start
To follow along with the example in this guide, we recommend you have the following:
  * Familiarity with [C#](https://learn.microsoft.com/en-us/dotnet/csharp/) and [.NET Core](https://learn.microsoft.com/en-us/dotnet/).
  * A running instance of Xperience by Kentico, installed with the `kentico-xperience-mvc` template, preferably version [30.8.0](/documentation/changelog).
  * A running instance of Kentico Xperience 13 with the _Dancing Goat_ sample site, running on [Refresh 5](/13/installation/hotfix-instructions-xperience-13) or higher.
  * A local copy of the [Kentico migration tool](https://github.com/Kentico/xperience-by-kentico-kentico-migration-tool), version **3.12.0** , connected to each instance.


## Choose which page types to convert
Start by gathering information about the pages you wish to convert.
Since we want to work with all of the content types under the _Store_ page, we can query the KX13 database based on that node alias path:
SQL
**Class information query**
Copy
```
SELECT ClassID, ClassName, ClassDisplayName
FROM CMS_Class
WHERE ClassID IN
    (SELECT NodeClassID
     FROM CMS_Tree
     WHERE
     NodeAliasPath LIKE '/Store%')
```

This query lists all content types used in the _Store_ subtree, helping you identify which classes to migrate.
[![Screenshots of the result of the above query, listing the ID, name, and display name of page type classes from the /Store path](/docsassets/guides/transfer-page-hierarchy-to-content-hub/Page-type-query-result.png)](/docsassets/guides/transfer-page-hierarchy-to-content-hub/Page-type-query-result.png)
Now that we know which page types we want to use, we need to tell the migration tool to convert them to _reusable_ content types.
Switch to the **Migration.Tool.CLI** project and open its **appsettings.json** file. Here, you’ll find a `System` object, containing a `ConvertClassesToContentHub` property where you can add the code names of the classes you want to migrate. Let’s include all of them, except **Hot tips**.
**Hot tips** requires a field migration, which is outside the scope of this example. We’ll cover it in the future.
JSON
**appsettings.json**
Copy
```
{
  ...

  "Settings": {

    ...

    "ConvertClassesToContentHub": "DancingGoatCore.StoreSection, DancingGoatCore.ProductSection, DancingGoatCore.Tableware, DancingGoatCore.ManualGrinder, DancingGoatCore.ElectricGrinder, DancingGoatCore.FilterPack, DancingGoatCore.Brewer, DancingGoatCore.Coffee",

    ...
  }
}
```

## Examine page type relationships
So far, we’ve retrieved a list of page types from the _Store_ section and configured the migration tool to convert them to _reusable content types_. Now, let’s think about the conversion process.
For every page type whose pages may have children, we will need to tell the tool how to handle those children. First, let’s investigate where different types occur in the hierarchy.
To see which page types are used where, you can query the `CMS_Tree` table of the KX13 database, or simply look at the **Properties > General** tab of pages in the KX13 **Pages** application.
In the Dancing Goat example, you’ll find the following:
  * The _Store_ page is of the **Store section** type.
  * The direct children of the _Store_ page are of the **Product section** and **Hot tips**.
  * The remaining page types (**Tableware** , **Manual grinder** , **Electric grinder** , **Filter pack** , **Brewer** , and **Coffee**) belong to children of **Product section** pages.


## Create a director class
Next, let’s create a class to instruct the migration tool on handling items of these types.
In the **Migration.Tool.Extensions** project, create a new folder called **ContentItemDirectors**. Then, define a new class called `TrainingGuidesContentItemDirector` that inherits from `ContentItemDirectorBase`.
Override the `Direct` method, using the `LinkChildren` method of the provided `options` parameter to define a new content type field for holding children.
For the **Store section** page type, define a dedicated field for **Product section** children, and a catchall field for other children. Similarly, for **Product section** pages, create a “Products” field to hold the product page types we found earlier, and a catchall field for other children.
C#
**TrainingGuidesContentItemDirector.cs**
Copy
```
using Migration.Tool.Source.Mappers.ContentItemMapperDirectives;

namespace Migration.Tool.Extensions.ContentItemDirectors;

public class TrainingGuidesContentItemDirector : ContentItemDirectorBase
{
    public override void Direct(ContentItemSource source, IContentItemActionProvider options)
    {
        if (source.SourceClassName == "DancingGoatCore.StoreSection")
        {
            // Class ID of the product section class
            int productSectionClassId = 5508;

            options.LinkChildren("ProductSections", source.ChildNodes!.Where(x => x.NodeClassID == productSectionClassId));
            options.LinkChildren("Children", source.ChildNodes!.Where(x => x.NodeClassID != productSectionClassId));
        }
        if (source.SourceClassName == "DancingGoatCore.ProductSection")
        {
            // Class IDs of the product classes that appear in the store hierarchy
            int[] productClassIDs = {
                5503, // Tableware
                5516, // Manual grinder
                5521, // Electric grinder
                5522, // Filter pack
                5526, // Brewer
                5529 // Coffee
            };

            options.LinkChildren("Products", source.ChildNodes!.Where(x => productClassIDs.Contains(x.NodeClassID)));
            options.LinkChildren("Children", source.ChildNodes!.Where(x => !productClassIDs.Contains(x.NodeClassID)));
        }
    }
}
```

**Examine other filtering options**
In this simple example, we handled pages based on their page type, but you also have other options:
  * The `Direct` method provides access to many fields, such as the `SourceNode.NodeAliasPath`, allowing you to address pages based on their subtree or on an individual level. See the available fields in the [ICmsTree interface](https://github.com/Kentico/xperience-by-kentico-kentico-migration-tool/blob/5dcac65439f1ad9a7227b313f913a199f667a564/KVA/Migration.Tool.Source/Model/CmsTree.cs).
  * You can also sort the child items in a more granular way, based on the same `ICmsTree` fields.
  * Here, we sorted the children into mutually exclusive fields, but this is not necessary; you can reference the same child from multiple fields.


Remember to register the class with the migration _Migration.Tool.Extensions_ project’s dependency injection container.
C#
**ServiceCollectionExtensions.cs**
Copy
```
...
using Migration.Tool.Extensions.ContentItemDirectors;

namespace Migration.Tool.Extensions;

public static class ServiceCollectionExtensions
{
    public static IServiceCollection UseCustomizations(this IServiceCollection services)
    {
        ...

        services.AddTransient<ContentItemDirectorBase, TrainingGuidesContentItemDirector>();

        ...
 


```

## See the results
Run a migration that [includes pages](https://github.com/Kentico/xperience-by-kentico-kentico-migration-tool/blob/master/Migration.Tool.CLI/README.md#migrate-command-parameters) on the KX13 Dancing Goat site to observe the results.
Here is the command for full migration, for example:
CMD
**Full migration command**
Copy
```
.\Migration.Tool.CLI.exe  migrate --sites --custom-modules --users --settings-keys --page-types --pages --contact-management --forms --media-libraries --data-protection --custom-tables --members --categories
```

## Combine with other migration features
The director approach described in this example is compatible with the custom class mapping approach we’ve used in earlier upgrade deep dives. When you migrate your content tree hierarchy to the Content hub, you can still restructure the affected types with custom class mappings.
For example, let’s revisit the [grinder class mapping example](/guides/development/upgrade-deep-dives/remodel-page-types-as-reusable-field-schemas) from earlier in the upgrade deep dives. We simply need to change the `ClassContentTypeType` for each content type, and the other code can remain the same.
C#
**GrinderClassMapping.cs**
Copy
```
private static MultiClassMapping BuildElectricGrinderMapping()
{
    // Migrate electric grinders
    var mappingElectric = new MultiClassMapping(Target_ClassName_Electric, target =>
    {
        target.ClassName = Target_ClassName_Electric;
        target.ClassTableName = Target_TableName_Electric;
        target.ClassDisplayName = Target_DisplayName_Electric;
        target.ClassType = ClassType.CONTENT_TYPE;
        target.ClassContentTypeType = ClassContentTypeType.REUSABLE;
        target.ClassGUID = new Guid(Target_ClassGuid_Electric);
    });

    ...
}

private static MultiClassMapping BuildManualGrinderMapping()
{
    // Migrate manual grinders
    var mappingManual = new MultiClassMapping(Target_ClassName_Manual, target =>
    {
        target.ClassName = Target_ClassName_Manual;
        target.ClassTableName = Target_TableName_Manual;
        target.ClassDisplayName = Target_DisplayName_Manual;
        target.ClassType = ClassType.CONTENT_TYPE;
        target.ClassContentTypeType = ClassContentTypeType.REUSABLE;
        target.ClassGUID = new Guid(Target_ClassGuid_Manual);
    });

    ...
}

...
 



 


```

**Class mappings and ConvertClassesToContentHub**
If you set a content type to `ClassContentTypeType.REUSABLE` in a custom class mapping, the migration tool will put its items in the Content hub regardless of whether or not the KX13 page type appears in the `ConvertClassesToContentHub` value in **appsettings.json**.
However, if you map a KX13 page type to a content type with `ClassContentTypeType.WEBSITE` in a custom class mapping, you **must not** include its codename in the `ConvertClassesToContentHub` value in **appsettings.json** if you want the migration tool to create web page items in XbyK.
## Review the scenario
Let’s review the key points from this example:
  * Query the database to quickly find the classes located in a certain subtree, or use the **Properties → General** tab in the Pages app.
  * Specify which page types to migrate to Content hub in **appsettings.json** with `ConvertClassesToContentHub`.
  * Create a class that inherits from `ContentItemDirectorBase` to specify where child item references should go.
  * Filter both parent and child items by various attributes, like class name and tree location, to fully control the process.
  * Use `MultiClassMapping` objects in combination with the director to remodel classes as needed.


If you have any questions or upgrade scenarios you’d like us to cover in the future, please let us know with the “Send us feedback” button at the bottom of this page.
## What’s next?
If you haven’t already, we recommend checking out our [upgrade walkthrough](/guides/architecture/upgrade-from-kx13/upgrade-walkthrough), to see content retrieval migration in context of the whole upgrade process, including more caching examples.
You can find more advanced upgrade and data migration use cases in our other [Upgrade deep dive guides](/guides/development/upgrade-deep-dives).
![]()
[]()[]()
