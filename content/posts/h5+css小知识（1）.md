---
title: "html5+css小知识（1）"
date: 2023-08-07T09:21:16+08:00
draft: false
slug: "2308070957"
authors: ["howey"]
tags: ["H5", "css"]
series: ["编程系列"]
categories: ["前端"]
---
# 文本元素解析
1. `<br>`强制换行、`<wbr>`安全换行

    解释：在任意文本位置键入<br>都会被换行，而在英文单词过长时使用<wbr>会根据浏览器的宽度适当的裁切换行。

```html
<br> Thisabc<wbr>dkedkslakdj<wbr>fkdlsakd is apple.
```

    
2. `<i>`表示外文词汇或科技术语

    解释：`<i>`元素实际作用就是倾斜。从语义上来看，表示区分周围内容，并不是特别强调或重要性。

3. `<em>`加以强调

    解释：`<em>`元素实际作用和`<i>`一样，就是倾斜；从语义上来看，表示对一段文本的强调。

4. `<s>`表示不准确或校正 

    解释：`<del>`<del>元素</del>实际作用和`<s>`一样，就是删除线；从语义上来看，表示删除相关文字。

5. `<ins>`添加一段文本

    解释：`<ins>`<ins>元素</ins>实际作用和`<u>`一样，加一条下划线；从语义上来看，是添加一段文本，起到强调的作用。

6. `<small>`添加小号字体

    解释：`<small>`<small>元素</small>实际作用就是将文本放小一号。从语义上来看，用于免责声明和澄清声明。

7. `<sub><sup>`添加上标和下标

    解释：`<sub>`和`<sup>`<sup>元素</sup> 实际作用就是数学的上标和下标。语义也是如此。

8. `<code>`等表示输入和输出

    解释：`<code>` <code>表示计算机</code>代码片段；`<var>`表示编程语言中的变量；`<samp>`<samp>表示程序</samp>或计算机的输出；`<kdb>`<kdb>表示</kdb>用户的输入。由于这属于英文范畴的，必须将 lang="en"英语才能体现效果。

9. `<abbr>`表示缩写

    解释：<abbr>元素没有实际作用；从语义上看，是一段文本的缩写。

10. `<dfn>`表示定义术语

    解释：`<dfn>`元素就是一般性的倾斜；从语义上看，表示解释一个词或短语的一段文本。

11. `<q>`引用来自他处的内容

    解释：`<q>`<q>元素</q>实际作用就是加了一对双引号。从语义上来看，表示引用来自其他地方的内容。

12. `<cite>`引用其他作品的标题

    解释：<cite>元素实际作用就是加粗。从语义上来看，表示引用其他作品的标题。

13. `<ruby>`语言元素

    解释：<ruby>用来为非西方语言提供支持。<rp><rt>用来帮助读者掌握表意语言文字正确发音。比如：汉语拼音在文字的上方。但目前 Firefox 还不支持此特性。

示例代码：
```html
<ruby> 饕<rp>(</rp><rt>tāo</rt><rp>)</rp> 餮<rp>(</rp><rt>tiè</rt><rp>)</rp> </ruby>
```
示例效果：<ruby> 饕<rp>(</rp><rt>tāo</rt><rp>)</rp> 餮<rp>(</rp><rt>tiè</rt><rp>)</rp> </ruby>

14. `<bdo>`设置文字方向

    解释：<bdo>必须使用属性 dir 才可以设置，一共两个值：rtl（从右到左）和 ltr（从左到右）。一般默认是 ltr。还有一个<bdi>元素也是处理方向的，由于是特殊语言的特殊效果，且主流浏览器大半不支持，忽略。

15. `<mark>`突出显示文本

    解释：`<mark>`<mark>实际</mark>作用就是加上一个黄色的背景，黑色的字；从语义上来看，与上下文相关而突出的文本，用于记号。

16. `<time>`表示日期和时间

    解释：`<time>`元素没有实际作用；从语义上来看，表示日期和时间。

# 分组元素

## <figure><figcaption>使用插图

```
<figure>
    <figcaption> 这是一张图 </figcaption>
    <img src="img.png">
</figure>
```

# 嵌入元素

1. `<progress>`**显示进度**

    解释：`<progress>`元素可以显示一个进度条，一般通过 JS 控制内部的值。IE9 以及更低版本不支持此元素。

    示例代码:
    ```
    <progress value="30" max="100"></progress>
    ```
    效果展示：
    <progress value="30" max="100"></progress>

2. `<meter>`**显示范围里的值**

    解释：`<meter>`元素显示某个范围内的值。其下的属性包含：min 和 max 表示范围边界， low 表示小于它的值过低，high 表示大于它的值过高，optimum 表示最佳值，但不出现效果。IE 浏览器不支持此元素。

     示例代码:
    ```
    <meter value="90" min="10" max="100" low="40" high="80" optimum="60"></meter>
    ```
    效果展示：
    <meter value="90" min="10" max="100" low="40" high="80" optimum="60"></meter>

# 表单元素

1. `datalist` 定义一组提供给用户的建议值

示例代码:

```html
<form action="/action_page.php">
    <input list="browsers" name="browser">
    <datalist id="browsers">
        <option value="Internet Explorer">
        <option value="Firefox">
        <option value="Chrome">
        <option value="Opera">
        <option value="Safari">
    </datalist>
    <input type="submit" value="Submit">
</form>
```
<hr>
效果展示：

<form action="/action_page.php">
    <input list="browsers" name="browser">
    <datalist id="browsers">
        <option value="Internet Explorer">
        <option value="Firefox">
        <option value="Chrome">
        <option value="Opera">
        <option value="Safari">
    </datalist>
    <input type="submit" value="Submit">
</form>

<hr style="margin-top:15px;">

2. `type`为color时

    解释：实现文本框获取颜色的功能，最新的现代浏览器测试后 IE 不支持，其余的都能显示一个颜色对话框提供选择。


```
<input type="color">
```

# 全局属性和其他

1. `accesskey` **属性**

    解释：快捷键操作，windows 下 alt+指定键，前提是浏览器 alt 并不冲突。

    `<input type="text" name="user" accesskey="n">`

2. `contenteditable`**属性**

    解释：让文本处于可编辑状态，设置 true 则可以编辑，false 则不可编辑。或者直接设置属性。

    `<p contenteditable="true">我可以修改吗</p>s`

3. `tabindex`**属性**

    解释：表单中按下 tab 键，实现获取焦点的顺序。如果是-1，则不会被选中。

    `<input type="text" name="user" tabindex="2">`