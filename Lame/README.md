---
Title: "Hack The Box - Traceback"
Author: Xh4H
Date: "01-07-2020"
---

# Information Gathering

## Nmap
I began my reconnaissance by running an Nmap scan:

![NMap](https://i.imgur.com/Bzzrvgg.png)


## Google

I then began to search the exploit on Google. This was the first result, from a reliable source: Rapid7.

![Rapid7](https://i.imgur.com/Giv3H9S.png)

As you can see, this version of `Samba smbd 3.0.20-Debian` has an RCE exploit called "username map script", which can be found in metasploit.

## Searchsploit

Using `msfconsonsole > searchsploit samba`, I was able to locate the exploit in metasploit.

![Metasploit Search](https://i.imgur.com/hz0DjAW.png)


# Exploitation

As per usual metasploit exploitation, the rest was pretty easy. I firstly selected the relevent exploit using `use 13`, set the RHOST to `10.10.10.3` and then ran `exploit` to begin.

![Exploit](https://i.imgur.com/KGcAvRJ.png)


## User Flag

Simply searching for and outputting the contents of the user flag, often found in `user.txt`

![User Flag](https://i.imgur.com/zWg8YUO.png)



## Root Flag

Simply searching for and outputting the contents of the user flag, often found in `user.txt`

![Root Flag](https://i.imgur.com/4z70D1K.png)


# Conclusion
This was a very simple box, but I feel like it definately help solidify my knowledge on the basics such as locateing and running exploits effectively using metasploit.
