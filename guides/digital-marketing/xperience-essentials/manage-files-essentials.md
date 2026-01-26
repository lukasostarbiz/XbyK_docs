---
source: https://docs.kentico.com/guides/digital-marketing/xperience-essentials/manage-files-essentials
scrape_date: 2026-01-26
---

  * [Home](/guides)
  * [Digital marketing](/guides/digital-marketing)
  * [Xperience essentials](/guides/digital-marketing/xperience-essentials)
  * Manage files in Xperience 


# Manage files in Xperience
Managing your project’s files doesn’t have to be a complicated chore. In **Xperience by Kentico** , you can keep all your important assets in one place, making it easier to find and update them. They can also be reused whenever you need. In Xperience, you can store various file types, including images, videos, PDFs, presentations, documents, and more. All assets are stored within the [Content hub](/documentation/business-users/content-hub), your central repository for reusable marketing content.
The assets in the _Content hub_ are based on a custom [content type](/guides/architecture/content-modeling/content-modeling-guide/what-is-a-content-type). Depending on your project [implementation](/guides/architecture/content-modeling/content-modeling-guide/store-files#store-images-and-other-assets-in-the-content-hub), you’ll be working with _Images_ , _Media_ , or _Assets_. For example, in the Kbank demo site, you’ll work with the **Asset** content type.
[![Asset content type](/docsassets/guides/manage-files-essentials/asset-content-type.png)](/docsassets/guides/manage-files-essentials/asset-content-type.png)
Don’t have the demo Kbank website yet? You’ll need one to follow up on the exercises. If you don’t have your demo site set up, request the **Xperience by Kentico – Business Tutorial Kbank demo site** through a [form at Kentico website](https://www.kentico.com/services/training/training-website-requests).
We’ll show you the basics so you can easily store and manage your assets, and make your everyday marketing tasks a lot easier.
## Work with assets in Content hub
Storing files in _Content hub_ means you can reuse them in different digital marketing activities across your marketing [channels](/guides/digital-marketing/work-with-channels/digital-marketing-channels). Assets in the _Content hub_ provide you with helpful features that make managing large projects a breeze.
You can preview all content items with images (in Kbank based on the _Asset_ content type) and switch between view modes to suit your current [workflow](/guides/digital-marketing/work-with-workflows/workflows).
[![Asset content type](/docsassets/guides/manage-files-essentials/cm-guide-content-hub-assets-preview.png)](/docsassets/guides/manage-files-essentials/cm-guide-content-hub-assets-preview.png)
If you need to add multiple files to the _Content hub_ at once, use the **Upload files** button.
[![Upload files in bulk](/docsassets/guides/manage-files-essentials/cm-guide-mass-file-upload.png)](/docsassets/guides/manage-files-essentials/cm-guide-mass-file-upload.png)
You can also upload an image **directly from a widget** while editing a page.
[![Create new item in widget](/docsassets/guides/manage-files-essentials/empty-image-widget.png)](/docsassets/guides/manage-files-essentials/empty-image-widget.png)
When you upload an image (whether through the _Content hub_ or a widget), Xperience can automatically create [image variants](/documentation/business-users/content-hub/content-item-assets#image-variants) for different devices and applies [taxonomy tags](/guides/digital-marketing/work-with-taxonomies/taxonomies#what-are-taxonomy-trees-tags-or-parent-child-relationships) to help categorize the images.
Automatic image tagging works only for mass image uploads. If you upload a single image into a content item, the auto-tagging process may not be triggered, so you’ll have to assign the tags manually.
For more details on tags and taxonomy, check out our material on [working with taxonomies](/guides/digital-marketing/work-with-taxonomies/work-with-taxonomies-in-kbank).
The automatic taxonomy tags are generated using [Azure AI services](https://learn.microsoft.com/en-us/azure/ai-services/what-are-ai-services), which means results can vary. The same image may receive slightly different tags at different times, as the process is not standardized. You can, however, review and edit these tags as needed.
[![Updating automatic taxonomy tags](/docsassets/guides/manage-files-essentials/update_automatic-taxonomy-tags.png)](/docsassets/guides/manage-files-essentials/update_automatic-taxonomy-tags.png)
To ensure no unapproved data is published live, you can apply different [content management workflows](/documentation/developers-and-admins/configuration/workflows). You can also fully control who on your team uses the assets and how they use them through [workspaces](/documentation/developers-and-admins/configuration/users/role-management/workspaces).
You can also create asset variants for different languages to ensure that your visitors receive properly [localized content](/documentation/developers-and-admins/configuration/languages), and use [taxonomies](/documentation/developers-and-admins/configuration/taxonomies) to categorize your images accordingly.
Note that assets in _Content hub_ don’t support versioning. If you replace a file, only the latest version is available. To learn more, see the [Content versioning](/documentation/business-users/content-versioning#assets) page.
Xperience also provides an out-of-the-box method to **restrict access** to a file, meaning only logged-in visitors can view or access it.
[![Content items that require the user to sign before viewing their content](/docsassets/guides/manage-files-essentials/cm-guide-secured-access.png)](/docsassets/guides/manage-files-essentials/cm-guide-secured-access.png)
## Exercise 1: Upload and manage multiple images in Content hub
Imagine that you’re running a flash promotional push to promote a new product – _Family Savings Account_. The promo push will involve a whole batch of images, but uploading them one by one would be slow and a bit frustrating.
That’s why _Content hub_ allows you to **upload and prepare multiple images** at the same time. With some help from _AIRA_ , you’ll also ad the correct tags, and automatically generate image variants in different sizes. By the end, you’ll have a clean, [well-organized folder of assets](/guides/digital-marketing/work-with-reusable-content/organize-assets-in-content-hub#create-folder-hierarchy) ready to be used across your promo push.
To follow along with the exercise, download a [zipped file of 11 images](https://download.kentico.com/Training/sample+assets_multifile_upload.zip) we prepared for you.
  1. **Download and prepare your images**
    1. Download the sample images ZIP from the course resources and extract it to a local folder on your device.
    2. Review the file names and remove duplicates if necessary to keep the library clean.


If your team has a file-naming convention, rename files to ensure consistency with internal rules.
  1. **Open Content hub and choose your target folder**
    1. In Xperience, go to **Content management** and open **Content hub**.  

    2. Ensure you are working in the **Personal Banking** workspace.
    3. Navigate the **Folders** tab and locate or create the folder where your campaign images should live.  

    4. Let’s also create a subfolder like _Family Savings Campaign_ to keep assets easy to find later.


[![Choose the correct workspace and create folder structure](/docsassets/guides/manage-files-essentials/correct-workspace-and-folders.png)](/docsassets/guides/manage-files-essentials/correct-workspace-and-folders.png)
Why does it matter? A logical [folder hierarchy](/guides/digital-marketing/work-with-reusable-content/organize-assets-in-content-hub) makes browsing and permissions simpler for everyone.
  1. **Start a bulk upload**
    1. Next to the **New content item** button, use the arrow to open dropdown menu and select **Upload files**. [![Upload button](/docsassets/guides/manage-files-essentials/upload-button.png)](/docsassets/guides/manage-files-essentials/upload-button.png)
    2. Select the files you want to upload from your source folder in the upload dialog.  

    3. Use **Open** to confirm selected files and begin the upload.
    4. Once the files are successfully uploaded, you can **Close** the upload dialog. [![Upload queue](/docsassets/guides/manage-files-essentials/selected-files-during-upload.png)](/docsassets/guides/manage-files-essentials/selected-files-during-upload.png)
    5. The _Assets_ are now in the _Draft (Initial)_ status, and you can check them and make further adjustments before you publish the assets.


During the upload, AIRA automatically tags and optimizes the images. You can then start adjusting them manually.
  1. **Check and adjust images**
    1. Open the image with the original filename _AdobeStock_445562436.jpeg_ and adjust: 
       * **Description** : _Image of a smiling woman with a credit card and laptop._
       * **Alt text** : _Smiling woman holding a credit card, looking into an open laptop computer._
       * Click the **Adjust image** icon to update the image variant. [![Adjust image](/docsassets/guides/manage-files-essentials/adjust-image-variants.png)](/docsassets/guides/manage-files-essentials/adjust-image-variants.png)
       * **Primary Image** changes to make: 
         * **Image format** : Change from _JPEG_ to _JPG_.
         * **Quality** : adjust the quality to _Medium 80%_
    2. **Save** the changes.
    3. Manually adjust the **focal point** of the _Primary image_. [![Manual focal point adjustment](/docsassets/guides/manage-files-essentials/move-focal-point.gif)](/docsassets/guides/manage-files-essentials/move-focal-point.gif)
    4. Check and adjust the crop in **Image variants** if needed. [![Adjust image variant crop](/docsassets/guides/manage-files-essentials/adjust-image-variant-crop.jpg)](/docsassets/guides/manage-files-essentials/adjust-image-variant-crop.jpg)
    5. **Save** your latest updates.


**Variant notes** :
Follow any team internal rules you might have on the image quality settings or where the focal point should typically go, such as “don’t lower the quality of the image below 50%” and “keep the focal point on people’s faces.”
  1. **Manually adjust predefined tags:**


  * Use the tag selector to add relevant tags from your project’s predefined list, such as _Young people_ and _Family setting_.
  * **Save** your changes to apply tags.  
[![AIRA-generated tags](/docsassets/guides/manage-files-essentials/AIRA-generated-tags.png)](/docsassets/guides/manage-files-essentials/AIRA-generated-tags.png)


Currently, you can only choose from predefined tags configured by your developer. You cannot create new tags in the _Content hub_ at the moment. However, it is still beneficial to follow a few best practices when it comes to tagging.
  * Prefer specific tags over vague labels like _misc_ or _general_.
  * Reuse existing tags rather than inventing near-duplicates (for example, choose _Buildings_ over adding a new tag _House_).  

  * Keep it focused: aim for 3 to 6 precise tags per asset to improve search accuracy.  

  * Follow team conventions for consistency.


  1. **Inspect another image to verify results**
    1. Open newly uploaded image.  

    2. Confirm it has the expected tags and a clear description.  

    3. Make minor edits if needed, then **Save**. [![Check images for last updates](/docsassets/guides/manage-files-essentials/check-image-for-last-updates.png)](/docsassets/guides/manage-files-essentials/check-image-for-last-updates.png)
  2. **Final tidy-up and quality check**
    1. Ensure all images reside in the **correct folder** with the proper naming and tags.  

    2. Use **Filters** in the folder, for example, filter by **Content type** (_Asset_), or by **Taxonomy** tags you just applied (to confirm your tagging works as expected).
    3. If an image does not fit this campaign, move it to a more suitable folder, or make any last adjustments now.  
[![Filter assets in folder for last checks](/docsassets/guides/manage-files-essentials/filter-assets-in-folder.png)](/docsassets/guides/manage-files-essentials/filter-assets-in-folder.png)
  3. **Publish your assets**
    1. Select all assets in the folder by checking off the **Content item name** checkbox, or one by one if you want to select only some.
    2. **Publish** all selected items in bulk. [![Publish selected assets](/docsassets/guides/manage-files-essentials/publish-selected-assets.png)](/docsassets/guides/manage-files-essentials/publish-selected-assets.png)
    3. If unsure about the result, verify that the status of the selected assets has changed from _Draft (Initial)_ to _Published_. [![Published assets status](/docsassets/guides/manage-files-essentials/published-assets-status.png)](/docsassets/guides/manage-files-essentials/published-assets-status.png)


Nice work! You’ve just uploaded and organized a larger set of images in one smooth process. By using bulk upload, AIRA-assisted tagging and adding alt description, and creating manually updated variants, you’ve built a tidy, reusable library that will save you and your team time later.
## Exercise 2: Add an image to the Premier credit card image widget
Now that you know the basics of working with assets in Xperience, let’s add an image to a page that only your editor team will be able to manage. Follow these steps to **add an image to the empty image widget** on the website channel’s **Premier credit card** page.
To follow along with the exercise, download a [sample image file](https://download.kentico.com/Training/exercise-sample-manage-files-man-holding-credit-card.png) we prepared for you.
  1. **Open the page for editing**
     * In the **Personal Banking** channel, navigate to the **Cards** section.
     * Open the _Premier credit card_ page and **Edit page**.
  2. **Select the empty Image widget**
     * The empty image widget needs setup.
     * Open the widget properties to insert the sample image you downloaded. [![Image widget properties](/docsassets/guides/manage-files-essentials/empty-image-widget-properties.png)](/docsassets/guides/manage-files-essentials/empty-image-widget-properties.png)
  3. **Create a new image**
     * In the image properties dialogue, choose _Content hub asset_.
     * To add a new picture, select **Create new** , and choose the following in the **Create new item** dialogue: 
       1. **Content item name** – Enter a clear, descriptive name, like _Premier credit card holder_.
       2. **Workspace** – Select the _Personal Banking_ workspace.
       3. **Location** – Leave the default _Uncategorized (no folder)_.
       4. **Content type** – Select _Asset_ and **Continue**.
       5. **Description** – Add a meaningful description to help your team understand where and how to use the picture. Like: _Image of a young man holding a credit card, to be used as a Premier credit card product image._
       6. **Alt text** – description of image content to enhance usability. In this case: _Smiling man holding a credit card._
       7. **File** – Upload the _exercise-sample-manage-files-man-holding-credit-card.png_ file.
       8. **Tags** – Select one or more tags that best describes what’s in the image, like _Young people_. Consistent tagging helps with search and asset reuse later.
     * **Save and Close** your newly created content item. This step will return you to the **Image properties** dialogue, where you can finish setting up your image widget.
[![Image properties dialogue](/docsassets/guides/manage-files-essentials/image-properties-with-new-item.png)](/docsassets/guides/manage-files-essentials/image-properties-with-new-item.png)
  4. **Adjust widget display settings**


  * After inserting the image, you can adjust how it appears on the page before you save the image properties.
  * You can, for example, display the image as _Large_ to make it less limited in height.
The available options depend on your project’s configuration, so the options in your project may differ from our example.


  1. **Save your changes**


  * **Apply** the changes you just made, and **Save** the page to **Preview** your updates.
  * If you’re happy with the newly created image, return to the **Page Builder** view and **Publish** the new version of the _Premier credit card_ page.


The page should look something like the following image: [![Premier credit card page with new image](/docsassets/guides/manage-files-essentials/premier-credit-card-with-new-product-image.png)](/docsassets/guides/manage-files-essentials/premier-credit-card-with-new-product-image.png)
### See where your image is used before making any changes
Before you edit or remove an image, take a moment to review the impact of your changes with the _Content Reuse Locator_. You’ll find [this tool](https://www.kentico.com/discover/blog/content-reuse-locator) in the **Used in** [tab](/documentation/business-users/website-content#used-in) of the asset details within Content hub. The _Used in_ tab shows every page or content item that references the image. You can confidently update or change the image while knowing how your changes affect other content.
[![See where your image is used before making any changes](/docsassets/guides/manage-files-essentials/content_reuse_locator_use_in_tab_on_image.png)](/docsassets/guides/manage-files-essentials/content_reuse_locator_use_in_tab_on_image.png)
## Image AI features
Xperience [AIRA](/documentation/developers-and-admins/configuration/aira-configuration) provides additional features when it comes to images stored in the _Content hub_. It can automatically adjust images when you upload them by, for example, **automatically selecting the image focal point** and creating **different image variants for desktops and mobile devices**. This ensures your audience sees the best version of your image, no matter their device.
If you want more control over how the final image looks, you can **manually adjust the focal point** to crop the image appropriately before saving the new content item.
[![Manually selected focal point](/docsassets/guides/manage-files-essentials/primary-image-manually-selected-focal-point.png)](/docsassets/guides/manage-files-essentials/primary-image-manually-selected-focal-point.png)
You can test this for yourself and instantly see how changing the focal point also **adjusts the cropped variants** of the image to be in line with the newly selected focal point.
[![Vertical image variant](/docsassets/guides/manage-files-essentials/vertical-image-variant.png)](/docsassets/guides/manage-files-essentials/vertical-image-variant.png)
Learn more about how AIRA [processes and adjusts](/documentation/business-users/aira#image-processing) your images to improve performance and visual quality.
If you want to leverage the most out of **working with assets** , and see how _AIRA_ optimization works firsthand, check out the following short demo, where you’ll learn how to upload images into the **Content hub** and how to keep them organized. You’ll also see how Xperience automatically prepares the uploaded images for use across your websites and campaigns.
To follow along with this demo step by step, see details in our dedicated guide on [working with assets](/guides/digital-marketing/work-with-reusable-content/work-with-assets).
## Media libraries sunset
**Media libraries are a legacy feature** and have been officially sunset. Their related API members are marked as obsolete, and support will end on **July 24, 2026** , after which the feature and all associated APIs will be removed. If your project still uses media libraries, discuss [our migration guide](/guides/architecture/media-libraries-migration-guidance) and the migration of media library files to [Content hub](/documentation/business-users/content-hub) with your developers.
In previous versions of Kentico, media libraries stored file types such as images, videos, and audio files for use on websites. While they worked similarly to assets in the _Content hub_ , they offered fewer customization options, only had a predefined set of fields, and could not be secured (meaning that anyone with the direct file URL could access them).
[![Folder structure in a media library](/docsassets/guides/manage-files-essentials/cm-guide-library-files-folder-structure.png)](/docsassets/guides/manage-files-essentials/cm-guide-library-files-folder-structure.png)
For all of the above reasons, the clear way forward is to organize, optimize, and safeguard your assets in the _Content hub_.
## Wrap up
By now, you’ve seen how Xperience helps you keep files organized and easy to reuse. You also practiced uploading and working with image assets directly from a widget. These skills make it easier to handle your daily tasks with less manual effort and much faster than before.
Use _Content hub_ to:
  * Store and reuse assets across different channels.  

  * Upload files directly in the _Content hub_ or from within an image widget.
  * Use multi-file upload to speed up and simplify your asset management.
  * Benefit from automatic image variants and taxonomy tags.  

  * Use AIRA to process and optimize images for better performance automatically.  

  * Add and configure images in widgets to control how they display on your site and on different devices.  

  * Improve working with assets when compared to _Media libraries_.


## Next step
You’ve now learned the basics of managing your files in Xperience. You’re in a great position to take a deep dive look at how [working with image variants and focal point](/guides/digital-marketing/xperience-essentials/work-with-image-variants-and-focal-point) can make your daily image-related chores a walk in the park.
If you’re curious about content management, you can also learn more about how developers define the [content type assets](/documentation/developers-and-admins/development/content-types) and [retrieve their content](/documentation/developers-and-admins/development/content-retrieval/retrieve-content-items) to the website.
![]()
[]()[]()
