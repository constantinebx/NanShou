1、什么是Redis持久化？Redis有哪几种持久化方式？优缺点是什么？
持久化就是把内存的数据写到磁盘中去，防止服务宕机了内存数据丢失。  
Redis 提供了两种持久化方式:RDB（默认） 和AOF  
RDB：  
rdb是Redis DataBase缩写，功能核心函数rdbSave(生成RDB文件)和rdbLoad（从文件加载内存）两个函数
AOF:  
Aof是Append-only file缩写，每当执行服务器(定时)任务或者函数时flushAppendOnlyFile 函数都会被调用， 
这个函数执行以下两个工作，aof写入保存：  
WRITE：根据条件，将 aof_buf 中的缓存写入到 AOF 文件。  
SAVE：根据条件，调用 fsync 或 fdatasync 函数，将 AOF 文件保存到磁盘中。  
存储结构:内容是redis通讯协议(RESP )格式的命令文本存储。  
比较：  
1、aof文件比rdb更新频率高，优先使用aof还原数据。  
2、aof比rdb更安全也更大  
3、rdb性能比aof好  
4、如果两个都配了优先加载AOF  

2、解释下什么是RESP？有什么特点？  
RESP 是redis客户端和服务端之前使用的一种通讯协议；  
RESP 的特点：实现简单、快速解析、可读性好  

3、Redis 有哪些架构模式？讲讲各自的特点  
1）单机版  
特点：简单。  
问题：内存容量有限；处理能力有限；无法高可用。  
2）主从复制  
Redis 的复制（replication）功能允许用户根据一个 Redis 服务器来创建任意多个该服务器的复制品，其中被
复制的服务器为主服务器（master），而通过复制创建出来的服务器复制品则为从服务器（slave）。 只要主从服
务器之间的网络连接正常，主从服务器两者会具有相同的数据，主服务器就会一直将发生在自己身上的数据更新同步
给从服务器，从而一直保证主从服务器的数据相同。  
特点：
master/slave 角色；master/slave 数据相同；降低 master 读压力到从库。  
问题：
无法保证高可用；没有解决master的写压力。  
3）哨兵  
Redis sentinel 是一个分布式系统，可以监控 redis 主从服务器，并在主服务器下线时自动进行故障转移。其中三个特性：  
监控（Monitoring）：    Sentinel  会不断地检查你的主服务器和从服务器是否运作正常。  
提醒（Notification）： 当被监控的某个 Redis 服务器出现问题时， Sentinel 可以通过 API 向管理员或者其他应用程序发送通知。  
自动故障迁移（Automatic failover）： 当一个主服务器不能正常工作时， Sentinel 会开始一次自动故障迁移操作。  
特点：保证高可用；监控各个节点；自动故障迁移；主从切换需要时间，可能丢失数据；没有降低master的写压力只降低了读压力。  
4）集群（proxy型）
