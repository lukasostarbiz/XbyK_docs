# Examine the DXP capabilities of Xperience
  * Concept| [ Copy page link ](guides/architecture/xperience-implementation-handbook/examine-xperience-capabilities#) | [Get HelpService ID](guides/architecture/xperience-implementation-handbook/examine-xperience-capabilities#) | This page is part of a module: [ Xperience implementation handbook ](modules/xperience-implementation-handbook)
Core MVC 5


[✖](guides/architecture/xperience-implementation-handbook/examine-xperience-capabilities# "Close page link panel") [Copy to clipboard](guides/architecture/xperience-implementation-handbook/examine-xperience-capabilities#)
During the development process, it can be easy to forget that websites, applications, and the platforms they run on are _marketing tools_ , intended to help businesses achieve goals like increased sales, brand recognition, or engagement. Teams can be so focused on the development tasks necessary to get a solution running that they lose sight of _the problems it should solve_ and the _objectives it should achieve_.
It may be tempting to dismiss the term **Digital experience platform (DXP)** as a buzzword for a CMS with some marketing features tacked on. Still, one’s online interactions with an organization don’t exist in a vacuum; all those experiences come together to form a customer’s perception of the organization, which _influences whether their actions align with the organization’s objectives_.
With this in mind, it makes sense to integrate the many aspects of a customer’s interactions with a business as seamlessly as possible through a central platform, a **DXP**.
**Understanding the tools** offered by such a platform is essential for creating digital experiences that persuade customers toward your strategic objectives. To that end, let’s examine **Xperience by Kentico’s features** , how they interact, and the terminology surrounding them.
## Marketing
### Contact management
To engage visitors, you need to save information about them. Saving a profile helps you to craft a personalized digital experience according to each visitor’s specific needs.
[Contact management](documentation/business-users/digital-marketing/contact-management) saves a contact profile with details about individuals, which you can use to track [activities](guides/architecture/xperience-implementation-handbook/examine-xperience-capabilities#activities) and segment them into [groups](guides/architecture/xperience-implementation-handbook/examine-xperience-capabilities#contact-groups).
[![Contact profile screenshot](docsassets/guides/examine-xperience-capabilities/ContactProfile.png)](https://docs.kentico.com/docsassets/guides/examine-xperience-capabilities/ContactProfile.png)
### Contact groups
[Contact groups](documentation/business-users/digital-marketing/contact-groups) are segments for organizing contacts based on characteristics and [activities](guides/architecture/xperience-implementation-handbook/examine-xperience-capabilities#activities).
You can target contact groups with more strategic marketing materials to tailor their digital experience, for example, by [personalizing](guides/architecture/xperience-implementation-handbook/examine-xperience-capabilities#content-personalization) page content based on contact group.
### Customer journeys
[Customer journeys](documentation/business-users/digital-marketing/customer-journeys) offer a unified view of a contact’s experience with an organization’s content. They allow marketers to [define steps](guides/customer-journeys) through which contacts progress on their way to a conversion, and the conditions a contact must meet to move between steps.
The data gathered from customers helps your client or organization to visualize and identify weak points in their marketing processes. By pinpointing where customers are lost, marketers can address shortcomings in the digital experience and implement fixes that increase conversions.
[![Customer journey screenshot](docsassets/guides/examine-xperience-capabilities/CJ_Journey.png)](https://docs.kentico.com/docsassets/guides/examine-xperience-capabilities/CJ_Journey.png)
### Activities
[Activities](documentation/developers-and-admins/digital-marketing-setup/set-up-activities) represent actions that users take throughout their digital experience, such as visiting pages on a website channel or submitting forms.
Apart from the default actions, you can use [custom activities](documentation/developers-and-admins/digital-marketing-setup/set-up-activities/custom-activities) to represent actions or circumstances specific to your organization or client.
Activities can trigger [Automation processes](guides/architecture/xperience-implementation-handbook/examine-xperience-capabilities#automation) and segment contacts into [groups](guides/architecture/xperience-implementation-handbook/examine-xperience-capabilities#contact-groups).
### Content personalization
[Content personalization](documentation/developers-and-admins/digital-marketing-setup/content-personalization) displays different content to different visitors depending on [personalization condition types](documentation/developers-and-admins/digital-marketing-setup/content-personalization/develop-personalization-condition-types).
Personalization is integrated into [Page Builder widgets](guides/architecture/xperience-implementation-handbook/examine-xperience-capabilities#page-builder), enabling your marketers to configure different variants of each widget based on conditions.
Your developers can also integrate personalization conditions directly into code, for example, fetching different content or calling different business logic based on personalization conditions.
Your browser does not support the video tag. 
### Automation
With [Automation](documentation/business-users/digital-marketing/automation), business users can create processes that dynamically interact with contacts, creating a much more holistic digital experience.
These processes can send emails, log custom [activities](guides/architecture/xperience-implementation-handbook/examine-xperience-capabilities#activities), adjust contact field values, and control the flow of actions with conditional splits and wait steps.
[![Automation process statistics screenshot](docsassets/guides/examine-xperience-capabilities/Statistics.png)](https://docs.kentico.com/docsassets/guides/examine-xperience-capabilities/Statistics.png)
### Email channels
[Email channels](documentation/business-users/digital-marketing/emails) are avenues for email communication. Each one corresponds to a brand or content strategy within an organization, enabling you to target only the recipients who find the content relevant.
Among several [email purposes](documentation/business-users/digital-marketing/emails#email-purposes), you can [send regular emails](documentation/business-users/digital-marketing/emails/send-regular-emails-to-subscribers) to subscribers, allowing for recurring communication in their digital experience.
### Data protection
Certain regions across the world have created regulations protecting their citizens’ right to privacy and personal data, for example, the European Union’s [GDPR](https://gdpr-info.eu/).
Xperience’s [Data protection](documentation/developers-and-admins/data-protection) features help you ensure that any personal data storage and tracking involved in your customers’ digital experiences comply with such regulations. Xperience includes consent management and flexible connectors where you can plug in your data collection, portability, and erasure logic.
### Members
[Membership](documentation/business-users/members) allows visitors to create accounts and sign in to your web channels.
This way, they can access things like exclusive content and configurable preferences while providing additional tracking data to enhance their digital experience.
Well-executed membership incentivizing members to sign in improves their opinion and investment in your organization or client.
## Content modelling and creation
### Content tree-based (CTB) routing
Xperience recommends using the [CTB router](documentation/developers-and-admins/development/routing/content-tree-based-routing) to control the default URLs of pages in your web site channel. This way, an organization can easily react and adjust the structure of their site to facilitate a smooth visitor experience without developer intervention. Custom routes will still work for exceptional cases, but the router will automatically provide a baseline of coherent URLs.
Marketers can also define [vanity URLs](documentation/business-users/website-content/manage-page-urls#edit-vanity-urls-of-pages) for pages, allowing a custom URL that is not based on tree structure without the need of custom routes or developer help.
### Content hub
The [Content hub](documentation/business-users/content-hub) is a place to store content that you need to reference from other channels, such as websites and mobile apps, along with media assets.
Content hub items help modernize your content model and avoid clutter while maintaining a consistent digital experience across multiple channels. You can manage their approval with [workflows](guides/architecture/xperience-implementation-handbook/examine-xperience-capabilities#workflows) to manage their approval and dynamically organize them with [smart folders](documentation/business-users/content-hub/content-hub-folders#smart-folders).
[![Content hub screenshot](docsassets/guides/examine-xperience-capabilities/CH_overview.png)](https://docs.kentico.com/docsassets/guides/examine-xperience-capabilities/CH_overview.png)
### Workflows
With the key role that content plays in the digital experience, it’s crucial to involve multiple people in its publishing.
Using [workflows](documentation/developers-and-admins/configuration/workflows), you can establish approval pipelines, making sure content does not go live without confirmation from the appropriate parties.
### Languages
Most organizations want their digital presence to reach people from multiple linguistic backgrounds.
Xperience by Kentico offers [multilingual support](documentation/developers-and-admins/configuration/languages) for content, with language variants of content items and assets to reach a multicultural audience.
### Reusable field schemas
[Reusable field schemas](documentation/developers-and-admins/development/content-types/reusable-field-schemas) are groups of fields that you can assign to multiple content types, making them easier to update and easier to work with in code.
They allow for consistency among the structured data in your project, leading to a more cohesive and consistent digital experience for visitors while reducing overhead for your content editors.
### Taxonomies
[Taxonomies](documentation/developers-and-admins/configuration/taxonomies) enable you to categorize content according to a hierarchy of tags.
You can then use these tags to filter content, both in front-end applications and for your marketers in the Content hub.
[![Taxonomy screenshot](docsassets/guides/examine-xperience-capabilities/Taxonomy.png)](https://docs.kentico.com/docsassets/guides/examine-xperience-capabilities/Taxonomy.png)
### Page Builder
[Page builder](documentation/developers-and-admins/development/builders/page-builder) empowers content editors to shape digital experiences on a website channel by designing pages.
Once developers create the components, marketers can use a drag-and-drop interface and configurable properties to control the content and visual structure of a page without any coding.
### Form Builder
Forms are one of the primary ways for visitors to provide information to an organization, so they’re an important part of the digital experience.
[Form Builder](documentation/developers-and-admins/development/builders/form-builder) allows marketers to compose online forms in a website channel with a graphical editor. They can utilize _Form sections_ and _Form components_ to control the layout and appearance of the form without help from a developer.
[![Form Builder screenshot](docsassets/guides/examine-xperience-capabilities/FormBuilderUIOverview.png)](https://docs.kentico.com/docsassets/guides/examine-xperience-capabilities/FormBuilderUIOverview.png)
### Email Builder
[Email Builder](documentation/developers-and-admins/development/builders/email-builder) empowers marketers to visually design emails through a drag-and-drop interface with developer-provided components.
This enables them to create a more engaging experience for recipients, with flexible and visually appealing email messages.
### Headless content
With [headless channels](documentation/business-users/headless-content), you can easily extend digital experiences to channels beyond the web, exposing your content to a variety of technologies through a [GraphQL API](documentation/developers-and-admins/development/content-retrieval/retrieve-headless-content).
This facilitates scenarios like mobile apps, digital signage, and IoT applications.
## Configuration and development
### Roles and Permissions
[Roles and permissions](documentation/developers-and-admins/security-guidelines#roles-and-permissions) dictate what different groups of users are able to do in Xperience by Kentico, safeguarding each aspect of the digital experience from accidental or malicious interference. These features allow you to establish a security policy that grants each user access only to what they need.
Permissions are not exclusive to built-in modules; you can also add them to [custom modules](guides/architecture/xperience-implementation-handbook/examine-xperience-capabilities#custom-modules) to restrict the capabilities of roles.
### Custom modules
You may find that your organization or client envisions a digital experience that includes something not covered by Xperience’s out-of-box features.
Xperience offers the ability to [create custom object types](documentation/developers-and-admins/customization/object-types#section) that smoothly integrate with other features, with relationships to system classes, optional inclusion in features like [Continuous Integration (CI) and Continuous Deployment (CD)](guides/architecture/xperience-implementation-handbook/examine-xperience-capabilities#continuous-integration-ci-and-continuous-deployment-cd), and seamless inclusion in the Xperience UI.
Custom modules are an excellent tool for adding new niche functionality or integrating data from external systems.
Your browser does not support the video tag. 
### Global Event handlers
[Global event handlers](documentation/developers-and-admins/customization/handle-global-events) are powerful tools for expanding upon existing functionality in Xperience.
They allow you to execute custom code in reaction to all manner of occurrences in Xperience, such as changes to content items’ workflow state, and [CRUD operations on any object type](documentation/developers-and-admins/customization/handle-global-events/handle-object-events) registered in the system, including custom object types.
Global event handlers are a pivotal tool in digital experiences that require integrations or customization for niche requirements.
### Scheduled tasks
[Scheduled tasks](documentation/developers-and-admins/customization/scheduled-tasks) in Xperience are recurring operations that run at defined intervals.
They are an excellent option for parts of your digital experience that require regular maintenance, such as integrations with external systems and custom logging scenarios.
### Continuous Integration (CI) and Continuous Deployment (CD)
CI and CD are tools that serialize data from the Xperience database for automated data transfer between environments, for example, via source control or deployment pipelines.
These improve development processes and enable your team to deliver new content across environments.
### Caching
Performance optimization improves the ease of a customer’s digital experience, but it’s important to clear a cache item when any of the data it includes is updated. Xperience’s [Data caching](documentation/developers-and-admins/development/caching/data-caching) and [Output caching](documentation/developers-and-admins/development/caching/output-caching) both allow you to set [cache dependencies](documentation/developers-and-admins/development/caching/cache-dependencies) on Xperience objects when they are created, updated, or removed, as well as custom cache keys which you can trigger with custom code at any time.
You can also account for any features that use dynamic data, such as personalization, when you set the names of custom cache entries. This ensures that the cache won’t return the wrong version of data when many factors exist.
## What’s next?
The next guide in this handbook covers the [Planning phase](guides/architecture/xperience-implementation-handbook/plan-your-content-model) of your project. It discusses how to determine your content model and choose the Xperience features that best meet your requirements.