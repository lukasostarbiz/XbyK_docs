# Personalize website content
  * [ Copy page link ](modules/digital-marketing-essentials/personalize-website-content-essentials#) | [Get HelpService ID](modules/digital-marketing-essentials/personalize-website-content-essentials#)
Core MVC 5


[✖](modules/digital-marketing-essentials/personalize-website-content-essentials# "Close page link panel") [Copy to clipboard](modules/digital-marketing-essentials/personalize-website-content-essentials#)
Marketing communication should never be a “one-size-fits-all” interaction. With the correct content served at the right time, you can **turn your visitors into loyal followers of your brands**.
Personalization allows you to **custom-tailor the content** for your website visitors. Xperience can dynamically recommend content visitors might find interesting based on their previous behavior.  
You can **personalize website content using page builder widgets** because you know what your audience segment is looking for. Once your developers prepare the widgets for personalization, you can start building contact groups and ensure that your **website content automatically changes** based on who interacts with it.
Here are a few ideas on how to use the widget personalization:
  * Change the content of the homepage based on a previous visitor’s activity.
  * Recommend related content based on customer preference.
  * Show/Hide content on the website based on the current time of the day or season.
  * And many more.


Find out more about the [widget personalization](documentation/business-users/digital-marketing/widget-personalization) in the documentation.
## Exercise - Personalizing website content
In this exercise, you’ll learn how to use content personalization to point potential partners to content they might find interesting. You’ll create and then use a contact group and personalize the website’s content.
**Task** :
Your marketing manager wants you to personalize the hero banner on your website’s home page to attract more visitors who have read the _kBank’s new Tiny Loans are cheapest in the Galaxy_ article but haven’t applied for a loan.
### Create a contact group
  1. Go to the **Contact groups** application.
  2. Create a **New contact group** and fill in: 
    1. **Name** : _Personal loan Hero banner_.
    2. **Description** : _Show a Personal loan hero banner on the Home page to visitors who read the “kBank’s new Tiny Loans are cheapest in the Galaxy” article but haven’t visited the Personal loan page and haven’t submitted any loan application form_.
    3. **Add condition** : 
      1. **Add condition group**.
      2. Select the _Contact has visited a page in last X days_ condition.
      3. **Add page** and select _News_ → _kBank’s new Tiny Loans are cheapest in the Galaxy_.
    4. **Add another condition** : 
      1. Again, select the _Contact has visited a page in the last X days_ condition.
      2. Change it to **has not**.
      3. **Add page** and select _Loans_ → _Personal loan_.
    5. **Add another condition** : 
      1. Select the _Contact has submitted a form in the last X days_ condition.
      2. Change it to **has not**.
      3. Pick the _Request account (2-column)_ form.
    6. And lastly, **Add another condition** : 
      1. Select the _Contact agreed with consent_ condition.
      2. Choose _Marketing cookies_.
    7. **Apply** the new conditions set.
  3. **Save** the contact group.


### Create a personalized variant of a widget
  1. In the **Personal Banking** application, navigate to the _Home page_.
  2. Select **Edit page** to move the page into the _Draft_ step.
  3. Select the **Personalize** button in the header of the _Hero banner_ widget. 
    1. Select the _Is in contact group_ condition type.
    2. Enter a **Variant name** : _kBank’s new Tiny Loans are the cheapest in the Galaxy, not Personal loan_.
    3. Select the _Personal loan Hero banner_ group from the list of contact groups.
    4. **Apply** to create the personalized variant.


### Adjust the content of the personalized widget
  1. Use the **Configure widget** button to personalize the widget’s content.
  2. To **Select hero content item** , choose **Select existing content item**. 
    1. Select the _loan_ item, and remove the _Kbank default banner_.
    2. Choose **Select** to confirm your choice.
  3. Add a **CTA** : _Learn more_.
  4. **Apply** your adjustments to the new banner.
  5. **Save** and **Publish** the new version of the _Home_ page.


### Test content personalization
Test how your new personalization works for a website visitor using the incognito window in your browser:
  1. Visit the _Home_ page of your website.
  2. Agree with tracking on the website.
  3. Visit some pages on the website and try browsing for at least half a minute. (This is a limitation of the training site. On your own website, activities will be logged immediately.) Visit the _kBank’s new Tiny Loans are the cheapest in the Galaxy_ article, as well as the _Accounts_ page and some of the pages with Kbank cards. Do NOT visit the _Personal loan_ page, and do NOT submit any form on the _loans_ pages.
  4. Return to the _Home_ page. Your personalized _Home_ page should look like the following:


[![](docsassets/guides/personalize-website-content-essentials/personal-loan-hero-banner.png)](https://docs.kentico.com/docsassets/guides/personalize-website-content-essentials/personal-loan-hero-banner.png)
[ Previous page ](modules/digital-marketing-essentials/segment-your-website-audience-essentials)
5 of 7
[ Mark complete and continue ](modules/digital-marketing-essentials/email-marketing-essentials)
  * [Community Questions & Answers](https://community.kentico.com/q-and-a)
  * [Contact support](https://community.kentico.com/support)


### Cookie consent
We use necessary [cookies](https://www.kentico.com/cookies-policy) to run our website and improve your experience while browsing. Additional cookies are only used with your consent. You may revoke your consent on the [Cookies Policy](https://www.kentico.com/cookies-policy) page or in your browser at any time. 
ACCEPT ALL  [Configure](https://www.kentico.com/cookies-policy)
USE ONLY NECESSARY 
![](https://docs.kentico.com/modules/digital-marketing-essentials/personalize-website-content-essentials)
[](https://docs.kentico.com/modules/digital-marketing-essentials/personalize-website-content-essentials)[](https://docs.kentico.com/modules/digital-marketing-essentials/personalize-website-content-essentials)