# Plan your content model
  * [ Copy page link ](modules/xperience-implementation-handbook/plan-your-content-model#) | [Get HelpService ID](modules/xperience-implementation-handbook/plan-your-content-model#)
Core MVC 5


[✖](modules/xperience-implementation-handbook/plan-your-content-model# "Close page link panel") [Copy to clipboard](modules/xperience-implementation-handbook/plan-your-content-model#)
This guide talks about **content modeling** and its role in the planning phase of your project. For the full picture, we highly recommend our [Content modeling guide](guides/architecture/content-modeling/content-modeling-guide).
Reading the _Content modeling guide_ is not a prerequisite to understanding the high-level overview on this page. But for your convenience, we’ve included links in several places to elaborate on the concepts discussed.
We strongly recommend reading the _Content modeling guide_ **in full** before building your content model.
## Identify data structures
As a solid foundation for development, every project needs well-designed information architecture.
### Data design process
  * Define data structure (e.g., fields, field types, etc.) for each data object (e.g., news, products).
  * Define relationships between your data objects (e.g., related articles).
  * Identify functional requirements of data objects (e.g., workflow, versioning, security).
  * Identify how to best utilize the Xperience by Kentico tools (e.g., content types, reusable field schemas, taxonomies).


Before continuing with this guide, we recommend you read at least [the first page](guides/architecture/content-modeling/content-modeling-guide) of the _Content modeling guide_. It defines the content modeling terminology, and explains its importance and impact.
### Content type, reusable content, and structured vs. unstructured content
One of the most important building blocks of content modeling in Xperience by Kentico is **content type**.
Read about it in the [Begin with business needs](guides/architecture/content-modeling/content-modeling-guide/content-modeling-business-requirements) page of our _Content modeling guide_. The page discusses the importance of **reusable content** and explores differences between [structured and unstructured content](guides/architecture/content-modeling/content-modeling-guide/structured-and-unstructured-content#StructuredVsUnstructuredContent).
### Storing data in Kentico
Xperience by Kentico allows you to store data in:
  * [Content hub](documentation/business-users/content-hub)
  * Custom module classes
  * [Page Builder pages](guides/architecture/content-modeling/xperience-content-modeling-basics/differences-between-structured-content-and-page-builder-content#page-builder-content)


Read the [Store content](guides/architecture/content-modeling/content-modeling-guide/store-content) page of the _Content modeling guide_ to learn about where, when, and how you should store different types of data.
## Map the requirements
Choosing the best features to meet your requirements will greatly improve your project’s chances of success.
### Get familiar with built-in functionality
To select the right Xperience functionality for any given requirement, you need to be familiar with what Xperience by Kentico offers. As with anything, you will become more familiar with the features and how to use them over time, but where should you start? Thankfully, there are many resources available to you. We recommend starting with the following:
  * Go through the [Xperience by Kentico training guides](guides/).
  * Read the [Xperience by Kentico documentation](documentation/).
  * Search the [Kentico Community Portal](https://community.kentico.com/) or [Kentico Stack Overflow](https://stackoverflow.com/questions/tagged/kentico).
  * Watch videos on the [Kentico YouTube channel](https://www.youtube.com/Kentico/).
  * Contact [Kentico support](https://www.kentico.com/services/support) for general questions or problems regarding the core platform.
  * Work with [Kentico consulting](https://www.kentico.com/services/consulting) to get hands-on assistance with your implementation.


### Evaluate built-in functionality
Once you’ve selected the feature that best suits your purpose, you should evaluate it further with a proof of concept. This will help you understand whether you can achieve all your functional requirements.
If you find that the selected feature meets most but not all of your functional requirements, consider whether you can extend the feature to meet all requirements.
### Extend built-in functionality
Before extending Xperience by Kentico’s built-in functionality you need to take the following steps:
  1. Make sure that you’ve checked the [resources mentioned earlier](modules/xperience-implementation-handbook/plan-your-content-model#get-familiar-with-built-in-functionality) for other features that might accomplish what you need to do. This way, you can avoid “reinventing the wheel” and solving an already-solved problem.
  2. Research the available extensibility options of built-in functionality, being conscious of [customization best practices](documentation/developers-and-admins/customization/stable-customization-guidelines) in the Xperience by Kentico documentation.
  3. Compare extending Xperience by Kentico functionality versus building something completely custom. During the comparison, consider the viability of one versus the other with regard to:
     * The effort (in time and cost)
     * The usability from the business’s end user’s perspective
     * The technical complexity


### Consider utilizing existing integrations
Before moving onto building a custom functionality to accommodate your project’s requirements, take a look at the list of existing [Xperience by Kentico integrations](https://github.com/search?q=topic%3Axperience-by-kentico-integrations+org%3AKentico&type=Repositories) that we have built in collaboration with our partners.
### Build custom functionality
If you find that there is no suitable Xperience by Kentico feature or existing integration that meets your needs, and you determine that extending Kentico is not a viable option, do the following:
  * Architect the solution with low maintenance in mind by using as much built-in functionality as possible.
  * Design the solution to be extensible and scalable for the future.
  * Investigate how other built-in functionality influences the solution.


## Plan content management
Create a strategy for the production, organization, and management of both content and assets. Make sure to consider the initial work at the beginning of the project, as well as future additions and maintenance.
### Content production
#### Creating and managing content inside Xperience by Kentico
If you manage content in your solution this way, the developers must prepare the data structures and storage before the editors can enter the content.
In live projects, we recommend creating content in a separate staging environment rather than directly editing in production. You can synchronize data to the production environment using Continuous Deployment.
#### Creating and managing content outside of Xperience by Kentico
When you need to synchronize data between Xperience and an external system continuously, we recommend using custom [scheduled tasks](documentation/developers-and-admins/customization/scheduled-tasks).
If the built-in scheduled tasks do not meet your requirements, you can look into external services like [Hangfire](https://www.hangfire.io/) or [Quartz.NET](https://www.quartz-scheduler.net/).
Instead of pulling data directly from the external system at runtime, we recommend storing it in your Xperience instance and working with it there if possible. If managing data in Xperience isn’t feasible, clearly define your “source of truth” (e.g., your ERP or another external system) and the process for content editors to manage each data type.
For a one-way data migration from an external system, you can use our [Xperience by Kentico: Universal Migration Tool](https://github.com/Kentico/xperience-by-kentico-universal-migration-tool/tree/main).
If you plan to migrate from an older Kentico product using the [Kentico Migration Tool](https://github.com/Kentico/xperience-by-kentico-kentico-migration-tool), take a look at our [migration strategy guide](guides/architecture/upgrade-from-kx13/plan-your-strategy-for-migrating-features) for information about important considerations.
### Design, organize, and share content
In Xperience by Kentico, you can present content to visitors using multiple channels: websites, emails, or headless.
Read the [Design content](guides/architecture/content-modeling/content-modeling-guide/design-content) page of the _Content modeling guide_ to learn how to build, manage, share, and secure your content.
The [General content modeling recommendations](guides/architecture/content-modeling/content-modeling-guide/general-content-modeling-recommendations) page that follows explores considerations for planning several features with your team, including digital marketing, routing, URL and navigation, and website system pages (e.g., search results, consent settings, and SEO-specific pages).
[ Previous page ](modules/xperience-implementation-handbook/examine-xperience-capabilities)
4 of 9
[ Mark complete and continue ](modules/xperience-implementation-handbook/develop-your-project)
  * [Community Questions & Answers](https://community.kentico.com/q-and-a)
  * [Contact support](https://community.kentico.com/support)


### Cookie consent
We use necessary [cookies](https://www.kentico.com/cookies-policy) to run our website and improve your experience while browsing. Additional cookies are only used with your consent. You may revoke your consent on the [Cookies Policy](https://www.kentico.com/cookies-policy) page or in your browser at any time. 
ACCEPT ALL  [Configure](https://www.kentico.com/cookies-policy)
USE ONLY NECESSARY 
![](https://docs.kentico.com/modules/xperience-implementation-handbook/plan-your-content-model)
[](https://docs.kentico.com/modules/xperience-implementation-handbook/plan-your-content-model)[](https://docs.kentico.com/modules/xperience-implementation-handbook/plan-your-content-model)