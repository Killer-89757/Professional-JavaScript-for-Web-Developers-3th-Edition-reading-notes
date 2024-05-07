# 第三章：基本概念

## 3.1 语法

### 3.1.1 区分大小写

ECMAScript中一切区分大小写。

### 3.1.2 标识符

标识符就是变量、函数、属性或函数参数的名称。标识符可以由一或多个下列字符组成：

- 第一个字符必须是一个字母、下划线(_)或美元符号($)；
- 剩下的其他字符可以是字母、下划线、美元符号或数字。

按照惯例，ECMAScript标识符使用**驼峰大小写**形式，即第一个单词的首字母小写，后面每个单词的首字母大写，如：

- firstSecond
- myCar

### 3.1.3 注释

```js
//  单行注释

/*
	多行注释
*/
```

### 3.1.4 严格模式

ES5增加了严格模式(strict mode)的概念。**严格模式是一种不同的JavaScript解析和执行模型**，ES3的一些不规范写法在这种模式下会被处理，对于不安全的活动将抛出错误。

要启动严格模式，有两种方式：

```js
"use strict"  // 全局严格模式
----------------------------------------
function doSomething(){
    "use strict"   // 局部严格模式
    
}
```

所有现代浏览器都支持严格模式。

### 3.1.5 语句

ECMAScript中的语句以分号结尾，**但不是必需的**：

```js
let sum = a + b  // 没有分号也有效,但是不推荐
let diff = a - b;  // 加分号有效
```

- 加分号的好处：
  - 加分号也便于开发者通过删除空行来压缩代码（如果没有结尾的分号，只删除空行，则会导致语法错误）。
  - 加分号也有助于在某些情况下提升性能，因为解析器会尝试在合适的位置补上分号以纠正语法错误。

多条语句可以合并到一个 C 语言风格的代码块中。代码块由一个左花括号（{）标识开始，一个右花括号（}）标识结束：

```js
if (test) { 
  test = false; 
  console.log(test); 
} 
```

if 之类的控制语句**只在执行多条语句**时要求必须有代码块。不过，最佳实践是始终在控制语句中使用代码块，即使要执行的只有一条语句，如下例所示：

```js
// 有效，但容易导致错误，应该避免
if (test) console.log(test); 

// 推荐
if (test) { 
 console.log(test); 
}
```

## 3.2 关键字与保留字

ECMA-262 第 6 版规定的所有关键字如下：

```js
break		do			in					typeof 
case		else		instanceof		 	var 
catch		export		new 				void 
class		extends 	return 				while 
const 		finally 	super 				with 
continue 	for 		switch 				yield 
debugger 	function 	this 
default 	if 			throw 
delete 		import 		try
```

规范中也描述了一组未来的保留字，同样不能用作标识符或属性名。ECMA-262 第 6 版为将来保留的所有词汇

```js
始终保留: 
enum 

严格模式下保留: 
implements 		package 		public 
interface 		protected 		static 
let 			private 

模块代码中保留: 
await
```

这些词汇不能用作标识符，但现在还可以用作对象的属性名。一般来说，最好还是不要使用关键字和保留字作为标识符和属性名，以确保兼容过去和未来的 ECMAScript 版本

## 3.3 变量

ECMAScript是松散类型的，意思是变量可以用于保存任何类型的数据。每个变量不过是一个用于保存在任意值的命名占位符。

有3个关键字可以声明变量：

- var
- let
- const

**var在所有版本中可以使用，但let与const只能在ES6及更晚的版本中使用**。

### 3.3.1 var关键字

```js
var message     // 定义一个名为message的变量

/* ----------------------------------------- */
var message = "hi"      // 定义并赋值，这个变量可以改变保存的值或类型
message = 100  // 合法，但是不推荐
```

#### 1.var声明作用域

使用var操作符定义的变量会成为包含它的函数的局部变量。

```js
function test(){
    var message = "hi"  // 局部变量
}
test()
console.log(message)  // 出错！
```

在函数内定义变量时省略var操作符，可以创建全局变量：

```js
function test(){
    message = "hi" // 全局变量
}
test()
console.log(message) // "hi"
```

不建议这样写

如果需要**定义多个变量**，可以在一条语句中用逗号分隔每个变量（及可选的初始化）：

```js
var message = "hi", found = false, age = 29;
```

#### 2.var声明提升

使用var时，下面代码不会报错。这是因为**使用这个关键字声明的变量会自动提升到函数作用域顶部**：

```js
function foo(){
    console.log(age)
    var age = 26
}
foo() // undefined
```

等价于如下代码：

```js
function foo(){
    var age
    console.log(age)
    age = 26
}
foo() // undefined
```

这就是所谓的“提升” (hoist)， 也就是把所有变量声明都拉到函数作用域的顶部。

反复多次使用var声明同一个变量也没问题：

```js
function foo(){
    var age = 16
    var age = 26
    var age = 36
    console.log(age)
}
foo() // 36
```

### 3.3.2 let声明

let 跟 var的作用差不多，但有重要的区别。最明显的区别是，**let声明的范围是块作用域，而var声明的范围是函数作用域**。

```js
if(true){
    var name = 'Matt'
    console.log(name) // Matt
}
console.log(name) // Matt
```

```js
if(true){
    let age = 26
    console.log(age)  // 26
}
console.log(age) // ReferenceError: age没有定义
```

**let不允许同一个块作用域中出现冗余申明**。这样会导致报错：

```js
var name; 
var name;  // 可以

let age
let age // SyntaxError: 标识符age已经声明过了
```

对声明冗余报错不会因混用 let 和 var 而受影响

```js
var name;
let name; // SyntaxError 

let age; 
var age; // SyntaxError
```

#### 暂时性死区

let 与 var的另一个重要的区别，就是let声明的变量不会在作用域中被提升。

```js
console.log(age) // ReferenceError: age没有定义
let age = 26 
```

在let声明之前的执行瞬间被称为“暂时性死区”(temporal dead zone), 在此阶段引用任何后面才声明的变量都会抛出ReferenceError。

#### 全局声明

与 var 关键字不同，使用 let 在全局作用域中声明的变量不会成为 window 对象的属性（var 声明的变量则会）。

**在使用 var 声明变量时，由于声明会被提升，JavaScript 引擎会自动将多余的声明在作用域顶部合并为一个声明**。

```js
var name = 'Matt'; 
console.log(window.name); // 'Matt' 

let age = 26; 
console.log(window.age); // undefined
```

#### for循环中的let声明

在let出现之前，for循环定义的迭代变量会渗透到循环体外部(var是函数作用域)

```js
for(var i = 0; i < 5; i ++){
    // 循环逻辑
}
console.log(i)  // 5
```

改成let 后，这个问题就消失了，因为let作用域仅限于循环块内部

```js
for(var i = 0; i < 5; i ++){
    // 循环逻辑
}
console.log(i)  // ReferenceError: i 没有定义
```



```js
for (var i = 0; i < 5; ++i) { 
  setTimeout(() => console.log(i), 0) 
} 
// 你可能以为会输出 0、1、2、3、4 
// 实际上会输出 5、5、5、5、5

// 原理：之所以会这样，是因为在退出循环时，迭代变量保存的是导致循环退出的值：5。在之后执行超时逻辑时，所有的 i 都是同一个变量，因而输出的都是同一个最终值。
-----------------------------

for (let i = 0; i < 5; ++i) { 
  setTimeout(() => console.log(i), 0) 
} 
// 会输出 0、1、2、3、4

//原理：而在使用 let 声明迭代变量时，JavaScript 引擎在后台会为每个迭代循环声明一个新的迭代变量。每个 setTimeout 引用的都是不同的变量实例，所以 console.log 输出的是我们期望的值，也就是循环执行过程中每个迭代变量的值。
```

### 3.3.3 const声明

更喜欢称之为：**常量**

const 的行为与let基本相同，唯一一个重要的区别是用它声明变量时必须同时初始化变量，且尝试修改const声明的变量会导致运行时错误。

```js
const age = 26
age = 36 // TypeError: 给常量赋值
```

不允许重复声明

```js
const name = 'Matt'; 
const name = 'Nicholas'; // SyntaxError
```

const 声明的限制只适用于它指向的变量的引用。换句话说，如果const变量引用的是一个对象，那么**修改这个对象内部的属性并不违反const的限制**。

```js
const person = {}
person.name = 'Matt' // ok
```

```js
// 不能将其应用在for循环循环变量中
for (const i = 0; i < 10; ++i) {} // TypeError：给常量赋值
```

### 3.3.4 声明风格及最佳实践

行为怪异的 var 所造成的各种问题，已经让 JavaScript 社区为之苦恼了很多年。

1. **不使用var**

2. **const优先， let次之**

使用const可以让浏览器运行时强制保持变量不变，也可以让静态代码分析工具提前发现不合法的赋值操作。只有再提前知道未来会有修改操作时，才使用let。

## 3.4 数据类型

ECMAScript有7种简单数据类型(也称为**原始类型**):

- Undefined
- Null
- Boolean
- Number
- String
- Symbol  (ECMAScript 6 新增的)
- BigInt（原书没有写这个，因为只介绍到了ES2019，BigInt是ES2020新增的原始类型）

还有一种复杂数据类型叫Object。Object是一种无序名值对的集合。

### 3.4.1 typeof 操作符

对一个值使用typeof操作符会返回下列字符串之一：

- “undefined”表示值未定义
- "boolean"表示值为布尔值
- “string”表示值为字符串
- “number”表示值为数值
- “object”表示值为对象(而不是函数)或null
- “function"表示值为函数
- "symbol"表示值为符号
- “bigint”表示值为大整数（原书没有提到）

**注意typeof 是一个操作符而不是函数，所以不需要参数**（但可以使用参数）。

例子：

```js
let message = "some string"
console.log(typeof message)  // "string"
console.log(typeof(message)) // "string"
console.log(typeof 95)       // "number"
```

typeof null会返回“object”。这是因为特殊值null被认为是一个对空对象的引用。

对正则表达式调用typeof操作符，会返回object

### 3.4.2 Undefind 类型

Undefind类型只有一个值，就是特殊值undefined。当使用var或let声明了变量但没有初始化时，相当于给变量赋予了undefind值。

```js
let message
console.log(message == undefind) // true
```

在对未初始化的变量调用 typeof 时，返回的结果是"undefined"，但对未声明的变量调用它时，返回的结果还是"undefined"，这就有点让人看不懂了。

```js
let message; // 这个变量被声明了，只是值为 undefined 

// 确保没有声明过这个变量
// let age 

console.log(typeof message); // "undefined" 
console.log(typeof age); // "undefined"
```

undefind是一个假值(False)

```js
let message; // 这个变量被声明了，只是值为 undefined 
// age 没有声明 

if (message) { 
 // 这个块不会执行
} 

if (!message) { 
 // 这个块会执行
} 

if (age) { 
 // 这里会报错
}
```

### 3.4.3 Null 类型

Null类型同样只有一个值，即特殊值null。逻辑上讲，null值表示一个空对象指针。这是typeof 传一个 null 会返回"object"的原因

```js
let car = null; 
console.log(typeof car); // "object"
```

在定义将来要保存对象变量时，建议使用null来初始化，不要使用其他值。

```js
if(car != null){
    // car是一个对象的引用
}
```

undefind值是有null值派生而来的，他们表面上相等

```js
console.log(null == undefind)   //true
```

永远不必显式地将变量值设置为 undefined。但 null 不是这样的。任何时候，只要变量要保存对象，而当时又没有那个对象可保存，就要用 null 来填充该变量。

null同样是一个假值

```js
let message = null; 
let age;

if (message) { 
 // 这个块不会执行
} 
if (!message) { 
 // 这个块会执行  null
}

if (age) { 
 // 这个块不会执行 undefined
} 
if (!age) { 
 // 这个块会执行
}
```

### 3.4.4 Boolean类型				

Boolean（布尔值）类型是ECMAScript中使用最频繁的类型之一，有两个字面量值：true和false。

这两个布尔值不同于数值，因此 true 不等于 1，false 不等于 0。

要将其他值转换为布尔值，可以调用特定的Boolean()转型函数：

```js
let message = "Hello world!" 
let messageAsBoolean = Boolean(message)  // true
```

什么值能转换为true或false取决于数据类型和实际的值。下表总结了不同类型与布尔值之间的转换规则。

| 数据类型 |     转换为true的值     |   转换为false的值    |
| :------: | :--------------------: | :------------------: |
| Boolean  |          true          |        false         |
|  String  |       非空字符串       |   “” （空字符串）    |
|  Number  | 非零数值（包括无穷值） | 0、NaN（后面会介绍） |
|  Object  |        任意对象        |         null         |
| Undefind |     N/A（不存在）      |       undefind       |

因为像 if 等流控制语句会自动执行其他类型值到布尔值的转换

```js
let message = "Hello world!"; 
if (message) { 
  console.log("Value is true"); 
} 
```

### 3.4.5 Number 类型

Number类型使用IEEE 754格式表示整数和浮点值（在某些语言中也叫双精度值）。

最基本的数字字面量格式是十进制：

```js
let intNum = 55   // 整数
```

可以用八进制表示，第一个数字必须为0，然后是相应的八进制数字（0 ~ 7）。如果字面量中包含的数字超过了有效范围，则忽略前缀的零，后面的数字序列会被当成十进制：

```js
let octalNum1 = 070 // 八进制的56
let octalNum2 = 079 // 无效的八进制，当成79处理
```

**八进制字面量在严格模式无效，必须使用0o前缀**

用十六进制表示，必须让真正的数值前缀0x（区分大小写）：

```js
let hexNum1 = 0xA  // 十六进制10
let hexNum2 = 0x1f // 十六进制31
```

#### 1.浮点值

定义浮点值，必须包含小数点：

```js
let floatNum1 = 1.1
let floatNum2 = 0.1
let floatNum3 = .1  // 有效但不推荐
```

因为存储浮点值使用的内存空间是存储整数值的两倍，ECMAScript总是想方设法把值转换为整数，如下两种情况：

```js
let floatNum1 = 1.   // 小数点后面没有数字，当成整数1处理
let floatNum2 = 10.0 // 小数点后面是零，当成整数10处理
```

浮点值的精确度最高可达17位小数，但在算数计算中远不如整数精确。例如0.1加0.2的值不是0.3而是0.30000000000000004。

**永远不要测试某个特定的浮点值**

```js
if (a + b == 0.3) { // 别这么干！ 
  console.log("You got 0.3."); 
}
```

对于非常大或非常小的数值，浮点值可以用科学记数法来表示。

科学记数法用于表示一个应该乘以10 的给定次幂的数值。ECMAScript 中科学记数法的格式要求是一个数值（整数或浮点数）后跟一个大写或小写的字母 e，再加上一个要乘的 10 的多少次幂。默认情况下，ECMAScript 会将小数点后至少包含 6 个零的浮点值转换为科学记数法

```js
let floatNum = 3.125e7; // 等于 31250000
let floatNum = 3e-17; // 等于 0.000 000 000 000 000 03
```

#### 2.值的范围

ECMAScript可以表示的最小数值保存在**Number.MIN_VALUE**中，这个值在多数浏览器中是 5e324。

最大数值保存在**Number.MAX_VALUE**中，这个值在多数浏览器中是 1.797 693 134 862 315 7e+308。

如果超出了范围，数值会被自动转换为`Infinity`/`-Infinity`（无穷）值。

可以使用isFinite()函数判断是否是一个有穷值

```js
let result = Number.MAX_VALUE + Number.MAX_VALUE; 
console.log(isFinite(result)); // false
```

正负Infinity不能再进一步用于任何计算

使用 `Number.NEGATIVE_INFINITY` 和 `Number.POSITIVE_INFINITY` 也可以获取正、负 Infinity。没错，这两个属性包含的值分别就是`-Infinity` 和 `Infinity`。

#### 3.NaN

有一个特殊的值叫NaN，意思是“不是数值”（Not a Number），用于表示本来要返回数值的操作失败了。

用 0 除任意数值在其他语言中通常都会导致错误，从而中止代码执

行。但在 ECMAScript 中，0、+0 或 -0 相除会返回 NaN：

```js
console.log(0/0); // NaN 
console.log(-0/+0); // NaN
```

如果分子是非 0 值，分母是有符号 0 或无符号 0，则会返回 Infinity 或-Infinity：

```JS
console.log(5/0); // Infinity 
console.log(5/-0); // -Infinity 
```

NaN有几个独特的属性，首先，

- 任何涉及NaN的操作始终返回NaN。

```js
console.log(NaN/10) // NaN
```

- NaN不等于包括NaN在内的任何值：

```js
console.log(NaN == NaN) // false
```

ECMAScript提供了isNaN()函数。**该函数会尝试把参数转换为数值**，任何不能转换为数值的值都会导致这个函数返回true：

```js
console.log(isNaN(NaN))    // true
console.log(isNaN(10))     // false, 10是数值
console.log(isNaN("10"))   // false, 可以转换成数值10
console.log(isNaN("blue")) // true, 不可以转换成数值
console.log(isNaN(true))   // false, 可以转换成数值1
```

> 虽然不常见，但 isNaN()可以用于**测试对象**。此时，首先会调用对象的 `valueOf()`方法，然后再确定返回的值是否可以转换为数值。如果不能，再调用 `toString()`方法，并测试其返回值。

#### 4.数值转换

有3个函数可以将非数值转换为数值：**Number()**、**parseInt()**、**parseFloat()**

Number()函数基于如下规则执行转换。

- 布尔值，true转换为1，false转换为0
- 数值，直接返回
- null，返回0
- undefind，返回NaN
- 字符串，应用如下规则:
  - 如果字符串包含数值字符，包括数值字符前面带加减号的情况，则转换为一个**十进制数值**。
  - 如果字符串包含有效的浮点值格式如 “1.1” ，则会转换为相应的浮点值（同样，忽略前面的零）。
  - 如果字符串包含有效的十六进制格式如 “0xf” ,则会转换为与该十六进制值对应的十进制整数值。
  - 如果是空字符串（不包含字符），则返回0。
  - 如果字符串包含上述情况之外的其他字符，则返回NaN。
- 对象，调用valueOf()方法，并按照上述规则转换返回的值。如果转换结果是NaN，则调用toString()方法，再按照转换字符串的规则转换。

下面是几个具体的例子：

```js
let num1 = Numer("Hello world") // NaN
let num2 = Numer("")            // 0
let num3 = Numer("000011")      // 11
let num4 = Numer(true)          // 1
```

`parseInt()`和`parseFloat()`函数主要用于将字符串转换为数值

**通常在需要得到整数时可以优先使用parseInt()函数**。

字符串最前面的空格会被忽略，从第一个非空格字符开始转换。如果第一个字符不是数值字符、加号或减号，parseInt()立即返回NaN。**这意味着空字符串也会返回NaN**（与Number()不一样，空字符串会返回0）。

示例：

```js
var num1 = parseInt("1234blue");    //1234
var num2 = parseInt("");            //NaN
var num3 = parseInt("0xA");         //10 - hexadecimal
var num4 = parseInt(22.5);          //22
var num5 = parseInt("70");          //70 - decimal
var num7 = parseInt("070");          //70 - 不是8进制转换了
var num6 = parseInt("0xf");         //15 - hexadecimal
```

> parseInt()解析像八进制字面量字符串，ESMAScript 3 和 5存在分歧
>
> - var num7 = parseInt("070");    // ESMAScript 3 认为是八进制 56 ；ESMAScript 5 认为是十进制 70

**parseInt()也接受第二个参数，用于指定底数（进制数）**。

```js
let num = parseInt("0xAF", 16) // 175
let num1 = parseInt("AF", 16)  // 175， 可以省掉0x前缀
let num2 = parseInt("AF")      // NaN
```

parseFloat()函数的工作方式与parseInt()类似。但是当parseInt()遇到第二个小数点时，剩余字符都会被忽略，因此，“22.34.5”将被转换为22.34

**parseFloat()始终忽略字符串开头的零**。

parseFloat()只解析十进制值。

如果字符串表示整数（没有小数点或者小数点后面只有零（原书说只有一个零，我测试后是多个零都可以）），则parseFloat()返回整数。

示例：

```js
let num1 = parseFloat("1234blue")  // 1234，按整数解析
let num2 = parseFloat("0xA")       // 0
let num3 = parseFloat("22.5")      // 22.5
let num4 = parseFloat("22.34.5")   // 22.34
let num5 = parseFloat("0908.5")    // 908.5
let num6 = parseFloat("3.125e7")   // 31250000
```

### 3.4.6 String 类型

String（字符串）数据类型表示零或多个16位Unicode字符序列。字符串可以使用双引号（"）、单引号（'）或反引号（`）标示。

```js
let firstName = "John"
let lastName = 'Jacob'
let lastName2 = `Dead By Daylight`
let wrong = 'error"   // 这是错误的，必须以同种引号作为字符串结尾。
```

#### 1. 字符字面量

字符串数据类型包含一些字符字面量，用于表示非打印字符或其他用途的字符，如下表所示：

| 字面量 | 含义                                |
| :----- | :---------------------------------- |
| \n     | 换行                                |
| \t     | 制表                                |
| \b     | 退格                                |
| \r     | 回车                                |
| \f     | 换页                                |
| \\\    | 反斜杠（\）                         |
| \\'    | 单引号（'）                         |
| \\"    | 双引号（"）                         |
| \\`    | 反引号（`）                         |
| \xnn   | 以十六进制编码nn表示的字符          |
| \unnnn | 以十六进制编码nnnn表示的Unicode字符 |

#### 2. 字符串的特点

**ECMAScript中的字符串是不可变的（immutable），意思是一旦创建，它们的值就不能变了。要修改某个变量中的字符串值，必须先销毁原始的字符串，然后将包含新值的另一个字符串保存到该变量。**

变量 lang 一开始包含字符串"Java"。紧接着，lang 被重新定义为包含"Java"和"Script"的组合，也就是"JavaScript"。整个过程首先会分配一个足够容纳 10 个字符的空间，然后填充上"Java"和"Script"。最后销毁原始的字符串"Java"和字符串"Script"，因为这两个字符串都没有用了。所有处理都是在后台发生的，而这也是一些早期的浏览器（如 Firefox 1.0 之前的版本和 IE6.0）在**拼接字符串时非常慢**的原因。这些浏览器在后来的版本中都有针对性地解决了这个问题。

#### 3. 转换为字符串

**几乎所有值都有toString()方法**。这个方法唯一的用途就是返回当前值的字符串等价物。比如：

```js
let age = 11
let ageAsString = age.toString()     // 字符串“11”
let found = true
let foundAsString = found.toString() // 字符串“true”
```

toString()方法可见于数值、布尔值、对象和字符串值。null和undefind值没有toString()方法。

在对数值调用这个方法时，toString()可以接收一个底数参数，即以什么底数来输出数值的字符串表示。

```js
let num = 10
console.log(num.toString())     // "10"
console.log(num.toString(2))    // "1010"
console.log(num.toString(8))    // "12"
console.log(num.toString(10))   // "10"
console.log(num.toString(16))   // "a"
```

注意，默认情况下（不传参数）的输出与传入参数 10 得到的结果相同。

如果你不确定一个值是不是 null 或 undefined，可以使用 `String()`转型函数，它始终会返回表示相应类型值的字符串。String()函数遵循如下规则。

- 如果值有 toString()方法，则调用该方法（不传参数）并返回结果。

- 如果值是 null，返回"null"。

- 如果值是 undefined，返回"undefined"。

下面看几个例子：

```js
let value1 = 10; 
let value2 = true; 
let value3 = null; 
let value4; 

console.log(String(value1)); // "10" 
console.log(String(value2)); // "true" 
console.log(String(value3)); // "null" 
console.log(String(value4)); // "undefined" 
```

#### 4. 模板字面量

`ES6新增`模板字面量可以跨行定义字符串：

```js
let myNultiLineTemplateLiteral = `fisrt line
second line`
console.log(myNultiLineTemplateLiteral)
// first line
// second line
```

顾名思义，模板字面量在定义模板时特别有用，比如下面这个HTML模板：

```js
let pageHTML = `
<div>
  <a href="#">
	<span>Jake</span>
  </a>
</div>`
```

**模板字面量会保持反引号内部的空格**，因此在使用时要格外注意。格式正确的模板字符串看起来可能会缩进不当。

```js
// 这个模板字面量在换行符之后有25个空格符
let myTemplateLiteral = `first line
						 second line`
console.log(myTemplateLiteral.length)    // 47

let secondTemplateLiteral = `
first line
second line`
console.log(secondTemplateLiteral[0] === '\n')  // true
```

#### 5. 字符串插值

模板字面量最常用的一个特性是支持字符串插值，也就是可以在一个连续定义中插入一个或多个值。

模板字面量不是字符串，而是一种特殊的 JavaScript 句法表达式，只不过求值后得到的是字符串。

字符串插值通过在${}中使用一个JavaScript表达式实现：

```js
let value = 5
let exponent = 'second'
let interpolatedTemplateLiteral = `${ value } to thr ${ exponent } power is ${ value * value }`
console.log(interpolatedTemplateLiteral) // 5 to the second power is 25
```

**所有插入的值都会使用toString()强制转型为字符串**。任何JavaScript表达式都可以用于插值。

嵌套的模板字符串无须转义：

```js
// 模板也可以插入自己之前的值
let value = ''; 
function append() { 
 value = `${value}abc` 
 console.log(value); 
} 
append(); // abc 
append(); // abcabc 
append(); // abcabcabc
```

#### 6. 模板字面量标签函数

模板字面量也支持定义**标签函数（tag function）**，而通过标签函数可以自定义插值行为。

标签函数会接收被插值记号分隔后的模板和对每个表达式求值的结果。

通过一个例子来理解：

```js
let a = 6
let b = 9

function simpleTag(strings, aValExpression, bValExpression, sumExpression){
    console.log(strings)
    console.log(aValExpression)
    console.log(bValExpression)
    console.log(sumExpression)
    return 'foobar'
}

let untaggedResult = `${ a } + ${ b } = ${ a + b }`
let taggedRsult = simpleTag`${ a } + ${ b } = ${ a + b }`
// ["", " + ", " = ", ""]
// 6
// 9
// 15

console.log(untaggedResult) // "6 + 9 = 15"
console.log(taggedRsult)    // "foobar"
```

#### 7. 原始字符串

使用模板字面量也可直接获取原始的模板字面量内容（如换行符或Unicode字符），而不是被转换后的字符表示。

为此，可以使用默认的**String.raw**标签函数：

```js
console.log(`\u00A9`)  			 //	©
console.log(String.raw`\u00A9`)  // \u00A9
```

另外，也可以通过标签函数的第一个参数，即字符串数组的.raw属性取得每个字符串的原始内容：

```js
function printRaw(strings){
    console.log('Actual characters:')
    for(const string of strings){
        console.log(string)
    }
    
    console.log('Escapled characters:')
    for(const rawString of strings.raw){
        console.log(rawString)
    }
}

printRaw`\u00A9${'and'}\n`
// Actual characters:
// ©
// (换行符)
// Escaped characters:
// \u00A9
// \n
```

### 3.4.7 Symbol类型

Symbol（符号）是 ECMAScript 6 新增的数据类型

**符号（Symbol）是原始值，且符号实例是唯一、不可变的。**符号的用途是**确保对象属性使用唯一标识符，不会发生属性冲突的危险**。

符号就是用来创建唯一记号，进而用作非字符串形式的对象属性。

#### 1. 符号的基本用法

符号需要使用Symbol()函数初始化。

```js
let sym = Symbol()
console.log(typeof sym) // symbol
```

调用Symbol函数时，也可以传入一个字符串参数作为对符号的描述（description）。但是，这个字符串参数与符号定义或标识完全无关：

```js
let genericSymbol = Symbol()
let otherGenericSymbol = Symbol()

console.log(genericSymbol == otherGenericSymbol)  // false

let fooSymbol = Symbol('foo')
let otherFooSymbol = Symbol('foo')

console.log(fooSymbol == otherFooSymbol)  // false
```

#### 2. 使用全局符号注册表

如果运行时的不同部分需要共享和重用符号实例，那么可以用一个字符串作为键，在全局符号注册表中创建并重用符号。

为此，需要使用Symbol.for()方法。**这个方法对每个字符串键都执行幂等操作**。第一次使用某个字符串调用时，它会检查全局运行时注册表，发现不存在对应的符号，于是会生成一个新的符号实例并添加至注册表中。后续使用相同字符串的调用会同样检查注册表，发现存在与该字符串对应的符号，就会返回该符号实例。

```js
let fooGlobalSymbol = Symbol.for('foo')
let otherFooGlobalSymbol = Symbol.for('foo')

console.log(fooGlobalSymbol == otherFooGlobalSymbol)  // true
```

**全局注册表中的符号必须使用字符串键来创建，因此作为参数传给Symbol.for()的任何值都会被转换为字符串**。

还可以使用Symbol.keyFor()来查询全局注册表，这个方法接收符号，返回该全局符号对应的字符串键，如果查询的不是全局符号，则返回undefind。

```js
// 创建全局符号
let s = Symbol.for('foo')
console.log(Symbol.keyFor(s))  // foo

// 创建普通符号
let s2 = Symbol.for('bar')
console.log(Symbol.keyFor(s2)) // undefind

Symbol.keyFor(123)  // TypeErrr: 123 is not a symbol
```

#### 3. 使用符号作为属性

凡是可以使用字符串或数值作为属性的地方，都可以使用符号。

```js
let s1 = Symbol('foo')
let s2 = Symbol('bar')

let o = {
    [s1]: 'foo val'
}
// 这样也可以： o[s1] = 'foo val'

Object.defineProperty(0, s2, {value: 'bar val'})
```

#### 4. 常用内置符号

ECMAScript6也引入了一批常用内置符号（well-known symbol），用于暴露语言内部行为，开发者可以直接访问、重写或模拟这些行为。

这些内置符号最重要的用途之一是重新定义它们，从而改变原生结构的行为。比如，我们知道for-of循环会在相关对象上使用Symbol.iterator属性，那么就可以通过在自定义对象上重新定义Symbol.iterator的值来改变for-of在迭代该对象时的行为。

这些内置符号只是全局函数Symbol的普通字符串属性，指向一个符号的实例。所有这些内置符号属性都是不可写、不可枚举、不可配置的。

> 在提到ECMAScript规范时，经常会引用符号在规范中的名称，前缀为@@。比如，@@iterator指的就是Symbol.iterator。

**关于内置符号，我推荐大家看一看阮一峰老师在[《ECMAScript 6 入门》](https://es6.ruanyifeng.com/#docs/symbol#%E5%86%85%E7%BD%AE%E7%9A%84-Symbol-%E5%80%BC)中的讲解**。

### 3.4.8 Object 类型

ECMAScript中的对象其实就是**一组数据和功能的集合**。对象通过`new`操作符后跟对象类型的名称来创建。开发者可以通过创建Object类型的实例来创建自己的对象，然后再给对象添加属性和方法。

```js
let o = new Object();

// ECMAScript 只要求在给构造函数提供参数时使用括号。如果没有参数,可以不用写括号
let o = new Object; // 合法，但不推荐
```

Object实例本身并不是很有用，但理解与它相关的概念非常重要。类似Java中的Java.lang.Object，ECMAscript中的Object也是派生其他对象的基类。Object类型的所有属性和方法在派生的对象上同样存在。

每个Object实例都有如下属性和方法。

- **constructor**: 用于创建当前对象的函数。在前面的例子中，这个属性的值就是Object()函数。
- **hasOwnProperty(propertyName)**: 用于判断当前对象实例（不是原型）上是否存在给点的属性。要检查的属性名必须是字符串（如o.hasOwnProperty("name")）或符号。
- **isPrototypeOf(object)**: 用于判断当前对象是否为另一个对象的原型。
- **propertyIsEnumerable(protertyName)**: 用于判断给点的属性是否可以使用for-in语句枚举。 与hasOwnProperty()一样，属性名必须是字符串。
- **toLocaleString()**: 返回对象的字符串表示。该字符串反映对象所在的本地化执行环境。
- **toString()**: 返回对象的字符串表示。
- **valueOf()**: 返回对象对应的字符串、数值或布尔值表示。通常与toString()的返回值相同。

## 3.5 操作符

一组可用于**操作数据值的操作符**，包括数学操作符（如加、减）、位操作符、关系操作符和相等操作符等。ECMAScript 中的操作符是独特的，因为它们可用于各种值，包括字符串、数值、布尔值，甚至还有对象。在应用给对象时，操作符通常会调用 `valueOf()`和/或` toString()`方法来取得可以计算的值

### 3.5.1 一元操作符

只操作一个值的操作符叫**一元操作符**（unary operator）。

#### 1. 递增/递减操作符

递增和递减操作符直接照搬自C语言，有两个版本：前缀版和后缀版。

前缀操作，变量的值都会在语句被求值前改变。

```js
let age = 29
++age   // 等于 age = age + 1

let age = 29
let anotherAge = --age + 2

console.log(age)         // 28
console.log(anotherAge)  // 30
```

后缀操作与前缀操作的主要区别在于，后缀版递增和递减在语句被求值后才发生。。

```js
let num1 = 2
let num2 = 20
let num3 = num1-- + num2
let num4 = num1 + num2

console.log(num3)      // 22
console.log(num4)      // 21
```

这4个操作符可以作用于任何值，意思是不限于整数，字符串、布尔值、浮点值，甚至对象都可以。递增和递减操作符遵循如下规则。

- 对于字符串，如果是有效的数值形式，则转换为数值再应用改变。变量类型从字符串变为数值。
- 对于字符串，如果不是有效的数值形式，则将变量的值设置为NaN。变量类型从字符串变为数值。
- 对于布尔值，如果是false则转换为0再应用改变。变量类型从布尔值变为数值。
- 对于布尔值，如果是true则转换为1再应用改变。变量类型从布尔值变为数值。
- 对于浮点值，加1或减1。
- 如果是对象，则调用valueOf()方法取得可以操作的值。对得到的值应用上述规则。如果是NaN，则调用toString()并再次应用其他规则。变量类型从对象变为数值。

```js
let s1 = "2"; 
let s2 = "z"; 
let b = false; 
let f = 1.1; 
let o = { 
  valueOf() { 
    return -1; 
  } 
}; 

s1++; // 值变成数值 3 
s2++; // 值变成 NaN 
b++; // 值变成数值 1 
f--; // 值变成 0.10000000000000009（因为浮点数不精确）
o--; // 值变成-2
```

#### 2. 一元加和减

一元加由一个加号（+）表示。对数值没有任何影响。

一元减由一个减号（-）表示，放在变量前头，主要用于把数值变成负值，如把 1 转换为-1。

如果将一元操作符应用到非数值，则会执行与使用Number()转型函数一样的类型转换。

```js
let s1 = '01'
let s2 = '1.1'
let s3 = 'z'
let b = false
let f = 1.1
let o = {
    valueOf(){
        return -1
    }
}

s1 = +s1        // 值变成数值1
s2 = +s2        // 值变成数值1.1
s3 = +s3        // 值变成NaN
b = +b	        // 值变成数值0
f = +f          // 不变，还是1.1
o = +o          // 值变成数值-1 
```

```js
let s1 = "01"; 
let s2 = "1.1"; 
let s3 = "z"; 
let b = false; 
let f = 1.1; 
let o = { 
  valueOf() { 
    return -1; 
  } 
};

s1 = -s1; // 值变成数值-1 
s2 = -s2; // 值变成数值-1.1 
s3 = -s3; // 值变成 NaN 
b = -b; // 值变成数值 0 
f = -f; // 变成-1.1 
o = -o; // 值变成数值 1
```

### 3.5.2 位操作符

​	位操作符用于数值的底层操作，也就是操作内存中表示数据的比特（位）。ECMAScript中的所有数值都以 IEEE 754 64 位格式存储，但位操作并不直接应用到 64 位表示，而是先把值转换为32 位整数，再进行位操作，之后再把结果转换为 64 位。

​	有符号整数使用 32 位的前 31 位表示整数值。第 32 位表示数值的符号，如 0 表示正，1 表示负。这一位称为符号位（sign bit），它的值决定了数值其余部分的格式。数值18的二进制格式为00000000000000000000000000010010，或更精简的 10010

![image-20240507151632939](https://cdn.jsdelivr.net/gh/Killer-89757/PicBed/images/2024%2F05%2Fimage-20240507151632939-91f9cd.png)

负值以一种称为二补数（或补码）的二进制编码存储

- 确定绝对值的二进制表示（如，对于-18，先确定 18 的二进制表示）；

- 找到数值的一补数（或反码），换句话说，就是每个 0 都变成 1，每个 1 都变成 0；

- 给结果加 1。

特殊值 `NaN` 和 `Infinity` 在位操作中都会被当成 0 处理。

如果将位操作符应用到非数值，那么首先会使用 Number()函数将该值转换为数值（这个过程是自动的），然后再应用位操作。最终结果是数值。

#### 1. 按位非

按位非操作符用波浪符（~）表示，它的作用是返回数值的一补数。

```js
let num1 = 25				 // 二进制00000000000000000000000000011001
let num2 = ~num1             // 二进制11111111111111111111111111100110
console.log(num2)            // -26
```

**按位非的最终效果是对数值取反并减1**。

实际上，尽管两者返回的结果一样，但位操作的速度快得多。这是因为位操作是在数值的底层表示上完成的。

#### 2. 按位与

按位与操作符用和号（＆）表示。

![image-20240507152246486](https://cdn.jsdelivr.net/gh/Killer-89757/PicBed/images/2024%2F05%2Fimage-20240507152246486-632a1e.png)

```js
let result = 25 & 3; 
console.log(result); // 1
```

![image-20240507152321098](https://cdn.jsdelivr.net/gh/Killer-89757/PicBed/images/2024%2F05%2Fimage-20240507152321098-2d6d67.png)

#### 3. 按位或

按位或操作符用管道符（|）表示。

![image-20240507152349263](https://cdn.jsdelivr.net/gh/Killer-89757/PicBed/images/2024%2F05%2Fimage-20240507152349263-edd706.png)

```js
let result = 25 | 3; 
console.log(result); // 27
```

![image-20240507152412482](https://cdn.jsdelivr.net/gh/Killer-89757/PicBed/images/2024%2F05%2Fimage-20240507152412482-76a48f.png)

#### 4.按位异或

按位异或操作符用脱字符（^）表示。

![image-20240507152422904](https://cdn.jsdelivr.net/gh/Killer-89757/PicBed/images/2024%2F05%2Fimage-20240507152422904-38adc8.png)

```js
let result = 25 ^ 3; 
console.log(result); // 26
```

![image-20240507152441363](https://cdn.jsdelivr.net/gh/Killer-89757/PicBed/images/2024%2F05%2Fimage-20240507152441363-532895.png)

#### 5. 有符号左移右移

有符号左移：<<

有符号右移：>>

```js
let oldValue = 2; // 等于二进制 10 
let newValue = oldValue << 5; // 等于二进制 1000000，即十进制 64
```

![image-20240507152509003](https://cdn.jsdelivr.net/gh/Killer-89757/PicBed/images/2024%2F05%2Fimage-20240507152509003-ab5d4e.png)

注意在移位后，数值右端会空出 5 位。**左移会以 0 填充**这些空位，让结果是完整的 32 位数值；注意，**左移会保留它所操作数值的符号**。比如，如果2 左移 5 位，将得到64，而不是正 64

```js
let oldValue = 64; // 等于二进制 1000000 
let newValue = oldValue >> 5; // 等于二进制 10，即十进制 2
```

![image-20240507152543269](https://cdn.jsdelivr.net/gh/Killer-89757/PicBed/images/2024%2F05%2Fimage-20240507152543269-181680.png)

同样，移位后就会出现空位。不过，右移后空位会出现在左侧，且在符号位之后。**右移会用符号位的值**来填充这些空位，以得到完整的数值。

#### 6. 无符号左移右移

无符号左移：<<<

无符号右移：>>>

```js
let oldValue = 64; // 等于二进制 1000000 
let newValue = oldValue >>> 5; // 等于二进制 10，即十进制 2
--------------------------------------------------------------
let oldValue = -64; // 等于二进制 11111111111111111111111111000000 
let newValue = oldValue >>> 5; // 等于十进制 134217726
```

### 3.5.3 布尔操作符

布尔操作由3个： 逻辑非、逻辑与和逻辑或。

#### 1. 逻辑非

逻辑非操作符由一个叹号（!）表示，可用于任何值。这个操作符始终返回布尔值。**逻辑非操作符首先将操作数转换为布尔值，然后取反**。逻辑非操作符遵循以下规则：

- 如果操作数是对象，则返回false
- 如果操作数是空字符串，则返回true
- 如果操作数是非空字符串，则返回false
- 如果操作数是数值0，则返回true
- 如果操作数是非0数值(包括Infinity)，则返回false
- 如果操作数是null，则返回true
- 如果操作数是NaN，则返回true
- 如果操作数是undefind，则返回true

同时使用两个叹号（!!），相当于调用了转型函数 Boolean()

#### 2. 逻辑与

逻辑与操作符由两个和号（&&）表示。

逻辑与操作符可用于任何类型的操作数，不限于布尔值。如果有操作数不是布尔值，则逻辑与并不一定会返回布尔值：规则：

- 如果第一个操作数是对象，则返回第二个操作数。

- 如果第二个操作数是对象，则只有第一个操作数求值为 true 才会返回该对象。

- 如果两个操作数都是对象，则返回第二个操作数。

- 如果有一个操作数是 null，则返回 null。

- 如果有一个操作数是 NaN，则返回 NaN。

- 如果有一个操作数是 undefined，则返回 undefined。

逻辑与操作符是一种**短路操作符**，如果第一个运算子的布尔值为true，则返回第二个运算子的值（注意是值，不是布尔值）；如果第一个运算子的布尔值为false，则直接返回第一个运算子的值，且不再对第二个运算子求值 。

``` javascript
let found = true
let result = (found && someUndeclaredVariable)   // 这里会出错
console.log(result)      // 不会执行这一行
```

```js
let found = false
let result = (found && someUndeclaredVariable)   // 这里不会出错
console.log(result)      // 会执行这一行
```

![image-20240507153828852](https://cdn.jsdelivr.net/gh/Killer-89757/PicBed/images/2024%2F05%2Fimage-20240507153828852-c6b8fb.png)

#### 3. 逻辑或

逻辑或操作符由两个管道符（||）表示。

与逻辑与类似，如果有一个操作数不是布尔值，那么逻辑或操作符也不一定返回布尔值，规则：

- 如果第一个操作数是对象，则返回第一个操作数。

- 如果第一个操作数求值为 false，则返回第二个操作数。

- 如果两个操作数都是对象，则返回第一个操作数。

- 如果两个操作数都是 null，则返回 null。

- 如果两个操作数都是 NaN，则返回 NaN。

- 如果两个操作数都是 undefined，则返回 undefined。

逻辑或操作符是一种**短路操作符**，如果第一个运算子的布尔值为false，则返回第二个运算子的值（注意是值，不是布尔值）；如果第一个运算子的布尔值为true，则直接返回第一个运算子的值，且不再对第二个运算子求值 。

![image-20240507154259024](https://cdn.jsdelivr.net/gh/Killer-89757/PicBed/images/2024%2F05%2Fimage-20240507154259024-1a861f.png)

逻辑或同样也具有短路的特性。

利用逻辑或的特性，可以避免给变量赋值null或undefind

```js
let myObject = preferredObject || backupObject
```

### 3.5.4 乘性操作符

3 个乘性操作符：乘法、除法和取模。

在处理非数值时，它们也会包含一些**自动的类型转换**。如果乘性操作符有不是数值的操作数，则该操作数会在后台被使用 Number()转型函数转换为数值。

#### 3.5.4.1 乘法操作符

乘法操作符由一个星号（*）表示，可以用于计算两个数值的乘积

规则：

- 如果操作数都是数值，则执行常规的乘法运算，即两个正值相乘是正值，两个负值相乘也是正值，正负符号不同的值相乘得到负值。如果 ECMAScript 不能表示乘积，则返回 Infinity 或 -Infinity。

- 如果有任一操作数是 NaN，则返回 NaN。
- 如果是 Infinity 乘以 0，则返回 NaN。
- 如果是 Infinity 乘以非 0的有限数值，则根据第二个操作数的符号返回 Infinity 或-Infinity。

- 如果是 Infinity 乘以 Infinity，则返回 Infinity。

- 如果有不是数值的操作数，则先在后台用 Number()将其转换为数值，然后再应用上述规则。

#### 3.5.4.2 除法操作符

除法操作符由一个斜杠（/）表示，用于计算第一个操作数除以第二个操作数的商

规则：

- 如果操作数都是数值，则执行常规的除法运算，即两个正值相除是正值，两个负值相除也是正值，符号不同的值相除得到负值。如果ECMAScript不能表示商，则返回Infinity或-Infinity。

- 如果有任一操作数是 NaN，则返回 NaN。

- 如果是 Infinity 除以 Infinity，则返回 NaN。

- 如果是 0 除以 0，则返回 NaN。

- 如果是非 0 的有限值除以 0，则根据第一个操作数的符号返回 Infinity 或-Infinity。

- 如果是 Infinity 除以任何数值，则根据第二个操作数的符号返回 Infinity 或-Infinity。

- 如果有不是数值的操作数，则先在后台用 Number()函数将其转换为数值，然后再应用上述规则。

#### 3.5.4.1 取模操作符

取模（余数）操作符由一个百分比符号（%）表示

规则：

- 如果操作数是数值，则执行常规除法运算，返回余数。

- 如果被除数是无限值，除数是有限值，则返回 NaN。

- 如果被除数是有限值，除数是 0，则返回 NaN。

- 如果是 Infinity 除以 Infinity，则返回 NaN。

- 如果被除数是有限值，除数是无限值，则返回被除数。

- 如果被除数是 0，除数不是 0，则返回 0。

- 如果有不是数值的操作数，则先在后台用 Number()函数将其转换为数值，然后再应用上述规则

### 3.5.5 指数操作符

ECMAScript 7 新增了指数操作符，Math.pow()现在有了自己的操作符 `**`

```js
console.log(Math.pow(3, 2); // 9 
console.log(3 ** 2); // 9 

console.log(Math.pow(16, 0.5); // 4 
console.log(16** 0.5); // 4
-----------------------------------
// 指数赋值运算符
let squared = 3; 
squared **= 2; 
console.log(squared); // 9

let sqrt = 16; 
sqrt **= 0.5; 
console.log(sqrt); // 4
```

### 3.5.6 加性操作符

#### 1. 加法操作符

加法操作符（+）用于求两个数的和。

如果两个操作数都是数值，加法操作符执行加法运算并根据如下规则返回结果。

- 如果有任一操作数是NaN，则返回NaN
- 如果是 `Infinity` 加 `Infinity`，返回 `Infinity`
- 如果是 `-Infinity` 加 `-Infinity`，返回`-Infinity`
- 如果是 `Infinity` 加 `-Infinity`，返回NaN
- 如果是+0 加 +0，返回+0
- 如果是-0 加 +0，返回+0
- 如果是-0 加 -0，返回-0

不过，如果有一个操作数是字符串，则要应用如下规则：

- 如果两个操作数都是字符串，则拼接
- 如果只有一个是字符串，则将另一个操作数转换为字符串，再拼接。

如果有任一操作数是对象、数值或布尔值，则调用它们的`toString()`方法以获取字符串，对于`undefind`和`null`则调用`String()`函数，分别获取`“undefind"`和`”null“`

```js
let num1 = 5; 
let num2 = 10; 
let message = "The sum of 5 and 10 is " + num1 + num2; 
console.log(message); // "The sum of 5 and 10 is 510"
```

#### 2. 减法操作符

简单记忆，减法操作符会尽力将两个操作数转成数值（Number(）。会优先调用对象的valueOf()方法，如果没有valueOf()则调用toString()方法，再将获得的值变为数值。如果操作数存在NaN，返回NaN

```js
let result1 = 5 - true    // true被转换成1， 结果是4
let result2 = NaN - 1     // NaN
let result3 = 5 - ''      // ''被转换成0， 结果是5
let result4 = 5 - null    // null被转换成0，结果是5
```

规则如下：

如果两个操作数都是数值，则执行数学减法运算并返回结果。

- 如果有任一操作数是 NaN，则返回 NaN。

- 如果是 `Infinity` 减 `Infinity`，则返回 NaN。

- 如果是 `-Infinity` 减 `-Infinity`，则返回 NaN。

- 如果是 `Infinity` 减 `-Infinity`，则返回 `Infinity`。

- 如果是 `-Infinity` 减 `Infinity`，则返回`-Infinity`。

- 如果是+0 减+0，则返回+0。

- 如果是+0 减-0，则返回-0。

- 如果是-0 减-0，则返回+0。

- 如果有任一操作数是字符串、布尔值、null 或 undefined，则先在后台使用 Number()将其转换为数值，然后再根据前面的规则执行数学运算。如果转换结果是 NaN，则减法计算的结果是NaN。

- 如果有任一操作数是对象，则调用其 valueOf()方法取得表示它的数值。如果该值是 NaN，则减法计算的结果是 NaN。如果对象没有 valueOf()方法，则调用其 toString()方法，然后再将得到的字符串转换为数值

### 3.5.7 关系操作符

关系操作符（>, < , >=, <=）

简单记忆，尽力将两个操作数转为数值，但如果是两个字符串例外，会对每个字符一一比较编码值的大小(本质还是对比数值的大小)。

规则如下：

- 如果操作数都是数值，则执行数值比较。

- 如果操作数都是字符串，则逐个比较字符串中对应字符的编码。

- 如果有任一操作数是数值，则将另一个操作数转换为数值，执行数值比较。

- 如果有任一操作数是对象，则调用其 valueOf()方法，取得结果后再根据前面的规则执行比较。如果没有 valueOf()操作符，则调用 toString()方法，取得结果后再根据前面的规则执行比较。

- 如果有任一操作数是布尔值，则将其转换为数值再执行比较。
- 如果有NaN，一定返回false

```js
"Brick" < "alphabet"  // true, B的编码为66，a为97
"23" < "3"            // true
"23" < 3              // false, "23"会被转换为23
"a" < 3               // false, "a"会被转换为NaN
```

### 3.5.8 相等操作符

#### 1. 等于和不等于(== , !=)

等于和不等于在**比较时会进行类型转换**。

这两个操作符都会先进行类型转换（通常称为**强制类型转换**）再确定操作数是否相等

规则：

- 如果任一操作数是布尔值，则将其转换为数值再比较是否相等。false 转换为 0，true 转换为 1。

- 如果一个操作数是字符串，另一个操作数是数值，则尝试将字符串转换为数值，再比较是否相等。

- 如果一个操作数是对象，另一个操作数不是，则调用对象的 valueOf()方法取得其原始值，再根据前面的规则进行比较。

在进行比较时，这两个操作符会遵循如下规则。

- **null 和 undefined 相等**

- null 和 undefined 不能转换为其他类型的值再进行比较

- 如果有任一操作数是 NaN，则相等操作符返回 false，不相等操作符返回 true。记住：即使两个操作数都是 NaN，相等操作符也返回 false，因为按照规则，NaN 不等于 NaN。

- 如果两个操作数都是对象，则比较它们是不是同一个对象。如果两个操作数都指向同一个对象，则相等操作符返回 true。否则，两者不相等。

```js
true == 1        // true， 因为会把true转为1
NaN == NaN       // false， NaN不和任何值相等
"5" == 5         // true， 会把"5"转为5
```

![image-20240507163804307](https://cdn.jsdelivr.net/gh/Killer-89757/PicBed/images/2024%2F05%2Fimage-20240507163804307-9e9f76.png)

#### 2. 全等和不全等(`===`, `!==`)

全等和不全等在比较时会首先判断数据类型是否相同，不相同则返回false。但是NaN仍然不和NaN相等。

```js
“5” === 5        // false， 数据类型不同
```

虽然 null == undefined 是 true（因为这两个值类似），但 null === undefined 是false，因为它们不是相同的数据类型

### 3.5.9 条件操作符

```js
variable = boolean_expression ? true_value : false_value;
```

根据条件表达式 `boolean_expression` 的值决定将哪个值赋给变量 `variable` 。如果 `boolean_expression` 是 `true` ，则赋值 `true_value` ；如果

`boolean_expression` 是 `false`，则赋值 `false_value`。

### 3.5.10 赋值操作符

简单赋值用等于号（=）表示，将右手边的值赋给左手边的变量

复合赋值使用乘性、加性或位操作符后跟等于号（=）表示

- 乘后赋值（*=）

- 除后赋值（/=）

- 取模后赋值（%=）

- 加后赋值（+=）

- 减后赋值（-=）

- 左移后赋值（<<=）

- 右移后赋值（>>=）

- 无符号右移后赋值（>>>=）

### 3.5.11 逗号操作符

逗号操作符可以用来在一条语句中执行多个操作

```js
let num1 = 1, num2 = 2, num3 = 3;
```

在赋值时使用逗号操作符分隔值，最终会返回表达式中最后一个值：

```js
let num = (5, 1, 4, 8, 0); // num 的值为 0
```

## 3.6 语句 

#### 3.6.1 if语句

这里的条件（condition）可以是任何表达式，并且**求值结果不一定是布尔值**。ECMAScript 会自动调用 Boolean()函数将这个表达式的值转换为布尔值。

```js
if (i > 25) 
 console.log("Greater than 25."); // 只有一行代码的语句
else { 
 console.log("Less than or equal to 25."); // 一个语句块
}
```

这里的最佳实践是**使用语句块**，即使只有一行代码要执行也是如此。

```js
if (condition1) 
	{statement1} 
else if (condition2) 
	{statement2} 
else 
	{statement3}
```

#### 3.6.2 do-while 语句

do-while 语句是一种后测试循环语句，即循环体中的代码执行后才会对退出条件进行求值。循环体内的代码至少执行一次。

```js
do { 
 statement 
} while (expression);
```

#### 3.6.3 while 语句

while 语句是一种先测试循环语句，即先检测退出条件，再执行循环体内的代码。while 循环体内的代码有可能不会执行。

```js
while(expression) 
	{statement}
```

#### 3.6.4 for 语句

for 语句也是先测试语句，只不过增加了进入循环之前的初始化代码，以及循环执行后要执行的表达式

```js
for (initialization; expression; post-loop-expression) 
	{statement}
```

最清晰的写法是使用 let 声明迭代器变量，这样就可以将这个变量的作用域限定在循环中

创建一个无穷循环

```js
for (;;) { // 无穷循环
  doSomething(); 
}
```

#### 3.6.5 for-in 语句

for-in 语句是一种严格的迭代语句，**用于枚举对象中的非符号键属性**

```js
for (property in expression) 
	{statement} 
```

对象的属性是无序的，因此 for-in 语句不能保证返回对象属性的顺序

如果 for-in 循环要迭代的变量是 null 或 undefined，则不执行循环体。

#### 3.6.6 for-of 语句

for-of 语句是一种严格的迭代语句，用于遍历可迭代对象的元素

```js
for (property of expression) 
	{statement}
```

for-of 循环会按照可迭代对象的 next()方法产生值的顺序迭代元素

### 3.6.7 标签语句(label)

标签语句用于给语句加标签

```js
label: statement

start: for (let i = 0; i < count; i++) { 
  console.log(i); 
}
```

start 是一个标签，可以在后面通过 break 或 continue 语句引用

标签语句的典型应用场景是**嵌套循环**。

### 3.6.8 break 和 continue 语句

break 和 continue 语句为执行循环代码提供了更严格的控制手段

- **break** ：用于立即退出循环，强制执行循环后的下一条语句
- **continue**：用于立即退出循环，但会再次从循环顶部开始执行

break 和 continue 都可以与标签语句一起使用，返回代码中特定的位置

```js
let num = 0; 
outermost: for (let i = 0; i < 10; i++) { 
  for (let j = 0; j < 10; j++) { 
    if (i == 5 && j == 5) { 
      break outermost; 
    } 
    num++; 
  } 
} 
console.log(num); // 55
```

组合使用标签语句和 break、continue 能实现复杂的逻辑，但也容易出错。注意标签要使用描述性强的文本，而嵌套也不要太深。

### 3.6.9 with 语句

with 语句的用途是将代码作用域设置为特定的对象

```js
with (expression) 
	{statement};
```

使用 with 语句的主要场景是针对**一个对象反复操作**，这时候将代码作用域设置为该对象能提供便利

```js
let qs = location.search.substring(1); 
let hostName = location.hostname; 
let url = location.href;

// 使用with简化代码
with(location) { 
  let qs = search.substring(1); 
  let hostName = hostname; 
  let url = href; 
}
```

这里，with 语句用于连接 location 对象。这意味着在这个语句内部，每个变量首先会被认为是一个局部变量。如果没有找到该局部变量，则会搜索 location 对象，看它是否有一个同名的属性。如果有，则该变量会被求值为 location 对象的属性。

严格模式不允许使用 with 语句，否则会抛出错误

### 3.6.10 switch 语句

```js
switch (expression) { 
  case value1: statement
    break; 
  case value2: statement 
    break; 
  case value3: statement 
    break; 
  case value4: statement 
     break; 
  default: statement 
}
```

这里的每个 case（条件/分支）相当于：如果表达式等于后面的值，则执行下面的语句。`break`关键字会导致代码执行跳出 `switch` 语句。如果没有 `break`，则代码会继续匹配下一个条件。`default`关键字用于在任何条件都没有满足时指定默认执行的语句（相当于 else 语句）。

特性：

- switch 语句可以用于所有数据类型（在很多语言中，它只能用于数值），因此可以使用字符串甚至对象
- 条件的值不需要是常量，也可以是变量或表达式

```js
let num = 25; 
switch (true) { 
  case num < 0: 
    console.log("Less than 0."); 
    break; 
  case num >= 0 && num <= 10: 
    console.log("Between 0 and 10."); 
    break; 
  case num > 10 && num <= 20: 
    console.log("Between 10 and 20."); 
    break; 
  default: 
    console.log("More than 20."); 
}
```

首先在外部定义了变量 num，而传给 switch 语句的参数之所以是 true，就是因为每个条件的表达式都会返回布尔值。条件的表达式分别被求值，直到有表达式返回 true；否则，就会一直跳到 default 语句（这个例子正是如此）。

switch 语句在比较每个条件的值时会使用**全等操作符**，因此不会强制转换数据类型（比如，字符串"10"不等于数值 10）

## 3.7 函数

ECMAScript中的函数使用function关键字声明，后跟一组参数，然后是函数体。

```js 
function functionName(arg0, arg1){
    statements
}
```

函数只要碰到return语句，就会立即停止执行并退出。

```js
function sum(num1, num2){
    return num1 + num2
    console.log('ok')       // 不会执行
}
```

如果没有指定return的变量，则函数默认返回undefind。

严格模式对函数也有一些限制：

- 函数不能以 `eval` 或 `arguments` 作为名称；

- 函数的参数不能叫 `eval` 或 `arguments`；

- 两个命名参数不能拥有同一个名称。

如果违反上述规则，则会导致语法错误，代码也不会执行。

#### 3.7.1 理解参数

> **重点**：
>
> - ECMAScript 函数**不介意传递进来多少个参数，也不在乎传进来参数是什么数据类型**。也就是说，**即便你定义的函数只接收两个参数，在调用这个函数时也未必一定要传递两个参数。可以传递一个、三个甚至不传递参数**，而解析器永远不会有什么怨言。之所以会这样，原因是 **ECMAScript 中的参数在内部是用一个`数组`来表示的**。**函数接收到的始终都是这个数组**，而不关心数组中包含哪些参数（如果有参数的话）。如果这个数组中不包含任何元素，无所谓；如果包含多个元素，也没有问题。实际上，**在函数体内可以通过 arguments 对象来访问这个参数数组，从而获取传递给函数的每一个参数**。

arguments 对象只是与数组类似（它并不是 Array 的实例），因为可以使用方括号语法访问它的每一个元素

**命名的参数只提供便利，但不是必需的**

```js
function sayHi() { 
  alert("Hello " + arguments[0] + "," + arguments[1]); 
}
```

就是 arguments 对象可以与命名参数一起使用

```js
function doAdd(num1, num2) { 
  if(arguments.length == 1) { 
    alert(num1 + 10); 
  } else if (arguments.length == 2) { 
    alert(arguments[0] + num2); 
  } 
}
```

关于 arguments 的行为，还有一点比较有意思。那就是它的值永远与对应命名参数的值保持同步

```js
function doAdd(num1, num2) { 
  // 修改 arguments[1]，也就修改了 num2，结果它们的值都会变成 10
  // 这并不是说读取这两个值会访问相同的内存空间；它们的内存空间是独立的，但它们的值会同步
  arguments[1] = 10; 
  alert(arguments[0] + num2); 
}
```

如果只传入了一个参数，那么为 arguments[1]设置的值不会反应到命名参数中。这是**因为 arguments 对象的长度是由传入的参数个数决定的，不是由定义函数时的命名参数的个数决定的。**

关于参数还要记住最后一点：**没有传递值的命名参数将自动被赋予 undefined 值**

#### 3.7.2 没有重载

ECMAScript 函数不能像传统意义上那样实现重载。而在其他语言（如 Java）中，可以为一个函数编写两个定义，只要这两个定义的签名（接受的参数的类型和数量）不同即可。如前所述，ECMAScirpt函数没有签名，因为其参数是由包含零或多个值的数组来表示的。而没有函数签名，真正的重载是不可能做到的。

如果在 ECMAScript 中定义了两个名字相同的函数，则该名字只属于后定义的函数

```js
function addSomeNumber(num){ 
 return num + 100; 
} 
function addSomeNumber(num) { 
 return num + 200; 
} 
var result = addSomeNumber(100); //300
```

