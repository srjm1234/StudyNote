---
title: Dock 与 Anchor
tags:
  - CSharp
  - WinForms
  - 布局
---

# Dock 与 Anchor

> **来源**：`D:\study\C#1\WinForm\stu0811\Dock`、`stu0811\表格布局`

让控件在窗体大小变化时**自动调整位置和大小**，实现自适应界面。

---

## 1. Dock 停靠

控件紧贴父容器的某个边，或填满剩余空间：

```csharp
button1.Dock = DockStyle.Top;     // 顶部
button1.Dock = DockStyle.Bottom;  // 底部
button1.Dock = DockStyle.Left;    // 左侧
button1.Dock = DockStyle.Right;   // 右侧
button1.Dock = DockStyle.Fill;    // 填充全部剩余空间
button1.Dock = DockStyle.None;    // 取消停靠
```

> 💡 **停靠顺序**：多个控件 Dock 时按添加顺序依次占据边缘，最后 `Fill` 的控件占剩余区域。典型的界面结构：MenuStrip（Top）→ 主内容（Fill）→ StatusStrip（Bottom）。

### 常用组合示例

```csharp
// 左右面板 + 中间填充
panelLeft.Dock = DockStyle.Left;    // 左侧固定宽度面板
panelRight.Dock = DockStyle.Right;  // 右侧固定宽度面板
panelCenter.Dock = DockStyle.Fill;  // 中间自动填满剩余
```

## 2. Anchor 锚定

控件相对父容器的**四边距离固定**，窗体缩放时按比例跟着走：

```csharp
// 锚定到四边：控件随窗体一起缩放
button1.Anchor = AnchorStyles.Top | AnchorStyles.Bottom | AnchorStyles.Left | AnchorStyles.Right;

// 只锚定左上角：控件保持位置，窗体放大时不动
button1.Anchor = AnchorStyles.Top | AnchorStyles.Left;

// 锚定底部：窗体变大时按钮贴住底部
button1.Anchor = AnchorStyles.Bottom | AnchorStyles.Right;
```

| Anchor 值 | 效果 |
|-----------|------|
| `Top \| Left` | 位置固定（默认） |
| `Top \| Right` | 随窗体宽度变化保持右边距 |
| `Top \| Bottom` | 垂直方向拉伸 |
| `Top \| Bottom \| Left \| Right` | 四边跟随，完全拉伸 |

## 3. Dock vs Anchor

| 对比 | Dock | Anchor |
|------|------|--------|
| 作用 | 停靠到**边缘/填满** | 保持与**边距** |
| 调整 | 位置 + 大小 | 位置 + 大小（按比例） |
| 典型场景 | 面板式布局 | 控件自适应窗体 |

> 💡 两者可以配合：容器用 Dock 划分区域，区域内控件用 Anchor 自适应。

## 4. 实战：登录框居中且随窗体缩放

```csharp
// 设计器设置
this.panel2.BorderStyle = BorderStyle.FixedSingle;
this.button5.Anchor = AnchorStyles.Top | AnchorStyles.Bottom
                    | AnchorStyles.Left | AnchorStyles.Right;

// 代码：面板大小变化时，按钮保持居中
private void button5_SizeChanged(object sender, EventArgs e)
{
    int W = this.panel2.Size.Width - this.button5.Size.Width;
    int H = this.panel2.Size.Height - this.button5.Size.Height;
    this.button5.Location = new Point(W / 2, H / 2);
}
```

> 💡 项目源码 `stu0811\Dock`、`stu0811\表格布局` 中有完整的 Dock + Anchor 布局练习。


---

## 相关笔记

- [[Winform/04-布局管理/01-手动定位与窗体居中]] — 手动定位
- [[Winform/04-布局管理/03-TableLayoutPanel与FlowLayoutPanel]] — 布局容器
