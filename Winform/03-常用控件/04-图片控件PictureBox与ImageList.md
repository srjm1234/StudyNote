---
title: 图片控件 PictureBox 与 ImageList
tags:
  - CSharp
  - WinForms
  - 控件
---

# 图片控件：PictureBox / ImageList

> **来源**：`D:\study\C#1\WinForm\stu0806\imagelist`、`stu0806\0806\图片`、`stu0811\text文件`

---

## 1. PictureBox 图片框

```csharp
// 加载图片的三种方式
pictureBox1.ImageLocation = @"D:\Images\1.jpg";        // 方式一：路径（自动加载）
pictureBox1.Image = Image.FromFile(@"D:\Images\1.jpg"); // 方式二：Image 对象
pictureBox1.Image = Properties.Resources.guga;          // 方式三：项目资源

// 缩放模式
pictureBox1.SizeMode = PictureBoxSizeMode.Zoom;         // 等比缩放（推荐）
pictureBox1.SizeMode = PictureBoxSizeMode.StretchImage; // 拉伸填充（会变形）
pictureBox1.SizeMode = PictureBoxSizeMode.CenterImage;  // 居中裁剪

// 显隐控制
pictureBox1.Visible = true;
```

### SizeMode 对比

| SizeMode | 效果 |
|----------|------|
| `Normal` | 原始大小，超出裁剪 |
| `StretchImage` | 拉伸填充（可能变形） |
| `AutoSize` | 控件自适应图片大小 |
| `CenterImage` | 居中，超出裁剪 |
| `Zoom` | 等比缩放（**推荐**，不变形） |

## 2. ImageList 图片列表

`ImageList` 把多张图片**统一管理**，按索引取用，适合轮播、图标列表等场景。

```csharp
// 设计器里：给 imageList1 添加多张图片（Images 集合）

// 通过索引切换图片
int i = 0;
private void button1_Click(object sender, EventArgs e)
{
    if (i == 7) { i = 0; }                  // 到末尾回到开头（轮播）
    pictureBox1.Image = imageList1.Images[i++];
}
```

```csharp
// 代码方式向 ImageList 添加图片
ImageList imageList1 = new ImageList();
imageList1.Images.Add(Image.FromFile(@"1.png"));
imageList1.Images.Add(Image.FromFile(@"2.png"));

// 访问
Image img = imageList1.Images[0];           // 按索引
int count = imageList1.Images.Count;        // 图片数量
```

> 💡 `ImageList` 常与 `ListView` 小图标、`ToolStrip` 按钮图标配合使用。

## 3. 实战一：小图点击 → 大图预览

```csharp
// 多个小图 PictureBox 共用事件
private void img1_Click(object sender, EventArgs e)
{
    imgB.Image = (sender as PictureBox).Image;   // 被点击的小图
    imgB.SizeMode = PictureBoxSizeMode.Zoom;
}
```

## 4. 实战二：保存 PictureBox 中的图片

```csharp
// 保存当前显示的图片为 PNG 文件
pictureBox1.Image.Save($@"./imge/{Name1.Text}a.png", System.Drawing.Imaging.ImageFormat.Png);
```

> ⚠️ `Image.Save` 需要 `using System.Drawing.Imaging;`，`ImageFormat` 支持 Png / Jpeg / Bmp / Gif 等。

## 5. 实战三：通过 OpenFileDialog 选择图片

```csharp
OpenFileDialog ofd = new OpenFileDialog();
if (ofd.ShowDialog() == DialogResult.OK)
{
    pictureBox1.ImageLocation = ofd.FileName;   // 显示选择的图片
}
```

> 💡 完整文件对话框用法见 `06-文件与IO操作/03-对话框OpenFileDialog与SaveFileDialog`。


---

## 相关笔记

- [[Winform/03-常用控件/09-控件速查表]] — 控件速查
- [[Winform/03-常用控件/08-定时器Timer与进度条ProgressBar]] — Timer
