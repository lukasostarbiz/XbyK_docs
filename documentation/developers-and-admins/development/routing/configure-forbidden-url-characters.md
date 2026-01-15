# Configure forbidden URL characters
  * [ Copy page link ](documentation/developers-and-admins/development/routing/configure-forbidden-url-characters#) | [Get HelpService ID](documentation/developers-and-admins/development/routing/configure-forbidden-url-characters#)
Core MVC 5


[✖](documentation/developers-and-admins/development/routing/configure-forbidden-url-characters# "Close page link panel") [Copy to clipboard](documentation/developers-and-admins/development/routing/configure-forbidden-url-characters#)
In URLs, some characters are considered unsafe because they may be used for special purposes or could cause issues with browsers or servers. The system automatically replaces or removes forbidden characters in [URL slugs and vanity URLs](documentation/business-users/website-content/manage-page-urls).
By default, the following characters are forbidden:
Text
Copy
```
\/:*?"<>|&%.'#[]+=„“ and the space and tab characters
```

## Specify additional forbidden characters
To specify additional forbidden characters:
  1. Open the **Settings** application.
  2. Select the **URLs and SEO** category.
  3. Type the characters into the **Forbidden URL characters** setting (without any separator).
  4. **Save** the settings.


Alternatively, you can enter a [regular expression](http://en.wikipedia.org/wiki/Regular_expression) into the **Allowed URL characters** setting to precisely specify which characters are allowed in URLs.
## Override default forbidden characters
The default characters listed above are always forbidden unless you override the `CMSForbiddenURLValues` key in your project’s configuration file (_appsettings.json_ by default). For example:
JSON
**appsettings.json**
Copy
```
"CMSForbiddenURLValues": "\\/:*?\"<>|&%.'#[]+ ="
```

**Note** : Support for the slash (‘/’) character in [vanity URLs](documentation/business-users/website-content/manage-page-urls#manage-vanity-urls-of-pages) always requires additional [configuration](documentation/developers-and-admins/development/routing/configure-forbidden-url-characters#allow-the-slash-character-in-vanity-urls), even if the slash character is not included in the forbidden characters specified by the `CMSForbiddenURLValues` key.
You can either use the key to allow some of the default forbidden characters or add new ones. We **strongly recommend keeping the default set of forbidden characters** – entering the characters into URL values may prevent certain types of URLs from resolving correctly.
### Allow the slash (‘/’) character in vanity URLs
Support for the slash character in [vanity URLs](documentation/business-users/website-content/manage-page-urls#manage-vanity-urls-of-pages) can’t be configured through the `CMSForbiddenURLValues` key, but has to be enabled separately. When the setting below is enabled, the slash character is always allowed in vanity URLs, regardless of other settings.
To allow the slash character in vanity URLs:
  1. Open the **Settings** application.
  2. Select the **URLs and SEO** category.
  3. Select the **Allow ‘/’ in vanity URLs** setting.
  4. **Save** the settings.


**Note:**
The following vanity URL paths are always forbidden:
  * paths starting with ‘/’ or ‘~/’
  * paths ending with ‘/’
  * paths containing multiple ‘/’ in a row (e.g., `vanity//path`)


## Specify forbidden character replacement
To specify forbidden character replacement:
  1. Open the **Settings** application.
  2. Select the **URLs and SEO** category.
  3. Enter the character into the **Forbidden character replacement** setting.
  4. **Save** the settings.


By default, forbidden characters located at the beginning or end of the path are removed completely and consecutive forbidden characters are only replaced by a single replacement character. For example, “ _Articles*and%%%blogs_ ” is replaced with “ _Articles-and-blogs_ ” when using the default replacement character: ‘ _-_ ’ hyphen. If no replacement character is specified, the underscore ‘ ___ ’ character is used.