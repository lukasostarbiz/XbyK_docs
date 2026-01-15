# Macro expressions
  * [ Copy page link ](documentation/developers-and-admins/configuration/macro-expressions#) | [Get HelpService ID](documentation/developers-and-admins/configuration/macro-expressions#)
Core MVC 5


[✖](documentation/developers-and-admins/configuration/macro-expressions# "Close page link panel") [Copy to clipboard](documentation/developers-and-admins/configuration/macro-expressions#)
Macros are text expressions that the system evaluates and resolves into values. For more information about macro usage, see [Macro syntax](documentation/developers-and-admins/configuration/macro-expressions/macro-syntax).
## Macro availability
In the current version of Xperience by Kentico, macro expressions are usable in:
  * the source code of [Email templates](documentation/developers-and-admins/digital-marketing-setup/email-templates)
  * the **Modules** application when defining default values for the editing forms of [object types](documentation/developers-and-admins/customization/object-types)


## Development considerations
Developers and administrators need to consider macros when deploying content and other data between different instances of Xperience, for example using [Continuous Integration](documentation/developers-and-admins/ci-cd/continuous-integration) or [Continuous Deployment](documentation/developers-and-admins/ci-cd/continuous-deployment). To ensure that all functionality relying on macros works correctly in these scenarios, macros must have valid signatures for their current environment. See the [Macro signatures](documentation/developers-and-admins/configuration/macro-expressions/macro-signatures) page to learn more.