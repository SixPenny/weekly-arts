## Algorithm

###  Description

Difficulty: Hard  
Tag: Sql  

The Trips table holds all taxi trips. Each trip has a unique Id, while Client_Id and Driver_Id are both foreign keys to the Users_Id at the Users table. Status is an ENUM type of (��completed��, ��cancelled_by_driver��, ��cancelled_by_client��).
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
The Users table holds all users. Each user has an unique Users_Id, and Role is an ENUM type of (��client��, ��driver��, ��partner��).
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


����������˽⵽��һ��֪ʶ�㣬�� `left join ` �У������� where ������ʩ���� (from, left join) ���֮��ı��ϵģ������ǽ���Ӱ�� left join �еı�

���ֻ��ҪӰ��Ҫleft join �ı������ְ취
1. �������� on ����
2. ��ʱ����ʽΪ`left join (select * fro users u where u.banned = 'No')`

## Review 

[9999999999999999.0 - 9999999999999998.0](http://geocar.sdf1.org/numbers.html)

It's a simple floating point problem. Just like `0.1 + 0.2 != 0.3` which is a typical problem of IEEE 754. But we cannot blame computer language. `Computer isn't accurate`. It's against our consiceness but in some extent it's true. Computer use its 16/32/64 bits to represent our real world which is far more enough. One more thing that we should we keep in mind  is that computer is base 2 while real world is base 10. We can do this to avoid these problems:
- Never use floating points
- Use integer instead of float points
- Use BigDecimal in java or other similar tool in the other language

## Tips

��Ҫʹ�� YYYY ��ʹ�� yyyy ����ȡ���

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

ʹ��format ���������ڵ���ȷ�ģ���Ҫ��ϲ��̫�磬parse �����Ľ������ȫ����������ͷ�ԡ� 

�� ISO 8601 �� Y ������� `Week of year`, �ǲ��ܺ� `MM``dd` һ��ʹ�õġ�Ҫ��������Ҫ�������ĸ�ʽ��Ԫ��һ��ʹ�ã� u (һ�ܵ��е�ĳ��) �� ww (һ���е�ĳ��)��ʹ��������Ԫ�����ǾͿ�����ȷ�ı�ʾ�����ˡ�
����һ�·ݵ�����
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
u ����java �ĵ��Ľ���Ϊ`Day number of week (1 = Monday, ..., 7 = Sunday)`, �Ǵ�1 ��ʼ�ģ���������Ϊһ���Ǵ���һ�����գ����������ִ��������7 Ҳ��������Ӧ����ÿ�ܿ�ʼ�ĵ�һ��
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
��ͦ���˺�Ϳ�ģ���Ҫ��ס�������������������ж���������Ϊ0���������������ǵ�����  

�ص���������Ǹ���������ʹ��`MM-dd-YYYY`�� parse һ���ַ������ڵ�ʱ�򣬳�������ֵĽ������������һ��ʵ�飺

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

�����������Է��֣���Щ���ڶ���ÿ��ĵ�һ�ܵĵ�һ��(����)�����������Ԫ����`YYYY`��ƥ��ʱ��������Զ��ȡ����Ĭ�ϵĵ�һ�ܵ�һ�졣

ע��
Erica Sadun �� [ISO-8601, YYYY, yyyy, and why your year may be wrong](https://ericasadun.com/2018/12/25/iso-8601-yyyy-yyyy-and-why-your-year-may-be-wrong/) ʹ��swift ������ʾ�����������Java�����ڸ�ʽ�������Ĭ����Ϊ���� Apple ����һ�£���¼�ڴˡ�

## Share


Parallelism Vs Concurrency

��������Ӣ�Ľ��ͱ߽綼��ģ���ĵ��ʣ����뵽������͸�������ͷ��ˮ�ˡ����Ѷ��������ʵĽ���Ҳ�Ǹ�ִһ�ʣ�������Stack Overflow ��Ҳ�ʹ��������[What is the difference between concurrency and parallelism?](https://stackoverflow.com/questions/1050222/what-is-the-difference-between-concurrency-and-parallelism), ������ߵĽ���������ƽ�����յ���һ�£�Parallelism(����) ��ʾ���������ͬһʱ��ͬʱ���У�������ʾ�����������ڲ�ͬ��ʱ��Ƭ��ִ�У���ĳһ��ʱ�̿���ֻ��һ���������С����ҿ����������ĸ��˵ĸо����������CPU �ǵ��˻��Ƕ�ˣ�û��ʲô��ֵ����¥�����Ѹ�����parallelism �� concurrency ֮�䲻ͬ�Ĺؼ�������`Parallelism: Independentability, Concurrency: Interruptability`, ������������������ӡ�

�ؼ������������ʶ����ǵĿ�����ʲôָ�������أ������溬����������ȫû��ͷ����������Ƴ���ʱ���ǣ�������̱߳�̸����ĸ�Ϊָ���أ��������߶���Ҫ���ǣ�(������������Ϻ��в�������Ϊ����ˣ����оͲ���Ҫ������ô��)

Haskell ��wiki����������й�һƪ[����](https://wiki.haskell.org/Parallelism_vs._Concurrency), ��������˽���:`The term Parallelism refers to techniques to make programs faster by performing several computations at the same time. The term Concurrency refers to techniques that make programs more usable.` ������Ϊ�����������ٶȵļ�������������Ϊ��ʹ��������á�`If you run distributed-net computations in the background while working with interactive applications in the foreground, that is concurrency. On the other hand, dividing a task into packets that can be computed via distributed-net clients is parallelism.`. Go �ķ����� Rob Pike �����������һ�� presentation [Concurrency is not parallelism](https://blog.golang.org/concurrency-is-not-parallelism), ���� concurrency ����ָ�����Ǳ�̵�׼��Ҳ������˵��`Concurrency is about dealing with lots of things at once. Parallelism is about doing lots of things at once.` ������Ҫ������һ�����ֽ⿪�������Ժ�����������������Ĳ��裬ÿһ�������Ƕ�����Ҳ�������������Ĳ��衣�����ǽ���������ɺ����������Կ������ƺܶ�ݲ���ִ�С�

�ڵ�������У��߳�����С��ִ�е�λ�������ά��������ֻ���ǲ��������ǵ�Ӱ�죬��Ϊִ���ǲ�̫��Ҫ���ǹ��ĵģ�OS �����Լ����õ��ȡ��ڷֲ�ʽϵͳ�У���̨�����������С�ĵ�λ�����ǲ���Ҫ���ǲ���������Ҫ���ǲ��С�һ��������ηֽ��һϵ�в��裬����֮��������໥�����ģ����ǲ���ά�ȣ���һ������ֳɲ�ͬ���飬����֮���ǿ��Բ���ִ�еģ�����Ҫ���ǵ������⡣��˲����Ͳ��ж���˼ά��Ҫ���ǲ�һ���ģ���Ҳ���������������ʱ�����ֽ��˼·��
