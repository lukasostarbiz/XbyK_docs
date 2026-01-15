# Set up your Xperience Portal
  * [ Copy page link ](modules/deploy-to-saas-step-by-step/saas-setup#) | [Get HelpService ID](modules/deploy-to-saas-step-by-step/saas-setup#)
Core MVC 5


[✖](modules/deploy-to-saas-step-by-step/saas-setup# "Close page link panel") [Copy to clipboard](modules/deploy-to-saas-step-by-step/saas-setup#)
## First time sign-in
When Kentico creates your Xperience Portal instance, the person assigned as the **Primary contact** for your project (as agreed before your Xperience Portal instance creation) will receive an email invitation.
The **Primary contact** also has the role of **Tenant Administrator**.
There can be **multiple Tenant administrators** but **only one Primary contact** for each project.
After the Primary contact sets up their password and multi-factor authentication (MFA), they can add other users to the project, who will also receive an email invitation.
Follow [these steps](guides/configuration/saas/get-started-with-saas#sign-in-to-xperience-portal) to set up your account.
Upon successful sign-in to the Xperience Portal, click **Accept** to join the project you were invited to.
[![The Xperience Portal screen showing the button to Accept the project invite.](docsassets/guides/saas-setup/accept-invite.png)](https://docs.kentico.com/docsassets/guides/saas-setup/accept-invite.png)
The system will then navigate you to the Xperience Portal **Dashboard** screen.
## Identify the active project
It is possible that during your time working with the Xperience Portal, you will be a part of several projects. Here’s how you can identify that you are working on the correct one.
Notice the project selector in the top-left. If you have access to more than one project, you can switch between them. When collapsed, it indicates the project you are currently working on.
Another way to identify a project is by looking at the **Project info** panel, which shows the project name, the GUID, expiration date, and the hash string salt. We will utilize these values later in this series.
[![A screenshot of the Xperience Portal dashboard, highlighting the project selector and the Project info panel](docsassets/guides/saas-setup/dashboard.png)](https://docs.kentico.com/docsassets/guides/saas-setup/dashboard.png)
## Explore the users and roles
Navigate to the **Users** screen. Here you can see everyone participating in your current project and their [roles](documentation/developers-and-admins/saas/xperience-portal/reference-xperience-portal-user-roles).
The crown icon next to a Tenant administrator role indicates who the Primary contact for the project is.
[![Users screen in the Xperience Portal, highlighting the project’s Primary contact user.](docsassets/guides/saas-setup/primary-contact.png)](https://docs.kentico.com/docsassets/guides/saas-setup/primary-contact.png)
If your role has sufficient permissions, you can invite more users to the project or change their roles.
Read about the [different user roles in Xperience Portal here](guides/configuration/saas/get-started-with-saas#users-in-xperience-portal). Find detailed information about role permissions in [our documentation](documentation/developers-and-admins/saas/xperience-portal/reference-xperience-portal-user-roles).
## Create your personal access token
You will need a [personal access token (PAT)](documentation/developers-and-admins/saas/xperience-portal#personal-access-tokens) to upload a deployment package using the Xperience Portal API later in this series. Let’s create one.
Navigate to **Personal access tokens → My tokens** and click **Create token**.
[![Create new PAT in Xperience Portal](docsassets/guides/saas-setup/PAT-create.png)](https://docs.kentico.com/docsassets/guides/saas-setup/PAT-create.png)
Fill out the **Display name** and the **Expiration date** to fit your project needs.
Check the **Deploy to QA** checkbox under **Permissions**.
[![PAT create screen with display name, expiration and permissions set](docsassets/guides/saas-setup/PAT-create-2.png)](https://docs.kentico.com/docsassets/guides/saas-setup/PAT-create-2.png)
After you click **Create** , the system will show you the generated token.
**This is the only time you will be able to access your token value. Make sure to copy and store the PAT in a secure location BEFORE clicking OK.**
[![The only screen where you can see and copy the generated PAT.](docsassets/guides/saas-setup/PAT-create-3.png)](https://docs.kentico.com/docsassets/guides/saas-setup/PAT-create-3.png)
[![Active PAT](docsassets/guides/saas-setup/PAT-active.png)](https://docs.kentico.com/docsassets/guides/saas-setup/PAT-active.png)
## Set up notifications
As a technical user, you likely want to receive alerts about technical issues with your environment (e.g., a failed deployment, DDoS attack, etc.). Xperience portal sends such notifications via email and allows each user to set their notification preferences using the dropdown menu in the top-right corner.
By default, all notifications are enabled. Since every team has a slightly different distribution of responsibilities, we encourage you to give these settings extra thought to tweak them according to your needs.
Our recommendation for technical users is to have at least **all _Error_ and _Critical_ alerts on the environment they are responsible for**.
[![User notifications](docsassets/guides/saas-setup/notifications.png)](https://docs.kentico.com/docsassets/guides/saas-setup/notifications.png)
## Update maintenance schedule - optional
In your own project you may want to consider rescheduling the maintenance window.
For more information of what the maintenance involves and how it affects your project, visit [our documentation](documentation/developers-and-admins/saas/xperience-portal#schedule-maintenance-of-xperience-portal-projects).
For this example, let’s leave the schedule as is.
[ Previous page ](modules/deploy-to-saas-step-by-step)
2 of 8
[ Mark complete and continue ](modules/deploy-to-saas-step-by-step/saas-install-configure-project)
  * [Community Questions & Answers](https://community.kentico.com/q-and-a)
  * [Contact support](https://community.kentico.com/support)


### Cookie consent
We use necessary [cookies](https://www.kentico.com/cookies-policy) to run our website and improve your experience while browsing. Additional cookies are only used with your consent. You may revoke your consent on the [Cookies Policy](https://www.kentico.com/cookies-policy) page or in your browser at any time. 
ACCEPT ALL  [Configure](https://www.kentico.com/cookies-policy)
USE ONLY NECESSARY 
![](https://docs.kentico.com/modules/deploy-to-saas-step-by-step/saas-setup)
[](https://docs.kentico.com/modules/deploy-to-saas-step-by-step/saas-setup)[](https://docs.kentico.com/modules/deploy-to-saas-step-by-step/saas-setup)