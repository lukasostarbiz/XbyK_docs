# Create a newsletter email and template in Email Builder
  * How-to| [ Copy page link ](guides/digital-marketing/work-with-email/create-newsletter-email-and-template-in-Email-Builder#) | [Get HelpService ID](guides/digital-marketing/work-with-email/create-newsletter-email-and-template-in-Email-Builder#)
Core MVC 5


[✖](guides/digital-marketing/work-with-email/create-newsletter-email-and-template-in-Email-Builder# "Close page link panel") [Copy to clipboard](guides/digital-marketing/work-with-email/create-newsletter-email-and-template-in-Email-Builder#)
Creating marketing emails often feels more complicated than it should be. You have to worry about HTML quirks, ensuring emails display correctly across all kinds of inboxes, devices, and operating systems. It’s a lot, especially if you always need a developer’s help.
That’s why Xperience includes the **Email Builder** : a no-code email editing tool that helps you create professional, responsive emails without writing a single line of code. Developers create the reusable building blocks called components once, and from then on, you can simply **drag and drop** them into place.
The result? You can create your emails faster. It’s easy to use and everybody who knows [Page Builder](documentation/developers-and-admins/development/builders/page-builder), so you don’t run into bottlenecks. And since the Email Builder aims to build once, reuse everywhere, it asks for less back-and-forth with the dev team.
## Email Builder components
As an editor, you don’t need to write any code. Developers prepare building blocks like **templates, sections, and widgets** to put your emails together piece by piece.
It’s as easy as building castles out of Lego bricks: choose a template, drop in content blocks, and create an email to share your big, bold marketing message.
Here’s a quick look at the building blocks you’ll use:
  * **Sections** Sections divide the layout of your email into logical parts. Each section can hold one or more widgets in one or more email widget _zones_. For example, you might add a section for your header (and header image), another for the main content, and a final one for the footer.
[![Sections in Email Builder](docsassets/guides/create-newsletter-email-and-template-in-Email-Builder/email-builder-sections.png)](https://docs.kentico.com/docsassets/guides/create-newsletter-email-and-template-in-Email-Builder/email-builder-sections.png)
  * **Widgets**  
Widgets are content blocks you drag into your email, such as text areas, images, buttons, and more. You can mix and match them to build your content.


[![Widgets in Email Builder](docsassets/guides/create-newsletter-email-and-template-in-Email-Builder/email-builder-widgets.png)](https://docs.kentico.com/docsassets/guides/create-newsletter-email-and-template-in-Email-Builder/email-builder-widgets.png)
  * **Templates**  
Templates allow you to save the structure, layout, and other email configurations of your email to reuse it for other emails as many times as you need. You can create and save a **template preset** that suits your campaign, then adjust the layout and content to match your message.


[![Preset template](docsassets/guides/create-newsletter-email-and-template-in-Email-Builder/choose-preset-template.png)](https://docs.kentico.com/docsassets/guides/create-newsletter-email-and-template-in-Email-Builder/choose-preset-template.png)
Now, let’s put all this theory into practice.
## Create your first marketing newsletter
Imagine you’re creating a simple newsletter email for Kbank’s upcoming summer event—a fun-filled gathering for colleagues to connect, relax, and celebrate the season. The email should capture the event’s playful tone while remaining clean and professional.
Before you can start sending newsletters, your team needs to set up a way how your users subscribe to your newsletters. If you want to get a feel for what setting it up looks like, the following video walks you through adding subscription form and additional subscription pages.
Are you ready?
You’ll use **Email Builder** to structure and style the message. If you’re excited to put your very own newsletter creation skills to the test, then let’s jump right in!
### Step 1: Start a new email
  1. Go to the **Marketing emails** channel and create **New email**.
  2. Fill in the **Email name** : _Kbank summer bash_
  3. Select: 
    1. **Email purpose** : _Regular_
    2. **Content type** : _Simple email_
    3. **Continue** to save your choices.
  4. Select **Email template** : [_MJML Starter Kit template_](guides/development/email-marketing/use-email-builder-starter-kit) and **Create** your new email.


### Step 2: Add sections and widgets
  1. New email draft opens in the **Content** tab by default. Fill in the email metadata: 
    1. **Subject** : _Kbank Summer Bash Is Here!_
    2. **Preview text** : _Get your goodies at Kbank’s big summer bash_
    3. **Save** your changes and switch to the **Email Builder** view.
  2. In the **Email Builder** , you’ll see a blank layout with an empty section already in place.
  3. Use the **“+”** (**Add section**) gray button on the left side to create the layout of the email. Let’s add three more **Full-width sections** - one for the **header image** , one for the **email headline and first call to action** , one section for the **main content** , and one for the **CTA button** , which can guide users to your website, landing page, or special offers.
  4. Add widgets to your sections: 
    1. Into the top section, add **Image** widget for the company logo. 
      1. In widget properties, **Select existing content item**.
      2. Switch to the _Brand Assets_ workspace, add the _Kbank logo_ image, **Select** , and then **Apply** to save the asset.
    2. In the section section, add your email headline and content using the **Text** widgets: 
      1. **Headline** : _Get ready for a season full of sunshine, celebration, and good vibes_
      2. Set the headline to _Heading 1._
      3. Add a **Button** widget to the section, and adjust the properties:
      4. **Button text** : _Kbank summer_
      5. **URL** : _https://www.kentico.com_
      6. Set the horizontal alignment to _HorizontalAlignment.Center_.
    3. Add **Text** widget to add the email body: _Get ready for a season full of sunshine, celebration, and good vibes_ _Hi, Kbank fans!_ _This is your chance to connect with colleagues, enjoy fun activities, and unwind in true summer style. Whether you’re here for the food, the games, or just to soak up the atmosphere, there’s something for everyone. Expect music, laughter, and maybe even a shark or two along the way. This placeholder text is here to give you a preview of how your email content will look once finalized. Stay tuned for more details and baby sharks._ _Summer has officially arrived at Kbank!_
    4. Copy the _Kbank summer_ Button widget the last section, and adjust the properties: 
      1. **Button text** : _Get your goodies here_
      2. **URL** : _https://www.kentico.com_
      3. Set the horizontal alignment to _HorizontalAlignment.Center_.


Keep the number of sections to a minimum to avoid overwhelming your layout. Keep your content clear and focused. Short paragraphs and simple but eye-catching headlines work best for email.
**Tip:** Try using the _Text_ widget’s properties to, for example, change the headline to _Heading 1_.
### Step 3: Improve the email
Let’s see if you’ve grasped the basics of creating email with _Email Builder_. Here’s a challenge:
**Can you turn your email to look like the following?**
[![Sample email exercise](docsassets/guides/create-newsletter-email-and-template-in-Email-Builder/email-builder-kbank-newsletter-exercise.png)](https://docs.kentico.com/docsassets/guides/create-newsletter-email-and-template-in-Email-Builder/email-builder-kbank-newsletter-exercise.png)
Use fewer sections, **Text** , **Button** , and **Divider** widgets in the email body. Set the logo width to 250.
**A note on subject lines** Before you send your email, be sure to write a strong subject line. It’s the first thing your readers will see and can make a big difference. Stay within your brand guidelines and:
  * Be clear and specific.
  * Add a sense of urgency or curiosity.
  * Keep it under 50 characters if possible.


### Step 4: Preview your email
  1. Select **Preview** tab on the right to see how your email will look on **Desktop** and **Mobile**.
  2. In the **Inbox** tab, preview the _From_ , _Subject_ and _Preview text_ fields.
  3. Now is a good moment to double-check: 
    1. Any spelling and grammar errors? Off-brand communication style?
    2. Is the image loading correctly for different devices?
    3. Do button links correctly work?


### Step 5: Schedule or send your email
  1. In the **Properties** tab, define **Sender and recipients** : 
    1. **Sender** : _Kbank team_
    2. **Send to** : _Newsletter recipient list_
    3. **Save**
  2. **Review and send**
    1. Either select **Schedule** to set a send time.
    2. Or **Send now** to deliver your email immediately.


_Marketing Emails_ channel in Kbank is not connected to any SMTP server, the emails are not sent, and that’s why you won’t be able to view the email statistics.
## Turn your newsletter into a reusable template
Now that you’ve sent your first newsletter, you might be thinking, “I’ll need to send something like this again.” Chances are that you’re right, and the good news is that you don’t need to start from scratch next time.
In this section, you’ll learn how to save your newsletter as a template so you can reuse the layout and structure for future emails.
## Why save it as a template?
Templates help you:
  * Save time by reusing the same structure.
  * Keep your emails consistent in layout and branding.
  * Avoid repetitive work like adding the logo or footer every time.


If your emails usually follow a similar pattern, templates are your best friend.
## Save your newsletter email as a preset template
Let’s turn your one-off newsletter email into a handy template you can use again and again.
### Step 1: Find your sent newsletter
  1. Go to the **Marketing emails** app.
  2. Find the _Kbank summer bash_ newsletter email in **List of emails**.


### Step 2: Save the email as a template
  1. Open the email, and switch from the **Content** tab to the **Email Builder** tab. You can save the email as a preset only from there.
  2. Navigate to the **Send draft** button and choose _Save as preset template_ from the menu.
  3. Fill in preset template properties: 
    1. **Name** : _Kbank quarterly bash newsletter_
    2. **Description** : _Kbank quarterly bash newsletter - to be sent out to Kbank coffee-drinkers club members each quarter._
    3. **Icon** : _cup_
  4. **Save** the template.


This template is now available to use the next time you create an email. You can verify it’s availability in the **Preset templates** app.
### Step 3: Build a clean, reusable layout
Let’s create a new version of the newsletter, this time with a reusable layout. You’ll build it like before but with more flexibility in mind.
  1. Create a new email the same way you did in the first part of this guide, but this time, focus on layout rather than content.
  2. Add these building blocks: 
    1. **Image** widget for company logo
    2. Placeholder text areas: 
      1. **Text widget** for headline: _Your headline here_
      2. **Text widget** for email content: _Your email content here_
      3. A footer section - add the **Product** widget to include a link to a product page from the page tree, and include a CTA button placeholder: 
        1. **Product page** : _Personal banking_ -> _Current account_
        2. **Product page button text** : _Your CTA text here_
  3. **Apply** changes and **Save** them.
  4. When you **Save as preset template** , it should look similar to the following:


[![Saved reusable email preset](docsassets/guides/create-newsletter-email-and-template-in-Email-Builder/reusable-layout-preset.png)](https://docs.kentico.com/docsassets/guides/create-newsletter-email-and-template-in-Email-Builder/reusable-layout-preset.png)
### Step 4: Save your layout as a template
  1. Use the **Save as template** button once your layout is ready.
  2. Choose a clear name, like **Newsletter with logo and footer**.
  3. Optionally, add a description to remind others what it’s for.


### Step 5: Use your new template
Next time you create an email, why not use the template you saved?
Here’s the simplified flow:
  1. **Create new** in the **Marketing emails** app.
  2. **Email name** : _Kbank winter bash_
  3. **Email purpose** : _Regular_
  4. **Content type** : _Simple email_
  5. **Continue** to choose **Email template**.
  6. In _Preset_ templates, find _Newsletter with logo and footer_.
  7. **Create** a new email from the preset template you created.


Now, you can jump straight into building strong content without worrying about the layout each time, and let preset templates help you scale your email marketing while staying on-brand.
## What you learned and what’s next
First, you’ve learned the basic components of the _Email Builder_ and got a hands-on demonstration of how easy it is to create and send a newsletter email with these building blocks.
In the second part of this guide, you built on this knowledge and created your own email template to simplify your workflow and speed up email creation in the _Marketing Emails_ channel.