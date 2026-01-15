# Create a new role
  * Tutorial| [ Copy page link ](guides/configuration/work-with-users-and-roles/work-with-roles/create-new-role#) | [Get HelpService ID](guides/configuration/work-with-users-and-roles/work-with-roles/create-new-role#) | This page is part of a module: [ User management ](modules/user-management)
Core MVC 5


[✖](guides/configuration/work-with-users-and-roles/work-with-roles/create-new-role# "Close page link panel") [Copy to clipboard](guides/configuration/work-with-users-and-roles/work-with-roles/create-new-role#)
Let’s create a **Content editor** role with specific permissions for a user that works on managing website content.
  1. Before you create a new role in Kentico, put together a list of activities users with this role should be able to perform. Consider the applications they need and the actions they must perform in each application.
  2. Go to the **Role management** application.
  3. Add a **New role** and fill in the following information:
    1. **Display name** : e.g., _Content Editor,_
    2. **Code name** : e.g.,  _ContentEditor,_
    3. **Description** : e.g., _“A role with access to website channels (e.g. Business Banking channel) and Content hub. Responsible for creating, managing, updating, and curating the website’s content.”_.
  4. **Save** to create the role.
  5. On the left, switch to the **Permissions** tab.
  6. For each application that the user needs to work with, select **Add permission set** and choose the rights for the application and wokspace. For example:
    1. **Content hub** : 
      1. _View_ , _Create_ , _Update_ , _Delete,_
      2. **In** worskpace: **Business Banking**
    2. **Business Banking** channel: _Access channel_
  7. **Save** your changes.
  8. Go to the **Assigned users** tab and select the existing users you want to add to this role. If you need to add a new user, use the **Users** application.
  9. **Save** the changes.
  10. In this example, the _Content Editor_ role should look like the following:


[![Content editor role settings](docsassets/guides/create-new-role/content-editor-role-settings.png)](https://docs.kentico.com/docsassets/guides/create-new-role/content-editor-role-settings.png)