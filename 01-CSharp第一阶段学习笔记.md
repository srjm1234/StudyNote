---
title: C# 第一阶段学习笔记
date: 2026-08-03
tags:
  - CSharp
  - 总览
  - MOC
---

# C# 第一阶段学习笔记

> 整理自 `D:\study\C#1\` 下多个项目的源码，覆盖 7 月基础语法 → 7 月底面向对象 → 8 月文件 I/O 的完整学习路径。本文为**总览（MOC）**，各主题的详细笔记见底部「相关笔记」。

---

# 目录

- [一、方法与函数](#一方法与函数)
- [二、面向对象基础](#二面向对象基础)
- [三、类与对象](#三类与对象)
- [四、委托与事件](#四委托与事件)
- [五、泛型](#五泛型)
- [六、文件 I/O 操作](#六文件-io-操作)
- [七、项目实战](#七项目实战)

---

# 一、方法与函数

> **来源目录**：`四_方法（函数）/`

## 1.1 方法的定义

```csharp
// 基本语法
[访问修饰符] 返回类型 方法名(参数列表)
{
    // 方法体
    return 返回值;  // 若返回类型为 void，则不需要 return
}
```

| 要素 | 说明 |
|------|------|
| 访问修饰符 | `public` / `private` / `internal` / `protected` |
| 返回类型 | 方法执行完返回的数据类型，无返回值用 `void` |
| 方法名 | 遵循 PascalCase 命名规范 |
| 参数列表 | 方法接收的输入，可多个，用逗号分隔 |

## 1.2 方法的重载（Overload）

同一个类中，方法名相同，**参数列表不同**（参数个数、类型或顺序不同），返回值类型可以不同但**不能仅靠返回值区分重载**。

```csharp
public int Add(int a, int b)       => a + b;
public double Add(double a, double b) => a + b;
public int Add(int a, int b, int c)  => a + b + c;
```

## 1.3 可选参数与命名参数

```csharp
// 可选参数：为参数指定默认值
public void Greet(string name, int age = 18)
{
    Console.WriteLine($"{name} 今年 {age} 岁");
}
Greet("张三");           // 使用默认值 18
Greet("张三", age: 25);  // 命名参数指定值
```

## 1.4 ref 与 out 参数

```csharp
// ref：传入的变量必须在调用前初始化
public void Swap(ref int a, ref int b)
{
    int temp = a; a = b; b = temp;
}
int x = 1, y = 2;
Swap(ref x, ref y);  // x=2, y=1

// out：方法内部必须赋值，调用前无需初始化
public bool TryParse(string s, out int result)
{
    result = int.Parse(s);
    return true;
}
int num;
bool success = TryParse("123", out num);
```

## 1.5 参数数组（params）

```csharp
// 允许传入不定数量的同类型参数
public int Sum(params int[] numbers)
{
    int total = 0;
    foreach (int n in numbers) total += n;
    return total;
}
Sum(1, 2, 3, 4, 5);  // → 15
```

## 1.6 递归方法

```csharp
// 斐波那契数列
public int Fib(int n)
{
    if (n <= 1) return n;
    return Fib(n - 1) + Fib(n - 2);
}
// 阶乘
public int Factorial(int n)
{
    if (n <= 1) return 1;
    return n * Factorial(n - 1);
}
```

> ⚠️ **注意**：递归要有**终止条件**（基准情形），否则会导致栈溢出（StackOverflowException）。

---

# 二、面向对象基础

## 2.1 面向对象的三大特性

| 特性 | 含义 | 关键词 |
|------|------|--------|
| **封装** | 将数据（字段）和行为（方法）包装在一起，对外隐藏内部实现 | `private` + `public` 属性 |
| **继承** | 子类获得父类的属性和方法，实现代码复用 | `:`（单继承） |
| **多态** | 同一个方法在不同对象上有不同的表现 | `virtual` / `override` / `abstract` |

## 2.2 访问修饰符

| 修饰符 | 当前类 | 同程序集 | 子类 | 其他程序集 |
|--------|--------|----------|------|------------|
| `public` | ✅ | ✅ | ✅ | ✅ |
| `private` | ✅ | ❌ | ❌ | ❌ |
| `protected` | ✅ | ❌ | ✅ | ❌ |
| `internal` | ✅ | ✅ | ❌ | ❌ |
| `protected internal` | ✅ | ✅ | ✅ | ❌ |

## 2.3 static 关键字

```csharp
public class MyClass
{
    // 静态字段：属于类本身，所有实例共享
    public static int count;

    // 静态方法：不依赖于实例即可调用
    public static void Show() { }

    // 实例字段：每个对象独有
    public int instanceField;

    // 实例方法：需要通过对象调用
    public void InstanceMethod() { }
}

// 调用方式
MyClass.Show();           // 直接通过类名调用
MyClass.count = 5;
MyClass obj = new MyClass();
obj.InstanceMethod();     // 通过实例调用
```

> 💡 **核心区别**：`static` 属于"类"，不依赖对象；非 `static` 属于"对象"，每个实例独立。

## 2.4 构造函数与析构函数

```csharp
public class Person
{
    public string Name { get; set; }
    public int Age { get; set; }

    // 无参构造函数（默认）
    public Person() { }

    // 带参构造函数
    public Person(string name, int age)
    {
        Name = name;
        Age = age;
    }

    // 析构函数（不推荐手动使用，由 GC 自动调用）
    ~Person() { }
}
```

## 2.5 this 关键字

```csharp
public class Person
{
    private string name;
    public string Name
    {
        get { return name; }
        set { this.name = value; }  // this 可省略，但用于区分同名变量
    }

    // this 调用其他构造函数（构造函数链）
    public Person() : this("未知", 0) { }
    public Person(string name) : this(name, 0) { }
    public Person(string name, int age)
    {
        this.name = name;
        this.Age = age;
    }
}
```

## 2.6 属性（Property）vs 字段（Field）

```csharp
public class Car
{
    // 字段（Field）：直接存储数据，通常用 private
    private string _plateNumber;

    // 属性（Property）：带逻辑控制的"智能字段"
    public string PlateNumber
    {
        get { return _plateNumber; }
        set
        {
            if (!string.IsNullOrEmpty(value))
                _plateNumber = value;
            else
                throw new Exception("车牌号不能为空");
        }
    }

    // 自动属性（编译器自动生成私有字段）
    public DateTime InTime { get; set; }
    public DateTime OutTime { get; set; }
}
```

| 对比 | 字段（Field） | 属性（Property） |
|------|--------------|-----------------|
| 语法 | 直接声明变量 | `get` / `set` 访问器 |
| 控制 | 无法添加验证逻辑 | 可在 `set` 中添加验证 |
| 封装 | 需要配合方法 | 天然封装 |
| 推荐 | 用 `private` 或 `protected` | 对外暴露用 `public` |

> 📌 **参考项目**：`复习/属性与字段.cs`、`停车场/Car.cs`

## 2.7 抽象类（abstract）与接口（interface）

```csharp
// 抽象类：可以有字段、有实现的方法
public abstract class Animal
{
    public string Name { get; set; }    // 字段
    public void Breathe() { }           // 普通方法（有实现）
    public abstract void MakeSound();   // 抽象方法（无实现，子类必须重写）
}

// 接口：只能有方法签名和属性（C# 8.0+ 可以有默认实现）
public interface IFlyable
{
    void Fly();           // 方法声明
    string Name { get; }  // 属性声明
}

// 子类继承抽象类 + 实现接口
public class Bird : Animal, IFlyable
{
    public override void MakeSound()
    {
        Console.WriteLine("叽叽喳喳");
    }
    public void Fly()
    {
        Console.WriteLine("展翅高飞");
    }
    public string Name { get; set; }
}

// 一个类只能有一个父类，但可以有多个接口
public class Bat : Animal, IFlyable, ISwimmable { }
```

| 对比 | 抽象类 | 接口 |
|------|--------|------|
| 继承 | 单继承（`:`） | 多实现（用 `,` 分隔） |
| 字段 | 可以有 | 不能有（C# 8.0 前） |
| 方法实现 | 可以有具体实现 | 默认只有签名 |
| 构造函数 | 可以有 | 不能有 |
| 使用场景 | "Is-a" 关系，有共性 | "Can-do" 关系，能力约定 |

> 📌 **参考项目**：`复习/接口.cs`、`图书管理系统/User.cs`（抽象类 `User` + 实现类 `Student`、`Administrator`）

## 2.8 索引器（Indexer）

> **来源**：`08-面向对象/类与对象/0728作业.md`

索引器让对象可以像数组一样通过索引访问元素。

```csharp
public class Employee
{
    private Dictionary<string, int> nameToAge = new Dictionary<string, int>();
    private Dictionary<int, string> ageToName = new Dictionary<int, string>();

    // string 索引器：按姓名查找/设置年龄
    public int this[string name]
    {
        get { return nameToAge[name]; }
        set { nameToAge[name] = value; }
    }

    // int 索引器：按年龄查找/设置姓名
    public string this[int age]
    {
        get { return ageToName[age]; }
        set { ageToName[age] = value; }
    }
}

// 使用
Employee emp = new Employee();
emp["张三"] = 25;       // string 索引器：设置年龄
int age = emp["张三"];  // → 25
emp[25] = "张三";       // int 索引器：设置姓名
string name = emp[25];  // → "张三"
```

> 💡 **要点**：一个类可以定义多个索引器（索引器重载），只要参数列表不同即可。

---

# 三、类与对象

## 3.1 构造函数初始化 vs 属性初始化

```csharp
public class Car
{
    public string PlateNumber { get; set; }
    public List<string> Handcards { get; set; }  // 引用类型，默认 null

    // ❌ 错误：只初始化了字段，属性未初始化
    public Car() { }

    // ✅ 正确：属性必须显式初始化
    public Car(string plate)
    {
        PlateNumber = plate;
        Handcards = new List<string>();  // 引用类型必须 new
    }
}
```

> ⚠️ **常见陷阱**：引用类型属性（如 `List<T>`）默认值为 `null`，未初始化直接调用会抛 `NullReferenceException`。
> 📌 **参考项目**：`停车场/Car.cs` — 注意其 `Car` 类在构造函数中未初始化可能为 null 的属性。

## 3.2 继承的设计原则

```csharp
// ❌ 空洞继承：子类没有添加任何新成员
class Farmer : Player { }
class Landlord : Player { }

// ✅ 有意义的继承
class Landlord : Player
{
    public bool HasBottomCards { get; set; }  // 地主特有：有底牌
    public List<Card> BottomCards { get; set; }  // 底牌
}
```

> 💡 **继承的原则**：子类应该比父类"更具体"，增加新的字段、属性或方法。空洞继承应删除或加上实际内容。
> 📌 **参考项目**：`停车场/` — `Car` 类使用了自动属性 `get; set;`，`InTime` 和 `OutTime` 使用 `DateTime.Now` 初始化入场时间。

## 3.3 Lambda 表达式与委托

```csharp
// 基本语法：(参数) => 表达式 或 (参数) => { 语句块 }
Func<int, int, int> add = (a, b) => a + b;
Action<string> print = msg => Console.WriteLine(msg);
Predicate<int> isEven = n => n % 2 == 0;

// 集合中的使用
List<int> numbers = new List<int> { 1, 2, 3, 4, 5, 6 };
numbers.Find(n => n > 3);          // → 4
numbers.ForEach(n => Console.WriteLine(n));
numbers.RemoveAll(n => n % 2 == 0); // 移除所有偶数
```

> 📌 **参考项目**：`stu0729/0729作业/0729作业/Program.cs` 中 `Mylist<T>` 类使用 `Func<T, bool>` 实现自定义 Find 方法。

## 3.4 自定义泛型集合（类似 List<T>）

```csharp
// 手动实现简化版泛型列表
public class MyList<T>
{
    private T[] _items = new T[0];

    public void Add(T item)
    {
        T[] newItems = new T[_items.Length + 1];
        newItems[_items.Length] = item;
        _items = newItems;
    }

    // 索引器
    public T this[int index]
    {
        get { return _items[index]; }
        set { _items[index] = value; }
    }

    public int Count() => _items.Length;
}

// 使用
MyList<string> list = new MyList<string>();
list.Add("123");
list.Add("1234");
Console.WriteLine(list[1]);   // → 1234
Console.WriteLine("长度：" + list.Count());  // → 2
```

> 💡 **扩展**：如果频繁 Add，上述每次扩容效率低。更好的做法是初始化较小容量，容量不足时翻倍（参考 `stu0729/0729作业/0729作业/Program.cs` 中的 `Mylist<T>` 实现）。

---

# 四、委托与事件

> **来源目录**：`stu0730/` 多个子项目 + `委托与事件_学习笔记.md`

## 4.1 委托（Delegate）回顾

### 4.1.1 声明委托

```csharp
// 语法：访问修饰符 delegate 返回值类型 委托名(参数列表)
public delegate void MyDelegate(string v);
public delegate int MyMathDelegate(int a, int b);
public delegate bool PredicateDelegate<T>(T value);
```

### 4.1.2 委托的三要素

| 要素 | 说明 | 示例 |
|------|------|------|
| **声明** | 定义方法签名 | `delegate void MyDelegate(string v)` |
| **实例化** | 绑定具体方法 | `MyDelegate del = ShowMessage;` |
| **调用** | 执行所有绑定方法 | `del("Hello");` |

### 4.1.3 委托的实例化与调用

```csharp
// 方式一：实例化委托（传递方法名）
MyDelegate del = new MyDelegate(Show);
del("Hello");

// 方式二：直接赋值（简化写法）
MyDelegate del2 = Show;
del2("Hello");

// 方式三：使用 Invoke 方法（不常用，但可行）
del.Invoke("Hello");

// 被委托的方法签名必须匹配
public void Show(string msg)
{
    Console.WriteLine(msg);
}
```

> 📌 **参考项目**：`stu0730/委托实例化/Program.cs`

### 4.1.4 多播委托

```csharp
MyDelegate del = Show1;
del += Show2;   // 添加订阅者
del += Show3;
del("触发");    // 依次执行 Show1 → Show2 → Show3

del -= Show2;   // 移除订阅者
del("再次触发"); // 执行 Show1 → Show3
```

> 💡 **多播委托**执行顺序：按照 `+=` 的**添加顺序**依次执行。

```csharp
// 实例化多播委托
MyDelegate my = Show;
Test test = new Test();
my += test.Show2;
my += test.Show3;
my("123");      // Show → Show2 → Show3
my -= test.Show2;
my("456");      // Show → Show3
```

> 📌 **参考项目**：`stu0730/多播委托/Program.cs`

### 4.1.5 委托的安全调用（?.Invoke）

```csharp
// ❌ 如果委托没有绑定任何方法，调用会抛 NullReferenceException
Fns(10);  // 危险！

// ✅ 安全调用方式
Fns?.Invoke(10);   // null 时静默返回
// 或
if (Fns != null) Fns(10);
```

> 📌 **参考项目**：`stu0730/委托调用/Program.cs`

### 4.1.6 泛型委托

```csharp
// 自定义泛型委托
public delegate bool MyDelegate<T>(T t);

// 使用
int[] nums = { 1, 2, 3, 4, 5, 6, 7, 8 };
Find(nums, (num) => num % 2 == 0);  // 查找第一个偶数

string[] names = { "张三", "李四", "王五", "赵六" };
Find(names, n => n.StartsWith("王"));  // 查找姓王的
```

> 📌 **参考项目**：`stu0730/泛型委托/Program.cs`

### 4.1.7 内置委托类型：Action 与 Func

| 内置委托 | 返回值 | 参数数量 | 示例 |
|----------|--------|----------|------|
| `Action` | 无 (`void`) | 0 个 | `Action act = () => Console.WriteLine("Hi");` |
| `Action<T1>` | 无 | 1 个 | `Action<int> act = x => Console.WriteLine(x);` |
| `Action<T1, T2>` | 无 | 2 个 | `Action<string, int> act = (s, n) => {};` |
| `Func<T1, TResult>` | 有返回值 | 1 个输入 | `Func<int, int> fn = x => x * 2;` |
| `Func<T1, T2, TResult>` | 有返回值 | 2 个输入 | `Func<int, int, int> fn = (a, b) => a + b;` |

```csharp
// 使用 Action 和 Func 代替自定义委托
Action fn1 = F1;                          // 无参无返回值
Action<int> fn2 = F2;                     // 一个 int 参数，无返回值
Action<string> fn3 = F3;                  // 一个 string 参数，无返回值
Func<string, int> fn4 = F4;               // string 参数，返回 int

// 传入方法作为参数
public void Fn1(Action fn) { fn(); }
public void Fn2(Action<int> fn) { fn(1); }
public void Fn4(Func<string, int> fn) { int result = fn("test"); }
```

> 📌 **参考项目**：`stu0730/内置委托类型/Program.cs`

### 4.1.8 委托的本质理解

```
委托 = 函数的数据类型

普通变量：int a = 5;        // 存整数
委托变量：MyDelegate d = Show; // 存函数

调用委托：d("Hello");       // → 等价于 Show("Hello")
```

> 📌 **参考项目**：`stu0729/委托/Program.cs`

## 4.2 事件（Event）

### 4.2.1 为什么需要事件？

委托可以直接被外部调用，缺乏安全控制：

```csharp
// ❌ 普通委托字段：外部可以直接调用，不受控制
public delegate void MyDelegate(string v);
public MyDelegate MyDelegateField;  // 外部可以恶意调用
obj.MyDelegateField("恶意触发");

// ✅ event 关键字：外部只能订阅/取消，不能直接触发
public event MyDelegate MyEvent;  // 外部只能 += / -=
```

| 操作 | 普通委托字段 | `event` 委托 |
|------|------------|-------------|
| 外部订阅 `+=` | ✅ 允许 | ✅ 允许 |
| 外部取消 `-=` | ✅ 允许 | ✅ 允许 |
| 外部直接调用 `()` | ✅ 允许 ❌ | ❌ 禁止 ✅ |
| 类内部调用 | ✅ 允许 | ✅ 允许 |

> 💡 **`event` 把委托的"调用权"收回了发布者内部，只对外暴露"订阅/取消订阅"能力。**

### 4.2.2 事件模式（标准写法）

```csharp
// ===== 步骤 1：定义委托（通常用 EventHandler<T> 或自定义）=====
public delegate void ValueChangedHandler(string newValue);

// ===== 步骤 2：发布者声明事件 =====
public class Publisher
{
    public event ValueChangedHandler ValueChanged;

    // 内部触发方法
    public void SetValue(string value)
    {
        ValueChanged?.Invoke(value);  // ?. 安全调用
    }
}

// ===== 步骤 3：订阅者实现处理方法 =====
public class Subscriber
{
    public void OnValueChanged(string newValue)
    {
        Console.WriteLine($"值变了：{newValue}");
    }
}

// ===== 步骤 4：使用 =====
Publisher pub = new Publisher();
Subscriber sub = new Subscriber();

pub.ValueChanged += sub.OnValueChanged;   // 订阅
pub.SetValue("罗");                        // 触发
pub.ValueChanged -= sub.OnValueChanged;   // 取消订阅
```

### 4.2.3 自定义事件参数（标准模式）

```csharp
// 继承 EventArgs，传递事件数据
public class ValueChangedEventArgs : EventArgs
{
    public string NewValue { get; }
    public ValueChangedEventArgs(string value) => NewValue = value;
}

// 委托签名：void 方法名(object sender, EventArgs e)
public delegate void ValueChangedHandler(object sender, ValueChangedEventArgs e);

// sender：触发事件的发布者对象
// e：事件携带的数据
```

### 4.2.4 事件与观察者模式

```
                     ┌──────────┐
  触发  ────────>   │  事件    │  ──────>  订阅者A
                    └────┬─────┘
                         │
  委托链 <────────────── │
                         │
                    ┌────┴─────┐
  多个订阅者 <────── │  发布者  │
                    └──────────┘
```

| 好处 | 说明 |
|------|------|
| **解耦** | 发布者不需要知道订阅者是谁 |
| **安全** | 外部只能订阅/取消，不能随意触发 |
| **灵活** | 一个事件可以同时通知多个订阅者 |

### 4.2.5 事件练习：水温监控系统

```csharp
class WaterStatus
{
    public delegate void Status(int temp);
    public event Status Water;

    public void Heat()
    {
        for (int i = 0; i <= 100; i += 10)
        {
            Water?.Invoke(i);      // 触发事件
            Thread.Sleep(500);
        }
    }
}

class Monitor
{
    public void Show(int temp) => Console.WriteLine($"当前水温：{temp} ℃");
}

class Alarm
{
    public void Ding(int temp)
    {
        if (temp == 100)
        {
            Console.WriteLine("水烧开了！");
            Console.Beep(800, 500);
        }
    }
}

// 使用
WaterStatus water = new WaterStatus();
Monitor monitor = new Monitor();
Alarm alarm = new Alarm();
water.Water += monitor.Show;
water.Water += alarm.Ding;
water.Heat();
// 输出：当前水温：0 ℃ → ... → 当前水温：100 ℃ → 水烧开了！
```

### 4.2.6 锅炉压力温度监控（综合练习）

```csharp
class Boiler
{
    public event Action<double> THigh;   // 温度过高
    public event Action<double> TLow;    // 温度过低
    public event Action<double> PHigh;   // 压力过高
    public event Action<double> PLow;    // 压力过低
    public event Action<double, double> OnLog;  // 日志事件

    private double _temperature;
    public double Temperature
    {
        get => _temperature;
        set
        {
            _temperature = value;
            if (value > 300) THigh?.Invoke(value);
            if (value < 200) TLow?.Invoke(value);
        }
    }

    private double _pressure;
    public double Pressure
    {
        get => _pressure;
        set
        {
            _pressure = value;
            if (value > 2000) PHigh?.Invoke(value);
            if (value < 500) PLow?.Invoke(value);
        }
    }

    public void Log() => OnLog?.Invoke(_temperature, _pressure);
}

// 使用
Boiler boiler = new Boiler();
TemperMonitor tempMonitor = new TemperMonitor();
PressMonitor pressMonitor = new PressMonitor();

boiler.THigh += tempMonitor.High;   // 温度过高 → 显示警告
boiler.TLow += tempMonitor.Low;     // 温度过低 → 显示警告
boiler.PHigh += pressMonitor.High;  // 压力过高 → 显示警告
boiler.PLow += pressMonitor.Low;    // 压力过低 → 显示警告
boiler.OnLog += (t, p) => Console.WriteLine($"[日志] T={t}, P={p}");
```

> 📌 **参考项目**：`stu0730/0730作业/0730作业/Program.cs`（锅炉监控）、`stu0730/练习/Program.cs`（水温监控）

### 4.2.7 委托与事件的区别总结

```
委托                        事件
─────────────────────────────────────────
定义方法签名                定义方法签名（通常与委托配套）
可以存储多个方法引用        存储多个订阅方法（多播）
外部可以调用               外部只能 += / -=
没有访问控制                封装了调用权限

关系：事件 = event 关键字 + 委托
      事件是委托的安全包装
```

> 💡 **记忆口诀**：委托管"做什么"，事件管"谁能触发"。
> 📌 **参考项目**：`委托与事件_学习笔记.md`（更详细的解释）

---

# 五、泛型

> **来源目录**：`stu0729/泛型的使用/`、`stu0729/泛型的约束/`

## 5.1 泛型的基本概念

**泛型**允许在定义类、方法时不指定具体类型，而是在使用时再指定。

```csharp
// 非泛型：只能存 int
List<int> numbers = new List<int>();

// 非泛型：只能存 string
List<string> names = new List<string>();

// 泛型：可以用任何类型
MyList<T> list = new MyList<int>();    // T = int
MyList<T> list2 = new MyList<string>(); // T = string
```

## 5.2 泛型类

```csharp
// 定义泛型类
public class People<TKey, TValue>
{
    public TKey A { get; set; }
    public TValue B { get; set; }
}

// 使用
People<int, string> person = new People<int, string>();
person.A = 1;
person.B = "张三";

// 泛型在接口中使用
public interface IBook<T>
{
    T Id { get; set; }
    T Name { get; set; }
}

class IntBook : IBook<int>
{
    public int Id { get; set; }
    public int Name { get; set; }
}

class StringBook : IBook<string>
{
    public string Id { get; set; }
    public string Name { get; set; }
}
```

## 5.3 泛型方法

```csharp
public class Test
{
    // 泛型方法：类型参数在方法名后声明
    public T Find<T>(T[] array, Predicate<T> condition)
    {
        foreach (T item in array)
        {
            if (condition(item))
                return item;
        }
        return default(T);  // 返回类型的默认值（int→0, string→null）
    }
}

// 使用
int[] nums = { 1, 2, 3, 4, 5 };
int found = test.Find(nums, n => n > 3);  // → 4
```

## 5.4 泛型约束（where）

泛型约束限制 T 能使用的类型范围，提高类型安全性。

| 约束 | 语法 | 含义 | 示例 |
|------|------|------|------|
| `struct` | `where T : struct` | T 只能是值类型 | `int`, `double`, `DateTime` |
| `class` | `where T : class` | T 只能是引用类型 | `string`, `List<T>`, 自定义类 |
| `基类名` | `where T : People` | T 必须是 People 或其子类 | `People`, `Student` |
| `接口名` | `where T : IPeople` | T 必须实现 IPeople | 所有实现该接口的类 |
| `new()` | `where T : new()` | T 必须有公共无参构造函数 | 大多数类 |

```csharp
class Test
{
    // T 只能是值类型（int, double, bool 等）
    public void Fn1<T>(T v) where T : struct { }

    // T 只能是引用类型（string, class 等）
    public void Fn2<T>(T v) where T : class { }

    // T 必须是 People 或其派生类
    public void Fn3<T>(T v) where T : People { }

    // T 必须实现 IPeople 接口
    public void Fn4<T>(T v) where T : IPeople { }

    // T 必须有公共无参构造函数
    public T Create<T>() where T : new() => new T();
}

// 测试
test.Fn1(123);             // ✅ int 是值类型
// test.Fn1("231");        // ❌ string 是引用类型，编译报错

test.Fn2(new People());    // ✅ People 是引用类型
test.Fn3(new People());    // ✅
test.Fn3(new Student());   // ✅ Student 继承 People
// test.Fn3(new Book());   // ❌ Book 不继承 People
```

## 5.5 泛型的优势

| 优势 | 说明 |
|------|------|
| **类型安全** | 编译期检查类型，避免运行时错误 |
| **性能优化** | 值类型无需装箱/拆箱（相比 `ArrayList`） |
| **代码复用** | 一套代码支持多种类型 |
| **消除强制转换** | 无需 `(int)list[0]` 这样的类型转换 |

---

# 六、文件 I/O 操作

> **来源目录**：`stu0731/` 多个子项目

## 6.1 File 类（静态工具类）

`File` 类提供了一组静态方法，用于常见的文件操作。

```csharp
string path = @"./data.txt";

// 判断文件是否存在
bool exists = File.Exists(path);

// 写入全部内容（覆盖）
File.WriteAllText(path, "Hello World");

// 写入多行（覆盖）
File.WriteAllLines(path, new string[] { "第一行", "第二行", "第三行" });

// 追加文本到文件末尾
File.AppendAllText(path, "追加的内容");

// 读取全部文本
string content = File.ReadAllText(path);

// 逐行读取（返回字符串数组）
string[] lines = File.ReadAllLines(path);
foreach (string line in lines)
{
    Console.WriteLine(line);
}
```

### 常用 File 方法速查

| 方法 | 说明 |
|------|------|
| `File.Exists(path)` | 判断文件是否存在 |
| `File.Create(path)` | 创建文件 |
| `File.Copy(src, dest)` | 复制文件 |
| `File.Move(src, dest)` | 移动/重命名文件 |
| `File.Delete(path)` | 删除文件 |
| `File.ReadAllText(path)` | 读取全部文本 |
| `File.ReadAllLines(path)` | 逐行读取 |
| `File.WriteAllText(path, text)` | 覆盖写入文本 |
| `File.WriteAllLines(path, lines)` | 覆盖写入多行 |
| `File.AppendAllText(path, text)` | 追加写入文本 |
| `File.AppendAllLines(path, lines)` | 追加写入多行 |

> 📌 **参考项目**：`stu0731/File类操作文件/Program.cs`

## 6.2 FileInfo 类（实例类）

`FileInfo` 提供更丰富的文件信息操作，需要实例化。

```csharp
FileInfo file = new FileInfo(@"D:\study\data.txt");

// 文件信息属性
Console.WriteLine($"名称：{file.Name}");           // data.txt
Console.WriteLine($"大小：{file.Length} 字节");     // 文件大小
Console.WriteLine($"完整路径：{file.FullName}");    // 完整路径
Console.WriteLine($"目录：{file.DirectoryName}");   // 所在目录
Console.WriteLine($"扩展名：{file.Extension}");     // .txt
Console.WriteLine($"创建时间：{file.CreationTime}"); // 创建时间
Console.WriteLine($"访问时间：{file.LastAccessTime}"); // 最后访问
Console.WriteLine($"写入时间：{file.LastWriteTime}");  // 最后写入

// 文件操作方法（与 File 类类似）
file.Create();
file.CopyTo(@"D:\backup.txt", true);  // true 表示覆盖
file.MoveTo(@"D:\newdata.txt");
file.Delete();
```

| 对比 | File 类 | FileInfo 类 |
|------|---------|-------------|
| 类型 | 静态类 | 实例类 |
| 使用方式 | `File.ReadAllText(path)` | `new FileInfo(path).ReadAllText()` |
| 适用场景 | 单次操作 | 需要多次操作同一文件（创建后缓存属性） |

> 📌 **参考项目**：`stu0731/File类 FileInfo文件信息类/Program.cs`

## 6.3 Directory 与 DirectoryInfo（目录操作）

```csharp
// Directory（静态类）
bool exists = Directory.Exists(@"./MyFolder");      // 判断目录是否存在
Directory.CreateDirectory(@"./MyFolder");            // 创建目录
Directory.Delete(@"./EmptyFolder");                  // 删除空目录
Directory.Delete(@"./NonEmptyFolder", true);         // 删除非空目录（true 递归）
Directory.Move(@"./OldName", @"./NewName");          // 移动/重命名目录

// 获取目录下的所有文件
string[] files = Directory.GetFiles(@"./Data");
// 获取所有子目录
string[] dirs = Directory.GetDirectories(@"./Data");

// DirectoryInfo（实例类）
DirectoryInfo dir = new DirectoryInfo(@"./MyFolder");
Console.WriteLine($"名称：{dir.Name}");
Console.WriteLine($"完整路径：{dir.FullName}");
Console.WriteLine($"创建时间：{dir.CreationTime}");
Console.WriteLine($"访问时间：{dir.LastAccessTime}");
Console.WriteLine($"写入时间：{dir.LastWriteTime}");
```

> 📌 **参考项目**：`stu0731/Director目录类 DirectorInfo目录信息类/Program.cs`

## 6.4 StreamReader 与 StreamWriter（文本流读写）

基于 `FileStream` 的文本读写方式，适合大文件逐行操作。

```csharp
// 写入（追加模式）
using (FileStream fs = new FileStream("./账号.txt", FileMode.Append, FileAccess.Write))
using (StreamWriter sw = new StreamWriter(fs))
{
    sw.WriteLine("张三,123456");
    sw.WriteLine("李四,654321");
}  // using 自动释放资源

// 读取（逐行读取）
using (FileStream fs = new FileStream("./账号.txt", FileMode.Open, FileAccess.Read))
using (StreamReader sr = new StreamReader(fs))
{
    // 方式一：逐行读取
    string line;
    while ((line = sr.ReadLine()) != null)
    {
        Console.WriteLine(line);
    }

    // 方式二：读取全部（注意：ReadToEnd 会读到文件末尾）
    fs.Position = 0;  // 重置指针
    string all = sr.ReadToEnd();
    Console.WriteLine(all);
}
```

> ⚠️ **注意**：`ReadLine()` 会移动文件指针，如果前面已经读取到末尾，再调用 `ReadToEnd()` 会得到空字符串。如果要用两种方式，需要重置 `fs.Position = 0`。
> 📌 **参考项目**：`stu0731/StreamReader StreamWriter/Program.cs`

## 6.5 BinaryWriter 与 BinaryReader（二进制流读写）

写入时会记录数据类型信息，读取时按写入顺序还原。

```csharp
// 二进制写入
using (FileStream fs = new FileStream("./data.bin", FileMode.OpenOrCreate, FileAccess.Write))
using (BinaryWriter bw = new BinaryWriter(fs, Encoding.UTF8))
{
    bw.Write("张三");           // 写入字符串（带长度信息）
    bw.Write(25);              // 写入整数（4 字节）
    bw.Write(3.14);            // 写入 double（8 字节）
    bw.Write(true);            // 写入布尔值
    bw.Flush();                // 刷新缓冲区
}

// 二进制读取（必须按写入顺序读取！）
using (FileStream fs = new FileStream("./data.bin", FileMode.Open, FileAccess.Read))
using (BinaryReader br = new BinaryReader(fs, Encoding.UTF8))
{
    string name = br.ReadString();   // 按顺序读取
    int age = br.ReadInt32();
    double score = br.ReadDouble();
    bool active = br.ReadBoolean();
}
```

| 对比 | StreamWriter/StreamReader | BinaryWriter/BinaryReader |
|------|---------------------------|---------------------------|
| 数据格式 | 纯文本（人类可读） | 二进制（机器可读） |
| 空间占用 | 较大 | 较小 |
| 读取方式 | 按行/按文本 | 按类型顺序读取 |
| 适用场景 | 配置文件、日志 | 需要高效存储或跨平台 |

> 📌 **参考项目**：`stu0731/BinaryWrite BinaryReader/Program.cs`

## 6.6 FileMode、FileAccess、FileShare 详解

### FileMode（文件打开模式）

| 值 | 说明 |
|----|------|
| `Create` | 创建新文件，存在则覆盖 |
| `CreateNew` | 创建新文件，存在则抛异常 |
| `Open` | 打开已有文件，不存在则抛异常 |
| `OpenOrCreate` | 打开已有文件，不存在则创建 |
| `Truncate` | 打开已有文件并清空内容 |
| `Append` | 打开文件并将指针移到末尾（只能写入） |

### FileAccess（访问权限）

| 值 | 说明 |
|----|------|
| `Read` | 只读 |
| `Write` | 只写 |
| `ReadWrite` | 读写 |

### 组合使用示例

```csharp
// 日志文件：追加写入
new FileStream("log.txt", FileMode.Append, FileAccess.Write)

// 配置文件：读写
new FileStream("config.json", FileMode.OpenOrCreate, FileAccess.ReadWrite)

// 只读数据文件
new FileStream("data.txt", FileMode.Open, FileAccess.Read)
```

## 6.7 文本数据存储格式

### 管道符分隔（`|`）

图书管理项目中使用的方式：

```
1|C#入门到精通|张三|10|10
2|数据结构与算法|李四|5|3
```

```csharp
// 写入
string line = $"{book.Id}|{book.Title}|{book.Author}|{book.Sum}|{book.Num}";
File.AppendAllText(path, line + "\n");

// 读取
string[] lines = File.ReadAllLines(path);
foreach (string line in lines)
{
    string[] parts = line.Split('|');
    int id = int.Parse(parts[0]);
    string title = parts[1];
    string author = parts[2];
    int sum = int.Parse(parts[3]);
    int num = int.Parse(parts[4]);
}
```

### CSV（逗号分隔值）

```
1,C#入门到精通,张三,10,10
2,数据结构与算法,李四,5,3
```

```csharp
// 使用 string.Split(',') 解析，注意字段含逗号时需用引号包裹
```

> ⚠️ **注意**：`String.Split()` 返回的数组元素是引用，**不能直接修改**（如 `str.Split('|')[4] = "新值"` 不会生效），需要重新组合赋值。
> 📌 **参考项目**：`图书管理/Library.cs` 中的 `Return()` 方法存在此问题。

### 账号密码存储（`/` 分隔）

```
admin/123456
student/654321
```

```csharp
string[] users = File.ReadAllLines("users.txt");
foreach (string user in users)
{
    string[] parts = user.Split('/');
    string account = parts[0];
    string password = parts[1];
}
```

---

# 七、项目实战

## 7.1 停车场管理系统

> **来源目录**：`停车场/`、`stu0723/停车场/`

### 项目结构

```
停车场/
├── Program.cs      # 程序入口，菜单循环
├── Car.cs          # 车辆数据模型
└── Stopcar.cs      # 停车场业务逻辑
```

### 核心类分析

**Car 类（车辆模型）**

```csharp
public class Car
{
    // 车牌号（带验证）
    private string _plateNumber;
    public string PlateNumber
    {
        get => _plateNumber;
        set
        {
            if (value != null)
                _plateNumber = value;
            else
                throw new Exception("输入无效数据");
        }
    }

    // 入场时间（构造函数初始化）
    public DateTime InTime { get; set; } = DateTime.Now;

    // 离场时间（离场时赋值）
    public DateTime OutTime { get; set; }
}
```

**Stopcar 类（停车场管理）**

```csharp
public class Stopcar
{
    private List<Car> _cars = new List<Car>();
    private const int Capacity = 10;  // 车位容量

    // 入场
    public void CarIn(string plate)
    {
        if (_cars.Count >= Capacity)
        {
            Console.WriteLine("车位已满");
            return;
        }
        if (_cars.Exists(c => c.PlateNumber == plate))
        {
            Console.WriteLine("已存在车辆");
            return;
        }
        _cars.Add(new Car(plate));
        Console.WriteLine($"车牌号:{plate},入场时间:{DateTime.Now:yyyy-MM-dd HH:mm:ss}");
    }

    // 出场（含费用计算）
    public void CarOut(string plate)
    {
        int index = _cars.FindIndex(c => c.PlateNumber == plate);
        if (index == -1)
        {
            Console.WriteLine("车辆不存在");
            return;
        }
        Car car = _cars[index];
        car.OutTime = DateTime.Now;
        TimeSpan duration = car.OutTime - car.InTime;

        Console.WriteLine($"车牌号:{car.PlateNumber}");
        Console.WriteLine($"入场时间:{car.InTime:yyyy-MM-dd HH:mm:ss}");
        Console.WriteLine($"离场时间:{car.OutTime:yyyy-MM-dd HH:mm:ss}");

        // 费用计算（按分钟计费）
        int minutes = (int)duration.TotalMinutes;
        int fee = minutes < 30 ? 5 : 5 + (minutes - 30) * 10;
        Console.WriteLine($"停车时长：{minutes} 分钟");
        Console.WriteLine($"停车应缴费用 {fee}￥");

        _cars.RemoveAt(index);
    }

    // 查看停车场状态
    public void ShowStatus()
    {
        Console.WriteLine($"停车场状态：{_cars.Count}/10 个车位已被占用");
        foreach (Car car in _cars)
        {
            TimeSpan parked = DateTime.Now - car.InTime;
            Console.WriteLine($"车牌号：{car.PlateNumber}，" +
                $"入场时间：{car.InTime:yyyy-MM-dd HH:mm:ss}，" +
                $"已停放：{parked.Days}天{parked.Hours}小时{parked.Minutes}分钟");
        }
    }
}
```

### 程序入口（菜单循环）

```csharp
static void Main(string[] args)
{
    Stopcar stopcar = new Stopcar();
    bool running = true;

    while (running)
    {
        Console.WriteLine("\n=== 停车管理系统 ===");
        Console.WriteLine("1. 车辆入场");
        Console.WriteLine("2. 车辆离场");
        Console.WriteLine("3. 查看停车场状态");
        Console.WriteLine("4. 退出系统");
        Console.Write("请选择操作[1-4]：");

        int choice = int.Parse(Console.ReadLine());
        switch (choice)
        {
            case 1:
                Console.Write("输入车牌号：");
                string plate = Console.ReadLine();
                stopcar.CarIn(plate);
                break;
            case 2:
                Console.Write("输入车牌号：");
                plate = Console.ReadLine();
                stopcar.CarOut(plate);
                break;
            case 3:
                stopcar.ShowStatus();
                break;
            case 4:
                Console.WriteLine("欢迎下次光临！");
                running = false;
                break;
            default:
                Console.WriteLine("无效选择，请重新输入");
                break;
        }
    }
}
```

### 优化建议

| 问题 | 当前实现 | 改进建议 |
|------|---------|---------|
| 费用计算 | `timeSpan.Seconds` 实际只有 0-59 秒 | 应使用 `TotalMinutes` 计算总分钟数 |
| 格式字符串 | `"yyy-MM-dd"` 错误（应为 `yyyy`） | 修正为 `yyyy-MM-dd HH:mm:ss` |
| 输入验证 | `int.Parse` 在非法输入时崩溃 | 使用 `int.TryParse` |
| 容量硬编码 | `Count = 10` | 可改为可配置 |

## 7.2 图书管理系统（迭代演变）

> **来源目录**：`图书管理/` → `图书管理系统/`

### 7.2.1 第一版：基础版（`图书管理/`）

**项目结构**

```
图书管理/
├── Book.cs           # 图书数据模型
├── Library.cs        # 图书馆逻辑（含管理员/学生功能）
└── Program.cs        # 入口（空）
```

**Book 数据模型**

```csharp
public class Book
{
    public int Id { get; set; }           // 编号
    public string Title { get; set; }     // 书名
    public string Author { get; set; }    // 作者
    public int Sum { get; set; }          // 总库存量
    public int Num { get; set; }          // 可借数量
    public string Status { get; set; }    // 借阅状态（"可借"/"不可借"）
}
```

**数据存储格式**（`Data/books.txt`）

```
1|C#入门到精通|张三|10|10
2|数据结构与算法|李四|5|3
```

**Library 类**

```csharp
public class Library
{
    public static List<Book> books = new List<Book>();
    public static string booksPath = @"../../Data/books.txt";
    public static string recordsPath = @"../../Data/borrow_records.txt";

    // 从文件加载图书数据
    public static List<Book> LoadBooks()
    {
        string[] lines = File.ReadAllLines(booksPath);
        foreach (string line in lines)
        {
            string[] parts = line.Split('|');
            books.Add(new Book
            {
                Id = int.Parse(parts[0]),
                Title = parts[1],
                Author = parts[2],
                Sum = int.Parse(parts[3]),
                Num = int.Parse(parts[4])
            });
        }
        return books;
    }

    // 事件定义（用于数据变更通知）
    public delegate void BookDelegate();
    public event BookDelegate BookChanged;
}
```

**第一版问题**：

| 问题 | 说明 |
|------|------|
| 主程序为空 | `Program.cs` 中 `Main` 方法为空 |
| 事件未使用 | `BookChanged` 事件声明但从未触发 |
| 文件写入错误 | `AdmChange()` 中 `sw.WriteLine(book)` 会调用 `ToString()`，输出类型名而非数据 |
| 归还逻辑错误 | `str2[i].Split('|')[4] = ...` 字符串不可直接修改 |

### 7.2.2 第二版：完整系统（`图书管理系统/`）

**项目结构**

```
图书管理系统/
├── Program.cs          # 入口
├── CLI.cs              # 命令行界面（登录菜单）
├── Login.cs            # 登录验证
├── User.cs             # 用户抽象类 + 学生/管理员子类
├── Book.cs             # 图书数据模型
├── BookManager.cs      # 图书管理（增删改查 + 事件）
├── Library.cs          # 抽象基类 + 管理菜单入口
└── Data/
    ├── books.txt       # 图书数据
    ├── borrow_records.txt  # 借阅记录
    └── User/
        ├── Student.txt  # 学生账号
        └── Administrator.txt  # 管理员账号
```

**整体架构**

```
用户身份选择（CLI.login）
    ├── 1. 学生 → Login.StuLogin() → StuLib.Manage()
    │                                    ├── 1. 查看所有图书
    │                                    ├── 2. 查询图书
    │                                    ├── 3. 借阅图书
    │                                    └── 4. 归还图书
    └── 2. 管理员 → Login.AdmLogin() → AdmLib.Manage()
                                         ├── 1. 添加图书
                                         ├── 2. 查看所有图书
                                         └── 3. 查看借阅记录
```

**登录系统**

```csharp
// 账号文件格式：账号/密码
// Student.txt: 张三/123456
// Administrator.txt: admin/123456

public static void StuLogin()
{
    string[] accounts = File.ReadAllLines(stuPath);
    Console.Write("请输入账号：");
    string inputAcc = Console.ReadLine();
    Console.Write("请输入密码：");
    string inputPwd = Console.ReadLine();

    foreach (string account in accounts)
    {
        string[] parts = account.Split('/');
        if (parts[0] == inputAcc && parts[1] == inputPwd)
        {
            Console.WriteLine("登录成功");
            StuLib.Manage();
            return;
        }
    }
    Console.WriteLine("登录失败");
}
```

**事件驱动的图书管理**

```csharp
public class BookManager
{
    public static List<Book> books = LoadBooks();
    public event BookDelegate BookChanged;

    // 触发事件并保存数据
    public void Change()
    {
        BookChanged?.Invoke();  // 先执行绑定的业务逻辑
        SaveBooks();            // 再持久化到文件
    }

    private void SaveBooks()
    {
        using (FileStream fs = new FileStream(path, FileMode.Open, FileAccess.Write))
        using (StreamWriter sw = new StreamWriter(fs))
        {
            foreach (Book book in books)
            {
                sw.WriteLine($"{book.Id}|{book.Title}|{book.Author}|{book.Sum}|{book.Num}");
            }
        }
    }
}

// 菜单中使用事件绑定操作
case "1":
    BookManager manager = new BookManager();
    manager.Book += new StLib().StuLook;  // 绑定"查看所有图书"方法
    manager.Change();                      // 触发 → 执行 StuLook → 保存
    break;
```

**借阅记录格式**（`Data/borrow_records.txt`）

```
张三|1|C#入门到精通|2025/7/29 14:30:00||未归还
李四|2|数据结构|2025/7/29 15:00:00||已归还
```

**版本对比**

| 功能 | 第一版 | 第二版 |
|------|--------|--------|
| 登录系统 | ❌ 无 | ✅ 学生/管理员角色分离 |
| 数据持久化 | 部分 | ✅ FileStream + StreamWriter |
| 事件机制 | 声明但未用 | ✅ 事件驱动操作 |
| 面向对象设计 | 简单 | ✅ 抽象类 + 继承 + 多态 |
| 用户界面 | ❌ 无 | ✅ 菜单系统 |

## 7.3 斗地主项目（代码问题分析）

> **来源目录**：`stu0725/7斗地主/` + `0728作业.md`

### 项目文件

```
斗地主/
├── Card.cs       # 牌的数据模型
├── Player.cs     # 玩家/角色模型
├── Game.cs       # 游戏核心逻辑
└── Program.cs    # 程序入口
```

### 问题汇总与修复路线

#### P0（致命，必修复）

| # | 文件 | 问题 | 修复 |
|---|------|------|------|
| 1 | `Player.cs` | `Handcards` 属性未初始化，访问即抛 `NullReferenceException` | 构造函数中 `Handcards = new List<Card>()` |
| 2 | `Game.cs` | `players` 列表永远为空，发牌时 `IndexOutOfRangeException` | 构造函数中创建 3 个 Player 并加入列表 |
| 3 | `Game.cs` | 构造函数参数 `p1, p2, p3` 形同虚设 | 实际使用参数创建 Player 对象 |

```csharp
// 修复后的 Game 构造函数
public Game(string p1, string p2, string p3)
{
    players = new List<Player>
    {
        new Player(p1),
        new Player(p2),
        new Player(p3)
    };
}
```

#### P1（严重，影响核心玩法）

| # | 文件 | 问题 | 修复 |
|---|------|------|------|
| 4 | `Card.cs` | 缺少牌面大小权重 | 增加 `int Rank` 属性（3→3, ..., K→13, A→14, 2→15, 小王→16, 大王→17） |
| 5 | `Player.cs` | 冗余 `cards` 字段 | 删除未使用的字段 |
| 6 | `Player.cs` | 空洞继承 `Farmer`/`Landlord` | 增加 `bool IsLandlord` 或角色枚举 |

#### P2（一般，优化完善）

| # | 文件 | 问题 | 修复 |
|---|------|------|------|
| 7 | `Game.cs` | 洗牌算法有统计偏差 | 替换为标准 Fisher-Yates 洗牌 |
| 8 | `Card.cs`/`Game.cs` | 花色字符串尾部有空格 | 去除字符串中的尾部空格 |
| 9 | `Game.cs` | `Console.OutputEncoding` 混在牌生成方法中 | 移到 `Program.Main` |
| 10 | `Game.cs` | 无叫地主/抢地主流程 | 增加叫分环节 |
| 11 | `Program.cs` | 缺少异常处理 | 增加 `try-catch` |

#### Fisher-Yates 洗牌算法

```csharp
// ❌ 当前实现（有偏差）
for (var i = 0; i < cards.Count; i++)
{
    int r = rnd.Next(cards.Count);
    (cards[i], cards[r]) = (cards[r], cards[i]);
}

// ✅ 标准 Fisher-Yates（均匀随机）
for (int i = cards.Count - 1; i > 0; i--)
{
    int r = rnd.Next(i + 1);
    (cards[i], cards[r]) = (cards[r], cards[i]);
}
```

### 当前运行结果

```
程序启动 → GenerateDeck() 生成 54 张牌 → DealCards()
                                           ↓
                              players.Count == 0
                                           ↓
                              players[0] → 索引越界
                                           ↓
                              System.IndexOutOfRangeException
```

> 💡 **结论**：当前代码可以编译通过，但运行时必然崩溃。必须先修复 P0 级别的三个问题。

---

## 7.4 各项目技术要点速查

| 项目 | 核心技术 | 关键知识点 |
|------|---------|-----------|
| **停车场** | List、DateTime、LINQ | `List<T>.FindIndex()`、`TimeSpan` 计算 |
| **图书管理 v1** | 文件读写、继承 | `File.ReadAllLines`、`Split('|')`、继承 |
| **图书管理系统 v2** | 事件驱动、抽象类、多态 | `event`、`abstract class`、菜单系统 |
| **斗地主** | 类设计、集合操作 | 洗牌算法、索引器、构造函数 |
| **0729作业** | 泛型集合、索引器、委托 | 自定义 `MyList<T>`、索引器重载 |
| **0730作业** | 事件、委托、多播 | `Action<T>`、事件模式、观察者模式 |
| **0731作业** | File I/O | File/FileInfo、Stream 流、二进制读写 |

---

# 附录：C# 常用语法速查

## A.1 常用集合类型

| 集合 | 特点 | 适用场景 |
|------|------|---------|
| `List<T>` | 动态数组，有序，可重复 | 通用列表 |
| `Dictionary<TKey, TValue>` | 键值对，快速查找 | 映射关系 |
| `HashSet<T>` | 无序，不重复 | 去重、集合运算 |
| `Queue<T>` | 先进先出 | 排队、消息处理 |
| `Stack<T>` | 后进先出 | 撤销、回溯 |

## A.2 常用的 Lambda 表达式

```csharp
// 选择（投影）
numbers.Select(n => n * 2)

// 过滤
numbers.Where(n => n > 5)

// 排序
books.OrderBy(b => b.Title)
books.OrderByDescending(b => b.Id)

// 聚合
numbers.Sum()
numbers.Count()
numbers.Average()
numbers.Max()
numbers.Min()

// 判断
numbers.Any(n => n > 5)    // 是否存在大于 5 的元素
numbers.All(n => n > 0)    // 是否全部大于 0
numbers.First(n => n > 5)  // 第一个大于 5 的元素
numbers.FirstOrDefault(n => n > 5)  // 不存在则返回默认值
```

## A.3 常用的 string 方法

```csharp
string s = "  Hello, World!  ";

s.Trim();                          // 去除两端空格 → "Hello, World!"
s.TrimStart();                     // 去除左侧空格
s.TrimEnd();                       // 去除右侧空格
s.Split(',');                      // 按逗号分割 → ["  Hello", " World!  "]
s.Contains("World");               // 是否包含
s.StartsWith("Hello");             // 是否以...开头
s.EndsWith("!");                   // 是否以...结尾
s.Replace("World", "C#");          // 替换
s.Substring(0, 5);                 // 截取子串
string.IsNullOrEmpty(s);           // 判断空
string.Join(",", array);           // 数组合并为字符串
```

## A.4 DateTime 常用操作

```csharp
DateTime now = DateTime.Now;                    // 当前时间
DateTime today = DateTime.Today;                // 今天（时间归零）
DateTime utc = DateTime.UtcNow;                 // UTC 时间

// 格式化
now.ToString("yyyy-MM-dd HH:mm:ss");            // 2025-07-29 14:30:00
now.ToString("G");                              // 通用格式
now.ToString("yyyy年MM月dd日");                 // 2025年07月29日

// 时间计算
TimeSpan diff = end - start;                    // 时间差
diff.TotalMinutes;                              // 总分钟数（double）
diff.TotalSeconds;                              // 总秒数
diff.Days;                                      // 天数部分
diff.Hours;                                     // 小时部分

// 构造指定时间
new DateTime(2025, 7, 29, 14, 30, 0);          // 年,月,日,时,分,秒
```

## A.5 异常处理

```csharp
try
{
    // 可能出错的代码
    int num = int.Parse(Console.ReadLine());
}
catch (FormatException)
{
    // 格式错误（输入的不是数字）
    Console.WriteLine("请输入有效数字");
}
catch (OverflowException)
{
    // 数值溢出
    Console.WriteLine("数值过大");
}
catch (Exception ex)
{
    // 捕获所有其他异常
    Console.WriteLine($"发生错误：{ex.Message}");
}
finally
{
    // 无论是否异常都会执行
    Console.WriteLine("清理资源");
}
```

> 💡 **推荐**：文件操作使用 `using` 语句代替 `try-finally`：

```csharp
// ✅ using 自动释放资源（推荐）
using (FileStream fs = new FileStream(path, FileMode.Open))
using (StreamReader sr = new StreamReader(fs))
{
    string content = sr.ReadToEnd();
}  // 离开作用域时自动关闭

// ❌ 手动关闭（容易遗漏）
FileStream fs = new FileStream(path, FileMode.Open);
try
{
    StreamReader sr = new StreamReader(fs);
    // ...
    sr.Close();
    fs.Close();
}
finally
{
    fs.Close();
}
```

---

## 相关笔记

- [[01-基础语法/C#基础]] — 基础语法与程序结构
- [[01-基础语法/C#数据类型]] — 数据类型
- [[01-基础语法/运算符]] — 运算符与表达式
- [[02-流程控制/流程控制]] — 流程控制（if / switch / goto）
- [[02-流程控制/循环结构]] — 循环结构
- [[03-数组/数组基础]] — 数组基础
- [[04-委托与Lambda/Array数组方法]] — Array 数组方法（Clear/Copy/Find）
- [[04-委托与Lambda/委托与Lambda]] — 委托与 Lambda
- [[04-委托与Lambda/事件]] — 事件
- [[05-集合/集合与泛型]] — 集合与泛型
- [[05-集合/泛型]] — 泛型（方法/类/约束）
- [[06-字符串/String常用方法]] — 字符串
- [[07-方法/方法(函数)]] — 方法
- [[08-面向对象/类与对象/基础]] — 面向对象基础
- [[08-面向对象/类与对象/继承]] — 继承
- [[08-面向对象/类与对象/抽象类]] — 抽象类
- [[08-面向对象/类与对象/接口]] — 接口
- [[09-练习与项目/停车场管理系统]] — 停车场项目
- [[10-IO操作/文件IO概览]] — 文件 IO
