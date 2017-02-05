---
title: PyPy
date: 2017-02-05 16:58:16
tags: [pypy]
---
### PyPy是什么？
[PyPy](http://pypy.org/index.html)使用rPython语言和JIT实现了可以替代CPython的Python解析器。
### 为什么选择PyPy？
主要原因是速度快。以寻找50000以内的素数为例，同样的代码CPython要用7点多秒，PyPy只需要0.8秒。
PyPy已经基本兼容Python2.7.12，但是对于Python3的兼容还没有那么快。
需要注意的地方：
CPython执行所有的代码都要解析机器码，但是PyPy的JIT技术优化部分代码到CPU中，所以速度回很快，但是前期的编译也会浪费部分时间，
对于重复执行的代码，PyPy的速度要比CPython快几倍，但是PyPy对于C语言的扩展还不是特别好，如果你的代码只需要执行一次或者频繁使用C语言扩展的话，
还是建议使用CPython，其他情况请尽情享受PyPy的速度吧！
<!-- more -->
### PyPy的安装
PyPy直接编译源代码比较麻烦，建议直接使用编译好的。
[下载地址](https://github.com/squeaky-pl/portable-pypy#portable-pypy-distribution-for-linux)
[参考文档](http://doc.pypy.org/en/latest/install.html)
下载完成后，解压缩到/opt目录下面，文件夹命名为pypy：
```
tar xf pypy-xxx.tar.bz2
mv pypy-xxx pypy
./pypy/bin/pypy
ln -s /usr/local/bin/pypy /opt/pypy/bin/pypy
./pypy-xxx/bin/pypy -m ensurepip
```
### PyPy与uwsgi
Environment: PyPy+uwsgi+flask+nginx
虚拟环境安装:
```
/opt/pypy-xxx/bin/pip install supervisor
virtualenv -p pypy env
env/bin/pip install -r requirements.txt
```
uwsgi.ini主要配置：
```
[uwsgi]
http = :5060
chdir = /path/to/project/home/folder
# cpython use module and callable, pypy use pypy-wsgi-file
#module = app
#callable = application
pypy-wsgi-file = app.py
master = true
```
wsgi协议app.py:
```
from main_module import init 
application = init.create_app()
```
