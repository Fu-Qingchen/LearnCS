---
title: C++ Primer 阅读笔记
date: 2020-09-25 17:00:00
tags: [C++, 编程]
categories: 计算机相关
mathjax: true
toc: true
---

更新至第十章：泛型算法 | Generic Algorithm

PS：这本书可真厚实

<!--more-->

<p class="note note-primary">正在进行</p>

相关练习：[https://github.com/Fu-Qingchen/LearnCPP](https://github.com/Fu-Qingchen/LearnCPP)

# 开始 | Getting Started

- stream是字符序列，使用这个术语是想表达随着时间的推移，字符是顺序生成或消耗的。

- 操纵符，如`std::endl`，在读写流的时候操纵流本身

- 缓冲区：定义流对象时，会在内存开辟一个缓冲区，用来暂存输入输出流的数据。

- 对Windows系统，`Ctrl + Z`然后按`Enter`结束输入

- C++的输出运算符`<<`和输入运算符`>>`

- 《C++ Primer》P2提到`main`函数的返回类型必须为`int`，但是我自己测试`void`也可以

# C++基础 | The Basics

这一块的难点主要是类型转换

## 变量和基本类型 | Variables and Basic Types

这一块可以分为基本类型、复合类型、常量和自定义类型

### 基本类型

- 基本类型的选择

  - 明确知道数值部位负时，选用无符号类型
  - 使`int` 进行整数运算
  - 算术表达式中不使用`char`或`bool`
  - 使用`double`进行浮点数运算
  
- C++约定表示范围内正值和负值的数量要平衡，即`signed char`范围为`-127~127`

- C++中将浮点数赋值给整数时，小数点部分直接去掉

- C++中将整数赋值给浮点数时，若整数超过浮点数的精度，浮点数将会对整数进行四舍五入

- 切勿混用带符号类型的数和无符号类型的数

- C++中字符串结束的字符为`\0` ，`'A'`中有1个字符，`"A"`中有两个字符

- 由空格、缩进和换行连接的两个字符串其实是一个字符串

- `f/F`后缀或前缀只能用于浮点数类型的字面量，`1024F`是无效写法

### 变量

- 数据类型决定
  - 变量/对象所占据的空间大小和布局方式
  - 该空间所能储存值的范围
  - 变量能进行的运算

- 对象：一块能储存数据，并具有某种类型的储存空间

- 初始值不是赋值，初始化的含义是创建变量时赋予其一个初值，而赋值的含义是擦除当前的值，然后用一个新值来代替

- 声明和定义：
  - 声明：规定变量的类型和名字
  - 定义：规定变量的类型和名字，申请储存空间，并可能为变量赋一个初始值
  >  在多个文件使用同一个变量，必须将变量声明与定义分开（？？？）

- 变量的4种属性
  - 数据结构
  - 储存类型
  - 作用域
  - 作用期

### 复合类型

- 引用即别名

- 建议初始化所有指针，如果实在是不清楚，可以将它初始化为`nullptr`

- 指针与引用的主要区别

  - 指针是对象，在初始化和赋值后还可以更改、拷贝等
  - 指针不需要在定义的时候初始化，引用需要

- 为了在多个文件用到同一个变量，可以在其中一个文件定义，其他文件用`extern`仅声明不定义

### 常量

-  常量引用是对`const`的引用

- 只有常量引用才能引用常量

- 初始化常量引用时允许引用类型与所引用对象的类型不一致

  ```c++
  int a = 1;
  const double &b = a;
  ```

- 指向常量的指针：`const int a = 0; const int *b = &a` 指针非常量，指向对象为常量

  `const`指针：`int a = 0; int *cosnt b = &a`指针是常量，指向对象可以是非常量
  
- 顶层const：表示对象本身是个常量，如`const`修饰的基本类型和const指针

  底层const：表示指针/引用所指对象是个常量

- 顶层const的拷贝不受影响，底层const的拷贝两个数据类型必须能够进行转换

  ```c++
  const int v2 = 0;
  int v1 = v2;
  int *p1 = &v1;
  const int *p2 = &v2, *const p3 = &i;
  p1 = p2;	// error
  p1 = p3;	// error
  ```

- `constexpr` 常量表达式

### 处理类型

- `typedef`类型重命名

- `auto` 自动检测赋值的类型
  - 自动去掉顶级`const`和引用
  - 设置为引用时，其初始化的顶级`const`保留
  - 一条语句定义多变量要保持一致
- `decltype` 自动检测一个函数返回的类型，与`auto`的区别是可以赋其他值
  - 不去掉顶级`const`和引用
  - `decltype(*p)`将会得到一个指针

- 赋值的表达式语句本身是一种引用

  ```C++
  int a = 1;	
  return typeof(x = a)	//return int & 类型
  ```

## 字符串、向量和数组 | Strings, Vectors, and Arrays

- 头文件中不应该使用`using`

- 用`string(int, char)`初始化得到给定字符重复n次。

- 字符数组无法进行整体赋值

  ```c++
  char c[10];
  // c = {'C', 'h', 'i', 'n', 'a'}; error
  c[0] = 'C', c[1] = 'h', c[2] = 'i', c[3] = 'n', c[4] = 'a';
  ```

- 字符数组的最后一个字符并不要求是`\0`，但是为了统一，习惯性的在字符数组后面加一个

- `unsigned string::size()`，注意他和`int`比较时可能会发生意想不到的bug，**不要在有`size()`的表达式中加入`int`类型的数据**

- 数组不支持拷贝和赋值

## 表达式 | Expressions

- 左值->对象的身份（内存中的位置）

  右值->对象的值（内容）

- **C++没有规定运算符求解的顺序**，如果表达式指向并修改了同一个对象，将产生错误并产生未定义的行为

  ```c++
  int i;
  cout << i << " " << ++i << endl;	//不会报错，但是混淆
  ```

  除了`&&`、`||`、`,`、`?:`

- C++中`true == 0`，因此`-true == true`
- `++i`比`i++`性能好

- `,` 返回逗号右侧的运算结果
- 当数组**不**转换为指针的情况
  - 作为`decltype`的参数
  - 作为取地址符的对象
  - `sizeof`的运算对象
  - `typeid`的运算对象

- 对指针来说， `static_cast` 必须保证转换后的类型就是指针所指的类型
- `reinterpret_cast` 可以为运算对象的位模式提供较低层次上的重新解释

## 语句 | Statements

- case标签后跟常量

## 函数 | Functions

- 内置函数(函数名前加`inline`)的使用可以减少函数调用的时间，当一个函数只有少量代码且调用频繁时可以将其加入

- 指定默认值的参数必须放在形参表列的最右端

- C++中名称有作用域，对象有生命周期

- 尽量使用引用避免拷贝，大的类类型的数据拷贝起来费时间

- 函数无须改变引用形参的值时，最好申明为常量引用

- 实参初始化形参时会默认去掉顶层const

- 函数重载不区分顶层const

  ```c++
  void test(int);
  void test(const int);	//这两个申明不构成重载，是同一个函数
  ```

- 尽量使用常量引用

- 虽然不存在引用的数组，但是可以写出来

- `initializer_list`可以实现可变参数

  ```c++
  #include <initializer_list>
  void Test(initializer<int> il){
      for(auto pointer = il.begin(); pointer != il.end(); pointer++){
          cout << *pointer;
      }
  }
  int main(){
      Test({1, 2, 3})
  }
  ```

- **不要返回局部对象的指针或引用**

- 返回引用的函数返回的是左值（可以对函数赋值），返回其他类型的函数返回的是右值

- 给定作用域中形参只能给一次默认值

- `constexpr`函数不一定返回常量表达式，只是这个函数可以用于常量表达式；当起实参是常量表达式时，返回常量表达式

- 重载函数的函数匹配
  - 候选函数（所有调用点可见，重名的函数）
  - 可行函数（实参与形参对应或可以进行类型转换的函数）
  - 最优函数
    - 有一个实参的匹配优于其他
    - 没有实参的匹配劣于其他

- 类型转换
  1. 精确匹配
     - 类型相同
     - 数组，函数 --> 指针
     - 顶层`const` --> `const`
  2. 非`const` --> `const`
  3. 算术类型的提升
  4. 算术类型的转换，或指针的转换(`0`/`nullptr` --> other; other --> `void *`)
  5. 类类型的转换

## 类 | Classes

- 不能在常量对象上调用普通成员函数，可以调用参数列表后有`const`的常量成员函数，它将返回`const`对象

- IO类不允许拷贝，因此使用引用传递

- 执行输出任务的函数不应该有格式的控制

- 默认构造函数：不接受任何实参的函数

- 定义在块中内置类型或复合类型的数据默认初始化后的值时未确定的

- 友元函数 --> 类相关的非成员函数，在类中申明友元只是说明了访问性，在类外也必须要申明和定义

- 类成员函数定义在外部时，其返回类型的作用域在类的作用域外(设置了别名时需要注意)

- 名字查找
  - 类的成员函数
    - 编译类的声明
    - 完成后编译函数体
  - 其他
    - 在名字所在块中查找
    - 外层作用域中查找

- 尽量使用初始化而不是赋值，后者底层效率更低

- 构造函数初始化顺序与成员在类定义出现的顺序一样

- `explicit`只对**一个实参**的构造函数有效，切只能以直接初始化的方式使用

- 聚合类没有类内初始值，没构造函数，也没基类

- `string`不是字面值类型的数据

- 静态成员函数无法声名成`const`，也无法使用`this`

- 类外无法使用`friend, explicit, static`关键字

- 类的静态成员不应该在类内初始化，即使这样也要在类外定义一下

- 类：抽象和封装

# C++标准库 | The C++ Library

## IO库 | The IO Library

- 不能拷贝IO对象或对IO对象赋值，因此进行IO操作的函数经常用引用来进行数据的传递

- 流一旦发生错误，后续的IO操作失效，因此代码通常在使用流之前对其进行检查

  ```c++
  while(cin >> input){/* */}
  ```
  
- 如果一个程序中途崩溃，有些流信息可能就在缓冲区，无法输出。

  > 当调试时用输出判断执行位置时要注意，可能代码已经执行，程序崩溃后输出的语句卡在缓冲区无法输出

- 每次都刷新缓冲区，可以使用`unitbuf`操作符

  ```c++
  cout << unitbuff;	//之后的输出操作后会立即刷新缓冲区
  cout << nounitbuff;	//回到正常的模式
  ```

- 关联了输出和输入的流，执行输入会默认刷新缓冲区（`cin, cout`就是关联在一起的），交互式系统一般都会关联

- 为了保留`out`模式打开的文件的内容，必须同时指定`app`模式

- `istringstream`的应用：数据先对整行进行存储，再对行内的每个单词进行存储

- `ostringstream`的应用：逐步构造输出，最后一起打印

## 顺序容器 | Sequential Containers

- `string` & `vector` : 元素存储在连续的内存空间中，访问速度快，在尾部外的位置插入删除慢

- `list` & `forward_list` :  双向和单向链表，插入删除很快，不支持随机访问

- `deque` : 两端队列，两端插入删除很快

- `array<T, n>` : 有固定大小

- **标准库容器比旧版本性能快的多**，因此尽量使用标准库容器，而不是数组。**通常 `vector` 是最好的选择，除非有理由选择其他容器**

- 程序中有很多小元素且额外的空间开销很重要：不选`list` 和 `forward_list`

- 如果程序仅在输入时要求在中间插入数据，可以在输入阶段使用`list`，然后将`list`中的拷贝到`vector`中

- 可以通过`vector`的`sort()`解决很多在中间插入的情况

- **迭代器范围**：`[begin, end)`

- 通过迭代器范围对容器元素进行拷贝就不需要两个容器类型匹配，只需要可以互相转化 `vector<string> v1; vector<chat *> v2(v1.begin(), v1.end())`

- `assign`能够实现不同类型之间元素的拷贝

- 拷贝后之前的迭代器、指针和引用会失效

- `swap`交换的是一整个容器

  - 除`array` 外容器，`swap`不对任何数据进行操作，指向容器的迭代器、指针和引用依然有效，不过`swap`后他们指向的元素属于不同的容器了， $O(1)$时间复杂度
  - `array`容器：进行操作，迭代器、指针和引用所指元素不变，但是元素的值进行了交换，$O(n)$时间复杂度

- `vector, string, deque, array`才支持下标和`at()`操作

- 访问成员函数返回引用，**但是使用`auto`时必须将变量定义为引用类型**，如`auto &v = c.front(); v = 2; //c.front()的值也变了`

- `at()`会检查越界，下标不会

- 在删除容器元素前，必须保证他们是存在的

- **添加/删除元素前，必须考虑迭代器**，特别是对于`vector`和`string`

- 不要保存`end()`返回的迭代器 

- 复合赋值语句只能用于 `string`、`vector`、`deque`、`array`

- `reserve(n)` 分配至少容纳 n 个元素的内存空间，不过这个函数不会减小容器咱用的容量

- `capacity` 与 `size` 的区别

- 容器适配器：`stack`，`queue`，`priority_queue`

- `stack` 和 `queue`基于 `deque` 实现，`priority_queue` 基于 `vector` 实现

- `pop()`：删除不返回；`top`：返回不删除

## 泛型算法 | Generic Algorithm

- 对于只读取不写入的泛型算法，可通常使用`cbegin()` 和`cend()`

- 对用单一迭代器表示第二个序列的算法，默认认为第一个序列与第二个序列等长

- `accumulate()` 的第三个参数决定了返回类型

- **泛型算法不检查写操作**，不要越界

- 可调用对象：可以使用调用运算符`()`的对象或表达式，有四类
  - 函数
  - 函数指针
  - lambda表达式
  - 重载了函数调用运算符的类
  
- lambda表达式的形式：`[lambda在函数体中声明的局部变量](函数参数表)->返回类型{函数体}`。其中，可以忽略参数表和返回类型，即`[局部变量]{函数体},[局部变量](函数参数表){函数体},``[lambda在函数体中声明的局部变量]->返回类型{函数体}`

- 当以引用的方式捕获变量时，要注意它在 lambda 表达式执行时变量依然存在

- 要尽量减少捕获的数据量，避免捕获指针和引用（`int` 等就直接用值捕获）

- 默认情况下，值捕获变量，lambda不会改变它的值。如果要改变，就要在参数表后加关键字 `mutable`：`auto f = [v] () mutable {v++;} ;`

- lambda 表达式如果不只包含单一的 `return` 语句，它的返回类型默认是 `void`，如果想指定返回类型需要用到 lambda 的返回类型（带`->`的版本）

- 有些算法仅支持一元谓词，但是我们希望传多个参数给函数，这时候除了用 lambda 表达式通过捕获，还可以用到**绑定**，形式为 `auto newCallable = bind(callabled, arglist)`，`newCallable`就是满足要求的

- 对`auto g = bing(f, A, B, _2, C, _1)`，有`g1(_1, _2)` 映射为 `f(A, B, _2, C, _1)`

- 绑定不能直接绑引用参数，必须借助`ref()`：`bind(print, ref(os), _1, " ")`

  