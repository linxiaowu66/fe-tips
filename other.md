### 1、rocketMq

1.1 启动多个mq客户端的时候，注意consumer-group的名称设置问题

1.2 关于mq的幂等性问题

### 2、socket.io

2.1 集群部署的问题

### 3、测试工具

3.1、使用nc(Netcat)工具测试的时候，不要使用localhost这个当做host，因为nc会将localhost解析为ipv4和ipv6地址，除非你指定`-4`这个参数，这样的话如果你的服务器不支持ipv6，那么测试UDP的时候往往通不过，因为在IPV6的ICMP包的时候会返回Destination Unreachable的错误。而TCP因为会降级到IPV4，所以没有这种测试失败的问题。参考这个回答：[Trouble with netcat over UDP](https://superuser.com/questions/1238038/trouble-with-netcat-over-udp)
