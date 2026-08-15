---
title: File 类与 FileInfo 类
tags:
  - CSharp
  - IO
  - 文件操作
---

# File 类与 FileInfo 类

> 来源：`D:\study\C#1\stu0731\File类 FileInfo文件信息类` 及 `File类操作文件`

---

## File 静态类

`File` 是静态工具类，适合**一次性**对文件进行操作（创建、复制、移动、删除、读写）。

### 文件操作方法

```csharp
string path = @"./data.txt";

// 判断文件是否存在
bool exists = File.Exists(path);     // → true / false

// 创建文件（返回 FileStream，需 Close）
File.Create(path).Close();

// 复制文件（第三个参数：true = 允许覆盖同名文件）
File.Copy(path, @"./backup.txt", true);

// 移动 / 剪切文件
File.Move(path, @"D:\data.txt");

// 删除文件
File.Delete(@"D:\data.txt");
```

### 文件读写方法

```csharp
string path = @"./Book.txt";

// 追加文本到末尾（不会覆盖原有内容）
File.AppendAllText(path, "三国演义|罗贯中|售价99.99￥\n");

// 替换整个文件内容（覆盖写入）
File.WriteAllText(path, "新内容");

// 按行写入
File.WriteAllLines(path, new string[] { "第一行", "第二行", "第三行" });

// 读取整个文件为字符串
string content = File.ReadAllText(path);

// 读取整个文件为字符串数组（每行一个元素）
string[] lines = File.ReadAllLines(path);
foreach (string line in lines)
{
    Console.WriteLine(line);
}
```

### File 方法速查表

| 方法 | 功能 | 返回值 |
|------|------|--------|
| `File.Exists(path)` | 判断文件是否存在 | `bool` |
| `File.Create(path)` | 创建文件 | `FileStream` |
| `File.Copy(src, dest, overwrite)` | 复制文件 | `void` |
| `File.Move(src, dest)` | 移动文件 | `void` |
| `File.Delete(path)` | 删除文件 | `void` |
| `File.ReadAllText(path)` | 读取全部文本 | `string` |
| `File.ReadAllLines(path)` | 读取全部行 | `string[]` |
| `File.WriteAllText(path, text)` | 覆盖写入全部文本 | `void` |
| `File.WriteAllLines(path, lines)` | 按行写入 | `void` |
| `File.AppendAllText(path, text)` | 追加文本 | `void` |

---

## FileInfo 实例类

`FileInfo` 是实例类，适合**多次操作同一个文件**或**获取文件详细信息**。

```csharp
string path = @"./1234.txt";

// 创建实例
FileInfo fileInfo = new FileInfo(path);

// 判断文件是否存在
Console.WriteLine(fileInfo.Exists);    // → true / false

// 创建文件
fileInfo.Create().Close();

// 复制文件（第二个参数：true = 允许覆盖）
fileInfo.CopyTo(@"./backup.txt", true);

// 移动文件
fileInfo.MoveTo(@"D:\123.txt");

// 删除文件
fileInfo.Delete();
```

### FileInfo 常用属性

```csharp
FileInfo fileInfo = new FileInfo(@"./data.txt");

Console.WriteLine("文件名称："       + fileInfo.Name);           // data.txt
Console.WriteLine("文件大小："       + fileInfo.Length);          // 字节数
Console.WriteLine("完整路径："       + fileInfo.FullName);        // D:\...\data.txt
Console.WriteLine("所在目录路径："   + fileInfo.DirectoryName);   // D:\...
Console.WriteLine("文件属性："       + fileInfo.Attributes);      // Archive, ReadOnly 等
Console.WriteLine("扩展名："         + fileInfo.Extension);       // .txt
Console.WriteLine("创建时间："       + fileInfo.CreationTime);
Console.WriteLine("最后一次访问时间：" + fileInfo.LastAccessTime);
Console.WriteLine("最后一次写入时间：" + fileInfo.LastWriteTime);

// 修改写入时间
fileInfo.LastWriteTime = new DateTime(1989, 6, 4);
```

### File vs FileInfo 对比

| 特性 | File（静态类） | FileInfo（实例类） |
|------|---------------|-------------------|
| 调用方式 | `File.Method(path)` | `new FileInfo(path).Method()` |
| 适用场景 | 一次性操作 | 多次操作同一个文件 |
| 信息查询 | 需要分别调用 | 属性直接访问（Name、Length、CreationTime 等） |
| 性能 | 每次都验证路径 | 只在构造时验证一次 |

---

---

## 相关笔记

- [[10-IO操作/文件IO概览]] — 文件 IO 概览
- [[10-IO操作/Directory与DirectoryInfo]] — 目录操作
- [[10-IO操作/FileStream]] — FileStream 底层字节流
- [[08-面向对象/类与对象/密封类与静态类]] — File 静态类
