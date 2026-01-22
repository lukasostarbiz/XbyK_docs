---
source: https://docs.kentico.com/guides/development/upgrade-deep-dives/optimize-images-during-upgrade
scrape_date: 2026-01-22
---

  * [Home](/guides)
  * [Development](/guides/development)
  * [Upgrade deep dives](/guides/development/upgrade-deep-dives)
  * Optimize images during your upgrade 


# Optimize images during your upgrade
Xperience by Kentico (XbyK) boasts powerful features for working with images.
With [image variants](/documentation/developers-and-admins/development/content-types#configure-image-variants), you can automatically generate cropped versions of image assets across the system at a specified size. [Automatic asset optimization](/documentation/developers-and-admins/development/content-types#automatically-optimize-image-assets) allows you to define a format, quality, and maximum dimensions to improve load times and consistency. These attributes are applied to each image uploaded to a given content type field, allowing you to tailor the specifications for each use case.
While _image variants_ can be generated at any time, _automatic image optimization_ applies to images the moment they are uploaded—a problem if you’ve already migrated thousands of media library files and attachments from Kentico Xperience 13 (KX13) to XbyK.
It’s possible to optimize already-uploaded images, but editors can only process each image individually, which is not scalable.
Let’s go over the three quick steps to optimize all your images during migration and save time for your team!
## Before you start
To follow along with the example in this guide, we recommend you have the following:
  * Familiarity with [C#](https://learn.microsoft.com/en-us/dotnet/csharp/) and [.NET Core](https://learn.microsoft.com/en-us/dotnet/).
  * A running instance of Xperience by Kentico, installed with the `kentico-xperience-mvc` template, preferably version [30.8.0](/documentation/changelog).
  * A running instance of Kentico Xperience 13 that contains page attachments and media files (such as the Dancing Goat sample site), running on [Refresh 5](/13/installation/hotfix-instructions-xperience-13) or higher.
  * A local copy of the [Kentico migration tool](https://github.com/Kentico/xperience-by-kentico-kentico-migration-tool), version **3.12.0** , connected to each instance.


## Decide your optimization parameters
Start by considering the parameters you want to set for image optimization.
  * Which types of images should Xperience convert to a new target format?
  * What format should Xperience convert the images to?
  * How much compression should Xperience apply to the images (if applicable to your selected target format)?
  * Should you set width and height limits? If so, what should they be?


For reference, go to the **Content types** application in a non-production instance of Xperience by Kentico and create a new reusable content type with a **Content item asset** field called _Asset_. The **Automatic image format conversion** properties of the **Asset uploader** form control will allow you to configure image optimization.
For our example, let’s convert the other supported formats to _WEBP_ and set the quality to _90%_ , meaning Xperience only applies a small degree of lossy compression to the images.
[![A screenshot of form component configuration of the new content type showing the conversion settings.](/docsassets/guides/optimize-images-during-upgrade/FieldConfiguration.png)](/docsassets/guides/optimize-images-during-upgrade/FieldConfiguration.png)
Since we’re going to apply these transformations to _all media files and attachments_ , which could have a wide variety of purposes and sizes, let’s not set maximum dimensions in this case.
We recommend ensuring that the target extension you choose is allowed in your KX13 site. Allowed extension settings are copied to XbyK during the upgrade by default, so you might see an error if the extension is not allowed.
If the extension is forbidden in KX13, you can change the setting in XbyK afterward.
Save this content type for reference. You can come back to delete it after we’ve finished setting up our migration code.
## Identify the correct format for your configurations
An interesting aspect of **Automatic image format conversion** is that, although these settings are configured through the **Asset uploader** form control assigned to a field, they still apply to images uploaded via API—not just those uploaded through the form control in the admin UI.
To apply these settings, we need to configure the form controls of the asset content types created by the migration tool. Let’s examine how the configurations we set in the UI are stored in the database, to see the proper format for this data.
In your preferred database manager, query the form definition of the dummy content type that you defined earlier:
SQL
**Form definition query**
Copy
```
SELECT [ClassFormDefinition]
FROM [CMS_Class]
WHERE [ClassName] = 'Test.Test' -- substitute your type's codename here
```

Copy the form definition, and optionally format the XML for readability in your preferred text processor or IDE.
Here, within the field you defined for the asset, you should be able to see the **code names of the properties** you configured in the UI, and **the format of their stored values**.
XML
**Form definition**
Copy
```
<form>
    <field column="ContentItemDataID" columntype="integer" enabled="true" guid="8259c0ed-ee0c-4236-ae2e-ca2d3635760b" isPK="true"/>
    <field column="ContentItemDataCommonDataID" columntype="integer" enabled="true" guid="4d4cd43c-51f9-402b-ba93-70c01d3f59b1" refobjtype="cms.contentitemcommondata" reftype="Required" system="true"/>
    <field column="ContentItemDataGUID" columntype="guid" enabled="true" guid="4af7f2c6-69da-4a13-90f8-22384ab8a843" isunique="true" system="true"/>
    <field allowempty="true" column="Asset" columnprecision="0" columntype="contentitemasset" enabled="true" guid="f4e1c06e-6d96-4536-b7c4-84faa9aaa27a" visible="true">
        <properties>
            <explanationtextashtml>False</explanationtextashtml>
            <fieldcaption>Asset</fieldcaption>
            <fielddescriptionashtml>False</fielddescriptionashtml>
        </properties>
        <settings>
            <AllowedExtensions>_INHERITED_</AllowedExtensions>
            <controlname>Kentico.Administration.ContentItemAssetUploader</controlname>
            <InputImageExtensions>["avif","jpeg","jpg","png"]</InputImageExtensions>
            <IsFormatConversionEnabled>True</IsFormatConversionEnabled>
            <OutputImageExtension>webp</OutputImageExtension>
            <OutputQuality>90</OutputQuality>
        </settings>
    </field>
</form>
 



 
 
 
 


```

## Configure the form control in code
So far, we’ve decided which parameters to set and learned how to structure their data. Now, let’s move on to the code.
In your local copy of the [Kentico migration tool](https://github.com/Kentico/xperience-by-kentico-kentico-migration-tool/), find the `AssetFacade` class in the **Migration.Tool.Source** project.
Here you can find definitions of the content types that correspond to KX13’s [attachments](/13/managing-website-content/working-with-files/page-attachments) and [media library files](/13/configuring-xperience/configuring-the-environment-for-content-editors/configuring-media-libraries). By default, they are called **Legacy attachment** and **Legacy media file** , and contain fields called `LegacyMediaFileAsset` and `LegacyAttachmentAsset` respectively.
You can _Ctrl+F_ to the definitions of each relevant field to see how it is defined:
  * `internal static readonly FormField LegacyMediaFileAssetField`
  * `internal static readonly FormField LegacyAttachmentAssetField`


Since we’re setting the same parameters for each content type in this example, define constants to hold both the names and values we saw in the form definition.
Then use the constants to set the appropriate values in the `CustomProperties` dictionary of each field.
C#
**AssetFacade.cs**
Copy
```
...

 // Shared string constants for automatic image optimization of assets
private const string InputImageExtensionsKey = "InputImageExtensions";
private const string IsFormatConversionEnabledKey = "IsFormatConversionEnabled";
private const string OutputImageExtensionKey = "OutputImageExtension";
private const string OutputQualityKey = "OutputQuality";

private const string InputImageExtensionsValue = "[\"jpeg\", \"jpg\", \"png\", \"avif\"]";
private const string IsFormatConversionEnabledValue = "True";
private const string OutputImageExtensionValue = "webp";
private const string OutputQualityValue = "90";

internal static readonly FormField LegacyMediaFileAssetField = new()
{
    Column = "LegacyMediaFileAsset",
    ColumnType = "contentitemasset",
    AllowEmpty = true,
    Visible = true,
    Enabled = true,
    Guid = new Guid("DFC3D011-8F63-43F6-9ED8-4B444333A1D0"),
    Properties = new FormFieldProperties { FieldCaption = "Asset", },
    Settings = new FormFieldSettings
    {
        CustomProperties = new Dictionary<string, object?>
        {
            { "AllowedExtensions", "_INHERITED_" },
            { InputImageExtensionsKey, InputImageExtensionsValue },
            { IsFormatConversionEnabledKey, IsFormatConversionEnabledValue },
            { OutputImageExtensionKey, OutputImageExtensionValue },
            { OutputQualityKey, OutputQualityValue }
        },
        ControlName = "Kentico.Administration.ContentItemAssetUploader"
    }
};

...

internal static readonly FormField LegacyAttachmentAssetField = new()
{
    Column = "LegacyAttachmentAsset",
    ColumnType = "contentitemasset",
    AllowEmpty = true,
    Visible = true,
    Enabled = true,
    Guid = new Guid("50C2BC4C-A8FF-46BA-95C2-0E74752D147F"),
    Properties = new FormFieldProperties { FieldCaption = "Asset", },
    Settings = new FormFieldSettings
    {
        CustomProperties = new Dictionary<string, object?>
        {
            { "AllowedExtensions", "_INHERITED_" },
            { InputImageExtensionsKey, InputImageExtensionsValue },
            { IsFormatConversionEnabledKey, IsFormatConversionEnabledValue },
            { OutputImageExtensionKey, OutputImageExtensionValue },
            { OutputQualityKey, OutputQualityValue }
        },
        ControlName = "Kentico.Administration.ContentItemAssetUploader"
    }
};

...
 
 
 
 
 
 
 
 
 



 
 
 
 



 
 
 
 


```

## See the result
With this code in place, the migration tool will use the settings you defined for the asset fields of **Legacy attachment** and **Legacy media file**.
Run a migration that [includes media libraries and pages](https://github.com/Kentico/xperience-by-kentico-kentico-migration-tool/blob/master/Migration.Tool.CLI/README.md#migrate-command-parameters), and you’ll see that page attachments and media library files that were other formats in KX13 have changed into the specified target format (_WebP_ in our example) with the specified quality level.
Here is the command for full migration, for example:
CMD
**Full migration command**
Copy
```
.\Migration.Tool.CLI.exe  migrate --sites --custom-modules --users --settings-keys --page-types --pages  --contact-management --forms --media-libraries --data-protection --custom-tables --members --categories
```

KX13:
[![Screenshot of an image in KX13](/docsassets/guides/optimize-images-during-upgrade/ImagePreConversion.png)](/docsassets/guides/optimize-images-during-upgrade/ImagePreConversion.png)
XbyK:
[![Screenshot of a converted image in XbyK](/docsassets/guides/optimize-images-during-upgrade/ImagePostConversion.png)](/docsassets/guides/optimize-images-during-upgrade/ImagePostConversion.png)
Now your images utilize the WebP format, helping reduce file sizes for faster loading.
![]()
[]()[]()
