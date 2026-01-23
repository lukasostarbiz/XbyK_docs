---
source: https://docs.kentico.com/guides/architecture/content-modeling/content-modeling-guide/store-files
scrape_date: 2026-01-22
---

  * [Home](/guides)
  * [Architecture](/guides/architecture)
  * [Content modeling](/guides/architecture/content-modeling)
  * [Content modeling guide](/guides/architecture/content-modeling/content-modeling-guide)
  * Store files 


# Store files
## About this guide
This page is a part of the **Content modeling guide** which you should follow sequentially from [beginning to end](/guides/architecture/content-modeling/content-modeling-guide). You can also follow this series in [Content modeling guide](/modules/content-modeling-guide), which helps you keep track of your progress as you move through this sequential material.
Xperience by Kentico allows teams to store and manage different files, such as images, videos, documents, and more, as part of their structured content. Instead of treating files as generic uploads, you can model them as [reusable content](/guides/development/developer-kickstart/create-a-reusable-content-type#define-the-content-type) items and manage their metadata, permissions, and relationships to other content in your system.
We’ll explore how to design and implement content types to store assets in the [Content hub](/documentation/business-users/content-hub) application. You’ll learn how to define content types for different file formats, what fields to include, and how to support editorial workflows and front-end delivery requirements.
Xperience provides editors with a rich set of tools, including:
  * Automatic [image transformations to predefined formats](/documentation/business-users/content-hub/content-item-assets#optimize-image-assets).
  * Responsive [image variants](/documentation/business-users/content-hub/content-item-assets#image-variants).
  * [Focal point selection](/documentation/business-users/content-hub/content-item-assets#change-image-focal-point) for smart cropping and scaling.
  * Optimized delivery through the [Content Retriever API](/documentation/developers-and-admins/development/content-retrieval/retrieve-content-items#retrieve-image-variants).


By modeling your files as structured content items, editors and developers can control how the files are stored, categorized, retrieved, and reused. This approach supports long-term scalability and content delivery across channels, enables content personalization features, and helps teams comply with SEO strategies.
The guide also touches on when and how to work with [unmanaged files](#unmanaged-files-and-when-to-use-them). These files aren’t part of the project’s content model but are still needed for development, layout, or system integrations. You’ll learn how to correctly separate content-managed assets from technical files and handle each type.
## Store images and other assets in the Content hub
Modeling file assets as content items gives you more than a place to store files. It provides the editor team with structured, reusable building blocks to deliver optimized and personalized content across all channels. Whether you’re working with images, documents, or video, storing assets in _Content hub_ offers a flexible and future-friendly approach.
### See the benefits of storing assets in Content hub
[Media libraries](/documentation/business-users/media-libraries) have historically been used to store files in previous Kentico products, such as Kentico 12 or Kentico Xperience 13. However, the market has evolved and media libraries are **not the recommended approach** for storing files in Xperience by Kentico.
[Content item assets](/documentation/business-users/content-hub/content-item-assets) offer a modern, structured approach to media management. They address many limitations that make it difficult for Media libraries to [extend](/documentation/developers-and-admins/customization/extend-the-administration-interface) or integrate into content-first solutions. The result is a more flexible and secure way to manage files in content-driven projects.
Media libraries are considered an Xperience by Kentico legacy feature. We consider [sunsetting the Media library feature](https://roadmap.kentico.com/c/279-migrating-media-library-items-to-content-hub-media-library-sunset), marking the functionality as obsolete and phasing out Media libraries in the future.
Let’s cover improvements you gain from using the _Content hub_.
### Define custom content types to store images
Xperience by Kentico doesn’t come with a pre-built [content type](/documentation/developers-and-admins/development/content-types) to store images. During [content modeling sessions](/guides/architecture/content-modeling/content-modeling-guide/content-modeling-process), you define the structure of each asset content type based on the project’s needs. Instead of relying on a one-size-fits-all file system, you can:
  * Create different content types for various file formats, such as images, videos, PDFs, or audio files.
  * Store only the fields you need and don’t clutter the admin UI with unnecessary fields.
  * Include custom fields to support business use cases, such as licensing notes, expiration dates, or external system IDs.
  * Use the built-in _Content item asset_ to upload the binary file and the _Combined content selector_ component to reference assets in other content items, [Email Builder](/documentation/developers-and-admins/development/builders/email-builder), or [Page Builder](/documentation/developers-and-admins/development/builders/page-builder) widgets.


This flexibility supports editorial workflows and compliance requirements and opens the door to tailor-fit Xperience to custom integrations.
Let’s explore how moving away from media libraries towards the _Content hub_ can help in the mentioned use cases.
### Aim for an editor experience
Xperience provides an editing interface that streamlines how teams upload, organize, and reuse files, especially images. Key features include:
  * Enforced [default editing workflow](/guides/digital-marketing/xperience-essentials/manage-content-hub-essentials).
  * Drag-and-drop [mass uploads](/documentation/business-users/content-hub/content-item-assets#mass-asset-upload) into _Content hub_.
  * Built-in [AIRA-driven](/documentation/business-users/aira) image transformations to automatically create predefined image formats.
  * [Focal point](/documentation/business-users/content-hub/content-item-assets#change-image-focal-point) for smarter cropping and scaling.
  * UI for creating and previewing [responsive image variants](/documentation/business-users/content-hub/content-item-assets#image-variants).
  * [Hierarchical taxonomies](/documentation/developers-and-admins/configuration/taxonomies#enable-editors-to-tag-content) to improve image categorization, dynamic content resurfacing, or auto-tagging in mass uploads using [AIRA](/documentation/business-users/aira#automatically-assign-tags-to-images).
  * [Combined content selector](/guides/digital-marketing/xperience-essentials/manage-files-essentials#work-with-assets-in-content-hub) with thumbnail preview that simplifies reuse across content items without re-uploading.


These features help reduce friction and ensure consistency across the system.
### Support responsive and optimized asset delivery
Xperience provides the [ContentRetriever API](/documentation/developers-and-admins/api/content-item-api/content-retriever-api), which supports advanced asset retrieval options. The API helps developers serve optimized assets to traditional websites or email channels.
For images, Xperience can:
  * Serve as a [central repository](/documentation/business-users/content-hub) for images.
  * Automatically [resize and adjust](/documentation/business-users/content-hub/content-item-assets#optimize-image-assets) image formats depending on your application configuration when uploading.
  * [Restrict visitor’s access to files](/documentation/business-users/content-hub/content-items#secure-content-items) when they need to authenticate to access the files, for example, in scenarios that include gated content or licensed media.
  * Manage assets under [content workflows](/documentation/business-users/content-hub/content-items#content-items-lifecycle) to ensure consistent messaging.


[![Interface for adjusting image in Content hub](/docsassets/guides/store-files/cm-guide-store-files-adjust-image-ui-example.png)](/docsassets/guides/store-files/cm-guide-store-files-adjust-image-ui-example.png)
Together, these capabilities ensure your content is delivered efficiently and securely.
### Define structured metadata without developing custom UI components
Each business needs different data about its files. In [Media libraries](/documentation/business-users/media-libraries), adding fields like _captions_ , _copyright info_ , or _internal usage notes_ requires [custom UI development](/documentation/developers-and-admins/customization/extend-the-administration-interface). Customers often turned to third-party DAM ([Digital Asset Management](https://en.wikipedia.org/wiki/Digital_asset_management)) solutions to avoid unnecessary UI customizations.
Built-in features in Xperience allow you to avoid using third-party DAM solutions in many customer scenarios. Your [Content hub assets](/documentation/business-users/content-hub/content-item-assets) can directly contain the fields you need in the data model and metadata requirements. Editors can manage their data in one interface and don’t need to rely on workarounds or developer interventions.
### Leverage built-in taxonomies
Media libraries don’t support [categorization or tagging](/guides/architecture/content-modeling/content-modeling-guide/model-taxonomies). This limits filtering, personalization, and content discovery. In contrast, _Content hub assets_ can use [taxonomies](/guides/digital-marketing/work-with-taxonomies/taxonomies) out of the box, allowing your teams to group and retrieve assets dynamically based on marketing themes, campaigns, or topics.
For example, a marketing team can tag product images with taxonomies, such as _Winter Campaign_ or _Eco-Friendly Collection_.
When building a campaign landing page, editors can use a [widget](/guides/architecture/content-modeling/model-website-presentation-components/model-page-builder-widgets#widgets-that-display-data-from-semantic-content-types) and select only the images tagged with _Winter Campaign_ without manually sorting through folders. Developers can also use these tags to dynamically load matching assets on the website or in personalized email campaigns, ensuring the right visuals appear in the proper context automatically.
### Control access to protected files
Files stored in Media libraries are always [publicly accessible](/documentation/business-users/media-libraries) through their URLs. Developers or DevOps engineers need to create custom solutions to enforce sign-in or restrict access.
_Content hub assets_ support access control natively. Editors can [configure which assets](/documentation/business-users/content-hub/content-items#secure-content-items) require authentication in the default admin UI, making _content hub assets_ more than suitable for storing gated, licensed, or confidential content.
### Track versions and localize assets without workarounds
Media libraries don’t offer publishing [workflows](/guides/digital-marketing/work-with-workflows/workflows) or [language variants](/guides/digital-marketing/work-with-multilingual/multilingual-variant-of-content). That made it hard to manage content across regions or align with campaign timelines.
Reusable content items support default draft and published states. Editors can build custom [workflows](/documentation/developers-and-admins/configuration/workflows) that fully fit the project’s content lifecycle. Additionally, items support scheduled publishing and creating per-language variants with the option to [fallback to the default language variant](/guides/digital-marketing/work-with-multilingual/multilingual-variant-of-content#what-is-a-fallback-language).
### Optimize image delivery
Media libraries don’t support [image transformations](/documentation/business-users/content-hub/content-item-assets#optimize-image-assets), [focal point cropping](/documentation/business-users/content-hub/content-item-assets#change-image-focal-point), or [responsive image variants](/documentation/business-users/content-hub/content-item-assets#image-variants). These tasks always required customization by front-end developers.
Content hub assets simplify delivering a [responsive experience](/documentation/business-users/content-hub/content-item-assets#image-variants) with built-in tools for image resizing, format switching, and responsive rendering—without requiring extra markup or manual asset prep.
[![Content item UI for editing image variants](/docsassets/guides/store-files/cm-guide-store-files-edit-variant-ui-example.png)](/docsassets/guides/store-files/cm-guide-store-files-edit-variant-ui-example.png)
## Define content types for media assets
Xperience does not provide a _default content type_ for managing media assets. Instead, customers define their asset [content types](/documentation/developers-and-admins/development/content-types) based on the file types they plan to use and the structure their solution requires.
Xperience offers advanced functionality for working with image-based assets, like [transformations](/documentation/business-users/content-hub/content-item-assets#optimize-image-assets), [focal point cropping](/documentation/business-users/content-hub/content-item-assets#change-image-focal-point), or [responsive variants](/documentation/business-users/content-hub/content-item-assets#image-variants). However, these capabilities are only available when the asset is stored in a properly modeled content type.
To support a flexible, scalable approach to asset management, we recommend defining different content types for each major file format used in your project.
### Image content type
Define this content type to manage image files used in [banners](/guides/digital-marketing/work-with-reusable-content/reuse-content-with-the-hero-widget) , [articles](/guides/architecture/content-modeling/model-reusable-content/model-a-reusable-article), _product listings_ , or _promotional content_. Then, enable built-in image tools and responsive delivery features, such as transformations, focal point cropping, and device-based variant selection.
Recommended fields:
  * **File** – image uploader using the [Asset uploader](/documentation/developers-and-admins/development/content-types#configure-asset-content-types) form component. [Configure mass asset upload](/documentation/developers-and-admins/development/content-types#configure-asset-content-types) for this content type and set this field as the target field.
  * **Title**
  * **Alt text**
  * **Caption** (or **Description**)
  * **Tags** – reference to [taxonomy](/documentation/developers-and-admins/configuration/taxonomies) collections.


[![Sample Asset content type in the Kbank demo site](/docsassets/guides/store-files/cm-guide-store-files-asset-example.png)](/docsassets/guides/store-files/cm-guide-store-files-asset-example.png)
Optional fields depending on the business requirements:
  * **Expiration date** – for managing licensing or campaign life cycles.
  * **License owner**


### Restricted image content type
For some projects, you might need to introduce a second asset [content type](/documentation/developers-and-admins/development/content-types) to store files for legally protected, license-limited, or tightly controlled images. Typical use cases include _licensed photos from an agency_ , _regulated visuals in the medical or pharmaceutical space_ , and _internal_ or _embargoed media_. Customers can leverage this content type when they don’t plan to use Xperience’s built-in [transformations](/documentation/business-users/content-hub/content-item-assets#optimize-image-assets).
We recommend creating an additional content type and defining dedicated fields to hold different file variants you plan to serve in the presentation channels. For example, you can introduce the following fields:
  * Separate **content item asset** fields for desktop, tablet, and mobile to store only pre-approved image variants.
  * **Metadata** fields for licensing notes or usage restrictions.
  * Do not enable image transformations in the [Asset uploader](/documentation/developers-and-admins/development/content-types#configure-asset-content-types) – editors can upload pre-approved variants only.
  * Enable [authentication](/documentation/business-users/media-libraries) if the content needs to be hidden behind a sign-in process.


This approach ensures that Xperience doesn’t automatically apply any unauthorized changes to the asset and gives developers full control over which version appears on different devices or regions.
### Content types for non-image files
Depending on the business requirements, you might need to introduce additional content types to hold other asset types.
#### Document asset
A **Document asset** [content type](/documentation/developers-and-admins/development/content-types) can store and manage files that aren’t images or videos but are still crucial for sharing information with website visitors or internal users. This typically includes file formats, such as _PDFs_ (brochures, whitepapers, or eBooks), _Microsoft Word_ documents (such as reports or instructional guides), _spreadsheets_ (for example, pricing sheets or data summaries), _presentations_ created in PowerPoint, and other downloadable files like _ZIP archives_ or _plain text_ files.
Recommended fields:
  * **File** – document field using the [Asset uploader](/documentation/developers-and-admins/development/content-types#configure-asset-content-types) form component.
  * **Title**
  * **Author**
  * **Publication date**
  * **Lede** , **Perex** , or **Summary**
  * **Download tracking** – optional boolean to [log custom file download activity](/guides/development/activities-and-marketing/log-custom-activities) for every reference. Your developers can enable editors to enable download tracking in the presentation layer, e.g., in a widget property.


#### Video asset
Editors can use the **Video asset** [content type](/documentation/developers-and-admins/development/content-types) to store and manage video content they want to embed or share on their website. This can include externally hosted videos, such as YouTube or Vimeo links, and internally hosted video files in formats like MP4 or WebM. Video assets make it easy to centralize video content so editors can reuse it across multiple pages without uploading it repeatedly.
Customers might use this content type for _promotional videos_ , _customer testimonials_ , _product demos_ , _feature walkthroughs_ , _recorded webinars_ , or _training videos_. Whether the video is streamed from an external platform or hosted directly within the system, a _Video asset_ helps ensure consistent access, descriptions, and preview images across the site.
[![Sample Video content type from Kentico Community Portal](/docsassets/guides/store-files/cm-guide-store-files-video-community-example.png)](/docsassets/guides/store-files/cm-guide-store-files-video-community-example.png)
Depending on your project’s requirements, consider adding the following properties:
  * **File** – local video file
  * **Video ID** - external embed code, e.g., YouTube URL slug or Vimeo ID.
  * **Platform ID** – an optional field for third-party video services.
  * **Duration**
  * **Transcript**
  * **Thumbnail** image - can reference to [Image](#image-content-type) content type using the _Combined content selector_ form control.


The **Video** content type can include a visibility condition, letting editors choose how to add a specific video. For example, when creating a new video content item, an editor can select through a [Radio button](/documentation/developers-and-admins/customization/extend-the-administration-interface/ui-form-components/reference-admin-ui-form-components#radio-group-component) form component whether the video is coming from YouTube or they uploaded it directly. Based on that selection, Xperience automatically hides or shows only relevant fields.
Developers can [implement visibility logic](/documentation/developers-and-admins/customization/extend-the-administration-interface/ui-form-components/ui-form-component-visibility-conditions) into the content type and display data using the correct presentation. If the editor selects YouTube, the system might display a field for entering the YouTube URL and hide any _file upload options_ or the _Thumbnail_ field. If they choose to upload a file, the interface might show a field for uploading the video file and hide the YouTube URL input. This approach makes the content editing experience cleaner and more intuitive, helping editors focus only on the information they need to fill out.
Developers can define different presentation options based on the video source. For example, they can present the YouTube link through an embedded YouTube player component to leverage its capabilities and serve uploaded videos through a custom media player to ensure proper embedding, styling, and playback behavior.
#### Audio asset
If the project requires it, you might need to define a dedicated **Audio asset** content type to store and manage audio content. This can include direct uploads of audio files, such as MP3 or WAV formats, and links to externally hosted audio, like a podcast episode hosted on a streaming service.
Customers can store data, such as podcast recordings, voiceovers for tutorials, interviews, background music for specific sections of a website, or downloadable audio guides. Just like with [Video](#video-asset) and [Document](#document-asset) assets, audio files stored as **Audio** can be reused across multiple website pages or referenced from emails, and editors can manage metadata like titles, descriptions, and cover images for consistent presentation.
Recommended fields:
  * **File** – uploaded audio file.
  * **Duration**
  * **Transcript**
  * **Series** / **Episode number** – for podcasts or serialized content.


### Serve the right asset for every context
Once assets are stored as structured content items, developers can retrieve them consistently across channels using the Xperience API.
#### Provide optimized asset delivery
To render content that includes asset references, developers will use the [ContentRetriever API](/documentation/developers-and-admins/api/content-item-api/content-retriever-api), which allows them to query asset metadata and binaries defined in the project’s content types, including:
  * **File URLs** (original and predefined image variants) using the built-in attribute [TagHelper](/documentation/developers-and-admins/development/reference-tag-helpers#attribute-tag-helpers-images).
  * Related custom **Metadata** fields (e.g., alt text, caption, copyright).
  * **References** to related content via taxonomy or linked items.


This supports rendering content in web pages, mobile apps, and other delivery channels, without requiring separate queries for file storage or metadata.
#### Best practices for images
To ensure responsive and accessible image delivery across devices, follow these best practices when working with [Content item assets](/documentation/business-users/content-hub/content-item-assets):
  * Use predefined [image variants](/documentation/business-users/content-hub/content-item-assets#image-variants) (e.g., thumbnail, mobile, desktop) to support responsive display.
  * Reference variants using `<picture>` and `srcset` to [adapt to screen size or device type](https://developer.mozilla.org/en-US/docs/Web/HTML/Guides/Responsive_images).
  * Let editors define [focal points](/documentation/business-users/content-hub/content-item-assets#change-image-focal-point) during upload for smart cropping and visual consistency.
  * The system automatically [optimizes image variants](/documentation/business-users/content-hub/content-item-assets#optimize-image-assets) for performance.
  * Developers can request variants by name or use [predictable URL patterns](/documentation/developers-and-admins/development/reference-tag-helpers#attribute-tag-helpers-images).
  * Always include alt text and fallback images to support accessibility and reliability.


## Metadata, taxonomy, and smart organization
Asset content types in Xperience give you complete control over how files are described, categorized, and grouped for reuse. You can include structured metadata and [taxonomies](/documentation/developers-and-admins/configuration/taxonomies) based on your project’s requirements to make assets easier to search, filter, personalize, and manage over time.
### Use metadata to support retrieval and compliance
Include fields that help editors and developers retrieve and evaluate the asset quickly, for example:
  * **Title** and description for identifying the asset.
  * **Alt text** for [accessibility and SEO](/guides/architecture/content-modeling/model-website-content-types/model-an-article-page#consult-required-data-with-stakeholders-and-seos).
  * **Copyright** , **author** , or **licensing information** for legal tracking.
  * **Expiration dates** for campaign or [usage control](/documentation/business-users/content-hub/content-items).
  * **Internal notes** to support editorial workflows.


Metadata can also support integrations with external systems by including fields like external IDs or source references.
### Tag assets using Taxonomies
[Taxonomies](/guides/digital-marketing/work-with-taxonomies/taxonomies) let editors classify assets in a consistent, structured way. Instead of relying on folder names or filenames, editors can [assign tags](/documentation/developers-and-admins/configuration/taxonomies#enable-editors-to-tag-content) to reflect the topic, a campaign, or the dedicated audience.
Benefits of using taxonomies:
  * Editors can [filter assets](/guides/digital-marketing/work-with-taxonomies/work-with-taxonomies-in-kbank#use-tags-to-filter-categories-in-the-article-list-widget) during selection or content creation.
  * Developers can [query](/documentation/developers-and-admins/configuration/taxonomies#retrieve-taxonomies-and-tagged-content) assets dynamically based on tags.
  * Marketers can use categories to support personalization and targeting.


This approach also helps large teams stay aligned on naming and categorization, making it easier to scale content operations without confusion or duplication.
### Use Smart folders for organization
[Smart folders](/documentation/business-users/content-hub/content-hub-folders#smart-folders) provide a dynamic way to group and filter content items based on live conditions. Editors don’t need to manually maintain content in smart folders, as it automatically includes or excludes items when an item’s data or tags change, and it now fits the smart folder [filter conditions](/documentation/business-users/content-hub/content-hub-folders).
Each _smart folder_ is defined by a filter, such as _Items modified in the last 7 days_ , _Items with the Draft status_ , or _Items tagged with [company/topic/campaign name]_. A single content item can appear in multiple _smart folders_ if it matches more than one condition.
_Smart folders_ help both editorial workflows and content delivery. Editors use them to [create views](/documentation/business-users/content-hub/content-hub-folders#create-smart-folders) to quickly find relevant items in the _Content hub_. They also don’t need to manually manage item references or update the widget each time, since developers can use the [smart folders API](/guides/development/advanced-content/deliver-content-dynamically-with-smart-folders) to retrieve content dynamically into website pages.
For example, an editor working on a website adds a widget to the homepage and selects a smart folder, such as “This month’s campaign” or “Press releases”. The widget then displays a rotating set of items that updates automatically as other team members add or change their content items. Developers, in turn, build this widget to pull content from these folders at runtime and ensure that the website stays in sync with editorial updates.
Additional details about smart folders:
  * Conditions are evaluated in real time as content changes.
  * A single item can belong to any number of smart folders.
  * They support organizing content in the admin interface, as editors don’t need to sort content items manually.
  * Dynamic data retrieval and display help editors manage campaign content, seasonal assets, or status-based workflows.
  * They can be used to dynamically filter and deliver content on website channels.


Currently, Xperience doesn’t support smart folder-based delivery for [headless](/documentation/developers-and-admins/configuration/headless-channel-management) or [email channels](/documentation/developers-and-admins/digital-marketing-setup/email-channel-management).
Using _smart folders_ helps large teams stay organized and makes dynamic content filtering much easier to manage across growing libraries.
## Summary: Recommendations for modeling asset content types
When modeling content types that store different assets in Xperience, treat each media format as a distinct content structure with its purpose, metadata, and lifecycle. Let’s consider a few aspects that will help you design flexible and consistent asset content types that align with how the editor team creates and delivers their content.
  * **Model each media format separately.** Use dedicated content types for images, videos, PDFs, or audio files. This lets you define only the fields relevant to each asset type and keeps the UI clean for editors.
  * **Use an image asset type as your baseline.** _Image_ content types benefit from Xperience’s built-in features, like responsive variants, focal point cropping, and automatic transformations. Most projects will need this type from the start.
  * **Add controlled asset types only where needed.** Use a _Restricted image_ content type when legal, licensing, or technical constraints prevent automated resizing or transformation. Keep this structure lean and purpose-driven.
  * **Make use of asset selectors.** Include the [Combined content selector](/documentation/developers-and-admins/customization/extend-the-administration-interface/ui-form-components/reference-admin-ui-form-components#combined-content-selector) in other content types to reference assets to avoid data duplication, support reuse, and ensure consistency.
  * **Tag assets using taxonomies.** Taxonomies support dynamic filtering, content personalization, and campaign grouping. Editors can apply tags directly in the asset form, and developers can query by tag.
  * **Structure for editorial clarity, not just technical needs.** Collaborate with marketers, content designers, and legal teams when defining which asset types to create and what each should include.
  * **Plan for growth.** Choose field names and structures that will scale with your library—avoid assumptions that only work for initial content volume or a single channel.


Using Xperience Content hub to store assets in purpose-built content types gives editors and developers more control over how files are described, used, and delivered. It ensures that your content model remains flexible and scalable as your content library and team needs grow.
## Unmanaged files and when to use them
Not every file in your project has to become a content item. Some files make more sense to handle outside the content model, especially when they’re static, technical, or not part of the team’s editorial workflow.
Let’s look at what qualifies as an unmanaged file, when to use them, and how to organize them in your project.
### What are unmanaged files?
Unmanaged files aren’t created, edited, or referenced through the _Content hub_ or the Xperience content API. Instead, they are deployed directly as part of your project code or infrastructure.
Unmanaged files include:
  * **Front-end assets** – icons, favicons, fonts, CSS, JavaScript, or background images used for styling and layout.
  * **System or integration files** – configuration files, embedded PDFs, or attachments managed by a third-party service.
  * **Non-marketing assets** – internal-only resources that don’t need metadata, tagging, or structured reuse.


These files are not searchable, taggable, or retrievable via the [Xperience API](/documentation/developers-and-admins/api/content-item-api/reference-content-retriever-api), and editors cannot manage them through the content lifecycle.
### Where to store unmanaged files
Store unmanaged files in a separate location in your project’s directory structure. We recommend using subfolders under `~/assets/<project_name>/`.
Avoid placing unmanaged files in folders used by the _Content hub_ or legacy Media libraries. This helps prevent confusion and keeps deployment pipelines clean.
When deploying to a SaaS environment, unmanaged files need to be [deployed to Azure Blob storage](/documentation/developers-and-admins/deployment/deploy-to-the-saas-environment#create-a-deployment-package) as part of your application’s asset pipeline.
### When to use unmanaged files
Use unmanaged files when:
  * The file is tightly bound to your front-end layout or code.
  * There’s no need for editor access, tagging, or personalization.
  * The file doesn’t change often (e.g., logos, placeholder images, system PDFs).
  * The file is part of an integration that manages its storage lifecycle.


Avoid unmanaged files for anything that requires:
  * Metadata or structured categorization.
  * Reuse across multiple content items that editors might want to update from the UI.
  * Personalization, localization, or content security.
  * Editorial updates or workflows.


By keeping unmanaged files separate from content-managed assets, you simplify your content model and avoid unnecessary complexity for developers and editors alike.
## Summary
Modeling files as structured content items in _Content hub_ gives your project a strong foundation for flexibility, scalability, and long-term maintainability. Whether you’re managing images, PDFs, videos, or licensed assets, treating them as content opens the door to better editorial workflows, secure delivery, and content reuse across channels.
Let’s recap what this guide covered:
  * Use _Content hub_ to store and manage assets as structured content items.
  * Model each media format, such as images, PDFs, videos, audio, with its content type.
  * Leverage built-in features for image optimization, responsive variants, and focal point cropping.
  * Use taxonomies and metadata to make assets discoverable and reusable.
  * Apply asset selectors to connect files to pages, components, or other content items.
  * Avoid Media libraries for new projects—they are being deprecated and lack critical features.
  * Use unmanaged files only for static or technical assets that don’t require structured management.


A well-structured asset model improves editors’ day-to-day content operations, strengthens content curation, supports compliance, and future-proofs your solution for personalization and multichannel delivery.
## What’s next?
You have learned about the options for storing data in Xperience. In the [Design content](/guides/architecture/content-modeling/content-modeling-guide/design-content) section, you’ll learn about different approaches to designing and presenting this data in your application.
![]()
[]()[]()
