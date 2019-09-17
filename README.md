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

104题 求树的深度，仍然是递归就可以，但是注意迭代的方法，搞懂迭代和递归的不同

```
class Solution {
    public int maxDepth(TreeNode root) {
        if (root == null) {
            return 0;
        } else {
            int lDepth = maxDepth(root.left);
            int rDepth = maxDepth(root.right);
            return lDepth > rDepth ? lDepth + 1 : rDepth + 1;
        }
    }
}
```

```
//迭代
import javafx.util.Pair;
import java.lang.Math;

class Solution {
  public int maxDepth(TreeNode root) {
    Queue<Pair<TreeNode, Integer>> stack = new LinkedList<>();
    if (root != null) {
      stack.add(new Pair(root, 1));
    }

    int depth = 0;
    while (!stack.isEmpty()) {
      Pair<TreeNode, Integer> current = stack.poll();
      root = current.getKey();
      int current_depth = current.getValue();
      if (root != null) {
        depth = Math.max(depth, current_depth);
        stack.add(new Pair(root.left, current_depth + 1));
        stack.add(new Pair(root.right, current_depth + 1));
      }
    }
    return depth;
  }
};
```

其实就是一个深度遍历

107 广度遍历 树
广度遍历一般要借助栈或者队列来实现
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
    public List<List<Integer>> levelOrderBottom(TreeNode root) {
        LinkedList<List<Integer>> list = new LinkedList<>();
        if (root == null)
            return list;
        Queue<TreeNode> queue = new LinkedList<>();
        queue.add(root);
        while (!queue.isEmpty()) {
            List<Integer> level = new ArrayList<>();
            int count = queue.size();
            for (int i = 0; i < count; i++) {
                TreeNode node = queue.poll();
                level.add(node.val);
                if (node.left != null)
                    queue.add(node.left);
                if (node.right != null)
                    queue.add(node.right);
            }
            list.addFirst(level);
        }
        return list;
    }
}
```
**2019年9月17日**
575题 分糖果
这道题主要是一道逻辑题，要拿到妹妹最多糖果种类要分情况讨论
1. 当糖果的种类多于可以分到的糖果数时，当然能得到最多的糖果种类是能分到的糖果数
2. 当糖果的种类少于可以分到的糖果数时，可以得到的糖果种类为糖果的种类
这道题我是直接先对数组进行排序，然后去重得到糖果总数，应该还有更加机智的做法
```
class Solution {
    public int distributeCandies(int[] candies) {
        int[] cate = new int[10000];
        Arrays.sort(candies);
        int len = candies.length;
        int index = 0;
        for (int i = 1; i < len; i++) {
            if(candies[i] != candies[i-1]) {
                cate[index++] = candies[i];
            }
        }
        return index+1 > len/2 ? len/2 : index+1;
    }
}
```
