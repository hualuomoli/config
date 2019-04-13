# Elastic Search

#  安装依赖 

+ Elastic Search 依赖[JDK](https://www.oracle.com/technetwork/java/javase/downloads/index.html)

# 配置环境变量

新建用户(elasticsearch 不支持root启动)
```
useradd elasticsearch
passwd elasticsearch
```

安装目录 
```
export TOOLS_PATH=/home/elasticsearch
export ELASTICSEARCH_PATH=/home/elasticsearch/elasticsearch-7.0.0-linux-x86_64
```

# 安装Elastic Search

切换elasticsearch用户
```
su elasticsearch
cd ~
```

下载 
```
wget https://artifacts.elastic.co/downloads/elasticsearch/elasticsearch-7.0.0-linux-x86_64.tar.gz
```
解压 
```
tar -zxvf elasticsearch-7.0.0-linux-x86_64.tar.gz
```
添加到系统环境变量 
```
echo  >> ~/.bash_profile
echo '# Elastic Search' >> ~/.bash_profile
echo PATH=\$PATH:$ELASTICSEARCH_PATH/bin >> ~/.bash_profile
```
刷新环境变量 
```
source ~/.bash_profile
```

# 关闭防火墙
```
systemctl stop firewalld
```

# 配置Elastic Search

1、修改`vi $ELASTICSEARCH_PATH/config/elasticsearch.yml`
```
network.host: you.host.name
http.port: 9200
```

以下需要root权限 <br />
2、max file descriptors
```
# vi /etc/security/limits.conf
elasticsearch soft nofile 65536
elasticsearch hard nofile 65536
```

3、max number of threads
```
# vi /etc/security/limits.d/20-nproc.conf
*          hard    nproc     4096
*          soft    nproc     4096
```

4、max virtual memory areas vm.max_map_count
```
# vi /etc/sysctl.conf 
vm.max_map_count=655360
```

5、重启服务器
```
shutdown -r now
```

# 启动
```
$ELASTICSEARCH_PATH/bin/elasticsearch
```

# 查询Elastic Search状态
```
ps -ef | grep elasticsearch
```

# 杀死进程
```
ps aux | grep elasticsearch| grep -v grep | awk '{print $2}' | xargs kill -9
```

# 错误
```
[1]: max file descriptors [4096] for elasticsearch process is too low, increase to at least [65536]
[2]: max number of threads [3796] for user [es] is too low, increase to at least [4096]
[3]: max virtual memory areas vm.max_map_count [65530] is too low, increase to at least [262144]
```
