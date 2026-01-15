# CI/CD
  * [ Copy page link ](documentation/developers-and-admins/ci-cd#) | [Get HelpService ID](documentation/developers-and-admins/ci-cd#)
Core MVC 5


[✖](documentation/developers-and-admins/ci-cd# "Close page link panel") [Copy to clipboard](documentation/developers-and-admins/ci-cd#)
**Continuous Integration** and **Continuous Deployment** (CI/CD) enable Xperience projects to work in an automated software development and deployment flow.
Both features serialize the data of objects from the database into XML files within a **repository** on the file system. These repositories can then be restored into an Xperience database in a desired environment.
### [Continuous Integration](documentation/developers-and-admins/ci-cd/continuous-integration)
With Continuous Integration enabled, the system tracks local changes made to database objects, and automatically transfers them to the developer’s repository. Developers track the CI repository in a [source control system](https://en.wikipedia.org/wiki/Revision_control) (for example, [Git](https://git-scm.com/) or [Subversion](https://subversion.apache.org/)) together with other code and project files. The repository is then used to synchronize database data between team members.
### [Continuous Deployment](documentation/developers-and-admins/ci-cd/continuous-deployment)
Developers prepare new projects or updates for existing ones within a dedicated development environment. Once the project is ready to be published, data from the development database is stored into a CD repository, which is then deployed to another Xperience instance in a staging, UAT or production environment.