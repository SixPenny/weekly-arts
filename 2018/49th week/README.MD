## Algorightm 

### Description

Given a list of 24-hour clock time points in "Hour:Minutes" format, find the minimum minutes difference between any two time points in the list.  
Example 1:
```
Input: ["23:59","00:00"]
Output: 1
```
Note:  
The number of time points in the given list is at least 2 and won't exceed 20000.  
The input time is legal and ranges from 00:00 to 23:59.

### Solution

```java
public class FindMiniDifference {
    public int findMinDifference(List<String> timePoints) {
        List<Integer> time = new ArrayList<>(timePoints.size() * 2);

        for (String s : timePoints) {
            String[] minuteSeconds = s.split(":");
            time.add(Integer.valueOf(minuteSeconds[0]) * 60 + Integer.valueOf(minuteSeconds[1]));
        }
        time.sort(Integer::compareTo);
        int miniDifference = mini(time.get(time.size() - 1) - time.get(0));
        for (int i = 0; i < time.size() - 1; i++) {
            int diff = mini(time.get(i + 1) - time.get(i));
            if (diff < miniDifference) {
                miniDifference = diff;
            }
        }

        return miniDifference;
    }

    private int mini(int t) {
        int rank = 24 * 60;
        return Math.min(t, rank - t);
    }

    @Test
    public void test() {
        Assert.assertEquals(1, findMinDifference(Arrays.asList("23:59", "00:00")));
        Assert.assertEquals(1, findMinDifference(Arrays.asList("00:02", "23:59", "00:00")));
    }
}
```

此处使用了排序，是很耗费性能的，网友的[解答](https://leetcode.com/problems/minimum-time-difference/discuss/100640/Verbose-Java-Solution-Bucket)非常巧妙, 条件自己理解的还不够深刻啊.

## Review

[Excellent Code = Clean and Beautiful Code](https://medium.com/swlh/excellent-code-clean-and-beautiful-code-b541ca4b5a39)

- Always remember, the name of any variable, function or class should answer three big questions; why it exists, what it does and what it is used.
- Functions are the verbs of that language and classes are the nouns.


1. Why we need to write clean code?
Readablity. Maintainablity. Bug-free. 
2. What is clean code? 
As this article described, clean code has a lot of aspects. 
3. Hot to write clean code ?
Learn and practice.
4. About comments.
Comments are a good tool for non-programmers. We can't say comments are bad in a simple way. We need to say it in a context.

## Tips

MySQL Locking 

### Abstract

看到孤独烟回答粉丝的一个[问题](https://www.itcodemonkey.com/article/11725.html), 就想要研究一下MySQL 锁的情况。下面是对[MySQL manual locking](https://dev.mysql.com/doc/refman/5.5/en/innodb-locking.html)的一个笔记。


### Share & Exclusive Lock

- A shared (S) lock permits the transaction that holds the lock to read a row.

- An exclusive (X) lock permits the transaction that holds the lock to update or delete a row.

### Intention Lock
- Intention locks are **table-level** locks that indicate which type of lock (shared or exclusive) a transaction requires **later** for a row in a table. 
- `SELECT ... LOCK IN SHARE MODE` sets an IS lock, and `SELECT ... FOR UPDATE` sets an IX lock.(after version 8.0, share mode used this way `select ... for share`, consistent with update)


| | X | S | IX | IS |  
|--- |---| ---|--- | --- |
| X | Conflict | Conflict | Conflict | Conflict | 
| S | Conflict | Compatible | Conflict | Compatible |
| IX |Conflict | Conflict |  Compatible | Compatible |
| IS |Conflict | Compatible | Compatible | Compatible|


The intention locking protocol is as follows:
- Before a transaction can acquire a shared lock on a row in a table, it must first acquire an IS lock or stronger on the table.

- Before a transaction can acquire an exclusive lock on a row in a table, it must first acquire an IX lock on the table.


### Record Lock

- A record lock is a lock on an index record. 
- Record locks **always** lock index records, even if a table is defined with no indexes. For such cases, InnoDB **creates a hidden clustered index and uses this index** for record locking

Example:
```sql
CREATE TABLE `tt` (
  `a` int(11) NOT NULL DEFAULT '0',
  `b` varchar(10) COLLATE utf8mb4_bin DEFAULT NULL,
  `c` varchar(10) COLLATE utf8mb4_bin DEFAULT NULL,
  PRIMARY KEY (`a`)
) ENGINE=InnoDB DEFAULT CHARSET=utf8mb4 COLLATE=utf8mb4_bin;

insert into tt values (1, 'aaa', 'ccc');
insert into tt values (2, 'bbb', 'ccc');
insert into tt values (3, 'ccc', 'ccc');


start transaction;
select * from tt where a = 1 for update;

show engine innodb status;
```

可以看到如下输出,在 table 上有一个 IX 锁，在行上有一个 X 的 record lock. 
```
2 lock struct(s), heap size 376, 1 row lock(s)
MySQL thread id 127707691, OS thread handle 0x7efbeb65d700, query id 4117992277 10.16.163.157 mams_test
TABLE LOCK table `test1`.`tt` trx id A06213D6 lock mode IX
RECORD LOCKS space id 6784 page no 3 n bits 72 index `PRIMARY` of table `test1`.`tt` trx id A06213D6 lock_mode X locks rec but not gap
Record lock, heap no 6 PHYSICAL RECORD: n_fields 5; compact format; info bits 0
 0: len 4; hex 80000001; asc     ;;
 1: len 6; hex 0000a0600c90; asc    `  ;;
 2: len 7; hex 780000c61b1e0f; asc x      ;;
 3: len 3; hex 646464; asc ddd;;
 4: SQL NULL;
```

ps: 执行`show engine innodb status`之前需要先将lock 监控打开，需要创建两张表，具体信息可见[Enabling InnoDB Monitors](https://dev.mysql.com/doc/refman/5.5/en/innodb-enabling-monitors.html)
```
CREATE TABLE innodb_monitor (a INT) ENGINE=INNODB;
CREATE TABLE innodb_lock_monitor (a INT) ENGINE=INNODB;
```

无index Example:
```
create table ttp (a int, b varchar(10));
insert into ttp values (1, 'cc');
insert into ttp values (1, 'dd');

start transaction;
select * from ttp where b = 'dd' for update;

show engine innodb status;
```

输出如下，可以看到MySQL自动生成了一个[clustered index](https://dev.mysql.com/doc/refman/5.5/en/innodb-index-types.html),而且由于没有索引，这次将表里的两条数据全部加了 X 锁：
```
TABLE LOCK table `test1`.`ttp` trx id A0622BA2 lock mode IX
RECORD LOCKS space id 6789 page no 3 n bits 72 index `GEN_CLUST_INDEX` of table `test1`.`ttp` trx id A0622BA2 lock_mode X
Record lock, heap no 1 PHYSICAL RECORD: n_fields 1; compact format; info bits 0
 0: len 8; hex 73757072656d756d; asc supremum;;

Record lock, heap no 2 PHYSICAL RECORD: n_fields 5; compact format; info bits 0
 0: len 6; hex 000001ae524d; asc     RM;;
 1: len 6; hex 0000a0622b13; asc    b+ ;;
 2: len 7; hex e1000014d20110; asc        ;;
 3: len 4; hex 80000001; asc     ;;
 4: len 2; hex 6363; asc cc;;

Record lock, heap no 3 PHYSICAL RECORD: n_fields 5; compact format; info bits 0
 0: len 6; hex 000001ae524e; asc     RN;;
 1: len 6; hex 0000a0622b2b; asc    b++;;
 2: len 7; hex f9000084630110; asc     c  ;;
 3: len 4; hex 80000001; asc     ;;
 4: len 2; hex 6464; asc dd;;
```


### Gap Lock

- A gap lock is a lock on a gap between index records, or a lock on the gap before the first or after the last index record.
- Used in `REPEATABLE READ` and `SERIALIZABLE` isolation level. You can disable gap lock explicitly by changing the isolation level to `READ COMMITTED`
- Gap locking is **not needed** for statements that lock rows **using a unique index**to search for a unique row.  (This does not include the case that the search condition includes only some columns of a multiple-column unique index; in that case, gap locking does occur.) 
- Gap locks in InnoDB are “purely inhibitive”, which means that their **only purpose is to prevent other transactions from inserting to the gap**.Gap locks can co-exist. A gap lock taken by one transaction does not prevent another transaction from taking a gap lock on the same gap. There is no difference between shared and exclusive gap locks. They do not conflict with each other, and they perform the same function.
- Gap locking can be disabled explicitly. 
   -  change the transaction isolation level to READ COMMITTED.
   -  set `innodb_locks_unsafe_for_binlog = 1`(not in 8.0 doc)
 
Example:

```
create table ttp (a int, b varchar(10));
insert into ttp values (1, 'cc');
insert into ttp values (2, 'dd');
insert into ttp values (20, 'dd');
insert into ttp values (25, 'dd');
create index idx_a on ttp(a); -- 必不可少

delete from ttp where a = 23;
```

`show engine innodb status` 显示如下：

```
TABLE LOCK table `test1`.`ttp` trx id A0640DCF lock mode IX
RECORD LOCKS space id 6789 page no 4 n bits 72 index `idx_a` of table `test1`.`ttp` trx id A0640DCF lock_mode X locks gap before rec
Record lock, heap no 5 PHYSICAL RECORD: n_fields 2; compact format; info bits 0
 0: len 4; hex 80000019; asc     ;;
 1: len 6; hex 000001ae5251; asc     RQ;;
```

执行`insert into ttp values(22, '');``insert into ttp values(24, '');`都不可以，但是`insert into ttp values(25, '');`是OK的，说明不是一个next-key lock.


### Next-key Lock

- A next-key lock is a combination of a record lock on the index record and a gap lock on the gap before the index record.
- InnoDB performs row-level locking in such a way that when it searches or scans a table index(which prevents phantom rows), it sets shared or exclusive locks on the index records it encounters.
- A next-key lock is an index-record lock plus a gap lock on the **gap preceding the index record**. 

next-key lock range example:  
Suppose that an index contains the values 10, 11, 13, and 20.
```
(negative infinity, 10]
(10, 11]
(11, 13]
(13, 20]
(20, positive infinity)
```
For the last interval, the next-key lock locks the gap above the largest value in the index and the “**supremum**” pseudo-record having a value higher than any value actually in the index. The supremum is not a real index record, so, in effect, this next-key lock locks only the gap following the largest index value.

Example:

```
select * from ttp where a> 20 for update;
```
`show engine innodb status` shows:
```
TABLE LOCK table `test1`.`ttp` trx id A067CC23 lock mode IX
RECORD LOCKS space id 6789 page no 4 n bits 80 index `idx_a` of table `test1`.`ttp` trx id A067CC23 lock_mode X
Record lock, heap no 1 PHYSICAL RECORD: n_fields 1; compact format; info bits 0
 0: len 8; hex 73757072656d756d; asc supremum;;
 ```

### Insert Intention Locks

- An insert intention lock is **a type of gap lock** set by INSERT operations prior to row insertion.
- This lock signals the intent to insert in such a way that multiple transactions inserting into the same index gap need not wait for each other if they are not inserting at the same position within the gap. 

Example:
```
session1:
start transaction;
select * from tt where a > 100 for update;


session2:
start transaction;
insert into tt(a) values (91); --waiting

```

`show engine innodb status;`shows
```
insert into tt(a) values (91)
------- TRX HAS BEEN WAITING 4 SEC FOR THIS LOCK TO BE GRANTED:
RECORD LOCKS space id 6784 page no 3 n bits 80 index `PRIMARY` of table `test1`.`tt` trx id A068EAF9 lock_mode X locks gap before rec insert intention waiting
Record lock, heap no 4 PHYSICAL RECORD: n_fields 5; compact format; info bits 0
 0: len 4; hex 80000066; asc    f;;
 1: len 6; hex 0000a068e94a; asc    h J;;
 2: len 7; hex d600005a94011d; asc    Z   ;;
 3: SQL NULL;
 4: SQL NULL;

------------------
TABLE LOCK table `test1`.`tt` trx id A068EAF9 lock mode IX
RECORD LOCKS space id 6784 page no 3 n bits 80 index `PRIMARY` of table `test1`.`tt` trx id A068EAF9 lock_mode X locks gap before rec insert intention waiting
Record lock, heap no 4 PHYSICAL RECORD: n_fields 5; compact format; info bits 0
 0: len 4; hex 80000066; asc    f;;
 1: len 6; hex 0000a068e94a; asc    h J;;
 2: len 7; hex d600005a94011d; asc    Z   ;;
 3: SQL NULL;
 4: SQL NULL;

---TRANSACTION A068E9BF, ACTIVE 66 sec
2 lock struct(s), heap size 376, 2 row lock(s)
MySQL thread id 127963261, OS thread handle 0x7efbf5bb0700, query id 4122875322 10.16.163.157 mams_test
TABLE LOCK table `test1`.`tt` trx id A068E9BF lock mode IX
RECORD LOCKS space id 6784 page no 3 n bits 80 index `PRIMARY` of table `test1`.`tt` trx id A068E9BF lock_mode X
Record lock, heap no 1 PHYSICAL RECORD: n_fields 1; compact format; info bits 0
 0: len 8; hex 73757072656d756d; asc supremum;;

Record lock, heap no 4 PHYSICAL RECORD: n_fields 5; compact format; info bits 0
 0: len 4; hex 80000066; asc    f;;
 1: len 6; hex 0000a068e94a; asc    h J;;
 2: len 7; hex d600005a94011d; asc    Z   ;;
 3: SQL NULL;
 4: SQL NULL;
```

compatiblity table:

|acquiring ↓/acquired→| Gap | Insert Intention| Record |
|---|---|---|---|
|Gap| Compatible | Compatible |Compatible 
|Insert Intention| Conflict| Compatible|Compatible|
|Record| Conflict|Compatible|Compatible|

### AUTO-INC Locks

- An AUTO-INC lock is a special table-level lock taken by transactions inserting into tables with AUTO_INCREMENT columns. 


### Extended Read

1. 
> SELECT statements will not lock any rows while running which is generally correct, however there a notable exception – INSERT INTO table1 SELECT * FROM table2. This statement will perform locking read (shared locks) for table2 table. It also applies to similar tables with where clause and joins. 

from [INSERT INTO … SELECT Performance with Innodb tables.](https://www.percona.com/blog/2006/07/12/insert-into-select-performance-with-innodb-tables/)

2. 

> If a non-INSERT write operation is more likely to not match any row because the INSERT part is yet to come on the transaction, don’t do it or use REPLACE INTO or use READ-COMMITTED transaction isolation.

from [One more InnoDB gap lock to avoid](https://www.percona.com/blog/2013/12/12/one-more-innodb-gap-lock-to-avoid/)

3. 
> REPEATABLE READ  
> For locking reads (SELECT with FOR UPDATE or LOCK IN SHARE MODE), UPDATE, and DELETE statements, locking depends on whether the statement uses a unique index with a unique search condition, or a range-type search condition.  
> - For a unique index with a unique search condition, InnoDB locks only the index record found, not the gap before it.  
> - For other search conditions, InnoDB locks the index range scanned, using gap locks or next-key locks to block insertions by other sessions into the gaps covered by the range. For information about gap locks and next-key locks.

from [Transaction Isolation Levels](https://dev.mysql.com/doc/refman/5.5/en/innodb-transaction-isolation-levels.html)

## Share

在此之前拉了两周的arts， 究其原因一个是工作上有点忙，另一个就是当事情琐碎繁杂时，我的精力损耗就会比平常快，在忙完之后还会有一段时间的什么也不想干时间段。我不知道这个在心理学上有什么说法，在我看来，这应该是大脑的上下文切换过多所带来的后果。

在CPU中，中断来了之后，CPU要及时响应中断就必须将当前正在执行的进程挪出去，并将进程上下文保存起来以备后续执行。保存上下文是很耗时的，在中断多了之后，CPU 做正常工作的时间就被这种事情挤占，导致事务繁忙时CPU的表现比正常时差。而这也是分时系统所带来的必须要承担的代价。

在上一家公司中，由于工作表现可以，后来升任小组长，带领着几个人做系统。一个人干活的时候考虑的是自己的活计干的怎么样，漂不漂亮，写出的代码有没有可读性，可维护性，管好自己就行了。当一个团队的任务放在你肩上时，需要考虑的事情就很不一样了，而且任务的类跟量都有变化。首先是类，底下每个人的需求都需要自己参加评审，与他人技术实施方案，表现就是开会变多了，自己code 的时间变少了，时间长了之后会发现自己的知识储备只够几次方案，往后的都是老调重弹，都是一个模子。类多了相应的量也上来，需要处理的事务变得杂起来，而且很多需要即时响应，这就会打断你当前正在工作的进程，等处理完了再回来发现还得把切换出去的上下文在加载进来，又得耗费一番，真真是脑袋痛。

那能怎么办呢？我发现其实这个事情的源头在于**响应的即时性**，如果事情不是即时需要反馈的，那就不需要上下文切换，也就不用那么脑袋痛了。因此我这一周将即时通讯工具里的消息基本上都设置为免打扰，手机开启静音模式，如果没有来工位找，说明事情就没有那么紧急。安静的做事，然后在一个小时或更久时间以后，休息一下，查阅一下邮件，翻阅一下即时通讯里的消息，将需要做的事情放到queue（当然这是一个priority queue，而不是一个FIFO queue）中，等待被完成。

这一周感觉好多了，事情杂乱无章，而处理方法也杂乱无章的话，你的大脑也也要变得杂乱无章了。我们要做事务的掌控者，不是事务的奴隶，大家共勉。

