---
title: StreamReader 与 StreamWriter
tags:
  - CSharp
  - IO
  - 文件操作
  - 文本流
---

# StreamReader 与 StreamWriter — 文本流读写

> 来源：`D:\study\C#1\stu0731\StreamReader StreamWriter\Program.cs`

`StreamReader` / `StreamWriter` 以**文本**为单位读写文件，支持逐行操作，是处理文本文件最常用的方式。

---

## 写入数据（StreamWriter）

```csharp
// 方式一：手动管理资源
FileStream fs = new FileStream(@"./账号.txt", FileMode.OpenOrCreate, FileAccess.Write);
StreamWriter writer = new StreamWriter(fs);
writer.WriteLine("第一行内容");
writer.Close();   // 先关 StreamWriter
fs.Close();       // 再关 FileStream

// 方式二：using 自动释放资源（推荐）
using (FileStream fs = new FileStream(@"./账号.txt", FileMode.Append, FileAccess.Write))
{
    using (StreamWriter sw = new StreamWriter(fs))
    {
        sw.WriteLine("追加的一行");
        sw.WriteLine("再追加一行");
    }
}  // 自动 Close + Dispose
```

### StreamWriter 常用方法

| 方法 | 说明 |
|------|------|
| `Write(value)` | 写入内容（不换行） |
| `WriteLine(value)` | 写入内容 + 换行 |
| `Close()` | 关闭写入器 |
| `Flush()` | 将缓冲区内容写入文件 |

---

## 读取数据（StreamReader）

```csharp
FileStream fs = new FileStream(@"./账号.txt", FileMode.Open, FileAccess.Read);
StreamReader sr = new StreamReader(fs);

// 方式一：逐行读取
while (sr.ReadLine() != null)
{
    Console.WriteLine(sr.ReadLine());
}

// 方式二：从头读到尾
string content = sr.ReadToEnd();
Console.WriteLine(content);

sr.Close();
fs.Close();
```

### using 写法（推荐）

```csharp
using (FileStream fs = new FileStream(@"./data.txt", FileMode.Open, FileAccess.Read))
using (StreamReader sr = new StreamReader(fs))
{
    string line;
    while ((line = sr.ReadLine()) != null)
    {
        Console.WriteLine(line);
    }
}
```

### StreamReader 常用方法

| 方法 | 说明 | 返回值 |
|------|------|--------|
| `Read()` | 读取下一个字符 | `int`（-1 表示结束） |
| `ReadLine()` | 读取一行 | `string`（null 表示结束） |
| `ReadToEnd()` | 读取全部内容 | `string` |
| `Close()` | 关闭读取器 | `void` |

---

## using 语句

`using` 语句确保在代码块结束时自动调用 `Dispose()`，即使发生异常也能正确释放资源。

```csharp
// 等价写法：
using (var resource = new SomeResource())
{
    // 使用 resource
}
// 编译器自动插入：finally { resource.Dispose(); }
```

> **最佳实践**：所有实现了 `IDisposable` 接口的类（FileStream、StreamReader、StreamWriter 等）都应使用 `using` 语句管理生命周期。

---

---

## 相关笔记

- [[10-IO操作/FileStream]] — 底层字节流
- [[10-IO操作/文件IO概览]] — 文件 IO 概览
- [[10-IO操作/BinaryReader与BinaryWriter]] — 二进制读写
- [[02-流程控制/流程控制]] — 异常处理（using 资源释放）
