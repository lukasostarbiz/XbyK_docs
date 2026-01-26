---
source: https://docs.kentico.com/modules/developer-kickstart/define-a-page-content-type
scrape_date: 2026-01-26
---

Module: Xperience by Kentico Developer kickstart
5 of 13 Pages
# Define a page content type
Previously, we created a reusable content type called [_Slogan_](/modules/developer-kickstart/create-a-reusable-content-type). In order to use it in the website channel we defined in the [last step](/modules/developer-kickstart/add-a-website-channel), we’ll need a [page content type](/documentation/business-users/website-content). Pages of this type will reference reusable Slogan items.
## Add a page content type in the admin UI
Begin by navigating to **Configuration → Content types → New content type**.
Fill in the form with the following properties:
  * **Display Name:** Landing page
  * **Namespace:** Kickstart
  * **Name:** LandingPage
  * **Icon:** _xp-market_ (type ‘market’ in the select list search bar)
  * **Use for:** select **Pages** from the dropdown
  * **Include in routing:** True (checked)


[![Screenshot of the landing page content type’s general properties](/docsassets/guides/define-a-page-content-type/landing-page-content-type-general.png)](/docsassets/guides/define-a-page-content-type/landing-page-content-type-general.png)
**Save** and switch to the **Fields** tab of the new _Landing page_ content type. Add a field called _LandingPageSlogan_. Define the following properties to enable this field to reference Slogan items from the Content hub:
  * **Field name:** LandingPageSlogan (we recommend using the _< content type name><field name>_ naming convention [as explained here](/modules/developer-kickstart/create-a-reusable-content-type#NamingConvention))
  * **Data Type:** Pages and reusable content
  * **Display in editing form:** True (Enabled)
  * **Field caption:** Slogan
  * **Form component:** Combined content selector
  * **Form component configuration → Allowed content type:** Slogan
This links your reusable content type with the page and ensures only items of type _Slogan_ will be allowed on any web page of type _Landing page_. 


Your content type should look like this:
[![Screenshot of landing page field editor](/docsassets/guides/define-a-page-content-type/landing-page-content-type.png)](/docsassets/guides/define-a-page-content-type/landing-page-content-type.png)
Our example page content type is very basic, with only one field. In production scenarios, the web page content type should also include [reusable field schemas](/documentation/developers-and-admins/development/content-types/reusable-field-schemas) to represent structured data needed only for the web channel, such as those related to SEO, OpenGraph, and other web-specific metadata.
### Link the type to the channel
Now you need to associate the page content type with the website channel from the [previous step](/modules/developer-kickstart/add-a-website-channel).
Switch to the **Allowed in channels** tab of the content type, and click **Select channels**.
Select **Kickstart pages** , and save the selection.
[![Allowing the ‘Landing page’ content type in the ‘Kickstart pages’ channel.](/docsassets/guides/define-a-page-content-type/allow-in-channel.png)](/docsassets/guides/define-a-page-content-type/allow-in-channel.png)
The **Kickstart pages** channel now shows up as an item in the list.
[![Screenshot of the landing page content type’s allowed channels.](/docsassets/guides/define-a-page-content-type/landing-page-content-type-channels.png)](/docsassets/guides/define-a-page-content-type/landing-page-content-type-channels.png)
Next, you need to [allow the content type in our channel’s scope](/documentation/developers-and-admins/development/content-types/limit-the-pages-users-can-create#allow-content-types-in-scopes). Scopes in Xperience serve to [limit which pages users can create](/documentation/developers-and-admins/development/content-types/limit-the-pages-users-can-create) in channels. By default, your _Kickstart pages_ channel has one scope defined for root.
Switch to the **Allowed in scopes** tab of the content type, and click **Add scopes**.
Select **Kickstart pages** default scope, and **Save** the selection.
[![Allowing the ‘Landing page’ content type in the ‘/’ scope.](/docsassets/guides/define-a-page-content-type/allow-in-scopes.png)](/docsassets/guides/define-a-page-content-type/allow-in-scopes.png)
Now users are allowed to create pages of type _Landing page_ in the root of your **Kickstart pages** channel.
If you navigate to **Configuration → Channel management** → **Kickstart pages → Allowed content types** , you will see the _Landing page_ content type listed.
[![Landing page content type showing as allowed in the Kickstart pages channel.](/docsassets/guides/define-a-page-content-type/landing-page-allowed-in-channel.png)](/docsassets/guides/define-a-page-content-type/landing-page-allowed-in-channel.png)
When you switch to the **Scopes for content types** tab and click on the **“/”** scope, you’ll see the _Landing page_ type listed under **Allowed content types in scope** as well.
[![Landing page content type showing as allowed in the Kickstart pages default scope.](/docsassets/guides/define-a-page-content-type/landing-page-allowed-in-scope.png)](/docsassets/guides/define-a-page-content-type/landing-page-allowed-in-scope.png)
Learn more about [limiting page creation in channels in our docs](/documentation/developers-and-admins/development/content-types/limit-the-pages-users-can-create).
**Pages and page templates**
At this point, you are technically able to create a page of type _Landing page_ in your _Kickstart pages_ channel. However, if you are following along with the Kickstart series, do NOT create a new page of this type yet.
A future step in this series will show how to create page templates. Pages that are not assigned a page template at the time of creation cannot be assigned one at a later time.
### Generate a class for the Landing page content type
Just like with the _Slogan_ content type, you need to generate a class to access the _Landing page_ content type in your .NET solution. Go to the _Kickstart.Web_ project in a new command window and run the following command:
Bash
**Generate code files**
Copy
```
dotnet run --no-build -- --kxp-codegen --type "PageContentTypes" --location "../Kickstart.Entities/{type}/{name}" --include "Kickstart.LandingPage"
```

Find more information about code generation on [this documentation page](/documentation/developers-and-admins/api/generate-code-files-for-system-objects) or [this video](/guides/development/get-started/generate-code-for-custom-content-and-data-classes).
After the command or script above completes, you should see a new **LandingPage.generated.cs** file in the _~/PageContentTypes/LandingPage_ folder of the _Kickstart.Entities_ project.
In the next step, we’ll make some configurations to prepare the application to serve our page.
[ Previous page ](/modules/developer-kickstart/add-a-website-channel)
5 of 13
[ Mark complete and continue ](/modules/developer-kickstart/configure-the-project)
![]()
[]()[]()
