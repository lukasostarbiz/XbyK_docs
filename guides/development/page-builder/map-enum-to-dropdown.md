# Add a custom dropdown provider for administration components
  * How-to| [ Copy page link ](guides/development/page-builder/map-enum-to-dropdown#) | [Get HelpService ID](guides/development/page-builder/map-enum-to-dropdown#) | This page is part of a module: [ Page Builder ](modules/page-builder)
Core MVC 5


[✖](guides/development/page-builder/map-enum-to-dropdown# "Close page link panel") [Copy to clipboard](guides/development/page-builder/map-enum-to-dropdown#)
When editors create pages with [_Page Builder_](documentation/developers-and-admins/development/builders/page-builder), they utilize components (page templates, sections, widgets), prepared by the developers. Each component has properties, that editors set in the Xperience administration using [UI controls](documentation/developers-and-admins/customization/extend-the-administration-interface/ui-form-components/reference-admin-ui-form-components), for example, a dropdown selector.
[![Example of a dropdown page template property in Xperience by Kentico administration](docsassets/guides/map-enum-to-dropdown/dropdown-demo.gif)](https://docs.kentico.com/docsassets/guides/map-enum-to-dropdown/dropdown-demo.gif)
As a developer, you can populate an [Xperience dropdown selector](documentation/developers-and-admins/customization/extend-the-administration-interface/ui-form-components/reference-admin-ui-form-components#dropdown-selector) with data in two ways:
  1. Pass a “hardcoded” string of options, as you can see in [this example](https://github.com/Kentico/xperience-by-kentico-training-guides/blob/a58ae4215eaba8941ab98d797c1b8146875b8525/src/TrainingGuides.Web/Features/LandingPages/LandingPageTemplateProperties.cs#L24).
  2. Create a dropdown provider that maps options dynamically.


This guide covers the second approach. In our example, we use an **enumeration** to define the dropdown options.
Setting up dropdown mapping is an important building block for the later guides in this [Page Builder series](guides/development/page-builder/meet-requirements-with-page-builder), which use dropdown components heavily.
## Before you start
This guide requires the following:
  * Familiarity with [C#](https://learn.microsoft.com/en-us/dotnet/csharp/), [.NET Core](https://learn.microsoft.com/en-us/dotnet/), [Dependency injection](https://learn.microsoft.com/en-us/dotnet/core/extensions/dependency-injection), and the [MVC pattern](https://learn.microsoft.com/en-us/aspnet/core/mvc/overview).
  * A running instance of Xperience by Kentico, preferably [30.11.1](documentation/changelog) or higher. 
Some features covered in the training guides may not work in older versions. 
  * Basic knowledge of the [_Page Builder_](documentation/developers-and-admins/development/builders/page-builder) in Xperience by Kentico.


**Code samples**
You can find a project with completed, working versions of code samples from this guide and others in the [finished branch](https://github.com/Kentico/xperience-by-kentico-training-guides/tree/finished) of the _Training guides_ repository.
The [main branch](https://github.com/Kentico/xperience-by-kentico-training-guides) of the repository provides a starting point to code along with the guides.
The code samples in this guide are for [.NET 8](https://learn.microsoft.com/en-us/dotnet/core/whats-new/dotnet-8/overview) only.
They come from a project that uses [implicit using directives](https://learn.microsoft.com/en-us/dotnet/core/project-sdk/overview#implicit-using-directives). You may need to add additional `using` directives to your code if your project does not use this feature.
For a finished example implementation, take a look at the [_DropdownEnumOptionsProvider.cs_](https://github.com/Kentico/xperience-by-kentico-training-guides/blob/85feba75add184397fd29cc291e7f332abc75718/src/TrainingGuides.Web/Features/Shared/OptionsProviders/DropdownEnumOptionsProvider.cs) file. To see this provider in action, check out [_LandingPageTemplateProperties.cs_](https://github.com/Kentico/xperience-by-kentico-training-guides/blob/85feba75add184397fd29cc291e7f332abc75718/src/TrainingGuides.Web/Features/LandingPages/LandingPageTemplateProperties.cs) file in the same repo.
## Implement a generic mapper class
The [Xperience Dropdown selector](documentation/developers-and-admins/customization/extend-the-administration-interface/ui-form-components/reference-admin-ui-form-components#dropdown-selector) has a `DataProviderType` property that can take a class, mapping a custom enumeration to specific dropdown options. 
While it is viable to implement a custom class per enumeration, a reusable **generic** class will save you time and lines of code.
First, create a generic `DropdownEnumOptionProvider` class that:
  * Implements `IDropDownOptionsProvider`
  * Accepts `struct` and `Enum` as the generic type


If you are working in the Training guides repository, we recommend placing the class inside _~/Features/Shared/OptionProviders_ folder in the _TrainingGuides.Web_ project.
Then implement the required `GetOptionItems` method. This method tells Xperience how to map the enumeration members to dropdown items.
To work with enumerations, our samples use the [Enums.NET NuGet package](https://www.nuget.org/packages/Enums.NET/). The library allows you to access the enumeration attributes, like _Description_ , which is handy, for example, to store UI labels for each dropdown option.
Below is the complete code of a generic `DropdownEnumOptionProvider` class, for your reference:
C#
**DropdownEnumOptionProvider.cs**
Copy
```
using System.ComponentModel;
using EnumsNET;
using Kentico.Xperience.Admin.Base.FormAnnotations;

namespace TrainingGuides.Web.Features.Shared.OptionProviders;

public class DropdownEnumOptionProvider<T> : IDropDownOptionsProvider where T : struct, Enum
{
    public Task<IEnumerable<DropDownOptionItem>> GetOptionItems()
    {
        var results = Enums.GetMembers<T>(EnumMemberSelection.All)
            .Select(enumItem =>
            {
                string text = enumItem.Attributes.OfType<DescriptionAttribute>().FirstOrDefault()?.Description ?? enumItem.Name;
                string value = enumItem.Value.ToString();

                return new DropDownOptionItem { Value = value, Text = text };
            });

        return Task.FromResult(results.AsEnumerable());
    }
}
```

## Create a service to parse enumeration values from strings
With the provider in place, we need another element before we can start to use it.
Although we feed the dropdown list with options from the enumeration, the value from the dropdown selector control comes back as `string` by definition. As a result, we need a generic service to parse `enum` values from `string` objects when we want to work with them [later in the Page Builder series](guides/development/page-builder/create-versatile-templates-part-1#style-the-template-based-on-properties).
C#
**IEnumStringService.cs**
Copy
```
namespace TrainingGuides.Web.Features.Shared.OptionProviders;

public interface IEnumStringService
{
    T Parse<T>(string value, T defaultValue) where T : struct, Enum;
}
```

Use functionality from [Enums.NET](https://www.nuget.org/packages/Enums.NET/) to try parsing a value of the provided `enum` type, and return a default value if the parse operation fails.
C#
**EnumStringService.cs**
Copy
```
using EnumsNET;

namespace TrainingGuides.Web.Features.Shared.OptionProviders;

public class EnumStringService : IEnumStringService
{
    public T Parse<T>(string value, T defaultValue) where T : struct, Enum =>
        Enums.TryParse<T>(value, true, out var parsed)
            ? parsed
            : defaultValue;
}
```

## Use the new mapper class
Let’s demonstrate the enumeration mapping on an existing [dropdown selector property](https://github.com/Kentico/xperience-by-kentico-training-guides/blob/a58ae4215eaba8941ab98d797c1b8146875b8525/src/TrainingGuides.Web/Features/LandingPages/LandingPageTemplateProperties.cs#L24) of the Landing page template in the main branch of our Training guides repository.
If you are working on your own project, feel free to define a property and enumeration values that fit your use case. You will still be able to follow most of the steps below.
### Define your enumeration
Examine the [`LandingPageTemplateProperties.cs` file](https://github.com/Kentico/xperience-by-kentico-training-guides/blob/85feba75add184397fd29cc291e7f332abc75718/src/TrainingGuides.Web/Features/LandingPages/LandingPageTemplateProperties.cs) in `TrainingGuides.Web/Features/LandingPages`. The dropdown values are currently defined by a string constant: 
C#
**LandingPageTemplateProperties.cs - BEFORE MAPPING**
Copy
```
...
private const string OPTIONS =
    "H1;Heading 1" + "\n" +
    "H2;Heading 2" + "\n" +
    "H3;Heading 3" + "\n" +
    "H4;Heading 4" + "\n" +
    "P;Paragraph" + "\n";

[DropDownComponent(
    Label = "Message tag type",
    Options = OPTIONS,
    ExplanationText = DESCRIPTION)]
public string MessageType { get; set; } = "H4";
...
```

Define the `LandingPageHeadingTypeOption` enumeration accordingly. 
C#
Copy
```
using System.ComponentModel;
...

public enum LandingPageHeadingTypeOption
{
    [Description("Heading 1")]
    H1,
    [Description("Heading 2")]
    H2,
    [Description("Heading 3")]
    H3,
    [Description("Heading 4")]
    H4,
    [Description("Paragraph")]
    P
}
```

Each enumeration item should match one option in the dropdown component.
The _Description_ of each item is what editors will see as labels in the Xperience administration. If you don’t define a _Description_ attribute, the item’s name becomes the label by default.
### Map the enumeration to the dropdown component
Assign the type of `DropdownEnumOptionProvider<YOUR_ENUMERATION>` to the `DataProviderType` property of the `DropDownComponent`.
Next, remove the `Options` property, which is no longer needed.
See below for the complete new _LandingPageTemplateProperties.cs_ , with the `LandingPageHeadingTypeOption` enumeration mapped to the `MessageType` dropdown property.
C#
**LandingPageTemplateProperties.cs - AFTER MAPPING**
Copy
```
using System.ComponentModel;
using Kentico.PageBuilder.Web.Mvc.PageTemplates;
using Kentico.Xperience.Admin.Base.FormAnnotations;
using TrainingGuides.Web.Features.Shared.OptionProviders;

namespace TrainingGuides.Web.Features.LandingPages;

public class LandingPageTemplateProperties : IPageTemplateProperties
{
    private const string DESCRIPTION = "Select the type of Html tag that wraps the home page message";

    [DropDownComponent(
        Label = "Message tag type",
        ExplanationText = DESCRIPTION,
        DataProviderType = typeof(DropdownEnumOptionProvider<LandingPageHeadingTypeOption>)
    )]
    public string MessageType { get; set; } = nameof(LandingPageHeadingTypeOption.H2);
}

// enumeration defining the dropdown values and labels
public enum LandingPageHeadingTypeOption
{
    [Description("Heading 1")]
    H1,
    [Description("Heading 2")]
    H2,
    [Description("Heading 3")]
    H3,
    [Description("Heading 4")]
    H4,
    [Description("Paragraph")]
    P
}
```

If you followed along with the example above, run the _TrainingGuides.Web_ project. When you edit the **Home page** template in the Xperience administration, the **Message tag type** property should let you change the style of the _“Welcome to Training guides!”_ message – the same way as before the refactor.
Your browser does not support the video tag. 
## What’s next
You can now utilize the `DropdownEnumOptionProvider` class across your project, for dropdown selector properties of page templates, Page Builder sections, or widgets alike. As you’ll see later in this series, you can work with enumerations that are shared or reuse other enumeration values.
If you have been following along using the [the Training guides repository](https://github.com/Kentico/xperience-by-kentico-training-guides), take a look at the [Landing page view in the finished branch](https://github.com/Kentico/xperience-by-kentico-training-guides/blob/finished/src/TrainingGuides.Web/Features/LandingPages/LandingPagePageTemplate.cshtml). You’ll notice it utilizes a **tag helper** that works with the heading type enum, rather than the in-template logic you can see in your version of the same file. Try to look for opportunities to extract reusable parts in your own projects.
Note, that not all [Xperience UI form components](documentation/developers-and-admins/customization/extend-the-administration-interface/ui-form-components/reference-admin-ui-form-components) have the `DataProviderType` property and support the dynamic data source.
If you’d like to read more about how to approach working with [_Page Builder_](documentation/developers-and-admins/development/builders/page-builder) in Xperience by Kentico projects, [Meet business requirements with Page Builder](guides/development/page-builder/meet-requirements-with-page-builder) provides a great introductory explanation.
The [next guide of this series](guides/development/page-builder/create-versatile-templates-part-1) will walk you through the process of creating versatile page templates.