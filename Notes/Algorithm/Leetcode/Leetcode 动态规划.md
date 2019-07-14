# Leetcode 动态规划

## 53 Maximum Subarray 

给定整数数组，找到具有最大和的子数组(至少包含一个数字)并返回其和。 

```java
class Solution {
    public int maxSubArray(int[] nums) {
        int max=Integer.MIN_VALUE;
        int curr=Integer.MIN_VALUE;
        for(int i=0;i<nums.length;i++){
            if(curr<=0){
                curr=nums[i];
            }else{
                curr+=nums[i];
            }
            max=Math.max(curr,max);
        }
        return max;
    }
}
```

## 62 Unique Paths

mxn 的矩阵，从（0,0）走到（m,n）有多少种走法  每次只能向右或向下走

```java
class Solution {
    public int uniquePaths(int m, int n) {
        int[] record=new int[n]; 
        for(int i=0;i<m;i++)
            for(int j=0;j<n;j++){
                if(i==0&&j==0){record[0]=1;continue;}
                if(i==0) record[j]=record[j-1];
                else if(j==0)continue;
                else 
                    record[j]+=record[j-1];
            }
        return record[n-1];
    }
}
```

## 62 Unique Paths II 

mxn 的矩阵，从（0,0）走到（m,n）有多少种走法  每次只能向右或向下走

矩阵中每个坐标有两种状态，1表示障碍路径不能经过这个点，0表示没有障碍

```java
class Solution {
    public int uniquePathsWithObstacles(int[][] obstacleGrid) {     
        int m=obstacleGrid.length;
        int n=obstacleGrid[0].length;  
        int[] dp =new int[n];     
        for(int i=0;i<m;i++){
            for(int j=0;j<n;j++){
                if(obstacleGrid[i][j]==1)dp[j]=0;
                else{
                    if(i==0&&j==0)dp[j]=1;
                    else{
                        if(j==0)continue;
                        else dp[j]+=dp[j-1];
                    }
                }
            }    
        }
        return dp[n-1];
    }
}
```

## 72. Edit Distance 

给定两个字符串word1,word2,计算需要通过多少步操作可以将word1转化为Word2；

操作可以包括：1.插入字符  2. 删除字符  3. 替代字符

这是**最长公共子序列**的变体问题

```java
class Solution {
    public int minDistance(String word1, String word2) {
        
        int m=word1.length();
        int n=word2.length();
        int[][] dp=new int[m+1][n+1];
        for(int i=0;i<=n;i++)
            dp[0][i]=i;                   //初始化很重要
        for(int j=0;j<=m;j++)
            dp[j][0]=j;
        for(int i=1;i<=m;i++)
            for(int j=1;j<=n;j++){
                if(word1.charAt(i-1)==word2.charAt(j-1)){
                    dp[i][j]=dp[i-1][j-1];
                }else{
                    dp[i][j]=Math.min(dp[i-1][j-1],  //如果对应位字符不相等，那么分为三种情况
                                     Math.min(dp[i-1][j],dp[i][j-1]))+1;
                }
            }
        return dp[m][n];
    }
}
```

## 91. Decode Ways

数字序列转化为字符串，'A' -> 1'B' -> 2...'Z' -> 26，计算有多少种转化方法；

```java
class Solution {
    public int numDecodings(String s) {
        int[] dp=new int[s.length()];
        
        for(int i=s.length()-1;i>=0;i--){
            if(i==s.length()-1){if(s.charAt(i)!='0')dp[i]=1;continue;}
            if(s.charAt(i)!='0')dp[i]=dp[i+1];
            if(s.charAt(i)>'0'&&((s.charAt(i)-'0')*10+(s.charAt(i+1))-'0')<27)
                dp[i]+=(i!=s.length()-2?dp[i+2]:1);
            
        }
        return dp[0];
    }
}
```

## 96. Unique Binary Search Trees

给定n  那么1....n这n个整数可以构造出多少个不同的二叉树

```java
class Solution {
    public int numTrees(int n) {
        int[] dp=new int[n+1];
        dp[0]=1;
        dp[1]=1;
        
        for(int i=2;i<=n;i++)
            for(int j=0;j<=i-1;j++){
                dp[i]+=dp[j]*dp[i-1-j];
            }
        return dp[n];
        
    }
}
```

## 95. Unique Binary Search Trees 2 

给定n  那么1....n这n个整数可以构造出多少个不同的二叉树  返回构造的二叉树集合

**递归方法**

```java
public class Solution {
    public List<TreeNode> generateTrees(int n) {
        
        return genTrees(1,n);
    }
        
    public List<TreeNode> genTrees (int start, int end)
    {
        List<TreeNode> list = new ArrayList<TreeNode>();
        if(start>end)
        {
            list.add(null);
            return list;
        }      
        if(start == end){
            list.add(new TreeNode(start));
            return list;
        }  
        List<TreeNode> left,right;
        for(int i=start;i<=end;i++)
        { 
            left = genTrees(start, i-1);
            right = genTrees(i+1,end);
            
            for(TreeNode lnode: left)
            {
                for(TreeNode rnode: right)
                {
                    TreeNode root = new TreeNode(i);
                    root.left = lnode;
                    root.right = rnode;
                    list.add(root);
                }
            }       
        }
        return list;
    }
}
```

**DP动态规划**

```java
public static List<TreeNode> generateTrees(int n) {
    List<TreeNode>[] result = new List[n + 1];
    result[0] = new ArrayList<TreeNode>();
    if (n == 0) {
        return result[0];
    }
    result[0].add(null);
    for (int len = 1; len <= n; len++) {
        result[len] = new ArrayList<TreeNode>();
        for (int j = 0; j < len; j++) {
            for (TreeNode nodeL : result[j]) {
                for (TreeNode nodeR : result[len - j - 1]) {
                    TreeNode node = new TreeNode(j + 1);
                    node.left = nodeL;
                    node.right = clone(nodeR, j + 1);   //改变对应节点的值  加上一个offset
                    result[len].add(node);
                }
            }
        }
    }
    return result[n];
}
private static TreeNode clone(TreeNode n, int offset) {
    if (n == null) {
        return null;
    }
    TreeNode node = new TreeNode(n.val + offset);
    node.left = clone(n.left, offset);
    node.right = clone(n.right, offset);
    return node;
}
```

## 120. Triangle

给定一个三角形，从上到下求最小路径和。每一步都可以移动到下面一行的相邻数字 

a\[i\][j] 可以移动到a\[i+1\][j]和a\[i+1\][j+1]   不可以移动到 a\[i+1\][j-1]

```
[
     [2],
    [3,4],
   [6,5,7],
  [4,1,8,3]
]                       最小的路径和是： 2+3+5+1=11
```

```java
class Solution {
    public int minimumTotal(List<List<Integer>> triangle) { 
        int m=triangle.size();
        int n=triangle.size();
        int[][]  dp=new int[m][n];     
        for(int i=0;i<m;i++)
            for(int j=0;j<n;j++)
                dp[i][j]=Integer.MAX_VALUE;  
        dp[0][0]=triangle.get(0).get(0);
        for(int i=1;i<m;i++)
            for(int j=0;j<=i;j++){
                if(j==0)
                    dp[i][j]=dp[i-1][j]+triangle.get(i).get(j);
                else
                    dp[i][j]=Math.min(dp[i-1][j],dp[i-1][j-1])+triangle.get(i).get(j);
            }
        int min=Integer.MAX_VALUE;
        for(int i=0;i<n;i++ )
            min=Math.min(min,dp[m-1][i]);
        return min;
    }
}
```

如果只用O(N)的空间复杂度，从下面往上面搜索

```java
class Solution {
    public int minimumTotal(List<List<Integer>> triangle) { 
        int m=triangle.size();
        int[] dp=new int[m+1];
        
        for(int i=m-1;i>=0;i--)
            for(int j=0;j<=i;j++){
                dp[j]=Math.min(dp[j],dp[j+1])+triangle.get(i).get(j);
            }
        return dp[0];
    }
}
```

