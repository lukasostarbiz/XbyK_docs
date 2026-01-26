---
source: https://docs.kentico.com/documentation/developers-and-admins/customization/extend-the-administration-interface/ui-pages/ui-page-extenders
scrape_date: 2026-01-26
---

  * [Home](/documentation)
  * [Developers and admins](/documentation/developers-and-admins)
  * [Customization](/documentation/developers-and-admins/customization)
  * [Extend the administration interface](/documentation/developers-and-admins/customization/extend-the-administration-interface)
  * [UI pages](/documentation/developers-and-admins/customization/extend-the-administration-interface/ui-pages)
  * UI page extenders 


# UI page extenders
If you need custom functionality or content adjustments that you cannot configure through the properties available from the admin UI, you can develop a **page extender** for the corresponding page.
Page extenders allow you to modify the back-end definition of existing UI pages without directly editing their source code. You can use them to add new [commands](/documentation/developers-and-admins/customization/extend-the-administration-interface/ui-pages/ui-page-commands) or change the initial configuration of pages. For example, you can add additional columns to an object listing (e.g., to display additional properties of registered users) or specify new actions (e.g., perform a specific operation with selected objects).
The following diagram illustrates the function of page extenders.
[![Page extender overview diagram](/docsassets/documentation/ui-page-extenders/PageExtenderHighlevel.png)](/docsassets/documentation/ui-page-extenders/PageExtenderHighlevel.png)
## Create page extenders
All page extenders must inherit from the `PageExtender<TPage>` base class. Substitute the generic parameter with the type of the page you wish to extend.
To find the implementing type of default or other third-party pages that don’t make their source code available, the admin UI provides the **UI tree** view available in the **System** application. See [Register new pages under existing admin UI pages](/documentation/developers-and-admins/customization/extend-the-administration-interface/ui-pages#UIpages-UITree) for more information.
C#
**Creating a page extender**
Copy
```
using Kentico.Xperience.Admin.Base;
using Kentico.Xperience.Admin.Base.UIPages;

// Extends the user listing from the 'Users' application
public class UserListExtender : PageExtender<UserList>
{
}
```

## Modify the page configuration
Within page extenders, you can modify the default [configuration of the page](/documentation/developers-and-admins/customization/extend-the-administration-interface/ui-pages) and its [client template properties](/documentation/developers-and-admins/customization/extend-the-administration-interface/ui-pages).
The options available to you largely depend on the type of the page you wish to extend. See [Reference - UI page templates](/documentation/developers-and-admins/customization/extend-the-administration-interface/ui-pages/reference-ui-page-templates) for a list of templates on which most of the administration interface is built.
C#
**Example - extending the Users application user listing page**
Copy
```
public class UserListExtender : PageExtender<UserList>
{
    // Configures the listing template on which UserList is based
    // The available configuration options vary based on the type of the extended page
    public override Task ConfigurePage()
    {
        // Adds a new action to each item in the listing
        Page.PageConfiguration.TableActions.AddCommand...

        // Adds a new action for the entire listing
        Page.PageConfiguration.HeaderActions.AddCommand...

        return base.ConfigurePage();
    }

    // Configures the properties of the client template this page is based on
    // Called after the 'ConfigurePage' method
    public override Task<TemplateClientProperties> ConfigureTemplateProperties(TemplateClientProperties properties)
    {
        // Manipulate properties as desired

        return base.ConfigureTemplateProperties(properties);
    }
}
```

## Add new page commands
You can use extenders to add new [page commands](/documentation/developers-and-admins/customization/extend-the-administration-interface/ui-pages/ui-page-commands). 
Declare a new method within the page extender class and annotate it with the `PageCommand` attribute. The process is identical to writing commands for UI pages.
C#
Copy
```
[PageCommand]
public async Task<ICommandResponse> MyExtenderCommand()
{
    // Command logic...

    return Response().AddSuccessMessage("Command executed.");
}
```

## Register page extenders
Page extenders need to be registered in the system via the `PageExtender` assembly attribute.
When registering extenders, you need to provide the `System.Type` of the implementing class.
C#
**Page extender registration**
Copy
```
using Kentico.Xperience.Admin.Base;

// Registers the page extender of the 'MyPageExtender' type into the system
[assembly: PageExtender(typeof(MyPageExtender))]

public class MyPageExtender : PageExtender<MyPage>
{
    ...
}
```

![]()
[]()[]()
