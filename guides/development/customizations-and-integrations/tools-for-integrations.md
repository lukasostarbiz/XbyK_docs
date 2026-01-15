# Choose the right tools for your integration
  * Concept| [ Copy page link ](guides/development/customizations-and-integrations/tools-for-integrations#) | [Get HelpService ID](guides/development/customizations-and-integrations/tools-for-integrations#) | This page is part of a module: [ Additional customizations ](modules/additional-customizations)
Core MVC 5


[✖](guides/development/customizations-and-integrations/tools-for-integrations# "Close page link panel") [Copy to clipboard](guides/development/customizations-and-integrations/tools-for-integrations#)
Powered by .NET, Xperience by Kentico projects offer the same flexibility as any .NET application when executing your code. This versatility provides plenty of opportunities to integrate your project with external services. However, you may want to execute code under certain circumstances specific to Xperience.
This guide will highlight the available entry points for integrating Xperience projects with third-party services. Then it will discuss which options were chosen for the [Lucene integration](https://github.com/Kentico/xperience-by-kentico-lucene).
## Appraise the available tools
### Front-end code
Let’s start by looking at ways you can work your integration into the front-end site that displays web channel content.
#### Controller actions
While controller actions aren’t strictly necessary in Xperience by Kentico, thanks to [Content tree-based (CTB) routing](documentation/developers-and-admins/development/routing/content-tree-based-routing), there are a few ways that you can implement them. With CTB routing, you can register controllers with the router to handle requests for specific content types. Additionally, CTB routing works alongside conventional ASP.NET routing so that you can execute controller actions for particular routes.
Because controller actions fire whenever requests reach a specific route, they are a great way to send data to the external service you want to integrate with whenever the user takes a route-related action, like visiting a page or clicking a button that posts data to a controller endpoint.
#### View components
A [view component](https://learn.microsoft.com/en-us/aspnet/core/mvc/views/view-components?view=aspnetcore-8.0) contains an invocation method that fires whenever a view loads it. This can come in handy if you want to send data to an integration when a page is visited, but you have several controller actions that could lead to the same page.
You can also add buttons that create POST requests to your view components. The view components can post to controller actions or APIs provided by the external service.
While view components may not be specific to Xperience by Kentico, they can be included in other Xperience-specific components, such as widgets, sections, and templates.
#### Page Builder components
Similar to view components, Page Builder components such as [widgets](documentation/developers-and-admins/development/builders/page-builder/widgets-for-page-builder) and [sections](documentation/developers-and-admins/development/builders/page-builder/sections-for-page-builder) can execute code whenever they are loaded and create POST requests when users interact with buttons or other controls.
You can build widgets with your integration in mind. Allow your editors to interact with an integrated service through a widget’s properties, or instead, create a UI for front-end users to interact with the service in the widget’s display.
### Back-end code
Next, let’s look at some options related to the project’s back end. In some cases, they may relate to actions in the front end, but they are not as tightly bound to it.
#### Global event handlers
[Global event handlers](documentation/developers-and-admins/customization/handle-global-events) execute whenever Xperience raises the events to which they are registered. Typically, this happens when Xperience inserts, updates, or deletes [an object of a given type](documentation/developers-and-admins/customization/handle-global-events/handle-object-events). Xperience also raises events for other scenarios, like [when a page changes workflow steps](documentation/developers-and-admins/customization/handle-global-events/reference-global-system-events#workflow-events) or [when a contact revokes consent](documentation/developers-and-admins/customization/handle-global-events/reference-global-system-events#dataprotectionevents).
These events allow you to react to things that happen in Xperience that are relevant to your integration. For a small-scale example, you could integrate with an SMS service that sends a text message to a user whenever an important field on their account is updated. For a larger-scale example, you could synchronize records with a custom CRM whenever the corresponding object in Xperience changes.
#### Custom scheduled tasks
[Scheduled tasks](documentation/developers-and-admins/customization/scheduled-tasks) allow you to execute custom code at regular intervals. This is ideal for tasks like processing data in bulk, or synchronizing Xperience with external systems.
In integration scenarios, we recommend storing data in your Xperience instance and working with it there, synchronizing changes to the external system. If managing data in Xperience isn’t feasible, clearly define your “source of truth” (e.g., your ERP or another external system) and the process for content editors to manage each data type.
If the built-in scheduled tasks do not meet your requirements, you can look into external services like [Hangfire](https://www.hangfire.io/) or [Quartz.NET](https://www.quartz-scheduler.net/).
### Custom module classes
[Custom module classes](documentation/developers-and-admins/customization/object-types) are a vital part of most integrations. They store structured data that doesn’t work as content items in the database. Xperience’s [code generation functionality](documentation/developers-and-admins/api/generate-code-files-for-system-objects) makes them easy to work with in code and opens many possibilities for using them with features.
For example, you could create a custom module class to mirror an object type from an external system, bind identifiers from that external system to Xperience objects, or represent a queue of Xperience objects that need to be synchronized.
You can [define dependencies between custom module classes](documentation/developers-and-admins/customization/object-types/object-type-configuration/model-object-type-relationships) and [decide whether they should be included in features like Continuous Integration](documentation/developers-and-admins/customization/object-types/object-type-configuration/enable-ci-cd-for-object-types).
#### Custom module code
While they are used to register global event handlers, [code-only modules](documentation/developers-and-admins/customization/run-code-on-application-startup) can also run other code when the application initializes. Code in a module’s `OnInit` method runs at the ideal point in the application lifecycle to hook into many parts of the Xperience by Kentico API, which comes in handy when you want to set anything based on values from your external integration.
#### Custom implementations
By registering custom implementations of Xperience’s [providers](documentation/developers-and-admins/customization/customize-system-providers) and [services](documentation/developers-and-admins/customization/decorate-system-services), you can ensure that your custom code executes in place of the default implementations, even when calls are made within Xperience libraries. In some cases, this allows you to achieve functionality that is not possible through event handlers alone.
#### ThreadQueueWorker
Xperience provides a `ThreadQueueWorker<TItem, TWorker>` class that you can use to process jobs in a queue that runs on an asynchronous background thread. This is ideal for scenarios where your integration has batches of tasks that need to be synchronized without slowing down the operations of other threads.
### Admin UI avenues
The following customization options allow you to execute custom code from within the admin UI of Xperience by Kentico and modify it to suit your integration.
#### Customizations to the admin UI
Xperience by Kentico offers multiple avenues for adding new screens and controls to the admin UI, including [extenders](documentation/developers-and-admins/customization/extend-the-administration-interface/ui-pages/ui-page-extenders), [custom UI pages](documentation/developers-and-admins/customization/extend-the-administration-interface/ui-pages), and [UI form components](documentation/developers-and-admins/customization/extend-the-administration-interface/ui-form-components).
  * **Extenders** allow you to expand the functionality of built-in UI pages with new data and actions. They can incorporate values from your custom module classes, new fields added to system classes, and custom UI page commands that allow you to execute code in context.
  * **Custom UI** pages allow you to build new applications and sections in the Xperience admin UI. These can include predefined templates for CRUD operations on custom module class objects and custom templates that define unique page layouts.
  * **Form components** allow you to define the interface for editing individual fields in widget properties, forms, and UI pages.


Together, these options provide several different ways to interact with your integration.
#### Custom macro fields
[Custom macro fields](documentation/developers-and-admins/configuration/macro-expressions/define-macro-fields) allow you to register a code that returns a value with a given macro resolver. You can later call this code from somewhere in the system where the macro resolver is available, for example, when defining fields for UI forms.
### Ways to interact with Xperience from external systems
When integrating Xperience with other systems, you will most likely run into scenarios where the other product needs to initiate an interaction, either providing input or retrieving data from Xperience. The following options allow external systems to connect to Xperience.
#### Endpoints
You can create endpoints in your Xperience application to enable two-way communication between Xperience and external systems. While it’s most common to use API controllers, .NET allows you to create web services in other formats, like GraphQL, RPC, and SOAP. Thanks to this flexibility, you can tailor these endpoints to suit the needs of your integration precisely.
#### External applications using Xperience API
If you are integrating with a system that is also .NET based, or if you prefer to create a separate application between Xperience and another service, you can [use the Xperience API in an external application](documentation/developers-and-admins/api/use-the-xperience-by-kentico-api-externally). This application will be able to leverage Xperience by Kentico code directly, rather than depending on endpoints in your primary Xperience project.
This can be ideal for synchronizing low-priority data from Xperience to another system because it can offload processing to a different machine. However, it should not be used when you need high data fidelity. For example, if you create a console application that updates contacts in the Xperience database, the primary Xperience application will not know about these changes and will display outdated data. The synchronized data will be overwritten if an editor saves this contact from the Xperience admin UI.
#### GraphQL
If your integration involves an external application that needs to access data stored as headless content items in Xperience, then the built-in [GraphQL API](documentation/developers-and-admins/configuration/headless-channel-management#graphql-api-endpoints) may provide exactly what you need. It allows you to query headless content items and the linked reusable content they reference using GraphQL syntax.
## Examine the Lucene integration
Let’s examine an existing integration and consider how it uses these tools to meet each of its requirements. For a proper search experience, the Lucene integration needs to do the following:
  1. Save information about what data to include in search indexes.
  2. Empower editors to define indexes with these specifications.
  3. React to changes in Xperience content and use them to update the relevant data in search indexes.
  4. Allow visitors to query the indexes for results.


### Save index information
The search integration needs to save configuration data that determines what an index should contain. There must be a way to determine which content types, paths, and languages to include in a given index.
Custom module classes best fit this requirement. Storing configurations for indexes is suited to structured data. Additionally, the dependencies you can create between module classes make it easier to have one-to-many relationships, for example, mapping several content types to a single search index.
The Lucene integration has one module class representing the overall index configuration and a separate, dependent module class for indexed content types, languages, and paths.
### Enable editors to define indexes
The integration must allow editors to create indexes with the configurations described previously. This means there should be a UI for them, and the best way to make it is through custom UI pages and UI form components.
The Lucene integration has UI pages to create, insert, and edit indexes. These pages use a custom UI form control to interact with all the previously mentioned classes on a single page.
### Keep indexes up to date
It is essential for search data on a site to be accurate, so the search integration needs to update the data in an index whenever there are changes to the content on the site. This is well suited to global event handlers, which watch for changes to content items and update the index.
However, on large enterprise projects with dozens of editors, changes to content are often frequent. This could lead to performance issues as the index is updated repeatedly.
The Lucene integration uses global event handlers to log tasks, which are then queued on a background thread by a class derived from `ThreadQueueWorker`.
### Query search indexes
A search integration’s most crucial capability is allowing visitors to search.
Since search queries require input from the visitor, a controller action that handles POST requests best fits this scenario.
The _examples_ folder of the Lucene integration’s repository demonstrates this, with the `SearchController` class resolving services that use the integration’s code to query the indexes.
## What’s next?
Now that we’ve walked through the ways that the Lucene integration connects to Xperience by Kentico, you can review the requirements of your next integration and consider whether the choices here make sense for you as well.
In the meantime, you can also check out the [Lucene integration’s repository](https://github.com/Kentico/xperience-by-kentico-lucene) to see how its code ticks under the hood and read about how to use it in your project.
If you haven’t already, we highly recommend looking over our [guidelines for stable customization](documentation/developers-and-admins/customization/stable-customization-guidelines), to ensure that your implementations are smooth and reliable.