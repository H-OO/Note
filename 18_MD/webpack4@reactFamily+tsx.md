# webpack4@react+typescript

目录结构

```
|- config
    |- vendor.alias.js
    |- webpack.base.js
    |- webpack.dev.js
    |- webpack.pro.js
|- public
    |- index.html
|- src
    |- index.tsx
|- .babelrc
|- package.json
|- postcss.config.js
|- tsconfig.json
```

## **起步**

- `npm init`
- `npm i -D webpack webpack-cli`
- `npm i -D webpack-dev-server webpack-merge`
- `npm i -D babel-loader @babel/core @babel/polyfill @babel/preset-env @babel/preset-react @babel/runtime`
- `npm i -D ts-loader typescript react react-dom @types/react @types/react-dom @types/es6-shim @types/webpack-env`
- `npm i -D tslint tslint-loader`

`@types/es6-shim` ES6 语法垫片  
`@types/webpack-env` 用于解决 `类型“NodeRequire”上不存在属性“ensure”。`

包版本

```json
{
  "webpack": "^4.19.1",
  "webpack-cli": "^3.1.0",
  "webpack-dev-server": "^3.1.9",
  "webpack-merge": "^4.1.4",
  "babel-loader": "^8.0.4",
  "@babel/core": "^7.1.2",
  "@babel/polyfill": "^7.0.0",
  "@babel/preset-env": "^7.1.0",
  "@babel/preset-react": "^7.0.0",
  "@babel/runtime": "^7.1.2",
  "ts-loader": "^5.2.1",
  "typescript": "^3.1.1",
  "react": "^16.5.2",
  "react-dom": "^16.5.2",
  "@types/react": "^16.4.14",
  "@types/react-dom": "^16.0.8",
  "@types/es6-shim": "^0.31.37",
  "@types/webpack-env": "^1.13.6",
  "tslint": "^5.11.0",
  "tslint-loader": "^3.5.4"
}
```

## **配置文件**

```json
// .babelrc
{
  "presets": [
    [
      "@babel/preset-env",
      {
        "useBuiltIns": "usage",
        "debug": false
      }
    ],
    "@babel/preset-react"
  ]
}
```

```json
// tsconfig.json
{
  "compilerOptions": {
    "outDir": "./dist/",
    "noImplicitAny": true,
    "module": "es6",
    "target": "es5",
    "jsx": "react",
    "allowJs": true
  }
}
```

```json
// package.json
{
  "scripts": {
    "start": "webpack-dev-server --config config/webpack.dev.js",
    "build": "webpack --config config/webpack.pro.js"
  }
}
```

## **相关 loader**

```js
const config = {
  module: {
    rules: [
      {
        test: /\.js$/,
        exclude: /node_modules/,
        use: ['babel-loader']
      },
      {
        test: /\.tsx?$/, // .ts .tsx
        exclude: /node_modules/,
        use: [
          {
            loader: 'ts-loader',
            options: {
              transpileOnly: true,
              experimentalWatchApi: true
            }
          }
        ]
      }
    ]
  }
};
```

## **清空目录文件插件**

`npm i -D clean-webpack-plugin`

插件误删保护机制导致无法删除指定文件夹的问题  
`path is outside of the project root`提示 path 在项目根目录之外(删除失败)  
需要解除误删保护机制，才能清空【上级目录】下的文件夹

```js
const config = {
  plugins: [
    new CleanWebpackPlugin(['dist'], {
      root: path.resolve(__dirname, '../'), // 通过改变root范围越过保护机制
      verbose: true // (true 测试/模拟删除，不删除文件) (false 删除文件)
    })
  ]
};
```

## **模板生成 HTML 文件并自动注入依赖包**

`npm i -D html-loader html-webpack-plugin`

```js
const HtmlWebpackPlugin = require('html-webpack-plugin');
const config = {
  plugins: [
    new HtmlWebpackPlugin({
      template: path.resolve(__dirname, '../public/index.html') // 模板路径
    })
  ]
};
```

## **处理样式**

`npm i -D style-loader css-loader postcss-loader autoprefixer sass-loader node-sass`

```js
// webpack.config.js
const config = {
  module: {
    rules: [
      {
        test: /\.css$/,
        exclude: /node_modules/,
        use: ['style-loader', 'css-loader', 'postcss-loader']
      },
      {
        test: /\.scss$/,
        exclude: /node_modules/,
        use: ['style-loader', 'postcss-loader', 'sass-loader']
      }
    ]
  }
};
module.exports = config;
```

## **样式自动添加前缀**

创建 postcss.config.js 文件

```js
// postcss.config.js
module.exports = {
  plugins: [
    require('autoprefixer') // 自动加前缀
  ]
};
```

解决样式不添加前缀问题  
主要由于高版本浏览器已经做了兼容处理，添加前缀主要为了低版本浏览器

```json
// package.json
{
  "browserslist": ["iOS >= 7", "Firefox >= 20", "Android >= 4"]
}
```

## **抽离样式**

将样式从 js 中抽离出来，生成 css 文件  
`npm i -D extract-text-webpack-plugin`

```js
// webpack.config.js
const ExtractTextPlugin = require('extract-text-webpack-plugin'); // version@next
const extractCSS = new ExtractTextPlugin('[name].[hash:6].css');
const extractSCSS = new ExtractTextPlugin('[name].[hash:8].css');
const config = {
  module: {
    rules: [
      {
        test: /\.css$/,
        exclude: /node_modules/,
        use: extractCSS.extract({
          fallback: 'style-loader',
          use: ['css-loader', 'postcss-loader']
        })
      },
      {
        test: /\.scss$/,
        exclude: /node_modules/,
        use: extractSCSS.extract({
          fallback: 'style-loader',
          use: ['css-loader', 'postcss-loader', 'sass-loader']
        })
      }
    ]
  },
  plugins: [extractCSS, extractSCSS]
};
```

注意：extract-text-webpack-plugin 版本需使用 4.x

## **压缩样式**

将抽离出来的样式进行压缩处理
`npm i -D optimize-css-assets-webpack-plugin cssnano postcss-safe-parser`

```js
// webpack.config.js
const OptimizeCSSAssetsPlugin = require('optimize-css-assets-webpack-plugin');
const config = {
  plugins: [
    new OptimizeCSSAssetsPlugin({
      assetNameRegExp: /\.css\.*(?!.*map)/g, // 注意不要写成 /\.css$/g
      cssProcessor: require('cssnano'), // css优化插件
      cssProcessorOptions: {
        discardComments: {
          removeAll: true // 移除所有注释
        },
        autoprefixer: false // 关闭cssnano的autoprefixer功能
      },
      canPrint: true // 设置是否可以向控制台打日志 默认为true
    })
  ]
};
module.exports = config;
```

## **加载图片与图标字体**

`npm i -D file-loader url-loader`

```js
// webpack.config.js
const config = {
  output: {
    publicPath: '' // 设置静态资源路径
  },
  module: {
    rules: [
      {
        test: /\.(png|svg|jpg|gif)$/,
        use: [
          {
            loader: 'url-loader',
            options: {
              limit: '3072', // 图片大小小于limit时转换成base64进行存储，减少http请求
              name: 'img/[name].[hash:6].[ext]' // 在输出文件夹中的路径（自动创建img文件夹存放所有图片）
            }
          }
        ]
      },
      {
        test: /\.(woff|woff2|eot|ttf|otf)$/,
        use: [
          {
            loader: 'file-loader',
            options: {
              name: '[name].[hash:6].[ext]',
              outputPath: 'img/' // 输出到dist目录下img文件夹中
            }
          }
        ]
      }
    ]
  }
};
```

图片大小大于 limit，url-loader 会调用 file-loader 进行处理，参数也会直接传给 file-loader

## **react 图片导入**

```ts
// 引入
const logo = require('./logo.svg');
// 使用
<img src={logo} alt="" />;
```

## **声明导出&导入**

`针对ts文件类型`

```ts
/**
 * 导出声明
 * 任何声明（比如变量、函数、类、类型别名或接口）都能通过添加`export`关键字来导出
 * 导入声明
 * 通过`import {} from '';`导入
 */

// m.ts
export function log(msg: string): void {
  console.log(msg);
}
export const title = '声明导出测试';

// App.jsx
// 导入-按需
import { log, title } from 'm.ts';
console.log(log); // f log
console.log(title); // 声明导出测试
// 导入-全部
import * as m from 'm.ts';
console.log(m); // { title: '声明导出测试', log: f }
// 导入-内容重命名
import { log as _log } from 'm.ts';
console.log(_log); // f log
```

```ts
// module.d.ts
/**
 * 帮助 TypeScript 判断传入的参数类型是否正确
 */
declare let log: (msg: string) => void;
```

## **使用第三方包**

`针对js文件`

```js
/**
 * foo.js
 * 声明导出
 */
function foo() {
  console.log('foo');
}
module.exports = foo;

/**
 * App.jsx
 * 声明导入
 */
import foo from 'foo.js';
```

说明一下`export default`跟`export`的用法区别

```js
// test.js

// 方式1： export default 默认输出
const obj = {};
export default obj;
// 引入
import test from 'test.js';
console.log(test); // {}

// 方式2： export 对象解构赋值输出
const obj = {};
const arr = [];
export { obj, arr };
// 引入
import { obj, arr } from 'test.js'; // 全引入
console.log(obj, arr); // {} []
import { obj } from 'test.js'; // 部分引入
console.log(obj); // {}
```

## **抽离第三方包**

webpack4.x 需使用 optimization.splitChunks

非按需加载（第三方包都抽离至同个文件中）

```js
const config = {
  optimization: {
    splitChunks: {
      // 注册需要单独抽离第三方包
      cacheGroups: {
        vendor: {
          chunks: 'all', // async(默认, 只会提取异步加载模块的公共代码), initial(提取初始入口模块的公共代码), all(同时提取前两者)
          test: /node_modules/,
          name: 'common',
          priority: -20, // 小于`-10`才能进行抽离出异步模块
          minSize: 0, // 大于0kb就被抽离到公共模块
          minChunks: 1, // 模块出现1次就被抽离到公共模块
          maxAsyncRequests: 5 // 异步模块, 一次最多只能加载5个
        }
      }
    }
  }
};
```

按需加载（第三方包单独抽离成独立文件）

通过用户交互触发加载与执行

```js
// App.jsx
import * as React from 'react';
class App extends React.Component {
  constructor() {
    super();
    this.handler = this.handler.bind(this);
  }
  handler() {
    require.ensure([], () => {
      // 引入
      const _ = require('lodash');
      // 使用
      console.log(_.defaults({ a: 1 }, { a: 3, b: 2 })); // {a: 1, b: 2}
    });
  }
  render() {
    return (
      <div>
        <button onClick={this.handler}>lodash</button>
      </div>
    );
  }
}
```

## **风格检查**

- `npm i -D tslint tslint-react`
- 创建`tslint.json`文件

```json
// tslint.json
{
  "rules": {
    "no-var-keyword": true, // 无var
    "triple-equals": [true, "allow-null-check"], // 使用全等或不全等
    "no-console": [false, "log", "error"] // 是否有console.log方法
  },
  "linterOptions": {
    "exclude": ["**/node_modules/**"]
  }
}
```

## **redux**

`npm i -D @types/redux`

```json
{
  "@types/redux": "^3.6.0"
}
```

目录结构

```
|- src
    |- actions
          |- testActionCreater.ts
    |- components
          |- App
              |- App.tsx
    |- reducers
          |- index.ts
          |- testReducer.ts
    |- store
          |- index.ts
```

```ts
// store/index.ts
import { createStore } from 'redux';
import reducers from '../reducers';
const store = createStore(reducers);
export default store;

// reducers/index.ts
import { combineReducers } from 'redux';
import testReducer from './testReducer';
const reducers = combineReducers({
  testReducer
});
export default reducers;

// reducers/testReducer.ts
export default (
  state: object = {},
  action: { type?: string; loginState?: boolean } = {}
) => {
  const { type, loginState }: { type?: string; loginState?: boolean } = action;
  switch (type) {
    case 'test':
      return {
        type,
        loginState
      };
    default:
      return state;
  }
};

// actions/testActionCreater.ts
export default (params: object = {}) => (
  dispatch: (arg: object) => void,
  getState: () => object
) => {
  dispatch(params);
};
```

使用

```ts
// components/App/App.tsx
import store from '../../store';
const { dispatch, getState, subscribe } = store;
import testActionCreater from '../../actions/testActionCreater';
class App extends React.Component {
  constructor(param: object) {
    super(param);
    // 将状态绑定给组件内部的state
    interface I_testReducer {
      type?: string;
      loginState?: boolean;
    }
    const { loginState }: I_testReducer = getState().testReducer;
    this.state = {
      loginState
    };
  }
  public render() {
    console.log('render..');
    interface I_state {
      loginState?: boolean;
    }
    const { loginState }: I_state = this.state;
    const _loginState =
      typeof loginState === 'boolean' ? loginState.toString() : 'false';
    return (
      <div className="App">
        <div>{_loginState}</div>
      </div>
    );
  }
}
```

## **react-keeper**

`npm i -D react-keeper`

```json
{
  "react-keeper": "^2.1.8"
}
```

**简单的例子-首页**

```tsx
// App.tsx
import * as React from 'react';
const { HashRouter, Route } = require('react-keeper');
import Home from '../Home/Home';
class App extends React.Component {
  public render() {
    return (
      <HashRouter>
        <div className="App">
          <Route component={Home} path="/>" index />
        </div>
      </HashRouter>
    );
  }
}
export default App;

// Home.tsx
import * as React from 'react';
class Home extends React.Component {
  public render() {
    return (
      <div className="home">
        <h2>Home</h2>
      </div>
    );
  }
}
export default Home;
```

`HashRouter` http://localhost:8080/#/

**路由路径&传参路由**

`path`精确匹配使用`>`路径结束符  
`path`传参`/:id`，通过`this.props`获取参数

**缓存组件**

作用：当路由发生跳转时，自动缓存当前页面的状态  
应用：列表页的缓存

缓存方式 1 - cache 属性  
cache / cache='root' `root`是默认值，表示根组件不解绑便会永久缓存  
cache='parent' `parent`表示为父组件缓存，父组件不解绑便会持续缓存

缓存方式 2 - CacheLink 组件  
情景：适用于`列表页>详情页`  
作用：在详情页回退列表页使用缓存，详情跳转其他路径清除列表页缓存  
`<CacheLink to={}>goto where</CacheLink>`

**加载动态组件**

`Route`组件的`loadComponent`属性  
作用：分割代码，实现按需加载，减少首屏时间

```js
// 待测试
const asyncGetComponent = callback => {
  import('../AsyncComponent/AsyncComponent.jsx').then(component => {
    callback(component.default);
  });
};
<Route loadComponent={asyncGetComponent} path="/asyncComponent" />;
```

**过滤器**

`Route`组件的`enterFilter`和`leaveFilter`属性  
作用：`enterFilter`进入组件之前执行过滤器；`leaveFilter`组件解绑之前执行过滤器

使用场景：登录检测、权限检测、表单关闭检测

```js
// 待测试
/**
 * `Route`组件的`enterFilter`属性
 * `enterFilter`接收一个函数，函数有两个参数
 * 参数1：callback 接收渲染组件的方法
 * 参数2：props    接收`Route`组件绑定的属性
 */

// 过滤器处理函数
handler(callback, props) {
  // 获取登录状态
  const {loginState} = store.getState().login;
  if (loginState) {
    // 已登录
    callback();
  } else {
    // 未登录
    // 跳转至登录页
    window.location.hash = '/login';
  }
};

// 组件路由配置
<Route component={userCenter} path='/userCenter' enterFilter={this.handler} />

// 前往该组件，改变URL的hash值
window.location.hash = '/userCenter';
```
