---
title: 窗体属性与 Graphics 绘图
tags:
  - CSharp
  - WinForms
  - 基础
---

# 窗体属性与 Graphics 绘图

> **来源**：`D:\study\C#1\WinForm\stu0804\01\FrmMain.cs`

---

## 1. 窗体常用属性

| 属性 | 说明 | 示例 |
|------|------|------|
| `Text` | 窗体标题 | `this.Text = "我的程序";` |
| `Size` | 窗体大小 | `this.Size = new Size(800, 600);` |
| `ClientSize` | 客户区大小（不含标题栏和边框） | `this.ClientSize = new Size(790, 502);` |
| `Location` | 窗体位置 | `this.Location = new Point(100, 100);` |
| `Controls` | 控件集合 | `this.Controls.Add(btn1);` |
| `AutoScroll` | 自动滚动条 | `this.AutoScroll = true;` |
| `StartPosition` | 窗体首次显示位置 | `this.StartPosition = FormStartPosition.CenterScreen;` |
| `WindowState` | 窗口状态（最大化/最小化） | `this.WindowState = FormWindowState.Maximized;` |

> 💡 **ClientSize vs Size**：`ClientSize` 是窗体内部可用区域（不含标题栏和边框），`Size` 是整个窗体的大小。居中计算用 `ClientSize` 更准确。

## 2. Graphics 对象 — 在窗体上绘图

`Graphics` 是画布对象，可以测量文字、画线、画图形。

### 2.1 获取 Graphics 对象

```csharp
// 方式一：从窗体直接创建
Graphics g = this.CreateGraphics();

// 方式二：在 Paint 事件中获取（推荐，重绘时会自动调用）
private void Form1_Paint(object sender, PaintEventArgs e)
{
    Graphics g = e.Graphics;
}
```

### 2.2 实战：动态计算标题居中

```csharp
private void SetTitleCenter()
{
    string titleMsg = "Winfrom Title";
    Graphics g = this.CreateGraphics();

    // 计算标题居中起始位置
    Double startingPoint = (this.Width / 2) - (g.MeasureString(titleMsg, this.Font).Width / 2);

    // 计算空格宽度
    Double widthOfASpace = g.MeasureString(" ", this.Font).Width;

    // 用空格填充使标题居中
    String tmp = " ";
    Double tmpWidth = 0;
    while ((tmpWidth + widthOfASpace) < startingPoint)
    {
        tmp += " ";
        tmpWidth += widthOfASpace;
    }
    this.Text = tmp + titleMsg;
}
```

> 💡 **应用场景**：动态计算字符串在窗体上的居中位置。`g.MeasureString()` 可以精确测量字符串在指定字体下的像素宽度，返回 `SizeF`（宽、高）。

### 2.3 常用绘图方法

```csharp
Graphics g = this.CreateGraphics();

// 画线
Pen pen = new Pen(Color.Red, 2);          // 颜色 + 粗细
g.DrawLine(pen, 10, 10, 100, 100);        // 起点(10,10) → 终点(100,100)

// 画矩形（边框）
g.DrawRectangle(pen, 10, 10, 80, 50);     // x, y, 宽, 高

// 画实心矩形
SolidBrush brush = new SolidBrush(Color.Blue);
g.FillRectangle(brush, 10, 10, 80, 50);

// 画椭圆
g.DrawEllipse(pen, 10, 10, 80, 50);

// 写文字
g.DrawString("文本", this.Font, brush, 10, 10);
```

## 3. 常见注意事项

- `CreateGraphics()` 创建的 Graphics 对象用完要 `g.Dispose()` 释放，或用 `using` 包裹。
- 窗体最小化再恢复时，用 `CreateGraphics` 画的内容会**消失**，需要重新绘制。需要持久显示的内容建议在 `Paint` 事件中绘制。
- 测量文字用 `MeasureString`，返回的 `SizeF.Width` 是文字的像素宽度。


---

## 相关笔记

- [[Winform/00-WinForms学习笔记总览]] — 总览与目录
- [[Winform/01-WinForm基础/03-坐标系统与控件定位]] — 坐标系统
