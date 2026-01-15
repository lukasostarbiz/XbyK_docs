# Define the module in the Xperience UI
  * [ Copy page link ](modules/custom-settings/define-module-in-xperience-ui#) | [Get HelpService ID](modules/custom-settings/define-module-in-xperience-ui#)
Core MVC 5


[✖](modules/custom-settings/define-module-in-xperience-ui# "Close page link panel") [Copy to clipboard](modules/custom-settings/define-module-in-xperience-ui#)
The individual settings in our custom settings module (e.g., the Email notification sender from the video above) correspond to objects of a [custom class](documentation/developers-and-admins/customization/object-types).
Each class must belong to a **Module** – a container that groups related data classes. We recommend organizing your custom classes into separate modules for different features.
### Create custom module
  1. Navigate to your Xperience administration.
  2. [Create a new module](documentation/developers-and-admins/customization/object-types#create-a-module), entering these values: 
     * **Module name** : Project settings
     * **Identifiers → Code name** : TrainingGuides.ProjectSettings
Note, that in order to fill out the **Code name** manually, you need to uncheck the **Pre-fill the code name automatically** checkbox (checked by default).
We recommend including the namespace of your client or organization in the code name (_TrainingGuides_ in this example). It will help you easily differentiate the custom modules and their purpose.


### Create data class
  1. [Create new class](documentation/developers-and-admins/customization/object-types#define-and-model-the-data-class) within your new module. Use the following parameters:
     * **Class name** : Global settings key
     * **Namespace** : TrainingGuides
     * **Name** : GlobalSettingsKey
Now if you check the **CMS_Class** table of the database, you’ll see a row representing your new class. [![Select from a CMS_Class table showing the new TrainingGuides.GlobalSettingsKey class](docsassets/guides/create-basic-module/global-settings-class-in-db.png)](https://docs.kentico.com/docsassets/guides/create-basic-module/global-settings-class-in-db.png)
  2. [Specify **Database columns**](documentation/developers-and-admins/customization/object-types#define-and-model-the-data-class) for the class. Add columns with the following properties:
    1. The value of the setting, which you will access in code and used to influence functionality 
       * **Field name** : GlobalSettingsKeyValue
       * **Data type** : Text
       * **Size** : 1000
       * **Required** : True (Enabled)
    2. The code name of the setting, which you will use to find the setting and its value 
       * **Field name** : GlobalSettingsKeyName
       * **Data type** : Text
       * **Size** : 200
       * **Required** : True (Enabled)
    3. The display name of the setting, which provides a human-readable name for the listing page 
       * **Field name** : GlobalSettingsKeyDisplayName
       * **Data type** : Text
       * **Size** : 200
       * **Required** : True (Enabled)
    4. A note field, which tells editors how to use the setting and provides additional context 
       * **Field name** : GlobalSettingsKeyNote
       * **Data type** : Text
       * **Size** : 2000
       * **Required** : True (Enabled)
[![](docsassets/guides/create-basic-module/global-settings-db-columns.png)](https://docs.kentico.com/docsassets/guides/create-basic-module/global-settings-db-columns.png)
**Column roles designations**
Create fields whose names end in _DisplayName_ , _Name_ , and _LastModified_ with appropriate data types to automatically map them to the **Display name column** , **Code name column** , and **“Last modified” column** roles, respectively.
_Binary_ and _Guid_ fields will automatically map to the **GUID column** and **Binary column** of the class.
These settings are important for [code generation](documentation/developers-and-admins/api/generate-code-files-for-system-objects), which we will discuss [later on](modules/custom-settings/define-module-in-xperience-ui#generate-code-files).
Switch to the **Code** tab to see that your new fields have been automatically mapped:
[![Database columns ‘GlobalSettingsKeyDisplayName’ and ‘GlobalSettingsKeyName’ automatically mapped to ‘Display name column’ and ‘Code name column’ fields in the Code tab](docsassets/guides/create-basic-module/global-settings-colum-designation.png)](https://docs.kentico.com/docsassets/guides/create-basic-module/global-settings-colum-designation.png)
Note that you can always manually change the designations on the **Code**.


### Add a UI form for the class
To enable editors or administrators to create items of your class in the admin UI, you need a **UI form**.
  1. [Create a new edit form](documentation/developers-and-admins/customization/object-types#define-editing-forms) for your class: 
    1. **UI form name** : Global settings key edit
    2. **Identifiers → Code name** : GlobalSettingsKeyEdit 
In this case, you can simply leave **Pre-fill code name automatically** checked instead of typing the code name manually. The system-generated code name will be identical.
  2. Switch to the **Fields** tab in the left menu.
  3. Add a new UI field for each of your class database fields: 
    1. Click **New field**.
    2. Select a database column you want to map the field to. The available columns are sorted alphabetically.
    3. Fill out field settings as desired and hit **Save**.
For our example, let’s set the fields up as follows: 
     * _GlobalSettingsKeyDisplayName_
       * **Field caption** : Display name
       * **Enabled** : True (Enabled)
       * **Form component** : Text input
     * _GlobalSettingsKeyName_
       * **Field caption** : Settings key code name
       * **Enabled** : True (Enabled)
       * **Form component** : Text input
     * _GlobalSettingsKeyValue_
       * **Field caption** : Value
       * **Enabled** : True (Enabled)
       * **Form component** : Text area
       * **Minimum number of rows** : 3
       * **Maximum number of rows** : 5
     * _GlobalSettingsKeyNote_
       * **Field caption** : Notes
       * **Tooltip text** : Explain the purpose of this settings key.
       * **Enabled** : True (Enabled)
       * **Form component** : Text area
       * **Minimum number of rows** : 3
       * **Maximum number of rows** : 15


[![Global settings key edit form fields](docsassets/guides/create-basic-module/global-settings-edit-form.png)](https://docs.kentico.com/docsassets/guides/create-basic-module/global-settings-edit-form.png)
Your custom class and edit form are now ready.
## Generate code files
The next step toward accessing your class and its objects in your code in a strongly typed manner is to [generate code files for it](documentation/developers-and-admins/customization/object-types#generate-code-files).
**Dedicated info providers**
We recommend using the `--with-provider-class "false"` parameter to **avoid** generating a [dedicated provider](documentation/developers-and-admins/customization/customize-system-providers) for your class.
You should use a [generic provider](documentation/developers-and-admins/customization/customize-system-providers), based on `IInfoProvider<TInfo>` to access the objects of your class - see an example [later on in this series](modules/custom-settings/access-custom-global-configurations#map-configuration-to-the-options-model) .
This is the best practice **unless** you specifically need to retrieve your objects in a context where **dependency injection is not yet available**. An example is the `CookieLevelConsentMapping` class in [one of our Data protection guides](guides/development/data-protection/map-consents-to-cookie-levels).
**Double-check**
If you’ve previously generated classes that **do** require dedicated providers, use the `--include` or `--exclude` parameters to ensure that the new command does not overwrite them.
If you are working in the [Training guides repository](https://github.com/Kentico/xperience-by-kentico-training-guides), you can use the following command. (Skip the `--exclude` parameter if your code doesn’t include the `CookieLevelConsentMapping` class.)
CMD
Copy
```
dotnet run --no-build -- --kxp-codegen --type "Classes" --location "../TrainingGuides.Entities/{type}/{name}/ " --exclude "trainingguides.cookielevelconsentmapping" --with-provider-class "false"
```

As a result, the system will generate a partial `GlobalSettingsKeyInfo` class under the namespace matching the module name (`TrainingGuides.ProjectSettings`) inside the _TrainingGuides.Entities_ project.
[![Generated Global settings key class](docsassets/guides/create-basic-module/global-settings-key-generated.png)](https://docs.kentico.com/docsassets/guides/create-basic-module/global-settings-key-generated.png)
Visit [our documentation page](documentation/developers-and-admins/api/generate-code-files-for-system-objects) for more information on the code generation parameters.
### Enable CI for the new class
If your project uses [Continuous integration](documentation/developers-and-admins/ci-cd/continuous-integration), you need to enable the feature for the newly generated `GlobalSettingsKeyInfo` class. You can achieve this by overriding the [object type configuration](documentation/developers-and-admins/customization/object-types/object-type-configuration) of the class.
Rather than editing the generated files directly, we recommend performing the configuration in **a new partial class file**. This way, you won’t accidentally overwrite your changes the next time you generate code files.
In the _TrainingGuides.Entities/Classes/Overrides_ folder create a partial `GlobalSettingsKeyInfo` class and override its `TYPEINFO` definition:
C#
**GlobalSettingsKeyInfoOverride.cs**
Copy
```
namespace TrainingGuides.ProjectSettings;

public partial class GlobalSettingsKeyInfo
{
    static GlobalSettingsKeyInfo()
    {
        TYPEINFO.ContinuousIntegrationSettings.Enabled = true;
    }
}
```

[ Previous page ](modules/custom-settings)
2 of 9
[ Mark complete and continue ](modules/custom-settings/build-module-ui)
  * [Community Questions & Answers](https://community.kentico.com/q-and-a)
  * [Contact support](https://community.kentico.com/support)


### Cookie consent
We use necessary [cookies](https://www.kentico.com/cookies-policy) to run our website and improve your experience while browsing. Additional cookies are only used with your consent. You may revoke your consent on the [Cookies Policy](https://www.kentico.com/cookies-policy) page or in your browser at any time. 
ACCEPT ALL  [Configure](https://www.kentico.com/cookies-policy)
USE ONLY NECESSARY 
![](https://docs.kentico.com/modules/custom-settings/define-module-in-xperience-ui)
[](https://docs.kentico.com/modules/custom-settings/define-module-in-xperience-ui)[](https://docs.kentico.com/modules/custom-settings/define-module-in-xperience-ui)