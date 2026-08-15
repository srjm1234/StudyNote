---
title: 列表控件 ListBox 与 ComboBox
tags:
  - CSharp
  - WinForms
  - 控件
---

# 列表控件：ListBox / ComboBox

> **来源**：`D:\study\C#1\WinForm\stu0804\01` 等

---

## 1. ListBox 列表框

一次性展示多个选项，可单选或多选。

```csharp
// 添加项
listBox1.Items.Add("项目1");
listBox1.Items.Add("项目2");

// 批量添加
listBox1.Items.AddRange(new string[] { "A", "B", "C" });

// 获取选中项
int index = listBox1.SelectedIndex;              // 索引（-1 表示未选中）
string item = listBox1.Items[index].ToString();  // 选中内容
string selected = listBox1.SelectedItem.ToString(); // 直接取选中项

// 移除/清空
listBox1.Items.Remove(item);
listBox1.Items.Clear();

// 选中变化时触发
private void listBox1_SelectedIndexChanged(object sender, EventArgs e)
{
    if (listBox1.SelectedIndex >= 0)
        label1.Text = listBox1.SelectedItem.ToString();
}
```

| 属性 | 说明 |
|------|------|
| `Items` | 项集合 |
| `SelectedIndex` | 选中项索引（-1 未选中） |
| `SelectedItem` | 选中项对象 |
| `SelectionMode` | 单选 / 多选（MultiExtended） |
| `Sorted` | 是否自动排序 |

## 2. ComboBox 下拉框

```csharp
// 添加选项
comboBox1.Items.Add("日用百货");
comboBox1.Items.Add("服装鞋袜");
comboBox1.Items.Add("数码产品");
comboBox1.SelectedIndex = 0;   // 默认选中第一个

// 获取选中项
string selected = comboBox1.Items[comboBox1.SelectedIndex].ToString();
string selectedText = comboBox1.Text;    // 当前显示的文本

// 移除
comboBox1.Items.RemoveAt(1);   // 按索引移除
comboBox1.Items.Clear();       // 清空

// 选中变化时触发
private void comboBox1_SelectedIndexChanged(object sender, EventArgs e)
{
    if (comboBox1.SelectedIndex >= 0)
        MessageBox.Show(comboBox1.SelectedItem.ToString());
}
```

| 属性 | 说明 |
|------|------|
| `Items` | 选项集合 |
| `DropDownStyle` | `DropDown`（可输入）/ `DropDownList`（只能选择） |
| `SelectedIndex` / `SelectedItem` | 选中项 |
| `MaxDropDownItems` | 下拉最多显示项数 |

> 💡 `DropDownList` 模式禁止手动输入，适合固定选项；`DropDown` 模式可以输入自定义内容。

## 3. ListBox vs ComboBox

| 对比 | ListBox | ComboBox |
|------|---------|----------|
| 显示 | 全部展开 | 收起，点击展开 |
| 占用空间 | 大 | 小 |
| 可输入 | 否 | 取决于 `DropDownStyle` |
| 适用 | 选项少、需要全部可见 | 选项多、节省空间 |

## 4. 实战：类别选择 + 数据联动

```csharp
// 类别下拉框 → 商品列表联动
private void comboBox1_SelectedIndexChanged(object sender, EventArgs e)
{
    listBox1.Items.Clear();
    string category = comboBox1.SelectedItem.ToString();
    switch (category)
    {
        case "日用百货":
            listBox1.Items.AddRange(new string[] { "毛巾", "牙膏", "牙刷" });
            break;
        case "服装鞋袜":
            listBox1.Items.AddRange(new string[] { "T恤", "牛仔裤", "运动鞋" });
            break;
    }
}
```

> 💡 联动思路：一个控件的 `SelectedIndexChanged` 事件里，根据选中内容刷新另一个控件的数据。


---

## 相关笔记

- [[Winform/03-常用控件/09-控件速查表]] — 控件速查
- [[Winform/03-常用控件/06-数据控件ListView与DataGridView]] — 数据控件
