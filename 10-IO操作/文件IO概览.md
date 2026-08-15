---
title: 文件 IO 概览
tags:
  - CSharp
  - IO
  - 文件操作
---

# 文件 IO 概览

> 来源：`D:\study\C#1\stu0731`

---

## 路径基础

| 路径类型 | 说明 | 示例 |
|----------|------|------|
| **绝对路径** | 以盘符开头 | `D:\study\C#1\file.txt` |
| **相对路径** | 相对当前位置 | `./file.txt`（当前目录）`../file.txt`（上一级） |
| **@ 字符串** | 防止转义 | `@"D:\study\file.txt"` |

---

## 文件 IO 类总览

```
System.IO 命名空间
├── FileStream            — 底层字节流（读写字节）
├── StreamReader/Writer   — 文本流（按行/按字符读写）
├── BinaryReader/Writer   — 二进制流（读写任意类型数据）
├── File                  — 静态工具类（一次性操作整个文件）
├── FileInfo              — 实例类（获取文件详细信息）
├── Directory             — 静态工具类（操作文件夹）
└── DirectoryInfo         — 实例类（获取文件夹详细信息）
```

### 选择建议

| 需求 | 推荐类 |
|------|--------|
| 快速读写整个文件（小文件） | `File.ReadAllText` / `File.WriteAllText` |
| 逐行读写文本 | `StreamReader` / `StreamWriter` |
| 读写任意数据类型 | `BinaryReader` / `BinaryWriter` |
| 精确控制字节流 | `FileStream` |
| 获取文件元数据（大小、创建时间） | `FileInfo` |
| 操作文件夹（创建/删除/移动） | `Directory` / `DirectoryInfo` |

---

---

## 相关笔记

- [[10-IO操作/File与FileInfo]] — File/FileInfo 文件操作
- [[10-IO操作/Directory与DirectoryInfo]] — 目录操作
- [[10-IO操作/FileStream]] — 底层字节流
- [[10-IO操作/StreamReader与StreamWriter]] — 文本流读写
- [[10-IO操作/BinaryReader与BinaryWriter]] — 二进制读写
