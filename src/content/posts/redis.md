---
title: redis install and deploy
published: 2025-01-07
description: This post demonstrates how to include embedded video in a blog post.
tags: [redis]
category: Linux
draft: false
---

Just copy the embed code from YouTube or other platforms, and paste it in the markdown file.

```yaml
---
title: redis 
published: 2025-01-07

---

```

## redis sentinel deploy
```
make
make install
make test
mkdir /usr/local/redis-cluster

cp redis.conf redis-cluster/redis-3
cp redis.conf redis-cluster/redis-2
cp redis.conf redis-cluster/redis-1

vim ./redis-1/redis.conf 
vim ./redis-2/redis.conf 
vim ./redis-3/redis.conf 

cp sentinel.conf redis-cluster/redis-3
cp sentinel.conf redis-cluster/redis-2
cp sentinel.conf redis-cluster/redis-1
vim ../redis-1/sentinel.conf 
vim ../redis-2/sentinel.conf 
vim ../redis-3/sentinel.conf 

./redis-server /home/devuser/app/redis-stable/redis-cluster/redis-1/redis.conf 
./redis-server /home/devuser/app/redis-stable/redis-cluster/redis-2/redis.conf 
./redis-server /home/devuser/app/redis-stable/redis-cluster/redis-3/redis.conf 

./redis-cli -p 7001
./redis-cli -p 7002
./redis-cli -p 7003
 info repication

SLAVEOF 127.0.0.1 7002
SLAVEOF 127.0.0.1 7003

./redis-sentinel /home/devuser/app/redis-stable/redis-cluster/redis-1/sentinel.conf 
./redis-sentinel /home/devuser/app/redis-stable/redis-cluster/redis-2/sentinel.conf 
./redis-sentinel /home/devuser/app/redis-stable/redis-cluster/redis-3/sentinel.conf 

 sudo firewall-cmd --zone=public --add-port=7001/tcp --permanent
 sudo firewall-cmd --zone=public --add-port=7002/tcp --permanent
 sudo firewall-cmd --zone=public --add-port=7003/tcp --permanent
```

## SPRINGBOOT REDIS 配置
```yaml
spring: #springboot3 spring.data.redis
  redis:
    database: 1
    host: vm_host
    port: 7001
    password: 123456
    lettuce:
      pool:
        min-idle: 0
        max-idle: 8
        max-active: 8
        max-wait: 1000

jetcache:
  statIntervalMinutes: 1 #每过1分钟在控制台汇总一次数据
  areaInCacheName: false
  local:
    default:
      type: caffeine
      keyConvertor: fastjson
      limit: 100
  remote:
    default:
      #keyPrefix: sms_ # 全局key前缀
      type: redis.springdata
      host: vm_host
      port: 7001
      keyConvertor: fastjson
      valueEncoder: kryo
      valueDecoder: kryo
      poolConfig:
        maxTotal: 50
        minIdle: 5
        maxIdle: 20

logging:
  level:com.alibaba.nacos.client.config.impl:WARN
```




