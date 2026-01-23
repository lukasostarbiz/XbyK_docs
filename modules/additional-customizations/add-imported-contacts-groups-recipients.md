---
source: https://docs.kentico.com/modules/additional-customizations/add-imported-contacts-groups-recipients
scrape_date: 2026-01-22
---

Module: Additional customizations
5 of 6 Pages
# Add imported contacts to contact groups and recipient lists
We’ve covered the process of [importing contacts in bulk](/modules/additional-customizations/import-objects-with-the-api), but we’ve yet to walk through the process of _doing something_ with those contacts once they are in your Xperience project.
Let’s create a condition-based [contact group](/documentation/business-users/digital-marketing/contact-groups) that segments imported contacts based on a value provided from the external system we imported them from, then propagate them to a [recipient list](/documentation/business-users/digital-marketing/emails/send-regular-emails-to-subscribers#create-recipient-lists).
To keep this example concise, we’ll only create these objects for _one of the segments_ from our external system (with the identifier `c2e5f9a1-7b4d-4e8c-a3f6-9d1b5c8e2a7f`), but **the same approach applies even if you have several segments**.
The code samples in this sample exist in the **~/Features/ContactImport** folder of the **TrainingGuides.Web** project and its subdirectories.
## Create a contact group
The code samples throughout this scenario use event logging IDs from [EventIds.cs](https://github.com/Kentico/xperience-by-kentico-training-guides/blob/finished/src/TrainingGuides.Web/Features/Shared/Logging/EventIds.cs) and values from [ContactishValues.cs](https://github.com/Kentico/xperience-by-kentico-training-guides/blob/finished/src/TrainingGuides.Web/Features/ContactImport/ContactishValues.cs)
For the sake of brevity, we’re linking the full completed versions here, rather than including a code block each time a new value is added throughout the scenario. If you’re following along, we recommend copying these files into your project first.
Xperience primarily segments contacts into groups based on dynamic conditions. Let’s use this to sort our imported contacts into groups corresponding to their segments in Contactish.
Make sure to include the `TrainingGuidesContactishSegmentIdentifiers` field in the **Contact edit** UI form for the **Contact** class in the **Contact management** module, so that contact group conditions can use it.
Then, create a contact group for contacts with a specific identifier in that field. You can do it in the UI, or in code.
In our example, we upsert the group each time the contact import runs, to ensure the group is defined exactly as how our code expects.
C#
**./Services/IContactishContactImportService.cs**
Copy
```
using System.Xml;
using CMS.ContactManagement;

namespace TrainingGuides.Web.Features.ContactImport;

public interface IContactImportService
{
    ...

    /// <summary>
    /// Ensures that the contact group(s) for imported contacts exists in the database.
    /// </summary>
    /// <param name="rebuildContactGroup">Indicates whether to rebuild the contact group after ensuring its existence</param>
    Task EnsureContactGroup(bool rebuildContactGroup);

    /// <summary>
    /// Gets a contact group by its code name from the database.
    /// </summary>
    /// <param name="contactGroupCodeName">Code name of the contact group to fetch</param>
    /// <returns>The contact group if found, otherwise null</returns>
    Task<ContactGroupInfo?> GetContactGroup(string contactGroupCodeName);

    ...
```

C#
**./Services/ContactishContactImportService.cs**
Copy
```
using System.Xml;
using CMS.Base;
using CMS.ContactManagement;
using CMS.DataEngine;
using CMS.DataEngine.Query;
using CMS.EmailMarketing;
using CMS.Helpers;
using TrainingGuides.Web.Features.Shared.Logging;

namespace TrainingGuides.Web.Features.ContactImport;

public class ContactishContactImportService(IInfoProvider<ContactInfo> contactInfoProvider,
    IInfoProvider<ContactGroupInfo> contactGroupInfoProvider,
    IInfoProvider<RecipientListSettingsInfo> recipientListSettingsInfoProvider,
    IInfoProvider<ContactGroupMemberInfo> contactGroupMemberInfoProvider,
    IInfoProvider<EmailSubscriptionConfirmationInfo> emailSubscriptionConfirmationInfoProvider,
    IProgressiveCache progressiveCache,
    ICacheDependencyBuilderFactory cacheDependencyBuilderFactory,
    ILogger<ContactishContactImportService> logger) : IContactImportService
{

    // Note: This is an expensive operation, use it sparingly.
    private async Task RebuildContactGroup(ContactGroupInfo contactGroup)
    {
        contactGroup.ContactGroupStatus = ContactGroupStatusEnum.Rebuilding;
        contactGroup.Generalized.SetObject();

        // Invoke in new thread
        await Task.Factory.StartNew(CMSThread.Wrap(() =>
            {
                try
                {
                    if (contactGroup.ContactGroupStatus != ContactGroupStatusEnum.Rebuilding)
                    {
                        // Set status that the contact group is being rebuilt
                        contactGroup.ContactGroupStatus = ContactGroupStatusEnum.Rebuilding;
                        contactGroup.Update();
                    }

                    new ContactGroupRebuilder().RebuildGroup(contactGroup);
                }
                catch (Exception ex)
                {
                    logger.LogError(EventIds.ContactGroupRebuildFailed, ex, "Failed to rebuild contact group {ContactGroupName}.", contactGroup.ContactGroupName);
                    throw;
                }
                finally
                {
                    // Return to ready status
                    contactGroup.ContactGroupStatus = ContactGroupStatusEnum.Ready;
                    contactGroup.Update();
                }
            }),
            TaskCreationOptions.LongRunning);
    }

    ...

    /// <summary>
    /// <inheritdoc/>
    /// Populates data based on <see cref="ContactishValues"/>
    /// </summary>
    public async Task EnsureContactGroup(bool rebuildContactGroup)
    {
        ContactGroupInfo contactGroup;

        var existingGroup = await GetContactGroup(ContactishValues.ContactGroupName);

        if (existingGroup is null)
        {
            var newGroup = new ContactGroupInfo()
            {
                ContactGroupName = ContactishValues.ContactGroupName,
                ContactGroupDisplayName = ContactishValues.ContactGroupDisplayName,
                ContactGroupDescription = ContactishValues.ContactGroupDescription,
                ContactGroupDynamicCondition = ContactishValues.ContactGroupDynamicCondition,
                ContactGroupEnabled = true,
                ContactGroupIsRecipientList = false
            };
            newGroup.Insert();
            contactGroup = newGroup;
        }
        else
        {
            existingGroup.ContactGroupName = ContactishValues.ContactGroupName;
            existingGroup.ContactGroupDescription = ContactishValues.ContactGroupDescription;
            existingGroup.ContactGroupDynamicCondition = ContactishValues.ContactGroupDynamicCondition;
            existingGroup.ContactGroupEnabled = true;
            existingGroup.ContactGroupIsRecipientList = false;

            existingGroup.Update();
            contactGroup = existingGroup;
        }

        if (rebuildContactGroup)
        {
            await RebuildContactGroup(contactGroup);
        }
    }

    ...

    /// <inheritdoc/>
    public async Task<ContactGroupInfo?> GetContactGroup(string contactGroupCodeName)
    {
        var contactGroup = await contactGroupInfoProvider.GetAsync(contactGroupCodeName);

        return contactGroup;
    }
}
```

C#
**./ScheduledTasks/ContactishContactImportScheduledTask.cs**
Copy
```
using System.Xml;
using CMS.Scheduler;
using TrainingGuides.Web.Features.ContactImport;
using TrainingGuides.Web.Features.Shared.Logging;

...

public class ContactishContactImportScheduledTask(
    ILogger<ContactishContactImportScheduledTask> logger,
    IContactImportService contactImportService,
    IWebHostEnvironment webHostEnvironment) : IScheduledTask
{
    ...

    public async Task<ScheduledTaskExecutionResult> Execute(ScheduledTaskConfigurationInfo taskConfiguration, CancellationToken cancellationToken)
    {
        ...
        foreach (string xmlFile in xmlFiles)
        {
            ...
            // Ensure the Contactish contact group exists, and rebuild the group to populate it with imported contacts
            try
            {
                await contactImportService.EnsureContactGroup(rebuildContactGroup: true);
            }
            catch (Exception ex)
            {
                logger.LogError(EventIds.EnsureContactGroupError,
                    ex,
                    "An error occurred while ensuring the contact group exists.");

                return await Task.FromResult(new ScheduledTaskExecutionResult(ex.Message));
            }
            ...
        }
        return await Task.FromResult(ScheduledTaskExecutionResult.Success);
    }
}
```

**Dealing with dynamic conditions in code**
A contact group’s Dynamic condition can be difficult to define from scratch, with a rather complicated macro value.
You can create a dummy contact group and copy its condition from the `OM_ContactGroup` table of the database. Just remove the signature (starting with the `|` character and ending just before the closing `%}` sequence) from the end of the macro.
Make sure to properly escape the `\` and `"` characters, and note that in the database, some quotes are already escaped while others are not.
C#
**./ContactishValues.cs**
Copy
```
...
internal const string ContactGroupDynamicCondition = "{%Rule(\"(Contact.ContactFieldContainsValue(\\\"TrainingGuidesContactishSegmentIdentifiers\\\"," +
    "\\\"Contains\\\", \\\"c2e5f9a1-7b4d-4e8c-a3f6-9d1b5c8e2a7f\\\"))\", \"<rules><r pos=\\\"0\\\" par=\\\"\\\" op=\\\"and\\\" /><r pos=\\\"0\\\"" +
    " par=\\\"0\\\" op=\\\"and\\\" n=\\\"CMSContactFieldContainsValue\\\" ><p n=\\\"op\\\"><t>contains</t><v>Contains</v><r>1</r>" +
    "<d>select operator</d><vt>text</vt><tv>0</tv></p><p n=\\\"field\\\"><t>#select field</t><v>TrainingGuidesContactishSegmentIdentifiers</v>" +
    "<r>1</r><d>select field</d><vt>text</vt><tv>0</tv></p><p n=\\\"value\\\"><t>#enter value</t><v>c2e5f9a1-7b4d-4e8c-a3f6-9d1b5c8e2a7f</v>" +
    "<r>1</r><d>enter value</d><vt>text</vt><tv>0</tv></p></r></rules>\")%}";
...
```

## Create a recipient list
As with the contact group, let’s create our recipient list in code as well.
A recipient list is represented by a `ContactGroupInfo` object (`OM_ContactGroup` in the DB) with `ContactGroupIsRecipientList` set to `true`, and an associated `RecipientListSettingsInfo` object (`EmailLibrary_RecipientListSettings` in the DB).
Since recipient lists are not based on dynamic conditions, we don’t have to worry about rebuilding them.
To distinguish the two groups, we’ll refer to the condition-based contact group as the **CB contact group** and the recipient list as the **recipient contact group** where applicable.
C#
**./Services/IContactishContactImportService.cs**
Copy
```
...

/// <summary>
/// Ensures that the recipient list(s) for imported contacts exists in the database (both <see cref="ContactGroupInfo"/> and <see cref="CMS.EmailMarketing.RecipientListSettingsInfo"/>).
/// </summary>
Task EnsureRecipientList();

...
```

C#
**./Services/ContactishContactImportService.cs**
Copy
```
...
/// <summary>
/// <inheritdoc/>
/// Populates data based on <see cref="ContactishValues"/>
/// </summary>
public async Task EnsureRecipientLists()
{
    var existingGroup = await GetContactGroup(ContactishValues.RecipientListName);

    ContactGroupInfo contactGroup;

    if (existingGroup is null)
    {
        var newGroup = new ContactGroupInfo()
        {
            ContactGroupName = ContactishValues.RecipientListName,
            ContactGroupDisplayName = ContactishValues.RecipientListDisplayName,
            ContactGroupDescription = ContactishValues.RecipientListDescription,
            ContactGroupDynamicCondition = string.Empty,
            ContactGroupIsRecipientList = true
        };

        newGroup.Insert();

        contactGroup = newGroup;
    }
    else
    {
        existingGroup.ContactGroupName = ContactishValues.RecipientListName;
        existingGroup.ContactGroupDescription = ContactishValues.RecipientListDescription;
        existingGroup.ContactGroupDynamicCondition = string.Empty;

        existingGroup.Update();

        contactGroup = existingGroup;
    }

    await EnsureRecipientListSettings(contactGroup);
}

/// <summary>
/// Ensures that the recipient list settings exist for the specified recipient list contact group, and creates or updates them as necessary.
/// </summary>
/// <param name="recipientList">ContactGroupInfo representing a recipient list</param>
public async Task EnsureRecipientListSettings(ContactGroupInfo? recipientList)
{
    if (recipientList is null)
    {
        return;
    }

    var existingListSettings = (await recipientListSettingsInfoProvider.Get()
        .WhereEquals(nameof(RecipientListSettingsInfo.RecipientListSettingsRecipientListID), recipientList.ContactGroupID)
        .GetEnumerableTypedResultAsync())
        .FirstOrDefault();

    if (existingListSettings is null)
    {
        var settings = new RecipientListSettingsInfo
        {
            RecipientListSettingsRecipientListID = recipientList.ContactGroupID,

            RecipientListSettingsAfterConfirmationPage = ContactishValues.RecipientListThankYouPageGuid,
            RecipientListSettingsSendSubscriptionConfirmationEmail = false,
            RecipientListSettingsSubscriptionConfirmationEmailID = default,

            RecipientListSettingsAfterUnsubscriptionPage = ContactishValues.RecipientListGoodbyePageGuid,
            RecipientListSettingsSendUnsubscriptionConfirmationEmail = false,
            RecipientListSettingsUnsubscriptionConfirmationEmailID = default,

        };

        settings.Insert();
    }
    else
    {
        existingListSettings.RecipientListSettingsAfterConfirmationPage = ContactishValues.RecipientListThankYouPageGuid;
        existingListSettings.RecipientListSettingsSendSubscriptionConfirmationEmail = false;
        existingListSettings.RecipientListSettingsSubscriptionConfirmationEmailID = default;

        existingListSettings.RecipientListSettingsAfterUnsubscriptionPage = ContactishValues.RecipientListGoodbyePageGuid;
        existingListSettings.RecipientListSettingsSendUnsubscriptionConfirmationEmail = false;
        existingListSettings.RecipientListSettingsUnsubscriptionConfirmationEmailID = default;

        existingListSettings.Update();
    }
}
...
```

C#
**./ScheduledTasks/ContactishContactImportScheduledTask.cs**
Copy
```
...

public class ContactishContactImportScheduledTask(
    ILogger<ContactishContactImportScheduledTask> logger,
    IContactImportService contactImportService,
    IWebHostEnvironment webHostEnvironment) : IScheduledTask
{
    ...

    public async Task<ScheduledTaskExecutionResult> Execute(ScheduledTaskConfigurationInfo taskConfiguration, CancellationToken cancellationToken)
    {
        ...
        foreach (string xmlFile in xmlFiles)
        {
            ...
            // Ensure the Contactish recipient lists exist, so that we can add contacts to them
            try
            {
                await contactImportService.EnsureRecipientLists();
            }
            catch (Exception ex)
            {
                logger.LogError(EventIds.EnsureRecipientListError,
                    ex,
                    "An error occurred while ensuring the recipient list exists.");

                return await Task.FromResult(new ScheduledTaskExecutionResult(ex.Message));
            }
        }
        return await Task.FromResult(ScheduledTaskExecutionResult.Success);
    }
}
```

## Synchronize contacts with recipients
In our scenario, we consider the condition-based contact group to be the _source of truth_ for the recipient list, which means the following:
  1. If the CB contact group contains a member that is not in the recipient list, we should add it to the recipient list.
  2. If the recipient list contains a member that is not in the CB contact group, we should remove it from the recipient list.


While adding and removing recipients, keep in mind that two components are necessary for a contact to be a functioning recipient who receives mail:
  1. A `ContactGroupMemberInfo` binding (`OM_ContactGroupMember` in the DB) between the contact and the recipient contact group
  2. An `EmailSubscriptionConfirmationInfo` binding (`EmailLibrary_EmailSubscriptionConfirmation` in the DB) between the contact and the recipient contact group, with `EmailSubscriptionConfirmationIsApproved` set to `true`.


Let’s ensure that our service has the tools to handle this functionality.
We’ll design our code so that we can use it in an [ongoing integration with the external system](/documentation/developers-and-admins/integrate-with-decoupled-systems), if we decide to expand it in that direction in the future. We’ll make sure our code can update recipients that already exist, rather than exclusively inserting new records.
C#
**./Services/IContactishContactImportService.cs**
Copy
```
...
/// <summary>
/// Gets the Contact Group ID from cache or database if not found in cache.
/// </summary>
/// <param name="contactGroupCodeName">Code name of the contact group to fetch</param>
/// <returns>The contact group if found, otherwise null</returns>
Task<ContactGroupInfo?> GetContactGroupCached(string contactGroupCodeName);

/// <summary>
/// Creates or updates a contact group member binding for a recipient list, corresponding to the provided standard contact group member.
/// </summary>
/// <param name="contactGroupMember">Member of the contact group to copy to the recipient list</param>
/// <param name="recipientListContactGroupId">ID of the recipient list contact group</param>
Task CreateOrUpdateRecipient(int contactId, int recipientListContactGroupId);

/// <summary>
/// Checks whether the contact is already in the specified recipient list.
/// </summary>
/// <param name="contactId">ID of contact to check</param>
/// <param name="recipientListContactGroupId">ID of the recipient list contact group</param>
/// <returns>True if a contact group member exists for the specified contact and recipient list</returns>
Task<bool> ContactAlreadyInGroup(int contactId, int recipientListContactGroupId);

/// <summary>
/// Upserts a recipient in the recipient list based on the provided contact group member.
/// </summary>
/// <param name="contactId">ID of the contact to upsert as a recipient</param>
Task UpsertRecipient(int contactId);

/// <summary>
/// Gets members one contact group that are not in another contact group.
/// </summary>
/// <param name="contactGroupX">The contact group to get members from</param>
/// <param name="contactGroupY">The contact group to compare against</param>
/// <param name="topN">The maximum number of members to return</param>
/// <returns>A collection of contact IDs</returns>
Task<IEnumerable<int>> GetGroupXMembersNotInGroupY(ContactGroupInfo contactGroupX,
    ContactGroupInfo contactGroupY,
    int topN);

/// <summary>
/// Logs a warning that the specified contact group was not found.
/// </summary>
/// <param name="contactGroupName">The name of the contact group that was not found</param>
void LogMissingContactGroup(string contactGroupName);

/// <summary>
/// Deletes the specified contacts from the specified recipient list, including their subscription confirmations and contact group member bindings.
/// </summary>
/// <param name="contactIds">IDs of the contacts to remove</param>
/// <param name="recipientListContactGroupId">ID of the recipient list contact group</param>
Task DeleteRecipients(IEnumerable<int> contactIds, int recipientListContactGroupId);
...
```

C#
**./Services/ContactishContactImportService.cs**
Copy
```
...
    /// <inheritdoc/>
    public async Task<ContactGroupInfo?> GetContactGroupCached(string contactGroupCodeName) =>
        await progressiveCache.LoadAsync(
            async cacheSettings =>
            {
                cacheSettings.Cached = true;
                var contactGroup = await GetContactGroup(contactGroupCodeName);

                if (contactGroup is not null)
                {
                    cacheSettings.CacheDependency = cacheDependencyBuilderFactory.Create()
                        .ForInfoObjects<ContactGroupInfo>()
                            // Clear the cache when any contact group is created, updated, or deleted
                            // .ByCodename could also work here, since we know what the code name will be. However, .ById and .ByGuid will only cover updates and deletions, not creations.
                            .All()
                            .Builder()
                        .Build();
                }

                return contactGroup;
            },
            new CacheSettings(cacheMinutes: 60,
                useSlidingExpiration: true,
                cacheItemNameParts: [nameof(ContactishContactImportService),
                    nameof(GetContactGroupCached),
                    contactGroupCodeName]));

    /// <inheritdoc/>
    public async Task CreateOrUpdateRecipient(int contactId, int recipientListContactGroupId)
    {

        var existingRecipient = (await contactGroupMemberInfoProvider.Get()
            .WhereEquals(nameof(ContactGroupMemberInfo.ContactGroupMemberRelatedID), contactId)
            .WhereEquals(nameof(ContactGroupMemberInfo.ContactGroupMemberType), ContactGroupMemberTypeEnum.Contact)
            .WhereEquals(nameof(ContactGroupMemberInfo.ContactGroupMemberContactGroupID), recipientListContactGroupId)
            .GetEnumerableTypedResultAsync()).FirstOrDefault();

        if (existingRecipient is null)
        {
            var recipient = new ContactGroupMemberInfo()
            {
                // Use the same contact for contact group binding
                ContactGroupMemberRelatedID = contactId,
                ContactGroupMemberType = ContactGroupMemberTypeEnum.Contact,

                // Bind the object to the recipient list contact group
                ContactGroupMemberContactGroupID = recipientListContactGroupId,

                // Indicate that the contact was added manually (not via dynamic condition)
                ContactGroupMemberFromManual = true,

            };

            recipient.Insert();

            await EnsureSubscriptionConfirmation(contactId, recipientListContactGroupId);
        }
        else
        {
            // Update the existing recipient, only touch the values we don't already know from the where condition of the query.
            existingRecipient.ContactGroupMemberFromManual = true;
            existingRecipient.ContactGroupMemberFromCondition = false;
            existingRecipient.ContactGroupMemberFromAccount = false;

            if (existingRecipient.HasChanged)
            {
                existingRecipient.Update();
            }
        }

    }

    /// <summary>
    /// Makes sure that a subscription confirmation exists for the specified contact and recipient list.
    /// </summary>
    /// <param name="contactId">ID of the contact you want to subscribe</param>
    /// <param name="recipientListContactGroupId">The recipient list to subscribe the contact to</param>
    /// <remarks>
    /// You must ensure that the contact has agreed and consented to receive emails in the external system before running this code.
    /// </remarks>
#warning "Make sure to consult your legal team to ensure that your subscriptions are compliant with the laws of your region."
    public async Task EnsureSubscriptionConfirmation(int contactId, int recipientListContactGroupId)
    {
        var existingConfirmation = (await emailSubscriptionConfirmationInfoProvider.Get()
            .WhereEquals(nameof(EmailSubscriptionConfirmationInfo.EmailSubscriptionConfirmationContactID), contactId)
            .WhereEquals(nameof(EmailSubscriptionConfirmationInfo.EmailSubscriptionConfirmationRecipientListID), recipientListContactGroupId)
            .GetEnumerableTypedResultAsync())
            .FirstOrDefault();

        if (existingConfirmation is null)
        {
            var confirmation = new EmailSubscriptionConfirmationInfo()
            {
                EmailSubscriptionConfirmationContactID = contactId,
                EmailSubscriptionConfirmationRecipientListID = recipientListContactGroupId,
                // Assume they are not unsubscribed if we are adding them as a recipient
                EmailSubscriptionConfirmationIsApproved = true,
                // Optionally query the contact or external system for a more accurate date
                EmailSubscriptionConfirmationDate = DateTime.Now,
            };

            confirmation.Insert();
        }
    }

    /// <summary>
    /// Checks if the provided contact is a member of the specified recipient list.
    /// </summary>
    /// <param name="contactId">ID of the contact to check</param>
    /// <param name="recipientListContactGroupId">ID of the recipient list (contact group)</param>
    /// <returns>True if the contact with the provided ID is a member of the specified recipient list</returns>
    public async Task<bool> ContactAlreadyInGroup(int contactId, int recipientListContactGroupId)
    {
        int recipientCount = await contactGroupMemberInfoProvider.Get()
            .WhereEquals(nameof(ContactGroupMemberInfo.ContactGroupMemberRelatedID), contactId)
            .WhereEquals(nameof(ContactGroupMemberInfo.ContactGroupMemberType), ContactGroupMemberTypeEnum.Contact)
            .WhereEquals(nameof(ContactGroupMemberInfo.ContactGroupMemberContactGroupID), recipientListContactGroupId)
            .GetCountAsync();

        return recipientCount > 0;
    }

    /// <inheritdoc/>
    public async Task UpsertRecipient(int contactId)
    {
        int? recipientListContactGroupId = (await GetContactGroupCached(ContactishValues.RecipientListName))?.ContactGroupID;

        if (recipientListContactGroupId is null or 0)
        {
            LogMissingContactGroup(ContactishValues.RecipientListName);
            return;
        }

        await CreateOrUpdateRecipient(contactId, (int)recipientListContactGroupId);
    }

    /// <inheritdoc/>
    public async Task<IEnumerable<int>> GetGroupXMembersNotInGroupY(ContactGroupInfo contactGroupX,
        ContactGroupInfo contactGroupY,
        int topN)
    {
        var GroupYIds = contactGroupMemberInfoProvider.Get()
            .WhereEquals(nameof(ContactGroupMemberInfo.ContactGroupMemberContactGroupID), contactGroupY.ContactGroupID)
            .WhereEquals(nameof(ContactGroupMemberInfo.ContactGroupMemberType), ContactGroupMemberTypeEnum.Contact)
            .Column(nameof(ContactGroupMemberInfo.ContactGroupMemberRelatedID));

        var groupXMembersNotInY = await contactGroupMemberInfoProvider.Get()
            .WhereEquals(nameof(ContactGroupMemberInfo.ContactGroupMemberContactGroupID), contactGroupX.ContactGroupID)
            .WhereNotIn(nameof(ContactGroupMemberInfo.ContactGroupMemberRelatedID), GroupYIds)
            .WhereEquals(nameof(ContactGroupMemberInfo.ContactGroupMemberType), ContactGroupMemberTypeEnum.Contact)
            .TopN(topN)
            .Column(nameof(ContactGroupMemberInfo.ContactGroupMemberRelatedID))
            .GetListResultAsync<int>();

        return groupXMembersNotInY;
    }

    /// <inheritdoc/>
    public void LogMissingContactGroup(string contactGroupName) => logger.LogError(EventIds.ContactGroupNotFound, "Recipient list contact group {ContactGroupName} not found for Contactish recipients.", contactGroupName);

    /// <inheritdoc/>
    public async Task DeleteRecipients(IEnumerable<int> contactIds, int recipientListContactGroupId)
    {
        // Delete the subscription confirmations and contact group members for the specified contacts and recipient list
        // Alternately, you can keep the subscription confirmations, but set EmailSubscriptionConfirmationIsApproved to false to indicate that the contact is unsubscribed
        emailSubscriptionConfirmationInfoProvider.BulkDelete(new WhereCondition()
            .WhereIn(nameof(EmailSubscriptionConfirmationInfo.EmailSubscriptionConfirmationContactID), contactIds)
            .WhereEquals(nameof(EmailSubscriptionConfirmationInfo.EmailSubscriptionConfirmationRecipientListID), recipientListContactGroupId));

        // Delete contact group member bindings for the specified contacts and recipient list
        contactGroupMemberInfoProvider.BulkDelete(new WhereCondition()
            .WhereIn(nameof(ContactGroupMemberInfo.ContactGroupMemberRelatedID), contactIds)
            .WhereEquals(nameof(ContactGroupMemberInfo.ContactGroupMemberContactGroupID), recipientListContactGroupId)
            .WhereEquals(nameof(ContactGroupMemberInfo.ContactGroupMemberType), ContactGroupMemberTypeEnum.Contact));
    }
...
```

With these new methods, we can orchestrate the transfer of CB contact group members to recipients.
C#
**./ScheduledTasks/ContactishRecipientTransferScheduledTask.cs**
Copy
```
using CMS.Scheduler;
using TrainingGuides.Web.Features.ContactImport;

[assembly: RegisterScheduledTask(identifier: ContactishRecipientTransferScheduledTask.IDENTIFIER, typeof(ContactishRecipientTransferScheduledTask))]

namespace TrainingGuides.Web.Features.ContactImport;

public class ContactishRecipientTransferScheduledTask(
    IContactImportService contactImportService) : IScheduledTask
{
    public const string IDENTIFIER = "TrainingGuides.ContactishRecipientTransferScheduledTask";

    // Keep in mind that each contact we transfer will lead to two database queries in the upsert method - one to check if it already exists, and a second to insert the recipient.
    // Choose your batch size - consider being conservative with batch size if you plan to run this task during peak hours, or if you have a large number of contacts.
    public const int BatchSize = 25;

    // This scheduled task moves batches of contacts from the source contact group to the target recipient list, and deletes any contacts from the recipient list that are no longer in the source contact group
    public async Task<ScheduledTaskExecutionResult> Execute(ScheduledTaskConfigurationInfo task, CancellationToken cancellationToken)
    {
        var sourceContactGroup = await contactImportService.GetContactGroupCached(ContactishValues.ContactGroupName);
        var targetRecipientList = await contactImportService.GetContactGroupCached(ContactishValues.RecipientListName);

        if (sourceContactGroup is null)
        {
            contactImportService.LogMissingContactGroup(ContactishValues.ContactGroupName);
            return await Task.FromResult(new ScheduledTaskExecutionResult("Failed - Source contact group not found."));
        }
        if (targetRecipientList is null)
        {
            contactImportService.LogMissingContactGroup(ContactishValues.RecipientListName);
            return await Task.FromResult(new ScheduledTaskExecutionResult("Failed - Recipient list contact group not found."));
        }

        // Get members of source contact group not in target recipient list
        var topUnsyncedRecipients = await contactImportService.GetGroupXMembersNotInGroupY(sourceContactGroup, targetRecipientList, BatchSize);

        // Add these members as recipients in the target recipient list
        foreach (int contactId in topUnsyncedRecipients)
        {
            await contactImportService.UpsertRecipient(contactId);
        }

        // Get members of target recipient list not in source contact group
        // Since the contact group is how recipients are added to the list, this must mean the missing contacts were deleted
        var topDeletableRecipients = await contactImportService.GetGroupXMembersNotInGroupY(targetRecipientList, sourceContactGroup, BatchSize);

        if (topDeletableRecipients.Any())
        {
            // Delete these members from the target recipient list
            // Avoid this approach if you do not want the source contact group to be the source of truth for your recipient list.
            await contactImportService.DeleteRecipients(topDeletableRecipients, targetRecipientList.ContactGroupID);
        }

        return await Task.FromResult(ScheduledTaskExecutionResult.Success);
    }
}
```

## Configure the scheduled task
Now that we’ve defined the `ContactishRecipientTransferScheduledTask` scheduled task, you can start your Xperience by Kentico project, open the **Scheduled tasks** application, and create a new scheduled task configuration to execute it. For example:
  * **Scheduled task configuration name:** Contactish transfer contact groups to recipients
  * **Task implementation:** TrainingGuides.ContactishRecipientTransferScheduledTask
  * **Enabled:** True (enabled)
  * **Start time:** (Any time in the future that you want the task to run for the first time)
  * **Period:** Hour
  * **Every:** 1
  * **Select the days on which the task should run:** True (enabled) for each day


You can also trigger the task to run manually from the **List of scheduled task configurations**.
Once it runs, a number of contacts up to the batch size you specified in code will be transferred to the recipient list from the condition-based contact group.
[ Previous page ](/modules/additional-customizations/import-objects-with-the-api)
5 of 6
[ Mark complete and continue ](/modules/additional-customizations/additional-customizations-learn-more)
![]()
[]()[]()
