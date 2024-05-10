# 第四章：变量、作用域与内存

## 4.1 原始值与引用值

`ECMAScript`包含两种不同类型的数据：原始值(`基本类型值`)和引用值(`引用类型值`)。原始值就是最简单的数据，引用值则是由多个值构成的对象。

在将一个值赋给变量时，解析器必须确定这个值是基本类型值还是引用类型值。

**保存原始值的变量按值访问，操作的是储存在变量中的实际值**。类型：Undefined、Null、Boolean、Number、String 和 Symbol。

引用值是保存在内存中的对象，JavaScript 不允许直接访问内存中的位置，也就是说不能直接操作对象的内存空间。**保存引用值的变量是按引用访问的，操作的是对该对象的引用而不是对象本身**。

### 4.1.1 动态属性

对于引用值而言，可以随时添加、修改和删除其属性和方法。

原始值不能有属性，尽管尝试给原始值添加属性不会报错。

原始类型的初始化可以只使用原始字面量形式。**如果使用的是 new 关键字，则 JavaScript 会创建一个 Object 类型的实例，但其行为类似原始值**。下面来看看这两种初始化方式的差异。

```js
let name1 = "Nicholas"; 
let name2 = new String("Matt"); 
name1.age = 27; 
name2.age = 26; 
console.log(name1.age); // undefined 
console.log(name2.age); // 26 
console.log(typeof name1); // string 
console.log(typeof name2); // object
```

### 4.1.2 复制值

在通过变量把一个**原始值**赋值到另一个变量时，原始值会被**复制到新变量**的位置

![image-20240510102956158](https://cdn.jsdelivr.net/gh/Killer-89757/PicBed/images/2024%2F05%2Fimage-20240510102956158-c26a26.png)

引用值和原始值的区别在于，**引用值复制的值实际上是一个指针，指向堆内存中的对象**。

所以对同一个指针所指向的对象进行操作，变化会在两个变量中显示出来。

```js
let obj1 = new Object()
let obj2 = obj1         // 把指针赋给了obj2
let obj1.name = "ryan"
console.log(obj2.name)  // 因为指向同一个对象，name为"ryan"
```

![image-20240510103112543](https://cdn.jsdelivr.net/gh/Killer-89757/PicBed/images/2024%2F05%2Fimage-20240510103112543-5f27d7.png)

### 4.1.3 传递参数(`重点理解`)

**ECMAScript中所有函数的参数都是按值传递的**。这意味着函数外的值会被**复制到函数内部的参数**中，就像从一个变量复制到另一个变量一样

```js
function addTen(num){
    num += 10
    return num
}

let count = 20
let result = addTen(count)

console.log(count)    // 20，没有变化
console.log(result)   // 30
```

`num`实际上是一个局部变量，这个`num`保存了实参的值，仅仅是一个副本，因此不会影响外部变量。

```js
function setName(obj){
    obj.name = "ryan"
}

let person = new Object()

setName(person)
console.log(person.name)    // "ryan"
```

这里的person是一个引用值，obj参数依然遵循按值传递的规则，但是obj是一个指针副本，**因此对指针所指向的对象进行修改，实际上是通过引用进行修改**，变化会表现在外部。

```js
function setName(obj){
    obj.name = "ryan"
    obj = new Object()
    obj.name = "Titan"
}

let person = new Object()

setName(person)
console.log(person.name)    // "ryan"
```

现在`setName()`函数内部会将obj变量重新赋值一个新的对象，但是这个变化不会反应在外部。原因就是，obj实际上只是一个指针副本，覆盖这个指针并不会影响这个指针所指向的对象。

### 4.1.4 确定类型

**`typeof`**无法区分null或对象。**`typeof`**虽然对原始值很有用，但它对引用值的作用不大。

为解决这个问题，`ECMAScript`提供了**`instanceof`**操作符，语法如下：

```js
result = variable instanceof constructor
```

如果变量时给定构造函数的实例，则**`instanceof`**操作符返回true。

> 所有引用值都是Object的实例，所以通过**`instanceof`**操作符检测任何引用值和Object构造函数都会返回true。如果用 `instanceof` 检测原始值，则始终会返回 false，因为原始值不是对象。

## 4.2 执行上下文与作用域

执行上下文（又称作用域）（以下简称“上下文”）的概念在JavaScript中颇为重要。每个上下文都有一个关联的**变量对象**（variable object)。这个变量对象保存了上下文中所有变量和函数。

全局上下文即是最外层的上下文。**浏览器中，全局上下文就是window对象**。

每个函数调用都有自己的上下文。**当代码执行流进入函数时，函数的上下文被推到一个上下文栈上。函数执行结束，上下文栈会弹出该函数上下文，将控制权返还给之前的执行上下文。**

上下文中的代码在执行的时候，会创建变量对象的一个**作用域链**（scope chain)。这个作用域链决定了各级上下文中的代码在访问变量和函数时的顺序。代码正在执行的上下文的变量对象始终位于作用域的最前端。

如果上下文是函数，则其**活动对象**（activation object）用作变量对象。活动对象最初只有一个定义变量：arguments。作用域链中的下一个变量对象来自包含上下文（上层上下文），以此类推至全局上下文。全局上下文的变量对象始终是最后一个变量对象。

代码执行时的标识符解析是通过沿作用域链逐级搜索标识符名称完成的。搜索过程始终从作用域链的最前端开始，然后逐级往后，直到找到标识符。

```js
var color = "blue"; 
function changeColor() { 
  let anotherColor = "red"; 
  function swapColors() { 
    let tempColor = anotherColor; 
    anotherColor = color; 
    color = tempColor; 
    // 这里可以访问 color、anotherColor 和 tempColor 
  } 
  // 这里可以访问 color 和 anotherColor，但访问不到 tempColor 
  swapColors(); 
} 
// 这里只能访问 color 
changeColor();
```

以上代码涉及 3 个上下文：**全局上下文**、**changeColor()的局部上下文**和 **swapColors()的局部上下文**。

![image-20240510105734285](https://cdn.jsdelivr.net/gh/Killer-89757/PicBed/images/2024%2F05%2Fimage-20240510105734285-29c3d5.png)

### 4.2.1 作用域链增强

虽然执行上下文主要有全局上下文和函数上下文两种（`eval()`调用内部存在第三种上下文），但有其他方式来增强作用域链。某些语句会导致在作用域链前端临时添加一个上下文，这个上下文在代码执行后会被删除。

- try/catch 语句的 catch 块
- with 语句

这两种情况下，都会在作用域链前端添加一个变量对象。对 with 语句来说，会向作用域链前端添加指定的对象；对 catch 语句而言，则会创建一个新的变量对象，这个变量对象会包含要抛出的错误对象的声明。

```js
function buildUrl() { 
  let qs = "?debug=true"; 
  with(location){ 
    let url = href + qs; 
  } 
  return url; 
}
```

with 语句将 location 对象作为上下文，因此 location 会被添加到作用域链前端。buildUrl()函数中定义了一个变量 qs。当 with 语句中的代码引用变量 href 时，实际上引用的是location.href，也就是自己变量对象的属性。

### 4.2.2 变量声明

以前var都是声明变量的唯一关键字。ES6增加了let和const两个关键字。

这一节内容，推荐看一下这篇文章[《我用了两个月的时间才理解 let》](https://zhuanlan.zhihu.com/p/28140450)。

## 4.3 垃圾回收

JavaScript 是使用垃圾回收的语言，也就是说执行环境负责在代码执行时管理内存。

JavaScript通过自动内存管理实现内存分配和闲置资源回收。基本思路很简单：确定哪个变量不会再使用，然后释放它占用的内存。

这个过程是周期性的，即垃圾回收程序**每隔一定时间**（或者说在代码执行过程中某个预定的收集时间）就会自动运行。

在浏览器的发展史上，用到过两种主要的标记策略：**标记清理**和**引用计数**。

### 4.3.1 标记清理

**标记清理**（mark-and-sweep）是JavaScript**最常用**的垃圾回收策略。

垃圾回收程序在运行的时候会给存储在内存中的所有变量都加上标记（标记方法有很多种）。然后，它会去掉上下文中的变量以及被上下文的变量引用的变量的标记。而在此之后仍有标记的变量就是待删除的了，原因是任何在上下文中的变量已经无法访问到这些它们了。

 ### 4.3.2 引用计数

另一种没那么常用的垃圾回收策略是**引用计数**（reference counting）。其思路是对每个值都记录它被引用的次数。将一个引用值赋给一个变量时，将这个值的引用数加一。当这个变量被其他值覆盖时，这个值的引用数减一。当一个值的引用数为0时，就说明没办法再访问到这个值了，因此可以安全地收回其内存了。

引用计数在遇到**循环引用**时不适用，所谓循环引用，就是对象A有一个指针指向对象B，而对象B也引用了对象A。比如：

```js
function problem(){
    let objectA = new Object()      // 这里的new Object()值我们叫它a, a的引用数加一
    let objectB = new Object()      // 这里的new Object()值我们叫它b, b的引用数加一
    objectA.b = objectB             // a的引用数再加一
    objectB.a - objectA             // b的引用数再加一
}
```

这里两个对象的引用数都为2，所以永远不会被回收。

由于循环引用问题的存在，Netscape在4.0版放弃了引用计数，转而采用标记清理。

### 4.3.3 性能

垃圾回收程序会周期性运行，如果内存中分配了很多变量，则可能造成性能损失，因此垃圾回收的时间调度很重要。

现代垃圾回收程序会基于对 JavaScript 运行时环境的探测来决定何时运行。

### 4.3.4 内存管理

分配给浏览器的内存通常比分配给桌面软件的要少很多，因此减少内存占用十分重要。将内存占用量保持在一个较小的值可以让页面性能更好。优化内存占用的最佳手段就是保住在执行代码时只保持必要的数据。

如果数据不再必要，那么把它设置为null，从而释放引用。这也可以叫作**解除引用**

```js
let globalPerson = new Object()
globalPerson = null      // 解除globalPerson对值的引用
```

解除对一个值的引用并不会自动导致相关内存被回收。解除引用的关键在于确保相关的值已经不在上下文里了，因此它在下次垃圾回收时会被回收。

#### 4.3.4.1 通过 **const** 和 **let** 声明提升性能

因为 const和 let 都以块（而非函数）为作用域，所以相比于使用 var，使用这两个新关键字可能会更早地让垃圾回收程序介入，尽早回收应该回收的内存。

#### 4.3.4.2 隐藏类和删除操作

V8 会将创建的对象与隐藏类关联起来，以跟踪它们的属性特征。能够共享相同隐藏类的对象性能会更好，V8 会针对这种情况进行优化，但不一定总能够做到。

#### 4.3.4.3 内存泄漏

- 意外声明全局变量是最常见但也最容易修复的内存泄漏问题。

- 定时器也可能会悄悄地导致内存泄漏。
- 使用 JavaScript 闭包很容易在不知不觉间造成内存泄漏。

#### 4.3.4.4 静态分配与对象池

在初始化的某一时刻，**可以创建一个对象池，用来管理一组可回收的对象**。应用程序可以向这个对象池请求一个对象、设置其属性、使用它，然后在操作完成后再把它还给对象池。由于没发生对象初始化，垃圾回收探测就不会发现有对象更替，因此垃圾回收程序就不会那么频繁地运行。