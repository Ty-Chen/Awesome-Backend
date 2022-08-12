## 树

#### 二叉树的问题主要包括树的遍历、树的构造、二叉搜索树、根据前中或者中后序还原二叉树、二叉树构造B树等。

* #### 二叉树遍历

  遍历包括前序中序后续，都很简单。写法包括递归和栈，如下所示

  ```cpp
  //前序遍历是中左右，推右儿子入栈，打印当前值，访问左儿子
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
              rt=S.top();
              S.pop();
          }
          return v;        
      }
  
  
  // 后续遍历顺序是左右中，为了方便栈记录所以记录成中右左，然后颠倒数组
  // 所以推左儿子入栈，记录当前值，访问右儿子
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
              rt=S.top();
              S.pop();
          }
          reverse(v.begin(),v.end());
          return v;
      }
  
  // 中序遍历是左中右，推左儿子入栈直到最后，打印当前值，访问右儿子
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
              rt=S.top();
              S.pop();
              v.push_back(rt->val);
            rt=rt->right;
          }
          return v;        
      }
  
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
      vector<int> ret;
  public:
      vector<int> inorderTraversal(TreeNode* root) {
          if(root) {
              inorderTraversal(root->left);
              ret.push_back(root->val);
              inorderTraversal(root->right);
          }
          return ret;
      }
  };
  
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

- #### [二叉树中的最大路径和](https://leetcode.cn/problems/binary-tree-maximum-path-sum/)：路径 被定义为一条从树中任意节点出发，沿父节点-子节点连接，达到任意节点的序列。同一个节点在一条路径序列中 至多出现一次 。该路径 至少包含一个 节点，且不一定经过根节点。路径和 是路径中各节点值的总和。给你一个二叉树的根节点 root ，返回其 最大路径和 。

  本题的关键在于将寻找路径转化为寻找每个节点对应的最大“贡献值”：节点值与其子节点中的最大贡献值之和（对于叶节点而言，最大贡献值等于节点值）。由此就可以很轻松的用递归解决。

  ```c
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
      int maxPathSum(TreeNode* root, int &val)
      {
          if (root == nullptr) return 0;
          int left = maxPathSum(root->left, val);
          int right = maxPathSum(root->right, val);
          int lmr = root->val + max(0, left) + max(0, right);
          int ret = root->val + max(0, max(left, right));
          val = max(val, lmr);
          return ret;
      }
  
      int maxPathSum(TreeNode* root) 
      {
          int val = INT_MIN;
          maxPathSum(root, val);
          return val;
      } 
   };
  ```

---

- #### [从二叉搜索树到更大和树](https://leetcode.cn/problems/binary-search-tree-to-greater-sum-tree/)：给定一个二叉搜索树 `root` (BST)，请将它的每个节点的值替换成树中大于或者等于该节点值的所有节点值之和。

  本题做法很简单：逆序的中序遍历即是从大到小的遍历，可以满足题意

  ```c
  /**
   * Definition for a binary tree node.
   * struct TreeNode {
   *     int val;
   *     TreeNode *left;
   *     TreeNode *right;
   *     TreeNode() : val(0), left(nullptr), right(nullptr) {}
   *     TreeNode(int x) : val(x), left(nullptr), right(nullptr) {}
   *     TreeNode(int x, TreeNode *left, TreeNode *right) : val(x), left(left), right(right) {}
   * };
   */
  class Solution 
  {
      int nTotal;
  public:
      TreeNode* bstToGst(TreeNode* root) 
      {
          nTotal = 0;
          ChangeVal(root);
          return root;
      }
  
  private:
      void ChangeVal(TreeNode* pNode)
      {
          if (pNode == NULL) return;
  
          ChangeVal(pNode->right);
  
          pNode->val += nTotal;
          nTotal = pNode->val;
  
          ChangeVal(pNode->left);
      }
  };
  ```

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

