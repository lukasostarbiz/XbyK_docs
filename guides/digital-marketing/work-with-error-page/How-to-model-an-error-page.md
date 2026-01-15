# How to model a 404 page
  * Tutorial| [ Copy page link ](guides/digital-marketing/work-with-error-page/How-to-model-an-error-page#) | [Get HelpService ID](guides/digital-marketing/work-with-error-page/How-to-model-an-error-page#)
Core MVC 5


[✖](guides/digital-marketing/work-with-error-page/How-to-model-an-error-page# "Close page link panel") [Copy to clipboard](guides/digital-marketing/work-with-error-page/How-to-model-an-error-page#)
When you’re exploring the digital world, a 404 error page comes into play if you, the user, try to visit a page that doesn’t exist or has been removed. The 404 is a server response code indicating a user is trying to access an invalid URL. There are many [HTTP status codes](https://www.semrush.com/blog/http-status-codes/) that respond to user requests, each one of them with a different meaning.
When a supposedly functional URL leads a visitor to a page that no longer exists or has been taken down, a well-designed 404 should navigate them back to your website. With Xperience, you can create a 404 page that aligns with your company’s brand design and ensures a positive user experience rather than making the visitors leave confused.
You can check some best practices for the 404 pages [here](guides/digital-marketing/work-with-error-page/404-best-practices).
## Set up a 404 page in Xperience
Let’s see how you can create a 404 page in Xperience by Kentico. We’ll use Kbank’s demo website as an example.
The 404 page you see in the Kbank demo is created to match Kbank guidelines. Your 404 design, the widgets you’ll use, and their configuration will depend on how your Xperience application is implemented.
### Store the error pages in one place
Before you create the page, think about where you want to store it in the content tree. If you plan to create more than one error page, keeping them together in one place is a good idea. This way, you know where to look for them.
As you can see in the example below, there is a folder in the content tree dedicated to  _Special pages_ , with an _Errors_ subfolder.
[![Special pages in Content tree](docsassets/guides/How-to-model-an-error-page/special-pages-content-tree.png)](https://docs.kentico.com/docsassets/guides/How-to-model-an-error-page/special-pages-content-tree.png)
### Create the 404 page
Let’s imagine a scenario where you already have a  _Special pages_ folder in the content tree and want to add a new folder for error pages. You know you plan to create just the 404 page for now, but you may add more error pages in the future.
To create a new folder, select the  _Special pages_ in the content tree. Open the drop-down menu next to the **New page** button. Use the **New folder** button to create an  _Errors_ folder.
[![New folder in the content tree](docsassets/guides/How-to-model-an-error-page/special-pages-new-folder.png)](https://docs.kentico.com/docsassets/guides/How-to-model-an-error-page/special-pages-new-folder.png)
Now select the folder you’ve just created and add a **New page**. Choose the **Error page** type and name it **404**.
[![New error page](docsassets/guides/How-to-model-an-error-page/new-page-error-page.png)](https://docs.kentico.com/docsassets/guides/How-to-model-an-error-page/new-page-error-page.png)
Fill in a **Status code**. It tells Xperience to display this page when the specific server error appears. So, in this scenario, the **Status code** will be _404_.
[![404 status code](docsassets/guides/How-to-model-an-error-page/404-status-code.png)](https://docs.kentico.com/docsassets/guides/How-to-model-an-error-page/404-status-code.png)
Save your changes and switch to the **Page Builder** tab to set up the page content.
The way editors created the 404 in Kbank is just a band-aid. They wanted to save time so they used existing widgets and created a very simple page. For them, it wasn’t worth it to create special designs or custom widgets.
  1. The first banner we use is a **Rich text**. Error pages are a good example of using the _Rich text widget_ with non-reusable content. The error page is one of a kind, and its content is so specific that there is no need to make this content reusable and save it in the _Content hub_. Add the “ _404_ ” header and a paragraph explaining the page was not found.
[![Rich text widget](docsassets/guides/How-to-model-an-error-page/404-rich-text-widget.png)](https://docs.kentico.com/docsassets/guides/How-to-model-an-error-page/404-rich-text-widget.png)
To adjust the widget’s styling, open the _Rich text_ editor, select the **More misc** option on the right, and choose the **Code view**. You can change the background using the HTML code.
[![Rich text editor](docsassets/guides/How-to-model-an-error-page/404-code-view.png)](https://docs.kentico.com/docsassets/guides/How-to-model-an-error-page/404-code-view.png)
[![Rich text editor in code view](docsassets/guides/How-to-model-an-error-page/rich-text-code-view.png)](https://docs.kentico.com/docsassets/guides/How-to-model-an-error-page/rich-text-code-view.png)
  2. The first widget explains to the visitors what happened. The second one helps them return to the  _home page_ and start their journey all over. Use the **CTA** widget, name it “ _Back to home_ ,” and link it to the home page.
[![CTA properties](docsassets/guides/How-to-model-an-error-page/404-CTA-properties.png)](https://docs.kentico.com/docsassets/guides/How-to-model-an-error-page/404-CTA-properties.png)
  3. The last two widgets offer the visitors links to the latest articles. Use the **Page heading** for the section title and the **Article list** widget, which links the _News_ section of the content tree and will display the newest articles.
[![Page heading properties](docsassets/guides/How-to-model-an-error-page/404-page-heading-properties.png)](https://docs.kentico.com/docsassets/guides/How-to-model-an-error-page/404-page-heading-properties.png)


### Use personalization
When you complete the error page, feel free to further refine and enhance the experience. If you [collect information about your visitors](https://docs.xperience.io/xp/business-users/digital-marketing/contact-groups), you can offer them tailored content. Content personalization is a great tool that may help your visitors find what they are looking for and keep them on your website.
Let’s assume that some of the visitors are interested in travel insurance, and you want to guide them on their journey and offer them relevant articles. To do so, you decided to personalize the **Page heading** and **Article list** to catch their interest.
  1. First, add a personalized variant of the widget. You have a contact group gathering visitors who showed interest in anything insurance-related, so you’ll create a new variant of the widget targeting this group.
[![Page heading personalization](docsassets/guides/How-to-model-an-error-page/page-heading-personalization.png)](https://docs.kentico.com/docsassets/guides/How-to-model-an-error-page/page-heading-personalization.png)
[![Page heading personalization properties](docsassets/guides/How-to-model-an-error-page/personalization-properties.png)](https://docs.kentico.com/docsassets/guides/How-to-model-an-error-page/personalization-properties.png)
[![Page heading personalization variant](docsassets/guides/How-to-model-an-error-page/page-heading-new-variant.png)](https://docs.kentico.com/docsassets/guides/How-to-model-an-error-page/page-heading-new-variant.png)
  2. Once the variant is ready, let’s change the heading. **Configure widget** to open the configure dialogue window and adjust the heading. It’s good to include keywords matching the targeted group. You want to engage visitors who showed interest in travel insurance before, so the keyword here will be _insurance_.
[![Page heading configuration](docsassets/guides/How-to-model-an-error-page/page-heading-configure.png)](https://docs.kentico.com/docsassets/guides/How-to-model-an-error-page/page-heading-configure.png)
[![Page heading properties](docsassets/guides/How-to-model-an-error-page/page-heading-properties.png)](https://docs.kentico.com/docsassets/guides/How-to-model-an-error-page/page-heading-properties.png)
  3. Similarly, prepare the personalization of the **Article list** widget.
[![Article list personalization](docsassets/guides/How-to-model-an-error-page/article-list-personalization.png)](https://docs.kentico.com/docsassets/guides/How-to-model-an-error-page/article-list-personalization.png)
Open the  _widget properties_ and select the existing category. Choose the  _Insurance_ category, which filters only the news in this category. In other words, you can say the chosen articles have an _Insurance_ tag.
Kbank uses a content type called _Category_ items to tag content with a specific category. Selecting the _insurance_ category filters all articles editors tagged with the category into the article listing.
[![Article list properties](docsassets/guides/How-to-model-an-error-page/artile-list-properties.png)](https://docs.kentico.com/docsassets/guides/How-to-model-an-error-page/artile-list-properties.png)
  4. The personalization is done. This is what the widgets for the specific contact group look like:
[![Personalized variant of the widgets](docsassets/guides/How-to-model-an-error-page/article-list-personalized.png)](https://docs.kentico.com/docsassets/guides/How-to-model-an-error-page/article-list-personalized.png)


You’re not limited in the number of personalizations, so you can adjust the page for more contact groups.
Watch a short step-by-step guide on creating a personalized 404 page in Xperience by Kentico.
You’ve just seen how you can create and personalize a simple 404 error page in Xperience by Kentico. It’s a good practice to have a 404 page and if you design it well, it can enhance user experience and turn inconvenience into opportunity.