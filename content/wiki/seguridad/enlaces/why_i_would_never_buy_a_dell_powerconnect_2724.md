---
title: "Why I would never buy a Dell PowerConnect 2724"
date: 2017-01-06T12:01:51-03:00
---

### Thu, 17 Mar 2011

**Why I would never buy a Dell PowerConnect 2724**

At work, we acquired the above mentioned 24Port switch from Dell. It supports a so called managed mode (needs to be enabled by poking into a hole on the switch with a paperclip).

Enabling that sets the switch IP to 192.168.2.1/24 – with no way to change that. So far so annoying. Ok, I can configure an alias on my Ethernet to connect to that.

When you do that, don’t be impatient, the IP needs ages to come up, but eventually you will be able to ping it.

I was a bit surprised that it actually only supports http on port :80, no https, no ssh, no telnet and no snmp. – But ok, lets log in via http.

The login page is not only a \<frameset\> (well, it came out 2005, but was that really necessary?), but only works with javascript: – WTF?

Trying to login with the default credentials (user admin, empty password) results in a Error message User name or Password is missing.

Googling for that error message finds hundreds of hits of people having the same problem, and Dell support always saying the same thing: Clear your browser cache, or similar unhelpful things. People report different levels of success by using different browsers, but it appears that nobody has a clue what the real problem is.

But I can tell you now. After some debugging I found the problem. It’s not even the javascript. That is only there to make your life difficult.

Here is what the login attempt looks like from Firefox:

```
T 192.168.2.10:51020 -> 192.168.2.1:80 [AP]
  POST /tgi/login.tgi HTTP/1.0..Host: 192.168.2.1..User-Agent: Mozilla/5.0 (W
  indows; U; Windows NT 5.1; de; rv:1.9.2.15) Gecko/20110303 Firefox/3.6.15..
  Accept: text/html,application/xhtml+xml,application/xml;q=0.9,*/*;q=0.8..Ac
  cept-Language: en,en-us;q=0.7,de;q=0.3..Accept-Encoding: gzip,deflate..Acce
  pt-Charset: ISO-8859-1,utf-8;q=0.7,*;q=0.7..Referer: http://192.168.2.1/log
  in11.htm..Cookie: SSID=cd12579c608e40a2ab7df4316d1eec76..Content-Type: appl
  ication/x-www-form-urlencoded..Content-Length: 97..Connection: keep-alive..
  ..
T 192.168.2.10:51020 -> 192.168.2.1:80 [AP]
  Username=admin&Password=4d454698e2f95b3664cd7e93c8c064b2&Session=697fb62d41
  f125f969b9ae31dc392cc8
```

which results in your favourite error page:

```
T 192.168.2.1:80 -> 192.168.2.10:51020 [AP]
  HTTP/1.0 200 OK..Set-Cookie: SSID=cd12579c608e40a2ab7df4316d1eec76; path=/.
  .Connection: close....<html>..<head>....<title>Error</title>..<link rel="ST
  YLESHEET" type="text/css" href="/style.css">..<script type="text/javascript
  " src="/globalFunctions.js"></script>..</head>..<body text="#000000" bgcolo
  r="#FFFFFF" leftmargin="15">..<script type="text/javascript">Draw_Header(do
  cument.title);</script>..<span class="data-area-prompt-text">User name or P
  assword is missing</span><br><br>....</body>..</html>......
```

Getting a little ahead of myself, a successfull login looks like this:

```
T 192.168.2.10:34456 -> 192.168.2.1:80 [AP]
  POST /tgi/login.tgi HTTP/1.0..Host: 192.168.2.1..User-Agent: Mozilla/5.0 (W
  indows; U; Windows NT 5.1; de; rv:1.9.2.15) Gecko/20110303 Firefox/3.6.15..
  Accept: text/html,application/xhtml+xml,application/xml;q=0.9,*/*;q=0.8..Ac
  cept-Language: en,en-us;q=0.7,de;q=0.3..Accept-Encoding: gzip,deflate..Acce
  pt-Charset: ISO-8859-1,utf-8;q=0.7,*;q=0.7..Referer: http://192.168.2.1/log
  in11.htm..Cookie: SSID=cd12579c608e40a2ab7df4316d1eec76..Content-Type: appl
  ication/x-www-form-urlencoded..Content-Length: 97..Connection: keep-alive..
  ..Username=admin&Password=dbe6d16727e70eb52b299060ef600111&Session=23ceb0d6
  04f433579f6737cac5d0b585..

T 192.168.2.1:80 -> 192.168.2.10:34456 [AP]
  HTTP/1.0 302 Found..Location: /index.htm..Set-Cookie: SSID=23ceb0d604f43357
  9f6737cac5d0b585; path=/..Connection: close....
```

See the difference? It’s all in one TCP packet instead of in two.

That’s right. Either the TCP stack, or the HTTP server are \_very\_ broken.

If you ever need to login to such a switch, here’s the minimal version:

```
#!/bin/sh
(

# Default user, and blank password
user=admin
pass=

# The "session" key is part of the login frame
ssid=`wget -q -O - http://192.168.2.1/login11.htm|sed -n 's/.*Session" value="\(.*\)".*/\1/p'`

# Calculate the "password"
pw=`echo -n ${user}${pass}${ssid}|openssl md5 -hex`

# Send it off to the server
cat << EOM
POST /tgi/login.tgi HTTP/1.0
Host: 192.168.2.1
Referer: http://192.168.2.1/login11.htm
Content-Type: application/x-www-form-urlencoded
Content-Length: 97

Username=${user}&Password=${pw}&Session=${ssid}
EOM
) | while read a ; do
echo $a\\015 # Do "Network-style" newlines
done |cat|nc 192.168.2.1 80
# The second "cat" may be necessary to get it all buffered up correctly
```

You can now use the resulting cookie to access the web-interface. If you want to use your favourite browser, you can edit your cookie with the help of the [CookieSafe](https://addons.mozilla.org/en-US/firefox/addon/cookiesafe/) or [EditCookies](https://addons.mozilla.org/en-US/firefox/addon/edit-cookies/) extension.

Well, I hope this helps anyone out there, because we most certainly won’t use this switch for anything more than a dumb switch for a test network.

**TL;DR**: Their TCP/HTTP code is **broken**, and their support doesn’t know it.

Have fun.

– Sec

posted at: 10:07

### Referencia

- <http://blogmal.42.org/tidbits/no-dell-2724.story>
