---
source: https://docs.kentico.com/api/configuration/web-farms
scrape_date: 2026-01-22
---

  * [Home](/api)
  * [Configuration](/api/configuration)
  * Web farms 


# Web farms
  
List of examples:
  * [Create web farm synchronization tasks](#create-web-farm-synchronization-tasks)


### Create web farm synchronization tasks
C#
Copy
```
// Sets the properties for the task
int objectId = 42;

// Creates the web farm task
WebFarmHelper.CreateTask(new InvalidateObjectWebFarmTask
{
    ObjectId = objectId,
});
```

[> Back to list of examples](#toc)
![]()
[]()[]()
