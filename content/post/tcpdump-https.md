---
title: tcpdump-https
description: TCP Dump HTTPS
tags: ["tcpdump", "https", "network", "packet", "kubernetes", "observability"]
date: 2023-05-21
publishdate: 2023-05-21
---

# How to capture HTTPS outbound request/response headers?

What to do when you get unexpected HTTPS Response code from your application or any third party application which you are using that connects to Rest endpoint? Especially when we don't have access to the target Rest endpoint?
When we seek for help, everyone will suggest to see the response headers of the HTTPS request.
But how can we see the response header? There are couple of ways:
* Log HTTPS request/response header in your application - Quicker solution
* Use any monitoring tools that provides this feature, could be costly
* Look for any tool. Simple Google search will suggest to use Fiddler but from my quick look that is for local developement debugging.
* Look for any other tool. HTTPToolKit looks promising but as of writing this article, it doesn't have headless feature. So, running this in your infra may be challenging. Especially in Kubernetes.
* Stick with old school approach, tcpdump.

## TCP Dump
Simple isn't it?
* Just take tcp dump by logging into the target pod/server
* Install tcpdump (better copy the binary)
* Take dump and copy it to your local
* Load it to Wireshark

Done, isn't it?

But you don't see any HTTPS traffic in the Wireshark UI. What are we missing?

Since HTTPS requests are HTTP over TLS. All the HTTPS requests are encrypted. We need the secrets to decrypt the data.

## What is SSLKEYLOGFILE?
It is an environment variable which is understood by most common used applications.

### Does Curl supports it?
Yes, but only the Curl with SSL support. (Build curl with SSL support - https://stackoverflow.com/questions/28782556/how-do-i-get-curl-to-use-https)
Just set the value of SSLKEYLOGFILE to the file path in the shell.
```
$ export SSLKEYLOGFILE=/tmp/sslkeylogfile.log

# Start tcpdump in another shell

$ curl https://google.com
<HTML><HEAD><meta http-equiv="content-type" content="text/html;charset=utf-8">
<TITLE>301 Moved</TITLE></HEAD><BODY>
<H1>301 Moved</H1>
The document has moved
<A HREF="https://www.google.com/">here</A>.
</BODY></HTML>

$ cat /tmp/SSLKEYLOGFILE
CLIENT_RANDOM C76EE3XXXXX FB0CE69XXXXX
```

### Does browsers supports it?
Yes, Just set the value of SSLKEYLOGFILE to the file path in the shell and open browser from the shell.
```
$ export SSLKEYLOGFILE=/tmp/sslkeylogfile.log

# Start tcpdump in another shell

$ /Applications/Google\ Chrome.app/Contents/MacOS/Google\ Chrome

$ cat /tmp/SSLKEYLOGFILE
CLIENT_RANDOM C76EE3XXXXX FB0CE69XXXXX
```

## Tell Wireshark to use the SSLKEYLOGFILE
Go to
* Wireshark Preferences
* Protocols --> TLS
* (Pre)-Master-Secret log filename --> Select /tmp/sslkeylogfile.log
* Open dump file
* Thats it, you can see the HTTPS requests


## But what about JAVA applications?
Make use of Java Agents.
* Download this Jar file, https://github.com/neykov/extract-tls-secrets
* Attach it to yoru Java application
```
java -javaagent:~/Downloads/extract-tls-secrets-4.0.0.jar=/tmp/sslkeylogfile.log -jar MyApp.jar
```
* In Kubernetes, we can make use of JAVA_TOOL_OPTIONS environment varilable.
```
env:
  name: JAVA_TOOL_OPTIONS
  value: -javaagent:~/Downloads/extract-tls-secrets-4.0.0.jar=/tmp/sslkeylogfile.log
```
