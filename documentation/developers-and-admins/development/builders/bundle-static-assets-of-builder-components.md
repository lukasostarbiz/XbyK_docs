---
source: https://docs.kentico.com/documentation/developers-and-admins/development/builders/bundle-static-assets-of-builder-components
scrape_date: 2026-01-22
---

  * [Home](/documentation)
  * [Developers and admins](/documentation/developers-and-admins)
  * [Development](/documentation/developers-and-admins/development)
  * [Builders](/documentation/developers-and-admins/development/builders)
  * Bundle static assets of builder components 


# Bundle static assets of builder components
Components developed for the system’s [Page Builder](/documentation/developers-and-admins/development/builders/page-builder) and [Form](/documentation/developers-and-admins/development/builders/form-builder) Builder features (such as [widgets](/documentation/developers-and-admins/development/builders/page-builder/widgets-for-page-builder)) often require custom CSS and JavaScript assets to achieve their desired appearance and functionality.
With complex components, the number of such assets quickly multiplies and it becomes impractical to include them on pages individually. Moreover, keeping request count and static asset size low helps improve page performance. For these reasons, we recommend using [bundling and minification](https://docs.microsoft.com/en-us/aspnet/core/client-side/bundling-and-minification) for your static assets.
Use the following process to implement bundling and minification:
  1. Choose and set up a bundling and minification process suitable for your needs and workflow. The implementation and used tools are completely in your hands.
  2. Create bundles for your component assets and provide them to Xperience: 
     * Create separate CSS and JS bundles for both the page and form builder.
     * Create separate bundles for the live site and the Xperience administration interface. Certain assets, such as [Inline editor](/documentation/developers-and-admins/development/builders/page-builder/widgets-for-page-builder/inline-editors-for-widget-properties) registration scripts, are only required when using the Page Builder editing interface in the administration and vice versa.
     * The bundles must have specific names, be placed into designated locations within the project’s web root, and contain assets depending on the type of the bundle.
See the [Bundles](#bundles) section for detailed information about the default bundle names and locations.
     * (Optional) If you do not wish to use the default bundle names and locations, you can [specify a custom location and sources for individual bundles](#customize-bundle-locations-and-sources).


The system includes and renders the resulting bundles on pages where Builder functionality is enabled:
  * For the Page Builder on pages that call the `PageBuilderScripts` and `PageBuilderStyles` [extension methods](/documentation/developers-and-admins/development/builders/page-builder/create-pages-with-editable-areas).
  * For the Form Builder automatically when creating forms in the administration interface and on pages containing forms.


**Example**
The [Sample bundling and minification process](#sample-bundling-and-minification-process) section demonstrates how to configure a simple bundling process using the [Grunt](https://gruntjs.com/) automation library. The process bundles files from the default source directories and places the created bundles into the default locations.
## System bundling features
### Handling missing bundles
The system stores information about the source directories of files that comprise each bundle. If a bundle is not found in the expected location, files from the corresponding source directories are linked individually as a fallback. Every file results in a new _script_ tag on the page. This can be useful, for example, for debugging purposes in a development environment.
Each bundle has a preconfigured default source directory (listed in the [Bundles](#bundles) section). If necessary, you can add additional source directories via [provided options classes](#add-source-directories-for-a-bundle).
### Bundle caching
The system automatically caches served bundles to reduce application overhead.
**Note** : If your bundling process creates an empty file for a bundle, the given bundle is not included in the page at all. This information is also cached by the system to reduce unnecessary overhead. However, adding content to a previously empty bundle requires clearing the cache to ensure the change is reflected on the site (e.g., by restarting the application).
## Bundles
The following sections describe the default names and locations required for individual bundle types:
  * [Page Builder component bundles](#page-builder-bundles)
  * [Form Builder component bundles](#form-builder-bundles)


**Complementary system bundles**
For each bundle described below, the system automatically includes a bundle containing the scripts and styles required by the system’s default builder components, such as the [Form](/documentation/business-users/digital-marketing/forms/display-forms-on-pages) widget and individual [form components](/documentation/developers-and-admins/development/builders/form-builder). These bundles can be identified by their _system_ prefix, and the rules for their inclusion are the same as those for user-created bundles. 
### Page Builder bundles
Static assets for custom Page Builder components need to be split between the following types of bundles:
Bundle |  Bundle name |  Bundle location (~/wwwroot/Content/Bundles/) |  Bundle source directory (within ~/wwwroot/) |  Bundle contents  
---|---|---|---|---  
|  pageComponents.min.js |  Admin/pageComponents.min.js |  PageBuilder/Admin/**/*.js |  Included in the administration interface when editing pages that use the [Page Builder feature](/documentation/developers-and-admins/development/content-types). The bundle must contain [inline editor](/documentation/developers-and-admins/development/builders/page-builder/widgets-for-page-builder/inline-editors-for-widget-properties) registration and configuration scripts together with any other functionality you wish to enable for your components within the Page Builder editing interface that is not required on the live site. For a clean directory structure, we recommend placing scripts comprising the bundle into individual directories based on component type. For example:
  * ~/wwwroot/PageBuilder/Admin/InlineEditors/*.js
  * ~/wwwroot/PageBuilder/Admin/ModalDialogs/*.js

  
|  pageComponents.min.css |  Admin/pageComponents.min.css |  PageBuilder/Admin/**/*.css |  Included in the administration interface when editing pages that use the [Page Builder feature](/documentation/developers-and-admins/development/content-types). The bundle must contain all styles you wish to use for your components in the Page Builder editing interface. For a clean directory structure, we recommend placing styles comprising the bundle into individual directories based on component type. For example:
  * ~/wwwroot/PageBuilder/Admin/Widgets/*.css
  * ~/wwwroot/PageBuilder/Admin/Sections/*.css

  
|  pageComponents.min.js |  Public/pageComponents.min.js |  PageBuilder/Public/**/*.js |  Included on the live site. The bundle must contain all scripts required for the functionality of your custom Page Builder components on the live site. For a clean directory structure, we recommend placing scripts comprising the bundle into individual directories based on component type. For example:
  * ~/wwwroot/PageBuilder/Public/Widgets/*.js
  * ~/wwwroot/PageBuilder/Public/Sections/*.js
  * ~/wwwroot/PageBuilder/Public/PageTemplates/*.js

  
|  pageComponents.min.css |  Public/pageComponents.min.css |  PageBuilder/Public/**/*.css |  Included on the live site. The bundle must contain all styles required to correctly display custom components. For a clean directory structure, we recommend placing styles comprising the bundle into individual directories based on component type. For example:
  * ~/wwwroot/PageBuilder/Public/Widgets/*.css
  * ~/wwwroot/PageBuilder/Public/Sections/*.css
  * ~/wwwroot/PageBuilder/Public/PageTemplates/*.css

  
### Form Builder bundles
Static assets for custom Form Builder components need to be split into three bundles:
  * A bundle containing component scripts.
  * Two bundles containing component styles: one for the live site, the other for the administration interface.


Bundle |  Bundle name |  Bundle location (within ~/wwwroot/Content/Bundles/) |  Bundle source directory (within ~/wwwroot/) |  Bundle contents  
---|---|---|---|---  
|  formComponents.min.css |  Admin/formComponents.min.css |  FormBuilder/Admin/**/*.css |  Included when accessing the Form Builder editing interface in the administration. The bundle must contain all styles you wish to use for your form components in the Form Builder editing interface in the Xperience administration. For a clean directory structure, we recommend placing styles comprising the bundle into individual directories. For example:
  * ~/wwwroot/FormBuilder/Admin/FormComponents/*.css

  
|  formComponents.min.js |  Public/formComponents.min.js |  FormBuilder/Public/**/*.js |  Included on pages with forms and when accessing the form builder editing interface in the administration. The bundle must contain all scripts required for the correct functionality of your custom Form Builder components. There is no dedicated bundle for the administration interface. This bundle is linked both when rendering forms on the live site (via the [Form widget](/documentation/business-users/digital-marketing/forms/display-forms-on-pages)) and when composing forms via the Form Builder interface. For a clean directory structure, we recommend placing scripts comprising the bundle into individual directories. For example:
  * ~/wwwroot/FormBuilder/Public/FormComponents/*.js

  
|  formComponents.min.css |  Public/formComponents.min.css |  FormBuilder/Public/**/*.css |  Included on pages with forms. The bundle must contain all styles required to correctly display your Form Builder components on the live site. For a clean directory structure, we recommend placing styles comprising the bundle into individual directories. For example:
  * ~/wwwroot/FormBuilder/Public/FormComponents/*.css

  
## Sample bundling and minification process
The following example demonstrates a bundling and minification process using Node.js with the [Grunt](https://gruntjs.com/) JavaScript task runner for automation. The process cleans all output directories, concatenates the CSS and JS files from relevant directories, performs minification, and outputs the resulting bundles into the default directories expected by the system. 
The process uses the following Grunt plugins:
  * **grunt-contrib-clean** for output directory cleaning
  * **grunt-contrib-concat** for bundling
  * **grunt-contrib-cssmin** for CSS minification
  * **grunt-terser** for JavaScript minification


First, add Grunt together with the plugins to your project’s _package.json_ file, create a new **Gruntfile.js** and load the plugins:
JS
**Gruntfile.js**
Copy
```
module.exports = function (grunt) {

    // Loads required Grunt plugins
    grunt.loadNpmTasks('grunt-contrib-clean');
    grunt.loadNpmTasks('grunt-contrib-concat');
    grunt.loadNpmTasks('grunt-contrib-cssmin');
    grunt.loadNpmTasks('grunt-terser');

    grunt.initConfig({
        // Contains task definitions
    });
}
```

Define the tasks used to create the individual bundles:
JS
**Gruntfile.js**
Copy
```
grunt.initConfig({

        // Cleans all bundle output directories
        clean: {
            formBuilder: ['wwwroot/Content/Bundles/Public/formComponents.css', 'wwwroot/Content/Bundles/Public/formComponents.min.css', 'wwwroot/Content/Bundles/Admin/formComponents.css',
                          'wwwroot/Content/Bundles/Admin/formComponents.min.css', 'wwwroot/Content/Bundles/Public/formComponents.js', 'wwwroot/Content/Bundles/Public/formComponents.min.js'],

            pageBuilder: ['wwwroot/Content/Bundles/Public/pageComponents.css', 'wwwroot/Content/Bundles/Public/pageComponents.min.css', 'wwwroot/Content/Bundles/Admin/pageComponents.css',
                          'wwwroot/Content/Bundles/Admin/pageComponents.min.css', 'wwwroot/Content/Bundles/Public/pageComponents.js', 'wwwroot/Content/Bundles/Public/pageComponents.min.js',
                          'wwwroot/Content/Bundles/Admin/pageComponents.js', 'wwwroot/Content/Bundles/Admin/pageComponents.min.js']
        },

        // Bundles files from the default source directories
        concat: {
            formBuilder: {
                files: {
                    // Styles for the live
                    'wwwroot/Content/Bundles/Public/formComponents.css': ['wwwroot/FormBuilder/Public/**/*.css'],
                    // Styles for the administration interface
                    'wwwroot/Content/Bundles/Admin/formComponents.css': ['wwwroot/FormBuilder/Admin/**/*.css'],
                    // Scripts included on both the live site and administration
                    'wwwroot/Content/Bundles/Public/formComponents.js': ['wwwroot/FormBuilder/Public/**/*.js']
                }
            },
            pageBuilder: {
                files: {
                    // Styles for the live site
                    'wwwroot/Content/Bundles/Public/pageComponents.css': ['wwwroot/PageBuilder/Public/**/*.css'],
                    // Styles for the administration interface
                    'wwwroot/Content/Bundles/Admin/pageComponents.css': ['wwwroot/PageBuilder/Admin/**/*.css'],
                    // Scripts for the live site
                    'wwwroot/Content/Bundles/Public/pageComponents.js': ['wwwroot/PageBuilder/Public/**/*.js'],
                    // Scripts for the administration
                    'wwwroot/Content/Bundles/Admin/pageComponents.js': ['wwwroot/PageBuilder/Admin/**/*.js']
                }
            }
        },

        // Minifies the resulting CSS bundles
        cssmin: {
            formBuilder: {
                files: {
                    'wwwroot/Content/Bundles/Public/formComponents.min.css': 'wwwroot/Content/Bundles/Public/formComponents.css',
                    'wwwroot/Content/Bundles/Admin/formComponents.min.css': 'wwwroot/Content/Bundles/Admin/formComponents.css'
                }
            },
            pageBuilder: {
                files: {
                    'wwwroot/Content/Bundles/Public/pageComponents.min.css': ['wwwroot/Content/Bundles/Public/pageComponents.css'],
                    'wwwroot/Content/Bundles/Admin/pageComponents.min.css': ['wwwroot/Content/Bundles/Admin/pageComponents.css']
                }
            }
        },

        // Minifies the resulting JS bundles
        terser: {
            formBuilder: {
                files: {
                    'wwwroot/Content/Bundles/Public/formComponents.min.js': ['wwwroot/Content/Bundles/Public/formComponents.js']
                }
            },
            pageBuilder: {
                files: {
                    'wwwroot/Content/Bundles/Public/pageComponents.min.js': ['wwwroot/Content/Bundles/Public/pageComponents.js'],
                    'wwwroot/Content/Bundles/Admin/pageComponents.min.js': ['wwwroot/Content/Bundles/Admin/pageComponents.js']
                }
            }
        }
});
```

Finally, register alias tasks that batch the created tasks for convenience:
JS
**Gruntfile.js**
Copy
```
// Running the 'formBuilder' and 'pageBuilder' tasks automatically executes all tasks provided in the array
grunt.registerTask('formBuilder', ['clean:formBuilder', 'concat:formBuilder', 'cssmin:formBuilder', 'terser:formBuilder']);
grunt.registerTask('pageBuilder', ['clean:pageBuilder', 'concat:pageBuilder', 'cssmin:pageBuilder', 'terser:pageBuilder']);
```

Using the created tasks, you can automate the bundling and minification process for your static assets. 
## Customize bundle locations and sources
You can customize the location where Xperience expects bundles and their contents (to be linked individually, in case the [bundle is not found](#handling-missing-bundles)). For this purpose, the system provides the `PageBuilderBundlesOptions` and `FormBuilderBundlesOptions` options classes. 
The options classes expose properties that allow you to configure the corresponding bundle:
  * **PageBuilderBundlesOptions**
    * [PageBuilderAdminScripts](#Bundlestaticassetsofbuildercomponents-PBSA)
    * [PageBuilderAdminStyles](#Bundlestaticassetsofbuildercomponents-PBCSSA)
    * [PageBuilderPublicScripts](#Bundlestaticassetsofbuildercomponents-PBSLS)
    * [PageBuilderPublicStyles](#Bundlestaticassetsofbuildercomponents-PBCSSLS)
  * **FormBuilderBundlesOptions**
    * [FormBuilderAdminStyles](#Bundlestaticassetsofbuildercomponents-FBCSSA)
    * [FormBuilderPublicScripts](#Bundlestaticassetsofbuildercomponents-FBS)
    * [FormBuilderPublicStyles](#Bundlestaticassetsofbuildercomponents-FBCSSLS)


These properties can be configured using the conventional ASP.NET Core [options pattern](https://docs.microsoft.com/en-us/aspnet/core/fundamentals/configuration/options). See the following sections for details about specific configuration options:
  * [Change the bundle location and name](#change-the-bundle-location-and-name)
  * [Add source directories for a bundle](#add-source-directories-for-a-bundle)
  * [Link jQuery as part of builder scripts](#link-jquery-as-part-of-builder-scripts)
  * [Use a different jQuery version](#use-a-different-jquery-version)


### Change the bundle location and name
You can change the file name and path where the system looks for individual bundles. Set the `WebRootBundlePath` property provided by the individual bundle objects within the options classes.
For example, the following code demonstrates how to set a custom location for the [FormBuilderPublicStyles](#Bundlestaticassetsofbuildercomponents-FBCSSLS) bundle within the application’s startup class:
C#
**Program.cs**
Copy
```
...

WebApplicationBuilder builder = WebApplication.CreateBuilder(args);

...

builder.Services.Configure<FormBuilderBundlesOptions>(options =>
{
    // Sets a custom path to the style sheet bundle used by form builder components on the live site
    // The specified path implicitly starts from the application's web root (~/wwwroot/ folder)
    options.FormBuilderPublicStyles.WebRootBundlePath = "My/Custom/Bundle/Path.min.css";
});
```

### Add source directories for a bundle
If the default directories (described in the [page](#page-builder-bundles) and [form](#form-builder-bundles) builder bundles sections) aren’t sufficient, the options class allows you to add additional source directories for each bundle. The system links all files from the specified directories if a corresponding bundle is not found. See [Handling missing bundles](#handling-missing-bundles).
Add new directories to the list provided via the Contents.IncludeWebRootDirectories property of the bundle you wish to configure. 
For example, the following code adds _~/wwwroot/Some/Directory_ to the list of sources for the bundle of Page Builder component styles linked on the live site. If the corresponding bundle is not found, the system includes the CSS files from the specified directory as well:
C#
**Program.cs**
Copy
```
...

WebApplicationBuilder builder = WebApplication.CreateBuilder(args);

...

builder.Services.Configure<PageBuilderBundlesOptions>(options =>
{
    // Adds a source directory for the bundle of styles used by Page Builder components on the live site
    // All added paths implicitly start from the application's web root (~/wwwroot/ folder)
    options.PageBuilderPublicStyles.Contents.IncludedWebRootDirectories.Add("Some/Directory");
});
```

### Link jQuery as part of builder scripts
You can have the system link the **jQuery 3.5.1** library on live site pages with Page Builder or Form Builder content. The library is not required by either builder and remains accessible primarily for backward compatibility.
_jQuery_ is considered deprecated and will be phased out completely in one of the future major releases. The library will not be updated, even in the case of new vulnerability disclosures. Projects with custom components that rely on the library should consider referencing an external implementation to prevent possible issues in the future.
Enable jQuery via the `CMSBuilderScriptsIncludeJQuery` configuration key:
JSON
**appsettings.json**
Copy
```
{
    "CMSBuilderScriptsIncludeJQuery": true
}
```

#### Use a different jQuery version
If you wish to use a different jQuery version on your pages, specify the path to your custom jQuery bundle using the `JQueryCustomBundleWebRootPath` property of the FormBuilderBundlesOptions object:
C#
**Program.cs**
Copy
```
...

WebApplicationBuilder builder = WebApplication.CreateBuilder(args);

...

builder.Services.Configure<FormBuilderBundlesOptions>(options =>
{
    // Specifies a custom jQuery bundle to link instead of the system default
    // All paths implicitly start from the application's web root (~/wwwroot/ folder)
    options.JQueryCustomBundleWebRootPath = "Path/To/My/jQuery/Bundle";
});
```

When you set a custom path, the system links the bundle at the specified location instead of the default jQuery version.
![]()
[]()[]()
