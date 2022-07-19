## 越界问题

* #### 两个数相乘，小数点后位数没有限制，请写一个高精度算法。

  输入 string a, string b； 计算string c=ab; 返回 c;
  1，纪录小数点在a,b中的位置l1,l2，则需要小数点后移动位置数为l=length(a)+length(b)-l1-l2-2;
  2, 去掉a,b中的小数点，（a,b小数点后移，使a,b变为整数）
  3, 计算c=ab; （同整数的大数相乘算法）
  4，输出c,（注意在输出倒数第l个数时，输出一个小数点。若是输出的数少于l个，就补0）

-----

* #### 整数反转 ：给出一个 32 位的有符号整数，你需要将这个整数中每位上的数字进行反转。（leet code 7)

  边界条件的考虑而已，记住就好。对接近最大正数和最小负数单独考虑，其他随意。

  ```cpp
  class Solution 
  {
  public:
      int reverse(int x) 
      {
          int rev = 0;
          while (x != 0) 
          {
              int pop = x % 10;
              x /= 10;
              if (rev > INT_MAX/10 || (rev == INT_MAX / 10 && pop > 7)) return 0;
              if (rev < INT_MIN/10 || (rev == INT_MIN / 10 && pop < -8)) return 0;
              rev = rev * 10 + pop;
          }
          return rev;
      }
  };
  ```

-----

* #### 两数相除：给定两个整数，被除数 dividend 和除数 divisor。将两数相除，要求不使用乘法、除法和 mod 运算符

  既然不能用乘除法和mod，那使用减法是理所当然的，唯一需要考虑的是边界溢出情况。**但是这种做法会遇到超时问题，因此需要思考更优秀的做法：每次让除数翻倍，多的部分递归去继续从原始除数处理**

  1. 避免溢出和对于被除数和除数一些特殊判定以加快运算速度和避免出错
  2. 采用被除数减除数来做到除的效果
  3. 采用除数自增翻倍的思想加快减法的速度
  4. 考虑到符号和可能的溢出，这里将除数和被除数转换为负数进行运算，同时传递运算结果的符号

  ```c
  class Solution {
  public:
  	int subDivide(int dividend, int divisor, bool minus)
  	{
  		int i = 0, times = 1;
  		int originDivisor = divisor;
  		while (dividend <= divisor && dividend <= 0)
  		{
  			dividend -= divisor;
   			i += times;           
              if (dividend >= divisor)
              {
                  break;
              }
  			divisor += divisor;
  			times += times;
  		}
  
  		if (dividend > originDivisor)
  		{
  			if (minus)
  				return -i;
  			else
  				return i;
  		}
  		else
  		{
  			if (minus)
  				return -i + subDivide(dividend, originDivisor, minus);
  			else
  				return i + subDivide(dividend, originDivisor, minus);
  		}
  	}
  
  	int divide(int dividend, int divisor) {
  		int i = 0, times = 1, left = 0;
  		bool minus = false;
  		
  		if (dividend == INT_MIN)
  		{
  			if (divisor == -1)
  				return INT_MAX;
  			else if (divisor == 1)
  				return INT_MIN;
  			else if (divisor == INT_MIN)
  				return 1;
  		}
  		else if (divisor == INT_MIN)
  			return 0;
          else if (divisor == 1)
              return dividend;
          else if (divisor == -1)
              return -dividend;
  
  		if (dividend < 0 && divisor > 0)
  		{
  			minus = true;
  			divisor = -divisor;
  		}
  		else if (dividend > 0 && divisor < 0)
  		{
  			minus = true;
  			dividend = -dividend;
  		}
          else if (dividend > 0 && divisor > 0)
          {
   			divisor = -divisor;
  			dividend = -dividend;                        
          }
  
  		return subDivide(dividend, divisor, minus);
  	}
  };
  ```

  

