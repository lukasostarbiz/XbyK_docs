# Access custom global configurations
  * How-to| [ Copy page link ](guides/development/customizations-and-integrations/access-custom-global-configurations#) | [Get HelpService ID](guides/development/customizations-and-integrations/access-custom-global-configurations#) | This page is part of a module: [ Custom modules: custom settings ](modules/custom-settings)
Core MVC 5


[✖](guides/development/customizations-and-integrations/access-custom-global-configurations# "Close page link panel") [Copy to clipboard](guides/development/customizations-and-integrations/access-custom-global-configurations#)
[Earlier in this series](guides/development/customizations-and-integrations/create-basic-module), you learned how to create a custom module that holds custom channel-agnostic key-value-based settings.
Let’s say your customer wants to use the new module to define two custom email notification settings: **Email notification recipients** and **Email notification sender**.
Your browser does not support the video tag. 
Every time Xperience sends a certain type of email notification, these two settings should determine who the recipients and the sender are.
**How do you access the values in your code to meet the customer’s requirements?**
Using the **email notification settings** described above as an example, let’s explore how you can access custom module values and how to utilize the [Options pattern](https://learn.microsoft.com/en-us/aspnet/core/fundamentals/configuration/options?view=aspnetcore-8.0) to work with custom settings defined in the Xperience in a strongly typed manner.
## Before you start
This guide requires the following:
  * Familiarity with [C#](https://learn.microsoft.com/en-us/dotnet/csharp/), [.NET Core](https://learn.microsoft.com/en-us/dotnet/), [Dependency injection](https://learn.microsoft.com/en-us/dotnet/core/extensions/dependency-injection), and the [MVC pattern](https://learn.microsoft.com/en-us/aspnet/core/mvc/overview).
  * A running instance of Xperience by Kentico, preferably [30.11.1](documentation/changelog) or higher. 
Some features covered in the training guides may not work in older versions. 


The examples in this guide require that you:
  * Have followed along with the samples from [the previous guide](guides/development/customizations-and-integrations/create-basic-module) that implement a custom module holding global settings.


**Code samples**
You can find a project with completed, working versions of code samples from this guide and others in the [finished branch](https://github.com/Kentico/xperience-by-kentico-training-guides/tree/finished) of the _Training guides_ repository.
The [main branch](https://github.com/Kentico/xperience-by-kentico-training-guides) of the repository provides a starting point to code along with the guides.
The code samples in this guide are for [.NET 8](https://learn.microsoft.com/en-us/dotnet/core/whats-new/dotnet-8/overview) only.
They come from a project that uses [implicit using directives](https://learn.microsoft.com/en-us/dotnet/core/project-sdk/overview#implicit-using-directives). You may need to add additional `using` directives to your code if your project does not use this feature.
## Enter the data
To follow along with the example here, use the UI developed earlier in this series to create two email notification settings, as seen in [this video](guides/development/customizations-and-integrations/access-custom-global-configurations#EmailSettingsDemo).
You can copy-paste these values:
  * **Display name:** Email notification recipients
  * **Settings key code name:** EmailNotificationToAddresses
  * **Value:** :admin@localhost.local;support@localhost.local
  * **Notes:** The email addresses that should receive custom email notifications. Separate addresses with a semi-colon.