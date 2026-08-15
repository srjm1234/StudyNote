---
title: 二进制序列化 BinaryFormatter
tags:
  - CSharp
  - WinForms
  - IO
---

# 二进制序列化：BinaryFormatter

> **来源**：`D:\study\C#1\WinForm\stu0810\test1`、`stu0812\0812作业`

把**对象**整个保存到文件（序列化），下次再从文件恢复成对象（反序列化）。不用手动逐字段拼接字符串。

---

## 1. 标记类为可序列化

```csharp
using System.Runtime.Serialization.Formatters.Binary;

// 用 [Serializable] 特性标记
[Serializable]
class Student
{
    public string Name { get; set; }
    public string Age { get; set; }
    public string Sex { get; set; }
    public string Birthday { get; set; }
}
```

> 💡 `[Serializable]` 告诉程序「这个类的对象可以被序列化」。没有这个标记，序列化时会抛异常。

## 2. 序列化（对象 → 文件）

```csharp
private void btnSave_Click(object sender, EventArgs e)
{
    // 1. 创建对象
    Student student = new Student()
    {
        Name = textBox1.Text,
        Age = textBox2.Text,
        Sex = textBox3.Text,
        Birthday = textBox4.Text,
    };

    // 2. 创建文件流（Create：覆盖写入）
    FileStream fs = new FileStream(@"./student.obj", FileMode.Create);

    // 3. 序列化写入
    BinaryFormatter bf = new BinaryFormatter();
    bf.Serialize(fs, student);

    // 4. 关闭
    fs.Close();
}
```

## 3. 反序列化（文件 → 对象）

```csharp
private void btnLoad_Click(object sender, EventArgs e)
{
    // 1. 打开文件
    FileStream fs = new FileStream(@"./student.obj", FileMode.Open);

    // 2. 反序列化（返回 object，要强转）
    BinaryFormatter bf = new BinaryFormatter();
    Student student = (Student)bf.Deserialize(fs);

    // 3. 关闭
    fs.Close();

    // 4. 使用数据
    textBox1.Text = student.Name;
    textBox2.Text = student.Age;
    textBox3.Text = student.Sex;
    textBox4.Text = student.Birthday;
}
```

## 4. 文本存储 vs 二进制序列化

| 方式 | 文件格式 | 可读性 | 空间 | 类型安全 |
|------|---------|--------|------|---------|
| 文本（StreamWriter） | 人类可读 | ✅ | 较大 | ❌ 需手动解析 |
| 二进制（BinaryFormatter） | 二进制 | ❌ | 较小 | ✅ 自动保持 |

> 💡 文本方式适合「人能看懂」的配置、日志；二进制序列化适合**整体保存对象**，恢复时类型完整。

## 5. 注意事项

> ⚠️ **重要**：
> 1. `BinaryFormatter` 在 .NET Core / .NET 5+ 中**已过时**（安全原因），.NET Framework 4.7.2 中仍可用。本课程项目用的就是 4.7.2。
> 2. 反序列化返回的是 `object`，**必须强转**成目标类型。
> 3. 类的结构变了（增删字段），反序列化旧文件可能失败。

## 6. 替代方案（了解）

| 方式 | 说明 |
|------|------|
| `JSON`（Newtonsoft.Json / System.Text.Json） | 现代推荐，文本可读 |
| `XML`（XmlSerializer） | 文本可读，结构复杂 |
| `BinaryFormatter` | 本课程使用，简单直接 |

> 学习阶段掌握 BinaryFormatter 即可，后续开发推荐 JSON。


---

## 相关笔记

- [[10-IO操作/BinaryReader与BinaryWriter]] — 二进制读写
- [[Winform/06-文件与IO操作/04-文本流读写StreamReader与StreamWriter]] — 文本流
