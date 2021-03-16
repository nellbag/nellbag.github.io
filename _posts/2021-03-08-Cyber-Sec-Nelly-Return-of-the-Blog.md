# Cyber Sec Nelly: Return of the Blog

**I’m back!** How are you? I am fine thank you for asking. Firstly let me apologise for my absence, I know this is your most favouritest Cyber Security themed blog written by me on the planet and I have rudely not been here. Rest assured I am back and ready to rock your cyber world! (actual results may vary).

I have a huge list of things to catch you up on so bear with me while I get that done, some will be posts of their own and some will just be wee updates here. As you may have noticed, this blog is no longer hosted on SquareSpace. I think SquareSpace is a great platform and I really liked the site I was able to build but it was just too expensive to keep going when there are free alternatives out there. So now I am on [GitHub Pages!][1] 

Huge shout out to [Chad Baldwin][2] for their blog post I saw on Reddit which resurrected this blog. If you can I would recommend reading through that blog post if you are interested in blogging, it’s really easy to set up and you can have a blog in minutes. It is a bit more technical than ready made templates like SquareSpace but I am looking at that as a learning opportunity, time to brush up on my HTML,CSS and JS skills (I use the term skills very loosely here).

OK on to some updates………

## eJPT (eLearn Security Junior Penetration Tester)

I passed the [eJPT!!][3] I was going to do a separate blog post on the eJPT exam but to be honest there is not much more I can add to all the other resources already out there. I will say that the exam format was great, without giving too much away it was set up like a real network and you had to answer questions that you could only get by enumerating and exploiting machines. 

<img src="{{site.url}}/images/ejpt_cert.png" style="zoom:50%;" />

One regret I do have though is rushing the last part of the exam. They give you 72 hours to complete it, which is more than enough time. I took two days' annual leave from work and set aside time from 9am to ~5pm both days to do the exam. By the end of the first day I was pretty sure I had enough points to pass and I just submitted my answers knowing I had free retakes if I failed. 

I did pass but in hindsight I wish I had taken that second day to reset my brain and go again to get a better pass mark. I know that a pass is still a pass but I would say if you have the time, then take your time. 

There have been some changes to the PTS course that lead to the eJPT exam. You can now get the [Starter Pass from INE][4] for free and this is the same course I did and would recommend. You then just pay for an exam voucher with eLearn Security which is about $200/£145 (if you want to that is, nothing stopping you using the starter pass on it’s own just for fun and learning!) 

## I'm blue, da ba dee da ba daa

Yo listen up, here's the story….. Well it’s not much of a story per se, just that I have been having fun doing some blue team-esque things lately. For those not in the know, Red Team is used to refer to offensive security/attackers and Blue Team is used to refer to defensive security/defenders. I just actually looked it up and Daniel Miessler explains this very well [here.][4]

So I enrolled in the [Cyber Defence learning path on Try Hack Me][5] and have managed a few boxes, I also signed up to [Blue Team Labs Online][6] which recently launched and offers some free challenges. It seems like a great platform and I really likes analysing windows logs, trying to recover deleted data and looking at malicious PowerShell code. 

I am still mainly working on offensive security and penetration testing stuff but it doesn't hurt to diversify your skills!

## What time is it? CTF Time!

I have also started doing some CTF events which have been really fun and challenging. A CTF is a Capture the Flag event where typically, if it is a jeopardy style event, you are given a number of different challenges based on different topics, so there could be **cryptography and steganography** challenges (hidden and encrypted messages), **web application security** (trying to find ways to exploit a website/webapp), **reverse engineering** (taking a program apart and finding out how it works) and many many more.

You complete these challenges by finding flags, which are usually just a string in a specific format like *"flag{random_string_here}"*, and you are awarded points for each flag found. It is really fun to try and has definitely been teaching me some new things. There are different types of CTF, I have only explained the Jeopardy style event as it's the only ones I have played, more info on the different types can be found [here][7].

My first event was the [Tenable CTF][8] which was actually the first one they put on as well, this is the same Tenable that own Nessus which is a vulnerability scanner used quite a lot in Cyber Security. I came a respectable 675th out of 1762 competitors which I was really happy with considering it was my first one. This happiness was short lived when I signed up for loads more events and didn't do so well! I registered for Aero CTF and zer0pts CTF but both were a bit beyond my skill level. 

<img src="{{site.url}}/images/CTFTime_nellbag.png" style="zoom:75%;" />

More recently I was very much looking forward to [Nahamcon2021 CTF][9] which was built by [John Hammond][10] and his team, the set up for this event was great and I was really looking forward to it but I never really got a chance to play, too much IRL things going on that weekend! I was a bit disappointed as I think there were some more beginner friendly challenges in this one that I might have been able to do. I am sure there will be more opportunities like this though and I enjoyed reading write-ups and [watching videos][11] from the event.  

## Passwords and Scripting

I have been doing a bit of research into passwords as I want to do a detailed blog post and maybe a You tube video about this topic. I am fascinated by the passwords that people choose; 123456 still tops the list as the [most common password][12] used. It is amazing to me how easy it is to crack an insecure or weak password and just how much breach data is out there for anyone to download. Like I say  though, keep an eye out for a future post about this. 

The reason I bring it up now is because while researching this I wrote my first functional and (somewhat) useful [bash script][13]! It's nothing spectacular but I have already found it quite useful. It is basically a wrapper for a program called [CeWL][14]. 

> CeWL is a ruby app which spiders a given URL to a specified depth, optionally following external links, and returns a list of words which can then be used for password crackers such as John the Ripper.

```Bash
#!/bin/bash

if [[ $# -eq 0 ]] ; then
    echo "Incorrect syntax!"
    echo "Please provide an input list, spider depth and output file:"
    echo "e.g. ./cewl_list.sh ./websites.txt 2 output.txt"
    exit 0
fi

echo "[*] Starting..."
touch temp_cewl.txt

while IFS="" read -r p || [ -n "$p" ]
do
    echo "[*] Crawling website: $p"
    cewl $p -d $2 --with-numbers >> temp_cewl.txt
    count=$(wc -w temp_cewl.txt | awk 'NR==1{print $1}')
    echo "[*] Total: $count words"
done < $1

sort temp_cewl.txt | uniq -u > $3
count=$(wc -w "$3" | awk 'NR==1{print $1}')
rm temp_cewl.txt

echo "[*] $count unique words found." 
echo "[*] See $3 for results."

```

So basically CeWL will search through a website and spit out a list of unique words contained in it (and it will follow links if you want it to). This can be useful when talking about cracking passwords as it gives you the opportunity to pull some unique words that may be more pertinent to your target, you can then run these unique words through [rules on Hashcat][15] to change them to be more "passwordy" i.e. password123 could be changed to P@s$worD123. 

When I was using CeWL I needed to run the command for each website I wanted to crawl and then append to a text list, this script lets you have a list of websites in a new line separated text file and it will crawl them all one after another then do a final sort to give you unique words from multiple sites as opposed to just one. It is slow and needs loads of work but I am happy with it for now :) 

## What's next?

Well I have LOADS of plans on what I want to do, just don't have the time to do it all..... some of the things on my ever growing list include; bug bounties, Portswigger Academy, more boxes on Try Hack Me and Hack the Box, more CTF events, set up a home lab, get an AWS certification, blogging and You tube videos, get better at scripting/coding, look into Android hacking, complete a TCM OSINT course.......

Keep an eye out for some more posts coming soon, and thanks for reading :)

All the best

Nelly

(Oh I will also be transferring my old blog posts over so you may see them pop up at some point)

---

[1]: https://pages.github.com/
[2]: https://chadbaldwin.net/2021/03/14/how-to-build-a-sql-blog.html
[3]: https://elearnsecurity.com/product/ejpt-certification/
[4]: https://danielmiessler.com/study/red-blue-purple-teams/
[5]: https://tryhackme.com/paths
[6]: https://blueteamlabs.online
[7]: https://ctf.zone/ctfinfo.html
[8]: https://tenable.ctfd.io/About
[9]: https://ctf.nahamcon.com
[10]: https://www.youtube.com/channel/UCVeW9qkBjo3zosnqUbG7CFw
[11]: https://www.youtube.com/watch?v=_9IREY2gUpI
[12]: https://nordpass.com/most-common-passwords-list/
[13]: https://github.com/nellbag/scripts/blob/main/cewl.sh
[14]: https://github.com/digininja/CeWL
[15]: https://hashcat.net/wiki/doku.php?id=rule_based_attack
