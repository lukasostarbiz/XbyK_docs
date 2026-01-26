---
source: https://docs.kentico.com/documentation/developers-and-admins/customization/handle-global-events/reference-global-system-events
scrape_date: 2026-01-26
---

  * [Home](/documentation)
  * [Developers and admins](/documentation/developers-and-admins)
  * [Customization](/documentation/developers-and-admins/customization)
  * [Handle global events](/documentation/developers-and-admins/customization/handle-global-events)
  * Reference - Global system events 


# Reference - Global system events
Each section below represents an event category (class) that serves as an entry point for accessing that specific group of events.
## ObjectEvents
**Namespace** : CMS.DataEngine
Contains events that occur for all types of objects in the system. See also: [Handle object events](/documentation/developers-and-admins/customization/handle-global-events/handle-object-events)
**Important**
Use the following classes to access the object events in the API:
  * **ObjectEvents** – events triggered for all object types
  * **< name>Info.TYPEINFO.Events** – events triggered only for a specific object type, for example: `UserInfo.TYPEINFO.Events`


Event |  Event types |  Handler parameters |  Description  
---|---|---|---  
Insert |  Before  
After  
Failure |  ObjectEventArgs **Properties**
  * Object (BaseInfo)

|  **Note** : We recommend using the newer [event handlers with async support](/documentation/developers-and-admins/customization/handle-global-events/handle-object-events) for _Insert_ object events. Occurs when a new object is created. To reliably handle all possible object creation events, you may also need to assign a matching handler to the **BulkInsert** event. The system triggers the _BulkInsert_ event instead of _Insert_ in certain cases, particularly for operations that can affect multiple objects.  
Update |  Before  
After  
Failure |  ObjectEventArgs **Properties**
  * Object (BaseInfo)

|  **Note** : We recommend using the newer [event handlers with async support](/documentation/developers-and-admins/customization/handle-global-events/handle-object-events) for _Update_ object events. Occurs when the data of an existing object is updated. To reliably handle all possible object update events, you may also need to assign a matching handler to the **BulkUpdate** event. The system triggers the _BulkUpdate_ event instead of _Update_ in certain cases, particularly for operations that can affect multiple objects.  
Delete |  Before  
After  
Failure |  ObjectEventArgs **Properties**
  * Object (BaseInfo)

|  **Note** : We recommend using the newer [event handlers with async support](/documentation/developers-and-admins/customization/handle-global-events/handle-object-events) for _Delete_ object events. Occurs when an object is deleted. To reliably handle all possible object deletion events, you may also need to assign a matching handler to the **BulkDelete** event. The system triggers the _BulkDelete_ event instead of _Delete_ in certain cases, particularly for operations that can affect multiple objects.  
BulkInsert |  Before  
After  
Failure |  BulkInsertEventArgs **Properties**
  * InsertedObjects (IEnumerable<IInfo>)
  * TypeInfo (ObjectTypeInfo)

|  Occurs when the system performs object creation operations that can affect multiple objects. To reliably handle all possible object creation events, assign matching handlers to both the **BulkInsert** and **Insert** events.  
BulkUpdate |  Before  
After  
Failure |  BulkUpdateEventArgs **Properties**
  * ChangedColumns (IEnumerable<string>)
  * TypeInfo (ObjectTypeInfo)
  * WhereCondition (IWhereCondition)

|  Occurs when the system performs object update operations that can affect multiple objects. To reliably handle all possible object update events, assign matching handlers to both the **BulkUpdate** and **Update** events.  
BulkDelete |  Before  
After  
Failure |  BulkDeleteEventArgs **Properties**
  * TypeInfo (ObjectTypeInfo)
  * WhereCondition (IWhereCondition)

|  Occurs when the system performs object deletion operations that can affect multiple objects. To reliably handle all possible object deletion events, assign matching handlers to both the **BulkDelete** and **Delete** events.  
Sort |  Before  
After  
Failure |  ObjectSortEventArgs **Properties**
  * Object (BaseInfo)
  * Ascending (bool)
  * OrderColumn (string)
  * NameColumn (string)

|  Occurs when sorting of objects is requested.  
ChangeOrder |  Before  
After  
Failure |  ObjectChangeOrderEventArgs **Properties**
  * Object (BaseInfo)
  * NewOrder (int)
  * RelativeOrder (int)
  * OrderColumn (string)

|  Occurs when a change in the order of objects is requested.  
GetData |  Before  
After  
Failure |  ObjectDataEventArgs **Properties**
  * Data (DataSet)
  * Query (IObjectQuery)
  * TotalRecords (int)

|  Occurs when the system retrieves object data using the ObjectQuery API. The **Before** event type is suitable, for example, when you want to retrieve data from an external source. Assign the external data into the **Data** property of the handler’s _ObjectDataEventArgs_ parameter, which is empty by default in the Before event. Use the **After** event type if you need to edit the data retrieved from Xperience in the **Data** property. The **Total** **Records** property provides a number of total records in case paging is used. Use _-1_ to use the rows count of the **Data** property.  
## ContentItemEvents
**Namespace** : CMS.ContentEngine
Contains events that occur when changes are made to [content items](/documentation/business-users/content-hub/content-items).
Event |  Event types |  Handler |  Handler parameters |  Description  
---|---|---|---|---  
Create |  Before  
After  
Failure |  Advanced handler |  CreateContentItemEventArgs |  Occurs when a new content item (not a language variant) is created.  
CreateLanguageVariant |  Before  
After  
Failure |  Advanced handler |  CreateContentItemLanguageVariantEventArgs |  Occurs when a new language variant of an existing content item is created.  
UpdateDraft |  Before  
After  
Failure |  Advanced handler |  UpdateContentItemDraftEventArgs |  Occurs when a draft is updated.  
Publish |  Execute |  Basic handler |  PublishContentItemEventArgs |  Occurs when an item is published. Also triggers when the system publishes an item that was [scheduled to be published](/documentation/business-users/content-hub/content-items#scheduled-publishing). The scheduling action itself triggers the `UpdateLanguageMetadata` event for the item.  
Unpublish |  Execute |  Basic handler |  UnpublishContentItemEventArgs |  Occurs when an item is unpublished. Also triggers when the system unpublishes an item that was scheduled to be unpublished. The scheduling action itself triggers the `UpdateLanguageMetadata` event for the item.  
Delete |  Execute |  Basic handler |  DeleteContentItemEventArgs |  Occurs when an item is deleted.  
UpdateMetadata |  Before  
After  
Failure |  Advanced handler |  UpdateContentItemMetadataEventArgs |  Occurs when an item’s metadata is updated (e.g., security settings).   
UpdateLanguageMetadata |  Before  
After  
Failure |  Advanced handler |  UpdateContentItemLanguageMetadataEventArgs |  Occurs when an item’s [language](/documentation/developers-and-admins/configuration/languages) variant-specific metadata is updated (e.g., administration display name). This metadata includes the time when an item is [scheduled to be published](/documentation/business-users/content-hub/content-items#scheduled-publishing) or unpublished. As a result, scheduling an item to be published or unpublished (or canceling a scheduled publish action) triggers the `UpdateLanguageMetadata` event, not `Publish` or `Unpublish`. These events are triggered when the system actually publishes or unpublishes the item.  
**General information**
Events are not cancelable. If you try to cancel an event, an exception is thrown instead. You can also throw exceptions to stop the corresponding operation. For example, an exception thrown from an event while creating an object will roll back the transaction.
**Event handlers**
**Advanced handler**
Events handled by advanced handlers expose property setters in their event args that can be used to further modify the item before it is stored in the database. Content type-specific data can be updated via the `ContentItemData` property, if present.
**Basic handler**
Events handled by basic handlers are only used for notification.
## WebPageEvents
**Namespace** : CMS.Websites
Contains events that occur when changes are made to [pages](/documentation/business-users/website-content).
Event |  Event types |  Handler |  Handler parameters |  Description  
---|---|---|---|---  
Create |  Before  
After  
Failure |  Advanced handler |  CreateWebPageLanguageVariantEventArgs |  Occurs when a new page is created.  
CreateFolder |  Before  
After  
Failure |  Advanced handler |  CreateFolderLanguageVariantEventArgs |  Occurs when a new folder is created.  
CreateLanguageVariant |  Before  
After  
Failure |  Advanced handler |  CreateWebPageLanguageVariantEventArgs |  Occurs when a new page language variant is created.  
CreateFolderLanguageVariant |  Before  
After  
Failure |  Advanced handler |  CreateFolderLanguageVariantEventArgs |  Occurs when a new folder language variant is created.  
UpdateDraft |  Before  
After  
Failure |  Advanced handler |  UpdateWebPageDraftEventArgs |  Occurs when a page draft is updated.  
Publish |  Execute |  Basic handler |  PublishWebPageEventArgs |  Occurs when a page is published. Also triggers when the system publishes a page that was [scheduled to be published](/documentation/business-users/website-content/edit-and-publish-pages#scheduled-publishing). The scheduling action itself triggers the `UpdateLanguageMetadata` event for the page.  
Unpublish |  Execute |  Basic handler |  UnpublishWebPageEventArgs Also triggers when the system unpublishes a page that was scheduled to be unpublished. The scheduling action itself triggers the `UpdateLanguageMetadata` event for the page. |  Occurs when a page is unpublished.  
Move |  Execute |  Basic handler |  MoveWebPageEventArgs |  Occurs when a page is moved.  
UpdateMetadata |  Before  
After  
Failure |  Advanced handler |  UpdateWebPageMetadataEventArgs |  Occurs when page properties are updated.  
UpdateTreePathSlug |  Before  
After  
Failure |  Advanced handler |  UpdateWebPageTreePathSlugEventArgs |  Occurs when the page URL slug is changed.  
UpdateSecuritySettings |  Before  
After  
Failure |  Advanced handler |  UpdateWebPageSecuritySettingsEventsArgs |  Occurs when page security settings are updated.  
Delete |  Execute |  Basic handler |  DeleteWebPageEventArgs |  Occurs when a page is deleted.  
UpdateLanguageMetadata |  Before  
After  
Failure |  Advanced handler |  UpdateWebPageLanguageMetadataEventArgs |  Occurs when a page’s [language](/documentation/developers-and-admins/configuration/languages) variant-specific metadata is updated (e.g., administration display name). This metadata includes the time when a page is [scheduled to be published](/documentation/business-users/website-content/edit-and-publish-pages#scheduled-publishing) pr unpublished. As a result, scheduling a page to be published or unpublished (or canceling the scheduled publish action) triggers the `UpdateLanguageMetadata` event, not `Publish` or `Unpublish`. These events are triggered when the system actually publishes or unpublishes the page.  
**General information**
Events are not cancelable. If you try to cancel an event, an exception is thrown instead. You can also throw exceptions to stop the corresponding operation. For example, an exception thrown from an event while creating an object will roll back the transaction.
**Event handlers**
**Advanced handler**
Events handled by advanced handlers expose property setters in their event args that can be used to further modify the item before it is stored in the database. Content type-specific data can be updated via the `ContentItemData` property, if present.
**Basic handler**
Events handled by basic handlers are only used for notification.
## HeadlessItemEvents
**Namespace** : CMS.ContentEngine
Contains events that occur when changes are made to [headless items](/documentation/business-users/headless-content).
Event |  Event types |  Handler |  Handler parameters |  Description  
---|---|---|---|---  
Create |  Before  
After  
Failure |  Advanced handler |  CreateHeadlessItemEventArgs |  Occurs when a new headless item (not a language variant) is created.  
CreateLanguageVariant |  Before  
After  
Failure |  Advanced handler |  CreateHeadlessItemLanguageVariantEventArgs |  Occurs when a new language variant of an existing headless item is created.  
UpdateDraft |  Before  
After  
Failure |  Advanced handler |  UpdateHeadlessItemDraftEventArgs |  Occurs when a headless item draft is updated.  
Publish |  Execute |  Basic handler |  PublishHeadlessItemEventArgs |  Occurs when a headless item is published.  
Unpublish |  Execute |  Basic handler |  UnpublishHeadlessItemEventArgs |  Occurs when a headless item is unpublished.  
Delete |  Execute |  Basic handler |  DeleteHeadlessItemEventArgs |  Occurs when a headless item is deleted.  
UpdateMetadata |  Before  
After  
Failure |  Advanced handler |  UpdateHeadlessItemMetadataEventArgs |  Occurs when a headless item’s metadata is updated (e.g., security settings).   
UpdateLanguageMetadata |  Before  
After  
Failure |  Advanced handler |  UpdateHeadlessItemLanguageMetadataEventArgs |  Occurs when a headless item’s language metadata is updated (e.g., admin UI display name).  
**General information**
Events are not cancelable. If you try to cancel an event, an exception is thrown instead. You can also throw exceptions to stop the corresponding operation. For example, an exception thrown from an event while creating an object will roll back the transaction.
**Event handlers**
**Advanced handler**
Events handled by advanced handlers expose property setters in their event args that can be used to further modify the item before it is stored in the database. Content type-specific data can be updated via the `ContentItemData` property, if present.
**Basic handler**
Events handled by basic handlers are only used for notification.
## Workflow events
**Namespace** : CMS.ContentWorkflowEngine
The following classes contain events related to items under [workflow](/documentation/developers-and-admins/configuration/workflows):
  * `WebPageWorkflowEvents` – for [pages](/documentation/business-users/website-content) in website channels.
  * `ContentItemWorkflowEvents` – for reusable content items in the [Content hub](/documentation/business-users/content-hub).
  * `HeadlessItemWorkflowEvents` – for [headless items](/documentation/business-users/headless-content) in headless channels.


Event |  Event types |  Handler parameters |  Description  
---|---|---|---  
MoveToStep |  Execute |  WebPageWorkflowMoveToStepArguments ContentItemWorkflowMoveToStepArguments HeadlessItemWorkflowMoveToStepArguments |  Occurs when a page/content item/headless item is moved from one workflow step to another. The event arguments allow you to access the name of the current and original workflow step, the [user](/documentation/developers-and-admins/configuration/users) who changed the step, and also properties of the given page, content item or headless item (ID, name, content type, language, etc.). If built-in [workflow notifications](/documentation/developers-and-admins/configuration/workflows#workflow-notifications) are enabled for the step the item is moved to, you can also access the comment entered by the user who moved the item. The event is **not** triggered when an item is published or scheduled to be published. For publish transitions, you need to handle the `Publish` or `UpdateLanguageMetadata` event of `WebPageEvents`, `ContentItemEvents` or `HeadlessItemEvents`. **Example** : [Set up custom workflow notifications](/documentation/developers-and-admins/customization/handle-global-events/set-up-custom-workflow-notifications)  
## DataProtectionEvents
**Namespace** : CMS.DataProtection
Contains events related to protection of personal data in the system.
Event |  Event types |  Handler parameters |  Description  
---|---|---|---  
RevokeConsentAgreement |  Execute |  RevokeConsentAgreementEventArgs **Properties**
  * ConsentAgreement (ConsentAgreementInfo)
  * Consent (ConsentInfo)
  * Contact (ContactInfo)

|  Occurs when a consent agreement is revoked. When a contact’s consent agreement is revoked, this event can be used to perform custom actions, such as terminating tracking of the contact or deleting [personal data](/documentation/developers-and-admins/data-protection) related to the contact. **See also** : [Consent management](/documentation/developers-and-admins/data-protection/consent-management), [Consent development](/documentation/developers-and-admins/data-protection/consent-development)  
## EventLogEvents
**Namespace** : CMS.EventLog
Contains events that occur when writing [logged events](/documentation/developers-and-admins/development/logging) into the system’s [event log](/documentation/developers-and-admins/configuration/event-log).
Event |  Event types |  Handler parameters |  Description  
---|---|---|---  
LogEvent |  Before  
After  
Failure |  LogEventArgs **Properties**
  * Event (EventLogInfo)

|  Occurs when the system writes a record into the event log. Only affects events in the built-in Xperience event log, not log entries created via `ILogger` in general. You can use the _Before_ event to customize the data logged for events or cancel logging for certain types of events. The _After_ event allows you to perform custom actions after events are successfully logged. **See also** : [Customize event logging](/documentation/developers-and-admins/development/logging#customize-event-logging)  
## BizFormItemEvents
**Namespace** : CMS.OnlineForms
Contains events related to [form](/documentation/business-users/digital-marketing/forms) actions. See [Handle form events](/documentation/developers-and-admins/customization/handle-global-events/handle-form-events) for an example.
Event |  Event types |  Handler parameters |  Description  
---|---|---|---  
Insert |  Before  
After  
Failure |  BizFormItemEventArgs **Properties**
  * Item (BizFormItem)

|  Occurs when inserting new form items (data records of forms).  
Update |  Before  
After  
Failure |  BizFormItemEventArgs **Properties**
  * Item (BizFormItem)

|  Occurs when updating form data records.  
Delete |  Before  
After  
Failure |  BizFormItemEventArgs **Properties**
  * Item (BizFormItem)

|  Occurs when deleting data records from forms.  
## ContactManagementEvents
**Namespace** : CMS.ContactManagement
Event |  Event types |  Handler parameters |  Description  
---|---|---|---  
ContactInfosDeleted  |  Execute |  ContactInfosDeletedHandlerEventArgs **Properties**
  * DeletedContactsIds (IEnumerable<int>)
  * DeletedContactsGUIDs (IEnumberable<Guid>)

|  Occurs when multiple contacts are deleted in a batch. This event is triggered when calling `IContactsBulkDeletionService.BulkDelete`, for example when [deleting inactive contacts](/documentation/developers-and-admins/digital-marketing-setup/contact-configuration/delete-inactive-contacts). When a single contact is deleted, the _TypeInfoEvents.Delete_ event can be used on _ContactInfo.TYPEINFO_.  
## ActivityEvents
**Namespace** : CMS.Activities
Event |  Event types |  Handler parameters |  Description  
---|---|---|---  
ActivityBulkInsertPerformed |  Before  
After  
Failure |  CMSEventArgs<IList<IActivityInfo>> |  Occurs when the system processes [digital marketing activities](/documentation/business-users/digital-marketing/contact-activities) that are logged in memory and inserts them into the database.  
ActivityProcessedInLogService |  Execute |  CMSEventArgs<IActivityInfo> |  Occurs when a [digital marketing activity](/documentation/business-users/digital-marketing/contact-activities) is logged and processed. At this point, the activity is only stored in the application memory (activities are regularly processed and saved in bulk to the database – see the _ActivityBulkInsertPerformed_ event).  
## SystemEvents
**Namespace** : CMS.Base
Contains general system events.
Event |  Event types |  Handler parameters |  Description  
---|---|---|---  
Exception |  Execute |  SystemEventArgs **Properties**
  * Exception (Exception)
  * LogException (bool)

|  Fires when an unhandled exception occurs in the system.  
## SqlEvents
**Namespace** : CMS.DataEngine
Contains events related to the execution of queries and other database operations.
Event |  Event types |  Handler parameters |  Description  
---|---|---|---  
BulkInsert |  Before  
After  
Failure |  BulkInsertDataEventArgs **Properties**
  * Connection (IDataConnection)
  * InsertSettings (BulkInsertSettings)
  * SourceData (DataTable)
  * TargetTable (string)

|  Occurs when the system creates objects by directly inserting data into a database table.  
ExecuteQuery |  Before  
After  
Failure |  ExecuteQueryEventArgs<DataSet> **Properties**
  * Connection (IDataConnection)
  * Query (QueryParameters)
  * Result (DataSet)

|  Occurs when the system executes a [DataSet](https://learn.microsoft.com/en-us/dotnet/framework/data/adonet/populating-a-dataset-from-a-dataadapter) database query.  
ExecuteNonQuery |  Before  
After  
Failure |  ExecuteQueryEventArgs<int> **Properties**
  * Connection (IDataConnection)
  * Result (DataSet)

|  Occurs when the system executes a non-query database operation.  
ExecuteScalar |  Before  
After  
Failure |  ExecuteQueryEventArgs<object> **Properties**
  * Connection (IDataConnection)
  * Query (QueryParameters)
  * Result (DataSet)

|  Occurs when the system executes a scalar query.  
ExecuteReader |  Before  
After  
Failure |  ExecuteQueryEventArgs<[DbDataReader](https://docs.microsoft.com/en-us/dotnet/api/system.data.common.dbdatareader)> **Properties**
  * Connection (IDataConnection)
  * Result (DataSet)

|  Occurs when the system runs a database [DataReader](https://docs.microsoft.com/en-us/dotnet/framework/data/adonet/dataadapters-and-datareaders), e.g., when the system (or custom code) materializes an [ObjectQuery](/documentation/developers-and-admins/api/objectquery-api) or [content item query](/documentation/developers-and-admins/api/content-item-api/content-item-query-api) call, typically by enumerating the results.  
## Application events
Xperience provides a set of events that you can use to customize the global behavior of the application.
### ApplicationEvents
**Namespace** : CMS.Base
Contains events related to the application lifecycle.
The following table lists the events that **occur once in the application’s lifecycle, in order of execution** :
Event |  Event types |  Handler parameters |  Description  
---|---|---|---  
PreInitialized |  Execute |  [EventArgs](https://docs.microsoft.com/en-us/dotnet/api/system.eventargs) |  Occurs when the application finishes pre-initialization (preparation of things unrelated to the database). Handlers need to be assigned in the **PreInit** method of [custom modules](/documentation/developers-and-admins/customization/run-code-on-application-startup).  
Initialized |  Execute |  [EventArgs](https://docs.microsoft.com/en-us/dotnet/api/system.eventargs) |  Occurs when the application finishes initialization and starts (including all modules and the database connection). Handle this event to perform standard application start events.  
End |  Execute |  [EventArgs](https://docs.microsoft.com/en-us/dotnet/api/system.eventargs) |  Occurs when the application ends.  
The following events occur as needed during the application lifecycle:
Event |  Event types |  Handler parameters |  Description  
---|---|---|---  
Error |  Execute |  [EventArgs](https://docs.microsoft.com/en-us/dotnet/api/system.eventargs) |  Fires when an error occurs in the application.  
### RequestEvents
**Namespace** : CMS.Base
Contains events related to the handling of requests.
The following table lists available events **in the order they are invoked by the application** :
Event |  Event type |  Handler parameters |  Description  
---|---|---|---  
Prepare |  Execute |  [EventArgs](https://docs.microsoft.com/en-us/dotnet/api/system.eventargs) |  Occurs before request processing begins.  
Begin |  Execute |  [EventArgs](https://docs.microsoft.com/en-us/dotnet/api/system.eventargs) |  Occurs when request processing starts.  
PreSendRequestHeaders |  Execute |  [EventArgs](https://docs.microsoft.com/en-us/dotnet/api/system.eventargs) |  Occurs just before ASP.NET sends HTTP headers to the client. **Note** : Added delegates are invoked during [HttpResponse.OnStarting](https://docs.microsoft.com/en-us/dotnet/api/microsoft.aspnetcore.http.httpresponse.onstarting).  
End |  Execute |  [EventArgs](https://docs.microsoft.com/en-us/dotnet/api/system.eventargs) |  Occurs at the end of request processing.  
RunEndRequestTasks |  Execute |  [EventArgs](https://docs.microsoft.com/en-us/dotnet/api/system.eventargs) |  Allows you to execute background tasks at the end of request processing. Running long tasks in this event’s handlers can negatively affect the response time of requests.  
Finalize |  Execute |  [EventArgs](https://docs.microsoft.com/en-us/dotnet/api/system.eventargs) |  Occurs when the request is finalized. Use to clean up and release any resources used by the request. Running long tasks in this event’s handlers can negatively affect the response time of requests.  
![]()
[]()[]()
