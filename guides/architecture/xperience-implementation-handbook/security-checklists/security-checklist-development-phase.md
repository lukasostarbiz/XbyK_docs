---
source: https://docs.kentico.com/guides/architecture/xperience-implementation-handbook/security-checklists/security-checklist-development-phase
scrape_date: 2026-01-22
---

  * [Home](/guides)
  * [Architecture](/guides/architecture)
  * [Xperience implementation handbook](/guides/architecture/xperience-implementation-handbook)
  * [Security checklists](/guides/architecture/xperience-implementation-handbook/security-checklists)
  * Security checklist - development phase 


# Security checklist - development phase
## General security recommendations
  * Protect your application against the OWASP top 10 most common [web attacks](https://owasp.org/www-project-top-ten/).
  * For AI-based applications, make sure to protect them against the OWASP top 10 most common [attacks for LLM applications](https://owasp.org/www-project-top-10-for-large-language-model-applications/).


## Input validation and data handling
  * Validate all user input for type, length, format, and allowed characters.
  * Rely primarily on server-side validation. Use client-side validation only to improve UX.
  * Avoid relying on client-side data for security decisions.
  * Use the provided Xperience API ([ObjectQuery API](/documentation/developers-and-admins/api/objectquery-api), [Content item query API](/documentation/developers-and-admins/api/content-item-api/content-item-query-api), [ContentRetriever API](/documentation/developers-and-admins/api/content-item-api/content-retriever-api) and others) to prevent SQL injection and other data manipulation attacks.
  * Protect sensitive endpoints against CSRF attacks.


## Authentication and authorization
  * Use modern authentication methods like [single-sign-on](/documentation/developers-and-admins/configuration/users/administration-registration-and-authentication/administration-external-authentication) or [passwordless](https://en.wikipedia.org/wiki/WebAuthn).
  * Enforce [multi-factor authentication](/documentation/developers-and-admins/configuration/users/user-management#multi-factor-authentication) (MFA) for admin and other privileged accounts.
  * Define roles based on the least privilege principle and actual usage patterns. Scope permissions narrowly to what users truly need for their tasks and avoid granting overpowered or broad access rights.
  * Rotate and securely store API keys and secrets using [Azure Key Vault](https://learn.microsoft.com/en-us/azure/key-vault/secrets/secure-secrets) or similar.


## AI security
  * Strip sensitive data before sending it to external AI APIs.
  * Validate and sanitize AI-generated content before rendering it.
  * Implement prompt injection defenses by restricting user-controlled input in AI prompts.
  * Log AI interactions for auditing and anomaly detection.
  * Ensure compliance with data privacy laws when sending data to AI services.


## File upload security
  * Validate file type, size and content upon upload.
  * Store uploaded files outside the web root.


## Error handling and logging
  * Show generic error messages to users.
  * Avoid exposing stack traces and sensitive information in the production environment.
  * Log authentication attempts and critical actions.
  * Configure and use common [logging](/documentation/developers-and-admins/development/logging) logic across the whole application.
  * Avoid excessive logging of high-frequency events (e.g., public endpoints) to prevent log bloat.


## Compliance and privacy
  * Implement GDPR / CCPA compliance for user data.
  * Encrypt sensitive data both at rest and in transit.
  * Provide clear privacy and AI usage policies.


![]()
[]()[]()
