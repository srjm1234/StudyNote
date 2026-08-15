---
title: 事件的联动响应与 sender
tags:
  - CSharp
  - WinForms
  - 事件
---

# 事件的联动响应（sender）

> **来源**：`D:\study\C#1\WinForm\stu0804\事件的联动响应`

多个控件可以**共用一个事件处理方法**，通过 `sender` 参数区分是哪个控件触发的。

---

## 1. 基本用法

```csharp
public void BtnTest_Click(object sender, EventArgs e)
{
    // sender：触发事件的对象（object 类型）
    Button btn = (Button)sender;   // 强制转换为 Button

    label1.Text = btn.Text;               // 显示按钮文字
    label1.BackColor = btn.BackColor;     // 显示按钮背景色
    label1.ForeColor = btn.ForeColor;     // 显示按钮前景色
    MessageBox.Show($"{btn.Text}被点击了");
}
```

## 2. 设计器中绑定多个控件

```csharp
// 三个按钮绑定同一个事件处理方法
this.button1.Click += new EventHandler(this.BtnTest_Click);  // 红色 Andy老师
this.button2.Click += new EventHandler(this.BtnTest_Click);  // 绿色 Carry老师
this.button3.Click += new EventHandler(this.BtnTest_Click);  // 蓝色 Coco老师
```

## 3. sender 工作原理

```
用户点击 button1（文字="Andy老师"，背景=红色）
         ↓
触发 BtnTest_Click 事件
         ↓
sender 就是 button1 对象
         ↓
((Button)sender).Text      → "Andy老师"
((Button)sender).BackColor → Color.Red
```

> 💡 **要点**：`sender` 是 `object` 类型，必须**强制转换**才能访问具体控件的属性。

## 4. 其他控件的 sender 使用

```csharp
// PictureBox 共用事件：点击小图，大图显示对应图片
private void img1_Click(object sender, EventArgs e)
{
    imgB.Image = (sender as PictureBox).Image;   // as 也是一种强转写法
    imgB.SizeMode = PictureBoxSizeMode.Zoom;
}

// TextBox 共用事件：显示输入框的文本
private void txt_TextChanged(object sender, EventArgs e)
{
    TextBox tb = (TextBox)sender;
    statusLabel.Text = tb.Text;
}
```

> 💡 `(Button)sender` 与 `sender as Button` 的区别：
> - `(Button)sender`：强转，类型不对会抛异常
> - `sender as Button`：类型不对返回 `null`，不抛异常

## 5. 实战场景

- 一排按钮（数字键、颜色键）共用事件 → 用 `sender` 区分
- 多个图片框共用放大预览事件
- 计算器中的数字按钮（见 `09-项目实战/02-计算器`）


---

## 相关笔记

- [[Winform/02-事件机制/01-事件的基本使用]] — 事件订阅
- [[Winform/09-项目实战/02-计算器]] — sender 共用事件实战
