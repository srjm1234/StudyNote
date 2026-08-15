---
title: base 关键字
tags:
  - CSharp
  - 面向对象
  - 继承
---

# 子类访问父类 — `base` 关键字

> 来源：`D:\study\C#1\stu0724\子类访问父类`

`base` 关键字用于在子类中访问父类的成员（构造函数、方法、属性）。

## base 调用父类构造函数

当子类创建实例时，**必须先完成父类的初始化**。子类可以通过 `base()` 指定调用父类的哪一个构造函数：

```csharp
class People
{
    public string Name { get; set; }

    // 父类：无参构造函数
    public People()
    {
        Console.WriteLine("我是People类的无参构造函数");
    }

    // 父类：有参构造函数
    public People(string name) : base()  // 先调用 Object 的构造函数
    {
        Console.WriteLine("我是People类的有参构造函数");
        Name = name;
    }
}

class Man : People
{
    // 子类无参构造函数 — base("1") 指定调用父类有参构造函数
    public Man() : base("1")
    {
        Console.WriteLine("我是Man类的无参构造函数");
    }

    // 子类有参构造函数 — 没有 base() 指定，默认调用父类无参构造函数
    public Man(string name)
    {
        Console.WriteLine("我是Man类的有参构造函数");
    }
}
```

## 执行顺序

```
new Man()
  ├─ 先执行父类 People 的构造函数（通过 base() 指定）
  │     └─ base() → People(string name) → 输出"我是People类的有参构造函数"
  ├─ 再执行子类 Man 的构造函数体
  │     └─ 输出"我是Man类的无参构造函数"
```

## `this` vs `base`

| 关键字 | 含义 | 用途 |
|--------|------|------|
| `this` | 当前类的实例 | 调用本类的其他构造函数 `this(...)` |
| `base` | 父类的实例 | 调用父类的构造函数 `base(...)` 或父类方法 `base.Method()` |

> ⚠️ 如果子类构造函数**没有显式写 `base()`**，编译器会自动插入 `base()`（调用父类无参构造函数）。
> 如果父类**没有无参构造函数**，子类必须显式使用 `base()` 指定调用哪个有参构造函数。

---

---

## 相关笔记

- [[08-面向对象/类与对象/继承]] — 继承基础
- [[08-面向对象/类与对象/构造函数链式调用]] — this() 链式调用
- [[08-面向对象/类与对象/构造函数执行顺序]] — 构造函数执行顺序
