# UI form component reference extractors
  * [ Copy page link ](documentation/developers-and-admins/customization/extend-the-administration-interface/ui-form-components/ui-form-component-reference-extractors#) | [Get HelpService ID](documentation/developers-and-admins/customization/extend-the-administration-interface/ui-form-components/ui-form-component-reference-extractors#)
Core MVC 5


[✖](documentation/developers-and-admins/customization/extend-the-administration-interface/ui-form-components/ui-form-component-reference-extractors# "Close page link panel") [Copy to clipboard](documentation/developers-and-admins/customization/extend-the-administration-interface/ui-form-components/ui-form-component-reference-extractors#)
The system tracks [usage of content items](documentation/business-users/content-hub/content-items#track-usage-of-content-items) to help editors determine the impact of modifying content items on the published data. The system tracks all content item references created using the default system form components ([combined content selector](documentation/developers-and-admins/customization/extend-the-administration-interface/ui-form-components/reference-admin-ui-form-components#combined-content-selector), [rich text editor](documentation/business-users/rich-text-editor)). For all custom [UI form components](documentation/developers-and-admins/customization/extend-the-administration-interface/ui-form-components) that contain links to content item assets or references to content items (e.g., a custom URL selector, a custom rich text inline editor), you need to implement a custom reference extractor.
## Implement reference extractors
Implement a custom class based on where you want to extract references from:
  * [Page and Email Builder component properties](documentation/developers-and-admins/customization/extend-the-administration-interface/ui-form-components/ui-form-component-reference-extractors#page-and-email-builder-component-properties-extractors)
  * [Content type fields](documentation/developers-and-admins/customization/extend-the-administration-interface/ui-form-components/ui-form-component-reference-extractors#content-type-fields-extractors)


### Page and Email Builder component properties extractors
To extract references from Page Builder component properties ([widget](documentation/developers-and-admins/development/builders/page-builder/widgets-for-page-builder/widget-properties), [section](documentation/developers-and-admins/development/builders/page-builder/sections-for-page-builder/section-properties), and [page template](documentation/developers-and-admins/development/builders/page-builder/page-templates-for-page-builder/page-template-properties) properties) and [Email Builder component](documentation/developers-and-admins/development/builders/email-builder/develop-email-builder-components) properties, implement the `IContentItemReferenceExtractor` interface in a [custom class](documentation/developers-and-admins/customization/integrate-custom-code). Within the `Extract` method, process the content of the property and return a collection of `ContentItemReference` objects, which contain GUID `Identifier` of content items that are linked in the property.
You need to [register the extractor](documentation/developers-and-admins/customization/extend-the-administration-interface/ui-form-components/ui-form-component-reference-extractors#register-reference-extractors) and [assign the extractor](documentation/developers-and-admins/customization/extend-the-administration-interface/ui-form-components/ui-form-component-reference-extractors#assign-reference-extractors-to-page-and-email-builder-component-properties) to all properties where references to content items were created using custom form components.
### Content type fields extractors
To extract references from [content type fields](documentation/developers-and-admins/customization/field-editor), implement the `IFormFieldContentItemReferenceExtractor` interface in a [custom class](documentation/developers-and-admins/customization/integrate-custom-code). The interface requires you to implement the following methods:
  * `Extract` – processes the content of the field and returns a collection of `ContentItemReference` objects, which contain GUID `Identifier` of content items that are linked in the field.
  * `CanExtractReferences` – returns a _bool_ value that specifies whether the extractor supports the field type specified in the `FormFieldInfo` parameter. To determine whether a field contains references that can be extracted, use the field’s data type (`fieldInfo.DataType` property) and the form control used by the field (`fieldInfo.GetComponentName()` method).


You need to [register the extractor](documentation/developers-and-admins/customization/extend-the-administration-interface/ui-form-components/ui-form-component-reference-extractors#register-reference-extractors). After any content item is modified, content type fields of the item are checked against all registered `IFormFieldContentItemReferenceExtractor` implementations and their `CanExtractReferences` methods to determine if references need to be extracted from said fields and the extraction is performed using the `Extract` method of the suitable extractor.
The following example shows the implementation of the default reference extractor used by the system to extract references from rich text fields:
C#
**Default extractor for references in the system rich text fields**
Copy
```
using System;
using System.Collections.Generic;
using System.Linq;
using System.Text.RegularExpressions;

using AngleSharp.Html.Dom;
using AngleSharp.Html.Parser;

using CMS;
using CMS.ContentEngine;
using CMS.Core;
using CMS.FormEngine;

...

public class ContentItemReferenceExtractor : IFormFieldContentItemReferenceExtractor
{
    private const string CONTENT_ITEM_GROUP_NAME = "ContentItemGuid";
    private const string GUID_REGEX = @"[0-9a-fA-F]{8}-[0-9a-fA-F]{4}-[0-9a-fA-F]{4}-[0-9a-fA-F]{4}-[0-9a-fA-F]{12}";

    private readonly HtmlParser htmlParser;
    private readonly Regex contentItemLinkRegex = new Regex(@$"getContentAsset\/(?<{CONTENT_ITEM_GROUP_NAME}>{GUID_REGEX})\/{GUID_REGEX}\/", RegexOptions.Compiled | RegexOptions.IgnoreCase);

    public ContentItemReferenceExtractor()
    {
        htmlParser = new HtmlParser();
    }

    // Returns whether the field in the parameter is supported by this extractor
    public bool CanExtractReferences(FormFieldInfo field)
    {
        return string.Equals(field.DataType, FieldDataType.RichTextHTML, StringComparison.InvariantCultureIgnoreCase) ||
            (string.Equals(field.GetComponentName(), RichTextEditorConstants.IDENTIFIER, StringComparison.OrdinalIgnoreCase) &&
                (field.DataType.Equals(FieldDataType.Text, StringComparison.Ordinal) || field.DataType.Equals(FieldDataType.LongText, StringComparison.Ordinal)));
    }


    // Returns a collection of reference object found in the field
    public IEnumerable<ContentItemReference> Extract(object fieldValue)
    {
        if (fieldValue is not string html)
        {
            return Enumerable.Empty<ContentItemReference>();
        }

        var referencesGuids = new HashSet<Guid>();
        var document = htmlParser.ParseDocument(html);

        referencesGuids.UnionWith(document.Images.Where(image => contentItemLinkRegex.IsMatch(image.Source))
            .Select(image => GetContentItemReferenceGuidFromLink(image.Source)));

        var anchors = document.QuerySelectorAll("a").Select(anchors => anchors as IHtmlAnchorElement);

        referencesGuids.UnionWith(anchors.Where(anchor => contentItemLinkRegex.IsMatch(anchor.Href))
            .Select(anchor => GetContentItemReferenceGuidFromLink(anchor.Href)));

        return referencesGuids.Select(guid => new ContentItemReference { Identifier = guid });
    }

    // Converts plaintext links to GUIDs
    private Guid GetContentItemReferenceGuidFromLink(string source)
    {
        return Guid.Parse(contentItemLinkRegex.Match(source).Groups[CONTENT_ITEM_GROUP_NAME].Value);
    }
}
```

**Universal extractors**
It is generally recommended to use individual extractors for distinct fields or properties. However, as the `IFormFieldContentItemReferenceExtractor` inherits from the `IContentItemReferenceExtractor`, you can create a universal extractor for all fields and properties where the references are stored in the same format.
## Register reference extractors
To register a reference extractor, use the `RegisterImplementation` [assembly attribute](documentation/developers-and-admins/customization/integrate-custom-code#enable-class-discovery). This attribute ensures the reference extractor is recognized by the system.
C#
**Registration of an extractor**
Copy
```
[assembly: RegisterImplementation(typeof(IFormFieldContentItemReferenceExtractor),
                                  typeof(ContentItemReferenceExtractor),
                                  Lifestyle = Lifestyle.Singleton,
                                  Priority = RegistrationPriority.Default)]
```

### Assign reference extractors to Page and Email Builder component properties
After you implement and register an instance of the `IContentItemReferenceExtractor`, you need to assign it to all Page Builder component properties ([widget](documentation/developers-and-admins/development/builders/page-builder/widgets-for-page-builder/widget-properties), [section](documentation/developers-and-admins/development/builders/page-builder/sections-for-page-builder/section-properties), and [page template](documentation/developers-and-admins/development/builders/page-builder/page-templates-for-page-builder/page-template-properties) properties) or [Email Builder component](documentation/developers-and-admins/development/builders/email-builder/develop-email-builder-components) properties that may contain the corresponding type of references to content items.
Use the `TrackContentItemReference` attribute and specify the type of your extractor as a parameter. Two versions of the attribute are available in different namespaces:
  * `Kentico.PageBuilder.Web.Mvc` – for Page Builder component properties
  * `Kentico.EmailBuilder.Web.Mvc` – for Email Builder component properties


C#
**Properties model**
Copy
```
// Page Builder widget properties model example
public class CustomPageBuilderWidgetProperties : IWidgetProperties
{
    [CustomEditingComponent]
    [Kentico.PageBuilder.Web.Mvc.TrackContentItemReference(typeof(CustomExtractor))]
    public string PropertyName { get; set; }
}

// Email Builder widget properties model example
public class CustomEmailBuilderWidgetProperties : IEmailWidgetProperties
{
    [CustomEditingComponent]
    [Kentico.EmailBuilder.Web.Mvc.TrackContentItemReference(typeof(CustomExtractor))]
    public string PropertyName { get; set; }
}
```