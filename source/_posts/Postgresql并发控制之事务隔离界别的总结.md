---
title: Postgresql事务隔离界别的总结
date: 2019-05-04 08:30:40
tags:
---
SQL标准定义了四种隔离级别，但在Postgresql中只有三个级别，所以所选的隔离级别比
SQL标准或者Mysql高一个级别。

### Read committed

在其中的select语句可以看到语句进行之前已经commit的事务的结果
也就是说在一个transaction中 select 可能会获得两个不同的结果，
但是select可以看到在这个事务中已经update的结果只是不能看到其他事务未提交之前的结果。
在官方文档上有一个有趣的例子。两个网页的点击量是9和10，现在有这样的两个事务
```
BEGIN;
UPDATE website SET hits = hits + 1;
-- run from another session:  DELETE FROM website WHERE hits = 10;
COMMIT;
```
执行这两个事务后之前点击量9和10的网页都没有删除。
原因是因为在更新之前9不等于10所以被skip掉，然后这个事物想删除更新之前是10的网页，
等待update释放锁，当update事务完成后 Read committed 级别有一个nonrepeatable read（在读已提交模式中，每个命令都是从一个新的快照开始），
发现现在的值为11，所以不满足条件，所以两条数据都没有被删除。

### Repeatable 和 Serializable
Repeatable 和 Serializable 的级别是最高的他们的区别在于幻读。
事实上，这个级别完全像可重复读一样地工作，除了它会监视一些条件，
这些条件可能导致一个可序列化事务的并发集合的执行产生的行为与这些事务所有可能的序列化
（一次一个）执行不一致。
```
class | value
-------+-------
     1 |    10
     1 |    20
     2 |   100
     2 |   200
```
第一个事务
```
SELECT SUM(value) FROM mytab WHERE class = 1;
```
然后将结果以class = 2 插入表

第二个并发事务
```
SELECT SUM(value) FROM mytab WHERE class = 2;
```
如果是 Repeatable级别那么所得到的结果是300，而且两个事务都能提交，之后如果查询class =2 的 和得到的是330。
姨？不是300吗？这就是 Repeatable欢度的结果，而如果 是Serializable 级别的话
```
ERROR:  could not serialize access due to read/write dependencies among transactions
```
一个事务能提交，而另一个事务会出现这个错误

* 注意：在Postgresql中默认的级别是Read Committed 而 Mysql中默认的级别是Repeatable Read 级别


### 总结
在psql中Read Committed 可以应付大多数情况，而repeableread 和 serialiable
需要 操作由徐立华冲突带来的错误进行再一次的提交。注意的一点是
只有更新事务可能需要被重试；只读事务将永远不会有序列化冲突。
