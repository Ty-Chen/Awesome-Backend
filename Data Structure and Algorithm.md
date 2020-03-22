数据结构和算法面试攻略

数据结构主要包括
1. 数组和字符串
2. 链表
3. 队列
4. 栈
5. 堆
6. 树
7. 哈希表

主要考察包括基本结构、基本用法、以及一些由这些结构衍生出来和其他算法结合的问题。队列和栈一般不单独考察，而是作为其他解法（尤其是回溯）中很重要的元素，因此不单独列出。

算法主要包括

1. 排序
2. 分治
3. 回溯
4. 动态规划
5. 贪心算法
6. 海量数据问题
7. 越界问题等。

其中分治、回溯、动态规划、贪心算法多和数据结构(尤其是数组)结合，因此不单独列出来。

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
  class Solution 
  {
  public:
      int lengthOfLongestSubstring(string s) 
      {
  
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

  本题主要在于关注以下几点：
  （1）完全匹配意味着寻找s中长度为words总长的窗口，采用滑动窗口进行处理
  （2）由于串联顺序并未规定，因此采用哈希表是很合理的做法
  （3）哈希表中key为单词，value为words中出现次数，滑动窗口去查找目前有多少个该类单词，如果恰好满足则数量+1

  ```cpp
  class Solution {
  public:
      vector<int> findSubstring(string s, vector<string> &words) {
          //特殊情况直接排除
          if(s.empty() || words.empty())
              return {};
          
          //存放结果的数组
          vector<int> result;
          
          //单词数组中的单词的大小，个数，以及总长度
          int one_word = words[0].size();
          int word_num = words.size();
          //int all_len = one_word * word_num;
          
          //建立单词->单词个数的映射
          unordered_map<string, int> m1;
          for(const auto &w:words)
              m1[w]++;
          
          for(int i = 0; i < one_word; ++i)
          {
              //left和rigth表示窗口的左右边界，count用来统计匹配的单词个数
              int left = i, right = i, count = 0;
              
              unordered_map<string, int> m2;
              
              //开始滑动窗口
              while(right + one_word <= s.size())
              {
                  //从s中提取一个单词拷贝到w
                  string w = s.substr(right, one_word);
                  right += one_word;//窗口右边界右移一个单词的长度
                  
                  if(m1.count(w) == 0)
                  {//此单词不在words中，表示匹配不成功,然后重置单词个数、窗口边界、以及m2
                      count = 0;
                      left = right;
                      m2.clear();
                  }
                  else
                  {//该单词匹配成功，添加到m2中
                      m2[w]++;
                      count++;    
                      while(m2.at(w) > m1.at(w))//一个单词匹配多次，需要缩小窗口，也就是left右移
                      {
                          string t_w = s.substr(left, one_word);
                          count--;
                          m2[t_w]--;
                          left += one_word;
                      }
                      if(count == word_num)
                          result.push_back(left);
                  }
              }
          }
          return result;
      }
  };
  ```

-----

* #### 编辑距离：给定两个单词word1和word2，计算出将word1转换成word2所用最少操作数

  本题可采用动态规划求解：dp[i][j]表示word1 i 位和 word2 i位之间的差距，其决定于i - 1和j - 1之间的关系，由于可以插入/删除/替换，因此包括了`dp[i - 1][j]， dp[i][j - 1], dp[i - 1][j - 1]`三种

  ```cpp
  class Solution 
  {
  public:
      //集合表示 dp[i][j] 对前i个字符进行操作,转换为目标的前j个字符的操作次数 属性->操作次数最小值
      
      //集合划分 dp[i][j]的来源  考虑对第i个字符进行的操作是什么
      //1 插入操作 从而相等 所以先让前i个字符变为j-1字符，然后在第i处插入j代表的字符 即dp[i][j-1]+1
      //2 删除操作 从而相等 所以先让前i-1个字符变为j字符，然后在第i处删除 即dp[i-1][j]+1
      //3 替换操作 从而相等 if(i处等于j处 不需要替换) 即dp[i-1][j-1]
      //                   else 需要替换 dp[i-1][j-1]+1 
      //上述取个最小值即可
      int minDistance(string w1, string w2) 
      {
          int n = w1.size(), m = w2.size();
          vector<vector<int>> dp(n+1,vector<int>(m+1));
          for(int i = 0; i <= n; i++) 
              dp[i][0] = i;//i个字符转化为0个字符 只能一直删i次
          
          for(int j = 0; j <= m; j++) 
              dp[0][j] = j;//0个字符转化为j个字符 只能一直插入j次
          
          for(int i = 1; i <= n; i++)
          {
              for(int j = 1; j <= m; j++)
              {
                  dp[i][j] = min(dp[i][j-1],dp[i-1][j])+1;//插入 删除 时
                  dp[i][j] = min(dp[i][j],dp[i-1][j-1] + (w1[i-1]==w2[j-1] ? 0:1));//替换时
              }
          }
          return dp[n][m];
      }
  };
  
  
  ```

---

* #### 最小覆盖子串：给你一个字符串 S、一个字符串 T，请在字符串 S 里面找出：包含 T 所有字母的最小子串。(leetcode 76)

  本题采用滑动窗口求解：左侧窗口用于收紧大小，右侧用于保证找到所有字母。

  1、我们在字符串 S 中使用双指针中的左右指针技巧，初始化 left = right = 0，把索引闭区间 [left, right] 称为一个「窗口」。

  2、我们先不断地增加 right 指针扩大窗口 [left, right]，直到窗口中的字符串符合要求（包含了 T 中的所有字符）。

  3、此时，我们停止增加 right，转而不断增加 left 指针缩小窗口 [left, right]，直到窗口中的字符串不再符合要求（不包含 T 中的所有字符了）。同时，每次增加 left，我们都要更新一轮结果。

  4、重复第 2 和第 3 步，直到 right 到达字符串 S 的尽头。

  ```cpp
  class Solution 
  {
  public:
      string minWindow(string s, string t) 
      {
          int count[256] = { 0 };
          for (auto c : t) ++count[c];
          int len = 0, minLength = s.length();
          string res;
          for (int l = 0, r = 0; r < s.length(); ++r) 
          {
              if (--count[s[r]] >= 0) 
                  ++len;
              while (len == t.length()) 
              {
                  if (r - l + 1 <= minLength) 
                  {
                      minLength = r - l + 1;
                      res = s.substr(l, r - l + 1);
                  }
                  if (++count[s[l++]] > 0) 
                      --len;
              }
          }
          return res;        
      }
  };
  
  ```

---

* #### 至多包含两个不同字符的最长子串(leetcode 159)

  同样采用滑动窗口求解：观察一下，发现可以用sliding window来解，

  用left和right表示window的左边界和右边界，

  用queue记录当前在window里的字符，

  用last_pos记录 当前在window里的字符的最后一次出现的下标。

  让right逐个向右扫描, 每次遇到一个新元素char = s[right]时，

  1. 如果char已经在queue里，说明非常好不需要额外处理，只需要更新last_pos，last_pos[char] = right

  2. 如果char不在queue里

     a.如果queue的size < 2，说明还有空位可以直接放进去，然后再在last_pos里记录last_pos[char] = right

     b.如果queue的size >= 2，说明要踢人了，queue中的两个元素里必须有一个滚蛋，

  怎么决定踢哪个呢？

  思路有点类似LRU， 如果一个元素上一次出现是在下标为3， 另一个上一次出现是下标为4，当前下标为5，

  应该选择踢下标为3的那个，为了保证新的子串更长。

---

* #### 长度最小的子数组：给定一个含有 **n** 个正整数的数组和一个正整数 **s ，**找出该数组中满足其和 **≥ s** 的长度最小的连续子数组**。**如果不存在符合条件的连续子数组，返回 0。(leetcode 209)

  * 初始化 left 指向 0 且初始化 sum 为 0
  * 遍历nums 数组：
    * 将 nums[i] 添加到 sum
    * 当 sum 大于等于 ss 时：
      * 更新 `ans = min(ans, i + 1-left)` ，其中i+1−left是当前子数组的长度
      * 然后我们可以移动左端点，因为以它为开头的满足 ssum≥s 条件的最短子数组已经求出来了
      * 将 sum 减去 nums[left] 然后增加left

---

* #### 字符串的排列：给定两个字符串 **s1** 和 **s2**，写一个函数来判断 **s2** 是否包含 **s1** 的排列。(leetcode 567)

  * 长度方面
  s2子串的长度必须和s1相等,是m.
  就是说，子串从s2的下标left处开始，那么子串right最多到达下标left+m-1处(right<left+m)
  而且下标left最大为n-m(left<=n-m),如果大于这个数，就算把剩余的字符都加起来也没有m个

  * 字符种类方面
    我们确定一个子串开始下标left后，要往后查询m个字符，假如遇到不属于s1中的字符怎么办？

    s1: "abc"  
    s2: "actb" 
    当我们从下标0开始往后查询，'a'和'c'都存在于s1中，但是't'不属于
    我们会发现,'t'前面的m-1个字符无论怎么样都不可能避过't'查询到后面的字符
    换句话说,从't'前面m-1个字符开始的子串都不可能与s1匹配
    所以我们可以直接从't'后面的第一个字符'b'开始查询
  
  * 字符数量方面
    假设一段长为m的子串中所有的字符都属于s1，我们怎么确定每种字符的数量呢？
    每种字符的数量有三种: 小于，等于，大于
  我们是要比较三种情况吗？实际上只需要比较一种即可。
  
  例如，我们比较子串中每一种字符是否大于s1这种字符的数量：
  
  > 如果没有任何一种字符数量大于s1中字符的数量，而字符总的数量是一定的，那么不就可以知道也不存在小于s1字符数量的字符吗？
> 即这两段字符串匹配。
  
  > 假如存在一种字符数量大于s1，下一步我们应该怎么做？
  > 我们是不是要把这个字符多出来的数量从子串中减去？
  > 如何操作呢？
  > 操作的方法就是 left++   
> 向后移动字符串的开始字符，直到这种字符的数量恢复正常。(不明白为什么动left不动right的可以自己想一下)   
  
  > 例子：
  > s1: "abc" a:1 b:1 c:1
  > s2: "abbca" 
  > 当left=0时，right走到2，也就是第二个'b'时，'b'的数量会超过s1'b'的数量，我们可以通过把left不断后移到 2   
  > 来使'b'的数量恢复正常
  > 虽然这个时候'a'的数量是正确的，但是我们依然要后移left，因为只要有两个'b'我们永远找不到匹配的字符串  
> 但是减去的'a'却可以在后面加回来

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

* #### 接雨水：给定 n 个非负整数表示每个宽度为 1 的柱子的高度图，计算按此排列的柱子，下雨之后能接多少雨水。(leet code 42)

  遇到这种一般最优解都是双指针首尾滑动，主要难点在于设计怎么计算。这题的核心思想在于：左边比右边高则从右边开始算格子，右边比左边高则从左边开始算格子

  ```cpp
  class Solution {
  public:
      int trap(vector<int>& height) {
          int begin = 0, end = height.size() - 1, ret = 0;
          int leftMax = 0, rightMax = 0;
          while (begin < end)
          {
              if (height[begin] < height[end])
              {
                  if (height[begin] < leftMax)
                      ret += leftMax - height[begin];
                  else
                      leftMax = height[begin];
                  
                  begin++;
              }
              else
              {
                  if (height[end] < rightMax)
                      ret += rightMax - height[end];
                  else
                      rightMax = height[end];
                  end--;
              }
          }
            
          return ret;
      }
  };
  ```

---

* #### 柱状图中的最大矩形：给定 n 个非负整数，用来表示柱状图中各个柱子的高度。每个柱子彼此相邻，且宽度为 1 。求在该柱状图中，能够勾勒出来的矩形的最大面积。（leetcode 84)

  本题和接雨水类似，均可以采用栈的方式去记录一个递增/递减序列从而顺序存储一个值，如果遇到不符合条件则出栈。也可以采用双指针滑动的方式求解。

  ```cpp
  class Solution {
  public:
      int largestRectangleArea(vector<int>& heights) {
          stack<int> s;
          s.push(-1);
          int max_area = 0, height, width;
          for(int i = 0; i < heights.size(); ++i) {
              while(s.top() != -1 && heights[i] <= heights[s.top()]) {
                  height = heights[s.top()];
                  s.pop();
                  width = i-s.top()-1;
                  max_area = max(max_area, width*height);
              }
              s.push(i);
          }
          while(s.top() != -1) {
              height = heights[s.top()];
              s.pop();
              width = heights.size() - s.top() - 1;
              max_area = max(max_area, width*height);
          }
          return max_area;
      }
  };
  
  ```

---

* #### 跳跃游戏：给定一个非负整数数组，你最初位于数组的第一个位置。数组中的每个元素代表你在该位置可以跳跃的最大长度。你的目标是使用最少的跳跃次数到达数组的最后一个位置。

  本题可用动态规划：dp[0] = 1 + dp[1]或dp[2], … dp[dp[0]]，倒推至可以一步到达n，则完成，计数在dp之中，然后取最小值返回即可

  ```cpp
  	int jump(vector<int>& nums) {
  		int size = nums.size();
  		int dp[100] = { 0 };
  
  		if (size == 1)
  			return 0;
  		for (int i = size - 2; i >= 0; i--)
  		{
  			if (nums[i] + i >= size - 1)
  				dp[i] = 1;
  			else
  			{
  				int min = size + 1;
  				for (int j = 1; j <= nums[i]; j++)
  				{
  					if (dp[i + j] >= 0 && dp[i + j] < min)
  					{
  						min = dp[i + j] + 1;
  					}
  				}
  				dp[i] = min;
  			}
  		}
  
  		return dp[0];
  	}
  
  ```

  **第二种方法是从头往后贪心算法求解**

  ```cpp
  class Solution 
  {
  public:
      int jump(vector<int>& nums) 
      {
          int farthest=0;
          if (size(nums) <= 1) 
              return 0;
          int *p = new int[size(nums)];
          for (int i = 0; i < size(nums); i++) 
              p[i] = INT_MAX;
          p[0] = 0;
          for (int i = 0; i < size(nums); i++)
          {
              if ((i + nums[i] + 1) >= size(nums)) 
                  return p[i] + 1;
              else if ((i + nums[i])>farthest) 
              {
                  for (int j = farthest - i; j <= nums[i]; j++)
                  {
                      if (p[i] + 1 < p[i + j]) 
                          p[i + j] = p[i] + 1;
                  }
                  farthest = i + nums[i];
              }
          }
          return -1;
      }
  };
  ```

---

* #### 最长公共前缀：编写一个函数来查找字符串数组中的最长公共前缀，如果不存在公共前缀，返回空字符串。（leet code 14）

  最简单的办法就是循环迭代查找，该方法时间复杂度和空间复杂度均很低，而且容易想到。除此之外，还可以使用分治的方法，但是结果并没有更优秀

  ```cpp
  class Solution 
  {
  public:
      string longestCommonPrefix(vector<string>& strs) 
      {
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

* #### 删除排序数组中的重复项：给定一个排序数组，你需要在原地删除重复出现的元素，使得每个元素只出现一次，返回移除后数组的新长度。（leet code 26)


  对已经排序好的数组，我们采取双指针法，检测到不同的元素再和相同元素第二位进行互换，然后指针前进即可

  ```cpp
  class Solution {
  public:
      int removeDuplicates(vector<int>& nums) {
          if (nums.size() == 0) 
              return 0;
          int i = 0;
          for (int j = 1; j < nums.size(); j++) 
          {
              if (nums[j] != nums[i]) 
              {
                  i++;
                  nums[i] = nums[j];
              }
          }
          return i + 1;
      }
  };
  ```

---

* #### 移除元素：给定一个数组 nums 和一个值 val，你需要原地移除所有数值等于 val 的元素，返回移除后数组的新长度。不要使用额外的数组空间，你必须在原地修改输入数组并在使用 O(1) 额外空间的条件下完成。(leetcode 27)

  本题可以采取和上题类似的策略：对每个重复的元素则赋值给后面不同的元素。但是注意这里需要保证不会重复赋值，因此比较可行的方法是进行节点的交换，注意**每次把不同于val的值赋值在前列就可以完成了**

  ```cpp
  class Solution {
  public:
  	int removeElement(vector<int>& nums, int val) 
      {
          if (nums.size() == 0) 
              return 0;
          int i = 0;
          for (int j = 0; j < nums.size(); j++) 
          {
              if (nums[j] != val) 
              {
                  nums[i] = nums[j];                
                  i++;
              }
          }
          return i;
  	}
  };
  ```

  **还有没有办法优化性能呢？是有的。上述方法存在一个普遍问题：对于检索过的val的值最后还会检查一遍。但是其实是不需要检查的：我们可以将重复的值替换为末尾的值， 并且不再检查它**

  ```cpp
  class Solution {
  public:
  	int removeElement(vector<int>& nums, int val) {
          int i = 0;
          int n = nums.size();
          while (i < n) {
              if (nums[i] == val) {
                  nums[i] = nums[n - 1];
                  // reduce array size by one
                  n--;
              } else {
                  i++;
              }
          }
          return n;
  	}
  };
  ```

---

* #### N皇后问题：给定一个整数 n，返回所有不同的 n 皇后问题的解决方案。

  回溯法求解的典型问题。回溯算法就是个多叉树的遍历问题，关键就是在前序遍历和后序遍历的位置做一些操作，算法框架如下：

  def backtrack(...):
      for 选择 in 选择列表:
          做选择
          backtrack(...)
          撤销选择
  写 backtrack 函数时，需要维护走过的「路径」和当前可以做的「选择列表」，当触发「结束条件」时，将「路径」记入结果集。

  ```cpp
  class Solution 
  {
  public:
      void solve(vector<vector<string>>& res, vector<string>& tmp, vector<bool>& cols_, vector<bool>& diag1s_, vector<bool>& diag2s_, int n, int row)
      {
          if(row == n)
          {
              res.push_back(tmp);
              return;
          }
          for(auto col = 0; col < n; col++)
          {
              int ll = row + col;
              int rr = row - col + n - 1;
              if (cols_[col] && diag1s_[ll] && diag2s_[rr])
              {
                  tmp[row][col] = 'Q';
                  cols_[col] = false;
                  diag1s_[ll] = false;
                  diag2s_[rr] = false;
  
                  solve(res, tmp, cols_, diag1s_, diag2s_, n, row+1);
  
                  tmp[row][col] = '.';
                  cols_[col] = true;
                  diag1s_[ll] = true;
                  diag2s_[rr] = true;
              }
          }
      }
      
      vector<vector<string>> solveNQueens(int n) 
      {
          vector<vector<string>> res;
          vector<string> tmp(n, string(n, '.'));
          vector<bool> cols_(n, true);
          vector<bool> diag1s_(2*n-1, true);
          vector<bool> diag2s_(2*n-1, true);
          solve(res, tmp, cols_, diag1s_, diag2s_, n, 0);
          return res;
      }
      
  };
  
  
  ```

---

* #### 交错字符串：给定三个字符串 s1, s2, s3, 验证 s3 是否是由 s1 和 s2 交错组成的。

  比较容易理解的方式是采取二维数组做动态规划：dp[i][j]表示s1的子串i和s2的子串j是否可以组成s3的子串。但是这种存储方式会比较浪费。更好的方式是采取一维数组dp[i]表示s1的子串和s2的子串在长度为i的情况下是否可以组成s3的子串。但是需要考虑较多情况，比如一个字符串已经结束了等

  ```cpp
  class Solution {
  public:
      bool isInterleave(string s1, string s2, string s3) {
          const int len1=s1.size();
          const int len2=s2.size();
          const int len3=s3.size();
          if(len1 == 0) return s2 == s3;
          if(len2 == 0) return s1 == s3;
          if(len1 + len2 != len3) return false;
          vector<int> d(len1+1,0);
          for(int j=0;j<=len2;++j){
              for(int k=0;k<=len1;++k){
                  const int i = j + k;
                  if(j == 0 && k == 0){
                      d[k] = 1;
                  }else if(j == 0 && k > 0){
                      d[k] = d[k - 1] && (s3[i - 1] == s1[k - 1]);
                  }else if(k == 0 && j > 0){
                      d[k] = d[k] && s3[i - 1] == s2[j - 1];
                  }else{
                      d[k] = d[k - 1] && (s3[i - 1] == s1[k - 1]) || 
                             d[k] && (s3[i - 1] == s2[j - 1]);
                  }
              }
          }
          return d[len1];
      }
  };
  
  ```

---

### 回文问题

* #### 最长回文子串：给定一个字符串 s，找到 s 中最长的回文子串。你可以假设 s 的最大长度为 1000。(leetcode 5)

  本题至少有三种不错的做法

  - 采用动态规划求解：`P(i, j) = (P(i + 1, j − 1) and Si == Sj )`
  - 中心扩展法：回文中心的两侧互为镜像。因此，回文可以从它的中心展开，并且只有 2n - 1 个这样的中心。此处n表示插入元素间隔的个数：因为偶数回文会在字母之间。
  - [马拉车算法](https://medium.com/hackernoon/manachers-algorithm-explained-longest-palindromic-substring-22cb27a5e96f)

  ```cpp
  // 动态规划
  class Solution 
  {
  public:
      string longestPalindrome(string s) 
      {
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
  
  class Solution
  {
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
  class Solution
  {
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
  class Solution 
  {
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

### 查找元素

* #### 找指定的两数之和：给定一个整数数组 nums 和一个目标值 target，请你在该数组中找出和为目标值的那两个整数，并返回他们的数组下标。(leetcode 1)

  最蠢的是遍历，好点的是采用Map存储和查找，最佳选择为边存储边查找，下面给出后两种的解法。注意后一种改用了[unsorted_map](https://blog.csdn.net/u013354486/article/details/103327973)，会有更好的性能。

  ```cpp
  class Solution 
  {
  public:
      vector<int> twoSum(vector<int>& nums, int target) 
      {
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
  class Solution 
  {
  public:
      vector<int> twoSum(vector<int>& nums, int target) 
      {
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
class Solution 
{
  public:
    double findMedianSortedArrays(vector<int>& nums1, vector<int>& nums2) 
    {
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
class Solution 
{
public:
	vector<vector<int>> threeSum(vector<int>& nums) 
    {
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
  class Solution 
  {
  public:
      int threeSumClosest(vector<int>& nums, int target) 
      {
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

* #### 搜索旋转排序数组：假设按照升序排序的数组在预先未知的某个点上进行了旋转。( 例如，数组 [0,1,2,4,5,6,7] 可能变为 [4,5,6,7,0,1,2] )。搜索一个给定的目标值，如果数组中存在这个目标值，则返回它的索引，否则返回 -1
  
	 使用二分法解决。本题的难点在于数组进行了一次不确定位置的旋转，但是旋转后依然是两部分升序的组合，这就存在一个必然性：旋转点假设为a和b之间，则b成为了新数组第一个数，a成为了末尾数。因为b大于a和a之前的数，所以我们判断中间点如果小于b，则证明旋转后的片段小于一半，即在左边，否则在右边。



```cpp
class Solution {
public:
    int search(vector<int>& nums, int target) {
        int left = 0;
        int right = nums.size() - 1;
        int mid = left + (right-left)/2;

        while(left <= right){
            if(nums[mid] == target){
                return mid;
            }

            if(nums[left] <= nums[mid]){  //左边升序
                if(target >= nums[left] && target <= nums[mid]){//在左边范围内
                    right = mid - 1;
                }else{//只能从右边找
                    left = mid+1;
                }

            }else{ //右边升序
                if(target >= nums[mid] && target <= nums[right]){//在右边范围内
                    left = mid +1;
                }else{//只能从左边找
                    right = mid-1;
                }

            }
            mid = left + (right-left)/2;
        }

        return -1;  //没找到
    }
};
```

---

* #### 有效的数独(leet code 36)

  本题可以采用暴力遍历法，这样空间成本小，但是时间成本较高。如果作为平衡的话，采用三个数组记录每一位所在位置的值，一次遍历即可解决三个条件的判断，但是空间成本较高

  ```cpp
  class Solution 
  {
  public:
      bool isValidSudoku(vector<vector<char>>& board) 
      {
          int cols[9][9] = {0}, rows[9][9] = {0}, blocks[9][9] = {0};
          for(int i = 0; i < 9; i++)
          {
              for(int j = 0; j < 9; j++)
              {
                  if(board[i][j] != '.')
                  {
                      int data = board[i][j] - '0';
                      cols[i][data - 1]++;
                      rows[j][data - 1]++;
                      blocks[(i / 3) * 3 + j / 3][data - 1]++;
                      if(cols[i][data - 1] > 1 || rows[j][data - 1] > 1
                          || blocks[(i / 3) * 3+ j / 3][data - 1] > 1)
                      {
                          return false;
                      }
                  }
              }
          }
          return true;    
      }
  };
  ```

---

* #### 解数独：编写一个程序，通过已填充的空格来解决数独问题。

  本题主要是采取回溯法解决，选择最少空位的行、列、块，然后进行填入，如果出现问题则回溯

  ```cpp
  class Solution {
  public:
      // line, column, block 分别存储每行、每列、每宫中可用的数字
      vector<set<int>> line, column, block;
      
      //哈希更新每行/列/宫中可以使用的数字
      void update( vector<vector<char>>& board){
          set<int> compare = {1,2,3,4,5,6,7,8,9};
          //a 行；b 列；c 宫
          for( int i = 0; i < 9; i++)
              line.push_back( compare), column.push_back( compare), block.push_back( compare); 
          
          for( int i = 0; i < 9; i++)
              for( int j = 0; j < 9; j++)
                  if( board[i][j] != '.'){
                      int t = board[i][j] - '0';
                      line[i].erase( t),  column[j].erase(t), block[i / 3 + j / 3 * 3].erase(t); 
                  }
          
          return ;
      }
      
      //检查该位置处字符是否可以放到该处
      bool check( vector<vector<char>>& board, const int& i, const int& j, int num){
          if( line[i].find( num) != line[i].end()
           && column[j].find( num) != column[j].end()
           && block[i/3 + j/3*3].find( num) != block[i/3 + j/3*3].end())
              return true;
          return false;
      }
      
      //标记
      int flag = 0;
      
      //dfs + 回溯
      void dfs( vector<vector<char>>& board, int count){
          if( count == 81){
              flag = 1;
              return ;
          }
              
  
          int i = count / 9, j = count % 9;
          
          if( board[i][j] == '.'){
              //检查 1 ～ 9 中数字哪一个可以放入该位置
              for( int k = 1; k < 10; k++)
                  if( check( board, i, j, k)){
                      line[i].erase( k), column[j].erase( k), block[ i /3 + j/3*3].erase( k);
                      
                      board[i][j] = k + '0';
                      dfs( board, count + 1);
                      
                      if( !flag){
                          line[i].insert( k), column[j].insert( k), block[ i /3 + j/3*3].insert( k);
                          board[i][j] = '.';
                      }
                      else
                          return ;
                  }
          }
          else
              dfs( board, count + 1);
              
          return ;
      }
      
      void solveSudoku(vector<vector<char>>& board) {
          update( board);
          //show( line, column, block);
          dfs(board, 0);
      }
  };
      
  
  ```

---

* #### 缺失的第一个正数：给定一个未排序的整数数组，找出其中没有出现的最小的正整数(leet code 41)

  本题有一个隐藏的结论：**数组长度为N，则最小正整数一定小于等于N+1**，介于此，我们可以用数组当哈希表用来存储状态，然后据此遍历一遍即可

  ```cpp
  class Solution {
  public:
      int firstMissingPositive(vector<int>& nums) {
          int ret = 1;        
          int size = nums.size();
          int mapNums[size + 2] = {0};
  
          for (int i = 0; i < nums.size(); i++)
          {
              int tmp = nums[i];
              if (tmp < size + 2 && tmp > 0)
                  mapNums[tmp] = 1;
          }
  
          while (1)
          {
              if (mapNums[ret] == 1)
                  ret++;
              else
                  return ret;
          }
      }
  };
  
  ```

---



### 匹配问题

匹配问题包括正则表达式、括号匹配等等。

---

* #### 正则表达式匹配：给你一个字符串 s 和一个字符规律 p，请你来实现一个支持 ‘.’ 和 ‘\*’ 的正则表达式匹配。‘.’ 匹配任意单个字符，‘*’ 匹配零个或多个前面的那一个元素。所谓匹配，是要涵盖 整个 字符串 s的，而不是部分字符串。（leet code 10)
  说明:s 可能为空，且只包含从 a-z 的小写字母。p 可能为空，且只包含从 a-z 的小写字母，以及字符 . 和 *。



**采用从后向前检索的方式进行**

```cpp
class Solution 
{
public:
    vector<vector<int> > memo;
    int dfs(const string& s, const string& p, int i, int j) 
    {
        if (i == s.size()) return j == p.size() ? 1 : -1;
        if (j == p.size()) return i == s.size() ? 1 : -1;
        if (memo[i][j] != 0) return memo[i][j];
        if (j == p.size() - 1 || p[j + 1] != '*') 
        {
            if (p[j] == '.' || p[j] == s[i]) 
            {
                memo[i][j] = dfs(s, p, i + 1, j + 1);
                return memo[i][j];
            }
        } 
        else 
        {
            if (dfs(s, p, i, j + 2) > 0) 
            {
                memo[i][j] = 1;
                return memo[i][j];
            }
            if (p[j] == '.' || p[j] == s[i]) 
            {
                bool t = dfs(s, p, i + 1, j + 2) > 0 || dfs(s, p, i + 1, j) > 0;
                memo[i][j] = t ? 1 : -1;
                return memo[i][j];
            }
        }
        memo[i][j] = -1;
        return memo[i][j];
    }
    
    bool isMatch(string s, string p) 
    {
        s += '#';
        p += '#';
        memo = vector<vector<int> >(s.size(), vector<int>(p.size(), 0));
        return dfs(s, p, 0, 0) > 0;
    }
};

```

---

* #### 通配符匹配：给定一个字符串 (s) 和一个字符模式 § ，实现一个支持 ‘?’ 和 ‘`*`’ 的通配符匹配。‘?’ 可以匹配任何单个字符。‘`*`’ 可以匹配任意字符串（包括空字符串）。两个字符串完全匹配才算匹配成功。

  本题中？其实不用在意，s和p指针自增略过即可，但是`\*`需要着重考虑，因为可以代替任意长度。这里有一个容易出现的误区，如果匹配让`\*`后一个字符等于s中判断位置之后第一个值则会出现错误。所以这里需要记录星号所在位置，然后继续自增s匹配下一处相同的值

  ```cpp
  class Solution {
  public:
      bool isMatch(string s, string p) {
          int i = 0, j = 0, iStar = -1, jStar = -1, m = s.size(), n = p.size();
          while (i < m) {
              if (j < n && (s[i] == p[j] || p[j] == '?')) {
                  ++i, ++j;
              } else if (j < n && p[j] == '*') {
                  iStar = i;
                  jStar = j++;
              } else if (iStar >= 0) {
                  i = ++iStar;
                  j = jStar + 1;
              } else return false;
          }
          while (j < n && p[j] == '*') ++j;//去除多余星号
          return j == n;
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
  class Solution 
  {
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
class Solution 
{
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

* #### 最长有效括号：给定一个只包含 ‘(’ 和 ‘)’ 的字符串，找出最长的包含有效括号的子串的长度。(leet code 32)

  **本题可以采用动态规划解决，即每次向后滑动，然后若出现（）则+2， 若出现))则需要检测))之前最优子结构前是否有多的(，有的话则+2**

  ```cpp
  class Solution {
  public:
      int longestValidParentheses(string s) {
          if (s.size() == 0)
              return 0;
  
          int maxans = 0;
          int dp[s.length()] = {0};
          for (int i = 1; i < s.length(); i++) 
          {
              if (s[i] == ')') 
              {
                  if (s[i - 1] == '(') 
                  {
                      dp[i] = (i >= 2 ? dp[i - 2] : 0) + 2;
                  } else if (i - dp[i - 1] > 0 && s[i - dp[i - 1] - 1] == '(') 
                  {
                      dp[i] = dp[i - 1] + ((i - dp[i - 1]) >= 2 ? dp[i - dp[i - 1] - 2] : 0) + 2;
                  }
                  maxans = max(maxans, dp[i]);
              }
          }
          return maxans;        
      }
  };
  
  ```

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
  class Solution 
  {
  public:
      ListNode* addTwoNumbers(ListNode* l1, ListNode* l2) 
      {
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
class Solution
{
public:
    ListNode* removeNthFromEnd(ListNode* head, int n)     
    {
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

* #### k个一组翻转链表：给你一个链表，每 k 个节点一组进行翻转，请你返回翻转后的链表。k 是一个正整数，它的值小于或等于链表的长度。如果节点总数不是 k 的整数倍，那么请将最后剩余的节点保持原有顺序。
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
  class Solution 
  {
  public:
      ListNode* reverseKGroup(ListNode* head, int k) 
      {
          int d = 0;
          auto node = head;
          while (node != NULL) 
          {
              if (++d >= k) break;
              node = node->next;
          }
          if (d < k) return head;
          ListNode* prev = NULL;
          ListNode* curr = head;
          for (int i = 0; i < k; ++i) 
          {
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

* #### 旋转链表：给定一个链表，旋转链表，将链表每个节点向右移动 k 个位置，其中 k 是非负数。(leet code 61)

  本题的做法是首先迭代找到尾部，然后首尾相连成环，之后遍历K找到新的尾部断开即可

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
      ListNode* rotateRight(ListNode* head, int k) {
          if(!head||k==0)return head;
          ListNode *tail=head;
          int size=1;
          while(tail->next){
              size++;
              tail=tail->next;
          }
          if(k%size==0)return head;
          //首尾相连，形成环形链表
          tail->next=head;
          int m=size-k%size;
          //tail移动m步，到达新头节点的前驱节点
          while(m--)tail=tail->next;
          //tail的next节点为新的头节点，顺便断开环形链表
          ListNode *res=tail->next;
          tail->next=nullptr;
          return res;
      }
  };
  
  
  ```

---

堆
------

> 主要用最大最小堆，用法大同小异，列一道题足矣

* #### 数组中第k个最大元素(leet code 215)

  * 使用堆，可以使用优先队列 priority_queue
  * 维护一个 k 大小的小顶堆，堆顶就是第 k 个最大的数
  * 注意：当堆的大小已经是 k 个的时候，需要注意直接与堆顶判断决定是否加入堆中，这样效率高
    因为加入堆中，就会对堆调整，然后容量超过了再取出来，又调整一遍

  ```cpp
  int findKthLargest(vector<int>& nums, int k)
  {
      priority_queue<int, vector<int>, greater<int>> pq;
      for (auto& n : nums)
      {
          if (pq.size() >= k && pq.top() >= n) continue;
          pq.push(n);
          if (pq.size() > k)
          {
              pq.pop();
          }
      }
      return pq.top();
  }
  ```

-----



树
------

> #### 二叉树的问题主要包括树的遍历、树的构造、二叉搜索树、根据前中或者中后序还原二叉树、二叉树构造B树等。

* #### 二叉树遍历
  
  遍历包括前序中序后续，都很简单。写法包括递归和栈，递归太简单不写了，栈下所示
  
  ```cpp
      vector<int> preorderTraversal(TreeNode* root) 
      {
          stack<TreeNode*> S;
          vector<int> v;
          TreeNode* rt = root;
          while(rt || S.size())
          {
              while(rt)
              {
                  S.push(rt->right);
                  v.push_back(rt->val);
                  rt=rt->left;
              }
              rt=S.top();S.pop();
          }
          return v;        
      }
  
  
      vector<int> postorderTraversal(TreeNode* root) 
      {
          stack<TreeNode*> S;
          vector<int> v;
          TreeNode* rt = root;
          while(rt || S.size())
          {
              while(rt)
              {
                  S.push(rt->left);
                  v.push_back(rt->val);
                  rt=rt->right;
              }
              rt=S.top();S.pop();
          }
          reverse(v.begin(),v.end());
          return v;
      }
  
      vector<int> inorderTraversal(TreeNode* root) 
      {
          stack<TreeNode*> S;
          vector<int> v;
          TreeNode* rt = root;
          while(rt || S.size())
          {
              while(rt)
              {
                  S.push(rt);
                  rt=rt->left;
              }
              rt=S.top();S.pop();
              v.push_back(rt->val);
              rt=rt->right;
          }
          return v;        
      }
  
  
  ```
  
  

-----

* #### 莫里斯遍历
  
  [see this](https://www.cnblogs.com/anniekim/archive/2013/06/15/morristraversal.html)
  
  可用于恢复二叉搜索树：二叉搜索树中的两个节点被错误地交换。请在不改变其结构的情况下，恢复这棵树。（莫里斯遍历可以实现常数空间完成）

---

* #### 从前序和中序遍历构造二叉树，以及从中序和后续遍历构造二叉树

  第一问的核心思想在于前序的开始为根节点，而中序根节点左侧为左子树，因此可以递归查找：每次由前序判断根，由中序判断左右子树，然后左右子树按照这个规律继续判断，直到没有节点为止

  第二问这里注意两点：后序遍历根节点永远在最后，后序遍历从后往前是先右子树后左子树，由此可得解

  ```cpp
  /**
   * Definition for a binary tree node.
   * struct TreeNode {
   *     int val;
   *     TreeNode *left;
   *     TreeNode *right;
   *     TreeNode(int x) : val(x), left(NULL), right(NULL) {}
   * };
   */
  class Solution {
  public:
      TreeNode* buildTree(vector<int>& preorder, vector<int>& inorder) {
          int pos = 0;
          return buildTree(preorder, pos, inorder, 0, inorder.size() - 1);
      }
  
      TreeNode* buildTree(vector<int>& preorder, int& pos, vector<int>& inorder, int left, int right) 
      {
          if (pos >= preorder.size()) 
              return 0;
  
          int i = left;
  
          // 搜索到目前的根在中序的位置i
          for (; i <= right; ++i) 
          {
              if (inorder[i] == preorder[pos]) 
                  break;
          }
  
          // 当前的根为前序的pos
          TreeNode* node = new TreeNode(preorder[pos]);
  
          // 当左边只剩一个就不用递归了
          if (left <= i - 1) 
              node->left = buildTree(preorder, ++pos, inorder, left, i - 1);  // 左子树
  
          // 当右边只剩一个也是不用了
          if (i + 1 <= right) 
              node->right = buildTree(preorder, ++pos, inorder, i + 1, right);  // 右子树
  
          return node;
      }
  };
  
  
  ```

  

  ```cpp
  /**
   * Definition for a binary tree node.
   * struct TreeNode {
   *     int val;
   *     TreeNode *left;
   *     TreeNode *right;
   *     TreeNode(int x) : val(x), left(NULL), right(NULL) {}
   * };
   */
  class Solution {
  public:
      TreeNode *buildTree(vector<int>& inorder, vector<int>& postorder) {
          int pos = postorder.size() - 1;
          return buildTree(postorder, pos, inorder, 0, inorder.size() - 1);
      }
  
      TreeNode *buildTree(vector<int>& postorder, int& pos, vector<int>& inorder, int left, int right) 
      {
          if (pos >= postorder.size()) 
              return 0;
  
          int i = left;
  
          // 搜索到目前的根在中序的位置i
          for (; i <= right; ++i) 
          {
              if (inorder[i] == postorder[pos]) 
                  break;
          }
  
          // 当前的根为前序的pos
          TreeNode* node = new TreeNode(postorder[pos]);
  
          // 当右边只剩一个就不用递归了
          if (i + 1 <= right) 
              node->right = buildTree(postorder, --pos, inorder, i + 1, right);  // 右子树
  
          // 当左边只剩一个就不用递归了
          if (left <= i - 1) 
              node->left = buildTree(postorder, --pos, inorder, left, i - 1);  // 左子树
  
          return node;
      }
  
  };
  ```

  

-----

* #### 不同的二叉搜索树：给定一个整数 n，生成所有由 1 … n 为节点所组成的二叉搜索树。
  
  对于求全解，回溯法显然可解，由于此问题具有最优子结构，也可以用动态规划求解：依次求解从1到n的结果。设当前在求解k的结果，则可以设根值r为1到k分别的情况，左子树的所有可能情况在dp[r - 1]中，右子树的所有可能情况在dp[k - r]中。备注: 右子树最终拷贝的时候启示值需要从r + 1开始
  
  **另外，该种解我们称为卡特兰数**
  
  ```cpp
  /**
   * Definition for a binary tree node.
   * struct TreeNode {
   *     int val;
   *     TreeNode *left;
   *     TreeNode *right;
   *     TreeNode(int x) : val(x), left(NULL), right(NULL) {}
   * };
   */
  class Solution {
  public:
  /*  递归版本的拷贝树实现
      TreeNode *copyTree(TreeNode *root, int delta = 0) {
          auto nroot = new TreeNode(root->val + delta);
          if (root->left)
              nroot->left = copyTree(root->left, delta);
          if (root->right)
              nroot->right = copyTree(root->right, delta);
          return nroot;
      }
  */
      // 非递归版本的拷贝树实现
      TreeNode *copyTree(TreeNode *root, int delta = 0) {
          auto nroot = new TreeNode(root->val + delta);
          queue<TreeNode*> qt;
          queue<TreeNode*> qo;
          qo.push(root);
          qt.push(nroot);
          while (!qt.empty()) {
              auto o_root = qo.front();
              qo.pop();
              auto t_root = qt.front();
              qt.pop();
              if (o_root->left) {
                  t_root->left = new TreeNode(o_root->left->val + delta);
                  qo.push(o_root->left);
                  qt.push(t_root->left);
              }
              if (o_root->right) {
                  t_root->right = new TreeNode(o_root->right->val + delta);
                  qo.push(o_root->right);
                  qt.push(t_root->right);
              }
          }
          return nroot;
      }
  
      vector<TreeNode*> generateTrees(int n) {
          // 使用static变量，节省用例不同用例执行时的重复求解开销
          static vector<vector<TreeNode*>> dp(1, vector<TreeNode*>(1, NULL));
          int c_size = n + 1; 
          int o_size = dp.size();
          if (c_size > dp.size())
              dp.resize(c_size);
  
          for (int i = o_size; i <= n; i++) { // 升序求解
              for (int j = 1; j <= i; j++) { // 遍历以不同的数的为根
                  const auto &left = dp[j - 1];
                  const auto &right = dp[i - j];
                  auto &cc = dp[i];
                  for (const auto left_ptr : left) // 遍历所有可能的左子树
                      for (const auto right_ptr : right) { // 遍历所有可能的右子树
                          auto root = new TreeNode(j);
                          if (j > 1)
                              root->left = copyTree(left_ptr); // 拷贝左子树
                          if (i > j)
                              root->right = copyTree(right_ptr, j); // 拷贝右子树，并加上偏移值
                          cc.push_back(root);
                      }
              }
          }
          if (n == 0)
              return {};
          return dp[n];
      }
  };
  
  
  ```
  
  

---

* #### 验证二叉搜索树：给定一个二叉树，判断其是否是一个有效的二叉搜索树。

  作为树的遍历，可以采取前序、中序、后序，可以用递归也可以用堆栈，其实都差不多。引入上下边界

  * 对于树的每个节点 val ，设其上下边界 low , high。(用 long 防止 INT_MAX 溢出 )
  * 判断根结点时，须满足 low < val < high ，否则返回 false
  * 判断左节点时，仅 上界 变化 ( 新上界为 high 与 val 较小值。又因 val 必小于 high，故新上界为 val )
  * 判断右节点时，仅 下界 变化 ( 同理，新下界为 val )

  ```cpp
  /**
   * Definition for a binary tree node.
   * struct TreeNode {
   *     int val;
   *     TreeNode *left;
   *     TreeNode *right;
   *     TreeNode(int x) : val(x), left(NULL), right(NULL) {}
   * };
   */
  class Solution 
  {
  public:
      bool fun(struct TreeNode* root, long low, long high) 
      {
          if (root == NULL) 
              return true;
          long num = root->val;
          if (num <= low || num >= high) 
              return false;
          return fun(root->left, low, num) && fun(root->right, num, high);
      }
      
      bool isValidBST(struct TreeNode* root)
      {
          return fun(root, LONG_MIN, LONG_MAX);
      }
  
  };
  
  ```

---



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
---

排序算法
------

# 一、分类与性能总结

![img](https:////upload-images.jianshu.io/upload_images/2240277-e008c47f06d3de93?imageMogr2/auto-orient/strip|imageView2/2/w/695/format/webp)

在本文中对归并排序和基数排序暂不介绍，有兴趣的可以查看本文最后参考资料中的[1]，里面有对这两个算法的详细解释。

# 二、详细算法介绍

## 1 直接插入排序

### 1.1 算法思想

直接插入排序的核心思想就是：**将数组中的所有元素依次跟前面已经排好的元素相比较，如果选择的元素比已排序的元素小，则交换，直到全部元素都比较过。**

![img](https:////upload-images.jianshu.io/upload_images/1156494-936d9f02b6aac880.gif?imageMogr2/auto-orient/strip|imageView2/2/w/594/format/webp)


 因此，从上面的描述中我们可以发现，直接插入排序可以用两个循环完成：



1. 第一层循环：遍历待比较的所有数组元素
2. 第二层循环：将本轮选择的元素(selected)与已经排好序的元素(ordered)相比较。如果：selected > ordered，那么将二者交换。

### 1.2 代码实现



```python
# 1. 直接插入排序
def insert_sort(L):
    # 遍历数组中的所有元素，其中0号索引元素默认已排序，因此从1开始
    for i in range(1,len(L)):
        # range(x-1,-1,-1):从x-1倒序循环到0
        for j in range(i-1,-1,-1):
            # 将该元素与已排序好的前序数组依次比较，如果该元素小，则交换
            if L[j]>L[j+1]:
                L[j], L[j+1] = L[j+1], L[j]
    return L
```

## 2 希尔排序

### 2.1 算法思想

希尔排序的核心思想就是：**将待排序数组按照步长gap进行分组，然后将每组的元素利用直接插入排序的方法进行排序；每次将gap折半减小，循环上述操作；当gap=1时，利用直接插入，完成排序。**

![img](https:////upload-images.jianshu.io/upload_images/10390594-bba96b5fbfddeb44.png?imageMogr2/auto-orient/strip|imageView2/2/w/1200/format/webp)


 因此，从上面的描述中我们可以发现，希尔排序可以用三个循环完成：



1. 第一层循环：将gap依次折半，对序列进行分组，直到gap=1
2. 第二、三层循环：也即直接插入排序所需要的两次循环。具体描述见上。

### 1.2 代码实现



```python
# 2. 希尔排序
def shell_sort(L):
    # 初始化gap值，此处利用序列长度的一半为其赋值
    gap = len(L) // 2
    # 第一层循环：依次改变gap值对列表进行分组
    while(gap >= 1):
        # 下面：利用直接插入排序的思想对分组数据进行排序，注意分组后下标与gap有关
        for i in range(gap,len(L)):
            for j in range(i-gap,-1,-gap):
                if L[j]>L[j+1]:
                    L[j], L[j + 1] = L[j + 1], L[j]
        # while循环条件折半
        gap = gap // 2
    return L
```

## 3 简单选择排序

### 3.1 算法思想

简单选择排序的核心思想就是：**比较+交换**。
 **1. 从待排序序列中，找到关键字最小的元素；**
 **2. 如果最小元素不是待排序序列的第一个元素，将其和第一个元素互换；**
 **3. 从余下的 N - 1 个元素中，找出关键字最小的元素，重复(1)、(2)步，直到排序结束。**
 因此，从上面的描述中我们可以发现，简单选择排序可以用两个循环完成：

1. 第一层循环：依次遍历序列当中的每一个元素
2. 第二层循环：将遍历得到的当前元素依次与余下的元素进行比较，符合最小元素的条件，则交换。

### 3.2 代码实现



```python
# 3. 简单选择排序
def select_sort(L):
    # 依次遍历序列中的每一个元素
    for i in range(0,len(L)):
        # 将当前位置的元素定义此轮循环当中的最小值
        min = L[i]
        # 将该元素与剩下的元素依次比较寻找最小元素
        for j in range(i+1,len(L)):
            if L[j] < min:
                L[j], min = min, L[j]
        # 将比较后得到的真正的最小值赋值给当前位置
        L[i] = min
    return L
```

## 4 堆排序

### 4.1 算法思想

**堆**：本质是一种数组对象。特别重要的一点性质：**任意的叶子节点小于（或大于）它所有的父节点**。对此，又分为大顶堆和小顶堆，大顶堆要求节点的元素都要大于其孩子，小顶堆要求节点元素都小于其左右孩子，两者对左右孩子的大小关系不做任何要求。

利用堆排序，就是基于大顶堆或者小顶堆的一种排序方法。下面，我们通过大顶堆来实现。

堆排序的核心思想就是：
 **1.首先将序列构建称为大顶堆；**

![img](https:////upload-images.jianshu.io/upload_images/1156494-596eee6397817ca2.png?imageMogr2/auto-orient/strip|imageView2/2/w/664/format/webp)

这样满足了大顶堆那条性质：位于根节点的元素一定是当前序列的最大值


**2. 取出当前大顶堆的根节点，将其与序列末尾元素进行交换；**

![img](https:////upload-images.jianshu.io/upload_images/1156494-7e5c63ce1ed48ebf.png?imageMogr2/auto-orient/strip|imageView2/2/w/658/format/webp)

此时序列末尾的元素为已排序的最大值；由于交换了元素，当前位于根节点的堆并不一定满足大顶堆的性质


**3. 对交换后的n-1个序列元素进行调整，使其满足大顶堆的性质**
**4. 重复2.3步骤，直至堆中只有1个元素为止**



### 4.2 代码实现



```python
# 4. 堆排序
def heap_adjust(L, start, end):
# start为当前需要调整最大堆的位置，end为调整边界
    root = start
    # 执行循环操作：两个任务：1 寻找最大值的下标；2.最大值与父节点交换
    while True:
        child = root * 2 + 1
        if child > end:
            break
        # 取较大的子节点
        if child + 1 <= end and L[child+1] > L[child]:
            child += 1
        # 较大的子节点成为父节点
        if L[child] > L[root]:
            L[child], L[root] = L[root], L[child]
            root = child
        else:
            break

def heap_sort(L):
    # 先建立大顶堆，保证最大值位于根节点；并且父节点的值大于叶子结点，从最后一个父节点开始逆序向前循环
    for start in range(len(L)//2-1, -1, -1):
        heap_adjust(L, start, len(L)-1)
    # 执行循环：1.每次取出堆顶元素置于序列的最后    2.调整堆，使其继续满足大顶堆的性质
    for end in range(len(L)-1, 0, -1):
        L[0], L[end] = L[end], L[0]
        heap_adjust(L, 0, end-1)
    return L
```

## 5 冒泡排序

### 5.1 算法思想

冒泡排序的思路比较简单，其核心思想为：
 **1. 将序列当中的左右元素，依次比较，保证右边的元素始终大于左边的元素；**
 **（ 第一轮结束后，序列最后一个元素一定是当前序列的最大值；）**
 **2. 对序列当中剩下的n-1个元素再次执行步骤1。**
 **3. 对于长度为n的序列，一共需要执行n-1轮比较**
 **（利用while循环可以减少执行次数）**

![img](https:////upload-images.jianshu.io/upload_images/1156494-fef2b2e3edc03289.gif?imageMogr2/auto-orient/strip|imageView2/2/w/600/format/webp)



### 5.2 代码实现



```python
# 5. 冒泡排序
def bubble_sort(L):
    # 序列长度为length，需要执行length-1轮交换
    for i in range(1,len(L)):
        # 对于每一轮交换，都将序列当中的左右元素进行比较
        # 每轮交换当中，由于序列最后的元素一定是最大的，因此每轮循环到序列未排序的位置即可
        for j in range(0, len(L)-i):
            if L[j]>L[j+1]:
                L[j], L[j+1] = L[j+1], L[j]
    return L
```

## 6 快速排序

### 6.1 算法思想

快速排序的核心思想为：**挖坑填数+分治法**
 **1. 从序列当中选择一个基准数(pivot)**
 **（ 在这里我们选择序列当中第一个数最为基准数）**
 **2. 将序列当中的所有数依次遍历，比基准数大的位于其右侧，比基准数小的位于其左侧。**
 **3. 重复步骤1.2，直到所有子集当中只有一个元素为止。**

![img](https:////upload-images.jianshu.io/upload_images/1156494-2d150e5550b700fa.gif?imageMogr2/auto-orient/strip|imageView2/2/w/600/format/webp)


 上述过程可用伪代码表示如下：
**1. i =L; j = R; 将基准数挖出形成第一个坑a[i]。**
**2. j--由后向前找比它小的数，找到后挖出此数填前一个坑a[i]中。**
**3. i++由前向后找比它大的数，找到后也挖出此数填到前一个坑a[j]中。**
**4. 再重复执行2，3二步，直到i==j，将基准数填入a[i]中**



### 6.2 代码实现

```python
# 6. 快速排序
def quick_sort(L, start, end):
# L：待排序的序列；start排序的开始index,end序列末尾的index
# 对于长度为length的序列：start = 0;end = length-1
    if start < end :
        i, j, pivot = start, end, L[start]
        while i < j :
            # 从右开始向左寻找第一个小于pivot的值
            while L[j] > pivot and i < j :
                j -= 1
            # 从左开始向右寻找第一个大于pivot的值
            while L[i] < pivot and i < j :
                i += 1
            # 交换两者的位置
            L[i], L[j] = L[j], L[i]
        # 循环结束后，说明 i=j，此时左边的值全都小于pivot,右边的值全都大于pivot
        # pivot的位置移动正确，那么此时只需对左右两侧的序列调用此函数进一步排序即可
        # 递归调用函数：依次对左侧序列：从0 ~ i-1//右侧序列：从i+1 ~ end
        # 左侧序列继续排序
        quick_sort(L, start, i-1)
        # 右侧序列继续排序
        quick_sort(L, i+1, end)
    return L
```



-----



大数查找
------

> 所谓海量数据处理，无非就是基于海量数据上的存储、处理、操作。
> 何谓海量，就是数据量太大，所以导致要么是无法在较短时间内迅速解决，要么是数据太大，导致无法一次性装入内存。
>
> 那解决办法呢？
>
> 针对时间，我们可以采用巧妙的算法搭配合适的数据结构，如Bloom filter/Hash/bit-map/堆/数据库或倒排索引/trie树。
>
> 针对空间，无非就一个办法：大而化小，分而治之（hash映射），你不是说规模太大嘛，那简单啊，就把规模大化为规模小的，各个击破不就完了嘛。
>
> 至于所谓的单机及集群问题，通俗点来讲，单机就是处理装载数据的机器有限(只要考虑cpu，内存，硬盘的数据交互)，而集群，机器有多辆，适合分布式处理，并行计算(更多考虑节点和节点间的数据交互)。

### 分而治之/hash映射 + hash统计 + 堆/快速/归并排序

- 分而治之/hash映射：针对数据太大，内存受限，只能把大文件化成(取模映射)小文件。
- hash_map统计：当大文件转化了小文件，那么我们便可以采用常规的hash_map(key，value)来进行频率统计。
- 堆/快速排序：统计完了之后，便进行排序(可采取堆排序)，得到次数最多的key。

#### 多层划分

多层划分，其实本质上还是分而治之的思想，重在“分”的技巧上！

- 适用范围：第k大，中位数，不重复或重复的数字。
- 基本原理及要点：因为元素范围很大，不能利用直接寻址表，所以通过多次划分，逐步确定范围，然后最后在一个可以接受的范围内进行。

#### Bloom filter/Bitmap

- Bloom filter

  适用范围：可以用来实现数据字典，进行数据的判重，或者集合求交集。

  基本原理：当一个元素被加入集合时，通过K个Hash函数将这个元素映射成一个位阵列（Bit array）中的K个点，把它们置为1。检索时，我们只要看看这些点是不是都是1就（大约）知道集合中有没有它了：如果这些点有任何一个0，则被检索元素一定不在；如果都是1，则被检索元素很可能在。

  Bloom Filter的这种高效是有一定代价的：在判断一个元素是否属于某个集合时，有可能会把不属于这个集合的元素误认为属于这个集合（false positive）。因此，Bloom Filter不适合那些“零错误”的应用场合。而在能容忍低错误率的应用场合下，Bloom Filter通过极少的错误换取了存储空间的极大节省。

- Bitmap

  Bitmap就是用一个bit位来标记某个元素对应的Value， 而Key即是该元素。由于采用了Bit为单位来表示某个元素是否存在，因此在存储空间方面，可以大大节省。

  Bitmap排序方法
  第一步，将所有的位都置为0，从而将集合初始化为空。
  第二步，通过读入文件中的每个整数来建立集合，将每个对应的位都置为1。
  第三步，检验每一位，如果该位为1，就输出对应的整数。

Bloom filter可以看做是对Bitmap的扩展。

#### Trie树/数据库/倒排索引

- Trie树
  适用范围：数据量大，重复多，但是数据种类小可以放入内存。
  基本原理及要点：实现方式，节点孩子的表示方式。
  扩展：压缩实现。
- 数据库索引
  适用范围：大数据量的增删改查。
  基本原理及要点：利用数据的设计实现方法，对海量数据的增删改查进行处理。
- 倒排索引(Inverted index)
  适用范围：搜索引擎，关键字查询。
  基本原理及要点：一种索引方法，被用来存储在全文搜索下某个单词在一个文档或者一组文档中的存储位置的映射。

#### 外排序

- 适用范围：大数据的排序，去重。
- 基本原理及要点：外排序的归并方法，置换选择败者树原理，最优归并树。

#### Map Reduce

Map Reduce是一种计算模型，简单的说就是将大批量的工作（数据）分解（MAP）执行，然后再将结果合并成最终结果（REDUCE）。这样做的好处是可以在任务被分解后，可以通过大量机器进行并行计算，减少整个操作的时间。

- 适用范围：数据量大，但是数据种类小可以放入内存。
- 基本原理及要点：将数据交给不同的机器去处理，数据划分，结果归约。

---

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

* #### 给定a、b两个文件，各存放50亿个url，每个url各占64字节，内存限制是4G，让你找出a、b文件共同的url？
  
  方案1：可以估计每个文件安的大小为50G×64=320G，远远大于内存限制的4G。所以不可能将其完全加载到内存中处理。考虑采取分而治之的方法。
  
  s 遍历文件a，对每个url求取[![clip_image002](https://images.cnblogs.com/cnblogs_com/youwang/WindowsLiveWriter/1340f2e6fa8e_D5D1/clip_image002_thumb.png)](http://images.cnblogs.com/cnblogs_com/youwang/WindowsLiveWriter/1340f2e6fa8e_D5D1/clip_image002_2.png)，然后根据所取得的值将url分别存储到1000个小文件（记为[![clip_image004](https://images.cnblogs.com/cnblogs_com/youwang/WindowsLiveWriter/1340f2e6fa8e_D5D1/clip_image004_thumb.png)](http://images.cnblogs.com/cnblogs_com/youwang/WindowsLiveWriter/1340f2e6fa8e_D5D1/clip_image004_2.png)）中。这样每个小文件的大约为300M。
  
  s 遍历文件b，采取和a相同的方式将url分别存储到1000各小文件（记为[![clip_image006](https://images.cnblogs.com/cnblogs_com/youwang/WindowsLiveWriter/1340f2e6fa8e_D5D1/clip_image006_thumb.png)](http://images.cnblogs.com/cnblogs_com/youwang/WindowsLiveWriter/1340f2e6fa8e_D5D1/clip_image006_2.png)）。这样处理后，所有可能相同的url都在对应的小文件（[![clip_image008](https://images.cnblogs.com/cnblogs_com/youwang/WindowsLiveWriter/1340f2e6fa8e_D5D1/clip_image008_thumb.png)](http://images.cnblogs.com/cnblogs_com/youwang/WindowsLiveWriter/1340f2e6fa8e_D5D1/clip_image008_2.png)）中，不对应的小文件不可能有相同的url。然后我们只要求出1000对小文件中相同的url即可。
  
  s 求每对小文件中相同的url时，可以把其中一个小文件的url存储到hash_set中。然后遍历另一个小文件的每个url，看其是否在刚才构建的hash_set中，如果是，那么就是共同的url，存到文件里面就可以了。
  
  方案2：如果允许有一定的错误率，可以使用Bloom filter，4G内存大概可以表示340亿bit。将其中一个文件中的url使用Bloom filter映射为这340亿bit，然后挨个读取另外一个文件的url，检查是否与Bloom filter，如果是，那么该url应该是共同的url（注意会有一定的错误率）。

-----

* #### 有10个文件，每个文件1G，每个文件的每一行存放的都是用户的query，每个文件的query都可能重复。要求你按照query的频度排序

  方案1：

  s 顺序读取10个文件，按照hash(query)%10的结果将query写入到另外10个文件（记为[![clip_image010](https://images.cnblogs.com/cnblogs_com/youwang/WindowsLiveWriter/1340f2e6fa8e_D5D1/clip_image010_thumb.png)](http://images.cnblogs.com/cnblogs_com/youwang/WindowsLiveWriter/1340f2e6fa8e_D5D1/clip_image010_3.png)）中。这样新生成的文件每个的大小大约也1G（假设hash函数是随机的）。

  s 找一台内存在2G左右的机器，依次对这十个文件用hash_map(query, query_count)来统计每个query出现的次数。利用快速/堆/归并排序按照出现次数进行排序。将排序好的query和对应的query_cout输出到文件中。这样得到了10个排好序的文件（记为[![clip_image012](https://images.cnblogs.com/cnblogs_com/youwang/WindowsLiveWriter/1340f2e6fa8e_D5D1/clip_image012_thumb.png)](http://images.cnblogs.com/cnblogs_com/youwang/WindowsLiveWriter/1340f2e6fa8e_D5D1/clip_image012_3.png)）。

  s 对这10个文件进行归并排序（内排序与外排序相结合）。

  方案2：

  一般query的总量是有限的，只是重复的次数比较多而已，可能对于所有的query，一次性就可以加入到内存了。这样，我们就可以采用trie树/hash_map等直接来统计每个query出现的次数，然后按出现次数做快速/堆/归并排序就可以了。

  方案3：

  与方案1类似，但在做完hash，分成多个文件后，可以交给多个文件来处理，采用分布式的架构来处理（比如MapReduce），最后再进行合并。

-----

* #### 有一个1G大小的一个文件，里面每一行是一个词，词的大小不超过16字节，内存限制大小是1M。返回频数最高的100个词

  方案1：顺序读文件中，对于每个词x，取[![clip_image014](https://images.cnblogs.com/cnblogs_com/youwang/WindowsLiveWriter/1340f2e6fa8e_D5D1/clip_image014_thumb.png)](http://images.cnblogs.com/cnblogs_com/youwang/WindowsLiveWriter/1340f2e6fa8e_D5D1/clip_image014_2.png)，然后按照该值存到5000个小文件（记为[![clip_image016](https://images.cnblogs.com/cnblogs_com/youwang/WindowsLiveWriter/1340f2e6fa8e_D5D1/clip_image016_thumb.png)](http://images.cnblogs.com/cnblogs_com/youwang/WindowsLiveWriter/1340f2e6fa8e_D5D1/clip_image016_2.png)）中。这样每个文件大概是200k左右。如果其中的有的文件超过了1M大小，还可以按照类似的方法继续往下分，知道分解得到的小文件的大小都不超过1M。对每个小文件，统计每个文件中出现的词以及相应的频率（可以采用trie树/hash_map等），并取出出现频率最大的100个词（可以用含100个结点的最小堆），并把100词及相应的频率存入文件，这样又得到了5000个文件。下一步就是把这5000个文件进行归并（类似与归并排序）的过程了。

---

* #### 海量日志数据，提取出某日访问百度次数最多的那个IP。

  方案1：首先是这一天，并且是访问百度的日志中的IP取出来，逐个写入到一个大文件中。注意到IP是32位的，最多有[![clip_image018](https://images.cnblogs.com/cnblogs_com/youwang/WindowsLiveWriter/1340f2e6fa8e_D5D1/clip_image018_thumb.png)](http://images.cnblogs.com/cnblogs_com/youwang/WindowsLiveWriter/1340f2e6fa8e_D5D1/clip_image018_3.png)个IP。同样可以采用映射的方法，比如模1000，把整个大文件映射为1000个小文件，再找出每个小文中出现频率最大的IP（可以采用hash_map进行频率统计，然后再找出频率最大的几个）及相应的频率。然后再在这1000个最大的IP中，找出那个频率最大的IP，即为所求。

---

* ##### 在2.5亿个整数中找出不重复的整数，内存不足以容纳这2.5亿个整数。

  方案1：采用2-Bitmap（每个数分配2bit，00表示不存在，01表示出现一次，10表示多次，11无意义）进行，共需内存[![clip_image020](https://images.cnblogs.com/cnblogs_com/youwang/WindowsLiveWriter/1340f2e6fa8e_D5D1/clip_image020_thumb.png)](http://images.cnblogs.com/cnblogs_com/youwang/WindowsLiveWriter/1340f2e6fa8e_D5D1/clip_image020_2.png)内存，还可以接受。然后扫描这2.5亿个整数，查看Bitmap中相对应位，如果是00变01，01变10，10保持不变。所描完事后，查看bitmap，把对应位是01的整数输出即可。

  方案2：也可采用上题类似的方法，进行划分小文件的方法。然后在小文件中找出不重复的整数，并排序。然后再进行归并，注意去除重复的元素。

---

* #### 海量数据分布在100台电脑中，想个办法高校统计出这批数据的TOP10。

  方案1：

  s 在每台电脑上求出TOP10，可以采用包含10个元素的堆完成（TOP10小，用最大堆，TOP10大，用最小堆）。比如求TOP10大，我们首先取前10个元素调整成最小堆，如果发现，然后扫描后面的数据，并与堆顶元素比较，如果比堆顶元素大，那么用该元素替换堆顶，然后再调整为最小堆。最后堆中的元素就是TOP10大。

  s 求出每台电脑上的TOP10后，然后把这100台电脑上的TOP10组合起来，共1000个数据，再利用上面类似的方法求出TOP10就可以了。

---

* #### 怎么在海量数据中找出重复次数最多的一个？

  方案1：先做hash，然后求模映射为小文件，求出每个小文件中重复次数最多的一个，并记录重复次数。然后找出上一步求出的数据中重复次数最多的一个就是所求（具体参考前面的题）。

---

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
  
  ```cpp
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
