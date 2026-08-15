---
title: File 与 Directory
tags:
  - CSharp
  - WinForms
  - IO
---

# File 与 Directory

> **来源**：`D:\study\C#1\WinForm\stu0810\文件操作`

`File` 和 `Directory` 是 C# 文件系统操作的两个**静态类**，一个操作文件、一个操作目录（文件夹），不需要创建对象，直接用类名调用。

---

## 1. File 类 — 文件操作（静态方法）

```csharp
// 判断文件是否存在
if (File.Exists(path)) { }

// 删除文件
File.Delete(path);

// 复制文件
File.Copy(sourcePath, destPath);           // 不允许覆盖（目标存在会异常）
File.Copy(sourcePath, destPath, true);     // 允许覆盖

// 移动/重命名文件
File.Move(sourcePath, destPath);

// 读取/写入（快捷方式）
string content = File.ReadAllText(path);              // 读整个文本
string[] lines = File.ReadAllLines(path);             // 按行读
File.WriteAllText(path, content);                     // 写整个文本
File.AppendAllText(path, "追加内容\r\n");              // 追加
```

### 完整实战（文件管理器）

```csharp
// 删除文件
private void button4_Click(object sender, EventArgs e)
{
    string path = textBox2.Text;
    File.Delete(path);
}

// 复制文件（先判断存在）
private void button5_Click(object sender, EventArgs e)
{
    if (File.Exists(textBox2.Text))
    {
        File.Copy(textBox2.Text, textBox3.Text);
    }
}

// 移动文件
private void button6_Click(object sender, EventArgs e)
{
    if (File.Exists(textBox2.Text))
    {
        File.Move(textBox2.Text, textBox3.Text);
    }
}
```

> 💡 复制/移动/删除前**先判断文件是否存在**是良好的习惯，避免抛异常。

## 2. Directory 类 — 目录操作（静态方法）

```csharp
// 获取目录下所有文件
string[] files = Directory.GetFiles(@"D:\MyFolder");

// 获取所有子目录
string[] dirs = Directory.GetDirectories(path);

// 创建目录
Directory.CreateDirectory(path);

// 删除目录（空目录）
Directory.Delete(path);

// 删除非空目录（递归删除所有内容）
Directory.Delete(path, true);
```

### 递归获取所有子目录

```csharp
public void GetDic(string path)
{
    string[] dirs = Directory.GetDirectories(path);
    foreach (var s in dirs)
    {
        textBox1.Text += s + "\r\n";
        if (Path.GetDirectoryName(s).Length > 0)
        {
            GetDic(s);   // 递归：继续往下找
        }
    }
}
```

### 实战：遍历目录所有文件

```csharp
private void button7_Click(object sender, EventArgs e)
{
    textBox1.Clear();
    string[] files = Directory.GetFiles($@"{textBox4.Text}");
    foreach (string file in files)
    {
        textBox1.Text += file + "\r\n";
    }
}
```

## 3. DirectoryInfo 类 — 目录对象方式

`DirectoryInfo` 是面向对象的用法，先创建对象再操作，还能访问目录的详细信息：

```csharp
// 创建对象
DirectoryInfo dir = new DirectoryInfo(path);

// 删除（true = 递归删除非空目录）
dir.Delete(true);

// 属性信息
Console.WriteLine("名称：" + dir.Name);
Console.WriteLine("完整路径：" + dir.FullName);
Console.WriteLine("创建时间：" + dir.CreationTime);
Console.WriteLine("访问时间：" + dir.LastAccessTime);
Console.WriteLine("写入时间：" + dir.LastWriteTime);

// 列出子目录
DirectoryInfo[] subDirs = dir.GetDirectories();
```

### 实战：删除整个目录

```csharp
// 用 DirectoryInfo 删除（递归）
DirectoryInfo directory = new DirectoryInfo(textBox4.Text);
directory.Delete(true);
```

## 4. 静态类 vs 实例类

| 对比 | File / Directory | FileInfo / DirectoryInfo |
|------|------------------|--------------------------|
| 使用方式 | `File.方法(路径)` | `new FileInfo(路径).方法()` |
| 多次操作 | 每次都要写路径 | 对象保存路径，复用 |
| 属性访问 | 无 | 有（创建时间等） |
| 适用 | 一次性简单操作 | 需要多次操作或读属性 |

> 💡 第一阶段已经学过 `10-IO操作` 目录下的 `File与FileInfo`、`Directory与DirectoryInfo` 笔记，本笔记是 WinForm 阶段的实战补充。


---

## 相关笔记

- [[10-IO操作/File与FileInfo]] — 控制台文件操作
- [[Winform/06-文件与IO操作/03-对话框OpenFileDialog与SaveFileDialog]] — 文件对话框
