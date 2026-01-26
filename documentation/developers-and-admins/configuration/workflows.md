---
source: https://docs.kentico.com/documentation/developers-and-admins/configuration/workflows
scrape_date: 2026-01-26
---

  * [Home](/documentation)
  * [Developers and admins](/documentation/developers-and-admins)
  * [Configuration](/documentation/developers-and-admins/configuration)
  * Workflows 


# Workflows
Workflow defines the lifecycle of content, including [pages](/documentation/business-users/website-content), [reusable content items](/documentation/business-users/content-hub), [headless items](/documentation/business-users/headless-content) and [emails](/documentation/business-users/digital-marketing/emails). Each workflow has one or more custom steps between the default **Draft** and **Published** steps. This allows you to ensure the quality of content by setting up a reviewing and approval process tailored specifically for your organization. The system provides [workflow notifications](#workflow-notifications) to facilitate smooth movement of items through the workflow.
An example of a simple workflow process is a website that publishes scientific articles. An author writes an article and sends it for approval to an editor who makes corrections and submits the article to the head of the respective department. The head then publishes the page, making it publicly accessible on the website.
## Create a workflow
  1. Open the **Workflows** application.
  2. Select the **New workflow** button.
  3. Fill in the **Workflow name** and optionally a code name in the **Identifiers** section.
  4. Select **Roles with full control** – users with these [roles](/documentation/developers-and-admins/configuration/users/role-management) are permitted to edit items in all [steps](#configure-the-workflow-steps) within the workflow, move items to the next or previous step, and also **directly publish** items or send/schedule regular emails from any step.


**Workflow roles and other permissions**
In addition to the role settings for workflows, the system also checks other relevant permissions ([application-level](/documentation/developers-and-admins/configuration/users/role-management) and [page permissions](/documentation/developers-and-admins/configuration/users/role-management/page-permission-management)) when deciding if a user is allowed to edit an item under workflow.
  1. **Save** the new workflow.


The workflow is now created and ready to be configured.
### Configure the workflow scope
Once a workflow is created, you can define its scope. Each content type can be assigned a single workflow. If a content type is already associated with a workflow, it will be excluded from the available scope options.
  1. Select the workflow you want to configure.
  2. Switch to the **Scope** tab.
  3. **Select content types** and choose the types to which you want to apply the workflow. You can pick multiple content types. Types for pages, emails, reusable content and headless items are all supported.
  4. **Save** the selected content types.


Upon assigning a workflow to a content type, individual content items do not initiate the workflow automatically. The workflow applies only when an item starts a new editing cycle, which includes new items or new versions of existing published items.
The editing cycle ends when an item reaches the **Published** step. Existing items in the **Draft** step do not automatically use the workflow with custom steps until they are published and a new editing cycle starts. There is currently no alternative way to start the newly applied workflow with custom steps.
### Configure the workflow steps
  1. Select the workflow you want to configure.
  2. On the **Steps** tab, select **New step**. [![New workflow step](/docsassets/documentation/workflows/workflow_create_steps.png)](/docsassets/documentation/workflows/workflow_create_steps.png)
  3. Specify the following values:
     * **Step name**
     * _(Optional)_ **Identifiers** – specify the code name if you wish to use a code name different than the pre-filled value. 
       * **Note** : The code names of workflow steps must be unique across all steps in any workflow.
     * **Icon**
     * **Roles that can work with this step** – designates [roles](/documentation/developers-and-admins/configuration/users/role-management) that are permitted to edit items in this step and move to the next or previous step. For the final custom step in the workflow, the roles gain the ability to publish items or send/schedule regular emails. 
       * When [workflow notifications](#workflow-notifications) are enabled for this step, users with roles selected in this field will receive notifications for items they have the _Update_ permission for.
       * Additionally, roles assigned in the _Roles with full control_ field on the workflow’s _General_ tab can work with items in any step.
**Workflow roles and other permissions**
In addition to the role settings for workflow steps, the system also checks other relevant permissions ([application-level](/documentation/developers-and-admins/configuration/users/role-management) and [page permissions](/documentation/developers-and-admins/configuration/users/role-management/page-permission-management)) when deciding if a user is allowed to edit an item under workflow. 
     * **Send email notifications** – clear the checkbox to disable [notifications](#workflow-notifications). When enabled, notifications are sent when an item reaches this step. 
       * The checkbox for the first workflow step also determines whether a notification is sent when an item moves from this step back to the _Draft step_.
       * If you want to enable notifications and your project is deployed in [private cloud](/documentation/developers-and-admins/deployment/deploy-to-private-cloud), make sure that the [service domain for notifications](/documentation/developers-and-admins/configuration/notifications#configure-domains-for-notifications) is set up for your project. Workflow notifications cannot be sent unless the domain is set up.
  4. **Save** the step.


Repeat the process for all steps required for your workflow. You can drag steps in the list to change their order.
## Workflow notifications
**Prerequisite**
Make sure that the [service domain for notifications](/documentation/developers-and-admins/configuration/notifications#configure-domains-for-notifications) is set up in your project. For projects deployed in the Xperience by Kentico [SaaS environment](/documentation/developers-and-admins/saas), the service domain is [configured](/documentation/developers-and-admins/digital-marketing-setup/email-channel-management#email-channels-in-the-saas-environment) automatically.
When using workflows in your content editing process, users in various roles need to work with items at different stages of the content lifecycle. Workflow [notifications](/documentation/developers-and-admins/configuration/notifications) can help users keep track of the work that requires their input. For example, users may wish to be notified when an item transitions to a step that needs their approval or review.
Notifications are available for custom workflow steps and for the _Draft_ step:
  * **Custom steps** : When notifications are [enabled](#enable-workflow-notifications) for a custom step, a notification email is sent automatically when an item is moved to this step.
  * **Draft step** : Notifications for the _Draft_ step are configured by enabling notifications for the first custom step, and they cannot be set up independently. _Draft_ step notifications are sent when an item is moved back to the _Draft_ step from the first custom step with notifications enabled.


By default, notifications are enabled when adding a new custom step. You can [see the list of custom steps configured to send notifications](#see-all-custom-workflow-steps-with-notifications-enabled) in the _Notifications_ application. Workflow notifications do not cover the _Published_ step. If you need notifications for this step, you can [set up custom workflow notifications](/documentation/developers-and-admins/customization/handle-global-events/set-up-custom-workflow-notifications).
### Notification recipients
When an item is moved to a custom step with notifications enabled, a notification email is automatically sent to all users who have the _Update_ [permission](/documentation/developers-and-admins/configuration/users/role-management) for the affected item and are also assigned to roles that can work with this step (determined by the _Roles that can work with this step_ field in [workflow step configuration](#configure-the-workflow-steps)). Users with full control do not get automatically notified.
Notifications about the movement of the item back to the _Draft_ step are always sent only to the user who created the item and the user who last modified the item.
### Notification comment
Users can add a comment when moving an item to a step with notifications enabled. The comment is included in the [email notification](#edit-the-content-of-the-workflow-notification).
[![Add a comment](/docsassets/documentation/workflows/workflow_comment.png)](/docsassets/documentation/workflows/workflow_comment.png)
### Enable workflow notifications
By default, workflow notifications are enabled when creating new custom workflow steps.
To enable notifications for an existing custom workflow step:
  1. Open the **Workflows** application.
  2. Select the workflow that includes the custom step for which you want to enable notifications.
  3. On the **Steps** tab, select the step you are interested in.
  4. Select the **Send email notifications** checkbox.
  5. **Save** the changes.


### Edit the content of the workflow notification
You can customize the default content of the email notifications:
  1. Open the **Notifications** application.
  2. Select the **Workflow step change** notification and [edit the email content](/documentation/developers-and-admins/configuration/notifications#edit-default-notifications).
     * You can use the provided [content placeholders](/documentation/developers-and-admins/configuration/notifications#notification-content-placeholders) to add dynamic parts of the content.
     * `{{InternalPreviewURL}}` can be utilized to add a [link](/documentation/developers-and-admins/configuration/notifications#add-url-placeholders) to the item that is moved. The placeholder is optional for workflow notifications.
     * We do not recommend removing the `{{Comment}}` content placeholder, as it inserts the comment added by users when moving an item.
[![Notification email](/docsassets/documentation/workflows/workflow_notification.png)](/docsassets/documentation/workflows/workflow_notification.png)
  3. **Save** the changes.


### See all custom workflow steps with notifications enabled
To see all custom workflow steps configured to send a notification when reached:
  1. Open the **Notifications** application.
  2. Select the **Workflow step change** notification.
  3. Go to the **Properties** tab and see **Affected workflow steps**.


## Share a page with external reviewers
When editors want to share a page with reviewers who don’t have access to the administration, they can create a [shareable preview URL](/documentation/business-users/website-content#shareable-preview-urls) for this purpose. See [Shareable preview URLs](/documentation/developers-and-admins/configuration/shareable-preview-urls) for more information.
## Delete a workflow and workflow steps
Workflows and steps cannot be deleted when there are existing items in the workflow steps, including the **Draft** status. Only items that have the workflow applied are considered, excluding items that were in the **Draft** step when the workflow was assigned to their content type.
If a workflow is removed from the content type (scope), items in the workflow editing cycle keep their current workflow with custom steps until they reach the **Published** step. If a new editing cycle starts (new item or new version of a published item), the default workflow with only **Draft/Published/Unpublished** statuses is used.
![]()
[]()[]()
