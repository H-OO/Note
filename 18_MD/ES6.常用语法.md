# ES6常用语法
**function\***
---
`function*`是函数生成器  
调用后不会立即执行正文，而是返回迭代器对象
```js
function* test(i) {
  yield i; // next()
  yield i + 10; // next()
  return 100; // next()
}
const t = test(1); // 迭代器对象
t.next(); // {value: 1, done: false}
t.next(); // {value: 11, done: false}
t.next(); // {value: 100, done: true}
```

