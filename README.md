![](https://img.shields.io/badge/license-MIT-brightgreen)

## Java相关知识

- [ ] 基础知识
- [ ] 集合
- [ ] 多线程
- [ ] JVM
- [ ] Spring
- [ ] 锁
- [ ] 网络编程

----------------
## 其它

- [ ] 计算机网络
- [ ] Linux
- [ ] 设计模式
- [ ] 数据库
- [ ] 分布式

---------------

## LeetCode && LintCode相关必刷题目(转自面经)

**排序**
重点：快速排序、归并排序、堆排序（面试问排序基本就是这三个，理解并背熟）
一般：冒泡排序、选择排序、插入排序、拓扑排序
- [x] LintCode 463 Sort Integers（用冒泡排序、选择排序、插入排序都实现一遍）

- [x] LeetCode 148 Sort List（链表排序，最简单是用归并排序做，有余力可以尝试快速排序来做）
- [x] LeetCode 215 Kth Largest Element in an Array（方法1：堆 方法二：快速排序（推荐））
（面试题40：最小的k个数）
- [x] LeetCode 347 Top K Frequent Elements（堆排序、桶排序）
- [x] LintCode 532 Reverse Pairs（归并排序的应用）（面试题51：数组中的逆序对）
- [x] LeetCode 315 Count of Smaller Numbers After Self（归并排序的应用）
- [x] LeetCode 207 Course Schedule （拓扑排序）
- [x] LeetCode 210 Course Schedule II（拓扑排序）
- [x] leetcode 56 Merge Intervals (合并n个区间)

**数组**
- [x] 442. Find All Duplicates in an Array(面试题3：数组中重复的数字)
- [x] leetcode 387. First Unique Character in a String（面试题50：第一个只出现一次的字符）
- [x] LeetCode 189 Rotate Array（翻转数组）(面试题58：翻转字符串)
- [x] LintCode 31 Partition Array（快速排序中的一次partition）
- [x] LintCode 373 Partition Array by Odd and Even（快速排序中的一次partition）
- [x] LintCode 144 Interleaving Positive and Negative Numbers（快速排序中的一次partition）
- [x] LeetCode 54 Spiral Matrix（面试题29 顺时针打印矩阵）
- [x] LeetCode 59 Spiral Matrix II
- [x] LeetCode 53 Maximum Subarray（DP）（面试题42：连续子数组的最大和）
- [x] LeetCode 152 Maximum Product Subarray（DP）
- [x] LintCode 138 Subarray Sum（子数组之和 = 0，hashmap、空间换时间）
- [x] LintCode 139 Subarray Sum Closest（排序）
- [x] LeetCode 136 Single Number（位操作、异或）
- [x] LeetCode 137 Single Number II（位操作、通用解法）
- [x] LeetCode 260 Single Number III（位操作）（面试题56：数组中数字出现的次数）
- [x] LeetCode 263 Ugly Number
- [x] LeetCode 264 Ugly Number II（面试题49：丑数）
- [x] LeetCode 295 Find Median from Data Stream（数据流中位数，设计、最大堆、最小堆）
（面试题41：数据流中的中位数）
- [x] LeetCode 480 Sliding Window Median（滑动窗口中位数，最大堆、最小堆）
- [x] LeetCode 239 Sliding Window Maximum（双端队列）（面试题59：队列的最大值）
- [x] LeetCode 128 Longest Consecutive Sequence（hashset、空间换时间）
- [x] LeetCode 169 Majority Element（面试题39：数组中出现次数超过一半的数字）

**双指针**
使用双指针一般需要数组是有序的
- [x] LeetCode 392 Is Subsequence（双指针）
- [x] LeetCode 1 Two Sum （两个数字之和=target，hashmap，空间换时间）
- [x] leetcode 167 Two Sum II - Input array is sorted(面试题57：和为s的数字)
- [x] LeetCode 15 3Sum （排序+双指针）
- [x] LeetCode 16 3Sum Closest
- [x] LeetCode 18 4Sum
- [x] leetcode 3 Longest Substring Without Repeating Characters
（面试题48：最长不含重复字符的子字符串）（hashmap 加双指针（滑动窗口））
- [x] LeetCode 42 Trapping Rain Water

**链表**
链表题没什么好说的，就是一些指针的操作，注意以下两点就行

- [x] LeetCode 206 Reverse Linked List（递归、迭代）（面试题24 反转链表）
- [x] 面试题6  从尾到头打印链表
- [x] LeetCode 92 Reverse Linked List II
- [x] LeetCode 86 Partition List （LeetCode 328 Odd Even Linked List 一样）
- [x] LeetCode 83 Remove Duplicates from Sorted List
- [x] LeetCode 82 Remove Duplicates from Sorted List II
- [x] LeetCode 237 Delete Node in a Linked List（面试题18 删除链表的节点）
- [x] LeetCode 19 Remove Nth Node From End of List（面试题22：链表中倒数第k个节点）
- [x] LeetCode 203 Remove Linked List Elements
- [x] LeetCode 61 Rotate List
- [x] LeetCode 2 Add Two Numbers
- [x] LeetCode 445 Add Two Numbers II
- [x] LeetCode 234 Palindrome Linked List
- [x] LeetCode 24 Swap Nodes in Pairs
- [x] LeetCode 160 Intersection of Two Linked Lists（面试题52：两个链表的第一个公共结点）
- [x] LeetCode 141 Linked List Cycle（面试题23：链表中环的入口节点）
- [x] LeetCode 142 Linked List Cycle II
- [x] LeetCode 21 Merge Two Sorted Lists（面试题25：合并两个排序的链表）
- [x] LeetCode 23 Merge k Sorted Lists（变型：N个倒序链表合并）
- [x] LeetCode 138 Copy List with Random Pointer（面试题35：复杂链表的复制）
- [x] LeetCode 25 Reverse Nodes in k-Group
- [x] LeetCode 143 Reorder List
- [x] LeetCode 146 LRU Cache（非常重要的一道题，双链表 + HashMap解决）（LinkedHashMap）

**二分**
- [x] LeetCode 35 Search Insert Position
- [x] LeetCode 34 Find First and Last Position of Element in Sorted Array(面试题53：数字在排序数组中出现的次数 )
- [x] LeetCode 162 Find Peak Element
一个先递增后递减的序列，允许有重复值，找出最大值（和162题一样解法）
- [x] LeetCode 74 Search a 2D Matrix
- [x] LeetCode 240 Search a 2D Matrix II（面试题4：二维数组中的查找）
- [x] LeetCode 153 Find Minimum in Rotated Sorted Array
- [x] LeetCode 154 Find Minimum in Rotated Sorted Array II
- [x] LeetCode 33 Search in Rotated Sorted Array（面试题11 旋转数组的最小值）
- [ ] LeetCode 81 Search in Rotated Sorted Array II
- [ ] LeetCode 378 Kth Smallest Element in a Sorted Matrix
（二维矩阵、每行每列都有序，找到第k个大）
- [ ] LeetCode 4 Median of Two Sorted Arrays（两个有序数组的中位数）
- [ ] 有序数组(a,a+1,a+2,a+3,….a+m,a+m,a+m+1,….) 寻找重复数字a+m
（变型：一个整型有序数组，数组里除了一个数字之外，其他的数字都出现了两次。请写程序找出这个只出现一次的数字）

**栈**
- [ ] LeetCode 155 Min Stack（面试题30 包含min函数的栈）
- [ ] LeetCode 232 Implement Queue using Stacks（面试题9  两个栈实现队列）
- [ ] LeetCode 225 Implement Stack using Queues
- [ ] LeetCode 84 Largest Rectangle in Histogram  （单调栈）
- [ ] LeetCode 85 Maximal Rectangle （单调栈）
- [ ] LeetCode 394 Decode String
- [ ] 面试题31：栈的压入、弹出序列

**BFS**
- [ ] LeetCode 102 Binary Tree Level Order Traversal   （面试题32：层次遍历二叉树）
- [ ] LeetCode 107 Binary Tree Level Order Traversal II   （层序遍历）
- [ ] LeetCode 103 Binary Tree Zigzag Level Order Traversal    （之字形遍历）
- [ ] 987. Vertical Order Traversal of a Binary Tree（314. Binary Tree Vertical Order Traversal）（二叉树垂直遍历）（从上往下看二叉树，打印节点）（都是同一个解题思路）


**DFS**
- [ ] LeetCode 200 Number of Islands
- [ ] 面试题8  二叉树的下一个节点
- [ ] LeetCode 144 Binary Tree Preorder Traversal    （前序遍历，递归和迭代都实现一遍）
- [ ] LeetCode 94 Binary Tree Inorder Traversal    （中序遍历，递归和迭代都实现一遍）
- [ ] LeetCode 145 Binary Tree Postorder Traversal    （后序遍历，递归和迭代都实现一遍）
- [ ] LeetCode 255. Verify Preorder Sequence in Binary Search Tree（检查二叉搜索树的前序遍历）
(面试题33：二叉搜索树的后序遍历序列)
- [ ] LeetCode 105 Construct Binary Tree from Preorder and Inorder Traversal
（面试题7  根据先序中序、重建二叉树）
- [ ] LeetCode 106 Construct Binary Tree from Inorder and Postorder Traversal
- [ ] 面试题37：序列化二叉树
- [ ] LeetCode 110 Balanced Binary Tree（判断是否为平衡二叉树）
- [ ] LeetCode 98 Validate Binary Search Tree（判断是否为平衡二叉搜索树）
- [ ] LeetCode 236 Lowest Common Ancestor of a Binary Tree（最近公共祖先）
（面试题68：树中两个节点的最低公共祖先）
- [ ] LeetCode 104 Maximum Depth of Binary Tree（面试题55：二叉树的深度）
- [ ] LeetCode 111 Minimum Depth of Binary Tree（二叉树的最小深度）
- [ ] LeetCode 257 Binary Tree Paths
- [ ] LeetCode 112 Path Sum
- [ ] LeetCode 113 Path Sum II（面试题34：二叉树中和为某一值的路径）
- [ ] LeetCode 437 Path Sum III
- [ ] LeetCode 114 Flatten Binary Tree to Linked List
- [ ] LintCode 595 Binary Tree Longest Consecutive Sequence
- [ ] LintCode 614 Binary Tree Longest Consecutive Sequence II
- [ ] LintCode  375 Clone Binary Tree
- [ ] LeetCode 101 Symmetric Tree（面试题28 对称的二叉树）
- [ ] LeetCode 572 Subtree of Another Tree（面试题26：树的子结构）
- [ ] leetcode 226. Invert Binary Tree（面试题27 二叉树的镜像）
- [ ] LeetCode 124 Binary Tree Maximum Path Sum
- [ ] LeetCode 109 Convert Sorted List to Binary Search Tree
- [ ] LintCode 378 Convert Binary Search Tree to Doubly Linked List
（面试题36：二叉搜索树与双向链表）
- [ ] leetcode 230. Kth Smallest Element in a BST（面试题54：二叉搜索树的第k小节点）
- [ ] leetcode 450 Delete Node in a BST（二叉搜索树删除一个节点）

**回溯法**
- [ ] LeetCode 494 Target Sum
- [ ] LeetCode 78 Subsets
- [ ] LeetCode 90 Subsets II
- [ ] LeetCode 39 Combination Sum
- [ ] LeetCode 40 Combination Sum II
- [ ] LeetCode 46 Permutations （面试题38：字符串的排列）
- [ ] LeetCode 47 Permutations II
- [ ] LeetCode 131 Palindrome Partitioning
- [ ] leetcode 79. Word Search（面试题12 矩阵中的路径）
- [ ] 剑指offer  面试题13：机器人的运动范围

**动态规划**
- [ ] LeetCode 70 Climbing Stairs
- [ ] LeetCode 62 Unique Paths
- [ ] LeetCode 63 Unique Paths II
- [ ] LeetCode 64 Minimum Path Sum(面试题47：礼物的最大价值)
- [ ] LeetCode 120 Triangle
- [ ] LeetCode 198 House Robber
- [ ] LeetCode 213 House Robber II
- [ ] LeetCode 279 Perfect Squares
- [ ] LeetCode 221 Maximal Square
- [ ] LeetCode 300 Longest Increasing Subsequence
- [ ] LeetCode 115 Distinct Subsequences
- [ ] LeetCode 5 Longest Palindromic Substring（最长回文子串）
- [ ] LeetCode 322 Coin Change
- [ ] LeetCode 72 Edit Distance
- [ ] LeetCode 403 Frog Jump
- [ ] LeetCode 354 Russian Doll Envelopes
- [ ] LeetCode 368 Largest Divisible Subset
- [ ] LeetCode 329 Longest Increasing Path in a Matrix
（这道题我面实习时腾讯微信和微软都问过，好好掌握下，属于记忆化搜索，即DFS+剪枝）
- [ ] LeetCode 10 Regular Expression Matching（面试题19：正则表达式匹配）
- [ ] LeetCode 97 Interleaving String
- [ ] leetcode 121. Best Time to Buy and Sell Stock（面试题63：股票的最大利润）
- [ ] leetcode 91. Decode Ways(面试题46：把数字翻译成字符串)

**贪心**
贪心算法考的非常少，面试中非常少见，因为贪心算法的数学证明是很难的，短时间你不太可能证明出来，而且面试官也大概率证不出来，只有机试中才小概率会出贪心算法题，比如头条这种。所以做了以下这几个题就行。具体原因可以看一下这个链接：https://www.jiuzhang.com/qa/2100/
- [ ] LeetCode 55 Jump Game
- [ ] LeetCode 45  Jump Game II
- [ ] LeetCode 134 Gas Station
- [ ] LeetCode 135 Candy（变型：N个孩子站成一圈）

**Math题**   一些trick题，考察数学和逻辑推理
- [ ] LintCode 365 Count 1 in Binary
- [ ] LintCode 1 A + B Problem（面试题65：不用加减乘除做加法）
- [ ] LintCode 3 Digit Counts（面试题43：1~n整数中1出现的次数）
- [ ] LintCode 379 Reorder array to construct the minimum number
（面试题45：把数组排成最小的数）（leetcode 179. Largest Number）
- [ ] leetcode 400 Nth Digit（面试题44：数字序列中某一位的数字）
- [ ] 面试题15：二进制中1的个数
- [ ] 面试题62：圆圈中最后剩下的数


