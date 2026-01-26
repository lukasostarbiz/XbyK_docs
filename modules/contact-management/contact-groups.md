---
source: https://docs.kentico.com/modules/contact-management/contact-groups
scrape_date: 2026-01-26
---

Module: Contact management
9 of 44 Pages
# Contact groups
**Contact groups** are segments that allow you to organize [contacts](/modules/contact-management/contact-management). Segmentation divides contacts into groups of people with shared characteristics, which you can then accurately target in your marketing efforts. For example, you can use contact groups to filter recipients when [sending regular emails](/documentation/business-users/digital-marketing/emails/send-regular-emails-to-subscribers).
The system assigns contacts to groups automatically based on dynamic conditions. A single contact can belong to any number of different contact groups.
**Relationship with channels**
Contact groups are global and shared across all [channels](/documentation/developers-and-admins/configuration/website-channel-management) (e.g., website, email, headless).
**Merged contacts**
For contacts that have been [merged](/modules/contact-management/contact-management) by the system, only the parent contacts are assigned to contact groups.
You can create and manage contact groups in the **Contact groups** application of the administration.
[![Viewing the list of contact groups](/docsassets/documentation/contact-groups/Contact_Group_List.png)](/docsassets/documentation/contact-groups/Contact_Group_List.png)
Select **New contact group** to add a contact group.
## Set up dynamic contact groups
Dynamic contact groups automatically gather all contacts who fulfill certain criteria. You define the criteria through conditions.
  1. Open the **Contact groups** application.
  2. Select the desired contact group in the list.
  3. On the **General** tab, select **Add** or **Edit** in the **Conditions** area.
  4. Select **Add condition group**.
  5. Pick a condition.
     * Use the search or select categories to find the appropriate condition.  
[![Conditions for building contact groups](/docsassets/documentation/contact-groups/Contact_Group_Conditions.png)](/docsassets/documentation/contact-groups/Contact_Group_Conditions.png)
  6. Fill in any required parameters for the condition.
  7. Repeat the process to add any number of conditions.
     * Conditions are organized together within condition groups.
     * Select **Add condition group** to create multiple groups. A selector appears above the groups where you can choose if **Any** or **All** of the condition groups need to be fulfilled.
     * To add individual conditions to a group, select **Add another condition**.
     * For each group, you can select if **Any** or **All** of the conditions need to be fulfilled.
  8. Select **Save**.


[![Building a condition for a dynamic contact group](/docsassets/documentation/contact-groups/Condition_Builder.png)](/docsassets/documentation/contact-groups/Condition_Builder.png)
You can find all contacts added through the overall condition on the group’s **Contacts** tab.
### Recalculate contact groups
When a contact is created or updated, the **system automatically** checks if the contact fulfills the conditions of contact groups and adds or removes the contact accordingly.
However, if you modify a group’s condition, you need to recalculate it to ensure that the group only contains contacts that fulfill the new condition.
To manually run the recalculation, select **Recalculate contact group** on the group’s **General** tab. This option appears only if the group has not yet been recalculated after an update of the condition.
[![Recalculating a contact group after updating its condition](/docsassets/documentation/contact-groups/Contact_Group_Recalc.png)](/docsassets/documentation/contact-groups/Contact_Group_Recalc.png)
**Time the recalculation**
If your application is hosting a live website, do not run the recalculation during the site’s peak traffic hours. Contact group recalculation can be a resource-intensive process that may reduce the application’s performance.
**Schedule automatic recalculation**
If you frequently change a group’s condition, or regularly perform bulk imports of contacts (e.g., via custom code or database scripts), you can schedule automatic recalculation for specific contact groups:
  1. Open the **Contact groups** application.
  2. Select a contact group in the list.
  3. On the **Overview** tab, select **Edit** under the group’s name.
  4. Enable the **Schedule recalculation** property.
  5. Select **Save**.


When you enable this option for a contact group, the system randomly selects a time between 2 AM and 6 AM (in the time zone of the server where the application is running). The group is then recalculated automatically once per day at this time.
The goal of this approach is to run recalculations during off-peak hours, and avoid recalculating multiple groups at the same time.
However, keep in mind that scheduled recalculation is not required in most scenarios. When contacts are updated or created based on natural website activity, the system automatically evaluates contact groups conditions and adds or removes the contacts accordingly.
[ Previous page ](/modules/contact-management/segmenting-anonymous-contacts)
9 of 44
[ Mark complete and continue ](/modules/contact-management/create-a-new-contact-group)
![]()
[]()[]()
