---
source: https://docs.kentico.com/modules/private-cloud-deployment/update-xperience-version
scrape_date: 2026-01-26
---

Module: Private cloud deployment
4 of 7 Pages
# Update a deployment's Xperience version
At some point after deploying your application, you’ll probably want to apply a hotfix or refresh to your application. Let’s look into the update process.
Your approach can vary depending on what your environment allows, we will look into examples that are relevant to the deployment processes described in this guide.
### Upgrade directly in your published environment
In some cases, you can access your published environment directly and perform a large part of the upgrade there. See [the documentation’s update overview](/documentation/developers-and-admins/installation/update-xperience-by-kentico-projects#update-process-overview) for more details.
For this approach, you need to run dotnet CLI commands directly on your published environment to update the published database.
This can be done through the [Kudu console](https://github.com/projectkudu/kudu/wiki/Kudu-console) for Azure Web Apps, or by logging in or remotely accessing your web server if you are using standard IIS.
### Update a local copy of your published site
If you prefer not to run the update in your published environment, we recommend setting up a local version of the application and database to perform the update, before re-deploying.
#### Create backups
Make sure to take backups of your development and published databases before starting this process. If you are not using source control, make a backup of your application files as well.
#### Prepare the files
Start by creating a new branch or copy of your development project. Update the Xperience NuGet packages to the version you want to target.
Fix any code errors that appear as a result of the updated libraries.
Then, take your published application offline, or institute a content freeze.
#### Copy the database to your local machine
Move a copy of the live database to your local SQL server using the same [.bacpac](/modules/private-cloud-deployment/deploy-xperience-with-cli) or [.bak](/modules/private-cloud-deployment/deploy-xperience-with-cli) method you used to initially copy it to the published SQL server.
Update the connection string of the development instance to point to the new copy of the live database
#### Run the update
Disable Continuous Integration (CI) and Continuous Deployment (CD). Then run the `kxp-update` command of the .NET CLI to update database data. After the update completes successfully, you can re-enable CI and/or CD.
Test your project to make sure it is in working order.
Once everything is ready, change the connection string back to its previous state.
#### Re-deploy your application
Take your published application offline if it is not already.
Copy the newly updated database to the live server using the same method you started with. You can do one of the following:
  * Delete the original live database and import the updated version with the same name.
  * Import the updated database alongside the original, then switch their names.
  * Import the updated database alongside the original, and change the published application’s connection string to point to the new database.


Then clear and deploy your updated application files, and bring the published application back online.
At this point, you can access your updated published application normally.
[ Previous page ](/modules/private-cloud-deployment/deploy-code-and-data-changes)
4 of 7
[ Mark complete and continue ](/modules/private-cloud-deployment/deploy-without-the-administration)
![]()
[]()[]()
