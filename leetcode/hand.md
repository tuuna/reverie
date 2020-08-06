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

#### 17. 求n个数的最小公倍数

```java
//方法一
//通过求最大公约数方法求最小公倍数
import java.util.*;
class Main{
    public static void main(String args[]){
        Scanner sc=new Scanner(System.in);
        while(sc.hasNext()){
            int n=sc.nextInt();
            int a[]=new int[n];
            for(int i=0;i<n;i++){
                a[i]=sc.nextInt();
            }
            int multipleAll=multiple(a,0);
            System.out.println(multipleAll);
        }
    }
    public static int divstor(int x,int y){//求2个数的最大公约数
        int min = x < y ? x : y ;    //取两个数的较小的那个
        for(int divstorX = min ; divstorX > 0 ; divstorX--){
            if(x%divstorX==0&&y%divstorX==0){    
                //从较小的那个数开始逐渐往后寻找直到找到可以同时整除这2个数的数就是最大公约数
                return divstorX;
            }
        }
        return 1;
    }
    public static int multiple(int a[],int count){
        //count表示从0开始，因为数组是从0开始的（后面也是这样）也就是从第一个数开始
        
        int divstorX=divstor(a[count],a[count+1]);    
        //求第count-1个数与第count个数的最大公约数
        
        int multipleX=a[count]/divstorX*a[count+1];
        //求这2个数的最小公倍数    
        //这句不能是int multipleX=a[count]*a[count+1]/divstorX；
        //虽然结果是一样的，但是先把2个int相乘可能会超过int的范围，所以先除，在乘，可以防止越界
        
        a[count+1]=multipleX;        
        //把2个数的最小公倍数赋值给后面那个数也就是第count+2个数
        
        count++;    //使标记转到第count+2个数
        
        if(count!=a.length-1){    //如果count不是在倒数第二个数
            return multiple(a,count);
            //在求第count+1个数开始与第count+2个数的最小公倍数
        }
        
        return multipleX;    //求完之后返回这个数组的最小公倍数
    }
}
//辗转相除
/*
		使用辗转相除法求n个数的最小公倍数
*/
import java.util.*;
class Main{
	public static void main(String args[]){
		Scanner sc=new Scanner(System.in);
		while(sc.hasNext()){
			int n=sc.nextInt();
			int a[]=new int[n];
			int max=0;
			
			for(int i=0;i<n;i++){
				a[i]=sc.nextInt();
				if(a[i]>max){
					max=a[i];
				}
			}
			
			int s=1;
			for(int i=2;i<=max;i++){
				
				boolean b=false;	//设置标记
				
				for(int j=0;j<n;j++){
					if(a[j]%i==0){
						a[j]=a[j]/i;	
						b=true;		
				//只要有一个数可以被i整除就令标记为真，并改变可以被整除的那个值，改变的值将出				现在辗转相除法的下一排
					}
				}
				
				if(b){
					s*=i;	//标记为真说明辗转相除法还能继续；令使s累乘边上的数
					i--;
				}
			}
			
			for(int i=0;i<n;i++){
				s*=a[i];	//在将s与最后得到的不能再继续辗转相除的数累乘
			}
			
			System.out.println(s);
		}
	}
}
//方法三
第三种方法代码实现如下：
//先求出n个数中最大的那个数max，如果这个数可以整除所有的数，则这是最小公倍数
//如果不能则令max+1直到找到可以整除所有的数的那个数为止
import java.util.*;
class Main{
	public static void main(String args[]){
		Scanner sc=new Scanner(System.in);
		while(sc.hasNext()){
			int n=sc.nextInt();
			int a[]=new int[n];
			int max=0;
			for(int i=0;i<n;i++){
				a[i]=sc.nextInt();
				if(max<a[i]){
					max=a[i];	//先找出这n个数的那个最大的数
				}
			}
			for(int i=max;;i++){
				boolean b=true;	//设置标记
				for(int j=0;j<n;j++){
					if(max%a[j]!=0){
						b=false;	
			//只要有一个数不能整除max则令标记为false
					}
				}
				max++;
				if(b){	//如果标记为true说明该max可以整除这n个数
					//则max为最小公倍数
					System.out.println(i);
					break;	//跳出循环
				}
			}
		}
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

#### 5. 字符串旋转及其应用

```java
class Solution {
    public String reverseLeftWords(String s, int n) {
        StringBuilder res = new StringBuilder();
        for(int i = n; i < n + s.length(); i++)
            res.append(s.charAt(i % s.length()));
        return res.toString();
    }
}
//最简单是直接subString
```

#### 6. 字符串解码

```java
/*
构建辅助栈 stack， 遍历字符串 s 中每个字符 c；
1. 当 c 为数字时，将数字字符转化为数字 multi，用于后续倍数计算；
2. 当 c 为字母时，在 res 尾部添加 c；
3. 当 c 为 [ 时，将当前 multi 和 res 入栈，并分别置空置 00：
		- 记录此 [ 前的临时结果 res 至栈，用于发现对应 ] 后的拼接操作；
	  - 记录此 [ 前的倍数 multi 至栈，用于发现对应 ] 后，获取 multi × [...] 字符串。
 		- 进入到新 [ 后，res 和 multi 重新记录。
4. 当 c 为 ] 时，stack 出栈，拼接字符串 res = last_res + cur_multi * res，其中:
		- last_res是上个 [ 到当前 [ 的字符串，例如 "3[a2[c]]" 中的 a；
		- cur_multi是当前 [ 到 ] 内字符串的重复倍数，例如 "3[a2[c]]" 中的 2。
返回字符串 res。
*/
//辅助栈
class Solution {
    public String decodeString(String s) {
        StringBuilder res = new StringBuilder();
        int multi = 0;
        LinkedList<Integer> stack_multi = new LinkedList<>();
        LinkedList<String> stack_res = new LinkedList<>();
        for(Character c : s.toCharArray()) {
            if(c == '[') {
                stack_multi.addLast(multi);
                stack_res.addLast(res.toString());
                multi = 0;
                res = new StringBuilder();
            }
            else if(c == ']') {
                StringBuilder tmp = new StringBuilder();
                int cur_multi = stack_multi.removeLast();
                for(int i = 0; i < cur_multi; i++) tmp.append(res);
                res = new StringBuilder(stack_res.removeLast() + tmp);
            }
            else if(c >= '0' && c <= '9') multi = multi * 10 + Integer.parseInt(c + "");
            else res.append(c);
        }
        return res.toString();
    }
}
//递归
class Solution {
    public String decodeString(String s) {
        return dfs(s, 0)[0];
    }
    private String[] dfs(String s, int i) {
        StringBuilder res = new StringBuilder();
        int multi = 0;
        while(i < s.length()) {
            if(s.charAt(i) >= '0' && s.charAt(i) <= '9') 
                multi = multi * 10 + Integer.parseInt(String.valueOf(s.charAt(i))); 
            else if(s.charAt(i) == '[') {
                String[] tmp = dfs(s, i + 1);
                i = Integer.parseInt(tmp[0]);
                while(multi > 0) {
                    res.append(tmp[1]);
                    multi--;
                }
            }
            else if(s.charAt(i) == ']') 
                return new String[] { String.valueOf(i), res.toString() };
            else 
                res.append(String.valueOf(s.charAt(i)));
            i++;
        }
        return new String[] { res.toString() };
    } 
}
```

#### 7. 无重复字符的最长子串

```java
//滑动窗口
class Solution {
    public int lengthOfLongestSubstring(String s) {
        if (s.length()==0) return 0;
        HashMap<Character, Integer> map = new HashMap<Character, Integer>();
        int max = 0;
        int left = 0;
        for(int i = 0; i < s.length(); i ++){
            if(map.containsKey(s.charAt(i))){
                left = Math.max(left,map.get(s.charAt(i)) + 1);
            }
            map.put(s.charAt(i),i);
            max = Math.max(max,i-left+1);
        }
        return max;
        
    }
}
```

#### 8. 字符串的最长公共子串和最长公共子序列

#### 9. 请实现一个函数用来判断字符串是否表示数值

#### 10. 判断一个字符串是否是一个合法的 IPV4

#### 11. 最长回文子串

```java
//中心扩散
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

//DP
public class Solution {

    public String longestPalindrome(String s) {
        // 特判
        int len = s.length();
        if (len < 2) {
            return s;
        }

        int maxLen = 1;
        int begin = 0;

        // dp[i][j] 表示 s[i, j] 是否是回文串
        boolean[][] dp = new boolean[len][len];
        char[] charArray = s.toCharArray();

        for (int i = 0; i < len; i++) {
            dp[i][i] = true;
        }
        for (int j = 1; j < len; j++) {
            for (int i = 0; i < j; i++) {
                if (charArray[i] != charArray[j]) {
                    dp[i][j] = false;
                } else {
                    if (j - i < 3) {
                        dp[i][j] = true;
                    } else {
                        dp[i][j] = dp[i + 1][j - 1];
                    }
                }

                // 只要 dp[i][j] == true 成立，就表示子串 s[i..j] 是回文，此时记录回文长度和起始位置
                if (dp[i][j] && j - i + 1 > maxLen) {
                    maxLen = j - i + 1;
                    begin = i;
                }
            }
        }
        return s.substring(begin, begin + maxLen);
    }
}
```

#### 12. 最长重复子串

```java
private static final long P = 805306457;
    private static final long MOD = (int) (1e9+7);
    private long[] hash;
    private long[] power;
    private int[] ansPos;

    private void calcHashAndPower(char[] arr) {
        hash[0] = arr[0];
        power[0] = 1;
        for (int i = 1; i < arr.length; i++) {
            hash[i] = (hash[i-1] * P + arr[i]) % MOD;
            power[i] = (power[i-1] * P) % MOD;
        }
    }

    private long getSubStrHash(int l, int r) {
        if (l == 0) {
            return hash[r];
        }
        return ((hash[r] - power[r-l+1] * hash[l-1]) % MOD + MOD) % MOD;
    }

    private boolean hasTrueExist(char[] arr, int l1, int l2, int subLen) {
        for (int i = 0; i < subLen; i++) {
            if (arr[l1 + i] != arr[l2 + i]) {
                return false;
            }
        }

        return true;
    }

    /**
     * 判断是否存在指定长度的重复子串
     * 由于不同子串可能存在相同的hash值，因此需要解决hash冲突
     */
    private boolean hasDuplicatedStr(char[] arr, int subLen) {
        Map<Long, List<Integer>> map = new HashMap<>();
        for (int l = 0; l <= arr.length - subLen; l++) {
            long strHash = getSubStrHash(l, l + subLen - 1);
            if (map.containsKey(strHash)) {
                List<Integer> oldPosList = map.get(strHash);
                for (Integer oldPos : oldPosList) {
                    if (hasTrueExist(arr, oldPos, l, subLen)) {
                        ansPos[0] = l;
                        ansPos[1] = l + subLen;
                        return true;
                    }
                }

                oldPosList.add(l);
            } else {
                List<Integer> posList = new ArrayList<>();
                posList.add(l);
                map.put(strHash, posList);
            }
        }

        return false;
    }

    public String longestDupSubstring(String str) {
        char[] arr = str.toCharArray();
        int len = arr.length;
        hash = new long[len];
        power = new long[len];
        ansPos = new int[2];

        calcHashAndPower(arr);

        // 二分+滑动窗口
        // 其中二分确定长度，因为当一个长串出现重复串，那么它的子串必定出现重复串。因此可以用二分去猜最大长度
        int low = 1;
        int high = len;
        while (low <= high) {
            int mid = (low + high) >>> 1;
            boolean hasDuplicated = hasDuplicatedStr(arr, mid);
            if (hasDuplicated) {
                low = mid + 1;
            } else {
                high = mid - 1;
            }
        }

        if (ansPos[0] == ansPos[1]) {
            return "";
        }
        
        return str.substring(ansPos[0], ansPos[1]);
    }

```



#### 13. 最小覆盖子串

```java
//滑动窗口
class Solution {
    public String minWindow(String s, String t) {
        if (s == null || t == null || s.length() == 0 || t.length() == 0) return "";
        // 定义一个数字，用来记录字符串 t 中出现字符的频率，也就是窗口内需要匹配的字符和相应的频率
        int[] map = new int[128];
        for (char c : t.toCharArray()) {
            map[c]++;
        }
        int left = 0, right = 0;
        int match = 0;  // 匹配字符的个数
        int minLen = s.length() + 1;   // 最大的子串的长度
        // 子串的起始位置 子串结束的位置(如果不存在这样的子串的话，start，end 都是 0，s.substring 截取就是 “”
        int start = 0, end = 0;
        while (right < s.length()){
            char charRight = s.charAt(right); // 右边界的那个字符
            map[charRight]--;   // 可以理解为需要匹配的字符 charRight 减少了一个
            // 如果字符 charRight 在 t 中存在，那么经过这一次操作，只要个数大于等于 0，说明匹配了一个
            // 若字符 charRight 不在 t 中，那么 map[charRight] < 0, 不进行任何操作
            if (map[charRight] >= 0) match++;
            right++;  // 右边界右移，这样下面就变成了 [)，方便计算窗口大小

            // 只要窗口内匹配的字符达到了要求，右边界固定，左边界收缩
            while (match == t.length()){
                int size = right - left;
                if (size < minLen){
                    minLen = size;
                    start = left;
                    end = right;
                }
                char charLeft = s.charAt(left);  // 左边的那个字符
                map[charLeft]++;  // 左边的字符要移出窗口
                // 不在 t 中出现的字符，移出窗口，最终能够达到的最大值 map[charLeft] = 0
                // 如果恰好移出了需要匹配的一个字符，那么这里 map[charLeft] > 0, 也就是还要匹配字符 charLeft，此时 match--
                if (map[charLeft] > 0) match--;
                left++;  // 左边界收缩
            }
        }
        return s.substring(start, end);
    }
}
```



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

```java
//暴力，枚举左右边界
public class Solution {

    public int subarraySum(int[] nums, int k) {
        int len = nums.length;
        int count = 0;
        for (int left = 0; left < len; left++) {
            for (int right = left; right < len; right++) {

                int sum = 0;
                for (int i = left; i <= right; i++) {
                    sum += nums[i];
                }
                if (sum == k) {
                    count++;
                }
            }
        }
        return count;
    }

    public static void main(String[] args) {
        int[] nums = new int[]{1, 1, 1};
        int k = 2;
        Solution solution = new Solution();
        int res = solution.subarraySum(nums, k);
        System.out.println(res);
    }
}

//暴力优化，先固定左边界，枚举右边界O(n^2)
public class Solution {
    public int subarraySum(int[] nums, int k) {
        int count = 0;
        int len = nums.length;
        for (int left = 0; left < len; left++) {
            int sum = 0;
            // 区间里可能会有一些互相抵销的元素
            for (int right = left; right < len; right++) {
                sum += nums[right];
                if (sum == k) {
                    count++;
                }
            }
        }
        return count;
    }
}

//前缀和
public class Solution {

    public int subarraySum(int[] nums, int k) {
        int len = nums.length;
        // 计算前缀和数组
        int[] preSum = new int[len + 1];
        preSum[0] = 0;
        for (int i = 0; i < len; i++) {
            preSum[i + 1] = preSum[i] + nums[i];
        }

        int count = 0;
        for (int left = 0; left < len; left++) {
            for (int right = left; right < len; right++) {
                // 区间和 [left..right]，注意下标偏移
                if (preSum[right + 1] - preSum[left] == k) {
                    count++;
                }
            }
        }
        return count;
    }
}

//前缀和+哈希表
import java.util.HashMap;
import java.util.Map;

public class Solution {

    public int subarraySum(int[] nums, int k) {
        // key：前缀和，value：key 对应的前缀和的个数
        Map<Integer, Integer> preSumFreq = new HashMap<>();
        // 对于下标为 0 的元素，前缀和为 0，个数为 1
        preSumFreq.put(0, 1);

        int preSum = 0;
        int count = 0;
        for (int num : nums) {
            preSum += num;

            // 先获得前缀和为 preSum - k 的个数，加到计数变量里
            if (preSumFreq.containsKey(preSum - k)) {
                count += preSumFreq.get(preSum - k);
            }

            // 然后维护 preSumFreq 的定义
            preSumFreq.put(preSum, preSumFreq.getOrDefault(preSum, 0) + 1);
        }
        return count;
    }
}
```

### 4. 栈

#### 1. 用固定大小的数组实现栈

```java

public class Stack {

    //存数据的数组
    int[] data;

    //栈的最大长度
    private int size;
    //栈顶的位置
    private int top;

    public Stack(int size) {
        this.size = size;
        data = new int[size];
        top = -1;
    }

    public int getSize() {
        return size;
    }

    public int getTop() {
        return top;
    }

    /**
     * 判断是否为空栈
     * @return
     */
    public boolean isEmpty()     {
        return top == -1;
    }

    /**
     * 判断是否为满栈
     * @return
     */
    public boolean isFull() {
        return (top+1) == size;
    }

    /**
     * 压栈操作
     * @param data
     * @return
     */
    public boolean push(int data) {
        if(isFull()) {
            System.out.println("the stack is full!");
            return false;
        } else {
            top++;
            this.data[top] = data;
            return true;
        }
    }


    /**
     *  弹栈操作
     * @return
     * @throws Exception
     */
    public int pop() throws Exception {
        if(isEmpty()) {
            throw new Exception("the stack is empty!");
        } else {
            return this.data[top--];
        }
    }

    /**
     * 获取栈顶的元素,但不弹栈
     * @return
     */
    public int peek() {
        return this.data[getTop()];
    }

    public static void main(String[] args) {
        Stack stack = new Stack(20);
        stack.push(0);
        stack.push(1);
        stack.push(2);
        stack.push(3);
        System.out.println("Now the top_num is:" + stack.peek());

        while(! stack.isEmpty()) {
            try {
                System.out.println(stack.pop());
            } catch (Exception e) {
                e.printStackTrace();
            }
        }
    }
}

```



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

```java
import java.util.Stack;

public class MinStack {

    // 数据栈
    private Stack<Integer> data;
    // 辅助栈
    private Stack<Integer> helper;

    /**
     * initialize your data structure here.
     */
    public MinStack() {
        data = new Stack<>();
        helper = new Stack<>();
    }

    // 思路 1：数据栈和辅助栈在任何时候都同步

    public void push(int x) {
        // 数据栈和辅助栈一定会增加元素
        data.add(x);
        if (helper.isEmpty() || helper.peek() >= x) {
            helper.add(x);
        } else {
            helper.add(helper.peek());
        }
    }

    public void pop() {
        // 两个栈都得 pop
        if (!data.isEmpty()) {
            helper.pop();
            data.pop();
        }
    }

    public int top() {
        if(!data.isEmpty()){
            return data.peek();
        }
        throw new RuntimeException("栈中元素为空，此操作非法");
    }

    public int getMin() {
        if(!helper.isEmpty()){
            return helper.peek();
        }
        throw new RuntimeException("栈中元素为空，此操作非法");
    }
}
```



#### 4. 栈的压入、弹出序列：输入两个整数序列，第一个序列表示栈的压入顺序，请判断第二个序列是否可能为该栈的弹出顺序

```java
class Solution {
    public boolean validateStackSequences(int[] pushed, int[] popped) {
        Stack<Integer> stack = new Stack<>();
        int i = 0;
        for(int num : pushed) {
            stack.push(num); // num 入栈
            while(!stack.isEmpty() && stack.peek() == popped[i]) { // 循环判断与出栈
                stack.pop();
                i++;
            }
        }
        return stack.isEmpty();
    }
}
```

> 5-8题是单调栈结构问题：

#### 5. 单调栈结构的实现

#### 6. 直方图中的最大矩形面积

```java
public class LargestRetangleArea {
 
    public static int largestRectangleArea(int[] heights){
        if(heights == null || heights.length == 0){
            return 0;
        }
 
        int res = 0;
        Stack<Integer> stack = new Stack<>();
        for(int i = 0; i < heights.length; i++){
            // 注意这里的等号 >= 最后一个相等的元素会计算出正确的值
            while(!stack.isEmpty() && heights[stack.peek()] >= heights[i]){
                int current = stack.pop();
                // 结算当前元素的左右最近比它小的元素信息
                int leftLessIndex = stack.isEmpty() ? -1 : stack.peek();
                // 以当前元素为矩形高度时的总面积
                int area = (i - leftLessIndex - 1) * heights[current];
                // 更新最大面积
                res = Math.max(res, area);
            }
            stack.push(i);
        }
        while(!stack.isEmpty()){
            int current = stack.pop();
            int leftLessIndex = stack.isEmpty() ? -1 : stack.peek();
            int area = (heights.length - leftLessIndex - 1) * heights[current];
            res = Math.max(res, area);
        }
        return res;
    }
}
```



#### 7. 求最大子矩阵的大小

```java
private int[] getMaxArray(int[] nums) {
        // 求一维数组的最大子数组和
        int len = nums.length;
        int max = nums[0];
        int maxFromCol = 0;
        int maxToCol = 0;
        int fromCol = 0;
        int preMax = nums[0];
        for (int col = 1; col < len; col++) {
            if (preMax <= 0) {
                if (nums[col] > max) {
                    max = nums[col];
                    maxFromCol = col;
                    maxToCol = col;
                }
                preMax = nums[col];
                fromCol = col;
            } else {
                preMax = preMax + nums[col];
                if (preMax > max) {
                    max = preMax;
                    maxFromCol = fromCol;
                    maxToCol = col;
                }
            }
        }

        return new int[]{max, maxFromCol, maxToCol};
    }

    // 最大子矩阵，最大子矩形
    public int[] getMaxMatrix(int[][] matrix) {
        int m = matrix.length;
        int n = matrix[0].length;
        int ansMax = Integer.MIN_VALUE;
        int[] ansArr = new int[4];
        int[][] colPreSum = new int[m][n];  // 列上的前缀和

        for (int j = 0; j < n; j++) {
            colPreSum[0][j] = matrix[0][j];
            for (int i = 1; i < m; i++) {
                colPreSum[i][j] = colPreSum[i-1][j] + matrix[i][j];
            }
        }

        int[] tmpArr = new int[n];
        for (int fromRow = 0; fromRow < m; fromRow++) {
            for (int toRow = fromRow; toRow < m; toRow++) {
                // 第fromRow行到第toRow行进行合并
                for (int col = 0; col < n; col++) {
                    tmpArr[col] = fromRow == 0 ? colPreSum[toRow][col] : colPreSum[toRow][col] - colPreSum[fromRow - 1][col];
                }

                // 求一维数组的最大子数组和
                int[] maxArrayRes = getMaxArray(tmpArr);
                int max = maxArrayRes[0];
                int maxFromCol = maxArrayRes[1];
                int maxToCol = maxArrayRes[2];

                if (max > ansMax) {
                    ansMax = max;
                    ansArr[0] = fromRow;
                    ansArr[1] = maxFromCol;
                    ansArr[2] = toRow;
                    ansArr[3] = maxToCol;
                }
            }
        }

        return ansArr;
    }


```



#### 8. **可见山峰问题**

```java
public class GetVisiableNum {
 
    public static class Record{
        public int value;
        public int times;
 
        public Record(int value){
            this.value = value;
            this.times = 1;
        }
    }
 
    public static int getVisiableNum(int[] arr){
        if(arr == null || arr.length < 2){
            return 0;
        }
        if(arr.length == 2){
            return 1;
        }
 
        int size = arr.length;
        int maxIndex = 0;
        // 先在环中找到其中一个最大值的位置，哪一个都行
        for(int i = 0; i < size; i++){
            maxIndex = arr[i] > arr[maxIndex] ? i : maxIndex;
        }
 
        Stack<Record> stack = new Stack<>();
        // 1、先把（最大值,1）这个记录放入stack中
        stack.push(new Record(arr[maxIndex]));
        // 2、从最大位置的下一个位置沿next方向遍历。当index再次回到maxIndex时，说了转了一圈，遍历结束
        // 找到一个数左边（逆时针）方向离它最近比它大的数，右边（顺时针）离它最近比它大的数，这两个数就可以与该数组组成可见山峰对
        int index = nextIndex(maxIndex, size);
        int res = 0;  // 可见山峰对数
        while(index != maxIndex){
            // 2.1 当前数字arr[index]要进栈，判断会不会破坏栈中元素从栈底到栈顶依次增大的顺序
            // 如果破坏了，就依次弹出栈顶记录，并计算该数的可见山峰对数量
            while(stack.peek().value < arr[index]){
                // 弹出记录为(X,K)，如果K==1，产生2对; 如果K>1，产生2*K + C(2,K)对。
                // 【由于相同高度的山峰也是可见山峰，他们之间共有C(2,K)种组合，getInternalSum】
                // 以及每个峰有左边最近比它大的和右边最近比它大的存在，即有两个可见山峰对，总共 2*times对
                int times = stack.pop().times;
                res += getInternalSum(times) + 2 * times;
            }
            // 2.2 当前数字arr[index]要进入栈了，如果和当前栈顶数字一样就合并，不一样就把记录(arr[index],1)放入栈中
            if(stack.peek().value == arr[index]){
                stack.peek().times++;
            }else{
                stack.push(new Record(arr[index]));
            }
            // 开始下一个数
            index = nextIndex(index, size);
        }
 
        // 3、开始清理栈中的数据
        // 第一阶段：栈里的元素大于两个，那么共 2*K + C(2,K)对
        while(stack.size() > 2){
            int times = stack.pop().times;
            res += getInternalSum(times) + 2 * times;
        }
 
        // 第二阶段：栈里的元素等于2个
        while(stack.size() == 2){
            int times = stack.pop().times;
            // 最大值有多个，则我每一个数依然能找到2对可见山峰
            res += getInternalSum(times) + (stack.peek().times == 1 ? times : 2 * times);
        }
 
        // 第三阶段：栈里只有一个元素了
        // 只剩下最大值了，则可见山峰只能是在最大值之间
        res += getInternalSum(stack.pop().times);
        return res;
    }
 
    // 如果k==1返回0，如果k>1返回C(2,k)
    public static int getInternalSum(int k){
        return k == 1 ? 0 : (k * (k - 1)) / 2;
    }
 
    //  环形数组中当前位置为i，数组长度为size，返回i的下一个位置
    public static int nextIndex(int i, int size){
        return i < (size - 1) ? (i + 1) : 0;
    }
}
```

#### 9. 直方图的水量

```java
class Solution {
    private int[] height;

    public int trap(int[] height) {
    	if(height.length <= 2)
    		return 0;
    	this.height = height;
        int sum = 0;
        int start = 0, end = 0;
        for(int i = 0; i < height.length; ++i) {  //初次遍历，找到最高的柱子给到start，end
        	if(height[start] < height[i]) {
        		start = i;
        		end = i;
        	}
        }
        while(start > 0 || end < height.length-1){
		//除start到end之外，找到最高柱子的位置 p，h[p] <=h[start] 并且h[p]<=h[end]
            int p = findHighest(start,end);
            if(start > p) {    //p在start前面时,计算此时p到start之间的积水
            	for(int i = h+1; i < start; ++i) {
            		sum += height[p]-height[i];
            	}
            	start = p;
            }
            if(end < p) {     //p在end后面时,计算此时end到p之间的积水
            	for(int i = end+1; i < p; ++i) {
            		sum += height[p]-height[i];
            	}
            	end = p;
            }
        }
        return sum;
    }

	//这个方法的作用是找到 start到end之外(不包含自身)的最高的h[i]，返回i
    private int findHighest(int start, int end){
    	int p = -1, max = -1;
    	for(int i = 0; i < start; ++i) {
    		if(max < height[i]) {
    			max = height[i];
    			p = i;
    		}
    	}
    	for(int i = end+1; i < height.length; ++i) {
    		if(max < height[i]) {
    			max = height[i];
    			p = i;
    		}
    	}
        return p;
    }
}
```

#### 10. 最大正方形

```java
class Solution {
    public int maximalSquare(char[][] matrix) {
        int maxSide = 0;
        if (matrix == null || matrix.length == 0 || matrix[0].length == 0) {
            return maxSide;
        }
        int rows = matrix.length, columns = matrix[0].length;
        int[][] dp = new int[rows][columns];
        for (int i = 0; i < rows; i++) {
            for (int j = 0; j < columns; j++) {
                if (matrix[i][j] == '1') {
                    if (i == 0 || j == 0) {
                        dp[i][j] = 1;
                    } else {
                        dp[i][j] = Math.min(Math.min(dp[i - 1][j], dp[i][j - 1]), dp[i - 1][j - 1]) + 1;
                    }
                    maxSide = Math.max(maxSide, dp[i][j]);
                }
            }
        }
        int maxSquare = maxSide * maxSide;
        return maxSquare;
    }
}
```

### 5. 队列

#### 1. 用固定大小的数组实现队列

```java
/**
 * 固定长度数组实现队列
 */
public class ArrayQueue {
    private int[] data;
    private int start;
    private int end;
    private int size;

    public ArrayQueue(int capacity) {
        if (capacity < 0)
            throw new IllegalArgumentException("capacity should not be less than 0");
        data = new int[capacity];
        start = end = size = 0;
    }

    public void push(int obj) {
        if (size == data.length)
            throw new ArrayIndexOutOfBoundsException("the queue is full");
        data[end] = obj;
        end = (end + 1) % data.length;
        size++;
    }

    public int poll() {
        if (size == 0)
            throw new ArrayIndexOutOfBoundsException("the queue is empty");
        size--;
        int temp = data[start];
        start = (start + 1) % data.length;
        return temp;
    }

    public Integer peek() {
        if (size == 0)
            throw null;
        return data[start];
    }
}


```



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

```java
public class Solution {
    public class DoubleNode{
        public int value;
        public DoubleNode next;
        public DoubleNode last;

        public DoubleNode(int data){
            this.value=data;
        }
    }
    public DoubleNode reverseList(DoubleNode head){
        DoubleNode pre = null;
        DoubleNode next = null;
        while(head!=null){
            next = head.next;
            head.next=pre;
            head.last=next;
            pre=head;
            head=next;
        }
        return pre;
    }
}

```



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

```java
class Solution {
    public ListNode deleteNode(ListNode head, int val) {
        if(head.val == val) return head.next;
        ListNode pre = head, cur = head.next;
        while(cur != null && cur.val != val) {
            pre = cur;
            cur = cur.next;
        }
        if(cur != null) pre.next = cur.next;
        return head;
    }
}

```

#### 7. 删除链表中重复的节点

```java
class ListNode {
    int val;
    ListNode next = null;
 
    ListNode(int val) {
        this.val = val;
    }
}
 
public class Solution {
    public ListNode deleteDuplication(ListNode pHead)
    {
       ListNode first = new ListNode(-1);
       first.next = pHead;
       ListNode last = first;
       ListNode p = pHead;
 
       while(p!=null&&p.next!=null){
           if(p.val==p.next.val){
               int val = p.val;
               while(p!=null&&p.val==val){
                   p = p.next;
               last.next = p;
               }
           }else{
               last = p;
               p = p.next;
           }
       }
 
       return first.next;
    }
}
```



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

```java
/*
1 找到中点位置，从这里开始反转后半段链表
2 再次找到中点位置，中点与head开始进行对比，中途有不相同的返回false，否则最后返回true
tips：注意这道题不同于返回中间节点，所以slow从dummy开始，而fast从head开始，也就是slow.next指向的是中点位置，为什么要这样做，是为了将链表从中点断开，也就是slow.next=null;
这样做可以不用担心链表节点数是偶数还是奇数。
*/
/**
 * Definition for singly-linked list.
 * public class ListNode {
 *     int val;
 *     ListNode next;
 *     ListNode(int x) { val = x; }
 * }
 */
class Solution {
    public static boolean isPalindrome(ListNode head) {
        //找到中点位置，从这里开始反转后半段链表
        //再次找到中点位置，中点与head开始进行对比，中途有不相同的返回false，否则最后返回true
        ListNode dummy = new ListNode(-1);
        dummy.next = head;
        ListNode slow = dummy;//遍历结束时slow指向的是mid的前一个
        ListNode fast = head;
        while (fast != null && fast.next != null){
            slow = slow.next;
            fast = fast.next.next;
        }

        ListNode head2 = reverse(slow.next);
        slow.next = null;
        while (head != null && head2 != null){
            if(head.val != head2.val)
                return false;
            else{
                head = head.next;
                head2 = head2.next;
            }
        }
        return true;
    }

    /**
     * 反转链表
     * @param head
     * @return
     */
    private static ListNode reverse(ListNode head){
        if(head == null)
            return null;
        ListNode dummy = new ListNode(0);
        dummy.next = head;
        ListNode pre = dummy;
        ListNode cur = pre.next;
        while (cur.next != null){
            ListNode nex = cur.next;

            cur.next = nex.next;
            nex.next = pre.next;
            pre.next = nex;
        }
        return pre.next;
    }
}
```

#### 10. 给出两个有序链表的头结点，打印出两个链表中相同的元素

#### 11. 将单向链表按某值划分成左边小、中间相等、右边大的形式

```java
class test{
    public class Node{
        public int value;
        public Node next;

        public Node(int data){
            this.value = data;
        }
    }

    public Node listPartition1(Node head,int pivot){
        if(head == null){
            return head;
        }
        Node cur = head;
        int i = 0;
        while(cur!=null){
            i++;
            cur = cur.next;
        }
        Node[] nodeArr = new Node[i];
        i = 0;
        cur = head;
        for(i=0;i<nodeArr.length;i++){
            nodeArr[i] = cur;
            cur = cur.next;
        }
        arrPartition(nodeArr,pivot);
        for(i=1;i!=nodeArr.length;i++){
            nodeArr[i-1].next = nodeArr[i];
        }
        nodeArr[i-1].next = null;
        return nodeArr[0];
    }

    public void arrPartition(Node[] nodeArr,int pivot){
        int small = -1;
        int big = nodeArr.length;
        int index = 0;
        while(index != big){
            if(nodeArr[index].value < pivot){
                swap(nodeArr,++small,index++);
            }else if(nodeArr[index].value == pivot){
                index++;
            }else{
                swap(nodeArr,--big,index);
            }
        }
    }

    public void swap(Node[] nodeArr,int a,int b){
        Node tmp = nodeArr[a];
        nodeArr[a] = nodeArr[b];
        nodeArr[b] = tmp;
    }   
}
```



#### 12. 复制含有随机指针节点的链表

```java
第一步：把链表的每个节点复制并将它放在原节点后面，只复制next指针；
第二步：如果要找到cur的复制节点的random指针，可以通过cur->random->next找到；
第三步：将原节点和新的节点分类开即可.

原节点1的随机指针指向原节点3，新节点1的随机指针指向的是原节点3的next
原节点3的随机指针指向原节点2，新节点3的随机指针指向的是原节点2的next
class Solution {
    public Node copyRandomList(Node head) {
        if(head==null) {
            return null;
        }
        Node p = head;
        //第一步，在每个原节点后面创建一个新节点
        //1->1'->2->2'->3->3'
        while(p!=null) {
            Node newNode = new Node(p.val);
            newNode.next = p.next;
            p.next = newNode;
            p = newNode.next;
        }
        p = head;
        //第二步，设置新节点的随机节点
        while(p!=null) {
            if(p.random!=null) {
                p.next.random = p.random.next;
            }
            p = p.next.next;
        }
        Node dummy = new Node(-1);
        p = head;
        Node cur = dummy;
        //第三步，将两个链表分离
        while(p!=null) {
            cur.next = p.next;
            cur = cur.next;
            p.next = cur.next;
            p = p.next;
        }
        return dummy.next;
    }
}	

```



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

#### 16. 二叉搜索树与双向链表转换

```java
//递归
class Solution {
    public Node treeToDoublyList(Node root) {
        //如果为空，返回空
        if(root==null) return null;
        //结果链表的起点和终点
        Node start=root,end=root;
        //如果右侧节点不为空，序列化右边子树后，将其与root进行连接，然后存储终点
        if(root.right!=null){
            Node kidHead = treeToDoublyList(root.right);
            Node kidEnd  =kidHead.left;
            root.right=kidHead;kidHead.left=root;
            end =kidEnd;
        }
        //如果左侧子树不为空，依次循环找到最左的起点
        while(root.left!=null) {
            Node pre =root;
            root = root.left;
            //在这过程中遇到的子节点的右子树如果不为空，则按递归方式序列化连接，
            if(root.right!=null){
                Node kidHead = treeToDoublyList(root.right);
                Node kidEnd  =kidHead.left;
                root.right=kidHead;kidHead.left=root;
                kidEnd.right=pre;pre.left=kidEnd;
            //右子树为空，直接改变右指向
            }else{
                root.right=pre;
            }
        }
        //连接头尾，构成循环链表，返回结果
        start=root;
        start.left=end;
        end.right=start;
        return start;
    }
}
//非递归
    public Node treeToDoublyList(Node root) {
        if(root == null){
            return null;
        }
         Stack<Node> stack = new Stack<>();
         Node current = root;
         Node pre = null, head = null, tail = null;
         while(!stack.isEmpty() || current != null) {
             while(current != null) {
                 stack.push(current);
                 current = current.left;
             }
             current = stack.pop();
             tail = current;
             if(pre == null) {//处理头结点
                 head = current;
             }else {
                 pre.right = current;
                 current.left = pre;
             }
            pre = current;
            current = current.right;
         }
         tail.right = head;
         head.left = tail;
         return head;
    }
```

#### 17. 两个链表的第一个公共节点

```java
public class Solution {
    public ListNode getIntersectionNode(ListNode headA, ListNode headB) {
        if(headA == null || headB == null) return null;
        ListNode a = headA, b = headB;
        int len1 = 0, len2 = 0, sum = 0;
        while(a != null && ++len1 > 0) a = a.next;
        while(b != null && ++len2 > 0) b = b.next;
        a = headA;
        b = headB;
        while (a != b){
            if(a.next == null) a = headB;
            else a = a.next;
            if(b.next == null) b = headA;
            else b = b.next;
            if(sum++ > len1 + len2) return null;
        }
        return a;
    }
}
```



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

```java
    public boolean verifyPostorder(int [] postorder) {
        if (postorder.length <= 2) return true;
        return verifySeq(postorder, 0, postorder.length-1);
    }
    private boolean verifySeq(int[] postorder, int start, int end) {
        if (start >= end) return true;
        int i;
        for (i = start; i < end; i++) {
            if (postorder[i] > postorder[end]) break;
        }
        // 验证后面的是否都大于sequence[end]
        for (int j = i; j < end; j++) {
            if (postorder[j] < postorder[end]) return false;
        }
        return verifySeq(postorder, start, i-1) && verifySeq(postorder, i, end-1);
    }
```



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

```java
//递归
class TreeNode {
    int val = 0;
    TreeNode left = null;
    TreeNode right = null;

    public TreeNode(int val) {
        this.val = val;

    }
}

class Solution {
    public boolean isSymmetric(TreeNode root) {
        if(root == null){
            return true;
        }
        return isMirror(root.left,root.right);
    }
    public boolean isMirror(TreeNode left, TreeNode right){
        if(left == null && right == null){
            return true;
        }else if((left == null && right != null) || (right == null && left != null)){
            //左右有一边为空另一边不为空，说明不对称
            return false;
        }
        if(left.val != right.val){
            return false;
        }

        //递归左的左孩子和右的右孩子相等，并且左的右孩子和右的左孩子相等
        return isMirror(left.left,right.right) && isMirror(left.right,right.left);

    }
}

//非递归
import java.util.Deque;
import java.util.LinkedList;

public class Solution {

    public boolean isSymmetric(TreeNode root) {
        if (root == null) {
            return true;
        }

        // 实现类不能选用 ArrayDeque，因为该类的 add 方法会对添加的元素做非空检查
        Deque<TreeNode> deque = new LinkedList<>();
        // Deque<TreeNode> deque = new ArrayDeque<>();

        deque.addFirst(root.left);
        deque.addLast(root.right);

        while (!deque.isEmpty()) {
            TreeNode leftNode = deque.removeFirst();
            TreeNode rightNode = deque.removeLast();

            if (leftNode == null && rightNode == null) {
                continue;
            }

            if (leftNode == null || rightNode == null) {
                return false;
            }

            if (leftNode.val != rightNode.val) {
                return false;
            }

            deque.addFirst(leftNode.right);
            deque.addFirst(leftNode.left);
            deque.addLast(rightNode.left);
            deque.addLast(rightNode.right);
        }

        return true;
    }
}
```

#### 14. 二叉树的镜像

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
    public TreeNode mirrorTree(TreeNode root) {
		if (root == null) {
            return root;
        }
		//下面三句是将当前节点的左右子树交换
		TreeNode tmp = root.right;
		root.right = root.left;
		root.left = tmp;
		//递归交换当前节点的 左子树
		mirrorTree(root.left);
		//递归交换当前节点的 右子树
		mirrorTree(root.right);
		//函数返回时就表示当前这个节点，以及它的左右子树
		//都已经交换完了
		return root;
    }
}

//迭代
class Solution {
	public TreeNode mirrorTree(TreeNode root) {
		if(root==null) {
			return null;
		}
		//将二叉树中的节点逐层放入队列中，再迭代处理队列中的元素
		LinkedList<TreeNode> queue = new LinkedList<TreeNode>();
		queue.add(root);
		while(!queue.isEmpty()) {
			//每次都从队列中拿一个节点，并交换这个节点的左右子树
			TreeNode tmp = queue.poll();
			TreeNode left = tmp.left;
			tmp.left = tmp.right;
			tmp.right = left;
			//如果当前节点的左子树不为空，则放入队列等待后续处理
			if(tmp.left!=null) {
				queue.add(tmp.left);
			}
			//如果当前节点的右子树不为空，则放入队列等待后续处理
			if(tmp.right!=null) {
				queue.add(tmp.right);
			}
			
		}
		//返回处理完的根节点
		return root;
	}
}
```

#### 15. 树的子结构：输入两棵二叉树 A 和 B，判断 B 是不是 A 的子结构

```java
//BFS
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
    public boolean isSubStructure(TreeNode A, TreeNode B) {
        if(A==null || B == null)
            return false;
        LinkedList<TreeNode> aq = new LinkedList<>();
        aq.add(A);
        while(aq.size() > 0){
            TreeNode n = aq.poll();
            if(n.val == B.val && compare(n,B))
                return true;
            if(n.left != null)
                aq.add(n.left);
            if(n.right != null)
                aq.add(n.right);
        }
        return false;
    }

    boolean compare(TreeNode n1, TreeNode n2){
        if(n2 == null)
            return true;
        if(n1 == null)
            return false;
        return n1.val==n2.val&&compare(n1.left,n2.left)&&compare(n1.right,n2.right);
    }
}

//dfs
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
    public boolean isSubStructure(TreeNode A, TreeNode B) {
        //采用先序遍历的方式进行逐个结点的比较
        return (A != null && B != null) && 
        (recur(A, B) || isSubStructure(A.left, B) || isSubStructure(A.right, B));
    }

    //判断是否是子结构
    public boolean recur(TreeNode A,TreeNode B){
        //子树为空了，即判断完成了
        if(B == null)   return true;
        //父树为空了，小树还没判断完毕，也即不存在子结构
        if(A == null || A.val != B.val)   return false;

        //查看子树相应的结点是否等于对应的子树中的结点的值
        return recur(A.left, B.left) && recur(A.right, B.right);
    }
}

```



#### 16. 合并二叉树

```java
时间复杂度：O(N)，其中 NN 是两棵树中节点个数的较小值。

空间复杂度：O(N)，在最坏情况下，会递归 NN 层，需要 O(N) 的栈空间。

/**
 * Definition for a binary tree node.
 * public class TreeNode {
 *     int val;
 *     TreeNode left;
 *     TreeNode right;
 *     TreeNode(int x) { val = x; }
 * }
 */
public class Solution {
    public TreeNode mergeTrees(TreeNode t1, TreeNode t2) {
        if (t1 == null)
            return t2;
        if (t2 == null)
            return t1;
        t1.val += t2.val;
        t1.left = mergeTrees(t1.left, t2.left);
        t1.right = mergeTrees(t1.right, t2.right);
        return t1;
    }
}

/**
 * Definition for a binary tree node.
 * public class TreeNode {
 *     int val;
 *     TreeNode left;
 *     TreeNode right;
 *     TreeNode(int x) { val = x; }
 * }
 */
public class Solution {
    public TreeNode mergeTrees(TreeNode t1, TreeNode t2) {
        if (t1 == null)
            return t2;
        Stack < TreeNode[] > stack = new Stack < > ();
        stack.push(new TreeNode[] {t1, t2});
        while (!stack.isEmpty()) {
            TreeNode[] t = stack.pop();
            if (t[0] == null || t[1] == null) {
                continue;
            }
            t[0].val += t[1].val;
            if (t[0].left == null) {
                t[0].left = t[1].left;
            } else {
                stack.push(new TreeNode[] {t[0].left, t[1].left});
            }
            if (t[0].right == null) {
                t[0].right = t[1].right;
            } else {
                stack.push(new TreeNode[] {t[0].right, t[1].right});
            }
        }
        return t1;
    }
}

```

#### 17. 二叉树中和为某一值的路径

```java
//回溯+前序
class Solution {
    LinkedList<List<Integer>> res = new LinkedList<>();
    LinkedList<Integer> path = new LinkedList<>(); 
    public List<List<Integer>> pathSum(TreeNode root, int sum) {
        recur(root, sum);
        return res;
    }
    void recur(TreeNode root, int tar) {
        if(root == null) return;
        path.add(root.val);
        tar -= root.val;
        if(tar == 0 && root.left == null && root.right == null)
            res.add(new LinkedList(path));
        recur(root.left, tar);
        recur(root.right, tar);
        path.removeLast();
    }
}
```



#### 18. 重建二叉树：输入某二叉树的前序遍历和中序遍历的结果，请重新构造出该二叉树

```java
//递归
public TreeNode buildTree(int[] preorder, int[] inorder) {
    //把前序遍历的值和中序遍历的值放到list中
    List<Integer> preorderList = new ArrayList<>();
    List<Integer> inorderList = new ArrayList<>();
    for (int i = 0; i < preorder.length; i++) {
        preorderList.add(preorder[i]);
        inorderList.add(inorder[i]);
    }
    return helper(preorderList, inorderList);
}

private TreeNode helper(List<Integer> preorderList, List<Integer> inorderList) {
    if (inorderList.size() == 0)
        return null;
    //前序遍历的第一个值就是根节点
    int rootVal = preorderList.remove(0);
    //创建跟结点
    TreeNode root = new TreeNode(rootVal);
    //查看根节点在中序遍历中的位置，然后再把中序遍历的数组劈两半，前面部分是
    //根节点左子树的所有值，后面部分是根节点右子树的所有值
    int mid = inorderList.indexOf(rootVal);
    //[0，mid)是左子树的所有值，inorderList.subList(0, mid)表示截取inorderList
    //的值，截取的范围是[0，mid)，包含0不包含mid。
    root.left = helper(preorderList, inorderList.subList(0, mid));
    //[mid+1，inorderList.size())是右子树的所有值，
    // inorderList.subList(mid + 1, inorderList.size())表示截取inorderList
    //的值，截取的范围是[mid+1，inorderList.size())，包含mid+1不包含inorderList.size()。
    root.right = helper(preorderList, inorderList.subList(mid + 1, inorderList.size()));
    return root;
}

//栈
public TreeNode buildTree(int[] preorder, int[] inorder) {
    if (preorder.length == 0)
        return null;
    Stack<TreeNode> s = new Stack<>();
    //前序的第一个其实就是根节点
    TreeNode root = new TreeNode(preorder[0]);
    TreeNode cur = root;
    for (int i = 1, j = 0; i < preorder.length; i++) {
        //第一种情况
        if (cur.val != inorder[j]) {
            cur.left = new TreeNode(preorder[i]);
            s.push(cur);
            cur = cur.left;
        } else {
            //第二种情况
            j++;
            //找到合适的cur，然后确定他的右节点
            while (!s.empty() && s.peek().val == inorder[j]) {
                cur = s.pop();
                j++;
            }
            //给cur添加右节点
            cur = cur.right = new TreeNode(preorder[i]);
        }
    }
    return root;
}
```



#### 19. 求一棵完全二叉树的节点个数，时间复杂度低于O(N)

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
    public int countNodes(TreeNode root) {
        if(root == null){
           return 0;
        } 
        int left = countLevel(root.left);
        int right = countLevel(root.right);
        if(left == right){
            return countNodes(root.right) + (1<<left);
        }else{
            return countNodes(root.left) + (1<<right);
        }
    }
    private int countLevel(TreeNode root){
        int level = 0;
        while(root != null){
            level++;
            root = root.left;
        }
        return level;
    }
}
```



#### 20. 找二叉树左下角的值

```java
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
class Solution {
    public int findBottomLeftValue(TreeNode root) {
        // target: result variable
        int target = root.val;
        Queue<TreeNode> queue = new LinkedList<>();
        queue.add(root);
        // BFS
        while(!queue.isEmpty()){
            TreeNode curr = queue.poll();
            if (curr.right != null) {queue.add(curr.right); target=curr.right.val;}
            if (curr.left != null) {queue.add(curr.left); target=curr.left.val;}           
        }
        return target;
    }
}
//dfs
class Solution {
    //找最深的一层
    int max = -1;
    int value = 0;

    public int findBottomLeftValue(TreeNode root) {
        get(root,0);
        return value;
    }

    public void get(TreeNode node,int num){
        if(node==null){
            return;
        }
        //第一次大于的时候就是每层最左边的节点
        if(num>max){
            max = num;
            value = node.val;
        }
        get(node.left,num+1);
        get(node.right,num+1);

    }
}
```



#### 21. 把二叉搜索树转换为累加树

```java
class Solution {
    private int sum = 0;

    public TreeNode convertBST(TreeNode root) {
        if (root != null) {
            convertBST(root.right);
            sum += root.val;
            root.val = sum;
            convertBST(root.left);
        }
        return root;
    }
}

//非递归
class Solution {
    public TreeNode convertBST(TreeNode root) {
        int sum = 0;
        TreeNode node = root;
        Stack<TreeNode> stack = new Stack<TreeNode>();

        while (!stack.isEmpty() || node != null) {
            /* push all nodes up to (and including) this subtree's maximum on
             * the stack. */
            while (node != null) {
                stack.add(node);
                node = node.right;
            }

            node = stack.pop();
            sum += node.val;
            node.val = sum;

            /* all nodes with values between the current and its parent lie in
             * the left subtree. */
            node = node.left;
        }

        return root;
    }
}d
```



> 22-25题是二叉树信息收集问题：

#### 22. 舞会的最大活跃度

```java
/*
题目：一个公司的上下级关系是一棵多叉树，这个公司要举办晚会，你作为组织者已经摸清了大家的心理：一个员工的直接上级如果到场，这个员工肯定不会来。每个员工都有一个活跃度的值（值越大，晚会上越活跃），你可以给某个员工发邀请函以决定谁来，怎么让舞会的气氛最活跃？返回最大的活跃值。
*/
public class MaxHappyInDanceMeeting {
 
    public static class Node{
        int happy;
        List<Node> subs;
 
        public Node(int happy){
            this.happy = happy;
            this.subs = new ArrayList<>();
        }
    }
 
    public static class ReturnData{
        int maxHappy;
        public ReturnData(int maxHappy){
            this.maxHappy = maxHappy;
        }
    }
 
    public static ReturnData process(Node node){
        if(node.subs.size() == 0){
            // 如果没有子节点了，就直接返回自己的活跃度
            return new ReturnData(node.happy);
        }
        // case1：node 去
        int go_Happy = node.happy;
        // case2：ndoe 不去
        int unGo_Happy = 0;
        for(Node sub : node.subs){
            unGo_Happy += process(sub).maxHappy;
        }
        return new ReturnData(Math.max(go_Happy, unGo_Happy));
    }
 
    public static int maxPartyHappy(Node root){
        if(root == null){
            return 0;
        }
        return process(root).maxHappy;
    }
}
```



#### 23. 求一棵二叉树中最大二叉搜索子树的节点个数

```java
/*
这类题一般都有一个大前提：假设对于以树中的任意结点为头结点的子树，我们都能求得其最大搜索二叉子树的结点个数，那么答案一定就在其中。

而对于以任意结点为头结点的子树，其最大搜索二叉子树的求解分为三种情况（列出可能性）：

整棵树的最大搜索二叉子树存在于左子树中。这要求其左子树中存在最大搜索二叉子树，而其右子树不存在。
整棵树的最大搜索二叉子树存在于右子树中。这要求其右子树中存在最大搜索二叉子树，而其左子树不存在。
最整棵二叉树的最大搜索二叉子树就是其本身。这需要其左子树就是一棵搜索二叉子树且左子树的最大值结点比头结点小、其右子树就是一棵搜索二叉子树且右子树的最小值结点比头结点大。
要想区分这三种情况，我们需要收集的信息：

子树中最大搜索二叉树的大小
子树的头结点
子树的最大值结点
子树的最小值结点
因此我们就可以开始我们的高度套路了：

1、 将要从子树收集的信息封装成一个 ReturnData，代表处理完这一棵子树要向上级返回的信息。
2、假设我利用子过程收集到了子树的信息，接下来根据子树的信息和分析问题时列出的情况加工出当前这棵树要为上级提供的所有信息，并返回给上级（整合信息）。
3、确定 baseCase，子过程到子树为空时，停。
*/
public class FIndMaxSizeBST {
 
    public static class Node{
        public int data;
        public Node left;
        public Node right;
 
        public Node(int data){
            this.data = data;
        }
    }
 
    // 构造返回信息
    public static class ReturnData{
        public int size;   // 最大二叉搜索子树的大小
        public Node head;  // 子树的头结点
        public int max;    // 子树中的最大结点值
        public int min;    // 子树中的最小结点值
 
        public ReturnData(int size, Node head, int max, int min){
            this.size = size;
            this.head = head;
            this.max = max;
            this.min = min;
        }
    }
 
    public static ReturnData process(Node root){
        if(root == null){
            return new ReturnData(0, null, Integer.MIN_VALUE, Integer.MAX_VALUE);
        }
 
        ReturnData leftInfo = process(root.left);
        ReturnData rightInfo = process(root.right);
 
        int leftSize = leftInfo.size;    // case1
        int rightSize = rightInfo.size;  // case2
        int selfSize = 0;
 
        if(leftInfo.head == root.left && rightInfo.head == root.right && leftInfo.max < root.data && rightInfo.min > root.data){
            selfSize = leftSize + rightSize + 1;  // case3
        }
 
        int maxSize = Math.max(Math.max(leftSize, rightSize), selfSize);
        Node maxHead = leftSize > rightSize ? leftInfo.head : selfSize > rightSize ? root : rightInfo.head;
 
        return new ReturnData(maxSize, maxHead,
                Math.max(Math.max(leftInfo.max, rightInfo.max), root.data),
                Math.min(Math.min(leftInfo.min, rightInfo.min), root.data));
    }
}
```



#### 24. 求一个二叉树的最远距离

```java
/*
大前提：如果对于以该树的任意结点作为头结点的子树中，如果我们能够求得所有这些子树的最大距离，那么答案就在其中。

对于该树的任意子树，其最大距离的求解分为以下三种情况：

该树的最大距离是左子树的最大距离（最大距离的两个节点都在左子树中）。
该树的最大距离是右子树的最大距离（最大距离的两个节点都在右子树中）。
该树的最大距离是从左子树的最深的那个结点经过该树的头结点走到右子树的最深的那个结点。
要从子树收集的信息：

子树的最大距离
子树的深度
*/
public class FIndMaxDisInTree {
 
    public class Node{
        public int val;
        public Node left;
        public Node right;
 
        public Node(int val){
            this.val = val;
        }
    }
 
    public static class ReturnData{
        public int maxDis;   // 最大距离
        public int height;   // 最大高度
 
        public ReturnData(int maxDis, int height){
            this.maxDis = maxDis;
            this.height = height;
        }
    }
 
    public static int getMaxDis(Node root){
        return process(root).maxDis;
    }
 
    // 时间复杂度都是O(N),其实就是二叉树的后序遍历：先找左树要信息，再找右树要信息，最后给出自己的信息
    public static ReturnData process(Node node){
        if(node == null){
            return new ReturnData(0, 0);
        }
 
        ReturnData leftData = process(node.left);
        ReturnData rightData = process(node.right);
        int maxDis = Math.max(leftData.maxDis, rightData.maxDis);
        int height = Math.max(leftData.height, rightData.height) + 1;
        // 下面这步要用到高度信息，所以必须放在高度信息收集的后面
        maxDis = Math.max(maxDis, leftData.height + rightData.height + 1);
        return new ReturnData(maxDis, height);
    }
}
```



#### 25. 二叉树的最大路径和

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

#### 26. 二叉树剪枝

```java
class Solution {
    public TreeNode pruneTree(TreeNode root) {
        if(root == null) {
            return null;
        }
        root.left = pruneTree(root.left);
        root.right = pruneTree(root.right);
        if(root.left == null && root.right == null && root.val == 0) {
            return null;
        }
        return root;
    }
}
```

#### 27. 中序和后序确定二叉树

````java
class Solution {
    public TreeNode buildTree(int[] inorder, int[] postorder) {
        if(inorder==null || postorder==null) {
            return null;
        }
        return helper(inorder,postorder);
    }

    private TreeNode helper(int[] in, int[] post) {
        if(in.length==0) {
            return null;
        }
        //根据后序数组的最后一个元素，创建根节点
        TreeNode root = new TreeNode(post[post.length-1]);
        //在中序数组中查找值等于【后序数组最后一个元素】的下标
        for(int i=0;i<in.length;++i) {
            if(in[i]==post[post.length-1]) {
                //确定这个下标i后，将中序数组分成两部分，后序数组分成两部分
                int[] inLeft = Arrays.copyOfRange(in,0,i);
                int[] inRight = Arrays.copyOfRange(in,i+1,in.length);
                int[] postLeft = Arrays.copyOfRange(post,0,i);
                int[] postRight = Arrays.copyOfRange(post,i,post.length-1);
                //递归处理中序数组左边，后序数组左边
                root.left = helper(inLeft,postLeft);
                //递归处理中序数组右边，后序数组右边
                root.right = helper(inRight,postRight);
                break;
            }
        }
        return root;
    }
}

````

#### 28. 二叉树的深度

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
    public int maxDepth(TreeNode root) {
        if (root == null) {
            return 0;
        }
        return Math.max(maxDepth(root.left), maxDepth(root.right)) + 1; 
    }
}
```

#### 29. 从上到下遍历

```java
class Solution {
    public int[] levelOrder(TreeNode root) {
        if(root == null) return new int[0];
        Queue<TreeNode> queue = new LinkedList<>(){{ add(root); }};
        ArrayList<Integer> ans = new ArrayList<>();
        while(!queue.isEmpty()) {
            TreeNode node = queue.poll();
            ans.add(node.val);
            if(node.left != null) queue.add(node.left);
            if(node.right != null) queue.add(node.right);
        }
        int[] res = new int[ans.size()];
        for(int i = 0; i < ans.size(); i++)
            res[i] = ans.get(i);
        return res;
    }
}
```

#### 30.  最近公共祖先

```java
/*
因为lowestCommonAncestor(root, p, q)的功能是找出以root为根节点的两个节点p和q的最近公共祖先，所以递归体分三种情况讨论：

如果p和q分别是root的左右节点，那么root就是我们要找的最近公共祖先
如果p和q都是root的左节点，那么返回lowestCommonAncestor(root.left,p,q)
如果p和q都是root的右节点，那么返回lowestCommonAncestor(root.right,p,q)
边界条件讨论：

如果root是null，则说明我们已经找到最底了，返回null表示没找到
如果root与p相等或者与q相等，则返回root
如果左子树没找到，递归函数返回null，证明p和q同在root的右侧，那么最终的公共祖先就是右子树找到的结点
如果右子树没找到，递归函数返回null，证明p和q同在root的左侧，那么最终的公共祖先就是左子树找到的结点
*/
public TreeNode lowestCommonAncestor(TreeNode root, TreeNode p, TreeNode q) {
    if(root==null || root==p || root==q)
        return root;
    
    TreeNode leftNode=lowestCommonAncestor(root.left,p,q);
    TreeNode rightNode=lowestCommonAncestor(root.right,p,q);

    if(leftNode==null)
        return rightNode;
    if(rightNode==null)
        return leftNode;

    return root;
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

```java
import java.util.Collection;
import java.util.LinkedHashMap;
import java.util.LinkedList;
import java.util.List;
import java.util.Map;
import java.util.Queue;

/*
 * 用来实现拓扑排序的有向无环图
 */
public class DirectedGraph {

    private class Vertex{
        private String vertexLabel;// 顶点标识
        private List<Edge> adjEdges;
        private int inDegree;// 该顶点的入度

        public Vertex(String verTtexLabel) {
            this.vertexLabel = verTtexLabel;
            inDegree = 0;
            adjEdges = new LinkedList<Edge>();
        }
    }

    private class Edge {
        private Vertex endVertex;

        // private double weight;
        public Edge(Vertex endVertex) {
            this.endVertex = endVertex;
        }
    }

    private Map<String, Vertex> directedGraph;

    public DirectedGraph(String graphContent) {
        directedGraph = new LinkedHashMap<String, DirectedGraph.Vertex>();
        buildGraph(graphContent);
    }

    private void buildGraph(String graphContent) {
        String[] lines = graphContent.split("\n");
        Vertex startNode, endNode;
        String startNodeLabel, endNodeLabel;
        Edge e;
        for (int i = 0; i < lines.length; i++) {
            String[] nodesInfo = lines[i].split(",");
            startNodeLabel = nodesInfo[1];
            endNodeLabel = nodesInfo[2];
            startNode = directedGraph.get(startNodeLabel);
            if(startNode == null){
                startNode = new Vertex(startNodeLabel);
                directedGraph.put(startNodeLabel, startNode);
            }
            endNode = directedGraph.get(endNodeLabel);
            if(endNode == null){
                endNode = new Vertex(endNodeLabel);
                directedGraph.put(endNodeLabel, endNode);
            }

            e = new Edge(endNode);//每读入一行代表一条边
            startNode.adjEdges.add(e);//每读入一行数据,起始顶点添加一条边
            endNode.inDegree++;//每读入一行数据,终止顶点入度加1
        }
    }

    public void topoSort() throws Exception{
        int count = 0;

        Queue<Vertex> queue = new LinkedList<>();// 拓扑排序中用到的栈,也可用队列.
        //扫描所有的顶点,将入度为0的顶点入队列
        Collection<Vertex> vertexs = directedGraph.values();
        for (Vertex vertex : vertexs)
            if(vertex.inDegree == 0)
                queue.offer(vertex);

        while(!queue.isEmpty()){
            Vertex v = queue.poll();
            System.out.print(v.vertexLabel + " ");
            count++;
            for (Edge e : v.adjEdges)
                if(--e.endVertex.inDegree == 0)
                    queue.offer(e.endVertex);
        }
        if(count != directedGraph.size())
            throw new Exception("Graph has circle");
    }
}
```



### 9. 数字与位运算

#### 1. 两数之和、三数之和

```java
class Solution {
    public int[] twoSum(int[] nums, int target) {
        Map<Integer, Integer> map = new HashMap<>();
        for (int i = 0; i < nums.length; i++) {
            int complement = target - nums[i];
            if (map.containsKey(complement)) {
                return new int[] { map.get(complement), i };
            }
            map.put(nums[i], i);
        }
        throw new IllegalArgumentException("No two sum solution");
    }
}
```



```java
/*
给你一个包含 n 个整数的数组 nums，判断 nums 中是否存在三个元素 a，b，c ，使得 a + b + c = 0 ？请你找出所有满足条件且不重复的三元组。
*/
class Solution {
    public List<List<Integer>> threeSum(int[] nums) {
        int n = nums.length;
        Arrays.sort(nums);
        List<List<Integer>> ans = new ArrayList<List<Integer>>();
        // 枚举 a
        for (int first = 0; first < n; ++first) {
            // 需要和上一次枚举的数不相同
            if (first > 0 && nums[first] == nums[first - 1]) {
                continue;
            }
            // c 对应的指针初始指向数组的最右端
            int third = n - 1;
            int target = -nums[first];
            // 枚举 b
            for (int second = first + 1; second < n; ++second) {
                // 需要和上一次枚举的数不相同
                if (second > first + 1 && nums[second] == nums[second - 1]) {
                    continue;
                }
                // 需要保证 b 的指针在 c 的指针的左侧
                while (second < third && nums[second] + nums[third] > target) {
                    --third;
                }
                // 如果指针重合，随着 b 后续的增加
                // 就不会有满足 a+b+c=0 并且 b<c 的 c 了，可以退出循环
                if (second == third) {
                    break;
                }
                if (nums[second] + nums[third] == target) {
                    List<Integer> list = new ArrayList<Integer>();
                    list.add(nums[first]);
                    list.add(nums[second]);
                    list.add(nums[third]);
                    ans.add(list);
                }
            }
        }
        return ans;
    }
}
```



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

```java
public class solution {
    public void printNumbers(int n) {
        StringBuilder str = new StringBuilder();
        // 将str初始化为n个'0'字符组成的字符串
        for (int i = 0; i < n; i++) {
            str.append('0');
        }
        while(!increment(str)){
            // 去掉左侧的0
            int index = 0;
            while (index < str.length() && str.charAt(index) == '0'){
                index++;
            }
            System.out.println(str.toString().substring(index));
        }
    }

    public boolean increment(StringBuilder str) {
        boolean isOverflow = false;
        for (int i = str.length() - 1; i >= 0; i--) {
            char s = (char)(str.charAt(i) + 1);
            // 如果s大于'9'则发生进位
            if (s > '9') {
                str.replace(i, i + 1, "0");
                if (i == 0) {
                    isOverflow = true;
                }
            }
            // 没发生进位则跳出for循环
            else {
                str.replace(i, i + 1, String.valueOf(s));
                break;
            }
        }
        return isOverflow;
    }
}
```



#### 4. 数值的整数次方

```java
class Solution {
    public double myPow(double x, int n) {
        if(x == 0) return 0;
        long b = n;
        double res = 1.0;
        if(b < 0) {
            x = 1 / x;
            b = -b;
        }
        while(b > 0) {
            if((b & 1) == 1) res *= x;
            x *= x;
            b >>= 1;
        }
        return res;
    }
}
```



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

```java
//归并
    void merge(int[] arr, int start, int end) {
        if (start == end) return;
        int mid = (start + end) / 2;
        merge(arr, start, mid);
        merge(arr, mid + 1, end);

        int[] temp = new int[end - start + 1];
        int i = start, j = mid + 1, k = 0;
        while(i <= mid && j <= end)
            temp[k++] = arr[i] < arr[j] ? arr[i++] : arr[j++];
        while(i <= mid)
            temp[k++] = arr[i++];
        while(j <= end)
            temp[k++] = arr[j++];
        System.arraycopy(temp, 0, arr, start, end);
    }

//题解(mid-i+1)
public class Solution {

    public int reversePairs(int[] nums) {
        int len = nums.length;

        if (len < 2) {
            return 0;
        }

        int[] copy = new int[len];
        for (int i = 0; i < len; i++) {
            copy[i] = nums[i];
        }

        int[] temp = new int[len];
        return reversePairs(copy, 0, len - 1, temp);
    }

    /**
     * nums[left..right] 计算逆序对个数并且排序
     *
     * @param nums
     * @param left
     * @param right
     * @param temp
     * @return
     */
    private int reversePairs(int[] nums, int left, int right, int[] temp) {
        if (left == right) {
            return 0;
        }

        int mid = left + (right - left) / 2;
        int leftPairs = reversePairs(nums, left, mid, temp);
        int rightPairs = reversePairs(nums, mid + 1, right, temp);

        // 如果整个数组已经有序，则无需合并，注意这里使用小于等于
        if (nums[mid] <= nums[mid + 1]) {
            return leftPairs + rightPairs;
        }

        int crossPairs = mergeAndCount(nums, left, mid, right, temp);
        return leftPairs + rightPairs + crossPairs;
    }

    /**
     * nums[left..mid] 有序，nums[mid + 1..right] 有序
     *
     * @param nums
     * @param left
     * @param mid
     * @param right
     * @param temp
     * @return
     */
    private int mergeAndCount(int[] nums, int left, int mid, int right, int[] temp) {
        for (int i = left; i <= right; i++) {
            temp[i] = nums[i];
        }

        int i = left;
        int j = mid + 1;

        int count = 0;
        for (int k = left; k <= right; k++) {
            // 有下标访问，得先判断是否越界
            if (i == mid + 1) {
                nums[k] = temp[j];
                j++;
            } else if (j == right + 1) {
                nums[k] = temp[i];
                i++;
            } else if (temp[i] <= temp[j]) {
                // 注意：这里是 <= ，写成 < 就不对，请思考原因
                nums[k] = temp[i];
                i++;
            } else {
                nums[k] = temp[j];
                j++;
                
                // 在 j 指向的元素归并回去的时候，计算逆序对的个数，只多了这一行代码
                count += (mid - i + 1);
            }
        }
        return count;
    }
}

```



#### 2. 小和问题：把数组中每一个数左边比当前数小的累加起来，叫着这个数组的小和

```java
import java.io.*;
class test  
{   public static int smallSum(int[] arr) {
        if (arr == null || arr.length < 2) {
            return 0;
        }
        return mergeSort(arr, 0, arr.length - 1);
    }

    public static int mergeSort(int[] arr, int l, int r) {
        if (l == r) {
            return 0;
        }
        int mid = l + ((r - l) >> 1);
        return mergeSort(arr, l, mid) + mergeSort(arr, mid + 1, r) + merge(arr, l, mid, r);
    }

    public static int merge(int[] arr, int l, int m, int r) {
        int[] help = new int[r - l + 1];
        int i = 0;
        int p1 = l;
        int p2 = m + 1;
        int res = 0;
        while (p1 <= m && p2 <= r) {
            res += arr[p1] < arr[p2] ? (r - p2 + 1) * arr[p1] : 0;
            help[i++] = arr[p1] < arr[p2] ? arr[p1++] : arr[p2++];
        }
        while (p1 <= m) {
            help[i++] = arr[p1++];
        }
        while (p2 <= r) {
            help[i++] = arr[p2++];
        }
        for (i = 0; i < help.length; i++) {
            arr[l + i] = help[i];
        }
        return res;
    }
    public static void main (String[] args) throws java.lang.Exception
    {
        int[] arr = { 1,2,3};
        System.out.println(smallSum(arr));
    }
}
```

### 11. 矩阵问题

#### 1. 顺时针打印矩阵

```java
class Solution {
    public int[] spiralOrder(int[][] matrix) {
        if (matrix == null || matrix.length == 0 || matrix[0].length == 0) {
            return new int[0];
        }
        int rows = matrix.length, columns = matrix[0].length;
        int[] order = new int[rows * columns];
        int index = 0;
        int left = 0, right = columns - 1, top = 0, bottom = rows - 1;
        while (left <= right && top <= bottom) {
            for (int column = left; column <= right; column++) {
                order[index++] = matrix[top][column];
            }
            for (int row = top + 1; row <= bottom; row++) {
                order[index++] = matrix[row][right];
            }
            if (left < right && top < bottom) {
                for (int column = right - 1; column > left; column--) {
                    order[index++] = matrix[bottom][column];
                }
                for (int row = bottom; row > top; row--) {
                    order[index++] = matrix[row][left];
                }
            }
            left++;
            right--;
            top++;
            bottom--;
        }
        return order;
    }
}
```

#### 2. 将一个正方形旋转 90 度

```java
class Solution {
    public void rotate(int[][] matrix) {
        int n = matrix.length;
        // 先以对角线（左上-右下）为轴进行翻转
        for (int i = 0; i < n - 1; i++) {
            for (int j = i + 1; j < n; j++) {
                int tmp = matrix[i][j];
                matrix[i][j] = matrix[j][i];
                matrix[j][i] = tmp;
            }
        }
        // 再对每一行以中点进行翻转
        int mid = n >> 1;
        for (int i = 0; i < n; i++) {
            for (int j = 0; j < mid; j++) {
                int tmp = matrix[i][j];
                matrix[i][j] = matrix[i][n - 1 - j];
                matrix[i][n - 1 - j] = tmp;
            }
        }
    }
}
```

#### 3. 之字型打印矩阵

```java

public int[] printMatrix(int[][] mat, int n, int m) {
    int[] arr = new int [n*m];
    //hang记录你的行数，count记录的是你返回的字符数组的长度
     int hang = 0;
     int count = 0;
       //偶数行顺序，奇数行倒序
       for(int i = 0; i < mat.length; i++ ){
           for(int j = 0; j < mat[i].length; j++){
               //判断数奇数行还是偶数行
               if(hang%2 == 0){
                   arr[count++] = mat[i][j];
               }else{
                   arr[count++] = mat[i][m-1-j];
               }
           }
           //写完了一行，行数需要+1
           hang++;
       }
       return arr;
   }
```

#### 4. 在一个行和列都有序的 m 行 n 列的矩阵中查找一个数是否存在

```java
/*
从矩阵 matrix 左下角元素（索引设为 (i, j) ）开始遍历，并与目标值对比：
当 matrix[i][j] > target 时： 行索引向上移动一格（即 i--），即消去矩阵第 i 行元素；
当 matrix[i][j] < target 时： 列索引向右移动一格（即 j++），即消去矩阵第 j 列元素；
当 matrix[i][j] == target 时： 返回 true 。
若行索引或列索引越界，则代表矩阵中无目标值，返回 false 。
*/
class Solution {
    public boolean findNumberIn2DArray(int[][] matrix, int target) {
        if (matrix == null || matrix.length == 0 || matrix[0].length == 0) {
            return false;
        }
        int rows = matrix.length, columns = matrix[0].length;
        int row = 0, column = columns - 1;
        while (row < rows && column >= 0) {
            int num = matrix[row][column];
            if (num == target) {
                return true;
            } else if (num > target) {
                column--;
            } else {
                row++;
            }
        }
        return false;
    }
}
```



### 12. 递归

#### 1. 求 n! 的结果

```java
public class Practice{
   public static long factorial1(int n){
    if(n==0||n==1){  //0或1的阶乘都是1
     return 1;
    }
    return factorial1(n-1)*n;
   }
   public static long factorial2(int n){
    long fact=1;
    for(int i=1;i<=n;i++){
     fact=fact*i;
    }
    return fact;
   }
   public static void main(String[] args){
    int n=12;
    //通过递归
    long fact1 = factorial1(n);
    //通过循环
    long fact2 = factorial2(n);
    System.out.printf("阶乘是%d%n",fact1);
    System.out.printf("阶乘是%d%n",fact2);
   }
 }

```

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

```java
public class SubSequences {
    /**
     * 打印字符串的所有子序列
     *
     * @param chars   字符数组
     * @param index 当前所在的字符位置
     * @param res   之前获得的子序列
     */
    public static void printSubSequences(char[] chars, int index, String res) {
        if (index == chars.length) {
            System.out.print(res + " ");
            return;
        }
        printSubSequences(chars,index+1,res);
        printSubSequences(chars,index+1,res+chars[index]);
    }

    public static void main(String[] args) {
        String test = "abc";
        printSubSequences(test.toCharArray(),0,"");
    }
}
```

#### 4. 打印一个字符串的全排列

```java
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

```



#### 5. 母牛问题：母牛每年生一只母牛，新出生的母牛成长三年后也能每年生一只母牛，假设不会死。求 N 年后，母牛的数量

```java
 public class CowProblem {
	
	/*
	 * 由于所有的母牛都不会死，所以第N-1年的牛会毫无损失的活到第N年。
	 * 同时，所有成熟的牛都会生一头新的母牛，那么成熟牛的数量如何估计？
	 * 就是第N-3年的所有牛，到第N年肯定都是成熟的牛，期间出生的牛肯定都没有成熟。
	 * 所以  S（n）= S（n-1）+S（n-3）。
	 * 初始项为：S（1）=1；S（2）=2；S（3）=3.
	 * 
	 * */
	
	public static int cowCompute(int n) {
		
		int result=0;
		
		if(n<1) {
			return 0;
		}
		
		if(n==1 || n==2 || n==3) {
			return n;
		}
		
		return cowCompute(n-1)+cowCompute(n-3);

	}
 
	public static void main(String[] args) {
		for(int i=1;i<=10;i++) {
			int result=cowCompute(i);
			System.out.println("第 "+i+"年： "+result);
		}
	}
}
```



#### 6. 机器人走路问题

```java
/*
给定四个参数N、P、M、K。表示：
N : 一共有1～N个位置
P : 一共有P步要走
M : 机器人初始停留在M位置上
K : 机器人想要去的位置是K
题目：已知，如果机器人来到 1 位置，那么下一步一定会走到 2 位置。如果机器人来到 N 位置，那么下一步一定会走到 N - 1 位置；如果机器人在中间的位置，那么下一步既可以走向左，也可以走向右。请返回，机器人如果初始停留在 M 位置，经过 P 步之后，机器人来到 K 位置的走法有多少种。
*/
public class RobotWork {
 
    /**
     * @param N ：共N个位置
     * @param M ：开始位置
     * @param P ：可以走的步数
     * @param K ： 目标位置
     * @return
     */
    public static int walk(int N, int M, int P, int K){
        if(P == 0){
            // basecase
            return M == K ? 1 : 0;
        }
        // 开始位置和结束位置只能往一个方向走
        if(M == 1){
            return walk(N, M + 1, P - 1, K);
        }else if(M == N){
            return walk(N, M - 1, P - 1, K);
        }
        // 向左走和向右走两种选择
        return walk(N, M + 1, P - 1, K) + walk(N, M - 1, P - 1, K);
    }
}

//DP
public class RobotWork {
 
    public static int walkDP(int N, int M, int P, int K){
        // 从递归的过程看：变量有 M 和 P
        int dp[][] = new int[N + 1][P + 1];
        // basecase：当在目标位置，还剩一步的时候
        dp[K][0] = 1;
        for(int j = 1; j <= P; j++){
            for(int i = 1; i <= N; i++){
                if(i - 1 < 1){
                    // 在第一个位置上
                    dp[i][j] = dp[i + 1][j - 1];
                }else if(i + 1 > N){
                    // 在最后一个位置上
                    dp[i][j] = dp[i - 1][j - 1];
                }else{
                    dp[i][j] = dp[i + 1][j - 1] + dp[i - 1][j - 1];
                }
            }
        }
        return dp[M][P];
    }
}
```

#### 7. 给定一个数字组成的字符串，返回有多少种合法的 IPV4 组合

```java
/*
题目：给定一个数字组成的字符串 str，返回有多少种合法的 IPV4 组合。
举例：
str = “245111”，只有一种合法 IPV4 组合：245.1.1.1，所以返回 1

str = “11111”，所有合法的 IPV4 组合为：
1.1.1.11
1.1.11.1
1.11.1.1
11.1.1.1
所以返回 4

str = “100111”，所有合法的 IPV4 组合为：
100.1.1.1
所以返回1

注意：10.01.1.1，并不合法，因为 IPV4 的部分不会出现 01这样的情况。
分析：IPV4 块不能超过 4 个，对于当前 index 指向的字符 来说，有三种情况

1、index 指向的字符自己作为一块；

2）index 和 index + 1 作为一块 【条件是 index != 0 才行】

3）index 和 index + 1、index + 2 作为一块 【条件是 它们组成的数 要 < 256】
*/

//递归
public class IPV4Num {
 
    public static int getIPV4Num(String str){
        if(str == null || str.length() < 4 || str.length() > 12){
            return 0;
        }
        char[] chars = str.toCharArray();
        return process(chars, 0, 0);
    }
 
    /**
     * 从index位置开始有多少种满足要求的ipv4的划分方式
     * @param chars ：字符数组
     * @param index ：从index位置开始
     * @param parts ：之前已经形成了多少部分(ipv4一共4块)
     * @return
     */
    public static int process(char[] chars, int index, int parts){
        if(parts > 4){
            // basecase1：parts最大值为4
            return 0;
        }
        if(index == chars.length){
            // basecase2：只剩下一个字符数时，只有是parts=4时才是合法的
            return parts == 4 ? 1 : 0;
        }
 
        // 选择1：当前index指向的字符独立成一块
        int res = process(chars, index + 1, parts + 1);
        // 选择2：让index和index+1位置的字符，共同构成一个块
        if(chars[index] == '0' || index == chars.length - 1){
            // 如果当前index指向的字符为0或者到了整个字符串的最后一个字符时，不可能满足选择2这种情况
            return res;
        }
        // 选择3：index、index+1、index+2，三个数组成一个块
        if(index + 2 < chars.length){
            int sum = (chars[index] - '0') * 100 + (chars[index + 1] - '0') * 10 + (chars[index] - '0');
            if(sum < 256){
                res += process(chars, index + 3, parts + 1);
            }
        }
        return res;
    }
}

//dp
public class IPV4Num {
 
    // 动态规划实现
    public static int getIPV4NumDynamic(String str){
        if(str == null || str.length() < 4 || str.length() > 12){
            return 0;
        }
 
        char[] chars = str.toCharArray();
        int[][] dp = new int[chars.length + 1][5];
        for(int i = dp.length - 1, j = 0; j < dp[0].length; j++){
            if(j == 4){
                dp[i][j] = 1;
            }else{
                dp[i][j] = 0;
            }
        }
 
        int res = 0;
        // 从上到下、从右往左依次填
        for(int i = dp.length - 2; i >= 0; i--){
            for(int parts = 3; parts >= 0; parts--){
                res = dp[i + 1][parts + 1];
                if(chars[i] == '0' || i == chars.length - 1){
                    dp[i][parts] = res;
                    continue;
                }
                res += dp[i + 2][parts + 1];
                if(i + 2 < chars.length){
                    int sum = (chars[i] - '0') * 100 + (chars[i + 1] - '0') * 10 + (chars[i] - '0');
                    if(sum < 256){
                        res += dp[i + 3][parts + 1];
                    }
                }
                dp[i][parts] = res;
            }
        }
        return dp[0][0];
    }
}
```

#### 8. 斐波那契数列

```java
//迭代
public class Solution {
    public int Fibonacci(int n) {
        int pre=1;
        int prepre=0;
        int result=0;
        if(n==0){
            return 0;
        }
        if(n==1){
            return 1;
        }
        for(int i=2;i<=n;i++){
            result=pre+prepre;
            prepre=pre;
            pre=result;
        }
        return result;
    }
}

//递归
public static int getFib(int n){
        if(n < 0){
            return -1;
        }else if(n == 0){
            return 0;
        }else if(n == 1 || n ==2){
            return 1;
        }else{
            return getFib(n - 1) + getFib(n - 2);
        }
    }
```

### 13. 动态规划

#### 1. 机器人范围

```java
/*
地上有一个m行n列的方格，从坐标 [0,0] 到坐标 [m-1,n-1] 。一个机器人从坐标 [0, 0] 的格子开始移动，它每次可以向左、右、上、下移动一格（不能移动到方格外），也不能进入行坐标和列坐标的数位之和大于k的格子。例如，当k为18时，机器人能够进入方格 [35, 37] ，因为3+5+3+7=18。但它不能进入方格 [35, 38]，因为3+5+3+8=19。请问该机器人能够到达多少个格子？
*/
class Solution {
    // //DFS
    int m,n,k;
    boolean[][] visited;
    public int movingCount(int m, int n, int k) {
        this.m = m;
        this.n = n;
        this.k = k;
        visited = new boolean[m][n];
        return dfs(0,0);
    }
    public int dfs(int i,int j){
        if(i>=m || j>=n || visited[i][j] || count(i,j)>k){
            return 0;
        }
        visited[i][j] = true;
        return 1 + dfs(i+1,j) + dfs(i,j+1);
    }
    public int count(int i,int j){
        int sum = 0;
        while(i != 0){
            sum += i%10;
            i /= 10;
        }
        while(j != 0){
            sum += j%10;
            j /= 10;
        }
        return sum;
    }
    //BFS
    public int movingCount(int m, int n, int k) {
        boolean[][] visited = new boolean[m][n];
        Queue<int[]> queue = new LinkedList<>();
        queue.offer(new int[]{0,0});//存放数组，第一位是i，第两位是j
        int ans = 0;
        while(queue.size() >0){
            int[] temp = queue.poll();
            int i = temp[0],j = temp[1];
            if(i>=m || j>=n|| visited[i][j] || count(i,j)>k) continue;
            visited[i][j] = true;
            ans++;
            queue.offer(new int[]{i+1,j});
            queue.offer(new int[]{i,j+1});
        }
        return ans;
    }
    public int count(int i,int j){
        int sum = 0;
        while(i != 0){
            sum += i%10;
            i /= 10;
        }
        while(j != 0){
            sum += j%10;
            j /= 10;
        }
        return sum;
    }

}
```

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

```java
/*
当绳子长度n>3时，最后一段绳子长度只有2，3两种情况(证明参考高赞精选贴)，因此：
dp[i] = max { 2 * dp[i-2], 3 * dp[i-3] }
*/
class Solution {
    public int cuttingRope(int n) {
        if(n<=3) return n-1;
        int[] dp = new int[n+1];
        dp[2] = 2; dp[3] = 3;
        for(int i = 4; i <= n; i++){
            dp[i] = Math.max(2*dp[i-2], 3*dp[i-3]);
        }
        return dp[n];
    }
}
```



#### 5. **数组中任意数累加得到目标值**

```java
//递归
public static isSum(int arr[],int i,int sum,int aim){
     if(i==arr.length){
         return sum==aim;
     }
     return isSum(arr,i+1,sum,aim)||isSum(arr,i+1,sum+arr[i],aim);
}
//dp
public static boolean isSum2(int arr[],int aim){
	     if(arr==null ||arr.length==0){
	        return false;
	     }
	     boolean [][]dp=new boolean[arr.length+1][aim+1];
	     //填最后一行
	     for(int i = arr.length, sum = 0; sum <= aim; sum++){
	            if(sum == aim){
	                dp[i][sum] = true;    // 目标值处设置为 true
	            }else{
	                dp[i][sum] = false;
	            }
	        }
	    for(int i=arr.length-1;i>=0;i--){
	        for(int j=aim-1;j>=0;j--){
	        	//先填最后一列
	            dp[i][j]=dp[i+1][j];	 
	            //再往上填
	            if(j+arr[i]<=aim){
	                 dp[i][j]=(dp[i][j]||dp[i+1][j+arr[i]]);
	            }
	            
	        }
	    }
	    //因为这是从下往上填dp表，所以最后得到的是dp[0][0]
	   return dp[0][0];
	}
```



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

#### 7. 最长公共子序列

```java
/*
状态表示：dp[i][j] text1[0~i] 与 text2[0~j]的最长公共子序列的长度; 属性：Max;
状态计算：找最后一个不同点。text1的第i位字符和text2的第j位字符是否在这个最长公共子序列中
分别用00, 01, 10, 11简要表示四种状态
00: 不包含text1[i]与text2[j]，所以dp[i][j] = dp[i - 1][j - 1]
11: 包含text1[i]与text2[j]，所以dp[i][j] = dp[i - 1][j - 1] + 1;这种情况有一个限制就是text1[i]==text2[j]。
01: 不包含text1[i], 包含text2[j]。可能想当然的认为dp[i][j] = dp[i - 1][j];
但是按照定义：dp[i - 1][j] text1[0~i - 1] 与 text2[0~j]的最长公共子序列的长度。
这个最长公众子序列可能包含text2[j]，也可能不包含text2[j]。也就是说dp[i - 1][j]表示的情况多于01表示的情况。
一般情况下，应该是不重复不遗漏。但是在计算最大值是只需要满足不遗漏，因为重复对于求最大值来说无影响。
综上所述，01情况可以用dp[i - 1][j]来表示。
10: 同理，该情况可以用dp[i][j - 1]。
最后就是求这几种情况的最大值。
Tips:可以发现dp[i - 1][j - 1]肯定是dp[i][j - 1]或者dp[i - 1][j]的子集，因此又可以去掉。
初始化：求的是最长的公众子序列，就初始化为0吧
状态转移中有i - 1， 所以显然应该初始化一下与首字母相关的
dp[0][0] = text1[0] == text2[0] ? 1 : 0;
dp[0][1 ~ n - 1] 就是说一定得包含text1[0]，不包含就只能是0了。
dp[1 ~ m - 1][0] 同理
*/
// java
class Solution {
    public int longestCommonSubsequence(String text1, String text2) {
        int m = text1.length(), n = text2.length();
        int[][] dp = new int[m + 1][n + 1];

        for (int i = 0; i < m; i++) {
            for (int j = 0; j < n; j++) {
                // 获取两个串字符
                char c1 = text1.charAt(i), c2 = text2.charAt(j);
                if (c1 == c2) {
                    // 去找它们前面各退一格的值加1即可
                    dp[i + 1][j + 1] = dp[i][j] + 1;
                } else {
                    //要么是text1往前退一格，要么是text2往前退一格，两个的最大值
                    dp[i + 1][j + 1] = Math.max(dp[i + 1][j], dp[i][j + 1]);
                }
            }
        }
        return dp[m][n];
    }
}
```

#### 8.连续子数组的最大和

```java
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



### 14. 贪心算法

#### 1. 按最低字典序拼接字符串

```java
贪心策略:两个字符串排序时先拼接再比较
str1 + str2 <= str2 + str1 ？ str1 : str2
import java.util.Arrays;
import java.util.Comparator;

/**
 * @description: 字符串字典序拼接
 * 给你一个字符串数组，目的是把所有的字符串拼起来，不能遗漏，这样会得到很多
 * 拼接的结果，请你找到拼接的字符串，字典序最小的结果。
 * 如："ab", "cd", "ef"
 * 拼接结果
 * abcdef
 * abefcd
 *
 * cdabef
 * cdefab
 *
 * efabcd
 * efcdab
 *
 * 这六种结果，其中，字面值最小的是abcdef。
 *
 * 理解一个概念：字典序
 * 长度一样，当做26进制的数，比较大小，小在前
 * 长度不等，补全，补位为字面值最小的数 如 "abc" 和 "b" 比较，补位 "b00"。a < b
 * 所以"abc"在前，"b"在后
 *
 *
 *
 * 思路：进行字典排序
 * @version: 1.0
 */
public class Code_37_TX_LowestLexicography {

    // 贪心策略:两个字符串排序时先拼接再比较
    // str1 + str2 <= str2 + str1 ？ str1 : str2

    public static class MyComparator implements Comparator<String>{

        @Override
        public int compare(String strA, String strB) {
            return (strA + strB).compareTo((strB + strA));
        }
    }

    public static String lowestLexicography(String[] str){
        if (str == null || str.length == 0)
            return "";
        Arrays.sort(str, new MyComparator());
        String res = "";
        for (int i = 0; i < str.length; i++) {
            res += str[i];
        }
        return res;
    }

    public static void main(String[] args) {
        String[] strs1 = { "abc", "bc", "ac", "aa", "abb" };
        System.out.println(lowestLexicography(strs1));

        String[] strs2 = { "ba", "b" };
        System.out.println(lowestLexicography(strs2));
    }
}
```

#### 2. 切分金条总代价最小

```java
import java.util.Comparator;
import java.util.PriorityQueue;

/**
 * @description: 黄金分割
 * 一块金条切成两半，是需要花费和长度数值一样的铜板的。
 * 比如 长度为20的 金条，不管切成长度多大的两半，都要花费20个铜 板。
 * 一群人想整分整块金 条，怎么分最省铜板？
 * 例如,给定数组{10,20,30}，代表一共三个人，整块金条长度为 10+20+30=60. 金条要分成10,20,30三个部分。
 * 如果， 先把长 度60的金条分成10和50，花费60 再把长度50的金条分成20和30， 花费50 一共花费110铜板。
 * 但是如果， 先把长度60的金条分成30和30，花费60 再把长度30 金条分成10和20，花费30 一共花费90铜板。
 *
 * 输入一个数组，返回分割的最小代价。
 * @version: 1.0
 */
public class Code_38_TX_LessMoney {

    public static int lessMoney(int[] arr) {
        // 堆，小根堆
        PriorityQueue<Integer> minHeap = new PriorityQueue<>();
        for (int i = 0; i < arr.length; i++) {
            minHeap.add(arr[i]); // 如小根堆
        }

        int res = 0;
        int cur = 0;
        while (minHeap.size() > 1) {
            cur = minHeap.poll() + minHeap.poll();
            res += cur;
            minHeap.add(cur);
        }
        return res;
    }

    // 小根堆比较器
    public static class MinHeapComparator implements Comparator<Integer> {

        @Override
        public int compare(Integer o1, Integer o2) {
            return o1 - o2; // < 0  o1 < o2  负数
        }
    }

    //大根堆比较器
    public static class MaxHeapComparator implements Comparator<Integer> {

        @Override
        public int compare(Integer o1, Integer o2) {
            return o2 - o1; // <   o2 < o1
        }

    }

    public static void main(String[] args) {
        // solution
        int[] arr = {6, 7, 8, 9};
        System.out.println(lessMoney(arr));

        int[] arrForHeap = {3, 5, 2, 7, 0, 1, 6, 4};


        // 测试大根堆，小根堆，优先级队列（实际就是小根堆）

        // min heap 默认情况下，PriorityQueue 就是小根堆
        PriorityQueue<Integer> minQ1 = new PriorityQueue<>();
        for (int i = 0; i < arrForHeap.length; i++) {
            minQ1.add(arrForHeap[i]);
        }
        while (!minQ1.isEmpty()) {
            System.out.print(minQ1.poll() + " ");
        }
        System.out.println();

        // min heap use Comparator
        System.out.println("min heap use Comparator");
        PriorityQueue<Integer> minQ2 = new PriorityQueue<>(new MinHeapComparator());
        for (int i = 0; i < arrForHeap.length; i++) {
            minQ2.add(arrForHeap[i]);
        }
        while (!minQ2.isEmpty()) {
            System.out.print(minQ2.poll() + " ");
        }
        System.out.println();

        // max heap use Comparator
        System.out.println("max heap use Comparator");
        PriorityQueue<Integer> maxQ = new PriorityQueue<>(new MaxHeapComparator());
        for (int i = 0; i < arrForHeap.length; i++) {
            maxQ.add(arrForHeap[i]);
        }
        while (!maxQ.isEmpty()) {
            System.out.print(maxQ.poll() + " ");
        }
    }
}
```

#### 3. 最多做 K 个项目的最大利润

```java
import java.util.Comparator;
import java.util.PriorityQueue;

public class Code_39_IPO {

    // 构建项目数据结构
    public static class Node{
        public int cost; // 花费
        public int profits; // 利润

        public Node(int cost, int profits){
            this.cost = cost;
            this.profits = profits;
        }
    }

    // 最小花费，小根堆
    public static class MinCostHeapComparator implements Comparator<Node> {

        @Override
        public int compare(Node o1, Node o2) {
            return o1.cost - o2.cost;
        }
    }

    // 最大收益，大根堆
    public static class MaxProfitsHeapComparator implements Comparator<Node> {

        @Override
        public int compare(Node o1, Node o2) {
            return o2.profits - o1.profits;
        }
    }

    /**
     *
     * @param k 表示你不能并行、只能串行的最多做k个项目
     * @param m 表示你初始的资金
     * @param profits profits[i]表示i号项目在扣除花费之后还能挣到的钱(利润)
     * @param costs costs[i]表示i号项目的花
     * @return 你最后获得的最大钱数。
     *
     * @Description 说明：你每做完一个项目，马上获得的收益，可以支持你去做下一个 项目
     */
    public static int findMaximizedCapital(int k, int m, int[] profits, int[] costs) {
        Node[] nodes = new Node[profits.length];
        // 结构化项目数据
        for (int i = 0; i < profits.length; i++) {
            nodes[i] = new Node(costs[i], profits[i]);
        }

        // 使用自定义的排序规则，即自定义的比较器，建立最小花费小根堆，最大收益大根堆
        PriorityQueue<Node> minCostsHeap = new PriorityQueue<>(new MinCostHeapComparator());
        PriorityQueue<Node> maxProfitsHeap = new PriorityQueue<>(new MaxProfitsHeapComparator());

        for (int i = 0; i < nodes.length; i++) {
            minCostsHeap.add(nodes[i]);
        }

        for (int i = 0; i < k; i++) { // 表示最多可以做 k 个项目
            while (!minCostsHeap.isEmpty() && minCostsHeap.peek().cost <= m){ // 小根堆不为空且存在花费小于当前可用的金额
                maxProfitsHeap.add(minCostsHeap.poll());
            }
            if (maxProfitsHeap.isEmpty()){
                return m; // 大根堆为空，表示没有花费小于当前可用金额的项目进入大根堆
            }
            m += maxProfitsHeap.poll().profits;
        }

        return m;
    }
}
```

#### 4. 安排最多的宣讲场次

```java
import java.util.Arrays;
import java.util.Comparator;

/**
 * @description: 会议时间安排
 *
 * 一些项目要占用一个会议室宣讲，会议室不能同时容纳两个项目 的宣讲。
 *
 * 给你每一个项目开始的时间和结束的时间(给你一个数 组，里面 是一个个具体的项目)，
 * 你来安排宣讲的日程，要求会 议室进行 的宣讲的场次最多。
 * 返回这个最多的宣讲场次。
 *
 * 贪心策略：每次选取结束时间最早的
 * @version: 1.0
 */
public class Code_40_TX_BestArrange {

    // 构建会议数据结构
    public static class Program{
        public int startTime;
        public int endTime;

        public Program(int startTime, int endTime) {
            this.startTime = startTime;
            this.endTime = endTime;
        }
    }

    // 会议比较器，按照项目结束时间进行排序，结束时间早放前
    public static class ProgramComparator implements Comparator<Program> {

        @Override
        public int compare(Program o1, Program o2) {
            return o1.endTime - o2.endTime;
        }
    }

    /**
     * @param programs 具体的会议信息
     * @param curTime 当前时间
     * @return 返回这个最多的宣讲场次
     */
    public static int bestArrange(Program[] programs, int curTime){
        if (programs == null || programs.length == 0) return 0;

        Arrays.sort(programs, new ProgramComparator()); // 按照结束时间排序

        int res = 0;
        for (int i = 0; i < programs.length; i++) {
            if (curTime <= programs[i].startTime){ // 可以进行的会议，其他都排除
                res++;
                curTime = programs[i].endTime; // 重置当前时间为会议结束时间
            }
        }
        return res;
    }

    public static void main(String[] args) {
        Program[] programs = new Program[9];
        int[] startTime = {7, 7, 8, 9, 10, 10, 12, 14, 16};
        int[] endTime = {9, 8, 11, 10, 12, 13, 15, 19, 17};

        for (int i = 0; i < startTime.length; i++) {
            programs[i] = new Program(startTime[i], endTime[i]);
        }

        int res = bestArrange(programs, 6);
        System.out.println(res);
    }
}
```



#### 5. 前缀树

```java

/**
 * @description:
 * @version: 1.0
 */
public class Code_36_TrieTree {

    // 前缀树数据项结构
    public static class TrieNode {
        public int pathNum; // 字符串滑过当前节点是次数
        public int endNum; // 以此节点结尾的字符串个数

        //        public HashMap<Character, TrieNode> nexts; // 结构复杂使用map
        public TrieNode[] nexts; // 这里为了方便使用数组，假设涉及数据都是小写的26个字母

        public TrieNode() {
            this.pathNum = 0;
            this.endNum = 0;
            this.nexts = new TrieNode[26];
        }
    }

    // 前缀树
    public static class Trie {
        private TrieNode root; // 前缀树加上数据项

        public Trie() {
            root = new TrieNode();
        }

        public void insert(String word) {
            if (word == null) return;
            TrieNode node = this.root;

            char[] chars = word.toCharArray();
            int index = 0; // 用来标记路线，默认26个字母，每个节点26条路线

            for (int i = 0; i < chars.length; i++) {
                index = chars[i] - 'a';
                if (node.nexts[index] == null) {
                    node.nexts[index] = new TrieNode();
                }

                node = node.nexts[index];
                node.pathNum++;
            }
            node.endNum++;// 结尾节点
        }

        public void delete(String word) {
            if (search(word) != 0) {
                TrieNode node = this.root;
                char[] chars = word.toCharArray();
                int index = 0;

                for (int i = 0; i < chars.length; i++) {
                    index = chars[i] - 'a';
                    if (--node.nexts[index].pathNum == 0) { // 如果节点滑过次数只有一次，直接置为null，后序节点无需再遍历
                        node.nexts[index] = null;
                        return;
                    }
                    node = node.nexts[index];
                }
                node.endNum--;
            }
        }


        // 查找是否存在某个字符串
        public int search(String word) {
            if (word == null) return 0;

            TrieNode node = this.root;
            char[] chars = word.toCharArray();
            int index = 0;
            for (int i = 0; i < chars.length; i++) {
                index = chars[i] - 'a';
                if (node.nexts[index] == null) {
                    return 0;
                }
                node = node.nexts[index];
            }
            return node.endNum;
        }

        public int prefixNumber(String pre) {
            if (pre == null) {
                return 0;
            }
            char[] chs = pre.toCharArray();
            TrieNode node = root;
            int index = 0;
            for (int i = 0; i < chs.length; i++) {
                index = chs[i] - 'a';
                if (node.nexts[index] == null) {
                    return 0;
                }
                node = node.nexts[index];
            }
            return node.pathNum;
        }
    }

    public static void main(String[] args) {
        Trie trie = new Trie();
        System.out.println(trie.search("test"));
        trie.insert("test");
        System.out.println(trie.search("test"));
        trie.delete("test");
        System.out.println(trie.search("test"));
        trie.insert("test");
        trie.insert("test");
        trie.delete("test");
        System.out.println(trie.search("test"));
        trie.delete("test");
        System.out.println(trie.search("test"));
        trie.insert("testa");
        trie.insert("testac");
        trie.insert("testab");
        trie.insert("testad");
        trie.delete("testa");
        System.out.println(trie.search("testa"));
        System.out.println(trie.prefixNumber("test"));

    }
}
https://zhuanlan.zhihu.com/p/76600383
```



### 15. 回溯算法

#### 1. 不同路径(机器人)

````java
//dp
class Solution {
    public int uniquePaths(int m, int n) {
        int[][] dp = new int[m][n];
        for (int i = 0; i < n; i++) dp[0][i] = 1;
        for (int i = 0; i < m; i++) dp[i][0] = 1;
        for (int i = 1; i < m; i++) {
            for (int j = 1; j < n; j++) {
                dp[i][j] = dp[i - 1][j] + dp[i][j - 1];
            }
        }
        return dp[m - 1][n - 1];  
    }
}

//dp优化
class Solution {
    public int uniquePaths(int m, int n) {
        int[] dp = new int[n];
        Arrays.fill(dp, 1);
        for (int i = 1; i < m; i++) {
            for (int j = 1; j < n; j++) {
                dp[j] = dp[j - 1] + dp[j];
            }
        }
        return dp[n - 1];
    }
}

````

#### 2. 不同路径(有障碍物)

```java
 class Solution {
    public int uniquePathsWithObstacles(int[][] obstacleGrid) {
        int n = obstacleGrid.length, m = obstacleGrid[0].length;
        int[] f = new int[m];

        f[0] = obstacleGrid[0][0] == 0 ? 1 : 0;
        for (int i = 0; i < n; ++i) {
            for (int j = 0; j < m; ++j) {
                if (obstacleGrid[i][j] == 1) {
                    f[j] = 0;
                    continue;
                }
                if (j - 1 >= 0 && obstacleGrid[i][j - 1] == 0) {
                    f[j] += f[j - 1];
                }
            }
        }
        
        return f[m - 1];
    }
}
```



### 16. 经典结构

#### 1. 单调栈结构[见上文]

> 2-4 题是滑动窗口结构:

#### 2. 滑动窗口结构的实现

#### 3. 生成窗口最大值数组

#### 4. 求一个数组中最大值减去最小值小于或等于 num 的子数组数量（要求O(N)）

#### 5. 柱状图中最大的矩阵

```java
//暴力，依次遍历柱形的高度，对于每一个高度分别向两边扩散，求出以当前高度为矩形的最大宽度多少。
public class Solution {

    public int largestRectangleArea(int[] heights) {
        int len = heights.length;
        // 特判
        if (len == 0) {
            return 0;
        }

        int res = 0;
        for (int i = 0; i < len; i++) {

            // 找左边最后 1 个大于等于 heights[i] 的下标
            int left = i;
            int curHeight = heights[i];
            while (left > 0 && heights[left - 1] >= curHeight) {
                left--;
            }

            // 找右边最后 1 个大于等于 heights[i] 的索引
            int right = i;
            while (right < len - 1 && heights[right + 1] >= curHeight) {
                right++;
            }

            int width = right - left + 1;
            res = Math.max(res, width * curHeight);
        }
        return res;
    }
}

//单调栈
import java.util.ArrayDeque;
import java.util.Deque;

public class Solution {

    public int largestRectangleArea(int[] heights) {
        int len = heights.length;
        if (len == 0) {
            return 0;
        }
        if (len == 1) {
            return heights[0];
        }

        int res = 0;
        Deque<Integer> stack = new ArrayDeque<>(len);
        for (int i = 0; i < len; i++) {
            // 这个 while 很关键，因为有可能不止一个柱形的最大宽度可以被计算出来
            while (!stack.isEmpty() && heights[i] < heights[stack.peekLast()]) {
                int curHeight = heights[stack.pollLast()];
                while (!stack.isEmpty() && heights[stack.peekLast()] == curHeight) {
                    stack.pollLast();
                }

                int curWidth;
                if (stack.isEmpty()) {
                    curWidth = i;
                } else {
                    curWidth = i - stack.peekLast() - 1;
                }

                // System.out.println("curIndex = " + curIndex + " " + curHeight * curWidth);
                res = Math.max(res, curHeight * curWidth);
            }
            stack.addLast(i);
        }

        while (!stack.isEmpty()) {
            int curHeight = heights[stack.pollLast()];
            while (!stack.isEmpty() && heights[stack.peekLast()] == curHeight) {
                stack.pollLast();
            }
            int curWidth;
            if (stack.isEmpty()) {
                curWidth = len;
            } else {
                curWidth = len - stack.peekLast() - 1;
            }
            res = Math.max(res, curHeight * curWidth);
        }
        return res;
    }
}
https://leetcode-cn.com/problems/largest-rectangle-in-histogram/solution/bao-li-jie-fa-zhan-by-liweiwei1419/
```



### 17.经典算法

#### 1. **蓄水池算法： 解决等概率问题**

#### 2. **Manacher 算法： 解决回文串问题**

#### 3. **KMP 算法： 解决字符串匹配问题：时间复杂度为：O(N + M)，空间复杂度为：O(M)**

#### 4. **BRPRT 算法： 解决第 k 大数问题：选择中位数组的中位数作为 partition 的基准，时间复杂度 O(N)**

#### 5. **单例模式： 懒汉+恶汉+静态内部类+双重校验锁**

```java
//final不允许被继承
public final class SingletonDoubleCheck {
 //实例变量
 private byte[] data = new byte[1024];

 //定义实例，但是不直接初始化
 private static volatile SingletonDoubleCheck instance = null;

 Connection con;
 Socket socket;

 //私有化构造函数，不允许外部NEW
 private SingletonDoubleCheck() {
     this.con = con;//初始化
     this.socket = socket;//初始化

 }

 public static SingletonDoubleCheck getInstance() {
     //当instance为null时，进入同步代码块，同时该判断避免了每次都需要进入同步代码块，可以提高效率
     if (null == instance) {
         //只有一个线程能够获得SingletonDoubleCheck.class关联的monitor
         synchronized (SingletonDoubleCheck.class) {
             //判断如果instance为null则创建
             if (null == instance) {
                 instance = new SingletonDoubleCheck();
             }
         }
     }
     return instance;
 }
}
```

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

```java
package high_frequent;


import java.util.HashMap;

public class LRU {

    class LinkedNode {
        int key;
        int value;
        LinkedNode prev;
        LinkedNode next;
        public LinkedNode(){}
        public LinkedNode(int k, int v) {key = k; value = v;}
    }

    //容量
    private int capacity;
    //当前大小
    private int size;
    //新建hashmap存储LRU
    private HashMap<Integer, LinkedNode> hashmap;
    //头结点,尾结点
    private LinkedNode head, tail;

    //init
    public LRU(int capacity) {
        this.capacity = capacity;
        this.size = 0;
        hashmap = new HashMap<>();
        head = new LinkedNode();
        tail = new LinkedNode();
        head.next = tail;
        tail.prev = head;
    }

    //get LRU
    public int get(int key) {
        if (!hashmap.containsKey(key)) {
            return -1;
        }
        LinkedNode value = hashmap.get(key);
        moveToHead(value);
        return value.value;
    }

    //put LRU
    public void put(int key, int value) {
        LinkedNode newNode = new LinkedNode(key, value);
        if (hashmap.containsKey(key)) {
            hashmap.put(key, newNode);
            moveToHead(newNode);
        } else {
            if (size > capacity) {
                moveLast(newNode);
                putNewNode(newNode);
            } else {
                putNewNode(newNode);
                size++;
            }
        }
    }

    public void moveToHead(LinkedNode value) {
        LinkedNode cur = value;
        cur.next = head.next;
        cur.prev.next = tail;
        tail.prev = cur.prev.next;
        cur.prev = head;
    }

    public void moveLast(LinkedNode value) {

    }

    public void putNewNode(LinkedNode value) {

    }


}

```



#### 2. LFU

#### 3. SkipList

```java
package high_frequent;

import java.util.Random;

class skipList {
    class SkipListNode {
        int val;
        int cnt;  // 当前val出现的次数
        SkipListNode[] levels;  // start from 0
        SkipListNode() {
            levels = new SkipListNode[MAX_LEVEL];
        }
    }

    private double p = 0.5;
    private int MAX_LEVEL = 16;
    private SkipListNode head;  // 头结点
    private int level;  //
    private Random random;

    public skipList() {
        // 保存此level有利于查询（以及其他操作）
        level = 0;  // 当前 skiplist的高度（所有数字 level数最大的）
        head = new SkipListNode();
        random = new Random();
    }
    // 返回target是否存在于跳表中
    public boolean search(int target) {
        SkipListNode curNode = head;
        for (int i = level-1; i >= 0; i--) {
            while (curNode.levels[i] != null && curNode.levels[i].val < target) {
                curNode = curNode.levels[i];
            }
        }
        curNode = curNode.levels[0];
        return (curNode != null && curNode.val == target);
    }
    // 插入一个元素到跳表。
    public void add(int num) {
        SkipListNode curNode = head;
        // 记录每层能访问的最右节点
        SkipListNode[] levelTails = new SkipListNode[MAX_LEVEL];
        for (int i = level-1; i >= 0; i--) {
            while (curNode.levels[i] != null && curNode.levels[i].val < num) {
                curNode = curNode.levels[i];
            }
            levelTails[i] = curNode;
        }
        curNode = curNode.levels[0];
        if (curNode != null && curNode.val == num) {
            // 已存在，cnt 加1
            curNode.cnt++;
        } else {
            // 插入
            int newLevel = randomLevel();
            if (newLevel > level) {
                for (int i = level; i < newLevel; i++) {
                    levelTails[i] = head;
                }
                level = newLevel;
            }
            SkipListNode newNode = new SkipListNode();
            newNode.val = num;
            newNode.cnt = 1;
            for (int i = 0; i < level; i++) {
                newNode.levels[i] = levelTails[i].levels[i];
                levelTails[i].levels[i] = newNode;

            }
        }
    }

    private int randomLevel() {
        int level = 1;  // 注意思考此处为什么是 1 ？
        while (random.nextDouble() < p && level < MAX_LEVEL) {
            level++;
        }
        return level > MAX_LEVEL ? MAX_LEVEL : level;
    }
    // 在跳表中删除一个值，如果 num 不存在，直接返回false. 如果存在多个 num ，删除其中任意一个即可。
    public boolean erase(int num) {
        SkipListNode curNode = head;
        // 记录每层能访问的最右节点
        SkipListNode[] levelTails = new SkipListNode[MAX_LEVEL];

        for (int i = level-1; i >= 0; i--) {
            while (curNode.levels[i] != null && curNode.levels[i].val < num) {
                curNode = curNode.levels[i];
            }
            levelTails[i] = curNode;
        }
        curNode = curNode.levels[0];
        if (curNode != null && curNode.val == num) {
            if (curNode.cnt > 1) {
                curNode.cnt--;
                return true;
            }
            // 存在，删除
            for (int i = 0; i < level; i++) {
                if (levelTails[i].levels[i] != curNode) {
                    break;
                }
                levelTails[i].levels[i] = curNode.levels[i];
            }
            while (level > 0 && head.levels[level-1] == null) {
                level--;
            }
            return true;
        }
        return false;
    }
}

```



#### 4. 快速排序、归并排序、桶排序、冒泡排序

```java
import java.util.Scanner;
public class Main {
    public static void main(String[] args) {
        //Scanner in = new Scanner(System.in);
        //int a = in.nextInt();
        //System.out.println(a);
        int[] nums = {1,3,5,2,3,6,7,8,34,23,13,24,45,34,13,67,3,2,1,5,89};
        quickSort(nums, 0, nums.length - 1);
        for(int num : nums) {
            System.out.println(num);
        }
    }
    
    public static void quickSort(int[] nums, int start, int end) {
        if(start >= end) return;
        int base = nums[start];
        int i = start, j = end;
        while(i < j) {
            while(i < j && nums[j] >= base) j--;
            while(i < j && nums[i] <= base) i++;
            if(i < j) {
                int temp = nums[i];
                nums[i] = nums[j];
                nums[j] = temp;
            }
        }
        nums[start] = nums[i];
        nums[i] = base;
        
        quickSort(nums, start, i-1);
        quickSort(nums, i + 1, end);
    }
}
```

#### 5.循环队列

#### 6.实现一个线程池

```java
通过上面的介绍线程的特点可以推断出，一个完整的线程池应该具有以下功能：

任务队列：用于缓存已经提交的任务。
线程数量管理：线程池应该自动控制线程池里面线程的数量，可以通过以下三个参数来实现：（1）初始线程数init，线程池能容纳最大线程数max，线程的活跃数量：core，三者的关系是int<=core<=max
任务拒绝策略：当线程数量达到上限且任务队列满的时候，需要有对应的拒绝策略来通知提交者线程被拒绝。
线程工厂：用于自定义线程，比如设置线程名称等。
提交队列：用于存放提交的线程，需要有数量限制。
活跃时间：线程自动维护的时间。
通过上面的定义，就可以自己写一个线程池，首先是接口类：

ThreadPool
线程池接口，定义了线程池的一些方法，其中最主要的是execute,shutdown，后面主要实现这两个方法。
public interface ThreadPool {
    //提交任务到线程池
    void execute(Runnable runnable);
    //关闭
    void shutdown();
    //获取线程池初始化时的线程大小
    int getInitSize();
    //获取线程池最大线程输
    int getMaxSize();
    //获取线程池核心线程数量
    int getCoreSize();
    //获取活跃线程数量
    int getActiveCount();
    //获取线程池缓存队列大小
    int getQueueSize();
    //查看线程是否被销毁
    boolean isShutdown();

}
RunnableQueue
//缓存提交到线程池的队列任务
public interface RunnableQueue {
    //新线程进来时,提交任务到缓存队列
    void offer(Runnable runnable);
    //取出线程
    Runnable take();
    //获取队列中线程的数量
    int size();
}
自定义异常
没什么特别，只是继承了Runtime异常，换了一个名字而已
//自定义异常
public class RunnableDenyException extends RuntimeException {
    public RunnableDenyException(String msg){
        super(msg);
    }
}
DenyPolicy
拒绝策略，我定义了三种策略，在代码中有注释可以体现
import Thread.ThreadPool.RunnableDenyException;

@FunctionalInterface
//这个类定义了当缓存队列达到上限的时候，将通过什么方式来通知提交者，实现了默认的三种方法
public interface DenyPolicy {
    void reject(Runnable runnable, ThreadPool threadPool);


    //直接丢弃线程，什么都不做，不通知
    class DiscardDenyPolicy implements DenyPolicy {

        @Override
        public void reject(Runnable runnable, ThreadPool threadPool) {

        }
    }

    //抛出异常通知
    class AbortDenyPolicy implements DenyPolicy {

        @Override
        public void reject(Runnable runnable, ThreadPool threadPool) {
            throw new RunnableDenyException("这个线程:" + runnable + " 将会被丢弃");
        }
    }

    //使线程在提交者所在的线程中运行
    class RunnerDenyPolicy implements DenyPolicy {
        @Override
        public void reject(Runnable runnable, ThreadPool threadPool) {
            if (!threadPool.isShutdown()) {
                runnable.run();
            }
        }
    }
}
ThreadFactory
创建线程的工厂，用于创建线程
@FunctionalInterface
//创建线程的工厂
public interface ThreadFactory {

    Thread creatThread(Runnable runnable);
}
接口定义完成后，就开始写接口的实现类了
缓存队列
使用了linkedList作为队列，进队的offer方法要判断队列是否满，没有满则加入队列并唤醒，出队只需要判断队列是否为空，如果为空就阻塞等待
这里也可以使用自己写的Queue方法，同样具有进队出队功能，最后会把代码放上
import java.util.LinkedList;

public class LinkedRunnableQueue implements RunnableQueue {
    //任务队列的最大容量
    private final int limit;
    //容量最大时，需要使用的拒绝策略
    private final DenyPolicy denyPolicy;
    //存放任务的队列
    private final LinkedList<Runnable> runnableLinkedList;
    private final ThreadPool threadPool;

    public LinkedRunnableQueue(int limit, DenyPolicy denyPolicy, ThreadPool threadPool) {
        this.limit = limit;
        this.denyPolicy = denyPolicy;
        this.threadPool = threadPool;
        runnableLinkedList = new LinkedList<>();
    }

    @Override
    public void offer(Runnable runnable) {
        synchronized (runnableLinkedList) {
            //如果缓存数量超过最大值，则使用拒绝策略
            if (runnableLinkedList.size() >= limit) {
                denyPolicy.reject(runnable, threadPool);
            } else {
                //成功加入list的末尾，并唤醒阻塞中的线程
                runnableLinkedList.addLast(runnable);
                runnableLinkedList.notifyAll();
            }
        }
    }

    @Override
    public Runnable take() {
        synchronized (runnableLinkedList) {
            //如果缓存队列为空，则挂起，等待新的任务进来唤醒
            while (runnableLinkedList.isEmpty()) {
                try {
                    runnableLinkedList.wait();
                } catch (InterruptedException e) {
                    e.printStackTrace();
                }
            }
        }
        return runnableLinkedList.removeFirst();
    }

    @Override
    public int size() {
        synchronized (runnableLinkedList){
            //返回list中的个数
            return runnableLinkedList.size();
        }
    }
}
继承Runnable
//实现Runnable,用于线程池内部，该类会用到RunnableQueue，会不断的从队列中拿出线程并运行
public class InternalTask implements Runnable {

    private final RunnableQueue runnableQueue;
    private volatile boolean running = true;

    public InternalTask(RunnableQueue runnableQueue) {
        this.runnableQueue = runnableQueue;
    }

    @Override
    public void run() {
        //如果当前线程在运行中切没有被中断，则不断从缓存队列中拿出线程运行
        while (running && !Thread.currentThread().isInterrupted()){
            try {
                Runnable task = runnableQueue.take();
                task.run();
            } catch (Exception e) {
                running = false;
                break;
            }
        }
    }
    //停止当前任务，会在shutdown中使用
    public void stop(){
        this.running = false;
    }
}
最后是线程池的具体实现，有注释
package Thread.ThreadPool;

import java.util.ArrayDeque;
import java.util.Queue;
import java.util.concurrent.TimeUnit;
import java.util.concurrent.atomic.AtomicInteger;
import java.util.stream.IntStream;


public class BasicThreadPool extends Thread implements ThreadPool {
    //初始化线程池的数量
    private final int initSize;
    //线程池最大线程数
    private final int maxSize;
    //线程池核心线程数
    private final int coreSize;
    //当前活跃线程的数量
    private int activeCount;
    //创建线程的工厂
    private final ThreadFactory threadFactory;
    //任务队列
    private final RunnableQueue runnableQueue;
    //线程是否被摧毁
    private volatile boolean isShutdown = false;
    //工作队列
    private final Queue<ThreadTask> internalTasks = new ArrayDeque<>();
    //拒绝策略
    private final static DenyPolicy DEFAULT_DENY_POLICY = new DenyPolicy.DiscardDenyPolicy();
    //看下面，自定义线程工厂
    private final static ThreadFactory DEFAULT_THREAD_FACTORY =
            new DefaultThreadFactory();
    private final long keepAliveTime;
    private final TimeUnit timeUnit;


    //构造默认线程池时需要传入的参数：初始线程池的数量，最大线程的数量，核心线程数量，任务队列的最大数
    public BasicThreadPool(int initSize, int maxSize, int coreSize,
                           int queueSize) {
        this(initSize, maxSize, coreSize, DEFAULT_THREAD_FACTORY,
                queueSize, DEFAULT_DENY_POLICY, 2,
                TimeUnit.SECONDS);
    }

    public BasicThreadPool(int initSize, int maxSize, int coreSize, ThreadFactory threadFactory, int queueSize,
                           DenyPolicy denyPolicy, long keepAliveTime, TimeUnit timeUnit) {
        this.initSize = initSize;
        this.maxSize = maxSize;
        this.coreSize = coreSize;
        this.threadFactory = threadFactory;
        this.runnableQueue = new LinkedRunnableQueue(queueSize, denyPolicy, this);
        this.keepAliveTime = keepAliveTime;
        this.timeUnit = timeUnit;
        this.init();
    }

    //初始化线程池并创建initSize个线程
    private void init() {
        //继承了Thread类，初始化时先启动自己
        start();
        IntStream.range(0, initSize).forEach(i -> newThread());
    }

    //创建新的任务线程并启动
    private void newThread() {
        InternalTask internalTask = new InternalTask(runnableQueue);
        Thread thread = this.threadFactory.creatThread(internalTask);
        ThreadTask threadTask = new ThreadTask(thread, internalTask);
        internalTasks.offer(threadTask);
        this.activeCount++;
        thread.start();
    }

    private void removeThread() {
        ThreadTask threadTask = internalTasks.remove();
        threadTask.internalTask.stop();
        this.activeCount--;
    }

    @Override
    public void execute(Runnable runnable) {
        if (this.isShutdown) {
            throw new IllegalStateException("这个线程池已经被销毁了");
        }
        this.runnableQueue.offer(runnable);
    }

    @Override
    public void run() {
       //自动维护线程池
        while (!isShutdown && !isInterrupted()) {
            try {
                timeUnit.sleep(keepAliveTime);
            } catch (InterruptedException e) {
                e.printStackTrace();
                isShutdown = true;
                break;
            }
            synchronized (this) {
                if (isShutdown) {
                    break;
                }
                //当任务队列大于0，活跃线程小于核心线程的时候，扩容线程
                if (runnableQueue.size() > 0 && activeCount < coreSize) {
                    IntStream.range(initSize, coreSize).forEach(i -> newThread());
                    continue;
                }
                if (runnableQueue.size() > 0 && activeCount < maxSize) {
                    IntStream.range(coreSize, maxSize).forEach(i -> newThread());
                }
                if (runnableQueue.size() == 0 && activeCount > coreSize) {
                    IntStream.range(coreSize, activeCount).forEach(i -> removeThread());
                }

            }
        }
    }

    @Override
    public void shutdown() {

    }
    //这一段方法不是特别重要，就有读者自己写
    @Override
    public int getInitSize() {
        return 0;
    }

    @Override
    public int getMaxSize() {
        return 0;
    }

    @Override
    public int getCoreSize() {
        return 0;
    }

    @Override
    public int getActiveCount() {
        return 0;
    }

    @Override
    public int getQueueSize() {
        return 0;
    }

    @Override
    public boolean isShutdown() {
        return this.isShutdown;
    }
    //把线程和internalTask一个组合
    private static class ThreadTask {
        public ThreadTask(Thread thread, InternalTask internalTask) {
            this.thread = thread;
            this.internalTask = internalTask;
        }

        Thread thread;
        InternalTask internalTask;
    }

    private static class DefaultThreadFactory implements ThreadFactory {
        private static final AtomicInteger GROUP_COUNTER = new AtomicInteger(1);
        private static final ThreadGroup group = new ThreadGroup("我的线程-" +
                GROUP_COUNTER.getAndDecrement());
        private static final AtomicInteger COUNTER = new AtomicInteger(0);

        @Override
        public Thread creatThread(Runnable runnable) {
            return new Thread(group, runnable, "线程池-" + COUNTER.getAndDecrement());
        }
    }
}
```

