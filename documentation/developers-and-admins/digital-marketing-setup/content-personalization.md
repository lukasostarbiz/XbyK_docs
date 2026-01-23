---
source: https://docs.kentico.com/documentation/developers-and-admins/digital-marketing-setup/content-personalization
scrape_date: 2026-01-22
---

  * [Home](/documentation)
  * [Developers and admins](/documentation/developers-and-admins)
  * [Digital marketing setup](/documentation/developers-and-admins/digital-marketing-setup)
  * Content personalization 


# Content personalization
Content personalization is digital marketing technique that creates pages with different content depending on the circumstances in which they are viewed. For example, you can build pages that offer special content for different types of visitors or dynamically change for each user according to the actions they performed on the website.
When developing websites, you can use the following different approaches to create personalized content:
  * [Allow marketers to personalize content added through Page Builder widgets](#set-up-personalization-of-page-builder-widgets)
  * [Create personalization conditions directly in the code of your site’s pages](#add-personalization-conditions-to-page-code)


## Set up personalization of Page Builder widgets
  1. [Set up Page Builder](/documentation/developers-and-admins/development/builders/page-builder) for your project.
  2. Prepare pages with [editable areas for Page Builder](/documentation/developers-and-admins/development/builders/page-builder/create-pages-with-editable-areas).
  3. Develop [widgets](/documentation/developers-and-admins/development/builders/page-builder/widgets-for-page-builder) and [sections](/documentation/developers-and-admins/development/builders/page-builder/sections-for-page-builder) that allow website editors to add the required content.
  4. Develop [personalization condition types](/documentation/developers-and-admins/digital-marketing-setup/content-personalization/develop-personalization-condition-types).


Marketers can now [personalize widgets](/documentation/business-users/digital-marketing/widget-personalization) on pages where Page Builder is enabled, using the condition types that you developed.
## Add personalization conditions to page code
In addition to personalization of Page Builder widgets, you can write personalization conditions directly within the code of your project’s components, controllers, views or other classes. The conditions can leverage the visitor segmentation tools that marketers define within Xperience, such as [Contact groups](/documentation/business-users/digital-marketing/contact-groups).
**Prerequisite**
Set up [tracking of contacts](/documentation/developers-and-admins/digital-marketing-setup/contact-configuration) before defining personalization conditions based on contact groups or other contact properties.
### Personalize content based on contact groups
We recommend using [Contact groups](/documentation/business-users/digital-marketing/contact-groups) to personalize your project’s content:
  1. Define contact groups to segment your website’s visitors (in the **Contact groups** application within Xperience).
  2. Use the contact tracking API to get the current contact in your project’s code.
  3. Create conditions to display different content for contacts who belong to different contact groups.
  4. Call one of the following extension methods for the current contact object within the conditions:
     * `IsInContactGroup` – evaluates whether the contact belongs to one specific contact group.
     * `IsInAnyContactGroup` – evaluates whether the contact belongs to _at least one_ of the specified contact groups.
     * `IsInAllContactGroups` – evaluates whether the contact belongs to _all_ of the specified contact groups.
All of the methods return a boolean value that you can use in your content conditions.
The methods accept one or more string parameters, which must match the **code names** of contact groups. To find the code names, view contact groups in the _Contact groups_ application in Xperience.


The system segments your website’s visitors into contact groups, for example according to [performed activities](/documentation/developers-and-admins/digital-marketing-setup/set-up-activities). Your site then displays personalized content depending on the contact groups to which the current visitor (contact) belongs.
cshtml
**Razor view example**
Copy
```
@using CMS.ContactManagement

@{
    // Gets the current contact
    ContactInfo currentContact = ContactManagementContext.GetCurrentContact();
}

@* Displays personalized content for contacts belonging to the "YoungCustomers" contact group *@
@if (currentContact != null && currentContact.IsInContactGroup("YoungCustomers"))
{
    <text>Hiya @currentContact.ContactFirstName</text>
}
else
{
    <text>Hello</text>
}
```

## Output caching on personalized pages
When using output caching to improve the performance of your website, you need to take additional steps to ensure that the output cache correctly stores personalized pages. Because personalized pages serve different content based on various conditions, you need to adjust your application to cache separate output versions according to the personalization criteria.
See [Output caching](/documentation/developers-and-admins/development/caching/output-caching) for more information.
![]()
[]()[]()
