# Handle form events
  * [ Copy page link ](documentation/developers-and-admins/customization/handle-global-events/handle-form-events#) | [Get HelpService ID](documentation/developers-and-admins/customization/handle-global-events/handle-form-events#)
Core MVC 5


[✖](documentation/developers-and-admins/customization/handle-global-events/handle-form-events# "Close page link panel") [Copy to clipboard](documentation/developers-and-admins/customization/handle-global-events/handle-form-events#)
You can run custom actions directly when form actions occur by [implementing handlers](documentation/developers-and-admins/customization/handle-global-events) for events of the `BizFormItemEvents` type. The system invokes the following events in response to form actions:
  * `Update` – invoked when a form record is updated.
  * `Insert` – invoked on each form submission.
  * `Delete` – invoked when a form record is deleted.


Use the appropriate event type to perform your custom actions or data modifications `Before` or `After` the given form event.
To access the data of the active form in the event handler, use the `Item` property _(_`BizFormItem` type) of the `BizFormItemEventArgs` parameter. The parameter is available for all form item event handlers.
C#
**Example**
Copy
```
using System;

using CMS;
using CMS.DataEngine;
using CMS.OnlineForms;

// Registers the custom module into the system
[assembly: RegisterModule(typeof(FormHandlerModule))]

public class FormHandlerModule : Module
{
    // Module class constructor, the system registers the module under the name "CustomFormHandlers"
    public FormHandlerModule()
        : base("CustomFormHandlers")
    {
    }

    // Contains initialization code that is executed when the application starts
    protected override void OnInit()
    {
        base.OnInit();

        // Assigns a handler to the BizFormItemEvents.Insert.After event
        // This event occurs after the creation of every new form record
        BizFormItemEvents.Insert.After += FormItem_InsertAfterHandler;
    }

    // Handles the form data when users create new records for the 'ContactUs' form
    private void FormItem_InsertAfterHandler(object sender, BizFormItemEventArgs e)
    {
        // Gets the form data object from the event handler parameter
        BizFormItem formDataItem = e.Item;

        // Checks that the form record was successfully created
        // Ensures that the custom actions only occur for records of the 'ContactUs' form
        // The values of form class names must be in lower case
        if (formDataItem != null && formDataItem.BizFormClassName.Equals("bizform.contactus", StringComparison.OrdinalIgnoreCase))
        {
            string firstNameFieldValue = formDataItem.GetStringValue("FirstName", "");
            string lastNameFieldValue = formDataItem.GetStringValue("LastName", "");

            // Perform any required logic with the form field values

            // Variable representing a custom value that you want to save into the form data
            object customFieldValue = "customValue";

            // Sets and saves a value for the form record's 'CustomField' field
            formDataItem.SetValue("CustomField", customFieldValue);
            formDataItem.SubmitChanges(false);
        }
    }
}
```