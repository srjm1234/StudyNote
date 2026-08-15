---
title: INI 配置文件
tags:
  - CSharp
  - WinForms
  - IO
---

# INI 配置文件

> **来源**：`D:\study\C#1\WinForm\stu0812\stu0813\ini配置文件`

INI 是轻量级配置文件格式，用「节 + 键值对」保存设置（如记住密码、界面主题）。

---

## 1. INI 文件格式

```ini
[用户信息]
张三=25
李四=30

[系统设置]
theme=dark
language=zh-CN
```

- `[节名]`：用方括号包裹，划分区域
- `键=值`：节内的键值对
- 一个文件可以有多个节

## 2. 使用 Win32 API 读写 INI

C# 通过调用系统 `kernel32.dll` 的函数来读写 INI（Windows 内置功能，无需第三方库）。

### IniAPI 封装类

```csharp
using System.Runtime.InteropServices;
using System.Text;

public static class IniAPI
{
    // 声明 Win32 API：写入
    [DllImport("kernel32.dll", CharSet = CharSet.Auto)]
    private static extern bool WritePrivateProfileString(
        string lpAppName, string lpKeyName, string lpString, string lpFileName);

    // 声明 Win32 API：读取
    [DllImport("kernel32.dll", CharSet = CharSet.Auto)]
    private static extern uint GetPrivateProfileString(
        string lpAppName, string lpKeyName, string lpDefault,
        StringBuilder lpReturnedString, uint nSize, string lpFileName);

    // 写入：节名、键名、值、文件路径
    public static bool INIWriteValue(string iniFile, string section, string key, string value)
    {
        return WritePrivateProfileString(section, key, value, iniFile);
    }

    // 读取：找不到时返回默认值
    public static string INIReadValue(string iniFile, string section, string key, string def)
    {
        StringBuilder sb = new StringBuilder(1024);
        GetPrivateProfileString(section, key, def, sb, 1024, iniFile);
        return sb.ToString();
    }
}
```

> 💡 `DllImport` 是 C# 调用系统底层 API 的入口，本案例只需照抄，重点是**会调用** IniAPI 类。

## 3. 使用示例

```csharp
// 配置文件路径（放在程序运行目录）
string iniPath = Directory.GetCurrentDirectory() + "\\config.ini";

// 写入
IniAPI.INIWriteValue(iniPath, "用户信息", "姓名", "张三");
IniAPI.INIWriteValue(iniPath, "用户信息", "年龄", "25");

// 读取（第 4 个参数是找不到时的默认值）
string name = IniAPI.INIReadValue(iniPath, "用户信息", "姓名", "未知");
string age = IniAPI.INIReadValue(iniPath, "用户信息", "年龄", "0");
```

## 4. 实战：记住密码

```csharp
// 登录成功时保存
private void BtnLogin_Click(object sender, EventArgs e)
{
    if (chkRemember.Checked)   // 勾选了"记住密码"
    {
        IniAPI.INIWriteValue(iniPath, "登录", "账号", txtAccount.Text);
        IniAPI.INIWriteValue(iniPath, "登录", "密码", txtPassword.Text);
    }
}

// 窗体加载时回填
private void Form1_Load(object sender, EventArgs e)
{
    txtAccount.Text = IniAPI.INIReadValue(iniPath, "登录", "账号", "");
    txtPassword.Text = IniAPI.INIReadValue(iniPath, "登录", "密码", "");
}
```

## 5. INI vs 文本文件 vs 序列化

| 方式 | 格式 | 适用 |
|------|------|------|
| 文本文件 | 自由 | 日志、简单记录 |
| INI | 节 + 键值对 | **配置项**（少而固定） |
| 序列化 | 二进制 | 保存完整对象 |

> 💡 INI 特别适合「少量、固定、需要经常读写」的配置，比如记住密码、窗口大小、主题设置。


---

## 相关笔记

- [[Winform/06-文件与IO操作/01-File与Directory]] — 文件操作
- [[Winform/08-面向对象与设计模式/03-登录注册系统实战]] — 登录注册（记住密码）
