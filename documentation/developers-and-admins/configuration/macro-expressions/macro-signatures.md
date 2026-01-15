# Macro signatures
  * [ Copy page link ](documentation/developers-and-admins/configuration/macro-expressions/macro-signatures#) | [Get HelpService ID](documentation/developers-and-admins/configuration/macro-expressions/macro-signatures#)
Core MVC 5


[✖](documentation/developers-and-admins/configuration/macro-expressions/macro-signatures# "Close page link panel") [Copy to clipboard](documentation/developers-and-admins/configuration/macro-expressions/macro-signatures#)
Whenever [macro expressions](documentation/developers-and-admins/configuration/macro-expressions) are saved in the administration, the system automatically adds a security signature. Signatures ensure the integrity of macros and identify the user who entered the macro.
Every macro signature contains an identifier of the macro’s author and a hash of the given expression. To increase the level of security, the hash function used when creating macro signatures appends a salt to the input (a sequence of additional characters). The salt value depends on the [configuration](documentation/developers-and-admins/configuration/macro-expressions/macro-signatures#configure-the-hash-salt-for-macro-signatures) of the application’s environment, so the signatures are by default not valid when deploying macros to other instances of Xperience.
## Configure the hash salt for macro signatures
The system appends a [salt](http://en.wikipedia.org/wiki/Salt_%28cryptography%29) to the input of the hash function that creates macro signatures.
If a specific hash salt value is not assigned during the installation of a new Xperience project, a randomly generated [GUID](http://en.wikipedia.org/wiki/GUID) is assigned. The salt value is configured through the `CMSHashStringSalt` key in the _appsettings.json_ file. Instances without this configuration key use the application’s main database connection string as the salt (the exact `CMSConnectionString` value in the _appsettings.json_ file).
We strongly recommend having the hash salt configuration key set to a specific value, which provides the following benefits:
  * **Stability** – you do not need to re-sign macros if your connection string changes
  * **Deployment** – you can use the same salt for other instances of Xperience, which makes it easier to deploy data containing macros


The best option is to set the hash salt value before you start creating content in your project. If you plan to [deploy to the SaaS environment](documentation/developers-and-admins/deployment/deploy-to-the-saas-environment), use the hash salt value provided for your project in [Xperience Portal](documentation/developers-and-admins/saas/xperience-portal).
**Impact of hash salt changes**
Changing the salt causes all current hash values to become invalid, including the signatures of macros. Having invalid macros throughout the system may lead to problems. You need to [re-sign macros](documentation/developers-and-admins/configuration/macro-expressions/macro-signatures#re-sign-macros) immediately after changing the hash salt value. In addition to macro signatures, the system uses the `CMSHashStringSalt` value for other hash functions.
To change the salt value for your application, edit the `CMSHashStringSalt` configuration key. You can use any string as the value, but the salt should be random and at least 16 characters long. For example:
JSON
Copy
```
"CMSHashStringSalt": "e68b9ad6-a461-4707-8e3e-ece73f03dd02",
```

## Re-sign macros
If the hash salt value used by your application changes, the security signatures of existing macro expressions become invalid. For example, you may encounter problems with unresolved macros:
  * After setting a new custom salt via the `CMSHashStringSalt` configuration key.
  * When deploying data containing macros (e.g., [Contact groups](documentation/business-users/digital-marketing/contact-groups) with conditions) to an instance of Xperience with a different hash salt.
  * If your application does not have a custom hash salt, and the connection string has changed (for example when moving to a different server or after setting a new database password).


If you encounter such problems, you need to re-sign macros.
Xperience provides a [.NET command-line interface](https://docs.microsoft.com/en-us/dotnet/core/tools/) command that re-signs all macros in the system:
  1. Open the command line prompt.
  2. Navigate to your Xperience project’s root directory.
  3. Use [dotnet run](https://docs.microsoft.com/en-us/dotnet/core/tools/dotnet-run) to execute the `--kxp-resign-macros` command:
CMD
**Example**
Copy
```
dotnet run --no-build -- --kxp-resign-macros --old-salt "Old_Salt" --new-salt "New_Salt"
```

**Command parameters**
The `--kxp-resign-macros` command has the following parameters:
`–old-salt`  
OR  
`–sign-all` |  If you specify the `–old-salt` parameter, only macros that have a valid signature under the old salt are re-signed and the identifiers of the macro authors remain unchanged. You need to enter the old salt that was used to generate the security hash of existing macro expressions in the system.  With the `–sign-all` parameter, the macro re-signing process skips the signature integrity check and creates new signatures for all macros. You do not need to enter the old salt value in this case. The new signatures contain the identity of the user specified in the `–username` parameter.  **Security warning** : With the `–sign-all` parameter, the resigning also includes macros that are unsigned or have invalid signatures. If your system’s data contains invalid macros, such macros receive valid signatures and represent potential security threats.   
---|---  
`–new-salt` |  Optional parameter that sets the hash salt used to re-sign macros. If not specified, macros are automatically re-signed using the application’s current hash salt value.  For example, use the `–new-salt` parameter if you are planning to change your application’s hash salt, or when preparing macros for deployment to other Xperience instances.   
`–username` |  The name of the user added to macro signatures when re-signing all macros with the `–sign-all` parameter.   
We recommend running the command with the `--no-build` parameter to avoid an unnecessary project build.
Add the parameters after the `--` syntax separator to pass the options directly to the Xperience application and avoid conflicts with .NET CLI parameters.


The command replaces the security signature in occurrences of macros based on the specified paramaters.