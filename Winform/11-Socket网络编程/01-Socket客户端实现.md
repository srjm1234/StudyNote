---
title: Socket 客户端实现
date: 2026-08-14
tags:
  - CSharp
  - WinForms
  - Socket
  - 网络编程
source: D:\study\C#1\WinForm\stu0814\stu0814
---

# Socket 客户端实现

> 对应源码：`D:\study\C#1\WinForm\stu0814\stu0814\Form1.cs`（原生 UI 版客户端）。
> 基础概念见 [[Winform/11-Socket网络编程/00-Socket基础与TCP通信流程]]。

## 功能概述

聊天客户端，包含四个操作：**连接服务器 → 接收消息 → 发送消息 → 断开连接**。

## 完整代码

```csharp
public partial class Form1 : Form
{
    // 通过 Socket 类实现 TCP/IP 通信
    Socket lianSocket = null;
    Thread t1;

    // ── 1. 连接服务器 ──
    private void button1_Click(object sender, EventArgs e)
    {
        // 实例化 Socket：协议类型、双向数据流、TCP
        lianSocket = new Socket(AddressFamily.InterNetwork, SocketType.Stream, ProtocolType.Tcp);
        try
        {
            // 绑定 IP 地址
            IPAddress ip = IPAddress.Parse(textBox1.Text);
            // 获取端口号
            int port = Convert.ToInt32(textBox2.Text);
            // 形成连接点
            IPEndPoint iPEndPoint = new IPEndPoint(ip, port);

            // 连接服务器
            lianSocket.Connect(iPEndPoint);
            this.button4.BackColor = Color.Green;
            this.button4.Text = "已连接";
            this.button1.Enabled = false;   // 连接后禁用连接按钮
            this.button2.Enabled = true;    // 启用断开按钮

            // 开一个后台线程监听消息
            t1 = new Thread(Receive);
            t1.IsBackground = true;
            t1.Start(lianSocket);  // 开启线程时传入 Socket 对象
        }
        catch (Exception ex)
        {
            MessageBox.Show(ex.ToString());
        }
    }

    // ── 2. 接收消息（后台线程） ──
    private void Receive(object o)
    {
        Socket socket = o as Socket;
        while (true)
        {
            byte[] buffer = new byte[1024];
            // r 是接收到的字节个数
            int r = socket.Receive(buffer);
            if (r == 0)
            {
                UpdateState(false);  // 服务器断开
                break;
            }
            // 字节数组 → 字符串
            string msg = Encoding.ASCII.GetString(buffer);
            AddMsg(msg, false);
        }
    }

    // ── 3. 发送消息 ──
    private void button3_Click(object sender, EventArgs e)
    {
        string str = richTextBox2.Text;
        if (string.IsNullOrEmpty(str))
        {
            return;
        }
        try
        {
            byte[] buffer = Encoding.UTF8.GetBytes(str);  // 字符串 → 字节数组
            lianSocket.Send(buffer);
            AddMsg(str);   // 自己的消息显示在右侧
        }
        catch (Exception ex)
        {
            // TODO: 记录日志
        }
    }

    // ── 4. 断开连接 ──
    private void button2_Click(object sender, EventArgs e)
    {
        if (lianSocket != null)
        {
            lianSocket.Shutdown(SocketShutdown.Both);  // 禁止收发
            lianSocket.Close();                        // 关闭连接
            t1.Abort();                                // 结束接收线程
            button4.BackColor = Color.Red;
            button4.Text = "连接已断开";
            button2.Enabled = false;
            button1.Enabled = true;
        }
    }

    // ── 封装显示消息（跨线程更新 UI） ──
    private void AddMsg(string str, bool a = true)
    {
        string str1 = a ? "= >" + str + "\r\n" : "< =" + str + "\r\n";
        this.richTextBox1.BeginInvoke(
            new Action<string>((msg) => this.richTextBox1.Text += msg), str1);
    }
}
```

## 核心流程解析

### 1. 连接服务器（Connect）

```csharp
lianSocket.Connect(iPEndPoint);
```

- `Connect` 是客户端**主动发起连接**的动作，服务器地址由 `IP + 端口` 组成
- 连接成功后立即开启**后台接收线程**，防止 `Receive()` 阻塞界面
- 界面状态切换：按钮 `Enabled` 互斥（连接后禁用连接、启用断开）

### 2. 接收消息（Receive + 线程）

```csharp
t1 = new Thread(Receive);
t1.IsBackground = true;   // 后台线程：主程序退出时自动结束
t1.Start(lianSocket);     // 把 Socket 传给线程方法
```

接收线程中 `socket.Receive(buffer)` 是**阻塞**的：

- 收到数据 → 返回字节数 `r`
- 对方关闭连接 → 返回 `0`，跳出循环

### 3. 发送消息（Send）

```csharp
byte[] buffer = Encoding.UTF8.GetBytes(str);
lianSocket.Send(buffer);
```

发送前必须**把字符串编码为字节数组**。

### 4. 断开连接（Shutdown + Close）

```csharp
lianSocket.Shutdown(SocketShutdown.Both);  // 禁止收发
lianSocket.Close();                        // 释放资源
```

断开三步：先 `Shutdown`（停止收发）→ 再 `Close`（释放 Socket）→ 最后结束线程。

## 代码中的问题（详见 [[Winform/11-Socket网络编程/03-Socket代码问题分析]]）

| 问题 | 位置 | 说明 |
|------|------|------|
| 编码不一致 | `Receive` 用 `ASCII`，发送用 `UTF8` | 中文会乱码，应统一用 `UTF8` |
| 未按实际长度解码 | `GetString(buffer)` 未用 `r` 截断 | 缓冲区剩余空字节会拼进消息 |
| `UpdateState` 空实现 | `Receive` 中调用 | 服务器断开时界面无反馈 |
| `t1.Abort()` | 断开连接 | 强制杀线程不推荐，建议用标志位 |

## 相关笔记

- [[Winform/11-Socket网络编程/00-Socket基础与TCP通信流程]] — 基础概念
- [[Winform/11-Socket网络编程/02-Socket服务端实现]] — 服务端对应实现
- [[Winform/11-Socket网络编程/03-Socket代码问题分析]] — 代码问题汇总
- [[Winform/07-多线程编程/01-Thread线程]] — 线程基础
- [[Winform/07-多线程编程/03-跨线程调用控件BeginInvoke]] — BeginInvoke 跨线程更新 UI
