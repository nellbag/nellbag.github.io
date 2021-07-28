# Let's go phishing!

I probably get an average of one or two phishing emails per day, most of them are ridiculously bad and very easy to spot. I recently received one from Gov.uk (it wasn't) advising me that my vehicle is not taxed (it is) and to click a link to pay (no thanks). 

Now this email had all the hallmarks of a dodgy email including spelling mistakes, incorrect email address, pushing for action quickly, addressing to my email instead of name etc. Although this particular one was easy to spot, I wanted to take a more detailed look at where this email came from and if we can try to find out a bit more information about what it is trying to do.

I am just taking a surface level look at a few different things connected to this email, if someone was doing incident response or threat intelligence I imagine they would go a lot deeper, this is just a look to see if we can see anything interesting. 

### First Look

<img src="{{site.url}}/images/phish1.png" style="zoom:80%;" />

As previously mentioned this one sticks out like a sore thumb but I have highlighted some of the main issues you might pick up at first glance, such as:

* Sender email appears to be a personal email address (blurred this out though which will be explained later)
* It's addressed to my personal email address, not to my name 
* The avatar seems to be a generic icon, not something that GOV.uk would have
* Lots of grammar and spelling mistakes
* Lots of prompts to get you to act fast and instill fear "your vehicle isn't taxed", "...could be clamped or crushed...", "...illegal to drive..."
* Plus loads more.....

It gives a handy helpful link to proceed to payment which is nice, first though we should hover the mouse over this and it should give a preview of where the link actually goes: 

<img src="{{site.url}}/images/phish2.png" style="zoom:80%;" />

As you can see this link would actually take you to a URL at contaboserver[.]net, again this is not something we would expect from a government communication asking to pay UK road tax. 

Ok so we know this will potentially navigate us to a dodgy site just by looking at the link, sometimes though they are not always that easy to spot. I have seen examples where it will initially take you to a generic domain, something along the lines of "payment-services.com" but then it could redirect you (sometimes more than once) to the eventual dodgy site. 

Luckily there are tools that can help us see where links go.....

### Wheregoes...

One such tool is wheregoes.com. This is one of many sites that will let you trace the path of a URL to see if it redirects anywhere. I updated our link from the phishing email and it showed this:

<img src="{{site.url}}/images/phish3.png" style="zoom:80%;" />

So this actually shows that this particular link doesn't re-direct anywhere and actually goes to that dodgy looking contaboserver[.]net. It gives a status code of "200" which means it was successfully completed. 

### What's next?

We know that the email link would take us to vmi630867[.]contaboserver[.].net so lets see if we can find anything out about this site. First stop is to use a domain lookup tool, again there are LOADS of these available, I am using whois.domaintools.com. These domain lookup tools let you type in a website or IP Address and you can see when it was registered and (sometimes) who it is registered to, the geographical location and more...

<img src="{{site.url}}/images/phish4.png" style="zoom:80%;" />

This has returned details for contaboserver[.].net but most of the details are redacted, which you can request for privacy, further down the page (not pictured) it tells us the domain is registered in Germany but not much else. At this stage I decided to do a quick Google search for this domain and this was returned:

<img src="{{site.url}}/images/phish5.png" style="zoom:80%;" />

So it looks like Contabo are a German company offering virtual private servers and web-hosting, so this in itself isn't dodgy, just someone is using this platform to attempt to phish people. We may have a dodgy site/server hosted on a legitimate platform, at least we can report the site and hopefully have it taken down. 

Where else can we look for domain information? 

### Shodan.io

> Shodan is a search engine that lets the user find specific types of computers connected to the internet using a variety of filters. Some have also described it as a search engine of service banners, which are metadata that the server sends back to the client.

Shodan is an extremely useful search engine as it can provide lots of information about internet connected devices. Usually when a computer or server or IOT device is connected to the internet it will have ports open to send and receive traffic. Shodan lets us poke at these ports and see what is open. 

<img src="{{site.url}}/images/phish6.png" style="zoom:80%;" />

The screenshot above from Shodan confirms some of the information we already know but also shows a list of open ports. As mentioned previously, ports are used by processes or network services to communicate with each other. As an example, this website you are looking at right now is likely sending and receiving traffic through port number 443 which is usually the default port for secure web traffic. 

Our phishing web server seems to have ports 22, 80, 143 and 587 open:

* **Port 22** is usually used for [SSH](https://en.wikipedia.org/wiki/Secure_Shell_Protocol) which is the Secure Shell Protocol which is used for (among other things) remotely accessing a command line on a networked computer. This could be a way for our phishers (is that a word?) to remotely access their server. 

* **Port 80** is the default port for unsecured web traffic. You know when you go to a website the address either has http:// or https:// in the address bar? Well the "s" means secure and it shows the web traffic is being encrypted in transport. The stuff going through this port is not secure and won't be encrypted. This could be used to host a fake website or login page for our phishers.

* **Port 143** is typically [IMAP](https://en.wikipedia.org/wiki/Internet_Message_Access_Protocol) which is the Internet Message Access Protocol, this is used to retrieve email messages from a server. Given we are dealing with phishing emails, it's not a surprise we would see something like this. 

* Finally **Port 587** seems to be another mail protocol, this time [SMTP](https://en.wikipedia.org/wiki/Simple_Mail_Transfer_Protocol) which is Simple Mail Transfer Protocol. SMTP is typically run on port 25 however based on [this quick Google search](https://serversmtp.com/port-587/) using port 587...

  >  ...will definitely **reduce the rate of rejected messages**. That is particularly helpful if you’re **sending bulk or mass emails** (e.g. newsletters)...

Notice anything that could be advantageous to someone running a phishing campaign? 

### Back to the link

Remember when we highlighted the original link there was loads of data after the contaboserver[.]net part?

<img src="{{site.url}}/images/phish7.png" style="zoom:80%;" />

This looks like nonsense but this part of the URL is likely passing information to the web server to aid in the phishing attempt, potentially just serving up a fake login page or prompting a malicious download. We can attempt to decode some of this though and see if we can make more sense of it. First things first it seems to be [URL encoded](https://en.wikipedia.org/wiki/Percent-encoding) but using a tool like [Cyber Chef](https://gchq.github.io/CyberChef) we can tidy this up a bit:

<img src="{{site.url}}/images/phish8.png" style="zoom:80%;" />

<img src="{{site.url}}/images/phish9.png" style="zoom:80%;" />

Cyber Chef has decoded some of the URL (first screenshot) then I have made it a bit more readable in a notepad (second screenshot). It looks like it was using the pipe character "|" as a delimiter to separate different sets of data. The characters on lines 7 and 8 look like they could be [hashed values](https://en.wikipedia.org/wiki/Hash_function) but what catches my eye are the characters that end in an equals sign as this could indicate [Base64 encoding](https://en.wikipedia.org/wiki/Base64).

### Decoding time!

I won't go into too much detail about what Base 64 encoding is but in general it's:

> "used when there is **a need to encode binary data that needs be stored and transferred over media that are designed to deal with textual data**. This is to ensure that the data remains intact without modification during transport”"

This is encoding, not encryption or hashing, so we can actually decode this data, again using our friend Cyber Chef...

<img src="{{site.url}}/images/phish10.png" style="zoom:80%;" />

Result! The nonsense text that was part of the URL can be decoded as shown above and it looks like it is showing some parameters again to do with sending or receiving mail? It's possibly setting some variables here for V, P, AN and WT. Not sure of the significance of this at this stage as it would require a bit more research however this demonstrates that what you see in the URL could be encoded or change to try and disguise it's true purpose.

### Oh, one other thing....

Another thing we can do with this link is run it through [VirusTotal](https://www.virustotal.com/gui/) and see if anything malicious is returned. VirusTotal is an online service where you can submit suspicious files or URLS which are then shared across various vendors and it is given a score as to whether it is suspicious. This is our link:

<img src="{{site.url}}/images/phish11.png" style="zoom:80%;" />

Bingo, this has been flagged by 10 different vendors with many of them marking it as phishing. Surprisingly though there were 79 vendors who did not flag it as malicious however the 10 hits are significant enough to know it is not a legitimate link and should be avoided. 

### Knock knock, who's there?

We know this email isn't from the government telling me my tax isn't paid, so who is it from? Well the email had what seemed to be a personal email address from a very well known provider.  

<img src="{{site.url}}/images/phish12.png" style="zoom:80%;" />

I have blurred this out because I suspect it might be a legitimate email address that has been taken over maliciously. By doing a little bit of email OSINT (Open Source Intelligence Gathering) we can try to confirm if this is the case. Again, I haven't gone too deep with this, especially if it is someone's own email. 

First stop to see if this email had potentially been taken over was to search through a collection of breach data to see if this email address was on there. There are many many sources of breach data out there for public download on the internet, usually sourced from previous data breaches. 

<img src="{{site.url}}/images/phish13.png" style="zoom:80%;" />

No hits on this particular email address although there are a few similar. 

One very useful service when looking at email addresses and checking if they are trustworthy is a site called emailrep.io. They use various sources of publicly available information to assess an email addresses reputation which should give you an idea if it is dodgy or not. 



<img src="{{site.url}}/images/phish14.png" style="zoom:80%;" />

As you can see this email address is returned as high reputation and confirms it has actually appeared in a data breach which could have been the malicious actors way in. It also confirms there is a Twitter account attached to the email address. At this stage I was pretty sure this was a legitimate email address that had been taken over so I decided not to look into it any further. 

### Conclusion

Hopefully this has been somewhat interesting in looking to see what we can find out about a potentially malicious email with just a few searches online. Of course you wouldn't be expected to go to all this effort for every phishing email you receive, especially for one like this as it was so obviously a phishing attempt. They won't all be bad though so we need to stay vigilant at all times, you might find yourself in a situation where an email looks totally legit but taking any one of the steps above could reveal it's true intention. 

Phishing campaigns are (generally) a numbers game, send huge volumes of generic emails and hope a small proportion click the link or download the file. Phishers have to get a lot of things right to end up exploiting someone but when they do it could be devastating for the person that clicks. 

If I can leave you with any parting advice it would be to be cautiously skeptical of emails and text messages asking you to click a link or download something, especially if they attempt to make the situation urgent. Hover over links, check senders and don't provide any details unless you know the site it legitimate. 

All the best

Nelly 
