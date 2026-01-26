---
source: https://docs.kentico.com/documentation/developers-and-admins/digital-commerce-setup/model-product-catalog
scrape_date: 2026-01-26
---

  * [Home](/documentation)
  * [Developers and admins](/documentation/developers-and-admins)
  * [Digital commerce setup](/documentation/developers-and-admins/digital-commerce-setup)
  * Product catalog 


# Product catalog
**Advanced license required**   
  
Features described on this page require the Xperience by Kentico **Advanced** license tier. 
When designing the content model for the product catalog, we recommend using content storage options provided by Xperience instead of developing custom classes or modules.
**Content modeling guide**
We recommend that you familiarize yourself with general content modeling recommendations and product features of Xperience by Kentico. Reading the [content modeling guide](/guides/architecture/content-modeling/content-modeling-guide) will help you understand this page.
For a practical implementation example, see [Example product catalog](/documentation/developers-and-admins/digital-commerce-setup/model-product-catalog/example-product-catalog) or browse the digital commerce implementation in the [DancingGoat sample project](/documentation/developers-and-admins/installation#available-project-templates).
There are two options when it comes to storing your product catalog. Either you can store products as [reusable content items](/documentation/business-users/content-hub/content-items) in the [content hub](/documentation/business-users/content-hub), or you can store them as [pages](/documentation/business-users/website-content) in a [website channel](/documentation/developers-and-admins/configuration/website-channel-management).
|  Content hub |  Website channel  
---|---|---  
Content storage |  Products are stored in the [content hub](/documentation/business-users/content-hub) as [reusable content items](/documentation/business-users/content-hub/content-items). This approach enables you to use all content hub features, but requires you to create a page for every product. Although, this process can be automated. |  Products are stored in a [website channel](/documentation/developers-and-admins/configuration/website-channel-management) as [pages](/documentation/business-users/website-content). This approach is best suited for scenarios when the store is tightly connected to the website channel.  
Catalog organization |  All content hub features are available: [workspaces](/documentation/developers-and-admins/configuration/users/role-management/workspaces), [folders](/documentation/business-users/content-hub/content-hub-folders#content-folders), [smart folders](/documentation/business-users/content-hub/content-hub-folders#smart-folders). |  You can use the content tree structure for organization of the product catalog.  
Categorization |  You can use [content types](/documentation/developers-and-admins/development/content-types) (with [reusable field schemas](/documentation/developers-and-admins/development/content-types/reusable-field-schemas)) and [taxonomies](/documentation/developers-and-admins/configuration/taxonomies) (or their combination) for categorization of the product catalog. It is not recommended to use the content tree structure for categorization of products, except for when the number of products is very small.  
## General recommendations
The following recommendations are valid regardless of the selected catalog storage option:
  * Use [reusable field schemas](/documentation/developers-and-admins/development/content-types/reusable-field-schemas) for common product fields. The schema should contain all fields that are used when displaying products in a listing. For more information, see [Product fields](#product-fields). 
    * _ProductFields_ (reusable field schema) 
      * _ProductFieldsName_
      * _ProductFieldsDescription_
      * _ProductFieldsPrice_
      * …
  * Create [content types](/documentation/developers-and-admins/development/content-types) for individual product types depending on required product fields: 
    * _Book_ (product type) 
      * _ProductFields_ (reusable field schema)
      * _BookAuthor_
      * _BookCoverType_
      * _BookGenre_
      * …
    * _Reader_ (product type) 
      * _ProductFields_ (reusable field schema)
      * _ReaderManufacturer_
      * _ReaderStorageSize_
      * …
    * _Accessory_ (general product without any extra fields) 
      * _ProductFields_ (reusable field schema)
  * You can use [reusable field schemas](/documentation/developers-and-admins/development/content-types/reusable-field-schemas) for sub-groups of product types. For example, all clothing has common properties in addition to the basic product fields: 
    * _ClothingProductFields_ (reusable field schema) 
      * _ClothingProductFieldsColor_
      * _ClothingProductFieldsSize_
      * _ClothingProductFieldsMaterial_
      * _ClothingProductFieldsManufacturer_
  * A field storing the product manufacturer can be separated into a [reusable field schema](/documentation/developers-and-admins/development/content-types/reusable-field-schemas)


## Product variants
It is recommended to model product variants as “child” items of a parent product, which holds all the shared information. Create a dedicated product content type for product variants and include only the fields that need to be overwritten from the parent. We recommend defining [reusable field schemas](/documentation/developers-and-admins/development/content-types/reusable-field-schemas) that group the modified fields, so you can easily reuse them across different types of variants.
A sample configuration can look like the following:
  * _ProductSizeFields_ (reusable field schema) 
    * _ProductSizeFieldsValue_
  * _Shirt_ (parent product type) 
    * _ProductFields_ (reusable field schema)
    * _ShirtMaterial_
    * _ShirtColor_
  * _ShirtSizeVariant_ (variant product type) 
    * _ProductSKU_ (reusable field schema)
    * _ProductStock_ (reusable field schema)
    * _ProductSizeFields_ (reusable field schema)


Note that the parent product doesn’t need to store an [SKU](#sku-field), since you cannot buy a shirt without specifying the variant. On the other hand, the variant product doesn’t need to store product fields, since the shirts are identical except for their size.
### Variants in the content hub
When storing products as [reusable content items](/documentation/business-users/content-hub/content-items) in the [content hub](/documentation/business-users/content-hub), the parent product should include a _Variants_ field configured as a [combined content selector](/documentation/developers-and-admins/customization/extend-the-administration-interface/ui-form-components/reference-admin-ui-form-components#combined-content-selector) restricted to the product variant content type. This allows you to clearly link variants to their parent. The parent product also you to create a page to provide a public URL.
Product variants appear in the same flat list as the parent products. To organized products, use clear naming conventions. We recommend to name the variant using the parent product’s name followed by a distinguishing suffix, for example: “Striped grey T-shirt - M”. Since product variants typically don’t require their own public URLs, there’s no need to create a page for them.
### Variants in website channels
When storing products as [pages](/documentation/business-users/website-content) in a [website channel](/documentation/developers-and-admins/configuration/website-channel-management), use the content tree to reflect the parent-child relationship between products and their variants by placing variants under the parent product. The parent product acts as the main product entry and should have its own URL.
For product variant content types, you can disable the _Include in routing_ setting, as they usually don’t need to be accessed through a separate URL. Follow the same naming recommendation: use the parent product name and add a variant-specific suffix: “Striped grey T-shirt - M”.
## Product fields
The base product [reusable field schema](/documentation/developers-and-admins/development/content-types/reusable-field-schemas) should contain all fields that are used when displaying products in a listing on your storefront. This enables you to easily work with the products programmatically and display all products in a universal way.
Recommended product fields are:
  * _ProductFields_ (reusable field schema) 
    * _ProductFieldsName_
    * _ProductFieldsDescription_
    * _ProductFieldsPrice_
    * _ProductFieldsImage_
    * _ProductFieldsCategory_
    * _ProductFieldsTags_


## SKU field
An SKU (Stock Keeping Unit) is a unique code used to identify and track a specific product or product variant in a store’s inventory.
Create a [reusable field schema](/documentation/developers-and-admins/development/content-types/reusable-field-schemas) with a SKU field:
  * _ProductSKU_ (reusable field schema) 
    * _ProductSKUCode_


The SKU field can be included in the _Product fields_ reusable field schema. However, when implementing [product variants](#product-variants), you may want to have a distinct SKU for each product variant, but only need _Product fields_ for the parent product.
## Product stock
See [Model product stock](/documentation/developers-and-admins/digital-commerce-setup/model-product-catalog/model-product-stock) for details on implementing product stock management.
## SEO fields
If you use content hub as the storage for your product catalog and you want to automatically generate pages for each product, it is recommended to store SEO fields (e.g., title, description, keywords) together with the product in the content hub, so that you can edit all properties of a product in one place.
![]()
[]()[]()
