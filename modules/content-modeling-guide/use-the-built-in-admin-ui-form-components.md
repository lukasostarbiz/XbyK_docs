# Use the built-in admin UI form components
  * [ Copy page link ](modules/content-modeling-guide/use-the-built-in-admin-ui-form-components#) | [Get HelpService ID](modules/content-modeling-guide/use-the-built-in-admin-ui-form-components#)
Core MVC 5


[✖](modules/content-modeling-guide/use-the-built-in-admin-ui-form-components# "Close page link panel") [Copy to clipboard](modules/content-modeling-guide/use-the-built-in-admin-ui-form-components#)
Xperience provides different form components for content types that editors will use to input their data. Other form components may also have additional configuration options.
For example, the _Long text_ data type comes with different text-based form components, such as the _Text input_ , _Password_ , _URL_ , or _Text area_. You can use the _Rich text (HTML)_ data type with the _Rich text editor_ form component for rich text.
[![Configuring an editing component for a text field](docsassets/guides/store-content/cm-guide-long-text-config.png)](https://docs.kentico.com/docsassets/guides/store-content/cm-guide-long-text-config.png)
Form components also come with various configuration options that provide different editing experiences. Developers can, for example, configure the _Rich text_ form component as _Structured content_ , _Email content_ , or _Contact notes_. Developers can also customize their configuration. The following image shows Kbank’s _Rich text editor_ configuration options; the last _News page editor configuration_ is custom.
[![Additional field configuration options](docsassets/guides/store-content/cm-guide-content-types-configuration-rich-text-editor.png)](https://docs.kentico.com/docsassets/guides/store-content/cm-guide-content-types-configuration-rich-text-editor.png)
Find out more about available [Admin UI form components](documentation/developers-and-admins/customization/extend-the-administration-interface/ui-form-components/reference-admin-ui-form-components) and the [React input type components](documentation/developers-and-admins/customization/extend-the-administration-interface/ui-form-components/reference-react-input-components) in the documentation.
### Simplify structured content with custom data types
The out-of-the-box [data types](documentation/developers-and-admins/customization/field-editor/data-type-management) and [UI form components](documentation/developers-and-admins/customization/extend-the-administration-interface/ui-form-components) that you use to define editing experience represent the most common types that fit most projects. During content modeling sessions, you may realize you might need to standardize some complex content object, like multiple addresses or office hours, into a predictable structure to improve the editing experience and help with data format predictability to match the unique project’s needs.
Instead of forcing every piece of structured content to live as its own content item (or even a granular content item composed of other, smaller content items) in the _Content hub_ , you can use _custom data types_ to model content that should belong to a parent item without cluttering your reusable content library. This is especially useful for structured content critical for presentation and governance, but it doesn’t make sense to manage it as standalone, reusable content items.
_Custom data types_ mean your content can remain structured, semantically meaningful, and repeatable while avoiding the limitations of hard-coded field sets in your content type. Editors can add, remove, and reorder structured entries (like multiple addresses for a business) without leaving empty fields or copy-pasting data they need to rearrange the content. This provides a smoother, frustration-free editing experience and enables developers to control presentation and validation for each piece of structured content.
See an example of an [address custom data type](https://community.kentico.com/blog/embedded-structured-content-and-the-power-of-custom-data-types) or [UTM parameters](https://github.com/Kentico/community-portal/blob/main/src/Kentico.Community.Portal.Admin/Client/src/components/UTMParametersDataTypeFormComponent.tsx) in the [Kentico Community Portal](https://community.kentico.com/).
### Leverage linked content items
Xperience allows editors to compose their content items by combining data stored in several other content items.
Let’s examine the _Product_ content type discussed on the previous page. Highlighted fields store the actual data inputs, while the light violet fields reference other content types that hold the values.
[![Content model diagram](docsassets/guides/store-content/cm-guide-product-content-model-diagram.png)](https://docs.kentico.com/docsassets/guides/store-content/cm-guide-product-content-model-diagram.png)
You can see that the _Product_ type contains fields like _Product features_ , _Product benefits_ , or _Product image_. Sure, you can create two more fields and store the values of product benefits and their icons within the _Product_. At the same time, why would you?
In real life, if we take the Kbank demo site example, more than one product will offer the same benefits, such as _included internet banking_ , _getting a quick decision_ , or _fast access to funds_.
[![Kbank demo site example](docsassets/guides/store-content/kbank-demo-banner-example.png)](https://docs.kentico.com/docsassets/guides/store-content/kbank-demo-banner-example.png)
Storing the information about benefits within the individual _product_ content item restricts access to this data only within the context of this one (and only one) product item itself. If another product allows for _fast access to funds_ , editors need to duplicate the same wording and decorate it with the same icon in another product item. Duplicating the same content often leads to content management nightmares.
The benefits of creating composable content types that store only references:
  * Help with content curation.
  * Prevent editors from duplicating the same content.
  * Improve content management and long-term content curation.
  * Lead to quick content composition.


When defining the content model, discuss which types can be reused and create them accordingly. Editors will use the **Content item** data type with the **Combined content selector** form component to refer to the relevant data. At the same time, developers will retrieve the [linked items](documentation/developers-and-admins/development/content-retrieval) and display them accordingly.
Developers also [limit](documentation/developers-and-admins/development/content-types#add-the-option-to-link-content-items) which content types editors can select when creating their content. The _Combined content selector_ allows developers to specify which content types editors select.
When an editor selects content items they want to use, rather than copying the values from the original item to the new item, Xperience creates a reference between these records.
Reusing content items improves content maintenance. When an editor updates the referenced item, the content changes are automatically promoted to every other item that uses this content, making content management a breeze. From the developer’s perspective, developers query the linked items in their custom code and return [a collection](documentation/developers-and-admins/development/content-retrieval/retrieve-content-items) of [references to content items](documentation/developers-and-admins/api/content-item-api/content-item-database-structure) they can further process. For example, they can retrieve URLs of web pages to which editors want to guide their audience.
One of the most typical examples of reusable content types is a content item [assets](guides/architecture/content-modeling/content-modeling-guide/store-files) that users store in the _Content hub_. See further examples mentioned in this material on the Kbank demo site.
[ Previous page ](modules/content-modeling-guide/build-flexible-model-with-content-blocks)
15 of 28
[ Mark complete and continue ](modules/content-modeling-guide/avoid-creating-channel-specific-content-from-linked-content-fragments)
  * [Community Questions & Answers](https://community.kentico.com/q-and-a)
  * [Contact support](https://community.kentico.com/support)


### Cookie consent
We use necessary [cookies](https://www.kentico.com/cookies-policy) to run our website and improve your experience while browsing. Additional cookies are only used with your consent. You may revoke your consent on the [Cookies Policy](https://www.kentico.com/cookies-policy) page or in your browser at any time. 
ACCEPT ALL  [Configure](https://www.kentico.com/cookies-policy)
USE ONLY NECESSARY 
![](https://docs.kentico.com/modules/content-modeling-guide/use-the-built-in-admin-ui-form-components)
[](https://docs.kentico.com/modules/content-modeling-guide/use-the-built-in-admin-ui-form-components)[](https://docs.kentico.com/modules/content-modeling-guide/use-the-built-in-admin-ui-form-components)