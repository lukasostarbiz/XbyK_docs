# Personalize widget content
  * [ Copy page link ](modules/advanced-content/personalize-widget-content#) | [Get HelpService ID](modules/advanced-content/personalize-widget-content#)
Core MVC 5


[✖](modules/advanced-content/personalize-widget-content# "Close page link panel") [Copy to clipboard](modules/advanced-content/personalize-widget-content#)
After our changes the widgets are working again. However, we have the opportunity to make them even better.
If we implement [content personalization](documentation/developers-and-admins/digital-marketing-setup/content-personalization), we can allow editors to show different featured articles to visitors who are members of certain contact groups, based on what is more relevant to them.
To give you an idea of what we are implementing, here is what the process of personalizing widgets looks like for editors:
Navigate to the **Contact groups** application in the Xperience admin, and define a new contact group with placeholder values for its name and description.
Now, [define a new personalization condition type](documentation/developers-and-admins/digital-marketing-setup/content-personalization/develop-personalization-condition-types) in the _TrainingGuides.Web/Features/Personalization_ folder that allows editors to select a contact group and evaluates whether the current contact is in that group.
C#
**IsInContactGroupConditionType.cs**
Copy
```
using CMS.ContactManagement;
using CMS.DataEngine;
using TrainingGuides.Web.Features.Personalization;
using Kentico.PageBuilder.Web.Mvc.Personalization;
using Kentico.Xperience.Admin.Base.FormAnnotations;
using Kentico.Xperience.Admin.Base.Forms;

[assembly: RegisterPersonalizationConditionType(
    "TrainingGuides.Web.Features.Personalization",
    typeof(IsInContactGroupConditionType),
    "Is in contact group",
    Description = "Evaluates if the current contact is in one of the contact groups.", IconClass = "icon-app-contact-groups", Hint = "Display to visitors who match at least one of the selected contact groups:")]

namespace TrainingGuides.Web.Features.Personalization;

/// <summary>
/// Personalization condition type based on contact group.
/// </summary>
public class IsInContactGroupConditionType : ConditionType
{
    /// <summary>
    /// Selected contact group code names.
    /// </summary>
    [ObjectSelectorComponent(PredefinedObjectType.CONTACTGROUP,
    Label = "Contact groups",
    Order = 0,
    MaximumItems = 0)]
    public IEnumerable<ObjectRelatedItem> SelectedContactGroups { get; set; } = [];

    /// <summary>
    /// Evaluate condition type.
    /// </summary>
    /// <returns>Returns <c>true</c> if implemented condition is met.</returns>
    public override bool Evaluate()
    {
        var contact = ContactManagementContext.GetCurrentContact();

        if (contact == null)
        {
            return false;
        }

        if (SelectedContactGroups == null || !SelectedContactGroups.Any())
        {
            return contact.ContactGroups.Count == 0;
        }

        return contact.IsInAnyContactGroup(SelectedContactGroups.Select(c => c.ObjectCodeName).ToArray());
    }
}
```

Rebuild the solution and access the page builder tab. Now you can define a different featured article that displays for members of your contact group.
Your browser does not support the video tag. 
[ Previous page ](modules/advanced-content/work-with-reusable-field-schemas)
3 of 11
[ Mark complete and continue ](modules/advanced-content/think-about-filtering-with-taxonomies)
  * [Community Questions & Answers](https://community.kentico.com/q-and-a)
  * [Contact support](https://community.kentico.com/support)


### Cookie consent
We use necessary [cookies](https://www.kentico.com/cookies-policy) to run our website and improve your experience while browsing. Additional cookies are only used with your consent. You may revoke your consent on the [Cookies Policy](https://www.kentico.com/cookies-policy) page or in your browser at any time. 
ACCEPT ALL  [Configure](https://www.kentico.com/cookies-policy)
USE ONLY NECESSARY 
![](https://docs.kentico.com/modules/advanced-content/personalize-widget-content)
[](https://docs.kentico.com/modules/advanced-content/personalize-widget-content)[](https://docs.kentico.com/modules/advanced-content/personalize-widget-content)