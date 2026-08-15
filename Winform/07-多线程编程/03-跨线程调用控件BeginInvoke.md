---
title: 跨线程调用控件 BeginInvoke
tags:
  - CSharp
  - WinForms
  - 多线程
---

# 跨线程调用控件：BeginInvoke

> **来源**：`D:\study\C#1\WinForm\stu0812\stu0813\跨线程调用控件`

多线程里最常踩的坑：**子线程不能直接操作 UI 控件**。解决办法是 `BeginInvoke`。

---

## 1. 问题：跨线程操作控件会抛异常

WinForm 控件**只能在创建它的线程（主线程/UI 线程）中访问**。从其他线程直接操作控件会抛出 `InvalidOperationException`：

> "线程间操作无效: 从不是创建控件"textBox1"的线程访问它。"

```csharp
private void Add()
{
    // ❌ 子线程里直接操作控件 → 抛异常
    textBox1.Text = "这是第一段文本";
}
```

## 2. 解决方案：BeginInvoke

`BeginInvoke` 把更新操作**排队到 UI 线程执行**，是安全的跨线程更新方式：

```csharp
private void button1_Click(object sender, EventArgs e)
{
    new Thread(Add).Start();   // 启动子线程
}

private void Add()
{
    List<string> list = new List<string>();
    list.Add("这是第一段文本");
    list.Add("这是第二段文本");

    for (int i = 0; i < list.Count; i++)
    {
        Thread.Sleep(2000);   // 模拟耗时

        // ✅ 通过 BeginInvoke 回到主线程更新控件
        textBox1.BeginInvoke(new Action<string>((value) =>
        {
            textBox1.Text += value + "  ";
        }), list[i]);
    }
}
```

> 💡 原理：`BeginInvoke(委托, 参数)` 把「更新控件的代码」打包成委托，投递到 UI 线程的消息队列，UI 线程空闲时执行。

## 3. 不推荐的方式（了解即可）

```csharp
// ❌ 关闭跨线程检查（会导致不可预知的 bug，学习阶段不建议）
CheckForIllegalCrossThreadCalls = false;
```

> ⚠️ 关闭检查只是不报错了，但多线程同时操作控件仍然可能产生**竞态**（显示错乱、崩溃），正确做法是 `BeginInvoke`。

## 4. 常见写法对比

```csharp
// 写法一：BeginInvoke + 匿名委托（推荐）
textBox1.BeginInvoke(new Action(() =>
{
    textBox1.Text = "更新内容";
}));

// 写法二：带参数
textBox1.BeginInvoke(new Action<string>((msg) =>
{
    textBox1.Text = msg;
}), "内容");

// 写法三：直接传方法
private void UpdateText(string msg)
{
    if (textBox1.InvokeRequired)          // 判断是否跨线程
    {
        textBox1.BeginInvoke(new Action<string>(UpdateText), msg);
        return;
    }
    textBox1.Text = msg;
}
```

> 💡 `InvokeRequired` 判断当前是否在 UI 线程：`true` 说明跨线程了，需要 `BeginInvoke` 转回去。

## 5. Invoke vs BeginInvoke

| 对比 | Invoke | BeginInvoke |
|------|--------|-------------|
| 等待 | 等待 UI 线程执行完 | 不等待，立即返回 |
| 阻塞 | 会阻塞子线程 | 不阻塞 |
| 适用 | 需要同步结果 | 异步更新界面（推荐） |

> 💡 **要点**：`BeginInvoke` 将更新操作排队到 UI 线程执行，是安全的跨线程方案。做耗时任务 + 进度条时，进度更新就用它。


---

## 相关笔记

- [[Winform/07-多线程编程/01-Thread线程]] — Thread 线程
- [[Winform/07-多线程编程/02-ThreadPool线程池]] — 线程池
