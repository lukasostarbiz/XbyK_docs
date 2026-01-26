---
source: https://docs.kentico.com/documentation/developers-and-admins/development/content-retrieval/retrieve-content-from-media-libraries
scrape_date: 2026-01-26
---

  * [Home](/documentation)
  * [Developers and admins](/documentation/developers-and-admins)
  * [Development](/documentation/developers-and-admins/development)
  * [Content retrieval](/documentation/developers-and-admins/development/content-retrieval)
  * Retrieve content from media libraries 


# Retrieve content from media libraries
**Media libraries sunset**
Media libraries have been officially sunset, and their related API members have been marked as obsolete. Support for media libraries will continue for one more year (until July 24, 2026), after which the feature and all associated APIs will be completely removed.
See [Media library migration](/guides/architecture/media-libraries-migration-guidance) for instructions on how to migrate your media library files to [Content hub](/documentation/business-users/content-hub).
Media libraries serve as one type of data storage for your application. They can store different types of audio, video, image, and document files. For a complete list of the file types supported by default, see [Media library configuration](/documentation/developers-and-admins/configuration/media-library-configuration).
This page covers how you can:
  * [Retrieve media library files](#retrieve-media-library-files)
  * [Get media file URLs](#get-media-file-urls)
  * [Display media files](#display-media-library-files)


## Retrieve media library files
The following example demonstrates how to retrieve files from Xperience media libraries in MVC applications. The example uses a media library with the code name _SampleMediaLibrary_. For this example to function, substitute the code name with any media library from your solution.
  1. Open Xperience project in Visual Studio.
  2. Create a view model class representing media files. Define properties for storing the media file values that you wish to use in your views.
  3. Create a new controller class or edit an existing one.
  4. Implement a GET action method to retrieve media files. This example retrieves .jpg files from the _SampleMediaLibrary_ media library.
  5. Use the view model to pass media file data to your views.


C#
**Required using statements**
Copy
```
using System.Collections.Generic;
using System.Linq;

using Microsoft.AspNetCore.Mvc;

using CMS.MediaLibrary;
using CMS.Base;

using Kentico.Content.Web.Mvc;
```

C#
**Retrieve media files in a controller action**
Copy
```
private readonly IMediaFileUrlRetriever mediaFileUrlRetriever;
private readonly IInfoProvider<MediaLibraryInfo> mediaLibraryInfoProvider;
private readonly IInfoProvider<MediaFileInfo> mediaFileInfoProvider;

// Initializes instances of required services using dependency injection
public MediaLibraryController(IMediaFileUrlRetriever mediaFileUrlRetriever,
                              IInfoProvider<MediaLibraryInfo> mediaLibraryInfoProvider,
                              IInfoProvider<MediaFileInfo> mediaFileInfoProvider)
{
    this.mediaFileUrlRetriever = mediaFileUrlRetriever;
    this.mediaLibraryInfoProvider = mediaLibraryInfoProvider;
    this.mediaFileInfoProvider = mediaFileInfoProvider;
}

/// <summary>
/// Retrieves media files with the .jpg extension from the 'SampleMediaLibrary'.
/// </summary>
public IActionResult ShowMediaFiles()
{
    // Gets an instance of the 'SampleMediaLibrary' media library
    MediaLibraryInfo mediaLibrary = mediaLibraryInfoProvider.Get("SampleMediaLibrary");

    // Gets a collection of media files with the .jpg extension from the media library
    IEnumerable<MediaFileInfo> mediaLibraryFiles = mediaFileInfoProvider.Get()
                                .WhereEquals("FileLibraryID", mediaLibrary.LibraryID)
                                .WhereEquals("FileExtension", ".jpg");

    // Prepares a collection of view models containing required data of the media files
    IEnumerable<MediaFileViewModel> model = mediaLibraryFiles.Select(
            mediaFile => {
                IMediaFileUrl fileUrl = mediaFileUrlRetriever.Retrieve(mediaFile);
                return new MediaFileViewModel
                {
                    FileTitle = mediaFile.FileTitle,
                    // Gets the relative path to the media file
                    RelativeUrl = fileUrl.RelativePath
                };
            }
    );

    // Passes the model to the view
    return View(model);
}
```

## Get media file URLs
To resolve the URLs of retrieved media library files (`MediaFileInfo` objects), use the `IMediaFileUrlRetriever` service from the `Kentico.Content.Web.Mvc` namespace. The service exposes a `Retrieve` method that takes a `MediaFileInfo` object as its parameter and returns an `IMediaFileUrl` instance with the following properties:
  * `RelativePath` – the application relative (starting with `~/`) permanent path to the file. For example: _~/getmedia/0140bccc-9d47-41ea-94a9-ca5d35b2964c/sample_image.jpg_. This format ensures that the image remains accessible if the file is renamed or moved to a different media library or directory on the file system.
  * `DirectPath` – the direct path to the media file on the file system. For example: _~/MediaLibraryFolder/sample_image.jpg_. These URLs change whenever the file is renamed or moved to a different media library (directory on the file system).
  * `QueryStringParameters` – a collection of query string parameters appended to the URL.
  * `IsImage` – a boolean flag indicating whether the URL leads to an image file. See [Resize images](#resize-images).


## Resize images
**Image processing**
Image operations are provided by _IImageProcessingService_ from the [Kentico.Xperience.ImageProcessing NuGet package](/documentation/developers-and-admins/development/website-development-basics/configure-new-projects/xperience-by-kentico-nuget-packages). The package doesn’t guarantee compatibility with niche distributions and platforms. If you run into issues with image processing on the hosting platform of your choice, you will need to provide your own implementation of `IImageProcessingService` registered using the [RegisterImplementation attribute](/documentation/developers-and-admins/customization/integrate-custom-code).
**Resizing not supported for avif images**
The default _IImageProcessingService_ used for resizing media library images does not support the avif image format.
You can resize images via extension methods for the `IMediaFileUrl` object. The methods add query string parameters that change the URL behavior and allow you to customize the format of the files.
The following extensions are available:
  * `WithSizeConstraint` – used to resize image media files. You can resize image files via the `SizeConstraint` parameter. Note, however, that this parameterization never upscales the image. The following image size constraints are available: 
    * `Empty` – leaves the image unchanged.
    * `Height(100)` – resizes the image to the specified height (maintains aspect ratio).
    * `Width(100)` – resizes the image to the specified width (maintains aspect ratio).
    * `MaxWidthOrHeight(100)` – resizes the image so that its width and height do not exceed the specified value (maintains aspect ratio).
    * `Size(100, 120)` – resizes the image to the specified width and height. Doesn’t maintain the aspect ratio.
It’s not possible to resize image files via the `SizeConstraint` parameter when using **direct URLs**. Files accessed via direct URLs are processed directly by the server, skipping application processing entirely.


### Set the encoding quality for resized images
You can configure the encoding quality of resized images via the `EncodingQuality` option of the `ImageProcessingOptions` class. The option accepts values from the range 0 - 100. Lower values indicate stronger compression that results in smaller, lower quality images. The default implementation sets the threshold to **80** and uses [SKImage.Encode](https://learn.microsoft.com/en-us/dotnet/api/skiasharp.skimage.encode) to perform the encoding, which skips the compression step for lossless and unsupported image formats.
Set the configuration during application startup:
C#
**Program.cs**
Copy
```
using Kentico.Xperience.ImageProcessing;

var builder = WebApplication.CreateBuilder(args);

...

builder.Services.AddOptions<ImageProcessingOptions>()
    // Sets the quality level for resized images to 90%, resulting in a barely noticeable loss of image quality
    // (only the highest frequencies from the image are filtered) and a roughly 30-40% size reduction
    .Configure(o => o.EncodingQuality = 90);
```

## Display media library files
To display media library files on your website, create views that work with the generated media file URLs. To convert relative URLs to their application absolute format, use methods from the framework’s `UrlHelper` class, such as `Url.Content`:
cshtml
**Display media files in Razor views**
Copy
```
@model IEnumerable<MediaFileViewModel>

<h2>Media library file listing</h2>

@foreach (MediaFileViewModel mediaFile in Model)
{
    @* Gets an application absolute URL for the media file *@
    string url = Url.Content(mediaFile.RelativeUrl);

    <img src="@url" alt="@mediaFile.FileTitle" />
}
```

![]()
[]()[]()
