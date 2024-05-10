# 第七章：迭代器与生成器

**ECMAScript 6** 规范新增了两个高级特性：`迭代器`和`生成器`

## 7.1 理解迭代

在JavaScript中，计数循环就是一种最简单的迭代：

```js
for(let i = 1; i <= 10; i ++){
    console.log(i)
}
```

**循环**是迭代机制的基础，这是因为它可以指定迭代的次数，以及要执行的操作，迭代的顺序是预先定义好的。

迭代会在一个**有序集合**上进行（比如数组）：

```js
let collection = ['foo', 'bar', 'baz']

for(let index = 0; index < collection.length; index ++){
    console.log(collection[index])
}
```

然而这种循环并不理想

- **迭代之前需要事先知道如何使用数据结构**。比如数组必须使用`[]`操作符获取数组中的一个元素。
- **遍历顺序并不是数据结构固有的**。通过递增索引来访问数据是特定于数组类型的方式，并不适用于其它数据结构。

ES5 新增了 `Array.prototype.forEach()` 方法

```js
let collection = ['foo', 'bar', 'baz']; 
collection.forEach((item) => console.log(item));
```

`forEach()`解决了单独记录索引和通过数组对象取得值的问题。

**缺点**：不过，没有办法标识迭代何时终止。因此这个方法**只适用于数组**，而且回调结构也比较笨拙

随着代码量的增加，代码会变得愈发混乱。JavaScript在ECMAScript 6以后支持了**迭代器模式**，作为这些问题的解决方案。

## 7.2 迭代器模式

`ECMAScript 6` 以后也支持了**迭代器模式**

**迭代器模式**描述了一个方案，即可以把有些结构称为“**可迭代对象**”，因为它们实现了`Iterable`接口，而且可以通过`Iterable`接口生成的**迭代器**消费。

把**可迭代对象**理解成`数组`或`集合`这样的集合类型的对象。它们包含的**元素都是有限的**，而且都**具有无歧义的遍历顺序**

**任何实现 Iterable 接口的数据结构都可以被实现 Iterator 接口的结构“消费”（consume）**

迭代器（iterator）是按需创建的一次性对象。每个迭代器都会关联一个可迭代对象，而迭代器会暴露迭代其关联可迭代对象的 API。

### 7.2.1 可迭代协议

实现Iterable接口要求具备两种能力

- **支持迭代的自我识别能力**：对象必须暴露`Symbol.iterator`属性，将这个属性作为“**默认迭代器**”
- **创建具有Iterator接口的对象的能力**：即默认迭代器（Symbol.iterator）必须返回一个新的迭代器。

内置类型都实现了 Iterable 接口：`字符串`、`数组`、`映射`、`集合`、`arguments 对象`、`NodeList 等 DOM 集合类型`

数组就是一个**可迭代对象**：

```js
let arr = [1, 2, 3]
```

它实现了Iterable接口：

```js
let arr = [1, 2, 3]
console.log(arr[Symbol.iterator])   // values() { [native code] }
```

Iterable接口可以生成一个具有Iterator接口的对象（我们叫它**迭代器**）

```js
let arr = [1, 2, 3]
console.log(arr[Symbol.iterator])   // values() { [native code] }
console.log(arr[Symbol.iterator]()) // Array Iterator {}
```

### 7.2.2 迭代器协议

迭代器是一种**一次性使用的对象**，用于**迭代与其关联的可迭代对象**

迭代器使用**next**()方法在可迭代对象中遍历数据。每次成功调用**next**()，都会返回一个`IteratorResult`对象，这个对象包含两个属性：done和value

- done是一个布尔值，表示是否还可以再次调用 next()取得下一个值；
- value包
  - done为true时，value为undefined，意为“耗尽”。
  - done为false时，value包含可迭代对象的下一个值

```js
let arr = ['foo', 'bar']

let iter = arr[Symbol.iterator]()

console.log(iter) // Array Iterator{}

// 执行迭代
console.log(iter.next()) // { done: false, value: 'foo' }
console.log(iter.next()) // { done: false, value: 'foo' }
console.log(iter.next()) // { done: true, value: undefined }
```

每个迭代器都表示对可迭代对象的一次性有序遍历。不同迭代器的实例相互之间没有联系，只会独立地遍历可迭代对象

```js
let arr = ['foo', 'bar']; 
let iter1 = arr[Symbol.iterator](); 
let iter2 = arr[Symbol.iterator](); 
console.log(iter1.next()); // { done: false, value: 'foo' } 
console.log(iter2.next()); // { done: false, value: 'foo' } 
console.log(iter2.next()); // { done: false, value: 'bar' } 
console.log(iter1.next()); // { done: false, value: 'bar' }
```

迭代器并不与可迭代对象某个时刻的快照绑定，而仅仅是使用游标来记录遍历可迭代对象的历程。**如果可迭代对象在迭代期间被修改了，那么迭代器也会反映相应的变化**

```js
let arr = ['foo', 'baz']; 
let iter = arr[Symbol.iterator](); 
console.log(iter.next()); // { done: false, value: 'foo' } 

// 在数组中间插入值
arr.splice(1, 0, 'bar'); 

console.log(iter.next()); // { done: false, value: 'bar' } 
console.log(iter.next()); // { done: false, value: 'baz' } 
console.log(iter.next()); // { done: true, value: undefined }
```

迭代器维护着一个指向可迭代对象的引用，因此**迭代器会阻止垃圾回收程序回收可迭代对象**

比较了一个显式的迭代器实现和一个原生的迭代器实现

```js
// 自己实现迭代器
// 这个类实现了可迭代接口（Iterable）
// 调用默认的迭代器工厂函数会返回
// 一个实现迭代器接口（Iterator）的迭代器对象
class Foo {
    [Symbol.iterator]() {
        return {
            next() {
                return { done: false, value: 'foo' };
            }
        }
    }
}
let f = new Foo();
// 打印出实现了迭代器接口的对象
console.log(f[Symbol.iterator]()); // { next: f() {} }
-------------------------------------------------------------------------
// js内置实现迭代器
// Array 类型实现了可迭代接口（Iterable）
// 调用 Array 类型的默认迭代器工厂函数
// 会创建一个 ArrayIterator 的实例
let a = new Array();
// 打印出 ArrayIterator 的实例
console.log(a[Symbol.iterator]()); // Array Iterator {}
```

为了更方便地使用迭代器，我们可以使用**接收可迭代对象的原生语言特性**

如 **for-of** 循环：

```js
let arr = [1, 2, 3, 4]
for(let num of arr){
    console.log(arr)
}
// 1
// 2
// 3
// 4
```

其他接收可迭代对象的原生语言特性：

- `for-of`循环

- 数组解构
- 扩展操作符
- `Array.from()`
- 创建集合
- 创建映射
- `Promise.all()`接收由期约组成的可迭代对象
- `Promise.race()`接收由期约组成的可迭代对象
- `yield*`操作符，在生成器中使用

我在这里实现一个简单的可迭代对象，帮助大家更好地理解迭代器模式。

```js
// 实现了Iterable接口的对象，并且这个接口返回一个迭代器
let iterable = {
    [Symbol.iterator](){			
        let count = 0
        // 返回了一个具有Iterator接口的对象（迭代器）
        return {
            next(){
                /*
                  每次调用next，count的值会加一，当count等于5的时候停止。
                */
                if( count ++ < 5){
                    return {
                        done: false,
                        value: count,
                    }
                }else{
                    return {
                        done: true,
                        value: undefined
                    }
                }
            }
        }         
    }
}

for(let num of iterable){
    console.log(num)
}
// 1
// 2
// 3
// 4
// 5

let arr = [...iterable]
console.log(arr)   // [1, 2, 3, 4, 5]
```

**按照我自己的理解做一个总结：迭代器模式，就是可以让本不具备迭代能力的对象，具备可迭代的能力。最直观的体现就是能在for-of循环中直接遍历对象。**

### 7.2.3 自定义迭代器

```js
class Counter {
    // Counter 的实例应该迭代 limit 次
    constructor(limit) {
        this.count = 1;
        this.limit = limit;
    }

    next() {
        if (this.count <= this.limit) {
            return {done: false, value: this.count++};
        } else {
            return {done: true, value: undefined};
        }
    }

    [Symbol.iterator]() {
        return this;
    }
}

let counter = new Counter(3);
for (let i of counter) {
    console.log(i);
}
// 1
// 2
// 3

//这个类实现了 Iterator 接口，但不理想。这是因为它的每个实例只能被迭代一次：
for (let i of counter) {
    console.log(i);
}
// (nothing logged)
```

上面的实例只能被迭代一次，为了让一个可迭代对象能够创建多个迭代器，必须每创建一个迭代器就对应一个新计数器。

```js
// 把计数器变量放到闭包里，然后通过闭包返回迭代器
class Counter {
    constructor(limit) {
        this.limit = limit;
    }
    [Symbol.iterator]() {
        let count = 1,
            limit = this.limit;
        return {
            next() {
                if (count <= limit) {
                    return { done: false, value: count++ };
                } else {
                    return { done: true, value: undefined };
                }
            }
        };
    }
}
let counter = new Counter(3);
for (let i of counter) { console.log(i); }
// 1
// 2
// 3
for (let i of counter) { console.log(i); }
// 1
// 2
// 3
```

每个以这种方式创建的迭代器也实现了 Iterable 接口。Symbol.iterator 属性引用的工厂函数会返回相同的迭代器

```js
let arr = ['foo', 'bar', 'baz']; 

let iter1 = arr[Symbol.iterator](); 
console.log(iter1[Symbol.iterator]); // f values() { [native code] } 

let iter2 = iter1[Symbol.iterator](); 
console.log(iter1 === iter2); // true
```

### 7.2.4 提前终止迭代器

执行迭代的结构在想让迭代器知道它不想遍历到可迭代对象耗尽时，就可以“关闭”迭代器。

- for-of 循环通过 break、continue、return 或 throw 提前退出
- 解构操作并未消费所有值

要知道某个迭代器是否可关闭，可以测试这个迭代器实例的 return 属性是不是函数对象。不过，仅仅给一个不可关闭的迭代器增加这个方法**并不能**让它变成可关闭的。这是因为调用 return()不会强制迭代器进入关闭状态。

## 7.3 生成器

生成器是**ECMAScript6**新增的一个极为灵活的结构，拥有一个函数块内暂停和恢复代码执行的能力。可以用作**自定义迭代器**和**实现协程**。

### 7.3.1 生成器基础

生成器是一个函数，在函数名前面加一个星号（*）表示它是一个生成器：

```js
function * generator(){
    
}
```

> 箭头函数不能用来定义生成器
>
> 标识生成器函数的星号**不受两侧空格**的影响

调用生成器函数会产生一个**生成器对象**，这个对象也实现了Iterator接口。

生成器对象一开始处于**暂停执行**（suspended）的状态，调用**next**()方法，调用这个方法会让生成器开始或恢复执行。

next()方法的返回值类似于迭代器，有一个done属性和一个value属性。函数体为空的生成器函数中间不会停留，调用一次**next**()就会到达**done: true**状态。

```js
function * generator(){
    
}

let generatorObj = generator()

console.log(generatorObj)           // generator {<suspended>}
console.log(generatorObj.next())    // {value: undefined, done: true}
```

value属性是生成器的返回值，默认为undefined，可以通过生成器函数的返回值指定：

```js
function * generator(){
    return 'foo'
}

let generatorObj = generator()

console.log(generatorObj)           // generator {<suspended>}
console.log(generatorObj.next())    // {value: 'foo', done: true}
```

生成器只会在初次调用next()方法后开始执行：

```js
function * generator(){
    console.log(1)
}

let generatorObj = generator()

console.log(generatorObj)           // generator {<suspended>}
console.log(generatorObj.next())    // 1
```

生成器对象实现了Iterable接口，它们**默认的迭代器是自引用**的：

```js
function * generator(){
    console.log(1)
}

let generatorObj = generator()

console.log(generatorObj === generatorObj[Symbol.iterator]())   // true
```



### 7.3.2 通过yield中断执行

生成器函数在遇到yield关键字之前 会正常执行，但遇到这个关键字后，执行会停止，函数作用域的状态会被保留。必须使用**next**()方法恢复执行。

- yield后跟的值会出现在**next**()方法返回的对象里（value属性）。

- 通过yield关键字暂停的生成器函数会处在**done: false**状态。

```js
function * generator(){
    yield 1
    yield 2
    yield 3
}

let generatorObj = generator()

console.log(generatorObj.next())    // {value: 1, done: false}
console.log(generatorObj.next())    // {value: 2, done: false}
console.log(generatorObj.next())    // {value: 3, done: false}
console.log(generatorObj.next())    // {value: undefined, done: true}
```

生成器函数生成的生成器对象不会干扰其他的生成器对象：

```js
function * generator(){
    yield 1
    yield 2
    yield 3
}

let generatorObj1 = generator()
let generatorObj2 = generator()

console.log(generatorObj1.next())    // {value: 1, done: false}
console.log(generatorObj2.next())    // {value: 1, done: false}
```

yield只能在生成器函数内使用，在其他地方会抛出错误。

```js
// 有效
function* validGeneratorFn() { 
    yield; 
} 
// 无效
function* invalidGeneratorFnA() { 
    function a() { 
        yield; 
    } 
} 
// 无效
function* invalidGeneratorFnB() { 
    const b = () => { 
        yield; 
    } 
} 
// 无效
function* invalidGeneratorFnC() { 
    (() => { 
        yield; 
    })(); 
}
```

#### 1. 生成器对象作为可迭代对象

可以将生成器对象作为可迭代对象使用（因为它实现了iterable接口）：

```js
function * generator(){
    yield 1
    yield 2
    yield 3
}

for(let num of generator()){
    console.log(num)
}
// 1
// 2
// 3
```

#### 2. 使用yield实现输入和输出

`yield` 关键字还可以作为函数的中间参数使用。上一次让生成器函数暂停的 yield 关键字会接收到传给 next()方法的第一个值。

**第一次调用next()传入的值不会被使用，因为这次调用是为了开始执行生成器函数：**

```js
function* generator() {
    console.log('start')
    let a = yield 1
    console.log("a--传入的参数是：", a)   // a
    let b = yield 2
    console.log("a--传入的参数是", b)   // b
}

let generatorObj = generator()

console.log(generatorObj.next().value)  // 'start'
console.log(generatorObj.next('a').value)  // '1'
console.log(generatorObj.next('b').value)  // '2'
```

yield 关键字并非只能使用一次

```js
function* generatorFn() { 
    for (let i = 0;;++i) { 
        yield i; 
    } 
} 
let generatorObject = generatorFn(); 
console.log(generatorObject.next().value); // 0 
console.log(generatorObject.next().value); // 1 
console.log(generatorObject.next().value); // 2 
console.log(generatorObject.next().value); // 3 
console.log(generatorObject.next().value); // 4 
console.log(generatorObject.next().value); // 5 
...
```

#### 3. 产生可迭代对象

可以使用星号增强yield的行为，让它能够迭代一个可迭代对象。

```js
function * generator(){
    yield* [1, 2, 3]
}

for(let num of generator()){
    console.log(num)
}

// 1
// 2
// 3
```

```js
function* generatorFnA() { 
  	for (const x of [1, 2, 3]) { 
 		yield x; 
 	} 
} 
for (const x of generatorFnA()) { 
 	console.log(x); 
} 
// 1 
// 2 
// 3 

// 等价于
function* generatorFnB() { 
    yield* [1, 2, 3]; 
} 
for (const x of generatorFnB()) { 
 	console.log(x); 
}
```

yield*的值是关联迭代器返回 done: true 时的 value 属性。对于普通迭代器来说，这个值是undefined

### 7.3.3 生成器作为默认迭代器

生成器很适合用作实现对象的iterable接口。

改写我们先前的例子：

```js
// 实现了Iterable接口的对象，并且这个接口返回一个迭代器
let iterable = {
    // 修改为生成器函数
    *[Symbol.iterator](){			
        let count = 0
        // 循环，并且使用yield输出值。
        while(count ++ < 5){
            yield count
        }
    }
}

for(let num of iterable){
    console.log(num)
}
// 1
// 2
// 3
// 4
// 5

let arr = [...iterable]
console.log(arr)   // [1, 2, 3, 4, 5]
```

代码变得简洁许多。

### 7.3.4 提前终止生成器

一个实现 Iterator 接口的对象一定有 `next()`方法，还有一个可选的 `return()`方法用于提前终止迭代器，生成器对象除了有这两个方法，还有第三

个方法：`throw()`。

```js
function* generatorFn() {} 
const g = generatorFn(); 
console.log(g); // generatorFn {<suspended>} 
console.log(g.next); // f next() { [native code] } 
console.log(g.return); // f return() { [native code] } 
console.log(g.throw); // f throw() { [native code] }
```

`return()` 和 `throw()`  方法都可以用于强制生成器进入关闭状态。

-  **return()**

`return()` 方法会强制生成器进入关闭状态。提供给 return()方法的值，就是终止迭代器对象的值

```js
function* generatorFn() { 
 	for (const x of [1, 2, 3]) { 
 		yield x; 
 	} 
} 
const g = generatorFn(); 
console.log(g); // generatorFn {<suspended>} 
console.log(g.return(4)); // { done: true, value: 4 } 
console.log(g); // generatorFn {<closed>}
```

所有生成器对象都有 return()方法，只要通过它进入关闭状态，就无法恢复了。后续调用 next()会显示 done: true 状态，而提供的任何返回值都不会被存储或传播

- **throw()**

throw()方法会在暂停的时候将一个提供的错误注入到生成器对象中。如果错误未被处理，生成器就会关闭

```js
function* generatorFn() { 
 	for (const x of [1, 2, 3]) { 
 		try { 
 			yield x; 
        } catch(e) {} 
 	} 
}
const g = generatorFn(); 
console.log(g.next()); // { done: false, value: 1} 
g.throw('foo'); 
console.log(g.next()); // { done: false, value: 3}
```

> 如果生成器对象还没有开始执行，那么调用 throw()抛出的错误不会在函数内部被捕获，因为这相当于在函数块外部抛出了错误