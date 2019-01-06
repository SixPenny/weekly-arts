## Algorithm

### Description

Difficulty： Hard  
Tag： Array Sort  


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

从左向右一次遍历，合并相交的区间

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

虽然已经很简洁，但和网友的解法比起来就ugly了。

[简单的解法Java](https://leetcode.com/problems/insert-interval/discuss/21602/Short-and-straight-forward-Java-solution)
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

[炸天的解法Python](https://leetcode.com/problems/insert-interval/discuss/21622/7+-lines-3-easy-solutions)

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
2. Fewer women are marrying out of need, Fisher says; instead, they’re marrying to please themselves. But that also means when they’re dissatisfied with something, they can feel justified to go elsewhere.
3. They end up having affairs not because they’re unhappy in their marriage, but because the pressure of feeling they have to be perfect ends up being explosive.”

## Tips
 
本周tip是对hibernate的使用思考，hibernate 的思想是ORM， 以对象映射数据库，这个模型给我们提供了极大的开发便利，利于前期启动项目快速上线。

这两天线上出现问题查看日志是由于 hibernate 抛出 `StaleObjectException` 的，大家没有对乐观锁做重试，导致业务逻辑回滚。这也看出 hibernate 的思想 ORM 模型的弊端：对数据库行的控制粒度太粗。实际上很多并发写的请求一般只写某几个字段值，两个不同的更新对同一行是没有并发问题的，但由于 hibernate 每次都需要写全部字段，才造成更新1请求 -> version++ -> 更新2请求 -> version过期，抛出异常。

这个问题的解决办法一是使用 `@DynamicUpdate` 来只更新所需字段(新版本hibernate才会有)，如果有字段级的并发，为防止覆盖，可以使用`@OptimisticLock` 来将更新的字段加入到 where 条件中。

在以前的tip中比较过 hibernate 与mybatis，mybatis 是 sql centric 的，可以实现更细粒度的控制，在我看来，mybatis这种按需更新在并发高的时候更有利，而且现在也有了很多mybatis 的自动生成框架，是一种更好的选择。现在项目里使用的出3.x 版本的hibernate，`@DynamicUpdate` 都未支持，而且加了`@Version`的并发控制，无法使用上面的两种解决办法。`StaleObjectException`真的是一个头疼的问题，现在看来只能catch这个异常手动重试了。

## Share

#### 问题说明

昨天发现线上有一些业务逻辑没有执行到，但是代码入口代码日志已经打印，深入下去一看，底层库里有一个事件执行的方法在每次执行时都会 new 一个 thread，在以往量不大时没有问题，量大时就可能导致线程创建不出来，报OOM错误（由于有同事在我看这个时重启了服务导致 gc 日志被清空和栈信息丢失，这个原因只是一个猜测）。

由此让我好奇几个问题  
1. Java 最多可以创建多少线程？
2. Java 控制线程大小选项 `-Xss` 的具体含义是什么？
3. Java 的选项 `-Xmx` `-Xms` 控制堆的选项对线程创建有无影响？
4. Java 的线程具体是怎么实现的？

#### 探索

其实这几个问题是相互交错的，在查询过程中很多答案对这几个问题都有涉及，因此下面很多链接并不仅仅是针对某一个问题，更是一个一般的描述。

Java 虚拟机运行于 Linux服务器上，因此第一个问题和第四个问题可以合在一起看。Java 线程直接map的 OS 的 native thread[^1] [^2], 因此Linux 对线程的限制也就限制了 Java 可以创建的线程。Linux 对系统能创建的总的线程数和每个用户能够创建的线程数都是有限制的。  
操作系统总的的限制可以看 `/proc/sys/kernel/pid_max` 值[^4]，`/proc/sys/kernel/threads-max `[^5] 值, `/proc/sys/vm/max_map_count` 的值对线程创建也有影响，如果太小在创建太多线程后会报错：
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
其他用户、组等限制可以在 `/etc/security/limits.conf` 中查看，用户的限制也可以使用 `ulimit -u` 来查看

对于第二个问题，`-Xss` 的具体含义模糊点在于以前一直以为这是限制每个线程能够使用的栈的最大值，但是在查问题过程中看到有一个网友回答[How does Java (JVM) allocate stack for each thread](https://stackoverflow.com/questions/36898701/how-does-java-jvm-allocate-stack-for-each-thread) 里面提到`The minimum stack size in HotSpot for a thread seems to be fixed. This is what the aforementioned -Xss option is for.`,这句话应该是不对的，`Xss` 限制的就是线程栈的最大值。因此接下来就是这个栈大小是动态扩展的还是线程创建时就直接分配好的呢？根据 Java Spec [^3]，这两种方式根据实现决定，不过按照实验来看，栈大小应该是动态扩展的。

第三个问题，`-Xmx` 与 `-Xms` 决定了Java 使用堆的大小，一直有人说将两者设为一样大小可以让Java 在启动时就分配好，可以防止后续堆的抖动，但就我实验来看，堆并没有在一开始就分配了，选项这样设置应该只能控制堆可以分配的最大值，堆容量分配后就不再缩小(防止抖动效果)。因此堆大小会影响线程数量，但前提是堆已经被分配，如果堆一直没有使用，内存不会为堆保留。

#### 测试

服务器配置：  
OS ： centos 6.4  
jdk : java version "1.7.0_09-icedtea"  
      OpenJDK Runtime Environment (rhel-2.3.4.1.el6_3-x86_64)  
      OpenJDK 64-Bit Server VM (build 23.2-b09, mixed mode)  
内存：总16G,剩余内存13G

测试程序：
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
1. 未修改pid_max(32768), max_map_count等数值
    1. 执行 `java -Xmx8096m -Xms8096m -Xss1m ThreadTest`,显示创建了 31341后报OOM，不过这个值是变化的，但一直在31.3K范围中，内存剩余很多。
    2. 执行 `java -Xmx10096m -Xms10096m -Xss1m ThreadTest`，显示创建线程也在31K处报OOM
    3. 执行 `java -Xmx8096m -Xms8096m -Xss10m ThreadTest` 结果一样
2. 修改pid_max(1000000), max_map_count(1000000)
    1. 执行 `java -Xmx8096m -Xms8096m -Xss1m ThreadTest`,一直在创建，但是在到70多w的时候会一直卡，整个系统接近不响应，但一直关注内存还剩3-4G

更新程序将heap 打满：
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

执行 `java -Xmx12096m -Xms12096m -Xss1m -server ThreadTest`, 程序创建到耗尽heap 内存才报`OOM：heap size`,分配heap数组时报异常会导致线程退出，然后又继续创建线程。

由于java的大对象都分配在堆上，因此没什么好办法耗尽栈内存，但可以看出栈在初始化时是很小的，更大的影响因素还是Linux的线程数限制。

结论：  
1. Java 线程创建取决于操作系统限制(pid_max, max_map_count, memory等)
2. stack 的栈帧是动态分配的，`-Xss` 限制栈最大值
3. `-Xmx` `-Xms` 限制堆大小，与栈共用内存，是相互影响的。


#### Reference 

[^1]: [Light-Weight Processes: Dissecting Linux Threads](https://opensourceforu.com/2011/08/light-weight-processes-dissecting-linux-threads/)  

[^2]: [Distinguishing between Java threads and OS threads?](https://stackoverflow.com/questions/1888160/distinguishing-between-java-threads-and-os-threads)

[^3]: [2.5.2. Java Virtual Machine Stacks](https://docs.oracle.com/javase/specs/jvms/se7/html/jvms-2.html#jvms-2.5.2)

[^4]: [If threads share the same PID, how can they be identified?](https://stackoverflow.com/questions/9305992/if-threads-share-the-same-pid-how-can-they-be-identified)

[^5]: [Maximum number of threads that can be created within a process in C](https://www.geeksforgeeks.org/maximum-number-threads-can-created-within-process-c/)