---
title: 空格与 空 格
tags: 
    - 折腾
    - node
    - unicode
    - string
categories:
    - 技术
layout: post
date: 2019-03-27 14:08:00
---

> 相关项目：[kongebot](https://github.com/abusetelegram/kongebot)
> 机器人 [@kongebot](https://t.me/kongebot)

## 剧情前提

`你 打 字 带 空 格`这个恶臭梗应该很多人知道吧（~~不知道的别知道了，我不是厨子，谢谢茄子~~)

然而手动加空格真的麻烦好吧，尤其是在这个方便的聊天工具里面：为什么不做个东西来自动`inline`转换呢？

这个看似简单的东西里面的坑却还不少，就借此机会学习一下

## 字符：我不是那么好惹的

把上面的问题分解一下就是：对于每一个独立的字符个体，我们希望在中间加一个空格字符串，例如`str str`

### 怎么定义独立的字符？

这是我第一个遇到的问题，对于我常用语言来说，汉字和字母都很好说“独立“。然而，emoji却不是这样想的👿

_注：本文不会过多描述编码，直接切入话题。如果不了解相关内容建议阅读一下参考_

参考：
- [https://www.ruanyifeng.com/blog/2007/10/ascii_unicode_and_utf-8.html](https://www.ruanyifeng.com/blog/2007/10/ascii_unicode_and_utf-8.html)
- [https://cenalulu.github.io/linux/character-encoding/](https://cenalulu.github.io/linux/character-encoding/)

简单来说，`UTF-8`是一个变长编码

> 在UTF-8编码中原本只需要一个字节的ASCII字符，仍然只占一个字节。而像中文及日语这样的复杂字符就需要2个到3个字节来存储。

为什么呢？

> 其实原因也比较容易理解：统一字库表的目的是为了能够涵盖世界上所有的字符，但实际使用过程中会发现真正用的上的字符相对整个字库表来说比例非常低。例如中文地区的程序几乎不会需要日语字符，而一些英语国家甚至简单的ASCII字库表就能满足基本需求。而如果把每个字符都用字库表中的序号来存储的话，每个字符就需要3个字节（这里以Unicode字库为例），这样对于原本用仅占一个字符的ASCII编码的英语地区国家显然是一个额外成本（存储体积是原来的三倍）。算的直接一些，同样一块硬盘，用ASCII可以存1500篇文章，而用3字节Unicode序号存储只能存500篇。

然后呢？


那肯定，遍历字符串的时候是希望按照“字符“为单位而不是一个字节为单位。然而，第一版的时候我是这样写的

```javascript
...
str.split('').join(' ')
```

<TelegramEmbed link="ButNothingHappened/3010"/>

这个问题是啥呢？我们来看看[MDN - Split](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/String/split#Syntax)的文档:

> If `separator` is an empty string, `str` is converted to an array having one element for each character of `str`.

_以前上面还没有那个警告，告诉你这样子会导致拆分，现在有了_

这也可以解释当你看到某小说站里面满屏幕的`#数字`：数据库存/去数据的时候估计编码出了点问题

这是一般是当无法解析某个编码时候，会用到的显示：[Replacement Character](https://unicode-table.com/en/FFFD/)


### 解决方案：String Iterator

> 相关实现：[https://github.com/abusetelegram/kongebot/commit/5c0ae75b46d3220cf3a04592241f384c3330da46](https://github.com/abusetelegram/kongebot/commit/5c0ae75b46d3220cf3a04592241f384c3330da46)

上面这个其实是调用了[String Iterator](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/String/@@iterator)，这个的实现就是按照字符分的啦。然后放心的用空格加起来就好了

### 延伸

#### 并不是所有的字符都可以加空格

比如阿拉伯/泰文，他们会有一种整合的机制（也是某些颜文字里面的用法）：多个字符拼在一起会缩短成一个更短的字符，在中间加空格/删掉一个字符只会拆分两个字符，并导致长度变长。

这种就真的是很恶心了，处理不好还会有BUG，比如苹果的白苹果也是这个：

[http://tech.sina.com.cn/mobile/n/n/2016-03-02/doc-ifxpvysv5088693.shtml](http://tech.sina.com.cn/mobile/n/n/2016-03-02/doc-ifxpvysv5088693.shtml)

> 阿拉伯文Bug的问题由来已久，早在iOS 6时代就出现过阿拉伯语（Arabic）字符串会使应用崩溃的问题，这类问题在iOS 7中得到了修复。不过，在去年的iOS 8中再曝出有关于阿拉伯文的神奇漏洞：iPhone在收到一串包含英文、阿拉伯文以、中文以及部分乱码的字符后，就会出现短信功能崩溃，无法打开的情况。

为什么呢？其实是因为折行的问题：比如，通知栏里面一般会把太长的内容用省略号表示，这个时候就要计算了：中文，英文在同一个屏幕下能装的字符肯定不一样长。
计算后你就要拆了：比如说取前30个字符，但是你刚好那个地方是一个合并。导致拆完以后字符串所占的像素反而还超限制不少，直接爆炸

<script>
import TelegramEmbed from 'vue-telegram-embed'

export default {
    components: {
        TelegramEmbed
    }
}
</script>