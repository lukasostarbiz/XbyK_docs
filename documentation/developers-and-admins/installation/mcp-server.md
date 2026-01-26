---
source: https://docs.kentico.com/documentation/developers-and-admins/installation/mcp-server
scrape_date: 2026-01-26
---

  * [Home](/documentation)
  * [Developers and admins](/documentation/developers-and-admins)
  * [Installation](/documentation/developers-and-admins/installation)
  * Documentation MCP server 


# Documentation MCP server
The Xperience by Kentico Documentation MCP Server allows AI agents to securely access complete and up-to-date documentation, including guides and API examples. The MCP server provides guidance for all areas of Xperience by Kentico, for example [content management](/documentation/business-users/content-hub), [digital marketing](/documentation/developers-and-admins/digital-marketing-setup), or [digital commerce](/documentation/developers-and-admins/digital-commerce-setup), directly within your integrated development environment (IDE).
The MCP server is built on the [Model Context Protocol (MCP)](https://modelcontextprotocol.io/), an open standard that lets clients dynamically discover and query external data sources in real time. The server provides accurate, context-aware responses by combining semantic search with high-quality content retrieval from the official documentation in markdown format. Since the server syncs continuously with the published documentation, you’ll always get the most recent information and implementation details available.
## Install the MCP server
The MCP server supports quick installation across multiple IDEs. Choose your preferred client below for a streamlined setup:
Development environment |  Installation |  Documentation  
---|---|---  
VS Code |  [Install in VS Code](https://vscode.dev/redirect/mcp/install?name=kentico-docs-mcp&config=%7B%22type%22%3A%22http%22%2C%22url%22%3A%22https%3A%2F%2Fdocs.kentico.com%2Fmcp%22%7D) |  [VS Code MCP Official Guide](https://code.visualstudio.com/docs/copilot/customization/mcp-servers)  
Visual Studio |  Manual configuration required by adding a new tool in _Select tools_ in the Copilot chat window. **Required parameters:**  
_Type_ : HTTP/SSE  
_URL_ : “https://docs.kentico.com/mcp” |  [Visual Studio MCP Official Guide](https://learn.microsoft.com/en-us/visualstudio/ide/mcp-servers)  
Claude Desktop |  Manual configuration required. For the URL, use https://docs.kentico.com/mcp. |  [Claude Desktop Remote MCP Guide](https://modelcontextprotocol.io/docs/develop/connect-remote-servers)  
Cursor IDE |  [Install in Cursor](cursor://anysphere.cursor-deeplink/mcp/install?name=kentico-docs-mcp&config=eyAiY29tbWFuZCI6ICJucHgiLCAiYXJncyI6IFsiLXkiLCAibWNwLXJlbW90ZSIsICJodHRwczovL2RvY3Mua2VudGljby5jb20vbWNwIl0gfQ==) |  [Cursor MCP Official Guide](https://cursor.com/docs/context/mcp)  
### Add the MCP server to VS Code
Before adding the MCP server to your VS Code, make sure you have set up the [GitHub Copilot](https://code.visualstudio.com/docs/copilot/setup) and [installed](#install-the-mcp-server) the MCP server.
  1. Open GitHub Copilot in VS Code and [switch to Agent mode](https://code.visualstudio.com/docs/copilot/chat/chat-agent-mode).
  2. **Select** _Configure Tools_ in the right bottom corner of the chat window, next to the Send button.
  3. **Check** the box next to the _MCP Server: kentico-docs-mcp_.
  4. Confirm by selecting the **OK** button above the list.


The agent should now be able to use the [MCP server tools](#mcp-server-tools) to complete your query.
## The Xperience by Kentico MCP server endpoint
The Xperience by Kentico MCP Server is accessible to any IDE, agent, or tool that supports the Model Context Protocol (MCP). Any compatible client can connect to the following remote MCP endpoint: _https://docs.kentico.com/mcp_.
JSON
**Example JSON configuration**
Copy
```
{
  "kentico-docs-mcp": {
    "type": "http",
    "url": "https://docs.kentico.com/mcp"
  }
}
```

This URL is intended for use within a compliant MCP client via [Streamable HTTP](https://modelcontextprotocol.io/specification/2025-03-26/basic/transports#streamable-http), such as the recommended clients listed in the [installation](#install-the-mcp-server) section. It does not support direct access from a web browser and may return a _405 Method Not Allowed_ error if accessed manually.
If your client doesn’t support native remote MCP servers or if you prefer local configuration, you can use [mcp-remote](https://www.npmjs.com/package/mcp-remote) as a proxy. Configure MCP servers in `.mcp.json` at your project root.
JSON
**mcp-remote example**
Copy
```
{
  "mcpServers": {
    "remote-example": {
      "command": "npx",
      "args": [
        "mcp-remote",
        "https://docs.kentico.com/mcp"
      ]
    }
  }
}
```

## MCP server tools
In the context of the MCP server, tools are individual capabilities that the MCP server exposes to connected clients (for example GitHub Copilot, Claude, or Cursor). Each tool performs a specific function, such as searching or fetching, and can be called programmatically by the client when it needs information from the server.
The MCP server currently provides two tools that let AI agents access official Kentico documentation:
Tool name |  Description |  Parameters  
---|---|---  
kentico_docs_search |  Performs hybrid (vector + semantic + text-based) search against Xperience by Kentico official documentation |  question (string): The question to search for in the documentation  
kentico_docs_fetch |  Downloads and converts Kentico documentation HTML content to markdown format |  url (string): URL of the Kentico documentation page to read (must be from docs.kentico.com)  
### Example prompts
Your AI assistant should automatically use these tools for Xperience by Kentico-related topics. With both search and fetch capabilities, you can get quick answers or comprehensive deep dives. To ensure that it always consults the official documentation, you can add phrases like _search Kentico docs_ , _use XbyK docs_ , _fetch full Xperience docs_. For example:
  * “How do I create a custom page type in Xperience by Kentico? Search Kentico docs.”
  * “Are you sure this is the right way to implement custom form components in Xperience by Kentico? Search Kentico docs and fetch full doc.”
  * “Get me the full step-by-step tutorial for setting up multi-site management. Search Kentico docs and deep dive.”


## Troubleshooting
If the MCP Server isn’t responding as expected or the tools don’t appear in your environment, the following guidance can help resolve common issues quickly.
### Ensure AI clients use MCP tools
Some AI clients, such as Claude or GitHub Copilot, may not automatically use MCP tools when answering Kentico-related queries. To improve reliability, you can define a system prompt that explicitly tells the client to use the Xperience by Kentico MCP tools.
Here’s an example of a Cursor rule (a system prompt) that will cause the LLM to utilize _kentico-docs-mcp_ more frequently:
**Example system prompt (Cursor rule)**
## Querying Xperience by Kentico Documentation
You have access to MCP tools called `kentico_docs_search` and `kentico_docs_fetch`. These tools allow you to search through and fetch Kentico’s latest official documentation, and that information might be more detailed or newer than what’s in your training data set.
When handling questions around Xperience by Kentico, content management, digital marketing features, commerce functionality, custom development, page builders, form builders, email marketing, or any other Kentico-specific topics. Please use these tools for research purposes when dealing with specific/narrowly defined questions that may occur.
### Common Issues
Issue |  Solution  
---|---  
Connection errors |  Verify your network connection and that the server URL is correctly entered  
No results returned |  Try rephrasing your query with more specific Xperience by Kentico terms  
Tool not appearing in VS Code |  Restart VS Code or check that the MCP extension is properly installed  
HTTP status 405 |  Method not allowed happens when a browser tries to connect to the endpoint. Try using the MCP Server through VS Code GitHub Copilot or MCP Inspector instead.  
Fetch tool returns error |  Ensure the URL is from _docs.kentico.com_ domain only  
![]()
[]()[]()
