#Redis
##Redis介绍
1.redis是一个带持久化的缓存数据
2.redis支持丰富的数据类型，String，hashes，list，sets，sorted sets
###关于Redis的持久化
1.redis提供一个数据快照，数据快照的原理是将整个Redis中存的所有数据遍历一遍存到一个扩展名为rdb的数据文件中。通过SAVE命令可以调用这个过程。

2.Append-Only File(追加式的操作日志记录）
Redis还支持一种追加式的操作日志记录，叫append only file，其日志文件以aof结局，我们一般各为aof文件。要开启aof日志的记录，你需要在配置文件中进行如下设置：
	appendonly yes
###关于Redis的数据散列
Partitioning can be responsibility of different parts of a software stack.
####Client side partitioning
客户端分片指的是在客户端知道向哪个node去写数据和读数据.
####Proxy assisted partitioning
代理Server的分片,客户端只是向Proxy-Server发起Redis请求,然后Proxy进行请求转发,Proxy实现了数据分片的业务.
####Query routing
查询路由指你可以发送你的请求到任何一个实例,由实例来转发请求到对应的机器上面去,Redis Cluster实现了hybird form 查询路由.


现在Redis-Cluster还是在bate阶段,然后还没有知名的公司在用,可以使用tomeproxy来实现redis的集群.
###基于Twemproxy的Redis集群方案
概述
由于单台redis服务器的内存管理能力有限，使用过大内存redis服务器的性能急剧下降，且服务器发生故障将直接影响大面积业务。为了获取更好的缓存性能及扩展型，我们将需要搭建redis集群来满足需求。因redis 3.0 beta支持的集群功能不适合生产环境的使用，所以我们采用twitter正在使用的twemproxy来搭建redis缓存服务器集群，目前用户包括Pinterest、Tumblr、Twitter、Vine、Kiip、Wuaki.tv、Wanelo、Kontera、Wikimedia、Bright、56.com、Snapchat、Digg、Gawkermedia、3scale.net等。

Twemproxy是memcached和redis协议的代理服务器，并能有效减少大量连接对redis服务器的性能影响;



