# CI/CD database migration scripts
  * [ Copy page link ](documentation/developers-and-admins/ci-cd/ci-cd-database-migration-scripts#) | [Get HelpService ID](documentation/developers-and-admins/ci-cd/ci-cd-database-migration-scripts#)
Core MVC 5


[✖](documentation/developers-and-admins/ci-cd/ci-cd-database-migration-scripts# "Close page link panel") [Copy to clipboard](documentation/developers-and-admins/ci-cd/ci-cd-database-migration-scripts#)
The Xperience [Continuous Integration](documentation/developers-and-admins/ci-cd/continuous-integration) and [Continuous Deployment](documentation/developers-and-admins/ci-cd/continuous-deployment) features serialize the data of most objects from the database into XML files on the file system. However, changes to database indexes, functions, stored procedures, custom views, and similar are not supported by CI/CD. To keep the database consistent, these changes need to be handled manually.
We recommend using **migration scripts** to synchronize database changes not managed by CI/CD.
## Synchronize database changes using migration scripts
A migration script allows you to alter a database by modifying its schema. This alteration can be as simple as adding or removing a column, or a complex refactoring task such as splitting tables or changing column properties in a way that could affect the stored data.
### Migration scripts in a CI/CD workflow
In addition to serialized database object data, you can add the following migration script files to the repository folder:
  * **@migrations** – folder for storing all created migrations connected to a particular repository. Since all migration scripts for a repository are stored in this folder, their file names need to be unique.
  * **Before.txt** – holds the file names of all migrations applied before the repository is restored to the database. Insert only the migration file names without the `.sql` suffix. Each file name must be added on a **separate line**.
  * **After.txt** – holds the file names of all migrations applied after the repository is restored to the database. Insert only the migration file names without the `.sql` suffix. Each file name must be added on a **separate line**.


The Xperience database contains the **CI_Migration** and **CD_Migration** tables used by the system to record migrations already executed on the database.
When you need to create or modify objects not supported by CI/CD (for example table indexes, functions, or stored procedures) and you want to synchronize or deploy these changes, you need to:
  1. Write a migration script that applies the required change to the database. Refer to [Example - Database change migration script](documentation/developers-and-admins/ci-cd/ci-cd-database-migration-scripts#example---database-change-migration-script) for an example of the process.
  2. Reference the migration script in either the **Before.txt** or **After.txt** file.
  3. Run the CI/CD restore utility with the `--enable-migrations` parameter on databases you want to keep synchronized.
CMD
**Run database migrations as part of CI/CD restore process**
Copy
```
dotnet run -- --kxp-ci-restore --enable-migrations
```

     * For [Continuous Deployment](documentation/developers-and-admins/ci-cd/continuous-deployment) scenarios, you can adjust the path to migration-related files using the `--migrations-path` parameter. The location must contain the _@migrations_ folder and the _Before.txt_ and _After.txt_ files. Specify either an absolute or application root-relative path to the files. If you store migrations in the root of your CD repository, the parameter can be omitted (the migration path is taken from `repository-path`). 
When [deploying](documentation/developers-and-admins/deployment/deploy-to-the-saas-environment) to our [SaaS](documentation/developers-and-admins/saas) solution, migrations detected in the deployment package are applied automatically. 
CMD
**Set custom path to migrations data for CD restore**
Copy
```
dotnet run -- --kxp-cd-restore --repository-path "..." --enable-migrations --migrations-path "..."
```



### When to execute a migration?
After creating a migration script, you need to decide whether it is going to be applied before or after the CI/CD files are restored.
The **Before.txt** file should only contain migrations that manipulate the database before the CI/CD files are restored. These migrations can, for example:
  * Drop indexes before their tables are deleted by the restore process.
  * Delete foreign key constraints before their tables are deleted by the restore process.


The **After.txt** file should only contain migrations that manipulate the restored database. These migrations can, for example:
  * Create new indexes.
  * Create new views.
  * Add foreign key constraints.
  * Add or modify stored procedures.


If you have multiple migrations, carefully consider the order in which they should be executed. The migration scripts in both the _Before.txt_ and _After.txt_ files are executed sequentially from the first to the last line.
### Example - Database change migration script
If you need to create a new index on, for example, a column **OfficeName** in a custom module class table **CompanyOffices** and want this change to be reflected in the repository, you need to: 
  1. Create a new migration script (for example, **AddOfficeNameIndex.sql**):
SQL
**AddOfficeNameIndex.sql**
Copy
```
CREATE INDEX IX_CompanyOffices_OfficeName ON CompanyOffices(OfficeName);
```

  2. Add the migration script to the  _@migrations_ folder.
  3. Add the migration name to a new line in the  _After.txt_ file (the index should only be created after the database is updated).
Text
**After.txt**
Copy
```
...
AddOfficeNameIndex
```

  4. Commit your changes to your source control repository (CI) or add them to the appropriate deployment repository (CD).


When the repository containing your changes is restored with the `--enable-migrations` parameter on another database, the **AddOfficeNameIndex.sql** migration is applied after the restore operation (creating the _IX_CompanyOffices_OfficeName_ index). The migration is then added to the _CI_Migration_ table, marked as applied, and will not be executed again on the target database.
## Roll back database changes
When rolling back changes in Continuous Integration scenarios, it is important to realize that migrations you have created and committed to source control may have already been applied to other databases. Therefore, removing your migrations from the _@migrations_ folder and _Before.txt_ or _After.txt_ is **insufficient**. You also need to write a new migration script that returns the database schema to its original state.
### Example - Roll back changes
The rollback process is demonstrated here by extending the [example above](documentation/developers-and-admins/ci-cd/ci-cd-database-migration-scripts#example---database-change-migration-script). To revert the changes made by the **AddOfficeNameIndex.sql** migration, you need to:
  1. Delete the migration script **AddOfficeNameIndex.sql** from the _@migrations_ folder.
  2. Remove the migration name **AddOfficeNameIndex** from the list of migrations in the  _After.txt_ file.
  3. Create a new migration script (for example, **RemoveOfficeNameIndex.sql**) that removes the index (if it exists):
SQL
**RemoveOfficeNameIndex.sql**
Copy
```
IF EXISTS(SELECT * FROM sys.indexes WHERE name = 'IX_CompanyOffices_OfficeName' AND object_id = OBJECT_ID('CompanyOffices'))
BEGIN
    DROP INDEX IX_CompanyOffices_OfficeName ON CompanyOffices
END
```

  4. Add the migration script to the _@migrations_ folder.
  5. Add the migration name to a new line in the  _Before.txt_ file (the index should be dropped before its table is deleted during the restore process):
Text
**Before.txt**
Copy
```
...
RemoveOfficeNameIndex
```

  6. Commit your changes to your source control repository.


When the repository containing your changes is restored with the `--enable-migrations` parameter on another database, the **RemoveOfficeNameIndex.sql** migration is applied before the restore operation (removing the _IX_CompanyOffices_OfficeName_ index, if it exists). The migration is then added to the _CI_Migration_ table, marked as applied, and will not be executed again on the target database.
### Restore the database to a specific point in time
If you need to revert your database to a specific backup, we recommend restoring the backup to a clean database to avoid possible structural database conflicts which may arise when restoring the database to an older version.
  1. Create a clean Xperience database or restore a database backup from before the point in time you wish to restore.
  2. Get the required version of your Xperience solution and the repository folder from your source control system.
  3. Update the **CMSConnectionString** in the project’s configuration file (_appsettings.json_ by default) to point to the new database.
  4. Rebuild the solution in Visual Studio.
  5. Execute your Continuous Integration restore process (including migration scripts).


After the restore finishes, you can continue working with the restored database backup.