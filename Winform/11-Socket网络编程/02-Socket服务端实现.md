---
title: Socket 服务端实现
date: 2026-08-14
tags:
  - CSharp
  - WinForms
  - Socket
  - 网络编程
source: D:\study\C#1\WinForm\stu0814\Server端
---

# Socket 服务端实现

> 对应源码：`D:\study\C#1\WinForm\stu0814\Server端\Form1.cs`（原生 UI 版服务端）。
> 基础概念见 [[Winform/11-Socket网络编程/00-Socket基础与TCP通信流程]]，客户端见 [[Winform/11-Socket网络编程/01-Socket客户端实现]]。

## 功能概述

聊天服务器，包含：**开启监听 → 接受连接 → 接收/发送消息 → 关闭服务器**，支持多个客户端连接。

## 完整代码

```csharp
public partial class Form1 : Form
{
    Socket serverSocket = null;   // 监听套接字
    Socket serverConnet = null;   // 与客户端通信的套接字
    Socket Accept = null;         // 当前接受的连接
    Thread t1;   // 监听线程
    Thread t2;   // 接收信息线程

    // ── 1. 开启服务器 ──
    private void button1_Click(object sender, EventArgs e)
    {
        this.button1.Enabled = false;
        this.button2.Enabled = true;

        serverSocket = new Socket(AddressFamily.InterNetwork, SocketType.Stream, ProtocolType.Tcp);
        IPAddress ip = IPAddress.Parse(textBox1.Text);
        int port = Convert.ToInt32(textBox2.Text);
        IPEndPoint iPEndPoint = new IPEndPoint(ip, port);
        try
        {
            serverSocket.Bind(iPEndPoint);   // 绑定 IP + 端口
            serverSocket.Listen(5);          // 开启监听（最多 5 个等待连接）
            button4.Text = "服务器已开启";
            button4.BackColor = Color.Green;

            // 开一个线程专门监听
            t1 = new Thread(Listen);
            t1.IsBackground = true;
            t1.Start(serverSocket);
        }
        catch (Exception Ex) { }
    }

    // ── 2. 监听线程：循环接受连接 ──
    private void Listen(object o)
    {
        Accept = o as Socket;
        try
        {
            while (true)
            {
                // 线程阻塞：有用户连接才会执行下面的代码
                serverConnet = Accept.Accept();
                string str = $"{DateTime.Now.ToString()}:{serverConnet.RemoteEndPoint}已连接";
                AddMsg(str, false);

                // 每个连接开启一个接收信息线程
                t2 = new Thread(Recive);
                t2.IsBackground = true;
                t2.Start(serverConnet);
            }
        }
        catch (Exception Ex) { }
    }

    // ── 3. 接收线程：循环接收消息 ──
    private void Recive(object o)
    {
        serverConnet = o as Socket;
        while (true)
        {
            byte[] buffer = new byte[1024 * 1024 * 2];   // 2MB 缓冲区
            int r = serverConnet.Receive(buffer);       // 阻塞接收
            if (r == 0)
            {
                break;   // 客户端断开
            }
            string str = Encoding.UTF8.GetString(buffer);  // 解码
            string msg = $"{DateTime.Now.ToString()}: {serverConnet.RemoteEndPoint} < = {str}";
            AddMsg(msg, false);
        }
    }

    // ── 4. 发送消息 ──
    private void button3_Click(object sender, EventArgs e)
    {
        string str = this.richTextBox2.Text;
        byte[] bytArr = Encoding.UTF8.GetBytes(str);
        try
        {
            if (serverConnet != null)
            {
                serverConnet.Send(bytArr);
                AddMsg($"My = >{str}");
                richTextBox2.Clear();
            }
        }
        catch (Exception Ex) { }
    }

    // ── 5. 关闭服务器 ──
    private void button2_Click(object sender, EventArgs e)
    {
        if (serverConnet != null)
        {
            serverConnet.Shutdown(SocketShutdown.Both);
            serverConnet.Close();
            t2.Abort();
        }
        Accept.Close();
        serverSocket.Close();
        t1.Abort();
        button4.BackColor = Color.Red;
        button4.Text = "服务器未开启";
        button1.Enabled = true;
        button2.Enabled = false;
    }

    // ── 封装显示消息（跨线程更新 UI） ──
    private void AddMsg(string str, bool flag = true)
    {
        string str1 = flag ? str : "< =" + str;
        this.richTextBox1.BeginInvoke(new Action<string>((msg) =>
        {
            richTextBox1.Text += msg;
            richTextBox1.Text += "\r\n";
        }), str1);
    }
}
```

## 核心流程解析

### 1. 开启服务器（Bind + Listen）

```csharp
serverSocket.Bind(iPEndPoint);   // 绑定到指定 IP + 端口
serverSocket.Listen(5);          // 开始监听，参数 = 最大等待连接数
```

服务端与客户端的本质区别：

| 动作 | 客户端 | 服务端 |
|------|--------|--------|
| `Bind` | ❌ 不需要 | ✅ 必须绑定本地 IP + 端口 |
| `Listen` | ❌ 不需要 | ✅ 开启监听，等待连接 |
| `Connect` | ✅ 主动连接服务器 | ❌ 不需要 |
| `Accept` | ❌ 不需要 | ✅ 接受客户端连接 |

### 2. 监听线程（循环 Accept）

```csharp
t1 = new Thread(Listen);   // 监听不能阻塞主线程
while (true)
{
    serverConnet = Accept.Accept();  // 阻塞等待，有客户端连接才继续
    // 每接受一个连接，开一个接收线程
    t2 = new Thread(Recive);
    t2.Start(serverConnet);
}
```

**关键设计**：监听线程里用 `while(true)` 循环 `Accept()`，保证服务器能持续接受**多个客户端**；每接受一个连接就开一个独立接收线程，实现并发通信。

### 3. 接收线程（循环 Receive）

```csharp
byte[] buffer = new byte[1024 * 1024 * 2];  // 2MB 缓冲区
int r = serverConnet.Receive(buffer);        // 阻塞，直到收到数据
if (r == 0) break;                           // 返回 0 = 客户端已断开
string str = Encoding.UTF8.GetString(buffer);
```

### 4. 发送消息（Send）

```csharp
serverConnet.Send(bytArr);
```

服务端向**已连接的客户端**发送数据，用的是 `Accept()` 返回的连接套接字，不是监听套接字。

### 5. 关闭服务器

```csharp
serverConnet.Shutdown(SocketShutdown.Both);  // 停止收发
serverConnet.Close();                        // 关闭客户端连接
Accept.Close();                              // 关闭监听
serverSocket.Close();                        // 关闭服务器套接字
t1.Abort(); t2.Abort();                      // 结束线程
```

## 代码中的问题（详见 [[Winform/11-Socket网络编程/03-Socket代码问题分析]]）

| 问题 | 位置 | 说明 |
|------|------|------|
| 单连接设计 | `Recive` 使用成员变量 `serverConnet` | 多客户端时接收线程互相覆盖，只保留最后一个 |
| 未按实际长度解码 | `GetString(buffer)` 未用 `r` 截断 | 2MB 缓冲区解码后空字节较多，消息尾部有 `\0` |
| `t.Abort()` | 关闭服务器 | 强制杀线程不推荐 |
| 空 catch | `catch (Exception Ex) { }` | 吞异常，问题难排查 |
| `Encoding.UTF8.GetString(buffer)` | 接收解码 | 应使用 `GetString(buffer, 0, r)` |

## 相关笔记

- [[Winform/11-Socket网络编程/00-Socket基础与TCP通信流程]] — 基础概念
- [[Winform/11-Socket网络编程/01-Socket客户端实现]] — 客户端对应实现
- [[Winform/11-Socket网络编程/03-Socket代码问题分析]] — 代码问题汇总
- [[Winform/07-多线程编程/01-Thread线程]] — 线程基础
- [[Winform/07-多线程编程/03-跨线程调用控件BeginInvoke]] — 跨线程更新 UI
