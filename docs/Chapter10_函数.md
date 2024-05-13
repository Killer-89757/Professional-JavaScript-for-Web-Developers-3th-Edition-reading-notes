# 第十章：函数

**函数实际上是对象**。每个函数都是`Function`类型的实例，而 Function 也有属性和方法，跟其他引用类型一样

因为函数是对象，所以**函数名就是指向函数对象的指针**，**而且不一定与函数本身紧密绑定**。

函数通常以函数声明的方式定义

```js
// 声明式定义
function sum (num1, num2) { 
 	return num1 + num2; 
}
// 注意函数定义最后没有加分号。

// 函数表达式 式定义
let sum = function(num1, num2) { 
 	return num1 + num2; 
};
// 注意 function 关键字后面没有名称，因为不需要。这个函数可以通过变量 sum 来引用
// 注意这里的函数末尾是有分号的，与任何变量初始化语句一样。

// 箭头函数 式定义
let sum = (num1, num2) => { 
 	return num1 + num2; 
};

// Function 构造函数 式定义
// 这个构造函数接收任意多个字符串参数
// 最后一个参数始终会被当成函数体，而之前的参数都是新函数的参数。
let sum = new Function("num1", "num2", "return num1 + num2");   // 不推荐
```

我们不推荐使用这种语法来定义函数，因为这段代码会被解释两次：第一次是将它当作常规ECMAScript 代码，第二次是解释传给构造函数的字符串。这显然会影响性能。

## 10.1 箭头函数

**ECMAScript 6** 新增了使用胖箭头（=>）语法定义函数表达式的能力

```js
let arrowSum = (a, b) => { 
 	return a + b; 
}; 
// 等价于
let functionExpressionSum = function(a, b) { 
 	return a + b; 
}; 
console.log(arrowSum(5, 8)); // 13 
console.log(functionExpressionSum(5, 8)); // 13
```

箭头函数非常适合嵌入函数的场景

```js
let ints = [1, 2, 3]; 

console.log(ints.map(function(i) { return i + 1; })); // [2, 3, 4] 
console.log(ints.map((i) => { return i + 1 })); // [2, 3, 4]
```

如果只有一个参数，那也可以不用括号。只有没有参数，或者多个参数的情况下，才需要使用括号

```js
// 以下两种写法都有效
let double = (x) => { return 2 * x; }; 
let triple = x => { return 3 * x; }; 

// 没有参数需要括号
let getRandom = () => { return Math.random(); }; 

// 多个参数需要括号
let sum = (a, b) => { return a + b; }; 

// 无效的写法：
let multiply = a, b => { return a * b; };
```

箭头函数也可以不用大括号，但这样会改变函数的行为。如果不使用大括号，那么箭头后面就只能有一行代码，**省略大括号会隐式`返回`这行代码的值**

```js
// 以下两种写法都有效，而且返回相应的值
let double = (x) => { return 2 * x; }; 
let triple = (x) => 3 * x; 

// 可以赋值
let value = {}; 
let setName = (x) => x.name = "Matt"; 

setName(value); 
console.log(value.name); // "Matt" 

// 无效的写法：
let multiply = (a, b) => return a * b;
```

箭头函数不能使用 `arguments`、`super` 和 `new.target`，也不能用作构造函数。此外，箭头函数也没有 `prototype` 属性

## 10.2 函数名

**函数名就是指向函数的指针**，所以它们跟其他包含对象指针的变量具有相同的行为。这意味着一个函数可以有多个名称

```js
function sum(num1, num2) { 
 	return num1 + num2; 
} 
console.log(sum(10, 10)); // 20

let anotherSum = sum; 
console.log(anotherSum(10, 10)); // 20 

sum = null; 
console.log(anotherSum(10, 10)); // 20
```

**ECMAScript 6** 的所有函数对象都会暴露一个只读的 name 属性，其中包含关于函数的信息。

多数情况下，这个属性中保存的就是一个函数标识符，或者说是一个字符串化的变量名。即使函数没有名称，也会如实显示成空字符串。

如果它是使用 Function 构造函数创建的，则会标识成`"anonymous"`

```js
function foo() {}
let bar = function() {};
let baz = () => {};

console.log(foo.name); // foo 
console.log(bar.name); // bar 
console.log(baz.name); // baz 

console.log((() => {}).name); //（空字符串）
console.log((new Function()).name); // anonymous
```

如果函数是一个获取函数、设置函数，或者使用 bind()实例化，那么标识符前面会加上一个前缀

```js
function foo() {}
console.log(foo.bind(null).name); // bound foo
let dog = {
    years: 1,
    get age() {
        return this.years;
    },
    set age(newAge) {
        this.years = newAge;
    }
}
let propertyDescriptor = Object.getOwnPropertyDescriptor(dog, 'age');
console.log(propertyDescriptor.get.name); // get age
console.log(propertyDescriptor.set.name); // set age
```

## 10.3 理解参数

ECMAScript 函数**既不关心传入的参数个数，也不关心这些参数的数据类型**。

定义函数时要接收两个参数，并不意味着调用时就传两个参数。你可以传一个、三个，甚至一个也不传，解释器都不会报错。

主要是因为 **ECMAScript 函数的参数在内部表现为一个数组**。函数被调用时总会接收一个数组，但函数并不关心这个数组中包含什么。

在使用 function 关键字定义（非箭头）函数时，可以在函数内部访问 arguments 对象，从中取得传进来的每个参数值

arguments 对象是一个类数组对象，因此可以使用中括号语法访问其中的元素（第一个参数是 arguments[0]，第二个参数是 arguments[1]）。

确定传进来多少个参数，可以访问 arguments.length 属性。

```js
// ECMAScript 函数的参数只是为了方便才写出来的，并不是必须写出来的。
function sayHi(name, message) { 
 	console.log("Hello " + name + ", " + message); 
}

// 等价于
function sayHi() { 
    console.log("Hello " + arguments[0] + ", " + arguments[1]); 
}

function doAdd() { 
 	if (arguments.length === 1) { 
 		console.log(arguments[0] + 10); 
 	} else if (arguments.length === 2) { 
 		console.log(arguments[0] + arguments[1]); 
 	} 
} 
doAdd(10); // 20 
doAdd(30, 20); // 50
```

- 那就是 arguments 对象可以跟命名参数一起使用

```js
function doAdd(num1, num2) { 
 	if (arguments.length === 1) { 
 		console.log(num1 + 10); 
 	} else if (arguments.length === 2) { 
 		console.log(arguments[0] + num2); 
 	} 
}
```

- 它的值始终会与对应的命名参数同步

```js
function doAdd(num1, num2) { 
 	arguments[1] = 10; 
 	console.log(arguments[0] + num2); 
}
```

因为 arguments 对象的值会自动同步到对应的命名参数，所以修改 arguments[1]也会修改 num2 的值，因此两者的值都是 10。**但这并不意味着它们都访问同一个内存地址，它们在内存中还是分开的，只不过会保持同步而已**。

**对于命名参数而言，如果调用函数时没有传这个参数，那么它的值就是 undefined**。这就类似于定义了变量而没有初始化。

> **严格模式下**，arguments 会有一些变化。
>
> - 首先，像前面那样给 arguments[1] 赋值不会再影响 num2 的值。就算把 arguments[1]设置为 10，num2 的值仍然还是传入的值。
>
> - 其次，在函数中尝试重写 arguments 对象会导致语法错误。

#### 箭头函数中的参数

如果函数是使用箭头语法定义的，那么**传给函数的参数将不能使用 arguments 关键字访问，而只能通过定义的命名参数访问**

```js
function foo() { 
 	console.log(arguments[0]); 
} 
foo(5); // 5 
let bar = () => { 
 	console.log(arguments[0]); 
}; 
bar(5); // ReferenceError: arguments is not defined
```

虽然箭头函数中没有 arguments 对象，但可以在包装函数中把它提供给箭头函数

```js
function foo() { 
 	let bar = () => { 
 		console.log(arguments[0]); // 5 
    }; 
    bar(); 
} 
foo(5);
```

> ECMAScript 中的所有参数都按值传递的。不可能按引用传递参数。**如果把对象作为参数传递，那么传递的值就是这个对象的引用**

## 10.4 没有重载

ECMAScript 函数不能像传统编程那样重载。

ECMAScript 中定义了两个同名函数，则后定义的会覆盖先定义的

可以通过检查参数的类型和数量，然后分别执行不同的逻辑来模拟函数重载

```js
// 函数名当成指针也有助于理解为什么 ECMAScript 没有函数重载
let addSomeNumber = function(num) { 
    return num + 100; 
}; 
addSomeNumber = function(num) { 
    return num + 200; 
}; 
let result = addSomeNumber(100); // 300
// 在创建第二个函数时，变量 addSomeNumber 被重写成保存第二个函数对象了
```

## 10.5 默认参数值

在 **ECMAScript5.1** 及以前，实现默认参数的一种常用方式**就是检测某个参数是否等于 undefined**，如果是则意味着没有传这个参数，那就给它赋一个值

```js
function makeKing(name) { 
    name = (typeof name !== 'undefined') ? name : 'Henry'; 
    return `King ${name} VIII`; 
} 
console.log(makeKing()); // 'King Henry VIII' 
console.log(makeKing('Louis')); // 'King Louis VIII
```

**ECMAScript 6** 支持显式定义默认参数了。下面就是与前面代码等价的 ES6 写法，**只要在函数定义中的参数后面用=就可以为参数赋一个默认值**

```js
function makeKing(name = 'Henry') { 
    return `King ${name} VIII`; 
} 

console.log(makeKing('Louis')); // 'King Louis VIII' 
console.log(makeKing()); // 'King Henry VIII'
```

给参数传 undefined 相当于没有传值，不过这样可以利用多个独立的默认值

```js
function makeKing(name = 'Henry', numerals = 'VIII') { 
    return `King ${name} ${numerals}`; 
} 
console.log(makeKing()); // 'King Henry VIII' 
console.log(makeKing('Louis')); // 'King Louis VIII' 
console.log(makeKing(undefined, 'VI')); // 'King Henry VI'
```

在使用默认参数时，arguments 对象的值不反映参数的默认值，只反映传给函数的参数

跟 ES5 严格模式一样，**修改命名参数也不会影响 arguments 对象**，**它始终以调用函数时传入的值为准**

```js
function makeKing(name = 'Henry') { 
    name = 'Louis'; 
    return `King ${arguments[0]}`; 
} 
console.log(makeKing()); // 'King undefined' 
console.log(makeKing('Louis')); // 'King Louis'
```

默认参数值**并不限于原始值或对象类型，也可以使用调用函数返回的值**

```js
let romanNumerals = ['I', 'II', 'III', 'IV', 'V', 'VI']; 
let ordinality = 0; 
function getNumerals() { 
    // 每次调用后递增
    return romanNumerals[ordinality++]; 
} 
function makeKing(name = 'Henry', numerals = getNumerals()) { 
    return `King ${name} ${numerals}`; 
} 
console.log(makeKing()); // 'King Henry I'
console.log(makeKing('Louis', 'XVI')); // 'King Louis XVI' 
console.log(makeKing()); // 'King Henry II' 
console.log(makeKing()); // 'King Henry III'
```

函数的默认参数只有在函数被调用时才会求值，不会在函数定义时求值。

计算默认值的函数只有在调用函数但未传相应参数时才会被调用

**箭头函数同样也可以这样使用默认参数，只不过在只有一个参数时，就必须使用括号而不能省略了**

```js
let makeKing = (name = 'Henry') => `King ${name}`; 
console.log(makeKing()); // King Henry
```

#### 默认参数作用域与暂时性死区

因为在求值默认参数时可以定义对象，也可以动态调用函数，所以**函数参数肯定是在某个作用域中求值的**。

给多个参数定义默认值实际上跟使用 let 关键字顺序声明变量一样。

```js
function makeKing(name = 'Henry', numerals = 'VIII') { 
 	return `King ${name} ${numerals}`; 
}

console.log(makeKing()); // King Henry VIII

// 因为参数是按顺序初始化的，所以后定义默认值的参数可以引用先定义的参数
function makeKing(name = 'Henry', numerals = name) { 
    return `King ${name} ${numerals}`; 
} 
console.log(makeKing()); // King Henry Henry

// 参数初始化顺序遵循“暂时性死区”规则，即前面定义的参数不能引用后面定义的
// 调用时不传第一个参数会报错
function makeKing(name = numerals, numerals = 'VIII') { 
 	return `King ${name} ${numerals}`; 
}
```

参数也存在于自己的作用域中，它们不能引用函数体的作用域：

```js
// 调用时不传第二个参数会报错
function makeKing(name = 'Henry', numerals = defaultNumeral) { 
    let defaultNumeral = 'VIII'; 
    return `King ${name} ${numerals}`; 
}
```

## 10.6 参数扩展与收集

ECMAScript 6 新增了扩展操作符，**扩展操作符最有用的场景就是函数定义中的参数列表**

扩展操作符既可以用于调用函数时传参，也可以用于定义函数参数

### 10.6.1 扩展参数

在给函数传参时，有时候可能不需要传一个数组，而是要分别传入数组的元素

如果不使用扩展操作符，想把定义在这个函数这面的数组拆分，那么就得求助于 `apply()` 方法

在使用扩展操作符传参的时候，并不妨碍在其前面或后面再传其他的值，包括使用扩展操作符传其他参数

```js
let values = [1, 2, 3, 4];
function getSum() {
    let sum = 0;
    for (let i = 0; i < arguments.length; ++i) {
        sum += arguments[i];
    }
    return sum;
}
console.log(getSum.apply(null, values)); // 10

// 等价于
console.log(getSum(...values)); // 10

// 在使用扩展操作符传参的时候，并不妨碍在其前面或后面再传其他的值，包括使用扩展操作符传其他参数
console.log(getSum(-1, ...values)); // 9
console.log(getSum(...values, 5)); // 15
console.log(getSum(-1, ...values, 5)); // 14
console.log(getSum(...values, ...[5,6,7])); // 28
```

也可以将扩展操作，符用于命名参数，当然同时也可以使用默认参数

```js
function getProduct(a, b, c = 1) { 
 	return a * b * c; 
} 
let getSum = (a, b, c = 0) => { 
 	return a + b + c; 
} 

console.log(getProduct(...[1,2])); // 2 
console.log(getProduct(...[1,2,3])); // 6 
console.log(getProduct(...[1,2,3,4])); // 6 

console.log(getSum(...[0,1])); // 1 
console.log(getSum(...[0,1,2])); // 3 
console.log(getSum(...[0,1,2,3])); // 3
```

### 10.6.2 收集参数

在构思**函数定义**时，可以使用扩展操作符把不同长度的独立参数组合为一个数组，收集参数的结果会得到一个 Array 实例

```js
function getSum(...values) {
    // 顺序累加 values 中的所有值
    // 初始值的总和为 0
    return values.reduce((x, y) => x + y, 0);
}
console.log(getSum(1,2,3)); // 6
```

- 收集参数的前面如果还有命名参数，则只会收集其余的参数；
- 因为收集参数的结果可变，所以只能把它作为最后一个参数

```js
// 不可以
function getProduct(...values, lastValue) {}

// 可以
function ignoreFirst(firstValue, ...values) {
    console.log(values);
}
ignoreFirst(); // []
ignoreFirst(1); // []
ignoreFirst(1,2); // [2]
ignoreFirst(1,2,3); // [2, 3]
```

箭头函数虽然不支持 arguments 对象，但支持收集参数的定义方式

```js
let getSum = (...values) => {
    return values.reduce((x, y) => x + y, 0);
}
console.log(getSum(1,2,3)); // 6
```

使用收集参数并不影响 arguments 对象，它仍然反映调用时传给函数的参数

```js
function getSum1(...values) {
    console.log(arguments.length); // 3
    console.log(arguments); // [1, 2, 3]
    console.log(values); // [1, 2, 3]
}
console.log(getSum1(1,2,3));
```

## 10.7 函数声明与函数表达式

JavaScript 引擎在任何代码执行之前，**会先读取函数声明，并在执行上下文中生成函数定义**。

**函数表达式必须等到代码执行到它那一行，才会在执行上下文中生成函数定义**。

```js
// 没问题 
console.log(sum(10, 10)); 
function sum(num1, num2) { 
    return num1 + num2; 
}
```

函数声明提升：以上代码可以正常运行，因为**函数声明会在任何代码执行之前先被读取并添加到执行上下文**。

> 在执行代码时，JavaScript 引擎会先执行一遍扫描，**把发现的函数声明提升到源代码树的顶部**。
>
> 因此即使函数定义出现在调用它们的代码之后，引擎也会把函数声明提升到顶部。

```js
// 会出错
console.log(sum(10, 10)); 
let sum = function(num1, num2) { 
 	return num1 + num2; 
};
```

会出错，是因为这个**函数定义包含在一个变量初始化语句中**，而不是函数声明中。

这意味着代码如果**没有执行到加粗的那一行，那么执行上下文中就没有函数的定义，所以上面的代码会出错**。

## 10.8 函数作为值

- 函数作为参数传给另一个函数

```js
function callSomeFunction(someFunction, someArgument) { 
 	return someFunction(someArgument); 
}

// 调用
function add10(num) { 
 	return num + 10; 
} 
let result1 = callSomeFunction(add10, 10); 
console.log(result1); // 20 

function getGreeting(name) { 
 	return "Hello, " + name; 
} 
let result2 = callSomeFunction(getGreeting, "Nicholas"); 
console.log(result2); // "Hello, Nicholas"
```

- 在一个函数中返回另一个函数

```js
function createComparisonFunction(propertyName) {
    return function(object1, object2) {
        let value1 = object1[propertyName];
        let value2 = object2[propertyName];
        if (value1 < value2) {
            return -1;
        } else if (value1 > value2) {
            return 1;
        } else {
            return 0;
        }
    };
}

// 调用
let data = [ 
 	{name: "Zachary", age: 28}, 
 	{name: "Nicholas", age: 29} 
]; 
data.sort(createComparisonFunction("name")); 
console.log(data[0].name); // Nicholas

data.sort(createComparisonFunction("age")); 
console.log(data[0].name); // Zachary
```

## 10.9 函数内部

函数内部存在两个特殊的对象：`arguments` 和 `this`

ECMAScript 6 又新增了 `new.target` 属性

### 10.9.1 arguments

类数组对象，包含调用函数时传入的所有参数

这个对象只有以 `function` 关键字定义函数（相对于使用箭头语法创建函数）时才会有

arguments 对象其实还有一个 `callee` 属性，是一个**指向 arguments 对象所在函数的指针**。

```js
function factorial(num) { 
    if (num <= 1) { 
        return 1; 
    } else { 
        return num * factorial(num - 1); 
    } 
}

// 等价于，优点：使用 arguments.callee 就可以让函数逻辑与函数名解耦
function factorial(num) { 
    if (num <= 1) { 
        return 1; 
    } else { 
        return num * arguments.callee(num - 1); 
    } 
}

// 这个重写之后的 factorial()函数已经用 arguments.callee 代替了之前硬编码的 factorial。这意味着无论函数叫什么名称，都可以引用正确的函数。
let trueFactorial = factorial; 
factorial = function() { 
 	return 0; 
}; 
console.log(trueFactorial(5)); // 120 
console.log(factorial(5)); // 0
```

### 10.9.2 this

另一个特殊的对象是 this，它在标准函数和箭头函数中有不同的行为。

在标准函数中，this 引用的是把函数当成方法调用的上下文对象，这时候通常称其为 this 值（在网页的全局上下文中调用函数时，this 指向 windows）。

```js
window.color = "red";
var o = { color: "blue" };

function sayColor(){
    alert(this.color);
}

sayColor();     //red

o.sayColor = sayColor;
o.sayColor();   //blue
```

在箭头函数中，this引用的是定义箭头函数的上下文

在对 `sayColor()` 的两次调用中，this 引用的都是 window 对象，因为这个箭头函数是在 window 上下文中定义的

```js
window.color = 'red'; 
let o = { 
    color: 'blue' 
}; 
let sayColor = () => console.log(this.color); 
sayColor(); // 'red' 
o.sayColor = sayColor; 
o.sayColor(); // 'red'
```

有读者知道，在事件回调或定时回调中调用某个函数时，this 值指向的并非想要的对象。

此时将回调函数写成箭头函数就可以解决问题。这是因为**箭头函数中的 this 会保留定义该函数时的上下文**

```js
function King() {
    this.royaltyName = 'Henry';
    // this 引用 King 的实例
    setTimeout(() => console.log(this.royaltyName), 1000);
}

function Queen() {
    this.royaltyName = 'Elizabeth';
    // this 引用 window 对象
    setTimeout(function() { console.log(this.royaltyName); }, 1000);
}
new King(); // Henry
new Queen(); // undefined
```

### 10.9.3 caller

这个属性引用的是调用当前函数的函数，或者如果是在全局作用域中调用的则为 null。

```js
function outer() { 
 	inner(); 
} 
function inner() { 
 	console.log(inner.caller); 
} 
outer();

// 等价于
function outer() { 
    inner(); 
} 
function inner() { 
    console.log(arguments.callee.caller); 
} 
outer();
```

以上代码会显示 `outer()` 函数的源代码。这是因为 `ourter()` 调用了 `inner()`，`inner.caller`指向 `outer()`。

严格模式下还有一个限制，就是**不能给函数的 caller 属性赋值**，否则会导致错误

### 10.9.4 new.target

函数始终可以作为构造函数实例化一个新对象，也可以作为普通函数被调用

**ECMAScript 6** 新增了检测函数是否使用 new 关键字调用的 new.target 属性。

- 如果函数是正常调用的，则 `new.target` 的值是 `undefined`

- 如果是使用 new 关键字调用的，则 `new.target` 将引用被调用的构造函数

```js
function King() {
    if (!new.target) {
        throw 'King must be instantiated using "new"'
    }
    console.log('King instantiated using "new"');
}
new King(); // King instantiated using "new"
King(); // Error: King must be instantiated using "new"
```

## 10.10 函数属性与方法

#### 属性

每个函数都有两个属性：

- length
  - length 属性保存函数定义的**`命名参数`的个数**
- prototype
  - 保存**引用类型所有实例方法**的地方

```js
function sayName(name){
    alert(name);
}      

function sum(num1, num2){
    return num1 + num2;
}

function sayHi(){
    alert("hi");
}

alert(sayName.length);  //1
alert(sum.length);      //2
alert(sayHi.length);    //0
```

在 **ECMAScript 5** 中，prototype 属性是不可枚举的，因此使用 for-in 循环不会返回这个属性

#### 方法

- `apply()`
  - 会以指定的 this 值来调用函数，即会设置调用函数时函数体内 this 对象的值
  - 两个参数
    - 函数内 this 的值
    - 一个参数数组(第二个参数可以是 Array 的实例，但也可以是 arguments 对象)
- `call()`
  - 会以指定的 this 值来调用函数，即会设置调用函数时函数体内 this 对象的值
  - 两个参数
    - 函数内 this 的值
    - 而剩下的要传给被调用函数的参数则是逐个传递的(将参数一个一个地列出来)

```js
// apply
function sum(num1, num2){
    return num1 + num2;
}

function callSum1(num1, num2){
    return sum.apply(this, arguments);
}

function callSum2(num1, num2){
    return sum.apply(this, [num1, num2]);
}

alert(callSum1(10,10));   //20
alert(callSum2(10,10));   //20
```

> **在严格模式下，调用函数时如果没有指定上下文对象，则 this 值不会指向 window**。
>
> 除非使用 `apply()` 或 `call()` 把函数指定给一个对象，否则 this 的值会变成 undefined

```js
function sum(num1, num2){
    return num1 + num2;
}

function callSum(num1, num2){
    return sum.call(this, num1, num2);
}

alert(callSum(10,10));   //20
```

**`apply()`和 `call()` 真正强大的地方并不是给函数传参，而是控制函数调用上下文即函数体内 this值的能力。**

```js
window.color = "red";
var o = { color: "blue" };

function sayColor(){
    alert(this.color);
}

sayColor();            //red

sayColor.call(this);   //red
sayColor.call(window); //red
sayColor.call(o);      //blue
```

使用 `call()` 或 `apply()` 的好处是可以**将任意对象设置为任意函数的作用域**

**ECMAScript 5** 出于同样的目的定义了一个新方法：`bind()`

`bind()` 方法会**创建一个新的函数实例**，其 **this 值会被绑定到传给 `bind()`的对象**

```js
window.color = "red";
var o = { color: "blue" };

function sayColor(){
    alert(this.color);
}
var objectSayColor = sayColor.bind(o);
objectSayColor();   //blue
```

> 在 `sayColor()` 上调用 `bind()` 并传入对象 o 创建了一个新函数 `objectSayColor()`。`objectSayColor()` 中的 this 值被设置为 o

## 10.11 函数表达式

定义函数有两种方式：函数声明和函数表达式

函数声明是这样的：

```js
function functionName(arg0, arg1, arg2) { 
    // 函数体 
}
```

函数声明的关键特点是**函数声明提升**

函数表达式有几种不同的形式，最常见的是这样的

```js
let functionName = function(arg0, arg1, arg2) { 
    // 函数体 
};
```

> 函数表达式看起来就像一个普通的变量定义和赋值，即创建一个函数再把它赋值给一个变量 `functionName`。这样创建的函数叫作**匿名函数**（`anonymous funtion`），因为 function 关键字后面没有标识符。（**匿名函数有也时候也被称为兰姆达函数**）。未赋值给其他变量的匿名函数的 name 属性是空字符串。

需要先赋值再使用。

```js
sayHi(); // Error! function doesn't exist yet 
let sayHi = function() { 
    console.log("Hi!"); 
};
```

高危代码：

```js
// 千万别这样做！
// JavaScript 引擎会尝试将其纠正为适当的声明。问题在于浏览器纠正这个问题的方式并不一致
if (condition) { 
    function sayHi() { 
        console.log('Hi!'); 
    } 
} else { 
    function sayHi() { 
        console.log('Yo!'); 
    } 
}

// 改进 没问题 
let sayHi; 
if (condition) { 
    sayHi = function() { 
        console.log("Hi!"); 
    }; 
} else { 
    sayHi = function() { 
        console.log("Yo!"); 
    }; 
}
```

创建函数并赋值给变量的能力也可以用于在一个函数中把另一个函数当作值返回

```js
function createComparisonFunction(propertyName) { 
    return function(object1, object2) { 
        let value1 = object1[propertyName]; 
        let value2 = object2[propertyName]; 
        if (value1 < value2) { 
            return -1; 
        } else if (value1 > value2) { 
            return 1; 
        } else {
            return 0; 
        } 
    }; 
}
```

> 这里的 `createComparisonFunction()`函数返回一个**匿名函数**，这个匿名函数要么被赋值给一个变量，要么可以直接调用。

## 10.12 递归

递归函数通常的形式是一个**函数通过名称调用自己**

```js
function factorial(num) { 
    if (num <= 1) { 
        return 1; 
    } else { 
        return num * factorial(num - 1); 
    } 
}
let anotherFactorial = factorial; 
factorial = null; 
console.log(anotherFactorial(4)); // 报错

// 修正代码
function factorial(num) { 
    if (num <= 1) { 
        return 1; 
    } else { 
        return num * arguments.callee(num - 1); 
    } 
}
```

在**严格模式**下运行的代码是不能访问 `arguments.callee` 的，因为访问会出错。

使用命名函数表达式（named function expression）达到目的

```js
const factorial = (function f(num) { 
    if (num <= 1) { 
        return 1; 
    } else { 
        return num * f(num - 1); 
    } 
});
```

## 10.13 尾调用优化

**ECMAScript 6** 规范新增了一项内存管理优化机制，让 JavaScript 引擎在满足条件时可以重用栈帧

这项优化非常适合**`尾调用`**，即**外部函数的返回值是一个内部函数的返回值**。

在 **ES6 优化之前**，执行这个例子会在内存中发生如下操作

- 执行到 `outerFunction` 函数体，第一个栈帧被推到栈上。
- 执行 `outerFunction` 函数体，到 return 语句。计算返回值必须先计算 `innerFunction`。
- 执行到 `innerFunction` 函数体，第二个栈帧被推到栈上。
- 执行 `innerFunction` 函数体，计算其返回值。
- 将返回值传回 `outerFunction`，然后 `outerFunction` 再返回值。
- 将栈帧弹出栈外。

在 **ES6 优化之后**，执行这个例子会在内存中发生如下操作。

- 执行到 `outerFunction` 函数体，第一个栈帧被推到栈上。

- 执行 `outerFunction` 函数体，到达 return 语句。为求值返回语句，必须先求值 `innerFunction`。

- 引擎发现把第一个栈帧弹出栈外也没问题，因为 `innerFunction` 的返回值也是 `outerFunction` 的返回值。

- 弹出 `outerFunction` 的栈帧。

- 执行到 `innerFunction` 函数体，栈帧被推到栈上。

- 执行 `innerFunction` 函数体，计算其返回值。

- 将 `innerFunction` 的栈帧弹出栈外。

很明显，第一种情况下每多调用一次嵌套函数，就会多增加一个栈帧。而第二种情况下无论调用多少次嵌套函数，都只有一个栈帧。

这就是 ES6 尾调用优化的关键：**如果函数的逻辑允许基于尾调用将其销毁，则引擎就会那么做**。

### 10.13.1 尾调用优化的条件

尾调用优化的条件就是**确定外部栈帧真的没有必要存在了**。

涉及的条件如下：

- 代码在严格模式下执行；
- 外部函数的返回值是对尾调用函数的调用；
- 尾调用函数返回后不需要执行额外的逻辑；
- 尾调用函数不是引用外部函数作用域中自由变量的闭包。

```js
// 因此都不符合尾调用优化的要求
"use strict"; 
// 无优化：尾调用没有返回 
function outerFunction() { 
    innerFunction(); 
} 
// 无优化：尾调用没有直接返回
function outerFunction() { 
    let innerFunctionResult = innerFunction(); 
    return innerFunctionResult; 
} 
// 无优化：尾调用返回后必须转型为字符串
function outerFunction() { 
    return innerFunction().toString(); 
} 
// 无优化：尾调用是一个闭包
function outerFunction() { 
    let foo = 'bar'; 
    function innerFunction() { return foo; } 
    return innerFunction(); 
}
```

### 10.13.2 尾调用优化的代码

通过把简单的递归函数转换为待优化的代码来加深对尾调用优化的理解。

```js
// 计算斐波纳契数列的函数
function fib(n) { 
    if (n < 2) { 
        return n; 
    } 
    return fib(n - 1) + fib(n - 2); 
} 
console.log(fib(0)); // 0 
console.log(fib(1)); // 1 
console.log(fib(2)); // 1 
console.log(fib(3)); // 2 
console.log(fib(4)); // 3 
console.log(fib(5)); // 5 
console.log(fib(6)); // 8

// 优化后的代码
"use strict"; 
// 基础框架 
function fib(n) { 
    return fibImpl(0, 1, n); 
} 
// 执行递归
function fibImpl(a, b, n) { 
    if (n === 0) { 
        return a; 
    } 
    return fibImpl(b, a + b, n - 1); 
}
```

## 10.14 闭包

**闭包**指的是那些**引用了另一个函数作用域中变量的函数**，通常是在嵌套函数中实现的。

```js
function createComparisonFunction(propertyName) { 
    return function(object1, object2) { 
        let value1 = object1[propertyName]; 
        let value2 = object2[propertyName]; 
        if (value1 < value2) { 
            return -1; 
        } else if (value1 > value2) { 
            return 1; 
        } else { 
            return 0; 
        } 
    }; 
}
```

**作用域链**：**调用一个函数时，会为这个函数调用创建一个执行上下文，并创建一个作用域链**。然后**用 `arguments` 和其他命名参数来初始化这个函数的活动对象**。**外部函数的活动对象是内部函数作用域链上的第二个对象**。这个作用域链一直向外串起了所有包含函数的活动对象，直到全局执行上下文才终止。

```js
function compare(value1, value2) { 
    if (value1 < value2) { 
        return -1; 
    } else if (value1 > value2) { 
        return 1; 
    } else { 
        return 0; 
    } 
} 
let result = compare(5, 10);
```

>  过程(`十分重要`)
>
> - 第一次调用 compare()时，会为它创建一个包含 `arguments`、`value1` 和 `value2` 的活动对象，**这个对象是其作用域链上的第一个对象**
> - **全局上下文的变量对象则是 `compare()` 作用域链上的第二个对象**，其中包含 `this`、`result` 和 `compare`。



![image-20240513200010442](https://cdn.jsdelivr.net/gh/Killer-89757/PicBed/images/2024%2F05%2Fimage-20240513200010442-866edb.png)

- **正常的函数执行流程**
  - 全局上下文中的叫变量对象，它会在代码执行期间始终存在。
  - 函数局部上下文中的叫活动对象，只在函数执行期间存在。
  - 在定义 `compare()` 函数时，就会为它**创建作用域链**，**预装载全局变量对象**，**并保存在内部的[[Scope]]**中。
  - 在调用这个函数时，会创建相应的执行上下文，然后通过复制函数的[[Scope]]来创建其作用域链。
  - 接着会创建函数的活动对象（用作变量对象）并将其推入作用域链的前端。
  - 这意味着 `compare()` 函数执行上下文的作用域链中有两个变量对象：局部变量对象和全局变量对象。作用域链其实是一个包含指针的列表，每个指针分别指向一个变量对象，但物理上并不会包含相应的对象。
  - 函数内部的代码在访问变量时，就会使用给定的名称从作用域链中查找变量。函数执行完毕后，局部活动对象会被销毁，内存中就只剩下全局作用域。
- **闭包的执行流程**
  - 在一个函数内部定义的函数会把其包含函数的活动对象添加到自己的作用域链中。
  - 在 `createComparisonFunction()` 返回匿名函数后，它的作用域链被初始化为包含 `createComparisonFunction()`的活动对象和全局变量对象

![image-20240513200835132](https://cdn.jsdelivr.net/gh/Killer-89757/PicBed/images/2024%2F05%2Fimage-20240513200835132-7c5e07.png)

`createComparisonFunction()`的活动对象并不能在它执行完毕后销毁，因为匿名函数的作用域链中仍然有对它的引用。

在 `createComparisonFunction()` 执行完毕后，其执行上下文的作用域链会销毁，但它的活动对象仍然会保留在内存中，直到匿名函数被销毁后才会被销毁

```js
// 创建比较函数
let compareNames = createComparisonFunction('name'); 

// 调用函数
let result = compareNames({ name: 'Nicholas' }, { name: 'Matt' }); 

// 解除对函数的引用，这样就可以释放内存了
compareNames = null;
```

### 10.14.1 this 对象

- 如果内部函数没有使用箭头函数定义，则 this 对象会在运行时绑定到执行函数的上下文
- 如果在全局函数中调用，则 this 在**非严格模式**下等于 `window`，在**严格模式**下等于 `undefined`
- 如果作为某个对象的方法调用，则 this 等于这个对象
- **匿名函数在这种情况下不会绑定到某个对象，这就意味着 this 会指向 window**

```js
window.identity = 'The Window'; 
let object = { 
    identity: 'My Object', 
    getIdentityFunc() { 
        return function() { 
            return this.identity; 
        }; 
    } 
}; 
console.log(object.getIdentityFunc()()); // 'The Window'
```

内部函数永远不可能直接访问外部函数的这两个变量。但是，如果把 this 保存到闭包可以访问的另一个变量中，则是行得通的

在定义匿名函数之前，先把外部函数的 this 保存到变量 that 中。然后在定义闭包时，就可以让它访问 that，因为这是包含函数中名称没有任何冲突的一个变量。即使在外部函数返回之后，that 仍然指向 object，所以调用 `object.getIdentityFunc()()` 就会返回`"My Object"`。

```js
window.identity = 'The Window'; 
let object = { 
    identity: 'My Object', 
    getIdentityFunc() { 
        let that = this; 
        return function() { 
            return that.identity; 
        }; 
    } 
}; 
console.log(object.getIdentityFunc()()); // 'My Object'
```

- 几种调用 `object.getIdentity()` 的方式及返回值

```js
object.getIdentity();            // 'My Object' 
(object.getIdentity)();          // 'My Object' 
(object.getIdentity = object.getIdentity)(); // 'The Window'
```

- 第一行调用 `object.getIdentity()` 是正常调用，会返回`"My Object"`，因为 `this.identity` 就是 `object.identity`。
- 第二行在调用时把 `object.getIdentity` 放在了括号里。虽然加了括号之后看起来是对一个函数的引用，但 this 值并没有变。这是因为按照规范，`object.getIdentity` 和 `(object.getIdentity)` 是相等的。
- 第三行执行了一次赋值，然后再调用赋值后的结果。**因为赋值表达式的值是函数本身，this 值不再与任何对象绑定，所以返回的是"The Window"**。

### 10.14.2 内存泄漏

略

## 10.15 立即调用的函数表达式

**立即调用的匿名函数又被称作立即调用的函数表达式**

它类似于函数声明，但由于被包含在括号中，所以会被解释为函数表达式

紧跟在第一组括号后面的第二组括号会立即调用前面的函数表达式。

```js
(function() { 
    // 块级作用域 
})();

// 作用域不对，导致报错
// IIFE 
(function () { 
    for (var i = 0; i < count; i++) { 
        console.log(i); 
    } 
})(); 
console.log(i); // 抛出错误
```

在 ECMAScript 6 以后，IIFE 就没有那么必要了，因为块级作用域中的变量无须 IIFE 就可以实现同样的隔离。

```js
// 内嵌块级作用域 
{ 
    let i; 
    for (i = 0; i < count; i++) { 
        console.log(i); 
    } 
} 
console.log(i); // 抛出错误

// 循环的块级作用域
for (let i = 0; i < count; i++) { 
    console.log(i); 
} 
console.log(i); // 抛出错误
```

问题代码：

```js
let divs = document.querySelectorAll('div'); 

// 达不到目的！ 
for (var i = 0; i < divs.length; ++i) { 
    divs[i].addEventListener('click', function() { 
        console.log(i); 
    }); 
}

// 这里使用 var 关键字声明了循环迭代变量 i
// 最终所有绑定i的值都变成了 divs.length，迭代变量的值是循环结束时的最终值，即元素的个数
// 这个变量 i 存在于循环体外部，随时可以访问
```

优化：

```js
// 使用自执行函数作用域去限定
let divs = document.querySelectorAll('div'); 
for (var i = 0; i < divs.length; ++i) { 
    divs[i].addEventListener('click', (function(frozenCounter) {
        return function() { 
            console.log(frozenCounter); 
        }; 
    })(i)); 
}

// 块级作用域变量，使用let变量
let divs = document.querySelectorAll('div'); 
for (let i = 0; i < divs.length; ++i) { 
    divs[i].addEventListener('click', function() {
        console.log(i); 
    }); 
}
```

如果对 for 循环**使用块级作用域变量关键字**，在这里就是 let，**那么循环就会为每个循环创建独立的变量**，从而让每个单击处理程序都能引用特定的索引。

## 10.16 私有变量

严格来讲，JavaScript 没有`私有成员`的概念，所有对象属性都公有的

倒是有**私有变量**的概念。任何定义在函数或块中的变量，都可以认为是私有的，因为在这个函数或块的外部无法访问其中的变量。

私有变量包括函数参数、局部变量，以及函数内部定义的其他函数。

**特权方法**（privileged method）是能够访问函数私有变量（及私有函数）的公有方法

两种方式创建特权方法:

- 第一种是在构造函数中实现

```js
function MyObject() { 
    // 私有变量和私有函数 
    let privateVariable = 10; 
    function privateFunction() { 
        return false; 
    } 
    // 特权方法
    this.publicMethod = function() { 
        privateVariable++; 
        return privateFunction(); 
    }; 
}
```

这个模式是把所有私有变量和私有函数都定义在构造函数中，再创建一个能够访问这些私有成员的特权方法。

这样做之所以可行，是因为**定义在构造函数中的特权方法其实是一个闭包**，它具有访问构造函数中定义的所有变量和函数的能力。

```js
function Person(name) { 
    this.getName = function() { 
        return name; 
    }; 
    this.setName = function (value) { 
        name = value; 
    }; 
} 
let person = new Person('Nicholas'); 
console.log(person.getName()); // 'Nicholas' 
person.setName('Greg'); 
console.log(person.getName()); // 'Greg'
```

### 10.16.1 静态私有变量

特权方法也可以通过使用**私有作用域定义私有变量和函数来实现**

```js
(function() { 
    // 私有变量和私有函数
    let privateVariable = 10; 
    function privateFunction() { 
        return false; 
    } 
    // 构造函数
    MyObject = function() {}; 
    // 公有和特权方法
    MyObject.prototype.publicMethod = function() { 
        privateVariable++; 
        return privateFunction(); 
    }; 
})();
```

- 首先定义的是私有变量和私有函数
- 又定义了构造函数和公有方法。
- 公有方法定义在构造函数的原型上，与典型的原型模式一样。
- 这里声明 `MyObject` 并没有使用任何关键字。因为不使用关键字声明的变量会创建在全局作用域中，所以 `MyObject` 变成了全局变量，可以在这个私有作用域外部被访问

```js
(function() { 
    let name = ''; 
    Person = function(value) { 
        name = value; 
    }; 
    Person.prototype.getName = function() { 
        return name; 
    }; 
    Person.prototype.setName = function(value) { 
        name = value; 
    }; 
})(); 

let person1 = new Person('Nicholas'); 
console.log(person1.getName()); // 'Nicholas' 

person1.setName('Matt'); 
console.log(person1.getName()); // 'Matt' 
let person2 = new Person('Michael'); 
console.log(person1.getName()); // 'Michael' 
console.log(person2.getName()); // 'Michael'
```

### 10.16.2 模块模式

前面的模式通过自定义类型创建了私有变量和特权方法

**模块模式**，则在一个**单例对象**上实现了相同的隔离和封装。**单例对象**（singleton）就是只有一个实例的对象。

```js
let singleton = { 
    name: value,
    method() { 
        // 方法的代码
    } 
};
```

模块模式是在单例对象基础上加以扩展，使其通过作用域链来关联私有变量和特权方法

```js
// 模块模式的样板代码
let singleton = function() { 
    // 私有变量和私有函数
    let privateVariable = 10; 
    function privateFunction() { 
        return false; 
    } 
    // 特权/公有方法和属性
    return { 
        publicProperty: true, 
        publicMethod() { 
            privateVariable++; 
            return privateFunction(); 
        } 
    }; 
}();
```

- 模块模式使用了匿名函数返回一个对象
- 在匿名函数内部，首先定义私有变量和私有函数。
- 创建一个要通过匿名函数返回的对象字面量。这个对象字面量中只包含可以公开访问的属性和方法

对象字面量定义了单例对象的公共接口，如果单例对象需要进行某种初始化，并且需要访问私有变量时，那就可以采用这个模式

```js
let application = function() { 
    // 私有变量和私有函数 
    let components = new Array(); 
    // 初始化
    components.push(new BaseComponent()); 
    // 公共接口
    return { 
        getComponentCount() { 
            return components.length; 
        }, 
        registerComponent(component) { 
            if (typeof component == 'object') { 
                components.push(component); 
            } 
        } 
    }; 
}();
```

在模块模式中，单例对象作为一个模块，经过初始化可以包含某些私有的数据，而这些数据又可以通过其暴露的公共方法来访问。

### 10.16.3 模块增强模式

另一个利用模块模式的做法是在返回对象之前先对其进行增强

这适合单例对象需要是某个特定类型的实例，但又必须给它添加额外属性或方法的场景

```js
let singleton = function() { 
    // 私有变量和私有函数
    let privateVariable = 10; 
    function privateFunction() { 
        return false; 
    } 
    // 创建对象
    let object = new CustomType(); 
    // 添加特权/公有属性和方法
    object.publicProperty = true; 
    object.publicMethod = function() { 
        privateVariable++; 
        return privateFunction(); 
    }; 
    // 返回对象
    return object; 
}();
```

application案例：

```js
let application = function() { 
    // 私有变量和私有函数 
    let components = new Array(); 
    // 初始化
    components.push(new BaseComponent()); 
    // 创建局部变量保存实例
    let app = new BaseComponent(); 
    // 公共接口
    app.getComponentCount = function() { 
        return components.length; 
    };
    app.registerComponent = function(component) { 
        if (typeof component == "object") { 
            components.push(component); 
        } 
    }; 
    // 返回实例
    return app; 
}();
```

