## webpack @4.2.0

目的：了解webpack4.x新特性及使用配置

PS:此文档仅适用于webpack4.x以上版本


**指令**
---
* `webpack` // 让 webpack 默认以根目录下的 webpack.config.js 文件来运行
* `webpack --config [config.js]` // --config 是让 webpack 使用某个配置文件来运行，忽略 webpack.config.js 文件的配置
* `webpack --mode [production|development]` // 4.0新特性 --mode 是让webpack知道当前处于生产环境还是开发环境，生产环境会自动压缩代码

**Loader**
---
作用：让 webpack 具有打包除 js 以外的其他文件的能力

本质：对使用 import 和 require 命令引入的资源进行对应的 loader 加工处理再输出

用法：先安装对应的 loader，之后在配置文件中注册使用规则，使用 import 命令引入

以 CSS 文件和图片为例

```js
// webpack.config.js
module.exports = {
  module: {
    // 注册使用规则
    rules: [
      {
        test: /\.css$/, // 匹配条件
        use: ['style-loader', 'css-loader'] // 使用的loader 使用顺序：从右往左
      },
      {
        test: /\.(png|svg|jpg|gif)$/,
        use: ['file-loader']
      }
    ]
  }
};
// module.js
import './xxx.css'; // 引入样式表
import img from './xxx.png'; // 引入图片
```

常用文件需要安装的 loader

* `.css` 安装 style-loader、css-loader

* `加载图片` 安装 file-loader、url-loader

* `.html` 安装 html-loader

常用插件

* `clean-webpack-plugin` 清理输出文件夹中不相关文件
* `html-webpack-plugin` 生成 html 文件

**使用模板生成HTML文件并自动载入CSS和JS**
---
注意：处理图片的路径需要 `html-loader`

```js
// 安装 html-loader html-webpack-plugin
const HtmlWebpackPlugin = require('html-webpack-plugin');
module.exports = {
  // 【入口】
  entry: {
    // 多入口文件采用对象写法
    index: './src/js/index.js'
  },
  // 【loader】
  module: {
    rules: [
      {
        test: /\.html$/,
        use: ['html-loader']
      }
    ]
  },
  // 【插件】
  plugins: [
    new HtmlWebpackPlugin({
      filename: 'index.html', // 输出文件名
      template: path.join(__dirname, 'src/index_model.html'), // 使用的模板html
      inject: 'body', // 插入body标签底部
      chunks: ['index'], // 插入scripts标签 引入打包后的js
      hash: true, // 文件名添加hash字符串
      // 压缩html
      minify: {
        removeAttributeQuotes: true, // 移除属性的引号
        removeComments: true, // 移除注释
        collapseWhitespace: true // 移除空格
      }
    })
  ],
  // 【输出】
  output: {
    filename: '[name].js', // 输出文件名
    path: path.resolve(__dirname, 'dist') // 输出文件夹路径
  }
};
```

**清理dist目录下的不相关文件**
---
```js
// webpack.config.js
const CleanWebpackPlugin = require('clean-webpack-plugin');
module.exports = {
  plugins: [
    new CleanWebpackPlugin(['dist']) // 指定dist目录
  ]
};
```

**使用第三方包**
---
所有的第三方包必须遵循ES6模块写法：  
需以`export default`或`export`进行输出  
通过`import`进行引入使用

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
export {obj, arr};
// 引入
import {obj, arr} from 'test.js'; // 全引入
console.log(obj, arr); // {} []
import {obj} from 'test.js'; // 部分引入
console.log(obj); // {}
```

**file-loader与url-loader**
---
file-loader 和 url-loader 可以接收并加载任何文件，例如图片、字体等；然后将其输出到构建目录

file-loader和url-loader的关系：url-loader中包含有file-loader，是对file-loader进行封装

**加载图片**
---
小图片转Base64减少HTTP请求
```js
module.exports = {
  module: {
    rules: [
      {
        test: /\.(png|svg|jpg|gif)$/,
        use: [{
          loader: 'url-loader',
          options: {
            limit: '3072', // 图片大小大于limit时不转换
            name: 'img/[name].[hash].[ext]' // 在输出文件夹中的路径（自动创建img文件夹存放所有图片）
          }
        }]
      }
    ]
  }
};
```
文件大小大于limit，url-loader会调用file-loader进行处理，参数也会直接传给file-loader

**加载字体**
---
```js
module.exports = {
  module: {
    rules: [
      {
        test: /\.(woff|woff2|eot|ttf|otf)$/,
        use: ['file-loader']
      }
    ]
  }
};
```

**devtool**
---
作用：在使用打包后文件的某一行代码时，可以追溯打包前所在源代码的位置

注意：只在开发环境下使用

常用选项：`devtool: 'source-map'`

**mode**
---
webpack4 的新特性

作用：指定当前环境，自动设置全局环境变量

```
// 方式1：加入命令
--mode [devlopment|production]

// 方式2：加入配置文件
module.exports = {
  mode: ['devlopment'|'production']
};
```

**观察模式**
---

作用：观察文件改动，自动执行编译

命令`webpack --watch`

缺点：浏览器需要手动重新加载

**webpack-dev-server**
---
作用：提供web服务，自动执行编译，并实现浏览器自动重新加载

使用：先安装`webpack-dev-server`，再修改配置文件

```js
// webpack.config.js
module.exports = {
  // web服务
  devServer: {
    host: '192.168.1.xxx', // 域名/IP，默认localhost
    port: '5000', // 端口，默认8080
    contentBase: './dist' // devServer访问该目录的文件
  }
};
```

**模块热替换**
---
HMR(Hot Module Replacement) 是webpack的内置插件

作用：当项目体积较大时，只更新变更内容，无需全部编译，以节省宝贵的开发时间

使用场景：只用于开发环境

可能存在的疑问：HTML上的修改不能自动触发浏览器的刷新

目前对HMR的理解：  
HMR仅限于入口文件import的CSS和JS文件进行热替换  
修改模板HTML文件的任何内容会触发重新编译，但浏览器不会热替换，也不自动刷新  
修改HTML文件无法实时看到修改后的效果，需要手动刷新浏览器  

那么问题来了，HMR不就变得鸡肋了吗！  
我认为不是的，当项目体积较大时，修改某个模块会导致全部模块进行重新编译，这是个耗时的操作  
但有了HMR，就能监听某个模块发生改动，只替换有改动的模块，而不会进行重新编译  
HMR解决了改动某个模块就要全部模块进行重新编译的耗时等待问题，节省出宝贵的开发时间

对于修改HTML文件不进行热替换的问题，...若有修改到就手动刷新浏览器即可，毕竟HMR解决了关键的耗时问题

开启HMR
```js
// webpack.config.js
const webpack = require('webpack');
module.exports = {
  devServer: {
    contentBase: './dist', // devServer访问该目录的文件
    hot: true // 【HMR】
  },
  plugins: [
    new webpack.NamedModulesPlugin(), // 容易查看要修补(patch)的依赖，辅助HMR
    new webpack.HotModuleReplacementPlugin() // 【HMR】
  ]
};

// main.js
import module1 from './module1.js';
module1(); // 执行模块1返回的函数
if (module.hot) {
  // 监听 module1.js 文件的改动 改动则执行回调函数
  module.hot.accept('./module1.js', function() {
    module1(); // 模块1返回的函数重新执行
  });
}

// module1.js
export default function module1() {
  const content = 'webpack';
  console.log(content);
}
```

**添加一个公共模块**
---
目的：方法共享，同时在使用该模块时，未引用的代码会被webpack标记

生产环境下 `--mode production` 或 `mode: 'production'`，会删除标记的未引用代码

格式如下：
```js
// math.js
export function first() {
  // todosomething...
}
export function second() {
  // todosomething...
}

// use
import {first} from './math.js'; // 引入first
```

**生产环境构建-配置**
---
开发环境和生产环境的构建目标差异很大，可以独立去写每个环境的webpack配置

但仍要遵循不重复原则，保留一个通用配置，然后将配置不同点与相同点进行合并

安装 `npm install --save-dev webpack-merge`

```js
// webpack.common.js
module.exports = {
  entry: {},
  module: {},
  plugins: [],
  output: {}
};

// webpack.[dev|prod].js
const merge = require('webpack-merge');
const common = require('./webpack.common.js');
// 将差异部分与公共部分进行合并
module.exports = merge(common, {
  module: {},
  plugins: []
})
```
通过执行命令 `--config webpack.[dev|prod].js`，指定使用配置文件

注意：webpack4 加入 `mode` 这一新特性，通过 `devlopment|production` 选项会执行最优的默认配置


**生产环境构建-指定环境**
---
可以通过参数指定 `--config [production|development]`

访问`process.env.NODE_ENV`变量可获取当前环境


**抽离CSS**
---

安装 `extract-text-webpack-plugin` 插件

使用
```js
const ExtractTextPlugin = require('extract-text-webpack-plugin');
// 单实例
module.exports = {
  module: {
    rules: [
      {
        test: /\.css$/,
        // use: ['style-loader', 'css-loader'] // 修改前
        use: ExtractTextPlugin.extract({
          fallback: 'style-loader',
          use: 'css-loader'
        })
      }
    ]
  },
  plugins: [
    new ExtractTextPlugin({
      filename: 'style.css' // 输出文件路径+文件名
    })
  ]
};

// 多实例
const extractCSS = new ExtractTextPlugin('stylesheets/[name]-one.css');
const extractSCSS = new ExtractTextPlugin('stylesheets/[name]-two.css');
module.exports = {
  module: {
    rules: [
      {
        test: /\.css$/,
        use: extractCSS.extract(['css-loader', 'postcss-loader']) // postcss-loader下面会介绍
      },
      {
        test: /\.scss$/,
        use: extractSCSS.extract(['css-loader', 'sass-loader'])
      }
    ]
  }
};
```

**postcss-loader**
---

作用：样式自动添加浏览器前缀

注意：postcss.config.js是必须的

使用
```js
// postcss.config.js
module.exports = {
  plugins: [
    require('autoprefixer') // 自动加前缀
  ]
}

// webpack.config.js
// 多实例编译情景
const ExtractTextPlugin = require('extract-text-webpack-plugin');
const extractCSS = new ExtractTextPlugin('[name]-one.css'); // css
const extractSASS = new ExtractTextPlugin('[name]-two.css'); // sass
module.exports = {
  module: {
    rules: [
      {
        test: /\.css$/,
        use: extractCSS.extract(['css-loader', 'postcss-loader'])
      },
      {
        test: /\.sass$/,
        use: extractSASS.extract(['css-loader', 'postcss-loader', 'sass-loader'])
      }
    ]
  },
  plugins: [
    extractCSS,
    extractSASS
  ]
}

```

**抽离第三方包**
---

注意：webpack4.0版本废除了CommonsChunkPlugin

新的抽离方法使用 `optimization.splitChunks.cacheGroups`

```js
// webpack.config.js
// example
module.exports = {
  entry: {
    index: './src/main.js',
    vendor: ['lodash'] // 第三方
  },
  optimization: {
    splitChunks: {
      cacheGroups: {
        // key需与entry入口的key保持一致
        index: {
          chunks: 'initial',
					minChunks: 2,
					maxInitialRequests: 5, // The default limit is too small to showcase the effect
					minSize: 0 // This is example is too small to create commons chunks
        },
        vendor: {
          test: /node_modules/,
					chunks: 'initial',
					name: 'vendor',
					priority: 10,
					enforce: true
        }
      }
    }
  }
};
```

**ES6转ES5**
---

问题：webpack无法对ES6的语法进行转换，ES6语法可能存在浏览器兼容问题

babel-loader的作用正是实现对使用了ES6语法的.js文件进行处理

安装 `npm i -D babel-loader babel-core babel-preset-es2015`

* babel-loader 专门处理js文件

* babel-core 提供一系列api，babel-loader实际上调用了babel-core的api

* babel-preset-es2015 告诉babel使用哪种转码规则进行文件处理

babel的转码规则有：`babel-preset-es2015、babel-preset-latest、babel-preset-env`  
官方推荐使用 `babel-preset-env`

```js
// webpack.config.js
module.exports = {
  module: {
    rules: [
      {
        test: /\.js$/,
        exclude: /node_modules/, // 不处理
        use: {
          loader: 'babel-loader',
          options: {
            presets: ['es2015'] // ES6 转 ES5
          }
        }
      }
    ]
  }
};
```


### 指南
---

目标：从零搭建webpack进行模块化开发

* 创建文件夹 `src` 输入目录、`dist` 输出目录
* 全局和本地安装`webpack`（唯一的全局安装）
* 开发环境工具包 `webpack-cli webpack-dev-server`
* 创建文件 `webpack.config.js`
* package.json 配置 scripts `"build": "webpack"`、`"start": "webpack-dev-server --open"`
* 安装常用loader `html-loader css-loader style-loader postcss-loader sass-loader node-sass url-loader file-loader`
* 安装babel相关loader `babel-loader babel-core babel-preset-es2015 babel-polyfill babel-plugin-transform-runtime`
* 安装plugins `clean-webpack-plugin html-webpack-plugin extract-text-webpack-plugin`

**基本目录**
---
```
|-dist(输出文件夹)
|-src(源代码)
|-webpack.config.js(webpack配置)
|-postcss.config.js(postcss配置)
|-package.json(添加快捷命令，查看已安装的包)
```

**单页配置**
---
```js
// webpack.config.js
const path = require('path');
const webpack = require('webpack');
const CleanWebpackPlugin = require('clean-webpack-plugin');
const HtmlWebpackPlugin = require('html-webpack-plugin');
const ExtractTextPlugin = require('extract-text-webpack-plugin');
const extractCSS = new ExtractTextPlugin('[name]-one.css');
const extractSASS = new ExtractTextPlugin('[name]-two.css');
module.exports = {
  mode: 'production', // development | production
  // devtool: 'source-map', // 用于查看代码在编译前的位置
  devServer: {
    host: '192.168.1.xxx',
    contentBase: './dist', // 监听该目录
    openPage: 'Home/Home.html',
    inline: true, // 用来支持dev-server自动刷新的配置
    historyApiFallback: true, // SPA任意跳转或404响应可以指向首页
    hot: true // 【HMR】
  },
  entry: {
    main: './src/index.js',
    vendor: ['lodash'] // 第三方
  },
  module: {
    rules: [
      {
        test: /\.html$/,
        use: ['html-loader']
      },
      {
        test: /\.css$/,
        // use: ['style-loader', 'css-loader']
        use: extractCSS.extract(['css-loader', 'postcss-loader'])
      },
      {
        test: /\.sass$/,
        use: extractSASS.extract([
          'css-loader',
          'postcss-loader',
          'sass-loader'
        ])
      },
      {
        test: /\.js$/,
        exclude: /node_modules/,
        use: {
          loader: 'babel-loader',
          options: {
            plugins: ['transform-runtime'], // 转ES6原生API 例如Promise、Object.assign
            presets: ['es2015'] // ES6转ES5
          }
        }
      },
      {
        test: /\.(png|svg|jpg|gif)$/,
        use: [{
          loader: 'url-loader',
          options: {
            limit: '3072',
            name: 'img/[name].[hash].[ext]'
          }
        }]
      }
    ]
  },
  // webpack4 新特性 (抽离后需要注入HTML才能生效) 【只在最终打包阶段使用，否则会使devServer异常】
  optimization: {
    splitChunks: {
      cacheGroups: {
        main: {
          chunks: 'initial',
					minChunks: 2,
					maxInitialRequests: 5, // The default limit is too small to showcase the effect
					minSize: 0 // This is example is too small to create commons chunks
        },
        vendor: {
          test: /node_modules/,
					chunks: 'initial',
					name: 'vendor',
					priority: 10,
					enforce: true
        }
      }
    }
  },
  plugins: [
    new CleanWebpackPlugin(['dist']),
    new HtmlWebpackPlugin({
      filename: 'index.html', // 输出文件名
      template: path.resolve(__dirname, 'src/index.html'), // 模板路径
      chunks: ['main', 'vendor'], // 指定所需的js
      inject: 'body', // 将js注入body标签
      // 压缩html
      minify: {
        removeAttributeQuotes: true, // 移除属性的引号
        removeComments: true, // 移除注释
        collapseWhitespace: true // 移除空格
      }
    }),
    extractCSS,
    extractSASS,
    // new webpack.NamedModulesPlugin(), // 【HMR】
    // new webpack.HotModuleReplacementPlugin() // 【HMR】
  ],
  output: {
    filename: '[name].bundle.js',
    path: path.resolve(__dirname, 'dist')
  }
};

// postcss.config.js
module.exports = {
  plugins: [
    require('autoprefixer')
  ]
};

// package.json
{
  "name": "webpack4",
  "version": "1.0.0",
  "description": "",
  "main": "index.js",
  "scripts": {
    "build": "webpack",
    "start": "webpack-dev-server --open"
  },
  "keywords": [],
  "author": "",
  "license": "ISC",
  "devDependencies": {
    "babel-core": "^6.26.0",
    "babel-loader": "^7.1.4",
    "babel-plugin-transform-runtime": "^6.23.0",
    "babel-polyfill": "^6.26.0",
    "babel-preset-es2015": "^6.24.1",
    "clean-webpack-plugin": "^0.1.19",
    "css-loader": "^0.28.11",
    "extract-text-webpack-plugin": "^4.0.0-beta.0",
    "file-loader": "^1.1.11",
    "html-loader": "^0.5.5",
    "html-webpack-plugin": "^3.2.0",
    "lodash": "^4.17.5",
    "node-sass": "^4.8.3",
    "postcss-loader": "^2.1.3",
    "sass-loader": "^6.0.7",
    "style-loader": "^0.20.3",
    "url-loader": "^1.0.1",
    "webpack": "^4.4.1",
    "webpack-cli": "^2.0.13",
    "webpack-dev-server": "^3.1.1"
  }
}
```

**多页配置**

src目录结构

```
|-src
   |-Home
       |-Home.html
       |-Home.js
       |-image
           |-test1.png
           |-test2.png
   |-News
      |-News.html
      |-News.js
```

```js
// webpack.config.js
const path = require('path');
const webpack = require('webpack');
const CleanWebpackPlugin = require('clean-webpack-plugin');
const HtmlWebpackPlugin = require('html-webpack-plugin');
const ExtractTextPlugin = require('extract-text-webpack-plugin');
const extractCSS = new ExtractTextPlugin('[name]-one.css');
const extractSASS = new ExtractTextPlugin('[name]-two.css');
const Home = new HtmlWebpackPlugin({
  filename: 'Home/Home.html', // 输出文件名
  template: path.resolve(__dirname, 'src/Home/Home.html'), // 模板路径
  chunks: ['Home', 'vendor'], // 指定所需的js
  inject: 'body', // 将js注入body标签
  // 压缩html
  minify: {
    removeAttributeQuotes: true, // 移除属性的引号
    removeComments: true, // 移除注释
    collapseWhitespace: true // 移除空格
  }
});
const News = new HtmlWebpackPlugin({
  filename: 'News/News.html', // 输出文件名
  template: path.resolve(__dirname, 'src/News/News.html'), // 模板路径
  chunks: ['News'], // 指定所需的js
  inject: 'body', // 将js注入body标签
  // 压缩html
  minify: {
    removeAttributeQuotes: true, // 移除属性的引号
    removeComments: true, // 移除注释
    collapseWhitespace: true // 移除空格
  }
});
module.exports = {
  mode: 'production', // development | production
  // devtool: 'source-map', // 用于查看代码在编译前的位置
  devServer: {
    host: '192.168.1.xxx',
    contentBase: './dist', // 监听该目录
    openPage: 'Home/Home.html',
    inline: true, // 用来支持dev-server自动刷新的配置
    historyApiFallback: true, // SPA任意跳转或404响应可以指向首页
    hot: true // 【HMR】
  },
  entry: {
    Home: './src/Home/Home.js',
    News: './src/News/News.js',
    vendor: ['lodash'] // 第三方
  },
  module: {
    rules: [
      {
        test: /\.html$/,
        use: ['html-loader']
      },
      {
        test: /\.css$/,
        // use: ['style-loader', 'css-loader']
        use: extractCSS.extract(['css-loader', 'postcss-loader'])
      },
      {
        test: /\.sass$/,
        use: extractSASS.extract([
          'css-loader',
          'postcss-loader',
          'sass-loader'
        ])
      },
      {
        test: /\.js$/,
        exclude: /node_modules/,
        use: {
          loader: 'babel-loader',
          options: {
            plugins: ['transform-runtime'], // 转ES6原生API 例如Promise、Object.assign
            presets: ['es2015'] // ES6转ES5
          }
        }
      },
      {
        test: /\.(png|svg|jpg|gif)$/,
        use: [{
          loader: 'url-loader',
          options: {
            limit: '3072',
            name: 'image/[name].[hash:8].[ext]'
          }
        }]
      }
    ]
  },
  // webpack4 新特性 (抽离后需要注入HTML才能生效) 【只在最终打包阶段使用，否则会使devServer异常】
  optimization: {
    splitChunks: {
      cacheGroups: {
        Home: {
          chunks: 'initial',
					minChunks: 2,
					maxInitialRequests: 5, // The default limit is too small to showcase the effect
					minSize: 0 // This is example is too small to create commons chunks
        },
        vendor: {
          test: /node_modules/,
					chunks: 'initial',
					name: 'vendor',
					priority: 10,
					enforce: true
        }
      }
    }
  },
  plugins: [
    new CleanWebpackPlugin(['dist']),
    Home,
    News,
    extractCSS,
    extractSASS,
    // new webpack.NamedModulesPlugin(), // 【HMR】
    // new webpack.HotModuleReplacementPlugin() // 【HMR】
  ],
  output: {
    filename: '[name]/[name].bundle.js', // 输出目录下的文件路径
    path: path.resolve(__dirname, 'dist'), // 输出目录
    publicPath: '../' // 静态资源路径 (build ../) (start /)
  }
};

```

**CSS Modules**
---
```
const ExtractTextPlugin = require('extract-text-webpack-plugin');
const extractCSS = new ExtractTextPlugin('[name]/[name].[hash:5].css'); // 分离
// loader配置
{
  test: /\.css$/,
  use: extractCSS.extract({
    use: [{
      loader: 'css-loader',
      options: {
        modules: true, // 样式名称hash处理
        localIdentName: '[name]__[local]___[hash:base64:5]' // hash名称格式化
      }
    }, 'postcss-loader'],
    fallback: 'style-loader'
  })
}
```
注意：只能用于Vue模板语法或JSX语法，普通html文件无法进行hash值替换


**html-loader通过标识符替换字符串模板**
---
```
{
  test: /\.html$/,
  use: [{
    loader: 'html-loader',
    options: {
      interpolate:['require'] // 通过require标识符，替换占位的模板字符串 "${require(`./xxx/xx.js`)}"
    }
  }]
}

// USE

// xxx.html
<div data-attr="${require(`../xxx.js`).test}"></div>
// xxx.js
module.exports = {
  test: 'xxx'
};

// 编译后 ↓↓
<div data-attr="xxx"></div>
```

### 多页应用测试版
---
不适用于多人协作开发

```js
const path = require('path');
const webpack = require('webpack');
const CleanWebpackPlugin = require('clean-webpack-plugin');
const HtmlWebpackPlugin = require('html-webpack-plugin');
const ExtractTextPlugin = require('extract-text-webpack-plugin');

const extractCSS = new ExtractTextPlugin({
  filename: '[name]/[name].[hash:5].css',
  allChunks: true
});
const extractSASS = new ExtractTextPlugin({
  filename: '[name]/[name].[hash:6].css',
  allChunks: true
});

module.exports = {
  mode: 'production', // development | production
  entry: {
    Home: './src/Home/js/Home.js',
    News: './src/News/js/News.js'
  },
  resolve: {
    alias: {
      echarts: path.resolve(__dirname, 'src/lib/echarts.min.js')
    }
  },
  module: {
    rules: [
      {
        test: /\.html$/,
        use: ['html-loader']
      },
      {
        test: /\.css$/,
        use: extractCSS.extract(['css-loader', 'postcss-loader'])
      },
      {
        test: /\.sass$/,
        use: extractSASS.extract(['css-loader', 'postcss-loader', 'sass-loader'])
      },
      {
        test: /\.js$/,
        exclude: /node_modules|lib/,
        use: {
          loader: 'babel-loader',
          options: {
            plugins: ['transform-runtime'], // 转ES6原生API 例如Promise、Object.assign
            presets: ['es2015'] // ES6转ES5
          }
        }
      },
      {
        test: /\.(png|svg|jpg|gif)$/,
        use: [
          {
            loader: 'url-loader',
            options: {
              limit: '3072',
              name: 'img/[name].[hash:8].[ext]'
            }
          }
        ]
      }
    ]
  },
  // webpack4 新特性 (抽离后需要注入HTML才能生效) 【只在最终打包阶段使用，否则会使devServer异常】
  optimization: {
    splitChunks: {
      cacheGroups: {
        Home: {
          chunks: 'initial',
          minChunks: 2,
          maxInitialRequests: 5, // The default limit is too small to showcase the effect
          minSize: 0 // This is example is too small to create commons chunks
        },
        News: {
          chunks: 'initial',
          minChunks: 2,
          maxInitialRequests: 5, // The default limit is too small to showcase the effect
          minSize: 0 // This is example is too small to create commons chunks
        },
        common: {
          test: /node_modules|lib/,
          chunks: 'initial',
          name: 'common',
          priority: 9,
          enforce: true
        },
        echarts: {
          test: new RegExp('echarts'),
          chunks: 'initial',
          name: 'echarts',
          priority: 10,
          enforce: true
        },
        lodash: {
          test: /lodash/,
          chunks: 'initial',
          name: 'lodash',
          priority: 10,
          enforce: true
        }
      }
    }
  },
  plugins: [
    new CleanWebpackPlugin(['dist']),
    extractCSS,
    extractSASS,
    new HtmlWebpackPlugin({
      filename: 'Home/Home.[hash:8].html', // 输出文件名
      template: path.resolve(__dirname, 'src/Home/Home.html'), // 模板路径
      chunks: ['Home', 'common', 'echarts', 'lodash'], // 指定所需的js
      inject: 'body' // 将js注入body标签
    }),
    new HtmlWebpackPlugin({
      filename: 'News/News.html', // 输出文件名
      template: path.resolve(__dirname, 'src/News/News.html'), // 模板路径
      chunks: ['News', 'common', 'echarts', 'lodash'], // 指定所需的js
      inject: 'body' // 将js注入body标签
    })
  ],
  output: {
    filename: '[name]/[name].bundle.js', // 输出目录下的文件路径
    path: path.resolve(__dirname, 'dist'), // 输出目录
    publicPath: '../' // 静态资源路径css和img会受影响 (build ../) (start /)
  }
};
```

### 多页应用生产版
---
|- webpack.moduleConfig.js // 模块注册
|- webpack.venderLocal.js // 非npm安装的第三方包路径
|- webpack.config.js // 根据moduleConfig.js和venderLocal.js进行动态配置

```js
// webpack.moduleConfig.js
/**
 * 模块配置文件
 * @param chunks 模块打包完毕后需要注入的js文件
 * 注意有些非npm安装的第三方包需要注册别名 resolve.alias
 */
const moduleConfig = {
  Home: {
    chunks: ['echarts', 'lodash']
  },
  News: {
    chunks: ['echarts', 'lodash']
  }
};
module.exports = moduleConfig;
```

```js
// webpack.venderLocal.js
/**
 * 本地第三方资源，非npm安装
 * path.resolve(__dirname, 'src/lib/xx.js')
 */
const venderLocal = {
  echarts: {
    path: 'src/lib/echarts.min.js'
  }
};

module.exports = venderLocal;
```

```js
// webpack.config.js 【用于生产阶段】
const path = require('path');
const webpack = require('webpack');
const CleanWebpackPlugin = require('clean-webpack-plugin');
const HtmlWebpackPlugin = require('html-webpack-plugin');
const ExtractTextPlugin = require('extract-text-webpack-plugin');

const extractCSS = new ExtractTextPlugin('[name]/[name].[hash:5].css'); // 分离css文件
const extractSASS = new ExtractTextPlugin('[name]/[name].[hash:6].css'); // 分离sass文件

const moduleConfig = require('./webpack.moduleConfig.js'); // 模块配置
const venderLocal = require('./webpack.venderLocal.js'); // 本地第三方包列表，非npm安装

/**
 * webpack空配置
 */
const webpackConfig = {
  mode: 'production', // development | production
  entry: {}, // 格式 Home:'./src/Home/js/Home.js'
  resolve: {
    alias: {} // 格式 echarts:path.resolve(__dirname, 'src/lib/echarts.min.js')
  },
  module: {
    rules: [
      {
        test: /\.html$/,
        use: ['html-loader']
      },
      {
        test: /\.css$/,
        exclude: /node_modules/,
        use: extractCSS.extract({
          use: ['css-loader', 'postcss-loader'],
          fallback: 'style-loader'
        })
      },
      {
        test: /\.sass$/,
        exclude: /node_modules/,
        use: extractSASS.extract({
          use: ['css-loader', 'postcss-loader', 'sass-loader'],
          fallback: 'style-loader'
        })
      },
      {
        test: /\.js$/,
        exclude: /node_modules|lib/,
        use: {
          loader: 'babel-loader',
          options: {
            plugins: ['transform-runtime'], // 转ES6原生API 例如Promise、Object.assign
            presets: ['es2015'] // ES6转ES5
          }
        }
      },
      {
        test: /\.(png|svg|jpg|gif)$/,
        use: [
          {
            loader: 'url-loader',
            options: {
              limit: '3072',
              name: 'img/[name].[hash:8].[ext]'
            }
          }
        ]
      }
    ]
  },
  plugins: [
    new CleanWebpackPlugin(['dist']),
    extractCSS,
    extractSASS
  ],
  // webpack4新特性 (第三方包抽离后需要注入HTML才能生效) 【只在最终打包阶段使用，否则会使devServer异常】
  optimization: {
    splitChunks: {
      cacheGroups: {
        // 使用该特性需要先注入该js文件，js才会生效
        common: {
          test: /node_modules|lib/,
          chunks: 'initial',
          name: 'common',
          priority: 9,
          enforce: true
        }
      }
    }
  },
  output: {
    filename: '[name]/[name].bundle.js', // 输出目录下的文件路径
    path: path.resolve(__dirname, 'dist'), // 输出目录
    publicPath: '../' // 静态资源路径css和img会受影响 (build ../) (start /)
  }
};

/**
 * 填充webpack配置
 */
const totalVenderList = []; // 全部的第三方包，目的是将第三方包各自打包输出
for (let item in moduleConfig) {
  // moduleConfig[item].chunks isArr
  const moduleChunks = moduleConfig[item].chunks;
  for (let i = 0, l = moduleChunks.length; i < l; i++) {
    if (totalVenderList.indexOf(moduleChunks[i]) === -1) {
      totalVenderList.push(moduleChunks[i]);
    }
  }
}

const cacheGroups = webpackConfig.optimization.splitChunks.cacheGroups; // isObj

for (let item in moduleConfig) {
  // 填充入口配置，设置入口文件路径
  webpackConfig.entry[item] = `./src/${item}/js/${item}.js`;
  
  // 填充抽离第三方包配置
  cacheGroups[item] = {
    chunks: 'initial',
    minChunks: 2,
    maxInitialRequests: 5,
    minSize: 0
  };
  // 填充Plugins
  webpackConfig.plugins.push(
    new HtmlWebpackPlugin({
      filename: `${item}/${item}.html`, // 输出文件名
      template: path.resolve(__dirname, `src/${item}/${item}.html`), // 模板路径
      chunks: function() {
        const configChunks = moduleConfig[item].chunks;
        const finalChunks = [item, 'common'];
        configChunks.map(function (v) {
          finalChunks.push(v);
        })
        return finalChunks;
      }(), // 指定所需的js
      // hash: true, // 为css和js文件末尾添加hash，解决缓存问题
      inject: 'body', // 将js注入body标签
      // minify: { // 压缩html
      //   removeAttributeQuotes: true, // 移除属性的引号
      //   removeComments: true, // 移除注释
      //   collapseWhitespace: true // 移除空格
      // }
    })
  );
}

for (let i = 0, l = totalVenderList.length; i < l; i++) {
  let vender = totalVenderList[i];
  cacheGroups[vender] = {
    test: new RegExp(vender),
    chunks: 'initial',
    name: vender,
    priority: 10,
    enforce: true
  };
}

for (let item in venderLocal) {
  // 填充相对路径第三方包别名，例如可直接require('echarts')
  webpackConfig.resolve.alias[item] = path.resolve(
    __dirname,
    venderLocal[item].path
  );
}

module.exports = webpackConfig;
```

### 多页应用终极版 180417
---
作用：通过配置运行环境变量，启用webpack构建功能

|- webpack.env.js // 获取当前运行环境
|- webpack.moduleConfig.js // 模块注册
|- webpack.venderLocal.js // 非npm安装的第三方包路径
|- webpack.config.js // 配置文件，根据moduleConfig.js和venderLocal.js进行动态配置

`webpack.env.js` ↓↓
```js
/**
 * 用于控制当前webpack使用的使用环境
 * @param development 开发环境 指令npm run start
 * @param production  生产环境 指令npm run build
 */
module.exports = 'development'; // development | production
```

`webpack.moduleConfig.js` ↓↓
```js
/**
 * 模块配置文件
 * @param chunks 注意有些非npm安装的第三方包需要注册别名
 */
const config = {
  Home: {
    chunks: ['echarts', 'lodash']
  },
  News: {
    chunks: ['echarts', 'lodash']
  }
};
module.exports = config;
```

`webpack.venderLocal.js` ↓↓
```js
/**
 * 本地第三方资源，非npm安装
 * path.resolve(__dirname, 'src/lib/xx.js')
 */
const venderLocal = {
  echarts: {
    path: 'src/lib/echarts.min.js'
  }
};
module.exports = venderLocal;
```

`webpack.config.js` ↓↓
```js
const path = require('path');
const webpack = require('webpack');
const CleanWebpackPlugin = require('clean-webpack-plugin');
const HtmlWebpackPlugin = require('html-webpack-plugin');
const ExtractTextPlugin = require('extract-text-webpack-plugin');

const extractCSS = new ExtractTextPlugin('[name]/[name].[hash:5].css'); // 分离css文件
const extractSASS = new ExtractTextPlugin('[name]/[name].[hash:6].css'); // 分离sass文件

const CURRENT_ENV = require('./webpack.env.js'); // 当前环境 development | production
const moduleConfig = require('./webpack.moduleConfig.js'); // 模块配置
const venderLocal = require('./webpack.venderLocal.js'); // 本地第三方包列表，非npm安装

/**
 * webpack空配置
 */
const webpackConfig = {
  mode: CURRENT_ENV, // development | production
  entry: {}, // 格式 Home:'./src/Home/js/Home.js'
  resolve: {
    alias: {} // 格式 echarts:path.resolve(__dirname, 'src/lib/echarts.min.js')
  },
  module: {
    rules: [
      {
        test: /\.html$/,
        use: ['html-loader']
      },
      {
        test: /\.css$/,
        exclude: /node_modules/,
        use: extractCSS.extract({
          use: ['css-loader', 'postcss-loader'],
          fallback: 'style-loader'
        })
      },
      {
        test: /\.sass$/,
        exclude: /node_modules/,
        use: extractSASS.extract({
          use: ['css-loader', 'postcss-loader', 'sass-loader'],
          fallback: 'style-loader'
        })
      },
      {
        test: /\.js$/,
        exclude: /node_modules|lib/,
        use: {
          loader: 'babel-loader',
          options: {
            plugins: ['transform-runtime'], // 转ES6原生API 例如Promise、Object.assign
            presets: ['es2015'] // ES6转ES5
          }
        }
      },
      {
        test: /\.(png|svg|jpg|gif)$/,
        use: [
          {
            loader: 'url-loader',
            options: {
              limit: '3072',
              name: 'img/[name].[hash:8].[ext]'
            }
          }
        ]
      }
    ]
  },
  plugins: [new CleanWebpackPlugin(['dist']), extractCSS, extractSASS],
  output: {
    filename: '[name]/[name].bundle.js', // 输出目录下的文件路径
    path: path.resolve(__dirname, 'dist'), // 输出目录
    publicPath: '' // 静态资源路径css和img会受影响 (build ../) (start /)
  }
};

/**
 * 根据当前环境开启所需功能+包含资源输出的publicPath
 */
if (CURRENT_ENV === 'development') {
  webpackConfig.devtool = 'source-map';
  webpackConfig.devServer = {
    // host: '192.168.1.xxx', // 域名/IP，默认localhost
    // port: '5000', // 端口，默认8080
    contentBase: './dist', // devServer访问该目录的文件
    openPage: 'Home/Home.html', // 默认打开页面
    inline: true // 用来支持dev-server自动刷新的配置
  };
  webpackConfig.output.publicPath = '/';
} else if (CURRENT_ENV === 'production') {
  // webpack4新特性 (第三方包抽离后需要注入HTML才能生效) 【只在最终打包阶段使用，否则会使devServer异常】
  webpackConfig.optimization = {
    splitChunks: {
      cacheGroups: {
        // 使用该特性需要先注入该js文件，js才会生效
        common: {
          test: /node_modules|lib/,
          chunks: 'initial',
          name: 'common',
          priority: 9,
          enforce: true
        }
      }
    }
  };
  webpackConfig.output.publicPath = '../';
}

/**
 * 填充webpack配置
 */
const totalVenderList = []; // 全部的第三方包，目的是将第三方包各自打包输出
for (let item in moduleConfig) {
  // moduleConfig[item].chunks isArr
  const moduleChunks = moduleConfig[item].chunks;
  for (let i = 0, l = moduleChunks.length; i < l; i++) {
    if (totalVenderList.indexOf(moduleChunks[i]) === -1) {
      totalVenderList.push(moduleChunks[i]);
    }
  }
}

let cacheGroups = null; // isObj
if (CURRENT_ENV === 'production') {
  cacheGroups = webpackConfig.optimization.splitChunks.cacheGroups; // isObj
}

for (let item in moduleConfig) {
  // 填充入口配置，设置入口文件路径
  webpackConfig.entry[item] = `./src/${item}/js/${item}.js`;

  // 【需要根据当前环境使用】
  if (CURRENT_ENV === 'production') {
    // 填充抽离第三方包配置
    cacheGroups[item] = {
      chunks: 'initial',
      minChunks: 2,
      maxInitialRequests: 5,
      minSize: 0
    };
  }

  // 填充Plugins
  webpackConfig.plugins.push(
    new HtmlWebpackPlugin({
      filename: `${item}/${item}.html`, // 输出文件名
      template: path.resolve(__dirname, `src/${item}/${item}.html`), // 模板路径
      chunks: (function() {
        const configChunks = moduleConfig[item].chunks;
        const finalChunks = [item, 'common'];
        configChunks.map(function(v) {
          finalChunks.push(v);
        });
        return finalChunks;
      })(), // 指定所需的js
      // hash: true, // 为css和js文件末尾添加hash，解决缓存问题
      inject: 'body' // 将js注入body标签
      // minify: { // 压缩html
      //   removeAttributeQuotes: true, // 移除属性的引号
      //   removeComments: true, // 移除注释
      //   collapseWhitespace: true // 移除空格
      // }
    })
  );
}

// 【需要根据当前环境使用】
if (CURRENT_ENV === 'production') {
  for (let i = 0, l = totalVenderList.length; i < l; i++) {
    let vender = totalVenderList[i];
    cacheGroups[vender] = {
      test: new RegExp(vender),
      chunks: 'initial',
      name: vender,
      priority: 10,
      enforce: true
    };
  }
}

for (let item in venderLocal) {
  // 填充相对路径第三方包别名，例如可直接require('echarts')
  webpackConfig.resolve.alias[item] = path.resolve(
    __dirname,
    venderLocal[item].path
  );
}

module.exports = webpackConfig;

```

**安装模块遇到的问题**
---
```
npm install 模块安装问题
Module build failed: Error: `sass-loader` requires `node-sass` >=4. Please install a compatible version.
查看 package.json 发现 "node-sass": "^4.8.3" 是能被兼容的版本
解决方案：
1.移除整个 node_modules 文件夹
2.运行命令清除缓存 npm cache clear --force
3.重新安装 npm install
```