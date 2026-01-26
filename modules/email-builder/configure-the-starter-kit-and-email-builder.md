---
source: https://docs.kentico.com/modules/email-builder/configure-the-starter-kit-and-email-builder
scrape_date: 2026-01-26
---

Module: Email Builder
4 of 13 Pages
# Configure the Starter Kit and Email Builder
### Register the MJML Starter Kit template
By default, the Starter Kit does not register its included page template. This allows you to specify which of your content types should have access to it, or easily leave it out.
Create a new file in the _~/Features/Shared/EmailBuilder_ folder of the _TrainingGuides.Web_ project called **StarterKitComponentRegister.cs**
C#
**StarterKitComponentRegister.cs**
Copy
```
using Kentico.EmailBuilder.Web.Mvc;
using Kentico.Xperience.Mjml.StarterKit.Rcl.Templates;
using TrainingGuides;

[assembly: RegisterEmailTemplate(
    identifier: EmailBuilderStarterKitTemplate.IDENTIFIER,
    name: "MJML Starter Kit template",
    componentType: typeof(EmailBuilderStarterKitTemplate),
    // Enter the code names of all email content types where you wish to use Email Builder
    ContentTypeNames = [BasicEmail.CONTENT_TYPE_NAME])]
```

The _Training guides_ project includes other email content types, but for this example, we’ll only enable the `BasicEmail` type.
In your own projects, you can include as many content types as you want in the `ContentTypeNames` collection.
### Enable Email Builder
If you run your project now, you’ll notice that you can’t use the **MJML Starter Kit template** for emails, even though you just registered it. This is because you haven’t yet enabled Email Builder.
During startup, we need to enable Email Builder and configure its options. We must specify which content types should use Email Builder at startup. You can also override the default section used in _Editable areas_ on email templates.
Let’s enable Email marketing and Email Builder for the **Basic email** content type and set the default section to the **Full-width section** from the Starter Kit.
C#
**Program.cs**
Copy
```
...
using Kentico.EmailBuilder.Web.Mvc;
using Kentico.Xperience.Mjml;
...

builder.Services.AddKentico(async features =>
{
    ...
    features.UseEmailMarketing();
    features.UseEmailBuilder();
});
...
builder.Services.Configure<EmailBuilderOptions>(options =>
{
    options.AllowedEmailContentTypeNames = [BasicEmail.CONTENT_TYPE_NAME];
    // Replaces the default Email Builder section
    options.RegisterDefaultSection = false;
    options.DefaultSectionIdentifier = FullWidthEmailSection.IDENTIFIER;
});

builder.Services.AddMjmlForEmails();
...
```

### Configure the Starter Kit
Similar to the Email Builder configuration, you also need to configure the Starter Kit when the application starts.
The available options allow you to define the path to a stylesheet that Email Builder will use, and the allowed content types to restrict the selections of the _Image_ and _Product_ widgets.
For our example, let’s use styles from the Starter Kit, and restrict the widget selectors to the content types we [already mapped](/modules/email-builder/prepare-your-project-for-the-starter-kit) to the widget models.
#### Copy the stylesheet
Since email CSS is beyond the scope of this example, let’s just copy the Dancing Goat stylesheet for Email Builder from the Starter Kit repository.
In the Starter Kit repo, find the [**EmailBuilder.css**](https://github.com/Kentico/xperience-by-kentico-email-builder-starter-kit/blob/main/examples/DancingGoat/wwwroot/EmailBuilder.css) file under the _examples/DancingGoat/wwwroot_ folder and copy it to the _wwwroot_ folder of your _TrainingGuides.Web_ project.
Now, you can reference this file in the Starter Kit configuration.
#### Configure the Starter Kit
Returning to the _Program.cs_ file, use the `AddKenticoMjmlStarterKit` method to configure the Starter Kit’s options. You need to tell it where to find your Email Builder CSS and which content types the Image and Product selectors can use.
C#
**Program.cs**
Copy
```
...
using Kentico.Xperience.Mjml.StarterKit.Rcl;
using Kentico.Xperience.Mjml.StarterKit.Rcl.Sections;
...
builder.Services.AddKenticoMjmlStarterKit(options =>
{
    options.StyleSheetPath = "EmailBuilder.css";
    // The Asset content type, which we mapped to the Image widget earlier
    options.AllowedImageContentTypes = [Asset.CONTENT_TYPE_NAME];
    // The Product page content type, which we mapped to the Product widget earlier.
    options.AllowedProductContentTypes = [ProductPage.CONTENT_TYPE_NAME];
});
...
```

If you prefer to configure these values in your **appsettings.json** file, you can call `builder.Services.AddKenticoMjmlStarterKit(builder.Configuration);` instead.
Then you can set the options under the `MjmlStarterKitOptions` object in _appsettings.json_.
## See the results
The Starter Kit is now integrated into your project.
If you’ve been following along with the _Training guides_ example, you can see the results by creating a new email in the _Training guides emails_ channel.
Your browser does not support the video tag. 
## Explore further
To see more examples of Email Builder components in action, we recommend checking out those used on the [Community Portal](https://github.com/Kentico/community-portal/tree/v30.5.1.6/src/Kentico.Community.Portal.Web/Components/EmailBuilder).
Here, you can see the code that makes up [community.kentico.com](https://community.kentico.com/), our site for the Xperience by Kentico community, which features many helpful resources.
[ Previous page ](/modules/email-builder/prepare-your-project-for-the-starter-kit)
4 of 13
[ Mark complete and continue ](/modules/email-builder/set-up-widget-properties)
![]()
[]()[]()
