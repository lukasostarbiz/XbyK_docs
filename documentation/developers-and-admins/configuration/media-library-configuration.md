---
source: https://docs.kentico.com/documentation/developers-and-admins/configuration/media-library-configuration
scrape_date: 2026-01-22
---

  * [Home](/documentation)
  * [Developers and admins](/documentation/developers-and-admins)
  * [Configuration](/documentation/developers-and-admins/configuration)
  * Media library configuration 


# Media library configuration
**Media libraries sunset**
Media libraries have been officially sunset, and their related API members have been marked as obsolete. Support for media libraries will continue for one more year (until July 24, 2026), after which the feature and all associated APIs will be completely removed.
See [Media library migration](/guides/architecture/media-libraries-migration-guidance) for instructions on how to migrate your media library files to [Content hub](/documentation/business-users/content-hub).
Using the Media libraries application, users can store multimedia files such as photos, pictures, sound files, videos, package files, or documents. The maximum size allowed for a single file is 100 MB.
This page covers media library configuration options you may wish to use in your applications:
  * [Supported file types](#supported-file-types)
  * [Enable upload of custom file types](#enable-upload-of-custom-file-types)
  * [Image file request protection](#image-file-request-protection)


## Supported file types
Media libraries support the following types of images: **bmp** , **gif** , **png** , **jpg** , **jpeg** , **tiff** , **tif** , **webp** , **avif**. These types of image files can be uploaded to media libraries and displayed in your channels by default. Support for **svg** images can be enabled after considering the [potential security impacts](#support-for-svg-images).
The system detects audio and video files based on their [MIME type](https://developer.mozilla.org/en-US/docs/Web/HTTP/Basics_of_HTTP/MIME_types) (audio types starting with the  _audio/_ prefix and video types starting with the  _video/_ prefix). For successfully detected audio and video file types, the system renders appropriate HTML5 markup. The types of files that can be previewed or played on your website therefore depend on the web browser (modern HTML5 web browsers typically support one or more of the **ogg** , **mp4** , or **webm** video codecs, and **mp3** , **wav** , or **ogg** audio codecs).
**Non-image file types**
All other file types are recognized as **documents** , which means that you cannot play them as videos or display them as images. You can still store them in the library and provide download links to visitors.
**Limitations for avif images in media libraries**
Media library [resizing functionality](/documentation/developers-and-admins/development/content-retrieval/retrieve-content-from-media-libraries#resize-images) is not supported for image files with the avif format. Additionally, media libraries cannot calculate the size of avif images and their resolution is displayed as “0x0 px”.
## Enable upload of custom file types
If you want to configure the system to allow upload of a custom file type into media libraries, you need to:
  1. [Enable the custom extensions](#customize-allowed-extensions) to be uploaded to the media library.
  2. (_Optional_) If you want to use a custom extension as an image file, you need to configure the system to [recognize the extension as an image file](#specify-image-file-types).


### Customize allowed extensions
To customize the set of file types that can be uploaded into media libraries:
  1. Open the **Settings** application.
  2. Navigate to the **Content → Assets** category.
  3. Add the selected file extensions into the **Asset allowed extensions** setting in the **Security** section (separated by semicolons).
  4. **Save** the changes.


**Setting shared with the content hub**
The **Asset allowed extensions** setting is shared with the [content hub](/documentation/developers-and-admins/configuration/content-hub-configuration). Changing the value of the setting also limits file types that can be uploaded to the content hub.
### Specify image file types
By default, the system can recognize and process any media type, if you have the right player for the given file type. You can allow custom file types to be recognized as image files via a configuration key. However, by specifying image file types, you disable the automatic detection and support for media file types other than those explicitly added via your configuration.
To allow media files of specified types to be recognized as image files, add the `CMSImageExtensions` key to your application’s configuration file (_appsettings.json_ by default).
JSON
Copy
```
"CMSImageExtensions": "bmp;gif;ico;png;wmf;jpg;jpeg;tiff;tif;webp;svg;avif;jfif;jfi;jif;jpe;dib"
```

Files of the specified types are now recognized as image files and will be, for example, offered in the _Insert image_ dialog of the rich text editor.
**Unsupported features for vector graphic formats**
Vector graphic formats other than **svg** (such as **eps** , **pdf** , **ai**) are not supported as image extensions in media libraries. These formats can be stored as documents and displayed on the website, but the preview functionality in the **Media libraries** application and asset selectors is unavailable for such files.
### Support for SVG images
Although the [SVG](https://en.wikipedia.org/wiki/SVG) vector format for images is supported by media libraries, it is not allowed by default. SVG images may host scripts or CSS and can pose a security risk. If you wish to use the SVG image format, you need to manually [enable support for the extension](#customize-allowed-extensions).
The system does **not** perform sanitization of SVG images when they are [uploaded](/documentation/business-users/media-libraries/manage-media-files#upload-files-into-the-media-library). This means that SVG files stored in media libraries may potentially contain malicious content. The system protects against these risks by adding a [CSP header](https://developer.mozilla.org/en-US/docs/Web/HTTP/CSP) to all responses that serve files through the system’s endpoints (_/getmedia_ , etc.). This header prevents execution of inline scripts.
If your project uses custom endpoints or other functionality to serve images, you need to provide your own security measures.
When displaying SVG images in custom HTML, always use the `<img>` element and its `src` attribute, rather than including the SVG code directly in the HTML.
Also consider additional security measures for SVG images if you have files mapped to external storage providers, such as [Azure storage](/documentation/developers-and-admins/api/files-api-and-cms-io/file-system-providers/azure-blob-storage) or [Amazon S3](/documentation/developers-and-admins/api/files-api-and-cms-io/file-system-providers/amazon-s3). If files in external storage are accessed outside of Xperience endpoints, the system cannot provide any protection, and security depends on the features of the given storage provider.
## Image file request protection
The system allows you to protect the media library image file endpoint (i.e., _/getmedia_), so that access to resized variants of image files is restricted only to validated requests.
The protection works by adding a validation hash at the end of requests to system handlers that contain resize parameters for image files. When the request protection is enabled, a request to _example.com/getmedia/1d35f70d-…-3a3a25bcf363/E5yfzxe.jpg?width=200_ would return the image in the default size. To retrieve the image in the desired size, you need to generate a validation hash for the request URL: _example.com/getmedia/1d35f70d-…-3a3a25bcf363/E5yfzxe.jpg?width=200 &mediaprotectionhash=94f…fcf_
If performance is crucial, it is recommended to use a different approach to image resizing, e.g., a CDN with resizing capabilities.
Content authored in the system includes the validation hash. For content managed outside of the system (e.g., links to image files from stylesheets), you need to make sure that all image requests with the resize query parameter include the validation hash. To generate a validation hash for image file requests:
  1. Make sure that the request protection is enabled. In the administration interface, enable the **Settings → Content → Assets → Enable image file request protection** setting.
  2. Generate the validation hash using the `GetProtectedUrl` method of the `IMediaProtectionService`.
     * Both relative and absolute request URLs are supported.
C#
**Generate validation hash for a request**
Copy
```
// An instance of the `IMediaProtectionService` obtained using the dependency injection
IMediaProtectionService mediaProtectionService;

// Retrieves a requests URL with a validation hash
var urlWithHash = mediaProtectionService.GetProtectedUrl("~/getmedia/1d35f70d-dcd8-4dc8-a528-3a3a25bcf363/E5yfzxe.jpg?width=201");
```



**Validation hash and hash string salt**
Values of the validation hash are dependent on the [hash string salt](/documentation/developers-and-admins/configuration/macro-expressions/macro-signatures#configure-the-hash-salt-for-macro-signatures) value configured for the environment. It is crucial to have the value of the hash string salt synchronized across different environments. If the value of the hash string salt changes, all static generated request URLs (e.g., static CSS files and scripts) with the validation hash need to be updated and generated again.
### Enable image file request protection on existing projects
When you enable the request protection on an existing project, you also need to ensure that any affected URLs in already existing content (i.e., any URLs to image files with resizing parameters) are updated to contain the validation hash. Otherwise, all affected URLs without a validation hash return image files in the original dimensions.
  * Affected URLs in custom code need to be validated using the `GetProtectedUrl` method of the `IMediaProtectionService`.
  * Affected URLs in static files need to be manually validated and replaced with a URL with the validation hash.
  * Depending on the customizations in your project, you may also need to update the affected URLs in existing content to be converted to URLs with the validation hash.


![]()
[]()[]()
