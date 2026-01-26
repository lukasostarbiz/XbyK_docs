---
source: https://docs.kentico.com/documentation/developers-and-admins/configuration/notifications
scrape_date: 2026-01-26
---

  * [Home](/documentation)
  * [Developers and admins](/documentation/developers-and-admins)
  * [Configuration](/documentation/developers-and-admins/configuration)
  * Notifications 


# Notifications
Notifications are emails that provide information to administration users about various events in the system.
Notifications are sent to internal [users](/documentation/developers-and-admins/configuration/users) of the Xperience administration. To send emails to your application’s public audience, use [Emails](/documentation/business-users/digital-marketing/emails).
## Configure domains for notifications
### Service domain
A service domain is used in the URLs of various links within notification content, such as [asset](/documentation/business-users/content-hub/content-item-assets) URLs. The service domain also sets the value of the `$$ServiceDomain$$` placeholder, which can be placed into the content of the system’s default notifications.
Default system notifications rely on the service domain being set to the domain where you host the administration interface. Otherwise, [user invitation links](#notification-content-placeholders), or [links to form details](#form-submission-notifications), for example, will not resolve correctly.
For [private cloud](/documentation/developers-and-admins/deployment/deploy-to-private-cloud) projects, you can configure the notification service domain using the `SystemEmailOptions.ServiceDomain` property and [.NET configuration providers](https://learn.microsoft.com/en-us/aspnet/core/fundamentals/configuration/). For more information about startup options and examples, see [Startup options in configuration files](/documentation/developers-and-admins/development/website-development-basics/configure-new-projects#startup-options-in-configuration-files).
If you have an established [administration domain](/documentation/developers-and-admins/configuration/administration-domain-configuration), consider using it as the service domain for notifications as well.
For [SaaS projects](/documentation/developers-and-admins/saas/saas-overview), the notification service domain is fixed and in the following format:  
_< environment>-<project_name>.xperience-sites.com_
### Sending domain
For [private cloud](/documentation/developers-and-admins/deployment/deploy-to-private-cloud), you can configure a global sending domain for notifications. This domain must be used in the sender address of all notifications.
Use the `SystemEmailOptions.SendingDomain` property and [.NET configuration providers](https://learn.microsoft.com/en-us/aspnet/core/fundamentals/configuration/). For more information and examples, see [Global sending domain for system emails](/documentation/developers-and-admins/configuration/email-configuration#global-sending-domain-for-system-emails).
For [SaaS projects](/documentation/developers-and-admins/saas/saas-overview), which use SendGrid to send emails, the sending domain is fixed and in the following format: _< environment>-<project_name>.xperience-sites.com_
## Edit default notifications
The system contains several built-in notifications, which are necessary for the correct functioning of Xperience by Kentico, and cannot be deleted. However, you can edit the subject, content and sender address of the email.
  1. Go to the **Notifications** application in the Xperience administration.
  2. Select the notification you wish to edit.
  3. On the **Content** tab, you can edit the subject of the email and its content. You need to use the _required_ placeholders listed below the _Content_ field, either in the _Content_ field or one of your [custom added fields](#extend-the-notification-editing-ui).
If you have multiple [language variants](/documentation/developers-and-admins/configuration/languages) of your content, any images or links to website channel pages added to the notification’s content are displayed in the project’s default language.
  4. **Save** the edited notification. [![Editing a default notification](/docsassets/documentation/notifications/Edit_Default_Notification.png)](/docsassets/documentation/notifications/Edit_Default_Notification.png)
  5. On the **Properties** tab, you can adjust the following:
     * **Notification template** – allows you to adjust the notification’s overall design and layout by preparing your own [notification email templates](#notification-email-templates).
     * **Sender email address** – the “From” address that notification recipients see in their inbox. The domain part of all sender addresses must match the application’s [sending domain](#sending-domain).
  6. **Save** any changes made on the _Properties_ tab.


To view how the generated email will appear to recipients, select the **Preview** tab.
### Add URL placeholders
All of the default system notifications require a URL [placeholder](#notification-content-placeholders), which is used to create a link that allows the user to perform a critical action (e.g., finish their registration, reset their password, or unlock their account). To add this placeholder into the notification **Content** , you can choose one of two approaches:
  * Use the **Insert link > Web URL** option from the toolbar of the [rich text editor](/documentation/business-users/rich-text-editor). Add the placeholder into the _URL_ field and enter the text you wish to display into the _Text_ field.
[![Use Insert link button to add a URL placeholder](/docsassets/documentation/notifications/Placeholder_Insert_Link.png)](/docsassets/documentation/notifications/Placeholder_Insert_Link.png)
  * Switch the _Content_ rich text editor to the [Code View](/documentation/business-users/rich-text-editor#format-text). This approach requires you to add an HTML tag that allows recipients to open a URL. For example, to insert a link with the multi-factor authentication reset URL and the text “Click here”, you would use the following code: `<a href="{{MFAUrl}}">Click here</a>`


### Extend the notification editing UI
If the default **Content** field is not sufficient for your notification editing requirements, you can extend the UI with custom fields.
  1. Open the **Modules** application.
  2. Select the **Notifications** module.
  3. On the **Classes** tab, select the **Notification email** class.
  4. Add [custom fields](/documentation/developers-and-admins/customization/object-types/extend-system-object-types) to the class and its **Content** UI form.


Such fields allow you to manage notification content in a more structured way.
You can then insert the fields via `{% field %}` macros into your [notification email templates](#create-custom-notification-email-templates).
## Notification content placeholders
Placeholders allow you to add variables into notifications. These placeholders are added into the notification’s content, and populated when the notification is actually sent. For example, a placeholder allows you to insert the name of the recipient into the notification content.
Each notification offers different placeholders given by its type. If a placeholder is marked **required** , it must appear at least once in the notification content or title.
[![Notification content placeholders](/docsassets/documentation/notifications/Content_Placeholders.png)](/docsassets/documentation/notifications/Content_Placeholders.png)
## Notification email templates
Notification email templates establish the structure, design and layout of notifications while incorporating static elements like headers or footers. A single template can be reused for multiple notifications. The content within the template is created using HTML code and styled with CSS. The templates also support the use of [macro placeholders](#notification-template-macros).
### Create custom notification email templates
  1. In the **Notifications** applications, go to the **Email templates** tab.
  2. Select **New notification email template**.
  3. Fill in the following fields: 
     * **Notification email template name** – the name displayed in the application.
     * (Optional) **Identifiers** – specify the code name if you wish to use a code name different than the pre-filled value.
     * (Optional) **Description** – allows you to add information and comments about the template. Only displayed to other editors in the Xperience administration.
  4. Select **Save**.
  5. On the **Source code** tab, write or copy the HTML and CSS code of your template.
  6. Insert the `{% notificationemailcontent %}` macro to position the content of notification emails that use the template.
  7. **Save** again.


HTML
**General email template example**
Copy
```
<!DOCTYPE html>
<html>
  <head></head>
  <body>
    {% notificationemailcontent %}
  </body>
</html>
```

Before deleting a notification email template, make sure it is not assigned to any notifications. Unused templates can be deleted directly from the **Notifications** application under the **Email templates** tab.
### Notification template macros
Notification templates must contain [macro expressions](/documentation/developers-and-admins/configuration/macro-expressions/macro-syntax), which serve as placeholders for notification email content.
The following macro types are available for notification emails:
  * `{% notificationemailcontent %}` – represents the main content field of notification emails.
  * `{% field %}` – represents any [custom fields](#extend-the-notification-editing-ui) added to the notification editing UI.


## Add notifications
In addition to built-in [workflow notifications](/documentation/developers-and-admins/configuration/workflows#workflow-notifications) and default notifications about events related to administration users, the system allows you to add the following types of notifications:
  * [Form submission notifications](#form-submission-notifications)
  * [Custom event notifications](#create-custom-notifications)


### Form submission notifications
Form submission notifications notify users about [form](/documentation/business-users/digital-marketing/forms) submissions by live site users. This can be useful for users who are responsible for processing submitted form data.
To add a form submission notification:
  1. Open the **Notifications** application.
  2. Select **New notification**.
  3. Fill in the following properties:
     * **Notification name** – the name of the notification displayed in the administration.
     * (Optional) **Identifiers** – specify the code name if you wish to use a code name different than the pre-filled value.
     * **Event type** – select _Form submission_.
     * **Forms** – forms that trigger the notification.
     * **Recipients** – users that receive the notification. 
**Permission requirements for recipients**
Users can only be selected as form submission notification recipients if they have a role with the **View** [permission](/documentation/developers-and-admins/configuration/users/role-management) for the **Forms** application. 
     * **Notification template** – the [email template](#notification-email-templates) used by the notification.
     * **Sender email address** – the “From” address that notification recipients see in their inbox. The domain part must match the application’s [sending domain](#sending-domain).
  4. Select **Save**.
  5. In the notification’s **Content** view, fill in the email **Subject** and **Content**. Use the available [placeholders](#notification-content-placeholders) as required.
     * Note that for the `{{FormLink}}` placeholder to resolve correctly, the application’s [service domain](#service-domain) must be set to the domain where you host the administration interface.
  6. Select **Save**.


The form submission notification is now ready. The target recipients will receive notification emails based on the selected template each time one of the selected forms gets submitted.
**Form submission notifications queueing**
For performance reasons, form notification emails are placed into an auxiliary in-memory queue before being enqueued and persisted in the [email queue](/documentation/business-users/email-queue). The placement in the auxiliary queue lasts approximately 10 seconds. In case of an application crash or restart, any notifications currently in the auxiliary queue are lost.
When a form for which notifications are configured is deleted, the corresponding notification stops sending emails for the deleted form, but remains in the system.
#### Extracting notification data from form fields
When editing the content of form submission notifications, you can use the `{{FormData}}` [placeholder](#notification-content-placeholders) to render a preview of the submitted form data directly into the notification.
The placeholder by default supports form fields with data types that can be reasonably represented as text – `int`, `double`, `boolean`, `string`. All [default Form Builder](/documentation/developers-and-admins/development/builders/form-builder/reference-form-builder-components) components with the listed data types are supported.
If you want the preview to include the contents of fields based on other data types or if you have form components built using [custom data types](/documentation/developers-and-admins/customization/field-editor/data-type-management), you need to implement dedicated data type extractors to have the fields appear in the `{{FormData}}` placeholder preview.
To help achieve this, the system provides the `FormFieldTextValueExtractorBase<TValue>` API whose purpose is to transform the field value of type `TValue` to its preferred text representation. For example, to add support for form components storing GUIDs:
C#
**Form field text extractor example**
Copy
```
using CMS.OnlineForms;

// Implement a class that inherits from 'FormFieldTextValueExtractorBase<TValue>'
// where 'TValue' is the data type of the value stored by the target form component
public class GuidExtractor : FormFieldTextValueExtractorBase<Guid>
{
    // The 'CanExtract' method determines whether this extractor can be used
    // for a given form field. The method runs for every field in the form when
    // {{FormData}} is being resolved.
    public override Task<bool> CanExtract(FormFieldExtractionContext context)
    {
        // Tests if the field data type matches the target data type
        return Task.FromResult(string.Equals(context.FormFieldInfo.DataType,
                                             FieldDataType.Guid,
                                             StringComparison.OrdinalIgnoreCase));

        // 'FormFieldExtractionContext' also provides access to the identifier
        // of the form component used for the currently processed field via
        // 'context.GetFormComponentIdentifier()', which can be used to target
        // components with specific formatting or text extraction requirements.
    }


    // The 'Extract' method is responsible for converting the underlying
    // form component value to its preferred text representation,
    // as rendered inside the notification email when {{FormData}} resolves.
    public override Task<string> Extract(Guid value, FormFieldExtractionContext context)
    {
        return Task.FromResult(value.ToString());
    }
}
```

Implemented text extractors must be registered in the application’s service container **after** the `AddKentico` call.
C#
**Program.cs**
Copy
```
builder.Services.AddKentico();
// Adds the implementation to the application's service container
builder.Services.AddSingleton(typeof(FormFieldTextValueExtractorBase<Guid>),
                              typeof(GuidExtractor));
```

The order in which text extractors are added to the container determines when they are called when resolving the `{{FormData}}` placeholder. The system uses the **last registered** extractor whose `CanExtract` method returns `true` for the given form field.
This can also be used to override the extractors provided by the system for the `int`, `double`, `boolean`, `string` data types by registering an alternative extractor (the default extractors are registered as part of `AddKentico`).
C#
**Program.cs**
Copy
```
builder.Services.AddKentico();
// Overrides the default system extractor for form components of the 'int' data type
builder.Services.AddSingleton(typeof(FormFieldTextValueExtractorBase<int>),
                              typeof(MyIntegerExtractor));
```

## Create custom notifications
Developers can create custom notifications to inform administration users about events that are not covered by the default notifications. For example, you can send a notification to users when a specific [global system event](/documentation/developers-and-admins/customization/handle-global-events/reference-global-system-events) occurs.
  1. Open the **Notifications** application.
  2. Select **New notification**.
  3. Fill in the following properties:
     * **Notification name** – the name of the notification displayed in the administration.
     * (Optional) **Identifiers** – specify the code name if you wish to use a code name different than the pre-filled value. 
       * The code name is used in code when registering placeholders and sending the notification.
     * **Event type** – select _Custom_.
     * **Notification template** – the notification template based on which the email body is created.
     * **Sender email address** – the “From” address that notification recipients see in their inbox. The domain part of all sender addresses must match the application’s [sending domain](#sending-domain).
  4. Select **Save**.


The basic notification object is now ready. Before editors can add the notification content, you need to [register placeholders](#register-placeholders-for-custom-notifications). Then finish the process by adding [code that sends the notification](#send-custom-notifications).
### Register placeholders for custom notifications
  1. Create a new class in your Xperience solution.
     * See [Integrate custom code](/documentation/developers-and-admins/customization/integrate-custom-code) for best practices about adding custom code to your project.
  2. Make the class implement the `INotificationEmailPlaceholdersByCodeName` interface.
  3. Assign the **Code name** of the related notification into the `NotificationEmailName` property.
  4. List each placeholder as a property in your class.
  5. _(Optional)_ You can also adjust placeholder behavior by adding the following attributes to the properties:
     * `PlaceholderDescription` – text that appears next to the placeholders in the notification editing UI.
     * `PlaceholderRequired` – enables validation for the notification, which fails if the placeholder is not used in the _Content_ field.
     * `PlaceholderDisableHtmlEncoding` – used to render the placeholder’s value as a raw string. Otherwise the value is HTML encoded by default.
C#
**Example of notification placeholders**
Copy
```
// Represents the placeholders for a custom notification email
public class CustomNotificationPlaceholders: INotificationEmailPlaceholdersByCodeName
{
    // Use the code name of the custom notification from the 'Notifications' application
    public string NotificationEmailName => "CustomNotification";

    [PlaceholderRequired]
    [PlaceholderDescription("This is a custom placeholder.")]
    [PlaceholderDisableHtmlEncoding]
    public string CustomPlaceholder { get; set; } = "Default value";

    public string AnotherCustomPlaceholder { get; set; } = "Another default value";
}
```

  6. Add a [custom module with initialization code](/documentation/developers-and-admins/customization/run-code-on-application-startup) into the project.
  7. In the custom initialization code, register the placeholders to the `NotificationEmailPlaceholderConfigurationStore` using the store’s `TryAdd` method.
C#
**Add the placeholders to the store**
Copy
```
using CMS.Notifications;

// ...

NotificationEmailPlaceholderConfigurationStore.Instance.TryAdd(new CustomNotificationPlaceholders());
```



### Send custom notifications
To send custom notifications:
  1. Identify the context where you want to send the notification. For example, you can send custom notifications from [global event handlers](/documentation/developers-and-admins/customization/handle-global-events).
  2. Make sure you have a [placeholder class defined](#register-placeholders-for-custom-notifications) for the notification.
  3. Create an instance of your placeholder class and populate the properties with the placeholder values for the context in which the notification is being sent.
  4. Create an `EmailMessage` by calling the `CreateEmailMessage` method of the `INotificationEmailMessageProvider` service
  5. Send the message using `IEmailService`.


C#
**Notification email API**
Copy
```
// Services obtained via dependency injection
private readonly INotificationEmailMessageProvider notificationEmailMessageProvider;
private readonly IUserInfoProvider userInfoProvider;
private readonly IEmailService emailService;

// ...

// Gets a user with the user name "UserName" as the notification recipient
var recipientUser = userInfoProvider.Get("UserName");

// Populates values for the placeholders in the notification
var placeholders = new CustomNotificationPlaceholders
{
    CustomPlaceholder = "Value",
    AnotherCustomPlaceholder = "Another value"
};

// Creates an email message based on a notification email
var emailMessage = await notificationEmailMessageProvider.CreateEmailMessage("CustomNotification", recipientUser.UserID, placeholders);

// Sends the email asynchronously
await emailService.SendEmail(emailMessage);
```

**Custom notification example**
For a full example of custom notification development, see: [Set up custom workflow notifications](/documentation/developers-and-admins/customization/handle-global-events/set-up-custom-workflow-notifications#example)
![]()
[]()[]()
