---
Title: "Hack The Box - Traceback"
Author: Xh4H
Date: "01-07-2020"
---

# Information Gathering

## Nmap
I began my reconnaissance by running an Nmap scan:

```console
root@kali:~# nmap -A -T4 -p- 10.10.10.181
Starting Nmap 7.80 ( https://nmap.org ) at 2020-07-02 08:12 EDT
Nmap scan report for 10.10.10.181
Host is up (0.024s latency).
Not shown: 65533 closed ports
PORT   STATE SERVICE VERSION
22/tcp open  ssh     OpenSSH 7.6p1 Ubuntu 4ubuntu0.3 (Ubuntu Linux; protocol 2.0)
| ssh-hostkey: 
|   2048 96:25:51:8e:6c:83:07:48:ce:11:4b:1f:e5:6d:8a:28 (RSA)
|   256 54:bd:46:71:14:bd:b2:42:a1:b6:b0:2d:94:14:3b:0d (ECDSA)
|_  256 4d:c3:f8:52:b8:85:ec:9c:3e:4d:57:2c:4a:82:fd:86 (ED25519)
80/tcp open  http    Apache httpd 2.4.29 ((Ubuntu))
|_http-server-header: Apache/2.4.29 (Ubuntu)
|_http-title: Help us
No exact OS matches for host (If you know what OS is running on it, see https://nmap.org/submit/ ).
TCP/IP fingerprint:
OS:SCAN(V=7.80%E=4%D=7/2%OT=22%CT=1%CU=30115%PV=Y%DS=2%DC=T%G=Y%TM=5EFDCF49
OS:%P=x86_64-pc-linux-gnu)SEQ(SP=FF%GCD=1%ISR=10D%TI=Z%CI=Z%II=I%TS=A)OPS(O
OS:1=M54DST11NW7%O2=M54DST11NW7%O3=M54DNNT11NW7%O4=M54DST11NW7%O5=M54DST11N
OS:W7%O6=M54DST11)WIN(W1=7120%W2=7120%W3=7120%W4=7120%W5=7120%W6=7120)ECN(R
OS:=Y%DF=Y%T=40%W=7210%O=M54DNNSNW7%CC=Y%Q=)T1(R=Y%DF=Y%T=40%S=O%A=S+%F=AS%
OS:RD=0%Q=)T2(R=N)T3(R=N)T4(R=Y%DF=Y%T=40%W=0%S=A%A=Z%F=R%O=%RD=0%Q=)T5(R=Y
OS:%DF=Y%T=40%W=0%S=Z%A=S+%F=AR%O=%RD=0%Q=)T6(R=Y%DF=Y%T=40%W=0%S=A%A=Z%F=R
OS:%O=%RD=0%Q=)T7(R=Y%DF=Y%T=40%W=0%S=Z%A=S+%F=AR%O=%RD=0%Q=)U1(R=Y%DF=N%T=
OS:40%IPL=164%UN=0%RIPL=G%RID=G%RIPCK=G%RUCK=G%RUD=G)IE(R=Y%DFI=N%T=40%CD=S
OS:)

Network Distance: 2 hops
Service Info: OS: Linux; CPE: cpe:/o:linux:linux_kernel

TRACEROUTE (using port 8888/tcp)
HOP RTT      ADDRESS
1   22.91 ms 10.10.14.1
2   24.44 ms 10.10.10.181

OS and Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 45.70 seconds
```


## Source Code

As you can see, `Apache` on ports `81/444` and `SSH` on port `22` are the only things that seem to be running.
Since I knew that there was a HTTP server running on the machine, I went to go check it out.

![Website Source](https://i.imgur.com/aA7YOYG.png)


## Google

I thought that this must've been a hint, so I did a little Googling. I found ![this](https://github.com/Xh4H/Web-Shells) Github repository, which had a list of php files (presumably web shells). On the website, there is a `<h2>` tag that read `"I have left a backdoor for all the net. FREE INTERNETZZZ"`, which could only mean that one of these php files have been left somewhere on the server.

![Github Repo](https://i.imgur.com/vnl5iw9.png)

To find it, I then used a tool called `wfuzz`. I created a text file that listed all of the filenames from the repository, which I then fed into `wfuzz` to see if they were files that existed on the server.

![Wfuzz](https://i.imgur.com/xpaFnXQ.png)

As you can see, a file called `smevk.php` was found! So naturally, I went to it and found the shell's login panel. I didn't have any credentials yet however, but I knew where I might find them.

![Shell Creds](https://i.imgur.com/K4XTEfb.png)

In the `smevk.php` source in the repository, exactly as I had thought, was the admin credentials to the panel.

![Admin Panel](https://i.imgur.com/oLTPy7v.png)


# Exploitation

After successfully compromising the panel, I began to look around the directories. under `/home/webadmin/`, I found the `.ssh` folder. Now, from my NMap scan earlier, I know that SSH is open on this machine. So, I used the upload feature on the shell to upload my `id_rsa.pub` file (my public key) to the server's `authorized_keys` file, to allow me to easily ssh into the server without needing any passwords.

![Authorized Keys](https://i.imgur.com/nQskYs4.png)

After SSH'ing into the machine as `webadmin`, I took a look around and found that there was another user (which I saw earlier from the shell) called sysadmin. I needed access. I used ```console sudo -l``` to list user's privileges on the machine and found that the sysadmin user, could be logged in through `luvit` (an lua shell) using `/home/sysadmin/luvit`. This was a nice suprise!

![Sysadmin](https://i.imgur.com/SWP3eS5.png)

From here, I simply ran the lua line ```console os.execute("/bin/bash")``` to get the familiar bash shell as the user.

![Sysadmin](https://i.imgur.com/9UZLmNw.png)


## User Flag

From this shell, I was able to cd over to the `/home/` directory, where the user flag, contained in `cat.txt` was found.

![User Flag](https://i.imgur.com/GzEB2Ji.png)


# Getting Root Flag

Now that I had found he user flag, the next target was the root flag. So, I began to look around the files on the machine that I could access as this user.
After a bit of searching around, I found two folders which had root permissions called 'update-manager' and 'update-motd.d'. Out of these two directories, update-motd.d was the one I was most interested in. This is because, typically motd's are ran as root on boot/when you SSH into the machine. 

![User Flag](https://i.imgur.com/sLbTP5x.png)


## Root Flag
So, I appended ```console cat /root/root.txt``` to the and of the file, so that the command would run the next time that I SSH'd into the machine and output the flag.

And that's exactly what it did!

![Root Flag](https://i.imgur.com/Ka631pN.png)


# Conclusion
This was my first active box and I have to say that, I had a lot of fun! There was a lot of interesting little hints and secrets that I had to find and note during my reconnaissance that made a great impact later on. A lot of this box was about exploring through it, seeing what I can find and making use of what I did. I learned qutie a lot during this, but most notably about the existance of the lua language and its shells such as luvit, executing commands from root files, generating SSH keys and overwriting the authorized_keys file etc.
