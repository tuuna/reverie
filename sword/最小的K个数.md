**题目描述**
输入n个整数，找出其中最小的K个数。例如输入4,5,1,6,2,7,3,8这8个数字，则最小的4个数字是1,2,3,4,。

**题解**

很显然排个序取前k个即可

```
import java.util.*;
public class Solution {
    public ArrayList<Integer> GetLeastNumbers_Solution(int [] input, int k) {
        ArrayList<Integer> list = new ArrayList<>();
        
        if (input == null || k > input.length || input.length == 0) {
            return list;
        }
        
        Arrays.sort(input);
        
        for (int i = 0; i < k; i++) {
            list.add(input[i]);
        }
        
        return list;
    }
}
```

看了下题解，发现其实这种很适合堆排序，时间复杂度仅为nlogk，适合处理海量数据

转一个题解

思路二：利用堆排序，O(N logK)，适合处理海量数据
(1) 遍历输入数组，将前k个数插入到推中；
(2) 继续从输入数组中读入元素做为待插入整数，并将它与堆中最大值比较：如果待插入的值比当前已有的最大值小，则用这个数替换当前已有的最大值；如果待插入的值比当前已有的最大值还大，则抛弃这个数，继续读下一个数。
这样动态维护堆中这k个数，以保证它只储存输入数组中的前k个最小的数，最后输出堆即可。

```
public class Solution {
    public ArrayList<Integer> GetLeastNumbers_Solution(int[] input, int k) {
        ArrayList<Integer> res = new ArrayList<>();
        if (input == null || k <= 0 || k > input.length) {
            return res;
        }
        Queue<Integer> queue = new PriorityQueue<>(k, Collections.reverseOrder());
 
        for (int i = 0; i < input.length; i++) {
 
            if (queue.size() < k) {
                queue.add(input[i]);
            } else {
                if (input[i] < queue.peek()) {
                    queue.remove();
                    queue.add(input[i]);
                }
            }
        }
        while (!queue.isEmpty()) {
            res.add(queue.remove());
        }
        return res;
    }
}
```

