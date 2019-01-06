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

�������node���һ��ȡһ����Ҫѭ��һ�飬����ÿ������Ҫ�ظ��ȽϺܶ�飬���mergeSort����list�ֳ������ֱַ�merge���ݹ�mergeһ��ֻmerge����node�����Դ�����Ԫ�صıȽϴ�����

����ÿ��Ԫ����Ҫ�Ƚ�log(k)�Σ�����ʱ�临�Ӷ�Ӧ����(size-of-all-elements)*log(n)

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

[How to think like a programmer��lessons in problem solving](https://medium.freecodecamp.org/how-to-think-like-a-programmer-lessons-in-problem-solving-d1d8bf1de7d2)

��������˼���������ʹ�õ��ļ���˼��

- understand
- plan
- divide
- conquer

��ƪֻ�Ǽ򵥵Ľ�������̵���߲��������ѧ����ʦ�Ƕ��Ǵ˵����֣���һЩ���������˼��������������ͻȻ����С��������Ļ�����+�����ں���ʽ���治����partial applied functionô�����Ժ��κζ���������������ҵ����һ������˵�����Ǹ��ܡ�

����ṩ�����ǵĲ�������һ�ݹ���������һ�ֽ��������ӽǣ�����һ�����˵�ǿ���������ǵ�����ۡ�

## Technique

ping�ĵײ�Э��ʵ�֣�

pingʹ��ICMP(Internet Control Message Protocol)Э�����һ̨�����Ƿ�ɴʹ�õ�ICMP��������ͻ���Ӧ�������£�

![icmp����](http://processors.wiki.ti.com/images/6/61/Frame_format_icmp.png)

ping��ICMP TypeΪ0��8��ICMP CodeΪ0��unixϵͳ�н�Identifier(��ʶ��)��Ϊ���ͽ��̵�ID����ʶ�����ͬ��ping���򷵻ص���Ϣ��Sequence-Number��0��ʼ��ÿ����һ���µĻ�������ͼ�1.

ping���
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

���Կ���ICMP���͵ı��Ĵ�СΪ56�ֽڣ�����20�ֵ�IP�ײ���8�ֽ�ICMP�ײ���IP���ݰ��ܳ���Ϊ84�ֽڡ�time������Ǳ��ĵ�����ʱ�䣬�����漰��RTT�ĸ�����Բ鿴wiki [RTT](https://en.m.wikipedia.org/wiki/Round-trip_delay_time)


����������ע�⵽`ping www.baidu.com`��`ping baidu.com`������ǲ�ͬ�ġ�`ping www.baidu.com`���ӡһ��`www.a.shifen.com`��������������Ҳ������й��������ۣ���ҿ��Կ�[���ڰٶ�DNS�Ľ�������](http://zhan.renren.com/starshen?gid=3602888498023142484&checked=true) ��
 [����Ping�ٶ��������˼��](https://blog.csdn.net/Wu_Roc/article/details/77168426) �ҳ�����һ��`www.a.shifen.com`�ǲ��ɷ��ʵģ�����`https://www.shifen.com/`�����Nginx��ҳ

�����кܶ����������Ϊ�ܾ���ping���ٶ�һֱ�����ſ����Ƿ������ǲ���������ͨ�԰ɡ�

## Share

���ܶ���������׫д�ġ���Ѷ����������Ѷһ·�����ĳɹ����̽�����һ�飬���ҿ����˺ܶ����ڻ�������˾��ս��Ӱ�ӣ�Ʃ���Ա��Ʃ�����ữ����Ȼ��ЩҲ��������ҵ��Ŀ����������ľ������������ǿ�������Ѷ��

�ͷ���һ��������

```
��ʷ����ʤ������д����仰һ��Ҳû�����Һ�������Ѷ��3Q��ս����ʶ�����Լ��ķ�ն�ת�򿪷ţ������������֮ǰ�Ĵ���ʳ�Ķ���Ҳֻ��һ�ʴ�����С����Ĳ�Ʒ���С�����ܣ��Դ������������в�û�����֣���������źܶ��º������ʽ����������������Ѷ�ڷ�չ������һ���������������ʧ�ܣ�������������Ŀ������ս���Ե���Ŀ���ߺ��������ɹ�����Ŀ�������Ƶģ����ɹ�����Ŀ���и���ʧ��֮��������Ӧ�ø���ɹ�������ͬʱҲҪ����ʧ�ܣ�һ������ֻ�жԳɹ��Ĵ�����������ʧ���ۼƵľ��飬�������Ҳ�㲻��һ��ΰ��Ĵ��ǣ�ֻ����Ϊһ���ɿ��Ķ���ա�
```