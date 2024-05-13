# 第八章：对象、类与面向对象编程

ECMA-262将对象定义为**一组属性的无序集合**。可以把对象想象成一张散列表，内容就是一组键值对，值可以是数据或者函数。

## 8.1 理解对象

```js
// 使用new的方式进行创建
let person = new Object(); 
person.name = "Nicholas"; 
person.age = 29; 
person.job = "Software Engineer"; 
person.sayName = function() { 
     console.log(this.name); 
};

// 使用对象字面量的方式创建
let person = { 
    name: "Nicholas", 
    age: 29, 
    job: "Software Engineer", 
    sayName() { 
        console.log(this.name); 
    } 
};
```

### 8.1.1 属性的类型

ECMA-262使用一些**内部特性来描述属性的特征**。开发者不能在 JavaScript 中直接访问这些特性。为了将某个特性标识为内部特性，规范会用两个中括号把特性的名称括起来，比如[[Enumerable]]。

属性分两种：**数据属性**和**访问器属性**。

#### 1. 数据属性

数据属性包含一个**保存数据值的位置**。值会从这个位置读取，也会写入到这个位置。

数据属性有4个特性描述它们的行为

- [[Configurable]]：表示是否可以通过delete删除并重新定义，是否可以修改它的特性，以及是否可以把它改为访问器属性。
  - 默认情况下，所有直接定义在对象上的属性的这个特性都是 true
- [[Enumerable]]：表示属性是否可以通过for-in循环返回。
  - 默认情况下，所有直接定义在对象上的属性的这个特性都是 true，
- [[Writable]]：表示属性的值是否可以被修改。
  - 默认情况下，所有直接定义在对象上的属性的这个特性都是 true
- [[Value]]：包含属性实际的值。
  - 特性的默认值为 undefined

当属性显示添加到对象后，[[Configurable]]，[[Enumerable]]，[[Writable]]都会被设置为true，[[Value]]属性会被设置为指定的值，如：

```js
let person = {
    name: "attackonryan"
}

console.log(Object.getOwnPropertyDescriptor(person, 'name'))
// {value: "attackonryan", writable: true, enumerable: true, configurable: true}
```

要修改属性的默认特性，就必须使用**`Object.defineProperty()`**方法。这个方法接收3个参数：

- 要添加属性的对象
- 属性的名称
- 一个描述符对象。

描述符对象上的属性可以包含：**configurable**、**enumerable**、**writable**和**value**，与特性名一一对应。

```js
let person = {}
Object.defineProperty(person, 'name', {
    writable: false,
    value: "attackonryan"
})
console.log(person.name)  // "attackonryan"
person.name = "ryan"
console.log(person.name)  // "attackonryan"
```

**非严格模式下给只读的属性赋值会被忽略，但严格模式下会抛出错误**。

一个属性被定义为不可配置之后，就不能再变回可配置的了。再次调用 Object.defineProperty()并修改任何非 writable 属性会导致错误

```js
let person = {}
Object.defineProperty(person, 'name', {
    configurable: false,
    value: "attackonryan"
})

// 抛出错误
Object.defineProperty(person, 'name', {
    configurable: false,
    value: "attackonryan"
})
```

调用**Object.defineProperty**()时，**configurable**、**enumerable**、**writable**的值如果不指定，则默认为false。

#### 2. 访问器属性

访问器属性不包含数据值。相反，它们包含一个获取（`getter`）函数和一个设置（`setter`）函数，不过这两个函数不是必需的。

- 在读取访问器属性时，会调用`getter`函数，这个函数的责任就是返回一个有效的值。
- 在写入访问器属性时，会调用`setter`函数并传入新值，这个函数必须决定对数据做出什么修改。

访问器属性有4个特性描述它们的行为

- [[Configurable]]：表示是否可以通过delete删除并重新定义，是否可以修改它的特性，以及是否可以把它改为访问器属性。
  - 默认情况下，所有直接定义在对象上的属性的这个特性都是 true。
- [[Enumerable]]：表示属性是否可以通过for-in循环返回。
  - 默认情况下，所有直接定义在对象上的属性的这个特性都是 true。
- [[Get]]：获取函数，在读取属性时调用。
  - 默认值为 undefined
- [[Set]]：设置函数，在写入属性时调用。
  - 默认值为 undefined

访问器属性不能直接定义，必须使用**Object.defineProperty**()。

```js
var book = {
    _year: 2017,
    edition: 1
};

Object.defineProperty(book, "year", {
    get: function(){
        return this._year;
    },
    set: function(newValue){

        if (newValue > 2017) {
            this._year = newValue;
            this.edition += newValue - 2017;

        }
    }
});

book.year = 2018;
alert(book.edition);   //2
```

> 对象 book 有两个**默认属性**：year\_和 edition。
>
> year_中的下划线常用来表示该属性并不希望在对象方法的外部被访问。
>
> 另一个属性 year 被定义为一个**访问器属性**
>
> - 获取函数简单地返回 year_的值
> - 设置函数会做一些计算以决定正确的版本（edition）。
>
> 因此，把 year 属性修改为 2018 会导致 year_变成 2018，edition 变成 2。

这是访问器属性的**典型使用场景**，即**设置一个属性值会导致一些其他变化发生**。

获取函数和设置函数**不一定都要定义**

- 只定义`获取函数`意味着属性**只读**，尝试修改属性会被忽略，**严格模式下会抛出错误**。

- 只定义`设置函数`意味着**不能读取**，非严格模式下读取会返回undefined，**严格模式下会抛出错误**。

**注意：每个属性只能定义一种属性类型（数据属性 or 访问器属性）**

> 在 ECMAScript 5以前，开发者会使用两个非标准的访问创建访问器属性：`__defineGetter__()`和`__defineSetter__()`。

### 8.1.2 定义多个属性

使用**`Object.defineProperties()`**方法可以通过多个描述符一次性定义多个属性。

它接收两个参数：

- 要为之添加或修改属性的对象
- 描述符对象

属性与要修改的属性一一对应。

```js
var book = {};
        
Object.defineProperties(book, {
    _year: {
        value: 2017
    },

    edition: {
        value: 1
    },

    year: {            
        get: function(){
            return this._year;
        },

        set: function(newValue){
            if (newValue > 2017) {
                this._year = newValue;
                this.edition += newValue - 2017;
            }                  
        }            
    }        
});

book.year = 2018;
alert(book.edition);   //2
```

### 8.1.3 读取属性的特性

使用**`Object.getOwnPropertyDescriptor()`**方法可以取得指定属性的属性描述符。

- 方法接收两个参数：
  - 属性所在对象
  - 要取得其描述符的属性名

- 返回值是一个对象

```js
var book = {};
        
Object.defineProperties(book, {
    _year: {
        value: 2017
    },

    edition: {
        value: 1
    },

    year: {            
        get: function(){
            return this._year;
        },

        set: function(newValue){
            if (newValue > 2017) {
                this._year = newValue;
                this.edition += newValue - 2017;
            }                  
        }            
    }        
});

var descriptor = Object.getOwnPropertyDescriptor(book, "_year");
alert(descriptor.value);          //2017
alert(descriptor.configurable);   //false
alert(typeof descriptor.get);     //"undefined"

var descriptor = Object.getOwnPropertyDescriptor(book, "year");
alert(descriptor.value);          //undefined
alert(descriptor.enumerable);     //false
alert(typeof descriptor.get);     //"function"
```

ECMAScript 2017 新增，使用**`Object.getOwnPropertyDescriptors()`**方法可以取得指定对象的所有属性描述符。

这个方法实际上会在每个自有属性上调用 `Object.getOwnPropertyDescriptor()`并在一个新对象中返回它们

```js
let book = {};
Object.defineProperties(book, {
    year_: {
        value: 2017
    },
    edition: {
        value: 1
    },
    year: {
        get: function () {
            return this.year_;
        },
        set: function (newValue) {
            if (newValue > 2017) {
                this.year_ = newValue;
                this.edition += newValue - 2017;
            }
        }
    }
});
console.log(Object.getOwnPropertyDescriptors(book));
// {
//   edition: {
//     configurable: false,
//     enumerable: false,
//     value: 1,
//     writable: false
//   },
//   year: {
//     configurable: false,
//     enumerable: false,
//     get: f(),
//     set: f(newValue),
//   },
//   year_: {
//     configurable: false,
//     enumerable: false,
//     value: 2017,
//     writable: false
//   }
// }
```

### 8.1.4 合并对象

“**合并**”（merge）两个对象：就是把源对象所有的本地属性一起复制到目标对象上。

**ECMAScript 6新增**，**`Object.assign()`**方法接收**一个目标对象**和**一个或多个源对象**作为函数，将每个源对象中可枚举（`Object.propertyIsEnumerable()`返回true）且自有（`Object.hasOwnProperty()`返回true）属性复制到目标对象。 以字符串和符号为键的属性会被复制。

对每个符合条件的属性，这个方法会使用**源对象**上的`[[Get]]`取得属性的值，然后使用**目标对象**上的`[[Set]]`设置属性的值。

```js
let dest = {}
let src = {id: "src"}

let result = Object.assign(dest, src)

console.log(dest === result)   // true
console.log(dest !== src)      // true
console.log(result)            // {id: "src"}
console.log(dest)			   // {id: "src"}
```

**`Object.assign()`**执行的是**浅复制**，如果多个源对象有相同属性，则使用最后一个复制的值。

如果赋值期间出错，则操作会中止并退出，同时抛出错误。

`Object.assign()`没有“回滚”之前赋值的概念，因此它是一个尽力而为、可能只会完成部分复制的方法。

```js
let dest, src, result;
/**
* 错误处理
*/
dest = {};
src = {
    a: 'foo',
    get b() {
        // Object.assign()在调用这个获取函数时会抛出错误
        throw new Error();
    },
    c: 'bar'
};
try {
    Object.assign(dest, src);
} catch(e) {}

// Object.assign()没办法回滚已经完成的修改
// 因此在抛出错误之前，目标对象上已经完成的修改会继续存在：
console.log(dest); // { a: foo }
```

### 8.1.5 对象标识及相等判定

在 ECMAScript 6 之前，有些特殊情况即使是`===`操作符也无能为力：

```js
// 这些是===符合预期的情况
console.log(true === 1); // false 
console.log({} === {}); // false 
console.log("2" === 2); // false

// 这些情况在不同 JavaScript 引擎中表现不同，但仍被认为相等
console.log(+0 === -0); // true 
console.log(+0 === 0); // true 
console.log(-0 === 0); // true 

// 要确定 NaN 的相等性，必须使用极为讨厌的 isNaN() 
console.log(NaN === NaN); // false 
console.log(isNaN(NaN)); // true
```

**ECMAScript 6** 新增了 **`Object.is()`**，这个方法与`===`很像，但同时也考虑到了上述边界情形。这个方法必须接收两个参数：

```js
console.log(Object.is(true, 1)); // false 
console.log(Object.is({}, {})); // false 
console.log(Object.is("2", 2)); // false 

// 正确的 0、-0、+0 相等/不等判定
console.log(Object.is(+0, -0)); // false 
console.log(Object.is(+0, 0)); // true 
console.log(Object.is(-0, 0)); // false 

// 正确的 NaN 相等判定
console.log(Object.is(NaN, NaN)); // true
```

### 8.1.6 增强的对象语法

**ECMAScript 6 新增**  定义和操作对象新增了很多极其有用的语法糖特性

#### 属性值简写

在给对象添加变量的时候，开发者经常会发现**属性名**和**变量名**是一样的。简写属性名只要使用变量名（不用再写冒号）就会自动被解释为同名的属性键。如果没有找到同名变量，则会抛出 ReferenceError。

```js
let name = 'Matt'; 
let person = { 
  name: name 
}; 
console.log(person); // { name: 'Matt' }

// 等价于
let name = 'Matt'; 
let person = { 
  name 
}; 
console.log(person); // { name: 'Matt' }
```

代码压缩程序会在不同作用域间保留属性名，以防止找不到引用。

```js
function makePerson(name) {
    return {
        name
    };
}
let person = makePerson('Matt');
console.log(person.name); // Matt
```

#### 可计算属性

在引入可计算属性之前，如果想使用变量的值作为属性，那么必须先声明对象，然后使用中括号语法来添加属性。

```js
const nameKey = 'name';
const ageKey = 'age';
const jobKey = 'job';
let person = {};
person[nameKey] = 'Matt';
person[ageKey] = 27;
person[jobKey] = 'Software engineer';
console.log(person); // { name: 'Matt', age: 27, job: 'Software engineer' }
```

有了可计算属性，就可以在**对象字面量中完成动态属性赋值**

中括号包围的对象属性键告诉运行时将其作为 JavaScript 表达式而不是字符串来求值

```js
const nameKey = 'name'; 
const ageKey = 'age'; 
const jobKey = 'job'; 
let person = { 
    [nameKey]: 'Matt', 
    [ageKey]: 27, 
    [jobKey]: 'Software engineer' 
}; 
console.log(person); // { name: 'Matt', age: 27, job: 'Software engineer' }
```

因为被当作 JavaScript 表达式求值，所以可**计算属性本身可以是复杂的表达式，在实例化时再求值**

> 可计算属性表达式中抛出任何错误**都会中断对象创建**。如果计算属性的表达式有副作用，那就要小心了，因为**如果表达式抛出错误，那么之前完成的计算是不能回滚的。**

#### 简写方法名

在给对象定义方法时，通常都要写一个方法名、冒号，然后再引用一个匿名函数表达式

开发者要放弃给函数表达式命名（不过给作为方法的函数命名通常没什么用）。相应地，这样也可以明显缩短方法声明。

```js
let person = {
    sayName: function(name) {
        console.log(`My name is ${name}`);
    }
};
person.sayName('Matt'); // My name is Matt

// 等价于
let person1 = {
    sayName(name) {
        console.log(`My name is ${name}`);
    }
};
person1.sayName('Matt1'); // My name is Matt
```

### 8.1.7 对象解构

**ECMAScript 6 新增**了对象解构语法，可以在一条语句中使用嵌套数据实现一个或多个赋值操作

对象解构就是**使用与对象匹配的结构来实现对象属性赋值**

```js
// 不使用对象解构
let person = {
    name: 'Matt',
    age: 27
};
let personName = person.name, personAge = person.age;
console.log(personName); // Matt
console.log(personAge); // 27

// 使用对象解构
let person1 = {
    name: 'Matt',
    age: 27
};
let { name: personName1, age: personAge1 } = person1;
console.log(personName1); // Matt 
console.log(personAge1); // 27
```

使用解构，可以在一个类似对象字面量的结构中，声明多个变量，同时执行多个赋值操作。如果**想让变量直接使用属性的名称**，那么可以使用简写语法

```js
let person = { 
   name: 'Matt', 
   age: 27 
}; 
let { name, age } = person; 
console.log(name); // Matt 
console.log(age); // 27
```

解构在内部使用函数 ToObject()（不能在运行时环境中直接访问）把源数据结构转换为对象

这意味着在对象解构的上下文中，原始值会被当成对象。这也意味着（根据 `ToObject()`的定义），`null`和 `undefined` 不能被解构，否则会抛出错误。

```js
let { length } = 'foobar'; 
console.log(length); // 6 

let { constructor: c } = 4; 
console.log(c === Number); // true 

let { _ } = null; // TypeError 
let { _ } = undefined; // TypeError
```

解构并不要求变量必须在解构表达式中声明。不过，如果是**给事先声明的变量赋值**，则赋值表达式必须包含在一对括号中

```js
let personName, personAge; 
let person = { 
    name: 'Matt', 
    age: 27 
}; 
({name: personName, age: personAge} = person); 
console.log(personName, personAge); // Matt, 27
```

#### 嵌套解构

解构对于引用嵌套的属性或赋值目标没有限制。可以通过解构来复制对象属性

```js
// 可以通过解构来复制对象属性
let person = {
    name: 'Matt',
    age: 27,
    job: {
        title: 'Software engineer'
    }
};
let personCopy = {};
({
    name: personCopy.name,
    age: personCopy.age,
    job: personCopy.job
} = person);
// 因为一个对象的引用被赋值给 personCopy，所以修改
// person.job 对象的属性也会影响 personCopy
person.job.title = 'Hacker'
console.log(person);
// { name: 'Matt', age: 27, job: { title: 'Hacker' } }
console.log(personCopy);
// { name: 'Matt', age: 27, job: { title: 'Hacker' } }
```

#### 部分解构

涉及多个属性的解构赋值是**一个输出无关的顺序化操作**。如果一个解构表达式涉及多个赋值，**开始的赋值成功而后面的赋值出错，则整个解构赋值只会完成一部分**

```js
let person = {
    name: 'Matt',
    age: 27
};
let personName, personBar, personAge;
try {
    // person.foo 是 undefined，因此会抛出错误
    ({name: personName, foo: { bar: personBar }, age: personAge} = person);
} catch(e) {}
console.log(personName, personBar, personAge);
// Matt, undefined, undefined
```

#### 参数上下文匹配

在**函数参数列表中也可以进行解构赋值**。对参数的解构赋值**不会影响 arguments 对象**，但可以在**函数签名中声明在函数体内使用局部变量**

```js
let person = {
    name: 'Matt',
    age: 27
};
function printPerson(foo, {name, age}, bar) {
    console.log(arguments);
    console.log(name, age);
}
function printPerson2(foo, {name: personName, age: personAge}, bar) {
    console.log(arguments);
    console.log(personName, personAge);
}
printPerson('1st', person, '2nd');
// ['1st', { name: 'Matt', age: 27 }, '2nd'] 
// 'Matt', 27

printPerson2('1st', person, '2nd');
// ['1st', { name: 'Matt', age: 27 }, '2nd'] 
// 'Matt', 27
```

## 8.2 创建对象

### 8.2.1 概述

ECMAScript 5.1 并没有正式支持面向对象的结构，比如类或继承

ECMAScript 6 开始正式支持类和继承，ES6 的类都仅仅是封装了 ES5.1 构造函数加原型继承的语法糖而已

> 在介绍 ES6 的类之前，本书会循序渐进地介绍被类取代的那些底层概念

### 8.2.2 工厂模式

```js
function createPerson(name, age, job){
    var o = new Object();
    o.name = name;
    o.age = age;
    o.job = job;
    o.sayName = function(){
        alert(this.name);
    };    
    return o;
}

var person1 = createPerson("Nicholas", 29, "Software Engineer");
var person2 = createPerson("Greg", 27, "Doctor");

person1.sayName();   //"Nicholas"
person2.sayName();   //"Greg"
```

这种工厂模式虽然可以解决**创建多个类似对象的问题**，但没有解决**对象标识问题**（即新创建的对象是什么类型）。

### 8.2.3 构造函数模式

构造函数是用于创建特定类型对象的。当然也可以自定义构造函数，以函数的形式为自己的对象类型定义属性和方法

```js
function Person(name, age, job){
    this.name = name;
    this.age = age;
    this.job = job;
    this.sayName = function(){
        alert(this.name);
    };    
}

var person1 = new Person("Nicholas", 29, "Software Engineer");
var person2 = new Person("Greg", 27, "Doctor");

person1.sayName();   //"Nicholas"
person2.sayName();   //"Greg"

console.log(person1.constructor == Person); // true 
console.log(person2.constructor == Person); // true

console.log(person1 instanceof Object); // true 
console.log(person1 instanceof Person); // true 
console.log(person2 instanceof Object); // true 
console.log(person2 instanceof Person); // true
```

`Person()`跟 `createPerson()`的区别：

- 没有显式地创建对象
- 属性和方法直接赋值给了 this
- 没有 return

>  **构造函数**名称的首字母都是要大写的，**非构造函数**则以小写字母开头。有助于在 ECMAScript 中区分构造函数和普通函数

要创建 Person 的实例，应使用 new 操作符。以这种方式调用构造函数会执行如下操作

- 在内存中创建一个新对象。
- 这个新对象内部的[[Prototype]]特性被赋值为构造函数的 prototype 属性。
- 构造函数内部的 this 被赋值为这个新对象（即 this 指向新对象）。
- 执行构造函数内部的代码（给新对象添加属性）。
- 如果构造函数返回非空对象，则返回该对象；否则，返回刚创建的新对象。

**定义自定义构造函数可以确保实例被标识为特定类型**，相比于工厂模式，这是一个很大的好处

构造函数不一定要写成函数声明的形式。**赋值给变量的函数表达式也可以表示构造函数**

```js
let Person = function(name, age, job) { 
    this.name = name; 
    this.age = age; 
    this.job = job; 
    this.sayName = function() { 
        console.log(this.name); 
    }; 
} 

let person1 = new Person("Nicholas", 29, "Software Engineer"); 
let person2 = new Person("Greg", 27, "Doctor"); 

person1.sayName(); // Nicholas 
person2.sayName(); // Greg 

console.log(person1 instanceof Object); // true 
console.log(person1 instanceof Person); // true 
console.log(person2 instanceof Object); // true 
console.log(person2 instanceof Person); // true
```

在实例化时，如果不想传参数，那么构造函数后面的括号可加可不加。只要有 new 操作符，就可以调用相应的构造函数

```js
// 在不传递参数的时候等价
let person1 = new Person(); 
let person2 = new Person;
```

#### 构造函数也是函数

构造函数与普通函数唯一的区别就是调用方式不同

- 任何函数只要使用 new 操作符调用就是构造函数，而不使用 new 操作符调用的函数就是普通函数

```js
// 作为构造函数 
let person = new Person("Nicholas", 29, "Software Engineer"); 
person.sayName(); // "Nicholas" 

// 作为函数调用
Person("Greg", 27, "Doctor"); // 添加到 window 对象
window.sayName(); // "Greg" 

// 在另一个对象的作用域中调用
let o = new Object(); 
Person.call(o, "Kristen", 25, "Nurse"); 
o.sayName(); // "Kristen"
```

- 普通函数的调用方式，这时候没有使用 new 操作符调用 Person()，结果会将属性和方法添加到 `window `对象。
- 调用方式是通过 `call()`（或 `apply()`）调用函数，同时将特定对象指定为作用域。**这里的调用将对象 o 指定为 Person()内部的 this 值**，因此执行完函数代码后，所有属性和 sayName()方法都会添加到对象 o 上面

#### 构造函数的问题

构造函数的主要问题：其定义的方法会在每个实例上都创建一遍

person1 和 person2 都有名为 sayName()的方法，但这两个方法不是同一个 Function 实例。

```js
// 因此每次定义函数时，都会初始化一个对象
function Person(name, age, job){ 
    this.name = name; 
    this.age = age; 
    this.job = job; 
    this.sayName = new Function("console.log(this.name)"); // 逻辑等价
}

console.log(person1.sayName == person2.sayName); // false
```

this 对象可以把函数**与对象的绑定推迟到运行时**

```js
function Person(name, age, job){ 
    this.name = name; 
    this.age = age; 
    this.job = job; 
    this.sayName = sayName; 
}

function sayName() { 
    console.log(this.name); 
}

let person1 = new Person("Nicholas", 29, "Software Engineer"); 
let person2 = new Person("Greg", 27, "Doctor"); 

person1.sayName(); // Nicholas 
person2.sayName(); // Greg
```

这样虽然解决了相同逻辑的函数重复定义的问题，**但全局作用域也因此被搞乱了**，因为那个函数实际上只能在一个对象上调用

### 8.2.4 原型模式

**每个函数都会创建一个 prototype 属性**，这个属性是一个**对象**，包含应该由特定引用类型的实例**共享的属性和方法**。

这个对象就是通过调用构造函数创建的对象的原型

**使用原型对象的好处**：在它上面定义的属性和方法可以被**对象实例**共享。原来在构造函数中直接赋给对象实例的值，可以直接赋值给它们的原型

```js
function Person(){}
// 等价于 let Person = function() {};

Person.prototype.name = "Nicholas";
Person.prototype.age = 29;
Person.prototype.job = "Software Engineer";
Person.prototype.sayName = function(){
    alert(this.name);
};

var person1 = new Person();
person1.sayName();   //"Nicholas"

var person2 = new Person();
person2.sayName();   //"Nicholas"

alert(person1.sayName == person2.sayName);  //true
```

#### 理解原型(`重点`)

无论何时，只要创建一个函数，就会按照特定的规则为这个函数**创建一个 prototype 属性**（指向原型对象）。

默认情况下，**所有原型对象自动获得一个名为 constructor 的属性，指回与之关联的构造函数**。

对前面的例子而言，`Person.prototype.constructor` 指向 `Person`。然后，因构造函数而异，可能会给原型对象添加其他属性和方法。

在自定义构造函数时，原型对象默认只会获得 `constructor` 属性，**其他的所有方法都继承自Object**。

每次调用构造函数创建一个新实例，这个**实例的内部[[Prototype]]指针**就会被**赋值为构造函数的原型对象**。

脚本中没有访问这个[[Prototype]]特性的标准方式，**但 Firefox、Safari 和 Chrome会在每个对象上暴露`__proto__`属性**，**通过这个属性可以访问对象的原型**。在其他实现中，这个特性完全被隐藏了。

关键在于理解这一点：**实例与构造函数原型之间有直接的联系，但实例与构造函数之间没有**。

下面这个代码很重要(`五星级`)：

```js
/**
* 构造函数可以是函数表达式
* 也可以是函数声明，因此以下两种形式都可以：
* function Person() {}
* let Person = function() {}
*/
function Person() {}
/**
* 声明之后，构造函数就有了一个
* 与之关联的原型对象：
*/
console.log(typeof Person.prototype); // object
console.log(Person.prototype);
// {
//     constructor: f Person(),
//     __proto__: Object
// }
/**
* 如前所述，构造函数有一个 prototype 属性
* 引用其原型对象，而这个原型对象也有一个
* constructor 属性，引用这个构造函数
* 换句话说，两者循环引用：
*/
console.log(Person.prototype.constructor === Person); // true
/**
* 正常的原型链都会终止于 Object 的原型对象
* Object 原型的原型是 null
*/
console.log(Person.prototype.__proto__ === Object.prototype); // true
console.log(Person.prototype.__proto__.constructor === Object); // true
console.log(Person.prototype.__proto__.__proto__ === null); // true
console.log(Person.prototype.__proto__);
// {
//     constructor: f Object(),
//     toString: ...
//     hasOwnProperty: ...
//     isPrototypeOf: ...
//     ...
// }
let person1 = new Person(),
    person2 = new Person();
/**
* 构造函数、原型对象和实例
* 是 3 个完全不同的对象：
*/
console.log(person1 !== Person); // true
console.log(person1 !== Person.prototype); // true
console.log(Person.prototype !== Person); // true
/**
* 实例通过__proto__链接到原型对象，
* 它实际上指向隐藏特性[[Prototype]]
*
* 构造函数通过 prototype 属性链接到原型对象
*
* 实例与构造函数没有直接联系，与原型对象有直接联系
*/
console.log(person1.__proto__ === Person.prototype); // true
console.log(person1.__proto__.constructor === Person); // true
/**
* 同一个构造函数创建的两个实例
* 共享同一个原型对象：
*/
console.log(person1.__proto__ === person2.__proto__); // true
/**
* instanceof 检查实例的原型链中
* 是否包含指定构造函数的原型：
*/

console.log(person1 instanceof Person); // true
console.log(person1 instanceof Object); // true
console.log(Person.prototype instanceof Object); // true
```

![image-20240511141813202](https://cdn.jsdelivr.net/gh/Killer-89757/PicBed/images/2024%2F05%2Fimage-20240511141813202-661a0d.png)

- **`isPrototypeOf()`**

虽然不是所有实现都对外暴露了[[Prototype]]，但可以使用 `isPrototypeOf()` 方法确定两个对象之间的这种关系。

**`isPrototypeOf()`会在传入参数的[[Prototype]]指向调用它的对象时返回 true**

```js
console.log(Person.prototype.isPrototypeOf(person1)); // true 
console.log(Person.prototype.isPrototypeOf(person2)); // true
```

- **`Object.getPrototypeOf()`**

ECMAScript 的 Object 类型有一个方法叫 `Object.getPrototypeOf()`，返回参数的内部特性[[Prototype]]的值

 `Object.getPrototypeOf()` 返回的对象就是传入对象的原型对象

```js
console.log(Object.getPrototypeOf(person1) == Person.prototype); // true 
console.log(Object.getPrototypeOf(person1).name);                // "Nicholas"
```

- **`setPrototypeOf()`**

可以向实例的私有特性[[Prototype]]写入一个新值。

```js
let biped = { 
    numLegs: 2 
}; 
let person = { 
    name: 'Matt' 
}; 
Object.setPrototypeOf(person, biped); 
console.log(person.name); // Matt 
console.log(person.numLegs); // 2 
console.log(Object.getPrototypeOf(person) === biped); // true
```

> `Object.setPrototypeOf()` 可能会严重影响代码性能

为避免使用 `Object.setPrototypeOf()`可能造成的性能下降，可以通过 `Object.create()`来创建一个新对象，同时为其指定原型

```js
let biped = { 
    numLegs: 2 
}; 
let person = Object.create(biped); 
person.name = 'Matt'; 
console.log(person.name); // Matt 
console.log(person.numLegs); // 2 
console.log(Object.getPrototypeOf(person) === biped); // true
```

#### 原型层级

在通过对象访问属性时，会按照这个属性的名称开始搜索。搜索开始于对象实例本身。如果在这个实例上发现了给定的名称，则返回该名称对应的值。如果没有找到这个属性，则搜索会沿着指针进入原型对象，然后在原型对象上找到属性后，再返回对应的值。

这就是**原型用于在多个对象实例间共享属性和方法的原理**。

前面提到的 constructor 属性只存在于原型对象，因此通过实例对象也是可以访问到的

**虽然可以通过实例读取原型对象上的值，但不可能通过实例重写这些值。**

如果在实例上添加了一个与原型对象中同名的属性，那就会在实例上创建这个属性，这个属性会遮住原型对象上的属性。

```js
function Person(){}
        
Person.prototype.name = "Nicholas";
Person.prototype.age = 29;
Person.prototype.job = "Software Engineer";
Person.prototype.sayName = function(){
    alert(this.name);
};

var person1 = new Person();
var person2 = new Person();

person1.name = "Greg";
alert(person1.name);   //"Greg"  from instance
alert(person2.name);   //"Nicholas"  from prototype
```

只要给对象实例添加一个属性，这个属性就会**遮蔽**（shadow）原型对象上的同名属性，也就是虽然不会修改它，但会屏蔽对它的访问。

即使在实例上把这个属性设置为 null，也不会恢复它和原型的联系。

不过，使用 delete 操作符可以完全删除实例上的这个属性，从而让标识符解析过程能够继续搜索原型对象。

`hasOwnProperty()`方法用于**确定某个属性是在实例上还是在原型对象**上。这个方法是继承自 Object的，**会在属性存在于调用它的对象实例上时返回 true**

```js
function Person() {} 

Person.prototype.name = "Nicholas";
Person.prototype.age = 29; 
Person.prototype.job = "Software Engineer"; 
Person.prototype.sayName = function() { 
    console.log(this.name); 
};

let person1 = new Person(); 
let person2 = new Person(); 
console.log(person1.hasOwnProperty("name")); // false 

person1.name = "Greg"; 
console.log(person1.name); // "Greg"，来自实例
console.log(person1.hasOwnProperty("name")); // true 

console.log(person2.name); // "Nicholas"，来自原型
console.log(person2.hasOwnProperty("name")); // false 

delete person1.name; 
console.log(person1.name); // "Nicholas"，来自原型
console.log(person1.hasOwnProperty("name")); // false
```

![image-20240513084940129](https://cdn.jsdelivr.net/gh/Killer-89757/PicBed/images/2024%2F05%2Fimage-20240513084940129-79e889.png)

> ECMAScript 的 `Object.getOwnPropertyDescriptor()`方法只对实例属性有效。要取得原型属性的描述符，就必须直接在原型对象上调用 `Object.getOwnPropertyDescriptor()`

#### 原型和 **in** 操作符

有两种方式使用 in 操作符：

- 单独使用
  - in 操作符会在可以**通过对象访问指定属性**时返回 true，**无论该属性是在实例上还是在原型上**
- 在 for-in 循环中使用
  - 在 for-in 循环中使用 in 操作符时，可以**通过对象访问且可以被枚举的属性都会返回**，包括`实例属性`和`原型属性`。遮蔽原型中不可枚举（[[Enumerable]]特性被设置为 false）属性的实例属性也会在 for-in 循环中返回，**因为默认情况下开发者定义的属性都是可枚举的**。

```js
function Person(){}
        
Person.prototype.name = "Nicholas";
Person.prototype.age = 29;
Person.prototype.job = "Software Engineer";
Person.prototype.sayName = function(){
    alert(this.name);
};

var person1 = new Person();
var person2 = new Person();

alert(person1.hasOwnProperty("name"));  //false
alert("name" in person1);  //true

person1.name = "Greg";
alert(person1.name);   //"Greg"  from instance
alert(person1.hasOwnProperty("name"));  //true
alert("name" in person1);  //true

alert(person2.name);   //"Nicholas"  from prototype
alert(person2.hasOwnProperty("name"));  //false
alert("name" in person2);  //true

delete person1.name;
alert(person1.name);   //"Nicholas"  from the prototype
alert(person1.hasOwnProperty("name"));  //false
alert("name" in person1);  //true
```

同时使用 `hasOwnProperty()`和 `in` 操作符

```js
// 只要 in 操作符返回 true 且 hasOwnProperty()返回 false，就说明该属性是一个原型属性
function hasPrototypeProperty(object, name){ 
    return !object.hasOwnProperty(name) && (name in object); 
}
```

**要获得对象上所有可枚举的`实例属性`**，可以使用 `Object.keys()`方法。

参数：一个对象

返回值：包含该对象所有可枚举属性名称的字符串数组

```js
function Person() {} 

Person.prototype.name = "Nicholas"; 
Person.prototype.age = 29; 
Person.prototype.job = "Software Engineer"; 
Person.prototype.sayName = function() { 
    console.log(this.name); 
}; 

let keys = Object.keys(Person.prototype); 
console.log(keys); // "name,age,job,sayName" 

let p1 = new Person(); 
p1.name = "Rob"; 
p1.age = 31; 

let p1keys = Object.keys(p1); 
console.log(p1keys); // "[name,age]"
```

如果想列出所有实例属性，无论是否可以枚举，都可以使用 `Object.getOwnPropertyNames()`：

```js
let keys = Object.getOwnPropertyNames(Person.prototype); 
console.log(keys); // "[constructor,name,age,job,sayName]"
```

 ECMAScript 6 新增符号类型之后，以符号为键的属性没有名称的概念。`Object.getOwnPropertySymbols()`方法针对符号

```js
let k1 = Symbol('k1'), k2 = Symbol('k2');

let o = { 
    [k1]: 'k1', 
    [k2]: 'k2' 
}; 
console.log(Object.getOwnPropertySymbols(o));    // [Symbol(k1), Symbol(k2)]
```

#### 属性枚举顺序

`for-in` 循环、`Object.keys()`、`Object.getOwnPropertyNames()`、`Object.getOwnPropertySymbols()`以及 `Object.assign()`在属性枚举顺序方面有很大区别

- for-in 循环和 Object.keys()的枚举顺序是**不确定**的
  - 取决于 JavaScript 引擎，可能因浏览器而异
- `Object.getOwnPropertyNames()`、`Object.getOwnPropertySymbols()`和 `Object.assign()`的枚举顺序是**确定性**的。
  - 先以升序枚举数值键，然后以插入顺序枚举字符串和符号键

```js
let k1 = Symbol('k1'), k2 = Symbol('k2'); 

let o = { 
    1: 1, 
    first: 'first', 
    [k1]: 'sym2', 
    second: 'second', 
    0: 0 
}; 

o[k2] = 'sym2'; 
o[3] = 3; 
o.third = 'third'; 
o[2] = 2; 

console.log(Object.getOwnPropertyNames(o));  // ["0", "1", "2", "3", "first", "second", "third"] 
console.log(Object.getOwnPropertySymbols(o));  // [Symbol(k1), Symbol(k2)]
```

### 8.2.5 对象迭代

**ECMAScript 2017** 新增了两个静态方法，用于将对象内容转换为序列化的——更重要的是可迭代的——格式

- `Object.values()`
  - 参数：一个对象
  - 返回值：对象值的数组
- `Object.entries()`
  - 参数：一个对象
  - 返回值：返回键/值对的数组

> 重点： 
>
> - 非字符串属性会被转换为字符串输出。
> - 这两个方法执行对象的浅复制
> - 符号属性会被忽略

```js
const o = {
    foo: 'bar',
    baz: 1,
    qux: {}
};
console.log(Object.values(o));  // ["bar", 1, {}]
console.log(Object.entries((o)));  // [["foo", "bar"], ["baz", 1], ["qux", {}]]

// 非字符串属性会被转换为字符串输出
// 这两个方法执行对象的浅复制
const o1 = {
    qux: {}
};
console.log(Object.values(o1)[0] === o1.qux);
// true
console.log(Object.entries(o1)[0][1] === o1.qux);
// true

// 符号属性会被忽略
const sym = Symbol();
const o2 = {
    [sym]: 'foo'
};
console.log(Object.values(o2));
// [] 
console.log(Object.entries((o2)));
// []
```

#### 其他原型语法

```js
function Person() {} 
Person.prototype = {
    name: "Nicholas", 
    age: 29, 
    job: "Software Engineer", 
    sayName() { 
        console.log(this.name); 
    } 
};

let friend = new Person(); 
console.log(friend instanceof Object); // true 
console.log(friend instanceof Person); // true

console.log(friend.constructor == Person); // false 
console.log(friend.constructor == Object); // true
```

这样重写之后，**Person.prototype 的 constructor 属性就不指向 Person了**。

在创建函数时，也会创建它的 prototype 对象，同时会自动给这个原型的 constructor 属性赋值。

而上面的写法**完全重写了默认的 prototype 对象**，因此**其 constructor 属性也指向了完全不同的新对象（Object 构造函数）**，不再指向原来的构造函数。

>  **虽然 instanceof 操作符还能可靠地返回值，但我们不能再依靠 constructor 属性来识别类型了**

上面的改进方式：

```js
function Person() {} 
Person.prototype = { 
    constructor: Person, 
    name: "Nicholas", 
    age: 29, 
    job: "Software Engineer", 
    sayName() { 
        console.log(this.name); 
    } 
};
```

以这种方式恢复 **constructor 属性会创建一个[[Enumerable]]为 true 的属性**。而原生 constructor 属性默认是不可枚举的

```js
function Person() {} 
Person.prototype = { 
    name: "Nicholas", 
    age: 29, 
    job: "Software Engineer", 
    sayName() { 
        console.log(this.name); 
    } 
}; 
// 恢复 constructor 属性
Object.defineProperty(Person.prototype, "constructor", { 
    enumerable: false, 
    value: Person 
});
```

#### 原型的动态性

因为从原型上搜索值的过程是动态的，所以**即使实例在修改原型之前已经存在，任何时候对原型对象所做的修改也会在实例上反映出来**。

因为实例和原型之间的链接就是**简单的指针**，而不是保存的副本，所以会在原型上找到 sayHi 属性并返回这个属性保存的函数。

实例的[[Prototype]]指针是在调用构造函数时自动赋值的，这个指针即使把原型修改为不同的对象也不会变。**重写整个原型会切断最初原型与构造函数的联系，但实例引用的仍然是最初的原型**。记住，实例只有指向原型的指针，没有指向构造函数的指针

```js
function Person() {} 
let friend = new Person(); 
Person.prototype = { 
    constructor: Person, 
    name: "Nicholas", 
    age: 29, 
    job: "Software Engineer", 
    sayName() { 
        console.log(this.name); 
    } 
};

friend.sayName(); // 错误
```

![image-20240513093238080](https://cdn.jsdelivr.net/gh/Killer-89757/PicBed/images/2024%2F05%2Fimage-20240513093238080-f2618d.png)

**重写构造函数上的原型之后再创建的实例才会引用新的原型**。**而在此之前创建的实例仍然会引用最初的原型**。

#### 原生对象原型

原型模式之所以重要，不仅体现在自定义类型上，而且还因为它也是实现所有原生引用类型的模式

所有原生引用类型的构造函数（包括 `Object`、`Array`、`String` 等）都在原型上定义了实例方法

通过原生对象的原型可以取得所有默认方法的引用，也可以给原生类型的实例定义新的方法。

```js
alert(typeof Array.prototype.sort);         //"function"
alert(typeof String.prototype.substring);   //"function"

String.prototype.startsWith = function (text) {
    return this.indexOf(text) == 0;
};

var msg = "Hello world!";
alert(msg.startsWith("Hello"));   //true
```

> 尽管可以这么做，但并不推荐在产品环境中修改原生对象原型

#### 原型的问题

- 原型模式也不是没有问题。首先，它弱化了向构造函数传递初始化参数的能力，会导致所有实例默认都取得相同的属性值。
- 原型的最主要`问题`源自它的**共享特性**
- 简化来说就是：一个对象修改了自己的对象类，数组类的属性，在另一个对象中也能访问的到。

```js
function Person(){}

Person.prototype = {
    constructor: Person,
    name : "Nicholas",
    age : 29,
    job : "Software Engineer",
    friends : ["Shelby", "Court"],
    sayName : function () {
        alert(this.name);
    }
};

var person1 = new Person();
var person2 = new Person();

person1.friends.push("Van");

alert(person1.friends);    //"Shelby,Court,Van"
alert(person2.friends);    //"Shelby,Court,Van"
alert(person1.friends === person2.friends);  //true
```

## 8.3 继承

很多面向对象语言都支持两种继承：`接口继承`和`实现继承`

**实现继承是 ECMAScript 唯一支持的继承方式**，而这主要是通过原型链实现的

### 8.3.1 原型链

ECMA-262 把原型链定义为 ECMAScript 的主要继承方式。其基本思想就是通过原型继承多个引用类型的属性和方法。

原型链的基本构想：每个构造函数都有一个原型对象，原型有一个属性指回构造函数，而实例有一个内部指针指向原型。如果原型是另一个类型的实例呢？那就意味着这个原型本身有一个内部指针指向另一个原型，相应地另一个原型也有一个指针指向另一个构造函数。这样就在实例和原型之间构造了一条原型链

```js
function SuperType(){
    this.property = true;
}

SuperType.prototype.getSuperValue = function(){
    return this.property;
};

function SubType(){
    this.subproperty = false;
}

//inherit from SuperType
SubType.prototype = new SuperType();

SubType.prototype.getSubValue = function (){
    return this.subproperty;
};

var instance = new SubType();
alert(instance.getSuperValue());   //true

alert(instance instanceof Object);      //true
alert(instance instanceof SuperType);   //true
alert(instance instanceof SubType);     //true

alert(Object.prototype.isPrototypeOf(instance));    //true
alert(SuperType.prototype.isPrototypeOf(instance)); //true
alert(SubType.prototype.isPrototypeOf(instance));   //true
```

![image-20240513095457763](https://cdn.jsdelivr.net/gh/Killer-89757/PicBed/images/2024%2F05%2Fimage-20240513095457763-d20a6c.png)

> - 这两个类型的主要区别是 **`SubType` 通过创建 `SuperType` 的实例并将其赋值给自己的原型 `SubTtype.prototype` 实现了对 `SuperType` 的继承**。
> - 过程(`绝对核心`)
>   - `SubType.prototype = new SuperType();` 这个赋值重写了 `SubType` 最初的原型，将其替换为 `SuperType` 的实例。这意味着 `SuperType` 实例可以访问的所有属性和方法也会存在于 `SubType.prototype`，**等价于`SubType.prototype` 指向 `SuperType.prototype`**
>   - 实现继承的关键，是 `SubType` 没有使用默认原型，而是将其替换成了一个新的对象。这个新的对象恰好是 `SuperType` 的实例
>   - `SubType` 的实例不仅能从 `SuperType` 的实例中继承属性和方法，而且还与 `SuperType 的原型`挂上了钩
>   - 由于 `SubType.prototype 的 constructor` 属性被重写为指向 `SuperType`，所以 `instance.constructor` 也指向 `SuperType`

**原型链扩展了前面描述的原型搜索机制**。我们知道，在读取实例上的属性时，首先会在实例上搜索这个属性。如果没找到，则会继承搜索实例的原型。

对属性和方法的搜索会一直持续到原型链的末端。

#### 默认原型

默认情况下，**所有引用类型都继承自 Object**，这也是通过原型链实现的

任何函数的默认原型都是一个 Object 的实例，这意味着这个实例有一个内部指针指向 `Object.prototype`

这也是为什么自定义类型能够继承包括 `toString()`、`valueOf()`在内的所有默认方法的原因。

![image-20240513100841928](https://cdn.jsdelivr.net/gh/Killer-89757/PicBed/images/2024%2F05%2Fimage-20240513100841928-9f901c.png)

#### 原型与继承关系

原型与实例的关系可以通过两种方式来确定。

- 使用 `instanceof` 操作符，如果一个实例的原型链中出现过相应的构造函数，则 `instanceof` 返回 true。

- 确定这种关系的第二种方式是使用 `isPrototypeOf()`方法。原型链中的每个原型都可以调用这个方法，只要原型链中包含这个原型，这个方法就返回 true

#### 关于方法

子类有时候需要覆盖父类的方法，或者增加父类没有的方法。为此，这些方法必须在原型赋值之后再添加到原型上

```js
function SuperType(){
    this.property = true;
}

SuperType.prototype.getSuperValue = function(){
    return this.property;
};

function SubType(){
    this.subproperty = false;
}

//inherit from SuperType
SubType.prototype = new SuperType();

//new method
SubType.prototype.getSubValue = function (){
    return this.subproperty;
};

//override existing method
SubType.prototype.getSuperValue = function (){
    return false;
};

var instance = new SubType();
alert(instance.getSuperValue());   //false
```

以对象字面量方式创建原型方法会破坏之前的原型链，因为这相当于重写了原型链

```js
function SuperType(){
    this.property = true;
}

SuperType.prototype.getSuperValue = function(){
    return this.property;
};

function SubType(){
    this.subproperty = false;
}

//inherit from SuperType
SubType.prototype = new SuperType();

//try to add new methods  this nullifies the previous line
SubType.prototype = {
    getSubValue : function (){
        return this.subproperty;
    },

    someOtherMethod : function (){
        return false;
    }
};

var instance = new SubType();
alert(instance.getSuperValue());   //error!
```

子类的原型在被赋值为 SuperType 的实例后，又被一个对象字面量覆盖了。覆盖后的原型是一个 Object 的实例，而不再是 SuperType 的实例。因此之前的原型链就断了

#### 原型链的问题

主要问题出现在**原型中包含的引用值会在所有实例间共享**

```js
function SuperType(){
    this.colors = ["red", "blue", "green"];
}

function SubType(){}

//inherit from SuperType
SubType.prototype = new SuperType();

var instance1 = new SubType();
instance1.colors.push("black");
alert(instance1.colors);    //"red,blue,green,black"

var instance2 = new SubType();
alert(instance2.colors);    //"red,blue,green,black"
```

原型链的第二个问题是，**子类型在实例化时不能给父类型的构造函数传参**

### 8.3.2 盗用构造函数

这种技术有时也称作`对象伪装`或`经典继承`

基本思路很简单：**在子类构造函数中调用父类构造函数**。因为毕竟函数就是在特定上下文中执行代码的简单对象，**所以可以使用 `apply()` 和  `call()` 方法以新创建的对象为上下文执行构造函数**。

```js
function SuperType(){
    this.colors = ["red", "blue", "green"];
}

function SubType(){  
    //inherit from SuperType
    SuperType.call(this);
}

// 实际上创建 instance1 就是调用 SuperType.call(this); 等价于在实例上增加 instance1.colors = ["red", "blue", "green"];
// 实际上创建 instance2 就是调用 SuperType.call(this); 等价于在实例上增加 instance2.colors = ["red", "blue", "green"];
// 所以实例属性不是在指向原型属性了，而是创建了自己的一份属性数据
var instance1 = new SubType();
instance1.colors.push("black");
alert(instance1.colors);    //"red,blue,green,black"

var instance2 = new SubType();
alert(instance2.colors);    //"red,blue,green"
```

#### 传递参数

相比于使用原型链，盗用构造函数的一个优点就是可以在**子类构造函数中向父类构造函数传参**。

```js
function SuperType(name){
    this.name = name;
}

function SubType(){  
    //inherit from SuperType passing in an argument
    SuperType.call(this, "Nicholas");

    //instance property
    this.age = 29;
}

var instance = new SubType();
alert(instance.name);    //"Nicholas";
alert(instance.age);     //29
```

#### 盗用构造函数的问题

盗用构造函数的主要缺点：构造函数模式自定义类型的问题：必须在构造函数中定义方法，因此函数不能重用。

子类也不能访问父类原型上定义的方法，因此所有类型只能使用构造函数模式。

### 8.3.3 组合继承

（有时候也叫`伪经典继承`）综合了原型链和盗用构造函数。

本的思路是使用原型链继承原型上的属性和方法，而通过盗用构造函数继承实例属性。

这样既可以把方法定义在原型上以实现重用，又可以让每个实例都有自己的属性。

```js
function SuperType(name){
    this.name = name;
    this.colors = ["red", "blue", "green"];
}

SuperType.prototype.sayName = function(){
    alert(this.name);
};

function SubType(name, age){  
    SuperType.call(this, name);

    this.age = age;
}

SubType.prototype = new SuperType();

SubType.prototype.sayAge = function(){
    alert(this.age);
};

var instance1 = new SubType("Nicholas", 29);
instance1.colors.push("black");
alert(instance1.colors);  //"red,blue,green,black"
instance1.sayName();      //"Nicholas";
instance1.sayAge();       //29


var instance2 = new SubType("Greg", 27);
alert(instance2.colors);  //"red,blue,green"
instance2.sayName();      //"Greg";
instance2.sayAge();       //27
```

**组合继承弥补了原型链和盗用构造函数的不足，是 JavaScript 中使用最多的继承模式**。

而且组合继承也保留了 `instanceof` 操作符和 `isPrototypeOf()` 方法识别合成对象的能力。

### 8.3.4 原型式继承

**道格拉斯·克洛克福德**：他的出发点是即使不自定义类型也可以通过原型实现对象之间的信息共享

```js
// 其实就是闭包的思想
function object(o) { 
    function F() {} 
    F.prototype = o; 
    return new F(); 
}
```

这个 `object()` 函数会创建一个临时构造函数，将传入的对象赋值给这个构造函数的原型，然后返回这个临时类型的一个实例。

**本质上，object()是对传入的对象执行了一次浅复制**

```js
function object(o){
    function F(){}
    F.prototype = o;
    return new F();
}

var person = {
    name: "Nicholas",
    friends: ["Shelby", "Court", "Van"]
};

var anotherPerson = object(person);
anotherPerson.name = "Greg";
anotherPerson.friends.push("Rob");

var yetAnotherPerson = object(person);
yetAnotherPerson.name = "Linda";
yetAnotherPerson.friends.push("Barbie");

alert(person.friends);   //"Shelby,Court,Van,Rob,Barbie"
```

原型式继承**适用于**这种情况：你有一个对象，想在它的基础上再创建一个新对象。你需要把这个对象先传给 object()，然后再对返回的对象进行适当修改。

**ECMAScript 5** 通过增加 `Object.create()`方法将原型式继承的概念规范化了。

- 两个参数：
  - 作为新对象原型的对象，
  - 以及给新对象定义额外属性的对象（第二个可选）。
- 在只有一个参数时，`Object.create()`与这里的 `object()`方法效果相同

```js
let person = { 
    name: "Nicholas", 
    friends: ["Shelby", "Court", "Van"] 
}; 
let anotherPerson = Object.create(person, { 
    name: { 
        value: "Greg" 
    } 
});

console.log(anotherPerson.name); // "Greg"
```

原型式继承非常适合**不需要单独创建构造函数**，但**仍然需要在对象间共享信息的场合**。

### 8.3.5 寄生式继承

与原型式继承比较接近的一种继承方式是**寄生式继承**（parasitic inheritance），也是 Crockford 首倡的一种模式

```js
function createAnother(original){ 
    let clone = object(original); // 通过调用函数创建一个新对象
    clone.sayHi = function() { // 以某种方式增强这个对象
        console.log("hi"); 
    }; 
    return clone; // 返回这个对象
}
```

- `createAnother()`函数接收一个参数，就是新对象的基准对象。这个对象 `original`会被传给 `object()`函数，然后将返回的新对象赋值给 `clone`

```js
let person = { 
    name: "Nicholas", 
    friends: ["Shelby", "Court", "Van"] 
};

let anotherPerson = createAnother(person); 
anotherPerson.sayHi(); // "hi"
```

寄生式继承同样适合主要关注对象，而不在乎类型和构造函数的场景

### 8.3.6 寄生式组合继承

组合继承其实也存在效率问题。

- 最主要的效率问题就是父类构造函数始终会被调用两次：
  - 一次在是创建子类原型时调用
  - 一次是在子类构造函数中调用

```js
function SuperType(name) { 
    this.name = name; 
    this.colors = ["red", "blue", "green"]; 
} 

SuperType.prototype.sayName = function() { 
    console.log(this.name); 
}; 

function SubType(name, age){ 
    SuperType.call(this, name); // 第二次调用 SuperType() 
    this.age = age; 
} 

SubType.prototype = new SuperType(); // 第一次调用 SuperType() 
SubType.prototype.constructor = SubType; 
SubType.prototype.sayAge = function() { 
    console.log(this.age); 
};
```

![image-20240513105426559](https://cdn.jsdelivr.net/gh/Killer-89757/PicBed/images/2024%2F05%2Fimage-20240513105426559-f5283d.png)

![image-20240513105437117](https://cdn.jsdelivr.net/gh/Killer-89757/PicBed/images/2024%2F05%2Fimage-20240513105437117-7b5621.png)



有两组 `name` 和 `colors` 属性：一组在实例上，另一组在 `SubType` 的原型上。这是调用两次 `SuperType` 构造函数的结果

寄生式组合继承通过盗用构造函数继承属性，但使用混合式原型链继承方法

**基本思路**：不通过调用父类构造函数给子类原型赋值，而是取得父类原型的一个副本

```js
function inheritPrototype(subType, superType) { 
    let prototype = object(superType.prototype); // 创建对象
    prototype.constructor = subType; // 增强对象 
    subType.prototype = prototype; // 赋值对象
}
```

这个 inheritPrototype()函数实现了寄生式组合继承的核心逻辑。

这个函数接收两个参数：

- 子类构造函数
- 父类构造函数

在这个函数内部，第一步是创建父类原型的一个副本

```js
function object(o){
    function F(){}
    F.prototype = o;
    return new F();
}

function inheritPrototype(subType, superType) { 
    let prototype = object(superType.prototype); // 创建对象
    prototype.constructor = subType; // 增强对象 
    subType.prototype = prototype; // 赋值对象
}

function SuperType(name) { 
    this.name = name; 
    this.colors = ["red", "blue", "green"]; 
}

SuperType.prototype.sayName = function() { 
    console.log(this.name); 
}; 

function SubType(name, age) { 
    SuperType.call(this, name);
    this.age = age; 
} 

inheritPrototype(SubType, SuperType); 
SubType.prototype.sayAge = function() { 
 	console.log(this.age); 
};
```

这里只调用了一次 `SuperType` 构造函数，避免了 `SubType.prototype` 上不必要也用不到的属性，因此可以说这个例子的效率更高。

`instanceof` 操作符和 `isPrototypeOf()` 方法正常有效

寄生式组合继承可以算是引用类型继承的最佳模式

## 8.4 类

**ECMAScript 6** 新引入的 `class` 关键字具有正式定义**类的能力**

虽然 `ECMAScript 6` 类表面上看起来可以支持正式的面向对象编程，但实际上它背后使用的仍然是**原型**和**构造函数的概念**

### 8.4.1 类定义

- 类声明
- 类表达式

```js
// 类声明
class Person {} 

// 类表达式
const Animal = class {};
```

与函数定义不同的是，

1. 函数声明可以提升，但类定义不能

```js
// 虽然函数声明可以提升，但类定义不能
// function
console.log(FunctionExpression); // undefined
var FunctionExpression = function() {};
console.log(FunctionExpression); // function() {}
console.log(FunctionDeclaration); // FunctionDeclaration() {}

function FunctionDeclaration() {}
console.log(FunctionDeclaration); // FunctionDeclaration() {}

//class
console.log(ClassExpression); // undefined
var ClassExpression = class {};
console.log(ClassExpression); // class {}
console.log(ClassDeclaration); // ReferenceError: ClassDeclaration is not defined

class ClassDeclaration {}
console.log(ClassDeclaration); // class ClassDeclaration {}
```

2. 函数受函数作用域限制，而类受块作用域限制

```js
{ 
    function FunctionDeclaration() {} 
 	class ClassDeclaration {} 
} 

console.log(FunctionDeclaration); // FunctionDeclaration() {} 
console.log(ClassDeclaration); // ReferenceError: ClassDeclaration is not defined
```

#### 类的构成

类可以包含构造函数方法、实例方法、获取函数、设置函数和静态类方法，但这些都不是必需的。

空的类定义照样有效

```js
// 空类定义，有效 
class Foo {} 
// 有构造函数的类，有效

class Bar { 
  	constructor() {} 
}

// 有获取函数的类，有效
class Baz { 
 	get myBaz() {} 
} 

// 有静态方法的类，有效
class Qux { 
 	static myQux() {} 
}
```

在把类表达式赋值给变量后，可以通过 name 属性取得类表达式的名称字符串。但不能在类表达式作用域外部访问这个标识符

```js
let Person = class PersonName {
    identify() {
        console.log(Person.name, PersonName.name);
    }
}
let p = new Person();
p.identify(); // PersonName PersonName
console.log(Person.name); // PersonName
console.log(PersonName); // ReferenceError: PersonName is not defined
```

### 8.4.2 类构造函数

`constructor` 关键字用于在类定义块内部创建类的构造函数。

方法名 constructor 会告诉解释器在使用 new 操作符创建类的新实例时，应该调用这个函数。

构造函数的定义不是必需的，不定义构造函数相当于将构造函数定义为空函数

#### 实例化

使用 new 操作符实例化 Person 的操作等于使用 new 调用其构造函数。

- 使用 new 调用类的构造函数会执行如下操作
  - 在内存中创建一个新对象。
  - 这个新对象内部的[[Prototype]]指针被赋值为构造函数的 prototype 属性。
  - 构造函数内部的 this 被赋值为这个新对象（即 this 指向新对象）。
  - 执行构造函数内部的代码（给新对象添加属性）。
  - 如果构造函数返回非空对象，则返回该对象；否则，返回刚创建的新对象。

```js
class Animal {}
        
class Person {
    constructor() {
        console.log('person ctor');
    }
}

class Vegetable {
    constructor() {
        this.color = 'orange';
    }
}

let a = new Animal();
let p = new Person(); // person ctor 
let v = new Vegetable();
console.log(v.color); // orange
```

类实例化时传入的参数会用作构造函数的参数。如果不需要参数，则类名后面的括号也是可选的

```js
class Person1 {
    constructor(name) {
        console.log(arguments.length);
        this.name = name || null;
    }
}
let p1 = new Person1; // 0
console.log(p1.name); // null

let p2 = new Person1(); // 0
console.log(p2.name); // null

let p3 = new Person1('Jake'); // 1
console.log(p3.name); // Jake
```

默认情况下，**类构造函数会在执行之后返回 `this` 对象**。

构造函数返回的对象会被用作实例化的对象，如果没有什么引用新创建的 `this` 对象，那么这个对象会被销毁。

如果返回的不是 this 对象，而是其他对象，那么这个对象不会通过 `instanceof` 操作符检测出跟类有关联，因为这个对象的原型指针并没有被修改。

```js
class Person {
    constructor(override) {
        this.foo = 'foo';
        if (override) {
            return {
                bar: 'bar'
            };
        }
    }
}
let p1 = new Person(), 
    p2 = new Person(true);
console.log(p1); // Person{ foo: 'foo' } 
console.log(p1 instanceof Person); // true

console.log(p2); // { bar: 'bar' } 
console.log(p2 instanceof Person); // false
```

类构造函数与构造函数的主要区别是，

- 调用类构造函数必须使用 new 操作符。
  - 调用类构造函数时如果忘了使用 new 则会抛出错误
  - 类构造函数没有什么特殊之处，实例化之后，它会成为普通的实例方法（但作为类构造函数，仍然要使用 new 调用）
- 普通构造函数如果不使用 new 调用，那么就会以全局的 this（通常是 window）作为内部对象。

```js
function Person() {}
class Animal {}
// 把 window 作为 this 来构建实例

let p = Person();
let a = Animal();
// TypeError: class constructor Animal cannot be invoked without 'new'


// 类构造函数没有什么特殊之处，实例化之后，它会成为普通的实例方法（但作为类构造函数，仍然要使用 new 调用）
class Person2 {}
// 使用类创建一个新实例
let p1 = new Person2();

p1.constructor();
// TypeError: Class constructor Person cannot be invoked without 'new'
// 使用对类构造函数的引用创建一个新实例
let p2 = new p1.constructor();
```

#### 把类当成特殊函数

**ECMAScript 类就是一种特殊函数**。

声明一个类之后，通过 typeof 操作符检测类标识符，表明它是一个函数

```js
// 通过 typeof 操作符检测类标识符，表明它是一个函数
class Person {}
console.log(Person); // class Person {}
console.log(typeof Person); // function

// 类标识符有 prototype 属性，而这个原型也有一个 constructor 属性指向类自身
console.log(Person.prototype); // { constructor: f() }
console.log(Person === Person.prototype.constructor); // true

// 使用 instanceof 操作符检查构造函数原型是否存在于实例的原型链中
let p = new Person();
console.log(p instanceof Person); // true
```

类中定义的 constructor 方法**不会被当成构造函数**，在对它使用 `instanceof` 操作符时会返回 `false`

但是，如果在创建实例时直接**将类构造函数当成普通构造函数来使用**，那么 `instanceof` 操作符的返回值是 `true`

```js
class Person {}

let p1 = new Person();
console.log(p1.constructor === Person); // true
console.log(p1 instanceof Person); // true
// 类中定义的 constructor 方法不会被当成构造函数，在对它使用 instanceof 操作符时会返回 false
console.log(p1 instanceof Person.constructor); // false

let p2 = new Person.constructor();
console.log(p2.constructor === Person); // false
console.log(p2 instanceof Person); // false
// 如果在创建实例时直接将类构造函数当成普通构造函数来使用，那么 instanceof 操作符的返回值会反转
console.log(p2 instanceof Person.constructor); // true
```

像其他对象或函数引用一样把**类作为参数传递**

```js
// 类可以像函数一样在任何地方定义，比如在数组中
let classList = [
    class {
        constructor(id) {
            this.id_ = id;
            console.log(`instance ${this.id_}`);
        }
    }
];
function createInstance(classDefinition, id) {
    return new classDefinition(id);
}
let foo = createInstance(classList[0], 3141); // instance 3141
```

与立即调用函数表达式相似，**类也可以立即实例化**

```js
// 因为是一个类表达式，所以类名是可选的
let p = new class Foo {
    constructor(x) {
        console.log(x);
    }
}('bar'); // bar

console.log(p); // Foo {}
```

### 8.4.3 实例、原型和类成员

#### 实例成员

每次通过new调用类标识符时，都会执行类构造函数。在这个函数内部，可以为新创建的实例（this）添加“自有”属性。至于添加什么样的属性，则没有限制。另外，在构造函数执行完毕后，仍然可以给实例继续添加新成员。

```js
class Person {
    constructor() { 
 		// 这个例子先使用对象包装类型定义一个字符串
		// 为的是在下面测试两个对象的相等性
 		this.name = new String('Jack'); 
 		this.sayName = () => console.log(this.name); 
    	this.nicknames = ['Jake', 'J-Dog'] 
 	} 
} 
let p1 = new Person(), 
 p2 = new Person();

p1.sayName(); // Jack 
p2.sayName(); // Jack 

console.log(p1.name === p2.name); // false 
console.log(p1.sayName === p2.sayName); // false 
console.log(p1.nicknames === p2.nicknames); // false 

p1.name = p1.nicknames[0]; 
p2.name = p2.nicknames[1]; 
p1.sayName(); // Jake 
p2.sayName(); // J-Dog
```

#### 原型方法与访问器

为了在实例间共享方法，类定义语法把在类块中定义的方法作为原型方法。

```js
class Person {
    constructor() {
        // 添加到 this 的所有内容都会存在于不同的实例上
        this.locate = () => console.log('instance');
    }

    // 在类块中定义的所有内容都会定义在类的原型上
    locate() {
        console.log('prototype');
    }
}

let p = new Person();
p.locate(); // instance
Person.prototype.locate(); // prototype
```

类方法等同于对象属性，因此可以使用**字符串**、**符号**或**计算的值**作为键

类定义也支持**获取和设置访问器**。

#### 静态类方法

这些方法通常用于**执行不特定于实例的操作**，也不要求存在类的实例。

与原型成员类似，**静态成员每个类上只能有一个**

静态类成员在类定义中使用 static 关键字作为前缀，在静态成员中，this 引用类自身。其他所有约定跟原型成员一样

```js
class Person {
    constructor() {
        // 添加到 this 的所有内容都会存在于不同的实例上
        this.locate = () => console.log('instance', this);
    }
    // 定义在类的原型对象上
    locate() {
        console.log('prototype', this);
    }
    // 定义在类本身上
    static locate() {
        console.log('class', this);
    }
}
let p = new Person();
p.locate(); // instance, Person {} 
Person.prototype.locate(); // prototype, {constructor: ... } 
Person.locate(); // class, class Person {}
```

静态类方法非常适合作为实例工厂

#### 非函数原型和类成员

**类定义并不显式支持在原型或类上添加成员数据，但在类定义外部，可以手动添加**

```js
class Person {
    sayName() {
        console.log(`${Person.greeting} ${this.name}`);
    }
}
// 在类上定义数据成员
Person.greeting = 'My name is';
// 在原型上定义数据成员
Person.prototype.name = 'Jake';
let p = new Person();
p.sayName(); // My name is Jake
```

#### 迭代器与生成器方法

类定义语法支持在原型和类本身上定义生成器方法

```js
class Person {
    // 在原型上定义生成器方法
    *createNicknameIterator() {
        yield 'Jack';
        yield 'Jake';
        yield 'J-Dog';
    }
    // 在类上定义生成器方法
    static *createJobIterator() {
        yield 'Butcher';
        yield 'Baker';
        yield 'Candlestick maker';
    }
}
let jobIter = Person.createJobIterator();
console.log(jobIter.next().value); // Butcher 
console.log(jobIter.next().value); // Baker 
console.log(jobIter.next().value); // Candlestick maker 
let p = new Person();
let nicknameIter = p.createNicknameIterator();
console.log(nicknameIter.next().value); // Jack 
console.log(nicknameIter.next().value); // Jake 
console.log(nicknameIter.next().value); // J-Dog
```

因为支持生成器方法，所以可以通过添加一个默认的迭代器，把类实例变成可迭代对象

```js
class Person {
    constructor() {
        this.nicknames = ['Jack', 'Jake', 'J-Dog'];
    }
    *[Symbol.iterator]() {
        yield *this.nicknames.entries();
    }
}
let p = new Person();
for (let [idx, nickname] of p) {
    console.log(nickname);
}
// Jack
// Jake
// J-Dog
-------------------------------------------------------
// 也可以只返回迭代器实例
class Person1 {
    constructor() {
        this.nicknames = ['Jack', 'Jake', 'J-Dog'];
    }
    [Symbol.iterator]() {
        return this.nicknames.entries();
    }
}
let p1 = new Person1();
for (let [idx, nickname] of p1) {
    console.log(nickname);
}
// Jack 
// Jake 
// J-Dog
```

### 8.4.4 继承

ECMAScript 6 新增特性中最出色的一个就是原生支持了类继承机制。

虽然类继承使用的是新语法，但背后依旧使用的是原型链。

#### 继承基础

ES6 类支持单继承。使用 extends 关键字，就可以继承任何拥有[[Construct]]和原型的对象。

很大程度上，这意味着不仅可以继承一个类，也可以继承普通的构造函数（保持向后兼容）

```js
class Vehicle {}
// 继承类
class Bus extends Vehicle {}
let b = new Bus();
console.log(b instanceof Bus); // true 
console.log(b instanceof Vehicle); // true 

function Person() {}
// 继承普通构造函数
class Engineer extends Person {}
let e = new Engineer();
console.log(e instanceof Engineer); // true 
console.log(e instanceof Person); // true
```

派生类都会通过原型链访问到类和原型上定义的方法。this 的值会反映调用相应方法的实例或者类

```js
class Vehicle {
    identifyPrototype(id) {
        console.log(id, this);
    }
    static identifyClass(id) {
        console.log(id, this);
    }
}
class Bus extends Vehicle {}
let v = new Vehicle();
let b = new Bus();
b.identifyPrototype('bus'); // bus, Bus {} 
v.identifyPrototype('vehicle'); // vehicle, Vehicle {} 
Bus.identifyClass('bus'); // bus, class Bus {} 
Vehicle.identifyClass('vehicle'); // vehicle, class Vehicle {}
```

#### 构造函数、HomeObject 和 super()

派生类的方法可以通过 super 关键字引用它们的原型

这个关键字只能在派生类中使用，而且仅限于类构造函数、实例方法和静态方法内部。

在类构造函数中使用 super 可以调用父类构造函数。

```js
class Vehicle {
    constructor() {
        this.hasEngine = true;
    }
}
class Bus extends Vehicle {
    constructor() {
        // 不要在调用 super()之前引用 this，否则会抛出 ReferenceError 
        super(); // 相当于 super.constructor() 
        console.log(this instanceof Vehicle); // true 
        console.log(this); // Bus { hasEngine: true } 
    }
}
new Bus();
```

在静态方法中可以通过 super 调用继承的类上定义的静态方法

- 在使用 super 时要注意几个问题
  - super 只能在派生类构造函数和静态方法中使用
  - 不能单独引用 super 关键字，要么用它调用构造函数，要么用它引用静态方法
  - 调用 super()会调用父类构造函数，并将返回的实例赋值给 this
  - super()的行为如同调用构造函数，如果需要给父类构造函数传参，则需要手动传入。
  - 如果没有定义类构造函数，在实例化派生类时会调用 super()，而且会传入所有传给派生类的参数。
  - 在类构造函数中，不能在调用 super()之前引用 this。
  - 如果在派生类中显式定义了构造函数，则要么必须在其中调用 super()，要么必须在其中返回一个对象。

#### 抽象基类

可能需要定义这样一个类，**它可供其他类继承，但本身不会被实例化**

但通过 `new.target` 也很容易实现。`new.target` 保存通过 `new` 关键字调用的类或函数。通过在实例化时检测 `new.target` 是不是抽象基类，可以阻止对抽象基类的实例化。

```js
// 抽象基类
class Vehicle {
    constructor() {
        console.log(new.target);
        if (new.target === Vehicle) {
            throw new Error('Vehicle cannot be directly instantiated');
        }
    }
}
// 派生类
class Bus extends Vehicle {}
new Bus(); // class Bus {}
new Vehicle(); // class Vehicle {}
// Error: Vehicle cannot be directly instantiated
```

通过在抽象基类构造函数中进行检查，可以要求派生类必须定义某个方法。因为原型方法在调用类构造函数之前就已经存在了，所以可以通过 this 关键字来检查相应的方法。

```js
// 抽象基类
class Vehicle {
    constructor() {
        if (new.target === Vehicle) {
            throw new Error('Vehicle cannot be directly instantiated');
        }
        if (!this.foo) {
            throw new Error('Inheriting class must define foo()');
        }
        console.log('success!');
    }
}
// 派生类
class Bus extends Vehicle {
    foo() {}
}
// 派生类
class Van extends Vehicle {}
new Bus(); // success!
new Van(); // Error: Inheriting class must define foo()
```

#### 继承内置类型

ES6 类为继承内置引用类型提供了顺畅的机制，开发者可以方便地扩展内置类型：

```js
class SuperArray extends Array {
    shuffle() {
        // 洗牌算法
        for (let i = this.length - 1; i > 0; i--) {
            const j = Math.floor(Math.random() * (i + 1));
            [this[i], this[j]] = [this[j], this[i]];
        }
    }
}
let a = new SuperArray(1, 2, 3, 4, 5);
console.log(a instanceof Array); // true
console.log(a instanceof SuperArray); // true
console.log(a); // [1, 2, 3, 4, 5]
a.shuffle();
console.log(a); // [3, 1, 4, 5, 2]
```

有些内置类型的方法会返回新实例。默认情况下，返回实例的类型与原始实例的类型是一致的

#### 类混入

**把不同类的行为集中到一个类是一种常见的 JavaScript 模式**。虽然 ES6 没有显式支持**多类继承**，但通过现有特性可以轻松地模拟这种行为。

> `Object.assign()`方法是为了**混入对象行为**而设计的。只有在需要混入类的行为时才有必要自己实现混入表达式。如果只是需要混入多个对象的属性，那么使用`Object.assign()`就可以了。

extends 关键字后面是一个 JavaScript 表达式。**任何可以解析为一个类或一个构造函数的表达式都是有效的**。这个表达式会在求值类定义时被求值

```js
class Vehicle {} 
function getParentClass() { 
 	console.log('evaluated expression'); 
 	return Vehicle; 
} 

class Bus extends getParentClass() {} 
// 可求值的表达式
```

混入模式可以通过在一个表达式中连缀多个混入元素来实现，这个表达式最终会解析为一个可以被继承的类。

如果 Person 类需要组合 A、B、C，则需要某种机制实现 B 继承 A，C 继承 B，而 Person再继承 C，从而把 A、B、C 组合到这个超类中。实现这种模式有不同的策略。

```js
class Vehicle {}
let FooMixin = (Superclass) => class extends Superclass {
    foo() {
        console.log('foo');
    }
};
let BarMixin = (Superclass) => class extends Superclass {
    bar() {
        console.log('bar');
    }
};
let BazMixin = (Superclass) => class extends Superclass {
    baz() {
        console.log('baz');
    }
};
class Bus extends FooMixin(BarMixin(BazMixin(Vehicle))) {}
let b = new Bus();
b.foo(); // foo
b.bar(); // bar
b.baz(); // baz
```

> 很多 JavaScript 框架（特别是 React）已经抛弃混入模式，转向了组合模式（把方法提取到独立的类和辅助对象中，然后把它们组合起来，但不使用继承）。这反映了那个众所周知的软件设计原则：“组合胜过继承（composition over inheritance）。”这个设计原则被很多人遵循，在代码设计中能提供极大的灵活性。