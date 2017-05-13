---
layout: post
title: XML vulnerabilities
---

#Introduction

As we know, today’s web technology advances are fast in good and bad ways. With almost every technology, if not used properly, its results might be devastating. Many programmers are not introduced to the vulnerabilities that might occur when working and parsing XML files, so that was the reason for me to write this article. I hope you like it.

#What is XML?

XML stands for Extensible Markup Language, mostly used for representing structured information. XML is widely employed in today’s web technology like web services (SOAP, REST, WSDL), RSS feed, Atom, configuration files (Microsoft Office and many other Desktop applications). XML has been standardized by the World Wide Web Consortium (W3C) and is part of SGML (ISO 8879). XML was created in 1996 by Tim Bray, Jean Paoli, C. M. Sperberg-McQueen, Eve Maler, François Yergeau, and John Cowan. The first standardization and specification for XML was made on 10 Feb 1998.

W3schools.com has a nice short description of what XML represents ([W3schools](http://www.w3schools.com/xml/xml_whatis.asp)):

* XML stands for Extensible Markup Language
* XML is a markup language much like HTML
* XML was designed to carry data, not to display data
* XML tags are not predefined. You must define your own tags
* XML is designed to be self-descriptive
* XML is a W3C Recommendation

#Designing an XML structure
* XML Header (Document Type Definition – DTD)

Designing an XML structure is pretty straightforward. Each XML document begins with a header that defines the XML declaration:

{% highlight xml %}
<?xml version=”1.0″ encoding=”UTF-8″ ?>
{% endhighlight %}

Code 1: Sample of header declaration

#  Common XML vulnerabilities 
 > (sample of vulnerable [code](https://gist.github.com/hakre/2416846))

Every application has vulnerabilities, so XML parsers have some too. This is a list of well-known XML vulnerabilities that might occur in your application:

* Billion laughs
* Quadratic blowup
* DTD retrieval

##Billion laughs

This vulnerability is a DoS (Denial Of Service) aimed for the parsers of the XML. This vulnerability is also known as XML bomb or Entity Expansion XML bomb. It also might happen that this vulnerability pass the validation of the XML schema. Consider the following tag:

{% highlight xml %}
<!ENTITY entityName “Some Value”>
{% endhighlight %}

####The vulnerable code

{% highlight xml %}
<?xml version="1.0"?>
<!DOCTYPE lolz [
<!ENTITY lol "lol">
<!ENTITY lol2 "&lol;&lol;&lol;&lol;&lol;&lol;&lol;&lol;&lol;&lol;">
<!ENTITY lol3 "&lol2;&lol2;&lol2;&lol2;&lol2;&lol2;&lol2;&lol2;&lol2;&lol2;">
<!ENTITY lol4 "&lol3;&lol3;&lol3;&lol3;&lol3;&lol3;&lol3;&lol3;&lol3;&lol3;">
<!ENTITY lol5 "&lol4;&lol4;&lol4;&lol4;&lol4;&lol4;&lol4;&lol4;&lol4;&lol4;">
<!ENTITY lol6 "&lol5;&lol5;&lol5;&lol5;&lol5;&lol5;&lol5;&lol5;&lol5;&lol5;">
<!ENTITY lol7 "&lol6;&lol6;&lol6;&lol6;&lol6;&lol6;&lol6;&lol6;&lol6;&lol6;">
<!ENTITY lol8 "&lol7;&lol7;&lol7;&lol7;&lol7;&lol7;&lol7;&lol7;&lol7;&lol7;">
<!ENTITY lol9 "&lol8;&lol8;&lol8;&lol8;&lol8;&lol8;&lol8;&lol8;&lol8;&lol8;">
]>
<lolz>&lol9;</lolz>
{endhighlight}

As you can see, we have 10 “lols”. So what is happening here? At the end, we have instance of “lol9”. When the &lol9; is parsed the entity lol9 will be called which has 10 “lol8” instances. The lol8 has 10 “lol7” instances and so on. At the end you may assume that there will be a lot of “lol” (100,000,000 instances = billion). The billion lol’s might cause DoS (Denial of Service). That’s why it is called the Billion Laughs Vulnerability. For more information about the vulnerability, check the link (http://cytinus.wordpress.com/2011/07/26/37/).
