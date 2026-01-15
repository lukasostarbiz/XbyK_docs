# Content Modeling MCP Server
  * Concept| [ Copy page link ](guides/architecture/content-modeling/content-modeling-mcp#) | [Get HelpService ID](guides/architecture/content-modeling/content-modeling-mcp#)
Core MVC 5


[✖](guides/architecture/content-modeling/content-modeling-mcp# "Close page link panel") [Copy to clipboard](guides/architecture/content-modeling/content-modeling-mcp#)
**Advanced license required**   
  
Features described on this page require the Xperience by Kentico **Advanced** license tier. 
Content modeling is a crucial yet time-consuming phase of any Xperience by Kentico project. Analyzing the results of a content audit, identifying key content types, and defining presentation components takes time and requires experience with Xperience. Yet these steps are critical for building a solid content model that determines the success of your entire project.
This guide introduces an AI-powered content modeling approach that streamlines content modeling tasks. By leveraging a structured workflow with built-in validation, you can reduce the time spent on prototyping a content model for Xperience application. The content modeling workflow takes away some of the mundane tasks, such as writing instructions for editors, and helps you quickly deliver project prototypes that are easy to iterate on.
For a comprehensive overview of content modeling concepts and strategies, see [Content modeling guide](guides/architecture/content-modeling/content-modeling-guide). If you’re new to Xperience by Kentico content modeling, start with [Content modeling basics](guides/architecture/content-modeling/xperience-content-modeling-basics).
**What you’ll learn:**
  * How to install the MCP server
  * How to use orchestration prompts and validation tools for each modeling phase
  * Ways to validate and visualize content structures for stakeholders
  * Best practices for efficient content model design in Xperience by Kentico


This approach helps agencies and implementation partners accelerate project delivery while maintaining high-quality content structures aligned with modern multi-channel needs. It helps you focus on building prototypes, not final outputs, so you can validate and refine your content model early.
## About the Content Modeling MCP Server
The Content Modeling MCP Server is part of [KentiCopilot](guides/development/kenticopilot), Kentico’s initiative to help developers effectively adopt AI in building Xperience by Kentico projects.
The server provides a deterministic, tools-based approach to content modeling that follows the **Orchestration prompt** + **Tool** pattern. Built on the [Model Context Protocol (MCP)](https://modelcontextprotocol.io/), an open standard that enables AI agents to dynamically discover and use external tools, the server offers reliable content modeling workflows with client-side LLM execution and server-side validation.
The MCP server works directly within your integrated development environment (IDE), allowing AI agents to access specialized content modeling capabilities without switching between tools or platforms.
For querying and retrieving information about existing content types in your Xperience application, see [Content type management API](documentation/developers-and-admins/development/content-types/management-api).
## Key features
The Content Modeling MCP Server provides the following capabilities:
  * **Orchestrated content modeling workflow** : Complete 5-phase workflow with built-in validation and transition logic.
  * **Phase-specific prompts** : Individual prompts for each workflow phase enabling granular control.
  * **Validation tools** : Deterministic tools that validate data structure and logic at each phase.
  * **Knowledge resources** : Comprehensive reference materials including field types, best practices, and examples.
  * **Multi-format output** : Generates structured JSON content models and markdown documentation (including Mermaid ERD diagrams).


## Install the MCP server
The Content Modeling MCP Server supports installation across multiple IDEs. Choose your preferred client below for setup:
Development environment |  Installation |  Documentation  
---|---|---  
VS Code |  [Install in VS Code](https://vscode.dev/redirect/mcp/install?name=kentico-cm-mcp&config=%7B%22type%22%3A%22http%22%2C%22url%22%3A%22https%3A%2F%2Fai.kentico.com%2Fcontent-model-mcp%22%7D) |  [VS Code MCP Official Guide](https://code.visualstudio.com/docs/copilot/customization/mcp-servers)  
Visual Studio |  Manual configuration required by adding a new tool in _Select tools_ in the Copilot chat window. **Required parameters:**  
_Server ID_ : kentico-cm-mcp  
_Type_ : HTTP/SSE  
_URL_ : “https://ai.kentico.com/content-model-mcp” |  [Visual Studio MCP Official Guide](https://learn.microsoft.com/en-us/visualstudio/ide/mcp-servers)  
Claude Desktop |  Manual configuration required. For the URL, use https://ai.kentico.com/content-model-mcp. |  [Claude Desktop Remote MCP Guide](https://modelcontextprotocol.io/docs/develop/connect-remote-servers)  
Cursor IDE |  [Install in Cursor](cursor://anysphere.cursor-deeplink/mcp/install?name=kentico-cm-mcp&config=eyAidXJsIjogImh0dHBzOi8vYWkua2VudGljby5jb20vY29udGVudC1tb2RlbC1tY3AiIH0) |  [Cursor MCP Official Guide](https://cursor.com/docs/context/mcp)  
### Recommended IDEs and AI models
The following table shows which IDE and AI model combinations work best with the Content Modeling MCP Server:
IDE |  Status |  Notes  
---|---|---  
Claude Desktop |  ✅ Fully supported |  Recommended for best experience  
Cursor |  ✅ Fully supported |  Recommended for best experience  
VS Code |  ⚠️ Inconsistent |  May experience inconsistent behavior with Claude Sonnet models. GPT-5 is stable.  
## Getting started
To begin using the Content Modeling MCP Server:
  1. [Install the Content Modeling MCP Server](guides/architecture/content-modeling/content-modeling-mcp#install-the-mcp-server) using the installation links for your preferred IDE.
  2. Open your MCP-compatible client (Claude Desktop, Cursor, VS Code with Copilot, etc.).
  3. Verify the Content Modeling MCP Server appears in your available prompts and tools.
  4. Start the content modeling workflow. The method varies by IDE, some might include the MCP server name in front of the `/start_content_modeling` prompt:


IDE/Platform |  How to invoke |  Additional steps  
---|---|---  
Claude Desktop |  Click _Add from kentico-cm-mcp_ |  Click _START HERE_ from the dropdown menu, then provide your project description into the _Enter prompt inputs_ pop-up  
Claude Code |  `/start_content_modeling` |  Provide your project description with the prompt  
Cursor |  `/start_content_modeling` |  Provide your project description in the pop-up that appears  
VS Code |  `/start_content_modeling` or via prompts menu |  Provide your project description in the pop-up, then select _Insert as text_  
**Providing context for better results**
The quality and specificity of your generated content model depend on the context you provide. Consider including:
  * Links to design files (for example, Figma designs - you can also add the [Figma Developer MCP](https://developers.figma.com/docs/figma-mcp-server/) for enhanced context extraction)
  * Project documentation, requirements, or specifications (attach files or reference them in your prompt)
  * Content audit results or existing content structures
  * Target audience and channel information


How you add context varies by IDE (attaching files, pasting links, referencing documents). The more relevant context you provide, the more tailored your content model will be. With minimal context, the MCP server generates a more general-purpose model.
## Prompts and tools reference
In the context of the MCP server, tools and prompts are capabilities that the MCP server exposes to connected clients (for example, GitHub Copilot, Claude, or Cursor). Each tool performs a specific function and can be called programmatically by the client when it needs to perform content modeling tasks.
### Orchestration prompts
The Content Modeling MCP Server provides orchestration prompts that guide you through the content modeling workflow:
Prompt name |  Description |  Parameters  
---|---|---  
start_content_modeling |  START HERE: Primary way to begin content modeling. Guides you through a complete 5-phase workflow from requirements gathering to final deliverables. |  **projectDescription** (string, optional): Project description and context  
content_modeling_1_requirements |  INTERNAL: Phase 1: Requirements gathering and approach selection |  **projectContext** (string, optional): Optional context about the project  
content_modeling_2_architect |  INTERNAL: Phase 2: Content type design with fields and metadata |  **approvedApproach** (string, optional): The approved approach from Phase 1 **requirementsSummary** (string, optional): Summary of requirements from Phase 1  
content_modeling_3_relationships |  INTERNAL: Phase 3: Relationship design between content types |  **contentTypes** (string, optional): List of content types from Phase 2  
content_modeling_4_pagebuilder |  INTERNAL: Phase 4: Page Builder templates and sections |  **approach** (string, optional): The approved approach **previousPhasesSummary** (string, optional): Content types and relationships from previous phases  
content_modeling_5_validator |  INTERNAL: Phase 5: Final validation and deliverable generation |  **completeModel** (string, optional): Complete content model from all previous phases  
### Validation tools
The following validation tools are used to verify the outputs of each content modeling phase. Validation tools are called when the agent completes the corresponding phase-specific work as described in the workflow prompts.
Tool Name |  Description |  Parameters  
---|---|---  
content_modeling_validate_requirements |  This tool validates outputs of _Requirements gathering_ phase: project requirements structure and basic fields. Call this tool ONLY after completing the Requirements gathering work (requirements gathering and approach selection) described in the phase-specific prompt, when the requirements are ready for validation. AI agent must provide the chosen approach based on decision logic in instructions. |  **projectDescription** (string): Project description and context **approach** (string): Chosen approach - “atomic” or “page-builder” (AI agent determines this) **channels** (string, optional): Target channels **teamExperience** (string, optional): Team experience level **contentVolume** (string, optional): Expected content volume **contentReuse** (string, optional): Content reuse needs  
content_modeling_validate_content_types |  This tool validates outputs of _Content type design phase_ : content type JSON structure and field definitions. Call this tool ONLY after completing the _Content Type Design_ work (content type design with fields) described in the phase-specific prompt, when the content types are ready for validation. |  **contentModelJson** (string): Complete content model JSON with contentTypes array to validate  
content_modeling_validate_relationships |  This tool validates outputs of _Relationship design_ phase: relationship definitions and cardinality rules. Call this tool ONLY after completing the _Relationship Design_ work (relationship design between content types) described in the phase-specific prompt, when the relationships are ready for validation. |  **contentModelJson** (string): Complete content model JSON with relationships array to validate  
content_modeling_validate_pagebuilder |  This tool validates outputs of _Page Builder design_ phase: Page Builder templates or skips for atomic approach. Call this tool ONLY after completing the _Page Builder Design_ work (Page Builder templates, sections, and widgets design) described in the phase-specific prompt, when the Page Builder components are ready for validation. |  **contentModelJson** (string): Complete content model JSON with pageBuilder object to validate  
content_modeling_final_validation |  This tool validates outputs of _Validation & final output_ phase: final validation of complete content model. Call this tool ONLY after completing the _Validation & Final Output_ work (final validation and output generation) described in the phase-specific prompt, when the complete model is ready for final validation. |  **completeModelJson** (string): Complete model JSON from all previous phases  
## Content modeling phases overview
The content modeling workflow in Xperience by Kentico is orchestrated by the `start_content_modeling` prompt, which guides users through all five phases automatically. Each phase is supported by a dedicated prompt and validation tool to ensure accuracy and completeness before moving to the next step:
**1. Requirements gathering & approach selection**
  * Gather project requirements, business goals, and editorial needs
  * Select the modeling approach ([atomic](guides/architecture/content-modeling/content-modeling-guide/design-atomic-content-model) vs. [page-builder](guides/architecture/content-modeling/content-modeling-guide/design-page-based-content-model)) based on requirements
  * Prompt: `content_modeling_1_requirements`
  * Validation tool: `content_modeling_validate_requirements`


**2. Content type design**
  * Define content types, fields, and metadata for each entity
  * Ensure field definitions and structures are consistent and complete
  * Prompt: `content_modeling_2_architect`
  * Validation tool: `content_modeling_validate_content_types`


**3. Relationship design**
  * Map relationships between content types, including cardinality and references
  * Validate relationship definitions and ensure correct linking strategies
  * Prompt: `content_modeling_3_relationships`
  * Validation tool: `content_modeling_validate_relationships`


**4. Page Builder templates & sections**
  * Design Page Builder components, such as page templates, Page Builder sections and widgets
  * Validate template structure or skip if using atomic modeling
  * Prompt: `content_modeling_4_pagebuilder`
  * Validation tool: `content_modeling_validate_pagebuilder`


**5. Final validation & deliverables**
  * Perform comprehensive validation of the complete content model
  * Generate deliverables for implementation
  * Prompt: `content_modeling_5_validator`
  * Validation tool: `content_modeling_final_validation`


**Next steps: Implement your content model**
After designing and validating your content model, you can implement it in your Xperience application using the [Content type management API](documentation/developers-and-admins/development/content-types/management-api). The Management API MCP server allows AI agents to create content types, add fields, and configure reusable field schemas based on your designed model.
## Example prompts
### Using the start_content_modeling prompt
The only way to begin content modeling is using the `start_content_modeling` prompt or the alternatives provided in the [Getting started](guides/architecture/content-modeling/content-modeling-mcp#getting-started) table:
  * “Use the start_content_modeling prompt to help me build a content model for a car dealership website”
  * “Guide me through the complete content modeling workflow for an commerce platform using start_content_modeling”