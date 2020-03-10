**题目描述**
输入一个递增排序的数组和一个数字S，在数组中查找两个数，使得他们的和正好是S，如果有多对数字的和等于S，输出两个数的乘积最小的。

**题解**
双指针

```

public ArrayList<Integer> FindNumbersWithSum(int [] array,int sum) {
       ArrayList<Integer> res= new ArrayList<>();
       int i = 0,j=array.length-1;
 
       int ij=Integer.MAX_VALUE;
       while(i<j){
           int sumV = array[i]+array[j];
           if(sumV>sum){
              j--;
           }else if(sumV<sum){
              i++;
           }else{
               if(ij>i*j){
                   res.clear();
                   res.add(array[i]);
                   res.add(array[j]);
                   ij=i*j;
               }
               i++;
           }
       }
       return res;
   }
```