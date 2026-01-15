# API
  * [ Copy page link ](documentation/developers-and-admins/api#) | [Get HelpService ID](documentation/developers-and-admins/api#)
Core MVC 5


[✖](documentation/developers-and-admins/api# "Close page link panel") [Copy to clipboard](documentation/developers-and-admins/api#)
This section contains articles related to working with various Xperience by Kentico APIs.
  * [Content API](documentation/developers-and-admins/api/content-item-api)
    * [Content item query API](documentation/developers-and-admins/api/content-item-api/content-item-query-api)
    * [Reference - Content item query](documentation/developers-and-admins/api/content-item-api/reference-content-item-query)
    * [ContentRetriever API](documentation/developers-and-admins/api/content-item-api/content-retriever-api)
    * [Reference - ContentRetriever API](documentation/developers-and-admins/api/content-item-api/reference-content-retriever-api)
    * [Content item asset upload API](documentation/developers-and-admins/api/content-item-api/content-item-asset-upload-api)
    * [Content item database structure](documentation/developers-and-admins/api/content-item-api/content-item-database-structure)
  * [ObjectQuery API](documentation/developers-and-admins/api/objectquery-api)
  * [Database table API](documentation/developers-and-admins/api/database-table-api)
  * [Files API and CMS.IO](documentation/developers-and-admins/api/files-api-and-cms-io)
    * [File system providers](documentation/developers-and-admins/api/files-api-and-cms-io/file-system-providers)
      * [Azure Blob storage](documentation/developers-and-admins/api/files-api-and-cms-io/file-system-providers/azure-blob-storage)
      * [Amazon S3](documentation/developers-and-admins/api/files-api-and-cms-io/file-system-providers/amazon-s3)
      * [Custom file system providers](documentation/developers-and-admins/api/files-api-and-cms-io/file-system-providers/custom-file-system-providers)
  * [Generate code files for system objects](documentation/developers-and-admins/api/generate-code-files-for-system-objects)
  * [Use the Xperience by Kentico API externally](documentation/developers-and-admins/api/use-the-xperience-by-kentico-api-externally)


Additionally, you can view the [Xperience by Kentico API Examples](api/), which is a code snippet library that demonstrates how to use the API to perform basic operations (creating, retrieving, updating, deleting, etc.) for common object types.
You can find the full API Reference for Xperience by Kentico at: <https://api-reference.kentico.com/>
**API usage guidelines**
See [API usage](documentation/developers-and-admins/customization/stable-customization-guidelines#api-usage) for information on using the Xperience by Kentico API in a way that minimizes problems when updating to newer versions.
For example, API contained in namespaces with the **.Internal** suffix is not intended for public use. Such API should be considered unstable and subject to breaking changes during **any update** (even outside of major releases).