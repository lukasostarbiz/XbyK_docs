# Taxonomies for personalization
  * [ Copy page link ](modules/work-with-taxonomies/taxonomies-for-personalization#) | [Get HelpService ID](modules/work-with-taxonomies/taxonomies-for-personalization#)
Core MVC 5


[✖](modules/work-with-taxonomies/taxonomies-for-personalization# "Close page link panel") [Copy to clipboard](modules/work-with-taxonomies/taxonomies-for-personalization#)
You can set up a taxonomy in which each tag represents a different audience type, such as a business user, developer, or administrator. Then, create contact groups that correspond to each persona.
Label your materials based on the intended audience to personalize content for different users. You can link these persona tags to users via contact groups or custom member data fields, which are filled out based on information customers submit during registration.
Here are two ways to filter content based on the visitor’s persona:
#### Personalization Based on Contact groups via widget
Editors will tag content items with the relevant persona to personalize content based on personas. Developers will prepare widgets with properties that include options for selecting default content items, persona tags, and content items specifically for personalization. Then, they will use logic like _ContactIsInContactGroup_ to ensure that content displays according to the visitor’s group membership, allowing for targeted, persona-based content delivery.
#### Personalization based on member data (Behind sign-in)
As part of the registration process, prompt users to provide information like their job title or field of expertise. This data will help determine the specific type of content they see on the site.
In the Page Builder widgets, you can enable editors to add content tailored to each persona based on this information. This setup allows for a more personalized user experience, as each user’s role or field directly influences the materials and resources presented to them, ensuring that they see the content most relevant to their needs.
When setting up page templates for this approach to content personalization, note that template properties apply globally. However, other Page Builder components will only reflect these changes once editors configure the page, so you may need to implement a check to ensure the correct content is displayed based on visitor type.
[ Previous page ](modules/work-with-taxonomies/taxonomy-types)
7 of 8
[ Mark complete and continue ](modules/work-with-taxonomies/conclusion)
  * [Community Questions & Answers](https://community.kentico.com/q-and-a)
  * [Contact support](https://community.kentico.com/support)


### Cookie consent
We use necessary [cookies](https://www.kentico.com/cookies-policy) to run our website and improve your experience while browsing. Additional cookies are only used with your consent. You may revoke your consent on the [Cookies Policy](https://www.kentico.com/cookies-policy) page or in your browser at any time. 
ACCEPT ALL  [Configure](https://www.kentico.com/cookies-policy)
USE ONLY NECESSARY 
![](https://docs.kentico.com/modules/work-with-taxonomies/taxonomies-for-personalization)
[](https://docs.kentico.com/modules/work-with-taxonomies/taxonomies-for-personalization)[](https://docs.kentico.com/modules/work-with-taxonomies/taxonomies-for-personalization)