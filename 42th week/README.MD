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

本周文章[Microservices Are Something You Grow Into, Not Begin With](https://nickjanetakis.com/blog/microservices-are-something-you-grow-into-not-begin-with)  
鉴于最近微服务很火，本文给不管三七二十一就上微服务的人泼了一盆冷水，微服务作为一种架构是有其使用场景的，如果一昧迷信微服务，那会给自己的业务带来更多的麻烦。

1. 抽象，在你写了n次重复代码后
2. 使用微服务，当发现单体应用承受不住时

## Tips

#### 摘要

在windows 中编辑的文件上传到 Linux 后，使用 curl 等工具调用时会报一个`curl: (3) Illegal characters found in URL` 的错误，这是因为 Linux 与 Windows 在文本文件中添加的换行符不一样。Linux 在每行只会添加一个`\n`,Windows系统会在每行后加入`\n\r`, 所以在 Windows 下的文件放到 Linux上时就会出这个问题。

#### 解决办法

1. 转换文件

```
tr -d '\r' < test.sh > testWithoutR.sh
```

2. 脚本中执行时

将`\r` 删掉
```
URL=${URL%$'\r'}
```

## Share

资源的有限性与任务的复杂性之间的矛盾

看了一篇文章[How to Manage Connections Efficiently in Postgres, or Any Database](https://brandur.org/postgres-connections), 文章讨论了如何管理Postgres数据库连接，列举了几种方式：

1. 每个请求一个连接
2. 连接复用
    1. 连接池
    2. 连接池管理工具

连接池作者提出了三种分别是`Session pooling`,`Transaction pooling`,`Statement pooling`,我们一般常用的是事务连接池，最开始是每个工程自行创建维护与数据库的连接池，但是在工程部署多了之后，部署工程数N，每个工程连接池的连接数量M，所需的总连接数 N*M 会超过数据库所允许的最大连接数，这种情况下我们需要一个总的连接池代理，工程不再直接连接到数据库中，由连接池管理工具来承接所有的连接请求，再请求到数据库中。

其实很多工具的出现都是由于资源的有限性与任务的复杂性之间的矛盾引起的，如果连接是无限的，那我们不需要讨论使用连接池技术，每个连接进来保持一个数据库连接就可以了，还有著名的 [C10K](http://www.kegel.com/c10k.html#top) 问题。我们使用复杂度更低的算法，优化各种场景下的参数，本质上都是由于计算资源的有限性导致的。

对于这个矛盾我们可用的工具有：

- 算法优化
- 异步
- 就近原则（皓哥提到过的边缘计算）
- 池（对象池，连接池等）
- 局部性原理（缓存，JIT等）
