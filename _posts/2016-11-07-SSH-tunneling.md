---
layout: post
title: "How to SSH tunneling"
description: ""
category: linux
tags:
---

### How to SSH tunneling

* ssh -L [bind_address:]port:host:hostport

<br>

* for e,g) if remote-server is only allowed to access from bastion-server, you can use ssh tunneling as below:

{% highlight bash %}
# on local server,

[annmoon@local ~ ]$ ssh -f -N userid@bastion-server -L 3389:remote-server:389
{% endhighlight %}

<br>

* and then, when you access to 127.0.0.1:3389, you can go to remote-server:389

<br>

>
-f: Requests ssh to go to background just before command execution.  This is useful if ssh is going to ask for passwords or passphrases, but the user wants it in the background.

<br>

>
-N: Do not execute a remote command.  This is useful for just forwarding ports (protocol version 2 only)

<br>

>
-L: Specifies that the given port on the local (client) host is to be forwarded to the given host and port on the remote side.  This works by allocating a socket to listen to port on the local side, optionally bound to the spec-ified bind_address.  
Whenever a connection is made to this port, the connection is forwarded over the secure channel, and a connection is made to host port hostport from the remote machine. 
Port forwardings can also be specified in the configuration file.  
IPv6 addresses can be specified by enclosing the address in square brackets.  
Only the superuser can forward privileged ports.  
By default, the local port is bound in accordance with the GatewayPorts setting.  
However, an explicit bind_address may be used to bind the connection to a specific address.  
The bind_address of ``localhost'' indicates that the listening port be bound for local use only, while an empty address or `*' indicates that the port should be available from all interfaces.

