---
source: https://docs.kentico.com/modules/xperience-implementation-handbook/deploy-to-a-new-environment
scrape_date: 2026-01-22
---

Module: Xperience implementation handbook
7 of 9 Pages
# Deploy to a new environment
## Deploying
Deployment is one of the most crucial steps in a project’s lifecycle. You will deploy continuously throughout the life of a project, as the need for maintenance and new features arises. Most people associate deployment with the production environment, but it also includes local, integration, staging, and testing environments. This section provides an overview of the process of deploying your solution from one environment to another, describing the basic approaches, available tools, and steps to deploy.
### Deployment approaches
The first part of deployment to consider is the deployment method. The best approach for your project depends on a number of factors, such as the nature of the changes, project resources, team size, project type, and the details of the source and target environments.
There are two basic approaches: **manual** and **automatic** deployment. In some scenarios, you might **combine** these approaches, manually deploying a subset of the changes and automatically deploying the rest.
  * Manual deployment involves copying files to the target environment, for example, via FTP or over a network. For manual deployment of database changes, you may recreate the changes on the target environment, execute SQL scripts, or copy serialized data files and run commands to propagate them to the database via [Continuous Deployment (CD)](/documentation/developers-and-admins/ci-cd/continuous-deployment) or [Continuous Integration (CI)](/documentation/developers-and-admins/ci-cd/continuous-integration).
  * Automated deployment of code typically involves special services or servers for builds and deployment. For example, you might set up a pipeline that automatically builds and deploys to a preview environment when a certain branch of your source control is updated. You can handle automatic database changes with built-in Xperience features. For example, include **CI** or **CD** commands in your automated pipelines, or, if the source and target servers connect to the same database, allow **multi-instance support** to synchronize the Xperience instances automatically.


Regardless of the deployment mechanism, you need to determine **who** will be responsible for deploying changes. You may find it best to have different people or groups be responsible for certain types of changes. Responsibility may vary depending on the target environment.
Sometimes, you might automate the process, so a server or service handles deployment. If this is the case, assign someone to monitor the automated process and make sure it continues to function.
Finally, make sure the responsible party knows **when** to deploy. For example, should changes be scheduled for a pre-determined maintenance window or pushed immediately? Ideally, you should determine this in advance, establishing rules that prevent conflicts and ensure you know who to talk to if something goes wrong.
### Deployment tools
Xperience by Kentico is a standard .NET project, meaning its code and files support any compatible deployment process or pipeline.
If you use the [Xperience SaaS environment](/documentation/developers-and-admins/saas/saas-overview), you can upload your deployment through the [Xperience Portal](/documentation/developers-and-admins/saas/xperience-portal) UI, or by posting to the deployment API endpoint through an automated process.
For deploying data to Xperience, we recommend using the [Continuous Deployment](/documentation/developers-and-admins/ci-cd/continuous-deployment) feature. Continuous Deployment serializes Xperience data in files which can be transferred by source control or a code deployment process. Then, dotnet CLI commands allow you to restore the serialized data into the target database, in a way that can be integrated into most automated deployment processes and pipelines.
Some common third-party tools for deploying different parts of projects include TeamCity, GitHub Actions, Azure DevOps Pipelines, FTP, WebDeploy, Visual Studio, and Redgate SQL Data Compare.
If you use any third-party tools that modify database data directly, you may need to [clear the Xperience cache](/documentation/developers-and-admins/development/caching#clear-the-cache) before the changes take effect.
**Read-only mode**
[Read-only deployments](/documentation/developers-and-admins/deployment/read-only-deployments) allow zero-downtime updates, serving content from a snapshot of the database while you deploy the new code.
You just need to [make sure your code supports read-only mode](/documentation/developers-and-admins/deployment/read-only-deployments#required-project-changes).
Zero-downtime deployments are [seamlessly integrated into SaaS](/documentation/developers-and-admins/deployment/deploy-to-the-saas-environment#zero-downtime-deployments).
### Deployment process
Before you start deploying to any environment, spend some time preparing to eliminate possible issues. The following list of tasks should help you understand the types of things to check:
  * Disable unused features to avoid consuming unnecessary resources.
  * Check your environment-specific configuration. 
    * Validate CDN settings, disable unnecessary logging, etc.
  * Determine objects to synchronize. 
    * Filter or remove unwanted objects from deployment (e.g., exclude files stored in a “test” folder or add config files for CI and CD repositories).
    * Validate that you have included all dependencies.
  * Prepare your deployment package(s). 
    * If you are deploying to the [SaaS environment](/documentation/developers-and-admins/saas/saas-overview), you can find specific instructions for creating your deployment package [in the documentation](/documentation/developers-and-admins/deployment/deploy-to-the-saas-environment#create-a-deployment-package).
    * If you are deploying to _Azure Web Apps_ or _IIS_ in a [private cloud](/documentation/developers-and-admins/deployment/deploy-to-private-cloud) environment, you can find examples in the [Deploy to your private cloud](/guides/development/deployment/deploy-to-private-cloud) guide.
  * Test the deployment before synchronizing. 
    * Be aware that the built-in deployment features do not support a bulk roll-back in case of a failed deployment.
  * Back up the target environment so you can recover if something goes wrong. 
    * If you are using the Xperience SaaS environment, it creates an [automatic restore point for each service](/documentation/developers-and-admins/deployment/deploy-to-the-saas-environment/manage-saas-deployments#automatic-restore-points-and-exports) before deployment, but you can also create a [manual restore point](/documentation/developers-and-admins/deployment/deploy-to-the-saas-environment/manage-saas-deployments#create-a-manual-restore-point) or [manual export](/documentation/developers-and-admins/deployment/deploy-to-the-saas-environment/manage-saas-deployments#create-a-manual-export) when you need to.
  * Plan the order in which things are synchronized (e.g., code, then database objects or vice-versa). 
    * In most cases, agencies deploy code before data.
  * Prepare a maintenance message when deploying with downtime (e.g., an _App_Offline.html_ file).


During the deployment, monitor the environment (e.g., CPU, RAM, and disk utilization), the application (e.g., event log errors), and take appropriate action when something goes amiss. After the deployment, verify and test that the deployment was successful.
### Scenarios 
#### Going live with a redesigned website
A client was going live with a new redesign of their old website, which previously ran on a different platform.
Since the website with all of its content and functionality was completed and tested in the staging environment, the easiest way to deploy this new website to production was to manually back up and restore the site files and database.
Both the solution codebase and database were fully restored in the production environment without any built-in or external deployment tools. Environment-specific settings and configuration were adjusted to match the production environment requirements. The system administrator prepared a backup plan to easily switch traffic between the old website and the new website running in the same environment in case there were any issues.
The client decided to forward the traffic to the new website early on Saturday morning to minimize the impact on users and ensured that the whole implementation team was ready to address any issues should they arise.
After going live, the system administrator received an email notification about a few errors in the _Event log_. The exceptions turned out to be related to a few configuration changes that were initially overlooked, and they were quickly fixed.
#### Rolling out a new feature
A client was planning to deploy a new feature that involved changing some of the existing code as well as the data in the database.
This new functionality was highly dependent on both the code and the data. It was crucial to have both in place at the same time, and therefore, a maintenance window with downtime was required.
The deployment process was tested with a sandbox copy of the production environment to ensure that the changes would not break any other functionality. They scheduled the production deployment early on a Sunday morning (based on Google Analytics data, this was their lowest traffic time). During this time, all live site visitor requests were redirected to a page with maintenance message explaining that the website was being updated.
Since the production environment contained visitor-generated data (e.g., form submissions, online marketing data, etc.), the customer could not simply restore a copy of the sandbox. They used TeamCity to deploy the code to production and after that, used built-in features to synchronize the necessary database data.
Once they verified everything was working correctly in the production environment, they redirected live traffic back to the site again.
#### Applying hotfix with no downtime
A client had a strict requirement to keep the production website running even during maintenance windows.
They needed to apply the latest hotfix, so they took a backup of the production website (both the database and codebase) and applied the hotfix in a local development environment. After testing the website, they restored it to another server in the production environment.
Once the hotfixed website was running, they redirected the traffic to it instead of the old server through a load balancer. During the maintenance window, they disabled visitor submission of forms with a customization they built. The customization put forms in a read-only mode and displayed a maintenance message, so that the client would not need to migrate form submission data to the hotfixed website.
[ Previous page ](/modules/xperience-implementation-handbook/test-your-solution)
7 of 9
[ Mark complete and continue ](/modules/xperience-implementation-handbook/maintain-what-you-have-built)
![]()
[]()[]()
