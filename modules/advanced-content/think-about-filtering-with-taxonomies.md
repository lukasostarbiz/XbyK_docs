# Think about filtering with taxonomies
  * [ Copy page link ](modules/advanced-content/think-about-filtering-with-taxonomies#) | [Get HelpService ID](modules/advanced-content/think-about-filtering-with-taxonomies#)
Core MVC 5


[✖](modules/advanced-content/think-about-filtering-with-taxonomies# "Close page link panel") [Copy to clipboard](modules/advanced-content/think-about-filtering-with-taxonomies#)
[Taxonomies](documentation/developers-and-admins/configuration/taxonomies) in Xperience by Kentico provide a powerful tool for your customers to categorize and organize content.
Each taxonomy is a _group of related tags_ (e.g., PC features: external GPU, disc drive, tower size…). Content editors assign these tags to content items with a field allowing the given taxonomy. Then they can, for example, configure a widget to display a list of content items with a specific set of tags.
Let’s empower your content editors to do just that - filter content items by taxonomy tags in a widget listing.
In the process, we’ll cover how you can easily query items of different content types as long as they share a reusable field schema. Let’s dive in!
## Consider the scenario
Earlier in this series, we explored reusable field schemas through the lens of content model evolution. You have adjusted your project to work with [two new Article content types](modules/advanced-content/work-with-reusable-field-schemas#consider-the-scenario) that share an _Article schema_ , alongside with a deprecated one.
Perhaps you recall that when you added the _Article list_ widget to your _Home_ page to check your progress and selected a parent page from the content tree, the widget rendered articles of all content types, both old and new.
Now imagine that your customer’s content editors want more control. They’re asking for the ability to filter articles in the widget by specific tags, so they can display only the articles that match the categories they’re interested in.
Your browser does not support the video tag. 
## Examine the current state
Take a look at the _Article schema_ in Xperience administration. Notice the _ArticleSchemaCategory_ field with the **Taxonomy** data type. This field allows editors to assign one or more tags from the _Article category_ taxonomy to any content item implementing this schema.
[![Taxonomy field in the Article schema in Xperience administration](docsassets/guides/filter-content-based-on-taxonomies/taxonomy-field.png)](https://docs.kentico.com/docsassets/guides/filter-content-based-on-taxonomies/taxonomy-field.png)
The _Article category_ taxonomy already exists in our project and includes two tags: **Animals** and **Plants**. You can find it in the **Taxonomies** application under the **Configuration** category.
[![Article category taxonomy in the administration interface](docsassets/guides/filter-content-based-on-taxonomies/article-category-taxonomy.png)](https://docs.kentico.com/docsassets/guides/filter-content-based-on-taxonomies/article-category-taxonomy.png)
When you examine any of the _Article (general)_ or _Article (interview)_ items in the **Content hub** , you’ll see they all have tags or an option to add tags in the **Category** field.
[ Previous page ](modules/advanced-content/personalize-widget-content)
4 of 11
[ Mark complete and continue ](modules/advanced-content/filter-content-with-taxonomies)
  * [Community Questions & Answers](https://community.kentico.com/q-and-a)
  * [Contact support](https://community.kentico.com/support)


### Cookie consent
We use necessary [cookies](https://www.kentico.com/cookies-policy) to run our website and improve your experience while browsing. Additional cookies are only used with your consent. You may revoke your consent on the [Cookies Policy](https://www.kentico.com/cookies-policy) page or in your browser at any time. 
ACCEPT ALL  [Configure](https://www.kentico.com/cookies-policy)
USE ONLY NECESSARY 
![](https://docs.kentico.com/modules/advanced-content/think-about-filtering-with-taxonomies)
[](https://docs.kentico.com/modules/advanced-content/think-about-filtering-with-taxonomies)[](https://docs.kentico.com/modules/advanced-content/think-about-filtering-with-taxonomies)