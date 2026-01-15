# Reference - React input components
  * [ Copy page link ](documentation/developers-and-admins/customization/extend-the-administration-interface/ui-form-components/reference-react-input-components#) | [Get HelpService ID](documentation/developers-and-admins/customization/extend-the-administration-interface/ui-form-components/reference-react-input-components#)
Core MVC 5


[✖](documentation/developers-and-admins/customization/extend-the-administration-interface/ui-form-components/reference-react-input-components# "Close page link panel") [Copy to clipboard](documentation/developers-and-admins/customization/extend-the-administration-interface/ui-form-components/reference-react-input-components#)
This page provides an overview of input components from the `@kentico/xperience-admin-components` package that can be used to define editing interfaces for [admin UI form components](documentation/developers-and-admins/customization/extend-the-administration-interface/ui-form-components).
  * [Checkbox](documentation/developers-and-admins/customization/extend-the-administration-interface/ui-form-components/reference-react-input-components#checkbox)
  * [Input](documentation/developers-and-admins/customization/extend-the-administration-interface/ui-form-components/reference-react-input-components#input)
  * [Select](documentation/developers-and-admins/customization/extend-the-administration-interface/ui-form-components/reference-react-input-components#anchor-referencereactinputcomponents-selectcomponent-select)
  * [RadioGroup](documentation/developers-and-admins/customization/extend-the-administration-interface/ui-form-components/reference-react-input-components#radiogroup)
  * [RichTextEditor](documentation/developers-and-admins/customization/extend-the-administration-interface/ui-form-components/reference-react-input-components#richtexteditor)
  * [Textarea](documentation/developers-and-admins/customization/extend-the-administration-interface/ui-form-components/reference-react-input-components#textarea)


## Checkbox
**onChange event Type:** React.ChangeEventHandler<HTMLInputElement>
A binary state input.
The checkbox component has the following configurable properties:
  * [label](documentation/developers-and-admins/customization/extend-the-administration-interface/ui-form-components/reference-react-input-components#ReferenceReactinputcomponents-checkboxLabel)
  * [size](documentation/developers-and-admins/customization/extend-the-administration-interface/ui-form-components/reference-react-input-components#ReferenceReactinputcomponents-checkboxSize)
  * [checked](documentation/developers-and-admins/customization/extend-the-administration-interface/ui-form-components/reference-react-input-components#ReferenceReactinputcomponents-checkboxChecked)
  * [indetermined](documentation/developers-and-admins/customization/extend-the-administration-interface/ui-form-components/reference-react-input-components#ReferenceReactinputcomponents-checkboxIndetermined)
  * [tooltipText](documentation/developers-and-admins/customization/extend-the-administration-interface/ui-form-components/reference-react-input-components#ReferenceReactinputcomponents-checkboxTooltipText)
  * [onChange](documentation/developers-and-admins/customization/extend-the-administration-interface/ui-form-components/reference-react-input-components#ReferenceReactinputcomponents-checkboxOnChange)
  * [Inherited properties](documentation/developers-and-admins/customization/extend-the-administration-interface/ui-form-components/reference-react-input-components#ReferenceReactinputcomponents-checkboxInherited)


**Type:** label?: string 
Sets the label for the checkbox.
[![Label property demonstration](docsassets/documentation/reference-react-input-components/CheckboxLabel.png)](https://docs.kentico.com/docsassets/documentation/reference-react-input-components/CheckboxLabel.png)
JS
**Example**
Copy
```
import { Checkbox } from "@kentico/xperience-admin-components";

<Checkbox label="I'm a checkbox!" />
```

**Type:** size?: CheckboxSize 
Sets the size of the checkbox to one of three sizes defined by the `CheckboxSize` enum. Defaults to `CheckboxSize.M` if not set.
[![Size property demonstration](docsassets/documentation/reference-react-input-components/CheckboxSize.png)](https://docs.kentico.com/docsassets/documentation/reference-react-input-components/CheckboxSize.png)
Value |  Example  
---|---  
CheckboxSize.S |  JS Copy ```
import { Checkbox, CheckboxSize } from "@kentico/xperience-admin-components";

<Checkbox label="Small checkbox" size={CheckboxSize.S} />
```
  
CheckboxSize.M |  JS Copy ```
import { Checkbox, CheckboxSize } from "@kentico/xperience-admin-components";

<Checkbox label="Medium checkbox" size={CheckboxSize.M} />
```
  
CheckboxSize.L |  JS Copy ```
import { Checkbox, CheckboxSize } from "@kentico/xperience-admin-components";

<Checkbox label="Large checkbox" size={CheckboxSize.L} />
```
  
**Type:** checked?: boolean
Indicates a checked state. Defaults to _false_.
[![Check property demonstration](docsassets/documentation/reference-react-input-components/CheckboxChecked.png)](https://docs.kentico.com/docsassets/documentation/reference-react-input-components/CheckboxChecked.png)
JS
**Example**
Copy
```
import { Checkbox } from "@kentico/xperience-admin-components";

<Checkbox label="Label" checked />
```

**Type:** indetermined?: boolean
Indicates an indeterminate state. Defaults to _false_.
[![Indetermined property demonstration](docsassets/documentation/reference-react-input-components/CheckboxIndeterminate.png)](https://docs.kentico.com/docsassets/documentation/reference-react-input-components/CheckboxIndeterminate.png)
JS
**Example**
Copy
```
import { Checkbox } from "@kentico/xperience-admin-components";

<Checkbox label="Label" indetermined />
```

**Type:** tooltipText?: string
Sets the tooltip for the checkbox.
[![tooltipText property demonstration](docsassets/documentation/reference-react-input-components/CheckboxTooltip.png)](https://docs.kentico.com/docsassets/documentation/reference-react-input-components/CheckboxTooltip.png)
JS
**Example**
Copy
```
import { Checkbox } from "@kentico/xperience-admin-components";

<Checkbox label="I'm a checkbox!" tooltipText="With a tooltip!" />
```

**Type:** onChange?: (event: React.ChangeEvent<HTMLInputElement>, checked: boolean) => void;
Allows the consumer to bind an _onChange_ event handler.
JS
**Example**
Copy
```
import { Checkbox } from "@kentico/xperience-admin-components";

<Checkbox label="Label" onChange={() => window.alert("Clicked!")} />
```

The component also inherits the following properties from [HTMLInputElement](https://developer.mozilla.org/en-US/docs/Web/API/HTMLInputElement).
  * name
  * [onClick](https://developer.mozilla.org/en-US/docs/Web/API/GlobalEventHandlers/onclick)
  * required
  * disabled
  * readOnly
  * tabIndex


## Input
**onChange event Type:** React.ChangeEventHandler<HTMLInputElement>
Conventional text input element.
The input component has the following configurable properties:
  * [label](documentation/developers-and-admins/customization/extend-the-administration-interface/ui-form-components/reference-react-input-components#ReferenceReactinputcomponents-inputLabel)
  * [invalid](documentation/developers-and-admins/customization/extend-the-administration-interface/ui-form-components/reference-react-input-components#ReferenceReactinputcomponents-inputInvalid)
  * [value](documentation/developers-and-admins/customization/extend-the-administration-interface/ui-form-components/reference-react-input-components#ReferenceReactinputcomponents-inputValue)
  * [type](documentation/developers-and-admins/customization/extend-the-administration-interface/ui-form-components/reference-react-input-components#ReferenceReactinputcomponents-inputType)
  * [validationMessage](documentation/developers-and-admins/customization/extend-the-administration-interface/ui-form-components/reference-react-input-components#ReferenceReactinputcomponents-inputValidationMessage)
  * [actionElement](documentation/developers-and-admins/customization/extend-the-administration-interface/ui-form-components/reference-react-input-components#ReferenceReactinputcomponents-inputActionElement)
  * [explanationText](documentation/developers-and-admins/customization/extend-the-administration-interface/ui-form-components/reference-react-input-components#ReferenceReactinputcomponents-inputExplanationText)
  * [labelDisabledTooltip](documentation/developers-and-admins/customization/extend-the-administration-interface/ui-form-components/reference-react-input-components#ReferenceReactinputcomponents-inputLabelDisabledTooltip)
  * [labelIcon](documentation/developers-and-admins/customization/extend-the-administration-interface/ui-form-components/reference-react-input-components#ReferenceReactinputcomponents-inputLabelIcon)
  * [labelIconTooltip](documentation/developers-and-admins/customization/extend-the-administration-interface/ui-form-components/reference-react-input-components#ReferenceReactinputcomponents-inputLabelIconTooltip)
  * [markAsRequired](documentation/developers-and-admins/customization/extend-the-administration-interface/ui-form-components/reference-react-input-components#ReferenceReactinputcomponents-inputMarkAsRequired)
  * [inputRef](documentation/developers-and-admins/customization/extend-the-administration-interface/ui-form-components/reference-react-input-components#ReferenceReactinputcomponents-inputInputRef)
  * [Inherited properties](documentation/developers-and-admins/customization/extend-the-administration-interface/ui-form-components/reference-react-input-components#ReferenceReactinputcomponents-inputInheritedProperties)


**Type:** label?: string
Element label.
**Type:** invalid?: boolean
Displays the specified validation message and changes element style to indicate incorrect input.
**Type:** value?: string | number
Sets the value of the input.
**Type:** type?: ‘text’ | ‘password’ | ‘email’ | ‘number’
Sets the [HTML5 input type](https://www.w3schools.com/html/html_form_input_types.asp). 
**Type:** validationMessage?: string
Sets the validation message displayed when the component contains invalid input.
**Type:** actionElement?: React.ReactNode
Specifies an element rendered inside the input.
JS
Copy
```
actionElement={<Icon name={ open ? 'xp-chevron-up' : 'xp-chevron-down'} />}
```

**Type:** explanationText?: string
Sets text displayed directly under the input. Used to clarify input requirements to users.
**Type:** labelDisabledTooltip?: string
Sets label text used when the input is disabled.
**Type:** labelIcon?: IconName
Sets the icon rendered next to the label.
**Type:** labelIconTooltip?: string
Sets the tooltip text of the icon rendered next to the label.
**Type:** markAsRequired?: boolean
Indicates if the input is required. If true, renders a small red asterisk in front of the component label text.
**Type:** inputRef?: RefObject<HTMLInputElement>
Text input [ref](https://reactjs.org/docs/refs-and-the-dom.html).
The component also inherits the following properties from [HTMLInputElement](https://developer.mozilla.org/en-US/docs/Web/API/HTMLInputElement).
  * onChange
  * disabled
  * readOnly
  * name
  * onClick
  * onKeyPress
  * min
  * max
  * id
  * placeholder
  * tabIndex
  * onBlur


**onChange event Type:** string
Drop-down menu. The element defines its options by its children.
The selector component has the following properties:
  * [label](documentation/developers-and-admins/customization/extend-the-administration-interface/ui-form-components/reference-react-input-components#ReferenceReactinputcomponents-selectLabel)
  * [invalid](documentation/developers-and-admins/customization/extend-the-administration-interface/ui-form-components/reference-react-input-components#ReferenceReactinputcomponents-selectInvalid)
  * [value](documentation/developers-and-admins/customization/extend-the-administration-interface/ui-form-components/reference-react-input-components#ReferenceReactinputcomponents-selectValue)
  * [maxContentHeight](documentation/developers-and-admins/customization/extend-the-administration-interface/ui-form-components/reference-react-input-components#ReferenceReactinputcomponents-selectMaxContentHeight)
  * [onChange](documentation/developers-and-admins/customization/extend-the-administration-interface/ui-form-components/reference-react-input-components#ReferenceReactinputcomponents-selectOnChange)
  * [validationMessage](documentation/developers-and-admins/customization/extend-the-administration-interface/ui-form-components/reference-react-input-components#ReferenceReactinputcomponents-selectValidationMessage)
  * [explanationText](documentation/developers-and-admins/customization/extend-the-administration-interface/ui-form-components/reference-react-input-components#ReferenceReactinputcomponents-selectExplanationText)
  * [labelDisabledTooltip](documentation/developers-and-admins/customization/extend-the-administration-interface/ui-form-components/reference-react-input-components#ReferenceReactinputcomponents-selectLabelDisabledTooltip)
  * [labelIcon](documentation/developers-and-admins/customization/extend-the-administration-interface/ui-form-components/reference-react-input-components#ReferenceReactinputcomponents-selectLabelIcon)
  * [labelIconTooltip](documentation/developers-and-admins/customization/extend-the-administration-interface/ui-form-components/reference-react-input-components#ReferenceReactinputcomponents-selectLabelIconTooltip)
  * [markAsRequired](documentation/developers-and-admins/customization/extend-the-administration-interface/ui-form-components/reference-react-input-components#ReferenceReactinputcomponents-selectMarkAsRequired)
  * [inputRef](documentation/developers-and-admins/customization/extend-the-administration-interface/ui-form-components/reference-react-input-components#ReferenceReactinputcomponents-selectInputRef)
  * [Inherited properties](documentation/developers-and-admins/customization/extend-the-administration-interface/ui-form-components/reference-react-input-components#ReferenceReactinputcomponents-selectInheritedProperties)


**Type:** label?: string
Element label.
**Type:** invalid?: boolean
Displays the specified validation message and changes element style to indicate incorrect input.
**Type:** value?: string | number
Sets the value of the input.
**Type:** maxContentHeight?: string
**Type:** (value?: string) => void
Allows the consumer to bind an _onChange_ event handler.
**Type:** validationMessage?: string
Sets the validation message displayed when the component contains invalid input.
**Type:** explanationText?: string
Sets text displayed directly under the input. Used to clarify input requirements to users.
**Type:** labelDisabledTooltip?: string
Sets label text used when the input is disabled.
**Type:** labelIcon?: IconName
Sets the icon rendered next to the label.
**Type:** labelIconTooltip?: string
Sets the tooltip text of the icon rendered next to the label.
**Type:** markAsRequired?: boolean
Indicates if the input is required. If true, renders a small red asterisk in front of the component label text.
**Type:** inputRef?: RefObject<HTMLInputElement>
Text input [ref](https://reactjs.org/docs/refs-and-the-dom.html).
The component also inherits the following properties from [HTMLInputElement](https://developer.mozilla.org/en-US/docs/Web/API/HTMLInputElement).
  * disabled
  * readOnly
  * name
  * onClick
  * onKeyPress
  * min
  * max
  * id
  * placeholder
  * tabIndex
  * onBlur Use a custom property to send a collection of options from the back end to the client. For example:


C#
Copy
```
using System.Linq;
using System.Threading.Tasks;

using CMS.Membership;

using Kentico.Xperience.Admin.Base.Forms;

public class DropDownOption
{
    public string Value { get; set; }
    public string Text { get; set; }
}

public class MyComponentClientProperties : FormComponentClientProperties<string>
{
    public IEnumerable<DropDownOption> Options { get; set; }
}

public class MyFormComponent : FormComponent<MyComponentClientProperties, string>
{
    protected override Task ConfigureClientProperties(MyComponentClientProperties clientProperties)
    {
        clientProperties.Options = UserInfo.Provider.Get()
                                .GetEnumerableTypedResult()
                                .Select(user => new DropDownOption()
        {
            Value = user.UserID.ToString(),
            Text = user.UserName
        });

        return base.ConfigureClientProperties(clientProperties);
    }
}
```

On the client, map the collection to `MenuItem` components passed as children to the selector. This ensures a consistent appearance.
JS
Copy
```
import { MenuItem, Select } from '@kentico/xperience-admin-components';
import { FormComponentProps } from '@kentico/xperience-admin-base';

export interface DropDownOption{
    value: string;
    text: string;
}

export interface MyFormComponentProps extends FormComponentProps {
    options : DropDownOption[];
}

export const MyFormComponent = (props: MyFormComponentProps) => {

    return (
        <Select onChange={(val) => props.onChange?.(val)}>
            {props.options?.map((item, index) => {
                return <MenuItem
                    primaryLabel={item.text}
                    value={item.value}
                    key={index}
                />;
            })}
        </Select>
    );
}
```

## RadioGroup
**onChange event Type:** string
Radio button group selector. Allows single selection. Uses RadioButton components for selectable options.
The component has the following properties:
  * [label](documentation/developers-and-admins/customization/extend-the-administration-interface/ui-form-components/reference-react-input-components#ReferenceReactinputcomponents-rgLabel)
  * [invalid](documentation/developers-and-admins/customization/extend-the-administration-interface/ui-form-components/reference-react-input-components#ReferenceReactinputcomponents-rgInvalid)
  * [value](documentation/developers-and-admins/customization/extend-the-administration-interface/ui-form-components/reference-react-input-components#ReferenceReactinputcomponents-rgValue)
  * [size](documentation/developers-and-admins/customization/extend-the-administration-interface/ui-form-components/reference-react-input-components#ReferenceReactinputcomponents-rgSize)
  * [disabled](documentation/developers-and-admins/customization/extend-the-administration-interface/ui-form-components/reference-react-input-components#ReferenceReactinputcomponents-rgDisabled)
  * [children](documentation/developers-and-admins/customization/extend-the-administration-interface/ui-form-components/reference-react-input-components#ReferenceReactinputcomponents-rgChildren)
  * [onChange](documentation/developers-and-admins/customization/extend-the-administration-interface/ui-form-components/reference-react-input-components#ReferenceReactinputcomponents-rgOnChange)
  * [validationMessage](documentation/developers-and-admins/customization/extend-the-administration-interface/ui-form-components/reference-react-input-components#ReferenceReactinputcomponents-rgValidationMessage)
  * [explanationText](documentation/developers-and-admins/customization/extend-the-administration-interface/ui-form-components/reference-react-input-components#ReferenceReactinputcomponents-rgExplanationText)
  * [labelDisabledTooltip](documentation/developers-and-admins/customization/extend-the-administration-interface/ui-form-components/reference-react-input-components#ReferenceReactinputcomponents-rgLabelDisabledTooltip)
  * [labelIcon](documentation/developers-and-admins/customization/extend-the-administration-interface/ui-form-components/reference-react-input-components#ReferenceReactinputcomponents-rgLabelIcon)
  * [labelIconTooltip](documentation/developers-and-admins/customization/extend-the-administration-interface/ui-form-components/reference-react-input-components#ReferenceReactinputcomponents-rgLabelIconTooltip)
  * [markAsRequired](documentation/developers-and-admins/customization/extend-the-administration-interface/ui-form-components/reference-react-input-components#ReferenceReactinputcomponents-rgMarkAsRequired)


**Type:** label: string
Element label.
**Type:** invalid?: boolean
Displays the specified validation message and changes element style to indicate incorrect input.
**Type:** value?: string
Sets the value of the input.
**Type:** size?: RadioGroupSize
Sets the option size.
**Type:** disabled?: boolean
Indicates disabled status.
**Type:** onChange?: (value: string) => void
Occurrs when the component state changes. `value` contains `RadioButton.value` of the current selection.
**Type:** children: React.ReactNode
A collection of `RadioButton` components representing selectable options.
**Type:** validationMessage?: string
Sets the validation message displayed when the component contains invalid input.
**Type:** explanationText?: string
Sets text displayed directly under the input. Used to clarify input requirements to users.
**Type:** labelDisabledTooltip?: string
Sets label text used when the input is disabled.
**Type:** labelIcon?: IconName
Sets the icon rendered next to the label.
**Type:** labelIconTooltip?: string
Sets the tooltip text of the icon rendered next to the label.
**Type:** markAsRequired?: boolean
Indicates if the input is required. If true, renders a small red asterisk in front of the component label text.
Use a custom property to send a collection of options from the back end. See the [select component](documentation/developers-and-admins/customization/extend-the-administration-interface/ui-form-components/reference-react-input-components#ReferenceReactinputcomponents-SelectComponent) for an example.
On the client, pass the collection of options as children using the `RadioButton` component.
JS
Copy
```
import { RadioButton, RadioGroup } from '@kentico/xperience-admin-components';

export const MyFormComponent = (props: MyFormComponentProps) => {

    return (
        <RadioGroup label='Choose' onChange={(val) => props.onChange?.(val)}>
            <RadioButton value='Cat'> Cat </RadioButton>
            <RadioButton value='Dog'> Dog </RadioButton>
        </RadioGroup>
    );
}
```

## RichTextEditor
**onChange event Type:** string
[Rich text editor](documentation/business-users/rich-text-editor) built using Froala WYSIWYG.
## Textarea
**onChange event Type:** React.ChangeEventHandler<HTMLTextAreaElement>
A text area.
The selector component has the following properties:
  * [label](documentation/developers-and-admins/customization/extend-the-administration-interface/ui-form-components/reference-react-input-components#ReferenceReactinputcomponents-areaLabel)
  * [value](documentation/developers-and-admins/customization/extend-the-administration-interface/ui-form-components/reference-react-input-components#ReferenceReactinputcomponents-areaValue)
  * [invalid](documentation/developers-and-admins/customization/extend-the-administration-interface/ui-form-components/reference-react-input-components#ReferenceReactinputcomponents-areaInvalid)
  * [validationMessage](documentation/developers-and-admins/customization/extend-the-administration-interface/ui-form-components/reference-react-input-components#ReferenceReactinputcomponents-areaValidationMessage)
  * [minRows](documentation/developers-and-admins/customization/extend-the-administration-interface/ui-form-components/reference-react-input-components#ReferenceReactinputcomponents-areaMinRows)
  * [maxRows](documentation/developers-and-admins/customization/extend-the-administration-interface/ui-form-components/reference-react-input-components#ReferenceReactinputcomponents-areaMaxRows)
  * [explanationText](documentation/developers-and-admins/customization/extend-the-administration-interface/ui-form-components/reference-react-input-components#ReferenceReactinputcomponents-areaExplanationText)
  * [labelDisabledTooltip](documentation/developers-and-admins/customization/extend-the-administration-interface/ui-form-components/reference-react-input-components#ReferenceReactinputcomponents-areaLabelDisabledTooltip)
  * [labelIcon](documentation/developers-and-admins/customization/extend-the-administration-interface/ui-form-components/reference-react-input-components#ReferenceReactinputcomponents-areaLabelIcon)
  * [labelIconTooltip](documentation/developers-and-admins/customization/extend-the-administration-interface/ui-form-components/reference-react-input-components#ReferenceReactinputcomponents-areaLabelIconTooltip)
  * [markAsRequired](documentation/developers-and-admins/customization/extend-the-administration-interface/ui-form-components/reference-react-input-components#ReferenceReactinputcomponents-areaMarkAsRequired)
  * [textAreaRef](documentation/developers-and-admins/customization/extend-the-administration-interface/ui-form-components/reference-react-input-components#ReferenceReactinputcomponents-areaTextAreaRef)


**Type:** label?: string
Element label.
**Type:** value?: string
Sets the value of the input.
**Type:** invalid?: boolean
Displays the specified validation message and changes element style to indicate incorrect input.
**Type:** validationMessage?: string
Sets the validation message displayed when the component contains invalid input.
**Type:** minRows?: number
Specifies the visible height of a text area, in lines.
**Type:** maxRows?: number
Specifies the visible height of a text area, in lines.
**Type:** explanationText?: string
Sets text displayed directly under the input. Used to clarify input requirements to users.
**Type:** labelDisabledTooltip?: string
Sets label text used when the input is disabled.
**Type:** labelIcon?: IconName
Sets the icon rendered next to the label.
**Type:** labelIconTooltip?: string
Sets the tooltip text of the icon rendered next to the label.
**Type:** markAsRequired?: boolean
Indicates if the input is required. If true, renders a small red asterisk in front of the component label text.
**Type:** textAreaRef?: RefObject<HTMLTextAreaElement>
Text area input [ref](https://reactjs.org/docs/refs-and-the-dom.html).
### Inherited properties
The component also inherits the following properties from [HTMLTextAreaElement](https://developer.mozilla.org/en-US/docs/Web/API/HTMLTextAreaElement).
  * onChange
  * disabled
  * readOnly
  * name
  * onClick
  * onKeyPress
  * id
  * placeholder