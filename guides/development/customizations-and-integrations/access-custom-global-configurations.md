---
source: https://docs.kentico.com/guides/development/customizations-and-integrations/access-custom-global-configurations
scrape_date: 2026-01-26
---

  * [Home](/guides)
  * [Development](/guides/development)
  * [Customizations and integrations](/guides/development/customizations-and-integrations)
  * Access custom global configurations 


# Access custom global configurations
[Earlier in this series](/guides/development/customizations-and-integrations/create-basic-module), you learned how to create a custom module that holds custom channel-agnostic key-value-based settings.
Let’s say your customer wants to use the new module to define two custom email notification settings: **Email notification recipients** and **Email notification sender**.
Your browser does not support the video tag. 
Every time Xperience sends a certain type of email notification, these two settings should determine who the recipients and the sender are.
**How do you access the values in your code to meet the customer’s requirements?**
Using the **email notification settings** described above as an example, let’s explore how you can access custom module values and how to utilize the [Options pattern](https://learn.microsoft.com/en-us/aspnet/core/fundamentals/configuration/options?view=aspnetcore-8.0) to work with custom settings defined in the Xperience in a strongly typed manner.
## Before you start
This guide requires the following:
  * Familiarity with [C#](https://learn.microsoft.com/en-us/dotnet/csharp/), [.NET Core](https://learn.microsoft.com/en-us/dotnet/), [Dependency injection](https://learn.microsoft.com/en-us/dotnet/core/extensions/dependency-injection), and the [MVC pattern](https://learn.microsoft.com/en-us/aspnet/core/mvc/overview).
  * A running instance of Xperience by Kentico, preferably [30.11.1](/documentation/changelog) or higher. 
Some features covered in the training guides may not work in older versions. 


The examples in this guide require that you:
  * Have followed along with the samples from [the previous guide](/guides/development/customizations-and-integrations/create-basic-module) that implement a custom module holding global settings.


**Code samples**
You can find a project with completed, working versions of code samples from this guide and others in the [finished branch](https://github.com/Kentico/xperience-by-kentico-training-guides/tree/finished) of the _Training guides_ repository.
The [main branch](https://github.com/Kentico/xperience-by-kentico-training-guides) of the repository provides a starting point to code along with the guides.
The code samples in this guide are for [.NET 8](https://learn.microsoft.com/en-us/dotnet/core/whats-new/dotnet-8/overview) only.
They come from a project that uses [implicit using directives](https://learn.microsoft.com/en-us/dotnet/core/project-sdk/overview#implicit-using-directives). You may need to add additional `using` directives to your code if your project does not use this feature.
## Enter the data
To follow along with the example here, use the UI developed earlier in this series to create two email notification settings, as seen in [this video](#EmailSettingsDemo).
You can copy-paste these values:
  * **Display name:** Email notification recipients
  * **Settings key code name:** EmailNotificationToAddresses
  * **Value:** :admin@localhost.local;support@localhost.local
  * **Notes:** The email addresses that should receive custom email notifications. Separate addresses with a semi-colon.


* * *
  * **Display name:** Email notification sender
  * **Settings key code name:** EmailNotificationFromAddress
  * **Value:** notifications@localhost.local
  * **Notes:** The email address that custom email notifications will be sent from.


## Expose your custom configuration
In general, you can access objects of custom classes in Xperience using the [ObjectQuery API](/documentation/developers-and-admins/api/objectquery-api).
Our module holds information related to system configuration (email notifications). To access it in a **strongly typed manner** , with the principles of **encapsulation** and **separation of concerns** in mind, we recommend using the [Options pattern](https://learn.microsoft.com/en-us/aspnet/core/fundamentals/configuration/options?view=aspnetcore-8.0).
If you are new to the Options pattern, outside of [the official Microsoft documentation](https://learn.microsoft.com/en-us/aspnet/core/fundamentals/configuration/options?view=aspnetcore-8.0), you may find [this article](https://www.milanjovanovic.tech/blog/how-to-use-the-options-pattern-in-asp-net-core-7) or [this video](https://www.youtube.com/watch?v=wxYt0motww0) helpful in understanding its basic principles.
In a nutshell, we will:
  1. [Create an options model](#create-an-options-model) class to hold our configuration values.
  2. [Create an options “setup” class to populate the model](#map-configuration-to-the-options-model) from Xperience objects using the ObjectQuery API.
  3. [Register the option classes](#register-the-options-setup-class) with the DI container.
  4. [Use DI to access the values](#utilize-custom-settings-values-in-your-application) and implement the desired functionality.


### Create an options model
In our example, the customer has defined two settings: **Email notification recipients** and **Email notification sender**.
Let’s create an `EmailNotificationOptions` class with two properties - one string value per setting.
Place your file in the _TrainingGuides.Web/Features/EmailNotifications_ folder. If you are following along using [our Training guides repository](https://github.com/Kentico/xperience-by-kentico-training-guides), you can place all the files we’ll create in this folder.
C#
**EmailNotificationOptions.cs**
Copy
```
namespace TrainingGuides.Web.Features.EmailNotifications;
public class EmailNotificationOptions
{
    public string SenderAddress { get; set; } = string.Empty;

    public string RecipientAddresses { get; set; } = string.Empty;
}
```

### Map configuration to the options model
Next, create an `EmailNotificationOptionsSetup` class that implements the `IConfigureOptions<TOptions>` interface, and pass in the `EmailNotificationOptions` model class.
Both email notification settings are objects of the `GlobalSettingsKeyInfo` class from [earlier in this series](/guides/development/customizations-and-integrations/create-basic-module#create-data-class).
Retrieve global settings with the generic `IInfoProvider`, using [ObjectQuery API extension methods](/documentation/developers-and-admins/api/objectquery-api) to filter your query based on their **Settings key code name**.
[![Email notification recipients ‘Settings key code name’ in administration](/docsassets/guides/access-custom-global-configurations/email-notification-recipients-codename.png)](/docsassets/guides/access-custom-global-configurations/email-notification-recipients-codename.png)
C#
**EmailNotificationOptionsSetup.cs**
Copy
```
using CMS.DataEngine;
using Microsoft.Extensions.Options;
using TrainingGuides.ProjectSettings;

namespace TrainingGuides.Web.Features.EmailNotifications;

public class EmailNotificationOptionsSetup : IConfigureOptions<EmailNotificationOptions>
{
    // Settings key code names of the two email notification settings.
    private const string FROM_ADDRESS_SETTINGS_KEY = "EmailNotificationFromAddress";
    private const string TO_ADDRESSES_SETTINGS_KEY = "EmailNotificationToAddresses";

    // Provide access to the objects of GlobalSettingsKeyInfo class using the generic IInfo provider and dependency injection.
    private readonly IInfoProvider<GlobalSettingsKeyInfo> globalSettingsKeyInfoProvider;

    public EmailNotificationOptionsSetup(IInfoProvider<GlobalSettingsKeyInfo> globalSettingsKeyInfoProvider)
    {
        this.globalSettingsKeyInfoProvider = globalSettingsKeyInfoProvider;
    }

    // Implement the Configure method of the IConfigureOptions interface.
    public void Configure(EmailNotificationOptions options)
    {
        // Retrieve GlobalSettingsKeyInfo objects
        // with a 'EmailNotificationFromAddress' or 'EmailNotificationToAddresses' code name
        // using ObjectQuery API.
        var emailSettings = globalSettingsKeyInfoProvider.Get()
            .WhereEquals(nameof(GlobalSettingsKeyInfo.GlobalSettingsKeyName), FROM_ADDRESS_SETTINGS_KEY)
            .Or()
            .WhereEquals(nameof(GlobalSettingsKeyInfo.GlobalSettingsKeyName), TO_ADDRESSES_SETTINGS_KEY)
            .GetEnumerableTypedResult();

        // Filter out sender address and recipient addresses and store them in variables respectively.
        var fromAddress = emailSettings.FirstOrDefault(s => s.GlobalSettingsKeyName == FROM_ADDRESS_SETTINGS_KEY);
        var toAddresses = emailSettings.FirstOrDefault(s => s.GlobalSettingsKeyName == TO_ADDRESSES_SETTINGS_KEY);

        // Populate the EmailNotificationOptions model.
        options.SenderAddress = fromAddress?.GlobalSettingsKeyValue ?? string.Empty;
        options.RecipientAddresses = toAddresses?.GlobalSettingsKeyValue ?? string.Empty;
    }
}
```

### Register the options setup class
Now, you need to tell your application to use your new custom class. Register the setup class and associated model with the DI container by calling `builder.Services.ConfigureOptions<EmailNotificationOptionsSetup>();` before application build.
Our [Training guides repository](https://github.com/Kentico/xperience-by-kentico-training-guides/blob/finished/src/TrainingGuides.Web/ServiceCollectionExtensions.cs) uses a static `ServiceCollectionExtensions` class to register all services.
If you are working in this repo or you extract service registrations in your project in a similar way, there are two steps:
  1. Add a new static method to register options (`AddTrainingGuidesOptions` method below).
C#
**ServiceCollectionExtensions.cs**
Copy
```
...
using TrainingGuides.Web.Features.EmailNotifications;

namespace TrainingGuides.Web;

public static class ServiceCollectionExtensions
{
    public static void AddTrainingGuidesServices(this IServiceCollection services)
    {
        ...
    }

    // New static method to register options.
    public static void AddTrainingGuidesOptions(this IServiceCollection services)
    {
        // Register your custom options setup class with the DI container.
        services.ConfigureOptions<EmailNotificationOptionsSetup>();
    }
}
```

  2. Call the new method in _Program.cs_ on application start.
C#
**Program.cs**
Copy
```
...

builder.Services.AddTrainingGuidesServices();
// new method call
builder.Services.AddTrainingGuidesOptions();
...

var app = builder.Build();

app.InitKentico();
...
```



## Utilize custom settings values in your application
Thanks to the options pattern, you can now easily access your configuration anywhere in your code where dependency injection is available.
Choose any of the [options interfaces](https://learn.microsoft.com/en-us/aspnet/core/fundamentals/configuration/options?view=aspnetcore-8.0#options-interfaces) that best fits your use case and application lifecycle.
For example, `IOptions<TOptions>` is a singleton service that holds a snapshot of the settings at the time of its first construction. It will work well in cases where the configuration values do not change during the application’s life.
In other scenarios, it may be better to take advantage of a scoped service, `IOptionsSnapshot<TOptions>`, which re-evaluates the options every time it’s constructed.
`IOptionsMonitor<TOptions>`, as the name suggests, is a suitable choice if you need a singleton service to monitor values that you expect to change continuously.
Find the full list of options interfaces in the [official Microsoft documentation](https://learn.microsoft.com/en-us/aspnet/core/fundamentals/configuration/options?view=aspnetcore-8.0#options-interfaces).
### Build a notification service
Let’s build a service that will send notifications to your configured email addresses.
C#
**IEmailNotificationService.cs**
Copy
```
namespace TrainingGuides.Web.Features.EmailNotifications;
public interface IEmailNotificationService
{
    Task SendEmailAsync(string subject, string message);
}
```

C#
**EmailNotificationService.cs**
Copy
```
using CMS.EmailEngine;
using Microsoft.Extensions.Options;

namespace TrainingGuides.Web.Features.EmailNotifications;

public class EmailNotificationService : IEmailNotificationService
{
    private readonly IEmailService emailService;

    // Use DI to access your configuration options
    private readonly IOptionsMonitor<EmailNotificationOptions> emailNotificationOptions;
    private readonly IOptionsMonitorCache<EmailNotificationOptions> emailNotificationOptionsCache;

    public EmailNotificationService(IEmailService emailService,
        IOptionsMonitor<EmailNotificationOptions> emailNotificationOptions,
        IOptionsMonitorCache<EmailNotificationOptions> emailNotificationOptionsCache)
    {
        this.emailService = emailService;
        this.emailNotificationOptions = emailNotificationOptions;
        this.emailNotificationOptionsCache = emailNotificationOptionsCache;
    }

    public async Task SendEmailAsync(string subject, string message)
    {
        // Clear the options cache to ensure we always get the latest data when settings have changed.
        emailNotificationOptionsCache.Clear();

        var msg = new EmailMessage()
        {
            // Assign the email sender based on the configuration.
            From = emailNotificationOptions.CurrentValue.SenderAddress,

            // Assign email recipients based on the configuration.
            Recipients = emailNotificationOptions.CurrentValue.RecipientAddresses,

            Priority = EmailPriorityEnum.Normal,

            Subject = subject,

            Body = message,
        };
        await emailService.SendEmail(msg);
    }
}
```

Register your service.
C#
**ServiceCollectionExtensions.cs**
Copy
```
...
using TrainingGuides.Web.Features.EmailNotifications;

namespace TrainingGuides.Web;

public static class ServiceCollectionExtensions
{
    public static void AddTrainingGuidesServices(this IServiceCollection services)
    {
        services.AddSingleton<IHttpRequestService, HttpRequestService>();
        ...
        // Register EmailNotificationService as a singleton service.
        services.AddSingleton<IEmailNotificationService, EmailNotificationService>();
    }
    ...
}
```

Notice that we use `IOptionsMonitor<TOptions>` to inject notification options. This way, we can monitor the settings while saving resources using a singleton service.
The options are cached. For simplicity, we clear the cache each time the `SendEmailAsync` method fires so that we always have fresh data. A more advanced approach example could be implementing another global handler that watches the settings keys.
Because the `IOptionsMonitor<TOptions>` is a singleton, we registered our `EmailNotificationService` in the same way.
### Watch your settings work in practice
To see our email notifications based on custom global settings at work, let’s generate a notification every time a new user is created in the system.
We will achieve this by creating a [custom object handler (module class)](/documentation/developers-and-admins/customization/handle-global-events/handle-object-events) and assign it to the `UserInfo` class.
C#
**EmailNotificationHandlerModule.cs**
Copy
```
using CMS;
using CMS.Core;
using CMS.DataEngine;
using CMS.Membership;
using TrainingGuides.Web.Features.EmailNotifications;

[assembly: RegisterModule(typeof(EmailNotificationHandlerModule))]

namespace TrainingGuides.Web.Features.EmailNotifications;
public class EmailNotificationHandlerModule : Module
{

    private IEmailNotificationService? emailNotificationService;

    public EmailNotificationHandlerModule() : base("EmailNotificationHandler")
    { }

    // Initialization code that is executed when the application starts.
    protected override void OnInit(ModuleInitParameters parameters)
    {
        base.OnInit();

        emailNotificationService = parameters.Services.GetRequiredService<IEmailNotificationService>();

        // Assign a handler to the Insert.After event for the UserInfo class.
        // This event occurs after a new user object is created.
        UserInfo.TYPEINFO.Events.Insert.After += User_InsertAfter;
    }

    private void User_InsertAfter(object? sender, ObjectEventArgs e)
    {
        if (e.Object is not UserInfo user)
        {
            return;
        }

        // Send the email using our Email notification service.
        emailNotificationService?.SendEmailAsync($"New user created ({user.Email})", $"New user inserted with ID {user.UserID}, email {user.Email}, guid {user.UserGUID}");
    }
}
```

You may have noticed we are using the `parameters.Services.GetRequiredService` method rather than dependency injection to access the `EmailNotificationService`.
We recommend this practice in module classes due to the nature of the Xperience application lifecycle. The initialization order of modules during application startup is not guaranteed, and it is possible that when your module initializes, DI will not be available yet.
See more [information and examples in our documentation](/documentation/developers-and-admins/customization/run-code-on-application-startup).
Build and run your solution. Navigate to the Xperience administration and add a new user. If you entered valid email addresses in the **Project settings** , the recipients should receive an email notification.
Alternatively, you can place a breakpoint in your application to see that your custom global settings are propagating in your application on user creation.
Your browser does not support the video tag. 
Try changing the email addresses and creating another user. If you followed our example and used the `IOptionsMonitor<TOptions>` options interface, you should see the changes take effect without restarting your app.
## What’s next
In this and the [previous guide](/guides/development/customizations-and-integrations/create-basic-module), we talked about utilizing modules in Xperience by Kentico to implement custom global (channel-agnostic) settings.
Continue with this series’s [next two guides](/guides/development/customizations-and-integrations/add-channels-to-module) to learn how to implement custom settings applicable per website channel.
![]()
[]()[]()
