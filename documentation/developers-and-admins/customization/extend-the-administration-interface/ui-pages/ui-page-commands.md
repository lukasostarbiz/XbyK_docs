---
source: https://docs.kentico.com/documentation/developers-and-admins/customization/extend-the-administration-interface/ui-pages/ui-page-commands
scrape_date: 2026-01-22
---

  * [Home](/documentation)
  * [Developers and admins](/documentation/developers-and-admins)
  * [Customization](/documentation/developers-and-admins/customization)
  * [Extend the administration interface](/documentation/developers-and-admins/customization/extend-the-administration-interface)
  * [UI pages](/documentation/developers-and-admins/customization/extend-the-administration-interface/ui-pages)
  * UI page commands 


# UI page commands
UI page commands enable the execution of arbitrary code on the server back end from within the client-side application. Typical use cases for commands are CRUD operations on database objects (creating or deleting new users, logging events). 
The following diagram illustrates the basic lifecycle of a command: 
[![UI page command lifecycle](/docsassets/documentation/ui-page-commands/UIPageCommandHighlevel.png)](/docsassets/documentation/ui-page-commands/UIPageCommandHighlevel.png)
  1. A client component invokes a command (e.g., as part of a user interaction with some UI element).
  2. The admin application sends a request containing command metadata together with user-specified data to the server.
  3. The server invokes a designated handler function which processes the command.
  4. The handler function returns a response back to the client (including custom data that can be further processed by the client)


Commands are identified by their names specified in the client template. When invoked, the system uses the surrounding context (command name, page URL path) to match the command to its back-end handling method (located within the page’s [back-end definiton](/documentation/developers-and-admins/customization/extend-the-administration-interface/ui-pages) or its [page extender](/documentation/developers-and-admins/customization/extend-the-administration-interface/ui-pages/ui-page-extenders)).
## Define commands within client templates
### Register page command definitions
Within client templates, register page commands via `usePageCommand<TCommandResult>`:
JS
Copy
```
import { Button } from "@kentico/xperience-admin-components";
import { usePageCommand } from "@kentico/xperience-admin-base";

// Declares a client template component
export const MyTemplate = () => {

...

// Registers a page command via 'usePageCommand'. Returns a callback used to invoke the command.
// When registering commands, you must provide a command name. This name needs to be unique within the context of the given client template.
const { execute: invokeCommand } = usePageCommand<TCommandResult>("CommandName");

...

return (
    <div>
        // Invokes the command on button click, sends a request to the back end for processing
        <Button label="Get value" onClick={ () => invokeCommand() } />
    </div>
)
```

Substitute the `TCommandResult` generic parameter for a type used to hold data returned from the server. If the command is not intended to return data, set the generic to `void.`
When registering commands, you must provide a command name. This name needs to be unique within the context of the client template.
Additionally, you can provide the following parameters to the method:
  * `config` – a configuration object with the following optional properties:
Parameter  |  Type  |  Description   
---|---|---  
`executeOnMount` |  boolean  |  Indicates whether the command should be invoked automatically on [component mount](https://reactjs.org/docs/react-component.html#the-component-lifecycle). Defaults to `false`.   
`before` |  () => boolean | void  |  Custom action executed before the command is sent to the back end for processing. If the function returns `false`, the command will not execute.   
`after` |  (commandResult: TCommandResult)  
=> void  |  Action executed after a response is received from the back-end command handler.  JS Copy ```
const { execute: getData } = usePageCommand<TCommandResult>("CommandName", {
        after: (response) => {
		 	// the 'response' object is of the supplied 'TCommandResult' type
        	doSomething(response.data);
      }
});

```
  
`data` |  TCommandData  |  Contains user-specified data to be sent to the back end as part of the command invocation. If you need to send data as part of a command, also specify the `TCommandData` type as the second generic parameter of `usePageCommand`.  JS Copy ```
usePageCommand<TCommandResult, TCommandData>("CommandName", {
	data: TCommandData }
);

```
  
  * `dependencies` – an array of dependencies. The command only executes when an object in the dependency list changes. Identical to the functionality within [conventional React hooks](https://reactjs.org/docs/hooks-reference.html#conditionally-firing-an-effect).


When a command is invoked on the client, the admin app sends a request to the back end, which invokes a corresponding handler method [assigned to the command](#designate-command-handlers).
## Prepare and designate command handlers on the back end
### Designate command handlers
Commands called from the client are handled by designated methods. The handling API is **asynchronous and task-based**. Only asynchronous methods can be used as command handlers.
Decorate methods designated as command handlers with the `PageCommand` attribute. The attribute has the following optional parameters:
  * `CommandName` – the name of the command as invoked from the client template. If not specified, defaults to the name of the handling method.


C#
Copy
```
// Designates the 'SetLabel' method as the handler for the 'SetLabel' page command invoked from the client template
[PageCommand]
public async Task<ICommandResponse> SetLabel()
{
    // Command logic...
}

// Designates the 'CommandHandler' method as the handler for the 'DeleteAll' page command invoked from the client template
[PageCommand("DeleteAll")]
public async Task<ICommandResponse> CommandHandler()
{
    // Command logic...
}
```

### Formulate command response
The system provides an API that enables you to fluently compose responses from command handlers.
If you wish to make use the fluent API support, page command handler methods need to return an instance of `ICommandResponse`.
C#
Copy
```
[PageCommand]
public async Task<ICommandResponse> PageCommandHandler(ClientArguments args)
{
    // Instantiates a 'CommandResult' object that can be further modified by chaining provided extension methods
    return Response();
}
```

A response may contain all of the following:
  * status signalling command completion
  * information message displayed on the client containing additional information for users
  * data returned from the server that can be acted upon by the client


At minimum, a command handler returns an instance of the `ICommandResponse` created by one of the following methods:
  * `Response` – notifies the client of command completion.
  * `ResponseFrom<TResult>` – additionally carries data from the server. To be acted upon by the client. Substitute the `TResult` generic with an object representing the command response data on the server. 
C#
**Example - send data back to the client**
Copy
```
// Data object class representing the command handler response to the client
public class MyCommandResult
{
    public string StringData { get; set; }

    public int IntData { get; set; }

    public MyCommandResult(string stringData, int intData)
    {
        StringData = stringData;
        IntData = intData;
    }
}

public class MyPage : Page
{
    ...

    [PageCommand]
    public async Task<ICommandResponse<MyCommandResult>> MyCommand()
    {
        // Obtain the data...

        // Sends a response object with the data back to the client
        return ResponseFrom(new MyCommandResult(stringData, intData));
    }
}
```



Both methods return an instance of the `CommandResult` object that can be further modified by the following extensions (_Kentico.Xperience.Admin.Base_ namespace):
Method |  Description  
---|---  
`AddMessage` |  Displays a general message on the client after a response has been received. C# Copy ```
return Response().AddMessage("General message.");
```
  
`AddSuccessMessage` |  Displays a success message on the client after a response has been received. C# Copy ```
return Response().AddSuccessMessage("Success message.");
```
  
`AddInfoMessage` |  Displays an info message on the client after a response has been received. C# Copy ```
return Response().AddInfoMessage("Info message.");
```
  
`AddErrorMessage` |  Displays an error message on the client after a response has been received. C# Copy ```
return Response().AddErrorMessage("Error message.");
```
  
`AddWarningMessage` |  Displays a warning message on the client after a response has been received. C# Copy ```
return Response().AddWarningMessage("Warning message.");
```
  
`UseCommand` |  Invokes a command with the provided name on the client. Can be used, e.g., with [listing pages](/documentation/developers-and-admins/customization/extend-the-administration-interface/ui-pages/reference-ui-page-templates/listing-ui-page-template) to refresh the object listing after a command has executed on the back end. C# Copy ```
// Reloads the object grid on listing pages (e.g., to display changes in displayed data)
return Response().UseCommand('LoadData').AddInfoMessage('Objects modified.');
```
  
### Receive data from client commands
If you need to send data from the client as part of a command, the data needs to have a corresponding strongly-typed object representing them on the back end. This object then must be present as a parameter in the method handling the given page command.
The system automatically binds the received data to the object, allowing you to access it within the command handler:
C#
**Receive data from the client within a page command**
Copy
```
public class MyCommandArguments
{
    public int ExtremelyImportantNumber { get; set; }

    public string DistressingMessage { get; set; }
}

public class MyPage : Page
{
    ...

    [PageCommand]
    // Binds data received from the client to the MyCommandArguments object
    public async Task<ICommandResult> MyCommand(MyCommandArguments args)
    {
        // Use data received from the client
        args.DistressingMessage...
        args.ExtremelyImportantNumber...

        // Sends a response back to the client
        return Response().AddSuccessMessage("Command data received and acted upon.");
    }
}
```

### Redirect to another page after command execution
Instead of returning a response, the command handler method can redirect the user to another page in the admin UI. 
If you wish to redirect from a command handler, use the `NavigateTo` method. The method takes a parameter containing the URL of the page where the user is to be redirected. To obtain the URL of a different page in the admin UI, use `IPageLinkGenerator`. See [UI pages](/documentation/developers-and-admins/customization/extend-the-administration-interface/ui-pages#generate-urls-to-other-administration-pages) for more information about URL generation and navigation.
C#
Copy
```
// Service instance obtained via dependency injection
private readonly IPageLinkGenerator pageLinkGenerator;

protected Constructor(IPageLinkGenerator pageLinkGenerator)
{
    this.pageLinkGenerator = pageLinkGenerator;
}
...

[PageCommand]
public ICommandResult MyCommandHandler()
{
    // Navigates to the page represented by the 'TRedirectPage' type
    return NavigateTo(pageLinkGenerator.GetPath<TRedirectPage>());
}
```

### Cancellation token support
Page commands must be executed asynchronously. In asynchronous environments, it is conventional to use [cancellation tokens](https://docs.microsoft.com/en-us/dotnet/api/system.threading.cancellationtokensource) to enable cooperative cancellation between threads handling various long running background tasks. Page commands support the cancellation token pattern by default. The system automatically passes a cancellation token instance to command handlers that request it in their signature:
C#
**Obtain a cancellation token instance**
Copy
```
[PageCommand]
// The system passes a CancellationToken token instance to all handlers that request it in their signature
public async Task<ICommandResponse> MyCommand(MyCommandArguments args, CancellationToken ct)
{
    // Uses data received from the client and passes a cancellation token (if supported by the operation)
    var data = await FetchDataLongRunning(args.ClientStringData, args.ClientIntData, ct);

    // Sends a response back to the client
    return ResponseFrom(new ReturnData(data));
}
```

If the command handler calls logic that supports the cancellation token pattern (typically for long-running async operations), the token ensures all related threads terminate (e.g., in case the application needs to shut down) when the corresponding token source sends a request for cancellation. 
## Add permission checks to page commands
You can configure page commands to evaluate user permissions before executing. See [UI page permission checks](/documentation/developers-and-admins/customization/extend-the-administration-interface/ui-pages/ui-page-permission-checks) for more information.
## Example - page command and its handling method
The following code demonstrates a sample client template with a page command that fetches a string value from the back end and displays it in a label. The implementation of the command on the back end can be altered to be suitable for the specific page’s role. For example, you can return `DateTime` values, or the name of the current user.
JS
**Command definition within a client template**
Copy
```
import React, { useState } from "react";
import { Button } from "@kentico/xperience-admin-components";
import { usePageCommand } from "@kentico/xperience-admin-base";

// Type storing the properties of the client template
interface LayoutProps {
    readonly label: string;
}

// Type handling data returned from the server
interface ResponseResult {
    readonly label: string;
}

// Holds command name constants
const Commands = {
    GetString : 'GetString'
}

export const LayoutTemplate = ({ label } : LayoutProps ) => {

    // Declares a state variable used to store response data from the server
    const [labelValue, setLabelValue] = useState(label);

    const { execute: invokeCommand } = usePageCommand<ResponseResult>(Commands.GetString, {
        // After the command successfully executes, sets 'labelValue' to the value returned by the server
        after: (response) => {
            // the 'response' object is of the 'ResponseResult' type
            setLabelValue(response.label)
        }
    });

    return (
        <div>
            <h1>{labelValue}</h1>
            // Binds the command to an 'onClick' handler of a button component
            <Button label="Get value" onClick={() => invokeCommand()} />
        </div>
    );
}
```

The following code contains an example of a command handler on the back end. 
C#
**Command handler definition on the backend**
Copy
```
// Registers a handler for the 'GetString' page command that can be invoked form the client template
[PageCommand]
public async Task<ICommandResponse> GetString()
{
    return ResponseFrom(new ResponseResult { Label = DateTime.Now.ToString() })
            .AddInfoMessage("Data returned");
}
```

![]()
[]()[]()
