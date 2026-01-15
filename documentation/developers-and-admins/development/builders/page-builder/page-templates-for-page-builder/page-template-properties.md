# Page template properties
  * [ Copy page link ](documentation/developers-and-admins/development/builders/page-builder/page-templates-for-page-builder/page-template-properties#) | [Get HelpService ID](documentation/developers-and-admins/development/builders/page-builder/page-templates-for-page-builder/page-template-properties#)
Core MVC 5


[✖](documentation/developers-and-admins/development/builders/page-builder/page-templates-for-page-builder/page-template-properties# "Close page link panel") [Copy to clipboard](documentation/developers-and-admins/development/builders/page-builder/page-templates-for-page-builder/page-template-properties#)
When developing [page templates](documentation/developers-and-admins/development/builders/page-builder/page-templates-for-page-builder), you can define properties that allow content editors to adjust the template appearance or behavior directly through the administration interface. Content editors can set the page template properties via a configuration dialog and the changes are then reflected on the website.
The page template can be shared by any number of pages, but each page has its own separate configuration of the available template properties.
Follow these steps to develop properties for a page template:
  1. [Create a model class that defines the page template properties](documentation/developers-and-admins/development/builders/page-builder/page-templates-for-page-builder/page-template-properties#create-property-models)
  2. [Define the configuration dialog to allow content editors to modify the properties](documentation/developers-and-admins/development/builders/page-builder/page-templates-for-page-builder/page-template-properties#define-the-configuration-dialog)
  3. [Handle the properties in the page template’s code](documentation/developers-and-admins/development/builders/page-builder/page-templates-for-page-builder/page-template-properties#handle-properties-in-page-template-code)


**Advanced page template example**
See the [Create versatile page templates](guides/development/page-builder/create-versatile-templates-part-1) guide for a detailed step-by-step walkthrough showing how to create a complex page template, including handling of template properties and page fields.
## Create property models
The properties of a page template must be defined within a model class that implements the `IPageTemplateProperties` interface (available in the `Kentico.PageBuilder.Web.Mvc.PageTemplates` namespace).
Specify each page template property by creating a corresponding property in the model class. You can also set default values for the properties.
C#
**Example - Defining a property**
Copy
```
public class LandingPageProperties : IPageTemplateProperties
{
    // Defines a property and sets its default value
    public bool ShowTitle{ get; set; } = true;
}
```

You can use the `Newtonsoft.Json.JsonIgnore` attribute to exclude dynamically computed page template properties from database serialization.
We recommend storing page template property models in the  _~/PageTemplates/ <TemplateName>_ folder.
## Define the configuration dialog
The configuration dialog provides a simple way for content editors to set values for page template properties via the Xperience administration. In the properties model class, you need to define [editing components](documentation/developers-and-admins/customization/extend-the-administration-interface/ui-form-components/editing-components) for page template properties which you want to make editable in the configuration dialog. You can use the system’s [default UI form components](documentation/developers-and-admins/customization/extend-the-administration-interface/ui-form-components/reference-admin-ui-form-components) or develop [custom UI form components](documentation/developers-and-admins/customization/extend-the-administration-interface/ui-form-components).
  1. Edit the page template’s property model class in your live site project.
  2. Define the visual interface of the configuration dialog using the desired form component attributes:
C#
**Example - Setting an editing component**
Copy
```
using Kentico.Xperience.Admin.Base.FormAnnotations;

[CheckBoxComponent(Order = 0, Label = "Show title")]
public bool ShowTitle{ get; set; } = true;
```



Users can now click the **Configure** ([working with the given page template in the administration interface](documentation/business-users/website-content/page-templates). This opens the page template properties dialog, and the configured property values affect the appearance and functionality of the edited page on the website. Every page using the template has its own separate configuration of the template’s properties.
**Custom editing components with links to content items**
If you decorate any page template property with a [custom form component](documentation/developers-and-admins/customization/extend-the-administration-interface/ui-form-components) that allows users to add links to content items (other than the default [combined content selector](documentation/developers-and-admins/customization/extend-the-administration-interface/ui-form-components/reference-admin-ui-form-components#combined-content-selector) and [rich text editor](documentation/business-users/rich-text-editor) ), you need to create a [custom reference extractor](documentation/developers-and-admins/customization/extend-the-administration-interface/ui-form-components/ui-form-component-reference-extractors) in order to enable [usage tracking](documentation/business-users/content-hub/content-items#track-usage-of-content-items) in the property.
**Advanced options**
  * You can add dynamic visibility conditions that restrict how and when properties are displayed in the template configuration dialog.
  * You can also assign validation rules to help users provide relevant information.


See [Builder component properties visibility and validation](documentation/developers-and-admins/development/builders/builder-component-properties-visibility-and-validation) for an overview.
## Handle properties in page template code
To access the template’s properties, the template’s view must use the generic `TemplateViewModel<TPropertiesType>` class as its model, where the generic type parameter needs to match the model class registered for the template. The system ensures the property values configured for the currently processed template. Access the property values via the model’s `Properties` member. The member returns an object of the property model class registered for the page template.
cshtml
Copy
```
@model TemplateViewModel<LandingPageProperties>

@* Accesses the template's properties (LandingPageProperties in this example) *@
@Model.Properties.ShowTitle
```

### Access page template properties in POST actions
Basic POST requests do not by default contain page template properties data. To access the properties of a template during POST actions (in the [controller class](documentation/developers-and-admins/development/builders/page-builder/page-templates-for-page-builder#handle-post-actions) handling POST requests), you first need to include the properties into the data submitted by the corresponding HTML form in the template’s output. Call the `Html.Kentico().ComponentPropertiesData` extension method (or its [Tag Helper](documentation/developers-and-admins/development/reference-tag-helpers) equivalent) within the given `form` tag in your template’s view.
cshtml
**Example**
Copy
```
using Kentico.Content.Web.Mvc

...

<form asp-controller="PageTemplatePostController" asp-action="HandlePost" method="post">
    ...

    @Html.Kentico().ComponentPropertiesData()

    <input type="submit" value="Submit" />
</form>
```

The method renders a hidden field that persists the template’s current properties configuration.
On the server, access the template’s properties in the corresponding controller class via the `IPageBuilderTemplatePropertiesRetriever` service and its `Retrieve<TPropertiesType>` method. Specify the template’s properties class as the method’s generic parameter: 
C#
**Controller class handling the request**
Copy
```
// Contains an instance of the IPageBuilderTemplatePropertiesRetriever service (e.g., obtained via dependency injection)
private readonly IPageBuilderTemplatePropertiesRetriever pageTemplatePropertiesRetriever;

// Gets the properties of the widget as a strongly typed object
LandingPageProperties properties = pageTemplatePropertiesRetriever.Retrieve<LandingPageProperties>();
```