# Content item assets
  * [ Copy page link ](documentation/business-users/content-hub/content-item-assets#) | [Get HelpService ID](documentation/business-users/content-hub/content-item-assets#)
Core MVC 5


[✖](documentation/business-users/content-hub/content-item-assets# "Close page link panel") [Copy to clipboard](documentation/business-users/content-hub/content-item-assets#)
_Content item assets_ are [content items](documentation/business-users/content-hub/content-items) of certain content types that allow you to upload and store various types of files, for example photos, pictures, sound files, videos, package files, presentations, or documents. Content item assets stored in the _Content hub_ can be reused throughout the system. Different [variants](documentation/business-users/content-hub/content-item-assets#image-variants) of image assets can be displayed in different contexts to ensure optimal visual presentations.
## Upload a single file
To upload a file to the _Content hub_ :
  1. Open the **Content hub** application.
  2. Select **New content item**.  
[![Creating a new content item](docsassets/documentation/content-item-assets/CH_create_item.png)](https://docs.kentico.com/docsassets/documentation/content-item-assets/CH_create_item.png)
  3. Select a [content type](documentation/developers-and-admins/development/content-types) that has an _Content item asset_ field. 
     * Search for content types named _Image_ , _Media_ , _File_ , _Asset_ , etc. The name of the content type depends on your project configuration.
  4. Enter the values of the content type fields and upload a file via the uploader.  
[![Asset uploader field](docsassets/documentation/content-item-assets/CH_asset_uploader.png)](https://docs.kentico.com/docsassets/documentation/content-item-assets/CH_asset_uploader.png)
  5. **Save** the new asset.


The asset is now created in the current [workspace](documentation/developers-and-admins/configuration/users/role-management/workspaces) and needs to be [published](documentation/business-users/content-hub/content-items#publish-content-items) before it is publicly available.
## Mass asset upload
To make uploading a large number of files easier, your administrators can [configure mass asset upload](documentation/developers-and-admins/development/content-types#mass-asset-upload-configuration) for your project. You can then upload multiple files in bulk and automatically convert them to content item assets of a configured content type.
  1. Upload files by drag and dropping them into the Content hub, combined content selector, or [rich text editor](documentation/business-users/rich-text-editor). Alternatively, you can upload them by selecting **New content item - > Upload files** in the Content hub. Content item assets are created automatically after the upload is finished. 
     * When mass uploading to the Content hub with [content folders](documentation/business-users/content-hub/content-hub-folders#content-folders) enabled, the files are uploaded into the currently selected folder.
     * If you cancel a mass upload in progress, the files that have already been successfully uploaded stay in the system.
  2. After a successful upload, you can open ([edit](documentation/business-users/content-hub/content-items#edit-content-items) it. Alternatively, you can edit the content item assets in the chosen location in the Content hub. The names of the created content item assets are automatically generated from the uploaded file names.


The assets are now created in the selected [workspace](documentation/developers-and-admins/configuration/users/role-management/workspaces) and need to be [published](documentation/business-users/content-hub/content-items#publish-content-items) before they become publicly available.
## Download files
If you need to access or modify a file previously [uploaded](documentation/business-users/content-hub/content-item-assets#upload-a-single-file) to the Content hub, you can download it from a content item asset.
To download a file from a content item asset:
  1. Open the **Content hub** application.
  2. Select a content item asset.
     * Use the search bar, filter or folders to [find content items](documentation/business-users/content-hub/content-items#find-content-items).
  3. **Download** (
[![Download an asset file](docsassets/documentation/content-item-assets/CH_download_asset_file.png)](https://docs.kentico.com/docsassets/documentation/content-item-assets/CH_download_asset_file.png)


The asset is now downloaded to your device.
## Optimize image assets
To help ensure that the images you use across different channels are properly optimized, your administrators can enable [automatic image optimization](documentation/developers-and-admins/development/content-types#automatically-optimize-image-assets). However, you can also manually optimize any image asset in the Content hub by changing the format of the image or reducing its size by adjusting image quality.
  1. [Edit](documentation/business-users/content-hub/content-items#edit-content-items) a content item asset that contains an image.
  2. **Adjust the image** (
     * If the image has [variants](documentation/business-users/content-hub/content-item-assets#image-variants), make sure to have the **Primary image** selected.  
[![Adjust image](docsassets/documentation/content-item-assets/CH_adjust_image_button.png)](https://docs.kentico.com/docsassets/documentation/content-item-assets/CH_adjust_image_button.png)
  3. Change the **Image format** or **Quality** of the image. 
     * Image format – while some formats like _WEBP_ and _AVIF_ are better suited for most modern web browsers, they may be incompatible with some legacy browsers (e.g., Internet Explorer). If you’re not sure about which image formats to use in your project, ask your project administrators.
     * Quality – the overall quality of the image. While higher quality retains more image details, lowering the quality reduces the file size. 
       * Quality selection is unavailable for image formats that use lossless compression, like _PNG_.
If you notice undesired [compression artifacts](https://en.wikipedia.org/wiki/Compression_artifact) in the optimized image, try increasing the quality until the artifacts are no longer visible. 
  4. You can preview the outcome of your adjustments on the image in the **Adjusted image info** section.
  5. **Save** the changes.


[![Adjust image dialog](docsassets/documentation/content-item-assets/CH_adjust_image.png)](https://docs.kentico.com/docsassets/documentation/content-item-assets/CH_adjust_image.png)
Xperience preserves all original images in the background, ensuring that optimizations are always applied to the original file rather than a previously modified version. This means that multiple adjustments do not compound quality loss. For example, if you change an image’s quality from 70% to 50%, the compression is applied as if you were reducing quality directly from 100%, rather than from the already compressed 70%.
## Image variants
Image variants are cropped or resized versions of an image, defined by a specific aspect ratio and pixel dimensions [configured](documentation/developers-and-admins/development/content-types#configure-image-variants) by your project administrator. They allow you to control how images appear in different contexts, such as hero banners, social media posts, or thumbnails. For example, you can adjust which part of an image is displayed in each scenario to ensure optimal visual presentation. Image variants also help with [art direction](https://developer.mozilla.org/en-US/docs/Web/HTML/Responsive_images) or [resolution switching](https://developer.mozilla.org/en-US/docs/Web/HTML/Guides/Responsive_images#how_do_you_create_responsive_images), allowing you to display different versions of the image on various layouts.
Image variants are generated from the primary [optimized image](documentation/business-users/content-hub/content-item-assets#optimize-image-assets), taking into account its format and quality. The [usage](documentation/developers-and-admins/development/content-types#use-image-variants) of image variants is currently supported only for [website channels](documentation/business-users/website-content).
There are two types of image variants available:
  * **Scaled variant** – Image dimensions are resized based on input while maintaining the original aspect ratio.
  * **Smart crop variant** – Images are cropped based on the focal point.


When using _Scaled variants_ , images can be scaled up if the specified dimensions exceed the original image dimensions. This may result in blurry images or increased file sizes due to image upscaling.
To see how your image will look like in various variants:
  1. [Edit](documentation/business-users/content-hub/content-items#edit-content-items) a content item asset that contains an image.
  2. **Adjust the image** (
  3. Select the image variant you want to view.


[![Image variants](docsassets/documentation/content-item-assets/CH_image_variants.png)](https://docs.kentico.com/docsassets/documentation/content-item-assets/CH_image_variants.png)
### Change image focal point
Image variants are automatically cropped based on the image’s focal point. When an image is uploaded, the focal point is initially placed at its center. To adjust how all image variants are cropped, move the focal point to your preferred location.
You don’t need to have any image variants [defined](documentation/developers-and-admins/development/content-types#configure-image-variants) in your project to adjust the focal point. Once administrators define new variants, they will be automatically cropped according to the selected focal point.
  1. **Adjust the image** (
  2. Use the purple dot to select a new focal point. 
     * If the image has variants, make sure the **Primary image** is selected.
  3. You can preview the new crops by selecting any image variant.
  4. **Save** the changes.


All image variants, except those [cropped manually](documentation/business-users/content-hub/content-item-assets#crop-variants-manually), are now re-cropped according to the updated focal point.
**Automatic focal point selection with AIRA**
AIRA can automatically select the best focal point for your image during file upload. See the [AIRA](documentation/business-users/aira#automatically-select-image-focal-point) documentation to learn more.
### Crop variants manually
You can manually crop an image variant to fine-tune its design by repositioning or resizing the crop. The crop retains the aspect ratio and dimensions defined for the variant.
  1. **Adjust the image** (
  2. Select the image variant you want to crop manually.
  3. Adjust the crop to fit your needs.
  4. **Save** the changes.


Manually cropped images, marked by the [focal point change](documentation/business-users/content-hub/content-item-assets#change-image-focal-point) on the primary image.
## Copy an asset URL
To use assets stored in Xperience across different platforms (such as social media), you can copy the URL of a published content item asset or an [image variant](documentation/business-users/content-hub/content-item-assets#image-variants). If you replace or modify the asset, you’ll need to publish it again before you can copy the URL.
To copy a URL of a content item asset:
  1. Open the **Content hub** application.
  2. Select a content item asset. 
     * Use the search bar, filter or folders to [find content items](documentation/business-users/content-hub/content-items#find-content-items).
  3. **Copy the URL** (


[![Copy URL of an asset](docsassets/documentation/content-item-assets/CH_copy_url.png)](https://docs.kentico.com/docsassets/documentation/content-item-assets/CH_copy_url.png)
To copy a URL of an image variant:
  1. [Edit](documentation/business-users/content-hub/content-items#edit-content-items) a content item asset that contains an image.
  2. **Adjust the image** (
  3. Select an image variant whose URL you want to copy.
  4. **Copy the URL** (


[![Copy URL of an image variant](docsassets/documentation/content-item-assets/CH_image_variant_copy_url.png)](https://docs.kentico.com/docsassets/documentation/content-item-assets/CH_image_variant_copy_url.png)
The copied URL path includes the domain of the Xperience administration by default. If you need to use a different domain, such as the domain of a [website channel](documentation/developers-and-admins/configuration/website-channel-management), manually change the corresponding part of the URL.
For example, if the copied URL is _https://admin.example.com/getContentAsset/…_ and your website channel uses _https://www.example.com_ , you should replace the domain accordingly to _https://www.example.com/getContentAsset/…_.