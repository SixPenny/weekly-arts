## Algorithm

### Description 

tags : `Divide And Conquer` `Dynamic Programming` `Array`

Difficulties ： `easy`

```

Given an integer array nums, find the contiguous subarray (containing at least one number) which has the largest sum and return its sum.

Example:

Input: [-2,1,-3,4,-1,2,1,-5,4],
Output: 6
Explanation: [4,-1,2,1] has the largest sum = 6.
Follow up:

If you have figured out the O(n) solution, try coding another solution using the divide and conquer approach, which is more subtle.
```

### Solution

#### 穷举法

这道题标为简单的原因应该就是使用穷举法是可以Accepted的，我们将所有的连续子串穷举之后取最大值就可以了。需要两个for循环，时间复杂度O(n^2), 空间复杂度O(1)

#### DP + 贪婪

这个解法是由Jon Bentley (Sep. 1984 Vol. 27 No. 9 Communications of the ACM P885) 给出的，下面摘录一下算法解释：

```
algorithm that operates on arrays: it starts at the left end (element A[1]) and scans through to the right end (element A[n]), keeping track of the maximum sum subvector seen so far. The maximum is initially A[0]. Suppose we've solved the problem for A[1 .. i - 1]; how can we extend that to A[1 .. i]? The maximum
sum in the first I elements is either the maximum sum in the first i - 1 elements (which we'll call MaxSoFar), or it is that of a subvector that ends in position i (which we'll call MaxEndingHere).

MaxEndingHere is either A[i] plus the previous MaxEndingHere, or just A[i], whichever is larger.
```

也就是说这是一个动态规划 + 贪婪的算法，时间复杂度只需要 O(n),空间复杂度为 O(1),算得上是完美解法了

```go
func maxSubArray(nums []int) int {
    if len(nums) == 0 {
        return 0
    }
    
    maxSoFar := nums[0]
    maxEndHere := nums[0]
    
    for i:= 1; i< len(nums); i++ {
        maxEndHere = int( math.Max(float64(maxEndHere + nums[i]), float64(nums[i])))
        maxSoFar = int(math.Max(float64(maxSoFar), float64(maxEndHere)))
    }
    
    return maxSoFar
}
```

#### Divide And Conquer

题目给出了要求使用分治法，我们来分析一下如何使用分治法

对于整形数组 `nums`, 分成左右两部分 `numsLeft` `numsRight`, 然后分别进行最大子串和的计算，当只有一个元素时，我们就返回这个元素，关键问题来了，如何合并两部分来得到最终的结果呢?（divide and conquer 里最难就是conquer部分了）

这里我先去看了一下 [divide and conquer 的 Wikipedia](https://en.wikipedia.org/wiki/Divide_and_conquer_algorithm) 字条，里面列出了很多使用分治法的经典算法案例，这个题目与 [Closest Pair Of Points problem](https://en.wikipedia.org/wiki/Closest_pair_of_points_problem) 是很相似的。

两个子串的 conquer 需要的条件有：

- 左侧和右侧都需要有元素参数
- 从两个子串的中心向左向右得出一个最大元素和m

左侧、右侧与m的最大值就是原数组的最大子串和。

分治法的复杂度为 O(nlogn),并不能算是最优解。

```go

func maxSubArray(nums []int) int {
	if len(nums) == 0 {
		return 0
	}
	if len(nums) == 1 {
		return nums[0]
	}

	return divideAndConquer(nums, 0, len(nums))
}

func divideAndConquer(nums []int, from, end int) int {
	if end-from == 1 {
		return nums[from]
	}
	middle := from + (end-from)/2

	maxLeft := divideAndConquer(nums, from, middle)
	maxRight := divideAndConquer(nums, middle, end)

	continuousMax := continuousMaxLeft(nums, from, middle) + continuousMaxRight(nums, middle, end)

	return max(max(maxLeft, maxRight), continuousMax)
}

func continuousMaxLeft(nums []int, from, end int) int {
	m := nums[end-1]
	cm := m
	for i := end - 2; i >= from; i-- {
		cm = cm+nums[i]
		m = max(m, cm)
	}
	return m;
}

func continuousMaxRight(nums []int, from, end int) int {
	m := nums[from]
	cm := m
	for i := from + 1; i < end; i++ {
		cm = cm+nums[i]
		m = max(m, cm)
	}
	return m
}
func max(x, y int) int {
	if x > y {
		return x
	}
	return y
}

```


## Review

本周读了[divide and conquer algorithm](https://en.wikipedia.org/wiki/Divide_and_conquer_algorithm)

1. divide and conquer 的定义是什么？

divide and conquer 是一个基于多路递归的算法设计范式，通过将问题分解成多个相同或相关子问题，知道问题小到可以直接给出答案，然后将各个小问题的答案组合起来得到原问题的解。

2. divide and conquer 有哪些算法应用？

- 排序 (quicksort, mergesort)
- 大数乘法
- 平面中距离最近的两个点
- 语法分析
- 离散傅里叶变换

等等

3. divide and conquer 有哪些优势？

- 解决困难问题的能力
- 提升算法效率
- 支持并行
- 内存访问友好(符合局部性原理)
- 舍入控制(计算浮点数相关)

## Technique

#### 摘要

Go 提供了两个关键字`new` 和 `make` 来为类型分配内存，但两者的功能是不同的，一句话来说就是：new只管 内存分配，而make用来初始化slice，map 和 channel


#### new

原型：`func new(Type) *Type`

Go 官方文档上的解释：

```
The new built-in function allocates memory. The first argument is a type,
not a value, and the value returned is a pointer to a newly
allocated zero value of that type.
```

这里我们关注到一点 `newly allocated zero value of that type`, 也就是说new 分配内存之后，会返回对应类型为零值的指针，它的参数是一个类型，这个类型在这里是一个占位符，可以看到它的定义为`type Type int` ， 我们在应用程序里是获取不到的（一直在这有疑惑，Go 的包规范要求首字母大写的是public的，小写的是private的，但是在builtin 包中，小写的可以在程序中使用，大写的反而是不可用的）。

#### make 

原型：`func make(t Type, size ...IntegerType) Type`

Go 官方文档解释：
```
The make built-in function allocates and initializes an object of type
slice, map, or chan (only). Like new, the first argument is a type, not a
value. Unlike new, make's return type is the same as the type of its
argument, not a pointer to it. The specification of the result depends on
the type:
```

make 只能用来创建 slice，map 和channel，并不能用于其他类型，当然我们可以看到 make 和 new 的函数签名是类似的，但是当使用 int 等类型作为 make 参数时，编译会报错。

make还可以接收整形参数来决定初始容量，slice 第一个参数代表len，第二个代表 capacity，map 只能有一个参数代表初始容量，channel 的 int 参数代表 buffer 大小（没有代表无缓冲）

为什么需要用 make 来创建这三个类型？因为这三个类型代表的是对数据结构的引用，要使用它们，必须要先初始化。以slice 为例，一个 slice 需要3 个元素：指向数据的指针，长度（len）和容量（cap）(有疑问的可以参见 [go slice usage](https://blog.golang.org/go-slices-usage-and-internals)), 在未初始化之前slice 为nil，是不可用的。也就是说 对slice、map、channel 三种类型，make初始化了它们的内部数据结构，使得它们立即可用。

#### 对比

`new` 对于接收的参数类型是没有限制的，也即我们可以传slice、map、channel类型，表面上看new 的存在就够了，但是，我们要知道，这个new 出来的slice、map、channel 为nil，实际上没有用处，对于接口也一样，new 可以接收，但返回 nil，在程序中是不可用的。

#### 为何要有两个函数

这个问题在网上也有很多讨论，问题的真正答案只有 Go 语言的发明人才能给出。我们这里只作猜测。

与这个问题对等的是如果只有一个分配函数会怎么样？

假设我们的新分配函数名字叫`alloc`, 参数只能与 make 一样，因为我们既需要能处理 int 这种不需要长度的类型，也需要处理 slice 这种需要长度的类型，然后返回类型为指针（只能有一种返回类型，分配到堆上返回指针），我们来看一下它的使用问题。

1. 参数冗余

`size ...IntegerType` 参数在为基本类型时是多余的，就算传递了，也是不用的，这是一种设计缺陷。按照我们在其他编程语言里的编程实践，遇到这种问题时，我们会使用方法重载，但 Go 中不支持函数重载，此路不通。

2. 返回类型为指针，slice等使用麻烦

使用 make 返回的是类型本身，如果返回的是指针的话，我们使用起来就需要多加一层括号，获取 slice 中的某一个元素需要这样使用`(*slice)[i]`, map 也是一样，会引起不必要的解引用


从语言设计者的角度来看，设计一个函数槽点太多，索性分开为两个，权责明确，使用方便，而且这不又给了很多人写博客的一个话题嘛。


## Share 

这周经历了一个数据问题，告诫大家以后操作数据库一定要先备份！

下了班回家组长打电话来说写到另一个系统的流程里有报错，给了我一个id查，我查了一下发现是以前的数据，这个数据格式在期间更改过一次，代码逻辑也变化为使用更改的数据格式，然后这个数据就在那一行代码报错空指针。

有两个方案，一种就是修改代码兼容以前的逻辑，还有一种是修改数据库数据把以前的老数据调整为新格式。操作数据库看起来简单方便，无需上线，我就选了这一种，然后着急忙慌的把 sql 写出来，然后直接在线上执行，回头一看原来id对应的数据，完了，对应的值全部变成了0。

看一下自己写的 sql， 那个字段类型为 varchar, 我用加号将这个数据和其他字符串拼接起来，可是 MySQL 中对字符串的拼接用加号是不行的！必须要用 `concat` 函数，加号只能用来做算数操作，好久不操作数据库把编程语言和数据库弄混了，这下完蛋了，心里想难道要上演一部现实版的从删库到跑路？

心中不断告诫自己要冷静，数据没了，只能想办法从其他层面做弥补了。这里要感觉我们的另一个团队，他们提供的接口采用的策略是局部更新，对于为null的列并不会做改动，我赶紧在代码里加了一个为零的判断，发布到线上，终于算是把这个问题解决了。

对数据的操作要慎之又慎，特别是对大批量数据的更新删除操作，这里我总结了几点希望自己以后碰上问题的时候时刻注意。

1. sql 写完需要先在测试环境中跑一下
2. 将影响到的表数据操作之前做好备份
3. 在评估方案时，优先选择能用代码解决的方式
