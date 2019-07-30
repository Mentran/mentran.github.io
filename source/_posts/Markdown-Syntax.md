title: Markdown Syntax
tags:
  - hexo
  - markdown
categories: []
date: 2018-04-11 20:53:00
---
# Markdown Syntax

## Basic

### 标题

    # 一级标题
    ## 二级标题
    ### 三级标题
    ##### 四级标题
    ###### 五级标题
    ####### 六级标题

### 列表

    * + - 都可以表示无序列表
    1. 表示有序列表
    * [ ] 待办事项
    * [x] 已办事项

### 引用

    > text
    >
    >> text
    >
    > text

<!-- more -->

### 强调

    *text*     # 斜体
    **text**   # 加粗
    ~~text~~   # 删除

### 分割线

    ***
    ---

### 图片与链接

    [链接文本](link "Title")
    ![alt text](/path/to/img.jpg "Title")

### 代码

    1. tab自动建立一个代码区块
    2. `代码` 可用于行内，嵌入到文字中
    3. 用 3个`+language 来写大块代码

### 表格

    |标题|标题|标题|
    |:-|:-:|-:|
    |居左|居中|居右|

    注：第二行用于分割表头和内容，每行两边都用|或者都不用
|标题|标题|标题|
|:-|-|-:|
|居左|居中|居右|


---


# Next 主题内建插件

### 引用块
```
{% centerquote %}
Everyone you will ever meet knows something you don't.
{% endcenterquote %}
```
{% centerquote %}
Everyone you will ever meet knows something you don't.
{% endcenterquote %}

```
{% blockquote 作者, 来源 %}
Mind.
{% endblockquote %}
```
{% blockquote 作者, 来源 %}
Mind.
{% endblockquote %}

```
{% blockquote Author http://linkaddress link content %}
Content
{% endblockquote %}
```
{% blockquote Author http://linkaddress link content %}
Content
{% endblockquote %}

### 其他嵌入

```text
    {% iframe url [width] [height] %}

    {% youtube video_id %}

    {% fullimage /image_path, alt, title %}

    # Bootstrap class_name
    {% note class_name %} Content {% endnote %}
```
{% note primary %} Bootstrap Primary {% endnote %}

***

![pikachu](/images/pikachu.png "pikapika")