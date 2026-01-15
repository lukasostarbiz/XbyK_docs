# Email templates
  * [ Copy page link ](documentation/developers-and-admins/digital-marketing-setup/email-templates#) | [Get HelpService ID](documentation/developers-and-admins/digital-marketing-setup/email-templates#)
Core MVC 5


[✖](documentation/developers-and-admins/digital-marketing-setup/email-templates# "Close page link panel") [Copy to clipboard](documentation/developers-and-admins/digital-marketing-setup/email-templates#)
**Email license requirements**
The license tier required for email features depends on the size of the [email channels](documentation/developers-and-admins/digital-marketing-setup/email-channel-management) that you use:
  * Email microchannels – available for all license tiers
  * Standard size email channels – require the **Advanced** license tier


To learn more, see the description of the [Channel size](documentation/developers-and-admins/digital-marketing-setup/email-channel-management#create-and-configure-email-channels) property for email channels.
**Email Builder templates**
The system uses two different types of email templates – the macro-based email templates described on this page and [Email Builder](documentation/developers-and-admins/development/builders/email-builder) templates. Email Builder templates are prepared and registered by developers as Razor components within the project’s code.
Both types of templates are offered when selecting a template for a new email.
Email templates define the overall layout of [emails](documentation/business-users/digital-marketing/emails) and add fixed content, such as a header or footer. You can reuse a single template for any number of emails. The template content is defined through HTML code and CSS styles. In many cases, the template code can be prepared in an external tool by a designer and then copied into Xperience. 
The basic template code needs to be extended by adding [macros](documentation/developers-and-admins/configuration/macro-expressions/macro-syntax), which serve as placeholders for the fields of email [content types](documentation/developers-and-admins/development/content-types), as well as system elements such as a regular email’s unsubscribe link URL.
The following diagram shows the structure of a sample email, with the template including pieces of content loaded from individual email fields.
[![Email template containing individual email fields](docsassets/documentation/email-templates/Email_Structure.png)](https://docs.kentico.com/docsassets/documentation/email-templates/Email_Structure.png)
## Email content types
Before you start creating email templates, you need to have exact knowledge of your email [content types](documentation/developers-and-admins/development/content-types) and their fields, as well as any linked content types used within.
Email templates display content from specific fields, so they are only compatible with [content types](documentation/developers-and-admins/development/content-types) containing these fields. For this reason, you need to configure which email templates are allowed for every email content type. You can assign templates when editing content types on the **Allowed templates** tab, or add allowed content types from the other side of the relationship when [creating or editing](documentation/developers-and-admins/digital-marketing-setup/email-templates#create-email-templates) email templates.
**Configuration of rich text fields for emails**
When creating text fields for email content types using the **Rich text editor** form component, we recommend using an **empty (unselected)** value for the component’s **Configuration name** setting. This allows the system to dynamically load the editor configuration based on the current email’s **Purpose** , i.e., the _Email content_ configuration for regular and confirmation emails, and _Email content (Form autoresponder)_ for autoresponder emails.
The _Email content (Form autoresponder)_ editor configuration contains the additional **Insert Double Opt-In Link** toolbar button, which is only relevant for form autoresponder emails.
If you wish to use a [custom editor configuration](documentation/developers-and-admins/configuration/rich-text-editor-configuration), you may need to create a separate configuration and content type for each email purpose.
## Create email templates
  1. Open the **Email templates** application in Xperience.
  2. Select **New email template**.
  3. Fill in the template properties: 
     * **Email template name** – the name of the template displayed in Xperience (for example when selecting the template for an email).
     * _(Optional)_ **Identifiers** – specify the code name if you wish to use a code name different than the pre-filled value.
     * **Description** – allows you to add information and comments about the template. Only displayed to other editors in the Xperience administration.
  4. Select **Save**.
  5. Write or copy the HTML and CSS code of your template into the **Source code** area.
  6. **Save** again.
  7. On the **Allowed content types** tab, **Select content types**.
  8. Use the checkboxes to allow compatible content types for the email template.
  9. Select **Save**.


The basic template is now ready, but cannot yet display content from emails in Xperience. Continue by adding [macros](documentation/developers-and-admins/digital-marketing-setup/email-templates#email-template-macros) into the template.
You can assign the template when creating emails of allowed content types in email channel applications. You can reuse a template for any number of emails.
## Email template macros
Email templates must contain [macro expressions](documentation/developers-and-admins/configuration/macro-expressions/macro-syntax), which serve as placeholders for various kinds of email content.
The following macros are available for email system fields:
  * `{% unsubscribeurl %}` – can be placed into templates for emails with the _Regular_ purpose. Resolves into the unsubscribe URL based on the settings of the email using the template. Position the placeholder into the `href` attribute of a suitable link tag, for example within the footer of your email template. See [Send regular emails to subscribers](documentation/business-users/digital-marketing/emails/send-regular-emails-to-subscribers) for detailed information and instructions.
HTML
**Unsubscribe link example**
Copy
```
<p>
    If you'd rather not receive future emails, <a href="{% unsubscribeurl %}">unsubscribe here</a>.
</p>
```

  * `{% previewtext %}` – resolves into the _Preview text_ of emails that use the template. Most email clients display a content preview, showing the starting characters of the email’s body. Insert this placeholder after the template’s opening `<body>` tag. Adding a hidden preview text placeholder allows editors to control the preview text, without needing to adjust the visible email content and design.
HTML
**Preview text placeholder example**
Copy
```
<body>
<!-- HIDDEN PREVIEW TEXT -->
<div style="display: none; font-size: 1px; color: #ffffff; line-height: 1px; max-height: 0px; max-width: 0px; opacity: 0; overflow: hidden;">
   {% HTMLEncode(previewtext) %}
</div>

...
```



### Content type field macros
Add `{% fieldname %}` macros to position the content of individual [content type](documentation/developers-and-admins/development/content-types) fields. Use the exact **Field name** (not the _Field caption_). For most field data types, the macro returns the field’s value as text.
#### Email template macro reference
Field data type |  Macro information |  Example  
---|---|---  
Basic data types (Text, Number, Date, etc.) |  Returns the field’s value as text. |  `{% field %}`  
Pages and reusable content |  Returns a collection of the selected content items and pages. Use iteration or indexing to get individual items. Access item fields via the `Data` macro. See: [Linked pages and content items](documentation/developers-and-admins/digital-marketing-setup/email-templates#linked-pages-and-content-items) |  `{% emailfield[0].Data.itemfield %}`  
Taxonomy |  Use the `Tags` macro to access the taxonomy tags selected in the field. Each tag then provides the following macro fields:
  * `Title`
  * `Name`
  * `Description`

See: [Taxonomy fields](documentation/developers-and-admins/digital-marketing-setup/email-templates#taxonomy-fields) |  `{% field.Tags[0].Title %}`  
Media files |  Returns a collection of [media file](documentation/business-users/media-libraries) objects. Use iteration or indexing to get individual objects. Access file properties via the following macros:
  * `Url`
  * `Extension`
  * `Name`

|  `{% field[0].Name %}`  
Object code names |  Returns a collection of the selected object code names. Use iteration or indexing to get individual code names. |  `{% field[0] %}`  
Object global identifiers |  Returns a collection of GUIDs for the selected objects. Use iteration or indexing to get individual GUID values. |  `{% field[0] %}`  
Fields of linked content items  
Content item asset |  Returns data about the file stored in the field. Access file properties via the following macros:
  * `Url`
  * `Extension`
  * `Name`

[Image variants](documentation/business-users/content-hub/content-item-assets#image-variants) are currently not supported for emails. See: [Content item assets (files)](documentation/developers-and-admins/digital-marketing-setup/email-templates#content-item-assets-files) |  `{% emailfield[0].Data.assetfield.Url %}`  
Smart folder |  Currently not supported for emails. |   
**Language-specific formatting**
For fields with the _Date and time_ , _Decimal number_ or _Floating-point number_ data type, the macro value is resolved according to the **Formatting culture** of the [language](documentation/developers-and-admins/configuration/languages) set for the [email channel](documentation/developers-and-admins/digital-marketing-setup/email-channel-management).
**Time values**
The values of fields with the _Date and time_ data type are always returned in the time zone of the server where the application is running.
#### Linked pages and content items
Fields with the **Pages and reusable content** data type link to [reusable content items](documentation/business-users/content-hub) or [pages](documentation/business-users/website-content) of different content types and require additional handling. In this case, the field macro returns a **collection** of the selected items.
Use **iteration** (loops) or **indexing** to get individual items. When processing linked content items, you can access the following macro fields:
  * `Data` – content type fields of the linked item
  * `Type` – content type of the linked item
  * _(Only for pages)_ `Url` – the absolute [canonical URL](documentation/business-users/website-content/manage-page-urls#select-the-canonical-url-of-pages) of the page, containing the main domain name assigned to the given [website channel](documentation/developers-and-admins/configuration/website-channel-management).


See [Macro syntax](documentation/developers-and-admins/configuration/macro-expressions/macro-syntax) and the example below for more details.
Note that secured content items with the **Requires authentication** flag enabled are not included in emails. If you send an email containing secured items, the corresponding content will be missing.
**Pages linked in field with the ‘Pages’ data type**
Pages that are linked using the **Page selector** form component in fields of the **Pages** data type can be retrieved similarly to pages linked in fields with the **Pages and reusable content** data type. The important difference is that for pages linked in fields with the **Pages** data type you cannot access their content type `Data` fields. Only their `Url` and `Type` are available.
#### Content item assets (files)
When processing linked content items with a field that stores a file (**Content item asset** data type), you can access the file properties via the following macros:
  * `Url` – the URL through which the file can be displayed or downloaded. Resolved based on the **Email service domain** set for the [email channel](documentation/developers-and-admins/digital-marketing-setup/email-channel-management).
  * `Extension` – the file extension with a leading dot character.
  * `Name` – the file name, including the extension.


**Example**
The following example shows the field structure of an email content type:
Email  
---  
Field name |  Data type  
EmailContent |  Rich text (HTML)  
Signature |  Pages and reusable content (Signature)  
SocialMediaLinks |  Pages and reusable content (Social media link)  
LinkedPages |  Pages and reusable content (Article page)  
several linked reusable content types:
Signature |  Social media link |  Image  
---|---|---  
Field name |  Data type |  Field name |  Data type |  Field name |  Data type  
SignatureText |  Text |  LinkTitle |  Text |  ImageFile |  Content item asset  
SignatureImage |  Pages and reusable content (Image) |  LinkUrl |  Text |  ImageDescription |  Text  
|  |  LinkImage |  Pages and reusable content (Image) |  |   
and a linked page type:
Article page  
---  
Field name |  Data type  
Title |  Text  
Content |  Rich text (HTML)  
Image |  Pages and reusable content (Image)  
The content types and fields described above could be displayed within an email template using the following HTML and macros:
HTML
Copy
```
<table border="0" cellpadding="0" cellspacing="0" style="max-width: 500px;" width="100%">
  <tbody>
    <tr>
      <td>
        {% EmailContent %}
      </td>
    </tr>
    <tr>
      <td>
        See the following related articles that might interest you:
        {% foreach (page in LinkedPages) { %}
          <h2>{% HTMLEncode(page.Data.Title) %}</h2>
          <a href="{% HTMLEncode(page.Url) %}" target="_blank">
            <img src="{% HTMLEncode(page.Data.Image[0].Data.ImageFile.Url) %}"
                 alt="{% HTMLEncode(page.Data.Title) %}"
                 title="{% HTMLEncode(page.Data.Title) %}"
                 loading="lazy">
          </a>
          {% page.Data.Content %}
        {% } %}
      </td>
    </tr>
    <tr>
      <td>
        {% HTMLEncode(Signature[0].Data.SignatureText) %}
      </td>
    </tr>
    <tr>
      <td>
        <img src="{% HTMLEncode(Signature[0].Data.SignatureImage[0].Data.ImageFile.Url) %}"
             alt="{% HTMLEncode(Signature[0].Data.SignatureImage[0].Data.ImageDescription) %}"
             title="{% HTMLEncode(Signature[0].Data.SignatureImage[0].Data.ImageDescription) %}"
             loading="lazy">
      </td>
    </tr>
    <tr>
      <td>
        {% foreach (link in SocialMediaLinks) { %}
          <a href="{% HTMLEncode(link.Data.LinkUrl) %}" target="_blank">
            <img src="{% HTMLEncode(link.Data.LinkImage[0].Data.ImageFile.Url) %}"
                 alt="{% HTMLEncode(link.Data.LinkTitle) %}"
                 title="{% HTMLEncode(link.Data.LinkTitle) %}"
                 loading="lazy">
          </a>
        {% } %}
      </td>
    </tr>
  </tbody>
</table>
```

#### Taxonomy fields
When processing emails with a [taxonomy](documentation/developers-and-admins/configuration/taxonomies) tag selector field (**Taxonomy** field data type), you can access the collection of tags selected in the field via the `Tags` macro. Each tag item in the collection then provides the following macro fields:
  * `Title` – the tag’s _Title_ text.
  * `Name` – the tag’s _Code name_.
  * `Description` – the tag’s _Description_ text.


**Tag translations**
When resolving a tag’s _Title_ or _Description_ , the system uses the translation in the [language](documentation/developers-and-admins/configuration/languages) set for the [email channel](documentation/developers-and-admins/digital-marketing-setup/email-channel-management) (if the translation is available for the tag).
For example, if an email has a _Products_ field linking to product content items, which then have a _ProductTags_ tag selector field, the tags could be displayed using the following email template code:
HTML
Copy
```
{% foreach (product in Products) { %}
  <div class="product">
    {% HTMLEncode(product.Data.ProductTitle) %}
    {% foreach (tag in product.Data.ProductTags.Tags) { %}
      <div class="tag">{% HTMLEncode(tag.Title) %}</div>
    {% } %}
  </div>
{% } %}
```

### Value encoding
Field macros in email templates resolve the text of fields without encoding. This means that the code of email templates can be broken, for example if a field’s value contains certain HTML characters.
To ensure that content is displayed correctly and securely in emails, enclose field macros into encoding methods:
  * `HTMLEncode` – encodes HTML characters to corresponding [entities](https://www.w3schools.com/html/html_entities.asp).
    * Use encoding to safely display HTML-like text.
    * Do **NOT** HTML encode fields using the **Rich text editor** form component, which return HTML elements for formatting and advanced content.
  * `UrlEncode` – performs [URL encoding](https://www.w3schools.com/tags/ref_urlencode.ASP) of the specified text. For example, use URL encoding when you have a text field that you insert as part of a URL.


HTML
**Examples**
Copy
```
<div>
   {% HTMLEncode(previewtext) %}
</div>

<a href="https://www.example.com/page?utm_content={% UrlEncode(UtmContent) %}">Link</a>
```

## Delete email templates
Templates that are not currently assigned to any emails can be deleted from the main listing in the **Email templates** application. If you wish to delete a template that is currently being used, you first need to edit the given emails your email channel applications and assign a different template.
Deleted emails can be restored from the [recycle bin](documentation/business-users/recycle_bin). This is no longer possible when their template is deleted.