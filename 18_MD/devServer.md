## devServer

devServer属性

- host 项目启动的 IP 地址 `默认localhost`
- port 端口 `默认8080`
- open 自动打开浏览器
- openPage 打开的页面 `默认index.html`
- inline 自动刷新 `默认true`
- progress 构建进度条 `默认false`
- proxy 代理

## **proxy 解决跨域问题**

proxy 是基于 `http-proxy-middleware` 的封装

案例

```js
{
  host: '10.0.0.0', // 本机公网IP
  port: 3000, // 项目运行端口
  proxy: {
    '/api': {
      target: 'http://localhost',
      secure: false, // 接受https
      changeOrigin: true // [默认false] changes the origin of the host header to the target URL
    }
  }
}

// 正确路径
const url = '/api/login.php';
// 错误路径
const url = 'http://10.0.0.0:3000/api/login.php';
// 请求
xhr.open('GET', url);
```

proxy 配置属性解析

- target 重定向域名
- secure: false (默认为 true 不接受 https，且使用了无效证书的后端服务器)
- changeOrigin: true (目前理解是：本地会虚拟一个服务端接收你的请求并为代发你的请求)

案例解析

- 首先，路径`/api/login.php`会自动补全地址信息，其实等价于`http://10.0.0.0:3000/api/login.php`
- 但是，内部使用了`http-proxy`进行重定向，`/api/login.php`最终等价于`http://localhost/api/login.php`
- 也就是说，`/api`实际上会被重定向为`http://localhost`
