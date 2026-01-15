# Vulnerabilities in transitive dependencies
  * [ Copy page link ](documentation/developers-and-admins/security-guidelines/vulnerabilities-in-transitive-dependencies#) | [Get HelpService ID](documentation/developers-and-admins/security-guidelines/vulnerabilities-in-transitive-dependencies#)
Core MVC 5


[✖](documentation/developers-and-admins/security-guidelines/vulnerabilities-in-transitive-dependencies# "Close page link panel") [Copy to clipboard](documentation/developers-and-admins/security-guidelines/vulnerabilities-in-transitive-dependencies#)
A dependency is a software component released by a third-party, which is required by the Xperience product. See [Third party software licenses](https://community.kentico.com/documentation/third-party-software-licenses) to view a list of dependencies and the licenses under which they are used.
Kentico distinguishes between two types of dependencies:
  * **Direct dependencies**  
Packages that are explicitly added and required by our product. We have full control over the version used and we are fully responsible for keeping the usage of these dependencies up-to-date and secure.
  * **Transitive dependencies**  
Packages that are indirectly required, meaning they are dependencies of our direct dependencies. They are managed by the authors of these direct dependencies. We do not have direct control over their versions, but they can still impact our product and should be monitored.


When a vulnerability with a [CVSS](https://www.first.org/cvss/) score of 7 and higher is discovered in a transitive dependency, we aim to provide a temporary fix, [overriding the version](https://learn.microsoft.com/en-us/nuget/concepts/auditing-packages#transitive-packages) by adding a direct reference to the Xperience by Kentico packages. This override is monitored by us and removed once a new version of the direct dependency that references the fixed transitive dependency becomes available.
For vulnerabilities with a score below 7, it is **your responsibility** to monitor and manage them. We recommend [setting up the NuGet audit process](https://learn.microsoft.com/en-us/nuget/concepts/auditing-packages) in your development and deployment pipeline to stay informed. If vulnerabilities are discovered, you need to override affected transitive dependencies in your project.
The version override may lead to discrepancies between the versions expected by your project and those introduced by the override, which need to be [resolved](https://learn.microsoft.com/en-us/nuget/concepts/dependency-resolution) to ensure compatibility and stability.