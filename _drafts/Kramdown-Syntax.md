---
layout: post
time: 2014-03-01
title: 【译文】Kramdown语法中文翻译
category: 翻译
keywords: Kramdown Syntax
tags: Kramdown Syntax
description: kramdown语法的中文翻译
---

> 原文地址:<http://kramdown.gettalong.org/syntax.html> 

说明: 当前文档是基于Kramdown 1.3.2版本

# Kramdown语法(Kramdown Syntax)

**Kramdown**的语法是基于Markdown的语法的，但是在这基础之上又有所扩充，扩充的这些特点都取自于其他的Markdown的解释器，比如[Markuku][Markuku],[PHP Markuku Extra][PHP]和[Pandoc][Pandoc].然而，**Kramdown**试图提供一种更加确定的严格语法规则，因此它并不一定能和原始的Markdown语法相兼容.话虽如此，但是大多数的Markdown的文档都能被**kramdown**正确的解析.所有**Kramdown**与**Markdown**不一致的地方都会被高亮显示。


## 源文本格式(Source Text Formatting)

**Kramdown**的输入文本可以是任意的编码，比如ASCII,UTF-8或者ISO-8859-1,而输出的文本的将会保持同样的编码方式。

一篇**Kramdown**的文档主要由两种元素组成，分别是**块级元素(block-level)**和**内联元素(span-level)**:

- **块级元素(block-level)**用来定义一篇文档的主要结构，比如哪一部分是段落，哪一部分是列表，哪一部分是引用等。

- **内联元素(span-level)**用来标记一些小的文本部分，比如强调或链接。

**内联元素**只能出现在**块级元素**内部或者是其他**内联元素**的内部[^1]。

在本篇语法说明文档中，你会经常见到"第一列","第一个字符"等说法，这些"第一列"、"第一个字符"都是相对于当前的缩进层级的，因为有些**块级元素**会开辟新的缩进层级(比如:引用).**Kramdown**默认会在文本的第一列开辟一个新的缩进层级。


### 换行(Line Wrapping)

一些轻量级的标记语言在硬换行(hard-wrapped)的环境中不能很好的发挥作用。举例来说，许多邮件程序就是这种情况。因此，**Kramdown**的文档允许像段落和引用这些元素进行硬换行(hard-wrapped)，也就是说能够自动换行[^2]。在有些情况下，这被称为是" 惰性语法(lazy syntax)"。因为文本第一行的缩进或者前缀并不要求连续行。

**块级元素**总是在末尾满足以下条件时才会换行:

- [一个空白行](#blank-lines),一个[EOB标记行](#end-of-block-marker),一个[IAL 块](#inline-attribute-lists)或者是文档的末尾(比如一个[块边界](#block-boundaries) )
- 一个[HTML块](#html-blocks)

整篇**kramdown**的文档除了少数几个块级元素以外，都是能够换行的,这些块级元素*不*支持硬换行:

**[标题](#headers)**

: 通常来说标题不存在问题，因为标题总是独自占一行的。如果标题太长的话，那就需要用户使用html标签了.

**[围栏代码块](#fenecd-code-blocks)**

: 围栏代码块的定界符是不能够硬换行的，而定界符之内的内容也是不能硬换行的,因为这些内容是被当做原生代码[^3]。

**[定义列表](#definition list terms)**

: 每一个定义项(`<dt>`)都会单独占有一行，硬换行会引起开辟新的定义项.而定义(`<dd>`)本身是支持硬换行的。

**[表格](#tables)**

: 因为每一行都代表了表格中的一整行，所以在表格中是无法硬换行的。


需要注意的是，Kramdown并**不**{:.red}建议使用惰性语法来书写Kramdown文档，kramdown提供的灵活性影响了文档的灵活性，因此惰性语法不应该被使用。


### 制表符的使用(Usage of Tabs)

Kramdown假设制表符是设置成四的倍数，这在用制表符进行列表缩进的时候非常重要。另外，制表符只能用在每行的开头位置，制表符之前不能出现任何的空格符，否则渲染结果将是无法预测的.

### 自动和手动转义(Automatic and Manual Escaping)

根据输出的格式，经常有许多字符需要特殊对待。举例来说，当将一个Kramdown文档转换为HTML文档时，需要特别处理<,>和&等符号。为了方便的处理这些特殊的字符，它们会自动根据输出的格式正确地进行转义。

这就意味着，举例来说，你可以直接在Kramdown文档里自由的使用<,>和&,而不需要考虑在HTML实体中使用的情况，当你确实在一个HTML实体或HTML标签中使用这些符号的时候，Kramdown依然会得到正确的结果。

因为Kramdown确实使用了一些符号来标记文本，所以必须有一种方式来对这些字符进行转义，以保持它们原来的意义。Kramdown可以通过一个反斜杠来进行转义，比如你可以这样使用反引号:

下面是一些可以被转义的的符号列表:


{% highlight bash %}
This \`is not a code\` span!
{% endhighlight %}


|----+-----------|
| 测试 | 测试 |
|:-----:|:-------:|
| \  | 反斜杠 |
| .  | 句号 |
| $*$  | 星号 |
| _  | 下划线 |
| +  | 加号|
| - | 减号 |
| =  | 等号 |
| $`$  | 反引号 |
| ()[]{}<>  | 左右括号,方括号,中括号,尖括号 |
| #  | 井号 |
| !  |  感叹号 |
| <<  | left guillemet |
| >>  | right guillemet |
| :  | 冒号 |
| $\|$  | 管道符 |
| "  | 双引号 |
| '  | 单引号 |
| $  | 美元符 |
|----+-----------|

## 块级边界(Block Boundaries)

块级元素会在一些称为**块级边界(Block Boundaries)**的地方开始或结束，一共有两种不同的**块级边界**

- 如果一个块级元素必须在块级边界处开始，那么它的前面必须是一个[一个空白行](#blank-lines),一个[EOB标记行](#end-of-block-marker),一个[IAL 块](#inline-attribute-lists)或者直接由它来开头。

- 如果一个块级元素必须在块级边界出结束，那么它的后面必须是一个[一个空白行](#blank-lines),一个[EOB标记行](#end-of-block-marker),一个[IAL 块](#inline-attribute-lists)或者它自己作为最后一个元素。

# 结构元素(Structural Elements)

所有的结构元素都是块级元素，他们是用来构建文档的结构的。它们可以用来标记文档的段落、引用或列表。

## 空白行(Blank Lines)

任何只包含空白符(空格或制表符)的一行都kramdown看成是空白行,一个或多个连续的空白行都被视作一个空白行。空白行是用来分割块级元素的，在这种情况下，它没有任何语义意义。但是，在一些情况下，空白行确实有语义信息，主要有以下几种情况:

- 当在**标题**中使用时——请查看[标题](#Headers)一节.
- 当在**代码块**中使用时——请查看[代码块](#code-blocks)一节.
- 当在**列表**中使用时——请查看[列表](#lists)一节.
- 当在**数学块**中使用时——请查看[数学块](#math-blocks)
- 当应用于需要开始/结束于[块级边界](#block-boundaries)的元素时

## 段落(Paragraphs)

**段落**是使用最多的**块级元素**.一个或多个连续的文本行都被看成是一个段落.段落的第一行可以缩进三个空格，段落中的其他行可以进行任意数量的缩进，因为段落是支持[换行](#line-wrapping)的。除了在[换行](#line-wrapping)中说明的规则以外，段落会在遇到[定义列表](#definition-list-line)时自动结束。

你可以通过一个或多个空白行来分割不同的段落，需要注意的是，源文件中的分行不一定是输出文件中的分行(因为[惰性语法](#line-wrapping]的原因)。如果你需要一个显示的换行,比如`<br/>`标签,你就必须在行末尾使用两个或多个空格符或者是两个反斜杠。然而，需要注意的是，上一个段落的最后一个文本行的分行会被忽略。段落的前导空格和尾部空格会被自动去掉。

下面是几个段落的例子:


{% highlight bash %}
段落起始于第一列。但是,
......接下去的行可以缩进任意空格或制表符。
...段落在这里继续。

这是另一个段落，和前一个并不相连。但是却\\
拥有一个硬换行
{% endhighlight %}


## 标题(Headers)

kramdown同时支持被称为Setext风格和atx风格的标题，它们能够被混合应用于同一个文档中。

### Setext风格(Setext Style)

Setext风格的标题必须开始于一个[块级边界](#block-boundaries)并且占领一整行的文本，然后紧跟着一行等号(第一级标题)或者一行破折号(第二级标题).标题的文本最多可以缩进三个空格，但是任何的前导和后导空格都会被自动移除。等号和破折号的数量并不重要，事实上只有一个就够了，但是多个符号看起来更加自然。等号或破折号必须开始于第一列。举例:


{% highlight bash %}
第一级标题
=========

第二级标题
---------

   另一个第一级标题
=
{% endhighlight %}

Setext风格的标题开始于块级边界，这就意味着在大多数情况下它的前面必须有一个空白行。然而，Setext风格的标题后面不一定要跟随空白行。

{% highlight bash %}
这是一个正常的段落。

增加一个标题
-----------
一个段落

> 这是一个引用

增加一个标题
-----------
{% endhighlight %}

然而，通常来说最好在Setext风格的标题后面跟随一个空白行，因为这看起来更加自然并且易于阅读。

> **和标准markdown语法不一致的地方:**
>
> 原始的markdown语法允许忽略Setext风格标题前的空白行，但是这会引起歧义并且不易于阅读，因此在kramdown中并不允许这样做。

但是有一种特殊情况需要特别注意:

{% highlight bash %}
标题
----
段落
{% endhighlight %}

也许有人会问这种情况下，这应该是两个被[水平线](#horizontal-rules)分割的段落还是一个二级标签跟着一个段落呢？就像在例子中的说法一样，这种情况下，是被渲染为一个标题和一个段落。常规的规则是，Setext风格的标题通常会比水平线先渲染。


### atx风格的标题

atx风格的标题必须开始于[块级边界](#block-boundaries),并且占领一整行的文本，由一个或多个井号打头，然后紧跟标题的文本。井号之前不允许出现空格，井号的数目指明了标题的层级:一个井号表示一级标题，两个井号表示二级标题，以此类推直至六级标题。你可以在标题文本后面紧跟任意个井号，任何前导和后导空格都会被忽略。


{% highlight bash %}
# 第一级标题

### 三级标题 ###

## 二级标题 #####
{% endhighlight %}

> **和标准markdown语法不一致的地方:**
>
> 同样的，原始的Markdown语法是允许在atx风格的标题前有空行的。

### 指定一个标题ID(Specifying a Header ID)

kramdown支持一种非常漂亮的方式来显示地指定标题的ID，这是从[Markuku][Markuku],[PHP Markuku Extra][PHP]继承而来的: 如果在标题文本之后跟随一个花括号，花括号中包括一个井号和需要指定的ID(花括号与文本之间至少要有一个空格).如果在atx风格的标题后有井号，那么标题ID必须在最后的井号之后。举例

{% highlight bash %}
Hello {#id}
----

# Hello {#id}

# Hello # {#id}
{% endhighlight %}

> **和标准markdown语法不一致的地方:**
>
> 这个新增的特性并不包含在标准的markdown语法中。

## 引用

一个引用开始于一个`>`符号标记，紧跟着一个可选的空格和引用的文本。标记本身可以被缩进3个空格。所有紧跟着的文本行，不论它们是否以引用标记打头都属于引用，因为引用本身是支持[换行](#line-wrapping)的.

引用的内容是属于块级元素，这就意味着引用的文本会被渲染成一个段落。举例来说，下面的例子中一个引用中包含了两个段落:


{% highlight bash %}

> 这是一个引用
>      有着许多行
  这是懒惰的用法
>
> 这是第二个段落

{% endhighlight %}

因为引用中的文本内容是一个块级元素，因此可以进行嵌套使用另一个块级元素(这也是为什么引用支持换行。)


{% highlight bash %}

> 这是一个段落
> 
> > 一个嵌套的引用
>
> ## 标题也支持
>
> - 也支持列表
>
> 以及所有其他的块级标签。

{% endhighlight %}

需要注意的是，引用标记`>`之后的第一个空格在计算块级元素缩进时是不计算在内的。所以[代码块](#code-blocks)必须被缩进5个空格或者一个空格加一个制表符。就像这样:

{% highlight bash %}

> 一个代码块:
>
>     ruby -e 'puts :works'

{% endhighlight %}


[换行](#line-wrapping)允许惰性语法，但是阻挠了可读性，因此应该避免使用,尤其在引用中使用时。以下是几个例子:

{% highlight bash %}
> 这是一个在引用中
的段落
>
> > 这是一个嵌套的段落
在这里继续

> 以及这里
> > 还有这里

{% endhighlight %}

## 代码区块(Code Blocks)

代码区块用来表示一段程序的文本，包括markup,HTML或者其他程序段，因为其中的文本不会被渲染。

### 标准代码区块(Standard Code Blocks)

一个代码区块开始于4个空格或者一个制表符，然后紧跟代码区块的文本。所有紧跟着的文本行，不管是否满足上述的语法要求都属于代码区块，因为代码区块是支持[换行](#line-wrapping).一个渲染之后的代码行会自动跟随在上一行的末尾，原来的换行符会被一个空格代替。每一行的缩进(4个空格或一个制表符)都会自动被删除。

> **和标准markdown语法不一致的地方:**
>
> 原始的Markdown语法在代码区块中是不支持换行的。

需要注意的是，只被空行分割的连续的代码区块会被自动合并成一个代码区块:

{% highlight bash %}

     这是一些代码

     这些文本属于同一个代码区块

{% endhighlight %}

如果你需要一个代码区块紧跟着另一个代码区块，你需要使用一个[EOB标记行](#end-of-block-marker)来分割:

{% highlight bash %}

    这是一些代码
^
    这是另外一个代码区块

{% endhighlight %}

### 围栏代码块(Fenced Code Blocks)

> **和标准markdown语法不一致的地方:**
>
> 这个可选的语法并不是标准的Markdown语法的一部分,这个语法来自于[PHP Markdown Extra][PHP].

Kramdown支持另外一种代码区块的方式，使用分割线而不是缩进。代码区块的开始行必须以3个或多个波浪线符号，而结束行必须和开始行有相同数量的波浪线。这期间的所有文本都被当做其他程序语言的代码，并且不需要进行缩进。举例来说:

{% highlight bash %}

~~~~
这是一些代码区块
~~~~

{% endhighlight %}

如果你需要在代码区块中使用波浪线，你只需要在开始和结束行使用更多的波浪线。


{% highlight bash %}

~~~~~~~~~~~~~~
~~~~
这是一些代码区块
~~~~
~~~~~~~~~~~~~~

{% endhighlight %}


这种类型的代码区块对于需要"复制-黏贴"的代码非常有用，因为它不需要进行代码的缩进。

### 代码区块的语言(Language of Code Blocks)

你可以通过[IAL 块](#inline-attribute-lists)来告诉Kramdown代码区块中的代码所使用的语言。

{% highlight bash %}

~~~
def what?
    42
end
~~~
{. language-ruby}

{% endhighlight %}

这个被指定的类名`language-ruby`告诉Kramdown这段代码是用Ruby的语法写成的。这些信息很有用，比如可以使用一些转换器来对代码区块中的代码进行代码高亮。

围栏代码块提供了一个更加容易的方式来指明程序语言:通过在开始行的末尾添加语言名称。


{% highlight bash %}

~~~ ruby
def what?
    42
end
~~~

{% endhighlight %}


## 列表

Kramdown同时提供创建有序和无序列表的语法元素。

### 有序和无序列表

有序和无需列表都遵循同样的规则。

一个列表开始于一个列表标记符(对于无序列表来说，可以用`+`,`-`,`*`并且你可以混合使用这些标记;对于有序列表来说，是一个数字加一个点号[^4]),紧跟着一个制表符或至少一个空格，然后可选地跟随着一个[IAL](#inline-attribute-lists),以应用于列表项上，最后是列表的第一项内容。为了更好的排版效果，前导的空格和制表符将会被移除。所有跟随在第一行之后的带有相同类型列表标记(有序或无序)的行都会被放置在一个列表中。在有序列表中的数字是无关的，一个有序列表总是从1开始的。

以下的例子说明了有序和无序列表:

{% highlight bash %}
* kram
+ down
- now

1. kram
2. down
3. now
{% endhighlight %}

> **和标准markdown语法不一致的地方:**
>
> 原始的markdown语法是允许有序标记和无序标记混合使用的，第一个标记用来指明标记的类型(有序或者无序)。但是这在kramdown中是不允许的，上述的代码在kramdown中会被渲染成两个列表(一个是有序的，一个是无序的),但在markdown中却会被渲染成一个无序列表。

一个列表的第一个标记可以缩进三个空格，对于列表内容的第一行来说，其列表标记后的第一个非空白字符的列号指明了该列表项后续内容行的缩进。如果没有这样的符号，那就默认是4个空格或者一个制表符。由于[换行](#line-wrapping)的原因，缩进的行后面也许会跟着任意缩进的内容行。需要注意的是，除了在换行那一章说明的规则以外，一个列表项会在遇到另一个列表的列表标记时结束，详细说明请阅读下一个段的说明.

缩进的空格将会从文本内容中删除，并且文本内容(这里的本文内容很自然地包括了每一行的文本内容及其列表标记)会被当成是块级元素来渲染。列表中所有的其他项的列表标记可以被缩进三个空格，或者是最后一项的缩进空格数减一，选择其中较小的一个。举例来说:


{% highlight bash %}

* 这是第一行.因为当前行的第一个非空白字符出现在第三列，所以所有的其他项的内容行必须缩进2个空格。
然而，这里可以使用惰性语法，不必缩进。但是并不推荐这样做。

*       这是列表的另外一个元素，使用了不同的缩进,这是可以的，但应该避免这样使用。
    * 这个列表项标记被缩进了3个空格，这是允许的，但也应该被避免使用。需要注意的是，这个在第二个列表项中的惰性行(lazy line)也许会让你觉得这是一个子列表，但实际却不是，所以应该避免使用这种惰性用法。

{% endhighlight %}

所以，当使用上述的格式的时候，会产生一个列表的三个项目。非常不建议在同一个层级的列表上使用不同的缩进(不管是列表标记还是列表文本),包括惰性用法也不建议使用。比较推荐用如下的方法:


{% highlight bash %}
* 这是第一个列表项目。bla bla bla bla bla bla bla bla bla bla bla bla bla bla bla bla bla bla bla bla bla bla bla bla bla bla bla bla bla bla bla 
* 这是列表的另外一个项目。bla bla bla bla bla bla bla bla bla bla bla bla bla bla bla bla bla bla bla bla bla bla bla bla bla bla bla bla bla bla bla 
{% endhighlight %}

> **和标准markdown语法不一致的地方:**
>
> 原始的Markdown语法也允许你缩进列表标记，然而，这样使用的结果可能是无法预测的。
> 另外，Markdown也认为同样缩进长度的列表行是属于同一个列表的。

当使用




[^1]: 也就是说内联元素是可以嵌套的。
[^2]: 原文是broken across lines，我不太确定理解的对不对。
[^3]: 这里的原文是"since everything between the delimiting is taken as is"，这里我想是作者写错了吧，我是按照自己的理解来翻译的。
[^4]: 英文的句号。

[Markuku]: http://maruku.rubyforge.org/
[PHP]: http://michelf.ca/projects/php-markdown/extra/
[Pandoc]: http://johnmacfarlane.net/pandoc/
