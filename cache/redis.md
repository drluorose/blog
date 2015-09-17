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
Client side partitioning means that the clients directly select the right node where to write or read a given key. Many Redis clients implement client side partitioning.
####Proxy assisted partitioning
Proxy assisted partitioning means that our clients send requests to a proxy that is able to speak the Redis protocol, instead of sending requests directly to the right Redis instance. The proxy will make sure to forward our request to the right Redis instance accordingly to the configured partitioning schema, and will send the replies back to the client. The Redis and Memcached proxy Twemproxy implements proxy assisted partitioning.
####Query routing
Query routing means that you can send your query to a random instance, and the instance will make sure to forward your query to the right node. Redis Cluster implements an hybrid form of query routing, with the help of the client (the request is not directly forwarded from a Redis instance to another, but the client gets redirected to the right node).