# C# WinForms 学习笔记（完整版）

> 整合 `02-C#第二阶段WinForm与文件操作.md` 与 `D:\study\C#1\WinForm\` 下 stu0804–stu0812 共 7 个目录、80+ 个项目的源码，按知识点分类整理。

---

# 目录

- [一、WinForm 基础](#一winform-基础)
  - [1.1 项目结构与部分类](#11-项目结构与部分类)
  - [1.2 窗体初始化与 Graphics 绘图](#12-窗体初始化与-graphics-绘图)
  - [1.3 坐标系统与控件定位](#13-坐标系统与控件定位)
- [二、事件机制](#二事件机制)
  - [2.1 事件的基本使用（+= / -=）](#21-事件的基本使用--)
  - [2.2 事件的联动响应（sender）](#22-事件的联动响应sender)
  - [2.3 键盘事件（KeyUp / KeyPress / KeyDown）](#23-键盘事件keyup--keypress--keydown)
  - [2.4 鼠标事件（MouseEnter / MouseLeave）](#24-鼠标事件mouseenter--mouseleave)
  - [2.5 焦点（Focus）与 KeyPreview](#25-焦点focus-与-keypreview)
  - [2.6 消息提示框（MessageBox）](#26-消息提示框messagebox)
- [三、常用控件详解](#三常用控件详解)
  - [3.1 按钮（Button）](#31-按钮button)
  - [3.2 面板（Panel）与分组（GroupBox）](#32-面板panel-与分组groupbox)
  - [3.3 标签（Label）与文本框（TextBox）](#33-标签label-与文本框textbox)
  - [3.4 图片框（PictureBox）](#34-图片框picturebox)
  - [3.5 列表框（ListBox）与下拉框（ComboBox）](#35-列表框listbox-与下拉框combobox)
  - [3.6 ListView 列表视图](#36-listview-列表视图)
  - [3.7 定时器（Timer）](#37-定时器timer)
  - [3.8 进度条（ProgressBar）](#38-进度条progressbar)
  - [3.9 菜单与状态栏（MenuStrip / ToolStrip）](#39-菜单与状态栏menustrip--toolstrip)
  - [3.10 选项卡（TabControl）](#310-选项卡tabcontrol)
  - [3.11 数据网格（DataGridView）](#311-数据网格datagridview)
- [四、布局管理](#四布局管理)
  - [4.1 手动定位（Location / Size）](#41-手动定位location--size)
  - [4.2 窗体居中](#42-窗体居中)
  - [4.3 Dock 布局](#43-dock-布局)
- [五、多窗体与数据传递](#五多窗体与数据传递)
  - [5.1 打开新窗口（Show / ShowDialog）](#51-打开新窗口show--showdialog)
  - [5.2 窗体之间数据传递](#52-窗体之间数据传递)
  - [5.3 窗体生命周期事件](#53-窗体生命周期事件)
- [六、文件操作](#六文件操作)
  - [6.1 文件与目录操作（File / Directory）](#61-文件与目录操作file--directory)
  - [6.2 文件路径操作（Path 类）](#62-文件路径操作path-类)
  - [6.3 对话框（OpenFileDialog / SaveFileDialog）](#63-对话框openfiledialog--savedfiledialog)
  - [6.4 文本流读写（StreamReader / StreamWriter）](#64-文本流读写streamreader--streamwriter)
  - [6.5 二进制序列化（BinaryFormatter）](#65-二进制序列化binaryformatter)
  - [6.6 INI 配置文件](#66-ini-配置文件)
- [七、面向对象设计模式](#七面向对象设计模式)
  - [7.1 单例模式（Singleton）](#71-单例模式singleton)
  - [7.2 登录注册系统（文件存储）](#72-登录注册系统文件存储)
- [八、多线程编程](#八多线程编程)
  - [8.1 Thread 线程](#81-thread-线程)
  - [8.2 ThreadPool 线程池](#82-threadpool-线程池)
  - [8.3 跨线程调用控件（BeginInvoke）](#83-跨线程调用控件begininvoke)
- [九、项目实战 — 图书管理系统](#九项目实战--图书管理系统)
  - [9.1 项目结构](#91-项目结构)
  - [9.2 数据模型](#92-数据模型)
  - [9.3 登录验证](#93-登录验证)
  - [9.4 学生端功能](#94-学生端功能)
  - [9.5 管理员端功能](#95-管理员端功能)
- [附录：WinForm 速查表](#附录winform-速查表)
  - [A.1 常用控件速查](#a1-常用控件速查)
  - [A.2 常用事件速查](#a2-常用事件速查)
  - [A.3 FileMode 枚举](#a3-filemode-枚举)
  - [A.4 FileAccess 枚举](#a4-fileaccess-枚举)

---

# 一、WinForm 基础

## 1.1 项目结构与部分类

> **来源**：stu0804/01、stu0805/stu0805

### 项目文件结构

```
WinForm 项目/
├── Program.cs              # 程序入口
├── Form1.cs                # 窗体逻辑代码（手动编写）
├── Form1.Designer.cs       # 设计器自动生成的代码（控件、事件绑定）
├── Form1.resx              # 窗体资源文件
└── Properties/
    └── AssemblyInfo.cs     # 程序集信息
```

### 部分类（partial class）

同一个类可以拆分成多个文件定义：

```csharp
// FrmMain.cs — 手动编写的部分
public partial class FrmMain : Form
{
    public FrmMain()
    {
        InitializeComponent();  // 在 Designer.cs 中定义
        SetTitleCenter();        // 自定义逻辑
    }
}

// FrmMain.Designer.cs — 设计器自动生成的部分
partial class FrmMain
{
    private void InitializeComponent()
    {
        // 控件初始化代码（不要手动修改）
        this.btn1 = new System.Windows.Forms.Button();
        this.comboBox1 = new System.Windows.Forms.ComboBox();
        // ...
    }
    private Button btn1;
    private ComboBox comboBox1;
}
```

> 💡 **规则**：`Designer.cs` 由 Visual Studio 设计器自动管理，逻辑代码都写在 `.cs` 文件里。

### Program 入口

```csharp
[STAThread]
static void Main()
{
    Application.EnableVisualStyles();                // 启用视觉样式
    Application.SetCompatibleTextRenderingDefault(false); // 禁用兼容模式
    Application.Run(new FrmMain());                  // 运行主窗体（阻塞式）
}
```

> 💡 **关键**：`Application.Run()` 是阻塞的，它会一直运行直到主窗体关闭。如果需要先显示登录窗口再显示主窗口，需要使用 `Show()` + `Hide()` 配合。

## 1.2 窗体初始化与 Graphics 绘图

> **来源**：stu0804/01/FrmMain.cs

### 窗体常用属性

| 属性 | 说明 | 示例 |
|------|------|------|
| `Text` | 窗体标题 | `this.Text = "我的程序";` |
| `Size` | 窗体大小 | `this.Size = new Size(800, 600);` |
| `ClientSize` | 客户区大小（不含标题栏和边框） | `this.ClientSize = new Size(790, 502);` |
| `Location` | 窗体位置 | `this.Location = new Point(100, 100);` |
| `Controls` | 控件集合 | `this.Controls.Add(btn1);` |
| `AutoScroll` | 自动滚动条 | `this.AutoScroll = true;` |

### Graphics 对象 — 在窗体上绘图

```csharp
private void SetTitleCenter()
{
    string titleMsg = "Winfrom Title";
    Graphics g = this.CreateGraphics();

    // 计算标题居中起始位置
    Double startingPoint = (this.Width / 2) - (g.MeasureString(titleMsg, this.Font).Width / 2);

    // 计算空格宽度
    Double widthOfASpace = g.MeasureString(" ", this.Font).Width;

    // 用空格填充使标题居中
    String tmp = " ";
    Double tmpWidth = 0;
    while ((tmpWidth + widthOfASpace) < startingPoint)
    {
        tmp += " ";
        tmpWidth += widthOfASpace;
    }
    this.Text = tmp + titleMsg;
}
```

> 💡 **应用场景**：动态计算字符串在窗体上的居中位置。`g.MeasureString()` 可以精确测量字符串在指定字体下的像素宽度。

## 1.3 坐标系统与控件定位

> **来源**：stu0804/练习、stu0811/stu0811

### WinForms 坐标系统

```
(0,0) ──────────────────────► X 增大（向右）
 │
 │         panel1
 │    ┌──────────────┐
 │    │  button3     │
 │    │  (x, y)      │
 │    └──────────────┘
 ▼
Y 增大（向下）
```

### 控件定位三要素

```csharp
// 1. Location（位置）— Point 结构
button.Location = new Point(100, 200);  // 左上角坐标

// 2. Size（大小）— Size 结构
button.Size = new Size(80, 30);         // 宽 × 高

// 3. 获取当前值
int x = button.Location.X;             // 当前 X 坐标
int y = button.Location.Y;             // 当前 Y 坐标
int w = button.Size.Width;             // 宽度
int h = button.Size.Height;            // 高度
```

### 窗体居中

```csharp
// 方式一：Load 事件中计算
private void Form1_Load(object sender, EventArgs e)
{
    int W = (this.ClientSize.Width - this.panel1.Size.Width) / 2;
    int H = (this.ClientSize.Height - this.panel1.Size.Height) / 2;
    this.panel1.Location = new Point(W, H);
}

// 方式二：SizeChanged 事件中计算（窗体大小改变时重新居中）
private void Form1_SizeChanged(object sender, EventArgs e)
{
    int W = (this.ClientSize.Width - this.panel1.Size.Width) / 2;
    int H = (this.ClientSize.Height - this.panel1.Size.Height) / 2;
    this.panel1.Location = new Point(W, H);
}
```

> 💡 **ClientSize vs Size**：`ClientSize` 是窗体内部可用区域（不含标题栏和边框），`Size` 是整个窗体的大小。居中计算用 `ClientSize` 更准确。

---

# 二、事件机制

## 2.1 事件的基本使用（+= / -=）

> **来源**：stu0804/事件使用

### 事件的三要素

```csharp
// 1. 事件提供者：控件（如 Button）
// 2. 事件：控件上发生的动作（如 Click）
// 3. 事件处理器：事件发生后执行的代码
```

### 订阅与取消事件

```csharp
// 订阅事件（绑定处理方法）
btnTest.Click += new EventHandler(btnTest_Click);

// 取消订阅（解除绑定）
btnTest.Click -= new EventHandler(btnTest_Click);

// 事件处理方法
private void btnTest_Click(object sender, EventArgs e)
{
    MessageBox.Show("按钮被点击了");
}
```

### 完整示例

```csharp
public partial class Form1 : Form
{
    public Form1()
    {
        InitializeComponent();
    }

    private void btnTest_Click(object sender, EventArgs e)
    {
        MessageBox.Show("按钮被点击了");
    }

    // 取消绑定
    private void button1_Click(object sender, EventArgs e)
    {
        this.btnTest.Click -= new EventHandler(this.btnTest_Click);
    }

    // 重新绑定
    private void button2_Click(object sender, EventArgs e)
    {
        this.btnTest.Click += new System.EventHandler(this.btnTest_Click);
    }
}
```

## 2.2 事件的联动响应（sender）

> **来源**：stu0804/事件的联动响应

### 多个控件共享一个事件处理方法

```csharp
public void BtnTest_Click(object sender, EventArgs e)
{
    // sender：触发事件的对象（object 类型）
    Button btn = (Button)sender;  // 强制转换为 Button

    label1.Text = btn.Text;               // 显示按钮文字
    label1.BackColor = btn.BackColor;     // 显示按钮背景色
    label1.ForeColor = btn.ForeColor;     // 显示按钮前景色
    MessageBox.Show($"{btn.Text}被点击了");
}
```

### 设计器中绑定

```csharp
// 三个按钮绑定同一个事件处理方法
this.button1.Click += new EventHandler(this.BtnTest_Click);  // 红色 Andy老师
this.button2.Click += new EventHandler(this.BtnTest_Click);  // 绿色 Carry老师
this.button3.Click += new EventHandler(this.BtnTest_Click);  // 蓝色 Coco老师
```

### sender 工作原理

```
用户点击 button1（文字="Andy老师"，背景=红色）
         ↓
触发 BtnTest_Click 事件
         ↓
sender 就是 button1 对象
         ↓
((Button)sender).Text     → "Andy老师"
((Button)sender).BackColor → Color.Red
```

> 💡 **要点**：`sender` 是 `object` 类型，必须强制转换才能访问具体控件的属性。

## 2.3 键盘事件（KeyUp / KeyPress / KeyDown）

> **来源**：stu0804/练习、stu0811/stu0811

### 三种键盘事件对比

| 事件 | 参数类型 | 获取内容 | 触发时机 | 特点 |
|------|---------|---------|---------|------|
| `KeyDown` | `KeyEventArgs` | `e.KeyCode` | 按键**按下**时 | 按住连续触发 |
| `KeyUp` | `KeyEventArgs` | `e.KeyCode` | 按键**松开**时 | 只触发一次 |
| `KeyPress` | `KeyPressEventArgs` | `e.KeyChar` | 字符输入时 | 输入法相关 |

### KeyUp 实战：方向键移动方块

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

### 边界判断公式

```
上边界：box.Location.Y &gt; 0
下边界：box.Location.Y &lt; panel1.Height - box.Height
左边界：box.Location.X &gt; 0
右边界：box.Location.X &lt; panel1.Width - box.Width
```

> 💡 **要点**：`panel1.Height - box.Height` 表示方块左上角能到达的最大 Y 值。

## 2.4 鼠标事件（MouseEnter / MouseLeave）

> **来源**：stu0805/stu0805

### 鼠标悬停切换密码框显隐

```csharp
private void button1_MouseEnter(object sender, EventArgs e)
{
    textBox1.UseSystemPasswordChar = false;  // 鼠标移入：显示明文
}

private void button1_MouseLeave(object sender, EventArgs e)
{
    textBox1.UseSystemPasswordChar = true;   // 鼠标移出：显示密文
}
```

### 鼠标悬停切换图片

```csharp
private void label1_MouseEnter(object sender, EventArgs e)
{
    pb1.Visible = true;
    pb1.Image = Properties.Resources.guga;    // 鼠标移入显示大图
}

private void label1_MouseLeave(object sender, EventArgs e)
{
    pb1.Image = Properties.Resources._131905150_p0_master1200;  // 鼠标移出恢复默认
}
```

### 图片点击切换

```csharp
private void feibi_Click(object sender, EventArgs e)
{
    pb1.Image = Properties.Resources.phoebe_1;
}

private void guga_Click(object sender, EventArgs e)
{
    pb1.Image = Properties.Resources.guga;
}

// 鼠标悬停时自动切换
private void feibi_MouseEnter(object sender, EventArgs e)
{
    pb1.Image = Properties.Resources.phoebe_1;
}
```

## 2.5 焦点（Focus）与 KeyPreview

> **来源**：stu0804/练习

### 焦点问题

键盘事件需要控件**获得焦点**才能触发。如果点击了其他按钮，焦点就会丢失。

### 解决方案

```csharp
// 方案一：每次操作后交回焦点（简单但不完美）
private void button1_Click(object sender, EventArgs e)
{
    button3.Location = new Point(x, y + 10);
    button3.Focus();  // 交回焦点
}

// 方案二：设置 KeyPreview = true（推荐）
// 在 Form1 构造函数中
public Form1()
{
    InitializeComponent();
    this.KeyPreview = true;  // 让窗体先于子控件收到键盘事件
    this.KeyUp += new KeyEventHandler(Form1_KeyUp);
}
```

> 💡 **`KeyPreview = true`** 是推荐做法：窗体先于所有子控件接收到键盘事件，不依赖焦点。

### Focus() vs Select()

| 方法 | 效果 | 推荐场景 |
|------|------|---------|
| `Focus()` | 强制控件获得输入焦点 | 大多数情况 |
| `Select()` | 选中控件，但不一定获得焦点 | 安全场景 |

## 2.6 消息提示框（MessageBox）

> **来源**：stu0805/菜单类控件

### MessageBox.Show 完整用法

```csharp
// 基本用法
MessageBox.Show("提示内容");

// 带标题
MessageBox.Show("提示内容", "标题");

// 完整参数
MessageBox.Show(
    "是否关闭？",                        // 内容
    "警告",                               // 标题
    MessageBoxButtons.OKCancel,           // 按钮样式
    MessageBoxIcon.Asterisk,              // 图标
    MessageBoxDefaultButton.Button1       // 默认选中按钮
);
```

### 获取用户选择

```csharp
DialogResult res = MessageBox.Show(
    "是否关闭？", "警告",
    MessageBoxButtons.OKCancel,
    MessageBoxIcon.Question);

if (res == DialogResult.OK)
{
    this.Close();
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
        e.Cancel = true;  // 取消关闭
    }
}
```

### 常用枚举值

| MessageBoxButtons | 显示的按钮 |
|-------------------|-----------|
| `OK` | 确定 |
| `OKCancel` | 确定 + 取消 |
| `YesNo` | 是 + 否 |
| `YesNoCancel` | 是 + 否 + 取消 |

| MessageBoxIcon | 图标 |
|----------------|------|
| `None` | 无 |
| `Information` | 信息 ℹ |
| `Warning` | 警告 ⚠ |
| `Error` | 错误 ❌ |
| `Question` | 问号 ❓ |
| `Asterisk` | 星标 ℹ |

> 💡 **要点**：`FormClosing` 可以阻止关闭（`e.Cancel = true`），`FormClosed` 在关闭后触发，无法阻止。

---

# 三、常用控件详解

## 3.1 按钮（Button）

```csharp
// 基本属性
button.Text = "点击我";
button.Location = new Point(100, 200);
button.Size = new Size(80, 30);
button.BackColor = Color.Red;
button.ForeColor = Color.White;
button.Enabled = false;    // 禁用（灰掉）
button.Visible = false;    // 隐藏

// 事件
button.Click += Button_Click;
```

## 3.2 面板（Panel）与分组（GroupBox）

```csharp
// Panel：容器控件，用于分组和布局
Panel panel1 = new Panel();
panel1.Width = 300;
panel1.Height = 300;
panel1.Location = new Point(20, 20);
panel1.BorderStyle = BorderStyle.FixedSingle;  // 显示边框
this.Controls.Add(panel1);

// 将按钮添加到 Panel 中（而非窗体）
panel1.Controls.Add(button1);
```

## 3.3 标签（Label）与文本框（TextBox）

```csharp
// Label：显示文本
label1.Text = "Hello";
label1.BackColor = Color.Red;
label1.ForeColor = Color.White;
label1.TextAlign = ContentAlignment.MiddleCenter;  // 文字居中

// TextBox：输入/显示文本
textBox1.Text = "输入内容";
textBox1.PasswordChar = '*';           // 密码模式
textBox1.UseSystemPasswordChar = true;  // 系统密码样式
textBox1.Multiline = true;             // 多行模式
textBox1.ScrollBars = ScrollBars.Vertical;  // 垂直滚动条
```

## 3.4 图片框（PictureBox）

```csharp
// 加载图片
pictureBox1.ImageLocation = @"D:\Images\1.jpg";
pictureBox1.Image = Image.FromFile(@"D:\Images\1.jpg");

// 缩放模式
pictureBox1.SizeMode = PictureBoxSizeMode.Zoom;  // 等比缩放（推荐）
pictureBox1.SizeMode = PictureBoxSizeMode.StretchImage;  // 拉伸填充
pictureBox1.SizeMode = PictureBoxSizeMode.CenterImage;   // 居中裁剪

// 显隐控制
pictureBox1.Visible = true;
```

| SizeMode | 效果 |
|----------|------|
| `Normal` | 原始大小，超出裁剪 |
| `StretchImage` | 拉伸填充 |
| `AutoSize` | 控件自适应图片大小 |
| `CenterImage` | 居中，超出裁剪 |
| `Zoom` | 等比缩放（推荐） |

## 3.5 列表框（ListBox）与下拉框（ComboBox）

```csharp
// ComboBox：下拉选择
comboBox1.Items.Add("日用百货");
comboBox1.Items.Add("服装鞋袜");
comboBox1.Items.Add("数码产品");
comboBox1.SelectedIndex = 0;  // 默认选中第一个

// 获取选中项
string selected = comboBox1.Items[comboBox1.SelectedIndex].ToString();
string selectedText = comboBox1.Text;

// ListBox：列表选择
listBox1.Items.Add("项目1");
listBox1.Items.Add("项目2");

// 获取选中项
int index = listBox1.SelectedIndex;
string item = listBox1.Items[index].ToString();
```

## 3.6 ListView 列表视图

```csharp
// 添加列（通常在 Form1_Load 或构造函数中）
columnHeader1.Width = listView1.Width / 5;
columnHeader2.Width = listView1.Width / 5;
// ...

// 添加行
ListViewItem item = new ListViewItem("第一列");
item.SubItems.Add("第二列");
item.SubItems.Add("第三列");
item.SubItems.Add("第四列");
item.SubItems.Add("第五列");
listView1.Items.Add(item);

// 自动滚动到最新项
listView1.Items[listView1.Items.Count - 1].EnsureVisible();

// 响应大小变化重新排列列
private void Form1_SizeChanged(object sender, EventArgs e)
{
    Buju();  // 重新计算列宽
}
```

## 3.7 定时器（Timer）

```csharp
// 设置定时器（设计器或代码）
timer1.Interval = 200;      // 间隔（毫秒）
timer1.Enabled = true;       // 启动
timer1.Stop();               // 停止
timer1.Start();              // 启动

// Tick 事件：每次间隔触发
private void timer1_Tick(object sender, EventArgs e)
{
    toolStripStatusLabel1.Text = DateTime.Now.ToString();
}

// 鼠标悬停时暂停/恢复
private void Pic1_MouseEnter(object sender, EventArgs e)
{
    timer1.Enabled = false;  // 鼠标移入暂停
}
private void Pic1_MouseLeave(object sender, EventArgs e)
{
    timer1.Start();          // 鼠标移出恢复
}
```

## 3.8 进度条（ProgressBar）

```csharp
// 设置最大值
progressBar1.Maximum = 100;
progressBar1.Minimum = 0;
progressBar1.Value = 0;

// 模拟进度
private void button1_Click(object sender, EventArgs e)
{
    for (int i = 0; i <= progressBar1.Maximum; i++)
    {
        progressBar1.Value = i;
        Thread.Sleep(100);  // 模拟耗时操作
    }
    if (progressBar1.Value == 100)
    {
        new hhh().ShowDialog();  // 完成后弹出窗口
    }
}
```

## 3.9 菜单与状态栏（MenuStrip / ToolStrip）

```csharp
// ToolStripStatusLabel：状态栏文字
toolStripStatusLabel1.Text = DateTime.Now.ToString();

// ToolStripMenuItem：菜单项点击
private void 保存ToolStripMenuItem_Click(object sender, EventArgs e)
{
    this.Close();
}

// ToolStripComboBox：工具栏下拉框
private void toolStripComboBox1_Click(object sender, EventArgs e)
{
    // 处理选择
}

// ToolStripButton：工具栏按钮
private void toolStripButton1_Click(object sender, EventArgs e)
{
    // 处理点击
}
```

## 3.10 选项卡（TabControl）

```csharp
// 切换到指定页面
tabControl1.SelectedTab = tabPage1;

// 显示/隐藏页面
tabPage1.Show();
tabPage1.Hide();
```

## 3.11 数据网格（DataGridView）

```csharp
// 绑定数据源
dataGridView1.DataSource = books;         // 绑定 List&lt;Book&gt;
dataGridView1.DataSource = recordBooks;    // 绑定 List&lt;RecordBook&gt;

// 数据模型
public class Book
{
    public int Id { get; set; }
    public string Title { get; set; }
    public string Author { get; set; }
    public int Sum { get; set; }
    public int Count { get; set; }
}

// 使用 BindingList 支持动态更新
BindingList&lt;People&gt; list = new BindingList&lt;People&gt;();
dataGridView1.DataSource = list;
list.Add(new People("张三", "12", "男", "郑州"));  // 自动刷新界面
```

> 💡 **BindingList`<T>`** 相比 `List<T>` 的优势：增删元素时自动通知 DataGridView 更新界面。

---

# 四、布局管理

## 4.1 手动定位（Location / Size）

```csharp
// 设置位置和大小
button.Location = new Point(100, 200);
button.Size = new Size(80, 30);

// 动态移动
button.Location = new Point(x += 10, y);
```

## 4.2 窗体居中

```csharp
private void Form1_SizeChanged(object sender, EventArgs e)
{
    int W = (this.ClientSize.Width - this.panel1.Size.Width) / 2;
    int H = (this.ClientSize.Height - this.panel1.Size.Height) / 2;
    this.panel1.Location = new Point(W, H);
}
```

## 4.3 Dock 布局

```csharp
// Dock 属性：控件紧贴父容器的某个边
button1.Dock = DockStyle.Top;     // 顶部
button1.Dock = DockStyle.Bottom;  // 底部
button1.Dock = DockStyle.Left;    // 左侧
button1.Dock = DockStyle.Right;   // 右侧
button1.Dock = DockStyle.Fill;    // 填充全部
```

---

# 五、多窗体与数据传递

## 5.1 打开新窗口（Show / ShowDialog）

```csharp
// Show()：非模态窗口（两个窗口都可以操作）
Form2 form2 = new Form2();
form2.Show();          // 非阻塞
this.Hide();           // 隐藏当前窗口

// ShowDialog()：模态窗口（必须关闭子窗口才能操作父窗口）
Form2 form2 = new Form2();
form2.ShowDialog();    // 阻塞，直到子窗口关闭
```

### 窗口切换模式

```csharp
// 登录 → 主窗口
private void BtnLogin_Click(object sender, EventArgs e)
{
    if (验证通过)
    {
        FrmMain main = new FrmMain(this);
        main.Show();
        this.Hide();        // 隐藏登录窗口
    }
}

// 主窗口 → 返回登录
private void button1_Click(object sender, EventArgs e)
{
    loginForm.Show();
    this.Close();
}
```

## 5.2 窗体之间数据传递

### 方式一：构造函数传参

```csharp
// Form2 定义带参数的构造函数
public Form2(string str)
{
    InitializeComponent();
    label1.Text = str;
}

// Form1 中打开
Form2 form2 = new Form2("test");
form2.ShowDialog();
```

### 方式二：保存父窗体引用

```csharp
// 登录窗口保存 FrmMain 引用
public partial class Login : Form
{
    Login L;
    public Login()
    {
        InitializeComponent();
    }

    private void button2_Click(object sender, EventArgs e)
    {
        L = this;
        new Reg(L).Show();    // 传递登录窗体引用
        L.Hide();
    }
}

// 注册窗口接收
public partial class Reg : Form
{
    Login L;
    public Reg(Login login)
    {
        InitializeComponent();
        L = login;  // 保存引用，注册成功后返回
    }

    private void button2_Click(object sender, EventArgs e)
    {
        L.Show();    // 返回登录窗口
        this.Close();
    }
}
```

### 方式三：公共属性

```csharp
// Form2 中定义公共字段
public string str = "一号窗口数据";

// Form1 中读取
Form2 form2 = new Form2(str);
form2.ShowDialog();
textBox1.Text = form2.str;  // 读取 Form2 的数据
```

## 5.3 窗体生命周期事件

```csharp
// 窗体加载时（首次显示）
private void Form1_Load(object sender, EventArgs e)
{
    // 初始化数据
}

// 窗体关闭前（可阻止关闭）
private void Form1_FormClosing(object sender, FormClosingEventArgs e)
{
    // e.Cancel = true;  // 取消关闭
    DialogResult res = MessageBox.Show("是否关闭？", "提示",
        MessageBoxButtons.OKCancel);
    if (res == DialogResult.Cancel)
        e.Cancel = true;
}

// 窗体关闭后（无法阻止）
private void Form1_FormClosed(object sender, FormClosedEventArgs e)
{
    // 清理资源
}
```

---

# 六、文件操作

## 6.1 文件与目录操作（File / Directory）

### File 类 — 静态方法

```csharp
// 判断文件是否存在
if (File.Exists(path)) { }

// 删除文件
File.Delete(path);

// 复制文件
File.Copy(sourcePath, destPath);              // 不允许覆盖
File.Copy(sourcePath, destPath, true);        // 允许覆盖

// 移动/重命名文件
File.Move(sourcePath, destPath);
```

### Directory 类 — 目录操作

```csharp
// 获取目录下所有文件
string[] files = Directory.GetFiles(@"D:\MyFolder");

// 递归获取所有子目录
public void GetAllDirs(string path)
{
    string[] dirs = Directory.GetDirectories(path);
    foreach (string dir in dirs)
    {
        if (Path.GetDirectoryName(dir).Length > 0)
            GetAllDirs(dir);  // 递归
        Console.WriteLine(dir);
    }
}

// 创建目录
Directory.CreateDirectory(path);

// 删除目录
Directory.Delete(path);           // 删除空目录
Directory.Delete(path, true);     // 删除非空目录（递归）

// DirectoryInfo 方式
DirectoryInfo dir = new DirectoryInfo(path);
dir.Delete(true);                 // true = 递归删除
```

### DirectoryInfo 属性

```csharp
DirectoryInfo dir = new DirectoryInfo(path);
Console.WriteLine("名称：" + dir.Name);
Console.WriteLine("完整路径：" + dir.FullName);
Console.WriteLine("创建时间：" + dir.CreationTime);
Console.WriteLine("访问时间：" + dir.LastAccessTime);
Console.WriteLine("写入时间：" + dir.LastWriteTime);
```

## 6.2 文件路径操作（Path 类）

```csharp
string filePath = @"D:\Images\photo.jpg";

// 获取目录名
Path.GetDirectoryName(filePath);    // → D:\Images

// 获取扩展名
Path.GetExtension(filePath);        // → .jpg

// 获取文件名
Path.GetFileName(filePath);         // → photo.jpg

// 获取完整路径
Path.GetFullPath(@"./1.txt");

// 更改扩展名
Path.ChangeExtension(filePath, "png");  // → D:\Images\photo.png

// 组合路径（自动处理分隔符）
string newPath = Path.Combine(@"D:\", "Images", "photo.jpg");

// 随机文件名
Path.GetRandomFileName();
```

> 💡 **推荐**：用 `Path.Combine()` 代替字符串拼接，自动处理不同系统的路径分隔符。

## 6.3 对话框（OpenFileDialog / SaveFileDialog）

```csharp
// OpenFileDialog
OpenFileDialog ofd = new OpenFileDialog();
ofd.InitialDirectory = @"D:\";                    // 初始目录
ofd.Filter = "文本文件|*.txt|图片|*.jpg;*.png|所有文件|*.*";  // 筛选器
ofd.Title = "选择文件";

if (ofd.ShowDialog() == DialogResult.OK)
{
    string filePath = ofd.FileName;
    // 处理文件...
}

// SaveFileDialog
SaveFileDialog sfd = new SaveFileDialog();
sfd.InitialDirectory = @"D:\";
sfd.Filter = "文本文件|*.txt";
sfd.Title = "保存文件";

if (sfd.ShowDialog() == DialogResult.OK)
{
    File.WriteAllText(sfd.FileName, textBox1.Text);
}
```

### Filter 格式

```
"描述1|扩展名1|描述2|扩展名2|描述3|扩展名3"
"文本文件|*.txt|图片|*.jpg;*.png|所有文件|*.*"
```

## 6.4 文本流读写（StreamReader / StreamWriter）

```csharp
// === 写入（using 自动释放资源）===
using (FileStream fs = new FileStream(@"./my.txt", FileMode.Append, FileAccess.Write))
using (StreamWriter sw = new StreamWriter(fs))
{
    sw.WriteLine(textBox1.Text.Trim());
}

// === 读取 ===
using (FileStream fs = new FileStream(@"./my.txt", FileMode.Open, FileAccess.Read))
using (StreamReader sr = new StreamReader(fs))
{
    textBox1.Text = sr.ReadToEnd();       // 读取全部
    // string line = sr.ReadLine();       // 读取一行
}
```

### 日志追加

```csharp
FileStream fs = new FileStream(@"./log.txt", FileMode.Append, FileAccess.Write);
StreamWriter sw = new StreamWriter(fs);
sw.WriteLine(DateTime.Now.ToString() + "[操作正常...]");
sw.Close();
fs.Close();
```

## 6.5 二进制序列化（BinaryFormatter）

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

    FileStream fs = new FileStream(@"./student.obj", FileMode.Create);
    BinaryFormatter bf = new BinaryFormatter();
    bf.Serialize(fs, student);
    fs.Close();
}

// 3. 反序列化（文件 → 对象）
private void btnLoad_Click(object sender, EventArgs e)
{
    FileStream fs = new FileStream(@"./student.obj", FileMode.Open);
    BinaryFormatter bf = new BinaryFormatter();
    Student student = (Student)bf.Deserialize(fs);
    fs.Close();

    textBox1.Text = student.Name;
    textBox2.Text = student.Age;
    textBox3.Text = student.Sex;
    textBox4.Text = student.Birthday;
}
```

### 序列化方式对比

| 方式 | 文件格式 | 可读性 | 空间 | 类型安全 |
|------|---------|--------|------|---------|
| 文本（StreamWriter） | 人类可读 | ✅ | 较大 | ❌ 需手动解析 |
| 二进制（BinaryFormatter） | 二进制 | ❌ | 较小 | ✅ 自动保持 |

> ⚠️ **注意**：`BinaryFormatter` 在 .NET Core / .NET 5+ 中已过时，.NET Framework 4.7.2 中仍可用。

## 6.6 INI 配置文件

> **来源**：stu0812/stu0813/ini配置文件

### INI 文件格式

```ini
[用户信息]
张三=25
李四=30

[系统设置]
theme=dark
language=zh-CN
```

### 使用 Win32 API 读写 INI

```csharp
// IniAPI.cs — 封装类
public static class IniAPI
{
    [DllImport("kernel32.dll", CharSet = CharSet.Auto)]
    private static extern bool WritePrivateProfileString(
        string lpAppName, string lpKeyName, string lpString, string lpFileName);

    [DllImport("kernel32.dll", CharSet = CharSet.Auto)]
    private static extern uint GetPrivateProfileString(
        string lpAppName, string lpKeyName, string lpDefault,
        StringBuilder lpReturnedString, uint nSize, string lpFileName);

    // 写入
    public static bool INIWriteValue(string iniFile, string section, string key, string value)
    {
        return WritePrivateProfileString(section, key, value, iniFile);
    }

    // 读取
    public static string GetPrivateProfileString(string section, string key, string def, string iniFile)
    {
        StringBuilder sb = new StringBuilder(1024);
        GetPrivateProfileString(section, key, def, sb, 1024, iniFile);
        return sb.ToString();
    }
}

// 使用
string iniPath = Directory.GetCurrentDirectory() + "\\config.ini";

// 写入
IniAPI.INIWriteValue(iniPath, "用户信息", "姓名", "张三");
IniAPI.INIWriteValue(iniPath, "用户信息", "年龄", "25");

// 读取
string name = IniAPI.GetPrivateProfileString("用户信息", "姓名", "未知", iniPath);
string age = IniAPI.GetPrivateProfileString("用户信息", "年龄", "0", iniPath);
```

---

# 七、面向对象设计模式

## 7.1 单例模式（Singleton）

> **来源**：stu0812/stu0813/单例模式

### 实现原理

确保一个类只有一个实例，并提供全局访问点。

```csharp
internal class Danli
{
    int num = 100;
    private Danli() { }           // 私有构造函数（禁止外部 new）
    static Danli danli;           // 静态实例

    public static Danli GetDanli()  // 全局访问点
    {
        if (danli == null)
        {
            danli = new Danli();  // 延迟初始化
        }
        return danli;
    }

    public int Dan()
    {
        return --num;
    }
}

// 使用：无论创建多少个对象，都共享同一个实例
Class1 one = new Class1();  // 内部调用 Danli.GetDanli()
Class2 two = new Class2();  // 内部也调用 Danli.GetDanli()
// one 和 two 内部的 dan 是同一个对象！
```

> 💡 **要点**：多个类通过 `Danli.GetDanli()` 获取同一个实例，所有操作都共享同一份数据。

## 7.2 登录注册系统（文件存储）

> **来源**：stu0805/8.5作业、stu0807/登录注册

### 数据存储格式

```
# 账号密码文件（| 分隔）
admin|123456
student|654321

# 或逗号分隔（CSV）
张三,123456,student
李四,654321,admin
```

### 登录验证

```csharp
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
            StuLib.Manage();  // 进入学生管理菜单
            return;
        }
    }
    Console.WriteLine("登录失败");
}
```

### 注册写入

```csharp
public static void Reg()
{
    Console.Write("请输入账号：");
    string admin = Console.ReadLine();
    Console.Write("请输入密码：");
    string passwd = Console.ReadLine();
    WritData(admin + "," + passwd);
}

public static void WritData(string user)
{
    File.AppendAllText(path, user + "\n");
}
```

---

# 八、多线程编程

## 8.1 Thread 线程

> **来源**：stu0812/多线程

### 创建与启动线程

```csharp
// 创建线程（传入方法）
Thread thread = new Thread(TestMethod);
thread.IsBackground = true;  // 设置为后台线程
thread.Start();              // 启动线程

// 带参数的线程
Thread thread2 = new Thread(TestMethod2);
thread2.Start(str);  // 传入参数

// 线程方法
private void TestMethod()
{
    Thread.Sleep(5000);       // 休眠 5 秒
    MessageBox.Show("嘿嘿");  // 弹出提示
}

private void TestMethod2(object item)
{
    string str = item.ToString();
    Thread.Sleep(5000);
    MessageBox.Show(str);
}
```

### 前台线程 vs 后台线程

| 类型 | 设置 | 行为 |
|------|------|------|
| 前台线程 | `IsBackground = false` | 只要有一个前台线程在运行，程序就不会退出 |
| 后台线程 | `IsBackground = true` | 所有前台线程结束后，后台线程被强制关闭 |

> 💡 **推荐**：WinForm 中用后台线程做耗时操作（如下载、计算），前台线程做 UI 更新。

## 8.2 ThreadPool 线程池

> **来源**：stu0812/stu0813/stu0813

### 基本用法

```csharp
// 排队一个工作项
ThreadPool.QueueUserWorkItem(TestMethod);

// 带参数的工作项
string str = "传入数据";
ThreadPool.QueueUserWorkItem((value) =>
{
    string r = value as string;
    Thread.Sleep(5000);
    MessageBox.Show(r);
}, str);
```

### 线程池 vs 手动创建线程

| 对比 | Thread | ThreadPool |
|------|--------|------------|
| 创建方式 | `new Thread()` | `ThreadPool.QueueUserWorkItem()` |
| 生命周期 | 手动管理 | 自动管理，重复利用 |
| 性能 | 创建开销大 | 复用线程，开销小 |
| 适用场景 | 长期运行的任务 | 短任务、大量小任务 |

## 8.3 跨线程调用控件（BeginInvoke）

> **来源**：stu0812/stu0813/跨线程调用控件

### 问题：跨线程操作控件会抛异常

WinForm 控件**只能在创建它的线程**中访问。从其他线程直接操作控件会抛出 `InvalidOperationException`。

### 解决方案：BeginInvoke

```csharp
private void button1_Click(object sender, EventArgs e)
{
    new Thread(Add).Start();
}

private void Add()
{
    List&lt;string&gt; list = new List&lt;string&gt;();
    list.Add("这是第一段文本");
    list.Add("这是第二段文本");

    for (int i = 0; i &lt; list.Count; i++)
    {
        Thread.Sleep(2000);

        // 通过 BeginInvoke 在主线程中更新控件
        textBox1.BeginInvoke(new Action&lt;string&gt;((value) =&gt;
        {
            textBox1.Text += value + "  ";
        }), list[i]);
    }
}
```

### 不推荐的方式

```csharp
// ❌ 关闭跨线程检查（会导致不可预知的 bug）
CheckForIllegalCrossThreadCalls = false;
```

> 💡 **要点**：`BeginInvoke` 将更新操作排队到 UI 线程执行，是安全的跨线程方案。

---

# 九、项目实战 — 图书管理系统

> **来源**：stu0807/图书管理系统

## 9.1 项目结构

```
图书管理系统/
├── Program.cs              # 入口
├── Login.cs                # 登录验证
├── Student.cs              # 学生数据模型
├── Book.cs                 # 图书数据模型
├── RecordBook.cs           # 借阅记录模型
├── Center.cs               # 个人中心
├── StudentPage.cs          # 学生主页面
├── Root.cs                 # 管理员登录
├── RootManage.cs           # 管理员主页面
├── AddBo.cs                # 添加图书
├── AddRoot.cs              # 添加管理员
├── DelBo.cs                # 删除图书
├── Borrow.cs               # 借阅图书
├── ReturnBook.cs           # 归还图书
└── Data/
    ├── Students.txt        # 学生数据
    ├── Root.txt            # 管理员数据
    ├── AllBooks.txt        # 图书数据
    └── StuBook.txt         # 借阅记录
```

## 9.2 数据模型

```csharp
// 学生
public class Student
{
    public string Account { get; set; }
    public string Password { get; set; }
    public string Name { get; set; }
    public string Sex { get; set; }
    public int Age { get; set; }
    public string Addr { get; set; }
    public List&lt;string&gt; Book { get; set; } = new List&lt;string&gt;();      // 借阅书名
    public List&lt;string&gt; BookId { get; set; } = new List&lt;string&gt;();    // 借阅书ID
    public string Path { get; set; }                                  // 头像路径
    public string Identity { get; set; }                              // 身份
}

// 图书
public class Book
{
    public int Id { get; set; }
    public string Title { get; set; }
    public string Author { get; set; }
    public string Publish { get; set; }    // 出版社
    public int Sum { get; set; }           // 总库存
    public int Count { get; set; }         // 可借数量
}

// 借阅记录
public class RecordBook
{
    public string Acc { get; set; }        // 学号
    public string Name { get; set; }       // 姓名
    public string Id { get; set; }         // 图书编号
    public string Title { get; set; }      // 书名
    public string BorrowTime { get; set; } // 借阅时间
    public string ReturnTime { get; set; } // 归还时间
}
```

## 9.3 登录验证

```csharp
private void button1_Click(object sender, EventArgs e)
{
    Reader();  // 从文件加载数据

    // 查找匹配账号密码的学生
    if (students.Contains(students.Find(a => a.Account == textBox1.Text && a.Password == textBox2.Text)))
    {
        new StudentPage(textBox1.Text, textBox2.Text).Show();
        this.Hide();
        return;
    }
    MessageBox.Show("账号或密码错误！");
}

public void Reader()
{
    using (FileStream fs = new FileStream(@"../../Data/Students.txt", FileMode.Open, FileAccess.Read))
    using (StreamReader sr = new StreamReader(fs))
    {
        string line;
        while ((line = sr.ReadLine()) != null)
        {
            string[] strings = line.Split('|');
            students.Add(new Student(strings[0], strings[1]));
        }
    }
}
```

## 9.4 学生端功能

| 功能 | 实现方式 |
|------|---------|
| 查看所有图书 | DataGridView 绑定 `List<Book>` |
| 查询图书 | `FindAll()` 按书名或编号筛选 |
| 借阅图书 | 写入 `StuBook.txt` + 图书 Count-- |
| 归还图书 | 更新 `StuBook.txt` 归还时间 + 图书 Count++ |
| 个人中心 | 显示头像、姓名、借阅数量等信息 |

### 借阅流程

```csharp
public void Borrow()
{
    // 1. 检查图书是否可借
    if (books[i].Num > 0)
    {
        // 2. 写入借阅记录
        sw.WriteLine($"{name}|{books[i].Id}|{books[i].Title}|{DateTime.Now}||未归还");

        // 3. 减少可借数量
        books[i].Num--;
    }
}
```

### 归还流程

```csharp
public void WStu()
{
    // 1. 找到借阅记录，标记为已归还
    int i = stuRe.FindIndex(a => a.Acc == student.Account && a.Id == student.BookId[stubi] && a.ReturnTime == "未归还");
    stuRe[i].ReturnTime = DateTime.Now.ToString();

    // 2. 重写整个文件
    using (FileStream fs = new FileStream(@"../../Data/StuBook.txt", FileMode.Create, FileAccess.Write))
    using (StreamWriter sw = new StreamWriter(fs))
    {
        foreach (RecordBook book in stuRe)
        {
            sw.WriteLine($"{book.Acc}|{book.Name}|{book.Id}|{book.Title}|{book.BorrowTime}|{book.ReturnTime}");
        }
    }
}

public void WBook()
{
    // 3. 恢复图书可借数量
    books[ibx].Count++;

    // 4. 重写图书文件
    using (var fs = new FileStream(@"../../Data/AllBooks.txt", FileMode.Create, FileAccess.Write))
    using (var sw = new StreamWriter(fs))
    {
        foreach (var a in books)
        {
            sw.WriteLine($"{a.Id}|{a.Title}|{a.Author}|{a.Publish}|{a.Sum}|{a.Count}");
        }
    }
}
```

## 9.5 管理员端功能

| 功能 | 实现方式 |
|------|---------|
| 添加图书 | 写入 `AllBooks.txt` |
| 查看所有图书 | DataGridView 绑定图书列表 |
| 查看借阅记录 | DataGridView 绑定 RecordBook 列表 |
| 添加管理员 | 写入 `Root.txt` |
| 删除图书 | 从列表中移除 + 重写 `AllBooks.txt` |

---

# 附录：WinForm 速查表

## A.1 常用控件速查

| 控件 | 类名 | 关键属性 | 关键事件 |
|------|------|---------|---------|
| 按钮 | `Button` | `Text`, `BackColor` | `Click` |
| 标签 | `Label` | `Text`, `TextAlign` | `Click` |
| 文本框 | `TextBox` | `Text`, `PasswordChar` | `TextChanged`, `KeyPress` |
| 图片框 | `PictureBox` | `Image`, `ImageLocation`, `SizeMode` | `Click`, `MouseEnter/Leave` |
| 面板 | `Panel` | `BorderStyle` | `Paint` |
| 组合框 | `ComboBox` | `Items`, `SelectedIndex`, `Text` | `SelectedIndexChanged` |
| 列表框 | `ListBox` | `Items`, `SelectedIndex` | `SelectedIndexChanged` |
| 复选框 | `CheckBox` | `Checked`, `Text` | `CheckedChanged` |
| 单选框 | `RadioButton` | `Checked`, `Text` | `CheckedChanged` |
| 定时器 | `Timer` | `Interval`, `Enabled` | `Tick` |
| 进度条 | `ProgressBar` | `Minimum`, `Maximum`, `Value` | — |
| 列表视图 | `ListView` | `Items`, `Columns` | — |
| 数据网格 | `DataGridView` | `DataSource` | `CellContentClick` |
| 选项卡 | `TabControl` | `TabPages` | — |
| 菜单 | `MenuStrip` / `ToolStrip` | `Items` | `ItemClicked` |
| 状态栏 | `StatusStrip` | `Items` | — |

## A.2 常用事件速查

| 事件 | 触发时机 | 参数 | 关键属性 |
|------|---------|------|---------|
| `Click` | 鼠标点击 | `EventArgs` | — |
| `Load` | 窗体加载完成 | `EventArgs` | — |
| `FormClosing` | 关闭前 | `FormClosingEventArgs` | `e.Cancel` |
| `FormClosed` | 关闭后 | `FormClosedEventArgs` | — |
| `KeyDown` | 按键按下 | `KeyEventArgs` | `e.KeyCode` |
| `KeyUp` | 按键松开 | `KeyEventArgs` | `e.KeyCode` |
| `KeyPress` | 字符输入 | `KeyPressEventArgs` | `e.KeyChar` |
| `MouseEnter` | 鼠标进入 | `EventArgs` | — |
| `MouseLeave` | 鼠标离开 | `EventArgs` | — |
| `TextChanged` | 文本变化 | `EventArgs` | — |
| `SelectedIndexChanged` | 选中项变化 | `EventArgs` | — |
| `Tick` | 定时器触发 | `EventArgs` | — |
| `SizeChanged` | 大小变化 | `EventArgs` | — |

## A.3 FileMode 枚举

| 值 | 说明 |
|----|------|
| `Create` | 创建新文件，存在则覆盖 |
| `CreateNew` | 创建新文件，存在则抛异常 |
| `Open` | 打开已有文件，不存在则抛异常 |
| `OpenOrCreate` | 打开或创建 |
| `Append` | 追加模式 |
| `Truncate` | 打开并清空 |

## A.4 FileAccess 枚举

| 值 | 说明 |
|----|------|
| `Read` | 只读 |
| `Write` | 只写 |
| `ReadWrite` | 读写 |
