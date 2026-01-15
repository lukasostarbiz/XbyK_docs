# Content types
  * [ Copy page link ](documentation/developers-and-admins/development/content-types#) | [Get HelpService ID](documentation/developers-and-admins/development/content-types#)
Core MVC 5


[✖](documentation/developers-and-admins/development/content-types# "Close page link panel") [Copy to clipboard](documentation/developers-and-admins/development/content-types#)
A content type is a template that defines the data structure, functionality, and behavior for all content items that are based on it. When creating content types, you determine their:
  * Target usage ([reusable content](documentation/business-users/content-hub), [pages](documentation/business-users/website-content), [emails](documentation/business-users/digital-marketing/emails), [headless items](documentation/business-users/headless-content))
  * Fields (define the content model and data structure)
  * Editing form layout (the interface through which users create and modify the content items)


## Create content types
To create a new content type:
  1. Open the **Content types** application.
  2. Select **New content type**.
  3. Fill in the following values: 
     * **Content type name** – the name that is displayed to users in the administration interface.
     * **Code name**
       * **Namespace** – the namespace is a prefix that distinguishes between content types. For example, it can be used to separate content types created for different sites, identify content types imported as part of third-party modules, etc.
       * **Name** – a unique content type identifier, which is appended to the namespace.
**Important**
When creating content types, **_never_** use names beginning with an **_underscore_** (‘ ___ ’) character (e.g., ‘ __article_ ’) to avoid conflicts with system fields.
Always use a **_unique namespace_**. Never use the **cms** , **om** , **content** or **ContentItemData** prefixes. A recommended option is to use your company name or an abbreviation as the prefix. For example: _ACME_
     * **Icon** – an icon identifying the content type when creating items (pages, emails, etc.).
     * **Use for** – determines the target usage for the content type. 
       * _Reusable content_ – intended for reuse across multiple channels and channel types.
       * _Pages_ – used for [website channels](documentation/developers-and-admins/configuration/website-channel-management).
       * _Emails_ – used for [email](documentation/business-users/digital-marketing/emails) content.
       * _Headless items_ – used for [headless channels](documentation/developers-and-admins/configuration/headless-channel-management).
     * **Short code name** – automatically generated based on the full _Code name_. Serves as a unique identifier of the content type in scenarios and environments where special characters (underscores and periods) are not supported, for example when generating type and field names in the [GraphQL schema of headless channels](documentation/developers-and-admins/development/content-retrieval/retrieve-headless-content). If you encounter a name conflict in these scenarios, you can edit the short code name value on the _General_ tab of existing content types.
  4. **Save** the content type.


The system creates the content type and opens its configuration menu. 
## Configure created content types
When configuring content types, you determine their functionality and purpose.
You can:
  * [Add fields](documentation/developers-and-admins/development/content-types#add-fields) to a content type.
  * [Allow editors to link other content items](documentation/developers-and-admins/development/content-types#add-the-option-to-link-content-items).
  * [Allow editors to upload files](documentation/developers-and-admins/development/content-types#add-option-to-upload-files) to content items. 
    * [Configure mass asset upload](documentation/developers-and-admins/development/content-types#mass-asset-upload-configuration) for a content type.
  * [Allow editor to tag content](documentation/developers-and-admins/development/content-types#add-option-to-tag-content).


### Add fields
Content type fields are used to store structured data. Each field corresponds to a database column and is of a certain [data type](documentation/developers-and-admins/customization/field-editor/data-type-management). Developers access the content of fields from the code to format and display stored data.
Fields that use custom data types are not supported by the GraphQL API for [headless channels](documentation/developers-and-admins/configuration/headless-channel-management) and their values cannot be retrieved. Only built-in scalar types are available in content types exposed through the API. See [Retrieve headless content](documentation/developers-and-admins/development/content-retrieval/retrieve-headless-content) to learn more about what is included in the GraphQL schema.
You can create content type fields via the [field editor](documentation/developers-and-admins/customization/field-editor) on the **Fields** tab when editing a content type in the **Content types** application:
  1. Select **New field**.
  2. Define a custom field using the field editor. 
     * There are a couple of things to keep in mind when naming fields – see [Field naming guidelines](documentation/developers-and-admins/development/content-types#field-naming-guidelines) for more information.
  3. **Save** the field.
  4. Repeat the above steps to create all desired fields.


Content items based on this content type now allow editors to input data into the added fields.
#### Field naming guidelines
Since each field is represented as a database column and eventually a C# class, adhere to the following restrictions when naming fields:
  * Don’t use the same name as another content type in the system.
  * Don’t use [C# keywords](https://learn.microsoft.com/en-us/dotnet/csharp/language-reference/keywords/) or [SQL keywords](https://en.wikipedia.org/wiki/List_of_SQL_reserved_words).
  * Don’t use the name _SystemFields_.


Not following these recommendation can result in unexpected behavior, runtime errors, or uncompilable code.
### Add the option to link content items
You can enable users to link content items from other reusable content items.
  1. In the **Content types** application, select the content type where you want to add the possibility to add linked content items.
  2. Create a **New field**.
  3. In **Data type** , select **Pages and reusable content** and fill in the required settings.
  4. In **Form component** , select the **Combined content selector**.
  5. In **Display items based on** , select whether you want to display items available in the selector based on **Content types** or a **Reusable field schema**.
  6. Add content types that are allowed to be linked via **Allowed content types** or select a **Allowed reusable field schema**.
  7. **Save** the field.


Now, whenever users edit a content item based on the content type, they can [link content items](documentation/business-users/content-hub/content-items#link-content-items) of allowed content types. You can then [retrieve](documentation/developers-and-admins/development/content-retrieval/retrieve-content-items#retrieve-linked-content-items) and use the linked items in your application’s code.
**Linked item limitations for emails**
Linked content items may have their own fields that link to further content items.
When modeling the fields of content types for [emails](documentation/business-users/digital-marketing/emails), the system has a **limit of 5 nested content item levels**. Further levels of linked items will not be loaded into the final email content.
### Add option to upload files
You can enable users to upload binary files to content items of a certain content type to create [content item assets](documentation/glossary#content-management). You can also [configure](documentation/developers-and-admins/configuration/content-hub-configuration#customize-maximum-file-upload-size-and-chunk-size) the parameters of the file upload (e.g., maximum file size).
  1. In the **Content types** application, select the **reusable content type** where you want to add the possibility to upload files.
  2. Create a **New field**.
  3. In **Data type** , select **Content item asset** and fill in the required settings.
  4. In **Form component** , select the **Asset uploader**.
  5. Configure **Allowed extensions**.
  6. **Save** the field.


Now, whenever users edit a content item based on the content type, they can [upload binary files](documentation/business-users/content-hub/content-item-assets) to the item to create a content item asset. You can then [retrieve](documentation/developers-and-admins/development/content-retrieval/retrieve-content-items#retrieve-assets) and use the assets in your application’s code.
#### Mass asset upload configuration
You can allow users to upload multiple files in a single bulk into the Content hub, combined content selector, or rich text editor, and automatically convert them to content item assets of a specific content type. The system only supports converting all mass-uploaded files to a single content type.
  1. In the **Content types** application, open the **Asset configurations - > Mass asset upload** tab.
  2. Select the **Content type for mass-uploaded items**. You can select only a content type used for reusable content that has at least one content item asset field configured.
  3. Select the **Content item asset field** of the content type which will store the uploaded file.
  4. **Save** the settings.


Users can now [mass upload assets](documentation/business-users/content-hub/content-item-assets#mass-asset-upload) into the Content hub, combined content selector, and [rich text editor](documentation/business-users/rich-text-editor).
### Add option to tag content
You can enable content editors to add [tags](documentation/developers-and-admins/configuration/taxonomies) to pages and reusable content items.
  1. In the **Content types** application, select a content type for which you want to enable the option to tag content.
  2. Create a **New field**.
  3. In **Data type** , select **Taxonomy** and fill the required settings.
  4. In **Form component** , select **Tag selector**.
  5. In **Taxonomy group** , select from which [taxonomy](documentation/developers-and-admins/configuration/taxonomies) users can select tags in this field.
  6. **Save** the field.


Now, whenever you edit a content item based on the content type, you are able to add tags to the content.
**Tip** : You can add multiple taxonomy fields to a single content type, each with a different set of tags.
## Configure asset content types
### Automatically optimize image assets
To ensure that your content editors work with images that are properly optimized for use across different channels, you can configure the system to automatically convert all newly created images to a single image format, and optimize their file size by adjusting image quality or image dimensions. While the system retains the original version of the image in case of a future re-optimization, the original version is not accessible from the administration interface.
  1. In the **Content types** application, select the content type for which you want to enable automatic image optimization.
  2. In the **Content item asset** field, select **Enable automatic image format conversion**.
  3. Select **Image extensions to convert**. You can choose only [optimizable image types](documentation/developers-and-admins/configuration/content-hub-configuration#customize-supported-file-types) that are [allowed](documentation/developers-and-admins/development/content-types#add-option-to-upload-files) for the particular content type.
  4. Select a single **Output image extension** for the converted images. If you are not sure which image format to use for the content item asset, see the [Image file type and format](https://developer.mozilla.org/en-US/docs/Web/Media/Formats/Image_types) guide.
  5. Select the **Quality** level of the converted images. 
     * Quality selection is unavailable for image formats that use lossless image compression, like _PNG_.
     * While higher quality retains more image details, lowering the quality reduces the output file size.
  6. _(Optional)_ In the **Image width limit** and **Image height limit** fields, you can set the maximum dimensions of the converted image. Images exceeding the set width or height limit are automatically resized to fit upon upload. 
Setting a very low image width or height limit can result in poor-quality [image variants](documentation/developers-and-admins/development/content-types#configure-image-variants) , as they will be generated from the downsized version of the original image. Make sure the limit is high enough to maintain visual quality across all variants.
  7. **Save** the field.


Upon upload, images with the selected **Image extensions to convert** are automatically converted to images with the **Output image extension** in the specified **Quality**. Images with other extensions are uploaded without any optimization.
We recommend [manually optimizing](documentation/business-users/content-hub/content-item-assets#optimize-image-assets) several sample images before enabling automatic optimization to fine-tune the optimization parameters for the best results.
If you notice undesired [compression artifacts](https://en.wikipedia.org/wiki/Compression_artifact) in the converted images, try increasing the quality until the artifacts are no longer visible.
### Configure image variants
Image variants are cropped and resized versions of an image, each defined by a specific aspect ratio and pixel dimensions. Variants allow marketers to control how images appear across different contexts, such as hero banners, social media posts, or thumbnails, and help developers address the [art direction](https://developer.mozilla.org/en-US/docs/Web/HTML/Responsive_images#art_direction) and [resolution switching](https://developer.mozilla.org/en-US/docs/Web/HTML/Guides/Responsive_images#how_do_you_create_responsive_images) problem.
Image variants are generated from the primary [optimized image](documentation/developers-and-admins/development/content-types#automatically-optimize-image-assets), taking into account its format and quality. The [usage](documentation/developers-and-admins/development/content-types#use-image-variants) of image variants is currently supported only for [website channels](documentation/business-users/website-content).
You can add two types of image variant definitions:
  * **Scaled variant** – Image dimensions are resized based on input while maintaining the original aspect ratio. If one dimension is set to 0, the system automatically calculates that dimension proportionally to preserve the aspect ratio. For example: 
    * If you define a variant with the size of **0 px × 200 px** and upload an image with the dimensions **600 px × 400 px** , the resulting image will have the dimensions **300 px × 200 px** , with the width calculated automatically to preserve the aspect ratio.
    * If you define a variant with the size of **1000 px × 100 px** and upload an image with the dimensions **500 px × 500 px** , the resulting image will have the dimensions **100 px × 100 px** , proportionally downscaled so that neither dimension exceeds the defined limits.
    * If you define a variant with the size of **1000 px × 800 px** and upload an image with the dimensions **500 px × 500 px** , the resulting image will have the dimensions **800 px × 800 px** , proportionally upscaled so that neither dimension exceeds the defined limits.
  * **Smart crop variant** – Images are cropped based on the focal point. Marketers can fine-tune the smart crop variants by [selecting a focal point](documentation/business-users/content-hub/content-item-assets#change-image-focal-point) to guide automatic cropping or by [adjusting the crop manually](documentation/business-users/content-hub/content-item-assets#crop-variants-manually). To ensure the image meets the specified dimensions, the system may upscale or downscale the final variant as needed.


The maximum supported number of image variant definitions is 63. Exceeding this limit may result in unexpected behavior.
When using _Scaled variants_ , images can be scaled up if the specified dimensions exceed the original image dimensions. This may result in blurry images or increased file sizes due to image upscaling. Developers who need to avoid upscaling should handle this behavior within their implementation.
To define a new variant for images in the system:
  1. In the **Content types** application, open the **Asset configurations → Image variants** tab.
  2. **Add new variant** definition.
  3. Select the variant type and fill in the variant’s properties.
  4. **Save** the changes.


All images in the system become available in the defined variant shortly, after the automatically triggered _Update asset variants_ [system scheduled task](documentation/developers-and-admins/customization/scheduled-tasks) completes. Users can then further [manage individual variants](documentation/business-users/content-hub/content-item-assets#image-variants) of their images.
Marketers typically won’t use every variant of every image. To prevent unnecessary storage use, the binary file for each variant is generated only when the variant is [accessed](documentation/developers-and-admins/development/content-retrieval/retrieve-content-items#retrieve-image-variants) for the first time.
#### Use image variants
To correctly display image variants on the pages of a [website channel](documentation/business-users/website-content), developers can either:
  * (Recommended) Use the `kxp-img`, `kxp-img-dimensions` and `kxp-img-variant` attribute Tag Helpers to render the image variant in your views. For more information on Tag Helper usage, see [Reference - Tag Helpers](documentation/developers-and-admins/development/reference-tag-helpers#attribute-tag-helpers-images). 
cshtml
Copy
```
@*
- kxp-img – Renders markup of the `src` or `srcset` attributes with
the image URL as their value. Accepts a ContentItemAsset object representing the image.
- kxp-img-dimensions – Renders markup of the `width` and `height`
attributes based on the dimensions of the image variant.
- kxp-img-variant – Specifies the code name of the image variant to be used.
*@

@addTagHelper Kentico.Content.Web.Mvc.ContentItemAssetImageTagHelper, Kentico.Content.Web.Mvc.ContentItemAssetPictureSourceTagHelper, Kentico.Content.Web.Mvc

@* Works well with both scaled and smart crop image variants *@
<img kxp-img="@Model.ContentItemAsset" kxp-img-dimensions kxp-img-variant="variantCodeName" ... />

@* Best used with smart-crop variants to address art direction scenarios *@
<picture>
    <source media="(min-width: 1024px)" kxp-img="@Model.ContentItemAsset" kxp-img-dimensions kxp-img-variant="variantCodeName1" ... />
    <source media="(min-width: 600px)" kxp-img="@Model.ContentItemAsset" kxp-img-dimensions kxp-img-variant="variantCodeName2" ... />
    <img kxp-img="@Model.ContentItemAsset" kxp-img-dimensions ... />
</picture>
```

  * Retrieve and use the variant’s URL and other properties in your views. For more information on image variant retrieval, see [Retrieve content items](documentation/developers-and-admins/development/content-retrieval/retrieve-content-items#retrieve-image-variants). 
C#
**Retrieve image variants**
Copy
```
// Retrieves a content item asset
// ...

// Accesses the asset object in the ImageFile property of the asset's content type
ContentItemAsset asset = myAsset.ImageFile;

// Accesses the properties of an image variant with the 'Hero' code name
if (asset.Metadata.Variants.ContainsKey("Hero")){
    int variantWidth = asset.Metadata.Variants["Hero"].Width;
    int variantHeight = asset.Metadata.Variants["Hero"].Height;
    string variantType = asset.metadata.Variants["Hero"].TransformationType;
    string variantUrl = asset.VariantUrls["Hero"];
}
```



#### Edit image variant definitions
To edit an image variant’s definition:
  1. In the **Content types** application, open the **Asset configurations → Image variants** tab.
  2. Edit the variant’s properties as needed.
  3. **Save** the changes.


The image variant is updated accordingly to its new definition in all images after the automatically triggered `Update asset variants` [system scheduled task](documentation/developers-and-admins/customization/scheduled-tasks) completes.
#### Delete image variant definitions
If a specific image variant should no longer be used in your project, you can remove its definition:
  1. In the **Content types** application, open the **Asset configurations → Image variants** tab.
  2. **Delete** (
  3. **Save** the changes.


After the image variant’s definition is deleted, the [variant itself](documentation/business-users/content-hub/content-item-assets#image-variants) remains visible when adjusting images but becomes read-only. Your developers should now update their code to no longer [use](documentation/developers-and-admins/development/content-retrieval/retrieve-content-items#retrieve-image-variants) the deleted variant.
## Work with content types in custom code
The system provides [code generation support](documentation/developers-and-admins/api/generate-code-files-for-system-objects) for objects that contain custom fields. The resulting generated classes are plain data transfer objects that work seamlessly with Xperience’s [data retrieval API](documentation/developers-and-admins/api/content-item-api).
Whenever you modify a content type’s fields (add or remove fields, change the field’s data type), you need to regenerate the corresponding classes using the code generator. This keeps the models consistent with the content type’s database definition.