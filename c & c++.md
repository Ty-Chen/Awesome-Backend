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

* #### 初始化序列和构造函数谁先执行
  答：

-----
* #### gdb调试进程
  答：

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
  
- static 数据成员是属于整个类的，不属于某个对象，必须在内定义的外部进行定义，在类定义体中定义是错误的。
  - 静态成员函数不能访问非静态函数或者变量。static函数中不能使用this关键字，不能声明为virtual虚函数。
  
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
  ```


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
  答：  
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
  ```


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
  a.空类：1
  b.非虚继承

  父类A无虚函数，子类B有虚函数：sizeof(B) = A数据 + B数据 + sizeof(vptr)
  父类A有虚函数，子类B有虚函数: sizeof(B) = A数据 + B数据 + sizeof(vptr)
  父类A、B、C均有虚函数，子类D有虚函数: sizeof(D) = A数据 + B数据 + C数据 + D数据 + 3*sizeof(vptr) （D的虚函数增加在继承的第一个类A的虚函数表里）
  c.虚继承

  父类A无虚函数，子类B有虚函数：sizeof(B) = A数据 + B数据 + sizeof(vptr)
  父类A有虚函数，子类B有虚函数: sizeof(B) = A数据 + B数据 + 2*sizeof(vptr)
  父类A、B、C均有虚函数，子类D有虚函数: sizeof(D) = A数据 + B数据 + C数据 + D数据 + 4*sizeof(vptr)
  父类A有虚函数，但无数据，子类B有虚函数：sizeof(B) = B数据 + sizeof(vptr)

-----

* #### 写string类的构造，析构，拷贝函数。
  答：
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
  答：
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

  \# 将右边的参数做整体的字符串替换。
  对于#的参数，即便是另一个宏，也不展开，仍然作为字符串字面信息输出。所以，g(f(1,2)) 为 f(1,2)。

  对于h(f(1,2))，由于h(a)是非#或##的普通宏，需要先宏展开其参数a，即展开f(1,2)为12，则h(a) 宏替换为h(12)，进而宏替换为g(12), 进而宏替换为12

  \## 将左右两边的参数做整体的字符串拼接替换，则f(1,2)为12。
  同#，对于##的参数，即便是另一个宏，也不展开，仍然作为字符串字面信息输出。此外，有一个限制是，经过##替换后的内容必须能够作为一个合法的变量。

-----

* #### 哪些库函数属于高危函数，为什么？
答：标准库中的许多字符串处理和IO流读取函数是导致缓冲区溢出的罪魁祸首。高危函数
  strcpy() 函数将源字符串复制到缓冲区。没有指定要复制字符的具体数目！如果源字符串碰巧来自用户输入，且没有专门限制其大小，则有可能会造成缓冲区溢出！
  也可以使用strncpy() 来完成同样的目的。

  而strcpy_s版本之所以安全，是因为他们在接口增加了一个参数numElems来表明dest中的字节数，防止目标指针dest中的空间不够而导致出现Bug，同时返回值改成返回错误代码，而不是为了一些所谓的方便而返回char*。这样接口的定义就比原来安全很多。

  `void * memcpy( void *dst, const void *src, size_t len);`

  当len超过dst实际内存大小时，存在踩越界；

  `char* strcpy(char* dest,const char* src)`

  当src字符串长度超过dest字符串长度，存在踩越界；

  `char * strncpy( void *dst, const void *src, size_t len);`

  当len超过dst实际内存大小时，存在踩越界；
  当len小于src实际内存大小时，实际按len大小拷贝到dest中，不会置结束符，可能会存在访问越界风险。

  `int sprintf(char * buf, const char *fmt, …）`

  当参数fmt变长字符串长度超过buf长度，存在踩越界；

  `char* strcat(char* dest,const char* src)

  当src字符串长度超过dest剩余长度，存在踩越界；

  `char * strncat(char \*dest, const char \*src, size_t count) `

  当count超过dest剩余内存大小时，存在踩越界；

-----

* ####
答：

-----

* ####
答：

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
    | |map               |unordered_map  |
|--|--|--|
    |Ordering          | increasing  order  ( by default )   |no ordering|
    |Implementation|Self balancing BST ( like RBT )|Hash Table|
    |Search Time|log(n)| average O(1), worst O(n)|
    |Insertion Time|log(n) + Rebalance| Same as search|
    |Deletion Time| log(n) + Rebalance|Same as search|

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

* #### map用[]和find查找有什么区别？
  答：map的下标运算符[]的作用是：将关键码作为下标去执行查找，并返回对应的值；如果不存在这个关键码，就将一个具有该关键码和值类型的默认值的项插入这个map。
map的find函数：用关键码执行查找，找到了返回该位置的迭代器；如果不存在这个关键码，就返回尾迭代器。

-----

* #### map对key的要求？
  答：1 支持拷贝构造
2 支持operator=
3 operator< 如果没有operator<那么 map模板必须增加第三个模板参数
4 默认的构造函数

-----

* #### vector和链表的区别
    答：  （1）vector为存储的对象分配一块连续的地址空间，随机访问效率很高。但是插入和删除需要移动大量的数据，效率较低。尤其当vector中存储的对象较大，或者构造函数复杂，则在对现有的元素进行拷贝的时候会执行拷贝构造函数。
  （2）list中的对象是离散的，随机访问需要遍历整个链表，访问效率比vector低。但是在list中插入元素，尤其在首尾插入，效率很高，只需要改变元素的指针。
  （3）vector是单向的，而list是双向的；
  （4）vector中的iterator在使用后就释放了，但是链表list不同，它的迭代器在使用后还可以继续用。

  使用原则：
  （1）如果需要高效的随机存取，而不在乎插入和删除的效率，使用vector；
  （2）如果需要大量高效的删除插入，而不在乎存取时间，则使用list；
  （3）如果需要高效的随机存取，还要大量的首尾的插入删除则建议使用deque，它是list和vector的折中；

-----
* ####
  答：

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
