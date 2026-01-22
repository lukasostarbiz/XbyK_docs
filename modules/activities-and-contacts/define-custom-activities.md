---
source: https://docs.kentico.com/modules/activities-and-contacts/define-custom-activities
scrape_date: 2026-01-22
---

Module: Activities and contacts
3 of 9 Pages
# Define custom activities in Xperience
You may encounter use cases where you need to track visitor actions not covered by out-of-box options, or where you need to track additional details. Xperience allows you to define custom activities, which can then be logged through both client-side and server-side code.
Let’s dive into the process of logging a custom activity when a visitor downloads a file using JavaScript, and logging a custom activity when a user clicks a like button using C#.
## Create custom activity types
The first step to logging custom activities is to create the activity types that represent them.
In the Xperience administration interface, navigate to the **Activity types** tab of the **Contact management** application and define activity types with the following values.
  * Activity 1: 
    * **Display name** : File Download
    * **Code name** : filedownload
    * **Description** : The visitor downloaded a file.
    * **Enabled:** True
  * Activity 2: 
    * **Display name** : Page like
    * **Code name** : pagelike
    * **Description** : The visitor clicked a like button on a page.
    * **Enabled:** True


Let’s explore two different approaches for logging custom activities, using one for each of the types we just defined.
[ Previous page ](/modules/activities-and-contacts/enable-activity-tracking)
3 of 9
[ Mark complete and continue ](/modules/activities-and-contacts/log-custom-activities-client-side)
![]()
[]()[]()
