---
title: 焦点与 KeyPreview
tags:
  - CSharp
  - WinForms
  - 事件
---

# 焦点（Focus）与 KeyPreview

> **来源**：`D:\study\C#1\WinForm\stu0804\练习`

---

## 1. 什么是焦点（Focus）

获得焦点的控件会**接收键盘输入**。比如点一下文本框就能打字，就是因为它获得了焦点。

键盘事件需要控件**获得焦点**才能触发。如果点击了其他按钮，焦点就会丢失，原来的键盘事件不再生效。

## 2. Focus() vs Select()

| 方法 | 效果 | 推荐场景 |
|------|------|---------|
| `Focus()` | 强制控件获得输入焦点 | 大多数情况 |
| `Select()` | 选中控件，但不一定获得焦点 | 安全场景 |

```csharp
button3.Focus();    // 让 button3 获得输入焦点
button3.Select();   // 选中 button3，但不一定获得焦点
```

> 💡 两者在大多数情况下效果相似，初学者用 `Focus()` 即可。

## 3. 焦点丢失的解决方案

### 方案一：每次操作后交回焦点（简单但不完美）

```csharp
private void button1_Click(object sender, EventArgs e)
{
    button3.Location = new Point(x, y + 10);
    button3.Focus();  // 交回焦点，保证键盘还能用
}
```

### 方案二：设置 KeyPreview = true（推荐）

```csharp
public Form1()
{
    InitializeComponent();
    this.KeyPreview = true;  // 让窗体先于子控件收到键盘事件
    this.KeyUp += new KeyEventHandler(Form1_KeyUp);
}
```

> 💡 **`KeyPreview = true`** 是推荐做法：窗体先于所有子控件接收到键盘事件，**不依赖焦点**。这样即使焦点在别的控件上，键盘事件依然能触发。

## 4. 完整示例：方向键移动方块（焦点问题解决方案）

```csharp
public partial class Form1 : Form
{
    int x = 0, y = 0;

    public Form1()
    {
        InitializeComponent();
        this.KeyPreview = true;          // 方案二：窗体先接收键盘事件
        this.KeyUp += Form1_KeyUp;
    }

    private void Form1_KeyUp(object sender, KeyEventArgs e)
    {
        switch (e.KeyCode)
        {
            case Keys.Left:
                if (x > 0) button3.Location = new Point(x -= 5, y);
                break;
            case Keys.Right:
                if (x < panel1.Width - button3.Width) button3.Location = new Point(x += 5, y);
                break;
            case Keys.Up:
                if (y > 0) button3.Location = new Point(x, y -= 5);
                break;
            case Keys.Down:
                if (y < panel1.Height - button3.Height) button3.Location = new Point(x, y += 5);
                break;
        }
    }

    // 方案一：点击按钮移动后交回焦点
    private void button1_Click(object sender, EventArgs e)
    {
        button3.Location = new Point(x, y + 10);
        button3.Focus();
    }
}
```

## 5. 常见陷阱

- 只有**能接收焦点的控件**才能用 `Focus()`（如 Button、TextBox）；Panel、Label 默认不接收焦点。
- 窗体加载时焦点在第一个控件上，可以用 `this.ActiveControl = textBox1` 指定初始焦点。
- 弹出了 `MessageBox` 或 `ShowDialog` 模态窗口后，焦点会被模态窗口接管。


---

## 相关笔记

- [[Winform/02-事件机制/03-键盘事件]] — 键盘事件
- [[Winform/02-事件机制/01-事件的基本使用]] — 事件基础
