---
title: String 字符串
date: 2026-07-14
tags:
  - CSharp
  - 字符串
source: D:\study\C#1\stu0714\StuString
---

# String 字符串

`string` 类提供了写好的用于操作字符串的方法。字符串：多个字符组成的序列，属于**引用类型**。

## 属性与索引

可以通过索引获取到字符串中的单个字符。

```csharp
string s = "abc123";
Console.WriteLine(s[0]); // 输出 a
int i = s[2];
Console.WriteLine(i);    // 输出 c（ASCII 码 99）

// Length 获取字符串长度（字符个数）
Console.WriteLine(s.Length); // 输出 6
```

## 拼接

### Concat() — 拼接字符串

```csharp
string s1 = "464646464";
string s2 = "black";
string s3 = "飞机";
string s4 = string.Concat(s1, s2, s3, "飞起来！");
Console.WriteLine(s4);
// 输出：464646464black飞机飞起来！
```

> 更常用的是 `+` 拼接与 `$` 插值，见 [[01-基础语法/定义变量]]。

## 判断类方法

### Contains() — 判断是否存在字符串

```csharp
bool b = s4.Contains(s1);
Console.WriteLine(b); // 输出 True
```

### StartsWith() / EndsWith() — 判断开头/结尾

```csharp
string a = "装B让你飞飞飞起来";
Console.WriteLine(a.StartsWith("B")); // 输出 False
Console.WriteLine(a.EndsWith("起来")); // 输出 True
```

### Equals() — 判断字符串是否相等

```csharp
Console.WriteLine(a.Equals("起来")); // 输出 False
```

### IsNullOrEmpty() — 判断是否为 null 或空

```csharp
string s1 = null;
Console.WriteLine(string.IsNullOrEmpty(s1)); // 输出 True
```

> 静态方法，安全判断字符串是否为空或 `null`，避免空引用异常。

## 查找类方法

### IndexOf() / LastIndexOf() — 查找字符位置

```csharp
string a = "装B让你飞飞飞起来";
Console.WriteLine(a.IndexOf("飞"));    // 输出 4（"飞"首次出现的位置）
Console.WriteLine(a.LastIndexOf("飞")); // 输出 6（"飞"最后一次出现的位置）
```

### IndexOfAny() / LastIndexOfAny() — 查找字符数组中任意字符

```csharp
char[] chars = new char[] { '飞', '装', 'B' };

// 从前往后：找到字符数组中最先匹配到的字符的索引
Console.WriteLine(a.IndexOfAny(chars));
// 输出 0（'装'在索引0处，是字符数组中最先匹配到的）

// 从后往前：找到字符数组中最后匹配到的字符的索引
Console.WriteLine(a.LastIndexOfAny(chars));
// 输出 6（最后一个'飞'在索引6处，是字符数组中最后匹配到的）
```

## 修改类方法

### Insert() — 在指定索引位置插入字符串

```csharp
string str1 = "吴亦凡";
string str2 = str1.Insert(1, "罗志祥");  // → "吴罗志祥凡"
Console.WriteLine(str2);
```

> `Insert(int index, string value)` 返回一个新字符串，原字符串不被修改。

### Replace() — 替换指定内容

```csharp
string str4 = "来日方长方";
Console.WriteLine(str4.Replace("日", "hahaha"));   // → "来hahaha方长方"
string str04 = str4.Replace("方", "仿");
Console.WriteLine(str04.Replace("长", "仿哈哈哈")); // 支持链式调用
```

### Remove() — 删除指定位置的字符

```csharp
string str7 = "qwerty12345";
Console.WriteLine(str7.Remove(3));   // 从索引3删到末尾 → "qwe"
Console.WriteLine(str7.Remove(1, 2)); // 从索引1删2个 → "erty12345"
```

### ToUpper() / ToLower() — 大小写转换

```csharp
string str5 = "abcEFG";
Console.WriteLine(str5.ToUpper());  // → "ABCEFG"
Console.WriteLine(str5.ToLower());  // → "abcefg"
```

## 分割与转换

### Split() — 按指定字符分割为数组

```csharp
string str8 = "黑鹰：,装X,让你,飞起来";
string[] fly = str8.Split(',');
foreach (string str in fly)
{
    Console.WriteLine(str);
}
// 输出：黑鹰： / 装X / 让你 / 飞起来
```

### Join() — 按分隔符拼接数组为字符串

```csharp
string[] str3 = new string[] { "a", "b", "c", "d" };
Console.WriteLine(string.Join("*", str3));  // → "a*b*c*d"

char[] chars = new char[] { 'a', 'b', 'c', 'd' };
Console.WriteLine(string.Join(" ", chars)); // → "a b c d"
```

### ToCharArray() — 字符串转字符数组

```csharp
string str9 = "来来 bxl";
char[] chars01 = str9.ToCharArray();
foreach (char c in chars01)
{
    Console.WriteLine(c);  // 输出每个字符，包括空格
}
```

### CopyTo() — 复制字符到数组

```csharp
string s4 = "1234567890";
char[] num01 = new char[10];
// CopyTo(被复制开始的索引位置, 目标数组, 目标数组起始位置, 复制个数)
s4.CopyTo(3, num01, 0, 9);
foreach (char c in num01)
{
    Console.WriteLine(c);
}
```

## 自定义字符串工具方法

在类库项目中，可以封装常用的字符串处理方法作为**静态工具方法**，供其他项目引用使用（类库引用见 [[01-基础语法/命名空间]]）。

### StrIndex() — 查找字符首次出现位置

```csharp
public class Rate
{
    // 重载 1：在字符串中查找字符首次出现位置
    public static int StrIndex(string a, string b)
    {
        for (int i = 0; i < a.Length; i++)
        {
            if (a[i].ToString() == b) return i;
        }
        return -1;  // 未找到返回 -1
    }

    // 重载 2：在字符串数组中查找元素首次出现位置
    public static int StrIndex(string[] a, string b)
    {
        for (int i = 0; i < a.Length; i++)
        {
            if (a[i].ToString() == b) return i;
        }
        return -1;
    }
}

// 使用
Console.WriteLine(Rate.StrIndex("asad", "a"));  // → 0
string[] arr = { "苹果", "香蕉", "橘子" };
Console.WriteLine(Rate.StrIndex(arr, "橘子"));  // → 2
```

### 与内置 IndexOf 对比

| 方法 | 来源 | 参数 | 返回值 |
|------|------|------|--------|
| `string.IndexOf(char)` | .NET 内置 | 单个字符 | 索引 / -1 |
| `string.IndexOf(string)` | .NET 内置 | 字符串 | 索引 / -1 |
| `Rate.StrIndex(string, string)` | 自定义 | 字符串 + 查找目标（string） | 索引 / -1 |
| `Rate.StrIndex(string[], string)` | 自定义 | 字符串数组 + 查找目标 | 索引 / -1 |

> **注意**：自定义 `StrIndex` 通过逐个字符比对实现，功能上等价于 `IndexOf`。方法重载概念见 [[08-面向对象/类与对象/方法重载]]。

## 相关笔记

- [[07-方法/方法(函数)]] — 手写 Contains / StartStr / EndStr 字符串方法
- [[01-基础语法/命名空间]] — 类库引用（自定义工具类）
- [[08-面向对象/类与对象/方法重载]] — 方法重载（StrIndex 重载）
``