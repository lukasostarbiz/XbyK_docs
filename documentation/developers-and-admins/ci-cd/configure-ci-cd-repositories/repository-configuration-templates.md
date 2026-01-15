# Repository configuration templates
  * [ Copy page link ](documentation/developers-and-admins/ci-cd/configure-ci-cd-repositories/repository-configuration-templates#) | [Get HelpService ID](documentation/developers-and-admins/ci-cd/configure-ci-cd-repositories/repository-configuration-templates#)
Core MVC 5


[✖](documentation/developers-and-admins/ci-cd/configure-ci-cd-repositories/repository-configuration-templates# "Close page link panel") [Copy to clipboard](documentation/developers-and-admins/ci-cd/configure-ci-cd-repositories/repository-configuration-templates#)
This page lists common CI/CD scenarios and their corresponding recommended repository configuration. In a typical deployment workflow, you may have multiple `repository.config` files, each suitable for a different type of deployment. You can use the templates provided on this page as a starter when defining your CI/CD deployment workflows.
The templates demonstrated on this page make use of features introduced in **v2** of the `repository.config` file and will not work with the legacy configuration format. See: [Migrate CI/CD repository.config to v2](documentation/developers-and-admins/ci-cd/configure-ci-cd-repositories/config-v2-migration) to learn how to convert legacy configuration files to the new format.
  * [Continuous Integration](documentation/developers-and-admins/ci-cd/configure-ci-cd-repositories/repository-configuration-templates#continuous-integration)
    * [Collaborative development workflow](documentation/developers-and-admins/ci-cd/configure-ci-cd-repositories/repository-configuration-templates#collaborative-development-workflow)
  * [Continuous Deployment](documentation/developers-and-admins/ci-cd/configure-ci-cd-repositories/repository-configuration-templates#continuous-deployment)
    * [Initial deployment to target instance](documentation/developers-and-admins/ci-cd/configure-ci-cd-repositories/repository-configuration-templates#initial-deployment-to-target-instance)
    * [Add a new type of content items](documentation/developers-and-admins/ci-cd/configure-ci-cd-repositories/repository-configuration-templates#add-a-new-type-of-content-items)
    * [Deploy specific content item data](documentation/developers-and-admins/ci-cd/configure-ci-cd-repositories/repository-configuration-templates#deploy-specific-content-item-data)
    * [Deploy object type changes](documentation/developers-and-admins/ci-cd/configure-ci-cd-repositories/repository-configuration-templates#deploy-object-type-changes)


## Continuous Integration
### Collaborative development workflow
For development teams working collaboratively on local instances and using the CI/CD feature to synchronize changes (e.g., via a shared Git repository), the following `repository.config` configuration is recommended as a baseline. It ensures that all supported object types and content items are included by default, simplifying the setup for typical local development requirements.
XML
Copy
```
<?xml version="1.0" encoding="utf-8"?>
<RepositoryConfiguration
  version="2"
  xmlns:xsd="http://www.w3.org/2001/XMLSchema"
  xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance">

  <IncludedObjectTypes>
    <!-- Includes all supported object types by default -->
    <IncludeAll />
  </IncludedObjectTypes>

  <IncludedContentItemsOfType>
    <!-- Includes all supported content items by default -->
    <IncludeAll />
  </IncludedContentItemsOfType>

  <ObjectFilters>
  </ObjectFilters>

</RepositoryConfiguration>
```

This configuration provides a simple starting point; it assumes fairly content-light instances primarily used for feature development. You can customize it further by replacing `<IncludeAll />` with specific `<ContentType>` elements or by adding exclusion rules (`<ExcludedObjectTypes>`, `<ExcludedContentItemsOfType>`, `<ContentItemFilters>`) if needed for your specific project workflow and requirements.
## Continuous Deployment
### Initial deployment to target instance
When using CD to perform an initial population of a target instance with data from the repository (especially content items), the following configuration is recommended.
  * **Private cloud deployments** – Use the `Create` [restore mode](documentation/developers-and-admins/ci-cd/configure-ci-cd-repositories#configure-cd-restore-mode) to ensure only new objects and items are added, preventing accidental updates or deletions of any pre-existing data on the target instance. Using [IncludedObjects](documentation/developers-and-admins/ci-cd/configure-ci-cd-repositories#includeexclude-object-types) and [IncludedContentItemOfType](documentation/developers-and-admins/ci-cd/configure-ci-cd-repositories#includeexclude-content-items) with `<IncludeAll />` ensures all data stored in the repository source is considered for this initial push.
**IncludeAll usage**
In [Continuous Deployment](documentation/developers-and-admins/ci-cd/continuous-deployment) scenarios outside of the [initial CD deployment](documentation/developers-and-admins/ci-cd/configure-ci-cd-repositories/repository-configuration-templates#initial-deployment-to-target-instance), always prefer explicitly listing all types tracked by the repository to using `<IncludeAll />`.
See [Include content items](documentation/developers-and-admins/ci-cd/configure-ci-cd-repositories#includeexclude-content-items) for more information. 
  * **SaaS deployments** – Use the `Create` restore mode to ensure only new objects and items are added, preventing accidental updates or deletions of any pre-existing data on the target instance. While the example uses `<IncludeAll />` for simplicity, initial deployments to [SaaS](documentation/developers-and-admins/saas) environments have specific requirements. For these deployments, you must explicitly list all desired object types using `<ObjectType>` elements within `<IncludedObjectTypes>` instead of using the `<IncludeAll />` shorthand.


XML
Copy
```
<?xml version="1.0" encoding="utf-8"?>
<RepositoryConfiguration
  Version="2"
  xmlns:xsd="http://www.w3.org/2001/XMLSchema"
  xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance">

  <!-- Run the restore in Create -->
  <RestoreMode>
    Create
  </RestoreMode>

  <!-- Include all object types -->
  <IncludedObjectTypes>
    <IncludeAll />
  </IncludedObjectTypes>

  <!-- Include all content items of all types -->
  <IncludedContentItemsOfType>
    <IncludeAll />
  </IncludedContentItemsOfType>

  <ObjectFilters>
  </ObjectFilters>

</RepositoryConfiguration>
```

This setup is specifically tailored for seeding an environment. For regular updates via CD after the initial deployment, you would typically change the `<RestoreMode>` (allowing updates/deletions) and use more specific filters instead of `<IncludeAll />`. See the following sections describing these common scenarios:
  * [Add a new type of content items](documentation/developers-and-admins/ci-cd/configure-ci-cd-repositories/repository-configuration-templates#add-a-new-type-of-content-items)
  * [Deploy specific content item data](documentation/developers-and-admins/ci-cd/configure-ci-cd-repositories/repository-configuration-templates#deploy-specific-content-item-data)
  * [Deploy object type changes](documentation/developers-and-admins/ci-cd/configure-ci-cd-repositories/repository-configuration-templates#deploy-object-type-changes)


### Add a new type of content items
When deploying a new content type (and its associated content items) to an existing environment using CD, we recommend the following approach. This configuration focuses on adding only the new type and its items without affecting existing data.
  * Use the `Create` [restore mode](documentation/developers-and-admins/ci-cd/configure-ci-cd-repositories#configure-cd-restore-mode) to ensure that the deployment only adds new entities (the content type definition, related objects, and the new content items) and does not modify or delete any existing data on the target instance.
  * Ensure the relevant object types (like the content type definition itself, **cms.contenttype**) are included in `<IncludedObjectTypes>`.
  * Add the code name of the new content type (e.g., **New.ContentType**) to the `<IncludedContentItemsOfType>` section. This tells the CD process to consider items of this specific new type during the restore operation.
  * Optionally, to also transfer changes to reusable field schemas, include the [relevant objects](documentation/developers-and-admins/ci-cd/reference-ci-cd-object-types#rfs_tracking).
  * Optionally, you can use [<ContentItemFilters>](documentation/developers-and-admins/ci-cd/configure-ci-cd-repositories#filter-individual-content-items) if you need to apply specific name-based filtering to the items of the new type being deployed.


XML
Copy
```
<?xml version="1.0" encoding="utf-8"?>
<RepositoryConfiguration
  Version="2"
  xmlns:xsd="http://www.w3.org/2001/XMLSchema"
  xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance">

  <!-- Run the restore in Create -->
  <RestoreMode>
    Create
  </RestoreMode>

  <IncludedObjectTypes>
    <!-- Include the content type definition -->
    <ObjectType>cms.contenttype</ObjectType>
    <!-- Holds reusable field schema definitions
    required to track schema changes. -->
    <ObjectType>cms.class</ObjectType>
  </IncludedObjectTypes>

  <IncludedContentItemsOfType>
    <!-- Add items of the new content type -->
    <ContentType>New.ContentType</ContentType>
  </IncludedContentItemsOfType>

  <ObjectFilters>
    <!-- Include reusable field schema data -->
    <IncludedCodeNames ObjectType="cms.class">
            CMS.ContentItemCommonData
    </IncludedCodeNames>
  </ObjectFilters>

</RepositoryConfiguration>
```

This configuration helps safely introduce new types of content items into your environment using CD.
### Deploy specific content item data
When your goal is to update existing content items of certain [types](documentation/developers-and-admins/development/content-types), we recommend using the following configuration:
  * Use the `CreateUpdate` [restore mode](documentation/developers-and-admins/ci-cd/configure-ci-cd-repositories#configure-cd-restore-mode). This allows the CD process to update existing content items found in the repository source and also create any new items of the specified types that might be included in the source. It avoids deleting items present on the target but missing from the source.
  * Explicitly list the code name of the content type whose items you intend to update within the `<IncludedContentItemsOfType>` section using `<ContentType>` elements.
  * Optionally, use [<ContentItemFilters>](documentation/developers-and-admins/ci-cd/configure-ci-cd-repositories#filter-individual-content-items) to further refine the deployment to only specific items within the selected types based on their code names or wildcard patterns. This is recommended for targeted updates.


XML
Copy
```
<?xml version="1.0" encoding="utf-8"?>
<RepositoryConfiguration
  Version="2"
  xmlns:xsd="http://www.w3.org/2001/XMLSchema"
  xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance">

  <!-- Run the restore in CreateUpdate -->
  <RestoreMode>
    CreateUpdate
  </RestoreMode>

  <!-- <IncludedObjectTypes> omitted from the sample -->

  <IncludedContentItemsOfType>
    <!-- Other listed types omitted from the sample -->
    <ContentType>Updated.ContentType</ContentType>
  </IncludedContentItemsOfType>

  <ContentItemFilters>
    <IncludedContentItemNames>
      <!-- Some more granular filtering applied -->
    </IncludedContentItemNames>
  </ContentItemFilters>

</RepositoryConfiguration>
```

This configuration allows you to push data updates for specific content items efficiently without affecting unrelated content or objects.
### Deploy object type changes
When deploying general changes (updates or additions) to specific object types (e.g., Users, Roles, Settings) using Continuous Deployment CD, use the following template as starter.
  * Use the `CreateUpdate` [restore mode](documentation/developers-and-admins/ci-cd/configure-ci-cd-repositories#configure-cd-restore-mode). This mode allows the CD process to create new objects and update existing objects of the specified types if they are present in the repository source. It will _not_ delete objects from the target environment that are missing from the source.
  * Explicitly list the code names of the specific object types you are deploying changes for within the [<IncludedObjectTypes>](documentation/developers-and-admins/ci-cd/configure-ci-cd-repositories#includeexclude-object-types) section (e.g., **cms.user**) using `<ObjectType>` elements. Avoid using `<IncludeAll />` for these targeted updates unless you intend to process all object types.
  * Optionally, use [<ObjectFilters>](documentation/developers-and-admins/ci-cd/configure-ci-cd-repositories#filter-individual-objects) to apply more granular filtering within the specified object types, if needed (e.g., deploying only specific users or roles).


XML
Copy
```
<?xml version="1.0" encoding="utf-8"?>
<RepositoryConfiguration
  Version="2"
  xmlns:xsd="http://www.w3.org/2001/XMLSchema"
  xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance">

  <RestoreMode>CreateUpdate</RestoreMode>

  <IncludedObjectTypes>
    <!-- Other listed object types omitted from the sample -->
    <ObjectType>cms.user</ObjectType>
  </IncludedObjectTypes>

  <!-- <IncludedContentItemsOfType> omitted from the sample -->

  <!-- Specify additional object filters as required -->
  <ObjectFilters>
  </ObjectFilters>

</RepositoryConfiguration>
```

This approach allows for controlled updates to specific parts of your application’s configuration or object data via the CD process.