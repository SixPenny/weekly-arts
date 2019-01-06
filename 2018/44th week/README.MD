## Algorithm

### Description

Difficulty�� Hard  
Tag�� Array Sort  


Given a set of non-overlapping intervals, insert a new interval into the intervals (merge if necessary).

You may assume that the intervals were initially sorted according to their start times.

Example 1:
```
Input: intervals = [[1,3],[6,9]], newInterval = [2,5]
Output: [[1,5],[6,9]]
```
Example 2:
```
Input: intervals = [[1,2],[3,5],[6,7],[8,10],[12,16]], newInterval = [4,8]
Output: [[1,2],[3,10],[12,16]]
Explanation: Because the new interval [4,8] overlaps with [3,5],[6,7],[8,10].
```

### Solution

��������һ�α������ϲ��ཻ������

```go
/**
 * Definition for an interval.
 * type Interval struct {
 *	   Start int
 *	   End   int
 * }
 */
func insert(intervals []Interval, newInterval Interval) []Interval {
    if len(intervals) == 0 {
        return []Interval{newInterval}
    }
    // head
    if newInterval.End < intervals[0].Start {
        return append([]Interval{newInterval}, intervals...)
    }
    result := make([]Interval,0)
    temp := new(Interval)
    temp.Start = newInterval.Start
    temp.End = newInterval.End
    for i, val := range intervals {
        //position finded
        if val.Start > temp.End{
            result = append(result, *temp)
            result = append(result, intervals[i:]...)
            temp = nil
            break
        }
        
        if val.End < temp.Start {
            result = append(result, val)
            continue
        }
        //merge
        temp.Start = min(temp.Start, val.Start)
        temp.End = max(temp.End, val.End)
    }
    //tail
    if temp != nil {
        return append(result, *temp)
    }
    return result
}

func min(a,b int) int {
    if a > b{
        return b
    }
    return a
}
func max(a,b int) int {
    if a > b {
        return a
    }
    return b
}
```

��Ȼ�Ѿ��ܼ�࣬�������ѵĽⷨ��������ugly�ˡ�

[�򵥵ĽⷨJava](https://leetcode.com/problems/insert-interval/discuss/21602/Short-and-straight-forward-Java-solution)
```java
public List<Interval> insert(List<Interval> intervals, Interval newInterval) {
    List<Interval> result = new LinkedList<>();
    int i = 0;
    // add all the intervals ending before newInterval starts
    while (i < intervals.size() && intervals.get(i).end < newInterval.start)
        result.add(intervals.get(i++));
    // merge all overlapping intervals to one considering newInterval
    while (i < intervals.size() && intervals.get(i).start <= newInterval.end) {
        newInterval = new Interval( // we could mutate newInterval here also
                Math.min(newInterval.start, intervals.get(i).start),
                Math.max(newInterval.end, intervals.get(i).end));
        i++;
    }
    result.add(newInterval); // add the union of intervals we got
    // add all the rest
    while (i < intervals.size()) result.add(intervals.get(i++)); 
    return result;
}
```

[ը��ĽⷨPython](https://leetcode.com/problems/insert-interval/discuss/21622/7+-lines-3-easy-solutions)

```python
def insert(self, intervals, newInterval):
    s, e = newInterval.start, newInterval.end
    left = [i for i in intervals if i.end < s]
    right = [i for i in intervals if i.start > e]
    if left + right != intervals:
        s = min(s, intervals[len(left)].start)
        e = max(e, intervals[~len(right)].end)
    return left + [Interval(s, e)] + right
```

## Review 

[The Future of Infidelity Is Female](https://medium.com/s/thenewnew/the-future-of-infidelity-is-female-4e2da7cc7037)

1. And yet what she liked most, she says, besides the great sex, was the ability to be someone else for a while.
2. Fewer women are marrying out of need, Fisher says; instead, they��re marrying to please themselves. But that also means when they��re dissatisfied with something, they can feel justified to go elsewhere.
3. They end up having affairs not because they��re unhappy in their marriage, but because the pressure of feeling they have to be perfect ends up being explosive.��

## Tips
 
����tip�Ƕ�hibernate��ʹ��˼����hibernate ��˼����ORM�� �Զ���ӳ�����ݿ⣬���ģ�͸������ṩ�˼���Ŀ�������������ǰ��������Ŀ�������ߡ�

���������ϳ�������鿴��־������ hibernate �׳� `StaleObjectException` �ģ����û�ж��ֹ��������ԣ�����ҵ���߼��ع�����Ҳ���� hibernate ��˼�� ORM ģ�͵ı׶ˣ������ݿ��еĿ�������̫�֡�ʵ���Ϻܶಢ��д������һ��ֻдĳ�����ֶ�ֵ��������ͬ�ĸ��¶�ͬһ����û�в�������ģ������� hibernate ÿ�ζ���Ҫдȫ���ֶΣ�����ɸ���1���� -> version++ -> ����2���� -> version���ڣ��׳��쳣��

�������Ľ���취һ��ʹ�� `@DynamicUpdate` ��ֻ���������ֶ�(�°汾hibernate�Ż���)��������ֶμ��Ĳ�����Ϊ��ֹ���ǣ�����ʹ��`@OptimisticLock` �������µ��ֶμ��뵽 where �����С�

����ǰ��tip�бȽϹ� hibernate ��mybatis��mybatis �� sql centric �ģ�����ʵ�ָ�ϸ���ȵĿ��ƣ����ҿ�����mybatis���ְ�������ڲ����ߵ�ʱ�����������������Ҳ���˺ܶ�mybatis ���Զ����ɿ�ܣ���һ�ָ��õ�ѡ��������Ŀ��ʹ�õĳ�3.x �汾��hibernate��`@DynamicUpdate` ��δ֧�֣����Ҽ���`@Version`�Ĳ������ƣ��޷�ʹ����������ֽ���취��`StaleObjectException`�����һ��ͷ�۵����⣬���ڿ���ֻ��catch����쳣�ֶ������ˡ�

## Share

#### ����˵��

���췢��������һЩҵ���߼�û��ִ�е������Ǵ�����ڴ�����־�Ѿ���ӡ��������ȥһ�����ײ������һ���¼�ִ�еķ�����ÿ��ִ��ʱ���� new һ�� thread��������������ʱû�����⣬����ʱ�Ϳ��ܵ����̴߳�������������OOM����������ͬ�����ҿ����ʱ�����˷����� gc ��־����պ�ջ��Ϣ��ʧ�����ԭ��ֻ��һ���²⣩��

�ɴ����Һ��漸������  
1. Java �����Դ��������̣߳�
2. Java �����̴߳�Сѡ�� `-Xss` �ľ��庬����ʲô��
3. Java ��ѡ�� `-Xmx` `-Xms` ���ƶѵ�ѡ����̴߳�������Ӱ�죿
4. Java ���߳̾�������ôʵ�ֵģ�

#### ̽��

��ʵ�⼸���������໥�����ģ��ڲ�ѯ�����кܶ�𰸶��⼸�����ⶼ���漰���������ܶ����Ӳ������������ĳһ�����⣬����һ��һ���������

Java ����������� Linux�������ϣ���˵�һ������͵��ĸ�������Ժ���һ�𿴡�Java �߳�ֱ��map�� OS �� native thread[^1] [^2], ���Linux ���̵߳�����Ҳ�������� Java ���Դ������̡߳�Linux ��ϵͳ�ܴ������ܵ��߳�����ÿ���û��ܹ��������߳������������Ƶġ�  
����ϵͳ�ܵĵ����ƿ��Կ� `/proc/sys/kernel/pid_max` ֵ[^4]��`/proc/sys/kernel/threads-max `[^5] ֵ, `/proc/sys/vm/max_map_count` ��ֵ���̴߳���Ҳ��Ӱ�죬���̫С�ڴ���̫���̺߳�ᱨ����
```
OpenJDK 64-Bit Server VM warning: Attempt to protect stack guard pages failed.
OpenJDK 64-Bit Server VM warning: Attempt to allocate stack guard pages failed.
OpenJDK 64-Bit Server VM warning: Attempt to allocate stack guard pages failed.
OpenJDK 64-Bit Server VM warning: Attempt to deallocate stack guard pages failed.
Exception in thread "main" java.lang.OutOfMemoryError: unable to create new native thread
        at java.lang.Thread.start0(Native Method)
        at java.lang.Thread.start(Thread.java:691)
        at ThreadTest.main(ThreadTest.java:6)
OpenJDK 64-Bit Server VM warning: Attempt to deallocate stack guard pages failed.
OpenJDK 64-Bit Server VM warning: Attempt to allocate stack guard pages failed.
```
�����û���������ƿ����� `/etc/security/limits.conf` �в鿴���û�������Ҳ����ʹ�� `ulimit -u` ���鿴

���ڵڶ������⣬`-Xss` �ľ��庬��ģ����������ǰһֱ��Ϊ��������ÿ���߳��ܹ�ʹ�õ�ջ�����ֵ�������ڲ���������п�����һ�����ѻش�[How does Java (JVM) allocate stack for each thread](https://stackoverflow.com/questions/36898701/how-does-java-jvm-allocate-stack-for-each-thread) �����ᵽ`The minimum stack size in HotSpot for a thread seems to be fixed. This is what the aforementioned -Xss option is for.`,��仰Ӧ���ǲ��Եģ�`Xss` ���Ƶľ����߳�ջ�����ֵ����˽������������ջ��С�Ƕ�̬��չ�Ļ����̴߳���ʱ��ֱ�ӷ���õ��أ����� Java Spec [^3]�������ַ�ʽ����ʵ�־�������������ʵ��������ջ��СӦ���Ƕ�̬��չ�ġ�

���������⣬`-Xmx` �� `-Xms` ������Java ʹ�öѵĴ�С��һֱ����˵��������Ϊһ����С������Java ������ʱ�ͷ���ã����Է�ֹ�����ѵĶ�����������ʵ���������Ѳ�û����һ��ʼ�ͷ����ˣ�ѡ����������Ӧ��ֻ�ܿ��ƶѿ��Է�������ֵ�������������Ͳ�����С(��ֹ����Ч��)����˶Ѵ�С��Ӱ���߳���������ǰ���Ƕ��Ѿ������䣬�����һֱû��ʹ�ã��ڴ治��Ϊ�ѱ�����

#### ����

���������ã�  
OS �� centos 6.4  
jdk : java version "1.7.0_09-icedtea"  
      OpenJDK Runtime Environment (rhel-2.3.4.1.el6_3-x86_64)  
      OpenJDK 64-Bit Server VM (build 23.2-b09, mixed mode)  
�ڴ棺��16G,ʣ���ڴ�13G

���Գ���
```java 
public class ThreadTest {
    public static void main(String[] args) {
        for (int i = 0; i < Integer.MAX_VALUE; i++) {
            System.out.println("create " + i + "th thread");
            try {
                new Thread(new Runnable() {
                    @Override
                    public void run() {
                        try {
                            while (true) {
                                Thread.sleep(100);
                            }
                        } catch (Exception e) {

                        }
                    }
                }).start();
            } catch (StackOverflowError stackOverflowError) {
                System.out.println("create " + i + "th thread error, stackOverflow");
                stackOverflowError.printStackTrace();
            } catch (Exception e) {
                System.out.println("create " + i + "th thread error");
                e.printStackTrace();
                break;
            }
        }
        while (true) {
            try {
                Thread.sleep(1000);
            } catch (InterruptedException e) {
                e.printStackTrace();
                break;
            }
        }
    }
}
```
1. δ�޸�pid_max(32768), max_map_count����ֵ
    1. ִ�� `java -Xmx8096m -Xms8096m -Xss1m ThreadTest`,��ʾ������ 31341��OOM���������ֵ�Ǳ仯�ģ���һֱ��31.3K��Χ�У��ڴ�ʣ��ܶࡣ
    2. ִ�� `java -Xmx10096m -Xms10096m -Xss1m ThreadTest`����ʾ�����߳�Ҳ��31K����OOM
    3. ִ�� `java -Xmx8096m -Xms8096m -Xss10m ThreadTest` ���һ��
2. �޸�pid_max(1000000), max_map_count(1000000)
    1. ִ�� `java -Xmx8096m -Xms8096m -Xss1m ThreadTest`,һֱ�ڴ����������ڵ�70��w��ʱ���һֱ��������ϵͳ�ӽ�����Ӧ����һֱ��ע�ڴ滹ʣ3-4G

���³���heap ������
```
public class ThreadTest {
    public static void main(String[] args) {
        
        for (int i = 0; i < Integer.MAX_VALUE; i++) {
            System.out.println("create " + i + "th thread");
            try {
                new Thread(new Runnable() {
                    @Override
                    public void run() {
                        byte[] heap = new byte[1024 * 1024];//1M
                        try {
                            while (true) {
                                Thread.sleep(100);
                            }
                        } catch (Exception e) {

                        }
                        System.out.println(heap[0]);
                    }
                }).start();
            } catch (StackOverflowError stackOverflowError) {
                System.out.println("create " + i + "th thread error, stackOverflow");
                stackOverflowError.printStackTrace();
            } catch (Exception e) {
                System.out.println("create " + i + "th thread error");
                e.printStackTrace();
                break;
            }
        }
        while (true) {
            try {
                Thread.sleep(1000);
            } catch (InterruptedException e) {
                e.printStackTrace();
                break;
            }
        }
        
    }
}
```

ִ�� `java -Xmx12096m -Xms12096m -Xss1m -server ThreadTest`, ���򴴽����ľ�heap �ڴ�ű�`OOM��heap size`,����heap����ʱ���쳣�ᵼ���߳��˳���Ȼ���ּ��������̡߳�

����java�Ĵ���󶼷����ڶ��ϣ����ûʲô�ð취�ľ�ջ�ڴ棬�����Կ���ջ�ڳ�ʼ��ʱ�Ǻ�С�ģ������Ӱ�����ػ���Linux���߳������ơ�

���ۣ�  
1. Java �̴߳���ȡ���ڲ���ϵͳ����(pid_max, max_map_count, memory��)
2. stack ��ջ֡�Ƕ�̬����ģ�`-Xss` ����ջ���ֵ
3. `-Xmx` `-Xms` ���ƶѴ�С����ջ�����ڴ棬���໥Ӱ��ġ�


#### Reference 

[^1]: [Light-Weight Processes: Dissecting Linux Threads](https://opensourceforu.com/2011/08/light-weight-processes-dissecting-linux-threads/)  

[^2]: [Distinguishing between Java threads and OS threads?](https://stackoverflow.com/questions/1888160/distinguishing-between-java-threads-and-os-threads)

[^3]: [2.5.2. Java Virtual Machine Stacks](https://docs.oracle.com/javase/specs/jvms/se7/html/jvms-2.html#jvms-2.5.2)

[^4]: [If threads share the same PID, how can they be identified?](https://stackoverflow.com/questions/9305992/if-threads-share-the-same-pid-how-can-they-be-identified)

[^5]: [Maximum number of threads that can be created within a process in C](https://www.geeksforgeeks.org/maximum-number-threads-can-created-within-process-c/)