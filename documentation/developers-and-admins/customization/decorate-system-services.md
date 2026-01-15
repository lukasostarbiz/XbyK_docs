# Decorate system services
  * [ Copy page link ](documentation/developers-and-admins/customization/decorate-system-services#) | [Get HelpService ID](documentation/developers-and-admins/customization/decorate-system-services#)
Core MVC 5


[✖](documentation/developers-and-admins/customization/decorate-system-services# "Close page link panel") [Copy to clipboard](documentation/developers-and-admins/customization/decorate-system-services#)
The [decorator pattern](https://refactoring.guru/design-patterns/decorator) allows you to add new functionality to an existing object without altering its structure.
In Xperience by Kentico, the decorator pattern is suitable for customization various services. Consider using decorators when:
  * extending an existing implementation with new logic while maintaining the original behavior
  * replacing the implementation of specific members, leaving others unchanged


**Fully custom implementations**
If you wish to completely replace the implementation of a service class without reusing any of the logic, there is no point in using the decorator pattern. In this case, you can simply create and register your own implementation of the given interface.
## Decorate services via dependency injection
Xperience service classes registered within the ASP.NET Core application’s Inversion of Control (IoC) container can be decorated by registering a new implementation of the same service (interface) with a constructor dependency on itself. The service container is capable of resolving the previous implementation of the service from within the new one, allowing you to add custom logic to the service’s members.
The benefits of this approach are:
  * the service maintains its behavior, but with added custom logic (e.g., additional logging)
  * the new implementation gets used in place of the old one automatically across the system
  * to revert back to the previous implementation, you only need to stop registering the new implementation into the container
  * opens `sealed` and `internal` service implementations for modification
  * the IoC container always resolves the _previous_ implementation of a service (last registered relative to the implementation being resolved), allowing you to chain multiple decorators


**Registering multiple decorators for a service from the same assembly**
Service registrations from within the same assembly are nondeterministic – the order of registration can be different every time the application starts. If you need to chain multiple decorators from within a single assembly, you can do so via a [custom code-only module](documentation/developers-and-admins/customization/run-code-on-application-startup) class. Override the module’s `OnPreInit` method, and call `Service.Use<TService, TImplementation>()` for each of your implementation in the order of dependency. This ensures deterministic ordering for the IoC container.
### Example
The following example demonstrates decoration via dependency injection:
  1. Create a new implementation of the desired service. This example modifies `IEmailService` to adjust the email queue priority for specific emails.
     * For best practices about integrating custom code, see [Integrate custom code](documentation/developers-and-admins/customization/integrate-custom-code).
  2. Inject the same service via a constructor dependency. When instantiating your service, the container resolves its previous implementation.
C#
Copy
```
using CMS.EmailEngine;

public class CustomEmailService : IEmailService
{
    // Stores an instance of the default IEmailService implementation
    private readonly IEmailService defaultEmailService;

    // Resolves the previous implementation of the service using constructor DI
    public CustomEmailService(IEmailService defaultEmailService)
    {
        this.defaultEmailService = defaultEmailService;
    }
}
```

  3. Implement the methods prescribed by the interface. To keep the original behavior, call the equivalent methods from the injected service within the corresponding method implementations. Add custom logic as required.
C#
Copy
```
using System;
using System.Threading.Tasks;

using CMS;
using CMS.EmailEngine;

// Registers the custom implementation of IEmailService
[assembly: RegisterImplementation(typeof(IEmailService), typeof(Custom.CustomEmailService))]

namespace Custom
{
    public class CustomEmailService : IEmailService
    {
        // Stores an instance of the default IEmailService implementation
        private readonly IEmailService defaultEmailService;

        // Resolves the previous implementation of the service using constructor DI
        public CustomEmailService(IEmailService defaultEmailService)
        {
            this.defaultEmailService = defaultEmailService;
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
            // Sets the email priority to high if one of the recipients has a specific domain in their email address
            if (message.Recipients.Contains("@important-domain.com", StringComparison.OrdinalIgnoreCase))
            {
                message.Priority = EmailPriorityEnum.High;
            }

            // Adds the email message to the queue using the default email service
            Task emailTask = defaultEmailService.SendEmail(message);

            return emailTask;
        }
    }
}
```

  4. Register the service implementation within the application’s IoC container via the `RegisterImplementation` attribute.
C#
Copy
```
using CMS;
using CMS.EmailEngine;

// Registers the custom implementation of IEmailService
[assembly: RegisterImplementation(typeof(IEmailService), typeof(CustomEmailService))]

public class CustomEmailService : IEmailService
```



The system now uses your service implementation in place of the previous one. The service’s core behavior remains unchanged, but it also executes the additional logic when used.