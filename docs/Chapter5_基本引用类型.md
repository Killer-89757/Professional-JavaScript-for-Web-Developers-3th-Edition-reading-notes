# 第五章：基本引用类型

引用类型有时候也被称为**对象定义**，因为它们描述了自己的对象应有的属性和方法。

**对象被认为是某个特定引用类型的实例**。新对象通过使用 `new` 操作符后跟一个**构造函数**（constructor）来创建。构造函数就是用来创建新对象的函数。

Javascript中的对象称为引用值。有几种内置的引用类型可用于创建特定类型的对象。

## 5.1 Date

要创建日期对象，使用new操作符来调用Date构造函数：

```js
let now = new Date()
```

Date类型将日期保存为自1970年1月1日午夜（零时）至今所经过的毫秒数。

在不给Date构造函数传参的情况下，创建的对象将保存当前的日期和时间。

```js
let now = new Date()
now.getTime()          // 1602464952410(相当于Date.now())
```

- 要基于其他日期和时间创建日期对象，必须传入其毫秒表示

  - Date.parse() 

    - Date.parse()方法接收一个表示日期的字符串参数，尝试将这个字符串转换为表示该日期的毫秒数

    - 所有实现都必须支持下列日期格式：

      - “月/日/年”，如"5/23/2019"；
      - “月名 日, 年”，如"May 23, 2019"；
      - “周几 月名 日 年 时:分:秒 时区”，如"Tue May 23 2019 00:00:00 GMT-0700"；
      -  ISO 8601 扩展格式“YYYY-MM-DDTHH:mm:ss.sssZ”，如 2019-05-23T00:00:00（只适用于兼容 ES5 的实现）。

    - ```js
      // 要创建一个表示“2019 年 5 月 23 日”的日期对象
      let someDate = new Date(Date.parse("May 23, 2019"));
      
      // 如果传给 Date.parse()的字符串并不表示日期，则该方法会返回 NaN。
      // 如果直接把表示日期的字符串传给 Date 构造函数，那么 Date 会在后台调用 Date.parse()。
      // 等价于
      let someDate = new Date("May 23, 2019");
      ```

  - Date.UTC()

    - Date.UTC()方法也返回日期的毫秒表示

    - 参数：

      - 传给 Date.UTC()的参数是年、零起点月数（1 月是 0，2 月是 1，以此类推）、日（1~31）、时（0~23）、分、秒和毫秒。
      - 这些参数中，只有前两个（年和月）是必需的。
      - 如果不提供日，那么默认为 1 日。
      - 其他参数的默认值都是 0。

    - ```js
      // GMT 时间 2000 年 1 月 1 日零点
      let y2k = new Date(Date.UTC(2000, 0)); 
      
      // GMT 时间 2005 年 5 月 5 日下午 5 点 55 分 55 秒
      let allFives = new Date(Date.UTC(2005, 4, 5, 17, 55, 55));
      ```

    - Date.UTC()也会被 Date 构造函数隐式调用，创建的是本地日期，不是 GMT 日期。

    - ```js
      // 本地时间 2000 年 1 月 1 日零点
      let y2k = new Date(2000, 0); 
      
      // 本地时间 2005 年 5 月 5 日下午 5 点 55 分 55 秒
      let allFives = new Date(2005, 4, 5, 17, 55, 55);
      ```

代码执行时间

```js
// 起始时间
let start = Date.now(); 

// 调用函数
doSomething(); 

// 结束时间
let stop = Date.now(), 
result = stop - start;
```

### 5.1.1 继承的方法

Date类型重写了**`toLocaleString()`**，**`toString()`**和**`valueOf()`**。

- **toLocaleString()** ：方法返回与浏览器运行的**本地环境**一致的日期和时间。

- **toString()**：方法通常返回**带时区信息**的日期和时间。

- **valueOf()**：返回日期的毫秒表示。

```js
let now = new Date()
now.toLocaleString()   // "2020/10/12 上午9:14:52"
now.toString()         // Mon Oct 12 2020 09:14:52 GMT+0800 (中国标准时间)"
now.valueOf()          // 1602465292914
```

常用方法

```js
let now = new Date()
now.getDate()          // 12 (返回日期中的日(1~31))
now.getDay()           // 1  (表示周几，0表示周日，6表示周六)
now.getMonth()         // 9  (返回日期中的月，0表示一月，11表示十二月)
now.getHours()         // 9  (返回日期中的时(0~23))
```

### 5.1.2 日期格式化方法

Date 类型有几个专门用于格式化日期的方法，它们都会**返回字符串**

- `toDateString()`显示日期中的周几、月、日、年（格式特定于实现）；
- `toTimeString()`显示日期中的时、分、秒和时区（格式特定于实现）；
- `toLocaleDateString()`显示日期中的周几、月、日、年（格式特定于实现和地区）；
- `toLocaleTimeString()`显示日期中的时、分、秒（格式特定于实现和地区）；
- `toUTCString()`显示完整的 UTC 日期（格式特定于实现）

### 5.1.3 日期/时间组件方法

| 方 法                              | 说 明                                                        |
| ---------------------------------- | ------------------------------------------------------------ |
| getTime()                          | 返回日期的毫秒表示；与 valueOf()相同                         |
| setTime(*milliseconds*)            | 设置日期的毫秒表示，从而修改整个日期                         |
| getFullYear()                      | 返回 4 位数年（即 2019 而不是 19）                           |
| getUTCFullYear()                   | 返回 UTC 日期的 4 位数年                                     |
| setFullYear(*year*)                | 设置日期的年（*year* 必须是 4 位数）                         |
| setUTCFullYear(*year*)             | 设置 UTC 日期的年（*year* 必须是 4 位数）                    |
| getMonth()                         | 返回日期的月（0 表示 1 月，11 表示 12 月）                   |
| getUTCMonth()                      | 返回 UTC 日期的月（0 表示 1 月，11 表示 12 月）              |
| setMonth(*month*)                  | 设置日期的月（*month* 为大于 0 的数值，大于 11 加年）        |
| setUTCMonth(*month*)               | 设置 UTC 日期的月（*month* 为大于 0 的数值，大于 11 加年）   |
| getDate()                          | 返回日期中的日（1~31）                                       |
| getUTCDate()                       | 返回 UTC 日期中的日（1~31）                                  |
| setDate(*date*)                    | 设置日期中的日（如果 *date* 大于该月天数，则加月）           |
| setUTCDate(*date*)                 | 设置 UTC 日期中的日（如果 *date* 大于该月天数，则加月）      |
| getDay()                           | 返回日期中表示周几的数值（0 表示周日，6 表示周六）           |
| getUTCDay()                        | 返回 UTC 日期中表示周几的数值（0 表示周日，6 表示周六）      |
| getHours()                         | 返回日期中的时（0~23）                                       |
| getUTCHours()                      | 返回 UTC 日期中的时（0~23）                                  |
| setHours(*hours*)                  | 设置日期中的时（如果 *hours* 大于 23，则加日）               |
| setUTCHours(*hours*)               | 设置 UTC 日期中的时（如果 *hours* 大于 23，则加日）          |
| getMinutes()                       | 返回日期中的分（0~59）                                       |
| getUTCMinutes()                    | 返回 UTC 日期中的分（0~59）                                  |
| setMinutes(*minutes*)              | 设置日期中的分（如果 *minutes* 大于 59，则加时）             |
| setUTCMinutes(*minutes*)           | 设置 UTC 日期中的分（如果 *minutes* 大于 59，则加时）        |
| getSeconds()                       | 返回日期中的秒（0~59）                                       |
| getUTCSeconds()                    | 返回 UTC 日期中的秒（0~59）                                  |
| setSeconds(*seconds*)              | 设置日期中的秒（如果 *seconds* 大于 59，则加分）             |
| setUTCSeconds(*seconds*)           | 设置 UTC 日期中的秒（如果 *seconds* 大于 59，则加分）        |
| getMilliseconds()                  | 返回日期中的毫秒                                             |
| getUTCMilliseconds()               | 返回 UTC 日期中的毫秒                                        |
| setMilliseconds(*milliseconds*)    | 设置日期中的毫秒                                             |
| setUTCMilliseconds(*milliseconds*) | 设置 UTC 日期中的毫秒                                        |
| getTimezoneOffset()                | 返回以分钟计的 UTC 与本地时区的偏移量（如美国 EST 即“东部标准时间”返回 300，进入夏令时的地区可能有所差异） |

## 5.2 RegExp

ECMAScript 通过 RegExp 类型支持正则表达式。

RegExp类型是ECMAScript支持正则表达式的接口，提供了大多数基础的和部分高级的正则表达式功能。

正则表达式使用类似Perl的简洁语法来创建：

```txt
let expression = /pattern/flags
```

pattern（模式）可以是任何简单或复杂的正则表达式，包括字符类、限定符、分组、向前查找和反向引用。

每个正则表达式可以带零个或多个flags（标记），用于控制正则表达式的行为：

- `g`: 全局模式，表示查找字符串的全部内容，而不是找到第一个就结束。
- `i`: 不区分大小写，表示在查找匹配时忽略pattern的字符串大小写。
- `m`: 多行模式，表示查找到一行文本末尾时会继续查找。
- `y`: 粘附模式，表示只查找从lastIndex开始及之后的字符串。
- `u`：Unicode 模式，启用 Unicode 匹配。
- `s`：dotAll 模式，表示元字符.匹配任何字符（包括`\n` 或`\r`）。

**使用字面量形式定义**

使用不同模式和标记可以创建出各种表达式：

```js
// 匹配字符串中的所有"at"
let pattern1 = /at/g

// 匹配第一个"bat"或"cat"，忽略大小写
let pattern2 = /[bc]at/i

// 匹配所有以"at"结尾的三字符组合，忽略大小写
let pattern3 = /.at/gi
```

如果要匹配元字符 （ [ { \ ^ $ | ] } ? * + . ，需要转义：

```js
// 匹配第一个"[bc]at",忽略大小写
let pattern = /\[bc\at]/i

// 匹配所有".at"，忽略大小写
let pattern4 = /\.at/gi
```

**使用 RegExp 构造函数**

正则表达式也可以使用 RegExp 构造函数来创建，它接收两个参数：模式字符串和（可选的）标记字符串

```js
// 跟 pattern1 一样，只不过是用构造函数创建的
let pattern2 = new RegExp("[bc]at", "i");
```

因为 RegExp 的模式参数是字符串，所以在某些情况下需要二次转义。所有元字符都必须二次转义，包括转义字符序列，如\n（\转义后的字符串是`\\`，在正则表达式字符串中则要写成`\\\\`）。

### 5.2.1 RegExp 实例属性

每个 RegExp 实例都有下列属性，提供有关模式的各方面信息。

- `global`：布尔值，表示是否设置了 g 标记。

- `ignoreCase`：布尔值，表示是否设置了 i 标记。

- `unicode`：布尔值，表示是否设置了 u 标记。

- `sticky`：布尔值，表示是否设置了 y 标记。

- `lastIndex`：整数，表示在源字符串中下一次搜索的开始位置，始终从 0 开始。

- `multiline`：布尔值，表示是否设置了 m 标记。

- `dotAll`：布尔值，表示是否设置了 s 标记。

- `source`：正则表达式的字面量字符串（不是传给构造函数的模式字符串），没有开头和结尾的斜杠。

- `flags`：正则表达式的标记字符串。始终以字面量而非传入构造函数的字符串模式形式返回（没有前后斜杠）。

### 5.2.2 RegExp 实例方法

RegExp实例的主要方法是exec()。主要用于配合捕获组的使用。这个方法只接收一个参数，即要应用模式的字符串。

如果找到了匹配项，则返回包含第一个匹配信息的数组；如果没找到匹配项，则返回null。

返回的数组虽然是 Array 的实例，但包含两个额外的属性：`index` 和 `input`。index 是字符串中匹配模式的起始位置，input 是要查找的字符串。

这个数组的第一个元素是匹配整个模式的字符串，其他元素是与表达式中的捕获组匹配的字符串。如果模式中没有捕获组，则数组只包含一个元素。

```js
let text = "attack on ryan"
let pattern = /at(t)ack/
let matches = pattern.exec(text)     // ["attack", "t", index: 0, input: "attack on ryan", groups: undefined]
```

如果模式设置了全局标记，则每次调用 exec()方法会返回一个匹配的信息。如果没有设置全局标记，则无论对同一个字符串调用多少次 exec()，也只会返回第一个匹配的信息

```js
// 没有设置全局标记
let text = "cat, bat, sat, fat"; 
let pattern = /.at/; 
let matches = pattern.exec(text); 
console.log(matches.index); // 0 
console.log(matches[0]); // cat 
console.log(pattern.lastIndex); // 0 

matches = pattern.exec(text); 
console.log(matches.index); // 0 
console.log(matches[0]); // cat 
console.log(pattern.lastIndex); // 0
-------------------------------------------
// 设置了全局标记(每次调用 exec()都会在字符串中向前搜索下一个匹配项)
let text = "cat, bat, sat, fat"; 
let pattern = /.at/g; 
let matches = pattern.exec(text); 
console.log(matches.index); // 0 
console.log(matches[0]); // cat 
console.log(pattern.lastIndex); // 3 

matches = pattern.exec(text); 
console.log(matches.index); // 5 
console.log(matches[0]); // bat 
console.log(pattern.lastIndex); // 8 

matches = pattern.exec(text); 
console.log(matches.index); // 10 
console.log(matches[0]); // sat 
console.log(pattern.lastIndex); // 13
```

另一个常用方法是test()。接收一个字符串参数，如果能够匹配则返回true否则返回false。

这个方法适用于只想测试模式是否匹配，而不需要实际匹配内容的情况。

```js
let text = "attack on ryan"
let pattern = /attack/

pattern.test(text)     // true
```

### 5.2.3 RegExp 构造函数属性

RegExp 构造函数的属性：可以通过两种不同的方式访问它们，每个属性都有一个全名和一个简写。

| 全 名        | 简 写 | 说 明                                      |
| ------------ | ----- | ------------------------------------------ |
| input        | $_    | 最后搜索的字符串（非标准特性）             |
| lastMatch    | $&    | 最后匹配的文本                             |
| lastParen    | $+    | 最后匹配的捕获组（非标准特性）             |
| leftContext  | $`    | input 字符串中出现在 lastMatch 前面的文本  |
| rightContext | $'    | input 字符串中出现在 lastMatch 后面的文本  |
| multiline    | $*    | 布尔值，表示是否所有表达式都是使用多行模式 |

```js
let text = "this has been a short summer"; 
let pattern = /(.)hort/g; 
if (pattern.test(text)) { 
  console.log(RegExp.input); // this has been a short summer 
  console.log(RegExp.leftContext); // this has been a 
  console.log(RegExp.rightContext); // summer 
  console.log(RegExp.lastMatch); // short 
  console.log(RegExp.lastParen); // s
  console.log(RegExp.multiline); // false
}

// 等价于
let text = "this has been a short summer"; 
let pattern = /(.)hort/g; 
/* 
 * 注意：Opera 不支持简写属性名
 * IE 不支持多行匹配
 */ 
if (pattern.test(text)) { 
  console.log(RegExp.$_); // this has been a short summer 
  console.log(RegExp["$`"]); // this has been a 
  console.log(RegExp["$'"]); // summer 
  console.log(RegExp["$&"]); // short 
  console.log(RegExp["$+"]); // s
  console.log(RegExp["$*"]); // false
}
```

RegExp 还有其他几个构造函数属性，可以存储最多 9 个捕获组的匹配项。这些属性通过 `RegExp. $1`~`RegExp.$9` 来访问，分别包含第 1~9 个捕获组的匹配项。

## 5.3 原始值包装类型

为了方便操作原始值，ECMAScript提供了3中特殊的引用类型：Boolean、Number和 String。

**每当用到某个原始值的方法或属性时，后台都会创建一个相应原始包装类型的对象，从而暴露出操作原始值的各种方法**。

```js
let s1 = "some text"
let s2 = s1.substring(2)
```

s1为基本字符串类型，本不应该存在**substring()**方法，但是后台会进行如下3个步骤：

1. 创建一个String类型的实例
2. 调用实例上的特定方法
3. 销毁实例

可以想象成如下代码

```js
let s1 = new String("some text")
let s2 = s1.substring(2)
s1 = null
```

**引用类型**与**原始值包装类型**的主要区别在于对象的生命周期。

- 在通过 new 实例化引用类型后，得到的实例会在离开作用域时被销毁
- 自动创建的原始值包装对象则只存在于访问它的那行代码执行期间。这意味着不能在运行时给原始值添加属性和方法。

因此给原始值添加属性或方法时无效的

```js
let s1 = "some text"
s1.color = "cyan"       // （临时创建的实例会在语句结束后销毁）
console.log(s1.color)   // undefined
```

> 原因就是第二行代码运行时会临时创建一个 String 对象，而当第三行代码执行时，这个对象已经被销毁了。实际上，第三行代码在这里创建了自己的 String 对象，但这个对象没有 color 属性。

在原始值包装类型的实例上调用 typeof 会返回"object"，所有原始值包装对象都会转换为布尔值 true

Object 构造函数作为一个工厂方法，能够根据传入值的类型返回相应原始值包装类型的实例。

```js
let obj = new Object("some text"); 
console.log(obj instanceof String); // true
```

### 5.3.1 Boolean

Boolean 是对应布尔值的引用类型。要创建一个 Boolean 对象，就使用 Boolean 构造函数并传入true 或 false

Boolean 的实例会重写 `valueOf()`方法，返回一个原始值 true 或 false。`toString()`方法被调用时也会被覆盖，返回字符串"true"或"false"。

理解**原始布尔值**和 **Boolean 对象**之间的区别非常重要，强烈建议永远不要使用后者

### 5.3.2 Number

Number 是对应数值的引用类型。要创建一个 Number 对象，就使用 Number 构造函数并传入一个数值

Number 类型重写了 `valueOf()`、`toLocaleString()`和 `toString()`方法。`valueOf()`方法返回 Number 对象表示的原始数值，另外两个方法返回数值字符串。`toString()`方法可选地接收一个表示基数的参数，并返回相应基数形式的数值字符串

- **toFixed()**

**toFixed()**：方法返回包含指定小数点位数的数值字符串，如果数值本身的小数位超过了参数指定的位数，则四舍五入到最接近的小数位；

```js
let num = 10; 
console.log(num.toFixed(2)); // "10.00"

let num = 10.005; 
console.log(num.toFixed(2)); // "10.01"
```

- **toExponential()**

**toExponential()**：返回以科学记数法（也称为指数记数法）表示的数值字符串

```js
let num = 10; 
console.log(num.toExponential(1)); // "1.0e+1"
```

- **toPrecision()**

**toPrecision()**：方法会根据情况返回最合理的输出结果，可能是固定长度，也可能是科学记数法形式。

```js
let num = 99; 
console.log(num.toPrecision(1)); // "1e+2" 
console.log(num.toPrecision(2)); // "99" 
console.log(num.toPrecision(3)); // "99.0"
```

本质上，toPrecision()方法会根据数值和精度来决定调用 toFixed()还是 toExponential()。

- **isInteger()**

ES6 新增了 Number.isInteger()方法，用于辨别一个数值是否保存为整数。有时候，小数位的 0可能会让人误以为数值是一个浮点值

```js
console.log(Number.isInteger(1)); // true 
console.log(Number.isInteger(1.00)); // true 
console.log(Number.isInteger(1.01)); // false
```

- **安全整数 isSafeInteger()**

特殊的数值范围：这个数值范围从 `Number.MIN_SAFE_INTEGER（-2^53 + 1）`到 `Number.MAX_SAFE_INTEGER（2^53 - 1）`。

```js
console.log(Number.isSafeInteger(-1 * (2 ** 53))); // false 
console.log(Number.isSafeInteger(-1 * (2 ** 53) + 1)); // true 

console.log(Number.isSafeInteger(2 ** 53)); // false 
console.log(Number.isSafeInteger((2 ** 53) - 1)); // true
```

### 5.3.3 String

String 是对应字符串的引用类型。要创建一个 String 对象，使用 String 构造函数并传入一个数值

3个继承的方法 `valueOf()`、`toLocaleString()`和 `toString()`都返回对象的原始字符串值。

- **length 属性**

表示字符串中字符的数量

```js
let stringValue = "hello world"; 
console.log(stringValue.length); // "11"
```

即使字符串中包含双字节字符（而不是单字节的 ASCII 字符），也仍然会按单字符来计数

- **charAt()**

**charAt()**：方法返回给定索引位置的字符，由传给方法的整数参数指定。

```js
let message = "abcde"; 
console.log(message.charAt(2)); // "c"
```

- **charCodeAt()**

**charCodeAt()**：方法可以查看指定码元的字符编码。这个方法返回指定索引位置的码元值，索引以整数指定。

```js
let message = "abcde"; 
// Unicode "Latin small letter C"的编码是 U+0063 
console.log(message.charCodeAt(2)); // 99 
// 十进制 99 等于十六进制 63 
console.log(99 === 0x63); // true
```

- **fromCharCode()**

**fromCharCode()**：方法用于根据给定的 UTF-16 码元创建字符串中的字符。这个方法可以接受任意多个数值，并返回将所有数值对应的字符拼接起来的字符串

- 代理对、codePointAt()、码点、 **normalize()**方法

#### 字符串操作方法

- **拼接**

  - **concat()**：用于将一个或多个字符串拼接成一个新字符串，不会修改调用它们的字符串，只会返回提取到的原始新字符串值

- **提取**

  - 都返回调用它们的字符串的一个子字符串，而且都接收一或两个参数。第一个参数表示子字符串开始的位置，第二个参数表示子字符串结束的位置
  - 任何情况下，省略第二个参数都意味着提取到字符串末尾
  - 不会修改调用它们的字符串，只会返回提取到的原始新字符串值
  - **slice()**
    - 第二个参数是提取结束的位置(不包含该位置)
  - **substr()**
    - 第二个参数表示返回的子字符数量
    - 第一个负参数值当成字符串长度加上该值，将第二个负参数值转换为 0。
    - 第二个参数会被转换为 0，意味着返回的字符串包含零个字符，因而会返回一个空字符串
  - **substring()**
    - 第二个参数是提取结束的位置(不包含该位置)
    - 会将所有负参数值都转换为 0
    - 调用substring(3, 0)，等价于 substring(0, 3)。因为这个方法会将较小的参数作为起点，将较大的参数作为终点

- **位置**

  - 从字符串中搜索传入的字符串，并返回位置（如果没找到，则返回-1）。
  - 这两个方法都可以接收可选的第二个参数，表示开始搜索的位置。
  - **indexOf()**
    - 从字符串开头开始查找子字符串
  - **lastIndexOf()**
    - 从字符串末尾开始查找子字符串

- **包含**

  - 用于判断字符串中是否包含另一个字符串
  - 从字符串中搜索传入的字符串，并返回一个表示是否包含的布尔值。
  - `startsWith()`和 `includes()`方法接收可选的第二个参数，表示开始搜索的位置
  - `endsWith()`方法接收可选的第二个参数，表示应该当作字符串末尾的位置
  - **startsWith()**
    - 检查开始于索引 0 的匹配项
  - **endsWith()**
    - 检查开始于索引(string.length - substring.length)的匹配项
  - **includes()**
    - 检查整个字符串

- **trim()**

  - 这个方法会创建字符串的一个副本，删除前、后所有空格符，再返回结果。
  - `trimLeft()`和 `trimRight()`方法分别用于从字符串开始和末尾清理空格符
  - `trim()`返回的是字符串的副本，因此原始字符串不受影响

- **repeat()**

  - 接收一个整数参数，表示要将字符串复制多少次，然后返回**拼接所有副本**后的结果

- **padStart()**和 **padEnd()**方法

  - padStart()和 padEnd()方法会复制字符串，如果小于指定长度，则在相应一边填充字符，直至满足长度条件。
  - 第一个参数是长度，第二个参数是可选的填充字符串，默认为空格

- **迭代与解构**

  - **迭代**

    - 字符串的原型上暴露了一个`@@iterator`方法，表示可以迭代字符串的每个字符

  - **解构**

    - 字符串就可以通过解构操作符来解构了

    - ```js
      let message = "abcde"; 
      console.log([...message]); // ["a", "b", "c", "d", "e"]
      ```

- **大小写转换**
  - 在很多地区，地区特定的方法与通用的方法是一样的。但在少数语言中（如土耳其语），Unicode 大小写转换需应用特殊规则，要使用地区特定的方法才能实现正确转换。
  - toLowerCase()
  - toLocaleLowerCase()
    - 基于特定地区实现
  - toUpperCase()
  - toLocaleUpperCase()
    - 基于特定地区实现
- **模式匹配**
  - **match()**
    - 接收一个参数，可以是一个正则表达式字符串，也可以是一个 `RegExp` 对象
    - `match()`方法返回的数组与 `RegExp` 对象的 `exec()`方法返回的数组是一样的：
      - 第一个元素是与整个模式匹配的字符串，
      - 其余元素则是与表达式中的捕获组匹配的字符串（如果有的话）
  - **search**()
    - 参数：正则表达式字符串或 `RegExp` 对象
    - 返回模式第一个匹配的位置索引，如果没找到则返回-1
- 替换
  - **replace()**：字符串替换操作
    - 接收两个**参数**
      - 第一个参数可以是一个`RegExp` 对象或一个字符串（这个字符串不会转换为正则表达式）
        - 如果第一个参数是字符串，那么只会替换第一个子字符串
        - 想替换所有子字符串，第一个参数必须为正则表达式并且带全局标记
      - 第二个参数可以是一个字符串或一个函数
        - ![image-20240510143156992](https://cdn.jsdelivr.net/gh/Killer-89757/PicBed/images/2024%2F05%2Fimage-20240510143156992-6662f9.png)

- **拆分**
  - **split()**：根据传入的分隔符将字符串拆分成数组。
  - 接收两个**参数**
    - 作为分隔符的参数可以是字符串，也可以是 RegExp 对象。（字符串分隔符不会被这个方法当成正则表达式)
    - 第二个参数，即数组大小，确保返回的数组不会超过指定大小。
- **比较**
  - **localeCompare()**：比较两个字符串，返回如下 3 个值中的一个
    - 如果按照字母表顺序，字符串应该排在字符串参数前头，则返回负值。（通常是-1，具体还要看与实际值相关的实现。）
    - 如果字符串与字符串参数相等，则返回 0。
    - 如果按照字母表顺序，字符串应该排在字符串参数后头，则返回正值。（通常是 1，具体还要看与实际值相关的实现。）
  - localeCompare()区分大小写，大写字母排在小写字母前面。
-  HTML 方法
  - 这些方法基本上已经没有人使用了

## 5.4 单例内置对象

ECMA-262对内置对象的定义是“**任何由ECMAScript实现提供、与宿主环境无关，并在ECMAScript程序开始执行时就存在的对象**”。这意味着开发者不用显式实例化内置对象，因为它们已经实例化好了。

- 包括 Object、Array 、String、Global 和 Math

### 5.4.1 Global

Global对象时ECMAScript中最特别的对象，因为代码不会显式访问它。ECMA-262规定Global对象为兜底对象，在全局作用域中定义的变量和函数都会变成Global对象的属性。

#### URL 编码方法

- 用于编码统一资源标识符（URI），以便传给浏览器

  - 有效的 URI 不能包含某些字符，比如空格。使用 URI 编码方法来编码 URI 可以让浏览器能够理解它们，同时又以特殊的 UTF-8 编码替换掉所有无效字符

- **encodeURI()**

  - 对整个 URI 进行编码
  - 不会编码属于 URL 组件的特殊字符，比如冒号、斜杠、问号、井号

- **encodeURIComponent()**

  - 用于编码 URI 中单独的组件
  - 会编码它发现的所有非标准字符

- **decodeURI()**

  - 对使用 encodeURI()编码过的字符解码

- **decodeURIComponent()**

  - 解码所有

    被 encodeURIComponent()编码的字符，基本上就是解码所有特殊值。

#### eval()方法

- 这个方法就是一个完整的 ECMAScript 解释器，它接收一个参数，即一个要执行的 ECMAScript（JavaScript）字符串

- ```js
  eval("console.log('hi')"); 
  
  //等价于
  console.log("hi");
  ```

- 可以在 eval()内部定义一个函数或变量，然后在外部代码中引用

- ```js
  eval("function sayHi() { console.log('hi'); }"); 
  sayHi();
  ```

- 通过 eval()定义的任何变量和函数都不会被提升，这是因为在解析代码的时候，它们是被包含在一个字符串中的。它们只是在 eval()执行的时候才会被创建

#### Global对象属性

| 属 性          | 说 明                     |
| -------------- | ------------------------- |
| undefined      | 特殊值 undefined          |
| NaN            | 特殊值 NaN                |
| Infinity       | 特殊值 Infinity           |
| Object         | Object 的构造函数         |
| Array          | Array 的构造函数          |
| Function       | Function 的构造函数       |
| Boolean        | Boolean 的构造函数        |
| String         | String 的构造函数         |
| Number         | Number 的构造函数         |
| Date           | Date 的构造函数           |
| RegExp         | RegExp 的构造函数         |
| Symbol         | Symbol 的伪构造函数       |
| Error          | Error 的构造函数          |
| EvalError      | EvalError 的构造函数      |
| RangeError     | RangeError 的构造函数     |
| ReferenceError | ReferenceError 的构造函数 |
| SyntaxError    | SyntaxError 的构造函数    |
| TypeError      | TypeError 的构造函数      |
| URIError       | URIError 的构造函数       |

#### window对象

**浏览器将window对象实现为Global对象的代理**，因此所以全局作用域中声明的变量和函数都会变成window的属性。

```js
var color = "red"
window.color = "red"
```

另一种获取 Global 对象的方式

```js
let global = function() { 
  return this; 
}();
```

### 5.4.2 Math

#### **Math** 对象属性

| 属 性        | 说 明                 |
| ------------ | --------------------- |
| Math.E       | 自然对数的基数 e 的值 |
| Math.LN10    | 10 为底的自然对数     |
| Math.LN2     | 2 为底的自然对数      |
| Math.LOG2E   | 以 2 为底 e 的对数    |
| Math.LOG10E  | 以 10 为底 e 的对数   |
| Math.PI      | π 的值                |
| Math.SQRT1_2 | 1/2 的平方根          |
| Math.SQRT2   | 2 的平方根            |

ECMAScript提供了Math对象作为保存数学公式、信息和计算的地方。

#### 常用属性和方法

```js
Math.E          // 自然对数的基数e的值
Math.PI         // Π的值

// min，max 两个方法都接收任意多个参数
Math.min(2，5，1，10)      // 返回一组数值中的最小值
Math.max(2，5，1，10)      // 返回一组数值中的最大值
let values = [1, 2, 3, 4, 5, 6, 7, 8]; 
let max = Math.max(...val);

Math.ceil()               // 向上舍入为最接近的整数
Math.floor()              // 向下舍入为最接近的整数
Math.round()              // 四舍五入返回整数
Math.fround()             // 返回数值最接近的单精度（32 位）浮点值表示
Math.random()             // 返回[0, 1)之间的随机数
```

- `Math.random()`方法在这里出于演示目的是没有问题的。如果是为了加密而需要生成随机数（传给生成器的输入需要较高的不确定性），那么建议使用 `window.crypto. getRandomValues()`。

#### 其他方法

| 方 法                  | 说 明                              |
| ---------------------- | ---------------------------------- |
| Math.abs(*x*)          | 返回 *x* 的绝对值                  |
| Math.e*x*p(*x*)        | 返回 Math.E 的 *x* 次幂            |
| Math.e*x*pm1(*x*)      | 等于 Math.e*x*p(*x*) - 1           |
| Math.log(*x*)          | 返回 *x* 的自然对数                |
| Math.log1p(*x*)        | 等于 1 + Math.log(*x*)             |
| Math.pow(*x*, *power*) | 返回 *x* 的 *power* 次幂           |
| Math.hypot(*...nums*)  | 返回 *nums* 中每个数平方和的平方根 |
| Math.clz32(*x*)        | 返回 32 位整数 *x* 的前置零的数量  |
| Math.sign(*x*)         | 返回表示 *x* 符号的 1、0、-0 或-1  |
| Math.trunc(*x*)        | 返回 *x* 的整数部分，删除所有小数  |
| Math.sqrt(*x*)         | 返回 *x* 的平方根                  |
| Math.cbrt(*x*)         | 返回 *x* 的立方根                  |
| Math.acos(*x*)         | 返回 *x* 的反余弦                  |
| Math.acosh(*x*)        | 返回 *x* 的反双曲余弦              |
| Math.asin(*x*)         | 返回 *x* 的反正弦                  |
| Math.asinh(*x*)        | 返回 *x* 的反双曲正弦              |
| Math.atan(*x*)         | 返回 *x* 的反正切                  |
| Math.atanh(*x*)        | 返回 *x* 的反双曲正切              |
| Math.atan2(*y*, *x*)   | 返回 *y*/*x* 的反正切              |
| Math.cos(*x*)          | 返回 *x* 的余弦                    |
| Math.sin(*x*)          | 返回 *x* 的正弦                    |
| Math.tan(*x*)          | 返回 *x* 的正切                    |

