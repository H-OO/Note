# **npm 发布资源包**

## **注册一个 npm 账号**

`https://www.npmjs.com`

## **新建**

- 新建项目目录，`npm init`生成`package.json`文件

```
name 项目名(默认文件夹名称)
version 版本(默认1.0.0)
description 项目描述(选填)
entry 入口文件(默认根目录下的index.js)
test 测试命令(选填)
git 远程仓库(选填)
keywords 搜索关键字(选填)
author 作者(选填)
license 开源协议(默认ISC)
```

## **发布第三方包**

目录结构

```
|- lib
|- index.js
|- package.json
```

```js
// ./index.js
module.exports = require('./lib/xxx.js');
```

## **发布命令**

- `npm login` 先登录
- `npm adduser` 为电脑添加发布授权
- `npm publish` 发布

## **注意事项**

npm规定发布包超过24小时就不能删除了，并且删除后未过24h不能重新发布同名包

删除命令 `npm unpublish project-name --force` (<24h)

不再维护提示 `npm deprecate project-name` (>24h)

## **发布可运行 node 命令的脚本**

目录结构

```
|- bin
    |- dev.js
|- package.json
```

```json
// package.json
{
  "bin": {
    "dev": "./bin/dev.js"
  }
}
```

```js
// bin/dev.js
#!/usr/bin/env node
console.log('Link success..');
```

**全局链接安装(本地测试)**

`npm link`

```
路径映射
/usr/local/bin/dev -> /usr/local/lib/node_modules/t-v1002/bin/dev.js
/usr/local/lib/node_modules/t-v1002 -> /Users/ooh/Desktop/t-v1002
```
依赖的其他第三方会自动下载

**测试使用**

终端输入`dev`，打印'Link success..'

**移除全局链接**

- 包目录下 `sudo npm unlink`
- `cd /usr/local/bin/` -> `sudo rm dev`
