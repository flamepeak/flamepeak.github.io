---
title: ELK 安装与简单实用
date: 2020-08-30 23:54:55
tags: [ Learning]
categories: [Web后端]
---





## 安装ELK

### 安装 Elasticsearch

```sh
wget https://artifacts.elastic.co/downloads/elasticsearch/elasticsearch-7.7.1-linux-x86_64.tar.gz
wget https://artifacts.elastic.co/downloads/elasticsearch/elasticsearch-7.7.1-linux-x86_64.tar.gz.sha512
shasum -a 512 -c elasticsearch-7.7.1-linux-x86_64.tar.gz.sha512 
tar -xzf elasticsearch-7.7.1-linux-x86_64.tar.gz
cd elasticsearch-7.7.1/
```



运行：

````
can not run elasticsearch as root

su dbuser
./elasticsearch -v




````











错误1：

```
OpenJDK 64-Bit Server VM warning: INFO: os::commit_memory(0x00007f53abbe9000, 33554432, 0) failed; error='Not enough space' (errno=12)
```

```
free -m 
查看可用的swap空间
可以增大swap空间
或者，将/etc/elasticsearch/jvm.options 中的 -Xms512m -Xmx512m，进行修改，低于可用的swap空间
```

### 安装可视化工具elasticsearch-head

```sh
git clone git://github.com/mobz/elasticsearch-head.git
cd elasticsearch-head
npm install
npm run start
open http://localhost:9100/

Enable CORS in elasticsearch：
In elasticsearch configuration，
1. add http.cors.enabled: true
2. you must also set http.cors.allow-origin because no origin allowed by default. 
http.cors.allow-origin: "*" is valid value, however it’s considered as a security risk 
as your cluster is open to cross origin from anywhere.
```

错误1:

```
property [elasticsearch.version] is missing for plugin [head]
```

不要把elasticsearch-head放到plugins目录下，放到外面

或者使用chrome插件： [ElasticSearch Head](https://chrome.google.com/webstore/detail/elasticsearch-head/ffmkiejjmecolpfloofpjologoblkegm/)

错误2：

远程无法访问

修改yml:

```
network.host: 0.0.0.0
```

修改完，es无法启动：

```
配置cluster.initial_master_nodes为当前node，默认为注释，放开注释就行了

cluster.initial_master_nodes: ["node-1", "node-2"]
```



错误3：

```
1.max file descriptors [4096] for elasticsearch process is too low, increase to at least [65536]
修改/etc/security/limits.conf,增加配置，用户退出后重新登录生效

*               soft    nofile            655350
*               hard    nofile            655350

2、max number of threads [3818] for user [es] is too low, increase to at least [4096]
修改/etc/security/limits.conf,增加配置，用户退出后重新登录生效
*               hard    nproc           4096
*               soft    nproc           4096

3、max file descriptors [65535] for elasticsearch process is too low, increase to at least [65536]
修改/etc/sysctl.conf，增加如下配置
fs.file-max=655350
保存后执行sysctl -p 生效

4、max virtual memory areas vm.max_map_count [65530] is too low, increase to at least [262144]
修改/etc/sysctl.conf，增加如下配置
vm.max_map_count=262144
保存后执行sysctl -p 生效
```

错误4:

```
org.elasticsearch.discovery.MasterNotDiscoveredException:null

修改：cluster.initial_master_nodes: ["node-1"]
如果有node-2，但是它不存在，可能导致错误

```

错误5：

```
selinux 可能导致错误

vi /etc/sysconfig/selinux
```







### 安装Kibana

```sh
curl -O https://artifacts.elastic.co/downloads/kibana/kibana-7.7.1-linux-x86_64.tar.gz
curl https://artifacts.elastic.co/downloads/kibana/kibana-7.7.1-linux-x86_64.tar.gz.sha512 | shasum -a 512 -c - 
tar -xzf kibana-7.7.1-linux-x86_64.tar.gz
cd kibana-7.7.1-linux-x86_64/
```

运行：

```
./bin/kibana
```



搜索语句：

```
GET _cat/indices
{
}


GET how2java/_search
{
  "query": {
    "match": {
      "name": "连衣裙"
    }
  },
  "sort": [
    {
      "_id": "desc"
    }
  ]
}
```











错误1:

```
问题描述：[error][reporting] The Reporting plugin encountered issues launching Chromium in a self-test. You may have trouble generating reports.

error  [10:21:36.799] [error][reporting] Error: Failed to launch chrome! ... ... kibana-7.5.1linux-x86_64/data/headless_shell-linux/headless_shell: cannot execute binary file

解决方案：Kibana 后端模块 xpack.reporting 的截图功能使用到了 chrome，CentOS 暂时不支持 chrome，因此需要在 kibana.yml 配置文件中增加以下参数禁用 X-Pack reporting。

xpack.reporting.enabled:  false 
```





### 安装LogStash

```
yum install java-1.8.0-openjdk.x86_64

java -version

必须先安装java环境
```





### 安装ik

```
download pre-build package from here: https://github.com/medcl/elasticsearch-analysis-ik/releases

create plugin folder:
cd your-es-root/plugins/ 
mkdir ik

unzip zipfile to folder:
your-es-root/plugins/ik
```







```
GET _analyze
{
  "analyzer": "ik_max_word",
  "text": "你好中国，我叫马小玲"
}
```





### mongodb 数据导入ES

```
Linux bash:
mongoexport --collection=people --db=human60 --out=human60_people.json

Logstash: import
vi mongodb_to_ES.conf

input {
  file {
  path => "/home/dbuser/ELK/human60_people.json"
  start_position => "beginning"
  sincedb_path => "/dev/null"
  codec => json
  }
}
filter {
  mutate {
    remove_field => ["_id","path","host"]
  }
}
output {
  elasticsearch {
  hosts => [ "localhost:9200" ]
  index => "human60"
  }
}

```

由于ES中mapping无法修改，所以在数据导入之前一定号