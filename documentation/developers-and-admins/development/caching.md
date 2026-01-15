# Caching
  * [ Copy page link ](documentation/developers-and-admins/development/caching#) | [Get HelpService ID](documentation/developers-and-admins/development/caching#)
Core MVC 5


[✖](documentation/developers-and-admins/development/caching# "Close page link panel") [Copy to clipboard](documentation/developers-and-admins/development/caching#)
A cache stores data so that future requests for that data can be served faster. Correctly using caching can significantly improve the performance of your website.
Caching the content of web pages provides two primary benefits:
  * Quicker data loading (retrieving data from the cache avoids communication with slower storage spaces, such as the website’s SQL database and file system)
  * Reduction of unnecessary page processing on repeated requests


The built-in caching mechanisms of Xperience work primarily on the server side and utilize the application’s memory to store data. The system saves data into the cache under **cache keys**. Cache keys have unique names that exactly identify the cached content. Each key stores the cached data itself (depending on the type of the cache), as well as other information such as the expiration time or [dependencies](documentation/developers-and-admins/development/caching/cache-dependencies).
## Clear the cache
To remove all server-side cached content from the Xperience application’s memory:
  1. Open the **System** application in the Xperience administration.
  2. In the **Overview → Memory statistics** , select **Clear cache**.