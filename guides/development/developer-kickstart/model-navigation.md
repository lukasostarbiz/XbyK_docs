---
source: https://docs.kentico.com/guides/development/developer-kickstart/model-navigation
scrape_date: 2026-01-22
---

  * [Home](/guides)
  * [Development](/guides/development)
  * [Developer kickstart](/guides/development/developer-kickstart)
  * Model navigation 


# Model navigation
Let’s create a content model that will allow your editors to be in control of the website’s main navigation.
We’ll create two content types– one to represent a menu, and one to represent the individual items it contains. The editors can manage these menus in the Content hub to decide what the menu includes.
## Define the navigation item type
Start with the content type that represents an individual item in the menu. It needs a title to hold the text of the link, and a target to choose which page the link points to.
Open the **Content types** application and define a new reusable type. The process will be the same as when you created the _Slogan_ type [earlier](/guides/development/developer-kickstart/create-a-reusable-content-type), but you should use these values instead:
  * **General**
    * **Content type name:** Navigation item
    * **Namespace:** Kickstart
    * **Name:** NavigationItem
    * **Icon:** xp-chain (search for ‘chain’)
    * **Use for:** Reusable content
  * **Fields**
    1. NavigationItemTitle 
       * **Field name:** NavigationItemTitle
       * **Data type:** Text
       * **Size:** 200
       * **Required:** True (Enabled)
       * **Display in editing form:** True (Enabled)
       * **Field caption:** Title
       * **Form component:** Text input
    2. NavigationItemTarget 
       * **Field name:** NavigationItemTarget
       * **Data type:** Pages and reusable content
       * **Display in editing form:** True (Enabled)
       * **Field caption:** Target
       * **Form component:** Combined content selector
       * **Form component configuration → Allowed content types:** Landing page
       * **Minimum number of selected items** : 1
       * **Maximum number of selected items** : 1


## Create the menu type
Next, you need another reusable content type to represent the menu as a whole.
Most importantly, it should have a field for referencing the items it contains, much like the _Landing page_ referenced a _Slogan_ in the [earlier step](/guides/development/developer-kickstart/define-a-page-content-type).
Additionally, it should have a _Display name_ and _Code name_ in case there are multiple menus that need to be handled in code or displayed in a list.
Define a new content type with the following values:
  * **General**
    * **Content type name:** Navigation menu
    * **Namespace:** Kickstart
    * **Name:** NavigationMenu
    * **Icon:** xp-list-bullets (search for ‘list-bullets’)
    * **Use for:** Reusable content
  * **Fields**
    1. NavigationMenuDisplayName 
       * **Field name:** NavigationMenuDisplayName
       * **Data type:** Text
       * **Size:** 200
       * **Required:** True (Enabled)
       * **Display in editing form:** True (Enabled)
       * **Field caption:** Display name
       * **Form component:** Text input
    2. NavigationMenuCodeName 
       * **Field name:** NavigationMenuCodeName
       * **Data type:** Text
       * **Size:** 200
       * **Required:** True (Enabled)
       * **Display in editing form:** True (Enabled)
       * **Field caption:** Code name
       * **Form component:** Text input
    3. NavigationMenuItems 
       * **Field name:** NavigationMenuItems
       * **Data type:** Pages and reusable content
       * **Display in editing form:** True (Enabled)
       * **Field caption:** Items
       * **Form component:** Combined content selector
       * **Form component configuration → Allowed content types:** Navigation item


Now, the list in your **Content types** application should look like this:
[![Screenshot of content types listing](/docsassets/guides/model-navigation/content-types-list.png)](/docsassets/guides/model-navigation/content-types-list.png)
## Generate the code
As with the _Slogan_ type [earlier](/guides/development/developer-kickstart/create-a-reusable-content-type#generate-the-class), open a command window in the _Kickstart.Web_ directory and generate code files for the _Navigation item_ and _Navigation menu_ content types.
CMD
Copy
```
dotnet run --no-build -- --kxp-codegen --type "ReusableContentTypes" --location "../Kickstart.Entities/{type}/{name}" --include "Kickstart.NavigationItem;Kickstart.NavigationMenu"
```

## Build the main menu
With these content types in place, we can create new items to define what the main menu should contain.
In the **Content hub** application, create a new content item of the type _Navigation menu_.
Set its **Content item name** to _Main navigation_.
On the next screen, set the following properties:
  * **DisplayName:** Main navigation
  * **CodeName:** MainNavigation


Then, under **Items** click **Create new** to define two items corresponding to the website’s pages:
  1. Home 
     * **Content item name:** Home link
     * **Location:** Uncategorized (no folder)
     * **Content type:** Navigation item
     * **Title:** Home link
     * **Target:** Home
  2. Contact us 
     * **Content item name:** Contact us link
     * **Location:** Uncategorized (no folder)
     * **Content type:** Navigation item
     * **Title:** Contact us link
     * **Target:** Contact us


Make sure to publish each item, either individually or through [cascade publishing](/documentation/business-users/content-hub/content-items#cascade-publishing).
Your browser does not support the video tag. 
At this point, your items in the Content hub should look like this:
[![Screenshot of Content hub ‘All items’ listing](/docsassets/guides/model-navigation/content-hub-list.png)](/docsassets/guides/model-navigation/content-hub-list.png)
Keep following along to see how to render this newly-defined menu on your website!
## Continue learning
When you’re ready, move on to the next page: [Implement navigation](/guides/development/developer-kickstart/implement-navigation)
![]()
[]()[]()
