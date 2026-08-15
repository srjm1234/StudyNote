---
title: MessageBox 消息框
tags:
  - CSharp
  - WinForms
  - 事件
---

# MessageBox 消息提示框

> **来源**：`D:\study\C#1\WinForm\stu0805\菜单类控件`、`0804-作业\0804-作业`

---

## 1. MessageBox.Show 完整用法

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

## 2. 获取用户选择

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

## 3. 阻止窗体关闭（FormClosing 中拦截）

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

> 💡 **要点**：`FormClosing` 可以阻止关闭（`e.Cancel = true`），`FormClosed` 在关闭后触发，无法阻止。

## 4. 常用枚举值

### MessageBoxButtons（按钮样式）

| 枚举值 | 显示的按钮 |
|--------|-----------|
| `OK` | 确定 |
| `OKCancel` | 确定 + 取消 |
| `YesNo` | 是 + 否 |
| `YesNoCancel` | 是 + 否 + 取消 |
| `AbortRetryIgnore` | 中止 + 重试 + 忽略 |

### MessageBoxIcon（图标）

| 枚举值 | 图标 |
|--------|------|
| `None` | 无 |
| `Information` | 信息 |
| `Warning` | 警告 |
| `Error` | 错误 |
| `Question` | 问号 |
| `Asterisk` | 星标 |

### MessageBoxDefaultButton（默认按钮）

| 枚举值 | 默认选中的按钮 |
|--------|--------------|
| `Button1` | 第一个按钮 |
| `Button2` | 第二个按钮 |
| `Button3` | 第三个按钮 |

## 5. 返回值 DialogResult

| 枚举值 | 对应按钮 |
|--------|---------|
| `OK` | 确定 |
| `Cancel` | 取消 |
| `Yes` | 是 |
| `No` | 否 |
| `Abort` / `Retry` / `Ignore` | 中止 / 重试 / 忽略 |

## 6. 实战：计算器中的关闭确认与非法数据提示

```csharp
// 窗体关闭前确认（计算器项目）
private void Fr_FormClosing(object sender, FormClosingEventArgs e)
{
    DialogResult dialogResult =
    MessageBox.Show("是否关闭", "提示", MessageBoxButtons.OKCancel, MessageBoxIcon.Error, MessageBoxDefaultButton.Button1);
    if (dialogResult == DialogResult.Cancel)
    {
        e.Cancel = true;
    }
}

// 除数为 0 的提示
if (double.Parse(textBox1.Text.Split('÷')[1]) != 0)
{
    textBox1.Text = (double.Parse(textBox1.Text.Split('÷')[0]) / double.Parse(textBox1.Text.Split('÷')[1])).ToString();
}
else
{
    MessageBox.Show("非法数据！");   // 提示后清空
    textBox1.Text = null;
}
```

> 💡 MessageBox 会**阻塞当前代码**，直到用户点击按钮后才继续执行，因此可以直接用返回值判断。


---

## 相关笔记

- [[Winform/02-事件机制/01-事件的基本使用]] — 事件基础
- [[Winform/05-多窗体与数据传递/01-打开新窗口Show与ShowDialog]] — 对话框
