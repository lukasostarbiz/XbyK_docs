---
source: https://docs.kentico.com/modules/email-builder/complete-flexible-template
scrape_date: 2026-01-26
---

Module: Email Builder
12 of 13 Pages
# Complete the flexible template and its section
With the Razor component in place, let’s create a section that utilizes our email builder columns component. Create the _~/Features/Shared/EmailBuilder/Sections_ folder.
### Define section properties.
To feed the parameters of our columns component, we need properties that allow editors to choose different layout and styling options.
Add properties for the corner style of the section, and the color scheme of each potential column. Depending on the selected layout, the component will not render certain columns. Let’s use visibility conditions and a configurator to conditionally hide columns in each scenario.
#### Section properties
C#
**GeneralEmailSectionProperties.cs**
Copy
```
using Kentico.EmailBuilder.Web.Mvc;
using Kentico.Xperience.Admin.Base.FormAnnotations;
using TrainingGuides.Web.Features.Shared.OptionProviders;
using TrainingGuides.Web.Features.Shared.OptionProviders.ColorScheme;
using TrainingGuides.Web.Features.Shared.OptionProviders.ColumnLayout;
using TrainingGuides.Web.Features.Shared.OptionProviders.CornerStyle;

namespace TrainingGuides.Web.Features.Shared.EmailBuilder.Sections;

public class GeneralEmailSectionProperties : IEmailSectionProperties
{
    [DropDownComponent(
        Label = "Column layout",
        ExplanationText = "Select the layout of the editable areas in the template.",
        DataProviderType = typeof(DropdownEnumOptionProvider<ColumnLayoutOption>),
        Order = 10)]
    public string ColumnLayout { get; set; } = nameof(ColumnLayoutOption.OneColumn);

    [DropDownComponent(
        Label = "Corner type",
        ExplanationText = "Select the corner type of the template.",
        DataProviderType = typeof(DropdownEnumOptionProvider<CornerStyleOption>),
        Order = 20)]
    public string CornerStyle { get; set; } = nameof(CornerStyleOption.Round);

    [DropDownComponent(
        Label = "Column 1 color scheme",
        ExplanationText = "Select the color scheme for the first column.",
        DataProviderType = typeof(DropdownEnumOptionProvider<ColorSchemeOption>),
        Order = 30)]
    public string Column1ColorScheme { get; set; } = nameof(ColorSchemeOption.Light1);

    [DropDownComponent(
        Label = "Column 2 color scheme",
        ExplanationText = "Select the color scheme for the second column.",
        DataProviderType = typeof(DropdownEnumOptionProvider<ColorSchemeOption>),
        Order = 40)]
    // This property is only visible if the layout is not single-column
    [VisibleIfNotEqualTo(nameof(ColumnLayout), nameof(ColumnLayoutOption.OneColumn))]
    public string Column2ColorScheme { get; set; } = nameof(ColorSchemeOption.Light1);

    [DropDownComponent(
        Label = "Column 3 color scheme",
        ExplanationText = "Select the color scheme for the third column.",
        DataProviderType = typeof(DropdownEnumOptionProvider<ColorSchemeOption>),
        Order = 50)]
    // This property is visible if the layout is any of multiple three-column options
    [FormComponentConfiguration(typeof(GeneralEmailSectionColumn3Configurator), [nameof(ColumnLayout)])]
    public string Column3ColorScheme { get; set; } = nameof(ColorSchemeOption.Light1);

}
```

#### Configurator
C#
**GeneralEmailSectionColumn3Configurator.cs**
Copy
```
using Kentico.Xperience.Admin.Base.Forms;
using TrainingGuides.Web.Features.Shared.OptionProviders;
using TrainingGuides.Web.Features.Shared.OptionProviders.ColumnLayout;
using TrainingGuides.Web.Features.Shared.VisibilityConditions;

namespace TrainingGuides.Web.Features.Shared.EmailBuilder.Sections;

public class GeneralEmailSectionColumn3Configurator : FormComponentConfigurator<DropDownComponent>
{
    private readonly IEnumStringService enumStringService;

    public GeneralEmailSectionColumn3Configurator(IEnumStringService enumStringService)
    {
        this.enumStringService = enumStringService;
    }

    public override async Task Configure(DropDownComponent formComponent, IFormFieldValueProvider formFieldValueProvider, CancellationToken cancellationToken)
    {
        if (!GetShowColumn3(formFieldValueProvider))
        {
            HideField(formComponent);
        }
    }

    private bool GetShowColumn3(IFormFieldValueProvider formFieldValueProvider)
    {
        if (formFieldValueProvider.TryGet(nameof(GeneralEmailSectionProperties.ColumnLayout), out string columnLayout))
        {
            var layout = enumStringService.Parse(columnLayout, ColumnLayoutOption.OneColumn);
            return layout is ColumnLayoutOption.ThreeColumnEven or ColumnLayoutOption.ThreeColumnSmLgSm;
        }
        return true;
    }

    private void HideField(DropDownComponent formComponent) =>
        formComponent.VisibilityConditions.Add(new Invisible());
}
```

#### Custom visibility condition for configurator
C#
**~/Features/Shared/VisibilityConditions/Invisible.cs**
Copy
```
using Kentico.Xperience.Admin.Base.Forms;

namespace TrainingGuides.Web.Features.Shared.VisibilityConditions;

// Custom visibility condition that is always false, used to dynamically hide fields in configurators
public class Invisible : VisibilityCondition
{
    public override bool Evaluate(IFormFieldValueProvider formFieldValueProvider) => false;
}
```

### Define the section’s Razor component
Thanks to the `EmailBuilderColumns` component [we built earlier](/modules/email-builder/build-reusable-columns-component), the section component can be fairly simple.
In the code-behind, register the component and include a `GeneralEmailSectionProperties` parameter to access marketers’ configurations. Then utilize the `IEnumStringService` to parse enumeration values from the properties.
C#
**GeneralEmailSection.razor.cs**
Copy
```
using Kentico.EmailBuilder.Web.Mvc;
using TrainingGuides.Web.Features.Shared.EmailBuilder.Sections;

using Microsoft.AspNetCore.Components;
using TrainingGuides.Web.Features.Shared.OptionProviders.ColumnLayout;
using TrainingGuides.Web.Features.Shared.OptionProviders.CornerStyle;
using TrainingGuides.Web.Features.Shared.OptionProviders.ColorScheme;
using TrainingGuides.Web.Features.Shared.OptionProviders;

[assembly: RegisterEmailSection(
    identifier: GeneralEmailSection.IDENTIFIER,
    name: "General email template section",
    componentType: typeof(GeneralEmailSection),
    PropertiesType = typeof(GeneralEmailSectionProperties),
    Description = "Section for the General email template.",
    IconClass = "icon-l-cols-3")]

namespace TrainingGuides.Web.Features.Shared.EmailBuilder.Sections;

public partial class GeneralEmailSection : ComponentBase
{
    public const string IDENTIFIER = "TrainingGuides.GeneralEmailSection";

    [Inject]
    private IEnumStringService EnumStringService { get; set; } = default!;

    private ColumnLayoutOption ColumnLayout => EnumStringService.Parse(Properties.ColumnLayout, ColumnLayoutOption.OneColumn);
    private CornerStyleOption CornerStyle => EnumStringService.Parse(Properties.CornerStyle, CornerStyleOption.Round);
    private ColorSchemeOption Column1ColorScheme =>
        EnumStringService.Parse(Properties.Column1ColorScheme, ColorSchemeOption.Light1);
    private ColorSchemeOption Column2ColorScheme =>
        EnumStringService.Parse(Properties.Column2ColorScheme, ColorSchemeOption.Light1);
    private ColorSchemeOption Column3ColorScheme =>
        EnumStringService.Parse(Properties.Column3ColorScheme, ColorSchemeOption.Light1);

    [Parameter]
    public GeneralEmailSectionProperties Properties { get; set; } = new();
}
```

In the template’s Razor file, wrap the columns component in a `mj-section` component, for consistency with the sections from the Email Builder Starter Kit.
Depending on the conventions of your project, you may use different MJML components at different levels. Keep in mind that **MJML has strict guidelines for which components are allowed where**. For example, `mj-section` cannot nest inside of other `mj-section` components, or in lower-level components like `mj-column`.
cshtml
**GeneralEmailSection.razor**
Copy
```
@using Kentico.VisualBuilderComponents.Rcl.Components
@using TrainingGuides.Web.Features.Shared.EmailBuilder.RazorComponents

<mj-section width="600px" padding="8px">
    <EmailBuilderColumns ColumnLayout="@ColumnLayout"
        CornerStyle="@CornerStyle"
        Column1ColorScheme="@Column1ColorScheme"
        Column2ColorScheme="@Column2ColorScheme"
        Column3ColorScheme="@Column3ColorScheme" />
</mj-section>
```

### Save the component identifier
If you use a shared component identifiers file in your project, like we do in the “https://github.com/Kentico/xperience-by-kentico-training-guides/tree/finished” linkText=“Training guides repository” %}, you can add the general section’s identifier.
C#
**~/ComponentIdentifiers.cs**
Copy
```
...
using TrainingGuides.Web.Features.Shared.EmailBuilder.Sections;
...
public static class ComponentIdentifiers
{
    ...
    public static class EmailBuilderSections
    {
        public const string GENERAL = GeneralEmailSection.IDENTIFIER;

        // Kentico.Xperience.Mjml.StarterKit.Rcl sections
        public const string FULL_WIDTH = FullWidthEmailSection.IDENTIFIER;
        public const string TWO_COLUMN = TwoColumnEmailSection.IDENTIFIER;
    }
}
 
 
 


```

## Create a general template
With the section in place, we can tie everything together with an Email Builder template.
Create the _~/Features/Shared/Emailbuilder/Templates_ folder to hold files related to the general email template.
### Define email template properties
Specify the template properties. We’ll include properties for the background color and corner shape, and allow multiple, vertically-stacked editable areas.
It’s possible to add rows to a single editable area by adding multiple sections to it.
Using multiple editable areas provides you the option to add contrasting styles for template rows, compared to sections within a single editable area, such as a larger space between. Additionally, it will make it more difficult for marketers to accidentally delete entire rows on templates that are meant to have a minimum number.
You can exclude this functionality if it does not meet your requirements.
C#
**GeneralEmailTemplateProperties.cs**
Copy
```
using Kentico.EmailBuilder.Web.Mvc;
using Kentico.Xperience.Admin.Base.FormAnnotations;
using TrainingGuides.Web.Features.Shared.OptionProviders;
using TrainingGuides.Web.Features.Shared.OptionProviders.ColorScheme;
using TrainingGuides.Web.Features.Shared.OptionProviders.CornerStyle;

namespace TrainingGuides.Web.Features.Shared.EmailBuilder.Templates;

public class GeneralEmailTemplateProperties : IEmailTemplateProperties
{
    [NumberInputComponent(
        Label = "Number of rows",
        ExplanationText = "The number of rows in the template. (maximum of 5)",
        Order = 10)]
    public int NumberOfRows { get; set; } = 1;

    [DropDownComponent(
        Label = "Corner type",
        ExplanationText = "Select the corner type of the template.",
        DataProviderType = typeof(DropdownEnumOptionProvider<CornerStyleOption>),
        Order = 20)]
    public string CornerStyle { get; set; } = nameof(CornerStyleOption.Round);

    [DropDownComponent(
        Label = "Color scheme",
        ExplanationText = "Select the color scheme of the template.",
        DataProviderType = typeof(DropdownEnumOptionProvider<ColorSchemeOption>),
        Order = 30)]
    public string MainColorScheme { get; set; } = nameof(ColorSchemeOption.Light2);
}
```

Now we can move on to the Razor component for the template. The approach for the template’s layout is much simpler than dealing with columns, so there’s no need to break it out into a separate component.
Aside from the layout, we also need to account for information in the header of the email, like styles and metadata, and the unsubscribe link.
If you haven’t been following along, make sure you have the [email data mapper](/guides/development/email-marketing/use-email-builder-starter-kit#create-the-data-mapper) from earlier in this series.
C#
**GeneralEmailTemplate.razor.cs**
Copy
```
using CMS.EmailMarketing;
using Kentico.Xperience.Mjml.StarterKit.Rcl;
using Kentico.Xperience.Mjml.StarterKit.Rcl.Contracts;
using Kentico.Xperience.Mjml.StarterKit.Rcl.Mapping;
using Microsoft.AspNetCore.Components;
using TrainingGuides.Web.Features.Shared.OptionProviders;
using TrainingGuides.Web.Features.Shared.OptionProviders.ColorScheme;
using TrainingGuides.Web.Features.Shared.OptionProviders.CornerStyle;
using TrainingGuides.Web.Features.Shared.Services;

namespace TrainingGuides.Web.Features.Shared.EmailBuilder.Templates;

public partial class GeneralEmailTemplate : ComponentBase
{
    public const string IDENTIFIER = "TrainingGuides.GeneralEmailTemplate";

    public const string AREA_MAIN = "MainContent";

    private IEmailData emailData = new EmailData(string.Empty, string.Empty);
    private CornerStyleOption CornerStyle => EnumStringService.Parse(Properties.CornerStyle, CornerStyleOption.Round);
    private ColorSchemeOption MainColorScheme => EnumStringService.Parse(Properties.MainColorScheme, ColorSchemeOption.Light2);
    private List<string> RowIdentifiers { get; set; } = [];
    private List<string> MainCssClasses { get; set; } = [];

    private string? cssContent;
    protected string CssContent
    {
        get => cssContent ?? string.Empty;
        set => cssContent = value;
    }

    private EmailRecipientContext? recipient;

    protected EmailRecipientContext Recipient => recipient ??= RecipientContextAccessor.GetContext();

    [Parameter]
    public GeneralEmailTemplateProperties Properties { get; set; } = new();

    [Inject]
    private IEmailRecipientContextAccessor RecipientContextAccessor { get; set; } = default!;

    [Inject]
    private IEmailDataMapper EmailDataMapper { get; set; } = default!;

    [Inject]
    private CssLoaderService CssLoaderService { get; set; } = default!;

    [Inject]
    private IComponentStyleEnumService ComponentStyleEnumService { get; set; } = default!;

    [Inject]
    private IEnumStringService EnumStringService { get; set; } = default!;

    protected override async Task OnInitializedAsync()
    {
        emailData = await EmailDataMapper.Map();
        CssContent = await CssLoaderService.GetCssAsync();

        var cornerCssClasses = ComponentStyleEnumService.GetCornerStyleClasses(CornerStyle);

        MainCssClasses.AddRange(ComponentStyleEnumService.GetColorSchemeClasses(MainColorScheme));
        MainCssClasses.AddRange(cornerCssClasses);

        int numberOfRows = Math.Clamp(Properties.NumberOfRows, 1, 5);
        for (int index = 0; index < numberOfRows; index++)
        {
            string rowIdentifier = index == 0 ? AREA_MAIN : $"Area_{index}";
            RowIdentifiers.Add(rowIdentifier);
        }

    }
}
```

cshtml
**GeneralEmailTemplate.razor**
Copy
```
@using Kentico.VisualBuilderComponents.Rcl.Components
@using TrainingGuides.Web
@using TrainingGuides.Web.Features.Shared.EmailBuilder.RazorComponents;
@using TrainingGuides.Web.Features.Shared.OptionProviders.ColumnLayout

<mjml>
  <mj-head>
    <mj-title>@emailData.EmailSubject</mj-title>
    <mj-attributes>
      <mj-all color="inherit" />
    </mj-attributes>
    <mj-preview>
      @emailData.EmailPreviewText
    </mj-preview>
    <mj-style>
      @((MarkupString)CssContent)
    </mj-style>
    <EmailBuilderStyles />
  </mj-head>
  <mj-body>
    <mj-wrapper css-class="@(string.Join(" ", MainCssClasses))" >
      @foreach (var rowIdentifier in RowIdentifiers)
      {
        <EditableArea AreaIdentifier="@rowIdentifier" DefaultSectionIdentifier="@ComponentIdentifiers.EmailBuilderSections.GENERAL" AllowedSections="[ComponentIdentifiers.EmailBuilderSections.GENERAL]" />
      }
      <mj-text align="center">
        <a href="@Recipient.UnsubscriptionUrl" data-click-tracking="false">Unsubscribe</a>
      </mj-text>
    </mj-wrapper>
    <EmailBuilderScripts />
  </mj-body>
</mjml>
```

### Register the template
We recommend registering the template in a separate file, in case you want to move the template and section into a more portable RCL or NuGet package.
That way, you can locate the registration attribute outside of the separate project, in a location with access to your solution’s content types.
C#
**~/Features/Shared/EmailBuilder/TrainingGuidesEmailBuilderComponentRegister.cs**
Copy
```
using Kentico.EmailBuilder.Web.Mvc;
using TrainingGuides;
using TrainingGuides.Web.Features.Shared.EmailBuilder.Templates;

[assembly: RegisterEmailTemplate(
    identifier: GeneralEmailTemplate.IDENTIFIER,
    name: "General email template",
    componentType: typeof(GeneralEmailTemplate),
    PropertiesType = typeof(GeneralEmailTemplateProperties),
    IconClass = "xp-l-rows-3",
    ContentTypeNames = [BasicEmail.CONTENT_TYPE_NAME])]
```

## See the results
Now your new Email Builder components are in working order!
Create a new email using the _General email template_ to test it out!
Your browser does not support the video tag. 
You can use these components along with widgets from the Email Builder Starter Kit to create an email like this:
[![Screenshot of a finished email](/docsassets/guides/build-a-flexible-email-builder-template/FinishedEmail.png)](/docsassets/guides/build-a-flexible-email-builder-template/FinishedEmail.png)
Due to current system limitations at the time this guide was authored, the text color on the **Email Builder** tab may not match the color specified in a template or section. The correct color will appear on the **Preview** tab and in the sent email.
Your marketers should consider the **Preview** tab as the _source of truth_.
[ Previous page ](/modules/email-builder/build-reusable-columns-component)
12 of 13
[ Mark complete and continue ](/modules/email-builder/email-builder-learn-more)
![]()
[]()[]()
