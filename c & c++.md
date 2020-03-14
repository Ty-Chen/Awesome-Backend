C和C++面试攻略
====

语言使用问题
----

* #### C++模板的作用
  答：将算法与具体对象分离，与类型无关，通用，节省精力
-----

* #### volatile的含义
  答：变量可能在编译器的控制或监控之外改变，告诉编译器不要优化该变量，如被系统时钟更新的变量。
-----

* #### OFFSETOF(s, m)的宏定义，s是结构类型，m是s的成员，求m在s中的偏移量
  答：``#define OFFSETOF（TYPE, MEMBER） size_t（&((TYPE*)0)->MEMBER）``
-----

* #### C++虚函数是如何实现的？
  答：使用虚函数表。 C++对象使用虚表， 如果是基类的实例，对应位置存放的是基类的函数指针；如果是继承类，对应位置存放的是继承类的函数指针（如果在继承类有实现）。所以 ，当使用基类指针调用对象方法时，也会根据具体的实例，调用到继承类的方法。

-----

* #### C++的虚函数有什么作用？
  答：虚函数作用是实现多态，更重要的，虚函数其实是实现封装，使得使用者不需要关心实现的细节。在很多设计模式中都是这样用法，例如Factory、Bridge、Strategy模式。

-----

* #### 给出float与“零值”比较的 if 语句（假设变量名为var）？
  答：
  ```cpp
    const float EPSINON = 0.00001;
    if ((x >= - EPSINON) && (x <= EPSINON)
      ...
    ```
-----

* #### 初始化序列和构造函数谁先执行
  答：

-----
* #### gdb调试进程
  答：

------
* #### 写一个c程序辨别系统是大端or小端字节序。
  答：大端模式，是指数据的高字节保存在内存的低地址中，而数据的低字节保存在内存的高地址中。
小端模式，是指数据的高字节保存在内存的高地址中，而数据的低字节保存在内存的低地址中。
```cpp
#include <stdio.h>
int main(int argc, char* argv[]){
    union {
        short s;
        char c[sizeof(short)];
    } un;

    un.s = 0x0102;  // 使un为2字节，一节为1，一节为2

    if (sizeof(short) == 2) {
        if (un.c[0] == 1 && un.c[1] == 2) printf("big-endian\n");
        else if (un.c[0] == 2 && un.c[1] == 1) printf("little-endian\n");
        else printf("unknown\n");
    } else printf("sizeof(short) = %d\n", sizeof(short));
    return 0;
}
```


STL相关问题
-----

* #### 具体说明STL如何实现vector
  答：vector的内部是使用动态数组的方式来实现的，如果动态数组的内部实现不够用，就要动态的重新分配内存。然后把原数组的内容拷贝过去。

-----

* #### vector和list的区别
  答：vector和数组类似，拥有连续的内存空间，支持随机的存取，在中间进行元素的插入和删除的操作时间复杂度是O(n)
list是由双向链表实现的，只能通过数组指针来进行数据访问，遍历中间的元素，时间的复杂度是O(n).


-----

* #### 如何选择使用vector或dequeue
  答：一般情况下使用vector,在需要从首尾两端进行插入或删除操作的时候需要选择dequeue.

-----

* #### map内部的实现
  答：map的底层是一棵红黑树，在对节点的插入或是删除操作中，通过旋转来保持平衡性，最坏情况下的插入、删除、查找时间是O(logn)
map和hashmap的区别
底层数据结构不同，map是红黑树，hashmap是哈希表
map元素可以自动按照键值排序，hashmap的各项操作平均时间复杂度接近常数
map是C++标准的一部而hashmap并不是
vector中erase方法与algorithn中的remove方法区别
vector中erase方法真正删除了元素，迭代器不能访问了
remove只是简单地将元素移到了容器的最后面，迭代器还是可以访问到。因为algorithm通过迭代器进行操作，不知道容器的内部结构，所以无法进行真正的删除。

------

* #### 为何map和set的插入删除效率比用其他序列容器高？
  答：

-----

* #### 每次insert之后，以前保存的iterator是否会失效？
  答：对于vector来说，每一次删除和插入，指针都有可能失效，调用push_back在尾部插入也是如此。因为为了保证内部数据的连续存放，iterator指向的那块内存在删除和插入过程中可能已经被其他内存覆盖或者内存已经被释放了。即使时push_back的时候，容器内部空间可能不够，需要一块新的更大的内存，只有把以前的内存释放，申请新的更大的内存，复制已有的数据元素到新的内存，最后把需要插入的元素放到最后，那么以前的内存指针自然就不可用了。特别时在和find等算法在一起使用的时候，牢记这个原则：不要使用过期的iterator。

-----

* #### 为何map和set不能像vector一样有个reserve函数来预分配数据？
  答：在map和set内部存储的已经不是元素本身了，而是包含元素的节点。也就是说map内部使用的Alloc并不是map<Key,Data, Compare, Alloc>声明的时候从参数中传入的Alloc

-----

* #### hash_map和map的区别在哪里？
  答：

    | |map               |unordered_map  |
    |--|--|--|
    |Ordering          | increasing  order  ( by default )   |no ordering
    |Implementation|Self balancing BST ( like RBT )|Hash Table
    |Search Time|log(n)| average O(1), worst O(n)
    |Insertion Time|log(n) + Rebalance| Same as search
    |Deletion Time| log(n) + Rebalance|Same as search

-----
* #### 什么时候需要用hash_map，什么时候需要用map?
  答：在以下场景使用std::map更优：
  1. 当需要排序数据
  2. 当需要顺序打印、访问数据
  3. 当需要找数据的前后项的时候


  相反的，在以下场合更适宜使用std::unsorted_map
  1. 不需要数据排序
  2. 需要统计数据或对增删有严格时间要求
  3. 需要快速访问单个元素


-----

* ####
  答：

  -----

* ####
  答：

C++ 11
-----

* #### 什么是auto_ptr以及如何使用
  答： auto_ptr可以代替指针进行类似指针的操作，并不用关心内存的释放，auto_ptr的析构函数自动释放它绑定的动态分配对象。

-----

* #### auto_ptr的实现
  答：每次创建类的新对象的时候，初始化指针并将引用计数设置为1；
当对象作为另一个对象的副本而创建的时候，拷贝构造函数，拷贝指针并增加与之相对应的引用计数
对一个对象进行赋值的时候，赋值操作符减少左操作数所指对象的引用计数，并增加右操作数所指对象的引用计数
调用析构函数的时候减少引用计数，如果引用计数减少至0，则删除基础对象
重载*操作符和->操作符，使auto_ptr有类似于普通指针的操作


-----

* #### 使用auto_ptr的限制
  答：auto_ptr所所指向的对象要求只能拥有一个拥有者
如下用法是错误的
  ``` cpp
  classA *pA = new classA;
  auto_ptr<classA> ptr1(pA);
  auto_ptr<classB> ptr2(pA);
  ```
  auto_ptr不能以传值的方式进行传递b不支持数组


-----



* ####
  答：

-----

* ####
  答：

-----
