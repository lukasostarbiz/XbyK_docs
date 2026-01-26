---
source: https://docs.kentico.com/documentation/developers-and-admins/configuration/users/user-management
scrape_date: 2026-01-26
---

  * [Home](/documentation)
  * [Developers and admins](/documentation/developers-and-admins)
  * [Configuration](/documentation/developers-and-admins/configuration)
  * [Users](/documentation/developers-and-admins/configuration/users)
  * User management 


# User management
This page describes user management specific to Xperience by Kentico projects. For management of users within Xperience Portal projects, see [Xperience Portal](/documentation/developers-and-admins/saas/xperience-portal#users).
User accounts are required to access the Xperience administration. After signing in, users can work with the available features – [manage website content](/documentation/business-users/website-content), set up [digital marketing](/documentation/business-users/digital-marketing) and [data protection](/documentation/developers-and-admins/data-protection) functionality, design [forms](/documentation/business-users/digital-marketing/forms), etc.
Table of contents:
  * [Default user accounts](#default-user-accounts)
  * [Manage users](#manage-users)
  * [User passwords](#user-passwords)
  * [Multi-factor authentication](#multi-factor-authentication)
  * [Account lockout](#account-lockout)


## Default user accounts
The system contains the following user accounts by default:
  * **Administrator** – the initial administrator account that can perform any action.
  * **Public** – represents an anonymous visitor. Present in the system but not visible or editable in user management UIs.


## Manage users
Users are classified into two groups:
  * **Live site users (members)** – regular visitors that register on a live site managed by Xperience. See [Registration and authentication](/documentation/developers-and-admins/development/registration-and-authentication) to learn how to work with these users.
  * **Admin UI users** – users that have access to the administration UI and directly interface with Xperience (e.g., content creators, administrators, marketers).


You can invite new admin UI users and manage user accounts via the **Users** application.
Select **New user** to [add a new user](#invite-users), or select an existing user in the list to [edit their details](#edit-users).
The user listing also allows you to perform the following actions:
  * Delete (
  * Enable/Disable user accounts. Account status can be seen in the **Status** column.
  * Resend invitation emails. Only available for users with the _Invited_ or _Invitation expired_ status.


### Invite users
**Prerequisite**
User invitations are done via email. Your application must have an email client (for example an SMTP server or SendGrid) configured for system emails. For more information, see [Email configuration](/documentation/developers-and-admins/configuration/email-configuration).
When inviting new users, you need to enter the user’s email address and assign them a [system role](/documentation/developers-and-admins/configuration/users/role-management).
Invited users receive a [notification email](/documentation/developers-and-admins/configuration/notifications) containing a URL that allows them to finish the registration process. The email message content and metadata can be customized to better suit your desired language and tone.
### Edit users
To **edit** a user, select the row that represents them in the listing. When editing users, you can modify the following properties:
  * **User name** – the name used when signing in. Cannot contain white space or special characters. The user name must be unique across all users in the system.
  * **First, Last name** – the names of the person using the account.
  * **Email** – the email address connected with the account.
  * **Role** – one of the two predefined system roles associated with the account.
  * **Enabled** – indicates if the user account is active, allowing the user to sign in.


### Import users
If you wish to import or migrate users from another system or external source, developers can leverage the [user API](/api/configuration/users).
When creating users, be sure to set the `UserInfo.UserAdministrationAccess` property to `true`, which is required for users to access the administration interface and be displayed in the _Users_ application.
To transfer users from an older version of Xperience, you can use the [Kentico Migration Tool](/documentation/developers-and-admins/upgrade-to-xperience-by-kentico/migration-toolkit#kentico-migration-tool).
## User passwords
Passwords are a critical part of any authentication process. Users must set their password when they finalize their registration into the system.
### Password policy
The system can be configured to use a password policy, which means that new passwords entered by users are validated according to a certain set of requirements. Passwords that do not meet the specified conditions are rejected. See [Password options](/documentation/developers-and-admins/configuration/users/administration-registration-and-authentication/administration-forms-authentication#password-options) to learn how to set password policies.
**Default admin UI password policy**
By default, Xperience sets the following requirements for passwords:
  * At least 8 characters
  * At least one uppercase character, lowercase character, a digit, and non-alphanumeric character


The policy is applied:
  * When newly invited users choose a password for their account on the registration page.
  * In all parts of the administration interface where a new password can be entered. Most commonly, this is the **Account** → **Password** tab opened when selecting your account icon in the admin UI.
  * When resetting user passwords via **Forgotten password** on the Xperience administration sign in page.


### Password storage format
The system stores user passwords in the database in an encrypted format, using the [PBKDF2](https://en.wikipedia.org/wiki/PBKDF2) key derivation standard. A cryptographic function is applied to the original password input (with a salt).
### Change password
After signing in to the administration, any user can change their own account’s password. Editing passwords is not possible for other users.
  1. Select your account menu and open the **Account** page.
  2. Switch to the **Password** tab.
  3. Enter your current password and the new one.
  4. Select **Save**.


You now need to use the new password when signing in.
### Reset password
**Prerequisite**
The password reset process uses emails. Your application must have an email client (for example an SMTP server or SendGrid) configured for system emails. For more information, see [Email configuration](/documentation/developers-and-admins/configuration/email-configuration).
If editors or other administration interface users forget their password, they may reset it, provided they have access to the email address specified for their account. They can do so via the _Forgotten password_ link on the administration sign-in page.
Password reset requires email approval from the affected user. When submitting a password reset request, users must enter their **email address**. The request affects only the user account with the corresponding email address. If the entered email address does not match any registered user, no email gets sent.
The password reset email content and metadata can be customized to better suit your desired language and tone. See the [Notifications](/documentation/developers-and-admins/configuration/notifications) documentation to learn more.
The main security benefits of this approach are:
  * Passwords are not directly included in the email, so they cannot be read by potential attackers.
  * The reset links are only valid temporarily. The validity period can be set via `AdminIdentityOptions.EmailOptions.LinkExpiration`. See [LinkExpiration](/documentation/developers-and-admins/configuration/users/administration-registration-and-authentication/administration-forms-authentication#linkexpiration).
  * After someone uses a password reset link and completes the password reset, it becomes invalid and cannot be accessed again.


## Multi-factor authentication
The Xperience administration supports multi-factor authentication (MFA), which adds an extra layer of security to the sign-in process. With MFA, users need to verify their identity by providing a **passcode** in addition to the basic username and password. This MFA feature only applies to the administration’s built-in [forms authentication](/documentation/developers-and-admins/configuration/users/administration-registration-and-authentication/administration-forms-authentication) ([external authentication providers](/documentation/developers-and-admins/configuration/users/administration-registration-and-authentication/administration-external-authentication) have their own MFA features and configuration).
To sign in with MFA enabled, users need an authenticator app that supports the [Time-based One-time Password](https://en.wikipedia.org/wiki/Time-based_one-time_password) (TOTP) algorithm. For example, [Google Authenticator](https://play.google.com/store/apps/details?id=com.google.android.apps.authenticator2) or [Microsoft Authenticator](https://www.microsoft.com/en-us/security/mobile-authenticator-app).
The authenticator application must be compatible with [RFC 6238](https://datatracker.ietf.org/doc/html/rfc6238).
### Enable MFA
Multi-factor authentication can be enabled and configured by developers in the application’s code. To learn more, see [MultiFactorAuthenticationOptions](/documentation/developers-and-admins/configuration/users/administration-registration-and-authentication/administration-forms-authentication#multifactorauthenticationoptions).
### MFA authentication process
#### Initial MFA setup
Once MFA is enabled for your application, you need to go through the following steps when **signing in for the first time** or when [registering a new account](#invite-users):
  1. Enter your username and password.
  2. The system verifies the credentials and displays a **Multi-factor authentication** setup page.
[![A user setting up multi-factor authentication for their account](/docsassets/documentation/user-management/MFA_setup.png)](/docsassets/documentation/user-management/MFA_setup.png)
  3. Link the Xperience application into your preferred TOTP authenticator app using one of the following options:
     * Scan the displayed QR code
     * Manually enter the **Secret key** into the app
  4. Generate an MFA passcode in the authenticator app.
     * Passcodes for the Xperience MFA feature are **6 digits** long and have a validity period of **30 seconds**.
  5. Enter the passcode into the Xperience MFA page.
  6. The system displays a **Recovery code**. Save the recovery code in a secure location.
     * The recovery code can be used **once** to sign in without providing an MFA passcode. This allows you to [reset MFA authentication](#reset-multi-factor-authentication-for-individual-users) for your user account if you lose access to your authenticator.
  7. **Continue** into the Xperience administration.


#### MFA sign-in
After you have set up MFA for your account, subsequent sign-ins are simpler and only consist of the following steps:
  1. Enter your username and password.
  2. Generate an MFA passcode in your authenticator app and enter it into the sign-in form 
     * Alternatively, you can sign in using the one-time recovery code.
[![Signing in using a multi-factor authentication passcode](/docsassets/documentation/user-management/MFA_signin.png)](/docsassets/documentation/user-management/MFA_signin.png)


### Reset multi-factor authentication for individual users
**Prerequisite**
The multi-factor authentication reset process uses [notification emails](/documentation/developers-and-admins/configuration/notifications). Your application must have an email client (for example an SMTP server or SendGrid) configured for system emails. For more information, see [Email configuration](/documentation/developers-and-admins/configuration/email-configuration).
If you lose access to your authenticator app, you need to reset your MFA secret key:
  1. Sign in to the Xperience administration using your one-time recovery code.
  2. Open the **Account** application.
  3. Select **Reset multi-factor secret key** on your account’s **General** tab. [![A user resetting their MFA secret key in the Xperience administration](/docsassets/documentation/user-management/MFA_reset.png)](/docsassets/documentation/user-management/MFA_reset.png)
  4. Select **Reset** in the confirmation dialog.


The system sends an email with a link that allows you to repeat the [MFA setup](#initial-mfa-setup) and connect a new authenticator app.
  * MFA reset links are only valid temporarily. The validity period can be set via `AdminIdentityOptions.EmailOptions.LinkExpiration`. See [LinkExpiration](/documentation/developers-and-admins/configuration/users/administration-registration-and-authentication/administration-forms-authentication#linkexpiration).
  * MFA reset links are invalidated after a successful MFA reset.


If you do not have a valid recovery code, the only option is to contact an administrator with [permissions](/documentation/developers-and-admins/configuration/users/role-management) to manage users in Xperience. Such administrators can reset the MFA secret key for any user account:
  1. Open the **Users** application.
  2. Select the user whose MFA secret key you want to reset.
  3. Select **Reset multi-factor secret key** on the **General** tab.
  4. Select **Reset** in the confirmation dialog.


The system sends the MFA reset email to the given user’s email address.
The content of the MFA reset email can be customized in the [Notifications](/documentation/developers-and-admins/configuration/notifications) application.
When a user’s MFA secret key is reset, authentication is **disabled** for the account (for security reasons). The account is unblocked only after the user sets up MFA again via the link in the reset email.
## Account lockout
Attackers may try to compromise user accounts through brute force, for example by repeatedly attempting to sign in with common password variants. The Xperience administration provides an account lockout mechanism to protect against such attacks (in addition to features like [Multi-factor authentication](#multi-factor-authentication)).
By default, the system locks accounts after **5** consecutive failed attempts to sign in. Authentication for the locked account is then blocked for **5 minutes**.
**Configure account lockout**
The account lockout functionality can be adjusted by developers in the application’s code. To learn more, see [Lockout options](/documentation/developers-and-admins/configuration/users/administration-registration-and-authentication/administration-forms-authentication#lockout-options).
### Unlock accounts
When a user’s account is locked, the system sends a notification to the user’s email address. This notification contains a link that allows the user to immediately unlock their account. The content of the notification email can be customized to suit your desired language and design in the [Notifications](/documentation/developers-and-admins/configuration/notifications) application.
Account unlock links are only valid temporarily and become invalidated after the account is successfully unlocked. The validity period can be set via `AdminIdentityOptions.EmailOptions.LinkExpiration`. See [LinkExpiration](/documentation/developers-and-admins/configuration/users/administration-registration-and-authentication/administration-forms-authentication#linkexpiration).
Accounts are also automatically unlocked if a user [resets their password](#change-password).
![]()
[]()[]()
