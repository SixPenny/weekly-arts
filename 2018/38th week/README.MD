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

ÿһ�����λ�ö���1-9�ֿ����ԣ����ǲ���ÿһ���������������ͨ�����ݽ��������յĽ⡣����ⷨû���õ�Hash Table�����ж��Ƿ�����Чֵʱ�˷���ʱ�䣬������һ�����ŵķ�����

## Review

������һƬרע����[The Power Of Doing Only One Thing](https://medium.com/the-mission/you-cant-be-100-motivated-and-on-fire-all-the-time-so-stop-trying-to-be-49cbafa9c51c)

�����о���һϵ��רע��һ��������ŵ㣬�Ѿ��кܶ���̸���ˣ���ʹ��CPU�������ִ������Ҳ�ή��throughput�����˺���

## Tips

Hibernate �� mybatis �Ա�

1. hibernate ��object-oriented �ģ�רע�ڶ��������ӳ��
2. mybatis �� sql-centric �ģ���������дsql
3. CQRS �� C ������ʹ�� Hibernate�� Q ������ʹ�� mybatis

## Share

���翴������˵���弾������Ҳ���ǵ�һ�����ս�Ŀ����ʱ�䣬�����������ڲ�ͬ���ǲμӽ�Ŀ��ѡ���ж���һ������ɽ��ʡ۩�ǵĴ�㣬�þ�Ӣ�������ս�Ŀ����һ��������Ϣ��

��λ���һ��̨��չ�ֳ��˷Ƿ���������ӻ�����������˵ʲô��Ҫ��һ�죬���ҽӵĻ���������һλ������һ�� rapper ���֣������������չ���һ���Է�����ô����������Ϣ��Ůʿ�����ޱ����ˣ���̨֮��ȫ����Ц�����ǵı�����Ŀ��`������봽����Ů������������`���������������Ϊ����֣��ȷ��ԡ�

�����۵�������������Ѵ���Ů����=��������Ǯ�����Ů�˻����������˵�������Ϊ����Ϊ�������ֻ����ɻ��������׬��Ǯȥ�����ˣ������ʱ��Ϊʲô��ȥ����ɻ���ҵĽǶȿ���������۵�ûʲô���⣬��Ϊ��������һ����ͬ�����⣺��û��ô����Ů����ֱ��������Ů������ֱ������ǿ���Բ�Ϊ�������ǡ�

rapper ���ֵĳ�������˵�ǽ�ά����ˣ�����׼�Ľ���Ů�����Ĵ�����Ϊ��ͬ�����ݸ��ѣ�����չ����������=�Է�����Ǯ=��Ƿũ�񹤹��ʣ����ٲ��죬������������ù���������ͬ�С��� battle �Ļ����У����ʳ���������һ�����⣺�㿴��ֱ��ô��������������������������˵����ꤻ��ˡ�

����������Һ��ид��������������Ժ��й��ĳ��п���˵�Ը����ٶ�ǰ��������ũ�����µ��˻�����¿�����ٶ�ǰ�������������������Щ��֪�����������ܴ�����ũ���У����м���һ���޴�ĺ蹵����Ϣ�ĺ蹵��

�ڼ�����Ҳ���������ĺ蹵��ֻ�����ĵĳ���Ա��Ӣ������ĳ���Ա��ֻ�����ĵĳ���Ա�ðٶȣ��ѵ����Ƕ��ֺͰ��˵���Ϣ��Ӣ������ĳ���Ա�ùȸ裬ȥ����ϢԴͷ����Ϣ���Ա��˽����ķ���Ȼ�����죬�����Լ����ܴ��쳬ǿ������ϵͳ���������ջ�ɳ�Ϊ�������е�ţx����Ա��������֪�����Լ�Ҫ�ߵ�·���кܳ��ܳ���
