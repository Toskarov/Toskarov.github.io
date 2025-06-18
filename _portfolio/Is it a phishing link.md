---
title: "Is it a phishing link?"
#excerpt: "Short description of portfolio item number 1<br/><img src='/images/500x300.png'>"
excerpt: "<b> A link that is not suspicious at all. </b> <br/> <img src='/images/Portfolio/Is%20it%20a%20phishing%20link/Mahou_Beer_Phishing_Alert.png' width='300' height='auto'>"
collection: portfolio
---

Today I received a message from my cousin on WhatsApp.
The message contains a link to Mahou, which is a popular beer in Madrid.
But I want you to judge for yourself:


![image](/images/Portfolio/Is%20it%20a%20phishing%20link/Pasted%20image%2020250614162601.png)



let's break down the link:
- ``https://mahou.es@`` --> *This is the Username*
- ``wpclick.cc`` --> *It's the domain*
- ``/FyAmTidJ/`` --> *It's the folder*
- ``?hieleras-cervezas-mahou-verano.html`` --> *It's the resource*

I admit that at first look and if you're busy, seems like a legitimate page because the page name mahou.es can confuse you, but obviously It's a fake page for phishing purposes because the domain is not **``Mahou.es``** 

This is the deception:
The user **``mahou.es``** is trying to authenticate on **``wpclick.cc``**
``mahou.es@wpclick.cc``
With this technique the attacker tries to confuse the victim

Furthermore, the resource ends with `.html`, which made me think that it means the page is not sanitized correctly or it was made without care. 

On the other hand we can explore the link's reputation:

We can use *[ScamAdviser](https://www.scamadviser.com/)* to find out if the page has been reported as a scam in the past.
When we visit *ScamAdviser*, the webpage tells us that **It is a suspicious website**
![image](/images/Portfolio/Is%20it%20a%20phishing%20link/Pasted%20image%2020250614171147.png)

So, we should start being suspecting.

We can use *[WebChecker](https://web-check.xyz/)* to provide us with more information about the webpage
When we are looking for information on this webpage we find nothing.
![image](/images/Portfolio/Is%20it%20a%20phishing%20link/Pasted%20image%2020250614172122.png)

That's strange because when we are looking for information on a legitimate page, the page, show us the information

![image](/images/Portfolio/Is%20it%20a%20phishing%20link/Pasted%20image%2020250614183154.png)

And of course we can use *[Virustotal](https://www.virustotal.com/gui/home/url)* for scanning the URL.
Virustotal is one of the most popular virus analysis webpages; here we can analyse files, URLs, hashes, domains, IPs, etc.

When we introduce the URL, VirusTotal tell us that.
This URL was flagged as malicious for 10 vendors. 
That means, in this case, vendors like BitDefender, Sophos or Fortinet mark this URL as a malicious.
![image](/images/Portfolio/Is%20it%20a%20phishing%20link/Pasted%20image%2020250614184239.png)

At this point it's very clear that it's something related to malware or phishing, but we are players and we want to see the website. 
to do this, I'm going to open the URL in a sandbox, which means in a virtual machine outside of my network.
I use Tor for an extra layer of anonymity, and when I try to log in 

![image](/images/Portfolio/Is%20it%20a%20phishing%20link/Pasted%20image%2020250614190330.png)

Something is wrong.

![image](/images/Portfolio/Is%20it%20a%20phishing%20link/Pasted%20image%2020250614192654.png)

I have created a virtual mobile using Android Studio.  
Through this, I connected to the URL where the resource was available.  
Upon entering, I encountered a kind of test

![image](/images/Portfolio/Is%20it%20a%20phishing%20link/Pasted%20image%2020250616172338.png)
![image](/images/Portfolio/Is%20it%20a%20phishing%20link/Pasted%20image%2020250616172453.png)


and after finishing it, I was redirected to an adult page.  
*(Which I obviously will not share)*
I believe this concludes our investigation.

See you soon 😄

