---
source: https://docs.kentico.com/documentation/developers-and-admins/configuration/macro-expressions/macro-syntax
scrape_date: 2026-01-22
---

  * [Home](/documentation)
  * [Developers and admins](/documentation/developers-and-admins)
  * [Configuration](/documentation/developers-and-admins/configuration)
  * [Macro expressions](/documentation/developers-and-admins/configuration/macro-expressions)
  * Macro syntax 


# Macro syntax
[Macro expressions](/documentation/developers-and-admins/configuration/macro-expressions) need to be enclosed in curly brackets and the percentage symbol: {% <expression> %}
Xperience provides an object-oriented language named **K#** , which defines the available macro expression syntax. The language is very similar to the C# programming language. This page provides an overview of the features available in K# and describes its differences from C#.
The syntax is **case-insensitive** – the system does not differentiate between upper and lower case letters in commands. However, letter case may have an effect when inputting constants and variables (for the purposes of comparisons and similar).
## Macro return values
When the system resolves a macro expression, the result is an object (value). Macros support the following types of objects:
  * Standard scalar C# types (int, double, string, DateTime, etc.).
  * Collections based on the `IEnumerable` interface.
  * [Object types](/documentation/developers-and-admins/api/database-table-api) (`UserInfo`, `MemberInfo`, etc.) – available for macros in the default values of object type editing forms
    * When the result of an expression is an object type, you can continue accessing its properties using conventional dot notation. For example: `UserInfo.UserName`


In most cases, the final result of a macro expression is automatically converted to a text (string) representation.
## Literals
To work with fixed values (literals) in macro expressions:
  * **numbers** – type numbers directly. You can use integers or doubles.
  * **text** – enclose text (string) values into quotes, for example: `"administrator"`
  * **booleans** – use the `true`and `false` keywords.
  * **date and time** – enclose date and time values into quotes. The date format depends on the application culture. For example: `"1/1/2020"`, `"9/3/2020 9:20:00 AM"`


Macro fields are equivalent to conventional get-only C# properties. They take no arguments and when resolved within a macro expression execute their specified getter logic. 
K#
**Use macro fields**
Copy
```
// Returns the current date and time in UTC
{% UtcNow %}
```

Xperience provides a set of macro fields that you can use in macro expressions, depending on the current location. See [Email templates](/documentation/developers-and-admins/digital-marketing-setup/email-templates) and [Macro resolvers](/documentation/developers-and-admins/configuration/macro-expressions/macro-resolvers) for more information.
You can also [register custom macro fields](/documentation/developers-and-admins/configuration/macro-expressions/define-macro-fields) for specific administration UI pages.
## Methods 
Macro methods perform specific tasks (run code) inside macro expressions. The recommended syntax for method calls is **infix** notation for the first argument. Prefix notation is also supported.
K#
**Use macro methods**
Copy
```
// Infix notation
// Checks for emptiness
{% "string".IsNullOrEmpty() %}

// Returns "Today is <current date and time>"
{% "Today is {0}".FormatString(UtcNow) %}

// Prefix notation
{% IsNullOrEmpty("string") %}
{% FormatString("Today is {0}", UtcNow) %}
```

Xperience provides a set of default methods that you can use in macro expressions. See: [Email templates](/documentation/developers-and-admins/digital-marketing-setup/email-templates) and [Macro resolvers](/documentation/developers-and-admins/configuration/macro-expressions/macro-resolvers)
## Operators
Macro syntax uses the same basic operators as C#.
The following table summarizes the differences in operator behavior that you need to keep in mind when writing macros.
Operator |  Examples |  Description  
---|---|---  
x.y |  “mystring”.IsNullOrEmpty() |  Accesses the members (methods, properties) of macro objects or namespaces.  
+ |  10+5 |  Adds two operands together. Adding is supported for numbers, and the following types:
  * **String + String** : Returns the concatenation as a _String_
  * **DateTime + TimeSpan** : Adds the _TimeSpan_ to the _DateTime_ and returns the result as _DateTime_
  * **TimeSpan + TimeSpan** : Returns the sum as a _TimeSpan_

String concatenation is the default option if none of the combinations above are detected – the operator returns the concatenation of the operands’ _String_ representations. For example, _{% “string” + 5 %}_ returns **string5**.  
- |  -10  
10-5 |  Unary - returns the numeric negation of a number.  
Binary - subtracts the second operand from the first. In addition to numeric types, you can subtract objects of the following types:
  * **DateTime - TimeSpan** : Subtracts the _TimeSpan_ from the _DateTime_ and returns the result as _DateTime_
  * **DateTime - DateTime** : Subtracts the second _DateTime_ from the first, and returns the difference as a _TimeSpan_
  * **TimeSpan - TimeSpan** : Returns the difference as a _TimeSpan_

  
==  
!= |  50 == 5*10 |  Operators that check for equality or inequality of the operands. Return a boolean value. Equality checks support all available object types, with the following special rules:
  * Empty strings are equal to _null_
  * Simple data types are equal to their string representation
  * _Info_ objects are equal to string constants that match the object’s display name or code name
  * Two  _Info_ objects are equal when they have the same object type and ID
  * GUID values are equal to the string representation of the GUID (always case insensitive)
  * Enumeration values are equal to constants of the enum’s underlying type (integer or string)

**Tip** : Use [macro parameters](#macro-parameters) to specify case sensitivity and the culture context in equality checks.  
<  
<=  
>  
>= |  10 < 5 |  Comparison operators that return boolean values: `<` – (true if the left operand is lesser than the right)  
`<=` – (true if the left operand is lesser than or equal to the right)  
`>` – (true if the left operand is greater than the right)  
`>=` – (true if the left operand is greater than or equal to the right) You can compare objects of the following types:
  * numeric (Int, Double)
  * string (comparison based on lexicographical order)
  * DateTime
  * TimeSpan

  
mod |  5 mod 2 |  Performs the [modulo operation](http://en.wikipedia.org/wiki/Modulo_operation) – computes the remainder after division of two integer operands.  
% |  30% |  In K#, the % character represents percentage values. You **cannot** use the % operator for the modulo operation. Adding the percentage sign converts the preceding number to a double equivalent (multiplies the number by 0.01). For example, _{% 30% %}_ returns **0.3**.  
?? |  |  Null-coalescing operator. Returns the left operand if the operand is not _null_ , otherwise returns the right operand. **Note** : Empty strings are not considered as null values by the operator.  
See [Reference of C# operators](https://docs.microsoft.com/en-us/dotnet/csharp/language-reference/operators/index) and look up a specific operator for more information.
## Compound expressions and declaring variables
The syntax allows you to write compound expressions containing any number of simple macro expressions. All but the last expression in a compound statement must end with a semicolon. The overall result of the compound expression is the result of the last expression.
Variables allow you to store and manipulate values inside macro expressions. You don’t need to declare variable types explicitly.
K#
**Declare variables**
Copy
```
// returns "12"
{% x = 5; x + 7 %}

// returns "10"
{% x = 5; y = 3; x += 2; x + y %}
```

Variables are scoped to the inputs where they are declared (for example a text field or the entire code editor of an email template), and are accessible from separate macro expressions within the input.
## Conditional statements
Use the `if` statement to create `if (<condition>) {<expression>}` expressions. The condition returns the value of _< expressions>_ if the condition is _true_ , and `null` if _false_.
K#
**Simple conditional statements**
Copy
```
// returns "z is less than 3"
{% z = 1; if (z<3) {"z is less than 3"} %}
```

To create conditions with a result for the false branch, use: `if (<condition>) {<expression>} else {<expression>}`
K#
**Branching conditional statements**
Copy
```
// returns "z is greater than or equal to 3"
{% z = 5; if (z<3) {"z is less than 3"} else {"z is greater than or equal to 3"} %}
```

The **ternary operator** enables the same behavior using a more concise notation: `<condition> ? <expression> : <expression>`
K#
**Ternary operator**
Copy
```
// returns "The second parameter is greater"
{% x=1; y=2; x > y ? "The first parameter is greater" : "The second parameter is greater" %}
```

## Iteration (loops)
K# provides several types of loop commands:
  * `while (<condition>) {<executed expressions>}`
  * `for (<init expression>; <condition>; <increment expression>) {<executed expressions>}`
  * `foreach (<variable> in <enumerable object>) {<executed expressions>}`


If a loop command is the last expression in a macro, the return value is a concatenated list containing the results of the sub-expressions executed by all iterations of the loop.
K#
**While loop example**
Copy
```
// returns "10"
{% z = 1; while (z<10) {++z}; z %}

// returns "2 3 4 5 6 7 8 9 10"
{% z = 1; while (z<10) {++z} %}
```

K#
**For loop example**
Copy
```
// returns "5"
{% z = 0; for (i = 0; i < 5; i++) { z += 1 }; z %}

// returns "1 2 3 4 5"
{% z = 0; for (i = 0; i < 5; i++) { z += 1 } %}
```

K#
**Foreach example**
Copy
```
// returns "HELLO"
{% z = ""; foreach (x in "hello") {z += x.toupper()}; z %}

// returns "H HE HEL HELL HELLO"
{% z = ""; foreach (x in "hello") {z += x.toupper()}%}
```

Use the `break` command to terminate loops. For nested loops, the command closes the innermost loop.
K#
Copy
```
// returns "1 2 3 4 5"
{% z = 0; while (z < 10) {if (z > 4) {break}; ++z} %}
```

The `continue` command skips to the next iteration of the current loop.
K#
Copy
```
// returns "0 1 2 4 5"
{% for (i=0; i<=5 ; i++) {if (i == 3) {continue}; i} %}
```

### Advanced loops
Do not use the automatic return value for loops that are nested inside compound expressions or another loop. Instead, choose one of the following approaches:
  * Use the `print` method to directly output the results of individual loop iterations
  * Concatenate the iteration results into a variable that you return at the end of the expression


K#
**Example - Nested loop with console output**
Copy
```
// Example in an email template displaying a content type with an 'ArticleAuthors' field,
// linking to Article content items with an 'AuthorName' field.
// Returns an HTML list of linked author names.
{%
    print("<ul>");
    foreach (author in ArticleAuthors) {
        print("<li>" + author.Data.AuthorName + "</li>")
    };
    print("</ul>");
%}
```

K#
**Example - Nested loop with concatenation into a variable**
Copy
```
// Example in an email template displaying a content type with an 'ArticleAuthors' field,
// linking to Article content items with an 'AuthorName' field.
// Returns an HTML list of linked author names.
{%
    result = "<ul>";
    foreach (author in ArticleAuthors) {
        result += "<li>" + author.Data.AuthorName + "</li>"
    };
    return result + "</ul>";
%}
```

## Open conditions and loops
When defining conditions or loops, you can leave the body of the loop/condition open and close it later in another macro expression. This allows you to apply the command to text content or HTML code placed between the macro expressions. Open commands can be particularly useful in [Email templates](/documentation/developers-and-admins/digital-marketing-setup/email-templates).
You can also nest additional macro expressions inside open loops or conditions.
K#
**Example**
Copy
```
// Example in an email template displaying a content type with a 'SocialPlatforms' field,
// linking to social platform content items with additional fields.
// Returns a list of HTML image links.
{% foreach (link in SocialPlatforms) { %}
    <a href="{% link.Data.SocialLinkUrl %}" target="_blank">
        <img src="{% link.Data.SocialLinkIcon[0].Data.ImageFile.Url %}" alt="{% link.Data.SocialLinkTitle %}" title="{% link.Data.SocialLinkTitle %}">
    </a>
{% } %}
```

## Return command
Use the `return` command to terminate the processing of a macro, and set the attached expression as the macro’s final result. You can use the return command inside loops, or anywhere in compound macro expressions.
K#
**Example**
Copy
```
// returns green
{% "red"; "yellow"; return "green"; "blue" %}

// returns "ignore the loop"
{% z = ""; foreach (x in "hello") {return "ignore the loop"; z += x } %}
```

## Indexing
K# supports indexing for collections and objects that serve as containers for other data:
  * **String** (returns the character at the specified index)
  * **IEnumerable** collections, e.g., when retrieving linked content items in the fields of email content types in [Email templates](/documentation/developers-and-admins/digital-marketing-setup/email-templates) (returns the object at the specified index in the collection)


K#
**Example**
Copy
```
// returns "e"
{% "hello"[1] %}

// Gets the first content item linked in the 'LinkedImages' field,
// and then the URL of the file stored in the content item's 'ImageFile'
{% LinkedImages[0].Data.ImageFile.Url %}
```

## Comments
To add explanatory text inside macro expressions, use one-line, multi-line or inline comments.
K#
**Example**
Copy
```
{%
// This is a one-line comment. Initiated by two forward slashes, spans across one full line.

/*
This is a multi-line comment.
Opened by a forward slash-asterisk sequence and closed with the asterisk-forward slash sequence.
Can span across any number of lines.
*/

x = 5; y = 3; /* This is an inline comment nested in the middle of an expression. */ x+= 2; x + y
%}
```

## Macro parameters
Macro parameters allow you to modify how the system resolves individual expressions. To append parameters to macro expressions, use the following syntax:
K#
Copy
```
{% <expression>|(<parameter>)<value> %}
```

**Note** : The | separator must be placed directly between the macro expression and the parameter **without any whitespace characters**.
You can add multiple parameters to a single expression. Use backslashes to escape the `|` separator in parameter values if necessary. The following macro parameters are available:
Parameter |  Example |  Description  
---|---|---  
default |  {% |(default)N|A %} |  Sets a default value that the macro returns if the result of the expression is an empty value.  
culture |  {% |(culture)en-us %} |  Sets the culture code used when formatting numbers and dates in the macro result.  
casesensitive |  {% |(casesensitive)true %} |  Determines whether string comparisons and other operations inside the macro expression are case sensitive. _False_ by default.  
encode |  {% |(encode)true %} |  Enables [HTML encoding](https://www.w3schools.com/html/html_charset.asp) for the result of the macro – converts reserved HTML characters to equivalent character entities.  
![]()
[]()[]()
