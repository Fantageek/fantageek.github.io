---
author: onmyway133
comments: true
date: 2013-04-23 08:45:42+00:00
layout: post
slug: how-to-test-for-request-timeout
title: How to test for request timeout
wordpress_id: 303
categories:
- Others
tags:
- request
- test
- timeout
---

Sometimes your software can hit bugs when a connection timeout occur. You want to reproduce this case to find the bug.
Here are 2 simple ways. The trick is that you can never receive reponse, thus cause a request timeout :)

**Way 1**
Block inbound traffic for that specific IP in your router/firewall

[![firewall_inbound_rule](http://www.fantageek.com/wp-content/uploads/2013/04/firewall_inbound_rule.png)](http://www.fantageek.com/303/how-to-test-for-request-timeout/firewall_inbound_rule/)

**Way 2**
If you can't touch the router. You can install [NetLimiter](http://www.netlimiter.com/). Do these steps
1. Select your software process.
2. Choose Add Rule in the right panel, with these options



	
  * Rule type: Firewall

	
  * Direction: In

	
  * Enabled: Checked

	
  * Action: Deny


[![netlimiter_firewall_rule](http://www.fantageek.com/wp-content/uploads/2013/04/netlimiter_firewall_rule.png)](http://www.fantageek.com/303/how-to-test-for-request-timeout/netlimiter_firewall_rule/)
