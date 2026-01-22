---
source: https://docs.kentico.com/modules/understand-emails/high-bounce-rate
scrape_date: 2026-01-22
---

Module: Understand emails in Xperience
25 of 31 Pages
# High bounce rate
A **“bounce”** happens when an email can’t be delivered to the recipient. There are two types:
  * **soft bounces** – temporary issues like full inboxes,
  * and **hard bounces** – invalid or non-existent addresses.


A high bounce rate lowers your sender reputation, making it **harder for your emails to reach inboxes**.
#### What you’ll see
A significant portion of your emails are undelivered. In your email stats, these appear as “bounced” messages.
#### What this means
Bounces usually happen when emails are sent to invalid, mistyped, or outdated addresses. They can also occur when recipient mailboxes are temporarily unavailable. If you use older contact data or haven’t cleaned your list recently, bounce rates may increase.
Xperience automatically [tracks bounced messages](/documentation/business-users/digital-marketing/emails/send-regular-emails-to-subscribers#bounced-email-tracking) and flags affected contacts. After repeated soft bounces or a confirmed hard bounce, the system switches the contact to a **Bounced** status. It then **excludes the bounced contacts from future sends** , thus **protecting your list quality and sender reputation**.
Bounce tracking must be configured for your project. If you’re not seeing bounce data, ask your developer to check if everything is set up correctly.
You can review a contact’s bounce status in:
  * **Contact management** (contact list or profile)
  * **Recipient lists** (_Overview_ and _Recipients_ tabs)


#### How to respond
  * **Clean your list regularly.** Remove email addresses that generate hard bounces. This simple action helps protect your sender reputation and improves overall deliverability.
  * **Use double opt-in.** Xperience by Kentico uses double opt-in by default, meaning recipients confirm their email address before receiving marketing emails. This helps ensure valid addresses and explicit consent from recipients. While developers can override this behavior (such as when importing contacts or syncing from a CRM), the default setup supports list quality and compliance out of the box.
  * **Watch bounce patterns over time.** A sudden spike may indicate a technical issue or a problematic list source. Monitoring [email delivery trends](/documentation/business-users/digital-marketing/emails/track-email-statistics#delivered-emails) in Xperience can help you catch problems early.


[ Previous page ](/modules/understand-emails/high-unsubscribe-rate)
25 of 31
[ Mark complete and continue ](/modules/understand-emails/spam-complaints)
![]()
[]()[]()
