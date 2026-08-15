---
title: 容器控件 Panel / GroupBox / TabControl
tags:
  - CSharp
  - WinForms
  - 控件
---

# 容器控件：Panel / GroupBox / TabControl

> **来源**：`D:\study\C#1\WinForm\stu0805\分组类控件`、`stu0812\Tab和菜单栏`

容器控件用来**装其他控件**，起分组、布局、管理作用。加到容器里的控件会跟随容器一起移动、显示/隐藏。

---

## 1. Panel 面板

```csharp
// Panel：容器控件，用于分组和布局
Panel panel1 = new Panel();
panel1.Width = 300;
panel1.Height = 300;
panel1.Location = new Point(20, 20);
panel1.BorderStyle = BorderStyle.FixedSingle;  // 显示边框
this.Controls.Add(panel1);

// 将按钮添加到 Panel 中（而非窗体）
panel1.Controls.Add(button1);
```

| 属性 | 说明 |
|------|------|
| `BorderStyle` | 边框样式（None / FixedSingle / Fixed3D） |
| `BackColor` | 背景色（可做区域视觉区分） |
| `AutoScroll` | 内容超出时显示滚动条 |
| `Dock` / `Anchor` | 布局（见 04-布局管理） |

> 💡 **注意**：添加到 Panel 的控件，`Location` 以 Panel 左上角为原点，不是窗体。

## 2. GroupBox 分组框

`GroupBox` 和 Panel 类似，但自带**标题文字**和默认边框，适合做表单分组：

```csharp
// 设计器里：拖一个 GroupBox，标题写"个人信息"
groupBox1.Text = "个人信息";

// 用法和 Panel 相同
groupBox1.Controls.Add(textBox1);
groupBox1.Controls.Add(label1);
```

| 对比 | Panel | GroupBox |
|------|-------|----------|
| 标题 | 无 | 有（`Text` 属性） |
| 边框 | 可设置 | 默认有 |
| 适用 | 布局分区 | 表单分组 |

## 3. TabControl 选项卡

把多个页面（TabPage）叠放在一起，点击标签切换：

```csharp
// 切换到指定页面
tabControl1.SelectedTab = tabPage1;

// 显示/隐藏页面
tabPage1.Show();
tabPage1.Hide();

// 获取当前选中的页面
TabPage current = tabControl1.SelectedTab;
```

| 属性/方法 | 说明 |
|----------|------|
| `TabPages` | 页面集合 |
| `SelectedTab` | 当前选中的页面 |
| `SelectedIndex` | 当前选中的索引 |
| `SelectTab()` / `DeselectTab()` | 程序切换页面 |

> 💡 每个 TabPage 都是一个**容器**，可以往里面放任何控件。适合做设置页、多模块界面。

## 4. 实战：登录界面分组

```csharp
// 设计思路：
// GroupBox "账号信息" → TextBox 账号、TextBox 密码
// GroupBox "操作"     → Button 登录、Button 注册
// Panel "提示区"      → Label 提示信息
```

> 💡 容器控件是 WinForms 布局的基础，配合 `Dock`/`Anchor` 可以实现自适应界面（见 `04-布局管理`）。


---

## 相关笔记

- [[Winform/03-常用控件/09-控件速查表]] — 控件速查
- [[Winform/04-布局管理/02-Dock与Anchor]] — 布局
