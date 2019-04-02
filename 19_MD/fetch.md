# fetch

Fetch API 是基于 Promise 设计，

兼容性：旧浏览器不支持 Promise，需要使用 polyfill es6-promise

## 语法

```js
// async/await (ES7)
async function getData() {
  try {
    let response = await fetch(url)
    let data = await response.json()
    console.log(data)
  } catch (e) {
    console.log('Oops, error', e)
  }
}
```

## 优点

- 语法简洁，更加语义化
- 标准 Promise 实现，支持 async/await
- 同构方便，使用`isomorphic-fetch`

## 体验

```js
// 一个简单的POST请求
fetch(url, {
  method: 'POST',
  credentials: 'include' // 携带 cookie
  headers: {
    'Content-Type': 'application/x-www-form-urlencoded;charset=UTF-8' // POST请求兼容性处理
  },
  body: Object.keys({ name: 'test' })
    .map(key => {
      return encodeURIComponent(key) + '=' + encodeURIComponent(params[key])
    })
    .join('&')
})

// POST 一个 JSON 到服务端
fetch(url, {
  method: 'POST',
  credentials: 'include' // 携带 cookie
  headers: {
    'Content-Type': 'application/json;charset=UTF-8'
  },
  body: JSON.stringify({name: 'test'})
})
```

**注意**

`body`字段一长串代码的作用：因为 fetch 是一个 low-level 的 API，所以你需要自己 encode HTTP 请求的 payload

需要自己指定 HTTP Header 中的`Content-Type`字段

`fetch`默认不携带`cookie`

## 处理异常

**什么是异常**

```js
// 模拟一个错误的请求
fetch('1.png')
  .then(() => {
    console.log('ok')
  })
  .catch(() => {
    console.log('err')
  })
// 由于请求路径问题，期望打印`err`
// 实际打印`ok`!!
```

为什么会打印 ok 呢

因为`fetch`只有在遇到网络错误的时候才会`reject`这个`Promise`，例如用户断网或者域名无法解析等

只要服务器能够返回`HTTP响应`(甚至是 CORS preflight 的 OPTIONS 响应)，`Promise`一定是`resolved`的状态

**如何处理异常**

首先要判断`fetch`请求是否成功，可以通过`response.ok`这个字段

```js
fetch()
  .then(response => {
    if (response.ok) {
      console.log('ok')
    } else {
      console.log('err')
    }
  })
  .catch(() => {})
```
