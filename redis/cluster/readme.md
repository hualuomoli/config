# Redis集群

#  安装依赖 
```
yum install -y gcc-c++
```

# 配置环境变量

安装目录 
```
echo  >> /etc/profile
echo REDIS_PATH=/opt/redis-5.0.3 >> /etc/profile
echo RUBY_PATH=/opt/ruby-2.6.1 >> /etc/profile
```
集群机器
```
echo  >> /etc/profile
echo HOST_REDIS_1=192.168.1.101 >> /etc/profile
echo HOST_REDIS_2=192.168.1.102 >> /etc/profile
echo HOST_REDIS_3=192.168.1.103 >> /etc/profile
```
刷新环境变量 
```
source /etc/profile
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

创建工作目录          
```
mkdir -p $REDIS_PATH/worker/{redis01,redis02,redis03,redis04,redis05,redis06}
```
创建节点配置文件
+ 节点1
```
sed \
-e 's/^bind 127.0.0.1$/bind ::1/g' \
-e 's/^port 6379$/port 7001/g' \
-e 's/^daemonize no$/daemonize yes/g' \
-e 's/^# cluster-enabled yes$/cluster-enabled yes/g' \
$REDIS_PATH/redis.conf > $REDIS_PATH/worker/redis01/redis.conf
```
+ 节点2
```
sed \
-e 's/^bind 127.0.0.1$/bind ::1/g' \
-e 's/^port 6379$/port 7002/g' \
-e 's/^daemonize no$/daemonize yes/g' \
-e 's/^# cluster-enabled yes$/cluster-enabled yes/g' \
$REDIS_PATH/redis.conf > $REDIS_PATH/worker/redis02/redis.conf
```
+ 节点3
```
sed \
-e 's/^bind 127.0.0.1$/bind ::1/g' \
-e 's/^port 6379$/port 7003/g' \
-e 's/^daemonize no$/daemonize yes/g' \
-e 's/^# cluster-enabled yes$/cluster-enabled yes/g' \
$REDIS_PATH/redis.conf > $REDIS_PATH/worker/redis03/redis.conf
```
+ 节点4
```
sed \
-e 's/^bind 127.0.0.1$/bind ::1/g' \
-e 's/^port 6379$/port 7004/g' \
-e 's/^daemonize no$/daemonize yes/g' \
-e 's/^# cluster-enabled yes$/cluster-enabled yes/g' \
$REDIS_PATH/redis.conf > $REDIS_PATH/worker/redis04/redis.conf
```
+ 节点5
```
sed \
-e 's/^bind 127.0.0.1$/bind ::1/g' \
-e 's/^port 6379$/port 7005/g' \
-e 's/^daemonize no$/daemonize yes/g' \
-e 's/^# cluster-enabled yes$/cluster-enabled yes/g' \
$REDIS_PATH/redis.conf > $REDIS_PATH/worker/redis05/redis.conf
```
+ 节点6
```
sed \
-e 's/^bind 127.0.0.1$/bind ::1/g' \
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


# 集群

## Rbuy

下载
```
wget https://cache.ruby-lang.org/pub/ruby/2.6/ruby-2.6.1.tar.gz
```
解压
```
tar -zxvf ruby-2.6.1.tar.gz
```
进入Ruby安装目录
```
cd ruby-2.6.1
```
安装
```
./configure --prefix=$RUBY_PATH
make
make install
```
```
添加到系统环境变量 
```
echo PATH=\$PATH:$RUBY_PATH/bin >> /etc/profile
```
刷新环境变量 
```
source /etc/profile
```

## RubyGems

下载
```
wget https://rubygems.org/rubygems/rubygems-3.0.3.tgz
```
解压
```
tar -zxvf rubygems-3.0.3.tgz
```

## 集群

```
redis-cli --cluster create --cluster-replicas 1 $HOST_REDIS_1:7001 $HOST_REDIS_1:7002 $HOST_REDIS_1:7003
```
