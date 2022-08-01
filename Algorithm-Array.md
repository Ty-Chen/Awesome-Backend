# 数组和字符串

数组的问题通常包括子串问题、回文问题、查找元素、匹配问题，**通常其解法为双指针滑动、回溯、动态规划、堆栈**。

### 一. 子串问题

> **核心解法** 子串问题分为单数组求子串以及给两个数组比较求子串。其核心思想均为动态规划：双数组类似于二维动态规划，一定可以降维为一维，而单数组则可以考虑为一维动态规划，降维为双指针滑动。对于更多个数组的比较，一般还是循环迭代求解吧。

#### 1. 滑动指针

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

* #### [盛水最多的容器](https://leetcode.cn/problems/container-with-most-water/solution/)：给定 n 个非负整数 a1，a2，…，an，每个数代表坐标中的一个点 (i, ai) 。在坐标内画 n 条垂直线，垂直线 i 的两个端点分别为 (i, ai) 和 (i, 0)。找出其中的两条线，使得它们与 x 轴共同构成的容器可以容纳最多的水。（leet code 11)

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

- #### [合并区间](https://leetcode.cn/problems/merge-intervals/)：以数组 intervals 表示若干个区间的集合，其中单个区间为 intervals[i] = [starti, endi] 。请你合并所有重叠的区间，并返回 一个不重叠的区间数组，该数组需恰好覆盖输入中的所有区间 。

  本题有两种思路：第一种是先对区间排序，然后逐个判断；第二种是将所有的区间映射到“线段”上，再输出“线段”即可

  ```c
  class Solution {
  public:
      vector<vector<int>> merge(vector<vector<int>>& intervals) {
          vector<vector<int>> res;
          if(intervals.empty()) return res;
  
          auto cmp = [](vector<int> &a, vector<int> &b) {return a[0] < b[0];};
          sort(intervals.begin(), intervals.end(), cmp);
  
          res.emplace_back(intervals[0]);
          for(int i = 1; i < intervals.size(); i++) {
              int bk = res.size() - 1;
              if(res[bk][1] >= intervals[i][0]) {
                  if(res[bk][1] < intervals[i][1]) {
                      res[bk][1] = intervals[i][1];
                  } 
              } else {
                  res.emplace_back(intervals[i]);
              }
          }
  
          return res; 
      }
  };
  ```

---

- #### [插入区间](https://leetcode.cn/problems/insert-interval/)：给你一个 **无重叠的** *，*按照区间起始端点排序的区间列表。在列表中插入一个新的区间，你需要确保列表中的区间仍然有序且不重叠（如果有必要的话，可以合并区间）。

  一次遍历然后按情况插入新区间

  ```c
  class Solution {
  public:
      vector<vector<int>> insert(vector<vector<int>>& intervals, vector<int>& newInterval) {
  		intervals.push_back(newInterval);
          sort(intervals.begin(), intervals.end());
          vector<vector<int>> ans{intervals[0]};
          int n = intervals.size();
          for(int i = 0; i < n; i ++){
              if(ans.back()[1] >= intervals[i][0]){
                  ans.back()[1] = max(intervals[i][1], ans.back()[1]);
                  continue;
              }
              else{
                  ans.push_back(intervals[i]);
                  continue;
              }
          }
          return ans;
          
      }
  };
  ```



---

#### 2. 动态规划

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

* #### 串联所有单词的子串：给定一个字符串 **s** 和一些长度相同的单词 **words。**找出 **s** 中恰好可以由 **words** 中所有单词串联形成的子串的起始位置。注意子串要与 **words** 中的单词完全匹配，中间不能有其他字符，但不需要考虑 **words** 中单词串联的顺序。（leetcode 30）


* 本题主要在于关注以下几点：
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

- #### [解码方法](https://leetcode.cn/problems/decode-ways/)：给你一个只含数字的 **非空** 字符串 `s` ，请计算并返回 **解码** 方法的 **总数** 。

  解码其实就是将A-Z翻译成1到26，但是因为两位数可以翻译成一个字母或者两个字母，有两种可能性。因此本题很明显可以用动态规划求解。数字小于26大于0，则dp[i] = dp[i - 1] + dp[i - 2]，否则dp[i] = dp[i - 2]

  ```c
  class Solution {
  public:
  int numDecodings(string s) {
      if (s[0] == '0') return 0;
      int pre = 1, curr = 1;//dp[-1] = dp[0] = 1
      for (int i = 1; i < s.size(); i++) {
          int tmp = curr;
          if (s[i] == '0')
              if (s[i - 1] == '1' || s[i - 1] == '2') curr = pre;
              else return 0;
          else if (s[i - 1] == '1' || (s[i - 1] == '2' && s[i] >= '1' && s[i] <= '6'))
              curr = curr + pre;
          pre = tmp;
      }
      return curr;
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

### 二. 回文问题

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

* #### [回文数](https://leetcode.cn/problems/palindrome-number/)。回文数是指正序（从左向右）和倒序（从右向左）读都是一样的整数。（leetcode 9)

  本题有两种做法：1.转化为字符串再进行检索 2. 获取一半的整数和另一半比较。第二种空间使用更少，更优。

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

### 三. 查找元素

* #### [两数之和](https://leetcode.cn/problems/two-sum/)：给定一个整数数组 nums 和一个目标值 target，请你在该数组中找出和为目标值的那两个整数，并返回他们的数组下标。(leetcode 1)

  最蠢的是遍历，好点的是采用Map存储和查找，最佳选择为边存储边查找，下面给出后两种的解法。注意后一种改用了unsorted_map，会有更好的性能。

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

---

- #### [三数之和](https://leetcode.cn/problems/3sum/)：给你一个包含 n 个整数的数组 nums，判断 nums 中是否存在三个元素 a，b，c ，使得 a + b + c = 0 ？请你找出所有和为 0 且不重复的三元组。

  通过哈希或者排序，记录下有哪些数字，然后双指针滑动去看是否存在值和两数之和相等。可以排序之后从首尾开始判断并滑动得出结果即可。

  ```c
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

- #### [最接近的三数之和](https://leetcode.cn/problems/3sum-closest/)：给你一个长度为 `n` 的整数数组 `nums` 和 一个目标值 `target`。请你从 `nums` 中选出三个整数，使它们的和与 `target` 最接近。返回这三个数的和。

  和上题类似，其区别在于是近似而非精确取值，这里用一个绝对值去计算gap。

  ```c
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

- #### [四数之和](https://leetcode.cn/problems/4sum/)：给你一个由 `n` 个整数组成的数组 `nums` ，和一个目标值 `target` 。请你找出并返回满足下述全部条件且**不重复**的四元组。

  一样的配方一样的解法。

  ```c
  class Solution {
  public:
      vector<vector<int>> fourSum(vector<int>& nums, int target) {
          if (nums.size() < 4) return {};
          sort(nums.begin(), nums.end());
          int N = nums.size();
          int maxSum3 = nums[N - 3] + nums[N - 2] + nums[N - 1];
          int maxSum2 = nums[N - 2] + nums[N - 1];
          vector<vector<int> > res;
          for (int i = 0; i < N - 3; ++i) {
              if (4 * nums[i] > target) break;
              if (i > 0 && nums[i] == nums[i - 1]) continue;
              if (nums[i] + maxSum3 < target) continue;
              for (int j = i + 1;j < N - 2; ++j) {
                  if (2 * (nums[i] + nums[j]) > target) break;
                  if (j > i + 1 && nums[j - 1] == nums[j]) continue;
                  if (nums[i] + nums[j] + maxSum2 < target) continue;
                  int t = target - nums[i] - nums[j];
                  int l = j + 1;
                  int r = N - 1;
                  while (l < r) {
                      if (nums[l] + nums[r] > t) {
                          --r;
                      } else if (nums[l] + nums[r] < t) {
                          ++l;
                      } else {
                          res.push_back({nums[i], nums[j], nums[l], nums[r]});
                          while (l < r && nums[l] == nums[++l]);
                          while (l < r && nums[r] == nums[--r]);
                      }
                  }
              }
          }
          return res;
      }
  };
  ```

---

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

- #### [在排序数组中查找元素的第一个和最后一个位置](https://leetcode.cn/problems/find-first-and-last-position-of-element-in-sorted-array/)：给你一个按照非递减顺序排列的整数数组 `nums`，和一个目标值 `target`。请你找出给定目标值在数组中的开始位置和结束位置。

  二分法解题

  ```c
  class Solution {
  public:
      vector<int> searchRange(vector<int>& nums, int target) {
  
          if (nums.size() == 0)
              return {-1, -1};
  
          int begin = 0, end = nums.size() - 1;
  
          if (nums[begin] > target || nums[end] < target)
              return {-1, -1};
  
          while (begin <= end)
          {
              if (nums[begin] == target && nums[end] == target)
              {
                  vector<int> ret = {begin, end};
                  return ret;
              }
  
              int mid = (begin + end) / 2; 
              if (nums[mid] < target)
                  begin = mid + 1;
              else if (nums[mid] > target)
                  end = mid - 1;
              else
              {
                  begin = mid;
                  end = mid;
                  while (begin >= 0 && nums[begin] == target)
                  {
                      begin--;
                  }
                  while (end < nums.size() && nums[end] == target)
                  {
                      end++;
                  }
                  vector<int> ret = {begin + 1, end - 1};
                  return ret;               
              }
  
              if (nums[begin] > target || nums[end] < target)
              {
                  return {-1, -1};
              }
          }
  
          return {-1, -1};
      }
  };
  
  ```

---

- #### [搜索插入位置](https://leetcode.cn/problems/search-insert-position/)：给定一个排序数组和一个目标值，在数组中找到目标值，并返回其索引。如果目标值不存在于数组中，返回它将会被按顺序插入的位置。

  二分法解题

  ```c
  class Solution {
  public:
      int searchInsert(vector<int>& nums, int target) {
          if (nums.size() == 0 || nums[0] > target)
              return 0;
          
          int begin = 0, end = nums.size() - 1;
          if (nums[end] < target)
              return end + 1;
  
          while (begin <= end)
          {
              int mid = (begin + end) / 2;
              if (nums[mid] == target)
                  return mid;
              else if (nums[mid] < target)
                  begin = mid + 1;
              else 
                  end = mid - 1;
          }
          
          return nums[begin] > target ? begin : begin + 1;
      }
  };
  ```

---

### 四. 数组操作

数组操作通常会进行反转、部分的增删或者替换等，通常较为简单，但是需要注意边界条件。除此之外还有一类数组映射题，这一类一般BFS或DFS处理。

- #### [整数反转](https://leetcode.cn/problems/reverse-integer/)：给出一个 32 位的有符号整数，你需要将这个整数中每位上的数字进行反转。（leetcode 7）**

- 本题主要需要考虑边界条件：判断反转后的数字是否超过 3232 位有符号整数的范围

  ```c
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

---

- #### [字符串转换整数 (atoi)](https://leetcode.cn/problems/string-to-integer-atoi/)：请你来实现一个 `myAtoi(string s)` 函数，使其能将字符串转换成一个 32 位有符号整数（类似 C/C++ 中的 `atoi` 函数）。

  同样也是注意边界：每个字符检测是否合法，另外检测是否超边界
  
  ```c
  class Solution {
  public:
      int myAtoi(string str) {
          int res = 0;
          int i = 0;
          int flag = 1;
          // 1. 检查空格
          while (str[i] == ' ') { i++; }
          // 2. 检查符号
          if (str[i] == '-') { flag = -1; }
          if (str[i] == '+' || str[i] == '-') { i++; }
          // 3. 计算数字
          while (i < str.size() && isdigit(str[i])) {
              int r = str[i] - '0';
              // ------ 4. 处理溢出，这是关键步骤 ------
              if (res > INT_MAX / 10 || (res == INT_MAX / 10 && r > 7)) { 
                  return flag > 0 ? INT_MAX : INT_MIN;
              }
              // ------------------------------------
              res = res * 10 + r;
              i++;
          }
          return flag > 0 ? res : -res;
      }
  };
  ```

---

- #### [电话号码的字母组合](https://leetcode.cn/problems/letter-combinations-of-a-phone-number/)：给定一个仅包含数字 `2-9` 的字符串，返回所有它能表示的字母组合。答案可以按 **任意顺序** 返回。

  BFS或者DFS即可，随便挑一个顺手的

  ```c
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

- #### [删除有序数组中的重复项](https://leetcode.cn/problems/remove-duplicates-from-sorted-array/)：给你一个 升序排列 的数组 nums ，请你 原地 删除重复出现的元素，使每个元素 只出现一次 ，返回删除后数组的新长度。元素的 相对顺序 应该保持 一致 。

  对已经排序好的数组，我们采取双指针法，检测到不同的元素再和相同元素第二位进行互换，然后指针前进即可

  ```c
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

- #### [删除有序数组中的重复项 II](https://leetcode.cn/problems/remove-duplicates-from-sorted-array-ii/)：给你一个有序数组 `nums` ，请你**[ 原地](http://baike.baidu.com/item/原地算法)** 删除重复出现的元素，使得出现次数超过两次的元素**只出现两次** ，返回删除后数组的新长度。

  和上题类似，区别是可以重复2次，加个判断

  ```c
  class Solution {
  public:
      int removeDuplicates(vector<int>& nums) {
          int n = nums.size();
          if(n <= 2)
          {
              return n;
          }
          int sp = 1;
          for(int fp = 2; fp < n; fp++)
          {
              if(nums[fp] != nums[sp - 1])
              {
                  nums[++sp] = nums[fp];
              }
          }
          return sp + 1;
      }
  };
  ```

---

- #### [移除元素](https://leetcode.cn/problems/remove-element/)：给你一个数组 `nums` 和一个值 `val`，你需要 **[原地](https://baike.baidu.com/item/原地算法)** 移除所有数值等于 `val` 的元素，并返回移除后数组的新长度。

  对每个满足条件的元素赋值给后面不同的元素。但是注意这里需要保证不会重复赋值，因此比较可行的方法是进行节点的交换：我们只要每次把不同于val的值赋值在前列就可以完成了。为了优化，我们可以将重复的值替换为末尾的值， 并且不再检查它。

  ```c
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

- #### [下一个排列](https://leetcode.cn/problems/next-permutation/)：整数数组的 **下一个排列** 是指其整数的下一个字典序更大的排列。给你一个整数数组 `nums` ，找出 `nums` 的下一个排列。

  本题的主要思路是倒叙查找到第一个不满足递减的数，然后与右侧比它大的最少的数交换，之后右侧排序获得最小排列，即为下一个更大的排列

  ```c
  class Solution {
  public:
      void nextPermutation(vector<int>& nums) {
          int size = nums.size();
          int ptr = nums[size - 1];
  
          for (int i = size - 2; i >= 0; i--)
          {
              //找到了一个比右侧小的数
              if (nums[i] < ptr)
              {
                  //找到它在右侧的位置并替换
                  for (int j = size - 1; j >= i + 1; j--)
                  {
                      if (nums[j] > nums[i])
                      {
                          int tmp = 0;
                          tmp = nums[j];
                          nums[j] = nums[i];
                          nums[i] = tmp;
                          sort(nums.begin() + i + 1, nums.end());
                          break;
                      }
                  }
                  return;
              }
              else
                  ptr = nums[i];
          }
  
          sort(nums.begin(), nums.end());
      }
  };
  ```

---

- #### [颜色分类](https://leetcode.cn/problems/sort-colors/)：给定一个包含红色、白色和蓝色、共 n 个元素的数组 nums ，原地对它们进行排序，使得相同颜色的元素相邻，并按照红色、白色、蓝色顺序排列。

  两个指针指向0的最右边界和2的最左边界，再用一个指针从头到尾遍历，然后交换0，1，2的位置即可

  ```c
  class Solution {
    public:
    /*
    荷兰三色旗问题解
    */
    void sortColors(vector<int>& nums) {
      // 对于所有 idx < p0 : nums[idx < p0] = 0
      // curr 是当前考虑元素的下标
      int p0 = 0, curr = 0;
      // 对于所有 idx > p2 : nums[idx > p2] = 2
      int p2 = nums.size() - 1;
  
      while (curr <= p2) {
        if (nums[curr] == 0) {
          swap(nums[curr++], nums[p0++]);
        }
        else if (nums[curr] == 2) {
          swap(nums[curr], nums[p2--]);
        }
        else curr++;
      }
    }
  };
  ```

---



---

### 五. 匹配问题

字符串匹配包括单模式串匹配和多模式串匹配。单模式串匹配指的是一个串跟一个串进行匹配，常用的有BF, RK, BM, KMP算法，另一种则是在一个串中同时查找多个串，它们分别是 Trie 树和 AC 自动机。

#### 1. 单模式串匹配

##### 1. BF算法

​	BF 算法中的 BF 是 Brute Force 的缩写，中文叫作暴力匹配算法，也叫朴素匹配算法。从名字可以看出，这种算法的字符串匹配方式很“暴力”，当然也就会比较简单、好懂，但相应的性能也不高。

​	暴力搜索正如其名，从头到尾轮询，检查起始位置分别是 0、1、2…n-m 且长度为 m 的 n-m+1 个子串，看有没有跟模式串匹配的。尽管理论上，BF 算法的时间复杂度很高，是 O(n*m)，但在实际的开发中，它却是一个比较常用的字符串匹配算法。第一，实际的软件开发中，大部分情况下，模式串和主串的长度都不会太长。第二，朴素字符串匹配算法思想简单，代码实现也非常简单。

##### 2. RK算法

​	Rabin-Karp 算法，属于BF算法的升级版。

​	RK 算法的思路是这样的：我们通过哈希算法对主串中的 n-m+1 个子串分别求哈希值，然后逐个与模式串的哈希值比较大小。如果某个子串的哈希值与模式串相等，那就说明对应的子串和模式串匹配了。

​	提高哈希算法计算子串哈希值效率的做法：用一个 K 进制数来表示一个子串，这个 K 进制数转化成十进制数，作为子串的哈希值

##### 3. BM（Boyer-Moore）算法

​	BM算法是一种非常高效的字符串匹配算法，**可用于文本编辑器中的查找替换功能**

​	BM 算法包含两部分，分别是坏字符规则（bad character rule）和好后缀规则（good suffix shift）

- 坏字符规则

  我们从模式串的末尾往前倒着匹配，当我们发现某个字符没法匹配的时候。我们把这个没有匹配的字符叫作坏字符，当发生不匹配的时候，我们把坏字符对应的模式串中的字符下标记作 si。如果坏字符在模式串中存在，我们把这个坏字符在模式串中的下标记作 xi。如果不存在，我们把 xi 记作 -1。那模式串往后移动的位数就等于 si-xi。（注意，我这里说的下标，都是字符在模式串的下标）。如果坏字符在模式串里多处出现，那我们在计算 xi 的时候，选择最靠后的那个，因为这样不会让模式串滑动过多，导致本来可能匹配的情况被滑动略过。

- 好后缀规则

  我们把已经匹配的部分叫作好后缀，记作{u}。我们拿它在模式串中查找，如果找到了另一个跟{u}相匹配的子串{u\*}，那我们就将模式串滑动到子串{u*}与主串中{u}对齐的位置。如果找不到，我们不仅要看好后缀在模式串中，是否有另一个匹配的子串，我们还要考察好后缀的后缀子串，是否存在跟模式串的前缀子串匹配的。找一个最长的并且能跟模式串的前缀子串匹配的，假设是{v}，然后将模式串滑动到匹配位置。

##### 4. KMP算法

​	KMP 算法的核心思想，跟上一节讲的 BM 算法非常相近。我们假设主串是 a，模式串是 b。在模式串与主串匹配的过程中，当遇到不可匹配的字符的时候，我们希望找到一些规律，可以将模式串往后多滑动几位，跳过那些肯定不会匹配的情况

​	KMP 算法就是在试图寻找一种规律：在模式串和主串匹配的过程中，当遇到坏字符后，对于已经比对过的好前缀，能否找到一种规律，将模式串一次性滑动很多位？

​	我们只需要拿好前缀本身，在它的后缀子串中，查找最长的那个可以跟好前缀的前缀子串匹配的。假设最长的可匹配的那部分前缀子串是{v}，长度是 k。我们把模式串一次性往后滑动 j-k 位，相当于，每次遇到坏字符的时候，我们就把 j 更新为 k，i 不变，然后继续比较。

​	类似 BM 算法中的 bc、suffix、prefix 数组，KMP 算法也可以提前构建一个数组，用来存储模式串中每个前缀（这些前缀都有可能是好前缀）的最长可匹配前缀子串的结尾字符下标。我们把这个数组定义为 next 数组，很多书中还给这个数组起了一个名字，叫失效函数（failure function）。

---

- #### [串联所有单词的子串](https://leetcode.cn/problems/substring-with-concatenation-of-all-words/)：给定一个字符串 `s` 和一些 **长度相同** 的单词 `words` **。**找出 `s` 中恰好可以由 `words` 中所有单词串联形成的子串的起始位置。

  本题主要在于关注以下几点：
  （1）完全匹配意味着寻找s中长度为words总长的窗口，采用滑动窗口进行处理
  （2）由于串联顺序并未规定，因此采用哈希表是很合理的做法
  （3）哈希表中key为单次，value为words中出现次数，滑动窗口去查找目前有多少个该类单次，如果恰好满足则数量+1

  ```c
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

---

- #### [最小覆盖子串](https://leetcode.cn/problems/minimum-window-substring/)：给你一个字符串 `s` 、一个字符串 `t` 。返回 `s` 中涵盖 `t` 所有字符的最小子串。如果 `s` 中不存在涵盖 `t` 所有字符的子串，则返回空字符串 `""` 。

  本题需要解决几个问题：1.记录t是否满足了，这个可以用一个数组计数解决。2. 如何取出最小的子串，可以用滑动窗口解决。左侧窗口用于收紧大小，右侧用于保证找到所有字母，当右侧滑动到找到了，则判断左侧是否可以缩小，缩到不满足条件了，右侧再继续动。

  ```c
  class Solution {
  public:
      string minWindow(string s, string t) {
          int count[256] = { 0 };
          for (auto c : t) ++count[c];
          int len = 0, minLength = s.length();
          string res;
          for (int l = 0, r = 0; r < s.length(); ++r) {
              if (--count[s[r]] >= 0) ++len;
              while (len == t.length()) {
                  if (r - l + 1 <= minLength) {
                      minLength = r - l + 1;
                      res = s.substr(l, r - l + 1);
                  }
                  if (++count[s[l++]] > 0) --len;
              }
          }
          return res;        
      }
  };
  ```

  

---

#### 2. 多模式匹配

#### 1. Trie树（搜索引擎提示功能）

​	Trie 树，也叫“字典树”。顾名思义，它是一个树形结构。它是一种专门处理字符串匹配的数据结构，用来解决在一组字符串集合中快速查找某个字符串的问题。Trie 树的本质，就是利用字符串之间的公共前缀，将重复的前缀合并在一起。Trie 树主要有两个操作，一个是将字符串集合构造成 Trie 树。这个过程分解开来的话，就是一个将字符串插入到 Trie 树的过程。另一个是在 Trie 树中查询一个字符串。

#### 2. AC自动机（敏感词过滤功能）

​	AC 自动机实际上就是在 Trie 树之上，加了类似 KMP 的 next 数组，只不过此处的 next 数组是构建在树上罢了。所以，AC 自动机的构建，包含两个操作：将多个模式串构建成 Trie 树；在 Trie 树上构建失败指针（相当于 KMP 中的失效函数 next 数组）。

​	关于next数组的实现过程，[看这里](https://www.bilibili.com/video/BV1uJ411Y7Eg?p=4)

#### 3. 正则匹配

[动态规划](https://leetcode.cn/problems/regular-expression-matching/solution/by-flix-musv/)是解决该类匹配问题的通用解法。

[72. 编辑距离](https://leetcode-cn.com/problems/edit-distance/)，[115. 不同的子序列](https://leetcode-cn.com/problems/distinct-subsequences/)，[583. 两个字符串的删除操作](https://leetcode-cn.com/problems/delete-operation-for-two-strings/)，[1143. 最长公共子序列](https://leetcode-cn.com/problems/longest-common-subsequence/)

---

* #### [正则表达式匹配](https://leetcode.cn/problems/regular-expression-matching/)：给你一个字符串 s 和一个字符规律 p，请你来实现一个支持 ‘.’ 和 ‘\*’ 的正则表达式匹配。‘.’ 匹配任意单个字符，‘*’ 匹配零个或多个前面的那一个元素。所谓匹配，是要涵盖 整个 字符串 s的，而不是部分字符串。（leetcode 10)

  **动态规划，可解决一众字符串匹配问题。另外，就本题来说，采用从后向前检索的方式进行相对来说会更容易**

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

* #### 通配符匹配：给定一个字符串 (s) 和一个字符模式 § ，实现一个支持 ‘?’ 和 ‘`*`’ 的通配符匹配。‘?’ 可以匹配任何单个字符。‘`*`’ 可以匹配任意字符串（包括空字符串）。两个字符串完全匹配才算匹配成功。(leetcode 44)

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

### 六. 特定规则的检索

常见的如判断字符串、数组是否满足一定条件，如括号匹配等，可以用栈来做。

* #### [有效的括号](https://leetcode.cn/problems/valid-parentheses/)：给定一个只包括 ‘(’，’)’，’{’，’}’，’[’，’]’ 的字符串，判断字符串是否有效。(leetcode 20)

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

* #### [括号生成](https://leetcode.cn/problems/generate-parentheses/)：给出 n 代表生成括号的对数，请你写出一个函数，使其能够生成所有可能的并且有效的括号组合。（leet code 22)

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

---

- #### [删除最外层的括号](https://leetcode.cn/problems/remove-outermost-parentheses/)：给出一个非空有效字符串 s，考虑将其进行原语化分解，使得：s = P_1 + P_2 + ... + P_k，其中 P_i 是有效括号字符串原语。对 s 进行原语化分解，删除分解中每个原语字符串的最外层括号，返回 s 。

  也是栈来做。但是这里其实只用记录括号计数，所以用一个计数器就足矣。

  ```c
  class Solution {
  public:
      string removeOuterParentheses(string s) {
          int level = 0;
          string res;
          for (auto c : s) {
              if (c == ')') {
                  level--;
              }
              if (level) {
                  res.push_back(c);
              }
              if (c == '(') {
                  level++;
              }
          }
          return res;
      }
  };
  ```

---

- #### [最长公共前缀](https://leetcode.cn/problems/longest-common-prefix/)：编写一个函数来查找字符串数组中的最长公共前缀。(leetcode 14)

  最简单的办法就是循环迭代查找，该方法时间复杂度和空间复杂度均很低，而且容易想到。除此之外，还可以使用分治的方法，但是结果并没有更优秀

  ```c
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

  