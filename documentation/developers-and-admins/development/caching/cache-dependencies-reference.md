---
source: https://docs.kentico.com/documentation/developers-and-admins/development/caching/cache-dependencies-reference
scrape_date: 2026-01-26
---

  * [Home](/documentation)
  * [Developers and admins](/documentation/developers-and-admins)
  * [Development](/documentation/developers-and-admins/development)
  * [Caching](/documentation/developers-and-admins/development/caching)
  * [Cache dependencies](/documentation/developers-and-admins/development/caching/cache-dependencies)
  * Reference - Cache dependency keys 


# Reference - Cache dependency keys
This page list dummy cache keys and their format for all supported objects in the system.
Avoid setting dependencies on all objects of a particular type via `|all`. Clearing the cache every time any object of a given type changes has a high chance of significantly reducing the effectivness of your caching solution. Always prefer targeting specific objects either via code name or GUID.
## Reusable content items
The following cache keys are touched when the **Published** version of the target reusable [content item](/documentation/business-users/content-hub) changes.
If you need to set cache dependencies for other workflow states, use the `contentitem|allstates|*` prefix. Where `*` can be substituted for any expression from the table that begins with `contentitem`. For example:
  * `contentitem|allstates|all`
  * `contentitem|allstates|byschema|MySchema`


Dependency on |  Cache key format |  Description  
---|---|---  
All items |  contentitem|all **Example:** `contentitem|all` |  Targets all items in the system.  
ID |  contentitem|byid|<ID> **Example:** `contentitem|byid|53` |  Targets an item by its database ID and optionally language variant. `languageName` must match the code name of a language registered in the **Languages** application.  
ID and language variant |  contentitem|byid|<ID>|<languageName> **Example:** `contentitem|byid|53|czech`  
Code name |  contentitem|byname|<codeName> **Example:** `contentitem|byname|TheArtOfCoffeeCupping-pzodajb5` |  Targets an item by its code name and optionally language variant. Code names of content items can be found on the **Properties** tab when editing items in the **Content hub** application. `languageName` must match the code name of a language registered in the **Languages** application.  
Code name and language variant |  contentitem|byname|<codeName>|<languageName> **Example:** `contentitem|byname|TheArtOfCoffeeCupping-pzodajb5|czech`  
GUID |  contentitem|byguid|<GUID> **Example:** `contentitem|byguid|affc44f3-...` |  Targets an item by its GUID and optionally language variant. `languageName` must match the code name of a language registered in the **Languages** application.  
GUID and language variant |  contentitem|<GUID>|<languageName> **Example:** `contentitem|byguid|affc44f3-...|czech`  
Content type |  contentitem|bycontenttype|<contentType> **Example:** `contentitem|bycontenttype|Medio.Clinic` |  Targets all items of the given content type and optionally language variant. `contentType` must match the full code name (namespace and name) of the targeted content type. `languageName` must match the code name of a language registered in the **Languages** application.  
Content type and language variant |  contentitem|bycontenttype|<contentType>|<languageName> **Example:** `contentitem|bycontenttype|Medio.Clinic|czech`  
[Reusable field schema](/documentation/developers-and-admins/development/content-types/reusable-field-schemas) name |  contentitem|byschema|{schemaname} **Example:** `contentitem|byschema|MySchema` |  Targets all items using the given schema name and optionally language variant. `schemaname` must match the name of the targeted schema. `languageName` must match the code name of a language registered in the **Languages** application.  
Reusable field schema name and item language variant |  contentitem|byschema|{schemaname}|{language} **Example:** `contentitem|byschema|MySchema|czech`  
## Pages
The following cache keys are touched when the **Published** version of the target [page](/documentation/business-users/website-content) changes.
If you need to set cache dependencies for other workflow states, use the `webpageitem|allstates|*` prefix. Where `*` can be substituted for any expression from the table, omitting the `webpageitem` prefix. For example: `webpageitem|allstates|all`.
Dependency on |  Cache key format |  Description  
---|---|---  
All |  webpageitem|all **Example:** `webpageitem|all` |  Targets all pages in the system.  
All in website channel |  webpageitem|bychannel|<channelName>|all **Example:** `webpageitem|bychannel|MyChannel|all` |  Targets all pages under the specified website channel.  
ID |  webpageitem|byid|<ID> **Example:** `webpageitem|byid|53` |  Targets a page by it’s database ID and optionally language variant. `languageName` must match the code name of a language registered in the **Languages** application.  
ID and language variant |  webpageitem|byid|<ID>|<languageName> **Example:** `webpageitem|byid|53|czech`  
Code name |  webpageitem|byname|<codeName> **Example:** `webpageitem|byname|TheArtOfCoffeeCupping-pzodajb5` |  Targets a page by its code name and optionally language variant. The code name of a page can be found on the **Properties** tab when viewing pages under website channels. `languageName` must match the code name of a language registered in the **Languages** application.  
Code name and language variant |  webpageitem|byname|<codeName>|<languageName> **Example:** `webpageitem|byname|TheArtOfCoffeeCupping-pzodajb5|czech`  
GUID |  webpageitem|byguid|<GUID> **Example:** `webpageitem|byguid|affc44f3-...` |  Targets an item by its GUID and optionally language variant. `languageName` must match the code name of a language registered in the **Languages** application.  
GUID and language variant |  webpageitem|byguid|<GUID>|<languageName> **Example:** `webpageitem|byguid|affc44f3-...|czech`  
Content type in website channel |  webpageitem|bychannel|<channelName>|bycontenttype|<contentType> **Example:** `webpageitem|bychannel|MyChannel|bycontenttype|My.Article` |  Targets all pages of the specified content type and optionally language variant in the given channel. `contentType` must match the full code name (namespace and name) of the targeted content type. `languageName` must match the code name of a language registered in the **Languages** application.  
Content type in website channel and language variant |  webpageitem|bychannel|<channelName>|bycontenttype|<contentType>|<languageName> **Example:** `webpageitem|bychannel|MyChannel|bycontenttype|My.Article|czech`  
Page path in website channel |  webpageitem|bychannel|<channelName>|bypath|<pagePath> **Example:** `webpageitem|bychannel|MyChannel|bypath|/Home` |  Targets a page with the specified page path, channel, and optionally language variant. `pagePath` must be a valid tree path. `languageName` must match the code name of a language registered in the **Languages** application. Changing a page’s path touches both the old and the new `bypath` dependency.  
Page path and language variant in website channel |  webpageitem|bychannel|<channelName>|bypath|<pagePath>|<languageName> **Example:** `webpageitem|bychannel|MyChannel|bypath|/Home|greek`  
Page path children in website channel |  webpageitem|bychannel|<channelName>|childrenofpath|<pagePath> **Example:** `webpageitem|bychannel|MyChannel|childrenofpath|/Articles` |  Targets all children on all sublevels of the specified page path. Optionally targets only specified language variants. `pagePath` must be a valid tree path. `languageName` must match the code name of a language registered in the **Languages** application.  
Page path children of language variant in website channel |  webpageitem|bychannel|<channelName>|childrenofpath|<pagePath>|<languageName> **Example:** `webpageitem|bychannel|MyChannel|childrenofpath|/Articles|greek`  
[Reusable field schema](/documentation/developers-and-admins/development/content-types/reusable-field-schemas) name |  webpageitem|bychannel|{channel}|byschema|{schemaname} **Example:** `webpageitem|bychannel|MyChannel|byschema|MySchema` |  Targets all pages using the specified schema name and optionally language variant in the given channel. `schemaname` must match the name of the targeted schema. `languageName` must match the code name of a language registered in the **Languages** application.  
Reusable field schema name and page language variant |  webpageitem|bychannel|{channel}|byschema|{schemaname}|{language} **Example:** `webpageitem|bychannel|MyChannel|byschema|MySchema|czech`  
## Reusable field schemas
Dependency on |  Cache key format |  Description  
---|---|---  
Schema name |  reusablefieldschema|byname|{name} **Example:** `reusablefieldschema|byname|MySchema` |  Targets a [reusable field schema](/documentation/developers-and-admins/development/content-types/reusable-field-schemas) by its name. Touched when anything related to the schema changes (e.g., schema update, assignment to content type, removal).  
## Media files
Dependency on |  Cache key format |  Example |  Description  
---|---|---|---  
GUID |  mediafile|<guid> |  mediafile|1ced44f3-f2fc- … |  Cache dependencies can only target [media library files](/documentation/business-users/media-libraries/manage-media-files) using their GUID.  
## Settings
Dependency on |  Cache key format |  Description  
---|---|---  
Code name |  cms.settingskey|byname|<settingCodeName> |  Targets the [settings](/documentation/developers-and-admins/configuration/settings) specified by the given code name.  
## General objects
Dependency on |  Cache key format |  Example |  Description  
---|---|---|---  
All |  <object type>|all |  cms.user|all |  Targets all administration users in the system.  
ID |  <object type>|byid|<id> |  cms.user|byid|53 |  Targets the administration user with the specific ID.  
Code name |  <object type>|byname|<code name> |  cms.user|byname|administrator |  Targets the administration user with the specific code name.  
GUID |  <object type>|byguid|<guid> |  cms.user|byguid|1ced44f3-f2fc- … |  Targets the administration user with the specific GUID.  
## Form data records
The system currently does not touch any dummy cache keys when changes occur for data submitted via [forms](/documentation/business-users/digital-marketing/forms).
As a workaround, developers can prepare custom [event handlers](/documentation/developers-and-admins/customization/handle-global-events) for `BizFormItemEvents`. Use the handler method to touch a custom cache key, and then enter the key into your cache dependencies. For example:
C#
Copy
```
private void FormItem_InsertAfterHandler(object sender, BizFormItemEventArgs e)
{
    // Touches a custom cache key, for example: "customformdata|bizform.contactus"
    CacheHelper.TouchKey("customformdata|" + e.Item.BizFormClassName);
}
```

![]()
[]()[]()
