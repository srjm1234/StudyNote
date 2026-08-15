---
title: 选择控件 CheckBox 与 RadioButton
tags:
  - CSharp
  - WinForms
  - 控件
---

# 选择控件：CheckBox / RadioButton

> **来源**：`D:\study\C#1\WinForm\stu0806\全选`、`stu0811\表格布局`

用于让用户做选择：CheckBox 多选（可勾选多个），RadioButton 单选（一组只能选一个）。

---

## 1. CheckBox 复选框

```csharp
// 基本属性
checkBox1.Text = "记住密码";
checkBox1.Checked = true;          // 默认勾选
checkBox1.AutoSize = true;

// 获取状态
bool isChecked = checkBox1.Checked;   // true = 勾选，false = 未勾选

// 勾选状态变化时触发
private void checkBox1_CheckedChanged(object sender, EventArgs e)
{
    if (checkBox1.Checked)
    {
        // 勾选后的逻辑
    }
}
```

## 2. RadioButton 单选按钮

```csharp
// 同一容器（窗体/Panel/GroupBox）内的 RadioButton 自动互斥
radioButton1.Text = "男";
radioButton2.Text = "女";

// 判断选中
if (radioButton1.Checked) { /* 选了男 */ }
if (radioButton2.Checked) { /* 选了女 */ }

// 默认选中
radioButton1.Checked = true;
```

> 💡 **互斥规则**：RadioButton 只在**同一个容器**内互斥。想在两个分组里各自单选，就把它们放进不同的 Panel/GroupBox。

## 3. 实战：登录界面的记住密码 + 身份选择

```csharp
private void BtnLogin_Click(object sender, EventArgs e)
{
    // CheckBox：记住密码
    if (chkRemember.Checked)
    {
        // 保存密码（示例：写入 ini 文件，见 06-文件与IO操作/06-INI配置文件）
    }

    // RadioButton：身份单选
    string identity = radioStudent.Checked ? "学生" : "管理员";

    // 登录逻辑...
}
```

## 4. 实战：全选/反选

```csharp
// 全选按钮：把所有 CheckBox 都勾上
private void btnSelectAll_Click(object sender, EventArgs e)
{
    checkBox1.Checked = true;
    checkBox2.Checked = true;
    checkBox3.Checked = true;
}

// 或者遍历容器里的所有 CheckBox
private void btnSelectAll2_Click(object sender, EventArgs e)
{
    foreach (Control c in this.Controls)
    {
        if (c is CheckBox)          // 判断类型
        {
            ((CheckBox)c).Checked = true;
        }
    }
}
```

> 💡 `is` 关键字可以判断控件类型，配合 `foreach` 遍历容器批量操作同类控件，是处理大量选择控件的常用技巧。

## 5. 速查

| 控件 | 选择规则 | 关键属性 | 关键事件 |
|------|---------|---------|---------|
| `CheckBox` | 多选 | `Checked` | `CheckedChanged` |
| `RadioButton` | 单选（同容器互斥） | `Checked` | `CheckedChanged` |


---

## 相关笔记

- [[Winform/03-常用控件/09-控件速查表]] — 控件速查
- [[Winform/03-常用控件/01-基础控件Button与Label与TextBox]] — 基础控件
