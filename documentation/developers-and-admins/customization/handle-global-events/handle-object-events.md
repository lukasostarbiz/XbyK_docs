---
source: https://docs.kentico.com/documentation/developers-and-admins/customization/handle-global-events/handle-object-events
scrape_date: 2026-01-26
---

  * [Home](/documentation)
  * [Developers and admins](/documentation/developers-and-admins)
  * [Customization](/documentation/developers-and-admins/customization)
  * [Handle global events](/documentation/developers-and-admins/customization/handle-global-events)
  * Handle object events 


# Handle object events
Objects include all data structures used within Xperience, such as user accounts, taxonomies, contacts, or setting keys. By handling events that occur during the lifecycle of objects, you can add custom functionality to almost any part of the system. In the API, objects are represented by [Info classes](/documentation/developers-and-admins/api/database-table-api).
Within the context of event handling, objects do NOT include [content items](/documentation/business-users/content-hub/content-items), [website channel pages](/documentation/business-users/website-content), [emails](/documentation/business-users/digital-marketing/emails) or [headless items](/documentation/business-users/headless-content).
Use the [ContentItemEvents](/documentation/developers-and-admins/customization/handle-global-events/reference-global-system-events#contentitemevents), [WebPageEvents](/documentation/developers-and-admins/customization/handle-global-events/reference-global-system-events#webpageevents) or [HeadlessItemEvents](/documentation/developers-and-admins/customization/handle-global-events/reference-global-system-events#headlessitemevents) classes to assign handlers for events triggered for this type of content.
## Object event handlers with async support
For the most common object events that occur before or after an object is created, updated or deleted, you can implement handlers with support for both synchronous and asynchronous code. These handlers also allow you to use standard constructor dependency injection to get instances of required API services.
To add an object event handler, create a class implementing the `IInfoObjectEventHandler` interface, with a generic event class parameter that determines which event type is handled. You can modify the event class with an [Info class](/documentation/developers-and-admins/api/database-table-api) generic parameter to choose a specific object type to handle. For example:
C#
Copy
```
// Class declaration for a "before update" event handler for admin user objects (UserInfo)
public class UserBeforeUpdateHandler : IInfoObjectEventHandler<InfoObjectBeforeUpdateEvent<UserInfo>>
```

Omit the second generic parameter if you wish to handle the event for all types of objects in the system.
The following event classes are available for the first generic parameter:
Event |  Description |  Before handler |  After handler  
---|---|---|---  
Insert |  Occurs when a new object is created. |  `InfoObjectBeforeInsertEvent` |  `InfoObjectAfterInsertEvent`  
Update |  Occurs when the data of an existing object is updated. |  `InfoObjectBeforeUpdateEvent` |  `InfoObjectAfterUpdateEvent`  
Delete |  Occurs when an object is deleted. |  `InfoObjectBeforeDeleteEvent` |  `InfoObjectAfterDeleteEvent`  
`Before` events are triggered before the given action’s result is saved in the database, `After` events occur after the action successfully completes and is saved.
For every event handler, you need to implement **both** of the following methods:
  * `Handle` – invoked when the event is triggered by a synchronous operation, such as the `Set` method of the corresponding [InfoProvider API](/documentation/developers-and-admins/api/database-table-api#info-class-managers).
  * `HandleAsync` – invoked when the event is triggered by an async operation, such as the `SetAsync` method of the corresponding [InfoProvider API](/documentation/developers-and-admins/api/database-table-api#info-class-managers).


The methods provide an `infoObjectEvent` parameter of the corresponding event class, which you can use to access the Info object related to the event. In many cases, your implementations of the methods will share code, which you can extract into private methods. See the [Example](#example) to learn more.
### Register object event handlers
Event handlers for object events must be registered in your application’s service collection by calling the `AddInfoObjectEventHandler` extension method for [IServiceCollection](https://learn.microsoft.com/en-us/dotnet/api/microsoft.extensions.dependencyinjection.iservicecollection). For example, you can create a [custom module with initialization code](/documentation/developers-and-admins/customization/run-code-on-application-startup) and access `IServiceCollection` in the `OnPreInit` override.
C#
**Register an event handler**
Copy
```
using CMS;
using CMS.Core;
using CMS.DataEngine;

using CMS.ContentEngine;

[assembly: RegisterModule(typeof(ObjectEventModule))]

public class ObjectEventModule : Module
{
    public ObjectEventModule() : base(nameof(ObjectEventModule))
    {
    }

    // OnPreInit allows you to access IServiceCollection via ModulePreInitParameters
    protected override void OnPreInit(ModulePreInitParameters parameters)
    {
        base.OnPreInit(parameters);

        // Registers an object event handler
        parameters.Services.AddInfoObjectEventHandler<InfoObjectBeforeUpdateEvent<UserInfo>, UserBeforeUpdateHandler>();
    }
}
```

### Example
The following example demonstrates how to use event handlers to customize the behavior of a specific type of object.
The sample code extends the functionality of [taxonomies](/documentation/developers-and-admins/configuration/taxonomies) by adding a handler for the “after insert” event of `TaxonomyInfo`. This event occurs when a taxonomy is created. The custom logic automatically adds a default initial tag for every taxonomy.
  1. Open your Xperience solution in Visual Studio.
  2. Add a new class [within a custom assembly](/documentation/developers-and-admins/customization/integrate-custom-code) (_Class Library_ project) referenced by your project.
  3. Make the class implement `IInfoObjectEventHandler<InfoObjectAfterInsertEvent<TaxonomyInfo>>`.
  4. Define the `Handle` and `HandleAsync` methods:
C#
Copy
```
using System.Threading.Tasks;
using System.Threading;

using CMS.DataEngine;
using CMS.ContentEngine;

// Event handler triggered after a new taxonomy object is created
// Creates a default initial tag for every new taxonomy
public class TaxonomyInitialTagHandler : IInfoObjectEventHandler<InfoObjectAfterInsertEvent<TaxonomyInfo>>
{
    private readonly IInfoProvider<TagInfo> tagInfoProvider;

    // Uses constructor dependency injection to get instances of required services
    public TaxonomyInitialTagHandler(IInfoProvider<TagInfo> tagInfoProvider)
    {
        this.tagInfoProvider = tagInfoProvider;
    }

    // Invoked by the synchronous Set operation
    public void Handle(InfoObjectAfterInsertEvent<TaxonomyInfo> infoObjectEvent)
    {
        TaxonomyInfo taxonomy = infoObjectEvent.InfoObject;

        TagInfo tag = PrepareTag(taxonomy.TaxonomyID);

        // Creates the tag under the taxonomy for which the event was triggered
        tagInfoProvider.Set(tag);
    }

    // Invoked by the asynchronous SetAsync operation
    public async Task HandleAsync(InfoObjectAfterInsertEvent<TaxonomyInfo> infoObjectEvent, CancellationToken cancellationToken)
    {
        TaxonomyInfo taxonomy = infoObjectEvent.InfoObject;

        TagInfo tag = PrepareTag(taxonomy.TaxonomyID);

        // Asynchronously creates the tag under the taxonomy for which the event was triggered
        await tagInfoProvider.SetAsync(tag);
    }

    // Prepares a tag object under the specified taxonomy
    private TagInfo PrepareTag(int taxonomyId)
    {
        return new TagInfo
        {
            TagTaxonomyID = taxonomyId,
            TagOrder = 1,
            TagName = "InitialTag",
            TagTitle = "Initial tag",
            TagDescription = "This initial tag was created automatically by an event handler."
        };
    }
}
```

  5. Create a [module class](/documentation/developers-and-admins/customization/run-code-on-application-startup) in your custom assembly and override the `OnPreInit` method.
  6. Register the event handler by calling the `AddInfoObjectEventHandler` extension method for `IServiceCollection`.
C#
Copy
```
using CMS;
using CMS.Core;
using CMS.DataEngine;

using CMS.ContentEngine;

[assembly: RegisterModule(typeof(ObjectEventModule))]

public class ObjectEventModule : Module
{
    public ObjectEventModule() : base(nameof(ObjectEventModule))
    {
    }

    // OnPreInit allows you to access IServiceCollection via ModulePreInitParameters
    protected override void OnPreInit(ModulePreInitParameters parameters)
    {
        base.OnPreInit(parameters);

        // Registers the event handler
        parameters.Services.AddInfoObjectEventHandler<InfoObjectAfterInsertEvent<TaxonomyInfo>, TaxonomyInitialTagHandler>();
    }
}
```



To try out the functionality of the custom event handler, create a new taxonomy:
  1. Open the **Taxonomy** application in the Xperience administration.
  2. Select **New taxonomy**.
  3. Enter a **Taxonomy name**.
  4. Select **Save**.


The new taxonomy is created and the registered event handler automatically creates the _Initial tag_.
### Pass state between before and after events
The system allows you to pass custom state information from the `Before` handler to the `After` handler of an object event. Use the `State` property of the corresponding event class, which is available as the parameter of your handler’s `Handle` and `HandleAsync` methods.
The `State` property (`EventStateStore`) provides a key-value store, which you can manage using the `SetValue<TValue>` and `TryGetValue<TValue>` methods. State values stored in `Before` handlers are available for retrieval in `After` handlers.
C#
**Set state in Before event handlers**
Copy
```
public void Handle(InfoObjectBeforeInsertEvent<TaxonomyInfo> infoObjectEvent)
{
    // Stores a value under the "TagTitle" key
    infoObjectEvent.State.SetValue<string>("TagTitle", "Initial tag");
}
```

C#
**Get state in After event handlers**
Copy
```
public void Handle(InfoObjectAfterInsertEvent<TaxonomyInfo> infoObjectEvent)
{
    // Gets the State value stored under the "TagTitle" key in the before event
    string tagTitle;
    infoObjectEvent.State.TryGetValue<string>("TagTitle", out tagTitle);

    //...
}
```

**Unique State key values**
The values of keys stored in the `State` property are shared across all handlers registered for a given event class.
  * For example, all registered `InfoObjectAfterInsertEvent<UserInfo>` handlers use the same state pool.
  * Handlers for specific object types have their own separate state. For example, `InfoObjectBeforeInsertEvent<UserInfo>` and `InfoObjectBeforeInsertEvent<TaxonomyInfo>` handlers do not share state.
  * Handlers that target all object types (without a generic parameter) share state with all object-type-specific handlers. For example, state keys added in `InfoObjectBeforeUpdateEvent` handlers are available in `InfoObjectAfterUpdateEvent<UserInfo>` handlers.


Make sure you use sufficiently unique keys to avoid overwriting data between different handlers.
## Legacy object event handlers
In most cases, we recommend using [handlers with async support](#object-event-handlers-with-async-support) for object events (_Insert_ , _Update_ , _Delete_).
However, the system also continues to support the following classes that provide access to legacy object [event handling](/documentation/developers-and-admins/customization/handle-global-events):
  * `ObjectEvents` – events triggered for all object types
  * **< name>Info.TYPEINFO.Events** – events triggered only for a specific object type, for example: `UserInfo.TYPEINFO.Events`


See the [ObjectEvents reference](/documentation/developers-and-admins/customization/handle-global-events/reference-global-system-events#objectevents) for detailed information.
These events are triggered for both synchronous and async operations with the corresponding objects.
![]()
[]()[]()
