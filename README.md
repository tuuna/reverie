# leetcode_sign
Leetcode打卡记录

**2019年9月9日**

342,371题  简单题，主要是位运算

342题主要是4的幂次方相关的一些规则，比如2的幂次方规则n&(n-1) == 0以及4的幂次方的规则(n-1)%3 == 0

371题主要是位运算，加减乘除注意都要学会，很重要

https://www.jianshu.com/p/7bba031b11e7

**2019年9月10日**
198题  简单题
这道题的正经思路是使用DP，但是这道题根据题目所给的规则可以得出

这道题有一道变式，就是加入不是算出所有路径，而是只能比较相邻两家，这个应该怎么做要思考一下，目前想到的是利用HashMap

**2019年9月11日**
206 链表翻转，题目很基础，但是要注意两种方式，迭代以及递归

**2019年9月16日**

108题 整型数组转换为二叉查找树

```
/**
 * Definition for a binary tree node.
 * public class TreeNode {
 *     int val;
 *     TreeNode left;
 *     TreeNode right;
 *     TreeNode(int x) { val = x; }
 * }
 */
class Solution {
    public TreeNode sortedArrayToBST(int[] nums) {
        return nums == null ? null : getTree(nums, 0, nums.length - 1);
    }
    
    public TreeNode getTree(int[] nums, int left, int right) {
        if (left > right) {
            return null;
        }
        
        int mid = left + ((right - left) >> 1);
        
        TreeNode root = new TreeNode(nums[mid]);
        root.left = getTree(nums, left, mid - 1);
        root.right = getTree(nums, mid + 1, right);
        return root;
    }
}
```

这里面有几个必须要注意的地方，首先这种题采用分治递归的思想，和二分的题思路差不多
然后要注意>> << 左移右移这两种运算符的优先级是小于`+号`,`-号`的，所以必须要给括号



    public TreeNode sortedArrayToBST(int[] nums) {题
    public TreeNode sortedArrayToBST(int[] nums) {
