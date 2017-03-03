---
title: python-script
date: 2017-03-03 22:25:21
tags: python
---
### Pythonic sample
总结了一下python高级一点的用法：上下文、装饰器、匿名函数、多线程；
未来有空添加一些：描述符、迭代器、生成器、闭包、进程线程协程、socket或其他魔术方法等等~
markdown渲染的有点问题，有空也要改下这个主题~
<!-- more -->

    # -*- coding: utf-8 -*-
    from __future__ import print_function
    import time
    import functools
    import datetime
    import threading
    from sqlalchemy import create_engine
    from sqlalchemy.orm import sessionmaker
    import redis

    # mysql
    DB_CONNECT_STRING = 'mysql+mysqldb://username:password@localhost/dbname?charset=utf8'
    engine = create_engine(DB_CONNECT_STRING, echo=False)
    DB_Session = sessionmaker(bind=engine)
    session = DB_Session()


    class DateChecker(object):
        def __init__(self, date):
            self.date = date

        def __enter__(self):
            if not isinstance(self.date, datetime.datetime):
                self.date = datetime.datetime.strptime(self.date, '%Y-%m-%d')
            return self.date

        def __exit__(self, exec_type, exec_value, traceback):
            if exec_type is not None:
                print(traceback)
            return False

    def query_deco(date, days=0):
        def decorator(func):
            @functools.wraps(func)
            def wrapper(*args, **kwargs):
                with DateChecker(date) as tbegin:
                    pass
                tbegin += datetime.timedelta(days)
                tend = tbegin + datetime.timedelta(1)
                params = {
                    'tbegin': tbegin,
                    'tend': tend,
                }
                p = lambda sql, msg: print(tstr, msg, map(int, session.execute(sql, params).first().values()))
                tstr = tbegin.strftime('%Y-%m-%d')
                sql = 'select count(1) from user where create_time \
                       between :tbegin and :tend'
                p(sql, '注册人数')
                sql = 'select count(distinct(user_id)) from xxx where create_time \
                       between :tbegin and :tend and status=1'
                p(sql, '充值人数')
                sql = 'select floor(sum(money)/100) from xxx where create_time \
                       between :tbegin and :tend and status=1'
                p(sql, '充值金额')
                sql = 'select sum(money),sum(ret_money) from xxx where create_time \
                       between :tbegin and :tend'
                p(sql, '消费金额 中奖金额')
            return wrapper
        return decorator


    class QueryThread(threading.Thread):
        def __init__(self, date, days=0):
            threading.Thread.__init__(self)
            self.date = date
            self.days = days

        def run(self):
            @query_deco(self.date, self.days)
            def _run():
                pass
            _run()


    if __name__ == '__main__':
        date = '2017-03-01'
        print('日期：', date)

        t1 = QueryThread(date)
        t2 = QueryThread(date, 1)
        t1.start()
        t2.start()
