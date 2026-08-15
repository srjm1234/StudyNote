---
title: Directory 与 DirectoryInfo
tags:
  - CSharp
  - IO
  - 目录操作
---

# Directory 与 DirectoryInfo — 目录操作

> 来源：`D:\study\C#1\stu0731\Director目录类 DirectorInfo目录信息类\Program.cs`

---

## Directory 静态类

`Directory` 是静态工具类，用于**操作文件夹**（创建、删除、移动）。

```csharp
string path = @"./data";

// 判断目录是否存在
Console.WriteLine(Directory.Exists(path));   // → true / false

// 创建目录（包括中间不存在的父目录也会自动创建）
Directory.CreateDirectory(path);

// 删除空目录
Directory.Delete(path);

// 删除非空目录（第二个参数 true 表示递归删除）
Directory.Delete(path, true);

// 移动目录（根目录一致，不能跨盘操作）
Directory.Move(path, @"../data");
```

### Directory 常用方法

| 方法 | 说明 |
|------|------|
| `Directory.Exists(path)` | 判断目录是否存在 |
| `Directory.CreateDirectory(path)` | 创建目录 |
| `Directory.Delete(path)` | 删除空目录 |
| `Directory.Delete(path, true)` | 删除非空目录（递归） |
| `Directory.Move(src, dest)` | 移动目录 |
| `Directory.GetFiles(path)` | 获取目录下所有文件路径 |
| `Directory.GetDirectories(path)` | 获取目录下所有子目录路径 |

---

## DirectoryInfo 实例类

`DirectoryInfo` 是实例类，适合**多次操作同一个目录**或**获取目录详细信息**。

```csharp
string path = @"./data";
DirectoryInfo dirInfo = new DirectoryInfo(path);

// 获取目录信息
Console.WriteLine("文件夹名称："       + dirInfo.Name);
Console.WriteLine("完整目录："         + dirInfo.FullName);
Console.WriteLine("文件夹属性："       + dirInfo.Attributes);
Console.WriteLine("创建时间："         + dirInfo.CreationTime);
Console.WriteLine("最后一次访问时间：" + dirInfo.LastAccessTime);
Console.WriteLine("最后一次写入时间：" + dirInfo.LastWriteTime);
```

---

## 综合应用示例：登陆注册系统

> 来源：`D:\study\C#1\stu0731\登陆 注册\Program.cs`

综合运用 `File` 类的读写方法，实现一个简单的文件存储登陆注册系统：

```csharp
public static string path = @"../../User/Data.txt";

static void Main(string[] args)
{
    bool flag = true;
    while (flag)
    {
        Console.WriteLine("请选择：1、登录  2、注册");
        string ID = Console.ReadLine();
        switch (ID)
        {
            case "1":
                flag = Login();
                break;
            case "2":
                Reg();
                break;
            default:
                break;
        }
    }
}

// 登录验证
public static bool Login()
{
    Console.WriteLine("输入账号：");
    string admin = Console.ReadLine();
    Console.WriteLine("输入密码：");
    string password = Console.ReadLine();
    string[] user = ReadUser();
    for (int i = 0; i < user.Length; i++)
    {
        string ad = user[i].Split(',')[0];
        string pw = user[i].Split(',')[1];
        if (ad == admin && pw == password)
        {
            Console.WriteLine("登录成功");
            return false;
        }
    }
    return true;
}

// 注册（追加写入）
public static void Reg()
{
    Console.WriteLine("请输入账号：");
    string admin = Console.ReadLine();
    Console.WriteLine("请输入密码：");
    string passwd = Console.ReadLine();
    WritData(admin + "," + passwd);
}

// 写入数据
public static void WritData(string user)
{
    File.AppendAllText(path, user + "\n");
}

// 读取数据
public static string[] ReadUser()
{
    return File.ReadAllLines(path);
}
```

### 数据存储格式

```
用户名,密码
admin,123456
zhangsan,abc123
lisi,pass456
```

> **要点**：使用逗号分隔存储多个字段，读取时用 `Split(',')` 解析。

---

---

## 相关笔记

- [[10-IO操作/文件IO概览]] — 文件 IO 概览
- [[10-IO操作/File与FileInfo]] — 文件操作
- [[10-IO操作/StreamReader与StreamWriter]] — 文本流读写
- [[Winform/06-文件与IO操作/01-File与Directory]] — WinForms 中的文件操作
