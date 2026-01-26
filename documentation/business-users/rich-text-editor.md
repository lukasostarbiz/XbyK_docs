---
source: https://docs.kentico.com/documentation/business-users/rich-text-editor
scrape_date: 2026-01-26
---

  * [Home](/documentation)
  * [Business users](/documentation/business-users)
  * Rich text editor 


# Rich text editor
To help you with the editing of text fields, the rich text editor allows you to work with content in an intuitive WYSIWYG (“What You See Is What You Get”) manner. The editor provides essential formatting functionality, e.g., bold, italics, paragraph formatting, lists, or indentation.
When editing, you can also switch to a code view to see the corresponding HTML formatting of the text. The count of the currently entered characters is always displayed at the bottom right corner of the editor.
[![Rich text editor](/docsassets/documentation/rich-text-editor/rich_text_editor.png)](/docsassets/documentation/rich-text-editor/rich_text_editor.png)
## Format text
The rich text editor offers essential formatting functionality. You can use the editor toolbar to format text within the editor.
Following is the default setup of the rich text editor (the editor might offer different functionality in your projects, depending on the configuration [set by the developers](/documentation/developers-and-admins/configuration/rich-text-editor-configuration)):
[![Rich text editor toolbar](/docsassets/documentation/rich-text-editor/rich_text_editor_toolbar.png)](/docsassets/documentation/rich-text-editor/rich_text_editor_toolbar.png)
  1. **Bold** (_Ctrl + B_)
  2. **Italic** (_Ctrl + I_)
  3. **Underline** (_Ctrl + U_)
  4. **Paragraph format**
     * Use the **Paragraph format** button to further break down the text into sections using predefined header formatting.
  5. **Ordered List**
  6. **Unordered List**
  7. **Decrease Indent** (_Ctrl + [_)
  8. **Increase Indent** (_Ctrl + ]_)
  9. **Align Left**
  10. **Align Center**
  11. **Align Right**
  12. **Code View**
     * Select **Code View** to see and edit the corresponding HTML formatting of the text.
  13. **Insert image** - insert a [content hub](/documentation/business-users/content-hub/content-item-assets) image into the edited content. 
     * Selecting an inserted image offers additional options: 
       * **Alternative text** – sets the alt text property.
       * **Align** – controls image alignment.
       * **Display** – sets display style.
       * **Change size** – resizes the image.
       * **Remove** – deletes the image.
  14. **Insert link**
     * Select any **Asset** from the [content hub](/documentation/business-users/content-hub/content-item-assets) (e.g., image, spreadsheet, PDF, text document) and insert a hypertext link to the file into the edited content.
     * Select a **Page** from the [content tree](/documentation/business-users/website-content) and insert a link into the edited content. Note that you can only select pages of content types that have the [URL feature enabled](/documentation/developers-and-admins/development/content-types).
     * Select **Web URL** to insert a hypertext link to any external resource into the edited content.


## Allowed HTML attributes and tags
In order to mitigate possible security threats, the rich text editor sanitizes the content’s HTML code. That is, any HTML attributes and tags [that are not allowed](/documentation/developers-and-admins/configuration/rich-text-editor-configuration#html-allowed-in-the-editor) are removed. This may in some cases modify or break your HTML code, for example when adding content via the editor’s  _Code View_ option.
![]()
[]()[]()
