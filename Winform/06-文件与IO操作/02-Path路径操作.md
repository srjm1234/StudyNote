---
title: Path 路径操作
tags:
  - CSharp
  - WinForms
  - IO
---

# Path 路径操作

> **来源**：`D:\study\C#1\WinForm\stu0810\文件操作`、`stu0812\0812作业`

`Path` 类专门处理**路径字符串**：拼接、拆分、取文件名、取扩展名等，不用手动处理反斜杠。

---

## 1. 常用方法

```csharp
string filePath = @"D:\Images\photo.jpg";

// 获取目录名
Path.GetDirectoryName(filePath);    // → D:\Images

// 获取扩展名
Path.GetExtension(filePath);        // → .jpg

// 获取文件名（含扩展名）
Path.GetFileName(filePath);         // → photo.jpg

// 获取文件名（不含扩展名）
Path.GetFileNameWithoutExtension(filePath);  // → photo

// 获取完整路径（相对转绝对）
Path.GetFullPath(@"./1.txt");       // → D:\...\1.txt

// 更改扩展名
Path.ChangeExtension(filePath, "png");  // → D:\Images\photo.png

// 随机文件名
Path.GetRandomFileName();           // → 随机字符串
```

## 2. Path.Combine — 路径拼接（推荐）

```csharp
// 自动处理分隔符，避免手动拼 "\" 或 "/"
string newPath = Path.Combine(@"D:\", "Images", "photo.jpg");
// → D:\Images\photo.jpg

// 实战：拼接可执行目录下的文件
string iniPath = Path.Combine(Directory.GetCurrentDirectory(), "config.ini");
```

> 💡 **推荐**：用 `Path.Combine()` 代替字符串拼接，自动处理不同系统的路径分隔符，不会出现 `\` 和 `/` 混用的问题。

## 3. 实战：保存到当前目录

```csharp
// 获取程序运行目录
string curDir = Directory.GetCurrentDirectory();
// 或
string curDir = AppDomain.CurrentDomain.BaseDirectory;

// 拼接文件路径
string file = Path.Combine(curDir, "filetext", "textTest.txt");

// 自动创建目录
string folder = Path.Combine(curDir, "filetext");
if (!Directory.Exists(folder))
{
    Directory.CreateDirectory(folder);
}
```

## 4. 路径相关技巧

### 相对路径写法

```csharp
@"./my.txt"        // 当前目录下的 my.txt（. 表示当前目录）
@"../data.txt"     // 上一级目录（.. 表示上级目录）
@"../../Data/a.txt" // 上两级目录（如图书管理系统的数据目录）
```

> ⚠️ 学习项目中常写 `@"../../Data/Students.txt"`，这是相对 `bin\Debug` 运行目录的相对路径，移植项目时容易失效，正式开发推荐用 `Path.Combine(Application.StartupPath, ...)`。

### 判断扩展名

```csharp
if (Path.GetExtension(file) == ".jpg")
{
    // 是 jpg 图片
}
```


---

## 相关笔记

- [[Winform/06-文件与IO操作/01-File与Directory]] — 文件操作
