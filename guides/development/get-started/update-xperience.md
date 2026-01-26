---
source: https://docs.kentico.com/guides/development/get-started/update-xperience
scrape_date: 2026-01-26
---

  * [Home](/guides)
  * [Development](/guides/development)
  * [Get started](/guides/development/get-started)
  * Update Xperience by Kentico 


# Update Xperience by Kentico
Keeping your Xperience by Kentico project up to date is quick and straightforward. Whether you’re applying a hotfix or adding new features delivered in the latest [refresh](/documentation/changelog), the process requires only a few steps.
Watch a short demo that walks you through updating an Xperience by Kentico project between minor versions (for example, 30.8.0 → 30.9.2). You will see the end-to-end flow in Visual Studio and the command line; plus, a straightforward way you can automate the update using a PowerShell script.
What the video shows:
  * Updating all Xperience by Kentico NuGet packages.
  * Running the database update CLI command `dotnet run --kxp-update`.
  * Verifying the version in the Xperience administration.
  * (Optional) Automating update process with a lightweight PowerShell script that updates the packages and database.


For detailed, step-by-step instructions, see [Update Xperience by Kentico projects](/documentation/developers-and-admins/installation/update-xperience-by-kentico-projects).
Before you run the update of your Xperience by Kentico project, you need to disable CI in the **Settings** application. Remember to re-enable CI when you complete the project update.
Your browser does not support the video tag. 
You can find a sample of the PowerShell script in the [Training guides repository](https://github.com/Kentico/xperience-by-kentico-training-guides/blob/finished/scripts/Update-XperienceProjectWithDatabase.ps1). (Please note that disabling and re-enabling CI is a part of this script.)
If you want to find out more about other options for updating your Xperience project, check out the [Kentico Xperience Manager](https://github.com/Kentico/xperience-by-kentico-manager?tab=readme-ov-file) project. (Please note this project runs under the [Kentico labs mode](https://github.com/Kentico/.github/blob/main/SUPPORT.md#labs-limited-support) which means Kentico provides only limited support.)
![]()
[]()[]()
