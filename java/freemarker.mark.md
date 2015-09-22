#Freemarker
##指令
###<#if condition>和</#if>

eg:

```
<#if price  ==  0>
	do something
</#if>
<#if price  ==  0>
	do something
<#else>
	do something
</#if>	
	
```

###<#list sequence as loopVariable>repeatThis</#list>

eg:

```<#list whatnot.fruits as fruit>	do something ${fruit}</#list>

[firstindex..lastindex]
```

###<#include "/copyright_footer.html">

###处理不存在的变量

```
<h1>Welcome ${user!"Anonymous"}!</h1>	

<#if user??><h1>Welcome ${user}!</h1></#if>
```
默认值用于序列

```
<#assign seq = ['a', 'b']>${seq[0]!'-'}${seq[1]!'-'}${seq[2]!'-'}${seq[3]!'-'}

a
b
-
-
```

检查不存在的值:

```
unsafe_expr??或(unsafe_expr)??
```

###用户自定义指令

```
<@box title="Attention!">Too much copy-pasting may leads tomaintenance headaches.</@box>
```

###Hash表连接

在+号右侧的哈希表中的项目优先

```
<#assign ages = {"Joe":23, "Fred":25} + {"Joe":30, "Julia":18}>
```

###内建函数
计算结果的整数部分，这可以使用内建函数int来解决

```
${1.01?int}  // 1
```
字符串使用的内建函数：

```
html: 字符串中所有的特殊HTML字符都需要用实体引用来代替（比如<代替&lt;）。cap_first:字符串的第一个字母变为大写形式lower_case:字符串的小写形式upper_case:字符串的大写形式trim:去掉字符串首尾的空格```
序列使用的内建函数：```
size：序列中元素的个数
```
数字使用的内建函数：```
int:数字的整数部分（比如-1.9?int就是-1）
```###方法调用
```
${repeat("What", 3)} //whatwhatwhat
```

###如果你要生成HTML，那么强烈建议你利用它来阻止跨站脚本攻击和非格式良好的HTML页面。这里有一个示例：

```
<#escape x as x?html>...<p>Title: ${book.title}</p><p>Description: 
<#noescape>${book.description}</#noescape></p><h2>Comments:</h2><#list comments as comment><div class="comment">${comment}</div></#list>...</#escape>
```
这个示例展示了当生成HTML时，你最好将完整的模板放入到escape指令中。那么，如果book.title包含&，它就会在输出中被替换成&amp;，而页面还会保持格式良好的HTML。如果用户注释包含如`<iframe>`（或其它的元素）的标记，那么就会被转义成如&lt;iframe&gt;的样子，使他们没有任何有害点。但是有时在数据模型中真的需要HTML，比如我们假设上面的book.description在数据库中的存储是HTML格式的，那么此时你不得不使用noescape来抵消escape的转

###关于给计算机看的数字

```
<a href="/shop/productdetails?id=${product.id?c}">Details...</a>
```
##自定义宏macro

```
<#macro greet><font size="+2">Hello Joe!</font></#macro>
```
```
<@greet></@greet>
```

```
<#macro greet person><font size="+2">Hello ${person}!</font></#macro>
```
```
<@greet person="Fred"/> and <@greet person="Batman"/>
```
```
<#macro greet person color><font size="+2" color="${color}">Hello ${person}!</font></#macro>
```
```
<@greet person="Fred" color="black"/>
```
当需要在macro中需要嵌入Freemaker时

```
<#macro border><table border=4 cellspacing=0 cellpadding=4><tr><td><#nested></td></tr></table></#macro>
```

##宏和循环变量

```
<#macro do_thrice>     <#nested 1>     <#nested 2>     <#nested 3></#macro><@do_thrice ; x> <#-- 用户自定义指令 使用";"代替"as" -->     ${x} Anything.</@do_thrice>
```
##模板中的变量

1.简单变量：它能从模板中的任何位置来访问，或者从使用include指令引入的模板访问。可以使用assign或macro指令来创建或替换这些变量。

```
<#assign x = 1>
```2.局部变量：它们只能被设置在宏定义体内，而且只在宏内可见。一个局部变量的生存周期只是宏的调用过程。可以使用local指令在宏定义体内创建或替换局部变量。

```
<#local x = "local">
```3.循环变量：循环变量是由指令（如list）自动创建的，而且它们只在指令的开始和结束标记内有效。宏的参数是局部变量而不是循环变量。

```
<#list ["loop"] as x>
```
##自定义裤
```
<#import "/lib/my_test.ftl" as my><#-- 被称为"my"的哈希表就会是那个"大门" --><@my.copyright date="1999-2002"/>${my.mail}
```

```
<#import "/lib/my_test.ftl" as my>${my.mail}<#assign mail="jsmith@other.com" in my>${my.mail}
```

```
<#macro copyright date><p>Copyright (C) ${date} ${user}. All rights reserved.</p></#macro><#assign mail = "${user}@acme.com">
```

##输入空白处理
```
<#compress><#assign users = [{"name":"Joe", "hidden":false},{"name":"James Bond", "hidden":true},{"name":"Julia", "hidden":false}]>List of users:<#list users as user><#if !user.hidden>- ${user.name}</#if></#list>That's all.</#compress>
```


##新的语法
````
替换（方括号）语法注意：这个特性从FreeMarker 2.3.4版本后才可用。FreeMarker支持一个替换的语法。就是在FreeMarker的指令和注释中用[和]来代替<<#compress><#assign users = [{"name":"Joe", "hidden":false},{"name":"James Bond", "hidden":true},{"name":"Julia", "hidden":false}]>List of users:<#list users as user><#if !user.hidden>- ${user.name}</#if></#list>That's all.</#compress>List of users:- Joe- JuliaThat's all.List of users: - Joe - Julia That's all.和>，例如下面这个例子：
````

