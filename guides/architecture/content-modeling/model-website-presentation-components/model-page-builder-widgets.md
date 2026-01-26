---
source: https://docs.kentico.com/guides/architecture/content-modeling/model-website-presentation-components/model-page-builder-widgets
scrape_date: 2026-01-26
---

  * [Home](/guides)
  * [Architecture](/guides/architecture)
  * [Content modeling](/guides/architecture/content-modeling)
  * [Model website presentation components](/guides/architecture/content-modeling/model-website-presentation-components)
  * Model Page Builder widgets 


# Model Page Builder widgets
Xperience Page Builder is a user-friendly interface that business users, such as website editors, content administrators, or marketers, use to manage web page content. During content modeling sessions, business analysts and solution architects, in collaboration with UX designers, front-end engineers, and stakeholders from the editor team, define which page components editors will use. This article will provide several recommendations for creating Kentico Page Builder widgets.
After reading the article, you should be able to:
  * Understand the role of widgets and their function in shaping website content and user experience.
  * Understand the different types of widgets and their uses, including those that store data, reference data from the Content Hub, and display data from content types.
  * Define widget properties while considering both the editing capabilities required by editors and the potential impact on the company’s content strategy.
  * Balance widget usage by considering whether the content being created should be reusable or unique.
  * Validate every decision related to widgets with ROI, considering the complexity of adding new features, tweaking existing configurations, or simplifying options.


## Prerequisites
  * You should have basic knowledge of [content modeling principles](/guides/architecture/content-modeling/content-modeling-guide).


## What are widgets
When defining the presentation model for editors, it’s essential to understand the different building blocks, their behavior, and their role in building a website experience. Well-designed widgets contribute to a smoother and more efficient editing process and empower editors to create engaging web experiences.
**Page Builder** is not the only approach to display content on the website, but compared to other options, it’s the one that editors prefer the most. In Xperience website pages, the **page template** defines the overall look and feel of the page, while **sections** serve to arrange content, and **widgets** display and often style it.
**Widgets** are the smallest, reusable components that website editors use to add or manage content on the website and create highly engaging pages with different layouts. Widgets play a crucial role in shaping how editors present content on the website and shape customer experience.
## Focus on your content goals
From a developers perspective, widgets are view components with potential additional properties defined through a class that implements the IWidgetProperties interface. Learn more about how developers implement widgets in [guides about the Page Builder](/guides/development/page-builder).
Page Builder provides a flexible environment that editors adore. Its widgets can display content from various sources, including **reusable data** stored in the _Content hub_ , **one-off data** stored on individual website pages, and data **ingested into Xperience via APIs**.
### Three types of widget
When editors see how widgets work, they want to use them for everything, from adding content to the website to defining their core content, which they want to reuse across different channels, to designing what data will be served through a headless channel.
Over-relying on widgets for all aspects of content management and data delivery can lead to significant drawbacks for your content unless you approach it right.
### Widgets that store data
Editors gravitate toward this widget type because it promises a simple and convenient way of adding data to the website. Unfortunately, this simplicity comes at a cost. All the data—regardless of its purpose—is stored within a single database cell, which holds the data configuration for the entire page.
At the same time, it’s evident that not all content is meant to be reused across different pages or contexts. Some information is inherently unique to a specific page or moment. For instance, time-sensitive announcements, event details, or localized content may fall into this category.
Typically, you’ll see one-off widgets to display:
  * Website page headings
  * Specific website-only information, for example, on campaign landing pages or timely information on the Home page
  * Personalized content on the website


In these cases, storing the data directly on the page makes sense.
Your browser does not support the video tag. 
”
### Widgets that store references to data in Content hub
Though it’s a small step from the previous one, this second widget type introduces enormous benefits. Editors will store their data in the _Content hub_ and then use the widget to craft the page experience. Xperience allows editors to create reusable content items on the fly from any channel, including the website channel. See the following video.
Your browser does not support the video tag. 
”
Displaying reusable content via widgets doesn’t lock your content into one specific channel, so it comes in handy in many client scenarios, especially with clients with less experience with complex content models. However, it has one significant drawback.
Xperience stores the combination of widgets and the content they contain or reference into a single database table cell which makes the whole content item. While editors can reuse the content item referenced by the widget itself on other channels, they cannot reuse the widget and its configuration.
For example, the Featured content widget in the Kbank demo site displays data from the content type called Featured content. Featured content is a combination of a title, text and an image which doesn’t represent any semantic type, such as a case study, article or product. You will read more about the Featured content widget in the following article.
### Widgets that display data from semantic content types
The third widget type often poses challenges for editor teams but offers significant benefits. These widgets leverage data organized into structured and meaningful types. We can call these representations _semantic_ content types as they represent not just the data, but also what this data means from the business perspective.
For example, when editors create a listing page with latest articles, they do not add title, short texts and images per each article, but they use a widget that displays articles’s, summaries, and thumbnails which are stored as [a reusable content type](/guides/architecture/content-modeling/model-reusable-content/model-a-reusable-article) and displayed through [an article page](/guides/architecture/content-modeling/model-website-content-types/model-an-article-page). Structuring content into _semantic types_ that keep data separate from their presentation boosts editor’s efficiency, help ensure content consistency, and improves content curation.
Take a look at the Kbank’s _Product_ content type. It stores [product-specific data in a reusable format](/guides/architecture/content-modeling/model-reusable-content/model-a-reusable-product).
[![Mortgage product item in the Content hub](/docsassets/guides/model-page-builder-widgets/product-content-type.gif)](/docsassets/guides/model-page-builder-widgets/product-content-type.gif)
However, editors are not limited when creating a product page, as designers modeled the widgets to display the referenced data. Even though the Product content type is composed of other content types, editors can easily create the reusable product and all its content items directly from the website channel.
Each content type has its [role in the company’s brand experience](https://www.clearvoice.com/resources/how-to-define-content-levels-brand-experience/). We recommend designing a semantic content model and defining the content type’s data structure based on “what entity the content type represents,” even if you define the content model as “just a web page.” For example, your editors will create pages to promote events and use widgets to display [event details](https://schema.org/Event), share information about different [speakers](https://schema.org/Person), feature [brands](https://schema.org/Brand) that sponsors the event or provide details about the [event organizers](https://schema.org/Organization). You can consult [schema.org](https://schema.org/docs/full.html) to see what properties should different semantic content types contain.
Your browser does not support the video tag. 
”
### Widgets that display data from 3rd-party integrations
This widget displays data from outside sources like ERPs or databases. Editors provide an identifier, and the widget’s code fetches the data. There are various methods to implement this, allowing editors to style the content using widget properties.
### Which widget option to choose?
While widgets streamline editing, striking a balance is crucial. Consider whether the content you’re creating should be reusable or if it’s truly one-of-a-kind. Understanding the trade-offs lets you decide which widget types to use and when.
In summary, while widgets enhance the editing experience, mindful content modeling of both data and presentation layers ensures that your content remains adaptable, maintainable, and relevant.
We need to play the ultimate _“It depends on”_ card. Nevertheless, based on the Kentico team’s experience, we recommend storing the content in a structured format and displaying it through widgets on the website. Editors can create free-form pages, but they’ll store data in a structured manner, making them easily accessible should they need to reuse them on another website page or in a different channel.
## Simplify, not oversimplify content management with widgets
From an editor’s perspective, nothing beats working with widgets. Editors just pick the widget, add content, change the size and color of the text, upload an image, and save the page. If they miss the spot, they can drag and drop the widget into the location and they are done. Sounds magical, right?
A neat editing experience is where the good story ends. You’ve already heard that Kentico serializes the page with the Page Builder, the widgets’ content, and their configuration into a single field in the database.
Here comes another twist; the page hosting the widget holds this content, while other pages are unaware. Bear in mind that editors who don’t oversee this part of the website are also unaware.
What if they want to reuse this content and dazzle your audience across multiple pages? Editors have two options: copy-paste the widget’s content (tedious, right?) or save the entire page as a template (a bit heavy-handed). What do they end up with? A tangled web of disconnected content variants, in other words, a content curator’s nightmare.
A better approach is to use widgets to display structured content from the Content hub (meaning use widget type two or three from the list above). Keep your free-form widgets for only specific situations where the content needs to be truly unique and one-off - campaign landing pages or sidebar enhancements.
## Widget properties
Xperience provides tools your developers use to define how editors input or configure content. It’s highly customizable, and it’s very common for editors from different companies to use widgets with similar capabilities, tailored to their specific project implementations.
### Widget configuration options
Editors can interact with widgets in two ways: through **side panels** that contain **configuration options** or through **inline editors**. Discuss how editors will use the widget and choose the configuration that helps them in their specific use case.
When deciding between [inline editors and configuration dialogs](https://dev.to/seangwright/kentico-12-design-patterns-part-21-mvc-widget-tips-3img), clarify what data the widget will show on the website channel and how editors will input the data.
  * Use **side panel configuration** when the editors display reusable Content hub or input and configure the look and feel of one-off content that will live only on this page.
  * Use **inline editors** if business users input one-off or rarely reused content within the widget with just basic rich text editing.
  * Combine **side panel and inline editors** when the editors input data into the default configuration and rarely change the widget’s configuration, such as the color of the hyperlinks, heading sizes, or image theme.


Before you hand any specifications to developers, discuss behavior preferences with stakeholders. As a rule of thumb, if default configuration suffices most of the time and adjustments are rare, inline editors are a good choice for you.
## Defining widget properties
When defining widget properties, consider both the editing capabilities that editors require and the potential impact of their work on the company’s content strategy.
While editors need certain functionalities, we must also be cautious about vanity requirements that could deviate from the established brand book.
To enhance content longevity, we recommend structuring content either within a dedicated reusable type in the Content hub or by using a dedicated widget property that stores only the necessary data. Other fields will influence the data’s appearance.
It’s quite common for editors to require control over, for example, colors they’ll use to sparkle their content. Instead of introducing, for example, a color wheel with unlimited color properties, we recommend creating dedicated collection of choices that represent brand colors. The more you limit editors’ designing capabilities, the easier it will be to support future redesigns or supporting new brand changes or rebranding.
## Handle complex widgets
Widgets are flexible enough to support almost any behavior, including adjusting HTML styling and content layout within a page. For instance, with the correctly implemented custom properties, editors can control the spacing between different content elements in a website section.
While there are no strict limitations, it’s essential to note that Kentico doesn’t evaluate the combined configuration of widgets and sections and doesn’t help editors identify whether they input conflicting instructions. To achieve a transparent editing experience, consider using sections to organize the content structure and widgets to incorporate that content onto the website page seamlessly.
## Widgets for different teams
Widgets feel like a Swiss Army knives that allow your team to work with content. But here’s the twist: the widget’s capabilities aren’t defined by the Xperience version.
Xperience provides the tools developers use to create a highly flexible, granular editing interface and tailor it to each team’s needs. If your team consists of novice editors who tiptoe through the interface and worry they’ll break something, provide limited configuration options and decorate properties with instructions to guide them.
At the same time, skilled editors crave complexity and nuance. They’ll prefer widgets with rich configuration options. It’s like handing a painter a palette of a thousand hues. They’ll craft effective webpage designs with information placed where it should be.
## Provide instructions on how to work with complex widgets
Two widgets of the same name will likely be different when you see them in different projects. Widgets can range from simple ones that offer just one editing property to complex ones that support diverse scenarios based on editors’ choices.
See the following image that shows different instructions editors see in the Kbank’s Hero banner widget.
[![](/docsassets/guides/model-page-builder-widgets/hero-banner-properties.png)](/docsassets/guides/model-page-builder-widgets/hero-banner-properties.png)
When preparing the content model your developers will implement, ensure your requirements contain the editor instructions you want to help your editors with. Do not leave creating these instructions to developers. Developers and editors often think differently, and you need to focus the guidance accordingly.
### Document complex editing flows before development
It is quite common for editors to come up with seemingly simple ideas, but when you inspect them closer, they reveal their true complexity. These ideas, though straightforward from the editors’ perspective, often require elaborate backend code to manifest the correct behavior. During content modeling sessions, you need to bridge the gap between developers, editors who will be working with the solution, marketers, and other stakeholders.
Imagine you’re shaping widget requirements. You define precisely how these widgets should behave. It’s not just about documenting the obvious – widget X will have Y and Z properties – in many cases, you need to capture the nuances. Detail the expected behavior to ensure all stakeholders—developers, designers, and product managers—align with the requirements. When you hand over these well-thought-out requirements to the development team, they’ll appreciate your clarity.
Now, picture yourself sitting across from the technical team used to working with meticulously crafted technical documentation. If your widget contains a complex series of actions, each of which can trigger some behavior, create a flow chart detailing the flow. As you walk your team through the flow of editor actions, you’re not merely conveying design and development instructions but showcasing your due diligence. The flow isn’t just a sequence of steps; it’s a narrative – a story of how the widget combines user interactions and backend logic.
This flow will serve more than developers. When you present it, you’re ensuring everybody says, “Look, we’ve thought this through. We’ve considered every twist and turn. This widget isn’t just lines of code; it helps editors succeed.” With a detailed flow approved by all stakeholders at the start, you can limit future iterations.
[![The flow of dependent fields in Hero banner widget](/docsassets/guides/model-page-builder-widgets/hero-banner-editor-flow.png)](/docsassets/guides/model-page-builder-widgets/hero-banner-editor-flow.png)
## Widget personalization
Editors of the Xperience website can tailor their marketing messages to appeal to different audiences.
  * **Widget personalization**. Editors can handpick content variants targeted at a contact group. Imagine tailor-stitching a bespoke experience: “Ah, this content is for the early risers; that content is for the night owls.”
  * **Personalization in code**. Developers can code personalization conditions directly into a website’s markup. In this case, editors have limited options to customize the messaging from the administration UI. Personalizing through code is especially helpful when you have precisely defined your content strategy to support the customer journey. Developers predefine how the content should behave, personalization _just happens_ , and editors can move their focus elsewhere.


## Test widgets with real-life content
UX designers love to jump right in and start cranking out website wireframes. They usually don’t have content to work with at the start, so when they need to fill space with something, they paste in some placeholder text or images and move along. These solutions work, but partially.
More often than not, your team will discover that your content barely fits the wall of nonsensical _Lorem ipsum_ text you’ve used to test the widgets.
Don’t compromise on dummy content. Since you [completed the content audit](/guides/architecture/content-modeling/content-modeling-guide), you’ve likely already molded your content strategy and defined your brand tone and voice.
Use your existing content from the audit and strategy and recreate it using the new widget prototypes and their properties. Find out if you can design your content in a way your brand book defines, identify other needs for widget configuration, and discuss options with your project owner.
## Validate every decision with ROI
Every project implementor needs to stay vigilant and monitor the requests from the stakeholders and the editing team.
Adding new features, tweaking existing configurations, or simplifying options sounds straightforward, right? However, nothing is as simple as it seems.
If your existing widget already moonlights as a Swiss Army knife (meaning it supports complex behavior and different configuration options), updates can get complicated. It’s not just about removing a property; it’s about orchestrating a series of changes.
When editors dream up fresh behaviors, such as updating the widget, it involves more than just developers:
  * UX and Front-End Engineers need to tweak how the content in the new widget’s configuration renders across devices.
  * Developers often must adjust complex series of if-else conditions and deploy their changes in UAT.
  * Testers guard user experience and ensure visitors don’t trip over surprises.
  * Stakeholders must ensure the new widget’s behavior aligns with their vision.


From experience, it’s safe to say that this update cycle rarely gets done in one pass. So, before you request any complex or straightforward change, remember that it can also mean getting back to the drawing board and seeing what this piece of content will do and what it will look like.
## Summary
With the right properties, Page Builder widgets transform content editors’ experiences and help them design their website content without developers. Editors can craft their pages with finesse, tailor the user experience, and guide website visitors toward conversion, and boost ROI.
## What’s next?
You have learned about different areas to consider when defining the widgets editors will use to create pages in their website channels. In the next guide, you’ll explore what to consider when [defining presentation components](/guides/architecture/content-modeling/model-website-presentation-components/model-landing-page-components) defining presentation components editors will use to build effective landing pages.
[Continue to the next guide](/guides/architecture/content-modeling/model-website-presentation-components/model-landing-page-components)
![]()
[]()[]()
