---
source: https://docs.kentico.com/documentation/developers-and-admins/customization/extend-the-administration-interface/ui-form-components/reference-react-input-components
scrape_date: 2026-01-26
---

  * [Home](/documentation)
  * [Developers and admins](/documentation/developers-and-admins)
  * [Customization](/documentation/developers-and-admins/customization)
  * [Extend the administration interface](/documentation/developers-and-admins/customization/extend-the-administration-interface)
  * [UI form components](/documentation/developers-and-admins/customization/extend-the-administration-interface/ui-form-components)
  * Reference - React input components 


# Reference - React input components
This page provides an overview of input components from the `@kentico/xperience-admin-components` package that can be used to define editing interfaces for [admin UI form components](/documentation/developers-and-admins/customization/extend-the-administration-interface/ui-form-components).
  * [Checkbox](#checkbox)
  * [Input](#input)
  * [Select](#anchor-referencereactinputcomponents-selectcomponent-select)
  * [RadioGroup](#radiogroup)
  * [RichTextEditor](#richtexteditor)
  * [Textarea](#textarea)


## Checkbox
**onChange event Type:** React.ChangeEventHandler<HTMLInputElement>
A binary state input.
The checkbox component has the following configurable properties:
  * [label](#ReferenceReactinputcomponents-checkboxLabel)
  * [size](#ReferenceReactinputcomponents-checkboxSize)
  * [checked](#ReferenceReactinputcomponents-checkboxChecked)
  * [indetermined](#ReferenceReactinputcomponents-checkboxIndetermined)
  * [tooltipText](#ReferenceReactinputcomponents-checkboxTooltipText)
  * [onChange](#ReferenceReactinputcomponents-checkboxOnChange)
  * [Inherited properties](#ReferenceReactinputcomponents-checkboxInherited)


**Type:** label?: string 
Sets the label for the checkbox.
[![Label property demonstration](/docsassets/documentation/reference-react-input-components/CheckboxLabel.png)](/docsassets/documentation/reference-react-input-components/CheckboxLabel.png)
JS
**Example**
Copy
```
import { Checkbox } from "@kentico/xperience-admin-components";

<Checkbox label="I'm a checkbox!" />
```

**Type:** size?: CheckboxSize 
Sets the size of the checkbox to one of three sizes defined by the `CheckboxSize` enum. Defaults to `CheckboxSize.M` if not set.
[![Size property demonstration](/docsassets/documentation/reference-react-input-components/CheckboxSize.png)](/docsassets/documentation/reference-react-input-components/CheckboxSize.png)
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
[![Check property demonstration](/docsassets/documentation/reference-react-input-components/CheckboxChecked.png)](/docsassets/documentation/reference-react-input-components/CheckboxChecked.png)
JS
**Example**
Copy
```
import { Checkbox } from "@kentico/xperience-admin-components";

<Checkbox label="Label" checked />
```

**Type:** indetermined?: boolean
Indicates an indeterminate state. Defaults to _false_.
[![Indetermined property demonstration](/docsassets/documentation/reference-react-input-components/CheckboxIndeterminate.png)](/docsassets/documentation/reference-react-input-components/CheckboxIndeterminate.png)
JS
**Example**
Copy
```
import { Checkbox } from "@kentico/xperience-admin-components";

<Checkbox label="Label" indetermined />
```

**Type:** tooltipText?: string
Sets the tooltip for the checkbox.
[![tooltipText property demonstration](/docsassets/documentation/reference-react-input-components/CheckboxTooltip.png)](/docsassets/documentation/reference-react-input-components/CheckboxTooltip.png)
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
  * [label](#ReferenceReactinputcomponents-inputLabel)
  * [invalid](#ReferenceReactinputcomponents-inputInvalid)
  * [value](#ReferenceReactinputcomponents-inputValue)
  * [type](#ReferenceReactinputcomponents-inputType)
  * [validationMessage](#ReferenceReactinputcomponents-inputValidationMessage)
  * [actionElement](#ReferenceReactinputcomponents-inputActionElement)
  * [explanationText](#ReferenceReactinputcomponents-inputExplanationText)
  * [labelDisabledTooltip](#ReferenceReactinputcomponents-inputLabelDisabledTooltip)
  * [labelIcon](#ReferenceReactinputcomponents-inputLabelIcon)
  * [labelIconTooltip](#ReferenceReactinputcomponents-inputLabelIconTooltip)
  * [markAsRequired](#ReferenceReactinputcomponents-inputMarkAsRequired)
  * [inputRef](#ReferenceReactinputcomponents-inputInputRef)
  * [Inherited properties](#ReferenceReactinputcomponents-inputInheritedProperties)


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
  * [label](#ReferenceReactinputcomponents-selectLabel)
  * [invalid](#ReferenceReactinputcomponents-selectInvalid)
  * [value](#ReferenceReactinputcomponents-selectValue)
  * [maxContentHeight](#ReferenceReactinputcomponents-selectMaxContentHeight)
  * [onChange](#ReferenceReactinputcomponents-selectOnChange)
  * [validationMessage](#ReferenceReactinputcomponents-selectValidationMessage)
  * [explanationText](#ReferenceReactinputcomponents-selectExplanationText)
  * [labelDisabledTooltip](#ReferenceReactinputcomponents-selectLabelDisabledTooltip)
  * [labelIcon](#ReferenceReactinputcomponents-selectLabelIcon)
  * [labelIconTooltip](#ReferenceReactinputcomponents-selectLabelIconTooltip)
  * [markAsRequired](#ReferenceReactinputcomponents-selectMarkAsRequired)
  * [inputRef](#ReferenceReactinputcomponents-selectInputRef)
  * [Inherited properties](#ReferenceReactinputcomponents-selectInheritedProperties)


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
  * [label](#ReferenceReactinputcomponents-rgLabel)
  * [invalid](#ReferenceReactinputcomponents-rgInvalid)
  * [value](#ReferenceReactinputcomponents-rgValue)
  * [size](#ReferenceReactinputcomponents-rgSize)
  * [disabled](#ReferenceReactinputcomponents-rgDisabled)
  * [children](#ReferenceReactinputcomponents-rgChildren)
  * [onChange](#ReferenceReactinputcomponents-rgOnChange)
  * [validationMessage](#ReferenceReactinputcomponents-rgValidationMessage)
  * [explanationText](#ReferenceReactinputcomponents-rgExplanationText)
  * [labelDisabledTooltip](#ReferenceReactinputcomponents-rgLabelDisabledTooltip)
  * [labelIcon](#ReferenceReactinputcomponents-rgLabelIcon)
  * [labelIconTooltip](#ReferenceReactinputcomponents-rgLabelIconTooltip)
  * [markAsRequired](#ReferenceReactinputcomponents-rgMarkAsRequired)


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
Use a custom property to send a collection of options from the back end. See the [select component](#ReferenceReactinputcomponents-SelectComponent) for an example.
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
[Rich text editor](/documentation/business-users/rich-text-editor) built using Froala WYSIWYG.
## Textarea
**onChange event Type:** React.ChangeEventHandler<HTMLTextAreaElement>
A text area.
The selector component has the following properties:
  * [label](#ReferenceReactinputcomponents-areaLabel)
  * [value](#ReferenceReactinputcomponents-areaValue)
  * [invalid](#ReferenceReactinputcomponents-areaInvalid)
  * [validationMessage](#ReferenceReactinputcomponents-areaValidationMessage)
  * [minRows](#ReferenceReactinputcomponents-areaMinRows)
  * [maxRows](#ReferenceReactinputcomponents-areaMaxRows)
  * [explanationText](#ReferenceReactinputcomponents-areaExplanationText)
  * [labelDisabledTooltip](#ReferenceReactinputcomponents-areaLabelDisabledTooltip)
  * [labelIcon](#ReferenceReactinputcomponents-areaLabelIcon)
  * [labelIconTooltip](#ReferenceReactinputcomponents-areaLabelIconTooltip)
  * [markAsRequired](#ReferenceReactinputcomponents-areaMarkAsRequired)
  * [textAreaRef](#ReferenceReactinputcomponents-areaTextAreaRef)


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


![]()
[]()[]()
