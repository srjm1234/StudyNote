---
title: C# 第二阶段学习笔记 — WinForm 与文件操作
date: 2026-08-10
tags:
  - CSharp
  - 总览
  - WinForms
  - MOC
---

# C# 第二阶段学习笔记 — WinForm 与文件操作

> 整理自 `D:\study\chuanding\课程\` 下三个课程目录的源码，覆盖 **8.3号 基础复习** → **8.4号 WinForm 入门** → **8.10号 文件操作** 的完整学习路径。本文为**总览（MOC）**，WinForm 详细分类笔记见 [[Winform/00-WinForms学习笔记总览]]。

---

# 目录

- [一、8.3号 基础复习](#一八3号-基础复习)
  - [1.1 字典（Dictionary）](#11-字典dictionary)
  - [1.2 接口（Interface）](#12-接口interface)
  - [1.3 委托（Delegate）](#13-委托delegate)
  - [1.4 事件（Event）](#14-事件event)
  - [1.5 属性与字段](#15-属性与字段)
  - [1.6 方法重载与泛型](#16-方法重载与泛型)
  - [1.7 类库（Class Library）](#17-类库class-library)
  - [1.8 枚举（Enum）](#18-枚举enum)
  - [1.9 创建文件与写入数据](#19-创建文件与写入数据)
- [二、8.4号 WinForm 基础](#二八4号-winform-基础)
  - [2.1 WinForm 项目结构与部分类](#21-winform-项目结构与部分类)
  - [2.2 WinForm 初始化与 Graphics 绘图](#22-winform-初始化与-graphics-绘图)
  - [2.3 WinForm 事件机制](#23-winform-事件机制)
  - [2.4 事件的联动响应（sender）](#24-事件的联动响应sender)
  - [2.5 键盘事件（KeyUp / KeyPress）](#25-键盘事件keyup--keypress)
  - [2.6 消息提示框（MessageBox）](#26-消息提示框messagebox)
  - [2.7 手动添加控件](#27-手动添加控件)
- [三、8.10号 文件操作深入](#三八10号-文件操作深入)
  - [3.1 文件与目录操作（File / Directory）](#31-文件与目录操作file--directory)
  - [3.2 文件路径操作（Path 类）](#32-文件路径操作path-类)
  - [3.3 对话框（OpenFileDialog / SaveFileDialog）](#33-对话框openfiledialog--savedfiledialog)
  - [3.4 对象序列化与反序列化](#34-对象序列化与反序列化)
  - [3.5 动态创建图片展示控件](#35-动态创建图片展示控件)

---

# 一、8.3号 基础复习

## 1.1 字典（Dictionary）

> **来源**：`8.3号_复习/01-字典/`

`Dictionary<TKey, TValue>` 是键值对集合，通过**键**快速查找对应的**值**。

### 基本操作

```csharp
// 创建字典：key 类型 → value 类型
Dictionary<string, Car> cards = new Dictionary<string, Car>();

// 添加元素
cards.Add("豫A.88888", new Car() { Name = "豫A.88888", JinTime = DateTime.Now });
cards.Add("豫A.22222", new Car() { Name = "豫A.22222", JinTime = DateTime.Now });

// 通过键访问值
cards["豫A.88888"].LiTimae = DateTime.Now;

// 判断是否包含某个键
bool exists = cards.ContainsKey("豫A.88888");

// 删除某个键值对
cards.Remove("豫A.88888");

// 遍历所有键
foreach (string key in cards.Keys) { }

// 遍历所有值
foreach (Car car in cards.Values) { }
```

### 嵌套字典

```csharp
// 字典的 value 也可以是另一个字典
Dictionary<string, Dictionary<string, DateTime>> card =
    new Dictionary<string, Dictionary<string, DateTime>>();

card.Add("豫A.88888", new Dictionary<string, DateTime>()
{
    { "豫A.88888", DateTime.Now }
});

// 访问：card["豫A.88888"]["豫A.88888"]
```

### 实战示例：停车场管理

```csharp
// 车牌号 → Car 对象的映射
Dictionary<string, Car> parking = new Dictionary<string, Car>();

// 入场：添加
parking.Add("豫A.88888", new Car { Name = "豫A.88888", JinTime = DateTime.Now });

// 离场：通过车牌查找 → 更新离场时间 → 计算时长 → 移除
Car car = parking["豫A.88888"];
car.LiTimae = DateTime.Now;
TimeSpan duration = car.LiTimae - car.JinTime;
parking.Remove("豫A.88888");
```

### 数据模型（Car 类）

```csharp
class Car
{
    public string Name { get; set; }
    public DateTime JinTime { get; set; }    // 入场时间
    public DateTime LiTimae { get; set; }    // 离场时间
    public TimeSpan Time                     // 计算属性：停车时长
    {
        get { return LiTimae - JinTime; }
    }
}
```

> 💡 **要点**：`Dictionary` 的 key 必须唯一，重复添加会抛异常。用 `ContainsKey()` 先判断。

### 写入文件

```csharp
List<Car> listCard = new List<Car>();
listCard.Add(cards["豫A.88888"]);
listCard.Add(cards["豫A.22222"]);

// 将列表写入文件
WriteCard(listCard);

public static void WriteCard(List<Car> cars)
{
    FileStream fs = new FileStream(@"./card.txt", FileMode.Create, FileAccess.Write);
    StreamWriter sw = new StreamWriter(fs);
    foreach (var item in cars)
    {
        string str = $"车牌号：{item.Name}停放了{item.JinTime},{item.LiTimae}离场,总共停了{item.Time}";
        sw.WriteLine(str);
    }
    sw.Close();
    fs.Close();
}
```

## 1.2 接口（Interface）

> **来源**：`8.3号_复习/02-继承/`

### 接口的定义与实现

```csharp
// 1. 接口定义：interface 关键字
// 命名规范：以大写字母 I 开头
// 接口成员只有声明，没有实现
interface IBook
{
    // 属性声明
    int Name { get; set; }
    // 方法声明（没有方法体）
    void Fn();
}

// 2. 类实现接口：用 : 连接
class Book : IBook
{
    // 必须实现接口中的所有成员
    public int Name { get; set; }
    public string Title { get; set; }

    public void Fn()
    {
        // 方法体
    }
}
```

### 接口的特点

| 特性 | 说明 |
|------|------|
| **命名规范** | 以 `I` 开头（如 `IBook`、`IFlyable`） |
| **成员** | 只有方法签名和属性声明，不能有字段和构造函数 |
| **实现** | 类用 `:` 实现接口，必须实现接口的所有成员 |
| **多实现** | 一个类可以实现多个接口（用逗号分隔） |
| **抽象 vs 接口** | 抽象类可以有字段和部分实现；接口只有约定 |

### 接口 vs 抽象类

```
抽象类（abstract class）：有字段、有方法实现，单继承
接口（interface）：无字段、只有约定，多实现

class Bird : Animal, IFlyable, ISwimmable  // ✅ 一个类 + 一个父类 + 多个接口
```

## 1.3 委托（Delegate）

> **来源**：`8.3号_复习/03-委托/`

### 委托的本质

**委托 = 函数的数据类型**

```csharp
// 普通变量存数据
int a = 5;

// 委托变量存函数
Action fn = Fn1;   // fn 里面存了 Fn1 这个方法
fn();              // 调用委托 = 调用 Fn1()
```

### 声明委托

```csharp
// 语法：访问修饰符 delegate 返回值类型 委托名(参数列表)
public delegate void MyAction();                    // 无参无返回值
public delegate void MyAction<T>(T obj);            // 泛型委托
public delegate TResult MyFunc<TResult>();          // 有返回值
public delegate TResult MyFunc<T, TResult>(T obj);  // 泛型 + 有返回值
```

### Action 与 Func（内置委托）

| 内置委托 | 返回值 | 参数 | 示例 |
|----------|--------|------|------|
| `Action` | 无 (`void`) | 0 个 | `Action act = () => Console.WriteLine("Hi");` |
| `Action<T>` | 无 | 1 个 | `Action<int> act = x => Console.WriteLine(x);` |
| `Action<T1, T2>` | 无 | 2 个 | `Action<string, int> act = (s, n) => { };` |
| `Func<TResult>` | 有返回值 | 0 个 | `Func<int> fn = () => 42;` |
| `Func<T1, TResult>` | 有返回值 | 1 个 | `Func<string, int> fn = s => s.Length;` |

```csharp
// 使用 Action 和 Func
Action fn1 = Fn1;              // 绑定无参方法
fn1();                         // 调用

Action<string> fn2 = Fn2;      // 绑定带 string 参数的方法
fn2("Hello");                  // 调用

Func<int> fn3 = Fn3;           // 绑定有返回值的方法
int result = fn3();            // → 0

Func<string, int> fn4 = Fn4;   // 带参数 + 返回值
int result2 = fn4("Hello");    // → 0
```

### Lambda 表达式

```csharp
// Lambda 格式：(参数列表) => { 函数体 }

// 无参
Action fn1 = () => { Console.WriteLine("Lambda 被调用"); };

// 有参
Action<int> fn2 = (int v) => { Console.WriteLine("参数值：" + v); };

// 有返回值
Func<int> fn3 = () => { Console.WriteLine("有返回值"); return 0; };
Func<int, bool> fn4 = (int v) => { Console.WriteLine(v); return false; };
```

### 多播委托

一个委托变量可以保存多个方法，按添加顺序依次执行。

```csharp
// 多播委托
MyAction action = () => { Console.WriteLine("Action1"); };
action += () => { Console.WriteLine("Action2"); };   // 添加
action += () => { Console.WriteLine("Action3"); };   // 添加

action();  // 依次执行：Action1 → Action2 → Action3

action -= () => { Console.WriteLine("Action2"); };   // 移除
action();  // 执行：Action1 → Action3

action = null;  // 清空所有订阅者
```

> 💡 **多播委托执行顺序**：按照 `+=` 的**添加顺序**依次执行。用 `-=` 移除指定方法。

## 1.4 事件（Event）

> **来源**：`8.3号_复习/04-事件/`

### 为什么需要事件？

委托可以被外部直接调用，缺乏安全控制。事件给委托加了一层"锁"。

```csharp
class Test
{
    // 普通委托字段：外部可以任意调用
    public MyAction myAction;
    // test.myAction();  // ❌ 外部可以直接调用

    // 事件：外部只能 += / -=，不能直接调用
    public event MyAction MyEvent;
    // test.MyEvent();   // ❌ 编译错误！
    // test.MyEvent += handler;  // ✅ 允许
    // test.MyEvent -= handler;  // ✅ 允许
}
```

### 事件的完整模式

```csharp
class Test
{
    // 1. 声明委托
    public delegate void MyAction();

    // 2. 声明事件
    public event MyAction MyEvent;

    // 3. 触发事件的方法（只能在类内部触发）
    public void Fn()
    {
        MyEvent?.Invoke();  // ?. 安全调用（无订阅者时不报错）
    }
}

// 使用
Test test = new Test();

// 订阅事件
test.MyEvent += () => { Console.WriteLine("A事件被触发了"); };
test.MyEvent += () => { Console.WriteLine("B事件被触发了"); };
test.MyEvent += () => { Console.WriteLine("C事件被触发了"); };

// 触发事件（必须在发布者内部）
test.Fn();
// 输出：
// A事件被触发了
// B事件被触发了
// C事件被触发了
```

### 委托 vs 事件对比

| 操作 | 普通委托字段 | `event` 委托 |
|------|------------|-------------|
| 外部订阅 `+=` | ✅ 允许 | ✅ 允许 |
| 外部取消 `-=` | ✅ 允许 | ✅ 允许 |
| 外部直接调用 `()` | ✅ 允许 ❌ | ❌ 禁止 ✅ |
| 类内部调用 | ✅ 允许 | ✅ 允许 |

> 💡 **一句话**：事件就是"只能由发布者触发、外部只能订阅的委托"。

## 1.5 属性与字段

> **来源**：`8.3号_复习/05-属性和字段/`

### 字段（Field）

```csharp
// 字段写法：访问修饰符 数据类型 字段名;
// 命名规范：小驼峰（camelCase）
private int age;      // 私有字段，外部不能直接访问
private int count;
```

### 属性（Property）

```csharp
// 属性写法：访问修饰符 数据类型 属性名 { get; set; }
// 命名规范：大驼峰（PascalCase）
public string Name { get; set; }          // 自动属性（编译器生成私有字段）

// 完整写法（带逻辑控制）
public int Count
{
    get { return count; }                 // 读取时执行
}
// public int Age
// {
//     get { return age; }                 // 读取
//     set { age = value; }                // 写入（value 是自动参数）
// }
```

### 自动属性 vs 完整属性

```csharp
// 自动属性（简单场景，无需验证）
public string Name { get; set; }

// 只读属性（只有 getter）
public int Count { get { return count; } }

// 完整属性（需要验证逻辑）
private int _age;
public int Age
{
    get { return _age; }
    set
    {
        if (value >= 0 && value <= 150)
            _age = value;
        else
            throw new ArgumentOutOfRangeException("年龄不合法");
    }
}
```

### 字段与属性的命名规范

| 类型 | 命名 | 示例 |
|------|------|------|
| 字段（Field） | 小驼峰 / 下划线+小驼峰 | `age`、`_age` |
| 属性（Property） | 大驼峰 | `Age`、`Name` |

## 1.6 方法重载与泛型

> **来源**：`8.3号_复习/06-方法的重载/`

### 方法重载（Overload）

同一个类中，方法名相同，**参数列表不同**。

```csharp
// 方法重载：同名不同参
public static void WriteArr(int[] arr) { }
public static void WriteArr(string[] arr) { }
public static void WriteArr(double[] arr) { }

// 调用时编译器自动匹配
WriteArr(new int[] { 1, 2, 3 });
WriteArr(new string[] { "a", "b", "c" });
```

### 泛型方法

```csharp
// 用 <T> 声明泛型参数，一套方法支持多种类型
public static void Write<T>(T[] arr) { }

Write(new int[] { 1, 2, 3 });       // T = int
Write(new string[] { "a", "b", "c" }); // T = string
Write<string>(new string[] { "a", "b", "c" }); // 显式指定类型
```

### 泛型接口

```csharp
// 泛型接口
public interface IBook<T>
{
    T Value { get; set; }
}

// 实现时指定具体类型
class Book : IBook<string>
{
    public string Value { get; set; }
}

// 泛型接口继承
interface IA<TA> { TA V { get; set; } }
interface IB<TB> : IA<TB> { TB V2 { get; set; } }

class A : IB<int>
{
    public int V { get; set; }
    public int V2 { get; set; }
}
```

### 泛型类

```csharp
// 泛型类：多个类型参数
class Test<T1, T2>
{
    public T1 Value { get; set; }
    public T2 Value2 { get; set; }
}

// 使用
Test<string, int> t = new Test<string, int>();
t.Value = "123";
t.Value2 = 123;
```

## 1.7 类库（Class Library）

> **来源**：`8.3号_复习/07-类库/`

### 什么是类库

**类库**就是一个 `.dll` 文件，里面包含了多个可复用的类。其他项目可以**引用**这个类库来使用其中的类和方法。

### 创建与引用步骤

```
1. 在解决方案中 → 添加 → 新建项目 → 选择"类库"
2. 编写类和方法（需要 public 才能被外部访问）
3. 在需要使用的项目中 → 引用 → 添加引用 → 选择类库项目
4. 在代码中用 using 导入命名空间
```

### 示例

**类库项目**（ClassLibrary）：

```csharp
namespace ClassLibrary
{
    public class MyArray
    {
        public static void Fn()
        {
            Console.WriteLine("我是MyArray类中的方法");
        }
    }

    public class MyString
    {
        public static void Fn()
        {
            Console.WriteLine("我是MyString类中的方法");
        }
    }
}
```

**调用项目**：

```csharp
using ClassLibrary;  // 导入类库的命名空间

class Program
{
    static void Main(string[] args)
    {
        MyArray.Fn();    // → 我是MyArray类中的方法
        MyString.Fn();   // → 我是MyString类中的方法
    }
}
```

> 💡 **要点**：类库中的类和方法必须用 `public` 修饰，否则外部项目无法访问。

## 1.8 枚举（Enum）

> **来源**：`8.3号_复习/08-枚举/`

### 枚举的定义

**枚举**是一种值类型，限定变量只能从预定义的几个值中选择。

```csharp
// 声明格式：访问修饰符 enum 枚举名 { 值1, 值2, ... }
public enum Week
{
    Monday,           // 默认从 0 开始
    Tuesday,          // 1
    Wednesday = 20,   // 自定义值
    Thursday = 23,    // 自定义值
    Friday,           // 24（自动 +1）
    Saturday = 10,    // 自定义值
    Sunday,           // 11（自动 +1）
    March,            // 12
}
```

### 枚举的使用

```csharp
// 创建枚举变量
Week day = Week.Monday;
Console.WriteLine(day);           // → Monday（字符串）
Console.WriteLine((int)day);      // → 0（转换为 int）
Console.WriteLine(day.ToString()); // → "Monday"

// int 转枚举
Week w2 = (Week)10;               // → Saturday
Console.WriteLine(w2);

// 中文枚举
public enum Sex { 男, 女 }
class Test
{
    public Sex Gender { get; set; }
}
Test t = new Test();
t.Gender = Sex.男;
```

> 💡 **要点**：枚举默认从 `0` 开始递增，可以手动指定某个值，后续未指定的会在此基础上递增。

## 1.9 创建文件与写入数据

> **来源**：`8.3号_复习/09-创建文件和写入数据/`

### File 类操作

```csharp
// 1. 创建文件（返回 FileStream，需关闭）
File.Create(@"./test.txt");

// 2. 写入文本（覆盖）
File.WriteAllText(@"./test.txt", "这是要写入的内容");

// 3. 追加文本
File.AppendAllText(@"./test.txt", "追加的内容\r\n");

// 4. 写入多行（覆盖）
File.WriteAllLines(@"./test.txt", new string[] { "第1行", "第2行", "第3行" });

// 5. 读取全部文本
string content = File.ReadAllText(@"./test.txt");

// 6. 逐行读取
string[] lines = File.ReadAllLines(@"./test.txt");
```

### FileInfo 类操作

```csharp
// FileInfo 是实例类，适合对同一个文件做多次操作
FileInfo fileInfo = new FileInfo(@"./test.txt");

// 创建文件
fileInfo.Create();

// 复制文件（true 表示覆盖目标文件）
fileInfo.CopyTo(@"D:\backup.txt", true);

// 移动/重命名
fileInfo.MoveTo(@"D:\new.txt");

// 删除
fileInfo.Delete();
```

### StreamWriter / StreamReader 读写

```csharp
// === 写入（using 自动释放资源）===
using (FileStream fs = new FileStream(@"./test.txt", FileMode.Open, FileAccess.Read))
using (StreamReader sr = new StreamReader(fs, Encoding.UTF8))
{
    Console.WriteLine(sr.ReadToEnd());
}
```

> 💡 **推荐**：使用 `using` 语句替代手动 `Close()`，确保资源自动释放。

---

# 二、8.4号 WinForm 基础

## 2.1 WinForm 项目结构与部分类

> **来源**：`8.4_WinForm初始_事件 处理机制_消息提示/01-WinForm初始/`

### 项目文件结构

```
WinForm 项目/
├── Program.cs           # 程序入口
├── Form1.cs             # 窗体逻辑代码（手动编写）
├── Form1.Designer.cs    # 窗体设计器代码（自动生成）
├── Form1.resx           # 窗体资源文件
└── Properties/
    └── AssemblyInfo.cs  # 程序集信息
```

### 部分类（partial class）

同一个类可以拆分成多个文件定义，用 `partial` 关键字标记。

```csharp
// FrmMain.cs — 手动编写的部分
public partial class FrmMain : Form
{
    public FrmMain()
    {
        InitializeComponent();
        SetTitleCenter();
    }

    private void SetTitleCenter()
    {
        // 自定义逻辑...
    }
}

// FrmMain.Designer.cs — 设计器自动生成的部分
partial class FrmMain
{
    private System.ComponentModel.IContainer components = null;
    private void InitializeComponent()
    {
        // 控件初始化代码...
    }
    private Button btn1;
    private ComboBox comboBox1;
    private Label label1;
    private PictureBox pictureBox1;
}
```

> 💡 **要点**：`InitializeComponent()` 在 `Designer.cs` 中定义，用于初始化窗体上的所有控件。通常不需要手动修改 `Designer.cs`。

### Program 入口

```csharp
[STAThread]
static void Main()
{
    Application.EnableVisualStyles();              // 启用视觉样式
    Application.SetCompatibleTextRenderingDefault(false); // 禁用兼容模式
    Application.Run(new FrmMain());                // 运行主窗体
}
```

## 2.2 WinForm 初始化与 Graphics 绘图

> **来源**：`8.4_WinForm初始_事件 处理机制_消息提示/01-WinForm初始/FrmMain.cs`

### 窗体常用属性

| 属性 | 说明 | 示例 |
|------|------|------|
| `Text` | 窗体标题 | `this.Text = "我的程序";` |
| `Size` | 窗体大小 | `this.Size = new Size(800, 600);` |
| `Location` | 窗体位置 | `this.Location = new Point(100, 100);` |
| `Controls` | 控件集合 | `this.Controls.Add(btn1);` |
| `Icon` | 窗体图标 | 在资源文件中设置 |

### Graphics 对象 — 在窗体上绘图

```csharp
// 1. 获取 Graphics 对象
Graphics g = this.CreateGraphics();

// 2. 测量字符串宽度
Double startingPoint = (this.Width / 2) - (g.MeasureString(titleMsg, this.Font).Width / 2);

// 3. 计算空格宽度
Double widthOfASpace = g.MeasureString(" ", this.Font).Width;

// 4. 用空格填充使标题居中
String tmp = " ";
Double tmpWidth = 0;
while ((tmpWidth + widthOfASpace) < startingPoint)
{
    tmp += " ";
    tmpWidth += widthOfASpace;
}
this.Text = tmp + titleMsg;
```

> 💡 **应用场景**：动态计算字符串在窗体上的居中位置、自定义绘制控件内容。

## 2.3 WinForm 事件机制

> **来源**：`8.4_WinForm初始_事件 处理机制_消息提示/02-事件的使用/`

### 事件的订阅与取消

```csharp
// 订阅事件（绑定处理方法）
btnTest.Click += new EventHandler(btnTest_Click);

// 取消订阅（解除绑定）
btnTest.Click -= new EventHandler(btnTest_Click);
```

### 完整示例

```csharp
public partial class Form1 : Form
{
    public Form1()
    {
        InitializeComponent();
    }

    // 按钮点击事件处理方法
    private void btnTest_Click(object sender, EventArgs e)
    {
        MessageBox.Show("按钮被点击了");
    }

    // 取消绑定按钮
    private void button1_Click(object sender, EventArgs e)
    {
        this.btnTest.Click -= new EventHandler(this.btnTest_Click);
    }

    // 重新绑定按钮
    private void button2_Click(object sender, EventArgs e)
    {
        this.btnTest.Click += new EventHandler(this.btnTest_Click);
    }
}
```

### 设计器中事件绑定

```csharp
// Designer.cs 中自动生成的绑定代码
this.btnTest.Click += new System.EventHandler(this.btnTest_Click);
```

> 💡 **要点**：`sender` 是触发事件的对象，`EventArgs e` 包含事件的相关数据。

## 2.4 事件的联动响应（sender）

> **来源**：`8.4_WinForm初始_事件 处理机制_消息提示/03-事件的联动响应/`

### 多个控件共享一个事件处理方法

```csharp
public partial class Form1 : Form
{
    public Form1()
    {
        InitializeComponent();
    }

    // 多个按钮共用同一个点击事件
    private void BtnTest_Click(object sender, EventArgs e)
    {
        // sender：触发事件的对象（object 类型）
        // 需要强制转换为具体类型才能访问其属性

        Button btn = (Button)sender;  // 将 sender 转为 Button

        label1.Text = btn.Text;               // 显示按钮文字
        label1.BackColor = btn.BackColor;     // 显示按钮背景色
        MessageBox.Show(btn.Text);            // 弹出按钮文字
    }
}
```

### 设计器中的绑定

```csharp
// 三个按钮都绑定到同一个事件处理方法
this.button1.Click += new EventHandler(this.BtnTest_Click);  // "Andy老师" 红色
this.button2.Click += new EventHandler(this.BtnTest_Click);  // "Carry老师" 绿色
this.button3.Click += new EventHandler(this.BtnTest_Click);  // "Coco老师" 蓝色
```

### sender 的工作原理

```
用户点击 button1（文字="Andy老师"，背景=红色）
         ↓
触发 BtnTest_Click 事件
         ↓
sender 就是 button1 这个对象
         ↓
((Button)sender).Text     → "Andy老师"
((Button)sender).BackColor → Color.Red
```

> 💡 **要点**：`sender` 是 `object` 类型（所有类型的基类），必须强制转换才能访问具体控件的属性。

## 2.5 键盘事件（KeyUp / KeyPress）

> **来源**：`8.4_WinForm初始_事件 处理机制_消息提示/04-练习/`

### KeyUp 事件 — 键盘按下后松开触发

```csharp
// 在 Designer.cs 中绑定
this.box.KeyUp += new KeyEventHandler(this.box_KeyUp);

// 事件处理方法
private void box_KeyUp(object sender, KeyEventArgs e)
{
    switch (e.KeyCode)  // 按下的键
    {
        case Keys.Left:
            box.Location = new Point(x -= boxWidth, y);
            break;
        case Keys.Right:
            box.Location = new Point(x += boxWidth, y);
            break;
        case Keys.Up:
            box.Location = new Point(x, y -= boxHeight);
            break;
        case Keys.Down:
            box.Location = new Point(x, y += boxHeight);
            break;
    }
}
```

### KeyPress 事件 — 按键字符输入触发

```csharp
// KeyPressEventArgs 包含按键对应的字符
private void textBox1_KeyPress(object sender, KeyPressEventArgs e)
{
    Console.WriteLine("按键被按下");
    // e.KeyChar 获取按下的字符
}
```

### 键盘事件对比

| 事件 | 参数类型 | 获取内容 | 触发时机 |
|------|---------|---------|---------|
| `KeyUp` | `KeyEventArgs` | `e.KeyCode`（按键代码） | 按键**松开**后 |
| `KeyPress` | `KeyPressEventArgs` | `e.KeyChar`（字符） | 字符输入时 |
| `KeyDown` | `KeyEventArgs` | `e.KeyCode` | 按键**按下**时 |

### 实战：方向键控制方块移动

```csharp
public partial class Form1 : Form
{
    int x = 0, y = 0;           // 当前坐标
    int boxWidth, boxHeight;     // 方块大小
    int pW, pH;                  // 面板大小

    public Form1()
    {
        InitializeComponent();
        boxWidth = box.Size.Width;
        boxHeight = box.Size.Height;
        pW = panel1.Size.Width;
        pH = panel1.Size.Height;
    }

    private void box_KeyUp(object sender, KeyEventArgs e)
    {
        switch (e.KeyCode)
        {
            case Keys.Left:
                if (x > 0)
                    box.Location = new Point(x -= boxWidth, y);
                break;
            case Keys.Right:
                if (x < pW - boxWidth)
                    box.Location = new Point(x += boxWidth, y);
                break;
            case Keys.Up:
                if (y > 0)
                    box.Location = new Point(x, y -= boxHeight);
                break;
            case Keys.Down:
                if (y < pH - boxHeight)
                    box.Location = new Point(x, y += boxHeight);
                break;
        }
    }
}
```

### 窗体生命周期事件

```csharp
// 窗体加载事件（窗体首次显示时）
private void Form1_Load(object sender, EventArgs e)
{
    MessageBox.Show("窗体加载完成");
}

// 窗体关闭前事件（可以阻止关闭）
private void Form1_FormClosing(object sender, FormClosingEventArgs e)
{
    // e.Cancel = true;  // 取消关闭
    MessageBox.Show("窗体正在关闭...");
}

// 窗体关闭后事件
private void Form1_FormClosed(object sender, FormClosedEventArgs e)
{
    MessageBox.Show("窗体已关闭");
}
```

## 2.6 消息提示框（MessageBox）

> **来源**：`8.4_WinForm初始_事件 处理机制_消息提示/06-消息提示框/`

### MessageBox.Show 基础

```csharp
// 基本用法
MessageBox.Show("提示内容");

// 带标题
MessageBox.Show("提示内容", "标题");

// 带按钮样式和图标
MessageBox.Show(
    "是否关闭？",               // 提示内容
    "警告",                      // 标题
    MessageBoxButtons.OKCancel,  // 按钮样式
    MessageBoxIcon.Asterisk,     // 图标样式
    MessageBoxDefaultButton.Button1  // 默认选中按钮
);
```

### MessageBoxButtons 按钮样式

| 值 | 显示的按钮 |
|----|-----------|
| `OK` | 确定 |
| `OKCancel` | 确定 + 取消 |
| `YesNo` | 是 + 否 |
| `YesNoCancel` | 是 + 否 + 取消 |
| `RetryCancel` | 重试 + 取消 |
| `AbortRetryIgnore` | 终止 + 重试 + 忽略 |

### MessageBoxIcon 图标样式

| 值 | 图标 |
|----|------|
| `None` | 无图标 |
| `Information` | 信息 ℹ |
| `Warning` | 警告 ⚠ |
| `Error` | 错误 ❌ |
| `Question` | 问号 ❓ |
| `Asterisk` | 星标 ℹ |

### 获取用户选择结果

```csharp
DialogResult res = MessageBox.Show(
    "是否关闭？", "警告",
    MessageBoxButtons.OKCancel,
    MessageBoxIcon.Asterisk);

if (res == DialogResult.OK)
{
    this.Close();  // 用户点了确定
}
```

### 阻止窗体关闭

```csharp
private void Form1_FormClosing(object sender, FormClosingEventArgs e)
{
    DialogResult res = MessageBox.Show(
        "是否关闭？", "警告",
        MessageBoxButtons.OKCancel,
        MessageBoxIcon.Asterisk);

    if (res == DialogResult.Cancel)
    {
        e.Cancel = true;  // 取消关闭操作
    }
}
```

> 💡 **要点**：`FormClosing` 事件中设置 `e.Cancel = true` 可以阻止窗体关闭。`FormClosed` 在窗体关闭后触发，无法阻止。

## 2.7 手动添加控件

> **来源**：`8.4_WinForm初始_事件 处理机制_消息提示/06-手动添加控件/`

### 动态创建控件的步骤

```csharp
private void Form1_Load(object sender, EventArgs e)
{
    // 1. 创建控件实例
    Button btn1 = new Button();
    btn1.Text = "OK";
    btn1.Location = new Point(100, 200);

    // 2. 绑定事件
    btn1.Click += Btn1_Click;

    // 3. 添加到窗体的 Controls 集合中
    this.Controls.Add(btn1);

    // === 添加到 Panel 中 ===
    Panel panel1 = new Panel();
    panel1.Width = 300;
    panel1.Height = 300;
    panel1.Location = new Point(20, 20);
    panel1.BorderStyle = BorderStyle.FixedSingle;
    this.Controls.Add(panel1);

    // 按钮添加到 Panel 而不是窗体
    panel1.Controls.Add(btn1);
}

private void Btn1_Click(object sender, EventArgs e)
{
    MessageBox.Show("动态按钮被点击");
}
```

### 控件容器层级

```
Form（窗体）
├── Controls[0] → Panel
│   └── Controls[0] → Button（btn1）
└── Controls[1] → Button（btn2）
```

> 💡 **要点**：控件必须添加到某个容器的 `Controls` 集合中才能显示。`this.Controls.Add()` 添加到窗体。

---

# 三、8.10号 文件操作深入

## 3.1 文件与目录操作（File / Directory）

> **来源**：`8.10_文件操作/01-文件和目录操作/`

### File 类 — 静态方法，文件操作

```csharp
// 判断文件是否存在
if (File.Exists(path)) { }

// 删除文件
File.Delete(path);

// 复制文件
if (File.Exists(sourcePath))
{
    File.Copy(sourcePath, destPath);  // 默认不允许覆盖
    File.Copy(sourcePath, destPath, true);  // 允许覆盖
}

// 移动文件
if (File.Exists(sourcePath))
{
    File.Move(sourcePath, destPath);
}
```

### StreamWriter / StreamReader — 文本流读写

```csharp
// === 写入 ===
// 1. 创建 FileStream
FileStream fs = new FileStream(@"./my.txt", FileMode.Append, FileAccess.Write);
// 2. 创建 StreamWriter
StreamWriter sw = new StreamWriter(fs);
// 3. 写入数据
sw.WriteLine(textBox1.Text.Trim());  // Trim() 去除两端空格
// 4. 关闭（推荐用 using）
sw.Close();
fs.Close();

// === 读取 ===
// 1. 创建 FileStream
FileStream fs = new FileStream(@"./my.txt", FileMode.Open, FileAccess.Read);
// 2. 创建 StreamReader
StreamReader sr = new StreamReader(fs);
// 3. 读取数据
textBox1.Text = sr.ReadToEnd();     // 读取全部
// string line = sr.ReadLine();     // 读取一行
// 4. 关闭
sr.Close();
fs.Close();
```

> 💡 **推荐**：使用 `using` 语句自动释放资源：

```csharp
// ✅ using 自动关闭
using (FileStream fs = new FileStream(path, FileMode.Append, FileAccess.Write))
using (StreamWriter sw = new StreamWriter(fs))
{
    sw.WriteLine(content);
}  // 离开作用域自动关闭

// ❌ 手动关闭（容易遗漏）
FileStream fs = new FileStream(path, FileMode.Append, FileAccess.Write);
StreamWriter sw = new StreamWriter(fs);
sw.WriteLine(content);
sw.Close();
fs.Close();
```

### 日志追加示例

```csharp
// 追加日志到文件末尾
FileStream fs = new FileStream(@"./log.txt", FileMode.Append, FileAccess.Write);
StreamWriter sw = new StreamWriter(fs);
sw.WriteLine(DateTime.Now.ToString() + "[文件操作正常...]");
sw.Close();
fs.Close();
```

### Directory 类 — 目录操作

```csharp
// 获取指定目录下的所有文件
string[] files = Directory.GetFiles(@"D:\MyFolder");

// 获取所有子目录（递归遍历）
public void GetAllDirs(string path)
{
    string[] dirs = Directory.GetDirectories(path);
    foreach (string dir in dirs)
    {
        // 递归获取子目录
        if (Path.GetDirectoryName(dir).Length > 0)
        {
            GetAllDirs(dir);
        }
        Console.WriteLine(dir);
    }
}

// 创建目录
Directory.CreateDirectory(path);

// 删除目录
Directory.Delete(path);            // 删除空目录
Directory.Delete(path, true);       // 删除非空目录（递归）

// DirectoryInfo 方式删除
DirectoryInfo dir = new DirectoryInfo(path);
dir.Delete(true);  // true 表示递归删除
```

### DirectoryInfo 类

```csharp
// DirectoryInfo 是实例类，适合多次操作同一目录
DirectoryInfo dir = new DirectoryInfo(path);

Console.WriteLine("名称：" + dir.Name);
Console.WriteLine("完整路径：" + dir.FullName);
Console.WriteLine("创建时间：" + dir.CreationTime);
Console.WriteLine("访问时间：" + dir.LastAccessTime);
Console.WriteLine("写入时间：" + dir.LastWriteTime);
```

## 3.2 文件路径操作（Path 类）

> **来源**：`8.10_文件操作/02-文件路径/`

### 相对路径 vs 绝对路径

```
绝对路径：从盘符开始的完整路径
D:\桌面\课件\8.10号\02-文件路径\1.jpeg

相对路径：相对于当前运行目录（bin\Debug）
./1.txt        → bin\Debug\1.txt
../1.txt       → bin\1.txt
../../1.txt    → 项目根目录\1.txt
```

### Path 类常用方法

```csharp
string dirPath = @"D:\桌面\课件\8.10号\02-文件路径";
string filePath = @"D:\桌面\课件\8.10号\02-文件路径\1.jpeg";

// 获取当前运行目录
Console.WriteLine(Environment.CurrentDirectory);

// 获取文件所在目录
Console.WriteLine(Path.GetDirectoryName(filePath));  // → D:\桌面\课件\8.10号\02-文件路径

// 获取文件扩展名
Console.WriteLine(Path.GetExtension(filePath));  // → .jpeg

// 获取文件名
Console.WriteLine(Path.GetFileName(filePath));   // → 1.jpeg

// 获取完整路径
Console.WriteLine(Path.GetFullPath(@"./1.txt")); // → 完整绝对路径

// 更改文件扩展名
Console.WriteLine(Path.ChangeExtension(filePath, "jpg"));  // → ...\1.jpg

// 组合路径（自动处理分隔符）
string newPath = Path.Combine(@"D:\", "yhh", "qqq");
// → D:\yhh\qqq

// 生成随机文件名
Console.WriteLine(Path.GetRandomFileName());
```

### Path.Combine 的优势

```csharp
// ❌ 手动拼接（不同系统分隔符不同，容易出错）
string path = @"D:\" + "yhh" + "\\" + "qqq";

// ✅ Path.Combine（自动处理）
string path = Path.Combine(@"D:\", "yhh", "qqq");
// 甚至可以传入数组
string path = Path.Combine(new string[] { @"D:\", "yhh", "qqq" });
```

## 3.3 对话框（OpenFileDialog / SaveFileDialog）

> **来源**：`8.10_文件操作/03-练习/`、`04-对话框/`

### OpenFileDialog — 打开文件对话框

```csharp
// 方式一：简单使用
OpenFileDialog ofd = new OpenFileDialog();
if (ofd.ShowDialog() == DialogResult.OK)
{
    string selectedFile = ofd.FileName;
    // 使用文件路径...
}

// 方式二：配置初始目录和筛选器
OpenFileDialog ofd = new OpenFileDialog();
ofd.InitialDirectory = @"D:\";             // 初始目录
ofd.Filter = "文本文件|*.txt|图片|*.jpg;*.png|所有文件|*.*";  // 文件筛选
ofd.Title = "选择文件";

if (ofd.ShowDialog() == DialogResult.OK)
{
    string filePath = ofd.FileName;
    // 处理文件...
}
```

### SaveFileDialog — 保存文件对话框

```csharp
SaveFileDialog sfd = new SaveFileDialog();
sfd.InitialDirectory = @"D:\";
sfd.Filter = "文本文件|*.txt";
sfd.Title = "保存文件";

if (sfd.ShowDialog() == DialogResult.OK)
{
    FileStream fs = new FileStream(sfd.FileName, FileMode.Create, FileAccess.Write);
    StreamWriter sw = new StreamWriter(fs);
    sw.WriteLine(textBox1.Text);
    sw.Close();
    fs.Close();
}
```

### Filter 格式说明

```
"文本文件|*.txt|图片|*.jpg;*.png|所有文件|*.*"
  ↑          ↑      ↑          ↑      ↑        ↑
  描述1      分隔符  扩展名1     描述2   扩展名2   描述3
```

### 图片浏览示例

```csharp
private void btnSelect_Click(object sender, EventArgs e)
{
    OpenFileDialog ofd = new OpenFileDialog();
    ofd.InitialDirectory = @"D:\Images";

    if (ofd.ShowDialog() == DialogResult.OK)
    {
        // 显示图片
        pictureBox1.ImageLocation = ofd.FileName;
    }
}
```

### 文本文件读取示例

```csharp
private void btnOpen_Click(object sender, EventArgs e)
{
    openFileDialog1.InitialDirectory = @"D:\";
    openFileDialog1.Filter = "文本文件|*.txt";
    openFileDialog1.Title = "选择文本文件";

    if (openFileDialog1.ShowDialog() == DialogResult.OK)
    {
        FileStream fs = new FileStream(openFileDialog1.FileName, FileMode.Open, FileAccess.Read);
        StreamReader sr = new StreamReader(fs);
        textBox1.Text = sr.ReadLine();
        sr.Close();
        fs.Close();
    }
}
```

## 3.4 对象序列化与反序列化

> **来源**：`8.10_文件操作/05-对象状态的保存/`

### 什么是序列化

**序列化**：将对象转换为二进制流，保存到文件或网络中。

**反序列化**：从文件或网络中读取二进制流，恢复为对象。

### 实现步骤

```csharp
using System.Runtime.Serialization.Formatters.Binary;

// 1. 标记类为可序列化
[Serializable]
class Student
{
    public string Name { get; set; }
    public string Age { get; set; }
    public string Sex { get; set; }
    public string Birthday { get; set; }
}

// 2. 序列化（对象 → 文件）
private void btnSave_Click(object sender, EventArgs e)
{
    Student student = new Student()
    {
        Name = textBox1.Text,
        Age = textBox2.Text,
        Sex = textBox3.Text,
        Birthday = textBox4.Text,
    };

    // 创建文件流
    FileStream fs = new FileStream(@"./student.obj", FileMode.Create);
    // 创建二进制序列化格式器
    BinaryFormatter bf = new BinaryFormatter();
    // 序列化
    bf.Serialize(fs, student);
    // 关闭流
    fs.Close();
}

// 3. 反序列化（文件 → 对象）
private void btnLoad_Click(object sender, EventArgs e)
{
    FileStream fs = new FileStream(@"./student.obj", FileMode.Open);
    BinaryFormatter bf = new BinaryFormatter();
    Student student = (Student)bf.Deserialize(fs);
    fs.Close();

    // 恢复数据到界面
    textBox1.Text = student.Name;
    textBox2.Text = student.Age;
    textBox3.Text = student.Sex;
    textBox4.Text = student.Birthday;
}
```

### 序列化 vs 文本存储对比

| 方式 | 文件格式 | 可读性 | 空间占用 | 类型安全 |
|------|---------|--------|---------|---------|
| 文本（StreamWriter） | 人类可读 | ✅ 好 | 较大 | ❌ 需手动解析 |
| 二进制（BinaryFormatter） | 二进制 | ❌ 不可读 | 较小 | ✅ 自动保持类型 |

> 💡 **注意**：`BinaryFormatter` 在 .NET Core / .NET 5+ 中已过时，但在 .NET Framework 4.7.2 中仍可用。新项目建议使用 `System.Text.Json` 进行序列化。

### 非序列化方式（对比示例）

```csharp
// ❌ 手动逐字段写入（不推荐，繁琐）
FileStream fs = new FileStream(@"./student.txt", FileMode.Create);
StreamWriter sw = new StreamWriter(fs);
sw.WriteLine(student.Name);
sw.WriteLine(student.Age);
sw.WriteLine(student.Sex);
sw.WriteLine(student.Birthday);
sw.Close();
fs.Close();

// 读取时也要逐行解析，字段顺序容易出错
```

## 3.5 动态创建图片展示控件

> **来源**：`8.10_文件操作/07-练习/`

### 动态创建 PictureBox

```csharp
private void Btn1_Click(object sender, EventArgs e)
{
    ShowImage();
}

public void ShowImage()
{
    int x = 0, y = 0;

    // 打开文件选择对话框
    OpenFileDialog ofd = new OpenFileDialog()
    {
        InitialDirectory = @"D:\Images"
    };

    if (ofd.ShowDialog() == DialogResult.OK)
    {
        // 获取选中文件的所在目录
        string path = "";
        for (int i = 0; i < ofd.FileName.Length - 5; i++)
        {
            path += ofd.FileName[i];
        }

        // 获取目录下所有图片文件
        string[] files = Directory.GetFiles(path);

        // 动态创建 PictureBox
        for (int i = 0; i < files.Length; i++)
        {
            panel1.Controls.Add(new PictureBox()
            {
                Width = 384,
                Height = 216,
                Location = new Point(i == 0 ? (x = 0) : (x += 384), y),
                ImageLocation = files[i],
                SizeMode = PictureBoxSizeMode.Zoom
            });
        }
    }
}
```

### PictureBox 关键属性

| 属性 | 说明 | 示例 |
|------|------|------|
| `ImageLocation` | 图片路径 | `pictureBox.ImageLocation = @"D:\img\1.jpg";` |
| `Image` | 图片对象 | `pictureBox.Image = Image.FromFile(path);` |
| `SizeMode` | 图片缩放模式 | `PictureBoxSizeMode.Zoom`（等比缩放） |
| `Width` / `Height` | 控件大小 | `Width = 384; Height = 216;` |
| `Location` | 控件位置 | `Location = new Point(100, 200);` |

### PictureBoxSizeMode 枚举

| 值 | 效果 |
|----|------|
| `Normal` | 原始大小，超出部分裁剪 |
| `StretchImage` | 拉伸填充整个控件 |
| `AutoSize` | 控件自动适应图片大小 |
| `CenterImage` | 图片居中，超出部分裁剪 |
| `Zoom` | 等比缩放，保持比例 |

---

# 附录：WinForm 常用控件速查

## A.1 常用控件

| 控件 | 类名 | 用途 |
|------|------|------|
| 按钮 | `Button` | 点击触发事件 |
| 标签 | `Label` | 显示文本 |
| 文本框 | `TextBox` | 输入/显示文本 |
| 组合框 | `ComboBox` | 下拉选择 |
| 图片框 | `PictureBox` | 显示图片 |
| 面板 | `Panel` | 容器，分组控件 |
| 复选框 | `CheckBox` | 多选 |
| 单选框 | `RadioButton` | 单选（通常用 GroupBox 分组） |
| 列表框 | `ListBox` | 列表选择 |
| 进度条 | `ProgressBar` | 显示进度 |

## A.2 常用事件

| 事件 | 触发时机 | 参数 |
|------|---------|------|
| `Click` | 鼠标点击 | `EventArgs` |
| `Load` | 窗体加载完成 | `EventArgs` |
| `FormClosing` | 窗体关闭前 | `FormClosingEventArgs`（可取消） |
| `FormClosed` | 窗体关闭后 | `FormClosedEventArgs` |
| `KeyUp` | 键盘松开 | `KeyEventArgs` |
| `KeyDown` | 键盘按下 | `KeyEventArgs` |
| `KeyPress` | 字符输入 | `KeyPressEventArgs` |
| `MouseEnter` | 鼠标进入控件 | `EventArgs` |
| `MouseLeave` | 鼠标离开控件 | `EventArgs` |

## A.3 常用 FileMode 值

| 值 | 说明 |
|----|------|
| `Create` | 创建新文件，存在则覆盖 |
| `CreateNew` | 创建新文件，存在则抛异常 |
| `Open` | 打开已有文件，不存在则抛异常 |
| `OpenOrCreate` | 打开已有文件，不存在则创建 |
| `Append` | 追加模式，打开文件并将指针移到末尾 |
| `Truncate` | 打开已有文件并清空内容 |

## A.4 常用 FileAccess 值

| 值 | 说明 |
|----|------|
| `Read` | 只读 |
| `Write` | 只写 |
| `ReadWrite` | 读写 |

---

## 相关笔记

- [[Winform/00-WinForms学习笔记总览]] — WinForms 完整分类笔记
- [[Winform/01-WinForm基础/01-项目结构与部分类]] — 项目结构与部分类
- [[Winform/02-事件机制/01-事件的基本使用]] — 事件机制
- [[Winform/03-常用控件/01-基础控件Button与Label与TextBox]] — 常用控件
- [[Winform/06-文件与IO操作/01-File与Directory]] — 文件与目录
- [[Winform/11-Socket网络编程/00-Socket基础与TCP通信流程]] — Socket 网络编程

- [[10-IO操作/文件IO概览]] — 文件 IO 概览（控制台版）
- [[04-委托与Lambda/委托与Lambda]] — 委托
- [[04-委托与Lambda/事件]] — 事件
- [[05-集合/集合与泛型]] — 字典与集合
- [[08-面向对象/类与对象/接口]] — 接口
- [[08-面向对象/类与对象/基础]] — 属性与字段
