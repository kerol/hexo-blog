---
title: flask-cookie
date: 2017-01-11 17:04:04
tags: flask
---
### Flask Cookie
flask的response中如何加入cookie，设置cookie名称，有效期限
[官方文档](http://flask.pocoo.org/docs/0.12/quickstart/#cookies)
[文档地址](http://junxiandoc.readthedocs.io/en/latest/docs/flask/flask_cookie_session.html)
<!-- more -->
### Example
```
@bp.route('/')
def index():
    cookie_qr = 'qrcode'
    cookie_title = 'title'
    qrcode = request.cookies.get(cookie_qr)
    title = request.cookies.get(cookie_title)
    avatar = 'http://i.clp2p.com/qrcode_avatar.jpeg'
    if not qrcode:
        ret = QrCode.query.all()
        qrcode_list = [(item.image, item.name) for item in ret]
        randqr = random.sample(qrcode_list, 1)[0]
        qrcode, title = randqr[0], randqr[1]
        exp = datetime.datetime.now() + datetime.timedelta(days=1, hours=18)
        resp = current_app.make_response(render_template('/wechatqr/index.html',
               avatar=avatar, qrcode=qrcode, title=title))
        resp.set_cookie(cookie_qr, value=qrcode, expires=exp)
        resp.set_cookie(cookie_title, value=title, expires=exp)
    else:
        resp = current_app.make_response(render_template('/wechatqr/index.html',
               avatar=avatar, qrcode=qrcode, title=title))

    return resp
```
