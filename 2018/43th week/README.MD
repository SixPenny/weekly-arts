## Algorithm
#### Description

Difficulty: Medium

In an array A of 0s and 1s, how many non-empty subarrays have sum S?

 

Example 1:

Input: A = [1,0,1,0,1], S = 2  
Output: 4  
Explanation:  
The 4 subarrays are bolded below:

[`1,0,1`,0,1]  
[`1,0,1,0`,1]  
[1,`0,1,0,1`]  
[1,0,`1,0,1`]  


#### Solution

����������������뵽����ʹ�û������ڣ�������������ָ���ҳ����д𰸡���������ܲ����š�

```go
func numSubarraysWithSum(A []int, S int) int {
    if len(A) == 0 || S < 0 {
        return 0
    }
    
    if S == 0 {
        return zero(A)
    } else {
        return notZero(A, S)
    }
}

func notZero(A []int, S int) int {
    left,right,total, sum := 0, 0, 0, 0
    for true {
        //find first window
        for ;right < len(A) && sum < S;right++ {
            sum += A[right]
        }
        if sum == S {
            total++
        }
        temp := right
        for ; right < len(A) && A[right] ==0;right++{
            total++
        }
        for left <=right && sum == S {
            if A[left] == 0 {
                total += right - temp + 1
            } else {
                sum -= 1
            }
            left++
        }
        if right >= len(A){
            return total
        }
    }
    return 0
}

func zero(A []int) int {
    left,right,total := -1, 0, 0
    for true {
        //first zero
        for ; right < len(A) && A[right] != 0;right++ {
            
        }
        left = right 
        // find sequential zero
        for ; right < len(A) && A[right] == 0; right++ {
            
        }
        total += addon(right - left)
        if right == len(A) {
            return total
        }
    }
    return 0
}

func addon(n int) int{
    total := 0
    for n > 0 {
        total += n
        n--
    }
    return total
}
```

�������ѵ�[����](https://leetcode.com/problems/binary-subarrays-with-sum/discuss/186647/Java-Clean-Solution-2-Sum-+-Prefix-Sum-Caching) ��ԭ������������˼򵥣�������㷨��������

## Review

[How to Configure Your iPhone to Work for You, Not Against You](https://betterhumans.coach.me/how-to-set-up-your-iphone-for-productivity-focus-and-your-own-longevity-bb27a68cc3d8)

�����о���һϵ�е��ֶ�������������ֻ�����ʱ��ڶ����������ֻ����һ�����������ߣ�����̫���������ղ�������������

## Technique

��ǰһֱ������ `Virtual Memory`,�����ܶ����ܿ�����ˣ��������ڿ�`Modern Operating System`, ����һ�仰��˵�����ˣ��ڴ�ժ������ҡ�

```
Virtual memory makes it possible to run programs larger than physical memory by placing them on the disk and using main memory as a kind of cache for the most heavily executed parts.
```

�����ڴ�ı��ʾ��ǽ��ڴ浱���˴��̵Ļ��棬��Ȼʵ���ֶκ� CPU �е� L1 L2 Cache ��ͬ�����Ǳ����ǲ���ġ�

## Share

���շ���һ�����Ծ���

���Բ��������ҹ�����Ҳ���Լ����Լ����������ܶ�ȥ������һ�����ʹ�˾������һ�£�Ϊ�Ժ��ѧϰ�빤���ṩ���顣

��˵һ�����Ըſ����˴�����ԭ������һ������ɣ���5�֣�������һ��manager �����˱����4�֡�����ְλ���� Go ����Ϊ�������ԣ����������Ҫ��ע�㷨���򵥵�java ֪ʶ��������������

��һλ�������������û˵������һ���㷨������������ row ��column�� ����һ������ʹ֮����������ʽ��
```
1   2   3   4   5  
14  15  16  17  6  
13  20  19  18  7  
12  11  10  9   8  
```

Ҳ����һ���������ڵĻ��;��� ��������ҿ������ǿ���ı�̻����������漰ʲô DP��Two Pointers �ȵı�̼��ɡ��ҵĽ�𷽷����ĸ��߽�(rowMax, columnMax, rowMin, ColumnMin) ��һ��������Ϊ������һ��ģʽ: ��i++,j++,��i--,j--��Ȼ��������ת���ı߽��������˵���ĸ�����д�Ĺ����вŷ��������м�����һ�Ǳ߽����̫�࣬�����жϺܸ��ӣ�����ֻ��һ��������Ҫ�ͱ߽硢��ǰλ��һ�������ô�ߣ�д�����܆��¡����Թ���ʾ���õķ���������һ���߽���󣬰��Ѿ��߹���λ�ü�¼����������ʹ��4������д�����������������������˵㽨����д�ĵ�ǰ���Ĺ�������һ�ʴ����ɡ�

�ڶ�λ�����������㷨��һ�Ǵ���һ�� `MaxStack` �࣬ʹ֧֮�� stack ����ط���(push,pop,top)�����һ�����֧��`popMax` �������ֵ,`peekMax` �鿴���ֵ������������ʾ�� Java ���е�Stack����ʵ�֡�����⿴��˵ʵ���е���ûʲô˼·���о�Ӧ��������һ����ٴ�һ�ݣ��������ʵ�������Ҫ��û��ͷ������������һ���򵥵Ķ�������������Ѱ���ֵ������֧�ֲ���ɾ��������ʱ�临�Ӷ� O(lgN)�� ���Թٿ��˾���ʵ��ûɶ���⣬����ֻ����Ϊ�������������һ��BST�е�С������ˣ���ʾ��һ����������ÿ��push��Ԫ�ص����ֵ������Һ������� ���һֱ `popMax` �Ļ���Ҫһֱ��¼pop���������������pop ��ʱ����Ҫȥ�ȶԣ��������ᵽO(N), ����������ֻ��˵������һ��ɭ�ֵ��⣬��һ����ά���飬�ж��ж��ٿŻ����ཻ������ʹ�� DFS ,BFS �����ԡ�

����λû�г��㷨��Ҫ�����һ���ֲ�ʽ���У���һ�������������⡣��Ҫ��Ʒ�����  

1.API���  
2.��Ⱥ����  
3.�ݴ����

�Ұ���ҵ��ķֲ�ʽ�������Լ����������һ���򵥵ķ�����Master-Taskģʽ��Master ���meta��Ϣ��Task�ڵ㣬consumer��producer�ȼ�¼������̨��Ⱥʹ��raft�㷨ѡ��master��Task�ڵ�ÿ����һ��Slave�ڵ������ݣ����Ҵ洢Queue��Ϣ��һ���֣�client �ȴ� Master�ڵ��ȡqueue��Ӧ��Task�ڵ㣬Ȼ���������е�Task�ڵ�������������Ϣ��������Լ���˼ά�Ǻܴ����ս���൱�ڴӿ����˵����ת��Ϊ������֪ʶ�Լ�������ƣ���һ���൱��Ȥ��˼ά������ʽ���Ժ�����Լ�����ʶʹ�á�

����λ���з��ܼ࣬��ע����ҵ����֯�ṹ���棬��ϧ�Ҷ���֯�ṹ֮��Ĳ�����Ȥ���Ӵ�����˵��˵��˵�Ҹ�����˵��һ��[facepalmed]��Ȼ�����һ������һ�������һ��sum���ҳ����е������ʹ�ú�Ϊsum������һ������̬�е����ͣ�������ô����ô�򵥵��⣬���ʹ����һ��O(2n)���Ӷȵ��㷨�ֱ�����һͨ������ȷʵһ�α����͹��ˣ�����̫�����㷨���ˣ����ܾ�����һ�ֽⷨ��Ҫ�����󾫰���

��ͷ��������ԣ��㷨�Ķ����Ǻ����洦�ģ������������ʱ���߿����и�����ֵĹ���ʹ�ã���Ҳ�����˺ܶ಻�㣬�ܶ�ʱ����̫�����ſ��ˣ�û�о������˼����Ҫ��һ�������γ��Լ���������õķ��������Լ�ȥʵ�����������Ȼ���ҵ�ڷ������ԱȲ��������Լ������ģ���������Ե�������ѭ���������ܳɳ�����