#  目录

- [C++笔记](#C++笔记)
- [.cpp编译流程](#.cpp编译流程)
- [函数的默认返回值](#函数的默认返回值)
- [左值右值和左值引用以及右值引用](#左值右值和左值引用以及右值引用)
  - [引用的本质](#引用的本质)
- [const类型](#const类型)
- [enum枚举类型](#enum枚举类型)
- [运算符和操作符的区别](#运算符和操作符的区别)
- [类型转换](#类型转换)
- [initializer_list列表初始化](#initializer_list列表初始化)
- [类](#类)
  - [构造函数与析构函数](#构造函数与析构函数)
  - [static属性的构造函数和成员以及在private中的构造函数](#static属性的构造函数和成员以及在private中的构造函数)
  - [静态成员变量和静态成员函数](#静态成员变量和静态成员函数)
  - [常量成员函数](#常量成员函数)
  - [友元](#友元)
  - [操作符重载](#操作符重载)
    - [一个操作符new的范例](#一个操作符new的范例)
  - [继承](#继承)
    - [多继承中的虚继承](#多继承中的虚继承)
  - [继承类中的 vptr 指针分布初始化](#继承类中的vptr指针分布初始化)
  - [虚函数和纯虚函数](#虚函数和纯虚函数)
    - [纯虚函数](#纯虚函数)
  - [多态](#多态)
    - [多态的原理](#多态的原理)
  - [抽象类](#抽象类)
    - [面向抽象层编程](#面向抽象层编程)
  - [重载,重写,重定义](#重载,重写,重定义)
  - [虚继承](#虚继承)
  - [虚继承、虚函数](#虚继承、虚函数)
- [普通函数重载](#普通函数重载)
- [占位参数\(也称默认实参\)](#占位参数-也称默认实参)
- [inline 内联函数](#inline内联函数)
- [临时对象](#临时对象)
- [new 和 delete 运算符](#new和delete运算符)
- [nullptr 和 NULL的区别](#nullptr和NULL的区别)
- [智能指针](#智能指针)
- [命名空间](#命名空间)
  - [using声明](#using声明)
    - [构造函数的using声明](#构造函数的using声明)
    - [using指示](#using指示)
    - [尽量少使用using指示污染命名空间](#尽量少使用using指示污染命名空间)
    - [using使用](#using使用)
- [::范围解析运算符](#::范围解析运算符)
- [静态联编和动态联编](#静态联编和动态联编)
- [内存分段](#内存分段)
- [decltype](#decltype)
- [typeid在运行时获得对象的类型](#typeid在运行时获得对象的类型)
- 



```c++
关键字集合:
 enum 枚举,    inline 内联,    public 公有,    private 私有,    protected 保护,
 virtual 虚或纯虚,    friend 友元,    operator 操作符重载

单词集合:
      by reference  引用,       
      pass by reference  传递引用,
      calss body  类结构体
      value 值,   
      ctor  构造函数,
      dtor  析构函数,


```

- <u>成员函数尾部出现 const ,修饰是 this 指针,就是不允许修改对象的任何数据内容.(`const 类名 *cosnt this`)</u>
- **参数传递尽量都使用引用, 如果不想被修改,那么可以添加 const 参数, 返回值也是一样可以使用引用**
- <u>***如果函数返回一个 值(不是引用), 那么可以使用 `&& 右值引用` 来避免昂贵的构造,拷贝,析构的资源***</u>
- <u>***相同的 `class` 的各个 `objects` 互为友元***</u>
- **在类中的成员函数 结尾能加 `const` 就尽量加上**
  - <u>**结尾没有 `const` 的成员函数, 是无法让 `const` 限定创建出来对象使用的**</u>
- <u>**类模板中如果出现 static 成员,那么每一种实例化都会有自己单独的一个static 的值,和其他的实例化不相同**</u>
- 



==**面向对象三大特征 —— 封装、继承、多态**==



# C++笔记

> - <u>在 c 下函数的默认返回值是int, 就算是 f(){xxx} 这样写在那里,也不会出错.但是c++下不行</u>
>
> -  <u>在 c 下可以重定义多个同名的全局变量,会存在不同的字段(无初始化会在bss,有初始化会在data).c++下不行</u>
>
> - <u>一个函数的返回值就是一个无名的隐藏变量(右值),一旦当接受它的是一个想要用它来定义的初始化的值的时候,它会直接由无名隐藏变成那个值的名字,从而顶替他.(这个很重要,尤其是类的时候,这样来定义一个类是不会触发析构函数的)</u>

- **双目(也称双元)运算就是运算符有两个参数,(a + b  中的 + 就是双目运算符)**
- **三目运算符这样写就可以变成左值:   (开发根本用不到)**
  - `*(a < b ? &a :&b) = 50;     // 如果a小于b 则返回a 的引用,然后修改a的值,反之亦然.`
  - `(a < b ?  a : b) = 50;     // 同上,三目运算返回的是变量的引用,并不是返回某个数值,这样是可以的.`
- **const int a; 等价于 int const b;  没区别的.(`指针除外`)**
- <u>const 类型的常量会在 常量区的符号表创建,而不是在栈区.没有地址`(而且还会在编译阶段把常量换成值)`</u>
- <u>**符号表 有可能在常量区 也有可能在text段 `(内存笔记有详细记录)`;**</u>
  
  - **符号表纯粹是一个键值对`(一个key对应一个value,和一个变量名字与变量所代表的值类似.)`**
    -  一个是 key (存放变量名称) 一个是 value(存放变量数据),
    - **没有空间和地址,而且是只读不可修改的.会在编译的时候替换(不是预处理),与#define 虽然很像但不是一个东西**
- <u>**如果对一个常量取地址,编译器会临时开辟一个空间(栈),让这个指针指向存放这临时空间的地址**</u>
  - <u>(当然,这个临时空间内的值也就失去了常量的性质,变成 `变量` 了,可以随意更改.但是和`原常量`没关系了)</u>
- <u>一个 `const` 的指针,当把它传递给其他函数形参的时候,接受它的也必须是 `const` 类型,否则会出现安全问题,编译也无法通过</u>
- **参数传递尽量都使用引用, 如果不想被修改,那么可以添加 const 参数, 返回值也是一样可以使用引用**

  





## .cpp编译流程

- **index.cpp 编译流程: (这是个普通的源文件)**
  - **`index.cpp -> 通过 预编译器 -> index.i`  (只是把需要的头文件之类的放入一个文件内,把宏定义展开和替换).**
  - **`index.i   -> 通过 编译器   -> index.s 汇编文件`** 
  - **`index.s   -> 通过 汇编器   -> index.obj (linux是.o)  目标文件`**
  - **`index.o   -> 通过 链接器   -> index.out (windows是.exe) 执行文件` (找到调用函数的函数实现).**
- **`$ g++ -E index.cpp -o index.i`     // 该命令会生成 index.i 通过预编译**
- **`$ g++ -S index.i   -o index.s`     // 该命令会把 .i 文件编译成 index.s 汇编文件.**
- **`$ g++ -c index.s   -o index.o`     // linux下是.o  win 下是.obj   ,该命令会生成目标文件**
- **`$ g++ index.s      -o index.out`       // 链接不需要命令, 直接就可以把.s 文件写在上面, 生成执行文件.**





## 函数的默认返回值

- **在 c 下函数的默认返回值是int, 就算是 `f(){xxx}` 这样写在那里,也不会出错.但是c++下不行**
- **一个函数的返回值就是一个无名的隐藏变量,一旦当接受它的是一个想要用它来定义的初始化的值的时候,它会直接由无名隐藏变成那个值的名字,从而顶替他.`(这个很重要,尤其是类的时候,这样来定义一个类是不会触发析构函数的!)`**





## 左值右值和左值引用以及右值引用

> - <u>**左值 可以在多行使用, 如果不是const的左值,那么就可以随意修改,如果是const  则不可以修改**</u>
> -  <u>**右值 就是一个临时变量, 只能在当前行使用,而且不可以进行直接的引用.`(在C++11标准中可以&&来对引用,取地址)`**</u>
> -  **引用不是对象；它们不必占用存储，尽管若需要分配存储以实现所需语义（例如，引用类型的非静态数据成员通常会增加类的大小，量为存储内存地址所需），则编译器会这么做。**
> - 右值引用可实现转移语义（Move Sementics）和精确传递（Perfect Forwarding），它的主要目的有两个方面：
>   - 消除两个对象交互时不必要的对象拷贝，节省运算存储资源，提高效率。
>   - 能够更简洁明确地定义泛型函数。

- **关于引用中的左值引用和右值引用, & 一个引用符号是左值引用, && 是右值引用,这个右值引用解决了模版的一些问题,如果遇到问题可以尝试**
- <u>**关于引用中的左值引用和右值引用的区分:**</u>
  -  **& 一个引用符号是左值引用** 
     - **常量左值引用  可以接受左值、右值、常量左值和常量右值**
     - **非常量左值引用只能接受左值**
  -  **&& 是右值引用, 这个右值引用解决了模版的一些问题,如果遇到问题可以尝试**
     - **如果出现在函数模版声明中, 那么可能是左值引用也有可能是右值引用, 具体需要看传入的参数是左值还是右值了**

> <u>**右值引用可以解决以下形式类似的问题:(尤其是存在重载的时候,这个时候要运行试一试)**</u>
>
> ```c++
> void func(int&& x){ cout << x << endl; }  //传入值是一个int
> func(3); 
> //可以直接传入常量,而不是一个对象.(如果不提供 func(int&&),那么会调用 func(const int &))
> // 当然这是有重载的时候发生的情况.
> ```

```cpp
std::remove_reference<decltype(*参数值)>::type   删除引用，  type 是个类型
```

- **引用折叠**
  - `X& &`、`X& &&`、`X&& &` 可折叠成 `X&`
  - `X&& &&` 可折叠成 `X&&`



## 引用的本质

- **引用的本质就是 type * const T_Nmae;   就是个指针常量,不能修改指向**

> - **如果我们去研究引用的时候,可以把引用当做一个常指针去研究,**
>
> - <u>**无法引用右值,比如 100 这个数值是不可以直接引用的,但是却可以 双重引用 使用两个&&符号来对引用,取地址. **</u>
>
> - **当我们在使用引用编程的时候,你就可以把引用理解为变量的别名就可以了**
>
>   - **把引用当做超级指针来看最好**
>
> - **还有种比较绕口的: (就是一个指针的引用)**
>
>   - ```c++
>     struct cer { int i;};  //一个结构体
>     int fun(struct cer* &tmp)  // 一个函数  形参是结构类型的指针的引用
>       {  tmp = (struct cer*)malloc(sizeof(struct cer)); } // 直接就可以当申请内存当指针
>     ```



## const类型

1. **const 类型的常量会在 常量区的符号表创建,而不是在栈区.没有地址(而且还会在编译阶段把常量换成值)**
   1. **通常C++编译器并不为const创建存储空间，相反它把这个定义保存在它的[符号表](http://baike.baidu.com/link?url=gYnEQDQ3o8mEj0xHnWvWMQfrraYCG1iVvntmoXcI1htWc14_Z5WCa6pVWR3UUUxr6n-CA9PGjK77Czc0MAFigK)里，但extern强制进行了存储空间分配，取const地址也会**
      1. **常量Const 同其他变量一样被分配空间，可寻址**
      2. 由于extern意味着使用外部连接（**定义时使用extern**），因此必须分配存储空间，这也就是说有几个不同的编译单元应当能够引用它，所以必须有存储空间
   2. **如果常变量有初始化赋初值，那编译器将该常变量在其他地方替换成文字常量 ,但是如果开始不初始化就会错误**
   3. **如果返回值为引用，用const修饰可以阻止对被引用对象修改**



> 常引用或常指针，只能指向常变量，这是一个极大的误解。常引用或者常指针只能说明不能通过该引用（或者该指针）去修改被引用的对象，至于被引用对象原来是什么性质是无法由常引用（常指针）决定的。

- **只有类的非静态成员函数可以被声明为常函数**
- **常对象，全体成员数据成员都是常量看待。** 
  - 类对象的非静态常量成员必须在构造函数中初始化，且只能借助初始化列表进行。



- <u>**符号表有可能在常量区也有可能在text段(去找[内存笔记](内存笔记.md));**</u>
  - **符号表纯粹是一个键值对, 一个是 key (存放变量名称) 一个是 value(存放变量数据),没有空间和地址,**
    - **而且是只读不可修改的.会在编译的时候替换(不是预处理),与#define 虽然很像但不是一个东西**
- <u>**如果对一个常量取地址,编译器会临时开辟一个空间(栈),让这个指针指向存放这临时空间的地址**</u>
  - **(当然,这个临时空间内的值也就失去了常量的性质,变成 变量了,可以随意更改.但是和原常量没关系了)**

```c++
int const *p; //指向常量的指针（即常指针，const修饰的是int），指向的对象是const型，不可以修改，但是指针p的指向可以修改
int *const p; //指针常量（const修饰的是int*），指针变量p是const型，它的指向不可修改，但是指向的对象可以修改

int & const r4=i; //const是多余的，编译器warning后忽略const存在, C++17会报错
const int&  r5=i;  // 限定了无法使用该引用来修改 r5 的值

int const **p1; //不是指针常量，指向 int count*（“int const*”是一个 指向整型常量的指针）
int *const *p2; //不是指针常量，但所指的变量是指针常量（int *const，即指向整型的指针常量，指向不能修改）


//##### 
void DefineArray(const int n){  // 绝对会出问题, 而且带const 与不带 cosnt 的函数是无法重载的
     int B[n]={}; //error,数组大小在编译期确定, 可能会申请非常大的内存.
}


int &r=5; //error，无法寻址文字常量，无法建立引用
const int &r=5; //ok，在数据区开辟一个值为5的无名整数量，然后将引用r与这个整形两绑定



class A{
	void  disp(void) const;   // 可重载, 这是可以的, this (class*) 变成了 const class* , 
  void  disp(void);         // 就只是 const , 签名不同  参数不同
  // 正常的 A a1;  会执行 非const 函数. 如果 const A a2;  则执行 const 函数.
  // 想要指定执行const 函数, 那么就  ((const A&)a1).disp(); 或 ((const* A)&a1)->disp();
  // 非只读对象（如a1）调用某个函数（如 disp()），先找非const版本，如果没有，再调用const版本。而常对象，只能调用类中定义的常函数，否则编译器报错。
  // ##  只有类的非静态成员函数可以被声明为常函数
};



const int i =0;
int& p = const_cast<int&>(i);    // 除去const 属性, 但必须是引用或指针, 不能出现同类型直转
```



## enum枚举类型

```c++
enum Season{ ziro = 0, one , two };   //声明
enum ppd { cmd_l , cmd_t };  // 默认 cmd_l = 0 ,cmd_t= 1
		int main(void)
    {
        enum Season vod = one;  // 定义了 vod 等于 one 代表的值 也就是1
        if (vod > one )    // 尽量来这样比较和使用
        { 
           ......
        }
    }

限定作用域的枚举类型
  enum class open_modes { input, output, append };
//   enum class能够有效对枚举作用域进行限定，避免了枚举成员的重定义。

不限定作用域的枚举类型
  enum color { red, yellow, green };
	enum { floatPrec = 6, doublePrec = 10 };


```



### 运算符和操作符的区别

- <u>操作符是嵌在每一条指令中的，换句话说，指令系统的每一条指令都有一个操作符。是计算机系统中的术语.</u>
  - 如=（赋值），==（判断相当），+，- 等等。
- <u>运算符是数学中的术语。主要是指数之间的运算方式.</u>
  - 如 + - * / %，但不包括 = （赋值）这样的操作符。



## 类型转换

- **类型转换的含义是通过改变一个变量的类型为别的类型从而改变该变量的表示方法,**
  - **为了类型转换一个简单对象为另一个对象你会使用传统的类型转换操作符.**
- <u>**c 风格的强制类型转换,不管是什么类型,统统都是 Type b = (Type)a;  (不会进行类型检查)**</u>
- <u>**c++ 风格的类型转换提供了 4 种类型转换操作符来应对不同场合的应用.    (会对类型进行检查)**</u>
- <u>**C++风格的强制类型转换关键字是运算符**</u>

- **C++ 风格的强制类型转换关键字(运算符):**
  - <u>**`static_cast`   一般的转换,用在内建基础数据类型,指针和引用.   (会有类型检查)**</u>
    - **用于内置的数据类型,还有具有继承关系的引用和指针.**
    - `double a = static_cast<char>(t); // t是 int 类型`
  - <u>**`dynamic_cast`  通常在 基类 和派生类之间进行转换时使用,有继承关系的. 动态类型转换**</u>
    - **只能转换具有继承关系的指针或者引用, 只能由子类型转换到父类型.**
    - **也可以由父类型指针转换到子类型指针, 但父类型必须拥有虚函数**
      - ==**会执行检查, 查看将要由父类型转换成子类型的值 是否原本就是子类型, 是否父类型包含虚函数,  否则会返回 nullptr;**==
        - **如果转换的是引用, 则会抛出异常**
  - <u>**`const_cast`    主要针对 const 的转换,只能对指针,引用.或者对象指针,增加const引用或者去掉const.**</u>
    - **转化之前会做安全检查,(绝对不可以对 对象使用,可以借助指向对象的指针或引用)**
  - <u>**`reinterpret_cast`  用于进行没有任何关联之间的转换,比如字符指针转换为整形数.(跟c差不多).**</u>
    - **没有安全检查,什么都能转, 比如class指针 转成函数指针.**

```c++
// 基础数据类型
    int  a  = 97;
    char c  =  static_cast<char>(a);   // 是基础类型可以转换
    char c1 =  dynamic_cast<char>(a);  //该转换会报错, 不可以转换 非指针或引用的类型
    cout << c  << endl;      // static_cast 转换后会输出一个字符 a  (ascll 码97就是a)

    const int& ca = a;
    int& ca1 = const_cast<int&>(ca);   // 可以转换,而且去除了const ,可以通过ca1进行修改了.
    ca1 = 30;                          //  通过ca1 修改了a 的值,但是不可以直接对a 进行赋值
                                       //  也就是 ca 原有const 属性还是存在的.
    int* p =nullptr;
    const int* p1 = const_cast<const int*>(p);  //这样也是可以的,但仅仅p1具有const int,还可以修改指向
    *p = 30;                           //虽然p1 指针具有const效应, 但是p 没有变,他还是可以修改.
   

// 基础数据类型指针或引用
    int*   p = nullptr;
    char* sp = static_cast<char*>(p);   // 这样转换是会报错的,无法编译
                                        // 不允许从'int *'到'char *'的转换

//对象指针 和 引用
    class A{};              // A 是C 的父类
    class B{};              // B 无继承
    class C: public A{};    // C 继承 A
      A* a1 = nullptr;
      B* b1 = nullptr;
      C* c1 = nullptr; 
    
    A* a2 = static_cast<A*>(b1);  // 错误的转换,无法转换的类型,因为他们没有继承关系
    B* b2 = static_cast<B*>(c1);  // 错误的转换,无法转换的类型,因为他们没有继承关系
	  C* c2 = static_cast<C*>(a1);  // 父类指针可以转换为子类指针 (不安全 但很常用)

    A* a3 = dynamic_cast<A*>(c1);   // 子类转换为父类的指针是可以的 不会出现内存访问越界
    B* b3 = dynamic_cast<B*>(c1);   // 错误的转换,两个毫无关系的类 是无法进行转换的
    C* c3 = dynamic_cast<C*>(a1);   // 错误的转换,父类转换子类内存会越界,dynamic_cast 会直接报错
    
    A a;
    B b;
    C c;
    
    A& a3 = a;   //正常的引用
    C& c3 = c;
    C& c4 = static_cast<C&>(a3);  // 父类引用可以转换为子类引用 (不安全)
    A& a4 = static_cast<A&>(c3);  // 子类引用也可以转换为父类引用
```





## initializer_list列表初始化

用花括号初始化器列表初始化一个对象，其中对应构造函数接受一个 `std::initializer_list` 参数.

initializer_list 使用

```cpp
#include <iostream>
#include <vector>
#include <initializer_list>
 
template <class T>
struct S {
    std::vector<T> v;
    S(std::initializer_list<T> l) : v(l) {
         std::cout << "constructed with a " << l.size() << "-element list\n";
    }
    void append(std::initializer_list<T> l) {
        v.insert(v.end(), l.begin(), l.end());
    }
    std::pair<const T*, std::size_t> c_arr() const {
        return {&v[0], v.size()};  // 在 return 语句中复制列表初始化
                                   // 这不使用 std::initializer_list
    }
};
 
template <typename T>
void templated_fn(T) {}
 
int main()
{
    S<int> s = {1, 2, 3, 4, 5}; // 复制初始化
    s.append({6, 7, 8});      // 函数调用中的列表初始化
 
    std::cout << "The vector size is now " << s.c_arr().second << " ints:\n";
 
    for (auto n : s.v)
        std::cout << n << ' ';
    std::cout << '\n';
 
    std::cout << "Range-for over brace-init-list: \n";
 
    for (int x : {-1, -2, -3}) // auto 的规则令此带范围 for 工作
        std::cout << x << ' ';
    std::cout << '\n';
 
    auto al = {10, 11, 12};   // auto 的特殊规则
 
    std::cout << "The list bound to auto has size() = " << al.size() << '\n';
 
//    templated_fn({1, 2, 3}); // 编译错误！“ {1, 2, 3} ”不是表达式，
                             // 它无类型，故 T 无法推导
    templated_fn<std::initializer_list<int>>({1, 2, 3}); // OK
    templated_fn<std::vector<int>>({1, 2, 3});           // 也 OK
}
```





## 类

**三种访问级别:  `public,  private, protected`**

- <u>成员函数尾部出现 const ,修饰是 this 指针,也不允许修改`const`对象的任何数据内容.(`const 类名 *cosnt this`)</u>
  - <u>**在C++中，只有被声明为const的成员函数才能被一个const类对象调用,也就是说 const 的对象是不可以调用 非const的成员函数的**</u>
- <u>this 指针原型`(类名 *const this)`;   不可以修改指向, 但可以修改指向对象的值</u>
- <u>如果想返回一个对象的本身,在成员方法中,用 `*this` 返回</u>
- <u>定义类函数的时候都会隐藏的传入一个 `this` 指针常量 隐藏参数</u>
  - **所有的成员函数,都拥有一个隐藏参数, 就是指向本类对象的指针 `this`**
- **<u>如果想对一个对象相续调用成员方法,每次都会改变对象本身,成员方法需要返回引用</u>**
- <u>临时创建的无名类,定义以后会立刻析构(temp(); 运行过了这一行就立刻析构,就是刚刚定义就没了)</u>
- <u>类的声命周期结束后会调用析构函数,如果函数中有多个类,那么会实行先入后出原则,最后构造的先析构(出栈)</u>
- **类中的 `const` 成员常量,只能通过构造函数的 初始化列表来进行初始化**
- **无法对 `static` 函数成员 使用 `const` 限定符(在末尾添加的 const)**



### 构造函数与析构函数

```c++
(int char double long 之类的这样的关键字都是类)
类 : class temp { 
    public:    // 公有成员
    private:   // 私有成员
    protected: // 保护控制权限.在类的继承中跟private有区别,在单个类中,跟private一样.

    explicit temp( int a ) {}   //不允许使用隐式类型转换来调用构造函数, (显示构造函数)
};

 //  构造函数为什么 不返回任何值: 
构造函数实际发生的事情是，运行时使用编译器生成的类型数据来确定将对象实例存储在内存中需要多少空间，将其放在堆栈或堆上。 
这个空间包括所有的成员变量和vtbl。分配这个空间之后，构造函数被调用为实例化和初始化过程的内部部分，以初始化这些字段的内容。
然后，当构造函数退出时，返回新创建的实例。所以构造函数没有返回值的原因是因为它不是由代码直接调用，而是由运行时的内存分配和对象初始化代码调用。  
它的返回值（如果它实际上有一个编译到机器码时）对用户来说是不透明的，因此，你不能指定它。
```

> - <u>构造函数和析构函数都不允许有返回值.(定义的时候都没有那么就不应该有)</u>
> - 构造对象成员的顺序跟初始化列表的顺序无关,而是跟成员对象的定义顺序有关.
> - **析构函数不能有形参,而且析构也不能重载, 但是构造可以重载**
> - 当有显示的构造函数或者显示拷贝构造函数的时候,默认无参构造函数都会被屏蔽.(但是默认拷贝构造函数还有).
> - <u>把拷贝函数 和 拷贝构造函数 放到 `private` 私有中,就可以拒绝对象进行拷贝了</u>
>   - <u>**正常的无参数构造如果放到 `private` 中, 那么代表不可以使用无参数来创建一个对象**</u>
> - <u>使用 `new` 和 `delete` 会自动调用构造和析构的</u>
> - <u>无论什么情况都要把  `= 号操作重载` 和 `深拷贝构造` 两个重要的成员写出来</u>
> - **一个函数的返回值的引用就是一个无名的隐藏变量,一旦当接受它的是一个想要用它来定义的初始化的值的时候, 它会直接用无名隐藏变成那个值的名字,从而顶替他.(,这样来定义一个类是不会触发析构函数的!)**
>   - 返回的必须是一个引用 &
> - `memset()`;  该函数可把数组内的所有元素都初始化为 '\0'
> - <u>绝对不可以出现二义性的重载构造函数</u>
>   - <u>例如:   `temp():a(0) {}`   和 `temp(int i=0):a(i) {}` , 是相同的, 绝对不可以这么写</u>

```c++
构造函数:
    构造函数 是生成(定义)时候触发,析构是结束时触发.
    构造函数 需要在 public 内,也可以在 private内:
	class temp{
    public:
    explicit temp (int& t, int& a=0):t1(t),a1(a)  /*a=0 是默认参数, :后面的是初始化列表  */
      {/*这里面的内容可有可无,跟正常的函数体一样*/ }
    temp():tl(0),al(0) {}     /* 重载构造函数*/
    private:
    int tl, al;
  };

拷贝构造函数:
    拷贝构造函数也在 pubilc 内:  (多多注意深拷贝和浅拷贝以及内存分配问题)
	      temp (const temp& tempthis) { 
           this->t1 = tempthis.t1; this->a1 = tempthis.a1;   //浅拷贝
        } 
     这么写:
        temp (const temp& tempthis):t1(tempthis.t1),a1(tempthis.al) {};
      
    这个拷贝构造函数在使用的时候有两种调用方式:
        temp t1;  
        temp t2 = t1;   
        temp t3(t1);
        temp t4;
        t4 = t3;
        void tempfun(temp t);
        // 函数形参的值传进来也会调用拷贝构造
        // 这几种种赋值方式都会调用同一个拷贝构造函数,所以他们是等价的.
        void  tempfun(temp& t); 
          // 这种不会进行拷贝构造,因为是引用.
```

```c++
析构函数
  析构函数也要在 public 内:
  ~temp() { /*里面是用来释放内存之类的操作 */ }
    /* 带指针的类必须有析构函数, 数值类可以不要(容器除外) */
```



### static属性的构造函数和成员以及在private中的构造函数

**这种写法是 `singleton` 设计模式**

```c++
class A{
  public:
    static  A& getInstall();   /* static 必不可少,因为返回的必须是静态对象,返回值可以当作左值来使用*/
    int setup() { std::cout << "OK" << std::endl; return this->sum; };
  private:
    int sum = 0;    /* 这个数据是不是 static 无所谓的 */
    static int ppt;
    A():sum(0){};      /* 私有构造函数 */
    A (const A& rhs):sum(0){};   /* 私有拷贝构造函数 */
};

int  A::ppt = 10;  // 类内静态数据初始化

A&
A::getInstall(){
 static A a; /* 返回的是一个静态对象,使用的是在private中的默认构造,进程启动时默认创建的对象,在data段 */
 std::cout << "getInstall()" << std::endl;
 return  a;
}


/* 使用 */
int main(void){
    cout << A::getInstall().setup() << endl;  /* 直接这样使用即可, 不需要手动构造 */
  return 0;
}
```



### 静态成员变量和静态成员函数

- **静态成员变量和静态成员函数:(类中的静态成员存在 静态区(全局区),其他的保留c原则,静态函数是文件作用域)**

```c++
static int a;   //如果a存在pubilc 中则外部可以访问,如果是private 则外部不可以访问.不可以在类内初始化
static const int b = 10;    // 常量的静态成员是可以在类内初始化的,如果不是常量则不可以.
static int& num(int a1,int b1);   // 这个成员函数可以访问内部静态成员,并且返回一个静态int类型的引用.
                                          // 这样写就可以当成左值进行修改返回的内部静态成员.
                                          
temp::num() = 300;  // 在类外可以这样来进行对静态成员的赋值,前提是返回的是类内的静态成员引用.
                                          
int temp::a = 19;   // 在类外,在temp第一个定义之前,这样来进行初始化,pubilc和private都可以这样来初始化.

/*    <u><u><u><u><u><u><u><u><u><u><u><u><u><u><u><u><u><u> <u><u><u><u><u><u><u><u><u><u><u><u><u><u><u><u><u><u> */

class OnlyHeapClass
{
public:
	  static OnlyHeapClass& GetInstance(){   // 静态函数
        static OnlyHeapClass only;          // 调用这个函数才会创建这个静态对象
  			return only;
    }
    
    int operator() (const int b) {
        std::cout << "aa = "  << this->a << std::endl;
        return this->a;
    }
    
   void Destroy();
private:
	  
       OnlyHeapClass (int b = 0):a(b) { }
       ~OnlyHeapClass () {  }
        int a;
        static int ppt;   // 静态数据成员, 仅仅是声明 .
};

int  OnlyHeapClass::ppt = 10;  // 类内静态数据初始化的方式,也就是初始化.

int
main(void){
        // 三种调用方式 (1)  . 这三种方式,都使用的是同一个对象.
        OnlyHeapClass::GetInstance().operator()(11);
				// (3)
  			OnlyHeapClass::GetInstance()(11);
        // (2)
        static OnlyHeapClass& pp = OnlyHeapClass::GetInstance();
        pp(10);
}
```

- <u>**静态数据成员在 第一个对象出现前 就已经存在并初始化了**</u>

- **静态成员在类外,在temp第一个定义之前,pubilc和private都可以用类作用域来初始化**

- **static 成员类外存储,求类大小,并不包含在内**

- <u>**静态成员是属于类的,不是属于类的对象的,对象之间共享同一个静态成员**</u>

- **static 成员是命名空间属于类的全局变量,存储在 data 区**

- <u>**静态成员常量 可以在类内初始化,也可以在类外初始化, 但只能选择一种, 变量不可以这么做**</u>

  - ```c++
    class OnlyHeapClass{
       private:
        static  const int cd=5;       // 这是一种初始化方式
    };
    const int  OnlyHeapClass::cd = 4;   // 这是另一种初始化方法, 但是两种不能同时出现
    ```

  - 

- <u>类内的静态数据仅仅只是声明, 脱离对象之外</u>

- <u>**静态成员不可以使用构造来进行初始化!!**</u>

  - ```c++
    class OnlyHeapClass{  private:  static int ppt;  /* 只有一个静态数据成员, 仅仅是声明*/ };
    int  OnlyHeapClass::ppt = 10;  // 类内静态数据初始化的方式
    ```

  - 

- <u>**静态成员变量可以使用非静态成员函数来访问和修改.(不是初始化)**</u>

  - ```c++
    class OnlyHeapClass{
      public:
          static  int& ret() { return ppt; } // 该函数可以返回静态数据成员
          int& nst_ret() const { return ppt;  }   // 可以返回静态成员.
      private:
          static int ppt;  /* 只有一个静态数据成员, 仅仅是声明 */
    };
    int  OnlyHeapClass::ppt = 10;  // 类内静态数据初始化的方式
    
    int main(){
          std::cout << OnlyHeapClass::ret();
          std::cout << OnlyHeapClass().nst_ret();
    }
    ```
    
  - 

- <u>**static 成员函数,只能返回static成员变量,无法返回非static的成员**</u>

- <u>调用静态函数成员的方法有两种, 一个是对象调用, 一个是类名调用</u>

  - ```c++
    class OnlyHeapClass{
      public:
      	static int& ret() {  return a; } // 可以使用对象或类型调用
               int& nst() {  return b; } // 只可以通过对象调用.而且不可以返回 a 成员
    	private:
      	static int a;
               int b;
    };
    ```

- <u>**类模板中如果出现 static 成员,那么每一种实例化都会有自己单独的一个static 的值,和其他的实例化不相同**</u>



==为了避免之后的初始化问题, 应该将静态成员封装到某个只返回该静态成员的成员函数内==

**如果没有调用过这个返回静态成员的函数, 那么就不会引发构造和析构成本, 普通的static可不会有这种好事**

**但是多线程的情况下会有些问题, 解决方式就是 在单线程时, 就把这些类进行初始化和静态函数的手工调用** 

```c++
template <typename T>
class A{
	public:
		static int&  retAi(void) {  static int i = 0;   return i; }	
  // 这样就多了一个静态成员
    static T&  retAt(void) { static T t = 0; return t;  }  // 使用于模版
}
```





### 常量成员函数

- <u>成员函数尾部出现 const ,修饰是 this 指针,也不允许修改`const`对象的任何数据内容.(`const 类名 *cosnt this`)</u>

- <u>**在C++中，只有被声明为const的成员函数才能被一个const类对象调用,也就是说 const 的对象是不可以调用 非const的成员函数的**</u>

```c++
class A{
  public:
		A(int& t):num(t) {}
    void printf() const {std::cout << num << endl;};  
  					/* 成员函数后面的const限制的是this指针,也就是变成了不可修改,非const对象也可以调用*/
  
    void pttr()     {std::cout << num << endl;};  
           /* 这个成员函数后面没有 const 关键字, 那么 有const 修饰的A对象, 是不可以调用这个函数的*/
  private:
  	int num;
};
```



#### 友元

> - <u>友元函数不是定义而是声明</u>
> - 友元声明写在类内的任何地方都可以,因为仅仅只是声明.
> - 友元可以直接访问类内的私有成员.
> - **友元的作用是提高了程序的运行效率(即减少了类型检查和安全性检查等都需要时间开销)**
> - <u>友元的弊端: 破坏了类的封装和隐蔽性,使得非成员函数可以访问类的私有成员</u>
> - **<u>同类对象间无私处,异类之间有友元</u>**
> - <u>友元关系不能被继承</u>
> - <u>友元类是单向访问的`(temp 声明了 ent的类, ent 可以随意访问 temp, 但是temp 不可以访问 ent)`</u>
> - **友元没有传递性**
>   - (若类B 是类A 的友元,类C 是类B 的友元,则类C 不一定是类A 的友元,同样要看类中是否有相应声明).
> - <u>***相同的 `class` 的各个 `objects` 互为友元***</u>
> - 不建议使用 !!

```c++
class temp;
class ent{
public:
    double  Poble(class temp);
};
class temp{
  public:
	friend double Point();    // 使 point() 函数成为temp 类的友元 (原型 double Point()).
	friend class ent;    // 声明一个友元类,ent可以随意访问tmep的所有私有成员,但temp不可访问ent私有成员
	friend double ent::Poble(class temp);   // 使 ent 类中的成员函数 Poble() ,成为temp类的友元.
  private:
	  int t;
};

double  ent::Poble(class temp tp){  tp.t=10 ; return 0.0; }
```



### 操作符重载

- **操作符分为 成员函数和非成员函数**
  - **操作符重载可以写在类内成为针对这个类的对象来使用的, 还可变成全局的,对所有类型和对象使用**
- **所有的操作符重载 都会传入一个隐藏参数 `this` 指针 ,它指向调用者的对象**
  - <u>**传入参数 第一个是符号前面的 ,第二个是符号后面的,如果在类内 `this` 会成为第一个,后传入是第二个**</u>
  - **如果传入两个和this不相干的参数(比如`int cout,char c1`) 则可以不理会this,  而且 cout 会成为第一个 c1第二个**

- **操作符相当于替代了 计算用的函数**
- <u>**不可以修改操作符的使用规则  cout << c1; 不能修改为 c1.operator(cout);  绝对不能**</u>
- <u>**左操符会作用在左边的数据上, 如果是对象的话,就会去对象中寻找操作符重载,并调用**</u>
  - **也就是 `this` 指针所指向的数据, 调用者**
    - `A+=B;    //A和B都是类对象`
    - `this 指向的就是 A`



- **传递者无需知道 接受者是以 reference(引用) 形式接收还是其他模式**

- **加号+ 和减号- 的重载要和  正号+ 和负号-  区分开来**
  - 加减 需要两个参数成员, 返回无名临时的一个值
  - 正负 需要一个参数成员, 返回无名临时的一个值
  
  

- <u>**`>>` 和 `<<`  右移和左移操作符重载 必须写在全局, 绝对不可以写到类内变成成员函数**</u>
  
  - **需要两个参数 一个 `std::ostream 或 std::istream`的引用,  另一个是 你想要输出的对象或其他的什么类型的东西的引用**
  - **返回值必须是 `std::ostream或std::istream`的引用, 用以多次调用 << 做预测**
  
  
  
- <u>**运算符++可以在前可以在后,如果多次++ 则必须在前,不能在后(++++a;可以, a++++; 不可以)**</u>
  
  - <u>想要重载则必须使用.函数重载. 来区分表示前++ 和后++</u>
    - <u>***类内成员的 后置++ 操作符重载, 必须给一个 `int` 类型的占位参数. 前置不需要参数***</u>
  - <u>如果想重载这个运算符,就应该把前++ 和后++ 都写上就最好了</u>
  - <u>***如果重载的是全局的 就给一个参数 ,如果是类内局部的就没有参数***</u>
  
  
  
- <u>**重载了运算符 ()  小括号的对象, 称这种行为的对象是 仿函数, 伪函数,或者 函数对象 .  三个名字**</u>
  - **可以将一个对象当成一个普通函数来调用. 只要适当的修改形参的数量和类型就可以达到重载函数的作用**
  
  
  
- **重载new运算符和delete运算符可以获得用户申请内存空间的大小. 获得空间之后, 会自动调用构造函数**

  - **`void* operator new(size_t nSize){ return malloc(nSize);}`**
    - **`void* operator delete(void* pMem){ free(pMem);}`**
  - **`void operator new[](size_t nSize){return malloc(nSize);}`**
    - **`void operator delete[](void* pMem){ free(pMem);}`**
  - **`void* operator mem_alloc(size_t nSize){ return malloc(nSize);}`**
    - **`void operator mem_free(void* pMem){ free(pMem);}`**

```c++
class temp{
public:
    temp (int a1 = 0, int b1 = 0, int arr1[10] = nullptr ): a(a1), b(b1)
    {
        if (arr1 ==  nullptr ){
            memset(this->arr, 0, sizeof(this->arr));
            return;
        }
        for ( int i =0; i < 10; i--)
            arr[i] = arr1[i];
    }
    
    temp (const temp& t): a(t.a), b(t.b)
    {
        for ( int i =0; i < 10; i--)
            arr[i] = t.arr[i];
    }
    
    int& WR_a() { return a; };
    int& WR_b() { return b; };
    int& WR_arr(int i)
    {
        if (i > 9){
            std::cout << "越界返回最后一个\n";
            return arr[sizeof(arr)/sizeof(int) -1];
        }
        return arr[i];
    }
    
    temp  operator+(const temp &) const ;
    temp& operator+= (const temp& );
    temp& operator-= (const temp& );
    temp& operator++ ();          // 前置++
    temp  operator++ (int );     // 后置++
    temp& operator= (const temp& );
    int&  operator[] (int );
    int   operator() (int );
    void* operator new(size_t);
    void* operator new[] (size_t);
    void operator delete (void*);
    void operator delete[] (void*);
    
private:
    friend temp& operator+= (temp&, const temp&);// 友元加等, 跟上面有冲突了, 两者选其一即可
    mutable int a, b;  // mutable 关键字,可以在类对象为const时, 仍可以修改这个变量,无法与static一起使用
    int arr[10];     // 注意深拷贝, 但是不涉及 加减法 以及 += 和 -= 的改变
};

/*&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&*/
/* 加 操作符重载: */
 inline temp 
 temp::operator+ (const temp& p) const{//操作符重载类内部标准模版operator+构成函数名,中间不能有空格
	  return temp (this->a + p.a, this->b + p.b );   // 直接返回一个匿名对象
  } 

/*&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&*/
/* 加等 操作符重载: */
 inline temp&
 temp::operator+= (const temp& p) {
   this->a += p.a;
   this->b += p.b;
   return *this;
 }

/*&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&*/
/* 乘法 操作符重载,  需要在返回值添加const , 否则会出现 (t1* t2) =t3 ;  的奇怪场面 */
const inline temp
temp::operator* (const temp& t){
	 temp t1(t);
   t1.a *= this->a;
   t1.b *= this->b;
	 for(int i = sizeof(t1.arr)/sizeof(int) -1; i > 0; i--)
      t1.arr[i] *= this->arr[i]
   return t1;
}

/*&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&*/
/* 加等 友元操作符重载 (也就是全局操作符重载), 如果这么写的话,全局和类内部就冲突了,那么留下一个就好了 */
 inline  temp&
 operator+= (temp& l, const temp& r){
 	 l.a += r.a;           //因为是友元 所以可以直接访问内部数据.
   l.b += r.b;
   return l;
 }

/*&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&*/
/* 减等  友元操作符重载 */
inline temp&
operator-= (const temp& p){
   this->a -= r.a;
   this->b -= r.b;
   return *this;
}

/*&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&*/
/* ++ 自增 操作符重载 , ++可在前可在后, 如果多次++, 则必须在前. (++++++temp) 自增3次*/
  /* 想要重载则必须使用.函数重载. 来区分表示前++ 和后++. */ 
  /* 如果想重载这个运算符,就应该把前++ 和后++ 都写上就最好了. */
  /* 如果重载的是全局的 就给一个参数 ,如果是类内局部的就没有参数 */
 inline temp&
 temp::operator++ (){ // 类内 重载的是前置++  先加 后使用 ,可以连加
 	 this->a++;
   this->b++;
   return *this;
 }

/*&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&*/
 inline const temp               // int t  是占位参数,没用处.
 temp::operator++ (int t){ // 类内 重载的是后置++  先使用 后加 ,不能连加
 	 this->a++;
   this->b++;
   return temp(this->a -1, this->b -1);
 }

/*&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&*/
/* 全局 ++ 自增操作符, 和 类内的差不多, 函数体内容差不多 */
 inline tmep&
 operator++ (temp& t ) { ...; return t; }     // 前置++, 可连加
 inline const temp 
 operator++ (temp& t, int p) { ...; return temp(this->a -1, this->b -1); } //后置++ ,不可连加

/*&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&*/
/*  << >> 最为常用的左移右移操作符 重载: (!!只能写在全局,绝对不要写在类内!!), 可连续输入 */
 inline  std::ostream&
 operator<< (std::ostream& os, const temp& p){
    os << ' ' <<p.WR_a << " , " << p.WR_b << ' ' << " , arr:";  
   	for (int i =0; i<10 ; i++)
      os << p.WR_arr(i) << ' ';
    return os << std::endl;
 }

 inline std::istream&
 operator>> (std::istream& is, temp& p){
    std::cout << "a: ";
    is >> p.WR_a;
   std::cout << "b: ";
   is >> p.WR_b;
   std::cout << "arr[10]: "
   for (int i=0; i<10 ; i++)
     is >> p.WR_arr(i);
   return is;
 }

/*&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&*/
/* = 等号 运算符重载, 注意内存空间和自身赋值, 还有深拷贝和内存泄漏 */
inline temp&
temp::operator= (const temp& t){   // 可连续赋值
    if(this !=  &t ){  // 防止自身赋值,也防止自身释放,必须要写,需要判断地址,( temp t =  temp t );
        this->a = t.a;
        this->b = t.b;
        memcpy(this->arr, t.arr, sizeof(this->arr) / sizeof(int));
    }
    return *this;
}

/* 替换规则 ==================================================================== */
temp p = p1 + p2;  /*等价于*/  temp p = operator+(p1,p2);  // 内部替换
temp p = p1 - p2 - p3; /*运算规则是*/ temp p = operator-(operator-(p1,p2),p3);   //替换规则
/*  =========================================================================== */

/* [] 重载 数组运算符, 其实返回的就是 对象内 某个数组类型的 数组值. */
// const 对象要使用的 重载函数, 返回值应该也使用 const
const  inline int&  
  temp::operator[] (int i)  const {
  if ( i>9 ){
    std::cout << "越界"
      return -1;
  }
  return this->arr[i];
}

// 非const 对象要是用的 重载函数 , 可以直接调用 const的重载函数, 并且进行转换

inline int&
  temp::operator[] (int i){
  return const_cast<int&> ( static_cast<const temp&>(*this)[i]);
  //static_cast<const temp&>(*this)[i] 这里调用了 const的[] 重载函数. const的位置是对的
  
}


/*&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&*/
/* () 重载小括号:(经常用在sql内), 或者 仿函数 , 如果是 静态的话 static  那么就可以直接使用*/
   inline int 
   temp::operator() (int n){ // 第一个括号是运算符重载,第二个是需要传入的参数
      return this->a * n;    // 随便写的,这些东西基本上都会用在sql内使用,而且各种各样的重载版本
   }
      使用范例:  temp p ; int a = p(10);  //a 就等于了 10* (p.a); , 就相当于调用了 p() 函数


/*&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&*/
/* new delete 重载操作符: (new 应该返回一个万能指针),这里可以在operator和 new delete 中间出现空格. */
  /* new[] delete[] 重载操作符, 就是数组 */

   inline void* 
   temp::operator new(size_t size){   // 返回一个指向内存的万能指针. 获得空间之后, 会自动调用构造函数
     return  malloc(size);  // 虽然只是使用了malloc 来代替,但是可以触发 构造函数 来给类分配空间和值
   }                          // size 这个参数是默认的  sizeof(temp) , 不会被修改.

   inline void*                     // 虽然这两个声明不同, 但是内容相同.
   temp::operator new[] (size_t size){
       return  malloc(size);
   }
    使用范例和解读:  
        temp* p = new temp(10); // 申请内存空间,调用形参为一个int 的构造函数.
               /*解析原型为: temp p(temp->operator new(sizeof(temp)))(10); */
        temp* p1 = new temp;    // 申请内存空间,调用一次默认构造函数
        temp* p2 = new temp[10] // 申请内存空间,调用10次默认构造函数.与 new 重载无关, 是new[]
       // 解析原型: p2->operator new[](sizeof(temp[10]));    //会触发 10次默认构造
       // 但是需要 operator delete[](void* p);  来释放,  因为new[] 数组 就需要 delete[]  这是定则.

inline void
temp::operator delete (void* p){// 因为上面申请的是一个void指针,所以这里也用void也更好.
    if(p != nullptr)
        free(p);  // 也会触发 析构! ,因为new 是给类来分配空间 ,所以直接释放类就好了.
}


inline void
 temp::operator delete[] (void* p){ // 与上面的 new[] 来对应,要不然可没法析构数组new[] 了
  if ( p ) {
    free(p);   // 也会触发 析构,并且是多次触发! 因为new 是给类来分配空间 ,所以直接释放类就好了.
  }
  p = NULL;
}
       调用范式: delete p;     // 直接定义的p 直接释放.
                delete [] p2;  // 因为是数组所以这样来释放,一一对应,因为p2有10个元素,就会触发10次析构

/*&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&*/
!&& || 操作符重载:(别用,不能和正常操作符有同一种操作,而且不稳定,也可能会出现编译报错)


```

- operator+ 构成函数名,中间不能有空格,而且可以写在全局也可以写在类内部.
- 操作符重载的返回值一定要小心,尽量返回一个值,而不是引用(引用很少很少用).
- c++不允许用户自定义新的运算符,只能对已有的c++运算符进行重载.
- 很多运算符都可以重载,但是` .()成员选择 .*(成员对象选择)  ::(域解析) ?:(条件操作符)`这四个运算符不可以重载定义.
- 不允许改变原有操作符的基本意思,以及运算符需要的对象数量.( 比如 a+b 需要a和b  但是a+ 放这里就意义不明了).
- 当重载了 + 和* 运算符之后,c++还是按照 先算乘除 *  后算加减来进行运算的.不能改变运算符的优先级别.
- 重载不能改变运算符的结合性.(如 赋值运算符"=" 是右结合性(自右向左),重载后仍为右结合性).
- 重载运算符的函数形参不可以出现默认的参数.`(temp operator+(temp &p1,temp &p2 = 0); //这样是不可以的`
- 重载运算符必须和用户定义的自定义类型的对象一起使用,其参数至少应有一个是类对象(或类对象的引用).
- 重载运算符不可以曲解原先运算符的本意( a + b 为加法,  不可以出现重载为减法的样式).
- 运算符重载函数可以写在类内,也可以写在类外然后变成类的友元函数,都是可以的.
- 重载双目运算就是运算符有两个参数,(a + b  中的 + 就是双目运算符).
- <u>无论什么情况都要把 = 号操作重载和 深拷贝构造 两个重要的成员写出来</u>







## 一个操作符new的范例

- **new 必须返回正确的值**
- **内存不足时必须调用 new_handling 函数( ` std::set_new_handler() 函数设置的`)**
  - **而且会无穷循环, 尝试分配内存**
- **如果申请不到内存 就会抛出一个 bad_alloc异常**
- **必须有对付 零 内存需求的准备**
- **避免掩盖正常形式的 new** 
- **基类绝对不可以 遗漏 virtual 析构函数**

```cpp


class base{
public:
    static void* operator new( std::size_t size) noexcept(false) {
        return ::operator new(size);
    }
    static void operator delete (void* pMemory) noexcept(true) {
        ::operator delete(pMemory);
    }
    
    static void* operator new (std::size_t  size, void* ptr) noexcept(false){
        return ::operator new (size, ptr);
    }
    static void operator delete (void* pMemory, void* ptr) noexcept(true) {
        ::operator delete(pMemory, ptr);
    }
    
    static void* operator new( std::size_t size, const std::nothrow_t& nt) noexcept(false) {
        return ::operator new(size,nt);
    }
    static void operator delete (void* pMemory, const std::nothrow_t) noexcept(true) {
        ::operator delete(pMemory);
    }
};

class NewHandlerHolder{
public:
    explicit NewHandlerHolder(std::new_handler nh) : handler(nh) {}
    ~NewHandlerHolder() { std::set_new_handler(handler); };
private:
    std::new_handler handler;
    NewHandlerHolder(const NewHandlerHolder&);
    NewHandlerHolder& operator = (const NewHandlerHolder&);
};



template<typename T>
class NewHandlerSupport{
public:
    static std::new_handler set_new_handler(std::new_handler p) noexcept(true);
    static void* operator new (std::size_t size) noexcept(false);
private:
    static std::new_handler currentHandler;
};

template<typename T>
std::new_handler NewHandlerSupport<T>::set_new_handler(std::new_handler p) noexcept(true){
    std::new_handler oldHandler = currentHandler;
    currentHandler = p;
    return oldHandler;
}


template<typename T>
void* NewHandlerSupport<T>::operator new (std::size_t size) noexcept(false){
 NewHandlerHolder h( std::set_new_handler(currentHandler) );
      return ::operator new(size); // 调用全局的operator new  ,如果分配失败 就调用 currentHandler 指向的代码, 如果成功就恢复 系统默认的
}

template<typename T>
std::new_handler NewHandlerSupport<T>::currentHandler = nullptr;

class Widget : public NewHandlerSupport<Widget>{
public:
    
};

std::new_handler old_glbal_new_handler;
std::new_handler new_glbal_new_handler;


void  my_new_handler(void){
    std::cerr << "my_new_handler 自定义错误" << std::endl;
    std::set_new_handler(new_glbal_new_handler);
///    std::abort();
}


void  glabl_new_handler(void){
    std::cerr << "新设置的函数" << std::endl;
    std::set_new_handler(old_glbal_new_handler);
///    std::abort();
}

int main(void){
    Widget::set_new_handler(my_new_handler);
    try {
        Widget* wdp = new Widget;
        if( wdp == nullptr) throw (std::bad_alloc());
    } catch ( std::bad_alloc()  ) {   //异常处理
        exit(errno);
    }
    std::cout <<std::endl;
}

```









### 继承

- **类的派生和继承:   `基类 = 父类`     `子类 = 派生类`**

- **继承的主要目的是为了搭配虚函数,让代码更加灵活,让子类更加可以变通**

- <u>**派生的关键字 public(公有继承)  和类中的 public(公有成员) 不是一个含义!**</u>
  
  - **派生关键 public 表示继承方式(公有继承).  类中的 public 表示的是访问权限**
  
- <u>**派生类中的成员,包含两大部分,一类是从基类继承过来的,一类是自己增加的成员,**</u>
  
- **从基类继承过来的变现其共性,而新增的成员体现了其个性**
  
- **父类中的构造函数是无法继承下来的**

- <u>**子类可以手动的调用 父类中的构造函数, 来构建父类中的成员**</u>

  - `A(int a, int b):B(a,b) {} //A是子类,B是父类.`

- <u>**派生类成员的标识和访问权限:**</u> 

  - | 继承方式 / 成员类型(公,私,保) | public    | protected | private | 公有成员 还是可以正常修改和访问的,                           |
    | ----------------------------- | --------- | --------- | ------- | ------------------------------------------------------------ |
    | 公有继承(public)              | public    | protected | 不可见  | 拥有继承下来的私有成员,但是不可以显示的更改,只能通过父类来修改. |
    | 保护继承(protected)           | protected | protected | 不可见  | 修改了作用域,不能在类外显示的使用成员,类内可以显示调用.      |
    | 私有继承(private)             | private   | private   | 不可见  | 全部变成私有,类内可以显示更改,外部不可以显示修改.            |
  
- <u>**规则:**</u>

  - **规则1, 只要是基类中的 private 成员,不管是什么继承方式,继承类都不可以访问,但是成员还是存在的,内存中有这个成员的.!**
  - **规则2, 如果是公有继承(public), 派生类中的访问控制权限保持不变,其他和 `规则1` 相同**
  - **规则3, 如果是保护继承(protected), 派生类中 基类中除了private成员,其余的都会变成 protected 成员**
  - **规则4, 如果是私有(private)继承,派生类中的基类中所有的成员在派生类中都是private成员**

- <u>**分析用的三看原则:**</u>
  - **看调用的成员变量是在类的内部还是类的外部**
  - **看派生类的继承方式**
  - **看当前变量在派生类中的变量在基类的访问控制权限**

```c++
class base{
public:
    void basePublic();
    int  pub;
private:
    void basePrivate();
    int  pri;
protected:
    void baseProtected();
    int  pro;
};

class childPub: public base{
public:
    void childPublic(void){
        this->pro = 1;  // 可以访问 , 继承这个类的子类还可以继续访问
        this->pub = 2;  // 可以访问 , 继承这个类的子类还可以继续访问
        // this->pri =0 ; // 不可以访问
    }
};

class childPri: private base{
    void childPrivate(void){
        this->pro = 1;  // 可以访问 , 继承这个类的子类就无法访问了
        this->pub = 2;  // 可以访问 , 继承这个类的子类就无法访问了
        // this->pri =0 ; // 不可以访问
    }  
};

class childPro: protected base{
    void childProtected(void){
        this->pro = 1;  // 可以访问 , 继承这个类的子类还可以继续访问
        this->pub = 2;  // 可以访问 , 继承这个类的子类还可以继续访问
        // this->pri =0 ; // 不可以访问
    }    
};

class A{
    public:
           void funcA(){ cout << "funcA" << endl;}
   private:
        int a;
}

class B{
    public:
           void funcB(){ cout << "funcB" << endl;}
   private:
            A a;    // 有个类A 的对象
}

class C{
    public:
           void funcC(A* a){ cout << "funcC" << endl;}  // 使用到了类A
};

class D:protected A{       //类D 继承于类A , 表示A里面有什么那么D里面就有什么,跟复制一样
  public:
  void funD(){
    cout << a << endl;   //直接就可以使用 a 这个私有成员
  }
};

class E:private : A,B{ ...  };   // E 继承了A 和B , 称为 A和B 的派生类(多继承)
```

- <u>**耦合度:**</u>
  - <u>**高内聚  低耦合   // 程序追求的最好的结果**</u>
    - **高内聚: 一个函数只干一件事,函数的功能越单一越好**
    - **低耦合: 一个函数和另一个函数的关联度越低越好**
- <u>**类和类之间有三种关系 : [B has(复合) A] , [C uses(使用) A], [D is(继承)  A]**</u>
- **类 B 拥有类A 的成员变量,称作 B has A ,  `// 类B 依赖于 类A`**
- **类 C 的成员方法, 需要类A 的形参, 称作 C uses A,  `//类C 依赖于 类A    耦合度小于类B,对A的需求较少`**
- **类 D 继承于类A , 类D 如果是继承类A ,读作类 D is A   `// D继承与A, 耦合度很高`**
- **类 E 继承于类A , 类B ,也称为派生类,只不过是多继承**
- **类 D 继承于类A , 是单继承**



```c++
继承:
class Stu1 {  // 这是个类内部有很多内容 // stu1 被称作基类(也称为父类)
public:
    Stu1(int id, string name){
        this->id = id;
        this->name = name;
    }
    
    void printS(){
        cout << "id = " << this->id << ", name = " << this->name << endl;
    }
private:
    int id;
    string name;
}; 
class Stu2 :public Stu1{   //继承的标准语法,  stu1 被称作基类(也成为父类) , stu2 被称作派生类(子类)
public:
    Stu2(int id1,string name1,int score):Stu1(id1,name1)//委托构造函数,委托父类的构造去构造父类的成员.
    {                                                      //会先调用父类的构造,然后才是子类的,
        this->score = score;                           //如果不是显示调用父类构造,则触发父类的默认构造
    }
    
    void printS(){
        Stu1::printS();   // 调用了基类中的函数成员, 要使用作用域, 或者 this-> 也行
        cout << "socre = " << this->score << endl;
    }
private:
    int score;
};

```

- <u>**c++中的可重用性(software reusability)是通过继承(inheritance)这一机制来实现的.*继承性必须掌握!**</u>
- **父类和子类分别创建对象的时候,内存完全是独立的,毫无关系.顶多就是代码共享**
- **子类内存中有父类的布局,而且是用父类的构造函数构造的,但是和父类没关系,可以看做把父类的东西放入子类,然后子类来使用**
- <u>**子类永远比父类占用的内存大**</u>
- <u>**在调用子类的构造函数的时候,一定会调用父类的构造函数,父类先构造,子类后构造. 析构是子类先析构,父类后析构**</u>

- <u>**如果在子类中有成员与父类成员名相同,而且都是public成员,那么应该通过 :: 作用域来访问父类中相同类型名的值.(私有的话不行)**</u>
- <u>**static 的成员是对整个家族都共享的,无论是哪个子类访问的都是同一个全局静态区的内容.(一个子类修改,那么全部的改变了)**</u>
  - <u>**如果这个 static 成员是 父类的话, 那么所有的子类和父类全部被修改.(因为访问的是同一块内容)**</u>
- **子类对象可以当作父类对象使用.(因为子类中拥有父类所有的内容,内存块也有和父类相同的地方)**
- **子类对象可以直接赋值给父类对象. (只是拿出子类的中父类的内存块来给父类赋值)**
- **<u>父类指针可以指向子类对象( 因为内存块的结构,而且子类中拥有父类中的所有内容,所以那一块内存结构相同)</u>**
- **父类引用可以直接引用子类对象.(和指针相同)**

- **<u>定义一个接口,引用一个父类的对象,那么所有的子类都可以直接传递进入,(相当方便,可以看成是整个类家族中的万能指针)</u>**

- **<u>绝对不可以用父类指向子类的指针 进行++ , 来遍历子类对象数组</u>**

```c++
class A{
public:
    void showA() const {
        std::cout << "temp = " << temp << std::endl;
    }
    static int& writeA( int t ){
        temp = t;
        return temp;
    }
private:
    static int temp;
};              // A 是C 的父类


class C: public A{
public:
    void  showC(){
        A::showA();
    }
    void writeC( int t ){
        A::writeA(t);
    }
};    // C 继承 A

template <class T>
class comp{
    T pp;
};

int A::temp = 0;


int
main(void){
    C c1;
    C c2;
    A a1;
    std::cout << "a1 { ";
    a1.showA();
    
    a1.writeA(20);
    std::cout << "a1 { ";
    a1.showA();

    
    c1.showC();
    c2.showC();
    
    c1.writeC(10);
    
    c1.showC();
    c2.showC();
}

/* 输出:
a1 { temp = 0
a1 { temp = 20
temp = 20
temp = 20
temp = 10
temp = 10
*/
```



### 多继承中的虚继承

```c++
出现虚继承的错误:
	class Stu{ int m; };  //父类有个 m
	class Stu2:public Stu{};   // 这个子类继承后也会出现一个父类中的 m 
	class Stu3:public Stu{};   // 这个子类继承后也会出现一个父类中的 m , 目前和 Stu2 没关系 
	class Stu4:public Stu2,public Stu3{};   // 这个子类继承了Stu2和Stu3,那么会出现两个父类的两个 m
                                         // 此时出现了虚继承,因为继承了Stu 两次
解决虚继承方法:
	class Stu{ int m; };  // 第一个父类(基类) 是不需要添加关键字 virtual 的 
  class Stu2: virtual public Stu{};  // 添加关键字 virtual  这是第一代子类,添加是为了第二代的子类
  class Stu3: virtual public Stu{};  // 这个也需要添加关键字, 除非认为这个类不会再有子类了.
  class Stu4: virtual public Stu2,public Stu3 {}; 
               // 这样Stu4 中将只有一个 m 类了,这个m 是 Stu 里面的,不会和 Stu2和 Stu3冲突


虚继承之后, 保留一份 基础类, 并且每个类都存在一个 vpnr指针,  但是最后的子类却没有 vptr指针.,
 而且 基础类是在 内存最末尾分布的空间, 先分布   Stu2和 Stu3, 然后是 Stu4, 最后是 Sut.
```

> - **多继承可能会出现虚继承,造成访问不明确**
>   - **(一般的解决方法是 添加作用域.`Stu4  a.Stu2::m = 10; //使用Sut2的 m`)**
> - <u>**关键字 virtual 除了第一代的基类(第一个没有继承的那个类),剩下的子类都要尽可能的添加,以避免虚继承**</u>
> - **添加关键字之后 m 这个成员是复制的第一个出现 m 这个类型的类,认为他就是这个m 的基类,而无论是不是继承 祖宗中的, 虚基类，只存储一份**
> - **内存格式的开头就是祖宗的类的格式**
> - ==**虚继承的 子类 会额外添加一个指针,  也就是在64位系统上  会增大8字节, 每一个子类都有一个 vptr指针**==
> - **虚表指针是永远只有一个的而不管基类有多少**
> - **虚表是和类相关的，派生类的虚表是派生类的，基类的是基类的**



### 继承类中的vptr指针分布初始化

- **继承类中的vptr指针分布初始化:**
  - **vptr指针是分布初始化的**
  - **如果初始化一个父类的指针去指向一个子类,那么在调用父类的构造器的时候,会将 vptr 指针当作父类来处理.此时会临时指向父类的虚函数表**
  - **将子类对象的空间变成子类对象处理, vptr 指针就从指向父类的表 变成 指向子类的表** 
    - **(不应该在构造函数中出现业务,也就是调用其他的函数成员之类的,初始化就是初始化)**
  - **在调用父类的构造函数时,vptr指针会指向父类的虚函数表,一旦父类初始化完成,则指针转而指向子类的虚函数表**
  - <u>**有多少个父类,那么 vptr指针就需要转换多少次,毕竟一旦类中有虚函数表,那么vptr指针就会指向他**</u> 

> <u>**如何证明 vptr指针的存在方式:**</u>
>
> ```c++
> 定义一个类,不包含 virtual 关键字的任何函数,然后使用 sizeof() 来查看类的大小,
> 随后将 virtual 关键字添加到类中,再次使用 sizeof() 来查看大小,
> 这时的类的大小就会比原先的值增加4个字节(如果是64位 则增加 8个字节).
> ```





## 虚函数和纯虚函数

> - <u>**没有 `virtual` 关键字的函数: 不允许 子类 重新定义这个函数**</u>
> - <u>**有 `virtual` 关键字的函数 :  希望子类进行对该函数的重定义,  但该函数已有默认定义**</u>
> - <u>**有 `virtual` 关键字的函数`(没有定义,也就是 纯虚函数 )`: 子类必须对这种函数重定义. `(pure virtual)`**</u>
> - 虚析构函数是为了解决基类的指针指向派生类对象，并用基类的指针删除派生类对象。



## 纯虚函数

- **纯虚函数:  (虚函数的语法: `virtual 类型 函数名 (参数表) = 0;   //固定格式 没有解释 `)**

- <u>**纯虚函数的意义:**</u>
  - **就是为了让子类去继承我,然后来重写我.(他不需要任何实现的,没有含义,仅仅是一个接口)**
- <u>**无论一个类有多么的复杂,有多少的成员, 一旦他出现了 纯虚函数 那么他就是 `抽象类`**</u>

> - <u>**纯虚函数是一个在基类中声明的虚函数,在基类中没有定义,要求任何派生类都定义自己的版本**</u>
> - **纯虚函数为每个派生类提供一个公共界面( 接口的封装和设计, 软件的模块功能划分)**
> - <u>**如果说一个类拥有一个纯虚函数,就说这个类是一个抽象类.不管这类有没有成员属性,有没有私有成员,只要这个类有纯虚函数,那么这个类就是一个抽象类,抽象类就是并不能够实例化,就算实例化也没有意义.(就是不能定义抽象类的对象)**</u>
> - <u>**如果说一个普通类,继承拥有纯虚函数的类,那就必须要重写这个纯虚函数, 如果不重写纯虚函数,那么这个普通类依然是一个抽象类.依然不能被实例化,如果想实例化, 那么必须重写这个纯虚函数.(有几个纯虚函数,就必须重写几个纯虚函数)**</u>
> - <u>**抽象类和普通类的区别是: 一个不可以实例化,一个可以实例化**</u>

- **不光成员函数可以变成虚函数,析构函数也可以变成虚析构函数.而且还避免了内存泄漏,所以还是要写上 `virtual ~A(){}` .来析构**

- **`virtual` 成员函数会被编译器放入虚函数表中**

- **设计模式经常使用这个纯虚函数变成接口,降低了代码之间的耦合度,一定要分层次,要可拓展,架构师就是设计纯虚函数的~~**
  - **纯虚函数是一个 面向抽象类编程的概念**
  - <u>**正确的纯虚函数架构应该存在如下的规范:**</u>
    - **存在若干个纯虚函数**
    - **存在正常的构造函数.且构造函数内不允许出现非初始化的语句**
    - **必须存在 虚析构函数. 以方便子类进行接口调用(一般是触发了子类的析构的时候就会自动触发抽象类的 虚析构函数)**



```c++
class Shape {
  public:                              // 因为这个类存在纯虚函数,所以不可以创建任何的对象
     virtual  void draw() const = 0;   //没有定义的纯虚函数, 继承的子类必须重定义它

     virtual  void error( const std::string& msg)  //虚函数,有定义, 子类可重定义也可不重定义
	     { ... 定义 }
     int objectID() const   // 普通函数, 子类不可以重定义
       { ... 定义 } 
     virtual  Shape()  {}    // 构造函数也可以变成虚函数.
     virtual  ~Shape() {}    // 析构也是可以变成虚函数的, 但尽量不要变成 纯虚函数.
}
```





## 多态

- **多态 : (面向对象编程的核心,真正要使用的东西),**
- <u>**如果有几个相似而不完全相同的对象,有时人们要求在向他们发出同一个消息时,他们的反应各不相同,分别执行不同的操作.这种情况就是多态**</u>

- <u>**析构函数也可以触发多态,而且还是重要的:**</u>

```c++
    class A{  virtual ~A(){}; }; // 虚析构函数
    class B:public A{ virtual ~B(){};};  // 这个析构虽然名称不同但是还是会触发多态
    void func(A *ap) {  delete ap;}  // 如果传进来的是B(子类),会先调用B的析构然后再去调用A的析构.
                                     // 如果传进来的是A(父类),就直接调用A 的析构函数了.
```

- <u>**虚析构函数的好处是: 函数在使用父类指针进行子类的传递的时候,可以在父类函数下直接进行delete , 而且这个delte 会调用子类的,然后再去调用父类的.这样内存就不会泄露**</u>

> <u>**多态发生的三个条件:**</u>
>
> - **1),要有一个父类,拥有一个(纯)虚函数** 
> - **2),子类一定要继承这个父类,重写这个(纯)虚函数.(在父类的成员方法前加 `virtual`, 来构成虚函数)**
> - **3),通过父类指针指向子类对象,进行调用**

- <u>**多态的意义就是:你可以写一个架构来调用未来的代码,通过原有的功能或架构来调用一个新功能的类**</u>

```c++
class one{          //定义了一个类, 他是父类
 public:
    one(string num){        //一个构造函数
        this->num = num;
    }
    
    virtual void print(){    //这是个方法,父类的虚函数(virtual关键字在起作用),也就是可以重定义的,对比默认构造和显示构造
        cout << " 编号 num = " << num <<endl;         //显示构造后,编译器默认给的构造就被隐藏了
    }
    string num;     // 为了方便把他放在了公有成员内,如果是private的话将还应该在写个返回他的值的公有函数就好了.
};

class two:public one{    // 一个子类,他的父类是one(我不打算给他增加子类,所以不在public前添加virtual)
public:
    two(string num):one(num){}    //调用父类的构造函数去初始化 num ,如果num是父类私有成员,那么只能通过父类的构造去赋值.
    virtual void print(){    // 重写了虚函数 print(), 虽然内容一样 ,但还是算重写,这个关键字虽然在这里没作用但是还是写上好
        cout <<" 编号 num = " << num << endl;     
    }
};

class three: virtual public one{   //一个子类 ,他的父类是 one (有可能会给他另一个子类)
public:
    three(string num):one(num){}   // 还是调用 one 的构造函数,如果num是父类私有成员,那么只能通过父类的构造去赋值.
    virtual void print(){    // 重写了虚函数 print(), 虽然内容一样 ,但还是算重写, 这个关键字虽然在这里没作用但是还是写上好
        cout <<" 编号 num = " << num << endl;  
    }
};

void COUT(one* prt){   //写在全局的一个架构函数,形参是父类的指针,(只能是父类的指针或引用)
    cout << "显示编号" << endl;
    prt->print();    //调用的是传入进来的形参的成员函数.
}

int main(void){   // 开始主函数
    one* ONE = new one("一号");   // 这是父类 给了他 一号
    two* TWO = new two("二号");   // 这是子类 给了他 二号
    three* THREE = new three("三号");  // 这是子类 给了他 三号
    
    COUT(ONE);    // 会打印 : 显示编号 编号 num = 一号
    COUT(TWO);    // 会打印 : 显示编号 编号 num = 二号
    COUT(THREE);  // 会打印 : 显示编号 编号 num = 三号   : 结束
                  // 这就是多态,使用虚函数来进行重载
}  //  结束   懒得写 delete 了    //此代码可以直接粘贴运行测试.

```

> - **代码中如果不添加 `virtual` 为什么会变成这样呢?**
>   - **这是因为编译器默认做了一个安全处理,编译器不管传递子类对象还是父类对象,如果统一执行父类的方法,那么一定可以被成功执行**
>   - **这个时候子类的那个同名成员函数就被忽略了**

- **如果想要在将来重载的成员函数,尽量都写上 virtual 关键字,无论基类或者继承类(为了健壮性)**
- **只要是通过父类的的指针,并且重载修改子类中父类的虚函数来达到成员的添加,只要继承一个父类那么就可以随便添加一个接口的各种状态**

- **不光成员函数可以变成虚函数,析构函数也可以变成虚析构函数.而且还避免了内存泄漏,所以还是要写上 `virtual ~A(){}` .来析构**

- **多态对于编译器来讲,也是一个动态联编,也是一个迟绑定**





## 多态的原理

- <u>**多态的原理: (借助虚函数表和vptr指针来实现)**</u>

> 1. **如果说一个类有 virtual 虚函数关键字的时候, 在编译器开辟空间的时候,会默认增加一个指针,vptr.(在内存中所有成员的前面)**
>    1. **如果子类内没有 `virtula` 这个关键字的存在, 但是父类中拥有这个关键字, 那么子类也会拥有一个 vptr 的指针**
> 2. **这个指针会指向一个虚函数列表(这个表存在常量区,代码段或bss段),里面存放了所有 virtual 修饰的成员头指针(函数,析构等等)**
> 3. **当定义父类指针来使用子类 类型来开辟内存空间后,`(父类*p = new 子类;). // 只能这样来申请指针,指向子类中父类的那一部分.`**
>    1. **调用父类和子类虚函数列表中的某一个函数的时候,会先从子类的虚函数表中开始寻找,**
>    2. **如果没有找到会再去父类的虚函数表中查找,这时如果找到就会输出父类表中的虚函数**
> 4. ==<u>**如果没有在表中找到,那么代表父类和子类的虚函数表中都没有相对应的虚函数,这个时候就从父类中查找(因为指针停留在了父类中) 普通函数成员,如果匹配则输出,如果没有匹配的则回到子类,继续寻找,如果还是没有,那么就报错失败,  递归顺序**</u>==

```c++
#include <iostream>
using namespace std;
class fu{ //父类开始, 为了方便就不写构造和析构以及私有成员
public:
    virtual void add(int a)
    {cout << "fu::virtual add(int).." << endl;}   // 在父类虚函数表中
    
    virtual void add1(int a,int b)
    {cout << "fu::virtual add1(int,int).." << endl;} // 在虚函数表中
    
    void add2(int a,int b,int c)
    {cout << "fu::add2(int,int,int).." << endl;}   //不在表中, 是父类作用域中的函数
};  //父类结束



class zi:public fu{  // 子类开始,继承父类,
public:
    void add(int a)
    {cout << "zi::add(int).." << endl;}  //覆盖了父类析构函数
    
    void add1(int a,int b)
    {cout << "zi::add1(int,int).." << endl;} // 覆盖了父类虚构函数
    
    void add2(int a,int b,int c)
    {cout << "zi:add2(int,int,int).." << endl;} //子类作用域中的函数
};  // 子类结束,因为父类也有最后这个成员,所以触发重定义,(子类指针时隐藏父类),(父类指针时忽略子类)



int main(void){     // 主函数开始
    fu* fup = new zi;   //父类的指针fup 指向子类中父类的那一部分,在堆上申请内存空间.
    fup->add(10);       //输出 zi::virtual add(int).. 父类虚构函数被子类同名同形参的覆盖掉了,然后输出子类
    fup->add1(10,20);    //输出 zi::add1(int,int).. 父类虚构函数被子类同名同形参的覆盖掉了,然后输出子类
    fup->add2(10,20,30); //输出 fu::add2(int,int,int).. 父类中的这一部分并不是虚构函数,
    // 父类指针会从 子类中查找与 父类的虚函数表内 相同的函数,一旦发现子类中有重定义的,则使用子类的函数,隐藏掉父类中的虚构函数, 又因为是父类指针指向了子类,所以直接忽略其余子类中特有的所有成员.
    
    zi* zip = new zi;    // 子类的指针zip 指向子类,  在堆上申请内存空间.
    zip->add(10);        // 输出 zi::add(int)..         虚构函数父类成员被隐藏
    zip->add1(10,20);    // 输出 zi::add1(int,int)..    虚构函数父类成员被隐藏
    zip->add2(10,20,30); // 输出 zi::add2(int,int,int). 重定义了父类中的函数,显示子类中的
    cout << sizeof(fu) << endl << sizeof(zi) << endl;   // 会返回 8 ,因为有个this指针和vpte指针
} 
// 重载 对普通函数有效(只能同作用域重载,不包括跨类重载).
// 重定义 对跨类中函数有效(只能对函数名相同,返回值相同,形参相同的两个不在同作用域内的父类和子类起作用).
// 重写 仅仅对两个父类和子类中的虚函数起作用.
```

- <u>**重载,重定义,重写,  是整个多态原理的关键中的关键**</u>
- **当类中声明虚函数时,编译器会在类中生成一个虚函数表**
  - **虚函数表是一个存储类成员函数指针的数据结构**
  - **虚函数表是由编译器自动生成与维护的**
  - **virtual 成员函数会被编译器放入虚函数表中**
  - **存在虚函数时,每个对象都有一个指向虚函数表的指针(vptr指针).这个指针会存在类中,占用4或8个字节(无论多少个虚函数,只能有一个指针)**
- <u>**相同的函数,父类是普通函数,子类是虚函数,那么父类指针访问的是父类的,子类指针访问的是子类的,不存在反向继承访问**</u>







### 抽象类

- <u>**一旦拥有一个或多个纯虚函数的类,那么这个类就是抽象类**</u>
- **抽象类不能够实例化,也没有意义来实例化,因为纯虚函数没有函数体,没有实现,仅仅只是个接口,供子类来重写的**



### 面向抽象层编程

- **依赖倒转,可以解决依赖层和 main() 函数之间的耦合度的减少**
- **可以无关联的其他东西,只写自己的,我只是根据我的抽象层进行编写,我不关心其他什么东西存在**
- **这么写完之后对我的维护度也很高,意义就是拓展性很强,每在增加一个新的实例化,都非常简单,只要继承抽象层就可以了**
- **拓展性很强,耦合度很低,对业务层(main()), 实现层解耦合的作用**





### 重载,重写,重定义

- <u>**重载 , 重写 , 重定义的区别: **</u>
  - **重载发生在同作用域的普通函数之间,  重写.重定义发生在父类和子类两个类之间**
- <u>**重载: 一定是同一个作用域下,(同名函数才会发生重载,也就是普通函数)**</u>
- <u>**重定义: 是发生在两个不同的类中,一个是父类,一个是子类**</u>
  - <u>**普通函数重定义:**</u>
    - **如果父类的普通成员函数`(没有virtual)`,被子类重写,说是重定义.(默认为子类,需要作用域才能调用父类函数)**
  - **<u>虚函数重写(也就是重写):</u>**
    -  **如果父类的虚函数,被子类重写,就是虚函数重写,这个函数会发生多态.(函数头必须一模一样)**
  - <u>**重写:**</u>
    - **也是发生在两个不同的类中,一个是父类,一个是子类,函数必须是虚函数,才可以发生重写.(也就只能是子类了)**





### 虚继承

虚继承用于解决多继承条件下的菱形继承问题（浪费存储空间、存在二义性）。

底层实现原理与编译器相关，一般通过**虚基类指针**和**虚基类表**实现，每个虚继承的子类都有一个虚基类指针（占用一个指针的存储空间，4字节）和虚基类表（不占用类对象的存储空间）（需要强调的是，虚基类依旧会在子类里面存在拷贝，只是仅仅最多存在一份而已，并不是不在子类里面了）；当虚继承的子类被当做父类继承时，虚基类指针也会被继承。

实际上，vbptr 指的是虚基类表指针（virtual base table pointer），该指针指向了一个虚基类表（virtual table），虚表中记录了虚基类与本类的偏移地址；通过偏移地址，这样就找到了虚基类成员，而虚继承也不用像普通多继承那样维持着公共基类（虚基类）的两份同样的拷贝，节省了存储空间。

### 虚继承、虚函数

- 相同之处：都利用了虚指针（均占用类的存储空间）和虚表（均不占用类的存储空间）
- 不同之处：
  - 虚继承
    - 虚基类依旧存在继承类中，只占用存储空间
    - 虚基类表存储的是虚基类相对直接继承类的偏移
  - 虚函数
    - 虚函数不占用存储空间
    - 虚函数表存储的是虚函数地址





## 普通函数重载

> - `函数的返回值,函数形参列表(参数的个数,参数的类型,参数顺序){};`
> - <u>函数重载的必要条件:  函数名相同, 参数列表不一样,参数个数不同</u>
>   - <u>函数返回值并不是构成函数重载的条件</u>
>   - (在c 中实际这重载函数并不是同样的文件名,都是被更改过的,然后去替换)

```c++
普通函数重载实现原理:用v c i f l d 表示 void,char,int,float,long,doube以及其他引用,然后写入函数名后.
	void func(char a) ;    //在编译时候就变成了, func_c(char a);
	int  func(int a, double b,char c);   //在编译时候就变成了, func_idc(int a, double b,char c);
```





## 占位参数-也称默认实参

```c++
 占位参数:存在于函数形参列表中,主要用途是 亚元 ,其实就是一个没有任何意义的参数,为了占位置.
    例;  
    int fun(int a,int b,int);    // 第三个参数是用不到的,但是调用的时候还是得传递参数.
    int fun(int a,int b,int = 0);    // 与上面相同只不过有个默认参数.
```



## inline内联函数

函数是否真的会变成 inline 是由编译器来决定的, 通常函数越短, 就越有可能让编译器做成inline.

**inline 对  virtual  函数无效, 不可以进行优化. 因为vitrual 是运行时确定执行哪个函数, 而inline 是编译时确定**

<u>**可能会提高性能,但却需要付出成本和代价,  不应该对构造和析构函数使用 inline**</u>

**过度使用会导致目标代码(.o)变得很庞大**

**编译器会对一个 inline 函数进行执行域境优化**

**inline 是编译期行为, 虽然可能会将 inline函数嵌入代码块内, 但是仍会有一个函数本体, 毕竟有可能会被取函数地址, 也就是说 代码块内可以替换,  同时也会生成函数本体 以供函数指针之类的使用**

**常用于头文件内**

==**如果 inline 函数的内容被修改, 那么需要重新编译所有使用过 inline 函数的代码**==

```c++
inline   double 
  函数名  ( 参数1 , 参数2)
{
 函数体.   
}
```



### 临时对象

```c++
class A{
	public:
  	A (const A& a ):t(a.t) {};
  private:
  	int t;
};

A                     // 绝对不可以返回 引用 reference
print (const A& a){
	return  A (a);    //这个函数返回的就是一个匿名的临时对象, 
}
```



## new和delete运算符

- **`new` 和 `delete`   (是运算符,而不是函数,运算符比函数执行效率高)**
- **`malloc()` 和 `free()`  是函数,标准库,来自 `stdlib.h`   `malloc.h`**
- **`new` 和 `delete`   是操作符, c++整合进入标准库的  iostream**
- <u>`new` 和 `malloc` 的区别:</u>
  - **`new`在实现中会调用 `malloc` 并且由编译器安插调用构造函数的代码**
  - **`new` 失败 会抛出异常,  `malloc` 失败则返回0**
  - <u>`new` 在初始化对象的时候可以触发构造函数,但是 `malloc` 却不可以初始化对象,而且这属于c 并不应该用在c++中</u>
    - **但是在 重载操作符的时候  是可以触发构造函数的.**
- <u>`delete` 和 `free` 的区别:</u>
  - **`delete` 在释放类的时候会自动调用析构函数,防止内存泄漏, 但是`free()`是不会调用析构,类里面申请的独立空间就会无法释放**
    - **但是在 重载操作符的时候  是可以触发析构函数的.**
- <u>**new 和 delete 的调用关系**</u>
  - `new` -> `operator new`  -> `malloc`
  - `delete` ->  `operator delete`  -> `free`
- <u>**`new` 做了三件事: `string* ps = new string("hello");`**</u>
  - **1). 找一块内存,并返回地址. `void* mem = operator new(sizeof( string ) ); 分配内存`**
  - **2). 将这块内存转换为需要的类型.  `ps = static_cast<string*>(mem); //转换类型 `**
  - **3).在这块内存上构建类对象.也就是调用构造函数. `ps->string::string("hello"); //编译器才可以调用的构造函数`**
- <u>**`delete` 做了两件事: `delete ps;`**</u> 
  - **1). 先进行析构函数的调用(如果是数组,则每个元素都会唤醒析构函数.). `string::~string(ps);`**
  - **2).释放内存. `operator delete(ps);  该函数内部调用的是 free(ps);`**
- <u>**如果使用 `string *p = new[10];` 来分配一个数组, 但是却使用 `delete p;` 来进行释放,那么会发生下面的问题:**</u>
  - **仅仅会唤醒一次析构函数, 删除掉最前面的一个元素,其他的元素内存块会被删除,但里面记录的是指针,并不是真正的数据**
  - **会造成内存泄漏**
- 对象在创建的同时要自动执行构造函数， 对象消亡之前要自动执行析构函数。由于malloc/free 是库函数而不是运算符，不在编译器控制权限之内，不能够把执行构造函数和析构函数的任务强加malloc/free。 
- **有一个 new 操作就需要一个 delete , 有一个 new[] 就需要 delete[].  (运算规则)**
- <u>**使用 `new` 分配的内存块是以 16的倍数单位进行补全, 并在头部和尾部各添加额外的4字节标识字段**</u>
  - **如果 一个类对象需要的内存为 12字节,则系统会给分配 `(12+4+4+12)=32` 字节,`(如果是调试模式下,会更大,再+8, 因为会存在调试信息.)`**
  - **如果一个类对象需要的内存为4字节, 则系统会分配 `(4+8+4)=16`字节.`(填充字段最后计算)`**
  - **如果分配的是一个数组(无论类型), 那么还会多一个4字节的字段,记录了数组中元素的个数,就在元素内存段开头位置的上面**
    - **对象占去12字节, 填充占4字节,填充到16的倍数. **
    - **剩下8字节分为两部分.每部分4字节, 分布在内存块的最开始和最结尾**
      - <u>**里面记录了该内存块是否 已分配或已回收, 和该内存块的总长度( 这个字段是给 `malloc和free`函数看的.)**</u>
        - <u>该字段存储值的规范必须是16的倍数,存储的值也是16进制的</u>
          - <u>当最后一位为0时, 代表该字段以释放并归还给了系统,可以重新使用,进行内存分配</u>
          - <u>当最后一位为1时, 代表该字段已经被分配,也就是正在使用.不可以进行二次分配内存</u>
          - <u>如果存储的是数组,那么该字段的值会向左移动一位`(字节)`,并且最后一位字节设置为`h`</u>

```c++
	  int *p = new int;    //标准范式
    int *a = new int (10);   //创建一个int类型的a 赋值为10
    int *array = new int[10];    // 创建一个有10个元素的int类型数组
    temp * tp = new temp(10,10);     // 在堆上申请空间,使用new 并且可以调用构造函数. 
                                     // 实际就是定义了一个类的指针,并且使用构造函数初始化了.
    temp * tp1 = new temp[5];     // 创建了一个类的数组指针,并且其中有5个元素,每个元素都指向一个类.
                                  // 使用默认构造函数.
    
    delete p;        // 释放一个int
    delete[] array;    //释放一个数组,必须要这样写
    delete tp;     // 释放一个类的指针,并且触发析构函数

定位 new（placement new）允许我们向 new 传递额外的地址参数，从而在预先指定的内存区域创建对象。
  new (place_address) type
	new (place_address) type (initializers)
	new (place_address) type [size]
	new (place_address) type [size] { braced initializer list }
	
	范例：
    // 基础地址
    int *base = new int(1);
    
    //新地址
    int * p = new (base + sizeof(int*)) int (2);
    std::cout << *p << std::endl;
    
    delete base;
    delete(p);
```

- `place_address` 是个指针
- `initializers` 提供一个（可能为空的）以逗号分隔的初始值列表



## nullptr和NULL的区别

- **在C++中, NULL 这个宏等于 一个整形值 0**
  - 如果出现重载或者模版推导的时候, 编译器就无法给出正确答案了.
  - 在C语言中，允许 void* 类型隐式转换为任意指针类型，而C++中不允许这样的强制类型转换，但是可以为任意类型的指针赋0值，因此，在C++中将NULL 定义为0
- **nullptr并非整型类别，甚至也不是指针类型，但是能转换成任意指针类型, 是个纯右值。nullptr的实际类型是`std:nullptr_t`**
  - `is_null_pointer()`  该类模版用于 检查类型是否为 `std::nullptr_t`   ;(C++14)
- **应该使用 `nullptr` 而非 `NULL`, 尤其是模版和函数重载时**





## 智能指针

**更详细的 [智能指针](智能指针.md) 说明**

> `智能指针: 需要头文件 <memory>, 实际上智能指针就是一个类,   //智能指针会自动回收空间`

- <u>**`auto_ptr<>` ; 已经弃用了. 现在的替代方案是 `shared_ptr<>  //使用方法和 auto_ptr<>差不多.`**</u>
- **智能指针可以避免内存泄露**
- **普通指针的三个必要素: **
  - **1). 定义一个指针,指针要指向一个变量**
  - **2) 建立关联**
  - **3) 开辟空间,间接赋值**
- **普通指针: ` A* pa = new A(10);   // A是个类,调用了形参int的构造函数`**
- <u>**智能指针:**</u>
  - **通过重载 -> 和 * 解指针 来自定义智能指针.(能用模板的话就要强纽成相应的类型来释放空间,不可以使用void* 来释放,坑)!**

```c++
普通指针:  A* pa = new A(10);   // A是个类,调用了形参int的构造函数,

#include <memory>
#include <memeory.h>
using namespace std;
智能指针:  shared_ptr<int>    // 写到这里表示 这是一个指向 int 类型的智能指针,这是数据类型.不是变量

初始化. 但是会进行拷贝构造, 相当于两次申请和释放内存
          shared_ptr<A> ptr(new A(10));  
           /* 创建一个智能指针,叫ptr,其实就是个对象,接受一个 new A 的匿名对象的指针(不能写等号) 
            * ptr 是变量名.()括号内的是地址, 调用的是它的构造函数, ptr就指向 (new A)匿名对象的地址.
            * 然后 (10) 来调用类 A 中的构造函数来初始化A对象. 返回地址给 ptr.
            */
					std::shared_ptr<int> pt (new  int  (10));

第二种初始化, 一次申请和释放内存
  			  shared_ptr<A> ptr = std::make_shared<A>(10);  
              // 应该使用模版函数来进行创建和初始化.

使用智能指针:
使用: *pte = 20;    // 跟正常指针差不多. 但是不需要 delete 来释放空间,他会在结束后自动调用自身的析构函数.
```

```c++
/* 函数返回值和 函数参数 */
std::shared_ptr<ClassA> returnShared(std::shared_ptr<ClassA> at){
    std::shared_ptr<ClassA> ret = std::make_shared_ptr<ClassA>();
    return ret;
}

/* 当 智能指针成为返回值时, 就算是在函数内部创建后 离开函数, 那么也不会进行析构, 这是编译器来做的 */
```



## 命名空间

```c++
namespace  std {
  ....           // 标准库的命名空间,主要目的是防止重命名
}

using namespace std;    // 将标准库的东西全部打开,就不需要 空间名:成员 来使用

using std::cout;       // 仅仅声明一个, 只有这个不需要全名 就可以使用了



namespace mainp{        // 自定义一个命名空间. 
    typedef  int  my_int;  //自定义类型
};
```

### using声明

一条 `using 声明` 语句一次只引入命名空间的一个成员。它使得我们可以清楚知道程序中所引用的到底是哪个名字。如：

```cpp
using namespace_name::name;
```

#### 构造函数的using声明

在 C++11 中，派生类能够重用其直接基类定义的构造函数。

```cpp
class Derived : Base {
public:
    using Base::Base;
    /* ... */
};

如上 using 声明，对于基类的每个构造函数，编译器都生成一个与之对应（形参列表完全相同）的派生类构造函数。生成如下类型构造函数：
  
Derived(parms) : Base(args) { }

```

#### using指示

`using 指示` 使得某个特定命名空间中所有名字都可见，这样我们就无需再为它们添加任何前缀限定符了。如：

```cpp
using namespace_name name;
```

#### 尽量少使用using指示污染命名空间

> 一般说来，使用 using 命令比使用 using 编译命令更安全，这是由于它**只导入了指定的名称**。如果该名称与局部名称发生冲突，编译器将**发出指示**。using编译命令导入所有的名称，包括可能并不需要的名称。如果与局部名称发生冲突，则**局部名称将覆盖名称空间版本**，而编译器**并不会发出警告**。另外，名称空间的开放性意味着名称空间的名称可能分散在多个地方，这使得难以准确知道添加了哪些名称。

#### using使用

尽量少使用 `using 指示`

```cpp
using namespace std;
```

应该多使用 `using 声明`

```cpp
int x;
std::cin >> x ;
std::cout << x << std::endl;
```

或者

```cpp
using std::cin;
using std::cout;
using std::endl;
int x;
cin >> x;
cout << x << endl;
```



## ::范围解析运算符

- 分类:
  - 全局作用域符（`::name`）：用于类型名称（类、类成员、成员函数、变量等）前，表示作用域为全局命名空间
  - 类作用域符（`class::name`）：用于表示指定类型的作用域范围是具体某个类的
  - 命名空间作用域符（`namespace::name`）:用于表示指定类型的作用域范围是具体某个命名空间的

```cpp
int count = 11;         // 全局（::）的 count

class A {
public:
    static int count;   // 类 A 的 count（A::count）
};
int A::count = 21;

void fun()
{
    int count = 31;     // 初始化局部的 count 为 31
    count = 32;         // 设置局部的 count 的值为 32
}

int main() {
    ::count = 12;       // 测试 1：设置全局的 count 的值为 12

    A::count = 22;      // 测试 2：设置类 A 的 count 为 22

    fun();                // 测试 3

    return 0;
}
```







## 静态联编和动态联编

- **联编是指一个程序模块,代码之间相互关联的过程**
- <u>**静态联编(static binding):**</u>
  - **是程序的匹配,连接在编译阶段的实现,也成为早期匹配. 重载函数使用静态联编,(因为确定了)**
- <u>**动态联编(Dynamic binding):**</u>
  - **是指程序联编推迟到运行时进行,所以又称为晚期联编(迟绑定). switch 和 if ,多态 都是动态联编的例子**

- **c 和 c++  都是静态联编型语言**

```cpp
int a = 10;
if ( a > 10){}   // 那怕这么写, 在 a 绝对等于10 的情况下,if也会是静态联编,也会运行进行判断和选择.
else {};          // 不会出现编译时就直接确定的情况, 这个时候两个选项的内容都会被编译.
```





## 内存分段

- **bss段:**
  - <u>**bss段（bss segment）通常是指用来存放程序中未初始化的全局变量的一块内存区域**</u>
  - **bss是英文Block Started by Symbol的简称。BSS段属于<u>静态内存</u>分配** 
  - **bss段用来分配未初始化（或初始化为0）的全局变量和静态变量**
- **data段:**
  - <u>**数据段 (data segment) 通常是指用来存放程序中已初始化的全局变量的一块内存区域**</u>
  - **数据段属于<u>静态内存</u>分配**
- **text段:**
  - <u>**代码段（code segment/text segment）通常是指用来存放程序执行代码的一块内存区域**</u>
  - **这部分区域的大小在程序运行前就已经确定,并且内存区域通常属于只读(某些架构也允许代码段为可写,即允许修改程序)**
  - **在代码段中，也有可能包含一些只读的常数变量，例如字符串常量等**
- **堆（heap）：**
  - <u>**堆是用于存放进程运行中被动态分配的内存段，它的大小并不固定，可动态扩张或缩减。**</u>
  - **当进程调用 `malloc` 等函数分配内存时，新分配的内存就被动态添加到堆上（堆被扩张）**
  - **当利用 `free` 等函数释放内存时，被释放的内存从堆中被剔除（堆被缩减）。**
- **栈(stack)：**
  - <u>**栈又称堆栈，是用户存放程序临时创建的局部变量**</u>
    - **也就是函数括弧“{}”中定义的变量（但不包括static声明的变量，static意味着在数据段中存放变量）**
  - **除此以外，在函数被调用时，其参数也会被压入发起调用的进程栈中，并且待到调用结束后，函数的返回值也会被存放回栈中**
  - **由于栈的先进先出(FIFO)特点，所以栈特别方便用来保存/恢复调用现场。**
  - **从这个意义上讲，我们可以把堆栈看成一个寄存、交换临时数据的内存区。**

> <u>**一个程序本质上都是由 bss段、data段、text段三个组成的。**</u>

- <u>**内存顺序:**</u>
  - <u>**最高位内存-> 内核 , 栈(占用最大) , bss(很小), data(很小紧挨着bss), text(垫底) ->最低位内存**</u>





## decltype

decltype 它和 auto 的功能一样，都用来在编译时期进行自动类型推导

decltype 关键字用于检查实体的声明类型或表达式的类型及值分类。语法：

```cpp
decltype ( expression )
```

表达式可以有“对象类型左值/亡值/纯右值". 
对其使用 decltype 的结果分别是 左值引用类型/右值引用类型/对象类型. 

```cpp
// 尾置返回允许我们在参数列表之后声明返回类型
template <typename It>
auto fcn(It beg, It end) -> decltype(*beg)
{
    // 处理序列
    return *beg;    // 返回序列中一个元素的引用
}
// 为了使用模板参数成员，必须用 typename
template <typename It>
auto fcn2(It beg, It end) -> typename std::remove_reference<decltype(*beg)>::type
{
    // 处理序列
    return *beg;    // 返回序列中一个元素的拷贝
}

// remove_reference<decltype(*参数值)>::type   删除引用，  type 是个类型
int main(void)
{
    int *i = 0;
    int *a = 0;
    fcn(i ,a );
    fcn2(i, a);
    
    return 0;
}
```



## typeid在运行时获得对象的类型

#### typeid

- typeid 运算符允许在运行时确定对象的类型
- type_id 返回一个 type_info 对象的引用
- 如果想通过基类的指针获得派生类的数据类型，基类必须带有虚函数
- 只能获取对象的实际类型

#### type_info

- type_info 类描述编译器在程序中生成的类型信息。 此类的对象可以有效存储指向类型的名称的指针。 type_info 类还可存储适合比较两个类型是否相等或比较其排列顺序的编码值。 类型的编码规则和排列顺序是未指定的，并且可能因程序而异。
- 头文件：`typeinfo`

typeid、type_info 使用

```cpp
#include <iostream>
#include <typeinfo>
#include <string>
using namespace std;

class Flyable                       // 能飞的
{
public:
    virtual void takeoff() = 0;     // 起飞
    virtual void land() = 0;        // 降落
};
class Bird : public Flyable         // 鸟
{
public:
    void foraging() {}           // 觅食
    virtual void takeoff() {}
    virtual void land() {}
    virtual ~Bird(){}
};
class Plane : public Flyable        // 飞机
{
public:
    void carry() {}              // 运输
    virtual void takeoff(){}
    virtual void land() {}
};

//class type_info
//{
//public:
//    const char* name() const;
//    bool operator == (const type_info & rhs) const;
//    bool operator != (const type_info & rhs) const;
//    int before(const type_info & rhs) const;
//    virtual ~type_info();
//private:
//
//};

void doSomething(Flyable *obj)                 // 做些事情
{
    obj->takeoff();
    std::string  asdp(typeid(*obj).name());
    cout << typeid(*obj).name() << endl;        // 输出传入对象类型（"class Bird" or "class Plane"）

    if(typeid(*obj) == typeid(Bird))            // 判断对象类型
    {
        Bird *bird = dynamic_cast<Bird *>(obj); // 对象转化
        bird->foraging();
    }

    obj->land();
}

int main(){
    Bird *b = new Bird();
    Plane *p= new Plane();
    doSomething(b);
    doSomething(p);
    delete b;
    b = nullptr;
    return 0;
}
/*
输出如下：
4Bird
5Plane

前面的数字代表该类型名称有多少个字节长度
*/
```

