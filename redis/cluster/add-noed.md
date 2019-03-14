# 添加节点

## 创建新增节点

创建目录
```
mkdir -p $REDIS_PATH/worker/{redis07,redis08}
```
添加配置信息
+ 节点7
```
sed \
-e 's/^bind 127.0.0.1$/bind 0.0.0.0/g' \
-e 's/^port 6379$/port 7007/g' \
-e 's/^daemonize no$/daemonize yes/g' \
-e 's/^# cluster-enabled yes$/cluster-enabled yes/g' \
$REDIS_PATH/redis.conf > $REDIS_PATH/worker/redis07/redis.conf
```
+ 节点8
```
sed \
-e 's/^bind 127.0.0.1$/bind 0.0.0.0/g' \
-e 's/^port 6379$/port 7008/g' \
-e 's/^daemonize no$/daemonize yes/g' \
-e 's/^# cluster-enabled yes$/cluster-enabled yes/g' \
$REDIS_PATH/redis.conf > $REDIS_PATH/worker/redis08/redis.conf
```
启动节点
```
cd $REDIS_PATH/worker/redis07 && redis-server ./redis.conf
cd $REDIS_PATH/worker/redis08 && redis-server ./redis.conf
```

## 执行命令

+ 添加主节点
```
redis-cli --cluster add-node $HOST2:7007 $HOST2:7006
```
7007是新节点7006是原来集群中的一个节点

+ 添加从节点
```
redis-cli --cluster add-node \
 --cluster-slave \
 --cluster-master-id ef0bb912c8654fa44a2c42c37e657354b77c7ec1 \
$HOST2:7008 $HOST2:7006
```
7008是新节点7006是原来集群中的一个节点
ef0bb912c8654fa44a2c42c37e657354b77c7ec1 是上一步主节点的node_id