---
title: TableLayoutPanel 与 FlowLayoutPanel
tags:
  - CSharp
  - WinForms
  - 布局
---

# TableLayoutPanel 与 FlowLayoutPanel

> **来源**：`D:\study\C#1\WinForm\stu0811\表格布局`

`TableLayoutPanel`（表格布局）和 `FlowLayoutPanel`（流式布局）是 WinForms 两大**自动布局容器**，控件按规则自动排列，窗体缩放时自动适应。

---

## 1. TableLayoutPanel 表格布局

按**行列网格**排布控件，每个格子放一个控件，适合做规整的界面。

### 1.1 核心属性（代码设置）

```csharp
TableLayoutPanel table = new TableLayoutPanel();
table.ColumnCount = 3;   // 3 列
table.RowCount = 3;      // 3 行
table.Dock = DockStyle.Fill;   // 填满父容器

// 列宽：三列各占 33.33%（百分比）
table.ColumnStyles.Add(new ColumnStyle(SizeType.Percent, 33.33333F));
table.ColumnStyles.Add(new ColumnStyle(SizeType.Percent, 33.33333F));
table.ColumnStyles.Add(new ColumnStyle(SizeType.Percent, 33.33333F));

// 行高：三行各占 33.33%
table.RowStyles.Add(new RowStyle(SizeType.Percent, 33.33333F));
table.RowStyles.Add(new RowStyle(SizeType.Percent, 33.33333F));
table.RowStyles.Add(new RowStyle(SizeType.Percent, 33.33333F));
```

### 1.2 向指定格子添加控件

```csharp
// Controls.Add(控件, 列, 行) — 注意参数顺序是 (控件, column, row)
table.Controls.Add(button1, 0, 0);   // 第 0 列 第 0 行
table.Controls.Add(button2, 1, 0);   // 第 1 列 第 0 行
table.Controls.Add(panel1, 2, 0);    // 第 2 列 第 0 行
```

### 1.3 SizeType 三种尺寸模式

| SizeType | 说明 |
|----------|------|
| `Percent` | 按父容器尺寸的百分比 |
| `Absolute` | 固定像素值 |
| `AutoSize` | 按内容自动调整 |

```csharp
// 固定宽度列（如左侧导航栏 200px）
table.ColumnStyles.Add(new ColumnStyle(SizeType.Absolute, 200F));

// 剩余空间自动
table.ColumnStyles.Add(new ColumnStyle(SizeType.Percent, 100F));
```

## 2. FlowLayoutPanel 流式布局

控件**从左到右、从上到下**自动排列，放不下自动换行，适合动态添加控件的场景。

```csharp
FlowLayoutPanel flow = new FlowLayoutPanel();
flow.Dock = DockStyle.Fill;
flow.FlowDirection = FlowDirection.LeftToRight;  // 排列方向

// 自动添加控件，无需手动定位
flow.Controls.Add(button6);
flow.Controls.Add(button7);
flow.Controls.Add(button8);
flow.Controls.Add(button9);
flow.Controls.Add(button10);
```

| 属性 | 说明 |
|------|------|
| `FlowDirection` | 排列方向（LeftToRight / TopDown 等） |
| `WrapContents` | 是否自动换行 |
| `AutoScroll` | 超出后滚动 |

## 3. 实战：仪表盘式布局（来源项目结构）

```
┌─────────────────────────────────────┐
│  tableLayoutPanel1（3行 × 3列）       │
│  ┌────────┬────────┬────────┐        │
│  │ button1│ button2│ panel1 │        │  ← panel1 内再放 button4
│  ├────────┼────────┼────────┤        │
│  │ flow   │ panel2 │ table2 │        │  ← 每个格子可以是另一个容器
│  │ 按钮流  │ 居中按钮 │ 2列表格 │        │
│  └────────┴────────┴────────┘        │
└─────────────────────────────────────┘
```

```csharp
// 组合使用：表格容器里嵌套面板、流式布局、子表格
// 外层 tableLayoutPanel1: 3列3行（各33.33%）
// 格子 (0,1) → flowLayoutPanel1（流式放 5 个按钮 + 3 个复选框）
// 格子 (1,1) → panel2（BorderStyle.FixedSingle，内含居中按钮）
// 格子 (2,1) → tableLayoutPanel2（2列，各50%，内含按钮）
```

> 💡 **嵌套是布局的精髓**：外层用 TableLayoutPanel 划分区域，每个格子内放 Panel / FlowLayoutPanel / 子 TableLayoutPanel，形成复杂的自适应界面。

## 4. 三种布局容器选型

| 容器 | 排列方式 | 适用场景 |
|------|---------|---------|
| `Panel` | 手动定位 | 自由布局 |
| `TableLayoutPanel` | 网格 | 规整表单、仪表盘 |
| `FlowLayoutPanel` | 流式换行 | 动态添加的按钮/标签 |

> 💡 学习建议：先用 Panel 手动布局理解坐标，再逐步用 Dock/Anchor、表格布局做自适应界面。


---

## 相关笔记

- [[Winform/04-布局管理/02-Dock与Anchor]] — Dock 与 Anchor
- [[Winform/03-常用控件/02-容器控件Panel与GroupBox与TabControl]] — 容器控件
