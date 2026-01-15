# Configure CI/CD repositories
  * [ Copy page link ](documentation/developers-and-admins/ci-cd/configure-ci-cd-repositories#) | [Get HelpService ID](documentation/developers-and-admins/ci-cd/configure-ci-cd-repositories#)
Core MVC 5


[✖](documentation/developers-and-admins/ci-cd/configure-ci-cd-repositories# "Close page link panel") [Copy to clipboard](documentation/developers-and-admins/ci-cd/configure-ci-cd-repositories#)
[Continuous Integration](documentation/developers-and-admins/ci-cd/continuous-integration) and [Continuous Deployment](documentation/developers-and-admins/ci-cd/continuous-deployment) repositories can be configured to only store specific types of objects. This is useful if you do not wish to synchronize or deploy all supported types, particularly in larger projects. For all included object types, CI/CD also allows object-level filtering using code names.
All configuration is defined within [repository configuration files](documentation/developers-and-admins/ci-cd/continuous-deployment#generate-cd-configuration-files) located in the root of the CI/CD repository. Use the following process if you need to make changes:
  1. Edit the repository configuration file.
  2. Adjust the configuration according to your object filtering requirements. See the following sections for details:
     * [Include/Exclude object types](documentation/developers-and-admins/ci-cd/configure-ci-cd-repositories#includeexclude-object-types)
     * [Filter individual objects](documentation/developers-and-admins/ci-cd/configure-ci-cd-repositories#filter-individual-objects)
     * [Configure CD restore mode](documentation/developers-and-admins/ci-cd/configure-ci-cd-repositories#configure-cd-restore-mode)
  3. Save the file.
  4. Run the store command ([CI](documentation/developers-and-admins/ci-cd/continuous-integration#store-object-data-to-the-repository)/[CD](documentation/developers-and-admins/ci-cd/continuous-deployment#store-objects-to-a-cd-repository)) to bring your repository into a consistent state.


Excluding objects in the configuration file affects both store and restore actions for CI/CD.
**Continuous Integration environments**
Always use the same CI repository configuration settings across your entire development environment to maintain consistency. Whenever you make any changes, use your source control system to synchronize the configuration file and the other repository content. When a developer loads a new version of the configuration file from the source control, the new settings apply after restoring objects into the database or manually restarting the application.