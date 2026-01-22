---
source: https://docs.kentico.com/modules/developer-kickstart/create-a-layout-view
scrape_date: 2026-01-22
---

Module: Xperience by Kentico Developer kickstart
9 of 13 Pages
# Create a layout view
Your project now has a _Home_ page. One last missing piece to set it up correctly is adding Page Builder scripts and styles.
Using [layout views](https://learn.microsoft.com/en-us/aspnet/core/mvc/views/layout?view=aspnetcore-8.0) in MVC is a standard practice. They wrap the content of specialized views and often include shared features, like navigation and frequently used styles and scripts.
Our layout will render the missing Page Builder scripts and styles from the sections included in the template. We will also add Bootstrap styles to our project to give it a nicer look.
## Add the Layout view
Create a new _~/Views/Shared_ directory in the root of your _Kickstart.Web_ project. Inside the directory, add a new view called __Layout.cshtml_.
Include a title, heading, and some simple text to make the layout view visually apparent while testing.
Call `@RenderBody()` inside the `body` tag to indicate where the Razor Engine should render the content of the views.
cshtml
**_Layout.cshtml**
Copy
```
<!DOCTYPE html>
<html>
<head id="head">
    <meta name="viewport" content="width=device-width, initial-scale=1" />
    <meta charset="UTF-8" />
    <meta data-hid="mobile-web-app-capable" name="mobile-web-app-capable" content="yes">
    <meta data-hid="author" name="author" content="Kentico software s.r.o.">

    <title>Xperience Kickstart</title>
</head>

<body>
    <div>
        <h2>Xperience Kickstart</h2>
    </div>
    <div>
        @RenderBody()
    </div>
    <div>
        <p>Find more information in the <a href="https://docs.kentico.com/guides/development" target="_blank">Xperience by Kentico guides</a>.</p>
    </div>
</body>

</html>
```

## Insert Page Builder styles and scripts
Add the `page-builder-styles` tag helper in the `head` tag to insert the styles required for Page Builder.
cshtml
**_Layout.cshtml**
Copy
```
...
    <!-- add Page Builder styles -->
    <page-builder-styles />
    <title>Xperience Kickstart</title>
</head>
...
```

Use the `page-builder-scripts` tag helper to render scripts required for Page Builder below the page content.
cshtml
**_Layout.cshtml**
Copy
```
...
<body>
    ...
    <div>
        @RenderBody()
    </div>
    <div>
        <p>Find more information in the <a href="https://docs.kentico.com/guides/development" target="_blank">Xperience by Kentico guides</a>.</p>
    </div>
    <!-- add Page Builder scripts -->
    <page-builder-scripts />
</body>
...
```

It is important to place the scripts after the page content. Otherwise, the content may not display until after the scripts finish loading.
## Set up templates to use the layout
If you run your website now, you’ll see it still only shows your slogan message.
Since the new layout view lives in a directory different from the _Landing page template_ , the system will **not** use it automatically. Let’s rectify this with a _ViewStart_ file.
_ViewStart_ files specify code that runs at the start of every razor view’s execution.
Like the _ViewImports_ file, _ViewStart_ files apply only to the folder where they are located and its subfolders. We recommend putting it in the root of the site, and overriding it for specific subdirectories where necessary.
You can use the override mechanism to specify a different layout view for specific subfolders.
In the root of the  _Kickstart.Web_ project, add an empty Razor view called __ViewStart.cshtml_.
Tell the system to use the __Layout_ view as a layout for all your project’s views.
cshtml
**_ViewStart.cshtml**
Copy
```
@{
    Layout = "_Layout";
}
```

**Layout file path**
Because the layout is in the _Views/Shared_ folder, where MVC automatically looks, you don’t need to provide a full path.
If you store your layout elsewhere, you have to specify its path, using ~ to represent the root of the project. For example, `~/CustomDirectory/CustomViewsFolder/Shared/_Layout.cshtml`.
## Check your progress
Run the site locally and visit the _/Home_ path to see your new layout view **and** the message from the reusable item rendered to the page.
[![Home page using the new layout view](/docsassets/guides/create-a-layout-view/home-page-w-layout.png)](/docsassets/guides/create-a-layout-view/home-page-w-layout.png)
To verify that your Page Builder scripts and styles work, navigate to your **Home** page in the **Kickstart pages** channel in the administration UI.
Now, when you switch to the **Page Builder** tab and click **Edit page** , you should see your Page Builder area from the [Landing page template](/modules/developer-kickstart/build-a-page-template#add-the-page-template-view). The **Publish** button in the top-right corner should now be active and working.
[![Editable area and a functioning Publish button in the Home page](/docsassets/guides/create-a-layout-view/page-builder-working.png)](/docsassets/guides/create-a-layout-view/page-builder-working.png)
Go ahead and add some more content to the page. Click the plus button in the middle to add a [Rich text](/documentation/business-users/rich-text-editor) widget. You can insert some Lorem ipsum text and and a simple table, like in our example.
Your browser does not support the video tag. 
As opposed to the _Welcome slogan_ reusable content item stored in the [Content hub](/documentation/business-users/content-hub), this content is **unstructured** , lives only within the _Home page_ , and cannot be referenced from elsewhere.
## Add styles to your website
Let’s make your website look nicer with some styles.
This step is optional. We will use [Bootstrap](https://getbootstrap.com/), but feel free to use any styles you like.
You can [host Bootstrap files locally](https://getbootstrap.com/docs/5.3/getting-started/download/) or point to an online resource by adding a `link` tag to your __Layout.cshtml_ file:
cshtml
**_Layout.cshtml**
Copy
```
...
    <page-builder-styles />
    <!-- add bootstrap as an online resource -->
    <link href="https://cdn.jsdelivr.net/npm/bootstrap@5.3.3/dist/css/bootstrap.min.css" rel="stylesheet"
        integrity="sha384-QWTKZyjpPEjISv5WaRU9OFeRpok6YctnYmDr5pNlyT2bRjXh0JMhjY6hW+ALEwIH" crossorigin="anonymous">

    <title>Xperience Kickstart</title>
</head>
...
```

Now you can use Bootstrap classes to give your layout some shape and color, for example:
cshtml
**_Layout.cshtml**
Copy
```
...
<body>
    <div class="navbar navbar-primary navbar-expand-sm navbar-dark bg-primary p-3">
        <div class="navbar-brand">
            <h2>Xperience Kickstart</h2>
        </div>
    </div>
    <div class="p-3">
        @RenderBody()
    </div>
    <div class="border-top p-3">
        <p>Find more information in the <a href="https://docs.kentico.com/guides/development" target="_blank">Xperience
                by Kentico
                guides</a>.</p>
    </div>
    <page-builder-scripts />
</body>
</html>
```

[![Your website with styling applied](/docsassets/guides/create-a-layout-view/layout-w-bootstrap.png)](/docsassets/guides/create-a-layout-view/layout-w-bootstrap.png)
See our __Layout.cshtml_ in the [Kickstart repository](https://github.com/Kentico/xperience-by-kentico-kickstart/blob/main/src/Kickstart.Web/Views/Shared/_Layout.cshtml) for your reference. Note that the file includes a navigation menu, which we will implement in the later steps of this series.
Your website will typically need more than one page. In the next step, we’ll add another one, reusing existing content types and adding a simple “Contact us” form.
[ Previous page ](/modules/developer-kickstart/apply-a-page-template)
9 of 13
[ Mark complete and continue ](/modules/developer-kickstart/add-contact-us-page)
![]()
[]()[]()
