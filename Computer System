计算机系统面试攻略
====

* #### 多线程如何同步
  答：windows --- 线程同步有四种方式：临界区、内核对象、互斥量、信号量。<br>
    &emsp;&emsp; Linux --- 线程同步有最常用的是：互斥锁、条件变量和信号量。

-----
* #### 进程间通讯的方式有哪些，各有什么优缺点
  答：Linux 进程间通信（IPC）以下以几部分发展而来：UNIX进程间通信、基于System V进程间通信、基于Socket进程间通信和POSIX进程间通信。<br>
  &emsp;&emsp;UNIX进程间通信方式包括：管道、FIFO、信号。<br>
&emsp;&emsp;SYSTEM V进程间通信方式包括：System V消息队列、System V信号灯、System V共享内存<br>
&emsp;&emsp;现在linux使用的进程间通信方式：<br>
（1）管道（pipe）和有名管道（FIFO）<br>
（2）信号（signal）<br>
（3）消息队列<br>
（4）共享内存<br>
（5）信号量<br>
（6）套接字（socket)<br>
 管道:<br>
&emsp;&emsp;优点是所有的UNIX实现都支持, 并且在最后一个访问管道的进程终止后,管道就被完全删除;<br>
&emsp;&emsp;缺陷是管道只允许单向传输或者用于父子进程之间  <br>
系统IPC:<br>
&emsp;&emsp;优点是功能强大,能在毫不相关进程之间进行通讯;<br>
&emsp;&emsp;缺陷是关键字KEY_T使用了内核标识,占用了内核资源,而且只能被显式删除,而且不能使用SOCKET的一些机制,例如select,epoll等.<br>
socket可以跨网络通讯，其他进程间通讯的方式都不可以，只能是本机进程通讯。<br>
-----
* ####
  答：
-----
* ####
  答：
-----
