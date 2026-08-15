---
title: 打开新窗口 Show 与 ShowDialog
tags:
  - CSharp
  - WinForms
  - 多窗体
---

# 打开新窗口：Show / ShowDialog

> **来源**：`D:\study\C#1\WinForm\stu0812\stu0813\打开新窗口`、`stu0805\8.5作业`

WinForms 程序通常有多个窗体（登录窗口 → 主窗口 → 子功能窗口），通过 `Show()` / `ShowDialog()` 打开。

---

## 1. Show() 非模态窗口

```csharp
// 非模态：两个窗口可以同时操作
Form2 form2 = new Form2();
form2.Show();          // 非阻塞，代码继续往下执行
this.Hide();           // 隐藏当前窗口
```

- 不阻塞当前代码
- 两个窗口都能操作
- 适合主窗口和辅助窗口并存

## 2. ShowDialog() 模态窗口

```csharp
// 模态：必须关闭子窗口才能操作父窗口
Form2 form2 = new Form2();
form2.ShowDialog();    // 阻塞，直到子窗口关闭才继续
```

- **阻塞**当前代码，关闭子窗口后才继续
- 父窗口不可操作
- 适合登录、设置、确认对话框

## 3. 登录 → 主窗口 切换模式

```csharp
// 登录窗口
private void BtnLogin_Click(object sender, EventArgs e)
{
    if (验证通过)
    {
        FrmMain main = new FrmMain(this);   // 把登录窗口引用传过去（可选）
        main.Show();
        this.Hide();        // 隐藏登录窗口
    }
}

// 主窗口 → 返回登录
private void button1_Click(object sender, EventArgs e)
{
    loginForm.Show();
    this.Close();
}
```

> 💡 **注意**：隐藏（`Hide`）和关闭（`Close`）不同。`Hide` 后还能 `Show` 回来；`Close` 会释放窗口。登录→主窗口推荐 `Hide` 当前窗口。

## 4. 打开注册窗口（ShowDialog 模式）

```csharp
// 登录窗口：打开注册
private void BtnReg_Click(object sender, EventArgs e)
{
    new Reg().ShowDialog();    // 注册窗口关闭后才继续
}

// 注册窗口：注册成功返回登录
private void button1_Click(object sender, EventArgs e)
{
    this.Close();   // 关闭注册窗口，回到登录窗口
}
```

## 5. Show vs ShowDialog 对比

| 对比 | Show() | ShowDialog() |
|------|--------|--------------|
| 阻塞 | 不阻塞 | 阻塞 |
| 返回值 | 无 | `DialogResult` |
| 父窗口 | 可操作 | 不可操作 |
| 适用 | 主/辅窗口并存 | 登录、设置、确认 |

> 💡 `ShowDialog()` 可以拿到返回值，配合 `DialogResult` 判断用户点了确定还是取消：
> ```csharp
> Form2 f2 = new Form2();
> if (f2.ShowDialog() == DialogResult.OK)
> {
>     // 用户点了确定
> }
> ```


---

## 相关笔记

- [[Winform/05-多窗体与数据传递/02-窗体之间数据传递]] — 数据传递
- [[Winform/02-事件机制/06-MessageBox消息框]] — MessageBox
