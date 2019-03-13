# Redis集群

# 前置必读

安装依赖 
```
yum install -y gcc-c++
```
安装目录 
```
export REDIS_PATH=/opt/redis-5.0.3
```
集群机器
```
export HOST_REDIS_1=192.168.1.101
export HOST_REDIS_2=192.168.1.102
export HOST_REDIS_3=192.168.1.103
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

修改配置文件为集群模式 
```
sed 's/# cluster-enabled yes/cluster-enabled yes/g' $REDIS_PATH/redis.conf > $REDIS_PATH/redis-cluster.conf
```
创建工作目录          
```
mkdir -p $REDIS_PATH/worker/{redis01,redis02,redis03,redis04,redis05,redis06}
```
创建节点配置文件 
```
sed 's/6379/7001/g' $REDIS_PATH/redis-cluster.conf > $REDIS_PATH/worker/redis01/redis.conf
sed 's/6379/7002/g' $REDIS_PATH/redis-cluster.conf > $REDIS_PATH/worker/redis02/redis.conf
sed 's/6379/7003/g' $REDIS_PATH/redis-cluster.conf > $REDIS_PATH/worker/redis03/redis.conf
sed 's/6379/7004/g' $REDIS_PATH/redis-cluster.conf > $REDIS_PATH/worker/redis04/redis.conf
sed 's/6379/7005/g' $REDIS_PATH/redis-cluster.conf > $REDIS_PATH/worker/redis05/redis.conf
sed 's/6379/7006/g' $REDIS_PATH/redis-cluster.conf > $REDIS_PATH/worker/redis06/redis.conf
```
+ 其它机器同样配置


# 启动redis节点

```
cd $REDIS_PATH/worker/redis01 && redis-server ./redis.conf &
cd $REDIS_PATH/worker/redis02 && redis-server ./redis.conf &
cd $REDIS_PATH/worker/redis03 && redis-server ./redis.conf &
cd $REDIS_PATH/worker/redis04 && redis-server ./redis.conf &
cd $REDIS_PATH/worker/redis05 && redis-server ./redis.conf &
cd $REDIS_PATH/worker/redis06 && redis-server ./redis.conf &
```
其它机器同样启动


# 集群

## 修改hosts文件，映射域名与实际IP地址


```
echo $HOST_REDIS_1 redis-cluster-1 >> /etc/hosts
echo $HOST_REDIS_2 redis-cluster-2 >> /etc/hosts
echo $HOST_REDIS_3 redis-cluster-3 >> /etc/hosts
```
重启网络
```
sysetmctl restart network
```
