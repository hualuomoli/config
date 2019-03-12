# Redis集群

# 集群服务器
这里使用的虚拟机搭建 <br />



服务器 | IP地址
-|-
redis1 | 192.168.59.101
redis2 | 192.168.59.102
redis3 | 192.168.59.103
redis4 | 192.168.59.104
redis5 | 192.168.59.105
redis6 | 192.168.59.106


# 安装redis
这里以`5.0.3`版本为例,安装到`/opt`目录 <br />

+ `wget http://download.redis.io/releases/redis-5.0.3.tar.gz`
+ `tar -zxvf redis-5.0.3.tar.gz`
+ `cd redis-5.0.3`
+ `make PREFIX=/opt/redis-5.0.3 install`


# 启动 Redis服务

+ 创建工作目录 `mkdir /opt/redis/work`
+ 复制`redis.conf`到工作目录 `cp redis.conf /opt/redis/work`
+ 修改redis.conf

```
bind 192.168.59.101              # 不同服务器修改成不同的值
port 6379                        # 端口
appendonly yes                   # 持久化
cluster-enabled yes              # 开启集群
cluster-config-file nodes.conf   # 集群配置文件
cluster-node-timeout 15000       # 集群超时时间
daemonize yes                    # 守护线程
```
+ 启动服务 `redis-server ./redis.conf`

