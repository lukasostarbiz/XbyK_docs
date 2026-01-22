---
source: https://docs.kentico.com/guides/digital-marketing/xperience-essentials/collect-visitor-data-essentials
scrape_date: 2026-01-22
---

  * [Home](/guides)
  * [Digital marketing](/guides/digital-marketing)
  * [Xperience essentials](/guides/digital-marketing/xperience-essentials)
  * Collect visitor data 


# Collect visitor data
Xperience’s built-in **Forms** application takes your contact management to the next level. The online forms you build in the application help you **collect valuable visitor information**. And since the application is a part of Xperience, you can **convert collected data into actionable insights** and use it to segment your contacts or trigger personalized customer journeys.
See how you can work with [forms](/documentation/business-users/digital-marketing/forms) in Xperience by Kentico.
Here are a few ideas for website forms to collect valuable customer data:
  * Request additional information through a contact form.
  * Provide visitors access to gated content after form submission.
  * Give users a way to set up preferences.
  * And many more.


Find out more about Xperience [forms](/documentation/developers-and-admins/development/builders/form-builder) in the documentation.
Your browser does not support the video tag. 
## Exercise - Collecting visitor data
In the following exercise, you’ll learn how to create and display a form on the website and use it to collect contact data.
**Task** :
Your content manager discovered a page without a form. They asked you to create one and add it to the _Loans_ page.
### Create your form
  1. In Xperience by Kentico, navigate to the **Forms** application and create a **New form**.
  2. Use the **Add field** button in the middle of the Form builder and add the following fields to your form:
    1. _First name_.
    2. _Last name_.
    3. _Email_.
    4. _Business phone_.
    5. _Leave a message_.
  3. You want to adjust the _Business phone_ label to just _Phone_ since the promoted loans are for personal usage, and you want to avoid potential confusion.
    1. Select the **Configure** button for the _Business phone_ field and change the **Label** to _Phone_.
    2. **Apply** the change.
  4. Adjust the _Leave a message_ as well:
    1. Select the **Configure** button for the message field and change the **Label** : _Let us know about your financial needs_.
    2. Add **Explanation text** : _Share any specifications or ideas with us so we can tailor the loan offer to your needs_.
    3. **Apply** to save the changes.
You can use **validation rules** to make sure people enter the **right kind of information** in your form fields. For example, you can limit the number of characters in a text field or set a maximum value for numbers. Keep in mind: Validation rules don’t apply to empty fields. If you want to make sure a field is filled in, mark it as _Required_ in the field’s properties.
Interested? Learn more about the available [form fields validations](/documentation/business-users/digital-marketing/forms/create-and-edit-forms#field-validation). 
  5. Make the _Email_ field and any other fields required. Select the field’s **Configure** button, select the **Required** checkbox field, and hit **Apply**.
  6. To complete the form, add the **Consent agreement** field and:
    1. Select the **Configure** button and, for this tutorial, select _Marketing cookies_.
    2. **Apply** to save the change.
  7. Switch to the **General** tab and:
    1. Rename the form to _Loans general form_.
    2. Change the **Code name** to _LoansGeneralForm_.
    3. **Save** the changes.


### Display your form on the live site
  1. Switch to the **Personal Banking** application and go to the _Loans_ page. Select **Edit page** to create a new version of the published _Loans_ page.
  2. **Add section** below the _Featured content_ widget with the **plus** button on the left and add the **Form column** section.
  3. **Add widget** using the **plus** button in the middle of a new section and insert the **Page heading** widget.
  4. Open the _Page heading properties_ with the **Configure widget** and:
    1. Choose **Heading type** : _H3_
    2. Add the **Title** : _Let us prepare a personalized loan offer that best suits your needs_.
    3. Hit **Apply** to save the changes.
  5. With the **plus** button, add a **Form** widget.
  6. Open the _Form properties_ with the **Configure widget** and:
    1. Select the _Loans general form_.
    2. For **After form submission** , keep **Display a message**.
    3. Adjust the text: _Thank you! Our loan specialist will contact you within the next two business days._
    4. **Apply** the change.
  7. Finally, **Save** and **Publish** your page.


Your page with a new form may look like the following:
[![](/docsassets/guides/collect-visitor-data-essentials/form-loans-page.png)](/docsassets/guides/collect-visitor-data-essentials/form-loans-page.png)
## Next step
Continue learning about [segmenting your website audience](/guides/digital-marketing/xperience-essentials/segment-your-website-audience-essentials).
![]()
[]()[]()
