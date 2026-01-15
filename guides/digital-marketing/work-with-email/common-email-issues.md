# Beginner’s guide to fixing common email marketing pitfalls
  * Concept| [ Copy page link ](guides/digital-marketing/work-with-email/common-email-issues#) | [Get HelpService ID](guides/digital-marketing/work-with-email/common-email-issues#)
Core MVC 5


[✖](guides/digital-marketing/work-with-email/common-email-issues# "Close page link panel") [Copy to clipboard](guides/digital-marketing/work-with-email/common-email-issues#)
Even well-planned email campaigns can run into troubles. Sometimes it’s a dip in engagement, an increase in unsubscribes, or results that just don’t match your effort. That’s where your [**email statistics**](documentation/business-users/digital-marketing/emails/track-email-statistics) come in.
Your email stats are more than just numbers. They’re signals that help you **fine-tune your content, improve targeting, and adjust your timing**. When something’s off, your stats can show you where to look and what to improve.
Let’s look at some of the most common challenges marketers face when they analyze email performance in **Xperience by Kentico** and see what you can do about it.
For each issue, you’ll explore:
  * What should you look for in your stats?
  * What it likely means.
  * What can you do to fix the issue?


Along the way, we’ll offer practical tips to help you create stronger, more relevant email communications. They’ll help you support your marketing goals and allow you to build and retain long-lasting relationships with your users.
## Check your email configuration
Before diving into your stats, make sure everything is set up correctly. If not, the data you’re seeing may be incomplete or misleading.
Prerequisites to tracking accurate email performance in _Xperience:_
  * Only emails sent through a **Emails channel** will generate statistics. Drafts, previews, and test sends won’t.
  * **Email tracking** and **contact activity logging** must be enabled in your system settings. (Ask your administrator or developer to confirm this.)
  * Be aware of the **limitations of open rate tracking** , especially if you’re relying on it to measure success.


## Understand the limitations of open rates
Email open rates used to be a go-to metric for measuring engagement, but that’s no longer the case. Over time, email clients and services have introduced privacy features that either **block open tracking entirely** or **generate false opens** , making the data increasingly unreliable.
These changes were led by Apple’s [**Mail Privacy Protection (MPP)**](https://www.twilio.com/en-us/blog/insights/apple-mail-privacy-protection), but similar behavior is now seen in other inbox tools and apps too.
### See how privacy features affect open tracking
  * Apple Mail preloads email content (including [tracking pixels](https://en.wikipedia.org/wiki/Web_beacon) by default, often registering an “open” even if the user never actually reads the message.
  * Gmail and other clients may block images, including tracking pixels, unless the user enables them manually.
  * Many providers now obfuscate or block IP address data, making it difficult to determine where or when an email was opened.
  * Depending on the provider or device, open tracking may fail silently or produce misleading results.


The result? Open data does not reflect real user behavior. In some cases, open rates are inflated by automated preloading. In others, they’re underreported due to blocked tracking pixels.
### Switch to metrics that matter
Open rates can be tempting to rely on, because they’re one of the first metrics marketers see. But with modern privacy protections affecting tracking accuracy, **open rates are no longer a reliable way to measure engagement**. To truly understand how your audience interacts with your emails, it’s better to focus on metrics that reflect actual user behavior.
  * Shift your focus to metrics that reflect real user interaction, such as: 
    * **Click-through rates** – who’s engaging with your content.
    * **Unsubscribe rates** – who’s opting out.
    * **Bounce rates** – how clean your email list is.
  * If you’re using personalization or automation, pay attention to **contact activity logs** to get a fuller picture of what individuals are doing post-send.


While open rates can still hint at deliverability issues or sudden changes in behavior, they should no longer be your primary measure of success.
## Identify and fix common email issues
Once your tracking is in place and you understand which metrics truly matter, it’s time to **dig into the patterns**. Your email statistics can quickly show where things are going well and where there’s room for improvement. Each section below highlights a familiar problem marketers face, how it shows up in your stats, and practical ways to fix it using tools available in _Xperience by Kentico_.
### 1. Low click-through rate
**Click-through rate (CTR)** shows how many people clicked a link in your email compared to how many received it. It’s one of the most reliable signs of engagement because it reflects action, not just interest. A solid CTR means your message reaches the right people with content that motivates them to explore and engage further.
#### What you’ll see
Few recipients click links in your emails, even if the delivery and open rates look high.
#### What this means
Your content may not motivate users to take the next step. A high delivery with low clicks often signals that people are receiving your emails, but are not interested enough to act. This could be happening due to unclear messaging, weak CTAs, or a mismatch between user expectations and your content.
#### How to respond
  * **Clarify your CTA.** Make sure there’s a single, prominent call-to-action in the email. Tell the reader exactly what to do next and why it matters.
  * **Cut the noise.** Reduce the number of competing links or visual clutter that might distract from your primary goal.
  * **Use personalization.** Tailor the message using personalization features in Xperience. For example, recommending products based on previous behavior, or [tailoring website content](guides/digital-marketing/xperience-essentials/personalize-website-content-essentials) by creating a [personalized variant of a widget](documentation/business-users/digital-marketing/widget-personalization) is an easy, effective win.
  * **Improve subject lines** Sometimes the subject line creates one expectation, but the content doesn’t deliver on it. Try brainstorming different variants with [AIRA](documentation/business-users/aira#rich-text-editor-refinements) to see what aligns better with your audience’s intent.