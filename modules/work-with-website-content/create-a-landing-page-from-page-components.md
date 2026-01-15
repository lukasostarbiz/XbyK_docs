# Define a landing page from page components
  * [ Copy page link ](modules/work-with-website-content/create-a-landing-page-from-page-components#) | [Get HelpService ID](modules/work-with-website-content/create-a-landing-page-from-page-components#)
Core MVC 5


[✖](modules/work-with-website-content/create-a-landing-page-from-page-components# "Close page link panel") [Copy to clipboard](modules/work-with-website-content/create-a-landing-page-from-page-components#)
A landing page is a page designed for a specific marketing or advertising campaign. It focuses on a single offer or product and has a simple purpose - conversion.
Let’s create a landing page from page components in the Kbank demo site. You’ll create a landing page for a special promotion of personal loans. The targeted group is existing Kbank clients with premium accounts. It offers higher loan limits and lower interest.
**Prerequisites** : you have a Kbank demo site running to follow along with the exercise.
## Create the landing page
### 1. Add the new page to the content tree
Create a new landing page in the _Personal banking_ channel within the _Campaign pages_ folder.
[![Adding a new page in the content tree](docsassets/guides/create-a-landing-page-from-page-components/content-tree.png)](https://docs.kentico.com/docsassets/guides/create-a-landing-page-from-page-components/content-tree.png)
Call it a _Personal loan campaign for existing clients in 2024_. This is an internal name that will not appear publicly or anywhere in the system, so you can follow a specific naming pattern. If you don’t have any naming conventions in your company, be descriptive enough to give your team an idea of what the page is used for. Use the **Content page** type and continue to the next step.
[![Creating new Content page](docsassets/guides/create-a-landing-page-from-page-components/new-page-content-type.png)](https://docs.kentico.com/docsassets/guides/create-a-landing-page-from-page-components/new-page-content-type.png)
### 2. Remove the page from indexing by search engines
Since you are creating the landing page for a selected group of people, you don’t want it to be publicly available or searchable.
Kbank pages have SEO properties that allow editors to influence how the search engines see and treat every page. By unselecting the **SEO Index page** in the _SEO_ part, you make sure the search engines don’t index the page and won’t show it in any search results.
[![Adjusting the SEO settings to remove the page from indexing](docsassets/guides/create-a-landing-page-from-page-components/seo-properties.png)](https://docs.kentico.com/docsassets/guides/create-a-landing-page-from-page-components/seo-properties.png)
Similarly, for the same reason, select the **Exclude content of this page from Rich results on Google** option.
[![Excluding the page from Google search](docsassets/guides/create-a-landing-page-from-page-components/exclude-on-google-option.png)](https://docs.kentico.com/docsassets/guides/create-a-landing-page-from-page-components/exclude-on-google-option.png)
**Save** the changed settings.
## Fill in the content
### 3. Add a Hero banner to the page
In the **Page Builder** , add widgets that you’ll use to populate your page with content. Start with the **Hero banner** at the beginning of the page.
You can see in the screenshot below that the top section of the page allows you to add just the _Hero banner_. Kbank developers set this up to ensure visual consistency on all the pages. Editors can add captivating images to the _Hero banner_ , paired with brief text to capture visitors’ attention and create a positive impression.
[![Adding the hero banner widget to a page](docsassets/guides/create-a-landing-page-from-page-components/add-hero-widget.png)](https://docs.kentico.com/docsassets/guides/create-a-landing-page-from-page-components/add-hero-widget.png)
### 4. Select content to promote
To promote a specific product and populate the widget with content, open the _Hero banner properties_ via the **Configure widget** button. **Select product page** and add the page with the product you want to promote to select the intended product in the content tree.
[![Adding the product to hero banner](docsassets/guides/create-a-landing-page-from-page-components/hero-banner-properties.png)](https://docs.kentico.com/docsassets/guides/create-a-landing-page-from-page-components/hero-banner-properties.png)
In this exercise, we want to promote personal loans. Select the _Loans_ folder in the content tree and the _Personal loan_ product page.
[![Selecting a product page for a Hero banner](docsassets/guides/create-a-landing-page-from-page-components/select-page-from-content-tree.png)](https://docs.kentico.com/docsassets/guides/create-a-landing-page-from-page-components/select-page-from-content-tree.png)
### 5. Make the content stand out
Change the widget design to make the hero area stand out (and distinguish it from the original product page). Switch the text color to **Light** , show the benefits, and display the image **in the background**.
[![Changing a Hero banner design](docsassets/guides/create-a-landing-page-from-page-components/change-hero-banner-design.png)](https://docs.kentico.com/docsassets/guides/create-a-landing-page-from-page-components/change-hero-banner-design.png)
### 6. Tell visitors about the benefits
In the next section, you want to introduce the special offer for the current Kbank customers. You’ll create a separate benefit tailored for the targeted group and link it to the Benefit widget.
The special benefit is meant for this special campaign. Still, you can reuse it on the landing page, in campaign emails, or for any similar campaign in the future.
Let’s create the special benefit first
  1. Create a new item in the **Content hub**.


[![Creating a new benefit content item](docsassets/guides/create-a-landing-page-from-page-components/create-new-content-item.png)](https://docs.kentico.com/docsassets/guides/create-a-landing-page-from-page-components/create-new-content-item.png)
  1. Name it _Personal loan special offer 2024_ to imply this is not a standard benefit and should be used only in this specific campaign. Use the **benefit** content type.
  2. **Describe** the special offer and pick the _Trophy_ icon:


_As our valued client, take advantage of this unique personal loan opportunity!_
_You can now borrow up to $30,000 compared to the standard personal loan. We also give you a friendlier interest rate, lowered by 2%._
  1. **Select** the _Product benefit_ category.


[![Publishing new benefit content item](docsassets/guides/create-a-landing-page-from-page-components/publish-new-benefit.png)](https://docs.kentico.com/docsassets/guides/create-a-landing-page-from-page-components/publish-new-benefit.png)
  1. **Publish** the benefit content item to make it visible on the page. If you don’t publish the content item in the Content Hub, you’ll see the new item in draft status in the Page Builder. You can open the content item, edit dialogue, and publish it directly from here.


Let’s continue with the landing page.
### 7. Promote a special offer
Add a **Page heading** and then **Benefit** widgets into one section.
[![Adding a Page heading widget](docsassets/guides/create-a-landing-page-from-page-components/add-widget.png)](https://docs.kentico.com/docsassets/guides/create-a-landing-page-from-page-components/add-widget.png)
In the **Page heading properties** , choose the **Heading type** _H2_ , **Heading visual type** _Large_ , and add the **Title*:
_We have a special offer for you._
[![Adjusting the Page heading widget properties](docsassets/guides/create-a-landing-page-from-page-components/page-heading-properties.png)](https://docs.kentico.com/docsassets/guides/create-a-landing-page-from-page-components/page-heading-properties.png)
### 8. List special offer benefits
In the **Benefit properties** , select the **Benefit content items** and choose from the existing benefits.
[![Adding specific benefits to Benefit widget](docsassets/guides/create-a-landing-page-from-page-components/benefit-properties.png)](https://docs.kentico.com/docsassets/guides/create-a-landing-page-from-page-components/benefit-properties.png)
Now, find the special benefit you prepared.
[![Selecting special benefit content item](docsassets/guides/create-a-landing-page-from-page-components/select-content-item.png)](https://docs.kentico.com/docsassets/guides/create-a-landing-page-from-page-components/select-content-item.png)
You want your audience to know they don’t have much time to hesitate! The offer is limited and is not coming back so they need to act when there’s still time. Let them know the offer ends in two months.
This information is specific to this campaign. You won’t repeat it anywhere else in your website, so you can use a Rich text widget.
### 9. Provide additional details to the special offer
Add the **Rich text** widget with the following text:
_This offer is available for the next two months from launch, ending on 30 July 2024. Requests received after this date will be processed as a standard personal loan. You can request one or more loans during this period for the total sum of 30.000 USD._
[![Adding text to Rich text widget](docsassets/guides/create-a-landing-page-from-page-components/rich-text-widget.png)](https://docs.kentico.com/docsassets/guides/create-a-landing-page-from-page-components/rich-text-widget.png)
### 10. Lure readers in with a powerful image
To freshen up the page a bit, let’s add an image. Use the **Image** widget, and in the properties **select existing** content hub Asset. Choose the _Two women in front of shopping mall_ image.
[![Adding an asset from Content hub to Image widget](docsassets/guides/create-a-landing-page-from-page-components/image-properties.png)](https://docs.kentico.com/docsassets/guides/create-a-landing-page-from-page-components/image-properties.png)
### 11. Collect visitor’s data with a form
Every landing page should end with a clear call message of what to do next. It can be, for example, a Call to action button or a from.
Let’s complete the page with a **Form** to contact the Kbank loan experts, who will contact the client and finish the loan processing.
For this exercise, create a new form that will group all the specific contacts in a dedicated contact group. Name the form _Personal loan special offer 2024_ and include the following fields:
  * First name (mandatory).
  * Last name (mandatory).
  * Phone.
  * Email.
  * Leave a message.


If you don’t know how, check our guides about how to [create a form](guides/digital-marketing/work-with-forms/create-a-new-form) and a [contact group](guides/digital-marketing/work-with-contacts/create-a-new-contact-group).
Add a **Form column** and the following widgets: the **Page heading** and **Form** widgets.
For the **Page heading** , choose the **Heading type** _h3_ , **Heading visual type** _Large_ , and the **Title** :
_Contact Kbank’s loan experts to get your special offer._
In the **Form** widget, pick the _Personal loan special offer 2024_ form. **Display a message** after submission:
_Thank you! Our loan experts will contact you within the next business day._
## Publish the page
Now you have the landing page ready. You can **publish** the page so it’s available and ready for the campaign.
[![Publishing the page](docsassets/guides/create-a-landing-page-from-page-components/publish-landing-page.png)](https://docs.kentico.com/docsassets/guides/create-a-landing-page-from-page-components/publish-landing-page.png)
If you have followed this exercise, your landing page will look like this:
[![Landing page final look](docsassets/guides/create-a-landing-page-from-page-components/landing-page-final.png)](https://docs.kentico.com/docsassets/guides/create-a-landing-page-from-page-components/landing-page-final.png)
[ Previous page ](modules/work-with-website-content/create-a-new-campaign-page)
7 of 20
[ Mark complete and continue ](modules/work-with-website-content/create-a-reusable-landing-page-template)
  * [Community Questions & Answers](https://community.kentico.com/q-and-a)
  * [Contact support](https://community.kentico.com/support)


### Cookie consent
We use necessary [cookies](https://www.kentico.com/cookies-policy) to run our website and improve your experience while browsing. Additional cookies are only used with your consent. You may revoke your consent on the [Cookies Policy](https://www.kentico.com/cookies-policy) page or in your browser at any time. 
ACCEPT ALL  [Configure](https://www.kentico.com/cookies-policy)
USE ONLY NECESSARY 
![](https://docs.kentico.com/modules/work-with-website-content/create-a-landing-page-from-page-components)
[](https://docs.kentico.com/modules/work-with-website-content/create-a-landing-page-from-page-components)[](https://docs.kentico.com/modules/work-with-website-content/create-a-landing-page-from-page-components)