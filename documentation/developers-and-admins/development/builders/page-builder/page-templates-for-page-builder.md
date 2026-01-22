---
source: https://docs.kentico.com/documentation/developers-and-admins/development/builders/page-builder/page-templates-for-page-builder
scrape_date: 2026-01-22
---

  * [Home](/documentation)
  * [Developers and admins](/documentation/developers-and-admins)
  * [Development](/documentation/developers-and-admins/development)
  * [Builders](/documentation/developers-and-admins/development/builders)
  * [Page Builder](/documentation/developers-and-admins/development/builders/page-builder)
  * Page templates for Page Builder 


# Page templates for Page Builder
Page templates allow content editors to assign MVC views to individual pages without developer assistance.
Templates make it easy to choose and switch between page layouts, so they are suitable for creating pages with a predesigned or repeating structure, such as articles or landing pages. Developers define the layout of these pages by preparing page templates in the code and content editors then create new landing pages based on these templates.
Page templates can also be used for creating pages with [structured content](/guides/architecture/content-modeling/content-modeling-guide/store-content) (without Page Builder editable areas). Useful, for example, if you have multiple possible layouts for product or article pages, and want to allow content editors to choose a suitable layout for each individual page.
**Example page template use cases** :
  * Creating pages that have a similar structure or content, such as articles or landing pages.
  * Defining the Page Builder content once, then reusing it for other pages. For example, with the content reordered or modified for the new page.
  * Using different JavaScript, CSS, or __Layout.cshtml_ files for the pages of the same content type (and the structured content or Page Builder content defined in it).
  * Using pages with no content, just Page Builder sections that can be of a different layout for each page template.


## Page template types
  * **Default page templates** are defined in the code of your Xperience project. Default page templates specify the layout of a page without page content.
  * **Preset page templates** are created from a page that was created using a _default page template_. If the page contains [Page Builder content](/documentation/business-users/website-content/widgets-and-page-builder), such as widgets and sections, the preset template will contain a snapshot of this content on top of the layout defined in the default page template.
**Preset page templates do not store structured content**
If the page is using structured content (the data stored in the fields of the page), this content is not saved when saving a page as a preset template.


Developers define the page layout in form of **default** page templates. Content creators then create a page from a default page template, or define **preset** page templates that inherit the layout from the default page templates. See [Enable content editors to create pages based on page templates](#enable-content-editors-to-create-pages-based-on-page-templates).
## Enable content editors to create pages based on page templates
To enable content editors to create pages based on page templates, you need to complete the following steps:
  1. [Create a content type that supports page templates](#create-pages-that-support-page-templates)
  2. [Implement the page template](#implement-page-templates)
  3. [Register the page template within the system](#register-page-templates)
  4. Scope page templates using one of the following approaches: 
     * (Recommended) `ContentTypeNames` parameter of the [registration attribute](#register-page-templates)
     * [Page template filtering](/documentation/developers-and-admins/development/builders/page-builder/page-templates-for-page-builder/page-template-filtering)


## Create pages that support page templates
To use page templates on your pages, you need to create and configure suitable content types and adjust the corresponding logic.
Start by preparing a content type for your template-based pages:
  1. Open the **Content types** application.
  2. [Create a content type](/documentation/developers-and-admins/development/content-types) with the _Include in routing_ option enabled.
  3. Open a command line and [generate the content type’s code file](/documentation/developers-and-admins/api/generate-code-files-for-system-objects) so that you can work with the content type in your project.
  4. Open your project in Visual Studio and enable the [Page Builder](/documentation/developers-and-admins/development/builders/page-builder) for the content type.


Continue by modifying your [routing implementation](/documentation/developers-and-admins/development/routing/content-tree-based-routing/set-up-content-tree-based-routing). The action methods handling your routes need to return a `TemplateResult` object. Optionally, you may pass a custom model to the `TemplateResult` constructor to provide the template view with desired [custom data](#custom-model). The model can be any `Object`. The router automatically provides information about the page to be rendered when handling the request.
C#
**Handling page templates**
Copy
```
public ActionResult Index()
{
    // Custom processing logic

    // Leverages information provided by the router when serving the request
    // to retrieve the corresponding page. No need to specify the page to render.
    return new TemplateResult();
}
```

When developing pages that use page templates, you do NOT need to create a view for the page itself. Instead, the output of the page is based on the view of the selected template.
The next step now is to [implement](#implement-page-templates) the page template and [register](#register-page-templates) it in the system. Content editors can then select a page template, depending on the selected content type or predefined [page template filters](/documentation/developers-and-admins/development/builders/page-builder/page-templates-for-page-builder/page-template-filtering). If only one template is available for a given page, it is selected automatically.
## Implement page templates
On a basic technical level, page templates are HTML pages. The main step in the development of a page template is to create a full-page **view** that defines the page’s layout. 
You can also develop [page templates with properties](/documentation/developers-and-admins/development/builders/page-builder/page-templates-for-page-builder/page-template-properties), which allow content editors to customize the template appearance in the administration interface. For templates with configurable properties, you need to create an additional model class that represents the properties and passes their values to the view component. See [Page template properties](/documentation/developers-and-admins/development/builders/page-builder/page-templates-for-page-builder/page-template-properties) to learn more.
**Areas**
Page templates are designed to be used in the global scope and their code files must be placed in the application root of your project (not in an [Area](https://docs.microsoft.com/en-us/aspnet/core/mvc/controllers/areas)). Creating page templates in Areas may lead to unexpected behavior.
Use the following process to develop a page template:
  1. Create a view with code that defines the output of the page template according to general [MVC best practices](https://docs.microsoft.com/en-us/aspnet/core/mvc/overview). 
    1. The output must be a full HTML page, so the view must include the following:
       * Full HTML markup, including the _html, head_ and _body_ elements
       * Links to all necessary resources, such as stylesheets and scripts
       * Links to [Page Builder scripts and styles](/documentation/developers-and-admins/development/builders/page-builder/create-pages-with-editable-areas#load-page-builder-scripts-and-styles)
    2. Use layouts with the template view for any shared output code (based on your requirements, you can use your site’s main layout, a dedicated layout for page templates, etc.).
    3. If your templates contain any Page Builder [editable areas](/documentation/developers-and-admins/development/builders/page-builder/create-pages-with-editable-areas) and you wish to allow editors to transfer Page Builder content (widget and section configuration) between various templates, create templates with matching editable area identifiers. Consider this mainly for projects with multiple page templates for a single [content type](/documentation/developers-and-admins/development/content-types) (e.g., blog post, article, landing page). When switching templates, the system detects editable areas with matching identifiers and transfers all Page Builder content between them. If there is no match on the target template, all Page Builder content gets discarded.
    4. We recommend storing page template views in the _~/PageTemplates_ folder, and using a view name that matches the **identifier** [assigned to the template](#register-page-templates) prefixed with the underscore (‘`_`’) character.
  2. Register the page template into the system. See [Register page templates](#register-page-templates).


With this approach, the template’s view is automatically displayed using logic provided by the Xperience API.
The values of any [properties](/documentation/developers-and-admins/development/builders/page-builder/page-templates-for-page-builder/page-template-properties) defined for the template can be accessed in the view by using `TemplateViewModel<TPropertiesType>` as the model. The properties are then available via the model’s `Properties` member.
You can also use the `Page` property of `TemplateViewModel` to access the ID, [language](/documentation/developers-and-admins/configuration/languages), [content type](/documentation/developers-and-admins/development/content-types) and [channel](/documentation/developers-and-admins/configuration/website-channel-management) information of the page where the template is displayed.
cshtml
**Example**
Copy
```
@using Kentico.PageBuilder.Web.Mvc
@using Kentico.Content.Web.Mvc.PageBuilder

@model TemplateViewModel<CustomTemplateProperties>

@{
    int customNumber = Model.Properties.Number;
    int webPageId = Model.Page.WebPageItemID;
    int languageId = Model.Page.LanguageID
    string languageName = Model.Page.LanguageName;
    int contentTypeId = Model.Page.ContentTypeID;
    string contentTypeName = Model.Page.ContentTypeName;
    int channelId = Model.Page.WebsiteChannelID;
    string channelName = Model.Page.WebsiteChannelName;
}

<html>
<head>
    ...

    <page-builder-styles />
</head>
<body>
    ...

    <page-builder-scripts />
</body>
</html>
```

**Advanced page template example**
See the [Create versatile page templates](/guides/development/page-builder/create-versatile-templates-part-1) guide for a detailed step-by-step walkthrough showing how to create a complex page template, including handling of [template properties](/documentation/developers-and-admins/development/builders/page-builder/page-templates-for-page-builder/page-template-properties) and page fields.
### Add custom functionality
The basic implementation of a page template consists of only a view file (and possibly a properties class). You may need additional functionality, for example if you need to:
  * respond to the configuration of the template’s properties
  * perform interactions based on the page where the template is rendered
  * work with custom data
  * execute general business logic not suitable for views (e.g., database operations)


There are multiple ways how you can achieve your desired custom functionality:
  * If you just need to access your custom data within a template, utilize the [custom model](#custom-model) approach.
  * If you need to execute custom logic within the template, separate your logic into a dedicated [service class](#service-class) and then inject it directly into the template’s view.
Injecting a service into a view is generally considered a bad practice. Therefore do so only if it is really necessary. In case you just need to access your custom data within the view, use the [custom model](#custom-model) approach instead.
  * If you have reusable rendering logic, make use of [view components](#view-component).


#### Custom model
You can provide a page template with your custom data by passing the data as a model to the `TemplateResult` constructor. You may then use this data within the page template as you need.
To implement a custom model for a page template:
  1. Prepare your data and create the custom model. The model can be any `Object`.
  2. Pass the model to the `TemplateResult` constructor within the action method handling the given route.
C#
**Create model and pass it to TemplateResult**
Copy
```
public ActionResult Index()
{
    // Custom processing logic

    // Create the model. The model can be any object.
    var model = new CustomModel();

    // Pass the model to the TemplateResult
    return new TemplateResult(model);
}
```

  3. The template’s properties (if implemented) are stored in the template’s model (`TemplateViewModel<TPropertiesType>`), available via the `Properties` member.
  4. Access your custom data via the `GetTemplateModel<T>` method of the model and use it as you need. 
cshtml
**Access the model within the view**
Copy
```
@using Kentico.Content.Web.Mvc.PageBuilder

@model TemplateViewModel

@{
    var viewModel = Model.GetTemplateModel<CustomModel>();
}
```



#### Service class
Separate your logic into a dedicated service class when you need to reflect the context of its page or execute general business logic not suitable for views (e.g., database operations). You can then [inject](https://docs.microsoft.com/en-us/aspnet/core/mvc/views/dependency-injection) the service to the template’s view and call your code as required.
To implement a service class for a page template:
  1. Create the service class for your page template.
     * We recommend storing the service class in the  _~/PageTemplates/ <TemplateName>_ directory together with other files required by the page template. For reusable code shared across components, you can create a  _~/PageTemplates/Shared_ directory.
  2. Implement required logic within the service class. For example, you may wish to react to the fields of the page where the template is rendered.
     * The the ID, [language](/documentation/developers-and-admins/configuration/languages), [content type](/documentation/developers-and-admins/development/content-types) and [channel](/documentation/developers-and-admins/configuration/website-channel-management) information of the current page can be accessed via the `TemplateViewModel.Page` property. You can use this data to [retrieve the page](/documentation/developers-and-admins/development/content-retrieval/retrieve-page-content) and access its content fields.
     * The template’s properties (if implemented) are stored in the template’s model (`TemplateViewModel<TPropertiesType>`), available via the `Properties` member.
  3. Register the service within the application’s service container.
  4. Inject the service into the template’s view file using the `@inject` directive.
cshtml
Copy
```
@inject Mysite.MyTemplateService templateService
```

  5. Call the service as required.


#### View component
[View components](https://docs.microsoft.com/en-us/aspnet/core/mvc/views/view-components) can be used when you have reusable rendering logic that needs to interact with the application’s business layer.
To implement a view component for a page template:
  1. Create a view component and its partial view. 
     * We recommend storing view components related to the page template in the  _~/PageTemplates/ <TemplateName>_ directory together with other related files. For reusable code shared across components, you can create a  _~/PageTemplates/Shared_ directory.
  2. Implement the component’s `InvokeAsync` method. You can pass any required parameters from the template’s view. 
     * The ID, [language](/documentation/developers-and-admins/configuration/languages), [content type](/documentation/developers-and-admins/development/content-types) and [channel](/documentation/developers-and-admins/configuration/website-channel-management) information of the current page can be accessed via the `TemplateViewModel.Page` property. You can use this data to [retrieve the page](/documentation/developers-and-admins/development/content-retrieval/retrieve-page-content) and access its content fields.
     * The template’s properties (if implemented) are stored in the template’s model (`TemplateViewModel<TPropertiesType>`), available via the `Properties` member.
  3. Invoke the component from within the template’s view.


### Handle POST actions
If your page template needs to communicate with the server using POST actions, create a custom controller class containing the required methods and logic. We recommend storing the class in the  _~/PageTemplates/ <TemplateName>_ folder together with other files required by the page template.
**Endpoint security**
Consider whether the endpoints you design should only be accessible from the admin UI (edit mode of the website channel applications). See [Secure custom endpoints](/documentation/developers-and-admins/customization/secure-custom-endpoints) for more information.
Data of the current page rendered using the page template is by default not accessible in controller actions that handle POST requests. Such requests do not contain sufficient information to identify the page from which they originate. 
To access page data in POST actions, you need to include information about the current page into the data submitted by the corresponding form in the page template’s output – call the `Html.Kentico().PageData` extension method (or the method’s [Tag Helper alternative](/documentation/developers-and-admins/development/reference-tag-helpers)) within the given form tag in your page template view.
cshtml
Copy
```
@using Kentico.Web.Mvc
@using Kentico.PageBuilder.Web.Mvc

<form asp-controller="TemplateController" asp-action="HandlePost" id="form">

    ...

    @Html.Kentico().PageData()

    <input type="submit" value="Submit" />
</form>
```

The method renders a hidden form field that persists information about the current page. The page data can be retrieved via the `IWebPageDataContextRetriever` service and [content item API](/documentation/developers-and-admins/development/content-retrieval/retrieve-page-content) in the corresponding controller action.
Obtain an instance of the `IWebPageDataContextRetriever` service (using dependency injection) and call its `Retrieve` method. The returned object’s `WebPage` property contains basic information about the current page, which you can use to [retrieve the page object](/documentation/developers-and-admins/development/content-retrieval/retrieve-page-content).
C#
Copy
```
// An instance of IWebPageDataContextRetriever (e.g., obtained via dependency injection)
private readonly IWebPageDataContextRetriever retriever;

// Access the current page data
var page = retriever.Retrieve().WebPage;

// Access the properties of the page object
int webPageId = page.WebPageItemID;
int languageId = page.LanguageID
string languageName = page.LanguageName;
int contentTypeId = page.ContentTypeID;
string contentTypeName = page.ContentTypeName;
int channelId = page.WebsiteChannelID;
string channelName = page.WebsiteChannelName;
```

## Register page templates
Every page template needs to be registered into the system to be available. Register templates using the `RegisterPageTemplate` assembly attribute (available in the `Kentico.PageBuilder.Web.Mvc.PageTemplates` namespace).
If a page template does not require any additional services or view components, we recommend adding the registration attributes to a dedicated code file. This keeps your registrations organized. For example, you can create a file named **PageTemplateRegister.cs** in your project’s _~/PageTemplates_ folder and use it to register page templates.
When registering a page template, specify the following attribute parameters:
  * `Identifier` – the unique identifier of the template. We recommend using a unique prefix in your template identifiers to prevent conflicts when deploying templates to other projects, for example matching your company’s name.
  * `Name` – the name used to identify the template when displayed in the administration interface.
  * `PropertiesType` – only required for [templates with properties](/documentation/developers-and-admins/development/builders/page-builder/page-templates-for-page-builder/page-template-properties) . Specifies the `System.Type` of the template’s property model class.
  * `CustomViewName` – specifies the name and location of the view that defines the template’s output. If not set, the system searches for a corresponding __ <Identifier>.cshtml_ view in the _~/Views/Shared/PageTemplates_ folder (any period characters ‘.’ in the identifier are replaced by underscores ‘_’).
C#
**Template registration example**
Copy
```
[assembly: RegisterPageTemplate("CompanyName.MyTemplate", "My template", typeof(CustomTemplateProperties), "~/PageTemplates/_MyTemplate.cshtml")]
```



You can also set the following optional properties:
  * `ContentTypeNames` – a list of content type identifiers for which a page template is available. If not specified, a page template is available for all content types.
    * If you do not specify content types for templates using this parameter, you need to implement [page template filtering](/documentation/developers-and-admins/development/builders/page-builder/page-templates-for-page-builder/page-template-filtering).
  * `Description` – the description of the template displayed as a tooltip.
  * `IconClass` – the font icon class of the icon displayed as a thumbnail when selecting templates. 
**Administration font icons**
Page templates use the administration font icons set (‘ _xp-_ ’ prefix). To see a list of all icons available for page templates, navigate to the icon picker in **Content types** application → select a content type → **General** tab → **Icon** field.


## Add scripts and styles for page templates
When adding JavaScript and CSS styles required by your page templates, we recommend placing script and stylesheet files into sub-folders under:
  * **_~/wwwroot/PageBuilder/Public/PageTemplates/ <PageTemplate>_** – scripts and styles intended for the live site.
  * **_~/wwwroot/PageBuilder/Admin/PageTemplates/ <PageTemplate>_** – scripts and styles intended for the administration interface (when working with the page template in the Page Builder editing interface).


You can use sub-folders that match the identifiers of individual templates, or a _Shared_ sub-folder for assets used by multiple templates. Note that this recommendation only applies when using the default configuration of the bundling support provided by Xperience and may be different for your project. See [Bundle static assets of builder components](/documentation/developers-and-admins/development/builders/bundle-static-assets-of-builder-components).
**CSS notes**
  * Only use the specified directories to add **basic styles** that are required for the template to render correctly. Any **site-specific styles** that finalize the live site design of the template should be handled separately within the given site’s main stylesheet.
  * To avoid potential conflicts between styles from other third-party components, we recommend adding a unique prefix to your CSS classes and identifiers (for example `#CompanyName-mid-column`), or similar measures to ensure their uniqueness.


![]()
[]()[]()
