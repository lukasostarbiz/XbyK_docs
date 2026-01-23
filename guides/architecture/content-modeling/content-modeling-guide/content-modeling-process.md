---
source: https://docs.kentico.com/guides/architecture/content-modeling/content-modeling-guide/content-modeling-process
scrape_date: 2026-01-22
---

  * [Home](/guides)
  * [Architecture](/guides/architecture)
  * [Content modeling](/guides/architecture/content-modeling)
  * [Content modeling guide](/guides/architecture/content-modeling/content-modeling-guide)
  * Content modeling process 


# Content modeling process
## About this guide
This page is a part of the **Content modeling guide** which you should follow sequentially from [beginning to end](/guides/architecture/content-modeling/content-modeling-guide). You can also follow this series in [Content modeling guide](/modules/content-modeling-guide), which helps you keep track of your progress as you move through this sequential material.
## Design platform-agnostic content models
The goal of any content modeling process is to create a universal content model that will work independently of any specific [content management system](https://en.wikipedia.org/wiki/Content_management_system). The **content model** should focus on the semantic meaning of the content, in other words, what the content represents, rather than on how editors will manage this content in a particular system.
During a content modeling process, your team defines:
  * The structure of the content.
  * The different [content types](/guides/architecture/content-modeling/content-modeling-guide/what-is-a-content-type) required to meet the organization’s marketing objectives.
  * The relationships between individual content types to ensure editors can reuse or modify the content items easily.


## Who needs to be involved
A content model is not something one person creates and others blindly use. Defining a future-friendly content model requires collaboration between multiple stakeholders’ roles within the organization to ensure the content model covers all necessary angles. Make sure that you organize your content modeling team from the following roles:
  * **Senior content creators/editors** – Understand the day-to-day needs of the content team.
  * **Marketers (email, content)** – Provide insights into how they want to use the content in campaigns to ensure the content model aligns with broader marketing goals.
  * **Business analysts** – Ensure the content model and individual content types fit common business standards and the organization’s operational needs.
  * **Developers** – Ensure the model is technically feasible and scalable.
  * **Designers** – Help define the layout and presentation components that display the content across different channels and ensure that the content marketers will publish fits the design framework and brand guidelines.


## Pros and cons of the content modeling process
Like any process, content modeling has its benefits and drawbacks.
### Content modeling pros
  * **Consistency** – A well-defined content model ensures that all content types follow a consistent structure that fits all stakeholders’ needs, making scaling and content curation easier.
  * **Reusability** – With transparent relationships between content types, marketers and editors can reuse content across different channels or campaigns.
  * **Future-Proofing** – A universal content model can outlast individual content management systems, allow for growth and expansion depending on specific needs, and reduce the need for frequent redesigns when technology changes.


### Content modeling cons
  * **Time-consuming** – The initial investment from identifying the needs, defining individual content types and their relationships, specifying how the content model enables every involved team, prototyping, and validating takes time. We have often seen that content and developer teams do not talk to one another. Any lack of communication makes the content modeling process costly. We recommend running the content modeling sessions with an action group that consists of representatives of stakeholders to maximize benefits to the organization.
  * **Risk of oversimplification** – If you use a cookie-cutter, boilerplate solution, your content model may be too generic. It will likely ignore your team’s specific needs and will be hard to maintain as your organization’s content strategy evolves.


What happens if you get it wrong? Poor content models lead to inconsistent content, missed opportunities for reuse, and even content silos where different teams manage the same content in incompatible ways. This results in inefficiencies that affect your entire digital strategy.
## Content modeling sessions
Do not expect a wise person to come to your organization with all the answers and hand you _THE_ content model, including project requirements and developer specifications.
Defining a good content model usually requires running a series of structured, collaborative workshops involving various stakeholders. The number of sessions and their topics depend on the organization’s needs. The initial sessions typically consist of brainstorming, analyzing the current situation, and defining content types that support the organization’s business needs.
### Before creating the content model
  * **Run a content audit** – Review existing content and identify your organization’s content and what content is missing. Your content modeling sessions can support large content-related initiatives, like updating the company’s content strategy or brand redesign.
  * **Identify the content lifecycle** – Sit with your editor team and describe each content type: 
    * The content’s internal workflow (who and where creates the content, edits, reviews, and publishes it).
    * How long a piece of content remains relevant.
    * When it needs to be revised, updated, or archived.
  * **Assess the editing team** – Identify the people responsible for content creation and editing and ensure their responsibilities are clear.


### While defining the content model
  * **Prioritize a good editing experience** – Your content model should aim for an intuitive editing workflow. To avoid creating complicated, hard-to-use content types, we recommend that every stakeholder who participates in the content modeling sessions creates several items based on the modeled content prototypes. Creating test items helps streamline the editing process and ensures editors will only end up with a straightforward editing flow and using well-defined content types.
  * **Define basic workflows** – Outline how content will be created, reviewed, and published within the system. We also recommend that you consider the editor team’s experience and discuss the amount and complexity of guidance you should include for the different content types.


Every field in the content type should have a clear purpose. If the field’s label needs to be clarified more, use a tooltip or text below the field to guide editors through how they should input data or explain behavior so editors don’t need to guess.
[![See the editor guidance in the Hero banner widget on Kbank demo site](/docsassets/guides/content-modeling-process/cm-banner-widget-example-guidance.png)](/docsassets/guides/content-modeling-process/cm-banner-widget-example-guidance.png)
### Evolving content model
You’ll never get it right for the first time. Your content and content strategy will evolve as your team learns what tactics and processes work.
You should ensure you don’t lock yourself into one specific solution during the content modeling process. This usually happens when editors request to work with content directly in the presentation layer, typically sprinkling their data with HTML styling tags. We recommend you resist the temptation of storing the content with its design.
Instead, **prepare your content for evolution**. A well-structured content model enables you to reuse content types easily across different campaigns or channels, making your marketing efforts more efficient and consistent.
Your content model needs to be flexible enough to grow with your content needs. During the content modeling process, remember to discuss how you’ll handle requests to improve specific content types based on editors’ feedback once you launch the project. Describe what the approach to updating content types will look like and get a buy-in from stakeholders.
There are different approaches to how your content model can evolve. Check out a short article that outlines the potential strategies of [safely evolving a content model](https://community.kentico.com/blog/safely-evolving-a-content-model-with-expand-and-contract).
## Where to document your content model
Teams typically store their content models using various software and tools depending on their specific needs, preferences, and the level of complexity of their content strategy.
We’ve seen different options from collaboration tools like Excel spreadsheets, Word or Google documents, notes in Notion, diagramming tools like [Miro](https://miro.com/), [FigJam](https://www.figma.com/figjam/), [Diagrams.net](http://Diagrams.net), or specialized custom-built tools.
For example, we keep the content model for our Kbank demo site in a single FigJam file.
Ultimately, which tool you choose depends on your team’s size, content complexity, technical expertise, collaboration needs, and integration with existing workflows. Assess your team’s requirements and goals before you select the most suitable tool for storing and managing your content model.
### Use shared vocabulary to standardize your content types
Though no two content models are the same, it helps when teams follow general guidance. Remember, your content model defines how editors manage content and create meaningful messages for their audience. Though it’s not often mentioned, other computers and systems, such as search engines, are part of your audience. It’s vital to structure your content model so that both humans and machines understand it.
When talking to Kentico’s clients, we found that clients who used a standardized library of content types helped them answer related business challenges, such as how to define _metadata_ schemas for their marketing content or commerce products that other systems either feed (e.g., data pulled from an ERP system) or consume, such as search engine crawlers.
The most commonly mentioned source of inspiration for defining properties (aka shared vocabulary) for different content types is [Schema.org](https://schema.org/), which currently contains over 800 type definitions. After you define the content types that your business domain will likely use in broad strokes, we recommend looking at what properties are usually associated with the types you’ve identified.
### Make your content types semantic
Defining the content types doesn’t begin when your designers finish the designs or wireframes. With a bit of stretch, you can say it’s the other way around: your content team should be able to deliver any content, including any marketing messages they want in any designs or using any content management system. Different systems will have somewhat different ways to edit the content types. Still, each content type’s meaning and purpose should stay the same nonetheless.
Instead of designers telling the marketing team what the page or email will look like, the content modeling team, which includes content creators, SEO specialists, and email marketing specialists, needs to inform the designers what data they need to display across different channels.
We recommend that the content types reflect the business domain’s reality; if the team publishes product information, store the data in a structured format that reflects the data type. The structured and predicable format makes the content easily reusable on a different marketing channel.
[![Example of a “Loan” product content type in the Kbank demo website.](/docsassets/guides/content-modeling-process/kbank_demo_site-product-loan-content-type.png)](/docsassets/guides/content-modeling-process/kbank_demo_site-product-loan-content-type.png)
Kbank demo site contains several _product-like_ content types modeled using [financial product](https://schema.org/FinancialProduct) and [Credit card](https://schema.org/CreditCard) in the <https://schema.org/> for inspiration.
The correct content model with the right content types will allow your marketing team to **benefit from Xperience’s content versatility**. At the same time, if you want to provide your customers with **smooth interactions** with your business and your marketing team with a **pleasant editing experience** , your content types within your model should respect Xperience by Kentico’s specifics.
### Empower your marketers through effective content model
We’ll repeat ourselves in this place, but our Customer Success team learned from many different projects that content modeling is a critical process. A good content model will enable marketers to design powerful digital experiences. It creates a strong basis for any marketing strategy. Your content team can focus on creating effective content. They can rest assured that they are not omitting critical day-to-day parts, such as adding new pages to the website channels. A good content model allows the content team to tailor their content to their specific marketing needs. See more in a [blog post](https://www.kentico.com/discover/blog/what-is-content-modeling) on [kentico.com](https://www.kentico.com).
### Recommendations – planning content production**
Before diving into prototyping your application in Xperience, we recommend that you:
  1. Gather your project’s [environmental and functional requirements](/guides/architecture/xperience-implementation-handbook/gather-requirements).
  2. Audit your existing content and align it with your content strategy.
  3. Identify different types of content that you want to serve through Xperience during the content audit. At this stage, our customers often repeat the same or similar mistakes that lead to a broken content experience. How can you avoid them? 
     * Think of customer journeys. What is the purpose of each different content type? How will your customers interact with these types? What action (conversion) do you want this content to drive? In which parts of the customer journey?
     * Think about your content: an article, a testimonial, a company address, an annual report, a biography, FAQs, videos, or case studies.
       * It’s common to look at content from the perspective of what it will look like (design) rather than what your team will use the content for (purpose). Focusing too much on the layout usually results in binding content types with their presentation–which is often unique for a specific channel, such as a website or emails. And when your editors decide to reuse content across different channels, they’ll have to clear the data off any presentation information. Ever tried to copy a piece of content from Word to a website page? You know what we mean.
     * When finished, you should have the structure and purpose of all content types (usually in a large spreadsheet) and a clear idea of the functionality your developers need to build.
  4. Have a clear idea about what you want to achieve with your content and which strategies will help you effectively serve your content to your website visitors.
  5. Make sure you know what [developing Xperience applications](/documentation/developers-and-admins/development/website-development-basics) requires in general.


With this information in mind and content types defined, the following pages will explain the different options for storing and displaying content with Xperience. This guide will give you a solid foundation for creating the content model for your website.
In the meantime, your designers can start prototyping and wireframing your customer experiences. Stay calm in your project just because you don’t know all the bells and whistles of how specifically Xperience stores and displays content.
At the end of the content modeling session, you should have a complete, fully defined **content model** that covers:
  * All **content types** the organization produces.
  * The **relationships** between content types.
  * The **data and structure** each content type stores and provides.
  * The **lifecycle** of each content type.


## Additional resources about content modeling
If you are new to content modeling, we have collected the following third-party resources that cover this vital topic.
  * HeadlessCreator weekly: 
    * <https://www.headlesscreator.com/course/content-modeling-weekly>
  * Content modeling free e-book: 
    * <https://contentmodeling.com/index.php/free-e-book/>
  * Kontent: What is content modeling: 
    * <https://kontent.ai/learn/model/intro-to-content-modeling>
  * Sanity’s Content Modeling Guide: 
    * <https://www.sanity.io/content-modeling>
    * <https://www.sanity.io/content-modeling/content-modeling-foundations>
    * <https://www.sanity.io/content-modeling/how-to-create-a-content-model>
    * <https://www.sanity.io/structured-content>
  * Contentful’s content modeling basics: 
    * <https://www.contentful.com/help/content-modelling-basics/>


## What’s next?
You’ve gained a basic overview of what the content modeling process looks like and what you should consider when you’ll define the content model for your team. The [next page](/guides/architecture/content-modeling/content-modeling-guide/model-taxonomies) explains how categorize your content in Xperience applications. You’ll learn about different options for building taxonomies, and how you can leverage taxonomies to drive your presentation layer’s behavior.
![]()
[]()[]()
