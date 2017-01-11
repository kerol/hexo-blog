---
title: flask-request-files
date: 2017-01-11 10:08:54
tags: flask
---
### About
主要记录使用Flask框架时遇到的一些坑~
### request.files
```
import os
import hashlib
from flask import request
file_obj = request.files['filename']
md5_key = hashlib.md5(file_obj.read()).hexdigest()
file_obj.seek(0)  # 坑就在这里，Move cursor back to beginning so we can write to disk，不然file_obj会写入失败
#file_obj.save(os.path.join(path, filename))
```
<!-- more -->
