# C++ 流控制库 `<iomanip>`  与 C 风格 `printf` 的对比

示例代码在 [test_base.cpp](https://github.com/OneForward/TACpp/blob/master/tutorials/codes/CppFormat/test_base.cpp), [test_fill_width.cpp](https://github.com/OneForward/TACpp/blob/master/tutorials/codes/CppFormat/test_fill_width.cpp)

- [C++ 流控制库 `<iomanip>`  与 C 风格 `printf` 的对比](#c-流控制库-iomanip--与-c-风格-printf-的对比)
  - [整数 $n$ 进制](#整数-n-进制)
  - [宽度、对齐、填充](#宽度对齐填充)
    - [使用 `printf`](#使用-printf)
    - [使用流控制库](#使用流控制库)
    - [浮点数定宽、对齐](#浮点数定宽对齐)
    - [字符串定宽、对齐](#字符串定宽对齐)
    - [浮点数精度设置](#浮点数精度设置)
  - [关于两种格式控制的评论](#关于两种格式控制的评论)
  - [`printf` 详解](#printf-详解)
    - [函数原型](#函数原型)
    - [格式化字符串的参数](#格式化字符串的参数)
    - [例子](#例子)


## 整数 $n$ 进制

* `printf` 使用格式标识符 `%x %o %d` 来表明进制。 
  - `%x` ：代表将数字格式化为16进制形式
  - `%o` ：代表将数字格式化为8进制形式
  - `%d` ：代表将数字格式化为10进制形式

* 流控制库 `<iomanip>` 可以使用 `hex oct dec` 来设置进制。或者使用 `std::setbase(int base)`。注意，如果传入的不是 8、10、16，则输出**十进制**。
  - `hex` , `setbase(16)`
  - `oct` , `setbase(8)`
  - `dec` , `setbase(10)`


```cpp
int num = 15;

printf( "十六进制：%x\n", num);
printf( "八进制：%o\n", num);
printf( "十进制：%d\n\n", num);


cout << "十六进制：" << hex << num << endl
     << "八进制：" << oct << num << endl
     << "十进制：" << dec << num << endl << endl;

cout << "十六进制：" << setbase(16) << num << endl
     << "八进制：" << setbase(8) << num << endl
     << "十进制：" << setbase(10) << num << endl << endl;
```

输出都是
```
十六进制：f
八进制：17
十进制：15
```


## 宽度、对齐、填充

### 使用 `printf`
`printf` 中格式化字符串的形式如下，

`%[flags][width][.precision][length]specifier`

解释几个常见的用法，更详细的规则见 [`printf` 详解](#printf-详解) 

- 中括号 `[]` 内的 设置都是可选的。 
- `specifier`: 类型说明符，必须设置。常见的
  *  `'d'`、`'i'` 代表整型
  * `'g'`、`'f'` 代表浮点数
  * `'c'` 代表字符 char
  * `'s'` 代表字符串
- `flags`: 字符,（用在定宽情形下）
  *  默认右对齐。
  *  `'-'` 代表左对齐，
  *  `'0'` 代表用 0 填充。
- `width`: 数字，设置 字符串宽度。
- `.precision`: `'.'` 加数字，表示小数点后保留几位小数。

注意，`printf` 的限制很大，例如 “填补字符” 只有 `'0'` 和 空格 `' '` ，使用 `'0'` 时还只能使用右对齐。因为左对齐的 `'-'` 会排斥填补的 `'0'` 。

例子

* 例如 `%-10g` 代表将浮点数字格式化为宽度为10，左对齐的字符串。

* 例如 `%010.3f` 代表将浮点数字格式化为保留3位小数、宽度为10，右对齐，前缀填充了0的字符串。
* 例如 `%-010g` 由于左对齐的 `'-'` 会排斥填补的 `'0'`，因此这里的效果等价于`%-10g`。


### 使用流控制库

相比之下，流控制库 `<iomanip>`  使用 

- `setw(int n)` : 设置宽度
- `setfill(char ch)`: 填充字符 
- `left` : 左对齐
- `right` : 右对齐
- `precision(int n)` : 设置有效数字个数
- `fixed` : 与 `precision` 一起用，设置小数点后有效数字的个数

其填补更加灵活，可以自定义填补字符，而且 “左右对齐” 和 “填补字符” 可以自由组合。

注意，流控制库 `<iomanip>`  在设置最小**宽度**时使用的流控制符 `std::setw(int n)` **仅对下一个输出有效**，因此需要不断使用。

### 浮点数定宽、对齐

```cpp
double num = 4.0; 
printf( "宽度为10，右对齐：[%10g]\n"
        "宽度为10，左对齐：[%-10g]\n"
        "宽度为10，右对齐，空白补0：[%010.g]\n"
        "\n", num, num, num);

cout << "宽度为10，右对齐：[" 
     << setw(10) << num << "]" << endl
     
     << "宽度为10，左对齐：[" << left
     << setw(10) << num << "]" << endl
     << resetiosflags(ios::left)
     
     << "宽度为10，右对齐，空白补0：[" << right
     << setfill('0') << setw(10) << num << "]" << endl
     << resetiosflags(ios::right)
                                 
     << "宽度为10，左对齐，空白补*：[" << left
     << setfill('*') << setw(10) << num << "]" << endl
     << resetiosflags(ios::left);
```

输出
```
宽度为10，右对齐：[         4]
宽度为10，左对齐：[4         ]
宽度为10，右对齐，空白补0：[0000000004]      

宽度为10，右对齐：[         4]
宽度为10，左对齐：[4         ]
宽度为10，右对齐，空白补0：[0000000004]      
宽度为10，左对齐，空白补*：[4*********]
```

### 字符串定宽、对齐

```cpp
auto s = "Hello";

printf("\n");
printf("宽度为10，右对齐: [%10s] \n", "Hello");
printf("宽度为10，左对齐: [%-10s] \n", "Hello");   
printf("\n");


cout << "宽度为10，右对齐：[" << setfill(' ')
        << setw(10) << s << "]" << endl
        
        << "宽度为10，左对齐：[" << left
        << setw(10) << s << "]" << endl
        << resetiosflags(ios::left);
```

输出
```
宽度为10，右对齐: [     Hello] 
宽度为10，左对齐: [Hello     ]

宽度为10，右对齐：[     Hello]
宽度为10，左对齐：[Hello     ]
```

### 浮点数精度设置

```cpp
double x = 20.0 / 3;

printf("\n");
printf("保留3位小数: %.3f\n", x);
printf("保留3位小数，宽度10且左对齐: [%-10.3f] [%-10.3f]\n", x, 2.);
printf("\n");


cout << "保留3位小数: " 
        << fixed << setprecision(3) << x << endl 
        << "保留3位小数，宽度10且左对齐: [" << left
        << setw(10) << setfill(' ') << x << "] ["
        << setw(10) << setfill('*') << x << "]\n" 
        << resetiosflags(ios::left);
```

输出
```
保留3位小数: 6.667
保留3位小数，宽度10且左对齐: [6.667     ] [2.000     ]

保留3位小数: 6.667
保留3位小数，宽度10且左对齐: [6.667     ] [6.667*****]
```

## 关于两种格式控制的评论

翻译自 [https://stackoverflow.com/questions/2872543/printf-vs-cout-in-c](https://stackoverflow.com/questions/2872543/printf-vs-cout-in-c)

1. 可拓展性。 `std::cout` 易于重载，因此可以承接各种各样的对象。而 `printf` 一般只能承接基本类型。
2. 直观性。 `printf` 相对而言字符串更紧凑，易于理解。而`std::cout` 的流控制语义非常复杂，且有滥用 流运算符 `<<` 的趋势，因此不易理解。
3. 性能。 `printf` 的 IO 性能更好。 `std::cout` 往往需要设置如下命令来提高 IO 性能。

```cpp
std::ios::sync_with_stdio(false);
std::cin.tie(0);
```

4. 类型安全。`printf` 最为人诟病的是类型不安全。不过可以设置编译器提供编译期间的检查，并产生 Warning。

```cpp
#include <stdio.h>

int main(void) {
    printf("String: %s\n", 42);
    return 0;
}

```

编译提醒
```cpp
safety.c:4:28: warning: format specifies type 'char *' but the argument has type 'int' [-Wformat]
    printf("String: %s\n", 42);
                    ~~     ^~
                    %d
1 warning generated.
```


## `printf` 详解

C++ 中 `printf()` 函数位于库文件 `<cstdio>` 或者 `<stdio.h>`，其功能为将格式化的数据打印到标准输出。


其调用格式为

```
printf("<格式化字符串>", <参量表>);
```

### 函数原型

```cpp
int printf ( const char * format, ... );
```

### 格式化字符串的参数

- `format` 这是字符串，包含了要被写入到标准输出 `stdout` 的文本。它可以包含嵌入的 `format` 标签， `format` 标签可被随后的附加参数中指定的值替换，并按需求进行格式化。 `format` 标签属性是
  
    `%[flags][width][.precision][length]specifier`

    具体讲解如下： 

| `specifier` (格式字符) | 意义                                       |
| :------: | :----------------------------------------- |
|  d, i  | 以十进制形式输出带符号整数(正数不输出符号) |
|    o     | 以八进制形式输出无符号整数(不输出前缀0)    |
|   x, X    | 以十六进制形式输出无符号整数(不输出前缀Ox) |
|    u     | 以十进制形式输出无符号整数                 |
|    f     | 以小数形式输出单、双精度实数               |
|   e, E    | 以指数形式输出单、双精度实数               |
|   g, G    | 以%f或%e中较短的输出宽度输出单、双精度实数 |
|    c     | 输出单个字符                               |
|    s     | 输出字符串                                 |
|    p     | 输出指针地址                               |
|    lu    | 32位无符号整数                             |
|   llu    | 64位无符号整数                             |



| `flags` (标识) | 描述                                                         |
| :-----------: | :----------------------------------------------------------- |
|       -       | 在给定的字段宽度内左对齐，默认是右对齐（参见 width 子说明符）。 |
|       +       | 强制在结果之前显示加号或减号（+ 或 -），即正数前面会显示 + 号。默认情况下，只有负数前面会显示一个 - 号。 |
|     空格      | 如果没有写入任何符号，则在该值前面插入一个空格。             |
|       #       | 与 o、x 或 X 说明符一起使用时，非零值前面会分别显示 0、0x 或 0X。 与 e、E 和 f 一起使用时，会强制输出包含一个小数点，即使后边没有数字时也会显示小数点。默认情况下，如果后边没有数字时候，不会显示显示小数点。 与 g 或 G 一起使用时，结果与使用 e 或 E 时相同，但是尾部的零不会被移除。 |
|       0       | 在指定填充 padding 的数字左边放置零（0），而不是空格（参见 width 子说明符）。 |



| `width` (宽度) | 描述                                                         |
| :-----------: | :----------------------------------------------------------- |
|   (number)    | 要输出的字符的最小数目。如果输出的值短于该数，结果会用空格填充。如果输出的值长于该数，结果不会被截断。 |
|       *       | 宽度在 format 字符串中未指定，但是会作为附加整数值参数放置于要被格式化的参数之前。 |



| `.precision` (精度) | 描述                                                         |
| :----------------: | :----------------------------------------------------------- |
|      .number       | 对于整数说明符（d、i、o、u、x、X）：precision 指定了要写入的数字的最小位数。如果写入的值短于该数，结果会用前导零来填充。如果写入的值长于该数，结果不会被截断。精度为 0 意味着不写入任何字符。 对于 e、E 和 f 说明符：要在小数点后输出的小数位数。 对于 g 和 G 说明符：要输出的最大有效位数。 对于 s: 要输出的最大字符数。默认情况下，所有字符都会被输出，直到遇到末尾的空字符。 对于 c 类型：没有任何影响。 当未指定任何精度时，默认为 1。如果指定时不带有一个显式值，则假定为 0。 |
|         .*         | 精度在 format 字符串中未指定，但是会作为附加整数值参数放置于要被格式化的参数之前。 |



| `length` (长度) | 描述                                                         |
| :------------: | :----------------------------------------------------------- |
|       h        | 参数被解释为短整型或无符号短整型（仅适用于整数说明符：i、d、o、u、x 和 X）。 |
|       l        | 参数被解释为长整型或无符号长整型，适用于整数说明符（i、d、o、u、x 和 X）及说明符 c（表示一个宽字符）和 s（表示宽字符字符串）。 |
|       L        | 参数被解释为长双精度型（仅适用于浮点数说明符：e、E、f、g 和 G）。 |


### 例子

```cpp
printf("指定宽度10且右对齐: [%10s] \n", "Hello");
printf("指定宽度10且左对齐: [%-10s] \n", "Hello");
printf("保留3位小数: %.3f %.3f\n", 2. / 3, 2.);
```

输出

```
指定宽度10且右对齐: [     Hello]
指定宽度10且左对齐: [Hello     ]
保留3位小数: 0.667 2.000
```
