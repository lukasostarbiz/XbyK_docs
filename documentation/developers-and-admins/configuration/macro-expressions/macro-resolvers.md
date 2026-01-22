---
source: https://docs.kentico.com/documentation/developers-and-admins/configuration/macro-expressions/macro-resolvers
scrape_date: 2026-01-22
---

  * [Home](/documentation)
  * [Developers and admins](/documentation/developers-and-admins)
  * [Configuration](/documentation/developers-and-admins/configuration)
  * [Macro expressions](/documentation/developers-and-admins/configuration/macro-expressions)
  * Macro resolvers 


# Macro resolvers
Macro resolution within Xperience is handled by system components known as macro resolvers. A resolver is responsible for processing entered macro expressions and outputting the result. The resolution process can be simplified into the following steps:
  1. The resolver parses the macro expression to identify its components ([macro fields](/documentation/developers-and-admins/configuration/macro-expressions/macro-syntax#Macrosyntax-macrofield), [macro methods](/documentation/developers-and-admins/configuration/macro-expressions/macro-syntax#Macrosyntax-macromethod), declared variables, control statements).
  2. For each identified macro field or method, the resolver runs the code [registered with the component](/documentation/developers-and-admins/configuration/macro-expressions/define-macro-fields). Results are substituted into the main expression.
  3. The resolver then evaluates the main expression and outputs the result.


The system contains multiple macro resolvers, each responsible for a particular feature set. This separation ensures that a resolver supports only macros relevant to the feature for which it is responsible. The set of supported macro fields and methods is “taught” to each resolver on application startup.
Some macro resolvers are open to customization. Developers can “teach” a resolver additional [macro fields](/documentation/developers-and-admins/configuration/macro-expressions/define-macro-fields) by registering them via specific classes. See the following reference for the extension points of individual resolvers.
## Resolver reference
### UI forms macro resolver
**Extension point:** `UIFormMacroSourceRegister` – see [Define macro fields](/documentation/developers-and-admins/configuration/macro-expressions/define-macro-fields) to learn how to register additional macro fields into the resolver.
This resolver is available when defining fields for [UI forms](/documentation/developers-and-admins/customization/object-types) in the **Modules** application. Navigate to the **Modules** application → _select a module_ → **Classes** → _select a class_ → **UI forms**.
**Handles macro resolution in the following field properties:**
  * Default value


The following macro expressions are available by default:
Macro fields |  Return type |  Parameters |  Example |  Description  
---|---|---|---|---  
Now  |  DateTime |  - |  `{% Now %}` |  Returns a `DateTime` string with the current server time.  
UtcNow |  DateTime |  - |  `{% UtcNow %}` |  Returns a `DateTime` string with the current UTC time.  
Today |  DateTime |  - |  `{% Today %}` |  Returns a `DateTime` string set to today’s date, with the time component set to 00:00:00 (with formatting dependent on the application’s UI culture).  
Macro methods |  |  |  |   
IsNullOrWhiteSpace |  bool | 
  1. string to test

|  `{% "mystringexpressionresult".IsNullOrWhiteSpace() %}` |  Tests whether the given string is null or contains only whitespace.  
IsNullOrEmpty |  bool | 
  1. string to test

|  `{% "mystringexpressionresult".IsNullOrEmpty() %}` |  Tests whether the given string is null or empty.  
FormatString |  string | 
  1. format string
  2. formatting params

|  `{% "It is now {0}".FormatString(Now) %}` |  Replaces all formatting expressions in a string using the text equivalents specified in the second parameter. Based on [composite formatting](https://docs.microsoft.com/en-us/dotnet/standard/base-types/composite-formatting).  
![]()
[]()[]()
