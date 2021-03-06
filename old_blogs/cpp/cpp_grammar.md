
## 背景

    以前有接触过c++, 但是完整的语法知识还是欠缺, 想借着学习openGL的机会学习cpp, 此文档对此opengl学习过程中关于cpp相关的学习记录下来

    书籍:
        深度探索C++对象模型

## 知识点
1. 指针
- 内存地址
- 指针与引用
    * 指针是内存地址的变量
    * 引用是对象在内存中的地址
    * 指针的值是对象的引用
    * 但是并不是说它们就是一回事
    * 编译器实现, 将引用对待一个对象, 而指针只是一个固长的地址值 

- 赋值: 
    * 指针与类型变量之间的赋值: 值拷贝, 引用拷贝
    * 指针变量间赋值: 默认是赋值变量所存储的内容(所指变量的地址), 如果强迫取指针变量的地址&, 理论上被赋值变量的类型是int* 型, 是可以的, 人工将指针变成二级指针, 但是在编译器里还是一级指针的形式
- 二级,三级指针
    * 内存地址是 int 型的变量, 所以一级指针隐式的也可以存指针的地址, 这得人工来维护
    * 二级指针, 三级指针, 是将这个维护工作交给编译器来做, 将上述的人工约束实现成规范, 人为读代码更容易理解

2. .h .cpp
- 为了更好的接口编程而搞的一种源代码存储形式
- 更有效的保护了代码的具体实现, 阻碍不良企图之人找漏洞
- #ifndef, 重复定义, 重复导入的问题, 每个 .h文件都首尾都需要添加, 那为何编译器不直接默认都做这个检查呢
- .h 放声明, .cpp 实现
- cpp 中的实现, 需带类名, 有什么方法不带的呢? 每个实现都在带上类名, 感觉上重复太多, 直接放在一个类中, 岂不是更好看?
    当然, 都带上类名限定更直观
- .h .cpp 名字建议都与类名一致, 约定如此, 代码更容易维护, 当然可以不一致

3. static
- 静态全局变量, 所谓的全局也只是针对文件, c语言的文件也是作用域划分的一部分, 这跟脚本语言类似, c++ 当然也有这种特性, 但是, namespace是跨类解决作用域的问题, 其他文件可以有相同名字的变量不会发生冲突 
- 静态局部变量, 也是存在全局内存区(.data区), 但是不可被其他函数和文件访问, 每次访问的值会延用上一次的值
- 局部与全局, 只是在作用域上不一样, 内存分布和生命周期是一样的
- static函数, 私有成员函数, 这个功能使用.h 文件不也有这个功能, 不在.h文件里声明就可以做到
    加了static, 即使在.h中声明, 也不可被外界访问
- 主要作用是对外隐藏, 所有的变量和函数声明默认是 extern 的

4. c程序的内存分布
- 明白内存分布, 十分有必要, 与内存分布相关的关键字, 是编写有质量代码的关键
- 命令行参数和环境变量, 栈, 堆, .bss, .data, .text

5. 对象的使用
> Object obj; 与 Object obj = new Object();
- 这两种初始化方式, 什么时候可以使用哪一种
- 直接声明的对象存储在栈上, 栈的空间有限, 2M(不同平台限制不一样, 总之上限比较小), 对象的初始化和释放都是自动管理
- new 出来的对象存放在堆上, 释放需手动 delete, 生命周期完全控制, 可以使用指针传递

6. 数组
- double a[3] 与 double *a
    * 前者 sizeof(a) = 3*8, 后者  sizeof(a) = 4, 是地址长度
    * 为何出现此情况? 数组可能会比较大, 若是一一赋值, 影响效率, 所以当作参数时, 只传入第一个元素的地址
    * 若是指针, 该如何计算数据长度? 计算数组的长度, 必须在数组声明的作用域内计算出来, 当作参数传入子函数

7. float
- 1.0f 必须带小数点, f可选

8. cpp 对象模型
- COFF: Common Object File Format
- Inside the C++ Object Model
- 对象模式
> class data members: static, nonstatic
  class member functions: static, nonstatic , virtual
    * 简单对象模型 A Simple Object Model
        每个 data member 和 member function 都对应一个 slot 索引
    * 表格驱动对象模型 A Table-driven Object Model
        class object 本身包含 data member table 和 member function table 的指针
        data member table 和 member function table 对应每个 slot
    * The C++ Object Model
        + Nonstatic data 置于每一个 class object 之内
        + static data 存放在所有的class object之外
        + static 和 nonstatic funtion member 放在所有的 class object 之外
        + Virtual Function 由有class 产生出指针, 存放在 virtual table (vtbl) 中, 每个class 添加了一个的指针, 指向 virtual table, 这个指针称为 vptr, vptr 的 setting 和 reseeting, 放在 constructor, deconstructor 或 copy assignment 运算符自动完成
        + 每个class 所关联的 type_info object (用以支持 RTTI: runtime type identification) 也经由 virtual table 指出来, 通常放在表格的第一个slot处, (多态的由来?!)
    * 加上继承(Adding Inheritance), 多继承
        * base class 唯一
        
- 对象模型如何影响程序
    * stuct 和 class, 最小哲学
        "一致性的用法" 只不过是一种风格上的问题而已
    * pointer 和 reference
        + 本质上, 一个reference 通常是以一个指针来实现
        + 指针类型 会教导编译器如何解释某个特定地址中的内存内容及其大小
        + cast 是一个编译器指令, 大部分情况下它并不改变一个指针所含的真正地址, 它只影响被指出之内存的大小和其内容
    * OO(Oriented Object), OB(Object Based)
        OO 更有弹性, OB 效率更高(String 就是一个OB设计思想)

9. 构造函数语义学 (The Semantics of Constructors)
- C++ 对象模型看得好爽, 有了对象模型的基础, 速度很快, 书的内容都是干货, 但是篇幅少
- conversion operator
- default constructor, 当编译器需要的时候, 才会合成一个, 若是程序需要初始化, 那是程序员的事情
    * trivial constructor
        实际并不会被合成出山来
    * nontrivial constructor
        + 如果一个class中含有一个 member object(有 default constructor), 那么这个class由编译合成的constructor 是 nontrivial 的
        > 如何避免合成多个default constructor:
        defaultu constructor, copy constructor, destructor, assignment copy operator 以inline方式
        如果不适合inline, 会合成一个  explicit non-inline static
        > 被合成的default constructor 只满足编译器的需要, 而不是程序的需要
        >. 如果class 已声明default constructor, 且含有多个 member object, 编译器会扩张default constructor, 调用所以member object的 default constructor, java中是显示调用super
        + 带有Default Constructor 的 BaseClass
        + 带有一个 Virtual Function 的 class
        > 编译器必须为每一个class object 的 vptr 设定初始值, 旋转适当的 virtual table 地址
        + 带有一个 Virtual Base Class 的 Class 
        > 每种编译器实现有极大的差异, 但是共通的点在于: 必须使用virtual base class 在其中每一个derived class object 中的位置
    * java对类中的基本类型, 都会初始化

- Copy Constructor
    * 有一个参数的类型是其class type, 此种形式是其定义 
        > X::X(const X& x);
        > Y::Y(const Y& y, int = 0);
    * 当一个class object 以另一个同类实体作为初值时, copy construct 会被调用 
    * Default Memberwise Initialization
        > 在必要的时候才由编译器产生出来, 跟default constructor 一样, 对象成员有explicit copy constructor, 或者基类, 继承链上有 virtual function
        > 递归实施 memberwise initialization
            nonclass member 也会被复制
    * Bitwise Copy Semantics (位逐次拷贝)
        > 这样的语法是跟编译器的约定
        > 编译器跟语法也是相辅相承
    * Bitwise Copy Semantics  失效情况
        > 这个失效是 trivial 的意思? 怎么是跟 default constructor反着来, 但是看描述又不是反的

- 程序转化语意学 (Program Transformation Semantics)
    * copy constructor 与 define constructor, 在java中是 clone(), define constructor, 也可以像普通函数一样被调用, 只不过是编译器多了一层保障, 在没明确写的时候, 补充上
    * 明确的初始化操作 (Explicit Initialization)
    * 参数的初始化操作 (Argument Initialization)
    * 返回值的初始化操作 ( Return Value Initialization ) 
        > NRV (Name Return Value), 此优化很大提高的效率, 编译义不容辞的优化操作
    * 在使用者层面做优化 (Optimization at the User Level)
    * 在编译器层面做优化 (Optimization at the Compiler Level)
    * Copy Constructor 是要还是不要

- 成员们的初始化队伍 (Member Initialization list)
    * 必须使用 member initialization list
        > initiate reference member
        > initiate const member
        > base class 中的 constructor 有一组参数
        > member class 中的 constructor 中有一组参数
    * member initialization list 安插在任何中 explicit user code 之前

10. Data 语意学 (The Semantics of Data) 
- Data Member 的绑定
    * 语言本身所造成的额外负担 (overhead) 
    * 编译器对特殊情况所提供的优化处理
    * Alignment 的限制
- Data Member 的布局
- Data Member 的存取
    * static data member, 在class之外, data segment
    * nonstatic data member
    * implicit class object 
- 继承与 Data Member
- 对象成员的效率(Object Member Effective)
- 指向Data Member的指针 (Pointer to Data Member)
- 看完还是有些云里雾里, 留一个大概印象吧

11. 没有返回值, 编译不会报错? 
- 不会报错, 从汇编中了解到, 所谓的返回值, 只是从exa寄存器中读数, 所以不管有没有返回值, 理论上是都可以读到数, 但是不一定是想要的数据
- 无参的函数也可以传参, 但是后面的参数获取不到值

12. const
- 常量折叠
- 常成员函数是不能入变成员变量值的
- 常指针与常变量
- 常成员变量只能在构造函数初始化列表中初始化, 不可以在构造函数体中初始化

13. 内存泄漏
- 内存泄漏一般是指大进程层面
- 一个进程挂掉了, 会由制作系统来负责回收该进程的所有资源
- 但是写代码最好还是按照 RAII(Resource Acquisition Is Initialization)原则 

14. Function 语义学
- Member 的各种调用方式
    * member function
    * nonmember function
    * nonstatic member function
    * static member functions
- Virtual Member Functions
    * passive polymophism
    * active polymophism
    * RTTI: (runtime type identification)
    * 不要在一个virtual base class中声明 nonstatic data members
- 函数的效能
- 指向Member Functions的指针
    * 各种语言的通性, 很多都是从c++ 的设计中借鉴来的, 看着很熟悉
    * 多重继承, 虚函数, 额外开销
    * 编译器, 真是一个大工程, 那么多特性要分析, 也很烧脑
    * 
- Inline Functions
    * #define 的安全替代品
    * 可能会引起代码大小暴涨
    * 多层嵌套, 局部变量, 编译器的优化

15. 构造, 解构, 拷贝语义学 (Semantics of constuction, destruction, and Copy)
- 纯虚函数的存在
    * 可以被静态调用
    * 继承体系中每一个 class object 的 destructors 都会被调用, 所以编译器不能够压抑这个操作
- 虚拟规格的存在
    * 
- 虚拟规格中const的存在
- 重新考虑class的声明
- 无继承情况下的对象构造
- 继承体系下的对象构造
- 对象复制语义学
- 对象的功能
- 解构的语意学

16. 执行期语意学
- 

17. 站在对象模型的类端
- 
