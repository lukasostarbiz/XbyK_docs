# Handle global events
  * [ Copy page link ](documentation/developers-and-admins/customization/handle-global-events#) | [Get HelpService ID](documentation/developers-and-admins/customization/handle-global-events#)
Core MVC 5


[✖](documentation/developers-and-admins/customization/handle-global-events# "Close page link panel") [Copy to clipboard](documentation/developers-and-admins/customization/handle-global-events#)
Global events allow you to execute custom code whenever specific actions occur within the system. Events can be raised as a result of both user interaction and the logic of the application itself (default or custom). When you assign a method as a handler for an event, the system automatically executes the code whenever the corresponding action occurs.
For example, when a new page is added to your website, you can use an event handler to load the page’s data, send out the content by email, and use a third-party component to generate a PDF version of the page.
## Assign handlers to events
**Object event handlers**
Object events represent the most common actions that occur in the system. They are triggered when objects are created, updated or deleted. The Xperience API provides a newer approach for implementing and registering object event handlers, which supports both synchronous and asynchronous code.
See [Handle object events](documentation/developers-and-admins/customization/handle-global-events/handle-object-events) for detailed information.
For non-object global events, register handlers using code in the following format:
**< event class>.<event action>.<event type> += <handler method name>**
  * **Event class** – event classes are containers of events related to groups of functionality
  * **Event action** – represents a specific action that occurs within the system
  * **Event type** – determines when exactly the event takes place, typically `Before` or `After` the action that invokes it. Some actions only have one type: `Execute`


For example:
C#
Copy
```
WebPageEvents.UpdateDraft.After += Page_Update_After;
```

The event handlers provide parameters derived from `EventArgs`, which you can use to access data related to the action that occurred. The exact type of the parameter depends on the event.
For information about the available event classes, actions, event types and handler parameters, see: [Reference - Global system events](documentation/developers-and-admins/customization/handle-global-events/reference-global-system-events)
You need to register event handlers at the beginning of the application’s lifecycle (during application startup). See [Run code on application startup](documentation/developers-and-admins/customization/run-code-on-application-startup) for more information.
## Example
The following steps describe how to register methods as global event handlers:
  1. Open your project in Visual Studio.
  2. [Add custom initialization code](documentation/developers-and-admins/customization/run-code-on-application-startup) that handles the required events.
     * Add the code into a custom _Class Library_ project within your solution. See [Integrate custom code](documentation/developers-and-admins/customization/integrate-custom-code) for more information.


C#
Copy
```
using CMS;
using CMS.DataEngine;
using CMS.Websites;

// Registers the custom module into the system
[assembly: RegisterModule(typeof(CustomInitializationModule))]

public class CustomInitializationModule : Module
{
    // Module class constructor, the system registers the module under the name "CustomInit"
    public CustomInitializationModule()
        : base("CustomInit")
    {
    }

    // Contains initialization code that is executed when the application starts
    protected override void OnInit()
    {
        base.OnInit();

        // Assigns custom handlers to events
        WebPageEvents.Create.After += Page_Create_After;
        WebPageEvents.UpdateDraft.Before += Page_UpdateDraft_Before;
    }

    private void Page_Create_After(object sender, CreateWebPageLanguageVariantEventArgs e)
    {
        // Add custom actions here
    }

    private void Page_UpdateDraft_Before(object sender, UpdateWebPageDraftEventArgs e)
    {
        // Add custom actions here
    }
}
```

## Use events to create integrations
Since events allow you to execute custom code when important actions occur in the system, they can be leveraged to integrate Xperience with external services. For example, integrations can perform operations when content is updated, users submit forms, or errors are written to the event log.
You can implement integrations using either tightly-coupled (shared resources, high dependence between application components) or decoupled architectural patterns (independent components, asynchronous communication). For more information about the latter approach, see [Integrate with decoupled systems](documentation/developers-and-admins/integrate-with-decoupled-systems).