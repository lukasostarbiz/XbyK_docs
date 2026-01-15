# Prepare data writers
  * [ Copy page link ](modules/data-protection/prepare-data-writers#) | [Get HelpService ID](modules/data-protection/prepare-data-writers#)
Core MVC 5


[✖](modules/data-protection/prepare-data-writers# "Close page link panel") [Copy to clipboard](modules/data-protection/prepare-data-writers#)
Some data protection laws require all of a visitor’s tracked personal data to be gathered and delivered to them upon request. A data collector is necessary to implement such functionality in Xperience by Kentico.
Data collectors find other objects associated with a provided set of identities from the Identity collector. In this example, the identities are contacts, and the data collector will find accounts, consent agreements, form data, activities, and other auxiliary data associated with those contacts.
This example includes _accounts_ and other related objects which are considered obsolete as of version **30.9.0**.
More details are available in the [changelog](documentation/changelog#refresh-august-25-2025).
If you are using a newer version, you can remove the affected code from these samples.
## Create data writers
Based on whether data collection is requested through the **Data portability** or **Right to access** tab in the Xperience UI, it will invoke registered personal data collectors with the `outputFormat` parameter set to a constant string value, either `PersonalDataFormat.HUMAN_READABLE` or `PersonalDataFormat.MACHINE_READABLE`.
Data collectors are meant to output a string result.
While it is possible to include logic to print in different formats within the code that collects the data, separating it out into dedicated writer classes keeps the code clearer and less redundant.
### Lay the groundwork
Before you define the first writer, create a class that represents a column that should be collected and displayed, as well as an interface that both writers will implement.
  1. In the _TrainingGuides.Web_ /Features/_DataProtection/Collectors_ folder, create a `CollectedColumn` class.
  2. Add properties to hold the display name and a code name.
  3. Define a constructor that takes both properties as parameters.
C#
**CollectedColumn.cs**
Copy
```
namespace TrainingGuides.Web.Features.DataProtection.Collectors;

public class CollectedColumn
{
    public string Name { get; }

    public string DisplayName { get; }

    public CollectedColumn(string name, string displayName)
    {
        Name = name;
        DisplayName = displayName;
    }
}
```

  4. Create a _Writers_ subfolder in _~/Features/DataProtection_ and add an interface that both the human and machine-readable writer classes can implement.
  5. Similar to the [example in the documentation](documentation/developers-and-admins/data-protection/personal-data-collection#implement-writer-classes), add signatures for void methods that create beginning and ending sections and info objects.
  6. Create an additional method signature for writing values.
  7. Add a method signature for a string method that outputs the result of the writer’s operations.
C#
**IPersonalDataWriter.cs**
Copy
```
using CMS.DataEngine;
using TrainingGuides.Web.Features.DataProtection.Collectors;

namespace TrainingGuides.Web.Features.DataProtection.Writers;

public interface IPersonalDataWriter : IDisposable
{
    void WriteStartSection(string sectionName, string sectionDisplayName);
    void WriteBaseInfo(BaseInfo baseInfo, List<CollectedColumn> columns, Func<string, object, object>? valueTransformationFunction = null);
    void WriteSectionValue(string sectionName, string sectionDisplayName, string value);
    void WriteEndSection();
    string GetResult();
}
```



### Add a human-readable writer
  1. In the same folder, define a `HumanReadablePersonalDataWriter` that implements the `IPersonalDataWriter` interface.
  2. Define it similarly to the [documentation example](documentation/developers-and-admins/data-protection/personal-data-collection#implement-writer-classes), utilizing a private string builder property.
  3. Add a method called `WriteSectionValue` that prints a key and value, ignoring the section name, which the machine-readable counterpart can utilize.
  4. Leave the `Dispose` method empty.
You don’t need the method here, but it is important for the machine-readable data writer covered in the [next step](modules/data-protection/prepare-data-writers#create-a-machine-readable-writer).


C#
**HumanReadablePersonalDataWriter.cs**
Copy
```
using CMS.Base;
using CMS.DataEngine;
using System.Globalization;
using System.Text;
using TrainingGuides.Web.Features.DataProtection.Collectors;

namespace TrainingGuides.Web.Features.DataProtection.Writers;

public class HumanReadablePersonalDataWriter : IPersonalDataWriter
{
    private readonly StringBuilder stringBuilder;
    private int indentationLevel;
    private bool ignoreNewLine;

    private static readonly string decimalPrecision = new('#', 26);
    private static readonly string decimalFormat = "{0:0.00" + decimalPrecision + "}";

    public CultureInfo Culture { get; set; } = new CultureInfo(SystemContext.SYSTEM_CULTURE_NAME);

    public HumanReadablePersonalDataWriter()
    {
        stringBuilder = new StringBuilder();
        indentationLevel = 0;
        ignoreNewLine = false;
    }

    public void WriteStartSection(string sectionName, string sectionDisplayName)
    {
        ignoreNewLine = false;
        Indent();

        stringBuilder.AppendLine(sectionDisplayName + ": ");
        indentationLevel++;
    }

    private void Indent() => stringBuilder.Append('\t', indentationLevel);

    public void WriteBaseInfo(BaseInfo baseInfo, List<CollectedColumn> columns,
        Func<string, object, object>? valueTransformationFunction = null)
    {
        if (baseInfo == null)
        {
            throw new ArgumentNullException(nameof(baseInfo));
        }

        foreach (var column in columns)
        {
            string columnName = column.Name;
            string columnDisplayName = column.DisplayName;

            if (string.IsNullOrWhiteSpace(columnDisplayName) ||
                columnName.Equals(baseInfo.TypeInfo.IDColumn, StringComparison.Ordinal) ||
                columnName.Equals(baseInfo.TypeInfo.GUIDColumn, StringComparison.Ordinal))
            {
                continue;
            }

            object value = baseInfo.GetValue(columnName);

            if (value == null)
            {
                continue;
            }

            if (valueTransformationFunction != null)
            {
                value = valueTransformationFunction(columnName, value);
            }

            WriteKeyValue(columnDisplayName, value);
        }
    }

    public void WriteSectionValue(string sectionName, string sectionDisplayName, string value)
    {
        Indent();

        stringBuilder.Append($"{sectionDisplayName}: {value}");
        stringBuilder.AppendLine();
    }

    private void WriteKeyValue(string keyName, object value)
    {
        Indent();
        stringBuilder.Append($"{keyName}: ");

        string format = "{0}";

        if (value is decimal)
        {
            format = decimalFormat;
        }

        stringBuilder.AppendFormat(Culture, format, value);
        stringBuilder.AppendLine();

        ignoreNewLine = true;
    }

    public void WriteEndSection()
    {
        indentationLevel--;

        if (ignoreNewLine)
            return;

        Indent();
        stringBuilder.AppendLine();
        ignoreNewLine = true;
    }

    public string GetResult() => stringBuilder.ToString();

    public void Dispose()
    {
    }
}
```

### Create a machine-readable writer
Add an XML writer that closely mirrors the [example in the documentation](documentation/developers-and-admins/data-protection/personal-data-collection#implement-writer-classes).
In this case, `WriteSectionValue` needs to use the `sectionName` property for the XML tags rather than the display name.
C#
**XmlPersonalDataWriter.cs**
Copy
```
using CMS.DataEngine;
using CMS.Helpers;
using System.Text;
using System.Xml;
using TrainingGuides.Web.Features.DataProtection.Collectors;

namespace TrainingGuides.Web.Features.DataProtection.Writers;

public class XmlPersonalDataWriter : IPersonalDataWriter
{
    private readonly StringBuilder stringBuilder;
    private readonly XmlWriter xmlWriter;

    public XmlPersonalDataWriter()
    {
        stringBuilder = new StringBuilder();
        xmlWriter = XmlWriter.Create(stringBuilder, new XmlWriterSettings { Indent = true, OmitXmlDeclaration = true });
    }

    public void WriteStartSection(string sectionName, string sectionDisplayName) =>
        xmlWriter.WriteStartElement(TransformElementName(sectionName));

    private string TransformElementName(string originalName) => originalName.Replace('.', '_');

    public void WriteBaseInfo(BaseInfo baseInfo, List<CollectedColumn> columns,
        Func<string, object, object>? valueTransformationFunction = null)
    {
        if (baseInfo == null)
        {
            throw new ArgumentNullException(nameof(baseInfo));
        }

        foreach (var columnTuple in columns)
        {
            string columnName = columnTuple.Name;

            if (string.IsNullOrWhiteSpace(columnTuple.DisplayName))
            {
                continue;
            }

            object value = baseInfo.GetValue(columnName);
            if (value == null)
            {
                continue;
            }

            if (valueTransformationFunction != null)
            {
                value = valueTransformationFunction(columnName, value);
            }

            xmlWriter.WriteStartElement(columnName);
            xmlWriter.WriteValue(XmlHelper.ConvertToString(value, value.GetType()));
            xmlWriter.WriteEndElement();
        }
    }

    public void WriteSectionValue(string sectionName, string sectionDisplayName, string value)
    {
        xmlWriter.WriteStartElement(sectionName);
        xmlWriter.WriteString(value);
        xmlWriter.WriteEndElement();
    }

    public void WriteEndSection() => xmlWriter.WriteEndElement();

    public string GetResult()
    {
        xmlWriter.Flush();

        return stringBuilder.ToString();
    }

    public void Dispose() => xmlWriter.Dispose();
}
```

[ Previous page ](modules/data-protection/identity-collectors-gather-contacts-associated-with-a-visitor)
10 of 13
[ Mark complete and continue ](modules/data-protection/collect-personal-data)
  * [Community Questions & Answers](https://community.kentico.com/q-and-a)
  * [Contact support](https://community.kentico.com/support)


### Cookie consent
We use necessary [cookies](https://www.kentico.com/cookies-policy) to run our website and improve your experience while browsing. Additional cookies are only used with your consent. You may revoke your consent on the [Cookies Policy](https://www.kentico.com/cookies-policy) page or in your browser at any time. 
ACCEPT ALL  [Configure](https://www.kentico.com/cookies-policy)
USE ONLY NECESSARY 
![](https://docs.kentico.com/modules/data-protection/prepare-data-writers)
[](https://docs.kentico.com/modules/data-protection/prepare-data-writers)[](https://docs.kentico.com/modules/data-protection/prepare-data-writers)