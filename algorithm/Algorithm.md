### 查找复杂度类型

主要有三种计算复杂度：

- O(1) 

   哈希查找

- O(log n) 

  二分查找，二叉树

- O(n)   

  暴力搜索



### 快速幂

x^n  (此处假设x>0,n>0. 小于0情况后面自己判断)

可以进行分解成：

y = x^(n/2)

- 奇数情况下   x^n = y * y *x
- 偶数情况下   x^n = y*y

之后递归，是一个很优化的算法。

**注意**： 要用y保存值， **不可 x^n = x^(n/2)*x^(n/2), 计算量爆炸**



### 组合数C(n,m)

从n个小球中取出m个小球的所有可能性。

> 计算公式

C(n,m) =  n!/(m!*(n-m)!)

> 递归公式

C(n,m) = C(n-1,m-1)+C(n-1,m)

递归到下面两种情况可以输出:

- 当n=0时，只有一种解法
- 当m=n时，只有一种解法

例子， [a,b,c,d,e] 中取2个小球

有一个小球是a的情况，C(n-1,m-1)

不包含小球a的情况C(n-1,m)

综上所述，C(n,m) = C(n-1,m-1)+C(n-1,m)



### 排序算法

#### 堆排序

> 特点

- 最坏、最好、平均时间复杂度O(nlogn)
- 不稳定排序

> 本质

1. 完全二叉树(可用数组表示)
2. **父节点大于子节点**(大根堆)

从最底层往上开始做heapify

> 过程

1. 建立堆
2. 堆顶与堆的最后一个元素交换位置

> 实现

![WeChat19cac8d9a5c02055f7a693adfdd0b4e8.png](http://ww1.sinaimg.cn/large/008aPpVGgy1goy25na1loj30iq0ein5q.jpg)

```java
int arr[] = {10,5,8,3,4,6,7,1,2}
// 假如i = 3,
parent =  (i-1)/2;
left = i*2+1;
right = i*2+2
```

```java
public class HeapSort {
    public static void main(String[] args) {
        int[] tree = {1,5,3,2,10,11,9,0};
        HeapSort heapSort = new HeapSort();
        heapSort.quickSort(tree,tree.length);
        for(int num:tree) System.out.println(num);
    }
    void quickSort(int[] tree,int n){
        // 构建
        buildeHeap(tree,n);
        // 交换
        for(int i=n-1;i>=0;i--){
            swap(tree,i,0);
            heapify(tree,i,0);
        }

    }
    void buildeHeap(int[] tree,int n){
        // 从最后的节点找上级，推起始idx
        int finalIdx = n-1;
        int lastParent = (finalIdx-1)/2;
        for(int i=lastParent;i>=0;i--)
            heapify(tree,n,i);

    }
    void heapify(int[] tree,int n,int i){
        if(i>=n) return;
        int left = i*2+1;
        int right = i*2+2;
        int maxIdx = i;
        if(left<n&&tree[left]>tree[maxIdx]) maxIdx = left;
        if(right<n&&tree[right]>tree[maxIdx]) maxIdx = right;
        if(maxIdx!=i){
            swap(tree,maxIdx,i);
            heapify(tree,n,maxIdx);
        }

    }
    void swap(int[] tree,int i,int j){
        int tmp = tree[i];
        tree[i] = tree[j];
        tree[j] = tmp;
    }
}
```

#### 快速排序

>时间复杂度：O(N*log N) - O(N^2)
>
>空间复杂度：O(log N) - O(N)

快排最差的情况是数组倒序。(全部被分在一边)

最好的情况是，每次的pivot左右都能中分数组！

| 算法     | 稳定性 | 最好时间     | 最坏时间     | 空间复杂度                                       |
| -------- | ------ | ------------ | ------------ | ------------------------------------------------ |
| 快速排序 | ❌      | O(n * log n) | O(n^2)       | O(log n) 递归空间                                |
| 归并排序 | ✅      | O(n * log n) | O(n * log n) | O(n) 临时数组                                    |
| 堆排序   | ❌      | O(n * log n) | O(n * log n) | O(1) 虽然heapify有递归，但前一个函数已经将近结束 |

