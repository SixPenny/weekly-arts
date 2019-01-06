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

使用贪婪算法，对B中的每一个元素找到在A中第一个比它大的元素，然后返回，如果不存在就返回A中最小的元素

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

这种算法非常耗时，虽然Accepted了，每次append 都是一次O(N) 的操作。有大神网友使用优先队列，实现非常优雅，具体discuss地址[JAVA Greedy 6 lines with Explanation](https://leetcode.com/problems/advantage-shuffle/discuss/149822/JAVA-Greedy-6-lines-with-Explanation)

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

学习一门语言仅仅学习它的语法是不够的，真正的精髓是一门语言背后的设计思想。A language that doesn't affect the way you think about programming, is not worth knowing. 市面上的很多21小时、21天书籍都是在向你推销焦虑，真正的本事所需要的是你勤勤恳恳的投入10年才能获得的。

## Tips

Visitor Pattern

### 摘要

设计模式是对设计原则的具体实践，在编码过程中我们要牢记设计原则，根据当前需求灵活选用我们要使用的设计模式。Visitor Pattern 是一个不常用的模式，在我看来，visitor pattern 也算是面向对象里的一种奇技淫巧了。

### what

什么是visitor模式？从Wikipedia 上的定义为：`In object-oriented programming and software engineering, the visitor design pattern is a way of separating an algorithm from an object structure on which it operates.`. 是将算法与类结构分开的这么一种模式，而且是面向对象独有的。

### When

我们在什么条件下才可以使用这个模式呢？有以下几个方面需要满足：
- 有一组提前定义好的类(已知)
- 对象上的操作是未知的
- 对象需要根据不同的类做出不同的操作，因此需要使用(instanceof)判断

操作就是定义中的algorithm，提前定义好的类就是object structure。也就是说由于操作未知，所以将原本属于类中的操作拿出来，放到 visitor 中。

### Why

其实按上面的定义是不是感觉 visitor pattern 违反了将类本身的职责放在类中这个简单原则呢？在我看来是的，那为何出现了这种反原则的模式并且堂而皇之的成为了24种模式之一呢？其实这是因为语言自身的问题导致的。visitor pattern 的底层本质是 `double dispatching`, 而像Java 这种语言只支持`single dispatching`，而要实现 `double dispatching` 怎么办呢，就只能使用 visitor pattern这种笨拙的模式了。

### How

现在我们有两种动物，定义如下:
```java
public abstract class Animal{}
public class Dog extends Animal{}
public class Cat extends Animal{}
```

我们需要增加一个叫声的方法，由于猫狗的叫声不一样，我们需要将3个类都修改一下。
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
过了两天我们发现还需要增加一个`eat` 的方法，三个类又需要同步修改，违反了OCP原则，这时我们就可以使用Visitor Pattern，将变化提取出来, Animal的三个类保持不动。
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
很美好，我们每当有什么操作需要添加的时候就新增一个继承了`AnimalVisitor`的类，由它来定义具体的行为。我们可以这样使用
```java
Dog dog = new Dog();
Cat cat = new Cat();

EatVisitor eat = new EatVisitor();
eat.visit(dog);
eat.visit(cat);
```
但这存在一个问题，我们只能使用具体的`Dog` `Cat` 类，而不能使用父类`Animal`：
```java
Animal dog = new Dog();
eat.visit(dog);//animal eat
```
明明dog 是 `Dog`类型，调用却到了`Animal`上，就是因为Java不具备按运行时类型来做不同的函数调用，也就是上面所说的不支持`double dispatching`，才导致了这样的结果。

如何解决这个问题呢？Java是支持方法的动态调用的(`single dispatching`)，我们可以根据这个来间接实现`double dispatching`,也就是由Animal 类族做一次转发。
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
> 很多visitor 模式的例子对不同的类使用了不同的方法名，这里我们在每个子类中的代码与父类中的代码一样，但是this的含义是不一样的。

使用时我们就需要由Visitor 调用 Animal变成Animal 调用Visitor了。
```
EatVisitor eat = new EatVisitor();
Animal dog = new Dog();
dog.visit(eat);
```

### compare

Visitor Patter 将子类中的实现全部拿到了Visitor中来做，如果熟悉函数式编程的人就会觉得这个模式很面熟。其实这就是函数式编程中强大的模式匹配的一部分，根据不同的子类选择的不同的函数而已。

### summary

Visitor 模式使用的很稀少，很大一个原因是它的条件太苛刻，它要求被visit的类族是稳定的，但是做过需求的都知道类是不断变化的。网上也有很多人说visitor 模式是一个反模式，不应该使用。我们辩证地来看待，毕竟这是在语言限制的条件做出的不得已选择。

### reference

- [visitor pattern](https://en.wikipedia.org/wiki/Visitor_pattern)
- [A little Java A little pattern](http://liuchengxu.org/books/src/Misc/A-Little-Java-A-Few-Patterns.pdf)
- [Double Dispatch](https://en.wikipedia.org/wiki/Double_dispatch)
- [解密“设计模式”](http://www.yinwang.org/blog-cn/2013/03/07/design-patterns)

## Share

### 论家庭里的能者多劳

看奇葩说，今天的辩题是“能者多劳是不是一个坑？”。这个辩题很有意思，不过呢我一直都是一个观点的：离开了剂量谈毒性，离开场景谈观点都是耍流氓。奇葩说引出辩题的小短片是一个情景，但很明显这并不是辩手们要谈论的场景，也不是我今天要谈论的场景。

中午吃完饭后我在水池边刷碗洗锅，想到家庭这个场景是很好的一个点。在一个家庭里，是不是应该能者多劳呢？

很多男生都不喜欢做家务，当然很多女生也是，但不可否认在家庭关系里女生承担的家庭事务是相对比较多的。对于我自己，我没有其他不想做家务的理由，就是懒得弄，锅需要用的时候再洗，碗需要用的时候再刷就可以了。这会衍生出一个问题，饭如果是你做还OK，如果不是你做，锅碗瓢盆在需要使用的时候就是做饭的人来洗涮了。实际上是将自己的承担的工作向后推迟最后推到了另一个人身上。

这种事应该很常见，不仅仅是洗碗刷锅问题，也包括其他家务。我经常吃完饭就坐那不想动，只想玩手机了（彬儿做饭），有时候呢很稍微勤快一点把碗筷收拾到厨房里，甚少吃完立马将餐具收拾出来的。于是就衍生出三种情况，一种是下次做饭前我在家，我把碗筷锅刷干净，也会比较费劲，因为有些剩菜干在了碗壁上，洗干净需要费一点工夫；第二种情况是做饭时我不在家，那就是彬儿将它们洗涮干净；第三种情况就是碗筷收拾下去了，她去厨房里将所有的东西洗涮出来。

第一种情况不用多说，自己的活自己干了；第二种情况下每次回到家吃饭想起该由自己刷的碗没有刷，该由自己洗的锅没有洗，就会想下次一定要自己把事情完成；第三种情况下实际上我在屋里是坐不住的，自己光吃饭不动手，很像封建时期的老太爷了，所以我就会去厨房里让她起开自己把碗刷掉。

看到没，这是一对正常爱对方的情侣会产生的情绪和行为。彬儿平常跟同学聊天会笑称自己为我的“洗脚婢”，因为晚上经常她端洗脚水来督促我洗脚的，当然我也会端洗脚水来让她洗脚，这都是平常事。但是什么让你愿意去做这件事呢，是因为爱呀。正是爱才会让她为你洗衣做饭，也是因为爱才会让我想玩的时候听到她在忙才会过去说我来刷吧。所以在家庭里面，不是能者多劳，而是爱者多劳。

爱是相互的付出，只索取不付出的土财主作风不是爱情。