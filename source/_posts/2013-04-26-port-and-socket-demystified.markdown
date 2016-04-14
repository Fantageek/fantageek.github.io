---
author: onmyway133
comments: true
date: 2013-04-26 07:32:45+00:00
layout: post
slug: port-and-socket-demystified
title: Port and socket demystified
wordpress_id: 306
categories:
- Others
tags:
- active
- connection
- passive
- port
- socket
---

When digging into the world of TCP, I get many terminologies I don't know any misconceptions. But with the help of those geeks on SO, the problems were demystified. Now it's time to sum up and share with others :D


### What defines a unique connection ?


There are 5 elements that identify a connection. They call them 5-tuple



	
  1. Protocol. This is often omitted as it is understood that we are talking about TCP, which leaves 4.

	
  2. Source IP address.

	
  3. Source port.

	
  4. Target P address.

	
  5. Target port.




### Does TCP listen on one port and talk on another port ?


No. This is a common misconception. TCP listens on 1 port and talk on that same port. If clients make multiple TCP connection to server. It's the client OS that must generate different random source ports, so that server can see them as unique connections


### What is the maximum number of concurrent TCP connections that a server can handle, in theory ?


A single listening port can accept more than one connection simultaneously.There is a '64K' limit that is often cited, but that is per client per server port, and needs clarifying.

If a client has many connections to the same port on the same destination, then three of those fields will be the same - only source_port varies to differentiate the different connections. Ports are 16-bit numbers, therefore the maximum number of connections any given client can have to any given host port is 64K.

However, multiple clients can each have up to 64K connections to some server's port, and if the server has multiple ports or either is multi-homed then you can multiply that further

So the real limit is file descriptors. Each individual socket connection is given a file descriptor, so the limit is really the number of file descriptors that the system has been configured to allow and resources to handle. The maximum limit is typically up over 300K, but is configurable e.g. with sysctl


### Concurrent connection request vs Concurrent open connection


When clients want to make TCP connection with server, this request will be queued in server 's backlog queue. This backlog queue size is small (about 5 - 10), and this size limits the number of concurrent connection requests. However, server quickly pick connection request from that queue and accept it. Connection request which is accepted are called open connection. The number of concurrent open connections is limited by server 's resources allocated for file descriptor.


### Why connection is rejected after successful TCP handshake ?


It's normal. When server receive connection request from client (by receiving SYN), it will then response with SYN, ACK, hence cause successful TCP handshake. But this request are stills in backlog queue.

But, if the application process exceeds the limit of max file descriptors it can use, then when server calls accept, then it realizes that there are no file descriptors available to be
the allocated for the socket and fails the accept call and the TCP connection sending a FIN to other side


### What are active and passive socket ?


Sockets come in two primary flavors. An active socket is con­nect­ed to a remote active socket via an open data con­nec­tion... A passive socket is not con­nect­ed, but rather awaits an in­com­ing con­nec­tion, which will spawn a new active socket once a con­nec­tion is es­tab­lished

Each port can have a single passive socket binded to it, await­ing in­com­ing con­nec­tions, and mul­ti­ple active sockets, each cor­re­spond­ing to an open con­nec­tion on the port. It's as if the factory worker is waiting for new mes­sages to arrive (he rep­re­sents the passive socket), and when one message arrives from a new sender, he ini­ti­ates a cor­re­spon­dence (a con­nec­tion) with them by del­e­gat­ing someone else (an active socket) to ac­tu­al­ly read the packet and respond back to the sender if nec­es­sary. This permits the factory worker to be free to receive new packets


### Reference





	
  1. [What is the difference between a port and a socket?](http://stackoverflow.com/questions/152457/what-is-the-difference-between-a-port-and-a-socket)

	
  2. [Passive and active sockets](http://stackoverflow.com/questions/4696812/passive-and-active-sockets)

	
  3. [Why will a TCP Server send a FIN immediately after accepting a connection?](http://stackoverflow.com/questions/3870260/why-will-a-tcp-server-send-a-fin-immediately-after-accepting-a-connection)

	
  4. [What is the theoretical maximum number of open TCP connections that a modern Linux box can have](http://stackoverflow.com/questions/2332741/what-is-the-theoretical-maximum-number-of-open-tcp-connections-that-a-modern-lin)

	
  5. [socket listen backlog parameter, how to determine this value?](http://stackoverflow.com/questions/114874/socket-listen-backlog-parameter-how-to-determine-this-value)

	
  6. [Question about listening and backlog for sockets](http://stackoverflow.com/questions/4253454/question-about-listening-and-backlog-for-sockets)

	
  7. [Rejecting a TCP connection before it's being accepted?](http://stackoverflow.com/questions/1752219/rejecting-a-tcp-connection-before-its-being-accepted)

	
  8. [Best way to generate million tcp connection](http://stackoverflow.com/questions/6590181/best-way-to-generate-million-tcp-connection)

	
  9. [Max parallel http connections in a browser?](http://stackoverflow.com/questions/985431/max-parallel-http-connections-in-a-browser)

	
  10. [How to retain one million simultaneous TCP connections?](http://stackoverflow.com/questions/2831434/how-to-retain-one-million-simultaneous-tcp-connections)

	
  11. [TCP RST packet details](http://stackoverflow.com/questions/7735618/tcp-rst-packet-details)

	
  12. [How many tuples are there in a connection?](http://stackoverflow.com/questions/15761436/how-many-tuples-are-there-in-a-connection)

	
  13. [Does TCP use another port for sending data?](http://stackoverflow.com/questions/15761776/does-tcp-use-another-port-for-sending-data)


