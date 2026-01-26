---
source: https://docs.kentico.com/documentation/developers-and-admins/configuration/aira-configuration
scrape_date: 2026-01-26
---

  * [Home](/documentation)
  * [Developers and admins](/documentation/developers-and-admins)
  * [Configuration](/documentation/developers-and-admins/configuration)
  * AIRA configuration 


# AIRA configuration
**Advanced license required**   
  
Features described on this page require the Xperience by Kentico **Advanced** license tier. 
**Preview feature**
AIRA is currently in preview mode. Expect AIRA features to be updated and modified in upcoming versions, including breaking changes.
Feel free to try out the AIRA features, for example using the sample _Dancing Goat_ [project template](/documentation/developers-and-admins/installation) or other demo sites. You can share your feedback directly with the Kentico [Product team](https://roadmap.kentico.com/).
AIRA is an AI-powered agent that can increase the productivity of content editors, marketers and copywriters by offering [in-product guidance](/documentation/business-users/aira#aira-in-product-guidance) and automating content-related tasks with the use of various AI-backed [features](/documentation/business-users/aira).
## Privacy and data protection
All data processed by AIRA is commercially protected.
Neither Kentico nor the underlying AI models store or log raw customer data inputs, prompts, or responses generated when using AIRA features. All data is discarded after each interaction with AIRA and is not used to train the underlying models. To learn more, see the privacy guarantees provided by Microsoft Azure for [Azure OpenAI model privacy](https://learn.microsoft.com/en-us/legal/cognitive-services/openai/data-privacy?tabs=azure-portal) and [Azure Image analysis privacy](https://learn.microsoft.com/en-us/legal/cognitive-services/computer-vision/imageanalysis-data-privacy-security).
## Manage AIRA features
After getting access to AIRA, all [AIRA features](/documentation/business-users/aira) are active by default. However, you can turn specific AIRA features off, should your project require it.
  1. Open the **AIRA** application.
  2. Switch to the **Feature settings** tab.
  3. Deselect the AIRA features you don’t want users to use in the project.
  4. **Save** the settings.


[![Feature settings of the AIRA application](/docsassets/documentation/aira-configuration/aira-app-feature-settings.png)](/docsassets/documentation/aira-configuration/aira-app-feature-settings.png)
## Disable AIRA globally
To globally disable all [AIRA features](/documentation/business-users/aira), including [in-product guidance](/documentation/business-users/aira#aira-in-product-guidance) in your project:
  1. Open your Xperience project.
  2. In the _appsettings.json_ file, add a new configuration key: `"CMSEnableAira": false`.
  3. Save your project.


All AIRA features are now disabled in your project.
To enable AIRA globally again, change the value of `"CMSEnableAira"` to `"CMSEnableAira": true`.
## Configure AIRA content generation
You can adjust the behavior of specific AIRA features by configuring the following settings:
  1. Open the **AIRA** application.
  2. Switch to the **Content generation** tab.
  3. Select the **AIRA** category and configure the settings: 
     * **Excluded content types** – allows you to simplify the selection of content sources when [generating content for email fields](/documentation/business-users/aira#generate-email-content). Items of the specified content types (for reusable content) will not be offered. For example, we recommend excluding content types that only store image content without text.
     * **Tone of voice guidelines** – allows you to describe your organization’s tone of voice guidelines (professional, simple, humorous, etc.). AIRA will use this tone when generating content for email fields or when [translating content](/documentation/business-users/aira#aira-translations). If not specified, AIRA uses a neutral tone.
  4. **Save** the settings.


The settings apply globally for all content types and fields that support the AIRA features across the system.
## Configure AIRA translations
You can adjust the behavior of [AIRA translations](/documentation/business-users/aira#aira-translations), which is used for the [translation of content items](/documentation/business-users/content-hub/content-items#translate-content-items) and [translation of pages](/documentation/business-users/website-content/translate-pages). To configure AIRA translations:
  1. Open the **AIRA** application.
  2. Switch to the **Translations** tab.
  3. Select the data types allowed for translations. 
     * Allows you to choose the field [data types](/documentation/developers-and-admins/customization/field-editor/data-type-management) you want translated (e.g., _Long text_ , _Text_ , _Rich text (HTML)_ , etc.).
  4. Specify instructions for translations. 
     * Allows you to add custom instructions for how your content should be translated by AIRA, for example, brand terminology, formatting rules, words to leave untranslated, etc.  
[![Configure AIRA translations](/docsassets/documentation/aira-configuration/configure-aira-translations.png)](/docsassets/documentation/aira-configuration/configure-aira-translations.png)
  5. Set whether to use tone-of-voice guidelines for translations.
  6. **Save** your changes.


The AIRA translations are now configured.
### Configure translation of specific fields
All content type fields of the data types allowed for translations are automatically enabled for translation. To specify which content type fields you want to translate:
  1. Open the **Content types** application.
  2. Select a content type.
  3. On the **Fields** tab, select which [fields](/documentation/developers-and-admins/customization/field-editor) you want to configure the translation for.
  4. Expand a field and scroll down to the **AIRA features** section.
  5. Select or clear the **Include field in translation** checkbox. 
     * The checkbox is only available if the [data type](/documentation/developers-and-admins/customization/field-editor/data-type-management) of the field is [allowed to be translated](#configure-aira-translations).
  6. **Save** your changes.


You have successfully configured translation for a specific field.
### Configure translation of Page Builder component properties
You can specify which properties of [Page Builder components](/documentation/developers-and-admins/development/builders/page-builder#page-builder-components) (widget, section, etc.) you want to exclude from translation using the `ExcludeFromAiraTranslation` attribute.
C#
**Example**
Copy
```
using Kentico.Xperience.Admin.Base;
using Kentico.PageBuilder.Web.Mvc;

public class TextWidgetProperties : IWidgetProperties
{
    [ExcludeFromAiraTranslation]
    public string Text { get; set; } = string.Empty;
}
```

## Add AIRA features to your content types
Some [AIRA features](/documentation/business-users/aira) need to be manually enabled for individual fields of your [content types](/documentation/developers-and-admins/development/content-types):
  1. Open the **Content types** application.
  2. Select a content type. 
AIRA features are currently not supported in [reusable field schemas](/documentation/developers-and-admins/development/content-types/reusable-field-schemas). 
  3. On the **Fields** tab, select which [fields](/documentation/developers-and-admins/customization/field-editor) will have AIRA features. You can enable AIRA features for fields: 
     * With the **Text** , **Long text** , **Rich text (HTML)** , or **Taxonomy** data type.
     * Using the **Rich text editor** , **Text area** , **Text input** , or **Tag selector** form component.
  4. Expand a field and scroll down to the **AIRA features** section.
  5. Select **Add AIRA feature** and choose the feature you want to enable: 
     * **Generate image description during mass upload** – generates an SEO-friendly description of an image based on its content during [mass upload](/documentation/business-users/content-hub/content-item-assets#mass-asset-upload).
     * **Automatically assign tags to images during mass upload** – automatically assigns tags from the defined [taxonomy group](/documentation/developers-and-admins/configuration/taxonomies) based on the image content during [mass upload](/documentation/business-users/content-hub/content-item-assets#mass-asset-upload).
     * **Generate email content** – generates email content based on reusable content items stored in the [Content hub](/documentation/business-users/content-hub), external content specified by web URLs, or a custom description. You can globally [configure](#configure-aira-content-generation) which content types are available as content sources.
For fields using the _Rich text editor_ form component, the _Generate email content_ feature also automatically enables [rich text editor refinements](/documentation/business-users/aira#rich-text-editor-refinements). 
     * **Generate email subject based on email content** – generates email subject suggestions based on the content of other fields. Use the feature for the _EmailSubject_ system field of email content types.
       1. Select **Continue**.
       2. Choose which fields of the content type will serve as the content source. Supported field data types are _Text_ , _Long text_ , _Rich text (HTML)_ or _Content items_. The same data types are supported when loading content from the fields of linked content items. The maximum length of the total source content is 10 000 characters (if exceeded, a warning is displayed to the user and content over the limit is truncated).
       3. Select **Save**.
     * **Rich text editor refinements** – supported for text fields using the _Rich text editor_ form component. Allows editors to select text and refine it in place using predefined options (make text shorter, improve writing and grammar, etc.), or apply a custom prompt and send it to AIRA for refinement.
  6. **Save** the field editor.


The selected AIRA feature is now enabled for the given field.
[![AIRA feature added to a content type field](/docsassets/documentation/aira-configuration/Field_editor_AIRA_features.png)](/docsassets/documentation/aira-configuration/Field_editor_AIRA_features.png)
Repeat the process for all fields and content types where you wish to enable the AIRA features.
**Use AIRA features outside of content types**
AIRA features are not restricted to content types in the _Content types_ application. You can use the same process to enable AIRA features in other field editors, for example for the fields of a [custom object type](/documentation/developers-and-admins/customization/object-types) in the _Modules_ application.
However, keep in mind that the _Generate email content_ and _Generate email subject based on email content_ features optimize the AIRA prompts to generate email content. Results may be inaccurate if you attempt to generate other types of content.
## Rich text refinements in Page Builder and Email Builder
In Page Builder and Email Builder, the rich text editor can appear directly on the respective builder tabs as an [inline editor](/documentation/developers-and-admins/development/builders/page-builder/widgets-for-page-builder/rich-text-inline-editors) or in the configuration dialog for component properties.
[Rich text editor refinements](/documentation/business-users/aira#rich-text-editor-refinements) are automatically enabled for the rich text inline editor. Refinements in the property configuration dialog need to be [enabled](#enable-rich-text-refinements-in-property-configuration-dialog) by developers.
### Enable rich text refinements in property configuration dialog
To enable rich text refinements in the configuration dialog, apply the `FormComponentConfiguration` attribute with `AiraTextRefinementConfigurator` as the configurator type to component properties that use the rich text editor as their editing component.
The example below shows how to enable text refinements for a Page Builder widget property:
C#
**MyWidgetProperties.cs**
Copy
```
using Kentico.Xperience.Admin.Base;
using Kentico.Xperience.Admin.Base.FormAnnotations;
using Kentico.PageBuilder.Web.Mvc;

public class MyWidgetProperties : IWidgetProperties
{

    [RichTextEditorComponent]
    // Enables AIRA rich text refinements for the rich text editor editing component
    [FormComponentConfiguration(typeof(AiraTextRefinementConfigurator))]
    public string RichText { get; set; }
}
```

![]()
[]()[]()
