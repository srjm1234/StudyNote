---
title: Array 数组方法
date: 2026-07-20
tags:
  - CSharp
  - 数组
source: D:\study\C#1\stu0720
---

# Array 数组方法

`Array` 是 C# 内置的静态类，提供操作数组的常用方法。基础数组知识见 [[03-数组/数组基础]]。

## 1. Array.Clear() — 清除元素

将指定范围内元素恢复为默认值（数字 → 0，引用类型 → null）。

```csharp
int[] num = { 1, 2, 3, 4, 5, 6, 7, 8 };
Array.Clear(num, 3, 4);
// 从索引 3 开始清除 4 个 → num[3..6] 变为 0
// 结果：[1, 2, 3, 0, 0, 0, 0, 8]
```

**参数：** `Clear(数组, 起始索引, 清除个数)`

## 2. Array.Copy() — 复制数组

**方式一：整体复制**

```csharp
int[] num2 = { 1, 2, 3, 4, 5, 6, 7, 8 };
int[] num3 = new int[4];
Array.Copy(num, num2, 8);
```

**方式二：指定范围复制**

```csharp
// Copy(源数组, 源起始索引, 目标数组, 目标起始索引, 复制个数)
Array.Copy(num2, 3, num, 3, 2);
```

## 3. Array.Reverse() — 反转数组

```csharp
Array.Reverse(num); // 原地反转
```

## 4. Array.Sort() — 排序

```csharp
Array.Sort(num); // 升序
```

## 5. Array.IndexOf() — 从前查找索引

```csharp
Array.IndexOf(num, 8);            // 查找 8 首次出现位置
Array.IndexOf(num, 2, 0);         // 从索引 0 开始查找
Array.IndexOf(num, 3, 2, 3);      // 从索引 2 起，查找 3 个位置内的 3
```

**反向查找：** `Array.LastIndexOf()`

## 6. Array.Resize() — 调整数组长度

```csharp
int[] num4 = new int[4];
Array.Resize(ref num4, 10); // 扩容到 10，多余位置默认值 0
```

> `ref` 关键字必需，因为 resize 会重新分配内存并替换原引用。

## Array.Find 系列方法

**核心概念**：`Array.Find()` 系列方法接收一个**判断函数**（返回 `bool`），对数组中每个元素调用，根据返回值筛选。这体现了"将方法作为参数传入"的委托思想，是 [[04-委托与Lambda/委托与Lambda]] 的实际应用。

### 方法总览

| 方法 | 功能 | 返回值 |
|------|------|--------|
| `Array.Find(arr, fn)` | 从前查找，返回第一个满足条件的**元素** | 元素值（或默认值） |
| `Array.FindIndex(arr, fn)` | 从前查找，返回第一个满足条件的**索引** | int（未找到 → -1） |
| `Array.FindLastIndex(arr, fn)` | 从后查找，返回最后一个满足条件的**索引** | int（未找到 → -1） |
| `Array.FindAll(arr, fn)` | 查找所有满足条件的元素 | 新数组 |
| `Array.Exists(arr, fn)` | 判断是否存在满足条件的元素 | bool |
| `Array.TrueForAll(arr, fn)` | 判断是否全部满足条件 | bool |
| `Array.ForEach(arr, action)` | 遍历数组，对每个元素执行操作 | void |

### 使用示例

```csharp
public static bool Fn(int i)          { return i < 20; }
public static bool FnWu(string name)  { return name.StartsWith("吴"); }

int[] ages = { 11, 32, 20, 25, 19, 18, 26, 17, 14 };
Array.Find(ages, Fn);               // 11（第一个 < 20 的）
Array.FindIndex(ages, Fn);          // 0
Array.FindLastIndex(ages, Fin);     // 6（最后一个奇数）
Array.FindAll(names, FnWu);         // 返回所有姓吴的人
Array.ForEach(names, Each);         // 遍历输出
```

## 自定义实现 Find 方法（作业）

自行实现 Array.Find 系列方法，加深对委托传参的理解：

```csharp
// 从前查找，返回第一个满足条件的元素
public static int Find_Num(int[] a, Func&lt;int, bool&gt; fn)
{
    for (int i = 0; i &lt; a.Length; i++)
        if (fn(a[i])) return a[i];
    return 0;
}

// 从前查找索引
public static int Find_Index(int[] a, Func&lt;int, bool&gt; fn)
{
    for (int i = 0; i &lt; a.Length; i++)
        if (fn(a[i])) return i;
    return -1;
}

// 从后查找索引
public static int Find_LastIndex(int[] a, Func&lt;int, bool&gt; fn)
{
    for (int i = a.Length - 1; i &gt;= 0; i--)
        if (fn(a[i])) return i;
    return -1;
}

// 查找所有满足条件的元素
public static int[] Find_AllNum(int[] a, Func&lt;int, bool&gt; fn)
{
    int[] b = new int[a.Length];
    int j = 0;
    for (int i = 0; i &lt; a.Length; i++)
        if (fn(a[i])) { b[j] = a[i]; j++; }
    return b;
}
```

## 去重随机数生成思路

```csharp
public static void RandomArr(int[] ints, int min, int max)
{
    Random r = new Random();
    for (int i = 0; i < ints.Length; i++)
    {
        while (true)
        {
            int num = r.Next(min, max);
            bool exists = false;
            for (int j = 0; j < i; j++)
                if (ints[j] == num) { exists = true; break; }
            if (!exists) { ints[i] = num; break; }
        }
    }
}
```

## 相关笔记

- [[03-数组/数组基础]] — 数组声明与遍历
- [[03-数组/数组进阶]] — 冒泡排序与二维数组
- [[04-委托与Lambda/委托与Lambda]] — 委托与 Lambda（Find 系列的基石）
- [[05-集合/集合与泛型]] — List\<T\> 动态数组（数组的替代方案）
