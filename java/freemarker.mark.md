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

```

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
<#assign seq = ['a', 'b']>

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
<@box title="Attention!">
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
html: 字符串中所有的特殊HTML字符都需要用实体引用来代替（比如<代替&lt;）。
序列使用的内建函数：
size：序列中元素的个数
```
数字使用的内建函数：
int:数字的整数部分（比如-1.9?int就是-1）
```
```
${repeat("What", 3)} //whatwhatwhat
```

###如果你要生成HTML，那么强烈建议你利用它来阻止跨站脚本攻击和非格式良好的HTML页面。这里有一个示例：

```
<#escape x as x?html>
<#noescape>${book.description}</#noescape></p>
```
这个示例展示了当生成HTML时，你最好将完整的模板放入到escape指令中。那么，如果book.title包含&，它就会在输出中被替换成&amp;，而页面还会保持格式良好的HTML。如果用户注释包含如`<iframe>`（或其它的元素）的标记，那么就会被转义成如&lt;iframe&gt;的样子，使他们没有任何有害点。但是有时在数据模型中真的需要HTML，比如我们假设上面的book.description在数据库中的存储是HTML格式的，那么此时你不得不使用noescape来抵消escape的转

###关于给计算机看的数字

```
<a href="/shop/productdetails?id=${product.id?c}">Details...</a>
```
##自定义宏macro

```
<#macro greet>
```
```
<@greet></@greet>
```

```
<#macro greet person>
```
```
<@greet person="Fred"/> and <@greet person="Batman"/>
```
```
<#macro greet person color>
```
```
<@greet person="Fred" color="black"/>
```
当需要在macro中需要嵌入Freemaker时

```
<#macro border>
```

##宏和循环变量

```
<#macro do_thrice>
```
##模板中的变量

1.简单变量：它能从模板中的任何位置来访问，或者从使用include指令引入的模板访问。可以使用assign或macro指令来创建或替换这些变量。

```
<#assign x = 1>
```

```
<#local x = "local">
```

```
<#list ["loop"] as x>
```
##自定义裤
```
<#import "/lib/my_test.ftl" as my>
```

```
<#import "/lib/my_test.ftl" as my>
```

```
<#macro copyright date>
```

##输入空白处理
```
<#compress>
```


##新的语法
````
替换（方括号）语法
````

