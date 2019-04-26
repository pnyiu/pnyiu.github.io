---
title: Access subnet behind DD-WRT
date: 2017-11-30 15:25:46
tags:
- Network
---

Below diagram illustrates the network design and layout of my testing environment:

{% img "https://docs.google.com/drawings/d/e/2PACX-1vQShbC3aNfgaeplwXNkBcBUku__hYVatDZv523TV4NiKXF2gBB6zYCpCvWtX1Iz084yKHkREZseepQ0/pub?w=809&h=559" %}

In office, I have two LAN ports and the IPs are assigned by the DHCP server. Therefore, I cannot assign static IPs to the servers. I decided to use the retired router to setup a subnet, so that I can allocate static IP address to the server attached to the router.

<!-- more -->

## PC (Win 10)
Add the route to the subnet (192.168.1.1/24)

{% codeblock lang:bash  %}
C:\Users\calviny>route add 192.168.1.0 mask 255.255.255.0 10.1.12.139
C:\Users\calviny>route print -4
IPv4 Route Table
===========================================================================
Active Routes:
Network Destination        Netmask          Gateway       Interface  Metric
          0.0.0.0          0.0.0.0        10.1.12.1      10.1.12.139     25
        10.1.12.0    255.255.255.0         On-link       10.1.12.139    281
      10.1.12.139  255.255.255.255         On-link       10.1.12.139    281
      10.1.12.255  255.255.255.255         On-link       10.1.12.139    281
        127.0.0.0        255.0.0.0         On-link         127.0.0.1    331
        127.0.0.1  255.255.255.255         On-link         127.0.0.1    331
  127.255.255.255  255.255.255.255         On-link         127.0.0.1    331
      192.168.1.0    255.255.255.0      10.1.12.135      10.1.12.139     26
        224.0.0.0        240.0.0.0         On-link         127.0.0.1    331
        224.0.0.0        240.0.0.0         On-link       10.1.12.139    281
  255.255.255.255  255.255.255.255         On-link         127.0.0.1    331
  255.255.255.255  255.255.255.255         On-link       10.1.12.139    281
{% endcodeblock %} 

## DD-WRT router
1. Network setup of the router (Setup -> Basic Setup)
{% img "/images/2017-11-30 -DD (build 21061) - Setup.png"  %}
1. Change the Operating Mode to Router (Setup -> Advanced Routing)
{% img "/images/2017-11-30 - DD (build 21061) - Routing.png"  %}
2. Add a new firewall rule to allow the traffic to be forwarded according (Administration -> Commands)
{% codeblock lang:bash  %}
iptables -I FORWARD -j ACCEPT
{% endcodeblock %}
{% img "/images/2017-11-30 -DD (build 21061) - Diagnostics.png"  %}

## Testing
{% codeblock lang:bash  %}
C:\Users\calviny>ping 192.168.1.192

Pinging 192.168.1.192 with 32 bytes of data:
Reply from 192.168.1.192: bytes=32 time=1ms TTL=63
Reply from 192.168.1.192: bytes=32 time=1ms TTL=63
Reply from 192.168.1.192: bytes=32 time<1ms TTL=63
Reply from 192.168.1.192: bytes=32 time=1ms TTL=63

Ping statistics for 192.168.1.192:
    Packets: Sent = 4, Received = 4, Lost = 0 (0% loss),
Approximate round trip times in milli-seconds:
    Minimum = 0ms, Maximum = 1ms, Average = 0ms
 {% endcodeblock %}

It is done. I can access the subnet behind DD-WRT router now.
