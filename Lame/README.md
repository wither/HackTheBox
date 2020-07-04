---
Title: "Hack The Box - Lame"
Author: ch4p
Date: "02-07-2020"
---

# Information Gathering

## Nmap
I began my reconnaissance by running an Nmap scan:

![NMap](https://i.imgur.com/t1BeqKh.png)

I noticed that this machine was running what seemed to be an old version of Samba, which from my experience are typically very vulnerable. So I then had to see if there was exploit already available for this version.

## Google

I then began to search for an exploit on Google. This was the first result, from a reliable source: Rapid7.

![Rapid7](https://i.imgur.com/8eJ3Ja8.png)

As you can see, this version of `Samba smbd 3.0.20-Debian` has an RCE exploit called "username map script", which can be found in metasploit.

## Searchsploit

Using `msfconsonsole > searchsploit samba`, I was able to locate the exploit in metasploit.

![Metasploit Search](https://i.imgur.com/Oxx7YRH.png)


# Exploitation

As per usual metasploit exploitation, the rest was pretty easy. I firstly selected the relevent exploit using `use 13`, set the `RHOSTS to 10.10.10.3` and then ran `run` to begin.

![Exploit](https://i.imgur.com/adzcLfK.png)


## User Flag

Simply searching for and outputting the contents of the user flag, often found in `user.txt`

![User Flag](https://i.imgur.com/7QItBrN.png)



## Root Flag

Simply searching for and outputting the contents of the root flag, often found in `root.txt`

![Root Flag](https://i.imgur.com/GmeoHTW.png)


# Conclusion
This was a very simple box, but I feel like it definately help solidify my knowledge on the basics such as locating and running exploits effectively using metasploit.
