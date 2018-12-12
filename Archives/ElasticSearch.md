1. 下载

2. 修改配置

```yml
#
# Path to directory where to store the data (separate multiple locations by comma):
#
path.data: /home/alog/elk/date
#
# Path to log files:
#
path.logs: /home/alog/elk/logs

# Set the bind address to a specific IP (IPv4 or IPv6):
#
network.host: 10.111.24.202
:
```

虚拟内存

在linux系统上，elasticsearch默认使用hybrid mmapfs / niofs来存储索引文件，因此操作系统主要会通过ｍｍａｐ来限制存储的空间，因此如果存储空间满了，那么会抛出异常，我们可以使用如下命令来更改设置

>修改java启动参数
>
>sudo sysctl -w vm.max_map_count=262144

3. 启动
展示
> bin/elasticsearch 前端方式启动

后台启动

> bin/elasticsearch -d 守护进程方式启动




# Kibana ui展示
 1. 启动

 >$ bin/kibana      //前台（foreground）启动方式
 >
 >$ bin/kibana&     //守护进程启动方式

 2. 配置修改

```yml
server.port: 5601
server.host: 10.111.24.202
elasticsearch.url: "http://10.111.24.202:9200"
```


logstash


1. 新建配置文件

logstash-test.conf


```conf
input {
 tcp {
        host => "10.111.27.202"
        port => 8082
        mode => "server"
        ssl_enable => false
        codec => json {
            charset => "UTF-8"
        }
    }
}
output {
    elasticsearch {
        hosts => "10.111.27.202:9200"
        index => "xinyu-api-test"
    }
  stdout { }
}

```

 2. 启动


>bin/logstash -f logstash-test.conf
