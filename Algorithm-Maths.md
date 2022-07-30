## 数学题

在众多算法题中，存在一个特殊类型：可以用数学公式简单得到答案。

- #### [x 的平方根 ](https://leetcode.cn/problems/sqrtx/)：给你一个非负整数 `x` ，计算并返回 `x` 的 **算术平方根** 。

  本题解法可以采用二分法，也可以用牛顿迭代法

  ```c
  class Solution {
  public:
      int mySqrt(int x) {
          //注：在中间过程计算平方的时候可能出现溢出，所以用long long。
          long long i=0;
          long long j=x/2+1;//对于一个非负数n，它的平方根不会大于（n/2+1）
          while(i<=j)
          {
              long long mid=(i+j)/2;
              long long res=mid*mid;
              if(res==x) return mid;
              else if(res<x) i=mid+1;
              else j=mid-1;
          }
          return j;
      }
  };
  
  class Solution {
  public:
      int mySqrt(int x) {
          if (x == 0) return 0;
          double last=0;
          double res=1;
          while(res!=last)
          {
              last=res;
              res=(res+x/res)/2;
          }
          return int(res);
      }
  };
  ```

---

