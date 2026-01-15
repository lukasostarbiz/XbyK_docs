# Define macro fields
  * [ Copy page link ](documentation/developers-and-admins/configuration/macro-expressions/define-macro-fields#) | [Get HelpService ID](documentation/developers-and-admins/configuration/macro-expressions/define-macro-fields#)
Core MVC 5


[✖](documentation/developers-and-admins/configuration/macro-expressions/define-macro-fields# "Close page link panel") [Copy to clipboard](documentation/developers-and-admins/configuration/macro-expressions/define-macro-fields#)
Macro fields need to be defined per [macro resolver](documentation/developers-and-admins/configuration/macro-expressions/macro-resolvers). Resolvers are system components that ensure the processing of macros. By adding fields into specific resolvers, you make them available for the functionality handled by that particular resolver.
You can register macro fields into resolvers:
  * [globally](documentation/developers-and-admins/configuration/macro-expressions/define-macro-fields#Definemacrofields-global) – makes the fields available everywhere the particular resolver is used (see the [resolver reference](documentation/developers-and-admins/configuration/macro-expressions/macro-resolvers) for details)
  * [per administration UI page](documentation/developers-and-admins/configuration/macro-expressions/define-macro-fields#Definemacrofields-perpage) – makes the fields resolve only within specific [administration UI pages](documentation/developers-and-admins/customization/extend-the-administration-interface/ui-pages).


### Global registration 
Global registration makes the macro field available everywhere the particular resolver is used.
Register global fields during application startup:
  1. Create a [custom module class](documentation/developers-and-admins/customization/run-code-on-application-startup). 
     * See [Integrate custom code](documentation/developers-and-admins/customization/integrate-custom-code) for best practices related to adding custom code to Xperience.
  2. Override the module’s `OnInit` method.
  3. Obtain an instance of the register that corresponds to the macro resolver you want to extend (e.g., `UIFormMacroSourceRegister` for the resolver responsible for the UI forms of [object types](documentation/developers-and-admins/customization/object-types)). 
     * See the [macro resolvers reference](documentation/developers-and-admins/configuration/macro-expressions/macro-resolvers) to see which registration classes belong to specific resolvers.


C#
Copy
```
public class MacroFieldsRegistrationModule : Module
{
    ...

    // Contains initialization code that is executed when the application starts
    protected override void OnInit()
    {
        base.OnInit();

        // Gets an instance of UIFormMacroSourceRegister
        var macroRegister = UIFormMacroSourceRegister.Instance;
    }
}
```

  1. Call `RegisterMacroField` on the macro register instance. The method takes a `MacroField` object which requires the following parameters: 
     * **name** – used to access the field from within macro expressions. For example: `{% MyMacroField %}`
     * **valueEvaluator** – a lambda expression with the logic to execute when the field is evaluated. Must return a value supported by macro expressions (scalars, [object types](documentation/developers-and-admins/customization/object-types), collections based on `IEnumerable`).


C#
Copy
```
protected override void OnInit()
{
    base.OnInit();

    // Gets an instance of UIFormMacroSourceRegister
    var macroRegister = UIFormMacroSourceRegister.Instance;

    // Registers a new "DefaultAdministrator" macro field that returns a UserInfo object that corresponds to the default 'administrator' account
    macroRegister.RegisterMacroField(new MacroField("DefaultAdministrator", () => UserInfo.Provider.Get("administrator")));
}
```

The macro field is now registered and available everywhere the extended resolver is used.
K#
**Example usage**
Copy
```
{% DefaultAdministrator.FirstName %}
```

### UI page registration 
UI page-specific field registration makes macro fields available only for specific pages in the administration interface.
The registration is done using [UI page extenders](documentation/developers-and-admins/customization/extend-the-administration-interface/ui-pages/ui-page-extenders) and the dedicated `RegisterMacroField` method.
  1. Create a new page extender and override the `ConfigurePage` method.
  2. Within the override, call `RegisterMacroField`. The method takes a `MacroField` object which requires the following parameters: 
     * **name** – used to access the field from within macro expressions. For example: `{% MyMacroField %}`
     * **valueEvaluator** – a lambda expression with the logic to execute when the field is evaluated. Must return a value supported by macro expressions (scalars, [object types](documentation/developers-and-admins/customization/object-types), collections based on `IEnumerable`).


C#
Copy
```
public override Task ConfigurePage()
{
    // Registers a new 'DefaultAdministrator' field that is only resolvable on the extended page
    Page.RegisterMacroField(new CMS.MacroEngine.MacroField("DefaultAdministrator", () => UserInfo.Provider.Get("administrator")));

    return base.ConfigurePage();
}
```

The macro field is now registered and available for fields on the given page in the administration.
K#
**Example usage**
Copy
```
{% DefaultAdministrator.FirstName %}
```