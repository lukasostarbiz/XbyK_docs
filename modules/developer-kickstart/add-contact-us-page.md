# Add a Contact us page
  * [ Copy page link ](modules/developer-kickstart/add-contact-us-page#) | [Get HelpService ID](modules/developer-kickstart/add-contact-us-page#)
Core MVC 5


[✖](modules/developer-kickstart/add-contact-us-page# "Close page link panel") [Copy to clipboard](modules/developer-kickstart/add-contact-us-page#)
Let’s implement a _Contact us_ page. We want it to display a simple form and our slogan message, the same as on the _Home page_.
Therefore the page is a perfect candidate for reusing our existing _Landing page_ template and _Welcome slogan_ reusable content item.
[![Future ‘Contact us’ page](docsassets/guides/add-contact-us-page/contact-us-page.png)](https://docs.kentico.com/docsassets/guides/add-contact-us-page/contact-us-page.png)
## Create the page in the administration UI
Just like with [_Home page_ before](modules/developer-kickstart/apply-a-page-template), navigate to the **Kickstart pages** channel in the administration UI and create a new page:
  * **Page name:** Contact us
  * **Content type:** Landing page


Set the Slogan. This time, you can simply use the **Select existing** and pick the _Welcome slogan_ you created before.
[![Selecting existing content item](docsassets/guides/add-contact-us-page/select-existing-content-item.png)](https://docs.kentico.com/docsassets/guides/add-contact-us-page/select-existing-content-item.png)
Now, when you publish your page and look at the **Preview** , you’ll see it shows the same Welcome slogan text as the Home page.
The first part is done. Next, you need the form.
## Build the form
In your Xperience administration, navigate to **Digital marketing → Forms → New form** to open a [Form Builder](documentation/developers-and-admins/development/builders/form-builder) .
Use the plus icon in the middle to add two fields:
  * **Email**
  * **Leave a message**


The changes will save automatically.
[![Adding fields using Form Builder](docsassets/guides/add-contact-us-page/form-builder-add-fields.png)](https://docs.kentico.com/docsassets/guides/add-contact-us-page/form-builder-add-fields.png)
Switch to the **General** tab on the right side. Adjust the Form name and Code name, and **Save** :
  * **Form name** : Contact us
  * **Code name** : ContactUs


Your _Contact us_ form should look like this:
[![New Contact us form](docsassets/guides/add-contact-us-page/form-builder-ready.png)](https://docs.kentico.com/docsassets/guides/add-contact-us-page/form-builder-ready.png)
## Use Page Builder to show the form and a custom message
Navigate to your **Kickstart pages** channel again and pick the _Contact us_ page from the page tree on the left side.
Switch to the **Page Builder** view and click **Edit page** in the top-right corner.
Use the Page Builder area to add a Rich text widget, just like in the _Home page_. Enter the following text:
HTML
Copy
```
Need to send us a message?

Use this form, and we'll get back to you at the provided email address!
```

Click the plus icon again to add a **Form** widget. Then, configure the widget’s properties using the gear icon in the top-right corner.
[![Configure widget properties](docsassets/guides/add-contact-us-page/configure-widget.png)](https://docs.kentico.com/docsassets/guides/add-contact-us-page/configure-widget.png)
Fill out the following:
  * **Form properties → Select form** : Select your new _Contact us_ form.
  * **After form submission → Display a message** : Thanks! We’ll get back to you soon!


Click **Apply**.
**Publish** your page.
## Check your progress
Navigate to the root of your website and add _/contact-us_ to the URL. You should be able to see the new _Contact us_ page and submit the form.
Your browser does not support the video tag. 
Now that your website has two pages, let’s implement a simple navigation so visitors can easily toggle between them.
[ Previous page ](modules/developer-kickstart/create-a-layout-view)
10 of 13
[ Mark complete and continue ](modules/developer-kickstart/model-navigation)
  * [Community Questions & Answers](https://community.kentico.com/q-and-a)
  * [Contact support](https://community.kentico.com/support)


### Cookie consent
We use necessary [cookies](https://www.kentico.com/cookies-policy) to run our website and improve your experience while browsing. Additional cookies are only used with your consent. You may revoke your consent on the [Cookies Policy](https://www.kentico.com/cookies-policy) page or in your browser at any time. 
ACCEPT ALL  [Configure](https://www.kentico.com/cookies-policy)
USE ONLY NECESSARY 
![](https://docs.kentico.com/modules/developer-kickstart/add-contact-us-page)
[](https://docs.kentico.com/modules/developer-kickstart/add-contact-us-page)[](https://docs.kentico.com/modules/developer-kickstart/add-contact-us-page)