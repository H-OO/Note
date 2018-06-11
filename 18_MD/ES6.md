## ES6

目的：学习新特性、学习现代编程语言概念中流行的思想  
例如：解构赋值、迭代器、作用域

#### 第一章 15page/2day

**Babel 转码器**
---
功能：ES6 转 ES5  
目的：学会 Babel 的配置  
使用：使用配置文件.babelrc，位置一般存在于项目根目录

```JS
  // 该文件用来设置转码规则和插件，格式如下
  {
    "presets": [],
    "plugins": []
  }
```

**babel-cli命令行转码**
---
全局或本地安装`babel-cli`  
相关参数：

* --out-file 文件转码输出至某个文件，简写 -o
* --out-dir 整个目录转码输出至某个目录，简写 -d
* -s 生成 source map 文件

注意：使用 Babel 转码，项目产生了对环境的依赖，此时需要将 `babel-cli` 安装在项目中  
再通过 `package.json` 文件中的 `scripts` 简化转码命令行

**babel-node 提供支持 ES6 的 REPL 环境**
---
`babel-node`随`babel-cli`一同安装  
同样可通过 `package.json` 文件中的 `scripts` 简化命令行操作，直接运行 ES6 文件

**babel-register 模块改写 require 命令，加上一个钩子**
---
作用：require 加载后缀为.js/.jsx/.es/.es6 的文件时，先用 Babel 进行转码  
使用：  
安装 `npm i babel-register --save-dev`  
先 `require('babel-register')`  
再 `require('./xxx/es6.js')`  
这样就无需手动转码

注意：由于它是实时转码，只适合在开发环境中使用

**babel-core 只有部分代码需要转 ES5 时**
---
作用：调用 Babel 的 API 进行转码  
使用：  
安装 `npm i babel-core --save-dev`

```JS
const es6Code = 'let x = n => n + 1'; // 字符串便于查看打印结果
const es5Code = require('babel-core').transfrom(es6Code, {
  presets: ['latest'] // 插件
});
```

**babel-polyfill 解决不转换新的 API 问题**
---
作用：由于 Babel 默认只转换新的 JS 句法，不转换新的 API  
例如：Iterator、Generator、Set、Maps、Promise 等全局对象，以及一些定义在全局对象上的方法 Object.assign 都不转码  
使用：如果想让方法运行，就需使用 `babel-polyfill` 模块  
安装 `npm i babel-polyfill --save`  
在脚本头部引入 `require('babel-polyfill')`

**ESLint 代码风格检查**
---
作用：用于静态检查代码的语法和风格  
使用：  
安装 `npm i eslint babel-eslint --save-dev`  
在项目根目录下创建配置文件 `.eslintrc`

```JS
  //
  {
    "parser": "babel-eslint",
    "rules": {
      ...
    }
  }
```

#### 第二章 15page/1day

目的：了解 let 和 const 与 var 的区别，掌握块级作用域的使用

**let and const**
---
作用：提供块级作用域  
注意：

* 不存在变量提升
* 暂时性死区（块级作用域中存在 let 或 const，其所声明的变量不再受外部影响）
* 不能在同一个作用域内重复声明同一个变量

**块级作用域**
---
ES6 能生成块级作用域有：`{}`、`let`、`const`  
ES5 中只存在全局作用域和函数作用域，并没有块级作用域；这导致很多场景不合理  
例如：内层变量可能覆盖外层变量；循环变量泄露为全局变量

ES6 允许块级作用域任意嵌套  
内层作用域可定义外层作用域的同名变量

应用：

```JS
// ES5 使用自执行函数(IIFE)生成作用域（函数作用域）
(function() {
  var tmp = '...';
  // ...
})();
// ES6 使用{}生成作用域（块级作用域）
{
  let tmp = '...';
  // ...
}
```

**块级作用域与函数声明**
---
ES6 明确允许在块级作用域中声明函数  
但使用环境会导致差异性，尽量使用函数表达式

**const 命令**
---
const 声明一个只读的常量。一旦声明，常量的值就不能改变  
本质：保证变量指向的那个内存地址不得改动

```JS
const obj = {};
obj.a = '123'; // 可添加
obj = {}; // error
```
冻结对象  
`Object.freeze({})`

**顶级对象**
---
Node 的 REPL 环境中，顶级对象为 `global`，仅 Node 支持  
浏览器和 Web Worker 中，`self`也指向顶级对象，但是 Node 不支持  
通用方法：使用 `this` 变量获取顶级对象，但有局限性

全局环境中 this 返回顶级对象，但在 Node 模块和 ES6 模块则返回当前模块

注意：在浏览器中顶级对象为 window，但`Node`和`Web Worker`没有 window

```JS
// REPL环境中定义至全局
global.a = '123';
console.log(a); // 123

// 通用方法定义至全局
this.b = '234';
console.log(b); // 234
```

很难找到一种方法可以在所有情况下都能获取到顶级对象，以下两种方法勉强可以使用：

```JS
// 方法1
typeof window !== 'undefined'
  ? window
  : typeof process === 'object' &&
    typeof require === 'function' &&
    typeof global === 'object'
    ? global
    : this;

// 方法2
var getGlobal = function() {
  if (typeof self !== 'undefined') {
    return self;
  }
  if (typeof window !== 'undefined') {
    return window;
  }
  if (typeof global !== 'undefined') {
    return global;
  }
  throw new Error('unable to locate global object');
};
```

#### 第三章 12page/2day

目的：掌握 ES6 变量的解构赋值

**数组解构赋值**
---
本质为模式匹配，只要等号两边的模式相同，就会被赋予对应的值  
解构不成功，变量的值就等于 undefined

只要某种数据结构具有 Iterator 接口，都可以采用数组形式的解构赋值

**generator 函数**
---
简介：generator 函数可以借助 yield 在需要的时候才继续执行剩余的语句，并且传递回一个值  
作用：实现无回调的异步流程控制  
使用：调用 generator 函数会返回一个 Iterator 对象  
Iterator 对象有个`next`方法  
调用`next`方法时，generator 函数会执行直到遇到 yield 语句，就暂停在那里，并返回一个对象

```JS
function* generator() {
  yield 1;
  yield 2;
  yield 3;
}
var ret = generator(); // 返回一个 Iterator 对象
var res1 = ret.next(); // 返回一个对象 {value: 1, done: false}
var res2 = ret.next(); // 返回一个对象 {value: 2, done: false}
var res3 = ret.next(); // 返回一个对象 {value: 3, done: false}
```

**对象解构赋值**
---
```JS
let { x, y, z } = {
  x: 'x',
  z: 'z'
};
console.log(x); // x
console.log(y); // undefined
console.log(z); // z
```

注意：模式不会被赋值

```JS
// 例子1
let obj = {
  p: ['aaa', { y: 'bbb' }]
};
let { p: [x, { y }] } = obj;
// p为模式，而不是变量，不会被赋值
console.log(x); // aaa
console.log(y); // bbb

// 例子2
let node = {
  loc: {
    start: {
      line: 1,
      column: 5
    }
  }
};
let { loc, loc: { start }, loc: { start: { line } } } = node;
console.log(loc); // { start: { line: 1, column: 5 } }
console.log(start); // { line: 1, column: 5 }
console.log(line); // 1

// 例子3
let obj = {};
let arr = [];
({ foo: obj.prop, bar: arr[0] } = { foo: 123, bar: true });
console.log(obj); // { prop: 123 }
console.log(arr); // [ true ]

// 例子4
// 由于数组本质是特殊的对象，因此可以对数组进行对象属性的解构
let arr = [1, 2, 3];
let { 0: first, [arr.length - 1]: last } = arr;
console.log(first); // 1
console.log(last); // 3
```

`()`的作用：  
JS 引擎会将`{}`理解成一个代码块，从而发生语法错误；  
使用`()`包裹，不将大括号写在行首，避免 JS 引擎理解为代码块，才能解决语法错误问题

**字符串解构赋值**
---
```JS
// 将字符串转成一个类数组的对象
let [a, b, c, d, e] = 'hello';
console.log(a); // h
console.log(b); // e
console.log(c); // l
console.log(d); // l
console.log(e); // o

// length为类数组对象的成员
let { length: len } = 'hello';
console.log(len); // 5
```

**数值和布尔值解构赋值**
---
如果等号右边是数值和布尔值，则会先转为对象

```JS
// 数值
let { toString: s } = 123;
console.log(s === Number.prototype.toString); // true
// 布尔值
let { toString: s } = true;
console.log(s === Boolean.prototype.toString); // true
```
解构赋值的规则是，只有等号右边的值不是对象或数组，就先将其转为对象；  
由于 undefined 和 null 无法转为对象，所以对它们进行解构赋值时都会报错

**函数参数解构赋值**
---
```JS
// add
function add([x, y]) {
  return x + y;
}
add([1, 2]); // 3

//
[1, 2].map((i, v) => {
  console.log(i, v); // 1, 0  2, 1
});

//
[[1, 2], [3, 4]].map(([a, b]) => {
  console.log(a + b); // 3 7
});
```

**默认值**
---
解构允许设置默认值

```JS
// 数组解构设置默认值
let [x = '1', y] = ['2', '3'];
console.log(x); // '2'
console.log(y); // '3'

// 对象解构设置默认值
let { x, y = 1 } = {};
console.log(x); // undefined
console.log(y); // 1

let { x: y = 1 } = { x: 2 };
console.log(y); // 2

// 函数参数解构设置默认值
function move({ x = 0, y = 0 } = {}) {
  console.log(x, y);
}
move({ x: 1, y: 2 }); // 1 2
move({ x: 3 }); // 3 0
move(); // 使用默认值{} 0 0
```
注意：默认值生效条件是对象属性值全等于 undefined

**Map**
---
任何部署了 Iterator 接口的对象都可以用 for...of 循环遍历  
Map 结构原生支持 Iterator 接口，配合变量的解构赋值获取键名和键值

```JS
let map = new Map();
map.set('first', 'Hello');
map.set('second', 'World');
console.log(map); // Map { 'first' => 'Hello', 'second' => 'World' }
for (let [k, v] of map) {
  console.log(k, v);
}
// first Hello
// second World
```

**输入模块的指定方法**
---
在加载模块时，往往需要指定输入的方法。解构赋值作用使得输入语句非常清晰

```JS
// REPL环境
// module.js 输出
module.exports {
  first: {},
  second: {}
}
// example.js 输入
const {first, second} = require('Module');
console.log(first); // {}
```

#### 第四章 20page/1day ★☆

**字符串可通过 for...of 循环遍历**
---
```JS
for (let v of 'hello') {
  console.log(v);
}
```

**字符串新增方法**
---
* includes() 返回布尔值，表示是否找到参数字符串
* startsWith() 返回布尔值，表示参数字符串是否在源字符串的头部
* endsWith() 返回布尔值，表示参数字符串是否在源字符串的尾部

参数 1：参数字符串  
参数 2：开始搜索的位置（可选）

注意：  
参数 2，endsWith 方法与其他方法的行为不同，它针对的是前 n 个字符，而其他两个方法针对从第 n 个位置

```JS
let str = 'hello world';
str.includes('hello', 0); // true
str.startsWith('hello', 0); // true
str.endsWith('world', 11); // true
```

* repeat() 返回新字符串，表示将源字符串重复 n 次
* padStart() 字符串长度补全，头部补全
* padEnd() 字符串长度补全，尾部补全

```JS
'x'.repeat(3); // xxx

'x'.padStart(5, 'abc'); // abcax
'x'.padEnd(5, 'abc'); // xabca
'x'.padStart(5); // '     x'
'x'.padEnd(5); // 'x     '
'12'.padStart(10, 'YYYY-MM-DD'); // YYYY-MM-12 字符串格式提醒
```

**模板字符串**
---
用反引号 ` 标识，可作为普通字符串使用，也可以定义多行字符串，或在字符串中嵌入变量

```JS
// 字符串中嵌入变量
// 语法：`${}`

// 例子1
let data = { x: 1, y: 2 };
let str = `${data.x + data.y}`;
console.log(str); // 3

// 例子2
function fn() {
  return 'Hello world';
}
console.log(`${fn()}`); // Hello world

// 例子3
let str = 'Hello';
console.log(`${str} World`); // Hello World

// 例子4
let str = 'return ' + '`${name}`';
const fn = new Function('name', str);
console.log(fn('xxx')); // xxx

// 例子5
var a = 5;
var b = 10;
tag`A ${a + b} B ${a * b}`;
function tag(s, v1, v2) {
  console.log(s); // [ 'A ', ' B ', '' ]
  console.log(v1); // 15
  console.log(v2); // 50
}

// 例子6
var total = 30;
var msg1 = passthru`The total is ${total} (${total * 1.05} with tax)`;
var msg2 = `The total is ${total} (${total * 1.05} with tax)`;
function passthru(literals) {
  // literals  [ 'The total is ', ' (', ' with tax)' ]
  // arguments  { '0': [ 'The total is ', ' (', ' with tax)' ], '1': 30, '2': 31.5 }
  var result = '';
  var i = 0;
  while (i < literals.length) {
    result += literals[i++];
    if (i < arguments.length) {
      result += arguments[i]; // 30  31.5
    }
  }
  return result;
}
console.log(msg1); // The total is 30 (31.5 with tax)
console.log(msg2); // The total is 30 (31.5 with tax)
```

标签模板的一个重要应用就是过滤 HTML 字符串，防止用户输入恶意内容

```JS
var sender = 'xxx';
var msg = saferHTML`<p>${sender} has sent you a message</p>`;
function saferHTML(tmpData) {
  console.log(tmpData); // [ '<p>', ' has sent you a message</p>' ]
  console.log(arguments); // { '0': [ '<p>', ' has sent you a message</p>' ], '1': 'xxx' }
}
```

#### 第五章 13page/2day

**RegExp 构造函数**
---
在 ES5 中，RegExp 构造函数的参数有两种情况：  
a.参数 1 是字符串，参数 2 是正则表达式修饰符

```JS
// a
var regexp = new RegExp('xyz', 'i');
// 等价于
var regexp = /xyz/i;
```

b.参数是正则表达式，返回该正则表达式

```JS
// b
var regexp = new RegExp(/xyz/i);
// 等价于
var regexp = /xyz/i;
```

注意：ES5 中不允许此时 b 使用第二个参数添加修饰符，否则会报错

```JS
// b
var regexp = new RegExp(/xyz/, 'i'); // error
```

ES6 改变了这种行为：  
如果 RegExp 构造函数第一个参数是一个正则对象，  
那么可以使用第二个参数指定修饰符，  
而且返回的正则表达式会忽略原有的正则表达式的修饰符，只用新指定的修饰符

```JS
var regexp = new RegExp(/xzy/gi, 'i'); // /xzy/i
```

正则表达式使用：
```JS
/a/.test('abc') // true
/^a/.test('abc') // true
/c$/.test('abc') // true
/^a|c$/.test('abc') // true
/b$/.test('abc') // false
```

**字符串的正则方法**
---
字符串对象共有 4 种方法使用正则表达式

* match() 返回一个数组，0 为匹配到的字符，1 为字符所在的下标，2 为整体字符串
* replace() 替换字符串中与正则表达式匹配的字符
* search() 检索字符串中与正则表达式匹配的字符下标
* split() 字符串转换成数组

```JS
let str = 'abc';
str.match(/b/); // [ 'b', index: 1, input: 'abc' ]
str.replace(/b/, 'x'); // axc
str.search(/b/); // 1
str.split(/b/); // ['a', 'c']
```

**正则表达式**
---
【修饰符】

* i 修饰符 含义为字母忽略大小写 (insensitive)

* g 修饰符 含义全局查找所有匹配项，后一次匹配只要剩余位置中存在匹配就行 (global)

* m 修饰符 含义多行模式匹配 (multi)

* u 修饰符 含义为 Unicode 模式，用来正确处理大于\uFFFF 的 Unicode 字符。也就是说，可以正确处理 4 个字节的 UTF-16 编码 （ES6）

* y 修饰符 含义全局匹配，后一次匹配都从上一次匹配成功的下个位置开始 （ES6）

【模式-量词】

* {}字符 表示出现的次数，{n}为至少出现 n 次，{n,m}为至少出现 n 次，最多出现 m 次；

* \* 字符 星号前的字符可以不出现，{0,}

* +字符 加号前的字符至少出现一次，{1,}

* ?字符 问好前的字符可以不出现或出现一次，{0,1}

* ^字符 在正则表达式最开始位置代表起始的意思，/^/；在[]中代表排除，/[^]/

* .字符 表示除换行符以外的任意单个字符 （ES6）

【模式-表达式】

* /[]/ 表示匹配中括号内的任意单个字符；具体写法：123 abc；范围写法：0-9 a-z

* /|/ 表示匹配|号左右两边的字符，只要其中一个成功匹配，表达式结果就为 true

* /()/ 括号为分组符，表示为每组匹配项单独设置匹配规则。例如：/(1)([0-9])/ 第一项必须是 1，第二项为 0 至 9 的其中一个字符

【模式-元字符】

* \d 数字

* \D 非数字

* \s 空格

* \S 非空格

* \w 字符（包含有字母或数字或下划线）

* \W 非字符（包含除字母、数字、下划线以外的其他字符）

【行终止符】

* \n 换行符

* \r 回车符

【ES6 正则表达式新增属性】

* sticky 表示是否设置了 y 修饰符

* flags 返回正则表达式的修饰符

```JS
var str = '123[哈哈]123[嘻嘻]123';
var regex = /\[.{1,3}\]/g;
var res = str.match(regex);
console.log(res); // ['[哈哈]', '[嘻嘻]']
for (var i = 0, l = res.length; i < l; i++) {
  str = str.replace(regex, '$');
}
console.log(str); // 123$123$123
```

#### 第六章 16page/1day

**Number 扩展**
---
* Number.isFinite() // 检查一个数值是否为有限的(finite)

* Number.isNaN() // 用来检查一个值是否为 NaN

* Number.parseInt() // 字符串转数字，不保留小数

* Number.parseFloat() // 字符串转数字，保留小数

* Number.isInteger() // 判断一个值是否为整数，JS 中整数与浮点数都是一样的存储方法，3 与 3.0 视为同一个值

* Number.EPSILON // 极小的常量，判断浮点数运算是否在可以接受的误差范围，误差小于该常量，则可视为得到正确结果

* Number.MAX_SAFE_INTEGER // 安全整数，最大上限，常量

* Number.MIN_SAFE_INTEGER // 安全整数，最小下限，常量

* Number.isSafeInteger() // JS 能精确表示的整数数值极限范围，返回 Boolean


**Math 对象扩展**
---
* Math.trunc() // 数值取整

* Math.sign() // 判断是正数(+1)、负数(-1)、还是 0(±0)

* Math.cbrt() // 计算一个数的立方根

* Math.clz32() // 正数使用 32 位二进制形式表示

* Math.imul() // 返回两个数以 32 位带符号整数形式相乘的结果

* Math.hypot() // 返回所有参数的平方和的平方根

* Math.signbit() // 判断一个值的正负


**指数运算符**
---
`**`

```JS
2 ** 2; // 4
2 ** 3; // 8

let a = 1.5;
a **= 2; // 等同于 a = a * a
```

#### 第七章 30page/3day

**作用域**
---
一旦设置了参数的默认值，函数进行声明初始化时，参数会形成一个独立的作用域。等到初始化结束，这个作用域就会消失。这种语法行为在不设置参数默认值时是不会出现的。

如果参数的默认值是一个函数，该函数的作用域也遵守这个规则

**rest 参数**
---
形式：`...变量名`

rest 参数作用等同于 arguments，以数组形式加多余的参数放入其中；能使用所有数组特有的方法

```JS
function add(...value) {
  // value isArr
}
```
注意：rest 参数之后不能再有其他参数（即只能是最后一个参数），否则会报错

**箭头函数**
---
ES6 允许使用`=>`定义函数
```JS
// 例子
var fn = () => 5;
// 等价于
function fn1() {
  return 5;
}

// 箭头函数返回对象
var getObj = id => ({});
```
注意事项：  
1、this 指向定义时所在的对象，而不是使用时所在的对象；箭头函数中的 this 指向是固定的  
2、不可以当构造函数使用  
3、不可以使用 arguments 对象，该对象在函数体内不存在，可以用 rest 参数代替  
4、不可以使用 yield 命令，箭头函数不能作为 Generator 函数

#### 第十四章

**Promise对象**
---
是异步编程的一种解决方案；then方法指定的回调函数将在当前脚本所有同步任务执行完成后才会执行

Promise对象代表一个异步操作，有3种状态：Pending（进行中），Fulfilled（已成功），Rejected（已失败）  
只有异步操作的结果可以决定当前是哪一种状态，任何其他操作都无法改变这个状态；一旦状态改变就不会再变  
状态改变只有两种可能：一种是Pending变为Fulfilled，另一种是Pending变为Rejected  
只要这两种情况发生，状态就凝固了，不会再变，这时就成为Resolved（已定型）

Promise构造函数接收一个函数作为参数，该函数的两个参数分别是resolve（表示成功）和reject（表示失败）  
它们是两个函数，由JS引擎提供，不用自己部署  
resolve函数的作用：将Promise对象的状态从Pending变为Fulfilled；将异步操作的结果作为参数传递出去  
reject函数的作用：将Promise对象的状态从Pending变成Reject；将异步操作报出的错误作为参数传递出去

Promise实例生成之后，then方法分别指定Fulfilled状态和Reject状态的回调函数  
根据状态只执行其中一个回调函数，传递出结果    

```JS
// 模拟状态 200代表成功
const state = 200;
// 成功
const successData = '成功...';
// 失败
const errorData = '失败...';

// Promise对象
const promise = new Promise(function (resolve, reject) {
  // ...
  if (state === 200) {
    resolve(successData); // 异步获取数据成功，输出结果
  } else {
    reject(errorData); // 异步获取数据失败，输出结果
  }
})
// 执行回调获取结果
promise.then(function(success) {
  // success
}, function(error) {
  // error
});
```

.catch方法用来捕获Promise在resolve语句后面抛出的错误  
Promise对象的错误具有"冒泡"性质，会一直向后传递，直到被捕获为止  
也就是说，错误总是会被下一个catch语句捕获

所以最正确的使用方式为：
```JS
promise.then(function(success) {
  // success..
}).catch(function(err) {
  // error..
})
```

**数组扩展**
---
* ... 扩展运算符

**对象扩展**
---
* Object.is() 判断两个值相等
```JS
+0 === -0 //true
NaN === NaN // false

Object.is(+0, -0) // false
Object.is(NaN, NaN) // true
```

**Set类型**
---
ES6新增的数据结构，类似数组，其中成员都是唯一的  
实例方法
* add() 添加成员
* delete() 移除成员
* has() 判断是否拥有某个成员，返回bool
* clear() 清空所有成员
遍历
* keys() 返回键名遍历器
* values() 返回键值遍历器
* entries() 返回键值对遍历器
* forEach() 使用回调函数遍历每个成员
注意：Set结构没有键名只有键值，或者说键名和键值是同一个值
```JS
const s = new Set(); // 实例
// 数组去重
const arr = [1, 2, 3, 1, 2];
arr.forEach(v => {
  s.add(v);
});
const newArr = [...s];
console.log(newArr);
```

**Map类型**
---
ES6新增的数据结构，类似对象，键可以是任何数据类型  
实例方法
* size 成员总数
* set() 设置键值对
* get() 获取值
* has() 判断键是否存在，返回bool
* delete() 移除某个键，返回bool
* clear() 清空所有键值对
遍历
* keys() 返回键名遍历器
* values() 返回键值遍历器
* entries() 返回键值对遍历器
* forEach() 使用回调函数遍历每个成员
```JS
const m = new Map();
m.set(NaN, 123);
console.log(m.get(NaN)); // 123
```

**Symbol**
---
ES6新增的原生数据类型，表示独一无二的值  
类似于字符串，主要作为对象的键来使用  
Symbol函数不能使用 new 操作符，接收参数只为了控制台打印方便区分
```JS
// 没有参数的情况
let s1 = Symbol();
let s2 = Symbol();
s1 === s2 // false

// 有参数的情况
let s1 = Symbol('foo');
let s2 = Symbol('foo');
s1 === s2 // false

// 作为键使用
const s = Symbol();
const obj = {
  [s]: 123
};

// 获取键的方法
const keys = Object.getOwnPropertySymbols(obj); // isArr
console.log(keys); // [Symbol()]
```

**Iterator**
---
主要提供给 for...of 循环内部使用  
原生数据结构使用通过 [Symbol.iterator] 属性访问  
原生具备 Iterator 接口的数据结构如下
* Array
* Map
* Set
* String
* argument
* NodeList
获得遍历器对象后可通过 next() 方法获取代表当前成员的信息对象
```JS
// 数组
const arr = [1, 2, 3];
const res = arr[Symbol.iterator]();
console.log(res.next()); // {value: 1, done: false}
console.log(res.next()); // {value: 2, done: false}
console.log(res.next()); // {value: 3, done: false}
console.log(res.next()); // {value: undefined, done: true}
```

**class**
---
类相当于实例的原型，所有在类中的方法都会被实例继承  
静态方法 使用static关键字标记，表示该方法不能被实例继承，只能通过类进行调用  
ES6 规定 class 内部只有静态方法， 没有静态属性
```JS
/****************_静态方法_****************/
class Test {
  static handler() {}
}
/****************_父类静态方法给子类继承_****************/
class P {
  static handler() {
    console.log('_P_');
  }
}
class C extends P {
  // 重名覆盖
  static handler() {
    console.log('_C_');
  }
}
C.handler(); // _C_
```