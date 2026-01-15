# Overview
  * [ Copy page link ](guides/development/developer-kickstart/xperience-by-kentico-overview#) | [Get HelpService ID](guides/development/developer-kickstart/xperience-by-kentico-overview#)
Core MVC 5


[✖](guides/development/developer-kickstart/xperience-by-kentico-overview# "Close page link panel") [Copy to clipboard](guides/development/developer-kickstart/xperience-by-kentico-overview#)
Welcome to a kickstart of the Xperience by Kentico development!
Throughout this series, we’ll create and configure a simple website fully managed by Xperience by Kentico.
Using the MVC design pattern, you’ll build logic that displays pages managed by Xperience and a simple navigation menu. You will learn how to work with your site content (e.q., articles, products, etc.) - modeling it with **content types** , storing it in **content items** , and making it **reusable**.
[![Demo of the Xperience by Kentico site](docsassets/guides/xperience-by-kentico-overview/demo.gif)](https://docs.kentico.com/docsassets/guides/xperience-by-kentico-overview/demo.gif)
We designed the steps to help you develop an Xperience website from scratch. You can find the complete implementation in [this repository](https://github.com/Kentico/xperience-by-kentico-kickstart).
## Requirements
To follow along with this series, we recommend your machine has the following installed:
  * [.NET 8.0](https://dotnet.microsoft.com/en-us/download/dotnet/8.0)
  * Microsoft SQL Server 2022 (or compatible version, such as the free [SQL Server Express Edition](https://www.microsoft.com/en-us/download/details.aspx?id=104781))
The **[collation](https://docs.microsoft.com/en-us/sql/relational-databases/collations/collation-and-unicode-support)** set for the SQL server **must be case-insensitive**. 
  * an **IDE** that supports ASP.NET Core development, such as [Visual Studio](https://visualstudio.microsoft.com/) or [Visual Studio Code](https://code.visualstudio.com/)


### Before you start
These materials assume you are familiar with the ASP.NET Core and C# development concepts, and have access to standard development tools.
We do not aim to teach you the general development principles but to introduce how website development works in Xperience.
Use these free resources to quickly get on board with the [ASP.NET Core MVC development](https://docs.microsoft.com/en-us/aspnet/core/tutorials/first-mvc-app/start-mvc?tabs=visual-studio) and [C# syntax nuances](https://learn.microsoft.com/en-us/dotnet/csharp/whats-new/csharp-12).
While we won’t fully leverage the MVC architectural pattern, you’ll find it beneficial to know the concepts behind the approach to understand how certain Xperience features work.
## Development model of Xperience by Kentico
Xperience projects are [ASP.NET](http://asp.net/) Core applications with two main components:
  * **Live site** displays content managed by Xperience to your visitors. It retrieves the content using the Xperience API, integrated into the application via [Xperience NuGet packages](documentation/developers-and-admins/development/website-development-basics/configure-new-projects/xperience-by-kentico-nuget-packages).
  * **Xperience administration** provides a user interface to edit and manage content, and to configure various system features.


The following diagram summarizes the general roles of the main personas that interact with an Xperience site – Xperience users, developers, and website visitors:
[![Main personas interacting with an Xperience site: Xperience users \(admins, marketers, content editors\) work with Xperience administration. Developers use Xperience administration to model content and custom functionality. They also implement business and presentation logic using the ASP.NET Core MVC application. Website visitors view and interact with the content presented by the ASP.NET Core MVC application.](docsassets/guides/xperience-by-kentico-overview/xbyk-basic-flow.png)](https://docs.kentico.com/docsassets/guides/xperience-by-kentico-overview/xbyk-basic-flow.png)
## Xperience administration
The administration interface allows editors to **manage their project’s content**.
The [**Content hub**](documentation/business-users/content-hub) application is where users manage reusable content items (structured content), which they can reference in various channels.
A **website channel** represents a site in the administration UI. Each website channel has its own application. Editors create pages and organize them in a content tree. They can add and modify content, use the [**Page Builder**](documentation/developers-and-admins/development/builders/page-builder) and workflow features, and preview changes before publishing their content live.
Your role as a developer is to **design the content structure** for editors to work with based on customer business requirements. You can also create custom Page Builder components and UI controls or add new administration interface functionality.
[![Xperience by Kentico administration](docsassets/guides/xperience-by-kentico-overview/xbyk-administration.png)](https://docs.kentico.com/docsassets/guides/xperience-by-kentico-overview/xbyk-administration.png)
All code samples you will see throughout the series come from our [Kickstart repository](https://github.com/Kentico/xperience-by-kentico-kickstart), where you can find the complete implementation of the simple website from this Kickstart series.
## Continue learning
When you’re ready, move on to the next page: [Set up an Xperience by Kentico project](guides/development/developer-kickstart/set-up-an-xperience-by-kentico-project)