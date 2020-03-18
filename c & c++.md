C和C++面试攻略
====

语言使用问题
----

* #### C++模板的作用
  ​	将算法与具体对象分离，与类型无关，通用，节省精力
-----

* #### 模板类的偏特化？
  ​	模板分为类模板与函数模板，特化分为全特化与偏特化。全特化就是限定死模板实现的具体类型，偏特化就是如果这个模板有多个类型，那么只限定其中的一部分。对于函数模板，却只有全特化，不能偏特化。

---

* #### volatile的含义
  ​	变量可能在编译器的控制或监控之外改变，告诉编译器不要优化该变量，如被系统时钟更新的变量。

-----

* #### OFFSETOF(s, m)的宏定义，s是结构类型，m是s的成员，求m在s中的偏移量
  ``#define OFFSETOF（TYPE, MEMBER） size_t（&((TYPE*)0)->MEMBER）``
  
-----

* #### C++虚函数是如何实现的？
  ​	实现机制: 为每个类对象添加一个隐藏成员，隐藏成员中保存了一个指向函数地址数组的指针，称为虚表指针（vptr），这种数组称为虚函数表（virtual function table, vtbl），即，每个类使用一个虚函数表，每个类对象用一个虚表指针。如果派生类重写了基类的虚方法，该派生类虚函数表将保存重写的虚函数的地址，而不是基类的虚函数地址。如果基类中的虚方法没有在派生类中重写，那么派生类将继承基类中的虚方法，而且派生类中虚函数表将保存基类中未被重写的虚函数的地址。如果派生类中定义了新的虚方法，则该虚函数的地址也将被添加到派生类虚函数表中
-----

* #### C++的虚函数有什么作用？
  ​	虚函数作用是实现多态，更重要的，虚函数其实是实现封装，使得使用者不需要关心实现的细节。在很多设计模式中都是这样用法，例如Factory、Bridge、Strategy模式。

-----

* #### 给出float与“零值”比较的 if 语句（假设变量名为var）？
  ```cpp
    const float EPSINON = 0.00001;
    if ((x >= - EPSINON) && (x <= EPSINON)
      ...
  ```
-----

* ### C++类中数据成员初始化顺序？

  1. 成员变量在使用初始化列表初始化时，与构造函数中初始化成员列表的顺序无关，只与定义成员变量的顺序有关。

  2. 如果不使用初始化列表初始化，在构造函数内初始化时，此时与成员变量在构造函数中的位置有关。

  3. 类中const成员常量必须在构造函数初始化列表中初始化。

  4. 类中static成员变量，只能在类内外初始化(同一类的所有实例共享静态成员变量)。

  初始化顺序：

  * 1） 基类的静态变量或全局变量
  * 2） 派生类的静态变量或全局变量
  * 3） 基类的成员变量
  * 4） 派生类的成员变量

-----
* #### gdb调试进程

  [see this](https://www.jianshu.com/p/6d306725286d)

---
* #### static_cast, dynamic_cast, const_cast, reinpreter_cast的区别？ 

  [see here](https://www.cnblogs.com/chio/archive/2007/07/18/822389.html)

---

* #### 深拷贝与浅拷贝

  1. 什么时候用到拷贝函数？

      - 一个对象以值传递的方式传入函数体； 

      - 一个对象以值传递的方式从函数返回；

      - 一个对象需要通过另外一个对象进行初始化。

     如果在类中没有显式地声明一个拷贝构造函数，那么，编译器将会自动生成一个默认的拷贝构造函数，该构造函数完成对象之间的位拷贝。位拷贝又称浅拷贝；

  2. 是否应该自定义拷贝函数？

      自定义拷贝构造函数是一种良好的编程风格，它可以阻止编译器形成默认的拷贝构造函数，提高源码效率。

  3. 什么叫深拷贝？什么是浅拷贝？两者异同？

       如果一个类拥有资源，当这个类的对象发生复制过程的时候，资源重新分配，这个过程就是深拷贝，反之，没有重新分配资源，就是浅拷贝。

  4. 深拷贝好还是浅拷贝好？

     如果实行位拷贝，也就是把对象里的值完全复制给另一个对象，如A=B。这时，如果B中有一个成员变量指针已经申请了内存，那A中的那个成员变量也指向同一块内存。这就出现了问题：当B把内存释放了（如：析构），这时A内的指针就是野指针了，出现运行错误。

---

* #### const与#define的区别

  1. 编译器处理方式 
     define – 在预处理阶段进行替换 
     const – 在编译时确定其值
  
  2. 类型检查 
     define – 无类型，不进行类型安全检查，可能会产生意想不到的错误 
     const – 有数据类型，编译时会进行类型检查
  
  3. 内存空间 
     define – 不分配内存，给出的是立即数，有多少次使用就进行多少次替换，在内存中会有多个拷贝，消耗内存大 
     const – 在静态存储区中分配空间，在程序运行过程中内存中只有一个拷贝
  
  4. 其他 
     在编译时， 编译器通常不为const常量分配存储空间，而是将它们保存在符号表中，这使得它成为一个编译期间的常量，没有了存储与读内存的操作，使得它的效率也很高。 
     宏替换只作替换，不做计算，不做表达式求解。

------
* #### 写一个c程序辨别系统是大端or小端字节序。
  大端模式，是指数据的高字节保存在内存的低地址中，而数据的低字节保存在内存的高地址中。
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

------
* #### 引用和多态的区别？
 引用是除指针外另一个可以产生多态效果的手段。这意味着，一个基类的引用可以指向它的派生类实例。

-----
 * #### 指针和引用的区别？
   1.  指针：指针是一个变量，只不过这个变量存储的是一个地址，指向内存的一个存储单元；而引用跟原来的变量实质上是同一个东西，只不过是原变量的一个别名而已;
   2. 可以有const指针，但是没有const引用；
   
   3. 指针可以有多级，但是引用只能是一级（int \**p；合法 而 int &&a是不合法的）
   
   4. 指针的值可以为空，但是引用的值不能为NULL，并且引用在定义的时候必须初始化；
   5. 指针的值在初始化后可以改变，即指向其它的存储单元，而引用在进行初始化后就不会再改变了。

   6. "sizeof引用"得到的是所指向的变量(对象)的大小，而"sizeof指针"得到的是指针本身的大小；
   
   7. 指针和引用的自增(++)运算意义不一样；
   
    对实参进行修改，用指针传递参数，是对指针指向的内存单元中的数据进行操作；
    用引用作为函数参数传递时，实质上传递的是实参本身，而非拷贝，不仅节约时间，而且可以节约空间。

-----

* #### 面向对象的三个特征，分别有什么作用？
  三个特征：封装、继承、多态。

  1. 封装：把客观事物封装成抽象的类，并且类可以把自己的数据和方法只让可信的类或者对象操作，对不可信的进行信息隐藏。
  2. 继承：让某个类型的对象获得另一个类型的对象的属性的方法。
  3. 多态：一个类实例的相同方法在不同情形有不同表现形式。

-----

* #### sizeof在计算变量所占空间大小时采取的机制？
  1. 对基本数据类型的变量,取得的是数据类型的大小。
  2. 对结构体类型的变量，取得的是结构体变量所占空间的大小。这其中涉及到字节对齐的问题。
  3. 对数组类型的变量，取得的是数组的大小，并不是数组元素的个数。
  4. 对指针类型（包括函数指针）的变量，取得的是指针的大小，即4个字节。
  5. 对数组形参，与指针相同，返回的是指针的大小，因为c语言中并不传递数组的每个元素值，只是将数组的首地址传给函数。
-----

* #### 结构体struct和联合体union的区别？
  1. struct和union都是由多个不同的数据类型成员组成,但在任何同一时刻,union中只存放了一个被选中的成员, 而struct的所有成员都存在。在struct中，各成员都占有自己的内存空间，它们是同时存在的。一个struct变量的总长度等于所有成员长度之和。在Union中，所有成员不能同时占用它的内存空间，它们不能同时存在。Union变量的长度等于最长的成员的长度。
  2. 对于union的不同成员赋值,将会对其它成员重写,原来成员的值就不存在了,而对于struct的不同成员赋值是互不影响的。
-----

* #### 重载和覆盖的区别是什么？
  1. 重载要求函数名相同，但是参数列表必须不同，返回值可以相同也可以不同。
     覆盖要求函数名、参数列表、返回值必须相同。
  2. 在类中重载是同一个类中不同成员函数之间的关系。
     在类中覆盖则是子类和基类之间不同成员函数之间的关系
  3.  重载函数的调用是根据参数列表来决定调用哪一个函数。
     覆盖函数的调用是根据对象类型的不同决定调用哪一个
  4. 在类中对成员函数重载是不能够实现多态。
     在子类中对基类虚函数的覆盖可以实现多态

-----
* #### extern C的作用？
  ​	C++语言在编译的时候为了解决函数的重载问题，会将函数名和参数联合起来生成一个中间的函数名称，而C语言则不会，因此会造成链接时找不到对应函数的情况，此时C函数就需要用extern “C”进行链接指定，这告诉编译器，请保持我的名称，不要给我生成用于链接的中间函数名。
  ​	当extern在头文件中: extern int g_Int; 它的作用就是声明函数或全局变量的作用范围的关键字，其声明的函数和变量可以在本模块活其他模块中使用，它是一个声明不是定义!也就是说B模块(编译单元)要是引用模块(编译单元)A中定义的全局变量或函数时，它只要包含A模块的头文件即可,在编译阶段，模块B虽然找不到该函数或变量，但它不会报错，它会在连接时从模块A生成的目标代码中找到此函数。 只在头文件中做声明!一般定义static全局变量时，都把它放在原文件中而不是头文件!

-----
* #### volatile的作用？
  ​	volatile 关键字是一种类型修饰符，用它声明的类型变量表示可以被某些编译器未知的因素更改，比如：操作系统、硬件或者其它线程等。遇到这个关键字声明的变量，编译器对访问该变量的代码就不再进行优化(优化编译器把变量从内存装入CPU寄存器中)，从而可以提供对特殊地址的稳定访问。声明时语法：int volatile vInt; 当要求使用volatile声明的变量的值的时候，系统总是重新从它所在的内存读取数据，即使它前面的指令刚刚从该处读取过数据。而且读取的数据立刻被保存。

  ​	一般说来，volatile用在如下的几个地方：

  - 中断服务程序中修改的供其它程序检测的变量需要加volatile；
  - 多任务环境下各任务间共享的标志应该加volatile；
  - 存储器映射的硬件寄存器通常也要加volatile说明，因为每次对它的读写都可能由不同意义；

-----
* #### 多重类构造和析构的顺序？
  ​	构造类时，会先构造其父类，然后创建类成员，最后调用本身的构造函数。析构函数的调用顺序与构造函数相反。

-----
* #### static const的用法？
  static变量的作用：
  
  - 文件中的static变量只能在该文件中访问；
  - static变量在程序结束后才释放；
  - 非static变量在全局变量中是会进行初始化的，但是在局部变量中，比如函数体内部是不会初始化的。但是static变量会在所有情况下进行初始化。
  
  类中static的作用:
  
  * static 数据成员是属于整个类的，不属于某个对象，必须在内定义的外部进行定义，在类定义体中定义是错误的。
  * 静态成员函数不能访问非静态函数或者变量。static函数中不能使用this关键字，不能声明为virtual虚函数。
  
  ```cpp
    const int y = 10;  // const常量
  
    int x = 30;
    const int* p1 = &x;  // 指向常量的指针，指针可以指向其他常量，但不能通过指针修改数据
    int* const p2 = &x;  // 指向变量的常量指针，指针不可以指向其他常量，但能通过指针修改数据
    const int* const p3 = &x;  // 指向常量的常量指针。
  ```
    ```cpp
     //类中的const：  
    class C {
          ...
          const C& fun(const C& c) const {
              ...
          }
          ...
  
          static int c;   // 静态变量
        const int b;    // 常量
  
          const static int a = 100;
        static const int b = 200;
          ...
      }；
  
      int C::c = 10;  // 静态变量的初始化
    ```
  - 第一个const修饰函数返回值，返回一个const值，不能被修改；
  
  - 第二个const修饰函数参数，函数中不能修改该参数，可以接收const或者非const的形参；
  
  - 第三个const修饰调用对象，也就是this，保证调用对象不会被修改。
  
  - 静态变量c不能在类声明中初始化，只能在类外初始化；
  
  - 常量d只能在构造函数中初始化。
  
  - static const和 const static一样，也可用枚举法表示（enum {a = 100};）

-----
* #### printf的可变参数是怎么实现的，如果参数个数不匹配会发生什么，比如字符串需要3个参数，但是只传了2个或者4个分别会发生什么
  [see this blog](http://www.cnblogs.com/hnrainll/archive/2011/08/05/2128496.html)

-----
* #### C语言各种指针
  - void* 类型指针：通用变体类型指针；可以不经转换，赋给其他指针，函数指针除外；malloc返回的就是void*类型。

  - NULL指针：是一个标准规定的宏定义；#define NULL （（void \*） 0） 用来表示空指针常量；

  - 零指针：指针值为0，零值指针，没有存储任何内存地址的指针；可以使任意一种指针类型，eg：void * ；int * ；double \*；

  - 空指针：指针赋值为0；0*7；3-3等之后，指针即变成空指针；即：空指针不指向任何实际的对象或者函数；NULL指针和零指针都是空指针。

  - 野指针：指向垃圾内存的指针；（1）指针变量没有初始化（2）指针被delete或者free之后没有置为空（3）指针操作超越了变量的范围

  - 悬垂指针：指向曾经存放对象的内存，但是该对象已经不存在了；delete操作完成后的指针就是悬垂指针，此时需要将指针置为0变为零值指针；

-----
* #### malloc的返回值，还有其他内存分配函数吗，有什么区别
  ​	malloc函数可以从堆上获得指定字节的内存空间，其函数原型如下：

  `void * malloc(int n)`

  ​	其中，形参n为要求分配的字节数。如果函数执行成功，malloc返回获得内存空间的首地址；如果函数执行失败，那么返回值为NULL。由于malloc函数值的类型为void型指针，因此，可以将其值类型转换后赋给任意类型指针，这样就可以通过操作该类型指针来操作从堆上获得的内存空间。

  ​	需要注意的是,malloc函数分配得到的内存空间是未初始化的。因此，一般在使用该内存空间时，要调用另一个函数memset来将其初始化为全0。memset函数的声明如下：
  `void * memset (void * p,int c,int n)`

  `void *calloc(int n,int size)`

  ​	函数返回值为void型指针。如果执行成功，函数从堆上获得size X n的字节空间，并返回该空间的首地址。如果执行失败，函数返回NULL。该函数与malloc函数的一个显著不同时是，calloc函数得到的内存空间是经过初始化的，其内容全为0。

  ​	可以实现内存分配和内存释放的功能，其函数声明如下：

  `void * realloc(void * p,int n)`

  ​	其中，指针p必须为指向堆内存空间的指针，即由malloc函数、calloc函数或realloc函数分配空间的指针。realloc函数将指针p指向的内存块的大小改变为n字节。如果n小于或等于p之前指向的空间大小，那么。保持原有状态不变。如果n大于原来p之前指向的空间大小，那么，系统将重新为p从堆上分配一块大小为n的内存空间，同时，将原来指向空间的内容依次复制到新的内存空间上，p之前指向的空间被释放。relloc函数分配的空间也是未初始化的。

-----
* #### 为什么构造函数不能为虚？
  ​	vptr指针指向虚函数表，执行虚函数的时候，会调用vptr指针变量指向的虚函数表中的虚函数。
当定义个对象的时候，首先会分配内存空间，然后再执行构造函数对该对象进行初始化构造。vptr变量是在构造函数中进行初始化的。又因为要想执行虚函数必须通过vptr变量找到虚函数表（在构造函数初始化vptr变量之前是不会调用虚函数的）。
  ​	所以不能将构造函数声明为虚函数。

-----
* #### 析构函数为什么必须是虚的？
  ​	原因是基类指针指向了派生类对象，而基类中的析构函数却是非virtual的，虚函数是动态绑定的基础。现在析构函数不是virtual的，因此不会发生动态绑定，而是静态绑定，指针的静态类型为基类指针，因此在delete时候只会调用基类的析构函数，而不会调用派生类的析构函数。这样，在派生类中申请的资源就不会得到释放，就会造成内存泄漏。

-----
* #### 虚函数表是什么时期创建的？
  ​	虚函数表中的virtual functions地址是如何被建构起来的？在C++中，virtual functions（可经由其class object被调用）可以在编译时期获知。此外，这一组地址是固定不变的，执行期不可能新增或替换之。由于程序执行时，表格的大小和内容都不会改变，所以其建构和存取皆可以由编译器完全掌控，不需要执行期的任何介入。

-----
* #### 仿函数
  ​	也就是函数对象，行为类似函数的对象，其类定义中必须重载function call运算符operator()。

  - 生成器(generator)是不用参数就可以调用的函数符(仿函数);
  - 一元函数(unary function) 是用一个参数就可以调用的函数符;
  - 二元函数(binary function)是用两个参数就可以调用的函数符;
  
  当然，这些概念都有相应的改进版：
  
  - 返回bool值的一元函数是谓词(predicate)；
  - 返回bool值的二元函数是二元谓词(binary predicate);
  - 记录了参数、返回值类型的仿函数称之为自适应的仿函数;
  

  
  函数适配器:
  
  - 绑定器（binder），是一种函数适配器，它通过将一个操作数绑定到给定值而将二元函数对象转换为一元函数对象。常用绑定器有两个：bind1st, bind2nd，分别是把值绑定到二元函数的第一个参数或者是第二个参数。
  - 求反器（negator），是一种函数适配器，它将谓词函数对象的真值求反。
  
  成员函数适配器是一个一元函数，参数为调用这个成员函数的对象(或者对象指针)。当然如果是对象的话适配器就是mem_fun_ref，如果是指针的话就是mem_fun.

-----
* #### 如何在main函数之前执行函数？
   在gcc中，可以使用attribute关键字，声明constructor和destructor，代码如下：
   
    ```cpp
   #include <stdio.h>  

    __attribute((constructor)) void before_main()  
    {  
        printf("%s/n",__FUNCTION__);  
    }  

    __attribute((destructor)) void after_main()  
    {  
        printf("%s/n",__FUNCTION__);  
    }  

    int main( int argc, char ** argv )  
    {  
        printf("%s/n",__FUNCTION__);  
        return 0;  
    }  
    ```
   
    vc不支持attribute关键字，在vc中，可以使用如下方法：
   
  ```cpp
  #include <stdio.h>  

  int  main( int argc, char ** argv )
  {  
        printf("%s/n",__FUNCTION__);  

        return 0;  
  }  
  
  int before_main()  
  {  
      printf("%s/n",__FUNCTION__);  
      return 0;  
  }  
  
    int after_main()  
    {  
          printf("%s/n",__FUNCTION__);  
  
          return 0;  
  
    }  
  
    typedef int func();  
  
    #pragma data_seg(".CRT$XIU")  
    static func * before[] = { before_main };  
  
    #pragma data_seg(".CRT$XPU")  
    static func * after[] = { after_main };  
  
    #pragma data_seg()  
  ```

 




  在C++中，还可以用全局对象构造函数

   ```cpp
      #include <iostream>
      #include <string>
    
      using namespace std;
    
      class A {
      public:
          A(string s) {
              str.assign(s);
              cout << str << ":A构造" <<endl;
          }
          ~A(){
              cout << str << ":A析构" <<endl;
          }
      private:
          string str;
      };
    
      A test1("Global"); // 全局对象的构造
    
      int main() {
          A test2("main");  // 局部对象的构造
          return 0;
      }
    
      // 输出结果
      // Global:A构造
      // main:A构造
      // main:A析构
      // Global:A析构
   ```

  编译执行，上述两段代码的结果均为：

  before_main

  main

  after_main

  （vc中可能不支持__FUNCTION__来获得函数名，你可以用另外的方式来获取，比如在befor_main()函数中printf("befor_main()\n");来模拟__FUNCTION__的功能！嘿嘿）

  可以在main前后调用多个函数，在gcc下使用attribute声明多个constructor、destructor，vc下在before、after数组中添加多个函数指针。

-----

* #### 如果是自己为一个类写一个sizeof函数，应该考虑哪些问题？ 
```cpp
  #pragma pack(1)
  
class Empty{
  };   // 1
  
class A {
  public:
      A() {a = 3;}
      int a;
      virtual int funa(){}
  };
  
class A1 {
      int a1;
      virtual int funa(){}
  };
  
class A2 {
      int a2;
      virtual int funa();
  };
  
class Derive1: public A, public A1, public A2 {
      virtual int derive();
  };    // 36
  
class Derive2: public A, public A1, public A2 {
      int der;
      virtual int derive();
  };    // 40
  
class B {
      int b;
  };   // 4
  
class C : public B {
      int c;
      virtual int func();
  };   // 16
  
    class D : public A {
        int d;
        virtual int fund();
    };   // 16
  
    class E : virtual public A {
    public:
        E() {e = 4;}
        int  e;
        virtual int fune() {}
    };   // 24
  
    class F : virtual public A, virtual public A1, virtual public B {
        int f;
        virtual int funf(){}
    };   // 40
  
    class G : virtual public A, virtual public A1, virtual public A2 {
        virtual int fung(){}
    };   // 44
  
  
    class H : virtual public B {
        int h;
        virtual int funh();
    };    // 16
  
    class J {
        //int j;  // 若增加该语句，sizeof(I) 为24
        virtual int funj();
    };    //8
  
    class I : virtual public J {
        int i;
        virtual int funi();
    };   // 12
```

1. 空类：1

2. 非虚继承

	- 父类A无虚函数，子类B有虚函数：sizeof(B) = A数据 + B数据 + sizeof(vptr)
	- 父类A有虚函数，子类B有虚函数: sizeof(B) = A数据 + B数据 + sizeof(vptr)

	-  父类A、B、C均有虚函数，子类D有虚函数: sizeof(D) = A数据 + B数据 + C数据 + D数据 + 3*sizeof(vptr) （D的虚函数增加在继承的第一个类A的虚函数表里）

3. 虚继承

	- 父类A无虚函数，子类B有虚函数：sizeof(B) = A数据 + B数据 + sizeof(vptr)

	- 父类A有虚函数，子类B有虚函数: sizeof(B) = A数据 + B数据 + 2*sizeof(vptr)
	- 父类A、B、C均有虚函数，子类D有虚函数: sizeof(D) = A数据 + B数据 + C数据 + D数据 + 4*sizeof(vptr)
- 父类A有虚函数，但无数据，子类B有虚函数：sizeof(B) = B数据 + sizeof(vptr)

-----

* #### 写string类的构造，析构，拷贝函数。
   ``` cpp
  class String {
  public:
      String() {  // 默认构造函数
          len = 0;
          str = new char[len + 1];
          str[0] = '\0';
      }

      String(const char* s) {  // 构造函数
          len = strlen(s);
          str = new char[len + 1];
          strcpy(str, s);
      }

      String(const String& s) {  // 赋值构造函数
          len = s.len;
          str = new char[len + 1];
          strcpy(str, s.str);
      }

      ~String() {   // 析构函数
          delete[] str;
      }

      String& operator=(const String& s) {  // 拷贝函数
          if (this == &s) return *this;
          delete[] str;
          len = s.len;
          str = new char[len + 1];
          strcpy(str, s.str);
          return *this;
      }
      // 以上拷贝函数先delete在分配内存，如果内存分配失败将导致程序奔溃，可做以下修改：
      String& operator=(const String& s) {
          if (this != &s) {
              String strTemp(s);  // 调用复制构造函数，先分配好内存
              char* p = strTemp.str;
              strTemp.str = str;  // 指向原本就该delete的this->str,函数结束时strTemp会被析构，str指向的空间也会被释放
              str = p;
          }
          return *this;
      }

      String& operator=(const char* s) {  // 拷贝函数
          delete[] str;
          len = strlen(s);
          str = new char[len + 1];
          strcpy(str, s);
          return *this;
      }
  private:
      char* str;
      int len;
  };
  ```
-----

* #### 宏定义和展开
  ```cpp
  #include <stdio.h>
  #define f(a,b) a##b
  #define g(a)  #a
  #define h(a) g(a)
  int main()
  {
    printf("%s/n",h(f(1,2)));   // 12
    printf("%s/n",g(f(1,2)));   // f(1,2)
    return 0;
  }
  ```
  

\# : 将右边的参数做整体的字符串替换。
  	对于#的参数，即便是另一个宏，也不展开，仍然作为字符串字面信息输出。所以，g(f(1,2)) 为 f(1,2)。

​	对于h(f(1,2))，由于h(a)是非#或##的普通宏，需要先宏展开其参数a，即展开f(1,2)为12，则h(a) 宏替换为h(12)，进而宏替换为g(12), 进而宏替换为12

\## : 将左右两边的参数做整体的字符串拼接替换，则f(1,2)为12。
  	同#，对于##的参数，即便是另一个宏，也不展开，仍然作为字符串字面信息输出。此外，有一个限制是，经过##替换后的内容必须能够作为一个合法的变量。

-----

* #### 哪些库函数属于高危函数，为什么？
	标准库中的许多字符串处理和IO流读取函数是导致缓冲区溢出的罪魁祸首。
  
  - strcpy() 函数将源字符串复制到缓冲区。没有指定要复制字符的具体数目！如果源字符串碰巧来自用户输入，且没有专门限制其大小，则有可能会造成缓冲区溢出！可以使用strncpy() 来完成同样的目的。

  - strcpy_s版本之所以安全，是因为他们在接口增加了一个参数numElems来表明dest中的字节数，防止目标指针dest中的空间不够而导致出现Bug，同时返回值改成返回错误代码，而不是为了一些所谓的方便而返回char*。这样接口的定义就比原来安全很多。

  - `void * memcpy( void *dst, const void *src, size_t len);`

    当len超过dst实际内存大小时，存在踩越界；

  - `char* strcpy(char* dest,const char* src)`

    当src字符串长度超过dest字符串长度，存在踩越界；

  - `char * strncpy( void *dst, const void *src, size_t len);`

    当len超过dst实际内存大小时，存在踩越界；
    当len小于src实际内存大小时，实际按len大小拷贝到dest中，不会置结束符，可能会存在访问越界风险。

  - `int sprintf(char * buf, const char *fmt, …）`

    当参数fmt变长字符串长度超过buf长度，存在踩越界；

  - `char* strcat(char* dest,const char* src)`

    当src字符串长度超过dest剩余长度，存在踩越界；

  - `char * strncat(char \*dest, const char \*src, size_t count) `

    当count超过dest剩余内存大小时，存在踩越界；

---

* #### 哪些函数不能成为虚函数？

  **不能被继承的函数和不能被重写的函数。**

  1. 普通函数

     普通函数不属于成员函数，是不能被继承的。普通函数只能被重载，不能被重写，因此声明为虚函数没有意义。因为编译器会在编译时绑定函数。

     而多态体现在运行时绑定。通常通过基类指针指向子类对象实现多态。

  2. 友元函数

     友元函数不属于类的成员函数，不能被继承。对于没有继承特性的函数没有虚函数的说法。

  3. 构造函数

     首先说下什么是构造函数，构造函数是用来初始化对象的。假如子类可以继承基类构造函数，那么子类对象的构造将使用基类的构造函数，而基类构造函数并不知道子类的有什么成员，显然是不符合语义的。从另外一个角度来讲，多态是通过基类指针指向子类对象来实现多态的，在对象构造之前并没有对象产生，因此无法使用多态特性，这是矛盾的。因此构造函数不允许继承。

  4. 内联成员函数

     我们需要知道内联函数就是为了在代码中直接展开，减少函数调用花费的代价。也就是说内联函数是在编译时展开的。而虚函数是为了实现多态，是在运行时绑定的。因此显然内联函数和多态的特性相违背。

  5. 静态成员函数

     首先静态成员函数理论是可继承的。但是静态成员函数是编译时确定的，无法动态绑定，不支持多态，因此不能被重写，也就不能被声明为虚函数。

---

* #### 程序加载时内存图

![img](https://github.com/Ty-Chen/Awesome-Backend/blob/master/1118296-20180731235515567-269130492.png)

​		在多任务操作系统中，每个进程都运行在一个**属于自己的虚拟内存**中，而虚拟内存被分为许多页，并映射到物理内存中，被加载到物理内存中的文件才能够被执行。这里我们主要关注程序被装载后的内存布局，其可执行文件包含了代码段，数据段，BSS段，堆，栈等部分，其分布如上图所示。

  - **代码段(.text)** 用来存放可执行文件的机器指令。存放在只读区域，以防止被修改。
  - **只读数据段(.rodata)：** 用来存放常量存放在只读区域，如字符串常量、全局const变量等。
  - **可读写数据段(.data)： ** 用来存放可执行文件中已初始化全局变量，即静态分配的变量和全局变量。
  - **BSS段(.bss)：**未初始化的全局变量和局部静态变量以及初始化为0的全局变量一般放在.bss的段里，以节省内存空间。eg:static int a=0;(初始化为0的全局变量（静态变量）放在.bss)。
  - **堆**：用来容纳应用程序动态分配的内存区域。当程序使用malloc或new分配内存时，得到的内存来自堆。堆通常位于栈的下方。
  - **栈**：用于维护函数调用的上下文。栈通常分配在用户空间的最高地址处分配。
  - **动态链接库映射区**：如果程序调用了动态链接库，则会有这一部分。该区域是用于映射装载的动态链接库。
  - **保留区**：内存中受到保护而禁止访问的内存区域。

---

* #### 溢出、越界、泄露

  1.溢出

  要求分配的内存超出了系统能给你的，系统不能满足需求，于是产生溢出。

  1）栈溢出

  a.栈溢出是指函数中的局部变量造成的溢出（注：函数中形参和函数中的局部变量存放在栈上）

  栈的大小通常是1M-2M,所以栈溢出包含两种情况，一是分配的的大小超过栈的最大值，二是分配的大小没有超过最大值，但是接收的buff比新buff小（buff：缓冲区, 它本质上就是一段存储数据的内存）

  例子1：（分配的的大小超过栈的最大值）

  ```
  void
  {
      char a[99999999999999999];
  }
  ```

  例子2：（接收的buff比新buff小）

  ```
  void
  {
     char a[10] = {0};
     strcpy(a, "abjjijjlljiojohihiihiiiiiiiiiiiiiiiiiiiiiiiiii");
  }
  ```

  注意：调试时栈溢出的异常要在函数调用结束后才会检测到，因为栈是在函数结束时才会开始进行出栈操作

  如：

  [![复制代码](https://common.cnblogs.com/images/copycode.gif)](javascript:void(0);)

  ```
  int main(int argc, char* argv[])
  
  {
  char a[10] = {0};
  
  strcpy(a, "abjjijjlljiojohihiihiiiiiiiiiiiiiiiiiiiiiiiiii");
  
  exit(0);
  
  return 0;
  
  }
  ```

  [![复制代码](https://common.cnblogs.com/images/copycode.gif)](javascript:void(0);)

  上面情况是检测不到栈溢出的，因为函数还没执行完就退出了

  [![复制代码](https://common.cnblogs.com/images/copycode.gif)](javascript:void(0);)

  ```
  void fun()
  {
   char a[10] = {0};
   strcpy(a, "abjjijjlljiojohihiihiiiiiiiiiiiiiiiiiiiiiiiiii");
  }
  int main(int argc, char* argv[])
  {
    fun();
    exit(0);
    return 0;
  }
  ```

  [![复制代码](https://common.cnblogs.com/images/copycode.gif)](javascript:void(0);)

  这种情况调用完fun函数就会检测到异常了

  b.栈溢出的解决办法

  如果是超过栈的大小时，那就直接换成用堆；如果是不超过栈大小但是分配值小的，就增大分配的大小

  2）内存溢出

  使用malloc和new分配的内存，在拷贝时接收buff小于新buff时造成的现象

  解决：增加分配的大小

  2.越界

  越界通常指的是数组越界，如

  char a[9]={0};

  cout << a[9] << endl;

  3.泄漏

  这里泄漏通常是指堆内存泄漏，是指使用malloc和new分配的内存没有释放造成的

---

* #### C/C++中内存分配方法

  1) malloc 函数： void *malloc(unsigned int size)

     在内存的动态分配区域中分配一个长度为size的连续空间，如果分配成功，则返回所分配内存空间的首地址，否则返回NULL，申请的内存不会进行初始化。

  2）calloc 函数： void *calloc(unsigned int num, unsigned int size)

     按照所给的数据个数和数据类型所占字节数，分配一个 num * size 连续的空间。

    calloc申请内存空间后，会自动初始化内存空间为 0，但是malloc不会进行初始化，其内存空间存储的是一些随机数据。 
  3）realloc 函数： void *realloc(void *ptr, unsigned int size)

    动态分配一个长度为size的内存空间，并把内存空间的首地址赋值给ptr，把ptr内存空间调整为size。

    申请的内存空间不会进行初始化。
  4）new是动态分配内存的运算符，自动计算需要分配的空间，在分配类类型的内存空间时，同时调用类的构造函数，对内存空间进行初始化，即完成类的初始化工作。动态分配内置类型是否自动初始化取决于变量定义的位置，在函数体外定义的变量都初始化为0，在函数体内定义的内置类型变量都不进行初始化。

---

STL相关问题
-----

* #### 具体说明STL如何实现vector
  ​	vector的内部是使用动态数组的方式来实现的，如果动态数组的内部实现不够用，就要动态的重新分配内存。然后把原数组的内容拷贝过去。

-----

* #### vector和list的区别
  ​	vector和数组类似，拥有连续的内存空间，支持随机的存取，在中间进行元素的插入和删除的操作时间复杂度是O(n)
  ​	list是由双向链表实现的，只能通过数组指针来进行数据访问，遍历中间的元素，时间的复杂度是O(n).

-----

* #### 如何选择使用vector或dequeue
  一般情况下使用vector,在需要从首尾两端进行插入或删除操作的时候需要选择dequeue.

-----

* #### map内部的实现
  ​	map的底层是一棵红黑树，在对节点的插入或是删除操作中，通过旋转来保持平衡性，最坏情况下的插入、删除、查找时间是O(logn)
  
  map和hashmap的区别
  
  * 底层数据结构不同，map是红黑树，hashmap是哈希表
* map元素可以自动按照键值排序，hashmap的各项操作平均时间复杂度接近常数
* map是C++标准的一部而hashmap并不是（unsorted_map是标准库了）

------

* #### 为何map和set的插入删除效率比用其他序列容器高？
  

-----

* #### 每次insert之后，以前保存的iterator是否会失效？
  ​	对于vector来说，每一次删除和插入，指针都有可能失效，调用push_back在尾部插入也是如此。因为为了保证内部数据的连续存放，iterator指向的那块内存在删除和插入过程中可能已经被其他内存覆盖或者内存已经被释放了。即使时push_back的时候，容器内部空间可能不够，需要一块新的更大的内存，只有把以前的内存释放，申请新的更大的内存，复制已有的数据元素到新的内存，最后把需要插入的元素放到最后，那么以前的内存指针自然就不可用了。特别时在和find等算法在一起使用的时候，牢记这个原则：不要使用过期的iterator。

-----

* #### 为何map和set不能像vector一样有个reserve函数来预分配数据？
  ​	在map和set内部存储的已经不是元素本身了，而是包含元素的节点。也就是说map内部使用的Alloc并不是map<Key,Data, Compare, Alloc>声明的时候从参数中传入的Alloc

-----

* #### hash_map和map的区别在哪里？
    | |map               |unordered_map  |
|--|--|--|
    |Ordering          | increasing  order  ( by default )   |no ordering|
    |Implementation|Self balancing BST ( like RBT )|Hash Table|
    |Search Time|log(n)| average O(1), worst O(n)|
    |Insertion Time|log(n) + Rebalance| Same as search|
    |Deletion Time| log(n) + Rebalance|Same as search|

-----
* #### 什么时候需要用hash_map，什么时候需要用map?
  在以下场景使用std::map更优：
  1. 当需要排序数据
  2. 当需要顺序打印、访问数据
  3. 当需要找数据的前后项的时候
  
  
  相反的，在以下场合更适宜使用std::unsorted_map
  1. 不需要数据排序
  2. 需要统计数据或对增删有严格时间要求
  3. 需要快速访问单个元素

-----

* #### map用[]和find查找有什么区别？
  ​	map的下标运算符[]的作用是：将关键码作为下标去执行查找，并返回对应的值；如果不存在这个关键码，就将一个具有该关键码和值类型的默认值的项插入这个map。
​	map的find函数：用关键码执行查找，找到了返回该位置的迭代器；如果不存在这个关键码，就返回尾迭代器。

-----

* #### map对key的要求？
  1. 支持拷贝构造
2. 支持operator=
3. operator< 如果没有operator<那么 map模板必须增加第三个模板参数
4. 默认的构造函数

-----

* #### vector和链表的区别
    1. vector为存储的对象分配一块连续的地址空间，随机访问效率很高。但是插入和删除需要移动大量的数据，效率较低。尤其当vector中存储的对象较大，或者构造函数复杂，则在对现有的元素进行拷贝的时候会执行拷贝构造函数。
  2. list中的对象是离散的，随机访问需要遍历整个链表，访问效率比vector低。但是在list中插入元素，尤其在首尾插入，效率很高，只需要改变元素的指针。
  3. vector是单向的，而list是双向的；
  4. vector中的iterator在使用后就释放了，但是链表list不同，它的迭代器在使用后还可以继续用。

  使用原则：
  
  - 如果需要高效的随机存取，而不在乎插入和删除的效率，使用vector；
  - 如果需要大量高效的删除插入，而不在乎存取时间，则使用list；
  - 如果需要高效的随机存取，还要大量的首尾的插入删除则建议使用deque，它是list和vector的折中；

-----
* ####  vector中size()和capacity()的区别
  
  - size()指容器当前拥有的元素个数（对应的resize(size_type)会在容器尾添加或删除一些元素，来调整容器中实际的内容，使容器达到指定的大小。）；capacity（）指容器在必须分配存储空间之前可以存储的元素总数。
  
  - size表示的这个vector里容纳了多少个元素，capacity表示vector能够容纳多少元素，它们的不同是在于vector的size是2倍增长的。如果vector的大小不够了，比如现在的capacity是4，插入到第五个元素的时候，发现不够了，此时会给他重新分配8个空间，把原来的数据及新的数据复制到这个新分配的空间里。（会有迭代器失效的问题）

-----
* #### vector、map、multimap、unordered_map、unordered_multimap的底层数据结构，以及几种map容器如何选择？
  **底层数据结构**：
  
  - vector基于**数组**，map、multimap基于**红黑树**，unordered_map、unordered_multimap基于**哈希表**。
  
  **根据应用场景进行选择：**
  
  - map/unordered_map **不允许重复元素**
  - multimap/unordered_multimap **允许重复元素**
  - map/multimap **底层基于红黑树，元素自动有序，且插入、删除效率高**
  - unordered_map/unordered_multimap **底层基于哈希表，故元素无序，查找效率高。**

-----
* #### vector扩容原理说明
  
  * 新增元素：Vector通过一个连续的数组存放元素，如果集合已满，在新增数据的时候，就要分配一块更大的内存，将原来的数据复制过来，释放之前的内存，在插入新增的元素；
  
  * 对vector的任何操作，一旦引起空间重新配置，指向原vector的所有迭代器就都失效了 ；
  
  * 初始时刻vector的capacity为0，塞入第一个元素后capacity增加为1；
  
  * 不同的编译器实现的扩容方式不一样，VS2015中以1.5倍扩容，GCC以2倍扩容。
  
    vector在push_back以成倍增长可以在均摊后达到O(1)的事件复杂度，相对于增长指定大小的O(n)时间复杂度更好。
  
    为了防止申请内存的浪费，现在使用较多的有2倍与1.5倍的增长方式，而1.5倍的增长方式可以更好的实现对内存的重复利用，因为更好。

---

C++ 11
-----

* #### 什么是auto_ptr以及如何使用
  ​	auto_ptr可以代替指针进行类似指针的操作，并不用关心内存的释放，auto_ptr的析构函数自动释放它绑定的动态分配对象。

-----

* #### auto_ptr的实现
  - 每次创建类的新对象的时候，初始化指针并将引用计数设置为1；
  - 当对象作为另一个对象的副本而创建的时候，拷贝构造函数，拷贝指针并增加与之相对应的引用计数
  - 对一个对象进行赋值的时候，赋值操作符减少左操作数所指对象的引用计数，并增加右操作数所指对象的引用计数
  - 调用析构函数的时候减少引用计数，如果引用计数减少至0，则删除基础对象
  - 重载*操作符和->操作符，使auto_ptr有类似于普通指针的操作

-----

* #### 使用auto_ptr的限制
  auto_ptr所所指向的对象要求只能拥有一个拥有者
如下用法是错误的
  
  ``` cpp
  classA *pA = new classA;
  auto_ptr<classA> ptr1(pA);
  auto_ptr<classB> ptr2(pA);
  ```
  auto_ptr不能以传值的方式进行传递b不支持数组

-----



* #### 说几个C++11的新特性
  - auto类型推导
  - 范围for循环
  - lambda函数
  - override 和 final 关键字
  
  ```cpp
  /*如果不使用override，当你手一抖，将foo()写成了f00()会怎么样呢？结果是编译器并不会报错，因为它并不知道你的目的是重写虚函数，而是把它当成了新的函数。如果这个虚函数很重要的话，那就会对整个程序不利。
  
  　　所以，override的作用就出来了，它指定了子类的这个虚函数是重写的父类的，如果你名字不小心打错了的话，编译器是不会编译通过的：*/
  class A
  {
      virtual void foo();
  }
  class B :public A
  {
      void foo(); //OK
      virtual foo(); // OK
      void foo() override; //OK
  }
  
  class A
  {
      virtual void foo();
  };
  class B :A
  {
      virtual void f00(); //OK
      virtual void f0o()override; //Error 
  };
  ```
  
  ```c++
  /*当不希望某个类被继承，或不希望某个虚函数被重写，可以在类名和虚函数后添加final关键字，添加final关键字后被继承或重写，编译器会报错。例子如下：*/
  
  class Base
  {
      virtual void foo();
  };
   
  class A : Base
  {
      void foo() final; // foo 被override并且是最后一个override，在其子类中不可以重写
      void bar() final; // Error: 父类中没有 bar虚函数可以被重写或final
  };
  
  class B final : A // 指明B是不可以被继承的
  {
      void foo() override; // Error: 在A中已经被final了
  };
   
  class C : B // Error: B is final
  {
  };
  ```
  

-----

* #### 智能指针
  
  - 智能指针是在 <memory> 头文件中的std命名空间中定义的，该指针用于确保程序不存在内存和资源泄漏且是异常安全的。它们对RAII“获取资源即初始化”编程至关重要，RAII的主要原则是为将任何堆分配资源（如动态分配内存或系统对象句柄）的所有权提供给其析构函数包含用于删除或释放资源的代码以及任何相关清理代码的堆栈分配对象。大多数情况下，当初始化原始指针或资源句柄以指向实际资源时，会立即将指针传递给智能指针。
  - 智能指针的设计思想：将基本类型指针封装为类对象指针（这个类肯定是个模板，以适应不同基本类型的需求），并在析构函数里编写delete语句删除指针指向的内存空间。
    unique_ptr只允许基础指针的一个所有者。unique_ptr小巧高效；大小等同于一个指针且支持右值引用，从而可实现快速插入和对STL集合的检索。
  - shared_ptr采用引用计数的智能指针，主要用于要将一个原始指针分配给多个所有者（例如，从容器返回了指针副本又想保留原始指针时）的情况。当所有的shared_ptr所有者超出了范围或放弃所有权，才会删除原始指针。大小为两个指针；一个用于对象，另一个用于包含引用计数的共享控制块。最安全的分配和使用动态内存的方法是调用
  - make_shared标准库函数，此函数在动态分配内存中分配一个对象并初始化它，返回对象的shared_ptr。
  
  **注**：**1.引用计数问题**
  
  * 每个shared_ptr所指向的**对象**都有一个引用计数，它记录了有多少个shared_ptr指向自己
  * shared_ptr的析构函数：递减它所指向的对象的引用计数，如果引用计数变为0，就会销毁对象并释放相应的内存
  * 引用计数的变化：决定权在shared_ptr，而与对象本身无关
  
  参考：https://www.cnblogs.com/xzxl/p/7852597.html
  
  　**2.智能指针支持的操作**
  
  	- 使用重载的->和\*运算符访问对象。
  	- 使用**get成员函数获取原始指针**，提供对原始指针的直接访问。你可以使用智能指针管理你自己的代码中的内存，还能将原始指针传递给不支持智能指针的代码。
  	- 使用删除器定义自己的释放操作。
  	- 使用**release**成员函数的作用是放弃智能指针对指针的控制权，将智能指针置空，并返回原始指针。（只支持unique_ptr）
  	- 使用**reset**释放智能指针对对象的所有权。
  

	[![复制代码](https://common.cnblogs.com/images/copycode.gif)](javascript:void(0);)

```cpp
#include <iostream>
#include <string>
#include <memory>
using namespace std;

class base
{
public:
    base(int _a): a(_a)    {cout<<"构造函数"<<endl;}
    ~base()    {cout<<"析构函数"<<endl;}
    int a;
};

int main()
{
    unique_ptr<base> up1(new base(2));
    // unique_ptr<base> up2 = up1;   //编译器提示未定义
    unique_ptr<base> up2 = move(up1);  //转移对象的所有权 
    // cout<<up1->a<<endl; //运行时错误 
    cout<<up2->a<<endl; //通过解引用运算符获取封装的原始指针 
    up2.reset(); // 显式释放内存 

    shared_ptr<base> sp1(new base(3));
    shared_ptr<base> sp2 = sp1;  //增加引用计数 
    cout<<"共享智能指针的数量："<<sp2.use_count()<<endl;  //2
    sp1.reset();  //
    cout<<"共享智能指针的数量："<<sp2.use_count()<<endl;  //1
    cout<<sp2->a<<endl; 
    auto sp3 = make_shared<base>(4);//利用make_shared函数动态分配内存 
}
```

	[![复制代码](https://common.cnblogs.com/images/copycode.gif)](javascript:void(0);)

 　	**3.智能指针的陷阱（循环引用等问题）**

	    [链接](https://blog.csdn.net/y1196645376/article/details/53023848)
	
	[![复制代码](https://common.cnblogs.com/images/copycode.gif)](javascript:void(0);)

```cpp
class B;
class A
{
public:
　　shared_ptr<B> m_b;
};
class B
{
public:
　　shared_ptr<A> m_a;
};

int main()
{
　　{
　　　　shared_ptr<A> a(new A);  //new出来的A的引用计数此时为1
　　　　shared_ptr<B> b(new B);  //new出来的B的引用计数此时为1
　　　　a->m_b = b;              //B的引用计数增加为2
　　　　b->m_a = a;              //A的引用计数增加为2
　　}
　　//b先出作用域，B的引用计数减少为1，不为0；
　　//所以堆上的B空间没有被释放，且B持有的A也没有机会被析构，A的引用计数也完全没减少

　　//a后出作用域，同理A的引用计数减少为1，不为0，所以堆上A的空间也没有被释放
}
```

-----

* #### 