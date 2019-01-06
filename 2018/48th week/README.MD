## Algorithm
### Description

Tag: Greedy, Array  
Difficulty: Medium

Given two arrays A and B of equal size, the advantage of A with respect to B is the number of indices i for which A[i] > B[i].

Return any permutation of A that maximizes its advantage with respect to B.

 

Example 1:
```
Input: A = [2,7,11,15], B = [1,10,4,11]
Output: [2,11,7,15]
```
Example 2:
```
Input: A = [12,24,8,32], B = [13,25,32,11]
Output: [24,32,8,12]
 ```

Note:
```
1 <= A.length = B.length <= 10000
0 <= A[i] <= 10^9
0 <= B[i] <= 10^9
```

### Solution

ʹ��̰���㷨����B�е�ÿһ��Ԫ���ҵ���A�е�һ���������Ԫ�أ�Ȼ�󷵻أ���������ھͷ���A����С��Ԫ��

```go
func advantageCount(A []int, B []int) []int {
    sort.Ints(A)
    result := make([]int, len(A))
    for i:=0 ;i < len(B); i++ {
        result[i], A = findAndDelete(A, B[i])
    }
    
    return result
}

func findAndDelete(A []int, b int)(int, []int){
    i := 0 
    for ; i< len(A); i++ {
        if A[i] > b {
            break
        }
    }
    
    if i == len(A) {
        return A[0], A[1:]
    }
    result := A[i]
	return result, append(A[0:i], A[i+1:]...)
}
```

�����㷨�ǳ���ʱ����ȻAccepted�ˣ�ÿ��append ����һ��O(N) �Ĳ������д�������ʹ�����ȶ��У�ʵ�ַǳ����ţ�����discuss��ַ[JAVA Greedy 6 lines with Explanation](https://leetcode.com/problems/advantage-shuffle/discuss/149822/JAVA-Greedy-6-lines-with-Explanation)

```Java 
class Solution {
    public int[] advantageCount(int[] A, int[] B) {
        Arrays.sort(A);
        int n=A.length;
        int[] res= new int[n];
        PriorityQueue<int[]> pq= new PriorityQueue<>((a,b)->b[0]-a[0]);
        for (int i=0; i<n; i++) pq.add(new int[]{B[i], i});
        int lo=0, hi=n-1;
        while(!pq.isEmpty()){
            int[] cur= pq.poll();
            int idx=cur[1], val=cur[0];
            if (A[hi]>val) res[idx]=A[hi--];
            else res[idx]=A[lo++];
        }
        return res;
    }  
}

```

![](https://s3-lc-upload.s3.amazonaws.com/users/caraxin/image_1531625378.png)

## Review

[Teach Yourself Programming in Ten Years](http://norvig.com/21-days.html)

ѧϰһ�����Խ���ѧϰ�����﷨�ǲ����ģ������ľ�����һ�����Ա�������˼�롣A language that doesn't affect the way you think about programming, is not worth knowing. �����ϵĺܶ�21Сʱ��21���鼮�����������������ǣ������ı�������Ҫ���������ڿҿҵ�Ͷ��10����ܻ�õġ�

## Tips

Visitor Pattern

### ժҪ

���ģʽ�Ƕ����ԭ��ľ���ʵ�����ڱ������������Ҫ�μ����ԭ�򣬸��ݵ�ǰ�������ѡ������Ҫʹ�õ����ģʽ��Visitor Pattern ��һ�������õ�ģʽ�����ҿ�����visitor pattern Ҳ��������������һ���漼�����ˡ�

### what

ʲô��visitorģʽ����Wikipedia �ϵĶ���Ϊ��`In object-oriented programming and software engineering, the visitor design pattern is a way of separating an algorithm from an object structure on which it operates.`. �ǽ��㷨����ṹ�ֿ�����ôһ��ģʽ�����������������еġ�

### When

������ʲô�����²ſ���ʹ�����ģʽ�أ������¼���������Ҫ���㣺
- ��һ����ǰ����õ���(��֪)
- �����ϵĲ�����δ֪��
- ������Ҫ���ݲ�ͬ����������ͬ�Ĳ����������Ҫʹ��(instanceof)�ж�

�������Ƕ����е�algorithm����ǰ����õ������object structure��Ҳ����˵���ڲ���δ֪�����Խ�ԭ���������еĲ����ó������ŵ� visitor �С�

### Why

��ʵ������Ķ����ǲ��Ǹо� visitor pattern Υ���˽��౾����ְ��������������ԭ���أ����ҿ����ǵģ���Ϊ�γ��������ַ�ԭ���ģʽ�����ö���֮�ĳ�Ϊ��24��ģʽ֮һ�أ���ʵ������Ϊ�������������⵼�µġ�visitor pattern �ĵײ㱾���� `double dispatching`, ����Java ��������ֻ֧��`single dispatching`����Ҫʵ�� `double dispatching` ��ô���أ���ֻ��ʹ�� visitor pattern���ֱ�׾��ģʽ�ˡ�

### How

�������������ֶ����������:
```java
public abstract class Animal{}
public class Dog extends Animal{}
public class Cat extends Animal{}
```

������Ҫ����һ�������ķ���������è���Ľ�����һ����������Ҫ��3���඼�޸�һ�¡�
```java
public abstract class Animal {
    public abstract void makeSound();
}
public class Dog extends Animal{
    public void makeSound() {
        System.out.println("wang wang");
    }
}
public class Cat extends Animal{
    public void makeSound(){
        System.out.println("miao miao");
    }
}
```
�����������Ƿ��ֻ���Ҫ����һ��`eat` �ķ���������������Ҫͬ���޸ģ�Υ����OCPԭ����ʱ���ǾͿ���ʹ��Visitor Pattern�����仯��ȡ����, Animal�������ౣ�ֲ�����
```
public abstract class Animal{}
public class Dog extends Animal{}
public class Cat extends Animal{}
public abstract class AnimalVisitor {
    public abstract void visit(Animal animal);
    public abstract void visit(Dog dog);
    public abstract void visit(Cat cat);
}
public class MakeSoundVisitor extends AnimalVisitor{
    public void visit(Animal animal){
        System.out.println("animal sound")
    }
    public void visit(Dog dog){
        System.out.println("wang wang");
    }
    public void visit(Cat cat) {
        System.out.println("miao miao");
    }
}
public class EatVisitor extends AnimalVisitor {
    public void visit(Animal animal){
        System.out.println("animal eat");
    }
    public void visit(Dog dog){
        System.out.println("eat dog food");
    }
    public void visit(Cat cat){
        System.out.println("eat cat food");
    }
}
```
�����ã�����ÿ����ʲô������Ҫ���ӵ�ʱ�������һ���̳���`AnimalVisitor`���࣬����������������Ϊ�����ǿ�������ʹ��
```java
Dog dog = new Dog();
Cat cat = new Cat();

EatVisitor eat = new EatVisitor();
eat.visit(dog);
eat.visit(cat);
```
�������һ�����⣬����ֻ��ʹ�þ����`Dog` `Cat` �࣬������ʹ�ø���`Animal`��
```java
Animal dog = new Dog();
eat.visit(dog);//animal eat
```
����dog �� `Dog`���ͣ�����ȴ����`Animal`�ϣ�������ΪJava���߱�������ʱ����������ͬ�ĺ������ã�Ҳ����������˵�Ĳ�֧��`double dispatching`���ŵ����������Ľ����

��ν����������أ�Java��֧�ַ����Ķ�̬���õ�(`single dispatching`)�����ǿ��Ը�����������ʵ��`double dispatching`,Ҳ������Animal ������һ��ת����
```java
public abstract class Animal{
    public void visit(AnimalVisitor visitor){
        visitor.visit(this);
    }
}
public class Dog extends Animal{
    public void visit(AnimalVisitor visitor){
        visitor.visit(this);
    }
}
public class Cat extends Animal{
    public void visit(AnimalVisitor visitor){
        visitor.visit(this);
    }
}
```
> �ܶ�visitor ģʽ�����ӶԲ�ͬ����ʹ���˲�ͬ�ķ�����������������ÿ�������еĴ����븸���еĴ���һ��������this�ĺ����ǲ�һ���ġ�

ʹ��ʱ���Ǿ���Ҫ��Visitor ���� Animal���Animal ����Visitor�ˡ�
```
EatVisitor eat = new EatVisitor();
Animal dog = new Dog();
dog.visit(eat);
```

### compare

Visitor Patter �������е�ʵ��ȫ���õ���Visitor�������������Ϥ����ʽ��̵��˾ͻ�������ģʽ�����졣��ʵ����Ǻ���ʽ�����ǿ���ģʽƥ���һ���֣����ݲ�ͬ������ѡ��Ĳ�ͬ�ĺ������ѡ�

### summary

Visitor ģʽʹ�õĺ�ϡ�٣��ܴ�һ��ԭ������������̫���̣���Ҫ��visit���������ȶ��ģ�������������Ķ�֪�����ǲ��ϱ仯�ġ�����Ҳ�кܶ���˵visitor ģʽ��һ����ģʽ����Ӧ��ʹ�á����Ǳ�֤�����������Ͼ��������������Ƶ����������Ĳ�����ѡ��

### reference

- [visitor pattern](https://en.wikipedia.org/wiki/Visitor_pattern)
- [A little Java A little pattern](http://liuchengxu.org/books/src/Misc/A-Little-Java-A-Few-Patterns.pdf)
- [Double Dispatch](https://en.wikipedia.org/wiki/Double_dispatch)
- [���ܡ����ģʽ��](http://www.yinwang.org/blog-cn/2013/03/07/design-patterns)

## Share

### �ۼ�ͥ������߶���

������˵������ı����ǡ����߶����ǲ���һ���ӣ�����������������˼����������һֱ����һ���۵�ģ��뿪�˼���̸���ԣ��뿪����̸�۵㶼��ˣ��å������˵���������С��Ƭ��һ���龰�����������Ⲣ���Ǳ�����Ҫ̸�۵ĳ�����Ҳ�����ҽ���Ҫ̸�۵ĳ�����

������극������ˮ�ر�ˢ��ϴ�����뵽��ͥ��������Ǻܺõ�һ���㡣��һ����ͥ��ǲ���Ӧ�����߶����أ�

�ܶ���������ϲ�������񣬵�Ȼ�ܶ�Ů��Ҳ�ǣ������ɷ����ڼ�ͥ��ϵ��Ů���е��ļ�ͥ��������ԱȽ϶�ġ��������Լ�����û��������������������ɣ���������Ū������Ҫ�õ�ʱ����ϴ������Ҫ�õ�ʱ����ˢ�Ϳ����ˡ����������һ�����⣬�������������OK�������������������ư������Ҫʹ�õ�ʱ���������������ϴ���ˡ�ʵ�����ǽ��Լ��ĳе��Ĺ�������Ƴ�����Ƶ�����һ�������ϡ�

������Ӧ�úܳ�������������ϴ��ˢ�����⣬Ҳ�������������Ҿ������극�����ǲ��붯��ֻ�����ֻ��ˣ��������������ʱ���غ���΢�ڿ�һ��������ʰ����������ٳ����������;���ʰ�����ġ����Ǿ����������������һ�����´�����ǰ���ڼң��Ұ�����ˢ�ɾ���Ҳ��ȽϷѾ�����Ϊ��Щʣ�˸���������ϣ�ϴ�ɾ���Ҫ��һ�㹤�򣻵ڶ������������ʱ�Ҳ��ڼң��Ǿ��Ǳ��������ϴ�̸ɾ���������������������ʰ��ȥ�ˣ���ȥ�����ｫ���еĶ���ϴ�̳�����

��һ��������ö�˵���Լ��Ļ��Լ����ˣ��ڶ��������ÿ�λص��ҳԷ���������Լ�ˢ����û��ˢ�������Լ�ϴ�Ĺ�û��ϴ���ͻ����´�һ��Ҫ�Լ���������ɣ������������ʵ������������������ס�ģ��Լ���Է������֣�����⽨ʱ�ڵ���̫ү�ˣ������Ҿͻ�ȥ�������������Լ�����ˢ����

����û������һ���������Է������»��������������Ϊ�����ƽ����ͬѧ�����Ц���Լ�Ϊ�ҵġ�ϴ��澡�����Ϊ���Ͼ�������ϴ��ˮ��������ϴ�ŵģ���Ȼ��Ҳ���ϴ��ˮ������ϴ�ţ��ⶼ��ƽ���¡�����ʲô����Ը��ȥ��������أ�����Ϊ��ѽ�����ǰ��Ż�����Ϊ��ϴ��������Ҳ����Ϊ���Ż����������ʱ����������æ�Ż��ȥ˵����ˢ�ɡ������ڼ�ͥ���棬�������߶��ͣ����ǰ��߶��͡�

�����໥�ĸ�����ֻ��ȡ�����������������粻�ǰ��顣