```
import java.util.Scanner;
import java.util.Stack;
/**

- 任意输入两个字符串，求出这两个字符串的公共子序列
- */
public class 最长公共子序列 {
public static void main(String[] args) {
	Scanner sc = new Scanner(System.in);
	String x = sc.next();
	String y = sc.next();
	getLCS(x,y);
}
  public static void getLCS(String x, String y){
	
      char[] s1 = x.toCharArray();
      char[] s2 = y.toCharArray();
      int[][] array = new int[x.length()+1][y.length()+1];//此处的棋盘长度要比字符串长度多加1，需要多存储一行0和一列0
      		
      for(int j = 0; j < array[0].length; j++){//第0行第j列全部赋值为0
      	array[0][j] = 0;
      }
      for(int i = 0; i < array.length; i++){//第i行，第0列全部为0
      	array[i][0] = 0;
      }
      
      for(int m = 1; m < array.length; m++){//利用动态规划将数组赋满值
      	for(int n = 1; n < array[m].length; n++){
      		if(s1[m - 1] == s2[n - 1]){
      			array[m][n] = array[m-1][n-1] + 1;//动态规划公式一
      		}else{
      			array[m][n] = max(array[m -1][n], array[m][n -1]);//动态规划公式二
      		}
      	}
      }
  //		for(int m = 0; m < array.length; m++){//将数组赋满值,这样可以从右下角开始遍历找出最大公共子序列
//			for(int n = 0; n < array[m].length; n++){
//				System.out.print(arraym);
//			}
//			System.out.println();
//		}
	Stack stack = new Stack();
	int i = x.length() - 1;
	int j = y.length() - 1;
	
      while((i >= 0) && (j >= 0)){
      	if(s1[i] == s2[j]){//字符串从后开始遍历，如若相等，则存入栈中
      		stack.push(s1[i]);
      		i--;
      		j--;
      	}else{
      		if(array[i+1][j] > array[i][j+1]){//如果字符串的字符不同，则在数组中找相同的字符，注意：数组的行列要比字符串中字符的个数大1，因此i和j要各加1
      			j--;
      		}else{
      			i--;
      		}
      	}
      }
      
      while(!stack.isEmpty()){//打印输出栈正好是正向输出最大的公共子序列
      	System.out.print(stack.pop());
      }			
  }
  public static int max(int a, int b){//比较(a,b)，输出大的值
	return (a > b)? a : b;
}
}

```

 一个序列S任意删除若干个字符得到新序列T，则T称为S的子序列。若两个序列X和Y的公共子序列中，长度最长的那个字序列称为X和Y的最长公共子序列（LCS）。

        Xi表示字符串的前i个字符，Yj表示字符串的前j个字符，它们的公共子序列记为：LCS(X,Y)，即Z=<z1,z2,…,zK>。利用动态规划来处理该问题，此时分为两种情况：
    
        1）Xm == Yn（最后一个字符相同），则Xm与Yn的最长公共子序列Zk的最后一个字符必定相同，即：Xm == Yn == Zk。此时的表达式为：
    
                LCS(Xm, Yn) = LCS(Xm-1, Yn-1) +Xm             (**公式一**)
    
        2）Xm != Yn（最后一个字符不相同），则Xm与Yn的最长公共子序列Zk的最后一个字符就存在两种肯能，即：Xm == Zk 或者 Yn == Zk。此时的表达式存在两种情况，如下：
    
                LCS(Xm, Yn) = LCS(Xm-1, Yn) 或者 LCS(Xm, Yn) = LCS(Xm, Yn-1)。则最终的表达式为：
    
                LCS(Xm, Yn) = MAX{LCS(Xm-1, Yn), LCS(Xm, Yn-1)}     (**公式二**)
    
        在寻找最长公共子序列的过程中利用数组Array来记录Xi和Yj的最长公共子序列的长度。当i=0或j=0时，Array数组记录为0。当i>0,j>0并且Xi==Yj时，利用公式一，当i>0,j>0,并且Xi！= Yj时，利用公式二。得到如下公式：
    
        Array(i,j) = 0;                                          当i=0或者j=0;
    
        Array(i,j) = Array(i-1,j-1);                        当i>0,j>0&&Xi==Yj;
    
        Array(i,j) = max{Array(i-1,j),Array(I,j-1)}      当i>0,j>0&&Xi！=Yj
