## Algorithm

#### Description

tags: backtrack,hash table
difficulty: hard

```
Write a program to solve a Sudoku puzzle by filling the empty cells.

A sudoku solution must satisfy all of the following rules:

Each of the digits 1-9 must occur exactly once in each row.
Each of the digits 1-9 must occur exactly once in each column.
Each of the the digits 1-9 must occur exactly once in each of the 9 3x3 sub-boxes of the grid.
Empty cells are indicated by the character '.'.

```

![](https://upload.wikimedia.org/wikipedia/commons/thumb/f/ff/Sudoku-by-L2G-20050714.svg/250px-Sudoku-by-L2G-20050714.svg.png)
```
A sudoku puzzle...


...and its solution numbers marked in red.
```
![](https://upload.wikimedia.org/wikipedia/commons/thumb/3/31/Sudoku-by-L2G-20050714_solution.svg/250px-Sudoku-by-L2G-20050714_solution.svg.png)
```
Note:

The given board contain only digits 1-9 and the character '.'.
You may assume that the given Sudoku puzzle will have a single unique solution.
The given board size is always 9x9.
```

#### Solution

```go
package main

import "fmt"

func main() {
	board := [][]byte{{'5','3','.','.','7','.','.','.','.'},{'6','.','.','1','9','5','.','.','.'},{'.','9','8','.','.','.','.','6','.'},{'8','.','.','.','6','.','.','.','3'},{'4','.','.','8','.','3','.','.','1'},{'7','.','.','.','2','.','.','.','6'},{'.','6','.','.','.','.','2','8','.'},{'.','.','.','4','1','9','.','.','5'},{'.','.','.','.','8','.','.','7','9'}}
	solvedBoard := [][]byte{{'5','3','4','6','7','8','9','1','2'},{'6','7','2','1','9','5','3','4','8'},{'1','9','8','3','4','2','5','6','7'},{'8','5','9','7','6','1','4','2','3'},{'4','2','6','8','5','3','7','9','1'},{'7','1','3','9','2','4','8','5','6'},{'9','6','1','5','3','7','2','8','4'},{'2','8','7','4','1','9','6','3','5'},{'3','4','5','2','8','6','1','7','9'}}
	fmt.Println(solvedBoard)
	solveSudoku(board)
	fmt.Println(board)
}

func solveSudoku(board [][]byte)  {
	backtrack(board,0,0)
}

func threeThreeLoc(i,j,r int)(int, int){
	if i <= 2 && j <= 2 {
		return r / 3, r % 3
	} else if i <=2 && j <= 5 {
		return r / 3 , r%3 + 3
	} else if i<= 2{
		return r / 3 , r%3 + 6
	}

	if i <= 5 && j <=2 {
		return r/3+3 , r%3
	} else if i <=5 && j <=5 {
		return r/3+3 , r%3 + 3
	} else if i<=5{
		return r/3+3, r%3 +6
	}
	if i <= 9 && j <=2 {
		return r/3+6 , r%3
	} else if i <=9 && j <=5 {
		return r/3+6 , r%3 + 3
	} else {
		return r/3+6, r%3 +6
	}
}

func backtrack(board [][]byte, i, j int) bool {
	m,n := nextBlank(board, i, j)
	if m >=9 || n >=9 {
		return true
	}

	for _, value := range []int{1,2,3,4,5,6,7,8,9} {
		if value <= 0 || !isValidPlace(board, m, n, value){
			continue
		}

		board[m][n] = byte(value) + '0'
		if backtrack(board, m, n){
			return true
		}
		board[m][n] = '.'
	}
	return false
}

func isValidPlace(board [][] byte, i, j, value int) bool {
	for r:=0;r<9;r++{
		if int(board[i][r]-'0') == value {
			return false
		}
		if int(board[r][j] - '0') == value {
			return false
		}
		m,n := threeThreeLoc(i, j, r)
		if int(board[m][n] - '0') == value {
			return false
		}

	}
	return true
}

func nextBlank(board [][]byte, i, j int) (int, int){
	for loc := i * 9 + j; loc < 81; loc++ {
		m,n := loc / 9, loc % 9
		if board[m][n] == '.' {
			return m,n
		}
	}
	return 10,10
}
```

Note:

每一个点的位置都有1-9种可能性，但是不是每一个都满足需求，因此通过回溯建立了最终的解。这个解法没有用到Hash Table，在判断是否是有效值时浪费了时间，并不是一种最优的方案。

## Review

本周是一片专注文章[The Power Of Doing Only One Thing](https://medium.com/the-mission/you-cant-be-100-motivated-and-on-fire-all-the-time-so-stop-trying-to-be-49cbafa9c51c)

文章列举了一系列专注于一件事情的优点，已经有很多人谈过了，即使是CPU，多进程执行任务也会降低throughput，况人乎。

## Tips

Hibernate 与 mybatis 对比

1. hibernate 是object-oriented 的，专注于对象与表的映射
2. mybatis 是 sql-centric 的，倾向于手写sql
3. CQRS 中 C 倾向于使用 Hibernate， Q 倾向于使用 mybatis

## Share

上午看了奇葩说第五季，本来也就是当一个综艺节目娱乐时间，但这期与往期不同的是参加节目的选手中多了一个来自山东省郓城的大姐，让精英化的综艺节目多了一点乡土气息。

这位大姐一上台就展现出了非凡的语速与接话能力，别人说什么都要插一嘴，而且接的还不错；另一位辩手是一个 rapper 歌手，而且是那种颜狗，一看对方是这么具有乡土气息的女士就面无表情了，上台之后全程尬笑。他们的辩论题目是`男朋友氪金打赏女主播你会分手吗`，大姐是正方，认为会分手，先发言。

大姐辩论的切入点是男朋友打赏女主播=男朋友拿钱给别的女人花，并把男人的这种行为描述为整天玩手机不干活，拿着老娘赚的钱去养别人，有这个时间为什么不去地里干活。从我的角度看大姐的这个论点没什么问题，因为大姐跟我有一个共同的问题：都没怎么看过女主播直播，而把女主播的直播内容强行脑补为撒娇发嗲。

rapper 歌手的陈述可以说是降维打击了，他精准的将对女主播的打赏行为等同于内容付费，又扩展开来不打赏=吃饭不给钱=拖欠农民工工资，语速不快，但句句致命，让观众有了认同感。在 battle 的环节中，他问出了致命的一个问题：你看过直播么？大姐语塞，后面基本大姐就是在说车轱辘话了。

看完这个让我很有感触，互联网出现以后中国的城市可以说以高铁速度前进，而在农村乡下的人还在以驴车的速度前进。新事物层出不穷，而这些不知道多少年后才能传导到农村中，这中间有一个巨大的鸿沟，信息的鸿沟。

在计算机里，也存在这样的鸿沟。只会中文的程序员和英语麻溜的程序员。只会中文的程序员用百度，搜到的是二手和搬运的信息；英语麻溜的程序员用谷歌，去搜信息源头的信息。吃别人嚼过的饭固然消化快，但靠自己才能打造超强的消化系统，他们最终会成长为他人眼中的牛x程序员，但他们知道，自己要走的路还有很长很长。

