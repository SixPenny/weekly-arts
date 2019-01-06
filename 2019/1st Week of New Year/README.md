## Algorithm

###  Description

Difficulty: Hard  
Tag: Sql  

The Trips table holds all taxi trips. Each trip has a unique Id, while Client_Id and Driver_Id are both foreign keys to the Users_Id at the Users table. Status is an ENUM type of (‘completed’, ‘cancelled_by_driver’, ‘cancelled_by_client’).
```plain
+----+-----------+-----------+---------+--------------------+----------+
| Id | Client_Id | Driver_Id | City_Id |        Status      |Request_at|
+----+-----------+-----------+---------+--------------------+----------+
| 1  |     1     |    10     |    1    |     completed      |2013-10-01|
| 2  |     2     |    11     |    1    | cancelled_by_driver|2013-10-01|
| 3  |     3     |    12     |    6    |     completed      |2013-10-01|
| 4  |     4     |    13     |    6    | cancelled_by_client|2013-10-01|
| 5  |     1     |    10     |    1    |     completed      |2013-10-02|
| 6  |     2     |    11     |    6    |     completed      |2013-10-02|
| 7  |     3     |    12     |    6    |     completed      |2013-10-02|
| 8  |     2     |    12     |    12   |     completed      |2013-10-03|
| 9  |     3     |    10     |    12   |     completed      |2013-10-03| 
| 10 |     4     |    13     |    12   | cancelled_by_driver|2013-10-03|
+----+-----------+-----------+---------+--------------------+----------+
```
The Users table holds all users. Each user has an unique Users_Id, and Role is an ENUM type of (‘client’, ‘driver’, ‘partner’).
```plain
+----------+--------+--------+
| Users_Id | Banned |  Role  |
+----------+--------+--------+
|    1     |   No   | client |
|    2     |   Yes  | client |
|    3     |   No   | client |
|    4     |   No   | client |
|    10    |   No   | driver |
|    11    |   No   | driver |
|    12    |   No   | driver |
|    13    |   No   | driver |
+----------+--------+--------+
```
Write a SQL query to find the cancellation rate of requests made by unbanned users between Oct 1, 2013 and Oct 3, 2013. For the above tables, your SQL query should return the following rows with the cancellation rate being rounded to two decimal places.
```
+------------+-------------------+
|     Day    | Cancellation Rate |
+------------+-------------------+
| 2013-10-01 |       0.33        |
| 2013-10-02 |       0.00        |
| 2013-10-03 |       0.50        |
+------------+-------------------+
```


### Solution

201ms:
```
select a.Request_at Day, round(sum(case when a.status = 'cancelled_by_driver' then 1 when a.status = 'cancelled_by_client' then 1 else 0 end) /count(1), 2) as 'Cancellation Rate'
from trips a 
where exists (select 1 from users u where u.users_id = a.client_id and u.banned='No')
and exists (select 1 from users u where u.users_id = a.driver_id and u.banned='No')
and a.Request_at between '2013-10-01' and '2013-10-03'
group by a.Request_at
```

198ms:
```
select a.Request_at Day, round(sum(case when a.status != 'completed' then 1  else 0 end) /count(1), 2) as 'Cancellation Rate'
from trips a 
where exists (select 1 from users u where u.users_id = a.client_id and u.banned='No')
and exists (select 1 from users u where u.users_id = a.driver_id and u.banned='No')
and a.Request_at between '2013-10-01' and '2013-10-03'
group by a.Request_at
```

178ms:
```
select a.Request_at Day, round(sum(case when a.status = 'cancelled_by_driver' then 1 when a.status = 'cancelled_by_client' then 1 else 0 end) /count(1), 2) as 'Cancellation Rate'
from trips a 
left join users d on d.users_id = a.client_id
left join users c on c.users_id = a.client_id
where d.banned='No' and c.banned = 'No'
and a.Request_at between '2013-10-01' and '2013-10-03'
group by a.Request_at
```

170ms:
```
select a.Request_at Day, round(sum(case when a.status != 'completed' then 1 else 0 end) /count(1), 2) as 'Cancellation Rate'
from trips a 
left join users d on d.users_id = a.client_id
left join users c on c.users_id = a.client_id
where d.banned='No' and c.banned = 'No'
and a.Request_at between '2013-10-01' and '2013-10-03'
group by a.Request_at
```


从做这道题了解到了一个知识点，在 `left join ` 中，最外层的 where 条件是施加在 (from, left join) 完成之后的表上的，而不是仅仅影响 left join 中的表。

如果只想要影响要left join 的表，有两种办法
1. 条件加在 on 后面
2. 临时表，形式为`left join (select * fro users u where u.banned = 'No')`

## Review 

[9999999999999999.0 - 9999999999999998.0](http://geocar.sdf1.org/numbers.html)

It's a simple floating point problem. Just like `0.1 + 0.2 != 0.3` which is a typical problem of IEEE 754. But we cannot blame computer language. `Computer isn't accurate`. It's against our consiceness but in some extent it's true. Computer use its 16/32/64 bits to represent our real world which is far more enough. One more thing that we should we keep in mind  is that computer is base 2 while real world is base 10. We can do this to avoid these problems:
- Never use floating points
- Use integer instead of float points
- Use BigDecimal in java or other similar tool in the other language

## Tips

不要使用 YYYY ，使用 yyyy 来获取年份

```java
@org.junit.Test
public void test() throws ParseException {
    SimpleDateFormat yyyy = new SimpleDateFormat("MM-dd-yyyy");
    SimpleDateFormat YYYY = new SimpleDateFormat("MM-dd-YYYY");

    System.out.println(yyyy.format(new Date()));
    System.out.println(YYYY.format(new Date()));

    System.out.println(yyyy.parse("12-25-2018"));
    System.out.println(YYYY.parse("12-25-2018"));
}

Console:      
01-06-2019
01-06-2019
Tue Dec 25 00:00:00 CST 2018
Sun Dec 31 00:00:00 CST 2017    
```

使用format 出来的日期的正确的，不要欢喜的太早，parse 给出的结果就完全让人摸不着头脑。 

在 ISO 8601 中 Y 代表的是 `Week of year`, 是不能和 `MM``dd` 一起使用的。要想用他需要和其他的格式化元素一起使用， u (一周当中的某天) 和 ww (一年中的某周)，使用这两个元素我们就可以正确的表示日期了。
这是一月份的日历
```
    January 2019      
Su Mo Tu We Th Fr Sa  
       1  2  3  4  5  
 6  7  8  9 10 11 12  
13 14 15 16 17 18 19  
20 21 22 23 24 25 26  
27 28 29 30 31
```
```java
@org.junit.Test
public void test() throws ParseException {
    SimpleDateFormat YYYY = new SimpleDateFormat("ww-u-YYYY");

    System.out.println(YYYY.format(new Date()));

    System.out.println(YYYY.parse("53-2-2018"));
    System.out.println(YYYY.parse("01-2-2019"));
}

Console:
02-7-2019
Tue Jan 01 00:00:00 CST 2019
Tue Jan 01 00:00:00 CST 2019

```
u 按照java 文档的解释为`Day number of week (1 = Monday, ..., 7 = Sunday)`, 是从1 开始的，会让你以为一周是从周一到周日，但从上面的执行来看，7 也就是周日应该是每周开始的第一天
```java
Calendar calendar = Calendar.getInstance();
calendar.set(Calendar.DAY_OF_YEAR, 1);//2019 Jan 1st
System.out.println(YYYY.format(calendar.getTime()));

calendar.set(Calendar.DAY_OF_YEAR, 6); //2019 Jan 6th
System.out.println(YYYY.format(calendar.getTime()));

calendar.set(Calendar.DAY_OF_YEAR, 7); //2019 Jan 7th
System.out.println(YYYY.format(calendar.getTime()));

Console:

01-2-2019
02-7-2019
02-1-2019
```
这挺让人糊涂的，需要记住这个规则，其他编程语言中都将周日作为0来处理，更符合人们的心理。  

回到最上面的那个，当我们使用`MM-dd-YYYY`来 parse 一个字符串日期的时候，出现了奇怪的结果。我们再做一下实验：

```java
System.out.println(YYYY.parse("02-02-2017"));
System.out.println(YYYY.parse("04-20-2017"));
System.out.println(YYYY.parse("12-25-2018"));
System.out.println(YYYY.parse("01-01-2018"));
System.out.println(YYYY.parse("12-26-2019"));
System.out.println(YYYY.parse("02-02-2019"));

Console:

Sun Jan 01 00:00:00 CST 2017
Sun Jan 01 00:00:00 CST 2017
Sun Dec 31 00:00:00 CST 2017
Sun Dec 31 00:00:00 CST 2017
Sun Dec 30 00:00:00 CST 2018
Sun Dec 30 00:00:00 CST 2018
```

对照日历可以发现，这些日期都是每年的第一周的第一天(周日)。因此在其他元素与`YYYY`不匹配时，程序永远获取的是默认的第一周第一天。

注：
Erica Sadun 的 [ISO-8601, YYYY, yyyy, and why your year may be wrong](https://ericasadun.com/2018/12/25/iso-8601-yyyy-yyyy-and-why-your-year-may-be-wrong/) 使用swift 语言演示了这种情况，Java语言在格式化语句与默认行为上与 Apple 都不一致，记录在此。

## Share


Parallelism Vs Concurrency

这是两个英文解释边界都很模糊的单词，翻译到中文里就更让人满头雾水了。网友对这两个词的解释也是各执一词，网友在Stack Overflow 上也问过这个问题[What is the difference between concurrency and parallelism?](https://stackoverflow.com/questions/1050222/what-is-the-difference-between-concurrency-and-parallelism), 排名最高的解释与我们平常接收到的一致：Parallelism(并行) 表示多个任务在同一时间同时运行，并发表示多个任务可以在不同的时间片上执行，但某一个时刻可以只有一个任务运行。在我看来这个定义的给人的感觉区别就在于CPU 是单核还是多核，没有什么价值。二楼的网友给出了parallelism 和 concurrency 之间不同的关键特征：`Parallelism: Independentability, Concurrency: Interruptability`, 并举了四种情况的例子。

关键是这两个单词对我们的开发有什么指导意义呢？从字面含义上来看完全没有头绪，我们设计程序时如果牵扯到多线程编程该以哪个为指导呢？或者两者都需要考虑？(并发编程字面上含有并发，但为何如此？并行就不需要考虑了么？)

Haskell 在wiki里对这两者有过一篇[文章](https://wiki.haskell.org/Parallelism_vs._Concurrency), 里面给出了解释:`The term Parallelism refers to techniques to make programs faster by performing several computations at the same time. The term Concurrency refers to techniques that make programs more usable.` 并行是为了提升计算速度的技术，而并发是为了使程序更合用。`If you run distributed-net computations in the background while working with interactive applications in the foreground, that is concurrency. On the other hand, dividing a task into packets that can be computed via distributed-net clients is parallelism.`. Go 的发明者 Rob Pike 曾对这个做过一个 presentation [Concurrency is not parallelism](https://blog.golang.org/concurrency-is-not-parallelism), 他将 concurrency 当做指导我们编程的准则，也就是他说的`Concurrency is about dealing with lots of things at once. Parallelism is about doing lots of things at once.` 我们需要将任务一步步分解开来，在脑海中描绘完成任务所需的步骤，每一步可以是独立的也可以依赖其他的步骤。当我们将任务步骤完成后，这个整体可以拷贝复制很多份并行执行。

在单机编程中，线程是最小的执行单位，在这个维度上我们只考虑并发对我们的影响，因为执行是不太需要我们关心的，OS 可以自己做好调度。在分布式系统中，单台机器变成了最小的单位，我们不仅要考虑并发，还需要考虑并行。一堆任务如何分解成一系列步骤，步骤之间可能是相互依赖的，这是并发维度；这一堆任务分成不同的组，任务之间是可以并行执行的，还需要考虑调度问题。因此并发和并行对于思维的要求是不一样的，这也是我们在面对问题时的两种解决思路。
