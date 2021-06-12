# 数组

## 0001.两数之和

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

## 0034.在排序数组中查找元素的第一个和最后一个位置

```
输入：nums = [5,7,7,8,8,10], target = 8
输出：[3,4]
```

> 方法一：二分查找
>
> 时间复杂度: O(log N)
>
> 空间复杂度: O(1)



## 0042.接雨水

![](https://assets.leetcode-cn.com/aliyun-lc-upload/uploads/2018/10/22/rainwatertrap.png)

```
输入：height = [0,1,0,2,1,0,1,3,2,1,2,1]
输出：6
```

> 解法一： 左右指针
>
> 时间复杂度: O(N)
>
> 空间复杂度: O(1)

```java
public static int trap(int[] height) {
    int left = 0;
    int right = height.length-1;
    int maxLeft = 0;
    int maxRight = 0;
    int sum = 0;
    while (left<=right){
      	// 记录左右最大值
        maxLeft = Math.max(maxLeft,height[left]);
        maxRight = Math.max(maxRight,height[right]);
      	// 移动小的值，移开时才记录进去sum
        if(height[left]<height[right]){
            sum+=maxLeft-height[left];
            left++;
        }else {
            sum+=maxRight-height[right];
            right--;
        }
    }
    return sum;
}
```

## 0048.旋转图像

![img](https://assets.leetcode.com/uploads/2020/08/28/mat1.jpg)

> 解法一： 两次镜像

先左斜线镜像，然后中线镜像。



## 0049.字母异位词分组

```java
输入: ["eat", "tea", "tan", "ate", "nat", "bat"]
输出:
[
  ["ate","eat","tea"],
  ["nat","tan"],
  ["bat"]
]
```

> 解法一：TreeMap<Character,Integer> charMap判断

```java
public static List<List<String>> groupAnagrams(String[] strs) {
    HashMap<TreeMap,List<String>> map = new HashMap<>();
    for(String str:strs){
        TreeMap<Character,Integer> charMap = new TreeMap<>();
        for(Character ch:str.toCharArray())
            charMap.put(ch,charMap.getOrDefault(ch,0)+1);
        List<String> list = map.getOrDefault(charMap,new ArrayList<>());
        list.add(str);
        map.put(charMap,list);
    }
    return new ArrayList<>(map.values());
}
```



> 解法二：质数乘法(时间更快)
>
> 缺点： 可能溢出！

用26个**质数**代表26个字母，乘积相同的就是同一个组！！！

## 0055.跳跃游戏(*)

给定一个非负整数数组 nums ，你最初位于数组的 第一个下标 。

数组中的每个元素代表你在该位置可以跳跃的最大长度。

判断你是否能够到达最后一个下标。

示例 1：

```
输入：nums = [2,3,1,1,4]
输出：true
解释：可以先跳 1 步，从下标 0 到达下标 1, 然后再从下标 1 跳 3 步到达最后一个下标。
```

>解法一：遍历数组，判断最远距离和当前下标
>
>时间复杂度 ：O(n)
>
>空间复杂度 :  O(1)

```java
public static boolean canJump(int[] nums) {
  	// 记录最远距离
    int maxFufure = 0;
    for(int i=0;i<nums.length;i++){
        if(maxFufure<i) return false;
      	// 更新最远距离
        maxFufure =  Math.max(maxFufure,i+nums[i]);
    }
    return true;
}
```

**核心思想**：能够到达当前这点的话，最远距离大于等于当前坐标

## 0056.合并区间

```
输入：intervals = [[1,3],[2,6],[8,10],[15,18]]
输出：[[1,6],[8,10],[15,18]]
解释：区间 [1,3] 和 [2,6] 重叠, 将它们合并为 [1,6].
```

> 解法一：快排后合并

先用快排队起始数值进行排序后，看是否存在交叉。 然后分交叉和不交叉情况处理。

注意，交叉情况下，区间的闭部分选最远的。

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

## 0076.最小覆盖子串(*)

给你一个字符串 s 、一个字符串 t 。返回 s 中涵盖 t 所有字符的最小子串。如果 s 中不存在涵盖 t 所有字符的子串，则返回空字符串 "" 。

注意：如果 s 中存在这样的子串，我们保证它是唯一的答案。

```java
输入：s = "ADOBECODEBANC", t = "ABC"
输出："BANC"
```

> 解法一： 双指针+2个Map+Rest Count记录量
>
> 时间复杂度：O(N)
>
> 空间复杂度：O(N)

```java
public static String minWindow(String s, String t) {
    if(s==null||s.equals("")||t==null||t.equals("")) return "";
    // 本处restCount用于判断当前还缺多少个匹配，减少了遍历patternMap的时间！
    int left=0, right=0,restCount = t.length(),lengthS = s.length();
    String currentStr = null;
    int currentLength = -1;
    // 初始化
    Map<Character,Integer> winMap = new HashMap<>();
    Map<Character,Integer> pattenMap = new HashMap<>();
    for(Character ch:t.toCharArray())
        pattenMap.put(ch,pattenMap.getOrDefault(ch,0)+1);
    // 移动右指针
    while (right<lengthS){
        char ch = s.charAt(right);
        // 过滤无关的
        if(!pattenMap.containsKey(ch)){
            right++;
            continue;
        }
        // 有效的进入
        winMap.put(ch,winMap.getOrDefault(ch,0)+1);
        // 剩余需要寻找的字母-1
        if(winMap.get(ch)<=pattenMap.getOrDefault(ch,0)) restCount--;
        // 找完了后，左指针收缩
        while (restCount==0){
            if(currentLength==-1||currentLength>right-left+1) {
                currentStr = s.substring(left, right + 1);
                currentLength = right-left+1;
            }
            // 收缩左指针
            char leftChar = s.charAt(left);
            if(pattenMap.containsKey(leftChar)) {
                // 在里面，判断去除然后操作
                winMap.put(leftChar, winMap.get(leftChar) - 1);
                // 造成影响
                if (winMap.get(leftChar) < pattenMap.get(leftChar)) restCount++;
            }
            // 直接收缩leftChar
            left++;
        }
        right++;
    }
    return currentStr==null?"":currentStr;
}
```

核心，pattenMap用于记录匹配串分布，winMap用于记录当前窗口中相关字符情况。 如果没有记录量，每次都要遍历pattenMap每个key判断是否全满足，耗时！ 因此，我们引入restCount信号量，用来记录待匹配的字符数，用map协助修改，当restCount==0时，当前窗口就满足条件。

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
        // 左边剩余数量大于右边剩余数量，剪枝
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

## 0039.组合总和

```
输入：candidates = [2,3,5], target = 8,
所求解集为：
[
  [2,2,2,2],
  [2,3,3],
  [3,5]
]
```

```java
class Solution {
    int[] candidates;
    int target;
    List<Integer> currentList;
    List<List<Integer>> finalLists;
    public List<List<Integer>> combinationSum(int[] candidates, int target) {
        this.candidates = candidates;
        this.target = target;
        currentList = new ArrayList<>();
        finalLists = new ArrayList<>();
        search();
        return finalLists;
    }

    void search(){
        if(target<0) return;
        if(target==0){
            finalLists.add(new ArrayList<>(currentList));
            return;
        }
        for(int i=0;i<candidates.length;i++){
            /** 跳过重复出现的情况
            本处注意 [2,3,5] target=8例子中， 如果currentList=[2,3]
            2进来触碰到跳过条件！不能return， 因为3进来满足，所以continue
            **/
            if(currentList.size()!=0&&currentList.get(currentList.size()-1)>candidates[i]) continue;

            if(target>=candidates[i]){
                currentList.add(candidates[i]);
                target -= candidates[i];
                search();
                target += candidates[i];
              	// remove比sublist省时间！  实测！！！
                currentList.remove(currentList.size()-1);
            }
        }
    }
}
```

主要注意点： 跳过地方用continue而不是 return!不然会漏掉很多组合！

## 0046.全排列

```
输入：nums = [1,2,3]
输出：[[1,2,3],[1,3,2],[2,1,3],[2,3,1],[3,1,2],[3,2,1]]
```

把Array转换成List操作，会快一点。



## 0078.子集

给你一个整数数组 `nums` ，数组中的元素 **互不相同** 。返回该数组所有可能的子集（幂集）。

解集 **不能** 包含重复的子集。你可以按 **任意顺序** 返回解集。

```
输入：nums = [1,2,3]
输出：[[],[1],[2],[1,2],[3],[1,3],[2,3],[1,2,3]]
```

> 解法一： 回溯法

```java
public class A0078_Subsets {
    int[] nums;
    int length;
    List<Integer> current;
    List<List<Integer>> result;
    public List<List<Integer>> subsets(int[] nums) {
        this.nums = nums;
        length = nums.length;
        current = new ArrayList<>();
        result = new ArrayList<>();
        recur(0);
        return result;
    }
    public void recur(int idx){
        result.add(new ArrayList<>(current));
        for(int i=idx;i< nums.length;i++){
            current.add(nums[i]);
            recur(i+1);
            current.remove(current.size()-1);
        }
    }
}
```

## 0079.单词搜索

![](https://assets.leetcode.com/uploads/2020/11/04/word2.jpg)

```
输入：board = [["A","B","C","E"],["S","F","C","S"],["A","D","E","E"]], word = "ABCCED"
输出：true
```

> 解法一：回溯法

```java
class Solution {
    char[] chs;
    char[][] board;
    int haveMatchCount;
    int length;
    int width;
    int height;
    boolean finish;
    public boolean exist(char[][] board, String word) {
        this.board = board;
        if (board.length==0) return false;
        chs = word.toCharArray();
        haveMatchCount = 0;
        length = chs.length;
        height = board.length;
        width = board[0].length;
        for(int i=0;i<board.length;i++)
            for(int j=0;j<board[0].length;j++)
            search(i,j);
        return finish;
    }
    void search(int i,int j){
        if(haveMatchCount==length){
            finish = true;
            return;
        }
        if(i<0||j<0||i>=height||j>=width||finish) return;
        if(board[i][j]=='/') return;
        if(board[i][j] == chs[haveMatchCount]){
            haveMatchCount++;
            char ch = board[i][j];
            board[i][j] = '/';
            search(i+1,j);
            search(i-1,j);
            search(i,j-1);
            search(i,j+1);
            board[i][j] = ch;
            haveMatchCount--;
        }
    }
}
```



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



## 0084.柱状图中最大的柜形(*)

给定 *n* 个非负整数，用来表示柱状图中各个柱子的高度。每个柱子彼此相邻，且宽度为 1 。

求在该柱状图中，能够勾勒出来的矩形的最大面积。

![](https://assets.leetcode-cn.com/aliyun-lc-upload/uploads/2018/10/12/histogram.png)

![](https://assets.leetcode-cn.com/aliyun-lc-upload/uploads/2018/10/12/histogram_area.png)

> 解法一：找最短那根切分(超时)
>
> 时间复杂度: O(N^2)
>
> 空间复杂度: O(1)

第一次找到index为1(因为本根最低)的做切分，计算[0,0]和[2,5]区间内的最大面积。



> 解法二：暴力解法
>
> 时间复杂度: O(N^2)
>
> 空间复杂度: O(1)

先固定第一根，左右指针扩散找比第一根高的，最后底*第一根高度。切换到第二根。



> 解法三： 单调递增栈
>
> 时间复杂度 : O(N)
>
> 空间复杂度: O(N)

[csdn讲解单调递增栈](https://blog.csdn.net/Zolewit/article/details/88863970)

```java
    public static int largestRectangleArea_stack(int[] heights) {
        heights = Arrays.copyOf(heights,heights.length+1);
        Stack<Integer> stack = new Stack<>();
        int maxArea = 0;
        for(int i=0;i<heights.length;i++){
            while (!stack.isEmpty()&&heights[stack.peek()]>heights[i]){
                int top = stack.pop();
                maxArea = Math.max(maxArea,heights[top]*(stack.isEmpty()?i:(i-stack.peek()-1)));
            }
            stack.push(i);
        }
        return maxArea;
    }
```

计算顺序，自己动手推算！

1. 2*1
2. 6*(4-2-1)
3. 5*(4-1-1)
4. 3*(6-4-1)
5. 2*(6-1-1)
6. 1*6



## 0085.最大矩形(*)

![](https://assets.leetcode.com/uploads/2020/09/14/maximal.jpg)

> 解法一： 高度转换，递增栈方法调用column次, 参考0084
>
> 时间复杂度: O(M*N)
>
> 空间复杂度: O(N)

![](https://pic.leetcode-cn.com/aabb1b287134cf950aa80526806ef4025e3920d57d237c0369ed34fae83e2690-image.png)

```java
public int maximalRectangle(char[][] matrix) {
    if(matrix.length==0) return 0;
    int[] heights = new int[matrix[0].length+1];
    int maxArea = 0;
    for(int row=0;row<matrix.length;row++){
        for(int i=0;i<heights.length-1;i++)
            heights[i] = matrix[row][i]=='1'?heights[i]+1:0;
        maxArea = Math.max(maxArea,calculateMaxArea(heights));
    }
    return maxArea;
}
public int calculateMaxArea(int[] heights){
    int maxArea = 0;
    Stack<Integer> stack = new Stack<>();
    for(int i=0;i<heights.length;i++){
        while (!stack.isEmpty()&&heights[stack.peek()]>heights[i]){
            int top = stack.pop();
            maxArea = Math.max(maxArea,heights[top]*(stack.isEmpty()?i:i-stack.peek()-1));
        }
        stack.push(i);
    }
    return maxArea;
}
```



# 动态规划

## 0054.最大序列和

给定一个整数数组 `nums` ，找到一个具有最大和的连续子数组（子数组最少包含一个元素），返回其最大和。

**示例 1：**

```
输入：nums = [-2,1,-3,4,-1,2,1,-5,4]
输出：6
解释：连续子数组 [4,-1,2,1] 的和最大，为 6 。
```

> 解法一：动态规划
>
> 时间复杂度：O(N)
>
> 空间复杂度：O(N) 可以优化成O(1),更新上一个状态量

```java
public int maxSubArray(int[] nums) {
    int[] dp = new int[nums.length];
    dp[0] = nums[0];
    int max = dp[0];
    for(int i=1;i<nums.length;i++) {
      // 核心
        dp[i] = dp[i - 1] < 0 ? nums[i] : nums[i] + dp[i - 1];
        max = Math.max(max,dp[i]);
    }
    return max;
}
```

## 0062.不同路径(*)

机器人每次只能向下或者向右移动一步。机器人试图达到网格的右下角（在下图中标记为 “Finish” ）。

问总共有多少条不同的路径？

![](https://assets.leetcode.com/uploads/2018/10/22/robot_maze.png)

> 解法一： 模拟深度搜索
>
> 算法复杂度：指数级别
>
> 空间复杂度: O(1)

```java
    int total = 0;
    int m;
    int n;

    public int uniquePaths_recursion(int m, int n) {
        this.m = m;
        this.n = n;
        move(0,0);
        return total;
    }
    void move(int currentM,int currentN){
        if(currentM>=m) return;
        if(currentN>=n) return;
        if(currentM==m-1&&currentN==n-1){
            total++;
            return;
        }
        move(currentM+1,currentN);
        move(currentM,currentN+1);
    }
```

> 解法二: 排列组合
>
> 算法复杂度: O(N)
>
> 空间复杂度: O(1)
>
> 有溢出风险

一共走m+n-2步，从其中挑选m-1或n-1出来

C(m+n-2,Math.min(m-1,n-1))

注意：本处要判断用m-1还是n-1，减少计算量，防止long溢出

本算法还是存在溢出风险！

```java
    public static int uniquePaths_permutation(int m,int n){
        if(n<m){
            int tmp = n;
            n=m;
            m=tmp;
        }
        long result = 1;
        int count = m-1;
        int initial = m+n-2;
        while (count>0){
            result *= initial;
            count--;
            initial--;
        }
        count = m-1;
        while (count>0){
            result/=count;
            count--;
        }
        return (int) result;
    }
```

> 解法三：动态规划
>
> 计算复杂度：O(m*n)
>
> 空间复杂度: O(m*n)

dp[m]'[n] = dp[m-1]'[n]+dp[m]'[n-1]

```java
    public static int uniquePaths_dp(int m,int n){
        int[][] dp = new int[m][n];
        for(int i=0;i<m;i++)
            dp[i][0] = 1;
        for(int i=0;i<n;i++)
            dp[0][i] = 1;
        for(int i=1;i<m;i++)
            for (int j=1;j<n;j++){
                dp[i][j] = dp[i-1][j]+dp[i][j-1];
            }
        return dp[m-1][n-1];
    }
```

## 0064.最小路径和

![](https://assets.leetcode.com/uploads/2020/11/05/minpath.jpg)

给定一个包含非负整数的 `*m* x *n*` 网格 `grid` ，请找出一条从左上角到右下角的路径，使得路径上的数字总和为最小。

**说明：**每次只能向下或者向右移动一步。

> 解法一：动态规划
>
> 算法复杂度: O(m*n)
>
> 空间复杂度: O(m*n)



## 0070.爬楼梯

假设你正在爬楼梯。需要 *n* 阶你才能到达楼顶。

每次你可以爬 1 或 2 个台阶。你有多少种不同的方法可以爬到楼顶呢？

> 解法一：动态规划
>
> 时间复杂度: O(N)
>
> 空间复杂度：O(1)



## 0072.编辑距离(*)

给你两个单词 word1 和 word2，请你计算出将 word1 转换成 word2 所使用的最少操作数 。

你可以对一个单词进行如下三种操作：

插入一个字符
删除一个字符
替换一个字符

```java
输入：word1 = "intention", word2 = "execution"
输出：5
解释：
intention -> inention (删除 't')
inention -> enention (将 'i' 替换为 'e')
enention -> exention (将 'n' 替换为 'x')
exention -> exection (将 'n' 替换为 'c')
exection -> execution (插入 'u')
```

> 解法一： 动态规划
>
> 时间复杂度: O(N^2)
>
> 空间复杂度: O(N^2)

![5271623298544_.pic](https://cdn.jsdelivr.net/gh/HuanjieGuo/PicGo/img/5271623298544_.pic.jpg)

```java
public int minDistance(String word1, String word2) {
    int len1 = word1.length();
    int len2 = word2.length();
    int[][] dp = new int[len1+1][len2+1];
    // 空和空匹配
    dp[0][0] = 0;
    // 插入
    for(int i=1;i<=len2;i++)
        dp[0][i] = dp[0][i-1]+1;
    // 删除
    for(int i=1;i<=len1;i++)
        dp[i][0] = dp[i-1][0]+1;
    // 正常
    for(int i=1;i<=len1;i++)
        for(int j=1;j<=len2;j++)
            /**
             i和j字符相同，操作次数等于 dp[i-1][j-1]
             */
            if(word1.charAt(i-1)==word2.charAt(j-1)) dp[i][j] = dp[i-1][j-1];
            /**
             i和j字符不同.
             dp[i-1][j-1] 表示替换
             dp[i-1][j] 表示删除
             dp[i][j-1] 表示插入

             word1为"horse", word2为"ros"
             1. dp[i-1][j-1] 先将word1前4个字母horse转换成word2前2个字母ro, 再将word1第五个字母e替换成word2第三个字母s
             2. dp[i][j-1] 先将word1前5个字母转成word2前2个字母ro,然后插入word2第三个字母s
             3. dp[i-1][j] 先将word1前4个字母转成word2 即ros,然后删除最后一个
             三个操作选择最小那个 + 1 就行！
             */
            else dp[i][j] = Math.min(Math.min(dp[i-1][j-1],dp[i-1][j]),dp[i][j-1])+1;
    return dp[len1][len2];
}
```

# 排序

## 0075.颜色分类(快排)

给定一个包含红色、白色和蓝色，一共 n 个元素的数组，原地对它们进行排序，使得相同颜色的元素相邻，并按照红色、白色、蓝色顺序排列。

此题中，我们使用整数 0、 1 和 2 分别表示红色、白色和蓝色。

```java
输入：nums = [2,0,2,1,1,0]
输出：[0,0,1,1,2,2]
```

复习下快速排序 O(N*Log(N) )



# 树

## 0094.二叉树的中序遍历

![](https://assets.leetcode.com/uploads/2020/09/15/inorder_1.jpg)

```java
输入：root = [1,null,2,3]
输出：[1,3,2]
```



## 0096.不同的二叉搜索树(*)

给你一个整数 `n` ，求恰由 `n` 个节点组成且节点值从 `1` 到 `n` 互不相同的 **二叉搜索树** 有多少种？返回满足题意的二叉搜索树的种数。

![](https://assets.leetcode.com/uploads/2021/01/18/uniquebstn3.jpg)

```
输入：n = 3
输出：5
```

> 解法一：DP算法

从0个节点的空树模拟至n的节点的树

选出一个根结点，然后不断变动左节点个数，所有情况累积

```java
public static int numTrees(int n) {
    int[] dp = new int[n+1];
    dp[0] = 1;
    dp[1] = 1;
    // 几个节点
    for(int i=2;i<=n;i++)
        // 树的左边分几个
        for(int left=0;left<=i-1;left++)
            // i-1表示去除根结点，i-1-left表示右边个数
            dp[i] += dp[left]*dp[i-1-left];
    return dp[n];
}
```

## 0098.验证二叉搜索树(*)

给定一个二叉树，判断其是否是一个有效的二叉搜索树。

假设一个二叉搜索树具有如下特征：

节点的左子树只包含小于当前节点的数。
节点的右子树只包含大于当前节点的数。
所有左子树和右子树自身必须也是二叉搜索树。

```java
输入:
    5
   / \
  1   4
     / \
    3   6
输出: false
解释: 5的右边存在比5小的
```

> 解法一： 中序遍历+递增判断

```java
public class A0098_IsValidBST {
    /**
     * 中序遍历为升序
     * @param root
     * @return
     */
    Integer last;
    boolean isValid;
    public boolean isValidBST(TreeNode root) {
        isValid = true;
        inOrderSearch(root);
        return isValid;
    }
    void inOrderSearch(TreeNode node){
        if(node==null||!isValid) return;
        inOrderSearch(node.left);
        if(last==null) last = node.val;
        else {
            // 本处注意， 只设置为false，不要 isValid = node.val>last, 会出错
            if(node.val<=last) isValid = false;
            last = node.val;
        }
        inOrderSearch(node.right);
    }
}
```

## 0101.对称二叉树

例如，二叉树 `[1,2,2,3,4,4,3]` 是对称的。

```
    1
   / \
  2   2
 / \ / \
3  4 4  3
```

> 解法一：值对比+左比右 右比左

```java
public boolean isSymmetric(TreeNode root) {
    if(root==null) return true;
    return compare(root.left,root.right);
}
public boolean compare(TreeNode a,TreeNode b){
    if(a==null&&b==null) return true;
    if(a==null||b==null) return false;
    return a.val==b.val
            &&compare(a.left,b.right)
            &&compare(a.right,b.left);
}
```

## 0102.二叉树的层序遍历

示例：
二叉树：[3,9,20,null,null,15,7],

    		3
       / \
      9  20
        /  \
       15   7
```
返回其层序遍历结果：
[
  [3],
  [9,20],
  [15,7]
]
```

father队列和child队列！

## 0104.二叉树的最大深度

示例：
给定二叉树 [3,9,20,null,null,15,7]，

    		3
       / \
      9  20
        /  \
       15   7
返回它的最大深度 3 。

```java
public int maxDepth(TreeNode root) {
    if(root==null)  return 0;
    return 1+Math.max(maxDepth(root.left),maxDepth(root.right));
}
```



# 其它

## 0007.整数反转

给你一个 32 位的有符号整数 x ，返回将 x 中的数字部分反转后的结果。

如果反转后整数超过 32 位的有符号整数的范围 [−2^31,  2^31 − 1] ，就返回 0。



> 解法一：转成String

用异常捕捉不满足情况。

> 解法二：int解法

注意，要时刻判断临结束前一步的值，是否比Integer.Maxium大，如果大返回0，如果等于，判断最后一位是否大于7，如果是，返回0.



