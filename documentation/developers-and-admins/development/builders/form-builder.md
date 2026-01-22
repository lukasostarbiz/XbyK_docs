---
source: https://docs.kentico.com/documentation/developers-and-admins/development/builders/form-builder
scrape_date: 2026-01-22
---

  * [Home](/documentation)
  * [Developers and admins](/documentation/developers-and-admins)
  * [Development](/documentation/developers-and-admins/development)
  * [Builders](/documentation/developers-and-admins/development/builders)
  * Form Builder 


# Form Builder
The Xperience Form Builder offers an intuitive graphical editor that allows content creators and marketers to [compose online forms](/documentation/business-users/digital-marketing/forms). It is available on the **Form Builder** tab of the **Forms** application when editing a form.
[![Xperience Form Builder interface](/docsassets/documentation/form-builder/FormBuilderUIOverview.png)](/docsassets/documentation/form-builder/FormBuilderUIOverview.png)
## Displaying forms
Forms created in the Form Builder can be displayed on the website in the following ways:
  * Content editors can [place](/documentation/business-users/digital-marketing/forms/display-forms-on-pages) the forms onto the live site as widgets using the [Page Builder](/documentation/business-users/website-content/widgets-and-page-builder).
  * Developers can add forms directly into the views of pages by [rendering](/documentation/developers-and-admins/development/builders/page-builder/render-widgets-in-code) the _Form_ widget.


## Form Builder components
The Form Builder framework consists of a number of component types, each contributing specific functionality.
#### Form sections
[Form sections](/documentation/developers-and-admins/development/builders/form-builder/form-sections) drive the overall layout of forms. They represent reusable pieces of markup that can store any number of form zones – areas where content creators place form fields. Sections are fully customizable, and as a developer, you have absolute freedom in the way you set each section’s layout. Form sections can be implemented with [properties](/documentation/developers-and-admins/development/builders/form-builder/form-sections/form-section-properties), which allow content editors to adjust the content or behavior of the sections using configuration dialogs in the Form Builder interface.
See [Form sections](/documentation/developers-and-admins/development/builders/form-builder/form-sections) to learn how to implement and customize form sections.
#### Form components
The main building blocks of forms are [form components](/documentation/developers-and-admins/development/builders/form-builder/form-components) – instances of components represent individual form fields. The implementation of form components consists of two classes, one facilitating the front-end functionality, the other encapsulating the state as configured via the Form Builder’s properties panel, and a corresponding partial view, providing the component’s visual elements.
Natively, Xperience contains a set of form components available for use instantly, without the need of any custom development on your part. See [Reference - Form Builder components](/documentation/developers-and-admins/development/builders/form-builder/reference-form-builder-components) for a comprehensive list. You can also implement form components suited for your specific scenarios. See [Form components](/documentation/developers-and-admins/development/builders/form-builder/form-components) for more information and a general overview.
#### Validation rules
Validation rules enforce constraints on form field inputs. For example, you can limit the maximum allowed text length or the highest number a user can enter into a field. Editors can [add validation rules](/documentation/business-users/digital-marketing/forms/create-and-edit-forms#field-validation) when configuring individual fields in the Form Builder UI.
Learn how to implement custom validation rules in [Form Builder validation rules](/documentation/developers-and-admins/development/builders/form-builder/form-builder-validation-rules).
Components need to be registered under a unique _Identifier,_ with a  _Name_ that is displayed in the Form Builder interface.
## Form Builder utilities
The system provides a set of extension methods and ASP.NET Core [Tag Helpers](/documentation/developers-and-admins/development/reference-tag-helpers) designed to facilitate development of new Form Builder components. To easily access these extension methods in the code of your views, edit your project’s **Views\ViewImports.cshtml** file and:
  * add the `Kentico.Forms.Web.Mvc` namespace
  * add the Tag Helpers from the `Kentico.Content.Web.Mvc` namespace


cshtml
**ViewImports.cshtml**
Copy
```
@using Kentico.Forms.Web.Mvc

...

@addTagHelper *, Kentico.Content.Web.Mvc
```

Alternatively, you can add these directly in the code of individual views.
![]()
[]()[]()
