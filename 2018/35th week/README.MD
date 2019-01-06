## Algorithm

### Description 

tags : `Divide And Conquer` `Dynamic Programming` `Array`

Difficulties �� `easy`

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

#### ��ٷ�

������Ϊ�򵥵�ԭ��Ӧ�þ���ʹ����ٷ��ǿ���Accepted�ģ����ǽ����е������Ӵ����֮��ȡ���ֵ�Ϳ����ˡ���Ҫ����forѭ����ʱ�临�Ӷ�O(n^2), �ռ临�Ӷ�O(1)

#### DP + ̰��

����ⷨ����Jon Bentley (Sep. 1984 Vol. 27 No. 9 Communications of the ACM P885) �����ģ�����ժ¼һ���㷨���ͣ�

```
algorithm that operates on arrays: it starts at the left end (element A[1]) and scans through to the right end (element A[n]), keeping track of the maximum sum subvector seen so far. The maximum is initially A[0]. Suppose we've solved the problem for A[1 .. i - 1]; how can we extend that to A[1 .. i]? The maximum
sum in the first I elements is either the maximum sum in the first i - 1 elements (which we'll call MaxSoFar), or it is that of a subvector that ends in position i (which we'll call MaxEndingHere).

MaxEndingHere is either A[i] plus the previous MaxEndingHere, or just A[i], whichever is larger.
```

Ҳ����˵����һ����̬�滮 + ̰�����㷨��ʱ�临�Ӷ�ֻ��Ҫ O(n),�ռ临�Ӷ�Ϊ O(1),������������ⷨ��

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

��Ŀ������Ҫ��ʹ�÷��η�������������һ�����ʹ�÷��η�

������������ `nums`, �ֳ����������� `numsLeft` `numsRight`, Ȼ��ֱ��������Ӵ��͵ļ��㣬��ֻ��һ��Ԫ��ʱ�����Ǿͷ������Ԫ�أ��ؼ��������ˣ���κϲ����������õ����յĽ����?��divide and conquer �����Ѿ���conquer�����ˣ�

��������ȥ����һ�� [divide and conquer �� Wikipedia](https://en.wikipedia.org/wiki/Divide_and_conquer_algorithm) �����������г��˺ܶ�ʹ�÷��η��ľ����㷨�����������Ŀ�� [Closest Pair Of Points problem](https://en.wikipedia.org/wiki/Closest_pair_of_points_problem) �Ǻ����Ƶġ�

�����Ӵ��� conquer ��Ҫ�������У�

- �����Ҳ඼��Ҫ��Ԫ�ز���
- �������Ӵ��������������ҵó�һ�����Ԫ�غ�m

��ࡢ�Ҳ���m�����ֵ����ԭ���������Ӵ��͡�

���η��ĸ��Ӷ�Ϊ O(nlogn),�������������Ž⡣

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

���ܶ���[divide and conquer algorithm](https://en.wikipedia.org/wiki/Divide_and_conquer_algorithm)

1. divide and conquer �Ķ�����ʲô��

divide and conquer ��һ�����ڶ�·�ݹ���㷨��Ʒ�ʽ��ͨ��������ֽ�ɶ����ͬ����������⣬֪������С������ֱ�Ӹ����𰸣�Ȼ�󽫸���С����Ĵ���������õ�ԭ����Ľ⡣

2. divide and conquer ����Щ�㷨Ӧ�ã�

- ���� (quicksort, mergesort)
- �����˷�
- ƽ���о��������������
- �﷨����
- ��ɢ����Ҷ�任

�ȵ�

3. divide and conquer ����Щ���ƣ�

- ����������������
- �����㷨Ч��
- ֧�ֲ���
- �ڴ�����Ѻ�(���Ͼֲ���ԭ��)
- �������(���㸡�������)

## Technique

#### ժҪ

Go �ṩ�������ؼ���`new` �� `make` ��Ϊ���ͷ����ڴ棬�����ߵĹ����ǲ�ͬ�ģ�һ�仰��˵���ǣ�newֻ�� �ڴ���䣬��make������ʼ��slice��map �� channel


#### new

ԭ�ͣ�`func new(Type) *Type`

Go �ٷ��ĵ��ϵĽ��ͣ�

```
The new built-in function allocates memory. The first argument is a type,
not a value, and the value returned is a pointer to a newly
allocated zero value of that type.
```

�������ǹ�ע��һ�� `newly allocated zero value of that type`, Ҳ����˵new �����ڴ�֮�󣬻᷵�ض�Ӧ����Ϊ��ֵ��ָ�룬���Ĳ�����һ�����ͣ����������������һ��ռλ�������Կ������Ķ���Ϊ`type Type int` �� ������Ӧ�ó������ǻ�ȡ�����ģ�һֱ�������ɻ�Go �İ��淶Ҫ������ĸ��д����public�ģ�Сд����private�ģ�������builtin ���У�Сд�Ŀ����ڳ�����ʹ�ã���д�ķ����ǲ����õģ���

#### make 

ԭ�ͣ�`func make(t Type, size ...IntegerType) Type`

Go �ٷ��ĵ����ͣ�
```
The make built-in function allocates and initializes an object of type
slice, map, or chan (only). Like new, the first argument is a type, not a
value. Unlike new, make's return type is the same as the type of its
argument, not a pointer to it. The specification of the result depends on
the type:
```

make ֻ���������� slice��map ��channel�������������������ͣ���Ȼ���ǿ��Կ��� make �� new �ĺ���ǩ�������Ƶģ����ǵ�ʹ�� int ��������Ϊ make ����ʱ������ᱨ����

make�����Խ������β�����������ʼ������slice ��һ����������len���ڶ������� capacity��map ֻ����һ������������ʼ������channel �� int �������� buffer ��С��û�д����޻��壩

Ϊʲô��Ҫ�� make ���������������ͣ���Ϊ���������ʹ������Ƕ����ݽṹ�����ã�Ҫʹ�����ǣ�����Ҫ�ȳ�ʼ������slice Ϊ����һ�� slice ��Ҫ3 ��Ԫ�أ�ָ�����ݵ�ָ�룬���ȣ�len����������cap��(�����ʵĿ��Բμ� [go slice usage](https://blog.golang.org/go-slices-usage-and-internals)), ��δ��ʼ��֮ǰslice Ϊnil���ǲ����õġ�Ҳ����˵ ��slice��map��channel �������ͣ�make��ʼ�������ǵ��ڲ����ݽṹ��ʹ�������������á�

#### �Ա�

`new` ���ڽ��յĲ���������û�����Ƶģ�Ҳ�����ǿ��Դ�slice��map��channel���ͣ������Ͽ�new �Ĵ��ھ͹��ˣ����ǣ�����Ҫ֪�������new ������slice��map��channel Ϊnil��ʵ����û���ô������ڽӿ�Ҳһ����new ���Խ��գ������� nil���ڳ������ǲ����õġ�

#### Ϊ��Ҫ����������

�������������Ҳ�кܶ����ۣ������������ֻ�� Go ���Եķ����˲��ܸ�������������ֻ���²⡣

���������Եȵ������ֻ��һ�����亯������ô����

�������ǵ��·��亯�����ֽ�`alloc`, ����ֻ���� make һ������Ϊ���Ǽ���Ҫ�ܴ��� int ���ֲ���Ҫ���ȵ����ͣ�Ҳ��Ҫ���� slice ������Ҫ���ȵ����ͣ�Ȼ�󷵻�����Ϊָ�루ֻ����һ�ַ������ͣ����䵽���Ϸ���ָ�룩����������һ������ʹ�����⡣

1. ��������

`size ...IntegerType` ������Ϊ��������ʱ�Ƕ���ģ����㴫���ˣ�Ҳ�ǲ��õģ�����һ�����ȱ�ݡ������������������������ı��ʵ����������������ʱ�����ǻ�ʹ�÷������أ��� Go �в�֧�ֺ������أ���·��ͨ��

2. ��������Ϊָ�룬slice��ʹ���鷳

ʹ�� make ���ص������ͱ�����������ص���ָ��Ļ�������ʹ����������Ҫ���һ�����ţ���ȡ slice �е�ĳһ��Ԫ����Ҫ����ʹ��`(*slice)[i]`, map Ҳ��һ���������𲻱�Ҫ�Ľ�����


����������ߵĽǶ����������һ�������۵�̫�࣬���Էֿ�Ϊ������Ȩ����ȷ��ʹ�÷��㣬�����ⲻ�ָ��˺ܶ���д���͵�һ�������


## Share 

���ܾ�����һ���������⣬������Ժ�������ݿ�һ��Ҫ�ȱ��ݣ�

���˰�ؼ��鳤��绰��˵д����һ��ϵͳ���������б�����������һ��id�飬�Ҳ���һ�·�������ǰ�����ݣ�������ݸ�ʽ���ڼ���Ĺ�һ�Σ������߼�Ҳ�仯Ϊʹ�ø��ĵ����ݸ�ʽ��Ȼ��������ݾ�����һ�д��뱨����ָ�롣

������������һ�־����޸Ĵ��������ǰ���߼�������һ�����޸����ݿ����ݰ���ǰ�������ݵ���Ϊ�¸�ʽ���������ݿ⿴�����򵥷��㣬�������ߣ��Ҿ�ѡ����һ�֣�Ȼ���ż�æ�ŵİ� sql д������Ȼ��ֱ��������ִ�У���ͷһ��ԭ��id��Ӧ�����ݣ����ˣ���Ӧ��ֵȫ�������0��

��һ���Լ�д�� sql�� �Ǹ��ֶ�����Ϊ varchar, ���üӺŽ�������ݺ������ַ���ƴ������������ MySQL �ж��ַ�����ƴ���üӺ��ǲ��еģ�����Ҫ�� `concat` �������Ӻ�ֻ�������������������þò��������ݿ�ѱ�����Ժ����ݿ�Ū���ˣ������군�ˣ��������ѵ�Ҫ����һ����ʵ��Ĵ�ɾ�⵽��·��

���в��ϸ���Լ�Ҫ�侲������û�ˣ�ֻ����취�������������ֲ��ˡ�����Ҫ�о����ǵ���һ���Ŷӣ������ṩ�Ľӿڲ��õĲ����Ǿֲ����£�����Ϊnull���в��������Ķ����ҸϽ��ڴ��������һ��Ϊ����жϣ����������ϣ��������ǰ�����������ˡ�

�����ݵĲ���Ҫ��֮�������ر��ǶԴ��������ݵĸ���ɾ���������������ܽ��˼���ϣ���Լ��Ժ����������ʱ��ʱ��ע�⡣

1. sql д����Ҫ���ڲ��Ի�������һ��
2. ��Ӱ�쵽�ı����ݲ���֮ǰ���ñ���
3. ����������ʱ������ѡ�����ô������ķ�ʽ