---
title: python-scripts
date: 2016-12-28 09:52:10
tags: python
---

## Networking
#### ip and int
```
>>> a = '192.168.0.1'
>>> import socket
>>> import struct
>>> b = socket.inet_aton(a)
>>> b
'\xc0\xa8\x00\x01'
>>> c = struct.unpack('!I', b)
>>> c
(3232235521,)
>>> c = struct.unpack('!I', b)[0]
>>> c
3232235521
>>> d = struct.pack('!I', c)
>>> d
'\xc0\xa8\x00\x01'
>>> e = socket.inet_ntoa(d)
>>> e
'192.168.0.1'
```
<!-- more -->
