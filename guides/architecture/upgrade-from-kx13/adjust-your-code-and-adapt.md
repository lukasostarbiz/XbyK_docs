# Adjust your code and adapt your project
  * Concept| [ Copy page link ](guides/architecture/upgrade-from-kx13/adjust-your-code-and-adapt#) | [Get HelpService ID](guides/architecture/upgrade-from-kx13/adjust-your-code-and-adapt#) | This page is part of a module: [ Upgrading to Xperience by Kentico - Overview ](modules/upgrade-overview)
Core MVC 5


[✖](guides/architecture/upgrade-from-kx13/adjust-your-code-and-adapt# "Close page link panel") [Copy to clipboard](guides/architecture/upgrade-from-kx13/adjust-your-code-and-adapt#)
While sites built on **Kentico Xperience 13 (KX13)** and **Xperience by Kentico (XbyK)** are all .NET applications, there are some differences in the code to consider when upgrading to XbyK, and considerations for where to go with a project once it is on the new platform.
This guide will provide more details about the points from the later top-level sections of the [upgrade overview](guides/architecture/upgrade-from-kx13/upgrade-from-kx13-overview).
## Adjust your code
### Update content querying code
Xperience by Kentico uses the [ContentRetriever API](documentation/developers-and-admins/api/content-item-api/content-retriever-api) and the [Content item query API](documentation/developers-and-admins/api/content-item-api/content-item-query-api) to query content, in place of [DocumentQuery and MultiDocumentQuery](13/custom-development/working-with-pages-in-the-api) from Kentico Xperience 13.
The documentation in the above link has more information about the specifics of how it works, but in broad strokes, here are the steps to retrieve content:
  * **ContentRetrieval API**
    * This is an approach we recommend.
    * Use an `IContentRetriever` object and its generic extension methods that fit your scenario, e.g., `RetrieveContentByGuids` or `RetrieveCurrentPage`. 
      * Specify the content type you want to retrieve for each method call - the implementation takes care of the data mapping for you.
      * You can pass additional information specifying your request through method parameters, like `additionalQueryConfiguration` or `cacheSettings`.
  * **Content item query API**
    * You can use this approach for advanced scenarios which the ContentRetrieval API doesn’t cover.
    * Use a `ContentItemQueryBuilder` object to construct a query. 
      * Apply one of the `ForContentType` or `ForContentTypes` extension methods to filter the query based on system aspects of the content type. For example, whether content items include a given reusable field schema, whether they _link to_ or _are linked from_ another item, and whether they are part of a given website channel.
      * Call the `Parameters` extension method to add standard query operations like _OrderBy_ and _Where_ conditions that test the values of the content type’s fields.
      * Call the `InLanguage` extension method to determine which language versions of content items Xperience should retrieve.
    * Use an `IContentQueryExecutor` to get a result from the query.


We recommend creating some kind of shared resource that you can use across your project to centralize content querying logic, such as a service.
The [ContentItemRetrieverService](https://github.com/Kentico/xperience-by-kentico-training-guides/blob/finished/src/TrainingGuides.Web/Features/Shared/Services/ContentItemRetrieverService.cs) from our Training guides repository illustrates both approaches, though you may want to create different methods to more closely mirror the content in your KX13 project.
There are some notable changes in related functionality as well:
  * `PageDataContext` and `IPageDataContextRetriever` are now `WebPageDataContext` and `IWebPageDataContextRetriever`. 
    * Unlike the `Page` property of `PageDataContext` in KX13, XbyK’s `WebPageDataContext` does not contain the full page data. It contains a `RoutedWebPage` object with information you can use to retrieve the correct page.
  * KX13’s `IPageUrlRetriever` corresponds to `IWebPageUrlRetriever`, which now works asynchronously.
  * The Kentico Migration Tool converts page attachments to asset content items. The assets are created as content items of a `Legacy.attachment` content type by the migration tool.


### Adjust Page Builder components
In Kentico Xperience 13, Page Builder components such as _widgets_ , _page templates_ , and _sections_ use C#-based editing components for their properties. These editing components are assigned to properties using attributes such as this one:
C#
**Kentico Xperience 13 editing component assignment**
Copy
```
// Assigns the 'TextInputComponent' as the editing component of the 'TextProperty' property
[EditingComponent(TextInputComponent.IDENTIFIER)]
public string TextProperty { get; set; }
```

Xperience by Kentico supports the same format for the sake of backward compatibility, but primarily uses a new format of [UI form components](developers-and-admins/customization/extend-the-administration-interface/ui-form-components/reference-admin-ui-form-components). These components are assigned using dedicated attributes, for example:
C#
**Xperience by Kentico editing component assignment**
Copy
```
// Assigns the 'TextInputComponent' as the editing component of the 'TextProperty' property
[TextInputComponent(Label = "Enter text")]
public string TextProperty { get; set; }
```

If you choose to enable [Source instance API discovery](https://github.com/Kentico/xperience-by-kentico-kentico-migration-tool/blob/master/Migration.Tool.CLI/README.md#source-instance-api-discovery) during data migration, which we recommend, you will need to change the attributes used by the properties of your Page Builder components to the new UI form components. Otherwise, your project will continue to use Page Builder in _Legacy mode_ , which won’t require these changes but lacks many benefits for the content editors.
### Rehome custom tables
Xperience by Kentico does not have a direct analog for the [Custom tables](13/developing-websites/defining-website-content-structure/managing-custom-tables) functionality in legacy Kentico products.
By default, the Kentico Migration Tool creates a [custom object type](documentation/developers-and-admins/customization/object-types) for each custom table, but leaves you in charge of defining their user interface.
If you choose to do so, each _custom table_ will correspond to an _object type_ or class, and each _custom table row_ will correspond to an _object_ of that type. You’ll need a UI application page to host one or more custom tables to define the following parts for each of these custom table types:
  * An alternative form that determines which form controls Xperience should render for each field of the object type
  * A [listing page](documentation/developers-and-admins/customization/extend-the-administration-interface/ui-pages/reference-ui-page-templates/listing-ui-page-template) that displays a list of the objects and includes delete functionality
  * An [_Edit section_ and an _Edit page_](documentation/developers-and-admins/customization/extend-the-administration-interface/ui-pages/reference-ui-page-templates/edit-ui-page-template) that allow editors to alter the values of individual objects
  * A [Create page](documentation/developers-and-admins/customization/extend-the-administration-interface/ui-pages/reference-ui-page-templates/edit-ui-page-template#use-edit-pages-to-create-new-objects) to add new objects


You can choose whether each type should have its own [UI application page](documentation/developers-and-admins/customization/extend-the-administration-interface/ui-pages/ui-application-pages) or if they should be located collectively beneath the same application page.
This may sound complicated, but these basic operations are fairly simple to implement, and you can find an example of building such a UI in our [training guides](guides/development/customizations-and-integrations/create-basic-module#build-the-module-ui). The CLI tools in [this repository](https://github.com/seangwright/xperience-community-dotnet-item-templates) can help to speed up the process.
We recommend this approach when the data in the table is more procedural than content-focused, or your client needs to edit the values through a dedicated admin UI.
When custom tables contain structured content, we recommend using [reusable content items](documentation/business-users/content-hub) instead.
You can use the `ConvertClassesToContentHub` configuration in the **appsettings.json** file of the _Migration.Tool.CLI_ project to [automatically convert your custom tables to reusable content types](https://github.com/Kentico/xperience-by-kentico-kentico-migration-tool/tree/master/Migration.Tool.CLI#convert-pages-or-custom-tables-to-content-hub). Then you will not need to build a dedicated UI for the existing types, or any similar types your client might want in the future.
Editors can use the [Content hub](documentation/business-users/content-hub) to manage the items. By selecting a content type in the Content hub’s filter, they can view the custom table’s data.
### Convert custom module pages
Xperience by Kentico’s admin UI no longer runs on web forms, so you’ll need to recreate any [custom module pages](13/custom-development/creating-custom-modules) as [UI pages](documentation/developers-and-admins/customization/extend-the-administration-interface/ui-pages) in XbyK.
Most custom modules revolve around performing basic operations on custom objects, so in many cases, the [previous heading’s](guides/architecture/upgrade-from-kx13/adjust-your-code-and-adapt#rehome-custom-tables) guidance about the types of pages you’ll need still applies.
Xperience by Kentico also supports custom page templates, if your scenario requires more advanced customization. You can find an example in the [admin boilerplate project](documentation/developers-and-admins/customization/extend-the-administration-interface/prepare-your-environment-for-admin-development#client-development-boilerplate).
You can find resources covering the process of creating custom modules with complex hierarchical structures in the [training guides](guides/development/customizations-and-integrations#custom-modules).
You can find general guidance and reference material in the [admin UI documentation](documentation/developers-and-admins/customization/extend-the-administration-interface).
### Alter auth and user management
Xperience by Kentico introduces a new [Member](documentation/business-users/members) object type to represent people who register and sign in to the live site. Your team must change [old registration and authentication code](13/managing-users/user-registration-and-authentication) to [work with the changes](documentation/developers-and-admins/development/registration-and-authentication).
The approaches are fairly similar, as both utilize [ASP.NET Identity](https://learn.microsoft.com/en-us/aspnet/identity/overview/getting-started/introduction-to-aspnet-identity), but syntax has changed. You’ll need to make some alterations to your controllers and initialization.
At this time, [multi-factor authentication](documentation/developers-and-admins/configuration/users/user-management#multi-factor-authentication) in Xperience by Kentico is supported only for users signing into the administration, not for members.
### Choose a search integration
Xperience by Kentico does not support search functionality natively, but it has three search integrations:
  * [Lucene](https://github.com/Kentico/xperience-by-kentico-lucene)
  * [Azure AI search](https://github.com/Kentico/xperience-by-kentico-azure-ai-search)
  * [Algolia](https://github.com/Kentico/xperience-by-kentico-algolia).


Each integration has its own specifics regarding how the code should look, so we recommend reviewing the **docs** folder of the one you choose for specific advice on how to structure its code.
### Address differences in localization
Xperience by Kentico uses [Languages](documentation/developers-and-admins/configuration/languages), where KX13 used Cultures.
In cases where you want to know the preferred language of current site visitor you can use `IPreferredLanguageRetriever`.
As mentioned earlier, you can use the `InLanguage` extension method for content item query to determine which variant of content items Xperience should retrieve.
Note that languages are global in XbyK, so if you have KX13 code to retrieve cultures associated with a given site, you’ll need to adjust it to account for this.
#### Localization of resource strings
Editors in Kentico Xperience 13 were able to [define custom resource strings](13/multilingual-websites/setting-up-a-multilingual-user-interface/working-with-resource-strings#adding-your-own-strings) in the administration interface. This functionality doesn’t exist natively in XbyK, and there are a few possible approaches to handle custom resource strings.
  * **Move them to a resource file** - If you move custom resource strings to a [resource file](guides/development/multilingual/display-your-website-content-in-multiple-languages#translate-other-application-texts), you can resolve them easily in your application. This, however, does not allow your editors to create new strings.
  * **Use the content hub** - If you want to allow editors to create strings, you can create a [reusable content type](guides/architecture/content-modeling/model-reusable-content/model-a-reusable-article) to represent a string. Editors will create language versions of this type in the Content hub. Then, in code, you can dynamically look up the appropriate translation via the [Content item API](documentation/developers-and-admins/api/content-item-api/content-item-query-api), for example, in your custom `IStringLocalizer` and `IHtmlLocalizer` [implementations](https://learn.microsoft.com/en-us/aspnet/core/fundamentals/localization/make-content-localizable).
  * **Use the _Community localization_ module** - If it is important to your client to have a separate localization module to manage their custom resource strings rather than working in the content hub, consider the [Xperience community localization](https://github.com/nittin-cz/xperience-community-localization) module by Nittin, which recreates the experience from KX13.
  * **Implement your own custom module** - If your project requires a specific way of handling resource strings, your team can create a custom implementation using the XbyK [custom module](guides/development/customizations-and-integrations/create-basic-module) that suits your client’s needs.


#### Localization of the admin UI
Since there are significant differences in the keys and values of resource strings between KX13 and XbyK, you will need to create new resource files for XbyK while [localizing the administration interface](documentation/developers-and-admins/customization/admin-ui-localization).
We recommend using AI tools to help speed up translation of the default UI string values.
### Migrate commerce
Xperience by Kentico now includes native support for digital commerce. The commerce capabilities have been completely rewritten, which means many components from KX13 do not map directly to XbyK capabilities. As a result, your existing implementations will require updates, and the level of effort will depend on the complexity of each project.
#### Key implementation requirements
To use XbyK commerce features, you first enable and configure the [commerce](documentation/developers-and-admins/digital-commerce-setup) module. From there, you’ll [set up the product catalog](documentation/developers-and-admins/digital-commerce-setup/model-product-catalog) and [define products](documentation/developers-and-admins/digital-commerce-setup/model-product-catalog#general-recommendations). Then, you’ll need to _upgrade the code that displays the product catalog_. Next, you’ll configure [order statuses](documentation/developers-and-admins/digital-commerce-setup/configure-order-statuses) and the built-in [notifications](documentation/developers-and-admins/digital-commerce-setup/configure-order-statuses#enable-notifications-for-order-statuses).
The new XbyK approach offers flexibility, but it also means you’ll need to budget for custom development. Beyond displaying the catalog and handling order-related entities, you must upgrade your existing code for:
  * [Checkout and shopping cart](documentation/developers-and-admins/digital-commerce-setup/checkout-process) flows.
  * [Shipping and payment](documentation/developers-and-admins/digital-commerce-setup/shipping-and-payment-methods) methods.
  * Any additional functionality that your KX13 project leverages, for example, _integrations_ with 3rd-party systems or _discounts and coupons_.


As we are extending commerce features in XbyK, follow the [product roadmap](https://roadmap.kentico.com/tabs/6-next-refreshes) to plan for upcoming functionality.
If you choose to migrate your commerce to [Shopify](https://www.shopify.com/) and use the [Shopify integration](https://github.com/Kentico/xperience-by-kentico-shopify), you won’t need your calculation and checkout code, as these things are handled directly in Shopify. From the Xperience side, you’ll need to add code to manage the contents of shopping carts and discount coupons.
### Handle marketing changes
XbyK and KX13 both feature online marketing, but key differences exist. Let’s take a look into some of them.
When you log custom activities from server-side code in Xperience by Kentico, use `ICustomActivityLogger` instead of `IActivityLogService`. Custom activities no longer need an _initializer_ class for the logger to work. Note that XbyK also allows you to log custom activities with _client-side code_.
Custom automation steps like those in Kentico Xperience 13 are not currently available, so you can archive their code for now.
Since A/B tests are not supported natively yet, you can also archive any code related to them, such as custom conversions. For updates on the feature, we recommend to follow [our roadmap](https://roadmap.kentico.com/tabs/4-planned).
Regarding marketing and consents, note that you should register custom cookies by configuring `CookieLevelOptions` during startup, rather than using `CookieHelper`. Additionally, set cookies in the visitor’s browser using `ICookieAccessor`.
## Think about the future
### Adapt your content model post data migration
In the previous guide we talked about the data migration as [an opportunity to re-think and adjust your content model](guides/architecture/upgrade-from-kx13/prep-for-upgrade-and-transfer-data#adapt-your-content-model) in order to best utilize new content modeling features of the XbyK solution.
Although this is an approach we recommend, you may have reasons to decide against remodeling during the data migration, or to make only partial adjustments to the content model. In this case, you can always take the [expand-contract approach](https://community.kentico.com/blog/safely-evolving-a-content-model-with-expand-and-contract) to future-proof your content model after the data migration, for example:
In order to convert flat KX13 pages that contain both web-specific fields and content fields into this new structure, you can follow these general steps:
  1. Create a new reusable content type that has all the same fields as your web page type.
  2. Add a _Content items_ field to the _Web page content type_ , referencing an item of the _reusable content type_.
  3. Create a one-off script that utilizes Xperience APIs to do the following: 
    1. Create a new item of the _reusable type_ , copying all the values of their matching fields.
    2. Reference the new reusable item from the web page item’s _Content items_ field.
  4. Delete all the web page-specific fields from the _reusable content type_.
  5. Delete all the content-specific fields from the _web page content type_.


### Utilize new capabilities
#### Reusable field schemas
The migration tool’s configuration and customization options allow you to create [reusable field schemas](documentation/developers-and-admins/development/content-types/reusable-field-schemas) out of your existing content types. If you choose to avoid this conversion during data migration, similarly to the [above approach](guides/architecture/upgrade-from-kx13/adjust-your-code-and-adapt#adapt-your-content-model-post-data-migration) for reusable content items, you can create one-off scripts later on to help convert common fields that exist in several content types into [reusable field schemas](documentation/developers-and-admins/development/content-types/reusable-field-schemas).
Just add the schema to all the child content types, write a one-off script to copy content from the old fields to the schema fields, and then delete the old fields.
#### Taxonomies
If you want to take advantage of the new [taxonomies](documentation/developers-and-admins/configuration/taxonomies) feature, you’ll have to consider how to hierarchically structure the tags to best fit your content model. Then, you can filter content queries based on taxonomies, as in [this example](guides/development/advanced-content/filter-content-based-on-taxonomies).
#### Smart folders
If your editors utilize [smart folders](documentation/business-users/content-hub/content-hub-folders#smart-folders), you can incorporate their contents into the live site and [filter content item queries](documentation/developers-and-admins/development/content-retrieval/retrieve-content-items#retrieve-content-items-from-smart-folders).
## What’s next?
We hope you feel a little more prepared for the upgrade to Xperience by Kentico after this series of guides.
Let us know if you come across anything that needs additional explanation over the course of your upgrade, and we will do our best to cover it in future materials.
In the meantime, you can find more information on the process in [the documentation](documentation/developers-and-admins/upgrade-to-xperience-by-kentico) and the [Xperience by Kentico: Kentico Migration Tool](https://github.com/Kentico/xperience-by-kentico-kentico-migration-tool/tree/master) repository. For additional context, check out [this blog](https://dev.to/truelime/tips-for-migrating-from-kx13-to-xperience-by-kentico-709) by Jeroen Fürst of TrueLime, who put together useful tips and key things to consider before the KX13 to XbyK upgrade.
If you are a hands-on learner, check out our [upgrade walkthrough](guides/architecture/upgrade-from-kx13/upgrade-walkthrough) guide, showcasing an upgrade of the Dancing Goat sample site from KX13 to XbyK step-by-step.