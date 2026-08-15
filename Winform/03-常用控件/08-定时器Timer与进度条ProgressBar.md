---
title: 定时器 Timer 与进度条 ProgressBar
tags:
  - CSharp
  - WinForms
  - 控件
---

# 定时器 Timer 与进度条 ProgressBar

> **来源**：`D:\study\C#1\WinForm\stu0806\timer`、`stu0812\进度条`

---

## 1. Timer 定时器

按固定间隔反复触发 `Tick` 事件，用于倒计时、时钟、动画、自动刷新等。

### 1.1 基本用法

```csharp
// 设置定时器（设计器或代码）
timer1.Interval = 200;      // 间隔（毫秒），200 = 0.2 秒
timer1.Enabled = true;      // 启用
timer1.Start();             // 启动（等价于 Enabled = true）
timer1.Stop();              // 停止

// Tick 事件：每个间隔触发一次
private void timer1_Tick(object sender, EventArgs e)
{
    toolStripStatusLabel1.Text = DateTime.Now.ToString();   // 状态栏实时时钟
}
```

### 1.2 鼠标悬停暂停/恢复

```csharp
private void Pic1_MouseEnter(object sender, EventArgs e)
{
    timer1.Enabled = false;   // 鼠标移入暂停
}

private void Pic1_MouseLeave(object sender, EventArgs e)
{
    timer1.Start();           // 鼠标移出恢复
}
```

### 1.3 倒计时示例

```csharp
int second = 10;   // 剩余秒数

private void button1_Click(object sender, EventArgs e)
{
    second = 10;
    timer1.Interval = 1000;   // 1 秒
    timer1.Start();
}

private void timer1_Tick(object sender, EventArgs e)
{
    second--;
    label1.Text = "剩余：" + second + " 秒";
    if (second <= 0)
    {
        timer1.Stop();        // 倒计时结束
        MessageBox.Show("时间到！");
    }
}
```

> 💡 **注意**：`Interval` 单位是**毫秒**（1000 = 1 秒）。Timer 的精度不高，不适合高精度计时（用 `Stopwatch`）。

## 2. ProgressBar 进度条

可视化显示任务进度。

### 2.1 基本属性

```csharp
progressBar1.Minimum = 0;      // 最小值
progressBar1.Maximum = 100;    // 最大值
progressBar1.Value = 0;        // 当前值
progressBar1.Step = 5;         // 步长（配合 PerformStep()）
```

### 2.2 模拟进度（for 循环 + 延时）

```csharp
private void button1_Click(object sender, EventArgs e)
{
    for (int i = 0; i <= progressBar1.Maximum; i++)
    {
        progressBar1.Value = i;
        Thread.Sleep(100);      // 模拟耗时操作
    }
    if (progressBar1.Value == 100)
    {
        new hhh().ShowDialog();  // 完成后弹出窗口
    }
}
```

### 2.3 配合 Timer 平滑进度

```csharp
// 每隔 100ms 增加 5，到达 100 停止
private void timer1_Tick(object sender, EventArgs e)
{
    if (progressBar1.Value < progressBar1.Maximum)
    {
        progressBar1.Value += progressBar1.Step;
    }
    else
    {
        timer1.Stop();
        MessageBox.Show("完成！");
    }
}
```

> ⚠️ **重要**：在按钮点击事件里直接 `Thread.Sleep` 会**卡死界面**（UI 线程被阻塞）。真实项目中要用多线程 + 进度条（见 `07-多线程编程`），学习阶段用这个写法理解原理即可。

## 3. 搭配使用：带进度的加载效果

| 组件 | 职责 |
|------|------|
| Timer | 定时推进进度 |
| ProgressBar | 显示进度 |
| StatusStrip + ToolStripProgressBar | 在状态栏显示进度 |
| Label | 显示百分比文字 |

> 💡 进度条 + 定时器是「假进度」（按时间增长）；真实进度需要任务回调（多线程/异步）。


---

## 相关笔记

- [[Winform/03-常用控件/09-控件速查表]] — 控件速查
- [[Winform/07-多线程编程/01-Thread线程]] — 多线程
- [[Winform/09-项目实战/03-猜数游戏]] — 猜数游戏（Timer 应用）
