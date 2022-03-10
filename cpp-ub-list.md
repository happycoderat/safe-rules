<img src="logo.png" align="right"/>

# 附录：C++ 未定义行为成因列表 ![Version](https://img.shields.io/badge/version-1.1.1-brightgreen)

&emsp;&emsp;未定义的行为（Undefined Behavior），指程序不可预测的执行效果，一般由错误的代码实现引起。出于效率、兼容性等多方面原因，语言标准不便于定义错误程序的明确行为，而是将其统称为“未定义”的行为，可以是崩溃，也可以是非预期的任意表现。代码质量管理的一个重要目标就是消除会导致未定义行为的代码。

&emsp;&emsp;C\+\+ 标准声明了导致未定义行为的情况，本文梳理了 ISO/IEC 14882:2003 和 ISO/IEC 14882:2011 前 18 章的相关内容，后 12 章的主题为标准库实现，相关内容的主旨在前 18 章中已有体现。
 
<br/>

|   ID   | Circumstance |  Provenance  |
| :----: |    :----     |   :----      |
| 1 | [代码行以反斜杠结尾，且与下一行连接后生成通用字符名称](#_1) | [`11-2.2(2)`](#_1) |
| 2 | [非空源文件未以换行符结尾，或以换行符结尾但换行符之前是反斜杠](#_2) | [`03-2.1(2)`](#_2) |
| 3 | [预处理符号连接产生了通用字符名称](#_3) | [`11-2.2(4)`](#_3) |
| 4 | [在一个逻辑行中单引号或双引号不匹配](#_4) | [`11-2.5(2)`](#_4) |
| 5 | [被引用的头文件名称中出现不合理的字符或字符序列](#_5) | [`03-2.8(2)`](#_5) |
| 6 | [字面整数常量过大，超过所有整数类型的取值范围](#_6) | [`03-2.13.1(2)`](#_6) |
| 7 | [使用非标准转义字符](#_7) | [`03-2.13.2(3)`](#_7) |
| 8 | [修改字符串字面常量](#_8) | [`11-2.14.5(12)`](#_8) |
| 9 | [窄字符串与宽字符串连接](#_9) | [`03-2.13.4(3)`](#_9) |
| 10 | [违反 One Definition Rule](#_10) | [`11-3.2(5)`](#_10) |
| 11 | [具有静态或线程存储期的对象在析构函数调用 std::exit 函数](#_11) | [`11-3.6.1(4)`](#_11) |
| 12 | [函数内的 static 或 thread\_local 对象已析构，之后该函数又被调用并引用到之前已析构的对象](#_12) | [`11-3.6.3(2)`](#_12) |
| 13 | [在对象析构之后使用对象](#_13) | [`11-3.6.3(4)`](#_13) |
| 14 | [指针指向长度为 0 的内存空间并被解引用](#_14) | [`11-3.7.4.1(2)`](#_14) |
| 15 | [内存回收函数抛出异常](#_15) | [`11-3.7.4.2(3)`](#_15) |
| 16 | [内存分配与回收使用不匹配的函数](#_16) | [`11-3.7.4.2(3)`](#_16) |
| 17 | [使用已被释放的指针](#_17) | [`11-3.7.4.2(4)`](#_17) |
| 18 | [对象生命周期已结束，但未调用其有副作用的析构函数](#_18) | [`11-3.8(4)`](#_18) |
| 19 | [在分配空间后，生命周期开始前，或在生命周期结束后，回收空间前，通过指针访问对象](#_19) | [`11-3.8(5)`](#_19) |
| 20 | [在分配空间后，生命周期开始前，或在生命周期结束后，回收空间前，通过 glvalue 访问对象](#_20) | [`11-3.8(6)`](#_20) |
| 21 | [具有静态、线程或自动存储期和 non\-trivial 析构函数的对象，其空间被非兼容类型的对象占据](#_21) | [`11-3.8(8)`](#_21) |
| 22 | [常量对象的空间或曾属于常量对象的空间被其他对象占据](#_22) | [`11-3.8(9)`](#_22) |
| 23 | [通过 glvalue 访问对象，但 glvalue 的类型不符合要求](#_23) | [`11-3.10(10)`](#_23) |
| 24 | [通过 glvalue 引用不相关类型的对象或未初始化的对象](#_24) | [`11-4.1(1)`](#_24) |
| 25 | [浮点类型转换产生的结果无法在相应的空间中表示](#_25) | [`11-4.8(1)`](#_25) |
| 26 | [浮点类型转为整数类型时，整数类型无法存储浮点类型的整数部分](#_26) | [`11-4.9(1)`](#_26) |
| 27 | [整数类型转为浮点类型时，浮点类型无法存储整数的值](#_27) | [`11-4.9(2)`](#_27) |
| 28 | [在序列点之间对同一对象产生多种顺序不定的副作用](#_28) | [`03-5(4)`](#_28) |
| 29 | [表达式的结果未在数学上定义或超出其类型的取值范围](#_29) | [`11-5(4)`](#_29) |
| 30 | [被调用函数的语言连接性与该函数定义的语言连接性不符](#_30) | [`11-5.2.2(1)`](#_30) |
| 31 | [将非 POD 类型对象传入可变参数列表](#_31) | [`03-5.2.2(7)`](#_31) |
| 32 | [用 static\_cast 将基类引用转为派生类引用时，基类引用对应的实际对象并非派生类对象](#_32) | [`11-5.2.9(2)`](#_32) |
| 33 | [用 static\_cast 将基类指针转为派生类指针时，基类指针指向的实际对象并非派生类对象](#_33) | [`11-5.2.9(11)`](#_33) |
| 34 | [用 static\_cast 将成员指针转为基类成员指针时，基类中没有相关成员](#_34) | [`11-5.2.9(12)`](#_34) |
| 35 | [函数指针被转为不兼容的类型并执行](#_35) | [`11-5.2.10(6)`](#_35) |
| 36 | [类型转换时去掉对象 const 属性并修改对象](#_36) | [`11-5.2.11(7)`](#_36) |
| 37 | [对不完整类型的对象取地址，但该对象的完整类型重载了 operator &](#_37) | [`11-5.3.1(5)`](#_37) |
| 38 | [new 运算符第一个数组维度的参数为负数](#_38) | [`03-5.3.4(6)`](#_38) |
| 39 | [用 delete 释放数组漏写中括号，用 delete 释放对象多写中括号，用 delete 释放非 new 表达式的结果](#_39) | [`11-5.3.5(2)`](#_39) |
| 40 | [被 delete 释放的对象或数组类型不符合要求](#_40) | [`11-5.3.5(3)`](#_40) |
| 41 | [用 delete 释放不完整类型的对象，但在对象完整类型声明中有 non\-trivial 析构函数](#_41) | [`11-5.3.5(5)`](#_41) |
| 42 | [对象解引用成员指针时，对象的动态类型不包含成员指针引用的成员](#_42) | [`11-5.5(4)`](#_42) |
| 43 | [对象解引用成员指针时，成员指针为空指针](#_43) | [`11-5.5(6)`](#_43) |
| 44 | [/ 或 % 运算符的第二个操作数为 0](#_44) | [`11-5.6(4)`](#_44) |
| 45 | [指针加减整数，结果超出了指针所在数组的地址范围](#_45) | [`11-5.7(5)`](#_45) |
| 46 | [不在同一数组中的指针相减](#_46) | [`11-5.7(5)`](#_46) |
| 47 | [移位运算符右操作数为负数或超过相关类型比特位的数量](#_47) | [`11-5.8(5)`](#_47) |
| 48 | [对负数进行左移运算](#_48) | [`11-5.8(2)`](#_48) |
| 49 | [将对象赋值给具有重叠区域的对象](#_49) | [`11-5.17(8)`](#_49) |
| 50 | [函数没有通过 return 语句返回明确的值，但函数返回值被使用](#_50) | [`11-6.6.3(2)`](#_50) |
| 51 | [递归地定义和初始化静态对象](#_51) | [`11-6.7(4)`](#_51) |
| 52 | [修改非 mutable 常量对象](#_52) | [`11-7.1.6.1(4)`](#_52) |
| 53 | [使用非 volatile glvalue 引用 volatile 对象](#_53) | [`11-7.1.6.1(6)`](#_53) |
| 54 | [具有 noreturn 属性的函数返回至调用方](#_54) | [`11-7.6.3(2)`](#_54) |
| 55 | [空指针解引用](#_55) | [`11-8.3.2(5)`](#_55) |
| 56 | [对象的实际类型与当前静态类型不相关，并调用其非静态成员函数](#_56) | [`11-9.3.1(2)`](#_56) |
| 57 | [在构造函数或析构函数中调用纯虚函数](#_57) | [`11-10.4(6)`](#_57) |
| 58 | [对象的实际类型与当前静态类型不相关，并调用其析构函数](#_58) | [`11-12.4(13)`](#_58) |
| 59 | [在对象生命周期结束后调用其析构函数](#_59) | [`11-12.4(15)`](#_59) |
| 60 | [基类对象构造完毕之前调用成员函数](#_60) | [`11-12.6.2(13)`](#_60) |
| 61 | [对成员或基类对象的不合理引用](#_61) | [`11-12.7(1)`](#_61) |
| 62 | [将对象指针转为其基类对象的指针时，基类对象尚未开始构造或已结束析构](#_62) | [`11-12.7(3)`](#_62) |
| 63 | [通过 . 或 \-> 调用正在构造或析构的对象的虚函数，而且该对象的类型不符合要求](#_63) | [`11-12.7(4)`](#_63) |
| 64 | [typeid 作用于正在构造或析构的对象，而且该对象的类型不符合要求](#_64) | [`11-12.7(5)`](#_64) |
| 65 | [需要无限递归的模版实例化](#_65) | [`11-14.7.1(15)`](#_65) |
| 66 | [构造或析构函数在 function\-try\-block 的 handler 中访问非静态成员](#_66) | [`11-15.2(10)`](#_66) |
| 67 | [有返回值的函数在 function\-try\-block 的 handler 中没有显式返回](#_67) | [`11-15.3(15)`](#_67) |
| 68 | [在 \#if、 \#elif 的条件中，由宏展开产生了 defined 表达式，或 defined 关键字格式不正确](#_68) | [`11-16.1(4)`](#_68) |
| 69 | [\#include 指令经宏展开后不满足标准格式](#_69) | [`11-16.2(4)`](#_69) |
| 70 | [宏的实参列表中出现预处理指令](#_70) | [`11-16.3(11)`](#_70) |
| 71 | [预处理运算符 \# 的结果不是有效的字符串](#_71) | [`11-16.3.2(2)`](#_71) |
| 72 | [预处理运算符 \#\# 的结果不是有效的符号](#_72) | [`11-16.3.3(3)`](#_72) |
| 73 | [\#line 指定的行号为 0 或大于规定值](#_73) | [`11-16.4(3)`](#_73) |
| 74 | [\#line 指令不符合标准格式](#_74) | [`11-16.4(5)`](#_74) |
| 75 | [用 \#define 定义或用 \#undef 取消定义具有保留意义的名称](#_75) | [`11-16.8(4)`](#_75) |
| 76 | [供语言机制调用的函数不符合条件](#_76) | [`11-17.3.21`](#_76) |
| 77 | [程序实现了应由标准库提供的功能](#_77) | [`11-17.3.22`](#_77) |
| 78 | [未经许可，向 std 命名空间添加声明或定义](#_78) | [`11-17.6.4.2.1(1)`](#_78) |
| 79 | [对标准库，特化模板类成员函数，特化模板类成员模板函数，特化、偏特化成员类模版](#_79) | [`11-17.6.4.2.1(2)`](#_79) |
| 80 | [未经许可，向 posix 命名空间添加声明或定义](#_80) | [`11-17.6.4.2.2(1)`](#_80) |
| 81 | [声明或定义标准库保留名称](#_81) | [`11-17.6.4.3(2)`](#_81) |
| 82 | [编译器未提供标准头文件，但编译时引入了非标准同名头文件](#_82) | [`11-17.6.4.4(1)`](#_82) |
| 83 | [为标准库函数提供不符合要求的参数](#_83) | [`11-17.6.4.9(1)`](#_83) |
| 84 | [多线程调用标准库函数造成 data race](#_84) | [`11-17.6.4.10(1)`](#_84) |
| 85 | [违反标准库函数要求的前置条件，除非标准库函数声明了这种情况会抛出异常](#_85) | [`11-17.6.4.11(1)`](#_85) |
| 86 | [offsetof 用于非 standard layout 类型，或用于计算静态成员以及成员函数的偏移量](#_86) | [`11-18.2(4)`](#_86) |
| 87 | [可变参数列表省略号左侧的形式参数为函数、数组或引用](#_87) | [`11-18.10(3)`](#_87) |
| 88 | [longjmp 跳转使应被执行的析构函数未被执行](#_88) | [`11-18.10(4)`](#_88) |

<br/>
<br/>
<br/>

### <span id="_1">1. 代码行以反斜杠结尾，且与下一行连接后生成通用字符名称</span>
<br/>

如果代码行以反斜杠结尾，预处理器会删除行尾的反斜杠和换行符，使其与下一行连接，如果连接后产生了以 \\u 或 \\U 开头的通用字符名称（universal character name）会导致未定义的行为。  
  
示例：
```
auto s = "\u54\
0D";               // Undefined behavior

auto \u54\
0D = 'a';          // Undefined behavior
```
例中字符串和变量名与下一行连接后产生了通用字符名称，会导致未定义的行为。
<br/>
<br/>

#### 依据
ISO/IEC 14882:2003 2.1(2)-undefined  
ISO/IEC 14882:2011 2.2(2)-undefined  
<br/>

#### 规则
[ID_badBackslash](https://github.com/Qihoo360/safe-rules/blob/main/c-cpp-rules.md#ID_badBackslash)  
<br/>

<br/>
<br/>

### <span id="_2">2. 非空源文件未以换行符结尾，或以换行符结尾但换行符之前是反斜杠</span>
<br/>

示例：
```
namespace NS {
    ....
}\                 // Undefined behavior if this is the last line
```
这种代码在 C\+\+03 中的行为是未定义的，C\+\+11 规定在这种情况下编译器应补全所需的空行。
<br/>
<br/>

#### 依据
ISO/IEC 14882:2003 2.1(2)-undefined  
<br/>

#### 规则
[ID_missingNewLineFileEnd](https://github.com/Qihoo360/safe-rules/blob/main/c-cpp-rules.md#ID_missingNewLineFileEnd)  
[ID_badBackslash](https://github.com/Qihoo360/safe-rules/blob/main/c-cpp-rules.md#ID_badBackslash)  
<br/>

<br/>
<br/>

### <span id="_3">3. 预处理符号连接产生了通用字符名称</span>
<br/>

\#\# 等预处理运算符可以将两个符号连接成一个符号，如果连接后产生了以 \\u 或 \\U 开头的通用字符名称会导致未定义的行为。  
  
示例：
```
#define M(a, b) a ## b

int M(\u54, 0d) = 123;    // Undefined behavior
```
示例代码意在连接 \\u54 和 0d，定义名为 \\u540d 的变量，但会导致未定义的行为。
<br/>
<br/>

#### 依据
ISO/IEC 14882:2003 2.1(4)-undefined  
ISO/IEC 14882:2011 2.2(4)-undefined  
<br/>

<br/>
<br/>

### <span id="_4">4. 在一个逻辑行中单引号或双引号不匹配</span>
<br/>

源文件中的代码行称为物理行（physical source lines），预处理器会将以反斜杠结尾的物理行与下一行连接，连接后为逻辑行（logical source lines），在一个逻辑行中单引号或双引号不匹配会导致未定义的行为。  
  
示例：
```
auto a = "abc\
def";                       // OK

auto b = "abc               // Undefined behavior
def";
```
例中第一个字符串在一个逻辑行中，第二个字符串常量被写到了两个逻辑行中，会导致未定义的行为。
<br/>
<br/>

#### 依据
ISO/IEC 14882:2003 2.4(2)-undefined  
ISO/IEC 14882:2011 2.5(2)-undefined  
<br/>

<br/>
<br/>

### <span id="_5">5. 被引用的头文件名称中出现不合理的字符或字符序列</span>
<br/>

引用头文件名称的相关文法：
```
header-name:
  < h-char-sequence >
  " q-char-sequence "
```
C\+\+03 规定单引号、反斜杠、/\*、// 出现在 h\-char\-sequence 或 h\-char\-sequence 中，双引号出现在 h\-char\-sequence 中会导致未定义的行为，C\+\+11 规定这些情况由实现定义。  
  
示例：
```
#include <"foo">        // Undefined behavior in C++03
#include "foo//bar"     // Undefined behavior in C++03
```
<br/>
<br/>

#### 依据
ISO/IEC 14882:2003 2.8(2)-undefined  
ISO/IEC 14882:2011 2.9(2)-implementation  
<br/>

#### 规则
[ID_nonStandardCharInHeaderName](https://github.com/Qihoo360/safe-rules/blob/main/c-cpp-rules.md#ID_nonStandardCharInHeaderName)  
<br/>

<br/>
<br/>

### <span id="_6">6. 字面整数常量过大，超过所有整数类型的取值范围</span>
<br/>

示例：
```
int a = 0xaaaabbbbccccddddeeeeffff;   // Undefined behavior in C++03
```
例中字面整数常量过大，这种情况在 C\+\+03 中是未定义的，在 C\+\+11 中是 ill formed，不会通过编译。
<br/>
<br/>

#### 依据
ISO/IEC 14882:2003 2.13.1(2)-undefined  
ISO/IEC 14882:2011 2.14.2(3)-illformed  
<br/>

<br/>
<br/>

### <span id="_7">7. 使用非标准转义字符</span>
<br/>

示例：
```
const char* p = "C:\Files\x.cpp";    // Undefined behavior in C++03
```
例中 \\F、\\x 均非标准转义字符，这种情况在 C\+\+03 中是未定义的，在 C\+\+11 中则由实现定义。
<br/>
<br/>

#### 依据
ISO/IEC 14882:2003 2.13.2(3)-undefined  
ISO/IEC 14882:2011 2.14.3(3)-implementation  
<br/>

#### 规则
[ID_literal_nonStandardEsc](https://github.com/Qihoo360/safe-rules/blob/main/c-cpp-rules.md#ID_literal_nonStandardEsc)  
<br/>

<br/>
<br/>

### <span id="_8">8. 修改字符串字面常量</span>
<br/>

示例：
```
*((char*)"oops") = 'O';  // Undefined behaivor
```
<br/>
<br/>

#### 依据
ISO/IEC 14882:2003 2.13.4(2)-undefined  
ISO/IEC 14882:2011 2.14.5(12)-undefined  
<br/>

#### 规则
[ID_missingConst](https://github.com/Qihoo360/safe-rules/blob/main/c-cpp-rules.md#ID_missingConst)  
<br/>

<br/>
<br/>

### <span id="_9">9. 窄字符串与宽字符串连接</span>
<br/>

示例：
```
auto* x = L"123" "456";    // Undefined in C++03
auto* y = L"123" "456";    // A wide string in C++11
```
<br/>
<br/>

#### 依据
ISO/IEC 14882:2003 2.13.4(3)-undefined  
ISO/IEC 14882:2011 2.14.5(13)-implementation  
<br/>

#### 规则
[ID_literal_hybridConcat](https://github.com/Qihoo360/safe-rules/blob/main/c-cpp-rules.md#ID_literal_hybridConcat)  
<br/>

<br/>
<br/>

### <span id="_10">10. 违反 One Definition Rule</span>
<br/>

任何翻译单元不得包含变量、函数、类型、枚举或模板的多个定义，即 One Definition Rule，违反此规则会导致未定义的行为。  
  
示例：
```
// In a.cpp 
struct T {
    int i;
};

// In b.cpp 
struct T {
    long i;    // Undefined behavior, volates One Definition Rule
};
```
<br/>
<br/>

#### 依据
ISO/IEC 14882:2003 3.2(5)-undefined  
ISO/IEC 14882:2011 3.2(5)-undefined  
<br/>

<br/>
<br/>

### <span id="_11">11. 具有静态或线程存储期的对象在析构函数调用 std::exit 函数</span>
<br/>

程序调用 exit 函数后，具有静态或线程存储期的对象开始析构，而这种对象的析构函数再调用 exit 函数会导致未定义的行为。  
  
示例：
```
class T {
    ....
public:
   ~T() {
        std::exit(1);  // Dangerous
    }
};

static T obj;  // Undefined behavior during destruct
```
<br/>
<br/>

#### 依据
ISO/IEC 14882:2003 3.6.1(4)-undefined  
ISO/IEC 14882:2011 3.6.1(4)-undefined  
<br/>

#### 规则
[ID_exitCallInDestructor](https://github.com/Qihoo360/safe-rules/blob/main/c-cpp-rules.md#ID_exitCallInDestructor)  
<br/>

<br/>
<br/>

### <span id="_12">12. 函数内的 static 或 thread\_local 对象已析构，之后该函数又被调用并引用到之前已析构的对象</span>
<br/>

示例：
```
// In T.cpp
void foo() {
    static T sObj;
    ....
}

// In U.cpp
class U {
    ....
public:
   ~U() {
        foo();   // Undefined behavior if ‘sObj’ is destructed
    }
};

U gObj;   // Problematic
```
当例中全局对象 gObj 析构时会调用 foo 函数，如果这时 foo 函数中的局部静态对象 sObj 已析构，会导致未定义的行为，gObj 与 sObj 的析构顺序在标准中是不确定的。
<br/>
<br/>

#### 依据
ISO/IEC 14882:2003 3.6.3(2)-undefined  
ISO/IEC 14882:2011 3.6.3(2)-undefined  
<br/>

<br/>
<br/>

### <span id="_13">13. 在对象析构之后使用对象</span>
<br/>

示例：
```
extern T obj;

void foo() {
    obj.fun();   // Undefined behavior if ‘obj’ is destructed
}
```
当全局对象 obj 析构之后，再调用 foo 函数会导致未定义的行为（如在另一个全局对象的析构函数中调用 foo 函数）。
<br/>
<br/>

#### 依据
ISO/IEC 14882:2011 3.6.3(4)-undefined  
<br/>

#### 规则
[ID_danglingDeref](https://github.com/Qihoo360/safe-rules/blob/main/c-cpp-rules.md#ID_danglingDeref)  
<br/>

<br/>
<br/>

### <span id="_14">14. 指针指向长度为 0 的内存空间并被解引用</span>
<br/>

示例：
```
void foo(size_t n) {
    int* p = new int[n];
    ....
    *p = 123;   // Undefined behavior if ‘n’ is zero
    ....
    delete[] p;
}
```
如果例中参数 n 为 0，对 p 的解引用会导致未定义的行为。
<br/>
<br/>

#### 依据
ISO/IEC 14882:2003 3.7.3.1(2)-undefined  
ISO/IEC 14882:2011 3.7.4.1(2)-undefined  
<br/>

#### 规则
[ID_arrayIndexOverflow](https://github.com/Qihoo360/safe-rules/blob/main/c-cpp-rules.md#ID_arrayIndexOverflow)  
<br/>

<br/>
<br/>

### <span id="_15">15. 内存回收函数抛出异常</span>
<br/>

示例：
```
class A {
    ....
public:
    void operator delete(void* p) {
        if (!p) {
            throw Exception();    // Undefined behavior
        }
        ....
    }
};
```
在 delete 运算符中抛出异常会导致未定义的行为。
<br/>
<br/>

#### 依据
ISO/IEC 14882:2011 3.7.4.2(3)-undefined  
<br/>

#### 规则
[ID_throwInDelete](https://github.com/Qihoo360/safe-rules/blob/main/c-cpp-rules.md#ID_throwInDelete)  
<br/>

<br/>
<br/>

### <span id="_16">16. 内存分配与回收使用不匹配的函数</span>
<br/>

示例：
```
void foo() {
    T* p = new T;
    ....
    free(p);   // Undefined behavior
}
```
例中由 new 分配的内存空间用 free 释放，会导致未定义的行为。
<br/>
<br/>

#### 依据
ISO/IEC 14882:2011 3.7.4.2(3)-undefined  
<br/>

#### 规则
[ID_incompatibleDealloc](https://github.com/Qihoo360/safe-rules/blob/main/c-cpp-rules.md#ID_incompatibleDealloc)  
<br/>

<br/>
<br/>

### <span id="_17">17. 使用已被释放的指针</span>
<br/>

示例：
```
int* p = new int;
delete p;
*p = 123;   // Undefined behavior
```
例中指针 p 被释放之后又被使用，导致未定义的行为。
<br/>
<br/>

#### 依据
ISO/IEC 14882:2003 3.7.3.2(4)-undefined  
ISO/IEC 14882:2011 3.7.4.2(4)-undefined  
<br/>

#### 规则
[ID_illAccess](https://github.com/Qihoo360/safe-rules/blob/main/c-cpp-rules.md#ID_illAccess)  
[ID_danglingDeref](https://github.com/Qihoo360/safe-rules/blob/main/c-cpp-rules.md#ID_danglingDeref)  
<br/>

<br/>
<br/>

### <span id="_18">18. 对象生命周期已结束，但未调用其有副作用的析构函数</span>
<br/>

示例：
```
struct U {
    ....
};

struct T {
   ~T();    // If it has side effects
    ....
};

void* p = malloc(max(sizeof(T), sizeof(U)));
T* pT = new (p) T;
U* pU = new (p) U;     // Undefined behavior
```
设例中 T 和 U 为两个类，第二次调用 new 结束了 pT 指向对象的生命周期，但没有调用其析构函数，如果其析构函数存在副作用，则会导致未定义的行为，可能会造成资源泄露或逻辑错误。
<br/>
<br/>

#### 依据
ISO/IEC 14882:2003 3.8(4)-undefined  
ISO/IEC 14882:2011 3.8(4)-undefined  
<br/>

<br/>
<br/>

### <span id="_19">19. 在分配空间后，生命周期开始前，或在生命周期结束后，回收空间前，通过指针访问对象</span>
<br/>

在对象的生命周期之外，通过指针进行如下操作会导致未定义的行为：  
 - 访问非静态成员函数  
 - 将指针转为基类指针  
 - 用 static\_cast 转换指针（转为 void\*、char\*、unsigned char\* 等情况除外）  
 - 用 dynamic\_cast 转换指针  
  
示例：
```
struct B {
    virtual void foo();
    virtual void bar();
};

struct D: B {
    void bar() override;
};

void B::foo() {
    new (this) D;    // Ends the lifetime of *this
    this->bar();     // Undefined behavior
}
```
<br/>
<br/>

#### 依据
ISO/IEC 14882:2003 3.8(5)-undefined  
ISO/IEC 14882:2011 3.8(5)-undefined  
<br/>

<br/>
<br/>

### <span id="_20">20. 在分配空间后，生命周期开始前，或在生命周期结束后，回收空间前，通过 glvalue 访问对象</span>
<br/>

在对象的生命周期之外，通过 glvalue 进行如下操作会导致未定义的行为：  
 - 对 glvalue 进行 lvalue\-to\-rvalue 的转换  
 - 访问非静态成员函数  
 - 将 glvalue 转为基类引用  
 - 用 static\_cast 转换 glvalue（转为 char& 或 unsigned char& 等情况除外）  
 - 用 dynamic\_cast 转换 glvalue  
 - 将 typeid 作用于 glvalue  
  
示例：
```
struct B { void foo(); };
struct D: B { .... };

void B::foo() {
    new (this) D;    // Ends the lifetime of *this
}

void bar(B& b) {
    b.foo();
    if (typeid(b) == ....) {   // Undefined behavior
        ....
    }
}
```
例中 b.foo 执行后 b 的生命周期结束，之后再对 b 的访问是有问题的。
<br/>
<br/>

#### 依据
ISO/IEC 14882:2003 3.8(6)-undefined  
ISO/IEC 14882:2011 3.8(6)-undefined  
<br/>

<br/>
<br/>

### <span id="_21">21. 具有静态、线程或自动存储期和 non\-trivial 析构函数的对象，其空间被非兼容类型的对象占据</span>
<br/>

示例：
```
struct A { ~A(); };
struct B { ~B(); };

void foo() {
    A a;
    new (&a) B;
}   // Undefined behavior
```
例中局部对象 a 的空间被 B 类型的对象占据，在 foo 函数返回前仍会调用 A 的析构函数，导致未定义的行为。
<br/>
<br/>

#### 依据
ISO/IEC 14882:2003 3.8(8)-undefined  
ISO/IEC 14882:2011 3.8(8)-undefined  
<br/>

<br/>
<br/>

### <span id="_22">22. 常量对象的空间或曾属于常量对象的空间被其他对象占据</span>
<br/>

示例：
```
struct T {
    T();
   ~T();
};

const T obj;

void foo() {
    obj.~T();
    new (&obj) const T;   // Undefined behavior
}
```
<br/>
<br/>

#### 依据
ISO/IEC 14882:2003 3.8(9)-undefined  
ISO/IEC 14882:2011 3.8(9)-undefined  
<br/>

<br/>
<br/>

### <span id="_23">23. 通过 glvalue 访问对象，但 glvalue 的类型不符合要求</span>
<br/>

只应通过以下类型的 glvalue 访问对象：  
 - 对象的动态类型  
 - 用 const 或 volatile 限定的对象动态类型  
 - 与对象动态类型相似的类型（参见 ISO/IEC 14882:2011 4.4）  
 - 与对象动态类型对应的有符号或无符号类型  
 - 用 const 或 volatile 限定的与对象动态类型对应的有符号或无符号类型  
 - 包含上述类型的聚合或联合类型（不包括静态成员类型）  
 - 对象动态类型的基类类型（也包括被 const 或 volatile 限定的基类类型）  
 - char 或 unsigned char  
  
否则导致未定义的行为。
<br/>
<br/>

#### 依据
ISO/IEC 14882:2003 3.10(15)-undefined  
ISO/IEC 14882:2011 3.10(10)-undefined  
<br/>

<br/>
<br/>

### <span id="_24">24. 通过 glvalue 引用不相关类型的对象或未初始化的对象</span>
<br/>

glvalue 的类型与其引用的对象类型不同或没有继承关系，以及引用的对象未初始化，会导致未定义的行为。  
  
示例：
```
struct A { int i; };
struct B { int i; };

int foo(A& a) {
    return reinterpret_cast<B&>(a).i;   // Undefined behavior
}

int foo() {
    int i;
    return i;   // Undefined behavior
}
```
<br/>
<br/>

#### 依据
ISO/IEC 14882:2003 4.1(1)-undefined  
ISO/IEC 14882:2011 4.1(1)-undefined  
<br/>

#### 规则
[ID_castNoInheritance](https://github.com/Qihoo360/safe-rules/blob/main/c-cpp-rules.md#ID_castNoInheritance)  
[ID_localInitialization](https://github.com/Qihoo360/safe-rules/blob/main/c-cpp-rules.md#ID_localInitialization)  
<br/>

<br/>
<br/>

### <span id="_25">25. 浮点类型转换产生的结果无法在相应的空间中表示</span>
<br/>

<br/>
<br/>

#### 依据
ISO/IEC 14882:2003 4.8(1)-undefined  
ISO/IEC 14882:2011 4.8(1)-undefined  
<br/>

<br/>
<br/>

### <span id="_26">26. 浮点类型转为整数类型时，整数类型无法存储浮点类型的整数部分</span>
<br/>

示例：
```
double f = 1e60;
signed i = f;      // Undefined behavior
```
例中 ie60 无法被整型变量存储，导致未定义的行为。
<br/>
<br/>

#### 依据
ISO/IEC 14882:2003 4.9(1)-undefined  
ISO/IEC 14882:2011 4.9(1)-undefined  
<br/>

#### 规则
[ID_narrowCast](https://github.com/Qihoo360/safe-rules/blob/main/c-cpp-rules.md#ID_narrowCast)  
<br/>

<br/>
<br/>

### <span id="_27">27. 整数类型转为浮点类型时，浮点类型无法存储整数的值</span>
<br/>

示例：
```
long n = 1234567890L;
float f0 = n;            // Cannot be represented exactly
double f1 = n;           // OK
```
例中 1234567890 无法被 float 型变量存储，但可被 double 型变量存储。
<br/>
<br/>

#### 依据
ISO/IEC 14882:2011 4.9(2)-undefined  
<br/>

#### 规则
[ID_narrowCast](https://github.com/Qihoo360/safe-rules/blob/main/c-cpp-rules.md#ID_narrowCast)  
<br/>

<br/>
<br/>

### <span id="_28">28. 在序列点之间对同一对象产生多种顺序不定的副作用</span>
<br/>

示例：
```
int a = 0;
a = a++;        // Undefined behavior
a = ++a;        // Undefined behavior
a = a + a;      // OK

volatile int b = 0;
b = b++;        // Undefined behavior
b = ++b;        // Undefined behavior
b = b + b;      // Undefined behavior
```
<br/>
<br/>

#### 依据
ISO/IEC 14882:2003 5(4)-undefined  
<br/>

#### 规则
[ID_evaluationOrderReliance](https://github.com/Qihoo360/safe-rules/blob/main/c-cpp-rules.md#ID_evaluationOrderReliance)  
[ID_confusingAssignment](https://github.com/Qihoo360/safe-rules/blob/main/c-cpp-rules.md#ID_confusingAssignment)  
<br/>

<br/>
<br/>

### <span id="_29">29. 表达式的结果未在数学上定义或超出其类型的取值范围</span>
<br/>

示例：
```
uint64_t u64 = UINT32_MAX * UINT32_MAX;  // Undefined behavior
```
<br/>
<br/>

#### 依据
ISO/IEC 14882:2003 5(5)-undefined  
ISO/IEC 14882:2011 5(4)-undefined  
<br/>

#### 规则
[ID_evalOverflow](https://github.com/Qihoo360/safe-rules/blob/main/c-cpp-rules.md#ID_evalOverflow)  
<br/>

<br/>
<br/>

### <span id="_30">30. 被调用函数的语言连接性与该函数定义的语言连接性不符</span>
<br/>

示例：
```
// In a.c
int foo() {
    return 0;
}

// In b.cpp
int foo();          // Missing extern "C"

int bar() {
    return foo();   // Undefined behavior
}
```
<br/>
<br/>

#### 依据
ISO/IEC 14882:2003 5.2.2(1)-undefined  
ISO/IEC 14882:2011 5.2.2(1)-undefined  
<br/>

<br/>
<br/>

### <span id="_31">31. 将非 POD 类型对象传入可变参数列表</span>
<br/>

示例：
```
std::string str;
printf("%s\n", str);  // Undefined behavior
```
<br/>
<br/>

#### 依据
ISO/IEC 14882:2003 5.2.2(7)-undefined  
ISO/IEC 14882:2011 5.2.2(7)-implementation  
<br/>

#### 规则
[ID_userObjectAsVariadicArgument](https://github.com/Qihoo360/safe-rules/blob/main/c-cpp-rules.md#ID_userObjectAsVariadicArgument)  
[ID_forbidVariadicFunction](https://github.com/Qihoo360/safe-rules/blob/main/c-cpp-rules.md#ID_forbidVariadicFunction)  
<br/>

<br/>
<br/>

### <span id="_32">32. 用 static\_cast 将基类引用转为派生类引用时，基类引用对应的实际对象并非派生类对象</span>
<br/>

示例：
```
struct B {};
struct D: B {};

B b;
D d;
B& rb = b;
B& rd = d;

static_cast<D&>(rb);    // Undefined behavior
static_cast<D&>(rd);    // OK
```
例中 rb 引用基类对象，rd 引用派生类对象，将 rb 转为派生类对象的引用会导致未定义的行为。
<br/>
<br/>

#### 依据
ISO/IEC 14882:2003 5.2.9(5)-undefined  
ISO/IEC 14882:2011 5.2.9(2)-undefined  
<br/>

#### 规则
[ID_downCast](https://github.com/Qihoo360/safe-rules/blob/main/c-cpp-rules.md#ID_downCast)  
<br/>

<br/>
<br/>

### <span id="_33">33. 用 static\_cast 将基类指针转为派生类指针时，基类指针指向的实际对象并非派生类对象</span>
<br/>

示例：
```
struct B {};
struct D: B {};

B b;
D d;
B* pb = &b;
B* pd = &d;

static_cast<D*>(pb);    // Undefined behavior
static_cast<D*>(pd);    // OK
```
例中 pb 指向基类对象，pd 指向派生类对象，将 pb 转为派生类对象的指针会导致未定义的行为。
<br/>
<br/>

#### 依据
ISO/IEC 14882:2003 5.2.9(8)-undefined  
ISO/IEC 14882:2011 5.2.9(11)-undefined  
<br/>

#### 规则
[ID_downCast](https://github.com/Qihoo360/safe-rules/blob/main/c-cpp-rules.md#ID_downCast)  
<br/>

<br/>
<br/>

### <span id="_34">34. 用 static\_cast 将成员指针转为基类成员指针时，基类中没有相关成员</span>
<br/>

示例：
```
struct B { int b; };
struct D: B { int d; };

int D::* mpb = &D::b;
int D::* mpd = &D::d;

static_cast<int B::*>(mpb);    // OK
static_cast<int B::*>(mpd);    // Undefined behavior
```
例中基类没有成员 d，将指向成员 d 的成员指针转为基类成员指针会导致未定义的行为。
<br/>
<br/>

#### 依据
ISO/IEC 14882:2003 5.2.9(9)-undefined  
ISO/IEC 14882:2011 5.2.9(12)-undefined  
<br/>

#### 规则
[ID_downCast](https://github.com/Qihoo360/safe-rules/blob/main/c-cpp-rules.md#ID_downCast)  
<br/>

<br/>
<br/>

### <span id="_35">35. 函数指针被转为不兼容的类型并执行</span>
<br/>

示例：
```
void foo();

int bar() {
    return ((int(*)())(&foo))();  // Undefined behavior
}
```
<br/>
<br/>

#### 依据
ISO/IEC 14882:2003 5.2.10(6)-undefined  
ISO/IEC 14882:2011 5.2.10(6)-undefined  
<br/>

#### 规则
[ID_functionPointerCast](https://github.com/Qihoo360/safe-rules/blob/main/c-cpp-rules.md#ID_functionPointerCast)  
<br/>

<br/>
<br/>

### <span id="_36">36. 类型转换时去掉对象 const 属性并修改对象</span>
<br/>

示例：
```
const int ci = 0;
const_cast<int&>(ci) = 1;  // Undefined behavior
```
<br/>
<br/>

#### 依据
ISO/IEC 14882:2003 5.2.11(7)-undefined  
ISO/IEC 14882:2011 5.2.11(7)-undefined  
<br/>

#### 规则
[ID_qualifierCastedAway](https://github.com/Qihoo360/safe-rules/blob/main/c-cpp-rules.md#ID_qualifierCastedAway)  
<br/>

<br/>
<br/>

### <span id="_37">37. 对不完整类型的对象取地址，但该对象的完整类型重载了 operator &</span>
<br/>

示例：
```
struct T;             // Incomplete type

T* foo(T& obj) {
    return &obj;      // Undefined behaviour
}

struct T {
    T* operator &();  // Overload
};
```
<br/>
<br/>

#### 依据
ISO/IEC 14882:2003 5.3.1(4)-undefined  
ISO/IEC 14882:2011 5.3.1(5)-undefined  
<br/>

#### 规则
[ID_overloadAddressOperator](https://github.com/Qihoo360/safe-rules/blob/main/c-cpp-rules.md#ID_overloadAddressOperator)  
<br/>

<br/>
<br/>

### <span id="_38">38. new 运算符第一个数组维度的参数为负数</span>
<br/>

示例：
```
int* p = new int[-1];  // Undefined in C++03
```
在 c\+\+11 中去掉了这项未定义行为的声明。
<br/>
<br/>

#### 依据
ISO/IEC 14882:2003 5.3.4(6)-undefined  
<br/>

<br/>
<br/>

### <span id="_39">39. 用 delete 释放数组漏写中括号，用 delete 释放对象多写中括号，用 delete 释放非 new 表达式的结果</span>
<br/>

示例：
```
string object;
delete &object;       // Undefined behavior
delete "string";      // Undefined behavior
delete new int[n];    // Undefined behavior
delete[] new int(n);  // Undefined behavior
```
<br/>
<br/>

#### 依据
ISO/IEC 14882:2003 5.3.5(2)-undefined  
ISO/IEC 14882:2011 5.3.5(2)-undefined  
<br/>

#### 规则
[ID_excessiveDelete](https://github.com/Qihoo360/safe-rules/blob/main/c-cpp-rules.md#ID_excessiveDelete)  
[ID_insufficientDelete](https://github.com/Qihoo360/safe-rules/blob/main/c-cpp-rules.md#ID_insufficientDelete)  
[ID_incompatibleDealloc](https://github.com/Qihoo360/safe-rules/blob/main/c-cpp-rules.md#ID_incompatibleDealloc)  
[ID_illDealloc](https://github.com/Qihoo360/safe-rules/blob/main/c-cpp-rules.md#ID_illDealloc)  
<br/>

<br/>
<br/>

### <span id="_40">40. 被 delete 释放的对象或数组类型不符合要求</span>
<br/>

用 delete 释放对象时，对象的静态类型应与动态类型兼容，如果静态类型是动态类型的基类，静态类型应提供虚析构函数，否则导致未定义的行为；用 delete\[\] 释放数组时，对象的静态类型应与动态类型一致，否则导致未定义的行为。  
  
示例：
```
struct B { ~B(); };
struct D: B { ~D(); };

B* pDObj = new D;
B* pDArr = new D[123];

delete pDObj;      // Undefined behavior
delete[] pDArr;    // Undefined behavior
```
例中基类 B 缺少虚析构函数，pDArr 的类型应为派生类指针。
<br/>
<br/>

#### 依据
ISO/IEC 14882:2003 5.3.5(3)-undefined  
ISO/IEC 14882:2011 5.3.5(3)-undefined  
<br/>

#### 规则
[ID_missingVirtualDestructor](https://github.com/Qihoo360/safe-rules/blob/main/c-cpp-rules.md#ID_missingVirtualDestructor)  
<br/>

<br/>
<br/>

### <span id="_41">41. 用 delete 释放不完整类型的对象，但在对象完整类型声明中有 non\-trivial 析构函数</span>
<br/>

示例：
```
struct T;

void foo(T* p) {
    delete p;       // Undefined behavior
}

struct T {
   ~T();            // Non-trivial destructor
};
```
例中 delete 作用于不完整类型的指针 p，会导致未定义的行为。
<br/>
<br/>

#### 依据
ISO/IEC 14882:2003 5.3.5(5)-undefined  
ISO/IEC 14882:2011 5.3.5(5)-undefined  
<br/>

#### 规则
[ID_deleteIncompleteType](https://github.com/Qihoo360/safe-rules/blob/main/c-cpp-rules.md#ID_deleteIncompleteType)  
<br/>

<br/>
<br/>

### <span id="_42">42. 对象解引用成员指针时，对象的动态类型不包含成员指针引用的成员</span>
<br/>

示例：
```
struct A { int a; };
struct B { int a, b; };

int foo(A* pa) {
    B* pb = (B*)pa;
    int B::* m = &B::b;
    return pb->*m;         // Undefined behavior
}
```
<br/>
<br/>

#### 依据
ISO/IEC 14882:2003 5.5(4)-undefined  
ISO/IEC 14882:2011 5.5(4)-undefined  
<br/>

<br/>
<br/>

### <span id="_43">43. 对象解引用成员指针时，成员指针为空指针</span>
<br/>

示例：
```
struct T { .... };

int foo(T& obj) {
    int T::* mp = nullptr;
    return obj.*mp;          // Undefined behavior
}
```
<br/>
<br/>

#### 依据
ISO/IEC 14882:2003 5.5(6)-undefined  
ISO/IEC 14882:2011 5.5(6)-undefined  
<br/>

<br/>
<br/>

### <span id="_44">44. / 或 % 运算符的第二个操作数为 0</span>
<br/>

<br/>
<br/>

#### 依据
ISO/IEC 14882:2003 5.6(4)-undefined  
ISO/IEC 14882:2011 5.6(4)-undefined  
<br/>

#### 规则
[ID_divideByZero](https://github.com/Qihoo360/safe-rules/blob/main/c-cpp-rules.md#ID_divideByZero)  
<br/>

<br/>
<br/>

### <span id="_45">45. 指针加减整数，结果超出了指针所在数组的地址范围</span>
<br/>

示例：
```
int foo() {
    int a[10];
    ....
    int* p = a + 9;   // OK
    return p[1];      // Undefined behavior
}
```
例中 p\[1\] 相当于 \*(p \+ 1)，即 a\[10\]，超过了数组最后一个元素的地址，导致未定义的行为。
<br/>
<br/>

#### 依据
ISO/IEC 14882:2003 5.7(5)-undefined  
ISO/IEC 14882:2011 5.7(5)-undefined  
<br/>

#### 规则
[ID_bufferOverflow](https://github.com/Qihoo360/safe-rules/blob/main/c-cpp-rules.md#ID_bufferOverflow)  
[ID_arrayIndexOverflow](https://github.com/Qihoo360/safe-rules/blob/main/c-cpp-rules.md#ID_arrayIndexOverflow)  
<br/>

<br/>
<br/>

### <span id="_46">46. 不在同一数组中的指针相减</span>
<br/>

示例：
```
int a[10];
int b[10];
ptrdiff_t d = a - b;  // Undefined behavior
```
<br/>
<br/>

#### 依据
ISO/IEC 14882:2003 5.7(5)-undefined  
ISO/IEC 14882:2011 5.7(5)-undefined  
<br/>

#### 规则
[ID_illPtrDiff](https://github.com/Qihoo360/safe-rules/blob/main/c-cpp-rules.md#ID_illPtrDiff)  
<br/>

<br/>
<br/>

### <span id="_47">47. 移位运算符右操作数为负数或超过相关类型比特位的数量</span>
<br/>

示例：
```
size_t a = 1 << -1;    // Undefined behavior
size_t b = 1 << 100;   // Undefined behavior
```
<br/>
<br/>

#### 依据
ISO/IEC 14882:2003 5.8(5)-undefined  
ISO/IEC 14882:2011 5.8(5)-undefined  
<br/>

#### 规则
[ID_illShiftCount](https://github.com/Qihoo360/safe-rules/blob/main/c-cpp-rules.md#ID_illShiftCount)  
<br/>

<br/>
<br/>

### <span id="_48">48. 对负数进行左移运算</span>
<br/>

示例：
```
size_t a = -1 << 10;    // Undefined behavior
```
<br/>
<br/>

#### 依据
ISO/IEC 14882:2011 5.8(2)-undefined  
<br/>

#### 规则
[ID_bitwiseOperOnSigned](https://github.com/Qihoo360/safe-rules/blob/main/c-cpp-rules.md#ID_bitwiseOperOnSigned)  
<br/>

<br/>
<br/>

### <span id="_49">49. 将对象赋值给具有重叠区域的对象</span>
<br/>

示例：
```
struct T {
    int a[10];
};

struct W {
    int i;
    T t;
};

union U {
    T t;
    W w;
};

void foo(U& u) {
    u.w.t = u.t;    // Undefined behavior
}
```
例中 u.w.t 和 u.t 具有部分重叠区域，这种赋值会导致未定义的行为
<br/>
<br/>

#### 依据
ISO/IEC 14882:2003 5.17(8)-undefined  
ISO/IEC 14882:2011 5.17(8)-undefined  
<br/>

<br/>
<br/>

### <span id="_50">50. 函数没有通过 return 语句返回明确的值，但函数返回值被使用</span>
<br/>

示例：
```
bool foo(int i) {
    if (i > 10) {
        return true;
    }
}   // Undefined behavior if i <= 10
```
<br/>
<br/>

#### 依据
ISO/IEC 14882:2003 6.6.3(2)-undefined  
ISO/IEC 14882:2011 6.6.3(2)-undefined  
<br/>

#### 规则
[ID_notAllBranchReturn](https://github.com/Qihoo360/safe-rules/blob/main/c-cpp-rules.md#ID_notAllBranchReturn)  
<br/>

<br/>
<br/>

### <span id="_51">51. 递归地定义和初始化静态对象</span>
<br/>

示例：
```
int foo(int i) {
    static int s = foo(2 * i);   // Undefined behavior, recursive call
    return i + 1;
}
```
例中静态局部变量 s 的初始化需要递归调用所在函数，导致未定义的行为。
<br/>
<br/>

#### 依据
ISO/IEC 14882:2003 6.7(4)-undefined  
ISO/IEC 14882:2011 6.7(4)-undefined  
<br/>

<br/>
<br/>

### <span id="_52">52. 修改非 mutable 常量对象</span>
<br/>

示例：
```
struct T {
    mutable int i;
    int j;
};

const T obj;
obj.i++;            // Well-defined
obj.j++;            // Ill-formed

T* p = (T*)&obj;
p->i = 0;           // Well-defined
p->j = 1;           // Undefined
```
<br/>
<br/>

#### 依据
ISO/IEC 14882:2003 7.1.5.1(4)-undefined  
ISO/IEC 14882:2011 7.1.6.1(4)-undefined  
<br/>

#### 规则
[ID_qualifierCastedAway](https://github.com/Qihoo360/safe-rules/blob/main/c-cpp-rules.md#ID_qualifierCastedAway)  
<br/>

<br/>
<br/>

### <span id="_53">53. 使用非 volatile glvalue 引用 volatile 对象</span>
<br/>

示例：
```
void bar(int*);

volatile int* pv = foo();
bar((int*)pv);   // Undefined behavior
```
<br/>
<br/>

#### 依据
ISO/IEC 14882:2003 7.1.5.1(7)-undefined  
ISO/IEC 14882:2011 7.1.6.1(6)-undefined  
<br/>

<br/>
<br/>

### <span id="_54">54. 具有 noreturn 属性的函数返回至调用方</span>
<br/>

示例：
```
[[noreturn]] void foo() {
    throw Exception();          // OK
}

[[noreturn]] int bar() {
    return 0;                   // Undefined behavior
}

[[noreturn]] void baz(int i) {  // Undefined behavior if ‘i’ != 0
    if (i == 0)
        throw Exception();
}
```
<br/>
<br/>

#### 依据
ISO/IEC 14882:2011 7.6.3(2)-undefined  
<br/>

#### 规则
[ID_unsuitableReturn](https://github.com/Qihoo360/safe-rules/blob/main/c-cpp-rules.md#ID_unsuitableReturn)  
<br/>

<br/>
<br/>

### <span id="_55">55. 空指针解引用</span>
<br/>

示例：
```
*((int*)nullptr) = 'UB';  // Undefined behavior
```
<br/>
<br/>

#### 依据
ISO/IEC 14882:2003 8.3.2(4)-undefined  
ISO/IEC 14882:2011 8.3.2(5)-undefined  
<br/>

#### 规则
[ID_nullDerefInScp](https://github.com/Qihoo360/safe-rules/blob/main/c-cpp-rules.md#ID_nullDerefInScp)  
[ID_nullDerefInExp](https://github.com/Qihoo360/safe-rules/blob/main/c-cpp-rules.md#ID_nullDerefInExp)  
<br/>

<br/>
<br/>

### <span id="_56">56. 对象的实际类型与当前静态类型不相关，并调用其非静态成员函数</span>
<br/>

示例：
```
struct A { int foo(); };
struct B { .... };

int bar(void* p) {
    return ((A*)p)->foo();
}

int main() {
    B b;
    return bar(&b);  // Undefined behavior
}
```
<br/>
<br/>

#### 依据
ISO/IEC 14882:2003 9.3.1(1)-undefined  
ISO/IEC 14882:2011 9.3.1(2)-undefined  
<br/>

#### 规则
[ID_castNoInheritance](https://github.com/Qihoo360/safe-rules/blob/main/c-cpp-rules.md#ID_castNoInheritance)  
[ID_forbidMemberVoidPtr](https://github.com/Qihoo360/safe-rules/blob/main/c-cpp-rules.md#ID_forbidMemberVoidPtr)  
[ID_forbidFunctionVoidPtr](https://github.com/Qihoo360/safe-rules/blob/main/c-cpp-rules.md#ID_forbidFunctionVoidPtr)  
<br/>

<br/>
<br/>

### <span id="_57">57. 在构造函数或析构函数中调用纯虚函数</span>
<br/>

示例：
```
struct A {
    virtual ~A() {
        release();   // Undefined behavior
    }

    virtual void release() = 0;
};
```
<br/>
<br/>

#### 依据
ISO/IEC 14882:2003 10.4(6)-undefined  
ISO/IEC 14882:2011 10.4(6)-undefined  
<br/>

#### 规则
[ID_virtualCallInConstructor](https://github.com/Qihoo360/safe-rules/blob/main/c-cpp-rules.md#ID_virtualCallInConstructor)  
[ID_virtualCallInDestuctor](https://github.com/Qihoo360/safe-rules/blob/main/c-cpp-rules.md#ID_virtualCallInDestuctor)  
<br/>

<br/>
<br/>

### <span id="_58">58. 对象的实际类型与当前静态类型不相关，并调用其析构函数</span>
<br/>

<br/>
<br/>

#### 依据
ISO/IEC 14882:2003 12.4(12)-undefined  
ISO/IEC 14882:2011 12.4(13)-undefined  
<br/>

#### 规则
[ID_castNoInheritance](https://github.com/Qihoo360/safe-rules/blob/main/c-cpp-rules.md#ID_castNoInheritance)  
<br/>

<br/>
<br/>

### <span id="_59">59. 在对象生命周期结束后调用其析构函数</span>
<br/>

示例：
```
struct T { ~T(); };

void foo() {
    T obj;
    obj.~T();  // End of lifetime
}              // Undefined behavior
```
<br/>
<br/>

#### 依据
ISO/IEC 14882:2003 12.4(14)-undefined  
ISO/IEC 14882:2011 12.4(15)-undefined  
<br/>

#### 规则
[ID_explicitDtorCall](https://github.com/Qihoo360/safe-rules/blob/main/c-cpp-rules.md#ID_explicitDtorCall)  
<br/>

<br/>
<br/>

### <span id="_60">60. 基类对象构造完毕之前调用成员函数</span>
<br/>

示例：
```
struct A {
    A(int);
};

struct B: A {
    int i;
    int m();

    B(): A(m()),   // Undefined
         i(m()) {  // Well-defined
    }   
};
```
例中用成员函数 m 的返回值作为基类构造函数的参数会导致未定义的行为，因为基类尚未开始构造，而用成员函数初始化成员变量则没有问题。
<br/>
<br/>

#### 依据
ISO/IEC 14882:2003 12.6.2(8)-undefined  
ISO/IEC 14882:2011 12.6.2(13)-undefined  
<br/>

#### 规则
[ID_illMemberCall](https://github.com/Qihoo360/safe-rules/blob/main/c-cpp-rules.md#ID_illMemberCall)  
<br/>

<br/>
<br/>

### <span id="_61">61. 对成员或基类对象的不合理引用</span>
<br/>

对具有 non\-trivial 构造函数的对象，在构造函数执行之前引用其非静态成员或基类对象，或者在析构函数执行之后引用其非静态成员或基类对象，会导致未定义的行为。  
  
示例：
```
struct A {              // Trivial
    int a;
};

struct B: A {           // Non-trivial
    B();
    int b;
};

extern B obj;           // The object is defined later
B* p = &obj;            // OK
int* p1 = &obj.a;       // Undefined, refers to base class member
int* p2 = &obj.b;       // Undefined, refers to member

A* pa = &obj;           // Undefined, upcast to a base class type

extern A trvlObj;       // The object is defined later
int* p3 = &trvlObj.a;   // OK, A is a trivial class

B obj;                  // Define the object
A trvlObj;              // Define the object
```
<br/>
<br/>

#### 依据
ISO/IEC 14882:2003 12.7(1)-undefined  
ISO/IEC 14882:2011 12.7(1)-undefined  
<br/>

<br/>
<br/>

### <span id="_62">62. 将对象指针转为其基类对象的指针时，基类对象尚未开始构造或已结束析构</span>
<br/>

示例：
```
struct A {};

struct B: A {
    B(A*);
};

struct C: B {
    C(): B(this) {     // Undefined behavior
    }
};
```
例中将 C 的 this 指针作为基类 B 构造函数的参数，相当于将 C\* 转为 A\*，而这时基类 B 和 A 均未开始构造，会导致未定义的行为。  
  
又如（各项声明接上例）：
```
struct X {
    X(A*);
};

struct D: B, X {
    D(): B(), X(this) {  // OK
    }
};
```
将 D 的 this 指针作为基类 X 构造函数的参数，相当于将 D\* 转为 A\*，这时 B 的构造函数已经执行完毕，所以这种情况没有问题。
<br/>
<br/>

#### 依据
ISO/IEC 14882:2003 12.7(2)-undefined  
ISO/IEC 14882:2011 12.7(3)-undefined  
<br/>

<br/>
<br/>

### <span id="_63">63. 通过 . 或 \-> 调用正在构造或析构的对象的虚函数，而且该对象的类型不符合要求</span>
<br/>

如果该对象与当前构造或析构函数不属于同一个类或基类，会导致未定义的行为。
<br/>
<br/>

#### 依据
ISO/IEC 14882:2003 12.7(3)-undefined  
ISO/IEC 14882:2011 12.7(4)-undefined  
<br/>

<br/>
<br/>

### <span id="_64">64. typeid 作用于正在构造或析构的对象，而且该对象的类型不符合要求</span>
<br/>

如果该对象与当前构造或析构函数不属于同一个类或基类，会导致未定义的行为。
<br/>
<br/>

#### 依据
ISO/IEC 14882:2003 12.7(4)-undefined  
ISO/IEC 14882:2011 12.7(5)-undefined  
<br/>

<br/>
<br/>

### <span id="_65">65. 需要无限递归的模版实例化</span>
<br/>

示例：
```
template <class T>
class A
{
    A<T>* p;   // OK
    A<T*> a;   // Implicit generation of X<T> requires
               // the implicit instantiation of X<T*> which requires
               // the implicit instantiation of X<T**> which requires ....
};
```
<br/>
<br/>

#### 依据
ISO/IEC 14882:2003 14.7.1(14)-undefined  
ISO/IEC 14882:2011 14.7.1(15)-undefined  
<br/>

<br/>
<br/>

### <span id="_66">66. 构造或析构函数在 function\-try\-block 的 handler 中访问非静态成员</span>
<br/>

<br/>
<br/>

#### 依据
ISO/IEC 14882:2003 15.3(10)-undefined  
ISO/IEC 14882:2011 15.2(10)-undefined  
<br/>

#### 规则
[ID_illMemberAccess](https://github.com/Qihoo360/safe-rules/blob/main/c-cpp-rules.md#ID_illMemberAccess)  
<br/>

<br/>
<br/>

### <span id="_67">67. 有返回值的函数在 function\-try\-block 的 handler 中没有显式返回</span>
<br/>

<br/>
<br/>

#### 依据
ISO/IEC 14882:2003 15.3(16)-undefined  
ISO/IEC 14882:2011 15.3(15)-undefined  
<br/>

#### 规则
[ID_notAllBranchReturn](https://github.com/Qihoo360/safe-rules/blob/main/c-cpp-rules.md#ID_notAllBranchReturn)  
<br/>

<br/>
<br/>

### <span id="_68">68. 在 \#if、 \#elif 的条件中，由宏展开产生了 defined 表达式，或 defined 关键字格式不正确</span>
<br/>

<br/>
<br/>

#### 依据
ISO/IEC 14882:2003 16.1(4)-undefined  
ISO/IEC 14882:2011 16.1(4)-undefined  
<br/>

#### 规则
[ID_macro_defineReserved](https://github.com/Qihoo360/safe-rules/blob/main/c-cpp-rules.md#ID_macro_defineReserved)  
[ID_illFormedDirective](https://github.com/Qihoo360/safe-rules/blob/main/c-cpp-rules.md#ID_illFormedDirective)  
<br/>

<br/>
<br/>

### <span id="_69">69. \#include 指令经宏展开后不满足标准格式</span>
<br/>

<br/>
<br/>

#### 依据
ISO/IEC 14882:2003 16.2(4)-undefined  
ISO/IEC 14882:2011 16.2(4)-undefined  
<br/>

#### 规则
[ID_illFormedDirective](https://github.com/Qihoo360/safe-rules/blob/main/c-cpp-rules.md#ID_illFormedDirective)  
<br/>

<br/>
<br/>

### <span id="_70">70. 宏的实参列表中出现预处理指令</span>
<br/>

示例：
```
#define PRINT(s) printf(#s)

PRINT(
#ifdef MAC      // Undefined behavior 
    rabbit
#else
    hamster
#endif
);
```
<br/>
<br/>

#### 依据
ISO/IEC 14882:2003 16.3(10)-undefined  
ISO/IEC 14882:2011 16.3(11)-undefined  
<br/>

#### 规则
[ID_directiveInMacroArgument](https://github.com/Qihoo360/safe-rules/blob/main/c-cpp-rules.md#ID_directiveInMacroArgument)  
[ID_macro_function](https://github.com/Qihoo360/safe-rules/blob/main/c-cpp-rules.md#ID_macro_function)  
<br/>

<br/>
<br/>

### <span id="_71">71. 预处理运算符 \# 的结果不是有效的字符串</span>
<br/>

<br/>
<br/>

#### 依据
ISO/IEC 14882:2003 16.3.2(2)-undefined  
ISO/IEC 14882:2011 16.3.2(2)-undefined  
<br/>

#### 规则
[ID_macro_complexConcat](https://github.com/Qihoo360/safe-rules/blob/main/c-cpp-rules.md#ID_macro_complexConcat)  
<br/>

<br/>
<br/>

### <span id="_72">72. 预处理运算符 \#\# 的结果不是有效的符号</span>
<br/>

示例：
```
#define M(a, b)  a ## b

int M(x, 0) = 0;   // OK
int M(0, x) = 0;   // Undefined behavior
```
<br/>
<br/>

#### 依据
ISO/IEC 14882:2003 16.3.3(3)-undefined  
ISO/IEC 14882:2011 16.3.3(3)-undefined  
<br/>

<br/>
<br/>

### <span id="_73">73. \#line 指定的行号为 0 或大于规定值</span>
<br/>

C\+\+03 规定指定的行号不可大于 32767，C\+\+11 规定不可大于 2147483647  
  
示例：
```
#line 0             // Undefined behavior
#line 4294967295    // Undefined behavior
```
<br/>
<br/>

#### 依据
ISO/IEC 14882:2003 16.4(3)-undefined  
ISO/IEC 14882:2011 16.4(3)-undefined  
<br/>

#### 规则
[ID_illFormedDirective](https://github.com/Qihoo360/safe-rules/blob/main/c-cpp-rules.md#ID_illFormedDirective)  
<br/>

<br/>
<br/>

### <span id="_74">74. \#line 指令不符合标准格式</span>
<br/>

\#line 后只应为整数常量或整数常量和文件名称字符串，其他形式均会导致未定义的行为。  
  
示例：
```
#line "filename"        // Undefined behavior
#line NUM + 123         // Undefined behavior
```
<br/>
<br/>

#### 依据
ISO/IEC 14882:2003 16.4(5)-undefined  
ISO/IEC 14882:2011 16.4(5)-undefined  
<br/>

#### 规则
[ID_illFormedDirective](https://github.com/Qihoo360/safe-rules/blob/main/c-cpp-rules.md#ID_illFormedDirective)  
<br/>

<br/>
<br/>

### <span id="_75">75. 用 \#define 定义或用 \#undef 取消定义具有保留意义的名称</span>
<br/>

如 \_\_LINE\_\_、\_\_FILE\_\_、\_\_DATE\_\_、\_\_TIME\_\_、\_\_STDC\_\_、\_\_cplusplus、defined 等名称被定义或取消定义。  
  
示例：
```
#undef __cplusplus   // Undefined behavior
#define __STDC__ 0   // Undefined behavior
```
<br/>
<br/>

#### 依据
ISO/IEC 14882:2003 16.8(3)-undefined  
ISO/IEC 14882:2011 16.8(4)-undefined  
<br/>

#### 规则
[ID_macro_defineReserved](https://github.com/Qihoo360/safe-rules/blob/main/c-cpp-rules.md#ID_macro_defineReserved)  
[ID_macro_undefReserved](https://github.com/Qihoo360/safe-rules/blob/main/c-cpp-rules.md#ID_macro_undefReserved)  
<br/>

<br/>
<br/>

### <span id="_76">76. 供语言机制调用的函数不符合条件</span>
<br/>

如 replacement functions 或 handler functions 不符合条件（required behavior），会导致未定义的行为。  
  
示例：
```
struct T {
    void* operator new(size_t) {  // A replacement function
        return nullptr;           // Undefined behavior
    }
};
```
标准要求 operator new 返回非空地址，分配失败则抛出 bad\_alloc 异常（见 ISO/IEC 14882:2011 18.6.1.1），不满足要求会导致未定义的行为。  
  
又如：
```
void my_handler() {  // A handler function
     return;         // Do nothing
}

int main() {
    set_terminate(my_handler);   // Undefined behavior
    ....
}
```
标准要求 terminate handler 结束进程的执行，不可返回（见 ISO/IEC 14882:2011 18.8.3.1），不满足要求会导致未定义的行为。
<br/>
<br/>

#### 依据
ISO/IEC 14882:2003 17.1.15-undefined  
ISO/IEC 14882:2011 17.3.21-undefined  
<br/>

<br/>
<br/>

### <span id="_77">77. 程序实现了应由标准库提供的功能</span>
<br/>

<br/>
<br/>

#### 依据
ISO/IEC 14882:2003 17.1.17-undefined  
ISO/IEC 14882:2011 17.3.22-undefined  
<br/>

<br/>
<br/>

### <span id="_78">78. 未经许可，向 std 命名空间添加声明或定义</span>
<br/>

<br/>
<br/>

#### 依据
ISO/IEC 14882:2011 17.6.4.2.1(1)-undefined  
<br/>

#### 规则
[ID_stdNamespaceModified](https://github.com/Qihoo360/safe-rules/blob/main/c-cpp-rules.md#ID_stdNamespaceModified)  
<br/>

<br/>
<br/>

### <span id="_79">79. 对标准库，特化模板类成员函数，特化模板类成员模板函数，特化、偏特化成员类模版</span>
<br/>

<br/>
<br/>

#### 依据
ISO/IEC 14882:2011 17.6.4.2.1(2)-undefined  
<br/>

<br/>
<br/>

### <span id="_80">80. 未经许可，向 posix 命名空间添加声明或定义</span>
<br/>

<br/>
<br/>

#### 依据
ISO/IEC 14882:2011 17.6.4.2.2(1)-undefined  
<br/>

<br/>
<br/>

### <span id="_81">81. 声明或定义标准库保留名称</span>
<br/>

示例：
```
#define override           // Undefined behavior
#define new new(nothrow)   // Undefined behavior
```
<br/>
<br/>

#### 依据
ISO/IEC 14882:2003 17.4.3.1(1)-undefined  
ISO/IEC 14882:2011 17.6.4.3(2)-undefined  
<br/>

#### 规则
[ID_reservedName](https://github.com/Qihoo360/safe-rules/blob/main/c-cpp-rules.md#ID_reservedName)  
[ID_macro_defineReserved](https://github.com/Qihoo360/safe-rules/blob/main/c-cpp-rules.md#ID_macro_defineReserved)  
<br/>

<br/>
<br/>

### <span id="_82">82. 编译器未提供标准头文件，但编译时引入了非标准同名头文件</span>
<br/>

<br/>
<br/>

#### 依据
ISO/IEC 14882:2003 17.4.3.2(1)-undefined  
ISO/IEC 14882:2011 17.6.4.4(1)-undefined  
<br/>

<br/>
<br/>

### <span id="_83">83. 为标准库函数提供不符合要求的参数</span>
<br/>

<br/>
<br/>

#### 依据
ISO/IEC 14882:2003 17.4.3.7(1)-undefined  
ISO/IEC 14882:2011 17.6.4.9(1)-undefined  
<br/>

<br/>
<br/>

### <span id="_84">84. 多线程调用标准库函数造成 data race</span>
<br/>

<br/>
<br/>

#### 依据
ISO/IEC 14882:2011 17.6.4.10(1)-undefined  
<br/>

<br/>
<br/>

### <span id="_85">85. 违反标准库函数要求的前置条件，除非标准库函数声明了这种情况会抛出异常</span>
<br/>

示例：
```
string s{"...."};

s[string::npos];         // Undefined behavior
s.at(string::npos);      // Well-defined, throw out_of_range
```
<br/>
<br/>

#### 依据
ISO/IEC 14882:2003 17.4.3.8(1)-undefined  
ISO/IEC 14882:2011 17.6.4.11(1)-undefined  
<br/>

<br/>
<br/>

### <span id="_86">86. offsetof 用于非 standard layout 类型，或用于计算静态成员以及成员函数的偏移量</span>
<br/>

示例：
```
struct T {
    int* m;

    T();
    virtual ~T();
};

size_t s = offsetof(T, m);  // Undefined behavior
```
<br/>
<br/>

#### 依据
ISO/IEC 14882:2003 18.1(5)-undefined  
ISO/IEC 14882:2011 18.2(4)-undefined  
<br/>

#### 规则
[ID_deprecatedOffsetof](https://github.com/Qihoo360/safe-rules/blob/main/c-cpp-rules.md#ID_deprecatedOffsetof)  
<br/>

<br/>
<br/>

### <span id="_87">87. 可变参数列表省略号左侧的形式参数为函数、数组或引用</span>
<br/>

示例：
```
void foo(void fun(), ...);     // Undefined behavior
void foo(string& str, ...);    // Undefined behavior
void foo(int arr[10], ...);    // Undefined behavior
```
<br/>
<br/>

#### 依据
ISO/IEC 14882:2003 18.7(3)-undefined  
ISO/IEC 14882:2011 18.10(3)-undefined  
<br/>

#### 规则
[ID_forbidVariadicFunction](https://github.com/Qihoo360/safe-rules/blob/main/c-cpp-rules.md#ID_forbidVariadicFunction)  
<br/>

<br/>
<br/>

### <span id="_88">88. longjmp 跳转使应被执行的析构函数未被执行</span>
<br/>

示例：
```
void foo() {
    string obj;
    ....
    longjmp(buf, 1);   // Undefined behavior
}
```
例中局部对象 obj 的析构函数不会被执行，由此引发的问题会导致未定义的行为。
<br/>
<br/>

#### 依据
ISO/IEC 14882:2003 18.7(4)-undefined  
ISO/IEC 14882:2011 18.10(4)-undefined  
<br/>

#### 规则
[ID_forbidLongjmp](https://github.com/Qihoo360/safe-rules/blob/main/c-cpp-rules.md#ID_forbidLongjmp)  
<br/>

<br/>
<br/>

<br/>
<br/>