---
source: https://docs.kentico.com/guides/architecture/content-modeling/model-website-content-types/model-website-navigation
scrape_date: 2026-01-22
---

  * [Home](/guides)
  * [Architecture](/guides/architecture)
  * [Content modeling](/guides/architecture/content-modeling)
  * [Model website content types](/guides/architecture/content-modeling/model-website-content-types)
  * Model website navigation 


# Model website navigation
Website navigation arranges links and menus to help users efficiently browse and access information on a website.
This guide will show you the recommended practices for defining and organizing content types and their properties that your editors will use to create your navigation menus in any website channel managed in Xperience by Kentico.
## Prerequisites
You should have basic knowledge of [content modeling](/guides/architecture/content-modeling/content-modeling-guide) principles.
## Start from your marketing team’s needs
A thorough examination of your existing content, its lifecycle, editing workflows, and discussions with content editors, strategists, or information architects help build the groundwork for your content model.
Let’s see how to approach building website navigation options in Xperience using the following scenario: 
Your marketing team manages several websites across different languages, including two main [website channels](/guides/digital-marketing/work-with-channels/digital-marketing-channels) and several smaller, campaign-focused microchannels. Each microchannel will have around 15 URLs, including several “thank you” pages that appear after users submit forms. The team wants to organize navigation menus independently on each channel.
On the main websites, they want to:
  * Group navigation items at various levels – primary, secondary, or tertiary – to fit each site’s structure.
  * Link from the navigation menu to pages that might reside within the same website, on another Xperience channel, or even on an external, third-party site.


On the microsite, they want to:
  * Build only a primary-level navigation menu on the microchannel website.
  * Link from the navigation menu to pages that reside on another Xperience channel and on external, third-party websites.


## Analyze your team’s requirements for working with navigation
During the content audit and project shaping to deliver the [scenario above](#guideScenario), your team identified the following criteria:
  * The navigation will differ per website channel and will be multilingual.
  * The primary navigation will have two levels, for example:
[![Simple navigation on the Kbank demo site](/docsassets/guides/model-website-navigation/kbank-sample-navigation.png)](/docsassets/guides/model-website-navigation/kbank-sample-navigation.png)
  * Side menus can nest up to three levels.
  * Some of the parent navigation items will contain a hyperlink.  
[![Conceptual draft of navigation structure with that contains several menu items without a link](/docsassets/guides/model-website-navigation/hierarchy-structure-concept-with-links.png)](/docsassets/guides/model-website-navigation/hierarchy-structure-concept-with-links.png)
  * The microchannel navigation will be single level and contain maximally five pages.
    * Editors need to be able to dynamically add or remove pages from the menu and reorder individual pages in the navigation depending on the running campaigns.
  * Editors will need to adjust the navigation content on their own; creating navigation should be simple and follow the same pattern across different website channels.
  * Editors will need to create different menu items in different groups depending on the website channel. For example, microsites will have navigation without nested elements.
  * Designers expect to introduce [mega menus](https://www.nngroup.com/articles/mega-menus-work-well/) for some of the website channels.


Requirements for building navigation will change depending on the [website channel hierarchy](https://www.nngroup.com/articles/flat-vs-deep-hierarchy/), the complexity of the project, and the size of each website channel. 
## Define the content types and their properties to build the navigation menu
Based on your team’s requirements, you can define a _Navigation_ [content type](/documentation/developers-and-admins/development/content-types) editors will use to create different navigation menus.
Considering your team’s conventions, you’ve defined the following properties of the _Navigation_ item:
  * Navigation 
    * Caption
    * Icon
    * Item description
    * Link to page
    * Link to external website
    * Open in a new tab
    * Navigation menu type 
      * Header navigation
      * Footer navigation
      * Mega menu
      * Side navigation


As designers consider building mega menus with icons and images, you will reuse the existing Asset content type to store mega menu visuals.
  * Asset 
    * Description
    * Alt text caption
    * The media file


## Three approaches to modeling navigation in Xperience by Kentico
In Xperience by Kentico, you can structure your navigation menus using one of three main approaches, each suited to different project needs. All approaches leverage the **Combined Content Selector** feature and allow editors to decide if they want to link to a page in the website channel, or use a dedicated reusable content type within one simple selector.
### Define navigation in the content tree
With the content tree-based approach, editors build navigation menus by arranging pages directly within the content tree. Here, they can define parent-child relationships to create complex, multi-level menus.
Website microchannels limit the number of content tree items to 20. Learn more about [the differences between standard and microchannels](/guides/digital-marketing/work-with-channels/digital-marketing-channels).
#### Advantages of defining navigation in the content tree
  * Intuitive Structure: Editors grasp this approach easily, as the navigation in the content tree mirrors the actual navigation structure on the live site. Each item in the navigation section in the content tree represents a visible item in the menu or part of its structure. Editors can set up the hierarchy without any issues.
  * Access Control: Using the [page permissions](/documentation/developers-and-admins/configuration/users/role-management/page-permission-management) in the content tree ACLs, you can control which editorial roles can access specific navigation sections within the content tree.


#### Challenges of defining navigation in the content tree
  * Limiting item’s reusability: Content items used for navigation are defined as “Pages” and thus are inaccessible in the Content Hub for broader reuse across channels. If your navigation item contains a link to an external site, you cannot reuse this “link” in email or headless channel, and you have to create a different “reusable-only” link representation in the Content hub.
  * Overloading the content tree: Adding navigation items to the content tree introduces “visual clutter,” as these items are non-navigable URLs. Editors can feel confused since these items don’t align with the rest of the content tree where each page represents a navigable URL. It also [limits the number of navigable pages](#microchannelItems) that editors can create in a microchannel.


### Define navigation in the Content Hub
A recommended approach for most projects is to keep navigation separate from the content tree. Instead, editors store the navigation menu items within the Content Hub. They can put them into a dedicated _Navigation menus_ folder per each website channel and ensure the navigation doesn’t mix with other content. (In the upcoming releases, editors will be able to assign these folders to a [channel-specific workspace](https://roadmap.kentico.com/c/146-workspaces-granular-role-permissions-for-content-hub-items). The workspace will ensure that only roles allowed to work with the navigation for that specific channel will be able to edit the channel’s navigation menus.)
The following example from the [Developer kickstart](/guides/development/developer-kickstart/model-navigation) shows a simple implementation of a navigation menu using a dedicated content type.
[![Navigation items in the Content hub](/docsassets/guides/model-website-navigation/cm-model-navigation-items-in-content-hub.png)](/docsassets/guides/model-website-navigation/cm-model-navigation-items-in-content-hub.png)
#### Advantages of defining navigation in the Content Hub
  * Improving consistent behavior: Because navigation elements aren’t navigable, they remain distinct from the main content tree items that visitors access through URLs, keeping the content tree clean and reducing visual overload.
  * Allows for flexible updates: Editors of large website projects often need to update the navigation structure completely, especially when part of their content strategy changes. Using navigation in the Content hub, they can create the new navigation layout without disturbing the existing content tree setup and block small navigation menu updates in the meantime.
  * Simplifying the updates using Smart Folders: Smart folders are saved views in the Content hub that support dynamic delivery. By placing the navigation structure in a dedicated smart folder, you can easily display the navigation through a smart folder selector or update the navigation directly from the Content hub.
  * Making your navigation future-proof: Defining navigation in the Content hub approach supports forward compatibility with the upcoming [Advanced navigation modeling](https://roadmap.kentico.com/c/234-advanced-navigation-modelling) feature.


#### Challenges of defining navigation in the Content hub
  * Too much reusability: Navigation items stored in the Content Hub become reusable across different channels, especially if you don’t store them in a dedicated folder or restrict access by adding them to a dedicated workspace [in the future](https://roadmap.kentico.com/c/146-workspaces-granular-role-permissions-for-content-hub-items). As navigation is often unique to each website channel and making menu items can confuse editors. They might accidentally use a navigation menu from one channel in another and create navigation inconsistencies.
  * Editor Confusion: Depending on the project configuration, editors in Xperience can create and launch their website channels by themselves without developers assistance. If approaches to modeling microchannel and standard channel navigation menus differ ([see below](#define-microchannel-navigation)), editors might not know where to define or edit the navigation. Using different methods for defining the same thing, such as a navigation menu, in projects with multiple channels where smaller channels are managed by teams with lower content management maturity can be confusing. If your project requires different approaches for building navigation menus, help everyone on your team succeed. For example, website administrators will use the upcoming _workspace_ feature and make sure that editors only see navigation items they need to work with. Remember also to provide suitable documentation that explains the different approaches (and reasoning why they differ) to the editor teams.


### Define microchannel navigation
In smaller or single-purpose website microchannels, navigation menus are simple with only a few items. The navigation typically consists of a primary menu, usually built from the main pages in the channel. It doesn’t make sense to create navigation structure of this size in the content tree as it makes the simple content tree unnecessarily complex. We recommend consulting with the team to understand their requirements for microchannels and determine which pages they want included in the navigation.
#### Dynamically list all navigation items
The simplest option developers can implement is to automatically include existing pages in the content tree into the navigation menu. Editors will need to follow the project documentation to ensure that the menu contains only the pages they intended to include.
There are many different options for how to approach building the navigation menu dynamically, for example:
  * Developers can programmatically display only the content-tree’s top-level pages in the navigation. If the editors want to add, for example, thank you pages to their microsite, they need to add them as child pages to ensure navigation menu contains only intended items.
  * The navigation menu will contain only pages of a specific content type, for example, a landing page built for a specific persona.


#### Allow editors to select navigation items
In many cases, editors want to define the microchannel navigation themselves and only include specific pages into the navigation menu. There are different ways how to implement the solution.
The recommended approach is to create a dedicated content type for **Microchannel settings** that editors add to the content tree. Editors can use it to override the global settings for each of their website microchannels.
Using the dedicated selector in this microchannel settings content type, editors can directly select the pages from the tree and order them as needed. In case they need to refer to external pages from their microchannel navigation, they can use a custom Navigation item content type or a reusable **Global Call to action** content type to store the target URL of an external website.
[![Draft of a microchannel settings content type](/docsassets/guides/model-website-navigation/cm-example-microchannel-settings-draft.png)](/docsassets/guides/model-website-navigation/cm-example-microchannel-settings-draft.png)
In large projects heavily built on reusable content scenarios, editors can display content of one content item in different places on the website or different even external website channels. They can use custom reusable _Global Call to action_ content type to pair the reusable content item with it’s default navigable representation. We will cover different aspects of using the reusable _Global Call to action_ content type in another guide.
[![Example of a Global call to action content type](/docsassets/guides/model-website-navigation/cm-global-cta-reusable-item.png)](/docsassets/guides/model-website-navigation/cm-global-cta-reusable-item.png)
One of the convenient options is to create a custom **Microchannel website page** content type containing a custom _Display in the menu_ checkbox property that editors select when creating the page. Developers will display all respective pages in the navigation using their order in the content tree. We don’t recommend using this approach for standard website channels with multi-layer navigation menus as they make their organization less transparent and tend to complicate building navigation menus when projects grow large.
Other options that provide less flexibility to editors include:
  * Editors select pages they want to include into the navigation using the [Page selector](/documentation/developers-and-admins/customization/extend-the-administration-interface/ui-form-components/reference-admin-ui-form-components#page-selector) in a dedicated content type’s field.
  * Editors leverage “Use in navigation” custom property that developers define for every webpage content type.


See our [Developer kickstart](/guides/development/developer-kickstart/model-navigation) for code samples about building navigation using dedicated Navigation menu content type instead of complex _Website settings page_ content type.
## Define how editors will create different navigation menus
Your model for the navigation menu is shaping up nicely. You know about the different options editors can use to define the navigation menus in Xperience. You’ve also identified the basic properties your team needs.
To sum up the different options for creating navigation menus, we recommend storing the navigation menu in the Content hub. If editors manage large websites with complex multi-level navigation or find creating the menus in Content hub tedious, using the visual hierarchy of the content tree hierarchy to organize reusable navigation items will ease up things. At the same time, using the content tree is less future-friendly given the plans for [advanced navigation modeling](https://roadmap.kentico.com/c/234-advanced-navigation-modelling). As Xperience by Kentico is _an evergreen product_ , we recommend following the [public roadmap for related upcoming features](https://roadmap.kentico.com/tabs/4-fy2024-priorities).
Let’s continue with the [scenario](#guideScenario).
The team has decided build the navigation menus for the larger site in the content tree. This will highlight the navigation hierarchy, provide an easy way to add new items and offer editors drag-and-drop capabilities if they need to reorder the menu.
[![Simplified diagram of navigation in the content tree](/docsassets/guides/model-website-navigation/cm-navigation-content-tree-sketch.png)](/docsassets/guides/model-website-navigation/cm-navigation-content-tree-sketch.png)
For microchannel websites, the team agreed to use a dedicated content type to manually select items for the navigation menu. They also want to introduce additional properties that apply only to this microchannel that are not related to the navigation menu and the custom _Microchannel settings_ content type was a convenient solution.
[![Simplified diagram of navigation in the channel settings](/docsassets/guides/model-website-navigation/cm-microchannel-settings-sketch.png)](/docsassets/guides/model-website-navigation/cm-microchannel-settings-sketch.png)
You can create a dedicated content type to define channel-specific properties. To manage your website channel settings, you can also create global solutions such as a dedicated [custom module](/guides/development/customizations-and-integrations/create-basic-module). If you use custom modules, we still recommend creating navigation menus using the Content hub, rather than defining the navigation structure in the custom module.
## Draft a simple navigation content type
When the team agrees on suggested approaches for building navigation, you can create a rough sketch of the content types for creating navigation menus.
[![Conceptual graph of a navigation content type](/docsassets/guides/model-website-navigation/navigation-simple-sketch.png)](/docsassets/guides/model-website-navigation/navigation-simple-sketch.png)
To simplify workflows for editors and developers, you’ve decided to build the navigation for standard website channel using one content type. As editors need to reorder items, they will make the navigation per [website channel](/documentation/business-users/website-content) using a dedicated section in the content tree.
## Add tips and hints for editors to your Navigation content type
Depending on your team’s experience, we recommend that you create sufficient instructions and document every content type field. Tips and hints help remove confusion and speed up your team’s content creation and curation process. Your instructions (and the whole content type) might look like the following:
[![Content model description in Excel with content type’s fields and instructions](/docsassets/guides/model-website-navigation/excel_list_of_contenttypefields.png)](/docsassets/guides/model-website-navigation/excel_list_of_contenttypefields.png)
## Verify Navigation content type with stakeholders
After completing the previous exercise, you and your team should know what content types editors need to store information for Navigation.
To help your team understand how editors will create the navigation in the hierarchy, you might want to create a simplified diagram like the following:
[![Diagrams shows the flow how editors will be creating the website’s navigation menu](/docsassets/guides/model-website-navigation/flow-of-creating-navigation.png)](/docsassets/guides/model-website-navigation/flow-of-creating-navigation.png)
## Create the content type in Xperience
To verify that the editors can easily create different navigation menus for multiple website channels that satisfy their needs, create the _Navigation content type_ in Xperience using the [Content types application](/documentation/developers-and-admins/development/content-types).
Xperience provides an dropdown component you can use to define lists of options. See a short, voice-less demonstration how you can help editors to define different navigation menu types or locations.
Your browser does not support the video tag. 
A navigation item added by an editor may look like the following:
[![Navigation menu content types in the Kbank demo website](/docsassets/guides/model-website-navigation/nav_item_content_type.png)](/docsassets/guides/model-website-navigation/nav_item_content_type.png)
## Have editors create a simple navigation structure
Representatives from the content editor team should participate in the website modeling process. It always helps to get a buy-in from the system users. Have your editors create several items in the content tree to get their feedback on how you’ve modeled the navigation content type and the whole navigation process.
During the hands-on testing, users raise questions and might bring out friction points they see when they use the content type. Listen to their feedback. They are the customers; they will use your designs daily, and you still have time to address their concerns.
For example, in our demo scenario, editors agreed that they like creating a navigation menu for their microsite using a dedicated settings page, as you can see in the Kbank demo site example below.
[![Kbank microchannel navigation settings](/docsassets/guides/model-website-navigation/cm-microchannel-navigation-example.png)](/docsassets/guides/model-website-navigation/cm-microchannel-navigation-example.png)
At the same time, they found it hard to understand that they create a navigation menu group and individual menu items with just one content type. They might also let you know that the visual experience where every item in the hierarchy was represented by the same icon of the content tree felt confusing.
[![Navigation menu structure modeled in the website channel’s content tree](/docsassets/guides/model-website-navigation/nav_menu_structure_in_content_tree.png)](/docsassets/guides/model-website-navigation/nav_menu_structure_in_content_tree.png)
## Redefine the editor flow into two Navigation content types
Talking to Kentico customers, we found that users with less technical background sometimes felt confused about how one item can reference another item of the same type or even become a parent to other items of the same type (and create a hierarchy). As your license in Xperience by Kentico does not limit you to a specific number of content types, you can create another content type to improve the editing experience.
Adjust the editing experience and create an additional **Navigation group** content type that editors will use to define sets of links. The new Navigation group will serve as a parent item for the links within the navigation menus. At the same time, editors can create a link from the Navigation group, should they need it.
Your final types for creating navigation menus might look like the following hierarchies.
[![Content model for navigation menu on a standard channel](/docsassets/guides/model-website-navigation/nav-menu-items-content-model.png)](/docsassets/guides/model-website-navigation/nav-menu-items-content-model.png)
[![Content model for navigation menu on a website microchannel](/docsassets/guides/model-website-navigation/cm-microchannel-settings.png)](/docsassets/guides/model-website-navigation/cm-microchannel-settings.png)
## Finalize your content type and get a write-off
Adjust both Navigation group and Navigation item content types, add instructions for editors to the new type, add anything missing during the testing, and resolve conflicts in data types and field order.
[![Content model for website navigation in an Excel spreadsheet](/docsassets/guides/model-website-navigation/final-excel-navigation-item-content-types.png)](/docsassets/guides/model-website-navigation/final-excel-navigation-item-content-types.png)
Once your content types for creating website navigation menus are done, hand over the definition to your team.
If you’ve followed the example in this guide, your editors can create different navigation menus in any website channel using the visual hierarchy of the content tree. Here’s an example of a demo of the Kbank Personal site’s navigation menu.
[![Example of a navigation menu structure in the Kbank demo site](/docsassets/guides/model-website-navigation/nav-menu-kbank-example.png)](/docsassets/guides/model-website-navigation/nav-menu-kbank-example.png)
![]()
[]()[]()
