---
source: https://docs.kentico.com/modules/developer-kickstart/add-contact-us-page
scrape_date: 2026-01-22
---

Module: Xperience by Kentico Developer kickstart
10 of 13 Pages
# Add a Contact us page
Let’s implement a _Contact us_ page. We want it to display a simple form and our slogan message, the same as on the _Home page_.
Therefore the page is a perfect candidate for reusing our existing _Landing page_ template and _Welcome slogan_ reusable content item.
[![Future ‘Contact us’ page](/docsassets/guides/add-contact-us-page/contact-us-page.png)](/docsassets/guides/add-contact-us-page/contact-us-page.png)
## Create the page in the administration UI
Just like with [_Home page_ before](/modules/developer-kickstart/apply-a-page-template), navigate to the **Kickstart pages** channel in the administration UI and create a new page:
  * **Page name:** Contact us
  * **Content type:** Landing page


Set the Slogan. This time, you can simply use the **Select existing** and pick the _Welcome slogan_ you created before.
[![Selecting existing content item](/docsassets/guides/add-contact-us-page/select-existing-content-item.png)](/docsassets/guides/add-contact-us-page/select-existing-content-item.png)
Now, when you publish your page and look at the **Preview** , you’ll see it shows the same Welcome slogan text as the Home page.
The first part is done. Next, you need the form.
## Build the form
In your Xperience administration, navigate to **Digital marketing → Forms → New form** to open a [Form Builder](/documentation/developers-and-admins/development/builders/form-builder) .
Use the plus icon in the middle to add two fields:
  * **Email**
  * **Leave a message**


The changes will save automatically.
[![Adding fields using Form Builder](/docsassets/guides/add-contact-us-page/form-builder-add-fields.png)](/docsassets/guides/add-contact-us-page/form-builder-add-fields.png)
Switch to the **General** tab on the right side. Adjust the Form name and Code name, and **Save** :
  * **Form name** : Contact us
  * **Code name** : ContactUs


Your _Contact us_ form should look like this:
[![New Contact us form](/docsassets/guides/add-contact-us-page/form-builder-ready.png)](/docsassets/guides/add-contact-us-page/form-builder-ready.png)
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
[![Configure widget properties](/docsassets/guides/add-contact-us-page/configure-widget.png)](/docsassets/guides/add-contact-us-page/configure-widget.png)
Fill out the following:
  * **Form properties → Select form** : Select your new _Contact us_ form.
  * **After form submission → Display a message** : Thanks! We’ll get back to you soon!


Click **Apply**.
**Publish** your page.
## Check your progress
Navigate to the root of your website and add _/contact-us_ to the URL. You should be able to see the new _Contact us_ page and submit the form.
Your browser does not support the video tag. 
Now that your website has two pages, let’s implement a simple navigation so visitors can easily toggle between them.
[ Previous page ](/modules/developer-kickstart/create-a-layout-view)
10 of 13
[ Mark complete and continue ](/modules/developer-kickstart/model-navigation)
![]()
[]()[]()
