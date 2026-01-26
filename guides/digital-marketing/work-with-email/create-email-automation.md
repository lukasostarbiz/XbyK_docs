---
source: https://docs.kentico.com/guides/digital-marketing/work-with-email/create-email-automation
scrape_date: 2026-01-26
---

  * [Home](/guides)
  * [Digital marketing](/guides/digital-marketing)
  * [Work with email](/guides/digital-marketing/work-with-email)
  * Create email automation 


# Create email automation
Email automation helps you respond to your customers’ actions right when they matter most.Instead of manually sending follow-ups, welcome messages, or reminders, you can create **effective, automatic** workflows **triggered by user behavior**.
Reaching the right customer at the right time can make all the difference. But sending every follow-up, welcome message, or reminder manually? Keeping track of who to send what by hand? That’s not scalable, and marketers can easily miss the moment.
That’s where email automation comes in. Xperience’s **Automation** application helps you create simple, effective workflows that run automatically in the background. These workflows are triggered by your customers’ behavior like signing up for a newsletter, registering in your business or reaching a specific milestone on their journey. To maintain momentum in the customer journey, these messages must reach people precisely when they’re most relevant.
In this hands-on material, you’ll learn how to build a simple but powerful email automation using **Kbank instance**.
You’ll need a demo Kbank website to follow along with the exercises. If you don’t have your demo site yet, request the **Xperience by Kentico - Business Tutorial Kbank** demo site through [a form at Kentico.com.](https://www.kentico.com/services/training/training-website-requests)
## Create an automation with a reminder email
Imagine you’re managing email marketing at Kbank. Right now, when visitors submit the Contact Us form, they only receive a quick thank-you message and nothing more.
But you see an opportunity.
To grow your subscriber list, you decide to follow up. If the visitor hasn’t subscribed to your newsletter yet, why not send them a friendly message letting them know it exists—and how to sign up?
Let’s build that flow in Xperience and see how the platform makes it easy to automate. And you don’t even need to write the email yourself. The “Newsletter signup reminder” is already prepared in your Kbank environment, you just connect it to your workflow and you’re ready to go.
### Step 1: Start a new email automation process
  1. Go to the **Automation** application in **Digital marketing**.
  2. Start a **New process**.
  3. Fill in the **Process name** : _Newsletter signup follow-up_.
  4. Leave **Process recurrence** set to _If not already running_ (default).
  5. **Save** the automation process.


### Step 2: Add trigger
Let’s trigger the automation when a contact submits the **Contact Us** form.
  1. In the visual designer, **Add trigger**.
  2. Select **Form submission** activity:
  3. In the _Form settings_ : 
    1. **Trigger name** : _Contact Us form submitted_
    2. **Select a form** : _ContactUsForm_ and **Apply** changes.


### Step 3: Add a Wait step
  1. **Add step** : _Wait_
  2. Update **Wait settings** : 
    1. **Step name** : _Wait_
    2. **Specific interval** : After _1 minute_.
  3. **Apply** the wait step.


In real-life projects, you might want to provide the customers some extra time to react. We’ll add _1 minute_ to give you an opportunity to see how the system actually works using the Kbank demo site.
### Step 4: Add a condition
Now, we’ll check whether the contact has subscribed.
  1. **Add step** : _Condition_
  2. Update **Condition settings** : 
    1. Change the **Step name** : _Is subscribed to newsletter?_
    2. **Add condition** and continue to **Add condition group** : _Contact is in recipient list_
  3. The final condition should look like this: _Contact is receiving in the Newsletter recipient list_
  4. **Apply** your changes.


### Step 5: Send the reminder email
Let’s set up what happens if the user is NOT currently on the newsletter recipient list.
  1. **Add step** in the _NO_ fork of the condition step (meaning the contact has not subscribed to the newsletter yet).
  2. Update **Send email settings** : 
    1. **Step name** : _Send newsletter reminder_
    2. **Choose an email** and continue to **Select existing** : _Newsletter signup reminder_
  3. **Select** and **Apply** your changes.


### Step 6: Finish the process
  1. **Add step** in both the _YES_ fork of the condition step, and after the “Newsletter reminder” step of the _NO_ fork.
  2. **Select step type** : _Finish_
  3. To make the process clear, add two different finish statuses: 
    1. In _YES_ fork, call the _Finish_ step **Is already subscribed**.
    2. In _NO_ fork, call the _Finish_ step **Finish with email sent**
  4. **Apply**.
  5. Your automation process should look similar to the following:


[![Newsletter reminder automation flow](/docsassets/guides/create-email-automation/newsletter-reminder-automation.png)](/docsassets/guides/create-email-automation/newsletter-reminder-automation.png)
### Step 7: Enable the automation
  1. Review settings in the **General** tab if needed.
  2. **Enable** to start the automation.
  3. Check **Statistics** for the individual steps.
  4. In the case of a live website, you’ll see the collected **Contacts** in a dedicated view on the right.


Your automation is now live!
## Test your automation process
Visit your website using an anonymous window and agree with the tracking. Visit the _Contact us_ page on the **Personal Banking** website, and submit the _Contact us_ form using a memorable email address. (Make sure you haven’t used this email address to subscribe to the newsletter in another activity).
Switch to the administration interface. Go to the [Email Queue](/documentation/business-users/email-queue) application and find the email sent to your memorable email address. Then, go to the **Automation** application to your _Newsletter signup follow-up_ process and look at the data.
## What you learned and what’s next
Congrats, you’ve just built your first email automation! This beginner-friendly flow used the following key components:
  * A **trigger** (form submission)
  * A **delay** (wait step)
  * A **condition** (checking subscription status)
  * A **marketing email** in the form of a newsletter reminder (sent only when needed)


You can build on what you learned later with more features, such as split paths, tagging, and other advanced scenarios.
![]()
[]()[]()
