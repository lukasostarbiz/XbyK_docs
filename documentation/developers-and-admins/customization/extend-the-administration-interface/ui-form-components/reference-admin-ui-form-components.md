---
source: https://docs.kentico.com/documentation/developers-and-admins/customization/extend-the-administration-interface/ui-form-components/reference-admin-ui-form-components
scrape_date: 2026-01-22
---

  * [Home](/documentation)
  * [Developers and admins](/documentation/developers-and-admins)
  * [Customization](/documentation/developers-and-admins/customization)
  * [Extend the administration interface](/documentation/developers-and-admins/customization/extend-the-administration-interface)
  * [UI form components](/documentation/developers-and-admins/customization/extend-the-administration-interface/ui-form-components)
  * Reference - Admin UI form components 


# Reference - Admin UI form components
This page lists all [form components](/documentation/developers-and-admins/customization/extend-the-administration-interface/ui-form-components) that are available in new installations of Xperience. For details on programmatically configuring these components in component configuration dialogs, see [Editing components](/documentation/developers-and-admins/customization/extend-the-administration-interface/ui-form-components/editing-components).
Form component attributes are located in the
  * `Kentico.Xperience.Admin.Base.FormAnnotations`
  * `Kentico.Xperience.Admin.Content.FormAnnotations`
  * `Kentico.Xperience.Admin.DigitalMarketing.FormAnnotations`


namespaces.
  * [Selectors](#selectors)
    * [Combined content selector](#combined-content-selector)
    * [Content item folder selector](#content-item-folder-selector)
    * [Email selector](#email-selector)
    * [Object selector](#object-selector)
    * [Page selector](#page-selector)
    * [URL selector](#url-selector)
    * [Smart folder selector](#smart-folder-selector)
    * [Tag selector](#tag-selector)
    * [General selector](#general-selector)
  * [Components](#components)
    * [Checkbox](#checkbox)
    * [Code editor](#code-editor)
    * [Dropdown selector](#dropdown-selector)
    * [Date input](#date-input)
    * [Date and time input](#date-and-time-input)
    * [Date and time with label](#date-and-time-with-label)
    * [Decimal number input](#decimal-number-input)
    * [Link](#link)
    * [Number input](#number-input)
    * [Number with label](#number-with-label)
    * [Password](#password)
    * [Rich text editor](#rich-text-editor)
    * [Radio group component](#radio-group-component)
    * [Text area](#text-area)
    * [Text input](#text-input)
    * [Text with label](#text-with-label)
    * [Extension selector](#extension-selector)


## Selectors
### Combined content selector
**Field data type** : Pages and reusable content  
**C# data type** : IEnumerable<ContentItemReference>  
**Form component attribute** : ContentItemSelectorComponent
Enables users to select and associate content items from the [content hub](/documentation/business-users/content-hub) and pages from the [content tree of any website channel](/documentation/business-users/website-content), depending on the configuration.
When used as an [editing component](/documentation/developers-and-admins/customization/extend-the-administration-interface/ui-form-components/editing-components), returns a collection of `ContentItemReference` objects that contain GUIDs of the selected content items.
#### Configuration properties
  * **Content item scoping** – The selector offers multiple ways to scope the set of content items available for selection. 
    * [ContentTypeName](#CiSelectorCTName) – the code name of the content type. Scopes the selector to offer items of the given content type.
    * [ContentTypeNames[]](#CiSelectorCTNameMultiple) – an array of content type code names. Scopes the selector to offer items of the given content types.
    * [IContentTypesFilter implementation](#CiSelectorCTFilter) – an implementation of the `IContentTypesFilter` interface that scopes the offered items to those whose content type is allowed by the filter.
    * [IReusableFieldSchemasFilter implementation](#CiSelectorReusable) – an implementation of the `IReusableFieldSchemasFilter` interface that scopes the offered items to those whose content type uses one of the specified [reusable field schemas](/documentation/developers-and-admins/development/content-types/reusable-field-schemas).
  * **AllowContentItemCreation** – determines whether the selector displays a button that allows users to create new content items. True by default. 
    * This options is not supported when scoping the selection using reusable field schemas.
  * **DefaultViewMode** – sets the selector’s default view mode. Use the values from the `ContentItemSelectorViewMode` enum: 
    * `Auto` – selected based on the content type displayed in the selector (List for standard items, Grid for images or other assets).
    * `Grid` – a gallery showing thumbnail images for the selectable content items (assets).
    * `List` – a list of content items.
  * **MaximumItems** – sets the maximum number of selectable items. If not specified, the number is not limited.
  * **MinimumItems** – sets the minimum number of selectable items. If not specified, the number is not limited.


#### Content type-based scoping and retrieval 
C#
**Scope the selection using content types**
Copy
```
// Assigns the combined content selector as the property's editing component
// Enables selection from content items of the 'Coffee' content type
[ContentItemSelectorComponent(Coffee.CONTENT_TYPE_NAME, Label = "Selected products", Order = 1)]
public IEnumerable<ContentItemReference> SelectedProducts { get; set; } = new List<ContentItemReference>();
```

C#
**Retrieve selection scoped to content type**
Copy
```
public class ContentItemSelectorUsage : ViewComponent
{
    private readonly IContentRetriever contentRetriever;

    public ContentItemSelectorUsage(IContentRetriever contentRetriever)
    {
        this.contentRetriever = contentRetriever;
    }

    public async Task<ViewViewComponentResult> InvokeAsync(ComponentViewModel<CustomWidgetProperties> model)
    {
        // Gets the GUIDs from the annotated property
        var coffeeGuids = model?.Properties?.SelectedProducts.Select(i => i.Identifier).ToList();

        // Retrieves the data of the selected content type
        IEnumerable<Coffee> result = await contentRetriever.RetrieveContentByGuids<Coffee>(coffeeGuids,
            new RetrieveContentParameters
            {
                LinkedItemsMaxLevel = 1
            });

         // Custom logic...

        return View("...");
    }
}
```

#### Content type-based scoping and retrieval for multiple content types 
C#
**Scope the selection using content types**
Copy
```
// Assigns the combined content selector as the property's editing component
// Enables selection from content items of 'Coffee' and 'Grinder' content types
[ContentItemSelectorComponent(new string[] { Coffee.CONTENT_TYPE_NAME, Grinder.CONTENT_TYPE_NAME },
                                             Label = "Selected products",
                                             Order = 1)]
public IEnumerable<ContentItemReference> SelectedProducts { get; set; } = new List<ContentItemReference>();
```

C#
**Retrieve selection scoped to multiple content types**
Copy
```
public class ContentItemSelectorUsage : ViewComponent
{
    private readonly IContentRetriever contentRetriever;

    public ContentItemSelectorUsage(IContentRetriever contentRetriever)
    {
        this.contentRetriever = contentRetriever;
    }

    public async Task<ViewViewComponentResult> InvokeAsync(ComponentViewModel<CustomWidgetProperties> model)
    {
        // Gets the GUIDs from the annotated property
        var productGuids = model?.Properties?.SelectedProducts.Select(i => i.Identifier).ToList();

        // Retrieves the data of selected content items
        // Use RetrieveContentOfContentTypes method with additional Where filtering for multiple content types
        var contentTypes = new[] { Coffee.CONTENT_TYPE_NAME, Grinder.CONTENT_TYPE_NAME };
        var result = await contentRetriever.RetrieveContentOfContentTypes<IContentItemFieldsSource>(
            contentTypes,
            new RetrieveContentOfContentTypesParameters
            {
                LinkedItemsMaxLevel = 1
            },
            query => query.Where(w => w.WhereIn("ContentItemGUID", productGuids)),
            new RetrievalCacheSettings(cacheItemNameSuffix:
                $"{nameof(RetrieveContentOfContentTypesQueryParameters.Where)}|{nameof(WhereParameters.WhereIn)}|ContentItemGUID|productGuids")
        );

        // Iterates over selected items as typed objects
        foreach (var item in result)
        {
            if (item is Coffee coffee)
            {
                // Handle coffee data
            }
            if (item is Grinder grinder)
            {
                // Handle grinder data
            }
        }

        return View("...");
    }
}
```

#### Content type-based scoping and retrieval for multiple content types using ‘IContentTypesFilter’ 
C#
**Scope the selection using ‘IContentTypesFilter’**
Copy
```
// Assigns the combined content selector as the property's editing component
// Enables selection from content items whose content types are allowed by the filter
[ContentItemSelectorComponent(typeof(GalleryFilter), Label = "Selected assets", Order = 1)]
public IEnumerable<ContentItemReference> AssetsToDisplay { get; set; } = new List<ContentItemReference>();

// Filters the set of content items available in the selector
// to content types that have a field of the 'ContentItemAsset' type
public class GalleryFilter : IContentTypesFilter
{
    public IEnumerable<Guid> AllowedContentTypeIdentifiers => DataClassInfoProvider
        .GetClasses()
        .WhereEquals(nameof(DataClassInfo.ClassType), ClassType.CONTENT_TYPE)
        .WhereEquals(nameof(DataClassInfo.ClassContentTypeType), ClassContentTypeType.REUSABLE)
        .GetEnumerableTypedResult()
        .Where(x => FormHelper
            .GetFormInfo(x.ClassName, false)
            .GetFields(true, false, includeDummyFields: false)
            .Any(y => y.DataType.Equals(FieldDataType.ContentItemAsset))
            )
        .Select(x => x.ClassGUID)
        .ToList();
}
```

C#
**Retrieve selection scoped to types allowed by ‘IContentTypesFilter’**
Copy
```
public class ContentItemSelectorUsage : ViewComponent
{
    private readonly IContentRetriever contentRetriever;

    public ContentItemSelectorUsage(IContentRetriever contentRetriever)
    {
        this.contentRetriever = contentRetriever;
    }

    public async Task<ViewViewComponentResult> InvokeAsync(ComponentViewModel<CustomWidgetProperties> model)
    {
        // Gets the GUIDs from the annotated property
        var assetsToDisplayGuids = model?.Properties?.AssetsToDisplay.Select(i => i.Identifier).ToList();

        // Gets the names of content types allowed by the filter
        var filter = new GalleryFilter();
        var contentTypeNames = DataClassInfoProvider
                        .GetClasses()
            .WhereIn(nameof(DataClassInfo.ClassGUID), filter.AllowedContentTypeIdentifiers)
            .Select(x => x.ClassName)
            .ToList();

        // Retrieves data of the selected content items
        var result = await contentRetriever.RetrieveContentOfContentTypes<IContentItemFieldsSource>(
            contentTypeNames,
            new RetrieveContentOfContentTypesParameters
            {
                LinkedItemsMaxLevel = 1
            },
            query => query.Where(w => w.WhereIn("ContentItemGUID", assetsToDisplayGuids)),
            new RetrievalCacheSettings(cacheItemNameSuffix:
                $"{nameof(RetrieveContentOfContentTypesQueryParameters.Where)}|{nameof(WhereParameters.WhereIn)}|ContentItemGUID|assetsToDisplayGuids")
        );

        // Iterates over selected items as typed objects. 'Image' and 'Video'
        // are generated model classes representing content types that contain
        // a content item asset field.
        foreach (var item in result)
        {
            if (item is Image image)
            {
                // Handle image data
            }
            if (item is Video video)
            {
                // Handle video data
            }
        }

        return View("...");
    }
}
```

#### Reusable field schema-based scoping and retrieval 
C#
**Scope the selection using reusable field schemas**
Copy
```
// Assigns the combined content selector as the property's editing component
// Enables selection from content items whose content type uses the 'ProductFields' schema
[ContentItemSelectorComponent(typeof(ProductCardSchemaFilter), Label = "Selected products", Order = 1)]
public IEnumerable<ContentItemReference> SelectedProducts { get; set; } = new List<ContentItemReference>();

// Filters the set of content items available in the selector
// to content types that use the 'ProductFields' reusable field schema
public class ProductCardSchemaFilter : IReusableFieldSchemasFilter
{
    IEnumerable<string> IReusableFieldSchemasFilter.AllowedSchemaNames => new List<string> { IProductFields.REUSABLE_FIELD_SCHEMA_NAME };
}
```

C#
**Retrieve selection scoped to reusable field schema**
Copy
```
public class ContentItemSelectorUsage : ViewComponent
{
    private readonly IContentRetriever contentRetriever;

    public ContentItemSelectorUsage(IContentRetriever contentRetriever)
    {
        this.contentRetriever = contentRetriever;
    }

    public async Task<ViewViewComponentResult> InvokeAsync(ComponentViewModel<CustomWidgetProperties> model)
    {
        // Gets the GUIDs from the annotated property
        var selectedProductGuids = model?.Properties?.SelectedProducts.Select(i => i.Identifier).ToList();

        // Retrieves the selected data using reusable field schema filtering
        var schemaNames = new[] { IProductFields.REUSABLE_FIELD_SCHEMA_NAME };
        IEnumerable<IProductFields> result = await contentRetriever.RetrieveContentOfReusableSchemas<IProductFields>(
            schemaNames,
            new RetrieveContentOfReusableSchemasParameters
            {
                LinkedItemsMaxLevel = 1
            },
            query => query.Where(w => w.WhereIn("ContentItemGUID", selectedProductGuids)),
            new RetrievalCacheSettings(cacheItemNameSuffix:
                $"{nameof(RetrieveContentOfContentTypesQueryParameters.Where)}|{nameof(WhereParameters.WhereIn)}|ContentItemGUID|selectedProductGuids")
        );

         // Custom logic...

        return View("...");
    }
}
```

### Content item folder selector
**C# data type** : Int32  
**Form component attribute** : ContentFolderSelectorComponent
Enables users to select a [content folder](/documentation/business-users/content-hub/content-hub-folders#content-folders). Typically, this selector is used to set the location of reusable content items. Smart folders are **not** available in the selector.
Returns the ID of the selected folder.
#### Configuration properties
  * **DisabledFolderIdsFilter** – optionally allows you to specify which folders are disabled for selection. Create a filter class that implements the `IContentFolderIdsFilter` interface and assign its type into the property.
  * **Inline** – by default, the selector displays a text field showing the path of the currently selected folder, and a _Select_ button that opens a dialog window with a tree where users can choose the folder. If the _Inline_ property is set to _true_ , only the folder selection tree is displayed directly after the field’s label.
  * **RootName** – overrides the name displayed for the root node in the selector’s folder tree. If not specified the default _Root_ name is used.
  * **WorkspaceName** – specifies the name of the workspace for which folders should be listed. If not specified, the _KenticoDefault_ name is used.


C#
**Assignment in a model class**
Copy
```
// Assigns the content folder selector as the property's editing component
[ContentFolderSelectorComponent(Label = "Location", Inline = true, Order = 1, WorkspaceName = "MyWorkspace")]
public int ContentItemFolderId { get; set; }
```

C#
**Content folder selector with a disabled folder ID filter**
Copy
```
// Assigns the content folder selector as the property's editing component
[ContentFolderSelectorComponent(DisabledFolderIdsFilter = typeof(DisabledContentFoldersFilter), Label = "Location", Order = 1)]
public int ContentItemFolderId { get; set; }

// ...

// Specifies folders that are disabled for selection
public class DisabledContentFoldersFilter : IContentFolderIdsFilter
{
    private readonly IInfoProvider<ContentFolderInfo> contentFolderInfoProvider;

    public DisabledContentFoldersFilter(IInfoProvider<ContentFolderInfo> contentFolderInfoProvider)
    {
        this.contentFolderInfoProvider = contentFolderInfoProvider;
    }

    public IEnumerable<int> FolderIds
    {
        get
        {
            // Gets a folder with the 'Unselectable' code name
            ContentFolderInfo folder = contentFolderInfoProvider.Get("Unselectable");

            return new List<int> { folder.ContentFolderID };
        }
    }
}
```

### Email selector
**Field data type** : Emails (only available for the fields of [module classes](/documentation/developers-and-admins/customization/object-types), not in the field editor for content types)  
**C# data type** : IEnumerable<EmailRelatedItem>  
**Form component attribute** : EmailSelectorComponent
Enables users to select [emails](/documentation/business-users/digital-marketing/emails) from an email channel.
When used as an editing component, returns a collection of `EmailRelatedItem` objects that point to the selected emails. Each `EmailRelatedItem` contains an `EmailGuid` property with the GUID identifier of the selected email’s configuration object (`EmailConfigurationInfo`).
#### Configuration properties
  * **MaximumEmails** – sets the maximum number of selected emails. If not specified, the default value is 1 (single email selection). Setting the value to 0 means the number of selected emails is not limited.
  * **Sortable** – enables or disables the ordering of the selected emails.
  * Allowed email purposes – the following properties allow you to restrict the selection to emails with specific [email purposes](/documentation/business-users/digital-marketing/emails#email-purposes). Set the purpose using the string constants from `CMS.EmailLibrary.EmailPurposeOptions`. 
    * **AllowedEmailPurpose** – string property that allows you to set one allowed email purpose.
    * **AllowedEmailPurposes** – an array of strings for multiple allowed email purposes.
  * **ForPreview** – indicates whether the selector allows emails that do not have a published version yet (i.e., emails with the initial _Draft_ status). False by default.


C#
**Assignment in a model class**
Copy
```
// Assigns the email selector as the property's editing component
// Returns a collection of email items (objects that contain the GUIDs of selected emails)
[EmailSelectorComponent(
    AllowedEmailPurposes = new[] { EmailPurposeOptions.FORMAUTORESPONDER, EmailPurposeOptions.AUTOMATION },
    Sortable = true,
    MaximumEmails = 3)]
public IEnumerable<EmailRelatedItem> Emails { get; set; } = new List<EmailRelatedItem>();
```

### Object selector
This form component is currently usable only in Page or Form Builder, and [model-based edit pages](/documentation/developers-and-admins/customization/extend-the-administration-interface/ui-pages/ui-pages-with-forms) or listing filter models in the Xperience administration.
**C# data type** : IEnumerable<ObjectRelatedItem>  
**Form component attribute** : ObjectSelectorComponent
Enables users to select any object types from the Xperience database.
Returns a collection of `ObjectRelatedItem` objects that represent the selected objects from the Xperience database. Each `ObjectRelatedItem` object contains an `ObjectGuid` or `ObjectCodeName` property (depending on the `IdentifyObjectByGuid` property) which contains the identifier of a selected object.
#### Configuration properties
  * **ObjectType** – a string identifier that sets the code name of the object type listed in the selector.
  * **IdentifyObjectByGuid** – a boolean property that indicates whether the returned object contains the GUID identifier of the selected object instead of the code name. By default, the option is disabled ( false ) and the returned object contains the object code name. It is recommended to identify objects by their code names for optimal performance.
  * **WhereConditionProviderType** – a custom type that allows you to filter what data is available in the object selector using a custom Where condition. The specified condition class must implement the `IObjectSelectorWhereConditionProvider` interface and define its `Get` method. The `Get` method specifies the _where_ condition applied to the data before the list of objects is shown to users.
  * **OrderBy** – Defines a list of columns by which the data should be sorted, e.g., `["UserIsExternal", "UserName DESC"]`
  * **MaximumItems** – sets the maximum number of selectable items (objects). If not specified, the default value is 1 (single object selection). 
    * 0 – no limit.
    * _n_ – at most _n_ objects can be selected at once.
  * **Placeholder** – a string property that sets the placeholder text displayed before selecting any items.


#### Example
C#
**Assignment in model classes**
Copy
```
[ObjectSelectorComponent("cms.user", WhereConditionProviderType = typeof(ObjectWhere),
                                     OrderBy = new string[] { "UserName DESC" })]
public IEnumerable<ObjectRelatedItem> Users { get; set; } = new List<ObjectRelatedItem>();

public class ObjectWhere : IObjectSelectorWhereConditionProvider
{
    // Where condition limiting the objects
    public WhereCondition Get() => new WhereCondition().WhereStartsWith("UserName", "a");

}
```

C#
**Selection retrieval**
Copy
```
public class ObjectSelectorUsage : ViewComponent
{
    private readonly IUserInfoProvider userInfoProvider;

    public ObjectSelectorUsage(IUserInfoProvider userInfoProvider)
    {
        this.userInfoProvider = userInfoProvider;
    }

    public IViewComponentResult Invoke(ComponentViewModel<CustomWidgetProperties> model)
    {
        // Retrieves the guid of the selected user from the properties
        Guid guid = model?.Properties?.Users?.FirstOrDefault()?.ObjectGuid ?? Guid.Empty;
        // Retrieves the corresponding user object
        var user = userInfoProvider.Get(guid);

        // Custom logic...

        return View("...");
    }
}
```

#### Object selector variants
In addition to the general object selector component, the system provides variant implementations for specific scenarios. See the following table for details:
Selector |  Component attribute |  Description  
---|---|---  
Object code name selector |  ObjectCodeNameSelectorComponent |  Returns object code names directly instead of `ObjectRelatedItem` instances.  
Object GUID selector |  ObjectGuidSelectorComponent |  Returns object GUIDs directly instead of `ObjectRelatedItem` instances.  
Object ID selector |  None, assignable only via the [field editor](/documentation/developers-and-admins/customization/field-editor). |  Returns the ID of a single selected object, as an `int` value.  
### Page selector
**Field data type** : Pages  
**C# data type** : IEnumerable<WebPageRelatedItem>  
**Form component attribute** : WebPageSelectorComponent
Enables users to select [pages](/documentation/business-users/website-content) from the content tree of a website channel.
Returns a collection of `WebPageRelatedItem` objects that represent the selected pages. Each `WebPageRelatedItem` object contains a `WebPageGuid` property with the GUID of a selected page. You can use the GUIDs to [retrieve](/documentation/developers-and-admins/development/content-retrieval) the selected pages and work with them in your custom components.
#### Configuration properties
  * **TreePath** – limits the selection of pages to a subtree under a page identified by its tree path (e.g., _“/Products/Coffee-grinders”_). Only the specified page and its sub-pages can be selected. If not configured, users can select from the entire content tree of a selected website channel.
  * **MaximumPages** – sets the maximum number of selectable pages. If not specified, the default value is 1 (single page selection). 
    * 0 – no limit.
    * _n_ – at most _n_ pages can be selected at once.
  * **Sortable** – a boolean property that enables or disables ordering of the selected pages.
  * **ItemModifierType** – a type that implements the `IWebPagePanelItemModifier` interface. Provides logic that allows you to disable individual pages offered by the selector based on a custom condition. See the code example below.


#### Example
C#
**Assignment in model classes**
Copy
```
// Assigns the page selector as the property's editing component
// Returns a list of page selector items (web page GUIDs)
[WebPageSelectorComponent(
    TreePath = "/Articles",
    MaximumPages = 5,
    // Uses a system modifier that prevents selection of folders,
    // and pages whose content type is not included in routing
    ItemModifierType = typeof(WebPagesWithUrlWebPagePanelItemModifier))]
public IEnumerable<WebPageRelatedItem> Pages { get; set; } = new List<WebPageRelatedItem>();
```

C#
**Selection retrieval**
Copy
```
public class PageSelectorUsage : ViewComponent
{
    private readonly IContentRetriever contentRetriever;

    public PageSelectorUsage(IContentRetriever contentRetriever)
    {
        this.contentRetriever = contentRetriever;
    }

    public async Task<IViewComponentResult> InvokeAsync(ComponentViewModel<CustomWidgetProperties> model)
    {
        // Retrieves the GUIDs of the selected pages from the 'Pages' property
        List<Guid> webPageItemGuids = model?.Properties?.Pages?
                                                    .Select(i => i.WebPageGuid)
                                                    .ToList();

        // Retrieves the data of the selected pages under a shared page interface.
        // Uses the 'RetrieveAllPages' method to get pages from all content types and filter
        // based on the GUIDs received from the selector.
        IEnumerable<IWebPageFieldsSource> selectedPages =
                await contentRetriever.RetrieveAllPages<IWebPageFieldsSource>(
                    // Uses default parameters (current channel, language from context, etc.)
                    parameters: RetrieveAllPagesParameters.Default,
                    // Applies WHERE condition to filter only the pages with matching GUIDs
                    additionalQueryConfiguration: query =>
                        query.Where(where => where
                                .WhereIn(nameof(IWebPageFieldsSource.SystemFields.WebPageItemGUID), webPageItemGuids)),
                    // Configures caching with a suffix that reflects the query modification (WhereIn filter by GUIDs)
                    cacheSettings: new RetrievalCacheSettings($"{nameof(InvokeAsync)}|{nameof(RetrieveAllPagesQueryParameters.Where)}|ArticlesGUIDs"));

        // Custom logic...

        return View("...");
    }
}
```

C#
**ItemModifierType example**
Copy
```
public class PagesWithUrlPagePanelItemModifier : IWebPagePanelItemModifier
{
    public WebPagePanelItem Modify(WebPagePanelItem pagePanelItem, WebPagePanelItemModifierParameters itemModifierParams)
    {
        // Disables selection of pages under a specific tree path
        if (itemModifierParams.WebPageMetadata.TreePath.StartsWith("/Articles/Paywalled", StringComparison.InvariantCultureIgnoreCase))
        {
            pagePanelItem.SelectableOption.Selectable = false;
            pagePanelItem.SelectableOption.UnselectableReason = "Cannot select pages under the '/Articles/Paywalled' section";
        }

        return pagePanelItem;
    }
}
```

### URL selector
This form component is currently usable only in Page or Form Builder, and [model-based edit pages](/documentation/developers-and-admins/customization/extend-the-administration-interface/ui-pages/ui-pages-with-forms) or [listing filter models](/documentation/developers-and-admins/customization/extend-the-administration-interface/ui-pages/reference-ui-page-templates/listing-ui-page-template#assign-filters-to-listing-pages) in the Xperience administration.
**C# data type:** string  
**Form component attribute** : UrlSelectorComponent
Allows content editors to insert a URL to an external resource or select a page from the [content tree](/documentation/business-users/website-content) of a website channel (the selector returns [canonical URLs](/documentation/business-users/website-content/manage-page-urls#select-the-canonical-url-of-pages) of pages). When used outside of a [website channel](/documentation/developers-and-admins/configuration/website-channel-management), the page selection functionality is disabled and URLs can only be inserted manually in text form.
#### Example
C#
**Assignment in model classes**
Copy
```
// Assigns the URL selector as the property's editing component
// Returns a string containing the relative URL of the selected page
[UrlSelectorComponent(Label = "Select a page")]
public string PageUrl { get; set; }
```

C#
**Selection retrieval**
Copy
```
public class UrlSelectorUsage : ViewComponent
{
    private readonly IUserInfoProvider userInfoProvider;

    public UrlSelectorUsage(IUserInfoProvider userInfoProvider)
    {
        this.userInfoProvider = userInfoProvider;
    }

    public IViewComponentResult Invoke(ComponentViewModel<CustomWidgetProperties> model)
    {
        // Retrieves the selected page URLfrom the properties (of a widget properties class, for example)
        string pageUrl = model?.Properties?.PageUrl ?? String.Empty;

        // Custom logic...

        return View("...");
    }
}
```

### Smart folder selector
**Field data type** : Smart folder  
**C# data type** : SmartFolderReference  
**Form component attribute** : SmartFolderSelectorComponent
Enables users to select a [smart folder](/documentation/business-users/content-hub/content-hub-folders#smart-folders).
Returns a `SmartFolderReference` object, which contains an `Identifier` property with the GUID of the selected smart folder. You can use the GUID to [retrieve](/documentation/developers-and-admins/development/content-retrieval/retrieve-content-items#retrieve-content-items-from-smart-folders) the content items that match the filter condition of the selected smart folder.
The selector optionally enables you to restrict which smart folders are available based on the content types allowed by the filter conditions of folders. Create a filter class that implements an appropriate filter interface, and assign its type into the corresponding property of the smart folder selector attribute:
  * **AllowedContentTypeIdentifiersFilter** – scopes the selector to offer only smart folders that include at least one of the returned [content types](/documentation/developers-and-admins/development/content-types) in their filter condition (as well as smart folders that don’t filter by content type at all). The filter type class must implement the `IContentTypesNameFilter` interface. See [Example with content type scoping](#example-with-content-type-scoping).
  * **AllowedReusableFieldSchemaIdentifiersFilter** – scopes the selector to offer only smart folders that include at least one content type using one of the returned [reusable field schemas](/documentation/developers-and-admins/development/content-types/reusable-field-schemas) in their filter condition (as well as smart folders that don’t filter by content type at all). The filter type class must implement the `IReusableFieldSchemasFilter` interface. See [Example with reusable field schema scoping](#example-with-reusable-field-schema-scoping).


If you do not set either property, the selector allows all smart folders that have dynamic content delivery enabled.
#### Example with content type scoping
C#
**Smart folder selector scoped to a content type**
Copy
```
// Assigns the smart folder selector as the property's editing component
// Enables selection of smart folders that allow the 'Coffee' content type
[SmartFolderSelectorComponent(AllowedContentTypeIdentifiersFilter = typeof(CoffeeContentTypeFilter), Label = "Coffee smart folder", Order = 1)]
public SmartFolderReference SmartFolderWithCoffee { get; set; }

// ...

// Filters the smart folders available in the selector to those that allow the 'Coffee' content type
public class CoffeeContentTypeFilter : IContentTypesNameFilter
{
    IEnumerable<string> IContentTypesNameFilter.AllowedContentTypeNames => new List<string> { Coffee.CONTENT_TYPE_NAME };
}
```

C#
**Retrieval of content items from the smart folder**
Copy
```
public class SmartFolderSelectorUsage : ViewComponent
{
    private readonly IContentRetriever contentRetriever;

    public SmartFolderSelectorUsage(IContentRetriever contentRetriever)
    {
        this.contentRetriever = contentRetriever;
    }

    public async Task<ViewViewComponentResult> InvokeAsync(ComponentViewModel<CustomWidgetProperties> model)
    {
        // Gets the GUID from the annotated property
        var selectedSmartFolderGuid = model?.Properties?.SmartFolderWithCoffee.Identifier ?? Guid.Empty;

        // Retrieves 'Coffee' content items from the smart folder
        IEnumerable<Coffee> result = await contentRetriever.RetrieveContent<Coffee>(
            RetrieveContentParameters.Default,
            query => query.InSmartFolder(selectedSmartFolderGuid),
            new RetrievalCacheSettings($"{nameof(RetrieveContentQueryParameters.InSmartFolder)}|{selectedSmartFolderGuid}"));

        // Custom logic...

        return View("...");
    }
}
```

#### Example with reusable field schema scoping
C#
**Smart folder selector scoped to a reusable field schema**
Copy
```
// Assigns the smart folder selector as the property's editing component
// Enables selection of smart folders that allow content types using the 'ProductFields' schema
[SmartFolderSelectorComponent(AllowedReusableFieldSchemaIdentifiersFilter = typeof(ProductSchemaFilter), Label = "Product smart folder", Order = 1)]
public SmartFolderReference SmartFolderWithProducts { get; set; }

// ...

// Filters the smart folders available in the selector to those that allow content types using the 'ProductFields' schema
public class ProductSchemaFilter : IReusableFieldSchemasFilter
{
    IEnumerable<string> IReusableFieldSchemasFilter.AllowedSchemaNames => new List<string> { IProductFields.REUSABLE_FIELD_SCHEMA_NAME };
}
```

C#
**Retrieval of content items from the smart folder**
Copy
```
public class SmartFolderSelectorUsage : ViewComponent
{
    private readonly IContentRetriever contentRetriever;

    public SmartFolderSelectorUsage(IContentRetriever contentRetriever)
    {
        this.contentRetriever = contentRetriever;
    }

    public async Task<ViewViewComponentResult> InvokeAsync(ComponentViewModel<CustomWidgetProperties> model)
    {
        // Gets the GUID from the annotated property
        var selectedSmartFolderGuid = model?.Properties?.SmartFolderWithProducts.Identifier ?? Guid.Empty;

        // Retrieves product content items from the smart folder
        IEnumerable<IProductFields> result = await contentRetriever.RetrieveContentOfReusableSchemas<IProductFields>(
            new[] { IProductFields.REUSABLE_FIELD_SCHEMA_NAME },
            RetrieveContentOfReusableSchemasParameters.Default,
            query => query.InSmartFolder(selectedSmartFolderGuid),
            new RetrievalCacheSettings(cacheItemNameSuffix:
                $"{nameof(RetrieveContentQueryParameters.InSmartFolder)}|{selectedSmartFolderGuid}"));

        // Custom logic...

        return View("...");
    }
}
```

### Tag selector
**Field data type** : Taxonomy  
**C# data type** : IEnumerable<TagReference>  
**Form component attribute** : TagSelectorComponent
Enables users to select tags from [taxonomies](/documentation/developers-and-admins/configuration/taxonomies) to associate them with the currently edited object.
When used as an [editing component](/documentation/developers-and-admins/customization/extend-the-administration-interface/ui-form-components/editing-components), returns a collection of `TagReference` objects that contain GUIDs of the selected tags.
#### Configuration properties
  * **TaxonomyName** – the code name of the taxonomy to offer the selection from.
  * **MinSelectedTagsCount** – the minimum number of tags that need to be selected.
  * **MaxSelectedTagsCount** – the maximum number of tags that can be selected. 
    * 0 – no limit.
    * _n_ – at most _n_ tags can be selected at once.


C#
**Assignment in a model class**
Copy
```
// Assigns the tag selector as the property's editing component
// Enables selection from the 'ItemCategory' taxonomy
[TagSelectorComponent("ItemCategory", Label = "Category", Order = 1)]
public IEnumerable<TagReference> Tags { get; set; }
```

C#
**Selection retrieval**
Copy
```
public class TagSelectorUsage : ViewComponent
{
    private readonly ITaxonomyRetriever taxonomyRetriever;

    public TagSelectorUsage(ITaxonomyRetriever taxonomyRetriever)
    {
        this.taxonomyRetriever = taxonomyRetriever;
    }

    public async Task<ViewViewComponentResult> InvokeAsync(ComponentViewModel<CustomWidgetProperties> model)
    {
        // Gets identifiers of the selected tags from the annotated property
        IEnumerable<Guid> tagIdentifiers = model?.Properties?.SelectedTags.Select(item => item.Identifier);

        // Retrieves a collection of Tag object corresponding to the selected tags.
        IEnumerable<Tag> tags = await taxonomyRetriever.RetrieveTags(tagIdentifiers, "en");

        // Custom logic...

        return View("...");
    }
}
```

### General selector
This form component is currently usable only in Page or Form Builder, and [model-based edit pages](/documentation/developers-and-admins/customization/extend-the-administration-interface/ui-pages/ui-pages-with-forms) or listing filter models in the Xperience administration.
**C# data type** : IEnumerable<string>  
**Form component attribute** : GeneralSelectorComponent
Allows users to select one or more items using a drop-down menu with a search bar. The items offered by the selector can be of any type, including external data outside of Xperience. Developers need to implement a data provider that loads and prepares the items displayed in the selector.
The selector returns a collection of _string_ objects, which contain the values of the selected items.
#### Configuration properties
  * **MaximumItems** – sets the maximum number of selected items. If not specified, the default value is 0, which means the number of selected items is not limited.
**Single general selector**
If you only wish to allow selection of a single item, you can use the `SingleGeneralSelectorComponent` form component attribute instead. The single general selector works just like the general selector, but the returned data type is `string` and the `MaximumItems` configuration property is not available.
  * **Placeholder** – text displayed before an option is selected. E.g., “Choose an option”
  * **DataProviderType** – dynamic data source for the selector’s options.


The general selector’s data source class must implement the `IGeneralSelectorDataProvider` interface and define the following methods:
  * **GetItemsAsync** – provides the items available in the selector. The `searchTerm` parameter contains the text entered into the search box (empty when the selector is initially opened). The `pageIndex` parameter allows you to implement pagination for better performance when working with a very large number of items. To disable pagination, ignore the `pageIndex` parameter and set the `NextPageAvailable` property of the return object to false. The method must return a `PagedSelectListItems<string>` object, containing the selector’s options in its `Items` property, as an `IEnumerable<ObjectSelectorListItem<string>` collection.
  * **GetSelectedItemsAsync** – identifies which items are currently selected. Transforms an `IEnumerable<string>` collection with the values of the selected items into `ObjectSelectorListItem<string>` objects, which contain both the item values and the text displayed in the selector interface.


**Performance considerations**
If you wish to load and display a very large number of items in the general selector, we recommend using pagination and implementing [caching](/documentation/developers-and-admins/development/caching/data-caching) in your data provider implementation.
C#
**Assignment in model classes**
Copy
```
// Assigns the General selector as the property's editing component
[GeneralSelectorComponent(
            dataProviderType : typeof(UserGeneralSelectorDataProvider),
            Label = "Users",
            Placeholder = "Choose a user")]
public IEnumerable<string> UserOptions { get; set; }
```

C#
**Data provider class**
Copy
```
using System;
using System.Collections.Generic;
using System.Linq;
using System.Threading;
using System.Threading.Tasks;

using Kentico.Xperience.Admin.Base.FormAnnotations;
using Kentico.Xperience.Admin.Base.Forms;

using CMS.Membership;

public class UserGeneralSelectorDataProvider : IGeneralSelectorDataProvider
{
    private readonly IUserInfoProvider userInfoProvider;

    // Item representing an invalid selector option
    private static ObjectSelectorListItem<string> InvalidItem => new ObjectSelectorListItem<string>() { IsValid = false };

    public UserGeneralSelectorDataProvider(IUserInfoProvider userInfoProvider)
    {
        this.userInfoProvider = userInfoProvider;
    }

    // Returns items displayed in the general selector drop-down list
    public Task<PagedSelectListItems<string>> GetItemsAsync(string searchTerm, int pageIndex, CancellationToken cancellationToken)
    {
        // Prepares a query for retrieving user objects
        var itemQuery = userInfoProvider.Get();

        // If a search term is entered, only loads users whose first name starts with the term
        if (!string.IsNullOrEmpty(searchTerm))
        {
            itemQuery.WhereStartsWith("FirstName", searchTerm);
        }

        // Ensures paging of items
        itemQuery.Page(pageIndex, 20);

        return Task.FromResult(new PagedSelectListItems<string>()
        {
            NextPageAvailable = itemQuery.NextPageAvailable,
            // Retrieves the users and converts them into ObjectSelectorListItem<string> options
            Items = itemQuery.GetEnumerableTypedResult()
                             .Select(GetUserListItem)
        });
    }

    // Returns ObjectSelectorListItem<string> options for all item values that are currently selected
    public Task<IEnumerable<ObjectSelectorListItem<string>>> GetSelectedItemsAsync(IEnumerable<string> selectedValues, CancellationToken cancellationToken) =>
        Task.FromResult(selectedValues?.Select(v => GetSelectedItemByValue(v)) ?? Enumerable.Empty<ObjectSelectorListItem<string>>()
    );

    private ObjectSelectorListItem<string> GetSelectedItemByValue(string value)
    {
        // Gets the user with the selected user name (or an invalid item if the user is not found)
        return GetUserListItem(userInfoProvider.Get(value)) ?? InvalidItem;
    }

    // Converts a user object (UserInfo) into an ObjectSelectorListItem<string> option
    private ObjectSelectorListItem<string> GetUserListItem(UserInfo user)
    {
        return new ObjectSelectorListItem<string>()
        {
            Value = user.UserName,
            Text = $"{user.FirstName} {user.LastName} ({user.UserName})",
            IsValid = true
        };
    }
}
```

## Components
### Checkbox
**Field data type** : Boolean (Yes/No)  
**C# data type** : bool  
**Form component attribute** : CheckBoxComponent
Checkbox field that saves a boolean value (true for a selected checkbox, false for a cleared checkbox).
### Code editor
**Field data type** : Text, Long text  
**C# data type** : string  
**Form component attribute** : CodeEditorComponent
Provides a text editing area suitable for code, with support for syntax highlighting and line numbers.
#### Configuration properties
  * **Language** – sets the language for the syntax highlighting applied by the editor. The following values are supported:
    * css
    * html
    * javascript
    * sql
    * xml


### Dropdown selector
**Field data type** : Text, Long text  
**C# data type** : string  
**Form component attribute** : DropDownComponent
Drop-down selector offering multiple options. Only one option can be selected.
#### Configuration properties
  * **Options** – items must be specified one per line in the format:
Text
Copy
```
1;One
2;Two
```

When configuring the component using the attribute, the options string must contain newline characters. For example for Windows:
C#
Copy
```
1;One\r\n2;Two
```

  * **Options value separator** – custom separator used between the value and text in the options. For example, if set to ‘-’, the options would be:
Text
Copy
```
1-One
2-Two
```

  * **Placeholder** – text displayed before an option in the drop-down is selected. E.g., “Month”
  * **DataProviderType** – dynamic data source for the component. The assigned type must implement the `IDropDownOptionsProvider` interface.
C#
**Example**
Copy
```
using System.Collections.Generic;
using System.Linq;
using System.Threading.Tasks;

using CMS.Membership;

using Kentico.Xperience.Admin.Base.FormAnnotations;

// Populates the selector with all users from the system
public class UsersDropdownOptionsProvider : IDropDownOptionsProvider
{
    private readonly IUserInfoProvider userInfoProvider;

    public UsersDropdownOptionsProvider(IUserInfoProvider userInfoProvider)
    {
        this.userInfoProvider = userInfoProvider;
    }

    public async Task<IEnumerable<DropDownOptionItem>> GetOptionItems()
    {
        return (await userInfoProvider.Get()
                                      .GetEnumerableTypedResultAsync())
                                      .Select(x => new DropDownOptionItem()
                                      {
                                          Value = x.UserID.ToString(),
                                          Text = x.UserName
                                      });
    }
}
```



### Date input
**Field data type** : Date  
**C# data type** : System.DateTime? (nullable)  
**Form component attribute** : DateInputComponent
Date selector.
#### Remarks
Annotating a non-nullable property results in the input being prepopulated with the minimum selectable date. If you want to display an empty input instead, use the following construction.
C#
Copy
```
[DateInputComponent]
// Use RequiredValidationRule to enforce user input
[RequiredValidationRule]
// Set the property as nullable
public DateTime? Date { get; set; } = null;
```

### Date and time input
**Field data type** : Date and time  
**C# data type** : System.DateTime? (nullable)  
**Form component attribute** : DateTimeInputComponent
Date and time selector.
Time values in the selector’s UI are automatically converted and displayed in the local time zone of each user, based on browser and environment settings. For example: _9/30/2025 12:00 PM GMT+2_. The value stored into the field is always in the time zone of the server where the application is running.
#### Remarks
Annotating a non-nullable property results in the input being prepopulated with the minimum selectable date. If you want to display an empty input instead, use the following construction.
C#
Copy
```
[DateTimeInputComponent]
// Use RequiredValidationRule to enforce user input
[RequiredValidationRule]
// Set the property as nullable
public DateTime? DateAndTime { get; set; } = null;
```

### Date and time with label
**Field data type** : Date and time  
**C# data type** : System.DateTime? (nullable)  
**Form component attribute** : DateTimeStringComponent
Displays a read-only `DateTime` value.
### Decimal number input
**Field data type** : Decimal number  
**C# data type** : Decimal? (nullable)  
**Form component attribute** : DecimalNumberInputComponent
Input field for entering decimal numbers.
### Link
**Field data type** : Text, Long text  
**C# data type** : string  
**Form component attribute** : LinkComponent
Displays a non-editable link within forms in the administration interface.
#### Configuration properties
  * **Text** – the text displayed for the link in the form. If null or empty, the value of the link (the URL) is used instead.
  * **OpenInNewTab** – a _boolean_ property that indicates whether the link opens in a new browser tab. The default value is _true_.


### Number input
**Field data type** : Integer number  
**C# data type** : Int32? (nullable)  
**Form component attribute** : NumberInputComponent
Text input field for entering whole (integer) numbers.
### Number with label
**Field data type** : Integer number  
**C# data type** : Int32
Used to display non-editable whole numbers together with a label for informational value.
### Password
**Field data type** : Text, Long text  
**C# data type** : string  
**Form component attribute** : PasswordComponent
Text input field that obscures user input.
#### Configuration properties
  * **IgnorePasswordPolicy** – if set, disregards all password policy configuration when validating input.
  * **RequiredLength** – the minimum length a password must be. Defaults to 8.
  * **RequiredUniqueChars** – the minimum number of unique characters which a password must contain. Defaults to 1.
  * **RequireDigit** – a flag indicating if passwords must contain a digit. Defaults to `true`.
  * **RequireNonAlphanumeric** – a flag indicating if passwords must contain a non-alphanumeric character. Defaults to `true`.
  * **RequireUppercase** – a flag indicating if passwords must contain an upper case ASCII character. Defaults to `true`.
  * **RequireLowercase** – a flag indicating if passwords must contain a lower case ASCII character. Defaults to `true`.


### Rich text editor
**Field data type** : Rich text (HTML)  
**C# data type** : string  
**Form component attribute** : RichTextEditorComponent
Allows users to enter HTML-enriched text using the [Rich text editor](/documentation/business-users/rich-text-editor).
#### Configuration properties
  * **ConfigurationName** – the configuration to use for the editor. See [Rich text editor configuration](/documentation/developers-and-admins/configuration/rich-text-editor-configuration).


### Radio group component
**Field data type** : Text, Long text  
**C# data type** : string  
**Form component attribute** : RadioGroupComponent
Allows single selection from a group of radio buttons.
#### Configuration properties
  * **Options** – specifies each button in the radio group, one per line in the `value;label` format. For example: `1;One`.
  * **Options value separator** – custom separator used between the value and label in the options. For example, if set to ‘-’, the options would be `1-One`.
  * **Inline** – indicates whether the options are displayed in a horizontal layout on a single line. If false, options are displayed vertically on separate lines.
  * **AriaLabel** – an accessibility label for the interactive element of the radio buttons. See [aria-label](https://developer.mozilla.org/docs/Web/Accessibility/ARIA/Attributes/aria-label).


### Text area
**Field data type** : Text, Long text  
**C# data type** : string  
**Form component attribute** : TextAreaComponent
Allows users to enter text into an area with adjustable size.
#### Configuration properties
  * **CopyButtonVisible** – indicates whether a copy that enables users to copy the contents of the text area is visible.
  * **MaxRowsNumber** – sets the maximum number of rows to which the area expands when text is added. If further rows added, the text area displays a scroll bar.
  * **MinRowsNumber** – sets the minimum number of rows displayed in the text area (i.e., the area’s height).
  * **WatermarkText** – placeholder displayed when the area is empty.


### Text input
**Field data type** : Text, Long text  
**C# data type** : string  
**Form component attribute** : TextInputComponent
Text input field.
### Text with label
**Field data type** : Text, Long text, Integer number  
**C# data type** : string  
**Form component attribute** : TextWithLabelComponent
Used to display non-editable text together with a label for informational value.
### Extension selector
**Field data type** : Text, Long text  
**C# data type** : string  
**Form component attribute** : ExtensionSelectorComponent
Allows you to specify a list of allowed extensions using a simple interface. Defaults to the list of system-wide allowed extensions specified in the **Settings** application: **Content** → **Media** → **Media file allowed extensions** setting.
#### Configuration properties
  * **AllowedExtensions** – the semicolon-delimited list of allowed extensions.


![]()
[]()[]()
