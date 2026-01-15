# Customize system providers
  * [ Copy page link ](documentation/developers-and-admins/customization/customize-system-providers#) | [Get HelpService ID](documentation/developers-and-admins/customization/customize-system-providers#)
Core MVC 5


[✖](documentation/developers-and-admins/customization/customize-system-providers# "Close page link panel") [Copy to clipboard](documentation/developers-and-admins/customization/customize-system-providers#)
Providers in the Xperience API are classes that allow the system to manipulate objects and perform various actions. The following list presents the providers that the system uses and their purpose:
  * **Info providers** – contain methods for working with specific types of objects stored in the database, such as users, contacts, and [custom entities](documentation/developers-and-admins/customization/object-types).
Xperience uses two distinct types of info provider classes.
**Dedicated providers**  
This type of provider consists of a dedicated class and interface implementation and is solely responsible for a single database entity – `UserInfoProvider` (with the `IUserInfoProvider` interface) responsible for `UserInfo` objects (from the _CMS_User_ database table), for example. These providers are being actively phased out, to be replaced by _generic providers_.
**Generic providers**  
Generic providers are instances of `IInfoProvider<TInfo>`, where _TInfo_ is the managed *Info class. We recommend using the generic provider pattern for all custom object types added to the system.
The info provider customization pattern differs depending on provider type. See [Providers based on I*InfoProvider interfaces](documentation/developers-and-admins/customization/customize-system-providers#providers-based-on-iinfoprovider-interfaces) for dedicated providers and [Providers based on IInfoProvider<TInfo>](documentation/developers-and-admins/customization/customize-system-providers#providers-based-on-iinfoprovidertinfo) for generic providers. For general overview of both types of providers, see [Database table API](documentation/developers-and-admins/api/database-table-api).
  * **File system providers** – allow you to access various file systems. For more information, see [Files API and CMS.IO](documentation/developers-and-admins/api/files-api-and-cms-io).
  * **BizFormItemProvider** – allows you to [retrieve](documentation/developers-and-admins/development/content-retrieval#retrieve-form-data) data submitted through [forms](documentation/business-users/digital-marketing/forms) and contains methods for basic management of form data. `BizFormItemProvider` is _not_ compatible with the Info provider customization approaches described on this page. For example, you may wish to adjust or extend how form data submitted by visitors is saved. For these scenarios, we recommend implementing handlers for [BizFormItemEvents events](documentation/developers-and-admins/customization/handle-global-events/handle-form-events).


By developing custom providers and using them instead of the standard ones, you can modify the behavior of the application (or a specific feature) according to your requirements.
**Customization best practices**
When developing custom functionality, we recommend placing your code files into a **separate Class Library project** (assembly), which is referenced by the main application. See [Integrate custom code](documentation/developers-and-admins/customization/integrate-custom-code) for details.
## Info provider customization
### Providers based on IInfoProvider<TInfo>
Generic providers must be customized via inheritance from `InfoProviderDecorator<TInfo>`. The class offers a set of virtual methods open to modification.
C#
**Generic provider customization example**
Copy
```
using CMS.DataEngine;

// Decorates a generic provider for objects of the 'MyInfo' type
public class MyInfoProviderDecorator : InfoProviderDecorator<MyInfo>
{
    public MyInfoProviderDecorator(IInfoProvider<MyInfo> decoratedProvider)
        : base(decoratedProvider)
    {
    }

    public override void Set(MyInfo info)
    {
        // Execute custom logic then set using the underlying implementation

        base.Set(info);
    }

    public override Task SetAsync(MyInfo info, CancellationToken? cancellationToken = null)
    {
        // Execute custom logic then set using the underlying implementation

        return base.SetAsync(info, cancellationToken);
    }

    public override void Delete(MyInfo info)
    {
        // Execute custom logic then delete using the underlying implementation

        base.Delete(info);
    }

    public override Task DeleteAsync(MyInfo info, CancellationToken? cancellationToken = null)
    {
        // Execute custom logic then delete using the underlying implementation

        return base.DeleteAsync(info, cancellationToken);
    }
}
 



 
 



 



 


```

With the custom logic in place, register the decorator class using the `RegisterCustomProvider` assembly attribute to replace it as the default for objects of the specified _Info_ class.
C#
**Register generic provider decorators**
Copy
```
[assembly: RegisterCustomProvider(typeof(MyInfoProviderDecorator))]

namespace Sample
{
    public class MyInfoProviderDecorator : InfoProviderDecorator<MyInfo>
    {
        // ...
    }
}
 



 


```

### Providers based on I*InfoProvider interfaces
While using [providers based on IInfoProvider<TInfo>](documentation/developers-and-admins/customization/customize-system-providers#providers-based-on-iinfoprovidertinfo) is strongly recommended for all custom providers, not all system providers currently support this customization pattern. Use the approach described in this section to customize such providers.
#### Write the custom code
Custom provider classes must **inherit from the original implementation**.
#### Register custom providers
Registering the customized provider replaces its default system implementation. To register custom providers, use the `RegisterCustomProvider` assembly attribute.
C#
**Custom provider registration**
Copy
```
[assembly: RegisterCustomProvider(typeof(UserInfoProviderDecorator))]
namespace Sample
{
    public class UserInfoProviderDecorator : UserInfoProvider
    {
        // Decoration logic...
    }
}
 


```

After the custom provider is registered, its implementation is automatically used when resolving the corresponding provider interface (`IUserInfoProvider` in the case of the example) via [dependency injection](documentation/developers-and-admins/development/website-development-basics/dependency-injection).
## Info object validation
When saving info objects to the database, the system validates object code name format and uniqueness. You can extend this validation by implementing info-specific rules via `IInfoValidator<TInfo>`.
  1. Create a new class that implements `IInfoValidator<TInfo>`. Substitute `TInfo` for the info object you want to validate.
  2. Implement the `Validate` method. The method must return `InfoValidationResult`. We recommend storing error resource strings in [resource files](documentation/developers-and-admins/customization/integrate-custom-code#store-application-resources-in-resource-files).
  3. Register the validator into the service container as a singleton using the `RegisterImplementation` assembly attribute.


C#
**Example - Custom validation**
Copy
```
using CMS;
using CMS.Core;
using CMS.DataEngine;

// Registers the validator implementation to the service container
[assembly: RegisterImplementation(typeof(IInfoValidator<MyInfo>), typeof(MyInfoValidator), Lifestyle = Lifestyle.Singleton)]

public class MyInfoValidator : IInfoValidator<MyInfo>
{
    public InfoValidationResult Validate(MyInfo info)
    {
        // Perform domain-specific validation and return a corresponding error
        if (!IsValid(info))
        {
            return new InfoValidationResult(nameof(MyInfo.MyField), "resource.key");
        };

        // Indicates successful validation
        return InfoValidationResult.Success;
    }

    private bool IsValid(MyInfo info)
    {
        // Validation logic...
    }
}
```

## Additional customization options
In addition to providers, you can also customize the following helper classes:
Helper class name |  Namespace |  Description  
---|---|---  
DirectoryHelper |  CMS.IO |  Manages directories in the file system.  
You can also use [global event handlers](documentation/developers-and-admins/customization/handle-global-events/reference-global-system-events) to customize the behavior of the system. Handlers allow you to execute custom code whenever a specific event occurs in the system, such as page or object changes.
## Code analyzer support
The _Kentico.Xperience.Core_ [NuGet package](documentation/developers-and-admins/development/website-development-basics/configure-new-projects/xperience-by-kentico-nuget-packages) contains analyzers that advocates best practices when customizing system providers. For info provider customization, the analyzer warns about the need to override the asynchronous counterpart of a synchronous method (or vice versa) if their custom type overrides one of the `InfoProviderDecorator<TInfo>` or `AbstractInfoBase<TInfo>` methods related to data modification. All Kentico-specific analyzer rules begin with the _KXA_ prefix.