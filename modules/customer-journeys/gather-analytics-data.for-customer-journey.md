# Gather website and app analytics data
  * [ Copy page link ](modules/customer-journeys/gather-analytics-data.for-customer-journey#) | [Get HelpService ID](modules/customer-journeys/gather-analytics-data.for-customer-journey#)
Core MVC 5


[✖](modules/customer-journeys/gather-analytics-data.for-customer-journey# "Close page link panel") [Copy to clipboard](modules/customer-journeys/gather-analytics-data.for-customer-journey#)
**Advanced license required**   
  
Features described on this page require the Xperience by Kentico **Advanced** license tier. 
To understand your customers’ behavior and motivations, you need to look at how they interact with your business on different outposts, such as websites, apps, or social media pages. Knowing where your audience is, what content resonates, and which digital assets bring you the highest conversion is crucial for optimizing marketing strategies and allocating resources for creating a flawless experience.
Analyze the traffic sources and interactions to determine which channels contribute most to awareness, engagement, and conversions – and prioritize efforts on high-performing sources and improve weaker ones.
## Traffic data – Where are your visitors coming from? What devices do they use?
Each traffic source provides different insights into how users discover your brand and engage with your content. Here’s a breakdown of the most common traffic sources and why they matter in customer journey mapping:
  * **Organic search** – Visitors who find your site through search engines. This indicates how well your SEO strategy works and whether your content matches user intent.
  * **Paid ads** – Visitors from PPC campaigns (Google Ads, social media ads). Tracking these helps measure ROI, refine ad targeting, and optimize outbound campaigns.
  * **Referral traffic** – Visitors who arrive from other websites. Analyzing referral sources can highlight potential partnerships or content collaborations and leverage visitors’ association with a partner or business vertical in content personalization scenarios.
  * **Direct traffic** – Users who type your URL directly into their browser. This often indicates you have repeat visitors and brand-loyal customers.
  * **Social Media** – Visitors from platforms like LinkedIn, Twitter, Facebook, or Instagram. Monitoring social traffic helps assess content engagement and brand awareness.
  * **Desktop vs. mobile usage** – If your analytics disclose that mobile conversion rates are significantly lower than desktop, this could signal mobile-specific usability issues such as slow load times, cluttered layouts, hard-to-tap buttons, or complex forms on smaller screens.
  * **Browser and operation system data** – Users may access your site using different browsers (e.g., Chrome, Safari, Firefox) and operating systems (e.g., iOS, Android, Windows, macOS). If specific browsers or devices show unusually high bounce or error rates, it could indicate compatibility issues such as broken layouts, unsupported features, or slow performance. You might even use this data to temporarily create different customer journeys for users on mobiles and desktop devices before you fix the underlying compatibility issues.
  * **App engagement metrics** – Using data from mobile platforms about installs, active users, session duration, retention rates, and feature usage, offers insights into how valuable and usable the app is to your audience. For example, short session times or high uninstall rates may point to a confusing interface, lack of value, or performance issues.


Analyzing traffic helps you understand where and on what devices customers first interact with your brand and how they start through their journey.
Use the **Xperience Tag Manager** and [connect your Xperience by Kentico application website channels](https://github.com/Kentico/xperience-by-kentico-tag-manager) with different popular tools. You’ll be able to easily add both prebuilt and custom tags to a website channel. The [Xperience Tag Manager](https://github.com/Kentico/xperience-by-kentico-tag-manager) includes built-in support for _Google Tag Manager_ for managing front-end tracking services, _Google Analytics 4_ for basic website analytics, and _Microsoft Clarity_ for session recordings and heatmaps. Additional integrations include _VWO_ for A/B testing, _Intercom Messenger_ for real-time customer communication, and options for inserting custom HTML snippets or pre-defined tags through the _Custom Tag Module_. Each integration includes setup guides and links to the respective service homepages for further information.
By leveraging analytics insights, you can:
  * Allocate budget more effectively – Once you know which sources bring you the most relevant audience, you can invest in improving the weaker ones and leveraging the high-performing ones.
  * Personalize customer experiences – Knowing where customers come from helps you tailor messaging and landing pages to match their expectations. This improves relevance and increases the chances of engagement or conversion.
  * Optimize conversion funnels – Understanding how audiences from different sources engage with your content helps you spot drop-off points and improve the customer journey using targeted campaigns.


## Behavior flow – How Do Users Navigate Your Site or App?
A well-structured behavior flow ensures that users can easily navigate your site or app, find the information they want, and continue progressing through the journey without confusion or friction. When users follow a clear, intuitive path, they’re more likely to stay engaged, complete desired actions, and convert whether purchasing, signing up for a newsletter, or requesting a demo. Once your customers are on your website and take action to learn about your business, customer journeys will help you understand their behavior. You’ll see how users move through your website or app and engage with your content. They’ll help you uncover patterns, potential pain points, and areas for improvement. Using these insights, you can optimize key touchpoints, reduce friction, and create a more intuitive experience.
Here are the key behavior flow indicators and how they help inform your journey mapping. Key insights to gather include:
  * **Landing pages** – These are the entry points into your site or app. They should match the intent of the traffic source and provide a clear next step. High bounce rates often signal a mismatch between user expectations and page content or issues like slow load times, poor design, or irrelevant messaging.
  * **Page paths** – The order in which users move through your site reveals whether they follow your intended journey. If users take unexpected detours, loop between pages, or drop from the flow in unexpected places, it could indicate a lack of guidance toward conversion points.
  * **Time spent on pages** – Combining data collected by Xperience by Kentico with data from your website analytics tools, such as _Google Analytics_ , _Matomo_ , _Clarity_ , _HotJar_ , and others, will indicate user interest. Short dwell times indicate users didn’t find what they were looking for. Unusually long times spent before navigating to another asset, such as landing pages, pricing, or checkout pages, may suggest confusion or hesitation due to unclear information or too many steps.
  * **Drop-off points** – The last activities users perform before leaving your site help you identify issues. Recognize common exit pages or activities that lead to exit will reveal friction points and opportunities for improvement. For example, a high drop-off rate on a form might suggest that the form is too long, unclear, or not compelling enough.


## Conversion points – What Actions Are Users Taking?
Conversion tracking shows how effectively your website or app moves users toward meaningful actions such as becoming a lead, making a purchase, or engaging with content. These conversion points will be represented by the stages of the customer journey. Analyzing them helps you understand where your touchpoints are working well and where improvements are needed.
Tracking conversions provides a clearer picture of how users interact with your site beyond just page views. When mapped against other customer behavior, these metrics help identify which stages of the customer journey successfully drive action and in which stages customers hesitate or even drop-off.
Before your start building customer journeys, collect data for the following conversion-related metrics:
  * **Sign-ups and form submissions** – Forms include [newsletter sign-ups](documentation/developers-and-admins/development/builders/email-builder), demo requests or booking calls through [forms](documentation/developers-and-admins/development/builders/form-builder), [account creation](documentation/business-users/members), or [lead capture](guides/digital-marketing/work-with-contacts). These are important indicators of user interest and intent. _Low submission rates_ may point to friction – such as forms being too long, asking for unnecessary information, or lacking a compelling reason to convert. _High abandonment_ can also signal trust issues, especially if you request personal data and don’t provide sufficient context or reassurance.
  * **Call-to-action (CTA) performance** – CTAs guide users toward a desired action, such as _Download now_ , _Book a demo_ , or _Start free trial_. Tracking CTA clicks and conversion rates help determine whether users are compelled to act. Low-performing CTAs may suffer from vague wording, poor placement, or visual design that fails to attract attention.
  * **Goal completions** – Custom goals (e.g., watching a video or downloading a guide) help assess whether users progress through the intended journey.
  * **Commerce transactions** – If you can enrich data in Xperience by Kentico with data about customer transactions, their purchases, cart additions, and checkout completions from [integrations with Kentico 13](https://github.com/Kentico/xperience-by-kentico-k13ecommerce) or [third-party commerce solution](https://github.com/Kentico/xperience-by-kentico-shopify), for example, using [Xperience API](api/digital-marketing/contacts) from your CRM, customer journeys will help you streamline the checkout process, provide multiple payment options, and include reassurance elements to build trust.


Xperience by Kentico includes [built-in contact activity tracking](documentation/business-users/digital-marketing/contact-activities) for common interactions such as page visits, form submissions, and email clicks. However, every business has its own unique set of customer interactions that may not be captured by default. If there are specific activities you want to track like product views, feature usage, or custom engagement you can use [custom activities](documentation/developers-and-admins/digital-marketing-setup/set-up-activities/custom-activities). Work with your development team to implement [automatic logging for these events](guides/development/activities-and-marketing/log-custom-activities), ensuring you capture the data most relevant to your customer journey.
Conversion data viewed alongside behavior flow, traffic sources, and your audience will give your team a complete picture of how users move through the customer journey.
It helps you understand:
  * Which actions users are willing to take.
  * Where friction prevents progress.
  * What content, design, or messages increase conversions.


When you back these insights with data and tie them to specific stages in the customer journey, your team will immediately see quick wins that bring immediate conversions or what they need to optimize to improve engagement and business outcomes.
[ Previous page ](modules/customer-journeys/prepare-to-build-customer-journey)
13 of 17
[ Mark complete and continue ](modules/customer-journeys/identify-customer-segments-for-customer-journey)
  * [Community Questions & Answers](https://community.kentico.com/q-and-a)
  * [Contact support](https://community.kentico.com/support)


### Cookie consent
We use necessary [cookies](https://www.kentico.com/cookies-policy) to run our website and improve your experience while browsing. Additional cookies are only used with your consent. You may revoke your consent on the [Cookies Policy](https://www.kentico.com/cookies-policy) page or in your browser at any time. 
ACCEPT ALL  [Configure](https://www.kentico.com/cookies-policy)
USE ONLY NECESSARY 
![](https://docs.kentico.com/modules/customer-journeys/gather-analytics-data.for-customer-journey)
[](https://docs.kentico.com/modules/customer-journeys/gather-analytics-data.for-customer-journey)[](https://docs.kentico.com/modules/customer-journeys/gather-analytics-data.for-customer-journey)