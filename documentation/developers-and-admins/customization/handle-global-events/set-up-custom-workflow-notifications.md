# Set up custom workflow notifications
  * [ Copy page link ](documentation/developers-and-admins/customization/handle-global-events/set-up-custom-workflow-notifications#) | [Get HelpService ID](documentation/developers-and-admins/customization/handle-global-events/set-up-custom-workflow-notifications#)
Core MVC 5


[✖](documentation/developers-and-admins/customization/handle-global-events/set-up-custom-workflow-notifications# "Close page link panel") [Copy to clipboard](documentation/developers-and-admins/customization/handle-global-events/set-up-custom-workflow-notifications#)
As of version [30.8.0](documentation/changelog#refresh-july-24-2025), Xperience includes built-in [workflow notifications](documentation/developers-and-admins/configuration/workflows#workflow-notifications). You don’t have to create custom workflow notifications unless you need further customization.
This page provides a full example of [custom notification](documentation/developers-and-admins/configuration/notifications#create-custom-notifications) development, including the implementation of [event handlers](documentation/developers-and-admins/customization/handle-global-events), their assignment to global events, and obtaining [user](documentation/developers-and-admins/configuration/users) IDs of the intended notification recipients.
You can use this demonstration as a blueprint to implement your own custom notifications, or follow it more closely to set up custom [workflow](documentation/developers-and-admins/configuration/workflows) notifications if the built-in [workflow notifications](documentation/developers-and-admins/configuration/workflows#workflow-notifications) do not fit your needs. For example, this can be the case if you do not want to use email to deliver notifications, or if you need to send the notification to a different group of recipients.
## Workflow notifications
When using [workflows](documentation/developers-and-admins/configuration/workflows) in your content editing process, users in various roles need to work with items at different stages of the content lifecycle. Notifications about workflow step transitions can help people keep track of their work. For example, users may wish to be notified when an item transitions to a step that requires their approval or review.
Developers can set up notifications by handling [global events](documentation/developers-and-admins/customization/handle-global-events/reference-global-system-events) that the system triggers when items transition between workflow steps:
  * `MoveToStep` – triggered when a content item, page, or headless item is moved from one workflow step to another. Handled via the _ContentItemWorkflowEvents_ , _WebPageWorkflowEvents_ or _HeadlessItemWorkflowEvents_ classes.
  * `Publish` – triggered when a content item, page or headless item finishes its workflow cycle and is published. Handled via the _ContentItemEvents_ , _WebPageEvents_ or _HeadlessItemEvents_ classes.
  * `UpdateLanguageMetadata` – triggered when a reusable content item or page is scheduled to be published or unpublished, or when a scheduled publish action is canceled. Handled via the _ContentItemEvents_ or _WebPageEvents_ classes.


The content of notification messages can either be based on [custom notifications](documentation/developers-and-admins/configuration/notifications#create-custom-notifications) and managed in the Xperience administration, or created directly in code.
Limitations:
  * The system currently does not provide events covering workflow transitions of [emails](documentation/business-users/digital-marketing/emails). However, you can use built-in [workflow notifications](documentation/developers-and-admins/configuration/workflows#workflow-notifications) for emails.
  * Workflow events are not triggered when an item enters the system’s default _Draft_ step for the first time in a workflow cycle, for example after creating a completely new item or a new version of a published or unpublished item. For newly added items, you can handle the `Create` event via the _ContentItemEvents_ , _WebPageEvents_ or _HeadlessItemEvents_ classes.


## Example
This example demonstrates how to set up email notifications informing about workflow step transitions. The example sends notifications when a [reusable content item](documentation/business-users/content-hub) or [website channel page](documentation/business-users/website-content) is moved to a different step in any workflow. The recipients of the emails depend on the step:
  * **Custom workflow steps** – sent to users who are allowed to work with the item in the given step, i.e. all users belonging to the [roles](documentation/developers-and-admins/configuration/users/role-management) assigned to the step.
  * **Draft (system step)** – _Draft_ is the default first step in all workflows, and cannot have any roles assigned. The notification emails are instead sent to users with roles that have full control for the workflow, and to users with the _ContentEditor_ role (as an example). Note that notifications are not sent when an item enters the _Draft_ step for the first time in a workflow cycle, for example after creating a completely new item or a new version of a published or unpublished item.


For the sake of simplicity, this example does **not** send notifications when pages or content items are published. However, you can set up publish notifications using a similar approach by handling the `ContentItemEvents.Publish` and `WebPageItemEvents.Publish` events.
You can extend or simplify the implementation based on your project’s content types and workflow steps, as well as the requirements of your content editors. If you wish to use a different type of notifications than emails, replace the notification and email API calls in the example’s code (e.g., with an external messaging SDK).
### Prerequisites
To integrate the code of this example, prepare a [custom Class Library project](documentation/developers-and-admins/customization/integrate-custom-code) in your solution.
To allow the system to send out emails, you also need to set up and configure an email client (for example an SMTP server or SendGrid integration). For more information, see [Email configuration](documentation/developers-and-admins/configuration/email-configuration).
### Register notification placeholders
Add the following class to your custom Class Library project:
C#
**WorkflowNotificationPlaceholders.cs**
Copy
```
public class WorkflowNotificationPlaceholders : INotificationEmailPlaceholdersByCodeName
{
    // The code name of the workflow notification, which will be created in the Notifications application
    public string NotificationEmailName => "custom_workflow";


    [PlaceholderRequired]
    [PlaceholderDescription("The name of the content item or page that was moved to a different workflow step.")]
    public string ItemName { get; set; }

    [PlaceholderRequired]
    [PlaceholderDescription("The display name of the new workflow step.")]
    public string CurrentStepDisplayName { get; set; }

    [PlaceholderRequired]
    [PlaceholderDescription("The display name of the previous workflow step from which the item was moved.")]
    public string OriginalStepDisplayName { get; set; }

    [PlaceholderDescription("The user name of the administration user who changed the item's workflow step.")]
    public string StepChangedByUserName { get; set; } = "System";
}
```

This class defines placeholders, which serve as variables in the workflow notification content.
Next, add a [custom module](documentation/developers-and-admins/customization/run-code-on-application-startup) into the project and register the placeholders in the module’s initialization code:
C#
**Module class with notification placeholder registration**
Copy
```
using CMS;
using CMS.Core;
using CMS.DataEngine;
using CMS.Notifications;

// Registers the custom module into the system
[assembly: RegisterModule(typeof(Custom.CustomWorkflowModule))]

namespace Custom
{
    public class CustomWorkflowModule : Module
    {
        // Module class constructor, the system registers the module under the name "CustomWorkflow"
        public CustomWorkflowModule()
            : base(nameof(CustomWorkflowModule))
        {
        }

        // Contains initialization code that is executed when the application starts
        protected override void OnInit(ModuleInitParameters parameters)
        {
            base.OnInit();

            // Registers placeholders for the custom workflow notification
            NotificationEmailPlaceholderConfigurationStore.Instance.TryAdd(new WorkflowNotificationPlaceholders());
        }
    }
}
```

### Create the notification
Add the [notification](documentation/developers-and-admins/configuration/notifications#create-custom-notifications) in Xperience:
  1. Open the **Notifications** application in the Xperience administration.
  2. Select **New notification**.
  3. Set the following values for the properties:
     * **Notification name** : Workflow notification
     * **Identifiers → Code name** (clear the _Pre-fill code name automatically_ option): custom_workflow
     * **Notification template** : General email template (or any other template)
     * **Sender email address** : Any sender with your application’s [sending domain](documentation/developers-and-admins/configuration/notifications#sending-domain) for system emails
  4. Select **Save**.
  5. Enter the following content for the notification:
     * **Subject** : Item ‘{{ItemName}}’ moved to step ‘{{CurrentStepDisplayName}}’
     * **Content** : The ‘{{ItemName}}’ item was moved from step ‘{{OriginalStepDisplayName}}’ to ‘{{CurrentStepDisplayName}}’ by user ‘{{StepChangedByUserName}}’.
  6. Select **Save**.


[![Workflow notification content with custom placeholders](docsassets/documentation/set-up-custom-workflow-notifications/Workflow_notification.png)](https://docs.kentico.com/docsassets/documentation/set-up-custom-workflow-notifications/Workflow_notification.png)
The notification and its content are now ready
### Send workflow notifications
Continue by extending the module with workflow event handlers that send the notifications.
To get the code of the module class, you can either download the [CustomWorkflowModule.cs](docsassets/documentation/set-up-custom-workflow-notifications/CustomWorkflowModule.cs) file, or view the code blocks below.
C#
**Module class and handler assignment**
Copy
```
using CMS;
using CMS.DataEngine;
using CMS.ContentWorkflowEngine;
using CMS.Core;
using CMS.EmailEngine;
using CMS.Membership;
using CMS.Notifications;
using CMS.Websites;

using Microsoft.Extensions.DependencyInjection;
using Microsoft.Extensions.Options;

// Registers the custom module into the system
[assembly: RegisterModule(typeof(Custom.CustomWorkflowModule))]

namespace Custom
{
    public class CustomWorkflowModule : Module
    {
        private IEmailService emailService;
        private IInfoProvider<ContentWorkflowStepInfo> workflowStepProvider;
        private IInfoProvider<ContentWorkflowStepRoleInfo> workflowStepRoleProvider;
        private IInfoProvider<ContentWorkflowRoleInfo> workflowRoleProvider;
        private IInfoProvider<WebsiteChannelInfo> websiteChannelInfoProvider;
        private IRoleInfoProvider roleInfoProvider;
        private IUserInfoProvider userInfoProvider;
        private IUserRoleInfoProvider userRoleInfoProvider;
        private INotificationEmailMessageProvider notificationEmailMessageProvider;
        private IOptionsMonitor<SystemEmailOptions> systemEmailOptions;

        // Module class constructor, the system registers the module under the name "CustomWorkflow"
        public CustomWorkflowModule()
            : base(nameof(CustomWorkflowModule))
        {
        }

        // Contains initialization code that is executed when the application starts
        protected override void OnInit(ModuleInitParameters parameters)
        {
            base.OnInit();

            // Gets instances of required services
            emailService = parameters.Services.GetRequiredService<IEmailService>();
            workflowStepProvider = parameters.Services.GetRequiredService<IInfoProvider<ContentWorkflowStepInfo>>();
            workflowStepRoleProvider = parameters.Services.GetRequiredService<IInfoProvider<ContentWorkflowStepRoleInfo>>();
            workflowRoleProvider = parameters.Services.GetRequiredService<IInfoProvider<ContentWorkflowRoleInfo>>();
            websiteChannelInfoProvider = parameters.Services.GetRequiredService<IInfoProvider<WebsiteChannelInfo>>();
            roleInfoProvider = parameters.Services.GetRequiredService<IRoleInfoProvider>();
            userInfoProvider = parameters.Services.GetRequiredService<IUserInfoProvider>();
            userRoleInfoProvider = parameters.Services.GetRequiredService<IUserRoleInfoProvider>();
            notificationEmailMessageProvider = parameters.Services.GetRequiredService<INotificationEmailMessageProvider>();
            systemEmailOptions = parameters.Services.GetRequiredService<IOptionsMonitor<SystemEmailOptions>>();

            // Registers placeholders for the custom workflow notification
            NotificationEmailPlaceholderConfigurationStore.Instance.TryAdd(new WorkflowNotificationPlaceholders());

            // Assigns a handler to the MoveToStep workflow event for reusable content items
            ContentItemWorkflowEvents.MoveToStep.Execute += ContentItem_MoveToStepEventHandler;

            // Assigns a handler to the MoveToStep workflow event for website channel pages
            WebPageWorkflowEvents.MoveToStep.Execute += WebPage_MoveToStepEventHandler;
        }

        // Add the event handlers and other helper methods from the code blocks below
        // ...
    }
}
```

C#
**Content item MoveToStep handler**
Copy
```
// Handler for the MoveToStep event for content items
// Sends an email notification to all users who can work with the item in the current step
private void ContentItem_MoveToStepEventHandler(object sender, ContentItemWorkflowMoveToStepArguments e)
{
    ContentWorkflowStepInfo currentWorkflowStep = workflowStepProvider.Get(e.StepName);

    IEnumerable<int> recipientIds;

    // If the current step is the 'Draft' system step, gets the email addresses of all users
    // belonging to specific roles, or roles that can work with all steps in the workflow.
    if (currentWorkflowStep.ContentWorkflowStepType == ContentWorkflowStepType.SystemDraft)
    {
        recipientIds = GetUserIdsForEditors(currentWorkflowStep.ContentWorkflowStepWorkflowID);
    }
    // For custom workflow steps, gets the email addresses of all users belonging to roles
    // who can work with the given workflow step.
    else
    {
        recipientIds = GetUserIdsForWorkflowStep(currentWorkflowStep.ContentWorkflowStepID, currentWorkflowStep.ContentWorkflowStepWorkflowID);
    }

    // Prepares values for placeholders in the workflow notification
    var placeholders = new WorkflowNotificationPlaceholders
    {
        ItemName = e.DisplayName,
        CurrentStepDisplayName = currentWorkflowStep.ContentWorkflowStepDisplayName,
        OriginalStepDisplayName = workflowStepProvider.Get(e.OriginalStepName).ContentWorkflowStepDisplayName,
        StepChangedByUserName = userInfoProvider.Get(e.UserID).UserName
    };

    SendWorkflowNotificationEmail(placeholders, recipientIds);
}
```

C#
**Page MoveToStep handler**
Copy
```
// Handler for the MoveToStep event for website channel pages
// Sends an email notification to all users who can work with the page in the current step
private void WebPage_MoveToStepEventHandler(object sender, WebPageWorkflowMoveToStepArguments e)
{
    ContentWorkflowStepInfo currentWorkflowStep = workflowStepProvider.Get(e.StepName);

    IEnumerable<int> recipientIds;

    // If the current step is the 'Draft' system step, gets the email addresses of all users
    // belonging to specific roles, or roles that can work with all steps in the workflow.
    if (currentWorkflowStep.ContentWorkflowStepType == ContentWorkflowStepType.SystemDraft)
    {
        recipientIds = GetUserIdsForEditors(currentWorkflowStep.ContentWorkflowStepWorkflowID);
    }
    else
    {
        // For custom workflow steps, gets the email addresses of all users belonging to roles
        // who can work with the given workflow step.
        recipientIds = GetUserIdsForWorkflowStep(currentWorkflowStep.ContentWorkflowStepID, currentWorkflowStep.ContentWorkflowStepWorkflowID);
    }

    // Prepares values for placeholders in the workflow notification
    var placeholders = new WorkflowNotificationPlaceholders
    {
        ItemName = e.DisplayName,
        CurrentStepDisplayName = currentWorkflowStep.ContentWorkflowStepDisplayName,
        OriginalStepDisplayName = workflowStepProvider.Get(e.OriginalStepName).ContentWorkflowStepDisplayName,
        StepChangedByUserName = userInfoProvider.Get(e.UserID).UserName
    };

    SendWorkflowNotificationEmail(placeholders, recipientIds);
}
```

C#
**Helper methods**
Copy
```
// Gets the IDs of all users belonging to a role that can work with a specified workflow and step
private IEnumerable<int> GetUserIdsForWorkflowStep(int stepId, int workflowId)
{
    // Gets the IDs of roles assigned to the workflow step.
    // IInfoProvider<ContentWorkflowStepRoleInfo> manages bindings between workflow steps and roles,
    // and each binding indicates that the role is allowed to work with the step.
    var stepRoleIds = workflowStepRoleProvider
                    .Get()
                    .WhereEquals(nameof(ContentWorkflowStepRoleInfo.ContentWorkflowStepRoleContentWorkflowStepID), stepId)
                    .Column(nameof(ContentWorkflowStepRoleInfo.ContentWorkflowStepRoleRoleID))
                    .GetListResult<int>();

    // Adds the IDs of roles that are allowed to work with all steps in the workflow
    var roleIds = stepRoleIds.Union(GetRoleIdsWithFullControlForWorkflow(workflowId));

    // Gets the IDs of users belonging to the roles (without duplicates)
    var userIds = new HashSet<int>();
    foreach (int roleId in roleIds)
    {
        var userIdsForRole = userRoleInfoProvider
                    .Get()
                    .WhereEquals(nameof(UserRoleInfo.RoleID), roleId)
                    .Column(nameof(UserRoleInfo.UserID))
                    .GetListResult<int>();

        userIds.UnionWith(userIdsForRole);
    }

    return userIds;
}

// Gets the IDs of all users belonging to the 'ContentEditor' or 'Administrator' roles,
// or roles that are allowed to work with all steps in the workflow.
private IEnumerable<int> GetUserIdsForEditors(int workflowId)
{
    var roleIds = new List<int>
    {
        // Adds the system 'administrator' role, which can automatically work with all applications
        roleInfoProvider.Get(RoleName.ADMINISTRATOR).RoleID
    };

    // Adds the role with the 'ContentEditor' code name. You can adjust this implementation to load a list of any required roles.
    RoleInfo contentEditorRole = roleInfoProvider.Get("ContentEditor");
    if (contentEditorRole is not null)
    {
        roleIds.Add(contentEditorRole.RoleID);
    }

    // Adds the IDs of roles that are allowed to work with all steps in the workflow
    roleIds = roleIds.Union(GetRoleIdsWithFullControlForWorkflow(workflowId)).ToList();

    // Gets the IDs of users belonging to the roles (without duplicates)
    var userIds = new HashSet<int>();
    foreach (int roleId in roleIds)
    {
        var userIdsForRole = userRoleInfoProvider
                    .Get()
                    .WhereEquals(nameof(UserRoleInfo.RoleID), roleId)
                    .Column(nameof(UserRoleInfo.UserID))
                    .GetListResult<int>();

        userIds.UnionWith(userIdsForRole);
    }

    return userIds;
}

// Gets the IDs of roles that are allowed to work with all steps in the specified workflow
private IList<int> GetRoleIdsWithFullControlForWorkflow(int workflowId)
{
    // IInfoProvider<ContentWorkflowRoleInfo> manages bindings between workflows and roles,
    // and each binding indicates that the role is allowed to work with all steps in the workflow.
    return workflowRoleProvider
                            .Get()
                            .WhereEquals(nameof(ContentWorkflowRoleInfo.ContentWorkflowRoleContentWorkflowID), workflowId)
                            .Column(nameof(ContentWorkflowRoleInfo.ContentWorkflowRoleRoleID))
                            .GetListResult<int>();
}
```

C#
**Notification sending logic**
Copy
```
private void SendWorkflowNotificationEmail(WorkflowNotificationPlaceholders placeholders, IEnumerable<int> recipientIds)
{
    // Only sends notifications if the recipient list is not empty
    if (recipientIds == null || !recipientIds.Any())
    {
        return;
    }

    foreach (int recipientUserId in recipientIds)
    {
        // Creates an email message based on the custom workflow notification email
        var emailMessage = notificationEmailMessageProvider.CreateEmailMessage(placeholders.NotificationEmailName,
                                                                               recipientUserId,
                                                                               placeholders
                                                                              ).GetAwaiter().GetResult();

        // Adds the email message to the email queue
        // The email is then sent using a configured email client (e.g., an SMTP server or SendGrid)
        emailService.SendEmail(emailMessage);
    }
}
```