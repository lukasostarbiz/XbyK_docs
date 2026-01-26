---
source: https://docs.kentico.com/documentation/developers-and-admins/configuration/rich-text-editor-configuration/rich-text-editor-plugins
scrape_date: 2026-01-26
---

  * [Home](/documentation)
  * [Developers and admins](/documentation/developers-and-admins)
  * [Configuration](/documentation/developers-and-admins/configuration)
  * [Rich text editor configuration](/documentation/developers-and-admins/configuration/rich-text-editor-configuration)
  * Rich text editor plugins 


# Rich text editor plugins
The [Froala rich text editor](/documentation/business-users/rich-text-editor) available in the admin UI allows you to integrate custom functionality in the form of plugins. 
In Xperience’s implementation of Froala, plugins are stored in **React components** that are loaded as requested based on the [configuration](/documentation/developers-and-admins/configuration/rich-text-editor-configuration) of the instantiated editor.
To customize the editor you can:
  * [Use the default Xperience plugins](#default-xperience-plugins)
  * [Add existing community plugins](#add-existing-community-plugins)
  * [Write completely custom plugins](#implement-custom-plugins)


## Default Xperience plugins
These plugins are available when the rich text editor is used within certain parts of the administration, for example when editing content items in the _Content hub_ application or pages under a website channel.
You can also use the default plugins when creating your own [editor configurations](/documentation/developers-and-admins/configuration/rich-text-editor-configuration#customize-the-editor).
### Asset plugin
Allows editors to select image files stored as [content item assets](/documentation/business-users/content-hub/content-item-assets), and insert them into the rich text content.
[![Asset plugin on the rich text editor toolbar](/docsassets/documentation/rich-text-editor-plugins/RTE_plugin_asset.png)](/docsassets/documentation/rich-text-editor-plugins/RTE_plugin_asset.png)
  * **Toolbar button command name** : `insertAsset`
  * **Plugin name** : `@kentico/xperience-admin-base/Asset`


JSON
**EditorConfiguration.json**
Copy
```
{
  "toolbarButtons": [
    ...
    "insertAsset"
  ],
  ...
  "customPlugins": [
    {
      "pluginName": "@kentico/xperience-admin-base/Asset"
    },
    ...
  ]
}
```

### Link plugin
Allows editors to insert various types of links into the rich text content.
[![Link plugin on the rich text editor toolbar](/docsassets/documentation/rich-text-editor-plugins/RTE_plugin_link.png)](/docsassets/documentation/rich-text-editor-plugins/RTE_plugin_link.png)
  * **Toolbar button command name** : `insertLinkDropDown`
  * **Plugin name** : `@kentico/xperience-admin-base/Link`


You can configure which types of links are offered when the _Insert link_ button is selected in the editor. Add `pluginOptions` configuration with one or more of the options shown below.
  * `asset` – links to files stored as [content item assets](/documentation/business-users/content-hub/content-item-assets).
  * `external` – links to any manually entered URL.
  * `webpage` – links to [pages](/documentation/business-users/website-content) created under a web channel.


If the `pluginOptions` is ommited, the link plugin uses all of the available options.
You can also make use of other Froala’s [Link options](https://froala.com/wysiwyg-editor/docs/options/#link) when [configuring](/documentation/developers-and-admins/configuration/rich-text-editor-configuration#customize-the-editor) the plugin.
JSON
**EditorConfiguration.json**
Copy
```
{
  "linkStyles": {
    "button-link": "Button",
    "acme-link": "ACME style"
  },
  "linkEditButtons": [ "linkOpen", "linkStyle", "linkEdit", "linkRemove" ],
  "toolbarButtons": [
    ...
    "insertLinkDropDown"
  ],
  ...
  "customPlugins": [
    {
      "pluginName": "@kentico/xperience-admin-base/Link",
      "pluginOptions": {
        "dropdownOptions": [
          {
            "linkOption": "asset",
            "componentName": "@kentico/xperience-admin-base/AssetPanel"
          },
          {
            "linkOption": "external"
          },
          {
            "linkOption": "webpage",
            "componentName": "@kentico/xperience-admin-websites/PageLink"
          }
        ]
      }
    }
  ]
}
```

## Add existing community plugins
Xperience bundles most of the plugins available in [Froala’s plugin gallery](https://froala.com/wysiwyg-editor/docs/plugins/). To use such plugins, enable them in the [editor’s configuration](/documentation/developers-and-admins/configuration/rich-text-editor-configuration#customize-the-editor). As each plugin has its own set of [options](https://froala.com/wysiwyg-editor/docs/options/), we recommend first trying the plugin out in a separate environment (like [JSFiddle](https://jsfiddle.net/)) and getting acquainted with its configuration before configuring it in Xperience.
However, some plugins in the gallery are marked as ‘third-party’. These plugins are not bundled with Xperience in order to speed up the loading time of the editor, as the plugins can be quite large in size and are not used often. For example:
  * [Embedly](https://froala.com/embedly-plugin/)
  * [Font Awesome](https://froala.com/font-awesome-plugin/)
  * [Image Tui](https://froala.com/image-tui-plugin/)
  * [Spell Checker](https://froala.com/spell-checker-plugin/)


To distinguish whether a plugin is bundled or not, e.g., check the format of the plugin’s JavaScript URL on the plugin’s site:
  * …/js/**plugins** /<plugin_name>.min.js – a bundled plugin
  * …/js/**third_party** /<plugin_name>.min.js – a third-party plugin


To add a third-party plugin:
  * [Download and modify the plugin files](#download-and-modify-the-plugin-files)
  * [Create a React component to load the plugin](#create-a-react-component-to-load-the-plugin)


Bundled plugins need only to be enabled in the [editor’s configuration](/documentation/developers-and-admins/configuration/rich-text-editor-configuration#customize-the-editor). Adding them as third-party plugins may result in unexpected JavaScript errors.
### Download and modify the plugin files
  1. Download the plugin resource files (typically a JavaScript file and a CSS file).
  2. Open the plugin’s JavaScript file and make the following modifications:
JS
Copy
```
/* All Froala plugins begin with the following call that attempts to find a Froala instance inside linked JS files and attach the plugin to it (variable names can differ per file based on minification). This approach is not supported for the Xperience administration. */
!function(e,c){"object"==typeof exports&&"undefined"!=typeof module?c(require("froala-editor")):"function"==typeof define&&define.amd?define(["froala-editor"],c):c(e.FroalaEditor)}(this,function(S){...

// To make the plugin load correctly, replace
"object"==typeof exports&&"undefined"!=typeof module?c(require("froala-editor")):"function"==typeof define&&define.amd?define(["froala-editor"],c):c(e.FroalaEditor)

// With
c(e.FroalaEditor)
// Where FroalaEditor is the Froala editor instance exposed by the admin application

// Modified output
!function(e, c){c(e.FroalaEditor)}(this, function(S){...
```

  3. [Load the modified plugin using a React component](#create-a-react-component-to-load-the-plugin).


### Create a React component to load the plugin
  1. Create a custom [JavaScript module for the admin UI](/documentation/developers-and-admins/customization/extend-the-administration-interface/prepare-your-environment-for-admin-development).
     * Use the provided boilerplate project to simplify the set up. Make sure the project has the **Kentico.Xperience.Admin** NuGet package installed so it can access admin UI-related APIs.
  2. Create a new React plugin component to load the plugin resource files.
JS
Copy
```
import React from 'react';
import { RichTextEditorPluginProps } from '@kentico/xperience-admin-components';

// Imports the plugin into the component as a dependency
// This example loads the resources of the Embedly integration downloadable from the Froala plugin gallery
import './embedly.min.js';
import './embedly.min.css';

// The component must be named 'PluginName' + 'RichTextEditorPlugin'
// Xperience uses this convention to load requested plugins
export const EmbedlyWrapperRichTextEditorPlugin = ({} : RichTextEditorPluginProps) => {
    return [];
};
```

  3. Export the component from the module.
TSX
**entry.tsx**
Copy
```
export * from './path/to/EmbedlyWrapperRichTextEditorPlugin';
```

  4. Add the plugin component to an [editor configuration](/documentation/developers-and-admins/configuration/rich-text-editor-configuration). Specify the component name without the **RichTextEditorPlugin** suffix.
The example extends an editor configuration to load the _EmbedlyWrapperRichTextEditorPlugin_ component.
JSON
**EditorConfiguration.json**
Copy
```
{
   "toolbarButtons": [
    "bold",
    "italic",
    "underline"
   ],

    ...

    "customPlugins": [ { "pluginName": "@orgName/projectName/EmbedlyWrapper" } ]
}
```



The plugin is now available for use within editors that use the given configuration.
## Implement custom plugins
The following general process outlines custom plugin development for the Xperience Froala implementation:
See the [example at the bottom of this page](#example---create-a-custom-button) for a step-by-step process that demonstrates how to implement a simple plugin and add it to an editor configuration.
  1. Create a custom [JavaScript module for the admin UI](/documentation/developers-and-admins/customization/extend-the-administration-interface/prepare-your-environment-for-admin-development). 
     * Use the provided boilerplate project to simplify the set up. Make sure the project has the **Kentico.Xperience.Admin** NuGet package installed so it can access admin UI-related APIs.
  2. Within the module, create a new file for the plugin code.
  3. Create a React component stub and a corresponding properties interface. 
     * Plugin components must use a properties interface derived from `RichTextEditorPluginProps` .
JS
**Plugin component skeleton**
Copy
```
import React, { useEffect } from 'react';
import { RichTextEditorPluginProps } from '@kentico/xperience-admin-components';

export interface CustomRichTextEditorPluginProps extends RichTextEditorPluginProps {
}

// The component must be named 'PluginName' + 'RichTextEditorPlugin'
// Xperience uses this convention when loading requested plugins
export const CustomRichTextEditorPlugin = ( {froalaEditorConfigurator} : CustomRichTextEditorPluginProps) => {
    useEffect(() => {
        // Call froalaEditorConfigurator methods within a 'useEffect' React Hook
        // to ensure correct behavior with multiple rich text editor instances on the same page
    }, [froalaEditorConfigurator]);
    return (
        <></>
    );
}
```

  4. `RichTextEditorPluginProps` provides properties that allow you to register plugins and use the API of the underlying Froala editor instance. 
     * `froalaEditorConfigurator` – contains global FroalaEditor API for plugin registration and other functionality as demonstrated in the [Froala documentation](https://froala.com/wysiwyg-editor/docs/concepts/custom/button/).  
The object provides the following methods:
Method  |  Description   
---|---  
`registerCommand` |  Defines a new command for the editor (button, drop-down list, etc.). For more information and available options, see, for example, [Custom Button](https://froala.com/wysiwyg-editor/docs/concepts/custom/button/).   
`defineIcon` |  Defines a new icon. Supports the **text** , **image** , and **svg** icon templates defined in the editor by default. See [Custom Icons](https://froala.com/wysiwyg-editor/docs/concepts/custom/icon/).   
`enableButton` |  Enables a button with the given name.   
`disableButton` |  Disables a button with the given name.   
`configureOptions` |  Allows you to dynamically adjust the [editor configuration](/documentation/developers-and-admins/configuration/rich-text-editor-configuration) (JSON represented by [FroalaOptions](https://froala.com/wysiwyg-editor/docs/options/)). This method provides a way to dynamically add a handler for the editor’s [initialized](https://froala.com/wysiwyg-editor/docs/events/#initialized) event before the event actually runs.   
To ensure correct behavior of custom plugins on pages with multiple rich text editor instance, call the `froalaEditorConfigurator` methods within a React [useEffect Hook](https://www.w3schools.com/react/react_useeffect.asp).
     * `froalaEditorRef` – points to the instance of the underlying Froala editor object. Get the current editor instance using `froalaEditorRef.current.editor` . For example, `froalaEditorRef?.current?.editor?.html.get()` selects all markup in the editor. For a reference of the available API, see [Methods](https://froala.com/wysiwyg-editor/docs/methods/) and [Events](https://froala.com/wysiwyg-editor/docs/events/).
     * `inputRef` – points to an Xperience wrapper of the Froala editor that provides customized methods for inserting images and links and for executing [UI form component commands](/documentation/developers-and-admins/customization/extend-the-administration-interface/ui-form-components#component-commands) from within the plugin.


The following snippet shows the basic structure of Froala plugins in Xperience:
JS
**Froala plugin structure in Xperience**
Copy
```
import React, { useEffect } from 'react';
import { RichTextEditorPluginProps } from '@kentico/xperience-admin-components';

// Any required properties must extend 'RichTextEditorPluginProps'
export interface CustomRichTextEditorPluginProps extends RichTextEditorPluginProps {
}

// The component must be named 'PluginName' + 'RichTextEditorPlugin'
// Xperience uses this convention when loading requested plugins
export const CustomRichTextEditorPlugin = ({

    // Exposes registration API for plugins
    froalaEditorConfigurator,

    // Contains a reference to the underlying Froala editor object
    froalaEditorRef,

    // A reference to an Xperience wrapper for the Froala editor
    // contains customized support for inserting images and links (ensures correct URL format)
    // and for executing UI form component commands from within the plugin
    inputRef
} : CustomRichTextEditorPluginProps) => {
    // Call froalaEditorConfigurator methods within a 'useEffect' React Hook
    // to ensure correct behavior with multiple rich text editor instances on the same page
    useEffect(() => {
        // The name of a custom icon
        const iconName = 'customIcon'

        // The name of a command/action
        // Used to reference the command/action from editor configurations
        const commandName = 'customButton';

        const buttonName = 'Click me!';

        // Defines an icon
        froalaEditorConfigurator?.defineIcon(iconName, {NAME: 'star'});

        // Defines a custom action
        froalaEditorConfigurator?.registerCommand(commandName, {
            title: buttonName,
    persona: admin, developer
            icon: iconName,
            // Runs when the button representing the plugin is selected
            callback: async () => {
                window.alert("It works!");
                // Command code to execute
            }
        });
    }, [froalaEditorConfigurator]);

    return (
        // Markup inserted to the page by the plugin (e.g., an input dialog that opens when a button is selected)
        <>
        </>
    );
};
```

### Add custom plugins to rich text editor configurations
To make custom plugins available within editor instances, you need to load them inside [editor configurations](/documentation/developers-and-admins/configuration/rich-text-editor-configuration):
  * To add a toolbar button for the plugin, reference the plugin via the registered `commandName` under the `toolbarButtons` array (`customButton` in the example below).
  * Load the plugin via the `customPlugins` array.


JSON
Copy
```
{
   "toolbarButtons": [
    "bold",
    "italic",
    "underline",
    "customButton"
   ],

    ...

    "customPlugins": [ { "pluginName": "@orgName/projectName/Custom" }, { "pluginName": "@orgName/projectName/<PluginName>" } ]
}
```

## Example - Create a custom button
The following example shows how to create a simple plugin that replaces occurrences of the pronoun ‘ _you_ ’ with ‘ _thou_.’
  1. Create a custom [JavaScript module for the admin UI](/documentation/developers-and-admins/customization/extend-the-administration-interface/prepare-your-environment-for-admin-development).
     * Make sure the project has the **Kentico.Xperience.Admin** NuGet package installed so it can access admin UI-related APIs.
  2. In a suitable location within the module, create a new file: **ArchaicPronounsRichTextEditorPlugin.tsx**
  3. Copy and paste the following code into the file:
JS
Copy
```
import React, { useEffect } from 'react';
import { RichTextEditorPluginProps } from '@kentico/xperience-admin-components';

export interface ArchaicPronounsRichTextEditorPluginProps extends RichTextEditorPluginProps {
}

export const ArchaicPronounsRichTextEditorPlugin = ({froalaEditorConfigurator, froalaEditorRef} : ArchaicPronounsRichTextEditorPluginProps) => {
    useEffect(() => {
        const pluginIconName = 'archaicPronounsIcon';
        const buttonName = 'Archaify!';
        const commandName = 'archaicPronouns';

        froalaEditorConfigurator?.defineIcon(pluginIconName, { SVG_KEY: 'smile' });
        froalaEditorConfigurator?.registerCommand(commandName, {
            title: buttonName,
persona: admin, developer
            icon: pluginIconName,
            undo: false,
            focus: true,
            showOnMobile: true,
            refreshAfterCallback: true,
            callback: async () => {
                var htmlContent = froalaEditorRef?.current?.editor?.html.get();
                htmlContent = htmlContent.replace(/you/gi, 'thou');
                froalaEditorRef?.current?.editor?.html.set(htmlContent);
            }
        });
    }, [froalaEditorConfigurator, froalaEditorRef]);

    return (
        // No markup to return, the plugin requires only existing content
        <>
        </>
    );
};
```

  4. Export the component from the module:
TSX
**entry.tsx**
Copy
```
export * from './path/to/ArchaicPronounsRichTextEditorPlugin';
```

  5. The plugin is now ready to be loaded by the Xperience administration. You can include it in a chosen editor configuration using the `customPlugins` array:
In the `customPlugins` configuration, replace `orgName` and `projectName` with your own values. By default, the boilerplate project uses `acme` and `web-admin` respectively. 
JSON
**Editor configuration JSON**
Copy
```
{
   "toolbarButtons": [
    "bold",
    "italic",
    "underline",
    "archaicPronouns"
   ],

    ...

    "customPlugins": [ { "pluginName": "@orgName/projectName/ArchaicPronouns" } ]
}
```



The plugin is now available under the registered custom button within editors using the modified [configuration](/documentation/developers-and-admins/configuration/rich-text-editor-configuration).
![]()
[]()[]()
