---
title: Encryption and Decryption
date: 2016-08-27 11:48:42
tags: encrypt
---
最近对接各家支付，加密方式和流程眼花缭乱，有空总结下加密方式和处理流程，包括但不限于AES、RSA、SHA、MD5、BASE64等。
由于时间和篇幅限制，只用Python语言做示例，Java的代码篇幅太大，一般PHP都有示例代码，毕竟是世界上最好的编程语言，
喝杯Java冷静下。。。

### 安装依赖库
pycrypto[https://www.dlitz.net/software/pycrypto/api/current/]
不太清楚CentOS服务器上的swig问题，Mac遇到的问题解决:
stackoverflow[http://stackoverflow.com/questions/33005354/]
```
pip install crypto # trouble comes here?
brew install openssl
brew install swig
env LDFLAGS="-L$(brew --prefix openssl)/lib" \
CFLAGS="-I$(brew --prefix openssl)/include" \
SWIG_FEATURES="-cpperraswarn -includeall -I$(brew --prefix openssl)/include" \
```

### AES
<!-- more -->
加密位数：128位，192位，256位；
模式：ECB，CBC常用；
填充模式：NoPadding, ZeroPadding, PKCS5Padding, PKCS7Padding常用；
PKCS#5 和 PKCS#7 唯一的区别就是前者规定了数据块大小是 64 比特(8 字节)，而 AES 中数据块大小是 128 比特(16字节)，因此在 AES 中， PKCS#5 和 PKCS#7 没有区别。
PyCrypto 是流行的 Python 加密/解密库。但是其 AES 的 ECB 模式在加密解密时未提供适合的密文填充工具，因此有必要自己实现一个，下面是常见的 PKCS#5 / PKCS#7 填充模式的一般写法：
```
BS = AES.block_size
pad =lambda s: s +(BS - len(s)% BS)* chr(BS - len(s)% BS)
unpad =lambda s : s[0:-ord(s[-1])]
```
示例代码：
```
from Crypto.Cipher import AES
import base64
AESKEY = '0123456789abcdef'
cipher = AES.new(AESKEY)
tmp = cipher.encrypt(pad(text))
encrypted = base64.b64encode(tmp)
```
