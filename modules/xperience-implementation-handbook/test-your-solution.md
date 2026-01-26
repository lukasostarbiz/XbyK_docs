---
source: https://docs.kentico.com/modules/xperience-implementation-handbook/test-your-solution
scrape_date: 2026-01-26
---

Module: Xperience implementation handbook
6 of 9 Pages
# Test your solution
## Functional testing
Thoroughly testing the functions of your solution helps to ensure a good user experience.
When testing the functionality of your project, consider the following:
  * Test all use case scenarios with automated tests. 
    * Set up end-to-end (E2E) testing for the solution, with tools like [Playwright](https://playwright.dev/) and [Selenium](https://www.selenium.dev/) for the live site, and [Cypress](/documentation/developers-and-admins/customization/extend-the-administration-interface/administration-interface-ui-tests#cypress) for the admin UI.
    * Add unit tests to ensure the correct functioning of your granular code components, like individual methods.
    * Create integration tests to make sure components interact as expected with mock data.
  * Validate website code and accessibility for all devices and browsers you are planning to support.
  * Ensure security: 
    * Check configuration, user accounts with empty passwords, etc., on the application level.
    * Use 3rd party tools and services to perform penetration testing from the outside (e.g., [Burp Suite](https://portswigger.net/)).


### Functional test cases
#### Avoid site outages
A client did not spend enough time on testing to determine the right performance tier for their project size, implementation, and traffic. They had no issues initially, but the performance tier they used was discontinued and they were forced to change tiers.
Since they didn’t have performance and load testing, they blindly chose an option they thought would work. However, during peak hours, the website went down because the selected performance tier was not able to handle the load.
The client immediately upgraded to a higher performance tier (via Azure management portal), but given the size of the database, the operation took some time and the website was offline for several hours.
#### Use case testing
A partner didn’t collect use cases at the beginning of the project. As a result, they failed to include testing of use cases into their workflow before delivering the finished website.
One of the most important features was collecting visitor data for personalization and marketing automation in the later stages of the project. The partner did not configure forms to map submitted data to contacts. This meant the collected data wasn’t available for Kentico’s online marketing features.
This caused a serious delay in delivering the project to the customer and pushed the project over the planned budget. If they had identified these use cases up front, they would have been able to avoid the delay.
#### Security vulnerabilities
A client’s website started displaying links to malicious websites. The first thought was that there was a severe security issue with the out-of-the-box software.
They were unable to confirm this, however. During the investigation, it was revealed that a solution partner implemented custom code that contained a SQL injection vulnerability. The vulnerability was introduced in a custom search filter which used _where_ conditions to filter out the search results.
This _where_ condition was not sanitized for malicious user input. Penetration testing could have identified this issue ahead of time, but the partner didn’t include penetration tests into their project workflow.
## Regression testing
Regression testing is an important step to ensure that previously tested parts of the software still perform correctly when adding new functionality.
### Non-functional testing
When testing the implementation, you should:
  * Investigate and address all issues produced during the testing phase.
  * Perform all tests in a production-like environment.


When running performance, load, and capacity tests, there are several things you need to consider:
  * Define your performance baseline. 
    * What are the results of your tests and what is the goal?
  * Depending on the type of tests you would like to perform and what area are you testing (front-end, back-end, environment), select the most suitable testing tool (e.g. [loadimpact.com](https://loadimpact.com/), [Xperience MiniProfiler integration](https://github.com/Kentico/xperience-by-kentico-miniprofiler), Visual Studio, Web Capacity Analysis Tool (WCAT)).
  * Perform one change at a time and measure the impact of the change on performance.
  * Focus on the optimizations or areas that can bring you the most significant results.
  * Perform all tests in a production-like environment. 
    * The results of running tests on your local environment won’t reflect how the website performs under heavy load on a hosting server. There are multiple variables involved, which may influence the performance. For example, network connectivity between the web and database servers, and configuration of the server or file system options, such as external storage providers, may differ between environments and have a significant impact on performance.
  * Keep the testing environment consistent so that you can compare test results directly.


[ Previous page ](/modules/xperience-implementation-handbook/develop-your-project)
6 of 9
[ Mark complete and continue ](/modules/xperience-implementation-handbook/deploy-to-a-new-environment)
![]()
[]()[]()
