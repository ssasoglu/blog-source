---
title: "TIL: Checking Port Connections on Servers"
date: 2020-08-11 20:13:43
tags: 
- today
- i
- learned
- til
- servers
- nc
- telnet
- terminal
- bash
- commandline
---

`nc` is a great tool to check the port connection to a remote server. It is similar to `telnet`.

```bash
-v: verbose
-z: Scan for listening daemons, do not send any data
```

```bash
> nc -vz 10.xxx.xxx.xxx 1221
found 0 associations
found 1 connections:
     1: flags=82<CONNECTED,PREFERRED>
 outif en0
 src 192.168.xxx.xxx port 5333
 dst 10.xxx.xxx.xx0 port 1221
 rank info not available
 TCP aux info available
Connection to 10.xxx.xxx.xxx port 1221 [tcp/afs3-prserver] succeeded!
```
