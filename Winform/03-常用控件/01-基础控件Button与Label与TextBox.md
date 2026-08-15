---
title: 基础控件 Button / Label / TextBox
tags:
  - CSharp
  - WinForms
  - 控件
---

# 基础控件：Button / Label / TextBox

> **来源**：`D:\study\C#1\WinForm\stu0804\练习`、`stu0806\全选` 等

---

## 1. Button 按钮

```csharp
// 基本属性
button.Text = "点击我";
button.Location = new Point(100, 200);
button.Size = new Size(80, 30);
button.BackColor = Color.Red;
button.ForeColor = Color.White;
button.Enabled = false;    // 禁用（灰掉，点击无效）
button.Visible = false;    // 隐藏

// 事件
button.Click += Button_Click;
```

| 属性 | 说明 |
|------|------|
| `Text` | 按钮文字 |
| `BackColor` / `ForeColor` | 背景色 / 文字颜色 |
| `Enabled` | 是否可用（false = 灰掉） |
| `Visible` | 是否可见 |
| `FlatStyle` | 按钮外观样式 |
| `DialogResult` | 点击后自动返回的对话框结果 |

## 2. Label 标签

```csharp
// 显示文本
label1.Text = "Hello";
label1.BackColor = Color.Red;
label1.ForeColor = Color.White;
label1.TextAlign = ContentAlignment.MiddleCenter;   // 文字水平垂直居中

// 自动调整大小
label1.AutoSize = true;

// 透明背景（叠加到图片上时用）
label1.BackColor = Color.Transparent;
```

| ContentAlignment 取值 | 位置 |
|----------------------|------|
| `TopLeft` / `TopCenter` / `TopRight` | 上左 / 上中 / 上右 |
| `MiddleLeft` / `MiddleCenter` / `MiddleRight` | 中左 / 居中 / 中右 |
| `BottomLeft` / `BottomCenter` / `BottomRight` | 下左 / 下中 / 下右 |

## 3. TextBox 文本框

```csharp
// 输入/显示文本
textBox1.Text = "输入内容";

// 密码模式
textBox1.PasswordChar = '*';            // 自定义掩码字符
textBox1.UseSystemPasswordChar = true;  // 系统密码样式

// 多行模式
textBox1.Multiline = true;
textBox1.ScrollBars = ScrollBars.Vertical;   // 垂直滚动条

// 只读
textBox1.ReadOnly = true;

// 获取输入
string input = textBox1.Text.Trim();   // Trim() 去掉首尾空格
textBox1.Clear();                      // 清空
```

| 属性 | 说明 |
|------|------|
| `Text` | 文本内容 |
| `PasswordChar` / `UseSystemPasswordChar` | 密码框 |
| `Multiline` | 多行显示 |
| `MaxLength` | 最大输入长度 |
| `ReadOnly` | 只读（可复制不可编辑） |
| `CharacterCasing` | 自动大小写（Upper/Lower） |

### 事件

```csharp
// 文本变化时触发
textBox1.TextChanged += textBox1_TextChanged;

// 按下回车时触发
textBox1.KeyDown += textBox1_KeyDown;   // 判断 e.KeyCode == Keys.Enter
```

## 4. 三控件搭配：表单录入

```csharp
// 登录界面示例
private void BtnLogin_Click(object sender, EventArgs e)
{
    string acc = txtAccount.Text.Trim();      // TextBox 输入
    string pwd = txtPassword.Text.Trim();

    if (acc == "admin" && pwd == "123456")
    {
        lblMsg.Text = "登录成功";              // Label 显示结果
        lblMsg.ForeColor = Color.Green;
    }
    else
    {
        lblMsg.Text = "账号或密码错误";
        lblMsg.ForeColor = Color.Red;
    }
}
```

> 💡 输入类控件（TextBox）负责**获取数据**，展示类控件（Label）负责**显示结果**，Button 负责**触发逻辑**——这是最基础的界面交互模式。


---

## 相关笔记

- [[Winform/03-常用控件/09-控件速查表]] — 控件速查
- [[Winform/02-事件机制/01-事件的基本使用]] — 事件绑定
