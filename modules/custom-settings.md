# Introduction
  * [ Copy page link ](modules/custom-settings#) | [Get HelpService ID](modules/custom-settings#)
Core MVC 5


[✖](modules/custom-settings# "Close page link panel") [Copy to clipboard](modules/custom-settings#)
A custom module is a common requirement in digital experience projects. It allows developers to empower certain users to configure the site in new ways that are not available out-of-the-box.
This is the start of a series that shows how to create a basic custom settings module. To start with, the module will hold custom settings keys and values. Administrators and editors with sufficient permissions in the UI will be able to create and adjust the values of these settings, which you can access from your code.
Your browser does not support the video tag. 
The custom settings we’ll create initially will be **global** and not associated with any specific channel. We will create custom **channel-specific settings** later in the series.
Some of the materials here are not exclusive, and may look familiar if you’ve already completed other modules.
## Before you start
This series requires the following:
  * Familiarity with [C#](https://learn.microsoft.com/en-us/dotnet/csharp/), [.NET Core](https://learn.microsoft.com/en-us/dotnet/), [Dependency injection](https://learn.microsoft.com/en-us/dotnet/core/extensions/dependency-injection), and the [MVC pattern](https://learn.microsoft.com/en-us/aspnet/core/mvc/overview).
  * A running instance of Xperience by Kentico, preferably [30.11.1](documentation/changelog) or higher. 
Some features covered in the training guides may not work in older versions. 


**Code samples**
You can find a project with completed, working versions of code samples from this series and others in the [finished branch](https://github.com/Kentico/xperience-by-kentico-training-guides/tree/finished) of the _Training guides_ repository.
The [main branch](https://github.com/Kentico/xperience-by-kentico-training-guides) of the repository provides a starting point to code along with our guides and learning modules.
The code samples in this series are for [.NET 8](https://learn.microsoft.com/en-us/dotnet/core/whats-new/dotnet-8/overview) only.
They come from a project that uses [implicit using directives](https://learn.microsoft.com/en-us/dotnet/core/project-sdk/overview#implicit-using-directives). You may need to add additional `using` directives to your code if your project does not use this feature.
Previous page
1 of 9
[ Mark complete and continue ](modules/custom-settings/define-module-in-xperience-ui)
  * [Community Questions & Answers](https://community.kentico.com/q-and-a)
  * [Contact support](https://community.kentico.com/support)


### Cookie consent
We use necessary [cookies](https://www.kentico.com/cookies-policy) to run our website and improve your experience while browsing. Additional cookies are only used with your consent. You may revoke your consent on the [Cookies Policy](https://www.kentico.com/cookies-policy) page or in your browser at any time. 
ACCEPT ALL  [Configure](https://www.kentico.com/cookies-policy)
USE ONLY NECESSARY 
![](https://docs.kentico.com/modules/custom-settings)
[](https://docs.kentico.com/modules/custom-settings)[](https://docs.kentico.com/modules/custom-settings)