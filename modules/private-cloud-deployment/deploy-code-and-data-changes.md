---
source: https://docs.kentico.com/modules/private-cloud-deployment/deploy-code-and-data-changes
scrape_date: 2026-01-22
---

Module: Private cloud deployment
3 of 7 Pages
# Deploy code and data changes
You will have to take a different approach for deploying new code to an existing published project depending on whether or not you [configured external storage](/modules/private-cloud-deployment/deploy-xperience-with-cli).
If you’ve configured external storage according to our recommendations, you should be able to clear the existing deployment and re-deploy with the same steps as the initial deployment- first [publish](/modules/private-cloud-deployment/deploy-xperience-with-cli) your application and then deploy the published files according to your platform.
If you host your app in an _Azure Web App_ , you can use the `--clean` parameter of the `az webapp deploy` command to clear out the deployment slot before you do anything.
If you use IIS, we recommend creating a reusable script that clears out the deployment location and copies the publishable files.
**If you do not use external storage** , you’ll need to copy _asset files_ , _media files_ , and _form submission files_ to a safe location before clearing the existing deployment, then copy them back after deployment.
Otherwise, **they may be lost permanently**.
We recommend creating a script to automate this process.
## Deploy new data to an existing published application
### Standard Continuous Deployment
In some cases, you may want to create some data in your development environment and deploy it to the published environment. For example, you might create a custom module, which contains classes and class data alongside code.
We recommend using **Continuous deployment (CD)** to keep the data in sync between your environments. You can follow the guidance in the [Continuous Deployment documentation](/documentation/developers-and-admins/ci-cd/continuous-deployment).
### Asymmetric Continuous Deployment workaround
If you want to have different data between your development environment and your published environment, standard CD procedures may cause issues, overwriting objects in the production database with data that you only want to exist locally.
In this case, you can use the following workaround:
#### Set up your environments
Make sure CD is enabled locally, along with some kind of source control. Also follow the advice in the [CD documentation](/documentation/developers-and-admins/ci-cd/continuous-deployment#synchronize-macro-signatures) to keep macro signatures in sync with your production environment.
#### Make your data changes
Add the data to your development instance, allowing Continuous Deployment to serialize the data in your source control repository.
#### Copy your dev environment
Make a new copy of your _Development application_ that exists outside of your source control system. We’ll refer to it as the **Copy instance** in this section. Make sure the _Copy instance_ exists outside of your source control. It should be a distinct project, not a branch.
Disable your _Published app_ , and change the connection string of the _Copy instance_ to point to the _Published database_. Then run a [CD store](/documentation/developers-and-admins/ci-cd/continuous-deployment#store-objects-to-a-cd-repository) from the _Copy instance_. This serializes the data from the _Published database_ into the _Copy instance_ ’s CD repository folder.
#### Transfer the data
Using your source control history, isolate the new serialized files that were created or modified as a result of your data changes in your _Development app_ , including any dependent objects that may have been affected.
Copy these files into the corresponding folders of the _Copy instance_ ’s CD repository. Then, run a [CD restore](/documentation/developers-and-admins/ci-cd/continuous-deployment#restore-the-cd-repository-to-the-database) to propagate the changes to the _Published database_.
Then you can delete the _Copy instance_ and [deploy any new code changes](/modules/private-cloud-deployment/deploy-code-and-data-changes) to the _Published app_ before reactivating it.
[ Previous page ](/modules/private-cloud-deployment/deploy-xperience-with-cli)
3 of 7
[ Mark complete and continue ](/modules/private-cloud-deployment/update-xperience-version)
![]()
[]()[]()
