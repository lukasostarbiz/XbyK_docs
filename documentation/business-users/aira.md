---
source: https://docs.kentico.com/documentation/business-users/aira
scrape_date: 2026-01-22
---

  * [Home](/documentation)
  * [Business users](/documentation/business-users)
  * AIRA 


# AIRA
**Advanced license required**   
  
Features described on this page require the Xperience by Kentico **Advanced** license tier. 
**Preview feature**
AIRA is currently in preview mode. Expect AIRA features to be updated and modified in upcoming versions, including breaking changes.
Feel free to try out the AIRA features, for example using the sample _Dancing Goat_ [project template](/documentation/developers-and-admins/installation) or other demo sites. You can share your feedback directly with the Kentico [Product team](https://roadmap.kentico.com/).
AIRA is an AI-powered agent integrated into Xperience by Kentico. It helps users automate and streamline marketing and content management tasks through AI-driven features, and provides [in-product guidance](#aira-in-product-guidance) on best practices.
## Privacy and data protection
All data processed by AIRA is commercially protected.
Neither Kentico nor the underlying AI models store or log raw customer data inputs, prompts, or responses generated when using AIRA features. All data is discarded after each interaction with AIRA and is not used to train the underlying models. To learn more, see the privacy guarantees provided by Microsoft Azure for [Azure OpenAI model privacy](https://learn.microsoft.com/en-us/legal/cognitive-services/openai/data-privacy) and [Azure Image analysis privacy](https://learn.microsoft.com/en-us/legal/cognitive-services/computer-vision/imageanalysis-data-privacy-security).
## AIRA in-product guidance
AIRA provides in-product guidance through a built-in chatbot available directly in the Xperience by Kentico administration interface. It assists users by answering questions about features and workflows, offering best practice suggestions, and using Xperience by Kentico’s product documentation and guides as its primary source of information. This allows users to get quick assistance without leaving their current workspace.
### Customer journey insights
Using its in-product guidance, AIRA offers actionable insights into [customer journeys](/documentation/business-users/digital-marketing/customer-journeys). It can show you which [contact groups](/documentation/business-users/digital-marketing/contact-groups) are present in a journey, how they progress through each stage and which are the best and worst performing audience segments. These insights help marketers and content editors better understand audience behavior and optimize engagement strategies.
**Overlaps between contact groups**  
In some journeys, the same contacts may belong to multiple [contact groups](/documentation/business-users/digital-marketing/contact-groups). This overlap can influence results when comparing segment performance. Keep this in mind when analyzing journeys, as contacts may be counted in more than one group.
**Expectations from AIRA**  
AIRA provides guidance by analyzing your journeys and contact group progression. Its recommendations are intended as insights to support your decision-making, not as prescriptive optimization steps.
## Image processing
### Automatically select image focal point
When using [image variants](/documentation/business-users/content-hub/content-item-assets#image-variants), AIRA automatically selects the most suitable focal point for an image during [file upload](/documentation/business-users/content-hub/content-item-assets#upload-a-single-file). This ensures that your image variants consistently capture the area of interest, eliminating the need for manual cropping or focal point selection for every image uploaded into the system.
For AIRA to successfully select the focal point in an image, the image needs to have:
  * a file size not larger than 20 MB
  * dimensions of at least 50x50 pixels
  * a [file type](/documentation/developers-and-admins/configuration/content-hub-configuration#customize-supported-file-types) supported for [image variants](/documentation/business-users/content-hub/content-item-assets#image-variants)


If the automatic selection fails, the focal point is automatically placed at the center of the image.
### Automatically generate image description
When [mass uploading](/documentation/business-users/content-hub/content-item-assets#mass-asset-upload) images, AIRA automatically generates an SEO-friendly description of an uploaded image and stores it in the [configured](/documentation/developers-and-admins/configuration/aira-configuration#add-aira-features-to-your-content-types) text field. The description is generated in the currently selected language.
### Automatically assign tags to images
When [mass uploading](/documentation/business-users/content-hub/content-item-assets#mass-asset-upload) images, AIRA automatically [assigns tags](/documentation/business-users/content-hub/content-items#add-tags-to-content-items) to the [configured](/documentation/developers-and-admins/configuration/aira-configuration#add-aira-features-to-your-content-types) taxonomy field based on the image content.
## AIRA translations
AIRA translations allow editors to translate existing content to new [language variants](/documentation/developers-and-admins/configuration/languages) directly without having to translate it themselves or using external tools for translation. You can translate content of pages (including their [Page Builder](/documentation/developers-and-admins/development/builders/page-builder) content), and content items.
Text links to your content are not translated automatically and need to be manually reviewed to ensure they lead to the correct language variant.
When translating content using AIRA, a draft of the content is created, which you can then review and publish, or you can delete it if you’re not satisfied with the translation. The translation process can be [configured](/documentation/developers-and-admins/configuration/aira-configuration#configure-aira-translations), which allows you to specify custom brand terminology, which words to leave untranslated, formatting rules, or other custom instructions.
When translating multiple items at the same time, e.g., by using [mass translation](/documentation/business-users/content-hub/content-items#mass-translating) or when linked items are translated with the original item, the set of items to be translated is sent to the “translation queue”, which you can check in the _Translation queue_ application. The application displays any sets of items currently being processed and items queued for translation (note that it does not display singular items which were sent for translation, only ones in bulk). Once you start the translation of your items you can safely continue working while the translation finishes in the background.
## Rich text editor refinements
When editing [Rich text fields](/documentation/business-users/rich-text-editor) that have the _Rich text editor refinements_ or _Generate email content_ [AIRA feature enabled](/documentation/developers-and-admins/configuration/aira-configuration#add-aira-features-to-your-content-types), you can send selected pieces of text to AIRA for refinement.
  1. Select a block of text in the editor.
  2. Select the **Refine text with AIRA** button, which appears next to the highlighted text.
  3. Choose one of the refinement options (make text shorter, improve spelling and grammar, etc.).  
[![Refining a paragraph in the rich text editor](/docsassets/documentation/aira/AIRA_RTE_Refinements.png)](/docsassets/documentation/aira/AIRA_RTE_Refinements.png)


AIRA displays a modified version of the selected text based on the chosen refinement option. You can apply further refinements by selecting the buttons below the list. The refinements always apply only to the latest output generated by AIRA. You can also describe adjustments yourself by typing into the prompt at the bottom of the AIRA panel.
Once you are happy with the result, select and copy the text back into the editor. Remember to **Save** the content.
## Generate email content
When creating [emails](/documentation/business-users/digital-marketing/emails) in Xperience, marketers can use AIRA features to generate content suggestions for email subjects and content fields.
Once AIRA features are [configured](/documentation/developers-and-admins/configuration/aira-configuration#add-aira-features-to-your-content-types) for your email content types, you can start generating content.
  1. Open an email channel application.
  2. Create an email or select an existing one, and open the **Content** tab.
  3. Select the **AIRA** (  
[![Generating email content](/docsassets/documentation/aira/Emails_AIRA_features.png)](/docsassets/documentation/aira/Emails_AIRA_features.png)
  4. Continue according to the type of the email field: 
     * [Email subject fields](#email-subject-fields)
     * [Email content fields](#email-content-fields)


**Generated content language**
AIRA generates content in the [language](/documentation/developers-and-admins/configuration/languages) that is currently selected in the given application, for example the _Content language_ assigned to an [email channel](/documentation/developers-and-admins/digital-marketing-setup/email-channel-management). If content items are not available in the given language when loading source content from the _Content hub_ , the system uses fallbacks based on the configured language settings.
When using the _Generate email content_ AIRA feature without a language context (for example in the UI of a custom module), content is generated in the system’s [Default language](/documentation/developers-and-admins/configuration/languages).
### Email subject fields
AIRA generates a list of email subject suggestions based on the content of the fields selected when [adding the AI feature](/documentation/developers-and-admins/configuration/aira-configuration#add-aira-features-to-your-content-types).
[![Generating email subject suggestions](/docsassets/documentation/aira/Emails_AIRA_subject.png)](/docsassets/documentation/aira/Emails_AIRA_subject.png)
If the initial suggestions are not helpful, you can generate further suggestions by selecting the “quick refinement” buttons below the list. The refinements always apply only to the latest output generated by the AI. You can also describe adjustments yourself by typing into the prompt at the bottom of the AI panel.
Once you find a suitable suggestion for your email subject, you can select the given line, which automatically inserts the text into the email’s **Subject** field. You can also copy and adjust the text manually. Remember to **Save** the email.
### Email content fields
In the **Generate with AIRA** panel, you first need to select content sources:
  * **Content hub** – content is generated from the text fields of the selected content items (stored in the [Content hub](/documentation/business-users/content-hub)), as well as any linked content items. The maximum length of the total source content is 10 000 characters (if exceeded, a warning is displayed and content over the limit is truncated). The system loads the latest edited content (_Draft_ or custom workflow steps) of the specified content items, even if they are not published yet.
  * **Web URLs** – content is generated from external sources found under the specified URLs. Add each URL on a new line.
  * **Custom description** – write your own description of the content you want AIRA to generate. To get the best results, specify the maximum length of the generated text, describe the target audience, and what the reader should do after reading the content (call to action).


[![Generate content for emails](/docsassets/documentation/aira/Emails_AIRA_content.png)](/docsassets/documentation/aira/Emails_AIRA_content.png)
For optimal results when using the **Content hub** and **Web URLs** content sources, set the **Target length (words)** according to your requirements, select **Provide additional parameters** , and fill in all available options.
Once your content sources and parameters are specified, select **Generate** , and wait for AIRA to produce the results.
You can further refine the generated text using the buttons below the text area. The refinements always apply only to the latest output generated by AIRA. You can also describe adjustments yourself by typing into the prompt at the bottom of the AIRA panel.
Once you are happy with the generated text, select **Copy** (**Save** the email.
For _Rich text editor_ fields, you can also adjust the resulting text at any time using [rich text editor refinements](#rich-text-editor-refinements).
![]()
[]()[]()
