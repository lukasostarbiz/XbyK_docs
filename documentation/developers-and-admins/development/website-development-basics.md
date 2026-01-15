# Website development basics
  * [ Copy page link ](documentation/developers-and-admins/development/website-development-basics#) | [Get HelpService ID](documentation/developers-and-admins/development/website-development-basics#)
Core MVC 5


[✖](documentation/developers-and-admins/development/website-development-basics# "Close page link panel") [Copy to clipboard](documentation/developers-and-admins/development/website-development-basics#)
[Xperience by Kentico](http://xperience.io) is a content and digital experience management platform built with [ASP.NET Core MVC](https://docs.microsoft.com/en-us/aspnet/core/mvc/overview).
This page provides a basic overview of the Xperience environment and the responsibilities of individual roles that interact with the system, with specific focus placed on the role of the site developer.
## Xperience by Kentico overview
Xperience is distributed as a set of NuGet integration packages for ASP.NET Core applications. The system is modular, each package providing functionality that the other packages build upon. The following packages form the core of the system:
  * **Kentico.Xperience.Core** – the runtime assemblies for libraries and applications that use Xperience API. This package contains low-level APIs required by all applications that interact with Xperience.
  * **Kentico.Xperience.WebApp** – builds upon the _Libraries_ package by providing a set of Xperience management APIs specifically designed for use in web applications.
  * **Kentico.Xperience.Admin** – provides the Xperience administration interface that adds a graphical UI for content management, digital marketing, and other features.


See [Xperience by Kentico NuGet packages](documentation/developers-and-admins/development/website-development-basics/configure-new-projects/xperience-by-kentico-nuget-packages) for an overview of all available NuGet integration packages and their roles in the system.
The following diagram summarizes the general roles of the main personas that interact with Xperience sites – Xperience users, developers, and website visitors:
[![Xperience website personas and their roles](docsassets/documentation/website-development-basics/BasicFlow.png)](https://docs.kentico.com/docsassets/documentation/website-development-basics/BasicFlow.png)
There are two main parts to each Xperience project:
  * [Xperience administration](documentation/developers-and-admins/development/website-development-basics#xperience-administration)
  * [ASP.NET Core MVC live site](documentation/developers-and-admins/development/website-development-basics#asp.net-core-mvc-live-site)


### Xperience administration
The Xperience administration is installed into projects via the **Kentico.Xperience.Admin** NuGet package, and provides a graphical interface for the low-level management API from other Xperience packages. The administration exists parallel to the live site and can be safely removed without impacting live site functionality or content (e.g., for production deployments).
Users of the Xperience administration can usually be segmented into the following roles:
  * **Site administrators** – configure and manage the Xperience integration. Control the administration interface by managing user access.
  * **Developers** – implement advanced configuration scenarios and customization. Also partially responsible for defining the site’s [content model](guides/architecture/content-modeling/content-modeling-guide) – the way content is structured impacts how developers work with it when building the site’s business and presentation layers.
  * **Content editors** – create and maintain content managed by the system.
  * **Marketers** – manage the project’s digital presence. Use collected data to launch targeted campaigns and provide personalized content. All with full support for compliance with the European Union’s General Data Protection Regulation (GDPR), if applicable to the project.


### ASP.NET Core MVC live site
The live site is a conventional ASP.NET Core MVC project that consumes and presents content managed by Xperience. 
The personas that primarily interact with the live site are:
  * **Developers** – implement the site’s business and presentation layers using standard MVC practices. In addition, they use the Xperience API to retrieve and cache content, set up analytics, augment the default MVC routing support, and build custom Xperience components that aid administration users in content creation.
  * **Site visitors** – regular users who view and interact with the site. The system can track their journey and behavior using specific digital marketing functionality. The collected data and observed patterns can be used to personalize displayed content on a visitor to visitor basis or send targeted email messages intended to drive engagement.


## Website development process with Xperience
Xperience sites follow a development process similar to other MVC applications. However, Xperience provides specific functionality that simplifies or can completely replace parts of the process.
A general development flow consists of the following steps:
  1. [Install and set up a local Xperience application](documentation/developers-and-admins/development/website-development-basics#install-and-set-up-an-xperience-application).
  2. [Define the project’s content model](documentation/developers-and-admins/development/website-development-basics#define-the-projects-content-model).
  3. [Configure and customize Xperience according to your requirements](documentation/developers-and-admins/development/website-development-basics#configure-and-customize-xperience).
  4. [Implement the application’s business and presentation layers](documentation/developers-and-admins/development/website-development-basics#implement-the-applications-business-and-presentation-layers).
  5. [Prepare the site’s content](documentation/developers-and-admins/development/website-development-basics#prepare-the-sites-content).
  6. [Deploy to a live environment](documentation/developers-and-admins/development/website-development-basics#deploy-to-a-live-environment).


The illustrated process isn’t rigorous, and you will most likely revisit certain steps multiple times as you iterate on the final implementation of the site.
### Install and set up an Xperience application
Before you can begin, you need to [install and set up](documentation/developers-and-admins/installation) a local development instance. Xperience provides project templates and tools that developers can use to quickly bootstrap new projects.
The next step is to [set up local hosting](documentation/developers-and-admins/development/website-development-basics/set-up-local-hosting) for the project. Xperience is built using .NET, which is natively cross-platform, so this largely depends on the preferred working environment of your team. To work over a shared database during development, you can use the provided [Continuous Integration](documentation/developers-and-admins/ci-cd/continuous-integration) feature.
### Define the project’s content model
A [content model](https://alistapart.com/article/content-modelling-a-master-skill/) documents all the content that an organization produces. Defining the content structure and establishing relationships between the different content types is essential for building a successful project.
Xperience provides content modeling tools for website channels primarily in the form of:
  * **Content types** – the primary method of storing structured content in Xperience is using pages. Each page is based on a blueprint – a [content type](documentation/developers-and-admins/development/content-types) that defines its basic structure. For example, every article created from an  _Article_ content type has the same properties.
  * **Page Builder** – a graphical interface that allows content editors to design sections or entire pages using a WYSIWYG approach. [Page Builder](documentation/business-users/website-content/widgets-and-page-builder) uses elements called sections to take over designated parts of a page’s layout and enables editors to modify them in real-time using modular pieces of content called widgets (often prepared to fit specific projects).
  * **Content hub** – [Content hub](documentation/business-users/content-hub/content-item-assets) provides a centralized repository for [assets managed by Xperience](documentation/business-users/content-hub/content-item-assets). Editors can upload, manage, or update the files and their properties, such as their name or description, using the [administration interface](documentation/administration-interface-basics). Decisions about storing general project-specific physical files (e.g., CSS, JS) are best made on a case-by-case basis for each implementation.


See [Content modeling](guides/architecture/content-modeling/content-modeling-guide) for more information on content modeling or [Design website content](guides/architecture/content-modeling/content-modeling-guide/design-website-content) for website-specific information.
### Configure and customize Xperience
Both Xperience and the Xperience administration interface offer high customizability and extensibility. Regular users and site administrators can achieve basic configuration tasks via settings available directly in the administration. 
For more advanced scenarios, developers directly modify [options objects](https://docs.microsoft.com/en-us/aspnet/core/fundamentals/configuration/options) related to the specific features, such as when configuring the system’s [mailout functionality](documentation/developers-and-admins/configuration/email-configuration) or [user management](documentation/developers-and-admins/configuration/users/administration-registration-and-authentication/administration-forms-authentication) for the administration. For other configuration scenarios, see the [Configuration](documentation/developers-and-admins/configuration) section of the documentation.
If certain default functionality doesn’t sufficiently cover a project’s requirements, developers can adjust the system’s behavior or implement additional features using:
  * **Event handling** – the system raises [events](documentation/developers-and-admins/customization/handle-global-events) in response to page operations, live site user form submissions, basic CRUD operations over managed objects, and many others.
  * **Custom components for content editing** – custom [Page Builder widgets](documentation/developers-and-admins/development/builders/page-builder) might be necessary for certain types of modular content. Additionally, to provide a more comfortable content editing experience, [widgets](documentation/developers-and-admins/development/builders/page-builder/widgets-for-page-builder) and [other types of dialogs](documentation/developers-and-admins/customization/field-editor) for inserting content support _input validation_ and the ability to hide specific components using _visibility conditions_. While the system provides a default set of components for validation and visibility, it might not cover all use cases sufficiently.
  * **Custom components for the live site** – in Xperience, forms for gathering visitor submissions are managed using [Form Builder](documentation/business-users/digital-marketing/forms/create-and-edit-forms), which uses _form components_ to provide an editing interface for individual form input fields.
  * **Service decoration** – developers can extend the system by [decorating](documentation/developers-and-admins/customization/decorate-system-services) the default behavior of system services and management classes.
  * **Custom administration functionality** – developers can also create custom pages and applications in the [administration interface](documentation/developers-and-admins/customization/extend-the-administration-interface) for advanced scenarios.


For more information about the available options, see the [Configuration](documentation/developers-and-admins/configuration) and [Customization](documentation/developers-and-admins/customization) chapters in the documentation.
### Implement the application’s business and presentation layers
In this part of the process, developers work on the live site application. The development closely mimics that of any other MVC website – developers define views, models, controllers, and routing according to project requirements. However, they can also use specific Xperience features to simplify the development process.
  * **Content management APIs** – Xperience provides a suite of content retrieval APIs for [page data](documentation/developers-and-admins/development/content-retrieval/retrieve-page-content), [media files](documentation/developers-and-admins/development/content-retrieval/retrieve-content-from-media-libraries), and [general objects in the system](documentation/developers-and-admins/api/objectquery-api).
  * **Registration and authentication** – enable visitors to [register](documentation/developers-and-admins/development/registration-and-authentication) and provide authenticated users access to secured content.
  * **Caching support** – available caching APIs support standard memory caching even in distributed ([scaled-out](documentation/developers-and-admins/configuration/auto-scaling-support)) environments.
  * **Content tree-based routing** – the system can [take over MVC routing](documentation/developers-and-admins/development/routing/content-tree-based-routing) for pages managed in the project’s [content tree](documentation/business-users/website-content). For these cases, developers only need to provide a corresponding view file that the system uses to display the page. Alternatively, if more direct control over the view model is necessary, developers can designate custom controller actions that the system uses to display specified pages. The developers then have full control over the implementation (including the model data). For content not directly managed by Xperience, conventional ASP.NET routing applies.
  * **GDPR support** – if compliance with GDPR is necessary, the system offers a suite of features for personal data management ranging from basic [management of consents](documentation/developers-and-admins/data-protection/consent-management) to support for [personal data collection](documentation/developers-and-admins/data-protection/personal-data-collection) (to satisfy the _right to access_) and [data erasure](documentation/developers-and-admins/data-protection/personal-data-erasure) (to satisfy the _right to be forgotten_).
  * **Tag Helpers** – a set of [custom ASP.NET Core Tag Helpers](documentation/developers-and-admins/development/reference-tag-helpers) that simplifies the inclusion of specific system API into views.


For more information about the available APIs and features, see the [Development](documentation/developers-and-admins/development) section of the documentation.
### Prepare the site’s content
In parallel with the site’s development, editors prepare a content baseline required by the site at launch. They can use the content model defined in the previous steps – directly create pages and upload the necessary media files – or use an external application and import the work later.
### Deploy to a live environment
When everything is finished and the project is ready to go live, the last remaining step is deployment. Xperience is built using the .NET framework, which is natively cross-platform. You can deploy to either [the SaaS environment](documentation/developers-and-admins/deployment/deploy-to-the-saas-environment), where your project is managed by Kentico, or [manage a deployment yourself](documentation/developers-and-admins/deployment/deploy-to-private-cloud).
In more complex deployment scenarios, production websites can be [published without the Xperience administration application](documentation/developers-and-admins/deployment/deploy-to-private-cloud/deploy-without-the-administration), reducing security risks. In such cases, the project is managed from a separate instance deployed on a private network or behind a firewall with both applications connected to the same database, which is possible due to the system’s [auto-scaling support](documentation/developers-and-admins/configuration/auto-scaling-support).
For other deployment scenarios, see the individual chapters in the [Deployment](documentation/developers-and-admins/deployment) section of the documentation.