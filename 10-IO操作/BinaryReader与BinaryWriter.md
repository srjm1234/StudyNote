---
title: BinaryReader 与 BinaryWriter
tags:
  - CSharp
  - IO
  - 文件操作
  - 二进制
---

# BinaryReader 与 BinaryWriter — 二进制读写

> 来源：`D:\study\C#1\stu0731\BinaryWrite BinaryReader\Program.cs`

`BinaryWriter` 可以写入**任意类型**的数据（int、string、double 等），并且会将数据类型信息一并写入文件。`BinaryReader` 按照写入的顺序和类型读回。

---

## 写入数据（BinaryWriter）

```csharp
FileStream fs = new FileStream(@"./data.bin", FileMode.OpenOrCreate, FileAccess.Write);
BinaryWriter bw = new BinaryWriter(fs, Encoding.UTF8);

// 写入不同类型的数据
bw.Write("字符串内容\n");                           // Write(string)
bw.Write(Encoding.UTF8.GetBytes("字节数组内容\n"));  // Write(byte[])
bw.Write(Encoding.UTF8.GetBytes("更多内容"));
bw.Write(12345);                                    // Write(int)
bw.Write(3.14);                                     // Write(double)

bw.Flush();    // 将缓冲区内容写入文件
bw.Close();    // 关闭二进制流
fs.Close();    // 关闭文件
```

---

## 读取数据（BinaryReader）

```csharp
FileStream fs = new FileStream(@"./data.bin", FileMode.OpenOrCreate, FileAccess.Read);
BinaryReader br = new BinaryReader(fs);

// 怎么写入的就怎么读取，顺序不能乱
// string data = br.ReadString();   // 读取字符串
// int num = br.ReadInt32();         // 读取整数

// 读取字节数组写入的数据
byte[] buffer = new byte[fs.Length];
string result = "";
while (br.Read(buffer, 0, buffer.Length) > 0)
{
    result = Encoding.UTF8.GetString(buffer);
}
Console.WriteLine(result);

br.Close();
fs.Close();
```

### 关键要点

| 要点 | 说明 |
|------|------|
| 写入顺序 | BinaryReader 按写入顺序读取，顺序不能乱 |
| 类型信息 | BinaryWriter 会记录数据类型，读取时需用对应类型的方法 |
| 常用读取方法 | `ReadString()`、`ReadInt32()`、`ReadDouble()`、`ReadBoolean()` 等 |
| Flush | 写入后必须调用 `Flush()` 确保缓冲区内容写入文件 |

---

---

## 相关笔记

- [[10-IO操作/FileStream]] — 底层字节流
- [[10-IO操作/StreamReader与StreamWriter]] — 文本流读写
- [[10-IO操作/文件IO概览]] — 文件 IO 概览
