---
title: PGP
date: 2017-01-15 13:44:29
tags: encrypt
---
### About PGP
PGP是一种商业用的加密算法，用来加密文件、邮件等，openPGP是开源的实现，GPG是GNU的加密软件。
- PGP: Pretty Good Privacy
- GPG: GNU Privacy Guard
目前PGP主要用来加密邮件或者文件，本文主要介绍基于Mac OS X系统加密邮件。
<!-- more -->
[参考文章](http://notes.jerzygangi.com/the-best-pgp-tutorial-for-mac-os-x-ever/)
### Step 1: Install GPGTool
[下载地址](https://gpgtools.org/)
### Step 2: Create your PGP key
![创建pgp key](http://img.kerolgao.com/create_pgp_key.png)
说明：需要勾选Upload public key, Passphase在每次加密和解密的时候需要输入；
![创建后的pgp key](http://img.kerolgao.com/generated_pgp_key.png)
可以双击查看自己和他人的fingerprint.
### Step 3: Set GPGTool shortcut
![快捷键](http://img.kerolgao.com/gpgtool_shortcut.png)
主要有四个快捷键：
- OpenGPG: Sign Selection(⌃⌥⌘[ )
- OpenGPG: Encrypt Selection(⌃⌥⌘=)
- OpenGPG: Dencrypt Selection(⌃⌥⌘-)
- OpenGPG: Verify the signature of Selection(⌃⌥⌘])
### Step 4: Send encrpted email
邮件正文：
```
Hello PGP!
```
全选邮件正文，使用Sign的快捷键(⌃⌥⌘[ )，会弹出输入Passphase的提示，输入后邮件正文如下:
```
-----BEGIN PGP SIGNED MESSAGE-----
Hash: SHA512

Hello PGP!
-----BEGIN PGP SIGNATURE-----

iQIcBAEBCgAGBQJYexK6AAoJEEXpwpNFWe2K/DkQAJPeg1urSnUAhiDZCdPwBiRe
...
H1MGy00d7sFXvgTzgmxc
=PgEG
-----END PGP SIGNATURE-----

```
上面省略了部分signature，然后使用encrypt的快捷键(⌃⌥⌘=)，会弹出需要勾选收件人的pgp，
如果收件人不在GPGTool的列表里面，需要搜索并下载，勾选后邮件正文如下:
```
-----BEGIN PGP MESSAGE-----

hQIMAxhl/v0uzN5sAQ//VwjynMTtxOkwypWBVY4rAN8/Z0/vbWvChKWavsw8rUWq
...
VgXhSeYktHlvvaEZDN6CIyGLkKwxBOs=
=z4uL
-----END PGP MESSAGE-----

```
### Step 5: Decrypt email
复制加密的邮件正文到文本编辑器如sublime里，全选按下decrypt的快捷键(⌃⌥⌘-)，
然后就可以看到解密后的邮件内容如下:
```
-----BEGIN PGP SIGNED MESSAGE-----
Hash: SHA512

Hello PGP!
-----BEGIN PGP SIGNATURE-----

iQIcBAEBCgAGBQJYexK6AAoJEEXpwpNFWe2K/DkQAJPeg1urSnUAhiDZCdPwBiRe
...
H1MGy00d7sFXvgTzgmxc
=PgEG
-----END PGP SIGNATURE-----

```
可以按下verify的快捷键(⌃⌥⌘])验证成功的信息。
### 写在后面
Enjoy and keep secret!
