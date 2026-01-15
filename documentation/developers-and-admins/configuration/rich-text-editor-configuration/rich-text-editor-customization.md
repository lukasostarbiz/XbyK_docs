# Rich text editor customization
  * [ Copy page link ](documentation/developers-and-admins/configuration/rich-text-editor-configuration/rich-text-editor-customization#) | [Get HelpService ID](documentation/developers-and-admins/configuration/rich-text-editor-configuration/rich-text-editor-customization#)
Core MVC 5


[✖](documentation/developers-and-admins/configuration/rich-text-editor-configuration/rich-text-editor-customization# "Close page link panel") [Copy to clipboard](documentation/developers-and-admins/configuration/rich-text-editor-configuration/rich-text-editor-customization#)
The [rich text editor](documentation/business-users/rich-text-editor) in Xperience can be extended by defining [custom editor configurations](documentation/developers-and-admins/configuration/rich-text-editor-configuration#customize-the-editor) and by developing [custom plugins](documentation/developers-and-admins/configuration/rich-text-editor-configuration/rich-text-editor-plugins).
Additionally, the system allows developers to exactly control which rich text editor configurations are loaded, and to make dynamic adjustments of the configuration content. This can be done by providing a custom implementation of the `IRichTextEditorConfigurationProvider` service. In most cases, we recommend extending the default implementation via the [decorator pattern](documentation/developers-and-admins/customization/decorate-system-services).
The sections below provide examples of scenarios that use this customization pattern:
  * [Replace the default editor configuration](documentation/developers-and-admins/configuration/rich-text-editor-configuration/rich-text-editor-customization#replace-the-default-editor-configuration)
  * [Add custom CSS to the rich text editor](documentation/developers-and-admins/configuration/rich-text-editor-configuration/rich-text-editor-customization#add-custom-css-to-the-rich-text-editor)


## Replace the default editor configuration
When adding fields with the _Rich text editor_ form component in the [field editor](documentation/developers-and-admins/customization/field-editor), the selected **Configuration name** determines which options and plugins are available in the editor toolbar. If the user leaves the _Configuration name_ empty, the field uses a default editor configuration.
You can use the `IRichTextEditorConfigurationProvider` service to replace the default configuration with a [custom editor configuration](documentation/developers-and-admins/configuration/rich-text-editor-configuration#customize-the-editor) that is preferred for your project. With this change, users no longer need to select your custom configuration for every rich text field.
  1. Open your Xperience solution in Visual Studio.
  2. [Add a custom assembly](documentation/developers-and-admins/customization/integrate-custom-code) (_Class Library_ project) with class discovery enabled to the solution, or re-use an existing assembly.
  3. Create a new custom class in the assembly, and implement the `IRichTextEditorConfigurationProvider` interface.
  4. Add the `GetConfigurationByName` method: 
     * The `configurationName` parameter contains the identifier of the configuration that was requested. Write a condition to detect the configurations that you want to replace. 
       * Access the constants in the `RichTextEditorConfigurationsIdentifiers` class to get the identifiers of the system’s configurations: 
         * `STRUCTURED_CONTENT` – for [reusable content items](documentation/business-users/content-hub)
         * `WEBSITE_STRUCTURED_CONTENT` – for [pages](documentation/business-users/website-content)
         * `HEADLESS_STRUCTURED_CONTENT` – for [headless items](documentation/business-users/headless-content)
         * `AUTORESPONDER_EMAIL_CONTENT` – for [emails](documentation/business-users/digital-marketing/emails) with the _Form autoresponder_ purpose
         * `EMAIL_CONTENT` – for all other emails
         * `NOTIFICATION_EMAIL_CONTENT` – for [notifications](documentation/developers-and-admins/configuration/notifications)
         * `CONSENT_TEXT` – for [consent text](documentation/developers-and-admins/data-protection/consent-management) fields
         * `CONTACT_NOTES` – for the _Notes_ field when editing [contact](documentation/business-users/digital-marketing/contact-management) details
       * For rich text editor fields with the default “empty” configuration outside of the above locations, the `configurationName` is `null`.
     * Use the [decorator customization pattern](documentation/developers-and-admins/customization/decorate-system-services#decorate-services-via-dependency-injection) to call the default implementation for cases where you do not wish to replace the requested configuration.
  5. Register the implementation using the `RegisterImplementation` assembly attribute.


C#
**Replacing the default rich text editor configurations**
Copy
```
using System;

using CMS;

using Kentico.Xperience.Admin.Base.Forms;
using Kentico.Xperience.Admin.Websites;

// Registers the custom IRichTextEditorConfigurationProvider implementation
[assembly: RegisterImplementation(typeof(IRichTextEditorConfigurationProvider), typeof(CustomDefaultRichTextEditorConfigurationProvider))]

public class CustomDefaultRichTextEditorConfigurationProvider : IRichTextEditorConfigurationProvider
{
    private readonly IRichTextEditorConfigurationProvider richTextEditorConfigurationProvider;

    public CustomDefaultRichTextEditorConfigurationProvider(IRichTextEditorConfigurationProvider richTextEditorConfigurationProvider)
    {
        // Gets an instance of the default IRichTextEditorConfigurationProvider service
        this.richTextEditorConfigurationProvider = richTextEditorConfigurationProvider;
    }

    // Loads the configuration for instances of the rich text editor in the administration
    public string GetConfigurationByName(string configurationName)
    {
        // The 'configurationName' parameter contains the identifier of the requested editor configuration
        // This condition replaces the system's default editor configuration for page fields
        if (string.IsNullOrEmpty(configurationName) ||
            string.Equals(configurationName, RichTextEditorConfigurationsIdentifiers.WEBSITE_STRUCTURED_CONTENT, StringComparison.OrdinalIgnoreCase))
        {
            // Use the identifier of your preferred custom configuration
            // For example, access the IDENTIFIER constant of the class used to register the configuration
            return richTextEditorConfigurationProvider.GetConfigurationByName(CustomDefaultEditorConfiguration.IDENTIFIER);
        }

        // Calls the default implementation and returns the unmodified configuration in all other cases
        return richTextEditorConfigurationProvider.GetConfigurationByName(configurationName);
    }
}
```

## Add custom CSS to the rich text editor
The following example demonstrates how to adjust the appearance and design of the rich text editor by inserting custom styles. The recommended approach is to create a [custom plugin](documentation/developers-and-admins/configuration/rich-text-editor-configuration/rich-text-editor-plugins) containing the required elements and CSS, and then use the `IRichTextEditorConfigurationProvider` service to dynamically insert your plugin into the appropriate [editor configurations](documentation/developers-and-admins/configuration/rich-text-editor-configuration#customize-the-editor).
  1. Create a custom [JavaScript module for the admin UI](documentation/developers-and-admins/customization/extend-the-administration-interface/prepare-your-environment-for-admin-development). 
     * Use the [client development boilerplate](documentation/developers-and-admins/customization/extend-the-administration-interface/prepare-your-environment-for-admin-development#client-development-boilerplate) project to simplify the set up. Make sure the project has the **Kentico.Xperience.Admin** NuGet package installed so it can access admin UI-related APIs.
  2. Extend the module’s `webpack.config.js` to support CSS loading. For more information on configuring CSS loaders, see the [webpack documentation](https://webpack.js.org/loaders/#styling). 
JS
**webpack.config.js**
Copy
```
const projectConfig = {
    module: {
        rules: [
            ...
            // Adds the loader for basic CSS
            {
                test: /\.css$/,
                use: ["style-loader", "css-loader"],
            },
        ],
    },
 ...
};
```

  3. Within the module, create a React component file for the plugin. 
     * Import any required styles. You do not need to return any custom markup.
JS
**CustomCSSRichTextEditorPlugin.tsx**
Copy
```
import React from 'react';

// Imports custom styles
import './CustomCSSRichTextEditorPlugin.css';

// The plugin component's name must have the 'RichTextEditorPlugin' suffix
export const CustomCSSRichTextEditorPlugin = () => {
    return (
        // No markup to return
        <></>
    );
};
```

  4. Add files containing the styles that you wish to import. 
     * You can use basic CSS, or any CSS extension language supported by your module’s environment.
CSS
**CustomCSSRichTextEditorPlugin.css**
Copy
```
/* Examples of custom styles for the fr-view class, which is applied to the editor's content area */
.fr-view h2 {
    font-size: 3em;
}

.fr-view {
    background-color: red;
}
```

  5. Export the plugin component from the module: 
TSX
**entry.tsx**
Copy
```
export * from './path/CustomCSSRichTextEditorPlugin';
```

  6. Create a class in the admin module, and implement the `IRichTextEditorConfigurationProvider` interface.
  7. Add the `GetConfigurationByName` method and insert the `customPlugins` array with your custom CSS plugin into every configuration: 
C#
**Adding the custom CSS plugin to rich text editor configurations**
Copy
```
using System.Text.Json;
using System.Text.Json.Nodes;

using CMS;
using Kentico.Xperience.Admin.Base.Forms;

// Registers the custom IRichTextEditorConfigurationProvider implementation
[assembly: RegisterImplementation(typeof(IRichTextEditorConfigurationProvider), typeof(CustomCssRichTextEditorConfigurationProvider))]

public class CustomCssRichTextEditorConfigurationProvider : IRichTextEditorConfigurationProvider
{
    private readonly IRichTextEditorConfigurationProvider richTextEditorConfigurationProvider;

    public CustomCssRichTextEditorConfigurationProvider(IRichTextEditorConfigurationProvider richTextEditorConfigurationProvider)
    {
        // Gets an instance of the default IRichTextEditorConfigurationProvider service
        this.richTextEditorConfigurationProvider = richTextEditorConfigurationProvider;
    }

    public string GetConfigurationByName(string configurationName)
    {
        // Gets the requested rich text editor configuration
        var configuration = richTextEditorConfigurationProvider.GetConfigurationByName(configurationName);

        // Adds the custom CSS plugin to the configuration's JSON content
        var jsonObj = JsonNode.Parse(configuration);
        if (jsonObj?["customPlugins"] is JsonArray optionsArray)
        {
            var newOption = new JsonObject
            {
                // Replace 'orgName' and 'projectName' with the values for your client admin module.
                // The final part of the plugin name must match the name of
                // the plugin's React component without the 'RichTextEditorPlugin' suffix.
                ["pluginName"] = "@orgName/projectName/CustomCSS"
            };

            optionsArray.Add(newOption);
        }

        // Returns the modified configuration content, including the custom CSS plugin
        return jsonObj?.ToJsonString(new JsonSerializerOptions { WriteIndented = true });
    }
}
```

In the `pluginName` configuration value, replace `orgName` and `projectName` with your own values. By default, the boilerplate project uses `acme` and `web-admin` respectively. The final part of the plugin name must match the name of the plugin’s React component without the _RichTextEditorPlugin_ suffix (_CustomCSS_ in this example). 


The custom `IRichTextEditorConfigurationProvider` service dynamically inserts the custom CSS plugin into every rich text editor configuration, and the imported CSS is applied to the editor in the admin UI.