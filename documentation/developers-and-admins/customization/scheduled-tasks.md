---
source: https://docs.kentico.com/documentation/developers-and-admins/customization/scheduled-tasks
scrape_date: 2026-01-26
---

  * [Home](/documentation)
  * [Developers and admins](/documentation/developers-and-admins)
  * [Customization](/documentation/developers-and-admins/customization)
  * Scheduled tasks 


# Scheduled tasks
Scheduled tasks in Xperience allow you to automate recurring operations by executing tasks at defined intervals. You can manage the scheduling and configuration of these recurring processes using the **Scheduled tasks** application.
## Create scheduled tasks
Creating scheduled tasks is a two-step process. Each scheduled task consists of:
  * a class that implements the `IScheduledTask` interface
  * a task configuration provided via the _Scheduled tasks_ application


First, create the task class. The entry point for scheduled task logic is the `Execute` method.
  1. Create a new class in your project and implement the `IScheduledTask` interface.
     * We recommend adding custom code to dedicated assemblies. See [Integrate custom code](/documentation/developers-and-admins/customization/integrate-custom-code).
C#
**Example scheduled task implementation**
Copy
```
using System.Threading;
using System.Threading.Tasks;
using Microsoft.Extensions.Logging;

using CMS.Core;
using CMS.Scheduler;

public class HelloScheduledTask : IScheduledTask
{
    private readonly ILogger<HelloScheduledTask> logger;

    // Supports dependency injection to resolve required services
    public HelloScheduledTask(ILogger<HelloScheduledTask> logger)
    {
        this.logger = logger;
    }

    // Called by the system when running the scheduled task
    public Task<ScheduledTaskExecutionResult> Execute(ScheduledTaskConfigurationInfo task, CancellationToken cancellationToken)
    {
        // Logs a warning event
        logger.LogWarning(new EventId(0, "NOTIFICATION"), "Hello from my scheduled task!");

        // Indicates successful task completion
        return Task.FromResult(ScheduledTaskExecutionResult.Success);
    }
}
```

  2. Register the scheduled task using the `RegisterScheduledTask` assembly attribute. You can either pair task registration with its implementation or use a dedicated file. A task must be registered using a unique identifier and its implementing class.
C#
**Scheduled task registration**
Copy
```
// Only registered scheduled tasks can be configured via the 'Scheduled tasks' application.
[assembly: RegisterScheduledTask(identifier: "Acme.HelloScheduledTask", typeof(HelloScheduledTask))]
```

We recommend using a unique prefix in your identifiers to prevent conflicts when deploying components to other projects. For example, use prefixes matching your company name. 


In this example, the task only [logs](/documentation/developers-and-admins/development/logging) a warning event. After configuring the task in the system, you can view the [Event log](/documentation/developers-and-admins/configuration/event-log) application and see the events being logged.
Inside the `Execute` method:
  * Use `ScheduledTaskConfigurationInfo` to access the configuration of the associated task from the **Scheduled tasks** application (e.g., task name and last or next run times).
  * `ScheduledTaskExecutionResult` returns a message describing the task execution result. The returned value is displayed in the _Last result_ column in the _Scheduled tasks_ application. If your task doesn’t execute successfully, we strongly recommend returning a custom value to help with troubleshooting. For successful task runs, you can use `ScheduledTaskExecutionResult.Success` as a shortcut to return an empty string.
[![Task return values displayed in the Last result column](/docsassets/documentation/scheduled-tasks/ScheduledTasksListing.png)](/docsassets/documentation/scheduled-tasks/ScheduledTasksListing.png)
Scheduled tasks are executed in a dedicated worker thread. The code run from the `Execute` method doesn’t have access to contextual information about the application or the current request. 


## Configure scheduled tasks
Scheduled tasks must be configured via the **Scheduled tasks** application. The application pairs registered scheduled task classes with _scheduled task configurations_. Task configurations hold information about the task logic to execute, last task result, next scheduled task run times, and so on.
To create a scheduled task configuration:
  1. Open the **Scheduled tasks** application.
  2. Select **New scheduled task configuration** and enter: 
    1. **Scheduled task configuration name** – display name of the configuration.
    2. **Task implementation** – the task logic to run. Select from task identifiers assigned via `RegisterScheduledTask`. 
       * **NOTE** : The system uses task identifiers to pair a task configuration from the _Scheduled tasks_ application with its logic (the task implementation). Changing the identifier for tasks already assigned via the _Scheduled tasks_ application requires you to reselect the implementation under the new identifier. Tasks that have the renamed implementation assigned error out on their next run otherwise.
    3. **Enabled** – indicates if the task is periodically processed by the system according to the configured **Task schedule**.
    4. **Task schedule** – configured for individual scheduled tasks, and determines when tasks are _ready for execution_. See [Configure task schedule](#configure-task-schedule).
  3. **Save** your changes.


The system now executes the task regularly according to the specified interval.
### Configure task schedule
The **Task schedule** section controls the next run time for the corresponding scheduled task. Next run time determines when tasks are _ready for execution_. The system checks for tasks that are ready to be executed every 30 seconds and:
  * Runs the task.
  * Sets the next run time for the task.


To configure the task interval for individual scheduled tasks:
  1. Open the **Scheduled tasks** application.
  2. Select a scheduled task configuration.
  3. Configure the **Task schedule** section and **Save** your changes.


The task schedule is now changed.
## Scheduled tasks overview
The _Scheduled tasks_ application provides an overview of all tasks currently registered in the system.
[![Scheduled tasks overview](/docsassets/documentation/scheduled-tasks/ScheduledTasksOverview.png)](/docsassets/documentation/scheduled-tasks/ScheduledTasksOverview.png)
  * **Last run** – time of last task run.
  * **Last result** – the last value returned by the `Execute` method. Useful for task troubleshooting.
  * **Next run** – time of the next scheduled task run. Tasks that are still running by the time their next run time comes up are skipped and the next run time is adjusted forward according to the scheduled interval.
  * **Runs** – cumulative count of task runs.


You can also manually run (_Actions_ column.
**System scheduled tasks**
The application also transparently lists all scheduled tasks used by the system infrastructure. These tasks are mainly visible to provide troubleshooting assistance and cannot be interacted with in any way.
## Scheduler reliability
The scheduler runs together with the application within the web application process. As a result, scheduled tasks cannot be executed if the corresponding web application is not running. Depending on the hosting environment and the process manager used to host the application. Some process managers, such as Internet Information Services (IIS) automatically suspend application processes after a long period of inactivity to conserve resources. This behavior can often be suppressed with the correct configuration.
To ensure that scheduled tasks are processed regularly, ensure that the Xperience application’s worker process does not shut down if no one accesses the application for a certain period of time. For example, set the **Start Mode** advanced setting of the used application pool in IIS to _AlwaysRunning_.
## Scheduled tasks in multi-instance environments
In environments with [multiple application instances](/documentation/developers-and-admins/configuration/auto-scaling-support), scheduled tasks that are ready to run are assigned to one of the active instances (in a nondeterministic way). Task execution only occurs once, regardless of the number of instances.
Tasks launched manually via the _Scheduled tasks_ application run on the specific instance that handled the given request.
![]()
[]()[]()
