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

�˴�ʹ���������Ǻܺķ����ܵģ����ѵ�[���](https://leetcode.com/problems/minimum-time-difference/discuss/100640/Verbose-Java-Solution-Bucket)�ǳ�����, �����Լ�����Ļ�������̰�.

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

�����¶��̻ش��˿��һ��[����](https://www.itcodemonkey.com/article/11725.html), ����Ҫ�о�һ��MySQL ��������������Ƕ�[MySQL manual locking](https://dev.mysql.com/doc/refman/5.5/en/innodb-locking.html)��һ���ʼǡ�


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

���Կ����������,�� table ����һ�� IX ������������һ�� X �� record lock. 
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

ps: ִ��`show engine innodb status`֮ǰ��Ҫ�Ƚ�lock ��ش򿪣���Ҫ�������ű���������Ϣ�ɼ�[Enabling InnoDB Monitors](https://dev.mysql.com/doc/refman/5.5/en/innodb-enabling-monitors.html)
```
CREATE TABLE innodb_monitor (a INT) ENGINE=INNODB;
CREATE TABLE innodb_lock_monitor (a INT) ENGINE=INNODB;
```

��index Example:
```
create table ttp (a int, b varchar(10));
insert into ttp values (1, 'cc');
insert into ttp values (1, 'dd');

start transaction;
select * from ttp where b = 'dd' for update;

show engine innodb status;
```

������£����Կ���MySQL�Զ�������һ��[clustered index](https://dev.mysql.com/doc/refman/5.5/en/innodb-index-types.html),��������û����������ν��������������ȫ������ X ����
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
- Gap locks in InnoDB are ��purely inhibitive��, which means that their **only purpose is to prevent other transactions from inserting to the gap**.Gap locks can co-exist. A gap lock taken by one transaction does not prevent another transaction from taking a gap lock on the same gap. There is no difference between shared and exclusive gap locks. They do not conflict with each other, and they perform the same function.
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
create index idx_a on ttp(a); -- �ز�����

delete from ttp where a = 23;
```

`show engine innodb status` ��ʾ���£�

```
TABLE LOCK table `test1`.`ttp` trx id A0640DCF lock mode IX
RECORD LOCKS space id 6789 page no 4 n bits 72 index `idx_a` of table `test1`.`ttp` trx id A0640DCF lock_mode X locks gap before rec
Record lock, heap no 5 PHYSICAL RECORD: n_fields 2; compact format; info bits 0
 0: len 4; hex 80000019; asc     ;;
 1: len 6; hex 000001ae5251; asc     RQ;;
```

ִ��`insert into ttp values(22, '');``insert into ttp values(24, '');`�������ԣ�����`insert into ttp values(25, '');`��OK�ģ�˵������һ��next-key lock.


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
For the last interval, the next-key lock locks the gap above the largest value in the index and the ��**supremum**�� pseudo-record having a value higher than any value actually in the index. The supremum is not a real index record, so, in effect, this next-key lock locks only the gap following the largest index value.

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

|acquiring ��/acquired��| Gap | Insert Intention| Record |
|---|---|---|---|
|Gap| Compatible | Compatible |Compatible 
|Insert Intention| Conflict| Compatible|Compatible|
|Record| Conflict|Compatible|Compatible|

### AUTO-INC Locks

- An AUTO-INC lock is a special table-level lock taken by transactions inserting into tables with AUTO_INCREMENT columns. 


### Extended Read

1. 
> SELECT statements will not lock any rows while running which is generally correct, however there a notable exception �C INSERT INTO table1 SELECT * FROM table2. This statement will perform locking read (shared locks) for table2 table. It also applies to similar tables with where clause and joins. 

from [INSERT INTO �� SELECT Performance with Innodb tables.](https://www.percona.com/blog/2006/07/12/insert-into-select-performance-with-innodb-tables/)

2. 

> If a non-INSERT write operation is more likely to not match any row because the INSERT part is yet to come on the transaction, don��t do it or use REPLACE INTO or use READ-COMMITTED transaction isolation.

from [One more InnoDB gap lock to avoid](https://www.percona.com/blog/2013/12/12/one-more-innodb-gap-lock-to-avoid/)

3. 
> REPEATABLE READ  
> For locking reads (SELECT with FOR UPDATE or LOCK IN SHARE MODE), UPDATE, and DELETE statements, locking depends on whether the statement uses a unique index with a unique search condition, or a range-type search condition.  
> - For a unique index with a unique search condition, InnoDB locks only the index record found, not the gap before it.  
> - For other search conditions, InnoDB locks the index range scanned, using gap locks or next-key locks to block insertions by other sessions into the gaps covered by the range. For information about gap locks and next-key locks.

from [Transaction Isolation Levels](https://dev.mysql.com/doc/refman/5.5/en/innodb-transaction-isolation-levels.html)

## Share

�ڴ�֮ǰ�������ܵ�arts�� ����ԭ��һ���ǹ������е�æ����һ�����ǵ��������鷱��ʱ���ҵľ�����ľͻ��ƽ���죬��æ��֮�󻹻���һ��ʱ���ʲôҲ�����ʱ��Ρ��Ҳ�֪�����������ѧ����ʲô˵�������ҿ�������Ӧ���Ǵ��Ե��������л������������ĺ����

��CPU�У��ж�����֮��CPUҪ��ʱ��Ӧ�жϾͱ��뽫��ǰ����ִ�еĽ���Ų��ȥ���������������ı��������Ա�����ִ�С������������Ǻܺ�ʱ�ģ����ж϶���֮��CPU ������������ʱ��ͱ��������鼷ռ����������æʱCPU�ı��ֱ�����ʱ�����Ҳ�Ƿ�ʱϵͳ�������ı���Ҫ�е��Ĵ��ۡ�

����һ�ҹ�˾�У����ڹ������ֿ��ԣ���������С�鳤�������ż�������ϵͳ��һ���˸ɻ��ʱ���ǵ����Լ��Ļ�Ƹɵ���ô����Ư��Ư����д���Ĵ�����û�пɶ��ԣ���ά���ԣ��ܺ��Լ������ˡ���һ���Ŷӵ�������������ʱ����Ҫ���ǵ�����ͺܲ�һ���ˣ������������������б仯���������࣬����ÿ���˵�������Ҫ�Լ��μ����������˼���ʵʩ���������־��ǿ������ˣ��Լ�code ��ʱ������ˣ�ʱ�䳤��֮��ᷢ���Լ���֪ʶ����ֻ�����η���������Ķ����ϵ��ص�������һ��ģ�ӡ��������Ӧ����Ҳ��������Ҫ�����������������������Һܶ���Ҫ��ʱ��Ӧ����ͻ����㵱ǰ���ڹ����Ľ��̣��ȴ��������ٻ������ֻ��ð��л���ȥ���������ڼ��ؽ������ֵúķ�һ�����������Դ�ʹ��

������ô���أ��ҷ�����ʵ��������Դͷ����**��Ӧ�ļ�ʱ��**��������鲻�Ǽ�ʱ��Ҫ�����ģ��ǾͲ���Ҫ�������л���Ҳ�Ͳ�����ô�Դ�ʹ�ˡ��������һ�ܽ���ʱͨѶ���������Ϣ�����϶�����Ϊ����ţ��ֻ���������ģʽ�����û������λ�ң�˵�������û����ô���������������£�Ȼ����һ��Сʱ�����ʱ���Ժ���Ϣһ�£�����һ���ʼ�������һ�¼�ʱͨѶ�����Ϣ������Ҫ��������ŵ�queue����Ȼ����һ��priority queue��������һ��FIFO queue���У��ȴ�����ɡ�

��һ�ܸо��ö��ˣ������������£�����������Ҳ�������µĻ�����Ĵ���ҲҲҪ������������ˡ�����Ҫ��������ƿ��ߣ����������ū������ҹ��㡣
