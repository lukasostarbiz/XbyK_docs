---
source: https://docs.kentico.com/modules/contact-management/disable-or-modify-automation
scrape_date: 2026-01-22
---

Module: Contact management
40 of 44 Pages
# Disable or modify an automation process
**Advanced license required**   
  
Features described on this page require the Xperience by Kentico **Advanced** license tier. 
To disable a process:
  1. Open the **Automation** application.
  2. Select the process you wish to disable from the list.
  3. On the **Automation Builder** view, select **Disable new triggers**.


The process will no longer trigger for new contacts. However, contacts already within the process will continue moving through steps until the process completes. This includes contacts in _Wait_ steps, so a process may perform actions (e.g., send emails) hours or even days after being disabled.
## Modify an automation process with active contacts
Versioning is currently not available for automation processes. If any steps are edited while contacts are actively progressing through the process, the contacts will follow the updated sequence of steps.
You cannot delete steps if the process is currently enabled, or has been enabled in the past and has logged contact statistics. At this point, there may be contacts progressing through the process, and deleting steps could result in unexpected behavior.
If you delete the entire automation process, the contact history is fully cleared, and no further steps will be performed for any contacts currently within the process.
If a contact reaches the last step in a process branch, which is not connected to another step (whether it is a **Finish** step or not), the process “finishes” and the contact becomes “stuck” in the given step. Even if you add another step to an enabled process later, the contact will **not continue**.
[ Previous page ](/modules/contact-management/analyze-and-customize-automation)
40 of 44
[ Mark complete and continue ](/modules/contact-management/email-scenarios-forms)
![]()
[]()[]()
