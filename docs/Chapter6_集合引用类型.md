# 第六章：集合引用类型

## 6.1 Object

Object是ECMAScript中最常用的类型之一。

虽然 Object 的实例没有多少功能，但很适合存储和在应用程序间交换数据。

创建Object有两种方式，

- 一种是使用new操作符和Object构造函数

```js
let person = new Object()
person.name = 'Ryan'
```

- 另一种方式是使用**对象字面量**表示法（object literal）

左大括号（{）表示**对象字面量**开始，因为它出现在一个表达式上下文（expression context）中。在 ECMAScript 中，**表达式上下文指的是期待返回值的上下文**。赋值操作符表示后面要期待一个值，因此左大括号表示一个表达式的开始。

>  同样是左大括号，如果出现在语句上下文（statement context）中，比如 if 语句的条件后面，则表示一个语句块的开始。

```js
// 对象字面量表示
let person2 = {
    name: 'Titan'
}         
```

逗号用于在对象字面量中分隔属性，字符串的属性名可以是字符串或数值，**数值属性会自动转换为字符串**

> 在最后一个属性后面加上逗号在**非常老的浏览器**中会导致报错，但**所有现代浏览器都支持这种写法**。

```js
let person = {
    "name": "Ryan",
    age: 20,
    5: true
}

let person = {}; // 与 new Object()相同
person.name = "Nicholas"; 
person.age = 29;
```

在使用对象字面量表示法定义对象时，并不会实际调用 Object 构造函数。

虽然属性一般是通过**点语法**来存取的，这也是面向对象语言的惯例，但也可以使用中括号来存取属性。在使用中括号时，要在括号内使用属性名的字符串形式

```js
console.log(person["name"]); // "Nicholas" 
console.log(person.name); // "Nicholas"
```

使用中括号可以通过变量访问属性，或设置属性。

```js
let option = 'name'

let person = {
    [option]: 'Ryan'
}

person[option] = 'AttackonRyan'
```

通常，点语法是首选的属性存取方式，除非访问属性时必须使用变量。

如果属性名中包含可能会导致语法错误的字符，或者包含关键字/保留字时，也可以使用中括号语法

```js
// 因为"first name"中包含一个空格，所以不能使用点语法来访问。
person["first name"] = "Nicholas";
```

## 6.2 Array

ECMAScript中的数组是一组**有序**的数据，且**每个槽位可以存储任意类型的数据**。

ECMAScript中的数组也是动态大小的，会随着数据的添加而自动增长。

### 6.2.1 创建数组

#### Array构造函数

- 使用Array构造函数，给构造函数传入一个数值，然后length属性就会被自动创建并设置为这个值。

```js
let colors = new Array(20)    // 创建一个初始length为20的数组
```

也可以给Array构造函数传入要保存的元素。比如下面的代码会创建一个包含3个字符串值的数组。

```js
let colors = new Array("red", "blue", "green")
```

在使用 Array 构造函数时，也可以省略 new 操作符。

```js
let colors = new Array(3); // 创建一个包含 3 个元素的数组
let names = new Array("Greg"); // 创建一个只包含一个元素，即字符串"Greg"的数组

// 等价于
let colors = Array(3); // 创建一个包含 3 个元素的数组
let names = Array("Greg"); // 创建一个只包含一个元素，即字符串"Greg"的数组
```

#### 数组字面量

另一种创建数组的方式是使用**数组字面量**（array literal）表示法。

```js
let colors = ["red", "blue", "green"]
```

在使用数组字面量表示法创建数组不会调用 Array 构造函数

#### ES6新增(from/of)

##### Array.of()

有时候想创建一个只含一个数字的数组，直接使用Array构造函数是办不到的，因为他会创建一个长度为传入的数值的数组。这时候可以使用Array.of()方法（ES6新增），这个方法用于将一组参数转换为数组实例。

```js
let colors = new Array(20)    // [empty × 20] 创建了一个初始length为20的数组
let nums = Array.of(20)       // [20]
```

##### Array.from()

Array.from()也可以创建数组，用于将类数组结构转换为数组实例。

Array.from()的第一个参数是一个类数组对象，即任何可迭代的结构，或者有一个length属性和可索引元素的结构。

```js
// 字符串会被拆分成单字符串数组
console.log(Array.from("Ryan"))  // ["R", "y", "a", "n"]

// 可以使用 from()将集合和映射转换为一个新数组
const m = new Map().set(1, 2)
	.set(3, 4);
const s = new Set().add(1)
	.add(2)
	.add(3)
	.add(4);
console.log(Array.from(m)); // [[1, 2], [3, 4]]
alert(Array.from(s)); // [1, 2, 3, 4]

// Array.from()对现有数组执行浅复制
const a1 = [1, 2, 3, 4]
const a2 = Array.from(a1)

console.log(a1)      // [1, 2, 3, 4]
console.log(a1 === a2)  // false

// arguments对象可以被轻松地转换为数组
function getArgsArray(){
    return Array.from(arguments)
}
console.log(getArgsArray(1, 2, 3))   // [1, 2, 3]

// from()也能转换带有必要属性的自定义对象(有一个length属性和可索引元素的结构)
const arrayLikeObject = {
    0: 1,
    1: 2,
    2: 3,
    3: 4,
    length: 4
}
console.log(Array.from(arrayLikeObject))   // [1, 2, 3, 4]
```

Array.from()还接收第二个可选的映射函数参数。这个函数可以改写新数组的值。还可以接收第三个可选参数，用于指定映射函数中this的值。

```js
const a1 = [1, 2, 3, 4]
const a2 = Array.from(a1, x => x  2)
const a3 = Array.from(a1, function(x){ 
    return x  this.exponent
}, {
    exponent: 2
})

console.log(a2)   // [1, 4, 9, 16]
console.log(a3)   // [1, 4, 9, 16]
```

### 6.2.2 数组空位

使用数组字面量初始化数组时，可以使用一串逗号来创建空位（hole）。ECMAScript会将逗号之间相应索引位置的值当成空位，ES6规范重新定义了该如何处理这些空位。

```js
const options = [,,,,,]    //创建包含5个元素的数组
console.log(options.length)   // 5
console.log(options)    // [,,,,,]
```

ES6 新增方法(`for-of`)普遍将这些空位当成存在的元素，只不过值为 undefined。

```js
const options = [1,,,,5]; 
for (const option of options) { 
  console.log(option === undefined); 
} 
// false 
// true 
// true 
// true 
// false
```

ES6之前的方法(`map`，`join`)则会忽略这个空位，但具体的行为也会因方法而异。

> 注意：由于行为不一致和存在性能隐患，实践中要避免使用数组空位。如确实需要空位，则可以显示地用undefined代替

```js
const options = [1,,,,5]; 
// map()会跳过空位置
console.log(options.map(() => 6)); // [6, undefined, undefined, undefined, 6] 
// join()视空位置为空字符串
console.log(options.join('-')); // "1----5"
```

### 6.2.3 数组索引

要取得或设置数组的值，需要使用中括号并提供相应值的数字索引：

如果索引小于数组包含的元素数，则返回存储在相应位置的元素。

设置数组的值方法也是一样的，就是替换指定位置的值。

```js
let colors = ["red", "blue", "green"]
colors[2] = "black"   // 修改第三项
```

如果把一个值设置给超过数组最大索引的索引，则数组长度会自动扩展到该索引值加1

```js
let colors = ["red", "blue", "green"]
colors[5] = "black"   // 设置第6项为"black"
console.log(colors.length)   // 6
```

**通过修改length属性，可以截断或增长数组**。增长数组后多出来的空位由**undefined**填充。

```js
let colors = ["red", "blue", "green"]
colors.length = 1
console.log(colors)   // ["red"]

colors.length = 4
console.log(colors[3]) // undefined 
```

使用 length 属性可以方便地向数组末尾添加元素

```js
let colors = ["red", "blue", "green"]; // 创建一个包含 3 个字符串的数组
colors[colors.length] = "black"; // 添加一种颜色（位置 3）
colors[colors.length] = "brown"; // 再添加一种颜色（位置 4）
```

```js
let colors = ["red", "blue", "green"]; // 创建一个包含 3 个字符串的数组
colors[99] = "black"; // 添加一种颜色（位置 99）
alert(colors.length); // 100
```

colors 数组有一个值被插入到位置 99，结果新 length 就变成了 100（99 + 1）。这中间的所有元素，即位置 3~98，实际上并不存在，因此在访问时会返回 undefined。

### 6.2.4 检测数组

#### instanceof

只有一个全局作用域，**instanceof** 操作符可以检测一个对象是不是数组。

```js
if(value instanceof Array){
   // 操作数组 
}
```

#### Array.isArray()

但是如果网页里存在多个框架，可能会涉及两个不同的全局执行上下文，因此会有两个不同版本的Array构造函数，这个时候**instanceof**不一定返回正确的结果。

为解决这个问题，ECMAScript提供了**Array.isArray**()方法。这个方法的目的是确定一个值是否为数组，而不用管它在哪个全局执行上下文中创建的。

```js
if(Array.isArray(value)){
    // 操作数组
}
```

### 6.2.5 迭代器方法

ES6中，Array的原型上暴露了3个用于检索数组内容的方法：**keys**()、**values**()和**entries**()。

- **keys**()返回数组索引的迭代器
- **values**()返回数组元素的迭代器
- **entries**()返回索引/值对的迭代器。

```js
const a = ["foo", "bar", "baz", "qux"]

// 因为这些方法都返回迭代器，所以可以将它们的内容通过Array.from()直接转换为数组实例
const aKeys = Array.from(a.keys())
const aValues = Array.from(a.balues())
const aEntries = Array.from(a.entries())

console.log(aKeys)      // [0, 1, 2, 3]
console.log(aValues)    // ["foo", "bar", "baz", "qux"]
console.log(aEntries)   // [[0, "foo"], [1, "bar"], [2, "baz"], [3, "qux"]]
```

### 6.2.6 复制和填充方法

#### fill()

ES6新增了两个方法：批量赋值方法**fill**()，以及填充数组方法**copyWithin**()。

**fill**()的第一个参数是填充的值，第二个可选参数是索引的开始。第三个可选参数的索引的结束。

```js
const zeroes = [0, 0, 0, 0, 0]
// 用 7 填充索引大于等于 1 且小于 3 的元素
zeroes.fill(7, 1, 3) 

console.log(zeroes)  // [0, 7, 7, 0, 0]
```

#### copyWithin()

**copyWithin**()会按照指定范围浅复制数组中的部分内容，然后插入到指定索引开始位置。

- 第一个参数是索引开始位置
- 第二，第三个参数是指定范围的开始索引和结束索引。

```js
const numbers = [1,2,3,4,5,6]

// JavaScript 引擎在插值前会完整复制范围内的值
// 因此复制期间不存在重写的风险
numbers.copyWithin(2, 4, 6)

console.log(numbers)  // [1, 2, 5, 6, 5, 6]
```

### 6.2.7 转换方法

所有对象都有**toLocaleString**()、**toString**()和**valueOf**()方法。

- **valueOf**()：返回的还是数组本身。
- **toString**()：返回由数组中每个值调用**toString**()返回的字符串拼接而成的一个逗号分隔的字符串。

- **toLocaleString**()：方法类似**toString**()，取数组每个元素值的时候会调用**toLocaleString**()方法，返回字符串拼接而成的一个逗号分隔的字符串。

```js
let colors = ["red", "blue", "green"]
console.log(colors.toString())   // "red,blue,green"
alert(colors); // red,blue,green
```

最后一行代码直接用 alert()显示数组，因为 alert()期待字符串，所以会在后台调用数组的 toString()方法，从而得到跟前面一样的结果。

调用数组上的**join**()方法也可以获取字符串，不传参的情况下与**toString**()的返回值相同，如果传了参数，则分隔符变为参数。

如果不给 join()传入任何参数，或者传入 undefined，则仍然使用逗号作为分隔符。

```js
let colors = ["red", "blue", "green"]
console.log(colors.join(","))  // "red,green,blue"
console.log(colors.join("||")) // "red||green||blue"
```

> 如果数组中某一项是 null 或 undefined，则在 join()、toLocaleString()、toString()和 valueOf()返回的结果中会以空字符串表示

### 6.2.8 栈方法

ECMAScript数组提供了**push**()和**pop**()方法，以实现类似栈的行为。

**push**()：方法接收任意数量的参数，并将它们添加到数组末尾，返回数组的最新长度。

**pop**()：方法则用于删除数组的最后一项，同时减少数组的length值，返回被删除的项。

```js
let colors = new Array(); // 创建一个数组
let count = colors.push("red", "green"); // 推入两项
alert(count); // 2 
count = colors.push("black"); // 再推入一项
alert(count); // 3 

let item = colors.pop(); // 取得最后一项
alert(item); // black 
alert(colors.length); // 2
```

### 6.2.9 队列方法

使用**shift**()和**push**()方法，可以把数组当成队列来使用。

**shift**()：方法会删除数组的第一项并返回它。

ECMAScript也提供了**unshift**()方法，这个方法执行**shift**()相反的操作：在数组开头添加任意多个值，然后返回新的数组长度。

```js
let colors = new Array(); // 创建一个数组
let count = colors.push("red", "green"); // 推入两项
alert(count); // 2 
count = colors.push("black"); // 再推入一项
alert(count); // 3 

let item = colors.shift(); // 取得第一项
alert(item); // red 
alert(colors.length); // 2
```

### 6.2.10 排序方法

#### reverse()

数组有两种方法可以用来对元素重新排序：reverse()和sort()。

**reverse**()方法将数组元素反向 排列。

```js
let values = [1, 2, 3, 4, 5]
values.reverse()
console.log(values)  // [5, 4, 3, 2, 1]
```

#### sort()

**sort**()方法用于排序数组，**默认情况下会按照升序排序**，最小的值在前面，最大的值在后面。为此**sort**()会在每一项上**调用String()转型函数，然后来比较字符串决定顺序**。即使数组的元素都是数值，也会先把数组转换为字符串再比较、排序

```js
let values = [0, 1, 5, 10, 15]
values.sort()
console.log(values) // [0, 1, 10, 15 ,5]     // "5" > "15"
```

**sort**()方法接收一个比较函数，用于判断哪个值应该排在前面。

比较函数接收两个参数，如果第一个参数应该排在第二个参数前面，则函数返回负值。如果第一个参数应该排在第二个参数后面，则返回正值。如果参数不用变化位置，则返回0。

```js
function compare(v1, v2){
    if(v1 < v2){
        return -1
    }else if(v1 > v2){
        return 1
    }else{
        return 0
    }
}
/*
	等价
	function compare(v1, v2){
		return v1 - v2
	}
*/

let values = [0, 1, 15, 10, 5]
values.sort(compare)
console.log(values)  // [0, 1, 5, 10, 15]
```

### 6.2.11 操作方法

对于数组中的元素，我们有很多操作方法。接下来讲三种方法：**concat**()、**slice**()、**splice**()

#### concat()

**concat**()方法可以在**现有数组全部元素基础上创建一个新的数组**。它首先会创建一个当前数组的副本，然后再把它的参数添加到副本末尾，最后返回这个新构建的数组。

- 如果参数是一个或多个数组，**concat**()会把数组的每一项添加到结果数组。
- 如果参数不是数组，则直接把它们添加到结果数组末尾。

```js
let colors = ["red", "green", "blue"]
let colors2 = colors.concat("yellow", ["black", "brown"])

console.log(colors2)  // ["red", "green", "blue", "yellow", "black", "brown"]
```

打平数组参数的行为可以重写，方法是在参数数组上指定一个特殊的符号：`Symbol.isConcatSpreadable`。这个符号能够阻止 concat()打平参数数组。把这个值设置为 true 可以强制打平类数组对象。

#### slice()

**slice**()方法用于**创建一个包含原始数组中一个或多个元素的新数组**。**slice**()方法可以接收一个或两个参数：返回元素的开始索引和结束索引(不包含)，不过不提供第二个参数，则默认从开始索引取到末尾。

```js
let colors = ["red", "green", "blue"]
let colors2 = colors.slice(1)
let colors3 = colors.slice(1,2)
console.log(colors2)   // ["green", "blue"]
console.log(colors3)   // ["green"]
```

> 如果 slice()的参数有负值，那么就以数值长度加上这个负值的结果确定位置。比如，在包含 5 个元素的数组上调用 slice(-2,-1)，就相当于调用 slice(3,4)。如果结束位置小于开始位置，则返回空数组。

#### splice()

**splice**()方法可以改变数组本身，主要目的是在数组中间插入元素。它接收三个参数，第一个参数指定删除元素的开始位置，第二个参数指定删除元素的数量，第三个以及之后的参数指定在开始位置处要插入的元素。

- **删除**。需要给 splice()传 2 个参数：**要删除的第一个元素的位置**和要**删除的元素数量**。可以从数组中删除任意多个元素，比如 splice(0, 2)会删除前两个元素。

- **插入**。需要给 splice()传 3 个参数：**开始位置**、**0**（要删除的元素数量）和**要插入的元素**，可以在数组中指定的位置插入元素。第三个参数之后还可以传第四个、第五个参数，乃至任意多个要插入的元素。比如，splice(2, 0, "red", "green")会从数组位置 2 开始插入字符串"red"和"green"。

- **替换**。splice()在删除元素的同时可以在指定位置插入新元素，同样要传入 3 个参数：**开始位置**、**要删除元素的数量**和**要插入的任意多个元素**。要插入的元素数量不一定跟删除的元素数量一致。比如，splice(2, 1, "red", "green")会在位置 2 删除一个元素，然后从该位置开始向数组中插入"red"和"green"。

splice()方法始终返回这样一个数组

```js
let colors = ["red", "green", "blue"]
let removed = colors.splice(0, 1)    // 在位置0处删除1个元素
console.log(removed)    // "red"
console.log(colors)     // ["green", "blue"]

colors.splice(1, 0 ,"a", "b")    // 在位置1处删除0个元素，并插入两个元素
console.log(colors)    // ["green", "a", "b", "blue"]
```

### 6.2.12 搜索和位置方法

ECMAScript提供两类搜索数组的方法：按严格相等搜索和按断言函数搜索。

#### 1. 严格相等

ECMAScript提供了3个严格相等的搜索方法：**indexOf**()、**lastIndexOf**()和**includes**()（ES7）。

这三个方法都接收两个参数：**要查找的元素**和**一个可选的起始搜索位置**。 

**indexOf**()和**includes**()方法从数组前头开始向后搜索，而**lastIndexOf**()方法相反。

**indexOf**()和**lastIndexOf**()返回查找的元素在数组中的位置，如果没找到则返回-1.

**includes**()返回布尔值，表示是否找到一个指定元素匹配的项。

三个方法在比较时会使用全等（===）比较。

```js
let numbers = [1, 2, 3, 4, 5, 4, 3, 2, 1]

console.log(numbers.indexOf(4))     // 3
console.log(numbers.lastIndexOf(4)) // 5
console.log(numbers.includes(4))    // true

console.log(numbers.indexOf(4, 4))     // 5
console.log(numbers.lastIndexOf(4, 4)) // 3
console.log(numbers.includes(4, 7))    // false
```

#### 2. 断言函数

ECMAScript也允许按照定义的断言函数搜索数组，**每个索引都会调用这个函数**。断言函数的返回值决定了相应索引的元素是否被认为匹配

断言函数接收3个参数：**元素、索引和数组本身**。元素指的是数组中当前搜索的元素，索引是当前元素的索引，而数组就是正在搜索的数组。断言函数返回真值，表示是否匹配

**find**()和**findIndex**()方法使用了断言函数。

- **find()**：返回第一个匹配的元素
- **findIndex**()：返回第一个匹配元素的索引。

```js
const people = [ 
  { 
    name: "Matt", 
    age: 27 
  }, 
  { 
    name: "Nicholas", 
    age: 29 
  } 
]; 
alert(people.find((element, index, array) => element.age < 28)); 
// {name: "Matt", age: 27} 

alert(people.findIndex((element, index, array) => element.age < 28)); 
// 0
```

找到匹配项后，这两个方法都不再继续搜索

### 6.2.13 迭代方法

ECMAScript为数组定义了5个迭代方法。每个方法接收两个参数：**以每一项为参数运行的函数**、**以及可选的作为函数运行上下文的作用域对象**（影响函数中this的值）。传给每个方法的函数接收3个参数：**数组元素、元素索引和数组本身**。这5个迭代方法如下：

- **every()**：对数组每一项都运行传入的函数，如果对每一项都返回true，则这个方法返回true。
- **filter()**：对数组每一项都运行传入的函数，函数返回true的项会组成数组之后返回。
  - 非常适合从数组中筛选满足给定条件的元素
- **forEach()**：对数组每一项都运行传入的函数，无返回值。
  - forEach()方法相当于使用 for 循环遍历数组
- **map()**：对数组每一项都运行传入的函数，返回由每次函数调用的结果构成的数组。
  - 非常适合创建一个与原始数组元素一一对应的新数组
- **some()**：对数组每一项都运行传入的函数，如果有一项函数返回true，则这个方法返回true，否则返回false

```js
// every和some的例子
let numbers = [1, 2, 3, 4, 5, 4, 3, 2, 1]; 
let everyResult = numbers.every((item, index, array) => item > 2); 
alert(everyResult); // false 

let someResult = numbers.some((item, index, array) => item > 2); 
alert(someResult); // true

// filter的使用
let numbers = [1, 2, 3, 4, 5, 4, 3, 2, 1]; 
let filterResult = numbers.filter((item, index, array) => item > 2); 
alert(filterResult); // 3,4,5,4,3

// map的使用
let numbers = [1, 2, 3, 4, 5, 4, 3, 2, 1]; 
let mapResult = numbers.map((item, index, array) => item * 2); 
alert(mapResult); // 2,4,6,8,10,8,6,4,2

// forEach的使用
let numbers = [1, 2, 3, 4, 5, 4, 3, 2, 1]; 
numbers.forEach((item, index, array) => { 
  // 执行某些操作 
});
```

### 6.2.14 归并方法

ECMAScript为数组提供了两个归并方法：**reduce**()和**reduceRight**()。

**reduce**()：从数组第一项开始遍历到最后一项

**reduceRight**()：从最后一项开始遍历至第一项

这两个方法接收两个参数：

- 第一个是上一个归并值
- 第二个是当前项
- 第二个是当前项的索引
- 第一个是数组本身

**每轮循环的返回值都会作为下轮循环的初始值，最后一轮循环的返回值即最终的返回值**。

如果没有给这两个方法传入可选的第二个参数（作为归并起点值），则第一次迭代将从数组的第二项开始，因此传给归并函数的第一个参数是数组的第一项，第二个参数是数组的第二项

```js
let values = [1, 2, 3, 4, 5]; 
let sum = values.reduce((prev, cur, index, array) => prev + cur); 
alert(sum); // 15
```

如果不提供第二个参数，则默认第一次初始值为第一个元素，当前值则为第二个元素。

如果提供第二个参数，则默认第一次循环的初始值为第二个参数，当前值为第一个元素。

```js
let values = [1, 2, 3, 4, 5]; 
let sum = values.reduceRight(function(prev, cur, index, array){ 
 return prev + cur; 
}); 
alert(sum); // 15
```

**reduceRight**()和**reduce**()方法的差别只是方向相反一下。

## 6.3 定型数组（typed array）

这个部分了解(不重要)

定型数组（typed array）是 ECMAScript 新增的结构，目的是提升向原生库传输数据的效率。实际上，JavaScript 并没有“TypedArray”类型，它所指的其实是一种特殊的包含数值类型的数组。

- **ArrayBuffer** 是所有定型数组及视图引用的基本单位。ArrayBuffer()是一个普通的 JavaScript 构造函数，可用于在内存中分配特定数量的字节空间
- 允许你读写 ArrayBuffer 的视图是 **DataView**。这个视图专为文件 I/O 和网络 I/O 设计，其API 支持对缓冲数据的高度控制，但相比于其他类型的视图性能也差一些必须在对已有的 ArrayBuffer 读取或写入时才能创建 DataView 实例
  - 首先是要读或写的字节偏移量。可以看成 DataView 中的某种“地址”。
  - DataView 应该使用 ElementType 来实现 JavaScript 的 Number 类型到缓冲内二进制格式的转换。
  - 最后是内存中值的字节序。默认为大端字节序。
- **字节序**
  - 大端字节序也称为“网络字节序”，意思是最高有效位保存在第一个字节，而最低有效位保存在最后一个字节。
  - 小端字节序正好相反，即最低有效位保存在第一个字节，最高有效位保存在最后一个字节。

## 6.4 Map

作为ECMAScript 6的新增特性，Map是一种新的集合类型，为这门语言带来了**真正的键/值存储机制**。Map的大多数特性都可以通过Object类型实现，但二者之间还是存在一些细微的差别。

### 6.4.1 基本API

使用new关键字和Map构造函数可以创建一个空映射：

```js
const m = new Map()
```

如果想在创建的同时初始化实例，可以给Map构造函数传入一个**可迭代对象，需要包含键/值对数组**。

```js
const m1 = new Map([
    ["key1", "val1"],
    ["key2", "val2"],
    ["key3", "val3"]
])

console.log(m1.size)  // 3
```

可以使用**set**()方法添加键/值对。可以使用**get**()和**has**()进行查询，可以通过**size**属性获取映射中的键/值对的数量。使用**delete**()和**clear**()删除值。

```js
const m = new Map()

console.log(m.has("name"))   // false
console.log(m,size)          // 0

m.set("name", "Ryan")

console.log(m.has("name"))   // true
console.log(m.get("name"))   // "Ryan"

m.delete("name")
console.log(m.size)          // 0 
```

多个操作可以连缀

```js
const m = new Map().set("key1", "val1"); 
m.set("key2", "val2") .set("key3", "val3"); 
alert(m.size); // 3
```

> 与 Object 只能使用数值、字符串或符号作为键不同，Map 可以使用任何 JavaScript 数据类型作为键。Map 内部使用 SameValueZero 比较操作（ECMAScript 规范内部定义，语言中不能使用），基本上相当于使用严格对象相等的标准来检查键的匹配性。NaN与NaN认为是同一个值，-0 与 +0也认为是一个值。

### 6.4.2 顺序与迭代

> 与 Object 类型的一个主要差异是，**Map 实例会维护键值对的插入顺序**，因此可以根据插入顺序执行迭代操作

Map的默认迭代器返回**entries**()（或者`Symbol.iterator`属性，它引用**entries**()）方法取得的值，也就是以插入顺序生成[key, value]形式的数组。

```js
const m = new Map([
    ["key1", "val1"],
    ["key2", "val2"],
    ["key3", "val3"]
]);
alert(m.entries === m[Symbol.iterator]); // true 
for (let pair of m.entries()) {
    alert(pair);
}
// [key1,val1]
// [key2,val2]
// [key3,val3]

for (let pair of m[Symbol.iterator]()) {
    alert(pair);
}
// [key1,val1]
// [key2,val2]
// [key3,val3]

//因为 entries()是默认迭代器，所以可以直接对映射实例使用扩展操作，把映射转换为数组：
const m1 = new Map([
    ["key1", "val1"],
    ["key2", "val2"],
    ["key3", "val3"]
]);
console.log([...m1]); // [[key1,val1],[key2,val2],[key3,val3]]
```

如果不使用迭代器，而是使用回调方式，则可以调用映射的 `forEach(callback, opt_thisArg)`方法并传入回调，依次迭代每个键/值对。传入的回调接收可选的第二个参数，这个参数用于重写回调内部 this 的值：

```js
const m = new Map([ 
  ["key1", "val1"], 
  ["key2", "val2"], 
  ["key3", "val3"] 
]); 
m.forEach((val, key) => alert(`${key} -> ${val}`)); 
// key1 -> val1 
// key2 -> val2 
// key3 -> val3
```

**keys**()和**values**()分别返回以插入顺序生成键和值的迭代器。

```js
const m1 = new Map([
    ["key1", "val1"],
    ["key2", "val2"],
    ["key3", "val3"]
])

for(let key of m1.keys()){
    console.log(key)
}

// "key1"
// "key2"
// "key3"

for(let value of m1.values()){
    console.log(value)
}

// "val1"
// "val2"
// "val3"
```

键和值在迭代器遍历时是可以修改的，但映射内部的引用则无法修改。这并不妨碍修改作为键或值的对象内部的属性，因为这样并不影响它们在映射实例中的身份

> 简单来说就是字符串为键不能修改，但是若键是个对象，我们可以对其内部属性进行修改

```js
const m1 = new Map([ 
  ["key1", "val1"] 
]); 
// 作为键的字符串原始值是不能修改的
for (let key of m1.keys()) { 
  key = "newKey"; 
  alert(key); // newKey 
  alert(m1.get("key1")); // val1 
} 
const keyObj = {id: 1}; 
const m = new Map([ 
  [keyObj, "val1"] 
]); 
// 修改了作为键的对象的属性，但对象在映射内部仍然引用相同的值
for (let key of m.keys()) { 
  key.id = "newKey"; 
  alert(key); // {id: "newKey"} 
  alert(m.get(keyObj)); // val1 
} 
alert(keyObj); // {id: "newKey"}
```

### 6.4.3 选择object还是Map

- 内存占用
  - 给定固定大小的内存，Map 大约可以比 Object 多存储 50%的键/值对
- 插入性能
  - 如果代码涉及大量插入操作，那么显然 Map 的性能更佳。
-  查找速度
  - 如果代码涉及大量查找操作，那么某些情况下可能选择 Object 更好一些。
- 删除性能
  - 如果代码涉及大量删除操作，那么毫无疑问应该选择 Map。

>  如果代码涉及大量删除操作，选择Map，其它情况，Map与object差距不大，一般来说Map内存占用更少。

### 6.5 WeakMap

ECMAScript6新增的“弱映射”（WeakMap）是一种新的集合类型，为这门语言带来了增强的键/值对存储机制。WeakMap是Map的“兄弟”类型，其API也是Map的子集。WeakMap中的“weak”（弱），**描述的是JavaScript垃圾回收程序对待“弱映射”中键的方式。**

#### 6.5.1 基本API

使用new关键字实例化一个空的WeakMap:

```js
const wm = new WeakMap()
```

**弱映射中的键只能是`Object`或者继承自`Object`的类型**，尝试使用非对象设置键会抛出TypeError。值的类型没有限制。

```js
const wm = new WeakMap()

let obj = {}
console.log(wm.has(obj))   // false

wm.set(obj, "Ryan")

console.log(wm.has(obj))   // true
console.log(wm.get(obj))   // "Ryan"

m.delete(obj)
```

初始化之后可以使用 set()再添加键/值对，可以使用 get()和 has()查询，还可以使用 delete()删除

set()方法返回弱映射实例，因此可以把多个操作连缀起来

#### 6.5.2 弱键

**WeakMap中的键不属于正式的引用，不会阻止垃圾回收**。

原理：

```js
const wm = new WeakMap(); 
wm.set({}, "val");
```

set()方法初始化了一个新对象并将它用作一个字符串的键。因为没有指向这个对象的其他引用，所以当这行代码执行完成后，这个对象键就会被当作垃圾回收。然后，这个键/值对就从弱映射中消失了，使其成为一个空映射。在这个例子中，因为值也没有被引用，所以这对键/值被破坏以后，值本身也会成为垃圾回收的目标。

保持对象中有值就能保证键值对被引用中，不会被回收，除非我们主动将对象置空

```js
const wm = new WeakMap(); 
const container = { 
  key: {} 
}; 
wm.set(container.key, "val"); 
function removeReference() { 
  container.key = null; 
}
```

#### 6.5.3 不可迭代键

因为WeakMap中的键/值对任何时候都可能被销毁，**所以没必要提供迭代其键/值对的能力**。所以WeakMap不提供clear()这样一次性销毁所有键/值的方法。

#### 6.5.4 使用弱映射

了解即可。

- 弱映射造就了在 JavaScript 中实现真正私有变量的一种新方式。前提很明确：私有变量会存储在弱映射中，以对象实例为键，以私有成员的字典为值。
- 因为 WeakMap 实例不会妨碍垃圾回收，所以非常适合保存关联元数据。

## 6.6 Set

**ECMAScript6新增的Set是一种新集合类型**。Set在很多方面都像是加强的Map，这是因为它们的大多数API和行为都是共有的。

### 6.6.1 基本API

使用new关键字和Set构造函数创建一个空集合：

```js
const m = new Set()
```

可以给构造函数传入一个可迭代对象来初始化实例：

```js
const s1 = new Set(["val1", "val2", "val3"])
console.log(s1.size)   // 3
```

初始化之后，可以使用**add**()增加值，使用**has**()查询，通过**size**取得元素数量，以及使用**delete**()和**clear**()删除元素。

```js
const s1 = new Set(["val1", "val2", "val3"])
console.log(s1.size)   // 3
s1.add("val4")
s1.delete("val1")

console.log(s1)    // ["val2", "val3", "val4"]
```

Set可以包含任何值，但不允许出现重复的值，集合与Map类似，使用**SameValueZero**操作（NaN与NaN认为是同一个值，-0 与 +0也认为是一个值）。

```js
const s1 = new Set(["val1", "val2", "val3"])
s1.add("val3")

console.log(s1)    // ["val1", "val2", "val3"]
```

与严格相等一样，用作值的对象和其他“集合”类型在自己的内容或属性被修改时也不会改变

```js
const s = new Set(); 
const objVal = {}, 
 arrVal = []; 
s.add(objVal); 
s.add(arrVal); 
objVal.bar = "bar"; 
arrVal.push("bar"); 
alert(s.has(objVal)); // true 
alert(s.has(arrVal)); // true
```

add()和 delete()操作是幂等的。delete()返回一个布尔值，表示集合中是否存在要删除的值：

```js
const s = new Set(); 
s.add('foo'); 
alert(s.size); // 1 
s.add('foo'); 
alert(s.size); // 1 
// 集合里有这个值
alert(s.delete('foo')); // true 
// 集合里没有这个值
alert(s.delete('foo')); // false
```

### 6.6.2 顺序与迭代

**Set 会维护值插入时的顺序，因此支持按顺序迭代**

集合实例可以通过**values**()方法及其别名方法**keys**()（或者`Symbol.iterator`属性，它引用**values**()）取得迭代器（Iterator）：

```js
const s1 = new Set(["val1", "val2", "val3"])

console.log(s.values === s[Symbol.iterator])   // true
console.log(s.keys === s[Symbol.iterator])   // true

for(let value of s.values()){
    console.log(value)
}
// "val1"
// "val2"
// "val3"

for(let value of s[Symbol.iterator]()){
    console.log(value)
}
// "val1"
// "val2"
// "val3"
```

values()是默认迭代器，所以可以直接对集合实例使用扩展操作，把集合转换为数组

```js
const s = new Set(["val1", "val2", "val3"]); 
console.log([...s]); // ["val1", "val2", "val3"]
```

集合的**entries**()方法返回一个迭代器，可以按照插入顺序产生包含两个元素的数组，这两个元素是集合中每个值的重复出现：

```js
for(let pair of s.entries()){
    console.log(pair)
}
// ["val1", "val1"]
// ["val2", "val2"]
// ["val3", "val3"]
```

如果不使用迭代器，而是使用回调方式，则可以调用集合的 forEach()方法并传入回调，依次迭代每个键/值对。传入的回调接收可选的第二个参数，这个参数用于重写回调内部 this 的值：

```js
const s = new Set(["val1", "val2", "val3"]); 
s.forEach((val, dupVal) => alert(`${val} -> ${dupVal}`)); 
// val1 -> val1 
// val2 -> val2 
// val3 -> val3
```

## 6.7 WeakSet

WeakSet是Set的“兄弟”类型，其API也是Set的子集。

### 6.7.1 基本API

可以使用new关键字实例化一个空的WeakSet：

```js
const ws = new WeakSet()
```

**弱集合中的值只能是Object或者继承自Object的类型，尝试使用非对象设置值会抛出TypeError**。初始化之后可以使用 add()再添加新值，可以使用 has()查询，还可以使用 delete()删除

```js
const val1 = {id:1}
const val2 = {id:2}

const ws = new WeakSet([val1])

ws.has(val2)    // false
ws.add(val2)
ws.has(val2)    // true
```

### 6.7.2 弱值

**WeakSet中的值不属于正式的引用，不会阻止垃圾回收**。

### 6.7.3 不可迭代值

因为WeakSet中的值任何时候都可能被销毁，所以没必要提供迭代其值的能力，因此同样用不着像**clear**()这样一次性销毁所有值的方法。

## 6.8 迭代与扩展操作

4 种原生集合类型定义了默认迭代器:Array、所有定型数组、Map、Set

- 这意味着上述所有类型都支持**顺序迭代**，都可以传入 for-of 循环

- 所有这些类型都兼容扩展操作符
- 