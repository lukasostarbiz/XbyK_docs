# Admin UI localization
  * [ Copy page link ](documentation/developers-and-admins/customization/admin-ui-localization#) | [Get HelpService ID](documentation/developers-and-admins/customization/admin-ui-localization#)
Core MVC 5


[✖](documentation/developers-and-admins/customization/admin-ui-localization# "Close page link panel") [Copy to clipboard](documentation/developers-and-admins/customization/admin-ui-localization#)
Xperience allows you to localize the administration interface, enabling users to select their preferred working language.
**Admin UI vs. content localization**
Admin UI localization affects only the administration interface (buttons, menus, labels, etc.). This is different from [Languages](documentation/developers-and-admins/configuration/languages), which manages language versions of your content.
## Export system localization resources
Xperience includes a `dotnet` utility to export all system localization resources (Xperience’s default UI strings) to **.resx** files. This is useful when creating translations for new languages – you can export the default resources as a starting point for translation.
CMD
**Export command syntax**
Copy
```
dotnet run --no-build -- --kxp-export-localization-resources --export-path {path}
```

The export always contains the default `en-US` localization and all [registered languages](documentation/developers-and-admins/customization/admin-ui-localization#register-localized-resources).
### Export options
Option |  Description  
---|---  
`--export-path` |  **(Required)** The directory path where .resx files will be created.  
`--system-only` |  **(Optional)** Exports only the default system resources. When not provided, the utility exports both system and custom resources. The system treats all external resources registered using the [RegisterLocalizationResource](documentation/developers-and-admins/customization/integrate-custom-code#store-application-resources-in-resource-files) attribute as custom resources.  
`--skip-confirmation` |  **(Optional)** Skips the confirmation prompt before export.  
CMD
**Examples of usage**
Copy
```
# Exports all resources to a local directory
dotnet run --no-build -- --kxp-export-localization-resources --export-path "./localization-export"

# Exports only system resources
dotnet run --no-build -- --kxp-export-localization-resources --export-path "./system-resources" --system-only
```

### Export output structure
The export creates a structured directory with _.resx_ files organized by resource type:
```
{export-path}/
├── en-US/
│   ├── system/
│   │   ├── localization.client.en-US.resx
│   │   ├── localization.server.en-US.resx
│   │   └── localization.builder.en-US.resx
│   └── custom/
│       ├── localization.client.en-US.resx
│       ├── localization.server.en-US.resx
│       └── localization.builder.en-US.resx
└── {language}
    └───
```

Each exported _.resx_ file contains different types of localization resources:
  * **localization.client.en-US.resx** – Contains strings used in client-side JavaScript/TypeScript admin components, such as button labels, validation messages, and UI text that appears in the browser. Client resources are bundled and sent to the browser when the admin UI loads, making them immediately available on the client.
  * **localization.server.en-US.resx** – Contains strings used in server-side C# admin code, including API responses, server-generated messages, and backend validation text. These resources are resolved dynamically by the backend as part of API responses and request lifecycle processing.
  * **localization.builder.en-US.resx** – Contains strings specific to [builder](documentation/developers-and-admins/development/builders) components, including widget names, property labels, and builder-specific UI elements.


The _custom_ folder contains external resources registered using the [RegisterLocalizationResource](documentation/developers-and-admins/customization/integrate-custom-code#store-application-resources-in-resource-files) attribute.
## Translation workflow
The approach for translating admin UI resources depends on specific goals and target audience.
### Create a default localization package
To create a general-purpose localization package for the admin UI:
  1. [Export](documentation/developers-and-admins/customization/admin-ui-localization#export-system-localization-resources) system resources using `--system-only` to get Xperience’s English strings:
CMD
**Export system resources**
Copy
```
dotnet run --no-build -- --kxp-export-localization-resources --export-path "./system-resources" --system-only
```

  2. Translate the resources to your target language.
  3. Package and distribute the translations as a [NuGet package](documentation/developers-and-admins/customization/admin-ui-localization#distribute-localization-files) for others to use.


This approach creates a focused translation package containing only the default resources, making it suitable for sharing and reuse across different projects.
### Translate everything for local consumption
If your goal is to fully localize your specific application, including all custom resources:
  1. [Export](documentation/developers-and-admins/customization/admin-ui-localization#export-system-localization-resources) all resources to obtain all strings that need to be localized:
CMD
**Export all resources**
Copy
```
dotnet run --no-build -- --kxp-export-localization-resources --export-path "./localization-export"
```

  2. Translate both system and custom .resx files to your target language, covering all admin UI text in your application.
**Naming convention**
Custom projects are not required to follow the naming convention used in exported files; use naming conventions that fit your project structure. For an example, see the [finished branch of our Training guides repository](https://github.com/Kentico/xperience-by-kentico-training-guides/tree/finished/src/TrainingGuides.Admin/Localization/Resources). 
  3. Translate [custom Froala logic](documentation/developers-and-admins/customization/admin-ui-localization#froala-rich-text-editor).
  4. Register the translated resources in your application using the [registration API](documentation/developers-and-admins/customization/admin-ui-localization#register-localized-resources), including both system and custom translations.
  5. Configure the new languages in [AdminLocalizationOptions](documentation/developers-and-admins/customization/admin-ui-localization#configure-available-languages-for-users).


This approach ensures complete localization coverage for your specific application, including any custom components or extensions you’ve developed.
**Translation volume and tooling**
Note that exported system resources contain thousands of localization strings. Professional translation tools or AI-powered translation services handle this volume efficiently. When using AI tools, process resources in manageable batches and have a language professional review the results to ensure accuracy and proper context.
## Register localized resources
To add custom localization resources for your admin UI translations, create and register _.resx_ resource files using the [conventional .NET approach](https://learn.microsoft.com/en-us/dotnet/core/extensions/create-resource-files).
  1. Create a new .NET resource file in your [custom assembly](documentation/developers-and-admins/customization/integrate-custom-code). See [Create resource files for .NET apps](https://learn.microsoft.com/en-us/dotnet/core/extensions/create-resource-files).
  2. Register the resource file using the [RegisterLocalizationResource](documentation/developers-and-admins/customization/integrate-custom-code#store-application-resources-in-resource-files) attribute:
C#
**Register localization resources**
Copy
```
using CMS.Localization;

// Register resources for additional languages
[assembly: RegisterLocalizationResource(typeof(LocalizationClient), LocalizationTarget.Client, "cs-CZ")]
[assembly: RegisterLocalizationResource(typeof(LocalizationClient), LocalizationTarget.Client, "de-DE")]

namespace Acme.Web.Admin;

// Class encapsulating the localization resource files
public class LocalizationClient {}
```

The `RegisterLocalizationResource` attribute accepts an optional `LocalizationTarget` parameter to specify whether resources are used on the client-side, server-side, or in builder components. If not specified, defaults to `LocalizationTarget.Server`. For more details about the `LocalizationTarget` enum, see [Integrate custom code](documentation/developers-and-admins/customization/integrate-custom-code#store-application-resources-in-resource-files).


**Project configuration for multiple language variants**
When registering multiple language variants using a single registration class (as shown in the example), configure the `LogicalName` property in the project’s _.csproj_ file to map _.resx_ files to the registration class. See the [TrainingGuides.Admin project](https://github.com/Kentico/xperience-by-kentico-training-guides/blob/finished/src/TrainingGuides.Admin/TrainingGuides.Admin.csproj) for an example.
Alternatively, place each _.resx_ file next to its own _.cs_ registration class, which requires a separate class per variant but no _.csproj_ configuration.
**English resources are optional**
English (`en-US`) _.resx_ files are not required in the project unless overriding Xperience’s default English strings is necessary. The system uses built-in English resources automatically. Register English resources only when customizing or replacing the default English text.
## Distribute localization files
You can distribute your translations as NuGet packages. This approach allows other developers to easily include your translations.
  1. Structure your project (typically a custom class library) to include the translated resource files and their [registration](documentation/developers-and-admins/customization/admin-ui-localization#register-localized-resources).
  2. Package the translation using `dotnet pack`, and distribute:
CMD
**Create NuGet package**
Copy
```
dotnet pack --configuration Release --output ./packages
```



## Configure available languages for users
Use the `AdminLocalizationOptions` class to configure available languages for the administration interface. This configuration determines which languages appear in the language selector dropdown on the [profile page](documentation/business-users/manage-your-user-profile), allowing users to choose their preferred admin UI language.
**English language configuration**
English (`en-US`) must be explicitly included in the `SupportedCultures` collection to be available as a language option, even when the project does not include English _.resx_ files. The system uses built-in English resources automatically, but users can only select English if configured in `SupportedCultures`.
The following code sample demonstrates the configuration:
C#
**Configure admin UI localization (Program.cs)**
Copy
```
using CMS.Localization;

var builder = WebApplication.CreateBuilder(args);

builder.Services.AddKentico( ... );

// Configures admin UI localization - must be placed after the AddKentico call to ensure localization services are registered
builder.Services.Configure<AdminLocalizationOptions>(options =>
{
    options.DefaultCultureCode = "en-US";
    options.SupportedCultures = new[]
    {
        new AdminCulture
        {
            CultureCode = "en-US",
            DisplayName = "English (United States)"
        },
        new AdminCulture
        {
            CultureCode = "cs-CZ",
            DisplayName = "Czech (Czech Republic)"
        },
        new AdminCulture
        {
            CultureCode = "de-DE",
            DisplayName = "German (Germany)"
        }
    };
});
```

### AdminLocalizationOptions properties
Property |  Type |  Description  
---|---|---  
DefaultCultureCode |  `string` |  The default culture code used when no user preference is set or when the preferred language is unavailable. Must be present in `SupportedCultures`.  
SupportedCultures |  `IEnumerable<AdminCulture>` |  Collection of cultures available for the administration interface.  
**Default content language vs. Default admin UI language**
When editing content in the administration interface, content items are displayed in the default content language configured in [Languages](documentation/developers-and-admins/configuration/languages), regardless of the admin UI language set in `DefaultCultureCode`.
### AdminCulture properties
Property |  Type |  Description  
---|---|---  
CultureCode |  `string` |  The culture code in the format “language-region” (e.g., “en-US”, “cs-CZ”, “de-DE”).  
DisplayName |  `string` |  The display name shown to users in the language selector. If not provided, uses the culture’s native display name.  
UseDefaultSystemUICulture |  `bool` |  Indicates whether the default system UI culture provided by Kentico (en-US) will be used. This is used to set a different date format while preserving the default en-US translations provided by Kentico. C# **UseDefaultSystemUICulture example** Copy ```
new AdminCulture
{
    CultureCode = "de-DE",
    DisplayName = "German date and time formatting",
    // Uses English UI text with German formatting
    UseDefaultSystemUICulture = true
}
```
  
## Access localized resources
When localizing your custom code and components, use the following APIs.
### Back-end
Access registered resource strings using [ILocalizationService](https://api-reference.kentico.com/api/CMS.Core.ILocalizationService.html):
C#
**Using localized strings in admin components**
Copy
```
// Contains instance resolved using dependency injection
private readonly ILocalizationService localizationService;

// Gets localized string for current UI culture
var message = localizationService.GetString("myproject.admin.users.createButton");

// Gets localized string for specific culture
var germanMessage = localizationService.GetString("myproject.admin.users.createButton", "de-DE");
```

### Front-end
[Client-side admin code](documentation/developers-and-admins/customization/extend-the-administration-interface) uses the [i18n library](https://react.i18next.com/) instead of `ILocalizationService`. The i18n library is automatically initialized with the current user’s [preferred admin UI culture](documentation/developers-and-admins/customization/admin-ui-localization#resource-string-resolution-and-fallbacks) and provides access to client-side localization resources.
Access localized strings in React components using the `useTranslation` hook from `react-i18next`:
TSX
**Using localized strings in React admin components**
Copy
```
import { useTranslation } from 'react-i18next';

export const MyAdminComponent = () => {
    const { t } = useTranslation();

    return (
        <div>
            <h1>{t('myproject.admin.users.title')}</h1>
            <button>{t('myproject.admin.users.createButton')}</button>
            <p>{t('myproject.validation.emailRequired')}</p>
        </div>
    );
};
```

See the [i18n documentation](https://react.i18next.com/) for a full usage guide and other resources.
#### Accessing globalization context
Use the `useGlobalization` hook to access the current admin UI culture information, including both resource string culture and datetime format culture.
The `useGlobalization` hook provides:
  * `uiLanguage` – The culture code used for resource string localization (matches admin UI language selection).
  * `uiLocale` – The culture code used for datetime and number formatting (respects the `UseDefaultSystemUICulture` setting).


TSX
**useGlobalization example**
Copy
```
import { useGlobalization } from '@kentico/xperience-admin-base';

export const MyAdminComponent = () => {
    const { uiLanguage, uiLocale } = useGlobalization();

    return (
        <div>
            <p>Current UI language: {uiLanguage}</p>
            <p>Current UI locale: {uiLocale}</p>
        </div>
    );
};
```

This is particularly useful when you need to format dates, numbers, or other culture-specific content in your React components while maintaining consistency with the admin UI’s [localization settings](documentation/developers-and-admins/customization/admin-ui-localization#configure-available-languages-for-users).
### Builder components
For [builder](documentation/developers-and-admins/development/builders) elements that are used exclusively within the administration interface (such as widget configuration dialogs, or Page Builder inline editors), use `IAdminBuildersLocalizationService`:
cshtml
**Using IAdminBuildersLocalizationService in inline editors**
Copy
```
@using Kentico.PageBuilder.Web.Mvc
@using Kentico.Web.Mvc
@using Kentico.Builder.Web.Mvc

@* Injects the localization service for admin UI components *@
@inject IAdminBuildersLocalizationService localizationService

@model LearningKitCore.Components.PageBuilder.InlineEditors.NumberEditor.NumberEditorModel

@using (Html.Kentico().BeginInlineEditor("number-editor", Model.SelectedNumber))
{
    <div style="position: absolute; top: 0px; right: 0px;">
        <button id="add-btn" type="button">
            @* Gets the localized string using current admin UI culture *@
            @localizationService.GetString("my.project.editors.numbereditor.add")
        </button>
        <button id="sub-btn" type="button">
            @localizationService.GetString("my.project.editors.numbereditor.subtract")
        </button>
    </div>
}
```

For content that appears on the live site, use the framework’s `IHtmlLocalizer` API.
**Resource key consistency**
Use the same resource keys in both your _.resx_ files and client-side code. The admin UI automatically loads client-side resources (registered with `LocalizationTarget.Client`) and makes them available to the i18n library.
### Admin UI fields
Fields in the admin UI support localization expression in the format: `{$ resource.key $}`
These expression automatically resolve to a matching string based on the language currently selected by the user. If the localization is not found in the target language, the expression resolves according to [Resource string resolution and fallbacks](documentation/developers-and-admins/customization/admin-ui-localization#resource-string-resolution-and-fallbacks).
For example, the following screenshot demonstrates the localization of field metadata via the [field editor](documentation/developers-and-admins/customization/field-editor) (label, explanation text, etc.).
[![Localization expression usage](docsassets/documentation/admin-ui-localization/localization_expressions.png)](https://docs.kentico.com/docsassets/documentation/admin-ui-localization/localization_expressions.png)
In the editing interface of the corresponding object, the expression resolves to the underlying string.
[![Localized field value](docsassets/documentation/admin-ui-localization/localization_example.png)](https://docs.kentico.com/docsassets/documentation/admin-ui-localization/localization_example.png)
### Froala rich text editor
The [Froala rich text editor](documentation/business-users/rich-text-editor) supports localization to match the user’s [preferred admin UI language](documentation/developers-and-admins/customization/admin-ui-localization). The localization system works on multiple levels to ensure both system and custom elements are properly translated.
#### Default editor localization
By default, the editor automatically localizes its interface based on the user’s culture provided from the backend. The editor supports multiple languages out of the box, as documented in the [Froala languages documentation](https://froala.com/wysiwyg-editor/languages/).
The editor automatically applies the appropriate language pack when all of the following conditions are met:
  * The user has set a preferred admin UI language in their profile
  * The language is supported by Froala’s default language packs


If the language is not supported by Froala, the editor defaults to English (`en-US`).
#### Localize system customizations
Xperience’s customizations to the Froala editor (such as custom toolbar buttons and features) are not covered by Froala’s default localization. These strings are located inside `localization.client.en-US.resx` in the [exported resources](documentation/developers-and-admins/customization/admin-ui-localization#export-system-localization-resources) and must be translated as part of your localization efforts.
#### Localize custom configurations
For [custom rich text editor configurations](documentation/developers-and-admins/configuration/rich-text-editor-configuration/rich-text-editor-customization) , you can use localization macros to translate custom options and display names.
The corresponding resource strings must be registered in a [resource file](documentation/developers-and-admins/customization/integrate-custom-code#store-application-resources-in-resource-files) with `LocalizationTarget.Server`.
Rich text editor configurations support localization macros in the format `{$resource.key$}`. This allows you to reference registered resource strings for any option with a custom display name. For example, when localizing text for [inline styles](https://froala.com/wysiwyg-editor/examples/inline-styles/), you can use the macros like so:
JSON
**Example - Localized custom configuration**
Copy
```
{
  "toolbarButtons": [
    "bold", "italic", "underline", "|", "inlineStyle", "customButton"
  ],
  "inlineStyles": {
    "{$mycompany.editor.redBoldText$}": "font-size: 16px; font-weight: bold; color: red;",
    "{$mycompany.editor.blueItalicText$}": "font-style: italic; color: blue;",
    "{$mycompany.editor.highlightText$}": "background-color: yellow; padding: 2px;"
  }
}
```

## Resource string resolution and fallbacks
The localization logic uses a multi-step fallback process to resolve resource strings when a specific localized string is not available:
  1. **User preferred culture** – The system first attempts to load the resource string using the user’s preferred admin UI language (if set in their profile). If no user preference is set, the system uses the `DefaultCultureCode` specified in [AdminLocalizationOptions](documentation/developers-and-admins/customization/admin-ui-localization#adminlocalizationoptions-properties).
  2. **English fallback (en-US)** – If the resource string is not found in the selected culture, the system falls back to English (`en-US`).
  3. **Resource key display** – If no localized string is found in English, the system displays the resource key itself as a last resort.


This cascading fallback helps ensure users see meaningful text in the admin UI even if some resources remain untranslated.
### Example fallback scenario
Consider a user with Czech (`cs-CZ`) as their preferred language, and an application configured with German (`de-DE`) as the default culture:
  1. System looks for the resource key in Czech (`cs-CZ`) resources (user’s preference).
  2. If the translated resource isn’t found, the system falls back directly to English (`en-US`) resources.
  3. If the key doesn’t exist in English, the system displays the resource key itself.


If the user had no preferred language set, the system would use German (`de-DE`) in the first step instead of Czech.
## Example implementation
For an admin UI localization implementation example, see the [finished branch of the Training guides repository](https://github.com/Kentico/xperience-by-kentico-training-guides/tree/finished). The example includes localized custom [Page Builder widget](documentation/developers-and-admins/development/builders/page-builder/widgets-for-page-builder), a custom [UI page](documentation/developers-and-admins/customization/extend-the-administration-interface/ui-pages), and sample localizations in Spanish and French. The localization resources are defined in the _TrainingGuides.Admin_ project.
**Demo translations only**
The Spanish and French resource strings in the Training guides example are machine-translated for demonstration purposes only and may not be grammatically accurate. These translations are not suitable for production use.