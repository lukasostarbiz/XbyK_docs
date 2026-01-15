# Web farms
  * [ Copy page link ](api/configuration/web-farms#) | [Get HelpService ID](api/configuration/web-farms#)
Core MVC 5


[✖](api/configuration/web-farms# "Close page link panel") [Copy to clipboard](api/configuration/web-farms#)
  
List of examples:
  * [Create web farm synchronization tasks](api/configuration/web-farms#create-web-farm-synchronization-tasks)


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

[> Back to list of examples](api/configuration/web-farms#toc)