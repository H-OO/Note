# JS高级程序设计

目的：全面了解JS，其本质、历史、局限性

JS由三个部分构成：ECMAScript、DOM、BOM

## 第一章

**ECMAScript**
---
由 ECMA-262 定义，提供核心语言功能

**DOM 文档对象模型**
---
DOM(Document Object Model)是用于HTML的应用程序编程接口(API，Application Programming Interface)

通过 DOM 获取 HTML 的树形结构图，从而控制页面内容

DOM 提供的API能让开发人员轻松删除、添加、替换或修改任何节点

DOM1 主要是映射文档的结构

DOM2 扩展了事件、范围、遍历等细分模块；增加了对 CSS 的支持

DOM3 扩展了统一加载、保存和验证文档的方法

**BOM 浏览器对象模型**
---
BOM(Browser Object Model)是用于控制浏览器显示页面以外的部分

提供：

* 弹出新浏览器窗口的功能
* 移动、缩放和关闭浏览器窗口的功能
* 提供浏览器详细信息的 navigator 对象
* 提供浏览器所加载页面详细信息的 location 对象
* 提供用户显示器分辨率详细信息的 screen 对象
* 对 cookies 的支持

## 第二章

**script 元素**
---
HTML 为 script 元素定义了【5个】可选属性：  
* async 规定脚本将被异步执行
* charset 规定在外部脚本文件中使用的字符编码
* defer 外部脚本可以延迟到文档完全被解析和显示之后再执行
* src 要执行代码的外部文件路径
* type 脚本语言的内容类型，默认text/javascript

只要 script 元素不包含 async 和 defer 属性，浏览器都会按照 script 元素的位置依次进行解析

HTML 使用外部文件的优点：  
* 可维护性
* 可缓存（浏览器具有缓存外部JS文件的功能；也就是说两个页面都使用同一个文件，这个文件只需下载一次）
* 适应未来

**noscript 元素**
---
noscript 元素只有在浏览器将脚本禁用时显示

## 第三章

**严格模式**
---
'use strict'; // 浏览器将开启严格模式解析代码

目的是消除JS语法不合理不严谨的地方，提高编译器效率；ie10+支持

**typeof**
---
注意：
* typeof null 返回 'object'
* typeof [] 返回 'object'

**Number e表示法**
---
3.125e7 等于 31250000  
3e-7 等于 0.0000003  

e 的实际含义就是 3.125乘以10^7

**Number 精度**
---
注意：当数值长度或浮点数值超过17位时
* 123456789123456789123 // 123456789123456780000
* 0.123456789123456789123 // 0.12345678912345678

**Number 范围**
---
ECMAScript 能够表示的数值范围
* 最小数值保存在 Number.MIN_VALUE 中，大多浏览器的值为 5e-324  
* 最小数值保存在 Number.MAX_VALUE 中，大多浏览器的值为 1.7976931348623157e+308
如果某次计算结果超过JS数值范围的值，那么这个数值将自动转换成特殊的 ±Infinity值 (±无穷)

**Number 方法**
---
* Number.parseInt() // 默认以十进制解析，传参数二可改成其他进制进行解析
* Number.parseFloat() // 字符串转数字，保留浮点数
* toString() // 传递参数返回进制数值的字符串
* toFixed() // 保留几位小数

**String 字面量**
---
* \n 换行
* \b 空格

**String**
---
API
* charAt 根据索引返回字符
* charCodeAt 单个字符转 Unicode
* concat 连接字符串
* indexOf 正向检索字符串，返回字符串下标
* lastIndexOf 反向检索字符串，返回字符串下标
* match 正则检索，返回匹配字符、索引、整个字符串
* replace 替换
* search 正则检索，返回索引
* slice 切割
* split 字符串转数组
* substr 选取字符串，参数1为起始下标，参数2为字符个数
* substring 切割
* toLocaleLowerCase 字母字符串转小写
* toLocaleUpperCase 字母字符串转大写
* toLowerCase 字母字符串转小写
* toUpperCase 字母字符串转大写
* toString 返回自身
* trim 去首尾空格
* valueOf 返回自身

数值、布尔值、对象和字符串值都 toString 方法  
null 和 undefined 值没有 toString 方法  

注意：
* {}.toString() 打印结果为 [object Object]
* [1, 2, 3].toString() 打印结果为 '1,2,3'

```
slice substr substring 异同点

相同点：
当传入一个参数时，都根据传入的参数作为下标位置开始正向选取

不同点1:
当传入一个负值参数时
slice与substr是以计算公式：字符串长度+负值参数，进行反向选取
substring则是返回完整的字符串

不同点2：
当传入两个参数时
slice与substring是以区间[参数1,参数2)进行选取
substr则是将参数2作为选取的字符个数，等同于[参数1,参数2]进行选取
```

**Object 原型方法**
---
new Object() // 实例化后对象拥有 Object 所有的成员
* constructor 访问实例的构造函数
* hasOwnProperty 访问实例的属性是否在其构造函数中，isFunc 返回 Bool 值
* isPrototypeOf 检测原型是否一致， isFunc 返回 Bool 值
* propertyIsEnumerable 实例的属性是否能使用 for-in 语句，isFunc 返回 Bool 值
* toLocaleString 
* toString 
* valueOf 返回自身

**构造函数、原型对象、实例化**
---
```js
// 构造函数
function Example() {
  this.content = {};
}
console.log(Example.prototype); // 构造函数访问自身原型对象

const example = new Example(); // 实例化
console.log(example); // 实例
console.log(example.constructor); // 实例访问其构造函数
console.log(example.__proto__); // 实例访问其原型对象
```

**call、apply**
---
作用：继承其他构造函数实例化后对象的属性，两者作用相同  
参数一都是改变其他构造函数的 this 指向  
* apply 方法参数二接收一个数组或伪数组，方法内部会自行遍历
* call 方法则接收数组或伪数组遍历出来后的结果，每一项以逗号隔开

**一元操作符**
---
++ -- + -
* 前++/-- 在该行语句执行前进行++/--
* 后++/-- 在该行语句执行后进行++/--
* +/- 可进行隐式转换

**布尔操作符**
---
* ! 取反
* !! 快速转换成布尔值
* && 找假 例如：true && false => false
* || 找真 例如：false || true => true

**相等操作符**
---
ECMAScript 提供两组操作符：
* 相等与不相等---先转换再比较
* 全等与不全等---仅比较而不转换

注意：  
null == undefined // true  
null == 0 // false  
undefined == 0 // false  

**逗号操作符**
---
使用逗号操作符可以在一条语句中执行多个操作  
```js
// 声明多个变量
let a = 1, b = 2, c = 3;
// 赋值
let num = (1, 2, 3); // 3
```

**do-while**
---
```js
do {
  // 循环体（至少执行一次）
} while (false)
```

**while**
---
```js
while (false) {
  // 循环体
}
```

**break、continue**
---
* break 使用场景：当满足某个条件时退出循环（完全退出循环）
* continue 使用场景：当满足某个条件时跳过当前循环（只退出当前循环）

## 第四章

**typeof、instanceof 操作符**
---
* typeof 判断值类型
* instanceof 判断是否在原型链上

注意：  
typeof 无法精确识别 [] null /xxx/ 类型，都返回 'object'  
instanceof 可以判断该实例是否为该构造函数实例化出来的  
```js
[] instanceof Array; // true
{} instanceof Object; // true
/x/ instanceof RegExp; // true
```

**执行环境**
---
根据 ECMAScript 实现所在的宿主环境不同，表示执行环境的对象也不一样  
Web 浏览器中，全局执行环境被认为是 window 对象  
因此所有全局变量和函数都是作为 window 对象的属性和方法创建的  
某个执行环境中的所有代码执行完毕后，该环境被销毁，保存在其中的所有变量和函数定义也随之销毁

全局执行环境直到应用程序退出--例如关闭网页或浏览器--时才会被销毁

每个函数都有自己的执行环境  
当执行流进入一个函数时，函数的环境就会被推入一个环境栈中。  
而在函数执行之后，栈将其环境弹出，把控制权返回给之前的执行环境。  

**作用域**
---
当代码在一个环境中执行时，会创建变量对象的一个作用域链  
作用域链的用途，是保证对执行环境有权访问的所有变量和函数的有序访问  
作用域链的前端，始终都是当前执行的代码所在环境的变量对象

## 第五章

**Array**
---
API
* concat 拼接数组
* every 遍历数组，判断所有元素是否满足同一条件，返回 Bool
* filter 遍历数组，返回满足条件的元素组成的数组
* forEach 遍历数组，无返回值
* includes 判断数组中是否包含指定值，返回 Bool
* indexOf 正向获取元素下标
* join 转成以其参数进行分割的字符串
* lastIndexOf 反向获取元素下标
* map 遍历数组，将返回的内容用新的空数组进行接收
* pop 移除数组末项 【原】
* push 最末端追加全部参数 【原】
* reduce 累加器，数组从左往右累加
* reduceRight 累加器，数组从右往左累加
* reverse 反转数组 【原】
* shift 移除数组首项 【原】
* slice 切割数组
* splice 切割或拼接替换数组 【原】
* sort 数组升序或降序
* some 遍历数组，检测是否有元素满足条件，返回 Bool
* toString 转成以逗号分割的字符串
* unshift 最前端追加全部参数 【原】
* valueOf 返回自身

**栈**
---
栈是一种LIFO（Last-In-First-Out）的数据结构
```
In -↓-↑- Out
```

**队列**
---
队列是一种FIFO（First-In-First-Out）的数据结构
```
-↓- In
 -
-↓- Out
```

**数组排序**
---
```js
const arr = [2, 1, 3, 0, 4];

// 冒泡排序
function bubbleSort(arr) {
  for (let x = 0, l = arr.length; x < l; x++) {
    for (let y = x; y < l; y++) { // let y = x (优化)，比较过的元素无需再比
      if (arr[x] > arr[y]) { // 升序 > | 降序 <
        const tmp = arr[x];
        arr[x] = arr[y];
        arr[y] = tmp;
      }
    }
  }
  return arr;
}

// 快速排序
function quickSort(arr) {
  if (arr.length <= 1) {
    return arr;
  }
  const pivotIndex = Math.floor(arr.length / 2);
  const pivot = arr.splice(pivotIndex, 1)[0];
  const left = [];
  const right = [];
  for (let i = 0, l = arr.length; i < l; i++) {
    console.log(arr[i]);
    if (arr[i] < pivot) {
      left.push(arr[i]);
    } else {
      right.push(arr[i]);
    }
  }
  return quickSort(left).concat([pivot], quickSort(right)); // 升序
  // return quickSort(right).concat([pivot], quickSort(left)); // 降序
}
```

**Date**
---
API
* getTime() 13位时间戳，单位ms
* getFullYear() 年
* getMonth() 月 语言规定返回0至11，即0表示1月
* getDate() 日
* getHours() 时
* getMinutes() 分
* getSeconds() 秒
* getDay() 星期几
* getMilliseconds() 3位时间戳，单位ms
* getTimezoneOffset() 本地时间与UTC时间相差的分钟数 中国东八区-480 美国西五区300

**RegExp**
---
API
* exec() 接收字符串，返回数组，包含选中的字符串、字符串的起始位置下标、整个字符串
* test() 接收字符串，返回 bool

修饰符
* g 全局，应用于所有字符串 global
* i 不区分大小写 case-insensitive
* m 多行模式 multiline

表达式
* [] 范围写法，[0-9]、[a-zA-Z0-9]、[\w\.]
* | 其中任意一个规则匹配
* () 分组符，为单独一项设置匹配规则

元字符
* \d 数字
* \D 非数字
* \s 空格
* \S 非空格
* \w 字母数字下划线
* \W 非字母数字下划线

量词
* \* {0,} 可以不出现
* \+ {1,} 至少出现一次
* ? {0,1} 不出现或仅出现一次

支持符号
* . 除换行符以外的其他单个字符
* ^ 开始
* $ 结束

取反
* [^] 例如[^a-z] 忽略a-z的字符串

```js
// 例子1
const str = 'http://www.baidu.com/a/b.html';
// 截取域名
str.match(/\/\/[\w\.]+/)[0].replace(/\//g, '') // 'www.baidu.com'
// 截取文件名
str.match(/[\w]+\.html/)[0].match(/\w+/)[0] // 'b'

// 例子2
const str = 'http://www.baidu.com/a/b.html?username=1&password=123';
// 截取序列化后的参数变为对象形式
const res = str.match(/\?.*/)[0].replace(/\?/, '').split(/&/);
const param = {};
res.forEach((item, i) => {
  const tmp = item.split(/=/);
  param[tmp[0]] = tmp[1];
});
console.log(param); // {username: "1", password: "123"}

// replace $
const str = '15012341234';
const res = str.replace(/(\d{3})(\d{0,4})(\d{0,4})/, '$1-$2-$3');
console.log(res); // 150-1234-1234

// 包裹不确定字符
const str = `$t(Aa._01)aaa$t(Aa._02)`;
const res = str.match(/\$t\([\.\w]*\)/g);
console.log(res); // ['$t(Aa._01)', '$t(Aa._02)']

// 获取计算属性 transform 的 translate X 与 Y
const str = 'matrix(1, 0, 0, 1, -100, 0)';
const res = str.match(/[^a-z\(\)]+/)[0].split(/\,/).slice(-2); // ['-100', '0']

// 获取唯一结构中的内容
const str = ',{"type":"ping"},';
console.log(str);
let res1 = str.match(/\{"type":"(.+)\"}/)[1]; // 方式1 只匹配双引号
let res2 = str.match(/{.{1}type.{1}:.{1}(.+).{1}}/)[1]; // 方式2 不规定单双引号
console.log(res1); // ping
console.log(res2); // ping
```
match 方法返回一个数字，包含正向匹配、反向匹配的字符串（注意不要使用修饰符g）

**Function**
---
函数没有重载，重复声明两个同名函数，只有最后一个函数会生效

**Function内部属性**
---
函数内部两个特殊的对象
* arguments 伪数组，用于接收传入的所有参数
* this 默认指向使用对象，该指向会受 new call() apply() bind() 影响

**Math**
---
API 常用
* abs() 绝对值
* ceil() 向上取整
* floor() 向下取整
* max() 选出一组数值的最大值
* min() 选出一组数值的最小值
* PI 获取圆周率 π
* random() 获得大于等于0小于1的一个随机数
* round() 四舍五入取整

## 第六章

**面向对象**
---
目的：理解对象属性、创建对象、继承

ECMAScript 中有两种属性：数据属性和访问器属性

**数据属性**
---
4个描述其行为的特性
* [[Configurable]] 能否通过 delete 操作符删除属性，默认值为 true
* [[Enumerable]] 该属性能否 for-in 遍历获得，默认值为 true
* [[Writable]] 能否修改属性的值，默认为 true
* [[Value]] 保存属性值，在该位置进行读写操作，默认值为 undefined

想修改属性默认的特性，需要使用 Object.defineProperty() 方法
```js
// 该方法接收三个参数：属性所在的对象、属性名、描述符对象
// 描述符对象的属性必须是： configurable 、 enumerable 、 writable 、 value
const person = {};
Object.defineProperty(person, 'name', {
  writable: false,
  value: 'H_OO'
});
person.name = ''; // 非严格模式为无效操作 严格模式会报错
console.log(person.name); // 'H_OO'
```
注意：建议不要在 IE8 使用Object.defineProperty() 方法

**访问器属性**
---
* [[Configurable]] 
* [[Enumerable]] 
* [[Get]] 在读取属性时调用的函数
* [[Set]] 在写入属性时调用的函数

访问器属性不能直接定义，需要使用 Object.defineProperty() 方法
```js
const data = {
  _test: null
};
// Vue原理
const vm = (obj, property) => {
  Object.defineProperty(obj, property, {
    get: function () {
      return this['_' + property]
    },
    set: function(newValue) {
      this['_' + property] = newValue;
      console.log('修改视图...');
    }
  });
  return obj;
}
// 设置属性
const state = vm(data, 'test');
console.log(state['test']); // null
state['test'] = 'xxx'; // 重新赋值
console.log(state['test']); // xxx
```

**定义多个属性**
---
注意运行环境
```js
const book = {};
Object.defineProperties(book, {
  _year: {
    value: 2004
  },
  edition: {
    value: 1
  },
  year: {
    get: function () {
      return this._year;
    },
    set: function (newValue) {
      if (newValue > 2004) {
        this._year = newValue;
        this.edition += newValue - 2004;
      }
    }
  }
});
```

**创建对象-工厂模式**
---
通过 new Object() 创建一个空对象，再对该对象定制属性，通过参数进行赋值
```js
function createPerson (name, age, job) {
  const o = new Object();
  o.name = name;
  o.age = age;
  o.job = job;
  return o;
}
const person1 = createPerson('YY', 17, 'WEB');
const person2 = createPerson('OO', 17, 'H5C3ES6');
```

**创建对象-构造函数模式**
---
通过函数、this、new操作符创建对象
```js
function Person (name, age, job) {
  this.name = name;
  this.age = age;
  this.job = job;
  // 不需要 return 也会将自身返回
}
const person1 = new Person('YY', 17, 'WEB');
```

**创建对象-原型模式**
---
通过函数的 prototype 属性访问构造函数原型对象添加成员，原型对象上的成员是被实例共享的
```js
function Person () {}
Person.prototype.sayHi = () => {};
const person1 = new Person();
const person2 = new Person();
console.log(person1.sayHi === person2.sayHi); // true
```
因为创建了一个新函数，就会根据一组特定的规则为该函数创建一个 prototype 属性  
prototype 属性指向函数的原型对象  
原型对象都会自动获得一个 constructor 属性  
constructor 属性包含一个指向 prototype 属性所在函数的指针

注意：实例对象添加与原型对象相同的属性时，不会影响原型对象上的属性  
因为实例在使用某个属性时，先会在当前实例中查找，查不到再去原型对象中查找  
如果原型对象中也没有，则返回 undefined

**创建对象-构造函数模式与原型模式**
---
在 ECMAScript 中使用最广泛、认同度最高的一种
```js
function Person () {}
Person.prototype = {
  constructor: Person, // 替换了原有的原型对象 需要重新添加 constructor 属性
  sayHi: function () {
    console.log(this); // this 指向
  }
};
const p1 = new Person();
```

**创建对象-动态原型模式**
---
保留构造函数模式与原型模式的优点，将所有的信息都封装在构造函数中  
也就是说，通过检查某个应该存在的方法是否有效，来决定是否需要初始化原型
```js
function Person () {
  this.name = 'yy';
  // 只需检查其中一个即可
  if (typeof this.sayHi != 'function') {
    // 只执行一次
    Person.prototype.sayHi = function () {
      console.log(this);
    }
  }
}
const p1 = new Person();
```
注意：在已经创建了实例的情况下重写原型，会切断现有实例与新原型之间的联系

**创建对象-寄生构造函数模式**
---
思想：创建一个函数用来封装创建对象的代码，然后再返回新创建的对象
```js
function Person (name) {
  const o = new Object();
  o.name = name;
  o.sayHi = function () {
    console.log(this.name);
  }
  return o;
}
const p1 = new Person('yy');
p1.sayHi(); // yy
```
应用场景
```js
// 创建一个具有额外方法的特殊数组
function SpecialArray () {
  const arr = new Array();
  arr.push.apply(arr, arguments);
  arr.toPipedString = function () {
    return this.join('|');
  }
  return arr;
}
const arr = new SpecialArray(1, 2, 3); // 获得特殊数组
const res = arr.toPipedString(); // 使用特殊数组的方法
console.log(res); // 1|2|3
```

**创建对象-稳妥构造函数模式**
---
特征：构造函数内部不适用 this，使用构造函数不使用 new 操作符  
适用于安全环境中，这些环境会禁止使用 this 和 new
```js
function Person (name) {
  const o = new Object();
  o.sayName = function () {
    console.log(name);
  }
}
const p = Person('YY'); // 不使用 new
```

**继承**
---
许多OO语言都支持两种继承方式：接口继承、实现继承  
* 接口继承 只继承方法签名
* 实现继承 继承实际的方法

由于 ECMAScript 函数没有签名，所以不能实现接口继承  
ECMAScript 只支持实现继承，主要依靠原型链来实现

**原型链**
---
思想：利用原型让一个引用类型继承另一个引用类型的属性和方法

优点：实现继承

缺点：包含引用类型值的原型

**继承-借用构造函数继承**
---
借用构造函数继承也叫伪经典继承  
原理：通过apply() 和 call() 方法改变另一个构造函数的 this 指向
```js
// 将构造函数B的属性和方法继承给构造函数A的实例
function A() {
  B.call(this); // 继承
}
function B() {
  this.arr = [1, 2, 3];
}
const a1 = new A();
a1.arr.push(4);
console.log(a1.arr);
const a2 = new A();
console.log(a2.arr);
```
优点：可传递参数  
缺点：函数不可复用

**继承-组合继承**
---
组合继承指将原型链和借用构造函数的技术组合在一起  
思想：使用原型链实现对原型属性和方法的继承，而通过借用构造函数来实现对实例属性的继承  
做法：将简单类型和引用类型设置在构造函数中，function类型设置在原型对象中  
```js
function A() {
  B.call(this);
}
function B() {
  this.arr = [1, 2, 3];
}

B.prototype.sayArr = function () {
  console.log(this);
  console.log(this.arr);
};
A.prototype = new B(); // A原型继承B实例
// 原型链关系：a → a.__proto__ (b) → b.__proto__

const a1 = new A();
const a2 = new A();
console.log(a1.arr === a2.arr); // false
console.log(a1.sayArr === a2.sayArr); // true
```
优点：函数可复用  
缺点：B构造函数被调用了两次

**继承-原型式继承**
---
原生
```js
function object(o) {
  function F() {}
  F.prototype = o;
  return new F();
}
const person = {
  sayHi: function () {
    console.log('Hi');
  }
};
const p = object(person);
p.sayHi(); // Hi
```

使用 Object.create 方法 (IE9+)
```js
const person = {
  sayName: function () {
    console.log(this.name);
  }
};
const o1 = Object.create(person, {
  name: {
    value: 'yy'
  }
});
const o2 = Object.create(person, {
  name: {
    value: 'oo'
  }
});
o1.sayName(); // yy
o2.sayName(); // oo
console.log(o1.sayName === o2.sayName); // true
```

**继承-寄生式继承**
---
寄生式继承的思路与寄生构造函数和工厂模式类似，创建一个仅用于封装继承过程的函数  
```js
function object(o) {
  function F() {}
  F.prototype = o;
  return new F();
}
function createPerson(original) {
  const clone = object(original);
  clone.sayHi = function () {
    console.log('Hi');
  }
  return clone;
}
const original = {};
const p = createPerson(original);
console.log(p); // {sayHi: f}
```

**继承-寄生组合式继承**
---
通过借用构造函数来继承属性，通过原型链的混成形式来继承方法
```js
// 父类
function F(name) {
  this.name = name;
}
F.prototype.say = function () {
  console.log(this.name);
}
// 子类
function C() {
  F.apply(this, arguments); // 修改父类的this指向，继承属性
}
// 寄生组合式 ↓↓↓
// 生成新对象 
function object(o) {
  function O() {}
  O.prototype = o;
  return new O();
}
// 继承方法
function inheritPrototype(C, F) {
  const prototype = object(F.prototype); // 创建对象 （原生方法）
  // const prototype = Object.create(F.prototype); // 创建对象 （API方法）
  prototype.constructor = F; // 增强对象
  C.prototype = prototype; // 作为子类的原型
}
inheritPrototype(C, F);
// 寄生组合式 ↑↑↑
const c = new C('YY');
console.log(c);
c.say(); // YY
```

**小结**
---
创建对象的模式
* 工厂模式：通过原生对象构造函数生成对象，然后添加属性，最后return
* 构造函数模式：通过函数内部的this对象，生成对象使用 new 操作符
* 原型模式：通过prototype添加属性
* 组合模式：构造函数通过this用来设置属性，原型用来设置方法 【推荐】
* 动态原型模式：通过 typeof 检查某个方法是否有效，来决定是否初始化原型
* 寄生构造函数模式：想创建一个具有额外方法的特殊数组时
* 稳妥构造函数模式：函数内部不使用 this 对象，生成对象不使用 new 操作符

继承 (A子类 B父类)
* 原型链继承：A.prototype = new B()
* 借用构造函数继承：A构造函数内 B.call(this) 改变B构造函数的 this 指向
* 组合继承：将A构造函数的原型替换成B构造函数的实例，A构造函数内 B.call(this)
* 原型式继承：创建一个临时的构造函数，将传入的对象作为这个构造函数的原型
* 寄生式继承：与原型式紧密结合，接收原型式返回的对象，然后增强该对象后再返回
* 寄生组合式继承：A.prototype.__proto__ = B.prototype 【经典继承】

## 第七章

**递归**
---
```js
function factorial(num) {
  if (num <= 1) {
    return 1;
  } else {
    return num * factorial(num - 1);
  }
}
factorial(5); // 120
```
```
num = 5
|- 5 * factorial(4) // 5 * 24 = 120
|-       4 * factorial(3) // 4 * 6 = 24
|-             3 * factorial(2) // 3 * 2 = 6
|-                   2 * factorial(1) // 2 * 1 = 2
|-                         1
```

**闭包**
---
闭包是指有权访问另一个函数作用域中的变量的函数  
常见方式：在一个函数内部创建另一个函数  

* 使用闭包
```js
function outer () {
  const msg = 'Hi';
  return function () {
    console.log(msg);
  }
}
const p = outer(); // f inner () {}
p(); // Hi
```

* 释放内存
```js
function outer () {
  let num = 0;
  return function () {
    num++;
    console.log(num);
  }
}

let f = outer();
document.addEventListener('click', function (e) {
  f(); // 执行匿名函数
}, false)

document.addEventListener('dblclick', function (e) {
  f = null; // 回收
  f = outer(); // 重新创建
}, false)
```

**内存泄露**
---
IE9之前版本，如果闭包的作用域链中保存着一个 HTML 元素，那么就意味着该元素将无法被销毁
```js
// 泄露
function handler() {
  let ele = document.querySelector('#test');
  ele.onclick = function () {
    console.log(ele.id); // 存在对 ele 的引用
  }
}
handler();

// 解决方案
function handler() {
  let ele = document.querySelector('#test');
  const id = ele.id; // 
  ele.onclick = function () {
    console.log(id);
  }
  ele = null; // 内存回收
}
handler();
```

**匿名函数**
---
匿名函数的执行环境具有全局性，因此其 this 对象通常指向 window  
但是由于书写方式的不同，这一点可能不会那么明显
```js
// 对象中使用匿名函数 this 指向会丢失
const obj = {
  a: function () {
    return function () {
      console.log(this); // undefined
    }
  }
};
obj.a();
```

## 第八章
BOM

**window对象-窗口位置**
---
* screenX 浏览器与显示屏左边界的距离
* screenY 浏览器与显示屏上边界的距离

**window对象-窗口大小**
---
* outerWidth 浏览器整体宽度
* outerHeight 浏览器整体高度
* innerWidth 可视窗口宽度
* innerHeight 可视窗口高度

**window对象-导航**
---
* open() 跳转至开启一个新标签 参数1为[域名|本服务器HTML路径] 参数2为[_self|_blank]
* close() 关闭当前标签页

**定时器**
---
* setTimeout
* clearTimeout
* setInterval
* clearInterval

**系统对话框**
---
显示这些系统对话框，代码会停止执行，关闭后又恢复执行
* alert() 系统提示框
* confirm() 系统确认框 返回bool
* prompt() 系统输入框 返回输入内容

**location对象**
---
它提供了与当前窗口中加载的文档有关的信息，还提供了一些导航功能  
既是window对象的属性，又是document对象的属性  
https://github.com:80/H-OO/Note?username=H-OO#123
* hash #123
* host github.com:80
* hostname github.com
* href https://github.com:80/H-OO/Note?username=H-OO#123
* pathname /H-OO/Note
* port 80
* protocol https:
* search ?username=H-OO

**navigator对象**
---
navigator.userAgent 获取用户设备信息
* WeChat MicroMessenger
* 安卓 Android
* iOS iPhone
* iPad iPad
* PC 非以上则视为PC端
获取设备信息后，可先将字符串的字母转小写，再通过 indexOf() 进行检查

**screen对象**
---
表明客户端的能力，其中包括浏览器窗口外部的显示器的信息，如像素宽度和高度等
* availHeight
* availLeft
* availTop
* availWidth
* colorDepth
* height
* orientation
* pixelDepth
* width

**history对象**
---
出于安全方面的考虑，开发人员无法得知用户浏览过的 URL  
* back() 退后一页
* forward() 前进一页
* go() 传递number类型代表前进或后退几页，string类型则跳转最近的与参数匹配的页
* length 第一个页面 (history.length === 0)
* replace() 导航到一个新 URL，同时该 URL 会替换浏览器历史记录中当前显示的页面


## 第十章
DOM
```
Document
  |- html
      |- head
      |   |- title
      |- body
```

**Node类型**
---
Node.nodeType 获取节点类型 返回number类型 1~12  
节点类型由Node类型中定义的12个数值常量来表示，下列为常用类型：
* Node.ELEMENT_NODE // 1
* Node.ATTRIBUTE_NODE // 2
* Node.TEXT_NODE // 3
* Node.COMMENT_NODE // 8

**Node元素属性**
---
* childNodes 返回一个伪数组，包含所有子节点
* children 返回一个伪数组，包含所有子元素节点
* firstChild 第一个子节点
* firstElementChild 第一个子元素节点
* hasChildNodes() 是否拥有子节点 返回bool
* lastChild 最后一个子节点
* lastElementChild 最后一个子元素节点
* nodeName / tagName 获取大写的标签名

**Node子元素列表**
---
* nextSibling 下一个兄弟节点
* previousSibling 上一个兄弟节点
* parentNode 最近的父节点

**操作节点**
---
* appendChild() 用于向 childNodes 列表的末尾添加一个节点（排队）
* insertBefore() 参数1为新创建的节点 参数2为参照节点（插队）
* replaceChild() 参数1为新创建的节点 参数2为要替换的节点（替换）
* removeChild() 将需要移除的子节点传入
```js
// insertBefore 方法是将节点插入到参照节点的位置，参照节点往后挪一位
const ul = document.querySelector('ul');
const li = document.createElement('li');
// 第二个参数传null，则节点插入后成为最后一个子节点
ul.insertBefore(li, null); // 成为最后一个子节点，与appendChild方法作用相同
// 插入后成为第一个子节点
ul.insertBefore(li, ul.firstChild);
// 插入后成为最后一个子节点
ul.insertBefore(li, ul.lastChild);
```

**其他节点方法**
---
* cloneNode() 克隆节点，接收一个bool参数，表示是否深度克隆
```js
// cloneNode
const ul = document.querySelector('ul');
const deep = document.cloneNode(ul, true); // 复制节点及其整个子节点树
const shallow = document.cloneNode(ul, false); // 复制单个节点
```

**document**
---
* document.documentElement 获取html元素
* document.body
* document.title
* document.referrer 获取来源页面的URL，不包含hash或search部分
* document.URL 获取完整URL
* document.domain 获取域名（可设置）

**元素属性方法**
---
* attribute 元素属性对象
* getAttribute 获取元素属性
* removeAttribute 移除元素属性
* setAttribute 设置元素属性

**表格**
---
```html
<table border="1">
  <tbody>
    <tr>
      <td></td>
    </tr>
  </tbody>
</table>
```

**innerHTML**
---
不支持innerHTML的元素
* head
* html
* style
* table
* tbody
* thead
* tfoot
* tr

**script**
---
async 和 defer 的区别
```js
// 读到就加载并执行
<script src="x.js"></script>

// 加载和渲染后续文档元素的过程将和 x.js 的加载与执行并行进行（异步）
<script async src="x.js"></script>

// 加载和渲染后续文档元素的过程将和 x.js 的加载并行进行（异步）
// 但 x.js 的执行要在所有元素解析完成之后，DOMContentLoaded事件触发之前完成
<script defer src="x.js"></script>
```

**classList**
---
* add() // 添加类名
* contains() // 检查类名是否存在，返回bool
* remove() // 移除类名
* toggle() // 类名不存在就添加，存在就移除

**scrollIntoView方法**
---
控制页面滚动
* Element.scrollIntoView(true); // 元素滚动到顶部与窗口顶部平齐
* Element.scrollIntoView(false); // 元素滚动到底部与窗口底部平齐

**盒子模型**
---
盒子模型：margin+border+padding+content
* box-sizing: content-box; // content+border+padding（content、border、padding为平级关系）
* box-sizing: border-box; // content（content中包含了border与padding）

**client**
---
* clientWidth padding+content (pc)
* clientHeight padding+content (pc)

**offset**
---
* offsetLeft 与最近一个有定位的父元素的左侧距离
* offsetTop 与最近一个有定位的父元素的顶部距离
* offsetWidth border+padding+content (bpc)
* offsetHeight border+padding+content (bpc)

**scroll**
---
* scrollLeft 设置目标元素从视口元素左侧移出了多少px（边框不属于视口）
* scrollTop 设置目标元素从视口元素顶部移出了多少px （边框不属于视口）
* scrollWidth padding+content (pc)
* scrollHeight padding+content (pc)
PS: scrollLeft/scrollTop 默认是0（通过父元素进行设置和取值）
```js
// p 为父元素；c 为子元素
// 设置子元素向上偏移10px
p.scrollTop = 10;
// 获取子元素向上偏移量
console.log(p.scrollTop); // 10
```

**计算样式**
---
通过 getComputedStyle 方法获取 CSSStyleDeclaration 对象（只读）  
该方法可接收两个参数：参数1为要获取计算样式的元素，参数2为一个伪元素字符串（例如":after"，可选）  
PS: CSSStyleDeclaration 对象中的样式属性值为 string 类型，并会携带单位
```js
window.getComputedStyle(DOM, null); // 获取 DOM 计算样式  isObj
window.getComputedStyle(DOM, ':after'); // 获取 DOM 伪元素计算样式  isObj
// width
const w = window.getComputedStyle(DOM, null).width; // 0px
```

**JS事件机制**
---
分为三个阶段
* 捕获阶段
* 目标阶段
* 冒泡阶段

```js
// addEventListener
Element.addEventListener(event, fn, useCapture); // ie9+
// Element Dom
// 参数1 事件名 click
// 参数2 处理函数
// 参数3 是否在捕获阶段调用处理函数 默认为fasle(冒泡阶段) true(捕获阶段)

// attachEvent
Element.attachEvent(event, fn); // 低版本ie
// Element Dom
// 参数1 事件名 onclick
// 参数2 处理函数
// 默认在冒泡阶段调用处理函数
```

**阻止冒泡**
---
```js
// 兼容写法
window.event ? window.event.cancelBubble = true : e.stopPropagation();
```

**去除默认行为**
---
```js
// 兼容写法
window.event ? window.event.returnValue = false : e.preventDefault();
```

**UI事件**
---
* load 页面完全加载后触发
* resize 窗口大小变化触发
* scroll 滚动带滚动条的元素触发

**鼠标与滚轮事件**
---
* click
* dblclick
* mousedown 鼠标任意键按下触发
* mouseenter 鼠标移入触发（不冒泡）
* mouseleave 鼠标移出触发（不冒泡）
* mousemove 鼠标在元素内部移动重复触发
* mouseout 鼠标移入触发（冒泡）
* mouseover 鼠标移出触发（冒泡）
* mouseup 鼠标释放按钮触发
* mousewheel 鼠标滚轮滚动触发

**触摸设备**
---
在面向 iPhone 和 iPad 开发注意
* 不支持dblclick事件，双击浏览器窗口会放大界面，无法改变该行为
* 轻击触发事件顺序 mousemove → mousedown → mouseup → click
* mousemove 事件也会触发 mouseover 和 mouseout 事件
* 双指滑动触发 mousewheel 和 scroll 事件

**移动端touch事件**
---
* touchstart
* touchmove
* touchend
PS: touch事件都会冒泡
```
iOS and Android 事件触发顺序
// 轻击
touchstart → touchend → click
// 点击移动（没有click）
touchstart → touchmove → touchend
```

**mouse与touch事件触发顺序**
---
元素监听以下事件
* touchstart
* mouseover
* mousemove
* mousedown
* mouseup
* click
* touchmove
* touchend
```
// 轻击
touchstart
↓
touchend
↓
mouseover
↓
mousemove
↓
mousedown
↓
mouseup
↓
click

// 点击移动
touchstart
↓
mouseover
↓
mousemove
↓
touchmove
↓
touchend
```

**手势事件**
---
* gesturestart 当一个手指已经按在屏幕上而另一个手指又触摸屏幕时触发
* gesturechange 当触摸屏幕的任何一个手指的位置发生变化时触发
* gestureend 当任何一个手指从屏幕上面移开时触发

**键盘与文件事件**
---
* keydown 按下键盘任意键触发，按住不放会重复触发
* keypress 按下键盘字符键触发，按住不放会重复触发
* keyup 释放键盘任意键触发
* textInput 在可编辑区域中输入字符触发

**节点变动事件**
---
* DOMSubtreeModified DOM结构发生变化触发（在其他事件触发后触发）
* DOMNodeInserted 一个节点作为子节点被插入到另一个节点中触发
* DOMNodeRemoved 节点从其父节点中移除触发
* DOMNodeInsertedIntoDocument 一个节点被直接插入文档或通过子树间接插入文档之后触发（这个事件在 DOMNodeInserted 之后触发）
* DOMNodeRemovedFromDocument 一个节点被直接从文档中移除或通过子树间接从文档中移除之前触发（这个事件在 DOMNodeRemoved 之后触发）
* DOMAttrModified 在特性被修改之后触发
* DOMCharacterDataModified 在文本节点的值发生变化时触发

**浏览器事件**
---
* beforeunload 浏览器销毁页面之前触发
* DOMContentLoaded 形成完整的 DOM 树之后触发
* hashchange URL的hash值发生改变触发

**移除事件**
---
纯粹的DOM操作，例如 removeChild() 和 replaceChild() 方法，会移除事件  
如果是使用innerHTML替换子节点树，那么子节点树中的事件可能不会被移除
```js
// 使用innerHTML替换子节点树之前需要移除相关事件处理程序
Element.onclick = null; // 先移除事件处理程序
Element.innerHTML = ''; // 再移除子节点树
```

**剪贴板事件**
---
* beforecopy 复制操作前触发
* copy 复制操作时触发
* beforecut 剪切操作前触发
* cut 剪切操作时触发
* beforepaste 粘贴操作前触发
* paste 粘贴操作时触发

## 第21章

**XMLHttpRequest对象**
---
```js
const xhr = new XMLHttpRequest(); // 实例化
xhr.open('get', 'example.txt', true); // 接收三个参数，分别是：请求方式、接口地址，是否异步
// xhr.setRequestHeader('Content-type', 'application/x-www-form-urlencoded'); // 设置请求头
xhr.send(null); // 请求参数（对部分浏览器为必须项），无参传null
// 发送请求给服务器...
xhr.onreadystatechange = function () {
  if (xhr.readyState === 4 && xhr.status === 200) {
    // use xhr.responseText todosomething..
  }
}

// xhr.close(); // 关闭请求 在组件销毁之前检查是否存在超时的请求，在组件销毁之前关闭请求
// xhr.abort(); // 接收响应之前，xhr对象会停止触发事件（后续还需解除对xhr对象的引用）
```
收到服务器响应后，响应的数据会自动填充xhr对象的属性，相关属性：
* readyState 0~4状态
* responseText 作为响应主体被返回的文本
* status 响应的HTTP状态（请求响应先检查status属性）
* statusText HTTP状态的说明

**HTTP状态码**
---
* 1开头 临时响应并需要请求者继续执行操作
* 2开头 成功处理了请求
* 3开头 请求重定向
* 4开头 表示请求可能出错
* 5开头 服务器内部错误
```
常用状态码
100 继续
200 成功
302 重定向
304 缓存中取
401 未授权
403 拒绝请求
404 找不到
500 服务器错误
```

**readyState属性**
---
* 0 未初始化（未调用open方法）
* 1 启动（已经调用open方法，但未调用send方法）
* 2 发送（已经调用send方法，但尚未接收到响应）
* 3 接收（接收到部分响应数据）
* 4 完成（接收到全部响应数据）
监听 readyState 属性值的改变，使用 readystatechange 事件

**HTTP头部信息**
---
* Accept 浏览器能够处理的内容类型
* Accept-Charset 浏览器能够显示的字符集
* Accept-Encoding 浏览器能够处理的压缩编码
* Accept-Language 浏览器当前设置的语言
* Connection 浏览器与服务器之间连接的类型
* Cookie 当前页面设置的任何 Cookie
* Host 发出请求的页面所在的域
* Referer 发出请求的页面的 URI
* User-Agent 浏览器的用户代理字符串

**编码与解码**
---
```js
encodeURI('username=用户名'); // username=%E7%94%A8%E6%88%B7%E5%90%8D
decodeURI('username=%E7%94%A8%E6%88%B7%E5%90%8D'); // username=用户名

encodeURIComponent('username=用户名'); // username%3D%E7%94%A8%E6%88%B7%E5%90%8D
decodeURIComponent('username%3D%E7%94%A8%E6%88%B7%E5%90%8D'); // username=用户名

escape('username=用户名'); // "username%3D%u7528%u6237%u540D"
unescape('username%3D%u7528%u6237%u540D'); // username=用户名
```

**XHR传输FormData**
---
将 Content-Type 头部信息设置为 application/x-www-form-urlencoded  
也就是表单提交时的内容类型

**FormData对象**
---
主要为序列化表单以及创建与表单格式相同的数据（用于XHR传输）提供了便利  
情景1：获取表单元素上填入的数据
```html
<form action="" id="f">
  <label for="">Username:</label>
  <input type="text" name="username">
  <br>
  <label for="">Password:</label>
  <input type="text" name="password">
  <br>
  <input type="submit" value="Submit" id="submit">
</form>
```
```js
// 获取表单元素
const formEle = document.forms[0];
// submit按钮
const submit = document.querySelector('#submit');
// 填写完表单后点击submit
submit.addEventListener('click', e => {
  e.preventDefault(); // 阻止默认行为
  const data = new FormData(formEle); // 获取FormData对象
  console.log(data.get('username')); // 打印 username 对应的值
});
```
情景2：自定义表单数据，用于XHR传输
```js
// 创建空表单
const data = new FormData();
// 加入表单数据
data.append('username', 'xxx');
data.append('password', '123');
// 验证是否加入
console.log(data.get('username')); // xxx
console.log(data.get('password')); // 123
```
使用 FormData 的方便之处体现在不必明确地在 XHR 对象上设置请求头部  
XHR 对象能够识别传入的数据类型是 FormData 的实例，并配置适当的头部信息

**选择图片资源并在浏览器上显示**
---
window.URL.createObjectURL() 接收选中的资源，返回 blob 字符串
```html
<input type="file" id="file">
<img id="insert_img" width="300" height="400">
```
```js
const inputFile = document.querySelector('#file');
const insertImg = document.querySelector('#insert_img');
inputFile.addEventListener('change', e => {
  const target = e.target;
  // 取消操作也会触发change事件
  if (target.files.length === 0) {
    return;
  }
  // 获取 blob 字符串
  const url = window.URL.createObjectURL(target.files.item(0));
  // 设置图片路径
  insertImg.src = url;
});
```

**重写XHR响应的MIME类型**
---
```js
xhr.open('get', 'test.txt', true);
xhr.overrideMimeType('text/xml'); // 在send方法之前才能保证重写响应的MIME类型
xhr.send(null);
```

**进度事件**
---
客户端与服务器通信有关事件
* onabort 调用 abort() 方法而终止连接时触发
* onerror 请求发生错误时触发
* onload 完整接收响应数据时触发
* onloadstart 接收到响应数据的第一个字节时触发
* onprogress 接收响应期间持续不断触发

**progress事件**
---
三个额外的属性
* lengthComputable 进度信息是否可用的布尔值
* position 已经接收的字节数
* totalSize 根据Content-Length响应头部确定的预期字节数

可用来创建进度指示器功能
```js
xhr.onprogress = function (e) {
  if (e.lengthComputable) {
    // e.position
    // e.totalSize
  }
}
```

**跨域-图像Ping**
---
通过 onload 和 onerror 事件处理程序来确定是否接收到了响应
```js
const img = new Image();
img.onload = function (e) {}
img.onerror = function (e) {}
img.src = ''; // 设置接口路径
```
缺点：只能发送GET请求、无法访问服务器的响应文本

**跨域-JSONP**
---
通过 script 标签的 src 属性进行跨域  
将回调函数名保存在URL中 (?callback=handler) 传递给服务器
```js
// 浏览器注册回调函数
function handler (data) {
  // use data to do something..
}
// 后台返回js文件并加载运行
handler('相关信息');
```

**跨域-WebSocket**
---
实时通信，突破同源策略
```js
// 建立连接
const ws = new WebSocket('ws://www.xxx.com');
// 向服务器发送数据使用 send() 方法，并传入任意字符串
ws.send('Hello');
// 接收服务器数据使用 message 事件
wx.onmessage = function (e) {
  // 数据
  const data = e.data;
}
// 断开连接
ws.close();
```
注意：WebSocket只能发送纯文本，对于复杂的数据结构需要先要序列化成JSON字符串
* ws.OPENING 0 正在建立连接
* ws.OPEN 1 已经建立连接
* ws.CLOSING 2 正在关闭连接
* ws.CLOSE 3 已经关闭连接

连接生命周期不同阶段触发的事件：
* onopen 在成功建立连接时触发
* onerror 在发生错误时触发，连续不能持续
* onclose 在连接关闭时触发

onclose 事件的 event 对象有额外的三个属性：
* wasClean 布尔值，表示连接是否已经关闭
* code 服务器返回的数值状态码
* reason 服务器发回的消息

**安全-XSS攻击**
---
XSS指浏览器执行包含在普通元素标签内脚本

危害：这些包含在普通元素标签的脚本通常都是攻击者写入的，可恶意获取其他用户的隐私信息，例如cookie等

攻击方式：innerHTML + script  
情景：后端渲染存在  
原因：页面由后台返回，攻击的脚本已经被插入节点中  
防御处理：  
将包含`<script></script>`或`\u003cscript\u003e\u003c/script\u003e`的数据内容进行转义符替换  
转义后`&lt;script&gt;&lt;/script&gt;`  
注意：innerHTML 能将 unicode 码转成字符实体
```
|   <    |    >   | 实体括号
| \u003c | \u003e | unicode码
-------------------
|  &lt;  |  &gt;  | 转义符
```
```js
const str = "<script></script> \u003cscript\u003e\u003c/script\u003e";
const newStr = str.replace(/</g, '&lt;').replace(/>/g, '&gt;'); // 能处理实体括号和unicode码
console.log(newStr); // &lt;script&gt;&lt;/script&gt; &lt;script&gt;&lt;/script&gt;
```
PS:输出内容包含`javascript:`也需要进行转义处理  

攻击方式：innerHTML + img  
情景：前后端渲染都存在  
原因：innerHTML 能解析具有 img 标签结构的字符串  
防御处理：  
将包含`<img>`或`\u003cimg\u003e`的数据内容进行转义符替换  
转义后`&lt;img&gt;`
```js
// 使用 img 标签攻击
const ele = document.querySelector('.test');
ele.innerHTML = '<img src="" onerror="javascript:alert(1)">';
// 使用 unicode 码配合 img 标签攻击
const ele = document.querySelector('.test');
let data = '\u003cimg src="" onerror="javascript:alert(1);"\u003e';
ele.innerHTML = data;

// 防御
const ele = document.querySelector('.test');
let data = '\u003cimg src="" onerror="javascript:alert(1);"\u003e';
data = data.replace(/[\u003c]/g, '\\u003c').replace(/[\u003e]/g, '\\u003e');
ele.innerHTML = data;
```

攻击方式：innerHTML + URL search  
情景：前端渲染存在  
原因：从 URL 中获取 search 参数使用  
防御处理：获取 search 参数后，先进行转义
```js
// ?username=<img%20src=""%20onerror="javascript:alert(1);">
let data = decodeURIComponent(location.search); // 解码
// ?username=<img src="" onerror="javascript:alert(1);">

// 防御
data = data.replace(/</g, '&lt;').replace(/>/g, '&gt;').replace(/=/g, '&#61;');
// 使用
ele.innerHTML = data;
```

**安全-CSRF攻击**
---
跨站点请求伪造

危害：伪造用户请求进行敏感操作

攻击方式：抓包，获得 token，伪造用户请求  
防御处理：使用非对称加密算法，加密请求参数  
```js
// Multiple-precision.js
// Barrett-modular-reduction.js
// RSA.js
// 十六进制公钥
const rsa_n =
  "C34E069415AC02FC4EA5F45779B7568506713E9210789D527BB89EE462662A1D0E94285E1A764F111D553ADD7C65673161E69298A8BE2212DF8016787E2F4859CD599516880D79EE5130FC5F8B7F69476938557CD3B8A79A612F1DDACCADAA5B6953ECC4716091E7C5E9F045B28004D33548EC89ED5C6B2C64D6C3697C5B9DD3";
// 服务器端返回的 token
let token = 'xxxxxxxxxx';
// 加密 ↓↓↓
setMaxDigits(131); //131 => n的十六进制位数/2+3
const key = new RSAKeyPair("10001", '', rsa_n); // 10001 => e的十六进制
const _token = encryptedString(key, token); // 不支持汉字
// 加密 ↑↑↑
console.log(_token); // 0fe4cce62e82818c0d47dc3315494bc9a41721385e301e349344a81fe91b656665e99499ee4a2fc8115ae7d1d018ec84471ed5c263704af3a017303075f4b6c8575ddb617aecaa7a96a9a6d57df9285d0402ccb091ead3d69222aea8a21f6469928f4e2666cefc3d62e7cd6d9d75f3c1c088470dc2f7bb0c2120d50b3ff9983c
```
加密后还会存在安全问题：请求可能被重复提交，造成用户经济损失，这需要后台进行判断

攻击方式：token在登录时被抓取，攻击者使用各一个公钥加密信息伪造用户请求  
防御处理：  
登录接口参数添加浏览器指纹项，将请求参数进行公钥加密，发送给后台  
后台私钥解密将浏览器指纹保存，返回私钥加密后的token（浏览器指纹与该token进行绑定）  
敏感操作后台都需要验证浏览器指纹与初次登录提交的浏览器指纹是否一致  
不一致则中断该操作，并让 token 失效

**安全-开发注意**
---
* 开发时要提防用户产生的内容，要对用户输入的信息进行层层检测
* 要注意对用户的输出内容进行过滤(进行转义等)
* 重要的内容记得要加密传输(无论是利用https也好，自己加密也好)
* get请求与post请求，要严格遵守规范，不要混用，不要将一些危险的提交使用jsonp完成
* 对于URL上携带的信息，要谨慎使用

**优化-函数节流**
---
```js
const processor = {
  // 定时器
  timer: null,
  // 想执行的操作
  todo: function () {
    console.log('todo...');
  },
  // 节流：触发方法先清除上一个定时器
  process: function () {
    clearTimeout(this.timer);
    this.timer = setTimeout(() => {
      this.todo();
    }, 100)
  }
};

window.addEventListener('resize', e => {
  processor.process();
});
```
