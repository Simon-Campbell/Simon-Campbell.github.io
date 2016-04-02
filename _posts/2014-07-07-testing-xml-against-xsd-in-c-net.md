---
title: 'Testing XML Against XSD in C#/.NET'
author: Simon Campbell
layout: post
categories:
  - Development
tags:
  - .net
  - 'c#'
  - programming
  - testing
  - xml
  - xsd
---
In some software systems you are required to generate generate some XML output. Usually this XML output can be generated as a result of some serialising objects. The specification of the XML structure can either be very straightforward or complex for the inexperienced C#/.NET developer to implement. The straightforward specifications usually only require serializing some Plain Old CLR Object (POCO) whereas the other, more complex specifications may require additional C# class/field attributes or some custom serialisation layer. Either case should be tested because even the simplest serialization procedures can produce unexpected outputs.

If you are fortunate enough to have have XSD contract for how your XML should look then you can use the .NET framework to validate your XML documents against XSD. In order to get started with testing your outputs against an XSD you will need to create a Unit Test project in Visual Studio with a reference to System.Xml. Once you have created the project add a new unit test to the project with `using` statements for System.Xml and System.Xml.Schema.

In C#/.NET you can read an XML file using the [XmlReader][1] class. This class can be used to read XML files from a stream or a URI string using the XmlReader.Create method. One of the optional overloads for this method is to take an [XmlReaderSettings][2] instance. An XmlReaderSettings class allows for a validation type, validation options, validation schemas and an event handler to be set when any important validation events happen. This class is what will be used as the basis for testing XML against XSD. At the bottom of this post is an example of how the XmlReader class can be put to use.

The below example can be modified to fail tests on warnings instead of just errors. This can be done by simply modifying the following line of code in the event handler:

{% highlight csharp %}
if (e.Severity == XmlSeverityType.Error)
{% endhighlight %}

to be:

{% highlight csharp %}
if (e.Severity == XmlSeverityType.Error || e.Severity == XmlSeverityType.Warning)
{% endhighlight %}

If you are generating XML output against an XSD you probably should aim to have full compliance against the specification.

{% highlight csharp %}
using Microsoft.VisualStudio.TestTools.UnitTesting;
using System.Xml;
using System.Xml.Schema;
namespace XsdBlogPost
{
    [TestClass]
    public class TestXml
    {
        [TestMethod]
        public void TestXmlMethod()
        {
            var settings = new XmlReaderSettings();
            settings.ValidationType = ValidationType.Schema;
            settings.ValidationFlags |= XmlSchemaValidationFlags.ReportValidationWarnings;
            settings.ValidationFlags |= XmlSchemaValidationFlags.ProcessInlineSchema;
            settings.ValidationFlags |= XmlSchemaValidationFlags.ProcessSchemaLocation;
            using (var xmlSchemaReader = XmlReader.Create("CustomersOrders.xsd"))
            {
                var schemaSet = new XmlSchemaSet();
                schemaSet.Add(null, xmlSchemaReader);
                settings.Schemas = schemaSet;
                settings.ValidationEventHandler += (sender, e) =>
                {
                    if (e.Severity == XmlSeverityType.Error)
                    {
                        Assert.Fail("Failed a test due to an XSD error.");
                    }
                };
                var reader = XmlReader.Create("CustomersOrders.xml", settings);
                while (reader.Read()) ;
            }
        }
    }
}
{% endhighlight %}

You can get the [CustomersOrder.xsd file from Microsoft][3] as well as the [CustomersOrders.xml file][4]. Try modifying the XML in CustomersOrder to be invalid based on what the XSD file specifies (e.g. change the tag to ).

 [1]: http://msdn.microsoft.com/en-us/library/system.xml.xmlreader(v=vs.110).aspx
 [2]: http://msdn.microsoft.com/en-us/library/system.xml.xmlreadersettings(v=vs.110).aspx
 [3]: http://msdn.microsoft.com/en-us/library/vstudio/bb675181(v=vs.100).aspx
 [4]: http://msdn.microsoft.com/en-us/library/vstudio/bb387025(v=vs.100).aspx