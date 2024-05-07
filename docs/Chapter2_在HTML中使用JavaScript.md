# 第二章：在HTML中使用JavaScript

##  2.1 \<script\>元素

将JavaScript插入HTML的主要方法是使用 \<script\>元素。

\<script\>元素有下列8个属性：

- **async**: 可选。表示应该立即开始下载脚本，但不阻止其他页面动作。只对外部脚本文件有效。
- **charset**: 可选。使用src属性指定的代码字符集。很少用，因为大部分浏览器不在乎它的值。
- **crossorigin**: 可选。配置相关请求的CORS(跨域资源共享)设置。
- **defer**: 可选。 表示在文档解析和显示完成后再执行脚本是没有问题的。只对外部脚本文件有效。
- **integrity**: 可选。允许比对接收到的资源和指定的加密签名以验证子资源完整性(SRI, Subresource Intergrity)。
- **language**: 废弃。
- **src**: 可选。表示包含要执行的代码的外部文件
- **type**: 可选。 代替language，表示代码块中脚本语言的内容类型(也称MIME类型)。按照惯例，这个值始终都是"text/javascript"；如果这个值是module，则代代码会被当成ES6模块，这时才可以出现import和export关键字。

使用`<script>`的方式有两种：

- 通过它直接在网页中嵌入 JavaScript 代码，
- 在网页中包含外部 JavaScript 文件。

要嵌入行内JavaScript代码，直接把代码放在\<script\>元素中就行：

```html
<script type="text/javascript">
function sayHi(){
    console.log("Hi!");
}
</script>
```

包含在\<script\>内的代码会被从上到下解释。在`<script>`元素中的代码被计算完成之前，页面的其余内容不会被加载，也不会被显示。

注意代码中不能出现字符串\</script\>，下面的代码会导致浏览器报错：

```js
<script>
function sayScript(){
    console.log("</script>");
}
</script>
```

需要使用转义字符串

```html
<script>
function sayScript(){
    console.log("<\/script>");
}
</script>
```

要包含外部文件中的Javascript，就必须使用**src属性**

```html
<script type="text/javascript" src="example.js"></script>
```

与解释行内 JavaScript 一样，在解释外部 JavaScript 文件时，**页面也会阻塞**。（阻塞时间也包含下载文件的时间)

使用了src属性的\<script\>元素不应该再在\<script\>和\</script\>标签中再包含其他JavaScript代码，浏览器会忽略行内代码。

浏览器在解析这样的资源时，会向src属性指定的路径发送一个GET请求。**这个初始的请求不受浏览器同源策略限制**，但返回并执行的JavaScript则受限制。

```JS
<script src="http://www.somewhere.com/afile.js"></script>
```

来自外部域的代码会被当成加载它的页面的一部分来加载和解释。这个能力可以让我们通过不同的域分发 JavaScript。

不管包含的是什么代码，浏览器都会按照`<script>`在页面中出现的顺序依次解释它们，前提是它们没有使用 defer 和 async 属性。第二个`<script>`元素的代码必须在第一个`<script>`元素的代码解释完毕才能开始解释，第三个则必须等第二个解释完，以此类推。

### 2.1.1 标签位置

过去，所有\<script\>元素都被放在\<head\>标签内。这种做法目的是把外部CSS和JavaScript文件都集中到一起。但这意味着必须把所有JavaScript代码都下载、解析和解释完成后，才能开始渲染页面。这会导致页面渲染的明显延迟，在此期间浏览器窗口完全空白。

```html
<!DOCTYPE html> 
<html> 
 <head> 
   <title>Example HTML Page</title> 
   <script src="example1.js"></script> 
   <script src="example2.js"></script> 
 </head> 
 <body> 
   <!-- 这里是页面内容 --> 
 </body> 
</html>
```

**现代Web应用程序通常将所有JavaScript引用放在\<body\>元素中的页面内容后面**。

```html
<!DOCTYPE html> 
<html> 
 <head> 
   <title>Example HTML Page</title> 
 </head> 
 <body> 
   <!-- 这里是页面内容 --> 
   <script src="example1.js"></script> 
   <script src="example2.js"></script> 
 </body> 
</html>
```

### 2.1.2 推迟执行脚本

在\<script\>元素上设置defer属性会告诉浏览器应该**立即开始下载，但执行应该推迟**。脚本会被延迟到整个页面都解析完毕后再运行

```html
<script defer src="example.js"></script>
```

脚本会在浏览器解析到结束的`</html>`标签后才执行。HTML5规范要求脚本应该按照它们出现的顺序执行，因此第一个推迟的脚本会在第二个推迟的脚本之前执行，而且都会在`DOMContentLoaded`事件之前执行。**不过实际当中，不一定保证严格顺序，也不一定保证执行时间**，因此最好只包含一个这样的脚本。

```html
<!DOCTYPE html> 
<html> 
 <head> 
   <title>Example HTML Page</title> 
   <script defer src="example1.js"></script> 
   <script defer src="example2.js"></script> 
 </head> 
 <body> 
   <!-- 这里是页面内容 --> 
 </body> 
</html>
```

如前所述，defer 属性只对外部脚本文件才有效。不是所有的浏览器都完美支持defer属性，还是把要推迟执行的脚本放在页面底部比较好。

### 2.1.3 异步执行脚本

HTML5为\<script\>元素定义了async属性。async 属性只对外部脚本文件才有效，会告诉浏览器应该立即开始下载。从改变脚本处理方式上看，async与defer类似。**但标记为async的脚本并不保证按照出现顺序的次序执行**。

给脚本添加async属性的目的是告诉浏览器，不必等脚本下载和执行完成后再加载页面，同样也不必等到该异步脚本下载和执行后再加载其他脚本。**正因如此，异步脚本不应该在加载期间修改DOM**。

异步脚本保证会在页面的load事件前执行，但可能会在DOMContentLoaded之前或之后。

```html
<!DOCTYPE html> 
<html> 
 <head> 
   <title>Example HTML Page</title> 
   <script async src="example1.js"></script> 
   <script async src="example2.js"></script> 
 </head> 
 <body> 
   <!-- 这里是页面内容 --> 
 </body> 
</html>
```

## 2.2 行内代码与外部文件

虽然可以直接在HTML文件中嵌入JavaScript代码，但通常认为最佳实践是尽可能将JavaScript代码放在外部文件中。理由如下：

- **可维护性**。用一个目录保存所有JavaScript文件更容易维护。开发者可以独立于HTML页面来编辑代码。
- **缓存**。浏览器会根据特定的设置缓存所有外部链接的JavaScript文件。
- **适应未来**。通过把 JavaScript 放到外部文件中，就不必考虑用 XHTML 或前面提到的注释黑科技。包含外部 JavaScript 文件的语法在 HTML 和 XHTML 中是一样的。

## 2.3 文档模式

最初的文档模式有两种：**混杂模式**(quirks mode)和**标准模式**(standards mode)。前者让IE像IE5一样(支持一些非标准的特性)，后者让IE具有兼容标准的行为。

IE初次支持文档模式切换以后，其他浏览器也跟着实现了。随着浏览器的普遍实现，又出现了第三种文档模式：**准标准模式**(almost standards mode)。

准标准模式与标准模式非常接近，很少需要区分。

如果在文档开始处没有发现文档类型声明，则所有浏览器都会默认开启混杂模式。

```html
<!-- HTML 4.01 Strict --> 
<!DOCTYPE HTML PUBLIC "-//W3C//DTD HTML 4.01//EN" 
"http://www.w3.org/TR/html4/strict.dtd"> 

<!-- XHTML 1.0 Strict --> 
<!DOCTYPE html PUBLIC 
"-//W3C//DTD XHTML 1.0 Strict//EN" 
"http://www.w3.org/TR/xhtml1/DTD/xhtml1-strict.dtd"> 

<!-- HTML5 --> 
<!DOCTYPE html>
```

## 2.4 \<noscript\>元素

针对早期浏览器不支持Javascript的问题，需要一个页面优雅降级的处理方案。\<noscript\>出现，被用于给不支持JavaScript的浏览器提供替代内容。

\<noscript\>可以包含任何可以出现在\<body\>中的HTML元素，\<script\>除外。

在下列两种情况下，浏览器将显示包含在\<noscript\>中的内容：

- 浏览器不支持脚本；
- 浏览器对脚本的支持被关闭。

任何一个条件满足，包含在\<noscript\>中的内容就会被渲染。否则就不会元素中的任何内容。

```html
<!DOCTYPE html> 
<html> 
 <head> 
   <title>Example HTML Page</title> 
   <script defer="defer" src="example1.js"></script> 
   <script defer="defer" src="example2.js"></script> 
 </head> 
 <body> 
   <noscript> 
     <p>This page requires a JavaScript-enabled browser.</p> 
   </noscript> 
 </body> 
</html>
```









