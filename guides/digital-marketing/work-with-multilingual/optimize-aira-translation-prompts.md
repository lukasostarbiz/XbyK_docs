# Optimize AIRA translation prompts
  * How-to| [ Copy page link ](guides/digital-marketing/work-with-multilingual/optimize-aira-translation-prompts#) | [Get HelpService ID](guides/digital-marketing/work-with-multilingual/optimize-aira-translation-prompts#)
Core MVC 5


[✖](guides/digital-marketing/work-with-multilingual/optimize-aira-translation-prompts# "Close page link panel") [Copy to clipboard](guides/digital-marketing/work-with-multilingual/optimize-aira-translation-prompts#)
**Advanced license required**  
Features described on this page require the Xperience by Kentico Advanced license tier.
Marketers spend a lot of time creating content that turns a first‑time visitor into a loyal customer. But if they just swap words between languages and call this content “translated”, all their effort can be ruined. A bad translation will not keep the **intent, brand voice, and clarity intact** , and all the work your team put in may be wasted.
With AIRA, they can evaluate the translated content and then adjust the prompt to improve translation quality. Once they nail the prompt, they can use it for every other translation which will reduce the need for retranslation. In short: better quality in, better quality out.
## Prerequisites to translations with AIRA
Make sure a few basics are in place before you start optimizing prompts:
  * [AIRA](documentation/business-users/aira) needs to be available in your project and enabled for translations.
  * You know how to set up and start a translation in AIRA (the workflow itself is covered “Automate your translations with AIRA”).
  * You have access to AIRA app, where you can adjust and optimize your prompts in the **Translations** tab.


## Translation optimization scenarios
You don’t need to be a localization expert. We’ll go through the best scenarios on how to steer AIRA’s translations toward your brand voice and requirements. You will learn how to write short, focused prompts that improve quality, speed up reviews, and keep terms consistent. Your main takeway should be:
  * Translate with brand voice and approved terminology across your target languages.  

  * Prevent common AI translation pitfalls with do-not-translate (DNT) lists and glossaries.  

  * Speed up publishing time and reduce post-edit effort.


## How AIRA uses ad-hoc translation prompts
When you start translation, AIRA uses a default Xperience translation prompt. You can, however, customize the default system translation prompt by providing additional translation context that will be scoped to your specific project requirments.
To achieve best results, **keep your prompts brief and precise**. Too much information given in a random, unstructured format will confuse the [Large Language Model (LLM)](https://en.wikipedia.org/wiki/Large_language_model) that processes your translations on the backend. The more organized and clear you are in your prompt, the better your translation results will be.
These instructions influence the tone of voice, terminology, style, formatting, and SEO-related fields. You can also add DNT lists, or lists of forbidden phrases, which help AIRA determine how NOT to translate certain things. This is especially important in case of product names you might want to keep in the source language (like “FastTrack” in “FastTrack savings account” you can find in the Kbank demo project), or in cases where specific translations might be culturally inappropriate or sensitive.
You can instruct the model with clear prompts and give it **well-defined boundaries similar to a creative brief** , which helps AIRA make similar choices an editor would. Your prompts guide the model, but they do not override project settings or translate content that has been excluded from translation.
## Anatomy of an effective translation prompt
A great prompt reads like a compact brief and is easy for AIRA to navigate. Another benefit of keeping your prompts scannable and specific is that your editors and marketing colleagues can easily understand it, and reuse it for their projects, too.
Include the following elements in short sentences or bullet points, for clarity:
  * **Audience and intent.** Who will read the translated text, and why.  

  * **Tone of voice (ToV).** If you have a strong brand tone of voice, use it. If not, define a tone that is most suitable to the business vertical your company operates in. For example, _clear, compliant, reassuring_ ToV for banking, _empathetic, evidence-based_ for health and life sciences, _precise and technical_ for manufacturing and so on.  

  * **Formality and address.** Specify formal or informal address and neutral wording based on your vertical, or where regional variants may differ.  

  * **Terminology.** Provide a mini-glossary with preferred translations for the individual target languages.  

  * **Do-not-translate (DNT).** Add brand names, product names, acronyms, model numbers.  

  * **Style and formatting rules.** Specify headline case, sentence length, forbidden phrases, CTA patterns.  

  * **SEO constraints (if relevant).** Set title length, meta description limits, keyword intent, and Open Graph copy expectations.  

  * **Quality bar.** Ask for clarity, cultural appropriateness, and consistency across the whole page or item.


Keep prompts under ~200–300 words. Use bullet lists. Put the most critical constraints first and make them stand out with an exclamation mark.
## Quick-start translation prompt templates by vertical
The following examples show how a few lines of targeted guidance can dramatically improve translation quality. We include four often-occurring verticals because they cover distinct tones and limitations. Banking is especially useful for hands-on practice in our Kbank demo website, while the others help you model scenarios you may meet later.
For users new to AIRA translations, we recommend using these prompts as **demo material**. You can either use them when translating already existing pages and content items in Kbank, or you can create new content items in the demo environment and translate those, building on what you already learned and practicing multiple skills at once.
As you gain more confidence in your ability to prompt AIRA to give you the results you want, you can adapt these demo prompts to your own use case, and use them on real projects.
### Banking/Financial
Since our Kbank demo website is modeled as a fictitious banking institution, you can easily test out the demo scenario. Feel free to experiment with your prompt, and work iteratively to translate your way to best outcomes.
  * **Use cases:** Translating product pages, rate explanations, or campaign copy.
  * **Protected terms (examples):** Kbank, TravelGuard, SmartSave Junior, K-ID.
  * **Avoid:** Promising guaranteed outcomes.
  * **Risky:** Informal slang that undermines trust.


**Example ad-hoc prompt:**
  * Audience: Parents considering a child savings product.  

  * Tone: Clear, compliant, reassuring. Avoid hype.  

  * Formality: Neutral to formal.  

  * Terminology: “savings account” → [TL term]; “interest rate” → [TL term].  

  * DNT: Kbank, SmartSave Junior, K-ID.  

  * Style: Short sentences, no exclamation marks, no guarantees. Align with regulatory-safe wording.  

  * SEO: Title ≤ 60 chars; meta ≤ 155; include the concept of “child savings” in natural phrasing.  

  * Quality: Ensure cultural appropriateness, consistent terms across page, and correct local date and number formats.


### Healthcare
Experiment with this sample prompt in the demo environment or use it as inspiration to ideate translation prompts tailored to your specific projects.
**Use cases:** Service pages, patient guidance, educational content.  
**Protected terms:** Khealth, CarePath, names of devices or programs.  
**DNT examples:** COVID-19, MRI, CT, “stat”, WHO.  
**Template highlights:** Empathetic and precise tone, no medical diagnosis or advice, plain-language explanations, consistent use of medical acronyms.
**Example prompt:**
  * Audience: Prospective patients and caregivers.  

  * Tone: Empathetic, evidence-based, plain language.  

  * Formality: Neutral.  

  * DNT: Khealth, CarePath, COVID-19, MRI, CT, WHO.  

  * Terminology: Use standard clinical terms accepted in the target language; avoid colloquialisms.  

  * Style: Short paragraphs, active voice, no medical advice; encourage seeking professional guidance.  

  * Quality: Ensure sensitivity, remove idioms, and keep reading level accessible to a general audience.


### Coffee/hospitality
**Use cases:** Menu items, product cards, campaign banners, blog posts.  
**Protected terms:** Dancing Goat, GoatRewards, specific blend names.  
**Template highlights:** Friendly and upbeat, foodie vocabulary, maintain product names, adapt idioms, keep CTA playful but clear.
**Example prompt:**
  * Audience: Coffee lovers and café visitors.  

  * Tone: Friendly, upbeat, sensory.  

  * Formality: Informal but respectful.  

  * DNT: Dancing Goat, GoatRewards, [blend names].  

  * Style: Keep product names as-is; adapt idioms to natural local expressions; limit to one CTA per section.  

  * SEO: Include coffee style or origin naturally in meta text when relevant.


### Manufacturing
**Use cases:** Product specs, safety instructions, installation guides.  
**Protected terms:** Product codes, model numbers.  
**Template highlights:** Precise, technical tone; unit consistency; keep model numbers and tolerances exact; avoid marketing fluff.
**Example prompt:**
  * Audience: Engineers and procurement.  

  * Tone: Precise, technical, concise.  

  * Formality: Formal.
  * DNT: All model numbers and codes.  

  * Terminology: Use industry-standard terms in the target language; avoid synonyms once chosen.  

  * Style: Numbered steps, consistent units, no adjectives implying performance beyond specs.  

  * Quality: Check all measurements, units, and safety statements for clarity and consistency.


## Build mini-glossaries and DNT lists
As you may have noticed, small, **reusable terminology lists** are the easiest way to lock down consistency across languages. You do not need a full terminology database to see the results in your AIRA translations. You can start to learn and expand your terminology over time.
A few tips on handling terminology and DNT lists:
  * Begin with your existing brand or SEO term lists and product catalogs.
  * Start small and add 10–20 key terms per language.  

  * Create a short DNT list of brand names, product names, acronyms, regulated terms, and other words and phrases you wish to retain in source language.  

  * Use consistent formatting when pasting your list into the AIRA translation prompt, for example, “Term: [Preferred translation].”
  * Keep a shared document of approved terms to reuse in different prompts, and across your team.
  * Regularly review and update your terminology and DNT lists as the needs of your company or the industry standards evolve.


## SEO-specific prompts
SEO fields often get overlooked during translation, which can lead to poor discoverability of otherwise engaging content. You can add short, to-the-point instructions regarding SEO into your prompts, and nudge AIRA toward length-appropriate, natural meta content that fits the **search intent of your local audience**. Your main target should be the specific region/market for whom you are translating your content, not your overall campaing audience.
Keep in mind a few tips for better SEO-related translations:
  * Specify target title requirements, if any are in place (for example, Title: “Meryl Streep” → Czech [Meryl Streepová]).
  * Configure title and meta description length limits, and the search intent (for example, _commercial_).  

  * Ask for natural language, not keyword stuffing.  

  * Clarify whether to translate or retain the slug separately from the translation of the body of the text.  

  * Provide Open Graph title or description style if it’s used in your project.


**Reusable SEO Snippet:** SEO: Title ≤ 60 characters; Meta ≤ 155 characters ; natural inclusion of “[specific keyword]”; avoid duplicates; OG copy mirrors the source headline tone.
## Iterative translation prompt improvement
The most natural way to better results with translation prompts is to **treat prompt writing like editing**. Try a specific prompt or start with just one content item, translate your content with AIRA, review the translation output, adjust your prompt if you’re not happy with the results yet, and try again. This keeps your effort light while you’re improving quality one step at a time.
The iterative approach suits automated translations well, too, as changing too many elements in your prompt may lead to confusing or inconclusive changes.
Try the following workflow, and observe that you’ll probably need less iterations over time as you perfect your prompts according to your needs:
  1. **Run Prompt 1** then **Review translation v1** for tone, terminology, and clarity.  

  2. **Identify gaps.**
  3. **Add** a maximum of 2–3 **concrete fixes** to the prompt, for example, “use formal address,” “replace ‘account’ with [approved term],” “remove idioms”, “do not use emoticons”.  

  4. **Run Prompt 2** then **Review translation v2**.  

  5. **Repeat** until the output meets your **quality bar** (more on translation success metrics later).


Change one or two variables per iteration so you can see what helped. Keep a running “best prompt” snippet for reuse.
## Reusable prompt templates and governance
Since no man is an island, and effective teamwork is often the key to timely, high-quality content, you may want to keep in mind the shareability of your prompts and your snippets. A few shared templates that are readily accessible and easy to navigate for your team will save huge amounts of time and effort. It will also create brand consistency, especially when multiple editors are working with AIRA translations at the same time.
We therefore suggest keeping your translation governance **simple and reusable**. After all, clear and well-structured instructions are easy to follow for content editors, as well as AIRA.
As a team, you can agree to:
  * Maintain a small set of brand or vertical prompt templates editors can paste into AIRA during translation.  

  * Store these templates in your documentation or a shared content item so editors always use the latest version.  

  * Encourage editors to suggest prompt improvements with an example of before and after translation.
  * Make sure they share what changed in their prompt for full context.
  * Administrators need to ensure that editors who are supposed to work with translation have access to the _Aira_ app so that they can adjust the prompt.


## Troubleshooting prompt outcomes
If the result of the translation doesn’t feel right, or if you can spot obvious mistakes, adjust the prompt, not your whole process. Few purposeful tweaks are often enough to push your translation outcome from so-so to “oh, I would’ve thought this was written in my native language from scratch”.
These quick fixes solve the most common issues:
  * **Inconsistent tone.** Add an explicit tone description, and one to two sentences in the prompt that represent your company’s tone of voice.  

  * **Brand terms mistranslated.** Move the term list up in the prompt, add a DNT list, and specify “use exactly as written.”  

  * **Over-casual, or over-formal translation.** State the desired formality and form of address explicitly (for example, “tú” instead of “usted” in Spanish for content aimed at younger users); add a “do not use slang or overly formal legalese” note.  

  * **Too long or too short.** Provide target lengths for sections or set maximum sentence length.  

  * **Cultural appropriateness.** Ask to avoid generic idioms and replace with neutral, locally-sounding phrasing.


## Best translation prompt practices
Prompts shape style and clarity, but they should also protect you from avoidable risks.
Use these guardrails to keep your translations reliable, and following any regulations that might be applicable to the content:
  * Avoid sensitive data in prompts; do not include personal information.  

  * Do not ask the model to invent rates, guarantees, or medical advice.  

  * Prefer clarity over creativity for regulated content (like medical, or legal translations).  

  * **Always review before you publish** your translations; prompts reduce the number of edits you might need to do, but **do not replace human validation**.


## Track translation metrics to improve workflow
Quality checks usually come at the end of any content creation or translation process, but they should be at the front of your mind even as you begin your project. Be sure to define success metrics – or **how do you define a translation that meets your standards** – so your team can see when they need to update their translation prompt, and how they should keep improving their prompts. Even simple measures practiced by everyone involved bring visible improvements.
Every team may come to different metrics that are important to them specifically. However, you can watch out for these qualities to improve your translation outputs in general:
  * **Reduction in post-edit time.** The better the prompt, the less iteration needed to bring the translation bar up to your standard.  

  * **Terminology adherence rate.** Spot-check ten to twenty terms per batch. Update your glossaries and DNT lists as needed.
  * **Tone compliance.** Have a seasoned editor check the translations against your brand tone of voice.  

  * **Issue rate.** If possible, keep tabs on the number of manual corrections, for example per 1,000 words. Observe if the issue rate decreases over time. If not, identify common types of these errors (tone, style, brand terminology) and target your prompt updates to mitigate those issues.


**Reuse and adapt to your needs** Test, adapt, and reuse these snippets as building blocks inside your ad-hoc prompts.
  * **Tone snippet:** Banking (clear, compliant, reassuring). Healthcare (empathetic, evidence-based). Service industry (friendly, inviting). Manufacturing (precise, technical).  

  * **Do-Not-Translate snippet:** “Do not translate: [brand name], [product title], [brand-related acronym]. Keep exactly as in source.”  

  * **Terminology snippet:** “Use these translations consistently: [Source term] → [Target term]. No synonyms.”  

  * **SEO snippet:** “Titles ≤ 60 chars; metas ≤ 155 chars; natural inclusion of [specific keyword]; avoid duplicates; OG mirrors source headline.”  

  * **Clarity snippet:** “Short sentences, active voice, no idioms, culturally neutral phrasing.”


## Wrap up
By now, you’ve learned how **small, well-structured prompts** can make a **big difference in translation quality**. You know which details to include (like tone of voice, terminology, and DNT lists), and also how to keep refining prompts through quick iterations. You’ve also learned that clear, consistent guidance helps AIRA stay on brand, reducing the need for manual edits later. Now it’s your time to shine and put everything into practice, one bite-sized prompt at a time.
Not sure where to start? Here are some small ideas that you can add to the working templates your team can use this week:
  * Add your brand’s 10–30 key terms and DNT list into the templates.  

  * Pilot your first optimized prompt on a single page, and translate it into one or two target languages. Run a few iterations and note how the results improve. If you aren’t sure how the translation workflow works, try translating an article on Kbank to see how your updated prompts work.
  * Record the time saved in post-editing compared to standard translation.  

  * Share your “best prompt” back with your team and discuss what gave you the best results.


As you experiment with prompt iteration and expand your library of ready-to-use prompts for different content types or campaigns, you also grow your confidence. With each project, you’ll see how a few extra lines of clear guidance can elevate translation accuracy, protect your brand voice, and make every localization effort faster and smoother. AIRA becomes most powerful when you pair its automation with your own creative direction.
Keep experimenting, keep improving, and watch your content resonate naturally across every local market.