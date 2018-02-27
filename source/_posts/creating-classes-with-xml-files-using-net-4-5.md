---
title: Creating classes with XML files using .NET 4.5
date: 2014-06-18 00:50:43
tags:
  - .net
  - xml
  - xml as classes
---

In one of my recent assignments, I was expected to integrate with a virtual POS (vPOS) service of a bank. After examining the first test results of vPOS web service, I saw that the methods are designed to return just a string value for an entire transaction (which is supposed to contain a lot more information). I was expecting to see some of the credit card info, a name, date of transaction etc, but I only got a string. I started to read the documentation of vPOS and saw that all the data that I expected, was being sent in an xml format which does not have an xsd or detailed information. As you can deduct from here, I was dealing with some kind of a black box and all I had was a crappy documentation and a few test data.

![xmlFromBank](/images/posts/2014/xmlFromBank.png)

At first, I was planning to read the data from the xml using xPath or other means of XML operations, but I was aware that I was going to have a ton of issues without the xsd. Then I remembered this [feature (Using Paste XML as Classes)](http://msdn.microsoft.com/en-us/library/hh371548.aspx) from .NET 4.5. I had a few test data that can produce most of the probable xml results for me and I can get a few more test data from the bank to fill in the gaps. So after a few back and forth e-mails I got all the required test data to produce result xml files for each method that contains all the members and their schema in the response string.

Now all we need to do is create empty code files in the project, paste the xml results as classes and then use them as responses from the vPOS web service methods. Simple as that.

# Step-1

Open Visual Studio and create empty class file in the project:
![AddClass](/images/posts/2014/AddClass.png)

![Step-1](/images/posts/2014/step-1.png)

# Step-2

Copy the xml file, which you want to create as a class, to your clipboard (Ctrl+C).

# Step-3

Switch to Visual Studio, and paste the xml from Edit =&gt; Paste Special =&gt; Paste XML as Classes
If you fail to copy the xml file to your clipboard, you may notice that "Paste XML as Classes" is disabled.

![Step-3](/images/posts/2014/step-3.png)

# Final Result

As you can see related classes are automatically created in our empty class file.

![Final Step](/images/posts/2014/step-final.png)

Now I can use these classes as responses from vPOS web service. All I need to do is grab the xml result and deserialize it my new class.

``` csharp
public CustomTrnxList ReadFromRemote()
{
    using (BankvPOSService.ServiceSoapClient client = new BankvPOSService.ServiceSoapClient())
    {
        resultXMLFromBank = client.GetTrnxInfo(transactionGUID);
    }

    MemoryStream msMainXml = new MemoryStream(Encoding.UTF8.GetBytes(resultXMLFromBank));
    XmlDocument xDoc = new XmlDocument();
    xDoc.Load(msMainXml);
    XmlSerializer xmlSerializer = new XmlSerializer(typeof(CustomTrnxList));
    CustomTrnxList trnxResult = new CustomTrnxList();

    using (XmlReader xmlMainReader = new XmlNodeReader(xDoc))
    {
        trnxResult = (CustomTrnxList)xmlSerializer.Deserialize(xmlMainReader);
    }

    return trnxResult;
}
```

I hope you find this post useful! Please feel free to leave your comments below.