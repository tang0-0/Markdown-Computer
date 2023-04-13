# C语言语法笔记

> [C 语言教程 - 网道 (wangdoc.com)](https://wangdoc.com/clang/index.html)
> 
> [C 语言教程 | 菜鸟教程 (runoob.com)](https://www.runoob.com/cprogramming/c-tutorial.html)

---

## 一、关键字(32)

```c
unsigned signed char short int long float double
enum union struct typedef sizeof
void auto static extern register const volatile return 
if else switch case default while do for  
continue break goto
```

## 二、预编译指令

```c
#include
#define    #undef    
#ifdef     #ifndef    #endif    
#if        #endif 
#          ##
#pragma
```

## 三、流程控制

### 3.1 顺序结构

程序按照自上而下的顺序，以`;`为一句，一条一条执行。

### 3.2 循环结构

1. `while`循环
   
   ```c
   while (expression)
   {
       statement
   }
   ```
   
   a. 如果表达式expression为非零值（表示真），则执行statement语句；如果                       expression为零（表示假）就跳出循环，不再执行循环体。
   b. 循环上面步骤。

2. `do while`循环
   
   ```c
   do
   {
       statement
   }
   while(expression)
   ```
   
   a. 先执行statement语句。
   b. 再判断expression是否为非零值，如果非零再次执行statement语句；如果为零，则退出循环。
   c. 循环上面步骤。

3. `for`循环
   
   ```c
   for ( init; condition; increment )
   {
       statement
   }
   ```
   
   a. 首先执行初始化init，可以不在这里写任何语句，只要有一个分号出现即可。
   b. 再判断条件condition是否为真，为真则执行statement；为假则退出循环。
   c. 执行完 for 循环主体后，控制流会跳回上面的 increment 语句，更新循环控制变量。可以留空，只要在条件后有一个分号出现即可。
   d. 循环b、c步骤，直到不满足条件condition退出循环。

4. `contiue`、`break`与`goto`
   
   continue:结束本次循环，重新开始下次循环。
   
   break:退出循环或者switch语句。
   
   goto:无条件跳转到指定语句。

### 3.3 条件结构

1. `if else`判断
   
   ```c
   if(expression1)
   {
       statement1
   }
   else if(expression2)
   {
       statement2
   }
   ...
   else
   {
       statement
   }
   ```
   
   a. 判断表达式expression1是否为真，为真则执行statement1,为假则接着判断。
   b. 判断表达式expression2是否为真，为真则执行statement2,为假则接着判断。
   c. 以上判断都为假，则进入到else，执行statement。

2. `switch case`判断
   
   ```c
   switch(expression)
   {
       case value1:
       statement1
       break;/* 可选的 */
       case value2:
       statement2
       break;/* 可选的 */
       ...
       default:/* 可选的 */
       statement
       break;/* 可选的 */
   }
   ```
   
   判断表达式expression的值和哪个value的值相等，则执行相应的statement。switch语句须满足以下规则：
   
   - switch语句中的expression是一个常量表达式，必须是一个整型或枚举类型。
   - 在一个switch中可以有任意数量的case语句。
   - case的value必须与switch中的变量具有相同的数据类型，且必须是一个常量或字面量。
   - 当被测试的变量等于case中的常量时，case后跟的语句将被执行，直到遇到break语句为止。
   - 当遇到break语句时，switch终止，控制流将跳转到switch语句后的下一行。
   - 不是每一个case都需要包含break。如果case语句不包含break，控制流将会继续后续的case，直到遇到break为止。
   - 一个switch语句可以有一个可选的default case，出现在switch的结尾。default case可用于在上面所有case都不为真时执行一个任务。default case 中的break语句不是必需的。

3. ` ? : `判断
   
   ```c
   expression ? statement1 : statement2
   ```
   
   a. 判断表达式expression是否为真。
   b. 为真则执行statement1;为假则执行statement2。

## 四、变量

### 4.1 命名

1. 只能由字母、下划线和数字组成。
2. 只能由字母或下划线开头。
3. 不超过63字节。

### 4.2 作用域

每个变量都有其作用范围。

C 语言的变量作用域主要有两种：文件作用域（file scope）和块作用域（block scope）。

`文件作用域`：在源码文件所有函数之外声明的变量，从声明的位置到文件结束都有效。也就是全局变量那种。

`块作用域`：由大括号（`{}`）组成的代码块，它形成一个单独的作用域。凡是在块作用域里面声明的变量，只在当前代码块有效，代码块外部不可见。也就是局部变量那种。

当局部变量和全局变量重名，遵循就近原则，在代码块内，局部变量有效，出了代码块，全局变量有效。

### 4.3 数据类型

1. **基本数据类型**
   
   `bool`: 头文件stdbool.h定义了另一个类型别名bool，并且定义了true代表1、false代表0。只要加载这个头文件，就可以使用这几个关键字。
   
   `char`: 字符类型使用一个字节（8位）存储。C 语言将其当作整数处理
   
   `short`: 占用空间不多于int，一般占用2个字节
   
   `int`: 不同计算机的int类型的大小是不一样的。比较常见的是使用4个字节（32位）存储一个int类型的值
   
   `long`: 占用空间不少于int，至少为4个字节。
   
   `long long`: 占用空间多于long，至少为8个字节。
   
   `float`: 占用4个字节（32位），其中8位存放指数的值和符号，剩下24位存放小数的值和符号。float类型至少能够提供（十进制的）6位有效数字，指数部分的范围为（十进制的）-37到37，即数值范围为10-37到1037。
   
   `double`: 占用8个字节（64位），至少提供13位有效数字。
   
   `long double`: 通常占用16个字节。

2. **数据集合**
   
   `array`: 一组同类型数据的集合。
   
    `struct`: 一组不同类型数据的集合。
   
    `enum`: 对相关的标识进行编号。
   
    `union`: 同一块内存共享给不同类型数据使用。

3. **指针**
   
   一块存储地址的内存区域，函数名和数组名其实就是指针。
   
   `数据指针`：指针的值是数据的内存地址。
   
   `函数指针`：指针的值是函数的起始地址，函数名本身就是指向函数代码的指针。
   
   `空指针`： 指针的值为NULL。
   
   `野指针`：指针指向非设定的位置，常见的产生野指针的情况有三种:
   
   - 指针未初始化
   
   - 指针越界访问
   
   - 指针指向free()的返回

### 4.4 修饰符

`auto`: 说明符表示该变量的存储，由编译器自主分配内存空间，且只存在于定义时所在的作用域，退出作用域时会自动释放。由于只要不是`extern`的变量（外部变量），都是由编译器自主分配内存空间的，这属于默认行为，所以该说明符没有实际作用，一般都省略不写。

`static`: 修饰变量只在当前文件有效或者修饰变量为局部静态变量。

`extern`: 表示当前变量在其它文件中声明的，没有必要在当前文件里面为它分配空间。

`const`: 修饰变量属性为只读，不得被修改。

`register`: 修饰变量为高频使用的，需放在可以快速访问的寄存器中。但是，编译器可以忽略这个说明符，不一定按照这个指示行事。

`voliate`: 告诉编译器不要优化这个变量，每次都去内存中读取它的值。

`restrict`: 说明符允许编译器优化某些代码。它只能用于指针，表明该指针是访问数据的唯一方式。

## 五、运算符

### 5.1 算数运算符

`+     -     *     /     %     ++     --`

### 5.2 比较运算符

`>     <     ==     >=     <=     !=`

### 5.3 逻辑运算符

`&&     ||     !`

### 5.4 位运算符

`&     |     ~     ^`

### 5.5 三元运算符

` ? : `

## 六、函数

### 6.1 入参

1. 传值调用
   
   调用函数时，入参的值会被复制给形参，然后函数中的所有操作都是对形参来进行的，形参只在函数内有效。这种情况下，函数是无法对入参进行操作的。

2. 引用调用
   
   通过传入指针的方式，可以对传入的数据进行修改。因为形参复制了指针的值，通过对指针操作可以直接访问数据的那片内存进行修改。

3. const
   
   对入参加上const修饰，告诉编译器函数内部不得修改该参数变量，主要是针对入参是指针的情况。

### 6.2 返回值

函数在退出时可以返回一个数据或者指针，也可以不返回。

### 6.3 修饰符

`static `: 表示该函数只在当前文件中被调用。

`extern`: 表示该函数定义在其它文件中。

### 6.4 函数指针

指针指向函数的起始地址，可以用来构建回调函数或者面向对象编程。

### 6.5 递归

函数自己调用自己本身，但在使用递归时，程序员需要注意定义一个从函数退出的条件，否则会进入死循环。

```c
/* 求斐波那契数列 */
long fibonaci(long i)
{
   if(i == 0)
   {
      return 0;
   }
   if(i == 1)
   {
      return 1;
   }
   return fibonaci(i-1) + fibonaci(i-2);
}
```

### 6.6 可变参数

一般情况下，函数入参的个数都是固定的。但C语言也允许传入不定数目的入参。比如说printf这种`int printf(const char *format, ...)`。

首先，需要引用头文件`<stdarg.h>`，然后使用以下函数：

- `va_start`: 初始化可变参数列表。

- `va_arg`: 取当前的可变参数，每次调用后，内部指针会自动指向下一个可变参数。

- `va_end`: 清理可变参数列表。

```c
/* 取不固定数目数字平均数 */
double average(long num, ...)
{
    double total = 0;
    va_list list;

    list = va_start(list,i);
    for(int i = 0; i < num; i++)
    {
        total += va_arg(list,double);
    }
    va_end(list);

    return (total/num);
}
```

## 七、内存管理

C语言的内存管理由两部分组成：

1. 由系统管理的内存空间，称为栈空间。

2. 由用户手动管理的内存空间，称为堆空间。

### 7.1 栈空间

主要是存放函数内部的局部变量，在函数运行时，使用到的变量会被放入这片空间来进行操作。

### 7.2 堆空间

这片空间由用户手动来进行申请与释放。

### 7.3 区别

1. 分配方式：栈由系统分配，堆是用户分配。

2. 分配速度：栈更快。

3. 使用效率：堆的申请会产生内存碎片，栈不会。

## 八、标准库

[C 标准库 – 参考手册 | 菜鸟教程 (runoob.com)](https://www.runoob.com/cprogramming/c-standard-library.html)