---
title: 数据控件 ListView 与 DataGridView
tags:
  - CSharp
  - WinForms
  - 控件
---

# 数据控件：ListView / DataGridView

> **来源**：`D:\study\C#1\WinForm\stu0812\ListView控件`、`stu0807\数据显示`

---

## 1. ListView 列表视图

以表格形式展示多列数据（适合「表头 + 多行记录」的界面）。

### 1.1 添加列

```csharp
// 设计器里添加 columnHeader1~5，代码里设置宽度
columnHeader1.Width = listView1.Width / 5;   // 平均分配列宽
columnHeader2.Width = listView1.Width / 5;
columnHeader3.Width = listView1.Width / 5;
columnHeader4.Width = listView1.Width / 5;
columnHeader5.Width = listView1.Width / 5;
```

### 1.2 添加行

```csharp
ListViewItem item = new ListViewItem("第一列");   // 第一列用构造参数
item.SubItems.Add("第二列");                       // 其余列用 SubItems
item.SubItems.Add("第三列");
item.SubItems.Add("第四列");
item.SubItems.Add("第五列");
listView1.Items.Add(item);
```

### 1.3 封装成方法

```csharp
// 一行数据 → ListView
public void Itemmm(string a, string b, string c, string d, string e)
{
    ListViewItem i = new ListViewItem(a);
    i.SubItems.Add(b);
    i.SubItems.Add(c);
    i.SubItems.Add(d);
    i.SubItems.Add(e);
    listView1.Items.Add(i);
}

// 调用
Itemmm(textBox1.Text, textBox2.Text, textBox3.Text, textBox4.Text, textBox5.Text);
```

### 1.4 自动滚动到最新项

```csharp
// 添加后定位到最后一行（常用于聊天、日志界面）
listView1.Items[listView1.Items.Count - 1].EnsureVisible();
```

### 1.5 窗体大小变化时重新分配列宽

```csharp
private void Form1_SizeChanged(object sender, EventArgs e)
{
    Buju();   // 重新计算列宽
}
```

> 💡 `ListView` 需要先有列（ColumnHeader），添加行时第一列用 `new ListViewItem(第一列内容)`，后续列用 `SubItems.Add()`。

## 2. DataGridView 数据网格

直接**绑定数据集合**，自动生成表格，适合大量数据的展示和编辑。

### 2.1 绑定数据源

```csharp
// 绑定 List<T>（一次性显示）
dataGridView1.DataSource = books;          // List<Book>
dataGridView1.DataSource = recordBooks;    // List<RecordBook>

// 绑定 BindingList<T>（增删时界面自动刷新）
BindingList<People> list = new BindingList<People>();
dataGridView1.DataSource = list;
list.Add(new People("张三", "12", "男", "郑州"));   // 自动刷新界面
```

### 2.2 数据模型

```csharp
// 类的公共属性会自动生成对应列
public class People
{
    public string Name { get; set; }
    public string Age { get; set; }
    public string Sex { get; set; }
    public string Addr { get; set; }

    public People(string name, string age, string sex, string add)
    {
        Name = name; Age = age; Sex = sex; Addr = add;
    }
}
```

### 2.3 增删查

```csharp
// 新增一行
list.Add(new People(textBox1.Text, textBox2.Text, textBox3.Text, textBox4.Text));

// 按条件删除（Lambda + FindAll）
List<People> peoples = FindAll(list, a => a.Addr == textBox8.Text || a.Name == textBox8.Text);
foreach (var i in peoples)
{
    list.Remove(i);
}
```

### 2.4 通用查找方法（泛型 + 委托）

```csharp
public delegate bool MyFn<T>(T obj);   // 自定义泛型委托

// 查找单个
public T Find<T>(BindingList<T> list, MyFn<T> factory)
{
    for (int i = 0; i < list.Count; i++)
    {
        if (factory(list[i])) return list[i];
    }
    return default;                     // 找不到返回默认值
}

// 查找全部
public List<T> FindAll<T>(BindingList<T> list, MyFn<T> factory)
{
    List<T> values = new List<T>();
    for (int i = 0; i < list.Count; i++)
    {
        if (factory(list[i])) values.Add(list[i]);
    }
    return values;
}
```

> 💡 `BindingList<T>` 相比 `List<T>` 的优势：增删元素时**自动通知** DataGridView 更新界面，无需重新绑定。

## 3. ListView vs DataGridView

| 对比 | ListView | DataGridView |
|------|----------|--------------|
| 添加方式 | 手动 `ListViewItem` | 直接绑定集合 |
| 自动列 | 否，需手动建列 | 是，按属性生成 |
| 自动刷新 | 否 | `BindingList<T>` 支持 |
| 适用 | 简单列表、图标视图 | 复杂表格、数据管理 |


---

## 相关笔记

- [[Winform/03-常用控件/09-控件速查表]] — 控件速查
- [[Winform/09-项目实战/01-图书管理系统]] — 图书管理系统实战
