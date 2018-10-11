## 安装mysql：

1. 在官网：<https://dev.mysql.com/downloads/windows/installer/5.7.html>
2. 如果提示没有`.NET Framework`框架。那么就在提示框中找到下载链接，下载一个就可以了。
3. 如果提示没有`Microsoft Virtual C++ x64(x86)`，那么百度或者谷歌这个软件安装即可。
4. 如果没有找到。那么私聊我。

## navicat：

navicat是一个操作mysql数据库非常方便的软件。使用他操作数据库，就跟使用excel操作数据是一样的。

## 安装驱动程序：

Python要想操作MySQL。必须要有一个中间件，或者叫做驱动程序。驱动程序有很多。比如有`mysqldb`、`mysqlclient`、`pymysql`等。在这里，我们选择用`pymysql`。安装方式也是非常简单，通过命令`pip install pymysql`即可安装。

## 数据库连接：

数据库连接之前。首先先确认以下工作完成，这里我们以一个`pymysql_test`数据库.以下将介绍连接`mysql`的示例代码：

```python
    import pymysql

    db = pymysql.connect(
        host="127.0.0.1",
        user='root',
        password='root',
        database='pymysql_test',
        port=3306
    )
    cursor = db.cursor()
    cursor.execute("select 1")
    data = cursor.fetchone()
    print(data)
    db.close()
```

## 插入数据：

```python
import pymysql

db = pymysql.connect(
    host="127.0.0.1",
    user='root',
    password='root',
    database='pymysql_test',
    port=3306
)
cursor = db.cursor()
sql = """
insert into user(
    id,username,gender,age,password
  ) 
  values(null,'abc',1,18,'111111');
"""
cursor.execute(sql)
db.commit()
db.close()
```

如果在数据还不能保证的情况下，可以使用以下方式来插入数据：

```python
sql = """
insert into user(
    id,username,gender,age,password
  ) 
  values(null,%s,%s,%s,%s);
"""

cursor.execute(sql,('spider',1,20,'222222'))
```

## 查找数据：

使用`pymysql`查询数据。可以使用`fetch*`方法。

1. `fetchone()`：这个方法每次之获取一条数据。
2. `fetchall()`：这个方法接收全部的返回结果。
3. `fetchmany(size)`：可以获取指定条数的数据。
   示例代码如下：

```python
cursor = db.cursor()

sql = """
select * from user
"""

cursor.execute(sql)
while True:
    result = cursor.fetchone()
    if not result:
        break
    print(result)
db.close()
```

或者是直接使用`fetchall`，一次性可以把所有满足条件的数据都取出来：

```python
cursor = db.cursor()

sql = """
select * from user
"""

cursor.execute(sql)
results = cursor.fetchall()
for result in results:
    print(result)
db.close()
```

或者是使用`fetchmany`，指定获取多少条数据：

```python
cursor = db.cursor()

sql = """
select * from user
"""

cursor.execute(sql)
results = cursor.fetchmany(1)
for result in results:
    print(result)
db.close()
```

## 删除数据：

```python
cursor = db.cursor()

sql = """
delete from user where id=1
"""

cursor.execute(sql)
db.commit()
db.close()
```

## 更新数据：

```python
conn = pymysql.connect(host='localhost',user='root',password='root',database='pymysql_demo',port=3306)
cursor = conn.cursor()

sql = """
update user set username='aaa' where id=1
"""
cursor.execute(sql)
conn.commit()

conn.close()
```