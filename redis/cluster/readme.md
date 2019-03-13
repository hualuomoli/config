# Redis集群

# 前置必读

安装依赖 
```
yum install -y gcc-c++
```
安装目录 
```
/opt/redis-5.0.3
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
make install PREFIX=/opt/redis-5.0.3
```
添加到系统环境变量 
```
echo PATH=\$PATH:/opt/redis-5.0.3/bin >> /etc/profile
```
刷新环境变量 
```
source /etc/profile
```
复制默认配置文件 
```
cp ./redis.conf /opt/redis-5.0.3/redis.conf
```

# 关闭防火墙
```
systemctl stop firewalld
```


# 配置工作节点
测试使用3台机器，每台机器配置6个节点

修改配置文件为集群模式 
```
sed -i 's/# cluster-enabled yes/cluster-enabled yes/g' /opt/redis-5.0.3/redis.conf
```
创建工作目录          
```
mkdir -p /opt/redis-5.0.3/worker
```
节点1 
```
mkdir /opt/redis-5.0.3/worker/redis01 \
&& cp /opt/redis-5.0.3/redis.conf /opt/redis-5.0.3/worker/redis01/redis.conf \
&& sed -i 's/6379/7001/g' /opt/redis-5.0.3/worker/redis01/redis.conf
```
节点2 
```
mkdir /opt/redis-5.0.3/worker/redis02 \
&& cp /opt/redis-5.0.3/redis.conf /opt/redis-5.0.3/worker/redis02/redis.conf \
&& sed -i 's/6379/7002/g' /opt/redis-5.0.3/worker/redis02/redis.conf
```
节点3 
```
mkdir /opt/redis-5.0.3/worker/redis03 \
&& cp /opt/redis-5.0.3/redis.conf /opt/redis-5.0.3/worker/redis03/redis.conf \
&& sed -i 's/6379/7003/g' /opt/redis-5.0.3/worker/redis03/redis.conf
```
节点4 
```
mkdir /opt/redis-5.0.3/worker/redis04 \
&& cp /opt/redis-5.0.3/redis.conf /opt/redis-5.0.3/worker/redis04/redis.conf \
&& sed -i 's/6379/7004/g' /opt/redis-5.0.3/worker/redis04/redis.conf
```
节点5 
```
mkdir /opt/redis-5.0.3/worker/redis05 \
&& cp /opt/redis-5.0.3/redis.conf /opt/redis-5.0.3/worker/redis05/redis.conf \
&& sed -i 's/6379/7005/g' /opt/redis-5.0.3/worker/redis05/redis.conf
```
节点6 
```
mkdir /opt/redis-5.0.3/worker/redis06 \
&& cp /opt/redis-5.0.3/redis.conf /opt/redis-5.0.3/worker/redis06/redis.conf \
&& sed -i 's/6379/7006/g' /opt/redis-5.0.3/worker/redis06/redis.conf
```
+ 其它机器同样配置


# 启动redis节点

启动节点1
```
cd /opt/redis-5.0.3/worker/redis01 \
&& redis-server ./redis.conf
```
启动节点2
```
cd /opt/redis-5.0.3/worker/redis02 \
&& redis-server ./redis.conf
```
启动节点3
```
cd /opt/redis-5.0.3/worker/redis03 \
&& redis-server ./redis.conf
```
启动节点4
```
cd /opt/redis-5.0.3/worker/redis04 \
&& redis-server ./redis.conf
```
启动节点5
```
cd /opt/redis-5.0.3/worker/redis05 \
&& redis-server ./redis.conf
```
启动节点6
```
cd /opt/redis-5.0.3/worker/redis06 \
&& redis-server ./redis.conf
```
其它机器同样启动


# 集群

## 修改hosts文件，映射域名与实际IP地址

添加集群机器1
```
echo 192.168.1.101 redis-cluster-1
```
添加集群机器2
```
echo 192.168.1.102 redis-cluster-2
```
添加集群机器3
```
echo 192.168.1.103 redis-cluster-3
```
