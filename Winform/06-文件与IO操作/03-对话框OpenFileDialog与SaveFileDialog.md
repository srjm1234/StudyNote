---
title: 对话框 OpenFileDialog 与 SaveFileDialog
tags:
  - CSharp
  - WinForms
  - IO
---

# 对话框：OpenFileDialog / SaveFileDialog

> **来源**：`D:\study\C#1\WinForm\stu0811\text文件`、`stu0806` 练习

调用系统自带的「打开文件」「保存文件」窗口，让用户选择文件，省去手动输入路径。

---

## 1. OpenFileDialog 打开文件对话框

```csharp
// 创建对话框对象
OpenFileDialog ofd = new OpenFileDialog();
ofd.InitialDirectory = @"D:\";                    // 初始目录
ofd.Filter = "文本文件|*.txt|图片|*.jpg;*.png|所有文件|*.*";  // 文件类型筛选
ofd.Title = "选择文件";                            // 标题

// 显示对话框，用户点"确定"后处理
if (ofd.ShowDialog() == DialogResult.OK)
{
    string filePath = ofd.FileName;    // 用户选择的完整路径
    // 处理文件...
}
```

### 实战：选择图片并显示

```csharp
private void button5_Click(object sender, EventArgs e)
{
    OpenFileDialog ofd = new OpenFileDialog();
    ofd.Filter = "图片文件|*.jpg;*.png;*.bmp|所有文件|*.*";

    if (ofd.ShowDialog() == DialogResult.OK)
    {
        pictureBox1.ImageLocation = ofd.FileName;   // 显示图片
        path1.Text = ofd.FileName;                  // 显示路径
    }
}
```

## 2. SaveFileDialog 保存文件对话框

```csharp
// 创建对话框对象
SaveFileDialog sfd = new SaveFileDialog();
sfd.InitialDirectory = @"D:\";
sfd.Filter = "文本文件|*.txt";
sfd.Title = "保存文件";
sfd.FileName = "未命名.txt";        // 默认文件名

// 显示对话框
if (sfd.ShowDialog() == DialogResult.OK)
{
    File.WriteAllText(sfd.FileName, textBox1.Text);   // 保存
}
```

## 3. Filter 格式详解

```
"描述1|扩展名1|描述2|扩展名2|描述3|扩展名3"
```

```csharp
// 示例：三个选项
"文本文件|*.txt|图片|*.jpg;*.png|所有文件|*.*"
//       ↑描述  ↑扩展名  ↑描述    ↑两个扩展名用;分隔
```

| 部分 | 说明 |
|------|------|
| 描述 | 显示在下拉框里的文字 |
| 扩展名 | `*.txt`，多个用 `;` 分隔 |
| 选项间 | 用 `|`（竖线）分隔 |
| `*.*` | 匹配所有文件 |

## 4. FolderBrowserDialog 选择文件夹

```csharp
FolderBrowserDialog fbd = new FolderBrowserDialog();
fbd.Description = "请选择保存目录";

if (fbd.ShowDialog() == DialogResult.OK)
{
    string folder = fbd.SelectedPath;   // 选中的文件夹路径
    // 保存到该目录...
}
```

## 5. 对话框通用套路

```csharp
// 三个对话框使用模式完全一致：
// 1. new 对象
// 2. 设置属性（InitialDirectory / Filter / Title）
// 3. ShowDialog() 判断返回结果
// 4. 从 FileName / SelectedPath 拿路径

if (对话框.ShowDialog() == DialogResult.OK)
{
    // 拿到路径，开始处理
}
```

> 💡 对话框本身**不做读写操作**，只是帮用户选路径。拿到 `FileName` 后还是用 File/Stream 自己处理。


---

## 相关笔记

- [[Winform/06-文件与IO操作/01-File与Directory]] — 文件操作
- [[Winform/05-多窗体与数据传递/01-打开新窗口Show与ShowDialog]] — 对话框
