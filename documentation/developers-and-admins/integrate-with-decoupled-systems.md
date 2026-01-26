---
source: https://docs.kentico.com/documentation/developers-and-admins/integrate-with-decoupled-systems
scrape_date: 2026-01-26
---

  * [Home](/documentation)
  * [Developers and admins](/documentation/developers-and-admins)
  * Integrate with decoupled systems 


# Integrate with decoupled systems
In modern architectures, your Xperience project may need to interact with other external systems that operate independently, without shared databases and other internal resources. You can integrate Xperience with these decoupled systems by establishing asynchronous messaging through a [messaging service](#messaging-service), which facilitates the exchange of messages between the systems. Depending on your use case, Xperience can act as either [message producer](#xperience-as-a-message-producer), [message consumer](#xperience-as-a-message-consumer), or both.
The following architectural overview illustrates the flow of messages between an Xperience project and a decoupled system:
[![Integration overview](/docsassets/documentation/integrate-with-decoupled-systems/decoupled_systems.drawio.svg)](/docsassets/documentation/integrate-with-decoupled-systems/decoupled_systems.drawio.svg)
Decoupled integrations can be utilized in a wide range of scenarios. Some examples include:
  * send data submitted by customers through [forms](/documentation/business-users/digital-marketing/forms) into your CRM
  * synchronize [product](/documentation/developers-and-admins/digital-commerce-setup/model-product-catalog/model-product-stock) data in Xperience with your inventory management system
  * communicate with various types of business and ticketing systems


This page explains how to set up a decoupled integration with Xperience, covering the [key concepts](#key-concepts), configuration steps to [consume](#xperience-as-a-message-consumer) and [produce](#xperience-as-a-message-producer) messages, and an [example implementation](#example).
## Key concepts
The following concepts address the key points of decoupled integrations in Xperience:
  * [Message triggers](#message-triggers) – determine when messages should be sent from Xperience
  * [Message contents](#message-contents) – define what data is exchanged
  * [Messaging service](#messaging-service) – handle the transmission of messages between systems
  * [Messaging libraries](#messaging-libraries) – use code abstractions for working with the service


### Message triggers
When sending messages from your project, you need to determine what actions require communication with the decoupled system. In Xperience, significant operations are represented by [events](/documentation/developers-and-admins/customization/handle-global-events). Event handling allows you to implement custom logic to send messages when those events occur.
**Learn about events and event handling**
See the corresponding pages about [global events](/documentation/developers-and-admins/customization/handle-global-events) and [object events](/documentation/developers-and-admins/customization/handle-global-events/handle-object-events) or consult the [global events reference](/documentation/developers-and-admins/customization/handle-global-events/reference-global-system-events) to learn about available events and their handling.
You can also choose to send messages directly from appropriate places in your [custom code](/documentation/developers-and-admins/customization/integrate-custom-code) without relying on the provided events.
### Message contents
Messages represent structured data to be exchanged between systems. To ensure consistency, a message can be implemented as a class in a shared library, accessible from both systems.
For example, you can transfer:
  * [Content item](/documentation/business-users/content-hub/content-items) data
  * [Page](/documentation/business-users/website-content) data
  * Submitted [form](/documentation/business-users/digital-marketing/forms) data
  * [Contact](/documentation/business-users/digital-marketing/contact-management) data


### Messaging service
To enable communication with a decoupled system, you need to set up an external messaging service. You can use cloud-based messaging services such as [Azure Service Bus](https://learn.microsoft.com/en-us/azure/service-bus-messaging/service-bus-messaging-overview) or self-managed message brokers, for instance, [RabbitMQ](https://www.rabbitmq.com/).
**Note:** An external messaging service is required both for [SaaS](/documentation/developers-and-admins/saas/saas-overview) and [private-cloud](/documentation/developers-and-admins/deployment/deploy-to-private-cloud) deployments, as it’s not a part of the system or [Xperience Portal](/documentation/developers-and-admins/saas/xperience-portal).
Depending on your requirements, you can configure different communication patterns:
  * [Message queues](https://learn.microsoft.com/en-us/azure/service-bus-messaging/service-bus-messaging-overview#queues) – a single stream of messages typically received by one consumer.
  * [Topics and subscriptions](https://learn.microsoft.com/en-us/azure/service-bus-messaging/service-bus-messaging-overview#topics) – enable one-to-many communication and categorization of messages by topic.


### Messaging libraries
You can use existing libraries that already implement typical messaging patterns to send and receive messages. These libraries provide an abstraction on top of the actual messaging service, making it easier to switch between different messaging platforms without significantly changing your application code.
The sample code on this page uses the [Rebus](https://rebus.fm/what-is-rebus/) library. Similar functionality can be provided by other frameworks and libraries, for example, [Mass Transit](https://masstransit.io/introduction) and [Wolverine](https://wolverinefx.net/).
## Xperience as a message producer
The following steps outline how to configure Xperience to send messages, using the [Rebus](https://rebus.fm/what-is-rebus/) library and [Azure Service Bus](https://learn.microsoft.com/en-us/azure/service-bus-messaging/service-bus-messaging-overview) with the [queue](https://learn.microsoft.com/en-us/azure/service-bus-messaging/service-bus-messaging-overview#queues) messaging pattern. You can use other services and libraries to achieve the same result.
  1. Prerequisites: 
    1. [Set up a queue](https://learn.microsoft.com/en-us/azure/service-bus-messaging/service-bus-dotnet-get-started-with-queues?tabs=connection-string) in your Azure Service Bus [namespace](https://learn.microsoft.com/en-us/azure/service-bus-messaging/service-bus-messaging-overview#namespaces).
    2. Install the [Rebus](https://www.nuget.org/packages/Rebus) and [Rebus.ServiceProvider](https://www.nuget.org/packages/Rebus.ServiceProvider) NuGet packages.
    3. Define a [message](#message-contents) based on your scenario.
  2. Configure the Rebus library in the `Program.cs` file: 
C#
**Program.cs**
Copy
```
using Rebus.Config;
using Rebus.Routing.TypeBased;

...

builder.Services.AddRebus(configure => configure
    .Transport(t => t.UseAzureServiceBusAsOneWayClient("##connectionString##"))
    .Routing(r => r.TypeBased().Map<OutgoingMessage>("##queue-name##")));

...
```

  3. [Handle events](/documentation/developers-and-admins/customization/handle-global-events) and send messages from the handler. 
     * See the [Example](#handle-events-and-send-messages) section for a full implementation.


## Xperience as a message consumer
The following steps outline how to configure Xperience to receive messages, using the [Rebus](https://rebus.fm/what-is-rebus/) library and [Azure Service Bus](https://learn.microsoft.com/en-us/azure/service-bus-messaging/service-bus-messaging-overview) with the [queue](https://learn.microsoft.com/en-us/azure/service-bus-messaging/service-bus-messaging-overview#queues) messaging pattern. You can use other services and libraries to achieve the same result.
  1. Prerequisites:
    1. [Set up a queue](https://learn.microsoft.com/en-us/azure/service-bus-messaging/service-bus-dotnet-get-started-with-queues?tabs=connection-string) in your Azure Service Bus [namespace](https://learn.microsoft.com/en-us/azure/service-bus-messaging/service-bus-messaging-overview#namespaces).
    2. Install the [Rebus](https://www.nuget.org/packages/Rebus) and [Rebus.ServiceProvider](https://www.nuget.org/packages/Rebus.ServiceProvider) NuGet packages.
    3. Define a [message](#message-contents) based on your scenario.
  2. Configure the Rebus library in the `Program.cs` file:
C#
**Program.cs**
Copy
```
using Rebus.Config;

builder.Services.AddRebus(configure => configure
        .Transport(t => t.UseAzureServiceBus("##connectionString##", "##queue-name##")));
```

  3. Create a message handler by implementing the `IHandleMessages` Rebus interface:
C#
**SampleMessageHandler.cs**
Copy
```
using Rebus.Handlers;

public class SampleMessageHandler : IHandleMessages<IncomingMessage>
{
    public Task Handle(IncomingMessage message)
    {
        // Custom logic to process the message
    }
}
```

  4. Register the handler in the `Program.cs` file:
C#
**Program.cs**
Copy
```
builder.Services.AddRebusHandler<SampleMessageHandler>();
```



## Example
When a brand new article is published, you might want to automatically promote it on social media to gain more traction. This example shows an integration where Xperience sends a message to a decoupled system handling social media publishing when a new article is published for the first time.
### Prerequisites
This example uses the [Rebus](https://rebus.fm/what-is-rebus/) library and [Azure Service Bus](https://learn.microsoft.com/en-us/azure/service-bus-messaging/service-bus-messaging-overview). To follow along with this example, install the [Rebus](https://www.nuget.org/packages/Rebus) and [Rebus.ServiceProvider](https://www.nuget.org/packages/Rebus.ServiceProvider) NuGet packages, [set up a queue](https://learn.microsoft.com/en-us/azure/service-bus-messaging/service-bus-dotnet-get-started-with-queues?tabs=connection-string) in your Azure Service Bus namespace and obtain the corresponding [connection string](https://learn.microsoft.com/en-us/azure/service-bus-messaging/service-bus-dotnet-get-started-with-queues?tabs=connection-string).
It’s important to note that the principles shown in this example are not limited to specific platforms and can be implemented with other messaging libraries and services.
### Define the message
Create a class in a library shared by both systems.
C#
**NewArticlePublishedMessage.cs**
Copy
```
public class NewArticlePublishedMessage
{
    public string ArticleTitle { get; init; }
    public string ArticleSummary { get; init; }
    public string ArticleText { get; init; }
    public string ArticleUrl { get; init; }
}
```

### Configure the Rebus library
Add the Rebus library to your project with the following configuration:
C#
**Program.cs**
Copy
```
using Rebus.Config;
using Rebus.Routing.TypeBased;

...

builder.Services.AddRebus(configure => configure
    .Transport(t => t.UseAzureServiceBusAsOneWayClient("##connectionString##"))
    .Routing(r => r.TypeBased().Map<NewArticlePublishedMessage>("##queue-name##")));

...
```

### Handle events and send messages
Create a [handler](/documentation/developers-and-admins/customization/handle-global-events) for the `WebPageEvents.Publish.Execute` event that will send messages when the event takes place. Assign the handler to the event in a [custom initialization module](/documentation/developers-and-admins/customization/run-code-on-application-startup).
C#
**CustomMessagingModule.cs**
Copy
```
using CMS;
using CMS.Core;
using CMS.ContentEngine;
using CMS.DataEngine;
using CMS.Websites;

using Rebus.Bus;

using Microsoft.Extensions.DependencyInjection;

// Registers the custom module into the system
[assembly: RegisterModule(typeof(Custom.CustomMessagingModule))]

namespace Custom
{
    public class CustomMessagingModule : Module
    {
        private IBus bus;
        private IWebPageUrlRetriever webPageUrlRetriever;

        // Module class constructor, the system registers the module under the name 'CustomMessagingModule'
        public CustomMessagingModule()
                : base(nameof(CustomMessagingModule))
        {
        }

        // Contains initialization code that is executed when the application starts
        protected override void OnInit(ModuleInitParameters parameters)
        {
            base.OnInit(parameters);

            // Injects the Rebus IBus service used to send messages
            bus = parameters.Services.GetRequiredService<IBus>();

            // Injects the IWebPageUrlRetriever service used to retrieve the URL of the article
            webPageUrlRetriever = parameters.Services.GetRequiredService<IWebPageUrlRetriever>();

            // Assigns the custom handler to the event
            WebPageEvents.Publish.Execute += Article_Published_EventHandler;

            // Implement the Article_Published_EventHandler
            // See the code block below for the implementation

        }
    }
}
```

C#
**Article published event handler**
Copy
```
// Sends a message to the message queue when a page of the 'ArticlePage' content type is published for the first time
private void Article_Published_EventHandler(object sender, PublishWebPageEventArgs e)
{
    // The content type of the articles to be promoted
    string eligibleContentType = "Acme.ArticlePage";

    // Excludes pages that are not published for the first time and the pages of content types other than 'Acme.ArticlePage'
    if (e.IsFirstTimePublished == false || e.ContentTypeName != eligibleContentType)
    {
        return;
    }

    // Obtains necessary data from the event arguments ('PublishWebPageEventArgs')
    int articleId = e.ID;
    string articleLanguage = e.ContentLanguageName;
    ContentItemDataEventContainer articleData = e.ContentItemData;

    // Retrieves article field values from the data container and assigns them to newly instantiated variables
    // 'ContentItemDataEventContainer.TryGetValue()' returns true if the specified key exists in the container and outputs its value to the provided variable
    articleData.TryGetValue("ArticleTitle", out string articleTitle);
    articleData.TryGetValue("ArticlePageSummary", out string articleSummary);
    articleData.TryGetValue("ArticlePageText", out string articleText);

    // Retrieves the article URL using the 'IWebPageUrlRetriever'
    WebPageUrl articleUrl = webPageUrlRetriever.Retrieve(articleId, articleLanguage).GetAwaiter().GetResult();

    // Creates a new message with the article data
    NewArticlePublishedMessage message = new NewArticlePublishedMessage()
    {
        ArticleTitle = articleTitle,
        ArticleSummary = articleSummary,
        ArticleText = articleText,
        // Assigns the absolute URL of the article to the message property
        ArticleUrl = articleUrl.AbsoluteUrl
    };

    // Sends the message to the queue
    bus.Send(message);
}
```

![]()
[]()[]()
