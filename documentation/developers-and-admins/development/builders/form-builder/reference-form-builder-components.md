---
source: https://docs.kentico.com/documentation/developers-and-admins/development/builders/form-builder/reference-form-builder-components
scrape_date: 2026-01-22
---

  * [Home](/documentation)
  * [Developers and admins](/documentation/developers-and-admins)
  * [Development](/documentation/developers-and-admins/development)
  * [Builders](/documentation/developers-and-admins/development/builders)
  * [Form Builder](/documentation/developers-and-admins/development/builders/form-builder)
  * Reference - Form Builder components 


# Reference - Form Builder components
This page lists all components available for the [Form Builder](/documentation/developers-and-admins/development/builders/form-builder) by default in new installations of Xperience. For details on how to programmatically configure these components when used in the widget configuration dialog or in the form builder, see [Editing components](/documentation/developers-and-admins/customization/extend-the-administration-interface/ui-form-components/editing-components). 
In addition to the default system components, developers can [create custom components](/documentation/developers-and-admins/development/builders/form-builder/form-components) for the form builder.
**Identifiers of system form components**
You can access the identifiers of system form components in your code via the `IDENTIFIER` constant exposed by each form component class. For example, the identifier is required when [defining filters](/documentation/developers-and-admins/development/builders/form-builder/form-components/form-component-filtering) for form components.
Form component |  Form component class  
(Kentico.Forms.Web.Mvc) |  Type |  Description  
---|---|---|---  
Single checkbox |  CheckBoxComponent |  bool |  Checkbox field that saves a boolean value (`true` for a selected checkbox, `false` for a cleared checkbox).  
Consent agreement |  ConsentAgreementComponent |  GUID |  Checkbox field that allows users to give agreements to a pre-selected [consent](/documentation/developers-and-admins/data-protection/consent-management). Displays the selected consent’s short text next to the checkbox. When a visitor selects the consent checkbox and submits the form, the system creates a corresponding consent agreement for the given [contact](/documentation/business-users/digital-marketing/contact-management) and stores the agreement’s identifier (GUID) in the form field. To learn more, see: [Consent management](/documentation/developers-and-admins/data-protection/consent-management)  
Drop-down list |  DropDownComponent |  string |  Drop-down selector offering multiple options. Only one option can be selected. To learn how to configure options for this component programmatically, see [Configure selector editing components](#configure-selector-editing-components).  
Email |  EmailInputComponent |  string |  Input field for entering one or more email addresses.   
Upload file |  FileUploaderComponent |  string |  Field that allows users to submit a single file as part of the form data. The submitted files are stored on the file system in the _~/assets/BizFormFiles_ folder and the corresponding form field stores the file’s name. To learn how to configure options for this component programmatically, see [Configure the Upload file component](#configure-the-upload-file-component) for more information.  
Checkboxes |  MultipleChoiceComponent |  string |  Selector offering options in the form of checkboxes. Multiple options can be selected at the same time. To learn how to configure options for this component programmatically, see [Configure selector editing components](#configure-selector-editing-components).  
Number |  IntInputComponent |  int |  Textbox input field for entering whole numbers.   
Radio buttons |  RadioButtonsComponent |  string |  Selector offering multiple options in the form of radio buttons. Only one option can be selected. To learn how to configure options for this component programmatically, see [Configure selector editing components](#configure-selector-editing-components).  
reCAPTCHA |  RecaptchaComponent |  string |  A validation element based on [reCAPTCHA](https://www.google.com/recaptcha/), which allows forms to tell apart humans and computers. If the reCAPTCHA validation fails, the form cannot be submitted. Supports the following reCAPTCHA versions:
  * **reCAPTCHA v2** – requires users to click a checkbox indicating they are not a robot. This either passes the user immediately or challenges them to validate whether or not they are human (using an additional image selection or audio test).
  * **reCAPTCHA v3** – provides frictionless validation without interrupting users. Calculates a score for every request between 0.0 (very likely a bot) and 1.0 (very likely a good interaction). Validation succeeds or fails based on a specified score threshold.

The reCAPTCHA version used is determined by the website channel where the form is displayed, see: [Configure reCAPTCHA](#configure-recaptcha)  
Text area |  TextAreaComponent |  string |  Input field that allows users to enter text into an area with adjustable size.  
Text |  TextInputComponent |  string |  Textbox input field.  
U.S. phone number |  USPhoneComponent |  string |  Input field for a phone number in the standard US format, i.e. (132) 456-7890.  
## Configure selector editing components
When using selector components as editing components, configure the `DataSource` property to define their options. You can either specify a list of values, or a semicolon-separated list of values and text. Each option needs to be entered on a separate line. For example:
C#
Copy
```
// Assigns an editing component with DataSource to the SelectedOption property
[DropDownComponent(DataSource = "cz;Czech Republic\r\nusa;United States")]
public string SelectedOption { get; set; }
```

If you want to include a semicolon in the value or text of the options, you also need to set a different separator for the values and text using the `DataSourceValueSeparator` property. For example:
C#
Copy
```
// Assigns an editing component with DataSource to the SelectedOption property and sets "-" as the separator for the options
[DropDownComponent(DataSource = "cz-Czech Republic\r\nusa-United States", DataSourceValueSeparator = "-")]
public string SelectedOption { get; set; }
```

## Configure the Upload file component
You can configure properties of the file uploader via the `FormBuilderOptions` object:
  * [Limit the maximum uploadable file size](#limit-the-maximum-uploadable-file-size) – describes how to set the maximum allowed size of uploaded files.
  * [Protect against Denial of Service](#protect-against-denial-of-service) – describes measures the system employs as prevention against malicious attacks.


### Limit the maximum uploadable file size
You can control the maximum size of files uploaded via the file uploader form component by setting the `FormBuilderOptions.FileUploaderFileSizeLimit` option in your application’s startup code. The component automatically prevents people from uploading files larger than the specified limit and notifies them using a browser alert.
C#
Copy
```
using Kentico.Forms.Web.Mvc;

WebApplicationBuilder builder = WebApplication.CreateBuilder(args);

...

// Sets the maximum file size of files uploaded using the 'File uploader' form component to 100MB
// You can also load this value from any configuration source using the configuration provider pattern
builder.Services.Configure<FormBuilderOptions>(options => options.FileUploaderFileSizeLimit = 1024 * 100);
```

### Protect against Denial of Service
To expedite the form submission process, all files selected using the _Upload file_ component are immediately uploaded and stored in a dedicated folder on the server. This occurs independently of the form submission process. After the form is submitted, the system moves the corresponding file (currently selected in the _Upload file_ component) to the folder used to store form data on the server.
As a measure of protection against denial of service attacks, for example, by repeatedly uploading large files without submitting the form, the system limits the size of the folder used to temporarily store the uploaded files. The folder size limit, set to 500 MB by default, can be configured by setting the `FormBuilderOptions.TemporaryUploadFilesFolderSizeLimit` option in your application’s startup code. If the folder size limit is exceeded, the system prevents users from uploading additional files and logs an exception to the [event log](/documentation/developers-and-admins/configuration/event-log).
C#
Copy
```
using Kentico.Forms.Web.Mvc;

WebApplicationBuilder builder = WebApplication.CreateBuilder(args);

...

// Sets the temporary folder size limit to 1000MB
// You can also load this value from any configuration source using the configuration provider pattern
builder.Services.Configure<FormBuilderOptions>(options => options.TemporaryUploadFilesFolderSizeLimit = 1000);
```

The system deletes the contents of the temporary folder once every two hours. If you wish to modify this behavior, for example in cases where you expect higher volumes of form submissions containing large files, you can override the default two hour interval by configuring the **CMSDeleteTemporaryUploadFilesOlderThan** key in your project’s configuration file (_appsettings.json_ by default).
JSON
**Sets the folder cleanup period to one hour**
Copy
```
"CMSDeleteTemporaryUploadFilesOlderThan": 1,
```

## Configure reCAPTCHA
You need to register your site to use the reCAPTCHA API and obtain a pair of API keys:
  1. Go to <https://www.google.com/recaptcha/admin> and sign in with your Google account.
  2. Select the **reCAPTCHA type** that you wish to use. 
     * For reCAPTCHA v2, choose the **“I’m not a robot” Checkbox** option.
  3. Fill in all required details, including the domain where your site is running.
  4. Copy your **Site key** and **Secret key**.


Next, enter your site’s reCAPTCHA API keys into Xperience:
  1. Open the **Channel management** application in the administration.
  2. reCAPTCHA is configured per [website channel](/documentation/developers-and-admins/configuration/website-channel-management). Edit the channel you want to configure.
  3. Switch to the **Channel settings** tab.
  4. Under **reCAPTCHA settings** : 
    1. Select the reCAPTCHA version.
    2. Paste the API keys into the **Site key** and **Secret key** fields.
    3. For reCAPTCHA v3, set the **Score threshold** (a value between 0 and 1) required for the validation to be successful. Set a higher value if you have issues with spam or bots, and a lower value if you wish to make accessibility easier for users.
If the validation fails for a user, there are no further challenges and form submission is not possible. In these cases, users can attempt to refresh the page again.
  5. **Save** the settings.


With the API keys entered into the system, the **reCAPTCHA** form component now works in forms. When [composing forms](/documentation/business-users/digital-marketing/forms/create-and-edit-forms), the Form Builder interface allows users to configure additional properties for specific reCAPTCHA fields.
**reCAPTCHA v2**
  * **Label** – sets the field label.
  * **Size** – sets the size of the reCAPTCHA element (Normal or Compact).
  * **Type** – sets the type of challenge used if the initial noCAPTCHA validation fails (Image or Audio).


**reCAPTCHA v3**
  * **Badge Position** – _Bottom right_ and _Bottom left_ display the reCAPTCHA badge in the corresponding position at the bottom of the form. _Inline_ displays the reCAPTCHA badge as a standard field within the form.


### reCAPTCHA privacy and personal data handling
The reCAPTCHA API works by collecting hardware and software information, such as device and application data, and sending this data to Google for analysis. However, the reCAPTCHA form component is not integrated with the consent functionality in Xperience by Kentico. If required, you need to display any required privacy information to site visitors and collect consents manually.
For users in the European Union, you must comply with Google’s [EU User Consent Policy](https://www.google.com/about/company/user-consent-policy/).
### Using multiple reCAPTCHAs on the same page
The system does not support the use of multiple reCAPTCHA fields within the same form. You can have multiple different forms with a reCAPTCHA field on the same page.
![]()
[]()[]()
