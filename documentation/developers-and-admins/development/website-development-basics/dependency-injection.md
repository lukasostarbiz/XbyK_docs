---
source: https://docs.kentico.com/documentation/developers-and-admins/development/website-development-basics/dependency-injection
scrape_date: 2026-01-26
---

  * [Home](/documentation)
  * [Developers and admins](/documentation/developers-and-admins)
  * [Development](/documentation/developers-and-admins/development)
  * [Website development basics](/documentation/developers-and-admins/development/website-development-basics)
  * Dependency injection 


# Dependency injection
When developing applications with Xperience, you may need to use service classes provided by the API. For example, services for managing users, handling digital marketing activities, and more.
To use a service, you first need to create a service instance. We recommend instantiating Xperience services via dependency injection.
If you are not familiar with the dependency injection design pattern in general, we recommend that you learn about it first. For example, you can start with the [Dependency Injection](https://docs.microsoft.com/en-us/aspnet/core/fundamentals/dependency-injection) article.
## Dependency injection design pattern
Dependency injection is a design pattern that is common to many programming languages and environments. Instead of instantiating a service instance inside a class constructor, you can pass the service instance into the constructor as a parameter. This way classes (e.g. controllers) do not need to obtain and configure the instance of a class they depend on.
The following code snippet shows a general example of the dependency injection design approach. Notice that the controller constructor accepts an instance of a service created by the dependency injection [provider](#dependency-injection-providers), without the need to instantiate explicitly.
C#
Copy
```
public class AccountController : Controller
{
    private readonly IMembershipActivityLogger mMembershipActivityLogger;

    public AccountController(IMembershipActivityLogger membershipActivityLogger)
    {
        mMembershipActivityLogger = membershipActivityLogger;
    }

    ...

}
```

Developing applications with the dependency injection pattern in mind provides the following benefits:
  * Less maintenance – changing object implementation is easier without strong coupling.
  * Easier testing – application logic can be tested in isolation from its dependent objects.
  * Loose coupling – classes are not directly dependent on one another.


## Dependency injection providers
ASP.NET Core Web applications come with a dependency injection provider developed by Microsoft. The provider is designed to serve the needs of the framework and most consumer applications. However, it can also be replaced with another third-party service container.
**Service container requirements**
To be compatible with Xperience, third-party service containers must support the features available in the minimum .NET version specified in the [current system requirements](/documentation/developers-and-admins/installation/system-requirements), including [Keyed services](https://learn.microsoft.com/en-us/aspnet/core/fundamentals/dependency-injection?#keyed-services).
Xperience adds all of its services into the application’s [IServiceCollection](https://docs.microsoft.com/en-us/dotnet/api/microsoft.extensions.dependencyinjection.iservicecollection) during the `IServiceCollection.AddKentico()` call (see [Configure new projects](/documentation/developers-and-admins/development/website-development-basics/configure-new-projects)). This approach is completely independent of the service provider used by the application.
For example, to substitute the default Microsoft service container with [Autofac](https://autofac.org/):
  1. Add the **Autofac.Extensions.DependencyInjection** NuGet package to your project.
  2. Call `IHostBuilder.UseServiceProviderFactory(new AutofacServiceProviderFactory())` when building the web application.
C#
**Program.cs**
Copy
```
var builder = WebApplication.CreateBuilder(args);

builder.Host.UseServiceProviderFactory(new AutofacServiceProviderFactory());

// Register services directly with Autofac here
builder.Host.ConfigureContainer<ContainerBuilder>(builder => builder.RegisterModule(new MyApplicationModule()));

var app = builder.Build();
```



The application now uses Autofac for dependency resolution.
![]()
[]()[]()
