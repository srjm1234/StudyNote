---
title: Socket 代码问题分析
date: 2026-08-14
tags:
  - CSharp
  - WinForms
  - Socket
  - 网络编程
source: D:\study\C#1\WinForm\stu0814
---

# Socket 代码问题分析

> 汇总 stu0814 三个项目（客户端 `stu0814/`、服务端 `Server端/`、AntdUI 版 `Server服务/`）代码中的常见问题，是学习 Socket 编程的**反面教材**。实现笔记见 [[Winform/11-Socket网络编程/01-Socket客户端实现]] 与 [[Winform/11-Socket网络编程/02-Socket服务端实现]]。

## 问题一：编码不一致导致中文乱码

**位置**：客户端 `stu0814\Form1.cs` 的 `Receive` 方法

```csharp
// 发送时用 UTF8
byte[] buffer = Encoding.UTF8.GetBytes(str);

// 接收时却用 ASCII
string msg = Encoding.ASCII.GetString(buffer);
```

**后果**：发送中文时，客户端收到乱码。

**解决**：发送和接收**必须使用同一种编码**，中文场景统一用 `Encoding.UTF8`。

## 问题二：解码时未按实际字节数截断

**位置**：三个项目都有，如服务端 `Server端\Form1.cs` 的 `Recive`

```csharp
byte[] buffer = new byte[1024 * 1024 * 2];  // 2MB 缓冲区
int r = serverConnet.Receive(buffer);        // 实际只收到 5 字节
string str = Encoding.UTF8.GetString(buffer); // ❌ 整个 2MB 都解码了
```

**后果**：消息尾部带大量 `\0` 空字符，显示异常、占内存。

**解决**：用 `GetString(buffer, 0, r)` 只解码实际接收的部分：

```csharp
string str = Encoding.UTF8.GetString(buffer, 0, r);
```

## 问题三：单连接设计，多客户端互相覆盖

**位置**：服务端 `Server端\Form1.cs`

```csharp
Socket serverConnet = null;   // 成员变量，只有一个

private void Listen(object o)
{
    serverConnet = Accept.Accept();   // 新连接覆盖旧连接
    t2 = new Thread(Recive);
    t2.Start(serverConnet);
}

private void Recive(object o)
{
    serverConnet = o as Socket;   // 再次覆盖！
    // ...接收
}
```

**后果**：多个客户端连接时，成员变量被反复覆盖，接收线程操作的是同一个变量，连接管理混乱。

**解决**：用 `List<Socket>` 或字典管理多个连接；接收线程使用**局部变量**保存自己的套接字：

```csharp
private void Recive(object o)
{
    Socket client = o as Socket;   // 局部变量，每个线程独立
    while (true)
    {
        int r = client.Receive(buffer);
        // ...
    }
}
```

## 问题四：用 Thread.Abort() 强制杀线程

**位置**：客户端断开、服务端关闭时

```csharp
t1.Abort();
t2.Abort();
```

**后果**：`Abort()` 会直接抛出 `ThreadAbortException` 中断线程，可能造成**资源未释放**（如 Socket 未 Close）、数据不一致。.NET Core 3.0+ 中 `Thread.Abort` 已不再支持。

**解决**：使用**标志位**优雅退出循环：

```csharp
private volatile bool _running = true;

private void Receive(object o)
{
    Socket socket = o as Socket;
    while (_running)
    {
        int r = socket.Receive(buffer);
        if (r == 0) break;
        // ...
    }
}

// 关闭时：
_running = false;
socket.Close();   // Receive 会抛异常或返回 0，线程自然退出
```

## 问题五：空 catch 吞掉异常

**位置**：多个 try-catch

```csharp
catch (Exception ex) { }
```

**后果**：出错时没有任何提示，`Bind` 失败（端口被占用）、`Accept` 失败（Socket 已关闭）等问题完全无感知，调试极难。

**解决**：至少记录日志或提示：

```csharp
catch (Exception ex)
{
    MessageBox.Show("操作失败：" + ex.Message);
    // 或写入日志文件
}
```

## 问题六：无效/未完成代码

| 位置 | 代码 | 问题 |
|------|------|------|
| 客户端 `UpdateState` | `if (this.InvokeRequired) { }` | 空方法，服务器断开时界面无反馈 |
| 客户端 `AddMsg` | `string str1 = flag ? str : str;` | 三元表达式恒等，无意义 |
| 客户端发送 catch | 只声明了 `logDir`、`logFile` 未使用 | 日志功能未实现 |
| 服务端 `Server服务`（AntdUI 版） | `Listen` 中只 `Accept()` 不接收 | 只监听不通信，功能不完整 |
| 服务端按钮点击 | 多个事件方法（`inputPanel_TextChanged` 等）为空 | 模板残留 |

## 问题七：日志目录拼接错误（客户端）

**位置**：客户端发送消息的 catch 块

```csharp
string logDir = Directory.GetCurrentDirectory() + "\\AllLog";
string logFile = logDir + "\\log.txt";
// 之后没有任何写入操作
```

**解决**：真正写入日志：

```csharp
Directory.CreateDirectory(logDir);
File.AppendAllText(logFile, DateTime.Now + " " + ex.Message + "\n");
```

## 正确写法参考

### 服务端核心（改进版）

```csharp
public partial class Form1 : Form
{
    Socket listenSocket;
    List<Socket> clients = new List<Socket>();   // 管理多个连接
    private volatile bool _running = true;

    private void btnStart_Click(object sender, EventArgs e)
    {
        listenSocket = new Socket(AddressFamily.InterNetwork, SocketType.Stream, ProtocolType.Tcp);
        listenSocket.Bind(new IPEndPoint(IPAddress.Any, 8888));
        listenSocket.Listen(10);
        Thread listenThread = new Thread(AcceptLoop) { IsBackground = true };
        listenThread.Start();
    }

    private void AcceptLoop()
    {
        while (_running)
        {
            try
            {
                Socket client = listenSocket.Accept();
                clients.Add(client);
                Thread recvThread = new Thread(() => ReceiveLoop(client)) { IsBackground = true };
                recvThread.Start();
            }
            catch (SocketException) { break; }   // Socket 关闭时退出
        }
    }

    private void ReceiveLoop(Socket client)   // 局部变量，每连接独立
    {
        byte[] buffer = new byte[1024];
        while (_running)
        {
            int r = client.Receive(buffer);
            if (r == 0) break;
            string msg = Encoding.UTF8.GetString(buffer, 0, r);   // 按实际长度解码
            // BeginInvoke 更新 UI...
        }
        clients.Remove(client);
        client.Close();
    }
}
```

## 相关笔记

- [[Winform/11-Socket网络编程/00-Socket基础与TCP通信流程]] — 基础概念
- [[Winform/11-Socket网络编程/01-Socket客户端实现]] — 客户端实现（含问题代码）
- [[Winform/11-Socket网络编程/02-Socket服务端实现]] — 服务端实现（含问题代码）
- [[Winform/07-多线程编程/01-Thread线程]] — 线程基础
- [[Winform/07-多线程编程/03-跨线程调用控件BeginInvoke]] — 跨线程更新 UI
- [[02-流程控制/流程控制]] — 异常处理
