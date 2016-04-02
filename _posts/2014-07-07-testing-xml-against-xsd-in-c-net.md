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

<pre class="brush: plain; title: ; notranslate" title="">&lt;br /&gt;
if (e.Severity == XmlSeverityType.Error)&lt;br /&gt;
</pre>

to be:

<pre class="brush: plain; title: ; notranslate" title="">&lt;br /&gt;
if (e.Severity == XmlSeverityType.Error || e.Severity == XmlSeverityType.Warning)&lt;br /&gt;
</pre>

If you are generating XML output against an XSD you probably should aim to have full compliance against the specification.

<pre class="brush: csharp; collapse: true; light: false; title: Example XSD Testing; toolbar: true; notranslate" title="Example XSD Testing">&lt;br /&gt;
using Microsoft.VisualStudio.TestTools.UnitTesting;&lt;br /&gt;
using System.Xml;&lt;br /&gt;
using System.Xml.Schema;&lt;/p&gt;
&lt;p&gt;namespace XsdBlogPost&lt;br /&gt;
{&lt;br /&gt;
    [TestClass]&lt;br /&gt;
    public class TestXml&lt;br /&gt;
    {&lt;br /&gt;
        [TestMethod]&lt;br /&gt;
        public void TestXmlMethod()&lt;br /&gt;
        {&lt;br /&gt;
            var settings = new XmlReaderSettings();&lt;br /&gt;
            settings.ValidationType = ValidationType.Schema;&lt;br /&gt;
            settings.ValidationFlags |= XmlSchemaValidationFlags.ReportValidationWarnings;&lt;br /&gt;
            settings.ValidationFlags |= XmlSchemaValidationFlags.ProcessInlineSchema;&lt;br /&gt;
            settings.ValidationFlags |= XmlSchemaValidationFlags.ProcessSchemaLocation;&lt;/p&gt;
&lt;p&gt;            using (var xmlSchemaReader = XmlReader.Create(&quot;CustomersOrders.xsd&quot;))&lt;br /&gt;
            {&lt;br /&gt;
                var schemaSet = new XmlSchemaSet();&lt;br /&gt;
                schemaSet.Add(null, xmlSchemaReader);&lt;/p&gt;
&lt;p&gt;                settings.Schemas = schemaSet;&lt;br /&gt;
                settings.ValidationEventHandler += (sender, e) =&gt;&lt;br /&gt;
                {&lt;br /&gt;
                    if (e.Severity == XmlSeverityType.Error)&lt;br /&gt;
                    {&lt;br /&gt;
                        Assert.Fail(&quot;Failed a test due to an XSD error.&quot;);&lt;br /&gt;
                    }&lt;br /&gt;
                };&lt;/p&gt;
&lt;p&gt;                var reader = XmlReader.Create(&quot;CustomersOrders.xml&quot;, settings);&lt;/p&gt;
&lt;p&gt;                while (reader.Read()) ;&lt;br /&gt;
            }&lt;br /&gt;
        }&lt;br /&gt;
    }&lt;br /&gt;
}&lt;br /&gt;
</pre>

You can get the [CustomersOrder.xsd file from Microsoft][3] as well as the [CustomersOrders.xml file][4]. Try modifying the XML in CustomersOrder to be invalid based on what the XSD file specifies (e.g. change the tag to ).

 [1]: http://msdn.microsoft.com/en-us/library/system.xml.xmlreader(v=vs.110).aspx
 [2]: http://msdn.microsoft.com/en-us/library/system.xml.xmlreadersettings(v=vs.110).aspx
 [3]: http://msdn.microsoft.com/en-us/library/vstudio/bb675181(v=vs.100).aspx
 [4]: http://msdn.microsoft.com/en-us/library/vstudio/bb387025(v=vs.100).aspx