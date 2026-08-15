---
title: 文本流读写 StreamReader 与 StreamWriter
tags:
  - CSharp
  - WinForms
  - IO
---

# 文本流读写：StreamReader / StreamWriter

> **来源**：`D:\study\C#1\WinForm\stu0810\文件操作`、`stu0811\text文件`、`stu0811\保存text文件`

用「流」的方式读写文本文件，支持追加、按行读取、指定编码等，是文件操作的进阶写法。

---

## 1. 写入文件（StreamWriter）

```csharp
// === 写入（using 自动释放资源）===
using (FileStream fs = new FileStream(@"./my.txt", FileMode.Append, FileAccess.Write))
using (StreamWriter sw = new StreamWriter(fs))
{
    sw.WriteLine(textBox1.Text.Trim());   // 写一行
}
```

### 参数说明

```csharp
// FileMode：打开方式
FileMode.Append      // 追加（不覆盖原内容）
FileMode.Create      // 创建（存在则覆盖）
FileMode.Open        // 打开（不存在抛异常）
FileMode.OpenOrCreate // 打开或创建
FileMode.Truncate    // 打开并清空

// FileAccess：访问权限
FileAccess.Write     // 只写
FileAccess.Read      // 只读
FileAccess.ReadWrite // 读写
```

## 2. 读取文件（StreamReader）

```csharp
// === 读取全部 ===
using (FileStream fs = new FileStream(@"./my.txt", FileMode.Open, FileAccess.Read))
using (StreamReader sr = new StreamReader(fs))
{
    textBox1.Text = sr.ReadToEnd();       // 读取全部内容
    // string line = sr.ReadLine();       // 读取一行
}

// === 按行循环读取 ===
using (FileStream fs = new FileStream(@"./my.txt", FileMode.Open, FileAccess.Read))
using (StreamReader sr = new StreamReader(fs))
{
    string line;
    while ((line = sr.ReadLine()) != null)   // 读到末尾返回 null
    {
        // 逐行处理
        Console.WriteLine(line);
    }
}
```

## 3. 实战一：记事本（保存 + 读取）

```csharp
// 保存
private void button1_Click(object sender, EventArgs e)
{
    using (FileStream fs = new FileStream(@"./my.txt", FileMode.Append, FileAccess.Write))
    using (StreamWriter sw = new StreamWriter(fs))
    {
        sw.WriteLine(textBox1.Text.Trim());
    }
    textBox1.Clear();
}

// 读取
private void button2_Click(object sender, EventArgs e)
{
    using (FileStream fs = new FileStream(@"./my.txt", FileMode.Open, FileAccess.Read))
    using (StreamReader sr = new StreamReader(fs))
    {
        textBox1.Text = sr.ReadToEnd();
    }
}
```

## 4. 实战二：日志追加

```csharp
// 记录操作日志（追加模式）
FileStream fs = new FileStream(@"./log.txt", FileMode.Append, FileAccess.Write);
StreamWriter sw = new StreamWriter(fs);
sw.WriteLine(DateTime.Now.ToString() + "[操作正常...]");
sw.Close();
fs.Close();
```

> 💡 手动 `Close()` 也可以，但用 `using` 更安全：即使代码抛异常也会自动关闭释放资源。

## 5. 实战三：指定编码（中文不乱码）

```csharp
// UTF-8 编码写入（防止中文乱码）
using (StreamWriter sw = new StreamWriter(fs, Encoding.UTF8))
{
    sw.WriteLine("中文内容");
}

// 读取时也指定编码
using (StreamReader sr = new StreamReader(fs, Encoding.UTF8))
{
    string content = sr.ReadToEnd();
}
```

## 6. 快捷写法（简单场景）

```csharp
// 简单场景可以直接用 File 类的静态方法
File.WriteAllText(path, content);        // 写
File.AppendAllText(path, content);       // 追加
File.ReadAllText(path);                  // 读
File.ReadAllLines(path);                 // 读成字符串数组
```

| 场景 | 推荐 |
|------|------|
| 一次性读写 | `File.ReadAllText / WriteAllText` |
| 追加日志 | `File.AppendAllText` |
| 大文件/逐行处理 | `StreamReader / StreamWriter` |
| 需要编码控制 | Stream + 指定 `Encoding` |


---

## 相关笔记

- [[10-IO操作/StreamReader与StreamWriter]] — 控制台文本流
- [[Winform/06-文件与IO操作/07-文件操作综合实战]] — 文件操作实战
