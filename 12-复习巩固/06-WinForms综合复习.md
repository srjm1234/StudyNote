---
title: WinForms 综合复习
date: 2026-08-15
tags:
  - CSharp
  - 复习
  - WinForms
  - 多线程
  - Socket
---

# WinForms 综合复习

> 对应笔记：[[Winform/00-WinForms学习笔记总览]]、[[Winform/01-WinForm基础/01-项目结构与部分类]]、[[Winform/02-事件机制/01-事件的基本使用]]、[[Winform/03-常用控件/09-控件速查表]]、[[Winform/04-布局管理/02-Dock与Anchor]]、[[Winform/05-多窗体与数据传递/02-窗体之间数据传递]]、[[Winform/07-多线程编程/01-Thread线程]]、[[Winform/07-多线程编程/03-跨线程调用控件BeginInvoke]]、[[Winform/11-Socket网络编程/00-Socket基础与TCP通信流程]]
> 建议用时：70 分钟。

---

## 一、选择题

**1. WinForms 中事件订阅使用的运算符是？**

- A. `=` 
- B. `+=` / `-=`
- C. `==`
- D. `=>`

> [!success]- 答案
> **B**。`button.Click += new EventHandler(btn_Click);` 订阅，`-=` 取消。`=>` 是 Lambda 箭头。

**2. 事件处理方法的标准签名是？**

- A. `void 方法名(object sender, EventArgs e)`
- B. `int 方法名(object sender)`
- C. `void 方法名()`
- D. `string 方法名(EventArgs e)`

> [!success]- 答案
> **A**。sender 是触发事件的控件（object，通常要强转），e 是事件携带的信息（如键盘按了哪个键）。

**3. 跨线程更新 UI 控件，正确做法是？**

- A. 线程里直接修改控件
- B. 使用 `Control.BeginInvoke` 把操作交回 UI 线程
- C. 使用 Thread.Sleep 等待
- D. 不允许更新

> [!success]- 答案
> **B**。直接改会抛"跨线程操作"异常（InvalidOperationException）。用 `this.richTextBox1.BeginInvoke(new Action(...))` 安全更新。

**4. 关于 `sender`，下列说法正确的是？**

- A. 一定是 Button 类型
- B. 是触发事件的控件，object 类型，可强转成具体控件
- C. 永远为 null
- D. 是事件的参数集合

> [!success]- 答案
> **B**。sender 是 object，多个控件共用同一事件时，通过强转 `(Button)sender` 区分是哪个按钮。

**5. `ShowDialog()` 与 `Show()` 的区别是？**

- A. 没有区别
- B. ShowDialog 是模态（阻塞，必须关闭才能继续操作父窗体）
- C. Show 是模态
- D. ShowDialog 只能打开一次

> [!success]- 答案
> **B**。ShowDialog 模态阻塞；Show 非模态。模态常用于登录框、对话框（打开后不能操作父窗体）。

**6. `Dock` 与 `Anchor` 的作用是？**

- A. 设置字体
- B. 控制控件在窗体缩放时如何自适应（停靠/锚定）
- C. 设置颜色
- D. 绑定事件

> [!success]- 答案
> **B**。Dock 让控件停靠到某一边（Fill/Left/Right/Top/Bottom）；Anchor 锚定边距，窗体缩放时控件随之调整。

**7. Thread 线程的 `IsBackground = true` 表示？**

- A. 线程立即停止
- B. 后台线程，主程序退出时自动结束
- C. 线程优先级最高
- D. 线程不能被启动

> [!success]- 答案
> **B**。后台线程不阻止程序退出；前台线程会。Socket 监听/接收线程通常设为后台。

**8. Socket 服务端接受客户端连接的方法是？**

- A. Connect()
- B. Accept()
- C. Listen()
- D. Bind()

> [!success]- 答案
> **B. Accept()**。服务端流程：Bind → Listen → **Accept**（阻塞等待连接）。Connect 是客户端发起连接的。

---

## 二、填空题

**1. WinForms 项目由 ==____====____====____====____== 和 ==____====____====____====____== 两个部分文件组成（partial class）。**

> [!success]- 答案
> `Form1.cs`（业务逻辑）；`Form1.Designer.cs`（界面设计代码）。用 partial 关键字把类拆到多个文件。

**2. 窗体打开时自动执行的事件是 ==____====____====____====____==，关闭时是 ==____====____====____====____==。**

> [!success]- 答案
> `Load`（窗体加载）；`FormClosing`（关闭中，可取消）/ `FormClosed`（已关闭）。

**3. 三个常用容器控件：==____====____====____====____==、==____====____====____====____==、==____====____====____====____==。**

> [!success]- 答案
> Panel、GroupBox、TabControl。

**4. 定时器控件是 ==____====____====____====____==，通过 ==____====____====____====____== 事件周期触发。**

> [!success]- 答案
> `Timer`；`Tick`。常用作倒计时、轮询。

**5. 多线程中更新 UI 必须用 ==____====____====____====____== 方法，把操作封送回 UI 线程。**

> [!success]- 答案
> `BeginInvoke`（或 Invoke）。

**6. Socket 收发的是 ==____====____====____====____== 类型数据，发送前用 ==____====____====____====____== 编码，接收后用 ==____====____====____====____== 解码。**

> [!success]- 答案
> 字节数组（byte[]）；Encoding.UTF8.GetBytes()；Encoding.UTF8.GetString()。

---

## 三、代码分析题

**1. 写出按钮点击事件输出结果（假设 textBox1 内容是 "abc"）。**

```csharp
private void btnTest_Click(object sender, EventArgs e)
{
    string msg = textBox1.Text;
    MessageBox.Show("内容：" + msg);
}
```

> [!success]- 答案
> 弹出消息框显示"内容：abc"。`sender` 是 btnTest 按钮，代码中直接用 textBox1 访问其内容。

**2. 三个按钮共用一个事件，如何知道是哪个按钮被点击？**

> [!success]- 答案
> 强转 sender：`Button b = sender as Button;` 然后用 `b.Text` 或 `b.Name` 区分。多个按钮 `Click += 同一方法`。

**3. 下列代码有什么问题？如何修改？**

```csharp
Thread t = new Thread(() =>
{
    label1.Text = "加载完成";   // 在线程中直接改控件
});
t.Start();
```

> [!success]- 答案
> 跨线程直接访问控件会抛异常。应改为：
> ```csharp
> t = new Thread(() =>
> {
>     label1.BeginInvoke(new Action(() => label1.Text = "加载完成"));
> });
> ```

**4. Socket 客户端连接服务器的核心代码？**

> [!success]- 答案
> ```csharp
> Socket s = new Socket(AddressFamily.InterNetwork, SocketType.Stream, ProtocolType.Tcp);
> s.Connect(new IPEndPoint(IPAddress.Parse("127.0.0.1"), 8888));
> // 发送
> s.Send(Encoding.UTF8.GetBytes("你好"));
> // 接收（后台线程）
> byte[] buf = new byte[1024];
> int r = s.Receive(buf);
> string msg = Encoding.UTF8.GetString(buf, 0, r);
> ```

---

## 四、场景设计题

**1. 设计一个简易计算器界面：两个 TextBox 输入数字，一个 ComboBox 选运算符，一个 Button 计算，一个 Label 显示结果。写出关键代码。**

> [!success]- 答案
> ```csharp
> private void btnCalc_Click(object sender, EventArgs e)
> {
>     double a = double.Parse(textBox1.Text);
>     double b = double.Parse(textBox2.Text);
>     double result = comboBox1.SelectedItem.ToString() switch
>     {
>         "+" => a + b,
>         "-" => a - b,
>         "*" => a * b,
>         "/" => b != 0 ? a / b : 0,
>         _ => 0
>     };
>     label1.Text = "结果：" + result;
> }
> ```

**2. 实现登录窗体 → 主窗体的跳转（登录成功用 ShowDialog 打开主窗体）。**

> [!success]- 答案
> ```csharp
> // 登录按钮
> private void btnLogin_Click(object sender, EventArgs e)
> {
>     if (textBox1.Text == "admin" && textBox2.Text == "123456")
>     {
>         MainForm main = new MainForm();
>         main.Show();          // 打开主窗体
>         this.Hide();          // 隐藏登录窗体
>     }
>     else
>     {
>         MessageBox.Show("账号或密码错误");
>     }
> }
> ```

**3. 设计一个倒计时器：Timer 每 1 秒触发，Label 从 10 递减到 0 停止。**

> [!success]- 答案
> ```csharp
> int count = 10;
> private void Form1_Load(object sender, EventArgs e)
> {
>     timer1.Interval = 1000;
>     timer1.Start();
> }
> private void timer1_Tick(object sender, EventArgs e)
> {
>     count--;
>     label1.Text = count.ToString();
>     if (count <= 0)
>     {
>         timer1.Stop();
>         MessageBox.Show("时间到");
>     }
> }
> ```

**4. 实现一个简单聊天客户端：连接、发送、接收（线程 + BeginInvoke）。**

> [!success]- 答案
> ```csharp
> Socket socket;
> Thread recvThread;
>
> // 连接
> private void btnConnect_Click(object sender, EventArgs e)
> {
>     socket = new Socket(AddressFamily.InterNetwork, SocketType.Stream, ProtocolType.Tcp);
>     socket.Connect(new IPEndPoint(IPAddress.Parse(textBox1.Text), 8888));
>     recvThread = new Thread(ReceiveLoop) { IsBackground = true };
>     recvThread.Start();
> }
>
> // 接收
> private void ReceiveLoop()
> {
>     while (true)
>     {
>         byte[] buf = new byte[1024];
>         int r = socket.Receive(buf);
>         if (r == 0) break;
>         string msg = Encoding.UTF8.GetString(buf, 0, r);
>         richTextBox1.BeginInvoke(new Action(() =>
>             richTextBox1.AppendText("对方：" + msg + "\r\n")));
>     }
> }
>
> // 发送
> private void btnSend_Click(object sender, EventArgs e)
> {
>     socket.Send(Encoding.UTF8.GetBytes(textBox3.Text));
>     richTextBox1.AppendText("我：" + textBox3.Text + "\r\n");
>     textBox3.Clear();
> }
> ```

**5. 窗体之间传递数据（主窗体 → 子窗体）有哪些方式？**

> [!success]- 答案
> 1. **构造函数传参**：`ChildForm child = new ChildForm("数据");`
> 2. **公共属性/字段**：`child.UserName = "张三";`
> 3. **静态变量**：`GlobalData.Name = "张三";`
> 4. 事件回调（子窗体 → 主窗体）

---

## 知识卡片

**WinForms 事件机制？**
> 事件三要素：控件（提供者）+ 事件（如 Click）+ 处理方法。用 `+=` 订阅、`-=` 取消。

**事件处理方法签名？**
> `private void 方法名(object sender, EventArgs e)`。sender 是触发控件（可强转），e 是事件信息。

**控件绑定事件的方式？**
> 设计器绑定（属性→闪电图标→双击）或代码绑定（`控件.事件 += 方法`）。学习阶段建议代码绑定。

**跨线程更新 UI？**
> 用 `Control.BeginInvoke(new Action(...))` 把操作封送回 UI 线程；直接改会抛跨线程异常。

**Show 与 ShowDialog？**
> Show 非模态（可同时操作父窗体）；ShowDialog 模态（阻塞，关闭后才能返回）。

**Dock 与 Anchor？**
> Dock 停靠（Fill/Top/Bottom/Left/Right）；Anchor 锚定（窗体缩放时控件相对边距不变）。

**Timer 控件？**
> 周期性触发 Tick 事件，Interval 单位毫秒（1000 = 1 秒），Start/Stop 控制。

**Thread 与 ThreadPool？**
> Thread 手动创建线程；ThreadPool 线程池复用线程（QueueUserWorkItem）。

**Socket TCP 通信流程？**
> 服务端：Bind → Listen → Accept → 收发；客户端：Connect → 收发。收发字节数组，需编码/解码。

**Socket 断开的顺序？**
> Shutdown(SocketShutdown.Both) 禁止收发 → Close() 关闭 → 结束线程。用标志位优雅退出优于 Abort()。

---

## 相关笔记

- [[Winform/00-WinForms学习笔记总览]] — WinForms 总览
- [[Winform/01-WinForm基础/01-项目结构与部分类]] — 项目结构
- [[Winform/02-事件机制/01-事件的基本使用]] — 事件机制
- [[Winform/03-常用控件/09-控件速查表]] — 控件速查
- [[Winform/04-布局管理/02-Dock与Anchor]] — 布局
- [[Winform/05-多窗体与数据传递/02-窗体之间数据传递]] — 数据传递
- [[Winform/07-多线程编程/01-Thread线程]] — 多线程
- [[Winform/07-多线程编程/03-跨线程调用控件BeginInvoke]] — 跨线程
- [[Winform/11-Socket网络编程/00-Socket基础与TCP通信流程]] — Socket 基础
- [[12-复习巩固/00-复习总览]] — 复习总览
