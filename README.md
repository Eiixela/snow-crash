# snow-crash

snow-crash is the first project of the cybersecurity branch at 42.
The goal is to give an introduction to cybersecurity.

An ISO for a VM, booted on Debian, is provided to use, where the goal will ultimately be privilege escalation through different vulnerabilities and exploits.
The project is separated in 15 levels. The current level needs to be completed before we can move on to the next.

In this README, we will go over the first 10 (so far), explaining what the strategies were, where our ideas fell short, how we figured it out.

## General Rules

We start as a user called `level00`. The goal is to find the password to upgrade to the user named `flag00`, where we will finally get the password to the user `level01`, so on and so forth up to `level09`.

## Levels

### Level00

The first level was, for me, the hardest. I didn't know where to begin and was at a complete loss.
I first tried looking around the files, seeing if anything had a weird name, an unusal extensions or anything of interest really. 

But I couldn't find anything.

I then started looking online if any tool, pre-install on Linux, could help me. I found the `find` command. After reading documentation i used :
```
 find / -user flag00 2>/dev/null
```
Basically looking for files or directories owned by the user I was trying to be.

I got :
```
/usr/sbin/john
/rofs/usr/sbin/john
```

After checking inside these directories, we find :
```
----r--r--  1 flag00  flag00      15 Mar  5  2016 john
cat john 
cdiiddwpgswtgt
```

It's looking like a encrypted code. After looking around the internet I found the __caesar cipher__, a basic algorithm that shift every letter of a string to make it unreadable. With a shift key of 1, we get this string :
```
nottoohardhere
```

And sure enough, it's the password to the `flag00` user.
We can then ge the password for the `level01` user :
```
x24ti5gi3x0ol2eh4esiuxias
```

### Level01

For the second level, I started looking around available directories I could access. I ended up finding this directory :
```
cat etc/passwd
root:x:0:0:root:/root:/bin/bash
daemon:x:1:1:daemon:/usr/sbin:/bin/sh
bin:x:2:2:bin:/bin:/bin/sh
sys:x:3:3:sys:/dev:/bin/sh
sync:x:4:65534:sync:/bin:/bin/sync
games:x:5:60:games:/usr/games:/bin/sh
man:x:6:12:man:/var/cache/man:/bin/sh
lp:x:7:7:lp:/var/spool/lpd:/bin/sh
mail:x:8:8:mail:/var/mail:/bin/sh
news:x:9:9:news:/var/spool/news:/bin/sh
uucp:x:10:10:uucp:/var/spool/uucp:/bin/sh
proxy:x:13:13:proxy:/bin:/bin/sh
www-data:x:33:33:www-data:/var/www:/bin/sh
backup:x:34:34:backup:/var/backups:/bin/sh
list:x:38:38:Mailing List Manager:/var/list:/bin/sh
irc:x:39:39:ircd:/var/run/ircd:/bin/sh
gnats:x:41:41:Gnats Bug-Reporting System (admin):/var/lib/gnats:/bin/sh
nobody:x:65534:65534:nobody:/nonexistent:/bin/sh
libuuid:x:100:101::/var/lib/libuuid:/bin/sh
syslog:x:101:103::/home/syslog:/bin/false
messagebus:x:102:106::/var/run/dbus:/bin/false
whoopsie:x:103:107::/nonexistent:/bin/false
landscape:x:104:110::/var/lib/landscape:/bin/false
sshd:x:105:65534::/var/run/sshd:/usr/sbin/nologin
level00:x:2000:2000::/home/user/level00:/bin/bash
level01:x:2001:2001::/home/user/level01:/bin/bash
level02:x:2002:2002::/home/user/level02:/bin/bash
level03:x:2003:2003::/home/user/level03:/bin/bash
level04:x:2004:2004::/home/user/level04:/bin/bash
level05:x:2005:2005::/home/user/level05:/bin/bash
level06:x:2006:2006::/home/user/level06:/bin/bash
level07:x:2007:2007::/home/user/level07:/bin/bash
level08:x:2008:2008::/home/user/level08:/bin/bash
level09:x:2009:2009::/home/user/level09:/bin/bash
level10:x:2010:2010::/home/user/level10:/bin/bash
level11:x:2011:2011::/home/user/level11:/bin/bash
level12:x:2012:2012::/home/user/level12:/bin/bash
level13:x:2013:2013::/home/user/level13:/bin/bash
level14:x:2014:2014::/home/user/level14:/bin/bash
flag00:x:3000:3000::/home/flag/flag00:/bin/bash
flag01:42hDRfypTqqnw:3001:3001::/home/flag/flag01:/bin/bash
```

If you notice the very last line you can notice it not like any other one, plus it relates to the user we trying to find the password for. 

In Linux systems, user are stores this way :
```
username: x :userID:groupID:userinfo:/homedir:/bin/bash
```
x being the hashed password for the user.

So we have the password for flag01, we just need to decrypted it. Looking online I found the popular DES algorithm created in the 70's (not used anymore because it's can be easily cracked).
So to crack the password, I used `john the ripper`, a tool for linux system that allows use to crack hashed password.

in the ended we got :
```
john --show flag01.hash
?:abcdefg

su flag01
Password: 
Don't forget to launch getflag !
flag01@SnowCrash:~$ getflag
Check flag.Here is your token : f2av5il02puano7naaf6adaaf
```

### Level02

When starting level02, we directly find a .pcap file. This extension is used by Wireshark, which is an open-source network anaylzer tool. When opening the file via Wireshark, we get a lot of informations regarding 95 packets of data sent and receiving. Too much to look throught each manually. 

I wasn't famialiar with Wireshark so I followed tutorials online helping me understand use the tool and make sense of what I was looking at. Eventually I found the option to `follow the TCP stream` which reconstruct the exchanges of data.

We got something like this :
```
..%..%..&..... ..#..'..$..&..... ..#..'..$.. .....#.....'........... .38400,38400....#.SodaCan:0....'..DISPLAY.SodaCan:0......xterm.........."........!........"..".....b........b....    B.
..............................1.......!.."......"......!..........."........"..".............    ..
.....................
Linux 2.6.38-8-generic-pae (::ffff:10.1.1.2) (pts/10)

..wwwbugs login: l.le.ev.ve.el.lX.X
..
Password: ft_wandr...NDRel.L0L
.
..
Login incorrect
wwwbugs login: "
```

So we know somebody tried connected to a user but used the wrong password. I have the password but it's composed of non printable character, so I need to `show data as HEX DUMP`, a Wireshark tool, so I can see the hexa ASCII of the input.
We got this :
```
...Passw ord: 
000000B9  66                                                 f
000000BA  74                                                 t
000000BB  5f                                                 _
000000BC  77                                                 w
000000BD  61                                                 a
000000BE  6e                                                 n
000000BF  64                                                 d
000000C0  72                                                 r
000000C1  7f                                                 .
000000C2  7f                                                 .
000000C3  7f                                                 .
000000C4  4e                                                 N
000000C5  44                                                 D
000000C6  52                                                 R
000000C7  65                                                 e
000000C8  6c                                                 l
000000C9  7f                                                 .
000000CA  4c                                                 L
000000CB  30                                                 0
000000CC  4c                                                 L
000000CD  0d                                                 .
```

Now comparing those ASCII code to the ASCII table, we get :
```
ft_wandr[erase][erase][erase]NDRel[erase]L0L[enter]
ft_waNDReL0L
```

And sure enough, that the password I was looking for :

```
su flag02
Password: ft_waNDReL0L
Don't forget to launch getflag !
flag02@SnowCrash:~$ getflag
Check flag.Here is your token : kooda2puivaav1idi4f57q8iq
```

### Level03