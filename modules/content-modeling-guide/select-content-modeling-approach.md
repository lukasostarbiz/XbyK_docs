# Select your content modeling approach
  * [ Copy page link ](modules/content-modeling-guide/select-content-modeling-approach#) | [Get HelpService ID](modules/content-modeling-guide/select-content-modeling-approach#)
Core MVC 5


[✖](modules/content-modeling-guide/select-content-modeling-approach# "Close page link panel") [Copy to clipboard](modules/content-modeling-guide/select-content-modeling-approach#)
You’ve learned about different ways to store _structured content_ in Xperience. From a global perspective, the approach you choose depends on the project needs, editor teams, and content delivery strategy.
You can store your data in a **reusable content** format in the Content hub for universal delivery, including a headless or hybrid-headless experience, and follow the pattern that we call an **atomic content model** , or create a **page-based model** that works great for web-centric, visually driven projects.
The **atomic content model** focuses on creating a [highly composable, omnichannel experience](modules/content-modeling-guide/design-atomic-content-model). Editors store **reusable content** in the _Content hub_ and keep this content channel-agnostic. To display this content in the presentation channel, such as a website, app, or campaign microsite, editors will use channel-specific “wrappers” around the _reusable content_ and add _channel-specific content_ , for example, SEO metadata in website channels. This approach works best for solutions that require scalable content reuse across many channels while maintaining a single source of truth.
The **page-based model** allows you to manage [structured and unstructured content](modules/content-modeling-guide/structured-and-unstructured-content) within _webpage content types_ for _web-centric_ , [page-driven content flows](modules/content-modeling-guide/design-page-based-content-model). The editing UI for structured content in the [website page](guides/development/developer-kickstart/define-a-page-content-type) is similar to _Content hub_ , and editors can reuse content input through this interface in other website pages (on the same channel or cross website channel), for example, for listing pages, or in emails. The unstructured data is managed through the [Page Builder](documentation/developers-and-admins/development/builders/page-builder). In _Page Builder_ components, editors can refer to other reusable, structured content and display it. Or, they can store the content inside the _Page Builder_ page or area. This makes the content “owned by the page” and very hard to reuse.
Using _Page Builder_ as a primary way to store data is effective for:
  * Projects where content is used primarily on one website and in one place on this website.
  * Teams that require a user-friendly, WYSIWYG editing interface while maintaining semantic structure for SEO and AI readiness.


We’ll discuss both approaches later in dedicated material.
#### Compare the content modeling options
The following high-level comparison helps you decide which approach aligns with your project goals and highlights the most important trade-offs.
The _Content hub_ fits best for content reuse, omnichannel consistency, and headless delivery. At the same time, _page-based models_ align with visual-first workflows.
_Page-based structured content_ aligns with design-driven editorial workflows, enabling structured content while maintaining flexibility for visual layout. In contrast, the _Content hub_ enables consistent content reuse across websites, apps, and campaigns with centralized management.
The editing experience differs. _Page-based model_ supports primarily visual, page-driven editing while the _Content hub_ focuses on structured, presentation-agnostic, reusable data management.
Xperience also supports **a combination of these approaches** , allowing you to use _Page Builder_ widgets to display data stored in a _structured format_ from the _Content hub_ within page layouts. However, Xperience does not support _inline editing_ of the _structured data_ within the _Page Builder_ interface.
Both approaches can support strong SEO and AI search readiness if you correctly structure the individual content types.
Let’s make a short overview of both approaches:
Aspect |  Atomic content model |  Page-based content model  
---|---|---  
Best for |  Multi-channel, headless, and scalable reuse |  Web-centric projects  
Structure |  Channel-agnostic, reusable |  Structured within the page hierarchy  
Editing experience |  Content-centric |  Visual, page-driven  
SEO and AI |  Strong with consistent reusable data, displayed in semantic HTML structures |  Editors need to focus on crafting semantic HTML structures  
[ Previous page ](modules/content-modeling-guide/work-with-semantic-content-types)
12 of 28
[ Mark complete and continue ](modules/content-modeling-guide/store-content-in-reusable-schemas)
  * [Community Questions & Answers](https://community.kentico.com/q-and-a)
  * [Contact support](https://community.kentico.com/support)


### Cookie consent
We use necessary [cookies](https://www.kentico.com/cookies-policy) to run our website and improve your experience while browsing. Additional cookies are only used with your consent. You may revoke your consent on the [Cookies Policy](https://www.kentico.com/cookies-policy) page or in your browser at any time. 
ACCEPT ALL  [Configure](https://www.kentico.com/cookies-policy)
USE ONLY NECESSARY 
![](https://docs.kentico.com/modules/content-modeling-guide/select-content-modeling-approach)
[](https://docs.kentico.com/modules/content-modeling-guide/select-content-modeling-approach)[](https://docs.kentico.com/modules/content-modeling-guide/select-content-modeling-approach)