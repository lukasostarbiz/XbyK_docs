---
source: https://docs.kentico.com/modules/multilingual/explore-languages-in-xperience
scrape_date: 2026-01-26
---

Module: Multilingual content
2 of 6 Pages
# Explore languages in Xperience
Offering your website content in multiple languages is an important step in reaching a broader audience. To provide a better and more inclusive user experience for your visitors, Xperience by Kentico supports localization and language variants.
Preparing different language versions of your content is a task for the editors. However, development effort is required to properly set up different language support in your website.
**Multilingual content series**
**This is the first part of the[Set up multilingual](/guides/development/multilingual) mini-series.** Using a simple concrete example, the mini-series demonstrates best practices and necessary steps you need to perform in order to present your website content in multiple languages in a user-friendly way.
In this section, you will learn:
  * the basic terminology related to multilingual support in Xperience by Kentico.
  * how to display your home page and cookie consents in two language versions: English and Spanish.


[![screen recording gif that demonstrates language switching by URL](/docsassets/guides/display-your-website-content-in-multiple-languages/Switch-language-manual.gif)](/docsassets/guides/display-your-website-content-in-multiple-languages/Switch-language-manual.gif)
This section focuses on the **developer tasks** of the process, but it also references actions editors will typically take. Feel free to follow those steps as well to see and enjoy the fruits of your effort at the end. 🙂
## Prerequisites
To follow along, you should have the following:
  * a running instance of **Xperience by Kentico version 30.11.1 or higher** (for guidance, follow [Install a specific version of Xperience by Kentico](/guides/development/get-started/install-a-specific-version-of-xperience-by-kentico))
  * **a website channel** set up with at least one **page displaying a content item** stored in the Content hub (for guidance, follow [our Kickstart series](/guides/development/developer-kickstart/xperience-by-kentico-overview))


The main branch of [this repository](https://github.com/Kentico/xperience-by-kentico-training-guides) is a good starting point that has all of the above set up. You will find the instructions to run in the README file.
Additionally, we assume you have followed [Create a cookie preferences widget guide](/guides/development/data-protection/create-a-cookie-preferences-widget) and [Build a tracking consent banner guide](/guides/development/data-protection/build-a-tracking-consent-banner) (or implemented your own cookies widget and tracking consent banner in a similar way.) The widget and tracking consent banner you will be working with should look like this:
[![screenshot of cookie consent page](/docsassets/guides/display-your-website-content-in-multiple-languages/cookies-consents.png)](/docsassets/guides/display-your-website-content-in-multiple-languages/cookies-consents.png)
If you prefer to see the finished implementation of multilingual right away, take a look at the [finished branch of our repository](https://github.com/Kentico/xperience-by-kentico-training-guides/tree/finished). It contains both the cookie widget and tracking consent banner.
## Understand languages in Xperience by Kentico
Before diving in, let’s get familiar with the basic concepts of working with [languages in Xperience by Kentico](/documentation/developers-and-admins/configuration/languages).
Languages are defined in the administration interface using the **Languages** application.
When more than one language is defined, [editors can create **multiple language variants**](/documentation/business-users/content-hub/content-items#translate-content-items) of [website channel pages](/documentation/business-users/website-content), [content items](/documentation/business-users/content-hub), and [consent texts](/documentation/developers-and-admins/data-protection/consent-management#create-consents) (one variant per language).
One language must always be set as a [**default language**](/documentation/developers-and-admins/configuration/languages#set-up-a-new-language).
[![Default language in Xperience by Kentico](/docsassets/guides/display-your-website-content-in-multiple-languages/default-language.png)](/docsassets/guides/display-your-website-content-in-multiple-languages/default-language.png)
For example, if the default language is English, any content items editors create are, by default, English language variants. Editors can create a different language variant by [manually picking a different language](/documentation/business-users/content-hub/content-items).
[A fresh install of Xperience by Kentico boilerplate project](/guides/development/get-started/install-a-specific-version-of-xperience-by-kentico), comes with English pre-set as a default language. 
Each language can be assigned a [**fallback language**](/documentation/developers-and-admins/configuration/languages#language-fallbacks). If you set up a fallback language and try to retrieve content in a language it is not translated into, the content will come back in the fallback language instead.
Every [website channel](/documentation/developers-and-admins/configuration/website-channel-management) has a **primary language** (typically assigned at the time of channel creation). This is a “default” language for the channel. When a visitor navigates to your website without specifying the language code in the URL (e.g., _baseURL/page_), they will see the page content in the primary language. Navigating to _baseURL/ <language_code_name>/page_ will return the page content in a specific language. We talk more about [multilingual URLs](/documentation/developers-and-admins/development/routing/content-tree-based-routing#multilingual-urls) in [the next part of this mini-series](/modules/multilingual/handle-multilingual-urls).
**Multilingual in multichannel setup**
Languages are global and exist above website channels. **All website channels are automatically available in all defined languages**. At this time, there is no way (out of the box) to configure a channel to be available only in a subset of languages.
This means that you can get in a situation where one is able to retrieve your website pages in a language they are not (and are not meant to be) translated into.
_For example, you have three languages defined in your solution: English, Spanish (with a fallback to English), and French (with a fallback to English). Your instance of Xperience contains two website channels, each translated into two language versions:_
  1. _Channel A in English and Spanish_
  2. _Channel B in English and French_


_Even though by design, pages of Channel A are not supposed to be available in French, when you request https://channelA/fr, the system will still return[200 OK](https://www.rfc-editor.org/rfc/rfc9110.html#name-200-ok) because French exists globally (content will come back in the fallback language, English)._
This is a behavior to be mindful of when you work with multichannel and multilingual solutions. The best way of handling it will depend on your implementation, need and preference and is out of the scope of this guide. 
**Editor’s task (optional): Add Spanish language with a fallback to English**
Sign in to the administration interface to experience the theory in practice. If you have been working off the main branch of our Training guides repository, the only language in your Xperience instance is English at the moment.
  1. [Follow the documentation](/documentation/developers-and-admins/configuration/languages#set-up-a-new-language) to create a new language. Call it **Español** , with language code **es** , do NOT and set the fallback language yet.
  2. Try to navigate to different pages of your website, adding the code _/es_ to the base URL (e.g., _http://localhost:53415/es_ or _http://localhost:53415/es/cookie-policy_). The system will return a [404 Not Found](https://www.rfc-editor.org/rfc/rfc9110.html#name-404-not-found) error each time because there is no Spanish version of your content to display.
  3. Now, go back to **Languages** in the administration interface and set the fallback language to **English.** Notice that you can now see your pages, and the content comes back in the primary language - English.


[ Previous page ](/modules/multilingual)
2 of 6
[ Mark complete and continue ](/modules/multilingual/implement-multilingual-support)
![]()
[]()[]()
