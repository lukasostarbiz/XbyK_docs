# Widgets for Page Builder
  * [ Copy page link ](documentation/developers-and-admins/development/builders/page-builder/widgets-for-page-builder#) | [Get HelpService ID](documentation/developers-and-admins/development/builders/page-builder/widgets-for-page-builder#)
Core MVC 5


[✖](documentation/developers-and-admins/development/builders/page-builder/widgets-for-page-builder# "Close page link panel") [Copy to clipboard](documentation/developers-and-admins/development/builders/page-builder/widgets-for-page-builder#)
Page Builder widgets are reusable components that can be easily manipulated by content editors and other non-technical users. Widgets and the [Page Builder](documentation/developers-and-admins/development/builders/page-builder) feature give non-technical users more power and flexibility when adjusting page content, in addition to basic editing of text and images. By working with widgets, users can decide which components are placed on pages and where. For the list of widgets available in the system by default, check the [system widget reference](documentation/developers-and-admins/development/builders/page-builder/reference-default-page-builder-widgets).
For inspiration, explore the implementation of sample widgets on the [Dancing Goat site](documentation/developers-and-admins/installation).
On this page, you can find information about:
  * [Implementing widgets](documentation/developers-and-admins/development/builders/page-builder/widgets-for-page-builder#implement-widgets)
  * [Basic widgets](documentation/developers-and-admins/development/builders/page-builder/widgets-for-page-builder#basic-widgets)
  * [Widgets based on a view component](documentation/developers-and-admins/development/builders/page-builder/widgets-for-page-builder#widgets-based-on-a-view-component)
  * [Handling POST actions](documentation/developers-and-admins/development/builders/page-builder/widgets-for-page-builder#handle-post-actions)
  * [Registering widgets](documentation/developers-and-admins/development/builders/page-builder/widgets-for-page-builder#register-widgets)
  * [Adding scripts and styles for widgets](documentation/developers-and-admins/development/builders/page-builder/widgets-for-page-builder#add-scripts-and-styles-for-widgets)


## Implement widgets
On a basic level, widgets are pieces of HTML output placed within a suitable location in the page structure. You can develop two types of widgets:
  * [Basic widgets](documentation/developers-and-admins/development/builders/page-builder/widgets-for-page-builder#basic-widgets) – define their logic in a single partial view.
  * [Widgets based on a view component](documentation/developers-and-admins/development/builders/page-builder/widgets-for-page-builder#widgets-based-on-a-view-component) – define their logic in an ASP.NET Core [view component](https://docs.microsoft.com/en-us/aspnet/core/mvc/views/view-components). Recommended for more complex widgets that require non-trivial interactions with the application’s business layer.


In both cases, the widgets can contain configurable properties, which allow content editors to adjust the widget content or behavior directly in the administration interface. For widgets with properties, you need to create an additional model class that holds the properties data. See [Widget properties](documentation/developers-and-admins/development/builders/page-builder/widgets-for-page-builder/widget-properties) to learn more.
**Areas**
Widgets are designed to be used in the global scope and their code files must be placed in the application root of your Core project (not in an [Area](https://docs.microsoft.com/en-us/aspnet/core/mvc/controllers/areas)). Creating widgets in Areas may lead to unexpected behavior.
**Examples of widget development**
For a step-by-step guide showcasing the development of a simple widget, see [Example - Widget development](documentation/developers-and-admins/development/builders/page-builder/widgets-for-page-builder/example-widget-development).
For a detailed walkthrough showing how to create a more complex widget, see the [Define an advanced widgets](guides/development/page-builder/define-advanced-widget) guide.
## Basic widgets
Use the following process to develop a widget:
  1. Prepare a **partial view** that defines the output of the widget according to general [MVC best practices](https://docs.microsoft.com/en-us/aspnet/core/mvc/overview). 
     * We recommend storing widget views in the _~/Components/Widgets/ <WidgetName>_ folder and using a view name that matches the **identifier** assigned to the widget [upon its registration](documentation/developers-and-admins/development/builders/page-builder/widgets-for-page-builder#register-widgets) prefixed with the underscore (_‘_’_) character.
     * Alternatively, you can use any required view location or name, and then specify it when registering the widget.
  2. [Register the widget](documentation/developers-and-admins/development/builders/page-builder/widgets-for-page-builder#register-widgets) into the system.


When added via the Page Builder, the widget’s view is automatically displayed using logic provided by the Xperience API.
The values of any [properties](documentation/developers-and-admins/development/builders/page-builder/widgets-for-page-builder/widget-properties) defined for the widget can be accessed by using the `ComponentViewModel<TPropertiesType>` class as the model.
You can also use the `Page` property of `ComponentViewModel` to access the ID, [language](documentation/developers-and-admins/configuration/languages), [content type](documentation/developers-and-admins/development/content-types) and [channel](documentation/developers-and-admins/configuration/website-channel-management) information of the page where the widget is displayed.
cshtml
**Example**
Copy
```
@using Kentico.PageBuilder.Web.Mvc

@model ComponentViewModel<CustomWidgetProperties>

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

...
```

## Widgets based on a view component
The basic implementation of a widget consists of only a partial view (and possibly a [properties](documentation/developers-and-admins/development/builders/page-builder/widgets-for-page-builder/widget-properties) class). You may need additional logic, for example if you need to:
  * react to the configuration of the widget’s properties
  * perform interactions based on the page where the widget is rendered
  * execute general business logic not suitable for views (e.g., database operations)


For this purpose, you can develop widgets based on ASP.NET Core [view components](https://docs.microsoft.com/en-us/aspnet/core/mvc/views/view-components). Such widgets consist of a view component class and the corresponding partial view it renders.
Implementing a widget based on a view component follows this general process:
  1. Create a [view component](https://docs.microsoft.com/en-us/aspnet/core/mvc/views/view-components) for the widget.
     * We recommend storing view component files in the  _~/Components/Widgets/ <WidgetName>_ directory together with other files required by the widget. For reusable code shared across components, you can create a _~/Components/Shared_ directory.
  2. Implement the component’s `Invoke` or `InvokeAsync` method (the synchronous and asynchronous approaches are both supported, this choice depends solely on your requirements).
     * The system invokes the view component when the widget is inserted via Page Builder. As part of the rendering process, the system passes the widget’s [properties](documentation/developers-and-admins/development/builders/page-builder/widgets-for-page-builder/widget-properties) (the `ComponentViewModel` class) into the component’s `Invoke` method. For this reason, include the `ComponentViewModel` parameter in the method’s signature. For widgets with custom properties, you also need to specify the properties class as the generic parameter:
C#
Copy
```
// The signature of a view component's InvokeAsync method for widgets without custom properties
public async Task<IViewComponentResult> InvokeAsync(ComponentViewModel widgetModel)

// The signature of a view component's InvokeAsync method for widgets with custom properties
public async Task<IViewComponentResult> InvokeAsync(ComponentViewModel<TWidgetPropertiesClass> widgetModel)
```

  3. The return statement of the component’s `ComponentViewModeInvoke` method needs to specify the full relative path to the component’s partial view. For example:
C#
Copy
```
return View("~/Components/Widgets/MyWidget/_MyWidget.cshtml", model);
```

  4. Create any required view model classes used to pass data from the widget view component to the partial view according to general [ASP.NET Core MVC best practices](https://docs.microsoft.com/en-us/aspnet/core/mvc/overview).
     * We recommend storing widget models in the  _~/Components/Widgets/ <WidgetName>_ folder together with other files required by the widget.
     * For widgets with [configurable properties](documentation/developers-and-admins/development/builders/page-builder/widgets-for-page-builder/widget-properties), do not directly pass the property model to your widget views. Passing data to views is the responsibility of the widget’s view model. For this reason, we strongly recommend keeping the models separate.
  5. Prepare a **partial view** that defines the output of the widget.
     * We recommend storing widget views in the _~/Components/Widgets/ <WidgetName>_ folder together with other files used by the widget.
  6. [Register](documentation/developers-and-admins/development/builders/page-builder/widgets-for-page-builder#register-widgets) the widget in the system.


Access the current page
If you need to access the page where the widget is currently rendered, use the `Page` property of the `ComponentViewModel` class (from the component’s Invoke method). The property exposes further properties containing the ID, [language](documentation/developers-and-admins/configuration/languages), [content type](documentation/developers-and-admins/development/content-types) and [channel](documentation/developers-and-admins/configuration/website-channel-management) information of the page. You can use this data to [retrieve the page](documentation/developers-and-admins/development/content-retrieval/retrieve-page-content) and access its content fields.
C#
Copy
```
// Retrieves the language of the page on which the widget is currently rendered
string language = widgetProperties.Page.LanguageName;
```

## Handle POST actions
If your widget needs to communicate with the server using _POST_ actions, create a custom controller class containing the required action methods and logic. We recommend storing the class in the _~/Components/Widgets/ <WidgetName>_ folder together with other files required by the widget.
**Endpoint security**
Consider whether the endpoints you design should only be accessible from the admin UI (edit mode of website channel applications). See [Secure custom endpoints](documentation/developers-and-admins/customization/secure-custom-endpoints) for more information.
Note that the page where the widget is currently rendered is not by default accessible from within _POST_ actions. Common _POST_ requests do not contain sufficient information to identify the page from which they originate. 
To access the page data in _POST_ actions, you need to include information about the current page into the data submitted by the corresponding form in the widget’s output by calling the `ModeHtml.Kentico().PageData` extension method (or the method’s [Tag Helper alternative](documentation/developers-and-admins/development/reference-tag-helpers)) within the given form tag in your widget view.
cshtml
Copy
```
@using Kentico.Web.Mvc
@using Kentico.PageBuilder.Web.Mvc

<form asp-controller="WidgetPostActionsController" asp-action="HandlePost" method="post">

    ...

    @Html.Kentico().PageData()

    <input type="submit" value="Submit" />
</form>
```

The method renders a hidden form field that persists information about the current page. The page data can be retrieved via the `IWebPageDataContextRetriever` service and [content retriever API](documentation/developers-and-admins/api/content-item-api/content-retriever-api) in the corresponding controller action.
Obtain an instance of the `IWebPageDataContextRetriever` service (using dependency injection) and call its `Retrieve` method. The returned object’s `WebPage` property contains basic information about the current page, which you can use to [retrieve the page object](documentation/developers-and-admins/development/content-retrieval/retrieve-page-content).
C#
Copy
```
// An instance of IWebPageDataContextRetriever (e.g., obtained via dependency injection)
private readonly IWebPageDataContextRetriever retriever;

// Access the current page data
var page = retriever.Retrieve().WebPage;

// Access the properties of the page
int webPageId = page.WebPageItemID;
int languageId = page.LanguageID
string languageName = page.LanguageName;
int contentTypeId = page.ContentTypeID;
string contentTypeName = page.ContentTypeName;
int channelId = page.WebsiteChannelID;
string channelName = page.WebsiteChannelName;
```

## Register widgets
Every widget needs to be registered into the system to be available via Page Builder. Register widgets using the `RegisterWidget` assembly attribute (from the `Kentico.PageBuilder.Web.Mvc` namespace).
For basic widgets that consist of only partial views, we recommend adding their registration attributes to a dedicated code file. This keeps your registrations organized. For example, you can create a file named `ComponentRegister.cs` in your project’s _~/Components_ folder and use it to register your Page Builder components. 
When registering basic widgets, specify the following parameters:
  * `Identifier` – the unique identifier of the widget. We recommend using a unique prefix in your widget identifiers to prevent conflicts when deploying widgets to other projects, for example matching your company’s name.
  * `Name` – the name used to identify the widget when displayed in the administration interface.
  * (Optional) `PropertiesType` – only required for [widgets with properties](documentation/developers-and-admins/development/builders/page-builder/widgets-for-page-builder/widget-properties). Specifies the `System.Type` of the widget’s property model class.
  * `CustomViewName` – specifies the name and location of the view that defines the widget’s output. If not set, the system searches for a corresponding  __ <Identifier>.cshtml_ view in the _~/Views/Shared/Widgets_ folder (any period characters ‘.’ in the identifier are replaced by underscores ‘_’).
C#
**Basic widget registration example**
Copy
```
[assembly: RegisterWidget(
    identifier: "CompanyName.CustomWidget",
    name: "Custom widget",
    propertiesType: typeof(CustomWidgetProperties),
    customViewName: "~/Components/Widgets/<WidgetName>/_CustomWidgetView")]
```



For widgets based on view components, you can add the assembly attribute directly into the component code file. In this case, specify the following attribute parameters:
  * `Identifier` – the unique identifier of the widget. We recommend using a unique prefix in your widget identifiers to prevent conflicts when deploying widgets to other projects, for example matching your company’s name.
  * `ViewComponentType` – the `System.Type` of the widget’s view component class.
  * `Name` – the name used to identify the widget when displayed in the administration interface.
  * (Optional) `PropertiesType` – only required for [widgets with properties](documentation/developers-and-admins/development/builders/page-builder/widgets-for-page-builder/widget-properties). Specifies the `System.Type` of the widget’s property model class.
C#
**View component widget registration example**
Copy
```
[assembly: RegisterWidget(
    identifier: "CompanyName.CustomWidget",
    viewComponentType: typeof(CustomWidgetViewComponent),
    name: "Custom widget",
    propertiesType: typeof(CustomWidgetProperties))]
```



For both types of widgets you can also set the following optional properties:
  * `AllowCache` – a `bool` value indicating whether the output of the widget can be [cached](documentation/developers-and-admins/development/caching/output-caching).
  * `Description` – the description of the widget displayed as a tooltip.
  * `IconClass` – the [font icon class](http://devnet.kentico.com/docs/icon-list/index.html) displayed when viewing the widgets in the widget list.


## Add scripts and styles for widgets
To add JavaScript and CSS styles required by your widgets, we recommend placing script and stylesheet files into sub-folders under:
  * **_~/wwwroot/PageBuilder/Public/Widgets/ <WidgetName>_** – scripts and styles intended for the live site
  * **_~/wwwroot/PageBuilder/Admin/Widgets/ <WidgetName>_** – scripts and styles intended for the administration interface (when working with the widget in the Page Builder editing interface). For example, [inline editor](documentation/developers-and-admins/development/builders/page-builder/widgets-for-page-builder/inline-editors-for-widget-properties) registration scripts.


You can use sub-folders that match the identifiers of individual widgets, or a _Shared_ sub-folder for assets used by multiple widgets. Note that this recommendation only applies when using the default configuration of the bundling support provided by Xperience (and may therefore be different for your project). See [Bundle static assets of builder components](documentation/developers-and-admins/development/builders/bundle-static-assets-of-builder-components).
**CSS notes**
  * Only use the specified directories to add **basic styles** that are required for the widget to render correctly. Any **site-specific styles** that finalize the live site design of the widget should be handled separately within the given site’s main stylesheet.
  * To avoid potential conflicts between styles from other third-party components, we recommend adding a unique prefix to your CSS classes and identifiers (for example  _#CompanyName-mid-button_), or use similar measures to ensure their uniqueness.


### Initialize widget scripts
In many cases, you will need to initialize your scripts from the views of widgets (for example if you need to call a function on page load or register an event listener). For most types of page or element events, you can use [HTML Event Attributes](https://www.w3schools.com/tags/ref_eventattributes.asp) of elements in your views.
For scripts that you want to run on page load, you need to consider the following:
  * Your main scripts are added at the end of the HTML document’s body tag, so they are not available in the widget code during the page load process. A solution is to run the initialization script during the [DOMContentLoaded](https://developer.mozilla.org/en-US/docs/Web/Events/DOMContentLoaded) event.
  * Widgets in the Page Builder interface may be added dynamically after the page is loaded. In this case, the `DOMContentLoaded` event has already occurred and will not fire again.


For example, the following script demonstrates how to reliably call a custom function on page load:
C#
Copy
```
if (document.readyState === "loading") {
    // Calls the function during the 'DOMContentLoaded' event, after the HTML document has been completely loaded
    document.addEventListener("DOMContentLoaded", function () {
        customFunction();
    });
} else {
    // Calls the function directly in cases where the widget is rendered dynamically after 'DOMContentLoaded' has occurred
    customFunction();
}
```

This approach ensures that the initialization script runs correctly when the widget is displayed on the live site, as well as in the Page Builder interface.
**Note** : Apart from initialization code, avoid linking or executing scripts directly within widget views – this could lead to duplicated scripts on pages that contain multiple instances of the same widget.