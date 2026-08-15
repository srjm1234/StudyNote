---
title: 委托与 Lambda
date: 2026-07-20
tags:
  - CSharp
  - 委托
  - Lambda
source: D:\study\C#1\stu0720
---

# 委托与 Lambda

> 核心思想：**C# 中函数也是数据**，存储函数的变量类型就是**委托类型**。Lambda 是匿名函数的简写形式。事件是委托的进一步封装（见 [[04-委托与Lambda/事件]]）。

## 什么是委托

委托就是**函数的数据类型**。C# 是强类型语言，每种数据都有类型——函数也不例外。声明一个委托类型的变量，就可以保存一个函数。

### 自定义委托

```csharp
// 声明委托类型 — 格式：delegate 返回值类型 委托名(参数列表)
public delegate bool Mydelegate(string v);
public delegate int My(string v);
```

### 委托变量的赋值与调用

```csharp
// 1. 用 Lambda 表达式赋值
Mydelegate fn2 = (string v) => { return true; };
My fn3 = v => { return 1; };

// 2. 用方法名赋值（方法签名必须与委托匹配）
My fn = Fn1;
Console.WriteLine(fn("123"));

// 3. 委托变量可以像方法一样调用
fn3("abc");   // → 1

// 4. 将委托作为参数传递
Test test = new Test();
test.TestFn(fn3);     // 传入委托变量
test.TestFn(Fn1);     // 传入方法名
```

### 委托作为方法参数

```csharp
class Test
{
    // 接收函数类型的参数
    public void TestFn(My a)
    {
        Console.WriteLine(a("123"));  // 调用传入的函数
    }
}
```

## 委托实例化

```csharp
public delegate void MyDelegate(int a, int b);
public delegate int My(int a, int b);

// 方式一：new 委托类型(方法名)
MyDelegate myDelegate = new MyDelegate(Add);

// 方式二：直接赋值（简写）
My Mysub = Sub;

// 调用委托
myDelegate(1, 2);       // → "1+2=3"
Mysub(12, 13);          // → -1

// 方式三：使用 Invoke 方法调用
int a = Mysub.Invoke(100, 123);

public static void Add(int a, int b)
{
    Console.WriteLine($"{a}+{b}={a + b}");
}
public static int Sub(int a, int b)
{
    return a - b;
}
```

## 委托调用的空值问题

委托变量如果没有存储任何方法，直接调用会抛出 `NullReferenceException`。

```csharp
public delegate void My(int x);
public My Fns { get; set; }

public void Run()
{
    // ⚠️ 错误：Fns 为 null 时直接调用会抛异常
    // Fns(10);

    // ✅ 方式一：先判断是否为 null
    if (Fns != null) { Fns(100); }

    // ✅ 方式二：使用 ?. 空条件运算符（推荐）
    Fns?.Invoke(1000);
}
```

> **`?.` 运算符**：当变量为 `null` 时，不会执行后面的方法，直接返回 `null`，不会抛异常。

## 多播委托

**多播委托** = 存储了多个方法的委托。调用时按添加顺序依次执行所有方法。

```csharp
public delegate void MyDelegate(string str);

MyDelegate my = Show;          // 添加第 1 个方法
my += test.Show2;              // 添加第 2 个方法
my += test.Show3;              // 添加第 3 个方法

my("hello");
// 输出：show：hello / show2：hello / show3：hello

// 移除方法
my -= test.Show2;
my("world");
// 输出：show：world / show3：world
```

### 多播委托操作汇总

| 操作 | 语法 | 说明 |
|------|------|------|
| 添加方法 | `del += Method` | 追加到委托末尾 |
| 移除方法 | `del -= Method` | 移除第一个匹配的方法 |
| 调用 | `del(args)` 或 `del.Invoke(args)` | 按顺序依次执行所有方法 |
| 判空 | `del?.Invoke(args)` | 安全调用，null 时不抛异常 |

## 内置委托类型：Func / Action

C# 提供了两种内置委托类型，不需要自己声明：

| 委托类型 | 返回值 | 说明 |
|----------|--------|------|
| `Action&lt;参数...&gt;` | **无** | 有参数，无返回值 |
| `Func&lt;参数..., 返回值&gt;` | **有** | 有参数，有返回值（最后一个泛型参数是返回值类型） |

```csharp
// Action — 无返回值
Action fn1 = () =&gt; { };                    // 无参
Action&lt;int&gt; fn2 = (int a) =&gt; { };          // 1 个 int 参数

// Func — 有返回值
Func&lt;string, int&gt; fn4 = (string a) =&gt; { return 0; };  // string 参数，返回 int
Func&lt;int&gt; f = () =&gt; 42;                    // 无参有返回值
Func&lt;int, bool&gt; fn = v =&gt; v &gt; 0;           // 一参有返回值
Func&lt;int, int, bool&gt; fn2 = (a, b) =&gt; a &lt; b; // 两参有返回值
```

### 作为方法参数

```csharp
class Test
{
    public static void Fn1(Action fn) { }              // 接收无返回值的函数
    public static void Fn2(Action&lt;int&gt; fn) { }         // 接收 int 参数无返回值函数
    public static void Fn4(Func&lt;string, int&gt; fn) { }   // 接收 string→int 函数
}

Test.Fn1(F1);       // 传入无参无返回值的方法
Test.Fn2(F2);       // 传入 int 参数无返回值的方法
Test.Fn4(F4);       // 传入 string 参数返回 int 的方法
```

## Lambda 表达式

Lambda 是匿名函数的简写形式。

```csharp
// 完整写法
(参数列表) => { 方法体 }
// 简写（单个表达式时，省略大括号和 return）
v => v % 2 == 0
```

### 简化规则

1. 参数类型可以省略：`(a, b) => { return (a == b); }`
2. 只有一个参数时，可以省略括号：`a => { return false; }`
3. 只有一个表达式时，省略 `{}` 和 `return`，自动返回结果：`(a, b) => a == b`

```csharp
int[] nums = { 1, 2, 3, 4, 5 };
Array.Find(nums, (int v) => { return v % 2 == 0; }); // 完整写法
Array.Find(nums, v => v % 2 == 0);                   // 简写
```

### 嵌套委托

委托可以作为另一个委托的**参数类型**，形成嵌套。

```csharp
Func&lt;Func&lt;int&gt;, bool&gt; fn2 = (Func&lt;int&gt; f) =&gt; { return false; };

// 传参示例：将函数作为参数传给方法
Func&lt;int, bool&gt; fn = (int a) =&gt; { Console.WriteLine("1234"); return true; };
Test(fn);

public static void Test(Func&lt;int, bool&gt; Funct)
{
    Funct('w');   // char 可以隐式转换为 int，'w' 实际传入 119
    Console.WriteLine("发发");
}
```

## 泛型委托

泛型委托让委托更灵活，可以用一个委托类型适配多种数据类型：

```csharp
// 声明泛型委托
public delegate bool MyDelegate&lt;T&gt;(T t);

// 泛型方法中使用泛型委托
public static T Find&lt;T&gt;(T[] nums, MyDelegate&lt;T&gt; del)
{
    foreach (var num in nums)
    {
        if (del(num)) return num;
    }
    return default(T);
}

// 使用：查找第一个偶数
int[] nums = { 1, 2, 3, 4, 5, 6, 7, 8 };
Find(nums, (num) =&gt; num % 2 == 0);    // → 2

// 使用：查找第一个姓"王"的
string[] names = { "张三", "李四", "王五", "赵六" };
Find(names, n =&gt; n.StartsWith("王"));  // → "王五"
```

## 关键知识点总结

| 概念 | 说明 |
|------|------|
| 委托（Delegate） | 方法的类型，用于将方法作为参数传递或赋值给变量 |
| 委托实例化 | `new 委托(方法名)` / 直接赋值 / `Invoke()` 调用 |
| 多播委托 | `+=` 添加多个方法，按顺序执行；`-=` 移除 |
| Func\&lt;T\&gt; | 有返回值的委托，最后一个泛型参数是返回值类型 |
| Action\&lt;T\&gt; | 无返回值的委托 |
| Lambda 表达式 | 匿名函数简写：(参数) =&gt; 表达式 |
| 空值保护 | `委托?.Invoke()` 避免 NullReferenceException |
| char 转 int | C# 中 char 可隐式转为 int（Unicode 码） |

## 相关笔记

- [[04-委托与Lambda/事件]] — 事件（委托的封装，发布-订阅模式）
- [[04-委托与Lambda/Array数组方法]] — Array.Find 系列（委托的实际应用）
- [[05-集合/集合与泛型]] — Action 委托、泛型约束
- [[06-字符串/String常用方法]] — 字符串常用方法
