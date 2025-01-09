---
title: 基于Docker安装Elasticsearch
published: 2025-01-07
description: This post demonstrates how to include embedded video in a blog post.
tags: [Elasticsearch]
category: Linux
draft: false
---


```yaml
---
title: 基于Docker安装Elasticsearch 
published: 2025-01-07

---

```

### 因为需要部署kibana容器，因此需要让es和kibana容器互联。
```
docker network create es-net
```


### 拉取镜像
```
docker pull elasticsearch:8.6.0
```


### 创建挂载点目录
```
mkdir -p /usr/local/es/data /usr/local/es/config /usr/local/es/plugins

chmod 777  /usr/local/es/data
chmod 777  /usr/local/es/config
chmod 777  /usr/local/es/plugins
```


### 部署单点es，创建es容器
```
docker run -d \
--restart=always \
--name es \
--network es-net \
-p 9200:9200 \
-p 9300:9300 \
--privileged \
-v /usr/local/es/data:/usr/share/elasticsearch/data \
-v /usr/local/es/plugins:/usr/share/elasticsearch/plugins \
-e "discovery.type=single-node" \
-e "ES_JAVA_OPTS=-Xms512m -Xmx512m" \
elasticsearch:8.6.0
```


### 因为需要部署kibana容器，因此需要让es和kibana容器互联。
```
[root@bogon howlong]# docker exec -it es /bin/bash #先进入es容器
elasticsearch@6b64658d77d5:~$ cd config
echo 'xpack.security.enabled: false' >> elasticsearch.yml #关闭密码安全验证
docker restart es
```

### 测试es
```
 http://127.0.0.1:9200
```

### 因为需要部署kibana容器，因此需要让es和kibana容器互联。
```
docker pull kibana:8.6.0
mkdir -p /usr/local/kibana/config /usr/local/kibana/data
chmod 777 /usr/local/kibana/data
chmod 777 /usr/local/kibana/config

docker run -d \
--restart=always \
--name kibana \
--network es-net \
-p 5601:5601 \
-e ELASTICSEARCH_HOSTS=http://es:9200 \
kibana:8.6.0
```

### 测试Kibana是否安装成功
```
http://192.168.27.129:5601
```

### 注意：安装IK分词器的版本，必须和Elasticsearch的版本一致，上文安装的是Elasticsearch 8.6.0的，所以接下来安装的IK分词器版本是8.6.0
```
[root@bogon howlong]# docker exec -it es /bin/bash
elasticsearch@6b64658d77d5:~$ 
./bin/elasticsearch-plugin install https://github.com/medcl/elasticsearch-analysis-ik/releases/download/v8.6.0/elasticsearch-analysis-ik-8.6.0.zip
```

## 以下是集群 
```
1进入目录 cd /home/mason/ 
文件夹创建命令  mkdir es
cd es
2导入文件 elasticsearch.tar,kibana.tar
docker load -i elasticsearch.tar
docker load -i kibana.tar

3.# root用户 编辑
vim /etc/sysctl.conf
vm.max_map_count=655360
# 保存后 执行
sysctl -p

4.创建目录（普通用户）
mkdir /home/mason/elk
cd /home/mason/elk
mkdir /home/mason/elk/kibana
cd /home/mason/elk/kibana

5.编辑文件 修改地址成 es:9200
vim kibana.yml

cd /home/monitor/mason/elk
上传文件plugins.zip
Unzip plugins.zip

mkdir /home/mason/elk/single
cd /home/mason/elk/single

mkdir -p /home/mason/elk/single/data

chmod -R 777 /home/mason/elk/single/

Cd  /home/mason/elk/single/
Cd  /home/mason/elk/single/
在目录single里面导入
导入文件 docker-compose.yml

7 创建容器 
docker-compose up -d
8检查状态  docker ps -a 

9检测是否生效 http://ip:9200
10验证是否集群成功 http://ip:9200/_cat/nodes?v
```
### docker-compose.yml
```yaml
version: '3.6'
services:
  es0:
    image: 容器id
    container_name: es0 #集群要改es1 es2
    privileged: true
    environment:
      # 设置集群名称，集群内所有节点的名称必须一致。
      - cluster.name=elasticsearch-cluster
      # 设置节点名称，集群内节点名称必须唯一。node1 node2
      - node.name=node0
      # 表示该节点会不会作为主节点，true表示会；false表示不会
      - node.master=true
      # 当前节点是否用于存储数据，是：true、否：false
      - node.data=true
      # 需求锁住物理内存，是：true、否：false
      - bootstrap.memory_lock=true
      #查询结果在分片上找到的条目  默认10000个
      - search.max_buckets=100000000
      # 是否支持跨域，是：true，在使用head插件时需要此配置
      - http.cors.enabled=true
      # “*” 表示支持所有域名
      - http.cors.allow-origin=*
      # es7.x 之后新增的配置，初始化一个新的集群时需要此配置来选举master 仅集群第一次启动时
      - cluster.initial_master_nodes=node0,node1,node2
      - "ES_JAVA_OPTS=-Xms2048m -Xmx2048m"
      #端口
      - http.port=9200
      #节点间通讯端口
      - transport.tcp.port=9300
      #- "discovery.zen.ping.unicast.hosts=es0,es1,es2"
      # 如果是拆分版，这里必须填写宿主机ip,节点间交互的tcp端口，默认是9300
      - discovery.seed_hosts=193.x.x.x0:9300,193.x.x.x1:9300,193.x.x.x2:9300
      # 设置这个参数来保证集群中的节点可以知道其它N个有master资格的节点。默认为1，对于大的集群来说，可以设置大一点的值（2-4）
      - "discovery.zen.minimum_master_nodes=2"
      #设置集群中自动发现其它节点时ping连接超时时间，默认为3秒，对于比较差的网络环境可以高点的值来防止自动发现时出错。
      - discovery.zen.ping_timeout=120s
      #TransportClient ping命令的响应超时时间，默认为5s。
      - client.transport.ping_timeout=60s
      # 如果是拆分版，这条配置必须加上，指定当前节点访问的ip
      - network.publish_host=193.x.x.x0
      - TZ=Asia/Shanghai
    ulimits:
      memlock:
        soft: -1
        hard: -1
    volumes:
      - /etc/localtime:/etc/localtime
      - ../plugins:/usr/share/elasticsearch/plugins
      - ./data:/usr/share/elasticsearch/data
    ports:
      - "9200:9200"
      - "9300:9300"
    network_mode: host
    healthcheck:
      test: ["CMD-SHELL", "curl --silent --fail localhost:9200/_cluster/health || exit 1"]
      interval: 30s
      timeout: 10s
      retries: 3
    restart: always
    # networks:
    #  - esnet
    #networks:
    #esnet:
    #driver: bridge

```

## 配置kibana
```
1 进入目录 
cd /home/mason/elk/kibana
2 导入文件docker-compose-kibana.yml
3创建容器 docker-compose -f docker-compose-kibana.yml up -d
三台执行3次

登录 http://ip:5601/app/kibana

# dev tools 配置kibana 多文件管道处理脚本
PUT _ingest/pipeline/attachment
{
  "description": "多文件管道处理",
  "processors": [
    {
      "foreach": {
        "field": "attachments",
        "processor": {
          "attachment": {
            "target_field": "_ingest._value.attachment",
            "field": "_ingest._value.data",
            "ignore_missing": true
          }
        }
      }
    },
    {
      "foreach": {
        "field": "attachments",
        "processor": {
          "remove": {
            "field": "_ingest._value.data"
          }
        }
      }
    }
  ]
}

```

### docker-compose-kibana.yml
```yaml
version: '3.6'
services:
  kibana:
    image: kibana:7.17.5
    container_name: kibana
    ports:
      - "5601:5601"
    network_mode: host
    volumes:
      - /etc/localtime:/etc/localtime
      - ../kibana/kibana.yml:/usr/share/kibana/config/kibana.yml
    environment:
      - TZ=Asia/Shanghai
    restart: always
```

## 开启es安全验证
```
进入容器
docker exec it 容器id /bin/bahs

生成证书
./bin/elasticsearch-certutil ca 
默认空 敲二次回车

生成安全密钥
./bin/elasticsearch-certutil cert --ca elastic-stack-ca.p12
敲三次回车

移动安全证书到config 目录下
mv  elastic-certificates.p12 config/

赋777权限
chmod 777 config/elastic-certificates.p12

复制到主机上修改添加验证，再复制回docker容器里
添加配置如下

xpack.security.enabled: true
xpack.security.transport.ssl.enabled: true
xpack.security.transport.ssl.verification_mode: certificate
xpack.security.transport.ssl.keystore.path: elastic-certificates.p12
xpack.security.transport.ssl.truststore.path: elastic-certificates.p12

返回容器的elasticsearch目录执行命令添加密码 前面设置空密码，这边直接回车

./bin/elasticsearch-keystore add xpack.security.transport.ssl.keystore.secure_password
./bin/elasticsearch-keystore add xpack.security.transport.ssl.truststore.secure_password

拷贝安全证书 到其他服务器上
docker cp elastic-certificates.p12 其他服务

docker restart es容器

进入容器

执行 
./bin/elasticsearch-setp-passwords interactvie
有多密码要设置，要记住

修改 kibanna.yml 添加
elasticsearch.username: "elastic"
elasticsearch.password: "密码"

```


