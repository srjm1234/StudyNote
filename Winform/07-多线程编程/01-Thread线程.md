---
title: Thread 线程
tags:
  - CSharp
  - WinForms
  - 多线程
---

# Thread 线程

> **来源**：`D:\study\C#1\WinForm\stu0812\多线程`、`stu0812\进度条`

用多线程让程序「同时」做多件事：界面不卡顿的同时执行耗时操作。

---

## 1. 什么是线程

- **主线程**：`Main()` 函数所在的线程，负责界面响应（画界面、处理点击）
- **子线程**：`new Thread()` 创建的线程，负责耗时任务（下载、计算、读文件）
- 主线程被耗时操作阻塞 → 界面「假死」→ 用子线程解决

## 2. 创建与启动线程

```csharp
using System.Threading;

// 创建线程（传入要执行的方法）
Thread thread = new Thread(TestMethod);
thread.IsBackground = true;   // 设置为后台线程
thread.Start();               // 启动线程

// 线程方法（无参数）
private void TestMethod()
{
    Thread.Sleep(5000);       // 休眠 5 秒（模拟耗时）
    MessageBox.Show("嘿嘿");
}
```

### 带参数的线程

```csharp
string str = "视觉88班";

// 启动线程时传值
Thread thread2 = new Thread(TestMethod2);
thread2.Start(str);     // 参数必须是 object 类型

// 线程方法（接收 object 参数）
private void TestMethod2(object item)
{
    string str = item.ToString();
    Thread.Sleep(5000);
    MessageBox.Show(str);
}
```

> 💡 线程方法有参数时，**必须**接收 `object` 类型，`Start(参数)` 传进去。

## 3. 前台线程 vs 后台线程

| 类型 | 设置 | 行为 |
|------|------|------|
| 前台线程 | `IsBackground = false`（默认） | 只要有一个前台线程在运行，程序就不会退出 |
| 后台线程 | `IsBackground = true` | 所有前台线程结束后，后台线程被强制关闭 |

```csharp
// 主线程(Main)就是前台线程
// 前台线程：用户更新界面、必须完成的操作
// 后台线程：打辅助，比如后台监控

Thread th1 = new Thread(Huo);
th1.IsBackground = true;   // 后台线程
th1.Start();
```

> 💡 **推荐**：WinForm 中把耗时操作放到**后台线程**（`IsBackground = true`），避免程序退出时线程还挂着。

## 4. 完整示例：按钮点击 → 子线程执行耗时操作

```csharp
private void button2_Click(object sender, EventArgs e)
{
    // 创建并实例化线程（传入方法）
    var th1 = new Thread(Huo);
    // true = 后台线程，false = 前台线程
    th1.IsBackground = true;
    // 开启线程
    th1.Start();
}

private void Huo()
{
    Thread.Sleep(5000);          // 耗时操作：界面不卡
    MessageBox.Show("嘿嘿");
}
```

> 💡 对比：如果直接在按钮事件里写 `Thread.Sleep(5000)`，整个窗口会卡住 5 秒（假死）。放进子线程就不会。

## 5. 常用成员

| 成员 | 说明 |
|------|------|
| `Thread.Sleep(毫秒)` | 当前线程休眠 |
| `thread.Start()` | 启动线程 |
| `thread.IsBackground` | 是否后台线程 |
| `thread.Name` | 线程名（调试用） |
| `Thread.CurrentThread` | 当前线程 |
| `thread.Join()` | 等待该线程结束 |

> ⚠️ **警告**：子线程里**不能直接操作 UI 控件**（会抛异常），需要跨线程调用控件（见 `07-多线程编程/03-跨线程调用控件BeginInvoke`）。


---

## 相关笔记

- [[Winform/07-多线程编程/02-ThreadPool线程池]] — 线程池
- [[Winform/07-多线程编程/03-跨线程调用控件BeginInvoke]] — 跨线程
- [[Winform/03-常用控件/08-定时器Timer与进度条ProgressBar]] — Timer 与进度条
