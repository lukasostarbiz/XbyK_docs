---
source: https://docs.kentico.com/guides/development/get-started/automate-regular-tasks-with-powershell-scripts
scrape_date: 2026-01-26
---

  * [Home](/guides)
  * [Development](/guides/development)
  * [Get started](/guides/development/get-started)
  * Automate regular tasks with PowerShell scripts 


# Automate regular tasks with PowerShell scripts
Many development teams use PowerShell scripts to speed up common tasks. Even in cases where the script automates one or two commands, a developer can simply run a file from a folder without the need to look up or memorize several specific commands.
This guide will show you how to create PowerShell scripts to automate common recurring tasks in Kentico Xperience environments.
**Repository and folder structure**
The examples in this guide use the structure of the [Training guides repository](https://github.com/Kentico/xperience-by-kentico-training-guides/tree/finished) when traversing directories.
Each script will assume it is located in a folder called  _scripts_ in the root of the repository, and will deal with projects located in the  _src_ folder.
The code samples in this guide were developed using Windows PowerShell 5.1. Note that some other versions of PowerShell may not support the same commands, and you may need to make minor adjustments for your environment.
## Generate code files
Code generation is a common development function that requires a command. Xperience code generation through the .NET CLI automatically generates strongly typed C# classes and interfaces based on content types, module classes, reusable field schemas and forms that exist in the Xperience database.
The code generation command has [many parameters](/documentation/developers-and-admins/api/generate-code-files-for-system-objects#generate-code-files) that you can use to control which objects to include and where to save their files.
This guide’s example will demonstrate all object types and save them to the _TrainingGuides.Entities_ project.
  1. Save the current location to a variable before switching to the _TrainingGuides.Web_ directory.
Utilize the PowerShell [$PSScriptRoot](https://learn.microsoft.com/en-us/powershell/module/microsoft.powershell.core/about/about_automatic_variables?view=powershell-7.4#psscriptroot) variable to ensure the correct file paths even if you call the script from other than the _scripts_ folder. 
  2. Call the `run` command in the .NET CLI with the `--kxp-codegen` option and specify the `--type` parameter for each type supported by the tool.
  3. Return back to the initial directory, in case the script is called through a command window instead of with a click.
This way developers executing the script through the command line can continue their work without having to change directories. 
  4. Track errors and use that to determine whether the script returns a nonzero exit code. You can extract this into a function and add some human-friendly messages.


PS
**GenerateCodeFiles.ps1**
Copy
```
<#
.Synopsis
    Generates code for classes, forms, and content types stored in the database.
#>
$exitCode = 0

$originalLocation = Get-Location
Set-Location -Path $PSScriptRoot/../src/TrainingGuides.Web

# https://docs.xperience.io/xp/developers-and-admins/development/content-retrieval/generate-code-files-for-xperience-objects

$contentTypesNamespace = "TrainingGuides"

function Write-Result-Get-Exit-Code {
    param(
        [string] $type
    )
    if ($LASTEXITCODE -ne 0) {
        Write-Error "$type code generation failed."
        return 1;
    }
    else{
        Write-Host "$type code generation succeeded." -ForegroundColor Green
        return 0;
    }
    Write-Host
}

#Reusable content types
dotnet run --no-build -- --kxp-codegen --type "ReusableContentTypes" --namespace $contentTypesNamespace --location "../TrainingGuides.Entities/{type}/{name}"
$exitCode = Write-Result-Get-Exit-Code "Reusable content type"

#Page content types
dotnet run --no-build -- --kxp-codegen --type "PageContentTypes" --namespace $contentTypesNamespace --location "../TrainingGuides.Entities/{type}/{name}" --skip-confirmation
$exitCode = Write-Result-Get-Exit-Code "Page content type"

#Reusable field schemas
dotnet run --no-build -- --kxp-codegen --type "ReusableFieldSchemas" --namespace $contentTypesNamespace --location "../TrainingGuides.Entities/{type}/{name}" --skip-confirmation
$exitCode = Write-Result-Get-Exit-Code "Reusable field schema"

#Custom module classes
dotnet run --no-build -- --kxp-codegen --type "Classes" --with-provider-class False --location "../TrainingGuides.Entities/{type}/{name}" --skip-confirmation
$exitCode = Write-Result-Get-Exit-Code "Class"

#Forms
dotnet run --no-build -- --kxp-codegen --type "Forms" --location "../TrainingGuides.Entities/{type}/{name}" --skip-confirmation
$exitCode = Write-Result-Get-Exit-Code "Form"

if ($exitCode -ne 0) {
    Set-Location -Path $originalLocation
    Write-Error "Completed with errors. See above."
    Read-Host -Prompt "Press Enter to exit"
    exit $exitCode
}

Set-Location -Path $originalLocation
Read-Host -Prompt "Press Enter to exit"
```

Visit our [Code generator documentation](/documentation/developers-and-admins/api/generate-code-files-for-system-objects) to learn more about its parameters and usage. Additionally, you can see a more detailed exploration and examples [in this video](/guides/development/get-started/generate-code-for-custom-content-and-data-classes). 
## Publish
The .NET CLI allows for projects to be built and published, meaning this process can be automated. Using a Powershell script, you can carry out additional automated steps before and after, and ensure your team doesn’t need to worry about Visual Studio _publish_ _profiles_.
This example is relatively straightforward, but you can expand it with additional deployment tasks for your specific scenario.
  1. Take a `switch` parameter called `$KeepProductVersion` to represent whether or not a custom build number should be used as the version suffix.
  2. Save the current location and set up variables to store the path to the output folder and the build number.
  3. Switch to the _TrainingGuides.Web_ directory.
  4. Assemble a new string containing the `dotnet publish` command based on the value of the `$KeepProductVersion` parameter, and execute it.
  5. Log any errors before returning to the original directory, in case the script is called through a command window instead of by clicking.


PS
**Publish.ps1**
Copy
```
<#
.Synopsis
    Creates a deployment package.
#>
    [CmdletBinding()]
param ([switch]$KeepProductVersion)

$originalLocation = Get-Location
Set-Location -Path $PSScriptRoot

$outputFolderPath = "./bin/Deployment/"
$buildNumber = (Get-Date).ToUniversalTime().ToString("yyyyMMddHHmm")

Set-Location -Path ../src/TrainingGuides.Web

# Publish the application in the 'Release' mode
$publishCommand = "dotnet publish --nologo -c Release --self-contained true --runtime win-x64 -o $OutputFolderPath"

if (!$KeepProductVersion) {
    $publishCommand += " --version-suffix $buildNumber"
}

Write-Host $publishCommand

Invoke-Expression $publishCommand

if ($LASTEXITCODE -ne 0) {
    Set-Location -Path $originalLocation
    Write-Error "Publishing the website failed."
    Read-Host -Prompt "Press Enter to exit"
    exit 1
}

Set-Location -Path $originalLocation
Read-Host -Prompt "Press Enter to exit"
```

## Continuous integration
_[Continuous integration](/documentation/developers-and-admins/ci-cd/continuous-integration) (CI)_ is a feature of Xperience that allows you to easily share database changes with other developers on your team.
Using the `--kxp-ci-store` option you can serialize your data changes into XML format in your file system (see [CI store](#ci-store)). These can be shared over a source control with your team members who can then restore the changes to update their database, using `--kxp-ci-restore` (see [CI restore](#ci-restore)).
The following sections will share tips and best practices on how to work with these commands to help your team be the most effective. 
### CI store
The following script serializes database data and automatically stores it in the _App_Data/CIRepository_ directory of your current project - in this case, the _TrainingGuides.Web_.
  1. Save the current location to a variable, and switch to the _TrainingGuides.Web_ directory.
  2. Call the `run` command in the .NET CLI with the `--kxp-ci-store` option. 
     * Optionally, use the `--no-build` parameter to save time, if your team knows to run the command only when the site has been compiled with any necessary updates.
  3. Log an error if there are any issues.
  4. Return to the original directory, in case it is being run from a PowerShell window instead of with a click.


PS
**CIStore.ps1**
Copy
```
<#
.Synopsis
    Serializes database data to the continuous integration repository.
#>

$originalLocation = Get-Location
Set-Location -Path $PSScriptRoot/../src/TrainingGuides.Web

Write-Host 'Storing CI files'

dotnet run --no-build --kxp-ci-store

if ($LASTEXITCODE -ne 0) {
    Set-Location -Path $originalLocation
    Write-Error "CI store failed."
    Read-Host -Prompt "Press Enter to exit"
    exit 1
}
else{
    Write-Host 'CI files stored'
}

Set-Location -Path $originalLocation

Read-Host -Prompt "Press Enter to exit"
```

### Connection string function
Restoring data from the _CIRepository_ requires retrieving a connection string to access the database. Because there are two more scripts in this guide that will need this utility, let’s create a reusable _Get-ConnectionString_ script before diving into [CI restore](#ci-restore).
  1. Create a new file called _Get-ConnectionString.ps1_ in the _scripts_ directory of the repository.
  2. Define a function with the same name as the file, taking two `string` parameters: `$Path` and `$OriginalLocation`.
  3. Check if there is a _CMSConnectionString_ saved in the user secrets, and return it if found.
Leave this part out if your team does not use user secrets.  

  4. Fall back the _appSettings.json_ file for the _CMSConnectionString_ if one is not found in the user secrets.


PS
**Get-ConnectionString.ps1**
Copy
```
<#
.Synopsis
    Contains functions for use in other scripts
#>

<#
.DESCRIPTION
   Gets the database connection string from the config file
#>
function Get-ConnectionString {
    param(
        [string] $Path,
        [string] $OriginalLocation
    )

    # Try to get the connection string from user secrets first
    $connectionString = dotnet user-secrets list --project $Path `
        | Select-String -Pattern "ConnectionStrings:" `
        | ForEach-Object { $_.Line -replace '^ConnectionStrings:CMSConnectionString \= ','' }

    if (-not [string]::IsNullOrEmpty($connectionString)) {
        Write-Host 'Using ConnectionString from user-secrets'

        return $connectionString
    }

    Write-Host 'Unable to find connection string in user secrets.'

    $appSettingsFileNames = 'appSettings.json'

    foreach ($appSettingFileName in $appSettingsFileNames)
    {
        $jsonFilePath = Join-Path $Path $appSettingFileName

        if (Test-Path $jsonFilePath)
        {
            $appSettingsJson = Get-Content $jsonFilePath | Out-String | ConvertFrom-Json
            $connectionString = $appSettingsJson.ConnectionStrings.CMSConnectionString;

            if ($connectionString)
            {
                Write-Host "Using ConnectionString from $appSettingFileName"

                return $connectionString;
            }
        }
    }

    Set-Location $OriginalLocation
    Write-Error "Connection string not found."
    Read-Host -Prompt "Press Enter to exit"
    exit 1
}
```

### CI restore
The script below covers restoring data from the _App_Data/CIRepository_ folder of your project and updating the Xperience database to match the version specified by the NuGet packages in your application.
While it may take just a single command to restore objects from the _CIRepository_ to the database, things get more complicated when changes are made to the database structure.
Continuous integration handles some database schema changes on its own, creating and deleting tables for custom content types, forms, and module classes. However, further customizations, such as custom indexes, are not accounted for.
[Our documentation](/documentation/developers-and-admins/ci-cd/ci-cd-database-migration-scripts) shows how to run migration scripts before and after a CI restore. Since certain changes to the database schema may interfere with the restore process, this process automatically executes certain SQL commands before and after the CI restore operation.
This guide’s example closely follows the PowerShell script provided by the documentation. Start by copying the script and changing it in three key ways.
  * Assume that the script is stored in the _scripts_ folder of the repository, and remove the path as a parameter.
  * Use the `Get-ConnectionString` function from the [previous section](#connection-string-function), rather than including the function innately.
  * Add a `Handle-Error` function that sets location back to the original directory, logs an error and returns `exit 1`. Use it throughout the script.


PS
**CIRestore.ps1**
Copy
```
<#
.Synopsis
    Restores objects serialized in the CI repository into the database.
#>
param (
    # Displays time elapsed for the restore operation including migrations
    [switch] $DisplayTimeElapsed
)

$originalLocation = Get-Location
Set-Location -Path $PSScriptRoot

. .\Get-ConnectionString.ps1

$beforeList = "Before.txt"
$afterList = "After.txt"
$repositoryPath = "App_data\CIRepository"
$migrationFolder = "@migrations"

Set-Location -Path ../src/TrainingGuides.Web

$path = Get-Location

<#
.DESCRIPTION
   Handles errors by displaying a message and exiting the script.
#>
function Handle-Error {
    param(
        [string] $Message
    )
    Set-Location -Path $originalLocation
    Write-Error $Message
    Read-Host -Prompt "Press Enter to exit"
    exit 1
}

<#
.DESCRIPTION
   Runs a database migration with the given name
#>
function Run-Migration {
    param(
        [System.Data.SqlClient.SqlConnection] $Connection,
        [System.Data.SqlClient.SqlTransaction] $Transaction,
        [string] $MigrationName
    )

    $migrationPath = "$path\$repositoryPath\$migrationFolder\$MigrationName.sql"
    if (!(Test-Path $migrationPath)) {
        Write-Error "The file $migrationPath does not exist."
        return $FALSE
    }

    $sourceScript = Get-Content $migrationPath

    $sqlCommand = ""
    $sqlList = @()

    foreach ($line in $sourceScript) {
        if ($line -imatch "^\s*GO\s*$") {
            $sqlList += $sqlCommand
            $sqlCommand = ""
        }
        else {
            $sqlCommand += $line + "`r`n"
        }
    }

    $sqlList += $sqlCommand

    $rowsAffected = 0
    foreach ($sql in $sqlList) {
        if ([bool]$sql.Trim()) {
            $command = New-Object System.Data.SqlClient.SqlCommand($sql, $Connection)
            $command.Transaction = $Transaction

            try {
                $rowsAffectedInBatch = $command.ExecuteNonQuery()

                if ($rowsAffectedInBatch -gt 0) {
                    $rowsAffected += $rowsAffectedInBatch
                }
            }
            catch {
                Write-Error $_.Exception.Message
                return $FALSE
            }
        }
    }

    Log-RowsAffected -Connection $Connection -Transaction $Transaction -MigrationName $MigrationName -RowsAffected $rowsAffected

    return $TRUE
}


<#
.DESCRIPTION
   Logs rows affected by the migration.
#>
function Log-RowsAffected {
    param(
        [System.Data.SqlClient.SqlConnection] $Connection,
        [System.Data.SqlClient.SqlTransaction] $Transaction,
        [string] $MigrationName,
        [int] $RowsAffected
    )

    $logRowsAffectedQuery = "UPDATE CI_Migration SET RowsAffected = $RowsAffected WHERE MigrationName = '$MigrationName'"
    $logRowsAffectedCommand = New-Object System.Data.SqlClient.SqlCommand($logRowsAffectedQuery, $Connection)
    $logRowsAffectedCommand.Transaction = $Transaction

    try {
        $logRowsAffectedCommand.ExecuteNonQuery()
    }
    catch {
        Write-Host "Can't log rows affected: $_.Exception.Message"
    }
}

<#
.DESCRIPTION
   Checks if a migration with the given name was already applied. If not, the method returns false and the migration is marked as applied.
#>
function Check-Migration {
    param(
        [System.data.SqlClient.SQLConnection] $Connection,
        [System.Data.SqlClient.SqlTransaction] $Transaction,
        [string] $MigrationName
    )

    $sql = "DECLARE @migrate INT
            EXEC @migrate = Proc_CI_CheckMigration '$MigrationName'
            SELECT @migrate"

    $command = New-Object system.data.sqlclient.sqlcommand($sql, $Connection)
    $command.Transaction = $Transaction

    return $command.ExecuteScalar()
}


<#
.DESCRIPTION
   Runs all migrations in the migration list
#>
function Run-MigrationList {
    param(
        [string] $ConnectionString,
        [string] $MigrationList
    )

    $migrations = Get-Content "$path\$repositoryPath\$MigrationList"

    $connection = New-Object system.data.SqlClient.SQLConnection($ConnectionString)
    $connection.Open()
    foreach ($migrationName in $migrations) {
        $transaction = $connection.BeginTransaction("MigrationTransaction")

        if (Check-Migration -Connection $connection -Transaction $transaction -MigrationName $migrationName) {
            Write-Host "Applying migration '$migrationName'."
            if (!(Run-Migration -Connection $Connection -Transaction $transaction -MigrationName $migrationName)) {
                $transaction.Rollback()
                $connection.Close()
                return $FALSE
            }
        }

        $transaction.Commit()
    }

    $connection.Close()

    return $TRUE
}


<#
.DESCRIPTION
   Restores the repository to the database and executes migrations before and after the restore.
#>
function Run-Restore {
    param(
        [string] $Path
    )

    $connectionString = Get-ConnectionString -Path $Path -OriginalLocation $originalLocation

    # Creates an 'App_Offline.htm' file to stop the website
    "<html><head></head><body>Continuous Integration restore in progress...</body></html>" > "$Path\App_Offline.htm"

    # Executes migration scripts before the restore
    if (!(Run-MigrationList $connectionString $beforeList)) {
        Handle-Error "Database migrations before the restore failed."
    }

    $configuration = "Release";
    if (Test-Path (Join-Path $Path "bin\Debug"))
    {
        $configuration = "Debug";
    }

    # Runs the restore CLI command
    dotnet run --project $Path --no-build -c "$configuration" -- --kxp-ci-restore
    if ($LASTEXITCODE -ne 0) {
        Handle-Error "Restore failed."
    }

    # Executes migration scripts after the restore
    if (!(Run-MigrationList $connectionString $afterList)) {
        Handle-Error "Database migrations after the restore failed."
    }

    # Removes the 'App_Offline.htm' file to bring the site back online
    Remove-Item "$Path\App_Offline.htm"

    Write-Host "Done"
}

$sw = [System.Diagnostics.Stopwatch]::StartNew()

Run-Restore -Path $path

$sw.Stop()
if ($DisplayTimeElapsed) {
    Write-Host "Time Elapsed: $($sw.Elapsed)"
}

if ($LASTEXITCODE -ne 0) {
    Handle-Error "Completed with errors. See above."
}

Set-Location -Path $originalLocation

Read-Host -Prompt "Press Enter to exit"
```

While most of the script is copied from the documentation, it is still worth understanding its primary components, and the structures it relies on.
Essentially, the script needs the _App_Data/CIRepository_ folder to contain files called _Before.txt_ and _After.txt_. These text files can hold lists of the names of _.sql_ files in the _@migrations_ subfolder (not including the extension). The lists in the text files determine which of these SQL files, called _migrations,_ are executed, and in what order.
Information about the migrations is logged into the `CI_Migration` table of the database, which you can check to ensure that the same migration does not run multiple times.
  * `Run-Migration`
    * Finds the SQL file that corresponds to the provided migration name and executes the commands within it, using the provided connection and transaction.
    * Returns `$FALSE` if it throws an exception, `$TRUE` otherwise.
    * Is called by `Run-MigrationList.`
  * `Log-RowsAffected`
    * Logs how many rows were affected by a migration to the row corresponding to it in the `CI_Migration` table of the database.
    * Is called by `Run-Migration.`
  * `Check-Migration`
    * Checks the `CI_Migration` table to see if a migration with the given name was already applied.
    * Returns `$TRUE` if the migration already exists, `$FALSE` otherwise.
    * Is called by `Run-MigrationList.`
  * `Run-MigrationList`
    * Establishes a database connection, then creates and executes a new transaction for each migration name in the provided list.
    * Commits the transactions after they are executed.
    * Rolls back transaction and returns `$FALSE` if an exception is encountered, returns `$TRUE` otherwise.
    * Is called by `Run-Restore.`
  * `Run-Restore`
    * Takes the application offline and runs the _Before_ migration list.
    * Runs a CI restore.
Just like the [CI store script above](#ci-store), CI restore also uses the optional `--no-build` parameter. Consider your team’s procedures when deciding whether to include it. 
    * Runs the _After_ migration list and brings the application back online.
    * Writes any errors it encounters along the way.


To provide this script with the conditions it needs, create new text files named _Before.txt_ and _After.txt_ in the _App_Data/CIRepository_ folder of the _TrainingGuides.Web_ project, along with an empty folder named _@migrations_.
You can find an example of the type of migration that can be run here on [this documentation page](/documentation/developers-and-admins/ci-cd/ci-cd-database-migration-scripts#example---database-change-migration-script). 
## Update
The last script updates the Xperience by Kentico instance to the version specified by the application’s NuGet packages.
To prevent continuous integration operations from interfering and causing errors, the script needs to disable CI before running the update. After the update is finished it will re-enable CI.
Since continuous integration is enabled and disabled through a settings key, a database connection is necessary to change these settings.
  1. Dot-source the _Get-ConnectionString.ps1_ script, to re-use the `Get-ConnectionString` function.
  2. Add a `Handle-Error` function that sets location back to the original directory, logs an error and returns `exit 1`. Use it throughout the script.
  3. Create a function, `Execute-SQL-Command` to execute a SQL command that does not return a dataset. 
     * This is used later on to set the value of the settings key.
  4. Add another function, `Execute-SQL-Data-Query` to execute an SQL query that returns a dataset. 
     * This is used later on to check the current value of the settings key.
  5. Switch to the _TrainingGuides.Web_ directory and retrieve the current connection string.
  6. Call the `Execute-SQL-Data-Query` function to check whether continuous integration is in use and save the return value to a variable for future reference.
  7. If CI is enabled, disable it using the `Execute-SQL-Data-Query` function.
  8. Call the `run` command in the .NET CLI and use the `--kxp-update` option to trigger the update, log an error if any issues are encountered.
  9. If CI was initially enabled, re-enable it, so that the serialized objects will reflect any new database structure.
  10. Attempt to serialize all objects with the _CI store_ operation, returning an error if it fails.
  11. Return to the original directory, in case the script is being run from a PowerShell window instead of with a click.


PS
**Update-XperienceDatabaseOnly.ps1**
Copy
```
<#
.Synopsis
    Updates Xperience by Kentico to the version specified by the installed NuGet packages.
#>

$originalLocation = Get-Location
Set-Location -Path $PSScriptRoot

. .\Get-ConnectionString.ps1

function Handle-Error {
    param(
        [string] $Message
    )
    Set-Location -Path $originalLocation
    Write-Error $Message
    Read-Host -Prompt "Press Enter to exit"
    exit 1
}

#Query that executes a command without returning a dataset.
function Execute-SQL-Command {
    param(
        [string] $ConnectionString,
        [string] $CommandText
    )
    $connection = New-Object system.data.SqlClient.SQLConnection($ConnectionString)

    $connection.Open()
    $command = new-object system.data.sqlclient.sqlcommand($CommandText,$connection)
    $transaction = $connection.BeginTransaction()
    $command.Transaction = $transaction

    try {
        $rowsAffected = $command.ExecuteNonQuery()
        Write-Host 'Command: '$CommandText
        Write-Host 'Rows affected: '$rowsAffected
        $transaction.Commit()
    }
    catch {
        Write-Error $_.Exception.Message
        return $FALSE
    }

    $connection.Close()

    return $TRUE
}

#Query that retrieves a data set
function Execute-SQL-Data-Query {
    param(
        [string] $ConnectionString,
        [string] $CommandText
    )
    $connection = New-Object System.Data.SqlClient.SQLConnection($ConnectionString)

    $connection.Open()

    $command = New-Object System.Data.SqlClient.SqlCommand($CommandText,$connection)
    $dataAdapter = New-Object System.Data.SqlClient.SqlDataAdapter($command)
    $dataset = new-object System.Data.Dataset
    $dataAdapter.Fill($dataset)

    $connection.Close()

    return $dataset
}

Set-Location -Path ..\src\TrainingGuides.Web

$appPath = Get-Location

$connectionString = Get-ConnectionString -Path $appPath -OriginalLocation $originalLocation

$resultDataSet = Execute-SQL-Data-Query -ConnectionString $connectionString -CommandText "SELECT KeyValue FROM CMS_SettingsKey WHERE KeyName = N'CMSEnableCI'"

$isUsingCD = $resultDataSet.Tables[0].Rows[0][0]

$readyToUpdate = $True

#Since the settings key value is a string and could theoretically be something other than true or false, compare the value rather than treating it as a boolean expression on its own
if($isUsingCD -eq 'True'){
    Write-Host 'Disabling continuous integration'
    $commandResult = Execute-SQL-Command -ConnectionString $connectionString -CommandText "UPDATE CMS_SettingsKey SET KeyValue = N'False' WHERE KeyName = N'CMSEnableCI'"
    $readyToUpdate = $commandResult
}

if($readyToUpdate){
    Write-Host 'Starting Xperience update'

    dotnet run --no-build --kxp-update

    if ($LASTEXITCODE -ne 0) {
        Handle-Error "Update failed."
    }
}
else{
    Handle-Error 'Unable to disable continuous integration to perform the update.'
}

if($isUsingCD -eq 'True'){
    Write-Host 'Re-enabling continuous integration'

    $commandResult = Execute-SQL-Command -ConnectionString $connectionString -CommandText "UPDATE CMS_SettingsKey SET KeyValue = N'True' WHERE KeyName = N'CMSEnableCI'"

    if(-not $commandResult){
        Handle-Error 'Unable to re-enable continuous integration.'
    }

    dotnet run --kxp-ci-store

    if($LASTEXITCODE -ne 0) {
        Handle-Error 'Unable to store continuous integration. Make sure to run the store operation after fixing any issues.'
    }
}

Set-Location -Path $originalLocation

Read-Host -Prompt "Press Enter to exit"
```

**Refactoring**
You may have noticed the _Update-XperienceDatabaseOnly_ script file contains two function definitions: `Execute-SQL-Command` and `Execute-SQL-Data-Query.` If you have use for these functions elsewhere, consider extracting them into separate files and re-using them, as you did `Get-ConnectionString` earlier.
**SQL transactions**
Note that unlike the SQL functionality in the _RestoreCI_ script, the functions in the _Update-XperienceDatabaseOnly_ script close the connection immediately, rather than executing several transactions on one connection.
This ensures no connections are left open during the update, but may be less efficient if these functions were repurposed to process several consecutive commands.
**Data consistency**
If the _CI store_ operation fails when you run this script, we highly recommend taking the following steps after addressing the issue that led to failure:
  1. Building your application
  2. Running [CI store](#ci-store)


This ensures that updates to the schema of objects in the database are not in conflict with the data serialized in the CI repository.
Make sure your solution builds without errors before running CI store - otherwise, the serialized data may not be in the correct format. 
You can also inspect [a similar sample PowerShell script](https://github.com/Kentico/xperience-by-kentico-training-guides/blob/finished/scripts/Update-XperienceProjectWithDatabase.ps1) that updates both the Xperience project files and database.
# What’s next?
Scripts like these will save your developers time and uncertainty in recurring tasks. All they need to do to run one of these scripts is right-click and choose Run with PowerShell, or alternately, open a PowerShell command line in the _scripts_ folder and call one of the files. You may want to customize these scripts to fit your team’s procedures or look into any other tasks that could potentially be automated in similar ways.
![]()
[]()[]()
