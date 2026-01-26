---
source: https://docs.kentico.com/documentation/developers-and-admins/development/builders/form-builder/form-widget-customization
scrape_date: 2026-01-26
---

  * [Home](/documentation)
  * [Developers and admins](/documentation/developers-and-admins)
  * [Development](/documentation/developers-and-admins/development)
  * [Builders](/documentation/developers-and-admins/development/builders)
  * [Form Builder](/documentation/developers-and-admins/development/builders/form-builder)
  * Form widget customization 


# Form widget customization
This page describes how you can customize the markup of forms rendered using the [Form Page Builder widget](/documentation/business-users/digital-marketing/forms/display-forms-on-pages). The page is divided into the following sections:
  * [Customize the HTML markup of rendered forms](#customize-the-html-markup-of-rendered-forms) – describes how to set static, project-wide rendering configurations used when forms or form fields are rendered.
  * [Contextually modify form markup](#contextually-modifying-form-markup) – introduces events that you can handle to dynamically modify markup when forms or individual form fields are rendered (based on the current context, such as the field type, properties, or form name).
  * [Examples](#examples) – provides a set of sample form customization use cases.


**Tip** : If you only need to add custom content wrapped around the default form markup, you can use widget nesting and create an extended form widget. To learn more, see [Extend widgets](/documentation/developers-and-admins/development/builders/page-builder/widgets-for-page-builder/extend-widgets).
## Customize the HTML markup of rendered forms
### Form markup
Forms rendered using the Form widget are by default wrapped by an empty `<div>` block element. You can customize the markup of forms rendered using the widget by setting the static `FormWidgetRenderingConfiguration.Default` property. 
The `FormWidgetRenderingConfiguration` type exposes a group of properties you can configure to customize the markup surrounding the form, as well as the HTML attributes of the form’s _Submit_ button and `<form>` HTML element.
Property |  Type |  Description  
---|---|---  
`FormWrapperConfiguration` |  [FormWrapperRenderingConfiguration](#Formwidgetcustomization-FormWrapperRenderingConfiguration) |  Specifies the HTML element (and its attributes) wrapping the entire form. If `null`_,_ no wrapping element is rendered for forms.   
`FormHtmlAttributes` |  `IDictionary<string, object>` |  Specifies HTML attributes applied to the `<form>` HTML element. **Setting the id attribute of forms** You can use this property to set the `id` attribute of the form element, which is by default randomly generated. However, note that this sets the identical `id` for **ALL** form widget instances. As a result, you can place no more than one form via the _Form_ widget per page.   
`SubmitButtonHtmlAttributes` |  `IDictionary<string, object>` |  Specifies HTML attributes applied to the submit button.  
`FormWrapperConfiguration` is of the `FormWrapperRenderingConfiguration`type, which holds information about HTML elements wrapping each form. It exposes the following properties:
Property |  Type |  Description  
---|---|---  
`ElementName` |  `string` |  The name of the HTML element wrapping the form.  
`HtmlAttributes` |  `IDictionary<string, object>` |  A dictionary of attributes of the `ElementName`.  
`ChildConfiguration` |  `ElementRenderingConfiguration` |  May hold additional [ElementRenderingConfigurations](#Formwidgetcustomization-ElementRenderingConfiguration). Use this property when you wish to nest multiple wrapping elements.  
`CustomHtmlEnvelopeString` |  `string` |  Adds custom HTML content before or after the form wrapper. The HTML string needs to contain the `FormWrapperRenderingConfiguration.CONTENT_PLACEHOLDER` constant to mark where you wish to render the form wrapper and the body of the form. See [CONTENT_PLACEHOLDER usage](#content_placeholder-usage) for an example. **Note** : Content of the `CustomHtmlEnvelopeString` is only rendered once per page load. The form wrapper elements and form body may be rendered repeatedly, for example when form validation fails or when processing field visibility conditions.  
For illustration, the following pseudocode shows the configurable HTML structure of each form:
```
<FormWrapperConfiguration>
    <FORM>
        <FORM_FIELD_A>
        <FORM_FIELD_B>
        ...
        <SUBMIT_BUTTON>
    </FORM>
</FormWrapperConfiguration>
```

### Form field markup
By default, each form field section contains placeholder `<div>` block elements. You can customize the wrapping HTML markup and the attributes of each form field by modifying the `FormFieldRenderingConfiguration.Widget`property.
`FormFieldRenderingConfiguration` exposes a collection of properties you can configure to alter the HTML markup of the rendered form, its form fields, and each field’s subsections, such as label or explanation text:
Property |  Type |  Description  
---|---|---  
`RootConfiguration` |  [ElementRenderingConfiguration](#Formwidgetcustomization-ElementRenderingConfiguration) |  Specifies the HTML element (and its attributes) wrapping the label and corresponding input element. If `null`_,_ no wrapping element is rendered for the given form field.  
`LabelWrapperConfiguration` |  `ElementRenderingConfiguration` |  Specifies the HTML element (and its attributes) wrapping the labelelement.  
`LabelHtmlAttributes` |  `IDictionary<string, object>` |  Specifies HTML attributes applied to the labelelement.  
`ColonAfterLabel` |  `bool` |  Indicates whether a colon ‘:’ character is rendered after the text of the label element. Set to `true` by default.  
`EditorWrapperConfiguration` |  `ElementRenderingConfiguration` |  Specifies the HTML element (and its attributes) wrapping the field’s input, validation message, and explanation text. If `null`_,_ no wrapping HTML element is rendered.  
`EditorHtmlAttributes` |  `IDictionary<string, object>` |  Specifies HTML attributes applied to the field’s input element.  
`ComponentWrapperConfiguration` |  `ElementRenderingConfiguration` |  Specifies the HTML element (and its attributes) wrapping the field’s input element.  
`ExplanationTextWrapperConfiguration` |  `ElementRenderingConfiguration` |  Specifies the HTML element (and its attributes) wrapping the explanation text – a short message usually rendered under the input fields – of the component. If `null`_,_ no wrapping HTML element is rendered.  
`SuppressValidationMessages` |  `bool` |  Indicates whether validation messages are rendered individually for each form field.  
`FormWidgetRenderingConfiguration` and `FormFieldRenderingConfiguration` are of the `ElementRenderingConfiguration`type, which is used to specify the wrapping HTML element. `ElementRenderingConfiguration` exposes the following properties:
Property |  Type |  Description  
---|---|---  
`ElementName` |  `string` |  The name of the HTML element wrapping the given form field component.  
`HtmlAttributes` |  `IDictionary<string, object>` |  Contains attributes of the `ElementName`.  
`ChildConfiguration` |  `ElementRenderingConfiguration` |  May hold additional`ElementRenderingConfigurations`. Use this property to nest multiple wrapping elements for a given form field section.  
`CustomHtml` |  `IHtmlString` |  Sets custom HTML markup instead of the corresponding `ElementRenderingConfiguration`. The HTML string needs to contain `ElementRenderingConfiguration.CONTENT_PLACEHOLDER` to mark where you wish to render the remaining sub-elements within the given wrapping element. See [CONTENT_PLACEHOLDERusage](#content_placeholder-usage) for an example. **Note** : If the `CustomHtml`property is set, the system **disregards** the element and HTML attributes specified in `ElementName` and `HtmlAttribute`and renders the value of this property as **raw HTML** instead. Moreover, further nesting of additional configurations via `ChildConfiguration` becomes disabled.  
For illustration, the following pseudocode shows the configurable structure of individual form fields:
```
<RootConfiguration>
    <LabelWrapperConfiguration>
         <LABEL>
    </LabelWrapperConfiguration>
    <EditorWrapperConfiguration>
        <ComponentWrapperConfiguration>
            <EDITOR>
            <VALIDATION MESSAGE>
        </ComponentWrapperConfiguration>
        <ExplanationTextWrapperConfiguration>
            <EXPLANATION TEXT>
        </ExplanationTextWrapperConfiguration>
    </EditorWrapperConfiguration>
</RootConfiguration>
```

## Contextually modifying form markup
In addition to setting the global rendering configuration, you can modify forms and form fields as they are being rendered. This allows you to adjust the resulting markup (accessibility or data attributes, CSS classes, etc.) based on contextual information. For example, you can adjust form markup based on the properties of specific fields or the name of the form. The system provides the following two events that fire when forms or form fields are rendered:
  * [FormWidgetRenderingConfiguration.GetConfiguration](#formwidgetrenderingconfiguration.getconfiguration) – fired when a form inserted using the _Form_ Page Builder widget is rendered.
  * [FormFieldRenderingConfiguration.GetConfiguration](#formfieldrenderingconfiguration.getconfiguration) – fired when individual form fields are rendered.


### FormWidgetRenderingConfiguration.GetConfiguration
**Namespace** : Kentico.Forms.Web.Mvc.Widgets
Fired when a form inserted via the _Form_ widget is rendered. The `GetFormWidgetRenderingConfigurationEventArgs` event arguments allow you to access or modify:
Property |  Type (and select properties) |  Description  
---|---|---  
`Configuration` |  `FormWidgetRenderingConfiguration` |  The rendering configuration of the form. See [Form markup](#form-markup) for a detailed description of the `FormWidgetRenderingConfiguration` type.  
`Form` |  `BizFormInfo`
  * `Form`
  * `FormName`
  * `FormDisplayName`

|  The system object representing the form.   
`FormComponents` |  `IEnumerable<FormComponent>` |  Contains a collection of form fields being rendered on this request. This collection can change when rendering forms containing [smart fields](/documentation/business-users/digital-marketing/forms/use-smart-fields-in-forms).  
`FormWidgetProperties` |  `FormWidgetProperties` |  Contains the properties of the current [Form widget](/documentation/business-users/digital-marketing/forms) instance.  
See [Add contextual markup to forms](#add-contextual-markup-to-forms) for an example.
### FormFieldRenderingConfiguration.GetConfiguration
**Namespace** : Kentico.Forms.Web.Mvc
Fired whenever a form field is being rendered (on the live site or when viewing pages in a website channel application). The `GetFormFieldRenderingConfigurationEventArgs` event arguments allow you to access or modify:
Property |  Type (and select properties) |  Description  
---|---|---  
`Configuration` |  `FormFieldRenderingConfiguration` |  The rendering configuration of the form field. See [Form field markup](#form-field-markup) for a detailed description of the `FormFieldRenderingConfiguration`type.  
`FormComponent` |  `FormComponent`
  * `BaseProperties`
  * `Definition`

|  Allows you to access type and configuration information of the rendered form component. `BaseProperties` holds the component’s properties, such as the field’s required status, label, and explanation text, as configured via the Form Builder’s [properties panel](/documentation/business-users/digital-marketing/forms/create-and-edit-forms) or programmatically using [editing component attributes](/documentation/developers-and-admins/customization/extend-the-administration-interface/ui-form-components/editing-components). `Definition` holds information under which the corresponding form component was [registered into the system](/documentation/developers-and-admins/development/builders/form-builder/form-components), such as its `identifier`, `name`, or `description`.  
To access contextual information about the form field, use `GetBizFormComponentContext`:
C#
Copy
```
BizFormComponentContext context = e.FormComponent.GetBizFormComponentContext();
```

You can access the code and display names, as well as other system data of the form containing the currently processed form field via `BizFormComponentContext.FormInfo`.
See [Add contextual markup to form fields](#add-contextual-markup-to-form-fields) for an example.
## Examples
This section contains a set of examples demonstrating usage of the form customization API:
  * [Set global rendering configurations](#set-global-rendering-configurations) – shows how to set a static, project-wide configuration using `FormFieldRenderingConfiguration` and `FormWidgetRenderingConfiguration`.
  * `CONTENT_PLACEHOLDER` [usage](#content_placeholder-usage) – demonstrates the impact of setting `CustomHtml` on the resulting HTML structure.
  * [Adding contextual markup to forms](#add-contextual-markup-to-forms) – uses the [FormWidgetRenderingConfiguration.GetConfiguration](#formwidgetrenderingconfiguration.getconfiguration) event to contextually modify form markup.
  * [Adding contextual markup to form fields](#add-contextual-markup-to-form-fields) – uses the [FormFieldRenderingConfiguration.GetConfiguration](#formfieldrenderingconfiguration.getconfiguration) event to contextually modify form field markup.


### Set global rendering configurations
We recommend setting the rendering configurations during the live site project’s startup. For example, within the initialization code of a [custom module](/documentation/developers-and-admins/customization/run-code-on-application-startup):
C#
Copy
```
using Kentico.Forms.Web.Mvc;
using Kentico.Forms.Web.Mvc.Widgets;

namespace FormBuilderCustomizations
{
    public class FormBuilderStaticMarkupConfiguration
    {
        public static void SetGlobalRenderingConfigurations()
        {
            // Modifies the default FormFieldRenderingConfiguration for the 'Form' widget
            // Specifying a new FormFieldRenderingConfiguration instance completely replaces the default Kentico configuration
            FormFieldRenderingConfiguration.Widget.RootConfiguration =
                 new ElementRenderingConfiguration
                 {
                     ElementName = "div",
                     HtmlAttributes = { { "class", "form-field" } }
                 };

            FormFieldRenderingConfiguration.Widget.ExplanationTextWrapperConfiguration =
                 new ElementRenderingConfiguration
                 {
                     ElementName = "div",
                     HtmlAttributes = { { "class", "explanation-text" } }
                 };

            // Sets a new rendering configuration for the 'Form' widget, adding attributes
            // to the form element and the submit button and wrapping the form in two 'div' blocks
            FormWidgetRenderingConfiguration.Default = new FormWidgetRenderingConfiguration
            {
                // Form element HTML attributes
                FormHtmlAttributes = { { "formattribute", "attributevalue" } },

                // Submit button HTML attributes
                SubmitButtonHtmlAttributes = { { "class", "submit-button" } },

                // Elements wrapping the Form element
                FormWrapperConfiguration = new FormWrapperRenderingConfiguration
                {
                    ElementName = "div",
                    HtmlAttributes = { { "class", "form-widget-form" } },
                    // Adds an additional div element wrapper
                    ChildConfiguration = new ElementRenderingConfiguration
                    {
                        ElementName = "div"
                    }
                }
            };
        }
    }
}
```

Call the `SetGlobalRenderingConfigurations` method on application start. For example, as part of [custom module initialization code](/documentation/developers-and-admins/customization/run-code-on-application-startup).
Forms displayed via the _Form_ widget are now rendered using the modified rendering configurations.
### CONTENT_PLACEHOLDER usage
The `CONTENT_PLACEHOLDER` property, part of `FormFieldRenderingConfiguration` and `FormWrapperRenderingConfiguration`, acts as a marker that tells the system where to render the inner HTML markup in case `CustomHtml` (or `CustomHtmlEnvelopeString`) is set as part of the configuration.
For example, modifying `FormFieldRenderingConfiguration` in the following manner:
C#
Copy
```
FormFieldRenderingConfiguration.Widget.EditorWrapperConfiguration.CustomHtml =
                MvcHtmlString.Create($"<div data-customhtml=\"Replacing EditorWrapperConfiguration\">{ElementRenderingConfiguration.CONTENT_PLACEHOLDER}</div>");
```

Results in this HTML structure for form fields:
HTML
Copy
```
<RootConfiguration>
    <LabelWrapperConfiguration>
         <LABEL>
    </LabelWrapperConfiguration>
    // Any existing 'EditorWrapperConfiguration' is discarded and its markup gets replaced
    // CONTENT_PLACEHOLDER indicates where the inner markup is placed - wrapped by a 'div' element in this case.
    <div data-customhtml="Replacing EditorWrapperConfiguration">
        <ComponentWrapperConfiguration>
            <EDITOR>
            <VALIDATION MESSAGE>
        </ComponentWrapperConfiguration>
        <ExplanationTextWrapperConfiguration>
            <EXPLANATION TEXT>
        </ExplanationTextWrapperConfiguration>
    </div>
</RootConfiguration>
```

### Add contextual markup to forms
The following example uses the [FormWidgetRenderingConfiguration.GetConfiguration](#formwidgetrenderingconfiguration.getconfiguration) event to:
  * Render the **name** of the form above the form element using `CustomHtmlEnvelopeString`.
  * Contextually assign additional attributes only to the **ContactUs** form’s  _Submit_ button.


Register a new `FormWidgetInjectMarkup` handler for the `FormWidgetRenderingConfiguration.GetConfiguration.Execute` event:
Call the `RegisterEventHandlers` method on application start. For example, as part of [custom module initialization code](/documentation/developers-and-admins/customization/run-code-on-application-startup).
C#
Copy
```
using System;
using System.Web.Mvc;

using Kentico.Forms.Web.Mvc.Widgets;

namespace FormBuilderCustomizations
{
    public class FormWidgetMarkupInjection
    {

        public static void RegisterEventHandlers()
        {
            FormWidgetRenderingConfiguration.GetConfiguration.Execute += FormWidgetInjectMarkup;
        }

        private static void FormWidgetInjectMarkup(object sender, GetFormWidgetRenderingConfigurationEventArgs e)
        {
            e.Configuration.FormWrapperConfiguration = new FormWrapperRenderingConfiguration
            {
                // Renders the form's name above the form using the 'CustomHtmlEnvelope' property
                // FormWrapperRenderingConfiguration.CONTENT_PLACEHOLDER acts as a placeholder for the form's body in the resulting markup
                CustomHtmlEnvelope = MvcHtmlString.Create($@"<h1>{e.Form.FormDisplayName}</h1> {FormWrapperRenderingConfiguration.CONTENT_PLACEHOLDER}")
            };

            // Sets additional attributes only for specific forms. Since the 'class' attribute is fairly
            // common, checks if the key is already present and inserts or appends the key accordingly.
            if (e.Form.FormName.Equals("ContactUs", StringComparison.InvariantCultureIgnoreCase))
            {
                if (e.Configuration.SubmitButtonHtmlAttributes.ContainsKey("class"))
                {
                    e.Configuration.SubmitButtonHtmlAttributes["class"] += " contactus-submit-button";
                }
                else
                {
                    e.Configuration.SubmitButtonHtmlAttributes["class"] = "contactus-submit-button";
                }
            }
        }
    }
}
```

### Add contextual markup to form fields
The following example uses the [FormFieldRenderingConfiguration.GetConfiguration](#formfieldrenderingconfiguration.getconfiguration) event to:
  * Add the semantic `aria-required` WAI-ARIA and _required_ HTML5 attributes to form fields marked as **Required** via the Form Builder properties panel, and append a red asterisk after the field’s label using CSS.
  * Assigns additional attributes to `TextArea` fields, provided the form contains any.
  * Adds additional attributes to fields that are part of the **ContactUs** form.


Register a new `KenticoComponentMarkupInjection` handler for the `FormFieldRenderingConfiguration.GetConfiguration.Execute`event:
C#
Copy
```
using System;

using Kentico.Forms.Web.Mvc;

namespace FormBuilderCustomizations
{
    public class FormFieldMarkupInjection
    {
        public static void RegisterEventHandlers()
        {
            // Contextually customizes the markup of rendered form fields
            FormFieldRenderingConfiguration.GetConfiguration.Execute += InjectMarkupIntoKenticoComponents;
        }

        private static void InjectMarkupIntoKenticoComponents(object sender, GetFormFieldRenderingConfigurationEventArgs e)
        {
            // Only injects additional markup into default Kentico form components
            if (!e.FormComponent.Definition.Identifier.StartsWith("Kentico", StringComparison.InvariantCultureIgnoreCase))
            {
                return;
            }

            // Adds WAI-ARIA and HTML5 accessibility attributes to form fields marked as 'Required' via the Form Builder interface
            AddAccessibilityAttributes(e);

            // Assigns additional attributes to 'TextArea' fields
            AddFieldSpecificMarkup(e);

            // Assigns additional attributes to fields rendered as part of a specified form
            AddFormSpecificMarkup(e);
        }

        private static void AddAccessibilityAttributes(GetFormFieldRenderingConfigurationEventArgs e)
        {
            if (e.FormComponent.BaseProperties.Required)
            {
                // Adds the 'aria-required' and 'required' attributes to the component's 'input' element
                e.Configuration.EditorHtmlAttributes["aria-required"] = "true";
                e.Configuration.EditorHtmlAttributes["required"] = "";

                // Appends an asterisk to the component's 'Label' element. Since the class attribute is fairly
                // common, checks if the key is already present and inserts or appends the key accordingly.
                if (e.Configuration.LabelHtmlAttributes.ContainsKey("class"))
                {
                    e.Configuration.LabelHtmlAttributes["class"] += " required-field-red-star";
                }
                else
                {
                    e.Configuration.LabelHtmlAttributes["class"] = "required-field-red-star";
                }
            }
        }

        private static void AddFieldSpecificMarkup(GetFormFieldRenderingConfigurationEventArgs e)
        {
            // Sets a custom CSS class for the wrapping element of 'TextAreaComponent' type form fields
            if (e.FormComponent is TextAreaComponent)
            {
                if (e.Configuration.RootConfiguration.HtmlAttributes.ContainsKey("class"))
                {
                    e.Configuration.RootConfiguration.HtmlAttributes["class"] += " text-area-styles";
                }
                else
                {
                    e.Configuration.RootConfiguration.HtmlAttributes["class"] = "text-area-styles";
                }
            }
        }

        private static void AddFormSpecificMarkup(GetFormFieldRenderingConfigurationEventArgs e)
        {
            // Gets the context of the Form for which the field is being rendered
            BizFormComponentContext context = e.FormComponent.GetBizFormComponentContext();

            // Modifies only form fields rendered as part of the 'ContactUs' form
            if (context.FormInfo.FormName.Equals("ContactUs", StringComparison.InvariantCultureIgnoreCase))
            {
                e.Configuration.ColonAfterLabel = false;
                e.Configuration.EditorHtmlAttributes["data-formname"] = context.FormInfo.FormDisplayName;
            }
        }
    }
}
```

Add the `required-field-red-star` class that renders a red asterisk after a required field’s label to your website’s CSS:
CSS
Copy
```
.required-field-red-star::after {
    content: "*";
    color: red;
}
```

Call the `RegisterEventHandlers` method on application start. For example, as part of [custom module initialization code](/documentation/developers-and-admins/customization/run-code-on-application-startup).
![]()
[]()[]()
