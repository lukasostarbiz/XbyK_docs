# Content versioning configuration
  * [ Copy page link ](documentation/developers-and-admins/configuration/content-versioning-configuration#) | [Get HelpService ID](documentation/developers-and-admins/configuration/content-versioning-configuration#)
Core MVC 5


[✖](documentation/developers-and-admins/configuration/content-versioning-configuration# "Close page link panel") [Copy to clipboard](documentation/developers-and-admins/configuration/content-versioning-configuration#)
When content versioning is [enabled](documentation/developers-and-admins/configuration/content-versioning-configuration#set-up-content-versioning), Xperience by Kentico automatically stores previously published versions of [reusable content items](documentation/business-users/content-hub/content-items), [pages](documentation/business-users/website-content), and [headless items](documentation/business-users/headless-content). Once content versioning is in use, storing newly published versions can be [prevented](documentation/developers-and-admins/configuration/content-versioning-configuration#suspend-versioning-in-custom-code) in contexts where it’s not desirable.
## Set up content versioning
Once enabled, content versioning starts tracking published versions. Only versions published after enabling it will be included in the version history.
  1. Open the **Settings** application.
  2. Select **Content** -> **Content versioning**.
  3. Select the **Enable content versioning** setting.
  4. Enter the **Number of stored versions**.
     * The number of versions determines how many published versions of one language variant of an item the system stores.
     * To track all versions that will be published, enter 0. However, we recommend considering whether you need all versions and how this affects your system’s performance. 
Storing too many versions can be detrimental to the system’s performance. The optimal number of versions depends on your project and environment. We don’t recommend storing more versions than you need. 
  5. **Save** the settings.
[![Save configuration](docsassets/documentation/content-versioning-configuration/settings_save.png)](https://docs.kentico.com/docsassets/documentation/content-versioning-configuration/settings_save.png)


## Permissions
The following table shows which permissions are needed for users to be able to [restore a previous version](documentation/business-users/content-versioning#restore-previously-published-versions) of an item or view its [version history](documentation/business-users/content-versioning#view-version-history).
Content |  Permissions related to content versioning  
---|---  
Pages |  Users with permission to _Read_ a page can view its [version history](documentation/business-users/content-versioning#view-version-history). Users with permission to _Update_ a page can [restore](documentation/business-users/content-versioning#restore-previously-published-versions) its previous versions.  
Reusable content items |  Users with the _View_ permission for a workspace in [Content hub](documentation/business-users/content-hub) can [view version history](documentation/business-users/content-versioning#view-version-history) for all items in that workspace. Users with _Update_ permission for a workspace can [restore](documentation/business-users/content-versioning#restore-previously-published-versions) previous versions of items in that workspace.  
Headless items |  Users with permission to _View_ a headless channel application can [view version history](documentation/business-users/content-versioning#view-version-history) for all items in that channel. Users with _Update_ permission for a headless channel application can [restore](documentation/business-users/content-versioning#restore-previously-published-versions) previous versions of items in this channel.  
An item can be restored when the item is in any [workflow step](documentation/developers-and-admins/configuration/workflows). Users can restore a previous version of an item even when the item is in a step they don’t have permission for. The item is restored to the _Draft_ step with the data from the selected version, and the item has to go through the regular workflow from the start.
## Suspend versioning in custom code
If you need to suspend content versioning in custom code (for example, in integration scenarios), you can do so by enclosing the code into a `using` statement with a declared `ContentItemVersionActionContext` instance and the `EnableStoreVersion` property set to `false`.
C#
**Example**
Copy
```
using CMS.ContentEngine;
using CMS.Websites;
using CMS.Websites.Routing;

...

// Disables Versioning
using (new ContentItemVersionActionContext { EnableStoreVersion = false })
{
    ...

    await webPageManager.TryCreateDraft(pageId, languageName);

    ...

    // Page is published without creating a version in the version history
    await webPageManager.TryPublish(pageId, languageName);

}
```