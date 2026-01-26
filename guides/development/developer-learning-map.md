---
source: https://docs.kentico.com/guides/development/developer-learning-map
scrape_date: 2026-01-26
---

  * [Home](/guides)
  * [Development](/guides/development)
  * Developer learning map 


# Developer learning map
Welcome to the Developer Learning Map for Xperience by Kentico. This curated collection of training materials, documentation articles, and additional resources is structured to guide you through the sequential learning process tailored for developers.
We have put together this collection for developers working with Kentico 12 or Kentico 13 and those new to the Kentico community. These guides are organized in a recommended order to help you navigate different materials. If you are new to certain aspects of .NET framework or specific parts of Xperience by Kentico development, you will find links to resources that will prepare you before you dive into more detailed Kentico-specific information.
Are you a visual learner? You can find the same resources organized [into a roadmap](https://roadmap.sh/r?id=66bdc03491320df4bddcabc8).
## 1. Xperience development basics
You’ll learn the basics of application and development setup, how to leverage CI/CD processes to enhance solutions’ scalability and robustness, and how the channels work. Xperience by Kentico does not use Entity Framework but its own API layer for efficient content handling.
**Development basics prerequisites**
Before you start with Xperience development basics, ensure that you understand the basics of .NET 8 and develop applications with the MVC pattern. If you are new to the .NET framework, you might find the following resources helpful. (Kentico did not create these resources, and payment might be required to access them.)
  * [Get started with ASP.NET Core MVC](https://learn.microsoft.com/en-us/aspnet/core/tutorials/first-mvc-app/start-mvc?view=aspnetcore-8.0&tabs=visual-studio), as the MVC is the recommended development pattern.
  * [Host and deploy ASP.NET Core](https://learn.microsoft.com/en-us/aspnet/core/host-and-deploy/?view=aspnetcore-8.0) – Learn the basics of deploying ASP.NET Core applications in Microsoft documentation.
  * [Deploy an ASP.NET web app](https://learn.microsoft.com/en-us/azure/app-service/quickstart-dotnetcore?tabs=net80&pivots=development-environment-vs) – Go through a Microsoft tutorial that walks you through deploying a web application into Azure Web Apps.
  * [Pluralsight: ASP.NET Core tutorial](https://www.pluralsight.com/courses/aspnet-core-fundamentals) – Learn all the features you’ll need to build ASP.NET Core applications.
  * [.NET Developer roadmap in 2024](https://www.youtube.com/watch?v=1oeMTz7LwrU) – If you are new to .NET framework, see an unofficial roadmap for learning .NET development presented by Nick Chapsas, Microsoft MVP.


### Kentico-specific getting started resources
  1. [What is Xperience by Kentico](https://www.kentico.com/platform) – Learn about the next-gen DXP developed by Kentico Software.
  2. [Xperience by Kentico development basics](/documentation/developers-and-admins/development/website-development-basics) – Get a basic of the Xperience environment and the responsibilities of individual roles that interact with the system.
  3. [Install Xperience by Kentico](/guides/development/get-started/install-xperience-by-kentico) – See a brief Xperience Dancing Goat sample site installation walkthrough.
  4. [Xperience developer kickstart](/guides/development/developer-kickstart) – Learn how to set up the environment for development, create pages that hold data, display the content on the live site using the Model-View-Controller design pattern, and prepare the page builder feature to allow dynamic modification of page content.
  5. [Deploy Xperience websites](/documentation/developers-and-admins/deployment) – See the deployment options for Xperience projects.
  6. [Xperience implementation handbook](/guides/architecture/xperience-implementation-handbook) – Leverage the collective experience of Kentico solution architects to implement best practices for implementing Xperience projects.
  7. [Developing with Xperience by Kentico on macOS](https://community.kentico.com/blog/developing-with-xperience-by-kentico-on-macos) - Read an introduction to running Xperience by Kentico on macOS by Sean Wright, Kentico’s Lead Evangelist.
  8. [Setting up a solution for integrating custom code in Xperience by Kentico](https://www.milanlund.com/knowledge-base/setting-up-a-solution-for-integrating-custom-code-in-xperience-by-kentico-on-macos) - Read a short article written by Milan Lund, [freelance full-stack web developer](https://www.milanlund.com/) and [Kentico Community Leader](https://community.kentico.com/community/programs#kentico-community-leaders).


* * *
## 2. Xperience content modeling
Content modeling is crucial for structuring and managing digital content effectively. In this section, you’ll learn the best practices for creating optimal content models for Xperience by Kentico projects.
**Content modeling prerequisites**
You might find the following resources helpful if you are new to content modeling and designing applications’ information architectures. (These resources were not developed by Kentico and might not reflect Kentico recommended practices for Xperience by Kentico projects.)
  * [Kontent: Content modeling overview](https://kontent.ai/learn/model/overview) – Learn introduction to content modeling from an excellent series created by the Kontent.ai documentation team.
  * [Schema.org](https://schema.org/) – See the community-driven project to create shared vocabulary you can use with different encodings.
  * [Nielsen Group: Information Architecture](https://www.nngroup.com/articles/ia-study-guide/) – A collection of links will help you learn the information architecture (IA), how to run an IA research study, and how to design navigation effectively.


### Kentico-specific content modeling resources
  1. [Content modeling guide](/guides/architecture/content-modeling/content-modeling-guide) – Learn what content modeling is, how to tie the content model to your marketing goals in Xperience by Kentico applications, and general content modeling recommendations.
  2. [Model reusable content](/guides/architecture/content-modeling/model-reusable-content) – Discover techniques for modeling reusable structured content, such as articles or products, and enable your team to manage and repurpose your content across your digital marketing channel.
  3. [Model website content types](/guides/architecture/content-modeling/model-website-content-types) – See how to tailor content types to the website’s requirements, such as articles and navigation elements, to foster uniformity and clarity across your entire digital ecosystem.
  4. [Differences between Page Builder content and structured content](/guides/architecture/content-modeling/xperience-content-modeling-basics/differences-between-structured-content-and-page-builder-content) – Find out more about the different approaches to content in Xperience website channels.
  5. [Model website presentation components](/guides/architecture/content-modeling/model-website-presentation-components) – Learn how to structure key website presentation components like widgets, page templates, or sections that help editors create a unified visual design while giving them the flexibility to achieve their marketing goals.
  6. [How to safely evolve a content model](https://community.kentico.com/blog/safely-evolving-a-content-model-with-expand-and-contract) – see how developers can manage an evolving solution that will inevitably have breaking changes while maintaining business continuity.


* * *
## 3. Xperience advanced content presentation
In this section, you will learn options to retrieve and present content using advanced techniques with Xperience Builders, including how to use the Page Builder, define editable areas, and create dynamic components for different marketing channels.
**Advanced content presentation prerequisites**
If you are new to .NET, these resources will help you understand ViewComponents, use Tag Helpers to render HTML, and manage views for data presentation in ASP.NET Core.
  * [View components in ASP.NET Core](https://learn.microsoft.com/en-us/aspnet/core/mvc/views/view-components?view=aspnetcore-8.0) – Get familiar with ViewComponents using Microsoft documentation.
  * [An introduction to ViewComponents](https://andrewlock.net/an-introduction-to-viewcomponents-a-login-status-view-component/) – See a detailed introduction to ViewComponents by [Andrew Lock](https://andrewlock.net/about/).
  * [Tag Helpers in ASP.NET Core](https://learn.microsoft.com/en-us/aspnet/core/mvc/views/tag-helpers/intro?view=aspnetcore-8.0) – Learn how to enable server-side code to create and render HTML elements in Razor files from Microsoft documentation.
  * [Views in .NET](https://learn.microsoft.com/en-us/aspnet/core/mvc/views/overview?view=aspnetcore-8.0) – See the basics of handling the app’s data presentation and user interaction in Microsoft documentation.


**Multilingual ASP.NET applications**
  * [Globalization and localization in ASP.NET Core](https://learn.microsoft.com/en-us/aspnet/core/fundamentals/localization?view=aspnetcore-8.0) – Freshen up on the fundamentals of creating localized ASP.NET applications in Microsoft documentation.
  * [Localization – .NET](https://learn.microsoft.com/en-us/dotnet/core/extensions/localization) – Find out more about localizing .NET applications in Microsoft documentation.
  * [Best practices for developing world-ready applications](https://learn.microsoft.com/en-us/dotnet/core/extensions/best-practices-for-developing-world-ready-apps) – See Microsoft’s best practices to follow when developing world-ready applications.


**Headless applications**
  * [Learn GraphQL](https://graphql.org/learn/) – Learn about GraphQL that Xperience used to retrieve content in Xperience headless channels.


### Kentico-specific advanced content presentation resources
  1. [Page Builder](/guides/development/page-builder) – Go through a comprehensive guide that provides step-by-step instructions on implementing and optimizing Xperience using Kentico Page Builder components.
  2. [Form Builder](/documentation/developers-and-admins/development/builders/form-builder) – Read how to enable marketers to create dynamic forms using the built-in Xperience in Xperience by Kentico documentation.
  3. [Reference - Tag Helpers](/documentation/developers-and-admins/development/reference-tag-helpers) – See how to add tag helpers to views in Xperience by Kentico applications.
  4. [Defining Editable Area Restrictions](https://community.kentico.com/blog/defining-editable-area-restrictions) – Learn how to limit the options available to marketers in the Page Builder to specific combinations that work best together.
  5. [Configuring Rich text editor](/documentation/developers-and-admins/configuration/rich-text-editor-configuration) – See how you can customize the built-in editor by changing its configuration (e.g., the set of available formatting options) or by adding custom plugins that extend its available feature set.
  6. [Taxonomies in Xperience applications](/guides/development/advanced-content/filter-content-based-on-taxonomies) – Learn how to leverage taxonomy tags to fetch and display related content in Xperience by Kentico documentation.
  7. [Creating a video embed widget with visibility conditions](https://community.kentico.com/blog/creating-a-video-embed-widget-with-visibility-conditions) – See how you can use multiple visibility conditions in the properties of page builder and form builder components on a custom Video widget.
  8. [Multilingual](/guides/development/multilingual) – Learn how to localize Xperience applications and manage URLs for multilingual pages while implementing a user-friendly language selector.
  9. [Delivering content using smart folders](/guides/development/advanced-content/deliver-content-dynamically-with-smart-folders) – Learn how you can dynamically deliver content through Xperience Smart folders.
  10. [Retrieving headless content](/documentation/developers-and-admins/development/content-retrieval/retrieve-headless-content) – Learn how to retrieve headless content in the JSON data format through the GraphQL API endpoint.


* * *
## 4. Xperience configurations
You’ll learn how to configure the foundational settings of Xperience by Kentico to optimize your development environment. You’ll explore configurations for essential components like the event log, Content hub, and rich text editor, ensuring you can tailor these elements to meet your project’s needs. You’ll also learn more about available system settings and configuration keys for precise control over your application’s functionality.
### Kentico-specific configuration resources
  1. [Digital marketing channels in Xperience](/guides/digital-marketing/work-with-channels/digital-marketing-channels) – Learn how Xperience understands digital channels, especially what the website channels are and how they fit into the digital experience you can build with Xperience by Kentico DXP.
  2. [Settings](/documentation/developers-and-admins/configuration/settings) – See the different settings you can use in the administration to configure system features in Xperience by Kentico documentation.
  3. [Content hub configuration](/documentation/developers-and-admins/configuration/content-hub-configuration) – Learn about the different configuration options you can use to customize your project in Xperience by Kentico documentation.
  4. [Website channel management](/documentation/developers-and-admins/configuration/website-channel-management) – See how to configure websites in Xperience by Kentico documentation.
  5. [Headless channel management](/documentation/developers-and-admins/configuration/headless-channel-management) – Learn how to configure Xperience to support delivering content through headless channels, including the content preview.
  6. [Work with users](/guides/configuration/work-with-users-and-roles/work-with-users) – Learn about user management in Xperience by Kentico and empower your team with the right roles and permissions for efficient content creation and personalization.
  7. [Work with roles](/guides/configuration/work-with-users-and-roles/work-with-roles) – See how Xperience by Kentico leverages the role-based access control system to manage which applications and actions editors and marketers can perform.
  8. [Reference - Configuration keys](/documentation/developers-and-admins/configuration/reference-configuration-keys) – Find out more how to extend settings available in Xperience administration using low-level application settings.
  9. [Notifications](/documentation/developers-and-admins/configuration/notifications) – Find out how to provide information to administration users about various events in the system.
  10. [Scheduled tasks](/documentation/developers-and-admins/customization/scheduled-tasks) - See how to run custom code leveraging Xperience’s built-in scheduler.


* * *
## 5. Xperience data access techniques
In this section, you’ll learn how to design data access techniques for Xperience projects, including implementing the Repository and CQRS patterns to enhance performance, scalability, and security.
**Data access techniques prerequisites**
If you are new to .NET, find out more about how to design your application’s infrastructure using the Repository and CQRS patterns.
  * [Design the infrastructure persistence layer](https://learn.microsoft.com/en-us/dotnet/architecture/microservices/microservice-ddd-cqrs-patterns/infrastructure-persistence-layer-design) – Learn about the Repository pattern in the Microsoft documentation.
  * [CQRS architecture pattern](https://learn.microsoft.com/en-us/azure/architecture/patterns/cqrs) – Learn how you can separate read and update operations for storing data to increase application’s performance, scalability and security. (This pattern is used in the [Kentico Community portal](https://github.com/Kentico/community-portal)).


### Kentico-specific resources about data access techniques
  1. [Use abstraction and enterprise-level architecture in Xperience projects](/guides/development/get-started/use-abstraction-and-enterprise-level-architecture-in-xperience-projects) – Learn about the benefits of abstraction in enterprise projects and the advantages of layering for code maintenance. See examples of displaying content with clean architecture in Xperience by Kentico.
  2. [Guide to Repository pattern with a Generic Type in Xperience by Kentico](https://www.milanlund.com/knowledge-base/guide-to-repository-pattern-with-a-generic-type-in-xperience-by-kentico) – Go through a step-by-step process of creating repositories that retrieve data from the Xperience by Kentico database written by Milan Lund, [freelance full-stack web developer](https://www.milanlund.com/) and [Kentico Community Leader](https://community.kentico.com/community/programs#kentico-community-leaders).


* * *
## 6. Xperience caching, testing, and optimization
In this section, you’ll learn how to use caching to improve website performance by storing data for faster access, explore various caching types like data, output, and file caching to reduce load times and understand Xperience’s server-side caching mechanisms, including how to manage cache keys and dependencies for efficient content delivery.
**Caching, testing, and optimization prerequisites**
If you are new to .NET, discover the fundamentals of caching techniques and debugging practices for ASP.NET Core and Visual Studio, and learn about different testing methods to validate your code.
  * [Overview of caching in ASP.NET Core](https://learn.microsoft.com/en-us/aspnet/core/performance/caching/overview?view=aspnetcore-8.0) – Learn about the different types of caching in .NET Core in Microsoft documentation.
  * [Caching in .NET](https://learn.microsoft.com/en-us/dotnet/core/extensions/caching) – See the different caching mechanisms in .NET in Microsoft documentation.
  * [Debug C# code in Visual Studio](https://learn.microsoft.com/en-us/visualstudio/get-started/csharp/tutorial-debugger?view=vs-2022) – See the step-by-step tutorial about Visual Studio debugger in Microsoft documentation.
  * [Debugging in Visual Studio Code](https://code.visualstudio.com/docs/editor/debugging) – Learn about the VS Code’s built-in debugger helps accelerate your edit, compile, and debug loop.
  * [Testing in .NET](https://learn.microsoft.com/en-us/dotnet/core/testing/) – Learn about the different types of tests in .NET to validate your code in Microsoft documentation.


### Kentico-specific caching, testing, and optimization resources
  1. [Caching](/documentation/developers-and-admins/development/caching) – Learn about the built-in caching mechanisms to improve performance and content delivery.
  2. [Event log](/documentation/developers-and-admins/configuration/event-log) – See how you can view logged information about system-level events in the documentation article.
  3. [Xperience by Kentico MiniProfiler](https://github.com/Kentico/xperience-by-kentico-miniprofiler) – The integration allows you to use a simple but effective mini-profiler for .NET and other languages. Michael Eustace, [Kentico MVP](https://community.kentico.com/community/programs#kentico-mvps), describes how to use this integration in his article [Get to know Xperience by Kentico: Unlocking performance insights with MiniProfiler](https://dev.to/michael419/get-to-know-xperience-by-kentico-unlocking-performance-insights-with-miniprofiler-2b2c).
  4. [Diagnose Page Builder template](https://community.kentico.com/blog/diagnosing-xperience-by-kentico-page-template-issues) – Learn about different template configuration errors and how to solve them.


* * *
## 7. Xperience user management, membership, registration, and authentication
In this section, you’ll learn how to create and manage different memberships for website visitors and backend users, set up registration, control access to tools and content, and add custom fields to user profiles.
**Registration and authentication prerequisites**
If you are new to Identity, explore these resources to understand ASP.NET Core’s security features, including documentation on authentication, user management, and integrating Microsoft’s Identity platforms like the Entra ID and Azure Active Directory into your web applications.
  * [ASP.Core security topics](https://learn.microsoft.com/en-us/aspnet/core/security/?view=aspnetcore-8.0) – See a list of links for different security-related topics in Microsoft documentation.
  * [Overview of ASP.NET Core authentication](https://learn.microsoft.com/en-us/aspnet/core/security/authentication/?view=aspnetcore-8.0) – Go through several examples of authentication and authorization in Microsoft documentation.
  * [(optional) Introduction to Identity on ASP.NET Core](https://learn.microsoft.com/en-us/aspnet/core/security/authentication/identity?view=aspnetcore-8.0&tabs=visual-studio) – This documentation by Microsoft covers the basics of managing users, passwords, profile data, roles, claims, tokens, email confirmation, and more with the default framework’s libraries and Identity API.
  * [Quickstart: Sign in users and call the Microsoft Graph API from an ASP.NET Core web app](https://learn.microsoft.com/en-us/entra/identity-platform/quickstart-web-app-dotnet-core-sign-in) – See a short tutorial on how to sign in users with the authorization code flow using Microsoft Authentication Library for .NET and Microsoft Identity Web to handle authentication.
  * [Microsoft Entra ID with ASP.NET Core](https://learn.microsoft.com/en-us/aspnet/core/security/authentication/azure-active-directory/?view=aspnetcore-8.0)– Follow a short tutorial Microsoft that demonstrates how to implement Identity platform and Entra ID in ASP.NET Core.


### Kentico-specific registration and authentication resources
  1. [Members](/documentation/business-users/members) – Learn about the members, who are visitors registered on the website who can browse the website, and access secured content but don’t have access to the administration UI.
  2. [Add fields to member objects](/documentation/developers-and-admins/development/registration-and-authentication/add-fields-to-member-objects) – See how you can extend the default field set of your member objects.
  3. [Members](/guides/development/members) – Read a series of guides that show you how to implement a [member registration widget](/guides/development/members/implement-member-registration), [create a profile page](/guides/development/members/create-a-profile-page) or [require authentication for certain content](/guides/development/members/require-authentication-for-certain-content).
  4. [Administration - Registration and authentication](/documentation/developers-and-admins/configuration/users/administration-registration-and-authentication) – Learn about how to configure the ASP.NET Identity for admin UI or implement external authentication provider in the documentation.
  5. [Users](/documentation/developers-and-admins/configuration/users) – Learn about managing users and roles and enabling registration and authentication, and working with user and role management in the Xperience by Kentico documentation.
  6. [Reference - Xperience Portal user roles](/documentation/developers-and-admins/saas/xperience-portal) – Find out about the different users and roles to access the Xperience Portal administration and read through a detailed [reference](/documentation/developers-and-admins/saas/xperience-portal/reference-xperience-portal-user-roles) that explains the Xperience Portal’s roles and privileges.
  7. [Developing registration and authentication](/documentation/developers-and-admins/development/registration-and-authentication) – Learn how to implement different registration and authentication mechanisms in Xperience by Kentico documentation.


* * *
## 8. Xperience deployment
In this section, you’ll learn about Xperience by Kentico’s hosting options, ranging from [private cloud](/documentation/developers-and-admins/deployment/deploy-to-private-cloud) on dedicated or virtual servers to managed solutions on Microsoft Azure, and how to deploy and maintain websites as a standard .NET project in both traditional and SaaS environments, separating production from non-production instances.
**Deployment prerequisites**
If you are new to deploying .NET applications, these resources will help you skill up on various deployment strategies, such as hosting and deploying ASP.NET Core applications, using the Azure App Service, understanding deployment best practices, or leveraging Azure Pipelines for efficient CI/CD workflows.
  * [Host and deploy ASP.NET Core](https://learn.microsoft.com/en-us/aspnet/core/host-and-deploy/?view=aspnetcore-8.0) – Learn about different types of deployment options in Microsoft documentation.
  * [Deploy an ASP.NET web app](https://learn.microsoft.com/en-us/azure/app-service/quickstart-dotnetcore?tabs=net80&pivots=development-environment-vs) – See how to create and deploy ASP.NET web application to Azure App Service in a Microsoft step-by-step tutorial.
  * [Deployment Best Practices](https://learn.microsoft.com/en-us/azure/app-service/deploy-best-practices) – Read Microsoft’s guide to deployment best practices.
  * [Key concepts for new Azure Pipelines users](https://learn.microsoft.com/en-us/azure/devops/pipelines/get-started/key-pipelines-concepts?view=azure-devops) – Learn about effective building, testing, and deploying through Azure Pipelines in Microsoft documentation, which you can leverage in Xperience by Kentico projects.
  * [Scale up an app in Azure App Service](https://learn.microsoft.com/en-us/azure/app-service/manage-scale-up) – Learn about two workflows to increase the app’s performance in Microsoft documentation.


### Kentico-specific deployment resources
  1. [Deploy to the SaaS environment](/guides/configuration/saas/saas-walkthrough) – See how you can deploy to multiple environments to isolate the production and non-production application instances using the Xperience Portal in our step-by-step practical guide.
  2. [Manage SaaS deployments](/documentation/developers-and-admins/deployment/deploy-to-the-saas-environment/manage-saas-deployments) – Learn how to work with Xperience Portal to manage and debug different environments, monitor the uptime of application deployments, or evaluate the performance of your projects in Xperience by Kentico documentation.
  3. [Deploy to private cloud](/documentation/developers-and-admins/deployment/deploy-to-private-cloud) – See how you can run Xperience on dedicated or virtual servers or manage your hosting on the Microsoft Azure in Xperience by Kentico documentation.
  4. [Deploy without the administration](/documentation/developers-and-admins/deployment/deploy-to-private-cloud/deploy-without-the-administration) – See how to remove the Xperience administration interface and all related customizations when publishing to production in Xperience by Kentico documentation.
  5. [Multi-instance support in Xperience](/documentation/developers-and-admins/configuration/auto-scaling-support) – Learn about the built-in support for multiple instances (auto-scaling and load balancing) you can use to distribute computing among multiple applications or increase availability in Xperience by Kentico documentation.
  6. [Xperience by Kentico CI/CD developer scenarios](https://community.kentico.com/blog/xperience-by-kentico-ci-cd-developer-scenarios) – Get inspiration from various scenarios for managing and deploying code using Continuous Deployment, and other Xperience features.
  7. [Xperience by Kentico – release process considerations and deployment on the SaaS environment](https://konabos.com/blog/xperience-by-kentico-release-process-considerations-and-deployment-on-the-saas-environment) – Read an article by Lukas Skowroński, a Senior Solutions Architect at Kentico partner agency.
  8. [Kentico’s Xperience Portal and SaaS: From Setup to Production](https://www.milanlund.com/knowledge-base/kentico-xperience-portal-and-saas-from-setup-to-production) – Read an article by Milan Lund, [freelance full-stack web developer](https://www.milanlund.com/) and [Kentico Community Leader](https://community.kentico.com/community/programs#kentico-community-leaders).


* * *
## 9. Xperience maintenance, hotfixing, and upgrades
In this section, you’ll learn how to maintain an Xperience project by regularly updating your system, managing tasks through a SaaS portal, and automating updates with tools like Dependabot for continuous integration and security.
### Kentico-specific maintenance resources
  1. [Maitain your solution](/guides/architecture/xperience-implementation-handbook/maintain-what-you-have-built) – Remind yourself about different maintenance scenarios in [Xperience implementation handbook](/guides/architecture/xperience-implementation-handbook).
  2. [Update Xperience by Kentico projects](/documentation/developers-and-admins/installation/update-xperience-by-kentico-projects) – See the documentation for an overview of recommended approaches to updating Xperience by Kentico projects.
  3. [Changelog](/documentation/changelog) – Get familiar with the Xperience changelog.
  4. [Keeping your Xperience by Kentico project up-to-date using Dependabot](https://www.goldfinch.me/blog/keeping-your-xperience-by-kentico-project-up-to-date-using-dependabot) – Learn from Liam Goldfinch, [Kentico MVP](https://community.kentico.com/community/programs#kentico-mvps), how to use Dependabot to regularly scan your project to identify vulnerabilities, outdated packages, or even raise pull requests.
  5. [How to automate Xperience by Kentico upgrades in your deployment pipeline](https://www.youtube.com/watch?v=qCXIgbSFXZ8&ab_channel=MichaelEustace) – Watch a video by Michael Eustace, [Kentico MVP](https://community.kentico.com/community/programs#kentico-mvps), about how you can leverage PowerShell scripts to upgrade sites.


* * *
## 10. Xperience administration customizations
In this section, you’ll learn about creating custom and extending existing modules in Xperience, such as building a website settings module, adding fields to the Contact profile or customizing system providers.
**Administration customization prerequisites**
If you are primarily a back-end developer tasked with expanding Xperience administration resources will guide you through learning React for building user interfaces and TypeScript for adding strong typing to your projects, as utilized in the Xperience UI.
  * [Quick Start: React](https://react.dev/learn) – Use the React documentation to refresh your knowledge of React, which is used to build the Xperience UI.
  * [Describing the UI](https://react.dev/learn/describing-the-ui) – Scan through an overview of how to build a simple UI component in React.
  * [React JavaScript Framework for Beginners](https://www.youtube.com/watch?v=u6gSSpfsoOQ) – Use this FreeCodeCamp course for beginners to learn React.
  * [TypeScript](https://www.typescriptlang.org/) – Use the TypeScript documentation to refresh your understanding of the language that’s used to strongly type the Xperience by Kentico UI components.


### Kentico-specific customization resources
**Custom module**
  1. [Create a basic module in Xperience](/guides/development/customizations-and-integrations/create-basic-module) – Follow a series of guides that show you how to implement a module that holds custom settings.
  2. [Add a custom field to the Contact profile](/guides/development/customizations-and-integrations/add-custom-contact-field) – See how to extend the contact profile to allow marketers to track custom domain-specific data about visitors.


**Extending UI pages**
  1. [Extend the administration interface](/documentation/developers-and-admins/customization/extend-the-administration-interface) – See how to extend the existing Xperience UI in the documentation.
  2. [Integrate custom code](/documentation/developers-and-admins/customization/integrate-custom-code) – Learn the recommended approach to adding custom code into your project.
  3. [Administration interface UI tests](/documentation/developers-and-admins/customization/extend-the-administration-interface/administration-interface-ui-tests) – Learn about the recommended practices for testing custom UI code created to extend Xperience administration.


**Extend default functionality**
  1. [Extending Xperience by Kentico Administration UI Pages](https://community.kentico.com/blog/extending-xperience-by-kentico-administration-ui-pages) – Learn how to extend the existing UI pages to display the required information.
  2. [Extending Xperience by Kentico Administration UI Pages #2 - Enhanced System Overview](https://community.kentico.com/blog/extending-xperience-by-kentico-administration-ui-pages-2-enhanced-system-overview) – See how you can extend the Xperience administration UI to help developers track deployments in multiple environments.
  3. [Handle global events](/documentation/developers-and-admins/customization/handle-global-events) – Learn about the different options you can use to execute custom code whenever specific actions occur within the system.
  4. [Excluding Content Items from Continuous Deployment](https://community.kentico.com/blog/excluding-content-items-from-continuous-deployment-using-global-events) – See the different ways you can use to remove specific object types from continuous deployment.
  5. [Customize system providers](/documentation/developers-and-admins/customization/customize-system-providers) – Learn about providers in Xperience API that you can use to manipulate objects and perform various actions.


* * *
## 11. Xperience digital marketing
In this section on Kentico’s digital marketing features of Xperience, you’ll learn how to build Xperience by Kentico projects that allow marketers to attract new visitors, promote your products and business, analyze traffic, and maintain engagement with existing contacts. As a developer or administrator, you will explore detailed scenarios that guide you through setting up, configuring, and possibly extending these features using custom code.
**Digital marketing in Xperience**
[Digital marketing essentials](/guides/digital-marketing/xperience-essentials/digital-marketing-essentials-introduction) – Learn about several Xperience applications marketers use to shape your visitors’ experiences and brush up on your digital marketing terminology in Kentico [LexiK](https://www.kentico.com/discover/lexik).
## 11.1 Data protection and cookies prerequisites
In this section, you’ll learn how to create cookie configuration pages with detailed consent levels, design simple cookie banners, and use identity collectors, data collectors, and data erasers to manage visitor information and ensure GDPR compliance efficiently within your Xperience platform.
**Data protection and cookies prerequisites**
  * [CNIL: GDPR guide for developers](https://github.com/LINCnil/GDPR-Developer-Guide) – An overview of GDPR best practices prepared by French Comission Nationale Informatique & Libertés (CNIL) to assist web and application developers make their work GDPR-compliant.
  * [Working with SameSite cookies in ASP.NET Core](https://learn.microsoft.com/en-us/aspnet/core/security/samesite?view=aspnetcore-8.0) – See an overview of SameSite cookies to protect your application against CSRF attacks in Microsoft documentation.


### Kentico-specific data protection resources
  1. [Data protection](/guides/development/data-protection) – Learn how to implement data protection and the cookie consents in a series of guides.


## 11.2 Activity tracking
In this section, you’ll learn advanced tracking techniques with Xperience by Kentico, including standard activity tracking, custom activity logging, and consent-driven form displays, how to use contacts for detailed behavior analysis across channels, and how to implement both website-based and headless tracking to monitor activities from a centralized hub for diverse content channels.
### Kentico-specific activity tracking resources
  1. [Activities and digital marketing](/guides/development/activities-and-marketing) – Learn about standard activity tracking, logging custom activities, and other scenarios in a dedicated series of guides on digital marketing.
  2. [Contact configuration](/documentation/developers-and-admins/digital-marketing-setup/contact-configuration) – See an overview of what contacts are and how they work in Xperience by Kentico documentation.
  3. [Activity tracking in headless channels](/documentation/developers-and-admins/digital-marketing-setup/headless-tracking) – Read documentation about how to track visitors as contacts and log their activities in Xperience headless channels.


## 11.3 Content personalization
In this section, you’ll learn about content personalization in Xperience by Kentico, including enhancing Page Builder widgets with dynamic changes based on user interactions and integrating personalization to your project’s code across components, controllers, views, or other classes.
### Kentico-specific content personalization resources
  1. [Content personalization](/documentation/developers-and-admins/digital-marketing-setup/content-personalization) – Learn about different approaches to enable marketers to tailor visitor content based on their preferences.
  2. [Develop personalization condition types](/documentation/developers-and-admins/digital-marketing-setup/content-personalization/develop-personalization-condition-types) – See how you can create conditions marketers will use to personalize widgets.


## 11.4 Email markerting
You’ll learn about setting up and managing email channels in Xperience, creating and customizing email templates to enhance your digital marketing strategies, and exploring advanced email customization techniques, including tracking setups, using autoresponders, and integrating custom workflow notifications to automate communication effectively.
**Emails prerequisites**
If you are primarily a back-end developer and interested in email development, these resources will help you start with HTML emails, understand the MJML language for responsive designs, and learn to render Razor views to strings in ASP.NET Core for sending various types of emails.
  * [Get started with HTML emails](https://github.com/lukas-xb/starter-html-email-resources) – List of resources to start with developing HTML emails.
  * [MJML Guides](https://documentation.mjml.io/) – Learn about the MJML markup language designed to code responsive emails.


**Sending emails from Xperience using code**
  * [Back to Basics: Rendering Razor Views to String in ASP.NET Core](https://weblog.west-wind.com/posts/2022/Jun/21/Back-to-Basics-Rendering-Razor-Views-to-String-in-ASPNET-Core) – Refresh the techniques you can use to send custom email confirmations and account verifications, status updates, or scheduler notifications.


### Kentico-specific emails resources
  1. [Email channel management](/documentation/developers-and-admins/digital-marketing-setup/email-channel-management) – Find out how to configure channels marketers can use to send and administer emails.
  2. [Email templates](/documentation/developers-and-admins/digital-marketing-setup/email-templates) – Learn how to create templates in Xperience by Kentico documentation.
  3. [Set up email tracking](/documentation/developers-and-admins/digital-marketing-setup/set-up-email-tracking) – See how to enhance emails that allow marketers to track sent emails and monitor recipients’ interactions.
  4. [Log email activities](/documentation/developers-and-admins/digital-marketing-setup/set-up-email-tracking/log-email-activities) – See how to implement logic that enables tracking activities while staying GDPR-compliant.
  5. [Email customizations](/documentation/developers-and-admins/customization/email-customization) – Learn how to customize the Xperience’s email-sending process.
  6. [Programmatically sending templated emails with dynamic data](https://community.kentico.com/blog/programmatically-sending-templated-emails-with-dynamic-data) – Learn to build custom templated emails for the system to send when specific events occur.
  7. [Custom form autoresponder emails](/documentation/developers-and-admins/digital-marketing-setup/custom-form-autoresponder-emails) – Learn how to send simple autoresponder emails after form submissions.
  8. [Custom Autoresponder Emails with Razor Components](https://community.kentico.com/blog/custom-autoresponder-emails-with-razor-components) – See how to create “developer-managed” autoresponder emails that fully customize automatic responses for form submissions.
  9. [Set up custom workflow notifications](/documentation/developers-and-admins/customization/handle-global-events/set-up-custom-workflow-notifications) – Follow this guide to set up notifications about workflow steps.


* * *
## 12. Advanced: Working with Xperience API
This section contains a list of important documentation pages that cover advanced topics for developing sites in Xperience by Kentico. You’ll learn about available APIs and macro syntax you can use to manipulate Xperience objects.
### Kentico-specific API resources
  * [Use the Xperience by Kentico API externally](/documentation/developers-and-admins/api/use-the-xperience-by-kentico-api-externally) – See how to configure and external .NET application to work with Xperience API.
  * [Xperience by Kentico API Examples](/api/) – See the API examples documentation to learn the basics of the Xperience by Kentico API, and serves as a library of code snippets.
  * [Content item API](/documentation/developers-and-admins/api/content-item-api/content-item-query-api) – Learn about the API available for working with content items.
  * [ObjectQuery API](/documentation/developers-and-admins/api/objectquery-api) – Find out more about the QbjectQuery API that creates an abstraction layer over the SQL database.
  * [Database table API](/documentation/developers-and-admins/api/database-table-api) – Learn about the API for managing the data stored in database tables.
  * [Files API and CMS.IO](/documentation/developers-and-admins/api/files-api-and-cms-io) – Find out more about the CMS.IO namespace that provides an interlayer between the business layer of Xperience and storage used for physical files.
  * [Macro expressions](/documentation/developers-and-admins/configuration/macro-expressions) – Go through a section of Xperience by Kentico documentation that covers macro expressions and raises different considerations for developing and deploying code containing macros.


* * *
## 13. Xperience integrations and migrations
In this section, you will learn how to improve Xperience by Kentico with third-party integrations, including external authentication and search features using Algolia, Lucene, and Azure AI Search, and how to upgrade from Kentico 13 to Xperience by Kentico.
### Kentico-specific integration resources
  1. [Choose the right tools for your integration](/guides/development/customizations-and-integrations/tools-for-integrations) – Learn the available entry points for integrating Xperience projects with third-party services.
  2. [Xperience third-party integrations](/documentation/developers-and-admins/third-party-integrations) – See an overview of different third-party integrations in Xperience by Kentico documentation.


**Search**
  1. [Xperience by Kentico Algolia](https://github.com/Kentico/xperience-by-kentico-algolia) – Learn about the integration you can use to create Algolia search indexes to index content of web pages from an Xperience website channel using a code-first approach.
  2. [Xperience by Kentico Azure AI Search](https://github.com/Kentico/xperience-by-kentico-azure-ai-search) – Learn about an integration that allows you to AzureSearch search indexes to index content of pages from the Xperience content tree using a code-first approach.
  3. [Xperience by Kentico Lucene](https://github.com/Kentico/xperience-by-kentico-lucene) – Learn about the integration leveraging the latest 4.8 beta version of Lucene.NET, enabling auto-indexing of content in Xperience based on application-local, code-defined search indexes and search results retrieval.


**External scheduled tasks**
  1. [Missing Scheduled Tasks in Xperience by Kentico? Simply integrate Hangfire](https://www.bluesoft.cz/en/blog/missing-scheduletasks-in-xperience-by-kentico) – Read an article written by Milan Sustek, Kentico Partner agency CIO. (This article came out before Xperience’s [built-in scheduled tasks](/documentation/developers-and-admins/customization/scheduled-tasks) were publicly available, but you may find it useful for edge cases that they do not cover.)


**Migrations**
  1. [Migrate from Kentico Xperience 13](/guides/upgrade-to-xbyk/upgrade-from-kx13/upgrade-from-kx13-overview) – Read a four-article series about [migrating to Xperience by Kentico](/guides/upgrade-to-xbyk/upgrade-from-kx13), including advice on [how to prepare for code changes](/guides/upgrade-to-xbyk/upgrade-from-kx13/adjust-your-code-and-adapt).
  2. [Xperience by Kentico Migration Toolkit](https://github.com/Kentico/xperience-by-kentico-migration-toolkit) – Learn about the tools designed to help teams migrate content from various data sources into Xperience by Kentico solutions.


* * *
## Keep advancing your skills in Xperience by Kentico
Congratulations on completing these resources to improve your understanding of Xperience by Kentico.
We encourage you to keep exploring and deepening your knowledge to further your developer journey. Remember, every step you take builds more expertise and confidence in using Xperience by Kentico to create exceptional solutions. Follow the [Community Portal](https://community.kentico.com/), find more resources in the [Resource hub](https://community.kentico.com/resource-hub). If you find a helpful resource that you think we should include into this learning path (or have other feedback), let us know through the feedback form below!
Keep learning and growing as you tackle new challenges! And after you have delivered an Xperience project or two, why not take the [Certified developer exam](https://www.kentico.com/services/certification/xperience-developer-certification), share your Xperience by Kentico knowledge on a [blog](https://community.kentico.com/blog) , and even [become a Kentico MVP](https://community.kentico.com/blog/you-could-be-a-kentico-mvp).
The sky is the limit!
![]()
[]()[]()
