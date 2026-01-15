# Editing components in Xperience by Kentico
  * [ Copy page link ](documentation/developers-and-admins/upgrade-to-xperience-by-kentico/editing-components-in-xperience-by-kentico#) | [Get HelpService ID](documentation/developers-and-admins/upgrade-to-xperience-by-kentico/editing-components-in-xperience-by-kentico#)
Core MVC 5


[✖](documentation/developers-and-admins/upgrade-to-xperience-by-kentico/editing-components-in-xperience-by-kentico# "Close page link panel") [Copy to clipboard](documentation/developers-and-admins/upgrade-to-xperience-by-kentico/editing-components-in-xperience-by-kentico#)
In Xperience by Kentico, editing components are no longer only used when configuring [Page Builder](documentation/developers-and-admins/development/builders/page-builder) and [Form Builder](documentation/developers-and-admins/development/builders/form-builder) components. Instead, you can use them across the entire Xperience administration (e.g., in [content types](documentation/developers-and-admins/development/content-types), [UI form components](documentation/developers-and-admins/customization/extend-the-administration-interface/ui-form-components), [validation](documentation/developers-and-admins/customization/extend-the-administration-interface/ui-form-components/ui-form-component-validation-rules) and [visibility](documentation/developers-and-admins/customization/extend-the-administration-interface/ui-form-components/ui-form-component-visibility-conditions) conditions, [custom configuration dialogs](documentation/developers-and-admins/customization/extend-the-administration-interface/ui-pages/ui-pages-with-forms)).
A consequence of this broadened scope is that the Kentico Xperience 13 approach of assigning and configuring editing components via the `EditingComponent` attribute is now obsolete. Editing components in Xperience by Kentico, though functionally mostly equivalent, use a [different technology stack](documentation/developers-and-admins/customization/extend-the-administration-interface/ui-form-components) to integrate with the new administration interface.
The old API remains supported and is backward compatible, including modal dialog support. However, this will change in the future when support for Kentico Xperience 13 ends. We **strongly recommend** updating your codebase to the new components as soon as possible.
See:
  * [Editing components](documentation/developers-and-admins/customization/extend-the-administration-interface/ui-form-components/editing-components) for more information about the new API used to assign editing components.
  * [Reference - Admin UI form components](documentation/developers-and-admins/customization/extend-the-administration-interface/ui-form-components/reference-admin-ui-form-components) for a reference of all components usable in the admin UI.


See the [Form components](documentation/developers-and-admins/upgrade-to-xperience-by-kentico/editing-components-in-xperience-by-kentico#form-components) section for general information about updating to the new admin UI form components.For details about updating selector components, see the following sections:
  * [Page selector](documentation/developers-and-admins/upgrade-to-xperience-by-kentico/editing-components-in-xperience-by-kentico#page-selector)
  * [URL selector](documentation/developers-and-admins/upgrade-to-xperience-by-kentico/editing-components-in-xperience-by-kentico#url-selector)
  * [Object selector](documentation/developers-and-admins/upgrade-to-xperience-by-kentico/editing-components-in-xperience-by-kentico#object-selector)
  * [General selector](documentation/developers-and-admins/upgrade-to-xperience-by-kentico/editing-components-in-xperience-by-kentico#general-selector)
  * [Media file selector](documentation/developers-and-admins/upgrade-to-xperience-by-kentico/editing-components-in-xperience-by-kentico#media-file-selector)
  * [Path selector](documentation/developers-and-admins/upgrade-to-xperience-by-kentico/editing-components-in-xperience-by-kentico#path-selector)
  * [Attachment selector](documentation/developers-and-admins/upgrade-to-xperience-by-kentico/editing-components-in-xperience-by-kentico#attachment-selector)


## Form components
In Kentico Xperience 13, you assigned editing components using the `EditingComponent` attribute and configured them via the `EditingComponentProperty` attribute.
C#
**Kentico Xperience 13 usage**
Copy
```
using Kentico.Forms.Web.Mvc;

// Assigns the 'TextAreaComponent' as the editing component of the 'Text' property
[EditingComponent(TextAreaComponent.IDENTIFIER)]
[EditingComponentProperty(nameof(TextAreaProperties.Placeholder), "Enter your text here...")]
public string Text { get; set; }
```

In Xperience by Kentico, each editing component has a dedicated attribute that allows you to configure all properties in the constructor.
Update the code by mapping the corresponding `EditingComponentProperty` calls to individual attribute constructor parameters. Keep in mind that some properties may be renamed, changed, or added in the new product:
C#
**Xperience by Kentico usage**
Copy
```
using Kentico.Xperience.Admin.Base.FormAnnotations;

// Assigns the 'TextAreaComponent' as the editing component of the 'Text' property
[TextAreaComponent(WatermarkText = "Enter your text here...")]
public string  Text { get; set; }
```

**Builder compatibility mode**
Existing [Page Builder](documentation/developers-and-admins/development/builders/page-builder) and [Form Builder](documentation/developers-and-admins/development/builders/form-builder) components with properties using the old `EditingComponent` attributes remain functional, and are displayed in “compatibility mode” within the Page or Form Builder interface.
However, this compatibility mode is only intended as a short-term solution to help get [upgraded projects](documentation/developers-and-admins/upgrade-to-xperience-by-kentico) running as soon as possible. This option will stop working when support for Kentico Xperience 13 ends. We **strongly recommend** that you update your components to the new Xperience by Kentico framework.
## Selectors
### Page selector
In Kentico Xperience 13, you assigned the page selector using the `EditingComponent` attribute and configured it via the `EditingComponentProperty` attribute.
C#
**Kentico Xperience 13 usage**
Copy
```
using Kentico.Forms.Web.Mvc;
using Kentico.Components.Web.Mvc.FormComponents;

[EditingComponent(PageSelector.IDENTIFIER)]
[EditingComponentProperty(nameof(PageSelectorProperties.RootPath), "/Articles")]
[EditingComponentProperty(nameof(PageSelectorProperties.MaxPagesLimit), 0)]
// Returns a list of page selector items (node GUIDs)
public IEnumerable<PageSelectorItem> Pages { get; set; } = new List<PageSelectorItem>();
```

In Xperience by Kentico, the page selector has a dedicated `WebPageSelectorComponent` attribute that allows you to configure all properties in the constructor. Also, the return type was changed to a collection of `WebPageRelatedItem` objects.
Update the code by mapping the corresponding `EditingComponentProperty` calls to individual attribute constructor parameters. The properties of the returned object were not changed and will be updated automatically: 
C#
**Xperience by Kentico usage**
Copy
```
using System.Collections.Generic;
using System.Linq;

using CMS.Websites;

using Kentico.Xperience.Admin.Websites.FormAnnotations;

// Returns a list of selected page items (node GUIDs)
[WebPageSelectorComponent(TreePath = "/Articles", MaximumPages = 5)]
public IEnumerable<WebPageRelatedItem> Pages { get; set; } = new List<WebPageRelatedItem>();
```

### URL selector
In Kentico Xperience 13, you assigned the URL selector using the `EditingComponent` attribute and configured it via the `EditingComponentProperty` attribute.
C#
**Kentico Xperience 13 usage**
Copy
```
using Kentico.Forms.Web.Mvc;
using Kentico.Components.Web.Mvc.FormComponents;

[EditingComponent(UrlSelector.IDENTIFIER)]
[EditingComponentProperty(nameof(UrlSelectorProperties.Tabs), ContentSelectorTabs.Attachment | ContentSelectorTabs.Media)]
[EditingComponentProperty(nameof(UrlSelectorProperties.MediaAllowedExtensions), ".gif;.png;.jpg;.jpeg")]
[EditingComponentProperty(nameof(UrlSelectorProperties.AttachmentAllowedExtensions), ".gif;.png;.jpg;.jpeg")]
[EditingComponentProperty(nameof(UrlSelectorProperties.MediaLibraryName), "Graphics")]
// Returns a string containing the relative URL of the selected page
public string Url { get; set; }
```

In Xperience by Kentico, the URL selector contains a few changes:
  * you can only use it to select page URLs. Attachments and files from media libraries are not supported. If you have page or page template configurations with media file URLs stored using the selector, the data persists until users make a different selection using the new selector and save the changes.
  * the URL selector now has a dedicated `UrlSelectorComponent` attribute that allows you to configure all properties in the constructor. However, due to the reduction in available selection scopes, the selector no longer allows you to configure the: 
    1. available selection tabs, previously set via `UrlSelectorProperties.Tabs`.
    2. extension filter for selectable types of media files and attachments, previously set via `UrlSelectorProperties.MediaAllowedExtensions` and `UrlSelectorProperties.AttachmentAllowedExtensions`.
    3. filter for media libraries from which users can select the media files, previously set via `UrlSelectorProperties.MediaLibraryName`.


The following snippet shows an example of the new usage:
C#
**Xperience by Kentico usage**
Copy
```
using Kentico.Xperience.Admin.Content.FormAnnotations;

// Returns a string containing the relative URL of the selected page
[UrlSelectorComponent(Label = "Select a page")]
public string PageUrl { get; set; }
```

### Object selector
In Kentico Xperience 13, you assigned the object selector using the `EditingComponent` attribute and configured it via the `EditingComponentProperty` attribute.
C#
**Kentico Xperience 13 usage**
Copy
```
using Kentico.Forms.Web.Mvc;
using Kentico.Components.Web.Mvc.FormComponents;

[EditingComponent(ObjectSelector.IDENTIFIER)]
[EditingComponentProperty(nameof(ObjectSelectorProperties.ObjectType), "cms.user")]
[EditingComponentProperty(nameof(ObjectSelectorProperties.WhereConditionProviderType), typeof(ObjectWhere))]
[EditingComponentProperty(nameof(ObjectSelectorProperties.OrderBy), new string[] { "UserName DESC" })]
public IEnumerable<ObjectSelectorItem> Users { get; set; } = new List<ObjectSelectorItem>();

public class ObjectWhere : IObjectSelectorWhereConditionProvider
{
    // Where condition limiting the selectable objects
    public WhereCondition Get() => new WhereCondition().WhereStartsWith("UserName", "a");
}
```

In Xperience by Kentico, the object selector has a dedicated `ObjectSelectorComponent` attribute that allows you to configure all properties in the constructor. Also, the return type was changed to a collection of `ObjectRelatedItem` objects.
To update the code, map the corresponding `EditingComponentProperty` calls to individual attribute constructor parameters:
C#
Copy
```
using Kentico.Xperience.Admin.Content.FormAnnotations;

[ObjectSelectorComponent("cms.user", WhereConditionProviderType = typeof(ObjectWhere), OrderBy = new string[] { "UserName DESC" })]
public IEnumerable<ObjectRelatedItem> Users { get; set; } = new List<ObjectRelatedItem>();

public class ObjectWhere : IObjectSelectorWhereConditionProvider
{
    // Where condition limiting the selectable objects
    public WhereCondition Get() => new WhereCondition().WhereStartsWith("UserName", "a");
}
```

### General selector
In Kentico Xperience 13, you assigned the General selector using the `EditingComponent` attribute and configured it via the `EditingComponentProperty` attribute.
C#
**Kentico Xperience 13 usage**
Copy
```
[EditingComponent(GeneralSelector.IDENTIFIER)]
[EditingComponentProperty(nameof(GeneralSelectorProperties.DataProviderType), typeof(UserGeneralSelectorDataProvider))]
public IEnumerable<GeneralSelectorItem> UserOptions { get; set; } = new List<GeneralSelectorItem>();
```

In Xperience by Kentico, the General selector has a dedicated `GeneralSelectorComponent` attribute that allows you to configure all properties in the constructor. Instead of returning a collection of _GeneralSelectorItem_ objects, General selector now returns a collection of _string_ objects, which contain the values of the selected items.
C#
**Xperience by Kentico usage**
Copy
```
// Assigns the General selector as the property's editing component
[GeneralSelectorComponent(
            dataProviderType : typeof(UserGeneralSelectorDataProvider),
            Label = "Users",
            Placeholder = "Choose a user")]
public IEnumerable<string> UserOptions { get; set; }
```

### Media file selector
There are no plans to make the media file selector available in Xperience by Kentico.
### Path selector
There are no plans to make the path selector available in Xperience by Kentico.
If you need to retrieve a collection of [tree paths](documentation/developers-and-admins/development/content-retrieval/retrieve-page-content#filter-pages-based-on-content-tree-structure), we recommend using the [Page selector](documentation/developers-and-admins/upgrade-to-xperience-by-kentico/editing-components-in-xperience-by-kentico#page-selector) and obtaining paths from the selected pages:
C#
**Use the page selector to obtain a collection of tree paths**
Copy
```
// Retrieves the GUIDs of the selected pages from the 'Pages' property
List<Guid> pageGuids = model?.Properties?.Pages?
                                           .Select(i => i.WebPageGuid)
                                           .ToList();

// Retrieves the data of the selected article pages
IEnumerable<string> articlePaths = (await contentRetriever.RetrievePagesByGuids<Article>(pageGuids))
                                                  .Select(t => t.SystemFields.WebPageItemTreePath);
```

### Attachment selector
There are no plans to make the attachment selector available for Xperience by Kentico. Page attachments are not supported by the system – use [Content hub](documentation/business-users/content-hub) instead.