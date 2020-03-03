**题目描述**
定义栈的数据结构，请在该类型中实现一个能够得到栈中所含最小元素的min函数（时间复杂度应为O（1））。
注意：保证测试中不会当栈为空的时候，对栈调用pop()或者min()或者top()方法。

**题解**

利用另一个栈结构 
在push的时候就判断栈顶元素与当前元素的大小

```
import java.util.Stack;

public class Solution {
    Stack<Integer> stackTotal = new Stack<Integer>();
    Stack<Integer> stackMin = new Stack<Integer>();
 
    public void push(int node) {
        stackTotal.push(node);
        if(stackMin.empty()){
            stackMin.push(node);
        }else{
            if(node <= stackMin.peek()){
                stackMin.push(node);
            }else{
                stackMin.push(stackMin.peek());
            }
        }
    }
 
    public void pop() {
        stackTotal.pop();
        stackMin.pop();
    }
 
    public int top() {
        return stackTotal.peek();
    }
 
    public int min() {
        return stackMin.peek();
    }
}
```

还有降低空间为O(1)的，后续再做的时候可以想一下