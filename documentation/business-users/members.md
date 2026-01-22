---
source: https://docs.kentico.com/documentation/business-users/members
scrape_date: 2026-01-22
---

  * [Home](/documentation)
  * [Business users](/documentation/business-users)
  * Members 


# Members
Visitors who register an account in the system are referred to as _members_. Unlike _Users_ who can access the admin UI, members can access only the front-facing part of an application, such as a website. They can browse pages or access member-only sections of the website.
Member accounts are managed using the **Members** application. The application’s home page lists all member accounts in the system.
[![The Members application displays members registered in the system](/docsassets/documentation/members/MembersList.png)](/docsassets/documentation/members/MembersList.png)
The **Members** application only displays visitor accounts registered in the system. If the application is empty, either your project has no registered accounts, or it doesn’t support [Registration and authentication](/documentation/developers-and-admins/development/registration-and-authentication).
## View member details
Select a member from the listing to view their account details.
  * **Member name** – the name used by the member to sign in. Modifying this value may **change** the sign-in credentials for the member, depending on your site’s implementation.
  * **Email** – the email used by the member to sign in. Modifying this value may **change** the sign-in credentials for the member, depending on your site’s implementation.
  * **Enabled** – determines whether the member account is allowed to sign in.


[![Member detail view](/docsassets/documentation/members/MemberDetail.png)](/docsassets/documentation/members/MemberDetail.png)
### External user accounts
Users that create an account using an external provider (e.g., Google or Facebook) cannot be edited via the _Members_ application. 
## Disable or delete members
**Disabling** a member account prevents visitors from signing in with the associated credentials. However, the account stays in the system and can be reactivated later.
To disable an account:
  1. In the member listing, select the additional actions menu (…).
  2. Select **Disable**.


The member account is now disabled and cannot be used to sign in.
**Deleting** a member account removes it from the system completely. It cannot be used to sign in again unless the visitor registers a new account.
To delete an account:
  1. In the member listing, select the **Delete** action.
  2. Confirm the deletion.


The member account is now deleted.
![]()
[]()[]()
