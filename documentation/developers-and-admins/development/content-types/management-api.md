---
source: https://docs.kentico.com/documentation/developers-and-admins/development/content-types/management-api
scrape_date: 2026-01-26
---

  * [Home](/documentation)
  * [Developers and admins](/documentation/developers-and-admins)
  * [Development](/documentation/developers-and-admins/development)
  * [Content types](/documentation/developers-and-admins/development/content-types)
  * Content type management API 


# Content type management API
**Preview feature**
The Xperience by Kentico management API and MCP server are currently in preview mode. Expect changes in the functionality, potentially including breaking changes.
Feel free to try out the features, for example using the sample _Dancing Goat_ [project template](/documentation/developers-and-admins/installation). You can share your feedback directly with the Kentico [Product team](https://roadmap.kentico.com/).
The Xperience by Kentico management API provides endpoints that allow retrieval and management (Create, Read, Update, Delete) of objects within the system. At this time, the API allows:
  * Retrieval and management of [content types](/documentation/developers-and-admins/development/content-types) and [reusable field schemas](/documentation/developers-and-admins/development/content-types/reusable-field-schemas)
  * Retrieval of information about [UI form components](/documentation/developers-and-admins/customization/extend-the-administration-interface/ui-form-components) and [data types](/documentation/developers-and-admins/customization/field-editor/data-type-management) for fields


This enables automated [content modeling](/guides/architecture/content-modeling) workflows, accelerates migration scenarios, and simplifies integration with external systems that need to manage your project’s content model.
The management API is designed to be used via **AI tools**. Kentico provides a [Model Context Protocol (MCP)](https://modelcontextprotocol.io/docs/getting-started/intro) server that allows AI clients to easily discover and use the tools provided by the API.
## Enable the management API
**Only for local development purposes**
The management API is only intended for use on local development instances. It currently only provides basic authentication and no authorization options for various types of operations.
Do **not** enable the management API for production deployments or other publicly available instances.
To enable the content management API for a development project:
  1. Install the [Kentico.Xperience.ManagementApi](https://www.nuget.org/packages/Kentico.Xperience.ManagementApi) NuGet package into your Xperience project. 
     * Kentico.Xperience.ManagementApi is currently a **prerelease** package.
  2. Adjust your application’s startup code in _Program.cs_ : 
    1. Call the `AddKenticoManagementApi()` extension method on your application’s service collection. 
       * Adds controllers used by the management API. Also registers and configures services for authentication, authorization, [Swagger generation](https://swagger.io/tools/swagger-codegen/), and API versioning. If you already configure such services, place the _AddKenticoManagementApi()_ call after your configuration. Most of the configuration is scoped only to the management API endpoints, but there are exceptions (see the method’s remarks in your IDE for details).
    2. In the method’s `ManagementApiOptions` parameter, set the `Secret` property to a string with at least 32 characters. 
       * The secret is required to authenticate all requests to the management API endpoints, and will be used in the configuration of your management API [MCP server](#management-api-mcp-server).
    3. Add the following to the application’s [middleware pipeline](/documentation/developers-and-admins/development/website-development-basics/configure-new-projects): 
       * `UseAuthentication()` – adds authentication middleware. Must be called **before** _UseKentico()_.
       * `UseKenticoManagementApi()` – adds middleware required for the management API.
       * `UseAuthorization()` – adds authorization middleware. Must be called **after** _UseKentico()_.


C#
**Program.cs**
Copy
```
using Microsoft.AspNetCore.Builder;

using Kentico.Xperience.ManagementApi;
using Kentico.Web.Mvc;

// ...

var builder = WebApplication.CreateBuilder(args);

// ...

// Only enables the management API in the local development environment
if (builder.Environment.IsDevelopment())
{
    builder.Services.AddKenticoManagementApi(options =>
    {
       // Sets the secret required to authenticate all requests to the management API endpoints
       // Must be at least 32 characters
       options.Secret = "rrvpzpu18m5385tfw1h9j5luxton5vyv";
    });
}

// ...

var app = builder.Build();

app.InitKentico();

// ...

// Adds authentication middleware
app.UseAuthentication();

if (builder.Environment.IsDevelopment())
{
    // Adds middleware required for the management API
    app.UseKenticoManagementApi();
}

app.UseKentico();

// Adds authorization middleware
app.UseAuthorization();

// Maps routes used by Xperience by Kentico feature
app.Kentico().MapRoutes();
```

## Management API MCP server
Kentico provides an [MCP](https://modelcontextprotocol.io/docs/getting-started/intro) server that allows AI clients to easily discover and work with the management API. The MCP server is accessible to any IDE, agent, or tool that supports MCP.
The server **runs locally** on your development machine, and automatically generates tools for using the available management API endpoints.
**Node.js requirement**
To use the management API MCP server, you must have a [supported version of Node.js](/documentation/developers-and-admins/installation/system-requirements#task-specific-requirements) installed on your development machine.
To add the MCP server:
  * Use the [@kentico/management-api-mcp](https://www.npmjs.com/package/@kentico/management-api-mcp) npm package.
  * Set the following environment variables in the server’s configuration: 
    * `MANAGEMENT_API_URL` – the URL of your locally running Xperience application, followed by the _kentico-api/management_ path.
    * `MANAGEMENT_API_SECRET` – the secret configured when [enabling the management API](#enable-the-management-api) for your application.


See the documentation of your IDE for detailed information:
  * [Use MCP servers in VS Code](https://code.visualstudio.com/docs/copilot/customization/mcp-servers)
  * [MCP guide for Cursor](https://cursor.com/docs/context/mcp)
  * [Use MCP servers in Visual Studio](https://learn.microsoft.com/visualstudio/ide/mcp-servers?view=visualstudio)
  * [Connect to local MCP servers from Claude Desktop](https://modelcontextprotocol.io/docs/develop/connect-local-servers)


For example, to get the MCP server running in [VS Code](https://code.visualstudio.com/docs):
  1. Edit the `mcp.json` file of your workspace (located in the project’s _.vscode_ folder).
  2. Add the following server configuration:
JSON
**mcp.json**
Copy
```
{
  "servers": {
    "xperience-management-api": {
      "type": "stdio",
      "command": "npx",
      "args": [
        "@kentico/management-api-mcp@latest"
      ],
      "env": {
        "MANAGEMENT_API_URL": "http://localhost:5001/kentico-api/management",
        "MANAGEMENT_API_SECRET": "rrvpzpu18m5385tfw1h9j5luxton5vyv"
      }
    }
  }
}
```

  3. Adjust the environment variables for your project:
     * `MANAGEMENT_API_URL` – change the port number to match the URL where your Xperience application is running.
     * `MANAGEMENT_API_SECRET` – set the value to the secret configured when [enabling the management API](#enable-the-management-api).
  4. Run your Xperience application. See [Set up local hosting](/documentation/developers-and-admins/development/website-development-basics/set-up-local-hosting) for more information.
  5. Start the MCP server in your IDE.


The server should now be running and your AI agent configuration should now show the MCP server and display the available tools.
### Example prompts
Your AI agent will automatically use the MCP server’s tools when handling prompts related to content types and reusable field schemas. For example:
  * “List all content types containing a field with the ‘Content item asset’ data type.”
  * “Which content types use the ‘Product SKU’ reusable field schema?”
  * “Create a new content type named ‘Coffee cup’ with fields ‘Size’, ‘Color’ and with the ‘Product SKU’ reusable field schema.”


## Troubleshooting
### Certificate issues
In certain cases, you may encounter certificate related errors when using the MCP server with an application running under HTTPS (i.e., you have an HTTPS URL in the server’s `MANAGEMENT_API_URL` environment variable).
To resolve the issue, try setting the `NODE_OPTIONS` environment variable to `--use-system-ca`.
![]()
[]()[]()
