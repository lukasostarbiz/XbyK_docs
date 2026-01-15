# Access custom global configurations
  * [ Copy page link ](modules/custom-settings/access-custom-global-configurations#) | [Get HelpService ID](modules/custom-settings/access-custom-global-configurations#)
Core MVC 5


[✖](modules/custom-settings/access-custom-global-configurations# "Close page link panel") [Copy to clipboard](modules/custom-settings/access-custom-global-configurations#)
[Earlier in this series](modules/custom-settings), you learned how to create a custom module that holds custom channel-agnostic key-value-based settings.
Let’s say your customer wants to use the new module to define two custom email notification settings: **Email notification recipients** and **Email notification sender**.
Your browser does not support the video tag. 
Every time Xperience sends a certain type of email notification, these two settings should determine who the recipients and the sender are.
**How do you access the values in your code to meet the customer’s requirements?**
Using the **email notification settings** described above as an example, let’s explore how you can access custom module values and how to utilize the [Options pattern](https://learn.microsoft.com/en-us/aspnet/core/fundamentals/configuration/options?view=aspnetcore-8.0) to work with custom settings defined in the Xperience in a strongly typed manner.
## Enter the data
To follow along with the example here, use the UI developed earlier in this series to create two email notification settings, as seen in [this video](modules/custom-settings/access-custom-global-configurations#EmailSettingsDemo).
You can copy-paste these values:
  * **Display name:** Email notification recipients
  * **Settings key code name:** EmailNotificationToAddresses
  * **Value:** :admin@localhost.local;support@localhost.local
  * **Notes:** The email addresses that should receive custom email notifications. Separate addresses with a semi-colon.