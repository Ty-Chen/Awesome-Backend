## 链表

基础操作包括手写单双链表、链表合并、链表查找、两个链表比较等，高级操作包括跳表以及和哈希表结合的问题。

### 基本操作

单链表、多链表的遍历、增删查找以及链表间的交换等。

---

* #### LRU缓存淘汰算法

  我们维护一个有序单链表，越靠近链表尾部的结点是越早之前访问的。当有一个新的数据被访问时，我们从链表头开始顺序遍历链表。

  1. 如果此数据之前已经被缓存在链表中了，我们遍历得到这个数据对应的结点，并将其从原来的位置删除，然后再插入到链表的头部。
  2. 如果此数据没有在缓存链表中，又可以分为两种情况：
     1. 如果此时缓存未满，则将此结点直接插入到链表的头部；
     2. 如果此时缓存已满，则链表尾结点删除，将新的数据结点插入链表的头部。

  实际上，我们可以继续优化这个实现思路，比如引入散列表（Hash table）来记录每个数据的位置，将缓存访问的时间复杂度降到 O(1)。


---

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

* #### [删除链表的倒数第N个节点](https://leetcode.cn/problems/remove-nth-node-from-end-of-list/)：给定一个链表，删除链表的倒数第 n 个节点，并且返回链表的头结点。（leet code 19)

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

- #### [合并两个有序链表](https://leetcode.cn/problems/merge-two-sorted-lists/)：将两个升序链表合并为一个新的 **升序** 链表并返回。新链表是通过拼接给定的两个链表的所有节点组成的。 

  不断判断两个链表的值然后指针拼接即可。

  ```c
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
      ListNode* mergeTwoLists(ListNode* l1, ListNode* l2) {
          ListNode *preHead, *ptr;
  
          preHead = new ListNode(-1);
          ptr = preHead;
  
          while (l1 != NULL && l2 != NULL)
          {
              if (l1->val <= l2->val)
              {         
                  ptr->next = l1;    
                  l1 = l1->next;                                 
              }
              else
              {
                  ptr->next = l2;
                  l2 = l2->next;
              }
              ptr = ptr->next;
          }
  
          ptr->next = (l1 == NULL? l2 : l1);
  
          return preHead->next;
      }
  };
  ```

---

- #### [合并K个升序链表](https://leetcode.cn/problems/merge-k-sorted-lists/)：给你一个链表数组，每个链表都已经按升序排列。请你将所有链表合并到一个升序链表中，返回合并后的链表。

  采用二分法逐个合并直至剩下一个即可

  ```c
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
      ListNode* mergeKLists(vector<ListNode*>& lists) {
          int size = lists.size();
          if (size == 0) {
              return nullptr;
          }
          if (size == 1) {
              return lists[0];
          }
          
          while (size > 1)
          {               
              for (int i = 0; i < size / 2; i++)
              {
                  lists[i] = mergeTwoLists(lists[i], lists[i + size / 2]);
              }
              if (size % 2)
              {
                   lists[size / 2] = lists[size - 1];
                   size = size / 2 + 1;               
              }
              else
              {
                  size = size / 2;
              }
          }
  
          return lists[0];
      }
  
      ListNode* mergeTwoLists(ListNode* l1, ListNode* l2) {
          ListNode *preHead, *ptr;
  
          preHead = new ListNode(-1);
          ptr = preHead;
  
          while (l1 != NULL && l2 != NULL)
          {
              if (l1->val <= l2->val)
              {         
                  ptr->next = l1;    
                  l1 = l1->next;                                 
              }
              else
              {
                  ptr->next = l2;
                  l2 = l2->next;
              }
              ptr = ptr->next;
          }
  
          ptr->next = (l1 == NULL? l2 : l1);
  
          return preHead->next;
      }
  };
  ```

---

- #### [两两交换链表中的节点](https://leetcode.cn/problems/swap-nodes-in-pairs/)：给你一个链表，两两交换其中相邻的节点，并返回交换后链表的头节点。你必须在不修改节点内部的值的情况下完成本题（即，只能进行节点交换）。

  ```c
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
      ListNode* swapPairs(ListNode* head) {
          ListNode *preHead = new ListNode(-1);
          ListNode *tmp, *ahead;
  
          preHead->next = head;
          ahead = preHead;
          while (head != NULL && head->next != NULL)
          {
              tmp = head->next;
              ahead->next = tmp;
              head->next = tmp->next;
              tmp->next = head;
              ahead = head;            
              head = head->next;
          }
  
          return preHead->next;
      }
  };
  ```

---

* #### [k个一组翻转链表](https://leetcode.cn/problems/reverse-nodes-in-k-group/)：给你一个链表，每 k 个节点一组进行翻转，请你返回翻转后的链表。k 是一个正整数，它的值小于或等于链表的长度。如果节点总数不是 k 的整数倍，那么请将最后剩余的节点保持原有顺序。

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

- #### [删除排序链表中的重复元素](https://leetcode.cn/problems/remove-duplicates-from-sorted-list/)：给定一个已排序的链表的头 `head` ， *删除所有重复的元素，使每个元素只出现一次* 。返回 *已排序的链表* 。

  已排序，那就遍历删除就完事了

  ```c
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
      ListNode* deleteDuplicates(ListNode* head) 
      {
      if(head == NULL)  //如果所给链表为空直接返回，否则在执行ptr->next这部操作时会因为ptr为NULL而造成执行时错误
          return head;
  	ListNode* ptr{ head };  //遍历的指针
  	while (ptr->next != NULL)  //遍历首个元素到第倒数第二个元素，因为是逐个向后比较，最后一个元素会被比较到，这样是正确的
  	{
  		if (ptr->val == ptr->next->val)  //和后一个元素比较
  		{
  			ListNode* p = ptr->next;  
  			ptr->next = p->next;  //删除操作
  			delete p;  //释放空间
  		}
  		else
  		{
  			ptr = ptr->next;  //移动到后一个元素
  		}
  	}
  	return head;  //返回首个节点
      }
  };
  ```

---

- #### [删除排序链表中的重复元素 II](https://leetcode.cn/problems/remove-duplicates-from-sorted-list-ii/)：给定一个已排序的链表的头 `head` ， *删除原始链表中所有重复数字的节点，只留下不同的数字* 。返回 *已排序的链表* 。

  和上题类似，但是需要全删，因为是排好序的链表，所以有头指针+遍历，一遍就够了

  ```c
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
      ListNode* deleteDuplicates(ListNode* head) {
          if (head == NULL)
              return head;
          ListNode *ptr = head, *last;
          ListNode *ahead = new ListNode(-1);
          ahead->next = head;
          last = ahead;
          
          while (ptr->next != NULL)
          {
              if (ptr->val == ptr->next->val)
              {
                  while (ptr->next != NULL && ptr->val == ptr->next->val)
                  {
                      ptr = ptr->next;
                  }
                  last->next = ptr->next;
                  ptr = ptr->next;
                  if (ptr == NULL)
                      return ahead->next;
              }
              else
              {
                  last = ptr;
                  ptr = ptr->next;
              }
          }
          return ahead->next;
      }
  };
  ```

---

- #### [反转链表 II](https://leetcode.cn/problems/reverse-linked-list-ii/)：给你单链表的头指针 head 和两个整数 left 和 right ，其中 left <= right 。请你反转从位置 left 到位置 right 的链表节点，返回 反转后的链表 。

  比起逆转链表，就是多了一个先遍历left而已。

  ```c
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
      ListNode* reverseBetween(ListNode* head, int m, int n) {  
          ListNode *dummy = new ListNode(-1);
          dummy->next = head;
  
          // 第m-1个节点
          ListNode *pre=dummy;
          for(int i=1; i<m; i++){
              pre = pre->next;
          }
  
          // 第m个节点
          ListNode *t, *cur=pre->next, *mNode=pre->next;
          // 头插法
          for(int i=m; i<=n; i++){
              t = cur->next;
              cur->next = pre->next;
              pre->next = cur;
              cur = t;
          }
          // 第m个节点指向第n+1个节点
          mNode->next = cur;
          
          return dummy->next;
      }
  };
  ```

  

---

### 复杂题