---
source: https://docs.kentico.com/documentation/developers-and-admins/customization/email-customization
scrape_date: 2026-01-22
---

  * [Home](/documentation)
  * [Developers and admins](/documentation/developers-and-admins)
  * [Customization](/documentation/developers-and-admins/customization)
  * Email customization 


# Email customization
Xperience allows developers to customize the system’s email sending functionality. The **default** sending process adds emails to a queue, stored in the Xperience database. Emails from the queue are then sent out using either an SMTP server or via SendGrid. See [Email configuration](/documentation/developers-and-admins/configuration/email-configuration) to learn more about the default email functionality.
If the default email functionality does not meet your project’s requirements, you can perform the following types of customization:
  * [Integrate a custom client for sending emails](#custom-email-client)
  * [Implement completely custom email sending functionality](#custom-email-sending-functionality)
  * [Extend the default email functionality](#extend-the-default-email-functionality)


## Custom email client
By implementing a custom email client, you can integrate with any custom or third‑party service for sending emails.
With a custom email client, the system still adds emails to the Xperience email queue. However, emails are sent out from the queue using your custom email client.
Registering a custom email client **overrides** the default [SMTP or SendGrid clients](/documentation/developers-and-admins/configuration/email-configuration). You cannot combine both options, e.g., assign a custom client to one [email channel](/documentation/developers-and-admins/digital-marketing-setup/email-channel-management) and default clients to others.
See [Custom email clients and channels](#custom-email-clients-and-channels) if you wish to use different custom email services for individual channels.
  1. Open your project’s solution in Visual Studio.
  2. [Add a custom assembly](/documentation/developers-and-admins/customization/integrate-custom-code) (_Class Library_ project) to your solution or re-use an existing one.
  3. Create a new class that implements the `IEmailClient` interface.
  4. Define the `SendEmail` method prescribed by the `IEmailClient` interface. 
     * Validate that the properties of the email represented by the method’s `EmailMessage` parameter are set correctly by calling the `EmailMessage.Validate` extension method.
     * Use the API of your email service to send out the email.
     * Return a `Task<EmailSendResult>` value describing the result and details of the send operation.
  5. Register your custom email client in the Xperience web application: 
    1. Edit your application’s **Program.cs** file.
    2. Add your `IEmailClient` implementation [as a singleton](https://docs.microsoft.com/en-us/dotnet/api/microsoft.extensions.dependencyinjection.servicecollectionserviceextensions.addsingleton) to the service container.
    3. Call the `AddEmailQueueServices` method for `IServiceCollection` – this method adds services that load emails from the default Xperience email queue and pass them to the registered `IEmailClient` implementation.
C#
**Program.cs**
Copy
```
using CMS.EmailEngine;

...

WebApplicationBuilder builder = WebApplication.CreateBuilder(args);

...

// Registers a custom IEmailClient
builder.Services.AddSingleton<IEmailClient, CustomEmailClient>();

// Adds services required to process emails from the default Xperience email queue
builder.Services.AddEmailQueueServices();
```



The system now adds emails to the queue just like when using the default SMTP or SendGrid clients. Users can [monitor emails](/documentation/business-users/email-queue) in the queue using the **Email queue** application in the Xperience administration. When sending emails from the queue, the system uses the logic implemented in the `SendEmail` method of the registered `IEmailClient`.
### Custom email clients and channels
Custom `IEmailClient` implementations cannot be used in combination with the default [SMTP or SendGrid clients](/documentation/developers-and-admins/configuration/email-configuration).
If you wish to use different email services for specific email channels or [notifications](/documentation/developers-and-admins/configuration/notifications) and system emails not related to a specific channel, you need to branch the code in your client’s `SendEmail` method.
C#
**Example**
Copy
```
public class CustomEmailClient : IEmailClient
{
    private readonly IInfoProvider<EmailConfigurationInfo> emailConfigurationInfoProvider;

    public CustomEmailClient(IInfoProvider<EmailConfigurationInfo> emailConfigurationInfoProvider)
    {
        this.emailConfigurationInfoProvider = emailConfigurationInfoProvider;
    }

    public Task<EmailSendResult> SendEmail(EmailMessage emailMessage, CancellationToken cancellationToken = default)
    {
        // ...

        // The EmailConfigurationID of the EmailMessage parameter is '0' for system emails not related to a channel
        // For channel-specific emails, you can use the ID to load the configuration object and get the related channel
        int emailConfigurationId = emailMessage.EmailConfigurationID;

        // Checks that the email message is related to an email channel with ID '1'
        if (emailConfigurationInfoProvider.Get(emailConfigurationId).EmailConfigurationEmailChannelID == 1)
        {
            // Send emails to service 1
        }

        // ...
    }
}
```

## Custom email sending functionality
For projects with advanced custom email requirements, you can fully bypass and replace the default Xperience functionality, i.e. the database email queue and the overall sendout mechanism. For example, you can implement custom email functionality that stores emails on the file system, and then use an external service to pick up and mail them.
  1. Open your project’s solution in Visual Studio.
  2. [Add a custom assembly](/documentation/developers-and-admins/customization/integrate-custom-code) (_Class Library_ project) to your solution or re-use an existing one.
  3. Create a new class that implements the `IEmailService` interface.
  4. Define the members prescribed by the `IEmailService` interface: 
     * `ProcessingConfigured` – indicates whether processing of emails via the service is configured and enabled. Affects parts of the Xperience administration that rely on email sending (displaying of warning notifications, enabling/disabling buttons, etc.). For example, you can check whether an application setting is configured with an API key for the email service used by your implementation.
     * `SendEmail` – perform any required logic to send out or store the email represented by the method’s `EmailMessage` parameter.
  5. Register the custom service implementation by adding the `RegisterImplementation` assembly attribute above the class declaration.


Email sending is now fully controlled by your custom service. In this scenario, the Xperience **Email queue** application is not functional and cannot be used to [monitor emails](/documentation/business-users/email-queue) (unless your custom implementation is built around the default Xperience email queue).
### Log email statistics with a custom email service
In custom scenarios where you do not send emails via the the Xperience email queue, [tracking of email statistics](/documentation/business-users/digital-marketing/emails/track-email-statistics) works in the following way:
  * The number of **Sent** emails is not tracked (the default logging occurs when emails are sent from the email queue). If you wish to preserve tracking of sent emails, you must log the statistic hits in your custom code.
  * Email **Opens** and **Clicked** links are tracked automatically, just like when using the default Xperience email functionality.


To log statistics for sent emails, use the `IInfoProvider<EmailStatisticsHitsInfo>` and `EmailStatisticsHitsInfo` [API](/documentation/developers-and-admins/api/database-table-api). Depending on the implementation of your custom email service, you also need to ensure the following:
  * Only log sent email statistic when emails are mailed out successfully. Most email APIs return result objects for mailout operations, which you can use in your conditions.
  * To avoid heavy load on your database, we do not recommend logging hits separately for every sent email. You can prepare a queue or another buffering mechanism, and then log the hits in bulk (`IInfoProvider<EmailStatisticsHitsInfo>.BulkInsert`).


C#
Copy
```
using System;
using System.Threading.Tasks;

using CMS;
using CMS.EmailEngine;
using CMS.EmailLibrary;

// Registers the custom implementation of IEmailService
[assembly: RegisterImplementation(typeof(IEmailService), typeof(Custom.CustomEmailService))]

namespace Custom
{
    public class CustomEmailService : IEmailService
    {
        // Stores an instance of the email statistics hits provider
        private readonly IInfoProvider<EmailStatisticsHitsInfo> emailStatisticsHitsProvider;

        public CustomEmailService(IInfoProvider<EmailStatisticsHitsInfo> emailStatisticsHitsProvider)
        {
            this.emailStatisticsHitsProvider = emailStatisticsHitsProvider;
        }

        // Indicates whether processing of emails is configured
        // Enables or disables parts of the admin UI that rely on email sending
        public bool ProcessingConfigured
        {
            get => true;
        }

        public Task SendEmail(EmailMessage message)
        {
            // Custom mailout logic

            // Prepares an "email sent" statistics hit for the processed EmailMessage
            var emailStatisticsSentHit = new EmailStatisticsHitsInfo()
            {
                EmailStatisticsHitsEmailConfigurationID = message.EmailConfigurationID,
                EmailStatisticsHitsType = EmailStatisticsHitsType.Sent,
                EmailStatisticsHitsTime = DateTime.Now,
                EmailStatisticsHitsMailoutGUID = message.MailoutGuid
            };

            // Log statistic hits into the database
            // We recommend storing/queuing the hits and then logging them in bulk
            // using IInfoProvider<EmailStatisticsHitsInfo>.BulkInsert
        }
    }
}
```

## Extend the default email functionality
The default Xperience email sending functionality allows you to add your own custom actions into the process. For example, you can duplicate sent emails and log them to an external location for auditing purposes.
  1. Follow the customization steps described in the [Custom email sending functionality](#custom-email-sending-functionality) section.
  2. In your `IEmailService` implementation, access the default `IEmailService` using constructor injection.
  3. When defining your class’s members, perform any required custom actions, and call the default service member at the appropriate location. For more information about this pattern, see [Decorate system services](/documentation/developers-and-admins/customization/decorate-system-services).


With this type of customization, the system adds emails to the database queue, and users can [monitor emails](/documentation/business-users/email-queue) using the **Email queue** application in the Xperience administration. You still need to [register an email client](/documentation/developers-and-admins/configuration/email-configuration) (e.g., the default SMTP or SendGrid client) to send out the emails from the queue.
### Example - Extended IEmailService
The following example demonstrates how to define a custom email service that extends the default implementation. This sample uses all functionality of the default `IEmailService` email service, and extends it by [logging an event](/documentation/developers-and-admins/development/logging) whenever the system sends an email. You can use the same approach to add any required custom functionality.
Start by preparing a separate project for custom classes in your Xperience solution:
  1. Open your project’s solution in Visual Studio.
  2. [Add a custom assembly](/documentation/developers-and-admins/customization/integrate-custom-code) (_Class Library_ project) with class discovery enabled to the solution or re-use an existing assembly.
  3. Reference the project from your Xperience website project.


Continue by implementing the custom `IEmailService` class:
  1. Add a new class under the custom project. For example, name the class: _CustomEmailService.cs_
  2. Make the class implement the `IEmailService` interface and define the required members.
  3. Register the custom service implementation by adding the `RegisterImplementation` assembly attribute above the class declaration.


C#
Copy
```
using System.Threading.Tasks;
using Microsoft.Extensions.Logging;

using CMS;
using CMS.Core;
using CMS.EmailEngine;

// Registers the custom implementation of IEmailService
[assembly: RegisterImplementation(typeof(IEmailService), typeof(Custom.CustomEmailService))]

namespace Custom
{
    public class CustomEmailService : IEmailService
    {
        // Stores an instance of the default IEmailService implementation
        private readonly IEmailService defaultEmailService;
        // Stores an instance of the logging service
        private readonly ILogger<CustomEmailService> logger;

        public CustomEmailService(IEmailService defaultEmailService, ILogger<CustomEmailService> logger)
        {
            this.defaultEmailService = defaultEmailService;
            this.logger = logger;
        }

        // Indicates whether processing of emails is configured
        // Enables or disables parts of the admin UI that rely on email sending
        public bool ProcessingConfigured
        {
            // Leaves the default email processing detection
            // True if email queue services and an IEmailClient are registered on application start
            get => defaultEmailService.ProcessingConfigured;
        }

        // Sends out email messages
        public Task SendEmail(EmailMessage message)
        {
            // Adds the email message to the queue using the default email service
            Task emailTask = defaultEmailService.SendEmail(message);

            // Custom action that creates a log entry when an email is sent
            string detail = string.Format("Email from sender {0} sent to the queue.", message.From);
            logger.LogInformation(new EventId(0, "EMAIL_SENDOUT"), detail);

            return emailTask;
        }
    }
}
```

The system now uses the customized service to send emails. Whenever the system adds an email to the queue, a log entry with the _EMAIL_SENDOUT_ event code is created.
You can view the entry in the [Event log](/documentation/developers-and-admins/configuration/event-log), if you have the `XperienceEventLog` logging provider [configured](/documentation/developers-and-admins/development/logging) to include the `Information` log level for the given event category (`Custom.CustomEmailService` in the example above).
![]()
[]()[]()
