---
source: https://docs.kentico.com/guides/architecture/xperience-implementation-handbook/gather-requirements
scrape_date: 2026-01-26
---

  * [Home](/guides)
  * [Architecture](/guides/architecture)
  * [Xperience implementation handbook](/guides/architecture/xperience-implementation-handbook)
  * Gather requirements 


# Gather requirements
## Environment requirements
The questions in this section focus on the processes and infrastructure that your project demands. Your team’s structure and procedures address some of these questions, and your client’s needs and requirements will answer the rest. The responses further your understanding of the constraints you need to work within and provide you with sufficient opportunity to address potential shortcomings.
### Development team
  * How many developers are working on this project?
  * Do developers collaborate or work independently?
  * What source control system will you use?
  * Will you use any external resources for development?
  * What is the distribution of expertise in your team? For example, do you have full-stack developers, or separate front-end and back-end developers?


### Deployment strategy
  * What is your DevOps strategy?
  * How often are you planning to deploy new features/code?
  * How much downtime is acceptable during deployments?
  * Do you plan to have automated deployments?


### Infrastructure (Dev, Stage, QA, Prod)
  * Should you have multiple servers for load distribution?
  * Do you plan to distribute servers across different geographical regions?
  * Will you use additional environments, such as those for testing, integration, and staging?
  * Will content owners work in production environment directly?
  * Are you planning to use online marketing features?


### Hosting
  * Will you use [Xperience SaaS](/documentation/developers-and-admins/saas/saas-overview) or manage your own hosting?
  * Will you host on-premise or in the cloud?
  * Is there a preferred hosting provider (e.g., Azure, AWS)?
  * Will the SQL server be dedicated or shared? 
  * Do you require any type of auto-scaling?
  * Should media and design assets be distributed to different geographic locations with a CDN?
  * What health monitoring and backup tools do you plan to use?


### Expected traffic
  * Based on current traffic and projections, what is the expected average and peak traffic daily/monthly (e.g., unique visitors, page views, file downloads, etc.)?
  * What is the required maximum response time for a single page request to load? 
  * Are there different maximum response times for visitors from different geographic locations?


### Disaster recovery
  * What is the disaster recovery strategy for this project?
  * How often will you back up the application files and the database?
  * Will the backup process be automated?
  * Does the client have expectations regarding what should and should not be recoverable?
  * What is the tolerance for time to recovery?


### Scenarios
Here are some example scenarios to demonstrate how knowing or not knowing the answers to these questions can help or hurt your project.
#### Client requires fast load times on a global site
A partner was working with a client that operates a global site and required that the site would load within 1-2 seconds in every country they operate in. Because they knew that one of the biggest slowdowns to page loads in other countries are the supporting assets like images, CSS, and JavaScript, they planned to implement a CDN to load these assets.  
If they had not identified that the site needed a CDN, they might have stored the assets in ways that don’t support CDNs out of the box. Additionally, they knew to configure the system to be aware of the CDN, so that the links are added properly. This avoided a costly delay in the project’s go-live as they would have needed to re-link assets on the site. This resulted in a site that loaded quickly from day one.
#### Performance issue due to on-premise hosting limitations
The front-end developers did not pay close attention to optimization of resources like JS, CSS stylesheets and images. This led to a home page that loaded approximately 5MB of data spread across approximately 150 requests for new visitors and visitors with expired client caches. The organization hosted the site on-premise along with other mission critical services not connected to the website. The organization had relatively limited bandwidth and the added request load from the un-optimized home page resulted in an outage not only for the website, but for the other mission critical services.  
If they considered the production environment’s limitations, they could have avoided this issue by focusing more on the website performance and considering to host this website elsewhere. This would have prevented the chain reaction from bringing down the unrelated services.
#### Handling traffic spikes
When discussing the traffic requirements for a project, a partner found that regular daily traffic is generally moderate to low, but the client expects significantly higher traffic once a quarter, due to a regular event. The partner used this knowledge to pitch [Azure autoscaling](https://learn.microsoft.com/en-us/azure/architecture/best-practices/auto-scaling) features to keep costs lower while still providing capacity to handle these quarterly spikes. The client appreciated the partner’s considerations of budget while maintaining performance year-round.
## Functional requirements
These questions help identify the project goals, business processes, and required features and functions. Put another way, this is the project scope.
### Project definition
It is crucial to grasp the motivation behind the project and the decisions that led the client to choose a website for the project. If you understand this, you can define the primary focus areas and concerns. This allows you to allocate more resources to the most important areas.
  * What are the project goals?
  * What is the target audience?
  * What does the client need to track to define the success of the project (e.g., key performance indicators like increased traffic, sales, etc.)?
  * What kind of new digital experience do you want to bring to the audience?
  * Is the project a brand new development, a redesign, or a migration?
  * What type of a project is it (e.g., marketing website, corporate website, application, etc.)?
  * Is this a stand alone project or is it part of a larger initiative? How does it fit within a larger initiative?
  * Is the project’s content meant to be mostly static or dynamic?
  * Is content context sensitive (e.g., personalization)?


### Design
A number of factors influence your design decisions. For example, government projects require strict accessibility guidelines to be followed. Other projects may require a heavy focus on mobile technologies. These questions help you find the constraints that drive the decisions in your design process. 
  * Which web standards and accessibility requirements does the project need to support (e.g., HTML5, Section 508)?
  * Are there any particular requirements around the final HTML code that you need to consider?
  * Will the project prioritize mobile devices?
  * Are there existing wireframes or templates that you must consider?
  * Do your specifications require or forbid third-party frameworks (e.g., bootstrap, jQuery)?
  * Are there any specific browsers or browser versions that need to be supported (e.g., older browsers like IE 7)?


### Content and assets
The content and business processes of the project requires are another key focus of this phase. You need to gather as many details as possible about the different types and amounts of content in the project. You also need to understand the details of the processes for adding, editing, and removing content from the site.
#### Content structure
  * What different types of structured content are planned (e.g., news, blogs, events)?
  * What relationships do different content items have? For example, do news items have one or more author, or can editors specify related news items?
  * Do you need categorization and/or tagging of content? How does it need to work?
  * Does the project require a pre-determined structure or sitemap?
  * How many pages will the primary web site channel have? What about pages on microsite channels and headless items?
  * What is the expected content growth for each type of content (month, year, decade)?
  * Will content be reused in multiple areas of one channel, or across multiple channels? If so, how?


#### Content workflow / life-cycle
  * What percentage of the project’s content will be new, compared to imported or migrated?
  * Will there be an approval process for publishing content?
  * Does any content have publishing time frame requirements (e.g., an expiration date)?
  * Should the project remove or archive any content automatically (e.g., should news articles older than one year be archived)?
  * How often do you expect content to be updated?
  * How long is acceptable to cache each content type for better performance? 
  * Where will business users manage/create content?
  * Does content need to be deployed through multiple environments (e.g., staging to production)?


#### Digital assets (files)
  * How many digital assets does the project require? 
  * What types and sizes are these digital assets?
  * Will digital assets require categorization/tagging?
  * Do you need to secure access to any of the digital assets?
  * Will any of the digital assets require an approval process?
  * Will media assets be reused multiple times in one channel, or across multiple channels?
  * Does the project require a CDN for delivery of digital assets?


### Languages
Multilingual websites require more considerations than sites with one language and adding multilingual functionality after the fact can be difficult. As such, you need to understand the current and future needs for the project as they relate to multiple cultures and regions. 
  * Does the project require multiple languages?
  * What is the client’s strategy for creating and displaying language-specific content?
  * Will you have any content that should be shared across multiple languages without being translated? 
  * Does content need to fall back to another language if it isn’t available in another?


### Integrations with other systems
Many projects require integration with third-party components or systems. You should explore these integrations right away, because the type and complexity of integrations can influence a project’s timeline significantly.
  * What do you need to integrate?
  * Do you need to synchronize data? What kind of data?
  * Will synchronization be in one direction or two directions? 
  * Which system will be the _“system of record”_ , or _“source of truth”_ , for the data?
  * What should happen if one system is unavailable? What should when it becomes available after an outage?
  * For what period time can the systems be allowed to be out-of-sync?
  * Does the external system have data throughput or bandwidth limitations?


### General functionality
These questions deal with some larger features and functionality that are native to Xperience and can be implemented any number of ways. 
#### Online marketing & digital experience
  * How does the project fit into your client’s overall digital experience?
  * What is the data retention plan for online marketing data (e.g., visitors and their activities)?
  * What are the email throughput requirements (e.g., how quickly do you need an email with 50K recipients to be sent)?
  * Will marketing emails have strict requirements for delays in email delivery?
  * Do you expect high-traffic events like targeted campaigns to drive significantly more users to the site than normal?
  * What kinds of personalization are needed? 
  * How much flexibility do business users need to manage things like personalization without help from a technical resource?
  * What kind of processes can be automated using marketing automation?
  * How will the marketing data be integrated with a CRM system?


#### Members (live-site users)
  * What types of authentication do you need (e.g., Forms, Facebook, Google)?
  * How many registered users do you expect?
  * Should any website pages or sections be available only for registered members? 


#### Editors and administrators (admin UI users)
  * What types of authentication do you need (e.g. Forms, External provider, Single-Sign-On)?
  * How many content editors and administrators do you expect?
  * What types of user roles and what kinds of permissions do you need?
  * Should any website pages or sections allow only certain roles to edit them?


#### Search
  * What content should be searchable?
  * Do you need to search external sources?
  * How do you need to present search results (ordered, categorized, filtered, etc.)?
  * Are there any special search functions you need (e.g., autocomplete, faceted search, etc.)?
  * Do you prefer a specific search service (e.g., Lucene, Azure AI Search, Algolia)?


## What’s next?
The next guide in this handbook provides an [overview of the features Xperience has to offer](/guides/architecture/xperience-implementation-handbook/examine-xperience-capabilities) and discusses how they contribute to a digital experience that furthers the goals of your client or organization.
![]()
[]()[]()
