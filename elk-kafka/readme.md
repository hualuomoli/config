# 准备

## hosts

+ 为了方便配置，使用域名替代服务器IP。本次测试使用IP为`192.168.1.226`
+ 修改服务器`hosts`配置文件 `vi /etc/hosts`
```
127.0.0.1         localhost localhost.localdomain localhost4 localhost4.localdomain4
::1               localhost localhost.localdomain localhost6 localhost6.localdomain6
192.168.1.226     elk.com
```

## Elastic Search

### 创建用户
+ `elasticsearch`不能以`root`用户启动
+ 创建`elasticsearch`用户
```
useradd elasticsearch
```
+ 设置`elasticsearch`密码
```
passwd elasticsearch
```

### 配置
+ 设置`max file descriptors`
```
# vi /etc/security/limits.conf
elasticsearch soft nofile 65536
elasticsearch hard nofile 65536
```
+ 设置`max number of threads`
```
# vi /etc/security/limits.d/20-nproc.conf
*          hard    nproc     4096
*          soft    nproc     4096
```
+ 设置`max virtual memory areas vm.max_map_count`
```
# vi /etc/sysctl.conf 
vm.max_map_count=655360
```

## 防火墙

由于时局域网测试,本实例关闭防火墙
```
systemctl stop firewalld
systemctl disable firewalld
```

## 重启服务器
```
# reboot
shutdown -r now
```

# JDK

+ [下载](http://download.oracle.com/otn/java/jdk/8u192-b12/750e1c8617c5452694857ad95c3ee230/jdk-8u192-linux-x64.tar.gz) [更多](https://www.oracle.com/technetwork/java/javase/downloads/index.html)
+ 解压到`/opt`目录 `tar -zxvf jdk-8u192-linux-x64.tar.gz -C /opt`
+ 配置到环境变量
```
echo >> /etc/profile
echo '# JDK' >> /etc/profile
echo 'JAVA_HOME=/opt/jdk1.8.0_192' >> /etc/profile
echo 'PATH=$PATH:$JAVA_HOME/bin' >> /etc/profile
```
+ 刷新环境变量 `source /etc/profile`
+ 查看是否配置成功 `java -version`
```
java version "1.8.0_192"
Java(TM) SE Runtime Environment (build 1.8.0_192-b12)
Java HotSpot(TM) 64-Bit Server VM (build 25.192-b12, mixed mode)
```

# Zookeeper

+ [下载](https://mirrors.tuna.tsinghua.edu.cn/apache/zookeeper/stable/zookeeper-3.4.14.tar.gz) [更多](https://www.apache.org/dyn/closer.cgi/zookeeper/)
+ 解压到`/opt`目录 `tar -zxvf zookeeper-3.4.14.tar.gz -C /opt`
+ 设置`zookeeper`配置信息
```
cp /opt/zookeeper-3.4.14/conf/zoo_sample.cfg /opt/zookeeper-3.4.14/conf/zoo.cfg
```
+ 启动`zookeeper`
```
sh /opt/zookeeper-3.4.14/bin/zkServer.sh start
```
+ 查看`zookeeper`状态 `netstat -anp|grep 2181`
```
tcp6       0      0 :::2181                 :::*                    LISTEN      1412/java 
```

# Kafka

+ [下载](http://mirrors.tuna.tsinghua.edu.cn/apache/kafka/2.2.0/kafka_2.12-2.2.0.tgz) 更多(http://kafka.apache.org/downloads)
+ 解压到`/opt`目录 `tar -zxvf kafka_2.12-2.2.0.tgz -C /opt`
+ 设置`kafka`配置文件
```
# vi /opt/kafka_2.12-2.2.0/config/server.properties
listeners=PLAINTEXT://elk.com:9092

```
+ 启动`kafka`
```
/opt/kafka_2.12-2.2.0/bin/kafka-server-start.sh -daemon /opt/kafka_2.12-2.2.0/config/server.properties
```
+ 查看`kafka`状态 `netstat -anp|grep 9092`
```
tcp6       0      0 192.168.1.226:9092      :::*                    LISTEN      1797/java           
tcp6       0      0 192.168.1.226:35848     192.168.1.226:9092      ESTABLISHED 1797/java           
tcp6       0      0 192.168.1.226:9092      192.168.1.226:35848     ESTABLISHED 1797/java
```
+ [测试](http://kafka.apache.org/quickstart)`kafka`通信状态
```
# 将实例中的`localhost`修改为`elk.com`
```

# Elastic Search

注: 需要使用`elasticsearch`普通用户操作 <br />
```
su elasticsearch
```

+ [下载](https://artifacts.elastic.co/downloads/elasticsearch/elasticsearch-6.6.1.tar.gz) [更多](https://www.elastic.co/cn/downloads/elasticsearch)
+ 解压到`elasticsearch`用户目录 `tar -zxvf elasticsearch-6.6.1.tar.gz -C /home/elasticsearch`
+ 配置`elasticsearch`配置文件
```
# vi /home/elasticsearch/elasticsearch-6.6.1/config/elasticsearch.yml
network.host: elk.com
http.port: 9200
```
+ 启动`elasticsearch`
```
/home/elasticsearch/elasticsearch-6.6.1/bin/elasticsearch -d
```
+ 查看`elasticsearch`状态 `netstat -anp|grep 9200` (需切换为`root`用户)
```
tcp6       0      0 192.168.1.226:9200      :::*                    LISTEN      1971/java 
```

# Logstash

+ [下载](https://artifacts.elastic.co/downloads/logstash/logstash-6.6.1.tar.gz) [更多](https://www.elastic.co/cn/downloads/logstash)
+ 解压到`/opt`目录 `tar -zxvf logstash-6.6.1.tar.gz -C /opt`
+ 配置`logstash`
```
# kafka的topic为kafka-logback
# log信息如 172.21.96.1 2019-04-19 19:44:44.806 [main] INFO s.kafka.logback.KafkaLogbackTest - send info to kafka
# 根据自己的需要配置filter -> grok -> match
# vi /opt/logstash-6.6.1/config/logstash-demo.conf
input {
    kafka {
        topics => ["kafka-logback"]
        bootstrap_servers => "elk.com:9092"
        consumer_threads => 5
        decorate_events => true
    }
}
filter {
    grok {
        pattern_definitions => {"CALLER_TIME" => "[0-9]{4}-[0-9]{2}-[0-9]{2} [0-9]{2}:[0-9]{2}:[0-9]{2}(\.[0-9]{1,4})?"}
        match => {"message" => "%{IP:ip} %{CALLER_TIME:callerTime} \[%{WORD:thread}\] %{WORD:level} %{JAVACLASS:class} - %{GREEDYDATA:message}"}
    }
}
output {  
    elasticsearch {  
        hosts => "elk.com"
        index => "logback"
    } 
}
```
+ 启动测试配置
```
nohup /opt/logstash-6.6.1/bin/logstash -f /opt/logstash-6.6.1/config/logstash-demo.conf &
```
+ 查看`logstash`状态 `netstat -anp|grep logstash`

# Kibana

+ [下载](https://artifacts.elastic.co/downloads/kibana/kibana-6.6.1-linux-x86_64.tar.gz) [更多](https://www.elastic.co/cn/downloads/kibana)
+ 解压到`/opt`目录 `tar -zxvf kibana-6.6.1-linux-x86_64.tar.gz -C /opt`
+ 配置`kibana`
```
# vi /opt/kibana-6.6.1-linux-x86_64/config/kibana.yml
server.host: "elk.com"
server.port: 5601
elasticsearch.hosts: ["http://elk.com:9200"]
```
+ 启动`kibana` 
```
nohup /opt/kibana-6.6.1-linux-x86_64/bin/kibana &
```
+ 查看`kibana`状态 `netstat -anp|grep 5601`
```
tcp        0      0 192.168.1.226:5601      0.0.0.0:*               LISTEN      2118/node 
```