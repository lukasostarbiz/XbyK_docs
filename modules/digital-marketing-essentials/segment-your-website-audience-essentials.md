# Segment your website audience
  * [ Copy page link ](modules/digital-marketing-essentials/segment-your-website-audience-essentials#) | [Get HelpService ID](modules/digital-marketing-essentials/segment-your-website-audience-essentials#)
Core MVC 5


[✖](modules/digital-marketing-essentials/segment-your-website-audience-essentials# "Close page link panel") [Copy to clipboard](modules/digital-marketing-essentials/segment-your-website-audience-essentials#)
You can use the contact’s demographic data, content preferences, or various website activities to shape their digital experience.
Xperience comes with the **Contact groups** application, where you **build specific segments** for which you can **create targeted content** and **improve** their **customer experience**. The _Contact groups_ application organizes existing contacts. Any individual contact can belong to any number of contact groups.
You can create your contact groups manually or use **built-in conditions** to populate them dynamically. In the latter case, Xperience **evaluates contacts** on the website and in the database and **automatically adds contacts to groups** based on the contact group’s conditions.
Here are a few examples of groups in which you can organize your contacts:
  * Contacts that are interested in specific content.
  * Contacts that submitted a specific form on your website.
  * Contacts to target in seasonal campaigns.
  * Contacts who came to a particular landing page.
  * Contacts with the same characteristics, e.g., those of the same age group.
  * And many more.


Find out more about [contact groups](documentation/business-users/digital-marketing/contact-groups) in the documentation.
Your browser does not support the video tag. 
## Exercise - Segmenting website audience
In this exercise, you’ll collect all contacts with similar characteristics into a contact group and test how Xperience dynamically adds contacts to a group and removes them once their conditions don’t match the group’s conditions.
**Task** :
You want to work with your website visitors more effectively, so you’ve decided to segment them into categories to better target your marketing efforts.
  1. Switch to the **Contact groups** application. Create a **New contact group** and call it _Prospective Account Clients_.
  2. Fill in the **Description** : _Visitors who read any of the Accounts subpages but haven’t filled in the Request account form_.
  3. Use the **Add condition** button to open the condition designer:
    1. **Add a new condition group** :
      1. Select _Contact has agreed with consent_ condition and select _Marketing cookies_ consent.
    2. **Add another condition** :
      1. Choose _Contact has submitted a form in last X days_ condition.
      2. Change it to **has not** and select the _request account (single)_ form.
    3. **Add condition group** to filter all contacts who have visited any of your _Accounts_ pages.
      1. Use the _Contact has visited a page in the last X days_ condition.
      2. **Add page** and choose _Personal Banking_ site from the dropdown menu.
      3. Continue and choose _Accounts_ in the content tree and _Current account_ from the available pages.
      4. Confirm with the **Select** button.
    4. **Add another condition** , and again, choose _Contact has visited a page in the last X days_ condition.
      1. **Add page** and, this time, select the _Accounts_ → _Advanced account_.
      2. Confirm with the **Select** button.
    5. **Add another condition** again and choose the _Contact has visited a page in the last X days_ condition.
      1. **Add page** and, this time, select the _Accounts_ → _Premium account_.
      2. Confirm with the **Select** button.
    6. Lastly, **Add another condition** again and choose _Contact has visited a page in the last X days_ condition.
      1. **Add page** and select the last page - _Accounts_ → _FastTrack savings account_.
      2. Confirm with the **Select** button.
    7. Change the logical operator between the second group of conditions - page visits. Change _Applies if:_ to **Any** _of the following conditions are fulfilled_. This way, your visitor needs to see just one page to be added to the contact group, not all of them.
    8. **Apply** the changes.
  4. **Save** your new contact group.


The conditions in the new Contact group should look like this:
[![](docsassets/guides/segment-your-website-audience-essentials/form-conditions.png)](https://docs.kentico.com/docsassets/guides/segment-your-website-audience-essentials/form-conditions.png)
You can _Recalculate_ your contact group. Xperience will filter the contact database and add every contact that meets the conditions to the group.
## Xperience it now: Dynamic contact segmentation in action
Open your website in an incognito window, agree with the tracking, and visit some pages, including one or more _Accounts_ subpages. To recognize your new contact in the **Contact management** application, submit a form, for example, on the _Contact_ page, but NOT on the _Accounts_ pages. Then check your _Prospective Account Clients_ contact group. You will see Xperience added your test contact.
Once you can see your contact in the group, you can further test how Xperience works with a contact once it doesn’t fulfill the condition anymore. Go to one of the _Accounts_ subpages and fill out the form. Go back to the Xperience administration and reload the contact group. After a few moments, you should be able to see your contact was removed.
[ Previous page ](modules/digital-marketing-essentials/collect-visitor-data-essentials)
4 of 7
[ Mark complete and continue ](modules/digital-marketing-essentials/personalize-website-content-essentials)
  * [Community Questions & Answers](https://community.kentico.com/q-and-a)
  * [Contact support](https://community.kentico.com/support)


### Cookie consent
We use necessary [cookies](https://www.kentico.com/cookies-policy) to run our website and improve your experience while browsing. Additional cookies are only used with your consent. You may revoke your consent on the [Cookies Policy](https://www.kentico.com/cookies-policy) page or in your browser at any time. 
ACCEPT ALL  [Configure](https://www.kentico.com/cookies-policy)
USE ONLY NECESSARY 
![](https://docs.kentico.com/modules/digital-marketing-essentials/segment-your-website-audience-essentials)
[](https://docs.kentico.com/modules/digital-marketing-essentials/segment-your-website-audience-essentials)[](https://docs.kentico.com/modules/digital-marketing-essentials/segment-your-website-audience-essentials)