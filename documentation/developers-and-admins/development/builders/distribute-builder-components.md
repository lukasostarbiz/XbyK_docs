---
source: https://docs.kentico.com/documentation/developers-and-admins/development/builders/distribute-builder-components
scrape_date: 2026-01-22
---

  * [Home](/documentation)
  * [Developers and admins](/documentation/developers-and-admins)
  * [Development](/documentation/developers-and-admins/development)
  * [Builders](/documentation/developers-and-admins/development/builders)
  * Distribute builder components 


# Distribute builder components
After you create a custom [Page Builder](/documentation/developers-and-admins/development/builders/page-builder) or [Form Builder](/documentation/developers-and-admins/development/builders/form-builder) component for your project, you might want to share the code of this component among other projects inside your company, or with the rest of the world. 
The most common way of distributing code in the .NET community is using [NuGet packages](https://docs.microsoft.com/en-us/nuget/what-is-nuget). NuGet packages are code bundles containing compiled code along with other content needed in the projects for which they are created. Developers can share these packages with other developers directly or by publishing them to public or private hosts (such as [nuget.org](https://www.nuget.org/)). Another possible way of distributing your code is simply by sharing the code packaged in [ZIP files](https://en.wikipedia.org/wiki/Zip_\(file_format\)).
On this page, you can read about best practices related to distributing builder components. See the following sections for information about individual types of components:
  * [Widgets](#widgets)
  * [Inline property editors](#inline-property-editors)
  * [Page Builder sections](#page-builder-sections)
  * [Page templates](#page-templates)
  * [Form components](#form-components)
  * [Form Builder sections](#form-builder-sections)
  * [Personalization condition types with a custom configuration dialog](#personalization-condition-types-with-a-custom-configuration-dialog)
  * [Single class components](#single-class-components)


## Widgets
  * Use a **unique prefix** in your widget identifiers to prevent conflicts when deploying the widgets to other projects. For example, the prefix can match your company’s name.
  * Store all text displayed by the widgets using resource strings. Create a **designated RESX file** for the resource strings of each of your widgets in the  _~/App_Data/Global/Resources_ directory.
  * Place all custom code into _~/Components/PageBuilder/Widgets/ <WidgetIdentifier>_. This includes the widget’s: 
    * Controller.
    * Property and view models.
    * View files.
  * Place administration-only script and stylesheet files into the _~/wwwroot/PageBuilder/Admin/Widgets/ <WidgetIdentifier>_ directory.
  * Place general script and stylesheet files into the  _~/wwwroot/PageBuilder/Public/Widgets/ <WidgetIdentifier>_ directory.


See [Widgets for Page Builder](/documentation/developers-and-admins/development/builders/page-builder/widgets-for-page-builder) for more information.
## Inline property editors
  * Use a **unique prefix** in your inline editor identifiers to prevent conflicts when deploying the editors to other projects. For example, the prefix can match your company’s name.
  * Store all text displayed by the inline editors using resource strings. Create a **designated RESX file** for the resource strings of each of your inline editors in the  _~/App_Data/Global/Resources_ directory.
  * Place all custom code into _~/Components/PageBuilder/InlineEditors/ <InlineEditorIdentifier>_. This includes the editor’s: 
    * View models.
    * View files.
  * Place script and stylesheet files into the _~/wwwroot/PageBuilder/Admin/InlineEditors/ <InlineEditorIdentifier>_ directory.


See [Inline editors for widget properties](/documentation/developers-and-admins/development/builders/page-builder/widgets-for-page-builder/inline-editors-for-widget-properties) for more information.
## Page Builder sections
  * Use a **unique prefix** in your section identifiers to prevent conflicts when deploying the widgets to other projects. For example, the prefix can match your company’s name.
  * Store all text displayed by the widgets using resource strings. Create a **designated RESX file** for the resource strings of each of your widgets in the  _~/App_Data/Global/Resources_ directory.
  * Place all custom code into _~/Components/PageBuilder/Sections/ <SectionIdentifier>_. This includes the section’s: 
    * Controller.
    * View model.
    * View files.
  * Place administration-only script and stylesheet files into the _~/wwwroot/PageBuilder/Admin/Sections/ <SectionIdentifier>_ directory.
  * Place general script and stylesheet files into the  _~/wwwroot/PageBuilder/Public/Sections/ <SectionIdentifier>_ directory.


See [Sections for Page Builder](/documentation/developers-and-admins/development/builders/page-builder/sections-for-page-builder) for more information.
## Page templates
  * Use a **unique prefix** in your page template identifiers to prevent conflicts when deploying the page templates to other projects. For example, the prefix can match your company’s name.
  * Store all text displayed by the page templates using resource strings. Create a **designated RESX file** for the resource strings of each of your page templates in the  _~/App_Data/Global/Resources_ directory.
  * Place all custom code into _~/Components/PageBuilder/Templates/ <TemplateIdentifier>_. This includes the template’s: 
    * View model.
    * View files.
  * Place administration-only script and stylesheet files into the _~/wwwroot/PageBuilder/Admin/Templates/ <TemplateIdentifier>_ directory.
  * Place general script and stylesheet files into the  _~/wwwroot/PageBuilder/Public/Templates/ <TemplateIdentifier>_ directory.
  * Place your [page template filters](/documentation/developers-and-admins/development/builders/page-builder/page-templates-for-page-builder/page-template-filtering) into the _~/Components/PageBuilder/Templates/PageTemplateFilters_ directory.


See [Page templates for Page Builder](/documentation/developers-and-admins/development/builders/page-builder/page-templates-for-page-builder) for more information.
## Form components
  * Use a **unique prefix** in your form component identifiers to prevent conflicts when deploying the components to other projects. For example, the prefix can match your company’s name.
  * Store all text displayed by the form components using resource strings. Create a **designated RESX file** for the resource strings of each of your components in the  _~/App_Data/Global/Resources_ directory.
  * Place all custom code into _~/Components/FormBuilder/FormComponents/ <FormComponentIdentifier>_. This includes the component’s: 
    * Form component classes.
    * Form component properties.
    * View files.
  * Place administration-only script and stylesheet files into the _~/wwwroot/FormBuilder/Admin/FormComponents/ <ComponentIdentifier>_ directory.
  * Place general script and stylesheet files into the  _~/wwwroot/FormBuilder/Public/FormComponents/ <ComponentIdentifier>_ directory.


See [Form components](/documentation/developers-and-admins/development/builders/form-builder/form-components) for more information.
## Form Builder sections
  * Use a **unique prefix** in your form section identifiers to prevent conflicts when deploying the sections to other projects. For example, the prefix can match your company’s name.
  * Store all text displayed by the form builder sections using resource strings. Create a **designated RESX file** for the resource strings of each of your sections in the  _~/App_Data/Global/Resources_ directory.
  * Place all custom code into _~/Components/FormBuilder/FormSections/ <FormSectionIdentifier>_. This includes the section’s: 
    * Controllers.
    * Model classes.
    * View files.
  * Place administration-only script and stylesheet files into the _~/wwwroot/FormBuilder/Admin/FormSections/ <FormSectionIdentifier>_ directory.
  * Place general script and stylesheet files into the  _~/wwwroot/FormBuilder/Public/FormSections/ <FormSectionIdentifier>_ directory.


See [Form sections](/documentation/developers-and-admins/development/builders/form-builder/form-sections) for more information.
## Personalization condition types with a custom configuration dialog
  * Use a **unique prefix** in your condition type identifiers to prevent conflicts when deploying the condition types to other projects. For example, the prefix can match your company’s name.
  * Store all text displayed by the condition types using resource strings. Create a **designated RESX file** for the resource strings of each of your condition types in the  _~/App_Data/Global/Resources_ directory.
  * Place all code into _~/Components/PageBuilder/Personalization/ <ConditionIdentifier>_. This includes the condition’s:
    * Type classes.
    * Dialog controllers.
    * Dialog models.
    * Dialog views.
  * Place administration-only CSS and JavaScript files into the _~/wwwroot/PageBuilder/Admin/Personalization/ <ConditionIdentifier>_ directory.
  * Place general CSS and JavaScript files into the _~/wwwroot/PageBuilder/Public/Personalization/ <ConditionIdentifier>_ directory.


See [Personalize Page Builder widgets](/documentation/business-users/digital-marketing/widget-personalization) for more information.
## Single class components
When distributing components consisting of a single class, such as [personalization condition types](/documentation/business-users/digital-marketing/widget-personalization) or [Form Builder validation rules](/documentation/developers-and-admins/development/builders/form-builder/form-builder-validation-rules):
  * Use a **unique prefix** in your component identifiers to prevent conflicts when deploying the components to other projects. For example, the prefix can match your company’s name.
  * For text displayed by the components in the admin UI, use [localization files](/documentation/developers-and-admins/customization/admin-ui-localization).


![]()
[]()[]()
