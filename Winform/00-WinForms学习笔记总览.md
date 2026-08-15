---
title: WinForms 学习笔记总览
tags:
  - CSharp
  - WinForms
  - 总览
---

# C# WinForms 学习笔记总览

> 整理自 `D:\study\C#1\WinForm\` 下 `0804-作业` 与 stu0804–stu0812 共 8 个目录、80+ 个项目的源码，按知识点分类整理。
>
> 本目录采用与上级目录 `D:\study\chuanding\note\C#` 相同的组织方式：**编号分类文件夹 + 主题笔记**。原《03-WinForms完整笔记.md》已拆分为以下分类笔记，原文件备份在 `_archive\` 中。

---

# 一、目录导航

## 00 总览与索引

| 文件 | 说明 |
|------|------|
| [[00-WinForms学习笔记总览|00-WinForms学习笔记总览.md]] | 本文档：总览、目录导航、学习时间线 |

## 01-WinForm基础

| 文件 | 内容 |
|------|------|
| [[01-WinForm基础/01-项目结构与部分类|01-项目结构与部分类.md]] | 项目文件结构、partial class、Program 入口 |
| [[01-WinForm基础/02-窗体属性与Graphics绘图|02-窗体属性与Graphics绘图.md]] | 窗体常用属性、Graphics 测量/绘图、标题居中 |
| [[01-WinForm基础/03-坐标系统与控件定位|03-坐标系统与控件定位.md]] | 坐标系统、Location/Size、边界判断、窗体居中 |
| [[01-WinForm基础/04-手动添加控件|04-手动添加控件.md]] | 纯代码动态创建控件 |

## 02-事件机制

| 文件 | 内容 |
|------|------|
| [[02-事件机制/01-事件的基本使用|01-事件的基本使用.md]] | 事件三要素、+= / -= 订阅 |
| [[02-事件机制/02-事件的联动响应与sender|02-事件的联动响应与sender.md]] | 多控件共用事件、sender 强转 |
| [[02-事件机制/03-键盘事件|03-键盘事件.md]] | KeyUp / KeyDown / KeyPress、方向键移动 |
| [[02-事件机制/04-鼠标事件|04-鼠标事件.md]] | MouseEnter / MouseLeave / Click |
| [[02-事件机制/05-焦点与KeyPreview|05-焦点与KeyPreview.md]] | Focus / Select、KeyPreview 解决焦点问题 |
| [[02-事件机制/06-MessageBox消息框|06-MessageBox消息框.md]] | 完整参数、DialogResult、关闭确认 |

## 03-常用控件

| 文件 | 内容 |
|------|------|
| [[03-常用控件/01-基础控件Button与Label与TextBox|01-基础控件Button与Label与TextBox.md]] | Button / Label / TextBox |
| [[03-常用控件/02-容器控件Panel与GroupBox与TabControl|02-容器控件Panel与GroupBox与TabControl.md]] | Panel / GroupBox / TabControl |
| [[03-常用控件/03-选择控件CheckBox与RadioButton|03-选择控件CheckBox与RadioButton.md]] | 复选框、单选、全选 |
| [[03-常用控件/04-图片控件PictureBox与ImageList|04-图片控件PictureBox与ImageList.md]] | 图片加载、SizeMode、ImageList、保存图片 |
| [[03-常用控件/05-列表控件ListBox与ComboBox|05-列表控件ListBox与ComboBox.md]] | 列表项操作、下拉选择、联动 |
| [[03-常用控件/06-数据控件ListView与DataGridView|06-数据控件ListView与DataGridView.md]] | 多列表格、数据绑定、BindingList |
| [[03-常用控件/07-菜单与状态栏MenuStrip与ToolStrip|07-菜单与状态栏MenuStrip与ToolStrip.md]] | 菜单栏、工具栏、状态栏 |
| [[03-常用控件/08-定时器Timer与进度条ProgressBar|08-定时器Timer与进度条ProgressBar.md]] | 定时器、倒计时、进度条 |
| [[03-常用控件/09-控件速查表|09-控件速查表.md]] | 控件/事件/枚举速查 |

## 04-布局管理

| 文件 | 内容 |
|------|------|
| [[04-布局管理/01-手动定位与窗体居中|01-手动定位与窗体居中.md]] | Location/Size、居中公式 |
| [[04-布局管理/02-Dock与Anchor|02-Dock与Anchor.md]] | 停靠、锚定、自适应 |
| [[04-布局管理/03-TableLayoutPanel与FlowLayoutPanel|03-TableLayoutPanel与FlowLayoutPanel.md]] | 表格布局、流式布局、嵌套布局 |

## 05-多窗体与数据传递

| 文件 | 内容 |
|------|------|
| [[05-多窗体与数据传递/01-打开新窗口Show与ShowDialog|01-打开新窗口Show与ShowDialog.md]] | 模态/非模态窗口、窗口切换 |
| [[05-多窗体与数据传递/02-窗体之间数据传递|02-窗体之间数据传递.md]] | 构造函数传参、公共字段、静态变量 |
| [[05-多窗体与数据传递/03-窗体生命周期事件|03-窗体生命周期事件.md]] | Load / FormClosing / FormClosed |

## 06-文件与IO操作

| 文件 | 内容 |
|------|------|
| [[06-文件与IO操作/01-File与Directory|01-File与Directory.md]] | 文件/目录操作、递归遍历 |
| [[06-文件与IO操作/02-Path路径操作|02-Path路径操作.md]] | 路径拼接、拆分、相对路径 |
| [[06-文件与IO操作/03-对话框OpenFileDialog与SaveFileDialog|03-对话框OpenFileDialog与SaveFileDialog.md]] | 打开/保存对话框、Filter |
| [[06-文件与IO操作/04-文本流读写StreamReader与StreamWriter|04-文本流读写StreamReader与StreamWriter.md]] | 流式读写、编码、日志 |
| [[06-文件与IO操作/05-二进制序列化BinaryFormatter|05-二进制序列化BinaryFormatter.md]] | 对象序列化/反序列化 |
| [[06-文件与IO操作/06-INI配置文件|06-INI配置文件.md]] | Win32 API 读写 INI、记住密码 |
| [[06-文件与IO操作/07-文件操作综合实战|07-文件操作综合实战.md]] | 保存+查询+图片、工具类封装、CSV |

## 07-多线程编程

| 文件 | 内容 |
|------|------|
| [[07-多线程编程/01-Thread线程|01-Thread线程.md]] | 创建线程、前台/后台线程 |
| [[07-多线程编程/02-ThreadPool线程池|02-ThreadPool线程池.md]] | 线程池、Lambda 传参 |
| [[07-多线程编程/03-跨线程调用控件BeginInvoke|03-跨线程调用控件BeginInvoke.md]] | 跨线程异常、BeginInvoke 解决 |

## 08-面向对象与设计模式

| 文件 | 内容 |
|------|------|
| [[08-面向对象与设计模式/01-单例模式|01-单例模式.md]] | 私有构造 + 静态实例 + 全局访问点 |
| [[08-面向对象与设计模式/02-封装与访问修饰符|02-封装与访问修饰符.md]] | 访问修饰符、属性、数据模型类 |
| [[08-面向对象与设计模式/03-登录注册系统实战|03-登录注册系统实战.md]] | 文件存储注册登录完整实现 |

## 09-项目实战

| 文件 | 内容 |
|------|------|
| [[09-项目实战/01-图书管理系统|01-图书管理系统.md]] | 综合大项目：多窗体+文件+DataGridView |
| [[09-项目实战/02-计算器|02-计算器.md]] | sender 共用事件、字符串解析 |
| [[09-项目实战/03-猜数游戏|03-猜数游戏.md]] | Random、事件解绑、三元表达式 |
| [[09-项目实战/04-简易图书馆|04-简易图书馆.md]] | List 集合增删查 |

## 10-作业与练习

| 文件 | 内容 |
|------|------|
| [[10-作业与练习/01-阶段作业汇总|01-阶段作业汇总.md]] | 每日作业目录对照、练习清单 |

## 11-Socket网络编程

| 文件 | 内容 |
|------|------|
| [[11-Socket网络编程/00-Socket基础与TCP通信流程|00-Socket基础与TCP通信流程.md]] | Socket 概念、TCP 通信流程、IP/端口/编码 |
| [[11-Socket网络编程/01-Socket客户端实现|01-Socket客户端实现.md]] | 客户端：Connect / 收发 / 断开 |
| [[11-Socket网络编程/02-Socket服务端实现|02-Socket服务端实现.md]] | 服务端：Bind / Listen / Accept / 收发 |
| [[11-Socket网络编程/03-Socket代码问题分析|03-Socket代码问题分析.md]] | 编码不一致、buffer 截断、多连接等坑点 |

---

# 二、学习时间线

```
8.4  WinForm 入门：窗体结构、事件机制、坐标定位、键盘/鼠标事件
     ├─ 作业：计算器 / 猜数游戏 / 简易图书馆
8.5  控件进阶：分组类控件、菜单类控件、登录注册（文件存储）
8.6  图像与定时：ImageList、图片显示、Timer、全选
8.7  数据显示：DataGridView + BindingList、登录注册、图书管理系统（大项目）
8.10 文件操作：手动添加控件、File/Directory、流读写、序列化
8.11 布局进阶：Dock、Anchor、表格布局、文本/图片保存
8.12 多线程与配置：ListView、Tab/菜单、Thread、进度条、INI、单例模式
8.14 网络编程：Socket TCP/IP 通信、客户端/服务端、多线程收发
```

# 三、学习路径建议

1. **打基础**（01-03）：先懂项目结构、事件机制、常用控件
2. **做界面**（04）：布局管理让界面好看又自适应
3. **多窗口**（05）：登录 → 主窗口 → 子功能的切换和数据传递
4. **存数据**（06）：文件读写、序列化、INI 配置
5. **提性能**（07）：多线程 + BeginInvoke 避免界面卡死
6. **写模式**（08）：单例、封装、登录注册模板
7. **做项目**（09）：图书管理系统是综合检验，务必独立复现
8. **刷作业**（10）：对照每日作业自查掌握情况
9. **通网络**（11）：Socket 聊天室打通客户端/服务端，综合运用线程与 UI 更新

> 💡 建议配合源码阅读：每个分类笔记都标注了来源目录（如 `stu0804\事件使用`），对照源码 + 笔记学习效果最佳。
