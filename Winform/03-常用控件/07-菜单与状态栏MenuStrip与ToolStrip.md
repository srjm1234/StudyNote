---
title: 菜单与状态栏 MenuStrip / ToolStrip
tags:
  - CSharp
  - WinForms
  - 控件
---

# 菜单与状态栏：MenuStrip / ToolStrip / StatusStrip

> **来源**：`D:\study\C#1\WinForm\stu0805\菜单类控件`、`stu0812\Tab和菜单栏`

---

## 1. MenuStrip 菜单栏（顶部菜单）

```csharp
// 设计器里拖入 MenuStrip，输入菜单项（如"文件"→"保存"、"退出"）

// 菜单项点击事件
private void 保存ToolStripMenuItem_Click(object sender, EventArgs e)
{
    this.Close();   // 示例：保存后关闭
}

private void 退出ToolStripMenuItem_Click(object sender, EventArgs e)
{
    Application.Exit();   // 退出整个程序
}
```

| 成员 | 说明 |
|------|------|
| `MenuStrip` | 菜单容器 |
| `ToolStripMenuItem` | 单个菜单项（可嵌套子菜单） |
| `菜单项.Text` | 菜单文字（可带 & 快捷键，如 `&F`） |
| `菜单项.ShortcutKeys` | 快捷键（如 Ctrl+S） |

## 2. ToolStrip 工具栏（图标按钮区）

```csharp
// 工具栏按钮点击
private void toolStripButton1_Click(object sender, EventArgs e)
{
    // 处理点击
}

// 工具栏下拉框
private void toolStripComboBox1_Click(object sender, EventArgs e)
{
    // 处理选择
}

// 工具栏整体点击（判断点了哪个项）
private void toolStrip1_ItemClicked(object sender, ToolStripItemClickedEventArgs e)
{
    MessageBox.Show(e.ClickedItem.Text);   // 被点击的项
}
```

| 成员 | 说明 |
|------|------|
| `ToolStrip` | 工具栏容器 |
| `ToolStripButton` | 按钮 |
| `ToolStripComboBox` | 下拉框 |
| `ToolStripTextBox` | 文本框 |
| `ToolStripSeparator` | 分隔线 |

## 3. StatusStrip 状态栏（底部）

```csharp
// 状态栏文字（显示当前时间）
private void Form1_Load(object sender, EventArgs e)
{
    toolStripStatusLabel3.Text = "日期：" + DateTime.Now.ToString();
}

// 配合 Timer 每秒刷新时间
private void timer1_Tick(object sender, EventArgs e)
{
    toolStripStatusLabel1.Text = DateTime.Now.ToString();
}
```

| 成员 | 说明 |
|------|------|
| `StatusStrip` | 状态栏容器 |
| `ToolStripStatusLabel` | 状态文字 |
| `ToolStripProgressBar` | 状态栏中的进度条 |
| `ToolStripSplitButton` | 分割按钮 |

## 4. 布局结构示意

```
┌──────────────────────────────────────────────┐
│ MenuStrip   文件(F)  编辑(E)  帮助(H)          │  ← 菜单栏
├──────────────────────────────────────────────┤
│ ToolStrip   [保存] [打开] [下拉框] [文本框]     │  ← 工具栏
├──────────────────────────────────────────────┤
│                                              │
│              窗体主体（控件区）                  │
│                                              │
├──────────────────────────────────────────────┤
│ StatusStrip  就绪            2026/8/13 19:00  │  ← 状态栏
└──────────────────────────────────────────────┘
```

> 💡 三个控件都支持 `Dock`，默认 `MenuStrip` 在顶部、`StatusStrip` 在底部、`ToolStrip` 可拖动。它们是构成标准桌面程序界面的「三件套」。


---

## 相关笔记

- [[Winform/03-常用控件/09-控件速查表]] — 控件速查
