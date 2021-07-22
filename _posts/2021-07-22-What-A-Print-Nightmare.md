# What a (print) nightmare!

Microsoft are not having a good Cyber Security time right now with multiple issues affecting a number of users. Several bugs have been identified with the print spooler service and more recently, an issue with windows passwords. 

#### First though, what is the print spooler service?

Well print spooler is a very old service that is enabled by default on all windows machines and it helps manage (you guessed it), printing jobs on the system/network. There is a more technical definition [here](Additionally, the print spooler service exposes local interfaces that are used by client applications to print, obtain [print queue](https://docs.microsoft.com/en-us/openspecs/windows_protocols/ms-prsod/b1e6690e-453a-4415-9506-2706ba31feac#gt_569f1f1c-f426-46fa-91d2-3d1eb0b19aa1) status, administer print queues, or perform other print-specific actions.) in the Microsoft documentation but that's the gist of it. It lets you click print and queue print jobs, view the print queue etc. All of these things can be very useful but also remember this service it automatically enabled and running unless you switch it off.  

#### Ok but why should I care?

Well, multiple CVE's were discov......

#### Wait, what's a CVE??

Oh my apologies, this quote from [RedHat](https://www.redhat.com/en/topics/security/what-is-cve) sums it up:

> CVE, short for Common Vulnerabilities and Exposures, is a list of publicly disclosed computer security flaws. When someone refers to a CVE, they mean a security flaw that's been assigned a CVE ID number.

So basically if a vulnerability is found in a computer system, say with the way it handles print jobs or whatever, then it can be assigned a CVE ID and logged on a huge database. This helps companies and users track new vulnerabilities that they may need to fix on their systems. 

#### Ok, back to the print-nightmare...

[CVE-2021-1675](https://msrc.microsoft.com/update-guide/vulnerability/CVE-2021-1675) was identified in June 2021 and it allowed a user to execute code remotely through the print spooler service. Another person (or group, not sure), released details of what they thought was the same vulnerability however it turned out they had discovered a brand new issue, it was just in the same place as before, so this newer one was assigned [CVE-2021-34527](https://msrc.microsoft.com/update-guide/vulnerability/CVE-2021-34527). 

#### Phew, so these issues were fixed and everything is all good?

Not quite..... Microsoft issued fixes for these issues but there was a THIRD CVE identified in the same place affecting the print spooler service! This one was given the ID [CVE-2021-34481](https://msrc.microsoft.com/update-guide/vulnerability/CVE-2021-34481) and it enabled a user to elevate their privileges and run commands as an administrator. 

#### Oh dear, so NOW it's all fixed?

No. At the time of writing there is no fix I'm afraid. The current advice is to turn off the print spooler service meaning no-one would be able to print. 

#### No! I need to fax a report today!!

Fax?! It's 2021, send an email. 

#### OK fair enough, so some people can't print. Big deal. What about the password thing you mentioned?

Oh yes! So ANOTHER CVE has been identified ([CVE-2021-36934](https://msrc.microsoft.com/update-guide/vulnerability/CVE-2021-36934)), not the print spooler this time, this one is to do with the Security Account Manager (SAM) in **all** versions of Windows 10. The SAM is a database file that stores all user passwords and a standard user should not have access to this, but they do. A user can download the file and attempt to crack the passwords or they can also run code as an administrator meaning they could take over a system. 

#### Wow, I Wouldn't want to be the Microsoft security people right now.

Me neither. 

It is worth noting that CVE's are identified all the time on many different systems, in 2020 there were [18,358 unique CVE's](https://www.google.com/url?sa=t&rct=j&q=&esrc=s&source=web&cd=&cad=rja&uact=8&ved=2ahUKEwjSr7qQwvfxAhXDsKQKHT0cDiAQFjACegQIBRAD&url=https%3A%2F%2Fwww.infosecurity-magazine.com%2Fnews%2F2020-saw-6-rise-in-number-of-cves%2F%23%3A~%3Atext%3DA%2520year%252Din%252Dreview%2520report%2C%2525%252C%2520from%25206%252C487%2520to%252018%252C358.&usg=AOvVaw0zp68Zfe8cFPVLrP8bGu-D) assigned, that's about **50 per day!**

#### Well thanks for the info but how does this affect me, what can I do?

# *Make sure you update ALL your computers, tablets, phones, kettles and fridges. Updates are important!*

*Also* make sure you choose long, strong and unique passwords. Better yet use a password manager. Oh and also enable [2FA/MFA](https://en.wikipedia.org/wiki/Multi-factor_authentication) anywhere you can. 

