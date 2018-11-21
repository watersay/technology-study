## sendfile
指定是否使用sendfile系统调用来传输文件
sendfile系统调用在两个文件描述符之间直接传递数据(完全在内核中操作)，从而避免了数据在内核缓冲区和用户缓冲区之间的拷贝，操作效率很高，被称之为零拷贝

## 配置说明
语法： sendfile on | off;
默认值： sendfile off;
上下文： http，server，location，if in location

## 原理解释
```
read/write
在传统的文件传输方式（read、write/send方式），具体流程细节如下：

调用read函数，文件数据拷贝到内核缓冲区
read函数返回，数据从内核缓冲区拷贝到用户缓冲区
调用write/send函数，将数据从用户缓冲区拷贝到内核socket缓冲区
数据从内核socket缓冲区拷贝到协议引擎中
在这个过程当中，文件数据实际上是经过了四次拷贝操作：
硬盘—>内核缓冲区—>用户缓冲区—>内核socket缓冲区—>协议引擎

sendfile
sendfile系统调用则提供了一种减少拷贝次数，提升文件传输性能的方法。

sendfile系统调用利用DMA引擎将文件数据拷贝到内核缓冲区，之后数据被拷贝到内核socket缓冲区中
DMA引擎将数据从内核socket缓冲区拷贝到协议引擎中
这里没有用户态和内核态之间的切换，也没有内核缓冲区和用户缓冲区之间的拷贝，大大提升了传输性能。
这个过程数据经历的拷贝操作如下：
硬盘—>内核缓冲区—>内核socket缓冲区—>协议引擎
```