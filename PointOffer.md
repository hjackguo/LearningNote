# 剑指offer

## 栈

### 09.两个栈实现队列

> 思路

设计两个栈，inputStack和outputStack。

队列添加的所有元素存放到inputStack

队列要删除队头元素时，

1. outputStack如果为空，inputStack所有元素压入outputStack
2. 此时从outputStack.pop()元素就可以

------

### 30.包含min函数的栈

> 要求

定义栈的数据结构，请在该类型中实现一个能够得到栈的最小元素的 min 函数在该栈中，调用 min、push 及 pop 的时间复杂度都是 O(1)。

> 思路

设计一个stack和一个minStack

1.  push x时，如果minStack为空，把值也push进minStack。如果minStack不为空，假如x<=minStack的栈顶，把x压入minStack。
2. pop 时，如果stack的pop值等于minStack的peek，那么minStack也进行pop操作
3. 返回minStack的peek就是最小值。

------



### 59_2.队列的最大值(*)

> 要求

请定义一个队列并实现函数 max_value 得到队列里的最大值，要求函数max_value、push_back 和 pop_front 的均摊时间复杂度都是O(1)。

> 思路

声明一个普通队列和一个双向队列

1. push元素x时，如果x大于双向队列最右边的元素，把双向队列最右边小于x的所有元素移除，然后双向队列执行addLast(x)
2. pop时，判断普通队列pop的值是否等于双向队列getFirst的值，如果等于，双向队列执行removeFirst
3. 取Max时，返回双向队列的getFirst.

![lc](https://pic.leetcode-cn.com/9d038fc9bca6db656f81853d49caccae358a5630589df304fc24d8999777df98-fig3.gif)

------

## 堆

### 40.最小的k个数

输入整数数组 `arr` ，找出其中最小的 `k` 个数。例如，输入4、5、1、6、2、7、3、8这8个数字，则最小的4个数字是1、2、3、4。

> 最大堆  
>
> 空间复杂度 O(K), 时间复杂度O(N*log(K))

重写PriorityQueue方法，实现最大堆。

注意，新的元素a进来时，判断规则如下：

1. 如果堆的size<k, 此时直接插入a
2. 如果堆size==k,此时判断堆的peek
   - 如果peek值>a，堆要执行poll，然后插入
   - 如果peek<=a, 不需要执行任何操作

![lc](https://pic.leetcode-cn.com/ffd837f564946747ff6fca9df7568fa894494f2e46fe0fe2d6590123432f0a57.gif)

> 快排变形
>
> 空间复杂度 O(1), 理想时间复杂度O(N),特殊情况下时间复杂度O(N^2)



