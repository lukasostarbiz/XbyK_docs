---
source: https://docs.kentico.com/modules/email-builder/design-structured-data-template
scrape_date: 2026-01-26
---

Module: Email Builder
8 of 13 Pages
# Design a template for structured data
Email Builder in Xperience by Kentico empowers marketers to control the visual design of emails with a flexible drag-and-drop interface.
However, some scenarios might call for less flexibility. For example: - You may want to consistently display structured content, such as product details, event information, or other reusable content items. - Your organization might have business requirements that set strict styling rules for certain emails. - Your marketers may not have the time to invest in planning and designing each issue of a recurring newsletter.
Unlike Xperience’s original implementation of [email templates](/documentation/developers-and-admins/digital-marketing-setup/email-templates), which only exist in the database, Email Builder allows developers to manage templates and other components in the application, where they can edit code without having to run the site and sign in.
Let’s explore the use of structured content in Email Builder, displaying this data with a rigid template that still includes some editable areas for drag and drop.
## Explore the scenario
In this scenario, we will create a template for a **Nature spotlight** email newsletter: a regular publication that describes interesting species of organisms, geographical features, and other natural phenomena.
The Training guides project already includes a content type for this newsletter, called [NatureSpotlightEmail](https://github.com/Kentico/xperience-by-kentico-training-guides/blob/finished/src/TrainingGuides.Entities/EmailContentTypes/NatureSpotlightEmail/NatureSpotlightEmail.generated.cs). Aside from the standard _Subject_ and _Preview text_ fields, it has fields representing the following:
  * The name of the spotlight’s topic
  * The main text of the newsletter issue
  * One or more images of the topic
  * One or more countries where the topic lives or occurs
  * One or more related articles from the website


Compared to the [previous scenario](/guides/development/email-marketing/create-an-email-widget-for-articles#implement-the-mapper), where we built a widget for displaying Articles, this example is more closely tied to a specific content type.
For this reason, we won’t use a mapper class to abstract the template from the content type and make it portable. However, if a mapper fits your requirements, it is still possible to implement in this scenario. Feel free to employ the lessons you learned in the earlier guide
## Enable Email Builder for the Nature spotlight newsletter
The [main branch](https://github.com/Kentico/xperience-by-kentico-training-guides) of the Training guides repository contains the `NatureSpotlightEmail`. However, it is not enabled for Email Builder yet.
[Earlier in this series](/modules/email-builder/configure-the-starter-kit-and-email-builder#enable-email-builder), you enabled Email Builder for the `BasicEmail` content type.
Navigate to **Program.cs** and add the `NatureSpotlightEmail` content type identifier to the configured `EmailBuilderOptions`.
C#
**~/Program.cs**
Copy
```
...
builder.Services.Configure<EmailBuilderOptions>(options =>
{
    // Allows Email Builder for the provided content types
    options.AllowedEmailContentTypeNames = [
        BasicEmail.CONTENT_TYPE_NAME,
        NatureSpotlightEmail.CONTENT_TYPE_NAME
    ];
    // Replaces the default Email Builder section
    options.RegisterDefaultSection = false;
    options.DefaultSectionIdentifier = FullWidthEmailSection.IDENTIFIER;
});
...
 
 
 
 


```

## Build a razor component for the template
Xperience uses [Razor components](https://learn.microsoft.com/en-us/aspnet/core/blazor/components/) for Email Builder templates.
We’ll use separate files to display the template and handle its code-behind in this example, though you can optionally use the same file depending on your project’s conventions.
The _single-file approach_ uses a **.razor** file with razor syntax and a `@code { ... }` block for its C# code.
The _two-file approach_ uses a **.razor** file with razor syntax for display, and a **.razor.cs** file for the code-behind file.
Aside from the files that directly make up the component, we’ll also include a **model** to hold the data we want the component to display, and a **service** that its code-behind will use to fetch data.
### Define a model for the Razor component’s data
Let’s start with a model to hold all of the data our template will display.
In the _TrainingGuides.Web_ project, create the folder **~/Features/Newsletters/NatureSpotlight**. We’ll primarily work in this folder throughout this example.
Define a model for the newsletter in a file called **NatureSpotlightEmailModel.cs** :
C#
**NatureSpotlightEmailModel.cs**
Copy
```
using Kentico.Xperience.Mjml.StarterKit.Rcl.Widgets;
using Microsoft.AspNetCore.Components;
using TrainingGuides.Web.Features.Articles.EmailWidgets;

namespace TrainingGuides.Web.Features.Newsletters.NatureSpotlight;

public class NatureSpotlightEmailModel
{
    public string Subject { get; set; } = string.Empty;

    public string PreviewText { get; set; } = string.Empty;

    public IEnumerable<string> Countries { get; set; } = [];

    public string Topic { get; set; } = string.Empty;

    public MarkupString Text { get; set; } = new MarkupString();

    public IEnumerable<ImageWidgetModel> Images { get; set; } = [];

    public IEnumerable<ArticleEmailWidgetModel> RelatedArticles { get; set; } = [];
}
```

This file uses the `ImageWidgetModel` from the Email Builder Starter Kit and the `ArticleWidgetModel` from the example earlier in this series. If you’re starting here, you can find these models in our repository:
  * [ImageWidgetModel](https://github.com/Kentico/xperience-by-kentico-email-builder-starter-kit/blob/main/src/Kentico.Xperience.Mjml.StarterKit.Rcl/Widgets/ImageWidget/ImageWidgetModel.cs)
  * [ArticleEmailWidgetModel](https://github.com/Kentico/xperience-by-kentico-training-guides/blob/finished/src/TrainingGuides.Web/Features/Articles/EmailWidgets/ArticleEmailWidgetModel.cs)


### Display the component’s data
Add a new file called **NatureSpotlightEmailTemplate.razor**.
We haven’t defined its code-behind yet, but we’ll plan for it to have a `NatureSpotlightEmailModel` property called `Model`, and a `string` property called `CssContent` to mirror the [template](https://github.com/Kentico/xperience-by-kentico-email-builder-starter-kit/blob/main/src/Kentico.Xperience.Mjml.StarterKit.Rcl/Templates/EmailBuilderStarterKitTemplate.razor.cs) from the Email Builder Starter Kit).
In this example, let’s include some [editable areas](/documentation/developers-and-admins/development/builders/email-builder/develop-email-builder-components#editable-areas), so that marketers have the option to add extra notices, notes, and other content within the pre-determined structure.
You can use Email Builder templates that do not have editable areas if you want a fully locked-down template that is part of your project’s source code.
cshtml
**NatureSpotlightEmailTemplate.razor**
Copy
```
@using Kentico.VisualBuilderComponents.Rcl.Components
<mjml>
    <mj-head>
        <mj-title>@Model.Subject</mj-title>
        <mj-style>
            @((MarkupString)CssContent)
        </mj-style>
        <EmailBuilderStyles />
        <mj-preview>@Model.PreviewText</mj-preview>
    </mj-head>
    <mj-body>
        <mj-section>
            <mj-text>
                <h1>Nature spotlight</h1>
            </mj-text>
            @foreach(var image in Model.Images)
            {
                <mj-image src="@image.ImageUrl" alt="@image.AltText" />
            }
        </mj-section>
        <EditableArea AreaIdentifier="MainContent" />
        <mj-section>
            <mj-text>
                <h2>@Model.Topic</h2>
                <p>
                    @foreach(string country in Model.Countries)
                    {
                        <em>
                            @(country == Model.Countries.Last() ? country : $"{country}, ")
                        </em>
                    }
                </p>
                <br/>
                @Model.Text

                <hr/>
                <h3>Related articles:</h3>
            </mj-text>
            @foreach(var article in Model.RelatedArticles)
            {
                <mj-image src="@article.ArticleTeaserImage.ImageUrl" alt="@article.ArticleTeaserImage.AltText" width="300px" />
                <mj-text>
                    <h4>@article.ArticleTitle</h4>
                    <p>
                        <em>@article.ArticleSummary</em>
                    </p>
                </mj-text>
                <mj-button href="@article.ArticleUrl">
                    Read More
                </mj-button>
            }
        </mj-section>
        <EditableArea AreaIdentifier="SecondaryContent" />
        <mj-text align="center">
            <a href="@Recipient.UnsubscriptionUrl" data-click-tracking="false">Unsubscribe</a>
        </mj-text>
        <EmailBuilderScripts />
    </mj-body>
</mjml>
```

You might notice that this uses the `mj-section` component to wrap some content, but does not wrap `EditableArea` components in the same way.
We avoided wrapping the editable area in this example because the Email Builder sections in this project use `mj-section` around their widget zones, and MJML does not support nesting `mj-section` components within one another.
This approach is not a requirement, but you should make sure the approach you decide on is consistent across all of the Email Builder components of each type within your project.
[ Previous page ](/modules/email-builder/build-widget-component)
8 of 13
[ Mark complete and continue ](/modules/email-builder/implement-structured-data-template)
![]()
[]()[]()
