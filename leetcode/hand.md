[TOC]

### 1. 数组

#### 1. **找出数组中出现次数大于数组长度一半和 N / K 的数**

```java
//hashmap解决，时间空间复杂度较高(均为O(n))
class Solution {
    public int majorityElement(int[] nums) {
        HashMap<Integer, Integer> map = new HashMap<>();
        int limit = nums.length / 2;
        for (int i = 0; i < nums.length; i++) {
            if (!map.containsKey(nums[i])) {
                map.put(nums[i],map.getOrDefault(nums[i], 0) + 1);
            } else {
                map.put(nums[i], map.get(nums[i]) + 1);
            
            }
            if(i>=limit&&map.get(nums[i])>limit) return nums[i];

        }
        return 0;
            
        
    }
}
```



#### 2. 合并区间

```java
/*
对起点和终点分别进行排序，将起点和终点一一对应形成一个数组。
如果没有overlap,返回当前起点和终点
如果有overlap,判断以下条件
找出最小的起点
如果当前终点大于下一个数组的起点的时候，比较当前终点和下一个终点的大小，取为right
返回满足要求的区间[[left,right]]
*/
class Solution {
    public int[][] merge(int[][] intervals) {
        List<int[]> res = new ArrayList<>();
        if (intervals.length == 0 || intervals == null) return res.toArray(new int[0][]);
        // 对起点终点进行排序
        Arrays.sort(intervals, (a, b) -> a[0] - b[0]);
        int i = 0;
        while (i < intervals.length) {
            int left = intervals[i][0];
            int right = intervals[i][1];
            // 如果有重叠，循环判断哪个起点满足条件
            while (i < intervals.length - 1 && intervals[i + 1][0] <= right) {
                i++;
                right = Math.max(right, intervals[i][1]);
            }
            // 将现在的区间放进res里面
            res.add(new int[]{left, right});
            // 接着判断下一个区间
            i++;
        }
        return res.toArray(new int[0][]);
    }
}
```



#### 3. **调整数组顺序使奇数位于偶数前面**

```java
//双指针(从左往右寻找偶数，从右向左寻找奇数)
public int[] exchange(int[] nums) {
        if (nums == null || nums.length == 0)
            return nums;
        int left = 0;
        int right = nums.length - 1;
        while (left < right) {
            while (left < right && (nums[left] & 1) == 1) {
                left++;
            }
            while (left < right && (nums[right] & 1) == 0) {
                right--;
            }
            if (left < right) {
                nums[left] ^= nums[right];
                nums[right] ^= nums[left];
                nums[left] ^= nums[right];
            }
        }
        return nums;
}
//快慢指针(慢指针slow存放下一个奇数应该存放的位置，快指针fast往前搜索奇数，搜索到之后然后就和slow指向的值交换)
public int[] exchange(int[] nums) {
        int slow = 0, fast = 0;
        while (fast < nums.length) {
            if ((nums[fast] & 1) == 1) {//奇数
                if (slow != fast) {
                    nums[slow] ^= nums[fast];
                    nums[fast] ^= nums[slow];
                    nums[slow] ^= nums[fast];
                }
                slow++;
            }
            fast++;
        }
        return nums;
}
```



#### 4. 数组的度

```java
//出现次数最多的元素的连续子数组长度为：该元素的最大索引 - 该元素的最小索引 + 1 ， 找最小长度
class Solution {
    public int findShortestSubArray(int[] nums) {
        Map<Integer, Integer> left = new HashMap(),
            right = new HashMap(), count = new HashMap();

        for (int i = 0; i < nums.length; i++) {
            int x = nums[i];
            if (left.get(x) == null) left.put(x, i);
            right.put(x, i);
            count.put(x, count.getOrDefault(x, 0) + 1);
        }

        int ans = nums.length;
        int degree = Collections.max(count.values());
        for (int x: count.keySet()) {
            if (count.get(x) == degree) {
                ans = Math.min(ans, right.get(x) - left.get(x) + 1);
            }
        }
        return ans;
    }
}
```



#### 5. **求一个数组中的第 k 小 / 大的数**

```java
//(求第k个最大元素)用最小堆，java默认是最小堆(也可以直接排序做)
class Solution {
    public int findKthLargest(int[] nums, int k) {
        PriorityQueue<Integer> queue = new PriorityQueue<>(new Comparator<Integer>() {
            @Override
            public int compare(Integer t1, Integer t2) {
                return t1 - t2;
            }
        });
         for (int val : nums) {
            queue.add(val);
            if (queue.size() > k)
                queue.poll();
        }
        return queue.peek();
    }
}
时间复杂度：O(nlogn)，建堆的时间代价是 O(n)O(n)，删除的总代价是 O(k \log n)O(klogn)，因为 k < nk<n，故渐进时间复杂为 O(n + k \log n) = O(n \log n)O(n+klogn)=O(nlogn)。
空间复杂度：O(logn)，即递归使用栈空间的空间代价。
```

#### 6. 将一个整数数组划分为 K 个相等的子集问题

```java
class Solution {
    public boolean search(int[] groups, int row, int[] nums, int target) {
        if (row < 0) return true;
        int v = nums[row--];
        for (int i = 0; i < groups.length; i++) {
            if (groups[i] + v <= target) {
                groups[i] += v;
                if (search(groups, row, nums, target)) return true;
                groups[i] -= v;
            }
            if (groups[i] == 0) break;
        }
        return false;
    }

    public boolean canPartitionKSubsets(int[] nums, int k) {
        int sum = Arrays.stream(nums).sum();
        if (sum % k > 0) return false;
        int target = sum / k;

        Arrays.sort(nums);
        int row = nums.length - 1;
        if (nums[row] > target) return false;
        while (row >= 0 && nums[row] == target) {
            row--;
            k--;
        }
        return search(new int[k], row, nums, target);
    }
}
```



#### 7. 旋转数组中的最小数字

```java
public class Solution {

    // [3, 4, 5, 1, 2]
    // [1, 2, 3, 4, 5]
    // 不能使用左边数与中间数比较，这种做法不能有效地减治

    // [1, 2, 3, 4, 5]
    // [3, 4, 5, 1, 2]
    // [2, 3, 4, 5 ,1]

    public int minArray(int[] numbers) {
        int len = numbers.length;
        if (len == 0) {
            return 0;
        }
        int left = 0;
        int right = len - 1;
        while (left < right) {
            int mid = (left + right) >>> 1;
            if (numbers[mid] > numbers[right]) {
                // [3, 4, 5, 1, 2]，mid 以及 mid 的左边一定不是最小数字
                // 下一轮搜索区间是 [mid + 1, right]
                left = mid + 1;
            } else if (numbers[mid] == numbers[right]) {
                // 只能把 right 排除掉，下一轮搜索区间是 [left, right - 1]
                right = right - 1;
            } else {
                // 此时 numbers[mid] < numbers[right]
                // mid 的右边一定不是最小数字，mid 有可能是，下一轮搜索区间是 [left, mid]
                right = mid;
            }
        }

        // 最小数字一定在数组中，因此不用后处理
        return numbers[left];
    }
}
```

#### 8. 在二维数组中查找一个数

```java
//O(m+n) O(1)
public boolean searchMatrix3(int[][] matrix, int target) {
        if (matrix == null || matrix.length == 0 || matrix[0].length == 0)
            return false;
        int x = matrix[0].length - 1, y = 0;
        while(y < matrix.length && x >= 0){
            if(matrix[y][x] < target)
                y++;
            else if (matrix[y][x] > target)
                x--;
            else return true;
        }
        return false;
 }
```

#### 9. 找出数组中重复的数字

```java
class Solution {
    public int findRepeatNumber(int[] nums) {
        Set<Integer> set = new HashSet<Integer>();
        int repeat = -1;
        for (int num : nums) {
            if (!set.add(num)) {
                repeat = num;
                break;
            }
        }
        return repeat;
    }
}
```

#### 10. 找出数组中只出现一次的那个数，其他都出现两次

```java
class Solution {
    public int singleNumber(int[] nums) {
        int single = 0;
        for (int num : nums) {
            single ^= num;
        }
        return single;
    }
}
```

> 11-15 题都是 **子数组** 问题：在条件下，每一个位置的元素都会作为子数组的开头或者结尾元素，那么遍历完整个数组，结果一定在其中：

#### 11. 子数组最大累乘积：给定一个 double 类型的数组 arr，其中的元素可正、可负、可 0，返回子数组累乘的最大乘积

```java
class Solution {
    public int maxProduct(int[] nums) {
        int max = Integer.MIN_VALUE, imax = 1, imin = 1;
        for(int i=0; i<nums.length; i++){
            if(nums[i] < 0){ 
              int tmp = imax;
              imax = imin;
              imin = tmp;
            }
            imax = Math.max(imax*nums[i], nums[i]);
            imin = Math.min(imin*nums[i], nums[i]);
            
            max = Math.max(max, imax);
        }
        return max;
    }
}
```

#### 12. 需要排序的最短子数组长度

#### 13. 最长的可整合子数组的长度

#### 14. 最短无序连续子数组

```java
class Solution {
    public int findUnsortedSubarray(int[] nums) {
        int len = nums.length;
        int max = nums[0];
        int min = nums[len-1];
        int l = 0, r = -1;
        for(int i=0;i<len;i++){
            if(max>nums[i]){
                r = i;
            }else{
                max = nums[i];
            }
            if(min<nums[len-i-1]){
                l = len-i-1;
            }else{
                min = nums[len-i-1];
            }
        }
        return r-l+1;
    }
}
```

#### 15. **连续子数组的最大和**

```java
//dp 时间复杂度O(n)，空间复杂度O(n)
class Solution {
    public int maxSubArray(int[] nums) {
        int[] dp = new int[nums.length];
        int max = Integer.MIN_VALUE;
        for (int i = 0; i < nums.length; i++) {
            dp[i] = i-1>=0&&dp[i-1]>0 ? dp[i-1] + nums[i]: nums[i];
            max = Math.max(dp[i], max);
        }
        return max;
    }
}

//空间复杂度O(1)
class Solution {
    public int maxSubArray(int[] nums) {
        int res = nums[0];
        for(int i = 1; i < nums.length; i++) {
            nums[i] += Math.max(nums[i - 1], 0);
            res = Math.max(res, nums[i]);
        }
        return res;
    }
}
```

#### 16. 合并两个有序数组

```java
class Solution {
  public void merge(int[] nums1, int m, int[] nums2, int n) {
    // Make a copy of nums1.
    int [] nums1_copy = new int[m];
    System.arraycopy(nums1, 0, nums1_copy, 0, m);

    // Two get pointers for nums1_copy and nums2.
    int p1 = 0;
    int p2 = 0;

    // Set pointer for nums1
    int p = 0;

    // Compare elements from nums1_copy and nums2
    // and add the smallest one into nums1.
    while ((p1 < m) && (p2 < n))
      nums1[p++] = (nums1_copy[p1] < nums2[p2]) ? nums1_copy[p1++] : nums2[p2++];

    // if there are still elements to add
    if (p1 < m)
      System.arraycopy(nums1_copy, p1, nums1, p1 + p2, m + n - p1 - p2);
    if (p2 < n)
      System.arraycopy(nums2, p2, nums1, p1 + p2, m + n - p1 - p2);
  }
}
```



### 2. 字符串

#### 1. **字符串的排列与组合**

##### 字符串的排列

```java
//这个固定位置然后用dfs，不是很好理解
class Solution {
    List<String> res = new LinkedList<>();
    char[] c;
    public String[] permutation(String s) {
        c = s.toCharArray();
        dfs(0);
        return res.toArray(new String[res.size()]);
    }
    void dfs(int x) {
        if(x == c.length - 1) {
            res.add(String.valueOf(c)); // 添加排列方案
            return;
        }
        HashSet<Character> set = new HashSet<>();
        for(int i = x; i < c.length; i++) {
            if(set.contains(c[i])) continue; // 重复，因此剪枝
            set.add(c[i]);
            swap(i, x); // 交换，将 c[i] 固定在第 x 位 
            dfs(x + 1); // 开启固定第 x + 1 位字符
            swap(i, x); // 恢复交换
        }
    }
    void swap(int a, int b) {
        char tmp = c[a];
        c[a] = c[b];
        c[b] = tmp;
    }
}

//回溯
import java.util.ArrayList;
import java.util.Arrays;
import java.util.List;

public class Solution {

    public String[] permutation(String s) {
        int len = s.length();
        if (len == 0) {
            return new String[0];
        }

        // 转换成字符数组是常见的做法
        char[] charArr = s.toCharArray();
        // 排序是为了去重方便
        Arrays.sort(charArr);

        // 由于操作的都是字符，使用 StringBuilder
        StringBuilder path = new StringBuilder();
        boolean[] used = new boolean[len];

        // 为了方便收集结果，使用动态数组
        List<String> res = new ArrayList<>();
        dfs(charArr, len, 0, used, path, res);

        // 记得转成字符串数组
        return res.toArray(new String[0]);
    }

    /**
     * @param charArr 字符数组
     * @param len     字符数组的长度
     * @param depth   当前递归深度
     * @param used    当前字符是否使用
     * @param path    从根结点到叶子结点的路径
     * @param res     保存结果集的变量
     */
    private void dfs(char[] charArr,
                     int len,
                     int depth,
                     boolean[] used,
                     StringBuilder path,
                     List<String> res) {
        if (depth == len) {
            // path.toString() 恰好生成了新的字符对象
            res.add(path.toString());
            return;
        }
        for (int i = 0; i < len; i++) {
            if (!used[i]) {
                if (i > 0 && charArr[i] == charArr[i - 1] && !used[i - 1]) {
                    continue;
                }
                used[i] = true;
                path.append(charArr[i]);

                dfs(charArr, len, depth + 1, used, path, res);

                // 递归完成以后，需要撤销选择，递归方法执行之前做了什么，递归方法执行以后就需要做相应的逆向操作
                path.deleteCharAt(path.length() - 1);
                used[i] = false;
            }
        }
    }

    public static void main(String[] args) {
        Solution solution = new Solution();
        String[] res = solution.permutation("aba");
        System.out.println(Arrays.toString(res));
    }
}
```

##### 字符串的组合

```java
//给定两个整数 n 和 k，返回 1 ... n 中所有可能的 k 个数的组合。

import java.util.ArrayList;
import java.util.List;
import java.util.Stack;

public class Solution {

    private List<List<Integer>> res = new ArrayList<>();

    private void findCombinations(int n, int k, int begin, Stack<Integer> pre) {
        if (pre.size() == k) {
            // 够数了，就添加到结果集中
            res.add(new ArrayList<>(pre));
            return;
        }
        // 关键在于分析出 i 的上界
        for (int i = begin; i <= n; i++) {
            pre.add(i);
            findCombinations(n, k, i + 1, pre);
            pre.pop();
        }
    }

    public List<List<Integer>> combine(int n, int k) {
        // 特判
        if (n <= 0 || k <= 0 || n < k) {
            return res;
        }
        // 从 1 开始是题目的设定
        findCombinations(n, k, 1, new Stack<>());
        return res;
    }
}

```

```java
//无重复字符串的排列组合。编写一种方法，计算某字符串的所有排列组合，字符串每个字符均不相同。
class Solution {
    private List<String> list = new ArrayList<>();
    private StringBuilder path = new StringBuilder();
    private boolean[] used = new boolean[10];

    public String[] permutation(String S) {
        dfs(S);
        String[] res = new String[list.size()];
        for (int i = 0; i < list.size(); i++) {
            res[i] = list.get(i);
        }

        return res;
    }

    private void dfs(String S) {
        if (path.length() == S.length()) {
            list.add(new String(path.toString()));
            return;
        }
        for (int i = 0; i < S.length(); i++) {
            if (!used[i]) {
                path.append(S.charAt(i));
                used[i] = true;
                dfs(S);
                used[i] = false;
                path.deleteCharAt(path.length() - 1);
             }
        }
    }
}
```

```java
//有重复字符串的排列组合。编写一种方法，计算某字符串的所有排列组合。
class Solution {
    public String[] permutation(String S) {
        Set<String> res = new HashSet<>();
        combination(res,S,new StringBuilder(),new boolean[S.length()]);
        return res.toArray(new String[res.size()]);
    }

    private void combination(Set<String> res, String s, StringBuilder sb, boolean[] marked){
        if(sb.length() == s.length()){
            res.add(sb.toString());
            return;
        }
        for(int i = 0;i<s.length();i++){
            if(!marked[i]){
                marked[i] = true;
                sb.append(s.charAt(i));
                combination(res,s,sb,marked);
                sb.deleteCharAt(sb.length()-1);
                marked[i] = false;
            }
        }
    }
}

```



#### 2. **最长回文子串**

```java
//中心扩散O(n^2) O
public class Solution {

    public String longestPalindrome(String s) {
        int len = s.length();
        if (len < 2) {
            return s;
        }
        int maxLen = 1;
        String res = s.substring(0, 1);
        // 中心位置枚举到 len - 2 即可
        for (int i = 0; i < len - 1; i++) {
            String oddStr = centerSpread(s, i, i);
            String evenStr = centerSpread(s, i, i + 1);
            String maxLenStr = oddStr.length() > evenStr.length() ? oddStr : evenStr;
            if (maxLenStr.length() > maxLen) {
                maxLen = maxLenStr.length();
                res = maxLenStr;
            }
        }
        return res;
    }

    private String centerSpread(String s, int left, int right) {
        // left = right 的时候，此时回文中心是一个字符，回文串的长度是奇数
        // right = left + 1 的时候，此时回文中心是一个空隙，回文串的长度是偶数
        int len = s.length();
        int i = left;
        int j = right;
        while (i >= 0 && j < len) {
            if (s.charAt(i) == s.charAt(j)) {
                i--;
                j++;
            } else {
                break;
            }
        }
        // 这里要小心，跳出 while 循环时，恰好满足 s.charAt(i) != s.charAt(j)，因此不能取 i，不能取 j
        return s.substring(i + 1, j);
    }
}
```



#### 3. 正则表达式匹配：实现一个函数用来匹配包括'.'和'*'的正则表达式

```java
如果 BB 的最后一个字符是正常字符，那就是看 A[n-1]A[n−1] 是否等于 B[m-1]B[m−1]，相等则看 A_{0..n-2}A 
0..n−2
​	
  与 B_{0..m-2}B 
0..m−2
​	
 ，不等则是不能匹配，这就是子问题。

如果 BB 的最后一个字符是.，它能匹配任意字符，直接看 A_{0..n-2}A 
0..n−2
​	
  与 B_{0..m-2}B 
0..m−2
​	
 

如果 BB 的最后一个字符是*它代表 B[m-2]=cB[m−2]=c 可以重复0次或多次，它们是一个整体 c*c∗

情况一：A[n-1]A[n−1] 是 00 个 cc，BB 最后两个字符废了，能否匹配取决于 A_{0..n-1}A 
0..n−1
​	
  和 B_{0..m-3}B 
0..m−3
​	
  是否匹配
情况二：A[n-1]A[n−1] 是多个 cc 中的最后一个（这种情况必须 A[n-1]=cA[n−1]=c 或者 c='.'c= 
′
 . 
′
 ），所以 AA 匹配完往前挪一个，BB 继续匹配，因为可以匹配多个，继续看 A_{0..n-2}A 
0..n−2
​	
  和 B_{0..m-1}B 
0..m−1
​	
 是否匹配。
class Solution {
    public boolean isMatch(String A, String B) {
        int n = A.length();
        int m = B.length();
        boolean[][] f = new boolean[n + 1][m + 1];

        for (int i = 0; i <= n; i++) {
            for (int j = 0; j <= m; j++) {
                //分成空正则和非空正则两种
                if (j == 0) {
                    f[i][j] = i == 0;
                } else {
                    //非空正则分为两种情况 * 和 非*
                    if (B.charAt(j - 1) != '*') {
                        if (i > 0 && (A.charAt(i - 1) == B.charAt(j - 1) || B.charAt(j - 1) == '.')) {
                            f[i][j] = f[i - 1][j - 1];
                        }
                    } else {
                        //碰到 * 了，分为看和不看两种情况
                        //不看
                        if (j >= 2) {
                            f[i][j] |= f[i][j - 2];
                        }
                        //看
                        if (i >= 1 && j >= 2 && (A.charAt(i - 1) == B.charAt(j - 2) || B.charAt(j - 2) == '.')) {
                            f[i][j] |= f[i - 1][j];
                        }
                    }
                }
            }
        }
        return f[n][m];
    }
}
```



#### 4. 替换空格

```java
class Solution {
    public String replaceSpace(String s) {
        int length = s.length();
        char[] array = new char[length * 3];
        int size = 0;
        for (int i = 0; i < length; i++) {
            char c = s.charAt(i);
            if (c == ' ') {
                array[size++] = '%';
                array[size++] = '2';
                array[size++] = '0';
            } else {
                array[size++] = c;
            }
        }
        String newStr = new String(array, 0, size);
        return newStr;
    }
}
```

#### 5. 字符串的翻转和旋转及其应用

#### 6. 字符串解码

#### 7. 无重复字符的最长子串

#### 8. 字符串的最长公共子串和最长公共子序列

#### 9. 请实现一个函数用来判断字符串是否表示数值

#### 10. 判断一个字符串是否是一个合法的 IPV4

### 3. 哈希表

#### 1. **手写一个简单的 HashMap**

```java
class MyHashMap {
	/** Initialize your data structure here. */
	List<List<Integer>> keys;
	List<List<Integer>> values;
	int length = 500; //桶的个数
	
    public MyHashMap() {
    	keys = new ArrayList<List<Integer>>();
        values = new ArrayList<List<Integer>>();
    	for(int i=0; i<length; i++) {
    		keys.add(i, new ArrayList<Integer>());
    		values.add(i, new ArrayList<Integer>());
    	}
    }
	
    /** value will always be non-negative. */
    public void put(int key, int value) {
    	if (!contains(key)) {    		
    		keys.get(key%length).add(key);
    		values.get(key%length).add(value);
    	} else {
    		remove(key);
    		keys.get(key%length).add(key);
    		values.get(key%length).add(value);
    	}

    }
    
    /** Returns the value to which the specified key is mapped, or -1 if this map contains no mapping for the key */
    public int get(int key) {
    	if(contains(key)) {
			//找到key在list中对应的位置，value也在相同的位置
    		int index = keys.get(key%length).indexOf((Integer)key);
    		return values.get(key%length).get(index);
    	}
    	return -1;
    	
    }
    
    /** Removes the mapping of the specified value key if this map contains a mapping for the key */
    public void remove(int key) {
    	if(contains(key)) {		
	    	int index = keys.get(key%length).indexOf((Integer)key);
	    	keys.get(key%length).remove(index);
	    	values.get(key%length).remove(index);
    	}
    }
	
    public boolean contains(int key) {
        return keys.get(key%length).contains(key);
    }
}
```



#### 2. 和为 K 的子数组：给定一个整数数组和一个整数 k，你需要找到该数组中和为 k 的连续的子数组的个数

#### 3. 一种接收消息并按顺序打印的结构设计：单链表 + 两个HashMap

#### 4. 哈希表增加 setAll 功能

### 4. 栈

#### 1. 用固定大小的数组实现栈

#### 2. **如何仅用队列实现栈**

```java
class MyStack {

    Queue<Integer> queue1;
    Queue<Integer> queue2;
    int top;

    /** Initialize your data structure here. */
    public MyStack() {
        queue1 = new LinkedList<>();
        queue2 = new LinkedList<>();
    }
    
    /** Push element x onto stack. */
    public void push(int x) {
        queue1.offer(x);
        top = x;
    }
    
    /** Removes the element on top of the stack and returns that element. */
    public int pop() {
        while (queue1.size() > 1) {
            top = queue1.remove();
            queue2.offer(top);
        }
        int result = queue1.remove();
        Queue<Integer> temp = queue1;
        queue1 = queue2;
        queue2 = temp;
        return result;
    }
    
    /** Get the top element. */
    public int top() {
        return top;
    }
    
    /** Returns whether the stack is empty. */
    public boolean empty() {
        return queue1.isEmpty();
    }
}

/**
 * Your MyStack object will be instantiated and called as such:
 * MyStack obj = new MyStack();
 * obj.push(x);
 * int param_2 = obj.pop();
 * int param_3 = obj.top();
 * boolean param_4 = obj.empty();
 */
```



#### 3. 最小值栈：能够返回栈中最小元素的栈

#### 4. 栈的压入、弹出序列：输入两个整数序列，第一个序列表示栈的压入顺序，请判断第二个序列是否可能为该栈的弹出顺序

> 5-8题是单调栈结构问题：

#### 5. 单调栈结构的实现

#### 6. 直方图中的最大矩形面积

#### 7. 求最大子矩阵的大小

#### 8. **可见山峰问题**

```

```



### 5. 队列

#### 1. 用固定大小的数组实现队列

#### 2. **如何仅用栈结构实现队列**

```java
class MyQueue {

    public Stack<Integer> s1 = new Stack<Integer>();
    public Stack<Integer> s2 = new Stack<Integer>();
    /** Initialize your data structure here. */
    public MyQueue() {
    }
    
    /** Push element x to the back of queue. */
    public void push(int x) {
        while (!s2.empty()){
            s1.push(s2.pop());
        }
        s1.push(x);
    }
    
    /** Removes the element from in front of queue and returns that element. */
    public int pop() {
        while (!s1.empty()){
            s2.push(s1.pop());
        }
        return s2.pop();
    }
    
    /** Get the front element. */
    public int peek() {
        while (!s1.empty()){
            s2.push(s1.pop());
        }
        return s2.peek();
    }
    
    /** Returns whether the queue is empty. */
    public boolean empty() {
        while (!s1.empty()) {
            s2.push(s1.pop());
        }
        return s2.empty();
    }
}
```

### 6. 链表

#### 1. 反转单向链表

```java
/**
 * Definition for singly-linked list.
 * public class ListNode {
 *     int val;
 *     ListNode next;
 *     ListNode(int x) { val = x; }
 * }
 */
//迭代
class Solution {
	public ListNode reverseList(ListNode head) {
		//申请节点，pre和 cur，pre指向null
		ListNode pre = null;
		ListNode cur = head;
		ListNode tmp = null;
		while(cur!=null) {
			//记录当前节点的下一个节点
			tmp = cur.next;
			//然后将当前节点指向pre
			cur.next = pre;
			//pre和cur节点都前进一位
			pre = cur;
			cur = tmp;
		}
		return pre;
	}
}

//递归
class Solution {
	public ListNode reverseList(ListNode head) {
		//递归终止条件是当前为空，或者下一个节点为空
		if(head==null || head.next==null) {
			return head;
		}
		//这里的cur就是最后一个节点
		ListNode cur = reverseList(head.next);
		//这里请配合动画演示理解
		//如果链表是 1->2->3->4->5，那么此时的cur就是5
		//而head是4，head的下一个是5，下下一个是空
		//所以head.next.next 就是5->4
		head.next.next = head;
		//防止链表循环，需要将head.next设置为空
		head.next = null;
		//每层递归函数都返回cur，也就是最后一个节点
		return cur;
	}
}

```



#### 2. 反转双向链表

#### 3. **K 个一组翻转链表**

```java
/*
1、找到待翻转的k个节点（注意：若剩余数量小于 k 的话，则不需要反转，因此直接返回待翻转部分的头结点即可）。
2、对其进行翻转。并返回翻转后的头结点（注意：翻转为左闭又开区间，所以本轮操作的尾结点其实就是下一轮操作的头结点）。
3、对下一轮 k 个节点也进行翻转操作。
4、将上一轮翻转后的尾结点指向下一轮翻转后的头节点，即将每一轮翻转的k的节点连接起来。
*/
   public ListNode reverseKGroup(ListNode head, int k) {
        if (head == null || head.next == null) {
            return head;
        }
        ListNode tail = head;
        for (int i = 0; i < k; i++) {
            //剩余数量小于k的话，则不需要反转。
            if (tail == null) {
                return head;
            }
            tail = tail.next;
        }
        // 反转前 k 个元素
        ListNode newHead = reverse(head, tail);
        //下一轮的开始的地方就是tail
        head.next = reverseKGroup(tail, k);

        return newHead;
    }

    /*
    左闭又开区间
     */
    private ListNode reverse(ListNode head, ListNode tail) {
        ListNode pre = null;
        ListNode next = null;
        while (head != tail) {
            next = head.next;
            head.next = pre;
            pre = head;
            head = next;
        }
        return pre;

    }
```



#### 4. **合并两个排序的链表**

```java
/*
如果 l1 或者 l2 一开始就是空链表 ，那么没有任何操作需要合并，所以我们只需要返回非空链表。否则，我们要判断 l1 和 l2 哪一个链表的头节点的值更小，然后递归地决定下一个添加到结果里的节点。如果两个链表有一个为空，递归结束。
O(n+m)
*/
class Solution {
    public ListNode mergeTwoLists(ListNode l1, ListNode l2) {
        if (l1 == null) {
            return l2;
        }
        else if (l2 == null) {
            return l1;
        }
        else if (l1.val < l2.val) {
            l1.next = mergeTwoLists(l1.next, l2);
            return l1;
        }
        else {
            l2.next = mergeTwoLists(l1, l2.next);
            return l2;
        }

    }
}
```



#### 5. **链表中倒数第 K 个节点**

```java
/**
 * Definition for singly-linked list.
 * public class ListNode {
 *     int val;
 *     ListNode next;
 *     ListNode(int x) { val = x; }
 * }
 */
class Solution {
    public ListNode getKthFromEnd(ListNode head, int k) {
        ListNode fast = head, slow = head;
        while (fast != null && k > 0) {
            fast = fast.next;
            k--;
        }
        while (fast != null) {

            fast = fast.next;
            slow = slow.next;
        }

        return slow;
    }
}
```

#### 6. O(1) 时间内删除一个节点

#### 7. 删除链表中重复的节点

#### 8. **从尾到头打印链表**

```java
//递归
class Solution {
    ArrayList<Integer> tmp = new ArrayList<Integer>();
    public int[] reversePrint(ListNode head) {
        recur(head);
        int[] res = new int[tmp.size()];
        for(int i = 0; i < res.length; i++)
            res[i] = tmp.get(i);
        return res;
    }
    void recur(ListNode head) {
        if(head == null) return;
        recur(head.next);
        tmp.add(head.val);
    }
}

//辅助栈
class Solution {
    public int[] reversePrint(ListNode head) {
        LinkedList<Integer> stack = new LinkedList<Integer>();
        while(head != null) {
            stack.addLast(head.val);
            head = head.next;
        }
        int[] res = new int[stack.size()];
        for(int i = 0; i < res.length; i++)
            res[i] = stack.removeLast();
    return res;
    }
}
```



#### 9. 判断一个链表是否为回文结构

#### 10. 给出两个有序链表的头结点，打印出两个链表中相同的元素

#### 11. 将单向链表按某值划分成左边小、中间相等、右边大的形式

#### 12. 复制含有随机指针节点的链表

#### 13. **环形链表**

```java
public boolean hasCycle(ListNode head) {
    if (head == null || head.next == null) {
        return false;
    }
    ListNode slow = head;
    ListNode fast = head.next;
    while (slow != fast) {
        if (fast == null || fast.next == null) {
            return false;
        }
        slow = slow.next;
        fast = fast.next.next;
    }
    return true;
}
```

#### 14. 链表中环的入口节点

```java
/*
 public class ListNode {
    int val;
    ListNode next = null;
 
    ListNode(int val) {
        this.val = val;
    }
}
*/
public class Solution {
 
    public ListNode EntryNodeOfLoop(ListNode pHead)
    {
        if(pHead == null){
            return null;
        }
        // 1.判断链表中有环
        ListNode l=pHead,r=pHead;
        boolean flag = false;
        while(r != null && r.next!=null){
            l=l.next;
            r=r.next.next;
            if(l==r){
                flag=true;
                break;
            }
        }
        if(!flag){
            return null;
        }else{
            // 2.得到环中节点的数目
            int n=1;
            r=r.next;
            while(l!=r){
                r=r.next;
                n++;
            }
            // 3.找到环中的入口节点
            l=r=pHead;
            for(int i=0;i<n;i++){
                r=r.next;
            }
            while(l!=r){
                l=l.next;
                r=r.next;
            }
            return l;
        }
    }
}
```

#### 15. 复杂链表的复制

### 7. 树

> 1-5 是二叉树的遍历：

#### 1. **二叉树的前序、中序、后序遍历的递归实现**

```java
public class TreeNode {
      int val;
      TreeNode left;
      TreeNode right;
      TreeNode(int x) { val = x; }
 }
//前序
public List<Integer> preorderTraversal(TreeNode root) {
    List<Integer> res = new ArrayList<>();
    preorder(root, res);
    return res;
}

private void preorder(TreeNode root, List<Integer> res){
    if(root != null){
        res.add(root.val);
        preorder(root.left, res);
        preorder(root.right, res);
    }
}

//中序
public List<Integer> inorderTraversal(TreeNode root) {
    List<Integer> res = new ArrayList<>();
    preorder(root, res);
    return res;
}

private void inorder(TreeNode root, List<Integer> res){
    if(root != null){
        preorder(root.left, res);
        res.add(root.val);
        preorder(root.right, res);
    }
}

//后序
public List<Integer> postorderTraversal(TreeNode root) {
    List<Integer> res = new ArrayList<>();
    preorder(root, res);
    return res;
}

private void postorder(TreeNode root, List<Integer> res){
    if(root != null){
        preorder(root.left, res);
        preorder(root.right, res);
        res.add(root.val);
    }
}
```

#### 2. **二叉树的前序、中序、后序遍历的非递归实现**

```java
public class TreeNode {
      int val;
      TreeNode left;
      TreeNode right;
      TreeNode(int x) { val = x; }
 }

//前序
class Solution {
  public List<Integer> preorderTraversal(TreeNode root) {
    List<Integer> res = new ArrayList<Integer>();
        if (root == null) {
            return res;
        }
        Stack<TreeNode> stack = new Stack<TreeNode>();
        stack.push(root);
        while (!stack.isEmpty()) {
            TreeNode node = stack.pop();
            res.add(Integer.valueOf(node.val));
            if (node.right != null) {
                stack.push(node.right);
            }
            if (node.left != null) {
                stack.push(node.left);
            }
        }
        return res;
  }
}

//中序
class Solution {
  public List<Integer> preorderTraversal(TreeNode root) {
    List<Integer> res = new ArrayList<Integer>();
        if (root == null) {
            return res;
        }
        TreeNode cur = root;
				Stack<TreeNode> stack = new Stack<>();
				while (!stack.isEmpty() || cur != null) {
            while (cur != null) {
              stack.push(cur);
              cur = cur.left;
            }
            TreeNode node = stack.pop();
            res.add(Integer.valueOf(node.val));
            if (node.right != null) {
              cur = node.right;
            }
				}
				return res;
  	}
}
	
//后序
class Solution {
  public List<Integer> preorderTraversal(TreeNode root) {
    List<Integer> res = new ArrayList<Integer>();
    if (head == null) {
			return res;
		}
		Stack<TreeNode> stack1 = new Stack<>();
		Stack<TreeNode> stack2 = new Stack<>();
		stack1.push(root);
		while (!stack1.isEmpty()) {
			TreeNode node = stack1.pop();
			stack2.push(node);
			if (node.left != null) {
				stack1.push(node.left);
			}
			if (node.right != null) {
				stack1.push(node.right);
			}
		}
		while (!stack2.isEmpty()) {
      res.add(stack2.pop().val);
		}
    return res;
}
```

#### 3. **二叉树的层序遍历**

```java
//BFS
import java.util.*;	
class Solution {
	public List<List<Integer>> levelOrder(TreeNode root) {
		if(root==null) {
			return new ArrayList<List<Integer>>();
		}
		
		List<List<Integer>> res = new ArrayList<List<Integer>>();
		LinkedList<TreeNode> queue = new LinkedList<TreeNode>();
		//将根节点放入队列中，然后不断遍历队列
		queue.add(root);
		while(queue.size()>0) {
			//获取当前队列的长度，这个长度相当于 当前这一层的节点个数
			int size = queue.size();
			ArrayList<Integer> tmp = new ArrayList<Integer>();
			//将队列中的元素都拿出来(也就是获取这一层的节点)，放到临时list中
			//如果节点的左/右子树不为空，也放入队列中
			for(int i=0;i<size;++i) {
				TreeNode t = queue.remove();
				tmp.add(t.val);
				if(t.left!=null) {
					queue.add(t.left);
				}
				if(t.right!=null) {
					queue.add(t.right);
				}
			}
			//将临时list加入最终返回结果中
			res.add(tmp);
		}
		return res;
	}
}

//DFS
import java.util.*;	
class Solution {
	public List<List<Integer>> levelOrder(TreeNode root) {
		if(root==null) {
			return new ArrayList<List<Integer>>();
		}
		//用来存放最终结果
		List<List<Integer>> res = new ArrayList<List<Integer>>();
		dfs(1,root,res);
		return res;
	}
	
	void dfs(int index,TreeNode root, List<List<Integer>> res) {
		//假设res是[ [1],[2,3] ]， index是3，就再插入一个空list放到res中
		if(res.size()<index) {
			res.add(new ArrayList<Integer>());
		}
		//将当前节点的值加入到res中，index代表当前层，假设index是3，节点值是99
		//res是[ [1],[2,3] [4] ]，加入后res就变为 [ [1],[2,3] [4,99] ]
		res.get(index-1).add(root.val);
		//递归的处理左子树，右子树，同时将层数index+1
		if(root.left!=null) {
			dfs(index+1, root.left, res);
		}
		if(root.right!=null) {
			dfs(index+1, root.right, res);
		}
	}
}
```



#### 4. **Morris 遍历二叉树：前序、中序、后序【二叉树的最优遍历：时间复杂度 O(N)、额外空间复杂度 O(1)】**

```java
public class TreeNode {
      int val;
      TreeNode left;
      TreeNode right;
      TreeNode(int x) { val = x; }
 }

//前序
class Solution {
  public List<Integer> preorderTraversal(TreeNode root) {
    List<Integer> res = new ArrayList<Integer>();
    if (root == null) {
				return res;
		}
		TreeNode cur1 = root;
		TreeNode cur2 = null;
		while (cur1 != null) {
			cur2 = cur1.left;
			if (cur2 != null) {
        while (cur2.right != null && cur2.right != cur1) {
            cur2 = cur2.right;
        }
        if (cur2.right == null) {
          cur2.right = cur1;
          res.add(cur1.val);
          cur1 = cur1.left;
          continue;
        } else {
          cur2.right = null;
        }
      } else {
        res.add(cur1.val);
      }
			cur1 = cur1.right;
		}
		return res;
  }
}

//中序
class Solution {
  public List<Integer> preorderTraversal(TreeNode root) {
    List<Integer> res = new ArrayList<Integer>();
    if (head == null) {
			return res;
		}
    TreeNode cur1 = head;
    TreeNode cur2 = null;
    while (cur1 != null) {
      cur2 = cur1.left;
      //构建连接线
      if (cur2 != null) {
        while (cur2.right != null && cur2.right != cur1) {
          cur2 = cur2.right;
        }
        if (cur2.right == null) {
          cur2.right = cur1;
          cur1 = cur1.left;
          continue;
        } else {
          cur2.right = null;
        }
      }
      res.add(cur1.val);
      cur1 = cur1.right;
    }
		return res;
  }
}

//后序
public static void postOrderMorris(TreeNode head) {
	if (head == null) {
		return;
	}
	TreeNode cur1 = head;//遍历树的指针变量
	TreeNode cur2 = null;//当前子树的最右节点
	while (cur1 != null) {
		cur2 = cur1.left;
		if (cur2 != null) {
			while (cur2.right != null && cur2.right != cur1) {
				cur2 = cur2.right;
			}
			if (cur2.right == null) {
				cur2.right = cur1;
				cur1 = cur1.left;
				continue;
			} else {
				cur2.right = null;
				postMorrisPrint(cur1.left);
			}
		}
		cur1 = cur1.right;
	}
	postMorrisPrint(head);
}
//打印函数
public static void postMorrisPrint(TreeNode head) {
	TreeNode reverseList = postMorrisReverseList(head);
	TreeNode cur = reverseList;
	while (cur != null) {
		System.out.print(cur.value + " ");
		cur = cur.right;
	}
	postMorrisReverseList(reverseList);
}
//翻转单链表
public static TreeNode postMorrisReverseList(TreeNode head) {
	TreeNode cur = head;
	TreeNode pre = null;
	while (cur != null) {
		TreeNode next = cur.right;
		cur.right = pre;
		pre = cur;
		cur = next;
	}
	return pre;
}
```



#### 5. 输入一个数组，判断是不是二叉搜索树的后序遍历序列

#### 6. 二叉树右视图

> 6-7题 是二叉树的序列化与反序列化：

#### 7. **二叉树的序列化、反序列化： 前序、层序**

```java
//前序(DFS)
//DFS 可以从一个根开始，一直延伸到某个叶，然后回到根，到达另一个分支。
public class Codec {
    public String rserialize(TreeNode root, String str) {
        if (root == null) {
            str += "None,";
        } else {
            str += str.valueOf(root.val) + ",";
            str = rserialize(root.left, str);
            str = rserialize(root.right, str);
        }
        return str;
    }
  
    public String serialize(TreeNode root) {
        return rserialize(root, "");
    }
  
    public TreeNode rdeserialize(List<String> l) {
        if (l.get(0).equals("None")) {
            l.remove(0);
            return null;
        }
  
        TreeNode root = new TreeNode(Integer.valueOf(l.get(0)));
        l.remove(0);
        root.left = rdeserialize(l);
        root.right = rdeserialize(l);
    
        return root;
    }
  
    public TreeNode deserialize(String data) {
        String[] data_array = data.split(",");
        List<String> data_list = new LinkedList<String>(Arrays.asList(data_array));
        return rdeserialize(data_list);
    }
}

//bfs
/**
 * Definition for a binary tree node.
 * public class TreeNode {
 *     int val;
 *     TreeNode left;
 *     TreeNode right;
 *     TreeNode(int x) { val = x; }
 * }
 */
import java.util.*;
public class Codec {

    // Encodes a tree to a single string.
    public String serialize(TreeNode root) {
        int size = 0;
        Queue<TreeNode> queue = new LinkedList<>();
        StringBuilder sb = new StringBuilder("[");
        if (root != null) {
            size++;
            queue.offer(root);
        }

        while (size > 0) {
            TreeNode nextNode = queue.poll();
            if (nextNode != null) {
                // 取出当前节点
                size--;
                sb.append(nextNode.val).append(",");

                /*添加节点*/
                // 先考虑当前节点的子节点是否为null
                if (nextNode.left != null) {
                    size++;
                }
                if (nextNode.right != null) {
                    size++;
                }
                // 若当前节点的子节点都为null,且剩余的也都为null
                if (size == 0) {
                    // 则不用添加,直接退出
                    break;
                } else {
                    queue.offer(nextNode.left);
                    queue.offer(nextNode.right);
                }
            } else {
                sb.append("null,");
            }
        }

        int len = sb.length();
        sb.replace(len - 1, len, "]");
        return sb.toString();
    }

    // Decodes your encoded data to tree.
    public TreeNode deserialize(String data) {
        if (data.length() < 3) {
            return null;
        }
        String[] subs = data.trim().substring(1, data.length() - 1).split(",");
        TreeNode root = new TreeNode(Integer.parseInt(subs[0]));

        int length = subs.length;

        /*根据树的数学结构 构建*/
        int n = -1;
        // index: 代表了树的构建程度,指向已经构成的最后一个叶子节点的下一位
        int index = 1;
        Queue<TreeNode> queue = new LinkedList<>();
        queue.offer(root);
        TreeNode temp;
        while (true) {
            do {
                temp = queue.poll();
                n++;
            } while (temp == null);

            if (index < length) {
                temp.left = "null".equals(subs[index]) ? null 
                    : new TreeNode(Integer.parseInt(subs[index]));
                queue.offer(temp.left);
                index++; // 每构建一个节点,index++
            } else {
                break;
            }

            if (index < length) {
                temp.right = "null".equals(subs[index]) ? null 
                    : new TreeNode(Integer.parseInt(subs[index]));
                queue.offer(temp.right);
                index++; // 每构建一个节点,index++
            } else {
                break;
            }
        }
        return root;
    }
}


```



#### 8. 反序列化：怎么序列化的就怎么反序列化

#### 9. **在二叉树中找一个节点的后继节点**

```java
  /*
  1、若该节点node有右子树，那么该节点的后继节点，必然是右子树中，最左的节点
  2、若该节点node没有右子树，则沿着parent节点一次往上找，直至parent的左节点==node节点，那么parent就是node的后继节点
  */
  // 找到node的后继节点
  public static Node getSuccessorNode(Node node) {
    if (node == null) {
      return node;
    }
    // 如果有右子树，那么后继节点必然是右子树中，最左边的节点
    // 因为中序遍历的过程是：左中右，因此打印完当前节点（zhong），下一个节点就是右
    // 然后下一个递归过程又是左中右，因此后继节点必然是右子树中，最左边的节点
    if (node.right != null) {
      return getLeftMost(node.right);
    }else {
      // 如果没有右子树，则沿着parent节点往上找，直到parent的左节点=node节点
      Node parent = node.parent;
      while(parent != null && parent.left != node) {
        node = parent;
        parent = node.parent;
      }
      return parent;
    }
  }

  /// 找到node的最左节点
  public static Node getLeftMost(Node node) {
    if (node == null) {
      return node;
    }
    // 遍历到最左的节点
    while(node.left != null) {
      node = node.left;
    }
    return node;
  }

//前继节点
/*
1、若该节点有左子树，那么其前继节点必然是左子树中，最右的节点
2、若该节点node没有左子树，则沿着parent节点往上找，直至parent的右节点==node节点，那么parent就是node的前继节点
*/
  // 找到node的前继节点
  public static Node getPerviousNode(Node node) {
    if (node == null) {
      return node;
    }
    if (node.left != null) {
      // 若有左子树，那么前继节点就是左子树中，最右的节点
      return getRightMost(node.left);
    }else {
      // 若没有右子树，那么沿着node的父节点查找，直至parent.right = node
      Node parent = node.parent;
      while(parent != null && parent.right != node) {
        node = parent;
        parent = node.parent;
      }
      return parent;
    }
  }

  /// 找到node的最右节点
  public static Node getRightMost(Node node) {
    if (node == null) {
      return node;
    }
    while (node.right != null) {
      node = node.right;
    }
    return node;
  }

```



#### 10. **判断一棵树是否是完全二叉树**

```java
/*
如果一棵树是一颗完全二叉树，那么如果是一个非叶结点，它的左右孩子要么都存在，要么只有左孩子，并且它右边的兄弟结点都是叶子结点；如果是一个叶子结点，那它的兄弟结点也都是叶子结点。
*/
public boolean isComplete(Node head) {
         if(head == null)
             return true;
         Queue<Node> queue = new LinkedList<>();
         boolean isLeaf = false;
         Node left = null;
         Node right = null;
         queue.offer(head);
         while(!queue.isEmpty()) {
             head = queue.poll();
             left = head.left;
             right = head.right;
             // 如果当前结点是叶子结点并且有孩子 或  有右孩子没有左孩子
             if(isLeaf && (left != null || right != null) || left == null && right != null) {
                 return false;
             }
             if(left != null) {
                 queue.offer(left);
             }
             if(right != null) {
                 queue.offer(right);
             }else {
                 // 当前结点是最后一个非叶结点（左不空右空）或当前是第一个叶子结点（左右都空）
                 isLeaf = true;
             }
         }
         return true;
   }
```

#### 11. **判断一棵树是否是搜索二叉树**

```java
/*
从二叉搜索树的特点，并结合二叉树的遍历可以得出：二叉排序树的中序遍历是升序的。所以只需要在进行中序遍历的时候对当前结点的值和上一个值的大小进行比较就可以判断一棵树是不是一颗二叉排序树了。
*/
// 非递归实现的中序遍历
public static boolean isSearched(Node head) {
        if(head == null)
            return false;
        int preNodeVal = Integer.MIN_VALUE;
        Stack<Node> stack = new Stack<>();
        while(!stack.isEmpty() || head != null) {
            if(head != null) {
                stack.push(head);
                head = head.left;
            }else {
                head = stack.pop();
                if(preNodeVal > head.val) {
                    // 不是排序树
                    return false;
                }
                preNodeVal = head.val;
                head = head.right;
            }
        }
        return true;
    }
```



#### 12. **判断一棵树是否是平衡二叉树**

```java
//自顶向下
class Solution {
  // Recursively obtain the height of a tree. An empty tree has -1 height
  private int height(TreeNode root) {
    // An empty tree has height -1
    if (root == null) {
      return -1;
    }
    return 1 + Math.max(height(root.left), height(root.right));
  }

  public boolean isBalanced(TreeNode root) {
    // An empty tree satisfies the definition of a balanced tree
    if (root == null) {
      return true;
    }

    // Check if subtrees have height within 1. If they do, check if the
    // subtrees are balanced
    return Math.abs(height(root.left) - height(root.right)) < 2
        && isBalanced(root.left)
        && isBalanced(root.right);
  }
}

/*
方法一计算height 存在大量冗余。每次调用height 时，要同时计算其子树高度。但是自底向上计算，每个子树的高度只会计算一次。可以递归先计算当前节点的子节点高度，然后再通过子节点高度判断当前节点是否平衡，从而消除冗余。
*/
// Utility class to store information from recursive calls
final class TreeInfo {
  public final int height;
  public final boolean balanced;

  public TreeInfo(int height, boolean balanced) {
    this.height = height;
    this.balanced = balanced;
  }
}

class Solution {
  // Return whether or not the tree at root is balanced while also storing
  // the tree's height in a reference variable.
  private TreeInfo isBalancedTreeHelper(TreeNode root) {
    // An empty tree is balanced and has height = -1
    if (root == null) {
      return new TreeInfo(-1, true);
    }

    // Check subtrees to see if they are balanced.
    TreeInfo left = isBalancedTreeHelper(root.left);
    if (!left.balanced) {
      return new TreeInfo(-1, false);
    }
    TreeInfo right = isBalancedTreeHelper(root.right);
    if (!right.balanced) {
      return new TreeInfo(-1, false);
    }

    // Use the height obtained from the recursive calls to
    // determine if the current node is also balanced.
    if (Math.abs(left.height - right.height) < 2) {
      return new TreeInfo(Math.max(left.height, right.height) + 1, true);
    }
    return new TreeInfo(-1, false);
  }

  public boolean isBalanced(TreeNode root) {
    return isBalancedTreeHelper(root).balanced;
  }
}
```

#### 13. 判断一棵树是否是对称的二叉树

#### 14. 二叉树的镜像

#### 15. 树的子结构：输入两棵二叉树 A 和 B，判断 B 是不是 A 的子结构

#### 16. 合并二叉树

#### 17. 二叉树中和为某一值的路径

#### 18. 重建二叉树：输入某二叉树的前序遍历和中序遍历的结果，请重新构造出该二叉树

#### 19. 求一棵完全二叉树的节点个数，时间复杂度低于O(N)

#### 20. 找二叉树左下角的值

#### 21. 把二叉搜索树转换为累加树

> 22-25题是二叉树信息收集问题：

#### 1. 舞会的最大活跃度

#### 2. 求一棵二叉树中最大二叉搜索子树的节点个数

#### 3. 求一个二叉树的最远距离

#### 4. 二叉树的最大路径和

```java
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
    int res = Integer.MIN_VALUE;
    public int maxPathSum(TreeNode root) {
        if (root == null) {
            return 0;
        }
        dfs(root);
        return res;
    }

    public int dfs(TreeNode root) {
        if (root == null) {
            return 0;
        }

        int leftMax  = Math.max(0, dfs(root.left));         // 左孩子贡献
        int rightMax = Math.max(0, dfs(root.right));        // 右孩子贡献
        res = Math.max(res, root.val + leftMax + rightMax); // 更新res
        return root.val + Math.max(leftMax, rightMax);      // 返回当前节点的总贡献
    }
}
```

### 8. 图

#### 1. **深度优先搜索**

```java
package com.bean.algorithm.graph;
 
import java.util.Iterator;
import java.util.LinkedList;
 
public class DFSGraph {
	
	private int V; // 定义结点（vertices）
 
	// 图的邻接表表示（ Adjacency List）
	private LinkedList<Integer> adj[];
 
	// 构造图的构造方法
	DFSGraph(int v) {
		V = v;
		adj = new LinkedList[v];
		for (int i = 0; i < v; ++i)
			adj[i] = new LinkedList();
	}
 
	// 向图中添加边
	void addEdge(int v, int w) {
		adj[v].add(w); 
	}
 
	// DFS算法工具
	void DFSUtil(int v, boolean visited[]) {
		// 标记当前结点为已访问（visited）并输出
		visited[v] = true;
		System.out.print(v + " ");
 
		// 访问当前的结点的所有邻接结点
		Iterator<Integer> i = adj[v].listIterator();
		while (i.hasNext()) {
			int n = i.next();
			if (!visited[n])
				DFSUtil(n, visited);
		}
	}
 
	// DFS traversal. 用来回溯调用 DFSUtil()工具
	void DFS(int v) {
		// 标记所有节点为未访问状态（ not visited），设置初始值为false。
		boolean visited[] = new boolean[V];
 
		// 回溯 DFS traversal
		DFSUtil(v, visited);
	}
 
	public static void main(String args[]) {
		DFSGraph g = new DFSGraph(4);
 
		g.addEdge(0, 1);
		g.addEdge(0, 2);
		g.addEdge(1, 2);
		g.addEdge(2, 0);
		g.addEdge(2, 3);
		g.addEdge(3, 3);
 
		System.out.println("下面是DFS搜索结果 " + "(从2号结点开始)");
 
		g.DFS(2);
	}
}
```

#### 2. **广度优先搜索**

```java
import java.util.LinkedList;
import java.util.Queue;
import java.util.Stack;
 
/**
 * 深度优先遍历
 * 
 *
 */
public class DeepFirstSort {
	public static void main(String[] args) {
		TreeNode head=new TreeNode(1);
		TreeNode second=new TreeNode(2);
		TreeNode three=new TreeNode(3);
		TreeNode four=new TreeNode(4);
		TreeNode five=new TreeNode(5);
		TreeNode six=new TreeNode(6);
		TreeNode seven=new TreeNode(7);
		head.rightNode=three;
		head.leftNode=second;
		second.rightNode=five;
		second.leftNode=four;
		three.rightNode=seven;
		three.leftNode=six;
		System.out.print("广度优先遍历结果：");
		new DeepFirstSort().BroadFirstSearch(head);
		System.out.println();
		System.out.print("深度优先遍历结果：");
		new DeepFirstSort().depthFirstSearch(head);
	}
	
	//广度优先遍历是使用队列实现的
	public void BroadFirstSearch(TreeNode nodeHead) {
		if(nodeHead==null) {
			return;
		}
		Queue<TreeNode> myQueue=new LinkedList<>();
		myQueue.add(nodeHead);
		while(!myQueue.isEmpty()) {
			TreeNode node=myQueue.poll();
			System.out.print(node.data+" ");
			if(null!=node.leftNode) {
				myQueue.add(node.leftNode);    //广度优先遍历，我们在这里采用每一行从左到右遍历
			}
			if(null!=node.rightNode) {
				myQueue.add(node.rightNode);
			}
			
		}
	}
	
	//深度优先遍历
	public void depthFirstSearch(TreeNode nodeHead) {
		if(nodeHead==null) {
			return;
		}
		Stack<TreeNode> myStack=new Stack<>();
		myStack.add(nodeHead);
		while(!myStack.isEmpty()) {
			TreeNode node=myStack.pop();    //弹出栈顶元素
			System.out.print(node.data+" ");
			if(node.rightNode!=null) {
				myStack.push(node.rightNode);    //深度优先遍历，先遍历左边，后遍历右边,栈先进后出
			}
			if(node.leftNode!=null) {
				myStack.push(node.leftNode);
			}
		}
		
	}
 
}
```

#### 3. 拓扑排序

### 9. 数字与位运算

#### 1. 两数之和、三数之和

#### 2. **大数问题：大数相加和大数相乘问题 +** **Karatsuba 算法**

```java
//大数相加
public String bigNumberAdd(String f, String s) {  
        //翻转两个字符串，并转换成数组  
        char[] a = new StringBuilder(f).reverse().toString().toCharArray();  
        char[] b = new StringBuilder(s).reverse().toString().toCharArray();  
        int lenA = a.length;  
        int lenB = b.length;  
        //计算两个长字符串中的较长字符串的长度  
        int len = lenA > lenB ? lenA : lenB;  
        int[] result = new int[len + 1];  
        for (int i = 0; i < len + 1; i++) {  
            //如果当前的i超过了其中的一个，就用0代替，和另一个字符数组中的数字相加  
            int aint = i < lenA ? (a[i] - '0') : 0;  
            int bint = i < lenB ? (b[i] - '0') : 0;  
            result[i] = aint + bint;  
        }  
        //处理结果集合，如果大于10的就向前一位进位，本身进行除10取余  
        for (int i = 0; i < result.length; i++) {  
            if (result[i] > 10) {  
                result[i + 1] += result[i] / 10;  
                result[i] %= 10;  
            }  
        }  
        StringBuilder sb = new StringBuilder();  
        //该字段用于标识是否有前置0，如果有就不要存储  
        boolean flag = true;  
        for (int i = len; i >= 0; i--) {  
            if (result[i] == 0 && flag) {  
                continue;  
            } else {  
                flag = false;  
            }  
            sb.append(result[i]);  
        }  
        return sb.toString();  
    }  

//大数相减
public static String bigNumberSub(String f, String s) {  
        System.out.print("减法:" + f + "-" + s + "=");  
        // 将字符串翻转并转换成字符数组  
        char[] a = new StringBuilder(f).reverse().toString().toCharArray();  
        char[] b = new StringBuilder(s).reverse().toString().toCharArray();  
        int lenA = a.length;  
        int lenB = b.length;  
        // 找到最大长度  
        int len = lenA > lenB ? lenA : lenB;  
        int[] result = new int[len];  
        // 表示结果的正负  
        char sign = '+';  
        // 判断最终结果的正负  
        if (lenA < lenB) {  
            sign = '-';  
        } else if (lenA == lenB) {  
            int i = lenA - 1;  
            while (i > 0 && a[i] == b[i]) {  
                i--;  
            }  
            if (a[i] < b[i]) {  
                sign = '-';  
            }  
        }  
        // 计算结果集，如果最终结果为正，那么就a-b否则的话就b-a  
        for (int i = 0; i < len; i++) {  
            int aint = i < lenA ? (a[i] - '0') : 0;  
            int bint = i < lenB ? (b[i] - '0') : 0;  
            if (sign == '+') {  
                result[i] = aint - bint;  
            } else {  
                result[i] = bint - aint;  
            }  
        }  
        // 如果结果集合中的某一位小于零，那么就向前一位借一，然后将本位加上10。其实就相当于借位做减法  
        for (int i = 0; i < result.length - 1; i++) {  
            if (result[i] < 0) {  
                result[i + 1] -= 1;  
                result[i] += 10;  
            }  
        }  
  
        StringBuilder sb = new StringBuilder();  
        // 如果最终结果为负值，就将负号放在最前面，正号则不需要  
        if (sign == '-') {  
            sb.append('-');  
        }  
        // 判断是否有前置0  
        boolean flag = true;  
        for (int i = len - 1; i >= 0; i--) {  
            if (result[i] == 0 && flag) {  
                continue;  
            } else {  
                flag = false;  
            }  
            sb.append(result[i]);  
        }  
        // 如果最终结果集合中没有值，就说明是两值相等，最终返回0  
        if (sb.toString().equals("")) {  
            sb.append("0");  
        }  
        // 返回值  
        System.out.println(sb.toString());  
        return sb.toString();  
    } 

//大数相乘
public static void bigNumberSimpleMulti(String f, String s) {  
        System.out.print("乘法：\n" + f + "*" + s + "=");  
        // 获取首字符，判断是否是符号位  
        char signA = f.charAt(0);  
        char signB = s.charAt(0);  
        char sign = '+';  
        if (signA == '+' || signA == '-') {  
            sign = signA;  
            f = f.substring(1);  
        }  
        if (signB == '+' || signB == '-') {  
            if (sign == signB) {  
                sign = '+';  
            } else {  
                sign = '-';  
            }  
            s = s.substring(1);  
        }  
        // 将大数翻转并转换成字符数组  
        char[] a = new StringBuilder(f).reverse().toString().toCharArray();  
        char[] b = new StringBuilder(s).reverse().toString().toCharArray();  
        int lenA = a.length;  
        int lenB = b.length;  
        // 计算最终的最大长度  
        int len = lenA + lenB;  
        int[] result = new int[len];  
        // 计算结果集合  
        for (int i = 0; i < a.length; i++) {  
            for (int j = 0; j < b.length; j++) {  
                result[i + j] += (int) (a[i] - '0') * (int) (b[j] - '0');  
            }  
        }  
        // 处理结果集合，如果是大于10的就向前一位进位，本身进行除10取余  
        for (int i = 0; i < result.length; i++) {  
            if (result[i] > 10) {  
                result[i + 1] += result[i] / 10;  
                result[i] %= 10;  
            }  
        }  
        StringBuffer sb = new StringBuffer();  
        // 该字段用于标识是否有前置0，如果是0就不需要打印或者存储下来  
        boolean flag = true;  
        for (int i = len - 1; i >= 0; i--) {  
            if (result[i] == 0 && flag) {  
                continue;  
            } else {  
                flag = false;  
            }  
            sb.append(result[i]);  
        }  
        if (!sb.toString().equals("")) {  
            if (sign == '-') {  
                sb.insert(0, sign);  
            }  
        } else {  
            sb.append(0);  
        }  
        // 返回最终结果  
        System.out.println(sb.toString());  
    }  
/**
 * Karatsuba乘法
 */
public static long karatsuba(long num1, long num2){
    //递归终止条件
    if(num1 < 10 || num2 < 10) return num1 * num2;

    // 计算拆分长度
    int size1 = String.valueOf(num1).length();
    int size2 = String.valueOf(num2).length();
    int halfN = Math.max(size1, size2) / 2;

    /* 拆分为a, b, c, d */
    long a = Long.valueOf(String.valueOf(num1).substring(0, size1 - halfN));
    long b = Long.valueOf(String.valueOf(num1).substring(size1 - halfN));
    long c = Long.valueOf(String.valueOf(num2).substring(0, size2 - halfN));
    long d = Long.valueOf(String.valueOf(num2).substring(size2 - halfN));

    // 计算z2, z0, z1, 此处的乘法使用递归
    long z2 = karatsuba(a, c);
    long z0 = karatsuba(b, d);
    long z1 = karatsuba((a + b), (c + d)) - z0 - z2;

    return (long)(z2 * Math.pow(10, (2*halfN)) + z1 * Math.pow(10, halfN) + z0);
}

```



#### 3. 打印从 1 到最大的 n 位数

#### 4. 数值的整数次方

#### 5. **二进制中 1 的个数**

```java
/*
n&(n−1) 解析： 二进制数字 nn 最右边的 11 变成 00 ，其余不变。
*/
public class Solution {
    public int hammingWeight(int n) {
        int res = 0;
        while(n != 0) {
            res++;
            n &= n - 1;
        }
        return res;
    }
}
```

### 10. 排序的应用

- 快排的应用：

#### 1. **求一个数组中的第 K 小 / 大的数**

```java
    public int findKthLargest(int[] nums, int k) {
        return quickSelect(nums, 0, nums.length - 1, k);
    }

    int quickSelect(int[] nums, int lo, int hi, int k) {
        int pivot = lo;
        for (int j = lo; j < hi; j++) {
            if (nums[j] <= nums[hi]) {
                swap(nums, pivot++, j);
            }
        }
        swap(nums, pivot, hi);
        int count = hi - pivot + 1;
        // 如果找到直接返回
        if (count == k)
            return nums[pivot];
        // 从右边部分找
        if (count > k)
            return quickSelect(nums, pivot + 1, hi, k);
        // 从左边部分找
        return quickSelect(nums, lo, pivot - 1, k - count);
    }

    private void swap(int[] nums, int i, int j) {
        if (i != j) {
            nums[i] ^= nums[j];
            nums[j] ^= nums[i];
            nums[i] ^= nums[j];
        }
    }
```

#### 2. **最小的 K 个数**

```java
//快排
class Solution {
    public int[] getLeastNumbers(int[] arr, int k) {
        if (k == 0 || arr.length == 0) {
            return new int[0];
        }
        // 最后一个参数表示我们要找的是下标为k-1的数
        return quickSearch(arr, 0, arr.length - 1, k - 1);
    }

    private int[] quickSearch(int[] nums, int lo, int hi, int k) {
        // 每快排切分1次，找到排序后下标为j的元素，如果j恰好等于k就返回j以及j左边所有的数；
        int j = partition(nums, lo, hi);
        if (j == k) {
            return Arrays.copyOf(nums, j + 1);
        }
        // 否则根据下标j与k的大小关系来决定继续切分左段还是右段。
        return j > k? quickSearch(nums, lo, j - 1, k): quickSearch(nums, j + 1, hi, k);
    }

    // 快排切分，返回下标j，使得比nums[j]小的数都在j的左边，比nums[j]大的数都在j的右边。
    private int partition(int[] nums, int lo, int hi) {
        int v = nums[lo];
        int i = lo, j = hi + 1;
        while (true) {
            while (++i <= hi && nums[i] < v);
            while (--j >= lo && nums[j] > v);
            if (i >= j) {
                break;
            }
            int t = nums[j];
            nums[j] = nums[i];
            nums[i] = t;
        }
        nums[lo] = nums[j];
        nums[j] = v;
        return j;
    }
}
//堆
// 保持堆的大小为K，然后遍历数组中的数字，遍历的时候做如下判断：
// 1. 若目前堆的大小小于K，将当前数字放入堆中。
// 2. 否则判断当前数字与大根堆堆顶元素的大小关系，如果当前数字比大根堆堆顶还大，这个数就直接跳过；
//    反之如果当前数字比大根堆堆顶小，先poll掉堆顶，再将该数字放入堆中。
class Solution {
    public int[] getLeastNumbers(int[] arr, int k) {
        if (k == 0 || arr.length == 0) {
            return new int[0];
        }
        // 默认是小根堆，实现大根堆需要重写一下比较器。
        Queue<Integer> pq = new PriorityQueue<>((v1, v2) -> v2 - v1);
        for (int num: arr) {
            if (pq.size() < k) {
                pq.offer(num);
            } else if (num < pq.peek()) {
                pq.poll();
                pq.offer(num);
            }
        }
        
        // 返回堆中的元素
        int[] res = new int[pq.size()];
        int idx = 0;
        for(int num: pq) {
            res[idx++] = num;
        }
        return res;
    }
}
```



- 归并排序的应用：

#### 1. 求一个数组中的逆序对数问题

#### 2. 小和问题：把数组中每一个数左边比当前数小的累加起来，叫着这个数组的小和

### 11. 矩阵问题

#### 1. 顺时针打印矩阵

#### 2. 将一个正方形旋转 90 度

#### 3. 之字型打印矩阵

#### 4. 在一个行和列都有序的 m 行 n 列的矩阵中查找一个数是否存在

### 12. 递归

#### 1. 求 n! 的结果

#### 2. **汉诺塔问题**

```java
    public void hanota(List<Integer> A, List<Integer> B, List<Integer> C) {
        hanoi(A.size(), A, B, C);
    }

    public void hanoi(int n, List<Integer> A, List<Integer> B, List<Integer> C){
        
        if(n == 1){
            C.add(A.get(A.size() - 1));
            A.remove(A.size() - 1);
        }else{
            //把A经过辅助C放到B上
            hanoi(n - 1, A, C, B);
            //把A放到C上
            C.add(A.get(A.size() - 1));
            A.remove(A.size() - 1);
            //把B经过辅助A放到C上
            hanoi(n - 1, B, A, C);
        }
    }
```



#### 3. 打印一个字符串的全部子序列，包括空字符串

#### 4. 打印一个字符串的全排列

#### 5. 母牛问题：母牛每年生一只母牛，新出生的母牛成长三年后也能每年生一只母牛，假设不会死。求 N 年后，母牛的数量

#### 6. 机器人走路问题

#### 7. 给定一个数字组成的字符串，返回有多少种合法的 IPV4 组合

#### 8. 斐波那契数列

### 13. 动态规划

#### 1. 机器人走路问题

#### 2. 给定一个数字组成的字符串，返回有多少种合法的 IPV4 组合

#### 3. **矩阵最小路径问题： 二维数组从左上角走到右下角的最短距离**

```java
class Solution {
    public int minPathSum(int[][] grid) {
        for(int i = 0; i < grid.length; i++) {
            for(int j = 0; j < grid[0].length; j++) {
                if(i == 0 && j == 0) continue;
                else if(i == 0)  grid[i][j] = grid[i][j - 1] + grid[i][j];
                else if(j == 0)  grid[i][j] = grid[i - 1][j] + grid[i][j];
                else grid[i][j] = Math.min(grid[i - 1][j], grid[i][j - 1]) + grid[i][j];
            }
        }
        return grid[grid.length - 1][grid[0].length - 1];
    }
}
```

#### 4. 剪绳子：剪成 m 段，最大乘积问题

#### 5. **数组中任意数累加得到目标值**

#### 6. 分割等和子集

```java
public class Solution {

    public boolean canPartition(int[] nums) {
        int len = nums.length;
        if (len == 0) {
            return false;
        }

        int sum = 0;
        for (int num : nums) {
            sum += num;
        }

        // 特判：如果是奇数，就不符合要求
        if ((sum & 1) == 1) {
            return false;
        }

        int target = sum / 2;

        // 创建二维状态数组，行：物品索引，列：容量（包括 0）
        boolean[][] dp = new boolean[len][target + 1];

        // 先填表格第 0 行，第 1 个数只能让容积为它自己的背包恰好装满
        if (nums[0] <= target) {
            dp[0][nums[0]] = true;
        }

        // 再填表格后面几行
        for (int i = 1; i < len; i++) {
            for (int j = 0; j <= target; j++) {
                // 直接从上一行先把结果抄下来，然后再修正
                dp[i][j] = dp[i - 1][j];

                if (nums[i] == j) {
                    dp[i][j] = true;
                    continue;
                }
                if (nums[i] < j) {
                    dp[i][j] = dp[i - 1][j] || dp[i - 1][j - nums[i]];
                }
            }
        }
        return dp[len - 1][target];
    }
}

//空间优化
public class Solution {

    public boolean canPartition(int[] nums) {
        int len = nums.length;
        if (len == 0) {
            return false;
        }

        int sum = 0;
        for (int num : nums) {
            sum += num;
        }

        if ((sum & 1) == 1) {
            return false;
        }

        int target = sum / 2;

        boolean[] dp = new boolean[target + 1];
        dp[0] = true;

        if (nums[0] <= target) {
            dp[nums[0]] = true;
        }

        for (int i = 1; i < len; i++) {
            for (int j = target; nums[i] <= j; j--) {
                if (dp[target]) {
                    return true;
                }

                dp[j] = dp[j] || dp[j - nums[i]];
            }
        }
        return dp[target];
    }
}
```



### 14. 贪心算法

#### 1. 按最低字典序拼接字符串

#### 2. 切分金条总代价最小

#### 3. 最多做 K 个项目的最大利润

#### 4. 安排最多的宣讲场次

### 15. 回溯算法

#### 1. 机器人的运动范围

### 16. 经典结构

#### 1. 单调栈结构[见上文]

> 2-4 题是滑动窗口结构:

#### 2. 滑动窗口结构的实现

#### 3. 生成窗口最大值数组

#### 4. 求一个数组中最大值减去最小值小于或等于 num 的子数组数量（要求O(N)）

### 17.经典算法

#### 1. **蓄水池算法： 解决等概率问题**

#### 2. **Manacher 算法： 解决回文串问题**

#### 3. **KMP 算法： 解决字符串匹配问题：时间复杂度为：O(N + M)，空间复杂度为：O(M)**

#### 4. **BRPRT 算法： 解决第 k 大数问题：选择中位数组的中位数作为 partition 的基准，时间复杂度 O(N)**

#### 5. **单例模式： 懒汉+恶汉+静态内部类+双重校验锁**

#### 6. **生产者消费者模式： wait/notify 、BlockingQueue 实现**

#### 7. **多个线程交替打印： 锁、信号量 Semaphore 实现**

#### 8. **工厂模式**

####9. 读写锁

### 18. 剑指 Offer

《剑指Offer》中不容易分类的题目，在这里单独列出：

第36题：二叉搜索树与双向链表：输入一棵二叉搜索树，将该二叉搜索树转换成一个排序的双向链表

第41题：数据流中的中位数：两个堆实现：最大堆和最小堆

#### 1. 实现一个最大堆

#### 2. 堆排序

### 19. 常见

#### 1. LRU

#### 2. LFU

#### 3. SkipList

#### 4. 快速排序、归并排序、桶排序、冒泡排序

