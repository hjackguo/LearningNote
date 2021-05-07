# 数组

## 0001. 两数之和

给定一个整数数组 `nums` 和一个整数目标值 `target`，请你在该数组中找出 **和为目标值** 的那 **两个** 整数，并返回它们的数组下标。

> 解法一：Map记录坐标
>
> 时间复杂度：O(n)
>
> 空间复杂度：O(n)

> 解法二：暴力算法
>
> 时间复杂度：O(n^2)
>
> 空间复杂度：O(1)

## 0011.盛最多水的容器

![](https://aliyun-lc-upload.oss-cn-hangzhou.aliyuncs.com/aliyun-lc-upload/uploads/2018/07/25/question_11.jpg)

> 解法一：双指针
>
> 时间复杂度: O(n)
>
> 空间复杂度 O(1)

## 0015.三数之和

三数和为0

```
输入：nums = [-1,0,1,2,-1,-4]
输出：[[-1,-1,2],[-1,0,1]]
```

> 思路一：排序+双指针
>
> 算法复杂度：O(N^2)
>
> 空间复杂度：O(1)



## 0031.下一个排序(*)

实现获取 下一个排列 的函数，算法需要将给定数字序列重新排列成字典序中下一个更大的排列。

如果不存在下一个更大的排列，则将数字重新排列成最小的排列（即升序排列）。

必须 原地 修改，只允许使用额外常数空间。

**示例 1：**

```
输入：nums = [1,2,3]
输出：[1,3,2]
```

```java
public static void nextPermutation(int[] nums) {
    Integer last = nums[nums.length-1];
    int i = nums.length-2;
    for(;i>=0;i--){
        // 从后往前 找到前一个点比后一个点小的位置
        if(nums[i]<nums[i+1]){
            // 肯定能找到
            for(int j=nums.length-1;j>=0;j--){
                // 交换
                if(nums[j]>nums[i]){
                    int tmp = nums[j];
                    nums[j] = nums[i];
                    nums[i] = tmp;
                    break;
                }
            }
            break;
        }
    }
    // 对后面的大->小数组  转换成小->大数组
    int left = i+1;
    int right = nums.length-1;
    while (left<right){
        int tmp = nums[left];
        nums[left++] = nums[right];
        nums[right--] = tmp;
    }
}
```

1. 从后往前找，先找num[i]<num[i+1]的位置
2. num[i]位置为异常位置， 从数组最右端向左找一个大于num[i]的数，交换位置
3. 对[i+1,len-1]范围大->小的数字，排序成小->大

![](https://pic.leetcode-cn.com/e56a66ed318d1761cd8c8f9d1521f82a30c71ecc84f551912b90d8fe254c8f3d-image.png)



## 0033.搜索旋转排序数组(*)

**示例 1：**

```
输入：nums = [4,5,6,7,0,1,2], target = 0
输出：4
```

> 解法一：二分查找
>
> 时间复杂度：O(log(N))
>
> 空间复杂度：O(1)

```java
while (left<=right){
    int mid = (left+right)/2;
    if(nums[mid]==target) return mid;
  	// 本处的等于号很重要！ [3,1]找1中， 本等于号能让3划分到左边有序！  又不是目标， 从而left = mid +1!
    if(nums[left]<=nums[mid]){
      // 左边有序
      if(target>=nums[left]&&target<nums[mid]) right = mid-1;
      else left = mid+1;
    }else {
      // 右边有序
      if(target>nums[mid]&&target<=nums[right]) left = mid+1;
      else right = mid -1;
    }
}
```



# 链表

## 0002. 两数相加

给你两个 **非空** 的链表，表示两个非负的整数。它们每位数字都是按照 **逆序** 的方式存储的，并且每个节点只能存储 **一位** 数字。

![](https://assets.leetcode-cn.com/aliyun-lc-upload/uploads/2021/01/02/addtwonumber1.jpg)

> 解法一：在最长链表上操作
>
> 时间复杂度: O(m+n)
>
> 空间复杂度：O(1)

从l1和l2中先确定较长的链表

设置进位标识符

```java
	int val = p1.val+(p2==null?0:p2.val)+(forward?1:0);
	forward = (val>=10);
	p1.val = val%10;
```

## 0019.删除链表的倒数第N个结点

> 解法一：前后指针

```java
/** 前后指针，pa和pb, pb指向被删除点的前一个点。
最后如果pb==null, 返回head.next. 因为这时是第一个点被删
**/
if(count-1>n)
  	if(p_behind==null) p_behind = head;
		else p_behind = p_behind.next;
```

## 0021.合并两个有序链表

![](https://assets.leetcode.com/uploads/2020/10/03/merge_ex1.jpg)

```java
if(l1.val<l2.val) {
 	l1.next = mergeTwoLists(l1.next, l2);
 	return l1;
}else {
 	 l2.next = mergeTwoLists(l2.next, l1);
 	 return l2;
}
```

## 0023.合并K个升序链表(*)

```
[
  1->4->5,
  1->3->4,
  2->6
]
将它们合并到一个有序链表中得到。
1->1->2->3->4->4->5->6
```

> 解法一： 分治法
>
> 时间复杂度:  O(log(k)*n)
>
> 空间复杂度：O(1)

```java
public ListNode mergeKLists(ListNode[] lists) {
    int length = lists.length;
  	// 长度为0 返回null
    if(length==0) return null;
  	// 长度为1 返回原来
    if(length==1) return lists[0];
  	// 长度为2 返回合并！
    if(length==2) return mergeTwoList(lists[0],lists[1]);
  	// 长度大于2， 二分！
    int mid = length/2;
    ListNode[] l1 = new ListNode[mid];
    for(int i=0;i<mid;i++)  l1[i] = lists[i];
    ListNode[] l2 = new ListNode[length-mid];
    for(int i=mid;i<length;i++) l2[i-mid] = lists[i];
  	// 返回前2/k和后2/k的两天链表的合并！
    return mergeTwoList(mergeKLists(l1),mergeKLists(l2));
}
```

**此解法很妙！！！**



# 字符串

## 0003.无重复字符的最长子串

给定一个字符串，请你找出其中不含有重复字符的 最长子串 的长度。

```java
输入: s = "abcabcbb"
输出: 3 
解释: 因为无重复字符的最长子串是 "abc"，所以其长度为 3。
```

> 解法一：双指针+Map
>
> 时间复杂度：O(n)
>
> 空间复杂度: O(128) = O(1) 

左指针跳到上一次出现右指针位置的下一位

> 解法二：暴力算法
>
> 时间复杂度：O(n^2)
>
> 空间复杂度：O(128) = O(1)



## 0010.正则表达式匹配(待完成)

# 二分

## 0004.寻找两个正序数列的中位数(*)

给定两个大小分别为 `m` 和 `n` 的正序（从小到大）数组 `nums1` 和 `nums2`。请你找出并返回这两个正序数组的 **中位数** 。

> 思路一： CUT方法
>
> 时间复杂度: O(log(n+m))

左边切i个

右边切j = totolLeft - i 个

如果num1[i-1]>num2[j]，则重新切

```java
    public double findMedianSortedArrays(int[] nums1, int[] nums2) {
        int m = nums1.length;
        int n = nums2.length;
      	// 让nums1长度小于nums2,这样子后面搜索区间[0,m]才全都满足
        if(m>n) return findMedianSortedArrays(nums2,nums1);
        // 计数情况下，中位数划分到左边
        int totalLeft = (m+n+1)/2;
        // num1的左右范围
        int left  = 0;
        int right = m;
        while (left<right){
            // 本处+1， 取保i-1>0
            int i  = (left+right+1)/2;
            int j  = totalLeft-i;
            if(nums1[i-1]>nums2[j]) right = i-1;
            else  left = i;
        }
        int i = left;
        int j = totalLeft - i;
        int num1LeftMax = i==0?Integer.MIN_VALUE:nums1[i-1];
        int num1RightMin = i==m?Integer.MAX_VALUE:nums1[i];
        int num2LeftMax = j==0?Integer.MIN_VALUE:nums1[j-1];
        int num2RightMin = j==n?Integer.MAX_VALUE:nums1[j];
        if((m+n)%2==1) return Math.max(num1LeftMax,num2LeftMax);
        else return (double)(Math.max(num1LeftMax,num2LeftMax)+Math.min(num1RightMin,num2RightMin))/2;
    }
```

## 0005.最长回文子串

给你一个字符串 `s`，找到 `s` 中最长的回文子串。

```
输入：s = "babad"
输出："bab"
解释："aba" 同样是符合题意的答案。
```

> 解法一：DP算法
>
> 时间复杂度: O(n^2)
>
> 空间复杂度 O(n^2)

长度为0和1都是true，逐渐扩大长度判断

```java
        for(int len=1;len<s.length();len++){
            for(int start=0;start<s.length()-len;start++){
                if(s.charAt(start)==s.charAt(start+len)
                        &&(len==1||dp[start+1][start+len-1])) {
                    dp[start][start + len] = true;
                }
                else dp[start][start+len] = false;
            }
        }
```



# 回溯

## 0017.电话号码的数字组合

![](https://assets.leetcode-cn.com/aliyun-lc-upload/original_images/17_telephone_keypad.png)

```
输入：digits = "23"
输出：["ad","ae","af","bd","be","bf","cd","ce","cf"]
```

> 解决思路：递归

```java
// 号码到字符转换，不要用String 
Map<Integer,char[]> map = new HashMap<>();

// 最后的结果，用StringBuilder，不要用String
// 保存剩余号码时，用List,当List==0时，证明需要添加到总可能字符串
```

## 0022.括号生成(*)

数字 `n` 代表生成括号的对数，请你设计一个函数，用于能够生成所有可能的并且 **有效的** 括号组合。

```java
输入：n = 3
输出：["((()))","(()())","(())()","()(())","()()()"]
```

```java
// 核心代码
    public void recur(StringBuilder str,int left,int right){
        // 左边数量大于右边数量，剪枝
        if(left>right) return;
      
        if(left==0&&right==0){
            // check
            res.add(str.toString());
            return;
        }
        if(left!=0){
            str.append('(');
            recur(str,left-1,right);
            str.deleteCharAt(str.length()-1);
        }
        if(right!=0){
            str.append(')');
            recur(str,left,right-1);
            str.deleteCharAt(str.length()-1);
        }
    }
```

本题目注意！剪枝细节！当剩余左括号>剩余右括号时，执行剪枝！



# 栈

## 0020.有效的括号

给定一个只包括 `'('`，`')'`，`'{'`，`'}'`，`'['`，`']'` 的字符串 `s` ，判断字符串是否有效。

> 解法一： 栈
>
> 时间复杂度: O(N)
>
> 空间复杂度: O(N)



## 0032.最长有效括号(*)

```
输入：s = ")()())"
输出：4
解释：最长有效括号子串是 "()()"
```

> 解法一：栈
>
> 时间复杂度: O(N)
>
> 空间复杂度：O(N)

用栈模拟过程 把"(()()))"转换成“1111110”，然后统计最长连续为1的数量！



# 其它

## 0007.整数反转

给你一个 32 位的有符号整数 x ，返回将 x 中的数字部分反转后的结果。

如果反转后整数超过 32 位的有符号整数的范围 [−2^31,  2^31 − 1] ，就返回 0。



> 解法一：转成String

用异常捕捉不满足情况。

> 解法二：int解法

注意，要时刻判断临结束前一步的值，是否比Integer.Maxium大，如果大返回0，如果等于，判断最后一位是否大于7，如果是，返回0.

