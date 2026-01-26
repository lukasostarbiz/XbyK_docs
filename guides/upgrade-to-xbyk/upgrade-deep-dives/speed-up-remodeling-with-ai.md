---
source: https://docs.kentico.com/guides/upgrade-to-xbyk/upgrade-deep-dives/speed-up-remodeling-with-ai
scrape_date: 2026-01-26
---

  * [Home](/guides)
  * [Upgrade to Xperience by Kentico](/guides/upgrade-to-xbyk)
  * [Upgrade deep dives for developers](/guides/upgrade-to-xbyk/upgrade-deep-dives)
  * Speed up remodeling with AI 


# Speed up remodeling with AI
In many cases, your client or organization may want to make many changes during the upgrade to Xperience by Kentico (XbyK) to modernize the project’s content model and take advantage of new features.
When you need to restructure several classes, the process can be tedious, as you specify a mapping for every individual field in the new content model. For this type of monotonous work, AI coding assistants can come in handy.
In this example, we used [GitHub Copilot](https://github.com/features/copilot) in **Agent** mode with the **GPT-4.1** model to significantly speed up the migration of an _Article_ page type in Kentico Xperience 13 (KX13) to XbyK. The _Article_ page type included fields related directly to the article, as well as Open Graph metadata fields, which we wanted to move into a reusable field schema in XbyK.
## Before you start
To follow along with the example in this guide, we recommend you have the following:
  * Familiarity with [C#](https://learn.microsoft.com/en-us/dotnet/csharp/) and [.NET Core](https://learn.microsoft.com/en-us/dotnet/).
  * A running instance of Xperience by Kentico, installed with the `kentico-xperience-mvc` template, preferably version [30.8.0](/documentation/changelog).
  * A running instance of Kentico Xperience 13 that contains page attachments and media files (such as the Dancing Goat sample site), running on [Refresh 5](/13/installation/hotfix-instructions-xperience-13) or higher.
  * A local copy of the [Kentico migration tool](https://github.com/Kentico/xperience-by-kentico-kentico-migration-tool), version **3.12.0** , connected to each instance.
  * Access to an agentic AI coding assistant such as Cursor or GitHub Copilot.


## Give samples of the type of output you want
In our experience, AI coding assistants provide much better output when you provide them with an example of a similar finished product.
They excel in scenarios where you need to repeat the same process with different but analogous input each time.
In this case, we were able to provide Copilot with the existing class mapping from our [Remodel page types as reusable field schemas](/guides/upgrade-to-xbyk/upgrade-deep-dives/remodel-page-types-as-reusable-field-schemas) guide.
We told Copilot to pay special attention to this file using the **Add context** button in the chat window.
**Grinder schema example**
At the time we created this example, the Grinder class mapping manually defined its schema fields, rather than using `IReusableFieldSchema.ConvertFrom`. In this case, manually-defined schema fields are preferable, as the KX13 _Article_ page type includes several fields we do not want in our OG schema.
If you’re following along with the example, you can point your coding assistant to the `AddReusableSchemaIntegrationSample` example from [ClassMappingSample.cs](https://github.com/Kentico/xperience-by-kentico-kentico-migration-tool/blob/master/Migration.Tool.Extensions/ClassMappings/ClassMappingSample.cs), or provide the [original version of the Grinder class mapping](/docsassets/guides/speed-up-remodeling-with-ai/GrinderClassMapping.cs) as context.
You can also add the code from [the end of this guide](#see-the-results) as reference material.
In similar cases, it often helps to manually complete the first repetition of a process and provide its input and output to the coding assistant as context.
## Provide structured input
AI tools are most effective when working with data in a structured format, such as JSON or XML.
Luckily, data about the KX13 classes we want to remodel already exists in a structured format in the database.
The `CMS_Class` table has a column named `ClassFormDefinition` that holds the XML structure of the class’s properties and their form controls.
For this example, we were able to use this query to select the form definition of the article class from Dancing Goat:
SQL
**Class form definition query**
Copy
```
SELECT [ClassFormDefinition]
FROM [CMS_Class]
WHERE [ClassName] = 'DancingGoatCore.Article'
```

Here is the result, formatted for readability:
XML
**DancingGoatCore.Article**
Copy
```
<form version="2">
    <field column="ArticleID" columnprecision="0" columntype="integer" guid="61a0aa87-d18b-4949-b1d0-d7671fd6f526" isPK="true">
        <properties>
            <fieldcaption>ArticleID</fieldcaption>
        </properties>
        <settings>
            <controlname>labelcontrol</controlname>
        </settings>
    </field>
    <field allowempty="true" column="OGTitle" columnsize="90" columntype="text" dependsonanotherfield="true" guid="8e46aa5a-2776-4e48-85e4-2288fdfad9c5" visible="true">
        <properties>
            <fieldcaption>OG title</fieldcaption>
        </properties>
        <settings>
            <AutoCompleteEnableCaching>False</AutoCompleteEnableCaching>
            <AutoCompleteFirstRowSelected>False</AutoCompleteFirstRowSelected>
            <AutoCompleteShowOnlyCurrentWordInCompletionListItem>False</AutoCompleteShowOnlyCurrentWordInCompletionListItem>
            <controlname>TextBoxControl</controlname>
            <FilterMode>False</FilterMode>
            <Trim>False</Trim>
        </settings>
    </field>
    <field allowempty="true" column="OGDescription" columnsize="200" columntype="text" guid="277af268-3b5e-414b-85cc-61c7f86e43a6" visible="true">
        <properties>
            <fieldcaption>OG Description</fieldcaption>
        </properties>
        <settings>
            <AutoCompleteEnableCaching>False</AutoCompleteEnableCaching>
            <AutoCompleteFirstRowSelected>False</AutoCompleteFirstRowSelected>
            <AutoCompleteShowOnlyCurrentWordInCompletionListItem>False</AutoCompleteShowOnlyCurrentWordInCompletionListItem>
            <controlname>TextBoxControl</controlname>
            <FilterMode>False</FilterMode>
            <Trim>False</Trim>
        </settings>
    </field>
    <field allowempty="true" column="OGImage" columnsize="300" columntype="text" guid="c497ae17-2065-477e-bb50-31ea5ea74a73" visible="true">
        <properties>
            <fieldcaption>OG Image</fieldcaption>
        </properties>
        <settings>
            <controlname>MediaSelectionControl</controlname>
            <ShowPreview>True</ShowPreview>
        </settings>
    </field>
    <field column="ArticleTitle" columnsize="450" columntype="text" guid="df654142-a892-4806-868f-b78c22f1c2b8" visible="true">
        <properties>
            <defaultvalue ismacro="true">{% ArticleTitle %}</defaultvalue>
            <fieldcaption>Title</fieldcaption>
        </properties>
        <settings>
            <AutoCompleteEnableCaching>False</AutoCompleteEnableCaching>
            <AutoCompleteFirstRowSelected>False</AutoCompleteFirstRowSelected>
            <AutoCompleteShowOnlyCurrentWordInCompletionListItem>False</AutoCompleteShowOnlyCurrentWordInCompletionListItem>
            <controlname>TextBoxControl</controlname>
            <FilterMode>False</FilterMode>
            <Trim>False</Trim>
        </settings>
    </field>
    <field allowempty="true" column="ArticleTeaser" columntype="file" guid="decc0681-142f-488f-9e8a-0e55b09e3ba8" translatefield="true" visible="true">
        <properties>
            <fieldcaption>Teaser</fieldcaption>
        </properties>
        <settings>
            <Autoresize>custom</Autoresize>
            <Autoresize_Height>0</Autoresize_Height>
            <Autoresize_MaxSideSize>1000</Autoresize_MaxSideSize>
            <Autoresize_Width>0</Autoresize_Width>
            <controlname>DirectUploadControl</controlname>
            <Extensions>inherit</Extensions>
        </settings>
    </field>
    <field column="ArticleSummary" columnsize="190" columntype="text" guid="86ca0bd7-d483-45fd-a902-fe3a261d4a45" visible="true">
        <properties>
            <fieldcaption>Summary</fieldcaption>
        </properties>
        <settings>
            <AutoCompleteEnableCaching>False</AutoCompleteEnableCaching>
            <AutoCompleteFirstRowSelected>False</AutoCompleteFirstRowSelected>
            <AutoCompleteShowOnlyCurrentWordInCompletionListItem>False</AutoCompleteShowOnlyCurrentWordInCompletionListItem>
            <controlname>TextAreaControl</controlname>
            <FilterMode>False</FilterMode>
            <Rows>5</Rows>
            <Wrap>True</Wrap>
        </settings>
        <rules>
            <rule errormsg="{$basicform.errortexttoolong$}">{%Rule("(Value.Length &lt;= 190)", "&lt;rules&gt;&lt;r pos=\"0\" par=\"\" op=\"and\" n=\"MaxLength\" &gt;&lt;p n=\"maxlength\"&gt;&lt;t&gt;190&lt;/t&gt;&lt;v&gt;190&lt;/v&gt;&lt;r&gt;0&lt;/r&gt;&lt;d&gt;&lt;/d&gt;&lt;vt&gt;integer&lt;/vt&gt;&lt;tv&gt;0&lt;/tv&gt;&lt;/p&gt;&lt;/r&gt;&lt;/rules&gt;")|(identity)GlobalAdministrator|(hash)8122a99d4c158f82a9f6e88d9d95188995bb070d91f904fd641f0962cf47fda6%}</rule>
        </rules>
    </field>
    <field column="ArticleText" columntype="longtext" guid="85787b75-3609-48e6-8584-1d88b4a85499" visible="true">
        <properties>
            <fieldcaption>Text</fieldcaption>
        </properties>
        <settings>
            <controlname>HtmlAreaControl</controlname>
            <Dialogs_Content_Hide>False</Dialogs_Content_Hide>
            <HeightUnitType>PX</HeightUnitType>
            <MediaDialogConfiguration>True</MediaDialogConfiguration>
            <ShowAddStampButton>False</ShowAddStampButton>
            <ToolbarSet>Standard</ToolbarSet>
            <WidthUnitType>PX</WidthUnitType>
        </settings>
    </field>
    <field allowempty="true" column="ArticleRelatedArticles" columntype="docrelationships" guid="1415dcbe-42f8-460b-91b9-12e52695622f" visible="true">
        <properties>
            <fieldcaption>Related articles</fieldcaption>
        </properties>
        <settings>
            <controlname>Pages</controlname>
            <StartingPath>/Articles</StartingPath>
        </settings>
    </field>
</form>
```

In more complex examples, you can also provide the form definitions of other page types that you already remodeled, so the coding assistant can see both the input and output of a previous case.
## Mind the details in your prompt
When prompting the AI assistant for remodeling, be sure to provide a specific description of what to do, addressing each aspect of the source content type that you want the agent to treat differently.
In this example, we made sure to specify that fields related to Open Graph should go in the schema, while fields related directly to the Article would go directly into the content type. After the first round of code, we refined the prompt with additional specific details based on what the tool had missed.
Text
**Prompt**
Copy
```
Create a class mapping that migrates the Article page type to Xperience by Kentico, similar to the grinder class mapping in GrinderClassMapping.cs.

The Article page type has the following form definition:

```
(Paste the form definition of the Article page type here)
```

Separate OG-related fields into a reusable field schema that is assigned to the Article content type.

Keep Article related fields directly in the Article content type.

For article fields, set the `isTemplate` parameter of the `SetFrom` method to true.

Set the values of the schema fields while building the article class mapping, but without the `isTemplate` parameter

Use constants for string values, like the example in GrinderClassMapping.cs
```

This prompt produced code that only required minor refactoring and cleanup—something Copilot was also able to expedite.
Depending on your workflow, you may prefer to proceed one step at a time, asking your AI assistant to refine the generated code with individual refactors rather than starting from scratch with an adjusted prompt.
## See the results
Here is the final code we arrived at with this example, after some minor adjustments to the generated code:
C#
**Migration.Tool.Extensions/ClassMappings/ArticleClassMapping.cs**
Copy
```
using CMS.DataEngine;
using CMS.FormEngine;
using Microsoft.Extensions.DependencyInjection;
using Migration.Tool.Common.Builders;
using Migration.Tool.KXP.Api.Auxiliary;

namespace Migration.Tool.Extensions.ClassMappings;

public static class ArticleClassMapping
{
    // Source class name
    private const string Source_ClassName_Article = "DancingGoatCore.Article";

    // Source field names
    private const string Source_FieldName_OGTitle = "OGTitle";
    private const string Source_FieldName_OGDescription = "OGDescription";
    private const string Source_FieldName_OGImage = "OGImage";
    private const string Source_FieldName_ArticleTitle = "ArticleTitle";
    private const string Source_FieldName_ArticleTeaser = "ArticleTeaser";
    private const string Source_FieldName_ArticleSummary = "ArticleSummary";
    private const string Source_FieldName_ArticleText = "ArticleText";
    private const string Source_FieldName_ArticleRelatedArticles = "ArticleRelatedArticles";

    // Target schema/class names
    // Make schema name public in case we want to assign it to content types located in other files.
    public const string Target_ClassName_OGSchema = "DancingGoat.OGMetadata";
    private const string Target_ClassName_Article = "DancingGoat.Article";

    // Target display names
    private const string Target_DisplayName_OGSchema = "Open Graph Metadata";
    private const string Target_DisplayName_Article = "Article";

    // Target schema descriptions
    private const string Target_Description_OGSchema = "Reusable schema for OG metadata fields";

    // Target table names
    private const string Target_TableName_Article = "Article_ContentType";

    // Target field names
    private const string Target_FieldName_OGTitle = "OGSchemaTitle";
    private const string Target_FieldName_OGDescription = "OGSchemaDescription";
    private const string Target_FieldName_OGImage = "OGSchemaImage";
    private const string Target_FieldName_ArticleID = "ArticleID";
    private const string Target_FieldName_ArticleTitle = "ArticleTitle";
    private const string Target_FieldName_ArticleTeaser = "ArticleTeaser";
    private const string Target_FieldName_ArticleSummary = "ArticleSummary";
    private const string Target_FieldName_ArticleText = "ArticleText";
    private const string Target_FieldName_ArticleRelatedArticles = "ArticleRelatedArticles";

    // Target field display names
    private const string Target_FieldDisplayName_OGTitle = "OG title";
    private const string Target_FieldDisplayName_OGDescription = "OG Description";
    private const string Target_FieldDisplayName_OGImage = "OG Image";

    // Target field guids
    private const string Target_FieldGuid_OGTitle = "8E46AA5A-2776-4e48-85e4-2288fdfad9c5";
    private const string Target_FieldGuid_OGDescription = "277af268-3b5e-414b-85cc-61c7f86e43a6";
    private const string Target_FieldGuid_OGImage = "c497ae17-2065-477e-bb50-31ea5ea74a73";

    // Target field sizes
    private const int Target_FieldSize_OGTitle = 90;
    private const int Target_FieldSize_OGDescription = 200;

    // Setting names
    private const string Target_Setting_ControlName = "controlname";

    // Reusable field schema for OG metadata
    private static ReusableSchemaBuilder BuildSchema()
    {
        var schemaBuilder = new ReusableSchemaBuilder(Target_ClassName_OGSchema, Target_DisplayName_OGSchema, Target_Description_OGSchema);

        schemaBuilder
            .BuildField(Target_FieldName_OGTitle)
            .WithFactory(() => new FormFieldInfo
            {
                Name = Target_FieldName_OGTitle,
                Caption = Target_FieldDisplayName_OGTitle,
                Guid = new Guid(Target_FieldGuid_OGTitle),
                DataType = FieldDataType.Text,
                Size = Target_FieldSize_OGTitle,
                Settings =
                {
                    [Target_Setting_ControlName] = FormComponents.AdminTextInputComponent
                }
            });

        schemaBuilder
            .BuildField(Target_FieldName_OGDescription)
            .WithFactory(() => new FormFieldInfo
            {
                Name = Target_FieldName_OGDescription,
                Caption = Target_FieldDisplayName_OGDescription,
                Guid = new Guid(Target_FieldGuid_OGDescription),
                DataType = FieldDataType.Text,
                Size = Target_FieldSize_OGDescription,
                Settings =
                {
                    [Target_Setting_ControlName] = FormComponents.AdminTextInputComponent
                }
            });

        schemaBuilder
            .BuildField(Target_FieldName_OGImage)
            .WithFactory(() => new FormFieldInfo
            {
                Name = Target_FieldName_OGImage,
                Caption = Target_FieldDisplayName_OGImage,
                Guid = new Guid(Target_FieldGuid_OGImage),
                DataType = FieldDataType.ContentItemAsset,
                Settings =
                {
                    [Target_Setting_ControlName] = FormComponents.AdminAssetSelectorComponent,
                }
            });

        return schemaBuilder;
    }

    // Article content type mapping
    private static MultiClassMapping BuildArticleMapping()
    {
        var mapping = new MultiClassMapping(Target_ClassName_Article, target =>
        {
            target.ClassName = Target_ClassName_Article;
            target.ClassTableName = Target_TableName_Article;
            target.ClassDisplayName = Target_DisplayName_Article;
            target.ClassType = ClassType.CONTENT_TYPE;
            target.ClassContentTypeType = ClassContentTypeType.WEBSITE;
        });

        mapping.BuildField(Target_FieldName_ArticleID).AsPrimaryKey();

        // Assign schema to article
        mapping.UseResusableSchema(Target_ClassName_OGSchema);

        // Map article-specific fields
        mapping.BuildField(Target_FieldName_ArticleTitle)
            .SetFrom(Source_ClassName_Article, Source_FieldName_ArticleTitle, true);

        mapping.BuildField(Target_FieldName_ArticleTeaser)
            .SetFrom(Source_ClassName_Article, Source_FieldName_ArticleTeaser, true);

        mapping.BuildField(Target_FieldName_ArticleSummary)
            .SetFrom(Source_ClassName_Article, Source_FieldName_ArticleSummary, true);

        mapping.BuildField(Target_FieldName_ArticleText)
            .SetFrom(Source_ClassName_Article, Source_FieldName_ArticleText, true);

        mapping.BuildField(Target_FieldName_ArticleRelatedArticles)
            .SetFrom(Source_ClassName_Article, Source_FieldName_ArticleRelatedArticles, true);

        // Map schema fields
        mapping.BuildField(Target_FieldName_OGTitle)
            .SetFrom(Source_ClassName_Article, Source_FieldName_OGTitle);

        mapping.BuildField(Target_FieldName_OGDescription)
            .SetFrom(Source_ClassName_Article, Source_FieldName_OGDescription);

        mapping.BuildField(Target_FieldName_OGImage)
            .SetFrom(Source_ClassName_Article, Source_FieldName_OGImage);

        return mapping;
    }

    public static IServiceCollection MigrateArticles(this IServiceCollection serviceCollection)
    {
        // Prepare article mapping and schema builder
        var schemaBuilder = BuildSchema();
        var articleMapping = BuildArticleMapping();

        // Register the article mapping and schema builder
        serviceCollection.AddSingleton<IClassMapping>(articleMapping);
        serviceCollection.AddSingleton<IReusableSchemaBuilder>(schemaBuilder);

        return serviceCollection;
    }
}
```

As you can see, the code sample here follows the conventions of the [Grinder example](/guides/upgrade-to-xbyk/upgrade-deep-dives/remodel-page-types-as-reusable-field-schemas), with a large set of private constants to centralize and organize string values. Feel free to take a different approach based on your project’s conventions.
You can use an AI coding assistant to quickly refactor our sample code to match your project’s style, creating better reference material for your prompts.
We also made the name of the schema public, in case it needs to be applied to other class mappings outside of this file.
Once you register the mapper, the migration tool will utilize it when bringing the Article page type to XbyK.
C#
**Migration.Tool.Extensions/ServiceCollectionExtensions.cs**
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
        services.MigrateArticles();
        ...
```

## Build upon this example
For the sake of this example, we only created a custom mapping for a single page type, but you’ll likely encounter several page types that benefit from remodeling when you upgrade larger projects.
With each successful round of generation, you’ll have more context to provide your AI assistant, which will help improve the quality of output going forward and save you more time on current and future upgrades.
## What’s next?
In [our next guide](/guides/upgrade-to-xbyk/upgrade-deep-dives/transfer-page-hierarchy-to-content-hub), move pages to the Content hub creating automatic relationships to preserve parent-child structure.
![]()
[]()[]()
