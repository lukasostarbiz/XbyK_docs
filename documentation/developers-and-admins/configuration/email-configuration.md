---
source: https://docs.kentico.com/documentation/developers-and-admins/configuration/email-configuration
scrape_date: 2026-01-26
---

  * [Home](/documentation)
  * [Developers and admins](/documentation/developers-and-admins)
  * [Configuration](/documentation/developers-and-admins/configuration)
  * Email configuration 


# Email configuration
Some Xperience by Kentico features utilize emails, for example, [digital marketing emails](/documentation/business-users/digital-marketing/emails) or when [resetting a user password](/documentation/developers-and-admins/configuration/users/user-management#reset-password).
To allow Xperience to send emails, you need to set up one or more of the following options:
  * an external [SMTP server](#smtp-servers)
  * [SendGrid integration](#sendgrid-integration) (the only option supported when [deploying to the SaaS environment](/documentation/developers-and-admins/deployment/deploy-to-the-saas-environment))
  * a [custom email client](/documentation/developers-and-admins/customization/email-customization) that utilizes a different third-party mailout service (e.g., MailChimp)


Xperience then processes sent emails with an **email queue** stored in the database and relays the emails to a designated email server. To **manage an email queue** from the Xperience administration – monitor the email queue, resend failed emails, etc. – use the [Email queue](/documentation/business-users/email-queue) application.
See [Email queue configuration](#email-queue-configuration) for information about the email queue processing and the available configuration options.
If your project needs to handle a very large number of emails, you can **offload the email queue processing from the main web application**. See [External mailout](#external-mailout).
## SMTP servers
Use the following process to connect external SMTP servers to your Xperience application. You can configure the SMTP settings separately for individual [email channels](/documentation/developers-and-admins/digital-marketing-setup/email-channel-management), as well as system emails and [notifications](/documentation/developers-and-admins/configuration/notifications) that do not belong under a specific channel (e.g., [user](/documentation/developers-and-admins/configuration/users) registration, password reset emails). Depending on your preferences and project requirements, you can use different SMTP servers, share the same server, or combine SMTP servers with [SendGrid accounts](#self-managed-sendgrid-integration).
  1. Open your Xperience project in Visual Studio and edit the **Program.cs** file.
  2. Call one or more of the following methods for `IServiceCollection` when building the web application: 
     * `AddXperienceSystemSmtp` – SMTP configuration for system emails and [notifications](/documentation/developers-and-admins/configuration/notifications) that do not belong under a specific email channel.
     * `AddXperienceChannelSmtp` – SMTP configuration for a specific [email channel](/documentation/developers-and-admins/digital-marketing-setup/email-channel-management). Specify the **code name** of the email channel as the method’s parameter.
     * `AddXperienceSmtp` – fallback SMTP configuration for system emails, [notifications](/documentation/developers-and-admins/configuration/notifications) and all email channels without their own dedicated configuration.
  3. For each method, configure the SMTP connection using the `Server` property of the method’s `SmtpOptions` parameter. The underlying `SmtpServer` type provides the following properties: 
     * `Host` – the hostname of the SMTP server (domain name or IP address). Enter _localhost_ to use a server provided by your local machine.
     * `Port` – the port number used by the server (typically 587).
     * `UserName` – if the server requires authentication, specify the user name.
     * `Password` – if the server requires authentication, specify the password for the user name.
  4. (Optional) Set the encoding properties of the `SmtpOptions` parameter: 
     * `Encoding` – sets the [System.Text.Encoding](https://docs.microsoft.com/en-us/dotnet/api/system.text.encoding) applied to the subject and body of sent emails. The default is `UTF8`.
     * `TransferEncoding` – sets the [System.Net.Mime.TransferEncoding](https://docs.microsoft.com/en-us/dotnet/api/system.net.mime.transferencoding?view=net-6.0) used when sending emails. The default is `Base64`.


C#
**Program.cs**
Copy
```
using System.Net.Mime;
using System.Text;

using CMS.EmailEngine;

...

WebApplicationBuilder builder = WebApplication.CreateBuilder(args);

...

// Configures email sending using an SMTP server for system emails
builder.Services.AddXperienceSystemSmtp(options =>
{
    options.Server = new SmtpServer { Host = "smtp.server1.com", Port = 587 };
    options.Encoding = Encoding.UTF8;
    options.TransferEncoding = TransferEncoding.QuotedPrintable;
});
// Configures email sending using an SMTP server for the 'AcmeEmailChannel' email channel
builder.Services.AddXperienceChannelSmtp("AcmeEmailChannel", options =>
{
    options.Server = new SmtpServer { Host = "smtp.server2.com", Port = 587 };
});
```

**SMTP server options in configuration files**
You can use ASP.NET Core [configuration providers](https://docs.microsoft.com/en-us/aspnet/core/fundamentals/configuration/) to separate the SMTP server details from the application’s startup code to a configuration source (e.g., the default _appsettings.json_ file). This allows you to make runtime changes to the SMTP server configuration without needing to adjust and redeploy the code or restart the application (depending on the specifics of your selected configuration source).
For system email SMTP configurations added via `AddXperienceSystemSmtp`, call the `ConfigureSystemSmtpOptions` method.
For email channel-specific SMTP configurations added via `AddXperienceChannelSmtp`, call the .NET [Configure](https://learn.microsoft.com/en-us/dotnet/api/microsoft.extensions.dependencyinjection.optionsconfigurationservicecollectionextensions.configure) method. The **name** of the options instance must match the code name of the email channel.
C#
**Example - Program.cs WebApplicationBuilder configuration**
Copy
```
// Loads the SmtpOptions configuration from configuration file sections
builder.Services.ConfigureSystemSmtpOptions(builder.Configuration.GetSection("SystemSmtpOptions"));
builder.Services.Configure<SmtpOptions>("AcmeEmailChannel", builder.Configuration.GetSection("AcmeSmtpOptions"));
```

JSON
**Example - appsettings.json**
Copy
```
{
   "SystemSmtpOptions": {
    "Server" : {
      "Host": "smtp.server1.com",
      "Port": 587
    }
  },
  "AcmeSmtpOptions": {
    "Server" : {
      "Host": "smtp.server2.com",
      "Port": 587
    }
  },
  ...
}
```

The system now relays emails to the connected SMTP servers. You can set **sender addresses** individually for each Xperience feature that sends emails, see [Configurable sender email addresses](#configurable-sender-email-addresses).
To test whether your SMTP servers are correctly configured, see [Test email sending](#test-email-sending). To [monitor emails](/documentation/business-users/email-queue) ready for mailout, use the **Email queue** application in the Xperience administration.
**SMTP connection restrictions on Microsoft Azure**
If your application is hosted on Microsoft Azure, emails may fail to send from Xperience due to SMTP connection restrictions on the side of Azure.
To resolve such issues, read and follow the recommendations in the [Troubleshoot outbound SMTP connectivity problems in Azure](https://docs.microsoft.com/en-us/azure/virtual-network/troubleshoot-outbound-smtp-connectivity) article.
### Bounced email tracking with SMTP servers
If you send emails using an SMTP server, and wish to track bounced emails and email delivery rates, you need to additionally configure a POP3 connection to a dedicated bounce mailbox. For more information, see [Set up email tracking](/documentation/developers-and-admins/digital-marketing-setup/set-up-email-tracking).
## SendGrid integration
Xperience provides a client for sending email using the [Twilio SendGrid](https://sendgrid.com/) email platform.
When [deploying to the SaaS environment](/documentation/developers-and-admins/deployment/deploy-to-the-saas-environment), Xperience provides the required SendGrid accounts and configuration – see [managed SendGrid integration](#Emailconfiguration-EnableManagedSendGrid).
For [private cloud](/documentation/developers-and-admins/deployment/deploy-to-private-cloud) Xperience deployments, you can use a [self-managed SendGrid instance](#self-managed-sendgrid-integration).
### Managed SendGrid integration
Xperience applications deployed to the SaaS environment integrate Twilio SendGrid for sending emails. Kentico manages the SendGrid accounts with the required API keys and configuration as part of all [service plans](/documentation/developers-and-admins/saas/saas-service-plans).
#### Enable managed SendGrid integration
Perform the following steps before [deploying](/documentation/developers-and-admins/deployment/deploy-to-the-saas-environment) your project to the SaaS environment:
If you are using the provided [.NET project templates](/documentation/developers-and-admins/installation#install-project-templates-package) (`kentico-xperience-mvc` or `kentico-xperience-sample-mvc` ), the required method is called by default for all SaaS deployment environments.
  1. Open your Xperience project in Visual Studio and edit the **Program.cs** file.
  2. Call `WebApplicationBuilder.Services.AddXperienceCloudSendGrid` when building the web application. Use [environment identification extension methods](/documentation/developers-and-admins/configuration/saas-configuration#environment-identification-extension-methods) to enable SendGrid only for selected environments.
SendGrid does not work in the local development environment. Use other email services for local development, for example, [Papercut SMTP server](https://github.com/ChangemakerStudios/Papercut-SMTP). See [SMTP servers](#smtp-servers) on how to connect the Xperience application with an external SMTP server.
C#
**Enabling SendGrid integration for SaaS deployment environments**
Copy
```
using Kentico.Xperience.Cloud;

...

WebApplicationBuilder builder = WebApplication.CreateBuilder(args);

...

if (builder.Environment.IsQa() ||
    builder.Environment.IsUat() ||
    builder.Environment.IsEnvironment(CloudEnvironments.Custom) ||
    builder.Environment.IsEnvironment(CloudEnvironments.Staging) ||
    builder.Environment.IsProduction())
{
    builder.Services.AddXperienceCloudSendGrid(builder.Configuration);
}
```



After you deploy your application to the SaaS environment, the system relays emails to SendGrid. Emails are considered as successfully sent when SendGrid accepts them. The actual delivery is not guaranteed by Xperience and relies on SendGrid. 
The domain used in sender addresses depends on the type of the sent email:
  * **Email channels** – initially use an automatically generated default domain, but you can [configure your own custom domains](/documentation/developers-and-admins/digital-marketing-setup/email-channel-management#email-channels-in-the-saas-environment) in Xperience Portal. 
    * This includes all [emails](/documentation/business-users/digital-marketing/emails) created in the UI of a website channel application. These emails use the channel’s _Sending domain_ , and are associated with your website’s branding and identity. The channel domain needs to be verified in SendGrid, which ensures that the email is sent from a legitimate source and is recognized as coming from your brand.
  * **Notifications and system emails** (e.g., [user invitation](/documentation/developers-and-admins/configuration/users/user-management#invite-users), password reset) – always use a default sending domain in format: _< environment>-<project_name>.xperience-sites.com_
    * This includes [notifications](/documentation/developers-and-admins/configuration/notifications) for administration users and any other emails that are not created in the UI of a website channel. The system sending domain is automatically generated and pre-verified. Since this domain is already configured in Kentico’s SaaS environment, no additional configuration is required.


Once your configuration is complete, you can:
  * [Test whether the integration is correctly configured](#test-email-sending).
  * [Monitor emails](/documentation/business-users/email-queue) using the **Email queue** application in the Xperience administration.


### Self-managed SendGrid integration
Use the following process to set up SendGrid integration for [private cloud](/documentation/developers-and-admins/deployment/deploy-to-private-cloud) environments. You can configure the SendGrid settings separately for individual [email channels](/documentation/developers-and-admins/digital-marketing-setup/email-channel-management), as well as system emails that do not belong under a specific channel (e.g., [user](/documentation/developers-and-admins/configuration/users) registration, password reset emails). Depending on your preferences and project requirements, you can use different SendGrid accounts, share the same account, or combine SendGrid with other [SMTP servers](#smtp-servers).
  1. Set up one or more [SendGrid accounts](https://sendgrid.com/) with an Email API plan.
  2. Open your Xperience project in Visual Studio.
  3. Install the **Kentico.Xperience.SendGrid** NuGet package.
  4. Edit your application’s **Program.cs** file.
  5. Call one or more of the following methods for `IServiceCollection` when building the web application: 
     * `AddXperienceSystemSendGrid` – SendGrid configuration for system emails and [notifications](/documentation/developers-and-admins/configuration/notifications) that do not belong under a specific email channel.
     * `AddXperienceChannelSendGrid` – SendGrid configuration for a specific [email channel](/documentation/developers-and-admins/digital-marketing-setup/email-channel-management). Specify the **code name** of the email channel as the method’s parameter.
     * `AddXperienceSendGrid` – fallback SendGrid configuration for system emails, [notifications](/documentation/developers-and-admins/configuration/notifications) and all email channels without their own dedicated configuration.
  6. For each method, set your [SendGrid API Key](https://docs.sendgrid.com/ui/account-and-settings/api-keys) into the `ApiKey` property of the `SendGridClientOptions` parameter. 
     * The SendGrid API Key must have the **Full Access** type and permissions.
     * You can use ASP.NET Core [configuration providers](https://docs.microsoft.com/en-us/aspnet/core/fundamentals/configuration/) to load the SendGrid API key from a configuration source, such as the default _appSettings.json_ file or [Azure Key Vault](https://docs.microsoft.com/en-us/aspnet/core/security/key-vault-configuration). 
       * For system email SendGrid configurations added via `AddXperienceSystemSendGrid`, call the `ConfigureSystemSendGridClientOptions` method.
       * For email channel-specific SendGrid configurations added via `AddXperienceChannelSendGrid`, call the .NET [Configure](https://learn.microsoft.com/en-us/dotnet/api/microsoft.extensions.dependencyinjection.optionsconfigurationservicecollectionextensions.configure) method. The **name** of the options instance must match the code name of the email channel.


C#
**Program.cs**
Copy
```
using CMS.EmailEngine;
using Kentico.Xperience.SendGrid;

using SendGrid;

...

WebApplicationBuilder builder = WebApplication.CreateBuilder(args);

...

// Configures email sending via SendGrid for system emails
builder.Services.AddXperienceSystemSendGrid();
// Configures email sending via SendGrid for the 'AcmeEmailChannel' email channel
builder.Services.AddXperienceChannelSendGrid("AcmeEmailChannel");

// Loads the SendGrid API keys from configuration file sections
builder.Services.ConfigureSystemSendGridClientOptions(builder.Configuration.GetSection("SystemSendGridClientOptions"));
builder.Services.Configure<SendGridClientOptions>("AcmeEmailChannel", builder.Configuration.GetSection("AcmeSendGridClientOptions"));
```

JSON
**Example - appsettings.json**
Copy
```
{
  "SystemSendGridClientOptions": {
    "ApiKey" : "..."
  },
  "AcmeSendGridClientOptions": {
    "ApiKey" : "..."
  },
  ...
}
```

The system now relays emails to the specified SendGrid instances. The system consider emails successfully sent when SendGrid accepts them. The actual delivery is not guaranteed by Xperience and relies on SendGrid.
To learn how to set email **sender addresses** suitable for your SendGrid account, configure individual Xperience features that send emails – see [Configurable sender email addresses](#configurable-sender-email-addresses).
You can now [test the email configuration](#test-email-sending), and [monitor emails](/documentation/business-users/email-queue) using the **Email queue** application in the Xperience administration.
## Configurable sender email addresses
### Sender email address for private cloud environment
The system allows you to configure the sender (From) email addresses used by various system features. See the following table for details:
**Supported email address formats**
The system supports all email address formats allowed by the [System.Net.Mail.MailAddress](https://learn.microsoft.com/en-us/dotnet/api/system.net.mail.mailaddress) class. For example:
  * `user@host`
  * `"display name" <user@host>`


Feature |  Default from address |  Description  
---|---|---  
[Custom form autoresponder emails](/documentation/developers-and-admins/digital-marketing-setup/custom-form-autoresponder-emails#custom-form-autoresponder-emails) |  `no-reply@localhost.local` |  Custom emails for the [form](/documentation/business-users/digital-marketing/forms) autoresponder. Sent automatically to every user who submits the form. The sender can be set in the [code that defines each custom email](/documentation/developers-and-admins/digital-marketing-setup/custom-form-autoresponder-emails). If the sender is not set directly, the default address reflects the [global sending domain for system emails](#global-sending-domain-for-system-emails).  
[Notifications](/documentation/developers-and-admins/configuration/notifications) |  `no-reply@localhost.local` |  Messages sent to Xperience administration users informing about various events, such as invitations or password reset requests. The sender can be configured individually for each notification, and reflects the [global sending domain for system emails](#global-sending-domain-for-system-emails) if only the local part of the address is set.  
[Emails](/documentation/business-users/digital-marketing/emails#create-emails) |  none |  Email created within an [email channel](/documentation/developers-and-admins/digital-marketing-setup/email-channel-management) use the sender configuration of the given channel.  
#### Global sending domain for system emails
You can configure the sending domain for all [notifications](/documentation/developers-and-admins/configuration/notifications) and other system emails that are not created under a specific email channel (e.g., [user invitation](/documentation/developers-and-admins/configuration/users/user-management#invite-users), password reset) via the `SystemEmailOptions` class.
The specified domain is used when sending system emails and [notifications](/documentation/developers-and-admins/configuration/notifications) if only the local part of the sender address is set (e.g., via the _Notifications_ application).
For example:
JSON
**appsettings.Development.json**
Copy
```
{
  "SystemEmailOptions": {
    "SendingDomain": "localhost.com"
  }
}
```

JSON
**appsettings.json**
Copy
```
{
  "SystemEmailOptions": {
    "SendingDomain": "maindomain.com"
  }
}
```

C#
**Program.cs - Configure sending domain for transactional emails**
Copy
```
var builder = WebApplication.CreateBuilder(args);

...

builder.Services.Configure<SystemEmailOptions>(builder.Configuration.GetSection("SystemEmailOptions"));
```

### Custom system email domains in the SaaS environment
You can customize the system email domains for applications [deployed in the SaaS environment](/documentation/developers-and-admins/deployment/deploy-to-the-saas-environment):
  1. Open the **Channels and Domains → System domains** application in [Xperience Portal](/documentation/developers-and-admins/saas/xperience-portal) with a **Tenant administrator** or **DevOps Engineer** role.
  2. Select the **Replace** (
     * Email sending domain is used to send emails that are not created under a specific email channel. Not to be confused with the [email channel sending domain](/documentation/developers-and-admins/digital-marketing-setup/email-channel-management#email-channels-in-the-saas-environment).
     * Email service domain is used as the base for various system URLs in the channel’s emails, such as subscription/unsubscription URLs.
  3. Enter the new **Domain name** and select **Create**. A domain name cannot be assigned to both a system domain and a channel domain.
  4. Copy the displayed DNS records from Xperience Portal to your DNS registrar.
  5. After the settings are successfully registered with your DNS registrar, confirm the DNS configuration. This triggers a DNS validation process that takes a few minutes to finish. 
     * If the DNS validation fails, double-check your DNS settings and select **Revalidate** (


Each SaaS environment can only have one active Email sending and one Email service domain for system emails. By configuring and successfully validating a new domain, the existing Email sending or Email service domain will be replaced by the newly configured one.
To configure domains for email channels in the SaaS environment, see [Email channels in the SaaS environment](/documentation/developers-and-admins/digital-marketing-setup/email-channel-management#email-channels-in-the-saas-environment).
## Email queue configuration
Emails created by the Xperience application are initially placed into an email queue and stored in the database. The queue reduces the risk of losing emails due to errors, allows manual resending, and can also be used to archive sent emails. Users can [work with the email queue](/documentation/business-users/email-queue) in the **Email queue** application of the Xperience administration. After you configure one or more email clients ([SMTP server](#smtp-servers) or [SendGrid integration](#sendgrid-integration)), the system starts processing emails from the queue.
Xperience runs an ongoing process that automatically manages the email queue:
  * The process checks the queue for new emails at **regular intervals**.
  * New emails in the queue are loaded in pre-defined **batches** and relayed to the configured email client.
  * The process continues loading and sending batches until all items in the queue are mailed out.
  * By default, successfully sent emails are deleted from the queue. You can configure **archiving** of successfully sent emails for a specific number of days.
  * If delivery to the email client fails, the email remains in the queue until it is handled manually – either deleted or successfully re-sent later.


You can **adjust the email queue configuration** by changing the following options:
  1. Open your Xperience project in Visual Studio and edit the **Program.cs** file.
  2. Call the `Configure<EmailQueueOptions>` method for `IServiceCollection` when building the web application (using `WebApplicationBuilder`).
  3. The `EmailQueueOptions` type provides the following properties: 
     * `ArchiveDuration` – sets the number of days for which successfully sent emails are archived. The default value is 0, which completely disables archiving. Archived emails can be viewed on the **Sent emails** tab in the Xperience **Email queue** application.
     * `DeleteBatchSize` – sets the number of archived emails that are deleted in a single batch when the system periodically cleans archived emails older than the _ArchiveDuration_. The default value is 2000. You can adjust the value to optimize performance on projects that archive a large number of emails.
     * `BatchSize` – sets the number of emails loaded from the email queue in a single batch. The default value is 100. You can adjust the batch size to optimize email sending performance based on the parameters of your web server and database.
     * `LoadInterval` – sets the interval between checks for new emails to be sent from the queue. The value is in milliseconds and the default is 60000 (one minute).


C#
**Program.cs**
Copy
```
using CMS.EmailEngine;

...

WebApplicationBuilder builder = WebApplication.CreateBuilder(args);

...

// Configures how the system processes the email queue
builder.Services.Configure<EmailQueueOptions>(options =>
{
    options.ArchiveDuration = 5;
    options.DeleteBatchSize = 5000;
    options.BatchSize = 500;
    options.LoadInterval = 30000;
});
```

## Test email sending
If you wish to test or debug email sending, we recommend setting up a local SMTP client, such as [Papercut SMTP](https://github.com/ChangemakerStudios/Papercut-SMTP). To verify that email sending from Xperience works correctly, send a test email.
### System emails
To test the email client configured for **system emails** :
  1. Open the **Email queue** application in the Xperience administration.
  2. On the **Email queue** tab, select **Send test email**.
  3. Fill in the email fields.
When using SendGrid, use a verified **domain address**. 
  4. Select **Send test email**.
  5. Check the recipient’s mailbox (or your virtual email viewer).


These test emails bypass the queue and are sent directly to the currently configured system email client.
### Channel emails
To test the email client configured for individual [email channels](/documentation/developers-and-admins/digital-marketing-setup/email-channel-management):
  1. Open the email channel application in Xperience.
  2. Create a testing [email](/documentation/business-users/digital-marketing/emails).
  3. Expand the action menu in the upper right of the view and select **Send draft**.
  4. Send a draft email to an address where you can check the email’s delivery.


## External mailout
External mailout configuration is only supported for [private cloud environments](/documentation/developers-and-admins/deployment/deploy-to-private-cloud).
If your project needs to handle a very large number of emails, you may wish to offload email sending away from your main Xperience web application. This avoids potential performance impacts of email sending on the live site.
### Main web application
To set up your main Xperience web application (live site) for external mailout:
  1. Open the application’s **Program.cs** file and remove any configuration related to email sending:
     * Email client registration (e.g., [SMTP server](#smtp-servers) or [SendGrid](#sendgrid-integration))
     * [Email queue configuration](#email-queue-configuration) (`EmailQueueOptions`)
  2. Call the `Configure<EmailServiceOptions>` method for `IServiceCollection` when building the web application (using `WebApplicationBuilder`).
  3. Set the `MailOutIsExternal` property of `EmailServiceOptions` to `true`.
C#
**Program.cs**
Copy
```
using CMS.EmailEngine;

...

WebApplicationBuilder builder = WebApplication.CreateBuilder(args);

...

builder.Services.Configure<EmailServiceOptions>(options =>
{
    options.MailoutIsExternal = true;
});
```



The application now only adds emails to the queue. Loading of mails from the queue and the mailing itself needs to be handled by a separate application.
With the `MailOutIsExternal` property enabled, parts of the Xperience administration that rely on email sending remain active, even though the application itself does not have an email client configured. In this scenario, it is not possible to send test emails from the Xperience **Email queue** application (test emails bypass the queue, so they cannot be processed by an external mailout application).
### Mailout application
External mailout can be handled either by a separate instance of Xperience, or a custom application that [uses the Xperience API externally](/documentation/developers-and-admins/api/use-the-xperience-by-kentico-api-externally).
  1. Ensure that the mailout application connects to the same database as your main Xperience web application. 
     * By default, this is determined by the `CMSConnectionString` in the **appsettings.json** configuration file.
  2. In the application’s startup class, add any required email configuration: 
     * Email client registration (e.g., [SMTP server](#smtp-servers) or [SendGrid](#sendgrid-integration))
     * [Email queue configuration](#email-queue-configuration) (`EmailQueueOptions`)


The mailout application now processes the email queue, which is shared with your main web application.
![]()
[]()[]()
