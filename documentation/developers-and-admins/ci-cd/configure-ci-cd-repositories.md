---
source: https://docs.kentico.com/documentation/developers-and-admins/ci-cd/configure-ci-cd-repositories
scrape_date: 2026-01-22
---

  * [Home](/documentation)
  * [Developers and admins](/documentation/developers-and-admins)
  * [CI/CD](/documentation/developers-and-admins/ci-cd)
  * Configure CI/CD repositories 


# Configure CI/CD repositories
[Continuous Integration](/documentation/developers-and-admins/ci-cd/continuous-integration) and [Continuous Deployment](/documentation/developers-and-admins/ci-cd/continuous-deployment) repositories can be configured to only store specific types of objects. This is useful if you do not wish to synchronize or deploy all supported types, particularly in larger projects. For all included object types, CI/CD also allows object-level filtering using code names.
All configuration is defined within [repository configuration files](/documentation/developers-and-admins/ci-cd/continuous-deployment#generate-cd-configuration-files) located in the root of the CI/CD repository. Use the following process if you need to make changes:
  1. Edit the repository configuration file.
  2. Adjust the configuration according to your object filtering requirements. See the following sections for details:
     * [Include/Exclude object types](#includeexclude-object-types)
     * [Filter individual objects](#filter-individual-objects)
     * [Configure CD restore mode](#configure-cd-restore-mode)
  3. Save the file.
  4. Run the store command ([CI](/documentation/developers-and-admins/ci-cd/continuous-integration#store-object-data-to-the-repository)/[CD](/documentation/developers-and-admins/ci-cd/continuous-deployment#store-objects-to-a-cd-repository)) to bring your repository into a consistent state.


Excluding objects in the configuration file affects both store and restore actions for CI/CD.
**Continuous Integration environments**
Always use the same CI repository configuration settings across your entire development environment to maintain consistency. Whenever you make any changes, use your source control system to synchronize the configuration file and the other repository content. When a developer loads a new version of the configuration file from the source control, the new settings apply after restoring objects into the database or manually restarting the application.
* * *
**Repository configuration templates**
The [Repository configuration templates](/documentation/developers-and-admins/ci-cd/configure-ci-cd-repositories/repository-configuration-templates) page provides `repository.config` templates for CI/CD scenarios. It details specific configurations for collaborative local development, initial instance population (including SaaS considerations), adding new content types, updating specific content items, and deploying changes to object types. Each template includes explanations of key settings like restore modes and filtering options, helping users effectively define and customize their CI/CD workflows for various tasks.
## Include/Exclude object types
By editing your **repository.config** file, you can configure CI/CD to include or exclude certain object types from the repository.
**Xperience by Kentico 30.4.0** introduced a new version of the _repository.config_ file with advanced options for content item exclusion. See [Migrate CI/CD repository.config to v2](/documentation/developers-and-admins/ci-cd/configure-ci-cd-repositories/config-v2-migration) for details.
This section describes the options available with the new config. See [Legacy repository.config options](#IncludeExcludeLegacy) for the configuration options available when using the legacy _repository.config_ file.
  1. Add `<ObjectType>` elements into one of the following sections of the config file:
     * `<IncludedObjectTypes>` – defines an _allowlist_ of object types. If you specify one or more object types, only objects of the given type are processed. The configuration must explicitly specify all object types that are to be included in the repository. To include all supported objects, use `<IncludeAll />` as shorthand. See [Reference - CI/CD object types](/documentation/developers-and-admins/ci-cd/reference-ci-cd-object-types) for an overview of object types that support CI/CD.
     * `<ExcludedObjectTypes>` – defines a _denylist_ of object types. All included object types are processed except for the listed types.
     * This filtering approach applies to general system objects. To include or exclude content items, see [Include/Exclude content items](#includeexclude-content-items).
  2. Set the values of individual `<ObjectType>` elements to the [names of object types](/documentation/developers-and-admins/ci-cd/reference-ci-cd-object-types) supported by CI/CD.


The system uses the following rules to automatically exclude child and binding objects:
  * Child object types follow the configuration of their parent object type.
  * Binding object types are excluded if all object types within the given relationship are excluded.


The `<IncludedObjectTypes>` allowlist only allows you to specify the main object types, not child types or bindings.
In the `<ExcludedObjectTypes>` denylist, you can exclude both the main object types and specific binding or child object types.
**Object removal due to dependencies**
Objects excluded in the configuration file may still be deleted when restoring data from the repository due to parent-child relationships and other dependencies. For example, restoring the deletion of a settings category will consequently delete all setting keys within that category, regardless of whether the `cms.settingskey` object type was excluded.
XML
**Example**
Copy
```
<?xml version="1.0" encoding="utf-8"?>
<RepositoryConfiguration
  version="2"
  xmlns:xsd="http://www.w3.org/2001/XMLSchema"
  xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance">

  <!-- Only includes email channel objects -->
  <IncludedObjectTypes>
    <!-- Shorthand to include all supported object types -->
    <IncludeAll />
    <!-- Include only specific object types using their code names -->
    <ObjectType>cms.channel</ObjectType>
    <ObjectType>emaillibrary.emailchannel</ObjectType>
  </IncludedObjectTypes>

  <!-- Excludes sender addresses, which are child objects
  under the emaillibrary.emailchannel object. -->
  <ExcludedObjectTypes>
    <ObjectType>emaillibrary.emailchannelsender</ObjectType>
  </ExcludedObjectTypes>

</RepositoryConfiguration>
```

### Include/Exclude content items
Content items (pages, reusable items, headless items, emails) can be included or excluded based on their [content type](/documentation/developers-and-admins/development/content-types). By default, the repository tracks all items of all included types. This can be further refined using [item-level filters](#filter-individual-content-items).
To include or exclude content items, add the following elements to **repository.config** :
  * `<IncludedContentItemsOfType>` – stores a list of content type code names. The repository tracks all content items that match the types specified in this collection. It must contain one or more `<ContentType>` elements specifying the code names of [content item types](/documentation/developers-and-admins/development/content-types) that are to be tracked by the repository. Omitting this element results in **NO** content items being tracked by the repository.
Instead of listing each content type individually, you can also use the `<IncludeAll />` element, which provides a shorthand method for including all system content items (mirroring its function for including all object types). However, careless usage of `IncludeAll` can have severely negative performance consequences.
**`<IncludeAll />`usage**
In [Continuous Deployment](/documentation/developers-and-admins/ci-cd/continuous-deployment) scenarios outside of the [initial CD deployment](/documentation/developers-and-admins/ci-cd/configure-ci-cd-repositories/repository-configuration-templates#initial-deployment-to-target-instance), **always** prefer to explicitly list all content item types tracked by the repository to using `<IncludeAll />`.
When you run [kxp-cd-store](/documentation/developers-and-admins/ci-cd/continuous-deployment#store-objects-to-a-cd-repository) (store operation), the system correctly identifies content item types present in the source environment and serializes them. However, when you run [kxp-cd-restore](/documentation/developers-and-admins/ci-cd/continuous-deployment#restore-the-cd-repository-to-the-database) (restore operation) with `<IncludeAll />`, the process iterates over **all content item of all types** that exist on the target environment, regardless of whether they were present in the source repository data. This can lead to extremely long processing times and potential timeouts if the target environment contains substantially more content types or items than the source repository, as every target item of every type must be processed during the restore.
**Recommendation:** For these reasons, we strongly advise against using `<IncludeAll />` for regular CD deployments to production or other target environments [after the initial setup](/documentation/developers-and-admins/ci-cd/configure-ci-cd-repositories/repository-configuration-templates#initial-deployment-to-target-instance). Instead, explicitly list each `<ContentType>` you intend to track via CD in the `<IncludedContentItemsOfType>` section.
Take a look at [Repository configuration templates](/documentation/developers-and-admins/ci-cd/configure-ci-cd-repositories/repository-configuration-templates) for starter CD repository templates designed to cover most common deployment scenarios.
  * `<ExcludedContentItemsOfType>` – provides an optional mechanism to refine which content items are tracked. While the element itself is optional, if included in the configuration, it must contain one or more `<ContentType>` elements specifying content type code names to exclude.
Functionally, it acts as a subtraction – the content types listed within `<ExcludedContentItemsOfType>` are removed from the set of items initially defined by the `<IncludedContentItemsOfType>` element. This can be useful for scenarios where you want to include a broad category (using `<IncludeAll />` within `<IncludedContentItemsOfType>`) but specifically omit certain types from being tracked by the repository.


XML
**Examples**
Copy
```
<?xml version="1.0" encoding="utf-8"?>
<RepositoryConfiguration
    Version="2"
    xmlns:xsd="http://www.w3.org/2001/XMLSchema"
    xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance">

    <!-- Defines the types of content items tracked by the repository.
    NOTE: This element is specific to Version 2 configurations. -->
    <IncludedContentItemsOfType>
        <!-- Includes all content items of a specific type -->
        <ContentType>DancingGoat.Coffee</ContentType>
        <ContentType>DancingGoat.Article</ContentType>
    </IncludedContentItemsOfType>

    <!-- Optional: Define types to exclude from the set defined above
    NOTE: This element must contain at least one ContentType element if added. -->
    <ExcludedContentItemsOfType>
        <!-- Exclude items of the 'DancingGoat.Article' type -->
        <ContentType>DancingGoat.Article</ContentType>
    </ExcludedContentItemsOfType>

</RepositoryConfiguration>
```

### Legacy include/exclude options
This section describes filtering options available in legacy (pre version 30.4.0) **repository.config** files.
  1. Add `<ObjectType>` elements into one of the following sections of the config file:
     * `<IncludedObjectTypes>` – defines an _allowlist_ of object types. If you specify one or more object types, only objects of the given type are processed. No restrictions apply if the allowlist is empty.
     * `<ExcludedObjectTypes>` – defines a _denylist_ of object types. All included object types are processed except for the listed types.
  2. Set the values of individual `<ObjectType>` elements to the [names of object types](/documentation/developers-and-admins/ci-cd/reference-ci-cd-object-types) supported by CI/CD.
To find the _ObjectType_ values, check the names of the corresponding folders in the repository folder.


The system uses the following rules to automatically exclude child and binding objects:
  * Child object types follow the configuration of their parent object type.
  * Binding object types are excluded if all object types within the given relationship are excluded.


The `<IncludedObjectTypes>` allowlist only allows you to specify the main object types, not child types or bindings.
In the `<ExcludedObjectTypes>` denylist, you can exclude both the main object types and specific binding or child object types.
XML
**Example**
Copy
```
<?xml version="1.0" encoding="utf-8"?>
<RepositoryConfiguration
  xmlns:xsd="http://www.w3.org/2001/XMLSchema"
  xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance">

  <!-- Only includes email channel objects -->
  <IncludedObjectTypes>
    <ObjectType>cms.channel</ObjectType>
    <ObjectType>emaillibrary.emailchannel</ObjectType>
  </IncludedObjectTypes>

  <!-- Excludes sender addresses, which are child objects
  under the emaillibrary.emailchannel object. -->
  <ExcludedObjectTypes>
    <ObjectType>emaillibrary.emailchannelsender</ObjectType>
  </ExcludedObjectTypes>

</RepositoryConfiguration>
```

## Filter individual objects
**Xperience by Kentico 30.4.0** introduced a new version of the _repository.config_ file with advanced filtering options. See [Migrate CI/CD repository.config to v2](/documentation/developers-and-admins/ci-cd/configure-ci-cd-repositories/config-v2-migration) for details.
This section describes the options available with the new version. See [Legacy repository.config options](#FilteringLegacy) for the configuration options available when using the legacy _repository.config_ file.
You can exclude individual objects from CI/CD via the `<ObjectFilters>` element. The system combines the filtering rules for individual objects with existing rules for entire object types. This filtering approach applies to general system objects. To filter individual content items, see [Filter individual content items](#filter-individual-content-items).
Within the `<ObjectFilters>` element, child elements `<IncludedCodeNames>` and `<ExcludedCodeNames>` specify item code name patterns that filter individual tracked items.
Within the content of each `<IncludedCodeNames>` and `<ExcludedCodeNames>` element, list the **code names** of objects to exclude or include.
  * Add the `%` wildcard to the end or start of the code name entry to cover multiple objects with a shared code name prefix or suffix. Using only the `%` wildcard as the value represents all objects of the given type. 
    * `prefix_%` matches all code names starting with **prefix_**.
    * `%_suffix` matches all code names ending with **_suffix**.
  * You can add multiple code name entries for a single object type, separated by semicolons.
  * The code name values are case insensitive.


This name-based filtering acts as a secondary layer in object type processing – it only applies to objects included via [<IncludedObjectTypes>](#includeexclude-object-types) and not removed by [<ExcludedObjectTypes>](#filter-individual-objects). It cannot introduce objects types not selected in the first filtering stage.
**ObjectType attribute remarks**
By default, values specified inside `<IncludedCodeNames>` and `<ExcludedCodeNames>` elements apply globally to ALL included objects.
```
<!-- Excludes ALL objects whose code name contains a 'test' prefix -->
<IncludedCodeNames>test%</IncludedCodeNames>
```

However, both elements can optionally specify the `ObjectType` attribute, which scopes the filtering only to objects of the provided type.
```
<!-- Excludes all cms.user objects whose code name contains a 'test' prefix -->
<IncludedCodeNames ObjectType="cms.user">test%</IncludedCodeNames>
```

The attribute can target the following object types:
  * You can use both main object types and child types [included in your repository](#includeexclude-object-types).
  * Binding types typically do not have a code name field, so they cannot be used.
  * Rules for specific object types override rules for all object types. You can disable rules for all object types by adding empty `<IncludedCodeNames>` or `<ExcludedCodeNames>` elements for individual object types.
  * Do not add multiple elements for the same object type. Instead, use multiple values separated by semicolons and/or wildcards.
XML
**ObjectType attribute usage examples**
Copy
```
<ObjectFilters>
  <!-- Excludes included objects of ALL types
  whose code name starts with the "test" or "demo" prefix. -->
  <ExcludedCodeNames>test%;demo%</ExcludedCodeNames>
  <!-- Overrides the preceding rule for objects of the 'cms.user' type -->
  <ExcludedCodeNames ObjectType="cms.user"></ExcludedCodeNames>
</ObjectFilters>
```



When you exclude an object using either the allowlist or denylist, the system automatically excludes dependent objects according to the following rules:
  * **Child objects** – all child objects are excluded together with their parent. For example, excluding a country object excludes all states under the given country.
  * **Binding objects** – binding objects are automatically excluded if at least one of the related objects is excluded.
  * **Channel objects** – excluding a channel automatically excludes all channel-specific objects belonging to the given channel. When excluding channels, exclude based on the `cms.channel` object type, not the specific channel configuration objects `emaillibrary.emailchannel` or `cms.websitechannel`.
    * Objects that are only connected to channels through bindings are not excluded.


**Notes**
  * It is not possible to exclude objects without a code name field, such as [binding tables](/documentation/developers-and-admins/customization/object-types/object-type-configuration/model-object-type-relationships).
  * The object exclusion rules apply globally across all objects and channels.


XML
**Example**
Copy
```
<?xml version="1.0" encoding="utf-8"?>
<RepositoryConfiguration
  version="2"
  xmlns:xsd="http://www.w3.org/2001/XMLSchema"
  xmlns:x="http://www.w3.org/2001/XMLSchema-instance">

  <!-- Includes all supported object types -->
  <IncludedObjectTypes>
    <IncludeAll />
  </IncludedObjectTypes>

  <ObjectFilters>

    <!-- Excludes all objects of any type whose code name starts with the "test" prefix, except for users -->
    <ExcludedCodeNames>test%</ExcludedCodeNames>
    <ExcludedCodeNames ObjectType="cms.user"></ExcludedCodeNames>

    <!-- Includes only the "birthdaysuccessgroup" and "recentbuyers" contact groups -->
    <IncludedCodeNames ObjectType="om.contactgroup">birthdaysuccessgroup;recentbuyers</IncludedCodeNames>

    <!-- Excludes the "articles" and "graphics" media libararies -->
    <ExcludedCodeNames ObjectType="media.library">articles;graphics</ExcludedCodeNames>

  </ObjectFilters>
</RepositoryConfiguration>
```

### Filter individual content items
For more granular control beyond type-level inclusion/exclusion, the `ContentItemFilters` element allows filtering of individual content items based on their code names, irrespective of type.
Within this element, child elements `IncludedContentItemNames` and `ExcludedContentItemNames` specify item code name patterns that filter individual tracked items. You can use the `%` character as a wildcard to match multiple code names. For example:
  * `prefix_%` matches all code names starting with **prefix_**.
  * `%_suffix` matches all code names ending with **_suffix**.


This name-based filtering acts as a secondary layer in content item processing – it only applies to content items included via `IncludedContentItemsOfType` and not removed by `ExcludedContentItemsOfType`. It cannot introduce items whose types were not selected in the first filtering stage.
The following code demonstrates content item filtering on a simple example:
XML
**Example**
Copy
```
<?xml version="1.0" encoding="utf-8"?>

<!-- Example: Include only 'DancingGoat.Article'
items whose code names start with 'News_' -->
<RepositoryConfiguration
  version="2"
  xmlns:xsd="http://www.w3.org/2001/XMLSchema"
  xmlns:x="http://www.w3.org/2001/XMLSchema-instance">

  <!-- Step 1: Include the relevant content item TYPE -->
  <IncludedContentItemsOfType>
    <!-- Only items of this type are considered for the filtering step -->
    <ContentType>DancingGoat.Article</ContentType>
  </IncludedContentItemsOfType>

  <!-- Step 2: Apply secondary filtering based on
  individual item code names. This filter only applies to items
    whose type ('DancingGoat.Article') was included above -->
  <ContentItemFilters>

    <!-- Include only items whose code names match the pattern 'News_%' -->
    <IncludedContentItemNames>
        News_%
    </IncludedContentItemNames>

    <!-- You could also use <ExcludedContentItemNames>
    here to exclude specific names. For example:

    <ExcludedContentItemNames>
        News_Internal%
    </ExcludedContentItemNames>

    -->
  </ContentItemFilters>

</RepositoryConfiguration>
```

This setup first selects all items of the type `DancingGoat.Article` and then applies the `ContentItemFilters` to keep only those articles whose code names begin with **News_**.
### Legacy filtering options
This section describes filtering options available in legacy (pre version 30.4.0) **repository.config** files.
You can exclude individual objects from CI/CD by editing your repository configuration file. The system combines the filtering rules for individual objects with existing rules for entire object types.
  1. Add the following elements into the `<ObjectFilters>` section:
     * `<IncludedCodeNames>` – defines an _allowlist_ of objects. If you specify one or more object code names, only matching objects are processed. No restrictions apply if the allowlist is empty.
     * `<ExcludedCodeNames>` – defines a _denylist_ of objects. Adds further filtering for the set of included objects. Objects that match the specified code names are excluded from processing.
  2. Specify the type of objects that you wish to exclude for each `<IncludedCodeNames>` and `<ExcludedCodeNames>` element:
     * If you do not set the `ObjectType` attribute for the element, it applies to objects of ALL types.
     * To apply the filtering rule to a specific object type, set the element’s `ObjectType` attribute to the given object type name.
       * You can use both main object types and child types (that are included in your CI/CD configuration).
       * Binding types typically do not have a code name field, so they cannot be used.
       * Rules for specific object types override rules for all object types. You can disable rules for all object types by adding empty `<IncludedCodeNames>` or `<ExcludedCodeNames>` elements for individual object types.
       * Do not add multiple elements for the same object type. Instead, use multiple values separated by semicolons and/or wildcards.
  3. Within the content of each `<IncludedCodeNames>` and `<ExcludedCodeNames>` element, list the **code names** of objects to exclude or include.
     * Add the `%` wildcard to the end or start of the code name entry to cover multiple objects with a shared code name prefix or suffix. Using only the `%` wildcard as the value represents all objects of the given type.
     * You can add multiple code name entries for a single object type, separated by semicolons.
     * The code name values are case insensitive.


When you exclude an object using either the allowlist or denylist, the system automatically excludes dependent objects according to the following rules:
  * **Child objects** – all child objects are excluded together with their parent. For example, excluding a country object excludes all states under the given country.
  * **Binding objects** – binding objects are automatically excluded if at least one of the related objects is excluded.
  * **Channel objects** – excluding a channel automatically excludes all channel-specific objects belonging to the given channel. When excluding channels, exclude based on the `cms.channel` object type, not the specific channel configuration objects `emaillibrary.emailchannel` or `cms.websitechannel`.
    * Objects that are only connected to channels through bindings are not excluded.


**Notes**
  * It is not possible to exclude objects without a code name field.
  * The object exclusion rules apply globally across all objects and channels.
  * It is not possible to exclude [website channel pages](/documentation/business-users/website-content) based on their [page path](/documentation/developers-and-admins/development/content-retrieval/retrieve-page-content#filter-pages-based-on-content-tree-structure). You can exclude individual pages using their code name (e.g., based on a predetermined code name prefix or suffix), but large-scale page filtering is currently not supported.


XML
**Example**
Copy
```
<?xml version="1.0" encoding="utf-8"?>
<RepositoryConfiguration
  xmlns:xsd="http://www.w3.org/2001/XMLSchema"
  xmlns:x="http://www.w3.org/2001/XMLSchema-instance">

  <!-- Includes all supported object types -->
  <IncludedObjectTypes>
  </IncludedObjectTypes>

  <ExcludedObjectTypes>
  </ExcludedObjectTypes>

  <ObjectFilters>
    <!-- Excludes all content items (reusable, pages, emails) whose code name starts with 'Banner'.
    Always use the 'cms.contentitem' object type to filter content items.
    Filtering based on other entities that compose content items can lead to
    repository inconsistencies and incorrect behavior. -->
    <ExcludedCodeNames ObjectType="cms.contentitem">Banner%</ExcludedCodeNames>

    <!-- Excludes all objects of any type whose code name starts with the "test" prefix, except for users -->
    <ExcludedCodeNames>test%</ExcludedCodeNames>
    <ExcludedCodeNames ObjectType="cms.user"></ExcludedCodeNames>

    <!-- Includes only the "birthdaysuccessgroup" and "recentbuyers" contact groups -->
    <IncludedCodeNames ObjectType="om.contactgroup">birthdaysuccessgroup;recentbuyers</IncludedCodeNames>

    <!-- Excludes the "articles" and "graphics" media libararies -->
    <ExcludedCodeNames ObjectType="media.library">articles;graphics</ExcludedCodeNames>
  </ObjectFilters>
</RepositoryConfiguration>
```

## Configure CD restore mode
For [Continuous Deployment](/documentation/developers-and-admins/ci-cd/continuous-deployment), the repository configuration file **must** contain the `<RestoreMode>` element. The restore mode specifies which types of object operations are performed in the target database when restoring the repository. The restore mode provides restrictive options that allow you to safely deploy to production environments where you do not wish to delete or overwrite live data.
The following values are possible:
  * `Create` – only creates new objects. Never deletes or modifies existing objects.
  * `CreateUpdate` – creates new objects and updates existing objects. Never deletes objects. This is the default option for newly generated CD repository configuration files.
  * `Full` – creates new objects, updates existing objects, and deletes objects that do not exist in the repository.


XML
Copy
```
<?xml version="1.0" encoding="utf-8"?>
<RepositoryConfiguration
  version="2"
  xmlns:xsd="http://www.w3.org/2001/XMLSchema"
  xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance">

  <RestoreMode>
    CreateUpdate
  </RestoreMode>

</RepositoryConfiguration>
```

`<RestoreMode>` only affects restore operations. The limitations do not apply if you run the store command to deploy object data to an existing CD repository (including updates and removal of the object XML files).
![]()
[]()[]()
