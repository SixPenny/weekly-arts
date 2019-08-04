## Algorithm

[46. Permutations](https://github.com/SixPenny/leetcode/blob/master/problems/46.%20Permutations.md)

[48. Rotate Image](https://github.com/SixPenny/leetcode/blob/master/problems/48.%20Rotate%20Image.md)

[415. Add Strings](https://github.com/SixPenny/leetcode/blob/master/problems/415.%20Add%20Strings.md)

## Review

[The Ultimate Strategy to Preparing for the Coding Interview](https://medium.com/@aahmad_49388/the-ultimate-strategy-to-preparing-for-the-coding-interview-ee9f7eb439f3)

作者提出了几个解决算法问题的模式，以前自己也总结过，不过不如作者总结的全面。

1. 如果输入是有序的，可以使用二分查找（及其变种）或two-pointer 算法（数组类问题，无序数组是否可以先排序？）
2. 如果 top/max/min/closest k 元素类问题，可以使用堆
3. 如果我们需要输入的所有组合，回溯和广度优先遍历是合适的算法
4. 大部分涉及到数或图的题目，深度优先遍历或广度优先遍历是不三法门
5. 所有的递归可以通过 stack 转换成迭代
6. 如果一个问题求的事最优解，可以考虑动态规划
7. 在一堆字符串中查找，可以使用 Trie 数据结构
8. 涉及到 LinkedList，并且不能使用额外空间，可以考虑 fast-slow 指针

## Tips

应用在重新启动后会丢失以前的 JIT 等编译信息，因此应用往往在最开始的时候需要预热后才能达到最佳性能，阿里发布的 AJDK 中包含了一个 JWarmup 工具，可以保存上一次编译过的信息，使得应用在启动时就完成预热，非常好的工具。

## Share

[设计模式之简单工厂模式](https://my.oschina.net/liufq/blog/3083093)
