**题目描述**
输入一个链表，输出该链表中倒数第k个结点。

**题解**

这种链表中查找取值的题型还有有序数组一般都离不开双指针或者二分

https://zhuanlan.zhihu.com/p/71643340
转一个双指针的解析

举个例子：

题目：给定一个数组和一个数s，在这个数组中找一个区间，使得这个区间之和等于s。

例如：给定的数组int x[14] = {1, 2, 3, 4, 5, 6, 7, 8, 9, 10, 11, 12, 13, 14};和一个s = 15。那么，可以找到的区间就应该有0到4， 3到5， 6到7.（注意这里的下标从0开始）

这种题其实递推+循环即可
先用一个数组sum[i]存放前i个元素的和,再通过两层循环求解

但是这种方式时间复杂度比较高

通过双指针方式来降低复杂度为O(N)


>尺取法：顾名思义，像尺子一样取一段，借用挑战书上面的话说，尺取法通常是对数组保存一对下标，即所选取的区间的左右端点，然后根据实际情况不断地推进区间左右端点以得出答案。之所以需要掌握这个技巧，是因为尺取法比直接暴力枚举区间效率高很多，尤其是数据量大的。

那么，用”尺取法“做上面这道题思路应该是这样的：

其实，这种方法很类似于蚯蚓的蠕动。

1）用一对脚标i, j。最开始都指向第一个元素。

2）如果区间i到j之和比s小，就让j往后挪一位，并把sum的值加上这个新元素。相当于蚯蚓的头向前伸了一下。

3）如果区间i到j之和比s大，就让sum减掉第一个元素。相当于蚯蚓的尾巴向前缩了一下。

4）如果i到j之和刚好等于s，则输入。

```
/*
public class ListNode {
    int val;
    ListNode next = null;
 
    ListNode(int val) {
        this.val = val;
    }
}*/
public class Solution {
    public ListNode FindKthToTail(ListNode head,int k) {
        if(head == null || k ==0 ){
            return null;
        }
 
        ListNode slow=head;
        ListNode fast=head;
        for(int i=0;i<k;i++){
            if(fast==null){
                return null;
            }
            fast=fast.next;
 
        }
        while(fast!=null){
            slow=slow.next;
            fast=fast.next;
        }
 
        return slow;
 
    }
}
```