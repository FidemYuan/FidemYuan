---
layout: post
title: redis RDB、AOF持久化过程
categories: JavaSE
tags: redis
author: fidemyuan
---

## RDB持久化

触发条件：<br>

1.客户端执行命令save和bgsave会生成快照；<br>
2.根据配置文件save m n规则进行自动快照；<br>
3.主从复制时，从库全量复制同步主库数据，此时主库会执行bgsave命令进行快照；<br>
4.客户端执行数据库清空命令FLUSHALL时候，触发快照；<br>
5.客户端执行shutdown关闭redis时，触发快照；<br>

### RDB持久化过程

![](https://github.com/fidemyuan/fidemyuan.github.io/blob/master/img-folder/RDB-persistence.png)

如上图:<br>

1.客户端执行bgsave命令，redis主进程收到指令并判断此时是否在执行bgrewriteaof(AOF文件重新过程，后续会讲解)，如果此时正好在执行则bgsave直接返回，不fork子进程，如果没有执行bgrewriteaof重写AOF文件，则进入下一个阶段；<br>
2.主进程调用fork方法创建子进程，在创建过程中redis主进程阻塞，所以不能响应客户端请求；<br>
3.子进程创建完成以后，bgsave命令返回“Background saving started”，此时标志着redis可以响应客户端请求了；<br>
4.子经常根据主进程的内存副本创建临时快照文件，当快照文件完成以后对原快照文件进行替换；<br>
5.子进程发送信号给redis主进程完成快照操作，主进程更新统计信息（info Persistence可查看）,子进程退出；<br>

##  AOF持久化

redisAOF持久化过程可分为以下阶段：<br>

1.追加写入<br>

　　redis将每一条写命令以redis通讯协议添加至缓冲区aof_buf,这样的好处在于在大量写请求情况下，采用缓冲区暂存一部分命令随后根据策略一次性写入磁盘，这样可以减少磁盘的I/O次数，提高性能。<br>

2.同步命令到硬盘<br>

　　当写命令写入aof_buf缓冲区后，redis会将缓冲区的命令写入到文件，redis提供了三种同步策略，由配置参数appendfsync决定，下面是每个策略所对应的含义：<br>

no：不使用fsync方法同步，而是交给操作系统write函数去执行同步操作，在linux操作系统中大约每30秒刷一次缓冲。这种情况下，缓冲区数据同步不可控，并且在大量的写操作下，aof_buf缓冲区会堆积会越来越严重，一旦redis出现故障，数据丢失严重。<br>
always：表示每次有写操作都调用fsync方法强制内核将数据写入到aof文件。这种情况下由于每次写命令都写到了文件中, 虽然数据比较安全，但是因为每次写操作都会同步到AOF文件中，所以在性能上会有影响，同时由于频繁的IO操作，硬盘的使用寿命会降低。<br>
everysec：数据将使用调用操作系统write写入文件，并使用fsync每秒一次从内核刷新到磁盘。 这是折中的方案，兼顾性能和数据安全，所以redis默认推荐使用该配置。<br>

3.文件重写(bgrewriteaof)<br>

　　当开启的AOF时，随着时间推移，AOF文件会越来越大,当然redis也对AOF文件进行了优化，即触发AOF文件重写条件（后续会说明）时候，redis将使用bgrewriteaof对AOF文件进行重写。这样的好处在于减少AOF文件大小，同时有利于数据的恢复。<br>

  为什么重写？比如先后执行了“set foo bar1 set foo bar2 set foo bar3” 此时AOF文件会记录三条命令，这显然不合理，因为文件中应只保留“set foo bar3”这个最后设置的值，前面的set命令都是多余的，下面是一些重写时候策略：<br>

 (1)重复或无效的命令不写入文件<br>
 (2)过期的数据不再写入文件<br>
 (3)多条命令合并写入（当多个命令能合并一条命令时候会对其优化合并作为一个命令写入，例如“RPUSH list1 a RPUSH list1 b" 合并为“RPUSH list1 a b” ）<br>


### AOF重写

**重写触发条件：**

1.手动触发：客户端执行bgrewriteaof命令。

2.自动触发：自动触发通过以下两个配置协作生效：

（1）auto-aof-rewrite-min-size: AOF文件最小重写大小，只有当AOF文件大小大于该值时候才可能重写,4.0默认配置64mb。
（2）auto-aof-rewrite-percentage：当前AOF文件大小和最后一次重写后的大小之间的比率等于或者等于指定的增长百分比，如100代表当前AOF文件是上次重写的两倍时候才重写。　


redis开启在AOF功能开启的情况下，会维持以下三个变量<br>

记录当前AOF文件大小的变量aof_current_size。<br>
记录最后一次AOF重写之后，AOF文件大小的变量aof_rewrite_base_size。<br>
增长百分比变量aof_rewrite_perc。<br>
每次当serverCron（服务器周期性操作函数）函数执行时，它会检查以下条件是否全部满足，如果全部满足的话，就触发自动的AOF重写操作：<br>

没有BGSAVE命令（RDB持久化）/AOF持久化在执行；<br>
没有BGREWRITEAOF在进行；<br>
当前AOF文件大小要大于server.aof_rewrite_min_size的值；<br>
当前AOF文件大小和最后一次重写后的大小之间的比率等于或者大于指定的增长百分比（auto-aof-rewrite-percentage参数）<br>


**重写过程：**

![](https://github.com/fidemyuan/fidemyuan.github.io/blob/master/img-folder/AOF-persistence.png)


　如上图　aof_rewrite_buf 代表重写缓冲区      aof_buf代表写写命令存放的缓冲区

　　1.开始bgrewriteaof，判断当前有没有bgsave命令(RDB持久化)/bgrewriteaof在执行，倘若有，则这些命令执行完成以后在执行。<br>

　　2.主进程fork出子进程，在这一个短暂的时间内，redis是阻塞的。<br>

　　3.主进程fork完子进程继续接受客户端请求，所有写命令依然写入AOF文件缓冲区并根据appendfsync策略同步到磁盘，保证原有AOF文件完整和正确。由于fork的子进程仅仅只共享主进程fork时的内存，因此Redis使用采用重写缓冲区(aof_rewrite_buf)机制保存fork之后的客户端的写请求，防止新AOF文件生成期间丢失这部分数据。此时，客户端的写请求不仅仅写入原来aof_buf缓冲，还写入重写缓冲区(aof_rewrite_buf)。<br>

　　4.子进程通过内存快照，按照命令重写策略写入到新的AOF文件。<br>

　　4.1子进程写完新的AOF文件后，向主进程发信号，父进程更新统计信息。<br>

　　4.2主进程把AOFaof_rewrite_buf中的数据写入到新的AOF文件(避免写文件是数据丢失)。<br>

　　5.使用新的AOF文件覆盖旧的AOF文件，标志AOF重写完成。<br>
