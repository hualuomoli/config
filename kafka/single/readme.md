# Kafka单点

#  安装依赖 

+ kafka 依赖[Zookeeper](https://github.com/hualuomoli/config/tree/zookeeper/zookeeper/single)

# 配置环境变量

安装目录 
```
export TOOLS_PATH=/opt
export KAFKA_PATH=/opt/kafka_2.12-2.2.0
```

# 安装Kafka

下载 
```
wget http://mirrors.tuna.tsinghua.edu.cn/apache/kafka/2.2.0/kafka_2.12-2.2.0.tgz 
```
解压 
```
tar -zxvf kafka_2.12-2.2.0.tgz -C $TOOLS_PATH
```
添加到系统环境变量 
```
echo  >> /etc/profile
echo '# Kafka' >> /etc/profile
echo PATH=\$PATH:$KAFKA_PATH/bin >> /etc/profile
```
刷新环境变量 
```
source /etc/profile
```

# 关闭防火墙
```
systemctl stop firewalld
```

# 启动Kafka

```
kafka-server-start.sh $KAFKA_PATH/config/server.properties
```

# 查询Zookeeper状态
```
ps -ef | grep kafka
```
# 杀死进程
```
ps aux | grep kafka| grep -v grep | awk '{print $2}' | xargs kill -9
```
