# Work with roles
  * [ Copy page link ](guides/digital-marketing/xperience-essentials/work-with-roles-essentials#) | [Get HelpService ID](guides/digital-marketing/xperience-essentials/work-with-roles-essentials#) | This page is part of a module: [ User management essentials ](modules/user-management-essentials)
Core MVC 5


[✖](guides/digital-marketing/xperience-essentials/work-with-roles-essentials# "Close page link panel") [Copy to clipboard](guides/digital-marketing/xperience-essentials/work-with-roles-essentials#)
[![](docsassets/guides/work-with-roles-essentials/Role-management-icon.png)](https://docs.kentico.com/docsassets/guides/work-with-roles-essentials/Role-management-icon.png)
## Roles in Xperience
Roles help you define which parts of the system a user should or should not have access to. This part of the series will walk you through setting up roles in the **Role management** application and controlling access to different parts of the system, from creating and managing roles to assigning permissions and adding users to these roles.
## Create a new role
A group of users will do the same work in your application. You want to create a role and define which applications these users will have access to - and what actions they can perform. Start by setting up your user roles.
  1. In Xperience, go to the **Role management** application and create **New role**.
  2. Enter a **Role name** , and **Save** the changes.
  3. Switch to the **Permissions** tab and add permissions one by one.
    1. In the drop-down list, select the application you want your user to have access to.
    2. Choose which actions the user can perform.
    3. **Save** your changes.
  4. Go to the **Assigned users** tab and select the existing users you want to add to this role.
    1. To add an entirely new user, you’ll need to switch to the **Users** application. See below how you can [add a new user](guides/digital-marketing/xperience-essentials/work-with-users-essentials) to your system.
  5. **Save** the changes.


[![](docsassets/guides/work-with-roles-essentials/Role-general.png)](https://docs.kentico.com/docsassets/guides/work-with-roles-essentials/Role-general.png)
## Update permissions
When a user realizes they don’t have access to all the applications or actions they need, you need to update their role’s _permissions_.
  1. Go to the **Role management** application.
  2. Select the role you want to change.
  3. Switch to the **Permissions** tab and update the permissions.
    1. In the drop-down list, select the application you want your user to have access to and choose which actions the user can perform.
  4. **Save** your changes.


[![](docsassets/guides/work-with-roles-essentials/role-management-permissions.png)](https://docs.kentico.com/docsassets/guides/work-with-roles-essentials/role-management-permissions.png)
## Update a role
Suppose a digital marketing manager in your company complains that the _Channel Managers_ should not have the right to send an email. The emails must be approved and sent only by the company’s _Email Marketers_. To update the _Digital Channel Manager_ role, follow the steps below.
**Task** :
  1. Navigate to the **Role management** application.
  2. Open the _Digital Channel Manager_ role.
  3. Go to the **Permissions** tab on the left.
  4. Find the permissions for the **Marketing Emails** channel.
  5. Remove the **Send email** permission.
  6. **Save** your changes.


## Next step
Continue learning about [working with users](guides/digital-marketing/xperience-essentials/work-with-users-essentials).