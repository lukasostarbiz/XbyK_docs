# Model website system pages
  * [ Copy page link ](modules/content-modeling-guide/website-system-pages#) | [Get HelpService ID](modules/content-modeling-guide/website-system-pages#)
Core MVC 5


[✖](modules/content-modeling-guide/website-system-pages# "Close page link panel") [Copy to clipboard](modules/content-modeling-guide/website-system-pages#)
A good website administration experience doesn’t only mean that editors can easily create and manage website content. A well-structured website offers editors ways to adjust system-specific features without asking developers for help whenever they need to change the website’s SEO performance. For example, you can create a ”Website settings” section in the content tree and create specific page types that content administrators can use to change system settings. We recommend including the following system pages in your project:
  * [Search result pages](modules/content-modeling-guide/website-system-pages#search-result-pages)
  * [Consent settings page](modules/content-modeling-guide/website-system-pages#consent-settings-page)
  * [SEO-specific pages](modules/content-modeling-guide/website-system-pages#seo-specific-pages)
    * [Sitemap](modules/content-modeling-guide/website-system-pages#sitemap)
    * [Error pages](modules/content-modeling-guide/website-system-pages#error-pages)
    * [Robots.txt and no-indexing content](modules/content-modeling-guide/website-system-pages#robots.txt-and-no-indexing-content)


### Search result pages
Xperience doesn’t currently provide any solution out of the box that will search through website data and display results on an internal search results page. To implement search functionality for the website, use a custom solution.
**Recommendations – Search**
Considering your project requirements, we recommend using one of the available integrations with [Algolia Search](https://github.com/Kentico/xperience-by-kentico-algolia), [Lucene Search](https://github.com/Kentico/xperience-by-kentico-lucene), or [Azure AI Search](https://github.com/Kentico/xperience-by-kentico-azure-ai-search).
### Consent settings page
Xperience comes with extensive [Data protection](documentation/developers-and-admins/data-protection) functionality that allows for building websites compliant with many different e-privacy laws and legislation.
Administrators can use the data protection features to create and manage tracking consents or easily remove personal data from the administration UI. Once the personal data is in the system, marketers can easily use the built-in Xperience online marketing features, e.g., ensure that marketing emails are sent only to visitors who provided appropriate consent.
To comply with data privacy regulations, developers must implement data collection and erasure features to match project requirements and accommodate how the website gathers, processes, and stores personal data.
Find out more about customer data protection in Xperience:
  * [Customer data management](documentation/business-users/digital-marketing/contact-management)
  * [Data protection](documentation/developers-and-admins/data-protection)


**Recommendations – Consents**
To ensure marketers stay within legal boundaries with their marketing activities, we recommend leveraging the Xperience data protection functionality for collecting and managing every piece of visitor data in the customer life-cycle, from cookie tracking up to data submitted through online forms. We recommend building a consent settings page to allow users to manage their consent.
### SEO-specific pages
#### Sitemap
Sitemaps help search engines correctly index website content and significantly affect search rankings. You need to create a custom generator to build a website sitemap.
**Recommendations – SEO**
We recommend doing a custom implementation of the sitemap generator, including a mechanism that enables editors to decide which page URLs to include in the sitemap and a way to canonicalize URLs. For example, add a custom URL canonical field to the page’s **Content** tab.
#### Error pages
Errors can appear on different occasions. Visitors can request content that’s no longer available on the page, or they can make a typo in the URL. A user-friendly error page and simple website navigation can improve user experience and guide them to relevant content. Xperience uses MVC conventions for handling error pages.
**Recommendations – Errors**
Give editors the power to personalize the content of error pages by enabling Page Builder. It can help if your website contains a lot of content and the menu needs to provide quick navigation. The most frequently used error pages are:
  * 404 - Page Not Found.
  * 403 Forbidden - You do not have permission to access this page.
  * 400 Bad request - Your browser performed an invalid operation.
  * 500 Internal server error - The server encountered an error while processing your request.


#### Robots.txt and no-indexing content
Website administrators sometimes want to prevent search engine agents from indexing specific pages or parts of the website. The _Robots.txt_ page can help in such situations.
**Recommendations - Indexing**
We recommend providing editors or administrators with means to disallow parts of the website from search engines:
  * Add dedicated fields on the _Content_ tab to disable indexing of individual website pages. Selecting the fields should include the no-index directive in your output markup.
  * To disable indexing parts of the website, create a dedicated page (or a section in a custom module) for _Robots.txt_ file with, e.g., a _Text area_ form control and use custom code to process the directives administrators want to input into the Robots.txt. The implementation could also restrict the website’s indexing by specific search bots.


[ Previous page ](modules/content-modeling-guide/model-navigation)
25 of 28
[ Mark complete and continue ](modules/content-modeling-guide/general-content-modeling-recommendations)
  * [Community Questions & Answers](https://community.kentico.com/q-and-a)
  * [Contact support](https://community.kentico.com/support)


### Cookie consent
We use necessary [cookies](https://www.kentico.com/cookies-policy) to run our website and improve your experience while browsing. Additional cookies are only used with your consent. You may revoke your consent on the [Cookies Policy](https://www.kentico.com/cookies-policy) page or in your browser at any time. 
ACCEPT ALL  [Configure](https://www.kentico.com/cookies-policy)
USE ONLY NECESSARY 
![](https://docs.kentico.com/modules/content-modeling-guide/website-system-pages)
[](https://docs.kentico.com/modules/content-modeling-guide/website-system-pages)[](https://docs.kentico.com/modules/content-modeling-guide/website-system-pages)