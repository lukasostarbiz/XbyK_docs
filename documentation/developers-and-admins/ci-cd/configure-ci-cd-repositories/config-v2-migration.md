---
source: https://docs.kentico.com/documentation/developers-and-admins/ci-cd/configure-ci-cd-repositories/config-v2-migration
scrape_date: 2026-01-22
---

  * [Home](/documentation)
  * [Developers and admins](/documentation/developers-and-admins)
  * [CI/CD](/documentation/developers-and-admins/ci-cd)
  * [Configure CI/CD repositories](/documentation/developers-and-admins/ci-cd/configure-ci-cd-repositories)
  * Migrate CI/CD repository.config to v2 


# Migrate CI/CD repository.config to v2
Xperience by Kentico version **30.4.0** introduced a new version of **repository.config** files. This updated format provides support for advanced filtering of [content items](/documentation/developers-and-admins/api/content-item-api/content-item-database-structure). The migration steps described on this page introduce all the added features and their recommended usage. However, you can also find the information in [Include/Exclude object types](/documentation/developers-and-admins/ci-cd/configure-ci-cd-repositories#includeexclude-object-types) and [Filter individual objects](/documentation/developers-and-admins/ci-cd/configure-ci-cd-repositories#filter-individual-objects).
## Migrate v1 repository.config files to v2
Follow these steps to migrate your existing _repository.config_ files to v2:
  1. Prepare a clean migration environment where you can test CI/CD restore operations.
  2. [Update the RepositoryConfiguration element](#update-the-repositoryconfiguration-element)
  3. [Include object types to the repository](#include-object-types)
  4. [Include content items to the repository](#include-content-items)
     * Not required if you don’t use CI/CD to track and synchronize content item data between instances.
  5. [Update object type filters](#update-object-type-filters)
  6. [Configure content item filters](#configure-content-item-filters)
     * Not required if you don’t use CI/CD to track and synchronize content item data between instances.
  7. Run a test serialization with the migrated config file.
CMD
Copy
```
dotnet run --no-build --kxp-ci-store
```



### Update the RepositoryConfiguration element
Add `Version="2"` to the `RepositoryConfiguration` element inside **respository.config**.
XML
Copy
```
<!-- Version 1 config -->
<RepositoryConfiguration
  xmlns:xsd="http://www.w3.org/2001/XMLSchema"
  xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance">

<!-- Version 2 config -->
<RepositoryConfiguration
  Version="2"
  xmlns:xsd="http://www.w3.org/2001/XMLSchema"
  xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance">
```

### Include object types
In v1 configs, the repository automatically included all object types if no restrictions were specified using the `IncludedObjectTypes` element.
In v2, configuration files must explicitly specify all object types included in the repository via the `IncludedObjectTypes` element. To include all supported objects, use `<IncludeAll />` as shorthand. See [Reference - CI/CD object types](/documentation/developers-and-admins/ci-cd/reference-ci-cd-object-types) for an overview of object types that support CI/CD.
The `ExcludedObjectTypes` element’s behavior remains unchanged. However, in v2 config files, it excludes object types only from the list specified via `IncludedObjectTypes`.
XML
Copy
```
<!-- Version 1 config -->
<RepositoryConfiguration ...>

    <!-- This element is optional and if not present,
    CI/CD by default serializes all supported objects. -->
    <IncludedObjectTypes>
    </IncludedObjectTypes>

</RepositoryConfiguration>

<!-- Version 2 config -->
<RepositoryConfiguration ...>

    <!-- This element is required and must explicitly
    list all object types held by the repository. -->
    <IncludedObjectTypes>
        <!-- Shorthand to include all supported objects -->
        <IncludeAll />
        <!-- Includes users and roles -->
        <ObjectType>CMS.User</ObjectType>
        <ObjectType>CMS.Role</ObjectType>
    </IncludedObjectTypes>

</RepositoryConfiguration>
```

### Include content items
Content items are no longer tracked by including `cms.contentitem` and related dependencies. Instead, the process now mirrors object type handling – users must explicitly list all tracked content items within the configuration file.
Two new configuration elements facilitate this:
  * `IncludedContentItemsOfType` – stores a list of content type code names. The repository tracks all content items that match the types specified in this collection. Omitting this element results in **NO** content items tracked by the repository. The `<IncludeAll />` element provides a shorthand method for including all system content items, mirroring its function for including all object types. However, careless usage of `IncludeAll` can have severly negative performance consequences.
**IncludeAll usage**
In [Continuous Deployment](/documentation/developers-and-admins/ci-cd/continuous-deployment) scenarios outside of the [initial CD deployment](/documentation/developers-and-admins/ci-cd/configure-ci-cd-repositories/repository-configuration-templates#initial-deployment-to-target-instance), always prefer explicitly listing all types tracked by the repository to using `<IncludeAll />`.
When you run [kxp-cd-store](/documentation/developers-and-admins/ci-cd/continuous-deployment#store-objects-to-a-cd-repository) (store operation), the system correctly identifies content item types present in the source environment and serializes them. However, when you run [kxp-cd-restore](/documentation/developers-and-admins/ci-cd/continuous-deployment#restore-the-cd-repository-to-the-database) (restore operation) with `<IncludeAll />`, the process iterates over **all content item of all types** that exist on the target environment, regardless of whether they were present in the source repository data. This can lead to extremely long processing times and potential timeouts if the target environment contains substantially more content types or items than the source repository, as every target item of every type must be processed during the restore.
**Recommendation:** For these reasons, we strongly advise against using `<IncludeAll />` for regular CD deployments to production or other target environments [after the initial setup](/documentation/developers-and-admins/ci-cd/configure-ci-cd-repositories/repository-configuration-templates#initial-deployment-to-target-instance). Instead, explicitly list the code names of each content item type you intend to track via CD in the `<IncludedContentItemsOfType>` section using `<ContentType>` elements.
  * `ExcludedContentItemsOfType` – provides an optional mechanism to refine which content items are tracked. While the element itself is optional, if included in the configuration, it must contain one or more `ContentType` elements specifying the code names of content types to exclude. Functionally, it is a subtraction. The content types listed within `ExcludedContentItemsOfType` are removed from the set of items initially defined by the `IncludedContentItemsOfType` element. This is useful for scenarios where you want to include a broad category (using `<IncludeAll />` within `IncludedContentItemsOfType`) but specifically omit certain types from being tracked by the repository.


XML
Copy
```
<!-- Version 2 config -->
<RepositoryConfiguration ...>

    <!-- Defines the types of content items tracked by the repository.
    NOTE: This element is specific to Version 2 configurations. -->
    <IncludedContentItemsOfType>
        <!-- Shorthand to include all content items of all types -->
        <IncludeAll />
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

#### Website channel folders
Website channel folders (used to group pages in the content tree) must be tracked using a dedicated **##WebPageFolders##** placeholder.
```
<IncludedContentItemsOfType>
  <ContentType>##WebPageFolders##</ContentType>
</IncludedContentItemsOfType>
```

### Update object type filters
Several object types directly related to content item data storage can no longer be filtered using `ObjectFilters` ([Filter individual objects](/documentation/developers-and-admins/ci-cd/configure-ci-cd-repositories#FilteringLegacy)). Attempting to filter any of the following object types directly using elements like `<IncludedCodeNames ObjectType="...">` now results in an exception during processing:
Content items:
  * cms.contentitem
  * cms.contentitemcommondata
  * cms.contentitemlanguagemetadata
  * cms.contentitemreference


Pages:
  * cms.webpageitem
  * cms.webpageformerurlpath
  * cms.webpageurlpath


Emails:
  * emaillibrary.emailconfiguration
  * emaillibrary.sendconfiguration


Headless items:
  * cms.headlessitem


To control which content items are tracked and synchronized via CI/CD, you must instead use the dedicated content item filtering elements: [Configure content item filters](#configure-content-item-filters).
### Configure content item filters
For more granular control beyond type-level inclusion/exclusion, the `ContentItemFilters` element allows filtering of individual content items based on their code names, irrespective of type.
Within this element, child elements `IncludedContentItemNames` and `ExcludedContentItemNames` specify item code name patterns that filter individual tracked items. You can use the `%` character as a wildcard to match multiple code names. For example:
  * `prefix_%` matches all code names starting with **prefix_**.
  * `%_suffix` matches all code names ending with **_suffix**.


This name-based filtering acts as a secondary layer in content item processing – it only applies to content items included via `IncludedContentItemsOfType` and not removed by `ExcludedContentItemsOfType`. It cannot introduce items whose types were not selected in the first filtering stage.
The following code demonstrates content item filtering on a simple example:
XML
Copy
```
<!-- Example: Include only 'DancingGoat.Article'
items whose code names start with 'News_' -->
<RepositoryConfiguration ...>

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
![]()
[]()[]()
