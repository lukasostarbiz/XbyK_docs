# Security checklist - design phase
  * Concept| [ Copy page link ](guides/architecture/xperience-implementation-handbook/security-checklists/security-checklist-design-phase#) | [Get HelpService ID](guides/architecture/xperience-implementation-handbook/security-checklists/security-checklist-design-phase#)
Core MVC 5


[✖](guides/architecture/xperience-implementation-handbook/security-checklists/security-checklist-design-phase# "Close page link panel") [Copy to clipboard](guides/architecture/xperience-implementation-handbook/security-checklists/security-checklist-design-phase#)
## Business and compliance requirements
  * Define security classification and compliance needs (e.g., GDPR, ISO 27001, SOC 2).
  * Document data residency, retention, and privacy policies.
  * Include security goals and acceptance criteria in scope and user stories.
  * Create an architecture overview showing security-critical components and data flows (roles, personal data, AI/ML, third-party services, etc.).
  * Perform threat modeling to identify and mitigate risks.
  * Estimate user count, roles, and access restrictions for the Xperience administration interface.
  * Allocate resources for security testing, code review, and secure deployment.


## Environment security considerations
  * Confirm which deployment environment best fits your business requirements (self-hosted, [private cloud](documentation/developers-and-admins/deployment/deploy-to-private-cloud), [SaaS](documentation/developers-and-admins/saas)).
  * Review available security controls for your environment (secrets management, WAF, network isolation, configuration, etc.).


## Xperience by Kentico security specifications
  * Map your security requirements to available Xperience features.
  * Review the [security guidelines](documentation/developers-and-admins/security-guidelines) for Xperience and plan your implementation accordingly.
  * Validate your third-party integrations and confirm they meet the required security standards for your environment.
  * Consider the integration of AI features like [AIRA](documentation/business-users/aira) and confirm they meet the required security standards for your project and environment.
  * Design authentication and authorization aligned with the least-privilege principle.