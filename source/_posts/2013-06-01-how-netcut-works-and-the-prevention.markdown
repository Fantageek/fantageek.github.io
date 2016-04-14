---
author: onmyway133
comments: true
date: 2013-06-01 02:59:30+00:00
layout: post
slug: how-netcut-works-and-the-prevention
title: How Netcut works and the prevention
wordpress_id: 359
categories:
- Others
tags:
- anti
- arp
- netcut
- prevent
- spoofing
---

Grrr, those bastards, they use Netcut to limit our connection. It's time to dig into the problem




**How does Netcut work**  

Netcut uses attacking technique called ARP Spoofing.




ARP (Address Resolution Protocol) is a link layer procotol, it is used for resolving network layer address (IP) into link layer address (MAC).  

When we want to send IP packet to another host (a computer in the same LAN, or the Gateway), we must know the destination IP address. In order for the packet to reach the destination, the destination IP address must be converted to the corresponding MAC address, so the transmission can be processed in the data link layer. To setup this mapping IP-MAC in the ARP table, we must first send ARP request as broadcast one to the LAN. In the happy case, the one with the IP in the ARP request will reply us with his MAC address.




Unfortunately, ARP is a stateless protocol. This means we continue to accept ARP replies and overwrite the old ones, even they have not expired yet. Worse, ARP does not define any authentication method to check whether the replies come from the trusted one (the one we want to receive the replies). These offer chances for the attacker to perform ARP Spoofing.




**The theory behind ARP Spoofing**  

From the machine in the LAN, the attacker associate his MAC address and IP address of the target host (usually the Gateway), so that any traffic sent to that target host will come to the attacker. Here the attacker can decide whether to modify the packet, send or not.




**There are 3 types of ARP Spoofing**  

1. Spoof the host computer  

2. Spoof the Gateway  

3. Spoof both the host computer and the Gateway




**To see the ARP table**  

Open Command line (Windows) or Terminal (Mac OSX), and type




[code language="css"]  

arp -a  

[/code]




This will list the MAC address associated with a specific IP. Note that some mappings maybe wrong due to ARP Spoofing




**How to prevent against this ?**  

To prevent spoofing our computer, we can use softwares (search for Anti Netcut, Anti ARP spoofing, ...) or set the static ARP ourselves. In fact, those softwares are based on setting static ARP. This way we set the static mapping IP-MAC for a specific host (computer or Gateway), and the OS definitely ignores all ARP replies for that IP.




Example of static ARP mapping associated with the Gateway, performed on the computer host




[code language="css"]  

192.168.1.1 B4-B3-62-7C-CE-55  

[/code]




Here 192.168.1.1 and B4-B3-62-7C-CE-55 are the Gateway's IP and MAC address




To prevent spoofing the Gateway, we must set static ARP on that Gateway. Go to the Gateway/Router interface, in its ARP setting, fill in the mapping IP-MAC for a specific host  

Look for that Gateway manual or the related guide on how to perform this. This is devices dependent-method, but the theory remains the same.




Example of static ARP mapping associated with the computer host, performed on the Gateway




[code language="css"]  

192.168.1.2 64-70-02-B2-9B-E1  

[/code]




Here 192.168.1.2 and 64-70-02-B2-9B-E1 are the machine host 's IP and MAC address. The machine host can be any computer in the LAN




**How to set static ARP**  

Proposed that  

192.168.1.1 The destination host 's IP  

B4-B3-62-7C-CE-55 The destination host 's MAC  

Local Area Connection Our network interface name




Open Command Line (Windows) or Terminal (Mac OSX) and type




**Windows XP**  

To delete specific mapping




[code language="css"]  

arp -d 192.168.1.1  

[/code]




To set specific mapping




[code language="css"]  

arp -s 192.168.1.1 B4-B3-62-7C-CE-55  

[/code]




**Windows 7 64bit**  

Maybe you need to open Command Line as Administration role  

To delete specific mapping




[code language="css"]  

netsh interface ipv4 delete neighbors 192.168.1.1  

[/code]




To set specific mapping




[code language="css"]  

netsh interface ipv4 add neighbors "Local Area Connection" 192.168.1.1 B4-B3-62-7C-CE-55  

[/code]




**Mac OSX**  

To delete specific mapping




[code language="css"]  

sudo arp -d 192.168.1.1  

[/code]




To set specific mapping




[code language="css"]  

sudo arp -s 192.168.1.1 B4-B3-62-7C-CE-55  

[/code]




**Reference**  

1. [http://en.wikipedia.org/wiki/ARP_spoofing](http://en.wikipedia.org/wiki/ARP_spoofing)
