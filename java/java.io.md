#JAVA IO 知识总结
##首先看一下JAVA都有哪些类
![JAVA IO类](http://www.dxiaoran.com/image/java-io.png)

1.字节流转化为字符流

```
InputStreamReader inputStreamReader =  new InputStreamReader(new InputStream());

OutputStreamWriter outputStreamWriter = new OutputStreamWriter(new OutputStream());
```
##Guava有哪些关于IO的方法
1.Guava操作文件的类

`com.google.common.io.Files`

方法有：

```
copy(File from, File to);
copy(File form,OutputStream to);
readLines(File file, Charset charset)

```
2.Guava包中与流有关的类

```
ByteStreams ,ByteSource,ByteSink;
CharStream,CharSource,CharSink;
```
##Buffer
怎么写这个buffer呢，我自己也没有弄明白了呢！