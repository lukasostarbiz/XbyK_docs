# Understand digital marketing channels in Xperience
  * [ Copy page link ](modules/manage-content/digital-marketing-channels#) | [Get HelpService ID](modules/manage-content/digital-marketing-channels#)
Core MVC 5


[✖](modules/manage-content/digital-marketing-channels# "Close page link panel") [Copy to clipboard](modules/manage-content/digital-marketing-channels#)
Digital marketing constantly evolves, and marketers must reach and engage their audiences across different platforms and channels. In this guide, you’ll learn how Xperience understands digital channels, especially website and email channels, and how they fit into the digital experience you can build with Xperience by Kentico DXP.
## What is a marketing channel?
Marketing channels refer to different ways companies and organizations communicate their marketing messages. Some channels, like print or TV, target the **offline**. In contrast, other channels target the **digital world** , such as websites, emails, landing pages, or mobile applications. Marketers can manage digital content through content management systems or digital experience platforms.
Find more general information about the channels in [Let’s explore terms like digital marketing channel, multichannel, multiexperience, and headless](https://www.kentico.com/discover/blog/what-is-a-channel-from-the-context-of-a-dxp) on Kentico blog.
## Channels in Xperience
By channel, Xperience by Kentico DXP understands any **digital platform that a company uses to communicate in the digital world** , including main websites, regional sites, different brand-specific sites, campaign microsites or landing pages, emails, mobile applications, or content fragments distributed, for example, as snippets to third-party websites.
Xperience customers typically maintain different marketing channels, and Xperience provides a _toolset_ to help the marketing team achieve their goals.
Channels are critical components in creating Xperience architecture.
In the past, marketers often managed their digital communication using several different tools. If they wanted to maintain consistency in their messaging, they had to transfer the same content, such as images, testimonials, product marketing copy, or product messages, between different third-party solutions and platforms, typically by copying and pasting. It was not an ideal solution, as it made content curation much more difficult.
[![Channels in Xperience by Kentico](docsassets/guides/digital-marketing-channels/xbyk-channels-content.png)](https://docs.kentico.com/docsassets/guides/digital-marketing-channels/xbyk-channels-content.png)
In the Xperience administration application, marketers manage both content and customer experiences from one platform.
They store the content they plan to reuse across different channels in the [Content hub](guides/digital-marketing/xperience-essentials/manage-content-hub-essentials), a central place for managing digital assets and structured content. The second key area of Xperience, [Contact management](documentation/business-users/digital-marketing/contact-management), provides tools for tracking visitor behavior, creating audience segments, and mapping customer journeys. Together, these two parts form the foundation of the ecosystem: content and customer data are managed in one place, then funneled into various communication channels, such as websites, email campaigns, or mobile applications. The Xperience ecosystem enables marketers to maintain consistent messaging and deliver the right content to the right audience at the right time.
Most typically, marketers will use the data on website and microsite channels in Xperience by Kentico. They also use their content for marketing emails and non-website apps.
Marketers can spin new website channels without asking developers for help, depending on the implementation of their project.
The out-of-the-box headless API is just another type of channel; it is represented by a mobile app.
Marketers or business analysts typically store detailed customer data in software like CRM and synchronize the data over API. They also transfer their marketing data to warehouses for advanced analysis with data visualization tools.
[![Website channels in Kbank demo site](docsassets/guides/digital-marketing-channels/kbank-business-channels.png)](https://docs.kentico.com/docsassets/guides/digital-marketing-channels/kbank-business-channels.png)
## Website channels in Xperience
Marketers use websites to **promote their companies, products, brands, or services or share their latest messages** on websites and microsites. A common challenge marketers face is handling their marketing content across different websites.
With Xperience, marketers can store all their marketing content inside the administration and use it across their websites.
Their editors can work with a familiar UI and have the same tools across different domains. They can use the data they’ve collected about their customers to personalize the website’s content and tailor the digital journey to move customers from one touch point to another, getting them closer to conversion.
### Websites vs. microsites
Xperience differentiates between _website_ and _microsites_ for licensing purposes.
  * **Website** channel represents a typical company website that lives on its dedicated domain or subdomain, such as [kentico.com](https://kentico.com) or [community.kentico.com](https://community.kentico.com). These two represent two separate websites, which means two channels.
  * **Microsites** fall under the new concept of a marketing “microchannel”. A _microchannel_ is a mini-channel that contains only a limited amount of content. A microsite can have a maximum of 20 items (pages with URLs, nav menu structure, folders, etc.) in its content tree [Pages](documentation/business-users/website-content).


Learn more about how website and microsite licensing works in Xperience by visiting the [Licenses](documentation/developers-and-admins/installation/licenses) page, or on the [Partner Portal](https://partner.kentico.com).
## Email channels in Xperience
Marketers use email channels to **engage customers directly in their inboxes**. Emails help **nurture leads, and build lasting relationships**. With Xperience, marketers can design, personalize, and send campaigns directly within the same administration environment. Because their email campaigns share the same data and use the same or similar tools across other channels within one administration, marketers don’t have to juggle multiple platforms or duplicate work. This integrated approach ensures that every message is consistent with the overall customer experience. Marketers can reuse existing content, apply audience segments, and measure results alongside other digital ecosystem activities.
Working in an email channel in Xperience has one requirement though: developers first need to [build email content types and templates to display email data](documentation/developers-and-admins/digital-marketing-setup/email-channel-management) , so the email channel can be accessible to business users. This enables marketers to create emails, personalize their content, and track email performance from within the same administration UI.
### Email channels and microchannels
The number and size of email channels will depend on the licensing tier for email channels, as well as your individual licensing agreement.
However, the general Xperience email channel availability works as follows:
  * **Email microchannels** : Included in all licensing tiers, [email microchannels](documentation/developers-and-admins/digital-marketing-setup/email-channel-management#create-and-configure-email-channels), give marketers the ability to send email blasts directly from the platform. They are limited in the number of email content items, but not in the number of emails that can be sent out.
  * **Standard email channel** : Included in the _Advanced_ licensing tier, [standard-sized email channels](documentation/developers-and-admins/digital-marketing-setup/email-channel-management#create-and-configure-email-channels) come with an unlimited number of email items, allowing organizations to run campaigns across different brands, regions, or business units.


For more information about licensing options, see the [Licenses](documentation/developers-and-admins/installation/licenses) page, or the overview of [Xperience by Kentico license models](https://www.kentico.com/platform/how-to-buy).
## Administration app is not a digital channel
The very first thing a user sees when they sign in to the **Xperience administration** – usually at [(optional)](https://somexperiencedomainurl.local/admin) – is the application dashboard, which might lead one to believe that the administration interface is another channel.
[![admin application](docsassets/guides/digital-marketing-channels/xperience-admin-application.png)](https://docs.kentico.com/docsassets/guides/digital-marketing-channels/xperience-admin-application.png)
The admin application remains the primary environment where marketers and developers manage content, data, and campaigns.
**Logging into the administration does not count as accessing or creating a channel**. Only customer-facing entities, such as websites and email channels, count toward the total number of channels included in your license.
## Summary
In today’s digital era, connecting with audiences across diverse platforms is crucial. With Xperience by Kentico DXP, marketers can manage both websites and email campaigns as channels, making it easier to deliver consistent, personalized experiences and create lasting relationships with their audience. Explore insights into marketing channels on [Kentico’s blog](https://www.kentico.com/discover/blog/what-is-a-channel-from-the-context-of-a-dxp).
[ Previous page ](modules/manage-content)
2 of 19
[ Mark complete and continue ](modules/manage-content/introduction-reusable-content)
  * [Community Questions & Answers](https://community.kentico.com/q-and-a)
  * [Contact support](https://community.kentico.com/support)


### Cookie consent
We use necessary [cookies](https://www.kentico.com/cookies-policy) to run our website and improve your experience while browsing. Additional cookies are only used with your consent. You may revoke your consent on the [Cookies Policy](https://www.kentico.com/cookies-policy) page or in your browser at any time. 
ACCEPT ALL  [Configure](https://www.kentico.com/cookies-policy)
USE ONLY NECESSARY 
![](https://docs.kentico.com/modules/manage-content/digital-marketing-channels)
[](https://docs.kentico.com/modules/manage-content/digital-marketing-channels)[](https://docs.kentico.com/modules/manage-content/digital-marketing-channels)