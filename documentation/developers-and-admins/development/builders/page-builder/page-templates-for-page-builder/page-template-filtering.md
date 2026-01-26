---
source: https://docs.kentico.com/documentation/developers-and-admins/development/builders/page-builder/page-templates-for-page-builder/page-template-filtering
scrape_date: 2026-01-26
---

  * [Home](/documentation)
  * [Developers and admins](/documentation/developers-and-admins)
  * [Development](/documentation/developers-and-admins/development)
  * [Builders](/documentation/developers-and-admins/development/builders)
  * [Page Builder](/documentation/developers-and-admins/development/builders/page-builder)
  * [Page templates for Page Builder](/documentation/developers-and-admins/development/builders/page-builder/page-templates-for-page-builder)
  * Page template filtering 


# Page template filtering
When users [create a new page](/documentation/business-users/website-content/create-pages) or change the template of an existing page, the system displays a list of available [page templates](/documentation/developers-and-admins/development/builders/page-builder/page-templates-for-page-builder). By default, the list contains all of the page templates registered for the current content type in the website project.
You can additionally implement page template filtering to control what page templates are available to users to display only templates that are relevant for specific pages.
## Implement page template filters
To create a new page template filter:
  1. Open your project in Visual Studio.
  2. Create a new class that implements the `IPageTemplateFilter` interface (available in the `Kentico.PageBuilder.Web.Mvc.PageTemplates` namespace).
  3. Add a `Filter` method as prescribed by the `IPageTemplateFilter` interface. The method has the following input parameters: 
     * `IEnumerable<PageTemplateDefinition>` – is a collection of all available page templates (after applying previously registered filters). Page templates are represented by _PageTemplateDefinition_ objects from which you can retrieve properties of respective page templates.
     * `PageTemplateFilterContext` – is a context of the page for which the template is being selected. It allows you to access properties that you can use to filter page templates:
       * `LanguageName` – retrieves the culture code of the given page.
       * `ContentTypeName` – retrieves the content type code name for the given page.
       * `ParentWebPageItemId` – retrieves the ID of the parent page to the given page.The `Filter` method needs to return a collection of page templates, which is then passed to other registered filters and eventually to the template selection dialog. You cannot return `NULL` or an empty collection from a filter, as this would invalidate all subsequent filters and no templates will be displayed in the template selection dialog.


It is recommended to divide page templates into mutually exclusive categories by their intended use. You can then create filters, which, according to the context of the current page, either return page templates of the suitable category or all other page templates.
C#
**Example - Spanish page template filter**
Copy
```
using System;
using System.Collections.Generic;
using System.Linq;

using Kentico.PageBuilder.Web.Mvc.PageTemplates;

namespace CompanyName.PageTemplateFilters
{
    public class SpanishPageTemplateFilter : IPageTemplateFilter
    {
        public IEnumerable<PageTemplateDefinition> Filter(IEnumerable<PageTemplateDefinition> pageTemplates, PageTemplateFilterContext context)
        {
            // Applies filtering to a collection of page templates based on the language of the currently edited page
            if (context.LanguageName == "es")
            {
                // Filters the collection to only contain templates for Spanish pages
                return pageTemplates.Where(t => GetSpanishPageTemplates().Contains(t.Identifier));
            }

            // Excludes all Spanish page templates from the collection if the context does not match this filter
            // Assumes that the categories of page templates are mutually exclusive
            return pageTemplates.Where(t => !GetSpanishPageTemplates().Contains(t.Identifier));
        }

        // Gets all page templates that are designed for Spanish pages
        public IEnumerable<string> GetSpanishPageTemplates() => new string[] { "CompanyName.SpanishPageTemplate", "CompanyName.AnotherSpanishPageTemplate" };
    }
}
```

## Register page template filters
Register filters by adding them to the `PageBuilderFilters.PageTemplates` collection [on application start](/documentation/developers-and-admins/customization/run-code-on-application-startup).
C#
**Example - Registering page template filters**
Copy
```
using CMS;
using CMS.Core;
using CMS.DataEngine;

using Kentico.PageBuilder.Web.Mvc;

[assembly: RegisterModule(typeof(PageTemplateFilters))]

public class PageTemplateFilters : Module
{
    // Module class constructor, inherits from the base constructor with the code name of the module as the parameter
    public PageTemplateFilters() : base("PageTemplateFilters")
    {
    }

    // Initializes the module. Called when the application starts.
    protected override void OnInit()
    {
        base.OnInit();

        RegisterPageTemplateFilters();
    }

    private void RegisterPageTemplateFilters()
    {
        PageBuilderFilters.PageTemplates.Add(new SpanishPageTemplateFilter());
        PageBuilderFilters.PageTemplates.Add(new AnotherPageTemplateFilter());
    }
}
```

Note that the filtering is cumulative. The process starts with a collection of all page templates registered in the system. Each subsequently applied filter then works with the collection of templates returned (filtered) by the previous filter.
### Register filters that use dependency injection
Page template filter classes support [dependency injection](/documentation/developers-and-admins/development/website-development-basics/dependency-injection) and their constructor can have parameters (e.g., instances of services registered in the project’s DI container).
Such filters must be registered into the `PageBuilderFilters.PageTemplates` collection using the `Add<FilterClassType>` method:
C#
Copy
```
private void RegisterPageTemplateFilters()
{
    PageBuilderFilters.PageTemplates.Add<SpanishPageTemplateFilter>();
    PageBuilderFilters.PageTemplates.Add<AnotherPageTemplateFilter>();
}
```

![]()
[]()[]()
