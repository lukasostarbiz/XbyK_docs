---
source: https://docs.kentico.com/documentation/developers-and-admins/ci-cd/ci-cd-repository-structure
scrape_date: 2026-01-22
---

  * [Home](/documentation)
  * [Developers and admins](/documentation/developers-and-admins)
  * [CI/CD](/documentation/developers-and-admins/ci-cd)
  * CI/CD repository structure 


# CI/CD repository structure
When using [Continuous Integration](/documentation/developers-and-admins/ci-cd/continuous-integration) or [Continuous Deployment](/documentation/developers-and-admins/ci-cd/continuous-deployment), the system serializes database data into XML and stores the results on the file system in a repository folder.
The files are further organized within the following folder structure:
  * **Channel level folders** – the top level separates objects based on their relationship with channels: **@global** for global objects, **< channel code name>** for content and configuration objects related to individual channels (such as pages or emails).
    * **Object type folders** – the next level contains folders for specific types of objects, based on **_object type names_** (see [Reference - CI/CD object types](/documentation/developers-and-admins/ci-cd/reference-ci-cd-object-types) to learn more).
      * **Object files** – individual XML files are named according to the **_code name_** of the corresponding object. Object types without code names use the value of a non-unique field, followed by a unique hash. Object types without any easily readable identifying fields use their GUID value for the file name.
      * **Binding data files** – binding data is not stored in separate files for individual binding records, but combined for each main object in the relationship. The binding XML data contains an identifier of the main object (parent) in the relationship, and then identifiers of all objects that have the given relationship with the main object, along with any other data stored by the binding. The binding files are named after the main object in format: **_< object name>@<unique hash>_**
      * **Parent object folders** – object types that belong under a parent type use an additional level of subfolders – objects are separated into folders named according to the parent object in format: **_< parent object name>@<unique hash>_**. For object types that have multiple possible parent types, the folder name also contains a **_< parent object type>__** prefix.
      * **Separated field files** – certain types of objects store the values of specific data fields in separate files (placed next to the main XML file). For example, content items with a _Content item asset_ field store the asset’s binary data as a separate file. The separated files use names in format **_< object name>#<separated field identifier>_** and have an appropriate extension.
  * **Lock files** – the repository contains a **.lock** folder with temporary lock files used during store and restore operations. You can safely delete or ignore this folder in your source control system.
  * **Repository configuration file** – the repository contains an XML configuration file (_repository.config_ by default), which is used to adjust the behavior or CI/CD operations and exclude object types or individual objects. See [Exclude objects from CI/CD](/documentation/developers-and-admins/ci-cd/configure-ci-cd-repositories).


**Long names and forbidden characters**
Very long folder and file names automatically replace the middle of the name with **..** characters, preceded and followed by a limited number of characters from the start and end of the name. To ensure the uniqueness of the name, an **@** symbol followed by a fixed-length hash value is appended.
For example:  _longpagetypeprefix..pagetypenameend@fe6fd25d3e.xml_
If an object name contains characters that are not allowed in file names (for example slashes or backslashes), the given characters are removed and the same unique hash is appended.
**Source control ignore rules**
Certain source control systems may have ignore rules, such as _gitignore_ , which can unintentionally exclude files or folders used in the CI/CD repository (for example, the _*.class_ and _*.user_ file and folder extensions).
Such rules may prevent your environment from working correctly. We recommend that you evaluate ignore rules for the repository and disable them as required.
## Example - Repository structure
**Note** : The example does not include all [object types supported by CI/CD](/documentation/developers-and-admins/ci-cd/reference-ci-cd-object-types).
App_Data\CIRepository
  * @global
    * cms.channel
      * dancinggoat.xml
      * dancinggoatemails.xml
    * cms.contentlanguage
      * en.xml
    * cms.contenttype
      * dancinggoat.articlepage.xml
      * dancinggoat.cafe.xml
      * dancinggoat.homepage.xml
    * cms.role
      * administrator.xml
      * dancinggoat.digitalchannelmanager.xml
    * contentitemdata.dancinggoat.cafe
      * newyork-xn4wcoi7@f9f4b7d19c 
        * 6f1f362b-6b10-4279-a43e-add609bfb952_en@9fe0105a7b.xml
  * DancingGoat
    * cms.contentitem
      * coffeebeveragesexplained-ouq66na8.xml
      * donatewithus-ib1owx14.xml
    * cms.contentitemcommondata
      * coffeebeveragesexplained-ouq66na8@229b3e934c 
        * 455ecded-6b64-485c-ad3c-d4b96effa019_en@e80ce4a807
      * donatewithus-ib1owx14@d2b2946b0a 
        * 9246d73c-ac38-4318-b31d-d6540369c730_en@35b7656f37
    * cms.contentitemlanguagemetadata
      * coffeebeveragesexplained-ouq66na8@229b3e934c 
        * 0a9c1cfb-bc49-4de7..bcb72a271515_en-us@198d133b1e.xml
      * donatewithus-ib1owx14@d2b2946b0a 
        * 70e50ccf-53a7-4d1c..f460b1926823_en-us@91887bc8f9.xml
    * cms.webpageitem
      * articles@6cbaf541d4 
        * articles_coffee_beverages_explained@57ca1005db.xml
        * articles_donate_with_us@aa902b35ab.xml
      * articles@53f6ddebaf.xml
    * cms.webpageurlpath
      * articles_coffee_beverages_explained@9afb1e62f2 
        * articles_coffee-beverages-explained_en@4ea0f3386e.xml
      * articles_donate_with_us@24682d9230 
        * articles_donate-with-us_en@c600fb80ee.xml
    * cms.websitechannel
      * dancinggoat@3d35796746 
        * 55e3ae4b-3843-46b8-95d5-79611c371d6f.xml
    * contentitemdata.dancinggoat.articlepage
      * coffeebeveragesexplained-ouq66na8@229b3e934c 
        * cd602cee-95c2-4e21-90f3-63e1fd7af896_en@08bac1e16a.xml
      * donatewithus-ib1owx14@d2b2946b0a 
        * 8309ebb9-f6d2-44fc-a4fe-27c3903b9a81_en@e15397089c.xml
  * DancingGoatEmails
    * …
  * repository.config


## Storage structure for specific object types
### Content items
The system uses multiple object types to store the content and settings of [content items](/documentation/business-users/content-hub). As a result, the resulting CI/CD files use a more complex structure than standard objects.
Content items are global objects serialized under the _@global_ folder. Each item is divided into multiple folders and files:
  * _cms.contentitem_ – stores general system information about the content item.
  * _cms.contentitemcommondata_ – stores metadata about the item such as the current workflow and version statuses. Organized under subfolders matching the parent content item.
  * _cms.contentitemlanguagemetadata_ – metadata for specific language variants of the item. Organized under subfolders matching the parent content item.
  * _contentitemdata. <content type code name>_ – data stored in the [fields](/documentation/developers-and-admins/customization/field-editor) of the item’s [content type](/documentation/developers-and-admins/development/content-types). Items with fields of the _Content item asset_ data type also contain the referenced asset binary next to the XML definition.


For example, a _“Bolivia Finca Illimani_ ” reusable content item of the _DancingGoat.Coffee_ content type could be, depending on the type’s fields and supported language variants, serialized into the following XML files:
App_Data\CIRepository
  * @global 
    * cms.contentitem 
      * boliviafincaillimani-c77ome92.xml
    * cms.contentitemcommondata 
      * boliviafincaillimani-c77ome92@4af5d5cd12 
        * b8f2c34d-9391-4ac4-aa7d-3e1918ae8436_en@288568df8a.xml
    * cms.contentitemlanguagemetadata 
      * boliviafincaillimani-c77ome92@4af5d5cd12 
        * bc0d5a69-0257-4bbf-9906-b3a6b6d5f867_en@6b1ab2f0c2.xml
    * contentitemdata.dancinggoat.coffee 
      * boliviafincaillima..7d-3e1918ae8436_en@f22af72b2b 
        * 734cce16-db74-4114-8a71-50cfcd1136e5.xml


### Pages
The system uses multiple object types to store the content and settings of [pages](/documentation/business-users/website-content). As a result, the resulting CI/CD files use a more complex structure than standard objects.
Pages are serialized under a specific [website channel](/documentation/developers-and-admins/configuration/website-channel-management). Each page is divided into multiple folders and files:
  * _cms.contentitem_ – stores general system information about the page.
  * _cms.contentitemcommondata_ – stores metadata about the page such as the current workflow and version statuses. Organized under subfolders matching the parent content item for the page.
  * _cms.contentitemlanguagemetadata_ – metadata for specific language variants of the page. Organized under subfolders matching the parent content item for the page.
  * _cms.webpageitem_ – data about the page’s position in the website channel’s content tree. Organized into subfolders matching the parent-child hierarchy of the page tree.
  * _cms.webpageurlpath_ – stores the [URL path](/documentation/business-users/website-content/manage-page-urls) data of the page. Organized under subfolders matching the parent content item for the page.
  * _contentitemdata. <content type code name>_ – data stored in the [fields](/documentation/developers-and-admins/customization/field-editor) of the page’s [content type](/documentation/developers-and-admins/development/content-types).


For example, a _“Coffee Beverages Explained”_ page with the _DancingGoat.ArticlePage_ content type under the _DancingGoat_ website channel could be, depending on the page’s [fields](/documentation/developers-and-admins/customization/field-editor) and language variants, serialized into the following XML files:
App_Data\CIRepository
  * DancingGoat 
    * cms.contentitem
      * coffeebeveragesexplained-ouq66na8.xml
    * cms.contentitemcommondata
      * coffeebeveragesexplained-ouq66na8@229b3e934c 
        * 455ecded-6b64-485c-ad3c-d4b96effa019_en@e80ce4a807.xml
    * cms.contentitemlanguagemetadata
      * coffeebeveragesexplained-ouq66na8@229b3e934c 
        * 0a9c1cfb-bc49-4de7-9cd6-bcb72a271515_en@198d133b1e.xml
    * cms.webpageitem
      * articles@6cbaf541d4 
        * articles_coffee_beverages_explained@57ca1005db.xml
    * cms.webpageurlpath
      * articles_coffee_beverages_explained@9afb1e62f2 
        * articles_coffee-beverages-explained@4ea0f3386e.xml
    * contentitemdata.dancinggoat.articlepage
      * coffeebeveragesexp..3c-d4b96effa019_en@878c71bd71
        * cd602cee-95c2-4e21-90f3-63e1fd7af896_en@08bac1e16a.xml


### Emails
The system uses multiple object types to store the content and settings of [emails](/documentation/business-users/digital-marketing/emails). As a result, the resulting CI/CD files use a more complex structure than standard objects.
Emails are serialized under a specific [email channel](/documentation/developers-and-admins/digital-marketing-setup/email-channel-management). Each email is divided into multiple folders and files:
  * _cms.contentitem_ – stores general system information about the email.
  * _cms.contentitemcommondata_ – stores metadata about the email such as the current workflow and version statuses. Organized under subfolders matching the parent content item for the email.
  * _cms.contentitemlanguagemetadata_ – metadata for specific language variants of the email. Organized under subfolders matching the parent content item for the email.
  * _contentitemdata. <content type code name>_ – data stored in the [fields](/documentation/developers-and-admins/customization/field-editor) of the email’s [content type](/documentation/developers-and-admins/development/content-types).
  * _emaillibrary.emailconfiguration_ – stores the email purpose and other assorted metadata.
  * _emaillibrary.sendconfiguration_ – sendout settings for [regular emails](/documentation/business-users/digital-marketing/emails/send-regular-emails-to-subscribers).
  * _emaillibrary.sendconfigurationcontactgroup_ – included and excluded [contact groups](/documentation/business-users/digital-marketing/contact-groups) in the sendount settings for [regular emails](/documentation/business-users/digital-marketing/emails/send-regular-emails-to-subscribers).


For example, a _“Dancing Goat Regular”_ email with the _DancingGoat.Email_ content type under the _DancingGoatEmail_ email channel could be, depending on the type’s [fields](/documentation/developers-and-admins/customization/field-editor), serialized into the following XML files:
App_Data\CIRepository
  * DancingGoatEmails 
    * cms.contentitem 
      * dancinggoatregular-jspvrlaw.xml
    * cms.contentitemcommondata 
      * dancinggoatregular-jspvrlaw@c9e8075026 
        * 556e64e1-33ad-4745..f8f325852_en_draft@536bc3c16d.xml
    * cms.contentitemlanguagemetadata 
      * dancinggoatregular-jspvrlaw@c9e8075026 
        * 6f1792c0-399f-49e5-b0c3-5b03e9607d47_en@e6c32b6adf.xml
    * emaillibrary.emailconfiguration 
      * dancinggoatregular-jspvrlaw.xml
    * emaillibrary.sendconfiguration 
      * dancinggoatregular-jspvrlaw@a8301b74b5 
        * 715d8987-f654-4bbc-9add-6f90add97072.xml
    * emaillibrary.sendconfigurationcontactgroup 
      * dancinggoatregular..-9cbf-562d43bcefb3@6edd158a18.xml


### Headless items
The system uses multiple object types to store the content and settings of [headless items](/documentation/business-users/headless-content). As a result, the resulting CI/CD files use a more complex structure than standard objects.
Headless items are serialized under a specific [headless channel](/documentation/developers-and-admins/configuration/headless-channel-management). Each item is divided into multiple folders and files:
  * _cms.contentitem_ and _cms.headlessitem_ – stores general system information about the headless item.
  * _cms.contentitemcommondata_ – stores metadata about the item such as the current workflow and version statuses. Organized under subfolders matching the parent headless item.
  * _cms.contentitemlanguagemetadata_ – metadata for specific language variants of the item. Organized under subfolders matching the parent headless item.
  * _contentitemdata. <content type code name>_ – data stored in the [fields](/documentation/developers-and-admins/customization/field-editor) of the item’s [content type](/documentation/developers-and-admins/development/content-types).


For example, an _“Acme hammer”_ headless item with the _Acme.Product_ content type under the _AcmeStore_ headless channel could be, depending on the type’s [fields](/documentation/developers-and-admins/customization/field-editor), serialized into the following XML files:
App_Data\CIRepository
  * AcmeStore 
    * cms.contentitem 
      * acmehammer-lx3egsq6.xml
    * cms.contentitemcommondata 
      * acmehammer-lx3egsq6@7ae7d097cb 
        * f4f1c1e7-fd79-4253-8031-83e750910300_en@f64dd408c7.xml
    * cms.contentitemlanguagemetadata 
      * acmehammer-lx3egsq6@7ae7d097cb 
        * a0ab3aa4-170b-4091-96ba-2d54cf63e8fc_en@6b0e527ea5.xml
    * cms.headlessitem 
      * acmehammer-lx3egsq6.xml
    * contentitemdata.acme.product 
      * acmehammer-lx3egsq6_f4f1..31-83e750910300_en@fa109c2e74 
        * 0fc5a1ce-e263-4d37-9f61-6253b2f983ea.xml


### Forms
The system uses multiple object types to store the definitions and settings of [Forms](/documentation/business-users/digital-marketing/forms). As a result, the files containing the serialized data of forms use a more complex structure than standard objects.
Each form is serialized into the following **global** items:
  * **cms.formclass**
  * **cms.form**


**Note** :
  * CI/CD does NOT serialize form data submissions collected from visitors.
  * Global **cms.formfeaturedfield** items hold the system’s definitions of featured form fields (options displayed in the **Featured** category when [adding fields to forms](/documentation/business-users/digital-marketing/forms/create-and-edit-forms)).


For example, a form with the  _“UserFeedback”_ code name would be serialized into the following XML files:
CMS\App_Data\CIRepository
  * @global
    * cms.form 
      * userfeedback.xml
    * cms.formclass 
      * bizform.userfeedback.xml
    * cms.formfeaturedfield 
      * kentico.featured.businessphone.xml
      * kentico.featured.companyname.xml
      * kentico.featured.consentagreement.xml
      * kentico.featured.email.xml
      * …


![]()
[]()[]()
