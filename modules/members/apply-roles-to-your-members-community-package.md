# Apply roles to your members with this community package
  * [ Copy page link ](modules/members/apply-roles-to-your-members-community-package#) | [Get HelpService ID](modules/members/apply-roles-to-your-members-community-package#)
Core MVC 5


[✖](modules/members/apply-roles-to-your-members-community-package# "Close page link panel") [Copy to clipboard](modules/members/apply-roles-to-your-members-community-package#)
Some businesses have more complicated requirements for their authenticated members than the [earlier membership examples](guides/development/members/create-a-profile-page) cover.
For example, a business may need to have multiple groups of members, each of which has access to different pages. You could achieve this behavior if it were possible to assign roles to members, using those roles to restrict access to certain pages.
## The package
Kentico plans to include roles for members in Xperience, though it it does not yet have a firm position in our [roadmap](https://roadmap.kentico.com/tabs/4-planned) at the time of writing this guide.
In the meantime, Kentico MVP [_Trevor Fayas_](https://www.linkedin.com/in/trevor-fayas-a0628a32/) has created an open-source community module for assigning roles to members and controlling which pages they can access.
Visit the [XperienceCommunity.MemberRoles](https://github.com/KenticoDevTrev/MembershipRoles_Temp/tree/master) repository to view the module and find instructions for how to install and use this package.
This guide will give a brief overview of this member roles package and highlight some of its noteworthy features.
## How it works
Let’s take a high-level look at the components of the member roles package.
### How do the objects relate?
Instead of creating a new object type, Trevor’s repository uses [taxonomy tags](documentation/developers-and-admins/configuration/taxonomies) to represent the roles members can have.
There are three binding classes used to create relationships between these roles and the following objects:
  1. **Members** in the _Members_ application. 
     * A binding object relating a role to a member means the member is assigned to the given role.
  2. **Web page items** in web channels. 
     * A binding object relating a role to a web page item means that with correct configuration, members of the given role have permission to view the page.
  3. **Content folders** in _Content hub_. 
     * A binding object relating a role to a content folder means that with correct configuration, members of the given role have permission to view reusable items in the folder.


Then, there are two **Permission settings** classes: one for content folders and one for web page items. Each object of these types is associated with a single page or folder and specifies two things:
  1. Whether the given page or content folder should restrict unauthenticated members.
  2. Whether the list of permitted roles should be inherited from the parent or directly defined for the given object.


Finally, _XperienceCommunity.MemberRoles_ includes a **Member Permission Configuration** [reusable field schema](documentation/developers-and-admins/development/content-types/reusable-field-schemas), which indicates whether the package should check role permissions for a given content type. We’ll cover this vital schema in more detail under the next heading.
### How do you configure an item’s permissions?
You may have noticed the phrase “with correct configuration” qualifying a few statements under the previous heading. Let’s dive into that a bit further.
There are two important factors that designate _if_ and _how_ the member role code should check an item’s permissions:
#### Reusable field schema (the “If”)
As mentioned above, an item’s content type must have the **Member Permission Configuration** schema if you want to evaluate its permissions. You need to apply the schema _both in the database_ (by assigning it through the admin UI) _and in the code_ (by [re-generating code files](documentation/developers-and-admins/api/generate-code-files-for-system-objects) for the content type).
A content type **must** use the schema for the filters from _XperienceCommunity.MemberRoles_ to check its items’ permissions.
However, permission settings for an item can still exist, _even when they do not apply_. **MemberPermissions** still exist on the **Properties** tab of a web page item, even if its content type does not use the schema. Similarly, it’s possible to set **Folder permissions** for a content folder that does not contain items using the schema.
If an item’s content type does not have the correct schema, changes to these permissions will have _no direct effect_. However, if the item is a web page whose children inherit its permissions, changes will affect the children.
#### Inheritance (the “How”)
The way the package checks an item’s permissions is determined by setting permission inheritance.
If a web page item is set to **inherit permissions** , permission checks for that item will check the permissions settings of the parent item. If it’s set to **Break Inheritance** , the package will check the permissions assigned directly to the item in the **Member Permissions** category of its **Properties** tab.
If a content folder is set to **inherit permissions** , permission checks for the reusable items it contains will evaluate the settings of the parent folder. If it’s set to **Break Inheritance** , the package will check the permissions assigned directly to the folder in the **Folder Permissions** application.
**Overriding permissions**
There is an exception, however: language variants of items can override the permission settings of the web page item or content folder through configuring the fields of the **Member Permission Configuration** schema.
This is true for variants of both reusable items, which can override their containing folder’s permissions, and web pages, which can override their associated item’s permissions.
### How do you check permissions?
The _Member roles_ module provides new code that can filter unauthorized content from the current member. It’s important to note that **Xperience does not automatically apply this new logic** , so you need to adjust your code wherever you retrieve content that may be restricted by member role.
This process will be easier if you use some form of [abstraction](guides/development/get-started/use-abstraction-and-enterprise-level-architecture-in-xperience-projects) that centralizes your data access logic.
You can find details on how to check permissions for content in the [Developer guide](https://github.com/KenticoDevTrev/MembershipRoles_Temp/blob/master/DEVELOPER-GUIDE.md) of Trevor’s repository.
### Can you tie it in with authorization?
The _Member roles_ package includes logic to filter queries for content items based on member roles, but you can expand its utility further using another of Trevor’s tools.
The [XperienceCommunity.DevTools.Authorization.MemberRoles](https://github.com/KenticoDevTrev/KenticoAuthorization) package allows you to authorize things like controller actions and page templates based on member roles.
You can find specific instructions about its installation and usage in the link.
## How to help your business users
Many educational textbooks surrounding technology teach that information systems are made of five components: hardware, software, data, procedures, and people. Since the member roles module is a community-developed system that is not directly integrated into Xperience, it needs to rely a little more on procedures and people.
As with any new system or set of features, **it’s important to make sure your business users, such as editors and admins, understand the quirks of using _XperienceCommunity.MemberRoles_**.
The following list highlights the key points to cover:
  1. The package uses **inheritance**. Projects commonly set permissions at a high level in the content tree or Content hub folder structure, and allow child items and folders to inherit permissions with few-to-zero exceptions
     * Web pages and content folders can _inherit_ permission settings from their parents or _break inheritance_ to set their own permissions.
     * Individual _language and workflow versions_ of content items can _override_ the permission settings of their associated web page items or content folders.
  2. Not all types of permissions interact with **workflow and languages** in the same way.
     * Permission settings for a language version, set on the **Content** tab of content items are _language specific_ and _do not go into effect until the item is published_.
     * Permission settings from the **Properties** tab of web page items and the **Folder permissions** app are not language specific or subject to workflow, and _always go into effect immediately_.
  3. The **Member Permission Configuration** schema determines whether the package checks permissions for an item.
     * If a web page content type does not use the schema, any permissions applied to a web page item of that type _will not apply directly to the item_. Its children, however, may inherit the permissions.
     * If a reusable content type does not use the schema, any permissions applied to a folder containing items of this type _will not apply to those items_.
     * Simply assigning the schema to a content type in the admin UI _does not enable role permissions_ — a developer must _re-generate its code_.
  4. The code portion of this module does not account for the **Requires authentication** setting from the **Membership** properties of a page or reusable item. While using this module, editors should **ignore this property** , and instead _use the analogous custom properties_ in the following locations:
     * The **Folder Permissions** application for content folders
     * The **Member Permissions** properties category for web page items
     * The **Content** tab of a page for language versions


The [Admin guide](https://github.com/KenticoDevTrev/MembershipRoles_Temp/blob/master/ADMIN-GUIDE.md) of the repository explains these concerns, but we recommend having a discussion with all involved parties to reinforce the ideas verbally.
Business users are usually not required to read documentation of technical systems for their day-to-day tasks, so it helps to introduce these concepts and explain the importance before simply sending a link to the manual.
## Get started
Find instructions for installing and using the package in [the repository](https://github.com/KenticoDevTrev/MembershipRoles_Temp/tree/master) to include member roles in your project today.
The project is open-source, so you are free to fork and customize it to fit any of your specific needs.
If you encounter any problems, please report them through the [Issues tab](https://github.com/KenticoDevTrev/MembershipRoles_Temp/issues) of the repository.
If you have any ideas for functionality you’d like to see training materials about, please let us know with the _Send us feedback_ button below!
[ Previous page ](modules/members/expand-restricted-content-and-sign-in-functionality)
11 of 12
[ Mark complete and continue ](modules/members/members-learn-more)
  * [Community Questions & Answers](https://community.kentico.com/q-and-a)
  * [Contact support](https://community.kentico.com/support)


### Cookie consent
We use necessary [cookies](https://www.kentico.com/cookies-policy) to run our website and improve your experience while browsing. Additional cookies are only used with your consent. You may revoke your consent on the [Cookies Policy](https://www.kentico.com/cookies-policy) page or in your browser at any time. 
ACCEPT ALL  [Configure](https://www.kentico.com/cookies-policy)
USE ONLY NECESSARY 
![](https://docs.kentico.com/modules/members/apply-roles-to-your-members-community-package)
[](https://docs.kentico.com/modules/members/apply-roles-to-your-members-community-package)[](https://docs.kentico.com/modules/members/apply-roles-to-your-members-community-package)