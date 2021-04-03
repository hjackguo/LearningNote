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



### 31.栈的压入、弹出序列

输入两个整数序列，第一个序列表示栈的压入顺序，请判断第二个序列是否为该栈的弹出顺序。假设压入栈的所有数字均不相等。例如，序列 {1,2,3,4,5} 是某栈的压栈序列，序列 {4,5,3,2,1} 是该压栈序列对应的一个弹出序列，但 {4,3,5,1,2} 就不可能是该压栈序列的弹出序列。

> 解法一：辅助栈
>
> 时间复杂度：O(N)
>
> 空间复杂度: O(N)

借助辅助栈模拟，用一个指针指向pop数组

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

> 解法一：最大堆  
>
> 空间复杂度 O(K), 时间复杂度O(N*log(K))

重写PriorityQueue方法，实现最大堆。

注意，新的元素a进来时，判断规则如下：

1. 如果堆的size<k, 此时直接插入a
2. 如果堆size==k,此时判断堆的peek
   - 如果peek值>a，堆要执行poll，然后插入
   - 如果peek<=a, 不需要执行任何操作

![lc](https://pic.leetcode-cn.com/ffd837f564946747ff6fca9df7568fa894494f2e46fe0fe2d6590123432f0a57.gif)

> 解法二：快排变形
>
> 空间复杂度 O(1), 理想时间复杂度O(N),特殊情况下时间复杂度O(N^2)

通过快排算法，每回合结束时，left==right。 left左边比left小，left右边比left大。

1. left+1<k : quickSelect(left+1,end)
2. left+1=k:  return
3. left+1>k  quickSelect(start,left-1)

<img src="http://ww1.sinaimg.cn/large/008aPpVGgy1goee9hqnshj31qw0ewq7q.jpg" alt="WeChatff111acdbc56c85c6eba64ec2faa0323.png" style="zoom:33%;" />

> 快选和堆排序比较

快搜选择算法在时间、空间复杂度都优于堆排序，但快排算法存在限制：

1. 快选算法需要修改原数组，如果原数组不能修改，空间复杂度会提升
2. 算法需要保存所有数据。如果把数据看成输入流的话，堆得方法是来一个处理一个，不需要保存数据。而快速选择需要先保存下来所有的数据，在进行运算。

------

### 41.数据流中的中位数

如何得到一个数据流中的中位数？如果从数据流中读出奇数个数值，那么中位数就是所有数值排序之后位于中间的数值。如果从数据流中读出偶数个数值，那么中位数就是所有数值排序之后中间两个数的平均值。

例如，

[2,3,4] 的中位数是 3

[2,3] 的中位数是 (2 + 3) / 2 = 2.5

设计一个支持以下两种操作的数据结构：

void addNum(int num) - 从数据流中添加一个整数到数据结构中。
double findMedian() - 返回目前所有元素的中位数。

> 解法一：尖子班普通班 (最大堆+最小堆)
>
> 时间复杂度：n*log(n)
>
> 空间复杂度:  n
>
> 返回中位数：O(1)

普通班  最大堆

尖子班  最小堆

1. 新的数字先进普通班。
2. 如果普通班人数-尖子班人数>1,普通班poll，尖子班offer
3. 循环查看是否普通班的成绩最好的比尖子班成绩最差的分数高，如果是，互换
4. 取中位数时，如果普通班尖子班人一样，返回两个堆顶数字的平均值。如果人数不一样，返回普通班堆顶。

------

## 排序

### 45.把数组排成最小的数

输入一个非负整数数组，把数组里所有数字拼接起来排成一个数，打印能拼接出的所有数字中最小的一个。

输入: [3,30,34,5,9]
输出: "3033459"

> 字符串相加比较法
>
> 时间复杂度: O(N*log N), 最差情况下O(N^2)
>
> 空间复杂度: O(N)

核心思想： x+y>y+x , 则x"大于"y;

​					x+y<y+x,  则x“小于”y;

向Arrays.sort()重新传入比较规则或者自己手写新规则的快排。

Java中字符串比较用 X.compareTo(Y)方法

------



## 位运算

### 15. 二进制中1的个数 (*)

请实现一个函数，输入一个整数（以二进制串形式），输出该数二进制表示中 1 的个数。例如，把 9 表示成二进制是 1001，有 2 位是 1。因此，如果输入 9，则该函数输出 2

> 方法一：逐位判断
>
> 时间复杂度 O(n)

思想：

最低位与1取&，然后右移

注意**>>**与**>>>**区别：

- '>>>'**无符号右移**，左边补0，符号位如果1会变0.
- '>>'  **有符号右移**，如果符号位是1，右移时符号位一直是1。

```java
//  >> 如果 1<<31 >> 31，变成    -1 符号位存在   1000000 00000000 00000000 00000001
a>>31       
//  >>> 如果 1<<31 >>> 32 变成   1  0000000 00000000 00000000 00000001
a>>>31
```

> 方法二：n&(n-1)
>
> 时间复杂度O(n)

思想：

每次消除最右边的那个1

<img src="http://ww1.sinaimg.cn/large/008aPpVGgy1gofs8ci39fj30m60fyq8w.jpg" alt="WeChatd03b71f62c6cb93a2407aac9e7a70a9b.png" style="zoom:33%;" />

------

### 39.数组中出现次数超过一半的数字

数组中有一个数字出现的次数超过数组长度的一半，请找出这个数字。

**示例 1:**

```
输入: [1, 2, 3, 2, 2, 2, 5, 4, 2]
输出: 2
```

> 方法一：Map计数
>
> 时间复杂度 O(N)
>
> 空间复杂度 O(N)

key数字，value出现次数

> 方法二：排序取中间
>
> 时间复杂度 O(N*logN)
>
> 空间复杂度 1

Arrays.sort()后，取中间就是！

> 方法三：摩尔投票法
>
> 时间复杂度 O(N)
>
> 空间复杂度 1
>
> 最佳算法

```java
    public int majorityElement_Moer(int[] nums) {
      	// 记录待抵消数字
        Integer multiNum = null;
      	// 待抵消数字的积分
        int count = 0;
        for(int num:nums){
          	// 如果为null，把当前数字当成待抵消数字，count++
            if(multiNum==null){
                multiNum = num;
                count++;
            }else if(multiNum==num)
              // 如果当前数字等于待抵消数字， count++
                count++;
            else {
              // 当前数字不等于待抵消数字，抵消掉一次！ 
              // 同时判断count为0的话，multiNum置为0
                count--;
                if(count==0) multiNum = null;
            }
        }
      	// 还没消完的待抵消数字就是众数
        return multiNum;
    }
```



### 65. 不用加减乘除做加法(*)

> 解法一： 位运算

```java
   public static int add(int a, int b) {
        int c = 0;
        while (b!=0){
            c = (a&b)<<1;  // c为进位
            a ^= b;   // 此时重复的去处，a,b单独的留下
            b = c;    // b赋值为进位
        }
        return a;
    }
```

![WeChat60269e6c7b314556f2227fdf51fae2ea.png](http://ww1.sinaimg.cn/large/008aPpVGgy1goyysm1ekij32141ik7wh.jpg)



## 树

### 07.重建二叉树 (*)

输入某二叉树的前序遍历和中序遍历的结果，请重建该二叉树。假设输入的前序遍历和中序遍历的结果中都不含重复的数字。

 前序遍历 preorder = [3,9,20,15,7]
中序遍历 inorder = [9,3,15,20,7]

> 解法一 : 递归
>
> 时间复杂度 : O(N)
>
> 空间复杂度 : O(N) 

把inorder序列按照 map.put(inorder[i],i) 放进map,后面可以用来索引

定义函数 recur(left_pre, right_pre, left_in, right_in)

1. preorder的第一个值，放进map,拿到inorder里的中节点pivot，用pivot这个点建立一个ListNode

2. ListNode.left    = recur(left_pre+1, left_pre+1+(pivot - left_in - 1), left_in, pivot-1)

   ListNode.right = recur(left_pre+1+ (pivot- left_in -1)+1, right_pre, pivot+1, right_in)

<img src="http://ww1.sinaimg.cn/large/008aPpVGgy1gohnn7s6rpj32881mwqv5.jpg" alt="WeChat445b822a246e70b59a19c1c64ab4e5e6.png" style="zoom:50%;" />

推导过程自己需要用笔写下，边界用特殊值代入确定。

------

### 26. 树的子结构

输入两棵二叉树A和B，判断B是不是A的子结构。(约定空树不是任意一个树的子结构)

B是A的子结构， 即 A中有出现和B相同的结构和节点值。

示例 1：

输入：A = [1,2,3], B = [3,1]
输出：false

示例 2：

输入：A = [3,4,5,1,2], B = [4,1]
输出：true

> 解法一: 遍历A上每个点出发的结构是否满足B
>
> 时间复杂度: O(N*M)    N - A的节点数量   M - B的节点数量
>
> 空间复杂度 O(N)

```java
// 递归调用判断A某点出发与B是否相同时，如果进入函数后B==null，返回true，接下来A==null，返回false
    boolean checkIfSame(TreeNode A,TreeNode B){
        // B==null时，已经没有得比较了，返回true
        if(B==null) return true;
        // B不为null A却为Null, 返回false
        if(A==null) return false;
        return A.val==B.val
                &&checkIfSame(A.left,B.left)
                &&checkIfSame(A.right,B.right);
    }
```

### 27. 二叉树的镜像

请完成一个函数，输入一个二叉树，该函数输出它的镜像。

**示例 1：**

```
输入：root = [4,2,7,1,3,6,9]
输出：[4,7,2,9,6,3,1]
```

> 解法一： 递归左右对换
>
> 时间复杂度：O(N)

递归调用左右对换

### 28.对称的二叉树(*)

请实现一个函数，用来判断一棵二叉树是不是对称的。如果一棵二叉树和它的镜像一样，那么它是对称的。

> 解法一

```java
   public boolean isSymmetric(TreeNode root) {
        if(root==null) return true;
        return helper(root.left,root.right);
    }
    boolean helper(TreeNode leftTree,TreeNode rightTree){
        if(leftTree==null&&rightTree==null) return true;
        if(leftTree==null||rightTree==null||leftTree.val!=rightTree.val) return false;
        return helper(leftTree.left,rightTree.right)&&helper(leftTree.right,rightTree.left);
    }
```

比较三部分，左 右  本身值

### 32-I.从上到下打印二叉树

从上到下打印出二叉树的每个节点，同一层的节点按照从左到右的顺序打印。

>解法一：队列实现BFS
>
>时间复杂度: O(N)
>
>空间复杂度: O(N)

### 32-II.从上到下打印二叉树

从上到下按层打印二叉树，同一层的节点按从左到右的顺序打印，每一层打印到一行。

>解法一：队列实现BFS
>
>时间复杂度: O(N)
>
>空间复杂度: O(N)

在上次的基础上，增加一个childQueue, 来回切换。 记得添加到最终list时，要调用clone()方法

### 32-III.从上到下打印二叉树

请实现一个函数按照之字形顺序打印二叉树，即第一行按照从左到右的顺序打印，第二层按照从右到左的顺序打印，第三行再按照从左到右的顺序打印，其他行以此类推。

> 解法一：队列实现BFS
>
> 时间复杂度: O(N)
>
> 空间复杂度: O(N)

在上次的基础上，增加一个childQueue, 来回切换。 记得添加到最终list时，要调用clone()方法。维护一个方向信号量。调用Collections.reverse()方法。

### 33.二叉搜索树的后续遍历序列(*)

输入一个整数数组，判断该数组是不是某二叉搜索树的后序遍历结果。如果是则返回 `true`，否则返回 `false`。假设输入的数组的任意两个数字都互不相同。

```
输入: [1,6,3,2,5]
输出: false
```

> 解法一： 双while递归法
>
> 时间复杂度: O(N^2)

```java
    boolean check(int[] postorder,int start,int end){
        if(start>=end) return true;
        int p=start;
        while (postorder[p]<postorder[end]&&p<end)p++;
        int leftEnd = p-1;
        while (postorder[p]>postorder[end]&&p<end)p++;
        return p==end
                &&check(postorder,start,leftEnd)
                &&check(postorder,leftEnd+1,end-1);
    }
```

注意用while代码比较简洁。

### 37.序列化二叉树(*)

请实现两个函数，分别用来序列化和反序列化二叉树

> deserialize

1. 用Queue实现层次访问
2. 维护一个指针，用来指向下一个Queue中的字节点

建议，在纸上自己推导: 5, 2, 3, null, null, 2, 4, 3, 1

### 54.二叉搜索树的第K大节点

给定一棵二叉搜索树，请找出其中第k大的节点。

> 思路

按**右 -> 中 -> 左**顺序搜索

### 55-I.二叉树的深度

输入一棵二叉树的根节点，求该树的深度。从根节点到叶节点依次经过的节点（含根、叶节点）形成树的一条路径，最长路径的长度为树的深度。

```java
    public int maxDepth(TreeNode root) {
        if(root==null) return 0;
        return Math.max(maxDepth(root.left),maxDepth(root.right))+1;
    }
```

### 55-II.平衡二叉树

输入一棵二叉树的根节点，判断该树是不是平衡二叉树。如果某二叉树中任意节点的左右子树的深度相差不超过1，那么它就是一棵平衡二叉树。

> 思路

判断左右树深差

```java
    int depth(TreeNode root){
        if (!balanced||root==null) return 0;
        int leftD = depth(root.left);
        int rightD = depth(root.right);
        if(Math.abs(leftD-rightD)>1) balanced = false;
        return Math.max(leftD,rightD)+1;
    }
```

### 68-I.二叉搜索树的最近公共祖先(*)

给定一个二叉搜索树, 找到该树中两个指定节点的最近公共祖先。

百度百科中最近公共祖先的定义为：“对于有根树 T 的两个结点 p、q，最近公共祖先表示为一个结点 x，满足 x 是 p、q 的祖先且 x 的深度尽可能大（一个节点也可以是它自己的祖先）。”

> 思路

```java
public TreeNode lowestCommonAncestor(TreeNode root, TreeNode p, TreeNode q) {
        if(p.val<root.val&&q.val<root.val) return lowestCommonAncestor(root.left,p,q);
        if(p.val>root.val&&q.val>root.val) return lowestCommonAncestor(root.right,p,q);
        return root;
}
```

1. 如果两个点都大于当前点，递归去当前点右子树搜
2. 如果两个点都小于当前点，递归去当前点左子树搜
3. 前两种都不成立，就说明一定是当前点

> 注意

本题会做，但能否这么简便？ 注意多做几次。

### 68-II.二叉树的最近公共祖先(*)

给定一个二叉树, 找到该树中两个指定节点的最近公共祖先。

百度百科中最近公共祖先的定义为：“对于有根树 T 的两个结点 p、q，最近公共祖先表示为一个结点 x，满足 x 是 p、q 的祖先且 x 的深度尽可能大（一个节点也可以是它自己的祖先）。

> 思路

递归本身，拿到左和右的情况，如果左为空，如果都在右；如果右为空，说明都在左。如果都不为空，说明就是本身。

```java
    public TreeNode lowestCommonAncestor(TreeNode root, TreeNode p, TreeNode q) {
        if(root==null||root==p||root==q)return root;
        TreeNode left = lowestCommonAncestor(root.left,p,q);
        TreeNode right = lowestCommonAncestor(root.right,p,q);
        if(left==null) return right;
        if(right==null) return left;
        return root;
    }
```

> 注意

本题会写，但能否想出这种最简洁的思路。

### 34.二叉树中和为某一值的路径(补充题)

输入一棵二叉树和一个整数，打印出二叉树中节点值的和为输入整数的所有路径。从树的根节点开始往下一直到叶节点所经过的节点形成一条路径。

> 回溯法

本题加了后出来时要减去，同时添加到lists里的list要**新建对象**。

## DFS

### 12.矩阵中的路径

请设计一个函数，用来判断在一个矩阵中是否存在一条包含某字符串所有字符的路径。路径可以从矩阵中的任意一格开始，每一步可以在矩阵中向左、右、上、下移动一格。如果一条路径经过了矩阵的某一格，那么该路径不能再次进入该格子。例如，在下面的3×4的矩阵中包含一条字符串“bfce”的路径（路径中的字母用加粗标出）。

[["a","b","c","e"],
["s","f","c","s"],
["a","d","e","e"]]

但矩阵中不包含字符串“abfb”的路径，因为字符串的第一个字符b占据了矩阵中的第一行第二个格子之后，路径不能再次进入这个格子。

> 解法一： 每个点开始DFS
>
> 时间复杂度：两个FOR循环，里面还嵌套搜索

两个for循环计算从每个点开始的可能性。直接对四个方向搜，然后再去判断越界。用'.'符号标记已经被走过的路，记得结束的时候复位。

### 13. 机器人的运动范围

地上有一个m行n列的方格，从坐标 [0,0] 到坐标 [m-1,n-1] 。一个机器人从坐标 [0, 0] 的格子开始移动，它每次可以向左、右、上、下移动一格（不能移动到方格外），也不能进入行坐标和列坐标的数位之和大于k的格子。例如，当k为18时，机器人能够进入方格 [35, 37] ，因为3+5+3+7=18。但它不能进入方格 [35, 38]，因为3+5+3+8=19。请问该机器人能够到达多少个格子？

> 解法一： DFS
>
> 时间复杂度：O(MN)
>
> 空间复杂度: O(MN)

核心代码

```java
    void dfs(int i,int j){
        if(i<0||j<0||i>=m||j>=n) return;
        if(sumOfAllBit(i)+sumOfAllBit(j)>k) return;
      	// 已经标记了的跳过
        if(matrix[i][j]) return;
        matrix[i][j] = true;
        count++;
        dfs(i-1,j);
        dfs(i+1,j);
        dfs(i,j-1);
        dfs(i,j+1);
    }
```

> 解法二：BFS

借助队列实现

## 递归

### 10-I.斐波那契数列

```
F(0) = 0,   F(1) = 1
F(N) = F(N - 1) + F(N - 2), 其中 N > 1.
```

> 解法一：递归
>
> 时间复杂度：O(2^N)
>
> 空间复杂度: O(N) 深度过程中，栈里得留着数

```java
    public int fib(int n) {
        if(n==0) return 0;
        if(n==1) return 1;
        return (int)((fib(n-1)+fib(n-2))%(1e9+7));
    }
```

慎重选择递归，当n==100,跑都跑不动2^100次方复杂度

> 解法二：动态规划
>
> 时间复杂度: O(N)

用两个变量保存前两个值，循环替换就行。

> 解法三：记忆化递归法
>
> 时间复杂度: O(N)
>
> 空间复杂度: O(N)

新建一个长度为N的数组，存储对应的斐波那契值，如果递归过程中需要，直接去数组拿。如果不存在，计算后放入数组供别的路径使用。

### 10-II.青蛙跳台阶

一只青蛙一次可以跳上1级台阶，也可以跳上2级台阶。求该青蛙跳上一个 n 级的台阶总共有多少种跳法。

答案需要取模 1e9+7（1000000007），如计算初始结果为：1000000008，请返回 1。

> 解法一：递归

> 解法二：记忆化递归法

> 解法三：动态规划

本题和10-I思路完全一样。



### 16.数值整数次方

实现 [pow(*x*, *n*)](https://www.cplusplus.com/reference/valarray/pow/) ，即计算 x 的 n 次幂函数（即，xn）。不得使用库函数，同时不需要考虑大数问题。

> 解法一：快速幂
>
> 时间复杂度 O(Log(N))
>
> 空间复杂度O(1)

1. n要化为正数，由于int型特点 -2^31到2^31-1的特点，考虑是否溢出，如果溢出，先化成Integer.Max，再标记欠一次。

2. 快速幂函数中

   用y = Math.pow(x,n/2), 然后 return y*y

   不可以  return Math.pow(x,n/2)*Math.pow(x,n/2),

   第二种重复计算，直接复杂度指数级上升

## 队列

### 59-I.滑动窗口的最大值

给定一个数组 nums 和滑动窗口的大小 k，请找出所有滑动窗口里的最大值。

输入: nums = [1,3,-1,-3,5,3,6,7], 和 k = 3
输出: [3,3,5,5,6,7] 

> 解法一： 双向队列
>
> 时间复杂度: O(N)
>
> 空间复杂度 O(K)

核心思想，维护一个递减的双向队列，每次返回队列的第一个元素就行。



## 数组

### 03.数组中重复的数字

在一个长度为 n 的数组 nums 里的所有数字**都在 0～n-1 的范围内**。数组中某些数字是重复的，但不知道有几个数字重复了，也不知道每个数字重复了几次。请找出数组中任意一个重复的数字。

输入：
[2, 3, 1, 0, 2, 5, 3]
输出：2 或 3 

> 解法一：暴力算法
>
> 时间复杂度：O(N^2)
>
> 空间复杂度  O(1)

两个嵌套for循环判断



> 解法二：Set
>
> 时间复杂度：O(N)
>
> 空间复杂度:  O(N)



> 解法三：原地置换
>
> 时间复杂度:O(N)
>
> 空间复杂度: O(1)

核心思想：数字都在0～n-1范围，坐标0的坑位while找0，找到的话才找坐标1的坑位。中途如果遇到相同，就返回。

```yaml
 nums[i]     1  2  3  2    萝卜
     i       0  1  2  3    坑
```

同样的，0号坑不要1号，先和1号坑交换，交换完这样的：

```yaml
 nums[i]     2  1  3  2    萝卜
     i       0  1  2  3    坑
```

0号坑不要2号萝卜，去和2号坑交换，交换完这样的：

```yaml
 nums[i]     3  1  2  2    萝卜
     i       0  1  2  3    坑
```

0号坑不要3号萝卜，去和3号坑交换，交换完这样的：

```yaml
 nums[i]     2  1  2  3    萝卜
     i       0  1  2  3    坑
```

0号坑不要2号萝卜，去和2号坑交换，结果发现你2号坑也是2号萝卜，那我还换个锤子，同时也说明有重复元素出现。



### 04.二维数组中的查找

在一个 n * m 的二维数组中，每一行都按照从左到右递增的顺序排序，每一列都按照从上到下递增的顺序排序。请完成一个高效的函数，输入这样的一个二维数组和一个整数，判断数组中是否含有该整数。

现有矩阵 matrix 如下：

[
  [1,   4,  7, 11, 15],
  [2,   5,  8, 12, 19],
  [3,   6,  9, 16, 22],
  [10, 13, 14, 17, 24],
  [18, 21, 23, 26, 30]
]
给定 target = 5，返回 true。



> 解法一：暴力算法
>
> 时间复杂度 O(N)



> 解法二： 二分查找
>
> 时间复杂度：O(Log(N))

从右上角或者左下角出发进行搜索，指针出区域时停止循环。



### 21.调整数组奇前偶后

输入一个整数数组，实现一个函数来调整该数组中数字的顺序，使得所有奇数位于数组的前半部分，所有偶数位于数组的后半部分。

> 解法一： 双指针
>
> 时间复杂度：O(N)
>
> 空间复杂度: O(1)

右边指针用来锁定奇数

左边指针用来锁定偶数

进行换位

左等于右时结束

判断奇偶的话，%2和&1都可以



### 29.顺时针打印矩阵(*)

```
输入：matrix = [[1,2,3],[4,5,6],[7,8,9]]
输出：[1,2,3,6,9,8,7,4,5]
```

> 解法一：四指针
>
> 时间复杂度：O(N)
>
> 空间复杂度：O(1)

用l,t,r,b表示四个边界，巧妙简单输出。

会做，当能否这么简便完成？

```java
    public int[] spiralOrder(int[][] matrix) {
        if(matrix.length==0) return new int[0];
        int l = 0,r=matrix[0].length-1,t=0,b=matrix.length-1;
        int x = 0;
        int[] result = new int[(r+1)*(b+1)];
        while (true){
            for(int i=l;i<=r;i++) result[x++] = matrix[t][i]; // left to right 
            if(++t>b) break; //思考本次为什么是>不是>=
            for(int i=t;i<=b;i++) result[x++] = matrix[i][r]; // top to bottom
            if(--r<l) break;
            for(int i=r;i>=l;i--) result[x++] = matrix[b][i]; // right to left
            if(--b<t) break;
            for(int i=b;i>=t;i--) result[x++] = matrix[i][l]; // bottom to top
            if(++l>r) break;
        }
        return result;
    }
```

### 51.数组中的逆序对(*)

在数组中的两个数字，如果前面一个数字大于后面的数字，则这两个数字组成一个逆序对。输入一个数组，求出这个数组中的逆序对的总数。

```
输入: [7,5,6,4]
输出: 5
```

> 解法一：暴力算法
>
> 时间复杂度: O(N^2)
>
> 空间复杂度: O(1)

> 解法二：归并计数
>
> 时间复杂度: O(N*log N)
>
> 空间复杂度: O(N)

![WeChatcabdce6c1991d80b2d0796d7fcb38c91.png](http://ww1.sinaimg.cn/large/008aPpVGgy1goxqp5jicrj316c0v4dpj.jpg)



### 53-I.在排序数组中查找数字I

统计一个数字在排序数组中出现的次数。

```
输入: nums = [5,7,7,8,8,10], target = 8
输出: 2
```

> 解法一：二分查找
>
> 时间复杂度：O(log(N))
>
> 空间复杂度: O(1)



### 53-II.0～n-1中的缺失数字(*)

一个长度为n-1的递增排序数组中的所有数字都是唯一的，并且每个数字都在范围0～n-1之内。在范围0～n-1内的n个数字中有且只有一个数字不在该数组中，请找出这个数字。

```
输入: [0,1,2,3,4,5,6,7,9]
输出: 8
```

> 解法一：二分筛选
>
> 时间复杂度: O(log(N))
>
> 空间复杂度:O (1)

1. 判断mid-1是否正常，如果不正常，right = mid-1
2. 判断mid是否正常，如果不正常 return mid;
3. 此时右边肯定不正常，left = mid+1
4. 如果前面都没有返回，数组正常。返回nums.length.

### 56-I.数组中数字出现的次数

一个整型数组 nums 里除两个数字之外，其他数字都出现了两次。请写程序找出这两个只出现一次的数字。要求时间复杂度是O(n)，空间复杂度是O(1)。

输入：nums = [4,1,4,6]
输出：[1,6] 或 [6,1]

> 解法一：位运算
>
> 时间复杂度：O(n)
>
> 空间复杂度: O(1)

思想： 所有数字异或后结果为不同两个数字的疑惑结果，按位找到不同位，分组异或得到a,b

>  解法二：借助set
>
> 时间复杂度: O(n)
>
> 空间复杂度: O(n)

### 56_II.数组中数字出现的次数(*)

在一个数组 nums 中除一个数字只出现一次之外，其他数字都出现了三次。请找出那个只出现一次的数字。

```
输入：nums = [3,4,3,3]
输出：4
```

> 解法一：Map
>
> 时间复杂度： O(n)
>
> 空间复杂度:    O(n)

> 解法二：位%3
>
> 时间复杂度: O(n)
>
> 空间复杂度:  O(32) = O(1)

```java
    public static int singleNumber(int[] nums) {
        int[] bit = new int[32];
        for(int num:nums)
            for(int i=0;i<31;i++){
                if((1&num)==1) bit[31-i] += 1;
                num>>>=1;
            }
        int res = 0;
        for(int i=0;i<31;i++){
            int count = bit[31-i]%3;
            if(count==1) res+= Math.pow(2,i);
        }
        return res;
    }
```



## 哈希表

### 48.最长不含重复字符的子字符串

请从字符串中找出一个最长的不包含重复字符的子字符串，计算该最长子字符串的长度。

```
输入: "abcabcbb"
输出: 3 
```

> 解法一：滑动窗口
>
> 时间复杂度：O(N)
>
> 空间复杂度：O(1). ch 范围0-127  O(127) = O(1)

前后指针+Map，前指针先走,遇到情况更新后指针

### 50.第一个只出现一次的字符

在字符串 s 中找出第一个只出现一次的字符。如果没有，返回一个单空格。 s 只包含小写字母。

```
s = "abaccdeff"
返回 "b"
```

> 解法一：map<Character,Boolean>
>
> 时间复杂度: O(N)
>
> 空间复杂度: O(1). ch范围0-127， O(127) = O(1)

本处用map<Character,Boolean>而不用map<Character,Integer>原因是Boolean比Integer省内存。

## 链表

### 06.从头到尾打印链表

输入一个链表的头节点，从尾到头反过来返回每个节点的值（用数组返回）。

> 解法一：两次遍历
>
> 时间复杂度:O(N)
>
> 空间复杂度:O(1)



>解法二：辅助栈法
>
>时间复杂度 ：O(N)
>
>空间复杂度：O(N) 额外栈空间



### 18.删除链表的节点

> 解法一： 前后指针
>
> 时间复杂度: O(N)
>
> 空间复杂度: O(1)

排除head是要删除的情况后。p_ahead和p_behind表示前后指针遍历。



### 22.链表中倒数的第k个节点

> 解法一：前后指针
>
> 时间复杂度：O(N)
>
> 空间复杂度 O(1)

前指针先走K次



### 24.反转链表

> 解法一：前后标记

```java
    public ListNode reverseList(ListNode head) {
        ListNode pre = null;
        ListNode next ;
        while (head!=null){
            next = head.next;
            head.next = pre;
            pre = head;
            head = next;
        }
        return pre;
    }
```



### 35.复杂链表的复制(*)

请实现 copyRandomList 函数，复制一个复杂链表。在复杂链表中，每个节点除了有一个 next 指针指向下一个节点，还有一个 random 指针指向链表中的任意节点或者 null。

![链表](https://assets.leetcode-cn.com/aliyun-lc-upload/uploads/2020/01/09/e1.png)

输入：head = [[7,null],[13,0],[11,4],[10,2],[1,0]]
输出：[[7,null],[13,0],[11,4],[10,2],[1,0]]

> 解法一：哈希表

Map<Node,Node>结构，用老的节点映射新的节点

```java
   public Node copyRandomList(Node head) {
        if(head==null) return null;
        Map<Node,Node> map = new HashMap<>();
        Node p = head;
        while (p!=null){
            Node newNode = new Node(p.val);
            map.put(p,newNode);
            p = p.next;
        }
        p = head;
        while (p!=null){
            map.get(p).next = map.get(p.next);
            map.get(p).random = map.get(p.random);
            p=p.next;
        }
        return map.get(head);
    }
```



### 52.两个链表的第一个公共节点

输入两个链表，找出它们的第一个公共节点。

> 方法一： 共同长度
>
> 时间复杂度：O(n+m)
>
> 空间复杂度: O(1)

A跑完跑B，B跑完跑A, 一定会某个值相等或同时为null.

## 数学

### 17.打印从1到最大的n位数

输入数字 `n`，按顺序打印出从 1 到最大的 n 位十进制数。比如输入 3，则打印出 1、2、3 一直到最大的 3 位数 999。

> 解法一：for循环



### 20.表示数值的字符串

略

### 43.1～n整数中1出现的次数(*)

输入一个整数 n ，求1～n这n个整数的十进制表示中1出现的次数。

例如，输入12，1～12这些整数中包含1 的数字有1、10、11和12，1一共出现了5次。

> 方法一：递归

核心思想，数字划分为高位、低位、计算base。

拿1234来说，high = 1, low=234, base = 1000

- 0-999

  本处计算直接f(base-1)

- 1000-1234

  - 只看高位	(low+1)
  - 只看低位    f(low)

high==1  :    **f(n) = f(base-1)+(low+1)+f(low)**  

当高位不为1时，如3234，high=3, low=234, base = 1000

- 0-999

  f(base-1)

- 1000-1999

  - 只看高位     base
  - 只看低位     f(base-1)

- 2000-2999

  f(base-1)

- 3000-3234

  f(low)

high!=1    :   **f(n) =  f(base-1)*high+base+f(low)**

综合上面所述：

```java
int f(int n){
    if(n<10) return 1;
    if(high==1){
        return f(base-1)+(low+1)+f(low);
    }else{
        return high*f(base-1)+base+f(low);
    }
}
```

### 44.数字序列中某一位的数字

数字以0123456789101112131415…的格式序列化到一个字符序列中。在这个序列中，第5位（从下标0开始计数）是5，第13位是1，第19位是4，等等。

请写一个函数，求任意第n位对应的数字。

> 解法

```java
0  				1
1-9				9
10-99			90
100-999		900
```

1. 先算n属于什么单位级别的,记得减去0的1
2. 计算n属于这个单位级别的第几个数
3. 计算n的余数，是这个数的第几个index



### 49.丑数(*)

我们把只包含质因子 2、3 和 5 的数称作丑数（Ugly Number）。求按从小到大的顺序的第 n 个丑数。

输入: n = 10
输出: 12
解释: 1, 2, 3, 4, 5, 6, 8, 9, 10, 12 是前 10 个丑数。

> 三指针算法

创建一个丑数数组，三个指针一开始指向a[0]=1

```java
    public int nthUglyNumber(int n) {
        int[] a = new int[n];
        a[0] = 1;
        int idx_2 = 0;
        int idx_3 = 0;
        int idx_5 = 0;
        for(int i=1;i<n;i++){
            a[i] = Math.min(Math.min(a[idx_2]*2,a[idx_3]*3),a[idx_5]*5);
            if(a[i]==a[idx_2]*2) idx_2++;
            if(a[i]==a[idx_3]*3) idx_3++;
            if(a[i]==a[idx_5]*5) idx_5++;
        }
        return a[n-1];
    }
```

重点，三个if不能用else if，必须if.



### 67.把字符串变成整数

略

## 字符串

### 05.替换空格

```
输入：s = "We are happy."
输出："We%20are%20happy."
```

> 解法一： StringBuilder

用String Builder操作，比String.replaceAll快

### 46.把数字翻译成字符串(*)

给定一个数字，我们按照如下规则把它翻译为字符串：0 翻译成 “a” ，1 翻译成 “b”，……，11 翻译成 “l”，……，25 翻译成 “z”。一个数字可能有多个翻译。请编程实现一个函数，用来计算一个数字有多少种不同的翻译方法。

> 解法一：动态规划
>
> 时间复杂度: O(N)
>
> 空间复杂度: O(N)

符合条件时dp[i] = dp[i-1]+dp[i-2];

一共25个英文字母

### 58-I.翻转单词顺序

输入一个英文句子，翻转句子中单词的顺序，但单词内字符的顺序不变。为简单起见，标点符号和普通字母一样处理。例如输入字符串"I am a student. "，则输出"student. a am I"。

> 解法

注意，每个单词要replace，最后的返回值要trim

### 58-II.左旋转字符串

字符串的左旋转操作是把字符串前面的若干个字符转移到字符串的尾部。请定义一个函数实现字符串左旋转操作的功能。比如，输入字符串"abcdefg"和数字2，该函数将返回左旋转两位得到的结果"cdefgab"。

```java
s.substring(n)+s.substring(0,n);
```

## 二分查找

### 11.旋转数组的最小数字(*)

把一个数组最开始的若干个元素搬到数组的末尾，我们称之为数组的旋转。输入一个递增排序的数组的一个旋转，输出旋转数组的最小元素。例如，数组 [3,4,5,1,2] 为 [1,2,3,4,5] 的一个旋转，该数组的最小值为1。

> 二分查找
>
> 时间复杂度： 理论O(log(N)) 实际可能出现O(N)

```java
情况一：
8 9 1 2 3 4 5 6 7
这时候mid在位置3的位置
nums[mid]<num[r]  
此时范围缩短  right = mid;

情况二：
3 4 5 6 7 8 9 1 2
此时mid在7的位置
nums[mid]>num[r]
此时范围缩短 left = mid+1;

情况三：
2,1,2,2,2
此时mid在2的位置
nums[mid]==num[r]
r--;
```



## 分治

### 25.合并两个排序的链表

```
输入：1->2->4, 1->3->4
输出：1->1->2->3->4->4
```

> 递归
>
> 时间复杂度 O(M+N)
>
> 空间复杂度O(1)

```java
// 核心代码
public ListNode mergeTwoLists(ListNode l1, ListNode l2) {
    if(l1==null) return l2;
    if(l2==null) return l1;
    if(l1.val>l2.val) {
        l2.next = mergeTwoLists(l1,l2.next);
        return l2;
    }else {
        l1.next = mergeTwoLists(l1.next,l2);
        return l1;
    }
}
```



### 36.二叉搜索树与双向链表(*)

输入一棵二叉搜索树，将该二叉搜索树转换成一个排序的循环双向链表。要求不能创建任何新的节点，只能调整树中节点指针的指向。

!['abc'](https://pic.leetcode-cn.com/1599401091-PKIjds-Picture1.png)



> 解法一：辅助ArrayList
>
> 时间复杂度: O(N)
>
> 空间复杂度: O(N) 保存引用

ArrayList保存点的引用，在依次遍历后修改左右指针

> 解法二: head,pre记录量
>
> 时间复杂度: O(N)
>
> 空间复杂度: O(1)

pre为空的时候，让head = cur.

其余时候pre.right = cur

```java
Node pre;
Node head;
public Node treeToDoublyList(Node root) {
    if(root==null) return null;
    dfs(root);
    pre.right = head;
    head.left = pre;
    return head;
}
void dfs(Node cur){
    if(cur==null) return;
    dfs(cur.left);
    if (pre==null) head = cur;
    else pre.right = cur;
    cur.left = pre;
    pre = cur;
    dfs(cur.right);
}
```



## 动态规划

### 14-I.剪绳子(*)

给你一根长度为 n 的绳子，请把绳子剪成整数长度的 m 段（m、n都是整数，n>1并且m>1），每段绳子的长度记为 k[0],k[1]...k[m-1] 。请问 k[0]*k[1]*...*k[m-1] 可能的最大乘积是多少？例如，当绳子的长度是8时，我们把它剪成长度分别为2、3、3的三段，此时得到的最大乘积是18。

> 解法一：动态规划
>
> 时间复杂度 O(N^2)
>
> 空间复杂度O(N)  

dp数组起点dp[2]=1,双重循环遍历所有情况。

```java
    public static int cuttingRope_dp(int n) {
        if(n<=1) return n;
        int[] dp = new int[n+1];
        dp[2] = 1;
        for(int i=3;i<=n;i++)
            for(int j=2;j<i;j++)
              	// 在原来dp[i], j*(i-j), j*dp[i-j]中取最大
                dp[i] = Math.max(dp[i],Math.max(j*(i-j),j*dp[i-j]));
        return dp[n];
    }
```

> 解法二: 数学归纳
>
> 时间复杂度 O(C)
>
> 空间复杂度 O(C)
>
> 核心： 每一段为e,最接近的是3时最大

```java
    public static int cuttingRope_theory(int n){
        if(n<=3) return n-1;
        int a = n/3;
        int b = n%3;
        if(b==0) return (int)Math.pow(3,a);
        if(b==1) return (int)Math.pow(3,a-1)*4;
        return (int)Math.pow(3,a)*2;
    }
```

### 14-II.剪绳子(*)

给你一根长度为 n 的绳子，请把绳子剪成整数长度的 m 段（m、n都是整数，n>1并且m>1），每段绳子的长度记为 k[0],k[1]...k[m - 1] 。请问 k[0]*k[1]*...*k[m - 1] 可能的最大乘积是多少？例如，当绳子的长度是8时，我们把它剪成长度分别为2、3、3的三段，此时得到的最大乘积是18。

答案需要取模 1e9+7（1000000007）

> 数学归纳法
>
> 溢出情况需要用long解决

```java
    public int cuttingRope(int n) {
        if(n<=3) return n-1;
        int p = 1000000007;
        int a = n/3;
        int b = n%3;
        // 本处用了long!
        long rem = 1;
        for (int i=0;i<a-1;i++)
            rem = rem*3%p;
        if(b==0) return (int)(rem*3%p);
        if(b==1) return (int)(rem*4%p);
        return (int)(rem*3*2%p);
    }
```

注意：这个版本没办法用DP做，因为取模后，max函数没有比较意义。

### 19.正则表达式匹配(*)

请实现一个函数用来匹配包含'. '和'*'的正则表达式。模式中的字符'.'表示任意一个字符，而'*'表示它前面的字符可以出现任意次（含0次）。在本题中，匹配是指字符串的所有字符匹配整个模式。例如，字符串"aaa"与模式"a.a"和"ab*ac*a"匹配，但与"aa.a"和"ab*a"均不匹配。

> 时间复杂度 O(N^2)
>
> 空间复杂度O (N^2)

```java
    public boolean isMatch_dp(String s, String p) {
        int m = s.length()+1;
        int n = p.length()+1;
        boolean[][] dp = new boolean[m][n];
        dp[0][0] = true;
        for(int j=2;j<n;j+=2)
            dp[0][j] = dp[0][j-2]&&(p.charAt(j-1)=='*');
        for(int i=1;i<m;i++)
            for(int j=1;j<n;j++){
                if(p.charAt(j-1)=='*'){
                    // 含* 三种情况
                    // * 取0
                    if(dp[i][j-2]) dp[i][j] = true;
                    // * 取1个
                    else if(dp[i-1][j]&&s.charAt(i-1)==p.charAt(j-2)) dp[i][j] = true;
                    // * 前面出现 .
                    else if(dp[i-1][j]&&p.charAt(j-2)=='.') dp[i][j] = true;
                }else {
                    // 不含* 两种情况
                    if(dp[i-1][j-1]&&s.charAt(i-1)==p.charAt(j-1)) dp[i][j] = true;
                    else if(dp[i-1][j-1]&&p.charAt(j-1)=='.') dp[i][j] = true;
                }
            }
        return dp[m-1][n-1];
    }
```

1. 初始化字符串为空，匹配串不为空的情况。  为什么偶数推导一下
2. 分为有\*情况和无\*情况。
3. 有*情况下，有三种可能
   - 前面的数字用*取0个, 此时判断dp\[i][j] = dp\[i][j-2]
   - 前面的数字用*取1个, 此时判断dp\[i][j] = dp\[i-1][j]&&s[i-1]==p[j-2]
   - *前面是‘.’符号             此时判断dp\[i][j] = dp\[i-1][j]&&p[j-2] = '.'
4. 无*的情况下
   - 两个指针是否相等         dp\[i][j] = dp\[i-1][j-1]&&s[i-1]=p[j-1]
   - 匹配串当前字符是否为'.'  dp\[i][j] = dp\[i-1][j-1]&&p[j-1] = '.'

### 42.连续子数组的最大和(*)

输入一个整型数组，数组中的一个或连续多个整数组成一个子数组。求所有子数组的和的最大值。

**示例1:**

```
输入: nums = [-2,1,-3,4,-1,2,1,-5,4]
输出: 6
解释: 连续子数组 [4,-1,2,1] 的和最大，为 6。
```

 

> 解法一：暴力算法
>
> 时间复杂度: O(N^2)
>
> 空间复杂度 O(1)

i为开头坐标，j为终止坐标， 记录最大值

> 解法二：动态规划
>
> 时间复杂度：O(N)
>
> 空间复杂度:  O(N) 可在原数组上优化成O(1)

```java
// 如果要把空间优化成O(1)，不要建立dp数组，直接在nums上修改
if(dp[i-1]<=0) dp[i] = nums[i];
else dp[i] = dp[i-1]+nums[i];
```

### 47.礼物的最大价值(*)

在一个 m*n 的棋盘的每一格都放有一个礼物，每个礼物都有一定的价值（价值大于 0）。你可以从棋盘的左上角开始拿格子里的礼物，并每次向右或者向下移动一格、直到到达棋盘的右下角。给定一个棋盘及其上面的礼物的价值，请计算你最多能拿到多少价值的礼物？

> 动态规划
>
> 时间复杂度 O(N)
>
> 空间复杂度 O(N) 原数组上优化，O(1)
>
> 核心： dp\[i][j] = Max(dp\[i-1][j], dp\[i][j-1]) + grid\[i][j]

<img src="http://ww1.sinaimg.cn/large/008aPpVGgy1gom2r912ppj30yo0h4gsy.jpg" alt="WeChat8fd299ab2514dfd4799c5470509b353d.png" style="zoom:50%;" />

> 少占内存方法DP(原地修改)

```java
    public int maxValue(int[][] grid) {
        if(grid.length==0) return 0;
        int height = grid.length;
        int width  = grid[0].length;
        for(int i=0;i<height;i++){
            for(int j=0;j<width;j++){
              // i == j == 0
                if(i==0&&j==0) continue;
              // i==0
                else if(i==0) grid[i][j] += grid[i][j-1]; 			 // j==0
                else if(j==0) grid[i][j] += grid[i-1][j];					 // i!=0&&j!=0
                else grid[i][j] += Math.max(grid[i][j-1],grid[i-1][j]);
            }
        }
        return grid[height-1][width-1];
    }
```

> 多占内存的DP(无法利用原来的空间+最上和最左的0行)

```java
    public int maxValue(int[][] grid) {
        if(grid.length==0) return 0;
        int height = grid.length+1;
        int width  = grid[0].length+1;
        int[][] dp = new int[height][width];
        for(int i=1;i<height;i++)
            for(int j=1;j<width;j++)
                dp[i][j] = Math.max(dp[i-1][j],dp[i][j-1])+grid[i-1][j-1];
        return dp[height-1][width-1];
    }
```

### 60.n个骰子的点数

把n个骰子扔在地上，所有骰子朝上一面的点数之和为s。输入n，打印出s的所有可能的值出现的概率。

> 解法一：动态规划
>
> 空间复杂度：O(n^2)
>
> 时间复杂度: O(n^2)





### 63.股票的最大利润

假设把某股票的价格按照时间先后顺序存储在数组中，请问买卖该股票一次可能获得的最大利润是多少？

输入: [7,1,5,3,6,4]
输出: 5

> 解法一：暴力算法
>
> 时间复杂度: O(N^2)
>
> 空间复杂度: O(1)
>
> 两个for，第一个选买，第二个选卖

> 解法二：DP算法
>
> 时间复杂度: O(N)
>
> 空间复杂度O(1)

保存前面出现的最低价，用当前价减去就行。



### 66.构建乘积数组

给定一个数组 A[0,1,…,n-1]，请构建一个数组 B[0,1,…,n-1]，其中 B[i] 的值是数组 A 中除了下标 i 以外的元素的积, 即 B[i]=A[0]×A[1]×…×A[i-1]×A[i+1]×…×A[n-1]。不能使用除法。

```
输入: [1,2,3,4,5]
输出: [120,60,40,30,24]
```

> 解法一：动态规划
>
> 时间复杂度: O(n)
>
> 空间复杂度: O(n)

双dp数组

假如输入[2,3,4,5]

dp_forward记录从前往后乘法[1,2,6,24]

dp_backward记录从后往前乘法[1,5,20,60]

接下来按如下规律

dp_forward[0]*dp_backward[3]

dp_forward[1]*dp_backward[2]

dp_forward[2]*dp_backward[1]

dp_forward[3]*dp_backward[0]

得到[60,40,30,24]

------

## 字符串排序

### 38.字符串的排序

> 解法一：普通递归
>
> 时间复杂度：N！

记得用StringBuilder操作，效率快一点

## 其它题型

### 57-II.和为s的两个数字

输入一个递增排序的数组和一个数字s，在数组中查找两个数，使得它们的和正好是s。如果有多对数字的和等于s，则输出任意一对即可。

> 解法一：排序+左右指针
>
> 时间复杂度: O(n * log n)
>
> 空间复杂度：O(1)

> 解法二：Map
>
> 时间复杂度: O(n)
>
> 空间复杂度: O(n)



### 57_II.和为s的连续正数序列

输入一个正整数 target ，输出所有和为 target 的连续正整数序列（至少含有两个数）。

序列内的数字由小到大排列，不同序列按照首个数字从小到大排列。

```
输入：target = 9
输出：[[2,3,4],[4,5]]
```

> 解法一： 滑动窗口思想
>
> 时间复杂度： O(n)
>
> 空间复杂度:   O(n)

本题目在n/2+1可以提前停止



### 61.扑克牌中的顺子(*)

从扑克牌中随机抽5张牌，判断是不是一个顺子，即这5张牌是不是连续的。2～10为数字本身，A为1，J为11，Q为12，K为13，而大、小王为 0 ，可以看成任意数字。A 不能视为 14。

```
输入: [1,2,3,4,5]
输出: True
```

> 方法一： 集合Set+遍历

1. 除0外，重复就返回false
2. 排除重复后，如果max-min<5 就是顺子！



### 62.圈圈中最后剩下的数字

0,1,···,n-1这n个数字排成一个圆圈，从数字0开始，每次从这个圆圈里删除第m个数字（删除后从下一个数字开始计数）。求出这个圆圈里剩下的最后一个数字。

例如，0、1、2、3、4这5个数字组成一个圆圈，从数字0开始每次删除第3个数字，则删除的前4个数字依次是2、0、4、1，因此最后剩下的数字是3。

> 解法一： ArrayList模拟
>
> 时间复杂度: O(N*M)
>
> 空间复杂度 O(N)

> 解法二：数学归纳
>
> 时间复杂度 O(N)
>
> 空间复杂度 O(1)

![WeChat6e610e0c5282dd1103045690a3c42bd9.png](http://ww1.sinaimg.cn/large/008aPpVGgy1goyxmyq57qj320816g7wh.jpg)

从只剩下一个人，index是0开始倒推到N个人

```java
    public static int lastRemaining(int n, int m) {
        // 只有一个人时，是0
        int idx = 0;
        // 从倒数第二轮开始反推
        for(int i=2;i<=n;i++)
            idx = (idx+m)%i;
        return idx;
    }
```





### 64.求1+2+3+...n(*)

求 `1+2+...+n` ，要求不能使用乘除法、for、while、if、else、switch、case等关键字及条件判断语句（A?B:C）。

> 解法一：短路与

```java
    public int sumNums(int n) {
        int sum = n;
        boolean flag = n!=0&&(sum+=sumNums(n-1))>0;
        return sum;
    }
```