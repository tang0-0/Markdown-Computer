# C语言语法笔记

## 关键字(32)

```c
unsigned signed char short int long float double
enum union struct typedef sizeof
void auto static extern register const volatile return 
if else switch case default while do for  
continue break goto
```

## 预编译

```c
#define        #ifdef    #ifndef        #endif    #if #else
#include
```

## 控制流程

### 顺序结构

程序按照自上而下的顺序，以`;`为一句，一条一条执行。

### 循环结构

1. while循环

2. do while循环

3. for循环

4. contiue与break

## 条件结构

1. if else

2. switch case

3. goto

## 变量

### 命名

1. 由字母、下划线和数字组成
2. 字母或下划线开头
3. 不超过63字节

### 作用域

每个变量都有其作用范围

### 数据类型

#### 基本数据类型
char
short
int
long
long long
float
double

#### 数据集合
arry
struct
enum
union

#### 指针

一块存储地址的内存区域，函数名和数组名其实就是指针。

### 修饰符

auto 
static
extern 
register 
voliate 
const

## 运算符

### 算数运算符
+ - * / % ++ --

### 比较运算符
> < == >= <= !=

### 逻辑运算符
&& || !

### 位运算符
& | ~ ^

### 三元运算符


## 函数

### 入参

### 返回值

### 函数指针

### 修饰符

static extern

## 内存分配

栈空间

堆空间

## 标准库