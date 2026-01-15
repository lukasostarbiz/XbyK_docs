# Understand how Xperience stores digital content
  * [ Copy page link ](modules/content-modeling-guide/understand-storing-data-in-content-type#) | [Get HelpService ID](modules/content-modeling-guide/understand-storing-data-in-content-type#)
Core MVC 5


[✖](modules/content-modeling-guide/understand-storing-data-in-content-type# "Close page link panel") [Copy to clipboard](modules/content-modeling-guide/understand-storing-data-in-content-type#)
In Xperience, all content is based on **content types**. You already know that a [content type](documentation/developers-and-admins/development/content-types) is a _blueprint_ that defines _data structure, functionality, and behavior_ for all content items editors create from this type. Based on this definition, reusing content requires storing it in a presentation-independent manner, i.e., in a structured format.
Xperience uses different content types depending on the purpose. Each has its own storage options and ways of connecting to other stored content.
  * **Reusable content type** – Stores data in the [Content hub](documentation/business-users/content-hub). This content isn’t tied to any specific channel. Editors can reuse it across multiple channels, such as websites, emails, or headless applications.
  * **Webpage content type** – Used in the [website channel](documentation/business-users/website-content). _Web pages_ can store data directly or act as a container displaying reusable content from the _Content hub_ (or a combination of these approaches).
  * **Email content type** – Used in the [email channel](documentation/developers-and-admins/digital-marketing-setup/email-channel-management). Emails can store data directly or display data from _webpages_ or _reusable items_ stored in the _Content hub_.
  * **Headless content type** – Used in the [headless channel](documentation/developers-and-admins/configuration/headless-channel-management). Headless items can store data directly in the headless channel or display _reusable content_ from the _Content hub_.


We’ll discuss each of the options further in this and other dedicated materials. The following diagram overviews the storage options in Xperience.
[![Levels of abstraction in data storage and data presentation](docsassets/guides/store-content/cm-guide-store-content-objects.png)](https://docs.kentico.com/docsassets/guides/store-content/cm-guide-store-content-objects.png)
### Content types from the data perspective
[Content types](documentation/developers-and-admins/development/content-types) that store data in a _structured format_ allow editors to create items in a form-like interface where they input data. The editing forms create a unified editing experience, and adding content in the _Content hub_ feels like filling out an online form. From a [data perspective](documentation/developers-and-admins/api/content-item-api/content-item-database-structure), these fields correspond to database columns. Developers define content types in the **Content types** application.
When developers create content types, they configure:
  * _Fields_ that define the data structure of the content type.
  * _Editing form layout_ that creates the interface editors use to create and modify the content items.
  * _Usage_ of the content type: 
    * Reusable content types are building blocks of reusable content meant to be linked together to form further content.
    * Content types for pages are used in website channels to form pages.
    * Content types for email are used in email channels to form emails.


Developers use the **Content types** application to define the [data model](modules/content-modeling-guide/structured-and-unstructured-content) of the Xperience application. They create _content types_ based on the project’s business requirements using an easy-to-use admin UI consisting of form fields or ready-made form components.
[![Managing content type fields](docsassets/guides/store-content/cm-guide-content-types-application-ui.png)](https://docs.kentico.com/docsassets/guides/store-content/cm-guide-content-types-application-ui.png)
The _content type_ is stored in a coupled database table; its fields represent database columns. For example, Kbank’s _News_ content type can have a coupled table named _Kbank_News_ and columns defined as _NewsTitle_ , _NewsSummary_ , or _NewsText_.
[ Previous page ](modules/content-modeling-guide/store-all-content-in-xperience)
8 of 28
[ Mark complete and continue ](modules/content-modeling-guide/understand-different-storing-options)
  * [Community Questions & Answers](https://community.kentico.com/q-and-a)
  * [Contact support](https://community.kentico.com/support)


### Cookie consent
We use necessary [cookies](https://www.kentico.com/cookies-policy) to run our website and improve your experience while browsing. Additional cookies are only used with your consent. You may revoke your consent on the [Cookies Policy](https://www.kentico.com/cookies-policy) page or in your browser at any time. 
ACCEPT ALL  [Configure](https://www.kentico.com/cookies-policy)
USE ONLY NECESSARY 
![](https://docs.kentico.com/modules/content-modeling-guide/understand-storing-data-in-content-type)
[](https://docs.kentico.com/modules/content-modeling-guide/understand-storing-data-in-content-type)[](https://docs.kentico.com/modules/content-modeling-guide/understand-storing-data-in-content-type)