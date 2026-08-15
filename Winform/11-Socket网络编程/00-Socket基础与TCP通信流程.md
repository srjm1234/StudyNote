---
title: Socket 基础与 TCP 通信流程
date: 2026-08-14
tags:
  - CSharp
  - WinForms
  - Socket
  - 网络编程
source: D:\study\C#1\WinForm\stu0814
---

# Socket 基础与 TCP 通信流程

> 通过 `System.Net.Sockets` 命名空间下的 `Socket` 类实现 TCP/IP 网络通信。本篇是 [[Winform/11-Socket网络编程/01-Socket客户端实现|客户端]] 与 [[Winform/11-Socket网络编程/02-Socket服务端实现|服务端]] 两篇笔记的基础。

## 什么是 Socket

Socket（套接字）是网络通信的端点，程序通过它与其他计算机上的程序交换数据。C# 中 `Socket` 类封装了底层网络协议，最常用的是 **TCP**（面向连接、可靠）和 UDP（无连接）。

## 创建 Socket

```csharp
// 第一个参数：地址族（IPv4）
// 第二个参数：通信类型（双向数据流）
// 第三个参数：协议（TCP）
Socket socket = new Socket(AddressFamily.InterNetwork, SocketType.Stream, ProtocolType.Tcp);
```

| 参数 | 常用值 | 说明 |
|------|--------|------|
| `AddressFamily` | `InterNetwork` | IPv4 地址族 |
| `SocketType` | `Stream` | 双向可靠的数据流（TCP 用） |
| `ProtocolType` | `Tcp` | TCP 协议 |

## IP 地址与端口

```csharp
// 将 IP 字符串解析为 IPAddress 对象
IPAddress ip = IPAddress.Parse("127.0.0.1");

// 端口号（0~65535），通常使用 1024 以上
int port = 8888;

// 组合成网络端点（IP + 端口）
IPEndPoint iPEndPoint = new IPEndPoint(ip, port);
```

## TCP 通信流程

### 服务端流程

```
1. 创建 Socket（TCP）
2. Bind(iPEndPoint)       — 绑定 IP 和端口
3. Listen(数量)           — 开启监听（最多等待 N 个连接请求）
4. Accept()               — 接受客户端连接（阻塞，直到有客户端连接）
5. Receive(buffer)        — 接收客户端发来的数据（阻塞）
6. Send(byte[])           — 向客户端发送数据
7. Shutdown(Both) + Close — 关闭连接
```

### 客户端流程

```
1. 创建 Socket（TCP）
2. Connect(iPEndPoint)    — 连接服务器（指定服务器 IP + 端口）
3. Send(byte[])           — 发送数据
4. Receive(buffer)        — 接收服务器数据（阻塞）
5. Shutdown(Both) + Close — 关闭连接
```

> **核心特点**：`Accept()` 和 `Receive()` 都是**阻塞方法**——调用后程序会停在那里等待，直到有客户端连接或有数据到达。所以服务端通常把它们放到**单独的线程**里执行，避免界面卡死。

## 字节与字符串的转换

Socket 传输的是**字节数组**，字符串需要先编码再发送、接收后再解码：

```csharp
// 字符串 → 字节数组（发送前）
byte[] buffer = Encoding.UTF8.GetBytes("要发送的内容");

// 字节数组 → 字符串（接收后）
string msg = Encoding.UTF8.GetString(buffer);
```

| 编码 | 特点 |
|------|------|
| `Encoding.UTF8` | 支持中文，推荐 |
| `Encoding.ASCII` | 仅支持英文字符，中文会乱码 |
| `Encoding.Default` | 系统默认编码 |

## 线程与跨线程更新 UI

网络通信中常用**多线程**：

| 线程 | 职责 |
|------|------|
| 监听线程 | 服务端：循环 `Accept()` 等待客户端连接 |
| 接收线程 | 每个连接一个，循环 `Receive()` 收数据 |

线程中**不能直接操作 UI 控件**（会抛跨线程异常），需要用 `BeginInvoke` 把更新 UI 的操作交回主线程：

```csharp
// 线程中更新 RichTextBox
this.richTextBox1.BeginInvoke(new Action<string>((msg) =>
{
    richTextBox1.Text += msg + "\r\n";
}), msg);
```

> 跨线程调用控件的完整讲解见 [[Winform/07-多线程编程/03-跨线程调用控件BeginInvoke]]。

## 相关笔记

- [[Winform/11-Socket网络编程/01-Socket客户端实现]] — 客户端完整实现
- [[Winform/11-Socket网络编程/02-Socket服务端实现]] — 服务端完整实现
- [[Winform/11-Socket网络编程/03-Socket代码问题分析]] — 常见坑点
- [[Winform/07-多线程编程/01-Thread线程]] — 线程基础
- [[Winform/07-多线程编程/03-跨线程调用控件BeginInvoke]] — 跨线程更新 UI
