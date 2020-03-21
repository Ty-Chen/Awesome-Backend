

数据结构和算法面试攻略
====

数据结构主要包括
1. 数组和字符串
2. 链表
3. 队列
4. 栈
5. 堆
6. 树
7. 哈希表

主要考察包括基本结构、基本用法、以及一些由这些结构衍生出来和其他算法结合的问题

算法主要包括

1. 排序
2. 分治
3. 回溯
4. 动态规划
5. 贪心算法
6. 海量数据问题
7. 越界问题等。

---

数组和字符串
------

数组字符串的问题通常包括子串问题、回文问题、查找元素、匹配问题，**通常其解法为双指针滑动、回溯、动态规划、堆栈**。

### 子串问题

> **核心解法** 子串问题分为单数组求子串以及给两个数组比较求子串。其核心思想均为动态规划：双数组类似于二维动态规划，一定可以降维为一维，而单数组则可以考虑为一维动态规划，降维为双指针滑动。对于更多个数组的比较，一般还是循环迭代求解吧。

---

* #### 求最大子串和

  ​       最大子串和，即求一个数列中和最大的子列，采用**动态规划**可以有最优算法，时间复杂度为O（N）。因为最大连续子序列和只可能是以位置0～n-1中某个位置结尾。
  
  ​       当遍历到第i个元素时，判断在它前面的连续子序列和是否大于0，如果大于0，则以位置i结尾的最大连续子序列和为元素i和前门的连续子序列和相加；否则，则以位置i结尾的最大连续子序列和为元素i。

```cpp
int maxsequence3(int a[], int len)  
{  
    int maxsum, maxhere;  
    maxsum = maxhere = a[0];   //初始化最大和为a【0】  
    for (int i = 1; i < len; i++) 
    {  
        if (maxhere <= 0)  
            maxhere = a[i];  //如果前面位置最大连续子序列和小于等于0，则以当前位置i结尾的最大连续子序列和为a[i]  
        else  
            maxhere += a[i]; //如果前面位置最大连续子序列和大于0，则以当前位置i结尾的最大连续子序列和为它们两者之和  
        if (maxhere > maxsum) {  
            maxsum = maxhere;  //更新最大连续子序列和  
        }  
    }  
    return maxsum;  
}
```

-----

* #### 无重复字符的最长子串:给定一个字符串，请你找出其中不含有重复字符的最长子串的长度。(leetcode 3)

  采取**双指针滑动窗口**求解

  ```cpp
  class Solution {
  public:
      int lengthOfLongestSubstring(string s) {
  
          int start, end, size, maxSubString, len;
          char val; 
  
          start = end = len = maxSubString = 0;
          size = s.size();
  
          while (end < size)
          {
              val = s[end];
              for (int i = start; i < end; i++)
              {
                  if (s[i] == val)
                  {
                      start = i + 1;
                      len = end - start;
                      break;
                  }
              }
  
              end++;
              len++;
              maxSubString = max(maxSubString, len);
          }
  
          return maxSubString;
      }
  };
  ```

-----

* #### 串联所有单词的子串：给定一个字符串 **s** 和一些长度相同的单词 **words。**找出 **s** 中恰好可以由 **words** 中所有单词串联形成的子串的起始位置。注意子串要与 **words** 中的单词完全匹配，中间不能有其他字符，但不需要考虑 **words** 中单词串联的顺序。（leetcode 30）



-----

* #### 最小覆盖子串：给你一个字符串 S、一个字符串 T，请在字符串 S 里面找出：包含 T 所有字母的最小子串。(leetcode 76)



---

* #### 至多包含两个不同字符的最长子串(leetcode 159)



---

* #### 长度最小的子数组：给定一个含有 **n** 个正整数的数组和一个正整数 **s ，**找出该数组中满足其和 **≥ s** 的长度最小的连续子数组**。**如果不存在符合条件的连续子数组，返回 0。(leetcode 209)



---

* #### 滑动窗口最大值：给定一个数组 *nums*，有一个大小为 *k* 的滑动窗口从数组的最左侧移动到数组的最右侧。你只可以看到在滑动窗口内的 *k* 个数字。滑动窗口每次只向右移动一位。返回滑动窗口中的最大值。(leetcode 239)



---

* #### 字符串的排列：给定两个字符串 **s1** 和 **s2**，写一个函数来判断 **s2** 是否包含 **s1** 的排列。(leetcode 567)



---

* #### 最小区间(leetcode 632)



---

* #### 最小窗口子序列(leetcode 727)



---

* #### 盛水最多的容器：给定 n 个非负整数 a1，a2，…，an，每个数代表坐标中的一个点 (i, ai) 。在坐标内画 n 条垂直线，垂直线 i 的两个端点分别为 (i, ai) 和 (i, 0)。找出其中的两条线，使得它们与 x 轴共同构成的容器可以容纳最多的水。（leet code 11)

  形成的区域受限制于较短的那条，同时距离越远则可能的收益越大。因此我们从最左和最右开始检索，采用双指针法，移动较短的那端。

  ```cpp
   int maxArea(vector<int> &height)
      {
          int result = 0;
          int heightSize = int(height.size());
          int leftIndex = 0;
          int rightIndex = heightSize - 1;
  
          while (leftIndex != rightIndex)
          {
              int tmpHeight;
              int tmpWidth = rightIndex - leftIndex;
              
              //短的一侧向中间移动
              if (height[leftIndex] < height[rightIndex])
              {
                  tmpHeight = height[leftIndex];
                  leftIndex++;
              }
              else
              {
                  tmpHeight = height[rightIndex];
                  rightIndex--;
              }
              int tmpResult = tmpWidth * tmpHeight;
              if (tmpResult > result)
              {
                  result = tmpResult;
              }
          }
          return result;
      }
  ```

  

---

* #### 最长公共前缀：编写一个函数来查找字符串数组中的最长公共前缀，如果不存在公共前缀，返回空字符串 “”。（leet code 14）

  最简单的办法就是循环迭代查找，该方法时间复杂度和空间复杂度均很低，而且容易想到。除此之外，还可以使用分治的方法，但是结果并没有更优秀

  ```cpp
  class Solution {
  public:
      string longestCommonPrefix(vector<string>& strs) {
  		int count = 0;
  		int size = 0;
  		char c;
  		bool end = true;
  		string ret;
          
  		if (strs.size() == 0)
  			return ret;
  
  		vector<string>::iterator iter;
  
  		iter = strs.begin();
  		while (iter != strs.end())
  		{
  			if (size < (*iter).size())
  				size = (*iter).size();
  			iter++;
  		}
  
  		while (end)
  		{
  			iter = strs.begin();
  			c = (*iter)[count];
  			while ((iter + 1) != strs.end())
  			{
  				iter++;
  				if (c != (*iter)[count])
  				{
  					end = false;
  					break;
  				}
  			}
  
  			if (end)
  			{
  				ret += c;
  				count++;
  			}	
  
  			if (count >= size)
  				return ret;
  		}
  
  		return ret;
      }
  };
  ```

---

* #### 电话号码组合问题：给定一个仅包含数字 2-9 的字符串，返回所有它能表示的字母组合。给出数字到字母的映射如下（与电话按键相同）。注意 1 不对应任何字母。（leet code 17)

  对每一位可选可不选，因此采取递归回溯法求解。这里既可以采用深度优先也可以采取广度优先。

  ```cpp
  // 深度优先，优先考虑每一位选择到结束
  class Solution
  {
      vector<string> result;
      string digits;
      unordered_map<char, string> store;
  
  public:
      vector<string> letterCombinations(string digits)
      {
          if (digits.empty())
          {
              return result;
          }
          this->digits = digits;
          //储存字典
          store = unordered_map<char, string>{
              {'2', "abc"}, {'3', "def"}, {'4', "ghi"}, {'5', "jkl"}, {'6', "mno"}, {'7', "pqrs"}, {'8', "tuv"}, {'9', "wxyz"}};
          dfs("", 0);
          return result;
      }
  
      void dfs(string resultStr, int index)
      {
          int digitsSize = int(this->digits.size());
          if (digitsSize == index)
          {
              result.push_back(resultStr);
              return;
          }
          char targetChar = this->digits[index];
          string targetStr = store[targetChar];
          for (auto tmpChar : targetStr)
          {
              dfs(resultStr + tmpChar, index + 1);//递归调用
          }
          return;
      }
  };
  
  ```




```cpp
//广度优先，从第一位开始顺序向后判断，直至输入结束
class Solution
{
    vector<string> result;
    string digits;
    unordered_map<char, string> store;

public:
    vector<string> letterCombinations(string digits)
    {
        if (digits.empty())
        {
            return result;
        }
        this->digits = digits;
        
        //储存字典
        store = unordered_map<char, string>{
            {'2', "abc"}, {'3', "def"}, {'4', "ghi"}, {'5', "jkl"}, 
            {'6', "mno"}, {'7', "pqrs"}, {'8', "tuv"}, {'9', "wxyz"}};

        queue<string> workQueue;
        workQueue.push("");
        for (auto targetChar : digits)
        {
            string targetStr = store[targetChar];
            int queueSize = int(workQueue.size());
            
            //当前层，进行添加字符
            for (int i = 0; i < queueSize; ++i)
            {
                string tmpStr = workQueue.front();
                workQueue.pop();
                for (auto tmpChar : targetStr)
                {
                    workQueue.push(tmpStr + tmpChar);
                }
            }
        }
        while (!workQueue.empty())
        {
            string tmpStr = workQueue.front();
            workQueue.pop();
            result.push_back(tmpStr);
        }
        return result;
    }
};
```

---





---

### 回文问题

* #### 最长回文子串：给定一个字符串 s，找到 s 中最长的回文子串。你可以假设 s 的最大长度为 1000。(leetcode 5)

  本题至少有三种不错的做法

  - 采用动态规划求解：`P(i, j) = (P(i + 1, j − 1) and Si == Sj )`
  - 中心扩展法：回文中心的两侧互为镜像。因此，回文可以从它的中心展开，并且只有 2n - 1 个这样的中心。此处n表示插入元素间隔的个数：因为偶数回文会在字母之间。
  - [马拉车算法](https://medium.com/hackernoon/manachers-algorithm-explained-longest-palindromic-substring-22cb27a5e96f)

  ```cpp
  // 动态规划
  class Solution {
  public:
      string longestPalindrome(string s) {
  
          int len = s.size();
  
          if (len == 0 || len == 1)
              return s;
  
          int start = 0, max = 1;
          int isPalin[len][len] = {0};
  
          for (int i = 0; i < len; i++)
          {
              isPalin[i][i] = 1;
              if (i < len -1 && s[i] == s[i + 1])
              {
                  isPalin[i][i + 1] = 1;
                  max = 2;
                  start = i;
              }
          }
  
          for (int l = 3; l <= len; l++)
          {
              for (int i = 0; i + l - 1 < len; i++)
              {
                  int end = i + l - 1;
                  if (s[i] == s[end] && isPalin[i + 1][end - 1] == 1 )
                  {
                      isPalin[i][end] = 1;
                      max = l;
                      start = i;
                  }
              }
          }
  
          return s.substr(start, max);
          
      }
  };
  ```

  ```cpp
  // 中心扩展法
  // 采用内联追求极限性能
  inline 	int expandAroundCenter(string s, int left, int right)
  	{
  		int L = left, R = right;
  		while (L >= 0 && R < s.length() && s[L] == s[R])
  		{// 计算以left和right为中心的回文串长度
  			L--;
  			R++;
  		}
  		return R - L - 1;
  	}
  
  class Solution{
          public:
      string longestPalindrome(string s) 
  	{
  		if (s.length() < 1)
  		{
  			return "";
  		}
  		int start = 0, end = 0;
  		for (int i = 0; i < s.length(); i++)
  		{
  			int len1 = expandAroundCenter(s, i, i);//一个元素为中心
  			int len2 = expandAroundCenter(s, i, i + 1);//两个元素为中心
  			int len = max(len1, len2);
  			if (len > end - start)
  			{
  				start = i - (len - 1) / 2;
  				end = i + len / 2;
  			}
  		}
  		return s.substr(start, end - start + 1);
  	}
  
  };
  ```

  ```cpp
  // 马拉车算法
  class Solution{
  public:
  string longestPalindrome(string s)
  {
  	int len = s.length();
  	if (len < 1)
  	{
  		return "";
  	}
  
  	// 预处理
  	string s1;
  	for (int i = 0; i < len; i++)
  	{
  		s1 += "#";
  		s1 += s[i];
  	}
  	s1 += "#";
  
  	len = s1.length();
  	int MaxRight = 0;				// 当前访问到的所有回文子串，所能触及的最右一个字符的位置
  	int pos = 0;					// MaxRight对应的回文串的对称轴所在的位置
  	int MaxRL = 0;					// 最大回文串的回文半径
  	int MaxPos = 0;					// MaxRL对应的回文串的对称轴所在的位置
  	int RL[len] = {0};			// RL[i]表示以第i个字符为对称轴的回文串的回文半径
  
  	for (int i = 0; i < len; i++)
  	{
  		if (i < MaxRight)
  		{// 1) 当i在MaxRight的左边
  			RL[i] = min(RL[2 * pos - i], MaxRight - i);
  		}
  		else
  		{// 2) 当i在MaxRight的右边
  			RL[i] = 1;
  		}
  
  
  		// 尝试扩展RL[i]，注意处理边界
  		while (i - RL[i] >= 0  // 可以把RL[i]理解为左半径,即回文串的起始位不能小于0
  			&& i + RL[i] < len // 同上,即回文串的结束位不能大于总长
  			&& s1[i - RL[i]] == s1[i + RL[i]]// 回文串特性，左右扩展，判断字符串是否相同
  			)
  		{
  			RL[i] += 1;
  		}
  
  		// 更新MaxRight, pos
  		if (RL[i] + i - 1 > MaxRight)
  		{
  			MaxRight = RL[i] + i - 1;
  			pos = i;
  		}
  
  		// 更新MaxRL, MaxPos
  		if (MaxRL <= RL[i])
  		{
  			MaxRL = RL[i];
  			MaxPos = i;
  		}
  	}
  	return s.substr((MaxPos - MaxRL + 1) / 2, MaxRL - 1);
  }
  
  };
  ```

---

* #### 判断一个整数是否是回文数。回文数是指正序（从左向右）和倒序（从右向左）读都是一样的整数。（leetcode 9)

  本题有两种做法：1.转化为字符串再进行检索 2. 获取一半的整数和另一半比较。第二种空间使用更少，更优
```cpp
  class Solution {
  public:
      bool isPalindrome(int x) 
      {
          // 特殊情况：
          // 如上所述，当 x < 0 时，x 不是回文数。
          // 同样地，如果数字的最后一位是 0，为了使该数字为回文，
          // 则其第一位数字也应该是 0
          // 只有 0 满足这一属性
          if(x < 0 || (x % 10 == 0 && x != 0)) 
          {
              return false;
          }

          int revertedNumber = 0;
          while(x > revertedNumber) 
          {
              revertedNumber = revertedNumber * 10 + x % 10;
              x /= 10;
          }
      
          // 当数字长度为奇数时，我们可以通过 revertedNumber/10 去除处于中位的数字。
          // 例如，当输入为 12321 时，在 while 循环的末尾我们可以得到 x = 12，revertedNumber = 123，
          // 由于处于中位的数字不影响回文（它总是与自己相等），所以我们可以简单地将其去除。
          return x == revertedNumber || x == revertedNumber/10;
      }
  };
```

---

* #### 



---

### 查找元素

* #### 找指定的两数之和：给定一个整数数组 nums 和一个目标值 target，请你在该数组中找出和为目标值的那两个整数，并返回他们的数组下标。(leetcode 1)

  最蠢的是遍历，好点的是采用Map存储和查找，最佳选择为边存储边查找，下面给出后两种的解法。注意后一种改用了[unsorted_map](https://blog.csdn.net/u013354486/article/details/103327973)，会有更好的性能。

  ```cpp
  class Solution {
  public:
      vector<int> twoSum(vector<int>& nums, int target) {
          vector<int> result;
          map<int, int> mapNums;
          size_t count = nums.capacity();
  
          for (int i = 0; i < count; i++)
          {
              mapNums[nums[i]] = i;
          }
  
          for (int i = 0; i < count; i++)
          {
              int complete = target - nums[i];
              if (mapNums.find(complete) != mapNums.end() && mapNums[complete] != i)
              {               
                  result.push_back(i);
                  result.push_back(mapNums[complete]);
                  return result;
              }
          }
  
          return result;
      }
  };
  ```

  ```cpp
  class Solution {
  public:
      vector<int> twoSum(vector<int>& nums, int target) {
          //Key是数字，value是该数字的index
          unordered_map<int, int> hash;
          vector<int> result;
          int numsSize = int(nums.size());
          for (int i = 0; i < numsSize; i++)
          {
              int numberToFind = target - nums[i];
  
              //如果找到numberToFind，就return
              if (hash.find(numberToFind) != hash.end())
              {
                  result.push_back(hash[numberToFind]);
                  result.push_back(i);
                  return result;
              }
  
              //如果没有找到，把该数字的index放到hash表中
              hash[nums[i]] = i;
          }
          return result;
      }
  };
  ```

-----

* #### 寻找两个有序数组的中位数 ：给定两个大小为 m 和 n 的有序数组 nums1 和 nums2。请你找出这两个有序数组的中位数，并且要求算法的时间复杂度为 O(log(m + n))。你可以假设 nums1 和 nums2 不会同时为空。（leetcode 4)
  本题规定时间复杂度为Log，因此使用二分法可满足要求
  ```cpp
class Solution {
  public:
    double findMedianSortedArrays(vector<int>& nums1, vector<int>& nums2) {
  		int n = nums1.size();
  		int m = nums2.size();
  
  		if (n > m)  //保证数组1一定最短
  		{
  			return findMedianSortedArrays(nums2, nums1);
  		}
  
  		// Ci 为第i个数组的割,比如C1为2时表示第1个数组只有2个元素。LMaxi为第i个数组割后的左元素。RMini为第i个数组割后的右元素。
  		int LMax1, LMax2, RMin1, RMin2, c1, c2, lo = 0, hi = 2 * n;  //我们目前是虚拟加了'#'所以数组1是2*n长度
  
  		while (lo <= hi)   //二分
  		{
  			c1 = (lo + hi) / 2;  //c1是二分的结果
  			c2 = m + n - c1;
  
  			LMax1 = (c1 == 0) ? INT_MIN : nums1[(c1 - 1) / 2];
  			RMin1 = (c1 == 2 * n) ? INT_MAX : nums1[c1 / 2];
  			LMax2 = (c2 == 0) ? INT_MIN : nums2[(c2 - 1) / 2];
  			RMin2 = (c2 == 2 * m) ? INT_MAX : nums2[c2 / 2];
  
  			if (LMax1 > RMin2)
  				hi = c1 - 1;
  			else if (LMax2 > RMin1)
  				lo = c1 + 1;
  			else
  				break;
  		}
  		return (max(LMax1, LMax2) + min(RMin1, RMin2)) / 2.0;
      }
  };
  
  ```

-----

* #### 三数求和：给你一个包含 *n* 个整数的数组 `nums`，判断 `nums` 中是否存在三个元素 *a，b，c ，*使得 *a + b + c =* 0 ？请你找出所有满足条件且不重复的三元组。（leet code 15)

  * 最简单的做法是三重循环查找，一定可以得解。

  * 在此基础上，对第三次查找其实可以做优化：只需要找指定的数字即可，所以可以通过哈希、排序等方式获得，通过二分法降低时间复杂度

  * 更优的做法是将内部二层循环改为首尾双指针滑动判断：因为内层双循环寻找固定的值，而且要求不重复，因此排序之后从首尾开始判断并滑动得出结果即可。时间复杂度NlogN
    

```cpp
class Solution {
public:
	vector<vector<int>> threeSum(vector<int>& nums) {
		int target = 0, val = 0, begin, end;
        int size = nums.size();
		vector<int> tmp;
		vector<int>::iterator beginIter, endIter;
		vector<vector<int>> ret;

		if (size <= 2)
			return ret;

		sort(nums.begin(), nums.end());
		for (int i = 0; i < size - 2; i++)
		{
			target = 0 - nums[i];
			if (target < 0)
				break;

			beginIter = nums.begin() + i + 1;
			endIter = nums.end() - 1;

			while (beginIter < endIter)
			{
				begin = *beginIter;
                end = *endIter;
				val = begin + end;
				if (val == target)
				{
					tmp.push_back(nums[i]);
					tmp.push_back(*beginIter);
					tmp.push_back(*endIter);
					ret.push_back(tmp);
					tmp.clear();
					while (beginIter < endIter && *beginIter == begin)
						beginIter++;
					while (beginIter < endIter && *endIter == end)
						endIter--;

				}
				else if (val < target)
				{
					beginIter++;
				}
				else
				{
					endIter--;
				}
			}

			while (i < size - 2 && nums[i] == nums[i + 1])
				i++;
		}

		return ret;
	}
};

```

---

* #### 最接近的三数之和：给定一个包括 n 个整数的数组 nums 和 一个目标值 target。找出 nums 中的三个整数，使得它们的和与 target 最接近。返回这三个数的和。假定每组输入只存在唯一答案。（leet code 16)

  和上题类似，加一个绝对值判断

  ```cpp
  class Solution {
  public:
      int threeSumClosest(vector<int>& nums, int target) {
  		int val = 0, begin, end, ret = 0, inTarget, gap;
  		int size = nums.size();
  		vector<int>::iterator beginIter, endIter;
  
  		if (size <= 2)
  			return 0;
  
  		gap = INT_MAX;
  
  		sort(nums.begin(), nums.end());
  		for (int i = 0; i < size - 2; i++)
  		{
  			inTarget = target - nums[i];
  
  			beginIter = nums.begin() + i + 1;
  			endIter = nums.end() - 1;
  
  			while (beginIter < endIter)
  			{
  				begin = *beginIter;
  				end = *endIter;
  				val = begin + end;
  				if (val == inTarget)
  				{
  					return target;
  				}
  				else if (val < inTarget)
  				{
  					beginIter++;
  				}
  				else
  				{
  					endIter--;
  				}
  
  				if (abs(val - inTarget) < gap)
  				{
  					gap = abs(val - inTarget);
  					ret = val + nums[i];
  				}
  			}
  		}
  
  		return ret; 
      }
  };
  
  ```

---

* #### 





---

### 匹配问题

匹配问题包括正则表达式、括号匹配等等。

---

* #### 正则表达式匹配：给你一个字符串 s 和一个字符规律 p，请你来实现一个支持 ‘.’ 和 ‘\*’ 的正则表达式匹配。‘.’ 匹配任意单个字符，‘*’ 匹配零个或多个前面的那一个元素。所谓匹配，是要涵盖 整个 字符串 s的，而不是部分字符串。（leet code 10)
  说明:s 可能为空，且只包含从 a-z 的小写字母。p 可能为空，且只包含从 a-z 的小写字母，以及字符 . 和 *。



**采用从后向前检索的方式进行**

```cpp
class Solution {
public:
    vector<vector<int> > memo;
    int dfs(const string& s, const string& p, int i, int j) {
        if (i == s.size()) return j == p.size() ? 1 : -1;
        if (j == p.size()) return i == s.size() ? 1 : -1;
        if (memo[i][j] != 0) return memo[i][j];
        if (j == p.size() - 1 || p[j + 1] != '*') {
            if (p[j] == '.' || p[j] == s[i]) {
                memo[i][j] = dfs(s, p, i + 1, j + 1);
                return memo[i][j];
            }
        } else {
            if (dfs(s, p, i, j + 2) > 0) {
                memo[i][j] = 1;
                return memo[i][j];
            }
            if (p[j] == '.' || p[j] == s[i]) {
                bool t = dfs(s, p, i + 1, j + 2) > 0 || dfs(s, p, i + 1, j) > 0;
                memo[i][j] = t ? 1 : -1;
                return memo[i][j];
            }
        }
        memo[i][j] = -1;
        return memo[i][j];
    }
    bool isMatch(string s, string p) {
        s += '#';
        p += '#';
        memo = vector<vector<int> >(s.size(), vector<int>(p.size(), 0));
        return dfs(s, p, 0, 0) > 0;
    }
};

```

---

* #### 有效的括号：给定一个只包括 ‘(’，’)’，’{’，’}’，’[’，’]’ 的字符串，判断字符串是否有效。
  有效字符串需满足：左括号必须用相同类型的右括号闭合。左括号必须以正确的顺序闭合。

  本题采用栈比较容易解决：对左半边括号采取入栈操作，右半边括号和栈顶进行对比，如果不一致则说明有问题

  ```cpp
  class Solution 
  {
  public:
      bool isValid(string s) 
      {
          if (s.size() == 0) 
              return true;
          char *stack = (char*)malloc(s.size()); 
          int top =0;
          for (int i = 0; s[i]; ++i) 
          {
              if (s[i] == '(' || s[i] == '[' || s[i] == '{') 
                  stack[top++] = s[i];
              else 
              {
                  if ((--top) < 0) //先减减，让top指向栈顶元素             
                      return false;
                  if (s[i] == ')' && stack[top] != '(') 
                      return false;
                  if (s[i] == ']' && stack[top] != '[') 
                      return false;
                  if (s[i] == '}' && stack[top] != '{') 
                      return false;
              }
          }
          free(stack);
          return (!top);
      }
  };
  ```

---

* #### 括号生成：给出 n 代表生成括号的对数，请你写出一个函数，使其能够生成所有可能的并且有效的括号组合。（leet code 22)

  对于此类问题回溯法显然可解

  1. 当前左右括号都有大于 0 个可以使用的时候，才产生分支；
  2. 产生左分支的时候，只看当前是否还有左括号可以使用；
  3. 产生右分支的时候，还受到左分支的限制，右边剩余可以使用的括号数量一定得在严格大于左边剩余的数量的时候，才可以产生分支；
  4. 在左边和右边剩余的括号数都等于 0 的时候结算。

  ```cpp
  class Solution {
  public:
      void backtrace(int left, int right, int n, string& s, vector<string>& res) 
      {
          if (left == n && right == n) 
          {
              res.push_back(s);
              return;
          }
          if (left < n) 
          {
              s += "(";
              backtrace(left + 1, right, n, s, res);
              s.pop_back();
          }
          if (right < left) 
          {
              s += ")";
              backtrace(left, right + 1, n, s, res);
              s.pop_back();
          }
      }
      vector<string> generateParenthesis(int n) 
      {
          vector<string> res;
          string s;
          backtrace(0, 0, n, s, res);
          return res;
      }
  };
  
  ```

  更优秀的解法为动态规划：

  **核心思想**

  * 对与 `i=n` 的情况，我们考虑整个括号排列中最左边的括号

  * 最左边的括号一定是左括号

  * 和它对应的右括号组成一组完整的括号 `"( )"`，我们认为这一组是相比 `n-1` 增加进来

  * 剩下的括号要么在这一组新增的括号内部，要么在这一组新增括号的外部（右侧）

  * "(" + 【i=p时所有括号的排列组合】 + ")" + 【i=q时所有括号的排列组合】

    其中 `p + q = n-1`，且 `p q` 均为非负整数。

    事实上，当上述 `p` 从 `0` 取到 `n-1`，`q` 从 `n-1` 取到 `0` 后，所有情况就遍历完了。

```cpp
class Solution {
public:
	vector<string> generateParenthesis(int n) 
    {
		if (n == 0) return {};
		if (n == 1) return { "()" };
		vector<vector<string>> dp(n+1);
		dp[0] = { "" };
		dp[1] = { "()" };
        
		for (int i = 2; i <= n; i++) 
        {
			for (int j = 0; j <i; j++) 
            {
				for (string p : dp[j])
                {
					for (string q : dp[i - j - 1]) 
                    {
						string str = "(" + p + ")" + q;
						dp[i].push_back(str);
					}
                }
			}
		}
		return dp[n];
	}
};
```

---

* ####



-----

链表
------

主要包括手写单双链表、链表合并、链表查找、两个链表比较等。

* #### 手写代码，实现一个双向循环链表的增删查操作

```cpp
  struct list_head
  {
  	struct list_head *next, *prev;
  };

  /**
   * 添加链表表项
   * insert a new entry between two known consecutive entries.
   *
   * This is only for internal list manipulation where we konw
   * the prev/next entries already
   */
  static inline void __list_add(struct list_head *new,
  			     struct list_head *prev,
  			     struct list_head *next)
  {
  	next->prev = new;
  	new->next = next;
  	new->prev = prev;
  	prev->next = new;
  }

  /**
   * 添加新的链表项
   * list_add    -     add a new entry
   * @new: new entry to be added
   * @head: list head to add it after
   *
   * Insert a new entry after the specified head.
   * This is good for implenting stacks.
   */
  static inline void list_add(struct list_head *new, struct list_head *head)
  {
  	__list_add(new, head, head->next);
  }

  /**
   * 在尾部添加新链表项
   * list_add_tail    -     add a new entry
   * @new: new entry to be added
   * @head: list head to add it before
   *
   * Insert a new entry before the specified head.
   * This is good for implenting queue.
   */
  static inline void list_add_tail(struct list_head *new, struct list_head *head)
  {
  	__list_add(new, head->prev, head);
  }

  /*
   * Delete a list entry by making the prev/next entries
   * point to each other
   *
   * This is only for internal list manipulation where we know
   * the prev/next entries already!
   */
   static inline void __list_del(struct list_head *prev, struct list_head *next)
   {
   	next->prev = prev;
  	prev->next = next;
   }

  /**
   * list_del    -    delete entry from list
   * @entry: the element to delete from the list
   * Note: list_empty on entry does not return true after this, the entry is
   * in an undefined state.
   */
  static inline void list_del(struct list_head *entry)
  {
  	__list_del(entry->prev, entry->next);
  	entry->next = LIST_POSITION1;
  	entry->prev = LIST_POSITION2;
  }

```

-----

* #### 两数相加：给出两个非空的链表用来表示两个非负的整数。其中, 它们各自的位数是按照逆序的方式存储的，并且它们的每个节点只能存储一位数字。(leetcode 2)
  直接链表操作即可，考察基本的链表遍历、插入操作
  
  ```cpp
  /**
   * Definition for singly-linked list.
   * struct ListNode {
   *     int val;
   *     ListNode *next;
   *     ListNode(int x) : val(x), next(NULL) {}
   * };
   */
  class Solution {
  public:
      ListNode* addTwoNumbers(ListNode* l1, ListNode* l2) {
          int count = 0;
          ListNode *num = new ListNode(-1);
          ListNode *ptr = num;
          
          while (l1 || l2)
          {
              int sum=0;
              if(l1)
              {
                  sum += l1->val;
                  l1 = l1->next;
              }
              if(l2)
              {
                  sum += l2->val;
                  l2 = l2->next;
              }
  
              sum += count;
              if (sum >= 10)
              {
                  count = 1;
              }
              else
              {
                  count = 0;
              }
  
              ptr = ptr->next = new ListNode(sum % 10) ;
          }
  
          if (count)
               ptr = ptr->next = new ListNode(1) ;           
  
          return num->next;
      }
  };
  ```
-----

* #### 删除链表的倒数第N个节点：给定一个链表，删除链表的倒数第 n 个节点，并且返回链表的头结点。（leet code 19)
  
  采用双指针滑动：两个指针相距为n，则尾指针到结束了表示首指针为倒数第n个节点

```cpp
/**
 * Definition for singly-linked list.
 * struct ListNode {
 *     int val;
 *     ListNode *next;
 *     ListNode(int x) : val(x), next(NULL) {}
 * };
 */
class Solution{
public:
    ListNode* removeNthFromEnd(ListNode* head, int n) {
        ListNode *front = head;
        ListNode *rear = head;

        if(head->next == NULL) 
            return NULL;

        while(n >= 0)
        {
            if(rear == NULL) 
                return head -> next;
            rear = rear->next;
            n --;
        }

        while(rear)
        {
            rear = rear -> next;
            front = front -> next;
        }
        
        front->next = front->next->next;
        return head;
    }
};
```

---

* #### k个一组翻转链表：给你一个链表，每 k 个节点一组进行翻转，请你返回翻转后的链表。k 是一个正整数，它的值小于或等于链表的长度。
  如果节点总数不是 k 的整数倍，那么请将最后剩余的节点保持原有顺序。

  为了翻转K个一组，则需要遍历k个，让每一个指针指向前一个节点，然后最前面的节点指向下一组的开始。因此考虑迭代解决

  ```cpp
  /**
   * Definition for singly-linked list.
   * struct ListNode {
   *     int val;
   *     ListNode *next;
   *     ListNode(int x) : val(x), next(NULL) {}
   * };
   */
  class Solution {
  public:
      ListNode* reverseKGroup(ListNode* head, int k) {
          int d = 0;
          auto node = head;
          while (node != NULL) {
              if (++d >= k) break;
              node = node->next;
          }
          if (d < k) return head;
          ListNode* prev = NULL;
          ListNode* curr = head;
          for (int i = 0; i < k; ++i) {
              auto node = curr->next;
              curr->next = prev;
              prev = curr;
              curr = node;
          }
          head->next = reverseKGroup(curr, k);
          return prev;
      }
  };
  ```

-----

* ####
  
-----

队列
------
* ####
  
-----

* ####
  
-----

* ####
  
-----

* ####
  
-----


栈
------
* ####
-----

* ####
  
-----

* ####
  
-----

* ####
  
-----



堆
------
* ####
  
-----

* ####
  
-----

* ####
  
-----

* ####
  
  
-----




树
------
* ####
  
-----

* ####
  
-----

* ####
  
-----

* #### 红黑树的特点和优点
  红黑树能够以O(log2 n)的时间复杂度进行搜索、插入、删除操作。

  满足下列条件的二叉搜索树是红黑树：
  * 每个结点要么是“红色”，要么是“黑色”
  * 所有的叶结点都是空结点，并且是“黑色”的
  * 如果一个结点是“红色”的，那么它的两个子结点都是“黑色”的
  * 如果結點是黑色的，那么它的子節點可以是紅色或者是黑色的
  * 结点到其子孙结点的每条简单路径都包含相同数目的“黑色”结点
  * 根结点永远是“黑色”的
-----




哈希表
------
* ####
  
-----

* ####
  
-----

* ####
  
-----

* ####
  
-----




排序算法
------

* ####
  
-----

* ####
  
-----

* ####
  
-----

* ####
  
-----



回溯法
------



* ####
  
-----

* ####
  
-----

* ####
  
-----

* ####
  
-----



动态规划
------

​	动态规划是一种以空间换时间的做法，相对于回溯法穷尽所有来说，动态规划更类似于剪枝后的优化型回溯，但是要更为优秀：因为不用考虑前面过多的部属，而只用考虑当前状态上一状态即可。有一个很有用的判断方法：画出回溯的树形，如果有重复子结构，一般说明回溯不是最优，可以尝试动态规划。否则回溯就是最优解了。

* ####
  
  
-----

* ####
  
-----

* ####
  
-----

* ####
  
-----



贪心算法
------
* ####
  
-----

* ####
  
-----

* ####
  
-----

* ####
  
-----



大数查找
------

* #### 4G的long型整数中找到一个最大的，如何做？
  要找到最大的肯定要遍历所有的数的，而且不能将数据全部读入内存，可能不足。算法的时间复杂度肯定是O（n）
可以改进的地方，就是读入内存的时候，一次多读些
需要注意的就是每次从磁盘上尽量多读一些数到内存区，然后处理完之后再读入一批。减少IO次数，自然能够提高效率。而对于类快速排序方法，稍微要麻烦一些： 分批读入，假设是M个数，然后从这M个数中选出n个最大的数缓存起来，直到所有的N个数都分批处理完之后，再将各批次缓存的n个数合并起来再进行一次类快 速排序得到最终的n个最大的数就可以了。在运行过程中，如果缓存数太多，可以不断地将多个缓存合并，保留这些缓存中最大的n个数即可。由于类快速排序的时 间复杂度是O（N），这样分批处理再合并的办法，依然有极大的可能会比堆和败者树更优。当然，在空间上会占用较多的内存。

此题还有个变种，就是寻找K个最大或者最小的数。有以下几种算法：
  容量为K的最大堆/最小堆，假设K可以装入内存；
如果N个数可以装入内存，且都小于MAX，那么可以开辟一个MAX大的数组，类似计数排序。。。从数组尾部扫描K个最大的数，头部扫描K个最小的数。

------

* #### 有千万个string在内存怎么高速查找，插入和删除？
  对千万个string做hash，可以实现高速查找，找到了，插入和删除就很方便了。
-----


* #### 找出1-10w中没有出现的两个数字
  - 方法1：
    设这些数保存在数组A中，用一个10w的数组B标志某一个数是否出现，i出现则B[i]=1,没出现则B[i]=0；扫描数组B，查找缺失的两个数。
    时间复杂度：O(N)
    空间复杂度：O(N)

  - 方法2：
    设确实的两个数为X和Y，则我们可以通过累加1到10W的和减去数组A的和，得到X+Y的值S1：
    X+Y = S1
    同理，我们可以得到1到10W的平方和 减去 数组A中每个数的平方和，从而得到X^2 + Y^2的值：
    X^2 + Y^2 = S2
    根据两个方程可以解出X和Y。
    这里需要注意的是，计算平方和的时候有可能会越界，可以使用unsigned long long，或者一边加(1到10W的平方和)一边减(数组A中每个数)。
-----


* #### 判断数字是否出现在40亿个数中？给40亿个不重复的unsignedint的整数，没排过序的，然后再给几个数，如何快速判断这几个数是否在那40亿个数当中？
  ​        unsigned int 的取值范围是0到232-1。我们可以申请连续的232/8=512M的内存，用每一个bit对应一个unsigned int数字。首先将512M内存都初始化为0，然后每处理一个数字就将其对应的bit设置为1。当需要查询时，直接找到对应bit，看其值是0还是1即可。

  将这40亿个数分成两类: 最高位为0、最高位为1
  并将这两类分别写入到两个文件中，其中一个文件中数的个数<=20亿，而另一个>=20亿（这相当于折半了）；与要查找的数的最高位比较并接着进入相应的文件再查找。
  再然后把这个文件为又分成两类: 次最高位为0、次最高位为1
  并将这两类分别写入到两个文件中，其中一个文件中数的个数<=10亿，而另一个>=10亿（这相当于折半了）；> 与要查找的数的次最高位比较并接着进入相应的文件再查找
  …
  以此类推，就可以找到了,而且时间复杂度为O(logn)
-----

* #### 在一个文件中有10G个整数，乱序排列，要求找出中位数。内存限制为2G
  ​        假设整数用32bit来表示。
  第一步：要表示10G个整数，最少需要一个64位的数据空间。（10G = 5 * 2^31 > 2^32 )
  第二步：分区间
  2G的内存，能够表示多少个64bit，就能分多少个区间。（一个区间 就表示 一个64bit的数据空间）
  区间数位：2G / 64bit = 256M 个区间。
  第三步：求区间表示范围
  32bit的整数最大值为232-1,所以区间的范围是232 / 256M = 16.
  即0 ~ 15 ，16 ~ 31，32 ~ 47，…（总共256M个)
  此时我们有 256M个区间，大小总共为256M * 64bit = 2G内存。
  第四步：遍历10G个整数。每读取一个整数就将此整数对应的区间+1。
  第五步：找出中位数所在的区间
  统计每个区间中整数的值。然后从第一个区间的整数值开始累加。当累加到5G时，停止。此时的区间便包含中位数。记下此区间所表示的范围，设为[a,a+15].并且记下此区间之前所有区间的累加和，设为m。释放掉除包含中位数区间的其他所有区间的内存。
  第六步：再次遍历10G个整数，统计出现在区间[a,a+15]中每个值的计数，有16个数值，按照a到a+15排序。设为n0,n1,n2,…n15
  第七步：当m+n0+n1+…+nx首次大于5G时，此时的 a+x 就是所求的中位数。

-----

* #### 统计论坛在线人数分布。求一个论坛的在线人数，假设有一个论坛，其注册ID有两亿个，每个ID从登陆到退出会向一个日志文件中记下登陆时间和退出时间，要求写一个算法统计一天中论坛的用户在线分布，取样粒度为秒。
  ​    一天总共有 3600*24 = 86400秒。
  定义一个长度为86400的整数数组int delta[86400]，每个整数对应这一秒的人数变化值，可能为正也可能为负。开始时将数组元素都初始化为0。
  然后依次读入每个用户的登录时间和退出时间，将与登录时间对应的整数值加1，将与退出时间对应的整数值减1。
  这样处理一遍后数组中存储了每秒中的人数变化情况。
  定义另外一个长度为86400的整数数组int online_num[86400]，每个整数对应这一秒的论坛在线人数。
  假设一天开始时论坛在线人数为0，则第1秒的人数online_num[0] = delta[0]。第n+1秒的人数online_num[n] = online_num[n-1] + delta[n]。
  这样我们就获得了一天中任意时间的在线人数。
  另外，如果只知道IP，为了方便查找，可以采用哈希表进行记录，较数组更为方便。

-----

* #### 需要多少只小白鼠才能在24小时内找到毒药有1000瓶水，其中有一瓶有毒，小白鼠只要尝一点带毒的水24小时后就会死亡，至少要多少只小白鼠才能在24小时时鉴别出那瓶水有毒？
    ​       每个老鼠只有死或活2种状态，因此每个老鼠可以看作一个bit，取0或1
  N个老鼠可以看作N个bit，可以表达2^N种状态（其中第i个状态代表第i个瓶子有毒）
  例如：当N＝2时，可以表达4种状态，假设有A、B、C、D四个瓶子分别编号入下
  
  0， 0
  
  0， 1
  
  1， 0
  
  1， 1
  
  一号老鼠喝第一位为1的，二号老鼠喝第二位为1的，将老鼠活着记为0，死了记为1，则
  如果 一号老鼠活，二号老鼠活，状态为（0, 0）说明是A有毒
  如果 一号老鼠活，二号老鼠死 ,  状态为（0, 1）说明是B有毒 
  如果 一号老鼠死，二号老鼠活 ，状态为（1, 0）说明是C有毒 
  如果 一号老鼠死，二号老鼠死 ，状态为（1, 1）说明是D有毒
  
  ​    对N个瓶子也是一样的处理：让第一只老鼠喝所有第一位为1的瓶子，第二只喝第二位，依次喝下去。状态位和瓶子编号可以刚好对应，一次完成。

-----

* ####
  答：

-----

* ####
  答：

-----

* ####
  答：



* 
  
越界问题
-----
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
  class Solution {
  public:
      int reverse(int x) {
          int rev = 0;
          while (x != 0) {
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
* ####
  答：

-----
