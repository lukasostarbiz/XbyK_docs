# Email queue
  * [ Copy page link ](api/configuration/email-queue#) | [Get HelpService ID](api/configuration/email-queue#)
Core MVC 5


[✖](api/configuration/email-queue# "Close page link panel") [Copy to clipboard](api/configuration/email-queue#)
  
List of examples:
  * [Dependency injection](api/configuration/email-queue#dependency-injection)
    * [Initialize required services](api/configuration/email-queue#initialize-required-services)
    * [Send emails](api/configuration/email-queue#send-emails)


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

[> Back to list of examples](api/configuration/email-queue#toc)
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

[> Back to list of examples](api/configuration/email-queue#toc)