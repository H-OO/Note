## **es6 模块与 commonjs 模块的区别**

**es6 模块**

```js
// 方式1 --------------------
// -> 声明
function test() {}
// -> 导出声明(只能导出一个声明)
export default test; // {__esModule: true, default: f test()}
// 方式2 --------------------
// -> 声明+导出(可导出多个声明)
export function foo() {} // {__esModule: true, test: f}
export var obj = {}; // {__esModule: true, obj: {}}

// -> 导入
import { foo } from 'test'; // 按需
import * as all from 'test'; // 全部
```
注意：`export`需要写在声明语句前

`export default`导出的是一个对象  
- 含有`__esModule`属性，属性值为`true`
- 含有`default`属性，属性值为导出的内容

`export`导出的是一个对象  
- 含有`__esModule`属性，属性值为`true`
- 其他属性成员为模块导出的声明

**commonjs 模块**

```js
// -> 模块方法 `test.js`
function test() {}
// -> 导出
module.exports = test; // 直接
// -> 导入
var test = require('test'); // test ifFunc
```

小结:  
es6 模块导出导入语法: `export default / import from` (正规军)  
commonjs 模块导出导入语法: `module.exports = / require()` (受欢迎)

## **commonjs 模块中 exports 和 module.exports 的区别**

- `module.exports` 初始值为一个空对象
- `exports` 是指向 module.exports 的引用
- require() 返回的是 module.exports 而不是 exports

注意：当 module.exports 重新赋值时，`exports` 会与 `module.exports` 断开引用关系

所以，当需要对 module.exports 重新赋值时，正确的方式是`exports = module.exports = {};`

## **commonjs 模块按需导入**

模块通过`index.js`加工包裹后进行导出

```
目录结构
|- move
    |- index.js
    |- move.js
```

```js
// move.js
class Move() {}
module.exports = new Move();
```
```js
// -> 导出
// index.js
Object.defineProperty(exports, '__esModule', {
  value: true
}); // {__esModule: true}
exports.move = undefined;
function _interopRequireDefault(obj) {
  return obj && obj.__esModule ? obj : { default: obj };
}
var _move = require('./move');
exports.move = _move; // {__esModule: true, move: {}}
// -> 导入
import { move } from 'lib/move';
```
