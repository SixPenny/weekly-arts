## Algorithm

#### Description

Difficulty : Medium

You are given an array of positive and negative integers. If a number n at an index is positive, then move forward n steps. Conversely, if it's negative (-n), move backward n steps. Assume the first element of the array is forward next to the last element, and the last element is backward next to the first element. Determine if there is a loop in this array. A loop starts and ends at a particular index with more than 1 element along the loop. The loop must be "forward" or "backward'.

Example 1: Given the array [2, -1, 1, 2, 2], there is a loop, from index 0 -> 2 -> 3 -> 0.

Example 2: Given the array [-1, 2], there is no loop.

Note: The given array is guaranteed to contain no element "0".

Can you do it in O(n) time complexity and O(1) space complexity?

#### Solution

Points to be cared
1. A loop starts and ends at a particular index with more than 1 element along the loop. It means a satisfied loop contains more than 1 element
2. That the loop must be "forward" or "backward' means in a satisfied loop elements must be all postive or all negative


Mindpath:  
Since the give array is guaranteed to contain no 0, we start from a index which is not zero then loop through the array. 

Calculate next index and do next steps  
1. check next index is zero or not, if it is, break
2. set next index element to zero. It means that we visited that element
3. check the direction, if changed, break
4. check whether next index is equal the initial index, if so, we find a loop


Code blow:

```go
func circularArrayLoop(nums []int) bool {
    size := len(nums)
    if size <= 1 {
        return false
    }

    for i:=0;i<size;i++{
        if nums[i] == 0{
            continue
        }
        direction := nums[i] > 0
        curIndex := i
        curVal := nums[i]
        counter := 1
        for true{
            curIndex = curIndex + curVal
            if curIndex >= size {
                curIndex = curIndex - size
            } else if curIndex < 0 {
                curIndex = curIndex + size
            }
            curVal = nums[curIndex]
            //traveled
            if nums[curIndex] == 0 {
                break
            }
            nums[curIndex] = 0
            //loop detected
            if curIndex == i &&  counter > 1{
                return true
            }
            // direction changed
            if !((direction && curVal >0) || (!direction && curVal < 0)){
                break
            }
            counter++
        }
    }
    
    return false
} 
```

## Review

��������[Microservices Are Something You Grow Into, Not Begin With](https://nickjanetakis.com/blog/microservices-are-something-you-grow-into-not-begin-with)  
�������΢����ܻ𣬱��ĸ��������߶�ʮһ����΢�����������һ����ˮ��΢������Ϊһ�ּܹ�������ʹ�ó����ģ����һ������΢�����ǻ���Լ���ҵ�����������鷳��

1. ��������д��n���ظ������
2. ʹ��΢���񣬵����ֵ���Ӧ�ó��ܲ�סʱ

## Tips

#### ժҪ

��windows �б༭���ļ��ϴ��� Linux ��ʹ�� curl �ȹ��ߵ���ʱ�ᱨһ��`curl: (3) Illegal characters found in URL` �Ĵ���������Ϊ Linux �� Windows ���ı��ļ������ӵĻ��з���һ����Linux ��ÿ��ֻ������һ��`\n`,Windowsϵͳ����ÿ�к����`\n\r`, ������ Windows �µ��ļ��ŵ� Linux��ʱ�ͻ��������⡣

#### ����취

1. ת���ļ�

```
tr -d '\r' < test.sh > testWithoutR.sh
```

2. �ű���ִ��ʱ

��`\r` ɾ��
```
URL=${URL%$'\r'}
```

## Share

��Դ��������������ĸ�����֮���ì��

����һƪ����[How to Manage Connections Efficiently in Postgres, or Any Database](https://brandur.org/postgres-connections), ������������ι���Postgres���ݿ����ӣ��о��˼��ַ�ʽ��

1. ÿ������һ������
2. ���Ӹ���
    1. ���ӳ�
    2. ���ӳع�������

���ӳ�������������ֱַ���`Session pooling`,`Transaction pooling`,`Statement pooling`,����һ�㳣�õ����������ӳأ��ʼ��ÿ���������д���ά�������ݿ�����ӳأ������ڹ��̲������֮�󣬲��𹤳���N��ÿ���������ӳص���������M��������������� N*M �ᳬ�����ݿ�����������������������������������Ҫһ���ܵ����ӳش��������̲���ֱ�����ӵ����ݿ��У������ӳع����������н����е������������������ݿ��С�

��ʵ�ܶ๤�ߵĳ��ֶ���������Դ��������������ĸ�����֮���ì������ģ�������������޵ģ������ǲ���Ҫ����ʹ�����ӳؼ�����ÿ�����ӽ�������һ�����ݿ����ӾͿ����ˣ����������� [C10K](http://www.kegel.com/c10k.html#top) ���⡣����ʹ�ø��Ӷȸ��͵��㷨���Ż����ֳ����µĲ����������϶������ڼ�����Դ�������Ե��µġ�

�������ì�����ǿ��õĹ����У�

- �㷨�Ż�
- �첽
- �ͽ�ԭ�����ᵽ���ı�Ե���㣩
- �أ�����أ����ӳصȣ�
- �ֲ���ԭ�������棬JIT�ȣ�