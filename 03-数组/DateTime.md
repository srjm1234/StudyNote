---
title: DateTime 类
date: 2026-07-14
tags:
  - CSharp
  - 日期时间
  - DateTime
source: D:\study\C#1\stu0714\DateTime类
---

# DateTime 类

> C# 中用于处理日期和时间的核心类，位于 `System` 命名空间。

---

## 1. 获取当前时间

```csharp
DateTime NowTime = DateTime.Now;
Console.WriteLine(NowTime);   // 输出完整日期时间，如 2026/7/14 14:30:00
```

---

## 2. 时间属性（获取时间详情）

| 属性            | 说明        | 示例   |
| ------------- | --------- | ---- |
| `Year`        | 年份        | 2026 |
| `Month`       | 月份        | 7    |
| `Day`         | 日期        | 14   |
| `Hour`        | 小时（24小时制） | 14   |
| `Minute`      | 分钟        | 30   |
| `Second`      | 秒         | 0    |
| `Millisecond` | 毫秒        | 0    |

```csharp
Console.WriteLine("year:" + NowTime.Year);
Console.WriteLine("month:" + NowTime.Month);
Console.WriteLine("day:" + NowTime.Day);        // ⚠️ 注意：获取日期用 Day，不是 Hour
Console.WriteLine("hour:" + NowTime.Hour);
Console.WriteLine("minute:" + NowTime.Minute);
Console.WriteLine("second:" + NowTime.Second);   // ⚠️ 注意：获取秒用 Second，不是 Hour
Console.WriteLine("millsecond:" + NowTime.Millisecond);
```

### DayOfWeek — 星期几

- 返回枚举 `DayOfWeek`，**0 = 星期日，1 = 星期一，…，6 = 星期六**
- 可强制转为 `int` 使用

```csharp
Console.WriteLine("今天周几：" + (int)NowTime.DayOfWeek);  // 0=周日, 1=周一 ...
```

### DayOfYear — 年内第几天

```csharp
Console.WriteLine("今天是今年的第几天：" + NowTime.DayOfYear + "天");
```

---

## 3. 格式化输出 `ToString()`

### 标准格式

| 格式符 | 说明 | 示例输出 |
|--------|------|----------|
| `"G"` | 完整日期 + 长时间（含秒） | `2026/7/14 14:30:00` |
| `"g"` | 简短日期 + 短时间（含秒） | `2026/7/14 14:30` |
| `"s"` | ISO 8601 格式 | `2026-07-14T14:30:00` |
| `"d"` | 短日期 | `2026/7/14` |
| `"D"` | 长日期 | `2026年7月14日` |
| `"F"` | 完整日期时间（含星期） | `2026年7月14日 14:30:00` |
| `"m"` | 月日 | `7月14日` |

```csharp
Console.WriteLine(NowTime.ToString("G"));
Console.WriteLine(NowTime.ToString("g"));
Console.WriteLine(NowTime.ToString("s"));
Console.WriteLine(NowTime.ToString("d"));
Console.WriteLine(NowTime.ToString("D"));
Console.WriteLine(NowTime.ToString("F"));
Console.WriteLine(NowTime.ToString("m"));
```

### 自定义格式

| 占位符 | 说明 |
|--------|------|
| `y` / `yy` | 2 位年份 |
| `yyyy` | 4 位年份 |
| `M` / `MM` | 月份（01~12） |
| `d` / `dd` | 日期（01~31） |
| `h` / `hh` | 12 小时制（01~12） |
| `H` / `HH` | 24 小时制（00~23） |
| `m` / `mm` | 分钟（00~59） |
| `s` / `ss` | 秒（00~59） |
| `f` ~ `fffffff` | 秒的小数位数（毫秒/微秒） |
| `dddd` | 星期全称（如"星期一"） |
| `ddd` | 星期缩写 |

```csharp
// h 和 H 必须与其他字符串组合使用，否则会报错
Console.WriteLine(NowTime.ToString("现在的小时是h"));  // 12小时制
Console.WriteLine(NowTime.ToString("现在的小时是H"));  // 24小时制

// 毫秒（3位）
Console.WriteLine(NowTime.ToString("fff"));

// 自定义完整格式
Console.WriteLine(NowTime.ToString("今天是yyyy年MM月dd日，h时mm分，dddd"));
// 输出示例：今天是2026年07月14日，2时30分，星期一
```

---

## 4. Unix 时间戳

> 以 **格林威治时间 1970-01-01 00:00:00** 为起点计算总秒数/总毫秒数。

```csharp
// 使用 DateTimeOffset 转换
long t1 = new DateTimeOffset(DateTime.Now).ToUnixTimeSeconds();      // 秒级时间戳
long t2 = new DateTimeOffset(DateTime.Now).ToUnixTimeMilliseconds(); // 毫秒级时间戳
Console.WriteLine(t1);
Console.WriteLine(t2);
```

---

## 5. 创建指定时间

```csharp
// 最小组成单位：年、月、日
DateTime my = new DateTime(2004, 12, 21);
Console.WriteLine(my);   // 输出：2004/12/21 00:00:00
```

> ⚠️ `DateTime` 的最小精度到 **年、月、日**，时分秒默认为 `00:00:00`。

---

## 6. 时间运算

### 增减时间（返回新对象，不修改原对象）

```csharp
DateTime time = DateTime.Now;
time = time.AddDays(1);    // +1 天
time = time.AddHours(1);   // +1 小时
time = time.AddYears(1);   // +1 年

Console.WriteLine(time.Day);
Console.WriteLine(time.Hour);
Console.WriteLine(time.Year);
```

> ⚠️ `AddDays`、`AddHours`、`AddYears` 等方法 **返回新的 DateTime 对象**，不会修改原对象，所以需要重新赋值。

### 时间比较

直接使用比较运算符：

```csharp
if (DateTime.Now > new DateTime(2009, 2, 3))
{
    Console.WriteLine("今年是2009后");
}
else
{
    Console.WriteLine("今年是2009前");
}
```

### 时间相减 → TimeSpan

两个 `DateTime` 相减，结果类型为 **`TimeSpan`**（时间间隔）：

```csharp
// 到 2027 年还有多少天
TimeSpan show = new DateTime(2027, 1, 1) - DateTime.Now;
Console.WriteLine($"到27年还有{show.Days}天");

// 从 2020 年 12 月 31 日到今天过了多少天
TimeSpan show2 = new DateTime(2020, 12, 31) - DateTime.Now;
Console.WriteLine($"从2020年到今天有{-show2.Days}天");  // 注意取反
```

> ⚠️ 如果 `结束时间 < 开始时间`，`TimeSpan.Days` 会是负数，需取反（`-show.Days`）得到正数。

---

## 常见坑点

| 坑点 | 说明 |
|------|------|
| `NowTime.Day` vs `NowTime.Hour` | 获取日期用 `Day`，获取小时用 `Hour`，不要混用 |
| `NowTime.Second` vs `NowTime.Hour` | 获取秒用 `Second`，不是 `Hour` |
| `AddXxx()` 不修改原对象 | 这些方法返回新对象，必须重新赋值 |
| `h` 格式必须组合使用 | `ToString("h")` 会报错，需写 `ToString("现在是h时")` |
| `TimeSpan.Days` 可能为负 | 结束时间早于开始时间时需取反 |

---

## 相关笔记

- [[03-数组/数组基础]] — 数组基础（同阶段学习内容）
- [[09-练习与项目/停车场管理系统]] — 停车场项目（DateTime 记录进出场时间、TimeSpan 计算时长）
- [[10-IO操作/File与FileInfo]] — 文件时间信息（CreationTime 等）
