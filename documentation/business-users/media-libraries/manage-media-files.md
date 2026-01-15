# Manage media files
  * [ Copy page link ](documentation/business-users/media-libraries/manage-media-files#) | [Get HelpService ID](documentation/business-users/media-libraries/manage-media-files#)
Core MVC 5


[✖](documentation/business-users/media-libraries/manage-media-files# "Close page link panel") [Copy to clipboard](documentation/business-users/media-libraries/manage-media-files#)
**Media libraries sunset**
Media libraries have been officially sunset, and their related API members have been marked as obsolete. Support for media libraries will continue for one more year (until July 24, 2026), after which the feature and all associated APIs will be completely removed.
See [Media library migration](guides/architecture/media-libraries-migration-guidance) for instructions on how to migrate your media library files to [Content hub](documentation/business-users/content-hub).
On this page, you can find instructions on how to manage [media library](documentation/business-users/media-libraries) files:
  * [Upload files into the media library](documentation/business-users/media-libraries/manage-media-files#upload-files-into-the-media-library)
  * [Update existing media library files](documentation/business-users/media-libraries/manage-media-files#update-existing-media-library-files)
  * [Move media files](documentation/business-users/media-libraries/manage-media-files#move-media-files)
  * [Delete files from a media library](documentation/business-users/media-libraries/manage-media-files#delete-files-from-a-media-library)
  * [Manage folders](documentation/business-users/media-libraries/manage-media-files#manage-folders)
  * [Edit media file properties and metadata](documentation/business-users/media-libraries/manage-media-files#edit-media-file-properties-and-metadata)


## Upload files into the media library
The following scenario describes how to insert new files into media libraries. Media libraries are suited for storing any type of large files, not only of media character:
  1. Open a media library: 
    1. Open the **Media libraries** application.
    2. Select a media library.
  2. Insert files into the media library: 
    1. Select the **Browse** button.
    2. Select files you want to upload. You can also select multiple files at once.


The file is now available in the media library and can be used in a channel or made available via a direct link.
**Tip** : You can also drag and drop the files directly from the file explorer to a media library.
[![Upload file to a media library](docsassets/documentation/manage-media-files/media_library_upload.png)](https://docs.kentico.com/docsassets/documentation/manage-media-files/media_library_upload.png)
## Update existing media library files
You can update an existing media file by uploading an updated version of the file.
  1. Open a media library: 
    1. Open the **Media libraries** application.
    2. Select a media library.
  2. Select the file you want to replace and select the **Upload new version** button.


The new version of the file is now displayed in all locations where the media file is used.
[![Upload new version of a file](docsassets/documentation/manage-media-files/upload_new_version.png)](https://docs.kentico.com/docsassets/documentation/manage-media-files/upload_new_version.png)
## Move media files
You can move media files between folders within a media library. Moving a file does not change the file URL. Files cannot be moved between media libraries, you need to upload the file to each media library.
  1. Open a media library: 
    1. Open the **Media libraries** application.
    2. Select a media library.
  2. Select the file you want to move and select the **Move file** button.  
[![Move a media file](docsassets/documentation/manage-media-files/ML_move_file_btn.png)](https://docs.kentico.com/docsassets/documentation/manage-media-files/ML_move_file_btn.png)
  3. Select the folder where you want to move the file and select **Move**.  
[![Move file dialog](docsassets/documentation/manage-media-files/ML_move_file_dialog.png)](https://docs.kentico.com/docsassets/documentation/manage-media-files/ML_move_file_dialog.png)


## Delete files from a media library
You can delete files from media libraries directly in the **Media libraries** application:
  1. Open a media library: 
    1. Open the **Media libraries** application.
    2. Select a media library.
  2. Select the file you want to delete and select the **Delete** (


The file is now removed from the media library and is no longer available publicly.
[![Delete file from media library](docsassets/documentation/manage-media-files/media_library_delete.png)](https://docs.kentico.com/docsassets/documentation/manage-media-files/media_library_delete.png)
## Manage folders
You can create folders in media libraries to organize your files. Folders in a media library are also reflected in the file system. To create a folder in a media library:
  1. Open a media library.
    1. Open the **Media libraries** application.
    2. Select a media library.
  2. Navigate to a location in the media library where you want to create the new folder.
  3. Select **Add new folder** and fill in the folder name.
     * The folder name can contain only alphanumeric characters, underscores (‘ ___ ’), hyphens (‘ _-_ ’), and cannot contain file names [reserved by the operating system](https://learn.microsoft.com/en-us/windows/win32/fileio/naming-a-file) (e.g., ‘ _CON_ ’, ‘ _PRN_ ’, ‘ _AUX_ ’ for Windows).
  4. **Save** the folder.


Media files can now be uploaded into the new folder. You can also rename the folder by selecting the **More actions** (
[![Add folder in a media library](docsassets/documentation/manage-media-files/ML_add_folder.png)](https://docs.kentico.com/docsassets/documentation/manage-media-files/ML_add_folder.png)
## Edit media file properties and metadata
You can edit the properties and metadata of media files in the **Media libraries** application:
  1. Open a media library. 
    1. Open the **Media libraries** application.
    2. Select a media library.
  2. Select the desired file to display a panel with file properties and metadata.
  3. Edit the values and select **Save** once you are done.


[![Media file metadata](docsassets/documentation/manage-media-files/media-library-file-properties.png)](https://docs.kentico.com/docsassets/documentation/manage-media-files/media-library-file-properties.png)