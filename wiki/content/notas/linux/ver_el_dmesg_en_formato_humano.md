---
title: "Ver el dmesg en formato humano"
date: 2014-01-02T17:39:30-03:00
---

## Ver el dmesg en formato humano

### Alternativa I

```perl>
==== Alternativa II ====

<code perl>
#!/usr/bin/perl -wn
use strict;
 
foreach my $line (<>) {
    my ($uptime) = (do { local @ARGV='/proc/uptime';<>} =~ /^(\d+)\./);
    $line=~/^\[\s*(\d+)\.\d+\](.+)/; 
    printf "[%s]%s\n", scalar localtime(time - $uptime + $1), $2;
}
```
