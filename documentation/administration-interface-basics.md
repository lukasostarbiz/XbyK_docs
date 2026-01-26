---
source: https://docs.kentico.com/documentation/administration-interface-basics
scrape_date: 2026-01-26
---

  * [Home](/documentation)
  * Administration interface basics 


# Administration interface basics
You can access the Xperience by Kentico administration using your browser:
  1. In your browser, navigate to the sign-in page: **https:// <domain>/admin** (for example: **https://localhost/admin**)
  2. Enter your credentials into the sign-in dialog.
**Default administrator account**
The default account is **administrator** with the password specified during the [installation process](/documentation/developers-and-admins/installation).
  3. Once you sign in, the Xperience administration opens.  
[![Administration interface dashboard](/docsassets/documentation/administration-interface-basics/admin_homepage.png)](/docsassets/documentation/administration-interface-basics/admin_homepage.png)


## Xperience administration interface
The system takes you to Xperience’s administration dashboard when you sign in. The dashboard consists of tiles representing various applications you will use to perform your daily tasks. No matter where you are in the administration interface, you can use the following elements to navigate around Xperience.
[![Administration interface controls overview](/docsassets/documentation/administration-interface-basics/admin_dashboard.png)](/docsassets/documentation/administration-interface-basics/admin_dashboard.png)
**Application list** |  The menu on the left side gives you access to all Xperience applications. The applications are conveniently grouped into dedicated areas according to their function, e.g., **Digital marketing** or **Configuration**. **Actions:**
  * Press **Enter** to open the selected application group or application. (**Tip:** Use **keyboard shortcut F2** to open the application list.)
  * **Search box** : search for applications inside Xperience.

  
---|---  
**Channels** |  **Channels** is a section of the application list, which allows you to open various channel applications. Channel applications are created dynamically whenever a new channel (website or email) is created. By default, the name of the channel application corresponds with the display name of the channel.  
**Home button** |  Returns you to the application dashboard.  
**Breadcrumbs** |  Displays the current application and your location within the application. You can click on items in the breadcrumbs to navigate back within the Xperience structure.  
**Product information** |  Displays the name and the current version of the product.  
**User menu** |  Expands a user menu where you can adjust the settings and preferences of your user account. Actions:
  * Account: You can change settings, such as username, password, or contact email.
  * Sign out: Use it to **Sign out** of the administration. Signing out takes you back to the sign-in page.

  
**Do you need to open applications quickly?**
To speed up your navigation around the application, use the **F2 key** on the keyboard to open the application list.
**Do you prefer searching through the application list?**
If the application has more than one word in its name, use each word’s initial to find what you need quickly. For example, searching for “ _c h_ ” lists the _Content hub_ application.
**Do you need to work with more than one application?**
You can open any number of applications simultaneously in different browser tabs. Simply open Xperience administration in a new tab and select the application you need.
**Do you work with the same applications all the time?**
Improve your workflow and bookmark the applications you work with in your browser. You’ll have quick access to any application straight from your bookmarks bar.
**Supported browsers**
Xperience by Kentico supports the latest major versions of the following browsers: 
  * Chrome
  * Firefox
  * Microsoft Edge
  * Safari on Mac OS


See other [browser requirements](/documentation/developers-and-admins/installation/system-requirements).
## Applications
The Xperience platform is split into different **_applications_**.
Applications group functionality and can be used to accomplish various tasks. For example, you segment your audience in **Contact groups** , manage user accounts in **Users** , or view various information in the **Event log**. 
## Administration interface URL structure
You can open applications from the dashboard or application list and bookmark the URLs.
The URL of the Xperience administration interface consists of the following parts:
  * Base URL: `<domain>/admin/` (opens the administration dashboard itself).
  * Applications in URL: `<domain>/admin/<application name>/<application-specific parameter>`. 
    * E.g., the Forms application’s URL contains the `/forms` application-specific parameter, while the Contact management application’s URL contains the `/contacts` parameter.


## Time in the administration
All time values in the administration are displayed in the local time zone of each user. For example: _01:30 PM (GMT+02:00)_
The time zone is detected according to the browser and environment settings of the user’s device.
![]()
[]()[]()
