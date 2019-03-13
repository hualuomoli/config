# Redis集群

#  安装依赖 
```
yum install -y gcc-c++
```

# 配置环境变量

安装目录 
```
export REDIS_PATH=/opt/redis-5.0.3
```

# 安装Redis

下载 
```
wget http://download.redis.io/releases/redis-5.0.3.tar.gz
```
解压 
```
tar -zxvf redis-5.0.3.tar.gz
```
进入Redis安装目录
```
cd redis-5.0.3
```
安装 
```
make install PREFIX=$REDIS_PATH
```
添加到系统环境变量 
```
echo  >> /etc/profile
echo # Redis >> /etc/profile
echo PATH=\$PATH:$REDIS_PATH/bin >> /etc/profile
```
刷新环境变量 
```
source /etc/profile
```
复制默认配置文件 
```
cp ./redis.conf $REDIS_PATH/redis.conf
```

# 关闭防火墙
```
systemctl stop firewalld
```


# 配置工作节点
测试使用3台机器，每台机器配置6个节点

创建工作目录          
```
mkdir -p $REDIS_PATH/worker/{redis01,redis02,redis03,redis04,redis05,redis06}
```
创建节点配置文件
+ 节点1
```
sed \
-e 's/^bind 127.0.0.1$/bind 0.0.0.0/g' \
-e 's/^port 6379$/port 7001/g' \
-e 's/^daemonize no$/daemonize yes/g' \
-e 's/^# cluster-enabled yes$/cluster-enabled yes/g' \
$REDIS_PATH/redis.conf > $REDIS_PATH/worker/redis01/redis.conf
```
+ 节点2
```
sed \
-e 's/^bind 127.0.0.1$/bind 0.0.0.0/g' \
-e 's/^port 6379$/port 7002/g' \
-e 's/^daemonize no$/daemonize yes/g' \
-e 's/^# cluster-enabled yes$/cluster-enabled yes/g' \
$REDIS_PATH/redis.conf > $REDIS_PATH/worker/redis02/redis.conf
```
+ 节点3
```
sed \
-e 's/^bind 127.0.0.1$/bind 0.0.0.0/g' \
-e 's/^port 6379$/port 7003/g' \
-e 's/^daemonize no$/daemonize yes/g' \
-e 's/^# cluster-enabled yes$/cluster-enabled yes/g' \
$REDIS_PATH/redis.conf > $REDIS_PATH/worker/redis03/redis.conf
```
+ 节点4
```
sed \
-e 's/^bind 127.0.0.1$/bind 0.0.0.0/g' \
-e 's/^port 6379$/port 7004/g' \
-e 's/^daemonize no$/daemonize yes/g' \
-e 's/^# cluster-enabled yes$/cluster-enabled yes/g' \
$REDIS_PATH/redis.conf > $REDIS_PATH/worker/redis04/redis.conf
```
+ 节点5
```
sed \
-e 's/^bind 127.0.0.1$/bind 0.0.0.0/g' \
-e 's/^port 6379$/port 7005/g' \
-e 's/^daemonize no$/daemonize yes/g' \
-e 's/^# cluster-enabled yes$/cluster-enabled yes/g' \
$REDIS_PATH/redis.conf > $REDIS_PATH/worker/redis05/redis.conf
```
+ 节点6
```
sed \
-e 's/^bind 127.0.0.1$/bind 0.0.0.0/g' \
-e 's/^port 6379$/port 7006/g' \
-e 's/^daemonize no$/daemonize yes/g' \
-e 's/^# cluster-enabled yes$/cluster-enabled yes/g' \
$REDIS_PATH/redis.conf > $REDIS_PATH/worker/redis06/redis.conf
```
+ 其它机器同样配置


# 启动redis节点

```
cd $REDIS_PATH/worker/redis01 && redis-server ./redis.conf
cd $REDIS_PATH/worker/redis02 && redis-server ./redis.conf
cd $REDIS_PATH/worker/redis03 && redis-server ./redis.conf
cd $REDIS_PATH/worker/redis04 && redis-server ./redis.conf
cd $REDIS_PATH/worker/redis05 && redis-server ./redis.conf
cd $REDIS_PATH/worker/redis06 && redis-server ./redis.conf
```
其它机器同样启动


# 查询Redis状态
```
ps -ef | grep redis
```

# 集群

# 配置机器IP
```
export HOST1=192.168.59.101
export HOST2=192.168.59.102
export HOST3=192.168.59.103
```

执行命令
```
redis-cli --cluster create \
--cluster-replicas 1 \
$HOST1:7001 $HOST1:7002 $HOST1:7003 $HOST1:7004 $HOST1:7005 $HOST1:7006 \
$HOST2:7001 $HOST2:7002 $HOST2:7003 $HOST2:7004 $HOST2:7005 $HOST2:7006 \
$HOST3:7001 $HOST3:7002 $HOST3:7003 $HOST3:7004 $HOST3:7005 $HOST3:7006
```


# 杀死进程
```
ps aux | grep redis| grep -v grep | awk '{print $2}' | xargs kill -9
```
