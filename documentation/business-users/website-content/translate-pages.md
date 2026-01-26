---
source: https://docs.kentico.com/documentation/business-users/website-content/translate-pages
scrape_date: 2026-01-26
---

  * [Home](/documentation)
  * [Business users](/documentation/business-users)
  * [Website content](/documentation/business-users/website-content)
  * Translate pages 


# Translate pages
Before you can translate individual pages in your project into other languages, you need to set those up in the **Languages** application. For detailed info on how to configure multiple languages in Xperience, see [Languages](/documentation/developers-and-admins/configuration/languages).
Afterwards, you can start to translate and create language variants of your pages: 
  1. Open the website channel where you want to translate pages.
  2. In the header bar, open the language switcher.  
[![Language switcher in header bar](/docsassets/documentation/translate-pages/header_bar_language_switcher.png)](/docsassets/documentation/translate-pages/header_bar_language_switcher.png)
The language switcher is available only if two or more languages are set up.
  3. Select the language to translate pages into.
  4. Select the page to translate.
Untranslated pages are visually marked by the _Not translated_ (
  5. Enter the translated page name. You can now choose one of the following options:
     * **Copy content from another language** is the preselected option. By default, the closest language in the [fallback chain](/documentation/developers-and-admins/configuration/languages) is preselected. You can choose a different language from the drop-down list. This option lets you prefill the content fields with the content available in the chosen language. If a linked item does not have a translated or published version, a warning will be displayed, and a fallback language variant of the item will be used.
     * **Create empty page** allows you to create an empty page with blank fields. Linked items need to be re-linked into the new language variant of the translated page.
     * **Translate using AIRA** see [Translate using AIRA](#translate-using-aira) for more information.  
[![Page translation](/docsassets/documentation/translate-pages/translate_page.png)](/docsassets/documentation/translate-pages/translate_page.png)
  6. Select **Continue**.
  7. Enter the translated values of the page’s fields.
  8. **Save** the translated page.


You have now successfully translated the page. Note that the particular language variant is now only a  _Draft_ and won’t be accessible on the live site until you [publish](/documentation/business-users/website-content/edit-and-publish-pages) it. Until you do so, the language variant in the fallback language will be displayed.
Translated content items on untranslated pages are loaded in the fallback language. However, there is an exception in case the content items are injected into the page via code. In that case, content items are loaded in the language variant according to the requested language. For more information on language fallbacks, see [Languages](/documentation/developers-and-admins/configuration/languages).
### Translate using AIRA
**AIRA license requirements**
[AIRA](/documentation/business-users/aira) features require the Xperience by Kentico **Advanced** license tier.
**Preview feature**
AIRA is currently in preview mode. Expect AIRA features to be updated and modified in upcoming versions, including breaking changes.
Feel free to try out the AIRA features, for example using the sample _Dancing Goat_ [project template](/documentation/developers-and-admins/installation) or other demo sites. You can share your feedback directly with the Kentico [Product team](https://roadmap.kentico.com/).
When you choose **Translate using AIRA** and start the translation process, the name and content of your page, including [Page Builder](/documentation/developers-and-admins/development/builders/page-builder) content, will be translated using [AIRA](/documentation/business-users/aira).
The translation workflow changes depending on the number of items submitted for translation. For a single item, AIRA creates a new language variant of the content in the _Draft_ state and immediately takes you to it.
However, when translating multiple pages at once (for example, when translating pages along with their linked content), your content is sent to a _Translation queue_. This allows the translation to be processed in the background, freeing you up to continue your work. You can check on its progress in the _Translation queue_ application. Once the translation of your content finishes, the related items are removed from the _Translation queue_ application.
The behavior of the translation can be [configured](/documentation/developers-and-admins/configuration/aira-configuration#configure-aira-translations), allowing you to specify, for example, formatting rules or brand terminology.
Existing text links to the translated content are not redirected to the new variant or translated automatically and need to be manually reviewed to ensure they lead to the correct content.
![]()
[]()[]()
