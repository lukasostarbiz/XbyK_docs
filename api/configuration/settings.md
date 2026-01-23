---
source: https://docs.kentico.com/api/configuration/settings
scrape_date: 2026-01-22
---

  * [Home](/api)
  * [Configuration](/api/configuration)
  * Settings 


# Settings
  
List of examples:
  * [Dependency injection](#dependency-injection)
    * [Initialize required services](#initialize-required-services)
    * [Get the values of database settings](#get-the-values-of-database-settings)
    * [Get the values of application settings](#get-the-values-of-application-settings)
    * [Set the values of settings](#set-the-values-of-settings)


## Dependency injection
### Initialize required services
C#
Copy
```
// Initializes all services and provider classes used within
// the API examples on this page using dependency injection.
private readonly ISettingsService settingsService;
private readonly IInfoProvider<SettingsKeyInfo> settingsKeyInfoProvider;
private readonly IConfiguration configuration;
private readonly IConversionService conversionService;

public SettingsServices(ISettingsService settingsService,
                IInfoProvider<SettingsKeyInfo> settingsKeyInfoProvider,
                IConfiguration configuration,
                IConversionService conversionService)
{
    this.settingsService = settingsService;
    this.settingsKeyInfoProvider = settingsKeyInfoProvider;
    this.configuration = configuration;
    this.conversionService = conversionService;
}
```

[> Back to list of examples](#toc)
### Get the values of database settings
C#
Copy
```
// Note: Find the code names of individual settings keys in the CMS_SettingsKey table

// Gets the value of the "Asset allowed extensions" setting
string assetAllowedExtensions = settingsService["CMSMediaFileAllowedExtensions"];

// Gets the value of the "Enable Continuous Integration" setting
bool continuousIntegrationEnabled = conversionService.GetBoolean(settingsService["CMSEnableCI"], false);

// Gets the value of the "Event log size" setting for the current site
int eventLogSize = conversionService.GetInteger(settingsService["CMSLogSize"], 60);
```

[> Back to list of examples](#toc)
### Get the values of application settings
C#
Copy
```
// Gets the value of the "CMSApplicationName" key from a configuration provider (e.g., appsettings.json)
string applicationSetting = configuration["CMSApplicationName"];
```

[> Back to list of examples](#toc)
### Set the values of settings
C#
Copy
```
// Adds additional extensions to the list of allowed extensions
SettingsKeyInfo allowedExtensionsSettingsKey = settingsKeyInfoProvider.Get("CMSMediaFileAllowedExtensions");
allowedExtensionsSettingsKey.KeyValue += ";tiff";
allowedExtensionsSettingsKey.Update();

// Disables the Continuous Integration feature
SettingsKeyInfo ciSettingsKey = settingsKeyInfoProvider.Get("CMSEnableCI");
ciSettingsKey.KeyValue = "False";
ciSettingsKey.Update();

// Sets the maximum event log size to 30
SettingsKeyInfo logSizeSettingsKey = settingsKeyInfoProvider.Get("CMSLogSize");
logSizeSettingsKey.KeyValue = 30.ToString();
logSizeSettingsKey.Update();
```

[> Back to list of examples](#toc)
![]()
[]()[]()
