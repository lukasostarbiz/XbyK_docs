---
source: https://docs.kentico.com/modules/model-website-presentation-components/model-page-builder-sections
scrape_date: 2026-01-26
---

Module: Model website presentation components
6 of 11 Pages
# Model Page Builder sections
Page Builder **sections** help editors define the page’s **vertical and horizontal layout**. Editors use them to create space on the page where they place content using Page Builder **widgets**. As is true for other Page Builder components, if you design _sections_ properly, editors will be able to craft pages with varying layouts and achieve their goals without additional developer’s help.
In this article, you’ll learn:
  * What Page Builder sections are and how they define a page’s layout, both vertically and horizontally.
  * How to define the correct section properties that help editors in website content creation.
  * About different options you can use to balance editing experience and section’s designing flexibility.


## Sections in Xperience by Kentico
Rather than coming with a prebuilt set of sections that will lock editors into a one-size-fits-all approach, Xperience comes with tools developers can use to tailor the editing experience. Why? Each business has slightly different requirements, and each editor team comes to Xperience with various skill sets and expectations.
We don’t want to prescribe every editor has to see or use this or that in the administration interface. We think that **business requirements play a pivotal role** in what types of sections and their associated properties your developers prepare. When you tailor sections during content modeling around specific business needs, you’ll deliver the right components that help the editor team execute their marketing strategy while aligning with the brand’s identity.
## How do editors use sections?
Watch a short video that explains how sections in Xperience work.
”
## Use sections to define the page’s layout and widgets to display content
Widgets allow editors to input content into various sections when designing web pages. That’s why the content personalization feature is only available for widgets.
### Enhance widgets with layout and display capabilities
If editors aim to create personalized experiences that resonate with their audience, they should focus on adapting individual elements within those sections. They can adjust section layouts, hide specific content, or fine-tune colors and themes. To achieve this level of customization, widgets with design capabilities become indispensable. These widgets act as building blocks, enabling editors to tailor content presentation based on the customer journey.
### See the Hero banner widget Example
Consider the **Hero banner widget** featured in the Kbank demo site. This versatile widget offers several customization options:
  * **Displaying/Hiding product benefits** : Editors can selectively showcase or conceal specific benefits relevant to their content. The Hero banner adapts dynamically, whether it’s highlighting financial services, special offers, or unique features.
  * **Hiding/Displaying product image** : Sometimes, a powerful image can convey more than words. With the Hero banner widget, editors can include or exclude product visuals, aligning with their storytelling strategy.
  * **Changing the whole color themes** : The Hero banner’s color theme is adjustable. Editors can harmonize it with the overall page design or create visual contrast to draw attention. The widget is flexible, so editors can easily add a vibrant call-to-action or a calming background.


Find out more about the editing options in [a guide about reusing content in the Hero banner widget](/guides/digital-marketing/work-with-reusable-content/reuse-content-with-the-hero-widget).
In summary, widgets empower editors to fine-tune content elements within sections, ensuring a personalized and engaging user experience.
[![Content personalization in the Kbank demo site](/docsassets/guides/model-page-builder-sections/personalization-options-widget.png)](/docsassets/guides/model-page-builder-sections/personalization-options-widget.png)
## Use section properties to empower editors
Page Builder sections should have properties that editors will use to define the sections with widget zones, for example, their background and the number of columns where editors input widgets or adjust whitespace around individual content pieces.
### Enable customization through layout and design options
Editors use _Page Builder_ sections to craft vertical and horizontal layout web pages. To help them succeed, these sections should offer various layout options.
When editors can choose from different column configurations (such as single-column, two-column, or three-column layouts), they can tailor the page structure to their specific content needs. Whether it’s a sleek product showcase or an informative blog post, having layout flexibility ensures that editors can create visually appealing and functional pages.
### Make editors self-sufficient and efficient in content creation
Empowering editors means making them self-sufficient in their content creation journey.
When you define the correct section properties, editors can confidently customize their pages without heavy reliance on developers. For instance, they can adjust the section background to set the tone for their content—a vibrant image or a subtle color gradient. Additionally, they will tweak section spacing (using properties like padding and margin) to fine-tune the alignment of elements within each section. And since the editable options come from designers, editors don’t need to worry about breaking the page design or brand messaging. Making editors independent of developers speeds up content production and inspires originality.
### Typical options defined via section properties
Let’s explore some typical section properties that enhance editor empowerment:
  * **Layout Options** : The ability to choose between single-column, multicolumn, or other custom layouts ensures flexibility.
  * **Section Background** : Editors can select an image or a solid color as the backdrop for their content, aligning with branding guidelines.
  * **Section Spacing** : Fine-tuning the padding (to adjust element position within the section) and margin (to control whitespace around the section) allows editors to achieve precise visual balance.
  * **title:** A clear title for each section aids content organization and navigation. persona: architect, developer
  * **Heading Sizes** : Providing default heading sizes and a list of allowed title styles ensures consistency across the page.


In summary, thoughtful section properties empower editors to create compelling content while maintaining design coherence. We foster a collaborative environment where developers and editors contribute to outstanding web experiences by allowing them to customize.
## How many types of sections should developers build?
The number of section types developers should build depends on a delicate balance between team experience and the development’s team preferences .
Imagine your project follows a brand book that allows editors creating various sections on a page. These sections can be structured as one large column, two columns, or three columns. Additionally, the brand book specifies that the sizes of columns within the two-column setup can vary. Now, let’s see how teams’ dynamics influences this decision.
On the other hand, if you deal with a less experienced development team, favor simplicity and granularity.
What does it mean?
Developers will create more sections, each with clear and straightforward behavior. For instance, they might **design five distinct sections** : a _single-column section_ , variations for _two-column layouts_ , and a _three-column section_. This approach simplifies development, making it easier for less seasoned teams to manage and maintain.
[![New section dialog on the Dancing goat site](/docsassets/guides/model-page-builder-sections/section-dialog-dg-sections.png)](/docsassets/guides/model-page-builder-sections/section-dialog-dg-sections.png)
On the other hand, seasoned teams might opt for fewer, more versatile sections, leveraging their expertise to effectively handle complex interactions and layouts.
[![New section dialog on the Kbank demo site](/docsassets/guides/model-page-builder-sections/kbank-sections.png)](/docsassets/guides/model-page-builder-sections/kbank-sections.png)
Editors pick one section and then use complex configuration options.
[![Multicolumn section properties on the Kbank demo site](/docsassets/guides/model-page-builder-sections/section-dialog-kbank-section-properties.png)](/docsassets/guides/model-page-builder-sections/section-dialog-kbank-section-properties.png)
What are the benefits and drawbacks of each approach? 
Several simple sections with limited properties |  Few sections with complex properties  
---|---  
  * Editors pick a section and they are done.
  * Easier for developers to set up widget zone restrictions.
  * With large number sections, the section dialog can become excessive.
  * When changing the design, editors need to change the section type instead of adjusting a section property.

| 
  * Editors don't have to think in advance; just pick from one option.
  * They are harder to develop and maintain to ensure consistency.
  * Developers have to set zone restrictions properly.
  * Editors find them more challenging to predict content changes on section change.

  
Developers, simplify your work with a prebuilt set of configurable [templates, sections, and widget properties](/guides/development/page-builder).
Which option for the presentation model is the best?
As with other things in app development, it depends. But you will get all the benefits when you balance simplicity and flexibility and align your proposals with the team’s capabilities and the project’s requirements.
## Add context to help marketers see what they are doing
As marketers navigate their tasks, provide them with clear context and actionable steps. Outline what to do next in the guidelines and set the stage for effective content creation. Adding editorial instructions to sections ensures they can confidently configure the page design to execute their vision. Your guidelines are not there to set limitations; they act as creative boundaries, allowing marketers to explore within defined parameters while maintaining consistency and alignment with the overall design and brand strategy.
[![Sample section with properties similar to section properties on kentico.com](/docsassets/guides/model-page-builder-sections/kentico-sample-section.png)](/docsassets/guides/model-page-builder-sections/kentico-sample-section.png)
### Name colors and themes properly
When naming colors and themes within a Page Builder, it’s crucial to strike the right balance between **editor freedom** and **responsibility**. While it’s great to grant editors all the flexibility to choose any color they want, it is a double-edges sword. In many cases, you don’t want editors to be too creative, as their websites must stay consistent with company brand brand book.
First and foremost, avoid allowing direct insertion of color codes (such as #f05a22 or rgba(240, 90, 34, 0.5)). Instead, encourage descriptive names that resonate with the marketing team’s understanding. For instance, terms like _“Gray (Light)”_ or _“Violet”_ provide more context than raw hex codes.
[![Demo of a two column sample section with editor guidance](/docsassets/guides/model-page-builder-sections/two-column-sample-section.png)](/docsassets/guides/model-page-builder-sections/two-column-sample-section.png)
However, let’s take this recommendation a step further.
### Future-proof design with meaningful names
To future-proof your Page Builder options, consider using terms like _primary_ , _secondary_ , or _form section_ colors. You can also introduce brand-related naming, like _Kentico vibrant_ or _Ultramarine blue_ or semantically driven names, such as _success color_ or _alert color_.
These personalized labels transcend specific shades and tie directly to the brand’s identity. Adopt meaningful names and ensure that the transition remains seamless even if the company decides to adjust its brand color palette or messaging. These intuitive names are user-friendly and facilitate code maintenance—making them easy to swap out when needed. In summary, thoughtful naming practices enhance usability and adaptability in the dynamic world of web design.
## Balance independence and consistency
Developers face an inherent challenge when they want to ensure consistent styling between sections and widgets. Why? By design, **Xperience doesn’t dynamically check how sections are configured, what widgets are input into those sections, and how these widgets are configured.**
To address this, consider the experience level of your editor teams. **Highly skilled teams** can confidently add content while adhering to design guidelines. They understand the nuances of layout, color schemes, and typography. However, providing proper instructions becomes critical for **less experienced teams**. This could be exhaustive documentation or detailed guidelines for page components.
Striking the right balance between independence and consistency is essential. What can you do?
One approach is to **limit editor freedom** by defining strict rules and boundaries. This ensures uniformity but may stifle creativity. Alternatively, give editors the **right tools and guidance**. Provide them with well-documented instructions, intuitive page components, and clear naming conventions. By doing so, you promote a collaborative environment where creativity thrives within the context of brand consistency. Ultimately, the goal is to strike a harmonious balance—where skilled editors flourish, and less experienced ones feel supported.
[![Example of Demo section with detailed instructions](/docsassets/guides/model-page-builder-sections/demo-sample-section.png)](/docsassets/guides/model-page-builder-sections/demo-sample-section.png)
If you don’t want editors to combine the section’s design options with options provided through widgets, simply introduce a “Lock section theme” property. The section design, such as colors or layout, will stay consistent no matter what widgets and which configurations editors introduce.
[ Previous page ](/modules/model-website-presentation-components/ensure-page-components-support-content-personalization)
6 of 11
[ Mark complete and continue ](/modules/model-website-presentation-components/what-are-page-builder-widgets)
![]()
[]()[]()
