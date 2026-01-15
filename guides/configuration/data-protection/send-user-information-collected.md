# Send customer information about their data (Right to access)
  * Tutorial| [ Copy page link ](guides/configuration/data-protection/send-user-information-collected#) | [Get HelpService ID](guides/configuration/data-protection/send-user-information-collected#) | This page is part of a module: [ Data protection configuration ](modules/data-protection-configuration)
Core MVC 5


[✖](guides/configuration/data-protection/send-user-information-collected# "Close page link panel") [Copy to clipboard](guides/configuration/data-protection/send-user-information-collected#)
Let’s help a customer to know which personal data and data about their interactions with your business have your company collected and stored. Handling customer’s requests about their personal data is one of the Data Protection officer’s responsibilities. (Find out more about [GDPR-related requirements](https://gdpr.eu/what-is-gdpr/).)
This feature requires implementing data protection, cookie consent, and activity tracking. Find out more in [Kentico Xperience documentation](documentation/developers-and-admins/data-protection).
Not a fan of a video? Follow the steps below to comply with customer’s _Right to access_ request.
  1. Navigate to the **Data protection** application.
  2. Open the **Right to access** tab.
  3. Use your customer’s email address and insert it into the **search** box. 
    1. The application lists all the data your company has collected about this customer.
  4. **Copy** the list of collected data.
  5. Insert the data into a _Notepad_ (or similar tool) on your computer.
  6. Share the data with the requester.


To test this scenario on the demo website, you need to first generate sample data for GDPR.
Navigate to the **Configuration** → **Sample data generator** and use the **Generate sample data** button. You can then also imitate the contact’s behavior to get more realistic data. Visit your sample website in an incognito window and:
  1. **Agree** with the tracking consent.
  2. Visit several pages using the navigation menu.
  3. Submit the form on the Contacts page.
  4. Optional: To test deleting membership:
  5. Use the user button in the top-right corner register on the website.
  6. Supply any email and password, for example, test@demo.local and password as a member to test deleting the member object.


Test the scenario with the email of the contact you have imitated previously or the following email: Name-20.Generated.Surname-20@localhost.local.