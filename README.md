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

