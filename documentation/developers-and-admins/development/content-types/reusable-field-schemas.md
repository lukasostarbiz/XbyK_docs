# Reusable field schemas
  * [ Copy page link ](documentation/developers-and-admins/development/content-types/reusable-field-schemas#) | [Get HelpService ID](documentation/developers-and-admins/development/content-types/reusable-field-schemas#)
Core MVC 5


[✖](documentation/developers-and-admins/development/content-types/reusable-field-schemas# "Close page link panel") [Copy to clipboard](documentation/developers-and-admins/development/content-types/reusable-field-schemas#)
When modeling site content, you may encounter situations where specific groups of fields keep repeating across content types. For example, each web page content type might need a basic set of fields such as title, date, and excerpt text. Or a set of fields dedicated to search engine optimization and social media promotion, such as for the Open Graph protocol or a specific platform (Twitter, etc.).
Reusable field schemas save you from having to repeatedly define similar fields across multiple content types. Instead, each schema can define a set of fields, often dedicated to a specific use case or scenario, that you then link to content types as required. All changes you make to a schema – adding, removing, or renaming a field, for example – get immediately reflected in all linked content types.
## Create and assign reusable field schemas
  1. Open the **Content types** application and switch to the **List of reusable field schemas** tab.
  2. Select **New reusable field schema**.
  3. Enter the schema display name, code name, and description
  4. **Save** the schema.
  5. On the **Fields** tab, define schema fields using the [field editor](documentation/developers-and-admins/customization/field-editor). 
     * There are a couple of things to keep in mind when naming fields – see [Field naming guidelines](documentation/developers-and-admins/development/content-types#field-naming-guidelines)
     * Note: We strongly recommend prefixing all field names with the name of the reusable schema. For example, for a schema **SEOFields** , use **SEOFieldsTitle** , **SEOFieldsDescription** , **SEOFieldsTags** , and so on. This is to prevent column name conflicts between different schemas. See [Remarks](documentation/developers-and-admins/development/content-types/reusable-field-schemas#remarks) for details.


To assign created schemas to a content type:
  1. Edit a content type in the **Content types** application.
  2. On the **Fields** tab, select **Add reusable schema**.
  3. Select the schemas you want to assign and **Confirm**.
  4. [Regenerate code files](documentation/developers-and-admins/api/generate-code-files-for-system-objects) for the modified content type to access the added schema fields in your model classes.


This adds new bar items with the _Reusable field schema_ label to the content type field listing. Each item represents a single schema and lists all fields that it contains.
[![Reusable schema indicated on the Fields tab](docsassets/documentation/reusable-field-schemas/reusableSchemaItem.png)](https://docs.kentico.com/docsassets/documentation/reusable-field-schemas/reusableSchemaItem.png)
The system treats fields assigned via reusable schemas as regular content type fields. You can retrieve and filter them via [Content Item](documentation/developers-and-admins/api/content-item-api) and [GraphQL](documentation/developers-and-admins/development/content-retrieval/retrieve-headless-content) queries or access them [using macros in email templates](documentation/developers-and-admins/digital-marketing-setup/email-templates#content-type-field-macros).
## Unassign reusable field schemas
To unassign a reusable field schema, remove it via the content type **Fields** tab when editing content types in the **Content types** application.
  1. Open the **Content types** application and edit a content type.
  2. Switch to the fields tab.
  3. Find schema fields by the _Reusable field schema_ label and remove them using the recycle bin (
     * Unassigning a schema also **deletes all data** stored by the removed schema fields for the corresponding content type.


## Delete reusable field schemas
  1. Open the **Content types** application and switch to the **List of reusable field schemas** tab.
  2. Delete the schema using the recycle bin (
     * Note that you cannot delete a schema that is currently assigned to at least one content type. Unassign the schema from all content types first.


This removes the schema from the system.
## Remarks
### Field count limitations
All schema fields are stored in the _CMS_ContentItemCommonData_ table. SQL tables have a maximum limit of 1024 columns per table. Therefore, a hard limit exists on the number of columns that can be defined for reusable schemas in a single instance. 
### Field name collisions
All schema fields are stored in the _CMS_ContentItemCommonData_ table. When defining schema fields, prefix each field with the name of the corresponding schema to prevent column name collisions on the SQL level.