# Changelog
  * [ Copy page link ](documentation/changelog#) | [Get HelpService ID](documentation/changelog#)
Core MVC 5
  * [ Subscribe to product releases ](feeds/xbyk-releases.xml)
  * [ Subscribe to security updates ](feeds/xbyk-security.xml)


[✖](documentation/changelog# "Close page link panel") [Copy to clipboard](documentation/changelog#)
### Hotfix (January 8, 2026)
**version 31.0.2**
Fixed issues
  * Performance – The hotfix improves the performance of the [ContentRetriever API](documentation/developers-and-admins/api/content-item-api/content-retriever-api) when retrieving a larger number of content items or pages with [linked items](documentation/business-users/content-hub/content-items#link-content-items).
  * Read-only deployments – When the application was running in [Read-only mode](documentation/developers-and-admins/deployment/read-only-deployments), accessing assets in the blob storage that were not previously cached caused exceptions, and the assets failed to load.