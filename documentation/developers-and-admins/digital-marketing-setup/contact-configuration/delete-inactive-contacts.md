---
source: https://docs.kentico.com/documentation/developers-and-admins/digital-marketing-setup/contact-configuration/delete-inactive-contacts
scrape_date: 2026-01-26
---

  * [Home](/documentation)
  * [Developers and admins](/documentation/developers-and-admins)
  * [Digital marketing setup](/documentation/developers-and-admins/digital-marketing-setup)
  * [Contact configuration](/documentation/developers-and-admins/digital-marketing-setup/contact-configuration)
  * Delete inactive contacts 


# Delete inactive contacts
The [contact management](/documentation/business-users/digital-marketing/contact-management) features in Xperience generate a very large amount of data, particularly on high‑traffic websites. The system creates contacts and logs [activities](/documentation/business-users/digital-marketing/contact-activities) for every visitor (depending on the project’s [default cookie level](/documentation/developers-and-admins/data-protection/cookies)), which may be overwhelming for your marketers.
Additionally, an extremely high volume of contact data can lead to performance issues in administration UIs related to contacts, and result in an excessively large database. This increases storage costs and complicates maintenance operations such as backups or restores.
To mitigate these issues, we recommend that you configure the system to regularly delete unnecessary contacts. By default, Xperience allows you to [enable automatic deletion of contacts](#enable-automatic-deletion-of-inactive-contacts) who have not performed any activities over a specified number of days.
**Custom contact deletion**
If the default deletion of inactive contacts does not satisfy your project’s requirements, developers can [implement custom deletion of contacts](#implement-custom-deletion-of-contacts) with any conditions for selecting inactive, outdated or otherwise unnecessary contacts.
## Consequences of deleting contacts
Deleting contacts also removes associated data, notably:
  * [Consent agreements](/documentation/developers-and-admins/data-protection/consent-management) given by the contacts
  * [Activities](/documentation/business-users/digital-marketing/contact-activities) logged for the contacts
  * [Customer journey](/documentation/business-users/digital-marketing/customer-journeys) conversions related to the contacts
  * [Email recipients](/documentation/business-users/digital-marketing/emails/send-regular-emails-to-subscribers) matching the contacts


Only set up automatic contact deletion if you are sure this data will not be needed in the future. In particular, consent agreements may be required to maintain compliance with data protection laws.
## Enable automatic deletion of inactive contacts
To set up the system’s default inactive contact deletion:
  1. Open the **Settings** application in the Xperience administration interface.
  2. Navigate to the **Digital marketing → Contact management** category in the settings tree.
  3. Select the **Delete contacts who have not been active for the last X days** option.
  4. Fill in the **Last X days** value. 
     * The system deletes contacts who do not have any [activities](/documentation/business-users/digital-marketing/contact-activities) logged within the specified number of days, counting into the past from the current date.
     * The minimum value is 7 days.
     * For contacts who do not have any activities logged, the contact’s creation date is considered as the time of the last activity.
  5. **Save** the changes.


The system now starts deleting inactive contacts according to the specified criteria.
### How exactly are contacts deleted?
The system deletes contacts using the **Delete inactive contacts** [scheduled task](/documentation/developers-and-admins/customization/scheduled-tasks). The task runs in a way that does not significantly impact your application’s performance:
  * Starts running once per day at 2 AM in the time zone of the server where the application is running.
  * Deletes contacts in batches of 1000.
  * Waits for 1 minute between each batch.
  * Has a maximum total run time of 4 hours (until 6 AM). If any inactive contacts remain at this point, they stay in the system until the task executes again the following day.


## Implement custom deletion of contacts
If deleting contacts based on inactivity duration doesn’t meet your project’s needs, you can create a custom automated task for contact cleanup. This approach lets you define your own criteria for selecting which contacts are still relevant and which are suitable for deletion.
  1. Make sure the system’s [default inactive contact deletion](#enable-automatic-deletion-of-inactive-contacts) is disabled. 
     * In **Settings → Digital marketing → Contact management** , select the **Do not delete contacts** option.
  2. Create a custom [scheduled task](/documentation/developers-and-admins/customization/scheduled-tasks): 
    1. In the task’s `Execute` method, define a `WhereCondition` to select the contacts you want to delete.
**Where condition resources**
To learn more about the Where condition API, see [Limit retrieved results](/documentation/developers-and-admins/api/objectquery-api#limit-retrieved-results-sql-where). For a full list of the available `Where*` filtering methods, see the [API reference](https://api-reference.kentico.com/api/CMS.DataEngine.WhereConditionBase-1.html). 
    2. Use the `IContactsBulkDeletionService` service to bulk delete contacts who match the condition.
    3. Set a suitable _Start time_ and _Period_ in the [scheduled task configuration](/documentation/developers-and-admins/customization/scheduled-tasks#configure-task-schedule) based on your task’s implementation and requirements.


C#
**Scheduled task that deletes contacts with an empty email address**
Copy
```
using System;
using System.Threading;
using System.Threading.Tasks;
using Microsoft.Extensions.Logging;

using CMS.Core;
using CMS.ContactManagement;
using CMS.DataEngine;
using CMS.Scheduler;

// Registers the scheduled task
[assembly: RegisterScheduledTask(identifier: "Acme.ContactCleanerTask", typeof(CustomContactCleanerTask))]

public class CustomContactCleanerTask : IScheduledTask
{
    private readonly IContactsBulkDeletionService contactDeletionService;
    private readonly ILogger<CustomContactCleanerTask> logger;

    // Constructor that obtains required services using dependency injection
    public CustomContactCleanerTask(IContactsBulkDeletionService contactDeletionService, ILogger<CustomContactCleanerTask> logger)
    {
        this.contactDeletionService = contactDeletionService;
        this.logger = logger;
    }

    // Called by the system when running the scheduled task
    public async Task<ScheduledTaskExecutionResult> Execute(ScheduledTaskConfigurationInfo task, CancellationToken cancellationToken)
    {
        var dateOneWeekAgo = DateTime.Now.AddDays(-7);

        try
        {
            // WhereCondition that selects contacts that are at least 7 days old and have an empty email address
            var condition = new WhereCondition()
                            .WhereLessThan(nameof(ContactInfo.ContactCreated), dateOneWeekAgo)
                            .And()
                            .WhereEmpty(nameof(ContactInfo.ContactEmail));

            // Deletes contacts matching the specified condition
            // Doesn't specify a batch size, which means that contacts are deleted in batches of 1000 (automatically repeats)
            await contactDeletionService.BulkDelete(condition);

            // Indicates successful task completion
            return ScheduledTaskExecutionResult.Success;
        }
        catch (Exception ex)
        {
            // If an exception occurs while deleting contacts, logs an error with the exception details
            logger.LogError(new EventId(0, nameof(Execute)), ex, "An error occurred while deleting contacts");
            return new ScheduledTaskExecutionResult("An error occurred while deleting contacts. See the event log for detailed information.");
        }
    }
}
```

Your scheduled task now automatically deletes contacts based on your custom selection criteria.
### Manual batch processing for advanced scenarios
If your project has specific performance requirements or limitations, you can take manual control over the batch processing used during contact deletion.
The `IContactsBulkDeletionService.BulkDelete` method can be called with an optional `batchLimit` parameter, which specifies how many contacts are deleted within a single batch. In this case, each call of the method only deletes a single batch, and you need to manually handle batch processing and iteration.
C#
Copy
```
// Required service instances (obtained using dependency injection)
private readonly IInfoProvider<ContactInfo> contactInfoProvider;
private readonly IContactsBulkDeletionService contactDeletionService;

// ...

// Gets the total number of contacts matching the deletion condition
int numberOfContactsToBeDeleted = contactInfoProvider.Get().Where(condition).Count;

// Deletes matching contacts in batches of 500
int batchSize = 500;
int deletedCount = 0;

while (deletedCount < numberOfContactsToBeDeleted)
{
    // Deletes a batch of contacts
    await contactDeletionService.BulkDelete(condition, batchSize);
    deletedCount += batchSize;

    // Adds a small delay between batches
    await Task.Delay(1000);
}
```

![]()
[]()[]()
