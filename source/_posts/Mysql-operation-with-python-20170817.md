---
title: Python 操作Mysql数据库的一段代码
date: 2017-08-17 09:53:10
tags: [Python, Mysql]
categories: [Python]
---

这段代码的功能是，把favorite_news_staging数据库里的article_images表中的图片url改为图片名。

```Python
# -*- coding: utf-8 -*-

import MySQLdb, MySQLdb.cursors

# 打开数据库连接
db = MySQLdb.connect("localhost","root","password","favorite_news_staging" )

# 使用cursor()方法获取操作游标 
cursor = db.cursor()

# 使用execute方法执行SQL语句
cursor.execute("SELECT VERSION()")

# 使用 fetchone() 方法获取一条数据库。
data = cursor.fetchone()

print "Database version : %s " % data

sql_select = "select url from article_images" #查询文章是否存在
cursor.execute(sql_select)
result_url = cursor.fetchall()

for url in result_url:
    #print url[0]
    new_url = url[0].split('/')[-1]
    print new_url, url[0]
    sql_edit = "update article_images set url = %s where url = %s;"
    sql_edit_params = [new_url, url[0]]
    cursor.execute(sql_edit, sql_edit_params)
    db.commit()


# 关闭数据库连接
db.close()

```