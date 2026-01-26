---
source: https://docs.kentico.com/documentation/developers-and-admins/development/routing/content-tree-based-routing
scrape_date: 2026-01-26
---

  * [Home](/documentation)
  * [Developers and admins](/documentation/developers-and-admins)
  * [Development](/documentation/developers-and-admins/development)
  * [Routing](/documentation/developers-and-admins/development/routing)
  * Content tree-based routing 


# Content tree-based routing
With content tree-based routing, the system generates and matches page URLs based on their position in the website’s [content tree](/documentation/business-users/website-content). You don’t need to perform any manual route mapping and configuration. All routing logic is handled for you by the system. 
This method of routing is not a full replacement of the standard ASP.NET routing model. Instead, it augments the routing pipeline, handling requests targeting the site’s pages. You can still set up [additional routes](/documentation/developers-and-admins/development/routing/content-tree-based-routing/set-up-content-tree-based-routing#combine-content-tree-based-and-asp.net-routing) for your application.
This simplified diagram models request handling when using content tree-based routing:
[![Request handling in the content tree-based routing mode](/docsassets/documentation/content-tree-based-routing/Routing_SimplifiedFlow.png)](/docsassets/documentation/content-tree-based-routing/Routing_SimplifiedFlow.png)
Incoming requests are matched against a general route and their target URL compared to system URLs and [vanity URLs](/documentation/business-users/website-content/manage-page-urls#edit-vanity-urls-of-pages) of existing pages. If there is a match with a route in the system (system or vanity URL), the system takes over the routing logic. Otherwise, the request continues matching against routes registered further down the routing table using conventional ASP.NET routing.
Once the request is taken over by Xperience, all routing happens automatically. You need to supply a controller, view model and view for each [content type](/documentation/developers-and-admins/development/content-types) to provide the desired processing logic and output formatting.
## Page URL generation
The system automatically generates URLs only for pages that have the  _Include in routing_ option set in their [content type](/documentation/developers-and-admins/development/content-types) definition.
A page system URL is generated when:
  * a new page is created
  * the [URL slug](/documentation/business-users/website-content/manage-page-urls) of an existing page is updated


The overall system URL is determined by the page’s location in the site’s content tree:
  * The system takes all of the page’s ancestors up to the site’s root (‘/’): 
    * If an ancestor has the  _Include in routing_ option set for its content type, it contributes a segment to the page’s resulting URL. All other ancestors are skipped.
    * By default, the segment is formed from the page’s title converted to a URL-sanitized string.


The following diagram illustrates how URLs are generated based on a page’s position in the content tree and the configuration of its ancestors:
[![Automatic URL generation illustrated](/docsassets/documentation/content-tree-based-routing/Routing_AutomaticRoutingIntro.png)](/docsassets/documentation/content-tree-based-routing/Routing_AutomaticRoutingIntro.png)
The _Articles_ section holds a collection of articles (_Article 1_ and _Article 2_). All pages are based on content types that have the  _Include in routing_ option set. As a result, both articles also contain their parent page segment (_Articles_) in their URL.
The _Landing pages_ section holds a collection of landing pages (_Page 1_ and _Page 2_). Only the landing pages are based on a content type with the _Include in routing_ option set. As a result, the URLs of both landing pages do not include their parent.
### Multilingual URLs
If you have multiple [languages](/documentation/developers-and-admins/configuration/languages) set up in your project, the settings of individual languages affect system URL generation in the following way:
  * For pages that are in the [primary language of a website channel](/documentation/developers-and-admins/configuration/website-channel-management), URLs are not modified. 
    * For example, if English is set as the primary language of the website channel, the URLs of pages in _English_ will be in the `~/page` format.
  * For pages in a non-primary language, the language’s **Code name** is inserted into the URL path after the site’s root (‘/’). 
    * For example, if English is set as the primary language of the website channel, the URLs of pages in _Spanish_ will be in the `~/es/page` format.
    * Vanity URLs of pages in a non-primary language DO NOT include the language’s _Code name_.
  * [Vanity URLs](/documentation/business-users/website-content/manage-page-urls#edit-vanity-urls-of-pages) and the configurations of [canonical URLs](/documentation/business-users/website-content/manage-page-urls#select-the-canonical-url-of-pages) are completely separate for each language. 
    * Regardless of the primary language of the website channel, the vanity URLs of pages in _Spanish_ will be in the `~/spanishVanityPath` format and in _English_ it will be in the `~/englishVanityPath` format.


## Handling of unpublished and deleted pages
When unpublishing or deleting a page, you can select how the requests for the page will be handled. Requests can be [redirected](/documentation/business-users/website-content/edit-and-publish-pages#redirects-from-unpublished-and-deleted-pages) to another page, or content in a fallback language can be served if available. Once a system URL is generated for a page, the URL persists until the corresponding page is deleted without a redirect.
If a request targets an unpublished or a deleted page without a redirect and without a fallback language variant, the system responds with the [HTTP 404 (Not Found)](https://en.wikipedia.org/wiki/HTTP_404) status and the request does not continue matching against other routes. This is to prevent accidentally matching requests intended to be handled by Xperience against other loosely constrained routes registered further down the routing table. You can use conventional [404 error handling](https://docs.microsoft.com/en-us/aspnet/core/fundamentals/error-handling) for such cases.
## Configure your project to use content tree-based routing
To start using content tree-based routing in your project:
  1. [Enable content tree-based routing](/documentation/developers-and-admins/development/routing/content-tree-based-routing/enable-content-tree-based-routing).
  2. [Set up the routing environment](/documentation/developers-and-admins/development/routing/content-tree-based-routing/set-up-content-tree-based-routing).


![]()
[]()[]()
