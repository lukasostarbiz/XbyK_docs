---
source: https://docs.kentico.com/modules/email-builder/prepare-template-styling-options
scrape_date: 2026-01-22
---

Module: Email Builder
10 of 13 Pages
# Prepare styling options for a flexible template
Email Builder in Xperience by Kentico empowers marketers to create visually engaging emails using a drag-and-drop interface. Let’s create a flexible, reusable template that supports a variety of layouts and color schemes.
We’ll walk through the process of building a general-purpose template and section for Email Builder, supporting a range of visual customization options for your marketers.
[![Screenshot of a finished email](/docsassets/guides/build-a-flexible-email-builder-template/FinishedEmail.png)](/docsassets/guides/build-a-flexible-email-builder-template/FinishedEmail.png)
## Prepare styling options
### Create an enumeration dropdown provider
To support flexible layouts and color schemes, start by creating dropdown providers for enumerations. This allows you to manage the options marketers can specify for column layout, corner style, and color scheme.
You may be familiar with a DropdownEnumOptionProvider class from our [dropdown provider guide](/guides/development/page-builder/map-enum-to-dropdown#implement-a-generic-mapper-class). If your project does not have the class, you add it by following the steps in that guide or copy the completed code from [the finished branch of the Training guides repository.](https://github.com/Kentico/xperience-by-kentico-training-guides/blob/finished/src/TrainingGuides.Web/Features/Shared/OptionProviders/DropdownEnumOptionProvider.cs).
### Define a service for parsing enum values
Next, create and register a service for parsing enum values from strings, using the [Enums.NET NuGet package](https://www.nuget.org/packages/Enums.NET/). It should take a _default value_ to fall back to when the provided string does not contain an enum value.
We also outline this in the [dropdown provider guide](/guides/development/page-builder/map-enum-to-dropdown#create-a-service-to-parse-enumeration-values-from-strings) mentioned earlier, which creates the following files:
  * [~/Features/Shared/OptionProviders/IEnumStringService.cs](https://github.com/Kentico/xperience-by-kentico-training-guides/blob/finished/src/TrainingGuides.Web/Features/Shared/OptionProviders/IEnumStringService.cs)
  * [~/Features/Shared/OptionProviders/EnumStringService.cs](https://github.com/Kentico/xperience-by-kentico-training-guides/blob/finished/src/TrainingGuides.Web/Features/Shared/OptionProviders/EnumStringService.cs)
  * [~/ServiceCollectionExtensions.cs](https://github.com/Kentico/xperience-by-kentico-training-guides/blob/finished/src/TrainingGuides.Web/ServiceCollectionExtensions.cs)


### Include enumerations for styling
With a dropdown provider and a class for parsing enumeration values in place, we can move on to the enumerations themselves.
If you haven’t completed our [Page Builder module](/modules/page-builder) or [guides](/guides/development/page-builder), add these files to your project:
  * [~/Features/Shared/OptionProviders/ColorScheme/ColorSchemeOption.cs](https://github.com/Kentico/xperience-by-kentico-training-guides/blob/finished/src/TrainingGuides.Web/Features/Shared/OptionProviders/ColorScheme/ColorSchemeOption.cs)
  * [~/Features/Shared/OptionProviders/ColumnLayout/ColumnLayoutOption.cs](https://github.com/Kentico/xperience-by-kentico-training-guides/blob/finished/src/TrainingGuides.Web/Features/Shared/OptionProviders/ColumnLayout/ColumnLayoutOption.cs)
  * [~/Features/Shared/OptionProviders/CornerStyle/CornerStyleOption.cs](https://github.com/Kentico/xperience-by-kentico-training-guides/blob/finished/src/TrainingGuides.Web/Features/Shared/OptionProviders/CornerStyle/CornerStyleOption.cs)


### Add styles corresponding to the enums
Now we have the framework for allowing editors to specify styling options, but we don’t have any actual styles to apply based on their selections.
Let’s define CSS classes to style your Email Builder components according to the selected options.
CSS
**~/wwwroot/EmailBuilder.css**
Copy
```
...
.tg-tiny-margin {
    margin: 1px;
}

.tg-corner-shrp {
    border-radius: 0;
}

.tg-corner-rnd {
    border-radius: 32px;
}

.tg-corner-v-rnd {
    border-radius: 64px;
}

.tg-bg-primary {
    background: #8107c1;
}

.tg-bg-secondary {
    background: #f0561a;
}

.tg-bg-none {
    background: none;
}

.tg-bg-light-1 {
    background: white;
}

.tg-bg-light-2 {
    background: #efe6f9;
}

.tg-bg-light-3 {
    background: #ebf5ff;
}

.tg-txt-light {
    color: white;
}

.tg-txt-light a {
    color: #f0561a;
}

.tg-txt-medium {
    color: #f0561a;
}

.tg-txt-medium a {
    color: white;
}
.tg-txt-dark {
    color: #1e1a1b;
}

.tg-txt-dark a {
    color: #f0561a;
}
```

### Link these styles to their enums with a service
Now, all that’s left is to build a service that retrieves a collection of CSS styles based on a provided enumeration value.
The column layout will change the markup of an email, so we’ll handle that logic in the specific Razor component. For this service, focus on retrieving classes for color scheme and corner style, which are controlled by CSS classes.
Add the following files:
  * [~/Features/Shared/Services/IComponentStyleEnumService.cs](https://github.com/Kentico/xperience-by-kentico-training-guides/blob/finished/src/TrainingGuides.Web/Features/Shared/Services/IComponentStyleEnumService.cs)
  * [~/Features/Shared/Services/ComponentStyleEnumService.cs](https://github.com/Kentico/xperience-by-kentico-training-guides/blob/finished/src/TrainingGuides.Web/Features/Shared/Services/ComponentStyleEnumService.cs)
  * [~/ServiceCollectionExtensions.cs](https://github.com/Kentico/xperience-by-kentico-training-guides/blob/finished/src/TrainingGuides.Web/ServiceCollectionExtensions.cs)


You can find more implementation details for the style service in the [versatile page templates guide](/guides/development/page-builder/create-versatile-templates-part-1#create-a-service-to-retrieve-styles).
[ Previous page ](/modules/email-builder/implement-structured-data-template)
10 of 13
[ Mark complete and continue ](/modules/email-builder/build-reusable-columns-component)
![]()
[]()[]()
