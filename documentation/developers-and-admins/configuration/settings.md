---
source: https://docs.kentico.com/documentation/developers-and-admins/configuration/settings
scrape_date: 2026-01-26
---

  * [Home](/documentation)
  * [Developers and admins](/documentation/developers-and-admins)
  * [Configuration](/documentation/developers-and-admins/configuration)
  * Settings 


# Settings
The system provides various settings that allow users in the administration to enable and configure system features.
You can access the settings in the **Settings** application.
[![Working in the Settings application](/docsassets/documentation/settings/Settings.png)](/docsassets/documentation/settings/Settings.png)
Always select **Save** to confirm any changes that you make in the settings.
## Search for settings
There is a large number of settings in Xperience. To find a particular setting among all the categories:
  1. Access the **Settings** application.
  2. Enter related keywords into the search bar at the top of the page.
  3. Press Enter.


The page displays all settings that contain the search text in their name or description text (tooltip). You can edit the values of the settings directly in the search results.
**Application settings**
In addition to the settings available in the Xperience administration, the system’s functionality can be adjusted via low-level application configuration keys. These keys are managed using ASP.NET Core [configuration providers](https://docs.microsoft.com/en-us/aspnet/core/fundamentals/configuration/). By default, Xperience projects contain an **appsettings.json** file for this purpose.
For more information, see [Reference - Configuration keys](/documentation/developers-and-admins/configuration/reference-configuration-keys).
## Load the values of settings using the API
If you wish to adjust custom functionality based on specific Xperience settings, you need to retrieve and reflect their values in your custom code.
To load the values of settings, use `ISettingsService` (we recommend using [dependency injection](/documentation/developers-and-admins/development/website-development-basics/dependency-injection) to instantiate system service classes). The service exposes an indexer that you can use to access the values of individual settings. Setting keys are identified by their code names.
The service always returns `string` values. If you need to cast the setting value to a different type, use `IConversionService`.
C#
**Example**
Copy
```
using CMS.Core;

...

// Contains an instance of the settings service (e.g., obtained using dependency injection)
private readonly ISettingsService settingsService;

// Contains an instance of the conversion service (e.g., obtained using dependency injection)
private readonly IConversionService conversionService;

// Checks if Continuous Integration is enabled
bool continuousIntegrationEnabled = conversionService.GetBoolean(settingsService["CMSEnableCI"], false);
```

![]()
[]()[]()
