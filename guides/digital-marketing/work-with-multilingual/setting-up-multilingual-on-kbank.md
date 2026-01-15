# Setting up multilingual on Kbank
  * Concept| [ Copy page link ](guides/digital-marketing/work-with-multilingual/setting-up-multilingual-on-kbank#) | [Get HelpService ID](guides/digital-marketing/work-with-multilingual/setting-up-multilingual-on-kbank#) | This page is part of a module: [ Manage content ](modules/manage-content)
Core MVC 5


[✖](guides/digital-marketing/work-with-multilingual/setting-up-multilingual-on-kbank# "Close page link panel") [Copy to clipboard](guides/digital-marketing/work-with-multilingual/setting-up-multilingual-on-kbank#)
The previous article introduced the [multilingual variants of content](guides/digital-marketing/work-with-multilingual/multilingual-variant-of-content) and showed you, how to distinguish between translated and not-translated content in Xperience by Kentico. Now, we’ll dive deeper into the theory of languages and how to set them up in Xperience by Kentico.
Editors can offer website visitors content in different languages. For example, a US-based company can have content in English and French for a Canadian audience. Different channels can have different language variants to fulfill any specific needs of the company.
These languages are defined by system administrators, and developers made sure every channel displayed them properly. From the system perspective, however, there are three types of languages in Xperience, that are used for different purposes.
We’ll talk about different types of languages in Xperience. Let’s quickly describe them:
  * **Fallback language** – we already covered this in the [previous article](guides/digital-marketing/work-with-multilingual/multilingual-variant-of-content). It’s a ‘backup’ language for the content you use in channels. In case the content won’t show in the new language variant (for example, because it wasn’t published), the fallback takes its place.
  * **Default language** – when you open the [Content hub](documentation/business-users/content-hub) for the first time, Xperience shows the content items in the default language first. After you switch to a different language in channels, Xperience remembers the new language and rearranges content items in the _Content hub_ , so you first see the items in the new language you worked with. For example, your default language is English, so the content items in the _Content hub_ you see first are in English. Then, you go to a website channel, switch to German, and work on the German language variant. When you go to the _Content hub_ next time, Xperience remembers and shows content items in German first. The default language doesn’t influence the live website.
  * **Primary language** - is the language in which the live website appears by default. The URL doesn’t include the language identifier of the primary language. For example, the primary language of a website is English, and a language variant is French. The URLs will look, fir example, like this: 
    * kbank.com/personal for English
    * kbank.com/fr/personal for French
You can set different primary languages for each website channel in the **Channels management** application.


## Add a new language
When creating a new language variant of content, you must add the desired language to the **Languages** application. To do so, you need to be a system administrator or have appropriate permission. The next step is to set the language for the backend. Lastly, the developers need to ensure the content in the desired language displays correctly.
When you **add a new language** , you’ll define the _language_ name used internally in Xperience and the _code name_ used in the URLs (more about URLs below). _Formatting culture_ drives how XbyK handles culture-related information, like time, dates, or number format.
The formatting culture values must be based on IETF or one of the ISO-639 standards.
[![Example of newly created language in the Languages application](docsassets/guides/setting-up-multilingual-on-kbank/language-set-up.png)](https://docs.kentico.com/docsassets/guides/setting-up-multilingual-on-kbank/language-set-up.png)
You can also **adjust an existing language** in the application; just open it and change the requested value.
However, changing the language’s code name or setting another language as the default will change the URL. This means your developers need to redirect all original URLs to the new ones. See more in the Languages and URL section.
To **remove an existing language** , use the bin button next to the language.
[![Remove the language using the bin button](docsassets/guides/setting-up-multilingual-on-kbank/remove-language.png)](https://docs.kentico.com/docsassets/guides/setting-up-multilingual-on-kbank/remove-language.png)
If the system already contains a translated version of some content, you cannot delete the language. For example, the screenshot above shows there already is a Czech and German variant of a default English content item - you can see the Delete button is disabled. Don’t forget to go through:
  * Content in the **Content Hub** ,
  * Pages in all **website channels** ,
  * **Email channels**.


Before you delete the language, check if it’s marked as the _default language_ or used as the _primary language_ for a channel. If so, make another language default and the primary one.
[![Xperience shows you if the language you want to delete is a default language](docsassets/guides/setting-up-multilingual-on-kbank/remove-default-language.png)](https://docs.kentico.com/docsassets/guides/setting-up-multilingual-on-kbank/remove-default-language.png)
## Languages are global
When you create a new channel, you define a _primary language_ your content will focus on. This will be the main language of your channel, but you can create content in any other language listed in the **Languages** application.
It’s important to remember, however, that Xperience, by default, does not limit the channel languages in which you can publish content. While creating a language, you cannot restrict the languages allowed for this channel.
Languages in Xperience are, by default, global. Meaning, when a language exists, editors can use it to translate content. Similarly, if a language has a fallback language, you cannot opt out of this behavior in a specific channel.
If your project needs to be more restrictive, developers can customize Xperience and introduce language per channel setting capability. For a practical example, look at the Kbank demo settings.
[![See language Seting keys in Kbank](docsassets/guides/setting-up-multilingual-on-kbank/kbank_custom_language_settings.png)](https://docs.kentico.com/docsassets/guides/setting-up-multilingual-on-kbank/kbank_custom_language_settings.png)
[![List of languages in Kbank](docsassets/guides/setting-up-multilingual-on-kbank/kbank_languages.png)](https://docs.kentico.com/docsassets/guides/setting-up-multilingual-on-kbank/kbank_languages.png)
The global nature of languages in Xperience can impact other content objects besides content. When you delete a language, Xperience removes translated variants of consent agreements, taxonomy names, or title tags. So, when you decide to remove a language, proceed with caution.
## Fallback languages
As mentioned in the [previous guide](guides/digital-marketing/work-with-multilingual/multilingual-variant-of-content#what-is-a-fallback-language), fallback language serves as a backup for content not published in the targeted multilingual variant.
### How fallback language works
Let’s say you have a website in Spanish, and its fallback language is German. You also have English as a fallback for German. A visitor goes to the Spanish version of the website, and there is content that was not published in Spanish. Xperience shows the content in the fallback language, which is German. But if even the German version doesn’t exist, it shows the next in line, English.
If there’s a fallback, the website’s URL will still appear as Spanish, even though some content shows in German or English.
### Defining fallback languages
You can use more fallback languages than one universal and set up different fallbacks for each language. Or create a fallback chain. For example, you can create a fallback chain of five languages where the content will show in its fallback language if one language doesn’t work. And if THAT fallback language doesn’t work, the content shows in that fallback language’s fallback language. See the chain in the box below (however unrealistic). If the first one doesn’t work, Xperience shows the content is the second one. If the second one doesn’t work, it shows the third one, and so on.
Here is an example of the fallback chain:
German → Czech → French → Spanish → English
To change the fallback language, open the **Languages** application and choose the preferred language. In our example, if German doesn’t work, it’ll show content in Czech. The following screenshot shows where to set up the fallback language.
[![Choose a fallback language in the language settings](docsassets/guides/setting-up-multilingual-on-kbank/choosing-fallback-language.png)](https://docs.kentico.com/docsassets/guides/setting-up-multilingual-on-kbank/choosing-fallback-language.png)
Similarly to the previous step, set up the fallback language for Czech. Open the Czech language and set up the fallback language - French. Continue like this till you have the whole chain set up.
### Opt-out from language fallback
Suppose your company doesn’t want a fallback language for a specific language variant. In that case, you can remove the fallback language.
Instead of choosing the fallback, remove it using the cross button.
[![Remove the fallback language](docsassets/guides/setting-up-multilingual-on-kbank/removing-fallback-language.png)](https://docs.kentico.com/docsassets/guides/setting-up-multilingual-on-kbank/removing-fallback-language.png)
But what if you don’t define the fallback language, and the desired page doesn’t exist in the target language? In that case, a visitor ends up on a 404 page. Make sure developers implement the 404 page for all language variants. Otherwise, the visitor will see just a white page.
## Languages and URL
URLs for pages in the [primary language of a website channel](documentation/developers-and-admins/configuration/website-channel-management) don’t include the language’s **code name** or **parameter**. For example, suppose English is set as the primary language of the website channel. In that case, the URLs of pages in English will be _domain.com/page_ , for example _kbank.com/personal_.
For pages in a non-primary language, Xperience inserts the language’s parameter into the URL path after the site’s root (‘/’). For example, suppose English is set as the primary language of the website channel. In that case, the URLs of pages in Spanish will be in the _kbank.com/es/personal_ format.
As we mentioned before, the languages in Xperience are global and Kentico doesn’t monitor changes in Global objects. Which means developers have to handle redirects of all pages indexed in the search engines from the old, now broken, URLs to the new ones.
## To sum up
In this guide, you’ve learned how to add, adjust, or remove a fallback language. Without a fallback language, visitors are redirected to a 404 page. You can create a chain of fallbacks or set a different fallback for each language variant. There doesn’t have to be just one universal fallback for everything; each language can have its fallback. Xperience creates a code name for the language in the URL. This code name doesn’t update automatically, so if you change it in settings, redirect the old pages.
## Next steps
In the following article, you can learn how to create a [language variant of a page](guides/digital-marketing/work-with-multilingual/multilingual-variant-of-page).
If you want to read more about multilingual from a developer’s point of view, visit our [Guides](guides/development/multilingual) dedicated to this topic.