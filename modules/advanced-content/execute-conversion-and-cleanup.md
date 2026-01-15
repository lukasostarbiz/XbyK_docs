# Execute conversion and cleanup
  * [ Copy page link ](modules/advanced-content/execute-conversion-and-cleanup#) | [Get HelpService ID](modules/advanced-content/execute-conversion-and-cleanup#)
Core MVC 5


[✖](modules/advanced-content/execute-conversion-and-cleanup# "Close page link panel") [Copy to clipboard](modules/advanced-content/execute-conversion-and-cleanup#)
With our code to convert articles in place, we need a way to trigger execution.
In this example, we’ll show a proof-of-concept for a simple controller that executes the code when you visit the provided URL.
In real-world scenarios, we only recommend this approach if you have copied your production database to a secure local machine for remodeling, and plan to re-deploy after making the changes.
If you plan to run this kind of code in any kind of public-facing environment, we recommend a more secure approach. For example:
  * Create a controller with a secure POST endpoint, and only execute the code upon successful validation of a secret key in the request body. This way, only people who know the secret key and the endpoint can trigger the code.
  * Create a [custom UI page](documentation/developers-and-admins/customization/extend-the-administration-interface/ui-pages) in the Xperience admin UI that calls the code in its `ConfigurePage` method, or upon a [button click](documentation/developers-and-admins/customization/extend-the-administration-interface/ui-pages/ui-page-commands). Then, only people with access to the Xperience admin UI can trigger the code. You can also apply [permissions](documentation/developers-and-admins/customization/extend-the-administration-interface/ui-pages/ui-page-permission-checks) to further control access.
  * Create a [custom scheduled task](documentation/developers-and-admins/customization/scheduled-tasks) that executes your code. Set the **Task schedule** configuration to _Once_ , so that the system does not automatically run the task, so that users with access to the scheduled tasks application in the Xperience admin UI can manually trigger execution.


Regardless of the approach you take, make sure to create a backup of your database prior to running the conversion, in case something goes wrong.
### Define a controller
Returning to the _~/OneTimeCode_ folder, add a new controller with a single GET action on the path _ConvertArticles_.
Call our `ArticleConverter` code and pass the resulting collection of `ConversionAttempt` objects as the model.
Supply the path to a view which we will implement in the next step.
C#
**ConvertArticlesController.cs**
Copy
```
using Microsoft.AspNetCore.Mvc;

namespace TrainingGuides.Web.OneTimeCode;

public class ConvertArticlesController(ArticleConverter articleConverter) : Controller
{
    [HttpGet("/ConvertArticles")]
    public async Task<IActionResult> Test()
    {
        var attempts = await articleConverter.Convert();
        return View("~/OneTimeCode/ConvertArticlesView.cshtml", attempts);
    }
}
```

Depending on your scenario, you may want to iterate through the results and [log the collected messages and exceptions](documentation/developers-and-admins/development/logging) using `ILogger`. In this example, we will simply print them on the page for simplicity and formatting.
### Add a view
With the controller in place, we can display the results in a simple view.
C#
**ConvertArticlesView.cshtml**
Copy
```
@using TrainingGuides.Web.OneTimeCode

@model IEnumerable<ConversionAttempt>

@{
    Layout = null;
}

@foreach (ConversionAttempt attempt in Model)
{
    <div style="border-bottom:1px solid black; margin-bottom:5px; padding-bottom:20px; margin-top:5px;">
        <strong>OldArticle:</strong>
        <div>@($"{attempt.OldArticle.ArticleTitle} ({attempt.OldArticle.SystemFields?.ContentItemGUID}) ({attempt.OldArticle.SystemFields?.ContentItemCommonDataContentLanguageID})")</div>
        <ul style="color:grey">
            @foreach (string log in attempt.LogMessages)
            {
                <li>@log</li>
            }
        </ul>
        <ul style="color:red">
            @foreach (var error in attempt.Exceptions)
            {
                <li>@error.Message</li>
            }
        </ul>
    </div>
}
```

### Visit the endpoint
**Make sure you create a backup of your database before executing the code**
Now, you can run the project and trigger the code by visiting the **/ConvertArticles** path in your browser.
[![GIF showing the result of visiting the /ConvertArticles path](docsassets/guides/convert-content-to-reusable-schemas/ConvertArticles.gif)](https://docs.kentico.com/docsassets/guides/convert-content-to-reusable-schemas/ConvertArticles.gif)
Depending on the number of items you are converting, our code may take a while to execute and heavily utilize resources. We recommend running this kind of code during a maintenance window, or during off-peak hours, so performance is less of a concern.
You can see the results in the admin UI:
Your browser does not support the video tag. 
## Clean up
Now our pages point to our new schema-based articles instead of our original `Article` items, but the old items still exist. Moreover, references to the `Article` type exist in several places throughout the solution, like generated classes, widgets, services, and the code we just executed.
### Delete old Article items
If you filter the Content hub listing by the old **Article** content type, you can use the mass actions to delete several items at a time.
Alternately, you can extend our existing code to [Delete the articles](api/content-management/content-items#delete-content-items) after they are successfully converted.
### Consider references from other content types
Before you delete a content type, we recommend taking inventory of any other types that reference it, which you did not choose to update via code. This serves as a refresher on the other functionality touched by the type.
**Deleting types referenced by other types**
You can delete content types that are still referenced by other types, but the form controls that reference them will display a **DELETED ITEM** message:
[![Screenshot of the message displayed in the UI when a content type references a deleted content type.](docsassets/guides/convert-content-to-reusable-schemas/deleted-item-ui.png)](https://docs.kentico.com/docsassets/guides/convert-content-to-reusable-schemas/deleted-item-ui.png)
The `ClassFormDefinition` column of the `CMS_Class` table stores field and form control configuration for content types, including content types assigned to the _Combined content selector_. Xperience stores this data as an array of content type GUIDs:
XML
**Allowed content types sample (ClassFormDefinition)**
Copy
```
...
<AllowedContentItemTypeIdentifiers>["17a2abf5-c412-4cee-8b6b-e5209bcd3e8c","8afff782-a445-4e6b-a237-821fca0db4fb","da713409-64bd-4bdd-bf21-3ec8294ab1b6"]</AllowedContentItemTypeIdentifiers>
...
```

To find your content type’s GUID, check its row in the CMS_Class table.
SQL
**Find a content type’s GUID**
Copy
```
SELECT ClassDisplayName, ClassName, ClassGuid FROM CMS_Class WHERE ClassName = 'TrainingGuides.Article'
```

Then, you can use the GUID in a query to find other content types that reference it.
SQL
**Find content types that reference another**
Copy
```
SELECT ClassDisplayName, ClassName, ClassFormDefinition FROM CMS_Class
WHERE ClassFormDefinition LIKE '%YOUR_GUID_HERE%'
```

Depending on your SQL Server version, you may be able to use the `REGEXP_LIKE` function for more accurate matching. This is ideal if you want to check for references from specific fields or form controls.
Reusable field schemas are defined in the `CMS.ContentItemCommonData` class. You can determine which fields belong to which schemas by matching the value of their `kxp_schema_identifier` element to the `guid` attribute of a `schema` element.
XML
**ClassFormDefinition of CMS.ContentItemCommonData sample**
Copy
```
...
<schema guid="c3b4896f-ba7c-4b75-9cd4-47afa7489ff1" name="ArticleSchema">
    <properties>
        <fieldcaption>Article schema</fieldcaption>
    </properties>
</schema>
<field allowempty="true" column="ArticleSchemaTitle" columnprecision="0" columnsize="300" columntype="text" enabled="true" guid="7f920142-174c-41cd-8e92-cc40e80aabdb" visible="true">
    <properties>
        <explanationtextashtml>False</explanationtextashtml>
        <fieldcaption>Title</fieldcaption>
        <fielddescriptionashtml>False</fielddescriptionashtml>
        <kxp_schema_identifier>c3b4896f-ba7c-4b75-9cd4-47afa7489ff1</kxp_schema_identifier>
    </properties>
    <settings>
        <controlname>Kentico.Administration.TextInput</controlname>
    </settings>
</field>
...
 



 


```

If you discover a reference you’d like to change to point to new items, as we did with page references and related articles in our code, you can restore a backup and further expand the one-time code.
**Updating references in builder properties**
Updating references from the properties of builder objects like widgets, templates, and sections falls outside the scope of this example.
The process of finding the replacement GUID would be the same as we used while updating references from content types, but you’d need to find those references in the JSON of fields like `ContentItemCommonDataInfo.ContentItemCommonDataVisualBuilderWidgets` and `ContentItemCommonDataInfo.ContentItemCommonDataVisualBuilderTemplateConfiguration`. Working with these fields via API requires use of [API not intended for public use](documentation/developers-and-admins/customization/stable-customization-guidelines#api-not-intended-for-public-use), from the `CMS.ContentEngine.Internal` namespace.
### Delete the old content type
Once you’re sure everything has been properly migrated to the new content type, you can delete the old one from the listing view in the **Content types** application.
At this point, you’ve completed the transformation of your Article content model. The old content types no longer exist alongside the new ones, and you can move forward with schema-based articles only.
### Get rid of the one-time code
After a successful conversion, you no longer need the `ArticleConverter` code, so you can delete all the files we’ve created throughout this example, and remove the reference to the `ArticleConverter` class from **ServiceCollectionExtensions.cs**.
### Remove the content type from your code
You likely reference the old content type throughout your application, for example, in repositories, facades, services, widgets, etc.
If you navigate to the generated class file for your type (_TrainingGuides.Entities/ReusableContentTypes/Article_ for our example) you can use your IDE to find all references to the type and its members throughout the solution.
Depending on which of our training materials you’ve followed along with and which branch you started with, you’ll find code that utilizes the `Article` type alongside the `IArticleSchema` in several places, including the `ArticlePageService`, `ArticlePageServiceTests` `ArticleEmailWidgetModelMapper`, and `NatureSpotlightEmailService`.
[ Previous page ](modules/advanced-content/update-references)
9 of 11
[ Mark complete and continue ](modules/advanced-content/deliver-content-dynamically-with-smart-folders)
  * [Community Questions & Answers](https://community.kentico.com/q-and-a)
  * [Contact support](https://community.kentico.com/support)


### Cookie consent
We use necessary [cookies](https://www.kentico.com/cookies-policy) to run our website and improve your experience while browsing. Additional cookies are only used with your consent. You may revoke your consent on the [Cookies Policy](https://www.kentico.com/cookies-policy) page or in your browser at any time. 
ACCEPT ALL  [Configure](https://www.kentico.com/cookies-policy)
USE ONLY NECESSARY 
![](https://docs.kentico.com/modules/advanced-content/execute-conversion-and-cleanup)
[](https://docs.kentico.com/modules/advanced-content/execute-conversion-and-cleanup)[](https://docs.kentico.com/modules/advanced-content/execute-conversion-and-cleanup)