---
source: https://docs.kentico.com/api/configuration/email-queue
scrape_date: 2026-01-22
---

  * [Home](/api)
  * [Configuration](/api/configuration)
  * Email queue 


# Email queue
  
List of examples:
  * [Dependency injection](#dependency-injection)
    * [Initialize required services](#initialize-required-services)
    * [Send emails](#send-emails)


## Dependency injection
### Initialize required services
C#
Copy
```
// Initializes all services and provider classes used within
// the API examples on this page using dependency injection.
private readonly IEmailService emailService;

public EmailQueueServices(IEmailService emailService)
{
    this.emailService = emailService;
}
```

[> Back to list of examples](#toc)
### Send emails
C#
Copy
```
// Creates the email message object
EmailMessage msg = new EmailMessage()
{
    // Sets the 'From' email address
    From = "system@localhost.local",

    // Sets the 'To' email address
    // Separate addresses with a semicolon ';' to add multiple recipients
    Recipients = "admin@localhost.local",

    // Sets the priority of the email
    // The system prioritizes processing emails with a higher priority
    Priority = EmailPriorityEnum.Normal,

    // Sets the subject line of the email
    Subject = "Hello!",

    // Sets the body of the email
    Body = "Lorem ipsum dolor sit amet...",
};

// Email sending is performed using an instance of 'IEmailService', typically obtained using dependency injection
// Adds the email message to the email queue
// The email is then sent using a configured email client (e.g., an SMTP server or SendGrid)
emailService.SendEmail(msg);
```

[> Back to list of examples](#toc)
![]()
[]()[]()
