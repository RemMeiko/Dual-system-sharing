### 文章

[第一份面试题C++](https://blog.csdn.net/weixin_42060900/article/details/96503788)

[陈皓杂谈](https://blog.csdn.net/haoel?type=blog)

### static关键字

> 静态数据成员是类的一部分，为类的所有实例共享(静态区)，通过类名:静态成员变量名y访问
>
> 非静态数据成员，类的每个实例都有一份拷贝(动态区)

> * 存储在静态数据区的变量会在程序刚开始运行时完成初始化，也是**唯一**一次初始化
> * 存储在静态数据区的变量：全局变量、static变量
>   * static可控制全局变量的可见范围
> * static局部变量
>   * 生存期为整个源程序，作用域局限于函数，退出该函数后该变量继续存在，但不能再使用
> * 静态数据区的内存中字节的默认值都是0x00

* 隐藏作用

  所有未加static前缀的全局变量和函数都具有全局可见性

  如果加了static，就会对其他源文件隐藏，函数、变量加上static，其他文件便无法看到，利用此特性可在不同文件中定义同名函数和同名变量，而不必担心冲突

* 保持变量内容的持久

* 默认初始化为0---根据不同编译器决定

  将数据存储在静态数据区系统会进行默认初始化

static成员变量一定要在类外初始化

* 类的静态成员必须在类内声明，在类外初始化

  ```c++
  class A {
      privite:
      	static int count; // 类内声明
  }
  int A::count = 0;// 类外定义并初始化，不必加static关键字
  int A::count; // 只定义不赋值，默认值视编译器而定
  ```

  > 因为静态成员属于整个类，而不属于某个对象，如果在类内初始化，会导致每个对象都包含该静态成员

  `static const int`可以在类里面初始化

  只有静态常量成员，常量必须初始化

* 不能在头文件中声明static的全局函数，不要再cpp内 声明非static的全局函数，如果要在多个cpp中复用该函数，就把声明提到头文件中去，否则cpp内部声明需加上static修饰

### C和 C++的区别

1. 设计思想上：C++是面向对象的，C语言是面向过程的结构化编程语言
2. 语法上：C++具有封装、继承、多态三种特性，相比C增加了强制类型转换等类型安全功能;支持范式编程，如模板类、函数模板等

### C++三种特性

* 封装
  * 把客观事物封装程抽象的类
* 继承
  * 使用现有类的所有功能，在无需编写原来的类的情况下对这些功能进行扩展
* 多态
  * 一个接口，多种实现

### C++空类

* C++空类默认含有6个成员函数：

  ```c++
  class Empty {
      public:
      	Empty(); // 缺省构造函数
      	Empty(const Empty&); //拷贝构造函数
      	~Empty();  // 析构函数
      	Empty & opeartor=(const Empy&);  // 赋值运算符
      	Empty *operator&(); // 取值运算符
      	const Empty* operator&() const; // 取值运算符const
  }
  ```

* 空类占用1字节是为了让每个实例在内存中都有独一无二的地址

* 如果类成员函数不是虚函数，则不占空间

* 静态数据成员不占用对象内存空间，只拥有一份实例，所有对象共有

* 如果成员函数中存在虚函数，则所有虚函数保存在虚函数表中，然后类中有一个指针指向该表，该指针需要占用4字节空间

### 虚函数

虚函数：是指被virtual关键字修饰的成员函数

在某基类中声明为virtual并在一个或多个派生类中被重新定义的成员函数

作用：实现多态(Polymorphism)性

关键字：virtual

关键字：用指向基类的指针或引用操作对象

总结：指向基类的指针在操作它的多态类对象时，会根据不同的类对象，调用其相应的函数，这个函数就是虚函数

* 基类成员函数是虚函数，其派生类相应的函数也自动变为虚函数(加不加都可以，为了阅读方便建议加上)
* 具体实现
  * 类中有虚函数，编译器会自动为其创建一个表(vtbl)，并插入一个vptr指针指向那个表，vtbl作用时保存自己类中虚函数的地址，可以将vtbl形象的看成一个数组，这个数组每个元素存放的就是虚函数的地址
  * 调用过程：首先取出vptr的值，根据vtbl的地址找到表，取出对应的虚函数的地址，最后调用这个函数
  * 这个vptr指针存放在他们各自的实例对象
  * 虚函数表的指针一般在实例的最前面，以保证其有最高性能
  * 拥有虚函数的类会创建一个虚函数表，指针
* 静态成员函数、内联函数不能是虚函数
* 构造函数不能是虚函数，析构函数通常声明为虚函数

> 虚函数是运行时多态的基础，函数重裁是编译时多态的基础
>
> [虚函数表博客](https://blog.csdn.net/haoel/article/details/1948051/)

* 虚函数表
  * 若子列重写父类虚函数，在虚函数表中，该函数的地址会被替换

### 多态

简而言之就是用父类型的指针指向其子类的实例，然后通过父类的指针调用实际子类的成员函数。

泛型技术：说白了就是试图使用不变的代码实现可变的算法

接口的多种不同实现方式

* 实现多态的两种方式：重裁和覆盖
  * 覆盖：是指子类重新定义父类的虚函数的做法(真正)
    * 当子类重新定义了父类的虚函数后，父亲指针根据赋给它的不同的子类指针，动态的调用属于子类的该函数，在编译期间无法确定，运行期间进行绑定(晚绑定)
  * 重载：函数的参数表不同(参数个数或类型不同)
    * 函数的地址在编译期就进行了绑定(早绑定)
* 如果方法没有关键字virtual,程序将根据引用类型或指针类型选择方法，如果使用了virtual，程序将根据引用或指针指向的对象的类型选择方法

### 构造函数、析构函数

构造函数：用于新建对象初始化工作

* 不能有返回值，缺省构造函数时系统自动初始化对象，所有数据成员都初始化为0或空

析构函数：用于撤销对象前(对象结束生命周期)，完成一些清理工作，如：释放内存等

* 析构函数必须是虚函数，默认的析构函数不是虚函数
  * 为基类声明虚函数是惯例，为了能够正确的调用析构函数
  * 虚函数需要额外的虚函数表和虚表指针占用额外的内存
* 如果用户没有编写析构函数，编译系统会自动生成一个缺省的析构函数
* 如果一个类中有指针，使用过程动态申请了内存，最好在析构函数销毁类之前，释放掉申请的内存空间，避免内存泄漏
* 类析构函数顺序
  1. 派生类本身的析构函数
  2. 对象成员析构函数
  3. 基类析构函数

### 友函数和友元函数

* 友函数
  * 书写
    * 自己类：`friend 返回值类型 函数名(参数表)`
    * 其他类：`friend 返回值类型 其他类的姓名::成员函数名(参数表)`
  * (可将全局函数和其他类成员函数)声明为"友元",友元函数内部可访问该类的私有成员
* 友元类
  * 一个类A可将另一个类B声明为自己的友元，类B的所有成员函数即可访问类A对象的私有成员
  * 书写：`friend class 类名`

### C++中cast转换

**隐式类型转换**

* 不需要用户干预，编译器默认进行的类型转换行为，分为内置数据类型、自定义数据类型
* 不同系统下有所差异，默认情况，int\short\long是带符号，即signed
* 一些基本类可使用一个或多个类型修饰符修饰：signed/unsigned/short/long

  1. 内置数据类型(基本数据类型)
     * 整型
       * short：2字节
       * int：4字节(32位为2字节)
       * long：4字节
       * long int：4字节
       * long long：8字节
     * 字符型
       * char：1字节
       * char16_t：2字节
       * char32_t：4字节
       * wchar_t：2字节
         * 不同的C\C++库有不同的规定，2字节或4字节，比char大，确保能存放机器基本字符集中任意字符对应的数字值
     * 浮点型
       * float：至少4字节，7个有效位，单精度
       * double：至少8字节，16个有效位，双精度
       * long double：至少和double一样大，16字节
     * 布尔型
       * 1字节
   * 空类型
* 类型转化

  * 非布尔型转布尔型：0-->false，其他-->true
  * 布尔型转非布尔型：false-->0，true-->1
  * 浮点型转整数型：进行近似处理，，结果值保留浮点数之前的证书部分
  * 整型-->浮点型：小数部分记为0，若整数空间超过浮点数容量，精度会有所损失

**typedef声明**

* 可以使用typedef为一个已有类型起一个新的名字
* 如`typedef int feet`，接下来可使用`feet distance`

**枚举类型(enumeration)**

* C++的一种派生类数据，由用户定义的若干枚举常量的集合

* 如果一个变量只有几种可能的值，可以定义为枚举类型，枚举就是将变量的值一一列举出来

* 关键字：enum

  ```c++
  enum 枚举名 {
      标识符【=整型常数】,
      标识符【=整型常数】,
       ...
  }枚举变量
  ```

* 如果未初始化，则从第一个标识符，赋0开始,`enum color {red,green,blue=5,black} c;`，这里black为6，默认情况下，每个名称比前面的大1，但green依旧为1,最后的c表示变量c的类型为color

> [cast强制转换C++](https://www.jianshu.com/p/c0f75ee4efd6)

1. static_cast<new_type>(expression) --- 静态转换

   * 主要用于内置数据类型的相互转化，用于各种隐式转换

   * 用于自定义类时，静态转换会判断转换类型之间的关系，如果转换类型之间没有任何关系，编译器会报错，不可转换

     > 如B继承A，则A和B可以相互转换，二者有关系

   * 把void类型指针转换为目标指针(不安全)

2. const_cast
   * 常量指针被强转为非常量指针，仍指向原来的对象
   * 常量引用被强转为非常量引用，仍指向原来的对象
   * 常量对象被强转为非常量对象
   
3. dynamic_cast
   * 是运行时处理的，运行时进行类型检查，其他三种都时编译时完成
   * 不能用于内置基本数据类型间强制转换
   * 使用动态转换时，基类中一定要有虚函数，不然编译通不过
   * 若转换成功，返回指向类的指针或引用，失败返回NULL
   * 类转换时，在类层次间进行上行转换时和静态转转效果一样，下行转换，dynamic_cast具有类型检查功能，更加安全
   
4. reinterpret_cast

### 引用和指针

**引用**

* 引用是某一变量的别名，对引用的操作和对变量直接操作完全一样，引用声明方法：类型标识符 &引用名 = 目标变量名

**指针**

* 指针利用地址，它的值直接指向存在电脑存储器中另一个地方的值，通过地址能够找到所需的变量单元。

**不同**

* 指针有自己的空间，引用只是别名
* sizeof：指针大小是4，引用这是被引用对象的大小
* 指针可以被初始化为NULL，引用必须被初始化且必须是一个已有对象的引用
* 作为参数传递：指针需要被解引用才可以对对象进行操作，而直接对引用的修改都会改变引用所指向对象
* 可以有const指针，但没有const引用
* 指针可以修改指向其他对象，但引用一经初始化就不能改变
* 指针可有多级指针(**p),引用只有一级
* 指针和引用使用++运算符意义不同
* 返回动态内存分配的对象或内存必须使用指针，引用会引起内存泄露

### 智能指针

> 除了静态内存和栈内存外，每个程序还有一个内存池，这部分内存被称为自由空间或堆，程序用堆来存放运行时对象
>
> 动态内存的管理由一堆运算符：new和delete完成
>
> 动态内存管理出现的问题：
>
> * 一：忘记释放内存，造成内存泄漏
> * 二：尚有指针引用内存的情况下释放了它，就会产生引用非法内存的指针

智能指针是一个类，当超出类的作用域时，类会自动调用析构函数，析构函数会自动释放内存，所以智能指针的作用原理就是函数结束时自动释放内存，不需要手动释放内存

智能指针就是一个将指针封装，然后通过引用计数或是其他方法完成自动释放内存的功能，达到和原有指针一样的功能，也需要对*，->进行重载

shared_ptr允许多个指针指向同一个对象，unique_ptr则“独占”所指向的对象。标准库还定义了一种名为weak_ptr的伴随类，它是一种弱引用，指向shared_ptr所管理的对象，这三种智能指针都定义在memory头文件中

![image-20220321105955554](F:\个人文件\资料\桌面文件\2.记录\md文件图片\image-20220321105955554.png)

### 野指针

定义：访问一个已销毁或者访问受限的内存区域的指针，不能通过是否为NULL进行判断

垂悬指针：指针正常初始化，曾指向一个对象，该对象被销毁，但是指针未制空

* 野指针产生原因
  1. 指针定义时未初始化：如果未初始化，程序会随机指向一块区域
  2. 指针释放时没有置空
  3. 指针操作超越变量作用域
* 规避方法
  1. 初始化指针的时候将其置为nullptr，之后对其操作
  2. 释放指针时候将其置为nullptr

### 函数指针和指针函数

函数的函数名就代表着函数的首地址

**函数指针**

* 是指向函数的指针，本身是一个指针变量

  * 在编译时，每个函数都有一个入口地址，该入口地址就是函数指针所指向的地址

* 用途

  * 调用函数和做函数的参数(如回调函数)

* 定义

  * 函数返回值类型(* 指针变量名)(函数参数列表)

  ```c
  int Func(int x);
  int （*p）(int x);
  p = Func; // 直接将函数名赋值即可
  p(3); // 执行函数Func
```

### fork函数

```c
#include <unistd.h>
pid_t fork(void);
// 返回值：子进程返回0，父进程中返回子进程ID，出错返回-1
```

fork调用一次返回两次，子进程是父进程的副本，如子进程获得父进程的数据空间、堆、栈的副本。父、子进程不共享这些存储空间，父、子进程共享正文段(text，代码段)。

* 现在很多不是执行一个父进程数据段、栈、堆的完全复制，而是作为替代，使用了写时复制(Copy-On-write)技术,这些区域由父、子进程共享，内核将他们访问权限设置为可读
* 一般fork之后父、子进程谁先执行是不确定的，取决内核的调度算法
* 常见做法是创建一个新进程然后使用exec()载入二进制映像替换掉当前进程的映像

### GCC的`_attribute_`(constructor)

GCC的`__attribute__`函数可以设置函数属性

```c
__attribute__((constructor)) // 在main函数被调用之前调用
__attribute__((destructor)) // 在main函数被调用之后调用 
```

举例：

```c
__attribute__((constructor))void before() // 函数名叫before
{
    printf("before main\n");
}
int main(int argc, char **argv) {
   printf("in main\n");
   return 0;
}
```

### const修饰符

1）修饰成员函数

* const对象只能调用const成员函数，不能调用普通成员函数

* const成员函数不能修改类的成员变量，若要修改则用mutable修饰该成员变量

  > const修饰的成员函数表明函数调用不会对对象做出任何更改，如果确认不会对对象做出更改，就应该为函数加上const修饰符，确保所有对象都能调用

2）修饰对象

* 把一个对象转换为常量(constant)，对象在定义时必须初始化
* 初始化后的const对象(或指针)是不能修改

3）其他const面试

* 两个函数，一个带const，一个不带相当于函数的重载

### 分配内存

new/delete	malloc/free

* new/detele时C++关键字，new会调用构造函数，会自动根据类型来确定多少字节内存，不用指定内存大小，返回指针不同强转
* malloc/free是C语言库函数，使用时必须声明申请内存空间的大小，返回的指针需要强转，对于对象，不会调用构造函数和析构函数

### 拷贝构造函数、赋值函数

> [区别](https://blog.csdn.net/zcyzsy/article/details/52132936)

1）构造函数

* 一类特殊的类成员函数，当创建一个类对象时，被调用对类的数据成员进行初始化和分配内存
* 构造函数的命名必须和类名完全相同

2）拷贝构造函数

* C++独有，一种特殊构造函数，基于同一类的一个对象构造和初始化另一个对象
* 没有重载拷贝构造函数时，通过默认拷贝构造函数创建一个对象

> 深拷贝和浅拷贝
>
> * 浅拷贝：复制的对象中引用了一个外部内容(如堆上的数据)，那么复制这个对象的时候，两个对象指向同一个外部内容，就是浅拷贝(指针虽然复制了，但所指向的空间内容并没有复制，而是两个对象共用，两个对象不独立)
>
> * 深拷贝：赋值对象时为新对象制作了外部对象的独立复制

* 拷贝构造函数被调用的情况
  1. 一个对象以值的传递方式传入函数体
  2. 一个对象以值传递的方式从函数返回
  3. 一个对象需要另一个对象进行初始化
* 拷贝构造函数重载：`A(const A &other)`

3）赋值函数

* 一个类的对象向该类的另一个对象赋值时，会用到该类的赋值函数
* 当没有重载赋值函数(赋值运算符)时，通过默认赋值函数进行赋值操作
* 重载声明：`A &operator = (const A &other)`

4）拷贝构造函数和赋值函数

* 拷贝构造函数时一个对象初始化一块内存区域，则会个内存就是新对象的内存区，而赋值函数就是对已初始化的对象进行赋值操作
* 一般数据成员包含指针对象时，有两种情况：一种是复制指针对象，一种是引用指针对象。拷贝函数大多是复制，赋值函数大多是引用
* 实现上：拷贝构造函数调用时通过参数的对象初始化产生一个对象。赋值是把一个对象赋值给一个原有的对象，分配时会先检查是不是一个对象，是则不做处理，如果原由对象中由内存分配则先将内存释放掉

### STL

STL(标准模板库)

#### 基本组成

* 容器
* 算法
* 迭代器
* 仿函数
* 配接器
* 空间配置器：给容器分配存储空间

#### list, vector, map, set

1）**List**

* 动态链表，堆上分配空间，底层：双向链表

* 封装了链表，以链表形式操作数据
* 优缺点
  * 随机访问慢，插入、删除数据快-->只需要修改指针指向
  * 相对于vector，占用内存多

2）**vector**

* 底层实现：数组

* 连续存储的容器，在堆上分配空间，动态数组，封装了数组，使用连续内存存储数据，可像数组一样进行操作，不需要指定大小(STL实现时会通过capacity()函数分配一个非常大的内存空间预备)
* 容量增长：插入元素，未超过容量，直接添加至最后-->调整迭代器。没有剩余空间，重新配置原有元素个数的两倍空间，将原空间元素通过复制的方式初始化新空间，向新空间增加元素，最后析构并释放原空间，之前迭代器失效
* 优缺点
  * 随机访问速度块，尾部插入数据快
  * 插入、删除元素慢
  * 当动态添加数据超过默认分配大小时需要进行整体的重新分配、拷贝和释放
* 使用
  * 头文件`#include <vector>`
  * 属于std命名域,命名限定`using std::vector`或`using namespace std`
  * `vector<int> v;`

> List和Vector区别
>
> 1) vector底层实现时数组，list底层实现是链表
> 2) vector支持随机访问，list不支持
> 3) vector顺序内存，list不是
> 4) vector中间节点插入删除会导致内存拷贝，list不会
> 5) vector一次性分配好空间，不够进行2倍扩容，list每次都会继续宁内存申请
> 6) vector随机访问性能好，插入删除性能差，list随机访问性能差，插入删除性能好

3）**map、set**

属于标准关联容器，使用非常高效的平衡检索二叉树：红黑树，插入删除不需要内存拷贝和移动，直接替换指向节点指针即可

Set和Vector区别在于Set不包含重复数据，Set和Map区别在于Set只含有Key，Map含有一个Key和Key对应的Value两个元素

#### 空间配置器allocaotr

STL空爱你配置器用于封装STL容器在内存管理上的底层细节

* C++其内存配置和释放过程如下
  * new运算两个阶段：1.调用::operator new配置内存 2.调用对象构造函数构造对象内容
  * detele运算分两个阶段：1.调用对象析构函数 2.调用::operator detele释放内存
* STL allocaotr将两个阶段操作分开
  * 内存配置有alloc::allocate()负责
  * 内存释放由alloc::dealocate()负责
  * 对象构造由::construct()负责
  * 对象析构由::destroy()负责
* 为提高内存管理的效率，减少申请小内存造成的内存碎片问题，SGI STL采用两级配置器，当分配空间大小超过128B时，使用第一级空间配置器，当分配的空间大小小于128B，将使用第二级空间配置器
  * 第一级空间配置器直接使用malloc()、realloc()、free()函数
  * 第二级空间配置器采用内存池技术，通过空闲链表来管理内存

#### STL迭代器

**介绍**

* Iterator(迭代器)模式又称为Cursor(游标)模式，用于提供一种方法顺序访问一个聚合对象中的各个元素，而又不需要暴露该对象的内部表示

  > 换种说法就是：Iterator模式是运用于聚合对象的一种模式，通过运用该模式，使得我们可以在不知道对象内部表示的情况下，按照一定的顺序(由Iterator提供的方法)访问聚合对象中的各个元素

* 一般仅用于底层聚合支持类，如STL的list、vector、stack等容器类以及ostream_iterator等扩展类

每个容器都有专属迭代器

访问顺序容器和关联容器中的元素，需要通过"迭代器(iterator)"进行，迭代器是一个变量，相当于容器和操作容器的算法之间的中介

**定义的四种方式**

1）正向迭代器：`容器类名::iterator 迭代器名;`

2）常量正向迭代器：`容器类名::const_iterator 迭代器名;`

3）反向迭代器：`容器类名::reverse_iterator 迭代器名;`

4）常量反向迭代器：`容器类名::const_reverse_iterator 迭代器名;`

通过迭代器可读取它指向的元素，`*迭代器名`就表示迭代器指向的元素，通过非常量迭代器还能修改其指向的元素

**正向和反向迭代器**

* 正向进行++操作，迭代器指向容器的后一个元素
* 反向进行++操作，迭代器指向容器的前一个元素

```c++
#include <iosteam>
#include <vector>
using namespace std;
int main() {
    vector<int> v;
    for(int i = 0;i < 5;i++) 
        v.push_back(i); // 向vector容器尾部添加一个元素
    // 定义正向迭代器
    vector<int>::iterator iter;
    for(iter = v.begin(); iter != v.end(); ++iter) {
        cout << *iter << ""; // *iter就是迭代器指向的元素
    }
    // 定义反向迭代器
    vector<int>::reserve_iterator resIter;
    for(resIter = v.rbegin(); resIter != v.rend(); ++resIter) {
       cout << *resIter;
    }
}
```

不同容器的迭代器功能强弱有所不同，决定了是否支持STL中的某种算法

**迭代器功能分类**

常用迭代器按功能强弱分为输入、输出、正向、双向、随机五种

1）正向迭代器：支持++p,p++,*p操作。两个正向迭代器可相互赋值，还可以用==和!=运算符进行比较

2）双向迭代器：具有正向迭代器全部功能，此外`--p`和`p--`也具有定义，是向相反方向移动

3）随机访问迭代器，具有双向迭代器全部功能。i整型，还支持`p+=i,p-=i,p+i,p-i,p[i]`等操作，另外两个随机访问迭代器，表达式`p2-p1`也是有定义 的，返回p2-p1的序号之差

![image-20220321192224305](F:\个人文件\资料\桌面文件\2.记录\md文件图片\image-20220321192224305.png)

**迭代器的辅助函数**

* advance(p,n)：使迭代器p向前或向后移动n个元素
* distance(p,q)：计算两个迭代器之间的距离，即迭代器p经过多少次++操作后和迭代器q相等，如果调用时p在q后面会陷入死循环
* iter_swap(p,q)：用于交换两个迭代器p、q指向的值

```C++
#include <list>
#include <iostream>
#include <algorithm> //要使用操作迭代器的函数模板，需要包含此文件
using namespace std;
int main()
{
    int a[5] = { 1, 2, 3, 4, 5 };
    list <int> lst(a, a+5);
    list <int>::iterator p = lst.begin();
    advance(p, 2);  //p向后移动两个元素，指向3
    cout << "1)" << *p << endl;  //输出 1)3
    advance(p, -1);  //p向前移动一个元素，指向2
    cout << "2)" << *p << endl;  //输出 2)2
    list<int>::iterator q = lst.end();
    q--;  //q 指向 5
    cout << "3)" << distance(p, q) << endl;  //输出 3)3
    iter_swap(p, q); //交换 2 和 5
    cout << "4)";
    for (p = lst.begin(); p != lst.end(); ++p)
        cout << *p << " ";
    return 0;
}
```

#### 迭代器删除元素

1. 对于序列容器vector、deque、list等来说，删除当前的iteraotor会使得后面所有元素的iterator都失效

   * 因为vector、deque等使用了连续分配的内存，删除一个元素会使后面所有元素向前移动一个位置，不过erase方法会返回下一个有效的iterator

   ```c++
   // 正确的使用方法
   int main() {
       vector<int> val = {1,2,3,4,5};
       vector<int>::iterator iter;
       for(iter = val.begin();iter != val.end();++iter) {
           if(*iter == 3) {
               iter = val.erase(iter);
           }
       }
   }
   ```

2. 对于关联容器（map,set,multimap,multiset）,删除当前的iterator，仅仅会使当前的iterator失效，只要在erase时，递增当前的iterator即可

   * 因为mop之类的容器，使用了红黑树来实现，插入、删除一个节点不会对其他节点造成影响

   ```c++
   int main {
       set<int> valset = {1，2}；
       set<int>::iterator iter;
       for(iter = valset.begin();iter != valset.end();iter++) {
           if(*iter != 2) {
               valset.erase(iter++);
           }
       }
   }
   // 因为传给erase的是iter的一个副本，iter++是下一个有效的迭代器
   ```

#### 迭代器和指针

* 迭代器不是指针，是类模板
* 通过重载指针的一些操作符如->、*、++等表现的像指针
* 迭代器封装了指针，是一个“可遍历STL的容器内全部元素或部分元素”的对象，本质是封装了原生指针
* 提供了比指针更高级的行为，相当于一种智能指针
* 迭代器返回的是对象引用，而不是对象的值
* 迭代器类的访问方式就是把不同集合类的访问逻辑抽象出来，使得不暴露集合内部的结构而达到循环遍历集合的效果

### 类和数据抽象

**类的三种成员类型**

![image-20220322162110222](F:\个人文件\资料\桌面文件\2.记录\md文件图片\image-20220322162110222.png)

类外部的意思是：其他类或main函数之类的

在类的外部，只能通过对象访问成员，并且通过对象只能访问public属性成员，不能访问private、protected属性成员

**类的三种继承方式**

![image-20220322224136497](F:\个人文件\资料\桌面文件\2.记录\md文件图片\image-20220322224136497.png)

**私有成员继承**

* 基类私有成员在派生类中是不可见的：指在派生类的成员函数中或者通过派生类的对象(指针、引用)不能直接访问他们，可以通过基类的共有函数方式间接访问基类的私有成员
* 私有成员会被继承，无法通过正常渠道访问

**struct和class**

struct和class都可定义类，且被继承，struc默认继承权限和默认访问权限是public,类的是private

**继承和动态内存分配**

* 派生类不使用new：不需要为派生类定义显式析构函数、赋值构造函数、赋值运算符
* 派生类使用new：需要显式定义析构函数、赋值构造函数、赋值运算符

**定义引用数据成员**

C++类内定义引用数据成员必须通过成员函数初始化列表初始化

### 面向对象和泛型编程

**左值引用和右值引用**

右值引用是C++11引入的新特性，实现了转移语义和精确传递

* 消除两个对象交互时不必要的对象拷贝，节省运算存储资源，提高效率
* 更简洁明确的定义泛型函数

**左值和右值概念**

* 左值：能对表达式取地址，或具名对象/变量.一般指表达式结束后依然存在的持久对象，变量的地址
  * 引用形式：类型 & a = 被引用的对象
* 右值：不能对表达式取地址，或匿名对象，一般指表达式结束就不再存在的临时对象，变量的值
  * 引用形式：类型 && a = 被引用的对象

**区别**

* 左值可以寻址，右值不可以
* 左值可以被赋值，右值不可以，可以用来给左值赋值
* 对于基础类型，左值可变，右值不可变

### C++源文件经历过程

1. 预处理阶段：对源代码文件中文件包含关系(头文件)、预编译语句(宏定义)进行分析和替换，生成预编译文件
2. 编译阶段：将预编译文件转换成特定汇编代码，生成汇编文件
3. 汇编阶段：将汇编文件转换成机器码，生成可重定位目标文件
4. 链接阶段：将多个目标文件及所需要的库连接成最终的可执行目标文件

### #include

预处理器发现#include指令后，会寻找文件名对应文件，将文件内容包含到当前文件中，被包含的文件中文本将替换源代码中的#include指令

**尖括号**

* 系统自带的文件用尖括号括起来，其寻找目录是
  * 编译器设置的头文件路径(编译器可使用-I显示指定搜索路径)
  * 系统变量CPLUS_INCLUDE_PATH/C_INCUDE_PATH指定的头文件路径

**双引号**

* 用户自定义的文件用双引号括起来，其寻找路径是
  * 当前头文件目录
  * 编译器设置的头文件路径(编译器可使用-I显示指定搜索路径)
  * 系统变量CPLUS_INCLUDE_PATH/C_INCUDE_PATH指定的头文件路径

使用双引号括起来一定是正确的

### malloc原理

Malloc函数用于动态内存分配，为减少内存碎片和系统调用的开销，malloc采用内存池的方式，先申请大块内存作为堆区，然后将堆区分为多个内存块，以块为内存管理基本单位，当用户申请内存时，直接从堆区分配一块合适的空闲块。

* Malloc采用隐式链表结构将堆区分成连续的、大小不一的块，包含已分配的块和为分配块
* malloc采用显式链表结构管理所有空闲块，即用一个双向链表将空闲块连接起来，每一个空闲块记录一个连续的、未分配的地址
* 当进行内存分配时，malloc会通过隐式链表遍历所有空闲块，选择满足要求的块进行分配，当进行内存合并时，malloc采用边界标记法，根据每个块的前后块时候已经分配来决定是否进行块合并
* Malloc申请内存时，一般会通过brk或mmap系统调用进行申请，其中当申请内存小于128K时，会使用系统函数brk在堆中分配，当申请内存大于128K时，会使用系统函数mmap在映射区分配

### 内存管理

C++中虚拟内存分为代码段、数据段、BSS段、堆区、文件映射区、栈区

* 代码段：包括只读存储区和文本区，其中只读存储区存储字符串常量，文本区存储程序的机器代码
* 数据段：存储程序已初始化的全局变量和静态变量
* bss段：存储未初始化的全局变量和静态变量(局部+全局)，以及所有被初始化为0的全局变量和静态变量
* 堆区：调用new/malloc函数时在堆区动态分配内存，同时需要调用delete/free手动释放申请的内存
* 映射区(待定):存储动态链接库以及调用mmap函数进行文件映射
* 栈：存储函数的返回地址、参数、局部变量、返回值

**静态区域和动态区域**

**静态区域**：

* test segment(代码段)
* data segment(数据段)
* bss segment：未初始化的全局变量和静态变量会在main运行之前统一清零

**动态区域**

* heap（堆）：进程未调用malloc时是没有堆段的，只有调用malloc才会分配一个堆，程序运行过曾中动态增加堆大小(移动break指针)，从低地址向高地址增长。分配小内存使用该区域。
  * 堆的起始地址由mm_strcut结构体中的start_brk标识，结束地址由brk标识
* memory mapping segment（映射区)：存储动态链接库等文件映射、申请大内存(malloc时调用mmap函数)
* stack(栈)：创建进程时会有一个最大栈大小，Linux可以通过ulimit命令指定

### 内存泄漏

memory leak(内存泄漏)是指由于疏忽或错误造成了程序未能释放掉不再使用的内存的情况，内存泄漏并非指内存在物理上的消失，而是应用程序分配某段内存后，由于设计错误，失去了对该段内存的控制，因而造成了内存的浪费

* 堆内存泄漏(Heap leak)：指程序运行过程根据malloc\realloc\new等从堆中分配的一块内存，完成后必须调用对应的free\delete删掉。如果程序的设计的错误导致这部分内存没有被释放，此后该内存就不会被使用。
* 系统资源泄漏(Resource Leak)：指程序使用系统分配的资源如Bitmap\handle\Socket等没有使用对应的函数释放，造成系统资源的浪费。严重可导致系统效能降低，系统运行不稳定
* 没有将基类的析构函数定义为虚函数。
  * 当基类指针指向子类对象时，如果基类的析构函数不是virtual，那么子类的析构函数将不会调用，子类资源没有正确释放。

**判断内存泄漏**

通常调用malloc\realloc\new等内存申请操作缺少对应的函数释放

* 使用linux环境下的内存泄漏检查工具Valgrind
* 在写代码时添加内存申请和释放的统计功能，统计当前申请和释放的内存是否一致，判断内存是否泄漏

**段错误**

段错误指访问的内存超出了系统所给的这个程序的内存空间

通常发生在访问非法内存地址的时候，几种情况

* 使用野指针
* 试图修改字符串常量的内容

**new和malloc区别**

* new分配内存按照数据类型进行分配，malloc分配内存按照指定大小分配
* new返回指定对象的地址，malloc返回的是void *,因此malloc的返回值一般需要进行类型转换
* new除了分配内存还会调用构造函数，malloc不会
* new分配的内存用delete销毁-会调用对象的析构函数，malloc用free销毁

* new是一个操作符可以重载，malloc是一个库函数
* malloc分配的内存不够可以使用realloc扩容，new不行
* new分配失败抛出bad_malloc异常，malloc失败返回NULL
* 申请数组，new[]一次分配所有内存，多次调用构造函数，搭配-delete[],调用多次析构函数。malloc只能sizeof(int) * n

### 共享内存

共享内存：一个进程把数据发送到共享内存中，另一个进程可以读取改数据

两个不相关的进程访问同一块逻辑内存

**共享内存相关API**

头文件在sys/shm.h中

1. 新建共享内存shmget
   * int shmget(ket_key,size_t size,int shmlg);
     * key：共享内存键值，理解为共享内存的唯一性标记
     * size：共享内存大小
     * shmflag：创建进程和其他进程的读写权限标识
     * 返回值：相应的共享内存标识符，失败返回-1
2. 连接共享内存到当前进程的地址空间shmat
   * void *shmat(int shm_id,const void *shm_addr,int shmflg);
     * shm_id：共享内存标识符
     * shm_addr：指定共享内存连接到当前进程的地址，通常为0，标识由系统来选择
     * shmflg：标志位
     * 返回值：指向共享内存的第一个字节的指针，失败返回-1
3. 当前进程分离共享内存shmdt
   * int shmdt(const void *shmaddr);
4. 控制共享内存shmctl
   * int shmctl(int shm_id,int command,struct shmid_ds *buf)
     * shm_id：共享内存标识符
     * command：有三个指
       * IPC_STAT：获取共享内存的状态，把共享内存的shmid_ds结构复制到buf中
       * IPC_SET：设置共享内存的状态，把buf复制到共享内存shmid_ds结构
       * IPC_RMID：删除共享内存
       * buf：共享内存管理结构体

### C++新特性

1. auto关键字：编译器根据初始值自动推导出类型，但是不能用于函数传参以及数组类型的推导
2. nullptr关键字：特殊类型的字面值，可以被转换成任意其他指针类型，NULL一般被宏定义为0，在遇到重载可能会出现问题
3. 智能指针：新增std::shared_ptr，str::weak_ptr等类型的智能指针，用于解决内存管理的问题
4. 初始化列表：使用初始化列表对类进行初始化
5. 右值引用：基于右值引用可以实现移动语义和完美转发，消除两个对象交互时不必要的对象拷贝，节省运算存储资源，提高效率
6. atomic原子操作用于多线程资源互斥操作
7. 新增STL容器array以及tuple

### 线程

**单线程处理高并发**

可采用I/O复用提高单线程处理多个请求的能力，再采用事件驱动模型，基于异步回调来处理事件
