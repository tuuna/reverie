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
283 移动零
这道题主要是不使用额外空间移动元素，很简单，但是注意类似这种不使用格外空间以及圆体交换的题目

**2019年9月18日**
121 买卖股票1️⃣  这种题一般是用动态规划的方式或者贪心算法来解决，注意多看一下动态规范的基本思路和写法，目前存在的问题在于还不知道哪些题应该选择DP，哪些应该选择贪心，包括DP的写法和用法
```
class Solution {
    public int maxProfit(int[] prices) {
        int len = prices.length;
        if (len <= 1)
            return 0;
        int min = prices[0], max = 0;
        
        
        for(int i = 1; i < len; i++) {
            max = Math.max(max, prices[i] - min);
            min = Math.min(min, prices[i]);
        }
        return max;
    }
}
```
**2019年9月24日**
42. 接雨水 HARD

这道题暴力思路比较简单，问题转化为求h，那么h[i]又等于左右两侧柱子的最大值中的较小值，即 h[i] = Math.min(左边柱子最大值, 右边柱子最大值)

问题的关键在于求解左边柱子最大值和右边柱子最大值, 用两个数组来表示leftMax, rightMax， 以leftMax为例，leftMax[i]代表i的左侧柱子的最大值，因此我们维护两个数组即可。

一种比较神奇的栈方法，类比括号配对
转一下大佬的分析
https://leetcode-cn.com/problems/trapping-rain-water/solution/xiang-xi-tong-su-de-si-lu-fen-xi-duo-jie-fa-by-w-8/

动态规划这块还有大问题，虽然有往动态规划前进的思路，但是每次写代码不知道怎么写

**2019年9月25日**
48.旋转图像
这道题其实就是矩阵转置题目，只是要把矩阵旋转90°
基本思路是先转置，再镜像对称
```
class Solution {
    public void rotate(int[][] matrix) {
       int martrix_row = matrix.length;
       for (int i = 0; i < martrix_row; i++) {
             for (int j = i,matrix_col = matrix[i].length; j < matrix_col; j++) {
                int k = matrix[i][j];
                matrix[i][j] = matrix[j][i];
                matrix[j][i] = k;
           }
       }
       for(int i=0;i<martrix_row;i++)
		for (int j=0;j<martrix_row/2;j++)
		{
			int temp=matrix[i][j];
			matrix[i][j]=matrix[i][martrix_row-j-1];
			matrix[i][martrix_row-j-1]=temp;
		}
    }
}
```
还有一个一开始看到这道题的想法，把边缘的一圈顺时针移动，但是不知道怎么编码
旋转，划分成4个矩阵
```
class Solution {
  public void rotate(int[][] matrix) {
    int n = matrix.length;
    for (int i = 0; i < n / 2 + n % 2; i++) {
      for (int j = 0; j < n / 2; j++) {
        int[] tmp = new int[4];
        int row = i;
        int col = j;
        for (int k = 0; k < 4; k++) {
          tmp[k] = matrix[row][col];
          int x = row;
          row = col;
          col = n - 1 - x;
        }
        for (int k = 0; k < 4; k++) {
          matrix[row][col] = tmp[(k + 3) % 4];
          int x = row;
          row = col;
          col = n - 1 - x;
        }
      }
    }
  }
}

```
双指针方法:https://www.jianshu.com/p/b5f9ac6de184  这个总结得很好

**9月26日**
49.字母异位词分组
给定一个字符串数组，将字母异位词组合在一起。字母异位词指字母相同，但排列不同的字符串。

示例:

输入: ["eat", "tea", "tan", "ate", "nat", "bat"],
输出:
[
  ["ate","eat","tea"],
  ["nat","tan"],
  ["bat"]
]

这种题一般看到的第一个思路就是把每个字符串中的字符进行排序，排序后判断是否一致，一致则放到List当中

但是关键是怎么比较好，这里就涉及到Java中HashMap的使用技巧了
```
class Solution {
    public List<List<String>> groupAnagrams(String[] strs) {
        Map<String,List> map = new HashMap<String,List>();
        for(String item : strs){
            char[] arr = item.toCharArray(); 
            Arrays.sort(arr);          
            String str=String.valueOf(arr);
            if(!map.containsKey(str)){
                map.put(str,new ArrayList()); 
            }
            map.get(str).add(item);
        }
        return new ArrayList(map.values());
    }
}
```
把键作为比较，值存原始数据
