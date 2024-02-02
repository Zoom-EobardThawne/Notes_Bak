---
title: Markdown书写方法
author: 闲卧庭前远看帆
date: 2024/01/08
subject: Markdown 使用介绍
tags: 
- Markdown
categories: 
- 教程
- 笔记
---



# 简介

## 选择Markdown 的理由

- 轻量化
- 易读易写
- 排版方便快捷
- 支持图表、数学公式等
- 多个主流论坛和网站支持
- 方便转换其他文件格式
- ...

## 常用的Markdown 编辑工具

- Windows：Typora、Simplenote、有道云笔记、印象笔记、VSCode 等
- Mac：Typora、Typed、VSCode 等
- ...

# 基础语法

Markdown是一种简单的格式化文本的方法，在任何设备上看起来都很棒。它不会做任何花哨的事情，比如改变字体大小、颜色或类型——只是基本的，使用你已经知道的键盘符号。

## 标题

```markdown
# 一级标题
## 二级标题
### 三级标题
...
```

## 加粗

```markdown
**粗体**
```

## 斜体

```markdown
*斜体*
```

## 删除线

```markdown
~~ 删除线 ~~
两边加 双波浪号
```

## 上标

```markdown
X^2^ = Y
```

X^2^ = Y

## 下标

```markdown
H~2~O
```

H~2~O

## 代码块

````markdown
```C\java\C++...

//Code

```
````

```C++
#include <iostream>
using namespace std;
int main()
{
    cout << "(●'◡'●)" << endl;
	return;    
}
```

## 超链接

```markdown
//内联式链接
[链接名称/标签](链接地址)

//引用式链接
[Google][]
And then define the link:

[Google]: http://google.com/

```

[Google][]
And then define the link:

[Google]: http://google.com/	"谷歌"



## 脚注

```markdown
这是一段文字[^脚注标签]
....
....
[^脚注标签]: xxxxxxxxxxx
```

Markdown[^1]

[^1]: 是一种便于书写的标记型语言。



## 分割线

```markdown
---
```

---



## Latex 公式

```markdown
公式块：
$$
  Latex公式语法
$$

行内公式：
$ \frac {F}{D}   $
```

公式块：
$$
1Etu = \frac{F}{D}* \frac{1}{f}
$$

行内公式：
$ \frac {F}{D}   $

# 进阶语法

> 本节内容所列举的语法，不一定适用所有的 Markdown 编辑器。这里仅以 `Typora 1.3.8`为例。

## 字体修改

> 字体修改，需要使用html 标签的语法进行修改，这需要书写环境支持 html标签语法。

### 字体颜色

```html
<font color='red'>这里输入文字</font>
```

效果：<font color='red'>红色</font>

### 字体大小

```html
<font size='4'>这里输入文字</font>
```

效果：<font size='6'>6号大小</font>

### 字体样式

```html
<font face='楷体'>楷体</font>
```

效果：<font face='楷体'>楷体</font>

## 文档内链接

```markdown
<a name='tag'>文字部分</a>

[内容](#tag)
```

<a name='tag'>文字部分</a>

[内容](#tag)



## YAML Front Matter

一种结构化标记型语言，类似于json。常常用在文档开头，标识文件的属性信息。（如：标题、作者、标签、主题等）

按照 YAML 语法解析，便于在一些网站或编辑工具中生成特定信息。

在转换成PDF时，YAML Front Matter 也将作为文档属性信息被写入。

简单介绍几个常用的属性：

```yaml
title:  标题
author: 作者名
date:   写作日期(xxxx/xx/xx)
subject: 主题
tags: 
- 标签1
- 标签2
categories: 
- 分类1
- 分类2
```









