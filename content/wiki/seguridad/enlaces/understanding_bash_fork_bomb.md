---
title: "Understanding Bash fork() bomb ~ :(){ :|:& };:"
date: 2014-06-03T11:05:13-03:00
---

## Understanding Bash fork() bomb ~ :(){ :\|:& };:

**Q.** Can you explain following bash code or bash fork() bomb? :(){ :\|:& };:

**A.** This is a bash function. It gets called recursively (recursive function). This is most horrible code for any Unix / Linux box. It is often used by sys admin to test user processes limitations (Linux process limits can be configured via /etc/security/limits.conf and PAM).

Once a successful fork bomb has been activated in a system it may not be possible to resume normal operation without rebooting, as the only solution to a fork bomb is to destroy all instances of it. \[Warning examples may crash your computer\] WARNING! These examples may crash your computer if executed. Understanding :(){ :\|:& };: fork() bomb code

:() - It is a function name. It accepts no arguments at all. Generally, bash function is defined as follows:

```bash>
fork() bomb is defined as follows:

<code bash>
:(){
 :|:&
};:
```

**:\|:** - Next it call itself using programming technique called recursion and pipes the output to another call of the function ‘:’. The worst part is function get called two times to bomb your system.

**&** - Puts the function call in the background so child cannot die at all and start eating system resources.

**;** - Terminate the function definition

**:** - Call (run) the function aka set the fork() bomb.

Here is more human readable code:

```bash>
Properly configured Linux / UNIX box should not go down when fork() bomb sets off.

==== Extra ====

Perl exmaple:

<code perl>
perl -e "fork while fork" &
```

Python example:

```python>
Windows XP / Vista bat file example:

<code>
:bomb
start %0
goto bomb
```

UNIX style for Windows:

```
%0|%0
```

C program example:

```c
#include
 int main() {   while(1)      fork();  } 
```

Plz note that the fork bomb is a form of denial of service, so don’t run on production or unauthorized system.

### Fuente

<http://www.cyberciti.biz/faq/understanding-bash-fork-bomb/#comment-37097>
