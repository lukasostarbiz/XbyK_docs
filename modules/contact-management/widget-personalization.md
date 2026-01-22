---
source: https://docs.kentico.com/modules/contact-management/widget-personalization
scrape_date: 2026-01-22
---

Module: Contact management
35 of 44 Pages
# Personalize Page Builder widgets
**Advanced license required**   
  
Features described on this page require the Xperience by Kentico **Advanced** license tier. 
Personalization is a digital marketing technique that allows you to display different content on pages based on the situation in which they are viewed. This can help you provide more accurately targeted information to visitors and increase the effectiveness of your websites. For example, you can show modified text or additional offers to visitors who belong to a specific [contact group](/modules/contact-management/contact-groups).
You can personalize content that is displayed through [Page Builder widgets](/documentation/business-users/website-content/widgets-and-page-builder). Individual widgets allow you to create multiple variants, which are then displayed to a different audience based on predefined conditions.
**Prerequisite**
Content personalization of widgets needs to be configured by your developers. For more information, see:
  * [Content personalization](/documentation/developers-and-admins/digital-marketing-setup/content-personalization)
  * [Develop personalization condition types](/documentation/developers-and-admins/digital-marketing-setup/content-personalization/develop-personalization-condition-types)


## Personalize widgets
To personalize widget content, you need to create one or more personalization variants and set up a condition. The condition decides whether the page displays the original widget or a personalized variant.
  1. In a website channel application, select a page that contains [Page Builder widgets](/documentation/business-users/website-content/widgets-and-page-builder).
  2. Select **Page Builder** in the page menu on the right side of the interface.
  3. Select the widget you want to personalize.
  4. Select the **Personalize** (
  5. Choose the condition type that you wish to use to personalize the widget.  
[![Selecting a personalization condition type](/docsassets/documentation/widget-personalization/Personalize_Button.png)](/docsassets/documentation/widget-personalization/Personalize_Button.png)
  6. Fill in the configuration dialog and enter a name for the new variant.  
[![Filling in the configuration dialog for a personalization variant](/docsassets/documentation/widget-personalization/Add_Personalization_Variant.png)](/docsassets/documentation/widget-personalization/Add_Personalization_Variant.png)
  7. Select **Apply** within the dialog.
  8. Modify the properties or content of the new widget variant. Consider the target audience of the variant’s condition when preparing the modified content.
  9. **Save** the page to confirm the personalization.


The new personalization variant of your widget is now created. In the Page Builder edit mode, you can switch between personalization variants of a widget by selecting the **Personalize** (
When a visitor views the page on the live site, the system evaluates the condition and displays the appropriate variant.
**Configure widget personalization variants**
The available personalization condition types, their properties in the configuration dialog, and the properties of widgets all depend on the implementation of your website. For more information, visit your project’s documentation or contact your developer.
**Search engine web crawlers**
Widget personalization in Xperience does not offer any special handling of search engine web crawlers. Crawlers are treated as regular anonymous visitors. To avoid [cloaking](https://developers.google.com/search/docs/advanced/guidelines/cloaking) (presenting content to crawlers which is different than content seen by regular users), we recommend using personalization conditions based on [contact](/modules/contact-management/contact-management) attributes. Such conditions are not fulfilled by crawlers, so they always receive and process the original widget variant.
However, certain conditions (e.g., time-based conditions depending on the day of the week) can be satisfied by crawlers and result in non-original variants being presented.
## Create additional variants
You can add variants to a personalized widget. All variants of a single widget use the **same type of personalization condition** , but you can configure different properties for the condition of each variant. For example, if a condition type requires visitors to belong to specific contact groups, you select different groups for each personalization variant.
  1. Select **Personalize** (
  2. Select **Add variant** under the list.  
[![Adding a personalization variant](/docsassets/documentation/widget-personalization/Variant_List.png)](/docsassets/documentation/widget-personalization/Variant_List.png)
  3. Fill in the configuration dialog and enter a name for the new variant.
  4. Select **Apply** within the dialog.
  5. Modify the properties or content of the new widget variant.
  6. **Save** the page to confirm the creation of the variant.


The new variant is created as a copy of the previously selected variant. The new variant is placed on top of the variant list while the original variant stays at the bottom.
**Priority of variants**
The variants are evaluated from top to bottom according to their position in the variant list. The first variant for which the conditions are satisfied is displayed.
**Recommended number of variants**
You can create an unlimited number of variants for a single widget. However, for optimal displaying of the variant list, we do not recommend creating more than **10 variants** for each widget.
## Edit variants
You can edit the properties of your widget variants even after they are created. To edit a variant:
  1. Select **Personalize** (
  2. Hover over the variant you want to edit.
  3. Select **Edit variant** (
  4. Edit the desired properties and select **Apply** within the dialog.
  5. **Save** the page to confirm the changes.


## Change the priority of variants
You can reorder personalization variants in order to change their priority of evaluation. Variants are evaluated from top to bottom, i.e. the first variant in the list for which the conditions are satisfied is displayed. To reorder variants:
  1. Select **Personalize** (
  2. Drag and drop the variants using the **Change priority** handle on the left of the list.
  3. **Save** the page to confirm the changes.


## Delete variants
To delete a personalization variant for a widget:
  1. Select **Personalize** (
  2. Hover over the variant you want to delete.
  3. Select **Delete variant** (
  4. **Save** the page to confirm the change.


[ Previous page ](/modules/contact-management/widget-personalization-overview)
35 of 44
[ Mark complete and continue ](/modules/contact-management/members-application-overview)
![]()
[]()[]()
