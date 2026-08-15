---
title: FileStream 字节流
tags:
  - CSharp
  - IO
  - 文件操作
---

 # FileStream — 底层字节流

> 来源：`D:\study\C#1\stu0731\stu0731\Program.cs`

`FileStream` 是最底层的文件操作类，以**字节**为单位读写文件。所有其他 IO 类（StreamReader、BinaryWriter 等）都基于它工作。

---

## 读取文件

```csharp
// 1. 创建 FileStream，指定路径、打开方式、访问模式
FileStream fs = new FileStream(@"./123.txt", FileMode.Open, FileAccess.Read);

// 2. 创建字节数组，用于存放读取的数据
byte[] bytes = new byte[21];

// 3. 调用 Read 方法
// 参数：存放字节的数组、开始位置、读取个数
// 返回值：实际读到的字节数
int bytesRead = fs.Read(bytes, 0, bytes.Length);

// 4. 将字节数组解码为字符串
// 参数：需解码的数组、起始位置、解码长度
string str = Encoding.UTF8.GetString(bytes, 0, bytesRead);

// 5. 关闭并释放资源
fs.Close();
fs.Dispose();
Console.WriteLine(str);
```

### 循环读取（大文件）

```csharp
FileStream fs = new FileStream(@"./123.txt", FileMode.Open, FileAccess.Read);
byte[] bytes = new byte[1024];  // 每次读取 1KB
string content = "";
int bytesRead;
while ((bytesRead = fs.Read(bytes, 0, bytes.Length)) != 0)
{
    content += Encoding.UTF8.GetString(bytes, 0, bytesRead);
}
fs.Close();
fs.Dispose();
```

---

## 写入文件

```csharp
// 打开或创建文件，以写入模式访问
FileStream fs = new FileStream(@"./output.txt", FileMode.OpenOrCreate, FileAccess.Write);

string str = "要写入的内容";
byte[] buffer = Encoding.UTF8.GetBytes(str);  // 字符串 → 字节数组

fs.Write(buffer, 0, buffer.Length);  // 写入字节
fs.Close();
fs.Dispose();
```

---

## FileMode 枚举

| 值 | 说明 |
|----|------|
| `Create` | 创建新文件（已存在则覆盖） |
| `CreateNew` | 创建新文件（已存在则报错） |
| `Open` | 打开已有文件（不存在则报错） |
| `OpenOrCreate` | 打开已有文件（不存在则创建） |
| `Truncate` | 打开并清空内容 |
| `Append` | 追加模式（不存在则创建） |

## FileAccess 枚举

| 值 | 说明 |
|----|------|
| `Read` | 只读 |
| `Write` | 只写 |
| `ReadWrite` | 读写 |

---

---

## 相关笔记

- [[10-IO操作/文件IO概览]] — 文件 IO 概览
- [[10-IO操作/StreamReader与StreamWriter]] — 文本流（基于 FileStream）
- [[10-IO操作/BinaryReader与BinaryWriter]] — 二进制流（基于 FileStream）
- [[10-IO操作/File与FileInfo]] — 高级文件操作
