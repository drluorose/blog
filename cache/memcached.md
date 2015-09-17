#Memcached相关知识
##Memcached安装
download file

yum install libevent-devel
./configure --prefix=/you_dir

./make 

./make install
##Memcached HA问题
Memcached本身是没有做集群或者高可用的。Memcached是通过代理软件来实现。
###Memcached代理软件Magent的安装
修改Makefile

ARCH := $(shell uname -m)
X64 = x86_64
CC = gcc
PROGS =	magent
ifeq ($(ARCH), $(X64))
	M64 = -m64
	LIBS = /usr/lib64/libevent.a /usr/lib64/libm.so
else
	LIBS = -levent -lm -lm -lm -lm -lm -L/usr/local/lib
endif

CFLAGS = -lrt -Wall -g -O2 -I/usr/local/include $(M64)

all: $(PROGS)

STPROG = magent.o ketama.o

ketama.o: ketama.c ketama.h
	$(CC) $(CFLAGS) -c -o $@ ketama.c

magent.o: magent.c ketama.h
	$(CC) $(CFLAGS) -c -o $@ magent.c

magent: $(STPROG)
	$(CC) $(CFLAGS) -o $@ $^ $(LIBS)

clean:
	rm -f *.o *~ $(PROGS)
	
修改ketama.h
添加声明

```
#ifndef SSIZE_MAX
#define SSIZE_MAX 32767
#endif
```
###Magent是如何做到HA的
magent

```
  -h this message
  -u uid
  -g gid
  -p port, default is 11211. (0 to disable tcp support)
  -s ip:port, set memcached server ip and port
  -b ip:port, set backup memcached server ip and port
  -l ip, local bind ip address, default is 0.0.0.0
  -n number, set max connections, default is 4096
  -D do not go to background
  -k use ketama key allocation algorithm
  -f file, unix socket path to listen on. default is off
  -i number, max keep alive connections for one memcached server, default is 20
  -v verbose
```

```
magent -u root -n 51200 -l 127.0.0.1 -p 12000 -s 127.0.0.1:11211 -s 127.0.0.1:11212   
-b 127.0.0.1:11213  
```

magent通过哈希算法将数据写到server和back中，写到server和back的方法是一样的。

当server有机器down掉的时候，原来从server中get数据的请求直接从back中拿数据。

在APPClient中可以写多个magent的ip，magent代理多个Memcached这样不会在magent端形成单点。

对于AppClient端而言，连接magent和连接Memcached的方式是一样的。

##关于Memcached的java客户端
Memcached的java客户端已经存在三种了：  
   
官方提供的基于传统阻塞io由Greg Whalin维护的客户端  
   
Dustin Sallings实现的基于java nio Spymemcached  
   
XMemcached  
###1. 三种API比较  
####memcached client for java  
   
较早推出的memcached JAVA客户端API，应用广泛，运行比较稳定。   
  
  
####spymemcached  
   
A simple, asynchronous, single-threaded memcached client written in java. 支持异步，单线程的memcached客户端，用到了java1.5版本的concurrent和nio，存取速度会高于前者，但是稳定性不好，测试中常 报timeOut等相关异常。   
  
  
####xmemcached  
   
XMemcached同样是基于java nio的客户端，java nio相比于传统阻塞io模型来说，有效率高（特别在高并发下）和资源耗费相对较少的优点。传统阻塞IO为了提高效率，需要创建一定数量的连接形成连接 池，而nio仅需要一个连接即可（当然,nio也是可以做池化处理），相对来说减少了线程创建和切换的开销，这一点在高并发下特别明显。因此 XMemcached与Spymemcached在性能都非常优秀，在某些方面（存储的数据比较小的情况下）Xmemcached比 Spymemcached的表现更为优秀，具体可以看这个Java Memcached Clients Benchmark。  
   
  
###建议  
   
由于memcached client for java发布了新版本，性能上有所提高，并且运行稳定，所以建议使用memcached client for java。  
   
XMemcached也使用得比较广泛，而且有较详细的中文API文档，具有如下特点：高性 能、支持完整的协议、支持客户端分布、允许设置节点权重、动态增删节点、支持JMX、与Spring框架和Hibernate-memcached的集 成、客户端连接池、可扩展性好等。 





