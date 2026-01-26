---
source: https://docs.kentico.com/api/development/event-log
scrape_date: 2026-01-26
---

  * [Home](/api)
  * [Development](/api/development)
  * Event log 


# Event log
  
List of examples:
  * [Dependency injection](#dependency-injection)
    * [Initialize required services](#initialize-required-services)
    * [Log events](#log-events)
    * [Work with events (sending events by email)](#work-with-events-sending-events-by-email)


## Dependency injection
### Initialize required services
C#
Copy
```
// Initializes all services and provider classes used within
// the API examples on this page using dependency injection.
private readonly IEmailService emailService;
private readonly ILogger<EventLogExamples> logger;
private readonly IInfoProvider<EventLogInfo> eventLogInfoProvider;
private readonly IAuthenticatedUserAccessor authenticatedUserAccessor;
private readonly IHttpContextAccessor httpContextAccessor;

public EventLogServices(IEmailService emailService,
                ILogger<EventLogExamples> logger,
                IInfoProvider<EventLogInfo> eventLogInfoProvider,
                IAuthenticatedUserAccessor authenticatedUserAccessor,
                IHttpContextAccessor httpContextAccessor)
{
    this.emailService = emailService;
    this.logger = logger;
    this.eventLogInfoProvider = eventLogInfoProvider;
    this.authenticatedUserAccessor = authenticatedUserAccessor;
    this.httpContextAccessor = httpContextAccessor;
}
```

[> Back to list of examples](#toc)
### Log events
C#
Copy
```
// The logging implementation in the Xperience API is based on .NET logging:
// https://learn.microsoft.com/en-us/dotnet/core/extensions/logging

/* Perform event logging via an instance of 'ILogger<TCategoryName>', typically obtained using dependency injection.
 * The TCategoryName generic (fully qualified name) sets the category of the logged events,
 * which also determines the 'Source' value for events in the built-in Xperience event log. */

/* The Xperience event log is represented by the 'XperienceEventLog' logging provider,
 * and can be configured in your project's appsettings.json file.
 * By default, only events with a log level of 'Warning' or higher are logged from custom code.
 * To include lower log levels, add configuration for your event categories.
 * For example, the following configuration includes events with a 'Debug' or higher log level
 * that are logged with a TCategoryName type from the 'Acme.*' namespace:

   "XperienceEventLog": {
     "LogLevel": {
       "Default": "Warning",
       ...
       "Acme" : "Debug"
*/

// Logs events with various logging levels into the event log:

// The Trace, Debug and Information log levels appear as the 'Info' event type in the Xperience event log
// By default, events with these log levels are not written to the Xperience event log
// Can be included by setting the corresponding log level for your event categories in the 'XperienceEventLog' logging provider (appsettings.json)
logger.LogTrace("Trace level event description.");
logger.LogDebug("Debug level event description.");
logger.LogInformation("Information level event description.");
// The Warning log level appears as the 'Warning' event type
logger.LogWarning("Warning event description.");
// The Error and Critical log levels appear as the 'Error' event type
logger.LogError("Error event description.");
logger.LogCritical("Critical event description");

// Logs an event with a specific event code via the 'name' value of an EventId object
// If the EventId parameter is omitted, the event Source is used as the event code
// The 'id' value of the EventId object is not used by the Xperience event log, but may affect other logging providers
logger.LogInformation(new EventId(0, "APIEXAMPLE INFO"), "Information event description.");

// Logs an event (error) with exception details
logger.LogError(new EventId(0, "APIEXAMPLE EXCEPTION"), new Exception(), "Exception event description.");

// Logging policy that ensures the event is logged only once during the application's lifetime
// The system checks whether the event has already been logged based on the identifier of the 'OnlyOnce' method
logger.LogWithIntervalPolicy(LoggingIntervalPolicy.OnlyOnce("api-example-once-per-lifetime"),
    log => log.LogInformation("Event logged only once."));

// Logging policy that ensures the event is logged at most once over a specified time interval (5 minutes in this case)
// The system checks whether the event has already been logged based on the identifier of the 'OncePerPeriod' method
logger.LogWithIntervalPolicy(LoggingIntervalPolicy.OncePerPeriod("api-example-once-per-5-minutes", TimeSpan.FromMinutes(5)),
    log => log.LogInformation("Event logged at most once every 5 minutes."));
```

[> Back to list of examples](#toc)
### Work with events (sending events by email)
C#
Copy
```
// Gets all error events logged in the past day
var errors = eventLogInfoProvider.Get()
                                .WhereEquals("EventType", EventType.ERROR)
                                .WhereGreaterThan("EventTime", DateTime.Now.Subtract(TimeSpan.FromDays(1)));

if (errors.Count > 0)
{
    // Creates the email message
    EmailMessage msg = new EmailMessage()
    {
        From = "system@localhost.local",
        Recipients = "admin@localhost.local",
        Subject = $"Xperience Errors ({errors.Count})",
        Body = "<html><body><ul>"
    };

    // Creates a list of the errors
    foreach (EventLogInfo errorEvent in errors)
    {
        msg.Body += $"<li>{errorEvent.EventType} - {errorEvent.EventCode} - {errorEvent.EventDescription}</li>";
    }

    msg.Body += "</ul></body></html>";

    // Email sending is performed using an instance of 'IEmailService', typically obtained using dependency injection
    // Sends out the email message
    emailService.SendEmail(msg);
}
```

[> Back to list of examples](#toc)
![]()
[]()[]()
