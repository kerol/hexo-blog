---
title: flask-request-response
date: 2017-01-11 10:08:54
tags: flask
---
### flask reqeust and response
Http request and response object and methods.
<!-- more -->
## flask request
- form
- args
- values
- cookies
- stream
- headers
- data
- files
- get_json()
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
### request.headers
```
header = request.headers
header.get('Content-Type')
header.get('User-Agent')
```

## flask response
- headers
- status
- status_code
- data
- mimetype
- set_cookie()
