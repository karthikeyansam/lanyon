---
layout: post
title: XML vulnerabilities
---

# Introduction

As we know, today’s web technology advances are fast in good and bad ways. With almost every technology, if not used properly, its results might be devastating. Many programmers are not introduced to the vulnerabilities that might occur when working and parsing XML files, so that was the reason for me to write this article. I hope you like it.

# What is XML?

XML stands for Extensible Markup Language, mostly used for representing structured information. XML is widely employed in today’s web technology like web services (SOAP, REST, WSDL), RSS feed, Atom, configuration files (Microsoft Office and many other Desktop applications). XML has been standardized by the World Wide Web Consortium (W3C) and is part of SGML (ISO 8879). XML was created in 1996 by Tim Bray, Jean Paoli, C. M. Sperberg-McQueen, Eve Maler, François Yergeau, and John Cowan. The first standardization and specification for XML was made on 10 Feb 1998.

W3schools.com has a nice short description of what XML represents ([W3schools](http://www.w3schools.com/xml/xml_whatis.asp)):

* XML stands for Extensible Markup Language
* XML is a markup language much like HTML
* XML was designed to carry data, not to display data
* XML tags are not predefined. You must define your own tags
* XML is designed to be self-descriptive
* XML is a W3C Recommendation

# Designing an XML structure
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

##  Billion laughs

This vulnerability is a DoS (Denial Of Service) aimed for the parsers of the XML. This vulnerability is also known as XML bomb or Entity Expansion XML bomb. It also might happen that this vulnerability pass the validation of the XML schema. Consider the following tag:

{% highlight xml %}
<!ENTITY entityName “Some Value”>
{% endhighlight %}

####  The vulnerable code

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
{% endhighlight %}

As you can see, we have 10 “lols”. So what is happening here? At the end, we have instance of “lol9”. When the &lol9; is parsed the entity lol9 will be called which has 10 “lol8” instances. The lol8 has 10 “lol7” instances and so on. At the end you may assume that there will be a lot of “lol” (100,000,000 instances = billion). The billion lol’s might cause DoS (Denial of Service). That’s why it is called the Billion Laughs Vulnerability. For more information about the vulnerability, check the link (http://cytinus.wordpress.com/2011/07/26/37/).

##  Quadratic blowup

Another Entity Expansion XML bomb is the quadratic blowup vulnerability discovered by Amin Klein of Trusteer. The “kaboom” entity has 50,000 “a” represented as “&a;” When parsed, the size of it changes, from 200KB to 2.5gb, causing DoS. Still the billion laughs create much bigger size when parsing compared to quadratic blowup.

{% highlight xml %}
<?xml version="1.0"?>
<!DOCTYPE kaboom [
  <!ENTITY a "aaaaaaaaaaaaaa.....
]>
<kaboom>&a;&a;&a;&a;&a;&a;.....</kaboom>
{% endhighlight %}

##  DTD retrieval

Also with entity declaration, you can have an URL link for replacement (for definition of replacement see previous vulnerability). When using the System identifiers you can download the content from external location and embed it in you XML file.

{% highlight xml %}

<!DOCTYPE external [
<!ENTITY ee SYSTEM "http://www.python.org/some.xml">
]>
</span></p><p><span style="font-size:14pt"><root>&ee;</root>
</span></p><p><span style="font-size:14pt">

{% endhighlight %}



The same vulnerability could be used for local file also:

{% highlight xml %}
<!DOCTYPE external [
<!ENTITY ee SYSTEM "file:///PATH/TO/simple.xml">
>
<root>&ee;</root>

{% endhighlight %}

According to an article from Python’s blog about XML vulnerabilities, here are the possible “bad things” that might happen because of this vulnerability (http://blog.python.org/2013/02/announcing-defusedxml-fixes-for-xml.html):

* An attacker can circumvent firewalls and gain access to restricted resources as all the requests are made from an internal and trustworthy IP address, not from the outside.
*    An attacker can abuse a service to attack, spy on or DoS your servers but also third party services. The attack is disguised with the IP address of the server and the attacker is able to utilize the high bandwidth of a big machine.
*   An attacker can exhaust additional resources on the machine, e.g. with requests to a service that doesn’t respond or responds with very large files.
*  An attacker may gain knowledge, when, how often and from which IP address a XML document is accessed.
*   An attacker could send mail from inside your network if the URL handler supports smtp:// URIs.


## References



1. http://docs.python.org/2/library/xml.html#xml-vulnerabilities
2.  http://msdn.microsoft.com/en-us/magazine/ee335713.aspx
3. http://en.wikipedia.org/wiki/Billion_laughs
4. http://www.xponentsoftware.com/Articles/XML_vulnerabilities.aspx
5. http://clawslab.nds.rub.de/wiki/index.php/XML_Generic_Entity_Expansion
6. http://cytinus.wordpress.com/2011/07/26/37/
7. http://stackoverflow.com/questions/10212752/how-can-i-use-phps-various-xml-libraries-to-get-dom-like-functionality-and-avoi
8. http://clawslab.nds.rub.de/wiki/index.php/XML_Generic_Entity_Expansion
9. http://www.w3schools.com/dtd/
10. http://www.w3schools.com/xml/default.asp
11. http://blog.python.org/2013/02/announcing-defusedxml-fixes-for-xml.html
