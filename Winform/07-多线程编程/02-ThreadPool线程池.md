---
title: ThreadPool 线程池
tags:
  - CSharp
  - WinForms
  - 多线程
---

# ThreadPool 线程池

> **来源**：`D:\study\C#1\WinForm\stu0812\stu0813\stu0813`

线程池：预先创建好一批线程**反复使用**，避免频繁创建/销毁线程的开销。适合大量短任务。

---

## 1. 基本用法

```csharp
using System.Threading;

// 排队一个工作项（线程池会安排空闲线程执行）
ThreadPool.QueueUserWorkItem(Test);

private void Test(object state)
{
    Thread.Sleep(5000);
    MessageBox.Show("这是一个线程池");
}
```

> 💡 `QueueUserWorkItem` = 「把任务丢进线程池排队」，线程池自动分配线程执行，**无需手动管理线程生命周期**。

## 2. 带参数的工作项（Lambda 写法）

```csharp
string str = "传入数据";

ThreadPool.QueueUserWorkItem((value) =>
{
    string r = value as string;      // 参数是 object 类型
    Thread.Sleep(5000);
    MessageBox.Show(r);
}, str);    // 第二个参数 = 传给 Lambda 的数据
```

> 💡 Lambda（箭头函数）写法：`(value) => { ... }`，适合简短的逻辑，不用单独定义方法。

## 3. 线程池 vs 手动创建线程

| 对比 | Thread | ThreadPool |
|------|--------|------------|
| 创建方式 | `new Thread()` | `ThreadPool.QueueUserWorkItem()` |
| 生命周期 | 手动管理 | 自动管理，重复利用 |
| 性能 | 创建开销大 | 复用线程，开销小 |
| 适用场景 | 长期运行的任务 | 短任务、大量小任务 |

## 4. 完整对比示例

```csharp
// 方式一：手动线程
Thread t1 = new Thread(Test);
t1.IsBackground = true;
t1.Start();

// 方式二：线程池（同一时间排队执行）
ThreadPool.QueueUserWorkItem(((value) =>
{
    var s = value.ToString();
    Thread.Sleep(5000);
    MessageBox.Show(s);
}), textBox1.Text);
```

> 💡 实际开发中，短任务优先用 `ThreadPool`（或 `Task`）；需要精细控制（暂停、取消、设置优先级）时用 `Thread`。

## 5. 注意事项

- 线程池线程默认是**后台线程**，所有前台线程结束后会被强制终止
- 线程池适合**短小任务**，不要在池里做超长循环（会占满线程影响其他任务）
- 线程池里依然**不能直接操作 UI 控件**，需要 `BeginInvoke`（见下一篇）


---

## 相关笔记

- [[Winform/07-多线程编程/01-Thread线程]] — Thread 线程
- [[Winform/07-多线程编程/03-跨线程调用控件BeginInvoke]] — 跨线程
