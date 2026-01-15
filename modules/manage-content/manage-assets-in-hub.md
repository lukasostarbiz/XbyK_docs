# Organize assets in Content hub
  * [ Copy page link ](modules/manage-content/manage-assets-in-hub#) | [Get HelpService ID](modules/manage-content/manage-assets-in-hub#)
Core MVC 5


[✖](modules/manage-content/manage-assets-in-hub# "Close page link panel") [Copy to clipboard](modules/manage-content/manage-assets-in-hub#)
Every business works with different assets such as images, icons, infographics, videos or documents. Editors usually reuse and share these assets on websites, emails, and other digital marketing channels.
Previous Kentico versions used [media libraries](documentation/business-users/media-libraries) where editors stored their data into a folder hierarchy. _Media libraries_ have become [obsolete](documentation/business-users/media-libraries), and editors should store all assets into [Content hub](documentation/business-users/content-hub).
Unlike _media libraries_ , _Content hub_ doesn’t use a hierarchical system. Instead, all reusable items exist in **a single flat list**.
Piling all assets into _Content hub_ without any structure may seem easy at first, but quickly becomes a problem, especially in larger editor teams. As the number of assets grows, editors will struggle to locate the correct file and waste time searching through _Content hub_. It can get even worse: if they don’t immediately find the image they had in mind, they might upload duplicates. This could lead to inconsistencies across channels and become a content curation nightmare.
To manage large collections of assets, you can combine **workspaces** that separate content across the whole Xperience application and sort assets into **folders** within _Content hub_.
The following examples in this guide are recommendations and suggestions, not a prescription. You should discuss the kinds of assets you work with and how your teams collaborate during [content modeling sessions](guides/architecture/content-modeling/content-modeling-guide/content-modeling-process) with the stakeholders and adapt the system to your project’s needs.
## Understand how content folders work
_Content hub_ does not use a real folder hierarchy like a traditional file system. All assets represent [reusable content](guides/digital-marketing/xperience-essentials/manage-content-hub-essentials) that exists alongside other content items within a [workspace](documentation/developers-and-admins/configuration/users/role-management/workspaces) in a single flat list.
[Content folders](documentation/business-users/content-hub/content-hub-folders) are a way to group and view content items inside _Content hub_. Adding an item, like an image to a _content folder_ doesn’t move it anywhere or create duplicate copies. Instead, the folder creates a filter within _Content hub_ that helps editors find their content.
Key points to remember:
  * Content folders and smart folders are like filters over items in content hub; they don’t remove the item form the _Content hub_.
  * Assets, like other content items, stay in the same _workspace_ , even if you place them in a _folder_.
  * Each asset can belong to only one _folder_ at a time.
  * You can create a folder structure with parent folders and subfolders to match your needs.
  * The breadcrumb navigation at the top of the screen shows your location in the folder tree.
  * The _Location_ shows which content folder an item is assigned to.


[![Content folder breadcrumbs](docsassets/guides/organize-assets-in-content-hub/content-folder-breadcrumbs.png)](https://docs.kentico.com/docsassets/guides/organize-assets-in-content-hub/content-folder-breadcrumbs.png)
This approach keeps navigation simple. Instead of scrolling through hundreds of assets, editors can open the correct folder and immediately see what they need.
Your browser does not support the video tag. 
For example, an insurance company can create folders for each product type it provides: _Car insurance_ , _Home insurance_ , _Travel insurance_ , and _Life insurance_. When a campaign team needs graphics to promote new travel insurance, they find the approved images in the _Travel insurance_ folder without digging through unrelated assets.
## Create folder hierarchy
A good folder hierarchy helps editors find and reuse assets. It doesn’t change where assets are stored in _Content hub_ ; they always remain in the same flat workspace. _Content folders_ help you create a logical way for your team to navigate to them.
You can create as many folders and nest them as deeply as you need.
To create folders:
  1. Open _Content hub_ within the **workspace** where you want to organize assets.
  2. Create a **parent folder** for a major category of your assets.
  3. If needed, select this _parent folder_ and create **subfolders** for more specific groupings.
  4. Repeat this process until you have a clear and simple structure.


Let’s test it out in the Kbank demo site. Kbank runs personal and business banking websites. Editors need to manage product images for both sites, as well as campaign graphics. They want to create the following structure inside their _Kbank Global Media Assets_ workspace:
  * Top-level folders: 
    * Accounts
    * Cards
    * Insurance
    * Loans
  * Subfolders under **Cards** : 
    * Balance transfer
    * Rewards
    * Premier
  * Temporary subfolders for campaigns: 
    * Under **Balance transfer** , a _Fall 2025 campaign_ subfolder to store campaign-specific banners and promotional graphics.


This structure helps editors find the right asset quickly without scrolling through unrelated files. The **breadcrumb navigation** shows them exactly where they are in the folder tree. Campaign teams know where to upload temporary images and can easily clean up the campaign subfolders once the promotion ends.
[![Organizing folders into hierarchy in Content hub](docsassets/guides/organize-assets-in-content-hub/organize-content-hub-folders-into-hierarchy.png)](https://docs.kentico.com/docsassets/guides/organize-assets-in-content-hub/organize-content-hub-folders-into-hierarchy.png)
Keep your folder structure simple. Two to three levels of folders will suffice for most projects that keep assets alongside other content within the _Content hub_. Editors find complex hierarchies harder to navigate and maintain.
## Move items into content folders
Once you have a folder structure, you can organize your assets into the right folders. Remember, moving items doesn’t mean creating copies of files; moving just assigns these content items to a folder so they are easier to find.
Let’s continue within the Kbank demo site.
  1. Ensure you are in the _Kbank Global Media Assets_ repository.
  2. Select the items you want to organize: _Balance transfer card_ and _Female with credit card_.
  3. Use the **Move** action above the content items and choose the **Cards** > **Balance transfer** subfolder in the folder tree.
  4. Use **Move** to confirm.


Select the _Balance transfer_ folder to confirm that your items appear correctly. Then, open the **All items view** to verify that the _Balance transfer card_ asset is still available in the workspace in _Content hub_ and its _Location_ has been updated.
Your browser does not support the video tag. 
## Use workspaces to manage assets apart from other content
[Media libraries](documentation/business-users/media-libraries) kept all “files” separated from other content, which is not the case in _Content hub_.
Depending on how your editors feel about it, you might want to create a dedicated _workspace_ to store all files that different teams should access. You might also need to define what editors can or cannot do with the assets.
Imagine that [workspaces](documentation/developers-and-admins/configuration/users/role-management/workspaces) in Xperience are containers for content in the _Content hub_. Content in the _Content hub_ always belongs to one specific workspace.
Your [administrator](guides/digital-marketing/xperience-essentials/work-with-roles-essentials) assigns you access to the _workspaces_ and other system parts. No matter which application you work in, whether the _Content hub_ or a website channel, your _role_ defines which resources you can access.
This means:
  * Your role determines which _workspaces_ you can view and edit.
  * You stay within that _workspace_ when working in _Content hub_.
  * You can switch between _workspaces_ whenever you need.

Your browser does not support the video tag. 
Depending on your role’s permissions, you’ll be allowed different [operations](documentation/developers-and-admins/configuration/users/role-management) within the workspace:
  * **View** – Editors can use the assets in their work, but cannot change or delete them.
  * **Create** , **Update** – Editors can reuse and change (update) assets.
  * **Delete** – Editors can remove existing assets.


Your access to _website channels_ is also based on the _role_ (not workspace). This allows organizations to maintain _independent asset management_ and _channel editing permissions_ , so that teams get only the necessary access level.
From an organizational perspective, setting up a workspace to collect your company’s assets does more than group images in one place. It creates a shared space where:
  * Content is organized consistently across the system.
  * Permissions and access levels are applied reliably.
  * Editors and teams can collaborate without stepping on each other’s work.


Let’s take a look at an example. The Kbank demo site uses _three websites_ managed in Xperience, and a _number of other channels_ :
  * _Personal banking website_
  * _Business banking website_
  * _Mortgages website_
  * _Email Marketing_
  * _Headless app_


The Kbank teams organize their assets into these _workspaces_ :
  * **Brand Assets** workspace. This workspace contains official logos, icons, and approved images. Editors in all channels can reuse these, but only the design team can modify them.
  * **Kbank Global Media Assets** workspace. This workspace holds images that all teams share, such as generic promotional graphics. Editors from any channel can add and update these items.
  * **Channel-specific workspaces**. _Personal banking_ and _Business Banking_ website channels have their own workspace for content and assets used only on that website. _Access permissions_ , such as [page-level permissions](documentation/developers-and-admins/configuration/users/role-management/page-permission-management), are limited so editors from one channel cannot accidentally change content in another website that they are not allowed to change.


This structure gives editors a consistent experience across Xperience applications while keeping each area organized and secure.
[Image: Workspace list showing Brand assets, Global media assets, and channel-specific workspaces]
[![Workspaces in Kbank demo site](docsassets/guides/organize-assets-in-content-hub/workspaces-in-kbank.png)](https://docs.kentico.com/docsassets/guides/organize-assets-in-content-hub/workspaces-in-kbank.png)
Plan your workspace structure early. If your teams or projects grow, you can add new workspaces or adjust permissions without disrupting the rest of the system.
## Use smart folders to organize and filter assets
[Smart folders](documentation/business-users/content-hub/content-hub-folders#smart-folders) let you create dynamic groups of assets in _Content hub_. Unlike regular folders, which hold specific items until you move them, smart folders automatically include items that match your defined filter. As assets change, for example, when AIRA applies taxonomy tags or [adds image description](documentation/business-users/aira#automatically-generate-image-description)or editors publish them, assets move in and out of the _smart folder_ automatically.
To quickly find content you are responsible for, you can create smart folders:
  * Assets tagged with _Campaign 2025_ to collect assets with relevant taxonomy [tag](guides/digital-marketing/work-with-taxonomies/taxonomies).
  * Images modified in the last 7 days.
  * Images in _Draft_ status waiting for review.


Unlike [folders](modules/manage-content/manage-assets-in-hub#move-items-into-content-folders), a single asset can appear in multiple _smart folders_ , which makes them helpful in managing different workflows without duplicating content. Because smart folders update automatically, you don’t need to remember to move items in and out. They stay accurate as the underlying assets change.
## Deliver content dynamically with smart folders
For projects where developers have [enabled dynamic content delivery](guides/development/advanced-content/deliver-content-dynamically-with-smart-folders), _smart folders_ can also control which assets are displayed on your website channels. You can manage which assets appear live by [editing the filter conditions](documentation/business-users/content-hub/content-hub-folders#edit-smart-folders) in _Content hub_ without needing code changes.
For example, your team could:
  * Update a campaign smart folder filter to include new images; the website will automatically display them.
  * Remove outdated assets from a smart folder, instantly updating what users see in the related channel.


This makes smart folders especially powerful for teams managing campaigns or time-sensitive updates, as the filtering logic stays centralized in _Content hub_.
[ Previous page ](modules/manage-content/reuse-content-with-the-hero-widget)
8 of 19
[ Mark complete and continue ](modules/manage-content/manage-assets-best-practices)
  * [Community Questions & Answers](https://community.kentico.com/q-and-a)
  * [Contact support](https://community.kentico.com/support)


### Cookie consent
We use necessary [cookies](https://www.kentico.com/cookies-policy) to run our website and improve your experience while browsing. Additional cookies are only used with your consent. You may revoke your consent on the [Cookies Policy](https://www.kentico.com/cookies-policy) page or in your browser at any time. 
ACCEPT ALL  [Configure](https://www.kentico.com/cookies-policy)
USE ONLY NECESSARY 
![](https://docs.kentico.com/modules/manage-content/manage-assets-in-hub)
[](https://docs.kentico.com/modules/manage-content/manage-assets-in-hub)[](https://docs.kentico.com/modules/manage-content/manage-assets-in-hub)