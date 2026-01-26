---
source: https://docs.kentico.com/documentation/developers-and-admins/customization/extend-the-administration-interface
scrape_date: 2026-01-26
---

  * [Home](/documentation)
  * [Developers and admins](/documentation/developers-and-admins)
  * [Customization](/documentation/developers-and-admins/customization)
  * Extend the administration interface 


# Extend the administration interface
The Xperience administration interface exposes a comprehensive customization framework that enables developers to
  * add new functionality to existing applications and pages,
  * add entirely new pages with custom functionality to applications in the system,
  * create new applications with dedicated UI and functionality, and
  * create new UI form components to manage user input when editing objects and configuring other components.


The administration consists of two main parts:
  * **Client application** – the front-end (client-side) part of the administration interface implemented as a single-page browser application using the [React JavaScript library](https://en.wikipedia.org/wiki/React_\(JavaScript_library\)). Provides a graphical abstraction over various management APIs that is suitable for business user interaction with the system.
  * **Back end** – sits in the ASP.NET Core web application as a set of API endpoints called by the client application. The back-end implementation is responsible for interacting with Xperience APIs and other business logic to perform database operations (typically [CRUD](https://en.wikipedia.org/wiki/Create,_read,_update_and_delete) over system objects), calling specific management APIs according to instructions received from the client application, and various other tasks.
All admin UI components (pages, applications) must contain a corresponding back-end definition and registration. For example, every page in the admin UI is registered on the back end using the following metadata:
    * React component used to render the page
    * the type that implements the page on the back end (handles requests from user interaction, provides data to display)
    * the page’s parent
    * the URL slug under which it’s accessible. A similar registration process is used for every admin UI element. With this approach, the back end drives the layout, appearance, and functionality of the entire administration application. The main benefit is that you can introduce new pages and other elements to the UI without writing any front-end code, assuming you reuse React components already available to the client application.


The following simplified diagram illustrates the described structure:
[![Xperience admin UI architecture overview](/docsassets/documentation/extend-the-administration-interface/BasicArchitectureOverview.png)](/docsassets/documentation/extend-the-administration-interface/BasicArchitectureOverview.png)
Both parts of the application communicate using a set of API endpoints. As a general rule, every action available in the client application has a corresponding handler on the back end that processes the request and ensures propagation to the database, if necessary. Typical examples are responding to user interactions (such as form submissions, button clicks) or during page requests to retrieve the data to display.
## Before you get started
  1. Take a look at [Admin UI customization model overview](/documentation/developers-and-admins/customization/extend-the-administration-interface/admin-ui-customization-model-overview) for a brief overview of the available customization options.
  2. Follow the instructions on [Prepare your environment for admin development](/documentation/developers-and-admins/customization/extend-the-administration-interface/prepare-your-environment-for-admin-development) to set up an environment needed for admin development. Depending on the type and scope of the planned customizations, you may need to write custom JavaScript modules for the client application, which requires additional tooling.
  3. Read the pages in this section to learn more about individual extensions.


![]()
[]()[]()
