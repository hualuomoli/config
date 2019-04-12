# Zookeeper单点

#  安装依赖 
```
zookeeper 依赖[JDK](https://www.oracle.com/technetwork/java/javase/downloads/index.html)
```

# 配置环境变量

安装目录 
```
export TOOLS_PATH=/opt
export ZOOKEEPER_PATH=/opt/zookeeper-3.4.14
```

# 安装Zookeeper

下载 
```
wget https://mirrors.tuna.tsinghua.edu.cn/apache/zookeeper/stable/zookeeper-3.4.14.tar.gz
```
解压 
```
tar -zxvf zookeeper-3.4.14.tar.gz -C $TOOLS_PATH
```
添加到系统环境变量 
```
echo  >> /etc/profile
echo '# Zookeeper' >> /etc/profile
echo PATH=\$PATH:$ZOOKEEPER_PATH/bin >> /etc/profile
```
刷新环境变量 
```
source /etc/profile
```

# 关闭防火墙
```
systemctl stop firewalld
```

# 启动Zookeeper

```
zkServer.sh start $ZOOKEEPER_PATH/conf/zoo_sample.cfg
```


# 查询Zookeeper状态
```
ps -ef | grep zookeeper
```
# 杀死进程
```
ps aux | grep zookeeper| grep -v grep | awk '{print $2}' | xargs kill -9
```
