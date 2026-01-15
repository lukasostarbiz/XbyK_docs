# Form data
  * [ Copy page link ](api/digital-marketing/form-data#) | [Get HelpService ID](api/digital-marketing/form-data#)
Core MVC 5


[✖](api/digital-marketing/form-data# "Close page link panel") [Copy to clipboard](api/digital-marketing/form-data#)
  
List of examples:
  * [Dependency injection](api/digital-marketing/form-data#dependency-injection)
    * [Initialize required services](api/digital-marketing/form-data#initialize-required-services)
    * [Add data records to a form](api/digital-marketing/form-data#add-data-records-to-a-form)
    * [Load data records from a form](api/digital-marketing/form-data#load-data-records-from-a-form)
    * [Update the data records of a form](api/digital-marketing/form-data#update-the-data-records-of-a-form)
    * [Delete form data records](api/digital-marketing/form-data#delete-form-data-records)


## Dependency injection
### Initialize required services
C#
Copy
```
// Initializes all services and provider classes used within
// the API examples on this page using dependency injection.
private readonly IInfoProvider<BizFormInfo> bizFormInfoProvider;

public FormDataServices(IInfoProvider<BizFormInfo> bizFormInfoProvider)
{
    this.bizFormInfoProvider = bizFormInfoProvider;
}
```

[> Back to list of examples](api/digital-marketing/form-data#toc)
### Add data records to a form
C#
Copy
```
// Gets the form object representing the 'ContactUs' form
BizFormInfo formObject = bizFormInfoProvider.Get("ContactUs");

if (formObject != null)
{
    // Gets the class name of the 'ContactUs' form
    DataClassInfo formClass = DataClassInfoProvider.GetDataClassInfo(formObject.FormClassID);
    string formClassName = formClass.ClassName;

    // Creates a new data record for the form
    BizFormItem newFormItem = BizFormItem.New(formClassName);

    // Sets the values for the form's fields (UserMessage in this case)
    newFormItem.SetValue("UserMessage", "This is a message submitted through the API.");

    // Saves the new form record into the database
    // Set values for all 'Required' fields in the form before calling the Insert method, otherwise an exception will occur
    newFormItem.Insert();
}
```

[> Back to list of examples](api/digital-marketing/form-data#toc)
### Load data records from a form
C#
Copy
```
// Gets the form object representing the 'ContactUs' form
BizFormInfo formObject = bizFormInfoProvider.Get("ContactUs");

if (formObject != null)
{
    // Gets the class name of the 'ContactUs' form
    DataClassInfo formClass = DataClassInfoProvider.GetDataClassInfo(formObject.FormClassID);
    string formClassName = formClass.ClassName;

    // Loads the form's data
    ObjectQuery<BizFormItem> data = BizFormItemProvider.GetItems(formClassName);

    // Loops through the form's data records
    foreach (BizFormItem item in data)
    {
        // Gets the values of the 'UserEmail' and 'UserMessage' text fields for the given data record
        string emailFieldValue = item.GetStringValue("UserEmail", "");
        string messageFieldValue = item.GetStringValue("UserMessage", "");
    }
}
```

[> Back to list of examples](api/digital-marketing/form-data#toc)
### Update the data records of a form
C#
Copy
```
// Gets the form object representing the 'ContactUs' form
BizFormInfo formObject = bizFormInfoProvider.Get("ContactUs");

if (formObject != null)
{
    // Gets the class name of the 'ContactUs' form
    DataClassInfo formClass = DataClassInfoProvider.GetDataClassInfo(formObject.FormClassID);
    string formClassName = formClass.ClassName;

    // Loads all data records from the form whose 'UserEmail' field ends with the ".net" suffix
    ObjectQuery<BizFormItem> data = BizFormItemProvider.GetItems(formClassName)
                                                            .WhereEndsWith("UserEmail", ".net");

    // Loops through the form's data records
    foreach (BizFormItem item in data)
    {
        // Assigns and saves a value for the form record's 'InternalNote' field
        item.SetValue("InternalNote", "This note was added by the system.");
        item.SubmitChanges(false);
    }
}
```

[> Back to list of examples](api/digital-marketing/form-data#toc)
### Delete form data records
C#
Copy
```
// Gets the form object representing the 'ContactUs' form
BizFormInfo formObject = bizFormInfoProvider.Get("ContactUs");

if (formObject != null)
{
    // Gets the class name of the 'ContactUs' form
    DataClassInfo formClass = DataClassInfoProvider.GetDataClassInfo(formObject.FormClassID);
    string formClassName = formClass.ClassName;

    // Loads all data records from the form that have an empty value in the 'UserMessage' field
    ObjectQuery<BizFormItem> data = BizFormItemProvider.GetItems(formClassName)
                                                            .WhereEmpty("UserMessage");

    // Loops through the form's data records
    foreach (BizFormItem item in data)
    {
        // Deletes all files stored in the form's fields
        BizFormInfoProvider.DeleteBizFormRecordFiles(formClass.ClassFormDefinition, item);

        // Deletes the form record from the database
        item.Delete();
    }
}
```

[> Back to list of examples](api/digital-marketing/form-data#toc)