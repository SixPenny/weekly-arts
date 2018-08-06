## Algorithm

### Merge k Sorted Lists

#### Description

Merge k sorted linked lists and return it as one sorted list. Analyze and describe its complexity.

Example:
```
Input:
[
  1->4->5,
  1->3->4,
  2->6
]
Output: 1->1->2->3->4->4->5->6
```

#### Mind path

多个有序node如果一次取一个需要循环一遍，这样每个都需要重复比较很多遍，借鉴mergeSort，将list分成两部分分别merge，递归merge一次只merge两个node，可以大大减少元素的比较次数。

由于每个元素需要比较log(k)次，所以时间复杂度应该是(size-of-all-elements)*log(n)

#### Solution

```java
package com.dylan.leetcode;

import org.junit.Assert;
import org.junit.Test;

/**
 * Created by liufengquan on 2018/8/1.
 */
public class MergeKSortedLists {

    public ListNode mergeKLists(ListNode[] lists) {
        if (lists == null || lists.length == 0) {
            return null;
        }
        if (lists.length == 1) {
            return lists[0];
        }
        return merge(lists, 0, lists.length);
    }

    private ListNode merge(ListNode[] nodes, int left, int right) {
        if ((right - left) == 1) {
            return nodes[left];
        }
        if ((right - left) == 2) {
            //merge
            return merge(nodes[left], nodes[left + 1]);
        }
        int middle = left + (right - left) / 2;
        return merge(merge(nodes, left, middle), merge(nodes, middle, right));
    }

    private ListNode merge(ListNode node1, ListNode node2) {
        ListNode temp = new ListNode(0);
        ListNode head = temp;
        while (node1 != null && node2 != null) {
            if (node1.val > node2.val) {
                temp.next = node2;
                temp = temp.next;
                node2 = node2.next;
            }else {
                temp.next = node1;
                temp = temp.next;
                node1 = node1.next;
            }
        }
        if (node1 != null) {
            temp.next = node1;
        }
        if (node2 != null) {
            temp.next = node2;
        }

        return head.next;
    }

    @Test
    public void test() {
        ListNode node1 = new ListNode(1);
        node1.next = new ListNode(4);
        node1.next.next = new ListNode(5);

        ListNode node2 = new ListNode(1);
        node2.next = new ListNode(3);
        node2.next.next = new ListNode(4);

        ListNode node3 = new ListNode(2);
        node3.next = new ListNode(6);

        ListNode[] nodes = new ListNode[]{node1, node2, node3};
        ListNode result = mergeKLists(nodes);

        Assert.assertEquals(1, result.val);
        result = result.next;
        Assert.assertEquals(1, result.val);
        result = result.next;
        Assert.assertEquals(2, result.val);
        result = result.next;
        Assert.assertEquals(3, result.val);
        result = result.next;
        Assert.assertEquals(4, result.val);
        result = result.next;
        Assert.assertEquals(4, result.val);
        result = result.next;
        Assert.assertEquals(5, result.val);
        result = result.next;
        Assert.assertEquals(6, result.val);
    }
}

```


## Review

[How to think like a programmer—lessons in problem solving](https://medium.freecodecamp.org/how-to-think-like-a-programmer-lessons-in-problem-solving-d1d8bf1de7d2)

里面阐述了计算机编程中使用到的几种思想

- understand
- plan
- divide
- conquer

这篇只是简单的讲述。编程到最高层面就是哲学，大师们都是此道高手，对一些方面有深度思考、剖析。今天突然发现小马哥提出的互联网+，这在函数式里面不就是partial applied function么，可以和任何东西连接起来，用业界另一个词来说明就是赋能。

编程提供给我们的不仅仅是一份工作，更是一种解决问题的视角，更高一点可以说是可以提高我们的世界观。

## Technique

ping的底层协议实现：

ping使用ICMP(Internet Control Message Protocol)协议测试一台主机是否可达。使用的ICMP回显请求和回显应答报文如下：

![icmp报文](http://processors.wiki.ti.com/images/6/61/Frame_format_icmp.png)

ping的ICMP Type为0或8，ICMP Code为0；unix系统中将Identifier(标识符)置为发送进程的ID，以识别出不同的ping程序返回的信息；Sequence-Number从0开始，每发送一次新的回显请求就加1.

ping输出
```
> ping baidu.com
PING baidu.com (123.125.115.110) 56(84) bytes of data.
64 bytes from 123.125.115.110: icmp_seq=1 ttl=49 time=13.3 ms
64 bytes from 123.125.115.110: icmp_seq=2 ttl=49 time=16.8 ms
64 bytes from 123.125.115.110: icmp_seq=3 ttl=49 time=17.9 ms
^C
--- baidu.com ping statistics ---
3 packets transmitted, 3 received, 0% packet loss, time 2387ms
rtt min/avg/max/mdev = 13.336/16.059/17.995/1.987 ms
```

可以看到ICMP发送的报文大小为56字节，加上20字的IP首部和8字节ICMP首部，IP数据包总长度为84字节。time输出的是报文的往返时间，这里涉及到RTT的概念，可以查看wiki [RTT](https://en.m.wikipedia.org/wiki/Round-trip_delay_time)


在这里我们注意到`ping www.baidu.com`与`ping baidu.com`的输出是不同的。`ping www.baidu.com`会打印一个`www.a.shifen.com`的域名，网友们也对这个有过不少讨论，大家可以看[关于百度DNS的解析过程](http://zhan.renren.com/starshen?gid=3602888498023142484&checked=true) 和
 [尝试Ping百度域名后的思考](https://blog.csdn.net/Wu_Roc/article/details/77168426) 我尝试了一下`www.a.shifen.com`是不可访问的，但是`https://www.shifen.com/`会出现Nginx首页

现在有很多服务器设置为拒绝被ping，百度一直保留着可能是方便我们测试网络连通性吧。

## Share

本周读了吴晓波撰写的《腾讯传》，将腾讯一路走来的成功历程讲述了一遍，让我看到了很多现在互联网公司的战略影子，譬如会员，譬如年轻化，当然这些也是在自身业务的考量上做出的决定，并不就是拷贝的腾讯。

就分享一下书评吧

```
历史是由胜利者书写的这句话一点也没错，我很欣赏腾讯在3Q大战后意识到了自己的封闭而转向开放，但传记里面对之前四处掠食的恶劣也只是一笔带过。小马哥的产品理念“小步快跑，试错迭代”在书中并没有体现，反而充斥着很多事后诸葛亮式的描述，我相信腾讯在发展过程中一定经历了数不清的失败，不计其数的项目甚至是战略性的项目上线后半死不活，成功的项目都是相似的，不成功的项目各有各的失败之处，我们应该给予成功掌声，同时也要尊重失败，一本传记只有对成功的吹捧而忽略了失败累计的经验，无论如何也算不上一本伟大的传记，只能沦为一本可看的读物罢。
```