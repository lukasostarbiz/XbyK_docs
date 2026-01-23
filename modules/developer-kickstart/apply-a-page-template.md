---
source: https://docs.kentico.com/modules/developer-kickstart/apply-a-page-template
scrape_date: 2026-01-22
---

Module: Xperience by Kentico Developer kickstart
8 of 13 Pages
# Apply a page template
Now that the template is set up, you can apply it to new landing pages. Let’s return to the Xperience administration interface and create a home page to try it out!
At this point, you have not yet designated a home page, so you will get an error when you visit the root of the site. However, you can still sign in to the administration interface through the **/admin** path.
From the dashboard, navigate to the **Kickstart pages** channel and create a new page with the following values:
  * **Page name:** Home
  * **Content type:** Landing page


[![Screenshot of the new page dialog for the home page](/docsassets/guides/apply-a-page-template/home-page-create-1.png)](/docsassets/guides/apply-a-page-template/home-page-create-1.png)
Then provide the _Home_ page with a _Slogan_ content item to display. Click **Create new** under **Slogan**.
Define a new slogan with the following properties:
  * **Content item name:** Welcome slogan
  * **Location:** Uncategorized (no folder)
  * **Content type:** Slogan


Click **Continue** , and fill out the following properties:
  * **Message:** Welcome to the Kickstart project!

Your browser does not support the video tag. 
Finally, remember to **_Publish_ both the reusable slogan item and the Home page** that references it.
Your browser does not support the video tag. 
**Reusable content items**
The _Slogan_ content item you just created and published can be reused in other pages and different channels.
For example, if you now create another _Landing page_ , you can reference the _Welcome slogan_ content item by using the **Select existing** option. If you later decide to change the _Slogan_ of the _Welcome slogan_ item, the change will show on both pages because they are both pointing at the same item in the [Content hub](/documentation/business-users/content-hub).
## Designate the home page of the site
Now that the website can display your page, let’s designate it as the home page. This way, if you hit the root domain of the site with no path, the page will display.
Open the **Channel management** application and navigate to **Kickstart pages → Channel settings**.
Enter _/Home_ into the **Home page** setting, and save.
This property takes the **Tree path** of a page. Find this value for any page by visiting it in the _Content tree_ of its channel, choosing **Properties** , and expanding the **Information** section to view the **Tree path** property.
## Check your progress
Your page and content are successfully published.
Try and navigate to the root of your site. You should see the Home page showing our Slogan:
[![Root of the website showing the homepage in the browser.](/docsassets/guides/apply-a-page-template/home-page-display-1.png)](/docsassets/guides/apply-a-page-template/home-page-display-1.png)
When you go back to the administration and navigate to your new **Home** page in the **Kickstart pages** channel and switch to the **Preview** or **Page Builder** tabs, you should be able to see the new item’s message displayed on the page.
[![Home page in Page Builder mode in Kickstart pages channel](/docsassets/guides/apply-a-page-template/home-page-display-2.png)](/docsassets/guides/apply-a-page-template/home-page-display-2.png)
However, you may notice that when you go to edit the page on the **Page Builder** tab and try to **Publish** your changes there, the button does nothing. The Page Builder area you included in the view is also nowhere to be seen. This is because these features rely on _Page Builder scripts_ , which are currently missing.
Keep following along with this series to add a layout view that enables them!
[ Previous page ](/modules/developer-kickstart/build-a-page-template)
8 of 13
[ Mark complete and continue ](/modules/developer-kickstart/create-a-layout-view)
![]()
[]()[]()
