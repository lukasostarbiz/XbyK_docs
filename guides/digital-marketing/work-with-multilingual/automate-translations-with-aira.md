---
source: https://docs.kentico.com/guides/digital-marketing/work-with-multilingual/automate-translations-with-aira
scrape_date: 2026-01-26
---

  * [Home](/guides)
  * [Digital marketing](/guides/digital-marketing)
  * [Work with multilingual](/guides/digital-marketing/work-with-multilingual)
  * Automate your translations with AIRA 


# Automate your translations with AIRA
**Advanced license required**  
Features described on this page require Xperience by Kentico Advanced license tier.
When your company expands to new markets, one of the first things your editors (or you) will have to do is to localize your content into local languages. Besides expanding your audience to users that may not be familiar with your source language, **addressing them in their native languages helps build trust in your brand**.
However, creating engaging messaging and running seasonal promotions while you are trying to maintain consistent brand messaging across several languages can quickly become a chore. More languages, more trouble, as they say.
[AIRA](/documentation/business-users/aira) helps you meet that challenge head-on by bringing content and translation together into one workspace. With a translation capability **built directly into Xperience** , you don’t need to constantly switch tools to manually copy the translations. Instead, let AIRA handle the boring parts and translate your content automatically. This way, you’ll gain more time and headspace to focus on keeping your unique tone of voice and personality intact.
In this guide, you’ll:
  * Learn what AIRA automated translations can and can’t do.
  * Prepare your environment for smooth translation workflows.
  * Tailor AIRA translation settings to your needs.
  * Try how AIRA translations work on a real-life campaign scenario.
  * Pick up best practices for achieving consistent, high-quality translations.


## What AIRA automated translations can and can’t do
Before we dive into details, it helps to know what to expect from AIRA’s translation capabilities. AIRA helps you translate content faster. One may argue that since it’s automatic, every translation will sound the same, but this is not the case.
AIRA is powered by a [large language model (LLM)](https://en.wikipedia.org/wiki/Large_language_model), which uses advanced AI to understand context and meaning. You can then configure AIRA to respect your brand’s tone of voice by adding custom translation rules (called **prompts**) to generate natural-sounding translations for your text fields.
Our internal trials showed that using AIRA’s built-in translation feature can save up to 90% of translation time when compared to external translation tools with manual workflows. That means you spend less time copy-pasting text from one tool to another, and more time creating content and shaping campaigns.
### Currently supported AIRA functionalities
  * Translation of _text_ , _long text_ , and _rich text (HTML)_ fields.  

  * Manually triggered automated translations and bulk (mass) translation from the [Content hub](/guides/digital-marketing/xperience-essentials/manage-content-hub-essentials) or the individual items.
  * Optional tone of voice guidelines that help AIRA stay on brand every time.
  * Custom translation prompts that reflect your terminology, cultural nuances, and preferences.


### Automated translation limitations to keep in mind
  * Linked content items are translated manually in a cascade, and you choose which ones to include.  

  * Automated translation of taxonomies isn’t supported yet.
  * Translation of content in hyperlinks is also not supported, so it is safer to always check links manually.
  * AIRA will only translate the data types that are currently out-of-the-box with Xperience (custom developer-defined data types may require setup, so discuss this with your developers before starting work on your project).
  * The translation process runs asynchronously, in the background, so it may take a few moments before the translated content appears in the content type.


Understanding these boundaries upfront helps you plan realistic, efficient translation workflows that save both time and money.
## Prerequisites for AIRA translations
A little preparation goes a long way toward smooth translation results. Before you start using AIRA, check that your project is ready.
  1. **Languages** – Make sure that all your desired target languages are added in the _Languages_ application.  
This step defines the [language variants](/guides/digital-marketing/work-with-multilingual/multilingual-variant-of-content) that AIRA can create for your website. See the documentation on [Languages](/documentation/developers-and-admins/configuration/languages) for detailed setup info.
  2. **Access rights** – Check that your [user role](/guides/digital-marketing/xperience-essentials/user-management-essentials-introduction) can access both **AIRA configuration** and **Content hub**. If you can’t see those apps, ask your Xperience administrator to grant you access to those spaces.
  3. **Tone of voice (optional)** – To help AIRA reflect your brand’s personality, define your tone of voice in **Configuration** → **AIRA** application → **Content generation** tab.  
For example: _Friendly and clear, with a touch of humor._  

The _Tone of voice_ field is limited to 256 characters, so you may want to take a bit of time to brainstorm a ToV prompt that is both brief and descriptive.


By preparing these basics, you’ll ensure your translations are as accurate as possible, and aligned with your brand identity from day one.
## Setting up automated translations in AIRA
Once your environment is ready, it’s time to **configure AIRA** for your first translations. This setup defines _how_ AIRA interprets and translates your content and ensures the results sound like your brand.
Like most AI tools, AIRA delivers the best results when you refine your prompts over time. Start with an initial version, review the results, and adjust your prompts based on what you learn. Even slight changes to the translation prompt can make a significant difference.
Set aside some time to fine-tune your translation process and prompts. This will help AIRA better match your brand’s tone and produce more natural translations.
### Open the AIRA configuration
Start by going to **Configuration** → **AIRA**. Here you’ll find two key areas: one for _tone of voice_ (how your brand speaks) and one for _translation settings_ (how your text gets converted across languages).
Switch to the **Content generation** tab to review or define your tone of voice. It’s located here and not in the translation settings because AIRA will use the definition you provide in this tab for both content generation and translation purposes. This way you make sure that your messaging is consistent across all markets from the very beginning.
### Define tone of voice
Your tone of voice is what gives your brand character. Even automated translations should sound recognizably **you**.
  1. Add a short description that captures your messaging style. Example: _Professional yet approachable. Clear, concise, and empathetic._  

  2. **Save** your settings.


### Configure translation options
Now it’s time to teach AIRA how to handle your translations.
  1. Go to the **Translations** tab.
  2. Choose which data types can be translated: 
     * _Text_  

     * _Long text_  

     * _Rich text (HTML)_
**For admins:** The available types depend on what your developers have enabled.
  3. Create your **translation instruction (prompt)** – this is the most important specification that tells AIRA exactly what to do.  
Think of it as your brand’s “translation brief”. You can mention tone, terminology, words to leave untranslated, or audience details. 
Example:  
_Translate the text accurately but naturally for local audiences. Keep the tone light and friendly, but still professional. Do not use emoticons. Keep brand names and slogans in the source language._
  4. Keep the _Use tone of voice guidelines for translations_ option selected if you wish AIRA to use the tone of voice you defined earlier.
  5. **Save** your prompt and settings.
  6. Perform a few **test translations** to see how your setup behaves and adjust it as needed.


[![AIRA translations configurated](/docsassets/guides/automate-translations-with-aira/aira-translations-configuration.png)](/docsassets/guides/automate-translations-with-aira/aira-translations-configuration.png)
You’ve now built the foundation that tells AIRA _how_ to translate in a way that fits your brand voice. Although you can not adjust the **system prompt** that powers AIRA translation feature itself, you can still customize it to add your own flavor.
## Demo translations in Kbank
Let’s put everything you’ve learned into practice with a real example you can follow directly in Kbank.  
In this self-paced exercise, you’ll work with existing demo content to see how AIRA handles a complete translation workflow, from setup to review.
If you’d first like to walk through practical examples of working with multilingual content and translating different content items, you can check out our materials on [Multilingual variant of a page](/guides/digital-marketing/work-with-multilingual/multilingual-variant-of-page) and [Multilingual variant of a product](/guides/digital-marketing/work-with-multilingual/multilingual-variant-of-product).
### AIRA translation scenario setup
You are a Kbank marketing specialist preparing to launch a new product campaign across several European markets. The product is the **FastTrack Savings Account** – a flexible, easy-to-manage savings account designed for young professionals who want to grow their savings with confidence.
Your goal is to localize Kbank’s promotional content into **German** , **French** , and **Czech** , ensuring that every translation feels as confident, friendly, and inspiring as the original.
AIRA will help you handle the heavy lifting of translation, so you can focus on keeping the tone, benefits, and clarity consistent across all markets.
### Prepare your translation settings in Kbank
Before you start translating, set up your **AIRA prompt** to reflect Kbank’s approachable, motivational tone of voice, and other important instructions:
_Translate the text accurately but naturally for local audiences. Keep the tone light and friendly, but still professional. Do not use emoticons. Keep brand names and slogans in the source language._
This prompt helps AIRA capture the optimistic and empowering spirit of Kbank’s messaging; practical, but never dry.
### Translate your campaign materials using AIRA
Now, you’ll apply what you’ve learned in this guide to translate three ready-to-use pieces of content from the Kbank demo site.
  1. **News article:**  
Go to the _Content hub_ and find the article titled **FastTrack Introduction**.  
Translate it into German, French, and Czech using AIRA.  
This article announces the new product, so pay attention to how well AIRA preserves its aspirational and empowering tone.
  2. **Product page:**  
In the _Pages_ app, open the product page for the **FastTrack Savings Account**.  
Translate it into your target languages using AIRA’s cascade translation feature.  
Watch how AIRA handles the product’s key benefits like flexibility, mobile access, and competitive interest rates.  
After translation, double-check that all internal links point to the correct language variants.
  3. **Promotional email:**  
Still acting as the Kbank marketer, draft a short campaign email to promote the new account.  
The email should introduce the FastTrack Savings Account and highlight its main benefit:  
_Open your FastTrack Savings Account today and enjoy an exceptional 5% annual interest rate. It’s the easiest way to fast-track your financial goals._  
Once drafted, use AIRA to translate the email into German, French, and Czech, and review how well the tone carries over.


### Review and reflect on your first translation round
Once your translations are ready, spend a few minutes reviewing them from a marketer’s perspective.
  * Does the tone still feel approachable and trustworthy?  

  * Did AIRA follow your prompt, for example, did not use any emoticons, kept the brand names in English, and so on?
  * Do calls to action (“open your account today,” “start saving smarter”) sound natural?  

  * If something feels slightly off, tweak your prompt and try again. You’ll quickly see how small changes improve results.


### Why iteration matters in translations with AIRA
By performing exercise translations on Kbank content, you can observe how AIRA keeps your messaging consistent while giving you the flexibility to refine and guide the results.
Once you’re comfortable with these basics, you can start experimenting further. For example, try **different tones** (more formal vs. conversational), or **local phrasing** for specific markets.
Each new demo translation gives you more insight into how AIRA understands your brand’s voice, helping you shape translations that feel natural and ready to publish.
By completing this exercise, you’ve practiced the full cycle of automated translation with AIRA: **setup, execution, and review**. Now you can apply the same approach to your own campaigns and multilingual projects with confidence.
## Common pitfalls of automated translation to avoid
Even with automation, brief human checks can help to keep your translations professional and polished. Watch out for these common, but preventable pitfalls:
  * Forgetting to review links and taxonomies after translation.
  * Writing vague or conflicting prompts (AIRA needs clarity to perform well).
  * Assuming that every content type is supported – always check with your developer about the project setup first.
  * Skipping test translations before using AIRA on digital marketing content.
  * Expecting instant results; remember that AIRA processes translations asynchronously.


## Best practices for high-quality AIRA translations
How to get quick wins and optimize your translation process on the go? Here are a few small habits that are relatively easy to adopt but can dramatically improve your results:
  * Keep prompts short and focused on what matters most; most common things to look out for are **tone, terminology, and style**.  

  * Always **preview your translations** in a staging environment before publishing.
  * **Update** your tone of voice and translation prompts as your brand **evolves**.
  * **Review generated translations before launching** them into your project, much like you would polish any other type of content before publishing.
  * **Collaborate with local reviewers** for language precision and better cultural fit with the target market.


Following these best practices will help your automated translations sound accurate and as authentic as possible.
When it comes to quality assessment, the rule of thumb is to keep your eyes on the user reactions and reviews. Is no-one mentioning your translations? Congratulations.
Chances are that your translation quality is high, and you most probably won’t hear about it. Why? Because a good translation tends to read like a text written from scratch in the users’ native language and therefore rarely receives further comments.
## Wrap-up
In the previous sections, you’ve learned how to bring translations into the same space where your content lives. You also had the opportunity to see how much easier life becomes when your tools work _with_ you instead of _against_ you.
Thanks to AIRA, translations won’t be an extra step at the tail end of your process, but an **integrated part of your creative flow**. You can easily move from drafting an article or email straight into creating localized versions that carry the same energy, message, and intent as the rest of your messaging.
You now know how to:
  * Set up AIRA so it understands your tone of voice and brand identity.
  * Translate a news article, product page, and emails directly in Xperience.
  * Fine-tune your prompts until the results sound naturally local, not machine-made.


As you start experimenting with your own campaigns, you’ll notice how much faster your workflows feel, and how much easier it becomes to maintain a polished, confident tone across markets. You’ll also be in the right position to experiment with refining your translation prompts to make every output sharper and more on-brand.
Let AIRA handle the hard task of keeping every language version aligned while you can focus on shaping marketing messages that resonate with your audience, no matter where they live.
## Next steps
If you’re keen to **Optimize your AIRA translation prompts** , follow the trail to the next part of this series, and discover how small tweaks to your instructions can make your translations even better.
![]()
[]()[]()
