---
title: "Tcpdump TCP Filters"
date: 2020-05-22T22:58:48-04:00
draft: true
tags: [ "tcpdump", "howto" ]
---
# Background

It is occasionally useful to be able to capture packets with only defined TCP flags set while troubleshooting some issues. While you can always just search out the magic command line to do so, I had wanted to understand how the filters worked. I figured this would be a useful item to have in my toolkit.

For example lets capture any TCP packet that has SYN set:
``` bash
root@pi3:~# tcpdump -c 5 -n  -i eth0 'tcp[13] & 2!=0'
tcpdump: verbose output suppressed, use -v or -vv for full protocol decode
listening on eth0, link-type EN10MB (Ethernet), capture size 262144 bytes
23:11:10.209587 IP 192.168.0.1.34910 > 192.168.0.203.22: Flags [S], seq 2362277497, win 14600, options [mss 1460,sackOK,TS val 106825941 ecr 0,nop,wscale 5], length 0
23:11:10.209677 IP 192.168.0.203.22 > 192.168.0.1.34910: Flags [S.], seq 268558861, ack 2362277498, win 65160, options [mss 1460,sackOK,TS val 4270385136 ecr 106825941,nop,wscale 7], length 0
23:11:40.210755 IP 192.168.0.1.34951 > 192.168.0.203.22: Flags [S], seq 1681472778, win 14600, options [mss 1460,sackOK,TS val 106828941 ecr 0,nop,wscale 5], length 0
23:11:40.210852 IP 192.168.0.203.22 > 192.168.0.1.34951: Flags [S.], seq 1131820815, ack 1681472779, win 65160, options [mss 1460,sackOK,TS val 4270415137 ecr 106828941,nop,wscale 7], length 0
23:11:49.035658 IP 192.168.0.151.51346 > 192.168.0.203.80: Flags [S], seq 3625716288, win 29200, options [mss 1460,sackOK,TS val 2169716477 ecr 0,nop,wscale 7], length 0
5 packets captured
6 packets received by filter
0 packets dropped by kernel
```

# PCAP filters

A powerful tool in your arsenal is the careful use of PCAP filters. Beyond the usual 'host', 'port 80', 'ip proto vrrp' or the like you can also filter on data inside the packet.

```
To access data inside the packet, use the following syntax:
                   proto [ expr : size ]
              Proto is one of ether, fddi, tr, wlan, ppp, slip, link, ip, arp, rarp, tcp, udp, icmp, ip6 or radio, and indicates the protocol layer for  the
              index  operation.   (ether,  fddi,  wlan, tr, ppp, slip and link all refer to the link layer. radio refers to the "radio header" added to some
              802.11 captures.)  Note that tcp, udp and other upper-layer protocol types only apply to IPv4, not IPv6 (this will be fixed  in  the  future).
              The  byte offset, relative to the indicated protocol layer, is given by expr.  Size is optional and indicates the number of bytes in the field
              of interest; it can be either one, two, or four, and defaults to one.  The length operator, indicated by the keyword len, gives the length  of
              the packet.
```