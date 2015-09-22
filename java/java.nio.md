#JAVA 网络编程模型（主要说NIO）
##NIO
java nio 是Selector基于epoll实现的IO多路复用模型。JDK1.7之后增加了套接字通道。

##多路复用器Selector

多路复用I/O技术是通过Selector轮询注册在上面的Channel实现I/O操作。
![JAVA NIO Compare](http://www.dxiaoran.com/image/io.compare.png)