---
source: https://docs.kentico.com/modules/custom-settings/build-channel-specific-settings-ui
scrape_date: 2026-01-22
---

Module: Custom modules: custom settings
6 of 9 Pages
# Build the channel-specific settings UI
[![screenshot of channel settings list](/docsassets/guides/add-channels-to-module/ChannelSettingsList.png)](/docsassets/guides/add-channels-to-module/ChannelSettingsList.png)
In the previous example from this series, we saw a [listing page that displayed _global settings key_ objects](/modules/custom-settings/build-module-ui#define-the-listing-page). Because these global settings were created and managed by users, the listing page just needed to display the objects, with links and buttons.
However, in situations where you want to enforce one-to-one relationships, you can use the landing page to execute code that ensures the existence of the required objects. [UI pages in Xperience by Kentico](/documentation/developers-and-admins/customization/extend-the-administration-interface/ui-pages) support dependency injection, so you should be able to use whichever services and options you need to properly set up data before the page loads.
For our example, let’s use the listing page to make sure that for each web channel, a _web channel settings_ object exists, and that each one has an _SEO settings_ child.
Start by creating a listing page similar to the [example from earlier in this seires](/modules/custom-settings/build-module-ui#define-the-listing-page), but with no delete button; Xperience will handle deletions automatically thanks to our class overrides that established the relationship between Web channel settings and their corresponding channels.
You only need one column in the `ColumnConfigurations`, showing the display name of the channel. Editors will arrive at this page to see a list of channel names. Then they can edit the settings of whichever channel they click on.
C#
**WebChannelSettingsListingPage.cs**
Copy
```
using Kentico.Xperience.Admin.Base;
using TrainingGuides.ProjectSettings;
using TrainingGuides.Admin.ProjectSettings.WebChannelSettings;
using TrainingGuides.Admin.ProjectSettings;
using CMS.DataEngine;
using CMS.ContentEngine;

[assembly: UIPage(
    parentType: typeof(ProjectSettingsApplication),
    slug: "channel-settings",
    uiPageType: typeof(WebChannelSettingsListingPage),
    name: "Channel settings",
    templateName: TemplateNames.LISTING,
    order: 0)]

namespace TrainingGuides.Admin.ProjectSettings.WebChannelSettings;
public class WebChannelSettingsListingPage : ListingPage
{
    protected override string ObjectType => WebChannelSettingsInfo.OBJECT_TYPE;

    public override async Task ConfigurePage()
    {
        PageConfiguration.ColumnConfigurations
                     .AddColumn(nameof(
                        WebChannelSettingsInfo.WebChannelSettingsChannelDisplayName), "Channel");

        PageConfiguration.AddEditRowAction<WebChannelSettingsEditSection>();

        await base.ConfigurePage();
    }
}
```

Next, add code that checks whether there is a `WebChannelSettingsInfo` object corresponding to each channel, and creates a new one if not.
It should check each `WebChannelSettingsInfo` object, ensuring that its display name matches the corresponding channel in case the channel is modified, and that it has a related `SeoSettingsInfo` object.
C#
**WebChannelSettingsListingPage.cs**
Copy
```
...
namespace TrainingGuides.Admin.ProjectSettings;
public class WebChannelSettingsListingPage : ListingPage
{
    private readonly IInfoProvider<ChannelInfo> channelInfoProvider;
    private readonly IInfoProvider<WebChannelSettingsInfo> webChannelSettingsInfoProvider;
    private readonly IInfoProvider<SeoSettingsInfo> seoSettingsInfoProvider;

    protected override string ObjectType => WebChannelSettingsInfo.OBJECT_TYPE;

    public WebChannelSettingsListingPage(
        IInfoProvider<ChannelInfo> channelInfoProvider,
        IInfoProvider<WebChannelSettingsInfo> webChannelSettingsInfoProvider,
        IInfoProvider<SeoSettingsInfo> seoSettingsInfoProvider) : base()
    {
        this.channelInfoProvider = channelInfoProvider;
        this.webChannelSettingsInfoProvider = webChannelSettingsInfoProvider;
        this.seoSettingsInfoProvider = seoSettingsInfoProvider;

        EnsureSettingsListData();
    }

    ...

    //Creates necessary settings objects if they do not exist, and ensures proper channel names.
    private void EnsureSettingsListData()
    {
        var channels = channelInfoProvider.Get();
        var webChannelSettings = webChannelSettingsInfoProvider.Get().ToList();
        var seoSettings = seoSettingsInfoProvider.Get().ToList();

        foreach (var channel in channels)
        {
            var currentChannelSettings = webChannelSettings.Where(setting => setting.WebChannelSettingsChannelID.Equals(channel.ChannelID)).ToList();

            EnsureChannelSetting(currentChannelSettings, channel);

            var currentChannelSetting = currentChannelSettings.FirstOrDefault();

            EnsureChannelSettingDisplayName(channel, currentChannelSetting);

            EnsureSeoSettings(currentChannelSetting, seoSettings);
        }
    }

    //Creates a new WebChannelSettingsInfo for the provided channel if none exists.
    private void EnsureChannelSetting(List<WebChannelSettingsInfo> currentChannelSettings, ChannelInfo channel)
    {
        if (currentChannelSettings.Count() == 0)
        {
            var newSetting = new WebChannelSettingsInfo
            {
                WebChannelSettingsChannelID = channel.ChannelID,
                WebChannelSettingsChannelDisplayName = channel.ChannelDisplayName,
            };
            webChannelSettingsInfoProvider.Set(newSetting);
            currentChannelSettings.Add(newSetting);
        }
    }

    //Updates display name of provided WebChannelSettingsInfo to match its channel if they are different.
    private void EnsureChannelSettingDisplayName(ChannelInfo channel, WebChannelSettingsInfo? currentChannelSetting)
    {
        if (currentChannelSetting != null && !channel.ChannelDisplayName.Equals(currentChannelSetting.WebChannelSettingsChannelDisplayName))
        {
            currentChannelSetting.WebChannelSettingsChannelDisplayName = channel.ChannelDisplayName;
            webChannelSettingsInfoProvider.Set(currentChannelSetting);
        }
    }

    //Creates a new SeoSettingsInfo object for the provided WebChannelSettingsInfoif none exists.
    private void EnsureSeoSettings(WebChannelSettingsInfo? currentChannelSetting, IEnumerable<SeoSettingsInfo> seoSettings)
    {
        int? webChannelSettingsId = currentChannelSetting?.WebChannelSettingsID;

        var currentSeoSettings = seoSettings
            .Where(setting => setting.SeoSettingsWebChannelSettingID == webChannelSettingsId).ToList();

        if (currentSeoSettings.Count() == 0 && webChannelSettingsId != null)
        {
            var newSeoSetting = new SeoSettingsInfo
            {
                SeoSettingsWebChannelSettingID = (int)webChannelSettingsId,
                SeoSettingsRobots = string.Empty,
            };

            seoSettingsInfoProvider.Set(newSeoSetting);
        }
    }
}
```

You can find the complete file [in the finished branch of the _Training guides repository_](https://github.com/Kentico/xperience-by-kentico-training-guides/blob/finished/src/TrainingGuides.Admin/Pages/ProjectSettings/WebChannelSettings/WebChannelSettingsListingPage.cs) for reference.
You may notice how the above code does not check for and delete settings objects with no parent. Thanks to the parent-child relationship [we set earlier](/modules/custom-settings/model-channel-specific-settings), settings are deleted automatically along with their channel.
## Edit child objects as part of a parent
When objects have parent-child relationships, you may want to edit one or more child objects when a user selects a parent from the listing page, rather than directly editing the parent itself. In our example, the _Web channel settings_ don’t have any meaningful properties for users to edit, so instead, so when they choose a channel, we want to display the _SEO settings_ edit form instead.
This will involve a few adjustments compared to previous examples, which involved _listing_ and _edit_ pages that worked with the same object type.
Luckily, the type of an `InfoEditPage<T>` does not need to match the type of the `EditSectionPage<T>` registered as its `parentType`.
Compared to the previous examples, we need an extra bound parameter to retrieve the ID of the parent object. Then we can use its value in our child pages. For editing child objects with one-to-one relationships (like our example’s parent _Web channels settings_ and child _SEO settings_), override the abstract `ObjectID` property inherited from `InfoEditPage<T>`, and use the parent’s ID to retrieve the appropriate child.
The [next section](#create-a-sub-section-for-multiple-child-objects) wil cover one-to-many relationships, where there may be mutiple child objects per parent.
For example, several _Web channel snippets_ may relate to one _Web channel settings_ object.
For editing pages, Xperience automatically propagates the display name of a saved object to the breadcrumbs and navigation, so you’ll need to override the `GetSubmitSuccessResponse` method to keep the name of the parent object on display.
**See the difference**
The behavior of the navigation with and without the `GetSubmitSuccessResponse` override is best demonstrated visually.
_Before:_
Your browser does not support the video tag. 
_After:_
Your browser does not support the video tag. 
If you’re following along with our example, start by making an edit section for `WebChannelSettingsInfo`. It should be a child of the listing page from earlier.
C#
**WebChannelSettingsEditSection.cs**
Copy
```
using Kentico.Xperience.Admin.Base;
using TrainingGuides.ProjectSettings;
using TrainingGuides.Admin.ProjectSettings.WebChannelSettings;

[assembly: UIPage(
    parentType: typeof(WebChannelSettingsListingPage),
    slug: PageParameterConstants.PARAMETERIZED_SLUG,
    uiPageType: typeof(WebChannelSettingsEditSection),
    name: "Edit",
    templateName: TemplateNames.SECTION_LAYOUT,
    order: 0)]

namespace TrainingGuides.Admin.ProjectSettings.WebChannelSettings;

public class WebChannelSettingsEditSection : EditSectionPage<WebChannelSettingsInfo>
{
}
```

Then add an edit page for the `SeoSettingsInfo` child of the edited `WebChannelSettingsInfo` object.
Bind the parent’s `WebChannelSettingsId` as a parameter, and use it to retrieve the corresponding `SeoSettingsInfo` in the accessor of `ObjectId`.
C#
**SeoSettingsEditPage.cs**
Copy
```
using Kentico.Xperience.Admin.Base;
using TrainingGuides.ProjectSettings;
using TrainingGuides.Admin.ProjectSettings.WebChannelSettings;
using Kentico.Xperience.Admin.Base.Forms;
using CMS.DataEngine;

[assembly: UIPage(
    parentType: typeof(WebChannelSettingsEditSection),
    slug: "edit",
    uiPageType: typeof(SeoSettingsEditPage),
    name: "SEO settings",
    templateName: TemplateNames.EDIT,
    order: 0)]

namespace TrainingGuides.Admin.ProjectSettings.WebChannelSettings;

public class SeoSettingsEditPage : InfoEditPage<SeoSettingsInfo>
{
    private readonly IInfoProvider<SeoSettingsInfo> seoSettingsInfoProvider;
    private readonly IInfoProvider<WebChannelSettingsInfo> webChannelSettingsInfoProvider;

    [PageParameter(typeof(IntPageModelBinder))]
    public int WebChannelSettingsId { get; set; }

    [PageParameter(typeof(IntPageModelBinder))]
    public override int ObjectId
    {
        // Retrieves the SEO settings ID based on the WebChannelSettingsId.
        get => seoSettingsInfoProvider.Get()
                .WhereEquals(nameof(SeoSettingsInfo.SeoSettingsWebChannelSettingID), WebChannelSettingsId)
                .FirstOrDefault()?
                .SeoSettingsID ?? 0;
        // The UI will try to set the ObjectID to value of the parent WebChannelSettingsId, so we have to ignore that.
        set { }
    }

    public SeoSettingsEditPage(IFormComponentMapper formComponentMapper,
        IFormDataBinder formDataBinder,
        IInfoProvider<SeoSettingsInfo> seoSettingsInfoProvider,
        IInfoProvider<WebChannelSettingsInfo> webChannelSettingsInfoProvider)
             : base(formComponentMapper, formDataBinder)
    {
        this.seoSettingsInfoProvider = seoSettingsInfoProvider;
        this.webChannelSettingsInfoProvider = webChannelSettingsInfoProvider;
    }

    public override Task ConfigurePage()
    {
        PageConfiguration.UIFormName = "seosettingsedit";
        return base.ConfigurePage();
    }
}
```

On the edit page, override `GetSubmitSuccessResponse` method. Set the `ObjectDisplayName` property of the `EditPageSuccessFormSubmissionResult` to the display name of the parent `WebChannelSettingsInfo`. This ensures that the breadcrumbs and navigation dispaly the correct hierarchy.
C#
**SeoSettingsEditPage.cs**
Copy
```
...
public class SeoSettingsEditPage : InfoEditPage<SeoSettingsInfo>
{

    ...

    private string WebChannelSettingsDisplayName =>
        webChannelSettingsInfoProvider
            .Get()
            .WhereEquals(nameof(WebChannelSettingsInfo.WebChannelSettingsID), WebChannelSettingsId)
            .FirstOrDefault()?
            .WebChannelSettingsChannelDisplayName ?? "Web channel settings";

    ...

    protected override async Task<ICommandResponse> GetSubmitSuccessResponse(SeoSettingsInfo savedInfoObject, ICollection<IFormItem> items)
    {
        var result = new EditPageSuccessFormSubmissionResult()
        {
            Items = await items.OnlyVisible().GetClientProperties(),
            ObjectDisplayName = WebChannelSettingsDisplayName,
            ObjectId = WebChannelSettingsId,
            RefetchAll = RefetchAll
        };

        return ResponseFrom(result).AddSuccessMessage(LocalizationService?.GetString("base.forms.saved"));
    }
}
```

You can see the completed file in the [finished branch of the _Training guides repository_](https://github.com/Kentico/xperience-by-kentico-training-guides/blob/finished/src/TrainingGuides.Admin/Pages/ProjectSettings/WebChannelSettings/SeoSettingsEditPage.cs) for reference. Note that the code sample above is not localized for simplicity, but the repository version includes a localized version. For more guidance on how to localize your custom UI pages, see our [Admin UI localization documentation](/documentation/developers-and-admins/customization/admin-ui-localization).
If you build the project and navigate to **Project settings → Channel settings → Training guides pages** , you should see a UI like this, where you can enter a _robots.txt_ value for your website channel:
[![Screenshot of SEO settings](/docsassets/guides/add-channels-to-module/SeoSettings.png)](/docsassets/guides/add-channels-to-module/SeoSettings.png)
## Create a sub-section for multiple child objects
If you want to include a listing of child objects that users can manage within the edit section of a parent object, for example, giving each _Web channel settings_ object a listing of _Web channel snippet_ objects, the process is nearly identical to the standard module page approach from the [basic module from earlier in this series](/guides/development/customizations-and-integrations/create-basic-module#build-the-module-ui).
You can define a _Listing page_ as a child of the parent’s _Edit section_. This listing can have its own _Edit section_ and _Create page_ as children.
[![Screenshot of the UI page hierarchy](/docsassets/guides/add-channels-to-module/UITree.png)](/docsassets/guides/add-channels-to-module/UITree.png)
Then, you just need to bind the parent object’s ID, like you did [earlier](#edit-child-objects-as-part-of-a-parent), on any pages that need access to it. For example, you need the parent object’s ID to set the foreign key value on the create page and generate the URL for the edit page, and to filter which child objects are displayed on the listing page.
For our example, complete the following steps:
  1. Create a listing page for _Web channel snippet_ objects under the _Web channel settings_ edit section. 
Follow the same approach as [earlier](/modules/custom-settings). 
  2. Bind `WebChannelSettingsId` and use it to add a `QueryModifier` to `PageConfiguration.QueryModifiers` in the `ConfigurePage` method.


C#
**WebChannelSnippetListingPage.cs**
Copy
```
using CMS.DataEngine;
using CMS.Membership;
using Kentico.Xperience.Admin.Base;
using TrainingGuides.Admin.ProjectSettings.WebChannelSettings;
using TrainingGuides.ProjectSettings;

[assembly: UIPage(
    parentType: typeof(WebChannelSettingsEditSection),
    slug: "snippets",
    uiPageType: typeof(WebChannelSnippetListingPage),
    name: "Channel snippets",
    templateName: TemplateNames.LISTING,
    order: 10)]

namespace TrainingGuides.Admin.ProjectSettings.WebChannelSettings;
public class WebChannelSnippetListingPage : ListingPage
{
    protected override string ObjectType => WebChannelSnippetInfo.OBJECT_TYPE;

    [PageParameter(typeof(IntPageModelBinder))]
    public int WebChannelSettingsId { get; set; }

    public override Task ConfigurePage()
    {
        PageConfiguration.ColumnConfigurations
            .AddColumn(nameof(WebChannelSnippetInfo.WebChannelSnippetDisplayName), "Snippet")
            .AddColumn(nameof(WebChannelSnippetInfo.WebChannelSnippetType), "Type");

        PageConfiguration.HeaderActions.AddLink<WebChannelSnippetCreatePage>(
            "New snippet",
            parameters: new PageParameterValues
                {
                    { typeof(WebChannelSettingsEditSection), WebChannelSettingsId }
                });

        PageConfiguration.AddEditRowAction<WebChannelSnippetEditSection>(
            parameters: new PageParameterValues
                {
                    { typeof(WebChannelSettingsEditSection), WebChannelSettingsId }
                });

        PageConfiguration.TableActions
            .AddDeleteAction(nameof(Delete));

        PageConfiguration.QueryModifiers
            .AddModifier((query, _) =>
            {
                return query.Where(new WhereCondition().WhereEquals(nameof(WebChannelSnippetInfo.WebChannelSnippetWebChannelSettingsID), WebChannelSettingsId));
            });

        return base.ConfigurePage();
    }

    [PageCommand(Permission = SystemPermissions.DELETE)]
    public override Task<ICommandResponse<RowActionResult>> Delete(int id) => base.Delete(id);
}
```

Next, add an edit section for code snippets and an edit page underneath it.
C#
**WebChannelSnippetEditSection.cs**
Copy
```
using Kentico.Xperience.Admin.Base;
using TrainingGuides.ProjectSettings;
using TrainingGuides.Admin.ProjectSettings.WebChannelSettings;

[assembly: UIPage(
    parentType: typeof(WebChannelSnippetListingPage),
    slug: PageParameterConstants.PARAMETERIZED_SLUG,
    uiPageType: typeof(WebChannelSnippetEditSection),
    name: "Edit snippets",
    templateName: TemplateNames.SECTION_LAYOUT,
    order: 0)]

namespace TrainingGuides.Admin.ProjectSettings.WebChannelSettings;

public class WebChannelSnippetEditSection : EditSectionPage<WebChannelSnippetInfo>
{
}
```

C#
**WebChannelSnippetEditPage.cs**
Copy
```
using Kentico.Xperience.Admin.Base;
using TrainingGuides.ProjectSettings;
using TrainingGuides.Admin.ProjectSettings.WebChannelSettings;
using Kentico.Xperience.Admin.Base.Forms;

[assembly: UIPage(
    parentType: typeof(WebChannelSnippetEditSection),
    slug: "edit",
    uiPageType: typeof(WebChannelSnippetEditPage),
    name: "Edit snippet",
    templateName: TemplateNames.EDIT,
    order: 0)]

namespace TrainingGuides.Admin.ProjectSettings.WebChannelSettings;

public class WebChannelSnippetEditPage : InfoEditPage<WebChannelSnippetInfo>
{
    [PageParameter(typeof(IntPageModelBinder), typeof(WebChannelSnippetEditSection))]
    public override int ObjectId { get; set; }

    public WebChannelSnippetEditPage(IFormComponentMapper formComponentMapper, IFormDataBinder formDataBinder)
             : base(formComponentMapper, formDataBinder)
    {
    }

    public override Task ConfigurePage()
    {
        PageConfiguration.UIFormName = "webchannelsnippetedit";
        return base.ConfigurePage();
    }
}
```

Finally, add a _create page_. Make sure to bind the `WebChannelSettingsID`, and add it as a URL parameter for when the edit page is displayed after creation.
Override the `FinalizeInfoObject` method to make sure that `WebChannelSnippetWebChannelSettingsID` is set to `WebChannelSettingsId`
C#
**WebChannelSnippetCreatePage.cs**
Copy
```
using Kentico.Xperience.Admin.Base;
using TrainingGuides.ProjectSettings;
using TrainingGuides.Admin.ProjectSettings.WebChannelSettings;
using Kentico.Xperience.Admin.Base.Forms;

[assembly: UIPage(
    parentType: typeof(WebChannelSnippetListingPage),
    slug: "create",
    uiPageType: typeof(WebChannelSnippetCreatePage),
    name: "Create snippet",
    templateName: TemplateNames.EDIT,
    order: 20)]

namespace TrainingGuides.Admin.ProjectSettings.WebChannelSettings;

public class WebChannelSnippetCreatePage : CreatePage<WebChannelSnippetInfo, WebChannelSnippetEditSection>
{
    // Bind the parent object's ID
    [PageParameter(typeof(IntPageModelBinder), typeof(WebChannelSettingsEditSection))]
    public int WebChannelSettingsId { get; set; }

    public WebChannelSnippetCreatePage(IFormComponentMapper formComponentMapper,
        IFormDataBinder formDataBinder,
        IPageLinkGenerator pageLinkGenerator)
        : base(formComponentMapper, formDataBinder, pageLinkGenerator)
        {
        }

    public override Task ConfigurePage()
    {
        // Add the parent object's ID to the URL
        AdditionalLinkParameters.Add(new PageParameterValues
            {
                { typeof(WebChannelSettingsEditSection), WebChannelSettingsId}
            });
        PageConfiguration.UIFormName = "webchannelsnippetedit";
        return base.ConfigurePage();
    }

    // Ensure that the child object's foreign key is set
    protected override Task FinalizeInfoObject(WebChannelSnippetInfo infoObject, IFormFieldValueProvider fieldValueProvider, CancellationToken cancellationToken)
    {
        infoObject.WebChannelSnippetWebChannelSettingsID = WebChannelSettingsId;

        return base.FinalizeInfoObject(infoObject, fieldValueProvider, cancellationToken);
    }
}
```

Now you should have a new section in your **Project settings** application with SEO settings and code snippets for each channel.
## See the result
With these tools you should be able to create a hierarchical custom module structure with multiple layers, including one-to-one and one-to-many relationships. You can also create channel-specific subsections in the admin UI.
If you followed the example, try going to the **Channel management** application a few times to create and delete channels. Each time you navigate to **Project settings → Channel settings** , you’ll be able to see the available channel settings update accordingly, each with is own distinct _SEO settings_ and its own empty set of snippets.
Your browser does not support the video tag. 
[ Previous page ](/modules/custom-settings/model-channel-specific-settings)
6 of 9
[ Mark complete and continue ](/modules/custom-settings/access-channel-specific-settings-options-pattern)
![]()
[]()[]()
